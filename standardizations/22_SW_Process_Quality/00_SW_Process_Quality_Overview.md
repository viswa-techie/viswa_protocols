# Software Process & Quality Management — Overview

**Domain:** Software Process Improvement, Quality Management, Capability Assessment  
**Standards:** CMMI v2.0, Automotive SPICE 4.0, ISO/IEC 33001, ISO/IEC 25010, ITIL 4, ISO/IEC/IEEE 29119  
**Frameworks:** SAFe 6.0, DevSecOps, DORA Metrics, LeSS, DAD  
**Audience:** Process engineers, quality managers, ASPICE assessors, Scrum Masters, DevOps engineers, CMMi appraisers  
**Prerequisites:** Basic software development lifecycle; familiarity with Agile/Waterfall; understanding of ISO 9001 quality concepts

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Milestone |
|------|-----------|
| 1968 | NATO Software Engineering Conference — recognizes "software crisis" |
| 1970 | Royce waterfall paper — first published SW lifecycle model |
| 1985 | Watts Humphrey begins CMM work at SEI (Carnegie Mellon) |
| 1991 | **CMM v1.0** published (Capability Maturity Model for Software) |
| 1993 | SPICE project begins (Software Process Improvement & Capability dEtermination) |
| 1995 | ISO 15504 (SPICE) standard development begins |
| 1998 | ISO/IEC 15504 Part 2 published |
| 2001 | **CMMI v1.0** (replaces CMM-SW, CMM-SE, CMM-IPPD); **Agile Manifesto** published |
| 2003 | Automotive SPICE v2.0 (VDA); Scrum adoption accelerates |
| 2006 | ISO/IEC 15504-5; CMMI v1.2 |
| 2009 | ITIL v3 (2007 update); DevOps movement begins |
| 2010 | CMMI v1.3 (final "v1" generation); ASPICE v2.4 |
| 2017 | ASPICE v3.0; Continuous Delivery becomes mainstream |
| 2018 | **CMMI v2.0** (ISACA/SEI); **ITIL 4**; ASPICE v3.1 |
| 2019 | DORA "Accelerate" State of DevOps Report; ISO/IEC 20000-1:2018 |
| 2022 | SAFe 6.0; ISO/IEC 25010:2023 revision |
| 2024 | **Automotive SPICE 4.0**; CMMI v3.0 in development |

### 1.2 The Evolution of Software Quality Thinking

| Era | Philosophy | Key Innovation |
|:---:|------------|----------------|
| **1970s** | "Test quality in" | Testing at end of development |
| **1980s** | "Inspect quality in" | Code reviews; Fagan inspection; structured programming |
| **1990s** | "Process quality" | CMM; ISO 9001; "Good process → Good product" |
| **2000s** | "Architect quality in" | Design patterns; architecture-driven quality |
| **2010s** | "Automate quality" | CI/CD; automated testing; DevOps; shift-left |
| **2020s** | "Continuous quality" | DevSecOps; AI-assisted quality; observability; SRE |

---

## Chapter 2 — Standards Landscape Overview

### 2.1 Standards Ecosystem Map

