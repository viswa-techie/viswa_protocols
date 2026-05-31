# CHAPTER 8 — VENDOR PROPRIETARY ECOSYSTEMS & APIs (L7)
# CHAPTER 9 — DE-FACTO STANDARDS & INFORMAL CONVENTIONS (L8)
# Prompt for: Universal Technology Standards Governance Encyclopedia
# Depends on: MASTER_PROMPT.md, Chapter 1, Chapter 7

---

## CHAPTER 8 SCOPE

**Covers (L7 — Vendor Proprietary):**
- Apple: Metal, Core ML, Core Bluetooth, Swift API ABI, iOS/macOS APIs, App Store policies
- NVIDIA: CUDA, TensorRT, NVLink, NVAPI, PhysX, Nsight toolchain
- Qualcomm: Hexagon DSP API, Snapdragon Neural Processing SDK, FastConnect
- Intel: OneAPI, DPDK (Intel origin → Linux Foundation), Quick Assist, Intel TDX attestation
- ARM Holdings: ARM Architecture Reference Manual, AArch64, AMBA AXI/AHB/APB
- Broadcom: VC4/V3D GPU (Raspberry Pi), Broadcom SoC interfaces
- Siemens: TIA Portal API, SIMATIC S7 communication protocol
- SAP: BAPI, RFC (Remote Function Call in ABAP), HANA API
- Oracle: Oracle JDBC extensions, OCI (Oracle Call Interface), PL/SQL
- Microsoft: Win32 API, COM/DCOM, WinRT, NTFS internals

---

## CHAPTER 9 SCOPE

**Covers (L8 — De-facto Standards):**
- REST (Representational State Transfer)
- JSON (before RFC 8259)
- YAML
- POSIX shell conventions
- x86 / x86-64 ISA (Intel/AMD de-facto)
- ARM architecture (before ARM Holdings formal specs)
- TCP/IP (before formal IETF RFCs — ARPANET de-facto)
- DNS (before formalization)
- Markdown
- Git wire protocol
- semantic versioning (semver.org)
- 12-factor app methodology
- Makefile conventions
- UNIX philosophy
- The C ABI (System V AMD64 ABI)
- Docker Compose format
- Kubernetes manifest YAML conventions
- OpenMetrics / Prometheus exposition format

---

## GENERATION INSTRUCTIONS FOR CHAPTERS 8 AND 9

Act as a technology architect and standards analyst covering proprietary ecosystems and de-facto conventions.
Generate the complete encyclopedia entries for **Chapter 8: Vendor Proprietary Ecosystems** and
**Chapter 9: De-facto Standards**.

---

# CHAPTER 8: VENDOR PROPRIETARY ECOSYSTEMS (L7)

### SECTION 8.1 — L7 GOVERNANCE MODEL

Explain the L7 governance model:
- Single-vendor complete control: specification, implementation, certification, deprecation
- No external governance body, no community vote, no public RFC process
- Patent protection typically covers both specification and implementation
- Competitive advantage through proprietary standard: switching costs, ecosystem lock-in
- Contrast with L6: L7 does NOT provide open specification access
- When L7 becomes problematic: safety-critical systems, public infrastructure, procurement
- Government and EU regulatory pressure on proprietary standards (EU Interoperability Act, CRA)
- Examples of L7 standards being forced open by regulation

### SECTION 8.2 — APPLE PROPRIETARY ECOSYSTEM

**Metal (Graphics API):**

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Metal (Apple Graphics and Compute API)            |
| Entry Type                   | Graphics + compute API                            |
| Governance Tier              | L7 (Apple proprietary)                            |
| Available on                 | macOS, iOS, iPadOS, tvOS, visionOS                |
| Competing open standard      | Vulkan (Khronos L3), OpenGL (legacy), WebGPU (W3C)|
| MoltenVK                     | Vulkan-on-Metal translation layer (open source)   |
| Specification access         | Developer documentation (free), but closed spec  |

**Core ML:**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Full Name           | Core ML (Core Machine Learning)                  |
| Governance Tier     | L7 (Apple proprietary)                            |
| .mlmodel format     | Apple-proprietary model container format         |
| Competing open format| ONNX (L5/L6), TFLite (Google L6)                |
| On-device inference  | Optimized for Apple Silicon (ANE — Apple Neural Engine) |
| Conversion path      | coremltools converts from PyTorch/TensorFlow to .mlmodel |

