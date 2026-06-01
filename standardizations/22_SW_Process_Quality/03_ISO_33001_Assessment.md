# ISO/IEC 33001-33020 — Process Assessment Framework

**Standard:** ISO/IEC 33001:2015 (Concepts & Terminology), ISO/IEC 33002:2015 (Requirements for Assessment), ISO/IEC 33003:2015 (Requirements for Measurement Frameworks), ISO/IEC 33004:2015 (Requirements for PRMs/PAMs/MFs), ISO/IEC 33020:2019 (Process Measurement Framework)  
**Replaces:** ISO/IEC 15504 (SPICE) series  
**SDO:** ISO/IEC JTC 1/SC 7 (Software & Systems Engineering)  
**Audience:** Process assessors, quality managers, PAM developers, ASPICE practitioners, CMMI appraisers transitioning to ISO framework  
**Prerequisites:** Understanding of process concepts; familiarity with ASPICE or CMMI; basic ISO standards knowledge

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Milestone |
|------|-----------|
| 1991 | CMM v1.0 published (SEI) — first widely-used process maturity model |
| 1993 | **SPICE project** begins — international effort to create ISO process assessment standard |
| 1995 | ISO/IEC 15504 development starts (based on SPICE project findings) |
| 1998 | ISO/IEC 15504-2:1998 (first normative part — assessment framework) |
| 2003 | ISO/IEC 15504-2:2003 (revised; widely adopted; basis for Automotive SPICE) |
| 2004 | ISO/IEC 15504-5 (exemplar PAM for software lifecycle processes) |
| 2006 | ISO/IEC 15504-5:2006 (updated; used as reference by ASPICE 2.x) |
| 2008 | Decision to restructure ISO 15504 into separate numbered standards (33000 series) |
| 2015 | **ISO/IEC 33001-33004** published (replacement for ISO 15504 core parts) |
| 2017 | ISO/IEC 15504 parts officially withdrawn; ISO 33000 series is authoritative |
| 2019 | **ISO/IEC 33020:2019** (Process measurement framework for capability assessment) |
| 2024 | Automotive SPICE 4.0 fully aligned with ISO 33001 framework |

### 1.2 Why ISO 15504 Was Replaced

| Problem with ISO 15504 | How ISO 33000 Series Addresses It |
|:---:|---|
| Monolithic standard (15504 had many parts mixed together) | Separated into focused standards (33001=concepts; 33002=assessment requirements; 33003=measurement; etc.) |
| Measurement framework was embedded within assessment requirements | 33020 is standalone measurement framework (can be used independently) |
| Requirements for PRMs, PAMs, and MFs were interleaved | 33004 clearly separates requirements for each model type |
| Limited flexibility for domain-specific extensions | Framework explicitly designed for domain-specific PAMs (automotive, medical, etc.) |
| Terminology was inconsistent across parts | 33001 provides single authoritative terminology source |

### 1.3 Relationship: ISO 33000 → ASPICE / CMMI

```mermaid
graph TB
    subgraph "ISO 33000 Framework Hierarchy"
        ISO33001[ISO/IEC 33001<br/>Concepts & Terminology<br/>━━━━━━━━━<br/>Defines: process assessment,<br/>capability, measurement]
        
        ISO33002[ISO/IEC 33002<br/>Requirements for Assessment<br/>━━━━━━━━━<br/>How to conduct assessments;<br/>assessor competence; evidence]
        
        ISO33003[ISO/IEC 33003<br/>Requirements for Measurement<br/>Frameworks<br/>━━━━━━━━━<br/>Rules for defining CL scales]
        
        ISO33004[ISO/IEC 33004<br/>Requirements for PRMs, PAMs, MFs<br/>━━━━━━━━━<br/>What a valid PRM/PAM must contain]
        
        ISO33020[ISO/IEC 33020<br/>Process Measurement Framework<br/>━━━━━━━━━<br/>6-level CL scale; Process Attributes;<br/>Rating scale (N/P/L/F)]
        
        subgraph "Domain-Specific PAMs"
            ASPICE_M[Automotive SPICE 4.0<br/>(PAM for automotive)]
            MED[IEC 62304<br/>(medical device SW)]
            SPACE[ECSS-Q-ST-80C<br/>(space SW)]
        end
    end
    
    ISO33001 --> ISO33002
    ISO33001 --> ISO33003
    ISO33001 --> ISO33004
    ISO33003 --> ISO33020
    ISO33004 --> ASPICE_M
    ISO33004 --> MED
    ISO33004 --> SPACE
    ISO33020 --> ASPICE_M
```

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 ISO 33000 Series Parts

