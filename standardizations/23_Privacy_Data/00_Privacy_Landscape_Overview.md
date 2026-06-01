# Privacy & Data Protection — Landscape Overview

**Topic:** Global Privacy & Data Protection Standards Landscape  
**Standards:** GDPR, CCPA/CPRA, ISO/IEC 27701, ISO 29100, NIST Privacy Framework, LGPD, APPI, PIPL, DPDPA, PDPL  
**Domain:** Data Protection, Privacy Engineering, Compliance, Information Governance  
**Audience:** Data Protection Officers (DPOs), privacy engineers, compliance managers, CISO teams, software architects  
**Prerequisites:** Basic understanding of personal data processing; information security awareness

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline of Global Privacy

| Year | Milestone |
|------|-----------|
| 1948 | UN Universal Declaration of Human Rights — Art. 12: right to privacy |
| 1970 | German state of Hesse: world's first data protection law |
| 1973 | US HEW Advisory Committee: Fair Information Practice Principles (FIPPs) |
| 1974 | US Privacy Act (federal agencies) |
| 1980 | OECD Privacy Guidelines — 8 principles (still foundational today) |
| 1981 | Council of Europe Convention 108 — first international data protection treaty |
| 1995 | EU Data Protection Directive 95/46/EC — harmonized EU rules |
| 1998 | US COPPA (children's online privacy); UK Data Protection Act 1998 |
| 2000 | Canada PIPEDA; EU-US Safe Harbor framework |
| 2002 | EU ePrivacy Directive (cookies; electronic communications) |
| 2013 | Snowden revelations — catalyzes global privacy urgency |
| 2015 | Schrems I (CJEU) — invalidates EU-US Safe Harbor |
| 2016 | **EU GDPR published** (Regulation 2016/679) — enforcement May 2018 |
| 2018 | CCPA (California); GDPR enforcement begins; major fines issued |
| 2020 | Schrems II (invalidates Privacy Shield); LGPD Brazil effective; NIST Privacy Framework 1.0 |
| 2021 | China PIPL; POPIA South Africa; Thailand PDPA; Virginia CDPA |
| 2022 | CPRA (enhanced CCPA); Japan APPI major amendment; Saudi PDPL; ISO 27701 widely adopted |
| 2023 | India DPDPA enacted; EU-US Data Privacy Framework (DPF); CPRA enforcement |
| 2024 | India DPDPA implementation; Saudi PDPL enforcement; EU AI Act + privacy provisions |

### 1.2 The Eight OECD Privacy Principles (1980)

| # | Principle | Modern Interpretation |
|:-:|-----------|---|
| 1 | **Collection Limitation** | Collect only what's needed; lawful means; consent where appropriate |
| 2 | **Data Quality** | Personal data must be accurate, complete, up-to-date |
| 3 | **Purpose Specification** | State purpose at collection time; don't use for incompatible purposes |
| 4 | **Use Limitation** | Don't use/disclose beyond stated purpose without consent or legal authority |
| 5 | **Security Safeguards** | Protect against loss, unauthorized access, destruction, modification |
| 6 | **Openness** | Be transparent about practices, policies, data controller identity |
| 7 | **Individual Participation** | Allow individuals to access, correct, delete their data |
| 8 | **Accountability** | Data controller is accountable for complying with above principles |

---

## Chapter 2 — Global Privacy Architecture

### 2.1 Privacy Regulation Map

```mermaid
graph TB
    subgraph "Global Privacy Regulation Landscape"
        subgraph "European Union"
            GDPR_O[GDPR (2016/679)<br/>━━━━━━━━━<br/>• 27 EU member states<br/>• Extraterritorial<br/>• €20M / 4% turnover fines<br/>• Gold standard globally]
            EPRIVACY[ePrivacy Directive<br/>━━━━━━━━━<br/>• Cookies; marketing<br/>• Electronic communications<br/>• Pending regulation update]
        end
        
        subgraph "Americas"
            CCPA_O[CCPA/CPRA (California)<br/>━━━━━━━━━<br/>• Consumer rights<br/>• Opt-out of sale<br/>• CPPA enforcement]
            LGPD_O[LGPD (Brazil)<br/>━━━━━━━━━<br/>• GDPR-inspired<br/>• ANPD enforcement<br/>• Effective 2020]
            PIPEDA_O[PIPEDA (Canada)<br/>━━━━━━━━━<br/>• Commercial data<br/>• Being replaced by C-27]
        end
        
        subgraph "Asia-Pacific"
            PIPL_O[PIPL (China)<br/>━━━━━━━━━<br/>• Extraterritorial<br/>• Security assessment<br/>• CAC enforcement]
            APPI_O[APPI (Japan)<br/>━━━━━━━━━<br/>• Amended 2022<br/>• PPC enforcement<br/>• EU adequacy]
            DPDPA_O[DPDPA (India)<br/>━━━━━━━━━<br/>• 2023 enacted<br/>• Consent-based<br/>• Penalties: ₹250 crore]
        end
        
        subgraph "Middle East / Africa"
            PDPL_O[PDPL (Saudi Arabia)<br/>━━━━━━━━━<br/>• 2022 enacted<br/>• 2024 enforcement<br/>• SDAIA oversight]
            POPIA_O[POPIA (South Africa)<br/>━━━━━━━━━<br/>• 2021 enforcement<br/>• Info Regulator<br/>• GDPR-influenced]
        end
    end
```

### 2.2 Comparison: Major Privacy Frameworks

| Feature | GDPR | CCPA/CPRA | LGPD | PIPL (China) | APPI (Japan) | DPDPA (India) |
|:-------:|:----:|:---------:|:----:|:---:|:---:|:---:|
| **Scope** | Any processing of EU residents' data | California residents; revenue >$25M OR 100K+ consumers | Brazilian citizens/residents | Chinese citizens + data processed in China | Japanese residents | Indian digital personal data |
| **Extraterritorial** | Yes (Art. 3) | Yes (businesses serving CA) | Yes (services to Brazil) | Yes (decisions about Chinese persons) | Yes (limited) | Yes |
| **Legal bases** | 6 (consent, contract, legal obligation, vital interest, public task, legitimate interest) | No legal basis concept; opt-out model | 10 (similar to GDPR + legitimate interest) | Consent + contract + HR + legal + public interest + legitimate interest | Consent (broad) + legitimate reasons | Consent + legitimate uses |
| **Consent standard** | Opt-in (affirmative; specific; informed; revocable) | Opt-out (consumer must request) | Opt-in (free; informed; unambiguous) | Opt-in (informed; express; voluntary) | Opt-in (some cases); opt-out (others) | Opt-in (free; specific; informed) |
| **Max fine** | €20M or 4% global revenue | $7,500/intentional violation; $2,500/unintentional | 2% revenue (max R$50M/infraction) | ¥50M or 5% prior year revenue | ¥100M (criminal); administrative orders | ₹250 crore (~$30M) |
| **DPO required** | Yes (certain controllers/processors) | No (but CPRA requires "privacy officer" role) | Yes (DPO equivalent - "encarregado") | Yes (personal information protection officer) | No (but recommended) | Consent manager role (not DPO) |
| **Cross-border mechanism** | SCCs; BCRs; adequacy decisions; DPF | No explicit mechanism (but CPRA limits) | Specific contract; international cooperation; adequacy | Security assessment; SCCs; certification; adequacy | APEC CBPR; adequacy (EU) | Consent or notified terms by government |

---

## Chapter 3 — Privacy Principles (Technical Deep Dive)

### 3.1 Privacy by Design (Ann Cavoukian's 7 Principles)

| # | Principle | Engineering Application |
|:-:|-----------|---|
| 1 | **Proactive not Reactive** | Privacy risk assessment BEFORE building; threat modeling includes privacy threats |
| 2 | **Privacy as Default Setting** | Minimal data collection by default; opt-IN for additional processing; most restrictive settings |
| 3 | **Privacy Embedded into Design** | Privacy requirements in architecture; not bolt-on; privacy patterns in code |
| 4 | **Full Functionality (Positive-Sum)** | Privacy AND functionality (not trade-off); find solutions that achieve both |
| 5 | **End-to-End Security** | Lifecycle protection: collection → processing → storage → deletion; encryption; access control |
| 6 | **Visibility and Transparency** | Clear privacy notices; audit trails; open documentation of practices |
| 7 | **Respect for User Privacy** | User-centric design; consent management; easy rights exercise |

### 3.2 Data Processing Principles (GDPR Art. 5)

| Principle | GDPR Article | Implementation |
|:---------:|:---:|---|
| **Lawfulness, fairness, transparency** | Art. 5(1)(a) | Valid legal basis; clear privacy notice; no deceptive practices |
| **Purpose limitation** | Art. 5(1)(b) | Specified purpose at collection; no incompatible secondary use without new basis |
| **Data minimisation** | Art. 5(1)(c) | Collect only necessary data; don't "just in case" collect; minimize fields |
| **Accuracy** | Art. 5(1)(d) | Keep data up-to-date; enable correction; validate at collection |
| **Storage limitation** | Art. 5(1)(e) | Retention periods; automated deletion; anonymize when no longer needed |
| **Integrity and confidentiality** | Art. 5(1)(f) | Encryption; access controls; security testing; breach prevention |
| **Accountability** | Art. 5(2) | Document compliance; DPIA; records of processing; demonstrate compliance |

### 3.3 Privacy-Enhancing Technologies (PETs)

| Technology | Privacy Benefit | Use Case |
|:----------:|:---|---|
| **Encryption (at rest + transit)** | Protects confidentiality; renders data unreadable if breached | All stored/transmitted personal data |
| **Pseudonymization** | Replaces identifiers with tokens; re-identification possible with key | Analytics; research (still personal data under GDPR) |
| **Anonymization** | Irreversible de-identification; NOT personal data if truly anonymous | Statistical reporting; open data |
| **Differential Privacy** | Adds calibrated noise to query results; mathematical privacy guarantee | Analytics on sensitive datasets; census data |
| **Homomorphic Encryption** | Compute on encrypted data without decryption | Cloud processing without data exposure |
| **Federated Learning** | ML training without centralizing data; models move, not data | Healthcare; financial (sensitive sectors) |
| **Zero-Knowledge Proofs** | Prove statement without revealing underlying data | Age verification without revealing DOB; credential verification |
| **Secure Multi-Party Computation** | Multiple parties jointly compute without revealing individual inputs | Joint analytics across competitors; anti-fraud cooperation |
| **Tokenization** | Replace sensitive data with non-reversible token | Payment processing (PCI DSS); database protection |
| **Data masking** | Replace real data with realistic fake data | Development/testing environments; demo systems |

---

## Chapter 4 — Implementation Guide

### 4.1 Privacy Compliance Program Structure

```mermaid
graph TB
    subgraph "Privacy Compliance Program"
        subgraph "Governance"
            DPO_R[DPO / Privacy Officer<br/>• Independent role<br/>• Reports to board<br/>• Advisory + monitoring]
            ROPA[Records of Processing Activities<br/>• Art. 30 GDPR<br/>• All processing documented<br/>• Legal basis per activity]
        end
        
        subgraph "Risk Management"
            DPIA_R[DPIA (Data Protection Impact Assessment)<br/>• High-risk processing<br/>• Before implementation<br/>• Mitigation measures]
            TIA[Transfer Impact Assessment<br/>• Cross-border transfers<br/>• Supplementary measures<br/>• Schrems II compliance]
        end
        
        subgraph "Operational"
            CONSENT_R[Consent Management<br/>• Collection mechanisms<br/>• Withdrawal capability<br/>• Preference center]
            DSR[Data Subject Rights<br/>• Access requests<br/>• Deletion requests<br/>• Portability requests<br/>• 30-day response (GDPR)]
            BREACH[Breach Management<br/>• 72-hour DPA notification<br/>• Individual notification<br/>• Root cause analysis]
        end
        
        subgraph "Technical"
            PBD[Privacy by Design<br/>• Architecture patterns<br/>• Data minimization<br/>• Encryption; pseudonymization]
            RETENTION[Retention Management<br/>• Defined periods<br/>• Automated deletion<br/>• Audit trail]
        end
    end
```

### 4.2 Data Subject Rights Response Process

| Right (GDPR) | Response Deadline | Key Actions |
|:---:|:---:|---|
| **Access (Art. 15)** | 30 days (extendable by 60) | Verify identity; locate all data; compile in readable format; provide processing info |
| **Rectification (Art. 16)** | 30 days | Verify identity; update data; notify recipients of correction |
| **Erasure (Art. 17)** | 30 days | Verify identity; check exceptions (legal obligation; litigation hold); delete from all systems; notify processors |
| **Portability (Art. 20)** | 30 days | Provide data in structured, machine-readable format (JSON/CSV); only for consent/contract-based processing |
| **Restriction (Art. 18)** | 30 days | Flag data as restricted; prevent further processing; maintain storage |
| **Objection (Art. 21)** | 30 days | Assess: legitimate grounds override? If not, cease processing; document decision |

---

## Chapter 5 — Certification & Compliance

### 5.1 Certification Mechanisms

| Mechanism | Standard | Scope | Benefit |
|:---------:|:--------:|:-----:|--------|
| **ISO 27701** | ISO/IEC 27701:2019 | Privacy Information Management System (extension of 27001) | Demonstrates systematic privacy management; maps to GDPR/CCPA/LGPD |
| **GDPR certification (Art. 42)** | Various (EU scheme) | Specific processing activities | Demonstrates GDPR compliance; voluntary; 3-year validity |
| **EU Cloud Code of Conduct** | CISPE/SWIPO | Cloud services processing | Demonstrates cloud provider compliance (processor obligations) |
| **APEC CBPR** | APEC framework | Cross-border data flows in APEC region | Facilitates data transfers within APEC (19 economies) |
| **SOC 2 + Privacy** | AICPA TSC | Service organization controls | Privacy criteria in SOC 2 report; assurance to customers |

### 5.2 Penalties Comparison

| Jurisdiction | Maximum Fine | Notable Enforcement |
|:---:|:---:|---|
| **GDPR** | €20M or 4% global annual turnover | Meta: €1.2B (2023, cross-border transfers); Amazon: €746M (2021, targeting); Google: €150M (2022, cookies) |
| **CCPA/CPRA** | $7,500/intentional; $2,500/unintentional per violation | Sephora: $1.2M (2022, sale of data); DoorDash: settlement |
| **LGPD** | 2% revenue, max R$50M per infraction | Enforcement ramping up (ANPD issuing first fines 2023-2024) |
| **PIPL** | ¥50M or 5% prior year revenue; personal liability for individuals | Didi: fined + app removed (2022); others under investigation |
| **India DPDPA** | ₹250 crore (~$30M) per instance | Not yet enforced (2024 implementation phase) |

---

## Chapter 6 — Privacy Standard Ecosystem

### 6.1 ISO Privacy Standards Family

| Standard | Title | Role |
|:--------:|:------|:----:|
| **ISO/IEC 27701:2019** | Privacy Information Management System (PIMS) | Management system (certification) |
| **ISO/IEC 29100:2011** | Privacy Framework | Principles; actors; requirements framework |
| **ISO/IEC 29101:2013** | Privacy Reference Architecture | Technical architecture patterns |
| **ISO/IEC 29134:2023** | Privacy Impact Assessment Guidelines | Risk assessment methodology |
| **ISO/IEC 29151:2017** | PII Protection Code of Practice | Best practices for PII handling |
| **ISO/IEC 29184:2020** | Online Privacy Notice and Consent | Consent/notice requirements |
| **ISO/IEC 27550:2019** | Privacy Engineering | Integrating privacy into systems lifecycle |
| **ISO/IEC 27018:2019** | PII in Public Clouds | Cloud-specific PII protection |

### 6.2 Relationship Between Standards

```mermaid
graph TB
    subgraph "Privacy Standards Ecosystem"
        ISO27001[ISO 27001<br/>(Information Security)]
        ISO27701[ISO 27701<br/>(Privacy extension)]
        ISO29100[ISO 29100<br/>(Privacy Framework)]
        ISO29134[ISO 29134<br/>(PIA Guidelines)]
        ISO29101[ISO 29101<br/>(Privacy Architecture)]
        GDPR_S[GDPR<br/>(EU Regulation)]
        NIST_PF[NIST Privacy Framework<br/>(US Voluntary)]
    end
    
    ISO27001 -->|"extended by"| ISO27701
    ISO29100 -->|"principles for"| ISO27701
    ISO29100 -->|"architecture via"| ISO29101
    ISO29134 -->|"risk methodology for"| ISO27701
    ISO27701 -->|"maps to"| GDPR_S
    NIST_PF -->|"complements"| ISO27701
```

---

## Chapter 7 — Comparison: Major Privacy Approaches

| Approach | EU (GDPR) | US (Sectoral) | China (PIPL) | Brazil (LGPD) |
|:--------:|:---------:|:---:|:---:|:---:|
| **Philosophy** | Fundamental right (comprehensive) | Consumer protection (sectoral) | State security + individual rights | Fundamental right (GDPR-modeled) |
| **Model** | Omnibus (single law for all sectors) | Sectoral (HIPAA health; GLBA finance; CCPA consumer) | Omnibus + sectoral | Omnibus |
| **Enforcement** | Independent DPAs (decentralized per member state) | FTC + state AGs (enforcement-driven) | CAC (Cyberspace Administration) + sectoral | ANPD (centralized) |
| **Cross-border** | SCCs; BCRs; adequacy; DPF | No unified mechanism | Security assessment; SCCs; certification | Contract; international cooperation |
| **Consent** | High standard (opt-in; granular; withdrawable) | Mixed (opt-out in many states) | High (separate consent for sensitive/cross-border) | High (opt-in; specific; informed) |
| **Individual rights** | Extensive (access; erasure; portability; object) | Varies by state (CCPA: know; delete; opt-out) | Similar to GDPR (access; correct; delete; portability) | Extensive (GDPR-modeled) |
| **DPO requirement** | Yes (certain entities) | No (except CPRA privacy officer concept) | Yes (personal information protection officer) | Yes (encarregado) |

---

## Chapter 8 — Architecture Diagrams

### 8.1 Privacy-Preserving System Architecture

```mermaid
graph TB
    subgraph "Privacy-Preserving Architecture"
        subgraph "Data Collection Layer"
            CONSENT_M[Consent Management Platform<br/>• Purpose-based consent<br/>• Granular opt-in/out<br/>• Audit trail]
            MIN[Data Minimization Gate<br/>• Only required fields<br/>• Validation at entry<br/>• Purpose-field mapping]
        end
        
        subgraph "Processing Layer"
            PSEUDO[Pseudonymization Engine<br/>• Separate identifiers<br/>• Token mapping (secured)<br/>• Re-identification controlled]
            ENCRYPT[Encryption Layer<br/>• At-rest (AES-256)<br/>• In-transit (TLS 1.3)<br/>• Key management (HSM)]
            ACCESS[Access Control<br/>• RBAC/ABAC<br/>• Need-to-know basis<br/>• Audit logging]
        end
        
        subgraph "Storage Layer"
            RETENTION_A[Retention Engine<br/>• Auto-deletion on expiry<br/>• Purpose-based retention<br/>• Legal hold exceptions]
            CROSS_B[Cross-Border Control<br/>• Data residency rules<br/>• Transfer mechanisms<br/>• TIA enforcement]
        end
        
        subgraph "Rights Management"
            DSR_A[DSR Automation<br/>• Discovery (find all data)<br/>• Response generation<br/>• Deletion propagation<br/>• Audit trail]
        end
    end
    
    CONSENT_M --> MIN --> PSEUDO --> ENCRYPT --> RETENTION_A
    ACCESS -.-> ENCRYPT
    DSR_A -.-> RETENTION_A
    CROSS_B -.-> ENCRYPT
```

### 8.2 DPIA Process Flow

```mermaid
graph TB
    subgraph "DPIA Process (GDPR Art. 35)"
        TRIGGER[Trigger Assessment<br/>• New processing activity<br/>• High-risk indicators<br/>• Systematic monitoring<br/>• Large-scale processing]
        
        DESCRIBE[Describe Processing<br/>• Purpose; nature; scope<br/>• Data categories<br/>• Recipients<br/>• Retention periods]
        
        NECESSITY[Assess Necessity & Proportionality<br/>• Is processing necessary?<br/>• Is data minimized?<br/>• Could purpose be achieved with less data?]
        
        RISKS[Identify & Assess Risks<br/>• Likelihood × severity<br/>• Risk to individuals<br/>• Physical; material; non-material harm]
        
        MITIGATE[Identify Mitigation Measures<br/>• Technical (encryption; pseudonymization)<br/>• Organizational (access controls; training)<br/>• Contractual (processor agreements)]
        
        RESIDUAL[Assess Residual Risk<br/>• After mitigations applied<br/>• Acceptable? → proceed<br/>• Unacceptable? → consult DPA (Art. 36)]
        
        DOCUMENT[Document & Monitor<br/>• DPIA report<br/>• Review triggers<br/>• Ongoing monitoring]
    end
    
    TRIGGER --> DESCRIBE --> NECESSITY --> RISKS --> MITIGATE --> RESIDUAL --> DOCUMENT
```

---

## Chapter 9 — Case Studies

### 9.1 GDPR Enforcement: Meta €1.2 Billion Fine (2023)

| Aspect | Detail |
|--------|--------|
| **Controller** | Meta Platforms Ireland (Facebook) |
| **Issue** | Transferring EU users' personal data to US servers without adequate safeguards after Schrems II invalidated Privacy Shield |
| **DPA** | Irish DPC (Data Protection Commission) — lead supervisory authority |
| **Timeline** | Investigation started 2020 (post-Schrems II); preliminary decision 2022; EDPB dispute resolution 2023; final decision May 2023 |
| **Fine** | €1.2 billion — largest GDPR fine ever issued |
| **Corrective measures** | Suspend transfers within 5 months; bring data into compliance within 6 months; delete previously transferred data |
| **Impact** | Demonstrated: cross-border transfers are highest-risk area; SCCs alone insufficient without supplementary measures; regulatory patience has limits |
| **Resolution** | EU-US Data Privacy Framework (DPF) adopted July 2023 — provides new legal mechanism for EU→US transfers |

### 9.2 Privacy Engineering: Building a GDPR-Compliant Platform

| Aspect | Detail |
|--------|--------|
| **Scenario** | Healthcare SaaS platform processing patient data (GDPR special category Art. 9); deployed across EU |
| **Architecture decisions** | (1) Data residency: all PII in EU data centers (no cross-border). (2) Encryption: AES-256 at rest; TLS 1.3 in transit; patient IDs pseudonymized. (3) Access control: RBAC with healthcare-specific roles; break-glass for emergencies (audited). (4) Consent: granular consent per processing purpose; easy withdrawal; consent versioning. (5) Retention: automated deletion after treatment + legal retention period (10 years for medical records in Germany). (6) DSR automation: patient portal for access/portability requests; auto-response in <72 hours. |
| **DPIA** | Completed before launch: identified 12 high risks; mitigated 11; 1 residual (re-identification from longitudinal data) → accepted with monitoring. |
| **ISO 27701** | Achieved certification within 12 months (built on existing ISO 27001). Maps controls to GDPR Art. 28 (processor), Art. 32 (security), Art. 35 (DPIA). |
| **Outcome** | Zero data breaches in first 2 years. DSR response time: median 3 days (target: 30). DPA audit: no findings. Customer trust: key differentiator (GDPR compliance as selling point in healthcare). |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **AI regulation + privacy** | 2024-2026 | EU AI Act + GDPR intersection; automated decision-making (Art. 22); AI training on personal data; right to explanation |
| **Global convergence** | 2024-2030 | More countries adopting GDPR-like laws; interoperability mechanisms; adequacy decisions expanding |
| **Privacy-preserving computation** | 2024-2028 | Confidential computing; homomorphic encryption becoming practical; federated learning mainstream |
| **Children's privacy** | 2024-2026 | Age verification technology; COPPA 2.0 discussions; EU Digital Services Act (minors); AADC (UK) |
| **Biometric regulation** | 2024-2027 | EU AI Act bans (real-time public facial recognition); biometric data as highest-risk category |
| **Universal consent** | 2025-2028 | Global Privacy Control (GPC); automated consent signals; consent interoperability |
| **Quantum-safe privacy** | 2025-2035 | Post-quantum cryptography for privacy; encrypted data at risk from future quantum decryption |
| **Decentralized identity** | 2025-2030 | Self-sovereign identity (SSI); verifiable credentials; user controls own data |
| **India DPDPA implementation** | 2024-2025 | Rules and regulations being finalized; major impact (1.4B population) |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is the difference between a data controller and a data processor under GDPR?

**A:**

| Aspect | Data Controller | Data Processor |
|:------:|:---:|:---:|
| **Definition** | Determines the PURPOSES and MEANS of processing personal data | Processes personal data ON BEHALF of the controller |
| **Decision-making** | Decides WHY and HOW data is processed | Follows controller's instructions (doesn't decide purpose) |
| **Example** | Company collecting customer data for marketing | Cloud provider (AWS/Azure) hosting that data; payroll provider processing employee data |
| **GDPR obligations** | Full compliance (legal basis; DPIA; DPO; breach notification to DPA; records of processing; DSR response) | Security measures; data processing agreement (Art. 28); breach notification to CONTROLLER; records of processing (limited); assist controller with DSRs |
| **Liability** | Primary liability for compliance | Liable for own violations (especially security); joint liability if acts outside instructions |
| **Key document** | Privacy notice (to data subjects) | Data Processing Agreement (DPA) with controller |

