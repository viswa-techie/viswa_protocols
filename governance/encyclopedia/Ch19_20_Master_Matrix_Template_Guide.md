# Chapters 19 & 20 — Master Classification Matrix & Entry Template Guide

## Universal Technology Standards Governance & Regulatory Architecture Encyclopedia
### First Edition, 2024–2026

---

# CHAPTER 19: MASTER CLASSIFICATION MATRIX

## 19.1 How to Use This Matrix

### Column Definitions

| Column        | Description                                         |
|---------------|-----------------------------------------------------|
| Short Name    | Common abbreviation (TLS, AES, OpenAPI, etc.)      |
| Tier          | L0–L8 per governance taxonomy (Chapter 1)          |
| Category      | Primary technology domain                           |
| Governing Body| Organization responsible for the specification     |
| Version       | Most recent published version or publication year  |
| Status        | A=Active, D=Deprecated, E=Emerging, L=Legacy       |
| Access        | Free / Paid / Member                               |
| Cert?         | Y=Certifiable, N=No, A=Audit report               |
| Key Doc       | Primary normative document reference               |
| Ch. Ref       | Chapter in this encyclopedia with full entry       |

### Status Codes

| Code | Meaning    | Guidance                                               |
|------|------------|--------------------------------------------------------|
| A    | Active     | Current recommended version; safe for new designs      |
| D    | Deprecated | Do NOT use in new systems; active removal in progress  |
| E    | Emerging   | Draft/early adoption; not yet stable for production    |
| L    | Legacy     | Superseded but still widely deployed; plan migration   |

### Tier Quick Reference

| Tier | Authority Level           | Examples                              |
|------|---------------------------|---------------------------------------|
| L0   | Mathematical/Physical law | Shannon, Nyquist, thermodynamics      |
| L1   | International treaty/ISO  | ISO 26262, IEC 61508, ISO 27001      |
| L2   | Government/Regulatory     | NIST FIPS, FCC rules, EU AI Act      |
| L3   | Multi-stakeholder consensus| IETF RFCs, W3C, PCI-SIG, USB-IF     |
| L4   | Professional association  | IEEE SA, SAE, RTCA                    |
| L5   | Foundation/Community      | Apache, CNCF, RISC-V International   |
| L6   | Vendor-led open           | Google Protobuf, Meta PyTorch         |
| L7   | Proprietary               | Apple Metal, NVIDIA CUDA, ARM ISA     |
| L8   | De-facto (no formal body) | REST, JSON, SemVer, Git protocol      |

---

## 19.2 Master Standards Matrix

### CRYPTOGRAPHIC STANDARDS

| Short Name         | Tier | Category   | Governing Body | Version       | Status | Access | Cert? | Key Doc       | Ch. |
|--------------------|------|------------|----------------|---------------|--------|--------|-------|---------------|-----|
| AES                | L2   | Symmetric  | NIST           | FIPS 197      | A      | Free   | N     | FIPS 197      | 13  |
| ChaCha20-Poly1305  | L3   | AEAD       | IETF           | RFC 8439      | A      | Free   | N     | RFC 8439      | 13  |
| 3DES               | L2   | Symmetric  | NIST           | FIPS 46-3     | D      | Free   | N     | FIPS 46-3     | 13  |
| SHA-256/384/512    | L2   | Hash       | NIST           | FIPS 180-4    | A      | Free   | N     | FIPS 180-4    | 13  |
| SHA-3              | L2   | Hash       | NIST           | FIPS 202      | A      | Free   | N     | FIPS 202      | 13  |
| BLAKE3             | L8   | Hash       | Community      | —             | A      | Free   | N     | blake3.io     | 13  |
| RSA                | L2   | Asymmetric | NIST/IETF      | FIPS 186-5    | L      | Free   | N     | FIPS 186-5    | 13  |
| ECDSA              | L2   | Asymmetric | NIST           | FIPS 186-5    | A      | Free   | N     | FIPS 186-5    | 13  |
| EdDSA (Ed25519)    | L3   | Asymmetric | IETF/NIST      | RFC 8032      | A      | Free   | N     | RFC 8032      | 13  |
| X25519             | L3   | Key Exch.  | IETF           | RFC 7748      | A      | Free   | N     | RFC 7748      | 13  |
| ML-KEM (Kyber)     | L2   | PQC KEM    | NIST           | FIPS 203      | A      | Free   | N     | FIPS 203      | 13  |
| ML-DSA (Dilithium) | L2   | PQC Sig    | NIST           | FIPS 204      | A      | Free   | N     | FIPS 204      | 13  |
| SLH-DSA (SPHINCS+) | L2   | PQC Sig    | NIST           | FIPS 205      | A      | Free   | N     | FIPS 205      | 13  |
| Argon2             | L3   | KDF        | IETF           | RFC 9106      | A      | Free   | N     | RFC 9106      | 13  |

