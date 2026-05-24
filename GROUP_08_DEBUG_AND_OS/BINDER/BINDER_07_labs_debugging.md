# ANDROID BINDER & AIDL — LABS & DEBUGGING SCENARIOS
# ════════════════════════════════════════════════════════════════════
# Protocol: Android Binder IPC | Document: 07 of 08
# 8 Progressive Labs + 7 Debug Scenarios
# ════════════════════════════════════════════════════════════════════

---

# PART A: PROGRESSIVE LABS

---

## LAB 1: DEFINE AN AIDL INTERFACE

### Objective
Create a complete AIDL interface for a temperature sensor service.

### Prerequisites
- AOSP source tree or standalone AIDL project
- Android SDK with aidl compiler
- Basic understanding of interface design

### Steps

**Step 1: Create the directory structure**
```bash
mkdir -p com/example/sensor/
```

**Step 2: Define the data type (Parcelable)**
```aidl
// com/example/sensor/SensorReading.aidl
package com.example.sensor;

parcelable SensorReading {
    int sensorId;
    float temperatureCelsius;
    long timestampMs;
    @nullable String sensorName;
}
```

**Step 3: Define the callback interface**
```aidl
// com/example/sensor/ISensorCallback.aidl
package com.example.sensor;

import com.example.sensor.SensorReading;

interface ISensorCallback {
    oneway void onTemperatureChanged(in SensorReading reading);
    oneway void onSensorError(int sensorId, int errorCode);
}
```

**Step 4: Define the main service interface**
```aidl
// com/example/sensor/ISensorService.aidl
package com.example.sensor;

import com.example.sensor.SensorReading;
import com.example.sensor.ISensorCallback;

interface ISensorService {
    const int MAX_SENSORS = 16;
    const int ERROR_NOT_FOUND = -1;
    const int ERROR_PERMISSION = -2;

    // Get current reading (synchronous)
    SensorReading getTemperature(int sensorId);

    // Get all sensor IDs
    int[] getSensorIds();

    // Subscribe to changes
    void registerCallback(ISensorCallback callback, int sensorId, float thresholdDelta);
    void unregisterCallback(ISensorCallback callback);

    // Configuration
    void setSampleRate(int sensorId, int rateHz);
}
```

**Step 5: Compile the AIDL**
```bash
# Using aidl compiler directly
aidl -I . com/example/sensor/SensorReading.aidl
aidl -I . com/example/sensor/ISensorCallback.aidl
aidl -I . com/example/sensor/ISensorService.aidl

# In Android.bp:
# aidl_interface {
#     name: "com.example.sensor",
#     srcs: ["com/example/sensor/*.aidl"],
#     backend: { java: { enabled: true }, ndk: { enabled: true } },
# }
```

### Verification
- [ ] AIDL compiles without errors
- [ ] Generated Stub and Proxy classes exist
- [ ] Parcelable has proper serialization code
- [ ] Callback is marked oneway
- [ ] Constants are accessible in generated code

### Key Lessons
- Always use `in` direction for Parcelable parameters
- Use `oneway` for all callback methods (prevents deadlock)
- Define error constants in the interface (not magic numbers)
- Keep interface focused (single responsibility)

---

## LAB 2: IMPLEMENT THE SERVER (SERVICE)

### Objective
Implement the ISensorService server in Java and register with ServiceManager.

### Steps

**Step 1: Implement the service**
```java
package com.example.sensor;

import android.os.RemoteCallbackList;
import android.os.IBinder;
import android.os.Binder;
import android.util.Log;
import java.util.concurrent.*;

public class SensorServiceImpl extends ISensorService.Stub {
    private static final String TAG = "SensorService";

    // Thread-safe callback management
    private final RemoteCallbackList<ISensorCallback> mCallbacks =
            new RemoteCallbackList<>();
    private final ConcurrentHashMap<Integer, SensorReading> mLastReadings =
            new ConcurrentHashMap<>();
    private final ScheduledExecutorService mExecutor =
            Executors.newScheduledThreadPool(2);

    // Sensor IDs (simulated)
    private static final int[] SENSOR_IDS = {1, 2, 3, 4};

    @Override
    public SensorReading getTemperature(int sensorId) {
        // Security check
        int callingUid = Binder.getCallingUid();
        Log.d(TAG, "getTemperature called by UID=" + callingUid
                + " sensorId=" + sensorId);

        // Validate input
        if (!isValidSensor(sensorId)) {
            throw new IllegalArgumentException("Invalid sensor ID: " + sensorId);
        }

        // Return cached reading (or poll hardware in real impl)
        SensorReading reading = mLastReadings.get(sensorId);
        if (reading == null) {
            reading = pollHardware(sensorId);
            mLastReadings.put(sensorId, reading);
        }
        return reading;
    }

    @Override
    public int[] getSensorIds() {
        return SENSOR_IDS.clone();
    }

    @Override
    public void registerCallback(ISensorCallback callback,
                                  int sensorId, float thresholdDelta) {
        if (callback == null) {
            throw new IllegalArgumentException("Callback cannot be null");
        }
        mCallbacks.register(callback);
        Log.d(TAG, "Callback registered for sensor " + sensorId);

        // Start monitoring this sensor
        startMonitoring(sensorId, thresholdDelta);
    }

    @Override
    public void unregisterCallback(ISensorCallback callback) {
        if (callback != null) {
            mCallbacks.unregister(callback);
        }
    }

    @Override
    public void setSampleRate(int sensorId, int rateHz) {
        // Validate
        if (rateHz < 1 || rateHz > 100) {
            throw new IllegalArgumentException("Rate must be 1-100 Hz");
        }
        Log.d(TAG, "Set sample rate: sensor=" + sensorId + " rate=" + rateHz);
        // Reconfigure hardware polling interval
    }

    // --- Internal methods ---

    private void startMonitoring(int sensorId, float threshold) {
        mExecutor.scheduleAtFixedRate(() -> {
            SensorReading newReading = pollHardware(sensorId);
            SensorReading oldReading = mLastReadings.put(sensorId, newReading);

            if (oldReading == null ||
                Math.abs(newReading.temperatureCelsius - oldReading.temperatureCelsius)
                    >= threshold) {
                notifyCallbacks(newReading);
            }
        }, 0, 1000, TimeUnit.MILLISECONDS);
    }

    private void notifyCallbacks(SensorReading reading) {
        int count = mCallbacks.beginBroadcast();
        for (int i = 0; i < count; i++) {
            try {
                mCallbacks.getBroadcastItem(i).onTemperatureChanged(reading);
            } catch (Exception e) {
                Log.w(TAG, "Failed to notify callback: " + e);
            }
        }
        mCallbacks.finishBroadcast();
    }

    private SensorReading pollHardware(int sensorId) {
        // Simulated hardware read
        SensorReading reading = new SensorReading();
        reading.sensorId = sensorId;
        reading.temperatureCelsius = 20.0f + (float)(Math.random() * 10);
        reading.timestampMs = System.currentTimeMillis();
        reading.sensorName = "Sensor_" + sensorId;
        return reading;
    }

    private boolean isValidSensor(int sensorId) {
        for (int id : SENSOR_IDS) {
            if (id == sensorId) return true;
        }
        return false;
    }
}
```

