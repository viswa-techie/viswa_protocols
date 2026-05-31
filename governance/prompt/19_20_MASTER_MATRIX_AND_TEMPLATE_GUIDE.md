# CHAPTER 19 — MASTER CLASSIFICATION MATRIX
# CHAPTER 20 — ENTRY TEMPLATE AND AUTHORING GUIDE
# Prompt for: Universal Technology Standards Governance Encyclopedia
# Depends on: MASTER_PROMPT.md, ALL Chapters 1–18

---

# CHAPTER 19: MASTER CLASSIFICATION MATRIX

## CHAPTER SCOPE

This chapter generates the **master reference index** — a flat, sortable matrix of all 200+ standards, protocols, specifications, and governance artifacts covered in Chapters 1–17.

Each row represents one standard/specification. Columns allow sorting by:
- Tier (L0–L8)
- Governance body
- Domain
- Access model (open/paid)
- Certification availability
- Current status (active/deprecated/emerging)

This is the encyclopedia's primary reference table — it should be usable as a standalone lookup.

---

## GENERATION INSTRUCTIONS FOR CHAPTER 19

Act as a world-class standards taxonomist and reference librarian for technology standards.
Generate the complete **Chapter 19: Master Classification Matrix**.

### SECTION 19.1 — HOW TO USE THIS MATRIX

Explain:
- Column definitions
- How to read the tier designations (L0–L8)
- How to interpret the status codes (A=Active, D=Deprecated, E=Emerging, L=Legacy)
- How to use the matrix for standards selection in a project
- Cross-references to detailed chapter entries

### SECTION 19.2 — MASTER STANDARDS MATRIX

Generate the full matrix. Every entry must have all columns populated.
Do not omit any standard covered in Chapters 1–17.

**Column definitions:**
| Column        | Description                                         |
|---------------|-----------------------------------------------------|
| Short Name    | Common abbreviation (TLS, OpenAPI, AES, etc.)      |
| Full Name     | Complete official name                              |
| Tier          | L0–L8 per MASTER_PROMPT.md taxonomy               |
| Category      | Cryptography / Networking / Identity / Data / Automotive / etc. |
| Governing Body| ISO, IETF, IEEE, OMG, AUTOSAR, etc.               |
| Current Version| Most recent published version or release year    |
| Status        | A (Active), D (Deprecated), E (Emerging), L (Legacy) |
| Access        | Free / Paid / Member                               |
| Certifiable   | Y (yes) / N (no) / A (audit report)               |
| Key RFC/Doc   | Primary normative document reference              |
| Chapter Ref   | Chapter in this encyclopedia with full entry      |

**Master Matrix — ALL entries (200+ rows grouped by category):**

**CRYPTOGRAPHIC STANDARDS:**

| Short Name  | Tier | Category | Governing Body | Version/Year | Status | Access | Certifiable | Key Doc      | Ch. Ref |
|-------------|------|----------|----------------|--------------|--------|--------|-------------|--------------|---------|
| AES         | L2   | Crypto   | NIST           | FIPS 197 (2001/2023 rev) | A | Free | N      | FIPS 197     | Ch.13   |
| 3DES        | L2   | Crypto   | NIST           | FIPS 46-3    | D      | Free   | N           | FIPS 46-3    | Ch.13   |
| ChaCha20-Poly1305| L3 | Crypto | IETF          | RFC 8439     | A      | Free   | N           | RFC 8439     | Ch.13   |
| SHA-256/384/512| L2 | Hash   | NIST           | FIPS 180-4   | A      | Free   | N           | FIPS 180-4   | Ch.13   |
| SHA-3       | L2   | Hash     | NIST           | FIPS 202     | A      | Free   | N           | FIPS 202     | Ch.13   |
| RSA         | L2   | Asym Crypto| NIST/IETF    | FIPS 186-5 / RFC 8017 | A (legacy) | Free | N | FIPS 186-5 | Ch.13 |
| ECDSA       | L2   | Asym Crypto| NIST          | FIPS 186-5   | A      | Free   | N           | FIPS 186-5   | Ch.13   |
| EdDSA/Ed25519| L3  | Asym Crypto| IETF/NIST    | RFC 8032 / FIPS 186-5 | A | Free | N        | RFC 8032     | Ch.13   |
| ML-KEM (FIPS 203)| L2 | PQC  | NIST           | FIPS 203 (2024)| A    | Free   | N           | FIPS 203     | Ch.13   |
| ML-DSA (FIPS 204)| L2 | PQC  | NIST           | FIPS 204 (2024)| A    | Free   | N           | FIPS 204     | Ch.13   |
| SLH-DSA (FIPS 205)| L2 | PQC | NIST          | FIPS 205 (2024)| A    | Free   | N           | FIPS 205     | Ch.13   |
| HMAC        | L3   | MAC      | IETF/NIST      | RFC 2104 / FIPS 198 | A  | Free   | N           | RFC 2104     | Ch.13   |
| PBKDF2      | L3   | KDF      | IETF           | RFC 8018     | A      | Free   | N           | RFC 8018     | Ch.13   |
| Argon2      | L5   | KDF      | IETF/Community | RFC 9106     | A      | Free   | N           | RFC 9106     | Ch.13   |

