# EU Cyber Resilience Act (CRA) — SBOM & Vulnerability Management Requirements

**Topic:** European Union Cyber Resilience Act (EU CRA), mandatory SBOM requirements for products with digital elements, vulnerability handling obligations, ENISA reporting, CE marking for cybersecurity, and global regulatory impact  
**Standard:** Regulation (EU) 2024/2847 — Cyber Resilience Act; EN 18031 series (harmonized standards); IEC 62443 (supporting); ISO/IEC 27001 (supporting)  
**SDO:** European Commission; European Parliament; ENISA (European Union Agency for Cybersecurity); CEN/CENELEC (harmonized standards)  
**Audience:** Product security engineers, compliance managers, legal counsel, PSIRT teams, embedded systems developers, IoT product managers, OSPO teams, regulatory affairs professionals  
**Prerequisites:** SBOM fundamentals (SPDX/CycloneDX), vulnerability management basics, EU regulatory framework (CE marking), product development lifecycle, open source governance

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 2019 | EU Cybersecurity Act (Regulation 2019/881) | Created ENISA permanent mandate; EU cybersecurity certification framework; foundation for CRA |
| 2020 | EU Cybersecurity Strategy for the Digital Decade | Flagged need for horizontal cybersecurity requirements for ALL connected products |
| 2021 | US Executive Order 14028 ("Improving the Nation's Cybersecurity") | US mandates SBOM for federal procurement; sets global precedent; EU takes notice |
| 2021 | Log4Shell (CVE-2021-44228) | Demonstrates devastating impact of supply chain vulnerabilities; accelerates regulatory urgency globally |
| 2022 | SolarWinds aftermath; continued supply chain attacks | Political momentum for mandatory product security requirements |
| 2022-09 | EU Commission publishes **CRA proposal** | First draft of Cyber Resilience Act; industry consultation begins |
| 2023-07 | European Parliament approves negotiating position | Amendments including stronger SBOM requirements; open source concerns addressed |
| 2023-11 | EU Council and Parliament reach political agreement | Trilogue concluded; final text agreed; open source steward concept added |
| 2024-03 | European Parliament votes to adopt CRA | Overwhelming approval (517 for, 12 against) |
| 2024-10 | **CRA published** in Official Journal (Regulation 2024/2847) | Enters into force 20 days after publication |
| 2024-11 | **CRA enters into force** | 36-month transition period begins |
| 2025 | Reporting obligations take effect (24 months early) | Vulnerability exploitation and incident reporting to ENISA |
| 2026 | Harmonized standards (EN 18031 series) expected | CEN/CENELEC develops standards; provides presumption of conformity |
| **2027-12** | **Full enforcement** | All products with digital elements must comply; CE marking includes CRA; market surveillance begins |

### 1.2 Why the CRA Was Created

| Problem | Impact | CRA Solution |
|---------|--------|:---:|
| No horizontal EU cybersecurity requirements for products | Products sold without basic security; no lifecycle security obligation | Mandatory essential requirements for ALL products with digital elements |
| Vulnerability management failures | Vendors abandon products; no patching obligation; users left exposed | **5-year minimum** security update obligation; vulnerability handling process |
| Supply chain opacity | Manufacturers don't know what's in their products; can't assess risk | **SBOM mandatory** — machine-readable; maintained throughout lifecycle |
| Incident response failures | Exploited vulnerabilities not reported; others remain exposed | **24-hour** reporting to ENISA for actively exploited vulnerabilities |
| Market fragmentation | 27 member states potentially creating different national rules | Single EU-wide regulation; CE marking; uniform enforcement |
| Open source ecosystem concerns | Early drafts threatened open source contributions; community alarm | **Open source steward** concept; non-commercial exceptions; proportionate obligations |

---

## Chapter 2 — CRA Scope & Architecture

### 2.1 What Does the CRA Cover?

| In Scope | Out of Scope |
|----------|:---:|
| ANY product with digital elements (hardware OR software) placed on EU market | Already regulated: medical devices (MDR); vehicles (type approval); aviation; military |
| IoT devices; consumer electronics; operating systems; firmware | Open source developed/supplied non-commercially (see nuances below) |
| Enterprise software; cloud-managed devices; routers; smart home | Pure SaaS/cloud services (covered by NIS2 instead) |
| Libraries/SDKs when distributed commercially | Internal tools not placed on market |
| App store applications | Government/national security systems |
| Software components in hardware products | Research and development prototypes (not placed on market) |

