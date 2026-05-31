# Chapter 18 — Crosswalk and Relationship Tables

## Universal Technology Standards Governance & Regulatory Architecture Encyclopedia
### First Edition, 2024–2026

---

## 18.1 Governance Body × Domain Coverage Matrix

Legend: **P** = Primary standard producer | **S** = Secondary/contributing | **A** = Adjacent/referencing | **—** = No significant involvement

| Governance Body      | Networking | Web | Security | Prog. Lang | Data Formats | Automotive | Cloud/Container | AI/ML | HW/Semiconductor | Industrial | Aviation | Identity |
|----------------------|-----------|-----|----------|-----------|-------------|------------|----------------|-------|-------------------|-----------|----------|----------|
| ISO/IEC JTC1         | S         | S   | P        | P         | P           | P          | P              | P     | S                 | P         | A        | P        |
| ITU-T                | P         | S   | S        | —         | S           | —          | S              | —     | —                 | —         | —        | S        |
| IETF                 | P         | P   | P        | —         | P           | —          | S              | —     | —                 | —         | —        | P        |
| W3C                  | —         | P   | S        | S         | S           | —          | —              | —     | —                 | —         | —        | P        |
| WHATWG               | —         | P   | —        | —         | —           | —          | —              | —     | —                 | —         | —        | —        |
| IEEE SA              | P         | —   | S        | —         | —           | S          | —              | S     | P                 | P         | —        | —        |
| ECMA TC39            | —         | P   | —        | P         | P           | —          | —              | —     | —                 | —         | —        | —        |
| OASIS                | —         | S   | P        | —         | S           | —          | S              | —     | —                 | P         | —        | P        |
| Linux Foundation     | —         | S   | S        | —         | P           | S          | P              | P     | —                 | —         | —        | S        |
| Apache ASF           | S         | —   | —        | —         | P           | —          | S              | S     | —                 | —         | —        | —        |
| CNCF                 | —         | —   | S        | —         | S           | —          | P              | S     | —                 | —         | —        | S        |
| FIDO Alliance        | —         | —   | P        | —         | —           | —          | —              | —     | —                 | —         | —        | P        |
| OpenID Foundation    | —         | —   | P        | —         | —           | —          | —              | —     | —                 | —         | —        | P        |
| PCI-SIG              | —         | —   | —        | —         | —           | S          | S              | —     | P                 | S         | —        | —        |
| USB-IF               | —         | —   | —        | —         | —           | S          | —              | —     | P                 | —         | —        | —        |
| JEDEC                | —         | —   | —        | —         | —           | S          | —              | —     | P                 | —         | —        | —        |
| NVMe.org             | —         | —   | —        | —         | —           | —          | S              | —     | P                 | —         | —        | —        |
| VESA                 | —         | —   | —        | —         | —           | —          | —              | —     | P                 | —         | —        | —        |
| MIPI Alliance        | —         | —   | —        | —         | —           | P          | —              | —     | P                 | —         | —        | —        |
| Khronos Group        | —         | P   | —        | —         | —           | —          | —              | S     | P                 | —         | —        | —        |
| OMG                  | —         | —   | —        | S         | S           | S          | —              | —     | —                 | P         | A        | —        |
| AUTOSAR              | —         | —   | S        | —         | —           | P          | —              | —     | —                 | —         | —        | —        |
| SAE International    | —         | —   | —        | —         | —           | P          | —              | —     | —                 | —         | P        | —        |
| RTCA                 | —         | —   | —        | —         | —           | —          | —              | —     | —                 | —         | P        | —        |
| NIST                 | S         | —   | P        | —         | —           | S          | S              | P     | —                 | S         | —        | P        |
| FCC                  | P         | —   | —        | —         | —           | S          | —              | —     | S                 | —         | —        | —        |
| ETSI                 | P         | —   | S        | —         | —           | S          | S              | S     | —                 | S         | —        | S        |
| EU Commission        | A         | A   | A        | —         | —           | A          | A              | A     | —                 | A         | A        | A        |
| 3GPP                 | P         | —   | S        | —         | —           | S          | S              | —     | —                 | —         | —        | —        |
| MLCommons            | —         | —   | —        | —         | —           | —          | —              | P     | S                 | —         | —        | —        |

---

## 18.2 Standards Interoperability Dependency Map

