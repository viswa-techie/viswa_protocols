# ANDROID BINDER & AIDL — REFERENCES & STUDY PATH
# ════════════════════════════════════════════════════════════════════
# Protocol: Android Binder IPC | Document: 08 of 08
# AOSP sources, documentation, books, and structured study plan
# ════════════════════════════════════════════════════════════════════

---

## 1. AOSP SOURCE CODE PATHS

### Kernel Driver
| Path | Description |
|------|-------------|
| `drivers/android/binder.c` | Main Binder driver implementation |
| `drivers/android/binder_alloc.c` | Buffer allocator |
| `drivers/android/binder_alloc.h` | Allocator header |
| `drivers/android/binder_internal.h` | Internal kernel structures |
| `drivers/android/binder_trace.h` | Ftrace event definitions |
| `include/uapi/linux/android/binder.h` | UAPI header (ioctl definitions, BC_/BR_ commands) |

### Userspace Libraries
| Path | Description |
|------|-------------|
| `frameworks/native/libs/binder/` | Platform libbinder (C++) |
| `frameworks/native/libs/binder/ndk/` | NDK libbinder (stable C++ API) |
| `frameworks/native/libs/binder/rust/` | Rust binder bindings |
| `frameworks/base/core/java/android/os/Binder.java` | Java Binder base class |
| `frameworks/base/core/java/android/os/IBinder.java` | Java IBinder interface |
| `frameworks/base/core/java/android/os/Parcel.java` | Java Parcel class |
| `frameworks/base/core/java/android/os/ServiceManager.java` | Java ServiceManager |
| `frameworks/native/cmds/servicemanager/` | ServiceManager process (native) |

### Key Headers
| File | Contents |
|------|----------|
| `IBinder.h` | IBinder interface, DeathRecipient |
| `Binder.h` | BBinder, BpRefBase |
| `BpBinder.h` | BpBinder proxy implementation |
| `Parcel.h` | Parcel serialization |
| `ProcessState.h` | Per-process Binder state |
| `IPCThreadState.h` | Per-thread Binder state |
| `IServiceManager.h` | ServiceManager interface |
| `IInterface.h` | BnInterface, BpInterface templates |

### AIDL Compiler & Tools
| Path | Description |
|------|-------------|
| `system/tools/aidl/` | AIDL compiler source |
| `system/tools/aidl/aidl_language.cpp` | AIDL parser |
| `system/tools/aidl/generate_java.cpp` | Java code generator |
| `system/tools/aidl/generate_cpp.cpp` | C++ code generator |
| `system/tools/aidl/generate_ndk.cpp` | NDK backend generator |
| `system/tools/aidl/generate_rust.cpp` | Rust backend generator |

### Vehicle HAL (AAOS)
| Path | Description |
|------|-------------|
| `hardware/interfaces/automotive/vehicle/aidl/` | AIDL Vehicle HAL interface |
| `hardware/interfaces/automotive/vehicle/2.0/` | HIDL Vehicle HAL (deprecated) |
| `packages/services/Car/service/` | CarService source |
| `packages/services/Car/car-lib/` | Car API library |
| `device/generic/car/emulator/vhal/` | Reference Vehicle HAL implementation |

### Fast Message Queue (FMQ)
| Path | Description |
|------|-------------|
| `system/libfmq/` | FMQ library |
| `system/libfmq/include/fmq/` | FMQ headers |
| `system/libfmq/include/fmq/AidlMessageQueue.h` | AIDL FMQ |
| `system/libfmq/include/fmq/EventFlag.h` | EventFlag signaling |

### SELinux Policy
| Path | Description |
|------|-------------|
| `system/sepolicy/` | Base AOSP SELinux policy |
| `system/sepolicy/public/binder.te` | Binder-specific policy macros |
| `system/sepolicy/private/service_contexts` | Service name → SELinux type mapping |
| `system/sepolicy/public/hwservice.te` | HW service types |

### Testing
| Path | Description |
|------|-------------|
| `frameworks/native/libs/binder/tests/` | Binder unit tests |
| `frameworks/native/libs/binder/tests/binderThroughputTest.cpp` | Performance benchmark |
| `hardware/interfaces/automotive/vehicle/aidl/vts/` | Vehicle HAL VTS tests |
| `cts/tests/tests/os/src/android/os/cts/BinderTest.java` | CTS Binder tests |