**PROTOCOL SECURITY:**

| Short Name  | Tier | Category   | Governing Body | Version/Year | Status | Access | Certifiable | Key Doc   | Ch. Ref |
|-------------|------|------------|----------------|--------------|--------|--------|-------------|-----------|---------|
| TLS 1.3     | L3   | Protocol Security| IETF    | RFC 8446     | A      | Free   | N           | RFC 8446  | Ch.13   |
| TLS 1.2     | L3   | Protocol Security| IETF    | RFC 5246     | L      | Free   | N           | RFC 5246  | Ch.13   |
| DTLS 1.3    | L3   | Protocol Security| IETF    | RFC 9147     | A      | Free   | N           | RFC 9147  | Ch.13   |
| X.509/PKIX  | L1/L3| PKI        | ITU-T/IETF     | RFC 5280     | A      | Free   | N           | RFC 5280  | Ch.13   |
| IPsec       | L3   | VPN/Security| IETF          | RFC 4301     | A      | Free   | N           | RFC 4301  | Ch.13   |
| IKEv2       | L3   | Key Exchange| IETF          | RFC 7296     | A      | Free   | N           | RFC 7296  | Ch.13   |
| WireGuard   | L3   | VPN        | IETF           | RFC 9107     | A      | Free   | N           | RFC 9107  | Ch.13   |
| SSH v2      | L3   | Remote Access| IETF         | RFC 4251-4256| A      | Free   | N           | RFC 4253  | Ch.13   |
| DNSSEC      | L3   | DNS Security| IETF          | RFC 4033-4035| A      | Free   | N           | RFC 4033  | Ch.13   |
| OpenPGP     | L3   | E2E Crypto | IETF           | RFC 9580     | A      | Free   | N           | RFC 9580  | Ch.13   |

**IDENTITY AND ACCESS MANAGEMENT:**

| Short Name       | Tier | Category | Governing Body    | Version/Year | Status | Access | Certifiable | Key Doc   | Ch. Ref |
|------------------|------|----------|-------------------|--------------|--------|--------|-------------|-----------|---------|
| OAuth 2.0        | L3   | AuthZ    | IETF              | RFC 6749     | A      | Free   | N           | RFC 6749  | Ch.13   |
| PKCE             | L3   | AuthZ    | IETF              | RFC 7636     | A      | Free   | N           | RFC 7636  | Ch.13   |
| OAuth 2.1        | L3   | AuthZ    | IETF              | Draft (2024) | E      | Free   | N           | Draft     | Ch.13   |
| OIDC 1.0         | L3   | AuthN/AuthZ| OpenID Foundation| Final Spec 2014 | A  | Free   | N           | openid.net spec| Ch.13|
| SAML 2.0         | L3   | Federated SSO| OASIS          | 2005         | A      | Free   | N           | OASIS spec| Ch.13  |
| SCIM 2.0         | L3   | Provisioning| IETF           | RFC 7643/7644| A      | Free   | N           | RFC 7643  | Ch.13   |
| FIDO2            | L3/L3| AuthN    | FIDO Alliance + W3C| 2019+       | A      | Free   | Y           | FIDO2 spec| Ch.13  |
| WebAuthn Level 2 | L3   | AuthN    | W3C              | 2021         | A      | Free   | N           | W3C spec  | Ch.13   |
| TOTP             | L3   | AuthN    | IETF             | RFC 6238     | A      | Free   | N           | RFC 6238  | Ch.13   |
| KMIP             | L3   | Key Mgmt | OASIS            | v2.0 (2019)  | A      | Free   | N           | OASIS spec| Ch.13  |

**INTERNET / NETWORKING PROTOCOLS:**

