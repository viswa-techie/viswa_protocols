# CHAPTER 14 — DOMAIN: CLOUD, WEB, API & MICROSERVICES STANDARDS
# CHAPTER 15 — DOMAIN: HARDWARE & SEMICONDUCTOR INTERFACE STANDARDS
# Prompt for: Universal Technology Standards Governance Encyclopedia
# Depends on: MASTER_PROMPT.md, Chapters 2, 3, 6, 7

---

# CHAPTER 14: CLOUD, WEB, API & MICROSERVICES STANDARDS

## CHAPTER 14 SCOPE
- Cloud architecture: NIST SP 800-145, ISO/IEC 17788, ISO/IEC 17789, ISO/IEC 19086
- Container and orchestration: OCI, CNCF, Kubernetes API, CRI, CNI, CSI
- API specifications: OpenAPI 3.x, AsyncAPI 2.x/3.x, GraphQL SDL, gRPC/Protobuf
- API gateways and service mesh: Envoy xDS API, SMI (Service Mesh Interface)
- Serverless: CNCF CloudEvents, Knative
- Infrastructure as Code: Terraform HCL, Pulumi, Bicep, CloudFormation
- Microservice patterns: 12-factor app, circuit breaker, bulkhead, CQRS, event sourcing
- Observability: OpenTelemetry, Prometheus/OpenMetrics, Jaeger
- GitOps and deployment: ArgoCD, Flux, OCI Helm charts
- FinOps: FinOps Foundation standards, FOCUS (FinOps Cost and Usage Specification)
- Multi-cloud portability: TOSCA, CAMP (Cloud Application Management for Platforms)

## GENERATION INSTRUCTIONS FOR CHAPTER 14

Act as a world-class cloud architect and API standards specialist.
Generate **Chapter 14: Cloud, Web, API & Microservices Standards**.

### SECTION 14.1 — CLOUD COMPUTING REFERENCE STANDARDS

**NIST Cloud Definition (SP 800-145):**

| NIST Cloud Component     | Description                                          |
|--------------------------|------------------------------------------------------|
| Essential Characteristics| On-demand self-service, broad network access, resource pooling, rapid elasticity, measured service |
| Service Models           | IaaS, PaaS, SaaS                                    |
| Deployment Models        | Private, Community, Public, Hybrid                  |

**ISO/IEC Cloud Standards:**

| Standard           | Title                                              | Domain              |
|--------------------|----------------------------------------------------|---------------------|
| ISO/IEC 17788:2014 | Cloud Computing — Overview and Vocabulary         | Taxonomy            |
| ISO/IEC 17789:2014 | Cloud Computing — Reference Architecture          | Architecture        |
| ISO/IEC 19086-1    | Cloud SLA Framework — Concepts and Terminology    | Service levels      |
| ISO/IEC 19086-4    | Cloud SLA — Security and Privacy                  | Cloud security SLA  |
| ISO/IEC 27017:2015 | Cloud Security Controls                           | Cloud security      |
| ISO/IEC 27018:2019 | PII in public cloud                               | Cloud privacy       |

### SECTION 14.2 — CONTAINER AND ORCHESTRATION STANDARDS

**OCI Specifications (complete):**

| Specification          | Description                                         | Status  |
|------------------------|-----------------------------------------------------|---------|
| OCI Image Spec v1.1    | Container image layer, manifest, index format      | Current |
| OCI Runtime Spec v1.2  | Container lifecycle: create/start/kill/delete/state | Current |
| OCI Distribution Spec v1.1 | Registry API: push/pull/catalog/tags          | Current |

**Kubernetes Interface Standards:**

| Interface  | Full Name                          | Description                                   |
|------------|------------------------------------|-----------------------------------------------|
| CRI        | Container Runtime Interface        | kubelet ↔ container runtime gRPC API          |
| CNI        | Container Network Interface        | Plugin API for container network setup        |
| CSI        | Container Storage Interface        | Plugin API for persistent storage             |
| CPI        | Cloud Provider Interface           | Cloud-specific load balancer + routing        |
| Device Plugin API | Device Plugin API          | GPU/FPGA/RDMA hardware exposure to pods       |

**Kubernetes API Versioning:**
- API Groups: core (v1), apps (v1), batch (v1), networking.k8s.io (v1), etc.
- Stability: Alpha (v1alphaX) → Beta (v1betaX) → Stable (v1)
- CRD (Custom Resource Definition): extends Kubernetes API for custom resources

