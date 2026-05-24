# ANDROID BINDER & AIDL — DIAGRAMS & VISUAL REFERENCE
# ════════════════════════════════════════════════════════════════════
# Protocol: Android Binder IPC | Document: 02 of 08
# ════════════════════════════════════════════════════════════════════

---

## DIAGRAM 01: Binder High-Level Architecture

```mermaid
graph TD
    subgraph "User Space"
        A["App Process<br/>(Client)"]
        B["System Server<br/>(ActivityManager, etc.)"]
        C["HAL Process<br/>(Vehicle HAL)"]
        SM["ServiceManager<br/>(Handle 0)"]
    end
    
    subgraph "Kernel Space"
        BD["/dev/binder<br/>Binder Driver"]
    end
    
    A -->|"ioctl()"| BD
    B -->|"ioctl()"| BD
    C -->|"ioctl()"| BD
    SM -->|"ioctl()"| BD
    BD -->|"Transaction routing"| BD
    
    style BD fill:#ff9,stroke:#333,stroke-width:2px
    style SM fill:#9f9,stroke:#333
```

---

## DIAGRAM 02: Three Binder Domains (Android 8.0+)

```
┌─────────────────────────────────────────────────────────────────┐
│                     ANDROID PROCESS SPACE                         │
├─────────────────┬─────────────────────┬─────────────────────────┤
│                 │                     │                           │
│  /dev/binder    │  /dev/hwbinder      │  /dev/vndbinder          │
│                 │                     │                           │
│  ┌───────────┐  │  ┌───────────────┐  │  ┌───────────────────┐  │
│  │ Apps      │  │  │ System Svcs   │  │  │ Vendor Process A  │  │
│  │ System    │  │  │ (Clients)     │  │  │ Vendor Process B  │  │
│  │ Services  │  │  │               │  │  │                   │  │
│  │           │  │  │ HAL Servers   │  │  │                   │  │
│  └───────────┘  │  └───────────────┘  │  └───────────────────┘  │
│                 │                     │                           │
│ servicemanager  │ hwservicemanager    │ vndservicemanager        │
│                 │ (or servicemanager  │                           │
│                 │  for AIDL HALs)     │                           │
├─────────────────┴─────────────────────┴─────────────────────────┤
│  SELinux enforces separation — no cross-domain access            │
└─────────────────────────────────────────────────────────────────┘
```

---

## DIAGRAM 03: Transaction Flow (Synchronous)

```mermaid
sequenceDiagram
    participant C as Client<br/>(Process A)
    participant K as Kernel<br/>(Binder Driver)
    participant S as Server<br/>(Process B)
    
    C->>C: Serialize args into Parcel
    C->>K: ioctl(BINDER_WRITE_READ)<br/>BC_TRANSACTION [handle, code, data]
    Note over C: Client thread SLEEPS
    
    K->>K: Validate UID/PID
    K->>K: Translate handle → binder_node
    K->>K: Alloc buffer in server's mmap
    K->>K: Copy data (ONE COPY)
    K->>S: Wake server thread<br/>BR_TRANSACTION [code, data, caller_uid/pid]
    
    S->>S: onTransact(code, data, reply)
    S->>S: Execute business logic
    S->>S: Write result into reply Parcel
    S->>K: ioctl() BC_REPLY [reply_data]
    
    K->>K: Copy reply to client
    K->>C: Wake client<br/>BR_REPLY [reply_data]
    Note over C: Client thread WAKES
    C->>C: Deserialize reply, return
```

---

## DIAGRAM 04: One-Copy Memory Mapping

```
CLIENT (Process A)                         SERVER (Process B)
┌─────────────────────┐                   ┌─────────────────────┐
│ User Virtual Memory │                   │ User Virtual Memory │
│                     │                   │                     │
│ Parcel data (src)   │                   │ mmap'd binder region│
│ [xxxxxxxxxx]        │                   │ [          ]        │
└────────┬────────────┘                   └────────┬────────────┘
         │                                         │ (same physical pages!)
         │ copy_from_user()                        │ (no copy needed!)
         ▼                                         │
┌─────────────────────────────────────────────────────────────────┐
│                        KERNEL SPACE                               │
│                                                                  │
│  binder_buffer (allocated in mmap'd physical pages)             │
│  [xxxxxxxxxx]  ← data copied here from client                  │
│                   server reads directly from its mmap view →    │
└─────────────────────────────────────────────────────────────────┘

Traditional IPC: Client → Kernel → Server = 2 copies
Binder:          Client → Kernel/Server shared pages = 1 copy
```

