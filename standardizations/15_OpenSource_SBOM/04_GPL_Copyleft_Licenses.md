# GPL & Copyleft Licenses — Deep Dive

**Topic:** GNU General Public License (GPL) family and Copyleft licensing mechanisms — GPL v2, GPL v3, AGPL v3, LGPL 2.1/3.0, MPL 2.0, EUPL, copyleft mechanics, derivative works, linking, compatibility  
**Standard:** GPL-2.0-only (1991); GPL-3.0-only (2007); AGPL-3.0-only (2007); LGPL-2.1-only (1999); LGPL-3.0-only (2007); MPL-2.0 (2012); EUPL-1.2 (2017)  
**SDO:** Free Software Foundation (FSF) — GPL, AGPL, LGPL; Mozilla Foundation — MPL; European Commission — EUPL  
**Audience:** Software engineers, embedded developers, open source compliance officers, OSPO managers, legal counsel, product managers, system architects  
**Prerequisites:** Software development, compilation/linking concepts, software distribution models, open source basics, copyright law fundamentals

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 1983 | GNU Project announced (Richard Stallman) | Vision of a completely free operating system; need for a license to protect software freedom |
| 1985 | Free Software Foundation (FSF) founded | Organization to promote free software; steward of GPL |
| 1989 | **GPL v1** published | First copyleft license; ensures source code availability; prevents proprietary lock-up |
| 1991 | **GPL v2** published | Major revision; "Liberty or Death" clause; anti-patent provision; widely adopted (Linux kernel uses GPL-2.0-only) |
| 1991 | **LGPL v2.0** (Library GPL) | Weaker copyleft for libraries; allows proprietary programs to link against LGPL libraries without becoming GPL themselves |
| 1999 | **LGPL v2.1** | Updated library/Lesser GPL; clarified linking semantics; widely used for GNU C Library (glibc), Qt (initially) |
| 2004 | GPLv3 drafting process begins | Response to: Tivoization (TiVo hardware locked despite GPL source); patent threats; DRM; internationalization needs |
| 2007 | **GPL v3** published | Anti-Tivoization (installation information requirement); explicit patent grant; anti-DRM; better internationalization; compatibility improvements |
| 2007 | **AGPL v3** published | "Network copyleft" — closes the "ASP loophole": if you modify AGPL software and provide it as a network service, you must share source (even without distribution) |
| 2007 | **LGPL v3** published | Reformulated as "GPL v3 with additional permissions" (not standalone); allows proprietary linking; inherits GPLv3 patent/Tivoization terms |
| 2012 | **MPL 2.0** published (Mozilla) | File-level copyleft (not project-level); modern; compatible with GPL/LGPL/AGPL; business-friendly "weak copyleft" |
| 2017 | **EUPL 1.2** | European Union Public License; multilingual; compatible with many licenses; interoperability clause |

### 1.2 The Four Freedoms (Foundation of Copyleft)

| Freedom | Description | Copyleft Ensures |
|:-------:|-------------|:---:|
| **Freedom 0** | Run the program for any purpose | Unrestricted use |
| **Freedom 1** | Study and modify the source code | Source must be available |
| **Freedom 2** | Redistribute copies | Can share freely |
| **Freedom 3** | Distribute modified versions | Modifications must also be free (copyleft enforcement) |

### 1.3 What "Copyleft" Means

| Concept | Explanation |
|---------|-------------|
| **Copyright** | Legal right of creator to control reproduction/distribution of their work |
| **Copyleft** | Uses copyright law to REQUIRE that derivative works maintain the same freedoms as the original; "share-alike" |
| **Mechanism** | License grants freedoms BUT requires: if you distribute modified versions, you must license them under the SAME copyleft license and provide source code |
| **Goal** | Prevent "proprietary capture" — no one can take free software, modify it, and distribute the result as proprietary |
| **Contrast** | Permissive licenses (MIT, BSD, Apache) allow proprietary derivatives; copyleft prevents this |

---

## Chapter 2 — License Architecture & Mechanics

### 2.1 Copyleft Strength Spectrum

