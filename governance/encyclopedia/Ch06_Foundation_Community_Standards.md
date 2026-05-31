# Chapter 6 — Foundation & Community Standards Bodies (L5)

## Universal Technology Standards Governance & Regulatory Architecture Encyclopedia
### First Edition, 2024–2026

---

## 6.1 Foundation Governance Model

### What Distinguishes L5 Foundations from L3 Consortia?

Foundations represent the **community-first** approach to standards. Unlike L3 consortia (where corporate membership fees buy influence), L5 foundations create standards through adoption velocity and community governance.

| Characteristic              | L5 Foundation                         | L3 Industry Consortium              |
|-----------------------------|---------------------------------------|--------------------------------------|
| Primary participants        | Individual contributors + corporate sponsors | Corporate members only          |
| Governance                  | Community meritocracy / elected TSC   | Board controlled by funding tier     |
| IP ownership                | Foundation (neutral custodian)        | Consortium or member-contributed     |
| Standard creation           | De-facto (adoption-driven)            | De-jure (ratification process)       |
| License model               | Open source (Apache, MIT, GPL)        | FRAND or member-exclusive            |
| Specification access        | Free (always)                         | Often member-only                    |
| Revenue                     | Membership tiers + events + training  | Membership dues (higher amounts)     |

### Four Governance Archetypes in Open Source

| Archetype                        | Description                                        | Examples                        |
|----------------------------------|----------------------------------------------------|---------------------------------|
| BDFL (Benevolent Dictator for Life) | Single technical lead makes final decisions     | Python (Guido, pre-2018), Linux (Linus) |
| Elected TSC (Technical Steering Committee) | Democratic, merit-based election          | Node.js, Kubernetes, Rust       |
| Apache Way (Lazy Consensus)      | Community-over-code; committers earn merit gradually | All Apache projects            |
| Corporate Steering               | Company representatives dominate technical decisions | WHATWG (browser vendors)        |

### IP Models in Foundations

| Model                          | Mechanism                                          | Example Bodies                  |
|--------------------------------|----------------------------------------------------|---------------------------------|
| CLA (Contributor License Agreement) | Contributor grants broad license to foundation | Apache SF, Eclipse Foundation  |
| DCO (Developer Certificate of Origin)| Contributor attests they have right to submit | Linux kernel, CNCF             |
| Copyright Assignment           | Contributor assigns copyright to foundation/entity | FSF/GNU, Python SF (historical)|
| No formal IP process           | Rely on implicit license of repository             | Many small projects             |

---

## 6.2 Linux Foundation

### Linux Foundation

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | The Linux Foundation                                           |
| Abbreviation / Alias         | LF                                                             |
| Entry Type                   | Body (Non-profit Technology Foundation + Project Host)         |
| Governance Tier              | L5                                                            |
| Governing Organization       | Linux Foundation (non-profit)                                  |
| Founded / Published          | 2000 (OSDL); merged with Free Standards Group 2007             |
| Headquarters                 | San Francisco, California, USA                                 |
| Membership Tiers             | Platinum ($500K/yr), Gold ($100K/yr), Silver ($5K–$20K/yr), Associate, Academic |
| Annual Revenue               | ~$250M (2023 — largest open-source foundation)                |
| Hosted Projects              | 800+ projects across multiple sub-foundations                  |
| Employees                    | 1,000+ (employed kernel maintainers, project managers)        |
| Specification Availability   | **Free** — all specifications and code openly available        |
| Primary Domain               | Linux kernel, cloud native, embedded, automotive, blockchain, security, networking |
| Industry Relevance           | Dominant — Linux runs 90%+ of cloud, 80%+ of smartphones (via Android), all top supercomputers |
| Automotive Relevance         | High (AGL, ELISA, SDV, Zephyr for MCU)                        |
| Cloud Relevance              | Very High (CNCF, OCI, Kubernetes)                             |
| Embedded Relevance           | Very High (Yocto, Zephyr, ELISA)                              |
| Notes                        | LF is not just about Linux kernel — it hosts sub-foundations covering nearly all technology domains |

