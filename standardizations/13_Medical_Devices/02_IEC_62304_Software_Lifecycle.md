# IEC 62304:2006+AMD1:2015 — Medical Device Software Lifecycle

**Topic:** Software lifecycle processes for medical device software development and maintenance  
**Standard:** IEC 62304:2006+AMD1:2015 — Medical device software — Software life cycle processes  
**SDO:** IEC/SC 62A (Common aspects of electrical equipment used in medical practice)  
**Audience:** Embedded software engineers, firmware developers, software quality engineers, regulatory affairs for SaMD, V&V engineers  
**Prerequisites:** Software engineering fundamentals, ISO 14971 risk management, ISO 13485 design controls

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 1985 | Therac-25 radiation accidents | 6 deaths from software bugs; catalyzed medical SW regulation |
| 1996 | FDA General Principles of Software Validation | Early guidance on SW validation for medical devices |
| 2002 | IEC 62304 development begins | IEC/SC 62A working group formed |
| 2006 | **IEC 62304:2006** published | First international standard for medical device SW lifecycle |
| 2008 | FDA recognizes IEC 62304 | Consensus standard for FDA submissions |
| 2015 | **Amendment 1 (AMD1:2015)** | Added software safety classification changes; SOUP maintenance; legacy SW |
| 2016 | EU MDR references IEC 62304 | Required for software medical devices in EU |
| 2017 | IEC 82304-1:2016 | Health software (standalone) product standard — references IEC 62304 for lifecycle |
| 2020 | IEC 62304 Edition 2 development starts | Planned major revision (expected 2025-2026) |
| 2023 | FDA Cybersecurity guidance | SBOM; secure development; complements IEC 62304 |
| 2024 | AAMI TIR45:2024 | Guidance on applying IEC 62304 in agile environments |

### 1.2 Position in Standards Framework

```mermaid
graph TB
    ISO14971[ISO 14971:2019<br/>Risk Management<br/>━━━━━━━━━━━━━<br/>Hazard identification<br/>Risk classification<br/>Drives SW safety class]
    
    IEC62304[IEC 62304:2006+AMD1<br/>Software Lifecycle<br/>━━━━━━━━━━━━━<br/>Development process<br/>Maintenance process<br/>Risk/config management]
    
    ISO13485[ISO 13485:2016<br/>Quality Management<br/>━━━━━━━━━━━━━<br/>Design controls (7.3)<br/>Process context<br/>Document control]
    
    IEC62366[IEC 62366-1:2015<br/>Usability Engineering<br/>━━━━━━━━━━━━━<br/>Use-related risk<br/>UI design<br/>Validation with users]
    
    IEC82304[IEC 82304-1:2016<br/>Health Software Product<br/>━━━━━━━━━━━━━<br/>Standalone SW products<br/>References 62304 for lifecycle]
    
    CYBER[FDA Cybersecurity Guidance<br/>━━━━━━━━━━━━━<br/>Secure development<br/>SBOM; threat modeling<br/>Patch management]
    
    ISO14971 -->|"Drives safety class"| IEC62304
    ISO13485 -->|"QMS context<br/>Design controls"| IEC62304
    IEC62304 -->|"Lifecycle for"| IEC82304
    IEC62366 -->|"Usability inputs"| IEC62304
    CYBER -->|"Security requirements"| IEC62304
```

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 IEC 62304 Process Structure

| Clause | Process | Content |
|:------:|---------|---------|
| 4 | General Requirements | QMS context; risk management; software safety classification |
| 5 | **Software Development Process** | Planning; requirements; architecture; detailed design; unit implementation; integration; system testing; release |
| 6 | **Software Maintenance Process** | Problem/modification analysis; implementation; verification/testing; release |
| 7 | **Software Risk Management Process** | Hazard identification; risk control; verification of risk measures |
| 8 | **Software Configuration Management Process** | Identification; change control; status accounting |
| 9 | **Software Problem Resolution Process** | Problem reporting; investigation; advisory communication |

### 2.2 Software Safety Classification

| Class | Severity (if SW fails) | Rigor Required | Example |
|:-----:|----------------------|:--------------:|---------|
| **A** | No injury or damage to health is possible | Minimal | Display-only software (non-diagnostic); data management (non-clinical decision) |
| **B** | Non-serious injury is possible | Moderate | Clinical decision support (non-life-threatening); monitoring with nurse override |
| **C** | Death or serious injury is possible | **Maximum** | Insulin pump control; ventilator control; radiation therapy planning; autonomous AI diagnosis |