| Short Name  | Tier | Category | Governing Body | Version/Year | Status | Access | Certifiable | Key Doc   | Ch. Ref |
|-------------|------|----------|----------------|--------------|--------|--------|-------------|-----------|---------|
| IPv4        | L3   | Network  | IETF           | RFC 791      | L (still used)| Free| N      | RFC 791   | Ch.12   |
| IPv6        | L3   | Network  | IETF           | RFC 8200     | A      | Free   | N           | RFC 8200  | Ch.12   |
| TCP         | L3   | Transport| IETF           | RFC 9293     | A      | Free   | N           | RFC 9293  | Ch.12   |
| UDP         | L3   | Transport| IETF           | RFC 768      | A      | Free   | N           | RFC 768   | Ch.12   |
| QUIC        | L3   | Transport| IETF           | RFC 9000     | A      | Free   | N           | RFC 9000  | Ch.12   |
| HTTP/1.1    | L3   | Application| IETF         | RFC 9112     | A/L    | Free   | N           | RFC 9112  | Ch.12   |
| HTTP/2      | L3   | Application| IETF         | RFC 9113     | A      | Free   | N           | RFC 9113  | Ch.12   |
| HTTP/3      | L3   | Application| IETF         | RFC 9114     | A      | Free   | N           | RFC 9114  | Ch.12   |
| DNS         | L3   | Naming   | IETF           | RFC 1034/1035| A      | Free   | N           | RFC 1034  | Ch.12   |
| MQTT 5.0    | L3   | IoT Msg  | OASIS          | v5.0 (2019)  | A      | Free   | N           | OASIS spec| Ch.12  |
| AMQP 1.0    | L3   | Messaging| OASIS          | 2012         | A      | Free   | N           | OASIS spec| Ch.12  |
| XMPP        | L3   | Messaging| IETF           | RFC 6120     | A/L    | Free   | N           | RFC 6120  | Ch.12   |
| CoAP        | L3   | IoT      | IETF           | RFC 7252     | A      | Free   | N           | RFC 7252  | Ch.12   |
| WebSocket   | L3   | Application| IETF/W3C     | RFC 6455     | A      | Free   | N           | RFC 6455  | Ch.14   |

**WEB STANDARDS:**

| Short Name      | Tier | Category    | Governing Body | Version/Year | Status | Access | Certifiable | Key Doc    | Ch. Ref |
|-----------------|------|-------------|----------------|--------------|--------|--------|-------------|------------|---------|
| HTML5           | L3   | Web markup  | WHATWG         | Living Standard| A    | Free   | N           | html.spec.whatwg.org| Ch.3 |
| CSS             | L3   | Web styling | W3C            | CSS3 modules | A      | Free   | N           | W3C specs  | Ch.3    |
| JavaScript (ECMAScript)| L3| Language  | ECMA TC39      | ES2024       | A      | Free   | N           | ECMA-262   | Ch.3    |
| WebAssembly     | L3   | Web runtime | W3C/WASI       | 2.0 draft    | A      | Free   | N           | W3C spec   | Ch.3    |
| DOM             | L3   | Web API     | WHATWG         | Living Standard| A    | Free   | N           | dom.spec.whatwg.org| Ch.3|
| URL             | L3   | Web         | WHATWG         | Living Standard| A    | Free   | N           | url.spec.whatwg.org| Ch.3|
| Fetch API       | L3   | Web API     | WHATWG         | Living Standard| A    | Free   | N           | WHATWG spec| Ch.3   |

**API AND DATA EXCHANGE:**