---

## 2. OFFICIAL DOCUMENTATION

### Android Developer Docs
| Topic | URL |
|-------|-----|
| AIDL Overview | https://developer.android.com/develop/background-work/services/aidl |
| Bound Services | https://developer.android.com/develop/background-work/services/bound-services |
| Parcelable | https://developer.android.com/reference/android/os/Parcelable |
| Binder API | https://developer.android.com/reference/android/os/Binder |
| IBinder API | https://developer.android.com/reference/android/os/IBinder |

### Android Source Docs (source.android.com)
| Topic | URL |
|-------|-----|
| AIDL for HALs | https://source.android.com/docs/core/architecture/aidl/aidl-hals |
| AIDL Backends | https://source.android.com/docs/core/architecture/aidl/aidl-backends |
| AIDL Stable/VINTF | https://source.android.com/docs/core/architecture/aidl/stable-aidl |
| HIDL Overview | https://source.android.com/docs/core/architecture/hidl |
| HIDL → AIDL Migration | https://source.android.com/docs/core/architecture/aidl/aidl-hals#converting-from-hidl |
| Vehicle HAL | https://source.android.com/docs/automotive/vhal |
| SELinux | https://source.android.com/docs/security/features/selinux |
| Treble Architecture | https://source.android.com/docs/core/architecture |
| VINTF | https://source.android.com/docs/core/architecture/vintf |
| Fast Message Queue | https://source.android.com/docs/core/architecture/hidl/fmq |

### Android Automotive (AAOS)
| Topic | URL |
|-------|-----|
| AAOS Architecture | https://source.android.com/docs/automotive |
| Car API | https://source.android.com/docs/automotive/start/what_automotive |
| Vehicle Properties | https://source.android.com/docs/automotive/vhal/properties |
| CarService | https://source.android.com/docs/automotive/car-service |

---

## 3. BOOKS

| Title | Author | Year | Relevance |
|-------|--------|------|-----------|
| *Embedded Android* | Karim Yaghmour | 2013 | Binder internals, system architecture (dated but foundational) |
| *Android Internals: A Confectioner's Cookbook* | Jonathan Levin | 2014-2020 | Deep Binder analysis, kernel driver walkthrough |
| *Android System Programming* | Roger Ye | 2017 | HAL development, Binder usage |
| *Learning Linux Binary Analysis* | Ryan O'Neill | 2016 | ELF/binary analysis relevant to Binder debugging |
| *Linux Kernel Development* | Robert Love | 2010 | Kernel foundations (scheduling, memory) relevant to driver |
| *SELinux System Administration* | Sven Vermeulen | 2016 | SELinux policy writing for Android |

---

## 4. KEY BLOG POSTS & ARTICLES

| Title | Author/Source | Topic |
|-------|--------------|-------|
| "Android Binder" | Thorsten Schreiber (thesis) | Comprehensive Binder analysis |
| "Deep Dive into Android IPC/Binder Framework" | Various (Medium) | Architecture overview |
| "An Introduction to Android Binder" | Android Open Source Project | Official introduction |
| "Binder – Desktop Summit" | Various (LWN.net) | Kernel perspective on Binder |
| "AIDL for HALs" | Android Developers Blog | AIDL migration guide |
| "Project Treble" | Android Developers Blog | Motivation for HIDL/Binder separation |
| "Android Automotive Vehicle HAL" | AOSP Docs | Vehicle HAL architecture |
| "Fast Message Queues in Android" | AOSP Docs | FMQ design and usage |

---

## 5. TRACING & PROFILING TOOLS

### Perfetto
| Resource | Description |
|----------|-------------|
| https://perfetto.dev | Main Perfetto documentation |
| https://ui.perfetto.dev | Web-based trace viewer |
| `perfetto` binary on device | Capture traces on-device |
| Binder ftrace events | `binder/binder_transaction`, `binder/binder_transaction_received` |

### Systrace (Legacy)
| Resource | Description |
|----------|-------------|
| `systrace.py` in SDK | Capture system traces |
| `catapult` project | Trace viewer |
| Tags: `binder_driver`, `binder_lock` | Binder-specific trace categories |

