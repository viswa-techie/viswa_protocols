# VOLUME 02 — INTERFACE DEFINITION LANGUAGES (IDLs)

## Universal Data Contract, Serialization, IDL & Interoperability Architecture Encyclopedia

---

## 02.0 — What is an IDL (Precise Definition and Stack Placement)

**Definition:** An Interface Definition Language (IDL) is a formal, language-neutral specification language used to describe the data types and service interfaces that form the contract between communicating software components, enabling automatic code generation of serialization logic, client stubs, and server skeletons in multiple target programming languages.

**Stack Position:**
```
    Data Modeling (Volume 01)
           ↓
    ┌─────────────────────────────────┐
    │  INTERFACE DEFINITION LANGUAGE  │  ← THIS VOLUME
    │  (.proto, .aidl, .fidl, .thrift)│
    └─────────────────────────────────┘
           ↓
    Schema Layer (Volume 03)
           ↓
    Serialization (Volume 04)
           ↓
    RPC / IPC Framework (Volume 06/07)
```

An IDL occupies the critical position between abstract domain modeling and concrete serialization. It is the **single source of truth** from which all implementation artifacts are generated.

---

## 02.1 — IDL vs Schema vs Data Model (Formal Distinction)

| Dimension | Data Model | Schema | IDL |
|-----------|-----------|--------|-----|
| **Primary purpose** | Describe domain concepts and relationships | Validate data structure and constraints | Define services AND types for code generation |
| **Defines services/RPCs** | No | No | **Yes** |
| **Defines data types** | Conceptually | Enforceable constraints | Precisely typed for codegen |
| **Machine-readable** | Sometimes (UML/XMI) | Always | Always |
| **Generates code** | Rarely | Sometimes (quicktype) | **Always** (primary purpose) |
| **Language-neutral** | Yes | Yes | **Yes** (designed for multi-language) |
| **Versioning/evolution** | Informal | Some (JSON Schema) | **Strong** (field tags, reserved) |
| **Examples** | ER diagram, UML, DDD | JSON Schema, XSD, Avro schema | Protobuf .proto, AIDL, FIDL, Thrift |

**Critical Rule:** All IDLs define types. Some IDLs also define services. A pure schema system (JSON Schema) defines structure but NOT services. A pure data model (ER diagram) defines concepts but generates neither code nor wire format.

---

## 02.2 — IDL Compilation Pipeline Architecture

```
                    Source IDL File
                    (.proto / .aidl / .fidl / .thrift)
                           │
                           ▼
                ┌─────────────────────┐
                │       LEXER         │  Tokenization
                └──────────┬──────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │       PARSER        │  Abstract Syntax Tree (AST)
                └──────────┬──────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │  SEMANTIC ANALYSIS  │  Type checking, import resolution,
                │                     │  constraint validation
                └──────────┬──────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │  INTERMEDIATE REP   │  FileDescriptorProto (protoc),
                │                     │  JSON IR (fidlc), AST (thrift)
                └──────────┬──────────┘
                           │
              ┌────────────┼────────────┐
              │            │            │
              ▼            ▼            ▼
    ┌──────────────┐ ┌──────────┐ ┌──────────────┐
    │  C++ Plugin  │ │Java Plugin│ │ Python Plugin│   Code Generator Plugins
    │  (codegen)   │ │ (codegen) │ │  (codegen)   │
    └──────┬───────┘ └─────┬────┘ └──────┬───────┘
           │               │              │
           ▼               ▼              ▼
    ┌──────────────┐ ┌──────────┐ ┌──────────────┐
    │  .h / .cc    │ │  .java   │ │    .py       │   Generated Source Files
    │  (types +    │ │ (types + │ │  (types +    │
    │   stubs)     │ │  stubs)  │ │   stubs)     │
    └──────────────┘ └──────────┘ └──────────────┘
```

**Key Principle:** The IDL compiler (protoc, thrift, aidl, fidlc) is the trust boundary. Once it accepts a source file, it guarantees that generated code in ALL target languages will correctly interoperate on the wire.

---

## 02.3 — Protocol Buffers (.proto)

### History and Versions

| Version | Year | Key Changes |
|---------|------|-------------|
| proto1 | 2001 | Internal Google, never public |
| proto2 | 2008 | Public release; required/optional/repeated; default values; extensions |
| proto3 | 2016 | Simplified: removed required, removed defaults, all fields optional; added map, Any; JSON mapping |
| Editions | 2023 | Replace syntax="proto2"/"proto3" with feature flags; gradual migration path |

### Complete .proto Syntax Reference

```protobuf
// File: vehicle_service.proto
syntax = "proto3";

package automotive.vehicle.v1;

import "google/protobuf/timestamp.proto";
import "google/protobuf/duration.proto";
import "google/protobuf/field_mask.proto";

option java_package = "com.automotive.vehicle.v1";
option java_multiple_files = true;
option go_package = "automotive/vehicle/v1;vehiclev1";
option optimize_for = SPEED;

// Enum: always start with UNSPECIFIED = 0
enum FuelType {
  FUEL_TYPE_UNSPECIFIED = 0;
  FUEL_TYPE_PETROL = 1;
  FUEL_TYPE_DIESEL = 2;
  FUEL_TYPE_ELECTRIC = 3;
  FUEL_TYPE_HYBRID = 4;
  FUEL_TYPE_HYDROGEN = 5;
  reserved 6 to 10;  // Reserved for future fuel types
}

enum TransmissionType {
  TRANSMISSION_TYPE_UNSPECIFIED = 0;
  TRANSMISSION_TYPE_MANUAL = 1;
  TRANSMISSION_TYPE_AUTOMATIC = 2;
  TRANSMISSION_TYPE_CVT = 3;
}

// Nested message (value object)
message GeoLocation {
  double latitude = 1;   // WGS-84 degrees (-90 to +90)
  double longitude = 2;  // WGS-84 degrees (-180 to +180)
  float altitude_m = 3;  // Meters above sea level
  float heading_deg = 4; // 0-360 degrees from true north
  float accuracy_m = 5;  // Horizontal accuracy in meters
}

// Main entity message
message Vehicle {
  // Identity
  string vehicle_id = 1;  // UUID
  string vin = 2;         // ISO 3779 VIN (17 chars)
  
  // Static attributes
  string make = 3;
  string model = 4;
  int32 year = 5;
  FuelType fuel_type = 6;
  TransmissionType transmission = 7;
  
  // Dynamic state
  float speed_kmh = 10;
  float odometer_km = 11;
  float fuel_level_percent = 12;
  GeoLocation location = 13;
  google.protobuf.Timestamp last_updated = 14;
  
  // Oneof: mutually exclusive fields (discriminated union)
  oneof powertrain_details {
    InternalCombustionEngine ice_engine = 20;
    ElectricPowertrain electric = 21;
    HybridPowertrain hybrid = 22;
  }
  
  // Map: key-value pairs
  map<string, string> metadata = 30;
  
  // Repeated: ordered list
  repeated DiagnosticCode active_dtcs = 31;
  
  // Reserved: prevent reuse of deleted fields
  reserved 40 to 49;
  reserved "legacy_field", "deprecated_speed";
}

message InternalCombustionEngine {
  int32 displacement_cc = 1;
  int32 cylinders = 2;
  int32 horsepower = 3;
  float rpm = 4;
}

message ElectricPowertrain {
  float battery_capacity_kwh = 1;
  float state_of_charge_percent = 2;
  float range_km = 3;
  int32 motor_power_kw = 4;
}

message HybridPowertrain {
  InternalCombustionEngine ice = 1;
  ElectricPowertrain electric = 2;
  bool ev_mode_active = 3;
}

message DiagnosticCode {
  string code = 1;        // e.g., "P0301"
  string description = 2;
  google.protobuf.Timestamp detected_at = 3;
  Severity severity = 4;
  
  enum Severity {
    SEVERITY_UNSPECIFIED = 0;
    SEVERITY_INFO = 1;
    SEVERITY_WARNING = 2;
    SEVERITY_CRITICAL = 3;
  }
}

// Service definition
service VehicleService {
  // Unary RPC: single request, single response
  rpc GetVehicle(GetVehicleRequest) returns (Vehicle);
  
  // Server streaming: single request, stream of responses
  rpc WatchVehicleState(WatchVehicleRequest) returns (stream VehicleStateUpdate);
  
  // Client streaming: stream of requests, single response
  rpc ReportTelemetry(stream TelemetryEvent) returns (TelemetryAck);
  
  // Bidirectional streaming
  rpc VehicleChat(stream VehicleCommand) returns (stream VehicleResponse);
  
  // Update with field mask (partial update)
  rpc UpdateVehicle(UpdateVehicleRequest) returns (Vehicle);
}

message GetVehicleRequest {
  string vehicle_id = 1;
}

message WatchVehicleRequest {
  string vehicle_id = 1;
  google.protobuf.Duration interval = 2;
}

message VehicleStateUpdate {
  Vehicle vehicle = 1;
  google.protobuf.Timestamp server_time = 2;
}

message TelemetryEvent {
  string vehicle_id = 1;
  float speed_kmh = 2;
  GeoLocation location = 3;
  google.protobuf.Timestamp event_time = 4;
}

message TelemetryAck {
  int64 events_received = 1;
  int64 events_processed = 2;
}

message VehicleCommand {
  oneof command {
    bool lock_doors = 1;
    float set_temperature_c = 2;
    bool start_engine = 3;
    GeoLocation navigate_to = 4;
  }
}

message VehicleResponse {
  bool success = 1;
  string message = 2;
}

message UpdateVehicleRequest {
  Vehicle vehicle = 1;
  google.protobuf.FieldMask update_mask = 2;
}
```

