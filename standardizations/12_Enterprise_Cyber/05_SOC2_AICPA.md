# SOC 2 — Trust Services Criteria (AICPA)

**Topic:** SOC 2 Type I & Type II Reports — Service Organization Controls for Trust Services Criteria  
**Standard:** SOC 2 (based on AICPA Trust Services Criteria 2017, with 2022 updates)  
**SDO:** AICPA (American Institute of Certified Public Accountants) / ASEC (Assurance Services Executive Committee)  
**Audience:** SaaS vendors, compliance managers, audit readiness teams, CISOs, B2B sales teams, customers evaluating vendors  
**Prerequisites:** Basic information security concepts, understanding of service organizations, internal controls fundamentals

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 1992 | SAS 70 published (AICPA) | Original service organization audit standard; financial controls focus |
| 2006 | SSAE 16 proposed | Replacement for SAS 70 with enhanced standards |
| 2010 | SSAE 16 effective (replaces SAS 70) | Stronger attestation standard; SOC 1 framework |
| 2011 | **SOC 2 introduced** | Trust Services Principles for non-financial controls; security focus |
| 2011 | SOC 3 introduced | Public-facing "seal" version of SOC 2 (limited detail) |
| 2014 | Trust Services Principles updated | Refined criteria; TSP 100 series |
| 2017 | **Trust Services Criteria (TSC) 2017** | Major revision; aligned with COSO 2013; current basis for SOC 2 |
| 2018 | SSAE 18 replaces SSAE 16 | Updated attestation standard for SOC engagements |
| 2019 | SOC for Cybersecurity | Organization-level cybersecurity risk management reporting |
| 2022 | TSC point-in-time updates | Clarifications on criteria interpretation (privacy, confidentiality) |
| 2023 | SOC 2+ emerges (SOC 2 + additional frameworks) | Combined reports: SOC 2 + HIPAA, SOC 2 + CSA STAR, SOC 2 + ISO 27001 |
| 2024 | AI/automation impacts | Automated evidence collection becoming standard (Vanta, Drata, Secureframe) |

### 1.2 SOC Report Types

| Report | Full Name | Purpose | Audience | Distribution |
|--------|-----------|---------|----------|-------------|
| **SOC 1** | Service Organization Controls 1 | Internal controls over financial reporting (ICFR) | Financial auditors; entities impacting client financials | Restricted (NDA/contract) |
| **SOC 2 Type I** | SOC 2 Point-in-Time | Design suitability of controls at a specific date | Security/compliance teams evaluating vendors | Restricted (NDA) |
| **SOC 2 Type II** | SOC 2 Period of Time | Design AND operating effectiveness over a period (6-12 months) | **Most valuable**; demonstrates sustained compliance | Restricted (NDA) |
| **SOC 3** | General Use Report | Seal/badge; summarizes SOC 2 without control details | Public (marketing, website) | Publicly available |
| **SOC for Cybersecurity** | Org-level cyber risk | Enterprise cybersecurity program effectiveness | Board; management; general stakeholders | Management/general use |

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 Trust Services Criteria (TSC) — Five Principles

```mermaid
graph TB
    subgraph "Trust Services Criteria"
        SEC[SECURITY (CC Series)<br/>═══════════════════<br/>MANDATORY for all SOC 2<br/>───────────────────<br/>CC1: Control Environment<br/>CC2: Communication & Information<br/>CC3: Risk Assessment<br/>CC4: Monitoring Activities<br/>CC5: Control Activities<br/>CC6: Logical & Physical Access<br/>CC7: System Operations<br/>CC8: Change Management<br/>CC9: Risk Mitigation]
        
        AVAIL[AVAILABILITY (A Series)<br/>═══════════════════<br/>Optional principle<br/>───────────────────<br/>A1.1: Capacity management<br/>A1.2: Recovery/continuity<br/>A1.3: Backup/restoration]
        
        PI[PROCESSING INTEGRITY (PI Series)<br/>═══════════════════<br/>Optional principle<br/>───────────────────<br/>PI1.1: Complete/accurate/timely<br/>PI1.2: Input validation<br/>PI1.3: Processing verification<br/>PI1.4: Output review<br/>PI1.5: Correction handling]
        
        CONF[CONFIDENTIALITY (C Series)<br/>═══════════════════<br/>Optional principle<br/>───────────────────<br/>C1.1: Confidential info identified<br/>C1.2: Confidential info disposed]
        
        PRIV[PRIVACY (P Series)<br/>═══════════════════<br/>Optional principle<br/>───────────────────<br/>P1-P8: Notice, choice, collection,<br/>use, disclosure, access, quality,<br/>monitoring/enforcement]
    end
    
    SEC --- AVAIL
    SEC --- PI
    SEC --- CONF
    SEC --- PRIV
```

