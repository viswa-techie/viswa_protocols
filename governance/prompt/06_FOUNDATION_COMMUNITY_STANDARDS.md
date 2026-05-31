# CHAPTER 6 — FOUNDATION & COMMUNITY STANDARDS (L5)
# Prompt for: Universal Technology Standards Governance Encyclopedia
# Depends on: MASTER_PROMPT.md, Chapter 1 (taxonomy)

---

## CHAPTER SCOPE

**Covers (L5 — Foundation / Community Bodies):**
- Linux Foundation — overall, plus key sub-projects
- Apache Software Foundation (ASF)
- Eclipse Foundation
- CNCF (Cloud Native Computing Foundation) — Kubernetes, Prometheus, Envoy, gRPC, OCI
- OCI (Open Container Initiative) — container runtime and image specs
- OpenSSF (Open Source Security Foundation)
- RISC-V International — open ISA
- Khronos Group — OpenGL, Vulkan, OpenCL, OpenXR, WebGL
- OSI (Open Source Initiative) — open source license definitions
- Python Software Foundation (PSF)
- Rust Foundation
- Node.js Foundation (OpenJS Foundation)
- Software Freedom Conservancy
- Free Software Foundation (FSF) + GNU Project
- Mozilla Foundation

**Defers to:**
- gRPC implementation details → Chapter 7 (Vendor-Led Open)
- OpenAPI specification → Chapter 14 (Cloud/Web/API)
- Vulkan/OpenGL GPU programming → Chapter 15 (Hardware/Semiconductor)
- OpenSSF security work → Chapter 13 (Cybersecurity)

---

## GENERATION INSTRUCTIONS FOR THIS CHAPTER

Act as a world-class open source governance and foundation standards specialist.
Generate the complete **Chapter 6: Foundation & Community Standards** encyclopedia entries.

### SECTION 6.1 — FOUNDATION GOVERNANCE MODEL

Explain the L5 foundation governance model:
- How foundations differ from consortia (L3) and professional associations (L4):
  - Community-first: individual contributors + corporate sponsors
  - Apache Way / Linux Foundation model / Eclipse Foundation model
  - Board governance: elected developer representatives + corporate sponsor seats
  - Neutral IP ownership: project assets assigned to foundation, not to any single contributor
  - License-first approach: open source license governs IP; foundation governs project health
- The four main governance archetypes within L5:
  1. **Benevolent Dictatorship For Life (BDFL)**: single technical lead (Python under Guido)
  2. **Elected Technical Steering Committee (TSC)**: democratic, merit-based (Node.js, CNCF projects)
  3. **Corporate Steering Group**: browser vendors effectively control WHATWG
  4. **Apache Way**: lazy consensus, merit, community-over-code
- IP models in open foundations:
  - Apache: CLA (Contributor License Agreement) → copyright assigned to ASF
  - Linux kernel: Developer Certificate of Origin (DCO) — no assignment, just attestation
  - Eclipse Foundation: CLA + IP review process
  - CNCF/Linux Foundation: CLA or DCO depending on project
- Why foundations matter as standards bodies:
  - De-facto standard creation through adoption velocity
  - Neutral governance prevents single-vendor domination
  - Examples: Kubernetes (Google → CNCF) became de-facto container orchestration standard

### SECTION 6.2 — LINUX FOUNDATION

Full encyclopedia entry:

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Linux Foundation                                  |
| Abbreviation                 | LF                                                |
| Entry Type                   | Non-profit foundation + Project host              |
| Governance Tier              | L5                                                |
| Founded                      | 2000 (Open Source Development Labs + Free Standards Group merger 2007) |
| Headquarters                 | San Francisco, California, USA                    |
| Membership                   | Platinum/Gold/Silver/Associate/Academic tiers     |
| Key hosted projects          | Linux kernel, Yocto, OpenEmbedded, Zephyr, ELISA, SPDX, OpenChain, ONOS, DPDK |
| Sub-foundations hosted       | CNCF, OpenSSF, OpenJS Foundation, Hyperledger, RISC-V International |