| Standard | Title | Purpose |
|:--------:|:-----:|---------|
| **ISO/IEC 33001:2015** | Concepts and terminology | Foundation: defines terms, concepts, relationships |
| **ISO/IEC 33002:2015** | Requirements for performing process assessment | Rules for conducting valid assessments |
| **ISO/IEC 33003:2015** | Requirements for process measurement frameworks | Rules for constructing measurement scales |
| **ISO/IEC 33004:2015** | Requirements for process reference models, process assessment models, and maturity models | What constitutes a valid PRM/PAM/MM |
| **ISO/IEC 33014:2013** | Guide for process improvement | How to use assessment results for improvement |
| **ISO/IEC 33020:2019** | Process measurement framework for assessment of process capability | The actual 6-level measurement scale (CL 0-5) |
| **ISO/IEC 33063:2015** | Process assessment model for software testing | PAM for ISO/IEC/IEEE 29119 testing processes |

### 2.2 Key Concepts (from 33001)

| Concept | Definition |
|:-------:|------------|
| **Process** | Set of interrelated activities that transform inputs into outputs |
| **Process Reference Model (PRM)** | Model defining processes, their purposes, and outcomes (WHAT to do) |
| **Process Assessment Model (PAM)** | Model mapping assessment indicators to a PRM for assessment purposes |
| **Measurement Framework (MF)** | Scale and rules for measuring process attribute achievement |
| **Process Attribute (PA)** | Measurable characteristic of process capability |
| **Capability Level (CL)** | Point on the ordinal scale representing process capability |
| **Process Assessment** | Disciplined evaluation of processes against a PAM using an MF |
| **Assessment Indicator** | Evidence that supports assessor judgment (practice performance; resource/infrastructure; process management) |

---

## Chapter 3 — Technical Deep Dive

### 3.1 Process Reference Model (PRM) — Requirements (ISO 33004)

A valid PRM must:

| Requirement | Description |
|:-----------:|-------------|
| **Process purpose** | Each process has a stated purpose (WHY it exists) |
| **Process outcomes** | Each process has measurable outcomes (observable results when purpose is achieved) |
| **Process grouping** | Processes organized into logical groups (life cycle categories) |
| **Process relationships** | Relationships between processes described |
| **Scope declaration** | PRM declares its domain and scope of applicability |
| **Conformance** | PRM demonstrates conformance to ISO 33004 requirements |

**Example PRM:** ISO/IEC 12207:2017 (Systems and software engineering — Software life cycle processes) serves as the PRM for many PAMs.

### 3.2 Process Assessment Model (PAM) — Requirements (ISO 33004)

A valid PAM must:

| Requirement | Description |
|:-----------:|-------------|
| **Based on PRM** | PAM must reference a specific PRM (or multiple PRMs) |
| **Assessment indicators** | Define indicators (evidence) for each process and each capability level |
| **Mapping** | Clear mapping between PAM indicators and PRM outcomes |
| **Measurement framework** | PAM uses a specific measurement framework (usually ISO 33020) |
| **Scope** | PAM declares scope (which processes from PRM; which CL levels supported) |

**Example PAM:** Automotive SPICE 4.0 is a PAM based on ISO/IEC 12207 (as PRM) using ISO 33020 (as MF).

### 3.3 Measurement Framework (ISO 33020)