### 2.2 Common Criteria (CC) — Security Principle Detail

| CC Series | Title | Focus Areas |
|-----------|-------|------------|
| CC1 | Control Environment | Governance, ethics, oversight, accountability, commitment to competence |
| CC2 | Communication and Information | Internal/external communication, relevant/quality information |
| CC3 | Risk Assessment | Risk identification, analysis of changes, fraud risk |
| CC4 | Monitoring Activities | Ongoing monitoring, separate evaluations, deficiency communication |
| CC5 | Control Activities | Selection/development of controls, technology general controls, policy deployment |
| CC6 | Logical and Physical Access Controls | Access provisioning, authentication, physical access, removal |
| CC7 | System Operations | Infrastructure/software management, detection of changes, incident management |
| CC8 | Change Management | Authorized changes, testing, approval, technology changes |
| CC9 | Risk Mitigation | Business disruption risk, vendor risk, mitigation through acceptance |

### 2.3 SOC 2 Report Structure

| Section | Content | Author |
|---------|---------|--------|
| Section I | Independent Service Auditor's Report (Opinion) | CPA Firm |
| Section II | Management's Assertion | Service Organization |
| Section III | System Description | Service Organization |
| Section IV | Trust Services Criteria, Controls, Tests, Results | CPA Firm (tests) + Service Org (controls) |
| Section V | Other Information (optional) | Service Organization |

---

## Chapter 3 — Technical Deep Dive

### 3.1 Common Criteria Control Mapping to Technical Controls

| TSC Criteria | Typical Controls Implemented | Technology/Evidence |
|-------------|----------------------------|-------------------|
| CC6.1 (Logical access security) | RBAC/ABAC; least privilege; access reviews | IAM platform config; access review screenshots; permission matrices |
| CC6.2 (User authentication) | MFA; password policy; SSO | MFA enrollment reports; IdP config; authentication logs |
| CC6.3 (Access provisioning) | Automated provisioning from HR; approval workflows | Provisioning system config; approval tickets; access request audit trail |
| CC6.6 (Access removal) | Deprovisioning on termination; periodic review | Termination checklist evidence; access review completion; user disable logs |
| CC6.7 (Data access restriction) | Encryption at rest + transit; network segmentation | TLS config; disk encryption; firewall rules; VPN config |
| CC7.1 (Infrastructure/software monitoring) | Vulnerability scanning; endpoint protection; SIEM | Scan reports; EDR dashboard; SIEM alert evidence |
| CC7.2 (Anomaly detection) | SIEM alerting; behavioral analytics; anomaly thresholds | Alert rules; triggered alert examples; response evidence |
| CC7.3 (Security incident handling) | IR plan; incident tickets; lessons learned | IR policy; incident response records; post-mortem reports |
| CC7.4 (Incident response) | Containment, eradication, recovery procedures | IR playbooks; incident timeline documentation |
| CC8.1 (Change management) | Change approval workflows; testing requirements | Change tickets; CAB meeting notes; test evidence; deployment records |
| CC9.2 (Third-party risk) | Vendor risk assessments; SLA monitoring | Vendor risk register; assessment reports; SOC 2 reviews of subservice orgs |
| A1.2 (Recovery) | DR plan; backup verification; RTO/RPO testing | DR plan document; backup success reports; recovery test results |

### 3.2 SOC 2 Type II — Observation Period

| Aspect | Typical Requirements |
|--------|---------------------|
| Minimum period | 6 months (some auditors accept 3 months for first report) |
| Standard period | 12 months (most common; highest confidence) |
| Evidence frequency | Monthly or quarterly sampling (depends on control) |
| Continuous controls | Must show evidence across entire period (not just start/end) |
| Population/sampling | Auditor samples from full population (e.g., 25 access reviews out of 500 users) |
| Exceptions | Deviations documented; evaluated for severity; may not affect opinion if isolated |
| Qualified opinion triggers | Systemic failures; large number of exceptions; control not operating for significant period |

### 3.3 SOC 2 Control Examples (Detailed)