### Debug Commands
| Command | Purpose |
|---------|---------|
| `cat /proc/binder/stats` | Global statistics |
| `cat /proc/binder/proc/<PID>` | Per-process state |
| `cat /proc/binder/transactions` | Active transactions |
| `service list` | All registered services |
| `dumpsys <service>` | Service dump |
| `lshal` | HIDL/AIDL HAL list |
| `cmd <service> <subcommand>` | Service shell commands |

---

## 6. RELATED TECHNOLOGIES

| Technology | Relation to Binder |
|-----------|-------------------|
| **SOME/IP** | Automotive Ethernet IPC; complementary to Binder for cross-ECU |
| **gRPC** | Network RPC; Binder is local equivalent |
| **D-Bus** | Linux desktop IPC; Binder is Android's replacement |
| **Wayland** | Display protocol; SurfaceFlinger uses Binder, not Wayland |
| **virtio** | VM I/O; may transport Binder across VMs |
| **Ashmem/SharedMemory** | Large data companion to Binder (FD-passed) |
| **ION/dmabuf** | GPU buffer sharing, FDs passed via Binder |
| **SELinux** | Mandatory access control enforcing Binder security |
| **init.rc** | Service startup (HALs started by init) |
| **VINTF** | Interface versioning framework dependent on Binder |

---

## 7. CONFERENCE TALKS & VIDEOS

| Talk | Event | Topic |
|------|-------|-------|
| "Android Binder IPC" | Android Builders Summit | Binder internals |
| "Project Treble" | Google I/O 2017 | Architecture separation |
| "AIDL for HALs" | Android Dev Summit 2021 | AIDL migration |
| "Android Automotive Architecture" | Google I/O | AAOS overview |
| "Perfetto Tracing" | Android Dev Summit | Trace analysis |
| "SELinux on Android" | Linux Security Summit | Policy writing |

---

## 8. STRUCTURED STUDY PATH (6 Weeks)

### Week 1: Foundations
| Day | Topic | Resources |
|-----|-------|-----------|
| 1 | What is IPC, why Binder | BINDER_01 §1-3, Android docs |
| 2 | Binder architecture overview | BINDER_01 §4-5, BINDER_02 diagrams 1-3 |
| 3 | ServiceManager & service registration | BINDER_01 §7, `service list` command |
| 4 | AIDL basics: syntax, compilation | BINDER_06 §2, write simple .aidl |
| 5 | Hands-on: LAB 1 (define AIDL) | BINDER_07 LAB 1 |
| 6-7 | Review flashcards Deck 1 & 3 | BINDER_04 Deck 1, 3 |

### Week 2: Kernel & Transactions
| Day | Topic | Resources |
|-----|-------|-----------|
| 1 | Kernel data structures | BINDER_01 §5-6, Deck 2 |
| 2 | One-copy mmap mechanism | BINDER_01 §6, BINDER_02 diagram 4 |
| 3 | Transaction flow (step by step) | BINDER_01 §8, BINDER_02 diagrams 5-6 |
| 4 | BC_/BR_ commands | BINDER_06 §5, BINDER_01 §8 |
| 5 | Parcel serialization | BINDER_06 §6, BINDER_01 §9 |
| 6-7 | Review: Read kernel source `binder.c` (skim) | AOSP source |

### Week 3: Implementation
| Day | Topic | Resources |
|-----|-------|-----------|
| 1 | Implement server (Java) | LAB 2 |
| 2 | Implement client | LAB 3 |
| 3 | Callbacks & death notification | LAB 4, BINDER_01 §12 |
| 4 | Reference counting & lifecycle | BINDER_01 §11, Deck 2 |
| 5 | Thread pool management | BINDER_01 §14, BINDER_06 §8 |
| 6-7 | Practice interview questions (Junior) | BINDER_03 Part A |

### Week 4: AIDL/HIDL & HALs
| Day | Topic | Resources |
|-----|-------|-----------|
| 1 | AIDL features: versioning, annotations | BINDER_01 §10, Deck 3 |
| 2 | HIDL overview & Treble | BINDER_01 §11, Deck 4 |
| 3 | HIDL → AIDL migration | BINDER_03 Q58 |
| 4 | NDK backend, native implementation | LAB 5 |
| 5 | VINTF manifest & compatibility | BINDER_06 §11 |
| 6-7 | Practice Mid-level questions | BINDER_03 Part B |

