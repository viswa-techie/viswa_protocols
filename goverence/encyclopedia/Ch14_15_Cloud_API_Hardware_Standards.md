# Chapters 14 & 15 — Cloud/API/Microservices Standards & Hardware/Semiconductor Standards

## Universal Technology Standards Governance & Regulatory Architecture Encyclopedia
### First Edition, 2024–2026

---

# CHAPTER 14: CLOUD, WEB, API & MICROSERVICES STANDARDS

## 14.1 Cloud Computing Reference Standards

### NIST Cloud Definition (SP 800-145)

| Component              | Values                                                     |
|------------------------|------------------------------------------------------------|
| Essential Characteristics | On-demand self-service, Broad network access, Resource pooling, Rapid elasticity, Measured service |
| Service Models          | IaaS (Infrastructure), PaaS (Platform), SaaS (Software)  |
| Deployment Models       | Private, Community, Public, Hybrid                        |

### ISO/IEC Cloud Standards

| Standard           | Title                                        | Domain              | Tier |
|--------------------|----------------------------------------------|---------------------|------|
| ISO/IEC 17788:2014 | Cloud Computing — Overview and Vocabulary   | Taxonomy            | L1   |
| ISO/IEC 17789:2014 | Cloud Computing — Reference Architecture    | Architecture        | L1   |
| ISO/IEC 19086-1    | Cloud SLA Framework                         | Service levels      | L1   |
| ISO/IEC 27017:2015 | Cloud Security Controls (extends 27002)     | Cloud security      | L1   |
| ISO/IEC 27018:2019 | Protection of PII in public cloud           | Cloud privacy       | L1   |
| ISO/IEC 22123      | Cloud Computing — Concepts and Terminology  | Definitions (2023)  | L1   |

### Cloud Service Models — Responsibility Matrix

| Component           | IaaS (You Manage) | PaaS (Shared)     | SaaS (Provider Manages) |
|---------------------|--------------------|--------------------|--------------------------|
| Application         | Customer           | Customer           | Provider                 |
| Data                | Customer           | Customer           | Shared                   |
| Runtime             | Customer           | Provider           | Provider                 |
| Middleware          | Customer           | Provider           | Provider                 |
| OS                  | Customer           | Provider           | Provider                 |
| Virtualization      | Provider           | Provider           | Provider                 |
| Servers/Storage/Network | Provider       | Provider           | Provider                 |

---

## 14.2 Container and Orchestration Standards

### OCI Specifications

| Specification            | Description                                   | Version | Status  |
|--------------------------|-----------------------------------------------|---------|---------|
| OCI Image Spec           | Container image format (layers, manifest, index)| v1.1  | Current |
| OCI Runtime Spec         | Container lifecycle (create/start/kill/delete) | v1.2   | Current |
| OCI Distribution Spec    | Registry API (push/pull/tags/catalog)         | v1.1    | Current |

### Kubernetes Interface Standards (CRI/CNI/CSI)

| Interface | Full Name                      | Purpose                                    | Transport |
|-----------|--------------------------------|--------------------------------------------|-----------|
| CRI       | Container Runtime Interface    | kubelet ↔ container runtime communication | gRPC      |
| CNI       | Container Network Interface    | Plugin API for pod networking setup        | Exec      |
| CSI       | Container Storage Interface    | Plugin API for persistent volume management| gRPC      |
| CPI       | Cloud Provider Interface       | Cloud-specific LB, routing, node lifecycle | Go interface |
| Device Plugin | Kubernetes Device Plugin API | Expose hardware (GPU/FPGA/RDMA) to pods   | gRPC      |
| Gateway API  | Kubernetes Gateway API       | Next-gen Ingress (L4/L7 routing)          | CRD       |

### Kubernetes API Versioning

