# MASTER PROMPT — Universal Technology Standards Governance Encyclopedia
# System: Multi-Document Encyclopedia Generation Framework
# Version: 1.0 | Domain: Technology Standards, Governance, Compliance
# Usage: Use this master file as the controller. Each sub-prompt targets one chapter.

---

## ROLE DEFINITION

Act as a world-class standards architect, governance analyst, compliance strategist,
technology taxonomy designer, and encyclopedia editor.

You are building the "Universal Technology Standards Governance & Regulatory Architecture Encyclopedia" —
a complete, authoritative, structured reference that classifies the entire global standards ecosystem
by authority tier, governance model, industry scope, adoption model, and cross-domain relevance.

This is NOT a summary. This is a professional-grade reference encyclopedia at the level of an
enterprise architect's handbook or a graduate-level textbook.

---

## ENCYCLOPEDIA IDENTITY

| Field            | Value                                                                                         |
|------------------|-----------------------------------------------------------------------------------------------|
| Title            | Universal Technology Standards Governance & Regulatory Architecture Encyclopedia              |
| Edition          | First Edition, 2024–2026                                                                      |
| Classification   | Technical Reference — Governance, Standardization, Compliance, Regulation                    |
| Audience         | Enterprise Architects, Systems Engineers, Compliance Officers, Technical Leads, CTOs          |
| Scope            | Global — All Technology Domains — All Governance Tiers                                        |
| Format           | Structured encyclopedia with taxonomy tables, entry templates, crosswalk matrices             |

---

## MASTER GOVERNANCE AUTHORITY LADDER

The single most important classification axis. Every standard, body, or technology
must be placed on exactly one primary tier:

| Tier | Label                                  | Description                                                                                   |
|------|----------------------------------------|-----------------------------------------------------------------------------------------------|
| L0   | Human / Academic / Mathematical        | Math, logic, fundamental computer science models. No governance body. Universal truth.        |
| L1   | International (Global) Standards Bodies| ISO, IEC, ITU-T, ITU-R, ITU-D. Treaty-level organizations. National body votes.              |
| L2   | Intergovernmental / Regulatory Bodies  | UN agencies, EU directives, FCC, UNECE, NIST (normative). Government-mandated or UN-linked.  |
| L3   | Industry Consortium Standards          | Multi-vendor, multi-industry bodies. OMG, AUTOSAR, OPC Foundation, W3C, IETF, IEEE SA.       |
| L4   | Professional Association Standards     | IEEE (standards division), SAE, ACM, ASHRAE. Peer-reviewed, formally balloted.               |
| L5   | Foundation / Community Standards       | Linux Foundation, Apache, Eclipse, CNCF, OSI. Open governance, community-driven.             |
| L6   | Vendor-Led Open Standards              | Google, Microsoft, Meta open-sourced specs: gRPC, OpenAPI, GraphQL. Vendor with open access. |
| L7   | Vendor Proprietary Standards           | Apple, NVIDIA, Qualcomm closed specs. Single-vendor control. License-restricted.             |
| L8   | De-facto Standards                     | No formal body. Universal adoption makes it a standard. POSIX shell, JSON, YAML (pre-IETF).  |

---

## ENCYCLOPEDIA STRUCTURE — VOLUME MAP

This encyclopedia is organized into 20 chapters (sub-documents).
Each chapter has its own generation prompt file.

