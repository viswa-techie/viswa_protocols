================================================================================
README — PROMPT LIBRARY INDEX
Universal Data Contract, Serialization, Interface Definition &
Interoperability Architecture Encyclopedia
================================================================================
Directory: viswa_protocols/data contract/
================================================================================

OVERVIEW
--------
This library contains a MASTER prompt and 15 volume-level sub-prompts for
generating a comprehensive, encyclopedia-level reference on every major data
contract, serialization format, IDL, schema system, RPC framework, and
interoperability technology used in modern software systems.

USAGE MODES
-----------

  MODE 1 — MASTER ONLY
    Feed MASTER_PROMPT.txt to the LLM alone.
    The LLM will generate all 24 parts (executive overview through master index)
    using the embedded technology taxonomy, comparison matrices, deep-dive
    sections, and cross-reference tables.

  MODE 2 — SINGLE VOLUME
    Feed any single VOL_XX_PROMPT.txt for deep-dive content on one domain.
    Each volume is standalone and generates encyclopedic output for its domain
    including annotated examples, comparison matrices, and a required-output list.

  MODE 3 — COMBINED CHAIN
    Feed MASTER_PROMPT.txt first for the framework, then send each volume
    prompt in order (01→15) to build up the full reference incrementally.
    Recommended for maximum depth per domain without context overflow.

  MODE 4 — TARGETED LOOKUP
    Use the Quick Reference section below to find exactly which file covers
    a specific technology, then feed that volume prompt.

================================================================================
FILE INDEX
================================================================================