### Tier 2: Mid-Level

**Q2:** A company wants to transfer EU customer data to a data center in India. What legal mechanisms are available, and what due diligence is required?

**A:**

**Post-Schrems II Transfer Mechanism:**

| Step | Action |
|:----:|--------|
| **1. Check adequacy** | India does NOT have an EU adequacy decision → cannot rely on Art. 45 |
| **2. Select transfer mechanism** | Options: (a) Standard Contractual Clauses (SCCs) — Art. 46(2)(c); (b) Binding Corporate Rules (BCRs) if intra-group — Art. 47; (c) Derogations Art. 49 (only specific; not systematic transfers) |
| **3. Transfer Impact Assessment (TIA)** | Required by EDPB post-Schrems II: assess Indian law (surveillance; government access; data protection law adequacy); identify risks to data subjects |
| **4. Supplementary measures** | If TIA identifies risks: encryption with EU-held keys; pseudonymization before transfer; contractual commitments from importer; organizational measures (access controls; audit rights) |
| **5. Documentation** | Document: TIA findings; chosen mechanism; supplementary measures; ongoing monitoring plan |
| **6. India-specific** | India has DPDPA (2023) — offers some data protection baseline; but implementation rules pending; government access provisions exist; assess on case-by-case basis |

**Practical recommendation:** Use SCCs (new 2021 module 1 or 2 depending on controller/processor) + TIA + supplementary measures (encryption with keys held in EU; access controls limiting India-based staff access to pseudonymized data).