**Linux Foundation Sub-Foundations and Project Families:**

| Sub-Foundation / Initiative  | Domain                           | Key Projects                         |
|------------------------------|----------------------------------|--------------------------------------|
| CNCF                         | Cloud native computing           | Kubernetes, Prometheus, Envoy, Helm  |
| OpenSSF                      | Open source security             | Scorecards, Sigstore, SLSA, OSSF     |
| OpenJS Foundation            | JavaScript runtime/tools         | Node.js, jQuery, Webpack, Electron   |
| Hyperledger                  | Enterprise blockchain            | Fabric, Besu, Indy, Aries            |
| RISC-V International         | Open processor ISA               | RISC-V ISA specifications            |
| Automotive Grade Linux (AGL) | Automotive Linux platform        | AGL Reference Platform               |
| Zephyr Project               | RTOS for IoT/embedded            | Zephyr RTOS                          |
| ELISA Project                | Linux in safety-critical systems | Safety-grade Linux qualification     |
| SPDX                         | Software Bill of Materials (SBOM)| SPDX format (ISO/IEC 5962:2021)     |
| OpenChain                    | Open source license compliance   | ISO/IEC 5230 (OpenChain)            |
| Yocto Project                | Embedded Linux build system      | BitBake, OpenEmbedded layers         |
| DPDK                         | Data plane development kit       | High-performance packet processing   |

### SECTION 6.3 — APACHE SOFTWARE FOUNDATION (ASF)

Full encyclopedia entry:

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Apache Software Foundation                        |
| Abbreviation                 | ASF                                               |
| Entry Type                   | Non-profit open source foundation                |
| Governance Tier              | L5                                                |
| Founded                      | 1999                                              |
| Headquarters                 | Wilmington, Delaware, USA (legal); Virtual (operational) |
| Governance model             | "The Apache Way" — meritocracy, consensus, community |
| License                      | Apache License 2.0 (permissive — patent grant included) |
| Project governance           | PMC (Project Management Committee) per project    |
| IP model                     | CLA → copyright assigned to ASF                  |

**Key Apache Projects relevant to technology standards:**

| Project          | Category                 | Role in Standards Ecosystem              |
|------------------|--------------------------|------------------------------------------|
| Apache HTTP      | Web server               | Reference implementation for HTTP        |
| Apache Kafka     | Distributed streaming    | De-facto event streaming standard         |
| Apache Avro      | Data serialization       | Schema-based binary serialization        |
| Apache Thrift    | IDL + RPC framework      | Facebook-origin cross-language RPC/IDL   |
| Apache Arrow     | In-memory columnar format| De-facto analytics memory format         |
| Apache Parquet   | Columnar storage format  | De-facto big data storage format         |
| Apache ORC       | Columnar storage format  | Alternative to Parquet in Hive ecosystem |
| Apache Flink     | Stream processing        | Dataflow programming model               |
| Apache Hadoop    | Distributed storage/compute | Foundation of big data ecosystem      |
| Apache Camel     | Integration framework    | Enterprise integration patterns impl.    |
| Apache CXF       | Web services framework   | WS-*, JAX-WS, JAX-RS implementation     |
| Apache Maven     | Build system             | De-facto Java build tool + POM format    |
| Apache Cassandra | NoSQL database           | Wide-column distributed database         |
| Apache ZooKeeper | Distributed coordination | De-facto distributed coordination service|
| Apache Spark     | Distributed processing   | De-facto big data processing engine      |

