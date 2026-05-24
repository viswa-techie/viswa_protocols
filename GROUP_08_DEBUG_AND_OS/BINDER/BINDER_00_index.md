# ANDROID BINDER & AIDL — ENCYCLOPEDIA INDEX
# ════════════════════════════════════════════════════════════════════
# Protocol: Android Binder IPC + AIDL + HIDL
# Position: #05 in Protocol Curriculum
# Domain: Android OS / AAOS / Embedded Linux / Automotive
# ════════════════════════════════════════════════════════════════════

---

## PROTOCOL IDENTITY CARD

| Field | Value |
|-------|-------|
| Full Name | Android Binder Inter-Process Communication |
| Type | IPC (Inter-Process Communication) Mechanism |
| Layer | Kernel driver + Userspace framework |
| Kernel Driver | `/dev/binder`, `/dev/hwbinder`, `/dev/vndbinder` |
| Interface Languages | AIDL (Android Interface Definition Language), HIDL (HAL Interface Definition Language) |
| First Introduced | Android 1.0 (2008), based on OpenBinder (BeOS/PalmOS, 2005) |
| Key Operations | Transact, Oneway, Death Notification, File Descriptor Passing |
| Max Transaction Size | 1 MB (default Binder buffer per process) |
| Thread Pool | 16 threads default (configurable) |
| Transport | Shared memory + kernel driver (zero-copy for large data) |
| Primary Use | All Android IPC: Activity↔Service, App↔System, Framework↔HAL |
| Automotive Relevance | AAOS Vehicle HAL (HIDL/AIDL), SOME/IP-to-Binder bridges, CarService |

---

## DOCUMENT MAP

| # | Document | Description | Pages |
|---|----------|-------------|-------|
| 00 | **This Index** | Navigation, overview, comparisons | — |
| 01 | [BINDER_01_master_theory.md](BINDER_01_master_theory.md) | Complete theory: kernel driver, userspace, AIDL, HIDL, transactions | ~800 lines |
| 02 | [BINDER_02_diagrams.md](BINDER_02_diagrams.md) | 20 Mermaid/ASCII diagrams | ~400 lines |
| 03 | [BINDER_03_interview_questions.md](BINDER_03_interview_questions.md) | 80+ questions: Junior → Staff | ~600 lines |
| 04 | [BINDER_04_flashcards.md](BINDER_04_flashcards.md) | 250+ Q/A flashcards in 10 decks | ~700 lines |
| 06 | [BINDER_06_cheatsheet.md](BINDER_06_cheatsheet.md) | Quick reference: commands, structures, APIs | ~350 lines |
| 07 | [BINDER_07_labs_debugging.md](BINDER_07_labs_debugging.md) | 8 labs + 7 debug scenarios | ~500 lines |
| 08 | [BINDER_08_references.md](BINDER_08_references.md) | Sources, repos, study path | ~300 lines |

---

## WHY BINDER MATTERS

```
┌────────────────────────────────────────────────────────────────┐
│                    ANDROID ARCHITECTURE                         │
├────────────────────────────────────────────────────────────────┤
│  Apps (Java/Kotlin)                                            │
│       ↕ AIDL (Binder)                                         │
│  System Services (SystemServer: AMS, WMS, PMS, CarService)    │
│       ↕ AIDL (Binder)                                         │
│  Framework (native C++ services)                               │
│       ↕ HIDL/AIDL (HwBinder)                                  │
│  HAL Implementations (vendor)                                  │
│       ↕ ioctl                                                  │
│  Kernel Drivers (hardware)                                     │
└────────────────────────────────────────────────────────────────┘

EVERY cross-process call in Android goes through Binder.
No Binder = No Android.
```

---

## BINDER vs OTHER IPC MECHANISMS

| Feature | Binder | Unix Socket | Shared Memory | D-Bus | SOME/IP |
|---------|--------|-------------|---------------|-------|---------|
| Copy count | 1 (kernel→target) | 2 (usr→ker→usr) | 0 | 2+ | 2 (network) |
| Security | UID/PID checked by kernel | File permissions | Manual | Policy-based | None built-in |
| Object refs | ✓ (remote object model) | ✗ | ✗ | ✗ (path-based) | ✗ |
| Death notify | ✓ (link-to-death) | ✗ | ✗ | ✗ | TTL-based |
| Thread mgmt | ✓ (kernel-managed pool) | Manual | Manual | Event loop | Manual |
| Interface gen | AIDL/HIDL compiler | Manual | Manual | XML introspection | SOME/IP generator |
| Typical use | Android IPC | Unix daemons | Multimedia buffers | Linux desktop | Automotive Ethernet |
| Latency | ~5-50µs | ~10-100µs | <1µs | ~100µs | ~1ms (network) |