```mermaid
graph TB
    subgraph "ISO 33020 Measurement Framework"
        subgraph "Capability Dimension (6 levels)"
            CL0[CL 0: Incomplete<br/>No process attributes assessed]
            CL1[CL 1: Performed<br/>PA 1.1: Process performance]
            CL2[CL 2: Managed<br/>PA 2.1: Performance management<br/>PA 2.2: Work product management]
            CL3[CL 3: Established<br/>PA 3.1: Process definition<br/>PA 3.2: Process deployment]
            CL4[CL 4: Predictable<br/>PA 4.1: Quantitative analysis<br/>PA 4.2: Quantitative control]
            CL5[CL 5: Innovating<br/>PA 5.1: Process innovation<br/>PA 5.2: Process optimization]
        end
        
        subgraph "Rating Scale"
            N_R[N = Not achieved (0-15%)]
            P_R[P = Partially achieved (16-50%)]
            L_R[L = Largely achieved (51-85%)]
            F_R[F = Fully achieved (86-100%)]
        end
    end
    
    CL0 --> CL1 --> CL2 --> CL3 --> CL4 --> CL5
```

### 3.4 Process Attributes Detailed

| PA | Name | What It Measures |
|:--:|:----:|:---|
| **1.1** | Process Performance | The extent to which the process achieves its defined outcomes; base practices are performed; work products are produced |
| **2.1** | Performance Management | The extent to which the process execution is managed: planned, monitored, adjusted; responsibilities defined; resources allocated |
| **2.2** | Work Product Management | The extent to which work products are appropriately managed: identified, documented, controlled, reviewed, baselined |
| **3.1** | Process Definition | The extent to which a standard process is maintained to support deployment: organizational standard defined; tailoring guidelines exist |
| **3.2** | Process Deployment | The extent to which the standard process is deployed: practitioners use standard process (tailored); data collected; process improvement feedback |
| **4.1** | Quantitative Analysis | The extent to which measurement is used to ensure process meets quality/performance objectives: measurement needs identified; data collected; analyzed; process variation understood |
| **4.2** | Quantitative Control | The extent to which the process is quantitatively managed to achieve objectives: control limits defined; special causes detected; corrective actions taken |
| **5.1** | Process Innovation | The extent to which changes are identified from innovation and process analysis: improvement opportunities identified; innovative practices evaluated; best practices selected |
| **5.2** | Process Optimization | The extent to which changes are systematically managed and impact is validated: improvement implementation planned; impact verified; optimization gains sustained |

---

## Chapter 4 — Assessment Requirements (ISO 33002)

### 4.1 Assessment Process Requirements

| Phase | Requirements |
|:-----:|---|
| **Initiation** | Sponsor identified; purpose defined; scope agreed (processes, organizational unit, CL target); constraints documented; PAM selected; MF selected |
| **Planning** | Assessment plan developed (schedule, resources, team, methods); competent assessor team assembled; evidence collection strategy defined |
| **Briefing** | Assessed organization informed of: purpose, scope, methods, timeline, participant roles, confidentiality |
| **Data Collection** | Systematic gathering of objective evidence: document review, interviews, observation, tool inspection; sufficient evidence for each process attribute |
| **Data Validation** | Evidence corroborated (triangulation: multiple sources); discrepancies resolved; preliminary ratings shared with assessed organization |
| **Process Rating** | Each process attribute rated (N/P/L/F); capability level determined per achievement rules; findings documented |
| **Reporting** | Assessment report produced: scope, approach, ratings, findings, strengths, weaknesses, improvement opportunities |

### 4.2 Assessor Competence Requirements

| Competence Area | Requirement |
|:-:|---|
| **Process assessment knowledge** | Understanding of ISO 33001-33004, MF (33020), PAM used, assessment process |
| **Domain knowledge** | Understanding of the domain being assessed (e.g., automotive SW development; service management) |
| **Process knowledge** | Understanding of the processes being assessed (what good practice looks like) |
| **Assessment skills** | Interviewing; evidence evaluation; objective judgment; conflict resolution; reporting |
| **Personal qualities** | Objectivity; professionalism; ethical behavior; communication skills |
| **Experience** | Demonstrated participation in prior assessments (under supervision for initial assessments) |