| Maturity | Format     | Meaning                                 | SLA                        |
|----------|------------|-----------------------------------------|----------------------------|
| Alpha    | v1alpha1   | Experimental, may change without notice | No backward compatibility  |
| Beta     | v1beta1    | Feature complete, may have minor changes| Breaking changes with notice|
| Stable   | v1         | Production-ready, backward compatible   | 12+ months deprecation     |

---

## 14.3 API Specification Standards

### OpenAPI Specification 3.1

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | OpenAPI Specification 3.1.0                                   |
| Governance                   | OpenAPI Initiative (Linux Foundation)                          |
| Governance Tier              | L3                                                            |
| Key Change in 3.1            | Full JSON Schema 2020-12 alignment (vs. draft-07 in OAS 3.0) |
| Document Structure           | openapi, info, paths, webhooks, components, security, tags    |
| Format                       | YAML or JSON                                                  |
| Tooling                      | Swagger UI, Redoc, Stoplight, Postman, Prism, openapi-generator |
| Relationship to Swagger      | "Swagger" = old name/tools; "OpenAPI" = current spec name     |

### AsyncAPI 3.0

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | AsyncAPI Specification 3.0                                    |
| Governance                   | AsyncAPI Initiative (Linux Foundation)                         |
| Governance Tier              | L5                                                            |
| Purpose                      | Describe event-driven/message-based APIs                      |
| Supported Protocol Bindings  | MQTT, AMQP, Kafka, WebSocket, SNS, SQS, Redis Streams, HTTP  |
| Relationship to OpenAPI      | AsyncAPI is the event-driven complement to OpenAPI (REST)     |
| Key Concepts                 | Channels, Messages, Operations, Bindings, Servers             |

### API Standards Stack

| Layer            | Standard/Spec              | Governance | Purpose                   |
|------------------|----------------------------|------------|---------------------------|
| Business SLA     | ISO/IEC 19086              | ISO (L1)   | Cloud service level agreement |
| API Contract     | OpenAPI 3.1 / AsyncAPI 3.0 | LF (L3/L5)| Interface description      |
| Query Language   | GraphQL SDL                | LF (L5)   | Client-driven data fetching|
| RPC Definition   | gRPC / Protobuf            | CNCF (L5) | Service-to-service calls   |
| Security         | OAuth 2.x + OIDC + TLS 1.3| IETF (L3) | AuthZ + AuthN + encryption |
| Transport        | HTTP/2 (RFC 9113), HTTP/3 (RFC 9114) | IETF (L3) | Application transport |
| Serialization    | JSON / Protobuf / CBOR     | IETF/L3   | Data encoding              |
| Observability    | OpenTelemetry (OTLP)       | CNCF (L5) | Traces, metrics, logs      |

---

## 14.4 Observability Standards

### OpenTelemetry (OTel)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | OpenTelemetry                                                  |
| Governance                   | CNCF (Graduated — 2024)                                      |
| Governance Tier              | L5                                                            |
| Key Protocol                 | OTLP (OpenTelemetry Protocol) — gRPC + HTTP/protobuf          |
| Three Signals                | Traces, Metrics, Logs                                         |
| Predecessor Projects         | OpenTracing (CNCF) + OpenCensus (Google) → merged into OTel  |
| SDK Languages                | Java, Go, Python, .NET, JavaScript/TypeScript, C++, Ruby, PHP, Swift, Erlang/Elixir, Rust |
| Collector                    | Vendor-neutral pipeline: receive → process → export           |
| Export Targets               | Prometheus, Jaeger, Zipkin, Grafana Tempo, Datadog, New Relic, Splunk, etc. |

### Observability Standards Comparison

| Standard/Tool   | Signal   | Governance | Protocol          | Status          |
|-----------------|----------|------------|-------------------|-----------------|
| OpenTelemetry   | All 3    | CNCF (L5)  | OTLP (gRPC/HTTP) | **Primary standard** |
| Prometheus      | Metrics  | CNCF (L5)  | HTTP pull + remote write | Dominant for metrics |
| OpenMetrics     | Metrics  | CNCF       | Text exposition format | Prometheus formalization |
| Jaeger          | Traces   | CNCF (L5)  | OTLP (migrated from Thrift) | Integrated with OTel |
| Fluent Bit      | Logs     | CNCF (L5)  | Various outputs   | Log collection  |

