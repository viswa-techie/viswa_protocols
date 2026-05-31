# VOLUME 10 — SCHEMA EVOLUTION & COMPATIBILITY

## Universal Data Contract, Serialization, IDL & Interoperability Architecture Encyclopedia

---

## 10.0 — Why Schema Evolution is the Hardest Problem in Distributed Systems

In any long-lived system with multiple independently-deployed services, the schema will change. The question is not *if* but *how* to evolve without breaking running systems.

**The fundamental tension:**

| Force | Pulls Toward |
|-------|-------------|
| New features | Adding fields, changing types, new operations |
| Running producers | Cannot update all at once (rolling deploy) |
| Running consumers | Must continue reading old AND new data |
| Stored data | Historical records written under old schema |
| Independent deployment | Services updated at different times |
| Multiple versions coexist | Old + new simultaneously in production |

**Consequences of uncontrolled schema change:**
- Silent data corruption (misinterpreted field values)
- Service crashes (missing required fields)
- Data loss (ignored unknown fields that were important)
- Cascading failures (one broken consumer blocks a pipeline)

---

## 10.1 — Formal Compatibility Definitions

### Backward Compatibility

**Definition:** A NEW schema (reader) can read data written with an OLD schema (writer).

```
Producer (old schema v1) ──writes──► Data ──reads──► Consumer (new schema v2) ✓
```

**Upgrade pattern:** Consumers upgrade first.

**Rules:**
- ✔ Add new optional fields with defaults
- ✔ Widen types (int32 → int64)
- ✘ Add new required fields without defaults
- ✘ Remove fields old producers still write (consumer expects them)

### Forward Compatibility

**Definition:** An OLD schema (reader) can read data written with a NEW schema (writer).

```
Producer (new schema v2) ──writes──► Data ──reads──► Consumer (old schema v1) ✓
```

**Upgrade pattern:** Producers upgrade first.

**Rules:**
- ✔ Add new fields (old readers ignore unknown fields)
- ✘ Remove fields that old readers depend on
- ✘ Change field type in incompatible way

### Full Compatibility

**Definition:** Both backward AND forward compatible simultaneously.

```
Any version writer ──writes──► Data ──reads──► Any version reader ✓
```

**Rules:** Only additive changes with defaults; no removal of fields with dependents.

### Transitive Variants

| Mode | Guarantee |
|------|-----------|
| BACKWARD | Compatible with immediately previous version |
| BACKWARD_TRANSITIVE | Compatible with ALL previous versions |
| FORWARD | Immediately next version can read |
| FORWARD_TRANSITIVE | ALL future versions can read |
| FULL | Both directions, latest only |
| FULL_TRANSITIVE | Both directions, ALL versions |

---

## 10.2 — Protobuf Evolution Rules

### Tag-Based Identity

The field NUMBER (tag) is the binary identity. Field names are irrelevant to wire format.

```protobuf
message Vehicle {
  string vin = 1;           // tag 1 = string
  float speed_kmh = 2;     // tag 2 = float
  // Field 3 was removed:
  reserved 3;
  reserved "old_field_name";
  // New field:
  optional string model = 4;  // tag 4 (safe: new number)
}
```

### Wire Type Compatibility Matrix

Fields with the **same wire type** can be type-changed without breaking the wire format:

| Wire Type | Value | Compatible Types |
|-----------|-------|-----------------|
| 0 (Varint) | 0 | int32, int64, uint32, uint64, sint32, sint64, bool, enum |
| 1 (64-bit) | 1 | fixed64, sfixed64, double |
| 2 (Length-delimited) | 2 | string, bytes, embedded message, packed repeated |
| 5 (32-bit) | 5 | fixed32, sfixed32, float |

**Safe type changes:**
- int32 ↔ int64 (varint naturally extends)
- sint32 ↔ sint64 (ZigZag encoding extends)
- string ↔ bytes (wire-level identical; validation differs)
- bool ↔ int32 (both varint; 0/1 semantics)