```mermaid
graph TB
    subgraph "Software Process & Quality Standards Landscape"
        subgraph "Capability/Maturity Models"
            CMMI[CMMI v2.0<br/>━━━━━━━━━<br/>• 20 Practice Areas<br/>• ML 1-5; CL 0-3<br/>• DEV/SVC/ACQ views<br/>• ISACA (was SEI)]
            ASPICE[Automotive SPICE 4.0<br/>━━━━━━━━━<br/>• VDA (German Auto Assn)<br/>• SWE, SYS, HWE, MAN, SUP<br/>• CL 0-5<br/>• Automotive-specific]
            ISO33[ISO/IEC 33000 series<br/>━━━━━━━━━<br/>• Process assessment framework<br/>• Measurement framework<br/>• Reference models<br/>• Replaces ISO 15504]
        end
        
        subgraph "Quality Models"
            ISO25[ISO/IEC 25010:2023<br/>━━━━━━━━━<br/>• Product quality model<br/>• 9 characteristics<br/>• Quality in use model]
            ISO9001[ISO 9001:2015<br/>━━━━━━━━━<br/>• Quality Management System<br/>• Generic (all industries)<br/>• Plan-Do-Check-Act]
        end
        
        subgraph "Service Management"
            ITIL[ITIL 4 (2019)<br/>━━━━━━━━━<br/>• Service Value System<br/>• 34 Practices<br/>• Value co-creation]
            ISO20K[ISO/IEC 20000-1<br/>━━━━━━━━━<br/>• ITSM certification<br/>• Aligned with ITIL]
        end
        
        subgraph "Testing"
            ISO29119[ISO/IEC/IEEE 29119<br/>━━━━━━━━━<br/>• Test processes<br/>• Test documentation<br/>• Test techniques]
            ISTQB[ISTQB<br/>━━━━━━━━━<br/>• Certified Tester<br/>• Foundation/Advanced/Expert<br/>• Test levels & types]
        end
        
        subgraph "Agile & DevOps"
            SAFE[SAFe 6.0<br/>━━━━━━━━━<br/>• Scaled Agile Framework<br/>• 4 configurations<br/>• ART; PI Planning]
            DORA[DORA Metrics<br/>━━━━━━━━━<br/>• Deployment frequency<br/>• Lead time<br/>• Change failure rate<br/>• MTTR]
            DEVSECOPS[DevSecOps<br/>━━━━━━━━━<br/>• Security in pipeline<br/>• SAST/DAST/SCA<br/>• Continuous compliance]
        end
    end
```

### 2.2 Standards Relationship

| Standard/Framework | Scope | Certification? | Assessable? |
|:---:|---|:---:|:---:|
| **CMMI v2.0** | Organization-level process maturity (any domain) | Yes (benchmarking) | Yes (SCAMPI appraisal) |
| **Automotive SPICE 4.0** | Automotive SW/SYS/HW process capability | No (but OEM requires results) | Yes (INTACS-certified assessor) |
| **ISO/IEC 33001** | Framework for process assessment (meta-standard) | No | Defines how to assess |
| **ISO/IEC 25010** | Software product quality model | No | Evaluation per ISO 25040 |
| **ITIL 4** | IT service management best practices | Yes (individual certification) | No (organization adopts voluntarily) |
| **ISO/IEC 20000-1** | ITSM system requirements | Yes (organization certification) | Yes (audit by certification body) |
| **ISO/IEC/IEEE 29119** | Software testing processes & documentation | No | Reference for test process |
| **SAFe 6.0** | Scaled Agile implementation | Yes (individual: SA, SPC, RTE) | No (but SAFe maturity assessments exist) |

---

## Chapter 3 — Key Concepts

### 3.1 Process Capability vs. Maturity

| Concept | Definition | Scale | Assessment |
|:-------:|------------|:-----:|:---:|
| **Capability Level** | How well a SINGLE PROCESS is performed | CL 0-5 (or CL 0-3 in CMMI v2.0) | Per process |
| **Maturity Level** | How well the ORGANIZATION manages its PROCESSES overall | ML 1-5 | Organization-wide |

**CMMI v2.0 levels:**

| Level | Capability (per process) | Maturity (organization) |
|:-----:|:---:|:---:|
| 0 | Incomplete (not performed or partially performed) | — |
| 1 | Performed (process achieves outcomes; ad-hoc) | Initial (unpredictable; reactive) |
| 2 | Managed (planned; monitored; controlled per project) | Managed (projects are managed) |
| 3 | Defined (tailored from org standard; proactive) | Defined (organization-wide standard processes) |
| 4 | — | Quantitatively Managed (statistical process control) |
| 5 | — | Optimizing (continuous improvement; innovation) |

### 3.2 Automotive SPICE Capability Levels

| Level | Name | Meaning |
|:-----:|:----:|---------|
| 0 | Incomplete | Process not implemented or fails to achieve outcomes |
| 1 | Performed | Process achieves its outcomes (work products exist; purpose fulfilled) |
| 2 | Managed | Process is planned, monitored, adjusted; work products managed (baseline, review) |
| 3 | Established | Standard process defined at organizational level; projects tailor from standard |
| 4 | Predictable | Process measured quantitatively; statistical control; predictable outcomes |
| 5 | Innovating | Continuous improvement driven by data; innovative practices adopted |