**Apple ABI (Application Binary Interface):**
- Swift ABI: stabilized in Swift 5.0 (2019) — locked ABI for Swift stdlib
- Apple does not publish a formal ABI specification
- Objective-C runtime ABI: documented but not formally standardized
- ARM64 calling convention on Apple Silicon: Apple-modified version of the ARM64 PCS

### SECTION 8.3 — NVIDIA PROPRIETARY ECOSYSTEM

**CUDA:**

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Compute Unified Device Architecture               |
| Abbreviation                 | CUDA                                              |
| Entry Type                   | GPU programming model + compiler + runtime API    |
| Governance Tier              | L7 (NVIDIA proprietary)                           |
| Access                       | Free to use; specification is NOT open — only documentation |
| License                      | NVIDIA Software License Agreement (proprietary)   |
| Key components               | CUDA C/C++ language extensions, nvcc compiler, cuBLAS, cuDNN, cuFFT, NCCL |
| Version                      | CUDA 12.x (current)                               |
| Competing open alternatives  | OpenCL (Khronos), ROCm/HIP (AMD), SYCL (Intel/Khronos), Vulkan Compute |
| Market position              | 90%+ of GPU compute workloads in AI/ML use CUDA  |
| Lock-in mechanism            | CUDA-specific intrinsics, cuDNN kernels, NVLink fabric — no portability |

**CUDA Ecosystem Components:**

| Component    | Description                                        | Domain               |
|--------------|----------------------------------------------------|----------------------|
| cuBLAS       | CUDA Basic Linear Algebra Subprograms              | ML training          |
| cuDNN        | CUDA Deep Neural Network library                   | Deep learning        |
| cuFFT        | CUDA Fast Fourier Transform                        | Signal processing    |
| cuSPARSE     | Sparse matrix operations                           | Scientific computing |
| cuRAND       | Random number generation on GPU                   | Simulation           |
| NCCL         | NVIDIA Collective Communications Library           | Distributed training |
| TensorRT     | High-performance inference optimizer               | ML inference         |
| Nsight       | GPU debugging and profiling suite                  | Developer tools      |
| NVLink       | High-bandwidth GPU-GPU interconnect (proprietary) | HPC/ML clusters      |
| NVAPI        | Windows GPU API                                   | Windows GPU control  |

