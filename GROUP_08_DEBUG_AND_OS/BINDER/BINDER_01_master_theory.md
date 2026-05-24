# ANDROID BINDER & AIDL — MASTER THEORY
# ════════════════════════════════════════════════════════════════════
# Protocol: Android Binder IPC | Document: 01 of 08
# Depth: Encyclopedia-level — from first principles to kernel internals
# ════════════════════════════════════════════════════════════════════

---

## SECTION 1: WHAT IS BINDER?

Binder is Android's **primary inter-process communication (IPC) mechanism**. It enables any process to call methods on objects in another process as if they were local — a concept called **remote procedure call (RPC)**.

### Why not use standard Linux IPC?
| Linux IPC | Problem for Android |
|-----------|-------------------|
| Pipes/FIFOs | Unidirectional, no object model, no security identity |
| Unix sockets | 2-copy, no automatic thread management, no death notification |
| Shared memory | No access control, no serialization, race conditions |
| Signals | Extremely limited data, unreliable for complex IPC |
| Message queues | No object references, limited size, 2-copy |
| D-Bus | Too slow (userspace daemon), not designed for mobile |

Binder provides: **one-copy transfer, kernel-verified caller identity (UID/PID), automatic object lifecycle management, death notifications, and thread pool management** — all required for a secure mobile OS.

---

## SECTION 2: HISTORY & ORIGIN

| Year | Event |
|------|-------|
| 1991 | Object-oriented IPC concepts at Be Inc. |
| 2000 | OpenBinder created by Be Inc. (BeOS) |
| 2002 | PalmSource acquires Be Inc., continues OpenBinder |
| 2005 | Dianne Hackborn (Android team) rewrites Binder for Linux kernel |
| 2008 | Android 1.0 released — Binder is sole IPC mechanism |
| 2015 | Android 6.0: AIDL improvements, oneway semantics refined |
| 2017 | Android 8.0 (Treble): HIDL + hwbinder + vndbinder split |
| 2020 | Android 11: AIDL for HALs begins (replacing HIDL) |
| 2022 | Android 13: New HALs MUST use AIDL (HIDL deprecated) |
| 2024 | Android 15: Rust AIDL backends, nearly complete HIDL→AIDL migration |

---

## SECTION 3: ARCHITECTURE OVERVIEW

```
┌──────────────────────────────────────────────────────────────┐
│                        USER SPACE                             │
│                                                              │
│  ┌─────────────┐         ┌─────────────────────────────┐   │
│  │   CLIENT    │         │          SERVER              │   │
│  │  Process A  │         │         Process B            │   │
│  │             │         │                              │   │
│  │ IBinder     │         │  Binder (BBinder)            │   │
│  │  Proxy      │ ──────→ │    ↓                        │   │
│  │ (BpBinder)  │         │  onTransact()               │   │
│  │             │         │    ↓                        │   │
│  │ Parcel out  │         │  Business Logic             │   │
│  └──────┬──────┘         └──────────────┬──────────────┘   │
│         │ ioctl(BINDER_WRITE_READ)       │                   │
├─────────┼────────────────────────────────┼───────────────────┤
│         │           KERNEL SPACE         │                   │
│         ▼                                ▼                   │
│  ┌─────────────────────────────────────────────────────┐    │
│  │              BINDER DRIVER (/dev/binder)             │    │
│  │                                                     │    │
│  │  • Copy data from client to server's mmap'd buffer  │    │
│  │  • Verify UID/PID of caller                         │    │
│  │  • Manage object references (binder_node/binder_ref)│    │
│  │  • Thread wakeup/scheduling                         │    │
│  │  • Death notification delivery                      │    │
│  └─────────────────────────────────────────────────────┘    │
└──────────────────────────────────────────────────────────────┘
```

### Key Participants:
1. **Client** (caller): Has a proxy (`BpBinder`) representing the remote object
2. **Server** (callee): Has the real object (`BBinder`) that implements the interface
3. **Binder Driver**: Kernel module that routes transactions, manages memory
4. **ServiceManager**: Special binder service (handle 0) — registry of named services
5. **Parcel**: Serialization container for transaction data

---

## SECTION 4: THE BINDER KERNEL DRIVER

### 4.1 Device Nodes
```
/dev/binder      — Framework binder (apps ↔ system services)
/dev/hwbinder    — Hardware binder (framework ↔ HALs)
/dev/vndbinder   — Vendor binder (vendor ↔ vendor)
```
All three use the same kernel driver code, different device instances.

### 4.2 Key Data Structures (kernel)