```mermaid
graph LR
    subgraph "Copyleft Strength"
        STRONG[Strong Copyleft<br/>━━━━━━━━━━━<br/>GPL v2, GPL v3<br/>━━━━━━━━━━━<br/>Entire derivative work<br/>must be GPL-licensed<br/>if distributed]
        
        NETWORK[Network Copyleft<br/>━━━━━━━━━━━<br/>AGPL v3<br/>━━━━━━━━━━━<br/>Strong copyleft PLUS<br/>network use triggers<br/>source sharing<br/>(no distribution needed)]
        
        WEAK[Weak Copyleft<br/>━━━━━━━━━━━<br/>LGPL 2.1/3.0<br/>━━━━━━━━━━━<br/>Library itself must stay<br/>LGPL; but programs<br/>linking to it can be<br/>any license]
        
        FILE[File-Level Copyleft<br/>━━━━━━━━━━━<br/>MPL 2.0<br/>━━━━━━━━━━━<br/>Modified FILES must<br/>stay MPL; new files<br/>can be any license;<br/>project can mix]
        
        PERM[No Copyleft<br/>━━━━━━━━━━━<br/>MIT, BSD, Apache<br/>━━━━━━━━━━━<br/>No copyleft obligation<br/>Derivatives can be<br/>proprietary]
    end
    
    NETWORK --> STRONG --> WEAK --> FILE --> PERM
```

### 2.2 GPL v2 vs GPL v3 Key Differences

| Feature | GPL v2 (1991) | GPL v3 (2007) |
|---------|:---:|:---:|
| **Tivoization** | Not addressed; hardware can lock out modified software | **Anti-Tivoization**: must provide "Installation Information" — whatever is needed to install modified versions on the hardware (keys, procedures) |
| **Patent** | Implicit patent grant (debated); "Liberty or Death" clause as defense | **Explicit patent grant**: every contributor grants patent license; if you sue for patent infringement, your license terminates |
| **DRM** | Not addressed | **Anti-DRM**: GPL'd code's DRM measures cannot prevent exercise of freedoms; if DRM is used, must provide keys/information to bypass for modification |
| **Compatibility** | Limited (not compatible with Apache-2.0) | **Improved**: explicitly compatible with Apache-2.0; AGPLv3; broader compatibility |
| **Internationalization** | US copyright law centric language | **International**: uses "convey" (broader than "distribute"); works across jurisdictions |
| **"Or later"** | Common: "GPL v2 or later" (allows upgrading) | "GPL v3 or later" (but v2-only cannot be upgraded — e.g., Linux kernel) |
| **Termination** | Immediate and permanent on violation (harsh) | **Cure provision**: 30-day cure period for first-time violations; automatic reinstatement if fixed within 30 days of notice |
| **Notable users** | Linux kernel (v2-only); BusyBox | GCC; GNOME; Samba; Bash; many GNU utilities |

### 2.3 Key Copyleft Licenses Comparison

| License | Copyleft Type | Trigger | Source Obligation | Patent | Notable Users |
|---------|:---:|:---:|:---:|:---:|------|
| **GPL-2.0** | Strong | Distribution of derivative work | Provide complete corresponding source | Implicit (Liberty or Death) | Linux kernel; Git; BusyBox |
| **GPL-3.0** | Strong | Conveyance (distribution) of derivative work | Provide complete corresponding source + installation info | Explicit patent grant | GCC; Bash; GIMP; Samba |
| **AGPL-3.0** | Network (strongest) | Distribution OR making available over network (SaaS) | Source for network-served modified versions | Explicit (same as GPLv3) | MongoDB (formerly); Nextcloud; Grafana |
| **LGPL-2.1** | Weak | Distribution of the library itself (modifications to it) | Source for library modifications; NOT for proprietary code linking to it | Similar to GPL v2 | glibc; GTK+; FFmpeg (parts) |
| **LGPL-3.0** | Weak | Distribution of the library; defined as "GPL-3.0 + additional permissions" | Source for library modifications; reverse engineering right for debugging | Explicit (inherits GPLv3) | Qt (current); GStreamer |
| **MPL-2.0** | File-level | Distribution of modified MPL-covered files | Source for modified MPL files only; new files can be any license | Explicit patent grant | Firefox; Thunderbird; Terraform; LibreOffice |
| **EUPL-1.2** | Strong (with interoperability) | Distribution of derivative | Source + copyleft; BUT compatibility clause allows combining with GPL/LGPL/AGPL/MPL/OSL/CeCILL/EPL | Explicit | EU institutional software; some government projects |

---

## Chapter 3 — Technical Deep Dive: Derivative Works & Linking

### 3.1 What Constitutes a "Derivative Work" Under GPL?