**Step 2: Register the service**
```java
// In system server or standalone service process
import android.os.ServiceManager;

public class SensorServiceMain {
    public static void main(String[] args) {
        SensorServiceImpl service = new SensorServiceImpl();
        ServiceManager.addService("example_sensor", service);
        Log.i("SensorService", "Registered with ServiceManager");

        // Keep the process alive
        android.os.Looper.prepareMainLooper();
        android.os.Looper.loop();
    }
}
```

### Verification
- [ ] Service compiles and starts without crash
- [ ] `adb shell service check example_sensor` returns "found"
- [ ] `adb shell service list | grep sensor` shows the service
- [ ] `dumpsys example_sensor` doesn't crash (add dump() for bonus)

---

## LAB 3: IMPLEMENT THE CLIENT

### Objective
Build a client that connects to the sensor service, calls methods, and handles callbacks.

### Steps

**Step 1: Create client class**
```java
package com.example.sensor.client;

import android.os.IBinder;
import android.os.ServiceManager;
import android.os.RemoteException;
import android.util.Log;

import com.example.sensor.ISensorService;
import com.example.sensor.ISensorCallback;
import com.example.sensor.SensorReading;

public class SensorClient {
    private static final String TAG = "SensorClient";
    private ISensorService mService;
    private IBinder.DeathRecipient mDeathRecipient;

    public boolean connect() {
        IBinder binder = ServiceManager.getService("example_sensor");
        if (binder == null) {
            Log.e(TAG, "Service not found! Is it running?");
            return false;
        }

        mService = ISensorService.Stub.asInterface(binder);

        // Register death notification
        mDeathRecipient = () -> {
            Log.e(TAG, "Sensor service died! Attempting reconnect...");
            mService = null;
            reconnectWithBackoff();
        };

        try {
            binder.linkToDeath(mDeathRecipient, 0);
        } catch (RemoteException e) {
            Log.e(TAG, "linkToDeath failed: " + e);
            return false;
        }

        return true;
    }

    public SensorReading getTemperature(int sensorId) {
        if (mService == null) {
            Log.e(TAG, "Not connected!");
            return null;
        }
        try {
            return mService.getTemperature(sensorId);
        } catch (RemoteException e) {
            Log.e(TAG, "RPC failed: " + e);
            return null;
        }
    }

    public void subscribeToChanges(int sensorId, float threshold) {
        if (mService == null) return;
        try {
            mService.registerCallback(mCallback, sensorId, threshold);
        } catch (RemoteException e) {
            Log.e(TAG, "Failed to register callback: " + e);
        }
    }

    private final ISensorCallback mCallback = new ISensorCallback.Stub() {
        @Override
        public void onTemperatureChanged(SensorReading reading) {
            Log.i(TAG, String.format("Temperature update: sensor=%d temp=%.1f°C time=%d",
                    reading.sensorId, reading.temperatureCelsius, reading.timestampMs));
            // Update UI or process data
        }

        @Override
        public void onSensorError(int sensorId, int errorCode) {
            Log.e(TAG, "Sensor error: id=" + sensorId + " code=" + errorCode);
        }
    };

    private void reconnectWithBackoff() {
        new Thread(() -> {
            int delay = 1000;
            for (int attempt = 0; attempt < 10; attempt++) {
                try { Thread.sleep(delay); } catch (InterruptedException e) { return; }
                if (connect()) {
                    Log.i(TAG, "Reconnected after " + (attempt + 1) + " attempts");
                    // Re-subscribe
                    subscribeToChanges(1, 0.5f);
                    return;
                }
                delay = Math.min(delay * 2, 30000); // Exponential backoff
            }
            Log.e(TAG, "Failed to reconnect after 10 attempts");
        }).start();
    }

    public void disconnect() {
        if (mService != null) {
            try {
                mService.unregisterCallback(mCallback);
                mService.asBinder().unlinkToDeath(mDeathRecipient, 0);
            } catch (RemoteException e) {
                // Service already dead
            }
            mService = null;
        }
    }
}
```

**Step 2: Usage in Activity/Application**
```java
public class SensorActivity extends Activity {
    private SensorClient mClient = new SensorClient();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        if (mClient.connect()) {
            // One-shot read
            SensorReading r = mClient.getTemperature(1);
            Log.i("Activity", "Current temp: " + r.temperatureCelsius);

            // Subscribe for updates
            mClient.subscribeToChanges(1, 0.5f);
        }
    }

    @Override
    protected void onDestroy() {
        mClient.disconnect();
        super.onDestroy();
    }
}
```

### Verification
- [ ] Client connects to service successfully
- [ ] `getTemperature()` returns valid data
- [ ] Callbacks fire when temperature changes
- [ ] Death recipient triggers on service kill
- [ ] Reconnection works after service restart

---

## LAB 4: IMPLEMENT CALLBACKS WITH DEATH HANDLING

### Objective
Build a robust callback system with proper death handling on both client and server.

### Steps