```c
struct binder_proc {
    struct hlist_node proc_node;     // Linked in global process list
    struct rb_root threads;          // Red-black tree of binder_threads
    struct rb_root nodes;            // Binder objects owned by this process
    struct rb_root refs_by_desc;     // References to other processes' objects
    struct rb_root refs_by_node;     // Same refs, indexed by node
    struct list_head waiting_threads; // Threads waiting for work
    pid_t pid;                       // Process ID
    struct vm_area_struct *vma;      // mmap'd region (kernel ↔ userspace shared)
    void *buffer;                    // Transaction buffer start
    size_t buffer_size;              // Total mmap size (default: 1MB)
    // ...
};

struct binder_thread {
    struct binder_proc *proc;        // Owning process
    struct rb_node rb_node;          // In proc->threads tree
    struct list_head waiting_thread_node;
    pid_t pid;                       // Thread ID
    struct binder_transaction *transaction_stack;  // Current work
    wait_queue_head_t wait;          // Sleep/wake mechanism
    // ...
};

struct binder_node {
    struct rb_node rb_node;          // In proc->nodes tree
    struct binder_proc *proc;        // Owning process
    struct list_head refs;           // Processes holding references
    int internal_strong_refs;        // Reference counts
    int local_strong_refs;
    binder_uintptr_t ptr;           // Userspace object pointer (BBinder*)
    binder_uintptr_t cookie;        // Extra data
    // ...
};

struct binder_ref {
    struct rb_node rb_node_desc;     // In proc->refs_by_desc
    struct rb_node rb_node_node;     // In proc->refs_by_node
    struct binder_node *node;        // Target node
    struct binder_proc *proc;        // Owning process
    uint32_t desc;                   // Handle number (local to this proc)
    // ...
};

struct binder_transaction {
    struct binder_thread *from;      // Sending thread
    struct binder_thread *to_thread; // Target thread (or NULL for any)
    struct binder_proc *to_proc;     // Target process
    struct binder_buffer *buffer;    // Transaction data buffer
    unsigned int code;               // Transaction code (method ID)
    unsigned int flags;              // TF_ONE_WAY, TF_ACCEPT_FDS, etc.
    // ...
};

struct binder_buffer {
    struct list_head entry;          // Free/allocated buffer list
    struct binder_transaction *transaction;
    struct binder_node *target_node;
    size_t data_size;                // Size of Parcel data
    size_t offsets_size;             // Size of flat_binder_object offsets
    void *data;                      // Points into mmap'd region
    // ...
};
```

### 4.3 The One-Copy Trick (mmap)

Traditional IPC is 2-copy: User A → Kernel buffer → User B.

Binder achieves 1-copy:
```
1. Server opens /dev/binder → calls mmap() → kernel allocates physical pages
2. These pages are mapped into BOTH:
   - Kernel's virtual address space (for writing)
   - Server's virtual address space (for reading)
3. When client sends transaction:
   - Kernel copies data from client userspace → binder_buffer (1 copy)
   - Server can read directly from its mmap'd view (0 additional copies)
   
Result: ONE copy total (client → kernel/server shared pages)
```

### 4.4 ioctl Interface

All Binder communication goes through a single syscall: `ioctl(fd, BINDER_WRITE_READ, &bwr)`

```c
struct binder_write_read {
    binder_size_t write_size;       // Bytes to write (commands to kernel)
    binder_size_t write_consumed;   // Bytes kernel consumed
    binder_uintptr_t write_buffer;  // Pointer to write command buffer
    binder_size_t read_size;        // Bytes available to read
    binder_size_t read_consumed;    // Bytes kernel wrote
    binder_uintptr_t read_buffer;   // Pointer to read result buffer
};
```

**Write commands (userspace → kernel):**
| Command | Purpose |
|---------|---------|
| BC_TRANSACTION | Send transaction to target |
| BC_REPLY | Send reply to caller |
| BC_ACQUIRE | Increment strong reference |
| BC_RELEASE | Decrement strong reference |
| BC_INCREFS | Increment weak reference |
| BC_DECREFS | Decrement weak reference |
| BC_FREE_BUFFER | Free transaction buffer |
| BC_REQUEST_DEATH_NOTIFICATION | Register for death notification |
| BC_DEAD_BINDER_DONE | Acknowledge death notification |
| BC_ENTER_LOOPER | Thread enters binder loop |
| BC_REGISTER_LOOPER | Thread registered as looper |
| BC_EXIT_LOOPER | Thread exits binder loop |

**Read commands (kernel → userspace):**
| Command | Purpose |
|---------|---------|
| BR_TRANSACTION | Incoming transaction to handle |
| BR_REPLY | Reply to our transaction |
| BR_ACQUIRE | Someone acquired reference to our node |
| BR_RELEASE | Someone released reference |
| BR_DEAD_BINDER | Remote object died |
| BR_NOOP | No-op (filler) |
| BR_SPAWN_LOOPER | Kernel requests new thread |
| BR_TRANSACTION_COMPLETE | Our transaction was queued |
| BR_FAILED_REPLY | Transaction failed |

---

## SECTION 5: TRANSACTION FLOW (Step by Step)

### Synchronous Transaction (REQUEST → REPLY):

```
CLIENT (Process A)                    BINDER DRIVER                    SERVER (Process B)
     │                                     │                                │
  1. │ Prepare Parcel (serialize args)     │                                │
  2. │ ioctl(BINDER_WRITE_READ)           │                                │
     │  write: BC_TRANSACTION              │                                │
     │  [target handle, code, data]        │                                │
     │ ─────────────────────────────────→  │                                │
  3. │                                     │ Validate caller UID/PID        │
  4. │                                     │ Find target process (handle→node)
  5. │                                     │ Allocate buffer in server's mmap│
  6. │                                     │ Copy client data → server buffer│
  7. │                                     │ Wake server thread              │
     │                                     │ ─────────────────────────────→ │
  8. │                                     │                                │ BR_TRANSACTION
  9. │                                     │                                │ onTransact(code, data)
 10. │                                     │                                │ Execute business logic
 11. │                                     │                                │ Prepare reply Parcel
 12. │                                     │ ←───────────────────────────── │
     │                                     │  BC_REPLY [reply data]         │
 13. │                                     │ Copy reply → client            │
 14. │ ←──────────────────────────────────  │                                │
     │  BR_REPLY [reply data]              │                                │
 15. │ Deserialize reply, return to caller │                                │
```