### Tier 3: Senior

**Q3:** Design a global privacy compliance program for a multinational operating in EU, US (California), Brazil, China, India, and Japan.

**A:**

**Architecture: Hub-and-Spoke Model**

| Component | Implementation |
|:---------:|----------------|
| **Global privacy framework** | Core standard: ISO 27701 (covers all jurisdictions via mapping); single PIMS with jurisdictional overlays |
| **Global DPO** | Group DPO in EU (GDPR Art. 37); local privacy leads in US, Brazil, China, India, Japan |
| **Records of processing** | Unified ROPA (Records of Processing Activities) tool: one global register; jurisdictional flags per activity |
| **Legal basis mapping** | Per processing activity: map required legal basis per jurisdiction (GDPR: consent/legitimate interest; CCPA: opt-out; PIPL: consent/contract; etc.) |
| **Consent management** | Global CMP (Consent Management Platform); jurisdiction-aware (EU: opt-in + cookie consent; US: opt-out + GPC; China: separate consent for sensitive; Brazil: opt-in) |
| **Cross-border transfers** | (1) EU→US: Data Privacy Framework (if self-certified); else SCCs. (2) EU→China: SCCs + TIA + supplementary measures. (3) EU→India: SCCs + TIA. (4) EU→Brazil: SCCs. (5) EU→Japan: adequacy decision (mutual). (6) China→outside: CAC security assessment (for >1M records or sensitive data); else SCCs |
| **Data subject rights** | Unified DSR portal: jurisdiction-aware workflows (GDPR: 30 days; CCPA: 45 days; LGPD: 15 days; PIPL: varies) |
| **Breach management** | Global incident response; jurisdiction-aware notification (GDPR: 72 hrs to DPA; PIPL: immediately to CAC; LGPD: reasonable time to ANPD) |
| **Training** | Annual privacy training for all staff; role-specific training (developers: privacy by design; marketing: consent rules; HR: employee data); jurisdiction-specific modules |
| **Audit** | Annual internal audit (ISO 27701 cycle); external certification audit; jurisdiction-specific readiness reviews before regulator audits |