**Step 1: Enhanced server-side callback management**
```java
public class RobustCallbackManager {
    private static final String TAG = "CallbackMgr";

    // Per-callback metadata
    private static class CallbackRecord {
        final ISensorCallback callback;
        final IBinder binder;
        final int sensorId;
        final float threshold;
        final int callerUid;
        final IBinder.DeathRecipient deathRecipient;

        CallbackRecord(ISensorCallback cb, int sensor, float thresh, int uid,
                       IBinder.DeathRecipient dr) {
            this.callback = cb;
            this.binder = cb.asBinder();
            this.sensorId = sensor;
            this.threshold = thresh;
            this.callerUid = uid;
            this.deathRecipient = dr;
        }
    }

    private final ConcurrentHashMap<IBinder, CallbackRecord> mRecords =
            new ConcurrentHashMap<>();

    public void register(ISensorCallback callback, int sensorId,
                         float threshold, int callerUid) {
        IBinder binder = callback.asBinder();

        // Create death recipient for this client
        IBinder.DeathRecipient deathRecipient = () -> {
            Log.w(TAG, "Client died (UID=" + callerUid + "), removing callback");
            unregister(callback);
        };

        try {
            binder.linkToDeath(deathRecipient, 0);
        } catch (RemoteException e) {
            Log.e(TAG, "Client already dead, not registering");
            return;
        }

        CallbackRecord record = new CallbackRecord(
                callback, sensorId, threshold, callerUid, deathRecipient);
        mRecords.put(binder, record);
        Log.d(TAG, "Registered callback: UID=" + callerUid + " sensor=" + sensorId);
    }

    public void unregister(ISensorCallback callback) {
        IBinder binder = callback.asBinder();
        CallbackRecord record = mRecords.remove(binder);
        if (record != null) {
            try {
                binder.unlinkToDeath(record.deathRecipient, 0);
            } catch (Exception e) {
                // Already dead, that's fine
            }
            Log.d(TAG, "Unregistered callback: UID=" + record.callerUid);
        }
    }

    public void broadcast(SensorReading reading) {
        for (CallbackRecord record : mRecords.values()) {
            if (record.sensorId == reading.sensorId) {
                try {
                    record.callback.onTemperatureChanged(reading);
                } catch (RemoteException e) {
                    Log.w(TAG, "Failed to deliver to UID=" + record.callerUid
                            + ", removing");
                    mRecords.remove(record.binder);
                }
            }
        }
    }

    public int getActiveCallbackCount() {
        return mRecords.size();
    }
}
```

**Step 2: Test death handling**
```bash
# Start service
adb shell am start-service com.example.sensor/.SensorService

# Start client (subscribes to callbacks)
adb shell am start com.example.sensor.client/.SensorActivity

# Kill client process to trigger death notification
adb shell kill $(adb shell pidof com.example.sensor.client)

# Verify server logs show client death handling
adb logcat | grep "CallbackMgr.*Client died"

# Verify no leaked callbacks
adb shell dumpsys example_sensor | grep "callback_count"
```

### Verification
- [ ] Server detects client death within ~1 second
- [ ] Dead callbacks are removed from list
- [ ] No memory leaks after repeated connect/disconnect
- [ ] Server doesn't crash when broadcasting to dead client
- [ ] Multiple clients can register and unregister independently

---

## LAB 5: NATIVE C++ AIDL HAL IMPLEMENTATION

### Objective
Implement a Vehicle HAL subset using AIDL NDK backend in C++.

### Steps

**Step 1: AIDL interface (simplified Vehicle HAL)**
```aidl
// hardware/interfaces/automotive/vehicle/aidl/
package android.hardware.automotive.vehicle;

@VintfStability
interface IVehicle {
    void getValues(IVehicleCallback callback, in GetValueRequests requests);
    void setValues(IVehicleCallback callback, in SetValueRequests requests);
    void subscribe(IVehicleCallback callback, in SubscribeOptions[] options,
                   int maxSharedMemoryFileCount);
    void unsubscribe(IVehicleCallback callback, in int[] propIds);
}
```

**Step 2: Implement the HAL (NDK backend)**
```cpp
// VehicleHal.h
#pragma once
#include <aidl/android/hardware/automotive/vehicle/BnVehicle.h>
#include <mutex>
#include <unordered_map>
#include <vector>
#include <thread>

namespace aidl::android::hardware::automotive::vehicle {

class VehicleHal : public BnVehicle {
public:
    VehicleHal();
    ~VehicleHal() override;

    ndk::ScopedAStatus getValues(
        const std::shared_ptr<IVehicleCallback>& callback,
        const GetValueRequests& requests) override;

    ndk::ScopedAStatus setValues(
        const std::shared_ptr<IVehicleCallback>& callback,
        const SetValueRequests& requests) override;

    ndk::ScopedAStatus subscribe(
        const std::shared_ptr<IVehicleCallback>& callback,
        const std::vector<SubscribeOptions>& options,
        int32_t maxSharedMemoryFileCount) override;

    ndk::ScopedAStatus unsubscribe(
        const std::shared_ptr<IVehicleCallback>& callback,
        const std::vector<int32_t>& propIds) override;

private:
    struct Subscriber {
        std::shared_ptr<IVehicleCallback> callback;
        ndk::ScopedAIBinder_DeathRecipient deathRecipient;
    };

    std::mutex mLock;
    std::unordered_map<int32_t, VehiclePropValue> mPropertyStore;
    std::vector<Subscriber> mSubscribers;
    std::atomic<bool> mRunning{true};
    std::thread mPollingThread;

    void initializeProperties();
    void pollingLoop();
    void notifySubscribers(const VehiclePropValue& value);
    static void onBinderDied(void* cookie);
};

}  // namespace
```