**Control CC6.3 (Access Provisioning):**
```
Control Description: 
"New logical access requests are documented, approved by the appropriate manager, 
and provisioned by the IT team within the organization's access management system. 
Access is granted based on the user's role and follows the principle of least privilege."

Test Performed (Type II):
Auditor selected a sample of 25 new user access provisioning events from the 
observation period. For each sampled event, auditor inspected:
(a) Access request ticket exists with documented justification
(b) Appropriate manager approval recorded before provisioning
(c) Access granted matches role-based access matrix
(d) No excess privileges beyond role definition

Results:
No exceptions noted. All 25 sampled events had documented requests, 
appropriate approvals, and role-aligned access grants.
```

---

## Chapter 4 — Implementation Guide

### 4.1 SOC 2 Readiness Roadmap

```mermaid
graph TB
    subgraph "Phase 1: Readiness Assessment (Months 1-2)"
        R1[Scope Definition<br/>• Which principles? (Security +?)<br/>• System boundaries<br/>• Subservice organizations<br/>• Carve-out vs. inclusive method]
        R2[Gap Assessment<br/>• Current controls vs. TSC requirements<br/>• Documentation gaps<br/>• Process gaps<br/>• Technology gaps]
        R3[Auditor Selection<br/>• CPA firm with SOC 2 experience<br/>• Industry expertise<br/>• Engagement letter<br/>• Timeline agreement]
    end
    
    subgraph "Phase 2: Implementation (Months 2-5)"
        I1[Control Design<br/>• Write control descriptions<br/>• Map controls to TSC criteria<br/>• Design monitoring procedures<br/>• Define evidence collection]
        I2[Control Implementation<br/>• Deploy missing controls<br/>• Configure tooling<br/>• Implement processes<br/>• Document procedures]
        I3[Evidence Automation<br/>• GRC platform setup (Vanta/Drata)<br/>• Automated evidence collection<br/>• Continuous monitoring<br/>• Alert configuration]
    end
    
    subgraph "Phase 3: Observation Period (Months 5-11+)"
        O1[Controls Operating<br/>• Consistently execute all controls<br/>• Collect evidence continuously<br/>• Document exceptions<br/>• Address deviations immediately]
        O2[Internal Monitoring<br/>• Monthly control reviews<br/>• Evidence completeness checks<br/>• Readiness self-assessment<br/>• Address gaps before audit]
    end
    
    subgraph "Phase 4: Audit (Months 11-13)"
        A1[Auditor Engagement<br/>• Provide system description<br/>• Walkthroughs of controls<br/>• Evidence submission<br/>• Inquiry/interview sessions]
        A2[Testing & Reporting<br/>• Auditor tests controls<br/>• Identifies exceptions<br/>• Drafts opinion letter<br/>• Report issuance (~4-6 weeks)]
    end
    
    R1 --> R2 --> R3 --> I1 --> I2 --> I3 --> O1 --> O2 --> A1 --> A2
```

### 4.2 Principle Selection Guide

| If you handle... | Select these principles | Why |
|-----------------|----------------------|-----|
| Any customer data (minimum) | **Security** (CC1-CC9) | Always required; covers security baseline |
| SaaS with uptime SLAs | Security + **Availability** | Customers rely on your uptime; SLAs need evidence |
| Financial data processing | Security + **Processing Integrity** | Must prove data processed correctly/completely |
| Sensitive/classified data | Security + **Confidentiality** | Must prove data protected from unauthorized disclosure |
| Consumer PII (direct) | Security + **Privacy** | Privacy principles if you're the data controller |
| Healthcare data (HIPAA) | Security + Availability + Confidentiality + (Privacy optional) | HIPAA-adjacent requirements |
| Typical B2B SaaS | Security + Availability + Confidentiality | Most common combination for enterprise SaaS |

### 4.3 GRC Automation Platforms

| Platform | Key Features | Cost (approx.) | Best For |
|----------|-------------|----------------|----------|
| Vanta | Automated evidence collection; 30+ integrations; auditor workflows; real-time readiness | $10K-$50K/year | Mid-market SaaS; fast implementation |
| Drata | Continuous monitoring; 75+ integrations; employee onboarding; risk assessment | $12K-$60K/year | SaaS companies; ISO + SOC 2 combined |
| Secureframe | Automated compliance; personnel management; vendor management; training | $10K-$40K/year | Startups; first-time SOC 2 |
| Thoropass (Laika) | End-to-end compliance (platform + auditor); AI-powered | Varies | Companies wanting bundled service |
| Sprinto | Automated evidence; risk management; training; cloud-first | $8K-$30K/year | Startups and SMBs; cost-sensitive |
| Tugboat Logic (OneTrust) | Policy generation; evidence automation; risk | $15K-$50K/year | Mid-market; multi-framework |
| AuditBoard | Enterprise GRC; SOX + SOC 2 + ISO; advanced reporting | $50K-$200K/year | Large enterprise; multi-compliance |