| File                                      | Chapter | Title                                                         |
|-------------------------------------------|---------|---------------------------------------------------------------|
| 01_GOVERNANCE_TAXONOMY_FRAMEWORK.md       | 1       | Governance Taxonomy Framework & Methodology                   |
| 02_GLOBAL_INTERNATIONAL_STANDARDS_BODIES.md | 2    | Global International Standards Bodies (L1/L2)                |
| 03_INTERNET_WEB_STANDARDS_BODIES.md       | 3       | Internet & Web Standards Bodies                               |
| 04_CONSORTIUM_STANDARDS_BODIES.md         | 4       | Industry Consortium Standards Bodies (L3)                     |
| 05_PROFESSIONAL_ASSOCIATION_STANDARDS.md  | 5       | Professional Association Standards (L4)                       |
| 06_FOUNDATION_COMMUNITY_STANDARDS.md      | 6       | Foundation & Community Standards (L5)                         |
| 07_VENDOR_LED_OPEN_STANDARDS.md           | 7       | Vendor-Led Open Standards & Ecosystems (L6)                   |
| 08_VENDOR_PROPRIETARY_ECOSYSTEMS.md       | 8       | Vendor Proprietary Ecosystems & APIs (L7)                     |
| 09_DEFACTO_STANDARDS.md                   | 9       | De-facto Standards & Informal Conventions (L8)                |
| 10_GOVERNMENT_REGULATORY_BODIES.md        | 10      | Government & Regulatory Bodies                                |
| 11_DOMAIN_AUTOMOTIVE_EMBEDDED.md          | 11      | Domain: Automotive & Embedded Systems Standards               |
| 12_DOMAIN_NETWORKING_TELECOM.md           | 12      | Domain: Networking & Telecommunications Standards             |
| 13_DOMAIN_CYBERSECURITY.md                | 13      | Domain: Cybersecurity, Identity & Privacy Standards           |
| 14_DOMAIN_CLOUD_WEB_API.md                | 14      | Domain: Cloud, Web, API & Microservices Standards             |
| 15_DOMAIN_HARDWARE_SEMICONDUCTOR.md       | 15      | Domain: Hardware & Semiconductor Interface Standards          |
| 16_DOMAIN_AI_ML_DATA.md                   | 16      | Domain: AI, ML, Data Exchange & Analytics Standards           |
| 17_DOMAIN_SAFETY_CRITICAL.md              | 17      | Domain: Safety-Critical, Functional Safety & Reliability      |
| 18_CROSSWALK_RELATIONSHIP_TABLES.md       | 18      | Crosswalk Tables & Standards Relationship Maps                |
| 19_MASTER_CLASSIFICATION_MATRIX.md        | 19      | Master Classification Matrix (All Standards, All Tiers)       |
| 20_ENTRY_TEMPLATE_GUIDE.md                | 20      | Entry Template Guide & Authoring Standards                    |

---

## UNIVERSAL ENTRY TEMPLATE (REFERENCE — USE IN ALL CHAPTERS)

Every encyclopedia entry MUST include these fields. Do not abbreviate or skip any field.

```
### [STANDARD / ORGANIZATION NAME]

| Field                        | Value                                      |
|------------------------------|--------------------------------------------|
| Full Name                    |                                            |
| Abbreviation / Alias         |                                            |
| Entry Type                   | [Body / Standard / Protocol / Format / IDL / Framework / Regulation / API / Encoding / Certification] |
| Governance Tier              | [L0–L8]                                    |
| Governing Organization       |                                            |
| Parent / Umbrella Body       |                                            |
| Founded / Published          |                                            |
| Current Version / Edition    |                                            |
| Specification Availability   | [Public Free / Public Paid / Restricted / Proprietary] |
| Open / Proprietary / Mixed   |                                            |
| Formal / Informal            |                                            |
| Scope                        | [Global / Regional / Industry / Vendor / Community] |
| Primary Domain               |                                            |
| Secondary Domains            |                                            |
| Industry Relevance           |                                            |
| Cross-Platform               | [Yes / Partial / No]                       |
| Cross-Language               | [Yes / Partial / No]                       |
| Cross-Domain                 | [Yes / Partial / No]                       |
| Automotive Relevance         | [High / Medium / Low / None]               |
| Cloud Relevance              | [High / Medium / Low / None]               |
| Embedded Relevance           | [High / Medium / Low / None]               |
| Safety-Critical Relevance    | [High / Medium / Low / None]               |
| Enterprise Relevance         | [High / Medium / Low / None]               |
| Compliance / Certification   |                                            |
| Versioning Model             |                                            |
| Adoption Model               |                                            |
| Competing / Adjacent Bodies  |                                            |
| Relationship to Other Stds   |                                            |
| Typical Use Cases            |                                            |
| Technology Stack Position    |                                            |
| Implementation Maturity      | [Emerging / Established / Mature / Legacy] |
| Notes                        |                                            |
```