---

## DIAGRAM 05: ServiceManager Lookup Flow

```mermaid
sequenceDiagram
    participant S as Server
    participant SM as ServiceManager<br/>(handle 0)
    participant C as Client
    
    Note over S: Server starts up
    S->>SM: addService("vehicle_hal", myBinder)
    SM->>SM: Store: "vehicle_hal" → handle
    SM->>S: OK
    
    Note over C: Client wants service
    C->>SM: getService("vehicle_hal")
    SM->>SM: Lookup: "vehicle_hal" → handle
    SM->>C: Return IBinder proxy (handle)
    
    Note over C,S: Direct communication (bypasses SM)
    C->>S: transact(METHOD_ID, parcel)
    S->>C: reply(result)
```

---

## DIAGRAM 06: Proxy/Stub Pattern

```
┌──────────────────────────────────────────────────────────────────┐
│                                                                   │
│  CLIENT PROCESS                       SERVER PROCESS             │
│  ──────────────                       ──────────────             │
│                                                                   │
│  ┌─────────────────┐                ┌─────────────────────┐     │
│  │  Application    │                │  Implementation      │     │
│  │  Code           │                │  (Business Logic)    │     │
│  │                 │                │                      │     │
│  │  service.foo()  │                │  int foo() {         │     │
│  │       │         │                │    return 42;        │     │
│  └───────┼─────────┘                │  }                   │     │
│          ▼                           └──────────┬───────────┘     │
│  ┌─────────────────┐                           │                 │
│  │  PROXY (Bp)     │                ┌──────────▼───────────┐     │
│  │  (Auto-gen)     │                │  STUB (Bn)           │     │
│  │                 │                │  (Auto-gen)          │     │
│  │  foo() {        │                │                      │     │
│  │    parcel.write │   Binder IPC   │  onTransact(code) {  │     │
│  │    transact()───┼────────────────┼──→  parcel.read()    │     │
│  │    return read()│◄───────────────┼──  impl.foo()        │     │
│  │  }              │                │    reply.write(42)    │     │
│  └─────────────────┘                │  }                   │     │
│                                      └─────────────────────┘     │
└──────────────────────────────────────────────────────────────────┘
```

---

## DIAGRAM 07: AIDL Compilation Pipeline

```mermaid
flowchart LR
    A["IMyService.aidl<br/>(Interface definition)"]
    
    A --> B["aidl compiler"]
    
    B --> C["Java Backend<br/>IMyService.java<br/>Stub + Proxy"]
    B --> D["C++ Backend<br/>IMyService.h<br/>BpMyService.cpp<br/>BnMyService.cpp"]
    B --> E["NDK Backend<br/>IMyService.h<br/>(ndk:: namespace)"]
    B --> F["Rust Backend<br/>IMyService.rs<br/>(binder crate)"]
    
    style A fill:#ffd
    style B fill:#f9f
```

---

## DIAGRAM 08: AAOS Vehicle HAL Binder Path

```
┌─────────────────────────────────────────────────────────────────┐
│                        APP LAYER                                  │
│  ┌────────────────────────────────────────────────────────┐     │
│  │  Car App (Java)                                        │     │
│  │  CarPropertyManager.getIntProperty(SPEED, GLOBAL)      │     │
│  └──────────────────────────┬─────────────────────────────┘     │
│                              │ AIDL Binder (ICarProperty)        │
│  ┌──────────────────────────▼─────────────────────────────┐     │
│  │  CarService (system_server/CarServiceHelper)           │     │
│  │  CarPropertyService.getProperty()                      │     │
│  └──────────────────────────┬─────────────────────────────┘     │
│                              │ AIDL Binder (IVehicle)            │
│  ┌──────────────────────────▼─────────────────────────────┐     │
│  │  Vehicle HAL (vendor process)                          │     │
│  │  VehicleHal::get(SPEED) → read from CAN/SOME-IP       │     │
│  └──────────────────────────┬─────────────────────────────┘     │
│                              │ CAN/Ethernet/SOME-IP              │
│  ┌──────────────────────────▼─────────────────────────────┐     │
│  │  Vehicle ECU Network (speed sensor → CAN → gateway)    │     │
│  └────────────────────────────────────────────────────────┘     │
└─────────────────────────────────────────────────────────────────┘
```