| File | Volume | Title | Description |
|------|--------|-------|-------------|
| MASTER_PROMPT.txt | MASTER | Encyclopedia Master Prompt | Central orchestration: taxonomy, governance ladder, universal entry template, 12 comparison matrices, 8 deep-dive sections, architecture diagram, full index |
| 01_DATA_MODELING_PROMPT.txt | 01 | Data Modeling | ER/EER, UML class diagrams, DDD, RDF/OWL, YANG, FHIR, ISO 20022, VSS, ROS2 .msg, JSON Schema as model |
| 02_IDL_PROMPT.txt | 02 | Interface Definition Languages (IDLs) | Protobuf .proto, Thrift IDL, AIDL, FIDL, OMG IDL/DDS IDL, ASN.1, WSDL, Smithy, GraphQL SDL, Franca, Bond, Cap'n Proto, FlatBuffers, Avro IDL |
| 03_SCHEMA_SYSTEMS_PROMPT.txt | 03 | Schema Systems | JSON Schema 2020-12, XSD 1.1, Avro schema, Protobuf schema, Thrift schema, GraphQL schema, OpenAPI Schema Object, YANG, Pydantic/Zod, Schema Registries |
| 04_SERIALIZATION_FORMATS_PROMPT.txt | 04 | Serialization Formats | JSON, XML, YAML, TOML, CSV, Protobuf wire, Avro binary, Thrift binary/compact, BSON, CBOR, MessagePack, Smile, UBJSON |
| 05_BINARY_ENCODING_PROMPT.txt | 05 | Binary Encoding Rules | ASN.1 BER/DER/PER/XER/OER/JER, IEEE 754, network byte order, PKCS#1/8/10/12, ASN.1 compilers |
| 06_RPC_FRAMEWORKS_PROMPT.txt | 06 | RPC Frameworks | gRPC (full request flow), Thrift RPC, JSON-RPC, XML-RPC, SOAP, REST (Richardson model), CORBA, Java RMI, WCF, Cap'n Proto RPC, Apache Dubbo, ONC-RPC, GraphQL |
| 07_IPC_MECHANISMS_PROMPT.txt | 07 | IPC Mechanisms | Linux (pipes/UDS/SHM/D-Bus/io_uring), Android Binder+AIDL+HIDL, macOS XPC/Mach IPC, Windows COM/ALPC, Fuchsia FIDL/channels, Iceoryx, SOME/IP |
| 08_API_CONTRACTS_PROMPT.txt | 08 | API Contract Systems | OpenAPI 3.1, AsyncAPI 3.0, GraphQL, WSDL, JSON:API, HAL, OData, RAML, CloudEvents, TypeSpec |
| 09_ZERO_COPY_SYSTEMS_PROMPT.txt | 09 | Zero-Copy Systems | FlatBuffers vtable/offset, Cap'n Proto arenas, Apache Arrow columnar/Flight, SBE flyweight, Iceoryx RouDi, DPDK rte_ring, typed struct overlays |
| 10_SCHEMA_EVOLUTION_PROMPT.txt | 10 | Schema Evolution | Compatibility types, Protobuf evolution rules, Avro reader/writer resolution, Thrift field IDs, OpenAPI versioning, Confluent Schema Registry, buf breaking, Stable AIDL |
| 11_INDUSTRY_SPECIFIC_PROMPT.txt | 11 | Industry-Specific Standards | Automotive (AUTOSAR/SOME-IP/DDS/ROS2), Telecom (3GPP/YANG/Diameter), Enterprise (SOAP/EDI/HL7/FHIR/ISO 20022/FIX), Security/PKI (X.509/JOSE/COSE), IoT (MQTT/CoAP/LwM2M), Safety (DO-178C/IEC 61508/ISO 26262), Finance |
| 12_GOVERNANCE_STANDARDS_PROMPT.txt | 12 | Governance & Standards Bodies | Governance tier ladder 0–8, ISO/IEC catalog, IETF RFC catalog, W3C specs, OMG specs, open-source foundations, vendor analysis, 38-row governance matrix |
| 13_TOOLING_CODEGEN_PROMPT.txt | 13 | Tooling & Code Generation | protoc/buf, Thrift compiler, aidl, fidlc, openapi-generator/Spectral/oasdiff, avro-tools, asn1c, flatc, capnp, graphql-codegen, JSON Schema validators, Maven/Gradle/Bazel/CMake/Cargo integration, CI/CD patterns |
| 14_TRANSPORT_BINDING_PROMPT.txt | 14 | Transport Layer & Runtime Binding | HTTP/1.1/2/3, TCP, UDP, Unix Domain Sockets, Binder transport, Mach ports, Zircon channels, shared memory, TLS/mTLS, transport abstraction (Thrift TTransport, gRPC transport), transport dependency matrix |
| 15_INTEROPERABILITY_PROMPT.txt | 15 | Cross-Platform Interoperability | Language binding matrix, ABI stability, endianness/pointer size, Stable AIDL/VINTF, WASM, REST as lingua franca, per-OS considerations, anti-patterns, decision framework, interop maturity matrix |
| README_PROMPT_INDEX.txt | INDEX | This file | Navigation guide for the entire prompt library |
| general_prompt.txt | ORIGINAL | Original master prompt | Source specification from which this library was derived (do not modify) |

================================================================================
QUICK REFERENCE — TECHNOLOGY TO VOLUME MAP
================================================================================

