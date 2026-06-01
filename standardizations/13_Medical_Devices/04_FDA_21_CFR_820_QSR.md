# FDA 21 CFR Part 820 — Quality System Regulation (QSR) & QMSR

**Topic:** Quality System Regulation for medical device manufacturing and the 2024 QMSR alignment with ISO 13485  
**Standard:** 21 CFR Part 820 (Quality System Regulation); Final Rule 2024 — Quality Management System Regulation (QMSR)  
**SDO:** U.S. Food and Drug Administration (FDA), Center for Devices and Radiological Health (CDRH)  
**Audience:** Quality engineers, regulatory affairs specialists, manufacturing engineers, design engineers, CAPA specialists  
**Prerequisites:** Basic GMP concepts, ISO 13485 familiarity, understanding of FDA regulatory framework (510(k), PMA, De Novo)

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 1938 | Federal Food, Drug, and Cosmetic Act (FD&C Act) | FDA authority established |
| 1976 | Medical Device Amendments | Device classification system (Class I/II/III); GMP requirements |
| 1978 | 21 CFR Part 820 (Original GMP) | First device GMP regulation; manufacturing controls |
| 1990 | Safe Medical Devices Act (SMDA) | Post-market reporting; device tracking |
| 1996 | **21 CFR Part 820 revision (QSR)** | Major revision: added design controls; aligned with ISO 13485:1996 concepts |
| 1997 | QSR effective date | All device manufacturers must comply |
| 2003 | ISO 13485:2003 | International QMS standard diverges from QSR in some areas |
| 2016 | ISO 13485:2016 | Major revision; process approach; risk throughout lifecycle |
| 2022 | FDA QMSR proposed rule (NPRM) | Proposed incorporating ISO 13485:2016 by reference |
| 2024 | **QMSR Final Rule published** | 21 CFR 820 revised to incorporate ISO 13485:2016 by reference; effective Feb 2, 2026 |
| 2026 | QMSR compliance date | Manufacturers must comply with new QMSR (ISO 13485-based) |

### 1.2 QSR to QMSR Transition

```mermaid
graph LR
    subgraph "Before 2026"
        QSR[21 CFR Part 820<br/>Quality System Regulation<br/>━━━━━━━━━━━━━<br/>US-specific requirements<br/>Design controls (820.30)<br/>CAPA (820.90)<br/>Production controls (820.70)]
    end
    
    subgraph "After Feb 2, 2026"
        QMSR[21 CFR Part 820<br/>QMSR<br/>━━━━━━━━━━━━━<br/>ISO 13485:2016 incorporated<br/>by reference<br/>+ FDA-specific additions<br/>(complaints, MDR, corrections)]
    end
    
    subgraph "Key Changes"
        CHG1[ISO 13485:2016 = primary QMS]
        CHG2[FDA additions for:<br/>• Complaint files (820.198)<br/>• MDR reporting<br/>• Corrections & removals<br/>• Unique device ID]
        CHG3[Eliminated:<br/>• Redundant requirements<br/>• US-only terminology<br/>• Duplicated ISO 13485 text]
    end
    
    QSR -->|"Transition"| QMSR
    QMSR --> CHG1
    QMSR --> CHG2
    QMSR --> CHG3
```

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 21 CFR Part 820 (Current QSR) Structure

| Subpart | Title | Key Sections |
|:-------:|-------|-------------|
| A | General Provisions | 820.1 (Scope); 820.3 (Definitions); 820.5 (Quality system) |
| B | Quality System Requirements | 820.20 (Management responsibility); 820.22 (Quality audit); 820.25 (Personnel) |
| C | **Design Controls** | 820.30 (Design controls — planning, inputs, outputs, review, verification, validation, transfer, changes) |
| D | Document Controls | 820.40 (Document controls) |
| E | Purchasing Controls | 820.50 (Purchasing controls) |
| F | Identification and Traceability | 820.60 (Identification); 820.65 (Traceability) |
| G | Production and Process Controls | 820.70 (Production controls); 820.72 (Inspection/test equipment); 820.75 (Process validation) |
| H | Acceptance Activities | 820.80 (Receiving, in-process, finished device acceptance); 820.86 (Acceptance status) |
| I | Nonconforming Product | 820.90 (Nonconforming product) |
| J | **Corrective and Preventive Action** | 820.90 (CAPA) |
| K | Labeling and Packaging Controls | 820.120 (Device labeling); 820.130 (Device packaging) |
| L | Handling, Storage, Distribution, Installation | 820.140-820.170 |
| M | Records | 820.180 (General records); 820.184 (DHF); 820.186 (QSR); 820.198 (Complaint files) |
| N | Servicing | 820.200 (Servicing) |
| O | Statistical Techniques | 820.250 (Statistical techniques) |

### 2.2 Key Record Types (The "Holy Trinity" of Device Records)

| Record | Acronym | Content | Purpose |
|--------|:-------:|---------|---------|
| **Design History File** | DHF | All design control records: plans, inputs, outputs, reviews, V&V, transfer records | Evidence that device was designed per design controls |
| **Device Master Record** | DMR | Specifications, processes, QA procedures, packaging/labeling specs for finished device | "Recipe" to manufacture the device |
| **Device History Record** | DHR | Production records for each lot/batch: dates, quantities, acceptance records, labels used | Evidence that specific units were manufactured per DMR |

### 2.3 QMSR (2024) — New Structure