| Short Name      | Tier | Category      | Governing Body  | Version/Year | Status | Access | Certifiable | Key Doc       | Ch. Ref |
|-----------------|------|---------------|-----------------|--------------|--------|--------|-------------|---------------|---------|
| OpenAPI 3.1     | L3   | API spec      | OpenAPI Init.   | 3.1.0        | A      | Free   | N           | spec.openapis.org| Ch.14 |
| AsyncAPI 3.0    | L3   | Event API spec| AsyncAPI Init.  | 3.0.0        | A      | Free   | N           | asyncapi.com  | Ch.14   |
| GraphQL         | L6/L3| Query lang    | GraphQL Fdn/Meta| 2021 spec    | A      | Free   | N           | graphql.org   | Ch.7    |
| gRPC            | L5/L6| RPC framework | CNCF/Google     | grpc.io spec | A      | Free   | N           | grpc.github.io| Ch.7    |
| JSON (RFC 8259) | L3/L8| Data format   | IETF            | RFC 8259     | A      | Free   | N           | RFC 8259      | Ch.3    |
| JSON Schema     | L8   | Schema validation| json-schema.org| 2020-12   | A      | Free   | N           | json-schema.org| Ch.16  |
| XML             | L3   | Data format   | W3C             | 1.0 (5th ed) | A/L    | Free   | N           | W3C spec      | Ch.2    |
| CBOR            | L3   | Binary JSON   | IETF            | RFC 8949     | A      | Free   | N           | RFC 8949      | Ch.16   |
| Protobuf        | L6   | IDL+binary    | Google          | proto3       | A      | Free   | N           | github.com/protocolbuffers| Ch.7 |
| Apache Avro     | L5   | Schema+binary | Apache ASF      | 1.12.x       | A      | Free   | N           | avro.apache.org| Ch.16  |
| Apache Parquet  | L5   | Column store  | Apache ASF      | 2.10.0       | A      | Free   | N           | parquet.apache.org| Ch.16|
| Apache Arrow    | L5   | In-memory col | Apache ASF      | 15.x         | A      | Free   | N           | arrow.apache.org| Ch.16 |
| MessagePack     | L8   | Binary JSON   | Community       | spec 2013    | A      | Free   | N           | msgpack.org   | Ch.16   |
| ONNX            | L5   | ML model exch.| Linux Fdn AI    | opset 21     | A      | Free   | N           | onnx.ai       | Ch.16   |

**AUTOMOTIVE AND EMBEDDED:**

| Short Name      | Tier | Category        | Governing Body | Version/Year | Status | Access | Certifiable | Key Doc        | Ch. Ref |
|-----------------|------|-----------------|----------------|--------------|--------|--------|-------------|----------------|---------|
| CAN 2.0 / CAN FD| L1  | Vehicle bus     | ISO TC22       | ISO 11898    | A      | Paid   | N           | ISO 11898-1    | Ch.11   |
| LIN 2.2A        | L1   | Vehicle bus     | ISO TC22       | ISO 17987    | A      | Paid   | N           | ISO 17987      | Ch.11   |
| FlexRay         | L3   | Vehicle bus     | FlexRay Consortium→ISO| ISO 17458 | L   | Paid   | N           | ISO 17458      | Ch.11   |
| SOME/IP         | L3   | Auto middleware | AUTOSAR        | PRS_SOMEIPProtocol| A | Member | N          | AUTOSAR spec   | Ch.11   |
| ISO 26262:2018  | L1   | Functional safety| ISO TC22      | Ed.2 (2018)  | A      | Paid   | Y (assessed)| ISO 26262      | Ch.11   |
| ISO 21448 (SOTIF)| L1  | ADAS safety    | ISO TC22       | 2022         | A      | Paid   | N           | ISO 21448      | Ch.11   |
| ISO/SAE 21434   | L1   | Cybersecurity  | ISO/SAE        | 2021         | A      | Paid   | Y (assessed)| ISO 21434      | Ch.11   |
| AUTOSAR Classic | L3   | ECU middleware | AUTOSAR        | R22-11       | A      | Member | N           | autosar.org    | Ch.4,11 |
| AUTOSAR Adaptive| L3   | HPC middleware | AUTOSAR        | R23-11       | A      | Member | N           | autosar.org    | Ch.4,11 |
| UDS (ISO 14229) | L1   | Diagnostics    | ISO TC22       | 2020         | A      | Paid   | N           | ISO 14229-1    | Ch.11   |
| DoIP (ISO 13400)| L1   | Diag over IP   | ISO TC22       | 2019         | A      | Paid   | N           | ISO 13400      | Ch.11   |
| OBD-II          | L2/L4| Emissions diag | SAE/EPA/ISO   | SAE J1979    | A      | Paid   | N           | SAE J1979      | Ch.11   |
| SAE J1939       | L4   | Heavy vehicle  | SAE Intl       | 2015         | A      | Paid   | N           | SAE J1939      | Ch.5,11 |
| MISRA C:2023    | L5   | Safe C subset  | MISRA          | 2023         | A      | Paid   | N           | misra.org.uk   | Ch.11   |
| UNECE Reg 155   | L2   | Cybersecurity  | UNECE WP.29    | 2021         | A      | Free   | Y (type approval)| UN R155   | Ch.10,11|
| UNECE Reg 156   | L2   | OTA updates    | UNECE WP.29    | 2021         | A      | Free   | Y (type approval)| UN R156   | Ch.10,11|

