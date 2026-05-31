# CHAPTER 18 — CROSSWALK AND RELATIONSHIP TABLES
# Prompt for: Universal Technology Standards Governance Encyclopedia
# Depends on: MASTER_PROMPT.md, ALL Chapters 1–17
# Purpose: Generate pure crosswalk tables linking standards bodies, standards, and domains

---

## CHAPTER SCOPE

This chapter is a **cross-reference synthesis chapter**. It does not describe individual standards — it produces tables that show how all standards in Chapters 1–17 relate to each other across:
- Governance bodies vs. domain coverage
- Interoperability relationships between standards
- Certification pathways
- Open vs. proprietary mapping
- Technology stack layers with standard assignments
- Standards that conflict, overlap, or supersede each other

---

## GENERATION INSTRUCTIONS FOR CHAPTER 18

Act as a technical standards analyst with expertise in cross-domain standards mapping.
Generate the complete **Chapter 18: Crosswalk and Relationship Tables**.

This chapter should be primarily table-driven with explanatory notes between tables.

---

### SECTION 18.1 — GOVERNANCE BODY × DOMAIN COVERAGE MATRIX

Generate a matrix table with rows = governance bodies and columns = technology domains.
Use cells to indicate:
- **P** = Primary standard producer
- **S** = Secondary/contributing standard producer
- **A** = Adjacent/referencing
- **—** = No significant involvement

**Governance Bodies (rows):**
ISO/IEC JTC1, ITU-T, IETF, W3C, WHATWG, IEEE SA, ECMA TC39, OASIS, Unicode Consortium, Linux Foundation, Apache ASF, CNCF, FIDO Alliance, OpenID Foundation, PCI-SIG, USB-IF, JEDEC, NVMe org, VESA, HDMI Forum, MIPI Alliance, Khronos Group, OMG, AUTOSAR, COVESA, OPC Foundation, SAE International, RTCA, ISA, NIST, FCC, ETSI, EU Commission

**Technology Domains (columns):**
Networking Protocols, Web Standards, Security/Crypto, Programming Languages, Data Formats, Automotive, Cloud/Container, AI/ML, Hardware/Semiconductor, Industrial Control, Aviation, Medical, Database/Query, Identity/Auth, Serialization

---

### SECTION 18.2 — STANDARDS INTEROPERABILITY DEPENDENCY MAP

Generate a table showing which standards depend on or reference other standards:

| Standard        | Depends On / References                                           | Used By / Depended On By                                |
|-----------------|-------------------------------------------------------------------|---------------------------------------------------------|
| TLS 1.3 (RFC 8446)| X.509/PKIX (RFC 5280), AES/GCM, ECDHE, SHA-256, ECDSA/EdDSA   | HTTPS, gRPC, MQTT/TLS, OPC UA security, automotive DoIP|
| OpenAPI 3.1     | JSON Schema 2020-12, JSON (RFC 8259), HTTP (RFC 9110), OAuth 2.0| REST API documentation, code generation tools          |
| OAuth 2.0       | TLS (transport req), JWT (RFC 7519), PKCE (RFC 7636)            | OIDC, FAPI 2.0, SCIM, API gateway auth               |
| OIDC 1.0        | OAuth 2.0 (RFC 6749), JWT (RFC 7519), JWKS                     | SSO, federation, SAML bridging                         |
| WebAuthn Level 2| FIDO CTAP2, CBOR (RFC 8949), COSE (RFC 8152)                   | Passkeys, browser authentication                       |
| gRPC            | HTTP/2 (RFC 9113), Protobuf IDL, TLS 1.3                        | Kubernetes API, service mesh data plane                |
| Kubernetes API  | OpenAPI 3.x (spec), JSON/YAML, gRPC (API server), OCI (images)  | Helm, ArgoCD, Istio, CNAB                             |
| OCI Image Spec  | tar (POSIX), JSON (RFC 8259), SHA-256 digest                    | Docker, Podman, containerd, BuildKit, Kubernetes       |
| AUTOSAR Adaptive| POSIX PSE51, DDS (OMG), TLS, C++14, SOME/IP                    | Vehicle ECU middleware, ADAS platforms                 |
| ISO 26262       | IEC 61508 (parent), AUTOSAR (implementation), MISRA C           | AUTOSAR Classic, QM/ASIL certification                |
| CAN FD          | ISO 11898 (base), AUTOSAR COM                                   | OBD-II, J1939, UDS (ISO 14229), SOME/IP              |
| DO-178C         | DO-330 (tools), DO-331 (MBD), DO-332 (OOT), DO-333 (formal)    | FAA/EASA type certification, avionics software        |
| ONNX            | Protobuf (format), NumPy type system (dtype), ONNX opset        | ONNX Runtime, TensorRT, OpenVINO, model deployment    |
| Apache Parquet  | Apache Arrow (in-memory), Apache Avro (schema option), Snappy   | Spark, Hive, DeltaLake, Iceberg, Pandas, Polars       |