| Standard               | Depends On / References                                             | Used By / Depended On By                             |
|------------------------|---------------------------------------------------------------------|------------------------------------------------------|
| TLS 1.3 (RFC 8446)    | X.509/PKIX (RFC 5280), AES-GCM, ECDHE (RFC 7748), SHA-256, HKDF  | HTTPS, gRPC, MQTT/TLS, QUIC, OPC UA, DoIP, FIDO2   |
| OpenAPI 3.1            | JSON Schema 2020-12, JSON (RFC 8259), HTTP (RFC 9110), OAuth 2.0  | REST API documentation, API gateways, code generators|
| OAuth 2.0              | TLS (transport mandate), JWT (RFC 7519), PKCE (RFC 7636)          | OIDC, FAPI 2.0, SCIM, API gateways                  |
| OIDC 1.0               | OAuth 2.0, JWT, JWK (RFC 7517), JWKS discovery                    | SSO providers, FIDO2 (Passkeys), SCIM               |
| WebAuthn Level 3       | FIDO CTAP2, CBOR (RFC 8949), COSE (RFC 8152), TLS origin binding | Passkeys, browser AuthN, enterprise passwordless     |
| gRPC                   | HTTP/2 (RFC 9113), Protobuf IDL, TLS 1.3                          | Kubernetes API, CRI, Envoy xDS, service meshes       |
| Kubernetes API         | OpenAPI 3.x, JSON/YAML, gRPC, etcd, OCI Image Spec, CNI/CSI/CRI | Helm, ArgoCD, Istio, Crossplane, operators           |
| OCI Image Spec         | tar (POSIX), JSON (RFC 8259), SHA-256 content-addressable digest  | Docker, Podman, containerd, BuildKit, k8s registries |
| AUTOSAR Adaptive       | POSIX PSE51, DDS (OMG), TLS, C++14/17, SOME/IP, ara::com        | Vehicle HPC platforms, ADAS ECUs                     |
| ISO 26262              | IEC 61508 (parent), HARA method, FMEA (IEC 60812), FTA (IEC 61025)| AUTOSAR, OEM safety cases, MISRA C compliance       |
| CAN FD (ISO 11898)     | ISO 11898-1 physical layer, ISO 15765 (transport)                 | OBD-II, J1939, UDS (ISO 14229), AUTOSAR COM         |
| DO-178C                | DO-330 (tools), DO-331 (MBD), DO-332 (OOT), DO-333 (formal)     | FAA/EASA type certification, avionics software       |
| ONNX                   | Protobuf (serialization), NumPy dtype system, opset versioning    | ONNX Runtime, TensorRT, OpenVINO, CoreML converter  |
| Apache Parquet         | Apache Thrift (metadata), Snappy/LZ4/Zstd compression            | Spark, Hive, Delta Lake, Iceberg, Pandas, Polars     |
| OpenTelemetry (OTLP)   | HTTP/2 or gRPC (transport), Protobuf (encoding), W3C Trace Context| Jaeger, Prometheus, Grafana, Datadog, service meshes |
| ISO/IEC 27001:2022     | ISO 31000 (risk management), ISO 27002 (controls guidance)        | SOC 2 mapping, GDPR Article 32, NIS2 compliance     |
| NIST CSF 2.0           | NIST SP 800-53 (controls), ISO 27001 (informative reference)     | Federal agencies, CRI Profile, sector-specific frameworks |

---

## 18.3 Technology Stack Layer × Standard Assignment