**HARDWARE / SEMICONDUCTOR:**

| Short Name  | Tier | Category   | Governing Body | Version/Year | Status | Access | Certifiable | Key Doc         | Ch. Ref |
|-------------|------|------------|----------------|--------------|--------|--------|-------------|-----------------|---------|
| PCIe 6.0    | L3   | I/O bus    | PCI-SIG        | 2022         | A      | Member | N           | pci-sig.com     | Ch.15   |
| USB4 v2     | L3   | I/O bus    | USB-IF         | 2022         | A      | Free   | Y (logo)    | usb.org         | Ch.15   |
| DDR5        | L3   | Memory     | JEDEC          | JESD79-5B    | A      | Member | N           | jedec.org       | Ch.15   |
| LPDDR5X     | L3   | Mobile Mem | JEDEC          | JESD209-5C   | A      | Member | N           | jedec.org       | Ch.15   |
| HBM3E       | L3   | HBM        | JEDEC          | JESD235D     | A      | Member | N           | jedec.org       | Ch.15   |
| NVMe 2.0    | L3   | Storage    | NVMe.org       | 2021         | A      | Free   | Y (logo)    | nvmexpress.org  | Ch.15   |
| CXL 3.1     | L3   | Coherent link| CXL Consortium| 2023        | A      | Member | N           | computeexpresslink.org| Ch.15|
| DisplayPort 2.1| L3 | Display   | VESA           | 2022         | A      | Member | Y (DP cert) | vesa.org        | Ch.15   |
| HDMI 2.1a   | L3   | Display    | HDMI Forum     | 2022         | A      | Member | Y (HDMI cert)| hdmi.org       | Ch.15   |
| IEEE 1149.1 (JTAG)| L4 | Debug  | IEEE SA       | 2013         | A      | Paid   | N           | IEEE Xplore     | Ch.15   |
| IEEE 1800 SV| L4   | HDL        | IEEE SA        | 2023         | A      | Paid   | N           | IEEE Xplore     | Ch.15   |
| RISC-V ISA  | L5   | Processor ISA| RISC-V Intl  | 20191213     | A      | Free   | N           | riscv.org       | Ch.6,15 |
| I2C         | L8   | Serial bus  | NXP (de-facto) | UM10204 Rev.7| A     | Free   | N           | nxp.com UM10204 | Ch.15  |
| SPI         | L8   | Serial bus  | De-facto       | No formal std| A      | —      | N           | Motorola orig.  | Ch.15   |
| MIPI CSI-2  | L3   | Camera IF  | MIPI Alliance  | v4.0         | A      | Member | N           | mipi.org        | Ch.4,15 |
| MIPI A-PHY  | L3   | Auto SerDes| MIPI Alliance  | v2.0         | A      | Member | N           | mipi.org        | Ch.4,15 |

**NETWORKING — PHYSICAL/LINK LAYER:**

| Short Name          | Tier | Category   | Governing Body | Version/Year | Status | Access | Certifiable | Key Doc   | Ch. Ref |
|---------------------|------|------------|----------------|--------------|--------|--------|-------------|-----------|---------|
| IEEE 802.3 (Ethernet)| L4  | Ethernet   | IEEE SA        | 802.3ck-2022| A      | Paid   | N           | IEEE 802.3| Ch.12   |
| IEEE 802.11 Wi-Fi 7 | L4  | Wi-Fi      | IEEE SA        | 802.11be-2024| A     | Paid   | Y (Wi-Fi cert)| IEEE 802.11| Ch.12|
| IEEE 802.1Q (VLAN)  | L4  | L2 switching| IEEE SA       | 2022         | A      | Paid   | N           | IEEE 802.1| Ch.12   |
| IEEE 802.1AS (PTP)  | L4  | Timing     | IEEE SA        | 2020         | A      | Paid   | N           | IEEE 802.1AS| Ch.12 |
| IEEE 802.1Qbv (TSN) | L4  | TSN        | IEEE SA        | 2015+        | A      | Paid   | N           | IEEE 802.1| Ch.12   |
| 5G NR (3GPP Rel.17) | L2/L3| 5G air  | 3GPP/ETSI      | Rel.17 (2022)| A     | Free   | Y (type appr)| 3GPP TS   | Ch.12  |
| Matter 1.x          | L3  | Smart home | CSA (formerly Zigbee Alliance) | 1.3 (2024)| A | Free | Y (Matter cert)| csa-iot.org| Ch.12|
| LoRaWAN 1.1         | L3  | LPWAN      | LoRa Alliance  | 1.1          | A      | Free   | N           | lora-alliance.org| Ch.12|
| PROFINET            | L3  | Industrial | PROFIBUS/PI    | v2.4         | A      | Free   | N           | profinet.com| Ch.12 |
| EtherCAT            | L3  | Industrial | ETG            | IEC 61158    | A      | Free   | N           | ethercat.org| Ch.12 |