### 4.3 Evidence Principles

| Principle | Description |
|:---------:|-------------|
| **Objectivity** | Evidence must be objective (observable; verifiable); not opinions |
| **Sufficiency** | Enough evidence to make confident rating decisions |
| **Representativeness** | Evidence represents the actual state (not cherry-picked best examples) |
| **Relevance** | Evidence relates to the process attribute being rated |
| **Traceability** | Ratings can be traced back to specific evidence items |
| **Triangulation** | Multiple evidence sources corroborate findings (minimum 2; ideally 3) |

---

## Chapter 5 — Capability Level Achievement (ISO 33020)

### 5.1 Achievement Rules

| Target CL | Requirements | In Plain Language |
|:---------:|:---:|---|
| **CL 1** | PA 1.1 ≥ L | Process basically works; outcomes are achieved |
| **CL 2** | PA 1.1 = F; PA 2.1 ≥ L; PA 2.2 ≥ L | Process is fully performed AND managed |
| **CL 3** | All PAs at CL 1-2 = F; PA 3.1 ≥ L; PA 3.2 ≥ L | Process standardized and deployed |
| **CL 4** | All PAs at CL 1-3 = F; PA 4.1 ≥ L; PA 4.2 ≥ L | Process statistically controlled |
| **CL 5** | All PAs at CL 1-4 = F; PA 5.1 ≥ L; PA 5.2 ≥ L | Process continuously innovating |

### 5.2 Rating Decision Process

```mermaid
graph TB
    subgraph "Rating Decision for One Process Attribute"
        COLLECT_E[Collect Evidence<br/>━━━━━━━━━<br/>• Multiple sources<br/>• Artifacts, interviews, observations<br/>• Relevant to this PA]
        
        JUDGE[Assess Achievement<br/>━━━━━━━━━<br/>• How much of this PA is achieved?<br/>• What's the extent of achievement?<br/>• Any significant weaknesses?]
        
        RATE_D[Assign Rating<br/>━━━━━━━━━<br/>• N: Little or no evidence (0-15%)<br/>• P: Some achievement (16-50%)<br/>• L: Significant achievement; minor gaps (51-85%)<br/>• F: Full and systematic; no weaknesses (86-100%)]
        
        DOCUMENT[Document Rationale<br/>━━━━━━━━━<br/>• List evidence supporting rating<br/>• Note strengths<br/>• Note weaknesses<br/>• Note improvement opportunities]
    end
    
    COLLECT_E --> JUDGE --> RATE_D --> DOCUMENT
```

### 5.3 Common Rating Scenarios

| Scenario | Likely Rating | Rationale |
|:---:|:---:|---|
| No evidence the process exists | **N** | Process not implemented |
| Some artifacts exist; informal; not systematic | **P** | Partially achieved; ad-hoc effort |
| Systematic approach; mostly achieved; few gaps | **L** | Largely achieved; gaps are minor |
| Complete and systematic; no significant weakness | **F** | Fully achieved |
| Process works well for 3/4 projects; one project has major gaps | **L** | Not consistently achieved across scope |
| Documented process exists but not followed | **P** (or N) | Having a document ≠ achieving the PA |

---

## Chapter 6 — Relationship to Domain Standards

### 6.1 How Domain PAMs Use ISO 33000 Framework

| Domain Standard | Role | Relationship to ISO 33000 |
|:-:|:---:|---|
| **ISO/IEC 12207:2017** | PRM (Process Reference Model) | Defines software lifecycle processes (the WHAT); serves as basis for PAMs |
| **ISO/IEC 15288:2023** | PRM | Defines system lifecycle processes; basis for system engineering PAMs |
| **Automotive SPICE 4.0** | PAM | Assessment model for automotive; uses ISO 33020 MF; maps to 12207/15288 processes |
| **ISO/IEC 33063** | PAM | Assessment model for software testing (maps to ISO 29119) |
| **IEC 62304:2015** | Domain standard (medical) | Medical device SW lifecycle; CAN be assessed using 33000 framework + medical PAM |
| **ECSS-Q-ST-80C** | Domain standard (space) | Space SW engineering; process assessment aligned with 33000 concepts |