### 2.2 Product Categories

| Category | Examples | Conformity Assessment | Standard |
|:--------:|----------|:---:|:---:|
| **Default** (90%+ of products) | Smart TVs; toys; home routers; basic IoT; consumer apps; productivity software | Self-assessment (Module A) | Harmonized standards (EN 18031) |
| **Important — Class I** | Identity management software; VPNs; password managers; firewalls (consumer); SIEM; network management; boot managers; disk encryption; microcontrollers; ICS | Self-assessment IF harmonized standard exists; otherwise third-party (Module B+C or Module H) | EN 18031 or equivalent |
| **Important — Class II** | Hypervisors; container runtime; firewalls (industrial); intrusion detection; tamper-resistant microprocessors; HSMs; smartcard readers; robotic sensing/actuating | Third-party assessment required (Module B+C or Module H) | EN 18031 or equivalent |
| **Critical** | Hardware devices with security boxes (smart cards, secure elements); smart meter gateways; (Commission can add via delegated acts) | European cybersecurity certification (under Cybersecurity Act) | Certification scheme |

### 2.3 Key Obligations by Role

| Role | Key Obligations |
|------|----------------|
| **Manufacturer** | Design security by default; conduct risk assessment; vulnerability handling process; SBOM; 5-year update obligation; CE marking; EU Declaration of Conformity; reporting to ENISA; provide security info to users |
| **Importer** | Verify manufacturer compliance; ensure CE marking present; maintain documentation; report to market surveillance if product non-compliant |
| **Distributor** | Verify CE marking; don't supply non-compliant products; inform authorities of risks |
| **Open Source Steward** | Cybersecurity policy; cooperate with market surveillance; facilitate vulnerability handling; NOT full manufacturer obligations; proportionate to role |

---

## Chapter 3 — SBOM Requirements Under CRA

### 3.1 CRA SBOM Mandate

| Requirement | CRA Text Reference | Detail |
|-------------|:---:|--------|
| **SBOM mandatory** | Article 13(5); Annex I Part II(1) | Manufacturers shall identify and document components contained in products, including by drawing up an SBOM |
| **Machine-readable format** | Annex I Part II(1) | SBOM in commonly used and machine-readable format covering at minimum top-level dependencies |
| **Minimum top-level dependencies** | Annex I Part II(1) | At minimum: top-level dependencies (not every transitive dependency required, but recommended) |
| **Available to market surveillance** | Article 13(5) | SBOM provided to market surveillance authorities upon request; NOT mandatory public disclosure |
| **Updated throughout lifecycle** | Implied by vulnerability handling + update obligations | SBOM must reflect current state of product as components updated |
| **Part of technical documentation** | Annex V | SBOM is part of technical documentation maintained by manufacturer |

### 3.2 SBOM Minimum Content (CRA + ENISA Guidance)

| Element | Required? | Recommended Format |
|---------|:---------:|:---:|
| Component name | ✅ Mandatory | SPDX PackageName / CycloneDX component.name |
| Component version | ✅ Mandatory | Semantic version; SPDX PackageVersion |
| Unique identifier | ✅ Mandatory | PURL (Package URL); CPE; SPDX identifier |
| Supplier / Manufacturer | ✅ Mandatory | SPDX Supplier; CycloneDX supplier |
| Dependency relationship | ✅ Mandatory (top-level) | SPDX relationships; CycloneDX dependencies |
| License information | Recommended | SPDX License (aids compliance assessment) |
| Hash / checksum | Recommended | SHA-256 for integrity verification |
| Component type | Recommended | Library; framework; application; OS; firmware |
| Transitive dependencies | Recommended (not mandatory) | Full dependency tree preferred for vulnerability management |
| Timestamp | Recommended | When SBOM was generated; enables freshness tracking |

### 3.3 SBOM Format Expectations

| Aspect | Expectation |
|--------|-------------|
| **Format** | SPDX (ISO/IEC 5962) or CycloneDX recommended; CRA does NOT mandate specific format (yet) |
| **Frequency** | Generated at release; updated when components change; maintained throughout support period |
| **Distribution** | NOT required to be public; available to market surveillance authorities upon request; recommended to provide to enterprise customers |
| **Scope** | Product-level SBOM; at minimum top-level dependencies; full SBOM (transitive) recommended for effective vulnerability management |
| **Automation** | Expected to be automated in build process; manual maintenance impractical at required scale |