**Important details:**
- Client thread **blocks** (sleeps) until reply arrives (steps 2-14)
- Server thread selected from thread pool (or new one spawned)
- UID/PID of caller available to server (`getCallingUid()`, `getCallingPid()`)
- Transaction code identifies which method (like a function pointer index)

### Oneway Transaction (fire-and-forget):

- Client does NOT wait for reply
- Returns immediately after `BR_TRANSACTION_COMPLETE`
- Server processes asynchronously
- Used for: callbacks, notifications, events
- Queued in target's async todo list (processed in order)

---

## SECTION 6: SERVICEMANAGER

ServiceManager is the **name service** for Binder — it's always handle 0 (well-known handle).

### Role:
1. **Register**: Server calls `addService("name", IBinder)` to publish service
2. **Lookup**: Client calls `getService("name")` to get handle to service
3. **List**: `listServices()` returns all registered services

### Flow:
```
Server                    ServiceManager              Client
  │                            │                        │
  │ addService("my.svc", obj) │                        │
  │ ──────────────────────────→│                        │
  │                            │ Store: "my.svc"→handle │
  │                            │                        │
  │                            │←───────────────────────│
  │                            │ getService("my.svc")   │
  │                            │───────────────────────→│
  │                            │   Return: IBinder proxy│
  │                            │                        │
  │←────────────────────────────────────────────────────│
  │            Client now has direct proxy to Server     │
  │            (subsequent calls bypass ServiceManager)  │
```

### ServiceManager for each domain:
- `/dev/binder` → `servicemanager` (frameworks)
- `/dev/hwbinder` → `hwservicemanager` (HIDL HALs)
- `/dev/vndbinder` → `vndservicemanager` (vendor)
- Since Android 11: `servicemanager` handles AIDL HALs on hwbinder too

---

## SECTION 7: PARCEL — SERIALIZATION FORMAT

A **Parcel** is the serialization container for Binder transaction data.

### What can a Parcel contain?
| Type | Method | Notes |
|------|--------|-------|
| Primitives | writeInt32(), writeFloat(), writeString16() | Basic types |
| Byte arrays | writeByteArray() | Raw data |
| Binder objects | writeStrongBinder(IBinder) | Passes object references across processes |
| File descriptors | writeFileDescriptor(fd) | Kernel duplicates FD into target process |
| Parcelables | writeParcelable() | Custom serializable objects |
| IBinder arrays | writeBinderVector() | Multiple object references |

### Parcel Binary Layout:
```
┌────────────────────────────────────────────────────────┐
│ Data buffer (sequential writes)                        │
│ [int32][int32][string16_len][string16_data][IBinder].. │
├────────────────────────────────────────────────────────┤
│ Objects offset array                                   │
│ [offset_0, offset_1, ...]                             │
│ (indices into data where flat_binder_objects live)     │
└────────────────────────────────────────────────────────┘
```

### flat_binder_object (for passing Binder references):
```c
struct flat_binder_object {
    __u32 hdr_type;          // BINDER_TYPE_BINDER or BINDER_TYPE_HANDLE
    __u32 flags;             // Stability, scheduling priority
    union {
        binder_uintptr_t binder;   // Local pointer (if sending own object)
        __u32 handle;              // Handle number (if forwarding reference)
    };
    binder_uintptr_t cookie;       // Extra context
};
```

When kernel sees a `flat_binder_object` in transaction data:
- If sending own object (BINDER_TYPE_BINDER): kernel creates `binder_node`, gives receiver a `binder_ref` (handle)
- If forwarding received reference (BINDER_TYPE_HANDLE): kernel translates handle to target's handle space

---

## SECTION 8: AIDL (Android Interface Definition Language)

### 8.1 What AIDL Does
AIDL defines a cross-process interface. The `aidl` compiler generates:
- **Proxy (Bp)**: Client-side stub that serializes calls → Binder transaction
- **Stub (Bn)**: Server-side skeleton that deserializes → calls real implementation
- **Interface**: Java/C++/Rust interface that both proxy and stub implement

### 8.2 AIDL File Syntax
```aidl
// IVehicleService.aidl
package android.automotive.vehicle;

import android.automotive.vehicle.VehiclePropValue;
import android.automotive.vehicle.IVehicleCallback;

@VintfStability  // Stable across Android versions (for HALs)
interface IVehicleService {
    // Synchronous method (blocks until reply)
    VehiclePropValue getProperty(int propId, int areaId);
    
    // Oneway method (async, no return value)
    oneway void setProperty(in VehiclePropValue value);
    
    // Callback registration
    void subscribe(IVehicleCallback callback, in int[] propIds, float sampleRate);
    
    // Unsubscribe
    void unsubscribe(IVehicleCallback callback, in int[] propIds);
}
```

