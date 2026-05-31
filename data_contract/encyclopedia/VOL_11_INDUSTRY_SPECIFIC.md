# VOLUME 11 — INDUSTRY-SPECIFIC DATA CONTRACTS

## Universal Data Contract, Serialization, IDL & Interoperability Architecture Encyclopedia

---

## 11.0 — Why Industry Verticals Need Domain-Specific Standards

Every industry vertical imposes unique constraints on data contracts:

| Constraint | Industry Example |
|-----------|-----------------|
| Real-time latency (<1 ms) | Automotive ADAS, HFT trading |
| Regulatory compliance | Healthcare (HIPAA), Finance (MiFID II) |
| Safety certification | Avionics (DO-178C), Automotive (ISO 26262) |
| Constrained resources | IoT (CBOR on 8-bit MCU with 32KB RAM) |
| Long-lived deployments | Telecom (20-year equipment lifecycle) |
| Interoperability mandate | Healthcare (FHIR), Finance (ISO 20022) |
| Deterministic behavior | Safety-critical (no dynamic allocation) |

Generic formats (JSON, Protobuf) cover 80% of needs, but industry standards encode domain semantics, interoperability requirements, and regulatory mandates that generic formats cannot.

---

## 11.1 — Automotive / ADAS / AAOS

### AUTOSAR Classic Platform (CP)

| Property | Value |
|----------|-------|
| Communication | Signal-based (PDU, I-Signal, I-PDU) |
| Configuration | ARXML (XML, XSD-validated) |
| Transport | CAN, LIN, FlexRay, Automotive Ethernet |
| Data types | boolean, uint8–64, sint8–64, float32/64, arrays, records |
| Governance | AUTOSAR consortium — Tier 3 |

### AUTOSAR Adaptive Platform (AP)

| Element | Description |
|---------|------------|
| ara::com | Service-oriented communication API |
| Method | Request-response (RPC-like) |
| Event | Publisher-subscriber data stream |
| Field | Stateful attribute with Get/Set + notifier |
| Bindings | SOME/IP (primary), DDS, IPC (shared memory) |
| Generation | From Franca IDL or ARXML manifests |

### SOME/IP

| Property | Value |
|----------|-------|
| Full name | Scalable service-Oriented MiddlewarE over IP |
| Transport | UDP (events), TCP (methods) |
| Governance | AUTOSAR — Tier 3 |

**Header Format (16 bytes):**
```
┌─────────────┬─────────────┬──────────────────────────────┐
│ Service ID  │ Method ID   │ Length                        │
│ (2 bytes)   │ (2 bytes)   │ (4 bytes)                    │
├─────────────┼─────────────┼──────────┬───────────────────┤
│ Client ID   │ Session ID  │Proto Ver │Interface Ver│MsgType│RetCode│
│ (2 bytes)   │ (2 bytes)   │(1 byte)  │(1 byte)     │(1 B)  │(1 B)  │
└─────────────┴─────────────┴──────────┴─────────────┴───────┴───────┘
```

**Message Types:** REQUEST, REQUEST_NO_RETURN, NOTIFICATION, RESPONSE, ERROR

**SOME/IP-SD (Service Discovery):** Find/Offer/Subscribe over UDP multicast (224.0.0.1)

### DDS in Automotive

| Property | Value |
|----------|-------|
| Standard | OMG DDS (Data Distribution Service) — Tier 3 |
| Wire protocol | RTPS (Real-Time Publish Subscribe) over UDP |
| Model | Data-centric publish-subscribe (DCPS) |
| Implementations | FastDDS, CycloneDDS, RTI Connext, OpenDDS |
| ADAS use | LiDAR point clouds, camera frames, RADAR tracks |

**Key QoS Policies:**
| Policy | Purpose |
|--------|---------|
| Reliability | BEST_EFFORT or RELIABLE |
| Durability | VOLATILE, TRANSIENT_LOCAL, TRANSIENT, PERSISTENT |
| Deadline | Maximum inter-sample interval |
| LatencyBudget | Acceptable latency hint |
| History | KEEP_LAST(N) or KEEP_ALL |

### COVESA Vehicle Signal Specification (VSS)

| Property | Value |
|----------|-------|
| Model | Hierarchical signal tree (Vehicle.Speed, Vehicle.Body.Lights.Beam.High) |
| Node types | branch, sensor, actuator, attribute |
| Exporters | JSON, YAML, CSV, Protobuf (vss-tools) |
| Governance | COVESA — Tier 3 |

### Android Automotive OS (AAOS)