---

## 14.5 Infrastructure as Code (IaC) Standards

| Tool/Spec       | Governance        | Tier   | Language    | Multi-cloud? | Status            |
|-----------------|-------------------|--------|-------------|--------------|-------------------|
| Terraform/OpenTofu | HashiCorp(BSL)/LF(OpenTofu) | L6/L5 | HCL   | Yes        | Dominant; forked  |
| Pulumi          | Pulumi Inc        | L6     | Python/TS/Go/C# | Yes      | Growing           |
| AWS CloudFormation | Amazon          | L7     | JSON/YAML   | No (AWS only)| AWS standard      |
| Azure Bicep     | Microsoft         | L6     | Bicep DSL   | No (Azure only)| Azure preferred  |
| Crossplane      | CNCF (Incubating) | L5     | YAML (K8s CRDs)| Yes      | K8s-native IaC   |
| TOSCA 2.0       | OASIS             | L3     | YAML        | Yes          | Standard (low adoption)|

---

## 14.6 Serverless and Event Standards

### CloudEvents (CNCF)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | CloudEvents Specification                                     |
| Governance                   | CNCF (Graduated)                                              |
| Purpose                      | Common event metadata format for interoperability             |
| Format                       | JSON (primary), Protobuf, Avro, XML                           |
| Required Attributes          | specversion, id, source, type                                 |
| Protocol Bindings            | HTTP, MQTT, AMQP, Kafka, NATS, WebSocket                     |

---

## 14.7 FinOps Standards

| Standard/Framework | Governance           | Purpose                              |
|--------------------|----------------------|--------------------------------------|
| FOCUS              | FinOps Foundation (LF)| FinOps Open Cost & Usage Specification — common billing data format |
| FinOps Framework   | FinOps Foundation    | Maturity model: Inform → Optimize → Operate |
| TBM (Technology Business Management) | TBM Council | IT cost taxonomy and allocation   |

---

## 14.8 Cloud/API Standards Master Table

| Standard          | Governance      | Tier | Domain               | Open? | Status     |
|-------------------|-----------------|------|----------------------|-------|------------|
| OCI Image/Runtime | OCI (LF)       | L5   | Container format     | Yes   | Current    |
| Kubernetes API    | CNCF (LF)      | L5   | Container orchestration | Yes | Current    |
| OpenAPI 3.1       | OAI (LF)       | L3   | REST API description | Yes   | Current    |
| AsyncAPI 3.0      | AsyncAPI (LF)   | L5   | Event-driven API     | Yes   | Current    |
| GraphQL           | GraphQL Fdn (LF)| L5  | Query language       | Yes   | Current    |
| gRPC              | CNCF (LF)      | L5   | RPC framework        | Yes   | Current    |
| OpenTelemetry     | CNCF (LF)      | L5   | Observability        | Yes   | Current    |
| CloudEvents       | CNCF (LF)      | L5   | Event format         | Yes   | Current    |
| Envoy xDS         | CNCF (LF)      | L5   | Service mesh control | Yes   | Current    |
| FOCUS             | FinOps Fdn (LF) | L5  | Cloud cost data      | Yes   | Current    |
| Terraform HCL     | HashiCorp (BSL) | L6  | IaC                  | BSL 1.1| Fork: OpenTofu |
| HTTP/2            | IETF            | L3   | Transport            | Yes   | RFC 9113   |
| HTTP/3            | IETF            | L3   | Transport (QUIC)     | Yes   | RFC 9114   |

---

# CHAPTER 15: HARDWARE & SEMICONDUCTOR INTERFACE STANDARDS