**Industry practice:** Most automotive OEMs require suppliers to achieve **CL 2** (minimum) for series production; CL 3 for platform/safety-critical suppliers.

---

## Chapter 4 — Process Assessment Fundamentals

### 4.1 Assessment Process (ISO 33002)

```mermaid
graph TB
    subgraph "Process Assessment (ISO 33002)"
        INIT[Initiation<br/>━━━━━━━━━<br/>• Define assessment scope<br/>• Select process reference model<br/>• Select measurement framework<br/>• Identify assessment team<br/>• Plan assessment]
        
        PLAN[Planning<br/>━━━━━━━━━<br/>• Assessment schedule<br/>• Evidence collection plan<br/>• Interview schedule<br/>• Resource allocation]
        
        BRIEF[Briefing<br/>━━━━━━━━━<br/>• Brief assessed organization<br/>• Explain process; methods<br/>• Set expectations<br/>• Confirm logistics]
        
        COLLECT[Data Collection<br/>━━━━━━━━━<br/>• Interviews (managers; practitioners)<br/>• Document review<br/>• Work product examination<br/>• Tool/artifact inspection]
        
        VALIDATE[Data Validation<br/>━━━━━━━━━<br/>• Corroborate findings<br/>• Triangulation (3 sources)<br/>• Confirm with assessed party<br/>• Resolve discrepancies]
        
        RATE[Process Rating<br/>━━━━━━━━━<br/>• Rate process attributes<br/>• Determine capability level<br/>• Document evidence<br/>• Identify strengths/weaknesses]
        
        REPORT[Reporting<br/>━━━━━━━━━<br/>• Assessment report<br/>• Findings; recommendations<br/>• Improvement priorities<br/>• Present to sponsor]
    end
    
    INIT --> PLAN --> BRIEF --> COLLECT --> VALIDATE --> RATE --> REPORT
```

### 4.2 Process Attribute Rating (ISO 33020)

| Rating | Symbol | Meaning | Achievement Range |
|:------:|:------:|---------|:-:|
| **Not achieved** | N | Little or no evidence of achievement | 0-15% |
| **Partially achieved** | P | Some evidence; not systematic | 16-50% |
| **Largely achieved** | L | Systematic approach; significant achievement | 51-85% |
| **Fully achieved** | F | Complete and systematic; no significant weakness | 86-100% |

**To achieve Capability Level N:**
- All process attributes at levels ≤ N-1 must be rated **F** (Fully achieved)
- All process attributes at level N must be rated **L** or **F** (Largely or Fully achieved)

---

## Chapter 5 — Quality Models (ISO 25010)

### 5.1 ISO/IEC 25010:2023 — Product Quality Model

| Characteristic | Sub-characteristics | Definition |
|:-:|---|---|
| **Functional Suitability** | Completeness, Correctness, Appropriateness | Does it do what it should? |
| **Performance Efficiency** | Time behaviour, Resource utilization, Capacity | How fast and resource-efficient? |
| **Compatibility** | Co-existence, Interoperability | Works with other systems? |
| **Interaction Capability** | Appropriateness recognizability, Learnability, Operability, User error protection, Accessibility | Easy to use? (replaces "Usability" from 25010:2011) |
| **Reliability** | Maturity, Availability, Fault tolerance, Recoverability | How dependable? |
| **Security** | Confidentiality, Integrity, Non-repudiation, Accountability, Authenticity | How protected? |
| **Maintainability** | Modularity, Reusability, Analysability, Modifiability, Testability | Easy to change? |
| **Flexibility** | Adaptability, Scalability, Installability, Replaceability | Adapts to new contexts? (new in 2023) |
| **Safety** | Operational constraint, Risk identification, Fail safe, Hazard warning, Safe integration | Freedom from unacceptable risk? (new in 2023) |

---