### SECTION 14.3 — API SPECIFICATION STANDARDS

**OpenAPI Specification 3.1:**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Full Name           | OpenAPI Specification 3.1.0                      |
| Governance          | OpenAPI Initiative (Linux Foundation sub-project)|
| Governance Tier     | L3                                                |
| Key change in 3.1   | Full JSON Schema 2020-12 alignment (vs. JSON Schema draft-07 in OAS 3.0) |
| Document structure  | openapi, info, paths, components, security, tags, externalDocs |
| Spec access         | Free — spec.openapis.org                          |
| Tooling ecosystem   | Swagger UI, Redoc, Stoplight, Postman, Pact, Prism|

**AsyncAPI 3.0:**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Full Name           | AsyncAPI Specification 3.0                       |
| Use case            | Event-driven and message-based API documentation |
| Governance          | AsyncAPI Initiative (Linux Foundation)           |
| Supported bindings  | MQTT, AMQP, Kafka, WebSocket, SNS, SQS, HTTP    |
| Relationship to OAS | AsyncAPI is the event-driven complement to OpenAPI |

**API Governance Standards Stack:**

```
Business Contract:   SLA (ISO 19086)
        ↓
API Specification:   OpenAPI 3.1 / AsyncAPI 3.0 / gRPC Protobuf
        ↓
Security:           OAuth 2.0 + OIDC (IETF/OpenID) + TLS 1.3 (IETF)
        ↓
Transport:          HTTP/2 (RFC 9113) + HTTP/3 (RFC 9114)
        ↓
Format:             JSON (RFC 8259) / Protobuf / CBOR
        ↓
Observability:      OpenTelemetry + Prometheus/OpenMetrics
```

### SECTION 14.4 — SERVICE MESH AND OBSERVABILITY

**OpenTelemetry (CNCF Graduated):**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Full Name           | OpenTelemetry                                    |
| Governance          | CNCF (Graduated 2023)                            |
| Key specification   | OpenTelemetry Protocol (OTLP) — traces, metrics, logs |
| Three pillars       | Traces, Metrics, Logs (Logging specification in progress) |
| Competing/preceding | Jaeger (tracing), Zipkin (tracing), Prometheus (metrics), Fluent Bit (logs) |
| SDK languages       | Java, Go, Python, .NET, JavaScript, C++, Ruby, PHP, Swift, Erlang |

**Envoy xDS API:**

| API      | Description                                      | Governance    |
|----------|--------------------------------------------------|---------------|
| LDS      | Listener Discovery Service                       | Envoy/CNCF    |
| RDS      | Route Discovery Service                          | Envoy/CNCF    |
| CDS      | Cluster Discovery Service                        | Envoy/CNCF    |
| EDS      | Endpoint Discovery Service                       | Envoy/CNCF    |
| SDS      | Secret Discovery Service (TLS certs)            | Envoy/CNCF    |
| ADS      | Aggregated Discovery Service (combines all)     | Envoy/CNCF    |

### SECTION 14.5 — CLOUD API INTEROPERABILITY TABLE

| API/Standard    | Governance   | IaaS | PaaS | SaaS | Multi-cloud | Open |
|-----------------|--------------|------|------|------|-------------|------|
| AWS APIs        | Amazon (L7)  | Yes  | Yes  | Yes  | No          | No   |
| Azure REST APIs | Microsoft(L7)| Yes  | Yes  | Yes  | No          | No   |
| GCP APIs        | Google (L7)  | Yes  | Yes  | Yes  | No          | No   |
| OpenStack APIs  | OpenStack Fdn| Yes  | Limited | No| Yes         | Yes  |
| CIMI (DMTF)     | DMTF (L3)    | Yes  | No   | No  | Partial     | Yes  |
| TOSCA 2.0       | OASIS (L3)   | Yes  | Yes  | No  | Yes         | Yes  |
| Kubernetes API  | CNCF (L5)    | No   | Yes  | No  | Yes         | Yes  |

---

# CHAPTER 15: HARDWARE & SEMICONDUCTOR INTERFACE STANDARDS

