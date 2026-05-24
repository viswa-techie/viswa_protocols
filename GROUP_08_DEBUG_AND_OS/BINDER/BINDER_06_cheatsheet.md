# ANDROID BINDER & AIDL — CHEATSHEET
# ════════════════════════════════════════════════════════════════════
# Protocol: Android Binder IPC | Document: 06 of 08
# Quick-reference for commands, APIs, syntax, and troubleshooting
# ════════════════════════════════════════════════════════════════════

---

## 1. ADB / SHELL COMMANDS

### Service Management
```bash
# List all framework Binder services
adb shell service list

# Check if specific service exists
adb shell service check <service_name>
adb shell service check vehicle

# Raw Binder call (code = method number, i32/s16 = arg types)
adb shell service call <service_name> <code> [i32 <int>] [s16 <string>]
adb shell service call phone 1 s16 "5551234"

# List HIDL HAL services
adb shell lshal
adb shell lshal -itr                    # Interfaces, transport, readable

# Dump service state
adb shell dumpsys <service_name>
adb shell dumpsys car_service           # AAOS car service
adb shell dumpsys activity              # Activity manager
adb shell dumpsys -l                    # List all dumpable services
```

### Binder Kernel Info (procfs)
```bash
# Global Binder statistics
adb shell cat /proc/binder/stats

# Per-process Binder info (threads, nodes, refs, buffers)
adb shell cat /proc/binder/proc/<PID>

# Active transactions
adb shell cat /proc/binder/transactions

# Binder state (all processes)
adb shell cat /proc/binder/state

# Debug mask (enable kernel logging)
adb shell "echo 0xf > /sys/module/binder/parameters/debug_mask"

# Check buffer usage
adb shell cat /proc/binder/proc/<PID> | grep -A5 "allocated"
```

### Process & Thread Debugging
```bash
# Find Binder threads of a process
adb shell ls /proc/<PID>/task/ | while read tid; do
  echo "$tid: $(cat /proc/<PID>/task/$tid/comm 2>/dev/null)"
done | grep -i binder

# Thread wait state
adb shell cat /proc/<PID>/task/*/wchan

# Native stack trace
adb shell debuggerd -b <PID>

# Java stack trace (ANR dump)
adb shell kill -3 <PID>                 # Output to /data/anr/

# Find PID of a service process
adb shell pidof <process_name>
adb shell pidof android.hardware.automotive.vehicle-service
```

### SELinux
```bash
# Check denials
adb logcat | grep "avc: denied" | grep binder

# Current SELinux mode
adb shell getenforce

# Process SELinux context
adb shell ps -eZ | grep <process>
```

---

## 2. AIDL SYNTAX REFERENCE

### Basic Interface
```aidl
package com.example.myservice;

import com.example.myservice.MyData;
import com.example.myservice.IMyCallback;

@VintfStability                          // For HAL interfaces
interface IMyService {
    const int VERSION = 1;
    const String TAG = "MyService";

    // Synchronous method
    MyData getData(int id);

    // Direction tags: in (default), out, inout
    void processData(in MyData input, out MyData output);

    // Async callback registration
    void registerCallback(IMyCallback callback);
    void unregisterCallback(IMyCallback callback);

    // Nullable
    @nullable MyData findData(int id);
}
```

### Callback Interface
```aidl
package com.example.myservice;

interface IMyCallback {
    oneway void onDataChanged(in MyData data);
    oneway void onError(int errorCode, String message);
}
```

### Parcelable
```aidl
package com.example.myservice;

@VintfStability
parcelable MyData {
    int id;
    String name;
    float[] values;
    @nullable String description;
    long timestamp;
    ParcelFileDescriptor sharedMemory;   // For large data
}
```

### Enum
```aidl
@VintfStability
@Backing(type="int")
enum Status {
    OK = 0,
    ERROR = 1,
    NOT_FOUND = 2,
    PERMISSION_DENIED = 3,
}
```

### Union
```aidl
@VintfStability
union PropertyValue {
    int intValue;
    float floatValue;
    String stringValue;
    byte[] byteArray;
}
```

### Annotations Summary
| Annotation | Purpose |
|-----------|---------|
| `@VintfStability` | Marks interface as ABI-stable for HALs |
| `@nullable` | Parameter/return can be null |
| `@utf8InCpp` | String encoded as UTF-8 in C++ (default UTF-16) |
| `@Hide` | Hidden from SDK docs |
| `@Enforce` | Build-time permission enforcement |
| `@FixedSize` | Fixed-size parcelable (SharedMemory compatible) |
| `@Backing(type="int")` | Enum backing type |
| `@JavaPassthrough(annotation="...")` | Pass through Java annotation |