| Interaction | Likely Derivative? | Reasoning | Practical Impact |
|:-----------:|:---:|---|---|
| Modify GPL source code and distribute | **YES** | Clear modification of copyrighted work; explicit GPL trigger | Modified code must be GPL; source provided |
| Copy GPL code into your source file | **YES** | Incorporation; your file now contains copyrighted GPL code | Your file becomes GPL-obligated |
| **Static linking** with GPL library | **YES** (strong consensus) | Binary contains GPL code combined with yours; forms single work | Entire binary must be GPL-licensed |
| **Dynamic linking** with GPL library | **Debated** (FSF says YES; others disagree) | FSF position: dynamic linking creates derivative work because intimate API relationship; others argue separate works merely aggregated | Contentious; FSF enforces as derivative; some courts haven't fully decided |
| **Dynamic linking** with LGPL library | **NO** (explicitly permitted) | LGPL specifically allows proprietary programs to dynamically link; this is LGPL's purpose | Your code stays proprietary; LGPL library modifications must be shared |
| **System call / IPC** to GPL program | **NO** (generally) | Separate processes communicating via standard interfaces; "mere aggregation" | Your program remains separate; not a derivative |
| Using GPL command-line tool in build process | **NO** | Tool processes your code but output is yours; tool not incorporated | Build tools (GCC, Make) don't infect output |
| GPL plugin loaded into proprietary host | **Debated** | Depends on: shared memory? API intimacy? Can plugin exist without host? | Case-by-case; FSF tends toward "yes"; courts inconsistent |
| Combining GPL code with proprietary in same repository | **Only if distributed as combined work** | Aggregation in repo ≠ derivative; distribution of combined binary = derivative | Keep components separately buildable/distributable |

### 3.2 Static vs. Dynamic Linking Under Copyleft

| Aspect | Static Linking | Dynamic Linking |
|--------|:---:|:---:|
| **Mechanism** | Library code copied into executable at compile/link time | Library loaded at runtime; executable has reference only |
| **GPL impact** | Almost universally considered derivative work; binary contains GPL code | FSF says derivative work; industry practice varies; some disagree |
| **LGPL impact** | LGPL allows static linking IF: (1) you provide object files allowing re-linking; OR (2) you use shared/dynamic linking | Explicitly permitted; primary use case for LGPL |
| **Practical guidance** | Avoid statically linking GPL libraries into proprietary code | For LGPL: prefer dynamic linking (simpler compliance); for GPL: either way creates obligations |
| **Embedded systems** | Common in embedded (no dynamic linker); must use LGPL's object-file provision if needed | Less common in deeply embedded; more common in Linux-based embedded |

### 3.3 Tivoization (GPL v3 Anti-Circumvention)

| Concept | Explanation |
|---------|-------------|
| **What is Tivoization?** | Named after TiVo: device runs GPL'd Linux; source code IS provided (GPL v2 compliant); BUT hardware uses cryptographic signature verification — only TiVo-signed binaries will run; user CANNOT install modified versions despite having source |
| **GPL v2 view** | Compliant (source is provided); hardware restrictions are separate from software license | 
| **GPL v3 view** | VIOLATION of spirit: user has source but cannot exercise Freedom 3 (distribute modified versions that actually run); defeats the purpose |
| **GPL v3 solution** | Section 6 requires "Installation Information": "all information necessary to install and execute modified versions" including signing keys, procedures, authorization codes — whatever the manufacturer uses to install their own versions |
| **Exceptions** | Installation Information NOT required when: (1) hardware does not allow ANYONE (including manufacturer) to install modified versions (ROM); (2) product is not "User Product" (industrial/non-consumer equipment has less clear obligation) |
| **Impact on embedded** | Major concern for consumer electronics; automotive ECUs; IoT devices using GPLv3 code; must either provide installation info or avoid GPLv3 code |

### 3.4 AGPL — The Network Clause

| Scenario | GPL v3 | AGPL v3 |
|:--------:|:---:|:---:|
| Modify code and distribute binary | Must provide source | Must provide source (same) |
| Modify code; run as SaaS; NO distribution | **No obligation** (no distribution = no trigger) | **MUST provide source** (Section 13: interacting with modified version over network = trigger) |
| Fork AGPL project; add features; run as internal service | **No obligation** (internal use only) | **MUST provide source to users** who interact over network |
| Use AGPL library in your web application | N/A | Your web app may be considered derivative (users interact with AGPL code over network); source obligation triggers |

---

## Chapter 4 — Implementation Guide: GPL Compliance

### 4.1 GPL Compliance Requirements

| Obligation | GPL v2 | GPL v3 | How to Comply |
|:-----------:|:---:|:---:|---|
| **Source code offer** | Yes (Section 3) | Yes (Section 6) | Written offer valid 3 years to provide complete corresponding source; OR accompany distribution with source |
| **License notice** | Yes | Yes | Include copy of GPL text; copyright notices; "NO WARRANTY" disclaimer |
| **Complete Corresponding Source** | All source code to generate/build the software | Same + scripts to control compilation and installation | Source code + build scripts + Makefiles + patch files + everything needed to reproduce |
| **Installation Information** | NOT required | Required for User Products | Keys; signing procedures; instructions to install modified version on hardware |
| **Notice of modification** | Yes | Yes | Clearly state that you modified the work; date of modification; carry original notices |
| **No additional restrictions** | Yes (Section 6) | Yes (Section 7) | Cannot add restrictions (NDA, patent fees) beyond what GPL allows |