---

## Chapter 5 — Certification & Audit

### 5.1 Audit Engagement Process

| Phase | Activities | Duration |
|-------|-----------|----------|
| Planning | Engagement letter; scope agreement; timeline; control matrix review | 2-4 weeks |
| Walkthrough | Auditor reviews system description; control walkthroughs; process understanding | 1-2 weeks |
| Testing | Auditor selects samples; tests controls; documents results; identifies exceptions | 3-6 weeks |
| Reporting | Draft report review; management response to exceptions; final opinion | 2-4 weeks |
| Delivery | Final report issued; NDA distribution to customers | 1 week |

### 5.2 Audit Opinions

| Opinion Type | Meaning | Implication |
|-------------|---------|-------------|
| **Unqualified (Clean)** | Controls suitably designed AND operating effectively | Best outcome; no material exceptions |
| **Qualified** | Most controls effective, but one or more material exceptions | Report still usable but customers may have concerns |
| **Adverse** | Material weaknesses across the system | Rarely issued (org would likely withdraw before adverse opinion) |
| **Disclaimer** | Auditor unable to obtain sufficient evidence | Cannot form opinion; systemic evidence gaps |

### 5.3 Common Exceptions and How to Avoid

| Exception Category | Example | Prevention |
|-------------------|---------|-----------|
| Access review not performed | Quarterly review missed for one quarter | Calendar automation; GRC platform alerts; backup owner |
| Terminated user still active | Access not removed within SLA (e.g., 24 hours) | Automated deprovisioning from HR system → IdP |
| Change without approval | Production change deployed without CAB/peer approval | CI/CD gate requiring approval; branch protection rules |
| Backup not tested | Annual DR test not performed | Schedule with executive sponsor; non-negotiable |
| Vulnerability not remediated | Critical vulnerability open beyond SLA (e.g., >14 days) | Automated tracking; escalation workflow; exception process |
| Missing evidence | Evidence not collected for specific period | Automated collection (GRC platform); gap alerts |
| Vendor assessment overdue | Annual vendor risk review missed | Calendar automation; vendor management workflow |

### 5.4 Cost Breakdown

| Component | First Year | Subsequent Years |
|-----------|-----------|-----------------|
| GRC Platform | $10K-$50K | $10K-$50K (annual) |
| Readiness Assessment (consultant) | $15K-$40K | N/A (first year only) |
| Control Implementation (gaps) | $20K-$100K | $5K-$20K (maintenance) |
| Penetration Test | $15K-$40K | $15K-$40K (annual) |
| SOC 2 Audit (CPA firm) | $30K-$100K | $30K-$80K (annual) |
| Staff Time | $50K-$100K equivalent | $30K-$50K |
| **Total (Mid-size SaaS)** | **$150K-$350K** | **$100K-$250K** |

---

## Chapter 6 — Regional & Domain Considerations

### 6.1 SOC 2 Global Relevance

| Region | SOC 2 Usage | Alternative/Complement |
|--------|-------------|----------------------|
| **United States** | Primary vendor trust mechanism for B2B SaaS | N/A — SOC 2 is the standard |
| **Canada** | Widely accepted (CSAE 3416 is Canadian equivalent) | SOC 2 preferred by US-based customers |
| **UK** | Accepted but ISO 27001 preferred locally | ISAE 3402 (Type I/II) — equivalent standard |
| **EU** | Growing acceptance; ISO 27001 still primary | ISAE 3402; ISO 27001 for local requirements |
| **Australia** | Increasing acceptance; ASAE 3402 locally | GS 007 (Australian equivalent); ISO 27001 |
| **India** | Growing with US-serving IT companies | ISO 27001 + SOC 2 for US clients |
| **Japan** | Limited; SOC 1 more common (financial focus) | ISMAP (government cloud); ISO 27001 |
| **Global SaaS** | Rapidly becoming global standard for B2B | ISO 27001 for non-US customers; SOC 2 for US |

### 6.2 SOC 2+ (Extended Reports)