### Direction Tags
| Tag | Data Flow | Allocation | Use Case |
|-----|-----------|------------|----------|
| `in` | Caller → Callee | Caller allocates | Input parameters (default) |
| `out` | Callee → Caller | Callee allocates | Output-only buffers |
| `inout` | Both directions | Caller allocates, callee modifies | Modify-in-place |

---

## 3. BUILD SYSTEM (Android.bp)

### AIDL Interface Module
```json
aidl_interface {
    name: "android.hardware.automotive.vehicle",
    vendor_available: true,
    stability: "vintf",
    srcs: ["android/hardware/automotive/vehicle/*.aidl"],
    imports: ["android.hardware.automotive.vehicle.property"],
    frozen: true,
    backend: {
        java: { enabled: true },
        cpp: { enabled: true },
        ndk: { enabled: true },
        rust: { enabled: true },
    },
    versions_with_info: [
        { version: "1", imports: [] },
        { version: "2", imports: [] },
    ],
}
```

### Service Binary
```json
cc_binary {
    name: "android.hardware.automotive.vehicle-service",
    vendor: true,
    relative_install_path: "hw",
    srcs: ["VehicleHal.cpp", "main.cpp"],
    shared_libs: [
        "libbase",
        "libbinder_ndk",
        "android.hardware.automotive.vehicle-V2-ndk",
    ],
    init_rc: ["vehicle-hal-default.rc"],
    vintf_fragments: ["vehicle-hal-default.xml"],
}
```

---

## 4. KEY APIs

### Java (Client)
```java
// Get service
IBinder binder = ServiceManager.getService("my_service");
IMyService service = IMyService.Stub.asInterface(binder);

// Wait for service (blocking)
IBinder binder = ServiceManager.waitForService("my_service");

// Call method
MyData result = service.getData(42);

// Death notification
service.asBinder().linkToDeath(() -> {
    Log.e(TAG, "Service died!");
    reconnect();
}, 0);

// Security check (in service implementation)
int callerUid = Binder.getCallingUid();
enforceCallingPermission("android.permission.MY_PERM", "Need MY_PERM");
```

### Java (Server)
```java
public class MyService extends IMyService.Stub {
    @Override
    public MyData getData(int id) {
        // Check permission
        enforceCallingPermission("com.example.permission.READ", null);
        // Implementation
        return new MyData(id, "name", new float[]{1.0f});
    }
}

// Register with ServiceManager
ServiceManager.addService("my_service", new MyService());

// In Application/Service
public class MyBoundService extends Service {
    private final IMyService.Stub mBinder = new MyService();
    @Override
    public IBinder onBind(Intent intent) { return mBinder; }
}
```

### C++ NDK Backend (Client)
```cpp
#include <aidl/android/hardware/automotive/vehicle/IVehicle.h>
using aidl::android::hardware::automotive::vehicle::IVehicle;

// Get service
std::shared_ptr<IVehicle> vehicle;
auto status = AServiceManager_waitForService("android.hardware.automotive.vehicle.IVehicle/default");
auto binder = ndk::SpAIBinder(status);
vehicle = IVehicle::fromBinder(binder);

// Call method
std::vector<GetValueResult> results;
auto aidlStatus = vehicle->getValues(callback, requests);
if (!aidlStatus.isOk()) {
    LOG(ERROR) << "getValues failed: " << aidlStatus.getMessage();
}

// Death notification
class MyDeathRecipient : public AIBinder_DeathRecipient {
    static void onDeath(void* cookie) {
        LOG(ERROR) << "Vehicle HAL died!";
    }
};
AIBinder_linkToDeath(vehicle->asBinder().get(), deathRecipient, nullptr);
```

### C++ NDK Backend (Server)
```cpp
#include <aidl/android/hardware/automotive/vehicle/BnVehicle.h>
using aidl::android::hardware::automotive::vehicle::BnVehicle;

class VehicleHal : public BnVehicle {
    ndk::ScopedAStatus getValues(
        const std::shared_ptr<IVehicleCallback>& callback,
        const GetValueRequests& requests) override {
        // Implementation
        return ndk::ScopedAStatus::ok();
    }
};

// main.cpp
int main() {
    ABinderProcess_setThreadPoolMaxThreadCount(8);
    auto hal = ndk::SharedRefBase::make<VehicleHal>();
    auto binder = hal->asBinder();
    AServiceManager_addService(binder.get(),
        "android.hardware.automotive.vehicle.IVehicle/default");
    ABinderProcess_joinThreadPool();
    return 0;
}
```