### 4.2 Complete Corresponding Source (CCS) Checklist

```
COMPLETE CORRESPONDING SOURCE must include:

□ All source code files (modified and unmodified)
□ Build system files (Makefiles, CMakeLists.txt, build.gradle, etc.)
□ Configuration scripts (./configure, autotools, CMake scripts)
□ Patch files (all patches applied to upstream source)
□ Compiler/toolchain SPECIFICATIONS (not necessarily the compiler itself;
  but enough info to obtain equivalent toolchain)
□ Kernel configuration (.config for Linux builds)
□ Package management files (recipes for Yocto/Buildroot/OpenWrt)
□ Any scripts used to control compilation and installation
□ Interface definition files (if needed for modification)

□ DOES NOT need to include:
  - System libraries (standard C library of target OS)
  - Generic tools available to the public (standard GCC if unmodified)
  - The hardware itself
```

### 4.3 GPL Compliance for Embedded Products (Yocto/Linux)

| Step | Action | Tool/Method |
|:----:|--------|-------------|
| 1 | Identify all GPL/LGPL components in firmware | SBOM generation (SPDX from Yocto); Scancode; FOSSology |
| 2 | Separate strong-copyleft from weak-copyleft | Categorize: GPL (full source + CCS); LGPL (library source + re-linking ability); others |
| 3 | Generate Complete Corresponding Source | Yocto: `archiver.bbclass` generates source archives; `INHERIT += "archiver"` |
| 4 | Verify buildability | Build from CCS archive independently (CI job that builds from source archive) |
| 5 | Prepare written offer (or bundle source) | Method: (a) ship source on physical media with product; (b) written offer (3-year validity); (c) provide download link |
| 6 | Handle LGPL components | For dynamic linking: ensure shared libraries are replaceable (not statically linked into ROM without re-link option) |
| 7 | Handle GPLv3 components (if any) | Provide Installation Information for User Products; or eliminate GPLv3 components from consumer product firmware |
| 8 | License notices | Include full license texts; copyright notices; modification notices; in accessible location (about screen; documentation; download) |
| 9 | Ongoing maintenance | Source offer valid 3 years after last distribution; maintain archive; update SBOM |

---

## Chapter 5 — License Compatibility

### 5.1 GPL Compatibility Matrix

| License | GPL-2.0 Compatible? | GPL-3.0 Compatible? | Notes |
|---------|:---:|:---:|------|
| **MIT** | ✅ Yes | ✅ Yes | Permissive; can be combined into GPL work |
| **BSD-2-Clause** | ✅ Yes | ✅ Yes | Permissive; compatible |
| **BSD-3-Clause** | ✅ Yes | ✅ Yes | Permissive; compatible |
| **Apache-2.0** | ❌ **No** (v2 incompatible) | ✅ Yes (v3 compatible) | Apache-2.0 patent termination clause conflicts with GPL-2.0; explicitly fixed in GPL-3.0 |
| **LGPL-2.1** | ✅ Yes (can upgrade to GPL) | ✅ Yes | LGPL is "weaker GPL"; can always be treated as full GPL |
| **MPL-2.0** | ✅ Yes (Section 3.3) | ✅ Yes (Section 3.3) | MPL has explicit "Larger Work" compatibility clause |
| **AGPL-3.0** | ❌ No (with GPL-2.0) | ✅ Yes (with GPL-3.0, Section 13) | AGPL is "GPL-3.0 + network clause"; GPL-3.0 code can be in AGPL project |
| **EPL-2.0** | ⚠️ Only with Secondary License option | ⚠️ Only with Secondary License option | EPL-2.0 has optional "Secondary License" clause enabling GPL compatibility |
| **CDDL-1.0** | ❌ No | ❌ No | Incompatible with GPL (per FSF); OpenSolaris/ZFS licensing conflict |
| **GPL-2.0-only** | N/A | ❌ Cannot upgrade | "Only" means exactly v2; cannot combine with GPL-3.0-only code |
| **GPL-2.0-or-later** | ✅ Is GPL-2.0 | ✅ Can be used as GPL-3.0 | "Or later" allows treating as v3; enables combination |