| New 820 Section | Content | Source |
|:---:|---------|--------|
| 820.1 | Scope and applicability | Revised (references ISO 13485) |
| 820.3 | Definitions | Revised (maps FDA terms to ISO 13485 terms) |
| 820.5 | Quality management system requirements | ISO 13485:2016 (incorporated by reference) |
| 820.35 | Control of records | Retained (FDA-specific records: complaint files, MDR, corrections/removals) |
| 820.45 | Device labeling | Retained (per 21 CFR 801) |
| 820.50 | Unique device identification (UDI) | New addition (per 21 CFR 830) |
| 820.55 | Complaint files | Retained and revised (§ 820.198 content moved here) |
| 820.60 | Reports of corrections and removals | Retained (per 21 CFR 806) |
| 820.65 | Medical device reporting (MDR) | Retained (per 21 CFR 803) |

---

## Chapter 3 — Technical Deep Dive

### 3.1 Design Controls (820.30) — The Waterfall Model

```mermaid
graph TB
    subgraph "Design Controls (21 CFR 820.30)"
        PLAN[Design Planning<br/>820.30(b)<br/>━━━━━━━━━━━<br/>• Development plan<br/>• Organizational interfaces<br/>• Resources<br/>• Design phases & reviews]
        
        INPUT[Design Input<br/>820.30(c)<br/>━━━━━━━━━━━<br/>• User needs<br/>• Intended use<br/>• Performance requirements<br/>• Safety requirements<br/>• Regulatory requirements<br/>• Standards compliance<br/>• Risk management inputs]
        
        OUTPUT[Design Output<br/>820.30(d)<br/>━━━━━━━━━━━<br/>• Specifications (DMR)<br/>• Drawings<br/>• Software code/docs<br/>• Acceptance criteria<br/>• Essential for proper functioning]
        
        REVIEW[Design Review<br/>820.30(e)<br/>━━━━━━━━━━━<br/>• Formal documented review<br/>• Adequate & suitable for requirements<br/>• Independent reviewer required<br/>• Actions identified & followed up]
        
        VERIF[Design Verification<br/>820.30(f)<br/>━━━━━━━━━━━<br/>• Confirm outputs meet inputs<br/>• Testing; inspection; analysis<br/>• Objective evidence<br/>• "Did we build the thing RIGHT?"]
        
        VALID[Design Validation<br/>820.30(g)<br/>━━━━━━━━━━━<br/>• Confirm device meets user needs<br/>• Under actual/simulated use<br/>• Including software validation<br/>• Clinical evaluation if needed<br/>• "Did we build the RIGHT thing?"]
        
        TRANSFER[Design Transfer<br/>820.30(h)<br/>━━━━━━━━━━━<br/>• Design → production<br/>• Manufacturing procedures<br/>• Process validation<br/>• Ensure produced = designed]
        
        CHANGE[Design Changes<br/>820.30(i)<br/>━━━━━━━━━━━<br/>• Document changes<br/>• Review before implementation<br/>• Verify & validate as appropriate<br/>• Approved before implementation]
    end
    
    PLAN --> INPUT --> OUTPUT
    OUTPUT --> REVIEW
    INPUT --> REVIEW
    REVIEW --> VERIF --> VALID --> TRANSFER
    TRANSFER --> CHANGE
    CHANGE -->|"May require<br/>re-verification<br/>re-validation"| VERIF
```

### 3.2 CAPA System (820.90/820.90a)

| CAPA Element | Requirement | Implementation |
|-------------|-------------|----------------|
| **Problem identification** | Analyze processes, operations, audit results, quality records, complaints, service records | Sources: complaints (820.198); nonconforming product (820.90); internal audits; NCRs; process monitoring; returned devices |
| **Investigation** | Investigate the cause of nonconformities (root cause analysis) | 5-Why analysis; Ishikawa diagram; fault tree analysis; statistical analysis |
| **Determine scope** | Identify actions needed to correct and prevent recurrence | Determine if problem is systemic or isolated; impact on other products |
| **Corrective action** | Implement corrective action to eliminate cause of existing nonconformity | Address root cause (not just symptoms); document rationale |
| **Preventive action** | Implement preventive action to prevent potential nonconformity | Proactive risk reduction; trend analysis; before problem occurs |
| **Verify/validate effectiveness** | Verify or validate that the corrective/preventive action is effective and does not adversely affect the device | Effectiveness check after implementation (define criteria; timeline) |
| **Communicate to management** | Disseminate CAPA information to management review | Regular CAPA status reporting; trends; resource needs |
| **Submit to regulatory (if needed)** | CAPA may trigger regulatory actions | 30-day MDR supplement; recall/correction; 510(k) for design change |

### 3.3 Process Validation (820.75)

| Requirement | Detail |
|-------------|--------|
| When required | When results of a process cannot be fully verified by subsequent inspection and test (e.g., sterilization, welding, molding, software compilation) |
| IQ (Installation Qualification) | Equipment installed correctly; utilities connected; environment controlled |
| OQ (Operational Qualification) | Process operates within specified parameters; challenge with worst-case conditions |
| PQ (Performance Qualification) | Process consistently produces product meeting specifications under actual production conditions |
| Monitoring | After validation: process parameters monitored (SPC); revalidation triggers defined |
| Revalidation triggers | Process change; equipment change; material change; move; adverse trend; product change |

### 3.4 Complaint Handling (820.198)