### proto2 vs proto3 vs Editions

| Feature | proto2 | proto3 | Editions |
|---------|--------|--------|----------|
| Required fields | Yes (`required`) | No (removed) | Feature flag `field_presence` |
| Optional keyword | Yes (`optional`) | Re-added in 3.15 | Feature flag |
| Default values | User-specified | Zero values only | Feature flag |
| Field presence | Always tracked | Only if `optional` | Configurable |
| Extensions | Yes | No (use `Any`) | Feature flag |
| Groups | Yes (deprecated) | No | No |
| Unknown fields | Preserved (configurable) | Always preserved | Always preserved |
| JSON mapping | Not standard | Built-in | Built-in |
| `map` type | No | Yes | Yes |
| `oneof` | Yes | Yes | Yes |

### Wire Format Fundamentals (encoding reference)

| Wire Type | ID | Used For | Encoding |
|-----------|-----|---------|----------|
| VARINT | 0 | int32, int64, uint32, uint64, sint32, sint64, bool, enum | Variable-length integer |
| I64 | 1 | fixed64, sfixed64, double | Fixed 8 bytes |
| LEN | 2 | string, bytes, embedded messages, packed repeated | Length-prefixed |
| SGROUP | 3 | group start (deprecated) | — |
| EGROUP | 4 | group end (deprecated) | — |
| I32 | 5 | fixed32, sfixed32, float | Fixed 4 bytes |

**Tag encoding:** `(field_number << 3) | wire_type`

**Varint encoding:** Each byte uses 7 bits for data, 1 bit (MSB) as continuation flag.

Example: field number 2, type int32, value 150
- Tag: (2 << 3) | 0 = 16 → encoded as varint: `0x10`
- Value 150: binary `10010110` → varint: `0x96 0x01`
- Wire bytes: `10 96 01`

### Governance

| Aspect | Detail |
|--------|--------|
| Governing body | Google (primary maintainer), CNCF (gRPC graduated) |
| Tier | 6 (Vendor-led open source) |
| License | Apache 2.0 (BSD 3-Clause for protoc runtime) |
| CLA | Google Individual/Corporate CLA |
| Specification | https://protobuf.dev/ |
| Reference implementation | protoc (C++), buf (Go) |
| Repository | https://github.com/protocolbuffers/protobuf |

---

## 02.4 — Apache Thrift IDL

### Overview

**Definition:** Apache Thrift is a cross-language serialization and RPC framework with its own IDL for defining types and services. Originally developed at Facebook (2007), donated to the Apache Software Foundation (2008).

### Complete Syntax Elements

```thrift
// File: vehicle_service.thrift

namespace java com.automotive.vehicle
namespace cpp automotive.vehicle
namespace py automotive.vehicle
namespace go automotive.vehicle

// Includes
include "common.thrift"

// Constants
const string VERSION = "1.0.0"
const i32 MAX_VEHICLES = 10000

// Typedef
typedef i64 Timestamp
typedef string VehicleId

// Enum
enum FuelType {
  PETROL = 1,
  DIESEL = 2,
  ELECTRIC = 3,
  HYBRID = 4,
}

// Struct (basic data type)
struct GeoLocation {
  1: required double latitude,
  2: required double longitude,
  3: optional double altitude,
  4: optional double heading,
}

// Union (discriminated union - exactly one field set)
union PowertrainDetails {
  1: ICEEngine ice,
  2: ElectricPowertrain electric,
  3: HybridPowertrain hybrid,
}

struct ICEEngine {
  1: i32 displacement_cc,
  2: i32 cylinders,
  3: i32 horsepower,
}

struct ElectricPowertrain {
  1: double battery_kwh,
  2: double charge_percent,
  3: double range_km,
}

struct HybridPowertrain {
  1: ICEEngine ice,
  2: ElectricPowertrain electric,
  3: bool ev_mode,
}

// Main struct
struct Vehicle {
  1: required VehicleId id,
  2: required string vin,
  3: required string make,
  4: required string model,
  5: optional i32 year,
  6: optional FuelType fuel_type,
  7: optional double speed_kmh,
  8: optional GeoLocation location,
  9: optional PowertrainDetails powertrain,
  10: optional map<string, string> metadata,
  11: optional list<string> active_dtcs,
  12: optional Timestamp last_updated,
}

// Exception
exception VehicleNotFoundException {
  1: string vehicle_id,
  2: string message,
}

exception InvalidOperationException {
  1: string operation,
  2: string reason,
}

// Service definition
service VehicleService {
  Vehicle getVehicle(1: VehicleId id) throws (
    1: VehicleNotFoundException not_found
  ),
  
  list<Vehicle> listVehicles(1: i32 limit, 2: i32 offset),
  
  void updateVehicle(1: Vehicle vehicle) throws (
    1: VehicleNotFoundException not_found,
    2: InvalidOperationException invalid
  ),
  
  // Oneway: fire-and-forget (no response)
  oneway void reportTelemetry(1: VehicleId id, 2: double speed, 3: GeoLocation loc),
}

// Service inheritance
service AdminVehicleService extends VehicleService {
  void deleteVehicle(1: VehicleId id),
  void resetAllVehicles(),
}
```

### Thrift Protocol Stack

```
┌─────────────────────┐
│  Generated Code     │  Service stubs, type classes
├─────────────────────┤
│  TProcessor         │  Dispatches method calls
├─────────────────────┤
│  TProtocol          │  Serialization format
│  (Binary/Compact/   │
│   JSON/SimpleJSON)  │
├─────────────────────┤
│  TTransport         │  I/O abstraction
│  (Socket/HTTP/      │
│   Framed/Buffered)  │
└─────────────────────┘
```

### Thrift Protocol Formats

| Protocol | Description | Use Case |
|----------|------------|----------|
| TBinaryProtocol | Simple binary encoding, field-by-field | Standard, debuggable |
| TCompactProtocol | Varint encoding, delta field IDs | Reduced message size |
| TJSONProtocol | Full JSON encoding | Debugging, interop |
| TSimpleJSONProtocol | Human-readable JSON (write-only) | Logging |

### Governance

| Aspect | Detail |
|--------|--------|
| Body | Apache Software Foundation |
| Tier | 5 (Open-source foundation) |
| License | Apache 2.0 |
| PMC | Apache Thrift PMC |
| Repository | https://github.com/apache/thrift |

---

## 02.5 — AIDL (Android Interface Definition Language)

### Overview

**Definition:** AIDL (Android Interface Definition Language) is Google's IDL for defining IPC interfaces over the Android Binder kernel driver, enabling type-safe cross-process communication between Android apps, system services, and hardware abstraction layers.

### Evolution

| Version | Android | Key Features |
|---------|---------|-------------|
| AIDL v1 | 1.0+ (2008) | Basic interfaces, primitives, Parcelable, List, Map, IBinder |
| AIDL v2 | 10+ (2019) | @nullable, @utf8InCpp, structured parcelable |
| AIDL v3 | 11+ (2020) | Union types, nested types, constant expressions, @VintfStability |
| Stable AIDL | 11+ | Frozen API surfaces for HAL interfaces, versioned interfaces |

### Complete Syntax

```aidl
// IVehicleHal.aidl
package android.hardware.automotive.vehicle;

import android.hardware.automotive.vehicle.VehiclePropConfig;
import android.hardware.automotive.vehicle.VehiclePropValue;
import android.hardware.automotive.vehicle.StatusCode;
import android.hardware.automotive.vehicle.IVehicleCallback;

@VintfStability
interface IVehicleHal {
    /**
     * Get all property configurations supported by this HAL.
     */
    VehiclePropConfig[] getAllPropConfigs();

    /**
     * Get specific property configurations by property IDs.
     */
    VehiclePropConfig[] getPropConfigs(in int[] propIds);

    /**
     * Get current property values.
     * @param requests Array of property value requests (prop ID + area ID)
     * @return Array of property values
     */
    VehiclePropValue[] getValues(
        in IVehicleCallback callback,
        in GetValueRequests requests
    );

    /**
     * Set property values.
     */
    void setValues(
        in IVehicleCallback callback,
        in SetValueRequests requests
    );

    /**
     * Subscribe to property change events.
     * @param callback Callback to receive events
     * @param options Subscription options (property, area, sample rate)
     * @param maxSharedMemoryFileCount Max number of shared memory files
     */
    void subscribe(
        in IVehicleCallback callback,
        in SubscribeOptions[] options,
        int maxSharedMemoryFileCount
    );

    /**
     * Unsubscribe from previously subscribed properties.
     */
    void unsubscribe(in IVehicleCallback callback, in int[] propIds);

    /**
     * Return the interface version (for Stable AIDL versioning).
     */
    int getInterfaceVersion();

    /**
     * Return the interface hash (for integrity verification).
     */
    String getInterfaceHash();
}
```