**Step 3: Implementation**
```cpp
// VehicleHal.cpp
#include "VehicleHal.h"
#include <android-base/logging.h>
#include <chrono>

namespace aidl::android::hardware::automotive::vehicle {

VehicleHal::VehicleHal() {
    initializeProperties();
    mPollingThread = std::thread(&VehicleHal::pollingLoop, this);
}

VehicleHal::~VehicleHal() {
    mRunning = false;
    if (mPollingThread.joinable()) mPollingThread.join();
}

ndk::ScopedAStatus VehicleHal::getValues(
        const std::shared_ptr<IVehicleCallback>& callback,
        const GetValueRequests& requests) {
    GetValueResults results;
    results.payloads.resize(requests.payloads.size());

    std::lock_guard<std::mutex> lock(mLock);
    for (size_t i = 0; i < requests.payloads.size(); i++) {
        int32_t propId = requests.payloads[i].prop;
        auto it = mPropertyStore.find(propId);
        if (it != mPropertyStore.end()) {
            results.payloads[i].status = StatusCode::OK;
            results.payloads[i].prop = it->second;
        } else {
            results.payloads[i].status = StatusCode::NOT_AVAILABLE;
        }
    }

    // Async reply via callback
    callback->onGetValues(results);
    return ndk::ScopedAStatus::ok();
}

ndk::ScopedAStatus VehicleHal::setValues(
        const std::shared_ptr<IVehicleCallback>& callback,
        const SetValueRequests& requests) {
    SetValueResults results;
    results.payloads.resize(requests.payloads.size());

    std::lock_guard<std::mutex> lock(mLock);
    for (size_t i = 0; i < requests.payloads.size(); i++) {
        auto& prop = requests.payloads[i];
        mPropertyStore[prop.prop] = prop;
        results.payloads[i].status = StatusCode::OK;
        // Notify subscribers of change
        notifySubscribers(prop);
    }

    callback->onSetValues(results);
    return ndk::ScopedAStatus::ok();
}

ndk::ScopedAStatus VehicleHal::subscribe(
        const std::shared_ptr<IVehicleCallback>& callback,
        const std::vector<SubscribeOptions>& options,
        int32_t /*maxSharedMemoryFileCount*/) {
    // Set up death notification
    auto deathRecipient = ndk::ScopedAIBinder_DeathRecipient(
        AIBinder_DeathRecipient_new(onBinderDied));
    AIBinder_linkToDeath(callback->asBinder().get(),
                         deathRecipient.get(), this);

    std::lock_guard<std::mutex> lock(mLock);
    mSubscribers.push_back({callback, std::move(deathRecipient)});
    LOG(INFO) << "Subscriber added. Total: " << mSubscribers.size();
    return ndk::ScopedAStatus::ok();
}

ndk::ScopedAStatus VehicleHal::unsubscribe(
        const std::shared_ptr<IVehicleCallback>& callback,
        const std::vector<int32_t>& /*propIds*/) {
    std::lock_guard<std::mutex> lock(mLock);
    mSubscribers.erase(
        std::remove_if(mSubscribers.begin(), mSubscribers.end(),
            [&](const Subscriber& s) {
                return s.callback->asBinder() == callback->asBinder();
            }),
        mSubscribers.end());
    return ndk::ScopedAStatus::ok();
}

void VehicleHal::onBinderDied(void* cookie) {
    LOG(WARNING) << "Subscriber died, cleaning up";
    // In production: remove from mSubscribers
}

void VehicleHal::initializeProperties() {
    // Initialize with default vehicle property values
    VehiclePropValue speed;
    speed.prop = 0x02060200;  // PERF_VEHICLE_SPEED
    speed.value.floatValues = {0.0f};
    mPropertyStore[speed.prop] = speed;
}

void VehicleHal::pollingLoop() {
    while (mRunning) {
        // Simulate hardware polling (replace with real CAN/Ethernet read)
        std::this_thread::sleep_for(std::chrono::milliseconds(100));
        // Update speed (simulated)
        std::lock_guard<std::mutex> lock(mLock);
        auto& speed = mPropertyStore[0x02060200];
        speed.value.floatValues[0] += 0.1f;
        speed.timestamp = std::chrono::system_clock::now().time_since_epoch().count();
        notifySubscribers(speed);
    }
}

void VehicleHal::notifySubscribers(const VehiclePropValue& value) {
    VehiclePropValues values;
    values.payloads.push_back(value);
    for (auto& sub : mSubscribers) {
        sub.callback->onPropertyEvent(values, /*sharedMemoryFileCount=*/0);
    }
}

}  // namespace
```

**Step 4: Main entry point**
```cpp
// main.cpp
#include "VehicleHal.h"
#include <android/binder_manager.h>
#include <android/binder_process.h>
#include <android-base/logging.h>

using aidl::android::hardware::automotive::vehicle::VehicleHal;

int main() {
    LOG(INFO) << "Vehicle HAL starting...";

    ABinderProcess_setThreadPoolMaxThreadCount(8);

    auto hal = ndk::SharedRefBase::make<VehicleHal>();
    const std::string instance =
        std::string(VehicleHal::descriptor) + "/default";

    auto status = AServiceManager_addService(hal->asBinder().get(),
                                              instance.c_str());
    CHECK_EQ(status, STATUS_OK) << "Failed to register Vehicle HAL";

    LOG(INFO) << "Vehicle HAL registered: " << instance;
    ABinderProcess_joinThreadPool();
    return 0;
}
```

### Verification
```bash
# Check registration
adb shell service list | grep vehicle

# Check VINTF
adb shell lshal | grep vehicle

# Test with service call
adb shell cmd vehicle_hal_service getprop 0x02060200
```

---

## LAB 6: FAST MESSAGE QUEUE (FMQ) FOR HIGH-FREQUENCY DATA

### Objective
Implement FMQ for streaming 100Hz accelerometer data bypassing Binder.

### Steps

**Step 1: Define FMQ message type**
```cpp
// AccelSample.h
#pragma once
#include <cstdint>

struct AccelSample {
    float x;
    float y;
    float z;
    int64_t timestamp_ns;
} __attribute__((packed));
```

