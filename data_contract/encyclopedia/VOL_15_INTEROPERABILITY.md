# VOLUME 15 — CROSS-LANGUAGE / CROSS-PLATFORM INTEROPERABILITY

## Universal Data Contract, Serialization, IDL & Interoperability Architecture Encyclopedia

---

## 15.0 — What Cross-Language/Cross-Platform Interoperability Means and Why It Is Hard

### Definition

Cross-language/cross-platform interoperability is the ability of systems written in different programming languages, running on different operating systems or hardware architectures, to exchange data and invoke each other's functionality correctly, efficiently, and safely.

### Why Interoperability Is Hard

| Challenge | Root Cause |
|-----------|-----------|
| Type system mismatch | Go has no inheritance; C++ has multiple inheritance; Python is dynamically typed |
| Memory model differences | GC languages (Java, Go) vs manual (C/C++) vs ownership (Rust) |
| Numeric representation | JavaScript has only `Number` (float64); C has separate int/float/double |
| String encoding | UTF-8 (Go, Rust), UTF-16 (Java, JavaScript, Windows), platform-native (C) |
| Endianness | ARM/x86 (LE) vs network protocols (BE) vs negotiated (DDS CDR) |
| ABI instability | C++ name mangling, struct padding, vtable layout differ across compilers |
| Platform APIs | Android Binder vs macOS Mach vs Fuchsia Zircon — no common abstraction |
| Evolution velocity | Server upgrades independently from mobile clients (years apart) |

### The Interop Stack

```
┌────────────────────────────────┐
│ Application types (native)     │  Language-specific (MyStruct, data class, etc.)
├────────────────────────────────┤
│ Generated bindings             │  protoc-gen-X, flatc, openapi-generator
├────────────────────────────────┤
│ Wire format (bytes)            │  Protobuf binary, JSON, Avro, FlatBuffers
├────────────────────────────────┤
│ Transport                      │  HTTP/2, TCP, Binder, shared memory
├────────────────────────────────┤
│ OS / Hardware                  │  Linux ARM64, Windows x86-64, Android, Fuchsia
└────────────────────────────────┘
```

---

## 15.1 — Language Binding Coverage Matrix

### Binding Quality Levels

| Level | Definition |
|-------|-----------|
| L1 | Official reference implementation (by standard/tool author) |
| L2 | Official well-maintained binding (by framework project) |
| L3 | Community-maintained binding (quality varies) |
| L4 | Partial or experimental binding |

### Coverage Matrix

| Technology | C | C++ | Java | Kotlin | Python | Go | Rust | TS/JS | Swift | C# | PHP | Ruby | Dart |
|-----------|---|-----|------|--------|--------|----|----|-------|-------|----|----|------|------|
| **Protobuf** | L2 | L1 | L1 | L2 | L1 | L1 | L2 | L2 | L2 | L1 | L2 | L2 | L2 |
| **gRPC** | L1 | L1 | L1 | L2 | L1 | L1 | L2 | L1 | L2 | L1 | L2 | L2 | L2 |
| **Thrift** | L1 | L1 | L1 | L3 | L1 | L1 | L3 | L1 | L3 | L1 | L1 | L1 | L3 |
| **Avro** | L1 | L3 | L1 | L2 | L1 | L2 | L2 | L2 | L3 | L2 | L3 | L3 | L3 |
| **FlatBuffers** | L1 | L1 | L1 | L1 | L1 | L1 | L1 | L1 | L1 | L2 | L3 | L3 | L3 |
| **Cap'n Proto** | L3 | L1 | L3 | L3 | L2 | L3 | L2 | L2 | L3 | L3 | L3 | L3 | L3 |
| **Arrow** | L1 | L1 | L1 | L2 | L1 | L1 | L1 | L2 | L3 | L2 | L3 | L3 | L3 |
| **CBOR** | L1 | L2 | L2 | L2 | L1 | L2 | L2 | L1 | L2 | L2 | L2 | L2 | L2 |
| **JSON Schema** | L3 | L3 | L1 | L2 | L1 | L1 | L2 | L1 | L3 | L2 | L2 | L2 | L3 |
| **OpenAPI codegen** | — | L2 | L1 | L1 | L1 | L1 | L2 | L1 | L2 | L1 | L2 | L2 | L2 |
| **AIDL** | — | L1 | L1 | L1 | — | — | L2 | — | — | — | — | — | — |
| **FIDL** | — | L1 | L3 | — | — | L1 | L1 | — | — | — | — | — | L1 |
| **ASN.1** | L1 | L2 | L1 | L2 | L2 | L2 | L3 | L3 | L3 | L1 | L3 | L3 | L3 |
| **DDS** | L1 | L1 | L1 | L2 | L1 | L3 | L3 | L3 | L3 | L1 | — | — | — |
| **MessagePack** | L1 | L1 | L1 | L2 | L1 | L1 | L1 | L1 | L2 | L1 | L1 | L1 | L2 |
| **SOME/IP** | — | L1 | L3 | — | L3 | — | L3 | — | — | — | — | — | — |