```aidl
// VehiclePropValue.aidl (structured parcelable)
package android.hardware.automotive.vehicle;

@VintfStability
parcelable VehiclePropValue {
    /** Property identifier (e.g., PERF_VEHICLE_SPEED) */
    int prop;
    
    /** Area ID (e.g., SEAT_ROW_1_LEFT for per-seat properties) */
    int areaId;
    
    /** Timestamp in nanoseconds (SystemClock.elapsedRealtimeNanos) */
    long timestamp;
    
    /** Status of the property value */
    VehiclePropertyStatus status;
    
    /** The value - exactly one of these arrays will be populated */
    int[] int32Values;
    long[] int64Values;
    float[] floatValues;
    byte[] byteValues;
    @utf8InCpp String[] stringValue;
}
```

```aidl
// IVehicleCallback.aidl
package android.hardware.automotive.vehicle;

@VintfStability
oneway interface IVehicleCallback {
    void onGetValues(in GetValueResults responses);
    void onSetValues(in SetValueResults responses);
    void onPropertyEvent(
        in VehiclePropValues propValues,
        int sharedMemoryFileCount
    );
    void onPropertySetError(in VehiclePropErrors errors);
}
```

### AIDL Type System

| Type | AIDL | Java Generated | C++ Generated |
|------|------|---------------|---------------|
| Primitive | `int`, `long`, `float`, `double`, `boolean`, `byte`, `char` | Primitive types | `int32_t`, `int64_t`, `float`, `double`, `bool`, `int8_t`, `char16_t` |
| String | `String` | `java.lang.String` | `std::string` (or `String16`) |
| Array | `int[]`, `String[]` | `int[]`, `String[]` | `std::vector<int32_t>`, `std::vector<std::string>` |
| Parcelable | `parcelable Foo { ... }` | Class implementing Parcelable | Struct with readFromParcel/writeToParcel |
| Interface | `interface IFoo { ... }` | `IFoo.Stub` + `IFoo.Proxy` | `BnFoo` (Binder native) + `BpFoo` (Binder proxy) |
| IBinder | `IBinder` | `android.os.IBinder` | `sp<IBinder>` |
| FileDescriptor | `FileDescriptor` | `java.io.FileDescriptor` | `android::base::unique_fd` |
| ParcelFileDescriptor | `ParcelFileDescriptor` | `android.os.ParcelFileDescriptor` | `ndk::ScopedFileDescriptor` |
| Nullable | `@nullable String` | May be null | `std::optional<std::string>` |
| Union | `union Foo { int a; String b; }` | Tagged union class | `std::variant`-like |
| Enum | `enum Foo { A=1, B=2 }` | `@IntDef` or Java enum | `enum class` |

### Stable AIDL and VINTF

**Stable AIDL** means the interface is:
1. Annotated with `@VintfStability` — part of the Vendor Interface
2. Frozen at a specific version — recorded in `aidl_api/<name>/<version>/`
3. Hash-verified — `.hash` file contains SHA-256 of the frozen .aidl files
4. Never modified once frozen — only new versions can add methods/types

**VINTF Compatibility Matrix:**
```xml
<!-- framework_compatibility_matrix.xml -->
<hal format="aidl">
    <name>android.hardware.automotive.vehicle</name>
    <version>1-2</version>  <!-- supports versions 1 and 2 -->
    <interface>
        <name>IVehicleHal</name>
        <instance>default</instance>
    </interface>
</hal>
```

### HIDL (Deprecated, for context)

HIDL (HAL Interface Definition Language) was introduced in Android 8.0 (Treble) and deprecated in Android 13+ in favor of Stable AIDL:

```hidl
// IVehicle.hal (HIDL - deprecated)
package android.hardware.automotive.vehicle@2.0;

interface IVehicle {
    getAllPropConfigs() generates (vec<VehiclePropConfig> configs);
    getPropConfigs(vec<int32_t> props) generates (StatusCode status, vec<VehiclePropConfig> configs);
    get(VehiclePropValue requestedPropValue) generates (StatusCode status, VehiclePropValue propValue);
    set(VehiclePropValue propValue) generates (StatusCode status);
    subscribe(IVehicleCallback callback, vec<SubscribeOptions> options) generates (StatusCode status);
    unsubscribe(IVehicleCallback callback, int32_t propId) generates (StatusCode status);
};
```

### Governance

| Aspect | Detail |
|--------|--------|
| Body | Google / Android Open Source Project (AOSP) |
| Tier | 6 (Vendor-led open source) |
| License | Apache 2.0 |
| Compiler | `aidl` (in AOSP build tools) |
| Build system | Soong (Android.bp): `aidl_interface` module |

---

## 02.6 — FIDL (Fuchsia Interface Definition Language)

### Overview

**Definition:** FIDL is the contract language for all inter-process communication on the Fuchsia operating system, defining protocols (services), data types, and capability-passing semantics over Zircon kernel channels.

### Complete Syntax

```fidl
// fuchsia.hardware.vehicle/vehicle.fidl
library fuchsia.hardware.vehicle;

using zx;

/// Maximum number of properties that can be reported in a single event.
const MAX_PROPERTIES_PER_EVENT uint32 = 100;

/// Fuel type classification.
type FuelType = strict enum : uint8 {
    UNKNOWN = 0;
    PETROL = 1;
    DIESEL = 2;
    ELECTRIC = 3;
    HYBRID = 4;
};

/// Geographic location with WGS-84 coordinates.
type GeoLocation = struct {
    latitude float64;
    longitude float64;
    altitude_m float32;
};

/// Vehicle property value - uses a flexible union for extensibility.
type PropertyValue = flexible union {
    1: int_value int32;
    2: float_value float32;
    3: string_value string:256;
    4: bool_value bool;
    5: bytes_value vector<uint8>:1024;
};

/// Vehicle property configuration.
/// Uses 'table' for forward-compatible extensibility.
type PropConfig = table {
    1: prop_id uint32;
    2: access uint32;
    3: change_mode uint32;
    4: min_sample_rate float32;
    5: max_sample_rate float32;
};

/// Error codes for vehicle operations.
type VehicleError = strict enum : uint32 {
    OK = 0;
    INVALID_ARG = 1;
    NOT_AVAILABLE = 2;
    ACCESS_DENIED = 3;
    INTERNAL_ERROR = 4;
    TRY_AGAIN = 5;
};

/// Main vehicle protocol - defines the IPC contract.
@discoverable
protocol Vehicle {
    /// Get all property configurations.
    GetAllPropConfigs() -> (struct {
        configs vector<PropConfig>:MAX_PROPERTIES_PER_EVENT;
    });

    /// Get a single property value.
    GetProperty(struct {
        prop_id uint32;
        area_id uint32;
    }) -> (struct {
        value PropertyValue;
    }) error VehicleError;

    /// Set a property value.
    SetProperty(struct {
        prop_id uint32;
        area_id uint32;
        value PropertyValue;
    }) -> () error VehicleError;

    /// Subscribe to property change events (server-to-client event).
    -> OnPropertyChanged(struct {
        prop_id uint32;
        area_id uint32;
        value PropertyValue;
        timestamp zx.Time;
    });

    /// One-way method (fire-and-forget, no response).
    ReportTelemetry(struct {
        prop_id uint32;
        value PropertyValue;
    });
};
```

### FIDL Key Concepts

| Concept | Definition | Protobuf Equivalent |
|---------|-----------|-------------------|
| `library` | Namespace for FIDL declarations | `package` |
| `struct` | Fixed-layout value type (no extensibility) | `message` (not quite — see table) |
| `table` | Extensible record (ordinal-based, fields can be added) | `message` with optional fields |
| `strict enum` | Closed enum (unknown values rejected) | `enum` (proto3 is open by default) |
| `flexible enum` | Open enum (unknown values accepted) | `enum` (proto3 default behavior) |
| `strict union` | Closed discriminated union | `oneof` |
| `flexible union` | Open discriminated union (unknown variants accepted) | No direct equivalent |
| `protocol` | Service definition with methods and events | `service` |
| `->` | Two-way method (has response) | `rpc Foo(Req) returns (Resp)` |
| `-> ()` | Two-way with empty response | `rpc Foo(Req) returns (Empty)` |
| `-> OnEvent(...)` | Event (server-initiated, no request) | `stream` (different semantics) |
| `error` | Rich error type on method | gRPC status codes |
| `@discoverable` | Protocol can be found via service directory | Service registration |

### struct vs table in FIDL

| Property | struct | table |
|----------|--------|-------|
| Layout | Fixed, dense, no gaps | Ordinal-indexed, sparse |
| Extensibility | None (breaking to add fields) | Safe to add new ordinals |
| Wire overhead | Minimal | Envelope per present field |
| Use case | Performance-critical, frozen types | Evolving configurations, APIs |
| Analogy | C struct / FlatBuffers struct | Protobuf message / FlatBuffers table |

### FIDL Wire Format

FIDL messages are encoded as:
- **Header:** Transaction ID (4 bytes) + flags (4 bytes) + ordinal (8 bytes)
- **Body:** Inline data + out-of-line data (for strings, vectors, tables)
- **Handles:** Transported alongside message data via Zircon channel syscall

### Governance

| Aspect | Detail |
|--------|--------|
| Body | Google / Fuchsia Project |
| Tier | 6 (Vendor-led open source) |
| License | BSD 3-Clause (Fuchsia) |
| Compiler | `fidlc` (C++ front-end, outputs JSON IR) |
| Backends | `fidlgen_cpp`, `fidlgen_rust`, `fidlgen_go`, `fidlgen_dart` |

---

## 02.7 — OMG IDL / CORBA IDL / DDS IDL

### CORBA IDL

