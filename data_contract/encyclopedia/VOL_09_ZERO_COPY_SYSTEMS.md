# VOLUME 09 — ZERO-COPY & HIGH-PERFORMANCE BINARY LAYOUTS

## Universal Data Contract, Serialization, IDL & Interoperability Architecture Encyclopedia

---

## 09.0 — Why Zero-Copy Matters (Formal Analysis)

### The Problem: Copy Costs

| Operation | CPU Cost (approximate) | Effect |
|-----------|----------------------|--------|
| memcpy 64 B | ~10 ns | Negligible |
| memcpy 4 KB | ~500 ns | Measurable at scale |
| memcpy 64 KB | ~5 µs | Significant for latency-sensitive |
| memcpy 1 MB | ~100–400 µs | Blocks pipeline processing |
| memcpy 12 MB (4K frame) | ~1.5–5 ms | Catastrophic at 60fps |

**Secondary costs of copying:**
- **Cache pollution:** Copied data evicts hot working set from L1/L2/L3
- **Memory bandwidth saturation:** Modern DDR4 ~50 GB/s shared across all cores
- **TLB pressure:** Accessing multiple virtual address spaces
- **Allocation overhead:** Heap allocs for deserialized objects (GC pressure)

### Traditional Serialization Path (4+ copies)

```
Application Object
     │ serialize
     ▼
[heap buffer] ──── copy 1: build serialized representation
     │ send()
     ▼
[kernel buffer] ── copy 2: copy_from_user (user → kernel)
     │ transport
     ▼
[kernel buffer] ── copy 3: copy_to_user (kernel → user) (receiver side)
     │ deserialize
     ▼
[heap objects] ─── copy 4: parse + allocate + populate application objects
```

### Zero-Copy Path (0 copies in data path)

```
Process A                          Process B
     │ write directly to shared memory
     ▼
[shared memory page] ◄── mmap'd by both processes ──► [same physical page]
     ↑                                                       ↑
  writer builds in-place                              reader accesses in-place
  (no serialize step)                                 (no deserialize step)
```

### Where Zero-Copy is Essential

| Domain | Data Rate | Requirement |
|--------|-----------|-------------|
| High-frequency trading (HFT) | ~100K msgs/sec, <1 µs latency | SBE overlays |
| ADAS perception pipeline | 12 MB × 6 cameras × 30fps = 2.16 GB/s | Iceoryx zero-copy |
| Network packet processing | 100 Gbps line rate = 12.5 GB/s | DPDK mbufs |
| GPU compute sharing | Multi-GB tensors | CUDA unified memory, DMA-BUF |
| Inter-process video | 4K HEVC decode → display | Android ION/DMA-BUF, GBM |

---

## 09.1 — FlatBuffers

### Overview