### SECTION 6.4 — ECLIPSE FOUNDATION

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Eclipse Foundation                                |
| Entry Type                   | Non-profit open source foundation                |
| Governance Tier              | L5                                                |
| Founded                      | 2004 (spun out of IBM)                            |
| Key governance feature       | Eclipse Public License (EPL) — copyleft with commercial exception |
| Key technology areas         | IDE (Eclipse IDE), Jakarta EE (enterprise Java), IoT (Eclipse IoT), automotive (Eclipse SDV) |
| Eclipse IoT Working Group    | Hosts MQTT clients, Eclipse Kura (IoT gateway), Eclipse Mosquitto (MQTT broker) |
| Eclipse SDV (Software Defined Vehicle) | Hosts Kuksa.val, Eclipse Leda, Eclipse uProtocol, Velocitas |
| Jakarta EE                   | Successor to Java EE — enterprise Java specifications |

### SECTION 6.5 — CNCF (CLOUD NATIVE COMPUTING FOUNDATION)

Full encyclopedia entry — critical for cloud standards:

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Cloud Native Computing Foundation                |
| Abbreviation                 | CNCF                                              |
| Entry Type                   | Sub-foundation of Linux Foundation               |
| Governance Tier              | L5                                                |
| Founded                      | 2015                                              |
| Mission                      | Make cloud native computing ubiquitous            |
| Hosted projects              | 170+ projects across Graduated, Incubating, Sandbox tiers |

**CNCF Project Maturity Levels:**
```
Sandbox → Incubating → Graduated → (Archived)
```

**Key CNCF Graduated Projects:**