| Step | Requirement | Implementation |
|------|-------------|----------------|
| Receive | Accept all complaints (written and oral) | Complaint intake form; CRM system; 24/7 availability |
| Evaluate | Determine if complaint represents a failure to meet specifications | Formal evaluation criteria; documented decision |
| MDR determination | Evaluate if complaint is MDR-reportable (death, serious injury, malfunction that could cause harm) | MDR decision tree; trained complaint handlers; regulatory affairs review |
| Investigate | Formal investigation of complaint (may require returned device analysis) | Root cause analysis; failure analysis lab; engineering assessment |
| CAPA link | Determine if CAPA is warranted based on investigation findings | Trend analysis; severity assessment; risk-based decision |
| Record | Document complaint investigation and outcome | Complaint record: date received, device identification, nature of complaint, investigation, action taken |
| Close | Close when investigation complete and actions implemented | Approval by quality; effectiveness verification if CAPA initiated |

### 3.5 FDA Inspection — Form 483 Common Observations

| Rank | 483 Citation | QSR Section | Common Finding |
|:----:|-------------|:-----------:|----------------|
| 1 | CAPA procedures inadequate | 820.90 | Root cause not determined; effectiveness not verified; CAPAs not completed timely |
| 2 | Complaint handling inadequate | 820.198 | Complaints not investigated; MDR decisions not documented; trends not analyzed |
| 3 | Design validation inadequate | 820.30(g) | Not performed under actual use conditions; software not validated; risk-based approach missing |
| 4 | Process validation inadequate | 820.75 | Sterilization not validated; manufacturing processes lack IQ/OQ/PQ; no revalidation |
| 5 | Production and process controls | 820.70 | Process instructions not followed; environmental monitoring gaps; equipment maintenance |
| 6 | Nonconforming product controls | 820.90 | Disposition not documented; rework without procedure; no impact assessment |
| 7 | Document controls | 820.40 | Obsolete documents in use; unapproved changes; inadequate review/approval |
| 8 | Management responsibility | 820.20 | Quality policy not established; management review missing; resources inadequate |
| 9 | Purchasing controls | 820.50 | Suppliers not evaluated; requirements not specified; incoming inspection gaps |
| 10 | Design controls (general) | 820.30 | No design plan; inputs incomplete; traceability missing |

---

## Chapter 4 — Implementation Guide

### 4.1 QMS Implementation Roadmap

```mermaid
graph TB
    subgraph "Phase 1: Foundation (Months 1-3)"
        P1A[Gap Assessment<br/>• Current state vs. 820/ISO 13485<br/>• Risk-rank gaps<br/>• Prioritize actions]
        P1B[Management Commitment<br/>• Quality policy<br/>• Quality objectives<br/>• Resource allocation<br/>• Appoint QA management]
        P1C[Document Framework<br/>• Quality manual<br/>• Procedure structure (SOPs)<br/>• Work instructions<br/>• Forms and templates]
    end
    
    subgraph "Phase 2: Core Processes (Months 3-8)"
        P2A[Document Control<br/>• DMS implementation<br/>• Review/approval workflow<br/>• Change control<br/>• Distribution]
        P2B[Design Controls<br/>• Design SOP<br/>• Templates (DHF structure)<br/>• Design review process<br/>• V&V procedures]
        P2C[CAPA System<br/>• CAPA SOP<br/>• Root cause methods<br/>• Effectiveness criteria<br/>• Trending/escalation]
        P2D[Complaint Handling<br/>• Complaint SOP<br/>• MDR evaluation<br/>• Investigation process<br/>• Trending]
    end
    
    subgraph "Phase 3: Production Controls (Months 6-10)"
        P3A[Production Controls<br/>• Process SOPs<br/>• Work instructions<br/>• Environmental monitoring<br/>• Equipment maintenance]
        P3B[Process Validation<br/>• Validation master plan<br/>• IQ/OQ/PQ protocols<br/>• Revalidation criteria]
        P3C[Purchasing & Supplier<br/>• Supplier qualification<br/>• ASL (Approved Supplier List)<br/>• Incoming inspection<br/>• Supplier monitoring]
    end
    
    subgraph "Phase 4: Maturity (Months 8-12)"
        P4A[Internal Audit Program<br/>• Audit schedule<br/>• Trained auditors<br/>• Finding management<br/>• CAPA integration]
        P4B[Management Review<br/>• Review inputs defined<br/>• Regular cadence<br/>• Action tracking<br/>• Continuous improvement]
        P4C[Metrics & KPIs<br/>• Quality objectives measured<br/>• CAPA timeliness<br/>• Complaint trends<br/>• Process capability]
    end
    
    P1A --> P1B --> P1C
    P1C --> P2A & P2B & P2C & P2D
    P2A & P2B --> P3A & P3B & P3C
    P3A & P3B & P3C --> P4A & P4B & P4C
```

### 4.2 Design Control Implementation Details