### Linux Foundation Sub-Foundations and Project Families

| Sub-Foundation / Initiative  | Domain                           | Key Projects / Specs                     |
|------------------------------|----------------------------------|------------------------------------------|
| CNCF                         | Cloud native computing           | Kubernetes, Prometheus, Envoy, Helm, OPA |
| OpenSSF                      | Open source security             | Sigstore, SLSA, Scorecards, OpenSSF Best Practices |
| OpenJS Foundation            | JavaScript ecosystem             | Node.js, jQuery, Webpack, Electron, Deno contributions |
| Hyperledger                  | Enterprise blockchain            | Fabric, Besu, Indy, Aries, AnonCreds     |
| RISC-V International         | Open processor ISA               | RISC-V ISA specifications                |
| Automotive Grade Linux (AGL) | Automotive Linux platform        | AGL Reference Platform, profiles (IVI, instrument cluster) |
| Zephyr Project               | RTOS for IoT/embedded            | Zephyr RTOS (supports 500+ boards)       |
| ELISA Project                | Linux in safety-critical systems | Safety qualification of Linux components |
| SPDX                         | SBOM format                      | SPDX 2.3 spec (ISO/IEC 5962:2021)       |
| OpenChain                    | License compliance               | OpenChain spec (ISO/IEC 5230:2020)       |
| Yocto Project                | Embedded Linux build             | BitBake, OpenEmbedded-Core, Poky         |
| DPDK                         | Network data plane               | High-performance packet processing       |
| LF Edge                      | Edge computing                   | Akraino, EdgeX Foundry, Open Horizon     |
| LF Networking                | Network infrastructure           | ONAP, DPDK, FD.io (VPP)                 |
| LF Energy                    | Energy sector                    | Open power system tools                  |
| Open 3D Foundation           | 3D graphics engine               | Open 3D Engine (O3DE)                    |
| Delta Lake                   | Data lakehouse format            | Delta Lake open table format             |
| AsyncAPI                     | Event-driven API description     | AsyncAPI specification                   |
| OpenTelemetry (via CNCF)     | Observability standard           | OTel SDK, Collector, Protocol (OTLP)     |

### Linux Kernel Governance

| Aspect          | Detail                                                        |
|-----------------|---------------------------------------------------------------|
| Maintainer      | Linus Torvalds (overall); subsystem maintainers per area      |
| IP model        | DCO (Developer Certificate of Origin — sign-off line)         |
| License         | GPL-2.0-only (kernel); LGPL for userspace libraries          |
| Release cycle   | New version every ~10 weeks (5.x → 6.x series)              |
| LTS releases    | 5.4, 5.10, 5.15, 6.1, 6.6 — maintained 2–6 years           |
| MAINTAINERS file| Defines ownership tree for every file/subsystem              |
| Contribution    | Email-based patch submission (git send-email) + mailing lists |

---

## 6.3 Apache Software Foundation (ASF)

### Apache Software Foundation

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | The Apache Software Foundation                                 |
| Abbreviation / Alias         | ASF, Apache                                                    |
| Entry Type                   | Body (Non-profit Open Source Foundation)                        |
| Governance Tier              | L5                                                            |
| Founded / Published          | 1999 (incorporated); Apache HTTP Server since 1995             |
| Headquarters                 | Wilmington, Delaware, USA (legal); Virtual (operational)       |
| Governance Model             | "The Apache Way" — meritocracy, lazy consensus, community-over-code |
| License                      | Apache License 2.0 (permissive — includes explicit patent grant) |
| IP Model                     | Individual CLA (ICLA) or Corporate CLA (CCLA) → copyright to ASF |
| Project Governance           | PMC (Project Management Committee) per top-level project       |
| Total Projects               | 350+ projects                                                  |
| Specification Availability   | **Free** — all code and specifications under Apache 2.0       |
| Industry Relevance           | Very High — Apache projects underpin most enterprise data infrastructure |
| Notes                        | ASF does not pay developers — all contribution is volunteer or corporate-sponsored |