## CHAPTER 15 SCOPE
- PCIe (PCI Express): PCI-SIG — PCIe 5.0, 6.0, 7.0
- USB: USB-IF — USB4, USB 3.2, USB 2.0, USB-C
- Thunderbolt: Intel + USB-IF (TB4 = USB4 Gen 3x2)
- SATA / NVMe: SATA-IO, NVMe SSD standards
- DisplayPort / HDMI: VESA (DisplayPort), HDMI Forum
- Memory: JEDEC — DDR5, LPDDR5, HBM3, GDDR7
- Storage: NVMe (NVM Express) — NVMe 2.0, NVMe-oF
- SoC bus: ARM AMBA (AXI4, AHB, APB), RISC-V platforms
- GPIO/Sensor: I2C, SPI, UART, I3C (MIPI), 1-Wire
- Processor ISA: x86-64 (de-facto), ARMv9 (proprietary), RISC-V (open)
- FPGA interchange: RapidIO, JESD204B, AXI (IP-XACT IEEE 1685)
- Automotive hardware: MIPI A-PHY, MIPI CSI-2, 100BASE-T1
- Semiconductor EDA: IP-XACT (IEEE 1685), SystemVerilog (IEEE 1800), SystemC (IEEE 1666)

## GENERATION INSTRUCTIONS FOR CHAPTER 15

Act as a world-class hardware architect and semiconductor interface standards specialist.
Generate **Chapter 15: Hardware & Semiconductor Interface Standards**.

### SECTION 15.1 — PCI EXPRESS (PCIe)

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Peripheral Component Interconnect Express         |
| Governing Body               | PCI Special Interest Group (PCI-SIG)             |
| Governance Tier              | L3                                                |
| Founded                      | 1992 (PCI); PCIe 1.0 in 2003                     |

**PCIe Generation Comparison:**

| Generation | Year | Speed/Lane | x16 Bandwidth | Key Feature                     |
|------------|------|------------|---------------|----------------------------------|
| PCIe 1.0   | 2003 | 2.5 GT/s   | 4 GB/s        | Base spec                        |
| PCIe 2.0   | 2007 | 5.0 GT/s   | 8 GB/s        | 2x bandwidth                     |
| PCIe 3.0   | 2010 | 8.0 GT/s   | ~16 GB/s      | 8b/10b → 128b/130b encoding     |
| PCIe 4.0   | 2017 | 16.0 GT/s  | ~32 GB/s      | 2x PCIe 3.0 bandwidth            |
| PCIe 5.0   | 2019 | 32.0 GT/s  | ~64 GB/s      | AI/ML, NVMe SSD                  |
| PCIe 6.0   | 2022 | 64.0 GT/s  | ~128 GB/s     | PAM4 encoding, FEC, FLIT         |
| PCIe 7.0   | 2025 | 128.0 GT/s | ~256 GB/s     | 8x PCIe 4.0 per lane             |

**Key PCIe concepts:**
- Lane: single differential pair (TX + RX)
- Link: collection of lanes (x1, x2, x4, x8, x16)
- TLP (Transaction Layer Packet): PCIe message unit
- DLLP (Data Link Layer Packet): flow control and ACK
- Base Address Register (BAR): memory-mapped I/O region

### SECTION 15.2 — USB (UNIVERSAL SERIAL BUS)

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Universal Serial Bus                             |
| Governing Body               | USB Implementers Forum (USB-IF)                  |
| Governance Tier              | L3                                                |

**USB Generation Table:**

| Version      | Branding          | Speed      | Connector              | Key Feature               |
|--------------|-------------------|------------|------------------------|---------------------------|
| USB 1.1      | USB 1.1           | 12 Mbps    | Type-A, Type-B, Mini   | Original USB              |
| USB 2.0      | Hi-Speed USB      | 480 Mbps   | Type-A, B, Mini, Micro | 40x speedup               |
| USB 3.2 Gen1 | USB 3.2 Gen1×1    | 5 Gbps     | Type-A, C              | SuperSpeed                |
| USB 3.2 Gen2 | USB 3.2 Gen2×1    | 10 Gbps    | Type-A, C              | SuperSpeed+               |
| USB 3.2 Gen2×2| USB 3.2 Gen2×2   | 20 Gbps    | Type-C only            | Two-lane                  |
| USB4 Gen 2×2 | USB4 20Gbps       | 20 Gbps    | Type-C only            | Tunnels DisplayPort + PCIe|
| USB4 Gen 3×2 | USB4 40Gbps       | 40 Gbps    | Type-C only            | ≡ Thunderbolt 4           |
| USB4 v2 Gen4×2| USB4 80Gbps      | 80 Gbps    | Type-C only            | PAM2, 40G per lane        |