### PROTOCOL SECURITY

| Short Name   | Tier | Category      | Governing Body | Version    | Status | Access | Cert? | Key Doc    | Ch. |
|--------------|------|---------------|----------------|------------|--------|--------|-------|------------|-----|
| TLS 1.3      | L3   | Channel Sec.  | IETF           | RFC 8446   | A      | Free   | N     | RFC 8446   | 13  |
| TLS 1.2      | L3   | Channel Sec.  | IETF           | RFC 5246   | L      | Free   | N     | RFC 5246   | 13  |
| DTLS 1.3     | L3   | Datagram Sec. | IETF           | RFC 9147   | A      | Free   | N     | RFC 9147   | 13  |
| X.509/PKIX   | L1/L3| PKI          | ITU-T/IETF     | RFC 5280   | A      | Free   | N     | RFC 5280   | 13  |
| IPsec/IKEv2  | L3   | Network VPN   | IETF           | RFC 7296   | A      | Free   | N     | RFC 7296   | 13  |
| WireGuard    | L8   | VPN           | De-facto/IETF  | RFC 9107   | A      | Free   | N     | RFC 9107   | 13  |
| SSH v2       | L3   | Remote Access | IETF           | RFC 4253   | A      | Free   | N     | RFC 4253   | 13  |
| MACsec       | L4   | L2 Encryption | IEEE SA        | 802.1AE    | A      | Paid   | N     | IEEE 802.1AE| 13 |
| DNSSEC       | L3   | DNS Integrity | IETF           | RFC 4033   | A      | Free   | N     | RFC 4033   | 13  |

### IDENTITY & ACCESS MANAGEMENT

| Short Name    | Tier | Category    | Governing Body    | Version     | Status | Access | Cert? | Key Doc      | Ch. |
|---------------|------|-------------|-------------------|-------------|--------|--------|-------|--------------|-----|
| OAuth 2.0     | L3   | AuthZ       | IETF              | RFC 6749    | A      | Free   | N     | RFC 6749     | 13  |
| OAuth 2.1     | L3   | AuthZ       | IETF              | Draft 2024  | E      | Free   | N     | IETF draft   | 13  |
| OIDC 1.0      | L3   | AuthN       | OpenID Foundation | 2014        | A      | Free   | N     | openid.net   | 13  |
| SAML 2.0      | L3   | Fed. SSO    | OASIS             | 2005        | A      | Free   | N     | OASIS spec   | 13  |
| FIDO2/WebAuthn| L3   | Passwordless| FIDO Alliance/W3C | Level 3     | A      | Free   | Y     | fido specs   | 13  |
| SCIM 2.0      | L3   | Provisioning| IETF              | RFC 7643    | A      | Free   | N     | RFC 7643     | 13  |
| SPIFFE        | L5   | Workload ID | CNCF              | v1.0        | A      | Free   | N     | spiffe.io    | 13  |
| Kerberos V5   | L3   | AuthN       | IETF              | RFC 4120    | A      | Free   | N     | RFC 4120     | 13  |

### NETWORKING PROTOCOLS

| Short Name    | Tier | Category   | Governing Body | Version    | Status | Access | Cert? | Key Doc    | Ch. |
|---------------|------|------------|----------------|------------|--------|--------|-------|------------|-----|
| IPv4          | L3   | Network    | IETF           | RFC 791    | L      | Free   | N     | RFC 791    | 12  |
| IPv6          | L3   | Network    | IETF           | RFC 8200   | A      | Free   | N     | RFC 8200   | 12  |
| TCP           | L3   | Transport  | IETF           | RFC 9293   | A      | Free   | N     | RFC 9293   | 12  |
| UDP           | L3   | Transport  | IETF           | RFC 768    | A      | Free   | N     | RFC 768    | 12  |
| QUIC          | L3   | Transport  | IETF           | RFC 9000   | A      | Free   | N     | RFC 9000   | 12  |
| HTTP/2        | L3   | Application| IETF           | RFC 9113   | A      | Free   | N     | RFC 9113   | 14  |
| HTTP/3        | L3   | Application| IETF           | RFC 9114   | A      | Free   | N     | RFC 9114   | 14  |
| DNS           | L3   | Naming     | IETF           | RFC 1034   | A      | Free   | N     | RFC 1034   | 12  |
| BGP-4         | L3   | Routing    | IETF           | RFC 4271   | A      | Free   | N     | RFC 4271   | 12  |
| OSPF v3       | L3   | Routing    | IETF           | RFC 5340   | A      | Free   | N     | RFC 5340   | 12  |
| MQTT 5.0      | L3   | IoT Msg    | OASIS          | 5.0        | A      | Free   | N     | OASIS spec | 12  |
| CoAP          | L3   | IoT        | IETF           | RFC 7252   | A      | Free   | N     | RFC 7252   | 12  |
| WebSocket     | L3   | Realtime   | IETF           | RFC 6455   | A      | Free   | N     | RFC 6455   | 14  |