### Key Apache Projects as De-facto Standards

| Project            | Category                   | De-facto Standard Role                         |
|--------------------|----------------------------|------------------------------------------------|
| Apache HTTP Server | Web server                 | Reference HTTP server implementation            |
| Apache Kafka       | Event streaming            | THE de-facto distributed event streaming platform |
| Apache Spark       | Distributed processing     | De-facto large-scale data processing engine    |
| Apache Hadoop      | Distributed storage/compute| Foundation of big data ecosystem (HDFS + YARN) |
| Apache Cassandra   | NoSQL database             | Wide-column distributed database (Facebook origin) |
| Apache Flink       | Stream processing          | Stateful stream processing engine              |
| Apache Arrow       | In-memory columnar format  | De-facto cross-language memory format for analytics |
| Apache Parquet     | Columnar storage           | De-facto big data file format (with Hadoop/Spark) |
| Apache Avro        | Data serialization         | Schema-based binary serialization (Hadoop ecosystem) |
| Apache Thrift      | RPC framework              | Cross-language RPC + IDL (Facebook origin)     |
| Apache Maven       | Build system               | De-facto Java build tool + POM dependency format |
| Apache ZooKeeper   | Distributed coordination   | Consensus and configuration (being replaced by Kafka Raft) |
| Apache Camel       | Integration framework      | Enterprise Integration Patterns implementation |
| Apache Airflow     | Workflow orchestration      | De-facto data pipeline scheduler               |
| Apache Iceberg     | Table format               | Open table format for data lakehouses          |

### The Apache Way — Governance Principles

| Principle              | Meaning                                                    |
|------------------------|------------------------------------------------------------|
| Community over code    | Healthy community matters more than elegant code           |
| Meritocracy            | Influence earned through sustained contribution            |
| Lazy consensus         | Silence = consent; only explicit objections block          |
| Open communication     | All decisions on public mailing lists (no private decisions) |
| Independent governance | No single company controls any project                     |

---

## 6.4 Eclipse Foundation

### Eclipse Foundation

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Eclipse Foundation AISBL (International Non-profit, Belgium)   |
| Entry Type                   | Body (Non-profit Open Source Foundation)                        |
| Governance Tier              | L5                                                            |
| Founded / Published          | 2004 (spun out of IBM Eclipse IDE project)                    |
| Headquarters                 | Brussels, Belgium (EU legal entity since 2021)                 |
| License                      | Eclipse Public License 2.0 (EPL-2.0) — weak copyleft with commercial linking |
| IP Model                     | CLA + mandatory IP review (every contribution checked for IP cleanliness) |
| Key Technology Areas         | IDE (Eclipse IDE), Jakarta EE, IoT, Automotive SDV, Cloud     |
| Membership                   | 350+ member organizations                                      |
| Notes                        | Eclipse Foundation moved to EU (Belgium) to be closer to EU regulatory environment |

### Key Eclipse Foundation Working Groups

| Working Group        | Domain                                  | Key Projects                           |
|----------------------|-----------------------------------------|----------------------------------------|
| Eclipse IDE          | Integrated development environments     | Eclipse IDE, Theia, Che                 |
| Jakarta EE           | Enterprise Java specifications          | Jakarta EE 10/11, MicroProfile         |
| Eclipse IoT          | Internet of Things                      | Mosquitto (MQTT broker), Kura, Ditto   |
| Eclipse SDV          | Software Defined Vehicle                | kuksa.val, Leda, uProtocol, Velocitas, Ankaios |
| Eclipse Adoptium     | Java runtimes                           | Temurin (OpenJDK builds)               |

