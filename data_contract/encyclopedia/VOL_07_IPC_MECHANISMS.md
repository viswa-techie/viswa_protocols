# VOLUME 07 — IPC MECHANISMS

## Universal Data Contract, Serialization, IDL & Interoperability Architecture Encyclopedia

---

## 07.0 — What is IPC (Formal Definition)

**Definition:** Inter-Process Communication (IPC) is the set of mechanisms provided by an operating system kernel to allow processes on the SAME machine to exchange data and synchronize across process-boundary protection domains.

**Stack Position:**
```
    Schema / Serialization (Volume 03/04)
           ↓
    ┌───────────────────────────────────┐
    │        IPC MECHANISMS             │  ← THIS VOLUME
    │  (kernel-provided, same-machine)  │
    └───────────────────────────────────┘
           ↓
    OS Kernel / Hardware
```

**IPC vs RPC vs Messaging:**

| Dimension | IPC | RPC (Volume 06) | Messaging |
|-----------|-----|-----------------|-----------|
| Scope | Same machine only | Same or remote machine | Same or remote |
| Provider | OS kernel | Application framework | Middleware/broker |
| Examples | Binder, pipes, shared memory, Mach ports | gRPC, Thrift, SOAP | Kafka, RabbitMQ, MQTT |
| Relationship | IPC is substrate for RPC | RPC uses IPC as local transport | Async, decoupled |
| Performance | Highest (kernel-optimized) | Moderate (framework overhead) | Lower (broker hop) |

**Key Principle:** IPC is a kernel feature. The kernel manages process address space isolation, data copying (or mapping) across the boundary, synchronization, and security credential injection.

---

## 07.1 — Copy Count Analysis (Why It Matters)

The number of times data is copied between address spaces is the primary performance differentiator for IPC mechanisms:

| Copy Count | Mechanism | Performance Characteristic |
|-----------|-----------|---------------------------|
| **0 (zero-copy)** | Shared memory (mmap, Iceoryx) | Highest throughput; requires external synchronization |
| **1 (single-copy)** | Android Binder (mmap optimization) | Good throughput; kernel manages synchronization |
| **COW (copy-on-write)** | Mach IPC out-of-line | Zero-copy until write; lazy allocation |
| **2 (two-copy)** | Pipes, sockets, message queues | Standard; kernel intermediates all data |

### Data Flow for Each Pattern

**Two-Copy (Pipes, Sockets):**
```
Process A          Kernel             Process B
[user buf] ──copy_from_user──► [kernel buf] ──copy_to_user──► [user buf]
```

**One-Copy (Binder):**
```
Process A          Kernel                 Process B
[user buf] ──copy_from_user──► [binder buffer] ──(mmap)──► [mapped region]
                                  ↑                            ↑
                            single copy              B reads directly
                                                   from mmap'd region
```

**Zero-Copy (Shared Memory):**
```
Process A                        Process B
[mapped page] ←──── same physical memory ────► [mapped page]
     ↑                                              ↑
  both see same data immediately
  (synchronization via mutex/futex/semaphore)
```

---

## 07.2 — Linux IPC Mechanisms

### 07.2.1 — Pipes (Anonymous)

| Property | Value |
|----------|-------|
| Syscalls | `pipe(2)`, `pipe2(2)` |
| Direction | Unidirectional |
| Relationship | Parent/child only (shared file descriptors via fork) |
| Capacity | 64KB (default, tunable via `/proc/sys/fs/pipe-max-size`) |
| Copy count | 2 |
| Blocking | write() blocks when full; read() blocks when empty |

### 07.2.2 — Named Pipes (FIFOs)

| Property | Value |
|----------|-------|
| Syscalls | `mkfifo(3)`, `open(2)`, `read(2)`, `write(2)` |
| Direction | Unidirectional |
| Relationship | Any processes (identified by filesystem path) |
| Copy count | 2 |

### 07.2.3 — Unix Domain Sockets (UDS)

| Property | Value |
|----------|-------|
| Syscalls | `socket(AF_UNIX)`, `bind`, `connect`, `send`/`recv`, `sendmsg`/`recvmsg` |
| Types | `SOCK_STREAM` (byte stream), `SOCK_DGRAM` (datagram), `SOCK_SEQPACKET` (message boundary) |
| Address | Filesystem path or abstract namespace (`\0name`) |
| Copy count | 2 |
| Special | `SCM_RIGHTS` (pass file descriptors), `SCM_CREDENTIALS` (pass PID/UID/GID) |
| Throughput | Higher than TCP loopback (no TCP/IP stack overhead) |