| Layer              | Sub-domain          | Primary Standard(s)                     | Governance Body     |
|--------------------|--------------------|-----------------------------------------|---------------------|
| Physical/Media     | Ethernet copper    | IEEE 802.3                              | IEEE SA             |
| Physical/Media     | Ethernet fiber     | IEEE 802.3                              | IEEE SA             |
| Physical/Media     | USB cable          | USB4 v2 / USB-C                         | USB-IF              |
| Physical/Media     | PCIe trace         | PCIe 6.0                                | PCI-SIG             |
| Physical/Media     | 5G air interface   | 3GPP TS 38.xxx (NR)                     | 3GPP/ETSI           |
| Physical/Media     | Wi-Fi radio        | IEEE 802.11be (Wi-Fi 7)                 | IEEE SA             |
| Physical/Media     | Automotive PHY     | MIPI A-PHY, 100BASE-T1 (IEEE 802.3cg)  | MIPI/IEEE           |
| Data Link          | Ethernet frame     | IEEE 802.3 MAC                          | IEEE SA             |
| Data Link          | Wi-Fi frame        | IEEE 802.11                             | IEEE SA             |
| Data Link          | CAN frame          | ISO 11898-1                             | ISO TC22            |
| Data Link          | VLAN tagging       | IEEE 802.1Q                             | IEEE SA             |
| Network            | IP routing         | IPv4 (RFC 791), IPv6 (RFC 8200)         | IETF                |
| Network            | Routing protocols  | BGP (RFC 4271), OSPF (RFC 2328)         | IETF                |
| Transport          | Reliable stream    | TCP (RFC 9293)                          | IETF                |
| Transport          | Datagram           | UDP (RFC 768)                           | IETF                |
| Transport          | Modern transport   | QUIC (RFC 9000)                         | IETF                |
| Security           | Channel encryption | TLS 1.3 (RFC 8446)                     | IETF                |
| Security           | Network VPN        | IPsec (RFC 4301), WireGuard             | IETF                |
| Security           | Certificate        | X.509 (RFC 5280), ACME (RFC 8555)      | ITU-T/IETF          |
| Session/Presentation | Serialization   | JSON (RFC 8259), Protobuf, CBOR         | IETF/Google/IETF    |
| Application        | Web transfer       | HTTP/2 (RFC 9113), HTTP/3 (RFC 9114)    | IETF                |
| Application        | REST API contract  | OpenAPI 3.1                             | OpenAPI Initiative   |
| Application        | Event API contract | AsyncAPI 3.0                            | AsyncAPI Initiative  |
| Application        | RPC                | gRPC + Protobuf                         | CNCF/Google          |
| Application        | IoT messaging      | MQTT 5.0, CoAP (RFC 7252)              | OASIS/IETF           |
| Identity           | Authentication     | FIDO2/WebAuthn + OIDC                   | FIDO/W3C + OpenID Fdn|
| Identity           | Authorization      | OAuth 2.0 (RFC 6749)                    | IETF                 |
| Identity           | Provisioning       | SCIM 2.0 (RFC 7643/7644)               | IETF                 |
| Container          | Image format       | OCI Image Spec v1.1                     | OCI/Linux Fdn        |
| Container          | Runtime            | OCI Runtime Spec v1.2                   | OCI/Linux Fdn        |
| Orchestration      | Cluster management | Kubernetes API                          | CNCF                 |
| Observability      | Telemetry          | OpenTelemetry (OTLP)                    | CNCF                 |
| Observability      | Metrics            | Prometheus / OpenMetrics                | CNCF                 |
| Data Storage       | Column analytics   | Apache Parquet                          | Apache ASF           |
| Data Storage       | Table format       | Apache Iceberg / Delta Lake             | Apache ASF / LF      |
| ML/AI              | Model interchange  | ONNX                                    | LF AI & Data         |
| Automotive E/E     | Middleware         | AUTOSAR SOME/IP + Adaptive ara::com     | AUTOSAR              |
| Automotive Safety  | Functional safety  | ISO 26262 (ASIL)                        | ISO TC22             |
| Aviation SW        | Certification      | DO-178C (DAL A–E)                       | RTCA/EUROCAE         |
| Industrial         | Control security   | IEC 62443                               | IEC/ISA              |

---

## 18.4 Open vs. Proprietary Classification

| Access Category        | Standards                                                             |
|------------------------|-----------------------------------------------------------------------|
| **Free — Open Access** | IETF RFCs, W3C Recommendations, WHATWG specs, ECMA standards, NIST publications, OCI specs, ONNX, OpenAPI, RISC-V ISA, Khronos specs (registered), CloudEvents, OpenTelemetry |
| **Free — Open Source License** | Kubernetes API (Apache 2.0), gRPC (Apache 2.0), Protobuf (BSD), Apache Parquet/Arrow/Avro (Apache 2.0), PyTorch (BSD-3), TensorFlow (Apache 2.0), Envoy (Apache 2.0) |
| **Paid — Per Document** | ISO/IEC standards ($50–$300+), IEEE standards ($50–$500+), SAE standards ($30–$500+), DO-178C/DO-254 ($500+) |
| **Member Access Only** | PCI-SIG specs, JEDEC standards, AUTOSAR specifications, MIPI Alliance specs, HDMI Forum specs, CXL Consortium specs |
| **Hybrid (Free spec + Paid certification/logo)** | USB-IF (free spec, paid compliance program), Wi-Fi Alliance (free 802.11 spec from IEEE, paid Wi-Fi certification), NVMe (free spec, logo program) |

---

## 18.5 Certification Pathway Table