### 6.2 PRM-PAM-MF Triad

```mermaid
graph LR
    subgraph "The Assessment Triad"
        PRM_T[Process Reference Model (PRM)<br/>━━━━━━━━━<br/>WHAT processes exist?<br/>What are their purposes?<br/>What are their outcomes?<br/>━━━━━━━━━<br/>Example: ISO 12207]
        
        PAM_T[Process Assessment Model (PAM)<br/>━━━━━━━━━<br/>HOW to assess?<br/>What evidence to look for?<br/>What indicators map to outcomes?<br/>━━━━━━━━━<br/>Example: ASPICE 4.0]
        
        MF_T[Measurement Framework (MF)<br/>━━━━━━━━━<br/>HOW to measure?<br/>What levels exist?<br/>What rating scale?<br/>━━━━━━━━━<br/>Example: ISO 33020 (CL 0-5)]
    end
    
    PRM_T -->|"PAM references PRM processes"| PAM_T
    MF_T -->|"PAM uses MF for measurement"| PAM_T
```

---

## Chapter 7 — Comparison: ISO 33000 vs ISO 15504

| Criterion | ISO/IEC 15504 (withdrawn) | ISO/IEC 33000 series |
|:---------:|:---:|:---:|
| **Status** | Withdrawn (2017) | Active; current |
| **Structure** | Multi-part single standard (15504-1 through 15504-10) | Separate numbered standards (33001-33063+) |
| **MF location** | Embedded in 15504-2 | Standalone: ISO 33020 |
| **PRM/PAM requirements** | In 15504-2 and 15504-4 | Separated: ISO 33004 |
| **Assessment requirements** | In 15504-2 (mixed with MF) | Separated: ISO 33002 |
| **Terminology** | Scattered across parts | Centralized: ISO 33001 |
| **Capability levels** | CL 0-5 (6 levels) | CL 0-5 (6 levels) — same scale |
| **Rating scale** | N/P/L/F | N/P/L/F — same |
| **Process attributes** | 9 PAs (PA 1.1 through PA 5.2) | 9 PAs (same set) |
| **Flexibility** | Limited extensibility | Designed for domain extensions |
| **Domain PAMs** | Informative annex (15504-5) | Separate standards; domain-specific PAMs encouraged |

**Key takeaway:** The ASSESSMENT FRAMEWORK (levels, ratings, process attributes) is essentially UNCHANGED between ISO 15504 and ISO 33000. The restructuring was organizational (better standard architecture), not substantive (no new levels or attributes).

---

## Chapter 8 — Architecture Diagrams

### 8.1 ISO 33000 Series Relationship Map

```mermaid
graph TB
    subgraph "ISO 33000 Series — Complete Architecture"
        subgraph "Foundation"
            S33001[ISO 33001<br/>Concepts & Terminology]
        end
        
        subgraph "Normative Requirements"
            S33002[ISO 33002<br/>Assessment Requirements]
            S33003[ISO 33003<br/>Measurement Framework Requirements]
            S33004[ISO 33004<br/>PRM, PAM, MM Requirements]
        end
        
        subgraph "Measurement Frameworks"
            S33020[ISO 33020<br/>Capability MF<br/>(CL 0-5; 9 PAs)]
        end
        
        subgraph "Guidance"
            S33014[ISO 33014<br/>Process Improvement Guide]
        end
        
        subgraph "Domain PAMs"
            S33063[ISO 33063<br/>Testing PAM]
            ASPICE_D[Automotive SPICE<br/>(industry PAM)]
            OTHER_D[Other domain PAMs<br/>(medical, space, etc.)]
        end
    end
    
    S33001 --> S33002
    S33001 --> S33003
    S33001 --> S33004
    S33003 --> S33020
    S33004 --> S33063
    S33004 --> ASPICE_D
    S33004 --> OTHER_D
    S33020 --> S33063
    S33020 --> ASPICE_D
    S33002 --> S33063
    S33002 --> ASPICE_D
    S33014 -.->|"uses results from"| S33002
```