---

## DIAGRAM 09: Death Notification Mechanism

```mermaid
sequenceDiagram
    participant C as Client
    participant K as Binder Driver
    participant S as Server
    
    C->>K: BC_REQUEST_DEATH_NOTIFICATION<br/>(for server's binder_node)
    K->>K: Store death callback in binder_ref
    
    Note over S: Server process CRASHES
    S-xK: Process exit/kill
    
    K->>K: Iterate dying process's binder_nodes
    K->>K: Find all refs with death notifications
    K->>C: BR_DEAD_BINDER (server died!)
    
    C->>C: DeathRecipient.binderDied()
    C->>C: Cleanup / Reconnect
    C->>K: BC_DEAD_BINDER_DONE (acknowledged)
```

---

## DIAGRAM 10: Parcel Data Layout

```
Parcel (serialized transaction data):

Byte offset:  0        4        8       12      16        20
              ┌────────┬────────┬────────┬───────┬─────────┬─────────────┐
Data buffer:  │ int32  │ int32  │str_len │ str16 data...   │flat_binder  │
              │ (arg1) │ (arg2) │  (4)   │ "Test"          │  _object    │
              └────────┴────────┴────────┴───────┴─────────┴──────┬──────┘
                                                                   │
Objects array (offsets to flat_binder_objects):                    │
              ┌────────┐                                          │
              │ off=20 │ ─────────────────────────────────────────┘
              └────────┘

flat_binder_object at offset 20:
┌──────────┬──────────┬──────────────────┬──────────┐
│ hdr_type │  flags   │  binder/handle   │  cookie  │
│ (4B)     │  (4B)    │     (8B)         │  (8B)    │
└──────────┴──────────┴──────────────────┴──────────┘

Types: BINDER_TYPE_BINDER (own object), BINDER_TYPE_HANDLE (reference),
       BINDER_TYPE_FD (file descriptor)
```

---

## DIAGRAM 11: Thread Pool Management

```
SERVER PROCESS
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  Main Thread ─── joinThreadPool() → handles transactions   │
│                                                             │
│  ┌─────────── Binder Thread Pool (max 15+1) ───────────┐  │
│  │                                                       │  │
│  │  Thread 1: [WAITING]  ← idle, ready for work        │  │
│  │  Thread 2: [BUSY]     ← handling transaction         │  │
│  │  Thread 3: [BUSY]     ← handling transaction         │  │
│  │  Thread 4: [WAITING]  ← idle                         │  │
│  │  ...                                                  │  │
│  │  Thread N: [WAITING]                                  │  │
│  │                                                       │  │
│  └───────────────────────────────────────────────────────┘  │
│                                                             │
│  When all threads busy AND pool not full:                   │
│    Kernel sends BR_SPAWN_LOOPER → process creates new thread│
│                                                             │
└─────────────────────────────────────────────────────────────┘

Transaction arrives → kernel picks idle thread → wakes it
If no idle thread → queued (or new thread spawned)
```

---

## DIAGRAM 12: HIDL Passthrough vs Binderized

```
BINDERIZED MODE (recommended):
┌───────────────┐         ┌───────────────────┐
│ Framework     │ hwbinder │ HAL Process       │
│ (system svc)  │ ───────→ │ (separate process)│
│               │ IPC call │                   │
└───────────────┘         └───────────────────┘
  Process isolation ✓   SELinux separation ✓   Stability ✓


PASSTHROUGH MODE (legacy/performance):
┌──────────────────────────────────────────┐
│ Framework Process                         │
│                                          │
│  Framework code                          │
│       │                                  │
│       ▼ dlopen("vendor_hal.so")         │
│  ┌────────────────────────────────────┐  │
│  │ HAL Implementation (.so loaded)    │  │
│  │ (runs IN framework's process!)     │  │
│  └────────────────────────────────────┘  │
│                                          │
└──────────────────────────────────────────┘
  No IPC overhead ✓   No isolation ✗   Legacy only
```

---

## DIAGRAM 13: Reference Counting