| Component | Technology |
|-----------|-----------|
| Vehicle HAL | Stable AIDL (`android.hardware.automotive.vehicle`) |
| Properties | VehicleProperty enum (speed, gear, fuel, HVAC) |
| Area types | GLOBAL, WINDOW, DOOR, SEAT, MIRROR, WHEEL |
| App access | CarPropertyManager (framework API) |

---

## 11.2 — Telecom / 3GPP / 5G

### ASN.1 in 3GPP

| Protocol | Interface | Encoding | Spec |
|----------|-----------|----------|------|
| RRC | Air interface (UE ↔ gNB) | Unaligned PER | TS 38.331 |
| NAS | Core (UE ↔ AMF) | Unaligned PER | TS 24.501 |
| NGAP | NG (gNB ↔ AMF) | Unaligned PER | TS 38.413 |
| XnAP | Xn (gNB ↔ gNB) | Unaligned PER | TS 38.423 |
| E1AP | E1 (CU-CP ↔ CU-UP) | Unaligned PER | TS 38.463 |
| F1AP | F1 (CU ↔ DU) | Unaligned PER | TS 38.473 |
| S1AP (LTE) | S1 (eNB ↔ MME) | Unaligned PER | TS 36.413 |

### Diameter Protocol

| Property | Value |
|----------|-------|
| Standard | IETF RFC 6733 |
| Encoding | AVP (Code + Flags + Length + Value) |
| Transport | TCP or SCTP |
| Applications | Cx (IMS), Gx (policy), Gy (charging), S6a (HSS) |

### YANG / NETCONF / gNMI

| Protocol | Transport | Encoding | Governance |
|----------|-----------|----------|-----------|
| NETCONF | SSH | XML | IETF (RFC 6241) |
| RESTCONF | HTTPS | JSON/XML | IETF (RFC 8040) |
| gNMI | gRPC/HTTP2 | Protobuf | OpenConfig |

### SNMP / SMIv2

| Property | Value |
|----------|-------|
| MIB language | SMIv2 (ASN.1 macros, RFC 2578) |
| PDU types | GetRequest, GetNextRequest, GetBulkRequest, SetRequest, Trap |
| Transport | UDP port 161 (agent), 162 (traps) |
| OID | Hierarchical identifier (1.3.6.1.2.1.2.2.1.1 = ifIndex) |

---

## 11.3 — Robotics / ROS / ROS2

### ROS2 Message System

| Component | Format | Example |
|-----------|--------|---------|
| Messages | `.msg` | `float64 x\nfloat64 y\nfloat64 z` |
| Services | `.srv` | `request\n---\nresponse` |
| Actions | `.action` | `goal\n---\nresult\n---\nfeedback` |

### Standard Message Packages

| Package | Key Types |
|---------|-----------|
| `std_msgs` | Header, Bool, Int32, Float64, String |
| `sensor_msgs` | Image, PointCloud2, LaserScan, Imu, NavSatFix, CameraInfo |
| `geometry_msgs` | Pose, Twist, Transform, Vector3, Quaternion |
| `nav_msgs` | Odometry, Path, OccupancyGrid |
| `tf2_msgs` | TFMessage (coordinate frame transforms) |

### ROS2 Middleware

| Layer | Options |
|-------|---------|
| RMW (ROS Middleware) | rmw_fastrtps_cpp, rmw_cyclonedds_cpp, rmw_iceoryx_cpp |
| DDS QoS | Reliability, Durability, History, Deadline, Liveliness |
| Code gen | rosidl_generator_cpp, rosidl_generator_py |

---

## 11.4 — Cloud Microservices

### Dominant Stack

| Layer | Technology |
|-------|-----------|
| Internal RPC | gRPC + Protobuf |
| Event streaming | Kafka + Avro + Schema Registry |
| External API | OpenAPI + REST + JSON |
| Graph API | GraphQL (GitHub, Shopify, Meta) |
| Event envelope | CloudEvents |
| Async catalog | AsyncAPI |
| Service mesh | Envoy xDS API (Protobuf) |

### Key Patterns

| Pattern | Technologies |
|---------|-------------|
| API Gateway | Kong, AWS API GW, Azure APIM (validates against OpenAPI) |
| Schema Registry | Confluent (Avro/Proto/JSON Schema), Apicurio |
| Contract testing | Pact (consumer-driven), Schemathesis (fuzzing) |
| REST-to-gRPC | grpc-gateway (transcoding reverse proxy) |

---

## 11.5 — Enterprise Integration

### EDI / ANSI X12

| Property | Value |
|----------|-------|
| Purpose | Electronic business document exchange (orders, invoices) |
| Format | Segment-based text (ISA/GS/ST delimiters) |
| HIPAA | X12 837 (claims), 835 (payments), 270/271 (eligibility) |
| Governance | ASC X12 — Tier 3 |