### 8.3 Generated Code Structure (C++)
```
IVehicleService.aidl
       │
       ▼ (aidl compiler)
       │
       ├── IVehicleService.h          (Abstract interface)
       ├── BpVehicleService.h/.cpp    (Client proxy - serialization)
       ├── BnVehicleService.h/.cpp    (Server stub - deserialization)
       └── IVehicleService.cpp        (Parcel read/write helpers)
```

### 8.4 AIDL Data Types
| AIDL Type | Java | C++ | Rust |
|-----------|------|-----|------|
| boolean | boolean | bool | bool |
| int | int | int32_t | i32 |
| long | long | int64_t | i64 |
| float | float | float | f32 |
| double | double | double | f64 |
| String | String | std::string | String |
| IBinder | IBinder | sp<IBinder> | SpIBinder |
| ParcelFileDescriptor | ParcelFileDescriptor | ScopedFileDescriptor | ParcelFileDescriptor |
| T[] | T[] | std::vector<T> | Vec<T> |
| parcelable | Parcelable | Parcelable | — |
| @nullable T | @Nullable T | std::optional<T> | Option<T> |

### 8.5 AIDL Annotations
| Annotation | Purpose |
|-----------|---------|
| @VintfStability | Interface is part of VINTF (stable ABI for HALs) |
| @nullable | Parameter/return can be null |
| @utf8InCpp | String is UTF-8 in C++ (vs default UTF-16) |
| @JavaPassthrough | Pass annotation through to generated Java code |
| @Backing(type="int") | Enum backing type |
| @FixedSize | Parcelable has fixed size (for shared memory) |
| @Hide | Hidden from SDK |

### 8.6 Direction Tags
```aidl
void method(in ParcelData input,    // Caller → Callee (read-only for callee)
            out ParcelData output,   // Callee → Caller (callee fills it)
            inout ParcelData both);  // Both directions
```
- `in`: Default. Data flows client → server.
- `out`: Server fills data, returns to client.
- `inout`: Data flows both ways (two copies — avoid if possible).

---

## SECTION 9: HIDL (HAL Interface Definition Language)

### 9.1 Why HIDL Existed (Project Treble)
Before Android 8.0: HALs were `.so` files loaded directly into system processes (tight coupling). OEM HAL update required full system image update.

**Project Treble (Android 8.0)**: Separated framework from vendor via versioned interfaces:
- HALs run in **separate processes** (binderized)
- Framework ↔ HAL communication via **HIDL over hwbinder**
- OEM can update HAL without rebuilding framework

### 9.2 HIDL Syntax
```hidl
// IVehicle.hal (android.hardware.automotive.vehicle@2.0)
package android.hardware.automotive.vehicle@2.0;

import IVehicleCallback;

interface IVehicle {
    getAllPropConfigs() generates (vec<VehiclePropConfig> configs);
    
    get(VehiclePropValue requestedPropValue)
        generates (StatusCode status, VehiclePropValue propValue);
    
    set(VehiclePropValue propValue) generates (StatusCode status);
    
    subscribe(IVehicleCallback callback, vec<SubscribeOptions> options)
        generates (StatusCode status);
    
    unsubscribe(IVehicleCallback callback, int32_t propId)
        generates (StatusCode status);
};
```

### 9.3 HIDL vs AIDL Key Differences
| Feature | HIDL | AIDL (for HALs) |
|---------|------|-----------------|
| Return values | `generates (...)` clause | Normal return + out params |
| Versioning | Package@major.minor | Interface version + hash |
| Transport | hwbinder only | hwbinder (AIDL HALs) |
| FMQ | Built-in (Fast Message Queue) | Supported via SharedMemory |
| Status | Deprecated (Android 13+) | Active, preferred |
| Inheritance | Interface extension | Interface extension |

### 9.4 Passthrough vs Binderized HALs
| Mode | Description | Performance | Isolation |
|------|-------------|-------------|-----------|
| Binderized | HAL in separate process, IPC via hwbinder | ~50µs overhead | Full process isolation |
| Passthrough | HAL `.so` loaded into client process (dlopen) | Zero IPC overhead | No isolation (same process) |

Passthrough mode was transitional — most HALs are now binderized (or AIDL).

---

## SECTION 10: REFERENCE COUNTING & OBJECT LIFECYCLE

### Strong and Weak References

Binder uses reference counting to manage object lifecycle:

```
Client A ──── strong ref (BpBinder) ────→ BBinder (Server)
Client B ──── strong ref (BpBinder) ────→ BBinder (Server)
Client C ──── weak ref ─────────────────→ BBinder (Server)

When ALL strong refs released → object can be destroyed
Weak refs don't prevent destruction (but can be promoted to strong)
```

### Kernel Reference Tracking:
```
BC_ACQUIRE  → Increment strong ref on binder_node
BC_RELEASE  → Decrement strong ref on binder_node
BC_INCREFS  → Increment weak ref
BC_DECREFS  → Decrement weak ref

When strong refs = 0:
  → Kernel sends BR_RELEASE to owning process
  → Server can destroy the object
```