### 5.2 The "GPL-2.0-only" vs "GPL-2.0-or-later" Problem

| Expression | Meaning | Can Combine with GPL-3.0? | Example |
|:---:|---|:---:|---|
| `GPL-2.0-only` | Exactly GPL version 2; no later | ❌ NO | Linux kernel (Torvalds chose v2-only) |
| `GPL-2.0-or-later` | GPL v2 OR any later version (user/recipient chooses) | ✅ YES (choose v3) | Many GNU utilities; GCC |
| `GPL-3.0-only` | Exactly GPL version 3 | Only with GPL-3.0+ code | Some newer projects |
| `GPL-3.0-or-later` | GPL v3 or any later version | ✅ YES (future versions) | Some newer projects |

---

## Chapter 6 — Domain-Specific Copyleft Considerations

### 6.1 Copyleft in Automotive (AOSP/Linux)

| Component | Typical License | Compliance Approach |
|-----------|:---:|---|
| Linux kernel | GPL-2.0-only | Provide kernel source + config + patches; NO Tivoization concern (v2; no installation info required) |
| U-Boot | GPL-2.0-or-later | Source + patches; could use as GPL-3.0 but typically distributed as v2 |
| BusyBox | GPL-2.0-only | Source for all included applets; complete build configuration |
| GStreamer | LGPL-2.1-or-later | Dynamic linking from proprietary player: OK; modifications to GStreamer itself: share |
| FFmpeg | Mixed (LGPL-2.1 / GPL-2.0) | Depends on configure flags: `--enable-gpl` enables GPL codecs; without: LGPL base only |
| Automotive Grade Linux (AGL) | Mixed | Platform components: various copyleft; applications: may be proprietary; clear separation needed |
| Android (AOSP) | Apache-2.0 (userspace); GPL-2.0 (kernel) | Google designed Android userspace as Apache-2.0 to allow proprietary apps; kernel stays GPL |

### 6.2 Copyleft in Cloud/SaaS

| Scenario | License Impact | Risk |
|----------|:---:|---|
| Run GPL software internally (never distribute) | No distribution = **no GPL obligation** triggered | Low (internal use only) |
| Run GPL software as SaaS (users access over network) | No distribution = **no GPL obligation** (for GPL v2/v3) | Low for GPL; AGPL is different |
| Run AGPL software as SaaS | Section 13 triggered: **must provide source to network users** | HIGH — if modified AGPL code, must share modifications |
| Modify AGPL MongoDB and run as a service | Must provide ALL modifications to users | This is why MongoDB relicensed to SSPL (even stronger than AGPL) |
| Use AGPL library in your proprietary SaaS | Depends on how integrated; if users interact with AGPL code → source obligation | Careful architecture needed to isolate AGPL components |

---

## Chapter 7 — Comparison of Copyleft Licenses

### 7.1 Detailed Feature Comparison

| Feature | GPL v2 | GPL v3 | AGPL v3 | LGPL 2.1 | LGPL 3.0 | MPL 2.0 |
|---------|:---:|:---:|:---:|:---:|:---:|:---:|
| Copyleft strength | Strong | Strong | Strongest | Weak | Weak | File-level |
| Network trigger | No | No | **Yes** | No | No | No |
| Anti-Tivoization | No | **Yes** | Yes | No | Yes | No |
| Explicit patent grant | No | **Yes** | Yes | No | Yes | Yes |
| Cure provision (30-day) | No | **Yes** | Yes | No | Yes | No |
| Compatible with Apache-2.0 | No | **Yes** | Yes | No | Yes | Yes |
| Proprietary linking allowed | No | No | No | **Yes** (dynamic) | **Yes** (dynamic) | **Yes** (separate files) |
| Installation info required | No | **Yes** (User Products) | Yes | No | Yes | No |
| "Or later" common | Yes | Yes | Less common | Yes | Yes | N/A (single version) |

### 7.2 When to Choose Which License (as Project Author)

| Goal | Recommended License | Reasoning |
|------|:---:|---|
| Maximum freedom protection; ensure all derivatives stay open | **GPL-3.0-or-later** | Strong copyleft; modern protections; patent grant; Tivoization prevention |
| Prevent proprietary SaaS competitors | **AGPL-3.0** | Network clause prevents "open source but hosted proprietary" model |
| Allow use in proprietary apps (encourage adoption) but keep library improvements open | **LGPL-3.0** or **MPL-2.0** | LGPL: library-level copyleft; MPL: file-level (even less restrictive) |
| Maximum compatibility while maintaining some copyleft | **MPL-2.0** | File-level; explicitly compatible with GPL/LGPL/AGPL; business-friendly |
| EU-centric project; multilingual legal text | **EUPL-1.2** | Available in 23 EU languages; interoperability clause for compatibility |
| Linux kernel contribution | **GPL-2.0-only** | Kernel is GPL-2.0-only; contributions must match |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 GPL Compliance Decision Tree