**Key users:** D-Bus, systemd, X11/Wayland, Docker daemon, gRPC local transport, PostgreSQL

### 07.2.4 — SysV Message Queues

| Property | Value |
|----------|-------|
| Syscalls | `msgget`, `msgsnd`, `msgrcv`, `msgctl` |
| Model | Typed message queue; receiver selects by message type |
| Max message | `MSGMAX` (default 8192 bytes) |
| Status | Legacy — POSIX message queues preferred |

### 07.2.5 — POSIX Message Queues

| Property | Value |
|----------|-------|
| Syscalls | `mq_open`, `mq_send`, `mq_receive`, `mq_notify`, `mq_close`, `mq_unlink` |
| Model | Named queue (in `/dev/mqueue`), priority-based, notification support |
| Copy count | 2 |
| Use case | Embedded systems, RTOS-like patterns on Linux |

### 07.2.6 — SysV Shared Memory

| Property | Value |
|----------|-------|
| Syscalls | `shmget`, `shmat`, `shmdt`, `shmctl` |
| Copy count | 0 (true zero-copy after mapping) |
| Sync | Separate semaphores (SysV or POSIX) required |
| Status | Legacy — POSIX shared memory preferred |

### 07.2.7 — POSIX Shared Memory (mmap)

| Property | Value |
|----------|-------|
| Syscalls | `shm_open`, `ftruncate`, `mmap`, `munmap`, `shm_unlink` |
| Also | `mmap(MAP_SHARED)` on regular file (persistent shared memory) |
| Copy count | 0 (true zero-copy after mapping) |
| Sync | `pthread_mutex` (with `PTHREAD_PROCESS_SHARED`), POSIX semaphores, futex |
| Use case | Iceoryx, DDS shared memory transport, Android HAL media buffers, graphics |

### 07.2.8 — eventfd / signalfd / timerfd

| Mechanism | Purpose | Use Case |
|-----------|---------|----------|
| `eventfd(2)` | 64-bit counter; write increments, read resets | Event notification, epoll wake-up |
| `signalfd(2)` | Receive signals as fd reads | Signal handling in event loops |
| `timerfd_create(2)` | Timer expiration as fd reads | Deadline scheduling in epoll loops |

### 07.2.9 — io_uring

| Property | Value |
|----------|-------|
| Kernel version | Linux 5.1+ (2019) |
| Model | Shared submission + completion ring buffers between user and kernel |
| Copy count | 0 for ring management; depends on operation for data |
| Key innovation | Eliminates syscall overhead for high-I/O workloads |
| Use case | High-performance servers, database I/O, future IPC substrate |

### 07.2.10 — D-Bus

| Property | Value |
|----------|-------|
| Specification | freedesktop.org D-Bus 1.0 |
| Governance | Tier 8 (Community/freedesktop.org) |
| Transport | Unix Domain Sockets (system bus: `/var/run/dbus/system_bus_socket`) |
| Model | Message bus daemon (`dbus-daemon`); named services, signals, method calls |
| Serialization | D-Bus wire format (binary, type codes per value) |
| Type system | byte, boolean, int16/32/64, uint16/32/64, double, string, object_path, signature, array, struct, variant, dict_entry, unix_fd |
| Max message | 128 MB |

**Key users:** NetworkManager, BlueZ (Bluetooth), polkit, systemd (sd-bus), PulseAudio/PipeWire, GNOME/KDE

### 07.2.11 — Netlink Sockets

| Property | Value |
|----------|-------|
| Purpose | Kernel ↔ user-space communication for system configuration |
| Families | `NETLINK_ROUTE`, `NETLINK_NETFILTER`, `NETLINK_AUDIT`, `NETLINK_KOBJECT_UEVENT`, `NETLINK_GENERIC` |
| Model | Datagram sockets (`AF_NETLINK`); multicast groups for async notifications |
| Use case | iproute2 (`ip`), `ss`, udev, audit framework |

---

## 07.3 — Android Binder

### Overview

| Property | Value |
|----------|-------|
| Origin | BeOS → OpenBinder → Android Binder |
| Kernel interface | `/dev/binder` character device + `binder.ko` kernel module |
| Model | Reference-counted object model with proxy/stub pattern |
| Copy count | **1** (single copy via mmap optimization) |
| Transaction | `ioctl(BINDER_WRITE_READ)` on `/dev/binder` |
| Max transaction | 1 MB (default, `BINDER_VM_SIZE`) |