### PHYSICAL LAYER NETWORKING

| Short Name       | Tier | Category   | Governing Body | Version     | Status | Access | Cert? | Key Doc     | Ch. |
|------------------|------|------------|----------------|-------------|--------|--------|-------|-------------|-----|
| IEEE 802.3       | L4   | Ethernet   | IEEE SA        | 802.3ck     | A      | Paid   | N     | IEEE 802.3  | 12  |
| IEEE 802.11be    | L4   | Wi-Fi 7    | IEEE SA        | 2024        | A      | Paid   | Y     | IEEE 802.11 | 12  |
| 5G NR (Rel.17)   | L3   | Cellular   | 3GPP/ETSI      | Rel.17      | A      | Free   | Y     | 3GPP TS 38  | 12  |
| Bluetooth 5.4    | L3   | Short-range| Bluetooth SIG  | 5.4         | A      | Free   | Y     | bluetooth.com| 12 |
| Matter 1.3       | L3   | Smart Home | CSA            | 1.3         | A      | Free   | Y     | csa-iot.org | 12  |
| LoRaWAN 1.1      | L3   | LPWAN      | LoRa Alliance  | 1.1         | A      | Free   | N     | lora-alliance| 12 |
| IEEE 802.1Qbv    | L4   | TSN        | IEEE SA        | 2015+       | A      | Paid   | N     | IEEE 802.1  | 12  |

### WEB STANDARDS

| Short Name    | Tier | Category    | Governing Body | Version       | Status | Access | Cert? | Key Doc        | Ch. |
|---------------|------|-------------|----------------|---------------|--------|--------|-------|----------------|-----|
| HTML          | L3   | Markup      | WHATWG         | Living Std    | A      | Free   | N     | html.spec.whatwg.org | 3 |
| CSS           | L3   | Styling     | W3C            | Modules (L3+) | A     | Free   | N     | w3.org/Style/CSS | 3 |
| ECMAScript    | L3   | Language    | ECMA TC39      | ES2024        | A      | Free   | N     | ECMA-262       | 3   |
| WebAssembly   | L3   | Runtime     | W3C            | 2.0 draft     | A      | Free   | N     | webassembly.org| 3   |
| DOM           | L3   | Web API     | WHATWG         | Living Std    | A      | Free   | N     | dom.spec.whatwg.org | 3 |
| WebGPU        | L3   | Graphics API| W3C            | 2024          | A      | Free   | N     | w3.org         | 3   |

### API & DATA EXCHANGE

| Short Name     | Tier | Category      | Governing Body | Version   | Status | Access | Cert? | Key Doc          | Ch. |
|----------------|------|---------------|----------------|-----------|--------|--------|-------|------------------|-----|
| OpenAPI 3.1    | L3   | API Spec      | OAI (LF)      | 3.1.0     | A      | Free   | N     | spec.openapis.org| 14  |
| AsyncAPI 3.0   | L5   | Event API     | AsyncAPI (LF)  | 3.0       | A      | Free   | N     | asyncapi.com     | 14  |
| GraphQL        | L5   | Query Lang    | GraphQL Fdn    | 2021      | A      | Free   | N     | graphql.org      | 7   |
| gRPC           | L5   | RPC           | CNCF           | grpc.io   | A      | Free   | N     | grpc.io          | 7   |
| JSON           | L3   | Data Format   | IETF           | RFC 8259  | A      | Free   | N     | RFC 8259         | 3   |
| JSON Schema    | L8   | Validation    | json-schema.org| 2020-12   | A      | Free   | N     | json-schema.org  | 16  |
| Protobuf       | L6   | IDL + Binary  | Google         | proto3    | A      | Free   | N     | protobuf.dev     | 7   |
| Apache Avro    | L5   | Schema+Binary | Apache ASF     | 1.12      | A      | Free   | N     | avro.apache.org  | 16  |
| Apache Parquet | L5   | Columnar Store| Apache ASF     | 2.10      | A      | Free   | N     | parquet.apache.org| 16 |
| Apache Arrow   | L5   | In-memory Col | Apache ASF     | 15.x      | A      | Free   | N     | arrow.apache.org | 16  |
| CBOR           | L3   | Binary JSON   | IETF           | RFC 8949  | A      | Free   | N     | RFC 8949         | 16  |
| MessagePack    | L8   | Binary JSON   | Community      | 2013      | A      | Free   | N     | msgpack.org      | 16  |
| FlatBuffers    | L6   | Zero-copy     | Google         | 24.x      | A      | Free   | N     | flatbuffers.dev  | 7   |
| ONNX           | L5   | ML Model      | LF AI & Data   | opset 21  | A      | Free   | N     | onnx.ai          | 16  |
| YAML 1.2       | L8   | Config Format | yaml.org       | 1.2.2     | A      | Free   | N     | yaml.org         | 8-9 |
| TOML           | L8   | Config Format | Community      | 1.0       | A      | Free   | N     | toml.io          | 8-9 |