### sp<> and wp<> in C++:
```cpp
sp<IFoo> strongPtr = service;   // Strong pointer: prevents destruction
wp<IFoo> weakPtr = service;     // Weak pointer: allows destruction

// Promote weak to strong (may fail if object destroyed)
sp<IFoo> promoted = weakPtr.promote();
if (promoted != nullptr) { /* Object still alive */ }
```

---

## SECTION 11: DEATH NOTIFICATION (linkToDeath)

When a server process dies (crashes, exits), clients need to know. Binder provides **death notifications**:

### Registration:
```cpp
// Client registers for death notification
class MyDeathRecipient : public IBinder::DeathRecipient {
    void binderDied(const wp<IBinder>& who) override {
        // Server died! Handle gracefully (reconnect, cleanup, etc.)
        ALOGE("Service died! Reconnecting...");
    }
};

sp<IBinder> binder = getService("my.service");
sp<MyDeathRecipient> death = new MyDeathRecipient();
binder->linkToDeath(death, nullptr, 0);
```

### Kernel mechanism:
```
1. Client sends BC_REQUEST_DEATH_NOTIFICATION to kernel
2. Kernel stores death callback in binder_ref->death
3. When server process exits:
   a. Kernel iterates all binder_nodes owned by dying process
   b. For each node, iterates all refs with death notifications
   c. Sends BR_DEAD_BINDER to each registered client
4. Client's DeathRecipient::binderDied() is called
```

### Java equivalent:
```java
IBinder binder = ServiceManager.getService("my.service");
binder.linkToDeath(() -> {
    Log.e(TAG, "Service died!");
}, 0);
```

---

## SECTION 12: SECURITY MODEL

### 12.1 Caller Identity
Every Binder transaction carries the caller's **UID** and **PID** (set by kernel, cannot be forged):
```cpp
// In server's onTransact():
int callingUid = IPCThreadState::self()->getCallingUid();
int callingPid = IPCThreadState::self()->getCallingPid();

// Permission check
if (callingUid != AID_SYSTEM && callingUid != AID_ROOT) {
    return PERMISSION_DENIED;
}
```

### 12.2 SELinux Policy
SELinux controls which processes can:
- Open binder device nodes (binder, hwbinder, vndbinder)
- Call specific services
- Register services
- Transfer specific binder objects

Example SELinux rules:
```
# Allow app to call system_server via binder
allow untrusted_app system_server:binder { call transfer };

# Allow HAL to register with hwservicemanager
allow vehicle_hal hwservicemanager:binder { call transfer };
allow vehicle_hal vehicle_hal_server:hwservice_manager { add find };
```

### 12.3 Permission Checks
```java
// System services check permissions before processing
public int getVehicleProperty(int propId) {
    mContext.enforceCallingPermission(
        Car.PERMISSION_SPEED,  // "android.car.permission.CAR_SPEED"
        "requires SPEED permission");
    return mVehicleHal.get(propId);
}
```

### 12.4 Binder Token (IBinder as Capability)
An IBinder reference acts as an unforgeable **capability token**:
- If you have a reference to a Binder object, you can call it
- References can only be obtained from ServiceManager or from another process that already has one
- Cannot be guessed or forged (kernel manages all handle translations)

---

## SECTION 13: THREAD POOL & SCHEDULING

### 13.1 Binder Thread Pool
Each process using Binder maintains a thread pool for handling incoming transactions:

```cpp
// In main() of a Binder server process:
ProcessState::self()->startThreadPool();  // Start pool with max threads
IPCThreadState::self()->joinThreadPool(); // Main thread also joins pool

// Default: 15 additional threads + 1 main = 16 total
// Configurable: ProcessState::self()->setThreadPoolMaxThreadCount(N);
```

### 13.2 Thread Spawning
```
1. Transaction arrives for process B
2. Kernel checks B's waiting_threads list
3. If idle thread available → wake it
4. If no idle thread AND pool not full → send BR_SPAWN_LOOPER to B
5. If pool full → transaction waits in queue
```

### 13.3 Priority Inheritance
Binder supports **priority inheritance** to prevent priority inversion:
- Client at priority 10 calls server
- Server thread inherits client's priority 10 for duration of transaction
- Prevents low-priority server thread from being preempted while holding client's call
- Controlled by `flat_binder_object.flags` (scheduling policy + priority)

### 13.4 Oneway Queue
Oneway (async) transactions are queued per-target-node:
- Processed sequentially (preserves ordering)
- Uses a separate async thread from the pool
- If queue is full (buffer exhausted) → client gets FAILED_REPLY

---

## SECTION 14: FILE DESCRIPTOR PASSING

Binder can transfer file descriptors between processes (kernel duplicates them):

```cpp
// Sender:
Parcel data;
int fd = open("/dev/some_device", O_RDWR);
data.writeFileDescriptor(fd);
// After write, sender's FD is still valid (not consumed)

// Receiver:
int receivedFd = data.readFileDescriptor();
// This is a NEW FD in receiver's process (different number, same underlying file)
```