### HL7 / FHIR (Healthcare)

| Standard | Format | Transport | Status |
|----------|--------|-----------|--------|
| HL7 v2.x | Pipe-delimited segments (MSH, PID, OBR) | MLLP/TCP | Legacy dominant |
| HL7 v3 | XML (RIM-based) | SOAP/HTTP | Limited adoption |
| FHIR R4+ | JSON/XML resources | RESTful HTTP | Modern standard |

**FHIR Resources:** Patient, Encounter, Observation, DiagnosticReport, Medication, AllergyIntolerance, Condition, Procedure

### ISO 20022 (Financial Messaging)

| Property | Value |
|----------|-------|
| Format | XML (MX messages), XSD-defined |
| Key messages | pacs.008 (credit transfer), pain.001 (payment initiation), camt.053 (statement) |
| Migration | SWIFT MT → ISO 20022 MX (2022-2025 transition) |
| Governance | ISO TC68/SC9 — Tier 1 |

### FIX Protocol

| Variant | Format | Use Case |
|---------|--------|----------|
| FIX tag-value | Text (`35=D\x0149=SENDER\x01`) | Order management |
| FIXML | XML | Regulatory reporting |
| SBE | Binary (zero-copy) | Market data, HFT |

---

## 11.6 — Security / PKI / Certificates

### X.509 Certificates (ASN.1 DER)

| Field | Purpose |
|-------|---------|
| version | v1, v2, or v3 |
| serialNumber | Unique per CA |
| signature | Algorithm OID + parameters |
| issuer | CA distinguished name |
| validity | notBefore, notAfter |
| subject | Certificate holder DN |
| subjectPublicKeyInfo | Algorithm + public key |
| extensions (v3) | SAN, BasicConstraints, KeyUsage, EKU |

### JOSE (JSON-based Security)

| Standard | Purpose | RFC |
|----------|---------|-----|
| JWS | JSON Web Signature | RFC 7515 |
| JWE | JSON Web Encryption | RFC 7516 |
| JWK | JSON Web Key | RFC 7517 |
| JWT | JSON Web Token (claims) | RFC 7519 |

### COSE (CBOR-based Security)

| Property | Value |
|----------|-------|
| Standard | RFC 9052 (supersedes RFC 8152) |
| Format | CBOR structures for sign/encrypt |
| Use | FIDO2/WebAuthn, IoT device attestation, IETF ACE |

---

## 11.7 — IoT / Embedded

### MQTT + Payload Formats

| Property | Value |
|----------|-------|
| Transport | TCP (port 1883), TLS (8883), WebSocket |
| Payload | Application-defined (JSON, CBOR, Protobuf, custom binary) |
| QoS | 0 (at most once), 1 (at least once), 2 (exactly once) |
| MQTT 5.0 | Content type header, message expiry, topic aliases |

### CoAP (RFC 7252)

| Property | Value |
|----------|-------|
| Transport | UDP (constrained devices) |
| Model | REST-like (GET/PUT/POST/DELETE on resources) |
| Encoding | CBOR, JSON, SenML |
| Observe | Server pushes resource updates |

### LwM2M (OMA SpecWorks)

| Property | Value |
|----------|-------|
| Model | Object/Resource hierarchy for device management |
| Encoding | TLV (binary), JSON, CBOR, SenML |
| Objects | 0=Security, 1=Server, 3=Device, 4=Connectivity |

### Embedded Serialization Choices

| Constraint | Recommended |
|-----------|-------------|
| No heap allocation | FlatBuffers, SBE, struct overlay |
| 8-bit MCU | CBOR (tiny encoder), custom TLV |
| Safety-critical | ASN.1 DER/PER (formally specified), fixed structs |
| TLS on embedded | ASN.1 DER (X.509), mbedTLS, wolfSSL |

---

## 11.8 — Safety-Critical Systems

### Relevant Standards

| Standard | Domain | Key Data Interface Requirement |
|----------|--------|-------------------------------|
| DO-178C | Avionics | Unambiguous data interface definition; formal verification |
| IEC 61508 | General functional safety | Data element: type, range, unit, access mode |
| ISO 26262 | Automotive safety | Interfaces documented in Technical Safety Concept |
| MISRA C/C++ | Coding | No dynamic allocation, deterministic data access |
| IEC 61784 | Industrial fieldbus | Profinet, EtherNet/IP data objects |

### Implications for Serialization

| Requirement | Effect on Choice |
|-------------|-----------------|
| No dynamic allocation | FlatBuffers structs, SBE, fixed overlays |
| Deterministic timing | No variable-length parsing (avoid Protobuf varint) |
| Formal specification | ASN.1 (formally defined encoding rules) |
| Safety qualification | No unqualified third-party libraries |
| Traceability | Every data element traceable to safety requirement |