### 8.2 Assessment Process Flow

```mermaid
graph TB
    subgraph "ISO 33002 Assessment Process"
        A_INIT[1. Initiation<br/>• Identify sponsor & purpose<br/>• Define scope<br/>• Select PAM + MF<br/>• Identify constraints]
        
        A_PLAN[2. Planning<br/>• Assessment plan<br/>• Team composition<br/>• Schedule & logistics<br/>• Evidence strategy]
        
        A_BRIEF[3. Briefing<br/>• Inform organization<br/>• Explain process<br/>• Set expectations<br/>• Confirm participants]
        
        A_DATA[4. Data Collection<br/>• Document review<br/>• Interviews<br/>• Observation<br/>• Tool inspection]
        
        A_VALID[5. Data Validation<br/>• Triangulation<br/>• Resolve discrepancies<br/>• Confirm with organization<br/>• Preliminary findings]
        
        A_RATE[6. Process Rating<br/>• Rate each PA (N/P/L/F)<br/>• Determine CL per process<br/>• Document evidence chain<br/>• Identify strengths/weaknesses]
        
        A_REPORT[7. Reporting<br/>• Assessment report<br/>• Capability profile<br/>• Findings<br/>• Improvement recommendations]
    end
    
    A_INIT --> A_PLAN --> A_BRIEF --> A_DATA --> A_VALID --> A_RATE --> A_REPORT
```

---

## Chapter 9 — Case Studies

### 9.1 Creating a Domain-Specific PAM (Railway)

| Aspect | Detail |
|--------|--------|
| **Context** | European railway signalling organization wants to assess supplier processes using ISO 33000 framework |
| **Challenge** | No existing railway-specific PAM; ASPICE is automotive; generic ISO 33063 is testing-only |
| **Approach** | Developed railway PAM: (1) Selected PRM: ISO 12207 + CENELEC EN 50128 (railway SW safety); (2) Used ISO 33020 as MF (CL 0-5; standard rating scale); (3) Defined assessment indicators specific to railway (e.g., safety integrity levels; proven-in-use evidence; formal methods for SIL 3/4); (4) Validated PAM: pilot assessments on 3 suppliers; (5) Submitted for CENELEC/ISO review |
| **Result** | Railway PAM enables: comparable supplier assessments; alignment with EN 50128 safety requirements; reuse of ISO 33000 assessor competence framework; assessment results compatible with automotive (same CL scale → supply chain speaks same language) |
| **Lesson** | ISO 33000 framework's strength: EXTENSIBLE. Any domain can create its own PAM while reusing the measurement framework, rating scale, and assessment process. Results are comparable across domains. |

### 9.2 ISO 15504 → ISO 33000 Transition (Automotive Supplier)