### Kernel implementation:
```
1. Sender puts FD in Parcel (marked as BINDER_TYPE_FD)
2. Kernel's binder_translate_fd():
   a. Gets struct file* from sender's fd table
   b. Allocates new FD in receiver's fd table
   c. Points it to same struct file*
   d. Replaces FD number in Parcel data with new number
3. Receiver reads new FD number from Parcel
```

Used for: Passing hardware buffers (GPU, camera), shared memory, device access.

---

## SECTION 15: SHARED MEMORY & LARGE DATA

### The 1MB Transaction Limit
Default Binder buffer per process = 1 MB. Single transaction cannot exceed available buffer space. For large data:

### Solution 1: Ashmem / SharedMemory
```cpp
// Create shared memory
int fd = ashmem_create_region("my_data", 4096);
void* ptr = mmap(NULL, 4096, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);

// Write data to shared memory
memcpy(ptr, large_data, 4096);

// Send FD via Binder (only FD travels through Binder, not the data!)
parcel.writeFileDescriptor(fd);
// Receiver maps the same FD → zero-copy large data transfer
```

### Solution 2: HIDL Fast Message Queue (FMQ)
```cpp
// Lock-free single-producer-single-consumer ring buffer
// Shared memory + atomic indices → no Binder call for each message!
MessageQueue<SensorEvent, kSynchronizedReadWrite> mQueue(
    1024,  // Queue depth
    true); // Event flag wait

// Producer (HAL)
mQueue.write(&event, 1);

// Consumer (Framework)  
mQueue.read(&event, 1);
```
FMQ avoids Binder overhead entirely for high-frequency data (sensor readings, audio samples).

### Solution 3: AIDL MemoryFileDescriptor / MappedByteBuffer
```java
// Java: Pass SharedMemory via AIDL
SharedMemory shm = SharedMemory.create("vehicle_data", 4096);
ByteBuffer buf = shm.mapReadWrite();
buf.putFloat(speed);
// Pass ParcelFileDescriptor of shm via AIDL call
```

---

## SECTION 16: AAOS VEHICLE HAL — BINDER IN ACTION

### 16.1 Architecture
```
App (CarPropertyManager.getProperty(SPEED))
    ↓ Binder (ICarProperty.aidl)
CarService (CarPropertyService)
    ↓ Binder (IVehicle.aidl - AIDL HAL)
Vehicle HAL Implementation (vendor process)
    ↓ Hardware access
CAN Bus / Ethernet (vehicle signals)
```

### 16.2 Vehicle HAL AIDL Interface (Android 13+)
```aidl
// android.hardware.automotive.vehicle
@VintfStability
interface IVehicle {
    VehiclePropConfigs getAllPropConfigs();
    void getValues(IVehicleCallback callback, in GetValueRequests requests);
    void setValues(IVehicleCallback callback, in SetValueRequests requests);
    void subscribe(IVehicleCallback callback, in SubscribeOptions[] options, int maxSharedMemoryFileCount);
    void unsubscribe(IVehicleCallback callback, in int[] propIds);
    void returnSharedMemory(IVehicleCallback callback, long sharedMemoryId);
}
```

### 16.3 Property Subscription Model
```
                  subscribe(SPEED, 10Hz)
App ──→ CarService ──────────────────────→ Vehicle HAL
                                                │
        ← onPropertyEvent(SPEED=60.0) ─────────┘ (callback via Binder)
        ← onPropertyEvent(SPEED=61.2) ─────────┘
        ← onPropertyEvent(SPEED=62.5) ─────────┘
```

Properties use **oneway callbacks** — HAL fires events asynchronously to subscribed clients.

### 16.4 Vehicle Property IDs
```cpp
// VehicleProperty.aidl (partial)
enum VehicleProperty {
    PERF_VEHICLE_SPEED = 0x0207_0200,    // float, m/s
    GEAR_SELECTION     = 0x0400_0400,    // int32
    HVAC_TEMPERATURE_SET = 0x0503_0500,  // float, celsius
    TURN_SIGNAL_STATE  = 0x0408_0400,    // int32 (enum)
    EV_BATTERY_LEVEL   = 0x060A_0600,    // float, percentage
    // Property ID encoding:
    // [property_group][vehicle_area_type][data_type]
}
```

---

## SECTION 17: BINDER IN NATIVE C++ (libbinder)

### 17.1 Server Implementation
```cpp
#include <binder/IServiceManager.h>
#include <binder/ProcessState.h>
#include <binder/IPCThreadState.h>

// Implement the interface
class MyService : public BnMyInterface {
public:
    binder::Status doSomething(int32_t input, int32_t* output) override {
        *output = input * 2;
        return binder::Status::ok();
    }
};

int main() {
    // Initialize process state (opens /dev/binder, mmap)
    sp<ProcessState> ps = ProcessState::self();
    ps->setThreadPoolMaxThreadCount(4);
    
    // Register service
    sp<MyService> service = new MyService();
    defaultServiceManager()->addService(String16("my.service"), service);
    
    // Start thread pool and join
    ps->startThreadPool();
    IPCThreadState::self()->joinThreadPool();
    return 0;
}
```

