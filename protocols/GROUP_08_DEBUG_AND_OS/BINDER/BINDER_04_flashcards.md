# ANDROID BINDER & AIDL — FLASHCARDS (250+)
# ════════════════════════════════════════════════════════════════════
# Protocol: Android Binder IPC | Document: 04 of 08
# Format: Q: / A: pairs organized in 10 themed decks
# ════════════════════════════════════════════════════════════════════

---

# DECK 1: IDENTITY & FUNDAMENTALS (25 cards)

**Q:** What is Binder?
**A:** Android's kernel-level IPC mechanism that enables cross-process method calls with 1-copy transfer, kernel-verified security, death notifications, and managed thread pools.

**Q:** What Linux syscall does Binder use?
**A:** `ioctl(fd, BINDER_WRITE_READ, &bwr)` — all communication goes through this single ioctl.

**Q:** How many memory copies does Binder use?
**A:** ONE. Client→kernel writes to server's mmap'd buffer. Server reads directly. (vs 2 copies for sockets/pipes).

**Q:** Binder kernel driver source location?
**A:** `drivers/android/binder.c` in the Linux kernel tree (or `drivers/staging/android/` in older kernels).

**Q:** What is the max Binder buffer per process?
**A:** 1 MB (default). Shared across all pending incoming transactions.

**Q:** Who created Binder?
**A:** OpenBinder by Be Inc./PalmSource (2000-2005). Rewritten for Linux by Dianne Hackborn for Android (2005).

**Q:** What exception is thrown when Binder buffer is full?
**A:** `TransactionTooLargeException` (Java) or `FAILED_TRANSACTION` status (native).

**Q:** Three binder device nodes?
**A:** `/dev/binder` (framework), `/dev/hwbinder` (HAL), `/dev/vndbinder` (vendor-vendor).

**Q:** Why three separate binder devices?
**A:** Security isolation: apps can't call HALs directly, vendor code can't touch framework services. Enforced by SELinux.

**Q:** What is ServiceManager?
**A:** The name registry at handle 0. Servers register by name, clients look up by name. First contact point for all Binder communication.

**Q:** Default Binder thread pool size?
**A:** 16 threads (15 spawned + 1 main thread that joins the pool).

**Q:** What is `ProcessState`?
**A:** Per-process singleton that opens `/dev/binder`, sets up mmap, and manages the thread pool. Created once per process.

**Q:** What is `IPCThreadState`?
**A:** Thread-local state for Binder communication. Manages the ioctl buffer, current transaction context, calling UID/PID.

**Q:** What does `joinThreadPool()` do?
**A:** Makes the calling thread enter the Binder event loop — it will process incoming transactions until the pool exits.

**Q:** What is the role of mmap in Binder?
**A:** Server mmaps /dev/binder → kernel and server share physical pages → kernel writes transaction data there → server reads without additional copy.

**Q:** Can you use Binder between Android and Linux (non-Android)?
**A:** Not standard — Binder is Android-specific. However, the kernel driver exists in mainline Linux; custom userspace could theoretically use it.

**Q:** What is a Binder context manager?
**A:** The process registered as handle 0 (ServiceManager). Set via `ioctl(BINDER_SET_CONTEXT_MGR)`. Only one per device node.

**Q:** What is `onTransact()`?
**A:** The method called on the server (BBinder) when a transaction arrives. Receives: transaction code, input Parcel, output Parcel, flags.

**Q:** What is `transact()`?
**A:** The method called on the client's proxy (BpBinder) to send a transaction. Parameters: code, data Parcel, reply Parcel, flags.

**Q:** What is transaction code?
**A:** An integer identifying which method to call. AIDL generates sequential codes starting from `FIRST_CALL_TRANSACTION (1)`.

**Q:** Minimum Android version with Binder?
**A:** Android 1.0 (2008) — Binder was there from day one.

**Q:** What replaced OpenBinder's userspace process model?
**A:** Android Binder moved all routing/management to the kernel driver (instead of a userspace daemon like D-Bus uses).

**Q:** Is Binder synchronous or asynchronous?
**A:** Both. Default is synchronous (client blocks until reply). `oneway` flag makes it asynchronous (fire-and-forget).

**Q:** Can Binder work across machines/network?
**A:** No. Binder is local IPC only (within one kernel). For network: use SOME/IP, gRPC, or custom transport.

**Q:** What is the Binder transaction limit for intents?
**A:** Practical limit ~500KB (within the 1MB buffer). Large Intent extras cause TransactionTooLargeException.

---

# DECK 2: KERNEL INTERNALS (25 cards)

**Q:** Key kernel struct for a process using Binder?
**A:** `binder_proc` — contains thread tree, node tree, ref trees, mmap info, buffer management.

**Q:** Key kernel struct for a Binder object?
**A:** `binder_node` — represents a BBinder owned by a process. Has reference counts, pointer to userspace object.

**Q:** What is `binder_ref`?
**A:** A client's reference to a remote binder_node. Contains a local handle number, pointer to the target node, strong/weak ref counts.

