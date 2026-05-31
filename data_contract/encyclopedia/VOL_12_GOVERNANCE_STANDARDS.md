# VOLUME 12 — GOVERNANCE, STANDARDS BODIES & OWNERSHIP

## Universal Data Contract, Serialization, IDL & Interoperability Architecture Encyclopedia

---

## 12.0 — Why Governance Matters for Technology Selection

A technology is only as stable as its governance model. When choosing a data contract technology for long-lived systems, governance determines:

| Risk Dimension | What Governance Tells You |
|---------------|--------------------------|
| **Longevity** | Will this standard exist in 10 years? |
| **Fork risk** | Can the spec be unilaterally changed by one entity? |
| **Patent exposure** | Are there encumbered essential patents? |
| **License change** | Can the license change from open to proprietary? |
| **Implementation diversity** | Are there multiple independent implementations? |
| **Vendor lock-in** | Does a single vendor control the ecosystem? |
| **Regulatory acceptance** | Will regulators accept this standard? |

**Decision principle:** For safety-critical and regulatory-mandated systems, prefer Tier 1–3. For internal microservices, Tier 5–6 is acceptable. For prototyping, Tier 8 is fine.

---

## 12.1 — Governance Tier Ladder

### Tier 0 — Mathematical / Formal Foundations

| Property | Value |
|----------|-------|
| Authority | None — mathematical truth |
| Examples | BNF/EBNF, type theory, algebraic data types, set theory |
| Stability | Permanent |
| Relevance | Foundational basis for all formal schema/IDL languages |

### Tier 1 — International Treaty-Level Standards

| Property | Value |
|----------|-------|
| Bodies | ISO, IEC, ISO/IEC JTC1 |
| Authority | Intergovernmental treaty; national standards body members vote |
| Process | Multi-year consensus; national body ballot |
| Patents | FRAND (Fair, Reasonable, And Non-Discriminatory) required |
| Stability | Very high; breaking changes extremely rare |
| Examples | ISO 8824 (ASN.1), ISO 20022, IEC 61508, ISO 26262, IEC 62541 (OPC UA) |

### Tier 2 — Global Internet / Engineering Standards

| Body | Process | Key Specs |
|------|---------|-----------|
| **IETF** | RFC (WG → IESG → RFC Editor) | JSON, CBOR, YANG, CoAP, JWT, XDR |
| **ITU-T** | Recommendations (X./G./H./Q. series) | ASN.1 (X.680), BER/DER (X.690), PER (X.691) |
| **W3C** | WD → CR → PR → Recommendation | XML, XSD, SOAP, WSDL, RDF, OWL |
| **IEEE** | Project → ballot → approval | IEEE 754 (floating point), 802.x |

### Tier 3 — Industry Consortia

| Body | Focus | Examples |
|------|-------|---------|
| **OMG** | Middleware, modeling | CORBA, DDS, UML, IDL 4.2 |
| **AUTOSAR** | Automotive software | SOME/IP, ARXML, Adaptive Platform |
| **3GPP** | Mobile telecom | LTE/5G protocol ASN.1 specs |
| **OpenAPI Initiative** | REST API contracts | OpenAPI 3.x |
| **OASIS** | Enterprise | SAML, OData, AMQP 1.0 |
| **COVESA** | Connected vehicles | VSS, Franca IDL |
| **OPC Foundation** | Industrial automation | OPC UA |

### Tier 4 — Professional / Trade Associations

| Body | Domain | Examples |
|------|--------|---------|
| **FIX Trading Community** | Financial markets | FIX Protocol, SBE, FIXML |
| **HL7 International** | Healthcare | HL7 v2, FHIR |
| **SWIFT** | Interbank messaging | SWIFT MT, MX (ISO 20022) |

### Tier 5 — Open-Source Foundation Standards

| Foundation | Governance | Projects |
|-----------|-----------|----------|
| **Apache ASF** | Meritocracy, PMC, Apache 2.0 | Avro, Thrift, Arrow, Kafka, Dubbo |
| **CNCF** (LF) | TOC + TAG governance | gRPC, CloudEvents |
| **Eclipse Foundation** | Board + PMC, EPL 2.0 | CycloneDDS, Iceoryx, Kuksa |
| **GraphQL Foundation** (LF) | Spec Working Group | GraphQL |
| **Open Robotics** | Board, Apache 2.0 | ROS, ROS2, rosidl |

### Tier 6 — Vendor-Led Open Source