**Step 2: Server-side FMQ setup**
```cpp
#include <fmq/AidlMessageQueue.h>
#include <fmq/EventFlag.h>

using AidlAccelQueue = AidlMessageQueue<AccelSample, SynchronizedReadWrite>;

class AccelHal {
    std::unique_ptr<AidlAccelQueue> mQueue;
    std::atomic<bool> mRunning{true};
    std::thread mProducerThread;

public:
    // Called via Binder to set up the FMQ
    ndk::ScopedAStatus configureFmq(
            MQDescriptor<AccelSample, SynchronizedReadWrite>* descriptor) {
        // Create queue with 1024 entries (enough for 10s at 100Hz)
        mQueue = std::make_unique<AidlAccelQueue>(1024, /*configureEventFlag=*/true);
        if (!mQueue->isValid()) {
            return ndk::ScopedAStatus::fromExceptionCode(EX_ILLEGAL_STATE);
        }

        // Return descriptor to client (via Binder — small, one-time)
        *descriptor = mQueue->dupeDesc();

        // Start producer thread
        mProducerThread = std::thread(&AccelHal::produceData, this);
        return ndk::ScopedAStatus::ok();
    }

private:
    void produceData() {
        EventFlag* efGroup = nullptr;
        EventFlag::createEventFlag(mQueue->getEventFlagWord(), &efGroup);

        while (mRunning) {
            AccelSample sample;
            sample.x = readHardwareX();  // Read from actual sensor
            sample.y = readHardwareY();
            sample.z = readHardwareZ();
            sample.timestamp_ns = systemTime(CLOCK_MONOTONIC);

            // Write to FMQ (no Binder, no syscall for the data!)
            if (!mQueue->write(&sample, 1)) {
                // Queue full — reader is too slow
                LOG(WARNING) << "FMQ overflow, dropping sample";
            }

            // Signal reader (optional — reader can also poll)
            if (efGroup) {
                efGroup->wake(1 /* event mask */);
            }

            // 100Hz = 10ms interval
            std::this_thread::sleep_for(std::chrono::milliseconds(10));
        }

        if (efGroup) EventFlag::deleteEventFlag(&efGroup);
    }
};
```

**Step 3: Client-side FMQ consumer**
```cpp
class AccelClient {
    std::unique_ptr<AidlAccelQueue> mQueue;
    std::thread mConsumerThread;
    std::atomic<bool> mRunning{true};

public:
    bool setup(std::shared_ptr<IAccelHal> service) {
        // Get FMQ descriptor via Binder (one-time setup)
        MQDescriptor<AccelSample, SynchronizedReadWrite> descriptor;
        auto status = service->configureFmq(&descriptor);
        if (!status.isOk()) return false;

        // Create client-side queue from descriptor
        mQueue = std::make_unique<AidlAccelQueue>(descriptor);
        if (!mQueue->isValid()) return false;

        // Start consumer
        mConsumerThread = std::thread(&AccelClient::consumeData, this);
        return true;
    }

private:
    void consumeData() {
        EventFlag* efGroup = nullptr;
        EventFlag::createEventFlag(mQueue->getEventFlagWord(), &efGroup);

        while (mRunning) {
            // Wait for signal (blocks until data available)
            uint32_t efState = 0;
            if (efGroup) {
                efGroup->wait(1 /* event mask */, &efState,
                              500000000 /* 500ms timeout */);
            }

            // Read all available samples
            AccelSample samples[64];
            size_t count = mQueue->availableToRead();
            if (count > 64) count = 64;

            if (count > 0 && mQueue->read(samples, count)) {
                for (size_t i = 0; i < count; i++) {
                    processAccelSample(samples[i]);
                }
            }
        }

        if (efGroup) EventFlag::deleteEventFlag(&efGroup);
    }

    void processAccelSample(const AccelSample& s) {
        // Process accelerometer data
        float magnitude = std::sqrt(s.x*s.x + s.y*s.y + s.z*s.z);
        if (magnitude > 2.0f * 9.81f) {
            LOG(WARNING) << "High-G event detected: " << magnitude << " m/s²";
        }
    }
};
```

### Verification
- [ ] FMQ descriptor transferred via Binder (one-time)
- [ ] Data flows at 100Hz without Binder transactions
- [ ] Latency <5µs per sample (measure with timestamps)
- [ ] No transaction in `/proc/binder/stats` during data streaming
- [ ] Queue overflow handled gracefully

---

## LAB 7: PERFORMANCE PROFILING WITH PERFETTO

### Objective
Capture and analyze Binder performance using Perfetto tracing.

### Steps

**Step 1: Create Perfetto trace config**
```bash
cat > /tmp/binder_trace.pbtxt << 'EOF'
buffers {
    size_kb: 32768
    fill_policy: RING_BUFFER
}

data_sources {
    config {
        name: "linux.ftrace"
        ftrace_config {
            ftrace_events: "binder/binder_transaction"
            ftrace_events: "binder/binder_transaction_received"
            ftrace_events: "binder/binder_transaction_alloc_buf"
            ftrace_events: "binder/binder_lock"
            ftrace_events: "binder/binder_locked"
            ftrace_events: "binder/binder_unlock"
            ftrace_events: "sched/sched_switch"
            ftrace_events: "sched/sched_waking"
            buffer_size_kb: 16384
            drain_period_ms: 250
        }
    }
}

data_sources {
    config {
        name: "linux.process_stats"
        process_stats_config {
            scan_all_processes_on_start: true
            proc_stats_poll_ms: 1000
        }
    }
}

duration_ms: 10000
EOF
```

**Step 2: Capture trace**
```bash
# Push config to device
adb push /tmp/binder_trace.pbtxt /data/local/tmp/

# Start trace
adb shell perfetto --txt -c /data/local/tmp/binder_trace.pbtxt -o /data/local/tmp/trace.perfetto-trace

# (During the 10s capture, exercise your Binder service)
adb shell service call example_sensor 1 i32 1  # Trigger transactions

# Pull trace
adb pull /data/local/tmp/trace.perfetto-trace .
```

**Step 3: Alternative with systrace**
```bash
# Quick systrace capture
python systrace.py -o binder_trace.html -t 5 binder_driver sched -a com.example.sensor

# View in browser
open binder_trace.html
```

**Step 4: Analyze in Perfetto UI**
```
1. Open https://ui.perfetto.dev
2. Load trace file
3. Search for "binder_transaction" events
4. Analyze:
   - Time between binder_transaction and binder_transaction_received = kernel latency
   - Time from received to next BR_REPLY = service processing time
   - Thread wakeup latency (sched_waking → sched_switch)
   - Buffer allocation sizes (binder_transaction_alloc_buf)
```