**Q:** What is `binder_transaction`?
**A:** In-flight transaction struct. Links sender thread, receiver process/thread, data buffer, transaction code, flags.

**Q:** What is `binder_buffer`?
**A:** Allocated chunk within a process's mmap'd region. Holds the actual Parcel data and object offsets for a transaction.

**Q:** What is `binder_write_read`?
**A:** The struct passed to ioctl(BINDER_WRITE_READ). Contains write buffer (commands to kernel) and read buffer (responses from kernel).

**Q:** What is BC_TRANSACTION?
**A:** Binder Command: "Send this transaction to the target." Written by client in the write buffer.

**Q:** What is BR_TRANSACTION?
**A:** Binder Return: "Here's an incoming transaction for you." Read by server from the read buffer.

**Q:** What is BC_REPLY?
**A:** Binder Command: "Here's my reply to the transaction I just processed." Written by server.

**Q:** What is BR_REPLY?
**A:** Binder Return: "Here's the reply to your transaction." Read by client (wakes from sleep).

**Q:** What is BC_FREE_BUFFER?
**A:** Command from userspace to kernel: "I'm done with this transaction buffer, free it." Essential to prevent buffer leaks.

**Q:** What is BR_SPAWN_LOOPER?
**A:** Kernel tells process: "All your threads are busy, spawn another one." Process creates a new binder thread.

**Q:** What is BC_ENTER_LOOPER?
**A:** Thread tells kernel: "I'm ready to receive transactions." Sent by main thread.

**Q:** What is BC_REGISTER_LOOPER?
**A:** Thread tells kernel: "I'm a newly spawned thread, ready for work." Sent by spawned pool threads.

**Q:** How does kernel wake a server thread?
**A:** `wake_up_interruptible()` on the thread's wait queue. Thread was sleeping in `binder_thread_read()`.

**Q:** What is `binder_alloc`?
**A:** Buffer allocator for a binder_proc. Manages free/used buffer list within the mmap'd region.

**Q:** How are binder_nodes indexed?
**A:** Red-black tree (`rb_root`) in `binder_proc->nodes`, keyed by userspace pointer address.

**Q:** How are binder_refs indexed?
**A:** Two red-black trees: `refs_by_desc` (by handle number) and `refs_by_node` (by target node pointer).

**Q:** What happens when a process with binder_nodes exits?
**A:** Kernel marks all nodes dead, sends BR_DEAD_BINDER to all refs with death notifications, cleans up all refs and buffers.

**Q:** What is `BINDER_SET_CONTEXT_MGR`?
**A:** ioctl that makes the calling process the ServiceManager (handle 0) for that binder device.

**Q:** What is `binder_transaction_data`?
**A:** Struct describing a single transaction: target (handle/ptr), code, flags, sender PID/UID, data size, offsets size, data pointer.

**Q:** What is the offsets array in a transaction?
**A:** Array of byte offsets into the data where `flat_binder_object` or FD entries are located. Kernel uses this to translate handles/FDs.

**Q:** How does kernel prevent handle forgery?
**A:** Each process has its own handle space (binder_ref tree). Handle numbers are process-local. Kernel translates on every transaction — userspace can't just invent valid handles.

**Q:** What is `binder_inner_proc_lock`?
**A:** Spinlock protecting per-process data structures (threads, nodes, buffers). Fine-grained locking for performance.

**Q:** Can a transaction target a specific thread?
**A:** Yes — for reply transactions. The reply goes to the thread that sent the original request (stored in `transaction_stack`).

---

# DECK 3: AIDL (30 cards)

**Q:** AIDL stands for?
**A:** Android Interface Definition Language.

**Q:** AIDL compiler generates what?
**A:** Proxy (client-side serialization), Stub (server-side deserialization), and Interface definition — in Java, C++, NDK C++, or Rust.

**Q:** AIDL direction tags?
**A:** `in` (caller→callee, default), `out` (callee→caller), `inout` (both directions).

**Q:** What is `@VintfStability`?
**A:** Annotation marking interface as stable ABI (frozen once released). Required for HAL interfaces (VINTF = Vendor Interface).

**Q:** What is `@nullable` in AIDL?
**A:** Marks parameter/return as possibly null. Maps to `std::optional<T>` in C++, `@Nullable` in Java.

**Q:** AIDL `oneway` keyword?
**A:** Makes method asynchronous — client doesn't wait for reply. Method must return void. Used for callbacks.

**Q:** How does AIDL versioning work?
**A:** Interface has version number + hash. New methods get added (never removed). Client queries `getInterfaceVersion()` to check capabilities.

**Q:** AIDL parcelable keyword?
**A:** Defines a structured data type that can be serialized into a Parcel. Like a struct that both sides understand.

**Q:** AIDL enum syntax?
**A:** `@Backing(type="int") enum MyEnum { VALUE_A = 0, VALUE_B = 1 }` — backed by int by default.