### How the One-Copy Works

```
Process A (client)             Kernel (binder driver)           Process B (server)
┌─────────────┐               ┌──────────────────┐            ┌─────────────┐
│ user buffer │──copy_from_   │ binder buffer    │            │ mmap region │
│ (Parcel)    │  user()──────►│ (allocated in    │◄───mmap───►│ (same phys  │
│             │               │  B's mmap area)  │            │  pages)     │
└─────────────┘               └──────────────────┘            └─────────────┘
                                                                     ↑
                                                              B reads directly
                                                              from mmap (no copy)
```

### Binder Objects

| Object | Role |
|--------|------|
| `IBinder` | Abstract interface to a remote object |
| `BBinder` | Server-side (Binder Native); receives transactions |
| `BpBinder` | Client-side proxy; sends transactions |
| `Parcel` | Serialization container for transaction data |
| `ServiceManager` | Registry; maps service names → IBinder references |

### Parcel Serialization

| Method | Data Written |
|--------|-------------|
| `writeInt32(val)` | 4 bytes, little-endian |
| `writeInt64(val)` | 8 bytes |
| `writeFloat(val)` | 4 bytes IEEE 754 |
| `writeString16(str)` | length (4B) + UTF-16LE chars + null |
| `writeStrongBinder(obj)` | Flat binder object (kernel resolves to handle) |
| `writeFileDescriptor(fd)` | Flat binder object type FD (kernel transfers) |
| `writeParcelable(obj)` | Calls obj.writeToParcel(parcel, flags) |

### Binder Domains (Android)

| Domain | Device Node | Purpose |
|--------|-------------|---------|
| `/dev/binder` | Framework services (apps ↔ system_server) |
| `/dev/hwbinder` | HAL interfaces (framework ↔ vendor HALs) |
| `/dev/vndbinder` | Vendor-to-vendor IPC |

### Binder Thread Pool

- Default: 15 + 1 main thread per process
- Threads block on `ioctl(BINDER_WRITE_READ)` waiting for transactions
- Thread pool exhaustion → ANR (Application Not Responding)
- Configure via `ProcessState::self()->setThreadPoolMaxThreadCount(N)`

### Security

| Mechanism | Protection |
|-----------|-----------|
| Caller identity | Kernel injects UID/PID into every transaction (unforgeable) |
| SELinux | `binder_call` permission between source/target domains |
| ServiceManager ACL | Controls who can register/lookup services |
| `@VintfStability` | Marks interfaces as stable for vendor boundary |

---

## 07.4 — macOS/iOS: Mach IPC and XPC

### Mach IPC (Ports)

| Property | Value |
|----------|-------|
| Kernel | XNU (Mach microkernel core) |
| Primitive | Mach port = kernel-managed message queue |
| Rights | SEND, SEND_ONCE, RECEIVE (ownership) |
| Syscall | `mach_msg()` — send and receive messages |
| Out-of-line data | Copy-on-write (COW) page mapping |
| Max inline | ~4 MB |

**Port rights are capabilities:** Holding a SEND right to a port grants the ability to communicate with the port's owner. Rights are unforgeable and managed by the kernel.

### XPC (Cross-Process Communication)

| Property | Value |
|----------|-------|
| Layer | Built on Mach IPC; managed by launchd |
| API | C (`libxpc` / `xpc/xpc.h`) and ObjC/Swift (`NSXPCConnection`) |
| Model | Service-oriented; connection-based; typed messages |
| Types | bool, int64, double, string, data, array, dictionary, fd, uuid, connection |
| Use case | Privilege separation, app extensions, sandboxed helpers |

**Privilege Separation Pattern:**
```
┌──────────────┐    XPC Connection    ┌───────────────────┐
│ Main App     │─────────────────────►│ XPC Helper Service│
│ (sandboxed)  │                      │ (elevated privs)  │
│              │◄─────────────────────│                   │
└──────────────┘                      └───────────────────┘
```

---

## 07.5 — Windows: COM, Named Pipes, ALPC

### Windows Named Pipes