**Step 5: Programmatic latency measurement**
```cpp
// Add in your Binder service's onTransact:
auto start = std::chrono::steady_clock::now();
// ... process transaction ...
auto end = std::chrono::steady_clock::now();
auto latency_us = std::chrono::duration_cast<std::chrono::microseconds>(end - start).count();
if (latency_us > 1000) {  // >1ms is concerning
    LOG(WARNING) << "Slow transaction: code=" << code
                 << " latency=" << latency_us << "µs";
}
```

### Key Metrics to Extract
| Metric | Target | Alert Threshold |
|--------|--------|----------------|
| Empty transaction RTT | <10µs | >50µs |
| 4KB transaction RTT | <50µs | >200µs |
| Thread wakeup latency | <20µs | >100µs |
| Buffer allocation | <100µs | >500µs |
| Thread pool utilization | <75% | >90% |

---

## LAB 8: SELINUX POLICY FOR BINDER SERVICE

### Objective
Write complete SELinux policy for a new Binder HAL service.

### Steps

**Step 1: Define the service type**
```te
# device/vendor/sepolicy/private/vehicle_hal.te

# Define the domain for Vehicle HAL process
type vehicle_hal, domain;
type vehicle_hal_exec, exec_type, vendor_file_type, file_type;

# Allow init to start this service
init_daemon_domain(vehicle_hal)

# Allow use of hwbinder
hwbinder_use(vehicle_hal)

# Allow this process to register HAL services
add_hwservice(vehicle_hal, hal_vehicle_hwservice)

# Allow Binder communication with CarService
binder_call(vehicle_hal, system_server)
binder_call(system_server, vehicle_hal)

# Allow reading vehicle hardware (CAN bus, etc.)
allow vehicle_hal can_device:chr_file { open read write ioctl };

# Allow shared memory for FMQ
allow vehicle_hal vehicle_hal_tmpfs:file { create read write map };

# Network access (for SOME/IP bridge)
allow vehicle_hal self:udp_socket { create bind read write };
```

**Step 2: Service context**
```te
# device/vendor/sepolicy/private/service_contexts
android.hardware.automotive.vehicle.IVehicle/default u:object_r:hal_vehicle_hwservice:s0
```

**Step 3: File context for binary**
```te
# device/vendor/sepolicy/private/file_contexts
/vendor/bin/hw/android\.hardware\.automotive\.vehicle-service u:object_r:vehicle_hal_exec:s0
```

**Step 4: VINTF hwservice type**
```te
# device/vendor/sepolicy/private/hwservice.te
type hal_vehicle_hwservice, hwservice_manager_type;
```

**Step 5: Client access policy**
```te
# Allow CarService (system_server) to find and call Vehicle HAL
allow system_server hal_vehicle_hwservice:hwservice_manager find;
binder_call(system_server, vehicle_hal)

# Deny apps direct access
neverallow untrusted_app hal_vehicle_hwservice:hwservice_manager find;
neverallow untrusted_app vehicle_hal:binder call;
```

**Step 6: Test policy**
```bash
# Build and flash
m selinux_policy

# Check for denials
adb logcat | grep "avc: denied" | grep vehicle

# Verify service accessible
adb shell cmd vehicle_hal_service get-prop 291504648

# Verify app denial
# (from an app trying to access HAL directly — should fail)
adb logcat | grep "avc: denied.*untrusted_app.*vehicle"
```

### Verification
- [ ] Vehicle HAL starts without SELinux denials
- [ ] CarService can communicate with Vehicle HAL
- [ ] Apps cannot directly access Vehicle HAL
- [ ] `neverallow` rules pass CTS `sepolicy_tests`
- [ ] No permissive mode required

---

# PART B: DEBUG SCENARIOS

---

## SCENARIO 1: TransactionTooLargeException

### Symptoms
```
E/JavaBinder: !!! FAILED BINDER TRANSACTION !!!  (parcel size = 1048892)
E/ActivityThread: App transport error: TransactionTooLargeException
```

### Root Cause Analysis
```bash
# 1. Check which process has buffer issues
adb shell cat /proc/binder/stats | grep "proc " | head -20

# 2. Check per-process buffer usage
adb shell cat /proc/binder/proc/<PID> | grep -A10 "allocated buffers"

# 3. Find large Parcels in the code
grep -r "writeToParcel\|Parcel.obtain" --include="*.java" | head

# 4. Check pending async transactions (buffer held by queued oneways)
adb shell cat /proc/binder/proc/<PID> | grep "pending async"
```

### Common Causes
1. **Large Intent extras**: Passing bitmaps, large lists, or byte arrays in Intents
2. **CursorWindow overflow**: Query returning too many rows without pagination
3. **Accumulated oneway calls**: Server too slow, oneways fill buffer
4. **Buffer leak**: Missing `BC_FREE_BUFFER` (library bug or native crash)
5. **SavedInstanceState too large**: Activity saving too much state

### Fixes
```java
// FIX 1: Use SharedMemory for large data
ParcelFileDescriptor pfd = createSharedMemory(largeData);
service.processLargeData(pfd);  // Only FD goes through Binder

// FIX 2: Paginate cursor queries
Cursor cursor = resolver.query(uri, null, null, null,
    "LIMIT 100 OFFSET " + page * 100);

// FIX 3: Reduce SavedInstanceState
@Override
public void onSaveInstanceState(Bundle outState) {
    // Only save IDs, not full objects
    outState.putIntArray("ids", getVisibleItemIds());
    // Don't: outState.putParcelableArrayList("items", allItems);
}

// FIX 4: Monitor buffer usage
int freeSpace = getBinderFreeSpace(Process.myPid());
if (freeSpace < 200_000) {
    Log.w(TAG, "Binder buffer running low: " + freeSpace + " bytes free");
}
```

---

## SCENARIO 2: BINDER DEADLOCK

### Symptoms
- ANR dialog
- Main thread stuck in `binder_thread_read`
- Multiple processes hung simultaneously
- No crash — processes alive but unresponsive