| SOC 2+ Extension | Additional Criteria | Use Case |
|-----------------|-------------------|----------|
| SOC 2 + HIPAA | HIPAA Security Rule requirements mapped | Healthcare SaaS vendors |
| SOC 2 + HITRUST | HITRUST CSF controls | Healthcare enterprise vendors |
| SOC 2 + CSA STAR | Cloud Controls Matrix (CCM) | Cloud service providers |
| SOC 2 + NIST 800-171 | CUI protection requirements | Defense contractor adjacent |
| SOC 2 + ISO 27001 | ISO 27001 Annex A controls | Global customers requiring both |
| SOC 2 + PCI DSS | Payment security requirements | Payment processors |
| SOC 2 + GDPR | GDPR Article 32 security measures | EU data processing SaaS |

---

## Chapter 7 — Comparison

### 7.1 SOC 2 Type I vs. Type II

| Dimension | Type I | Type II |
|-----------|--------|---------|
| Evaluation scope | Design suitability at a point in time | Design AND operating effectiveness over a period |
| Period covered | Single date (snapshot) | 6-12 months continuous |
| Evidence depth | Policies/configs exist; architecture reviewed | Policies + logs + evidence across time period |
| Customer value | Low-medium (shows intent; no proof of consistency) | **High** (proves sustained control operation) |
| Timeline to obtain | 3-6 months (implement + snapshot audit) | 9-16 months (implement + observation + audit) |
| Cost | $30K-$60K (audit) | $50K-$100K+ (audit) |
| Typical use | Bridge report while building toward Type II | Primary vendor trust report; customer requirement |
| Auditor testing | Inquiry + inspection at point-in-time | Inquiry + inspection + observation + reperformance over period |

### 7.2 SOC 2 vs. ISO 27001

| Dimension | SOC 2 Type II | ISO 27001:2022 |
|-----------|---------------|----------------|
| Type | Attestation report (auditor opinion) | Certification (certificate issued) |
| Output | Detailed report (50-200 pages) with control tests | Certificate + SOA (summary) |
| Assessor | Licensed CPA firm (AICPA members) | Accredited Certification Body |
| Standard | Trust Services Criteria (AICPA) | ISO/IEC 27001 (ISO) |
| Scope | Service system boundaries | Organizational ISMS boundaries |
| Frequency | Annual (every 12 months) | 3-year cycle (annual surveillance) |
| Detail visible to customer | Full control descriptions + test results | Certificate only (SOA if shared) |
| Control framework | CC1-CC9 + optional principles | 93 Annex A controls |
| Risk assessment | Embedded (CC3) | Mandatory (Clause 6.1) |
| Geographic preference | US + global SaaS | Global (especially non-US) |
| Cost (annual) | $50K-$150K (audit) | $30K-$100K (audit) |
| Time to achieve | 9-16 months (Type II) | 6-12 months (certification) |
| Public visibility | SOC 3 (summary badge) | Certificate publicly referenceable |
| Complementary | ISO 27001 certification | SOC 2 report |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 SOC 2 Engagement Lifecycle

```mermaid
graph TB
    subgraph "Scoping & Planning"
        SCOPE[Define System<br/>• Service commitments<br/>• System boundaries<br/>• Infrastructure/software/people/procedures/data<br/>• Subservice organizations<br/>• Carve-out vs. Inclusive]
        PRINC[Select Principles<br/>• Security (mandatory)<br/>• + Availability?<br/>• + Processing Integrity?<br/>• + Confidentiality?<br/>• + Privacy?]
    end
    
    subgraph "Implementation"
        CTRL[Design Controls<br/>• Map to TSC criteria<br/>• Write descriptions<br/>• Implement technically<br/>• Document procedures]
        EVID[Evidence Collection<br/>• Automated (GRC platform)<br/>• Manual (policies, reviews)<br/>• Continuous (logs, monitoring)<br/>• Periodic (assessments, tests)]
    end
    
    subgraph "Observation (Type II)"
        OBS[6-12 Month Period<br/>• Controls operating consistently<br/>• Evidence accumulating<br/>• Deviations documented<br/>• Continuous monitoring]
    end
    
    subgraph "Audit"
        WALK[Walkthroughs<br/>• Auditor reviews each control<br/>• Understands design<br/>• Plans test approach]
        TEST[Testing<br/>• Sample selection<br/>• Inquiry, inspection,<br/>  observation, reperformance<br/>• Document results]
        REPORT[Reporting<br/>• Opinion letter (qualified/unqualified)<br/>• System description<br/>• Controls, tests, results<br/>• Exceptions noted]
    end
    
    SCOPE --> PRINC --> CTRL --> EVID --> OBS --> WALK --> TEST --> REPORT
```

### 8.2 Trust Services Criteria to Technical Controls Mapping