| Property | Value |
|----------|-------|
| API | `CreateNamedPipe`, `ConnectNamedPipe`, `ReadFile`, `WriteFile` |
| Namespace | `\\.\pipe\PipeName` (local), `\\server\pipe\PipeName` (remote) |
| Modes | Byte mode, message mode; sync or overlapped (async) |
| Security | `SECURITY_ATTRIBUTES` with DACL |
| Use case | SQL Server, IIS, local RPC |

### COM / DCOM

| Property | Value |
|----------|-------|
| IDL | MIDL (Microsoft IDL, DCE RPC-based) |
| In-process | COM DLL loaded into caller's address space |
| Out-of-process | COM EXE; marshaling via ALPC |
| DCOM | Distributed COM over TCP/RPC |
| Registry | CLSID, ProgID → InProcServer32 or LocalServer32 |
| Use case | DirectX, WMI, Office automation, Shell extensions |

### ALPC (Advanced Local Procedure Call)

| Property | Value |
|----------|-------|
| Model | Kernel-internal IPC (successor to LPC) |
| Users | Windows RPC runtime, COM local activation |
| Public API | None documented (internal kernel interface) |
| Used by | `NtAlpcSendWaitReceivePort` (ntdll, undocumented) |

---

## 07.6 — Fuchsia: Zircon Channels

| Property | Value |
|----------|-------|
| Kernel | Zircon microkernel |
| Primitive | Channel = bidirectional, kernel-managed message pipe |
| Syscalls | `zx_channel_write`, `zx_channel_read`, `zx_channel_create` |
| Message content | Up to 64KB bytes + up to 64 handles |
| Copy count | 1 (copy into kernel, deliver to receiver) |
| Contract | FIDL (Volume 02) defines message structure |
| Security | Capability-based — no ambient authority; handle rights enforced |

### Handle Types

| Handle | Purpose |
|--------|---------|
| Channel | Bidirectional message passing |
| VMO (Virtual Memory Object) | Shared memory |
| Socket | Streaming byte/datagram pipe |
| Port | Async notification queue |
| Event/EventPair | Signaling |
| FIFO | Fixed-size kernel FIFO |
| Timer | Time-based signaling |

---

## 07.7 — Automotive IPC: Iceoryx and SOME/IP

### Iceoryx (Eclipse Foundation)

| Property | Value |
|----------|-------|
| Purpose | Zero-copy shared memory IPC for autonomous driving |
| Model | Publish-subscribe over shared memory pools |
| Daemon | RouDi (Routing and Discovery) manages memory segments |
| Copy count | **0** (true zero-copy in data path) |
| Governance | Eclipse Foundation — Tier 4 |
| Integration | ROS 2 (`rmw_iceoryx`), AUTOSAR Adaptive |

**Data Flow:**
```
Publisher                    Shared Memory Pool               Subscriber
┌──────────┐              ┌──────────────────┐             ┌──────────┐
│ loan()   │──────────── ►│ [sample chunk]   │◄────────────│ take()   │
│ publish()│              │ (in pool, owned  │             │ read     │
│          │              │  by publisher,   │             │ release()│
└──────────┘              │  then subscriber)│             └──────────┘
                          └──────────────────┘
```

No copy occurs. Publisher writes directly into a shared memory chunk; subscriber reads from the same physical memory.

### SOME/IP (Summary — full coverage in Volume 11)

| Property | Value |
|----------|-------|
| Full name | Scalable service-Oriented MiddlewarE over IP |
| Scope | Intra-ECU (IPC-like) and inter-ECU (network) |
| Transport | UDP (events), TCP (methods), SOME/IP-SD (service discovery) |
| Implementation | vsomeip (COVESA), AUTOSAR Classic/Adaptive |
| Governance | AUTOSAR consortium — Tier 3 |

---

## 07.8 — IPC Comparison Matrix