| Aspect | Detail |
|--------|--------|
| **Organization** | Tier-1 automotive supplier; previously assessed to ASPICE 3.0 (based on ISO 15504) |
| **Trigger** | ASPICE 4.0 now references ISO 33001 (not ISO 15504); OEM requires ASPICE 4.0 assessment |
| **Impact analysis** | Capability levels: SAME (CL 0-5). Rating scale: SAME (N/P/L/F). Process attributes: SAME (PA 1.1 through PA 5.2). Achievement rules: SAME. → Process capability ratings from ASPICE 3.1 assessments remain valid. |
| **Changes needed** | (1) Assessor terminology alignment (references to ISO 33001 instead of ISO 15504). (2) ASPICE 4.0 process model updates (SWE.5 name change; new base practices). (3) Agile evidence interpretation updated. (4) Assessors retrained on ASPICE 4.0 specifics. |
| **Conclusion** | Transition was LOW EFFORT for organizations (framework didn't fundamentally change). Main effort: adapting to ASPICE 4.0 content changes (not framework changes). |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact on ISO 33000 |
|-------|----------|---------------------|
| **New domain PAMs** | Ongoing | Cybersecurity PAM (ISO 21434-based); AI/ML PAM; cloud-native PAM |
| **Continuous assessment** | 2025-2030 | Framework may need to accommodate real-time/continuous evidence collection (not just periodic) |
| **Automated rating** | 2025-2028 | AI-assisted evidence analysis; automated PA rating with human validation |
| **ISO 33020 revision** | 2025+ | Possible simplification or extension for new quality concerns (AI, sustainability) |
| **Integration with other frameworks** | Now | Better alignment with CMMI (mapping guides); DevOps metrics as assessment evidence |
| **Simplified assessments** | 2024+ | Demand for lighter-weight assessment methods within the framework (fit between Class C and full assessment) |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is the relationship between ISO 15504 and ISO 33000? Why was the transition made?

**A:** ISO/IEC 33000 series REPLACES ISO/IEC 15504. The transition was made because:
- ISO 15504 was a monolithic multi-part standard mixing different concerns (assessment requirements, measurement framework, PAM requirements) in overlapping parts
- ISO 33000 separates concerns into focused standards (33001=terminology; 33002=assessment; 33003=measurement requirements; 33004=model requirements; 33020=measurement framework)
- This enables: independent updates to each part; cleaner domain-specific extensions; better maintainability

**Critical point:** The actual MEASUREMENT SYSTEM is unchanged:
- Same 6 capability levels (CL 0-5)
- Same 9 process attributes (PA 1.1 through PA 5.2)
- Same rating scale (N/P/L/F)
- Same achievement rules

### Tier 2: Mid-Level

**Q2:** Explain the difference between a PRM, a PAM, and an MF. Give examples of each.

**A:**

| Concept | Definition | Answers | Example |
|:-------:|------------|:-------:|:-------:|
| **PRM** (Process Reference Model) | Defines WHAT processes exist, their purposes, and outcomes | "What should we do?" | ISO/IEC 12207 (SW lifecycle processes); ISO/IEC 15288 (system lifecycle) |
| **PAM** (Process Assessment Model) | Defines HOW to assess processes — provides assessment indicators mapped to PRM | "How do we measure how well we do it?" | Automotive SPICE 4.0; ISO 33063 (testing PAM) |
| **MF** (Measurement Framework) | Defines the SCALE for measuring — capability levels, process attributes, rating | "What scale do we use?" | ISO 33020 (CL 0-5; PA 1.1-5.2; N/P/L/F rating) |

**Analogy:** 
- PRM = "What subjects should students learn?" (curriculum)
- PAM = "What exam questions test each subject?" (exam paper)
- MF = "What grading scale: A/B/C/D/F?" (marking scheme)

### Tier 3: Senior

**Q3:** You are tasked with creating a Process Assessment Model for DevOps practices, conformant to ISO 33004. Outline the approach.

**A:**

**Step 1: Select/Create PRM**
- Define DevOps processes with purposes and outcomes
- Candidates: Continuous Integration, Continuous Delivery, Infrastructure as Code, Monitoring & Observability, Incident Management, Configuration as Code, Automated Testing, Release Management
- Each process gets: purpose statement + 3-7 measurable outcomes

**Step 2: Define Assessment Indicators (PAM)**

For each process, define indicators at each PA level:

| PA Level | Indicators for "Continuous Integration" process example |
|:---:|---|
| PA 1.1 (Performance) | Code is integrated frequently (daily minimum); automated build on each commit; build failures detected within minutes; team is notified of failures |
| PA 2.1 (Performance Management) | CI process is planned (pipeline defined); roles assigned (who fixes broken builds); CI metrics monitored (build frequency; failure rate; fix time) |
| PA 2.2 (Work Product Management) | Build artifacts are managed (versioned; stored); build configurations are version-controlled; test results archived |
| PA 3.1 (Process Definition) | Organization has standard CI process (template pipeline; mandatory stages; quality gates) |
| PA 3.2 (Process Deployment) | All teams use standard CI process (tailored per project); CI metrics collected for improvement |

**Step 3: Map to ISO 33020 MF**
- Use standard CL 0-5 scale
- Use standard N/P/L/F rating
- Apply standard achievement rules

**Step 4: Validate PAM (ISO 33004 conformance)**
- Demonstrate: all PRM outcomes covered by PAM indicators
- Demonstrate: indicators are assessable (can find evidence)
- Pilot assessment: apply PAM to 3+ organizations; validate that ratings are meaningful and reproducible

---

## Chapter 12 — Cheat Sheet & Quick Reference

```
═══════════════════════════════════════════
ISO 33000 SERIES — QUICK REFERENCE
═══════════════════════════════════════════

KEY STANDARDS:
  ISO 33001: Concepts & Terminology
  ISO 33002: Requirements for Assessment
  ISO 33003: Requirements for Measurement Frameworks
  ISO 33004: Requirements for PRMs, PAMs, MMs
  ISO 33020: Measurement Framework (CL 0-5)
  ISO 33014: Process Improvement Guide
  ISO 33063: Testing PAM

═══════════════════════════════════════════
REPLACES ISO 15504 (withdrawn 2017):
  15504-2 → split into 33002 + 33003 + 33004
  15504-5 → domain PAMs (e.g., ASPICE)
  15504-9 → 33014 (improvement)

═══════════════════════════════════════════
THE TRIAD:
  PRM = WHAT processes (purposes + outcomes)
  PAM = HOW to assess (indicators + evidence)
  MF  = SCALE to measure (levels + rating)

═══════════════════════════════════════════
ISO 33020 MEASUREMENT FRAMEWORK:
  CL 0: Incomplete (no PA assessed)
  CL 1: Performed (PA 1.1)
  CL 2: Managed (PA 2.1, 2.2)
  CL 3: Established (PA 3.1, 3.2)
  CL 4: Predictable (PA 4.1, 4.2)
  CL 5: Innovating (PA 5.1, 5.2)

═══════════════════════════════════════════
9 PROCESS ATTRIBUTES:
  PA 1.1: Process Performance
  PA 2.1: Performance Management
  PA 2.2: Work Product Management
  PA 3.1: Process Definition
  PA 3.2: Process Deployment
  PA 4.1: Quantitative Analysis
  PA 4.2: Quantitative Control
  PA 5.1: Process Innovation
  PA 5.2: Process Optimization

═══════════════════════════════════════════
RATING SCALE:
  N = Not achieved (0-15%)
  P = Partially achieved (16-50%)
  L = Largely achieved (51-85%)
  F = Fully achieved (86-100%)

═══════════════════════════════════════════
CL ACHIEVEMENT RULES:
  CL 1: PA 1.1 ≥ L
  CL 2: PA 1.1=F; PA 2.1≥L; PA 2.2≥L
  CL 3: All lower=F; PA 3.1≥L; PA 3.2≥L
  CL 4: All lower=F; PA 4.1≥L; PA 4.2≥L
  CL 5: All lower=F; PA 5.1≥L; PA 5.2≥L

═══════════════════════════════════════════
ASSESSMENT EVIDENCE PRINCIPLES:
  • Objectivity (observable; verifiable)
  • Sufficiency (enough to decide)
  • Representativeness (actual state)
  • Triangulation (2-3 sources minimum)
  • Traceability (rating → evidence)

═══════════════════════════════════════════
ISO 33000 vs ISO 15504:
  SAME: Levels, PAs, ratings, achievement rules
  DIFFERENT: Better structure; separate standards;
    extensibility; domain PAM support
```

---

*End of Document — 03_ISO_33001_Assessment.md*