---

## Chapter 4 — Vulnerability Handling Requirements

### 4.1 Vulnerability Handling Process (Annex I Part II)

| Requirement | Detail | Deadline |
|-------------|--------|:---:|
| **Vulnerability handling policy** | Documented process for receiving, verifying, and addressing vulnerabilities; coordinated disclosure support | From market entry |
| **Contact point** | Clear, accessible contact for vulnerability reports | From market entry |
| **Effective handling** | Without delay; identify affected components; apply and verify fix; share info to help mitigation | Ongoing |
| **Regular security testing** | Regular testing and review of product security | Ongoing |
| **Security updates** | Provide free security updates for at minimum the expected product lifetime or 5 years (whichever shorter) | 5 years from market entry |
| **Update mechanism** | Automatic update mechanism with user opt-out; OR clear notification and easy manual update process | From market entry |
| **Disclosure of fixed vulnerabilities** | After patch available: publish info about fixed vulnerability including CVE; component affected; impact; severity | Without undue delay |
| **SBOM maintenance** | Maintain SBOM; use it to identify/track components affected by new vulnerabilities | Ongoing |

### 4.2 ENISA Reporting Obligations

| Event | Report To | Deadline | Content |
|-------|:---------:|:--------:|---------|
| **Actively exploited vulnerability discovered** | ENISA (single reporting platform) + National CSIRT | **24 hours** (early warning) | Manufacturer identity; product; general description; severity estimate; member states potentially affected |
| **Follow-up notification** | ENISA + National CSIRT | **72 hours** | General nature of exploit; corrective measures taken/planned; whether vulnerability was already reported by third party |
| **Final report** | ENISA + National CSIRT | **14 days** (or with fix notification) | Description of vulnerability; severity + impact; root cause if available; corrective measures applied; where applicable: CVE or equivalent identifier |
| **Severe incident affecting product security** | ENISA + National CSIRT | Same timelines as above (24h/72h/14d) | Impact assessment; incident type; affected products/versions |

### 4.3 Comparison: EU CRA vs. US Requirements

| Aspect | EU CRA (2024/2847) | US EO 14028 / CISA | Key Difference |
|--------|:---:|:---:|---|
| **Scope** | ALL products with digital elements on EU market | Federal government procurement only (initially) | CRA much broader — applies to entire market, not just government |
| **SBOM** | Mandatory; machine-readable; top-level minimum | Mandatory for federal suppliers; NTIA minimum elements | Similar content; CRA = legal obligation; US = procurement requirement |
| **Reporting** | 24-hour to ENISA for exploited vulns | Varies (CISA KEV; sector-specific) | CRA has explicit legal timelines; US more fragmented |
| **Updates** | 5-year minimum free security updates | Not explicitly mandated in regulation | CRA stronger on update obligation |
| **Enforcement** | Fines up to €15M or 2.5% global turnover | Contract penalties; debarment | CRA has much stronger penalties |
| **Open source** | Steward concept; non-commercial exemption | SBOM required regardless (for suppliers) | CRA more nuanced re: open source |
| **Timeline** | Full enforcement: December 2027 | Ongoing (phased since 2022) | US ahead on implementation; EU will be broader when enforced |
| **Conformity** | CE marking; self-assessment or third-party | Self-attestation (CISA secure-by-design pledge) | CRA: legal conformity; US: softer attestation |

---

## Chapter 5 — Compliance Implementation Roadmap

### 5.1 Implementation Timeline

