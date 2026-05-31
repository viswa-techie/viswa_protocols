# Chapter 8 & 9 — Vendor Proprietary Ecosystems (L7) & De-facto Standards (L8)

## Universal Technology Standards Governance & Regulatory Architecture Encyclopedia
### First Edition, 2024–2026

---

# CHAPTER 8: VENDOR PROPRIETARY ECOSYSTEMS (L7)

## 8.1 L7 Governance Model — Proprietary Standards

### Definition and Characteristics

**L7 (Vendor Proprietary)** represents the most controlled form of technology standardization: a single vendor owns, develops, publishes (or withholds), and controls the entire lifecycle of a specification or API.

| Characteristic              | L7 Proprietary                       | L6 Vendor-Led Open                  |
|-----------------------------|--------------------------------------|--------------------------------------|
| Specification access        | **Closed or documentation-only**     | Open / open-source                   |
| Implementation availability | Vendor-only implementation           | Multiple implementations possible    |
| Community contribution      | None (vendor-internal only)          | Open source contributions accepted   |
| Governance                  | Vendor roadmap / product team        | Vendor + community input             |
| Patent posture              | Aggressively enforced                | RF or FRAND licensing                |
| Switching cost              | Very High (by design)                | Medium (ecosystem effects)           |
| Deprecation risk            | Vendor can EOL without notice        | Community can fork                   |

### When L7 Becomes Problematic

| Scenario                           | Risk                                                      |
|------------------------------------|-----------------------------------------------------------|
| Safety-critical systems            | No independent implementation possible for redundancy     |
| Public infrastructure              | Vendor failure = infrastructure failure                    |
| Government procurement             | Vendor lock-in conflicts with fair competition laws       |
| Long-lifecycle systems (20+ years) | Vendor may discontinue support before system end-of-life  |
| Interoperability requirements      | Proprietary standard prevents multi-vendor integration    |

### Regulatory Pressure on Proprietary Standards

| Regulation / Initiative            | Impact on L7                                              |
|------------------------------------|-----------------------------------------------------------|
| EU Interoperability Act            | Public sector must use open standards where available     |
| EU Cyber Resilience Act (CRA)      | Vulnerability disclosure + transparency requirements      |
| EU Digital Markets Act (DMA)       | Gatekeepers must provide interoperability                 |
| US NIST SP 800-53 (federal)        | Preference for open, non-proprietary formats              |
| UK Open Standards Policy           | Mandates open standards for government data exchange      |

---

## 8.2 Apple Proprietary Ecosystem

### Metal (Graphics API)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Metal (Apple Graphics and Compute API)                         |
| Entry Type                   | Graphics + GPU Compute API                                     |
| Governance Tier              | L7 (Apple proprietary)                                        |
| Available On                 | macOS, iOS, iPadOS, tvOS, visionOS                            |
| First Released               | 2014 (iOS 8)                                                  |
| Apple Silicon Optimization   | Deep integration with A-series / M-series GPU architecture    |
| Shader Language              | Metal Shading Language (MSL) — C++14 dialect                  |
| Competing Open Standards     | Vulkan (Khronos L3), OpenGL (legacy, deprecated on Apple)     |
| MoltenVK                     | Open-source Vulkan → Metal translation layer (allows Vulkan apps on macOS) |
| Specification Access         | Developer documentation free; closed proprietary spec         |
| Lock-in Level                | Very High — no Metal on non-Apple hardware                    |

### Core ML

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Core ML (Core Machine Learning)                                |
| Governance Tier              | L7 (Apple proprietary)                                        |
| .mlmodel Format              | Apple-proprietary model container format (binary protobuf internally) |
| Inference Engine             | Optimized for Apple Neural Engine (ANE), GPU, and CPU         |
| Competing Open Formats       | ONNX (LF AI, L5), TFLite (Google, L6), GGUF (community, L5) |
| Conversion Path              | coremltools (Python) converts PyTorch/TensorFlow → .mlmodel   |
| Cross-Platform               | No — Apple platforms only                                     |

### Apple Platform APIs