To generate content about a specific technology, use the corresponding volume:

  Technology          → Volume(s)
  ─────────────────────────────────────────────────────────────────────────────
  Protobuf (.proto)   → 02 (IDL), 04 (wire format), 10 (evolution), 13 (tooling)
  gRPC                → 06 (RPC framework), 14 (transport), 13 (tooling)
  Avro                → 03 (schema), 04 (binary encoding), 10 (evolution), 13 (tooling)
  JSON Schema         → 03 (schema systems), 10 (evolution), 13 (tooling)
  OpenAPI             → 08 (API contract), 10 (versioning), 13 (tooling)
  AsyncAPI            → 08 (API contract)
  GraphQL             → 02 (SDL as IDL), 06 (as RPC), 08 (as API contract), 13 (tooling)
  AIDL / Binder       → 02 (IDL), 07 (IPC), 10 (Stable AIDL), 13 (tooling), 14 (transport)
  FIDL                → 02 (IDL), 07 (IPC), 13 (tooling), 14 (transport)
  ASN.1               → 02 (as IDL), 05 (BER/DER/PER/OER/XER), 11 (telecom), 13 (tooling)
  CBOR                → 04 (serialization), 05 (binary encoding), 11 (IoT)
  FlatBuffers         → 02 (IDL), 09 (zero-copy), 13 (tooling)
  Cap'n Proto         → 02 (IDL), 06 (RPC), 09 (zero-copy), 13 (tooling)
  Apache Arrow        → 09 (zero-copy / columnar)
  SBE                 → 09 (zero-copy), 11 (finance)
  Iceoryx             → 07 (IPC), 09 (zero-copy), 14 (transport)
  Thrift              → 02 (IDL), 06 (RPC), 13 (tooling)
  WSDL / SOAP         → 02 (WSDL as IDL), 06 (SOAP as RPC), 08 (WSDL as contract)
  D-Bus               → 07 (IPC), 14 (transport)
  SOME/IP             → 07 (IPC), 11 (automotive), 14 (transport)
  DDS / RTPS          → 07 (IPC), 11 (automotive/robotics), 14 (transport)
  ROS2 / rosidl       → 01 (data model), 11 (robotics)
  AUTOSAR / ARXML     → 11 (automotive), 12 (governance)
  YANG / NETCONF      → 01 (data model), 11 (telecom), 13 (tooling)
  ISO 20022           → 01 (data model), 11 (finance), 12 (governance)
  HL7 FHIR            → 01 (data model), 11 (healthcare)
  FIX / SBE           → 09 (zero-copy), 11 (finance)
  OPC UA              → 11 (industry), 12 (governance)
  CoAP / MQTT         → 11 (IoT), 14 (transport)
  X.509 / PKCS        → 05 (DER encoding), 11 (security/PKI)
  JWT / JOSE / COSE   → 11 (security/PKI)
  HTTP/2 / QUIC       → 14 (transport)
  WASM                → 15 (interoperability)
  Smithy              → 02 (IDL), 08 (API contract)
  TypeSpec            → 08 (API contract), 13 (tooling)
  Confluent Schema Registry → 03 (schema registry), 10 (schema evolution)
  buf                 → 10 (schema evolution), 13 (tooling)
  Governance tiers    → 12 (standards bodies)
  ABI stability       → 15 (interoperability)
  Endianness          → 15 (interoperability), 05 (binary encoding)
  Language bindings   → 15 (interoperability)

================================================================================
SUGGESTED READING ORDERS BY AUDIENCE
================================================================================

  CLOUD / API ARCHITECT:
    04 (Serialization Formats) → 08 (API Contracts) → 10 (Schema Evolution)
    → 03 (Schema Systems) → 06 (RPC Frameworks) → 12 (Governance) → 15 (Interop)

  AUTOMOTIVE ENGINEER (AUTOSAR/ADAS):
    11 (Industry-Specific: Automotive) → 07 (IPC: Binder/DDS/Iceoryx) → 14 (Transport: SOME/IP)
    → 09 (Zero-Copy: SBE/FlatBuffers) → 02 (IDL: AIDL/Franca) → 10 (Schema Evolution)

  ANDROID / OS ENGINEER:
    07 (IPC Mechanisms) → 02 (IDL: AIDL/FIDL) → 10 (Schema Evolution: Stable AIDL)
    → 14 (Transport: Binder) → 09 (Zero-Copy: FlatBuffers) → 13 (Tooling: aidl/fidlc)

  TELECOM / NETWORK ENGINEER:
    05 (Binary Encoding: ASN.1 PER/BER) → 11 (Industry: Telecom 3GPP/YANG)
    → 01 (Data Modeling: YANG) → 04 (CBOR) → 14 (Transport) → 12 (Governance: IETF/ITU-T)

  EMBEDDED / IoT ENGINEER:
    05 (Binary Encoding) → 04 (Serialization: CBOR/MessagePack) → 11 (IoT/Safety)
    → 09 (Zero-Copy) → 14 (Transport: CoAP/MQTT) → 12 (Governance)

  ENTERPRISE / JAVA DEVELOPER:
    04 (Serialization: JSON/XML/Avro) → 06 (RPC: gRPC/SOAP) → 03 (Schema Systems)
    → 08 (API Contracts: OpenAPI) → 10 (Schema Evolution) → 13 (Tooling: Maven/Gradle)

  SECURITY / PKI ENGINEER:
    05 (ASN.1 BER/DER/X.509) → 11 (Security: JOSE/COSE/SAML) → 12 (Governance)
    → 04 (Serialization: CBOR) → 15 (Interop: ABI/Platform)

  FINANCE / FIX / SWIFT ENGINEER:
    09 (Zero-Copy: SBE) → 11 (Finance: FIX/ISO 20022/SWIFT) → 05 (Binary Encoding)
    → 04 (Serialization) → 10 (Schema Evolution) → 12 (Governance)