| Phase | Timeline | Actions |
|:-----:|----------|---------|
| **Phase 0: Awareness** | Now — 2025 | Understand CRA scope; identify affected products; assess current state; executive buy-in; budget planning |
| **Phase 1: Gap Assessment** | 2025 | Map current security practices vs. CRA requirements; identify gaps (SBOM? Vulnerability handling? Reporting?); prioritize |
| **Phase 2: SBOM Program** | 2025-2026 | Deploy SBOM tooling in CI/CD; establish SBOM generation for all products; define format (SPDX/CycloneDX); test completeness |
| **Phase 3: Vulnerability Handling** | 2025-2026 | Establish/improve PSIRT; define vulnerability handling policy; set up coordinated disclosure process; implement patch management |
| **Phase 4: Reporting Infrastructure** | 2026 | Connect to ENISA reporting platform; define internal escalation process; test 24-hour reporting capability; train staff |
| **Phase 5: Conformity Assessment** | 2026-2027 | Prepare technical documentation; conduct risk assessment per product; self-assessment (Module A) for default products; engage notified body for Class I/II if needed |
| **Phase 6: CE Marking** | 2027 | Complete conformity assessment; issue EU Declaration of Conformity; apply CE marking; place compliant products on market |
| **Phase 7: Steady State** | 2027+ | Continuous: SBOM updates; vulnerability monitoring; security updates; ENISA reporting; market surveillance cooperation |

### 5.2 Technical Implementation Checklist

| Area | Actions | Tools |
|------|---------|-------|
| **SBOM generation** | Integrate into CI/CD; automate per-build; cover all products; validate completeness | Syft; cdxgen; Trivy; language-specific plugins |
| **SBOM management** | Store and version SBOMs; associate with product releases; maintain accessibility for authorities | Dependency-Track; artifact repository; SBOM lifecycle management |
| **Vulnerability monitoring** | Continuous matching of SBOM components against vulnerability databases; alert on new CVEs | Dependency-Track; Grype; osv-scanner; NVD/OSV feeds |
| **Vulnerability handling** | Triage process; severity assessment; patch development; coordinated disclosure support | JIRA/issue tracker; PSIRT playbooks; CVSS scoring |
| **Security updates** | Patch delivery mechanism; automatic updates or notification; 5-year commitment | OTA update infrastructure; update notification system |
| **ENISA reporting** | Integration with ENISA single reporting platform; internal escalation process; 24h capability | Incident management tools; on-call; reporting templates |
| **Technical documentation** | Risk assessment per product; security requirements mapping; SBOM; design documentation; conformity evidence | Documentation management; compliance tracking |
| **VEX** | Communicate vulnerability status to customers; maintain VEX documents per product | OpenVEX; CycloneDX VEX; CSAF advisories |

---

## Chapter 6 — Open Source & the CRA

### 6.1 Open Source Provisions

| Concept | CRA Treatment | Implication |
|---------|---------------|-------------|
| **Non-commercial open source** | EXCLUDED from manufacturer obligations | Hobby projects; community-maintained software not placed on market commercially → not covered |
| **Commercial open source** | COVERED as manufacturer | Open source software placed on market in context of commercial activity (even if free) = must comply |
| **"Placed on market" test** | Revenue; commercial context; integration into commercial product | If open source is part of a commercial product, the MANUFACTURER (integrator) bears responsibility |
| **Open Source Steward** | NEW concept: legal person that systematically provides support for open source used in commercial products | Lighter obligations: cybersecurity policy; cooperation with authorities; facilitate vulnerability handling. NOT full manufacturer obligations |
| **Examples of Stewards** | Linux Foundation; Apache Software Foundation; Eclipse Foundation; potentially large corporate maintainers | Must have documented security policy; cooperate with market surveillance; help coordinate vulnerability handling |
| **Steward obligations** | (1) Cybersecurity policy for secure development; (2) cooperate with market surveillance; (3) facilitate vulnerability handling; (4) provide SBOM information when feasible | Proportionate to role; no CE marking; no conformity assessment; lighter penalties |

### 6.2 Impact on Open Source Ecosystem

| Stakeholder | Impact | Required Action |
|-------------|--------|----------------|
| **Individual contributors** | Minimal direct impact; non-commercial contribution exempt | No action required for hobby/volunteer work |
| **Open source foundations** | May be classified as "Open Source Steward"; lighter obligations | Establish cybersecurity policy; facilitate vuln handling; potentially provide SBOM |
| **Companies using open source** | FULL manufacturer obligations for products incorporating open source | Generate SBOMs including open source components; vulnerability monitoring; patching; reporting |
| **Companies contributing to open source** | Depends on commercial context of contribution | If contributing to project used in own commercial product, consider vuln handling responsibilities |
| **Package maintainers (funded/commercial)** | May be covered if activity is commercial | Assess whether activity constitutes "placing on market in context of commercial activity" |