**Q:** AIDL union support?
**A:** Yes — `union MyUnion { int intVal; String strVal; }` — tagged union (only one field active).

**Q:** AIDL nested types?
**A:** Supported: interfaces can contain parcelables, enums, constants.

**Q:** How to pass a callback in AIDL?
**A:** Define callback as separate `interface IMyCallback { oneway void onEvent(...); }` then pass it as parameter.

**Q:** AIDL `String` encoding?
**A:** UTF-16 by default. Use `@utf8InCpp` annotation for UTF-8 in C++ backend.

**Q:** How to pass large data via AIDL?
**A:** Use `ParcelFileDescriptor` (passes FD) or `SharedMemory`. Actual data goes through shared memory, not Binder buffer.

**Q:** AIDL constant definition?
**A:** `const int MAX_SIZE = 1024;` — compile-time constants accessible by both sides.

**Q:** What is AIDL `List<>` type?
**A:** `List<T>` for variable-length lists (Java). In C++: `std::vector<T>`. Not recommended for large collections (hits buffer limit).

**Q:** AIDL `IBinder` type?
**A:** Raw Binder reference (untyped). Can pass any Binder object. Use when interface type isn't known at compile time.

**Q:** AIDL `FileDescriptor` vs `ParcelFileDescriptor`?
**A:** `ParcelFileDescriptor` is the preferred type — wraps FD with proper lifecycle management (closes on cleanup).

**Q:** Where are AIDL HAL interfaces defined?
**A:** `hardware/interfaces/<subsystem>/aidl/` in AOSP tree.

**Q:** How to build AIDL in Android.bp?
**A:** `aidl_interface { name: "my.interface", srcs: ["*.aidl"], stability: "vintf" }`

**Q:** AIDL Java backend output?
**A:** `IMyService.java` with inner classes `Stub` and `Stub.Proxy`.

**Q:** AIDL C++ backend output?
**A:** `IMyService.h`, `BpMyService.cpp`, `BnMyService.cpp`.

**Q:** AIDL NDK backend namespace?
**A:** `::aidl::my::package::IMyService` (aidl:: prefix to distinguish from platform libbinder).

**Q:** AIDL Rust backend?
**A:** Uses `binder` crate. Generates traits and implementations. Available since Android 12.

**Q:** What is `getInterfaceVersion()`?
**A:** Auto-generated method returning the interface version. Client calls it to check server's capabilities.

**Q:** What is `getInterfaceHash()`?
**A:** Returns hash of the interface definition. Ensures binary compatibility (catches API drift).

**Q:** AIDL `@Hide` annotation?
**A:** Hides interface/method from SDK documentation (still accessible by system apps).

**Q:** AIDL `@Enforce` annotation?
**A:** Specifies permission annotation that the build system validates. Compile-time permission checking.

**Q:** Can AIDL interfaces extend other interfaces?
**A:** Yes — `interface IChild extends IParent { ... }`. Child inherits parent's methods.

**Q:** AIDL `@FixedSize` parcelable?
**A:** Marks parcelable as fixed-size (no variable-length fields). Enables placement in shared memory without serialization overhead.

---

# DECK 4: HIDL (20 cards)

**Q:** HIDL stands for?
**A:** HAL Interface Definition Language.

**Q:** HIDL introduced in which Android version?
**A:** Android 8.0 (Oreo) as part of Project Treble.

**Q:** HIDL deprecated since?
**A:** Android 13 — new HALs must use AIDL.

**Q:** HIDL return syntax?
**A:** `method() generates (Type result, StatusCode status)` — uses `generates` clause.

**Q:** HIDL transport?
**A:** `/dev/hwbinder` (hardware binder device).

**Q:** HIDL passthrough mode?
**A:** HAL `.so` loaded directly into client process (dlopen). No IPC. Legacy compatibility mode.

**Q:** HIDL binderized mode?
**A:** HAL runs as separate process, IPC via hwbinder. Full process isolation.

**Q:** HIDL versioning format?
**A:** `@major.minor` package version. e.g., `android.hardware.automotive.vehicle@2.0`.

**Q:** What is `hidl-gen`?
**A:** The HIDL code generator tool. Produces C++ and Java bindings from `.hal` files.

**Q:** HIDL `vec<T>` type?
**A:** Variable-length vector. Maps to `hidl_vec<T>` in C++ (similar to std::vector but with shared memory optimization).

**Q:** HIDL `string` type?
**A:** Maps to `hidl_string` in C++ (includes size, supports zero-copy for large strings).

**Q:** What is FMQ in HIDL?
**A:** Fast Message Queue — lock-free ring buffer in shared memory. Bypass hwbinder for high-frequency data.

**Q:** FMQ flavors?
**A:** `kSynchronizedReadWrite` (blocking), `kUnsynchronizedWrite` (non-blocking, may overwrite).

**Q:** HIDL `oneway` keyword?
**A:** Same as AIDL oneway — async, no reply. Used for HAL callbacks.

**Q:** Where are HIDL interfaces defined?
**A:** `hardware/interfaces/<subsystem>/<version>/` in AOSP tree.