| Standard         | Certification Type     | Certifying Entity              | Output                      | Renewal         |
|------------------|------------------------|--------------------------------|-----------------------------|-----------------|
| ISO/IEC 27001    | Accredited 3rd-party   | UKAS/ANAB-accredited CB       | ISMS Certificate            | 3 years + annual surveillance |
| ISO/IEC 42001    | Accredited 3rd-party   | Accredited CB                 | AIMS Certificate            | 3 years         |
| ISO 9001         | Accredited 3rd-party   | Accredited CB                 | QMS Certificate             | 3 years         |
| ISO 26262        | Assessment             | TÜV, SGS, Bureau Veritas     | Functional safety assessment| Per project     |
| DO-178C          | Regulatory             | FAA DER / EASA DOA            | Type certificate supplement | Per product/STC |
| EN 50128         | Assessment             | Notified Body (NoBo)          | Safety case acceptance      | Per project     |
| SOC 2 Type II    | Audit                  | AICPA-licensed CPA firm       | SOC 2 Type II Report        | Annual          |
| PCI DSS 4.0      | Assessment             | PCI SSC QSA (Qualified Security Assessor)| RoC / SAQ      | Annual          |
| FedRAMP          | Authorization          | FedRAMP PMO + 3PAO            | ATO (Authorization to Operate)| Annual continuous monitoring |
| Common Criteria  | Evaluation             | National CC scheme (BSI, ANSSI, etc.)| CC Certificate (EAL 1–7)| Per product version |
| FIDO2            | Conformance + interop  | FIDO Alliance                 | FIDO Certified logo         | Per product     |
| Wi-Fi            | Conformance            | Wi-Fi Alliance                | Wi-Fi CERTIFIED badge       | Per product     |
| USB4             | Compliance testing     | USB-IF authorized labs        | USB4 logo certification     | Per product     |
| DisplayPort      | Compliance testing     | VESA authorized labs          | DP certification            | Per product     |
| Matter           | Conformance            | CSA (Connectivity Standards Alliance)| Matter certification  | Per product     |
| NIST CSF 2.0     | None (voluntary)       | —                             | Self-assessment only        | N/A             |
| OWASP Top 10     | None (reference list)  | —                             | N/A                         | N/A             |

---

## 18.6 Cross-Industry Standard Adoption

| Standard            | Automotive | Aviation | Medical | Industrial | Web/Cloud | Mobile | Defense | Railway | Energy |
|---------------------|-----------|----------|---------|-----------|-----------|--------|---------|---------|--------|
| TLS 1.3             | ✓         | ✓        | ✓       | ✓         | ✓         | ✓      | ✓       | ✓       | ✓      |
| OAuth 2.0 / OIDC    | ✓         | Limited  | ✓       | Limited   | ✓         | ✓      | ✓       | —       | Limited|
| gRPC                | ✓         | Limited  | Limited | ✓         | ✓         | ✓      | Limited | —       | Limited|
| ISO 26262           | ✓         | —        | —       | —         | —         | —      | —       | —       | —      |
| DO-178C             | —         | ✓        | —       | —         | —         | —      | ✓       | —       | —      |
| IEC 62443           | ✓         | ✓        | ✓       | ✓         | ✓         | —      | ✓       | ✓       | ✓      |
| IEC 61508           | Adapted   | Derived  | Inspired| ✓         | —         | —      | ✓       | ✓       | ✓      |
| MISRA C             | ✓         | ✓        | ✓       | ✓         | —         | —      | ✓       | ✓       | ✓      |
| ISO/IEC 27001       | ✓         | ✓        | ✓       | ✓         | ✓         | ✓      | ✓       | ✓       | ✓      |
| ONNX                | ✓         | Limited  | ✓       | ✓         | ✓         | ✓      | ✓       | —       | Limited|
| OPC UA              | ✓         | ✓        | ✓       | ✓         | ✓         | —      | ✓       | ✓       | ✓      |
| AUTOSAR             | ✓         | —        | —       | —         | —         | —      | ✓       | —       | —      |
| OpenTelemetry       | Limited   | Limited  | ✓       | ✓         | ✓         | ✓      | ✓       | —       | Limited|
| GDPR (ISO 27701)    | ✓         | ✓        | ✓       | ✓         | ✓         | ✓      | ✓       | ✓       | ✓      |
| SBOM (SPDX/CDX)     | ✓         | ✓        | ✓       | ✓         | ✓         | ✓      | ✓       | ✓       | ✓      |
| Kubernetes          | Limited   | —        | Limited | ✓         | ✓         | ✓      | ✓       | —       | ✓      |