**ROCm (AMD's Open Alternative):**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Full Name           | Radeon Open Compute (ROCm)                       |
| Governance Tier     | L6 (AMD — open source but AMD-led)               |
| HIP                 | Heterogeneous-compute Interface for Portability — CUDA-like API |
| Key advantage       | HIP code can compile for both NVIDIA CUDA and AMD ROCm |
| Limitation          | Less mature tooling, smaller library ecosystem than CUDA |

### SECTION 8.4 — ARM HOLDINGS PROPRIETARY ECOSYSTEM

**ARM Architecture (AArch64 / A64):**

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | ARM Architecture (AArch64 / ARMv9-A)             |
| Entry Type                   | Processor ISA + microarchitecture IP             |
| Governance Tier              | L7 (ARM Holdings — now SoftBank/Arm Ltd)          |
| License model                | Architecture license (ISA only) or implementation license (specific CPU design) |
| Key ISA revisions            | ARMv7-A (32-bit Cortex-A), ARMv8-A (AArch64), ARMv9-A (SVE2, MTE, PAC, BTI) |
| AMBA (bus protocol)          | AMBA AXI4, AHB, APB — de-facto SoC interconnect standard |
| Competing open ISA           | RISC-V International (open, royalty-free)         |
| ARM TrustZone                | Hardware security isolation — proprietary, widely adopted |
| ARM Cortex families          | Cortex-A (application), Cortex-R (real-time), Cortex-M (microcontroller) |

**AMBA (Advanced Microcontroller Bus Architecture):**
ARM AMBA is technically published as a free specification (Arm's documentation), making it
partially L6 rather than pure L7. However, ARM Holdings controls all revisions.

| Bus Protocol  | Full Name                         | Use Case                         |
|---------------|-----------------------------------|----------------------------------|
| AXI4          | Advanced eXtensible Interface 4   | High-bandwidth SoC interconnect  |
| AXI4-Lite     | Simplified AXI4 for control       | Low-bandwidth register access    |
| AXI4-Stream   | Streaming data interface          | DSP, DMA, video pipelines        |
| AHB           | Advanced High-performance Bus     | Peripheral bus (Cortex-M)        |
| APB           | Advanced Peripheral Bus           | Low-speed peripherals            |
| ACE           | AXI Coherency Extensions          | Multi-core cache coherency       |
| CHI           | Coherent Hub Interface            | ARM CMN mesh-based interconnect  |

### SECTION 8.5 — INTEL PROPRIETARY ECOSYSTEM

| Standard/API  | Description                                        | Tier | Open Alternative          |
|---------------|----------------------------------------------------|------|---------------------------|
| oneAPI        | Unified programming model (C++/SYCL) — L6 (Intel open source) | L6 | CUDA, OpenCL |
| Intel TDX     | Trust Domain Extensions — confidential computing   | L7   | AMD SEV, ARM CCA          |
| Intel SGX     | Software Guard Extensions                          | L7   | AMD SEV                   |
| Quick Assist  | Compression/crypto hardware offload API            | L7/L6| Hardware-specific         |
| P-state/C-state| CPU power management interface (ACPI profile)     | L6/L3| ACPI standard             |
| DPDK (Intel origin) | Data Plane Development Kit — donated to Linux Foundation | L5 | XDP/eBPF         |

### SECTION 8.6 — VENDOR PROPRIETARY COMPARISON TABLE (L7)

| Vendor     | Proprietary Standard | Domain          | Open Alternative         | Lock-in Level | Regulatory Risk  |
|------------|----------------------|-----------------|--------------------------|---------------|-----------------|
| Apple      | Metal                | Graphics        | Vulkan, WebGPU           | High          | Medium (EU DMA) |
| Apple      | Core ML              | AI inference    | ONNX, TFLite             | High          | Low             |
| NVIDIA     | CUDA                 | GPU compute     | OpenCL, ROCm, SYCL       | Very High     | High (AI regs)  |
| NVIDIA     | TensorRT             | ML inference    | ONNX Runtime, TFLite     | Very High     | Medium          |
| ARM        | ARM ISA              | Processor arch  | RISC-V                   | Very High     | Medium (EU)     |
| ARM        | AMBA AXI             | SoC bus         | None (de-facto standard) | Very High     | Low             |
| Intel      | Intel SGX/TDX        | Confidential compute| AMD SEV              | High          | Low             |
| Microsoft  | Win32 API            | OS API          | POSIX (partial)          | Very High     | High (EU)       |
| Oracle     | OCI/PL/SQL ext.      | Database        | JDBC standard, SQL       | High          | Medium          |
| SAP        | ABAP/BAPI            | ERP             | REST APIs                | Very High     | Low             |

---

# CHAPTER 9: DE-FACTO STANDARDS & INFORMAL CONVENTIONS (L8)

### SECTION 9.1 — DE-FACTO STANDARD GOVERNANCE MODEL

Explain the L8 de-facto standard model:
- Definition: a technology becomes a standard through adoption, not formal governance
- Three conditions for de-facto standardhood:
  1. Near-universal adoption across competing implementations
  2. Multiple independent implementations (not single-vendor)
  3. No viable alternative for the same purpose in the same domain
- The "network effect" mechanism: more adoption → more tools → more adoption
- De-facto vs. formal: both can coexist (TCP/IP is both IETF formal AND de-facto)
- De-facto → formal migration: why communities eventually formalize successful de-facto standards
- Governance vacuum risk: critical de-facto standards with no governing body
  (example: Markdown has no authoritative spec, causing dialect proliferation)

### SECTION 9.2 — MAJOR DE-FACTO STANDARDS REFERENCE TABLE

| De-facto Standard      | Domain               | Why De-facto              | Formal Equivalent?      | Status    |
|------------------------|----------------------|---------------------------|-------------------------|-----------|
| REST (Fielding 2000)   | Web API architecture | Dissertation, no spec     | No formal standard      | Universal |
| JSON (pre-2013)        | Data format          | Browser JS adoption       | RFC 8259, ECMA-404      | Formalized|
| YAML 1.x               | Config format        | Human-readable data       | YAML 1.2 spec (yaml.org)| Informal  |
| TOML                   | Config format        | Rust ecosystem adoption   | TOML spec (v1.0.0)      | Informal  |
| Markdown               | Lightweight markup   | Gruber 2004 / Stack Overflow| CommonMark (partial)  | Fragmented|
| CommonMark             | Markdown superset    | Spec attempt (2014)       | commonmark.spec         | Partial   |
| semver.org             | Version numbering    | Node.js ecosystem         | No formal standard      | De-facto  |
| Git wire protocol      | VCS transport        | GitHub dominance          | No formal standard      | De-facto  |
| x86-64 (AMD64 ISA)     | Processor ISA        | Market dominance          | No ISO standard         | De-facto  |
| System V ABI (AMD64)   | C calling convention | Linux ABI standard        | ELF specification       | De-facto  |
| UNIX philosophy        | SW design principles | Thompson/Ritchie 1969     | POSIX (partial)         | De-facto  |
| 12-Factor App          | Cloud app design     | Heroku 2012 methodology   | No formal standard      | De-facto  |
| Makefile syntax        | Build automation     | UNIX heritage             | POSIX make (partial)    | De-facto  |
| Docker Compose v3      | Container definition | Docker Inc. format        | No formal standard      | De-facto  |
| Prometheus exposition  | Metrics text format  | OpenMetrics extending it  | OpenMetrics (CNCF spec) | Formalizing|
| OpenMetrics            | Metrics format       | Prometheus community      | CNCF specification      | Formalizing|
| Kubernetes YAML API    | K8s resource manifest| CNCF/Google              | API spec in K8s source  | De-facto  |
| JWT (pre-RFC 7519)     | Auth token format    | OAuth2 ecosystem          | RFC 7519                | Formalized|
| Dockerfile syntax      | Container image build| Docker Inc. proprietary   | OCI Image Spec (partial)| L7/L8     |
| .env files             | Env variable config  | 12-factor app convention  | No standard             | De-facto  |
| ISO 8601 datetime str  | Date/time format     | Widespread text usage     | ISO 8601 (L1 formal)    | Both      |

### SECTION 9.3 — REST AS DE-FACTO STANDARD (DEEP ENTRY)

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Representational State Transfer                   |
| Abbreviation                 | REST                                              |
| Origin                       | Roy Fielding's 2000 PhD dissertation              |
| Entry Type                   | Architectural style (NOT a protocol, NOT a format)|
| Governance Tier              | L8 — no governing body, no formal specification   |
| Formal companion specs       | HTTP (IETF RFC 9110–9112), OpenAPI (L3), JSON (RFC 8259) |
| Key constraints (Fielding)   | Client-server, stateless, cacheable, uniform interface, layered system, (code on demand) |
| Common misuse                | "RESTful API" is often just HTTP+JSON — not truly RESTful (no HATEOAS) |
| HATEOAS                      | Hypermedia As The Engine Of Application State — rarely implemented |
| Competing architectural styles| gRPC (Google L6/L5), GraphQL (Meta L6→L5), SOAP (W3C legacy) |

**REST API Maturity Model (Richardson Maturity Model):**

| Level | Name              | Characteristics                                    |
|-------|-------------------|----------------------------------------------------|
| 0     | The Swamp of POX  | One URL, one HTTP method (POST), XML payload      |
| 1     | Resources         | Multiple URLs, but single HTTP method             |
| 2     | HTTP Verbs        | Proper GET/POST/PUT/DELETE/PATCH usage            |
| 3     | Hypermedia (HATEOAS)| Links in responses guide state transitions      |

### SECTION 9.4 — YAML AS DE-FACTO STANDARD (DEEP ENTRY)

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | YAML Ain't Markup Language (recursive acronym)    |
| Entry Type                   | Human-readable data serialization format          |
| Governance Tier              | L8 (yaml.org specification — no formal governance body) |
| Current spec version         | YAML 1.2.2 (2021) at yaml.org                     |
| Key uses                     | Kubernetes manifests, Ansible playbooks, GitHub Actions, Docker Compose, CI/CD configs |
| Key dangers                  | Norway problem (NO = false), YAML bombs, implicit type coercion |
| Competing formats            | TOML (Rust ecosystem), JSON (machine-readable), JSON5 (comments allowed) |
| Formal standardization       | No ISO/IETF RFC for YAML — highest governance is yaml.org |

### SECTION 9.5 — MARKDOWN FRAGMENTATION CASE STUDY

Produce a detailed case study on Markdown showing what happens when a de-facto standard
has NO formal governance:

- Original Markdown (Gruber, 2004): a loose specification with ambiguities
- Dialect proliferation: GitHub Flavored Markdown (GFM), MultiMarkdown, Pandoc Markdown,
  AsciiDoc, reStructuredText, JIRA Markdown, Confluence Wiki
- CommonMark (2014): attempt to unify with formal spec — partial success
- GitHub GFM: documented at github.github.com/gfm — de-facto for developer content
- No single governing body — each platform implements their own variant
- Lessons: de-facto standards without governance fragment over time

### SECTION 9.6 — SEMANTIC VERSIONING (SEMVER)

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Semantic Versioning                               |
| Specification URL            | semver.org (Tom Preston-Werner — GitHub co-founder)|
| Format                       | MAJOR.MINOR.PATCH (e.g., 2.1.3)                  |
| Rules                        | MAJOR: breaking change; MINOR: backward-compatible feature; PATCH: bug fix |
| Governance                   | No formal body — specification maintained by original author |
| Adoption                     | npm, pip, cargo, composer, gem — all enforce semver |
| Competing conventions        | Calendar versioning (CalVer), date-based versioning |

### SECTION 9.7 — SYSTEM V ABI (AMD64/x86-64)

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | System V Application Binary Interface — AMD64 Architecture Processor Supplement |
| Entry Type                   | ABI specification (calling convention, register usage, stack layout) |
| Governance Tier              | L8 (de-facto — no formal standards body, jointly maintained by AMD, Intel, Linux kernel devs) |
| Key rules                    | RDI, RSI, RDX, RCX, R8, R9 for integer args; XMM0-XMM7 for float args; RAX for return |
| Stack alignment              | 16-byte aligned at function call boundary         |
| ELF object format            | Defined alongside the ABI — ELF (Executable and Linkable Format) |
| Windows ABI difference       | Windows x64 ABI uses different register conventions (RCX, RDX, R8, R9 for first 4 args) |
| Importance                   | All C/C++ toolchains (GCC, Clang, Intel ICC) must conform for FFI interoperability |

### SECTION 9.8 — DE-FACTO STANDARD LIFECYCLE PATTERNS

Produce a table showing how de-facto standards evolve:

| Phase             | Description                                    | Example                          |
|-------------------|------------------------------------------------|----------------------------------|
| Invention         | Single source creates a format/practice        | JSON — Douglas Crockford         |
| Organic adoption  | Developers adopt independently                 | JSON used in browser AJAX        |
| Network effect    | Adoption accelerates; tooling appears          | JSON libraries for every language|
| Alternatives decline | Competing formats lose relevance           | XML loses to JSON for REST APIs  |
| De-facto status   | Universal adoption makes it "standard"         | JSON assumed in all REST APIs    |
| Formalization     | Community formalizes to reduce ambiguity       | JSON → RFC 4627 → RFC 7159 → RFC 8259 / ECMA-404 |
| Mature standard   | Formal spec coexists with de-facto status      | RFC 8259 JSON: both IETF + de-facto|

### SECTION 9.9 — COMPARISON TABLE: L7 vs L8

| Dimension            | L7 (Vendor Proprietary)              | L8 (De-facto)                         |
|----------------------|--------------------------------------|---------------------------------------|
| Control              | Single vendor                        | None (market consensus)               |
| Specification access | Restricted or documentation only     | Public (if documented at all)         |
| Change process       | Vendor decides unilaterally          | Community conventions evolve          |
| Enforcement          | License / copyright                  | Social norm + tool ecosystem          |
| Stability            | Vendor can break or deprecate        | Changes are slow (high switching cost)|
| Interoperability     | Only with vendor permission          | High (multiple implementations)       |
| Formal certification | Vendor certification only            | None                                  |
| Risk                 | Vendor bankruptcy, license change    | Fragmentation, governance vacuum      |
| Example              | CUDA, Apple Metal                    | REST, YAML, Markdown                  |

---

## OUTPUT FORMAT REQUIREMENTS

- All API names must be exact (e.g., "cuDNN" not "CUDNN")
- CUDA version must be current (12.x)
- REST architectural constraints must use Fielding's original terms
- YAML version number must be current (1.2.2)
- System V ABI register names must use Intel/AMD x86-64 nomenclature
- Minimum length for Chapter 8: 3,000 words; Chapter 9: 3,000 words
- Richardson Maturity Model levels must be correct