---

### SECTION 18.3 — TECHNOLOGY STACK LAYER × STANDARD ASSIGNMENT TABLE

For each architectural layer, list the dominant standard(s):

| Layer                    | Domain         | Primary Standard(s)                           | Governance Body |
|--------------------------|----------------|-----------------------------------------------|-----------------|
| **Physical/Media**       | Ethernet       | IEEE 802.3                                   | IEEE SA         |
| **Physical/Media**       | USB cable      | USB 3.2/USB4 USB-C spec                      | USB-IF          |
| **Physical/Media**       | PCIe trace     | PCIe 6.0 (PCI-SIG)                           | PCI-SIG         |
| **Physical/Media**       | 5G air interface| 3GPP TS 38.xxx (NR)                          | 3GPP/ETSI       |
| **Data Link**            | Ethernet frame  | IEEE 802.3                                   | IEEE SA         |
| **Data Link**            | Wi-Fi frame    | IEEE 802.11                                  | IEEE SA         |
| **Data Link**            | CAN frame      | ISO 11898-1                                  | ISO TC22        |
| **Network**              | IP routing     | RFC 791 (IPv4), RFC 8200 (IPv6)              | IETF            |
| **Transport**            | TCP            | RFC 9293                                     | IETF            |
| **Transport**            | QUIC           | RFC 9000                                     | IETF            |
| **Security**             | TLS            | RFC 8446 (TLS 1.3)                           | IETF            |
| **Application**          | HTTP/3         | RFC 9114                                     | IETF            |
| **Application**          | REST API       | OpenAPI 3.1 (contract)                       | OpenAPI Initative|
| **Application**          | Event API      | AsyncAPI 3.0                                 | AsyncAPI Initiative|
| **Application**          | RPC framework  | gRPC + Protobuf IDL                          | CNCF + Google   |
| **Identity**             | AuthN          | FIDO2/WebAuthn (W3C + FIDO Alliance)         | W3C + FIDO      |
| **Identity**             | AuthZ          | OAuth 2.0 + OIDC                             | IETF + OpenID   |
| **Container**            | Image format   | OCI Image Spec v1.1                          | OCI/Linux Fdn   |
| **Container**            | Runtime        | OCI Runtime Spec v1.2                        | OCI/Linux Fdn   |
| **Orchestration**        | Cluster API    | Kubernetes API (OpenAPI-described)           | CNCF            |
| **Data exchange**        | Structured data| JSON (RFC 8259) / Protobuf                   | IETF / Google   |
| **Data storage**         | Column analytics| Apache Parquet                              | Apache ASF      |
| **Schema**               | JSON validation| JSON Schema 2020-12                          | json-schema.org |
| **ML model**             | Model exchange | ONNX                                         | Linux Foundation|
| **Automotive E/E**       | Node communication| AUTOSAR SOME/IP + DDS (Adaptive)          | AUTOSAR + OMG   |
| **Automotive safety**    | Functional safety| ISO 26262 ASIL                             | ISO TC22        |
| **Aviation SW**          | Certification  | DO-178C DAL A–E                              | RTCA / EUROCAE  |
| **Cloud IaaS**           | VM/storage API | OpenStack API / CSP proprietary             | OpenStack Fdn   |
| **Observability**        | Telemetry      | OpenTelemetry OTLP                           | CNCF            |
| **Firmware/Embedded**    | Binary ABI     | System V AMD64 ABI (de-facto)               | No formal body  |

---

### SECTION 18.4 — OPEN VS. PROPRIETARY MATRIX

Generate a classification showing which standards are freely accessible vs. require payment or membership:

| Standard            | Access Model              | Cost to Access     | Redistribution   |
|---------------------|---------------------------|--------------------|------------------|
| IETF RFCs           | Free — open access        | $0                 | Free             |
| W3C Recommendations | Free — open access        | $0                 | Free             |
| ECMA Standards      | Free — open access        | $0                 | Free             |
| ISO/IEC standards   | Paid — purchase per document| $50–$300+        | Copyright restricted |
| IEEE standards      | Paid — IEEE Xplore        | $50–$500+         | Copyright restricted |
| SAE standards       | Paid — SAE platform       | $30–$500+         | Copyright restricted |
| JEDEC standards     | Member access (some free) | Membership or $60–$200| Restricted    |
| PCI-SIG standards   | Member access only        | Membership required| Restricted      |
| USB-IF standards    | Free (end-spec); adopter program | $0–$5,000  | Varies           |
| AUTOSAR (Classic)   | Member access             | Membership required| Restricted      |
| AUTOSAR (Adaptive)  | Member access             | Membership required| Restricted      |
| OCI Specs           | Free — GitHub             | $0                 | Apache 2.0      |
| ONNX                | Free — GitHub             | $0                 | Apache 2.0      |
| OpenAPI Spec        | Free — GitHub             | $0                 | Apache 2.0      |
| Protobuf IDL        | Free — GitHub             | $0                 | BSD / Apache    |
| FIDO2/WebAuthn      | Free — FIDO Alliance      | $0                 | Free to implement|
| OAuth 2.0 (RFC 6749)| Free — IETF              | $0                 | Free             |
| NIST publications   | Free — NIST.gov           | $0                 | Public domain (US)|
| MIPI specs          | Member access             | Membership required| Restricted      |
| Khronos specs       | Registered user (free)    | $0 registered     | Implementors license|
| DO-178C             | Purchase — RTCA           | $500+              | Copyright restricted|

---

### SECTION 18.5 — CERTIFICATION AND COMPLIANCE PATHWAY TABLE

Show which standards produce certifiable compliance vs. which are reference-only:

| Standard        | Certification Possible | Certifying Body         | Certificate Name              | Renewal Cycle  |
|-----------------|------------------------|-------------------------|-------------------------------|----------------|
| ISO/IEC 27001:2022| Yes                  | Accredited CB (UKAS, etc.)| ISMS Certificate             | 3 years (annual surveillance)|
| ISO 9001:2015   | Yes                    | Accredited CB            | QMS Certificate               | 3 years        |
| ISO/IEC 42001   | Yes                    | Accredited CB            | AI Management System          | 3 years        |
| SOC 2 Type II   | Yes (audit report)     | AICPA-licensed CPA firm  | SOC 2 Type II Report          | 12 months      |
| ISO 26262       | Assessed/developed to  | Functional safety assessors (TÜV, etc.) | Safety case | Per project |
| DO-178C         | Software artifact reviewed| FAA DER / EASA DOA    | Type certificate supplement   | Per product    |
| FIDO2           | Product certification  | FIDO Alliance            | FIDO Certified logo           | Per product version|
| Common Criteria | Yes                    | National CC body         | CC certificate (EAL 1–7)     | Per product version|
| FedRAMP         | Yes                    | FedRAMP PMO (US)         | Authorization to Operate (ATO)| Annual continuous|
| PCI DSS 4.0     | Yes                    | PCI SSC-qualified QSA    | Report on Compliance (RoC)    | Annual         |
| ISO/IEC 15408   | Yes                    | National CC bodies       | CC Certificate                | Per version    |
| NIST CSF 2.0    | No (voluntary framework)| —                       | N/A                           | N/A            |
| OWASP Top 10    | No (reference list)    | —                        | N/A                           | N/A            |
| RFC (IETF)      | No (technical spec)    | —                        | N/A                           | N/A            |
| W3C Recommendation| No (tech spec)       | —                        | N/A                           | N/A            |

---

### SECTION 18.6 — CROSS-INDUSTRY STANDARD ADOPTION MAP

Show which industries adopt which standards:

| Standard               | Auto | Aviation | Medical | Industrial | Web/Cloud | Mobile | Defense | Railway |
|------------------------|------|----------|---------|------------|-----------|--------|---------|---------|
| TLS 1.3                | ✓    | ✓        | ✓       | ✓          | ✓         | ✓      | ✓       | ✓       |
| OAuth 2.0 / OIDC       | ✓    | Limited  | ✓       | Limited    | ✓         | ✓      | Limited | Limited |
| REST/HTTP APIs         | ✓    | ✓        | ✓       | ✓          | ✓         | ✓      | ✓       | ✓       |
| gRPC                   | ✓    | Limited  | Limited | ✓          | ✓         | ✓      | Limited | Limited |
| ISO 26262              | ✓    | —        | —       | —          | —         | —      | —       | —       |
| DO-178C                | —    | ✓        | Limited | —          | —         | —      | ✓ (FAA-derived)| — |
| IEC 62061              | —    | —        | —       | ✓          | —         | —      | —       | —       |
| EN 50128               | —    | —        | —       | —          | —         | —      | —       | ✓       |
| IEC 62443              | ✓    | ✓        | ✓       | ✓          | ✓         | —      | ✓       | ✓       |
| MISRA C                | ✓    | ✓        | ✓       | ✓          | —         | —      | ✓       | ✓       |
| IEC 60601 (medical)    | —    | —        | ✓       | —          | —         | —      | —       | —       |
| FHIR (HL7)             | —    | —        | ✓       | —          | ✓         | ✓      | ✓       | —       |
| OPC UA                 | ✓    | ✓        | ✓       | ✓          | ✓         | —      | ✓       | ✓       |
| AUTOSAR                | ✓    | —        | —       | —          | —         | —      | ✓       | —       |
| GDPR compliance        | ✓    | ✓        | ✓       | ✓          | ✓         | ✓      | ✓       | ✓       |
| ISO/IEC 27001          | ✓    | ✓        | ✓       | ✓          | ✓         | ✓      | ✓       | ✓       |
| ONNX                   | ✓    | Limited  | ✓       | ✓          | ✓         | ✓      | ✓       | —       |