## 15.1 PCI Express (PCIe)

### Full Entry

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Peripheral Component Interconnect Express                     |
| Abbreviation                 | PCIe                                                          |
| Governing Body               | PCI Special Interest Group (PCI-SIG)                          |
| Governance Tier              | L3 (industry consortium)                                      |
| Founded                      | 1992 (PCI); PCIe 1.0 specification in 2003                   |
| Topology                     | Point-to-point serial links (lanes) organized in x1–x16 configurations |
| Specification Access         | PCI-SIG members only (paid membership)                        |

### PCIe Generation Comparison

| Generation | Year | Per-Lane Rate | Encoding      | x16 Bandwidth | Key Innovation           |
|------------|------|---------------|---------------|---------------|--------------------------|
| PCIe 1.0   | 2003 | 2.5 GT/s      | 8b/10b        | ~4 GB/s       | Base specification       |
| PCIe 2.0   | 2007 | 5.0 GT/s      | 8b/10b        | ~8 GB/s       | 2× bandwidth            |
| PCIe 3.0   | 2010 | 8.0 GT/s      | 128b/130b     | ~16 GB/s      | Scrambled encoding       |
| PCIe 4.0   | 2017 | 16.0 GT/s     | 128b/130b     | ~32 GB/s      | NVMe SSDs, GPU           |
| PCIe 5.0   | 2019 | 32.0 GT/s     | 128b/130b     | ~64 GB/s      | AI/ML accelerators       |
| PCIe 6.0   | 2022 | 64.0 GT/s     | PAM4 + FEC + FLIT | ~128 GB/s | PAM4 modulation, FEC    |
| PCIe 7.0   | 2025 | 128.0 GT/s    | PAM4 + FEC    | ~256 GB/s     | Next-gen AI/HPC          |

### CXL (Compute Express Link)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Compute Express Link                                          |
| Governance                   | CXL Consortium (Intel, AMD, ARM, Google, Meta, Samsung, others)|
| Governance Tier              | L3                                                            |
| Physical Layer               | PCIe electrical PHY (shares PCIe physical infrastructure)     |
| Current Version              | CXL 3.1 (2023)                                               |

| Protocol Type | Name       | Purpose                                        |
|---------------|------------|------------------------------------------------|
| CXL.io        | I/O        | Standard PCIe I/O operations (discovery, config)|
| CXL.cache     | Cache      | Device-to-host cache coherency (device caches host memory) |
| CXL.mem       | Memory     | Host-to-device memory access (memory expansion)|

---

## 15.2 USB (Universal Serial Bus)

### USB Generations

| Version        | Marketing Name    | Speed      | Connector             | Key Feature              |
|----------------|-------------------|------------|-----------------------|--------------------------|
| USB 1.1        | USB               | 12 Mbps    | Type-A, Type-B, Mini  | Original (1998)          |
| USB 2.0        | Hi-Speed USB      | 480 Mbps   | Type-A, B, Mini, Micro| 40× speedup (2000)     |
| USB 3.2 Gen 1  | SuperSpeed USB 5Gbps | 5 Gbps | Type-A, Type-C       | Additional data pairs    |
| USB 3.2 Gen 2  | SuperSpeed USB 10Gbps| 10 Gbps| Type-A, Type-C       | Better encoding          |
| USB 3.2 Gen 2×2| SuperSpeed USB 20Gbps| 20 Gbps| Type-C only          | Two-lane operation       |
| USB4 Gen 2×2   | USB4 20Gbps       | 20 Gbps    | Type-C only           | Tunnels PCIe + DP       |
| USB4 Gen 3×2   | USB4 40Gbps       | 40 Gbps    | Type-C only           | ≡ Thunderbolt 4         |
| USB4 v2 Gen 4  | USB4 80Gbps       | 80 Gbps    | Type-C only           | Asymmetric 120G possible|