---

## THREE BINDER DOMAINS (Android 8.0+)

| Domain | Device Node | Purpose | Users |
|--------|-------------|---------|-------|
| Framework Binder | `/dev/binder` | App ↔ System Services | Apps, SystemServer |
| HW Binder | `/dev/hwbinder` | Framework ↔ HAL | System services, HAL processes |
| Vendor Binder | `/dev/vndbinder` | Vendor ↔ Vendor | Vendor-proprietary IPC |

Separation enforced by SELinux — apps cannot access hwbinder, vendor cannot access framework binder.

---

## AIDL vs HIDL COMPARISON

| Aspect | AIDL | HIDL |
|--------|------|------|
| Full name | Android Interface Definition Language | HAL Interface Definition Language |
| Introduced | Android 1.0 (2008) | Android 8.0 Treble (2017) |
| Target | App↔System, System↔System | Framework↔HAL |
| Transport | `/dev/binder` | `/dev/hwbinder` |
| Languages | Java, C++, Rust, NDK | C++, Java (limited) |
| Status (2024+) | **Active** (replacing HIDL) | **Deprecated** (migrating to AIDL HALs) |
| Versioning | Interface hash + versions | Major.Minor package versions |
| Memory | Parcel (Binder buffer) | Parcel + FMQ (Fast Message Queue) |
| Code gen | `aidl` compiler | `hidl-gen` compiler |

**Key trend**: Google is migrating ALL HALs from HIDL → AIDL (started Android 11, mandatory new HALs from Android 13+).

---

## AUTOMOTIVE (AAOS) BINDER USAGE

```
┌─────────────────────────────────────────────────┐
│              AAOS Application Layer              │
│  Car Settings │ Maps │ Media │ Climate          │
├─────────────────────────────────────────────────┤
│              CarService (Java)                   │
│  CarPropertyManager │ CarAudioManager │ ...     │
│         ↕ AIDL Binder                           │
├─────────────────────────────────────────────────┤
│         Vehicle HAL (AIDL or HIDL)              │
│  android.hardware.automotive.vehicle            │
│         ↕ HwBinder                              │
├─────────────────────────────────────────────────┤
│     Vehicle HAL Implementation (Vendor)         │
│  Reads CAN/SOME-IP/Ethernet → exposes props    │
├─────────────────────────────────────────────────┤
│         Vehicle Network (CAN, Ethernet)         │
└─────────────────────────────────────────────────┘

Vehicle Properties flow:
  Speed, Gear, HVAC temp → CAN/Ethernet → VHAL → Binder → CarService → App
```

---

## STUDY CHECKLIST

### Beginner
- [ ] Understand what IPC is and why Android needs Binder
- [ ] Know the 3 binder domains (binder, hwbinder, vndbinder)
- [ ] Write a simple AIDL interface and client/server
- [ ] Understand Proxy/Stub pattern
- [ ] Use `adb shell service list` and `dumpsys`

### Intermediate
- [ ] Understand kernel driver operation (binder_ioctl, binder_transaction)
- [ ] Know Parcel serialization format
- [ ] HIDL interface definition and passthrough vs binderized
- [ ] Death notification (linkToDeath)
- [ ] Oneway transactions and async callbacks
- [ ] ServiceManager role and service registration

### Advanced
- [ ] Kernel driver internals (binder_proc, binder_thread, binder_node)
- [ ] Memory mapping and one-copy optimization
- [ ] Scattered transaction data (binder_buffer)
- [ ] AIDL HAL migration from HIDL
- [ ] Performance tuning (thread pool, transaction size, FMQ)
- [ ] Security: SELinux, UID verification, capability checks
- [ ] AAOS Vehicle HAL deep dive (property IDs, subscription model)
- [ ] Debug with binder traces, systrace, perfetto

---

## CURRICULUM POSITION

```
Completed: [✓] UART → [✓] SPI → [✓] I2C → [✓] Automotive Ethernet
Current:   [→] Binder/AIDL ← YOU ARE HERE
Upcoming:  [ ] CAN FD → [ ] LIN → [ ] AXI → [ ] PCIe → [ ] USB
           [ ] JTAG/SWD → [ ] Bluetooth/BLE → [ ] Wi-Fi → [ ] MIPI
```

---

END OF DOCUMENT 00 — INDEX