### CLOUD & CONTAINER

| Short Name       | Tier | Category      | Governing Body | Version  | Status | Access | Cert? | Key Doc           | Ch. |
|------------------|------|---------------|----------------|----------|--------|--------|-------|-------------------|-----|
| OCI Image        | L5   | Container     | OCI (LF)       | v1.1     | A      | Free   | N     | opencontainers.org| 14  |
| OCI Runtime      | L5   | Container     | OCI (LF)       | v1.2     | A      | Free   | N     | opencontainers.org| 14  |
| OCI Distribution | L5   | Registry      | OCI (LF)       | v1.1     | A      | Free   | N     | opencontainers.org| 14  |
| Kubernetes API   | L5   | Orchestration | CNCF           | v1.30    | A      | Free   | N     | kubernetes.io     | 14  |
| OpenTelemetry    | L5   | Observability | CNCF           | OTLP 1.x| A      | Free   | N     | opentelemetry.io  | 14  |
| Prometheus       | L5   | Metrics       | CNCF           | v2.x     | A      | Free   | N     | prometheus.io     | 14  |
| CloudEvents      | L5   | Event Format  | CNCF           | 1.0.2    | A      | Free   | N     | cloudevents.io    | 14  |
| Envoy xDS        | L5   | Service Mesh  | CNCF           | v3       | A      | Free   | N     | envoyproxy.io     | 14  |
| Helm             | L5   | Package Mgmt  | CNCF           | v3       | A      | Free   | N     | helm.sh           | 14  |

### AUTOMOTIVE & EMBEDDED

| Short Name       | Tier | Category       | Governing Body | Version   | Status | Access | Cert? | Key Doc        | Ch. |
|------------------|------|----------------|----------------|-----------|--------|--------|-------|----------------|-----|
| CAN FD           | L1   | Vehicle Bus    | ISO TC22       | ISO 11898 | A      | Paid   | N     | ISO 11898-1    | 11  |
| LIN 2.2A         | L1   | Vehicle Bus    | ISO TC22       | ISO 17987 | A      | Paid   | N     | ISO 17987      | 11  |
| FlexRay          | L1   | Vehicle Bus    | ISO TC22       | ISO 17458 | L      | Paid   | N     | ISO 17458      | 11  |
| Auto Ethernet    | L4   | Vehicle Net    | IEEE/OPEN Alln | 100BASE-T1| A     | Paid   | N     | IEEE 802.3cg   | 11  |
| SOME/IP          | L3   | Middleware     | AUTOSAR        | R23-11    | A      | Member | N     | AUTOSAR spec   | 11  |
| AUTOSAR Classic  | L3   | ECU Platform   | AUTOSAR        | R22-11    | A      | Member | N     | autosar.org    | 4   |
| AUTOSAR Adaptive | L3   | HPC Platform   | AUTOSAR        | R23-11    | A      | Member | N     | autosar.org    | 4   |
| ISO 26262        | L1   | Func. Safety   | ISO TC22       | 2018      | A      | Paid   | Y     | ISO 26262      | 11,17|
| ISO 21448 (SOTIF)| L1   | ADAS Safety    | ISO TC22       | 2022      | A      | Paid   | N     | ISO 21448      | 11  |
| ISO/SAE 21434    | L1   | Auto Cyber     | ISO/SAE        | 2021      | A      | Paid   | Y     | ISO 21434      | 11  |
| UDS              | L1   | Diagnostics    | ISO TC22       | ISO 14229 | A      | Paid   | N     | ISO 14229      | 11  |
| MISRA C:2023     | L5   | Safe C         | MISRA          | 2023      | A      | Paid   | N     | misra.org.uk   | 11  |
| UNECE R155       | L2   | Cyber Reg      | UNECE WP.29    | 2021      | A      | Free   | Y     | UN ECE R155    | 10,11|

### HARDWARE & SEMICONDUCTOR