## Chapter 6 — Agile + Process Compliance

### 6.1 Agile + ASPICE Integration

```mermaid
graph TB
    subgraph "Agile + ASPICE Integration"
        subgraph "ASPICE V-Model (Quality Gates)"
            A_SWE1[SWE.1: SW Requirements]
            A_SWE2[SWE.2: SW Architecture]
            A_SWE3[SWE.3: SW Design/Code]
            A_SWE4[SWE.4: SW Unit Verification]
            A_SWE5[SWE.5: SW Integration Test]
            A_SWE6[SWE.6: SW Qualification Test]
        end
        
        subgraph "Agile Practices (Delivery)"
            SPRINT[Sprint Cycle (2 weeks)<br/>• User stories from backlog<br/>• Sprint planning<br/>• Daily standup<br/>• Sprint review/retro]
            CI[CI/CD Pipeline<br/>• Build on every commit<br/>• Automated unit tests<br/>• Static analysis (MISRA)<br/>• Integration tests]
            BACKLOG[Product Backlog<br/>• Epics → Stories<br/>• Acceptance criteria<br/>• Definition of Done<br/>  includes ASPICE evidence]
        end
    end
    
    A_SWE1 -.->|"implemented as"| BACKLOG
    A_SWE3 -.->|"implemented as"| SPRINT
    A_SWE4 -.->|"implemented as"| CI
    A_SWE5 -.->|"implemented as"| CI
```

### 6.2 Mapping: Agile Artifacts ↔ ASPICE Work Products

| ASPICE Work Product | Agile Equivalent | How to Satisfy ASPICE |
|:---:|:---:|---|
| SW Requirements Specification | Product Backlog (Epics + Stories + Acceptance Criteria) | Export backlog as versioned SRS; trace to system requirements |
| SW Architecture Document | Architecture Decision Records (ADRs) + Architecture wiki | Maintain living architecture doc; update per sprint; review |
| Unit Test Results | CI pipeline test reports | Automated: every commit generates test report artifact |
| Integration Test Results | CI pipeline integration suite results | Automated: every merge triggers integration tests |
| Traceability Matrix | Jira links (Epic → Story → Task → Commit → Test) | Automated traceability via tool linking (Jira + Bitbucket + Jenkins) |
| Change Request | Jira ticket (type: Change Request; linked to original story) | Formal CR workflow in Jira; impact analysis; CCB approval |
| Review Records | Pull Request review comments; sprint review notes | PR = code review evidence; sprint review = demo evidence |

---

## Chapter 7 — Comparison: Major Frameworks

| Criterion | CMMI v2.0 | ASPICE 4.0 | ISO 9001 | ITIL 4 | SAFe 6.0 |
|:---------:|:---------:|:----------:|:--------:|:------:|:---------:|
| **Focus** | Organization process maturity | Automotive SW/SYS process capability | Quality management system | IT service management | Scaled Agile delivery |
| **Domain** | Any (SW, services, acquisition) | Automotive | Any industry | IT services | Any (but especially SW) |
| **Assessment** | SCAMPI (A, B, C) | INTACS-certified assessor | ISO certification audit | No formal audit (individual cert) | SAFe assessment (Measure & Grow) |
| **Levels** | ML 1-5; CL 0-3 | CL 0-5 | Pass/fail (certified or not) | N/A | N/A |
| **Process focus** | 20 Practice Areas | SWE (6), SYS (5), HWE (3), MAN, SUP, REU | Clauses 4-10 (QMS) | 34 Practices | ART; PI; DevOps pipeline |
| **Certification** | Organization benchmarked | Supplier assessed (OEM receives results) | Organization certified | Individual certified | Individual certified (SA, SPC) |
| **Cost** | High (SCAMPI appraisal: $50-200K) | Medium ($30-100K per assessment) | Medium ($20-50K per audit) | Low (individual exam: $500-2000) | Medium (training: $1-5K per person) |

---

## Chapter 8 — DevSecOps & DORA Metrics

### 8.1 DORA Four Key Metrics