| Project       | Category                      | De-facto standard status               |
|---------------|-------------------------------|----------------------------------------|
| Kubernetes    | Container orchestration       | THE de-facto container orchestration   |
| Prometheus    | Metrics / monitoring          | De-facto metrics/alerting in cloud     |
| Envoy         | Service proxy                 | De-facto data plane proxy (Istio)      |
| Argo          | GitOps / workflow             | De-facto GitOps CD tool                |
| Fluentd       | Log collection                | De-facto log aggregation agent         |
| Jaeger        | Distributed tracing           | De-facto OpenTracing implementation    |
| Vitess        | MySQL sharding                | YouTube-origin DB sharding             |
| Rook          | Storage orchestration         | Cloud-native Ceph orchestration        |
| Harbor        | Container registry            | Enterprise container image registry    |
| OPA           | Policy engine                 | De-facto policy-as-code (Rego language)|
| Falco         | Runtime security              | De-facto cloud workload security       |
| SPIFFE/SPIRE  | Workload identity             | Zero-trust identity standard for cloud |
| Backstage     | Developer portal              | De-facto internal developer portal     |
| OpenTelemetry | Observability framework       | De-facto telemetry SDK standard        |
| Helm          | Package manager               | De-facto Kubernetes application packaging|
| Flux          | GitOps CD                     | GitOps continuous delivery             |
| containerd    | Container runtime             | OCI-compliant runtime (Docker's core)  |
| CNI           | Container networking interface| De-facto container network plugin API  |

**OCI (Open Container Initiative):**

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Open Container Initiative                        |
| Entry Type                   | Sub-project of Linux Foundation                  |
| Governance Tier              | L5                                                |
| Founded                      | 2015 (Docker donated runc)                       |
| Key specifications           | OCI Image Spec, OCI Runtime Spec, OCI Distribution Spec |
| OCI Image Spec               | Defines container image layers, manifest, index format |
| OCI Runtime Spec             | Defines container lifecycle: create, start, kill, delete |
| OCI Distribution Spec        | Defines registry API (push, pull, catalog)       |
| Reference implementation     | runc (low-level), containerd (high-level)        |

### SECTION 6.6 — RISC-V INTERNATIONAL

Full encyclopedia entry — important for hardware standards:

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | RISC-V International                             |
| Entry Type                   | Non-profit open ISA standards body               |
| Governance Tier              | L5                                                |
| Founded                      | 2015 (as RISC-V Foundation); moved to Switzerland 2020 |
| Headquarters                 | Basel, Switzerland                               |
| Mission                      | Develop and maintain the RISC-V open ISA         |
| License model                | RISC-V ISA is open and royalty-free               |
| Key ISA modules              | RV32I, RV64I (integer base), M (multiply), A (atomic), F/D (float), C (compressed), V (vector) |
| Profiles                     | RVA20, RVA22, RVA23 — application processor profiles |
| Relationship to ARM/x86      | RISC-V is the only major open ISA; ARM and x86 are proprietary |

### SECTION 6.7 — KHRONOS GROUP

Full encyclopedia entry:

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Khronos Group                                    |
| Entry Type                   | Industry consortium (acts as L3, but community-oriented → L5 adjacent) |
| Governance Tier              | L3 (paid membership controls) but large community participation |
| Founded                      | 2000                                              |
| Domain                       | Graphics, compute, vision, machine learning, XR   |

**Key Khronos Standards:**

| Standard    | Full Name                              | Domain                    | Version        |
|-------------|----------------------------------------|---------------------------|----------------|
| OpenGL      | Open Graphics Library                  | 3D graphics (desktop)     | 4.6 (2017)     |
| OpenGL ES   | OpenGL for Embedded Systems           | Mobile/embedded 3D         | 3.2 (2015)     |
| Vulkan      | Low-overhead graphics + compute API   | High-performance graphics  | 1.3 (2022)     |
| Metal       | (Apple — NOT Khronos)                  | N/A                       | N/A            |
| WebGL       | WebGL (JS binding to OpenGL ES)        | Web 3D graphics            | 2.0            |
| OpenCL      | Open Computing Language                | GPU compute / heterogeneous| 3.0 (2020)     |
| SYCL        | C++ abstraction for OpenCL/GPU compute | Heterogeneous programming  | 2020 rev.3     |
| OpenXR      | Cross-platform XR (VR/AR) API          | Mixed/virtual reality      | 1.0 (2019)     |
| NNEF        | Neural Network Exchange Format         | ML model portability       | 1.0            |
| SPIR-V      | Standard Portable Intermediate Repr.  | GPU shader/kernel bytecode | 1.6            |
| glTF        | GL Transmission Format                 | 3D asset exchange          | 2.0            |
| KTX         | Khronos Texture format                 | Compressed texture storage | 2.0            |

### SECTION 6.8 — OSI AND LICENSE STANDARDS

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Open Source Initiative                            |
| Abbreviation                 | OSI                                               |
| Entry Type                   | Non-profit — open source license steward         |
| Governance Tier              | L5                                                |
| Founded                      | 1998 (Eric Raymond, Bruce Perens)                |
| Key function                 | Approves and maintains the list of "OSI-Approved" open source licenses |
| OSI Definition               | The Open Source Definition (OSD) — 10 criteria for a license to qualify |

**Key OSI-Approved License Families:**

| License Family   | Examples                    | Type          | Patent Grant | Copyleft Level  |
|------------------|-----------------------------|---------------|--------------|-----------------|
| MIT              | MIT License                 | Permissive    | No explicit  | None            |
| BSD              | BSD 2-Clause, BSD 3-Clause  | Permissive    | No explicit  | None            |
| Apache 2.0       | Apache License 2.0          | Permissive    | Explicit     | None            |
| GPL family       | GPL 2.0, GPL 3.0            | Copyleft      | (GPLv3: Yes) | Strong          |
| LGPL             | LGPL 2.1, LGPL 3.0          | Weak copyleft | (LGPLv3: Yes)| Weak            |
| AGPL             | AGPL 3.0                    | Network copyleft| Yes        | Strong (+ SaaS) |
| MPL              | Mozilla Public License 2.0  | File-level CL | Yes          | File-level      |
| EPL              | Eclipse Public License 2.0  | Weak copyleft | Yes          | Module-level    |
| EUPL             | European Union Public Lic.  | Interoperable | Yes          | Weak/network    |

### SECTION 6.9 — LANGUAGE/RUNTIME FOUNDATIONS

| Foundation              | Language/Runtime     | Governance Model    | License       | Key output                  |
|-------------------------|----------------------|---------------------|---------------|-----------------------------|
| Python Software Fdn     | CPython/Python       | Steering Council    | PSF License   | Python language + CPython   |
| Rust Foundation         | Rust                 | Board of Directors  | MIT/Apache 2.0| Rust compiler + ecosystem   |
| OpenJS Foundation       | JavaScript/Node.js   | Cross-project TSC   | Various       | Node.js, jQuery, etc.       |
| PHP Foundation          | PHP                  | Secretariat model   | PHP License   | PHP 8.x interpreter         |
| Haskell.org             | Haskell              | Community committee | BSD-style     | GHC compiler                |
| OCaml Software Foundation| OCaml               | Community governance| LGPL          | OCaml compiler              |
| Swift.org               | Swift                | Core Team (Apple)   | Apache 2.0    | Swift compiler + stdlib     |

### SECTION 6.10 — FOUNDATION COMPARISON TABLE

| Foundation      | Tier | IP Model | License Default | Project Governance | Corporate Influence | Key Domain              |
|-----------------|------|----------|-----------------|--------------------|---------------------|-------------------------|
| Linux Foundation| L5   | DCO/CLA  | Various         | TSC per project    | High (Platinum memb)| Linux, cloud, auto      |
| ASF             | L5   | CLA/ASF  | Apache 2.0      | PMC per project    | Medium              | Web, data, integration  |
| Eclipse Fdn     | L5   | CLA      | EPL 2.0         | PMC + IP review    | Medium              | IDE, IoT, Jakarta EE    |
| CNCF            | L5   | DCO/CLA  | Apache 2.0      | Graduated/Incubating| Very High (GAFAM) | Cloud native, containers|
| OSI             | L5   | N/A      | N/A (licenses)  | Board of directors | Low                 | License stewardship     |
| RISC-V Intl     | L5   | RISC-V IP| Open + RF       | Technical WGs      | High (chip vendors) | Processor ISA           |
| Khronos Group   | L3   | CLA      | Royalty-free    | Working groups     | Very High (GPU vendors)| Graphics, compute     |
| FSF/GNU         | L5   | Copyright assign| GPL family | RMS-era BDFL → board| Low (independent) | Copyleft advocacy      |

### SECTION 6.11 — DISAMBIGUATION: FOUNDATION CONFUSION PAIRS

1. **Linux Foundation (the org) vs. Linux kernel (the project)**:
   - Linux Foundation hosts 300+ projects; Linux kernel is just one of them
   - Linux kernel governance is Linus Torvalds + subsystem maintainers, not Linux Foundation

2. **Apache Foundation vs. Apache HTTP Server**:
   - Apache Software Foundation hosts 350+ projects; Apache HTTP is just one
   - Apache Kafka, Apache Spark, Apache Avro are ASF projects — NOT Apache HTTP

3. **CNCF vs. OCI vs. Docker**:
   - Docker Inc. (company) donated runc and containerd → Linux Foundation/OCI
   - OCI = specification body for container images + runtimes
   - CNCF = project host for cloud native ecosystem (Kubernetes, etc.)
   - Docker Desktop is a product; Docker Engine uses containerd (OCI-compliant)

4. **Graduated CNCF project vs. CNCF specification**:
   - Most CNCF projects are implementations (code), not formal specifications
   - OpenTelemetry is both: a CNCF project AND a protocol specification
   - Kubernetes is an implementation; the Kubernetes API spec is normative within the project

5. **RISC-V as open ISA vs. open source CPU**:
   - RISC-V International governs the ISA specification — it is open and royalty-free
   - Individual RISC-V CPU implementations (SiFive, VexRiscV) may or may not be open source
   - "RISC-V compliant" = implements the ISA spec; license of the implementation varies

---

## OUTPUT FORMAT REQUIREMENTS

- All project names must use official capitalizations (Kubernetes, not kubernetes; Prometheus, not PROMETHEUS)
- CNCF project tier (Graduated/Incubating/Sandbox) must be current and accurate
- Minimum length: 5,000 words
- Apache project relevance must explain WHY it qualifies as a standards-ecosystem artifact