### SECTION 15.3 — MEMORY STANDARDS (JEDEC)

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Governing Body               | JEDEC Solid State Technology Association         |
| Governance Tier              | L3 (ANSI-accredited)                             |
| Founded                      | 1958                                              |
| Domain                       | Semiconductor memory interface standards          |

**JEDEC Memory Standards:**

| Standard    | Full Name                               | Speed          | Bandwidth/Channel | Use Case              |
|-------------|---------------------------------------|----------------|-------------------|-----------------------|
| DDR5-6400   | Double Data Rate 5 SDRAM               | 6400 MT/s      | ~51 GB/s (dual ch)| Desktop/Server        |
| LPDDR5X     | Low Power DDR 5X                       | 8533 MT/s      | ~68 GB/s          | Mobile, automotive    |
| HBM3E       | High Bandwidth Memory 3E               | 9.6 Gbps/pin   | 1.2 TB/s (8-Hi)  | AI accelerators, HPC  |
| GDDR7       | Graphics DDR 7                         | 32 Gbps/pin    | ~1.8 TB/s (384b) | GPU memory            |
| UFS 4.0     | Universal Flash Storage 4.0            | 4200 MB/s seq  | Sequential R/W    | Mobile NAND storage   |
| eMMC 5.1    | Embedded MultiMediaCard 5.1            | 400 MB/s       | Sequential        | IoT, embedded         |
| LPCAMM2     | Low Power Compression Attached Memory Module 2 | LPDDR5X | Laptop form factor| Laptop memory         |

### SECTION 15.4 — NVME AND STORAGE STANDARDS

| Standard      | Full Name                                    | Governing Body | Speed              |
|---------------|----------------------------------------------|----------------|--------------------|
| NVMe 2.0      | NVM Express 2.0                              | NVMe.org       | PCIe 5.0 x4 = ~15 GB/s |
| NVMe-oF       | NVMe over Fabrics                            | NVMe.org       | RDMA/TCP transport |
| SATA 3.3      | Serial ATA 3.3                               | SATA-IO        | 6 Gbps             |
| SAS-4         | Serial Attached SCSI 4                       | INCITS T10     | 22.5 Gbps/port     |
| CXL 3.1       | Compute Express Link                         | CXL Consortium | PCIe 6.0 base      |

**CXL (Compute Express Link) — Emerging Standard:**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Full Name           | Compute Express Link                             |
| Governance          | CXL Consortium (Intel, AMD, ARM, Google, Meta, Samsung) |
| Governance Tier     | L3                                                |
| Protocol types      | CXL.io (PCIe I/O), CXL.cache (CPU-device cache coherency), CXL.mem (memory expansion) |
| Key use cases       | Memory pooling, GPU/accelerator memory, disaggregated memory fabrics |

### SECTION 15.5 — DISPLAY STANDARDS

| Standard       | Full Name                               | Governing Body    | Max Resolution/Rate    |
|----------------|-----------------------------------------|-------------------|------------------------|
| DisplayPort 2.1| VESA DisplayPort 2.1                    | VESA              | 16K@60Hz (UHBR13.5)  |
| HDMI 2.1a      | High-Definition Multimedia Interface 2.1a| HDMI Forum      | 10K@120Hz, eARC       |
| USB-C Alt Mode | USB4/Thunderbolt DP tunneling           | USB-IF            | 8K display via USB-C  |
| DSC 1.2        | Display Stream Compression 1.2          | VESA              | 3:1 visually lossless |
| EDID + E-EDID  | Extended Display ID Data                | VESA              | Monitor capability data|

### SECTION 15.6 — LOW-LEVEL HARDWARE BUS PROTOCOLS