**Q:** HIDL manifest file?
**A:** Device manifest XML declares which HIDL services a device provides (version + interface + instance name).

**Q:** `lshal` command?
**A:** Lists all HIDL HAL services registered with hwservicemanager and their versions.

**Q:** HIDL `safe_union`?
**A:** Discriminated union type — explicitly tracks which field is active (type-safe unlike C union).

**Q:** Can HIDL and AIDL coexist?
**A:** Yes — during migration. Same HAL process can register both HIDL and AIDL interfaces.

**Q:** HIDL memory type?
**A:** `memory` — represents shared memory (hidl_memory in C++). Used for large data.

---

# DECK 5: SECURITY (25 cards)

**Q:** How does Binder identify callers?
**A:** Kernel sets UID and PID in transaction data. Unforgeable — userspace cannot modify.

**Q:** Java API to get calling UID?
**A:** `Binder.getCallingUid()` or `Binder.getCallingPid()`.

**Q:** C++ API to get calling UID?
**A:** `IPCThreadState::self()->getCallingUid()`.

**Q:** What is `enforceCallingPermission()`?
**A:** Java method that throws SecurityException if caller lacks the specified Android permission.

**Q:** SELinux control for Binder?
**A:** Controls: `call` (can transact), `transfer` (can pass objects), `set_context_mgr` (become ServiceManager).

**Q:** How does SELinux know which service is called?
**A:** ServiceManager add/find operations are labeled. E.g., `vehicle_hal_server:hwservice_manager { add find }`.

**Q:** What is a Binder capability/token?
**A:** An IBinder reference used as an unforgeable authorization. Having the reference = having the capability.

**Q:** Can caller UID be spoofed?
**A:** No — kernel sets it based on the sending process's credentials. Cannot be overridden by userspace.

**Q:** What is `clearCallingIdentity()`?
**A:** Temporarily replaces caller identity with the service's own identity. Used when service makes nested Binder calls on behalf of caller.

**Q:** What is `restoreCallingIdentity()`?
**A:** Restores the original caller's identity after `clearCallingIdentity()`.

**Q:** How do apps get permission for Binder calls?
**A:** AndroidManifest.xml declares required permissions. User/system grants them. Service checks at runtime via `enforceCallingPermission()`.

**Q:** Can an app directly call a HAL service?
**A:** No — SELinux denies app access to hwbinder. Apps must go through system services which proxy to HALs.

**Q:** What is `PERMISSION_DENIED` in native Binder?
**A:** Status code returned when a permission check fails. Maps to `SecurityException` in Java.

**Q:** How is Binder safer than shared memory?
**A:** Binder: kernel mediates every access, verifies identity, enforces SELinux. Shared memory: anyone with FD can read/write, no access logging.

**Q:** Binder vs D-Bus security?
**A:** Binder: kernel-level identity (unforgeable), per-transaction checks. D-Bus: userspace daemon checks, can be bypassed if daemon is compromised.

**Q:** How to audit Binder permission denials?
**A:** `logcat | grep "avc: denied"` for SELinux denials. `logcat | grep "SecurityException"` for Java permission checks.

**Q:** What is `binder:transfer` in SELinux?
**A:** Permission controlling whether process A can pass a Binder reference owned by process B to process C.

**Q:** Multi-user security in Binder?
**A:** UID encodes user ID (uid = userId * 100000 + appId). Services can check `UserHandle.getCallingUserId()`.

**Q:** Rate limiting for Binder?
**A:** Not built-in to kernel. Must be implemented in service (e.g., count calls per UID per second, reject excess).

**Q:** What is a confused deputy attack on Binder?
**A:** Privileged service forwards requests from unprivileged caller without checking caller identity. Fix: always check `getCallingUid()` before forwarding.