```mermaid
graph TB
    subgraph "CC6: Logical & Physical Access"
        CC6_1[CC6.1: Security policies for access]
        CC6_2[CC6.2: Authentication mechanisms]
        CC6_3[CC6.3: Access provisioning & approval]
        CC6_6[CC6.6: Access removal on change/termination]
        CC6_7[CC6.7: Restriction of system modifications]
    end
    
    subgraph "Technical Implementation"
        IAM[Identity & Access Management<br/>• SSO (Okta, Azure AD)<br/>• MFA (FIDO2, TOTP)<br/>• RBAC permissions<br/>• Automated provisioning]
        PAM[Privileged Access Management<br/>• Just-in-time admin access<br/>• Session recording<br/>• Break-glass procedures]
        REVIEW[Access Reviews<br/>• Quarterly privilege review<br/>• Manager certification<br/>• Excess access removal]
        DEPROV[Deprovisioning<br/>• HR → IdP automation<br/>• Same-day disable on termination<br/>• Offboarding checklist]
    end
    
    CC6_1 --> IAM
    CC6_2 --> IAM
    CC6_3 --> IAM
    CC6_3 --> PAM
    CC6_6 --> DEPROV
    CC6_6 --> REVIEW
    CC6_7 --> PAM
```

---

## Chapter 9 — Case Studies

### 9.1 Early-Stage SaaS — First SOC 2 Type II

| Aspect | Detail |
|--------|--------|
| Company | Series B SaaS startup; 60 employees; healthcare-adjacent (not HIPAA BAA but customer expectation) |
| Motivation | Enterprise prospect ($1M ARR deal) requires SOC 2 Type II; 9-month deadline to close deal |
| Starting state | No formal security program; AWS + GCP cloud; good engineering practices but undocumented; no GRC tooling |
| Approach | Aggressive timeline: (1) Month 1: Drata deployed; gap assessment; principles selected (Security + Availability + Confidentiality). (2) Month 2-3: Policies written (used templates from GRC platform); controls implemented. (3) Month 3: Engaged CPA firm (Schellman); SOC 2 Type I conducted (bridge report for customer). (4) Month 3-9: Type II observation period begins; controls operate continuously; evidence auto-collected. (5) Month 9: Auditor begins Type II fieldwork. (6) Month 10: Report issued (unqualified opinion). |
| Key controls implemented | (1) MFA enforced (Google Workspace + AWS). (2) Access reviews (quarterly via Drata automation). (3) Vulnerability scanning (Snyk + AWS Inspector; automated). (4) Change management (GitHub PR reviews + Terraform plan/apply; required approvals). (5) Incident response plan (documented + tabletop exercise). (6) Vendor risk management (top-10 vendors assessed). (7) Background checks (for all employees, retroactive). (8) Encryption (TLS 1.2+ in transit; AES-256 at rest; AWS KMS). |
| Exceptions | 1 exception: single deployment without required peer review approval (developer emergency hotfix). Noted by auditor as isolated exception; did not affect overall opinion. |
| Result | Clean SOC 2 Type II report issued at month 10. Enterprise deal closed. 4 additional enterprise prospects immediately engaged (report shared under NDA). |
| Cost | $175K total (Drata $18K, audit $65K, pen test $20K, consulting $30K, staff time $42K) |
| Timeline | Type I at 3 months (bridge); Type II report at 10 months |
| Lesson | **Automated GRC platforms make aggressive timelines achievable. Type I as bridge report buys time for Type II. Start observation period immediately — the clock matters most.** |

### 9.2 Enterprise SOC 2 + ISO 27001 Harmonization

| Aspect | Detail |
|--------|--------|
| Company | Global B2B SaaS; 2,000 employees; handles financial + healthcare data; $200M ARR |
| Challenge | US customers require SOC 2; European customers require ISO 27001; maintaining two separate compliance programs is expensive |
| Strategy | Unified GRC framework: single control set mapped to both SOC 2 TSC and ISO 27001 Annex A simultaneously |
| Mapping approach | (1) Started with SOC 2 CC criteria (most prescriptive). (2) For each CC criterion, identified corresponding ISO 27001 Annex A controls. (3) Added ISO 27001-specific controls not covered by SOC 2 (e.g., supplier management depth, physical security). (4) Result: 95 unified controls (superset satisfying both). (5) Evidence collected once, used for both audits. |
| Tooling | ServiceNow GRC platform (enterprise-grade; multi-framework); integrated with Qualys, CrowdStrike, Okta, AWS for automated evidence |
| Audit coordination | ISO 27001 surveillance audit in March; SOC 2 Type II period Jan-Dec (audit in January). Same evidence base. Overlapping controls tested once. |
| Results | 35% reduction in compliance effort (vs. running separately). Single evidence collection. Unified risk register. One set of policies (satisfies both). Annual cost savings: ~$200K. |
| Cost | $450K/year (combined: GRC platform $150K, ISO audit $60K, SOC 2 audit $100K, pen tests $40K, staff 2 FTE $200K) — vs. estimated $700K if run separately |