```
                    binder_node (in Server's proc)
                    ┌─────────────────────────────┐
                    │ strong_refs = 2              │
                    │ weak_refs = 3               │
                    │ ptr = 0xABCD (BBinder*)      │
                    └───────────┬─────────────────┘
                                │
              ┌─────────────────┼─────────────────┐
              │                 │                   │
              ▼                 ▼                   ▼
    binder_ref (Client A)   binder_ref (Client B)  binder_ref (Client C)
    ┌───────────────┐       ┌───────────────┐     ┌───────────────┐
    │ handle = 1    │       │ handle = 3    │     │ handle = 2    │
    │ strong = 1    │       │ strong = 1    │     │ strong = 0    │
    │ weak = 1      │       │ weak = 1      │     │ weak = 1      │
    └───────────────┘       └───────────────┘     └───────────────┘
    (sp<IBinder>)           (sp<IBinder>)         (wp<IBinder> only)

When Client A calls BC_RELEASE → strong_refs drops to 1
When Client B calls BC_RELEASE → strong_refs = 0 → node can be destroyed
Client C's weak ref doesn't prevent destruction
```

---

## DIAGRAM 14: Binder Security Layers

```mermaid
graph TD
    A["Binder Transaction"] --> B{"SELinux Check"}
    B -->|Denied| X["PERMISSION_DENIED<br/>(avc: denied)"]
    B -->|Allowed| C{"Service-level<br/>Permission Check"}
    C -->|"enforceCallingPermission()"| D{"Has Permission?"}
    D -->|No| Y["SecurityException"]
    D -->|Yes| E{"UID/PID<br/>Authorization"}
    E -->|Unauthorized UID| Z["PERMISSION_DENIED"]
    E -->|Authorized| F["✅ Execute Method"]
    
    style X fill:#f99
    style Y fill:#f99
    style Z fill:#f99
    style F fill:#9f9
```

---

## DIAGRAM 15: Oneway (Async) vs Two-Way Transaction

```
TWO-WAY (default):
Client          Kernel          Server
  │                               │
  │──── BC_TRANSACTION ──────────→│
  │     (client BLOCKS)           │
  │                               │── onTransact()
  │                               │── prepare reply
  │←──── BR_REPLY ───────────────│
  │     (client WAKES)            │
  │                               │
  Time: Client blocked for entire server processing time


ONEWAY (async):
Client          Kernel          Server
  │                               │
  │──── BC_TRANSACTION ──────────→│ (queued)
  │←── BR_TRANSACTION_COMPLETE    │
  │     (client continues!)       │
  │                               │── onTransact() (later)
  │     ... does other work ...   │
  │                               │
  Time: Client returns immediately, server processes whenever
  
  ⚠️ No return value possible with oneway!
  ⚠️ Multiple oneways to same target processed IN ORDER
```

---

## DIAGRAM 16: File Descriptor Passing

```
SENDER (Process A)                    RECEIVER (Process B)
┌──────────────────┐                 ┌──────────────────┐
│ FD table:        │                 │ FD table:        │
│  fd=5 → file*X  │                 │  fd=3 → (empty)  │
│                  │                 │  fd=7 → file*Y   │
└────────┬─────────┘                 └──────────────────┘
         │ parcel.writeFileDescriptor(5)
         ▼
┌────────────────────────────────────────────────────────┐
│                  BINDER KERNEL                          │
│                                                        │
│  1. Read fd=5 from sender → get struct file* X        │
│  2. Allocate new fd=8 in receiver's table             │
│  3. Point receiver's fd=8 → same struct file* X      │
│  4. Replace "5" with "8" in parcel data              │
│                                                        │
└────────────────────────────────────────────────────────┘
         │
         ▼
┌──────────────────┐
│ RECEIVER:        │
│ FD table:        │
│  fd=3 → (empty)  │
│  fd=7 → file*Y   │
│  fd=8 → file*X ← │ NEW! Same underlying file as sender's fd=5
└──────────────────┘

Both processes now share access to the same open file/device/socket!
```

---

## DIAGRAM 17: Fast Message Queue (FMQ) - Bypassing Binder