---

### SECTION 18.7 — STANDARDS SUPERSESSION AND DEPRECATION TABLE

| Deprecated Standard     | Replaced By              | Reason                            | Year Deprecated |
|-------------------------|--------------------------|-----------------------------------|-----------------|
| TLS 1.0, TLS 1.1        | TLS 1.3 (RFC 8446)       | Security vulnerabilities          | RFC 8996 (2021) |
| SSL 2.0, SSL 3.0        | TLS (all versions)       | POODLE, fundamental design flaws  | RFC 7568 (2015) |
| MD5 (for signatures)    | SHA-256+                 | Collision vulnerabilities         | NIST 2005       |
| SHA-1 (for signatures)  | SHA-256+ (FIPS 180-4)    | SHAttered collision attack (2017) | NIST 2022       |
| RSA-1024                | RSA-3072+, ECDSA P-256+  | Insufficient security margin      | NIST 2010       |
| OAuth 2.0 Implicit Flow | Auth Code + PKCE         | Token leakage via redirect URI    | OAuth 2.1 draft |
| CORBA (OMG)             | DDS, gRPC, REST          | Complexity, middleware decline     | De-facto 2000s  |
| SOAP/WS-*               | REST, gRPC               | Complexity, XML overhead          | De-facto 2010s  |
| IPv4 (being phased out) | IPv6 (RFC 8200)          | Address space exhaustion          | Ongoing (2011–) |
| 3DES (FIPS 46-3)        | AES (FIPS 197)           | 64-bit block Sweet32 attack       | NIST 2023       |
| DO-178B                 | DO-178C                  | Model-based, formal methods added | 2011            |
| AUTOSAR Classic         | AUTOSAR Adaptive (for ADAS/AD) | Linux/POSIX needed for HPC     | Coexistence (not full replacement)|
| HTML 4.01               | HTML5 (WHATWG Living)    | Modern web features               | 2014            |
| XHTML 1.1               | HTML5 (WHATWG Living)    | Complex XML strictness            | De-facto 2010s  |

---

### SECTION 18.8 — REGULATORY-TO-TECHNICAL-STANDARD ALIGNMENT

Show which regulatory requirements map to which technical standards:

| Regulation          | Technical Standard Alignment                                        |
|---------------------|---------------------------------------------------------------------|
| GDPR Article 32     | ISO/IEC 27001, TLS 1.3, AES-256, OAuth 2.0 + OIDC, SPDX SBOM     |
| HIPAA Security Rule | NIST SP 800-66, AES-256, TLS, FHIR security guidelines            |
| PCI DSS 4.0         | TLS 1.3 only (req 4.2.1), AES-256, OAuth 2.0, tokenization standards|
| NIS2 Directive      | ISO 27001, ETSI EN 303 645 (IoT), IEC 62443                       |
| EU AI Act           | ISO/IEC 42001, ISO/IEC 23894, NIST AI RMF                         |
| EU Cyber Resilience Act (CRA) | IEC 62443, SBOM (SPDX/CycloneDX), CVE/CVSS, SLSA         |
| UNECE WP.29 Reg 155 | ISO/SAE 21434, TARA methodology, IEC 62443                        |
| UNECE WP.29 Reg 156 | ISO 24089, OTA update security, code signing                      |
| FedRAMP             | NIST SP 800-53 Rev.5, FIPS 140-3, TLS, FIDO2                      |
| NERC CIP            | IEC 62443, ISA/IEC 62443-3-3, NIST SP 800-82                     |

---

## OUTPUT FORMAT REQUIREMENTS

- All tables must use aligned Markdown pipe syntax
- Cross-reference entries must include chapter references (e.g., "See Chapter 13")
- Supersession table must include year of deprecation
- Regulatory alignment table must cite specific article/section where applicable
- Minimum length: 3,500 words of table data plus explanatory notes