---

## Chapter 12 — Cheat Sheet & Quick Reference

```
═══════════════════════════════════════════
PRIVACY & DATA PROTECTION — QUICK REFERENCE
═══════════════════════════════════════════

GDPR ESSENTIALS:
  Scope: EU residents' data (anywhere in world)
  Legal bases: Consent; Contract; Legal obligation;
    Vital interest; Public task; Legitimate interest
  Rights: Access; Rectify; Erase; Port; Restrict; Object
  Fines: €20M or 4% global annual turnover
  Breach notification: 72 hours to DPA
  DSR response: 30 days (extendable by 60)
  DPO required: if core = monitoring or special data

═══════════════════════════════════════════
CCPA/CPRA (California):
  Rights: Know; Delete; Opt-out of sale/sharing; Correct
  "Sale" = broadly defined (includes sharing for ads)
  No legal basis concept (processing allowed by default)
  Consumer opt-out (not opt-in)
  CPPA enforcement since 2023
  Fine: $7,500/intentional violation

═══════════════════════════════════════════
CROSS-BORDER TRANSFERS:
  EU → Adequate country: OK (Art. 45)
  EU → Non-adequate: SCCs + TIA + supplementary measures
  EU → US: Data Privacy Framework (Art. 45 adequacy 2023)
  China → outside: Security assessment (CAC) or SCCs
  India → outside: Per government notification (pending)

═══════════════════════════════════════════
KEY STANDARDS:
  ISO 27701: Privacy management system (certifiable)
  ISO 29100: Privacy principles framework
  ISO 29134: Privacy Impact Assessment
  NIST PF: Identify-P, Govern-P, Control-P, Communicate-P, Protect-P

═══════════════════════════════════════════
PRIVACY BY DESIGN (7 PRINCIPLES):
  1. Proactive not reactive
  2. Privacy as default
  3. Privacy embedded in design
  4. Full functionality (positive-sum)
  5. End-to-end security
  6. Visibility and transparency
  7. Respect for user privacy

═══════════════════════════════════════════
PRIVACY-ENHANCING TECHNOLOGIES (PETs):
  Encryption: Confidentiality (AES-256; TLS 1.3)
  Pseudonymization: Replace identifiers (still PD)
  Anonymization: Irreversible (NOT personal data)
  Differential privacy: Mathematical privacy guarantee
  Federated learning: Train without centralizing data
  Tokenization: Replace with non-reversible tokens

═══════════════════════════════════════════
DPIA TRIGGERS (GDPR Art. 35):
  • Systematic/extensive profiling with legal effects
  • Large-scale processing of special category data
  • Systematic monitoring of public areas
  • New technologies with high risk
  • Denial of service based on automated processing

═══════════════════════════════════════════
CONTROLLER vs. PROCESSOR:
  Controller: decides WHY and HOW (full obligations)
  Processor: processes ON BEHALF (follows instructions)
  DPA (contract) required between them (Art. 28)
```

---

*End of Document — 00_Privacy_Landscape_Overview.md*