**Definition:** OMG IDL (Interface Definition Language) is the contract language for CORBA (Common Object Request Broker Architecture), defining object interfaces that can be invoked remotely across languages and platforms using IIOP (Internet Inter-ORB Protocol).

```idl
// vehicle.idl (OMG IDL / CORBA)
module automotive {
    module vehicle {
        
        enum FuelType {
            PETROL,
            DIESEL,
            ELECTRIC,
            HYBRID
        };
        
        struct GeoLocation {
            double latitude;
            double longitude;
            float altitude;
        };
        
        typedef sequence<string> StringList;
        typedef sequence<octet> ByteBuffer;
        
        exception VehicleNotFound {
            string vehicle_id;
            string message;
        };
        
        exception AccessDenied {
            string reason;
        };
        
        interface Vehicle {
            // Readonly attribute (getter only)
            readonly attribute string vin;
            readonly attribute string make;
            readonly attribute string model;
            
            // Read-write attribute (getter + setter)
            attribute FuelType fuel_type;
            
            // Operations
            GeoLocation getLocation()
                raises (VehicleNotFound);
            
            void setSpeed(in float speed_kmh)
                raises (AccessDenied);
            
            float getSpeed()
                raises (VehicleNotFound);
            
            // Out parameters
            void getStatus(
                out float speed,
                out float fuel_level,
                out GeoLocation location
            ) raises (VehicleNotFound);
            
            // Oneway (asynchronous, no return)
            oneway void reportEvent(in string event_type, in string payload);
        };
        
        // Interface inheritance
        interface AdminVehicle : Vehicle {
            void shutdown();
            void factoryReset();
        };
    };
};
```

### DDS IDL (Data Distribution Service)

DDS IDL extends OMG IDL with data-centric annotations:

```idl
// vehicle_dds.idl (DDS IDL with X-Types annotations)
module automotive {
    module vehicle {
        
        @topic
        @mutable
        struct VehicleState {
            @key
            string<17> vin;          // Instance identity (DDS key)
            
            float speed_kmh;
            float fuel_level;
            
            @optional
            double latitude;
            
            @optional
            double longitude;
            
            long long timestamp_ns;
        };
        
        @topic
        @appendable
        struct DiagnosticEvent {
            @key
            string<17> vin;
            
            @key
            string<8> dtc_code;
            
            string<256> description;
            long long detected_at;
        };
        
        @topic
        @final
        struct SpeedSample {
            string<17> vin;
            float speed_kmh;
            long long timestamp_ns;
        };
    };
};
```

### DDS X-Types Extensibility

| Annotation | Meaning | Evolution Rules |
|-----------|---------|-----------------|
| `@final` | Type cannot be extended | No fields can be added |
| `@appendable` | New fields only at the end | Existing fields unchanged; new at tail |
| `@mutable` | Fields can be added anywhere (tagged) | Each field has unique ID |
| `@key` | Field is part of instance identity | Key fields identify data instances |
| `@optional` | Field may not be present | Readers handle absence |
| `@must_understand` | Reader must understand this field or reject | Critical field marker |

### Governance (CORBA + DDS)

| Aspect | Detail |
|--------|--------|
| Body | Object Management Group (OMG) |
| Tier | 3 (Industry consortium) |
| Specifications | CORBA 3.4, DDS 1.4, DDS-XTypes 1.3, IDL 4.2 |
| IP | OMG document access is free; some compliance tests require membership |

---

## 02.8 — ASN.1 (Abstract Syntax Notation One)

### Overview

**Definition:** ASN.1 is a formal notation standardized by ISO/ITU-T for describing data structures that can be serialized using various encoding rules (BER, DER, PER, XER, OER, JER). It serves as both an IDL and a formal data description language, with primary usage in telecommunications, security (X.509), and aviation.

### Module Structure

```asn1
-- VehicleModule.asn1
VehicleModule DEFINITIONS AUTOMATIC TAGS ::= BEGIN

EXPORTS Vehicle, VehicleId, GeoLocation;
IMPORTS
    TimeStamp FROM CommonTypesModule;

-- Basic type aliases
VehicleId ::= VisibleString (SIZE (1..36))
VIN ::= VisibleString (SIZE (17))

-- Enumerated type
FuelType ::= ENUMERATED {
    petrol(0),
    diesel(1),
    electric(2),
    hybrid(3),
    hydrogen(4),
    ...  -- Extension marker: new values can be added
}

-- Constrained integer
SpeedKmh ::= INTEGER (0..500)
FuelLevelPercent ::= INTEGER (0..100)

-- Sequence (equivalent to struct)
GeoLocation ::= SEQUENCE {
    latitude    REAL (-90.0..90.0),
    longitude   REAL (-180.0..180.0),
    altitude    REAL OPTIONAL,
    heading     REAL (0.0..360.0) OPTIONAL
}

-- Choice (discriminated union)
PowertrainDetails ::= CHOICE {
    ice         ICEEngine,
    electric    ElectricPowertrain,
    hybrid      HybridPowertrain,
    ...  -- Extensible
}

ICEEngine ::= SEQUENCE {
    displacement-cc     INTEGER (50..20000),
    cylinders           INTEGER (1..16),
    horsepower          INTEGER (1..2000)
}

ElectricPowertrain ::= SEQUENCE {
    battery-kwh         REAL (0.0..500.0),
    charge-percent      INTEGER (0..100),
    range-km            REAL (0.0..2000.0)
}

HybridPowertrain ::= SEQUENCE {
    ice         ICEEngine,
    electric    ElectricPowertrain,
    ev-mode     BOOLEAN
}

-- Main vehicle type with extension marker
Vehicle ::= SEQUENCE {
    id              VehicleId,
    vin             VIN,
    make            UTF8String (SIZE (1..100)),
    model           UTF8String (SIZE (1..100)),
    year            INTEGER (1886..2200),
    fuel-type       FuelType,
    speed           SpeedKmh OPTIONAL,
    location        GeoLocation OPTIONAL,
    powertrain      PowertrainDetails OPTIONAL,
    last-updated    TimeStamp OPTIONAL,
    ...,  -- Extension marker
    -- Fields added in version 2:
    active-dtcs     SEQUENCE OF DiagnosticCode OPTIONAL
}

DiagnosticCode ::= SEQUENCE {
    code            VisibleString (SIZE (5)),  -- e.g., "P0301"
    description     UTF8String (SIZE (1..500)),
    severity        ENUMERATED { info(0), warning(1), critical(2) }
}

END
```

### ASN.1 Type System

| ASN.1 Type | Description | Protobuf Equivalent |
|-----------|-------------|-------------------|
| `BOOLEAN` | True/False | `bool` |
| `INTEGER` | Arbitrary precision integer | `int32`/`int64`/`uint32`/`uint64` |
| `REAL` | Floating point (base 2 or 10) | `float`/`double` |
| `BIT STRING` | Sequence of bits | `bytes` with bit semantics |
| `OCTET STRING` | Sequence of bytes | `bytes` |
| `NULL` | No value | — |
| `ENUMERATED` | Named integer values | `enum` |
| `SEQUENCE` | Ordered collection of typed fields | `message` |
| `SEQUENCE OF` | Ordered list of same type | `repeated` |
| `SET` | Unordered collection of typed fields | — (no equivalent) |
| `SET OF` | Unordered list of same type | — (use repeated) |
| `CHOICE` | Discriminated union (one of N types) | `oneof` |
| `VisibleString` | ASCII printable characters | `string` (restricted) |
| `UTF8String` | UTF-8 encoded text | `string` |
| `IA5String` | International Alphabet 5 (ASCII) | `string` (restricted) |
| `OBJECT IDENTIFIER` | Globally unique dot-notation identifier | — (no equivalent) |
| `ANY` / `OPEN TYPE` | Type determined at runtime by context | `google.protobuf.Any` |

### ASN.1 Encoding Rules (Summary — Full detail in Volume 05)

| Encoding | Full Name | Size | Use Case |
|----------|----------|------|----------|
| BER | Basic Encoding Rules | Largest | General, flexible |
| DER | Distinguished Encoding Rules | Same as BER (canonical) | Cryptography (X.509, CMS) |
| PER Aligned | Packed Encoding Rules (aligned) | Small | 3GPP RRC/NAS, aviation |
| PER Unaligned | Packed Encoding Rules (unaligned) | Smallest | Maximum compression |
| XER | XML Encoding Rules | Large (XML) | Human-readable, debugging |
| OER | Octet Encoding Rules | Medium | Simple, fast (no bit-packing) |
| JER | JSON Encoding Rules | Medium (JSON) | Web interop |

### Governance

| Aspect | Detail |
|--------|--------|
| Body | ISO (ISO 8824) / ITU-T (X.680 series) — joint maintenance |
| Tier | 1 (ISO) / 2 (ITU-T) — highest governance tier |
| License | ISO documents are paid; ITU-T Recommendations freely available online |
| Stability | Extremely stable; backward-compatible since 1984 |
| Compilers | asn1c (open), OSS Nokalva (commercial), Objsys (commercial) |

---

## 02.9 — WSDL (Web Services Description Language)

### Overview

**Definition:** WSDL is a W3C standard XML-based IDL for describing SOAP web services — their operations, message formats, and network bindings.