| API / Framework        | Domain                    | Governance | Lock-in Impact                  |
|------------------------|---------------------------|------------|----------------------------------|
| Metal                  | Graphics/Compute          | L7         | No Vulkan on Apple (deprecated OpenGL) |
| Core ML                | ML inference              | L7         | .mlmodel format = Apple-only     |
| Core Bluetooth         | BLE communication         | L7         | Apple-specific extensions to BT standard |
| Core Audio / AVFoundation | Audio/Video             | L7         | Platform-tied media framework    |
| SwiftUI / UIKit        | UI framework              | L7         | iOS/macOS-only UI layer          |
| App Store Review Guidelines | Distribution policy   | L7         | Gatekeeping for all iOS apps     |
| Swift ABI              | Binary interface          | L7         | Stable since Swift 5.0 but Apple-controlled |

---

## 8.3 NVIDIA Proprietary Ecosystem

### CUDA (Comprehensive Entry)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Compute Unified Device Architecture                            |
| Abbreviation / Alias         | CUDA                                                           |
| Entry Type                   | GPU Programming Model + Compiler + Runtime + Libraries         |
| Governance Tier              | L7 (NVIDIA proprietary — free to use, closed specification)   |
| First Released               | 2007 (CUDA 1.0)                                               |
| Current Version              | CUDA 12.x (2024)                                              |
| License                      | NVIDIA Software License Agreement (proprietary, free of charge) |
| Access Model                 | Free to download and use; specification NOT open; NVIDIA GPU required |
| Primary Domain               | GPU-accelerated computing: AI/ML training, HPC, scientific simulation |
| Market Position              | 90%+ of GPU compute workloads in data centers use CUDA        |
| Lock-in Mechanisms           | CUDA intrinsics, cuDNN/cuBLAS optimized kernels, NVLink interconnect, NCCL |
| Competing Open Standards     | OpenCL (Khronos, L3), ROCm/HIP (AMD, L6), SYCL (Khronos/Intel, L3), Vulkan Compute |
| Notes                        | CUDA's dominance is the canonical case study of proprietary standard winning through ecosystem effects |

### CUDA Ecosystem Components

| Component      | Purpose                                          | Open Alternative           |
|----------------|--------------------------------------------------|----------------------------|
| nvcc           | CUDA C/C++ compiler                              | Clang CUDA (limited)       |
| cuBLAS         | Dense linear algebra (GEMM)                      | rocBLAS (AMD), oneMKL (Intel) |
| cuDNN          | Deep learning primitives (conv, BN, RNN)         | MIOpen (AMD)               |
| cuFFT          | Fast Fourier Transform                           | rocFFT (AMD)               |
| cuSPARSE       | Sparse matrix operations                         | rocSPARSE (AMD)            |
| NCCL           | Multi-GPU collective communications              | RCCL (AMD), Gloo (Meta)   |
| TensorRT       | Inference optimization + runtime                 | OpenVINO (Intel), TVM (Apache) |
| Nsight         | Profiling and debugging tools                    | rocProf (AMD), VTune (Intel) |
| NVLink/NVSwitch| GPU-to-GPU interconnect fabric                   | Infinity Fabric (AMD), CXL |
| cuDNN Graph API| Fused operation graph execution                  | None equivalent            |

---

## 8.4 Qualcomm Proprietary Ecosystem

### Qualcomm Technologies

| Technology               | Domain                    | Governance | Notes                           |
|--------------------------|---------------------------|------------|----------------------------------|
| Hexagon DSP SDK          | DSP programming (audio, ML)| L7       | Qualcomm SoC-specific DSP       |
| Snapdragon Neural Processing SDK | On-device ML inference | L7    | Qualcomm NPU-optimized          |
| Adreno GPU drivers       | Mobile GPU                | L7         | Vulkan/OpenGL ES implementations (closed-source) |
| FastConnect (Wi-Fi/BT)   | Connectivity              | L7         | Qualcomm-proprietary connection management |
| QNX Hypervisor integration| Automotive compute       | L7         | Qualcomm + BlackBerry QNX       |
| SA8155P / SA8295P        | Automotive SoC interfaces | L7         | Platform-specific BSP/APIs       |

---

## 8.5 ARM Holdings — Architecture Specifications