---

## MANDATORY CLASSIFICATION RULES

These rules MUST be applied in every chapter and every entry:

### Rule 1 — Governance First
Classify by governance authority, not by technology function.
A REST API spec owned by a vendor is L6 or L7, not an "internet standard."

### Rule 2 — Distinguish Roles Precisely
Always distinguish:
- **Owner**: who controls the specification
- **Publisher**: who publishes the document
- **Certifier**: who runs the compliance/certification program
- **Implementor**: who builds software/hardware conforming to it
- **Adopter**: the industry or ecosystem that uses it

### Rule 3 — Distinguish Technology Artifact Types
Always distinguish:
- **Specification** — normative document defining behavior
- **Protocol** — communication exchange rules
- **Format** — data structure definition
- **IDL** — interface definition language
- **Encoding** — binary or text serialization rules
- **Schema** — structural data model
- **API** — programmatic interface definition
- **Framework** — implementation guidance
- **Certification** — conformance/compliance program
- **Regulation** — legally enforceable rule

### Rule 4 — Avoid Common Confusions
Explicitly clarify when an entry is:
- A standards body (not a product)
- A protocol (not a format)
- An open standard (not a de-facto)
- A vendor-led open spec (not a neutral consortium standard)
- A regulatory requirement (not an industry recommendation)

### Rule 5 — Stack Relationships
For layered standards, always show the stack:
```
Governing Body → Standard Family → Specification Version → Reference Implementation → Certification Program → Compliance Requirement
```

---

## OUTPUT QUALITY REQUIREMENTS

For EACH generated chapter:

1. **Precision** — Use exact names, version numbers, dates, and governance structures
2. **Completeness** — Cover every major body, standard, and technology in that domain
3. **Depth** — Explain how the governance model works, not just what the standard is
4. **Tables** — Every chapter must contain structured comparison tables
5. **Crosslinks** — Reference related entries in other chapters by name
6. **Lifecycle** — For major standards, show the full standardization lifecycle
7. **Disambiguation** — Explicitly state what the standard is NOT, when confusion is common
8. **No vague prose** — Replace all vague language with structured fields and tables

---

## STANDARDIZATION LIFECYCLE MODEL

For all major standards bodies and standard families, cover this lifecycle:

```
1. Research / Incubation
2. Proposal / Submission
3. Working Group Formation
4. Initial Draft
5. Public Review / Industry Feedback
6. Ballot / Voting Process
7. Ratification / Approval
8. Publication
9. Reference Implementation
10. Conformance Testing
11. Certification Program
12. Compliance Requirement (regulatory/contractual)
13. Revision / Amendment
14. Deprecation / Supersession
```

---

## SPECIAL DISAMBIGUATION PAIRS (MANDATORY COVERAGE)

Every chapter where these pairs appear MUST explicitly disambiguate:

| Pair                                          | Common Confusion                                    |
|-----------------------------------------------|-----------------------------------------------------|
| Standards body vs implementation vendor       | IEEE is not a software company                      |
| Protocol vs format                            | HTTP is a protocol; JSON is a format                |
| IDL vs serialization format                   | Protobuf IDL vs Protobuf wire encoding              |
| Schema vs encoding                            | XML Schema vs XML encoding                          |
| Open standard vs de-facto standard            | TCP/IP is both; YAML was de-facto before RFC        |
| Consortium std vs vendor-led open std         | AUTOSAR vs Google Protobuf                          |
| Regulatory standard vs industry spec          | GDPR (law) vs ISO 27001 (certification framework)  |
| Platform-owned API vs neutral standard        | Android API vs POSIX API                            |
| Community project vs foundation-governed std  | Linux kernel vs a random GitHub project             |
| Certification vs compliance vs conformance    | ISO 9001 certification vs GDPR compliance           |