### Structure (WSDL 1.1)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<definitions name="VehicleService"
    targetNamespace="http://automotive.example.com/vehicle"
    xmlns="http://schemas.xmlsoap.org/wsdl/"
    xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/"
    xmlns:tns="http://automotive.example.com/vehicle"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema">

    <!-- Types: XSD definitions for messages -->
    <types>
        <xsd:schema targetNamespace="http://automotive.example.com/vehicle">
            <xsd:complexType name="Vehicle">
                <xsd:sequence>
                    <xsd:element name="vin" type="xsd:string"/>
                    <xsd:element name="make" type="xsd:string"/>
                    <xsd:element name="model" type="xsd:string"/>
                    <xsd:element name="year" type="xsd:int"/>
                    <xsd:element name="speed" type="xsd:float" minOccurs="0"/>
                </xsd:sequence>
            </xsd:complexType>
            
            <xsd:element name="GetVehicleRequest">
                <xsd:complexType>
                    <xsd:sequence>
                        <xsd:element name="vehicleId" type="xsd:string"/>
                    </xsd:sequence>
                </xsd:complexType>
            </xsd:element>
            
            <xsd:element name="GetVehicleResponse">
                <xsd:complexType>
                    <xsd:sequence>
                        <xsd:element name="vehicle" type="tns:Vehicle"/>
                    </xsd:sequence>
                </xsd:complexType>
            </xsd:element>
        </xsd:schema>
    </types>

    <!-- Messages -->
    <message name="GetVehicleInput">
        <part name="parameters" element="tns:GetVehicleRequest"/>
    </message>
    <message name="GetVehicleOutput">
        <part name="parameters" element="tns:GetVehicleResponse"/>
    </message>

    <!-- PortType (abstract interface) -->
    <portType name="VehiclePortType">
        <operation name="GetVehicle">
            <input message="tns:GetVehicleInput"/>
            <output message="tns:GetVehicleOutput"/>
        </operation>
    </portType>

    <!-- Binding (concrete protocol) -->
    <binding name="VehicleSoapBinding" type="tns:VehiclePortType">
        <soap:binding style="document" transport="http://schemas.xmlsoap.org/soap/http"/>
        <operation name="GetVehicle">
            <soap:operation soapAction="http://automotive.example.com/GetVehicle"/>
            <input><soap:body use="literal"/></input>
            <output><soap:body use="literal"/></output>
        </operation>
    </binding>

    <!-- Service (endpoint) -->
    <service name="VehicleService">
        <port name="VehiclePort" binding="tns:VehicleSoapBinding">
            <soap:address location="https://api.automotive.example.com/vehicle"/>
        </port>
    </service>
</definitions>
```

### WSDL 1.1 vs 2.0

| Feature | WSDL 1.1 | WSDL 2.0 |
|---------|----------|----------|
| Year | 2001 | 2007 |
| Status | W3C Note (never Recommendation) | W3C Recommendation |
| Abstract interface | `portType` | `interface` |
| Endpoint | `port` | `endpoint` |
| Message patterns | 4 (one-way, request-response, solicit-response, notification) | MEPs (Message Exchange Patterns) |
| HTTP binding | Limited | Full REST support |
| Adoption | Very widespread (WS-* era) | Poor (REST/OpenAPI superseded) |

### Governance

| Aspect | Detail |
|--------|--------|
| Body | W3C |
| Tier | 2 |
| Status | Largely superseded by OpenAPI for REST; still used in legacy SOAP systems |
| Code generation | wsdl2java (Apache CXF), svcutil (.NET), wsimport (JAX-WS) |

---

## 02.10 — AWS Smithy

### Overview

**Definition:** Smithy is a protocol-agnostic IDL developed by Amazon for defining web services. It uses a trait-based system to separate the abstract service model from protocol-specific concerns.

### Syntax

```smithy
// vehicle.smithy
$version: "2.0"

namespace com.automotive.vehicle

use aws.protocols#restJson1

/// Vehicle service for fleet management.
@restJson1
service VehicleService {
    version: "2024-01-15"
    operations: [GetVehicle, ListVehicles, UpdateVehicle, DeleteVehicle]
    resources: [VehicleResource]
}

resource VehicleResource {
    identifiers: { vehicleId: VehicleId }
    read: GetVehicle
    update: UpdateVehicle
    delete: DeleteVehicle
    list: ListVehicles
}

// Custom string type with constraints
@length(min: 1, max: 36)
string VehicleId

@pattern("^[A-HJ-NPR-Z0-9]{17}$")
string VIN

// Enum
@enum([
    { value: "PETROL", name: "PETROL" },
    { value: "DIESEL", name: "DIESEL" },
    { value: "ELECTRIC", name: "ELECTRIC" },
    { value: "HYBRID", name: "HYBRID" },
])
string FuelType

// Structure
structure Vehicle {
    @required
    vehicleId: VehicleId

    @required
    vin: VIN

    @required
    make: String

    @required
    model: String

    year: Integer

    fuelType: FuelType

    speed: Float

    location: GeoLocation

    @timestampFormat("date-time")
    lastUpdated: Timestamp
}

structure GeoLocation {
    @required
    latitude: Double

    @required
    longitude: Double

    altitude: Float
}

// Operation definitions
@readonly
@http(method: "GET", uri: "/vehicles/{vehicleId}")
operation GetVehicle {
    input: GetVehicleInput
    output: GetVehicleOutput
    errors: [VehicleNotFound, AccessDenied]
}

@input
structure GetVehicleInput {
    @required
    @httpLabel
    vehicleId: VehicleId
}

@output
structure GetVehicleOutput {
    @required
    vehicle: Vehicle
}

// Paginated list
@readonly
@http(method: "GET", uri: "/vehicles")
@paginated(inputToken: "nextToken", outputToken: "nextToken", pageSize: "maxResults")
operation ListVehicles {
    input: ListVehiclesInput
    output: ListVehiclesOutput
}

@input
structure ListVehiclesInput {
    @httpQuery("maxResults")
    maxResults: Integer

    @httpQuery("nextToken")
    nextToken: String

    @httpQuery("make")
    makeFilter: String
}

@output
structure ListVehiclesOutput {
    @required
    vehicles: VehicleList

    nextToken: String
}

list VehicleList {
    member: Vehicle
}

// Update
@idempotent
@http(method: "PUT", uri: "/vehicles/{vehicleId}")
operation UpdateVehicle {
    input: UpdateVehicleInput
    output: UpdateVehicleOutput
    errors: [VehicleNotFound, ValidationError]
}

// Delete
@idempotent
@http(method: "DELETE", uri: "/vehicles/{vehicleId}")
operation DeleteVehicle {
    input: DeleteVehicleInput
    output: DeleteVehicleOutput
    errors: [VehicleNotFound]
}

// Error shapes
@error("client")
@httpError(404)
structure VehicleNotFound {
    @required
    message: String
    vehicleId: VehicleId
}

@error("client")
@httpError(403)
structure AccessDenied {
    @required
    message: String
}

@error("client")
@httpError(400)
structure ValidationError {
    @required
    message: String
    fieldErrors: FieldErrorList
}
```

### Smithy Traits System

| Trait | Purpose | Example |
|-------|---------|---------|
| `@required` | Field must be present | Applied to structure members |
| `@http` | HTTP binding for an operation | `@http(method: "GET", uri: "/foo/{id}")` |
| `@httpLabel` | URI path parameter | Applied to input member matching `{...}` |
| `@httpQuery` | Query string parameter | `@httpQuery("limit")` |
| `@httpHeader` | HTTP header binding | `@httpHeader("X-Request-Id")` |
| `@paginated` | Pagination support | `inputToken`, `outputToken`, `pageSize` |
| `@idempotent` | Safe to retry without side effects | Applied to PUT/DELETE operations |
| `@readonly` | Does not modify state | Applied to GET operations |
| `@error` | Marks structure as error response | `@error("client")` or `@error("server")` |
| `@deprecated` | Marks as deprecated | Applied to any shape |
| `@documentation` / `///` | Documentation string | Triple-slash comments |
| `@length` | String/list length constraint | `@length(min: 1, max: 100)` |
| `@range` | Numeric range constraint | `@range(min: 0, max: 500)` |
| `@pattern` | Regex pattern constraint | `@pattern("^[A-Z]{3}$")` |

### Governance

| Aspect | Detail |
|--------|--------|
| Body | Amazon Web Services (open-sourced) |
| Tier | 6 (Vendor-led open source) |
| License | Apache 2.0 |
| Repository | https://github.com/smithy-lang/smithy |
| Code Gen | smithy-build → AWS SDK generators for 10+ languages |
| Transformation | Smithy → OpenAPI 3.0 (for external documentation/consumption) |

---

## 02.11 — GraphQL SDL (Schema Definition Language)

### Overview

**Definition:** GraphQL SDL defines the type system and API surface for a GraphQL service — simultaneously serving as an IDL (defines types and operations) and as a query language specification.

### Complete Schema Example