### C++ Platform libbinder (Legacy)
```cpp
#include <binder/IServiceManager.h>
#include <binder/ProcessState.h>

// Client
sp<IServiceManager> sm = defaultServiceManager();
sp<IBinder> binder = sm->getService(String16("my_service"));
sp<IMyService> service = interface_cast<IMyService>(binder);

// Server
sp<ProcessState> ps = ProcessState::self();
ps->setThreadPoolMaxThreadCount(16);
defaultServiceManager()->addService(String16("my_service"), new MyService());
ps->startThreadPool();
IPCThreadState::self()->joinThreadPool();
```

---

## 5. BINDER COMMANDS (BC_ / BR_)

### BC_ Commands (User → Kernel)
| Command | Purpose |
|---------|---------|
| `BC_TRANSACTION` | Send transaction to target |
| `BC_REPLY` | Reply to received transaction |
| `BC_FREE_BUFFER` | Free processed transaction buffer |
| `BC_ACQUIRE` | Increment strong ref on handle |
| `BC_RELEASE` | Decrement strong ref on handle |
| `BC_INCREFS` | Increment weak ref on handle |
| `BC_DECREFS` | Decrement weak ref on handle |
| `BC_ENTER_LOOPER` | Main thread entering event loop |
| `BC_REGISTER_LOOPER` | Spawned thread entering event loop |
| `BC_EXIT_LOOPER` | Thread leaving event loop |
| `BC_REQUEST_DEATH_NOTIFICATION` | Register for death notification |
| `BC_CLEAR_DEATH_NOTIFICATION` | Unregister death notification |

### BR_ Commands (Kernel → User)
| Command | Purpose |
|---------|---------|
| `BR_TRANSACTION` | Incoming transaction to process |
| `BR_REPLY` | Reply to your transaction |
| `BR_TRANSACTION_COMPLETE` | Kernel ACK of BC_TRANSACTION |
| `BR_DEAD_BINDER` | Target Binder has died |
| `BR_ACQUIRE` | Kernel requesting strong ref on local obj |
| `BR_RELEASE` | Kernel dropping strong ref |
| `BR_INCREFS` | Kernel requesting weak ref |
| `BR_DECREFS` | Kernel dropping weak ref |
| `BR_SPAWN_LOOPER` | Thread pool needs more threads |
| `BR_NOOP` | No-op (filler) |
| `BR_DEAD_REPLY` | Target died before replying |
| `BR_FAILED_REPLY` | Transaction failed (buffer full, etc.) |
| `BR_ERROR` | General error |

---

## 6. PARCEL METHODS (Java)

### Writing
| Method | Data Type |
|--------|-----------|
| `writeInt(int)` | 32-bit integer |
| `writeLong(long)` | 64-bit integer |
| `writeFloat(float)` | 32-bit float |
| `writeDouble(double)` | 64-bit float |
| `writeString(String)` | UTF-16 string |
| `writeByte(byte)` | 8-bit integer |
| `writeByteArray(byte[])` | Byte array |
| `writeIntArray(int[])` | Int array |
| `writeParcelable(Parcelable, int)` | Parcelable object |
| `writeStrongBinder(IBinder)` | Binder reference |
| `writeFileDescriptor(FileDescriptor)` | File descriptor |
| `writeTypedList(List<T>)` | List of Parcelable |
| `writeBundle(Bundle)` | Bundle |
| `writeBoolean(boolean)` | Boolean (as int) |

### Reading (must match write order exactly)
| Method | Returns |
|--------|---------|
| `readInt()` | int |
| `readLong()` | long |
| `readFloat()` | float |
| `readDouble()` | double |
| `readString()` | String |
| `readByteArray(byte[])` | void (fills array) |
| `readStrongBinder()` | IBinder |
| `readFileDescriptor()` | ParcelFileDescriptor |
| `readTypedList(list, creator)` | void (fills list) |
| `readBundle()` | Bundle |
| `readException()` | void (throws if exception encoded) |

---

## 7. SELINUX RULES FOR BINDER

### Common Policy Statements
```te
# Allow process to use binder device
binder_use(my_domain)

# Allow process to use hwbinder device
hwbinder_use(my_hal_domain)

# Allow process to call target via binder
binder_call(source_domain, target_domain)

# Allow process to transfer Binder objects
allow source_domain target_domain:binder { call transfer };

# Allow service registration
allow my_hal_domain hal_vehicle_hwservice:hwservice_manager { add find };

# Allow finding a service
allow my_app_domain vehicle_service:service_manager find;

# Set context manager (ServiceManager only)
allow servicemanager self:binder set_context_mgr;
```