### ARM Architecture

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | ARM Architecture (Advanced RISC Machines)                      |
| Entry Type                   | Instruction Set Architecture (ISA) + System Architecture       |
| Governance Tier              | L7 (ARM Holdings proprietary — licensed to chip designers)    |
| Key Documents                | ARM Architecture Reference Manual (ARM ARM), ARMv8-A, ARMv9-A |
| License Model                | ARM licenses ISA to chip designers (Qualcomm, Apple, Samsung, MediaTek) |
| Specification Access         | Public documentation (ARM developer portal); implementation requires license |
| Key Standards                | AArch64 (64-bit ISA), AMBA (AXI, AHB, APB bus protocols), SMMU, GIC |
| Competing Open ISA           | **RISC-V** (L5 — fully open, royalty-free)                    |
| Lock-in Level                | High — but widely licensed (hundreds of licensees)            |
| Notes                        | ARM ISA is proprietary but widely accessible. The distinction from RISC-V is licensing cost, not access. |

### ARM AMBA Bus Protocols

| Protocol    | Full Name                              | Purpose                              | Status     |
|-------------|----------------------------------------|--------------------------------------|------------|
| AXI         | Advanced eXtensible Interface          | High-performance SoC interconnect    | AXI5 (current) |
| AHB         | Advanced High-performance Bus          | Mid-performance (legacy, simpler)    | Active     |
| APB         | Advanced Peripheral Bus                | Low-power peripheral access          | Active     |
| ACE         | AXI Coherency Extensions               | Cache-coherent multi-core           | Active     |
| CHI         | Coherent Hub Interface                 | Mesh interconnect for many-core     | CHI (Neoverse) |

---

## 8.6 Intel Proprietary Technologies

| Technology          | Domain                    | Governance | Status                           |
|---------------------|---------------------------|------------|----------------------------------|
| x86/x86-64 ISA     | CPU instruction set       | L7/L8      | De-facto (co-owned with AMD via cross-license) |
| Intel Quick Assist (QAT) | Hardware crypto/compression | L7   | PCIe accelerator card            |
| Intel TDX           | Trusted Domain Extensions | L7         | Confidential computing           |
| Intel SGX           | Software Guard Extensions | L7         | Enclave-based security           |
| Intel oneAPI        | Heterogeneous compute     | L6→L5      | Open spec (Khronos SYCL-based)   |
| Intel VT-x/VT-d    | Hardware virtualization   | L7         | x86 virtualization extensions    |
| Intel AMT           | Remote management         | L7         | Out-of-band management           |
| Thunderbolt         | High-speed I/O            | L7→L3      | Now USB4 (donated to USB-IF)     |

---

## 8.7 Siemens / Industrial Proprietary

| Technology               | Domain                    | Governance | Notes                           |
|--------------------------|---------------------------|------------|----------------------------------|
| TIA Portal               | PLC engineering tool      | L7         | Siemens-proprietary IDE for SIMATIC |
| S7 Communication Protocol| PLC communication         | L7         | SIMATIC S7-300/400/1500 protocol |
| PROFINET                 | Industrial Ethernet       | L3 (PI org)| Siemens-originated but consortium-governed |
| MindSphere              | Industrial IoT platform   | L7         | Siemens cloud platform            |

---

## 8.8 Microsoft Proprietary Ecosystem

| Technology     | Domain                    | Governance | Lock-in Impact                        |
|----------------|---------------------------|------------|---------------------------------------|
| Win32 API      | OS application interface  | L7         | Windows-only; WINE partial compat     |
| COM / DCOM     | Component model           | L7         | Windows process/network IPC           |
| WinRT          | Modern Windows API        | L7         | UWP/WinUI applications                |
| NTFS           | File system               | L7         | Windows default; Linux has ntfs-3g    |
| Active Directory| Directory/auth service   | L7         | Enterprise identity backbone          |
| DirectX 12     | Graphics/Compute API      | L7         | Windows/Xbox-only (vs. Vulkan)        |
| MSVC ABI       | C++ binary interface      | L7         | Different from Itanium ABI (GCC/Clang)|
| MS-SMB2        | File sharing protocol     | L7→L3      | Published (EU antitrust); Samba implements |

---

## 8.9 L7 Proprietary Standards Risk Matrix