### Diagnosis Steps
```bash
# 1. Get process stack traces
adb shell debuggerd -b <PID_A>
adb shell debuggerd -b <PID_B>

# 2. Check Binder thread state
adb shell cat /proc/binder/proc/<PID_A>
# Look for: thread X waiting for PID_B
# Then check PID_B — is it waiting for PID_A?

# 3. Check wait channels
for tid in $(ls /proc/<PID>/task/); do
  echo "$tid: $(cat /proc/<PID>/task/$tid/wchan)"
done
# Deadlocked threads show: binder_thread_read

# 4. Systrace for visual identification
python systrace.py -o deadlock.html -t 10 binder_driver sched

# 5. Java ANR trace
adb pull /data/anr/traces.txt
grep -A20 "main" traces.txt
```

### Common Deadlock Patterns
```
Pattern 1: Simple Cycle
  Process A (holding lock L) → Binder call → Process B
  Process B → Binder call → Process A (needs lock L) → DEADLOCK

Pattern 2: Callback Deadlock
  Client calls Server.doWork() [sync]
    Server.doWork() calls Client.callback() [sync]
      Client.callback() tries to call Server.getState() [sync]
        All server threads busy → DEADLOCK

Pattern 3: Lock + Binder
  Thread 1: lock(mLock) → binderCall(serviceB) [waiting]
  Thread 2 (Binder thread): onTransact() → lock(mLock) [waiting for T1]
```

### Fixes
```java
// FIX 1: Never hold locks during Binder calls
void doWork() {
    Data data;
    synchronized(mLock) {
        data = mData.copy();  // Copy under lock
    }
    // Lock released BEFORE Binder call
    mRemoteService.process(data);
}

// FIX 2: Use oneway for callbacks (breaks the cycle)
interface IMyCallback {
    oneway void onResult(in Data data);  // Async — can't deadlock
}

// FIX 3: Post to handler instead of direct callback
void onTransact(...) {
    mHandler.post(() -> {
        // Process on handler thread, not Binder thread
        client.callback(result);
    });
}
```

---

## SCENARIO 3: SERVICE NOT FOUND (NULL)

### Symptoms
```
W/ServiceManager: Service 'my_service' not found
E/MyApp: NullPointerException - service is null
```

### Diagnosis
```bash
# 1. Is the service running?
adb shell ps -A | grep <service_process_name>

# 2. Is it registered?
adb shell service list | grep my_service
adb shell lshal | grep my_service  # For HALs

# 3. Check service logs for crash at startup
adb logcat | grep -i "my_service\|crash\|died"

# 4. Check SELinux for registration denial
adb logcat | grep "avc: denied.*add"

# 5. Check init.rc (for HAL services)
adb shell cat /vendor/etc/init/my_service.rc
adb shell getprop init.svc.my_service  # Should be "running"

# 6. Check VINTF compatibility (for HAL services)
adb shell /system/bin/vintf --check-compat
```

### Common Causes & Fixes
| Cause | Fix |
|-------|-----|
| Service not started yet (boot race) | Use `waitForService()` instead of `getService()` |
| Service crashed at init | Check logcat for crash, fix the init code |
| SELinux denies registration | Add `add_hwservice()` rule |
| Wrong service name | Verify name matches exactly (case-sensitive) |
| Wrong binder domain | HAL on hwbinder but looking on /dev/binder |
| Missing VINTF manifest entry | Add `<hal>` entry to device manifest |
| Service killed by OOM | Increase `oom_score_adj` or fix memory usage |

---

## SCENARIO 4: SELINUX DENIAL

### Symptoms
```
avc: denied { call } for scontext=u:r:my_app:s0 tcontext=u:r:vehicle_hal:s0
    tclass=binder permissive=0
```

### Diagnosis
```bash
# 1. Get all denials
adb logcat -d | grep "avc: denied" > denials.txt

# 2. Parse the denial
# scontext = source (who's trying)
# tcontext = target (what they're trying to access)
# tclass = type (binder, hwservice_manager, file, etc.)
# { call } = permission needed

# 3. Generate policy suggestion
audit2allow -i denials.txt

# 4. Check current policy
adb shell sesearch -A -s my_app -t vehicle_hal -c binder

# 5. Verify not a neverallow violation
grep "neverallow.*my_app.*vehicle_hal" device/*/sepolicy/**/*.te
```

### Fix Process
```te
# Step 1: Understand WHY the access happens — is it legitimate?
# If app shouldn't call HAL directly → fix the app code path
# If it's a system service that needs access → add policy

# Step 2: Add minimal policy (principle of least privilege)
# DON'T: allow my_app vehicle_hal:binder { call transfer };  # Too broad
# DO: Only allow the specific needed access

# For legitimate system service access:
allow car_service vehicle_hal:binder call;
allow car_service hal_vehicle_hwservice:hwservice_manager find;

# Step 3: Test
adb reboot
# Verify no more denials for this path
adb logcat | grep "avc: denied" | grep vehicle
```

---

## SCENARIO 5: THREAD POOL EXHAUSTION

### Symptoms
- Binder calls hang (no reply)
- Process is alive but unresponsive
- Other processes waiting for this process timeout (ANR)
- No crash, no error — just silence

### Diagnosis
```bash
# 1. Check thread pool state
adb shell cat /proc/binder/proc/<PID>
# Look for:
#   thread N: l 00 need_return 0 tr 0
# All threads should show some as "waiting" — if ALL are processing = exhausted

# 2. Count busy threads
adb shell cat /proc/binder/proc/<PID> | grep "thread" | wc -l
# Compare against max thread count

# 3. What are threads doing? (stack trace)
adb shell debuggerd -b <PID>
# Look for threads blocked on I/O, locks, or other Binder calls

# 4. Check for incoming queue backup
adb shell cat /proc/binder/proc/<PID> | grep "pending"
```

### Common Causes
1. **Blocking I/O in Binder thread**: Reading file/network on Binder thread
2. **Nested Binder calls**: All threads making outgoing calls, none free for incoming
3. **Lock contention**: Binder threads blocked on a mutex held by another thread
4. **Slow computation**: CPU-intensive work on Binder thread
5. **Pool too small**: Legitimate high concurrency exceeds pool size