**SAFETY-CRITICAL AND FUNCTIONAL SAFETY:**

| Short Name     | Tier | Category    | Governing Body | Version/Year | Status | Access | Certifiable | Key Doc     | Ch. Ref |
|----------------|------|-------------|----------------|--------------|--------|--------|-------------|-------------|---------|
| IEC 61508:2010 | L1   | Functional safety (root)| IEC TC65 | Ed.2 (2010)| A | Paid | Y (assessed)| IEC 61508 | Ch.17 |
| DO-178C        | L4   | Aviation SW | RTCA/EUROCAE   | 2011         | A      | Paid   | Y (type cert)| DO-178C  | Ch.5,17 |
| DO-254         | L4   | Aviation HW | RTCA/EUROCAE   | 2000         | A      | Paid   | Y (type cert)| DO-254   | Ch.5,17 |
| EN 50128       | L1   | Railway SW  | CENELEC        | 2011+A1:2020 | A      | Paid   | Y (assessed)| EN 50128  | Ch.17   |
| IEC 61511:2016 | L1   | Process safety| IEC TC65     | Ed.2 (2016)  | A      | Paid   | Y (assessed)| IEC 61511 | Ch.17   |
| IEC 62061:2021 | L1   | Machinery safety| IEC TC44  | 2021         | A      | Paid   | Y (assessed)| IEC 62061 | Ch.17   |
| ISO 13849:2015 | L1   | Machinery PL| ISO TC199      | 2015         | A      | Paid   | Y (assessed)| ISO 13849 | Ch.17   |
| IEC 62304:2006 | L1   | Medical SW  | IEC/ISO        | +AMD1:2015   | A      | Paid   | Y (510k/CE) | IEC 62304 | Ch.17   |

**GOVERNANCE AND MANAGEMENT:**

| Short Name     | Tier | Category    | Governing Body | Version/Year | Status | Access | Certifiable | Key Doc     | Ch. Ref |
|----------------|------|-------------|----------------|--------------|--------|--------|-------------|-------------|---------|
| ISO/IEC 27001  | L1   | ISMS        | ISO/IEC JTC1/SC27| 2022       | A      | Paid   | Y           | ISO 27001   | Ch.10,13|
| ISO/IEC 42001  | L1   | AI mgmt sys | ISO/IEC JTC1/SC42| 2023      | A      | Paid   | Y           | ISO 42001   | Ch.16   |
| NIST CSF 2.0   | L2   | Cyber framework| NIST        | 2024         | A      | Free   | N           | nist.gov    | Ch.10,13|
| NIST SP 800-53 | L2   | Controls catalog| NIST      | Rev.5 (2020) | A      | Free   | Y (FedRAMP) | SP 800-53   | Ch.10,13|
| ISO 9001:2015  | L1   | QMS         | ISO TC176      | 2015         | A      | Paid   | Y           | ISO 9001    | Ch.2    |
| SOC 2          | L4   | Trust audit | AICPA          | 2017         | A      | Paid   | Y (audit rpt)| AICPA TSC | Ch.10,13|
| PCI DSS 4.0    | L5   | Payment security| PCI SSC    | 4.0 (2022)   | A      | Free   | Y (QSA RoC) | pcisecuritystandards.org| Ch.10,13|

### SECTION 19.3 — MATRIX USAGE GUIDE

Provide guidance on:
- Filtering by tier to find international standards (L1, L2) vs. community standards (L5, L6)
- Filtering by "Certifiable=Y" for compliance projects
- Filtering by "Access=Free" for open-source project standard adoption
- Using chapter references to navigate to full entries
- How to identify deprecated standards to avoid in new system design

---

# CHAPTER 20: ENTRY TEMPLATE AND AUTHORING GUIDE

## CHAPTER SCOPE