```graphql
# vehicle.graphql

"""
Scalar type for ISO 8601 date-time strings.
"""
scalar DateTime

"""
Scalar type for geographic coordinates.
"""
scalar Coordinate

"""
Fuel type classification for vehicles.
"""
enum FuelType {
  PETROL
  DIESEL
  ELECTRIC
  HYBRID
  HYDROGEN
}

"""
Transmission type.
"""
enum TransmissionType {
  MANUAL
  AUTOMATIC
  CVT
}

"""
Sort direction for list queries.
"""
enum SortDirection {
  ASC
  DESC
}

"""
Geographic location with WGS-84 coordinates.
"""
type GeoLocation {
  latitude: Float!
  longitude: Float!
  altitude: Float
  heading: Float
}

"""
Diagnostic trouble code reported by vehicle ECU.
"""
type DiagnosticCode {
  code: String!
  description: String!
  severity: Severity!
  detectedAt: DateTime!
}

enum Severity {
  INFO
  WARNING
  CRITICAL
}

"""
Vehicle entity - the primary resource.
"""
type Vehicle implements Node {
  """Globally unique identifier."""
  id: ID!
  
  """ISO 3779 Vehicle Identification Number."""
  vin: String!
  
  make: String!
  model: String!
  year: Int!
  fuelType: FuelType!
  transmission: TransmissionType
  
  """Current speed in km/h. Null if vehicle is offline."""
  speed: Float
  
  """Current fuel/charge level as percentage (0-100)."""
  fuelLevel: Float
  
  """Current location. Null if GPS unavailable."""
  location: GeoLocation
  
  """Active diagnostic trouble codes."""
  activeDTCs: [DiagnosticCode!]!
  
  """Owner of the vehicle (relationship)."""
  owner: Person
  
  """Service history entries."""
  serviceHistory(first: Int = 10, after: String): ServiceHistoryConnection!
  
  lastUpdated: DateTime!
}

"""
Person entity.
"""
type Person implements Node {
  id: ID!
  name: String!
  email: String!
  vehicles: [Vehicle!]!
}

"""
Node interface for Relay-style global object identification.
"""
interface Node {
  id: ID!
}

"""
Connection type for cursor-based pagination (Relay spec).
"""
type ServiceHistoryConnection {
  edges: [ServiceHistoryEdge!]!
  pageInfo: PageInfo!
  totalCount: Int!
}

type ServiceHistoryEdge {
  cursor: String!
  node: ServiceRecord!
}

type ServiceRecord {
  id: ID!
  date: DateTime!
  description: String!
  mileageKm: Float!
  cost: Float
}

type PageInfo {
  hasNextPage: Boolean!
  hasPreviousPage: Boolean!
  startCursor: String
  endCursor: String
}

# ─── QUERY (Read Operations) ────────────────────────────────

type Query {
  """Fetch a single vehicle by ID."""
  vehicle(id: ID!): Vehicle
  
  """Fetch a vehicle by VIN."""
  vehicleByVin(vin: String!): Vehicle
  
  """List vehicles with filtering and pagination."""
  vehicles(
    filter: VehicleFilter
    sort: VehicleSort
    first: Int = 20
    after: String
  ): VehicleConnection!
  
  """Relay-style node lookup."""
  node(id: ID!): Node
}

input VehicleFilter {
  make: String
  model: String
  yearMin: Int
  yearMax: Int
  fuelType: FuelType
  speedAbove: Float
}

input VehicleSort {
  field: VehicleSortField!
  direction: SortDirection!
}

enum VehicleSortField {
  MAKE
  MODEL
  YEAR
  SPEED
  LAST_UPDATED
}

type VehicleConnection {
  edges: [VehicleEdge!]!
  pageInfo: PageInfo!
  totalCount: Int!
}

type VehicleEdge {
  cursor: String!
  node: Vehicle!
}

# ─── MUTATION (Write Operations) ─────────────────────────────

type Mutation {
  """Create a new vehicle."""
  createVehicle(input: CreateVehicleInput!): CreateVehiclePayload!
  
  """Update an existing vehicle."""
  updateVehicle(input: UpdateVehicleInput!): UpdateVehiclePayload!
  
  """Delete a vehicle."""
  deleteVehicle(id: ID!): DeleteVehiclePayload!
  
  """Send a command to a vehicle."""
  sendCommand(vehicleId: ID!, command: VehicleCommandInput!): CommandPayload!
}

input CreateVehicleInput {
  vin: String!
  make: String!
  model: String!
  year: Int!
  fuelType: FuelType!
  transmission: TransmissionType
  ownerId: ID
}

type CreateVehiclePayload {
  vehicle: Vehicle
  errors: [UserError!]!
}

input UpdateVehicleInput {
  id: ID!
  make: String
  model: String
  fuelType: FuelType
}

type UpdateVehiclePayload {
  vehicle: Vehicle
  errors: [UserError!]!
}

type DeleteVehiclePayload {
  deletedId: ID
  errors: [UserError!]!
}

input VehicleCommandInput {
  lockDoors: Boolean
  setTemperature: Float
  startEngine: Boolean
}

type CommandPayload {
  success: Boolean!
  message: String
  errors: [UserError!]!
}

type UserError {
  field: [String!]
  message: String!
  code: String!
}

# ─── SUBSCRIPTION (Real-time) ────────────────────────────────

type Subscription {
  """Subscribe to real-time vehicle state updates."""
  vehicleStateChanged(vehicleId: ID!): Vehicle!
  
  """Subscribe to new diagnostic codes across fleet."""
  diagnosticAlert(severity: Severity): DiagnosticCode!
}

# ─── SCHEMA ENTRY POINT ─────────────────────────────────────

schema {
  query: Query
  mutation: Mutation
  subscription: Subscription
}
```

### GraphQL as IDL: Key Properties

| Property | GraphQL | Traditional IDL (Protobuf) |
|----------|---------|---------------------------|
| Service definition | `type Query`, `type Mutation`, `type Subscription` | `service`, `rpc` |
| Request type | Operation arguments | Request message |
| Response type | Return type | Response message |
| Streaming | `Subscription` (server→client only) | Bidirectional streaming |
| Type system | Object, Interface, Union, Enum, Scalar, Input | Message, Enum, Oneof |
| Code generation | graphql-codegen, Apollo | protoc + plugins |
| Introspection | Built-in (`__schema`, `__type`) | Reflection service |
| Transport | HTTP (typically POST) | HTTP/2 (gRPC), TCP, etc. |

### Governance

| Aspect | Detail |
|--------|--------|
| Body | GraphQL Foundation (under Linux Foundation) |
| Tier | 5 (Open-source foundation) |
| License | OWF (Open Web Foundation) agreement |
| Specification | https://spec.graphql.org/ |
| Reference impl | graphql-js (JavaScript) |

---

## 02.12 — Franca IDL (Automotive)

### Overview

**Definition:** Franca IDL is an automotive-focused interface definition language originally developed for GENIVI (now COVESA), used to define in-vehicle infotainment (IVI) and AUTOSAR Adaptive service interfaces with bindings to SOME/IP and D-Bus via CommonAPI.

### Syntax

```franca
// VehicleService.fidl (Franca IDL)
package org.automotive.vehicle

interface VehicleService {
    version { major 2 minor 1 }
    
    // Attribute: observable property with getter/setter/notification
    attribute SpeedKmh currentSpeed readonly
    attribute FuelLevelPercent fuelLevel readonly
    attribute GeoLocation currentLocation readonly
    
    // Method: request-response
    method getVehicleInfo {
        in {
            String vehicleId
        }
        out {
            VehicleInfo info
        }
        error VehicleError
    }
    
    // Method: fire-and-forget (no response)
    method reportTelemetry fireAndForget {
        in {
            TelemetryData data
        }
    }
    
    // Broadcast: server-initiated event to subscribers
    broadcast diagnosticAlert {
        out {
            DiagnosticCode dtc
            Severity severity
        }
    }
    
    // Broadcast: selective (only sent to specific subscribers)
    broadcast propertyChanged selective {
        out {
            PropertyId property
            PropertyValue newValue
        }
    }
}

// Type collection (separate from interface)
typeCollection VehicleTypes {
    version { major 1 minor 0 }
    
    typedef SpeedKmh is Float
    typedef FuelLevelPercent is UInt8
    
    enumeration FuelType {
        PETROL = 0
        DIESEL = 1
        ELECTRIC = 2
        HYBRID = 3
    }
    
    enumeration Severity {
        INFO = 0
        WARNING = 1
        CRITICAL = 2
    }
    
    struct GeoLocation {
        Double latitude
        Double longitude
        Float altitude
    }
    
    struct VehicleInfo {
        String vin
        String make
        String model
        UInt16 year
        FuelType fuelType
        GeoLocation location
    }
    
    struct TelemetryData {
        Float speed
        GeoLocation location
        UInt64 timestampMs
    }
    
    struct DiagnosticCode {
        String code
        String description
    }
    
    union PropertyValue {
        Int32 intValue
        Float floatValue
        String stringValue
        Boolean boolValue
    }
    
    enumeration VehicleError {
        NOT_FOUND
        ACCESS_DENIED
        INTERNAL_ERROR
    }
}
```

### Deployment Descriptors (.fdepl)

```franca
// VehicleService.fdepl (SOME/IP deployment)
import "platform:/plugin/org.genivi.commonapi.someip/deployment/CommonAPI-4-SOMEIP_deployment_spec.fdepl"
import "VehicleService.fidl"

define org.genivi.commonapi.someip.deployment for interface org.automotive.vehicle.VehicleService {
    SomeIpServiceID = 0x1234
    
    method getVehicleInfo {
        SomeIpMethodID = 0x0001
        SomeIpReliable = true
    }
    
    method reportTelemetry {
        SomeIpMethodID = 0x0002
        SomeIpReliable = false
    }
    
    broadcast diagnosticAlert {
        SomeIpEventID = 0x8001
        SomeIpEventGroups = { 0x0001 }
    }
    
    attribute currentSpeed {
        SomeIpGetterID = 0x0010
        SomeIpNotifierID = 0x8010
        SomeIpEventGroups = { 0x0002 }
    }
}
```