---

## Chapter 7 — Penalties & Enforcement

### 7.1 Penalties

| Violation | Maximum Fine | Comparison |
|-----------|:---:|:---:|
| Essential cybersecurity requirements (Annex I) | **€15,000,000** or **2.5% of global annual turnover** (whichever higher) | Similar to GDPR-level |
| Other CRA obligations (reporting, documentation, SBOM) | **€10,000,000** or **2% of global annual turnover** | Significant for non-compliance with SBOM or reporting |
| Providing incorrect/misleading information to authorities | **€5,000,000** or **1% of global annual turnover** | Dishonesty about compliance status |
| **Open Source Steward penalties** | Proportionate; "appropriate to the nature of the open source steward" | Explicitly lighter than manufacturer penalties |

### 7.2 Enforcement Mechanisms

| Mechanism | Authority | Action |
|-----------|:---------:|--------|
| **Market surveillance** | National authorities (each EU member state) | Inspect products; request documentation (including SBOM); order corrective measures; restrict/withdraw products from market |
| **EU Declaration of Conformity** | Manufacturer self-declares | False declaration = legal liability; subject to verification |
| **CE marking** | Market surveillance authorities verify | Products without CE marking (post-2027) = cannot be legally placed on EU market |
| **Product recall/withdrawal** | Market surveillance | Authority can order product withdrawn from market if non-compliant and risk to cybersecurity |
| **ENISA coordination** | ENISA facilitates cross-border enforcement | Shared vulnerability databases; coordinated market surveillance |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 CRA Compliance Architecture

```mermaid
graph TB
    subgraph "Product Development"
        DESIGN[Secure by Design<br/>━━━━━━━━━━━<br/>Risk assessment<br/>Security requirements<br/>Threat modeling<br/>Annex I Part I]
        
        DEV[Development<br/>━━━━━━━━━━━<br/>Secure coding<br/>Dependency management<br/>Automated testing]
        
        SBOM_GEN[SBOM Generation<br/>━━━━━━━━━━━<br/>Automated in CI/CD<br/>CycloneDX / SPDX<br/>Top-level + transitive]
        
        CONFORM[Conformity Assessment<br/>━━━━━━━━━━━<br/>Module A (self) or<br/>Module B+C (third-party)<br/>Technical documentation]
    end
    
    subgraph "Market Entry"
        CE[CE Marking<br/>━━━━━━━━━━━<br/>EU DoC<br/>CE mark applied<br/>Product placed on market]
    end
    
    subgraph "Post-Market (5 years)"
        MONITOR[Vulnerability Monitoring<br/>━━━━━━━━━━━<br/>SBOM vs NVD/OSV<br/>Continuous matching<br/>New CVE detection]
        
        HANDLE[Vulnerability Handling<br/>━━━━━━━━━━━<br/>Triage; patch; test;<br/>coordinated disclosure;<br/>without undue delay]
        
        UPDATE[Security Updates<br/>━━━━━━━━━━━<br/>Free patches; 5 years<br/>Automatic or notified<br/>Easy installation]
        
        REPORT[ENISA Reporting<br/>━━━━━━━━━━━<br/>24h: actively exploited<br/>72h: follow-up<br/>14d: final report]
        
        VEX_PUB[VEX Publication<br/>━━━━━━━━━━━<br/>Communicate status<br/>per vulnerability<br/>to customers]
    end
    
    DESIGN --> DEV --> SBOM_GEN --> CONFORM --> CE
    CE --> MONITOR --> HANDLE --> UPDATE
    HANDLE --> REPORT
    HANDLE --> VEX_PUB
    MONITOR -.->|"new CVE found"| HANDLE
```

### 8.2 Reporting Flow