This chapter is the **style guide and authoring reference** for the encyclopedia. It contains:
- The universal entry template with field-by-field authoring guidance
- Completed example entries for each tier (L1, L3, L5, L6, L8)
- Disambiguation writing guide
- Common authoring mistakes to avoid
- Quality checklist for each entry

---

## GENERATION INSTRUCTIONS FOR CHAPTER 20

Act as a world-class technical documentation architect and encyclopedia editor.
Generate the complete **Chapter 20: Entry Template and Authoring Guide**.

### SECTION 20.1 — THE UNIVERSAL ENTRY TEMPLATE

Show the full template with every field, its type, and authoring guidance:

```
## [SHORT NAME] — [FULL OFFICIAL NAME]

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | [exact official name, no abbreviations]           |
| Abbreviation/Short Name      | [commonly used abbreviation]                      |
| Entry Type                   | [Protocol / Specification / Standard / Framework / ...] |
| Governance Tier              | [L0–L8 per taxonomy]                             |
| Governing Body               | [exact body name]                                 |
| Working Group / Committee    | [WG, TC, SC name]                                |
| Document Number              | [ISO number, RFC number, FIPS number, etc.]      |
| Current Version              | [exact version string]                            |
| Publication Date             | [year or year-month]                              |
| Status                       | [Active / Deprecated / Emerging / Legacy]        |
| Predecessor / Supersedes     | [what this replaced]                              |
| Successor / Superseded by    | [what replaced this]                             |
| License / Access Model       | [Free / Paid / Member-only / Open source license] |
| Certification Possible       | [Yes / No / Audit Report / Conformance Testing]  |
| Certifying Entity            | [who grants the certificate, if applicable]      |
| Domain                       | [primary technology domain]                       |
| Cross-domain Applications    | [other domains that use this standard]            |
| Problem Solved               | [one-paragraph description of the problem]       |
| Abstract                     | [2-5 sentence technical summary]                 |
| Key Concepts                 | [numbered list of key concepts]                  |
| Wire Format / Protocol Detail| [technical encoding details, if applicable]     |
| Interoperability             | [what it connects/interoperates with]            |
| Implementation Guidance      | [how to implement or adopt]                      |
| Security Considerations      | [security properties and known weaknesses]       |
| Performance Characteristics  | [latency, throughput, overhead, where relevant] |
| Disambiguation               | [table of commonly confused near-synonyms]      |
| Where to Find the Spec       | [URL to normative specification]                 |
| Key References               | [3-5 canonical references — papers, RFC, spec]  |
```

### SECTION 20.2 — FIELD-BY-FIELD AUTHORING GUIDANCE

For each field in the template above, provide:
- What to write (and what NOT to write)
- Common mistakes
- Examples of good vs. bad entries

**Field: Entry Type**
- Use exactly one of: Protocol, Specification, Standard, Framework, Algorithm, Format, Schema, Methodology, Architecture, Profile, Language, API, License
- NOT: "document", "standard/specification", "thing"

**Field: Governance Tier**
- Must use L0–L8 classification from Chapter 1
- Must assign exactly one primary tier
- If borderline, use the tier of the primary governing body, not secondary adopters
- Example: Protobuf is L6 (Google-originated vendor-led open), not L3 even though it has community governance elements

**Field: Problem Solved**
- Write from the perspective of an engineer encountering the problem
- Describe what would happen WITHOUT this standard
- 1–2 paragraphs; must be precise and technical, not marketing language

**Field: Key Concepts**
- 5–10 numbered items
- Each item: bold concept name + 1-2 sentence explanation
- Must cover the concepts needed to understand any entry in the disambiguation table

**Field: Disambiguation**
- Required for every entry that has a commonly confused near-synonym
- Use the format: "[THIS] vs. [THAT]: [one precise sentence distinguishing them]"
- Minimum 2 disambiguation pairs per entry where applicable

### SECTION 20.3 — EXAMPLE ENTRIES BY TIER

Provide complete filled-in example entries:

**Example 1: L1 Standard — ISO/IEC 27001:2022**
(Fill in every field of the Universal Entry Template for ISO 27001, demonstrating perfect L1 tier entry)

**Example 2: L3 Standard — TLS 1.3 (RFC 8446)**
(Fill in every field for TLS 1.3, demonstrating perfect L3 IETF RFC entry)

**Example 3: L5 Community Standard — Apache Parquet**
(Fill in every field for Apache Parquet, demonstrating L5 entry)

**Example 4: L6 Vendor-Led Open Standard — Protocol Buffers (Protobuf)**
(Fill in every field for Protobuf, demonstrating L6 entry)

