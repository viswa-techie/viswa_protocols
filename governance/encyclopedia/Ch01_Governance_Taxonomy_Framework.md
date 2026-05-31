# Chapter 1 — Governance Taxonomy Framework & Methodology

## Universal Technology Standards Governance & Regulatory Architecture Encyclopedia
### First Edition, 2024–2026

---

## 1.1 Executive Taxonomy Overview

### Why Standards Governance Matters

Technology standards do not exist in a vacuum. Every protocol transmitted over a wire, every data format stored on disk, every API consumed by an application exists because some governance process — formal or informal — produced and maintains that specification. Understanding *who* controls a standard, *how* decisions are made, and *what enforcement mechanisms* exist is as important as understanding the technical content itself.

For an enterprise architect selecting a serialization format, the governance model determines:
- **Longevity risk**: Will this standard exist in 10 years? (L1 ISO standards persist for decades; L6 vendor-led standards can be abandoned)
- **Vendor lock-in risk**: Can a single company change the spec unilaterally? (L7 yes; L1/L3 no)
- **Interoperability guarantee**: Is conformance testing available? (ISO, IEEE, USB-IF provide certification; de-facto standards often do not)
- **Legal compliance**: Is implementation mandated by regulation? (FCC Part 15, GDPR, UNECE R155 are law; IETF RFCs are voluntary)
- **Cost of access**: Can engineers read the specification freely? (IETF RFCs are free; ISO documents cost $50–$300+)

### The 9-Tier Authority Ladder

This encyclopedia classifies the entire global technology standards ecosystem into exactly **nine governance tiers** (L0–L8), representing a spectrum from mathematical universality to informal de-facto adoption:

```
L0 ── Mathematical / Academic    ─── No governance body; universal truths
L1 ── International Standards    ─── Treaty-level; national body voting (ISO, IEC, ITU)
L2 ── Intergovernmental / Regulatory ── Government-mandated (NIST, FCC, EU)
L3 ── Industry Consortium        ─── Multi-vendor bodies (IETF, W3C, AUTOSAR, PCI-SIG)
L4 ── Professional Association   ─── Peer-reviewed, balloted (IEEE SA, SAE, RTCA)
L5 ── Foundation / Community     ─── Open governance, meritocratic (CNCF, Apache, Linux Fdn)
L6 ── Vendor-Led Open Standards  ─── Single-vendor origin, open access (Google gRPC, Meta GraphQL)
L7 ── Vendor Proprietary         ─── Closed, license-restricted (Apple Metal, NVIDIA CUDA)
L8 ── De-facto Standards         ─── No formal body; universal adoption (REST, JSON, semver)
```

### Authority vs. Influence

A critical distinction exists between **authority** (formal legal or contractual power to enforce compliance) and **influence** (market power that makes adoption essentially mandatory):

| Dimension   | Authority-Driven Standards                  | Influence-Driven Standards                       |
|-------------|---------------------------------------------|--------------------------------------------------|
| Enforcement | Regulatory, contractual, certification      | Market pressure, ecosystem lock-in               |
| Examples    | FCC Part 15 (radio), GDPR, ISO 26262       | x86 ISA, JSON, REST, Docker image format         |
| Penalty     | Fines, market exclusion, legal liability    | Incompatibility, developer rejection             |
| Tier        | L1, L2 primarily                            | L6, L7, L8 primarily                            |
| Stability   | Very high (decades)                         | Variable (can shift rapidly)                     |

### Why One Technology Can Appear at Multiple Tiers