### Jakarta EE (Enterprise Java Standards)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Jakarta EE (Enterprise Edition)                                |
| Previous Name                | Java EE (transferred from Oracle to Eclipse Foundation, 2017)  |
| Entry Type                   | Specification Platform                                         |
| Current Version              | Jakarta EE 11 (2024)                                          |
| Key Specifications           | Servlet, CDI, JPA, JAX-RS (REST), JSON-B, Bean Validation     |
| Implementations              | GlassFish (RI), WildFly, Open Liberty, Payara, TomEE         |
| Namespace Change             | javax.* → jakarta.* (migration required from Java EE → Jakarta EE) |

---

## 6.5 CNCF — Cloud Native Computing Foundation

### CNCF

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Cloud Native Computing Foundation                              |
| Abbreviation / Alias         | CNCF                                                           |
| Entry Type                   | Body (Sub-foundation of Linux Foundation)                      |
| Governance Tier              | L5                                                            |
| Founded / Published          | 2015 (Google donated Kubernetes as seed project)              |
| Mission                      | "Make cloud native computing ubiquitous"                       |
| Specification Availability   | **Free** — all projects open source                           |
| Primary Domain               | Cloud-native: containers, microservices, service mesh, observability |
| Membership                   | 800+ member organizations (all major cloud providers)          |
| Cloud Relevance              | Dominant — CNCF projects ARE the cloud-native stack           |
| Key Concept                  | "Cloud native" = containerized, dynamically orchestrated, microservices-oriented |
| Notes                        | CNCF's landscape (landscape.cncf.io) maps the entire cloud-native ecosystem — 1,000+ projects |

### CNCF Project Maturity Model

```
Sandbox → Incubating → Graduated → (Archived)

Sandbox:      Early-stage; experimental; CNCF provides neutrality
Incubating:   Growing adoption; meets quality bar (security audit, governance)
Graduated:    Production-ready; proven adoption; full security audit; diverse maintainers
Archived:     No longer actively maintained
```

### CNCF Graduated Projects (De-facto Cloud Standards)

| Project         | Category                       | De-facto Standard Role                       |
|-----------------|--------------------------------|----------------------------------------------|
| Kubernetes (K8s)| Container orchestration        | THE cloud container orchestration standard   |
| Prometheus      | Metrics and alerting           | De-facto metrics collection and alerting     |
| Envoy           | Service proxy / data plane     | De-facto service mesh data plane (Istio, etc.)|
| OpenTelemetry   | Observability SDK/Protocol     | De-facto telemetry instrumentation standard  |
| Helm            | Package manager                | De-facto Kubernetes app packaging            |
| Argo            | GitOps / workflows             | De-facto GitOps continuous delivery          |
| Fluentd/Fluent Bit | Log collection             | De-facto log forwarding agents               |
| containerd      | Container runtime              | De-facto high-level container runtime        |
| CoreDNS         | DNS server                     | Default DNS in Kubernetes                    |
| etcd            | Key-value store                | Kubernetes control plane state store         |
| gRPC            | RPC framework                  | De-facto high-performance RPC (Google origin)|
| Jaeger          | Distributed tracing            | De-facto distributed trace backend           |
| Harbor          | Container registry             | Enterprise-grade container image registry    |
| OPA (Rego)      | Policy engine                  | De-facto policy-as-code engine               |
| SPIFFE/SPIRE    | Workload identity              | Zero-trust workload identity framework       |
| Falco           | Runtime security               | De-facto cloud runtime threat detection      |
| Backstage       | Developer portal               | De-facto internal developer platform         |
| Flux            | GitOps CD                      | GitOps continuous delivery (alongside Argo)  |
| TiKV            | Distributed KV store           | Rust-based distributed storage (PingCAP)     |
| Vitess          | MySQL sharding                 | YouTube-origin horizontal MySQL sharding     |
| Linkerd         | Service mesh (control plane)   | Lightweight service mesh alternative to Istio|

### OCI — Open Container Initiative

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Open Container Initiative                                      |
| Entry Type                   | Standards Body (under Linux Foundation)                         |
| Governance Tier              | L5                                                            |
| Founded / Published          | 2015 (Docker donated runC as seed)                            |
| Key Specifications           | OCI Image Spec, OCI Runtime Spec, OCI Distribution Spec        |
| Purpose                      | Ensure container portability — any OCI image runs on any OCI runtime |
| Impact                       | Every Docker/Kubernetes/Podman container uses OCI format       |