**Unsafe type changes:**
- int32 → fixed32 (wire type 0 → wire type 5)
- string → int32 (wire type 2 → wire type 0)
- float → double (wire type 5 → wire type 1)

### Reserved Fields

```protobuf
message Foo {
  reserved 3, 5, 8 to 12;       // prevent field number reuse
  reserved "old_name", "temp";   // prevent name reuse (compile error)
}
```

### Proto3 Default Values

| Type | Zero Value | Presence Detection |
|------|-----------|-------------------|
| int32/64 | 0 | Only with `optional` keyword |
| float/double | 0.0 | Only with `optional` keyword |
| bool | false | Only with `optional` keyword |
| string | "" | Only with `optional` keyword |
| bytes | empty | Only with `optional` keyword |
| enum | first value (0) | Only with `optional` keyword |
| message | null | Always has presence (not set vs empty) |

### Oneof Evolution

- ✔ Add new fields to oneof (safe)
- ✘ Move field into/out of oneof (wire-compatible but semantic change)
- ✘ Reuse field numbers within oneof

### Enum Evolution

| Proto version | Unknown enum value behavior |
|--------------|---------------------------|
| proto3 (OPEN) | Preserved as integer; round-trips correctly |
| proto2 (CLOSED) | Placed in unknown fields set; may cause error |

---

## 10.3 — Avro Evolution Rules

### Reader/Writer Schema Resolution

Avro's core evolution mechanism: the **reader schema** and **writer schema** are compared at read time.

```
Writer (schema v1)     →  [data on wire]  →  Reader (schema v2)
                                                ↓
                                         Schema Resolution:
                                         - Field in writer but not reader → ignored
                                         - Field in reader but not writer → use default
                                         - Both have field → type promotion rules
```

### Resolution Algorithm

| Condition | Action |
|-----------|--------|
| Writer has field, reader doesn't | Field data skipped/ignored |
| Reader has field, writer doesn't | Reader's DEFAULT value used |
| Reader has field WITHOUT default, writer doesn't have it | **ERROR** (schema incompatible) |
| Both have field, same type | Direct read |
| Both have field, promotable type | Promote (widen) |

### Type Promotion Table (Safe Widening)

| Writer Type | Can Promote To |
|-------------|---------------|
| int | long, float, double |
| long | float, double |
| float | double |
| string | bytes |
| bytes | string |

### Backward-Compatible Changes