```mermaid
sequenceDiagram
    participant PROD as Product/PSIRT Team
    participant ENISA as ENISA Platform
    participant CSIRT as National CSIRT
    participant MARKET as Market Surveillance
    participant CUSTOMER as Customers/Users
    
    Note over PROD: Actively exploited vulnerability<br/>discovered (Day 0)
    
    PROD->>ENISA: Early Warning (≤24 hours)<br/>Product; severity; affected states
    ENISA->>CSIRT: Forward to relevant national CSIRTs
    
    PROD->>ENISA: Follow-up (≤72 hours)<br/>Nature of exploit; corrective measures
    ENISA->>CSIRT: Updated notification
    
    PROD->>PROD: Develop patch; test; prepare advisory
    
    PROD->>ENISA: Final Report (≤14 days)<br/>Full description; CVE; root cause; fix
    ENISA->>CSIRT: Final notification
    
    PROD->>CUSTOMER: Security advisory<br/>Patch available; update instructions
    PROD->>CUSTOMER: VEX document update<br/>(status: fixed in version X)
    
    Note over ENISA: ENISA may share sanitized info<br/>with other manufacturers
    
    opt Non-compliance detected
        ENISA->>MARKET: Flag for market surveillance
        MARKET->>PROD: Corrective action request
    end
```

---

## Chapter 9 — Case Studies

### 9.1 Case Study: IoT Manufacturer CRA Compliance Program

| Aspect | Detail |
|--------|--------|
| Company | Smart home device manufacturer; 15 products; Embedded Linux (Yocto); Bluetooth/WiFi/Zigbee; sold across EU |
| Starting state | No formal SBOM process; firmware updates manual (user must download from website); no vulnerability handling process; no PSIRT; basic security testing |
| CRA classification | Default category (consumer IoT); self-assessment (Module A) sufficient |
| Compliance program | **Year 1 (2025)**: (1) Hired product security engineer. (2) SBOM pilot: integrated Syft into Yocto build (create-spdx class for Yocto recipes + Syft for container-based cloud services). (3) Established vulnerability monitoring: SBOMs uploaded to Dependency-Track; daily matching against NVD/OSV. (4) Created VDP (Vulnerability Disclosure Policy); security@company email. **Year 2 (2026)**: (5) Full PSIRT established (2 people + engineering support). (6) Automated OTA update mechanism deployed to all products (secure; signed; rollback capable). (7) Vulnerability handling process documented and tested. (8) ENISA reporting process defined and drilled (tabletop exercise). (9) Risk assessment per product completed. **Year 3 (2027)**: (10) Technical documentation compiled per product. (11) Conformity assessment (self-assessment Module A). (12) EU Declaration of Conformity issued. (13) CE marking applied (including CRA cybersecurity). (14) First new products placed on market under CRA compliance. |
| Investment | Year 1: €150K (1 hire + tooling). Year 2: €250K (PSIRT + OTA infrastructure). Year 3: €100K (documentation + conformity). Total 3-year: ~€500K |
| Challenges | (1) Yocto SBOM completeness: some binary blobs (GPU drivers) not fully decomposable → documented as "supplier-provided component" with supplier SBOM requested. (2) Legacy products: 5 products already on market without SBOM → retrofit SBOM generated from firmware analysis; ongoing support committed. (3) 5-year update commitment: product hardware lifecycle planning needed (some products previously abandoned after 2 years). |

### 9.2 Case Study: Open Source Foundation as "Steward"