| Design Phase | Required Records | FDA Expectations |
|-------------|-----------------|-----------------|
| **Planning** | Design & Development Plan; team composition; phase gates; deliverables per phase | Living document; updated as design evolves; identifies all organizational interfaces |
| **Inputs** | Design Input Document (or SRS); user needs; intended use/indications; performance specs; safety requirements; regulatory requirements | Complete, unambiguous, non-contradictory; approved; include applicable standards |
| **Outputs** | Design Output Specifications; drawings; software architecture; BOM; acceptance criteria | Expressed in terms verifiable against inputs; identify critical dimensions; reference acceptance criteria |
| **Review** | Design Review meeting minutes; attendees; action items; decisions | At defined phases (not just at end); includes independent reviewer not responsible for design; actions tracked to closure |
| **Verification** | Verification test protocols; test reports; analysis reports; inspection reports | Objective evidence outputs meet inputs; covers all design inputs; uses valid test methods |
| **Validation** | Validation protocol; clinical data; usability study; simulated/actual use testing | Under actual or simulated use conditions; includes software validation; risk-based; may include clinical evaluation |
| **Transfer** | Transfer checklist; process validation records; first article inspection; manufacturing procedures | Ensures design correctly translated to production; procedures adequate; staff trained |
| **Changes** | Design Change Request; impact assessment; V&V determination; approval | Before implementation; evaluate effect on function, performance, safety; new regulatory submission determination |

### 4.3 QMSR Transition Checklist (by Feb 2026)

| Area | Action | Priority |
|------|--------|:--------:|
| Quality Manual | Update to reference ISO 13485:2016 structure and terminology | High |
| Process approach | Ensure all processes have defined inputs, outputs, interactions, metrics | High |
| Risk management | Integrate risk into ALL QMS processes (not just design) — ISO 13485 requires "risk-based approach" | High |
| Terminology alignment | Map FDA terms (DHF/DMR/DHR) to ISO 13485 terms (design/development files) | Medium |
| Supplier controls | Align with ISO 13485 Clause 7.4 (may need more detailed supplier monitoring) | Medium |
| Infrastructure/work environment | Document infrastructure and work environment requirements per ISO 13485 Clause 6 | Medium |
| Complaint files | Retain FDA-specific complaint requirements (820.55 in QMSR) | High |
| MDR procedures | Ensure MDR evaluation procedures reference new section (820.65) | High |
| Training | Train all staff on QMSR changes; update quality training program | High |
| Internal audits | Update audit checklists to ISO 13485 + QMSR-specific requirements | Medium |
| Management review | Align inputs/outputs with ISO 13485 Clause 5.6 requirements | Medium |

---

## Chapter 5 — Audits & Inspections

### 5.1 FDA Inspection Types

| Type | Trigger | Scope | Duration |
|------|---------|-------|:--------:|
| **Pre-market (PMA)** | PMA submission | Manufacturing facility; design controls; quality system for specific device | 1-2 weeks |
| **Surveillance (routine)** | Scheduled (every 2-4 years for Class II/III) | Full QSR compliance; selected subsystems | 3-5 days |
| **For-cause** | Complaint; MDR; recall; whistleblower | Specific issue; may expand to full system | Variable |
| **Compliance follow-up** | Previous 483/Warning Letter | Verify corrections implemented; no repeat findings | 2-3 days |
| **QSIT** | Routine (Quality System Inspection Technique) | Four major subsystems (CAPA, design, production, management) | 3-5 days |

### 5.2 QSIT Subsystem Focus

| Subsystem | Key Areas Inspected | Common Findings |
|-----------|--------------------|--------------------|
| **CAPA** | Sources of quality data; problem analysis; root cause; corrective actions; verification of effectiveness; management communication | Ineffective root cause analysis; no effectiveness verification; overdue CAPAs |
| **Design Controls** | Design plan; inputs; outputs; reviews; verification; validation; transfer; changes | Incomplete inputs; validation not under use conditions; change control gaps |
| **Production & Process Controls** | Process validation; process monitoring; equipment qualification; environmental controls | Unvalidated processes; expired calibration; inadequate cleaning validation |
| **Management Controls** | Quality policy; management review; internal audits; quality objectives; resource allocation | Missing management review; no quality objectives; inadequate audit program |

### 5.3 Responding to FDA 483

| Step | Timeline | Action |
|------|:--------:|--------|
| 1 | During inspection | Ask for clarification; provide objective evidence; do not argue with inspector |
| 2 | Within 15 business days | Submit written response to FDA district office |
| 3 | Response content | For each observation: acknowledge or explain; describe root cause; detail corrective action (with timeline); describe preventive action; attach evidence of completion (if already done) |
| 4 | Follow-up | Implement all committed actions by stated dates; maintain evidence; prepare for follow-up inspection |
| 5 | Escalation prevention | Prompt 483 response prevents escalation to Warning Letter; complete actions prevent consent decree |

### 5.4 Enforcement Ladder

```
FDA 483 Observation → Untitled Letter → WARNING LETTER → Consent Decree → Seizure/Injunction → Criminal Prosecution
                                                         ↓
                                                    Import Alert (for foreign manufacturers)
```

---

## Chapter 6 — Regional Context

### 6.1 21 CFR 820 vs. International QMS Requirements

| Requirement | FDA QSR/QMSR | ISO 13485 (EU/International) | MDSAP |
|-------------|:---:|:---:|:---:|
| Quality Management System | ✅ (820.5 → ISO 13485) | ✅ | ✅ (all 5 countries) |
| Design Controls | ✅ (820.30 → ISO 13485 Clause 7.3) | ✅ (Clause 7.3) | ✅ |
| Risk Management | ✅ (referenced; ISO 14971) | ✅ (ISO 14971 required) | ✅ |
| CAPA | ✅ (820.90 → ISO 13485 Clause 8.5.2/8.5.3) | ✅ (Clause 8.5.2/8.5.3) | ✅ |
| Complaint Handling | ✅ (820.198/820.55 — specific) | ✅ (Clause 8.2.2) | ✅ |
| Post-Market Surveillance | Limited (complaints + MDR) | ✅ (EU MDR requires PMS plan, PSUR, PMCF) | ✅ (varies by country) |
| Clinical Evaluation | Not in QSR (separate: clinical trials) | ✅ (required by MDR for CE marking) | Varies |
| Medical Device Reporting | ✅ (21 CFR 803 — mandatory) | ✅ (Vigilance per MDR Article 87-92) | ✅ (country-specific) |
| UDI | ✅ (21 CFR 830; GUDID) | ✅ (EU MDR Article 27; EUDAMED) | Referenced |