| OCI Spec             | Defines                                                       |
|----------------------|---------------------------------------------------------------|
| Image Specification  | Container image format: layers (tar+gzip), manifest, config JSON, index |
| Runtime Specification| Container lifecycle: create → start → exec → kill → delete    |
| Distribution Spec    | Registry API: push, pull, catalog, tags (registry HTTP API v2) |

---

## 6.6 RISC-V International

### RISC-V International

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | RISC-V International                                           |
| Entry Type                   | Body (Open ISA Standards Organization)                         |
| Governance Tier              | L5                                                            |
| Founded / Published          | 2015 (as RISC-V Foundation); relocated to Switzerland 2020     |
| Headquarters                 | Basel, Switzerland (chosen for geopolitical neutrality)        |
| ISA Origin                   | UC Berkeley, 2010 (Krste Asanović, Andrew Waterman, Yunsup Lee) |
| License                      | RISC-V ISA specs are open and royalty-free — anyone can implement |
| Key ISA Extensions           | I (Integer), M (Multiply), A (Atomic), F (Float), D (Double), C (Compressed), V (Vector) |
| Profiles                     | RVA20U64, RVA22U64, RVA23U64 — define minimum for Linux-capable cores |
| Membership                   | 3,600+ members across 70+ countries                            |
| Automotive Relevance         | Growing — SiFive, Andes, Nuclei providing automotive-grade RISC-V cores |
| Embedded Relevance           | Very High — ESP32-C3/C6 (Espressif), CH32V (WCH), GD32VF (GigaDevice) |
| Competing ISAs               | ARM (proprietary license), x86 (Intel/AMD proprietary), MIPS (commercial) |
| Notes                        | RISC-V is NOT a processor — it's an ISA (instruction set architecture). Anyone can design a RISC-V core. |

### RISC-V ISA Extension System

| Extension | Letter | Description                                   | Status      |
|-----------|--------|-----------------------------------------------|-------------|
| Base Integer | I   | 32/64-bit integer instructions (load/store/ALU)| Ratified   |
| Multiply    | M    | Integer multiply/divide                        | Ratified   |
| Atomic      | A    | Atomic memory operations (AMO, LR/SC)         | Ratified   |
| Float (SP)  | F    | Single-precision floating-point                | Ratified   |
| Float (DP)  | D    | Double-precision floating-point                | Ratified   |
| Compressed  | C    | 16-bit compressed instructions (code density) | Ratified   |
| Vector      | V    | SIMD/vector operations (scalable)             | Ratified (1.0) |
| Bit Manip   | B    | Bit manipulation (Zba, Zbb, Zbs)             | Ratified   |
| Crypto      | K    | Scalar cryptography (AES, SHA)               | Ratified   |
| Hypervisor  | H    | Hypervisor support (two-level translation)   | Ratified   |

---

## 6.7 Khronos Group

### Khronos Group

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | The Khronos Group                                              |
| Entry Type                   | Body (Industry Consortium — GPU/Graphics Standards)            |
| Governance Tier              | L3/L5 hybrid (paid membership but community adoption driven)  |
| Founded / Published          | 2000                                                          |
| Headquarters                 | Beaverton, Oregon, USA                                         |
| Primary Domain               | Graphics, GPU compute, vision, XR, machine learning acceleration |
| Membership                   | 170+ companies (NVIDIA, AMD, Intel, Apple, Google, Samsung, ARM, Qualcomm) |
| Specification Availability   | **Free** — all Khronos specs freely available (implementations need conformance tests) |
| IP Policy                    | Royalty-free implementable by anyone; conformance testing required for trademark use |
| Industry Relevance           | Very High — Vulkan/OpenGL/OpenCL on every GPU                 |
| Notes                        | Khronos is technically L3 (paid membership) but specs are free → effectively L5 for implementers |