```mermaid
graph TB
    START[You want to use GPL-licensed code]
    
    Q1{Are you distributing<br/>the software?}
    Q2{Is it a derivative work?<br/>(modification; static link;<br/>intimate integration)}
    Q3{Is it GPL-2.0 or GPL-3.0?}
    Q4{Is your product a<br/>"User Product"?}
    
    A1[No GPL obligations.<br/>Internal use is free.<br/>No source sharing needed.]
    A2[Not a derivative work.<br/>Your code stays proprietary.<br/>"Mere aggregation" is OK.]
    A3[GPL-2.0: Provide source.<br/>No installation info needed.<br/>Written offer or accompany.]
    A4[GPL-3.0: Provide source<br/>+ Installation Information<br/>(keys, scripts, procedures)]
    A5[GPL-3.0: Provide source.<br/>No installation info<br/>(not consumer device).]
    
    START --> Q1
    Q1 -->|No| A1
    Q1 -->|Yes| Q2
    Q2 -->|No| A2
    Q2 -->|Yes| Q3
    Q3 -->|GPL-2.0| A3
    Q3 -->|GPL-3.0| Q4
    Q4 -->|Yes (consumer device)| A4
    Q4 -->|No (server/industrial)| A5
```

### 8.2 Copyleft Boundary Analysis

```mermaid
graph TB
    subgraph "GPL Boundary (Derivative Work)"
        GPL_CODE[GPL Library<br/>(source code)]
        YOUR_MOD[Your Modifications<br/>to GPL code]
        STATIC[Statically Linked<br/>Proprietary Code]
        
        GPL_CODE --> |"must be GPL"| YOUR_MOD
        STATIC --> |"becomes GPL-obligated"| GPL_CODE
    end
    
    subgraph "LGPL Boundary"
        LGPL_LIB[LGPL Library<br/>(shared object .so)]
        LGPL_MOD[Your Modifications<br/>to LGPL library code]
        PROP_APP[Your Proprietary App<br/>(dynamically links)]
        
        LGPL_LIB --> |"must be LGPL"| LGPL_MOD
        PROP_APP -.-> |"allowed! stays proprietary"| LGPL_LIB
    end
    
    subgraph "MPL 2.0 Boundary"
        MPL_FILE[MPL-covered file.c<br/>(modified)]
        NEW_FILE[Your new_file.c<br/>(any license)]
        PROJECT[Combined Project<br/>(mixed licenses OK)]
        
        MPL_FILE --> |"must stay MPL"| PROJECT
        NEW_FILE --> |"your license"| PROJECT
    end
    
    subgraph "Safe Zone (No Copyleft Infection)"
        SEPARATE[Separate Process<br/>(IPC / CLI / pipes)]
        API[REST API / Network<br/>(except AGPL)]
        KERNEL[Kernel Syscalls<br/>(not derivative of kernel)]
    end
```

---

## Chapter 9 — Case Studies

### 9.1 Case Study: BusyBox GPL Enforcement

| Aspect | Detail |
|--------|--------|
| Background | BusyBox is a GPL-2.0-only utility suite used in virtually every embedded Linux device (routers, TVs, appliances, IoT); provides shell, file utils, networking in single binary |
| Violations | Many device manufacturers shipped products containing BusyBox without providing source code; violated GPL-2.0 Section 3 |
| Enforcement | Software Freedom Conservancy (on behalf of BusyBox developers) filed multiple lawsuits (2007-2011) against: Monsoon Multimedia, Xterasys, High-Gain Antennas, Verizon, Best Buy, Samsung, Westinghouse, others |
| Typical violation pattern | (1) Ship router/TV/device with Linux + BusyBox; (2) Provide no source code; (3) No written offer for source; (4) When asked, provide incomplete source (missing patches, build scripts) |
| Resolution | Most settled with: (1) Provide Complete Corresponding Source on website; (2) Include written offer in product packaging; (3) Some monetary damages/donation; (4) Appoint open source compliance officer |
| Lessons | (1) GPL enforcement IS real; (2) "Complete" means truly complete (include build scripts, configs, patches); (3) Written offer must be included PROACTIVELY; (4) Establish process BEFORE shipping product; (5) CCS must actually build to the shipped binary |

### 9.2 Case Study: Automotive Tier-1 GPL Compliance Program