| Vendor/Technology | Domain          | Lock-in Risk | Migration Path Available? | Regulatory Pressure |
|-------------------|-----------------|--------------|---------------------------|---------------------|
| Apple Metal       | Graphics        | Very High    | MoltenVK (partial)        | EU DMA             |
| Apple Core ML     | ML inference    | High         | ONNX export possible      | Low                |
| NVIDIA CUDA       | GPU compute     | Very High    | OpenCL/ROCm (limited)     | Growing            |
| ARM ISA           | CPU architecture| Medium       | RISC-V (growing)          | Low                |
| Qualcomm Hexagon  | DSP compute     | Very High    | None equivalent           | Low                |
| Intel x86-64      | CPU ISA         | Medium       | ARM64/RISC-V              | Low                |
| MS Win32/DirectX  | OS/Graphics     | High         | Linux/Vulkan              | EU (historical)    |
| Siemens S7 Comm   | Industrial PLC  | High         | OPC UA (open alternative) | Medium             |
| SAP BAPI/RFC      | Enterprise      | Very High    | SAP BTP APIs (still SAP)  | EU DMA             |
| Oracle OCI/JDBC ext| Database       | High         | Standard JDBC/SQL         | Medium             |

---

# CHAPTER 9: DE-FACTO STANDARDS & INFORMAL CONVENTIONS (L8)

## 9.1 L8 Governance Model — De-facto Standards

### Definition

**L8 (De-facto)** standards are conventions, formats, or practices that become "standards" through widespread adoption rather than through any formal governance body. They have:

- **No formal specification body** (or specification came AFTER adoption)
- **No ratification process** — adoption IS the standard
- **No compliance certification** — "conformance" = "everyone else does it this way"
- **Community enforcement** through tooling, convention, and social pressure
- **Often formalized later** — successful L8 standards get adopted by L3–L5 bodies

### L8 → Formal Standard Migration Path

```
Informal practice → Community convention → De-facto standard
    ↓ (if successful enough)
Formal body adopts → Becomes L1–L5 standard
    
Examples:
  JSON (informal) → RFC 7159 (IETF) → RFC 8259 (Internet Standard)
  REST (dissertation) → OpenAPI (OAI/LF) → de-facto API standard
  TCP/IP (ARPANET) → RFC 793/791 (IETF) → Internet Standard
  Docker format → OCI Image Spec (OCI/LF) → formal spec
  Markdown (informal) → CommonMark (community) → partial formalization
  SemVer (semver.org) → referenced by npm, cargo, etc. → de-facto versioning
```

---

## 9.2 REST — Representational State Transfer