### Key Khronos Standards

| Standard      | Full Name                                | Purpose                              | Current Version | Status       |
|---------------|------------------------------------------|--------------------------------------|-----------------|--------------|
| OpenGL        | Open Graphics Library                    | Cross-platform 3D graphics API       | 4.6 (2017)      | Maintenance  |
| OpenGL ES     | OpenGL for Embedded Systems              | Mobile/embedded 3D graphics          | 3.2 (2015)      | Active       |
| Vulkan        | Vulkan Graphics and Compute API          | Low-overhead, explicit GPU control   | 1.3+ (2022)     | Active (primary) |
| WebGL         | Web Graphics Library                     | OpenGL ES in web browsers (JS)       | 2.0             | Active       |
| WebGPU        | (W3C — NOT Khronos)                     | Next-gen web GPU API                 | —               | W3C spec     |
| OpenCL        | Open Computing Language                  | Heterogeneous parallel compute       | 3.0 (2020)      | Active       |
| SYCL          | C++ Single-source Heterogeneous          | C++ abstraction over OpenCL/GPU      | 2020 rev.8      | Active       |
| OpenXR        | Open XR (Extended Reality)               | VR/AR headset and controller API     | 1.1 (2023)      | Active       |
| SPIR-V        | Standard Portable Intermediate Repr.     | GPU shader/kernel bytecode format    | 1.6             | Active       |
| glTF          | GL Transmission Format                   | 3D asset interchange ("JPEG of 3D")  | 2.0             | Active       |
| NNEF          | Neural Network Exchange Format           | ML model portability (inference)     | 1.0             | Active       |
| KTX           | Khronos Texture Format                   | GPU-compressed texture container     | 2.0             | Active       |
| EGL           | Native Platform Graphics Interface       | Windowing system binding for GL/VK   | 1.5             | Active       |

### Vulkan vs. OpenGL vs. Metal vs. DirectX

| Attribute        | Vulkan (Khronos)        | OpenGL (Khronos)       | Metal (Apple)           | DirectX 12 (Microsoft) |
|------------------|--------------------------|--------------------------|--------------------------|--------------------------|
| Governance       | Khronos Group            | Khronos Group            | Apple (proprietary)      | Microsoft (proprietary) |
| Level            | Low-level (explicit)     | High-level (implicit)    | Low-level (explicit)     | Low-level (explicit)    |
| Platforms        | Win, Linux, Android, macOS (MoltenVK) | Win, Linux, macOS, mobile | Apple only (iOS/macOS) | Windows/Xbox only     |
| Shader language  | SPIR-V bytecode          | GLSL (text)              | MSL (Metal Shading Lang.)| HLSL → DXIL            |
| Status           | Modern primary API       | Maintenance/legacy       | Active (Apple ecosystem) | Active (MS ecosystem)   |
| Open standard?   | Yes (royalty-free)       | Yes (royalty-free)       | **No** (Apple only)      | **No** (Microsoft only) |

---

## 6.8 OSI — Open Source Initiative

### Open Source Initiative

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Open Source Initiative                                          |
| Abbreviation / Alias         | OSI                                                            |
| Entry Type                   | Body (License Stewardship Organization)                        |
| Governance Tier              | L5                                                            |
| Founded / Published          | 1998 (by Eric S. Raymond and Bruce Perens)                    |
| Function                     | Maintains the Open Source Definition (OSD); approves licenses as "OSI-Approved" |
| Key Output                   | OSI-Approved License list — THE definitive list of what is "open source" |
| Notes                        | OSI does not produce technical standards — it defines what "open source" means legally |

### Open Source Definition (OSD) — 10 Criteria