| Aspect | Detail |
|--------|--------|
| Company | Tier-1 automotive supplier; ships 20+ ECU products with Linux-based software to 5 OEM customers; ~500 developers |
| Challenge | OEM customers increasingly requesting proof of GPL compliance; one OEM threatened to delist supplier if compliance not demonstrated; legacy products had incomplete source archives |
| Solution | (1) **SBOM-first approach**: generated SPDX SBOM for every ECU firmware image using Yocto `create-spdx` class; identified all GPL/LGPL components automatically. (2) **Automated CCS generation**: Yocto `archiver.bbclass` generates source archive per build; integrated into CI/CD (source archive built alongside every firmware release). (3) **Verification pipeline**: added CI job that builds firmware from source archive (without network access) — verifies CCS is truly complete and buildable. (4) **LGPL handling**: audited all LGPL usage; confirmed dynamic linking only (no static linking of glibc or other LGPL components); documented linking method per component. (5) **GPLv3 exclusion policy**: for consumer products (IVI systems), company policy prohibits GPLv3 components (to avoid installation information requirement); automated policy check in CI (FOSSA blocks GPLv3 for "consumer" product category). (6) **Written offer**: standardized written offer text included in every product's user documentation and on company website (valid 3 years). (7) **Source portal**: customer-facing portal hosts source archives per product version; OEM and end-user downloadable. (8) **Training**: mandatory 4-hour training for all developers on GPL/LGPL mechanics; "license review" required for new dependency introduction. |
| Outcome | Full compliance achieved across 20 products within 6 months; OEM satisfied; zero GPL complaints; process now standard part of product development lifecycle; compliance cost: ~$200K initial setup + $50K/year ongoing (tooling, personnel). |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **GPL enforcement continues** | Ongoing | Software Freedom Conservancy, FSF, and others continue enforcement; companies must maintain compliance |
| **AGPL growth in SaaS era** | 2024+ | More projects choosing AGPL to prevent "cloud exploitation" (take open source; sell as SaaS; contribute nothing back) |
| **"Source Available" alternatives** | 2020-2025 | Companies creating non-OSI-approved licenses (SSPL, BSL, Elastic License) to restrict SaaS competitors; community debate about "open source" definition |
| **Copyleft and AI** | 2024-2027 | Unresolved: if AI model is trained on GPL code, is the model a "derivative work"? Is AI-generated code from GPL training data copyleft-obligated? |
| **SBOM enabling compliance** | 2024+ | SBOM (SPDX/CycloneDX) makes license inventory automated; easier to identify and track copyleft obligations; SBOM-driven compliance workflows |
| **EU CRA and copyleft** | 2025-2027 | EU CRA requires vulnerability handling for ALL components including copyleft; creates new obligations for copyleft component maintainers and consumers |
| **License compatibility improvements** | Ongoing | More licenses adding explicit compatibility clauses (like MPL-2.0, EUPL-1.2); reducing incompatibility friction |
| **Embedded systems and GPLv3** | Ongoing | Tension between GPLv3 installation-info requirement and embedded security (secure boot); industry practice evolving |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is copyleft and how does GPL differ from MIT license?  
**A:** Copyleft is a licensing strategy that uses copyright law to ensure that derivatives of free software remain free. GPL is a copyleft license: if you modify GPL'd code and DISTRIBUTE it, your modifications must also be GPL-licensed and source code must be provided. MIT is permissive (no copyleft): you can take MIT code, modify it, and distribute even as proprietary/closed-source — you just need to include the MIT copyright notice. Key difference: GPL preserves freedom downstream (prevents proprietary capture); MIT maximizes freedom for the immediate recipient (they can do anything including making it proprietary). Practical example: Company takes MIT library → can ship in closed-source product (just include notice). Company takes GPL library → if they modify and ship it, must provide source code for their modifications too.

### Tier 2: Mid-Level