| Protocol  | Standard        | Speed          | Topology    | Typical Use                     |
|-----------|-----------------|----------------|-------------|---------------------------------|
| I2C       | NXP/Philips (de-facto) | 100k–5MHz | Multi-master bus | Sensors, EEPROMs, PMICs |
| SPI       | Motorola (de-facto) | 1–100+ MHz | Master-slave | Flash, ADC, displays         |
| UART      | TIA-232 (RS-232)| Configurable   | Point-to-point | Debug, GPS, serial comms  |
| I3C       | MIPI Alliance    | 12.5 MHz       | Multi-drop  | Next-gen sensors (replaces I2C) |
| 1-Wire    | Dallas/Maxim(de-facto)| 16 kbps  | Single wire | Temperature sensors (DS18B20) |
| SMBus     | Intel (de-facto) | 100 kHz       | Multi-master| PC management (battery, fans)  |
| PMBus     | PMBus Consortium | 100 kHz       | Multi-master| Power management ICs           |
| JTAG      | IEEE 1149.1      | DC–25 MHz     | Daisy chain | Boundary scan, debug, programming|
| SWD       | ARM (de-facto)   | Up to 100 MHz  | 2-wire     | ARM Cortex-M debugging        |

### SECTION 15.7 — SEMICONDUCTOR EDA STANDARDS

| Standard       | Full Name                                  | Governing Body | Description                  |
|----------------|--------------------------------------------|----------------|------------------------------|
| IEEE 1800      | SystemVerilog                              | IEEE SA        | HDL + verification language  |
| IEEE 1666      | SystemC                                    | Accellera/IEEE | C++ system-level modeling    |
| IEEE 1685      | IP-XACT                                    | IEEE SA        | XML schema for IP component metadata|
| VHDL (IEEE 1076)| VHSIC HDL                                | IEEE SA        | HDL for FPGA/ASIC design    |
| Verilog (IEEE 1364)| Hardware Description Language          | IEEE SA        | RTL design language (superseded by SV)|
| PSS (Accellera) | Portable Stimulus Standard                | Accellera      | Reusable test scenarios      |
| UVM             | Universal Verification Methodology        | Accellera      | Verification methodology + base class library |
| Cocotb          | Coroutine-based cosimulation testbench     | Cocotb project | Python verification library   |

### SECTION 15.8 — HARDWARE STANDARDS MASTER TABLE

| Standard/Body  | Governing Body | Tier | Domain               | Open/Proprietary | Current Version |
|----------------|----------------|------|----------------------|------------------|-----------------|
| PCIe 6.0       | PCI-SIG        | L3   | High-speed I/O       | Paid member spec | 6.0 (2022)      |
| USB4 v2        | USB-IF         | L3   | Universal I/O        | Free (consumer)  | USB4 v2 (2022)  |
| DDR5           | JEDEC          | L3   | Memory interface     | Member spec      | DDR5-6400       |
| HBM3E          | JEDEC          | L3   | HBM memory           | Member spec      | HBM3E           |
| NVMe 2.0       | NVMe.org       | L3   | Storage protocol     | Free             | 2.0 (2021)      |
| CXL 3.1        | CXL Consortium | L3   | Cache-coherent interconnect| Member spec| 3.1 (2023)    |
| IEEE 802.3      | IEEE SA        | L4   | Ethernet             | Paid             | 802.3ck (2022)  |
| MIPI CSI-2     | MIPI Alliance  | L3   | Camera interface     | Member spec      | v4.0            |
| MIPI A-PHY     | MIPI Alliance  | L3   | Auto SerDes          | Member spec      | v2.0            |
| ARM AMBA AXI4  | ARM (de-facto open doc)| L7| SoC interconnect | Free doc (ARM ctrl)| AXI4         |
| RISC-V ISA     | RISC-V Intl    | L5   | Processor ISA        | Open RF          | Unpriv 20191213 |
| IEEE 1800 SV   | IEEE SA        | L4   | Hardware verification| Paid             | 2023            |
| JTAG (IEEE 1149.1)| IEEE SA     | L4   | Chip debugging       | Paid             | 2013            |

---

## OUTPUT FORMAT REQUIREMENTS FOR BOTH CHAPTERS

- PCIe speeds must use correct notation: GT/s (gigatransfers per second), not Gbps
- USB branding names must use official USB-IF marketing names (e.g., USB4 not USB 4.0)
- JEDEC standard designations must be exact (DDR5-6400, LPDDR5X, HBM3E)
- All JEDEC memory bandwidth figures must specify number of channels
- ARM architecture names: AArch64, ARMv9-A (not ARM64)
- Minimum length: Chapter 14 = 3,500 words; Chapter 15 = 4,000 words