### 17.2 Client Implementation
```cpp
#include <binder/IServiceManager.h>

sp<IBinder> binder = defaultServiceManager()->getService(String16("my.service"));
if (binder == nullptr) {
    ALOGE("Service not found!");
    return -1;
}

sp<IMyInterface> service = interface_cast<IMyInterface>(binder);
int32_t result;
binder::Status status = service->doSomething(42, &result);
if (status.isOk()) {
    ALOGI("Result: %d", result);  // 84
}
```

---

## SECTION 18: BINDER IN JAVA (android.os)

### 18.1 AIDL Service (Java)
```java
// IMyService.aidl
interface IMyService {
    int computeSquare(int value);
    oneway void registerCallback(IMyCallback callback);
}

// MyServiceImpl.java (Server)
public class MyServiceImpl extends IMyService.Stub {
    @Override
    public int computeSquare(int value) {
        // Binder.getCallingUid() available here
        return value * value;
    }
    
    @Override
    public void registerCallback(IMyCallback callback) {
        mCallbacks.register(callback);
        // Use callback.linkToDeath() to detect client death
    }
}

// Registration (in system_server or standalone service)
ServiceManager.addService("my.service", new MyServiceImpl());
```

### 18.2 Client (Java)
```java
IBinder binder = ServiceManager.getService("my.service");
IMyService service = IMyService.Stub.asInterface(binder);
int result = service.computeSquare(5);  // Returns 25
// This is a cross-process call! Transparent to caller.
```

### 18.3 The Proxy/Stub Pattern
```
IMyService (Interface)
    ├── IMyService.Stub (Server side - abstract, extends Binder)
    │       └── MyServiceImpl (concrete implementation)
    └── IMyService.Stub.Proxy (Client side - auto-generated)
            └── Serializes calls → Binder transactions
```

---

## SECTION 19: BINDER DEBUGGING & TRACING

### 19.1 Key Debug Commands
```bash
# List all registered services
adb shell service list
adb shell dumpsys -l

# Dump specific service state
adb shell dumpsys activity
adb shell dumpsys car_service

# Binder process info
adb shell cat /proc/binder/state          # Global binder state
adb shell cat /proc/binder/stats          # Transaction statistics
adb shell cat /proc/binder/transactions   # Active transactions
adb shell cat /proc/binder/proc/<PID>     # Per-process binder info

# Find binder threads
adb shell cat /proc/<PID>/task/*/comm | grep -c Binder

# Service call (raw)
adb shell service call phone 1  # Call method 1 on "phone" service
```

### 19.2 Systrace / Perfetto
```bash
# Capture binder transactions in trace
adb shell perfetto -o /data/misc/perfetto-traces/trace.pb -t 10s \
  -c - <<EOF
buffers: { size_kb: 32768 }
data_sources: {
  config {
    name: "linux.ftrace"
    ftrace_config {
      ftrace_events: "binder/binder_transaction"
      ftrace_events: "binder/binder_transaction_received"
      ftrace_events: "binder/binder_lock"
      ftrace_events: "binder/binder_locked"
    }
  }
}
EOF
```

### 19.3 Binder Transaction Logging
```bash
# Enable binder transaction logging
echo 1 > /sys/module/binder/parameters/debug_mask

# Watch dmesg for binder debug
dmesg | grep binder
```

---

## SECTION 20: PERFORMANCE CHARACTERISTICS

### 20.1 Latency Benchmarks (typical)
| Operation | Latency |
|-----------|---------|
| Empty transaction (no data) | ~5 µs |
| Small transaction (64 bytes) | ~10-20 µs |
| Medium transaction (4 KB) | ~30-50 µs |
| Large transaction (64 KB) | ~100-200 µs |
| Max transaction (1 MB) | ~500-1000 µs |
| Service lookup (ServiceManager) | ~100-200 µs |
| Java Binder call (with JNI) | ~50-100 µs |

### 20.2 Throughput
- **Theoretical max**: ~1 GB/s (limited by memory copy bandwidth)
- **Practical max**: ~200-500 MB/s (overhead from ioctl, wake, schedule)
- **Typical system load**: 10,000-100,000 transactions/second on busy phone

### 20.3 Performance Tips
1. **Minimize transaction frequency**: Batch data when possible
2. **Use oneway for notifications**: Don't block caller unnecessarily
3. **Large data via SharedMemory**: Avoid filling 1MB buffer
4. **FMQ for high-frequency sensor data**: Bypass Binder entirely
5. **Avoid holding locks during Binder calls**: Risk of deadlock
6. **Thread pool sizing**: Match to expected concurrency

---

## SECTION 21: COMMON PATTERNS

### 21.1 Callback Pattern (Observer)
```aidl
// Define callback interface
interface IStatusCallback {
    oneway void onStatusChanged(int newStatus);
    oneway void onError(int errorCode, String message);
}

// Main service interface
interface IMyService {
    void registerCallback(IStatusCallback callback);
    void unregisterCallback(IStatusCallback callback);
}
```

### 21.2 Token Pattern (Authorization)
```java
// Server creates token and gives to authorized client
IBinder token = new Binder();  // Unique unforgeable token
giveTokenToClient(token);

// Client presents token in subsequent calls
service.doPrivilegedAction(token, args);

// Server verifies token identity
void doPrivilegedAction(IBinder token, ...) {
    if (token != mAuthorizedToken) throw SecurityException();
    // Proceed...
}
```