### Fixes
```cpp
// FIX 1: Increase thread pool (temporary relief)
ProcessState::self()->setThreadPoolMaxThreadCount(32);

// FIX 2: Move blocking work off Binder thread
ndk::ScopedAStatus onTransact(...) {
    // DON'T block Binder thread:
    // auto result = readFromNetwork();  // BAD
    
    // DO: Post to worker thread, reply async
    mWorkerPool.enqueue([this, callback]() {
        auto result = readFromNetwork();
        callback->onResult(result);  // Reply via callback
    });
    return ndk::ScopedAStatus::ok();
}

// FIX 3: Use oneway to avoid holding client threads
interface IMyService {
    oneway void processAsync(IMyCallback callback, in Data data);
}

// FIX 4: Avoid nested Binder calls from Binder threads
// Cache data instead of re-querying other services
```

---

## SCENARIO 6: DEATH NOTIFICATION NOT RECEIVED

### Symptoms
- Server process crashed/killed
- Client doesn't receive death notification
- Client continues trying to call dead service
- Eventually gets `DeadObjectException` on next call

### Diagnosis
```bash
# 1. Verify server is actually dead
adb shell pidof <server_process>  # Should return nothing

# 2. Check if linkToDeath was registered properly
# Add logging around linkToDeath call

# 3. Check if death callback thread is blocked
# If the Binder thread pool is exhausted, death callback can't be delivered

# 4. Check for race condition
# If client links AFTER server already died → no notification
```

### Common Causes & Fixes
```java
// CAUSE 1: linkToDeath called after service already dead
IBinder binder = ServiceManager.getService("name");
if (binder == null) return;  // Service not up
try {
    binder.linkToDeath(mDeathRecipient, 0);
} catch (RemoteException e) {
    // Service died between getService and linkToDeath!
    handleDeath();
}

// CAUSE 2: Weak reference collected before death
// BAD: Anonymous inner class (may be GC'd)
binder.linkToDeath(() -> { ... }, 0);  // Lambda may be collected!

// GOOD: Strong reference kept
private final IBinder.DeathRecipient mDeath = () -> { handleDeath(); };
binder.linkToDeath(mDeath, 0);  // mDeath won't be GC'd

// CAUSE 3: Thread pool exhausted (death notification can't be delivered)
// FIX: Keep thread pool healthy (see Scenario 5)

// CAUSE 4: Calling unlinkToDeath prematurely
// Don't unlinkToDeath until you truly don't care about the service anymore
```

---

## SCENARIO 7: BINDER BUFFER LEAK

### Symptoms
```
binder_alloc: <PID>: binder_alloc_buf, no vma
E/Binder: Transaction failed: buffer allocation error
```
- Free async space decreases over time
- Eventually all new transactions fail
- Process restart temporarily fixes it

### Diagnosis
```bash
# 1. Monitor buffer usage over time
watch -n 1 "adb shell cat /proc/binder/proc/<PID> | grep 'free async space'"

# 2. Check for unreleased buffers
adb shell cat /proc/binder/proc/<PID> | grep "buffer"
# Look for many "allocated" buffers with no corresponding "free"

# 3. Common in native code — check for missing freeBuffer
grep -r "BC_FREE_BUFFER\|freeBuffer\|IPCThreadState" --include="*.cpp"

# 4. Check if oneway queue is backing up
adb shell cat /proc/binder/proc/<PID> | grep "async_recv"
```

### Common Causes
1. **Missing BC_FREE_BUFFER**: Native code not freeing received buffers
2. **Oneway queue backup**: Server too slow processing async calls
3. **Crash in onTransact**: Exception before buffer is freed
4. **Library bug**: Middle layer receives buffer but crashes before forwarding

### Fixes
```cpp
// FIX 1: Ensure buffers always freed (RAII pattern)
class ScopedBinderBuffer {
    Parcel& mParcel;
public:
    ScopedBinderBuffer(Parcel& p) : mParcel(p) {}
    ~ScopedBinderBuffer() {
        mParcel.freeData();  // Always frees the buffer
    }
};

// FIX 2: Catch exceptions in onTransact
status_t MyService::onTransact(uint32_t code, const Parcel& data,
                                Parcel* reply, uint32_t flags) {
    // Buffer is freed when 'data' Parcel is destroyed
    // Make sure no early returns skip this
    try {
        return handleTransaction(code, data, reply, flags);
    } catch (...) {
        // Buffer will still be freed when 'data' goes out of scope
        return UNKNOWN_ERROR;
    }
}

// FIX 3: Throttle oneway senders
// If receiving too many oneways, have sender implement flow control
ndk::ScopedAStatus subscribe(...) {
    if (mPendingAsyncCount > 100) {
        // Tell client to slow down
        return ndk::ScopedAStatus::fromStatus(STATUS_BUSY);
    }
    // ...
}

// FIX 4: Monitor and alert
void checkBufferHealth() {
    // Read /proc/binder/proc/self and parse free space
    int freeSpace = parseFreeAsyncSpace();
    if (freeSpace < 200000) {  // Less than 200KB free
        LOG(ERROR) << "Binder buffer critically low: " << freeSpace;
        // Take corrective action: shed load, drain queues
    }
}
```

---

# PART C: DEBUGGING CHEAT COMMANDS

### One-Liners for Common Issues

```bash
# Who is this PID talking to via Binder?
adb shell cat /proc/binder/proc/<PID> | grep "outgoing"

# How many transactions has this service processed?
adb shell cat /proc/binder/stats | grep -A5 "proc <PID>"

# Is my service's thread pool full?
adb shell cat /proc/binder/proc/<PID> | grep -c "thread.*l 12"

# What's eating my Binder buffer?
adb shell cat /proc/binder/proc/<PID> | grep "buffer" | sort -k5 -n -r | head

# Kill and restart a HAL service
adb shell stop vendor.vehicle-hal && adb shell start vendor.vehicle-hal

# Quick latency test
adb shell "time service call SurfaceFlinger 1"

# Trace all Binder calls to a service for 5 seconds
adb shell "echo 1 > /sys/kernel/debug/tracing/events/binder/enable"
adb shell "sleep 5"
adb shell "cat /sys/kernel/debug/tracing/trace" | grep <PID>
adb shell "echo 0 > /sys/kernel/debug/tracing/events/binder/enable"
```

---

END OF DOCUMENT 07 — LABS & DEBUGGING