### 6.2 MDSAP (Medical Device Single Audit Program)

| Aspect | Detail |
|--------|--------|
| What | Single audit covering requirements of 5 regulatory authorities simultaneously |
| Participating countries | US (FDA), Canada (Health Canada), Brazil (ANVISA), Australia (TGA), Japan (PMDA) |
| Benefit | One audit satisfies multiple regulators; reduces audit burden; recognized by FDA as alternative to routine inspection |
| Auditing organizations | BSI, TÜV, SGS, Intertek, UL (MDSAP-recognized) |
| Structure | 7 audit tasks aligned to device lifecycle (Management, Device Marketing Authorization, Measurement/Monitoring, Design, Purchasing, Production, CAPA) |
| FDA recognition | FDA accepts MDSAP audit reports; may reduce frequency of FDA direct inspections |

---

## Chapter 7 — Comparison

### 7.1 QSR (Current) vs. QMSR (2026)

| Dimension | QSR (21 CFR 820 current) | QMSR (21 CFR 820 revised) |
|-----------|:---:|:---:|
| Primary basis | FDA-authored requirements | ISO 13485:2016 incorporated by reference |
| Structure | 14 subparts (A-O) | ISO 13485 clauses + FDA-specific sections |
| Terminology | DHF, DMR, DHR (FDA terms) | ISO 13485 terms (design/development file) + FDA terms retained for some concepts |
| Risk approach | Implied in some sections | Explicit throughout (ISO 13485 Clause 7.1) |
| Process approach | Not explicitly required | Required (ISO 13485 Clause 4.1) |
| Complaint files | 820.198 (detailed FDA requirements) | 820.55 (retained with updates) |
| MDR | Separate regulation (21 CFR 803) | Referenced in 820.65 |
| Global harmonization | US-specific language | Harmonized with international standard |
| Transition period | — | Feb 2, 2026 compliance date |

### 7.2 FDA Design Controls vs. ISO 13485 Design and Development

| Activity | FDA 820.30 | ISO 13485 Clause 7.3 |
|----------|:---:|:---:|
| Planning | 820.30(b) | 7.3.2 |
| Inputs | 820.30(c) | 7.3.3 |
| Outputs | 820.30(d) | 7.3.4 |
| Review | 820.30(e) | 7.3.5 |
| Verification | 820.30(f) | 7.3.6 |
| Validation | 820.30(g) | 7.3.7 |
| Transfer | 820.30(h) | 7.3.8 (Transfer of design and development) |
| Changes | 820.30(i) | 7.3.9 |
| Design & development files | DHF concept (820.184) | 7.3.10 (Design and development files) |

**Key differences:**  
- FDA explicitly requires design outputs to include "acceptance criteria" and identify features "essential for proper functioning" — ISO 13485 is less prescriptive  
- FDA validation must be under "actual or simulated use conditions" — ISO 13485 similar but less explicit  
- ISO 13485 Clause 7.3.10 explicitly requires design and development FILE — QSR had this as DHF (820.184)

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 FDA Quality System Elements

```mermaid
graph TB
    subgraph "Management Responsibility (820.20)"
        QP[Quality Policy & Objectives]
        MR[Management Review]
        ORG[Organization & Resources]
    end
    
    subgraph "Design Controls (820.30)"
        DC[Design Planning → Input → Output<br/>→ Review → Verification<br/>→ Validation → Transfer → Changes]
    end
    
    subgraph "Production Controls"
        PC[Production & Process Controls (820.70)]
        PV[Process Validation (820.75)]
        PURCH[Purchasing Controls (820.50)]
        AA[Acceptance Activities (820.80)]
    end
    
    subgraph "Corrective System"
        NCP[Nonconforming Product (820.90)]
        CAPA[CAPA (820.90)]
        COMP[Complaints (820.198)]
        MDR_D[MDR (21 CFR 803)]
    end
    
    subgraph "Support Systems"
        DOC[Document Controls (820.40)]
        REC[Records (820.180)]
        TRACE[Traceability (820.65)]
        STAT[Statistical Techniques (820.250)]
    end
    
    QP --> DC & PC
    DC --> PC
    PC --> AA --> NCP
    NCP --> CAPA
    COMP --> CAPA
    COMP --> MDR_D
    CAPA --> DC
    DOC --> DC & PC & CAPA
    MR --> QP & CAPA
```

### 8.2 CAPA Process Flow