### 21.3 Proxy Forwarding
```
App → SystemService → HAL

SystemService holds proxy to HAL.
When app calls SystemService, SystemService calls HAL on behalf of app.
App never directly contacts HAL (security boundary).
```

### 21.4 Death Notification + Reconnect
```java
class ServiceConnection implements IBinder.DeathRecipient {
    private IMyService mService;
    
    void connect() {
        IBinder b = ServiceManager.getService("my.svc");
        b.linkToDeath(this, 0);
        mService = IMyService.Stub.asInterface(b);
    }
    
    @Override
    public void binderDied() {
        mService = null;
        // Reconnect after delay
        handler.postDelayed(this::connect, 1000);
    }
}
```

---

## SECTION 22: AIDL FOR HALs (MODERN ANDROID)

### 22.1 Migration from HIDL to AIDL
```
Android 8-10: All HALs = HIDL (hwbinder)
Android 11:   New HALs can be AIDL
Android 13:   New HALs MUST be AIDL; HIDL deprecated
Android 14+:  Existing HIDL HALs being migrated to AIDL
```

### 22.2 AIDL HAL vs Framework AIDL
| Aspect | Framework AIDL | HAL AIDL |
|--------|---------------|----------|
| Device node | /dev/binder | /dev/binder (registered with ServiceManager) |
| Stability | @SystemApi or internal | @VintfStability (frozen ABI) |
| Manifest | Not in VINTF | Declared in device manifest (VINTF) |
| Versioning | Tied to SDK | Independent versioning + frozen interfaces |
| Location | frameworks/base/ | hardware/interfaces/ |

### 22.3 VINTF Manifest Entry
```xml
<!-- device manifest (vendor) -->
<manifest version="2.0" type="device">
    <hal format="aidl">
        <name>android.hardware.automotive.vehicle</name>
        <version>3</version>
        <fqname>IVehicle/default</fqname>
    </hal>
</manifest>
```

### 22.4 HAL Service Registration (C++)
```cpp
#include <android/binder_manager.h>
#include <android/binder_process.h>

int main() {
    ABinderProcess_setThreadPoolMaxThreadCount(4);
    
    std::shared_ptr<IVehicle> vehicle = ndk::SharedRefBase::make<VehicleHal>();
    
    const std::string instance = std::string() + IVehicle::descriptor + "/default";
    binder_status_t status = AServiceManager_addService(
        vehicle->asBinder().get(), instance.c_str());
    
    ABinderProcess_joinThreadPool();
    return EXIT_FAILURE;  // Should never reach here
}
```

---

## SECTION 23: BINDER CONTEXT MANAGER (SERVICEMANAGER INTERNALS)

### Process of ServiceManager:
```
1. Opens /dev/binder
2. Calls ioctl(BINDER_SET_CONTEXT_MGR) → becomes handle 0
3. Enters loop: reads transactions (addService/getService/listServices)
4. Maintains map: service_name → IBinder handle
5. Permission checks: SELinux policy for who can add/get each service
```

### ServiceManager is special because:
- Every process knows its handle (0) without lookup
- First process to claim context manager wins
- Kernel treats handle 0 transactions specially
- Controls access to all other services (gatekeeper)

---

## SECTION 24: BINDER LIMITATIONS & PITFALLS

| Limitation | Impact | Mitigation |
|-----------|--------|-----------|
| 1 MB buffer per process | Large data fails | SharedMemory / FMQ |
| 16 thread pool default | Starvation under load | Increase pool or use oneway |
| Synchronous blocking | Deadlock if circular calls | Use oneway, avoid cycles |
| No multicast | Can't send to multiple targets atomically | Loop + send individually |
| Java overhead | JNI crossings add latency | NDK AIDL for perf-critical |
| Transaction ordering | Oneway delivered in order per-object | Design for async |
| SELinux complexity | Permission denied at runtime | Test early, check policy |
| Process death = all objects dead | Client must handle | linkToDeath + reconnect |

---

## SECTION 25: BINDER INTERNALS — MEMORY MANAGEMENT

### Buffer Allocation:
```
Process mmap(1MB) at init:
┌──────────────────────────────────────────────────────┐
│ 1 MB Binder Buffer Space (shared kernel ↔ userspace) │
├──────┬──────┬────────────────┬──────┬────────────────┤
│ Used │ Used │    Free        │ Used │    Free        │
│ Txn1 │ Txn2 │                │ Txn3 │                │
└──────┴──────┴────────────────┴──────┴────────────────┘

Each transaction occupies a chunk:
  - Header (binder_buffer struct)
  - Data (Parcel contents)
  - Offsets (flat_binder_object positions)

When BC_FREE_BUFFER received → chunk returned to free list
If all 1MB used → new transactions FAIL (TransactionTooLargeException)
```

### TransactionTooLargeException:
```java
// Thrown when:
// 1. Single transaction exceeds remaining buffer space
// 2. Too many pending async transactions fill the buffer
// 3. Process has leaked buffers (not calling freeBuffer)

// Diagnosis:
adb shell cat /proc/binder/proc/<PID>  // Check buffer usage
// Look for: "free async space" and "buffer_size"
```

---

END OF DOCUMENT 01 — MASTER THEORY