**Classification Rules (Amendment 1):**
- Classification is determined by the **software system's contribution to a hazardous situation** (not the device class)
- A Class III medical device could have Class A software (if the software doesn't contribute to hazardous situations)
- Software can be decomposed: safety-critical components (Class C) can be separated from non-critical (Class A/B) through architectural segregation

### 2.3 Requirements by Safety Class

| Activity | Class A | Class B | Class C |
|----------|:-------:|:-------:|:-------:|
| Software development planning | ✅ | ✅ | ✅ |
| Software requirements analysis | ✅ | ✅ | ✅ |
| Software architectural design | — | ✅ | ✅ |
| Software detailed design | — | — | ✅ |
| Software unit implementation | ✅ | ✅ | ✅ |
| Software unit verification | — | — | ✅ |
| Software integration and integration testing | ✅ | ✅ | ✅ |
| Software system testing | ✅ | ✅ | ✅ |
| Software release | ✅ | ✅ | ✅ |
| Software maintenance process | ✅ | ✅ | ✅ |
| Software risk management | ✅ | ✅ | ✅ |
| Software configuration management | ✅ | ✅ | ✅ |
| Software problem resolution | ✅ | ✅ | ✅ |

---

## Chapter 3 — Technical Deep Dive

### 3.1 Clause 5 — Software Development Process

#### 5.1 Software Development Planning

| Required Content | Description |
|-----------------|-------------|
| Deliverables for each activity | What documents/artifacts are produced at each stage |
| Development lifecycle model | Waterfall, V-model, iterative, agile (with justification) |
| Processes to be applied | Which IEC 62304 processes are applicable; exclusions with rationale |
| Software configuration management plan | How code and documents are version-controlled |
| Tasks to resolve software anomalies | Bug handling; triage; resolution |
| Tools and methods | IDE, compilers, test frameworks, static analysis; validation of tools |
| Software integration and integration test plan | How modules are combined and tested |
| Verification plan | What verification activities; when; by whom; acceptance criteria |
| Risk management plan reference | How risk management integrates with development |
| Documentation requirements | What documents are produced; formats; review process |
| Traceability requirements | Requirements → design → code → tests |

#### 5.2 Software Requirements Analysis

| Requirement Type | Examples | Class A | Class B | Class C |
|-----------------|---------|:-------:|:-------:|:-------:|
| Functional requirements | "System shall calculate dose based on patient weight" | ✅ | ✅ | ✅ |
| Performance requirements | "Response time < 100ms for alarm" | ✅ | ✅ | ✅ |
| Interface requirements | "UART communication at 115200 baud with sensor" | ✅ | ✅ | ✅ |
| Safety requirements (from risk management) | "SW shall detect sensor failure within 500ms" | ✅ | ✅ | ✅ |
| Security requirements | "All patient data encrypted at rest (AES-256)" | ✅ | ✅ | ✅ |
| Usability requirements | "Alarm audio > 70dB within 1m" | ✅ | ✅ | ✅ |
| Requirements for SOUP | "Use validated version of FreeRTOS 10.4.3" | ✅ | ✅ | ✅ |

#### 5.3 Software Architecture (Class B & C)

| Required Documentation | Class B | Class C |
|-----------------------|:-------:|:-------:|
| Software items (components/modules) identified | ✅ | ✅ |
| Interfaces between software items | ✅ | ✅ |
| Interfaces to external systems | ✅ | ✅ |
| SOUP items identified and documented | ✅ | ✅ |
| Functional and performance requirements allocated to SW items | ✅ | ✅ |
| Safety class assigned to each SW item | ✅ | ✅ |
| Architecture supports safety class segregation | ✅ | ✅ |
| Architecture verified (review, analysis) | ✅ | ✅ |

#### 5.4 Software Detailed Design (Class C Only)

| Requirement | Content |
|-------------|---------|
| Detailed design for each software unit | Algorithms; data structures; interfaces between units |
| Interfaces to SOUP | How each SOUP component is used; API contracts |
| Verification of detailed design | Review; traceability to requirements |

#### 5.5 Unit Implementation and Verification

| Activity | Class A | Class B | Class C |
|----------|:-------:|:-------:|:-------:|
| Write code per detailed design | ✅ | ✅ | ✅ |
| Coding standards defined and followed | — | ✅ | ✅ |
| Unit verification (testing) | — | — | ✅ |
| Code review | — | Recommended | ✅ |
| Static analysis | — | Recommended | ✅ |

#### 5.6/5.7 Integration Testing & System Testing

| Activity | All Classes |
|----------|:-----------:|
| Integration testing per plan | ✅ |
| Integration test coverage of interfaces | ✅ |
| System testing against requirements | ✅ |
| System test coverage traceable to requirements | ✅ |
| Test results documented | ✅ |
| Anomalies documented and resolved/accepted | ✅ |

### 3.2 SOUP (Software of Unknown Provenance)

| Requirement | Description | Class |
|-------------|-------------|:-----:|
| Identify SOUP items | List all third-party/open-source components | A/B/C |
| Document SOUP title, manufacturer, version | Configuration identification | A/B/C |
| Document functional requirements for SOUP | Why is it included; what does it do? | B/C |
| Document performance requirements for SOUP | Timing, memory, interface constraints | B/C |
| Document hardware/software requirements | Platform dependencies | B/C |
| Identify known anomalies of SOUP | Known bugs; errata; workarounds | B/C |
| Evaluate SOUP anomalies for acceptable risk | Are known bugs acceptable for our use case? | B/C |
| Monitor SOUP for new anomalies (maintenance) | Ongoing vulnerability/bug monitoring | A/B/C |

**SOUP Examples in Medical Devices:**

| SOUP Component | Typical Use | Risk Consideration |
|---------------|-------------|-------------------|
| FreeRTOS / Zephyr RTOS | Real-time scheduling | Task scheduling failure → timing-critical safety functions |
| Linux kernel | Platform OS | Kernel crash → device failure |
| OpenSSL / mbedTLS | Cryptographic operations | Vulnerability → data breach; incorrect crypto → integrity failure |
| SQLite | Local data storage | Corruption → data loss; injection if inputs not sanitized |
| Qt / LVGL | UI framework | UI freeze → alarm not displayed |
| TensorFlow Lite | AI inference | Model error → misdiagnosis |
| DICOM libraries | Medical imaging | Parsing error → wrong image displayed |
| Communication stacks (BLE, TCP/IP) | Connectivity | Protocol failure → loss of data; unauthorized access |

### 3.3 Software Configuration Management (Clause 8)

| Requirement | Implementation |
|-------------|---------------|
| Identify configuration items | Source code, documents, SOUP, tools, build scripts, test cases |
| Version control all items | Git (code); DMS (documents); artifact repository (binaries) |
| Change control | Change request → impact analysis → approval → implementation → verification |
| Reproducible builds | Build from tagged source; documented build environment; deterministic output |
| Traceability | Requirement → design → code → test → defect (bidirectional) |
| Status accounting | Track status of all changes (open, in-progress, verified, released) |
| Release management | Defined release process; release notes; archived release package |

---

## Chapter 4 — Implementation Guide

### 4.1 IEC 62304 V-Model Implementation

```mermaid
graph LR
    subgraph "Left Side: Definition"
        SDP[SW Development Plan<br/>Clause 5.1]
        SRS[SW Requirements<br/>Clause 5.2<br/>• Functional<br/>• Performance<br/>• Safety<br/>• Security]
        ARCH[SW Architecture<br/>Clause 5.3<br/>• Components<br/>• Interfaces<br/>• SOUP<br/>• Safety class allocation]
        DD[Detailed Design<br/>Clause 5.4<br/>• Algorithms<br/>• Data structures<br/>• Unit interfaces<br/>(Class C only)]
    end
    
    subgraph "Bottom: Implementation"
        CODE[Unit Implementation<br/>Clause 5.5<br/>• Coding standards<br/>• Code review<br/>• Static analysis]
    end
    
    subgraph "Right Side: Verification"
        UV[Unit Verification<br/>Clause 5.5<br/>• Unit tests (Class C)<br/>• Coverage analysis]
        IT[Integration Testing<br/>Clause 5.6<br/>• Interface testing<br/>• Component interaction]
        ST[System Testing<br/>Clause 5.7<br/>• Requirements-based testing<br/>• Traceability to SRS]
        REL[SW Release<br/>Clause 5.8<br/>• Release package<br/>• Known anomalies<br/>• Release approval]
    end
    
    SDP --> SRS --> ARCH --> DD --> CODE
    CODE --> UV --> IT --> ST --> REL
    
    DD -.->|"Verified by"| UV
    ARCH -.->|"Verified by"| IT
    SRS -.->|"Verified by"| ST
```

### 4.2 Documentation Deliverables per Class

| Document | Class A | Class B | Class C |
|----------|:-------:|:-------:|:-------:|
| Software Development Plan | ✅ | ✅ | ✅ |
| Software Requirements Specification | ✅ | ✅ | ✅ |
| Software Architecture Document | — | ✅ | ✅ |
| Software Detailed Design Document | — | — | ✅ |
| Coding Standards | — | ✅ | ✅ |
| Unit Test Plan and Results | — | — | ✅ |
| Integration Test Plan and Results | ✅ | ✅ | ✅ |
| System Test Plan and Results | ✅ | ✅ | ✅ |
| Traceability Matrix | ✅ (requirements → tests) | ✅ (+ architecture) | ✅ (+ detailed design + code) |
| SOUP List | ✅ | ✅ | ✅ |
| Software Configuration Management Records | ✅ | ✅ | ✅ |
| Software Release Record | ✅ | ✅ | ✅ |
| Software Maintenance Plan | ✅ | ✅ | ✅ |
| Software Anomaly List (known residual) | ✅ | ✅ | ✅ |
| Risk Management (SW contribution) | ✅ | ✅ | ✅ |

### 4.3 Agile Implementation of IEC 62304

| Agile Concept | IEC 62304 Mapping | How to Comply |
|--------------|------------------|---------------|
| Sprint Planning | 5.1 (Development Planning) | Maintain living SW Development Plan; update per sprint as scope evolves |
| User Stories | 5.2 (Requirements) | Formal requirements exist separately; user stories decompose them; traceability maintained |
| Sprint Review | 5.3/5.4 (Design Review) | Document architectural decisions made during reviews; attendance record |
| Definition of Done | 5.5-5.7 (Verification) | DoD includes: code reviewed, unit tests pass (Class C), static analysis clean, requirements traced |
| Backlog Refinement | 5.2 (Requirements evolution) | Changes go through change control; risk impact assessed |
| Continuous Integration | 5.6 (Integration Testing) | Automated integration tests run on every merge; results recorded |
| Release Sprint | 5.8 (Release) | Formal release process: all tests pass; anomaly list reviewed; release approval documented |
| Retrospective | Clause 6 (Maintenance) / Improvement | Feed process improvements into SW Development Plan updates |

### 4.4 Tool Chain for IEC 62304 Compliance

| Category | Tools | Purpose |
|----------|-------|---------|
| Requirements Management | Jama Connect, Polarion, DOORS, Helix RM | Requirements with traceability to design/test |
| Version Control | Git (GitLab/GitHub/Bitbucket) | Source code; branching; tags for releases |
| CI/CD | Jenkins, GitLab CI, GitHub Actions | Automated build; test; static analysis |
| Static Analysis | Coverity, PC-lint, Polyspace, SonarQube | Coding standard compliance; defect detection |
| Unit Testing | Unity (C), Google Test (C++), pytest, JUnit | Unit verification (Class C) |
| Code Coverage | gcov, Bullseye, LDRA, Parasoft | Coverage measurement (statement, branch, MC/DC) |
| Integration/System Testing | Robot Framework, pytest, custom test harnesses | Automated integration and system tests |
| Defect Tracking | Jira, Azure DevOps, Redmine | Anomaly tracking; problem resolution |
| Document Management | Greenlight Guru, MasterControl, Confluence (with controls) | Controlled documents; approvals |
| SBOM Generation | CycloneDX, SPDX, Syft | SOUP inventory; vulnerability tracking |

---

## Chapter 5 — Verification and Validation

### 5.1 Testing Strategy by Safety Class

| Test Level | Class A | Class B | Class C |
|-----------|:-------:|:-------:|:-------:|
| Unit Testing | Not required | Recommended | **Required** |
| Code Coverage Target | N/A | N/A (but recommended) | Statement: 100%; Branch: recommended; MC/DC: safety-critical functions |
| Integration Testing | Required | Required | Required (more rigorous) |
| System Testing | Required | Required | Required (exhaustive) |
| Regression Testing | Required for changes | Required | Required (full regression) |
| Static Analysis | Not required | Required (coding standards) | Required (MISRA or equivalent) |
| Code Review | Not required | Recommended | **Required** (all safety-critical code) |
| Formal Methods | Not required | Not required | Considered for highest-risk components |

### 5.2 Traceability Requirements

```mermaid
graph LR
    REQ[Requirements<br/>(SRS)] --> ARCH_T[Architecture<br/>(SAD)]
    ARCH_T --> DD_T[Detailed Design<br/>(SDD)]
    DD_T --> CODE_T[Source Code<br/>(Units)]
    CODE_T --> UT[Unit Tests]
    ARCH_T --> IT_T[Integration Tests]
    REQ --> ST_T[System Tests]
    
    REQ -.->|"Forward trace"| ST_T
    ST_T -.->|"Backward trace"| REQ
    
    RISK[Risk Controls<br/>(ISO 14971)] --> REQ
    RISK --> ST_T
```

**Traceability Matrix Example:**

| Requirement ID | Architecture Component | Detailed Design | Code Module | Unit Test | Integration Test | System Test | Risk Control |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| SRS-001 | COMP-A | DD-A.1 | sensor_read.c | UT-A-001 | IT-001 | ST-001 | RC-003 |
| SRS-002 | COMP-B | DD-B.3 | alarm_mgr.c | UT-B-003 | IT-002 | ST-002 | RC-007 |
| SRS-003 | COMP-A, COMP-C | DD-A.2, DD-C.1 | comm_handler.c | UT-A-002, UT-C-001 | IT-003 | ST-003 | — |

---

## Chapter 6 — Regional & Regulatory Context

### 6.1 IEC 62304 Recognition by Regulators

| Regulator | Recognition | Additional Requirements |
|-----------|:-----------:|------------------------|
| FDA (US) | Recognized consensus standard | + FDA SW Validation Guidance; + Cybersecurity Guidance 2023; + SBOM |
| EU (MDR) | Harmonized standard (EN 62304) | + GSPR 17 (cybersecurity); + MDCG guidance on SW qualification |
| Health Canada | Recognized | + MDSAP software chapter |
| TGA (Australia) | Recognized | + TGA SaMD guidance |
| PMDA (Japan) | Recognized (JIS T 62304) | + PMDA specific guidance on SW |
| NMPA (China) | Referenced (GB/T 25000 series also used) | + Chinese-specific software technical review guidance |

### 6.2 FDA Software Level of Concern (Legacy) vs. IEC 62304

| FDA Level of Concern (legacy) | IEC 62304 Class | Approximate Mapping |
|:---:|:---:|---|
| Minor | A | SW failure not expected to cause injury |
| Moderate | B | SW failure could cause non-serious injury |
| Major | C | SW failure could cause death or serious injury |

*Note: FDA has moved toward referencing IEC 62304 safety classes directly rather than their legacy "Level of Concern" framework.*

---

## Chapter 7 — Comparison

### 7.1 IEC 62304 vs. DO-178C (Aerospace) vs. Automotive SPICE

| Dimension | IEC 62304 (Medical) | DO-178C (Aerospace) | Automotive SPICE (Auto) |
|-----------|:---:|:---:|:---:|
| Domain | Medical devices | Airborne systems | Automotive ECUs |
| Safety levels | 3 classes (A, B, C) | 5 DALs (A-E) | ASIL (A-D) via ISO 26262 |
| Rigor at highest level | High (unit test; code review; traceability) | **Very high** (MC/DC coverage; formal methods; tool qualification) | High (unit test; back-to-back; tool qualification) |
| Code coverage (highest) | Statement coverage (branch recommended) | **MC/DC required** (DAL A) | Statement + branch (MC/DC for ASIL D) |
| Certification body | Notified Body (EU) / FDA review (US) | DER (Designated Engineering Representative) | Assessor (VDA/ASPICE) |
| Tool qualification | Required (validated tools) | **DO-330** (tool qualification standard) | Tool confidence level (ISO 26262 Part 8) |
| SOUP/COTS | Extensive SOUP guidance (Amendment 1) | Limited; prefer qualified SW | AUTOSAR; qualified libraries |
| Cost of compliance | Moderate | **Very high** | Moderate-high |
| Lifecycle model | Flexible (V-model, agile allowed) | Primarily V-model/waterfall | V-model (Automotive SPICE process reference model) |
| Cybersecurity | Referenced (FDA guidance; IEC 81001-5-1) | DO-326A (security supplement) | ISO/SAE 21434 |

### 7.2 IEC 62304 vs. IEC 82304-1

| Dimension | IEC 62304 | IEC 82304-1 |
|-----------|-----------|-------------|
| Scope | Software **lifecycle processes** | Health software **product** requirements |
| Applies to | SW embedded in device OR standalone SaMD | Standalone health software products |
| Focus | HOW to develop/maintain SW | WHAT the SW product must deliver (safety, performance, security) |
| Relationship | Process standard (referenced BY IEC 82304) | Product standard (REFERENCES IEC 62304 for development process) |
| Content | Development; maintenance; risk; config; problem resolution | Labeling; instructions; safety; security; deployment; decommissioning |
| When to use | All medical device software | Standalone health apps; SaMD; clinical software |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 Safety Classification Decision Flow

```mermaid
graph TB
    START[Software System]
    
    Q1{Can the software<br/>contribute to a<br/>hazardous situation?}
    START --> Q1
    
    Q1 -->|"No"| CLASS_A[CLASS A<br/>No injury possible<br/>━━━━━━━━━━━━━<br/>Minimal documentation<br/>Integration + system test<br/>No architecture required]
    
    Q1 -->|"Yes"| Q2{Is the hazardous situation<br/>that SW contributes to<br/>able to result in<br/>SERIOUS INJURY or DEATH?}
    
    Q2 -->|"No (non-serious injury)"| CLASS_B[CLASS B<br/>Non-serious injury possible<br/>━━━━━━━━━━━━━<br/>Architecture required<br/>Coding standards<br/>Integration + system test<br/>Traceability]
    
    Q2 -->|"Yes (serious injury/death)"| Q3{Can hardware/external<br/>risk control measures<br/>reduce the risk to<br/>acceptable levels?}
    
    Q3 -->|"Yes (and properly validated)"| CLASS_B
    Q3 -->|"No (SW is sole/primary<br/>risk control)"| CLASS_C[CLASS C<br/>Death or serious injury possible<br/>━━━━━━━━━━━━━<br/>Full documentation<br/>Detailed design<br/>Unit testing (100% statement)<br/>Code review<br/>Static analysis<br/>Full traceability]
```

### 8.2 Software Maintenance Process (Clause 6)

```mermaid
graph TB
    subgraph "Inputs"
        PROB[Problem Reports<br/>• Customer complaints<br/>• Internal bugs<br/>• Field failures<br/>• SOUP vulnerabilities]
        MOD[Modification Requests<br/>• Feature enhancements<br/>• Regulatory changes<br/>• Standard updates<br/>• Cybersecurity patches]
    end
    
    subgraph "Analysis (6.2)"
        ANAL[Problem/Modification Analysis<br/>• Impact on safety<br/>• Impact on existing risk controls<br/>• Regression risk<br/>• Safety class re-evaluation<br/>• Criticality assessment]
    end
    
    subgraph "Implementation (6.3)"
        IMPL_M[Implementation<br/>• Use development process<br/>  (Clause 5) appropriate<br/>  to modification scope<br/>• Maintain traceability<br/>• Update documentation]
    end
    
    subgraph "Verification (6.4)"
        VER_M[Verification<br/>• Unit test (if Class C modification)<br/>• Integration test<br/>• System test (regression)<br/>• Traceability updated<br/>• Risk management updated]
    end
    
    subgraph "Release (6.5)"
        REL_M[Release<br/>• Release record updated<br/>• Known anomaly list updated<br/>• SOUP list updated<br/>• Release approval<br/>• Customer notification]
    end
    
    PROB --> ANAL
    MOD --> ANAL
    ANAL --> IMPL_M --> VER_M --> REL_M
```

---

## Chapter 9 — Case Studies

### 9.1 Class C Software — Insulin Pump Controller

| Aspect | Detail |
|--------|--------|
| Device | Insulin pump with closed-loop algorithm (artificial pancreas); Class C software (dose calculation error → hypoglycemia → death) |
| Architecture | (1) **Sensor Interface Module** (Class B): reads CGM data; validates signal. (2) **Algorithm Module** (Class C): calculates insulin dose; PID controller with safety constraints. (3) **Pump Control Module** (Class C): drives motor; monitors delivery. (4) **UI Module** (Class A): display; user input (non-safety; all safety-critical actions confirmed by Algorithm Module). (5) **Safety Monitor** (Class C): independent watchdog; checks dose limits; detects sensor failure. |
| SOUP | FreeRTOS 10.4.6 (RTOS); mbedTLS 3.4.0 (BLE encryption); custom math library (verified); STM32 HAL (hardware abstraction). Each with documented functional requirements, known anomalies evaluated. |
| Class C Activities | (1) **Detailed Design**: every function in Algorithm Module and Pump Control documented (pseudocode + data flow + state machines). (2) **Unit Testing**: 100% statement coverage; 95% branch coverage for safety-critical modules; 400+ unit tests. (3) **Code Review**: all Algorithm/Pump/Safety code peer-reviewed (2 reviewers required); static analysis with PC-lint (MISRA C:2012 compliance). (4) **Integration Testing**: 150+ integration tests; hardware-in-the-loop testing for pump motor control. (5) **System Testing**: 1,200 system test cases; includes 200 fault-injection tests (sensor failure, communication loss, power failure, overflow conditions). |
| Risk Controls in Software | (1) Maximum bolus limit (hardware AND software enforced independently). (2) Dose rate limiting (maximum units/hour). (3) Sensor validity checking (signal quality; rate-of-change limits). (4) Redundant dose calculation (Algorithm Module calculated; Safety Monitor independently verifies). (5) Failsafe: stop delivery if any anomaly detected (safe state = no insulin delivery). |
| Traceability | 847 requirements → 847 design elements → 2,400 code units → 2,100 test cases. Full bidirectional traceability in Jama Connect. |
| Outcome | FDA PMA approved; EU MDR Class III CE marked. Zero field safety events in 3 years post-market. |

### 9.2 Class A→B Reclassification Error

| Aspect | Detail |
|--------|--------|
| Device | Patient data viewer (hospital information display); initially classified as Class A (display only; no clinical decisions) |
| The problem | Software was classified Class A based on intended use: "displays patient information for reference." However, in practice, clinicians were using the displayed data to make treatment decisions (e.g., lab values displayed were being acted upon without verification from primary system). |
| Discovery | Post-market: a software bug caused lab results to display in wrong units (mmol/L displayed as mg/dL for glucose). Clinician administered insulin based on displayed value → patient hypoglycemia (serious adverse event). |
| Root cause | (1) **Classification error**: software contributed to hazardous situation because it was the primary information source for clinical decisions (not "reference only"). Should have been Class B (or C if sole information source for life-threatening decisions). (2) **Inadequate hazard analysis**: risk management didn't consider foreseeable misuse (clinicians treating displayed values as authoritative). (3) **As Class A**: no architecture document; no coding standards; no formal verification beyond basic system testing; unit conversion logic was never independently verified. |
| Corrective actions | (1) Reclassified software to **Class B**. (2) Performed full architectural review; identified 3 additional components that needed higher safety classification. (3) Implemented coding standards (MISRA C subset). (4) Added unit tests for all data transformation functions (unit conversions, formatting, rounding). (5) Integration tests for data pathway from HL7 interface to display. (6) Updated risk management file: added hazard "incorrect data display leading to incorrect clinical decision." (7) CAPA filed; FDA MDR submitted for serious adverse event. |
| Industry lesson | "Always classify based on REASONABLY FORESEEABLE use, not just intended use. If clinicians will use your display to make decisions, your software is NOT Class A." |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| IEC 62304 Edition 2 | 2025-2027 | Major revision: cybersecurity integration (IEC 81001-5-1); AI/ML lifecycle; agile recognition; cloud/SaaS; modernization |
| AI/ML software lifecycle | Now-2026 | PCCP (FDA); continuous learning; data lifecycle management; model validation |
| Cybersecurity integration (IEC 81001-5-1) | 2022+ (published) | Security requirements in SW lifecycle; threat modeling; SBOM; vulnerability management |
| DevSecOps for medical devices | Now | CI/CD with compliance automation; automated traceability; continuous verification |
| Cloud-deployed medical SW | Now-2025 | SaaS medical devices; multi-tenant; update management; availability requirements |
| Containerized medical SW | Now | Docker/Kubernetes in medical; container as SW item; SOUP management for base images |
| Model-based development | Now | Auto-code generation from models (Simulink → C); model verification vs. code verification |
| SBOM as regulatory requirement | 2023+ | FDA mandatory; EU CRA; machine-readable SOUP inventory; vulnerability monitoring |
| Digital twins for verification | 2025+ | Simulated device environments for system testing; reduced physical testing |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What are the three software safety classes in IEC 62304, and how do they affect development requirements?  
**A:** IEC 62304 defines three safety classes based on the potential harm from software failure: **Class A** — no injury or damage to health possible. Minimal requirements: development plan, requirements, integration testing, system testing, configuration management. NO architecture document required; NO unit testing required; NO detailed design required. Example: non-clinical data display; administrative software. **Class B** — non-serious injury possible. Moderate requirements: everything in Class A PLUS software architecture document, coding standards, SOUP evaluation (functional and performance requirements), architectural verification. Still no unit testing required (but recommended). Example: monitoring software with nurse override for alarms. **Class C** — death or serious injury possible. Maximum requirements: everything in Class A and B PLUS detailed design documentation, unit testing with coverage analysis, code review, static analysis, full traceability (requirements → architecture → detailed design → code → unit tests → integration tests → system tests). Example: insulin pump dose calculation; ventilator control; radiation therapy planning. Key: classification is based on the software's CONTRIBUTION to a hazardous situation, not the device class. A Class III medical device might have Class A software (if the software cannot contribute to harm).

**Q2:** What is SOUP and why is it important in IEC 62304?  
**A:** SOUP = **Software of Unknown Provenance**. It's any software component that was not developed under IEC 62304 control — meaning you don't have full visibility into its development process. This includes: open-source libraries (FreeRTOS, OpenSSL, Linux kernel), commercial off-the-shelf software (RTOS vendors, database engines), and legacy in-house code developed before IEC 62304 adoption. SOUP is important because: (1) **You inherit its risk** — if SOUP has a bug, your medical device has a bug. (2) **You must manage it**: identify all SOUP items; document version, manufacturer, purpose; document functional and performance requirements you rely on; evaluate known anomalies (published bugs/CVEs) for impact on your device; monitor for new anomalies throughout device lifetime (Amendment 1 requirement). (3) **Cybersecurity**: SOUP vulnerabilities (CVEs) are the #1 attack vector for medical devices. FDA now requires SBOM (Software Bill of Materials) listing all SOUP. You must have a process to receive vulnerability notifications and assess/patch them. (4) **You cannot just "use the latest version"** — each SOUP version must be verified in your system context. Updating SOUP is a change that requires regression testing per your maintenance process (Clause 6).

### Tier 2: Mid-Level

**Q3:** How would you architect a medical device software system to allow mixed safety classes (Class A UI with Class C safety-critical control)?  
**A:** [Full answer covers: architectural segregation principle — separate SW items with different safety classes through defined boundaries. (1) Memory protection: use MPU/MMU to isolate Class C from Class A (Class A crash cannot corrupt Class C memory). (2) Temporal separation: Class C tasks have guaranteed timing (higher RTOS priority; time partitioning). (3) Communication interfaces: well-defined APIs between safety classes; Class C validates ALL inputs from Class A (never trust lower-class data without checking). (4) Independent compilation: Class C and Class A compiled and linked separately where possible. (5) Watchdog: independent hardware watchdog monitored by Class C safety monitor (if Class A hangs, Class C continues). (6) Data flow analysis: document all data paths crossing class boundaries; each crossing point has validation. (7) Verification: Class A tested to Class A requirements; Class C tested to Class C requirements; integration tests specifically verify class boundary behavior (fault injection at boundaries). (8) Documentation: architecture document explicitly identifies class boundaries, protection mechanisms, and rationale for class allocation. This architecture allows the UI team to iterate quickly (Class A = minimal documentation) while safety-critical control code has full Class C rigor.]

### Tier 3: Senior

**Q4:** Your company wants to adopt continuous deployment for a Class B SaMD cloud application. How do you maintain IEC 62304 compliance with frequent releases?  
**A:** [Full answer covers the tension between continuous deployment and traditional medical device change control, and how to resolve it: (1) Establish validated CI/CD pipeline as the "manufacturing process" for software — pipeline itself is validated per 62304 and ISO 13485. (2) Automated regression: comprehensive automated test suite that runs on every commit; system tests trace to requirements; test results are automatically recorded as objective evidence. (3) Pre-defined change categories: "Type 1" changes (bug fixes, minor features within existing risk envelope) → automated release if all tests pass + automated risk assessment (pre-defined criteria). "Type 2" changes (new features, architecture changes, new SOUP, safety-impacting) → manual review gate required. (4) Living documentation: requirements and traceability maintained in code-adjacent tools (e.g., requirements as code; traceability embedded in test annotations). Architecture document updated as part of change (not a separate step). (5) Risk management integration: each PR tagged with risk impact; automated checks against risk controls; changes touching risk controls require explicit risk management review. (6) SOUP monitoring: automated CVE scanning in pipeline (Dependabot/Snyk); SOUP updates treated as Type 1 if no API changes + all tests pass. (7) Release records: auto-generated from pipeline artifacts (build hash, test results, SOUP versions, anomaly status). (8) Regulatory strategy: agree approach with Notified Body in advance; demonstrate pipeline validation evidence; periodic NB audits review pipeline effectiveness rather than individual releases. This is described in AAMI TIR45 and increasingly accepted by regulators for Class A/B SaMD.]

---

## Chapter 12 — Cheat Sheet & Quick Reference

### Safety Class Quick Decision

```
Can SW contribute to hazardous situation?
  NO  → Class A (minimal docs; integration + system test only)
  YES → Can it cause SERIOUS INJURY or DEATH?
    NO (non-serious only)  → Class B (architecture; coding standards; moderate rigor)
    YES → Can hardware controls reduce to acceptable?
      YES (and validated) → Class B
      NO (SW is primary control) → Class C (full rigor; unit test; code review; detailed design)
```

### Class C Mandatory Activities

```
✅ Software Development Plan
✅ Software Requirements Specification
✅ Software Architecture Document
✅ Software Detailed Design Document
✅ Coding Standards (MISRA C or equivalent)
✅ Unit Testing (100% statement coverage target)
✅ Code Review (all safety-critical code)
✅ Static Analysis
✅ Integration Testing
✅ System Testing
✅ Full Traceability (requirements ↔ design ↔ code ↔ tests)
✅ SOUP Management (identify, evaluate, monitor)
✅ Configuration Management
✅ Problem Resolution Process
✅ Risk Management (SW contribution to hazardous situations)
```

### SOUP Management Checklist

```
For each SOUP item:
□ Identified (name, manufacturer/source, version)
□ Functional requirements documented (what we need it to do)
□ Performance requirements documented (timing, memory, etc.)
□ Hardware/software requirements documented (platform dependencies)
□ Known anomalies evaluated (CVEs, errata → acceptable for our use?)
□ Monitoring process active (new vulnerabilities, updates, EOL)
□ Integration verified (works correctly in our system context)
□ Included in SBOM
```

### Key Deliverables Summary

```
PLAN:     Software Development Plan (Clause 5.1)
REQUIRE:  Software Requirements Specification (Clause 5.2)
DESIGN:   Software Architecture Document (Clause 5.3) [B/C]
          Software Detailed Design Document (Clause 5.4) [C only]
BUILD:    Source code + coding standards evidence [B/C]
TEST:     Unit Test Results [C]; Integration Test Results [all]; System Test Results [all]
TRACE:    Requirements Traceability Matrix [all]
SOUP:     SOUP List with evaluation [all]
CONFIG:   SCM records (version control, change control) [all]
RELEASE:  Software Release Record [all]
MAINTAIN: Maintenance Plan + Problem Resolution Records [all]
RISK:     Software risk management records (input to ISO 14971 file) [all]
```

### IEC 62304 Process Clause Map

```
Clause 4: General Requirements (QMS context; risk; classification)
Clause 5: Software DEVELOPMENT Process
  5.1 Planning | 5.2 Requirements | 5.3 Architecture
  5.4 Detailed Design | 5.5 Unit Implementation/Verification
  5.6 Integration Testing | 5.7 System Testing | 5.8 Release
Clause 6: Software MAINTENANCE Process
  6.1 Plan | 6.2 Problem Analysis | 6.3 Implementation
  6.4 Verification | 6.5 Release
Clause 7: Software RISK MANAGEMENT Process
Clause 8: Software CONFIGURATION MANAGEMENT Process
Clause 9: Software PROBLEM RESOLUTION Process
```

---

*End of Document — 02_IEC_62304_Software_Lifecycle.md*