| Property | Value |
|----------|-------|
| Origin | Google (2014) |
| Key insight | Serialized buffer IS the in-memory representation — no parsing needed |
| Schema | `.fbs` files (table, struct, enum, union, root_type) |
| Governance | Google / open-source — Tier 6 |
| Code gen | `flatc` compiler (C++, Java, Python, Rust, Go, TypeScript, Kotlin, Swift, C#) |

### Binary Layout

```
Offset 0:         [root_offset]  ← 4-byte offset to the root table
                       │
                       ▼
Root Table:       [vtable_offset (soffset32)]
                  [field data...]
                       │
        ┌──────────────┘ (negative relative offset)
        ▼
VTable:           [vtable_size (uint16)]       ← size of this vtable in bytes
                  [object_size (uint16)]       ← size of the object data
                  [field_offset_0 (uint16)]    ← offset to field 0 within object
                  [field_offset_1 (uint16)]    ← offset to field 1 (0 = not present)
                  [field_offset_2 (uint16)]    ← ...
                  ...
```

### Table vs Struct

| Feature | Table | Struct |
|---------|-------|--------|
| Evolution | Fields addable (vtable tracks presence) | Fixed — no evolution |
| Overhead | vtable + soffset per table | None (packed inline) |
| Access cost | vtable lookup + offset dereference | Direct offset |
| Use case | Evolvable messages | Performance-critical inner data (vectors of points) |

### How Zero-Copy Reading Works (C++)

```cpp
// 1. Receive/mmap a byte buffer (no parse/decode step)
const uint8_t* buf = receive_buffer();

// 2. Get typed root accessor (pointer arithmetic only)
auto vehicle = GetVehicle(buf);

// 3. Access fields (vtable lookup → offset → read)
float speed = vehicle->speed();           // reads float at computed offset
auto name = vehicle->name()->c_str();     // string: length-prefixed UTF-8
auto sensors = vehicle->sensors();        // vector: count + elements
float temp = sensors->Get(2)->temperature(); // nested table access
```

**No allocation. No parsing. Just pointer math.**

### Evolution Rules

| Change | Safe? | Mechanism |
|--------|-------|-----------|
| Add field to table | Yes | New vtable entry; old readers see 0 (default) |
| Remove field | Yes | vtable offset = 0; reader returns default |
| Change field type | No | Binary incompatible |
| Reorder fields | No (ID matters) | vtable entries correspond to field IDs |
| Add field to struct | No | Structs are fixed-size inline |

---

## 09.2 — Cap'n Proto

### Overview

| Property | Value |
|----------|-------|
| Origin | Kenton Varda (Protobuf v2 author) — 2013 |
| Philosophy | "Data is already in-memory format" — no encode/decode |
| Schema | `.capnp` files |
| Governance | Community (Sandstorm.io) — Tier 8 |

### Arena-Based Memory Model

```
Message = [Segment 0][Segment 1][Segment 2]...
           ↑
     contiguous memory; pointers are RELATIVE offsets

Segment 0:  ┌──────────────────────────────────────┐
            │ struct pointer │ list pointer │ data...│
            └──────────────────────────────────────┘
                    │              │
                    ▼              ▼
            (offset to struct) (offset to list elements)
```

### Pointer Types

| Pointer | Format (64 bits) | Purpose |
|---------|-----------------|---------|
| Struct pointer | A=0, offset(30b), data_size(16b), ptr_count(16b) | Points to struct within segment |
| List pointer | A=1, offset(30b), element_size(3b), count(29b) | Points to list of values |
| Far pointer | A=2, offset(29b), segment_id(32b) | Cross-segment reference |
| Capability pointer | A=3, capability_index(32b) | RPC capability reference |

**All pointers are relative** → entire message is relocatable without fixup.

### True Zero-Copy (Read + Write)

```cpp
// Writing: builder allocates in contiguous arena
MallocMessageBuilder message;
auto vehicle = message.initRoot<Vehicle>();
vehicle.setSpeed(85.5f);
vehicle.setVin("1HGBH41JXMN109186");

// Wire bytes = the arena memory itself (no serialization step)
auto segments = message.getSegmentsForOutput();  // just returns segment pointers

// Reading: wrap existing bytes, no copy/parse
FlatArrayMessageReader reader(received_segments);
auto v = reader.getRoot<Vehicle>();
float speed = v.getSpeed();  // direct memory read
```

### Promise Pipelining (RPC)

```
Traditional RPC (3 round trips):
  Server server = await getServer();
  Admin admin = await server.getAdmin();
  Vehicle vehicle = await admin.getVehicle("abc");

Cap'n Proto (1 round trip):
  auto server = getServer();                    // returns promise
  auto admin = server.getAdminRequest().send(); // pipelined (no wait)
  auto vehicle = admin.getVehicleRequest().send(); // pipelined
  auto result = vehicle.wait();                 // ONE round trip total
```

The network batches all pipelined calls and resolves them server-side without client waiting.

### Capability-Based Security

Interfaces are first-class values (capabilities):
- Holding a reference to an interface grants permission to call it
- No ambient authority — must be explicitly granted
- Revocation: drop the capability → access revoked

---

## 09.3 — Apache Arrow

### Overview

| Property | Value |
|----------|-------|
| Origin | Apache Software Foundation (2016) |
| Purpose | Columnar in-memory format for analytics + zero-copy IPC |
| Governance | Apache Software Foundation — Tier 5 |
| Languages | C++, Python (PyArrow), Java, Rust, R, Julia, Go |

### Columnar vs Row Layout

```
Row-oriented (traditional):
  Record 1: [name="Alice", age=30, score=95.5]
  Record 2: [name="Bob",   age=25, score=88.0]
  Record 3: [name="Carol", age=28, score=91.2]

Columnar (Arrow):
  name_column:  ["Alice", "Bob", "Carol"]    → contiguous string buffer
  age_column:   [30, 25, 28]                 → contiguous int32 buffer
  score_column: [95.5, 88.0, 91.2]           → contiguous float64 buffer
```

**Why columnar is faster for analytics:**
- `SUM(score)`: reads only the score buffer (no skipping over irrelevant fields)
- SIMD: vectorized operations on contiguous int32/float64 arrays
- Cache efficiency: sequential access pattern

### Memory Layout per Column Type

| Column Type | Buffers | Layout |
|-------------|---------|--------|
| Int32Array | validity + values | `[null bitmap][int32 values (4B each)]` |
| Float64Array | validity + values | `[null bitmap][float64 values (8B each)]` |
| StringArray | validity + offsets + data | `[null bitmap][int32 offsets][UTF-8 bytes]` |
| BooleanArray | validity + values | `[null bitmap][packed bits (1 bit/value)]` |
| ListArray | validity + offsets + child | `[null bitmap][int32 offsets] → child array` |
| StructArray | validity + children | `[null bitmap] → [child arrays...]` |

All buffers are 8-byte aligned. Null bitmap: 1 bit per value (0 = null, 1 = valid).

### Arrow IPC Format

```
┌─────────────────────┐
│ Schema Message       │  ← describes column types
├─────────────────────┤
│ RecordBatch Message  │  ← metadata + buffer offsets
│ [buffer 0 (aligned)]│
│ [buffer 1 (aligned)]│
│ ...                  │
├─────────────────────┤
│ RecordBatch Message  │  ← next batch
│ [buffers...]         │
└─────────────────────┘
```

Zero-copy IPC: pass the file descriptor of a shared memory region containing Arrow IPC format → receiver maps it and reads directly.

### Arrow Flight (gRPC-based)

| Operation | Purpose |
|-----------|---------|
| `GetFlightInfo` | Discover available datasets |
| `DoGet` | Stream RecordBatches from server to client |
| `DoPut` | Stream RecordBatches from client to server |
| `DoExchange` | Bidirectional RecordBatch streaming |
| **Arrow Flight SQL** | SQL queries over Flight protocol |

### Key Integrations

| System | Integration |
|--------|-------------|
| Pandas | PyArrow as backend (pandas 2.0+) |
| Polars | Arrow-native DataFrame library |
| Spark | Arrow for pandas UDF optimization |
| DuckDB | Arrow-native analytical database |
| Velox (Meta) | Arrow-based query engine |

---

## 09.4 — SBE (Simple Binary Encoding)

### Overview

| Property | Value |
|----------|-------|
| Origin | FIX Trading Community |
| Governance | FIX Protocol Ltd — Tier 4 |
| Purpose | Lowest-latency encoding for financial market data |
| Latency | ~50–100 nanoseconds encode/decode |
| Users | London Stock Exchange, CME, Nasdaq, CBOE |

### Design Principles

1. Fixed-length fields wherever possible (no variable-length parsing in hot path)
2. Direct memory overlay — cast pointer to struct type
3. No dynamic memory allocation in encoder/decoder
4. Template-driven codecs from XML schema

### Binary Layout

```
┌──────────────────────────────────────────────────────────┐
│ Message Header (8 bytes)                                  │
│  blockLength (uint16) │ templateId (uint16) │            │
│  schemaId (uint16)    │ version (uint16)                 │
├──────────────────────────────────────────────────────────┤
│ Fixed-Size Fields (blockLength bytes)                     │
│  [field1][field2][field3]...                              │
├──────────────────────────────────────────────────────────┤
│ Repeating Groups (optional)                               │
│  [numInGroup (uint16)][blockLength (uint16)]             │
│  [group_entry_1][group_entry_2]...                       │
├──────────────────────────────────────────────────────────┤
│ Variable-Length Data (optional)                            │
│  [length (uint32)][bytes...]                             │
│  [length (uint32)][bytes...]                             │
└──────────────────────────────────────────────────────────┘
```

### Flyweight Pattern (Generated Code)

```cpp
// Generated "flyweight" — no allocation, pure pointer arithmetic
class ExecutionReport {
    char* buffer_;
    int offset_;
public:
    uint64_t orderId() const {
        return *reinterpret_cast<uint64_t*>(buffer_ + offset_ + 0);
    }
    int64_t price() const {  // Decimal as mantissa
        return *reinterpret_cast<int64_t*>(buffer_ + offset_ + 8);
    }
    uint32_t quantity() const {
        return *reinterpret_cast<uint32_t*>(buffer_ + offset_ + 16);
    }
    // ... direct reads at fixed offsets
};

// Usage: wrap received buffer
ExecutionReport report;
report.wrapForDecode(buffer, offset, blockLength, version, bufferLength);
uint64_t id = report.orderId();  // ~1 instruction (load from offset)
```

### SBE vs FIX Tag-Value

| Dimension | FIX Tag-Value | SBE |
|-----------|--------------|-----|
| Format | Text (`35=8\x0137=12345\x01`) | Binary (fixed offsets) |
| Parse time | ~1–10 µs | ~50–100 ns |
| Human readable | Yes | No |
| Wire size | Larger (ASCII + delimiters) | Compact (no tags, fixed layout) |
| Schema required | No (self-describing) | Yes (XML template) |

---

## 09.5 — Iceoryx (Shared Memory Middleware)

### Summary (Full coverage in Volume 07)

| Property | Value |
|----------|-------|
| Copy count | **0** (true zero-copy in data path) |
| Model | Publisher writes into shared memory pool; subscriber reads same pages |
| Daemon | RouDi manages pool allocation, routing, discovery |
| API | `Publisher<T>::loan()` → write → `publish()` / `Subscriber<T>::take()` → read → release |
| Governance | Eclipse Foundation — Tier 4 |

### Zero-Copy Flow

```
1. Publisher::loan()    → RouDi allocates chunk from shared memory pool
2. Publisher writes     → directly into the chunk (no buffer, no serialize)
3. Publisher::publish() → RouDi updates routing table (pointer transfer, not data copy)
4. Subscriber::take()  → receives pointer to the SAME chunk
5. Subscriber reads    → directly from the chunk (no deserialize)
6. Subscriber release  → chunk returned to pool
```

**Total data copies: 0.** Only pointers/metadata are exchanged through RouDi.

---

## 09.6 — DPDK Ring Buffers

### Overview

| Property | Value |
|----------|-------|
| Origin | Intel (2010) → Linux Foundation |
| Governance | Linux Foundation — Tier 4/5 |
| Purpose | User-space packet processing at line rate (100 Gbps+) |

### rte_ring

| Feature | Description |
|---------|------------|
| Type | Lock-free circular buffer |
| Variants | SPSC, SPMC, MPSC, MPMC |
| Contents | `void*` pointers (not data copies) |
| Zero-copy | Pass mbuf pointer across ring — data stays in DMA-mapped memory |

### rte_mempool

| Feature | Description |
|---------|------------|
| Type | Pre-allocated object pool |
| Alignment | Cache-line aligned, NUMA-local |
| Objects | Packet buffers (mbufs) with headroom for protocol headers |
| Allocation | O(1) from per-core cache; no malloc/free in fast path |

### Data Path (Zero-Copy)

```
NIC DMA → [mbuf in mempool] → rte_ring (pointer) → worker → rte_ring (pointer) → TX
              ↑                                                        ↑
        no memcpy                                               no memcpy
        (DMA-mapped)                                           (DMA-mapped)
```

---

## 09.7 — Typed Struct Overlays (Pattern)

### The Pattern

```c
// Define a fixed-layout struct (ABI contract)
struct __attribute__((packed)) VehicleState {
    uint64_t timestamp_us;    // offset 0
    float    speed_kmh;       // offset 8
    float    heading_deg;     // offset 12
    uint8_t  gear;            // offset 16
    uint8_t  _reserved[3];   // explicit padding for alignment
    uint32_t sequence;        // offset 20
};  // Total: 24 bytes

// Writer process
VehicleState* state = (VehicleState*)mmap_shared_memory();
state->timestamp_us = get_monotonic_us();
state->speed_kmh = 85.5f;
atomic_thread_fence(memory_order_release);  // ensure visibility

// Reader process (different address space, same physical pages)
const VehicleState* state = (const VehicleState*)mmap_shared_memory();
atomic_thread_fence(memory_order_acquire);
float speed = state->speed_kmh;  // direct read, no deserialize
```

### Correctness Requirements

| Requirement | Enforcement |
|-------------|-------------|
| ABI stability | No reordering, no size changes across versions |
| Endianness agreement | Same architecture (intra-host guaranteed) |
| Alignment | Explicit padding, `__attribute__((packed))` or `alignas()` |
| Synchronization | Atomics, futex, or POSIX mutex with `PTHREAD_PROCESS_SHARED` |
| Padding | Explicit `_reserved` bytes to prevent compiler-added gaps |

### ABI Stability Tools

| Tool | Purpose |
|------|---------|
| libabigail (`abidiff`) | Detects ABI changes in C/C++ shared libraries/structs |
| abi-compliance-checker | Header-level ABI compatibility checking |
| pahole | Shows struct layout, holes, and padding |
| `-Wpadded` (GCC/Clang) | Warns about compiler-inserted padding |

---

## 09.8 — Zero-Copy Comparison Matrix

| System | True Zero-Copy | Random Access | Schema Required | Code Gen | Mutable In-Place | SIMD-Friendly | Primary Use Case | Governance Tier |
|--------|---------------|--------------|----------------|---------|------------------|--------------|-----------------|-----------------|
| **FlatBuffers** | Yes (read) | Yes (vtable) | Yes (.fbs) | Yes (flatc) | Limited | Partial (structs) | Games, Android, embedded | 6 |
| **Cap'n Proto** | Yes (read+write) | Yes (pointers) | Yes (.capnp) | Yes (capnpc) | Yes (builder) | Partial | RPC, services | 8 |
| **Apache Arrow** | Yes (in-process) | Columnar | Yes (schema msg) | Yes | Partial | **Yes** (vectorized) | Analytics, ML, OLAP | 5 |
| **SBE** | Yes (overlay) | Fixed-offset | Yes (XML) | Yes (sbe-tool) | Yes | **Yes** (fixed-width) | HFT, market data | 4 |
| **Iceoryx** | Yes (shared mem) | App-defined | C++ template | Partial | Yes | N/A | ADAS, automotive | 4 |
| **DPDK ring** | Yes (pointer pass) | Via mbuf | No (void*) | No | Yes | Yes (SIMD NIC) | Packet processing | 5 |
| **Struct overlay** | Yes | Fixed-offset | C/C++ struct | No | Yes | Yes (if aligned) | OS IPC, embedded | N/A |
| **Protobuf** | **No** | No | Yes (.proto) | Yes | No | No | gRPC, cloud | 5/6 |
| **Avro** | **No** | No | Yes (JSON) | Yes | No | No | Kafka, Hadoop | 5 |
| **JSON** | **No** | No | Optional | No | No | No | Web APIs | N/A |

---

## 09.9 — Alignment, Padding, and ABI Stability

### Natural Alignment Rule

A field of size N bytes must be placed at an address divisible by N:

| Type | Size | Required Alignment |
|------|------|-------------------|
| `char` / `uint8_t` | 1 | 1 |
| `int16_t` | 2 | 2 |
| `int32_t` / `float` | 4 | 4 |
| `int64_t` / `double` | 8 | 8 |
| Pointer (64-bit) | 8 | 8 |
| `__m128` (SSE) | 16 | 16 |
| `__m256` (AVX) | 32 | 32 |

### Padding Example

```c
// BAD: compiler adds hidden padding
struct Inefficient {
    char   a;       // offset 0, size 1
    // 3 bytes padding inserted by compiler
    int    b;       // offset 4, size 4
    char   c;       // offset 8, size 1
    // 7 bytes padding to align struct to 8
    double d;       // offset 16, size 8
};  // Total: 24 bytes (only 14 bytes of actual data)

// GOOD: reorder by size (largest first)
struct Efficient {
    double d;       // offset 0, size 8
    int    b;       // offset 8, size 4
    char   a;       // offset 12, size 1
    char   c;       // offset 13, size 1
    // 2 bytes padding
};  // Total: 16 bytes (same data, 33% smaller)
```

### Cache Line Considerations

- Cache line = 64 bytes on x86-64 and ARM64
- **False sharing:** Two cores writing to different fields in the same cache line → performance collapse
- Solution: `alignas(64)` or padding between hot fields accessed by different threads

### ABI-Breaking Changes for Zero-Copy Structs

| Change | Breaking? | Reason |
|--------|-----------|--------|
| Add field in middle | **Yes** | All subsequent offsets shift |
| Change field type/size | **Yes** | Binary layout changes |
| Reorder fields | **Yes** | Offsets change |
| Rename field | No | Only source-level; binary unchanged |
| Append field at end | Conditional | Safe if consumers check size/version |
| Remove field | **Yes** | Offsets of subsequent fields shift |

### Versioning Zero-Copy Structures

```c
struct VehicleState_v2 {
    uint32_t version;          // Always first field
    uint32_t struct_size;      // Enables forward compatibility
    // === v1 fields (never reorder or resize) ===
    uint64_t timestamp_us;
    float    speed_kmh;
    float    heading_deg;
    // === v2 additions (appended) ===
    float    acceleration_ms2;
    uint16_t steering_angle_deg;
    uint8_t  _pad[2];
};
```

---

## 09.10 — When NOT to Use Zero-Copy

| Scenario | Why Zero-Copy is Wrong | Better Choice |
|----------|----------------------|---------------|
| Cross-machine communication | Can't share physical memory | Protobuf, Avro (compact wire) |
| Schema evolution is frequent | Fixed layouts break on change | Protobuf, FlatBuffers tables |
| Small messages (<100 bytes) | Copy cost negligible; complexity not justified | Any format |
| Untrusted data | Zero-copy = trusting buffer structure | Parse + validate (Protobuf) |
| Different endianness | Struct overlay assumes same endian | XDR, Protobuf (portable) |
| Language diversity | Struct overlay = C/C++ only | Protobuf, FlatBuffers (multi-lang) |
| Long-term storage | Binary layout tied to code version | Avro, Parquet (self-describing) |

---

## 09.11 — Cross-References

| Topic | Volume |
|-------|--------|
| IPC mechanisms (Binder, shared memory, Iceoryx detail) | Volume 07 — IPC Mechanisms |
| Serialization formats (Protobuf, Avro, Thrift — non-zero-copy) | Volume 04 — Serialization |
| FlatBuffers and Cap'n Proto IDL syntax | Volume 02 — IDLs |
| Transport bindings for Arrow Flight | Volume 14 — Transport & Runtime |
| Schema evolution strategies | Volume 10 — Schema Evolution |

---

*End of Volume 09 — Zero-Copy & High-Performance Binary Layouts*