================================================================================
COMPARISON MATRICES AVAILABLE ACROSS THE LIBRARY
================================================================================

  Matrix | Location | Rows | Columns | Description
  ──────────────────────────────────────────────────────────────────────────────
  Data Modeling Paradigms | Vol 01 | 13 paradigms | 8 dimensions | Format, schema enforcement, formal semantics, tooling, etc.
  IDL Comparison | Vol 02 | 15 IDLs | 15 dimensions | Typing, schema, versioning, transport, code gen, etc.
  Schema Systems | Vol 03 | 10 formats | 8 dimensions | Schema location, evolution, validation, etc.
  Serialization Formats | Vol 04 | 15 formats | 10 dimensions | Size, speed, human-readable, streaming, etc.
  Binary Encoding Rules | Vol 05 | 11 formats | 8 dimensions | Tag system, compact, formal spec, etc.
  RPC Frameworks | Vol 06 | 13 frameworks | 9 dimensions | Transport, typing, streaming, code gen, etc.
  IPC Mechanisms | Vol 07 | 13 mechanisms | 9 dimensions | Copy count, security, platform, etc.
  API Contract Systems | Vol 08 | 10 systems | 8 dimensions | Versioning, governance, code gen, etc.
  Zero-Copy Systems | Vol 09 | 9 formats | 7 dimensions | Copy count, alignment, zero-allocation, etc.
  Schema Evolution | Vol 10 | 11 technologies | 10 dimensions | Compatibility, registries, tooling, etc.
  Industry Usage | Vol 11 | 19 technologies | 10 industries | Primary/Secondary/Rare/None cells |
  Governance | Vol 12 | 38 technologies | 7 dimensions | Tier, license, spec publication, etc.
  Toolchain | Vol 13 | 13 technologies | 7 dimensions | Compiler, languages, build systems, etc.
  Transport Dependency | Vol 14 | 17 frameworks | 12 transports | Yes/No/Primary cells |
  Interoperability Maturity | Vol 15 | 16 technologies | 7 dimensions | Cross-language, cross-platform, etc.

================================================================================
CROSS-VOLUME DEPENDENCY MAP
================================================================================

  Vol 02 IDLs  ←→ Vol 03 Schema Systems  (IDL defines schema; schema validates messages)
  Vol 02 IDLs  ←→ Vol 04 Serialization   (IDL describes what; serialization is how)
  Vol 04 Serialization ←→ Vol 05 Binary Encoding (binary formats are a subset)
  Vol 02 IDLs  ←→ Vol 06 RPC             (IDLs used to define RPC services)
  Vol 06 RPC   ←→ Vol 14 Transport       (RPC runs over transport)
  Vol 07 IPC   ←→ Vol 14 Transport       (IPC is transport for local communication)
  Vol 07 IPC   ←→ Vol 09 Zero-Copy       (zero-copy requires appropriate IPC/transport)
  Vol 02 IDLs  ←→ Vol 13 Tooling         (tools compile IDL to code)
  Vol 03 Schema←→ Vol 10 Evolution       (schema registries enforce evolution)
  Vol 10 Evolution ←→ Vol 13 Tooling     (buf, Stable AIDL, etc. are tooling for evolution)
  Vol 11 Industry←→ ALL                 (industry-specific standards span all volumes)
  Vol 12 Governance←→ ALL               (every technology has a governance tier)
  Vol 15 Interop←→ ALL                  (interoperability analysis references all volumes)

================================================================================
END OF README
================================================================================