---

## Chapter 10 — Future Evolution & Industry Trends

| Trend | Timeline | Impact |
|-------|----------|--------|
| Continuous compliance (replacing point-in-time) | Now-2026 | Real-time evidence; auditors accessing live dashboards; moving toward continuous assurance |
| AI-powered audit | 2024-2027 | AI analyzing evidence; automated exception detection; reduced auditor manual work |
| SOC 2+ expansion | Now | More framework combinations (GDPR, NIST, CSA STAR); single audit, multiple reports |
| Automated auditor access | 2024-2026 | Auditors accessing GRC platforms directly; reducing request-response cycles |
| SOC 2 for AI/ML systems | 2024-2025 | New criteria/guidance for AI system security, fairness, reliability |
| Privacy principle evolution | 2024-2026 | Privacy criteria evolving with state privacy laws (CCPA, CPRA, state laws) |
| Vendor concentration risk | Now | Single GRC platform outage affects thousands of companies' compliance evidence |
| SOC 2 as table stakes | Now | No longer differentiator — expected baseline for any B2B SaaS; bare minimum |
| Cybersecurity insurance requiring SOC 2 | Now | Insurers requesting SOC 2 report as underwriting input |
| International SOC 2 equivalent standards | 2024-2027 | ISAE 3000/3402 alignment; global equivalency acceptance growing |
| Zero Trust integration | Now | SOC 2 controls reflecting ZTA principles (continuous verification, least privilege) |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is the difference between SOC 1, SOC 2, and SOC 3 reports?  
**A:** **SOC 1** reports on Internal Controls over Financial Reporting (ICFR). Used when a service organization's controls could affect a user entity's financial statements (e.g., payroll processor, payment platform). Governed by SSAE 18. **SOC 2** reports on Trust Services Criteria (Security + optional Availability, Processing Integrity, Confidentiality, Privacy). Used for non-financial controls — proving a technology service is secure and reliable. Most relevant for SaaS vendors. Restricted distribution (under NDA). **SOC 3** is a simplified, public-facing summary of a SOC 2 report — same assessment but without detailed control descriptions or test results. Used as a marketing "trust seal" on websites. All three come in Type I (point-in-time) and Type II (period of time).

**Q2:** Why do customers prefer SOC 2 Type II over Type I?  
**A:** Type I only proves controls are **suitably designed** at a single point in time — a snapshot. It says "on this specific date, the right controls were in place." It doesn't prove they work consistently. Type II proves controls are both suitably designed AND **operating effectively** over a period (typically 12 months). It answers: "Were these controls working every day for the past year?" Type II includes test results showing the auditor actually verified control operation over time (sampled access reviews, checked logs, verified changes were approved). Customers trust Type II because it demonstrates sustained security — not just good intentions on audit day.

### Tier 2: Mid-Level

**Q3:** You have 9 months to achieve SOC 2 Type II from zero. Walk through your project plan including principle selection, auditor engagement, and how to handle the observation period constraint.  
**A:**

**Month 1: Foundation**
- Select principles: Security (mandatory) + Availability + Confidentiality (typical B2B SaaS)
- Select GRC platform (Vanta/Drata — needed for automated evidence)
- Engage auditor (CPA firm) — critical to engage early; they provide criteria interpretation
- Gap assessment: map current state against TSC criteria

**Month 2-3: Implementation**
- Write all policies (15-20 policies; templates from GRC platform accelerate)
- Implement missing controls (MFA, access reviews, change management, vulnerability management, IR plan, vendor management, backup testing)
- Configure automated evidence collection
- Deploy technical controls (EDR, SIEM, encryption)

**Month 3: Observation Period Starts**
- All controls must be operating from this point forward
- 6-month minimum observation period means Type II report can cover months 3-9
- Every control must have evidence for every month/quarter in this period

**Month 3 (parallel): SOC 2 Type I**
- Get Type I report as bridge (share with customers while waiting for Type II)
- Type I proves design is suitable NOW; buys customer confidence