| Vendor | Technologies | Risk |
|--------|-------------|------|
| Google | Protobuf, gRPC, FlatBuffers, FIDL | Product deprecation history |
| Amazon | Smithy IDL | AWS-centric ecosystem |
| Microsoft | Bond, TypeSpec, MIDL | Azure-centric (but TypeSpec → OpenAPI) |
| Confluent | Schema Registry | License changed to BSL 1.1 (2023) |
| MongoDB | BSON | Single-vendor format |

### Tier 7 — Vendor Proprietary

Closed specifications not available for independent implementation. Examples: internal vendor wire formats, SWIFT proprietary extensions.

### Tier 8 — De-Facto Standards

| Technology | Formalization Path |
|-----------|-------------------|
| JSON | De-facto → ECMA-404 + RFC 8259 |
| REST | Fielding dissertation → de-facto style |
| YAML | Community spec at yaml.org |
| MessagePack | Community spec at msgpack.org |
| Cap'n Proto | Informal spec at capnproto.org |
| JSON-RPC | Community specification |

---

## 12.2 — ISO/IEC Standards Catalog

| Standard | Title | Relevance |
|----------|-------|-----------|
| ISO 8824-1 / X.680 | ASN.1 Basic Notation | Data description language |
| ISO 8825-1 / X.690 | ASN.1 BER/DER | Binary encoding for PKI, telecom |
| ISO 8825-2 / X.691 | ASN.1 PER | Compact encoding (3GPP) |
| ISO 8825-5 / X.693 | ASN.1 XER | XML encoding rules |
| ISO/IEC 9075 | SQL | Relational data modeling |
| ISO/IEC 19505 | UML | Unified Modeling Language |
| ISO 20022 | Financial messaging | Payment/securities data contracts |
| IEC 61508 | Functional safety | Safety-critical data interfaces |
| ISO 26262 | Automotive safety | Vehicle safety data requirements |
| IEC 62541 | OPC UA | Industrial automation data model |
| ISO 9735 | EDIFACT | Enterprise data interchange |
| ISO/IEC 8859 | Character encoding | Text representation |

---

## 12.3 — IETF RFC Catalog (Data Contract Relevant)

| RFC | Title | Technology |
|-----|-------|-----------|
| RFC 8259 | JSON | Serialization format |
| RFC 8949 | CBOR | Binary serialization (IoT) |
| RFC 8610 | CDDL | CBOR schema language |
| RFC 7950 | YANG 1.1 | Network data modeling |
| RFC 6241 | NETCONF | Network configuration |
| RFC 8040 | RESTCONF | REST + YANG |
| RFC 7252 | CoAP | IoT constrained protocol |
| RFC 5531 | ONC-RPC | Remote procedure call |
| RFC 4506 | XDR | External data representation |
| RFC 7519 | JWT | JSON Web Token |
| RFC 7515 | JWS | JSON Web Signature |
| RFC 7517 | JWK | JSON Web Key |
| RFC 9052 | COSE | CBOR signing/encryption |
| RFC 5280 | X.509 v3 | Certificate structure |
| RFC 5652 | CMS | Cryptographic message syntax |
| RFC 6733 | Diameter | Telecom AVP encoding |
| RFC 2578 | SMIv2 | MIB structure |
| RFC 4180 | CSV | Comma-separated values |
| RFC 8594 | Sunset header | API deprecation |
| RFC 9110 | HTTP Semantics | HTTP protocol semantics |

---

## 12.4 — W3C Specifications

| Specification | Version | Purpose |
|--------------|---------|---------|
| XML | 1.0 / 1.1 | Extensible Markup Language |
| XSD | 1.0 / 1.1 | XML Schema Definition |
| SOAP | 1.1 / 1.2 | RPC protocol over XML |
| WSDL | 1.1 / 2.0 | Web service description |
| SPARQL | 1.1 | RDF query language |
| RDF | 1.1 | Resource Description Framework |
| OWL | 2 | Web Ontology Language |
| JSON-LD | 1.1 | Linked Data in JSON |
| SHACL | 1.0 | Shapes Constraint Language (RDF validation) |
| WS-Security | 1.1 | SOAP security |
| WS-Addressing | 1.0 | Endpoint references |

---

## 12.5 — OMG Specifications

| Specification | Version | Purpose |
|--------------|---------|---------|
| CORBA | 3.4 | IDL, GIOP, IIOP, CDR |
| UML | 2.5.1 | Unified Modeling Language |
| MOF | 2.5.1 | Meta-Object Facility |
| DDS | 1.4 | Data Distribution Service |
| DDS-XTypes | 1.3 | Extensible/dynamic types |
| DDS-Security | 1.1 | DDS security model |
| IDL | 4.2 | Interface Definition Language |
| SysML | 1.7 | Systems Modeling Language |