| Metric | Definition | Elite | High | Medium | Low |
|:------:|------------|:-----:|:----:|:------:|:---:|
| **Deployment Frequency** | How often code is deployed to production | On-demand (multiple/day) | Weekly-monthly | Monthly-6 months | < once per 6 months |
| **Lead Time for Changes** | Time from commit to production | < 1 hour | 1 day-1 week | 1 week-1 month | 1-6 months |
| **Change Failure Rate** | % of deployments causing failure | 0-15% | 16-30% | 16-30% | 46-60% |
| **Time to Restore** | Time to recover from failure | < 1 hour | < 1 day | 1 day-1 week | 1 week-1 month |

### 8.2 DevSecOps Pipeline

```mermaid
graph LR
    subgraph "DevSecOps Pipeline"
        CODE[Code<br/>• IDE plugins<br/>• Pre-commit hooks<br/>• SAST (static)]
        BUILD[Build<br/>• Compile<br/>• Dependency scan (SCA)<br/>• Container scan]
        TEST[Test<br/>• Unit tests<br/>• Integration tests<br/>• DAST (dynamic)<br/>• Fuzz testing]
        RELEASE[Release<br/>• Artifact signing<br/>• License compliance<br/>• Security gate (pass/fail)]
        DEPLOY[Deploy<br/>• IaC validation<br/>• Secret scanning<br/>• Runtime protection]
        MONITOR[Monitor<br/>• SIEM/SOC<br/>• Vulnerability alerts<br/>• Compliance drift<br/>• SLA monitoring]
    end
    
    CODE --> BUILD --> TEST --> RELEASE --> DEPLOY --> MONITOR
    MONITOR -->|"feedback"| CODE
```

---

## Chapter 9 — Case Studies

### 9.1 Automotive Supplier: ASPICE Level 3 Journey

| Aspect | Detail |
|--------|--------|
| **Company** | Tier-1 automotive supplier; 500 SW engineers; developing ADAS ECUs |
| **Starting point** | ASPICE Level 1 (processes performed but ad-hoc); OEM requires Level 2 minimum for new contract |
| **Approach** | 24-month improvement program: Phase 1 (6 months): gap assessment → process definition. Phase 2 (12 months): pilot projects → institutionalize. Phase 3 (6 months): internal assessment → external assessment. |
| **Key changes** | (1) Requirements management: DOORS deployed; bidirectional traceability mandated. (2) Architecture documentation: mandatory SAD per project; reviewed at PDR. (3) Testing: automated CI pipeline; coverage thresholds (80% statement; 60% branch). (4) Configuration management: Git + branching strategy + semantic versioning + release process. (5) Project management: standardized project plans; milestone reviews; risk management. |
| **Result** | External assessment: CL 2 achieved for all SWE/SYS processes; CL 3 for SWE.1, SWE.4, SUP.8. Contract won. Follow-up: targeting full CL 3 within 18 months. |
| **Investment** | €2M over 24 months (tools, training, consultancy, process engineering headcount) |
| **ROI** | Contract value: €50M over 5 years. Quality improvement: 40% reduction in post-release defects. Schedule improvement: 25% fewer integration issues (earlier detection). |

### 9.2 Enterprise IT: DevSecOps Transformation