---

## GENERATION INSTRUCTIONS (HOW TO USE THESE PROMPTS)

### Step 1 — Read MASTER_PROMPT.md first (this file)
Load the master role, governance hierarchy, entry template, rules, and quality bar.
This is your persistent system context for the entire encyclopedia project.

### Step 2 — Load the specific chapter prompt
Each chapter file (01–20) is a self-contained generation prompt for that chapter.
The chapter prompt inherits all master rules without repeating them.

### Step 3 — Generate chapter output
For each chapter prompt, produce:
- Chapter overview
- Scope boundary (what this chapter covers and what it defers to other chapters)
- All encyclopedia entries using the Universal Entry Template
- Domain-specific comparison tables
- Standardization lifecycle for the major body(ies) in scope
- Disambiguation notes for common confusions in that domain
- Chapter summary and crosslinks to other chapters

### Step 4 — Combine into master encyclopedia
After all 20 chapters are generated, consolidate:
- Chapter 18: Crosswalk tables linking all bodies and standards
- Chapter 19: Master classification matrix (all entries, all tiers, all domains)
- Chapter 20: Style guide and entry template reference

---

## CROSS-DOMAIN RELEVANCE MATRIX (SEED)

Use this matrix as a seed for Chapter 18 and Chapter 19 expansion:

| Domain           | L1 Bodies          | L3 Consortia         | L5 Foundations     | L6 Vendor-Led      | L8 De-facto        |
|------------------|--------------------|----------------------|--------------------|--------------------|---------------------|
| Networking       | ISO, ITU-T         | IETF, IEEE 802       | Linux Foundation   | Google gRPC        | TCP/IP, DNS         |
| Web              | ISO, W3C           | W3C, WHATWG          | Mozilla            | Google, Apple      | HTML5, REST         |
| Automotive       | ISO, IEC           | AUTOSAR, COVESA      | Eclipse Automotive | NVIDIA DRIVE       | CAN bus             |
| Cybersecurity    | ISO, IEC           | NIST, FIDO Alliance  | Linux Foundation   | Google BeyondCorp  | TLS, x.509          |
| Cloud            | ISO, NIST          | CNCF, OCI, OpenFog   | Linux Foundation   | AWS, Azure, GCP    | Docker, Kubernetes  |
| AI/ML            | ISO/IEC JTC1       | MLCommons, ONNX      | Linux Foundation   | Google TF, OpenAI  | Python, Jupyter     |
| Hardware/Semi    | IEEE, IEC          | PCI-SIG, USB-IF, JEDEC | RISC-V Foundation | NVIDIA CUDA        | x86, ARM            |
| Safety-Critical  | IEC, ISO           | SAE, EUROCAE         | —                  | —                  | MISRA C             |
| Telecom          | ITU, IEEE          | 3GPP, ETSI           | —                  | Ericsson, Nokia    | SIP, RTP            |
| Enterprise       | ISO, IEC           | OMG, OASIS           | Apache             | SAP, Oracle        | REST, JSON          |

---

## FINAL DELIVERABLE CHECKLIST

When all 20 chapters are complete, verify:

- [ ] All 9 governance tiers represented with at least 5 entries each
- [ ] All 10 domain sections covered (networking, web, automotive, security, cloud, AI, hardware, safety, telecom, enterprise)
- [ ] All entries use the Universal Entry Template with no missing fields
- [ ] All major standards bodies have full lifecycle descriptions
- [ ] All disambiguation pairs explicitly addressed
- [ ] Crosswalk table covers ≥ 100 entries across all tiers and domains
- [ ] Master classification matrix covers ≥ 200 entries
- [ ] No vague prose — all descriptions use structured fields
- [ ] All governance tier assignments are justified with rationale
- [ ] Competing and adjacent standards are cross-referenced in every entry

---
*This is the master control document. All chapter prompts inherit from this context.*
*Always load MASTER_PROMPT.md before executing any individual chapter prompt.*