### USB Power Delivery (USB PD)

| PD Version | Max Power | Voltage Levels                    | Key Feature               |
|------------|-----------|-----------------------------------|---------------------------|
| PD 2.0     | 100W      | 5V, 9V, 15V, 20V                 | Fixed voltage PDOs        |
| PD 3.0     | 100W      | 5V, 9V, 15V, 20V + PPS          | Programmable Power Supply |
| PD 3.1     | **240W**  | 5V–48V (Extended Power Range)    | Laptop/monitor charging   |

---

## 15.3 Memory Standards (JEDEC)

### JEDEC Memory Generations

| Standard    | Full Name                    | Speed        | Bandwidth/Channel | Primary Use Case    |
|-------------|------------------------------|--------------|-------------------|---------------------|
| DDR4-3200   | DDR4 SDRAM                   | 3200 MT/s    | ~25.6 GB/s       | Desktop/Server (legacy) |
| DDR5-6400   | DDR5 SDRAM                   | 6400 MT/s    | ~51.2 GB/s       | Desktop/Server (current)|
| DDR5-8800   | DDR5 SDRAM (future)          | 8800 MT/s    | ~70.4 GB/s       | High-end server     |
| LPDDR5X-8533| Low Power DDR5X             | 8533 MT/s    | ~68.3 GB/s       | Mobile, automotive  |
| HBM3E       | High Bandwidth Memory 3E    | 9.6 Gbps/pin | ~1.2 TB/s (8-Hi) | AI/ML accelerators  |
| GDDR7       | Graphics DDR 7               | 32 Gbps/pin  | ~1.8 TB/s (384-bit) | GPU memory       |

### DDR5 vs. DDR4 Key Differences

| Feature             | DDR4                    | DDR5                        |
|---------------------|-------------------------|-----------------------------|
| Speed range         | 1600–3200 MT/s         | 4800–8800+ MT/s             |
| Voltage             | 1.2V                   | 1.1V                        |
| Bank groups         | 4                      | 8                           |
| Burst length        | 8                      | 16                          |
| Channels per DIMM   | 1 (64-bit)            | 2 (2×32-bit)               |
| On-die ECC          | No                     | Yes                         |
| PMIC                | On motherboard VRM     | On-DIMM power management IC |

---

## 15.4 NVMe and Storage Standards

| Standard      | Full Name                          | Governance  | Interface    | Bandwidth           |
|---------------|------------------------------------|-------------|--------------|---------------------|
| NVMe 2.0      | NVM Express 2.0                    | NVMe.org    | PCIe 5.0 x4 | ~14 GB/s            |
| NVMe-oF 1.1   | NVMe over Fabrics                  | NVMe.org    | RDMA/TCP     | Network-limited     |
| SATA 3.3      | Serial ATA 3.3                     | SATA-IO     | SATA         | 600 MB/s (6 Gbps)  |
| SAS-4         | Serial Attached SCSI 4             | INCITS T10  | SAS          | 22.5 Gbps/port      |
| UFS 4.0       | Universal Flash Storage 4.0        | JEDEC       | MIPI M-PHY   | 4200 MB/s           |
| eMMC 5.1      | Embedded MultiMediaCard             | JEDEC       | Parallel     | 400 MB/s            |

---

## 15.5 Display Interface Standards

| Standard        | Governing Body | Max Bandwidth | Max Resolution     | Key Feature           |
|-----------------|----------------|---------------|--------------------|-----------------------|
| DisplayPort 2.1 | VESA          | 80 Gbps (UHBR20) | 16K@60Hz       | DSC, MST, Adaptive-Sync |
| HDMI 2.1a       | HDMI Forum    | 48 Gbps       | 10K, 4K@120Hz     | eARC, VRR, ALLM      |
| USB-C DP Alt Mode| USB-IF + VESA | Via USB4 tunnel| 8K              | Single-cable display   |
| Thunderbolt 5   | Intel + USB-IF | 120 Gbps (asym)| 3× 4K@144Hz    | DP 2.1 + PCIe + USB4 |