```mermaid
graph TB
    subgraph "Sources"
        S1[Complaints<br/>820.198]
        S2[Nonconforming<br/>Product 820.90]
        S3[Internal Audits<br/>820.22]
        S4[Process Data<br/>SPC/Trends]
        S5[Service Records<br/>820.200]
        S6[MDRs/Recalls<br/>21 CFR 803/806]
    end
    
    subgraph "CAPA Process (820.90)"
        OPEN[Open CAPA<br/>• Define problem<br/>• Assign owner<br/>• Set timeline]
        
        INVEST[Investigation<br/>• Data collection<br/>• Root cause analysis<br/>• Scope determination<br/>• Product impact assessment]
        
        ACTION[Action Plan<br/>• Corrective action (fix cause)<br/>• Preventive action (prevent recurrence)<br/>• Risk assessment of action<br/>• Verify no adverse effect]
        
        IMPL[Implementation<br/>• Execute actions<br/>• Document changes<br/>• Train personnel<br/>• Update procedures/specs]
        
        EFFECT[Effectiveness Verification<br/>• Define success criteria<br/>• Monitor for defined period<br/>• Verify recurrence eliminated<br/>• Statistical evidence]
        
        CLOSE[Close CAPA<br/>• Approved by Quality<br/>• Records complete<br/>• Communicated to MR]
    end
    
    S1 & S2 & S3 & S4 & S5 & S6 --> OPEN
    OPEN --> INVEST --> ACTION --> IMPL --> EFFECT --> CLOSE
    
    EFFECT -->|"Not effective"| INVEST
```

### 8.3 Complaint to MDR Decision Flow

```mermaid
graph TB
    RECV[Complaint Received<br/>• Verbal or written<br/>• Any source<br/>• Document immediately]
    
    EVAL{Is it a complaint?<br/>(Allegation that device<br/>failed to meet specs<br/>or caused/could cause harm)}
    
    RECV --> EVAL
    
    EVAL -->|"No (inquiry/feedback)"| LOG_NQ[Log as non-complaint<br/>Close or route to<br/>appropriate department]
    
    EVAL -->|"Yes"| MDR_Q{MDR Reportable?<br/>Did device<br/>malfunction/cause:<br/>• Death?<br/>• Serious injury?<br/>• Could cause death/injury<br/>if malfunction recurred?}
    
    MDR_Q -->|"Yes - Death"| MDR_DEATH[MDR Report<br/>within 30 days<br/>(5 days if awareness<br/>from MDR source)]
    
    MDR_Q -->|"Yes - Serious Injury"| MDR_SI[MDR Report<br/>within 30 days]
    
    MDR_Q -->|"Yes - Malfunction<br/>(could cause harm)"| MDR_MAL[MDR Report<br/>within 30 days]
    
    MDR_Q -->|"No"| INVEST_C[Investigate per<br/>complaint SOP<br/>• Root cause<br/>• CAPA determination<br/>• Trending]
    
    MDR_DEATH & MDR_SI & MDR_MAL --> INVEST_C
    
    INVEST_C --> CAPA_Q{CAPA<br/>Warranted?}
    CAPA_Q -->|"Yes"| OPEN_CAPA[Open CAPA]
    CAPA_Q -->|"No"| CLOSE_C[Close Complaint<br/>Document rationale]
```

---

## Chapter 9 — Case Studies & Failure Analysis

### 9.1 Case Study: Warning Letter — CAPA System Failure