### Key Observations

- **Protobuf/gRPC**: Best overall cross-language coverage (L1/L2 for 10+ languages)
- **FlatBuffers**: Surprisingly broad L1 coverage (Google maintains all generators)
- **Cap'n Proto**: C++ is excellent; other languages are community-maintained (L3)
- **AIDL/FIDL**: Platform-locked (Android-only, Fuchsia-only respectively)
- **DDS**: Strong for C/C++/Java/Python; weak for modern languages (Rust, Go, TS)

---

## 15.2 — ABI Stability Analysis

### What Is ABI

ABI (Application Binary Interface) = the binary-level contract between compiled code units:

| Component | Description |
|-----------|-------------|
| Calling convention | Register usage, stack layout, argument passing |
| Data layout | Field offsets, padding, alignment in structs |
| Name mangling | C++ symbol name encoding (Itanium vs MSVC) |
| Object format | ELF (Linux), Mach-O (macOS), PE/COFF (Windows) |
| Exception model | DWARF (GCC/Clang), SEH (MSVC), SjLj (legacy) |

### ABI Relevance to Data Contracts

| Format Type | ABI Dependent? | Explanation |
|------------|---------------|-------------|
| Tag-based (Protobuf, Thrift, Avro) | No | Wire format defined by tags/schema, not memory layout |
| Zero-copy overlays (FlatBuffers structs, SBE) | **Yes** | Binary layout must match reader's struct expectations |
| C struct serialization | **Yes** | Compiler padding varies across compilers/flags |
| Relative-offset based (Cap'n Proto) | Partially | Offsets are format-defined but alignment matters |

### C++ ABI Incompatibility Sources

| Source | Impact |
|--------|--------|
| Name mangling (GCC vs MSVC vs Clang) | Linking failure across compilers |
| `std::string` layout (libstdc++ vs libc++) | Memory corruption if mixed |
| vtable layout | Virtual call dispatch failure |
| Exception model (DWARF vs SEH) | Crash on throw across boundary |
| Template instantiation | Different compilers may produce different layouts |

### C ABI: The Universal Stable Interface

| Platform | ABI Document |
|----------|-------------|
| Linux x86-64 | System V AMD64 ABI |
| Linux ARM64 | AAPCS64 (Arm Procedure Call Standard) |
| Windows x86-64 | Microsoft x64 calling convention |
| macOS ARM64 | Apple ARM64 (based on AAPCS64 with modifications) |

C struct layout is universally stable across compilers on the same platform/architecture. This makes C the FFI lingua franca.

### ABI Checking Tools

| Tool | Purpose |
|------|---------|
| `libabigail` + `abidiff` | Detect ABI changes in shared libraries |
| `abi-compliance-checker` | Header-level ABI comparison |
| Android libclang ABI checker | AOSP vendor interface verification |
| `repr(C)` in Rust | Guarantee C-compatible layout |

---

## 15.3 — Endianness and Pointer Size

### Endianness

| Order | Description | Platforms |
|-------|-------------|-----------|
| Little-endian (LE) | LSB at lowest address | x86, x86-64, ARM (default), RISC-V |
| Big-endian (BE) | MSB at lowest address | POWER (configurable), MIPS BE, SPARC, Motorola 68k |
| Network byte order | Big-endian by convention | IP, TCP, UDP headers |

### Protocol Byte Order Choices

| Technology | Byte Order | Rationale |
|-----------|-----------|-----------|
| Protobuf (varint) | Little-endian (fixed types) | Performance on dominant LE platforms |
| FlatBuffers | Little-endian | Zero-copy on LE without byte-swap |
| SBE | Little-endian (default) | HFT targets x86 |
| Cap'n Proto | Little-endian | Zero-copy on LE |
| CBOR | Big-endian | IETF tradition (network byte order) |
| ASN.1 BER/DER | Big-endian | ITU-T tradition |
| DDS CDR | Negotiated (flag in header) | Peer-to-peer flexibility |
| SOME/IP | Big-endian (header) | Automotive convention |
| Apache Arrow | Native (platform) | Zero-copy on same architecture |

### Pointer Size

| Model | `sizeof(int)` | `sizeof(long)` | `sizeof(void*)` | Platforms |
|-------|--------------|----------------|-----------------|-----------|
| ILP32 | 4 | 4 | 4 | 32-bit Linux/Windows |
| LP64 | 4 | 8 | 8 | 64-bit Linux, macOS |
| LLP64 | 4 | 4 | 8 | 64-bit Windows |

### Portability Rules

| Rule | Rationale |
|------|-----------|
| Never serialize raw pointers | Not portable across processes or architectures |
| Use relative offsets | FlatBuffers, Cap'n Proto use offset-based addressing |
| Use fixed-width integers | `uint32_t`, `int64_t` — not `int` or `long` |
| Define explicit wire byte order | Protocol spec must declare endianness |
| Use `htonl()`/`ntohl()` for network protocols | Convert to/from network byte order |

---

## 15.4 — Stable Interface Patterns

### Android VINTF (Vendor Interface)

| Component | Purpose |
|-----------|---------|
| Framework Compatibility Matrix | Declares what HAL versions framework supports |
| Device Manifest | Declares which HAL versions vendor implements |
| VINTF check at boot | Prevents framework/vendor ABI mismatch |
| `@VintfStability` | Marks AIDL interface as frozen for cross-partition use |

### Stable AIDL Versioning

```
aidl_interface {
    name: "android.hardware.vehicle",
    stability: "vintf",
    versions: ["1", "2", "3"],
    // Version 3 is current; 1 and 2 are frozen
}
```

- **Freeze**: `aidl_freeze_api` creates versioned snapshot
- **Hash verification**: `.hash` file (SHA256) checked in CI
- **Backward compatibility**: New version must only ADD methods/types

### Linux Kernel Stable ABI

| Interface | Stability |
|-----------|-----------|
| Syscall numbers | Stable forever (never reused) |
| UAPI headers (`include/uapi/`) | Stable (kernel-userspace contract) |
| Kernel module ABI (KABI) | NOT stable (rebuild per kernel version) |
| `/proc` and `/sys` entries | Mostly stable (some deprecated) |

### gRPC Service Versioning

| Pattern | Example |
|---------|---------|
| Package versioning | `package com.example.fleet.v1;` / `v2` |
| Simultaneous serving | Both v1 and v2 services registered on same server |
| Client migration | Clients upgrade at their own pace |
| Deprecation | v1 marked deprecated; removed after migration period |

### AUTOSAR HAL Versioning

- BSW module versions in ARXML manifests
- Semantic versioning for BSW interfaces
- AUTOSAR schema version (`AUTOSAR_00050` etc.) in each ARXML file

---

## 15.5 — WebAssembly as Interop Layer

### What Is WebAssembly

| Property | Value |
|----------|-------|
| Governance | W3C WebAssembly Working Group (Tier 2) |
| Format | Binary instruction format for stack-based VM |
| Execution | Browser JS engines + standalone (Wasmtime, WasmEdge, Wasmer) |
| Memory | Linear memory (byte-addressable, bounds-checked) |
| Security | Sandboxed execution (no direct OS access without imports) |

### Cross-Language Compilation Targets

| Source Language | WASM Compiler |
|---------------|---------------|
| C/C++ | Emscripten, wasi-sdk (clang --target=wasm32-wasi) |
| Rust | `wasm32-unknown-unknown` / `wasm32-wasi` target |
| Go | TinyGo (full Go has large runtime overhead) |
| AssemblyScript | Native WASM output (TypeScript-like syntax) |
| Kotlin | Kotlin/Wasm (experimental) |
| C# | Blazor WebAssembly (.NET → WASM) |

### WASM Interface Types (Component Model — WIT)

```wit
package example:vehicle@1.0.0;

interface vehicle-service {
    record vehicle {
        vin: string,
        make: string,
        model: string,
        year: u16,
    }

    get-vehicle: func(vin: string) -> result<vehicle, string>;
    list-vehicles: func() -> list<vehicle>;
}

world vehicle-world {
    export vehicle-service;
}
```

**WIT Types:**
| Category | Types |
|----------|-------|
| Primitives | bool, s8/u8, s16/u16, s32/u32, s64/u64, f32, f64, char, string |
| Containers | `list<T>`, `tuple<T...>`, `option<T>`, `result<T, E>` |
| Compound | record, variant, enum, flags |

### Data Contract Use Cases for WASM

| Use Case | Description |
|----------|-------------|
| Schema validation in browser | Compile protoc/ajv to WASM → validate in browser |
| Policy evaluation | OPA/Rego policies compiled to WASM |
| Cross-language codec | One WASM codec module callable from any host |
| Edge computing | Run serialization logic on CDN edge (Cloudflare Workers, Fastly) |

---

## 15.6 — REST/OpenAPI as Lingua Franca

### Why REST + JSON Is the Universal Interop Layer

| Reason | Explanation |
|--------|-------------|
| Universal language support | Every language has HTTP client + JSON library |
| No codegen required | Can integrate without build tooling |
| Human-readable | Debug with curl, browser, Postman |
| Firewall-friendly | HTTP/HTTPS on ports 80/443 pass all firewalls |
| Tooling mature | Swagger UI, Postman, Insomnia, curl |
| Ecosystem depth | Caching (CDN), load balancing, WAF, API gateways |

### REST as Interop Bridge

| Scenario | Bridge Pattern |
|----------|---------------|
| gRPC ↔ REST | gRPC-Gateway (transcoding proxy) |
| SOAP ↔ REST | SOAP-to-REST adapter service |
| GraphQL ↔ REST | GraphQL endpoint wrapping REST backends |
| Event ↔ REST | Webhook delivery (HTTP POST for events) |
| Legacy ↔ Modern | Anti-corruption layer with REST interface |

### Specification Converters

| Source | Target | Tool |
|--------|--------|------|
| Protobuf (.proto) | OpenAPI | `protoc-gen-openapi`, gRPC-Gateway |
| Smithy | OpenAPI | `smithy-build` with OpenAPI plugin |
| TypeSpec | OpenAPI | `tsp compile` with `@typespec/openapi3` |
| RAML | OpenAPI | `oas-raml-converter` |
| WSDL | OpenAPI | `wsdl-to-openapi` |

### Limitations of REST as Lingua Franca

| Limitation | Impact |
|-----------|--------|
| No bidirectional streaming | Cannot express gRPC bidi streaming |
| Type imprecision | JSON `number` has no int vs float distinction |
| No binary efficiency | JSON is text; 2-10x larger than Protobuf |
| No schema enforcement by default | Must add validation middleware |
| Stateless only | No built-in connection-oriented semantics |
| Versioning complexity | URL path vs header vs query param — no standard |

---

## 15.7 — Cross-Platform Considerations by OS

### Linux

| Dimension | Details |
|-----------|---------|
| Dominance | Cloud, servers, embedded, automotive, robotics |
| IPC | Unix sockets, POSIX shm, D-Bus, Netlink, pipes, FIFOs |
| ABI | System V AMD64 (x86-64), AAPCS64 (ARM64) |
| Serialization | All major formats fully supported |
| Standard | glibc or musl; POSIX.1-2017 compliance |

### Android

| Dimension | Details |
|-----------|---------|
| Kernel | Linux (modified with Binder, Ashmem→memfd, ION→DMABuf) |
| Primary languages | Java/Kotlin (apps), C++ (NDK/HAL) |
| IPC | Binder (AIDL), ContentProvider, Intents, Messenger |
| Serialization | Protobuf (preferred), Parcel (Binder native), JSON |
| Cross-device | gRPC, REST/HTTP |

### macOS / iOS

| Dimension | Details |
|-----------|---------|
| Kernel | XNU (Mach + BSD) |
| Primary languages | Swift, Objective-C, C++ |
| IPC | XPC (Mach ports), POSIX sockets, NSDistributedNotification |
| Serialization | Codable (Swift), NSCoding, Protobuf, JSON |
| Network | URLSession, Network.framework (QUIC support) |

### Windows

| Dimension | Details |
|-----------|---------|
| Kernel | NT kernel |
| Primary languages | C++, C#/.NET |
| IPC | COM/DCOM, Named Pipes, WinSock, ALPC (kernel), Mailslots |
| Serialization | .NET DataContract, WCF bindings, Protobuf, JSON |
| Network | WinHTTP, WinInet, .NET HttpClient |

### Fuchsia

| Dimension | Details |
|-----------|---------|
| Kernel | Zircon (microkernel) |
| Primary languages | C++, Rust, Dart, Go |
| IPC | Zircon channels (FIDL only) |
| Serialization | FIDL binary (native), JSON (config) |
| Design | Capability-based; no global filesystem namespace |

### RTOS (FreeRTOS, Zephyr, QNX, VxWorks)

| Dimension | Details |
|-----------|---------|
| Constraints | Limited RAM (KB-MB), no MMU (some), deterministic scheduling |
| IPC | Message queues, shared memory, mailboxes (OS-specific) |
| Serialization | CBOR, FlatBuffers, SBE, ASN.1 PER (no dynamic allocation) |
| Safety | MISRA C compliance, ISO 26262 / DO-178C certification |
| Toolchains | Cross-compilation mandatory (arm-none-eabi-gcc, etc.) |

---

## 15.8 — Interoperability Anti-Patterns

### Anti-Pattern 1: Language-Specific Serialization

| Bad Practice | Risk |
|-------------|------|
| Java `Serializable` | Not portable; deserialization RCE vulnerabilities |
| Python `pickle` | Not portable; arbitrary code execution |
| .NET `BinaryFormatter` | Deprecated (security); not cross-language |
| Ruby `Marshal` | Ruby-only; version-sensitive |

**Correct**: Use schema-based formats (Protobuf, Avro, JSON Schema).

### Anti-Pattern 2: Struct Memory Layout as Wire Format

| Bad Practice | Risk |
|-------------|------|
| `write(fd, &my_struct, sizeof(my_struct))` | Padding varies by compiler/flags/platform |
| Different `#pragma pack` on reader/writer | Silent data corruption |
| Assuming `sizeof(long)` is same everywhere | 4 bytes on Windows, 8 on Linux (64-bit) |

**Correct**: Explicit serialization, or `__attribute__((packed))` with documented alignment rules for zero-copy.

### Anti-Pattern 3: Assuming Endianness

| Bad Practice | Risk |
|-------------|------|
| Write C struct to file, read on different arch | Byte-swapped integer values |
| Cast `char*` to `int*` without conversion | Undefined behavior + wrong values on different endianness |

**Correct**: Use `htonl()`/`ntohl()`, or use a format with defined byte order.

### Anti-Pattern 4: Nullable Type Confusion

| Bad Practice | Risk |
|-------------|------|
| JSON: treating `null` and missing key identically | Semantic loss (explicit null vs not set) |
| Proto3: no `optional` keyword → zero == absent | Cannot distinguish "set to 0" from "not set" |

**Correct**: Use `optional` (proto3 field presence), `oneOf`, or explicit nullable types.

### Anti-Pattern 5: Protocol-Specific Assumptions

| Bad Practice | Risk |
|-------------|------|
| Depending on HTTP header ordering | Not guaranteed by spec |
| Assuming JSON object key order | RFC 8259: objects are unordered |
| Relying on specific error message text | Changes between versions |

---

## 15.9 — Interoperability Decision Framework

### Decision Tree

```
Q1: Same process?
 └─ YES → Direct function calls. No serialization needed.
 └─ NO → Q2

Q2: Same machine?
 └─ YES → Q2a: Latency-critical / high-throughput?
           └─ YES → Shared memory (Iceoryx, mmap, HIDL FMQ)
           └─ NO → Q2b: Which OS?
                    ├─ Android → AIDL over Binder
                    ├─ Linux → Unix Domain Sockets / D-Bus
                    ├─ macOS/iOS → XPC
                    ├─ Fuchsia → FIDL over Zircon channels
                    └─ Windows → Named Pipes / COM
 └─ NO → Q3

Q3: Network (cross-machine)?
 └─ Q3a: Internal microservices?
    └─ Multi-language + streaming → gRPC + Protobuf
    └─ Event-driven → Kafka + Avro + Schema Registry
 └─ Q3b: External-facing API?
    └─ REST + JSON + OpenAPI
 └─ Q3c: IoT / constrained?
    └─ CoAP + CBOR (or MQTT + Protobuf)
 └─ Q3d: Legacy enterprise?
    └─ SOAP/WSDL (or wrap with REST adapter)

Q4: Safety-critical / embedded?
 └─ Formally certified → ASN.1 PER
 └─ Automotive ECU → SOME/IP, DDS, AUTOSAR COM
 └─ Low-resource MCU → CBOR, FlatBuffers (no heap allocation)

Q5: Schema evolution requirement?
 └─ Long-lived event store → Avro + Schema Registry
 └─ RPC with independent upgrades → Protobuf (tag-based)
 └─ No evolution (fixed protocol) → SBE, fixed C structs
```

---

## 15.10 — Interoperability Maturity Matrix

| Technology | Cross-Lang | Cross-Platform | Cross-OS | Cross-Arch | Evolution | Registry | Formal Spec |
|-----------|-----------|--------------|---------|-----------|-----------|---------|------------|
| **Protobuf** | Excellent | Excellent | Yes | Yes | Strong (tags) | BSR/Confluent | Yes |
| **gRPC** | Excellent | Excellent | Yes | Yes | Strong | BSR | Yes (CNCF) |
| **REST+JSON+OpenAPI** | Universal | Universal | Yes | Yes | Partial | Apicurio | Yes (OAI) |
| **Avro** | Excellent | Excellent | Yes | Yes | Strong | Confluent | Yes (Apache) |
| **Thrift** | Good | Good | Yes | Yes | Moderate | — | Yes (Apache) |
| **CBOR** | Good | Good | Yes | Yes | Partial | — | Yes (IETF) |
| **FlatBuffers** | Good | Good | Yes | Yes | Moderate | — | Yes (Google) |
| **Cap'n Proto** | Moderate | Moderate | Yes | Yes | Moderate | — | Partial |
| **SBE** | Limited | Limited | Yes | LE default | Very limited | — | Yes (FIX) |
| **AIDL** | Limited | No (Android) | Android | Yes | Strong | — | Yes (AOSP) |
| **FIDL** | Limited | No (Fuchsia) | Fuchsia | Yes | Strong | — | Yes (Google) |
| **ASN.1** | Good | Excellent | Yes | Yes | Strong | — | Yes (ISO/ITU) |
| **DDS (RTPS)** | Moderate | Good | Yes | Yes | Strong | — | Yes (OMG) |
| **SOME/IP** | Limited | Limited | POSIX/Win | Yes | Moderate | — | Yes (AUTOSAR) |
| **GraphQL** | Excellent | Excellent | Yes | Yes | Partial | Apollo | Yes (Foundation) |
| **AsyncAPI+Kafka** | Excellent | Excellent | Yes | Yes | Depends | Confluent | Yes |
| **MessagePack** | Excellent | Excellent | Yes | Yes | None | — | Yes (msgpack.org) |
| **WASM (WIT)** | Excellent | Excellent | Yes | Yes | In progress | — | Yes (W3C) |

---

## 15.11 — Final Synthesis: Universal Technology Selection Guidance

### Tier 1 — Universal Interop (Any Language, Any Platform)

| Technology | Best For |
|-----------|----------|
| **REST + JSON + OpenAPI** | External APIs, partner integrations, browser clients |
| **gRPC + Protobuf** | Internal microservices, multi-language, streaming |
| **Kafka + Avro + Schema Registry** | Event-driven architecture, data pipelines |

### Tier 2 — Broad Interop (Most Languages, Most Platforms)

| Technology | Best For |
|-----------|----------|
| **GraphQL** | Client-driven queries, API aggregation |
| **CBOR** | IoT, constrained environments (IETF standard) |
| **FlatBuffers** | Performance-critical with broad language support |
| **MessagePack** | Compact JSON alternative (no schema) |

### Tier 3 — Domain-Specific Interop

| Technology | Domain |
|-----------|--------|
| **DDS (RTPS)** | Real-time systems, robotics, defense |
| **SOME/IP** | Automotive (AUTOSAR) |
| **ASN.1** | Telecom, PKI/security, aviation |
| **OPC UA** | Industrial automation |

### Tier 4 — Platform-Locked (Single OS/Ecosystem)

| Technology | Platform |
|-----------|----------|
| **AIDL/Binder** | Android only |
| **FIDL/Zircon** | Fuchsia only |
| **XPC/Mach** | macOS/iOS only |
| **COM/DCOM** | Windows only |
| **D-Bus** | Linux desktop (freedesktop.org) |

### Selection Criteria Summary

| Priority | Choose |
|----------|--------|
| Maximum interop | REST + JSON (or gRPC with REST gateway) |
| Maximum performance | FlatBuffers / SBE / shared memory |
| Maximum evolution safety | Protobuf + buf / Avro + Schema Registry |
| Maximum formal rigor | ASN.1 (ISO/ITU-T certified tooling available) |
| Maximum real-time | DDS (RTPS) with QoS policies |
| Maximum simplicity | JSON + JSON Schema |
| Minimum wire size | Protobuf / SBE / ASN.1 PER |

---

## 15.12 — Cross-References to All Other Volumes

| Volume | Title | Relevance to Interoperability |
|--------|-------|-------------------------------|
| Vol 01 | Data Modeling Systems | Type systems that underpin cross-language mapping |
| Vol 02 | Interface Definition Languages | IDL syntax that generates language bindings |
| Vol 03 | Schema Systems | Schema definitions enabling validation across platforms |
| Vol 04 | Serialization Formats | Wire formats that enable cross-language data exchange |
| Vol 05 | Binary Encoding Rules | Encoding mechanisms (endianness, varint, TLV) |
| Vol 06 | RPC Frameworks | Cross-language remote procedure calls |
| Vol 07 | IPC Mechanisms | Platform-specific inter-process communication |
| Vol 08 | API Contract Systems | OpenAPI/AsyncAPI as interop specifications |
| Vol 09 | Zero-Copy Systems | ABI-dependent binary formats |
| Vol 10 | Schema Evolution | Cross-version compatibility rules |
| Vol 11 | Industry-Specific | Domain constraints on interoperability |
| Vol 12 | Governance & Standards | Standards bodies ensuring interop compliance |
| Vol 13 | Tooling & Code Generation | Build tools that produce language bindings |
| Vol 14 | Transport & Runtime | Transport mechanisms carrying serialized data |

---

*End of Volume 15 — Cross-Language / Cross-Platform Interoperability*

---

## ENCYCLOPEDIA COMPLETE

All 15 volumes of the Universal Data Contract, Serialization, IDL & Interoperability Architecture Encyclopedia have been generated:

| Volume | Title | File |
|--------|-------|------|
| 01 | Data Modeling Systems | VOL_01_DATA_MODELING_SYSTEMS.md |
| 02 | Interface Definition Languages | VOL_02_INTERFACE_DEFINITION_LANGUAGES.md |
| 03 | Schema Systems | VOL_03_SCHEMA_SYSTEMS.md |
| 04 | Serialization Formats | VOL_04_SERIALIZATION_FORMATS.md |
| 05 | Binary Encoding Rules | VOL_05_BINARY_ENCODING_RULES.md |
| 06 | RPC Frameworks | VOL_06_RPC_FRAMEWORKS.md |
| 07 | IPC Mechanisms | VOL_07_IPC_MECHANISMS.md |
| 08 | API Contract Systems | VOL_08_API_CONTRACT_SYSTEMS.md |
| 09 | Zero-Copy Systems | VOL_09_ZERO_COPY_SYSTEMS.md |
| 10 | Schema Evolution | VOL_10_SCHEMA_EVOLUTION.md |
| 11 | Industry-Specific | VOL_11_INDUSTRY_SPECIFIC.md |
| 12 | Governance & Standards | VOL_12_GOVERNANCE_STANDARDS.md |
| 13 | Tooling & Code Generation | VOL_13_TOOLING_CODEGEN.md |
| 14 | Transport & Runtime Binding | VOL_14_TRANSPORT_RUNTIME.md |
| 15 | Cross-Platform Interoperability | VOL_15_INTEROPERABILITY.md |