| Aspect | Detail |
|--------|--------|
| Organization | Major open source foundation hosting 50+ projects used in commercial products worldwide |
| CRA status | Classified as "Open Source Steward" — systematically supports open source used in commercial context |
| Obligations | (1) Cybersecurity policy for secure development. (2) Cooperate with market surveillance authorities. (3) Facilitate vulnerability handling for hosted projects. (4) Lighter penalties than manufacturers |
| Actions taken | (1) **Cybersecurity policy published**: secure development lifecycle for all projects; mandatory security review for releases; signed releases; reproducible builds encouraged. (2) **Vulnerability handling**: coordinated disclosure process across all projects; central security team for routing; 90-day disclosure policy; CVE assignment (CNA). (3) **SBOM infrastructure**: tooling available for projects to generate SBOM at release; not mandatory but strongly encouraged; foundation provides CI templates. (4) **Authority cooperation**: designated contact for market surveillance; will provide information on request; not liable for product-level compliance (manufacturer responsibility). (5) **Community education**: guidance for contributors on CRA implications; FAQ on when contributions might be "commercial". |
| Key insight | Foundation provides INFRASTRUCTURE for security (tools, processes, coordination) but MANUFACTURERS (companies using the open source in products) bear compliance responsibility for their products. Foundation is facilitator, not guarantor. |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **Harmonized standards published** | 2025-2026 | EN 18031 series provides presumption of conformity; gives manufacturers clear technical requirements; enables self-assessment |
| **ENISA single reporting platform** | 2025-2026 | Operational platform for 24h/72h/14d reporting; cross-border coordination; vulnerability intelligence sharing |
| **Market surveillance begins** | 2027-2028 | First enforcement actions; product withdrawals; fines; creates compliance urgency |
| **Global regulatory alignment** | 2025-2030 | Other jurisdictions (UK PSTI; Japan; Australia; Singapore) align with CRA concepts; SBOM becoming global norm |
| **CRA delegated acts** | 2025-2028 | Commission can add product categories; specify SBOM format details; update essential requirements |
| **SBOM format standardization** | 2026-2027 | CRA may specify mandatory format (SPDX and/or CycloneDX); currently format-neutral but "machine-readable" required |
| **Automated conformity tools** | 2026-2028 | Tools that check CRA conformity automatically; generate technical documentation; automate SBOM quality assessment |
| **Supply chain cascade effect** | 2025-2028 | Manufacturers require SBOMs from component suppliers; contractual requirements cascade up supply chain; Tier-2/3 suppliers must provide SBOMs |
| **CRA + NIS2 convergence** | 2025-2027 | NIS2 (for operators/services) + CRA (for products) create complete coverage; organizations subject to both coordinate compliance |
| **Sector-specific guidance** | 2026-2028 | Industry-specific interpretations: automotive (UNECE); medical devices (MDR overlap); industrial (IEC 62443 alignment) |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is the EU Cyber Resilience Act and what are its SBOM requirements?  
**A:** The EU Cyber Resilience Act (CRA, Regulation 2024/2847) is a European Union regulation that establishes mandatory cybersecurity requirements for ALL products with digital elements (hardware and software) sold on the EU market. It was created because previously there were no horizontal (cross-sector) cybersecurity requirements for products — manufacturers could sell insecure products with no legal obligation to provide security updates or manage vulnerabilities. Regarding SBOM: the CRA MANDATES that manufacturers create and maintain a Software Bill of Materials (SBOM) in a commonly used, machine-readable format. The SBOM must cover at minimum the top-level dependencies of the product. It's part of the technical documentation and must be available to market surveillance authorities upon request. The SBOM serves as the foundation for vulnerability management — manufacturers must use it to track which of their components are affected when new CVEs are published. Key timelines: reporting obligations from 2025/2026; full enforcement including SBOM from December 2027. Non-compliance penalties: up to €15M or 2.5% of global turnover.

### Tier 2: Mid-Level

**Q2:** Compare the CRA's approach to vulnerability handling with US Executive Order 14028 requirements. How would you design a compliance program that satisfies both?  
**A:** [Detailed answer covering: CRA scope (entire EU market vs. US federal procurement only); CRA reporting (24h to ENISA vs. no explicit federal timeline in US); CRA update obligation (5 years mandatory vs. US contractual/best-practice); SBOM requirements (similar content; CRA mandates top-level minimum vs. NTIA minimum elements); enforcement (CRA: fines + market withdrawal vs. US: contract penalties + debarment); combined program design (single SBOM process using CycloneDX; single PSIRT handling both EU reporting and US CISA coordination; single vulnerability monitoring platform; documentation that maps to both requirements); open source handling (CRA steward concept vs. US supplier requirement); practical implementation (Dependency-Track for monitoring + both regulatory reporting capabilities; VEX for both EU transparency and US CISA guidance compliance).]

### Tier 3: Senior

**Q3:** You're the Head of Product Security at a company with 200 products across IoT, industrial, and cloud services, selling globally (EU, US, Asia). The CRA enforcement deadline is 18 months away. Design the compliance program: organizational structure, technical architecture, timeline, budget justification, and risk mitigation strategy.  
**A:** [Comprehensive answer covering: organizational structure (PSIRT expansion; CRA compliance team; OSPO alignment; legal/regulatory coordination; training); product portfolio triage (categorize all 200 products: default vs. Class I vs. Class II vs. out-of-scope; prioritize by risk); technical architecture (SBOM tooling across all build systems — Yocto, Maven, npm, Go; centralized vulnerability management (Dependency-Track); ENISA reporting integration; automated conformity evidence generation; VEX at scale); parallel compliance (CRA + NIS2 + US EO + sector-specific: UNECE for automotive; IEC 62443 for industrial); timeline planning (12-month sprints: months 1-3 assessment; 4-9 implementation; 10-15 conformity; 15-18 buffer); budget justification (ROI vs. penalty risk: €15M fine vs. €2M program investment; market access risk; competitive advantage); legacy products (retrofit SBOMs; decide: support 5 years or withdraw from market; cost analysis); supplier management (cascade SBOM requirements to 500 component suppliers; contractual updates; supplier assessment); risk mitigation (pre-enforcement engagement with market surveillance; early voluntary reporting; industry association participation for standard development).]