**Example 5: L8 De-facto Standard — REST**
(Fill in every field for REST as a de-facto standard, demonstrating L8 entry — note that REST itself has no normative spec, only Fielding's dissertation and the informal Richardson Maturity Model)

### SECTION 20.4 — DISAMBIGUATION WRITING GUIDE

Provide rules for writing disambiguation sections:

**Rule 1: Distinguish by governance, not just function**
Bad: "Avro vs Protobuf: both are serialization formats"
Good: "Avro vs Protobuf: Avro embeds schema in every message and is governed by Apache (L5); Protobuf requires out-of-band .proto file distribution and is governed by Google (L6)"

**Rule 2: Include version disambiguation when breaking changes exist**
Example: "OAuth 2.0 vs OAuth 2.1: OAuth 2.1 is a consolidation draft that removes implicit flow and ROPC, mandates PKCE for all public clients, and was not yet published as an RFC as of 2024"

**Rule 3: Distinguish specification from implementation**
Example: "OpenAPI Specification (OAS) vs Swagger: Swagger was the original v1/v2 specification (SmartBear); in 2016 the spec was donated to the OpenAPI Initiative and renamed OpenAPI Specification 3.0+; Swagger now refers to SmartBear's tooling products, not the specification"

**Rule 4: Distinguish acronym collisions**
Example: "OPC (Ole for Process Control, legacy COM-based) vs OPC UA (OPC Unified Architecture, IEC 62541): OPC was deprecated in the 2000s; OPC UA is its platform-independent successor governed by the OPC Foundation"

### SECTION 20.5 — COMMON AUTHORING MISTAKES

List 20+ common mistakes with corrections:

1. **Confusing standard with implementation**: "Docker is an OCI standard" — WRONG. Docker is an OCI-compliant implementation; OCI defines the standards.
2. **Wrong governance tier**: Calling gRPC "L3 IETF standard" — WRONG. gRPC is L6 (Google vendor-led open, hosted under CNCF as L5 incubation).
3. **Outdated version references**: Citing TLS 1.2 as current when TLS 1.3 is the recommended version.
4. **Conflating deprecation and retirement**: TLS 1.0/1.1 are deprecated (RFC 8996 says "MUST NOT use") but implementations exist; SHA-1 is deprecated for digital signatures but not for all uses.
5. **Missing disambiguation for near-synonyms**: Entry for OIDC without distinguishing from OAuth 2.0.
6. **Incorrect SIL/ASIL notation**: Writing "SIL-3" (incorrect) vs. "SIL 3" (correct per IEC 61508).
7. **Confusing normative and informative**: RFC "MUST" requirements are normative; RFC "NOTE" sections are informative.
8. **Calling de-facto standards "proprietary"**: JSON is not proprietary even though it originated from JavaScript; it is IETF-standardized (RFC 8259).

### SECTION 20.6 — ENCYCLOPEDIA QUALITY CHECKLIST

For each entry before publication:

- [ ] Full Name is the exact official name (not a common abbreviation)
- [ ] Tier assignment is justified with a one-sentence rationale
- [ ] Governing body name is exact and current (check for mergers, renames)
- [ ] Document number is exact (ISO number, RFC number, etc.)
- [ ] Current version is verified (not a draft version treated as final)
- [ ] Status correctly reflects Active/Deprecated/Emerging/Legacy
- [ ] Predecessor/Successor fields completed for all non-root standards
- [ ] Problem Solved section is written from engineer perspective, no marketing
- [ ] All key concepts defined before use in technical sections
- [ ] Wire format / encoding details verified against normative specification
- [ ] Disambiguation table covers all top-3 commonly confused near-synonyms
- [ ] Security considerations included for any cryptographic or network protocol
- [ ] License/access model is current (check for relicensing — HashiCorp BSL case)
- [ ] Spec URL is active and points to normative document, not tutorial
- [ ] References include primary source (original paper, RFC, spec) not Wikipedia

---

## OUTPUT FORMAT REQUIREMENTS

- Entry template must be displayed as a filled-in Markdown code block
- Example entries must be complete (no "[fill in]" placeholders — actually fill in)
- Field guidance must use before/after examples for each field
- Disambiguation rules must include bad vs. good examples
- Quality checklist must be in GitHub-flavored Markdown checkbox format [ ]
- Minimum length: Chapter 19 = 4,000 words (matrix data); Chapter 20 = 3,500 words
