# GDPR — EU General Data Protection Regulation (Deep Dive)

**Topic:** Regulation (EU) 2016/679 — General Data Protection Regulation  
**Standard:** GDPR (Official Journal of the European Union, L 119, 4 May 2016)  
**Effective:** 25 May 2018  
**SDO:** European Parliament and Council of the European Union  
**Audience:** Data Protection Officers (DPOs), legal/compliance teams, privacy engineers, software architects, product managers  
**Prerequisites:** Basic understanding of personal data; EU institutional structure awareness

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Milestone |
|------|-----------|
| 1950 | European Convention on Human Rights — Art. 8: right to private life |
| 1981 | Council of Europe Convention 108 — first binding international data protection instrument |
| 1995 | **Directive 95/46/EC** — EU Data Protection Directive (harmonized EU; transposed by member states) |
| 2000 | EU Charter of Fundamental Rights — Art. 7 (privacy) + Art. 8 (data protection) |
| 2000 | EU-US Safe Harbor adopted (allows data transfers) |
| 2012 | European Commission proposes GDPR reform (Jan 2012) |
| 2014 | European Parliament approves GDPR text (first reading) |
| 2015 | Schrems I (CJEU C-362/14) — invalidates Safe Harbor |
| 2016 | **GDPR adopted** (14 April 2016); published (4 May 2016); 2-year transition |
| 2016 | EU-US Privacy Shield adopted (replacement for Safe Harbor) |
| 2018 | **GDPR enforcement begins** (25 May 2018) |
| 2019 | First major fines: Google (France CNIL): €50M; British Airways: £183M (proposed) |
| 2020 | **Schrems II** (CJEU C-311/18) — invalidates Privacy Shield; SCCs remain valid with supplementary measures |
| 2021 | New SCCs adopted (June 2021); EDPB guidelines on transfer supplementary measures |
| 2022 | Meta (Ireland): €405M (Instagram children); Amazon (Luxembourg): €746M |
| 2023 | **Meta: €1.2B** (largest GDPR fine — cross-border transfers); EU-US Data Privacy Framework adopted |
| 2024 | AI Act + GDPR intersection guidance; ongoing enforcement maturity |

### 1.2 GDPR vs. Directive 95/46/EC