### Binder Permission Classes
| Permission | Meaning |
|-----------|---------|
| `call` | Make Binder transaction to target |
| `transfer` | Transfer Binder reference to target |
| `set_context_mgr` | Become ServiceManager |

### Service Type Declarations
```te
# Declare service type
type my_service, service_manager_type;

# Associate service name with type
# In service_contexts file:
my_service_name                   u:object_r:my_service:s0
```

---

## 8. THREAD POOL CONFIGURATION

### Java
```java
// Not directly configurable in Java framework services
// System services get pool size from ProcessState (native)
```

### C++ (Platform libbinder)
```cpp
ProcessState::self()->setThreadPoolMaxThreadCount(16);
ProcessState::self()->startThreadPool();
IPCThreadState::self()->joinThreadPool();
```

### C++ (NDK)
```cpp
ABinderProcess_setThreadPoolMaxThreadCount(16);
ABinderProcess_startThreadPool();
ABinderProcess_joinThreadPool();
```

### init.rc (HAL service)
```rc
service vendor.vehicle-hal /vendor/bin/hw/android.hardware.automotive.vehicle-service
    class hal
    user vehicle_network
    group vehicle_network inet
    capabilities NET_ADMIN
    # Thread pool managed in service's main()
```

---

## 9. COMMON ERROR CODES & TROUBLESHOOTING

### Status Codes
| Code | Constant | Meaning |
|------|----------|---------|
| 0 | `OK` | Success |
| -1 | `UNKNOWN_ERROR` | Generic failure |
| -2 | `NO_MEMORY` | Allocation failed |
| -11 | `WOULD_BLOCK` | Non-blocking op would block |
| -22 | `BAD_VALUE` | Invalid parameter |
| -32 | `DEAD_OBJECT` | Target process died |
| -38 | `INVALID_OPERATION` | Operation not valid in current state |
| -74 | `UNKNOWN_TRANSACTION` | Method code not recognized |
| -EACCES | `PERMISSION_DENIED` | Security check failed |

### Java Exception Types in Binder
| Exception Code | Exception | Cause |
|---------------|-----------|-------|
| -1 | SecurityException | Permission denied |
| -2 | BadParcelableException | Parcel format error |
| -3 | IllegalArgumentException | Invalid argument |
| -4 | NullPointerException | Null parameter |
| -5 | IllegalStateException | Invalid state |
| -6 | NetworkOnMainThreadException | Network from main thread |
| -8 | UnsupportedOperationException | Not implemented |
| -129 | ServiceSpecificException | Service-defined error |

### Common Issues & Fixes

| Symptom | Cause | Fix |
|---------|-------|-----|
| TransactionTooLargeException | Data > buffer limit | Use SharedMemory/FD |
| DeadObjectException | Server crashed | linkToDeath + reconnect |
| SecurityException | Missing permission | Add to manifest + request |
| Service not found (null) | Not yet registered | waitForService() or retry |
| ANR on main thread | Binder call blocks >5s | Move to background thread |
| avc: denied | SELinux policy missing | Add allow rule to .te file |
| Thread pool exhausted | All threads busy | Increase pool / reduce blocking |
| binder_alloc: <PID> ... failed | Buffer full | Free buffers (BC_FREE_BUFFER) |

---

## 10. VEHICLE HAL PROPERTY FORMAT

### Property ID Encoding (32-bit)
```
Bits [31-28]: VehiclePropertyGroup
Bits [27-24]: VehicleAreaType  
Bits [23-20]: VehiclePropertyType
Bits [19-0]:  Property number (unique within group)
```

### VehiclePropertyGroup
| Value | Name | Description |
|-------|------|-------------|
| 0x0 | SYSTEM | AOSP-defined properties |
| 0x1 | VENDOR | Vendor-specific properties |

### VehicleAreaType
| Value | Name | Description |
|-------|------|-------------|
| 0x0 | GLOBAL | Whole vehicle |
| 0x1 | WINDOW | Per-window |
| 0x2 | MIRROR | Per-mirror |
| 0x3 | SEAT | Per-seat |
| 0x4 | DOOR | Per-door |
| 0x5 | WHEEL | Per-wheel |