| Mechanism | OS | Copy Count | Bidirectional | Max Size | Typed Contract | Code Gen | Zero-Copy | Primary Use |
|-----------|-----|-----------|---------------|----------|---------------|---------|-----------|-------------|
| **Anonymous Pipe** | Linux/macOS/Win | 2 | No | Stream | No | No | No | Shell pipelines |
| **Named Pipe (FIFO)** | Linux/macOS | 2 | No | Stream | No | No | No | Simple unrelated IPC |
| **Unix Domain Socket** | Linux/macOS | 2 | Yes | Unlimited | No | No | No | D-Bus, gRPC local, Docker |
| **POSIX Shared Mem** | Linux/macOS | 0 | Yes (manual) | RAM | No | No | **Yes** | Iceoryx, media, graphics |
| **POSIX Msg Queue** | Linux | 2 | No | MSGMAX | No | No | No | Embedded/RTOS-like |
| **D-Bus** | Linux | 2 | Yes | 128MB | Partial (intro) | Partial | No | Linux desktop services |
| **Binder** | Android | **1** | Yes | 1MB | Yes (AIDL) | Yes | Partial | Android services/HALs |
| **Mach IPC** | macOS/iOS | COW | Yes | ~4MB inline | No | No | COW | macOS kernel services |
| **XPC** | macOS/iOS | 1 | Yes | ~100MB | No (typed dict) | No | No | App extensions |
| **Named Pipe (Win)** | Windows | 2 | Yes | Configurable | No | No | No | SQL Server, local RPC |
| **COM/DCOM** | Windows | 2+ | Yes | Configurable | Yes (MIDL) | Yes | No | Win components, DirectX |
| **ALPC** | Windows | 1-2 | Yes | Variable | No (internal) | No | No | Internal Win RPC |
| **Zircon Channel** | Fuchsia | 1 | Yes | 64KB+handles | Yes (FIDL) | Yes | No | Fuchsia services |
| **Iceoryx** | Linux (POSIX) | **0** | Yes (pub/sub) | Pool size | Partial (C++) | Partial | **Yes** | ADAS, automotive |
| **io_uring** | Linux | 0 (rings) | Yes | Variable | No | No | Partial | High-perf I/O |

---

## 07.9 — Copy Count Deep Analysis

### Why Copies Matter

Each `copy_from_user` / `copy_to_user` costs:
- CPU cycles for memcpy (proportional to message size)
- Cache pollution (copied data evicts hot cache lines)
- TLB pressure (accessing multiple address spaces)
- Latency (synchronous blocking during copy)

### Quantitative Impact

| Message Size | Two-Copy Latency | One-Copy (Binder) | Zero-Copy (Iceoryx) |
|-------------|-----------------|-------------------|---------------------|
| 64 bytes | ~2 µs | ~1.5 µs | ~0.5 µs |
| 4 KB | ~5 µs | ~3 µs | ~0.5 µs |
| 64 KB | ~20 µs | ~12 µs | ~0.5 µs |
| 1 MB | ~200 µs | ~120 µs | ~0.5 µs |

*Note: Zero-copy latency is roughly constant regardless of message size because no data movement occurs.*

---

## 07.10 — Security: IPC Authentication and Access Control

### Linux

| Mechanism | Protection |
|-----------|-----------|
| `SCM_CREDENTIALS` | Kernel-verified PID/UID/GID on Unix sockets |
| File permissions | Socket file access control |
| SELinux | `unix_stream_socket` / `binder_call` policy rules |
| AppArmor | Path-based access control on socket files |
| seccomp | Restrict syscalls (can block IPC syscalls entirely) |

### Android (Binder-specific)

| Mechanism | Protection |
|-----------|-----------|
| Kernel-injected UID/PID | Every Binder transaction carries verified caller identity |
| SELinux `binder_call` | Policy controls which domains can call which services |
| ServiceManager ACL | Registration/lookup permission checks |
| `@VintfStability` | Marks interfaces for stability across vendor partition |

### macOS (Mach/XPC)

| Mechanism | Protection |
|-----------|-----------|
| Port rights | Capability-based — unforgeable tokens |
| Entitlements | Code-signed entitlements required for specific services |
| Sandbox | App Sandbox restricts Mach port access |
| audit_token | Kernel-verified process identity in XPC messages |

### Fuchsia (Zircon)

| Mechanism | Protection |
|-----------|-----------|
| Capability routing | Components only receive handles explicitly granted |
| No ambient authority | No global namespace; all access via handle |
| Handle rights | Read/write/duplicate/transfer — kernel-enforced |

---

## 07.11 — Cross-References

| Topic | Volume |
|-------|--------|
| RPC frameworks that use IPC as transport | Volume 06 — RPC Frameworks |
| Zero-copy serialization systems | Volume 09 — Zero-Copy Systems |
| Android AIDL (the contract language for Binder) | Volume 02 — IDLs |
| FIDL (the contract language for Zircon channels) | Volume 02 — IDLs |
| Automotive SOME/IP and DDS full coverage | Volume 11 — Industry-Specific |
| Transport layer bindings | Volume 14 — Transport & Runtime Binding |

---

*End of Volume 07 — IPC Mechanisms*