---

## 12.6 — Open-Source Foundation Governance Comparison

| Foundation | Governance Model | IP Policy | License | Notable Projects |
|-----------|----------------|----------|---------|-----------------|
| **Apache ASF** | Meritocracy; PMC per project | Apache ICLA/CCLA | Apache 2.0 | Avro, Thrift, Arrow, Kafka |
| **Linux Foundation** | Board + TAC | Per-project (varies) | Varies | Kernel, Yocto, OpenEmbedded |
| **CNCF** (LF) | TOC + TAGs; graduated/incubating/sandbox | Apache 2.0 preferred | Apache 2.0 | gRPC, CloudEvents, Envoy |
| **Eclipse Foundation** | Board + PMC | Eclipse Contributor Agreement | EPL 2.0 | CycloneDDS, Iceoryx |
| **GraphQL Foundation** (LF) | Spec WG + Lee Byron | OWF Agreement | MIT/Apache 2.0 | GraphQL spec |
| **OpenAPI Initiative** (LF) | TSC | Apache 2.0 | Apache 2.0 | OpenAPI 3.x |
| **AsyncAPI Initiative** (LF) | Core committers | Apache 2.0 | Apache 2.0 | AsyncAPI 3.x |
| **Open Robotics** | Board | Apache 2.0 | Apache 2.0/BSD | ROS, ROS2 |

---

## 12.7 — Vendor Governance Analysis

### Google

| Aspect | Detail |
|--------|--------|
| Technologies | Protocol Buffers, gRPC, FlatBuffers, FIDL (Fuchsia) |
| License | Apache 2.0, BSD 3-Clause |
| CLA | Google Individual/Corporate CLA |
| Risk | History of product deprecation (Google+, Cloud IoT) |
| Mitigation | gRPC donated to CNCF; Protobuf widely multi-vendor |

### Amazon Web Services

| Aspect | Detail |
|--------|--------|
| Technologies | Smithy IDL, CloudFormation schema, Glue Schema Registry |
| License | Apache 2.0 (Smithy) |
| Risk | AWS-centric toolchain; limited cross-cloud adoption |
| Mitigation | Smithy open-sourced; community contributions accepted |

### Microsoft

| Aspect | Detail |
|--------|--------|
| Technologies | Bond, TypeSpec, MIDL, WCF |
| License | MIT (Bond, TypeSpec) |
| Risk | Azure-centric for some tools |
| Mitigation | TypeSpec emits OpenAPI (reduces vendor lock-in) |

### Confluent

| Aspect | Detail |
|--------|--------|
| Technologies | Schema Registry, ksqlDB |
| License change | Apache 2.0 → BSL 1.1 (2023) |
| Risk | Commercial license restricts competitive SaaS use |
| Alternatives | Apicurio Registry (Red Hat/open), AWS Glue Schema Registry |

---

## 12.8 — Governance & Ownership Master Matrix