**Months 3-9: Operate and Collect Evidence**
- Monthly: access reviews, vulnerability scan reviews, backup verification
- Quarterly: vendor risk reviews, security awareness training, DR test
- Continuous: logs, change management tickets, incident documentation
- Monitor for gaps; fix immediately if evidence missing

**Month 8-9: Auditor Fieldwork**
- Auditor begins testing (can start before period ends for efficiency)
- Provide evidence packages; walkthroughs; interviews
- Address any preliminary findings immediately

**Month 9-10: Report Issuance**
- Auditor finalizes testing after observation period ends
- Draft report reviewed; management responses to any exceptions
- Final report issued (unqualified opinion target)

**Key risk:** 6-month observation is the hard constraint. Cannot compress below this. Starting evidence collection on Day 1 of Month 3 is critical.

### Tier 3: Senior

**Q4:** Design a SOC 2 compliance program for a multi-product SaaS company with 5 products, shared infrastructure, and a goal of minimizing audit cost while satisfying diverse customer requirements.  
**A:** [Full answer covers: (1) Inclusive vs. Carve-out decisions for subservice orgs (AWS). (2) Single system description covering shared infrastructure + product-specific controls. (3) Common controls framework (shared auth, network, monitoring) + product-specific controls. (4) Single SOC 2 report with system components clearly described (vs. 5 separate reports = 5× cost). (5) Modular system description allowing customers to identify their product. (6) Principle selection matrix per product (some need PI, some need Privacy). (7) Complementary controls vs. user entity controls clearly documented. (8) Audit efficiency: shared controls tested once; product controls sampled across products. (9) Annual cost optimization: $120K for unified report vs. $400K+ for 5 separate reports.]

---

## Chapter 12 — Cheat Sheet & Quick Reference

### SOC 2 Key Facts

```
Report Types:       SOC 2 Type I (point-in-time) / Type II (period, 6-12 months)
Principles:         5 (Security MANDATORY + Availability, PI, Confidentiality, Privacy)
Common Criteria:    CC1-CC9 (Security principle; always included)
Assessor:           Licensed CPA firm (must be AICPA member)
Frequency:          Annual (Type II covers 12-month period)
Distribution:       Restricted (under NDA to customers/prospects)
Cost (audit only):  $50K-$150K per year
Total program:      $150K-$350K first year; $100K-$250K ongoing
```

### Trust Services Criteria Quick Reference

```
SECURITY (CC1-CC9):             ALWAYS REQUIRED
  CC1: Control Environment       CC6: Logical & Physical Access
  CC2: Communication & Info      CC7: System Operations
  CC3: Risk Assessment           CC8: Change Management
  CC4: Monitoring Activities     CC9: Risk Mitigation
  CC5: Control Activities

AVAILABILITY (A1):              SLAs/uptime commitments
PROCESSING INTEGRITY (PI1):     Data processed correctly/completely
CONFIDENTIALITY (C1):           Sensitive data protected from disclosure
PRIVACY (P1-P8):                Personal information lifecycle management
```

### Type I vs. Type II

```
Type I:   Design of controls at a POINT IN TIME (snapshot; faster to obtain)
Type II:  Design + Operating Effectiveness over a PERIOD (6-12 months; more valuable)

Customer preference: Type II >>> Type I (always)
```

### SOC 2 Timeline (Minimum)

```
Month 1-2:    Scope + gap assessment + auditor selection
Month 2-4:    Control implementation + policy writing
Month 4:      Observation period STARTS (controls operating)
Month 4-10:   6-month observation (controls evidenced continuously)
Month 10-11:  Auditor fieldwork (testing)
Month 11-12:  Report issued

MINIMUM: ~10-12 months for first Type II report
BRIDGE: Get Type I at month 4 for customer conversations
```

### Common SOC 2 Controls (What Auditors Check)

```
ACCESS:    MFA enabled? Access reviews quarterly? Terminated users removed same-day?
CHANGES:   All prod changes approved? Peer review? Testing before deploy?
VULNERABILITIES: Regular scanning? Critical patched within SLA? Pen test annual?
INCIDENTS: IR plan exists and tested? Incidents documented? Post-mortems done?
VENDORS:   Critical vendors assessed? SOC 2 reports reviewed? Contracts include security?
BACKUPS:   Backups running? Restoration tested? RTO/RPO defined and met?
ENCRYPTION: Data encrypted in transit (TLS) and at rest (AES-256)?
LOGGING:   Security events logged? Alerts configured? Logs retained (1 year+)?
```

---

*End of Document — 05_SOC2_AICPA.md*