---

## 18.7 Standards Supersession and Deprecation

| Deprecated Standard        | Replaced By                  | Reason                              | Year Deprecated  |
|----------------------------|------------------------------|-------------------------------------|------------------|
| SSL 2.0 / SSL 3.0         | TLS 1.0+ (ultimately TLS 1.3)| POODLE, fundamental design flaws   | RFC 7568 (2015)  |
| TLS 1.0 / TLS 1.1         | TLS 1.2, TLS 1.3            | Multiple vulnerabilities            | RFC 8996 (2021)  |
| MD5 (for signatures)       | SHA-256+                     | Collision attacks (2004)            | NIST 2005        |
| SHA-1 (for signatures)     | SHA-256+ (FIPS 180-4)       | SHAttered collision (2017)          | NIST 2022        |
| RSA-1024                   | RSA-3072+, ECDSA P-256+     | Insufficient security margin        | NIST 2010        |
| 3DES (FIPS 46-3)           | AES (FIPS 197)               | Sweet32 attack, 64-bit block       | NIST 2023        |
| OAuth 2.0 Implicit Flow    | Authorization Code + PKCE    | Token leakage via redirect URI     | OAuth 2.1 (draft)|
| SOAP / WS-*               | REST, gRPC                   | Complexity, XML overhead           | De-facto ~2010   |
| CORBA (OMG)               | REST, gRPC, DDS              | Complexity, middleware decline      | De-facto ~2005   |
| DO-178B                    | DO-178C                      | Model-based, formal methods added  | 2011             |
| HTML 4.01 / XHTML 1.1     | HTML5 (WHATWG Living)        | Modern web requirements            | 2014             |
| IPv4 (transitioning)       | IPv6 (RFC 8200)              | Address space exhaustion           | Ongoing (2011–)  |
| HTTP/1.0                   | HTTP/1.1, HTTP/2, HTTP/3     | Persistent connections, multiplexing| De-facto ~2000  |
| AUTOSAR Classic (for ADAS) | AUTOSAR Adaptive             | POSIX/Linux needed for HPC          | Coexistence     |
| USB 2.0 Type-A (for high-speed) | USB-C + USB4           | Speed, power, universality          | Ongoing         |

---

## 18.8 Regulatory-to-Technical-Standard Alignment

| Regulation / Law              | Article/Section        | Technical Standard Alignment                           |
|-------------------------------|------------------------|--------------------------------------------------------|
| GDPR (EU 2016/679)           | Article 32             | ISO/IEC 27001, TLS 1.3, AES-256, pseudonymization     |
| GDPR                         | Article 25             | Privacy by design → ISO/IEC 27701, data minimization  |
| HIPAA Security Rule (US)     | 45 CFR §164.312       | NIST SP 800-66, AES-256, TLS, access controls          |
| PCI DSS 4.0                  | Req 4.2.1             | TLS 1.2+ only (TLS 1.3 recommended)                   |
| PCI DSS 4.0                  | Req 8.3               | MFA required → FIDO2, TOTP                            |
| NIS2 Directive (EU 2022/2555)| Article 21             | ISO 27001, ETSI EN 303 645 (IoT), IEC 62443           |
| EU AI Act                    | Article 9 (high-risk)  | ISO/IEC 42001, ISO/IEC 23894, NIST AI RMF             |
| EU Cyber Resilience Act (CRA)| Product security       | IEC 62443, SBOM (SPDX/CycloneDX), CVSS, SLSA         |
| UNECE WP.29 Reg 155         | Cybersecurity CSMS    | ISO/SAE 21434, TARA methodology                        |
| UNECE WP.29 Reg 156         | Software updates      | ISO 24089, code signing, secure OTA                    |
| FedRAMP (US)                 | Authorization         | NIST SP 800-53 Rev.5, FIPS 140-3, FIDO2              |
| NERC CIP (US energy)        | CIP-002 through CIP-014| IEC 62443, NIST SP 800-82                            |
| FDA 21 CFR Part 11          | Electronic records    | IEC 62304 (SW), digital signatures, audit trails       |
| EU Machinery Regulation 2023/1230 | Annex III       | IEC 62061, ISO 13849-1                                 |

---

*End of Chapter 18 — Crosswalk and Relationship Tables*
*Next: Chapters 19 & 20 — Master Classification Matrix & Entry Template Guide*