---

## Chapter 12 — Cheat Sheet & Quick Reference

### CRA Compliance Quick Reference

```
SCOPE:
• Products with digital elements placed on EU market
• Hardware + software (firmware, OS, apps, libraries)
• NOT: pure SaaS (NIS2 instead); non-commercial open source; already regulated (medical, automotive type-approval)

KEY DATES:
• 2024-11: CRA enters into force
• 2025/2026: Reporting obligations begin (24h to ENISA)
• 2027-12: FULL ENFORCEMENT (CE marking; market surveillance)

SBOM REQUIREMENTS:
• Mandatory; machine-readable format
• Minimum: top-level dependencies
• Recommended: full transitive; SPDX or CycloneDX
• Available to authorities on request (NOT public mandatory)
• Part of technical documentation

REPORTING TO ENISA:
• 24 hours: actively exploited vulnerability (early warning)
• 72 hours: follow-up (exploit details; corrective measures)
• 14 days: final report (CVE; root cause; fix; impact)

UPDATE OBLIGATION:
• 5 years minimum (or expected product lifetime if shorter)
• Free security updates
• Automatic mechanism or clear notification

PENALTIES:
• Essential requirements: €15M or 2.5% global turnover
• Other obligations (SBOM, reporting): €10M or 2%
• False information: €5M or 1%
• Open Source Stewards: proportionate (lighter)

CONFORMITY ASSESSMENT:
• Default products: Self-assessment (Module A)
• Important Class I: Self-assessment IF harmonized standard; else third-party
• Important Class II: Third-party required
• Critical: EU cybersecurity certification
```

### CRA vs. Other Regulations

```
EU CRA (2024/2847):
  Scope:    Products with digital elements
  SBOM:     Mandatory
  Updates:  5 years
  Report:   24h to ENISA
  Penalty:  €15M / 2.5%
  Enforce:  2027

EU NIS2 (2022/2555):
  Scope:    Essential/important entities (operators, not products)
  SBOM:     Encouraged (via supply chain security)
  Updates:  N/A (service continuity)
  Report:   24h to national authority
  Penalty:  €10M / 2%
  Enforce:  2024 (transposed to national law)

US EO 14028:
  Scope:    Federal procurement (suppliers)
  SBOM:     Mandatory for fed suppliers
  Updates:  Contractual
  Report:   Per CISA guidance
  Penalty:  Contract penalties
  Enforce:  Ongoing since 2022

UK PSTI Act (2022):
  Scope:    Consumer IoT
  SBOM:     Not mandatory
  Updates:  Declare support period
  Report:   N/A
  Penalty:  £10M / 4%
  Enforce:  2024
```

### Implementation Priority Matrix

```
IMMEDIATE (2025):
  □ Identify all products in CRA scope
  □ Classify: Default / Class I / Class II
  □ Start SBOM generation pilot (one product)
  □ Assess current vulnerability handling maturity
  □ Budget approval for compliance program

HIGH PRIORITY (2025-2026):
  □ SBOM tooling deployed across all products
  □ PSIRT established/strengthened
  □ Vulnerability handling process documented
  □ 24h reporting capability tested
  □ Supplier SBOM requirements communicated

REQUIRED FOR COMPLIANCE (2026-2027):
  □ Technical documentation per product
  □ Risk assessment per product
  □ Conformity assessment completed
  □ EU Declaration of Conformity issued
  □ CE marking applied
  □ 5-year update commitment resourced

ONGOING (2027+):
  □ SBOM maintained per release
  □ Vulnerability monitoring (SBOM vs NVD/OSV)
  □ Security updates delivered (5-year window)
  □ ENISA reporting when required
  □ Market surveillance cooperation
```

---

*End of Document — 09_EU_CRA_SBOM_Requirements.md*