| Short Name      | Tier | Category    | Governing Body | Version    | Status | Access | Cert? | Key Doc          | Ch. |
|-----------------|------|-------------|----------------|------------|--------|--------|-------|------------------|-----|
| PCIe 6.0        | L3   | I/O Bus     | PCI-SIG        | 6.0        | A      | Member | N     | pcisig.com       | 15  |
| PCIe 7.0        | L3   | I/O Bus     | PCI-SIG        | 7.0 (2025) | E     | Member | N     | pcisig.com       | 15  |
| USB4 v2         | L3   | I/O Bus     | USB-IF         | v2 (2022)  | A      | Free   | Y     | usb.org          | 15  |
| DDR5            | L3   | Memory      | JEDEC          | JESD79-5B  | A      | Member | N     | jedec.org        | 15  |
| LPDDR5X         | L3   | Mobile Mem  | JEDEC          | JESD209-5C | A      | Member | N     | jedec.org        | 15  |
| HBM3E           | L3   | AI Memory   | JEDEC          | JESD235D   | A      | Member | N     | jedec.org        | 15  |
| GDDR7           | L3   | GPU Memory  | JEDEC          | 2024       | A      | Member | N     | jedec.org        | 15  |
| NVMe 2.0        | L3   | Storage     | NVMe.org       | 2.0        | A      | Free   | Y     | nvmexpress.org   | 15  |
| CXL 3.1         | L3   | Coherent    | CXL Consortium | 3.1        | A      | Member | N     | cxl.org          | 15  |
| DisplayPort 2.1 | L3   | Display     | VESA           | 2.1        | A      | Member | Y     | vesa.org         | 15  |
| HDMI 2.1a       | L3   | Display     | HDMI Forum     | 2.1a       | A      | Member | Y     | hdmi.org         | 15  |
| RISC-V ISA      | L5   | Processor   | RISC-V Intl    | Ratified   | A      | Free   | N     | riscv.org        | 6,15|
| ARMv9-A         | L7   | Processor   | ARM Holdings   | v9         | A      | Licensed| N    | arm.com          | 8-9 |
| I2C             | L8   | Serial Bus  | NXP (de-facto) | UM10204    | A      | Free   | N     | nxp.com          | 15  |
| SPI             | L8   | Serial Bus  | De-facto       | —          | A      | —      | N     | No formal spec   | 15  |
| JTAG            | L4   | Debug       | IEEE SA        | 1149.1     | A      | Paid   | N     | IEEE 1149.1      | 15  |
| SystemVerilog   | L4   | HDL         | IEEE SA        | IEEE 1800  | A      | Paid   | N     | IEEE 1800-2023   | 15  |

### SAFETY-CRITICAL & FUNCTIONAL SAFETY

| Short Name     | Tier | Category      | Governing Body | Version    | Status | Access | Cert? | Key Doc      | Ch. |
|----------------|------|---------------|----------------|------------|--------|--------|-------|--------------|-----|
| IEC 61508      | L1   | Root FS       | IEC TC65       | Ed.2 (2010)| A      | Paid   | Y     | IEC 61508    | 17  |
| ISO 26262      | L1   | Auto FS       | ISO TC22       | Ed.2 (2018)| A      | Paid   | Y     | ISO 26262    | 17  |
| DO-178C        | L4   | Aviation SW   | RTCA/EUROCAE   | 2011       | A      | Paid   | Y     | DO-178C      | 17  |
| DO-254         | L4   | Aviation HW   | RTCA/EUROCAE   | 2000       | A      | Paid   | Y     | DO-254       | 17  |
| EN 50128       | L1   | Railway SW    | CENELEC        | 2011+A1    | A      | Paid   | Y     | EN 50128     | 17  |
| IEC 61511      | L1   | Process Safety| IEC TC65       | Ed.2 (2016)| A      | Paid   | Y     | IEC 61511    | 17  |
| IEC 62061      | L1   | Machinery     | IEC TC44       | 2021       | A      | Paid   | Y     | IEC 62061    | 17  |
| ISO 13849-1    | L1   | Machinery PL  | ISO TC199      | 2023       | A      | Paid   | Y     | ISO 13849-1  | 17  |
| IEC 62304      | L1   | Medical SW    | IEC/ISO        | 2006+A1   | A      | Paid   | Y     | IEC 62304    | 17  |

### AI/ML STANDARDS