| # | Criterion                          | Meaning                                          |
|---|-------------------------------------|--------------------------------------------------|
| 1 | Free Redistribution                 | No royalty for redistribution                    |
| 2 | Source Code                         | Must include or easily obtain source              |
| 3 | Derived Works                       | Must allow modifications and derived works        |
| 4 | Integrity of Author's Source Code   | May require patches separate from source         |
| 5 | No Discrimination Against Persons/Groups | License cannot exclude anyone               |
| 6 | No Discrimination Against Fields of Endeavor | Cannot restrict use to specific purpose  |
| 7 | Distribution of License             | Rights apply to all recipients                   |
| 8 | License Must Not Be Specific to a Product | Rights not limited to one distribution   |
| 9 | License Must Not Restrict Other Software | Cannot mandate other software be open     |
| 10| License Must Be Technology-Neutral  | Cannot require specific technology               |

### Key License Families (OSI-Approved)

| License Family   | Examples                    | Type           | Patent Grant    | Copyleft Level  | Typical Use              |
|------------------|-----------------------------|----------------|-----------------|-----------------|--------------------------|
| MIT              | MIT License                 | Permissive     | No explicit     | None            | Small projects, libraries|
| BSD              | BSD-2-Clause, BSD-3-Clause  | Permissive     | No explicit     | None            | FreeBSD, NGINX, OpenBSD  |
| Apache 2.0       | Apache License 2.0          | Permissive     | **Explicit**    | None            | Kubernetes, Kafka, Spark |
| GPL              | GPLv2, GPLv3                | Strong copyleft| GPLv3: Yes      | Strong          | Linux kernel (GPLv2), GCC|
| LGPL             | LGPLv2.1, LGPLv3           | Weak copyleft  | LGPLv3: Yes     | Weak (linking)  | glibc, Qt               |
| AGPL             | AGPLv3                      | Network copyleft| Yes            | Strong + SaaS   | MongoDB (pre-SSPL), GitLab |
| MPL              | MPL 2.0                     | File-level CL  | Yes             | File-level      | Firefox, Rust (dual)     |
| EPL              | EPL 2.0                     | Weak copyleft  | Yes             | Module-level    | Eclipse IDE, Jakarta EE  |

---

## 6.9 OpenSSF — Open Source Security Foundation

### OpenSSF

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Open Source Security Foundation                                 |
| Entry Type                   | Body (Sub-foundation of Linux Foundation)                      |
| Governance Tier              | L5                                                            |
| Founded / Published          | 2020                                                          |
| Mission                      | Improve security of open source software ecosystem             |
| Key Projects                 | Sigstore, SLSA, Scorecards, OpenSSF Best Practices Badge      |
| Industry Relevance           | Very High — supply chain security is critical post-SolarWinds/Log4Shell |

### Key OpenSSF Projects

| Project          | Purpose                                                       |
|------------------|---------------------------------------------------------------|
| Sigstore         | Keyless code signing for open source (cosign, Rekor, Fulcio) |
| SLSA (Supply-chain Levels for Software Artifacts) | Framework for supply chain integrity (Level 1–4) |
| Scorecards       | Automated security health checks for open source projects     |
| Best Practices Badge | Criteria for open source project security maturity       |
| Alpha-Omega      | Fund critical project security improvements                   |
| Package Analysis | Detect malicious packages in registries (npm, PyPI)           |

---

## 6.10 Language/Runtime Foundations

| Foundation               | Language/Runtime  | Governance Model       | License        | Key Outputs                   |
|--------------------------|-------------------|------------------------|----------------|-------------------------------|
| Python Software Foundation| Python / CPython | Steering Council (5)   | PSF License 2  | Python 3.x, PEP process       |
| Rust Foundation          | Rust              | Board + Project teams  | MIT/Apache 2.0 | Rust compiler, Cargo, crates.io|
| OpenJS Foundation (LF)   | Node.js + ecosystem| Cross-project TSC    | Various (MIT)  | Node.js, jQuery, webpack, Electron |
| PHP Foundation           | PHP               | Funded secretariat     | PHP License 3  | PHP 8.x interpreter           |
| Swift.org (Apple-led)    | Swift             | Core Team (Apple-led)  | Apache 2.0     | Swift compiler + stdlib        |
| Go (Google-led)          | Go                | Core Team (Google)     | BSD 3-Clause   | Go compiler + stdlib           |
| .NET Foundation          | .NET              | Board (Microsoft-heavy)| MIT            | .NET runtime, ASP.NET Core     |
| Ruby (Matz)              | Ruby              | BDFL (Matz)            | BSD 2-Clause   | CRuby/MRI interpreter          |