---

## 15.6 Low-Level Hardware Bus Protocols

| Protocol | Standard/Origin     | Speed         | Wires  | Topology      | Use Case                  |
|----------|---------------------|---------------|--------|---------------|---------------------------|
| I2C      | NXP/Philips (L8)    | 100k–5 MHz   | 2 (SDA, SCL) | Multi-master bus | Sensors, EEPROMs, PMICs |
| SPI      | Motorola (L8)       | 1–100+ MHz   | 4 (MOSI, MISO, SCLK, CS) | Master-slave | Flash, ADC, displays |
| UART     | TIA/EIA-232 (L3)    | Configurable  | 2 (TX, RX)   | Point-to-point | Debug, GPS, serial      |
| I3C      | MIPI Alliance (L3)  | 12.5 MHz      | 2 (SDA, SCL) | Multi-drop    | Next-gen I2C replacement |
| 1-Wire   | Dallas/Maxim (L8)   | 16 kbps       | 1 (+GND)     | Single-wire   | Temperature sensors       |
| SMBus    | Intel (L8)          | 100 kHz       | 2             | Multi-master  | PC management (battery, fans) |
| JTAG     | IEEE 1149.1 (L4)    | DC–25 MHz     | 4 (TDI, TDO, TMS, TCK) | Daisy chain | Boundary scan, debug  |
| SWD      | ARM (L7)            | Up to 100 MHz | 2 (SWDIO, SWCLK) | Point-to-point | Cortex-M debug    |

### I2C vs. SPI vs. UART

| Feature          | I2C                    | SPI                     | UART                    |
|------------------|------------------------|-------------------------|-------------------------|
| Wires            | 2 (shared bus)         | 4+ (per device CS)     | 2 (TX/RX)              |
| Speed            | Up to 5 MHz            | Up to 100+ MHz         | Up to 5 Mbps (typical) |
| Addressing       | 7-bit/10-bit address   | Chip Select (CS) per device | None (point-to-point) |
| Multi-device     | Yes (address-based)    | Yes (CS pin per device)| No                      |
| Full duplex      | No (half-duplex)       | Yes                    | Yes                     |
| Complexity       | Medium                 | Low (HW), High (many CS) | Low                  |
| Best for         | Many slow sensors      | Fast peripherals (Flash, display) | Debug, GPS, serial |

---

## 15.7 Processor ISA Standards

| ISA         | Governance       | Tier | License         | Key Implementation       |
|-------------|------------------|------|-----------------|--------------------------|
| x86-64      | Intel + AMD      | L7/L8| Cross-license (closed) | Intel Core, AMD Ryzen |
| ARMv9-A     | ARM Holdings     | L7   | Paid license    | Apple M-series, Snapdragon, Cortex |
| RISC-V      | RISC-V International | L5 | Open (royalty-free) | SiFive, Tenstorrent, Qualcomm |
| MIPS        | Wave Computing   | L7   | Licensed (declining) | Legacy embedded      |

---

## 15.8 Semiconductor EDA Standards

| Standard         | Full Name                        | Governance    | Purpose                        |
|------------------|----------------------------------|---------------|--------------------------------|
| IEEE 1800        | SystemVerilog                    | IEEE SA (L4)  | RTL design + verification HDL  |
| IEEE 1076        | VHDL                             | IEEE SA (L4)  | Hardware description language   |
| IEEE 1666        | SystemC                          | IEEE SA (L4)  | C++ system-level modeling       |
| IEEE 1685        | IP-XACT                          | IEEE SA (L4)  | IP component metadata exchange  |
| UVM              | Universal Verification Methodology| Accellera (L3)| Verification framework (SV)   |
| PSS              | Portable Stimulus Standard       | Accellera (L3)| Reusable test intent            |
| JESD204B/C       | SerDes for ADC/DAC               | JEDEC (L3)    | High-speed converter interface  |