### CommonAPI C++ Binding

Franca IDL generates C++ code via CommonAPI framework:
- **Proxy (client):** `VehicleServiceProxy` — calls remote methods, subscribes to attributes/broadcasts
- **Stub (server):** `VehicleServiceStubDefault` — implements method handlers, publishes attributes
- **Runtime:** Connects to SOME/IP (via vsomeip) or D-Bus transport at runtime

### Governance

| Aspect | Detail |
|--------|--------|
| Body | COVESA (formerly GENIVI Alliance) |
| Tier | 3 (Industry consortium) |
| License | MPL 2.0 |
| Status | Actively used; partially superseded by ara::com in AUTOSAR Adaptive |

---

## 02.13 — Bond IDL (Microsoft)

### Overview

**Definition:** Bond is a cross-platform serialization framework developed by Microsoft with its own IDL for defining data structures and services, supporting multiple compact binary protocols.

### Syntax

```bond
// vehicle.bond
namespace automotive.vehicle;

enum FuelType
{
    Petrol,
    Diesel,
    Electric,
    Hybrid,
};

struct GeoLocation
{
    0: double latitude;
    1: double longitude;
    2: double altitude = 0.0;
};

struct Vehicle
{
    0: string id;
    1: string vin;
    2: string make;
    3: string model;
    4: int32 year;
    5: FuelType fuel_type = Petrol;
    6: nullable<double> speed_kmh;
    7: nullable<GeoLocation> location;
    8: map<string, string> metadata;
    9: vector<string> active_dtcs;
    10: int64 last_updated_ms;
};

// Service definition (Bond over gRPC)
service VehicleService
{
    Vehicle GetVehicle(GetVehicleRequest);
    nothing UpdateVehicle(Vehicle);
    stream<VehicleUpdate> WatchVehicle(WatchRequest);
};

struct GetVehicleRequest
{
    0: string vehicle_id;
};

struct WatchRequest
{
    0: string vehicle_id;
    1: int32 interval_ms;
};

struct VehicleUpdate
{
    0: Vehicle vehicle;
    1: int64 server_time_ms;
};
```

### Bond Protocols

| Protocol | Type | Size | Speed | Use Case |
|----------|------|------|-------|----------|
| Fast Binary | Binary | Small | Fast | Default, high-performance |
| Compact Binary | Binary | Smaller | Fast | Network-constrained |
| Simple Binary | Binary | Fixed | Fastest | When size is known |
| Simple JSON | Text | Large | Slow | Debugging |
| JSON | Text | Large | Slow | Interop |

### Governance

| Aspect | Detail |
|--------|--------|
| Body | Microsoft |
| Tier | 6 (Vendor-led open source) |
| License | MIT |
| Repository | https://github.com/microsoft/bond |
| Compiler | `gbc` (Bond compiler, Haskell-based) |
| Languages | C++, C#, Python, Java |

---

## 02.14 — Cap'n Proto Schema

### Overview

**Definition:** Cap'n Proto is a data interchange format and capability-based RPC system designed for zero-copy serialization — the in-memory representation IS the wire format. Its schema language defines types with explicit field IDs and supports capability (object-capability) interfaces.

### Syntax

```capnp
# vehicle.capnp
@0xbf5147cbbecf40c1;  # Unique file ID (generated by `capnp id`)

using Cxx = import "/capnp/c++.capnp";
$Cxx.namespace("automotive::vehicle");

enum FuelType {
  petrol @0;
  diesel @1;
  electric @2;
  hybrid @3;
  hydrogen @4;
}

struct GeoLocation {
  latitude @0 :Float64;
  longitude @1 :Float64;
  altitude @2 :Float32 = 0.0;
  heading @3 :Float32;
}

struct Vehicle {
  id @0 :Text;
  vin @1 :Text;
  make @2 :Text;
  model @3 :Text;
  year @4 :UInt16;
  fuelType @5 :FuelType;
  
  # Union (unnamed - only one can be set)
  union {
    noSpeed @6 :Void;  # Default (no speed data)
    speed @7 :Float32;
  }
  
  location @8 :GeoLocation;
  metadata @9 :List(KeyValue);
  activeDtcs @10 :List(Text);
  lastUpdatedNs @11 :Int64;
  
  # Nested group (zero-cost grouping, no wire overhead)
  powertrain :group {
    union {
      unknown @12 :Void;
      ice @13 :ICEEngine;
      electric @14 :ElectricPowertrain;
      hybrid @15 :HybridPowertrain;
    }
  }
}

struct KeyValue {
  key @0 :Text;
  value @1 :Text;
}

struct ICEEngine {
  displacementCc @0 :UInt16;
  cylinders @1 :UInt8;
  horsepower @2 :UInt16;
}

struct ElectricPowertrain {
  batteryKwh @0 :Float32;
  chargePercent @1 :Float32;
  rangeKm @2 :Float32;
}

struct HybridPowertrain {
  ice @0 :ICEEngine;
  electric @1 :ElectricPowertrain;
  evMode @2 :Bool;
}

# Capability-based RPC interface
interface VehicleService {
  getVehicle @0 (id :Text) -> (vehicle :Vehicle);
  listVehicles @1 (limit :UInt32, offset :UInt32) -> (vehicles :List(Vehicle));
  watchVehicle @2 (id :Text) -> (stream :VehicleStream);
  
  # Promise pipelining: can call methods on returned capabilities
  # without waiting for the initial response
  getAdmin @3 () -> (admin :AdminService);
}

interface VehicleStream {
  next @0 () -> (update :Vehicle);
  cancel @1 () -> ();
}

interface AdminService {
  deleteVehicle @0 (id :Text) -> ();
  resetAll @1 () -> ();
}
```

### Promise Pipelining

Cap'n Proto's key differentiator: you can call methods on a not-yet-resolved result:

```
// Without pipelining (3 round trips):
admin = await vehicleService.getAdmin()
result = await admin.deleteVehicle("abc")

// With pipelining (1 round trip):
admin = vehicleService.getAdmin()  // returns a promise
result = admin.deleteVehicle("abc")  // calls on the promise
await result  // single round trip for both calls
```

### Governance

| Aspect | Detail |
|--------|--------|
| Body | Sandstorm.io / Cloudflare (primary users) |
| Tier | 8 (De-facto, community) |
| License | MIT |
| Author | Kenton Varda (also created Protobuf v2 at Google) |
| Repository | https://github.com/capnproto/capnproto |

---

## 02.15 — FlatBuffers Schema (.fbs)

### Overview

**Definition:** FlatBuffers is a zero-copy serialization library with its own schema language (.fbs), designed for performance-critical applications where deserialization overhead must be eliminated — the serialized buffer IS directly accessible without parsing.

### Syntax

```fbs
// vehicle.fbs
namespace automotive.vehicle;

enum FuelType : byte {
  Unknown = 0,
  Petrol = 1,
  Diesel = 2,
  Electric = 3,
  Hybrid = 4,
  Hydrogen = 5
}

enum Severity : byte {
  Info = 0,
  Warning = 1,
  Critical = 2
}

// Struct: fixed-size, inline, no indirection (truly zero-copy)
// All fields required, no optionality, fixed memory layout
struct GeoLocationFixed {
  latitude: float64;
  longitude: float64;
  altitude: float32;
  heading: float32;
}

// Table: variable-size, uses vtable for field access, extensible
table GeoLocation {
  latitude: double;
  longitude: double;
  altitude: float = 0.0;
  heading: float;
  accuracy: float;
}

table ICEEngine {
  displacement_cc: uint16;
  cylinders: ubyte;
  horsepower: uint16;
  rpm: float;
}

table ElectricPowertrain {
  battery_kwh: float;
  charge_percent: float;
  range_km: float;
  motor_power_kw: uint16;
}

table HybridPowertrain {
  ice: ICEEngine;
  electric: ElectricPowertrain;
  ev_mode: bool;
}

// Union: discriminated union (one of several table types)
union PowertrainDetails {
  ICEEngine,
  ElectricPowertrain,
  HybridPowertrain
}

table DiagnosticCode {
  code: string (required);
  description: string;
  severity: Severity = Info;
  detected_at_ns: int64;
}

table Vehicle {
  // Identity
  id: string (required);
  vin: string (required);
  
  // Static attributes
  make: string;
  model: string;
  year: uint16;
  fuel_type: FuelType = Unknown;
  
  // Dynamic state
  speed_kmh: float;
  odometer_km: float;
  fuel_level_percent: float;
  location: GeoLocation;
  
  // Union field (powertrain type + value)
  powertrain: PowertrainDetails;
  
  // Lists
  active_dtcs: [DiagnosticCode];
  metadata: [KeyValue];
  
  // Timestamp
  last_updated_ns: int64;
}

table KeyValue {
  key: string (required);
  value: string;
}

// Root type declaration
root_type Vehicle;

// File identifier (4 bytes, embedded in buffer for type checking)
file_identifier "VEHI";

// File extension hint
file_extension "vfb";
```

### table vs struct in FlatBuffers

| Property | table | struct |
|----------|-------|--------|
| Memory layout | Indirect (vtable + offsets) | Inline, fixed, dense |
| Field access | Offset lookup through vtable | Direct memory offset |
| Optionality | All fields optional by default | All fields always present |
| Extensibility | Can add new fields | Cannot add fields |
| Default values | Absent fields = default | No concept of absent |
| Nesting | Via offset pointer | Inline in parent |
| Performance | One indirection per access | Zero indirection |
| Use case | Top-level message, evolving schema | Fixed geometry (Vec3, Matrix4x4) |