**Q:** Binder and encryption?
**A:** Binder data is NOT encrypted in transit (it's local IPC within one machine). Encryption is unnecessary for local kernel-mediated IPC.

**Q:** How does Service differentiate apps vs system?
**A:** Check UID: system (1000), root (0), app (10000+). Or check specific permissions.

**Q:** Can a rooted device bypass Binder security?
**A:** Root (UID 0) can bypass most permission checks. SELinux in enforcing mode still restricts even root. Full bypass requires SELinux permissive mode.

**Q:** What protects ServiceManager registration?
**A:** SELinux policy: only authorized processes can call `addService()` for specific service names.

**Q:** How to prevent DoS via Binder flooding?
**A:** Service-side rate limiting, per-UID quotas, SELinux restricting who can call, thread pool management (don't block all threads).

---

# DECK 6: AAOS / VEHICLE HAL (25 cards)

**Q:** What is Vehicle HAL?
**A:** Hardware Abstraction Layer exposing vehicle properties (speed, gear, HVAC) to Android frameworks via Binder.

**Q:** Vehicle HAL interface name (AIDL)?
**A:** `android.hardware.automotive.vehicle.IVehicle`

**Q:** Vehicle HAL transport?
**A:** HW Binder (binderized mode). Registered with ServiceManager.

**Q:** What is CarService?
**A:** Android system service managing all car-related functionality. Talks to Vehicle HAL via Binder. Exposes Car API to apps.

**Q:** What is CarPropertyManager?
**A:** Java API for apps to get/set vehicle properties. Communicates with CarService via Binder.

**Q:** Vehicle property subscription model?
**A:** Client subscribes with property ID + sample rate. HAL fires oneway callbacks when value changes or period expires.

**Q:** How does Vehicle HAL get data from CAN bus?
**A:** Vendor implementation reads CAN frames (via SocketCAN or custom driver), decodes signals, exposes as vehicle properties.

**Q:** Vehicle property ID encoding?
**A:** 32-bit: [group(4)][area_type(4)][data_type(4)][property_number(20)].

**Q:** PERF_VEHICLE_SPEED property ID?
**A:** 0x02070200 (float, global area).

**Q:** What is VehicleAreaType?
**A:** Defines property scope: GLOBAL, WINDOW, MIRROR, SEAT, DOOR, WHEEL.

**Q:** CarService permission for speed?
**A:** `android.car.permission.CAR_SPEED` — app must declare and be granted this permission.

**Q:** Vehicle HAL thread pool recommendation?
**A:** 4-8 threads. Handles: getValues, setValues, subscribe/unsubscribe concurrently.

**Q:** How does Vehicle HAL handle high-frequency data?
**A:** SharedMemory or FMQ for data >100Hz. Single Binder call shares memory FD, then data flows directly.

**Q:** Vehicle HAL HIDL version?
**A:** `android.hardware.automotive.vehicle@2.0` (deprecated).

**Q:** Vehicle HAL AIDL version (Android 14)?
**A:** Version 3 of `android.hardware.automotive.vehicle`.

**Q:** What is VehiclePropValue?
**A:** Parcelable containing: propertyId, areaId, timestamp, value (int32, float, int64, string, bytes).

**Q:** What is IVehicleCallback?
**A:** Callback interface for async responses and property change events from Vehicle HAL to framework.

**Q:** VHAL init.rc service definition?
**A:** `service vendor.vehicle-hal /vendor/bin/hw/android.hardware.automotive.vehicle-service` with class hal, user vehicle_network.

**Q:** How does CarService survive VHAL crash?
**A:** linkToDeath on VHAL Binder. On death: enter degraded mode, wait for VHAL restart (init respawns), re-subscribe.

**Q:** What is SOME/IP to Vehicle HAL bridge?
**A:** Vendor component that subscribes to SOME/IP services (Ethernet) and exposes received data as Vehicle HAL properties via Binder.

**Q:** Can apps directly call Vehicle HAL?
**A:** No — SELinux prevents. Apps call CarPropertyManager → CarService → Vehicle HAL (proper permission checks at each layer).

**Q:** Vehicle HAL testing tool?
**A:** `adb shell dumpsys car_service` or Vehicle HAL Emulator for testing without real hardware.

**Q:** What is VTS for Vehicle HAL?
**A:** Vendor Test Suite — automated tests verifying VHAL interface compliance. Run on real device or emulator.

**Q:** How many vehicle properties in AAOS?
**A:** ~200+ standard properties defined. Vendor can add custom properties in vendor-specific range.

**Q:** Property change modes?
**A:** ON_CHANGE (event when value changes), CONTINUOUS (periodic updates), STATIC (read once, never changes).

---

# DECK 7: PERFORMANCE & OPTIMIZATION (25 cards)

**Q:** Empty Binder transaction latency?
**A:** ~5-10 µs (kernel overhead: ioctl, wake, context switch).

**Q:** 4KB transaction latency?
**A:** ~30-50 µs (includes copy time).

**Q:** Maximum practical transaction rate?
**A:** ~100,000 transactions/second per process (depends on data size and hardware).

**Q:** FMQ latency vs Binder?
**A:** FMQ: <1 µs (pure memory write + atomic). Binder: 10-50 µs. FMQ is 10-50× faster.

**Q:** When to use FMQ vs Binder?
**A:** FMQ: >100 Hz sensor data, audio samples, repeated small messages. Binder: setup, configuration, infrequent calls, complex data.

**Q:** Why is oneway faster for the caller?
**A:** Caller doesn't wait for server processing. Returns after BR_TRANSACTION_COMPLETE (~5µs) instead of full round-trip.

**Q:** Binder call overhead in Java vs native?
**A:** Java: ~50-100µs (adds JNI crossing + object marshaling). Native C++: ~10-30µs. Use NDK backend for performance-critical HALs.

**Q:** How to reduce Binder transaction count?
**A:** Batch operations: instead of 100 individual calls, send one call with array of 100 items.

**Q:** SharedMemory vs Binder for 1MB data?
**A:** SharedMemory: one FD transfer via Binder (~20µs) + mmap. Direct Binder: ~500-1000µs. SharedMemory is ~50× faster for large data.

**Q:** Thread pool size impact?
**A:** Too small: transactions queue, latency increases. Too large: wasted memory/resources, context switch overhead.

**Q:** `SCHED_FIFO` for Binder threads?
**A:** Real-time scheduling for latency-sensitive services. Set via `ProcessState::self()->setThreadPoolMaxThreadCount()` + thread priority.

**Q:** Priority inheritance benefit?
**A:** Prevents priority inversion: high-priority client's call gets high-priority execution in server. Eliminates unbounded blocking.

**Q:** CPU affinity for Binder threads?
**A:** Pin Binder threads to specific CPU cores for cache locality. Reduces latency jitter for real-time services.

**Q:** Memory-locked Binder threads?
**A:** `mlockall(MCL_CURRENT | MCL_FUTURE)` prevents page faults during transaction handling. Important for real-time services.

**Q:** Transaction buffer fragmentation?
**A:** Many small allocations/frees can fragment the 1MB buffer. Large transaction fails even if total free space is sufficient. Fix: free buffers promptly.

**Q:** How to profile Binder performance?
**A:** Perfetto with binder ftrace events. Shows per-transaction latency, thread wake-up time, kernel processing time.

**Q:** Binder transaction in systrace?
**A:** Shows as paired arrows between processes. Width = duration. Can identify slow services and bottlenecks visually.

**Q:** Impact of SELinux on Binder latency?
**A:** Adds ~1-2µs per transaction for policy lookup. Negligible in practice but measurable in microbenchmarks.

**Q:** Zero-copy for AIDL?
**A:** Not truly zero-copy (always 1 copy). True zero-copy requires SharedMemory/FMQ (bypass Binder for data plane).

**Q:** Binder vs gRPC for local IPC?
**A:** Binder: ~10µs, kernel-optimized, Android-native. gRPC: ~100-500µs locally (serialization, HTTP/2 overhead). Binder wins for local.

**Q:** `BINDER_DEBUG_TRANSACTION` flag?
**A:** Kernel debug flag that logs all transactions to dmesg. Useful for development but significant performance impact.

**Q:** Oneway queue starvation?
**A:** If server is slow processing synchronous calls, oneway queue backs up. Async callbacks get delayed. Fix: keep sync handlers fast or separate pools.

**Q:** Impact of large Parcel on other clients?
**A:** Large transaction consumes binder buffer space. Other pending transactions for same process may fail. Keep transactions small.

**Q:** Transaction coalescing?
**A:** Not built into Binder. Application-level: batch property changes into single callback instead of one per property.

**Q:** Binder benchmark tool?
**A:** `binderThroughputTest` in AOSP — measures transactions/second and latency percentiles.

---

# DECK 8: DEBUGGING (25 cards)

**Q:** List all Binder services?
**A:** `adb shell service list` (framework) or `adb shell lshal` (HIDL/AIDL HALs).

**Q:** Dump service state?
**A:** `adb shell dumpsys <service_name>` — calls service's dump() method.

**Q:** Binder process info?
**A:** `cat /proc/binder/proc/<PID>` — shows threads, nodes, refs, buffers.

**Q:** Global Binder stats?
**A:** `cat /proc/binder/stats` — transaction counts, thread counts across all processes.

**Q:** Active transactions?
**A:** `cat /proc/binder/transactions` — shows in-flight transactions.

**Q:** Check thread pool exhaustion?
**A:** `cat /proc/binder/proc/<PID>` — look for threads all in "busy" state with none "waiting."

**Q:** Binder buffer usage?
**A:** In `/proc/binder/proc/<PID>`: look for "free async space" and total buffer size.

**Q:** Raw service call from shell?
**A:** `adb shell service call <name> <code> [args]` — sends raw Binder transaction.

**Q:** Find Binder threads of a process?
**A:** `ls /proc/<PID>/task/` then `cat /proc/<PID>/task/*/comm | grep Binder`.

**Q:** TransactionTooLargeException diagnosis?
**A:** Check buffer usage in `/proc/binder/proc/<PID>`. Look for leaked buffers (not freed) or too many pending oneways.

**Q:** ANR caused by Binder?
**A:** Main thread blocked on Binder call for >5s. Diagnosis: ANR trace shows main thread in `binder_thread_read`. Target service is slow or dead.

**Q:** Perfetto Binder trace config?
**A:** Enable `binder/binder_transaction` and `binder/binder_transaction_received` ftrace events.

**Q:** Binder death notification debugging?
**A:** Logcat filter for `binderDied`. Check if service process is actually running (`ps -A | grep <service>`).

**Q:** SELinux denial for Binder?
**A:** `adb logcat | grep "avc: denied"` then `grep binder`. Shows source, target, permission denied.

**Q:** `svc` command for Binder?
**A:** Not a standard tool. Use `service` command instead: `service check <name>`, `service call <name> <code>`.

**Q:** How to check if service is registered?
**A:** `adb shell service check <name>` — returns "Service <name>: found" or "not found."

**Q:** HIDL service status?
**A:** `adb shell lshal -itr` — shows all HIDL services, their PIDs, and transport (passthrough/hwbinder).

**Q:** Binder memory leak detection?
**A:** Monitor `/proc/binder/proc/<PID>` buffer usage over time. Increasing usage without plateau = leak.

**Q:** Stack trace of hung Binder thread?
**A:** `debuggerd -b <PID>` (native) or `kill -3 <PID>` (Java ANR trace to /data/anr/).

**Q:** Who is calling my service?
**A:** In `onTransact()`: log `getCallingUid()` and `getCallingPid()`. Or use Perfetto to trace callers.

**Q:** Binder transaction timeout?
**A:** No built-in timeout in kernel. Java has ~5s ANR timeout. Native: blocks indefinitely unless process dies. Implement app-level timeout.

**Q:** Testing AIDL without device?
**A:** Use `aidl_test` or mock frameworks. Or run on emulator with fake HAL implementations.

**Q:** Binder debug mask?
**A:** `echo 0xf > /sys/module/binder/parameters/debug_mask` — enables kernel debug logging.

**Q:** Checking Binder IPC count?
**A:** `/proc/binder/stats` shows: BC_TRANSACTION count, BR_REPLY count per process. Track over time for trends.

**Q:** dumpsys for car service?
**A:** `adb shell dumpsys car_service` — shows all car properties, subscribers, HAL status.

---

# DECK 9: PATTERNS & BEST PRACTICES (25 cards)

**Q:** Callback pattern best practice?
**A:** Define callback interface with `oneway` methods. Store as weak reference on server. Use linkToDeath to detect client death. Remove dead callbacks.

**Q:** How to avoid deadlock?
**A:** Never hold a lock while making a Binder call. Use oneway for callbacks. Avoid circular call patterns (A→B→A).

**Q:** Large data transfer pattern?
**A:** Allocate SharedMemory, write data, pass FD via Binder (tiny transaction). Receiver mmaps the same memory. Zero-copy for the data payload.

**Q:** Service restart pattern?
**A:** linkToDeath → binderDied() → cleanup stale state → retry getService()/waitForService() → re-register callbacks → resume.

**Q:** Batch API design?
**A:** Instead of `int getProperty(int id)` × N times, use `int[] getProperties(int[] ids)` — one transaction for N values.

**Q:** When to use oneway?
**A:** Callbacks, notifications, events, fire-and-forget commands. Any time caller doesn't need a return value and shouldn't block.

**Q:** When NOT to use oneway?
**A:** When you need return value, need error status, or need to know operation completed before proceeding.

**Q:** Thread pool sizing rule?
**A:** Expected concurrency × 1.5. E.g., if 4 clients call simultaneously → 6 threads minimum. Monitor and adjust.

**Q:** Avoiding TransactionTooLargeException?
**A:** Keep transactions <200KB. Use SharedMemory/FMQ for large data. Monitor buffer usage. Don't send bitmaps via Intent.

**Q:** Permission check pattern?
**A:** Check permission FIRST in onTransact before ANY processing. Early return on failure. Log violations.

**Q:** Identity forwarding pattern?
**A:** When system service calls HAL on behalf of app: clearCallingIdentity() → call HAL (with service's own UID) → restoreCallingIdentity().

**Q:** Multi-client subscription pattern?
**A:** Server maintains `RemoteCallbackList<IMyCallback>`. Handles death, registration, broadcasting. Thread-safe.

**Q:** Binder token authorization?
**A:** Create new Binder() as unforgeable token. Give to authorized client. Client presents token in calls. Server verifies identity.

**Q:** Lazy service pattern?
**A:** Service starts only when first client calls getService(). Uses `LazyServiceRegistrar` to auto-shutdown when no clients.

**Q:** One-shot vs persistent connection?
**A:** Persistent: hold proxy, reuse for multiple calls (common). One-shot: getService() each time (wasteful, avoid).

**Q:** Error handling in AIDL?
**A:** Use `Status` return: `binder::Status::fromExceptionCode()` for errors. Or define error codes in interface constants.

**Q:** Parcelable vs flat args?
**A:** Use Parcelable for complex structured data (reusable, versionable). Flat args for simple cases (1-2 primitives).

**Q:** Interface segregation?
**A:** Split large interfaces into focused ones. E.g., IVehicle + IVehicleBus + IVehicleDiag. Clients only bind what they need.

**Q:** Avoiding binder thread starvation?
**A:** Keep onTransact() handlers fast. Offload heavy work to separate worker threads. Return quickly from Binder thread.

**Q:** Versioned interface extension pattern?
**A:** Add new methods at end. Client checks getInterfaceVersion() before calling new methods. Old servers return NOT_IMPLEMENTED.

**Q:** Testing strategy for Binder services?
**A:** Unit test implementation (no Binder), integration test via Binder (real IPC), VTS for HALs, CTS for framework.

**Q:** Using RemoteCallbackList?
**A:** Thread-safe container for Binder callbacks. Automatically handles: dead client cleanup, thread-safe broadcast, registration/unregistration.

**Q:** Avoiding callback loops?
**A:** Callback from server to client should NOT trigger another Binder call back to server (from the callback thread). Use handler/post instead.

**Q:** Service AIDL best package naming?
**A:** `android.hardware.<subsystem>` for HALs. `com.android.<service>` for framework. Match directory structure.

**Q:** Documentation in AIDL?
**A:** JavaDoc-style comments (`/** ... */`) propagate to generated code. Document all methods, params, return values.

---

# DECK 10: COMPARISONS & ADVANCED (25 cards)

**Q:** Binder vs Unix domain socket?
**A:** Binder: 1 copy, built-in security (UID), death notify, thread pool. Socket: 2 copies, manual security, no death notify, manual threading.

**Q:** Binder vs D-Bus?
**A:** Binder: kernel-level (fast), Android-only. D-Bus: userspace daemon (slower), portable Linux, policy-based security.

**Q:** Binder vs gRPC?
**A:** Binder: local IPC optimized (~10µs). gRPC: network RPC with protobuf, works across machines but slower locally (~100µs+).

**Q:** Binder vs shared memory?
**A:** Binder: mediated (secure), managed, 1-copy. SHM: unmediated (fast but insecure), manual sync, 0-copy.

**Q:** Binder vs SOME/IP?
**A:** Binder: local IPC, kernel-managed. SOME/IP: network IPC over Ethernet (automotive), service-oriented, discovery-based.

**Q:** Binder vs Android Messenger?
**A:** Messenger: simple message-based IPC (wraps Binder Handler). Limited to Message/Bundle. AIDL: full RPC with arbitrary types.

**Q:** Binder vs ContentProvider?
**A:** ContentProvider: CRUD interface for shared data, uses Binder underneath, optimized for cursor/bulk data.

**Q:** What is `android.os.Binder` class?
**A:** Java base class for implementing Binder objects. Extends to create services (`Stub extends Binder`).

**Q:** What is `android.os.IBinder` interface?
**A:** Java interface for any Binder reference (local or remote). Methods: `transact()`, `linkToDeath()`, `isBinderAlive()`.

**Q:** What is `BinderProxy` in Java?
**A:** Java class representing a remote Binder object. Implements IBinder. Created when receiving a handle from another process.

**Q:** Native Binder libraries?
**A:** `libbinder` (platform C++), `libbinder_ndk` (NDK stable C++), `libbinder_rs` (Rust).

**Q:** What is `Parcel.obtain()` / `Parcel.recycle()`?
**A:** Pool-based Parcel management in Java. Obtain gets from pool (avoids allocation), recycle returns to pool.

**Q:** What is `AIDL frozen interfaces`?
**A:** Once an interface is released in a shipped Android version, its existing methods can never be modified — only new methods added.

**Q:** What is `@SuppressLint("NewApi")` for AIDL?
**A:** Suppress lint warning when using AIDL methods that may not exist on older interface versions. Use with version check.

**Q:** Binder and Kotlin coroutines?
**A:** Suspend functions can wrap Binder calls. Use `withContext(Dispatchers.IO)` for blocking Binder calls. AIDL doesn't natively support coroutines.

**Q:** Binder in Android kernel vs mainline Linux?
**A:** Binder driver is in mainline Linux kernel (since 3.19). Android uses it with additional patches (sometimes ahead of mainline).

**Q:** What is `android.os.ServiceManager`?
**A:** Java API to interact with ServiceManager: `getService()`, `addService()`, `listServices()`, `waitForService()`.

**Q:** Binder and SELinux contexts?
**A:** Each process has SELinux context (e.g., `u:r:vehicle_hal:s0`). Binder driver passes context to target for policy decisions.

**Q:** What is `hwbinder_use` macro?
**A:** SELinux macro granting process permission to use hwbinder device node and communicate via hwbinder.

**Q:** Binder and APEX modules?
**A:** APEX (updatable modules) can define AIDL services. Registered via ServiceManager, accessible like any other service.

**Q:** Future of Binder?
**A:** Rust backends (safety), improved performance, potential cross-VM support, AIDL as sole interface language (HIDL fully removed).

**Q:** Binder and Trusty/TEE?
**A:** Trusty OS (TEE) has its own IPC. Communication between Android and Trusty uses tipc (not Binder). Binder doesn't cross TEE boundary.

**Q:** What is `libbinder_rs`?
**A:** Rust bindings for Binder. Provides safe Rust API for implementing and calling Binder services. Used in Android 12+.

**Q:** Maximum number of Binder nodes per process?
**A:** No hard limit in kernel. Practical: thousands. Limited by memory and file descriptor table.

**Q:** Binder vs Wayland IPC?
**A:** Binder: general-purpose Android IPC. Wayland: display protocol (compositor↔client). Different purposes. SurfaceFlinger uses Binder, not Wayland.

---

END OF DOCUMENT 04 — FLASHCARDS (250+)