| Aspect | Detail |
|--------|--------|
| **Company** | Financial services company; 2,000 developers; 400 microservices |
| **Before** | Deployment: quarterly (4 releases/year). Lead time: 3 months. Change failure rate: 25%. MTTR: 2 weeks. → DORA: "Low performer." |
| **Transformation** | 18-month DevSecOps transformation: trunk-based development; CI/CD (Jenkins → GitHub Actions); automated testing (unit + integration + contract); infrastructure as code (Terraform); container orchestration (Kubernetes); security scanning (Snyk + SonarQube + OWASP ZAP in pipeline); observability (Prometheus + Grafana + distributed tracing). |
| **After** | Deployment: daily (250 deploys/week across all services). Lead time: 2 hours. Change failure rate: 8%. MTTR: 45 minutes. → DORA: "Elite performer." |
| **Key enablers** | (1) Feature flags (decouple deploy from release). (2) Canary deployments (10% → 50% → 100% with automatic rollback). (3) Chaos engineering (GameDay exercises). (4) Security as code (policies in OPA; enforced in pipeline). (5) Platform team providing golden paths (standardized CI templates). |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **AI in quality assurance** | 2024-2027 | AI-generated tests; AI code review; predictive defect detection; autonomous testing |
| **ASPICE 4.0 + Agile** | 2024+ (now) | Explicit Agile integration; less document-focused; outcome-based assessment |
| **CMMI v3.0** | 2025-2026 | Expected: AI/ML practices; DevOps integration; sustainability practices |
| **Continuous compliance** | 2024-2028 | Real-time process compliance monitoring (not periodic audits); compliance-as-code |
| **Platform engineering** | 2023+ (now) | Internal developer platforms reduce cognitive load; golden paths enforce quality |
| **AI-assisted assessment** | 2025-2030 | AI tools analyze artifacts to pre-assess process capability; human assessor validates |
| **Quality engineering (shift everywhere)** | Now (ongoing) | Quality not just "shift-left" but embedded everywhere: design, code, deploy, operate |
| **Green software** | 2024-2028 | Software carbon footprint as quality attribute; energy-efficient coding practices |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is the difference between CMMI and Automotive SPICE?

**A:**

| Aspect | CMMI v2.0 | Automotive SPICE 4.0 |
|:------:|:---------:|:-------------------:|
| **Owner** | ISACA (formerly SEI) | VDA (German Automotive Association) + INTACS |
| **Domain** | Any (software, services, acquisition) | Automotive specifically |
| **Focus** | Organization maturity (holistic) | Process capability (individual processes) |
| **Levels** | Maturity Levels 1-5 (organization-wide) | Capability Levels 0-5 (per process) |
| **Assessment** | SCAMPI appraisal (class A/B/C) | INTACS-certified assessor |
| **Practice Areas** | 20 (governance, implementation, engineering, etc.) | SWE (6), SYS (5), HWE (3), MAN, SUP, REU |
| **Typical user** | Defense, aerospace, IT, banking | Automotive OEMs and their suppliers |
| **Cost** | $50-200K per appraisal | $30-100K per assessment |
| **Outcome** | ML rating (1-5) published to benchmark | CL rating per process (shared with OEM) |

**Key difference:** CMMI assesses the ORGANIZATION's overall maturity; ASPICE assesses individual PROCESSES' capability (more granular).

### Tier 2: Mid-Level

**Q2:** How do you integrate ASPICE compliance with Agile development practices? What challenges arise?

**A:** (See Chapter 6 for full mapping table)

**Core principle:** ASPICE defines WHAT must be achieved (outcomes, work products, traceability). Agile defines HOW work is organized (sprints, backlogs, ceremonies). They are compatible IF:

1. **Agile artifacts satisfy ASPICE evidence requirements** (backlog = SRS; PR reviews = code review evidence; CI reports = test evidence)
2. **Traceability is automated** (Jira → Git → Jenkins links provide bidirectional trace without manual effort)
3. **Definition of Done includes ASPICE** (story not done unless: traced, tested, reviewed, coverage met)

**Challenges:**

| Challenge | Solution |
|:---------:|---------|
| ASPICE expects baselined documents; Agile has living artifacts | Baseline per release (not per phase); automated export from tools at release |
| ASPICE assessor expects to see "SRS"; team has backlog | Map backlog structure to SRS template; provide traceability report as evidence |
| ASPICE requires formal architecture document; Agile evolves architecture | Maintain lightweight living architecture (wiki/ADR); formalize at release milestones |
| ASPICE requires change impact analysis; Agile embraces change | Lightweight impact analysis per story (1-line: "impacts: SRS section X; tests Y") |

### Tier 3: Senior

**Q3:** Design a measurement program that combines DORA metrics, ASPICE process metrics, and product quality metrics for an automotive software organization running Agile+DevOps.

**A:**