| Short Name     | Tier | Category   | Governing Body  | Version   | Status | Access | Cert? | Key Doc       | Ch. |
|----------------|------|------------|-----------------|-----------|--------|--------|-------|---------------|-----|
| ISO/IEC 42001  | L1   | AI Mgmt    | ISO/IEC SC42    | 2023      | A      | Paid   | Y     | ISO 42001     | 16  |
| NIST AI RMF    | L2   | AI Risk    | NIST            | AI 100-1  | A      | Free   | N     | nist.gov      | 16  |
| ONNX           | L5   | Model Exch | LF AI & Data    | opset 21  | A      | Free   | N     | onnx.ai       | 16  |
| PMML 4.4.1     | L3   | Model Exch | DMG             | 4.4.1     | A      | Free   | N     | dmg.org       | 16  |
| Safetensors    | L6   | Model Store| Hugging Face    | 2023      | A      | Free   | N     | huggingface.co| 16  |
| GGUF           | L8   | LLM Format | llama.cpp       | 2023      | A      | Free   | N     | github.com    | 16  |
| MLPerf         | L3   | Benchmark  | MLCommons       | v4.x      | A      | Free   | N     | mlcommons.org | 16  |
| OpenLineage    | L5   | Data Lineage| LF AI & Data   | 1.x       | A      | Free   | N     | openlineage.io| 16  |

### GOVERNANCE & COMPLIANCE

| Short Name    | Tier | Category    | Governing Body | Version    | Status | Access | Cert? | Key Doc       | Ch. |
|---------------|------|-------------|----------------|------------|--------|--------|-------|---------------|-----|
| ISO/IEC 27001 | L1   | ISMS        | ISO/IEC SC27   | 2022       | A      | Paid   | Y     | ISO 27001     | 13  |
| NIST CSF 2.0  | L2   | Cyber Fwk   | NIST           | 2.0 (2024) | A     | Free   | N     | nist.gov      | 10  |
| NIST SP 800-53| L2   | Controls    | NIST           | Rev.5      | A      | Free   | Y     | SP 800-53     | 10  |
| SOC 2         | L4   | Trust Audit | AICPA          | 2017 TSC   | A      | Paid   | A     | AICPA TSC     | 10  |
| PCI DSS 4.0   | L5   | Payment Sec | PCI SSC        | 4.0        | A      | Free   | Y     | pcissc.org    | 10  |
| ISO 9001      | L1   | QMS         | ISO TC176      | 2015       | A      | Paid   | Y     | ISO 9001      | 2   |
| CVSS v4.0     | L3   | Vuln Scoring| FIRST          | 4.0        | A      | Free   | N     | first.org     | 13  |
| SPDX          | L5   | SBOM        | Linux Fdn      | 2.3        | A      | Free   | N     | spdx.dev      | 13  |
| CycloneDX     | L5   | SBOM        | OWASP          | 1.6        | A      | Free   | N     | cyclonedx.org | 13  |
| SLSA          | L5   | Supply Chain| OpenSSF        | 1.0        | A      | Free   | N     | slsa.dev      | 13  |

---

## 19.3 Matrix Usage Guide

**For Compliance Projects:** Filter by `Cert? = Y` to identify standards that support third-party certification.

**For Open-Source Projects:** Filter by `Access = Free` to find standards that are freely implementable without licensing fees.

**For New System Architecture:** Filter by `Status = A` and use tier to prioritize (prefer L1-L3 over L6-L8 for long-term stability).

**For Migration Planning:** Filter by `Status = D` or `Status = L` to identify standards requiring replacement.

---

# CHAPTER 20: ENTRY TEMPLATE AND AUTHORING GUIDE

## 20.1 The Universal Entry Template

```markdown
## [SHORT NAME] — [FULL OFFICIAL NAME]

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | [exact official name, no abbreviations]           |
| Abbreviation/Short Name      | [commonly used abbreviation]                      |
| Entry Type                   | [Protocol / Specification / Standard / Framework / Algorithm / Format / Schema / API / Language] |
| Governance Tier              | [L0–L8 per taxonomy]                             |
| Governing Body               | [exact body name]                                 |
| Working Group / Committee    | [WG, TC, SC name]                                |
| Document Number              | [ISO number, RFC number, FIPS number, etc.]      |
| Current Version              | [exact version string]                            |
| Publication Date             | [year or year-month]                              |
| Status                       | [Active / Deprecated / Emerging / Legacy]        |
| Predecessor / Supersedes     | [what this replaced]                              |
| Successor / Superseded by    | [what replaced this, if applicable]              |
| License / Access Model       | [Free / Paid / Member-only / Open source license]|
| Certification Possible       | [Yes / No / Audit Report / Conformance Testing]  |
| Domain                       | [primary technology domain]                       |
| Problem Solved               | [1-2 paragraphs: engineer perspective]           |
| Abstract                     | [2-5 sentence technical summary]                 |
| Key Concepts                 | [5-10 numbered items]                            |
| Security Considerations      | [known weaknesses and mitigations]               |
| Disambiguation               | [commonly confused near-synonyms table]          |
| Where to Find the Spec       | [URL to normative specification]                 |
```

---

## 20.2 Field Authoring Guidance