**Q2:** Explain the difference between static and dynamic linking in the context of LGPL compliance, and how you'd handle LGPL in an embedded system with no dynamic linker.  
**A:** [Detailed answer covering: LGPL's purpose (allow proprietary programs to use the library while keeping library improvements open); dynamic linking as the simple path (proprietary app links to .so at runtime; LGPL satisfied because user can replace the .so with modified version); static linking complication (binary contains LGPL code; to comply: must provide object files or source for your proprietary code so user can re-link against modified LGPL library — LGPL 2.1 Section 6); embedded systems without dynamic linker (must use the object-file approach OR switch to a non-LGPL alternative); practical solutions (use shared libraries where possible even in embedded Linux; for bare-metal: avoid LGPL or use object-file provision; consider board support packages that provide link-time separation); example Yocto approach (ensure recipe builds shared library; proprietary app links dynamically).]

### Tier 3: Senior

**Q3:** Your company is building a consumer IoT device using Linux kernel, several GPLv3 components (GCC runtime libraries, Bash, some utilities), and proprietary application firmware. The device uses secure boot with hardware-fused keys. How do you architect the system for GPL compliance while maintaining device security?  
**A:** [Comprehensive answer covering: (1) GPLv3 installation-info analysis — consumer IoT is "User Product"; must provide installation information for GPLv3 components; secure boot with hardware-fused keys is the classic Tivoization problem. (2) Architecture options: (a) Eliminate GPLv3 components entirely (replace Bash with BusyBox/ash under GPL-2.0-only; use musl instead of glibc if LGPL-3.0 is concern; avoid GCC runtime if possible — use LLVM/clang which is Apache-2.0); (b) Partition the system: secure boot validates a "root of trust" partition (proprietary, no GPL); GPLv3 components in a user-modifiable partition (not signature-verified, or keys provided); (c) Provide installation information (signing keys or process to sign modified binaries); requires risk acceptance and security controls. (3) Linux kernel is GPL-2.0-only: no installation-info requirement; secure boot for kernel is fine from licensing perspective. (4) Recommendation for IoT: eliminate GPLv3 from secure boot chain; use GPL-2.0-only alternatives; keep GPLv3 (if needed) in modifiable userspace partition; document architecture decision in compliance documentation; get legal sign-off. (5) Long-term: monitor dependency updates that might introduce GPLv3; automated license policy enforcement in CI.]

---

## Chapter 12 — Cheat Sheet & Quick Reference

### GPL Family Quick Reference

```
GPL-2.0-only
  Trigger: DISTRIBUTION of derivative work
  Obligation: Complete Corresponding Source (CCS)
  Key: NO installation info; NO explicit patent grant; NO cure period
  User: Linux kernel; Git; BusyBox
  SPDX: GPL-2.0-only

GPL-3.0-only  
  Trigger: CONVEYANCE of derivative work (broader than "distribution")
  Obligation: CCS + Installation Information (for User Products)
  Key: Explicit patent grant; 30-day cure; anti-Tivoization; anti-DRM
  User: GCC; Bash; GIMP; Samba
  SPDX: GPL-3.0-only

AGPL-3.0-only
  Trigger: CONVEYANCE *OR* NETWORK INTERACTION with modified version
  Obligation: Source to all users (including network users)
  Key: Closes "SaaS loophole"; strongest copyleft
  User: Nextcloud; Grafana (AGPL); some MongoDB versions
  SPDX: AGPL-3.0-only

LGPL-2.1-only / LGPL-3.0-only
  Trigger: Distribution of modified LIBRARY (not the linking program)
  Obligation: Source for library modifications; allow re-linking
  Key: Proprietary programs CAN link (dynamic preferred)
  User: glibc (2.1); Qt (3.0); GStreamer; GTK+
  SPDX: LGPL-2.1-only / LGPL-3.0-only

MPL-2.0
  Trigger: Distribution of modified MPL-covered FILES
  Obligation: Source for modified files only; new files = any license
  Key: File-level copyleft; GPL-compatible; very business-friendly
  User: Firefox; Terraform; LibreOffice
  SPDX: MPL-2.0
```

### Copyleft Risk Assessment Quick Check

```
QUESTION 1: Are you DISTRIBUTING the software?
  NO  → No copyleft obligations (internal use = free)
  YES → Continue

QUESTION 2: Does your code form a DERIVATIVE WORK with GPL code?
  (Modification, static link, intimate integration)
  NO  → "Mere aggregation" — your code stays your license
  YES → Continue

QUESTION 3: Which GPL version?
  GPL-2.0 → Provide CCS. Done.
  GPL-3.0 → Provide CCS. Is it a "User Product" (consumer device)?
    YES → Must provide Installation Information (keys, procedures)
    NO  → CCS sufficient
  AGPL-3.0 → Even without distribution: if network users interact
             with modified code → must provide source

LINKING QUICK GUIDE:
  Static link + GPL    = Your code becomes GPL-obligated
  Dynamic link + GPL   = Debated (FSF: yes; practice: risky)
  Dynamic link + LGPL  = SAFE — your code stays proprietary
  Static link + LGPL   = Must provide object files for re-linking
  Separate process/IPC = SAFE — not derivative work
  MPL file modification = Only THAT FILE stays MPL
```

---

*End of Document — 04_GPL_Copyleft_Licenses.md*