| Category | Metric | Source | Target | Cadence |
|:--------:|--------|:------:|:------:|:-------:|
| **Delivery (DORA)** | Deployment frequency | CI/CD pipeline | ≥ weekly per ECU variant | Real-time |
| | Lead time (commit → release candidate) | Git + Jenkins | < 1 week | Real-time |
| | Change failure rate | Jenkins + Jira (defect linked to commit) | < 10% | Weekly |
| | MTTR (time to fix escaped defect) | Jira (defect open → resolved) | < 24 hours (critical) | Per incident |
| **Process (ASPICE)** | Requirements traceability coverage | DOORS traceability report | 100% bidirectional | Per release |
| | Review coverage | Jira + PR data | 100% work products reviewed | Per sprint |
| | Test case execution rate | Jenkins test reports | 100% planned tests executed | Per release |
| | Defect detection effectiveness (DDE) | Pre-release defects / (pre + post defects) | > 90% | Quarterly |
| **Product Quality (ISO 25010)** | Defect density (post-release) | Field data + Jira | < 0.5 defects/KLOC/year | Monthly |
| | Code coverage (unit tests) | Jenkins + coverage tool | ≥ 80% statement; ≥ 60% branch | Per commit |
| | Static analysis violations (MISRA) | Polyspace/PC-lint in CI | 0 mandatory violations; < 10 advisory | Per commit |
| | Performance (response time; resource usage) | Bench measurements; profiling | Within allocated budget (±5%) | Per release |
| | Reliability (MTBF in field) | Field failure reports | > 50,000 hours | Quarterly |

---

## Chapter 12 — Cheat Sheet & Quick Reference

```
═══════════════════════════════════════════
SW PROCESS & QUALITY — QUICK REFERENCE
═══════════════════════════════════════════

CAPABILITY MODELS:
  CMMI v2.0: ML 1-5 (organization maturity)
    ML1=Initial; ML2=Managed; ML3=Defined;
    ML4=Quantitatively Managed; ML5=Optimizing
  ASPICE 4.0: CL 0-5 (per process capability)
    CL0=Incomplete; CL1=Performed; CL2=Managed;
    CL3=Established; CL4=Predictable; CL5=Innovating
  Minimum for automotive series: CL 2

═══════════════════════════════════════════
ASPICE PROCESS AREAS:
  SWE.1-6: SW Requirements → Architecture → Design →
    Unit Test → Integration → Qualification
  SYS.1-5: System Requirements → Architecture →
    Integration → Qualification → Requirements Analysis
  SUP: Supporting (CM, QA, Problem Resolution, etc.)
  MAN: Management (Project, Risk, Measurement)

═══════════════════════════════════════════
ISO 25010:2023 QUALITY CHARACTERISTICS:
  1. Functional Suitability
  2. Performance Efficiency
  3. Compatibility
  4. Interaction Capability (was Usability)
  5. Reliability
  6. Security
  7. Maintainability
  8. Flexibility (new)
  9. Safety (new)

═══════════════════════════════════════════
DORA METRICS (Elite Targets):
  Deployment Frequency: multiple/day
  Lead Time: < 1 hour
  Change Failure Rate: 0-15%
  MTTR: < 1 hour

═══════════════════════════════════════════
ITIL 4 SERVICE VALUE CHAIN:
  Plan → Improve → Engage → Design & Transition →
  Obtain/Build → Deliver & Support

═══════════════════════════════════════════
ISO 29119 TEST PROCESS:
  Test Planning → Test Design → Test Execution →
  Test Completion (+ Test Monitoring & Control)

═══════════════════════════════════════════
ASSESSMENT RATING (ISO 33020):
  N = Not achieved (0-15%)
  P = Partially achieved (16-50%)
  L = Largely achieved (51-85%)
  F = Fully achieved (86-100%)
  
  To reach CL N: all lower attributes = F;
    level N attributes = L or F

═══════════════════════════════════════════
AGILE + ASPICE KEY MAPPING:
  Backlog = Requirements Spec
  PR review = Code Review evidence
  CI test report = Test Results
  Sprint demo = Qualification evidence
  Git history = CM evidence
  Jira links = Traceability
```

---

*End of Document — 00_SW_Process_Quality_Overview.md*