### Entry Type
- Use exactly ONE of: Protocol, Specification, Standard, Framework, Algorithm, Format, Schema, Methodology, Architecture, Profile, Language, API, License
- WRONG: "document", "standard/specification", "thing"
- RIGHT: "Protocol" (for TLS), "Standard" (for ISO 27001), "Format" (for Parquet)

### Governance Tier
- Assign based on the PRIMARY governing body, not secondary adopters
- WRONG: "Protobuf is L3" (it has community governance elements)
- RIGHT: "Protobuf is L6" (Google-originated vendor-led open source)
- WRONG: "gRPC is L3 IETF standard"
- RIGHT: "gRPC is L5 CNCF incubation / L6 Google-originated"

### Problem Solved
- Write from the perspective of an engineer encountering the problem
- Describe what would happen WITHOUT this standard
- WRONG: "TLS provides industry-leading security for enterprises" (marketing)
- RIGHT: "Without TLS, any network observer (ISP, coffee shop router, government) can read HTTP request/response content in cleartext, inject/modify data in transit, and impersonate servers to clients."

### Disambiguation
- Required for every entry with commonly confused near-synonyms
- Format: "[THIS] vs. [THAT]: [one precise sentence distinguishing them]"

---

## 20.3 Example Entries

### Example: L3 Standard — TLS 1.3

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Transport Layer Security version 1.3                           |
| Abbreviation                 | TLS 1.3                                                       |
| Entry Type                   | Protocol                                                      |
| Governance Tier              | L3 (IETF multi-stakeholder consensus)                         |
| Governing Body               | Internet Engineering Task Force (IETF)                        |
| Working Group                | TLS Working Group                                             |
| Document Number              | RFC 8446                                                      |
| Current Version              | 1.3                                                           |
| Publication Date             | August 2018                                                   |
| Status                       | Active                                                        |
| Predecessor                  | TLS 1.2 (RFC 5246, 2008)                                     |
| Successor                    | None (current)                                                |
| License / Access             | Free — open access at rfc-editor.org                          |
| Certification Possible       | No (protocol spec; products can be FIPS 140-3 validated)     |
| Domain                       | Protocol Security / Channel Encryption                        |
| Problem Solved               | Without TLS, all application-layer communication (HTTP, SMTP, etc.) is transmitted in cleartext, vulnerable to eavesdropping, injection, and server impersonation by any network intermediary. |
| Abstract                     | TLS 1.3 provides authenticated, encrypted, integrity-protected channels between two communicating applications over TCP. It achieves a 1-RTT handshake (vs. 2-RTT in TLS 1.2), mandatory forward secrecy via ephemeral ECDHE, and removes all legacy cipher suites that lacked AEAD. |
| Key Concepts                 | 1. **AEAD** — Authenticated Encryption with Associated Data (only mode permitted). 2. **Forward secrecy** — session keys cannot be derived from long-term keys. 3. **0-RTT** — optional early data for resumed connections (replay risk). 4. **Handshake encryption** — ServerHello onward is encrypted. 5. **KeyUpdate** — replaces renegotiation. |
| Security Considerations      | 0-RTT data is replayable; PSK mode without ECDHE lacks forward secrecy; implementations must validate certificates. |
| Disambiguation               | TLS vs. SSL: SSL is obsolete (Netscape 1990s); "SSL certificate" is a misnomer. TLS vs. HTTPS: HTTPS = HTTP + TLS; they are not separate protocols. TLS vs. DTLS: DTLS is TLS adapted for UDP/datagram transport. |
| Where to Find the Spec       | https://www.rfc-editor.org/rfc/rfc8446 |