TCP/IP is the canonical example of multi-tier existence:
- **L0**: The mathematical model (queuing theory, Shannon's theorem) underlying TCP congestion control
- **L3**: The IETF RFC specifications (RFC 9293 for TCP, RFC 8200 for IPv6)
- **L8**: TCP/IP as the de-facto universal network protocol stack — its dominance is not because of IETF authority, but because of 40 years of deployment

This encyclopedia assigns **one primary tier** based on the governing body that controls the normative specification, but notes cross-tier relevance in the entry template.

---

## 1.2 Complete Tier Definitions

### Tier L0 — Human / Academic / Mathematical

| Field                     | Description                                                          |
|---------------------------|----------------------------------------------------------------------|
| Tier                      | L0                                                                   |
| Official Label            | Human / Academic / Mathematical                                      |
| Governing Authority Type  | None — universal truth, peer-reviewed academic consensus             |
| Decision Model            | Scientific method / Mathematical proof / Peer review                 |
| Enforcement Mechanism     | None — enforced by logical necessity and physical law                |
| Specification Access      | Free — published in academic papers, textbooks                       |
| Openness Model            | Open                                                                 |
| Typical Bodies            | ACM, IEEE (research division), Universities, Research labs           |
| Typical Standard Types    | Mathematical models, formal grammars, computational models           |
| Adoption Driver           | Correctness — a proven theorem cannot be "unadopted"                 |
| Stability/Longevity       | Permanent — mathematical truths do not expire                        |
| Canonical Example         | Turing Machine model, Shannon entropy, Dijkstra's algorithm, IEEE 754 (floating point math), BNF grammar notation |

**Characteristics:**
- No governance body can "deprecate" 2+2=4
- IEEE 754 floating-point representation is at the boundary of L0/L4 — the math is L0; the encoding format standard is L4 (IEEE SA balloted)
- Formal grammars (BNF, EBNF) are L0 — they are notational mathematics
- Computational complexity classes (P, NP, PSPACE) are L0

---

### Tier L1 — International (Global) Standards Bodies

| Field                     | Description                                                          |
|---------------------------|----------------------------------------------------------------------|
| Tier                      | L1                                                                   |
| Official Label            | International (Global) Standards Bodies                               |
| Governing Authority Type  | Treaty-level international organization; national body membership     |
| Decision Model            | Treaty vote — one vote per national body (ISO, IEC) or Study Group consensus (ITU) |
| Enforcement Mechanism     | National adoption + regulatory reference (e.g., EU harmonized standards) |
| Specification Access      | Paid — typically $50–$300+ per document; national bodies sometimes provide free viewing |
| Openness Model            | Mixed — development is member-based; output is published but not free |
| Typical Bodies            | ISO, IEC, ISO/IEC JTC1, ITU-T, ITU-R, ITU-D                        |
| Typical Standard Types    | Specifications, terminology standards, management systems, test methods |
| Adoption Driver           | Regulatory mandate + international trade requirements                |
| Stability/Longevity       | Very High — ISO standards typically persist 10–30+ years with periodic review |
| Canonical Example         | ISO 9001 (QMS), ISO/IEC 27001 (ISMS), IEC 61508 (Functional Safety), ISO 26262 (Automotive Safety) |

**Key Characteristics:**
- **National body membership**: Each country has one national body (ANSI for USA, BSI for UK, DIN for Germany, JISC for Japan, SAC for China)
- **P-member vs. O-member**: P-members (Participating) vote and must submit comments; O-members (Observing) observe without vote
- **Development stages**: ISO uses numbered stages 00–95 (NP → WD → CD → DIS → FDIS → IS → Withdrawal)
- **Dual-logo standards**: ISO/IEC JTC1 produces standards under both ISO and IEC logos (e.g., ISO/IEC 27001)
- **Review cycle**: All ISO standards undergo Systematic Review every 5 years (Confirm / Revise / Withdraw)

---

### Tier L2 — Intergovernmental / Regulatory Bodies

| Field                     | Description                                                          |
|---------------------------|----------------------------------------------------------------------|
| Tier                      | L2                                                                   |
| Official Label            | Intergovernmental / Regulatory Bodies                                |
| Governing Authority Type  | Government agencies, UN specialized agencies, supra-national regulators |
| Decision Model            | Government authority — regulatory rulemaking (notice-and-comment for US; directive for EU) |
| Enforcement Mechanism     | Legal — fines, market exclusion, criminal liability                   |
| Specification Access      | Free — government publications are typically public domain or free access |
| Openness Model            | Open (access) but closed (participation is government-appointed)     |
| Typical Bodies            | NIST, FCC (USA), ETSI (EU), UNECE WP.29, EU Commission (GDPR, AI Act, CRA), ENISA |
| Typical Standard Types    | Regulations, technical requirements, FIPS, Special Publications      |
| Adoption Driver           | Legal mandate — non-compliance is illegal                            |
| Stability/Longevity       | High — regulations change slowly (GDPR: 2016+, FCC Part 15: decades) |
| Canonical Example         | NIST FIPS 197 (AES mandate), FCC Part 15 (radio emission limits), GDPR, EU AI Act, UNECE Reg 155 |

**Key Characteristics:**
- NIST Special Publications (SP 800 series) are informative but become effectively mandatory when referenced by FedRAMP, FISMA, or other mandates
- FIPS publications are normative for US government agencies
- EU Regulations (GDPR, AI Act) are directly applicable law in all EU member states
- EU Directives require national transposition (unlike Regulations)
- UNECE regulations (WP.29) apply to vehicle type approval across 50+ countries

---

### Tier L3 — Industry Consortium Standards

| Field                     | Description                                                          |
|---------------------------|----------------------------------------------------------------------|
| Tier                      | L3                                                                   |
| Official Label            | Industry Consortium Standards                                        |
| Governing Authority Type  | Multi-vendor, multi-stakeholder industry consortium or forum          |
| Decision Model            | Varies — Rough consensus (IETF), Working Group consensus (W3C), Member vote (AUTOSAR, PCI-SIG) |
| Enforcement Mechanism     | Contractual — membership agreements; market adoption; conformance testing |
| Specification Access      | Varies widely — IETF RFCs free; PCI-SIG/MIPI member-only; OASIS free |
| Openness Model            | Open to Mixed — participation often requires membership (fee-based) |
| Typical Bodies            | IETF, W3C, WHATWG, OASIS, AUTOSAR, PCI-SIG, USB-IF, JEDEC, MIPI Alliance, OPC Foundation, OMG, FIDO Alliance, OpenID Foundation, LoRa Alliance, CXL Consortium |
| Typical Standard Types    | Protocols, interface specifications, bus specifications, middleware architectures |
| Adoption Driver           | Industry agreement + ecosystem interoperability + certification programs |
| Stability/Longevity       | High for established specs (PCIe, USB), Medium for evolving (IETF HTTP) |
| Canonical Example         | IETF RFC 8446 (TLS 1.3), W3C HTML, AUTOSAR Classic Platform, PCIe 6.0, USB4, JEDEC DDR5 |

**Key Characteristics:**
- This is the largest and most diverse tier
- Sub-categories exist:
  - **Internet/Web consortia**: IETF, W3C, WHATWG (open, free spec access)
  - **Hardware interface consortia**: PCI-SIG, USB-IF, JEDEC, MIPI (member-gated spec access)
  - **Automotive/Industrial consortia**: AUTOSAR, COVESA, OPC Foundation (member-gated)
  - **Identity/Security consortia**: FIDO Alliance, OpenID Foundation (free specs)
- IETF has no formal membership — anyone can contribute
- W3C requires membership fee ($2,250–$77,000/year based on revenue)
- PCI-SIG membership is required to access full specifications

---

### Tier L4 — Professional Association Standards

| Field                     | Description                                                          |
|---------------------------|----------------------------------------------------------------------|
| Tier                      | L4                                                                   |
| Official Label            | Professional Association Standards                                   |
| Governing Authority Type  | Professional engineering/scientific society with standards-making arm |
| Decision Model            | Formal balloting — committee-based, comment resolution, recirculation |
| Enforcement Mechanism     | Contractual + regulatory reference (many IEEE/SAE standards referenced in law) |
| Specification Access      | Paid — IEEE Xplore ($50–$500+); SAE ($30–$500+); some free after embargo |
| Openness Model            | Mixed — open participation in working groups; paid access to final docs |
| Typical Bodies            | IEEE Standards Association (SA), SAE International, RTCA, EUROCAE, ACM |
| Typical Standard Types    | Technical standards, recommended practices, test methods, guidelines  |
| Adoption Driver           | Regulatory reference + industry best practice + professional credibility |
| Stability/Longevity       | High — IEEE 802.3 (Ethernet) first published 1983; continuously revised |
| Canonical Example         | IEEE 802.3 (Ethernet), IEEE 802.11 (Wi-Fi), IEEE 754 (Floating Point), SAE J1939, SAE J3016 (AD Levels), DO-178C (RTCA) |

**Key Characteristics:**
- IEEE SA standards go through a formal ballot process with IEEE-SA Standards Board oversight
- SAE standards are developed by Technical Committees with industry expert participation
- RTCA/EUROCAE standards (DO-178C, DO-254) become regulatory mandates via FAA/EASA adoption
- The IEEE-SA Sponsor Ballot process requires 75% affirmative votes with all negative ballots resolved
- Professional associations serve dual roles: standards development + professional education/certification

---

### Tier L5 — Foundation / Community Standards

| Field                     | Description                                                          |
|---------------------------|----------------------------------------------------------------------|
| Tier                      | L5                                                                   |
| Official Label            | Foundation / Community Standards                                      |
| Governing Authority Type  | Open-source foundation or community governance body                   |
| Decision Model            | Meritocratic consensus — committer/maintainer governance, TOC/TSC oversight |
| Enforcement Mechanism     | None (formal) — adoption driven by market utility and developer preference |
| Specification Access      | Free — typically on GitHub under open-source license                  |
| Openness Model            | Open — anyone can contribute; governance is transparent               |
| Typical Bodies            | Linux Foundation, CNCF, Apache Software Foundation, Eclipse Foundation, RISC-V International, Khronos Group, OpenSSF, MLCommons |
| Typical Standard Types    | Specifications, reference implementations, conformance test suites    |
| Adoption Driver           | Developer preference + ecosystem momentum + enterprise backing        |
| Stability/Longevity       | Medium to High — depends on community health and commercial backing   |
| Canonical Example         | Kubernetes API (CNCF), OCI Image Spec (Linux Fdn), Apache Kafka protocol, RISC-V ISA, OpenTelemetry (CNCF), Prometheus/OpenMetrics |

**Key Characteristics:**
- Foundation governance prevents any single vendor from dominating
- CNCF uses maturity levels: Sandbox → Incubating → Graduated
- Apache uses: Incubator → Top-Level Project (TLP) → Attic (retired)
- Linux Foundation hosts 100+ projects with independent governance
- The spec IS the reference implementation in many cases (Kubernetes, OCI)

---

### Tier L6 — Vendor-Led Open Standards

| Field                     | Description                                                          |
|---------------------------|----------------------------------------------------------------------|
| Tier                      | L6                                                                   |
| Official Label            | Vendor-Led Open Standards                                            |
| Governing Authority Type  | Single vendor that created and primarily maintains the specification  |
| Decision Model            | Corporate-controlled open — vendor makes final decisions; community provides input |
| Enforcement Mechanism     | None (formal) — adoption driven by vendor ecosystem dominance         |
| Specification Access      | Free — open-source license (Apache 2.0, MIT, BSD)                    |
| Openness Model            | Open (access) but vendor-controlled (governance)                     |
| Typical Bodies            | Google (Protobuf, gRPC, TensorFlow, Go), Meta (GraphQL, React, PyTorch), Microsoft (TypeScript, LSP, .NET), HashiCorp (Terraform HCL) |
| Typical Standard Types    | Languages, protocols, frameworks, APIs, serialization formats         |
| Adoption Driver           | Vendor ecosystem + developer experience + large-scale production use  |
| Stability/Longevity       | Medium — vendor can deprecate, relicense (HashiCorp BSL), or abandon  |
| Canonical Example         | Protocol Buffers (Google), gRPC (Google→CNCF), GraphQL (Meta→Linux Fdn), TypeScript (Microsoft) |

**Key Characteristics:**
- **Fork risk**: If the vendor acts against community interest, the community can fork (e.g., OpenTF → OpenTofu after HashiCorp BSL relicensing)
- **CLA requirement**: Most L6 projects require Contributor License Agreements giving the vendor special rights
- **Graduation path**: Some L6 standards "graduate" to L5 when donated to a foundation (GraphQL → Linux Foundation GraphQL Foundation)
- **Vendor override**: Despite open-source licensing, the vendor retains decision power through committer majority, roadmap control, and integration with proprietary products

---

### Tier L7 — Vendor Proprietary Standards

| Field                     | Description                                                          |
|---------------------------|----------------------------------------------------------------------|
| Tier                      | L7                                                                   |
| Official Label            | Vendor Proprietary Standards                                         |
| Governing Authority Type  | Single corporation with exclusive control                             |
| Decision Model            | Corporate — internal engineering decisions; no external governance    |
| Enforcement Mechanism     | License agreement — legal restriction on implementation              |
| Specification Access      | Proprietary — NDA-gated or commercially licensed                     |
| Openness Model            | Proprietary                                                          |
| Typical Bodies            | Apple (Metal, CoreML, Swift ABI), NVIDIA (CUDA, NVLink, NCCL), ARM (ISA pre-v8 open docs), Qualcomm (QMI), Intel (SGX, TDX) |
| Typical Standard Types    | APIs, instruction sets, bus protocols, runtime libraries              |
| Adoption Driver           | Hardware lock-in + market dominance + performance advantage           |
| Stability/Longevity       | Medium — tied to vendor product lifecycle and business strategy       |
| Canonical Example         | NVIDIA CUDA, Apple Metal, ARM ISA (licensed), Intel SGX               |

**Key Characteristics:**
- **Lock-in by design**: Developers targeting CUDA cannot easily move to AMD ROCm
- **Ecosystem moat**: Proprietary standards create switching costs (CUDA ecosystem: cuBLAS, cuDNN, TensorRT, NCCL)
- **Licensing models**: ARM ISA requires per-chip license fee; NVIDIA CUDA requires NVIDIA hardware
- **Documentation access**: Some vendors provide free documentation (ARM Architecture Reference Manual) but restrict implementation rights
- **Not necessarily bad**: Proprietary standards can drive innovation faster than consensus-based processes

---

### Tier L8 — De-facto Standards

| Field                     | Description                                                          |
|---------------------------|----------------------------------------------------------------------|
| Tier                      | L8                                                                   |
| Official Label            | De-facto Standards                                                    |
| Governing Authority Type  | None — no formal governance body exists for the standard itself       |
| Decision Model            | Market adoption — "everyone uses it" becomes the standard            |
| Enforcement Mechanism     | Social/market — deviation causes incompatibility or developer rejection |
| Specification Access      | Varies — often described in blog posts, dissertations, or informal specs |
| Openness Model            | Open (by default — no one controls it)                               |
| Typical Bodies            | None — that's the defining characteristic                            |
| Typical Standard Types    | Conventions, informal protocols, community practices                  |
| Adoption Driver           | First-mover advantage + network effects + "good enough" simplicity    |
| Stability/Longevity       | Variable — some are eternal (REST); some fade (SOAP was once de-facto)|
| Canonical Example         | REST (no spec — Fielding dissertation only), JSON (pre-RFC), YAML (pre-RFC), semver, Markdown, System V ABI |

**Key Characteristics:**
- **No normative specification**: REST has no RFC defining it — only Roy Fielding's 2000 PhD dissertation and informal interpretation
- **Fragmentation risk**: Without governance, implementations diverge (Markdown: CommonMark vs. GFM vs. MultiMarkdown)
- **Formalization path**: Many L8 standards eventually gain formal governance: JSON → RFC 8259 (IETF); YAML → yaml.org spec
- **Community-maintained informal spec**: semver.org maintains a specification document, but no standards body enforces it
- **Danger of "everyone assumes, no one specifies"**: Byte order, line endings, file path separators — de-facto norms vary by platform

---

## 1.3 Governance Decision Models

### Model 1: Treaty-Based Voting (ISO, IEC, ITU)

**How it works:**
```
National Body (e.g., ANSI-USA, BSI-UK, DIN-Germany)
    → Appoints experts to Technical Committee (TC)
    → TC creates Working Group (WG)
    → WG produces Working Draft (WD)
    → WD → Committee Draft (CD) [internal vote]
    → CD → Draft International Standard (DIS) [P-member vote — 2/3 majority required]
    → DIS → Final DIS (FDIS) [P-member vote — 2/3 majority, ≤1/4 negative]
    → FDIS → International Standard (IS) [published]
```

**Decision rules (ISO):**
- DIS approval: ≥ 2/3 P-members approve AND ≤ 1/4 total votes are negative
- FDIS approval: Same thresholds
- Each national body gets exactly ONE vote regardless of country size
- National bodies must form national mirror committees to develop their position
- Timeline: Typically 3–7 years from NP (New Proposal) to IS (International Standard)

**Strengths:**
- Maximum legitimacy — global consensus
- Broad industry input through national bodies
- Stable — changes require full re-balloting

**Weaknesses:**
- Extremely slow (3–7 years minimum)
- Expensive — participation requires national body membership and expert delegation
- Specification access is paid ($50–$300+ per document)
- Small countries may lack resources to participate effectively

---

### Model 2: Formal Balloting (IEEE SA, SAE, ANSI-accredited)

**How it works:**
```
Sponsor (IEEE Working Group or SAE Technical Committee)
    → Develops initial draft
    → Sponsor Ballot Group formed (experts from diverse organizations)
    → 30-day ballot period
    → All negative ballots must receive comment resolution
    → If changes made → Recirculation ballot (15 days)
    → 75% affirmative vote required (IEEE SA)
    → IEEE-SA Standards Board reviews and approves
    → Published standard
```

**Decision rules (IEEE SA):**
- Ballot group must have balanced representation (producers, users, general interest)
- 75% affirmative required (excluding abstentions)
- Every negative ballot with comment must be resolved — cannot be ignored
- Recirculation required if substantive changes are made during resolution
- Appeals process exists through IEEE-SA Standards Board

**Strengths:**
- Balanced stakeholder representation
- Rigorous comment resolution prevents railroad
- Broad industry acceptance

**Weaknesses:**
- Slow (2–5 years typical)
- Participation requires IEEE SA membership or ballot group invitation
- Final standards are behind paywall (IEEE Xplore)
- Process can be "captured" by well-funded companies flooding ballot group

---

### Model 3: Rough Consensus and Running Code (IETF)

**How it works:**
```
Individual contributor (any person — no membership required)
    → Internet-Draft (I-D) submitted
    → Working Group (WG) adoption (if chartered WG exists)
    → WG discussions (mailing list + meetings)
    → WG Last Call (2 weeks)
    → IETF Last Call (2 weeks — cross-area review)
    → IESG review and approval
    → RFC Editor processing
    → Published as RFC (Proposed Standard, Internet Standard, Informational, etc.)
```

**Decision rules (IETF — "The Tao of the IETF"):**
- "We reject kings, presidents, and voting. We believe in rough consensus and running code." — Dave Clark, 1992
- **Rough consensus**: Not unanimity; not majority vote. The chair "hums" the room or assesses mailing list discussion
- **Running code**: Specifications should be implementable; interop testing is expected
- No formal membership — anyone can participate and contribute
- No national body representation — individuals contribute personally
- IESG (Internet Engineering Steering Group) can override WG consensus on technical grounds

**Strengths:**
- Fast iteration (months, not years)
- Open to all — no membership fee
- All specifications are free (rfc-editor.org)
- "Running code" requirement ensures implementability
- Extremely high-quality technical output

**Weaknesses:**
- "Rough consensus" is subjective — WG chairs have significant power
- US-dominated participation (historically)
- Complex process for newcomers despite openness
- No formal enforcement — compliance is voluntary

---

### Model 4: W3C Process (Consensus with Director/Co-Chair Authority)

**How it works:**
```
Member Submission or Staff initiative
    → Working Group chartered (by W3C Member Advisory Committee)
    → First Public Working Draft (FPWD)
    → Working Draft iterations
    → Candidate Recommendation (CR) — implementation testing required
    → Proposed Recommendation (PR) — Advisory Committee review (4 weeks)
    → W3C Recommendation (REC) — final published standard
```

**Post Tim Berners-Lee (2022+) governance:**
- No longer "Director decides" — now Team/co-chairs resolve issues
- Advisory Board handles escalations
- W3C transitioned to a legal entity (501c3) in 2023

**Strengths:**
- Produces universally implemented web standards (HTML, CSS, WebAuthn)
- Implementation experience required before Recommendation status
- Royalty-Free patent policy (W3C Patent Policy)

**Weaknesses:**
- Membership fee required ($2,250 – $77,000/year)
- Process can be slow (CSS Grid took years)
- WHATWG "Living Standard" model creates tension (HTML is now WHATWG, not W3C)

---

### Model 5: BDFL (Benevolent Dictator For Life)

**How it works:**
```
Single technical leader makes final decisions
    → Community proposes (PEP for Python, RFC for Rust pre-foundation)
    → Discussion and feedback from community
    → BDFL accepts or rejects
    → Decision is final (no vote, no ballot)
```

**Historical examples:**
- **Python**: Guido van Rossum was BDFL (1991–2018); resigned after PEP 572 controversy
- **Linux kernel**: Linus Torvalds is effectively BDFL for kernel architecture decisions
- **Ruby**: Yukihiro "Matz" Matsumoto retains final say

**Successor governance:**
- Python → Steering Council (5 elected members, annual election)
- Rust → Core Team → Leadership Council (2023 governance reform)
- Node.js → TSC (Technical Steering Committee) under OpenJS Foundation

**Strengths:**
- Fast decisions
- Coherent technical vision
- Avoids "design by committee" problems

**Weaknesses:**
- Bus factor of 1
- Community burnout of the BDFL
- Succession crisis when BDFL steps down
- Personality-driven — can create toxic dynamics

---

### Model 6: Corporate-Controlled Open Standard

**How it works:**
```
Vendor creates technology internally
    → Open-sources specification and/or implementation
    → Accepts external contributions (with CLA)
    → Vendor retains commit rights, roadmap control, trademark
    → Community can fork (but rarely do due to ecosystem network effects)
```

**Examples:**
- **Google**: Protocol Buffers (proto3), gRPC, Go language, Angular, Kubernetes (donated to CNCF)
- **Meta**: GraphQL (donated to GraphQL Foundation), React, PyTorch (donated to Linux Fdn)
- **Microsoft**: TypeScript, .NET, Visual Studio Code, LSP (Language Server Protocol)
- **HashiCorp**: Terraform HCL (relicensed from MPL to BSL 1.1 in 2023 — community forked to OpenTofu)

**CLA implications:**
- Contributor License Agreement gives the company rights to relicense
- Google CLA: grants Google perpetual license to your contributions
- Apache CLA: more balanced — grants to the project, not a specific company
- BSL relicensing case (HashiCorp 2023) demonstrated CLA risk: company used CLA rights to close source

**Strengths:**
- Rapid innovation (corporate R&D budget)
- Production-proven at massive scale before open-sourcing
- Large investment in tooling and documentation

**Weaknesses:**
- Vendor can abandon, relicense, or deprecate without community consent
- CLA creates asymmetric power relationship
- "Open source" ≠ "open governance" — the company retains control
- Fork risk increases if community trust erodes

---

### Model 7: Pure Community Governance

**How it works:**
```
Community forms around shared interest
    → Governance structures emerge organically (or are designed)
    → Committer/maintainer model (earn rights through contribution)
    → Decisions by consensus among maintainers
    → Project leadership by election or "lazy consensus"
    → Conflict resolution through governance documents or community vote
```

**Examples:**
- **Linux kernel**: Linus + maintainer subsystem hierarchy
- **Debian**: Debian Constitution, elected DPL (Debian Project Leader), GR (General Resolution) votes
- **Apache Software Foundation**: PMC (Project Management Committee) per project, ASF Board oversight
- **Fedora**: FESCo (Fedora Engineering Steering Committee), elected

**Strengths:**
- No single-vendor control
- Contributor-aligned incentives
- Transparent decision-making
- Community ownership prevents abandonment

**Weaknesses:**
- Slow decision-making (consensus-based)
- "Meritocracy" can become oligarchy (long-tenured maintainers dominate)
- Volunteer burnout
- Hard to make controversial decisions (Python indentation debates lasted years)

---

### Model 8: De-facto / No Governance

**How it works:**
```
Someone creates something useful
    → Others copy it
    → "Everyone does it this way" becomes the standard
    → No formal body, no specification, no process
    → Community norms enforce conformance
```

**Path to de-facto standardhood:**
1. First-mover advantage — created early when no alternatives existed
2. Network effect — the more adopters, the more valuable conformance becomes
3. Simplicity — easy enough that no formal spec seems necessary
4. Cultural embedding — "that's just how we do it"

**Examples:**
- **REST**: No RFC. No W3C Recommendation. Only Roy Fielding's 2000 dissertation. Yet it's the dominant web API paradigm.
- **Markdown**: No formal standard until CommonMark (2014). John Gruber's 2004 Perl script was the "spec."
- **semver** (Semantic Versioning): Tom Preston-Werner wrote semver.org. No standards body. Universal adoption.
- **System V AMD64 ABI**: No ISO standard. Just a PDF maintained informally. Yet every x86-64 Linux binary conforms.

**Risks:**
- Fragmentation (Markdown: GFM ≠ CommonMark ≠ MultiMarkdown ≠ Gruber's original)
- Ambiguity (REST: what counts as "RESTful"? Richardson Maturity Model attempts to clarify)
- No conformance testing (no one certifies "semver compliant")
- Difficult to evolve (who decides what semver 3.0 would look like?)

---

## 1.4 Standard Artifact Type Taxonomy

Every technology artifact in this encyclopedia is classified into exactly one primary artifact type:

| Artifact Type          | Definition                                                                      | Example(s)                                     | Governs What                    | Disambiguation                                |
|------------------------|---------------------------------------------------------------------------------|------------------------------------------------|---------------------------------|-----------------------------------------------|
| **Specification**      | Normative document defining required behavior for conformance                   | HTTP/1.1 (RFC 9112), USB4 Spec                | System behavior                  | Not a tutorial or guide — it's normative       |
| **Protocol**           | Rules governing the exchange of messages between communicating parties          | TCP, TLS, CAN, SOME/IP, MQTT                   | Message exchange rules           | Not a format — protocols are active, formats are passive |
| **Format**             | Definition of how data is structured for storage or transport                   | JSON, XML, Parquet, HDF5, ELF                  | Data shape at rest or in flight  | Not a protocol — no exchange semantics        |
| **Encoding**           | Binary or text serialization rules mapping abstract data to concrete bytes      | Protobuf wire format, ASN.1 BER/DER, UTF-8     | Byte-level representation        | Not a schema — encoding is how, schema is what |
| **IDL**                | Language for defining interfaces independent of implementation platform        | CORBA IDL, Thrift IDL, Protobuf IDL (.proto)   | API contracts across languages   | Not an encoding — IDL defines structure, encoding defines bytes |
| **Schema**             | Structural model defining valid instances of a data type                        | JSON Schema, XML Schema (XSD), Avro Schema      | Validation rules                 | Not an encoding — schemas validate, not serialize |
| **API Specification**  | Machine-readable definition of a software interface                             | OpenAPI 3.1, AsyncAPI 3.0, WSDL                | HTTP/event API shape             | Not an implementation — spec describes the contract |
| **Framework**          | Architectural pattern + component model + interfaces for building systems       | AUTOSAR Classic/Adaptive, POSIX, .NET Framework | Implementation architecture     | Not a standard alone — frameworks prescribe how to build |
| **Reference Model**    | Abstract conceptual architecture for understanding a domain                     | OSI 7-layer model, TOGAF, SABSA, C4 Model      | Mental model / conceptual        | Not implementable directly — it's a thinking tool |
| **Compliance Framework** | Risk management, control catalog, and audit methodology                       | ISO 27001, NIST CSF 2.0, SOC 2, CIS Benchmarks| Organization processes           | Not a technical spec — governs processes/people |
| **Regulation**         | Legally enforceable rule with government authority behind it                    | GDPR, FCC Part 15, UNECE R155, EU AI Act        | Legal obligation                 | Not voluntary — non-compliance has legal penalties |
| **Certification Program** | Third-party conformance testing and credential issuance                      | Common Criteria (ISO 15408), Wi-Fi Certified, FIDO Certified | Market trust signal | Not a standard itself — certifies conformance TO a standard |
| **De-facto Convention** | Informal practice so widely adopted it functions as a standard                 | REST, semver, Markdown, 80-char line limit       | Community norm                   | No normative spec exists — adherence is voluntary and informal |

### Critical Disambiguation: Protocol vs. Format vs. Encoding vs. Schema

This confusion is the most common error in standards discussions. Precise distinction:

```
PROTOCOL:   "We exchange messages following these rules"
             → TCP, HTTP, gRPC, MQTT, CAN
             → Has: state machines, message sequences, handshakes, timeouts

FORMAT:     "Data is organized in this shape"
             → JSON, XML, Parquet, ELF binary, PNG
             → Has: field names, nesting rules, type constraints

ENCODING:   "Abstract values become these exact bytes"
             → Protobuf wire encoding, ASN.1 DER, UTF-8, Base64
             → Has: byte layout, endianness, varint encoding, length prefixes

SCHEMA:     "Only these data shapes are valid"
             → JSON Schema, XSD, Avro Schema, GraphQL SDL
             → Has: type definitions, required fields, validation rules, constraints
```

**The Protobuf triad (most commonly confused):**
| Component        | Artifact Type | What it Does                                    |
|------------------|---------------|-------------------------------------------------|
| `.proto` file    | IDL           | Defines messages, services, fields (human-readable language) |
| Wire format      | Encoding      | Defines how proto messages become bytes (varint, length-delimited, etc.) |
| gRPC             | Protocol      | Defines how encoded messages are exchanged over HTTP/2 |

---

## 1.5 Standardization Lifecycle Model

### The 14-Phase Universal Lifecycle

Every major standard — regardless of governance body — passes through these phases.
Not all phases apply to all bodies (L8 de-facto standards skip most of them).

```
┌──────────────────────────────────────────────────────────────────────────────┐
│ Phase 1: RESEARCH / INCUBATION                                               │
│ • Academic papers published identifying the problem                          │
│ • Proof-of-concept implementations (university, lab, startup)                │
│ • Birds-of-a-Feather (BoF) sessions at conferences                           │
│ • Duration: months to years                                                  │
│ • ISO: Pre-NP stage | IETF: I-D stage | W3C: Interest Group                 │
├──────────────────────────────────────────────────────────────────────────────┤
│ Phase 2: PROPOSAL / SUBMISSION                                               │
│ • Formal problem statement and proposed scope                                │
│ • Submitted to appropriate standards body                                    │
│ • ISO: New Work Item Proposal (NP) | IETF: I-D + WG charter proposal        │
│ • W3C: Member Submission or Staff Contact initiative                         │
│ • Feasibility and scope review by parent committee                           │
├──────────────────────────────────────────────────────────────────────────────┤
│ Phase 3: WORKING GROUP FORMATION                                             │
│ • Charter: scope, deliverables, milestones, timeline                         │
│ • Chair(s) and editor(s) appointed                                           │
│ • Members recruited or self-selected                                         │
│ • ISO: WG under TC | IETF: WG under Area | W3C: WG under Domain             │
│ • IPR (Intellectual Property Rights) policies activated                      │
├──────────────────────────────────────────────────────────────────────────────┤
│ Phase 4: INITIAL DRAFT                                                       │
│ • Editor assembles first complete draft specification                        │
│ • Internal WG review and iteration                                           │
│ • ISO: Working Draft (WD) | IETF: Working Group I-D | W3C: Editor's Draft   │
│ • Multiple iterations typical (WD1, WD2, WD3...)                             │
├──────────────────────────────────────────────────────────────────────────────┤
│ Phase 5: PUBLIC REVIEW / INDUSTRY FEEDBACK                                   │
│ • Draft published for external comment                                       │
│ • Public comment period (typically 30–90 days)                               │
│ • ISO: Committee Draft (CD) | IETF: WG Last Call + IETF Last Call            │
│ • W3C: First Public Working Draft (FPWD) + subsequent WD iterations          │
│ • Comments must be dispositioned (accepted, rejected with rationale)         │
├──────────────────────────────────────────────────────────────────────────────┤
│ Phase 6: BALLOT / VOTING PROCESS                                             │
│ • Formal vote by eligible participants                                       │
│ • ISO: DIS ballot (P-members vote — need 2/3 approval, ≤1/4 negative)       │
│ • IEEE SA: Sponsor Ballot (75% affirmative required)                         │
│ • IETF: "Humming" + IESG consensus (no formal vote)                         │
│ • Negative votes with technical substance must be resolved                   │
├──────────────────────────────────────────────────────────────────────────────┤
│ Phase 7: RATIFICATION / APPROVAL                                             │
│ • Final approval by governance authority                                     │
│ • ISO: FDIS stage + final vote | IETF: IESG approval | W3C: AC Review       │
│ • IEEE: Standards Board approval after ballot completion                     │
│ • SAE: Standards Committee publication approval                              │
├──────────────────────────────────────────────────────────────────────────────┤
│ Phase 8: PUBLICATION                                                         │
│ • Standard is officially published and available                             │
│ • ISO: Published IS | IETF: RFC publication | W3C: W3C Recommendation        │
│ • IEEE: Published in IEEE Xplore | SAE: Published on SAE MOBILUS             │
│ • Assigned unique identifier (ISO number, RFC number, IEEE number)           │
├──────────────────────────────────────────────────────────────────────────────┤
│ Phase 9: REFERENCE IMPLEMENTATION                                            │
│ • One or more implementations created to validate spec                       │
│ • W3C requires at least 2 independent implementations for CR → PR            │
│ • IETF expects "running code" but doesn't mandate specific implementations  │
│ • Open-source reference implementations accelerate adoption                  │
├──────────────────────────────────────────────────────────────────────────────┤
│ Phase 10: CONFORMANCE TESTING                                                │
│ • Test suites created to verify implementation compliance                    │
│ • USB-IF: compliance testing mandatory for USB logo use                      │
│ • Wi-Fi Alliance: Wi-Fi CERTIFIED test program                               │
│ • OCI: conformance test suite for container runtime compliance               │
│ • Some standards have no formal conformance program (IETF RFCs typically)    │
├──────────────────────────────────────────────────────────────────────────────┤
│ Phase 11: CERTIFICATION PROGRAM                                              │
│ • Formal credential issued after passing conformance tests                   │
│ • USB-IF logo program, Wi-Fi CERTIFIED, FIDO Certified, Bluetooth SIG       │
│ • ISO management system: third-party audit (e.g., ISO 27001 certification)  │
│ • Not all standards have certification — IETF RFCs do not                    │
├──────────────────────────────────────────────────────────────────────────────┤
│ Phase 12: COMPLIANCE REQUIREMENT                                             │
│ • External mandate makes compliance obligatory                               │
│ • Regulatory: GDPR requires data protection (references ISO 27001)           │
│ • Contractual: automaker requires AUTOSAR compliance from Tier-1 suppliers   │
│ • Procurement: US government requires FedRAMP for cloud (references NIST)    │
│ • Not all standards reach this phase — voluntary adoption is common          │
├──────────────────────────────────────────────────────────────────────────────┤
│ Phase 13: REVISION / AMENDMENT                                               │
│ • Errata, corrigenda, amendments published                                   │
│ • ISO: Systematic Review every 5 years → Confirm / Revise / Withdraw        │
│ • IETF: New RFC that obsoletes/updates previous RFC                          │
│ • IEEE: Revision (full new edition) or Amendment (incremental change)        │
│ • Major revisions restart from Phase 3 or 4                                  │
├──────────────────────────────────────────────────────────────────────────────┤
│ Phase 14: DEPRECATION / SUPERSESSION                                         │
│ • Standard declared deprecated, obsolete, or withdrawn                       │
│ • ISO: Withdrawn status (still available but not maintained)                 │
│ • IETF: "Obsoleted by RFC XXXX" header in original RFC                       │
│ • IEEE: Inactive status (no longer maintained)                               │
│ • Migration guidance published for moving to successor standard              │
│ • Legacy systems may continue using deprecated standards for years           │
└──────────────────────────────────────────────────────────────────────────────┘
```

### Lifecycle Duration by Governance Body

| Body     | Typical Duration (NP to Publication) | Fastest Possible    | Slowest Observed |
|----------|--------------------------------------|---------------------|------------------|
| ISO      | 3–7 years                            | 18 months (fast-track) | 10+ years     |
| IEC      | 3–5 years                            | 18 months           | 8 years          |
| ITU-T    | 2–4 years                            | AAP 9 months        | 5+ years         |
| IETF     | 6 months – 3 years                   | 3 months            | 10+ years (IPv6) |
| W3C      | 2–5 years                            | 1 year              | 8+ years (CSS3)  |
| IEEE SA  | 2–4 years                            | 18 months           | 6 years          |
| AUTOSAR  | 1–2 years (release cycle)            | 6 months (patch)    | 3 years          |

---

## 1.6 Normative Language Model

### RFC 2119 / BCP 14 Keyword Definitions

All IETF specifications and many other standards use these keywords per RFC 2119 and RFC 8174:

| Keyword        | Meaning                                                        | Compliance Implication                    |
|----------------|----------------------------------------------------------------|-------------------------------------------|
| **MUST**       | Absolute requirement — non-conformance is a specification violation | Implementation MUST do this             |
| **MUST NOT**   | Absolute prohibition                                           | Implementation MUST NOT do this          |
| **SHALL**      | Same as MUST (ISO/IEC usage; IETF prefers MUST)               | Identical to MUST in normative force      |
| **SHALL NOT**  | Same as MUST NOT                                               | Identical to MUST NOT                    |
| **SHOULD**     | Recommended — valid reasons to ignore exist, but implications must be understood | Deviation requires documented justification |
| **SHOULD NOT** | Discouraged — valid reasons to do it exist, but risks must be understood | Same as SHOULD (inverse)           |
| **MAY**        | Optional — implementation choice                               | Interoperability must work regardless    |
| **RECOMMENDED**| Same as SHOULD                                                 | Same as SHOULD                           |
| **OPTIONAL**   | Same as MAY                                                    | Same as MAY                              |

**Critical rule**: These keywords have normative force ONLY when written in UPPERCASE. Lowercase "must" or "should" carries no RFC 2119 meaning.

### ISO Normative Language

ISO standards use different terminology per ISO/IEC Directives, Part 2:

| ISO Term       | Equivalent RFC 2119 | Meaning                                  |
|----------------|---------------------|------------------------------------------|
| "shall"        | MUST                | Requirement                              |
| "shall not"    | MUST NOT            | Prohibition                              |
| "should"       | SHOULD              | Recommendation                           |
| "should not"   | SHOULD NOT          | Deprecation                              |
| "may"          | MAY                 | Permission                               |
| "can"          | —                   | Possibility/capability (not normative)   |
| "NOTE"         | —                   | Informative text (not normative)         |

---

## 1.7 Authority vs. Influence Model

### The Two Axes of Standards Power

```
                        HIGH AUTHORITY
                             │
          L1 (ISO/IEC)       │         L2 (Regulations)
          ─────────────      │         ─────────────────
          Treaty + vote      │         Legal enforcement
          Slow but durable   │         Mandatory compliance
                             │
   LOW INFLUENCE ────────────┼──────────── HIGH INFLUENCE
                             │
          L0 (Academic)      │         L7/L8 (De-facto/Vendor)
          ─────────────      │         ─────────────────────
          Pure knowledge     │         Market dominance
          No enforcement     │         Lock-in + ecosystem
                             │
                        LOW AUTHORITY
```

| Quadrant          | Standards in this zone                                | Characteristics                                |
|-------------------|-------------------------------------------------------|------------------------------------------------|
| High Auth + High Inf | GDPR, FCC Part 15, ISO 26262 (referenced by OEMs) | Maximum compliance pressure                    |
| High Auth + Low Inf  | Obscure ISO standards with low adoption             | Formally correct but practically ignored       |
| Low Auth + High Inf  | REST, JSON, semver, CUDA, Docker (pre-OCI)         | Everyone uses it but nobody enforces it         |
| Low Auth + Low Inf   | Abandoned proposals, superseded drafts              | Historical curiosity only                      |

---

## 1.8 Standards Migration Between Tiers

Standards are not fixed at one tier forever. Common migration patterns:

### Pattern 1: L8 → L3 (De-facto → Formalized)
```
JSON: De-facto (L8) → ECMA-404 (L3) → RFC 8259 (L3 IETF)
YAML: De-facto (L8) → yaml.org informal spec → (attempted formalization)
HTTP: Academic (L0 Berners-Lee) → IETF RFC (L3) → universal de-facto (L8 simultaneously)
```

### Pattern 2: L6 → L5 (Vendor-Led → Foundation)
```
Kubernetes: Google (L6) → CNCF Graduated (L5)
GraphQL: Meta (L6) → GraphQL Foundation / Linux Foundation (L5)
PyTorch: Meta (L6) → PyTorch Foundation / Linux Foundation (L5)
```

### Pattern 3: L3 → L1 (Consortium → International Standard)
```
USB: USB-IF (L3) → ISO/IEC 18881 (L1 — but USB-IF remains primary governance)
POSIX: IEEE 1003 (L4) → ISO/IEC 9945 (L1 dual-logo)
C language: Bell Labs (L6/L8) → ANSI C (L4) → ISO/IEC 9899 (L1)
```

### Pattern 4: L7 → L6 (Proprietary → Vendor Open)
```
.NET: Microsoft proprietary → .NET Core (MIT, open source L6)
Swift: Apple proprietary → Swift open-sourced (L6) — but Apple retains control
TypeScript: Microsoft (L6 from inception — but effectively controls evolution)
```

### Pattern 5: L5 → Abandoned / Forked
```
CoffeeScript: Community (L5) → superseded by ES6 (L3 ECMA)
AngularJS: Google (L6) → rewritten as Angular (breaking change, fork-like migration)
OpenOffice: Oracle abandoned → LibreOffice fork (community L5)
```

---

## 1.9 Key Disambiguation Pairs

This section establishes mandatory disambiguation rules used throughout the encyclopedia:

| Pair                                              | Distinction                                                                                              |
|---------------------------------------------------|----------------------------------------------------------------------------------------------------------|
| **Standards body** vs. **implementation vendor**  | IEEE is a standards body; Cisco is an implementation vendor. IEEE writes 802.11; Cisco builds Wi-Fi chips. |
| **Protocol** vs. **format**                       | HTTP is a protocol (exchange rules). JSON is a format (data shape). A REST API uses HTTP protocol to exchange JSON format data. |
| **IDL** vs. **serialization format**              | `.proto` file is an IDL (defines structure). Protobuf wire encoding is a serialization format (defines bytes). |
| **Schema** vs. **encoding**                       | JSON Schema validates data. JSON encoding specifies UTF-8 text. Schema checks correctness; encoding converts to bytes. |
| **Open standard** vs. **de-facto standard**       | TCP/IP is IETF-standardized (L3) AND de-facto universal (L8). REST is only de-facto (L8) — no normative spec. |
| **Consortium std** vs. **vendor-led open**        | AUTOSAR is multi-vendor consortium (L3). Google Protobuf is vendor-led open (L6). Both are "open" but governance differs fundamentally. |
| **Regulatory standard** vs. **industry spec**     | GDPR is EU law (L2) — violation means fines. ISO 27001 is industry certification (L1) — optional unless contractually required. |
| **Platform API** vs. **neutral standard**         | Android API is platform-specific (Google L6/L7). POSIX API is neutral portable standard (L1/L4). |
| **Community project** vs. **foundation-governed** | Random GitHub project: no governance. Apache Kafka: ASF PMC governance, clear committer process, trademark protection. |
| **Certification** vs. **compliance** vs. **conformance** | Certification: third-party issues credential (ISO 27001 cert). Compliance: meets regulation (GDPR compliant). Conformance: passes test suite (Wi-Fi CERTIFIED). |

---

## 1.10 Chapter Summary and Crosslinks

### What This Chapter Established:
- The 9-tier governance authority ladder (L0–L8) — the primary classification axis for all 200+ entries
- Eight governance decision models explaining HOW standards get made
- Thirteen artifact types for WHAT kind of standard it is
- The 14-phase universal lifecycle model
- Normative language definitions (RFC 2119 + ISO directives)
- Authority vs. Influence model
- Tier migration patterns
- Mandatory disambiguation rules

### Crosslinks to Other Chapters:

| Topic                                        | Chapter |
|----------------------------------------------|---------|
| ISO, IEC, ITU full entries                   | Ch. 2   |
| IETF, W3C, WHATWG, OASIS full entries        | Ch. 3   |
| AUTOSAR, OMG, PCI-SIG, MIPI full entries     | Ch. 4   |
| IEEE SA, SAE, RTCA full entries              | Ch. 5   |
| CNCF, Linux Foundation, Apache full entries  | Ch. 6   |
| Google, Meta, Microsoft open specs           | Ch. 7   |
| Apple, NVIDIA, ARM proprietary               | Ch. 8–9 |
| NIST, FCC, EU regulatory bodies              | Ch. 10  |
| Automotive domain standards                  | Ch. 11  |
| Networking/Telecom domain                    | Ch. 12  |
| Cybersecurity domain                         | Ch. 13  |
| Cloud/API domain                             | Ch. 14  |
| Hardware/Semiconductor domain                | Ch. 15  |
| AI/ML/Data domain                            | Ch. 16  |
| Safety-Critical domain                       | Ch. 17  |
| Full crosswalk tables                        | Ch. 18  |
| Master classification matrix                 | Ch. 19  |
| Entry template and authoring guide           | Ch. 20  |

---

*End of Chapter 1 — Governance Taxonomy Framework & Methodology*