---

## 15.9 Hardware Standards Master Table

| Standard/Protocol | Governing Body   | Tier | Domain                  | Spec Access       | Current Version |
|-------------------|------------------|------|-------------------------|-------------------|-----------------|
| PCIe 6.0          | PCI-SIG          | L3   | High-speed I/O          | Members only      | 6.0 (2022)      |
| PCIe 7.0          | PCI-SIG          | L3   | High-speed I/O          | Members only      | 7.0 (2025)      |
| USB4 v2           | USB-IF           | L3   | Universal I/O           | Free (consumers)  | v2 (2022)       |
| DDR5              | JEDEC            | L3   | System memory           | Members           | DDR5-6400+      |
| HBM3E             | JEDEC            | L3   | AI/HPC memory           | Members           | HBM3E (2024)    |
| GDDR7             | JEDEC            | L3   | Graphics memory         | Members           | GDDR7 (2024)    |
| NVMe 2.0          | NVMe.org         | L3   | Storage protocol        | Free              | 2.0 (2021)      |
| CXL 3.1           | CXL Consortium   | L3   | Cache-coherent link     | Members           | 3.1 (2023)      |
| DisplayPort 2.1   | VESA             | L3   | Display interface       | Members           | 2.1 (2022)      |
| HDMI 2.1a         | HDMI Forum       | L3   | Display/AV interface    | Licensed          | 2.1a (2022)     |
| MIPI CSI-2 v4.0   | MIPI Alliance    | L3   | Camera serial interface | Members           | v4.0            |
| MIPI A-PHY v2.0   | MIPI Alliance    | L3   | Automotive SerDes       | Members           | v2.0            |
| ARM AMBA AXI4     | ARM Holdings     | L7   | SoC interconnect        | Free documentation| AXI4            |
| RISC-V ISA        | RISC-V Intl      | L5   | Processor ISA           | Free (RF)         | Ratified 2019+  |
| IEEE 1800 (SV)    | IEEE SA          | L4   | Hardware verification   | Paid              | 2023            |
| JTAG (IEEE 1149.1)| IEEE SA          | L4   | Debug/test access       | Paid              | 2013            |
| JESD204C          | JEDEC            | L3   | ADC/DAC SerDes          | Members           | JESD204C        |

---

## 15.10 Key Disambiguation Notes

### 1. USB4 vs. Thunderbolt 4 vs. Thunderbolt 5

| Specification    | Speed       | Governance | Relationship                      |
|------------------|-------------|------------|-----------------------------------|
| USB4 Gen 3×2     | 40 Gbps     | USB-IF     | Incorporates TB3 protocol         |
| Thunderbolt 4    | 40 Gbps     | Intel      | USB4 + mandatory features (PCIe tunneling, dual 4K) |
| Thunderbolt 5    | 80/120 Gbps | Intel      | USB4 v2 + Intel requirements      |

### 2. PCIe GT/s vs. GB/s

- **GT/s** = Giga-transfers per second (raw signaling rate)
- **GB/s** = Gigabytes per second (usable data bandwidth after encoding overhead)
- PCIe 3.0: 8 GT/s × 128/130 encoding = ~0.985 GB/s per lane
- PCIe 6.0: 64 GT/s with PAM4 + FEC → ~7.5 GB/s per lane effective

### 3. DDR5 Channel Architecture Change

- DDR4: 1 channel per DIMM (64-bit wide)
- DDR5: **2 independent 32-bit channels per DIMM** — better bank utilization, lower latency for random access
- This is why DDR5 bandwidth numbers sometimes appear doubled

---

*End of Chapters 14 & 15 — Cloud/API/Microservices & Hardware/Semiconductor Standards*
*Next: Chapters 16 & 17 — AI/ML Standards & Safety-Critical Standards*