### VehiclePropertyType
| Value | Name | Size |
|-------|------|------|
| 0x0 | STRING | Variable |
| 0x1 | BOOLEAN | 4 bytes |
| 0x2 | INT32 | 4 bytes |
| 0x3 | INT32_VEC | Variable |
| 0x4 | INT64 | 8 bytes |
| 0x5 | INT64_VEC | Variable |
| 0x6 | FLOAT | 4 bytes |
| 0x7 | FLOAT_VEC | Variable |
| 0x8 | BYTES | Variable |
| 0xe | MIXED | Variable |

### Common Vehicle Properties
| Property | ID | Type | Area |
|----------|-----|------|------|
| PERF_VEHICLE_SPEED | 0x02060200 | FLOAT | GLOBAL |
| GEAR_SELECTION | 0x02040400 | INT32 | GLOBAL |
| CURRENT_GEAR | 0x02040401 | INT32 | GLOBAL |
| ENGINE_RPM | 0x02060300 | FLOAT | GLOBAL |
| EV_BATTERY_LEVEL | 0x02060309 | FLOAT | GLOBAL |
| HVAC_TEMPERATURE_SET | 0x02030503 | FLOAT | SEAT |
| HVAC_FAN_SPEED | 0x02030502 | INT32 | SEAT |
| DOOR_LOCK | 0x02010B00 | BOOLEAN | DOOR |
| TIRE_PRESSURE | 0x02060700 | FLOAT | WHEEL |
| NIGHT_MODE | 0x02010900 | BOOLEAN | GLOBAL |
| TURN_SIGNAL_STATE | 0x02040402 | INT32 | GLOBAL |

---

## 11. VINTF MANIFEST & COMPATIBILITY

### Device Manifest (AIDL HAL)
```xml
<!-- /vendor/etc/vintf/manifest.xml -->
<manifest version="2.0" type="device">
    <hal format="aidl">
        <name>android.hardware.automotive.vehicle</name>
        <version>2</version>
        <fqname>IVehicle/default</fqname>
    </hal>
</manifest>
```

### Framework Compatibility Matrix
```xml
<!-- /system/etc/vintf/compatibility_matrix.xml -->
<compatibility-matrix version="2.0" type="framework">
    <hal format="aidl" optional="false">
        <name>android.hardware.automotive.vehicle</name>
        <version>1-2</version>
        <interface>
            <name>IVehicle</name>
            <instance>default</instance>
        </interface>
    </hal>
</compatibility-matrix>
```

### Check VINTF Compatibility
```bash
adb shell vintf
adb shell /system/bin/vintf --check-compat
```

---

## 12. PERFETTO / SYSTRACE CONFIGURATION

### Perfetto Config for Binder
```protobuf
data_sources {
    config {
        name: "linux.ftrace"
        ftrace_config {
            ftrace_events: "binder/binder_transaction"
            ftrace_events: "binder/binder_transaction_received"
            ftrace_events: "binder/binder_lock"
            ftrace_events: "binder/binder_locked"
            ftrace_events: "binder/binder_unlock"
            ftrace_events: "binder/binder_transaction_alloc_buf"
            buffer_size_kb: 16384
        }
    }
}
duration_ms: 10000
```

### Systrace Command
```bash
# Capture Binder transactions
python systrace.py -o trace.html -t 5 binder_driver binder_lock sched freq

# With app tracing
python systrace.py -o trace.html -t 5 -a com.my.app binder_driver sched
```

### Key Trace Events
| Event | Information |
|-------|-------------|
| `binder_transaction` | From PID/TID, To PID, code, data_size |
| `binder_transaction_received` | Receiver PID/TID processing transaction |
| `binder_transaction_alloc_buf` | Buffer allocation (size, process) |
| `binder_command` | BC_ command issued |
| `binder_return` | BR_ command received |

---

## 13. QUICK DECISION TABLE

| Scenario | Recommended Approach |
|----------|---------------------|
| Simple RPC (get/set) | Standard AIDL synchronous |
| Event notification | oneway callback interface |
| Large data (>100KB) | SharedMemory via ParcelFileDescriptor |
| High-frequency data (>100Hz) | FMQ (Fast Message Queue) |
| Service discovery | ServiceManager getService/waitForService |
| Crash recovery | linkToDeath + reconnection logic |
| Cross-vendor interface | @VintfStability AIDL |
| Vendor-to-vendor only | vndbinder domain |
| Real-time requirement | FMQ + CPU pinning + SCHED_FIFO |
| Batch operations | Array parameters in single call |
| Authorization check | getCallingUid() + enforcePermission() |
| Debug service state | Implement dump() method |
| Performance profiling | Perfetto with binder ftrace events |
| Testing without device | Mock implementation + unit tests |

---

END OF DOCUMENT 06 — CHEATSHEET