| Aspect | Detail |
|--------|--------|
| Company | Mid-size Class II device manufacturer (surgical instruments) |
| FDA Finding | Warning Letter with 7 observations; primary issue: CAPA system ineffective |
| Specific citations | (1) 34 CAPAs open > 12 months without resolution. (2) Root cause documented as "human error" for 80% of CAPAs without further investigation. (3) Effectiveness checks defined as "no recurrence in 30 days" — inadequate for low-frequency events. (4) Preventive actions not initiated despite clear adverse trends in complaint data. (5) Management review did not include CAPA status or trends. |
| Root causes (company's own analysis) | (1) CAPA SOP lacked timelines and escalation procedures. (2) Personnel untrained in root cause analysis methods. (3) No CAPA metrics tracked; no overdue notification system. (4) Quality team understaffed (2 QA engineers for 500-person manufacturing). (5) Management not engaged in quality system oversight. |
| Corrective actions | (1) Hired Quality Director with CAPA expertise; added 3 QA engineers. (2) Implemented electronic CAPA management (Trackwise); automated overdue notifications. (3) Trained all investigators in formal root cause methods (8D, Ishikawa, 5-Why; minimum 16 hours training). (4) Defined CAPA timeliness targets: investigation complete 30 days; action plan 45 days; implementation 90 days; effectiveness 180 days. (5) Implemented weekly CAPA review meetings with management. (6) Re-investigated all 34 overdue CAPAs with proper root cause analysis. (7) Implemented statistical complaint trending (weekly Pareto charts; automatic CAPA trigger when trend identified). |
| Outcome | Follow-up inspection 8 months later: all 7 observations closed. No repeat findings. Warning Letter closed. |

### 9.2 Case Study: Design Control Failure — Recall

| Aspect | Detail |
|--------|--------|
| Device | Connected insulin pen with companion mobile app (Class II) |
| Event | Field issue: app displayed incorrect dose remaining, causing patients to under-dose or over-dose; 12 serious adverse events reported |
| Design control gaps discovered | (1) **Design Inputs** incomplete: app requirements did not address low-battery Bluetooth scenarios; no requirement for dose data integrity verification. (2) **Design Verification** missed edge case: BLE disconnect during dose recording not tested; only "happy path" tested. (3) **Design Validation** inadequate: validation performed only with healthy volunteers in controlled setting; did not simulate real-world use (intermittent connectivity, phone in pocket, multiple BLE devices nearby). (4) **Design Review** gap: no cybersecurity/connectivity expert in design reviews; software team reviewed software; hardware team reviewed hardware; no cross-functional review. (5) **Risk management** gap: FMEA for the app did not include "data transmission failure during dose logging" as a failure mode. |
| Corrective actions | (1) **Class I recall** (correction): app update deployed that validates dose data end-to-end (checksum); displays warning if dose data integrity cannot be confirmed. (2) **Design input update**: added 47 requirements for connectivity edge cases, data integrity, and graceful degradation. (3) **Verification**: added 200+ test cases for BLE failure scenarios (disconnect during transfer, partial transfer, corrupted data, reconnect timing). (4) **Validation**: repeated validation with actual patients (3 months, 150 patients, real-world conditions including urban RF-noisy environments). (5) **CAPA**: implemented cross-functional design review requirement (all disciplines in every review); updated FMEA methodology to include connectivity/cyber failure modes. |
| Regulatory impact | FDA 510(k) amendment required for software change; MDR reports filed; FDA inspection triggered by recall |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact on QSR/QMSR |
|-------|----------|---------------------|
| QMSR effective date | Feb 2, 2026 | ISO 13485 becomes the QMS standard for US; harmonization with global requirements |
| ISO 13485 revision (next edition) | 2026-2028 | Will affect QMSR when adopted; process approach refinement; digital QMS |
| AI/ML medical devices (PCCP) | Now-2025+ | Predetermined Change Control Plan; continuous learning; new design control paradigm |
| Digital health & SaMD growth | Accelerating | QMS for pure-software companies; agile; CI/CD; cloud deployment |
| Cybersecurity pre-market requirements | 2023+ (FDA mandate) | SBOM in design outputs; cybersecurity as design input; vulnerability management in maintenance |
| Real-world evidence (RWE) | Now | Complements clinical validation; post-market data feeding design improvements |
| Additive manufacturing | Now | New process validation challenges; unique device manufacturing; material qualification |
| Supply chain resilience | Post-COVID | Enhanced supplier controls; dual-sourcing requirements; supply chain risk management |
| Electronic QMS (eQMS) | Now | Paper → electronic; electronic signatures (21 CFR Part 11); automated workflows |
| Regulatory convergence (IMDRF) | Ongoing | Global harmonization of QMS requirements; single audit programs (MDSAP expansion) |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What are design controls and why does FDA require them?  
**A:** Design controls (21 CFR 820.30) are a set of practices and procedures that ensure a medical device is properly designed, meets user needs, and performs as intended. FDA requires them because the Therac-25 accidents, numerous recalls, and pre-1996 experience showed that manufacturing controls alone were insufficient — many device failures originated in **design**, not manufacturing. Design controls require: (1) **Planning** — documented development plan with phases and reviews. (2) **Inputs** — complete requirements (user needs, intended use, safety, performance, standards). (3) **Outputs** — specifications that can be verified against inputs. (4) **Reviews** — formal reviews at key stages with independent participation. (5) **Verification** — confirm outputs meet inputs ("built the thing right" — tests, inspections, analyses). (6) **Validation** — confirm device meets user needs under actual/simulated use ("built the right thing"). (7) **Transfer** — ensuring manufacturing can reproduce the design. (8) **Change control** — managing design changes with appropriate re-verification/validation. Design controls apply to all Class II and III devices, and to some Class I devices (e.g., software). They are the #3 most-cited 483 observation area.

**Q2:** What is the difference between verification and validation?  
**A:** **Verification** = "Did we build the thing RIGHT?" — Confirmation through objective evidence that specified requirements (design inputs) have been fulfilled by the design outputs. This is typically testing AGAINST SPECIFICATIONS: bench testing, analysis, inspection. Example: "The thermometer shall measure temperature ±0.1°C between 35-42°C" → verification test measures accuracy against reference standard. **Validation** = "Did we build the RIGHT thing?" — Confirmation through objective evidence that the device meets user needs and intended uses. This is testing UNDER USE CONDITIONS (actual or simulated). Example: same thermometer validated by having nurses use it on patients in a clinical setting, demonstrating it gives correct readings in real conditions (movement, sweat, varying ambient temperature, user technique variation). Key distinction: verification can often be done in the lab; validation requires use conditions. Both produce objective evidence (data, records). Software validation includes testing the software under conditions that simulate real use (with realistic data, realistic workflows, realistic environment).

### Tier 2: Mid-Level

**Q3:** Your company received a Warning Letter citing inadequate CAPA procedures. How would you remediate and prevent recurrence?  
**A:** Immediate actions: (1) Acknowledge receipt and respond within 15 business days with a detailed corrective action plan for each observation. (2) Hire external regulatory/quality consultant to provide independent assessment. Systemic remediation: (3) **CAPA procedure overhaul**: rewrite CAPA SOP with clear root cause analysis methodology (mandate formal methods: 8D, Ishikawa, 5-Why; prohibit "human error" as sole root cause); define timelines with automatic escalation; define effectiveness verification criteria (metric-based, time-bound, appropriate to failure frequency). (4) **CAPA system infrastructure**: implement eQMS (MasterControl/Greenlight Guru/ETQ) with automated workflows, overdue notifications, dashboards. (5) **Training**: train all CAPA owners in root cause analysis (16+ hours); train management on their QMS responsibilities; train complaint handlers on trend identification. (6) **Data inputs**: implement statistical trending of complaints, NCRs, process data; automatic CAPA trigger when adverse trend identified; Pareto analysis monthly. (7) **Management oversight**: mandatory monthly CAPA review at leadership level; CAPA metrics as quality objectives; CAPA closure rate as KPI. (8) **Effectiveness monitoring**: each CAPA must have measurable success criteria BEFORE implementation; effectiveness check performed at defined interval (typically 3-6 months); if not effective, re-investigation required (new CAPA or escalation). (9) **Internal audit focus**: next 3 audit cycles include CAPA system deep-dive. (10) **Mock inspection**: hire former FDA investigator to conduct mock inspection before follow-up. Prevention: (11) Establish leading indicators (trend analysis, risk scores) not just lagging indicators (complaints, NCRs).

### Tier 3: Senior

**Q4:** How would you manage the transition from current QSR to QMSR (ISO 13485 incorporation) for a multi-site company with 50+ devices?  
**A:** Strategic approach: (1) **Gap analysis at system level**: compare current QMS (820-based) against ISO 13485:2016 clause-by-clause; identify gaps (typically: process approach not formalized; risk not integrated into all processes; infrastructure/work environment not explicitly documented; some ISO 13485 requirements more detailed than QSR in areas like competency management). (2) **Prioritized transition plan**: focus on HIGH-RISK gaps first (process approach implementation; risk integration); then MEDIUM (terminology alignment; new document structure); then LOW (cosmetic changes). (3) **Architecture decision**: harmonized QMS covering both old QSR and new QMSR requirements simultaneously (important because some sites/products may transition at different times). Likely approach: restructure quality manual to ISO 13485 clause structure NOW; map existing SOPs; identify which SOPs need revision vs. only re-numbering. (4) **Documentation strategy**: for 50+ devices, bulk updates to DHFs are impractical. Approach: update QMS-level documents (quality manual, SOPs, templates) to QMSR/ISO 13485; existing DHFs remain valid (no retroactive requirement); new design projects use new templates; major changes to existing devices use new templates at point of change. (5) **Multi-site challenges**: different sites may have different maturity levels; CAPA system, document control, and training system should be unified; local work instructions can remain site-specific. (6) **Regulatory notifications**: no new submission required for QMS transition (it's a manufacturing change, not a design change); update quality agreements with contract manufacturers/suppliers; notify Notified Body (if EU) of QMS restructuring. (7) **Training cascade**: train QA/RA staff first (detailed ISO 13485 training); then train management (management responsibility changes); then all employees (awareness of terminology/process changes). (8) **Timeline**: start immediately (2+ year runway to Feb 2026 deadline); aim for full implementation 6 months before deadline to allow shake-out period and internal auditing against new requirements.

---

## Chapter 12 — Cheat Sheet & Quick Reference

### Design Controls Quick Reference

```
PLANNING → Who, what, when, how; organizational interfaces
INPUTS   → User needs + intended use + performance + safety + standards + risk
OUTPUTS  → Specifications + drawings + BOM + acceptance criteria (verifiable against inputs)
REVIEW   → Formal; documented; independent reviewer; at defined phases; actions tracked
VERIFY   → "Built it RIGHT?" — outputs meet inputs (lab testing/analysis/inspection)
VALIDATE → "Built the RIGHT thing?" — meets user needs under actual/simulated use
TRANSFER → Design → production (process validation; procedures; training)
CHANGES  → Document; review; verify/validate as needed; approve before implementation
```

### Record Types

```
DHF (Design History File)    = Evidence of design process (all design records)
DMR (Device Master Record)   = Recipe to build the device (specs, procedures, BOM)
DHR (Device History Record)  = Evidence specific units were built per DMR (lot records)
QSR (Quality System Record)  = QMS procedures, audits, management review, CAPA records
Complaint File               = All complaint records per 820.198/820.55
```

### CAPA Quick Reference

```
SOURCES: Complaints | NCRs | Audits | Process data | MDRs | Service | Trends
INVESTIGATE: Collect data → Root cause (NOT "human error" alone) → Scope determination
ACT: Correct (fix current problem) → Prevent (systemic fix for recurrence)
VERIFY: Define criteria BEFORE implementing → Monitor → Prove effectiveness
COMMUNICATE: Management review → Regulatory (if needed) → Close
```

### FDA 483 Response Checklist

```
□ Respond within 15 business days
□ Address EACH observation individually
□ For each:
  □ Acknowledge the finding (or explain with evidence)
  □ State root cause (or interim containment if still investigating)
  □ Describe corrective action with specific timeline
  □ Describe preventive action (systemic fix)
  □ Attach evidence of completion (if already done)
  □ Name responsible person
□ Do NOT promise what you can't deliver
□ Do NOT argue that FDA is wrong (provide evidence if you disagree)
□ Track all commitments to completion
□ Prepare for follow-up inspection
```

### MDR Reporting Timelines

```
DEATH:            30 calendar days (5 days if from MDR reportable source AND remedial action needed)
SERIOUS INJURY:   30 calendar days
MALFUNCTION:      30 calendar days (if malfunction could cause death/serious injury if recurred)

Report to: FDA MedWatch (eMDR electronic submission)
Who reports: Manufacturer (mandatory); User facility (death/serious injury); Importer (mandatory)
```

### QMSR Transition Quick Facts

```
Effective: February 2, 2026
What changes: ISO 13485:2016 incorporated by reference as primary QMS requirement
What stays: Complaint files (820.55); MDR (820.65); Corrections/Removals (820.60)
What's new: UDI requirements (820.50); explicit process approach; risk integrated throughout
Action: Gap analysis → Plan → Update QMS → Train → Audit → Comply by deadline
```

---

*End of Document — 04_FDA_21_CFR_820_QSR.md*