- ✔ Add field **with** default value
- ✔ Remove field that **has** a default value
- ✔ Type promotion (int → long)
- ✘ Add field **without** default value (reader can't resolve missing data)
- ✘ Remove field without default (reader crashes)
- ✘ Narrow type (long → int)

### Schema Fingerprinting

```
Canonical JSON → hash (Rabin-64, MD5, SHA-256) → fingerprint
```
Used by Schema Registry to identify schemas without full comparison.

---

## 10.4 — Thrift Evolution Rules

### ID-Based Identity (Like Protobuf)

```thrift
struct Vehicle {
  1: required string vin          // NEVER remove required fields
  2: optional double speed_kmh
  3: optional string model        // new field: safe
  // Field 4 was removed — never reuse ID 4
}
```

### Rules

| Change | Safe? | Notes |
|--------|-------|-------|
| Add optional field (new ID) | ✔ | Old readers skip unknown IDs |
| Remove optional field | ✔ | New readers handle missing |
| Remove required field | ✘ | **Breaking** (old readers crash) |
| Change field type | ✘ | Binary incompatible |
| Change field ID | ✘ | Identity changes |
| Rename field | ✔ | ID-based, name irrelevant to wire |

### Why `required` is Deprecated

Once a field is `required`, it can NEVER be removed. This makes `required` a permanent commitment that prevents evolution. Best practice: use `optional` everywhere.

---

## 10.5 — JSON Schema Evolution

### No Built-in Mechanism

JSON Schema describes structure but has no evolution protocol, no reader/writer resolution, and no schema registry integration by default.

### Evolution Strategy: `additionalProperties`

| Setting | Effect on Evolution |
|---------|-------------------|
| `additionalProperties: true` (default) | Open schema — consumers MUST tolerate unknown fields |
| `additionalProperties: false` | Closed schema — adding any property is BREAKING |

### Breaking Changes

| Change | Breaking? | Reason |
|--------|-----------|--------|
| Add required property | **Yes** | Old data lacks it; validation fails |
| Remove property | **Yes** | Consumers depending on it break |
| Change property type | **Yes** | Validation/parsing fails |
| Reduce enum values | **Yes** | Existing data may have removed value |
| Add minimum/maximum | **Yes** | Existing data may violate constraint |
| Add optional property | No | If `additionalProperties: true` |
| Widen enum (add values) | No | Consumers should handle unknowns |
| Relax constraint | No | More data now valid |

### Versioning by URI

```json
{
  "$id": "https://example.com/schemas/vehicle/v2.json",
  "$schema": "https://json-schema.org/draft/2020-12/schema"
}
```

---

## 10.6 — OpenAPI / REST API Versioning

### Non-Breaking Changes

| Change | Why Non-Breaking |
|--------|-----------------|
| Add optional query parameter | Existing clients don't send it; server uses default |
| Add new response field | Lenient parsers ignore unknown fields |
| Add new endpoint | Existing clients don't use it |
| Add new enum value in response | Consumers should handle unknowns |
| Expand acceptable input | More requests now succeed |
| Add new HTTP status code | Well-behaved clients handle unknown 4xx/5xx |

### Breaking Changes

| Change | Why Breaking |
|--------|-------------|
| Remove/rename endpoint | Existing client requests fail (404) |
| Change HTTP method | Clients use wrong verb |
| Add required request parameter | Old requests now fail (400) |
| Remove response field | Client parsing fails |
| Change response field type | Client type assertion fails |
| Remove enum value from request | Client sends now-invalid value |
| Change authentication | Client requests rejected (401) |

### Sunset Header (RFC 8594)

```http
HTTP/1.1 200 OK
Sunset: Sat, 01 Jan 2026 00:00:00 GMT
Deprecation: Mon, 01 Jun 2025 00:00:00 GMT
Link: <https://api.example.com/v2/vehicles>; rel="successor-version"
```

### Evolution Tools

| Tool | Purpose |
|------|---------|
| **oasdiff** | OpenAPI diff + breaking change detection (CLI) |
| **openapi-diff** | Visual HTML diff |
| **Spectral** | Linting + deprecation rules |
| **Bump.sh** | API change management platform |
| **Optic** | API traffic-based contract verification |

---

## 10.7 — Confluent Schema Registry

### Architecture

```
┌──────────────┐                        ┌───────────────────┐
│   Producer   │──register schema──────►│  Schema Registry  │
│              │◄──returns schema ID─────│  (REST service)   │
│              │                         │                   │
│  writes:     │                         │  Stores:          │
│  [0x00][ID]  │                         │  - schemas        │
│  [avro data] │                         │  - subjects       │
└──────────────┘                         │  - compatibility  │
                                         └───────────────────┘
┌──────────────┐                                │
│   Consumer   │──lookup schema by ID──────────►│
│              │◄──returns schema───────────────│
│  resolves:   │
│  writer schema (from ID) vs reader schema
└──────────────┘
```

### Wire Format (Confluent)

```
[0x00]           ← magic byte (1 byte)
[schema ID]      ← 4 bytes, big-endian unsigned int
[encoded data]   ← Avro binary / Protobuf / JSON
```

### Subject Naming Strategies

| Strategy | Subject Name | Use Case |
|----------|-------------|----------|
| TopicNameStrategy (default) | `{topic}-value` | One schema per topic |
| RecordNameStrategy | `{fully.qualified.Record}` | Multiple record types per topic |
| TopicRecordNameStrategy | `{topic}-{Record}` | Multiple record types, topic-scoped |

### REST API

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/subjects/{subject}/versions` | POST | Register new schema version |
| `/subjects/{subject}/versions` | GET | List all versions |
| `/subjects/{subject}/versions/{version}` | GET | Get specific version |
| `/schemas/ids/{id}` | GET | Get schema by global ID |
| `/compatibility/subjects/{subject}/versions/latest` | POST | Test compatibility |
| `/config/{subject}` | PUT | Set compatibility level |
| `/subjects/{subject}` | DELETE | Soft-delete subject |

### Compatibility Levels

| Level | Guarantees |
|-------|-----------|
| BACKWARD | New schema reads old data |
| BACKWARD_TRANSITIVE | New schema reads ALL old data |
| FORWARD | Old schema reads new data |
| FORWARD_TRANSITIVE | ALL old schemas read new data |
| FULL | Both backward + forward (latest) |
| FULL_TRANSITIVE | Both backward + forward (all versions) |
| NONE | No compatibility check |

---

## 10.8 — gRPC / Protobuf API Versioning Patterns

### Package-Based Versioning

```protobuf
// api/v1/vehicle.proto
package automotive.api.v1;
service VehicleService {
  rpc GetVehicle (GetVehicleRequest) returns (Vehicle);
}

// api/v2/vehicle.proto (incompatible changes)
package automotive.api.v2;
service VehicleService {
  rpc GetVehicle (GetVehicleRequest) returns (VehicleResponse);  // different response
  rpc StreamTelemetry (TelemetryRequest) returns (stream TelemetryEvent);  // new
}
```

Both served from same server:
- `/automotive.api.v1.VehicleService/GetVehicle`
- `/automotive.api.v2.VehicleService/GetVehicle`

### Buf Tool

| Command | Purpose |
|---------|---------|
| `buf lint` | Enforce style rules on .proto files |
| `buf breaking` | Detect breaking changes vs previous (committed) version |
| `buf build` | Compile to FileDescriptorSet |
| `buf generate` | Code generation with configurable plugins |
| `buf push` | Push schema to Buf Schema Registry (BSR) |

### Buf Breaking Change Rules

| Category | Examples |
|----------|---------|
| FILE | Package rename, remove file |
| MESSAGE | Remove message, change message name |
| FIELD | Remove field, change field type, change field number |
| ENUM | Remove enum, remove enum value |
| SERVICE | Remove service, change service name |
| METHOD | Remove method, change request/response type, change streaming mode |

---

## 10.9 — AIDL Stable Versioning (Android)

### Problem

Android vendor partition and system partition updated independently. HAL interfaces must remain stable across partition version differences.

### Mechanism

```
@VintfStability
interface IVehicle {
    VehicleState getState(in String vehicleId);
    // v2 addition:
    SensorData[] getSensorData(in String vehicleId);
}
```

### Rules

| Change | Allowed? | Notes |
|--------|----------|-------|
| Add new method | ✔ | Old clients get UNKNOWN_TRANSACTION |
| Add optional parcelable field | ✔ | Old readers get default |
| Remove method | ✘ | Breaking |
| Rename method | ✘ | Breaking |
| Change method signature | ✘ | Breaking |
| Remove parcelable field | ✘ | Breaking |

### Hash-Based Freeze

```bash
# Freeze API for release:
aidl --check-api current/IVehicle.aidl frozen/IVehicle.aidl

# CI rejects any modification to frozen API surface
```

---

## 10.10 — Breaking Change Taxonomy

### Structural Breaking Changes (Tool-Detectable)

| Change | Effect |
|--------|--------|
| Type change (string → int) | Parser/deserializer error |
| Nullability (non-null → nullable) | Consumer null-pointer crash |
| Cardinality (single → array) | Type assertion failure |
| Name change (field rename) | Field not found |
| Removal (field, method, endpoint) | Missing data / 404 |

### Semantic Breaking Changes (NOT Tool-Detectable)

| Change | Example |
|--------|---------|
| Behavior change | Sort order of results changed |
| Unit change | speed field changed from km/h to m/s |
| Timezone change | Timestamps now local instead of UTC |
| Null semantics | null means "unknown" → now means "zero" |
| Precision change | float32 → float64 (wider, but calculation results differ) |

### Additive Breaking Changes

| Change | Why Breaking |
|--------|-------------|
| Add required field/parameter | Old data/requests lack it |
| Add enum value (exhaustive switch) | `default:` case not handled |
| Add required auth | Old clients rejected |

### Contract-Level Breaking Changes

| Change | Effect |
|--------|--------|
| Error code semantics changed | Client error handling wrong |
| Rate limiting changed | Clients throttled unexpectedly |
| Pagination behavior changed | Client misses data |
| Idempotency guarantee removed | Client retries cause duplicates |

---

## 10.11 — Schema Evolution Comparison Matrix

| Technology | Add Optional | Remove Field | Rename | Change Type | Add Enum | Registry | Versioning | Strategy |
|-----------|-------------|-------------|--------|------------|---------|---------|-----------|----------|
| **Protobuf** | ✔ (new tag) | ✔ (reserved) | ✘ (tag-based) | Conditional (wire type) | ✔ (OPEN) | Confluent/Buf | Package | Tag-based |
| **Avro** | ✔ (with default) | ✔ (if defaulted) | ✘ | Promotion only | ✔ | Confluent/Glue | None native | Reader/Writer |
| **Thrift** | ✔ (new ID) | ✔ (optional only) | ✘ | ✘ | ✔ | No | None | ID-based |
| **JSON Schema** | ✔ (open schema) | ✔ | ✔ (breaking) | ✘ | ✔ | Apicurio | URI | URI-based |
| **OpenAPI** | ✔ | ✔ | ✘ | ✘ | ✔ | Apicurio | URL/header | Sunset |
| **GraphQL** | ✔ | ✔ (deprecate) | ✘ | ✘ | ✔ | Apollo/custom | None formal | Deprecation |
| **FlatBuffers** | ✔ (vtable entry) | ✔ (deprecated) | ✔ (by index) | ✘ | ✔ | No | File ID | vtable |
| **Cap'n Proto** | ✔ (end of struct) | ✔ | ✔ (no binary effect) | ✘ | ✔ | No | None | Position-based |
| **Stable AIDL** | ✔ | ✘ | ✘ | ✘ | ✔ | No | @VintfStability | Hash-frozen |
| **YANG** | ✔ (augment) | ✘ (deprecate) | ✘ | ✘ | ✔ | No | Module revision | Augment |

---

## 10.12 — Anti-Patterns: What Kills Compatibility

| Anti-Pattern | Problem | Solution |
|-------------|---------|----------|
| **No defaults on new fields** | Reader crashes when old data lacks field | Always specify defaults |
| **`required` fields** | Can NEVER be removed | Use `optional` everywhere |
| **Reusing deleted field numbers** | New field misinterpreted as old type | Use `reserved` |
| **Changing field semantics silently** | Consumers interpret data wrongly | Version bump + migration |
| **Tight coupling to field names** | Rename breaks all consumers | Use numeric identifiers |
| **No schema registry** | No enforcement of compatibility | Deploy registry + CI checks |
| **Testing only latest version** | Old producers break silently | Transitive compatibility checks |
| **Exhaustive enum switches** | Adding enum value breaks consumers | Always have `default:` case |
| **Removing without deprecation** | Consumers break immediately | Deprecate → wait → remove |

---

## 10.13 — Cross-References

| Topic | Volume |
|-------|--------|
| Schema systems (JSON Schema, XSD, Avro) | Volume 03 — Schema Systems |
| IDLs with evolution rules (Protobuf, FlatBuffers) | Volume 02 — IDLs |
| API governance and standards bodies | Volume 12 — Governance & Standards |
| API contract systems (OpenAPI versioning) | Volume 08 — API Contracts |
| Serialization formats with evolution support | Volume 04 — Serialization |

---

*End of Volume 10 — Schema Evolution & Compatibility*