### Example: L8 De-facto Standard — REST

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Representational State Transfer                                |
| Abbreviation                 | REST                                                          |
| Entry Type                   | Architecture (architectural style, not a protocol)            |
| Governance Tier              | L8 (de-facto — no formal specification body)                  |
| Governing Body               | None (originated in Roy Fielding's 2000 PhD dissertation)    |
| Working Group                | None                                                          |
| Document Number              | None (Fielding, Chapter 5, "Architectural Styles and the Design of Network-based Software Architectures") |
| Current Version              | N/A (no versioned specification exists)                       |
| Publication Date             | 2000                                                          |
| Status                       | Active (de-facto dominant API architectural style)            |
| Predecessor                  | RPC, CORBA, SOAP/WS-*                                        |
| Successor                    | None; complemented by GraphQL and gRPC for specific use cases|
| License / Access             | Free (academic dissertation)                                  |
| Certification Possible       | No                                                            |
| Domain                       | API Architecture                                              |
| Problem Solved               | Before REST, web services used complex middleware (CORBA, SOAP, WS-*) requiring code generation, WSDL parsing, and tight coupling. REST leverages existing HTTP semantics (verbs, status codes, URIs) to create stateless, cacheable, uniform interfaces without additional middleware. |
| Abstract                     | REST is an architectural style for distributed hypermedia systems. It defines six constraints: client-server, stateless, cacheable, uniform interface, layered system, and (optional) code-on-demand. REST itself is not a protocol or specification — it is a set of constraints that, when applied to HTTP, yield RESTful APIs. |
| Disambiguation               | REST vs. RESTful: REST is the architectural style; RESTful describes an API that follows REST constraints. REST vs. OpenAPI: OpenAPI is a specification FORMAT for describing REST APIs; REST is the architecture. REST vs. HTTP: REST uses HTTP as transport but REST is not HTTP — you could theoretically implement REST over other protocols. |
| Where to Find the Spec       | https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm |

---

## 20.4 Disambiguation Writing Rules

**Rule 1: Distinguish by governance, not just function**
- BAD: "Avro vs Protobuf: both are serialization formats"
- GOOD: "Avro vs Protobuf: Avro embeds schema in every message and is Apache-governed (L5); Protobuf requires out-of-band .proto distribution and is Google-governed (L6)"

**Rule 2: Include version disambiguation for breaking changes**
- "OAuth 2.0 vs OAuth 2.1: OAuth 2.1 consolidates best practices — removes implicit flow and ROPC, mandates PKCE — but is not yet an RFC (draft as of 2024)"

**Rule 3: Distinguish specification from implementation**
- "OpenAPI Specification vs Swagger: Swagger was the v1/v2 name (SmartBear); in 2016 the spec was donated to the OpenAPI Initiative and renamed; Swagger now refers to SmartBear's tooling"

**Rule 4: Distinguish acronym collisions**
- "OPC (OLE for Process Control, legacy COM-based) vs OPC UA (OPC Unified Architecture, IEC 62541): OPC was deprecated; OPC UA is the platform-independent successor"

---

## 20.5 Common Authoring Mistakes

| #  | Mistake                                              | Correction                                         |
|----|------------------------------------------------------|----------------------------------------------------|
| 1  | Confusing standard with implementation               | "Docker is an OCI standard" → Docker implements OCI|
| 2  | Wrong governance tier                                | gRPC is L5/L6, not L3 IETF                        |
| 3  | Outdated version reference                           | Cite TLS 1.3, not TLS 1.2, as current             |
| 4  | Conflating deprecation and retirement               | Deprecated ≠ non-functional; specify RFC/date      |
| 5  | Missing disambiguation for near-synonyms            | Every entry needs vs. comparison if confusable     |
| 6  | Incorrect SIL/ASIL notation                         | "SIL 3" not "SIL-3"; "ASIL D" not "ASIL-D"      |
| 7  | Confusing normative and informative                 | RFC "MUST" is normative; "NOTE" is informative     |
| 8  | Calling de-facto standards "proprietary"            | JSON is IETF-standardized, not proprietary         |
| 9  | Marketing language in Problem Solved                | Write for engineers, not sales decks               |
| 10 | Using Wikipedia as spec URL                         | Link to normative document, not encyclopedia entry |
| 11 | Wrong encoding notation                             | PCIe uses GT/s not Gbps; USB uses Gbps            |
| 12 | Confusing specification access with implementation  | RFC is free; certified implementation may cost     |
| 13 | Treating draft as published                         | Mark drafts as Status: Emerging                    |
| 14 | Omitting predecessor/successor chain                | Every non-root standard has a lineage              |
| 15 | Mixing up governing body with working group         | IETF is the body; TLS WG is the working group     |

---

## 20.6 Quality Checklist

Before publishing any entry:

- [ ] Full Name is exact official name (verified against normative document)
- [ ] Tier assignment justified with one-sentence rationale
- [ ] Governing body name is current (check for mergers/renames)
- [ ] Document number is exact (ISO/RFC/FIPS number verified)
- [ ] Current version is confirmed published (not draft unless marked Emerging)
- [ ] Status correctly reflects Active/Deprecated/Emerging/Legacy
- [ ] Predecessor/Successor fields completed for all non-root standards
- [ ] Problem Solved is from engineer perspective (no marketing language)
- [ ] Key concepts defined BEFORE use in technical sections
- [ ] Disambiguation covers top-3 commonly confused near-synonyms
- [ ] Security considerations included for any crypto or network protocol
- [ ] License/access model is current (check for relicensing events)
- [ ] Spec URL points to normative document (not tutorial/Wikipedia)
- [ ] Cross-references to other encyclopedia chapters are correct
- [ ] All tables use consistent formatting and aligned pipe syntax

---

*End of Chapters 19 & 20 — Master Classification Matrix & Entry Template Guide*
*End of Encyclopedia First Edition*