| Aspect | Directive 95/46/EC (1995) | GDPR (2016/679) |
|:------:|:---:|:---:|
| **Legal instrument** | Directive (requires transposition by each state → 28 different versions) | Regulation (directly applicable; uniform across EU) |
| **Territorial scope** | EU establishment only | Extraterritorial (any entity processing EU residents' data) |
| **Fines** | Set by member states (often weak) | €20M or 4% global turnover (whichever higher) |
| **Consent** | "Unambiguous" | "Freely given, specific, informed, unambiguous; clear affirmative action" |
| **DPO** | Not required | Required for certain organizations |
| **Breach notification** | Not required (except ePrivacy) | 72-hour notification to DPA; individual notification if high risk |
| **Right to erasure** | Limited | Explicit Art. 17 with conditions |
| **Data portability** | Not included | Art. 20 (new right) |
| **Accountability** | Implicit | Explicit principle (Art. 5(2)); demonstrate compliance |
| **One-stop-shop** | No (deal with each member state DPA) | Lead supervisory authority (establishment-based) |

---

## Chapter 2 — GDPR Architecture & Structure

### 2.1 Regulation Structure

| Chapter | Articles | Content |
|:-------:|:--------:|---------|
| I | 1-4 | General provisions (subject matter; scope; definitions; territorial scope) |
| II | 5-11 | **Principles** (lawfulness; purpose limitation; minimization; accuracy; storage; integrity; accountability) |
| III | 12-23 | **Rights of data subjects** (transparency; access; rectification; erasure; portability; restriction; objection; automated decisions) |
| IV | 24-43 | **Controller & Processor** (obligations; DPO; codes of conduct; certification) |
| V | 44-50 | **Transfers** to third countries (adequacy; SCCs; BCRs; derogations) |
| VI | 51-59 | **Supervisory authorities** (independence; tasks; powers) |
| VII | 60-76 | **Cooperation & consistency** (one-stop-shop; EDPB; dispute resolution) |
| VIII | 77-84 | **Remedies, liability, penalties** (right to lodge complaint; compensation; administrative fines) |
| IX | 85-91 | **Specific processing situations** (journalism; employment; research; churches) |
| X | 92-93 | Delegated and implementing acts |
| XI | 94-99 | Final provisions (repeal of Directive; entry into force) |

### 2.2 Key Definitions (Art. 4)

| Term | Definition | Example |
|:----:|:---|---|
| **Personal data** | Any information relating to an identified or identifiable natural person | Name; email; IP address; location data; cookie ID; employee number |
| **Processing** | Any operation on personal data (collection; storage; use; disclosure; erasure) | Storing in database; sending email; analytics; printing; deleting |
| **Data subject** | Identified/identifiable natural person whose data is processed | Customer; employee; website visitor; patient |
| **Controller** | Entity determining purposes and means of processing | Company deciding to collect customer emails for marketing |
| **Processor** | Entity processing on behalf of controller | Cloud hosting provider; payroll service; email delivery service |
| **Special categories** | Racial/ethnic origin; political opinions; religious beliefs; trade union; genetic; biometric; health; sex life/orientation (Art. 9) | Medical records; DNA samples; fingerprints; political party membership |
| **Consent** | Freely given, specific, informed, unambiguous indication by clear affirmative action (Art. 4(11)) | Ticking unticked checkbox; signing consent form; pressing "I agree" after clear notice |
| **DPIA** | Data Protection Impact Assessment — risk assessment for high-risk processing (Art. 35) | Assessment before implementing AI profiling system |

---

## Chapter 3 — Technical Deep Dive

### 3.1 Legal Bases for Processing (Art. 6)

| Legal Basis | When Applicable | Controller Obligation | Revocability |
|:-----------:|:---|:---:|:---:|
| **(a) Consent** | Data subject has given consent for specific purpose(s) | Demonstrate consent; easily withdrawable; granular | Yes (must be as easy as giving) |
| **(b) Contract** | Processing necessary for contract performance or pre-contractual steps | Limit to what's necessary for contract | N/A (processing ceases when contract ends) |
| **(c) Legal obligation** | Processing required by EU/member state law | Identify specific legal provision | N/A (legal requirement) |
| **(d) Vital interests** | Necessary to protect life of data subject or another person | Only when no other basis available; emergency situations | N/A |
| **(e) Public task** | Processing for public interest or exercise of official authority | Public sector; regulatory functions | Right to object applies |
| **(f) Legitimate interests** | Controller/third party legitimate interest, balanced against data subject rights | Document Legitimate Interest Assessment (LIA); demonstrate balancing | Right to object applies |

### 3.2 Consent Requirements (Art. 7)

| Requirement | Detail |
|:-----------:|--------|
| **Freely given** | No power imbalance; not bundled with contract; no detriment for refusal |
| **Specific** | Per purpose; granular (cannot be single consent for multiple unrelated purposes) |
| **Informed** | Identity of controller; purpose(s); right to withdraw; data types; recipients; transfers |
| **Unambiguous** | Clear affirmative action (opt-in); pre-ticked boxes NOT valid; silence NOT consent |
| **Withdrawable** | As easy to withdraw as to give; must inform of right before consent; withdrawal doesn't affect prior processing |
| **Children** | Under 16 (or 13 per member state): parental consent required (Art. 8) |
| **Demonstrable** | Controller must prove consent was given (records; logs; audit trail) |

### 3.3 Data Subject Rights (Art. 12-22)

```mermaid
graph TB
    subgraph "GDPR Data Subject Rights"
        R_INFO[Right to Information<br/>Art. 13/14<br/>━━━━━━━━━<br/>Privacy notice at collection<br/>or within 1 month (indirect)]
        
        R_ACCESS[Right of Access<br/>Art. 15<br/>━━━━━━━━━<br/>Copy of all personal data<br/>+ processing details<br/>Free (first copy)]
        
        R_RECTIFY[Right to Rectification<br/>Art. 16<br/>━━━━━━━━━<br/>Correct inaccurate data<br/>Complete incomplete data]
        
        R_ERASE[Right to Erasure<br/>Art. 17<br/>━━━━━━━━━<br/>"Right to be forgotten"<br/>Delete when no longer needed<br/>Exceptions apply]
        
        R_RESTRICT[Right to Restriction<br/>Art. 18<br/>━━━━━━━━━<br/>Mark data as restricted<br/>Only store, no process]
        
        R_PORT[Right to Portability<br/>Art. 20<br/>━━━━━━━━━<br/>Receive data in machine-<br/>readable format<br/>Only: consent/contract + automated]
        
        R_OBJECT[Right to Object<br/>Art. 21<br/>━━━━━━━━━<br/>Object to: legitimate interest;<br/>direct marketing (absolute);<br/>research (public interest)]
        
        R_AUTO[Automated Decisions<br/>Art. 22<br/>━━━━━━━━━<br/>Not subject to solely automated<br/>decisions with legal/significant effects<br/>Right to human intervention]
    end
```

### 3.4 DPIA (Data Protection Impact Assessment — Art. 35)

**Mandatory DPIA triggers:**

| Trigger | Example |
|:-------:|---------|
| Systematic, extensive evaluation of personal aspects (profiling) | Credit scoring; insurance risk assessment; behavioral advertising |
| Large-scale processing of special categories (Art. 9) or criminal data | Hospital patient records; genetic testing service |
| Systematic monitoring of publicly accessible area on large scale | CCTV with facial recognition; city-wide surveillance |
| DPA blacklist (national lists published by each DPA) | Various — check local DPA's Art. 35(4) list |

**DPIA content (minimum Art. 35(7)):**
1. Systematic description of processing (purpose, scope, context)
2. Assessment of necessity and proportionality
3. Assessment of risks to rights and freedoms
4. Measures to address risks (safeguards; security; compliance demonstration)

---

## Chapter 4 — Implementation Guide

### 4.1 GDPR Compliance Roadmap

| Phase | Duration | Activities |
|:-----:|:--------:|:---|
| **1. Discovery** | 4-8 weeks | Data mapping; identify all processing activities; identify legal bases; inventory data flows (internal + cross-border + third parties) |
| **2. Gap Analysis** | 2-4 weeks | Compare current practices against GDPR requirements; prioritize gaps by risk |
| **3. Governance** | 4-8 weeks | Appoint DPO (if required); establish privacy governance structure; create accountability framework |
| **4. Documentation** | 6-12 weeks | Records of Processing (Art. 30); privacy notices (Art. 13/14); consent mechanisms; DPAs with processors; DPIA template + conduct for high-risk |
| **5. Technical** | 8-16 weeks | Implement privacy by design; data minimization; pseudonymization/encryption; retention automation; DSR response system; breach detection |
| **6. Operational** | 4-8 weeks | Training; awareness; DSR procedures; breach notification procedures; third-party management |
| **7. Ongoing** | Continuous | Monitor compliance; internal audits; DPIA for new projects; review/update documentation; training refresher |

### 4.2 Article 30 — Records of Processing Activities

| Field | Controller (Art. 30(1)) | Processor (Art. 30(2)) |
|:-----:|:---|:---|
| Name/contact | Controller; DPO; joint controller | Processor; controller(s) on behalf of; DPO |
| Purposes | Yes | N/A (defined by controller) |
| Categories of data subjects | Yes | Yes |
| Categories of personal data | Yes | Yes |
| Categories of recipients | Yes (incl. third countries) | N/A |
| Transfers to third countries | Yes (safeguards documented) | Yes (safeguards documented) |
| Retention periods (envisaged) | Yes | N/A |
| Security measures (general description) | Yes | Yes |

### 4.3 Breach Notification (Art. 33-34)

| Step | Requirement | Timeline |
|:----:|:---|:---:|
| **1. Detection** | Identify breach (unauthorized access; loss; destruction; disclosure) | — |
| **2. Internal escalation** | Notify DPO; activate incident response team; assess risk | Immediately |
| **3. Risk assessment** | Determine: likelihood of risk to rights/freedoms of individuals | Within hours |
| **4. DPA notification** | If risk to rights/freedoms: notify supervisory authority | ≤ 72 hours from awareness |
| **5. Individual notification** | If HIGH risk to rights/freedoms: notify affected data subjects | "Without undue delay" |
| **6. Documentation** | Document: facts; effects; remedial actions (regardless of notification) | Always required |

**DPA notification content (Art. 33(3)):**
- Nature of breach (categories and approximate number of data subjects/records)
- DPO contact details
- Likely consequences
- Measures taken/proposed to address and mitigate

---

## Chapter 5 — Enforcement & Penalties

### 5.1 Fine Tiers

| Tier | Maximum | Violations |
|:----:|:-------:|:---|
| **Lower tier** | €10M or 2% global annual turnover | Controller/processor obligations (Art. 8, 11, 25-39, 42, 43); DPO; DPIA; records of processing |
| **Upper tier** | €20M or 4% global annual turnover | Principles (Art. 5); legal bases (Art. 6); consent (Art. 7); data subject rights (Art. 12-22); transfers (Art. 44-49) |

### 5.2 Major Fines (Top 10 as of 2024)

| Rank | Company | Fine | DPA | Year | Reason |
|:----:|:-------:|:----:|:---:|:----:|--------|
| 1 | Meta (Facebook) | €1.2B | Ireland DPC | 2023 | EU→US data transfers |
| 2 | Amazon | €746M | Luxembourg CNPD | 2021 | Advertising targeting |
| 3 | Meta (Instagram) | €405M | Ireland DPC | 2022 | Children's data |
| 4 | Meta (Facebook) | €390M | Ireland DPC | 2023 | Legal basis for behavioral ads |
| 5 | Meta (WhatsApp) | €225M | Ireland DPC | 2021 | Transparency |
| 6 | Google | €150M | France CNIL | 2022 | Cookie consent |
| 7 | H&M | €35M | Hamburg DPA | 2020 | Employee surveillance |
| 8 | TIM (Telecom Italia) | €27.8M | Italy Garante | 2020 | Unsolicited marketing |
| 9 | British Airways | £20M | UK ICO | 2020 | Security breach (reduced from £183M) |
| 10 | Marriott | £18.4M | UK ICO | 2020 | Security breach |

### 5.3 Enforcement Trends

| Trend | Observation |
|:-----:|------------|
| **Increasing fines** | Average fine size growing year-over-year; 2023 saw largest single fine (€1.2B) |
| **Cross-border focus** | Transfer violations heavily enforced post-Schrems II |
| **Consent/legal basis** | DPAs challenging weak consent mechanisms; legitimate interest without proper LIA |
| **Children's data** | Heightened enforcement for social media platforms processing minors' data |
| **Security breaches** | Significant fines for inadequate security measures (even without malicious attack) |
| **Cooperation mechanism** | EDPB dispute resolution used more frequently (overriding lead DPA decisions) |

---

## Chapter 6 — Cross-Border Data Transfers (Chapter V)

### 6.1 Transfer Mechanisms

```mermaid
graph TB
    subgraph "GDPR Transfer Mechanisms (Art. 44-49)"
        Q1{Adequate country?<br/>Art. 45}
        YES_AD[Transfer allowed<br/>without additional<br/>safeguards]
        
        Q2{Appropriate safeguards?<br/>Art. 46}
        SCC[Standard Contractual<br/>Clauses (SCCs)]
        BCR[Binding Corporate<br/>Rules (BCRs)]
        COC[Code of Conduct<br/>Art. 40 + binding commitments]
        CERT_T[Certification<br/>Art. 42 + binding commitments]
        
        Q3{Derogation?<br/>Art. 49}
        DEROG[Derogations:<br/>• Explicit consent (informed of risks)<br/>• Contract performance<br/>• Public interest<br/>• Legal claims<br/>• Vital interests<br/>• Limited public register]
        
        BLOCK[Transfer NOT<br/>permitted]
    end
    
    Q1 -->|"Yes"| YES_AD
    Q1 -->|"No"| Q2
    Q2 -->|"Yes"| SCC
    Q2 -->|"Yes"| BCR
    Q2 -->|"Yes"| COC
    Q2 -->|"Yes"| CERT_T
    Q2 -->|"No safeguard available"| Q3
    Q3 -->|"Yes (specific)"| DEROG
    Q3 -->|"No"| BLOCK
```

### 6.2 Adequacy Decisions (as of 2024)

| Country/Territory | Decision Year | Notes |
|:---:|:---:|---|
| Andorra | 2010 | Full adequacy |
| Argentina | 2003 | Full adequacy |
| Canada (PIPEDA) | 2001 | Commercial activities only |
| Faroe Islands | 2010 | Full adequacy |
| Guernsey | 2003 | Full adequacy |
| Israel | 2011 | Full adequacy |
| Isle of Man | 2004 | Full adequacy |
| Japan | 2019 | Mutual adequacy (EU↔Japan) |
| Jersey | 2008 | Full adequacy |
| New Zealand | 2012 | Full adequacy |
| Republic of Korea | 2022 | Full adequacy |
| Switzerland | 2000 | Full adequacy |
| UK | 2021 | Post-Brexit adequacy (4-year sunset; renewed) |
| Uruguay | 2012 | Full adequacy |
| **USA (DPF)** | **2023** | Data Privacy Framework (companies self-certify with DoC) |

### 6.3 Standard Contractual Clauses (2021 SCCs)

| Module | Scenario | Parties |
|:------:|:---------|:-------:|
| **Module 1** | Controller → Controller | EU controller transfers to non-EU controller |
| **Module 2** | Controller → Processor | EU controller transfers to non-EU processor |
| **Module 3** | Processor → Processor | EU processor sub-transfers to non-EU sub-processor |
| **Module 4** | Processor → Controller | Non-EU processor transfers back to EU controller |

**Post-Schrems II requirements (supplementary measures):**
1. Transfer Impact Assessment (TIA) — assess third country law
2. Technical measures (encryption with EU-held keys; pseudonymization)
3. Organizational measures (policies; access restrictions; audits)
4. Contractual measures (additional commitments beyond SCCs)

---

## Chapter 7 — Comparison: GDPR vs. Other Privacy Laws

| Aspect | GDPR | CCPA/CPRA | LGPD (Brazil) | PIPL (China) |
|:------:|:----:|:---------:|:---:|:---:|
| **Legal bases** | 6 (strict) | None (processing default OK) | 10 (broad) | Similar to GDPR |
| **Consent** | Opt-in (affirmative) | Opt-out (consumer requests) | Opt-in | Opt-in (separate for sensitive) |
| **Fines** | 4% global turnover | $7,500/violation | 2% revenue (R$50M cap) | 5% revenue (¥50M) |
| **DPO** | Required (certain cases) | Not required | Required ("encarregado") | Required |
| **Breach notification** | 72 hours to DPA | Reasonable time; to consumers | Reasonable time to ANPD | Immediately to authorities |
| **Right to erasure** | Yes (Art. 17) | Yes (with broader exceptions) | Yes | Yes |
| **Data portability** | Yes (Art. 20) | Yes (CPRA) | Yes | Yes |
| **Profiling restriction** | Yes (Art. 22) | Limited | Yes | Yes |
| **Cross-border** | SCCs/BCRs/adequacy | No specific mechanism | Contract/adequacy | Security assessment/SCCs/certification |

---

## Chapter 8 — Architecture Diagrams

### 8.1 GDPR Compliance Architecture

```mermaid
graph TB
    subgraph "GDPR Technical Compliance Architecture"
        subgraph "Governance Layer"
            DPO_A[DPO<br/>• Oversight<br/>• Advisory<br/>• Contact point for DPA]
            ROPA_A[ROPA System<br/>• Processing register<br/>• Legal basis per activity<br/>• Retention periods]
            DPIA_A[DPIA Framework<br/>• Risk assessment<br/>• Threshold analysis<br/>• Mitigation tracking]
        end
        
        subgraph "Control Layer"
            CONSENT_A[Consent Management<br/>• Collection<br/>• Storage (proof)<br/>• Withdrawal<br/>• Preference center]
            LAWFUL[Legal Basis Engine<br/>• Per processing activity<br/>• Per data category<br/>• Per purpose<br/>• Documented decision]
            RETENTION_B[Retention Manager<br/>• Policy per category<br/>• Automated deletion<br/>• Legal hold exception<br/>• Audit trail]
        end
        
        subgraph "Technical Layer"
            SECURITY_A[Security (Art. 32)<br/>• Encryption (rest + transit)<br/>• Pseudonymization<br/>• Access control (RBAC)<br/>• Integrity verification]
            DSR_B[DSR Engine<br/>• Identity verification<br/>• Data discovery<br/>• Response automation<br/>• Deadline tracking (30 days)]
            BREACH_A[Breach Management<br/>• Detection (SIEM)<br/>• Assessment (risk)<br/>• Notification (72h)<br/>• Documentation]
        end
    end
    
    DPO_A --> CONSENT_A
    DPO_A --> DPIA_A
    ROPA_A --> LAWFUL
    LAWFUL --> SECURITY_A
    CONSENT_A --> DSR_B
    RETENTION_B --> SECURITY_A
```

### 8.2 Data Subject Request Workflow

```mermaid
graph TB
    subgraph "DSR Processing Workflow"
        RECEIVE[Receive Request<br/>• Channel: portal/email/post<br/>• Log timestamp<br/>• 30-day clock starts]
        
        VERIFY[Verify Identity<br/>• Confirm data subject<br/>• Prevent unauthorized access<br/>• Cannot request more data than needed]
        
        ASSESS[Assess Request<br/>• Valid right exercised?<br/>• Exemptions apply?<br/>• Manifestly unfounded/excessive?]
        
        DISCOVER[Discover Data<br/>• Search all systems<br/>• Structured + unstructured<br/>• Processors included<br/>• Backup consideration]
        
        EXECUTE[Execute Request<br/>• Access: compile response<br/>• Erasure: delete (+ notify processors)<br/>• Portability: export (JSON/CSV)<br/>• Rectify: update records]
        
        RESPOND[Respond to Data Subject<br/>• Within 30 days<br/>• Free (first copy)<br/>• Extension: +60 days (complex; inform)]
        
        DOCUMENT_DSR[Document<br/>• Decision rationale<br/>• Actions taken<br/>• Response sent<br/>• Audit trail]
    end
    
    RECEIVE --> VERIFY --> ASSESS --> DISCOVER --> EXECUTE --> RESPOND --> DOCUMENT_DSR
```

---

## Chapter 9 — Case Studies

### 9.1 Schrems II Impact — Organization Response

| Aspect | Detail |
|--------|--------|
| **Scenario** | Multinational (EU-based, 50,000 employees) using US cloud services (Microsoft 365, AWS, Salesforce) for EU employee and customer data. July 2020: Schrems II invalidates Privacy Shield. |
| **Immediate risk** | All EU→US transfers previously reliant on Privacy Shield are now UNLAWFUL |
| **Response program** | (1) **Inventory** all EU→US data flows (identified 200+ flows across 45 services). (2) **Risk assess** each flow: necessity; sensitivity; US law access (FISA 702; EO 12333). (3) **Migrate** where possible: moved EU data to EU data centers (Azure EU; AWS EU; Salesforce EU). (4) **SCCs** for remaining necessary transfers: signed new 2021 SCCs with all US processors. (5) **TIA** per transfer: documented assessment of US surveillance law risk; proportionality. (6) **Supplementary measures:** BYOK encryption (keys held in EU); pseudonymization of employee data before transfer; contractual commitments from US processors (notification of government access requests). (7) **Monitoring:** ongoing monitoring of US legal landscape; EU-US DPF adoption (2023) reduced risk. |
| **Outcome** | No DPA enforcement action. 80% of data repatriated to EU. 20% still transferred with SCCs + TIA + supplementary measures. When DPF adopted (2023): US processors self-certified → additional legal mechanism available. |
| **Cost** | ~€2M (data migration; legal fees; technical measures; project team for 18 months) |
| **Lesson** | "Don't rely on a single transfer mechanism. Build flexibility. Minimize cross-border flows by design." |

### 9.2 DPIA in Practice — AI Hiring Platform

| Aspect | Detail |
|--------|--------|
| **Scenario** | Company developing AI-based resume screening tool for enterprise clients. Processes candidate names, CVs, education, experience. Uses ML to rank candidates. |
| **DPIA triggered** | Art. 35: (1) Systematic evaluation of natural persons (profiling). (2) Automated decision with legal/significant effects (employment decisions). |
| **Risk identified** | (1) Discrimination (ML bias against protected characteristics). (2) Lack of transparency (candidates don't know how they're scored). (3) Accuracy (incorrect assessment → unfair rejection). (4) Lawful basis uncertainty (legitimate interest vs. consent for candidates). |
| **Mitigations** | (1) Bias testing: regular fairness audits (demographic parity; equalized odds); human override required for final decisions (Art. 22 compliance). (2) Transparency: candidates informed AI is used; can request explanation; can contest decision. (3) Accuracy: regular model validation; false positive/negative monitoring; human review for borderline cases. (4) Legal basis: legitimate interest (LIA documented); candidates informed; right to object + human review. (5) Data minimization: process only relevant professional data; exclude photos, names, age in ML model. |
| **DPA consultation** | Not required (residual risk reduced to acceptable level after mitigations) |
| **Art. 22 compliance** | Ensured: system provides ranking (not final decision); human recruiter makes employment decision; candidate right to contest and request human review explicitly provided. |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **AI Act + GDPR** | 2024-2026 | AI systems processing personal data must comply with BOTH; Art. 22 (automated decisions) enforcement increasing; right to explanation for AI |
| **ePrivacy Regulation** | Pending (delayed since 2017) | Replace ePrivacy Directive; stricter cookie rules; broader scope (OTT services); fines aligned with GDPR |
| **Children's protection** | 2024+ | Age verification mandate; AADC (Age Appropriate Design Code) influence; stricter enforcement for social media |
| **Cross-border simplification** | 2024-2025 | GDPR procedural regulation (proposed 2023): speed up one-stop-shop mechanism; resolve DPA cooperation delays |
| **Privacy in generative AI** | 2024+ | Training data consent; right to erasure from models; copyright intersection; purpose limitation for AI training |
| **EU-US DPF stability** | 2024-2027 | Potential Schrems III challenge; DPF 4-year review (2027); monitoring of US surveillance reforms |
| **Standard contractual clauses v2** | 2025+ | Potential update to address new technologies; AI; automated transfers |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What are the six legal bases for processing personal data under GDPR? When would you use each?

**A:**

| # | Legal Basis | When to Use | Example |
|:-:|:-----------:|:---|---|
| a | **Consent** | Data subject freely agrees for a specific purpose; can withdraw | Marketing emails; cookies; newsletter subscription |
| b | **Contract** | Processing necessary to perform a contract with the data subject | Delivering purchased goods; processing payment; employee payroll |
| c | **Legal obligation** | Required by EU or member state law (controller has no choice) | Tax reporting; anti-money laundering (AML); employee social security |
| d | **Vital interests** | Protecting someone's life (emergency only) | Hospital treating unconscious patient; disaster relief |
| e | **Public task** | Public authority exercising official functions | Tax office processing tax returns; police investigation |
| f | **Legitimate interests** | Controller has legitimate business reason, balanced against data subject rights | Fraud prevention; network security; direct marketing to existing customers (with opt-out) |

**Key rule:** Choose the MOST appropriate basis. Cannot "swap" bases later. Document the choice.

### Tier 2: Mid-Level

**Q2:** You discover your company has been collecting excessive personal data (more than needed for the stated purpose) for 2 years. What steps do you take?

**A:**

| Step | Action | Rationale |
|:----:|--------|-----------|
| 1 | **Assess scope** | Identify: what data is excessive; how many data subjects; what purpose was stated; what data exceeds that purpose |
| 2 | **Stop collection** | Immediately cease collecting excessive data fields; update forms/APIs |
| 3 | **Legal analysis** | Consult DPO: does this constitute a breach? (Art. 4(12): breach = unauthorized processing). If purpose limitation violated → potential infringement |
| 4 | **Risk assessment** | Assess risk to data subjects: is the excessive data sensitive? Has it been shared? Any harm? |
| 5 | **Delete excessive data** | Erase data that has no legal basis to retain; document deletion; verify deletion across all systems (including backups within reasonable timeframe) |
| 6 | **Notify DPA?** | If constitutes a breach with risk to rights/freedoms → 72-hour notification. If purely internal non-compliance (no risk materialized) → document; no notification required |
| 7 | **Root cause** | Why was it collected? Missing DPIA? Missing privacy review in development? Inadequate training? |
| 8 | **Preventive measures** | Implement: (a) Privacy by design review for all new features; (b) Data minimization check in product requirements; (c) Regular data mapping audits; (d) Developer training on purpose limitation |
| 9 | **Document** | Document everything: discovery; assessment; actions; timeline; accountability (Art. 5(2)) |

### Tier 3: Senior/DPO Level

**Q3:** As DPO, the marketing department wants to use existing customer purchase data to build an AI model predicting purchasing behavior, then sell predictions to third-party advertisers. Advise on GDPR compliance.

**A:**

**Analysis framework:**

| Issue | Assessment |
|:-----:|------------|
| **Original purpose** | Data collected for: fulfilling purchase contracts (Art. 6(1)(b)) |
| **New purpose** | (1) Build AI model (profiling). (2) Sell predictions to third parties. |
| **Purpose limitation (Art. 5(1)(b))** | New purpose must be compatible with original OR have independent legal basis |
| **Compatibility test (Art. 6(4))** | Factors: (a) Link between original and new purpose — WEAK (purchase fulfillment ≠ selling predictions). (b) Context of collection — customer expected purchase; NOT advertising. (c) Nature of data — purchase history is sensitive commercially. (d) Consequences — data shared with unknown third parties. (e) Safeguards — limited (sold externally). **Assessment: NOT compatible** — cannot rely on original basis |
| **New legal basis needed** | Options: (a) **Consent** — must be specific, informed, freely given for THIS purpose. (b) **Legitimate interest** — controller interest (revenue) vs. data subject expectation → data subjects DO NOT expect purchase data sold to advertisers → likely fails balancing test. **Conclusion: only CONSENT is viable** |
| **Additional requirements** | (1) **DPIA required** (Art. 35) — profiling + new technology + large scale. (2) **Transparency** — inform customers of new purpose (Art. 13/14 update). (3) **Right to object** (Art. 21) — for profiling. (4) **Art. 22** — if predictions used for automated decisions affecting customers. (5) **Third-party recipients** — must be identified in privacy notice. (6) **Data minimization** — can purpose be achieved with anonymized/aggregated data? If yes → do that instead (no longer personal data). |
| **Recommendation** | (1) Explore ANONYMIZATION first (aggregate statistics sold; not individual predictions). If truly anonymous → GDPR doesn't apply. (2) If individual-level needed: obtain SPECIFIC CONSENT with clear explanation. (3) Offer meaningful OPT-OUT for existing customers. (4) Conduct DPIA before implementation. (5) Consider reputation risk (even if legally compliant, customers may react negatively). |

---

## Chapter 12 — Cheat Sheet & Quick Reference

```
═══════════════════════════════════════════
GDPR — QUICK REFERENCE
═══════════════════════════════════════════

SCOPE (Art. 3):
  • EU establishment processing data (regardless of where subjects are)
  • Non-EU entity: offering goods/services to EU; OR monitoring EU behavior

═══════════════════════════════════════════
LEGAL BASES (Art. 6):
  (a) Consent — opt-in; specific; informed; withdrawable
  (b) Contract — necessary for performance
  (c) Legal obligation — law requires it
  (d) Vital interests — life/death
  (e) Public task — official authority
  (f) Legitimate interests — balanced (+ LIA documented)

═══════════════════════════════════════════
PRINCIPLES (Art. 5):
  1. Lawfulness, fairness, transparency
  2. Purpose limitation
  3. Data minimisation
  4. Accuracy
  5. Storage limitation
  6. Integrity & confidentiality
  7. Accountability (demonstrate compliance)

═══════════════════════════════════════════
DATA SUBJECT RIGHTS:
  Access (Art. 15): copy of data + info
  Rectification (Art. 16): correct errors
  Erasure (Art. 17): "right to be forgotten"
  Restriction (Art. 18): stop processing; keep stored
  Portability (Art. 20): machine-readable; transfer
  Object (Art. 21): stop processing (legitimate interest/marketing)
  Automated decisions (Art. 22): human intervention right
  
  Response deadline: 30 days (extendable by 60 days)
  Cost: Free (first copy; reasonable fee for excessive requests)

═══════════════════════════════════════════
FINES:
  Lower tier: €10M or 2% global turnover
    → Controller/processor obligations; DPO; DPIA
  Upper tier: €20M or 4% global turnover
    → Principles; legal basis; rights; transfers

═══════════════════════════════════════════
BREACH NOTIFICATION:
  To DPA: ≤72 hours (if risk to rights/freedoms)
  To individuals: "without undue delay" (if HIGH risk)
  Document ALL breaches (regardless of notification)

═══════════════════════════════════════════
TRANSFERS (Chapter V):
  1. Adequacy decision? → OK
  2. SCCs + TIA + supplementary measures → OK
  3. BCRs (intra-group) → OK
  4. Derogations (Art. 49) → specific situations only
  
  POST-SCHREMS II: Always do TIA for non-adequate countries
  DPF (2023): US companies self-certify with DoC

═══════════════════════════════════════════
DPO REQUIRED WHEN (Art. 37):
  • Public authority/body (except courts)
  • Core activities: regular/systematic monitoring at large scale
  • Core activities: large-scale processing of special categories

DPIA REQUIRED WHEN (Art. 35):
  • Systematic profiling with legal/significant effects
  • Large-scale special category processing
  • Systematic public area monitoring
  • DPA's published blacklist

═══════════════════════════════════════════
KEY DEADLINES:
  DSR response: 30 days (+60 extension)
  Breach to DPA: 72 hours
  DPIA: BEFORE processing begins
  Records of processing: maintained continuously
```

---

*End of Document — 01_GDPR_Deep_Dive.md*