### REST

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Representational State Transfer                                |
| Entry Type                   | Architectural Style (NOT a protocol, NOT a specification)      |
| Governance Tier              | L8 (no formal standard — it's a dissertation concept)         |
| Origin                       | Roy Fielding's PhD dissertation (2000, UC Irvine)             |
| Specification                | **None** — REST is a set of architectural constraints, not a spec |
| Constraints                  | Client-server, Stateless, Cacheable, Uniform Interface, Layered System, Code-on-Demand (optional) |
| Formal Description Formats   | OpenAPI (for REST-like HTTP APIs), JSON:API, HAL              |
| Industry Status              | De-facto standard for web APIs (though many "REST APIs" violate REST constraints) |
| Common Misconception         | "REST = HTTP + JSON endpoints" — FALSE. REST is an architectural style; most APIs called "REST" are actually RPC-over-HTTP |
| Notes                        | Richardson Maturity Model levels (0–3) show how "RESTful" an API actually is |

### Richardson Maturity Model

| Level | Name                | Description                                          | Example              |
|-------|---------------------|------------------------------------------------------|----------------------|
| 0     | Swamp of POX        | Single endpoint, single HTTP method (POST everything)| SOAP, XML-RPC        |
| 1     | Resources           | Multiple URI endpoints representing resources        | /users, /orders      |
| 2     | HTTP Verbs          | Correct use of GET, POST, PUT, DELETE, PATCH         | Most "REST APIs"     |
| 3     | Hypermedia (HATEOAS)| Responses contain links to related actions           | True REST (rare)     |

---

## 9.3 JSON and YAML

### JSON

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | JavaScript Object Notation                                     |
| Entry Type                   | Data Interchange Format                                        |
| Governance Tier              | L8 → L3 (formalized as ECMA-404, RFC 8259 Internet Standard) |
| Origin                       | Douglas Crockford (2001) — documented existing JavaScript literal syntax |
| History                      | De-facto (2001–2006) → ECMA-404 (2013) → RFC 8259 (2017, Internet Standard) |
| Data Types                   | string, number, boolean, null, object ({}), array ([])         |
| Industry Status              | Universal data format for web APIs, configuration, data exchange |
| Notes                        | JSON started as pure L8 — Crockford "discovered" it rather than "invented" it |

### YAML

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | YAML Ain't Markup Language (recursive acronym)                |
| Entry Type                   | Data Serialization Language                                    |
| Governance Tier              | L8 (community-maintained spec at yaml.org; no formal body)    |
| Current Version              | YAML 1.2 (2009)                                               |
| Key Features                 | Indentation-based, human-readable, superset of JSON, anchors/aliases |
| Industry Usage               | Kubernetes manifests, CI/CD configs, Ansible playbooks, Docker Compose |
| Common Problem               | "Norway problem" — `NO` interpreted as boolean false (fixed in YAML 1.2 strict) |
| Notes                        | YAML 1.2 spec says "YAML is a superset of JSON" — every valid JSON is valid YAML 1.2 |

---

## 9.4 Semantic Versioning (SemVer)

### SemVer

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Semantic Versioning                                            |
| Entry Type                   | Versioning Convention / Specification                          |
| Governance Tier              | L8 (community specification at semver.org)                    |
| Author                       | Tom Preston-Werner (GitHub co-founder)                         |
| Current Version              | SemVer 2.0.0                                                  |
| Format                       | MAJOR.MINOR.PATCH (e.g., 2.17.3)                             |
| Rules                        | MAJOR = breaking change; MINOR = backward-compatible feature; PATCH = backward-compatible fix |
| Pre-release                  | 1.0.0-alpha.1, 1.0.0-beta.2                                  |
| Build metadata               | 1.0.0+build.123                                               |
| Adoption                     | npm (Node.js), Cargo (Rust), Go modules, Maven (partial), most modern package managers |
| Notes                        | SemVer is the most widely adopted versioning convention but has no formal standard body backing |

---

## 9.5 Markdown

### Markdown

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Markdown                                                       |
| Entry Type                   | Lightweight Markup Language                                    |
| Governance Tier              | L8 (no single authoritative spec — multiple competing flavors) |
| Original Author              | John Gruber (2004)                                            |
| Formalization Attempt        | CommonMark (2014 — community effort to standardize)           |
| GitHub Flavor                | GFM (GitHub Flavored Markdown — CommonMark + tables, task lists, autolinks) |
| Status                       | De-facto standard for documentation, READMEs, chat, notes     |
| Problem                      | Original spec is ambiguous — different parsers produce different output |
| Competing markup             | reStructuredText (Sphinx/Python docs), AsciiDoc (Red Hat docs) |

---

## 9.6 The C ABI (System V AMD64 ABI)

### System V AMD64 ABI

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | System V Application Binary Interface — AMD64 Architecture Processor Supplement |
| Entry Type                   | ABI Specification (calling convention + data layout)          |
| Governance Tier              | L8 (de-facto — no formal standards body)                      |
| Used By                      | Linux, FreeBSD, macOS (with modifications), all x86-64 UNIX-like systems |
| Key Specifications           | Register usage (RDI, RSI, RDX, RCX, R8, R9 for args), stack alignment (16-byte), red zone |
| Microsoft Equivalent         | Microsoft x64 Calling Convention (different register allocation: RCX, RDX, R8, R9) |
| Impact                       | Any library compiled on Linux x86-64 follows this ABI — enables binary compatibility |
| Notes                        | There is no ISO/IETF/IEEE standard for the C ABI. It's purely de-facto through GCC/Clang convention |

### x86-64 Calling Convention Comparison

| Attribute        | System V AMD64 (Linux/macOS)    | Microsoft x64 (Windows)          |
|------------------|---------------------------------|----------------------------------|
| Integer args (order) | RDI, RSI, RDX, RCX, R8, R9 | RCX, RDX, R8, R9               |
| Float args       | XMM0–XMM7                       | XMM0–XMM3                       |
| Return value     | RAX (int), XMM0 (float)         | RAX (int), XMM0 (float)         |
| Shadow space     | No                               | Yes (32 bytes required)          |
| Red zone         | 128 bytes below RSP              | No red zone                      |
| Stack alignment  | 16-byte at call                  | 16-byte at call                  |

---

## 9.7 x86-64 ISA — The Dominant De-facto CPU Architecture

### x86-64 (AMD64 / Intel 64)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | x86-64 (AMD64 / Intel 64 / x64)                              |
| Entry Type                   | Instruction Set Architecture                                  |
| Governance Tier              | L7/L8 (proprietary ISA owned by Intel+AMD via cross-license; de-facto standard through dominance) |
| Original x86 (8086)         | Intel, 1978                                                   |
| 64-bit Extension             | AMD (AMD64, 2003); Intel adopted as "Intel 64" / "EM64T"     |
| Formal Specification         | Intel Software Developer Manuals (5000+ pages), AMD Architecture Programmer's Manual |
| Market Position              | ~98% of server CPUs, ~75% of desktop/laptop CPUs (declining vs. ARM) |
| Competing ISAs               | ARM (AArch64), RISC-V, MIPS (declining)                      |
| Why It's De-facto            | No standards body governs x86 — Intel and AMD define it through implementations |

---

## 9.8 Git Wire Protocol

### Git Protocol

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Git Wire Protocol (version 2)                                 |
| Entry Type                   | Network Protocol for Git operations                           |
| Governance Tier              | L8 (de-facto; documented in Git source, no external standard) |
| Transport                    | SSH, HTTPS, git:// (daemon)                                   |
| Protocol Version             | v2 (2018) — capability-based negotiation                      |
| Key Operations               | fetch, push, ls-refs                                          |
| Specification                | Technical documentation in Git source tree (Documentation/technical/) |
| Notes                        | Git protocol has NO IETF RFC. It's defined solely by the Git project's implementation. |

---

## 9.9 12-Factor App Methodology

### 12-Factor App

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | The Twelve-Factor App                                          |
| Entry Type                   | Methodology / Best Practice Convention                        |
| Governance Tier              | L8 (blog post by Heroku co-founder Adam Wiggins, 2011)        |
| Specification                | 12factor.net                                                  |
| Industry Status              | De-facto standard for cloud-native application design         |

| Factor | Name           | Principle                                              |
|--------|----------------|--------------------------------------------------------|
| I      | Codebase       | One codebase in version control, many deploys          |
| II     | Dependencies   | Explicitly declare and isolate dependencies            |
| III    | Config         | Store config in the environment                        |
| IV     | Backing services| Treat backing services as attached resources          |
| V      | Build/Release/Run| Strictly separate build and run stages              |
| VI     | Processes      | Execute the app as one or more stateless processes     |
| VII    | Port binding   | Export services via port binding                       |
| VIII   | Concurrency    | Scale out via the process model                        |
| IX     | Disposability  | Maximize robustness with fast startup and graceful shutdown |
| X      | Dev/Prod parity| Keep development, staging, and production as similar as possible |
| XI     | Logs           | Treat logs as event streams                            |
| XII    | Admin processes | Run admin/management tasks as one-off processes       |

---

## 9.10 Docker Compose Format

### Docker Compose

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Docker Compose File Format                                     |
| Entry Type                   | Multi-container Application Definition Format                  |
| Governance Tier              | L8 → L5 (Compose Specification now community-maintained)      |
| Format                       | YAML                                                          |
| Current Version              | Compose Specification v2.x (no longer versioned in file)      |
| Key Concepts                 | services, networks, volumes, configs, secrets                  |
| Industry Status              | De-facto standard for local development multi-container setups |
| Formalization                | compose-spec.io (Docker donated spec to community)            |
| Production Alternative       | Kubernetes manifests (for production orchestration)            |

---

## 9.11 Kubernetes Manifest Conventions

### Kubernetes YAML Conventions

| Convention                   | Description                                                    |
|------------------------------|----------------------------------------------------------------|
| apiVersion/kind/metadata/spec| Four-field structure for all K8s resources                    |
| Labels and Selectors         | app.kubernetes.io/name, app.kubernetes.io/version, etc.       |
| Annotations                  | Free-form metadata (non-selecting)                            |
| Namespace isolation          | Convention: one namespace per team/service/environment         |
| Resource naming              | lowercase, hyphens (my-service, not MyService)                |
| ConfigMap/Secret separation  | Config via ConfigMaps; secrets via Secrets (base64)           |

These are pure L8 conventions — no formal standard mandates them, but the community enforces them through tooling (kubectl, Helm, kustomize) and documentation.

---

## 9.12 Prometheus Exposition Format / OpenMetrics

### Prometheus Exposition Format

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Prometheus Text-based Exposition Format                        |
| Entry Type                   | Metrics Exposition Format                                     |
| Governance Tier              | L8 → L5 (formalized as OpenMetrics under CNCF Sandbox)       |
| Format                       | Line-oriented text: metric_name{label="value"} numeric_value timestamp |
| Metric Types                 | counter, gauge, histogram, summary, untyped                   |
| Industry Status              | De-facto standard for cloud-native metrics exposition         |
| Endpoint Convention          | GET /metrics (HTTP, plain text)                               |
| OpenMetrics (formalization)  | CNCF project formalizing Prometheus format as proper standard |

---

## 9.13 UNIX Philosophy

### UNIX Philosophy (De-facto Design Principles)

| Principle                    | Description                                                    |
|------------------------------|----------------------------------------------------------------|
| Do one thing well            | Each program should do one thing and do it perfectly           |
| Everything is a file         | Uniform interface to hardware, processes, network              |
| Text streams as interface    | Programs communicate via text (pipes)                         |
| Compose small tools          | Complex tasks = pipeline of simple programs                    |
| Fail early and loudly        | Return non-zero exit on error; don't silently continue        |
| Avoid captive interfaces     | Programs should work in pipelines, not require interactive mode |

---

## 9.14 De-facto Standards Summary Table

| Standard / Convention    | Domain                | Origin            | Formalized To    | Current Body       |
|--------------------------|-----------------------|-------------------|------------------|--------------------|
| REST                     | API architecture      | Fielding (2000)   | OpenAPI (partial)| N/A (style)        |
| JSON                     | Data format           | Crockford (2001)  | ECMA-404, RFC 8259| IETF (Internet Std)|
| YAML                     | Data serialization    | Clark/Evans (2001) | YAML 1.2 (community)| yaml.org        |
| Markdown                 | Markup language       | Gruber (2004)     | CommonMark (partial)| None (fragmented)|
| SemVer                   | Versioning            | Preston-Werner     | semver.org 2.0   | Community          |
| 12-Factor App            | App methodology       | Wiggins/Heroku (2011)| N/A            | De-facto           |
| System V AMD64 ABI       | C calling convention  | SCO/AMD/GCC       | N/A              | GCC/Clang impl.    |
| x86-64 ISA               | CPU architecture      | AMD/Intel (2003)  | N/A              | Intel + AMD (L7)   |
| Git wire protocol        | VCS network protocol  | Torvalds (2005)   | N/A              | Git project         |
| Docker Compose format    | Container orchestration| Docker (2014)    | Compose Spec     | compose-spec.io    |
| Prometheus format        | Metrics exposition    | SoundCloud (2012) | OpenMetrics      | CNCF               |
| Makefile                 | Build system          | Bell Labs (1976)  | POSIX make       | IEEE 1003.1        |
| .gitignore               | VCS ignore patterns   | Git project       | N/A              | Git project         |
| .env files               | Configuration         | Community         | N/A              | De-facto (dotenv)  |

---

## 9.15 Key Disambiguation: L7 vs. L8

| Aspect           | L7 (Proprietary)                    | L8 (De-facto)                         |
|------------------|--------------------------------------|---------------------------------------|
| Ownership        | Single vendor explicitly owns        | No one owns; community practice       |
| Intent           | Competitive advantage / lock-in      | Emerged organically from usage        |
| Access           | Restricted or vendor-gated           | Free / openly practiced               |
| Enforcement      | Legal (patents, licensing)           | Social (tooling, convention, docs)    |
| Migration        | Difficult (by design)                | Easy (alternatives can implement)     |
| Example          | CUDA, Metal, Win32, NTFS             | REST, SemVer, Markdown, 12-Factor     |

---

*End of Chapters 8 & 9 — Vendor Proprietary Ecosystems (L7) & De-facto Standards (L8)*
*Next: Chapter 10 — Government & Regulatory Bodies*