### OPC UA (Industrial Automation)

| Property | Value |
|----------|-------|
| Standard | IEC 62541 |
| Model | Information model (nodes: Object, Variable, Method, DataType) |
| Encoding | Binary (OPC UA Binary), XML, JSON |
| Transport | TCP, HTTPS, WebSocket |
| Security | X.509 mutual authentication + encryption |
| Governance | OPC Foundation — Tier 3 |

---

## 11.9 — Finance / HFT

| Technology | Use | Latency |
|-----------|-----|---------|
| **FIX tag-value** | Order management, execution reports | ~1–10 µs parse |
| **SBE** | Market data, order entry (CME, LSE, Nasdaq) | ~50–100 ns |
| **FIXML** | Regulatory reporting, clearing | Seconds (batch) |
| **ISO 20022** | Interbank payments (SWIFT) | Seconds (batch) |
| **FpML** | OTC derivatives contracts | Seconds (batch) |
| **Apache Arrow** | Analytics on tick data | Sub-ms (in-memory) |

---

## 11.10 — Industry Usage Matrix

| Technology | Cloud | Automotive | Telecom | Robotics | IoT/Embedded | Enterprise | Safety | Finance | Healthcare | PKI |
|-----------|-------|-----------|---------|----------|------------|-----------|--------|---------|-----------|-----|
| Protobuf/gRPC | ●●● | ●● | ● | ●● | ● | ●● | ● | ● | ● | ● |
| SOME/IP | ● | ●●● | ● | ● | ● | ● | ●● | ● | ● | ● |
| DDS/RTPS | ●● | ●●● | ● | ●●● | ●● | ● | ●● | ● | ● | ● |
| AIDL/Binder | ● | ●●● | ● | ● | ● | ● | ● | ● | ● | ● |
| ASN.1/DER | ● | ●● | ●●● | ● | ●●● | ● | ●● | ● | ● | ●●● |
| ASN.1/PER | ● | ●● | ●●● | ● | ●● | ● | ●● | ● | ● | ●● |
| OpenAPI/REST | ●●● | ● | ●●● | ● | ● | ●●● | ● | ●● | ●●● | ● |
| Kafka/Avro | ●●● | ● | ● | ● | ● | ●● | ● | ●● | ● | ● |
| SOAP/WSDL | ● | ● | ●● | ● | ● | ●●● | ● | ●● | ●●● | ● |
| SBE | ● | ● | ● | ● | ●● | ● | ● | ●●● | ● | ● |
| CBOR/CoAP | ● | ●● | ● | ● | ●●● | ● | ● | ● | ● | ●● |
| OPC UA | ● | ●● | ● | ●● | ●●● | ●● | ●●● | ● | ● | ● |
| ISO 20022 | ● | ● | ● | ● | ● | ●● | ● | ●●● | ● | ● |
| FIX/FIXML | ● | ● | ● | ● | ● | ● | ● | ●●● | ● | ● |
| HL7/FHIR | ● | ● | ● | ● | ● | ●● | ● | ● | ●●● | ● |
| YANG/NETCONF | ●● | ● | ●●● | ● | ● | ●● | ● | ● | ● | ● |
| FlatBuffers | ●● | ●● | ● | ●● | ●●● | ● | ●● | ●● | ● | ● |
| Arrow | ●●● | ● | ● | ●● | ● | ●● | ● | ●●● | ● | ● |
| ROS2/rosidl | ● | ●● | ● | ●●● | ● | ● | ● | ● | ● | ● |

Legend: ●●● = Primary/dominant, ●● = Significant use, ● = Rare/minimal

---

## 11.11 — Cross-References

| Topic | Volume |
|-------|--------|
| ASN.1 encoding rules (BER, DER, PER) | Volume 05 — Binary Encoding Rules |
| Protobuf, Thrift, AIDL, FIDL, Franca IDLs | Volume 02 — IDLs |
| SOME/IP, DDS serialization details | Volume 04 — Serialization Formats |
| SBE, FlatBuffers, Arrow zero-copy | Volume 09 — Zero-Copy Systems |
| OpenAPI, AsyncAPI, CloudEvents | Volume 08 — API Contract Systems |
| Schema evolution (Avro, Protobuf, AIDL) | Volume 10 — Schema Evolution |
| Governance bodies and tiers | Volume 12 — Governance & Standards |
| Binder, Iceoryx, shared memory IPC | Volume 07 — IPC Mechanisms |

---

*End of Volume 11 — Industry-Specific Data Contracts*