---

## 6.11 Foundation Comparison Matrix

| Foundation       | Tier | Founded | IP Model    | Default License | Project Governance | Key Domain                |
|------------------|------|---------|-------------|-----------------|--------------------|-----------------------------|
| Linux Foundation | L5   | 2007    | DCO/CLA     | Various         | TSC per project    | Linux, cloud, auto, embedded |
| Apache SF        | L5   | 1999    | CLA → ASF   | Apache 2.0      | PMC per project    | Data, web, integration       |
| Eclipse Fdn      | L5   | 2004    | CLA + IP rev| EPL 2.0         | PMC + WG           | IDE, IoT, Jakarta EE, SDV    |
| CNCF             | L5   | 2015    | DCO/CLA     | Apache 2.0      | TOC + project TSC  | Cloud native, containers     |
| RISC-V Intl      | L5   | 2015    | Open ISA    | CC-BY-4.0 (spec)| Technical WGs      | Processor ISA                |
| Khronos Group    | L3   | 2000    | CLA         | RF (specs)      | Working groups     | Graphics, compute, XR        |
| OSI              | L5   | 1998    | N/A         | N/A             | Board              | License stewardship          |
| OpenSSF          | L5   | 2020    | DCO         | Apache 2.0      | Working groups     | Supply chain security        |
| FSF/GNU          | L5   | 1985    | Copyright assign | GPL family | Board (FSF)        | Copyleft advocacy, GNU tools |

---

## 6.12 Key Disambiguation Notes

### 1. Linux Foundation vs. Linux Kernel

- **Linux Foundation** = non-profit organization hosting many projects (not just Linux)
- **Linux kernel** = one project under LF; maintained by Linus Torvalds + subsystem maintainers
- LF employs kernel developers but does NOT control kernel technical decisions

### 2. CNCF Graduated vs. Incubating — What It Means

- **Graduated** = production-proven, diverse maintainers, security audit passed, stable governance
- **Incubating** = growing, has users, meets basic governance but not yet fully proven
- Common misconception: "incubating = not ready for production" — FALSE. Many incubating projects (like Cilium before graduation) are heavily used in production.

### 3. OCI Image vs. Docker Image

- **OCI Image** = the open standard format (manifest v2 schema 2 → OCI image spec)
- **Docker Image** = historically Docker's format; now essentially OCI-compatible
- In practice: Docker images ARE OCI images (Docker adopted OCI format since Docker 1.10+)
- The formats are interchangeable for practical purposes

### 4. Apache License 2.0 vs. MIT vs. GPLv2

| Scenario                          | Apache 2.0           | MIT                | GPLv2              |
|-----------------------------------|----------------------|--------------------|--------------------|
| Can I use in proprietary product? | Yes                  | Yes                | Derivative must be GPL |
| Patent protection?                | Yes (explicit grant) | No (risky)         | Implicit in GPLv2; explicit GPLv3 |
| Attribution required?             | Yes (NOTICE file)    | Yes (copyright)    | Yes + source       |
| Can I relicense?                  | Yes (permissive)     | Yes (permissive)   | No (copyleft)      |

### 5. Khronos Vulkan vs. W3C WebGPU

- **Vulkan** = native GPU API (C-based, hardware-near, runs on OS directly)
- **WebGPU** = web browser GPU API (JavaScript/WASM, runs in browser sandbox)
- **WebGPU is NOT "Vulkan in the browser"** — it's a separate API designed for web security model
- WebGPU implementations use Vulkan/Metal/D3D12 as backends but expose a different abstraction

---

*End of Chapter 6 — Foundation & Community Standards Bodies (L5)*
*Next: Chapter 7 — Vendor-Led Open Standards*