| Technology | Body | Tier | License | Spec Access | Ref Impl |
|-----------|------|------|---------|-------------|----------|
| ASN.1 (X.680) | ISO/ITU-T | 1/2 | Paid (ISO) | ITU-T Rec | asn1c, OSS Nokalva |
| BER/DER (X.690) | ISO/ITU-T | 1/2 | Paid | ITU-T X.690 | OpenSSL, BouncyCastle |
| PER (X.691) | ISO/ITU-T | 1/2 | Paid | ITU-T X.691 | asn1c, commercial |
| JSON | IETF | 2 | Free (RFC) | RFC 8259 | Every language |
| CBOR | IETF | 2 | Free (RFC) | RFC 8949 | cbor2, TinyCBOR |
| XML/XSD | W3C | 2 | Free | W3C Rec | libxml2, Xerces |
| SOAP/WSDL | W3C | 2 | Free | W3C Rec | Apache CXF, .NET |
| YANG | IETF | 2 | Free (RFC) | RFC 7950 | pyang, libyang |
| DDS | OMG | 3 | Free (OMG) | OMG spec | FastDDS, CycloneDDS |
| AUTOSAR/SOME/IP | AUTOSAR | 3 | Member-only | AUTOSAR PRS | Vector, ETAS |
| OpenAPI 3.1 | OAI/LF | 3 | Apache 2.0 | GitHub | swagger-parser |
| AsyncAPI 3.0 | AsyncAPI/LF | 3 | Apache 2.0 | GitHub | asyncapi-parser |
| Protobuf | Google/CNCF | 6 | Apache 2.0 | GitHub | protoc |
| gRPC | Google/CNCF | 5/6 | Apache 2.0 | GitHub | grpc-core |
| Avro | Apache ASF | 5 | Apache 2.0 | apache.org | avro-java |
| Thrift | Apache ASF | 5 | Apache 2.0 | apache.org | thrift (C++) |
| Arrow | Apache ASF | 5 | Apache 2.0 | apache.org | arrow-cpp |
| FlatBuffers | Google | 6 | Apache 2.0 | GitHub | flatc |
| Smithy | Amazon | 6 | Apache 2.0 | GitHub | smithy-build |
| TypeSpec | Microsoft | 6 | MIT | GitHub | tsp compiler |
| GraphQL | GraphQL Foundation | 5 | OWF | GitHub spec | graphql-js |
| Cap'n Proto | Community | 8 | MIT | capnproto.org | capnp (C++) |
| MessagePack | Community | 8 | Apache 2.0 | msgpack.org | msgpack-c |
| ISO 20022 | ISO TC68 | 1 | ISO license | iso20022.org | SWIFT MX |
| FHIR | HL7 International | 4 | HL7 IP | hl7.org/fhir | HAPI FHIR |
| FIX/SBE | FIX Trading | 4 | Apache 2.0 | fixtrading.org | sbe-tool |
| OPC UA | OPC Foundation | 3/1 | OPC license | opcfoundation.org | open62541 |
| MQTT 5.0 | OASIS | 3 | OASIS | oasis-open.org | Eclipse Paho |

---

## 12.9 — Technology Selection Framework: Using Governance for Risk

### Selection Matrix by System Criticality

| System Type | Recommended Tier | Rationale |
|-------------|-----------------|-----------|
| Safety-critical (ISO 26262) | Tier 1–3 | Regulatory requirement; stability paramount |
| Regulatory/compliance | Tier 1–4 | Mandated standards (ISO 20022, HL7) |
| Long-lived infrastructure (>10 years) | Tier 1–5 | Multiple implementations; low fork risk |
| Enterprise production | Tier 3–6 | Balance of stability and features |
| Internal microservices | Tier 5–6 | Velocity more important than longevity |
| Proof of concept / prototype | Tier 6–8 | Speed; willing to replace later |

### Red Flags in Governance

| Flag | Risk | Example |
|------|------|---------|
| Single-vendor CLA with no foundation | Vendor can change license | Confluent BSL change |
| No public specification | Reverse-engineering required | Proprietary wire formats |
| Spec behind paywall with no reference impl | Limited adoption | Some ISO standards |
| Foundation with no diverse membership | De-facto single vendor | Small LF sub-projects |
| BDFL (Benevolent Dictator For Life) model | Bus factor = 1 | Small community projects |

---

## 12.10 — Patent and Licensing Risks

### Patent Regimes in Standards

| Body | Patent Policy |
|------|-------------|
| ISO/IEC | FRAND (must disclose essential patents; reasonable licensing) |
| IETF | RFC 8179 — disclosure required; "Note Well" at meetings |
| W3C | Royalty-Free (RF) policy — members grant RF license |
| OMG | FRAND-like; IP policy per specification |
| Apache ASF | Apache License 2.0 includes express patent grant from contributors |
| CNCF | Apache 2.0 + CLA (patent grant) |

### License Risk Scenarios

| Scenario | Impact | Mitigation |
|----------|--------|-----------|
| BSL/SSPL change | Cannot use in competitive SaaS | Fork pre-change version; use alternatives |
| Patent troll on standard | Licensing fees for implementation | Choose RF-only standards; check IPR disclosures |
| CLA with patent trap | Contributor grants broad rights to vendor | Review CLA terms before contributing |
| Dual license (GPL + commercial) | GPL may infect proprietary code | Use Apache/MIT alternatives |

---

## 12.11 — Cross-References

| Topic | Volume |
|-------|--------|
| All technologies governed by these bodies | Volumes 01–11, 13–15 |
| Schema evolution governance (Confluent Registry) | Volume 10 — Schema Evolution |
| API governance patterns | Volume 08 — API Contract Systems |
| Industry-specific standards bodies | Volume 11 — Industry-Specific |
| Code generation tools from governed specs | Volume 13 — Tooling |

---

*End of Volume 12 — Governance, Standards Bodies & Ownership*