### Week 5: AAOS & Performance
| Day | Topic | Resources |
|-----|-------|-----------|
| 1 | Vehicle HAL architecture | BINDER_01 §17, Deck 6 |
| 2 | Vehicle HAL implementation | LAB 5 (Vehicle HAL section) |
| 3 | Fast Message Queue (FMQ) | LAB 6, BINDER_01 §16 |
| 4 | Performance profiling (Perfetto) | LAB 7 |
| 5 | SELinux policy for HALs | LAB 8 |
| 6-7 | Practice Senior questions | BINDER_03 Part C |

### Week 6: Advanced & Integration
| Day | Topic | Resources |
|-----|-------|-----------|
| 1 | Security deep-dive | BINDER_01 §13, Deck 5 |
| 2 | Debugging scenarios 1-3 | BINDER_07 Scenarios 1-3 |
| 3 | Debugging scenarios 4-7 | BINDER_07 Scenarios 4-7 |
| 4 | Architecture patterns & best practices | BINDER_04 Deck 9 |
| 5 | Staff-level design questions | BINDER_03 Part D |
| 6-7 | Full review: cheatsheet + flashcards | BINDER_06, BINDER_04 all decks |

---

## 9. INTERVIEW PREPARATION CHECKLIST

### Must-Know (All Levels)
- [ ] What Binder is and why Android uses it (vs sockets, pipes)
- [ ] One-copy mechanism (mmap trick)
- [ ] AIDL: define interface, compile, use generated code
- [ ] Proxy/Stub pattern
- [ ] ServiceManager role
- [ ] oneway vs synchronous
- [ ] linkToDeath and death notification
- [ ] Security: getCallingUid(), SELinux
- [ ] 1MB buffer limit and workarounds

### Mid-Level Additions
- [ ] Kernel structures (binder_proc, binder_node, binder_ref)
- [ ] BC_/BR_ command protocol
- [ ] Thread pool management
- [ ] Priority inheritance
- [ ] HIDL vs AIDL, migration path
- [ ] FMQ for high-frequency data
- [ ] VINTF and versioning
- [ ] Debugging with /proc/binder, Perfetto

### Senior/Staff Additions
- [ ] Design IPC architecture for automotive system
- [ ] Debug complex deadlocks
- [ ] Vehicle HAL design for 200+ properties
- [ ] Multi-SoC communication strategies
- [ ] Zero-downtime HAL updates
- [ ] Security for multi-tenant systems
- [ ] Performance optimization (latency < targets)

---

## 10. GLOSSARY

| Term | Definition |
|------|------------|
| **AIDL** | Android Interface Definition Language |
| **BBinder** | Server-side Binder object (local) |
| **BpBinder** | Client-side Binder proxy (remote) |
| **BC_** | Binder Command (userspace → kernel) |
| **BR_** | Binder Return (kernel → userspace) |
| **CarService** | Android system service managing car features |
| **FMQ** | Fast Message Queue (lock-free shared memory ring buffer) |
| **HAL** | Hardware Abstraction Layer |
| **HIDL** | HAL Interface Definition Language (deprecated) |
| **hwbinder** | Hardware Binder device (/dev/hwbinder) |
| **IBinder** | Base interface for all Binder references |
| **IPCThreadState** | Thread-local Binder communication state |
| **mmap** | Memory-mapped I/O (enables one-copy optimization) |
| **oneway** | Asynchronous (non-blocking) Binder call |
| **Parcel** | Serialization container for Binder transaction data |
| **ProcessState** | Per-process Binder singleton |
| **Proxy** | Client-side generated stub (serializes, calls transact) |
| **ServiceManager** | Name registry for Binder services (handle 0) |
| **SOME/IP** | Scalable service-Oriented MiddlewarE over IP |
| **Stub** | Server-side generated code (deserializes, dispatches) |
| **Treble** | Android architecture separating vendor/framework |
| **VHAL** | Vehicle HAL |
| **VINTF** | Vendor Interface — versioned compatibility framework |
| **vndbinder** | Vendor Binder device (/dev/vndbinder) |
| **VTS** | Vendor Test Suite |

---

END OF DOCUMENT 08 — REFERENCES & STUDY PATH