### Zero-Copy Access Pattern

```cpp
// Reading a FlatBuffer (C++) — NO deserialization step
const uint8_t* buffer = receive_bytes();
auto vehicle = GetVehicle(buffer);  // Just a pointer cast + verification

// Direct access — reading from the buffer in-place
auto vin = vehicle->vin()->str();      // String access
float speed = vehicle->speed_kmh();     // Scalar access
auto loc = vehicle->location();         // Nested table access
double lat = loc->latitude();           // Nested field access
```

### Governance

| Aspect | Detail |
|--------|--------|
| Body | Google |
| Tier | 6 (Vendor-led open source) |
| License | Apache 2.0 |
| Repository | https://github.com/google/flatbuffers |
| Compiler | `flatc` |
| Languages | C++, Java, C#, Go, Python, Rust, TypeScript, Kotlin, Swift, Dart, PHP, Lobster |

---

## 02.16 — Avro IDL (.avdl)

### Overview

**Definition:** Apache Avro IDL is a higher-level IDL that compiles to Avro JSON schema (.avsc). It provides a more familiar syntax (C/Java-like) for defining records, enums, protocols (RPC services), and fixed-width types.

### Syntax

```avdl
// vehicle.avdl
@namespace("com.automotive.vehicle")
protocol VehicleProtocol {

  /** Fuel type classification. */
  enum FuelType {
    PETROL, DIESEL, ELECTRIC, HYBRID, HYDROGEN
  }
  
  enum Severity {
    INFO, WARNING, CRITICAL
  }

  /** Geographic location. */
  record GeoLocation {
    double latitude;
    double longitude;
    union { null, float } altitude = null;
    union { null, float } heading = null;
  }

  record ICEEngine {
    int displacement_cc;
    int cylinders;
    int horsepower;
  }
  
  record ElectricPowertrain {
    float battery_kwh;
    float charge_percent;
    float range_km;
  }

  record DiagnosticCode {
    string code;
    string description;
    Severity severity;
    long detected_at_ms;
  }

  /** Main vehicle record. */
  record Vehicle {
    string id;
    string vin;
    string make;
    string model;
    int year;
    FuelType fuel_type;
    union { null, float } speed_kmh = null;
    union { null, GeoLocation } location = null;
    union { null, ICEEngine, ElectricPowertrain } powertrain = null;
    array<DiagnosticCode> active_dtcs = [];
    map<string> metadata = {};
    long last_updated_ms;
  }

  // RPC methods (protocol-level)
  
  /** Get a vehicle by ID. */
  Vehicle getVehicle(string id);
  
  /** List vehicles with pagination. */
  array<Vehicle> listVehicles(int limit, int offset);
  
  /** Update vehicle data. */
  void updateVehicle(Vehicle vehicle);
  
  /** One-way message (no response). */
  @one-way void reportTelemetry(string vehicleId, float speed, GeoLocation location);
}
```

### Avro IDL Compilation

```
vehicle.avdl  →  avro-tools idl vehicle.avdl  →  vehicle.avpr (Avro Protocol JSON)
                                                      ↓
                                               vehicle.avsc (Avro Schema JSON per record)
                                                      ↓
                                               Java/Python/C++ code generation
```

### Governance

| Aspect | Detail |
|--------|--------|
| Body | Apache Software Foundation |
| Tier | 5 (Open-source foundation) |
| License | Apache 2.0 |
| Key feature | Schema evolution via reader/writer schema resolution |
| Schema Registry | Confluent Schema Registry, AWS Glue, Apicurio |

---

## 02.17 — Comprehensive IDL Comparison Matrix

| IDL | Governing Body | Tier | Service Def | Type Def | Primary Compiler | Key Target Languages | Wire Format | Schema Evolution | Transport | Automotive | Cloud | Telecom | Embedded | OS-IPC |
|-----|---------------|------|-------------|----------|-----------------|---------------------|-------------|-----------------|-----------|-----------|-------|---------|----------|--------|
| **Protobuf .proto** | Google / CNCF | 6 | Yes (service/rpc) | Yes (message/enum/oneof) | protoc, buf | All major (10+) | Binary (varint+LEN) | Strong (field tags) | Agnostic (via gRPC) | Secondary | **Primary** | Secondary | Secondary | Secondary |
| **Thrift IDL** | Apache ASF | 5 | Yes (service) | Yes (struct/union/exception) | thrift | All major | Binary/Compact/JSON | Moderate (field IDs) | TCP/HTTP/Framed | Rare | Secondary | Rare | Rare | No |
| **AIDL** | Google / AOSP | 6 | Yes (interface) | Yes (parcelable/enum/union) | aidl | Java, C++, Rust, Kotlin | Binder Parcel | Strong (Stable AIDL) | Binder only | **Primary** | No | No | Rare | **Primary** |
| **FIDL** | Google / Fuchsia | 6 | Yes (protocol) | Yes (struct/table/union) | fidlc + fidlgen | C++, Rust, Dart, Go | Binary (envelope) | Strong (table ordinals) | Zircon channels | No | No | No | Rare | **Primary** |
| **OMG IDL** | OMG | 3 | Yes (interface) | Yes (struct/union/enum) | IDL compilers | C++, Java, Python, Ada | CDR (GIOP/IIOP) | Limited | IIOP/TCP | Secondary | Rare | Rare | Rare | No |
| **DDS IDL** | OMG | 3 | No (pub/sub topics) | Yes (@topic struct) | DDS tools | C++, Java, Python, C# | XCDR2 | Strong (X-Types) | RTPS/UDP/TCP/SHM | **Primary** | Secondary | Rare | Secondary | No |
| **ASN.1** | ISO / ITU-T | 1/2 | No | Yes (SEQUENCE/CHOICE/etc) | asn1c, OSS | C, C++, Java, Python | BER/DER/PER/XER/OER | Strong (extensibility) | Agnostic | Rare | Rare | **Primary** | **Primary** | No |
| **WSDL** | W3C | 2 | Yes (portType/binding) | Yes (via XSD) | wsdl2java, svcutil | Java, .NET | XML (SOAP) | Limited | HTTP (SOAP) | No | Rare (legacy) | Rare | No | No |
| **Smithy** | Amazon | 6 | Yes (service/operation) | Yes (structure/union) | smithy-build | All (AWS SDK gen) | JSON/XML | Strong (traits) | HTTP/REST | No | **Primary** | No | No | No |
| **GraphQL SDL** | GraphQL Foundation | 5 | Yes (Query/Mutation/Sub) | Yes (type/interface/union) | graphql-codegen | TypeScript, Java, Kotlin, Swift | JSON (response) | Partial (additive) | HTTP | No | **Primary** | No | No | No |
| **Franca IDL** | COVESA | 3 | Yes (interface) | Yes (struct/enum/union) | CommonAPI tools | C++ | SOME/IP or D-Bus | Partial | SOME/IP, D-Bus | **Primary** | No | No | Secondary | No |
| **Bond IDL** | Microsoft | 6 | Yes (service) | Yes (struct/enum) | gbc | C++, C#, Python, Java | Fast/Compact Binary | Strong (field IDs) | gRPC/custom | No | Secondary | No | No | No |
| **Cap'n Proto** | Community | 8 | Yes (interface) | Yes (struct/enum/group) | capnp | C++, Rust, Python, Java | Zero-copy binary | Strong (field IDs) | Custom RPC | No | Secondary | No | Secondary | No |
| **FlatBuffers .fbs** | Google | 6 | No (tables only) | Yes (table/struct/enum/union) | flatc | All major (12+) | Zero-copy binary | Moderate (tables only) | Agnostic | Secondary | Secondary | No | Secondary | No |
| **Avro IDL** | Apache ASF | 5 | Yes (protocol) | Yes (record/enum/fixed) | avro-tools | Java, Python, C++, Go | Avro binary/JSON | Strong (registry) | HTTP/socket | No | **Primary** | No | No | No |

---

## 02.18 — Cross-References

| Topic | Volume |
|-------|--------|
| How IDL-defined types get serialized to bytes | Volume 04 — Serialization Formats |
| Binary encoding rules for ASN.1 (BER/DER/PER) | Volume 05 — Binary Encoding Rules |
| RPC frameworks built on IDLs (gRPC, Thrift RPC) | Volume 06 — RPC Frameworks |
| IPC mechanisms that IDLs target (Binder, Zircon) | Volume 07 — IPC Mechanisms |
| API contracts derived from IDLs (OpenAPI, AsyncAPI) | Volume 08 — API Contract Systems |
| Zero-copy formats from FlatBuffers and Cap'n Proto | Volume 09 — Zero-Copy Systems |
| Schema evolution rules for IDLs | Volume 10 — Schema Evolution |
| Industry-specific IDL usage | Volume 11 — Industry-Specific Standards |
| Governance tiers for each IDL | Volume 12 — Governance & Standards |
| Compilers and code generation tools | Volume 13 — Tooling & Code Generation |
| Transport layer bindings for RPC IDLs | Volume 14 — Transport & Runtime Binding |
| Language binding coverage per IDL | Volume 15 — Cross-Platform Interoperability |

---

*End of Volume 02 — Interface Definition Languages*