```
┌───────────────────────────────────────────────────────────────┐
│ SHARED MEMORY (allocated once via Binder, then used directly) │
│                                                               │
│  ┌─────────────────────────────────────────────────────────┐ │
│  │          RING BUFFER (lock-free, single pair)           │ │
│  │                                                         │ │
│  │  write_ptr ──→  [D][D][D][D][ ][ ][ ][ ]  ←── read_ptr│ │
│  │                  ↑ new data        ↑ consumed          │ │
│  │                                                         │ │
│  │  Atomics: write_idx, read_idx (no locks needed!)       │ │
│  └─────────────────────────────────────────────────────────┘ │
│                                                               │
│  Event Flag (futex): signals new data available              │
└───────────────────────────────────────────────────────────────┘

PRODUCER (HAL)                        CONSUMER (Framework)
  mQueue.write(&event);                mQueue.read(&event);
  // Just memory write + atomic!       // Just memory read + atomic!
  // NO ioctl, NO kernel, NO Binder!   // ZERO system call overhead!
  
Use case: Sensor data at 400Hz → FMQ is MUCH faster than Binder
```

---

## DIAGRAM 18: Android System Service Binder Topology

```mermaid
graph TD
    subgraph "system_server (PID ~1000)"
        AMS["ActivityManagerService"]
        WMS["WindowManagerService"]
        PMS["PackageManagerService"]
        CS["CarService (AAOS)"]
    end
    
    subgraph "Apps"
        APP1["App 1"]
        APP2["App 2"]
    end
    
    subgraph "Native Services"
        SF["SurfaceFlinger"]
        AF["AudioFlinger"]
        MM["MediaServer"]
    end
    
    subgraph "HAL Processes"
        VHAL["Vehicle HAL"]
        AHAL["Audio HAL"]
        CHAL["Camera HAL"]
    end
    
    SM["ServiceManager"]
    
    APP1 -.->|"getService()"| SM
    SM -.->|"handle"| APP1
    APP1 -->|"Binder"| AMS
    APP1 -->|"Binder"| CS
    APP2 -->|"Binder"| WMS
    
    CS -->|"HW Binder"| VHAL
    AF -->|"HW Binder"| AHAL
    SF -->|"HW Binder"| CHAL
    
    style SM fill:#9f9
```

---

## DIAGRAM 19: Binder Transaction Buffer Anatomy

```
Per-Process Binder Buffer (default 1MB, mmap'd):

Address:  0x00000    0x10000     0x20000    ...    0xFFFFF
          ┌──────────┬───────────┬──────────┬─────┬──────┐
          │ Txn Buf 1│ Txn Buf 2 │ Txn Buf 3│ ... │ Free │
          │ (4KB)    │ (256B)    │ (1KB)    │     │      │
          └──────────┴───────────┴──────────┴─────┴──────┘
          
Each transaction buffer:
┌────────────────────────────────────────────────┐
│ binder_buffer header (kernel metadata)         │
│   - size, offsets_size                         │
│   - target_node pointer                        │
│   - transaction pointer                        │
├────────────────────────────────────────────────┤
│ Data area (Parcel bytes)                       │
│   [serialized arguments / return values]       │
├────────────────────────────────────────────────┤
│ Offsets area                                   │
│   [positions of flat_binder_objects]           │
└────────────────────────────────────────────────┘

Server reads from this buffer directly (zero additional copy).
After processing: BC_FREE_BUFFER returns space to free list.

⚠️ If server is slow / doesn't free buffers → 1MB fills up
   → TransactionTooLargeException for new incoming transactions!
```

---

## DIAGRAM 20: AIDL HAL Registration & Discovery

```mermaid
sequenceDiagram
    participant HAL as Vehicle HAL<br/>(vendor process)
    participant SM as ServiceManager
    participant FW as CarService<br/>(framework)
    participant VINTF as VINTF Manifest
    
    Note over HAL: Process starts at boot
    HAL->>SM: AServiceManager_addService(<br/>"android.hardware.automotive.vehicle.IVehicle/default")
    SM->>SM: Store service
    
    Note over FW: Framework needs HAL
    FW->>VINTF: Check: Is IVehicle declared?
    VINTF->>FW: Yes (version 3, instance "default")
    FW->>SM: getService("...IVehicle/default")
    SM->>FW: Return IBinder proxy
    
    Note over FW,HAL: Direct Binder communication
    FW->>HAL: getValues(callback, requests)
    HAL->>FW: callback.onGetValues(results)
```

---

END OF DOCUMENT 02 — DIAGRAMS
