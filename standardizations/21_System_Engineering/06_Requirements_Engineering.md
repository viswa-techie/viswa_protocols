# Requirements Engineering — ISO/IEC/IEEE 29148

**Standard:** ISO/IEC/IEEE 29148:2018 — Systems and Software Engineering — Life Cycle Processes — Requirements Engineering  
**Related:** ISO/IEC/IEEE 15288, ISO/IEC/IEEE 12207, IEEE 830 (superseded), INCOSE Guide for Writing Requirements  
**SDO:** ISO, IEC, IEEE (joint)  
**Tools:** IBM DOORS, Siemens Polarion, Jama Connect, Dassault Reqtify, Helix RM  
**Audience:** Requirements engineers, systems engineers, business analysts, project managers, V&V engineers  
**Prerequisites:** Basic SE lifecycle; understanding of stakeholder concepts; familiarity with V-model

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Milestone |
|------|-----------|
| 1984 | IEEE 830 — Guide for Software Requirements Specifications (first formal SRS standard) |
| 1993 | IEEE 1233 — Guide for Developing System Requirements Specifications |
| 1998 | IEEE 830-1998 — Recommended Practice for SRS (widely adopted; de facto standard for 20 years) |
| 2002 | ISO/IEC 15288 — introduces requirements processes at system level |
| 2011 | **ISO/IEC/IEEE 29148:2011** — combines/supersedes IEEE 830, IEEE 1233, IEEE 1362 (ConOps) |
| 2015 | INCOSE Guide for Writing Requirements (GWR) — practical guidance |
| 2018 | **ISO/IEC/IEEE 29148:2018** — current edition (aligned with 15288:2015 and 12207:2017) |
| 2020 | INCOSE GWR updated; requirements patterns emerging |
| 2024 | AI-assisted requirements analysis tools appear; SysML v2 requirements as model elements |

### 1.2 Why a Requirements Standard?

**The problem:** 40-60% of project failures trace to requirements issues (Standish Group; NASA studies):
- Incomplete requirements
- Ambiguous requirements
- Inconsistent requirements
- Requirements volatility (uncontrolled change)
- Gold-plating (unnecessary requirements)
- Missing stakeholder needs

**The solution:** ISO/IEC/IEEE 29148 defines:
1. **Processes** for requirements engineering (elicitation, analysis, specification, validation, management)
2. **Characteristics** of good requirements (necessary, unambiguous, verifiable, etc.)
3. **Templates** for requirements documents (StRS, SyRS, SRS)
4. **Guidance** on requirements management throughout lifecycle

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 ISO/IEC/IEEE 29148:2018 Document Structure

| Clause | Content |
|:------:|---------|
| 1-4 | Scope, references, terms, concepts |
| 5 | **Stakeholder requirements** — process, characteristics, content |
| 6 | **System requirements** — process, characteristics, content |
| 7 | **Software requirements** — process, characteristics, content (SRS template) |
| 8 | Requirements management | 
| Annexes | Templates (StRS, SyRS, SRS); requirement characteristics; examples |

### 2.2 Requirements Hierarchy

```mermaid
graph TB
    subgraph "Requirements Hierarchy (ISO 29148)"
        BRD[Business Requirements<br/>━━━━━━━━━━━<br/>• WHY the system is needed<br/>• Business objectives<br/>• Success criteria<br/>• Not in 29148 directly<br/>  (covered by 15288 Business Analysis)]
        
        STR[Stakeholder Requirements (StRS)<br/>━━━━━━━━━━━<br/>• WHAT stakeholders need<br/>• User perspective<br/>• Operational scenarios<br/>• Constraints (regulatory, environmental)<br/>━━━━━━━━━━━<br/>ISO 29148 Clause 5]
        
        SYR[System Requirements (SyRS)<br/>━━━━━━━━━━━<br/>• WHAT the system must do<br/>• Technical; unambiguous; verifiable<br/>• Functional + non-functional<br/>• Interface requirements<br/>━━━━━━━━━━━<br/>ISO 29148 Clause 6]
        
        SWR[Software Requirements (SRS)<br/>━━━━━━━━━━━<br/>• WHAT the software must do<br/>• Allocated from system requirements<br/>• Detailed; implementation-free<br/>• Each requirement verifiable<br/>━━━━━━━━━━━<br/>ISO 29148 Clause 7]
    end
    
    BRD -->|"derives"| STR -->|"transforms into"| SYR -->|"allocates to SW"| SWR
```

---

## Chapter 3 — Requirements Characteristics (Quality Attributes)

### 3.1 Individual Requirement Characteristics

| Characteristic | Definition | Test | Bad Example | Good Example |
|:-:|---|---|---|---|
| **Necessary** | Removal would cause deficiency | "Is this needed for system mission?" | "System shall be user-friendly" (vague; what does it mean?) | "System shall authenticate users via OAuth 2.0" |
| **Unambiguous** | Single interpretation only | "Can two readers interpret differently?" | "System shall respond quickly" | "System shall respond within 200ms (P95)" |
| **Complete** | Fully stated; no TBD | "Any missing information?" | "System shall support CAN (details TBD)" | "System shall support CAN 2.0B at 500 kbps, extended frame format" |
| **Consistent** | No contradiction with other requirements | "Does any other requirement conflict?" | "Req 1: System shall use AES-128. Req 2: System shall use AES-256" | Choose one; or: "System shall support AES-256-GCM for data at rest" |
| **Verifiable** | Can be proven by test/inspection/analysis | "How would I test this?" | "System shall never crash" (can't prove a negative infinitely) | "System shall achieve MTBF ≥ 10,000 hours" |
| **Traceable** | Origin known; forward link exists | "Where did this come from? What satisfies it?" | Requirement with no ID; no source | REQ-SYS-042 (traces to StR-015; satisfied by Component X) |
| **Feasible** | Achievable with current technology/budget | "Can we actually build this?" | "System shall predict all failures 24h in advance" | "System shall detect 95% of known failure patterns within 1 hour" |
| **Atomic** | Expresses single capability | "Does it contain 'and' combining two things?" | "System shall encrypt data AND compress files" | Split: REQ-1: "...encrypt data"; REQ-2: "...compress files" |

### 3.2 Set-Level Characteristics (Requirements Specification as a whole)

| Characteristic | Definition |
|:-:|---|
| **Complete** | All needed requirements are present; no gaps |
| **Consistent** | No contradictions between requirements |
| **Feasible** | All requirements together are achievable |
| **Bounded** | Scope is defined; what's IN and what's OUT is clear |
| **Modifiable** | Structure allows easy change without breaking consistency |
| **Traceable** | Every requirement has bidirectional traceability (source → requirement → design → test) |

---

## Chapter 4 — Requirements Engineering Process

### 4.1 RE Process Flow

```mermaid
graph TB
    subgraph "Requirements Engineering Process"
        ELICIT[ELICITATION<br/>━━━━━━━━━━━<br/>• Identify stakeholders<br/>• Conduct interviews<br/>• Workshops/brainstorming<br/>• Observe users<br/>• Study existing systems<br/>• Analyze regulations<br/>━━━━━━━━━━━<br/>Output: Raw needs/wishes]
        
        ANALYZE[ANALYSIS<br/>━━━━━━━━━━━<br/>• Classify requirements<br/>  (functional vs. non-functional)<br/>• Detect conflicts<br/>• Prioritize (MoSCoW; Kano)<br/>• Negotiate trade-offs<br/>• Model (use cases; scenarios)<br/>━━━━━━━━━━━<br/>Output: Analyzed requirements]
        
        SPECIFY[SPECIFICATION<br/>━━━━━━━━━━━<br/>• Write requirements<br/>  (formal; unambiguous; verifiable)<br/>• Apply quality attributes<br/>• Structure in document/database<br/>• Assign IDs and attributes<br/>━━━━━━━━━━━<br/>Output: StRS / SyRS / SRS]
        
        VALIDATE[VALIDATION<br/>━━━━━━━━━━━<br/>• Review with stakeholders<br/>• Prototyping<br/>• Walk-throughs<br/>• Model simulation<br/>• Check quality attributes<br/>━━━━━━━━━━━<br/>Output: Validated requirements]
        
        MANAGE[MANAGEMENT<br/>━━━━━━━━━━━<br/>• Version control<br/>• Change control (CR process)<br/>• Traceability maintenance<br/>• Impact analysis<br/>• Status tracking<br/>━━━━━━━━━━━<br/>Continuous throughout lifecycle]
    end
    
    ELICIT --> ANALYZE --> SPECIFY --> VALIDATE
    VALIDATE -->|"feedback"| ELICIT
    MANAGE -.->|"supports all"| ELICIT
    MANAGE -.->|"supports all"| ANALYZE
    MANAGE -.->|"supports all"| SPECIFY
    MANAGE -.->|"supports all"| VALIDATE
```

### 4.2 Elicitation Techniques

| Technique | When to Use | Strengths | Weaknesses |
|:---------:|:-----------:|-----------|------------|
| **Interviews** | Key stakeholders; domain experts | Deep understanding; can follow up | Time-consuming; biased by interviewer |
| **Workshops** | Multiple stakeholders; consensus needed | Generates ideas; resolves conflicts live | Dominant personalities; groupthink |
| **Questionnaires** | Large user base; quantitative data | Scalable; statistical analysis | Low response rate; no follow-up |
| **Observation** | Understanding current processes | Sees actual (not reported) behavior | Time-consuming; observer effect |
| **Document analysis** | Existing systems; regulations; standards | Factual; comprehensive | Outdated docs; missing context |
| **Prototyping** | UI/UX requirements; uncertain needs | Tangible; generates feedback | Expensive; "prototype = product" trap |
| **Use cases/scenarios** | Behavioral requirements; workflows | Concrete; testable | Can miss non-functional aspects |
| **Model analysis** | Domain modeling; information structure | Formal; complete | Requires modeling skills |

### 4.3 Requirement Types

| Type | Description | Example |
|:----:|-------------|---------|
| **Functional** | What the system does (behavior, computation, data handling) | "System shall calculate wheel speed from ABS sensor pulses" |
| **Performance** | Speed, throughput, latency, capacity | "System shall process 1000 CAN messages per second with latency < 5ms" |
| **Interface** | Interactions with external systems | "System shall communicate via Ethernet 100BASE-T1 using SOME/IP" |
| **Security** | Protection, authentication, authorization | "System shall authenticate diagnostic sessions using UDS security access (seed-key)" |
| **Reliability** | Availability, MTBF, fault tolerance | "System shall achieve MTBF ≥ 50,000 hours in operational environment" |
| **Safety** | Hazard mitigation; safe states | "System shall enter safe state (torque = 0) within 50ms of watchdog timeout" |
| **Usability** | User interaction quality | "Driver shall be able to activate cruise control within 2 actions" |
| **Environmental** | Operating conditions | "System shall operate in temperature range -40°C to +125°C" |
| **Constraint** | Restrictions on implementation | "System shall be implemented in C11 compliant with MISRA C:2012" |
| **Regulatory** | Compliance with laws/standards | "System shall comply with UNECE R79 (steering) lateral acceleration limits" |

---

## Chapter 5 — Requirements Management Tools

### 5.1 Tool Comparison

| Tool | Vendor | Domain | Strength | Cost |
|:----:|:------:|:------:|----------|:----:|
| **IBM DOORS Next** | IBM | Aerospace, defense, automotive | De facto standard; powerful traceability; OSLC | $$$$ |
| **IBM DOORS Classic (9.x)** | IBM | Legacy (still widely used) | Fast; flexible; scripting (DXL) | $$$ |
| **Polarion ALM** | Siemens | Automotive, industrial | ALM integration; Polarion LiveDoc | $$$ |
| **Jama Connect** | Jama Software | Medical, aerospace, automotive | Clean UI; test management; review workflow | $$$ |
| **Helix RM** | Perforce (MKS) | Automotive, defense | Workflow; variant management | $$$ |
| **Reqtify** | Dassault | Aerospace, automotive | Traceability analysis; cross-tool linking | $$$ |
| **Codebeamer** | PTC (Intland) | Automotive, medical, industrial | Agile-friendly; safety compliance; ASPICE support | $$$ |
| **Enterprise Architect** | Sparx | General | Modeling + requirements; affordable | $ |
| **Azure DevOps** | Microsoft | IT, general SW | Work items as requirements; cheap; Agile | $ |

### 5.2 IBM DOORS Deep Dive (Industry Standard)

| Feature | Description |
|:-------:|-------------|
| **Modules** | Requirements stored in modules (like databases); each row = one requirement |
| **Attributes** | Custom attributes per requirement: Priority, Status, ASIL, Verification Method, Owner, etc. |
| **Links** | Typed links between requirements: "derives from", "refines", "contradicts", "satisfies", "verifies" |
| **Views** | Configurable table views; filter by attribute; sort; color-code by status |
| **Baselines** | Freeze module at milestone; compare baselines; identify changes |
| **DXL scripting** | DOORS Extension Language; automate: reports, consistency checks, mass-edit, import/export |
| **Traceability** | Navigate link chains: Stakeholder Req → System Req → SW Req → Design → Test Case |
| **ReqIF export** | Standard export for tool-to-tool exchange (OEM ↔ supplier) |
| **Access control** | Per-module access rights; read/write/admin |
| **Impact analysis** | "If I change this requirement, what else is affected?" → navigate suspect links |

### 5.3 Traceability Implementation

```mermaid
graph LR
    subgraph "Bidirectional Traceability Chain"
        SN[Stakeholder Need<br/>SN-001: Driver shall be<br/>warned of collision risk]
        
        SR[System Requirement<br/>SR-042: System shall display<br/>forward collision warning when<br/>TTC < 2.5s]
        
        SWR_T[SW Requirement<br/>SWR-156: AEB ECU shall<br/>calculate TTC from radar<br/>object list every 40ms]
        
        DES[Design Element<br/>DES-089: TTC_Calculator<br/>module in perception SW]
        
        TC[Test Case<br/>TC-312: Verify TTC<br/>calculation accuracy<br/>(target vehicle at 50m, 80km/h)]
        
        TR[Test Result<br/>TR-312-v1.2: PASS<br/>(TTC error < 0.1s)]
    end
    
    SN -->|"derives"| SR -->|"refines"| SWR_T -->|"satisfied by"| DES
    DES -->|"verified by"| TC -->|"result"| TR
```

---

## Chapter 6 — Requirements Specification Templates

### 6.1 SyRS Template (ISO 29148 Clause 6)

| Section | Content |
|:-------:|---------|
| 1. Introduction | Purpose; scope; definitions; references |
| 2. System overview | System context; system boundaries; operational concepts |
| 3. System requirements | 3.1 Functional requirements. 3.2 Usability requirements. 3.3 Performance requirements. 3.4 Interface requirements. 3.5 Operational requirements. 3.6 Modes/states. 3.7 Physical requirements. 3.8 Environmental requirements. 3.9 Security requirements. 3.10 Safety requirements. 3.11 Design constraints. 3.12 SW quality attributes. 3.13 Data requirements |
| 4. Verification requirements | Verification methods per requirement (T/I/A/D) |
| 5. Appendices | Traceability matrix; glossary; assumptions |

### 6.2 Writing Good Requirements (INCOSE Patterns)

**The "shall" statement pattern:**

```
[Condition] [Subject] shall [Action] [Object] [Constraint]
```

**Examples:**

| Pattern Element | Example |
|:---:|---|
| Simple functional | "The system shall calculate vehicle speed from wheel speed sensor inputs." |
| With condition | "When ignition is ON, the system shall initialize CAN communication within 100ms." |
| With constraint | "The system shall store diagnostic data in non-volatile memory for a minimum of 400 power cycles." |
| With performance | "The system shall process radar object lists at a rate of ≥ 25 Hz with latency ≤ 40ms." |
| Negative (forbidden) | "The system shall not transmit personal data without explicit user consent." |

**Common mistakes:**

| Mistake | Example | Fix |
|:-------:|---------|-----|
| Vague/subjective | "System shall be fast" | "System shall respond within 200ms (P95)" |
| Compound (and/or) | "System shall encrypt AND compress" | Split into two requirements |
| Implementation-specific | "System shall use MySQL 8.0" | "System shall persist data in a relational database" (unless constraint) |
| Not verifiable | "System shall be reliable" | "System shall achieve 99.99% availability" |
| Passive voice (unclear actor) | "Data shall be transmitted" | "The ECU shall transmit data via CAN" |
| Premature design | "System shall use a linked list for message queue" | "System shall queue up to 100 messages without data loss" |

---

## Chapter 7 — Comparison: RE Approaches

| Approach | Description | When Used | Strengths | Weaknesses |
|:--------:|-------------|-----------|-----------|------------|
| **Traditional (document-based)** | StRS/SyRS/SRS in DOORS/Word; formal reviews | Safety-critical; regulated; waterfall | Complete; auditable; traceable | Heavy; slow to change; stale copies |
| **Agile (user stories)** | "As a [user], I want [goal], so that [reason]" + acceptance criteria | IT; web/mobile; uncertain requirements | Lightweight; adaptive; user-focused | Less precise; traceability informal; not suitable for safety-critical alone |
| **Model-based (MBSE)** | Requirements as model elements (SysML v2); linked to architecture | Complex systems; MBSE adoption | Consistent; analyzable; change impact automatic | Requires MBSE tools and skills |
| **Goal-oriented (KAOS, i*)** | Requirements derived from goals; why→what→how | Research; complex stakeholder environments | Captures rationale; handles conflicts | Academic; rarely used in industry |
| **Formal methods** | Requirements in formal notation (Z, B, TLA+, Alloy) | Ultra-critical (nuclear, rail signaling) | Mathematically precise; provable | Steep learning curve; expensive; limited domain |

---

## Chapter 8 — Architecture Diagrams

### 8.1 Requirements Engineering in Context

```mermaid
graph TB
    subgraph "RE in System Development Context"
        subgraph "Inputs to RE"
            STAKE[Stakeholders<br/>• Users; operators<br/>• Regulators<br/>• Maintainers<br/>• Business owners]
            DOMAIN[Domain Knowledge<br/>• Standards (ISO 26262, etc.)<br/>• Physics; constraints<br/>• Existing systems<br/>• Competitor analysis]
            CONTEXT[System Context<br/>• Operating environment<br/>• External interfaces<br/>• Enabling systems]
        end
        
        subgraph "RE Processes"
            ELICIT_P[Elicitation]
            ANALYZE_P[Analysis]
            SPECIFY_P[Specification]
            VALIDATE_P[Validation]
            MANAGE_P[Management]
        end
        
        subgraph "Outputs of RE"
            StRS_O[Stakeholder Requirements<br/>Specification (StRS)]
            SyRS_O[System Requirements<br/>Specification (SyRS)]
            SRS_O[Software Requirements<br/>Specification (SRS)]
            TRACE[Traceability Matrix]
        end
        
        subgraph "Consumers of Requirements"
            ARCH_C[Architecture (design from req)]
            TEST_C[V&V (test against req)]
            PM_C[Project Mgmt (scope from req)]
            SAFETY_C[Safety (safety req → safety case)]
        end
    end
    
    STAKE --> ELICIT_P
    DOMAIN --> ANALYZE_P
    CONTEXT --> ANALYZE_P
    ELICIT_P --> ANALYZE_P --> SPECIFY_P --> VALIDATE_P
    MANAGE_P -.-> SPECIFY_P
    VALIDATE_P --> StRS_O --> SyRS_O --> SRS_O
    SPECIFY_P --> TRACE
    SyRS_O --> ARCH_C
    SyRS_O --> TEST_C
    SyRS_O --> PM_C
    SRS_O --> SAFETY_C
```

### 8.2 Change Management Process

```mermaid
graph LR
    subgraph "Requirements Change Control"
        CR[Change Request<br/>• Who requests?<br/>• What changes?<br/>• Why?]
        
        IA[Impact Analysis<br/>• Which requirements affected?<br/>• Which design elements?<br/>• Which tests?<br/>• Cost/schedule impact?]
        
        CCB[CCB Review<br/>(Configuration Control Board)<br/>• Approve / Reject / Defer<br/>• Priority assignment<br/>• Resource allocation]
        
        IMPL_CR[Implement Change<br/>• Update requirements<br/>• Update design<br/>• Update tests<br/>• Update traceability]
        
        VER_CR[Verify Change<br/>• Review updated artifacts<br/>• Regression testing<br/>• Traceability check]
        
        CLOSE[Close CR<br/>• Update baseline<br/>• Notify stakeholders<br/>• Record lessons]
    end
    
    CR --> IA --> CCB --> IMPL_CR --> VER_CR --> CLOSE
    CCB -->|"Reject"| CLOSE
```

---

## Chapter 9 — Case Studies

### 9.1 Medical Device: Requirements for an Insulin Pump

| Aspect | Detail |
|--------|--------|
| **Product** | Automated insulin delivery system (Class III; life-sustaining) |
| **RE challenge** | Requirements come from: patients (usability), clinicians (safety limits), regulators (FDA/MDR), algorithm designers (control parameters), manufacturing (producibility) |
| **Stakeholder requirements (examples)** | StR-1: "Patient shall receive accurate insulin dosing to maintain blood glucose 70-180 mg/dL." StR-2: "Clinician shall be able to configure basal rate profiles." StR-3: "System shall comply with IEC 62304 Class C." |
| **System requirements (derived)** | SyR-1: "System shall deliver insulin with accuracy ±5% of programmed dose." SyR-2: "System shall limit maximum bolus to clinician-configured value (default: 25U)." SyR-3: "System shall detect occlusion within 30 minutes and alert user." |
| **Quality issues found in review** | Original: "System shall be safe." → Not verifiable; not atomic. Fixed: decomposed into 47 specific safety requirements (each with ASIL-equivalent classification, verification method, and acceptance criterion). |
| **Traceability** | StR → SyR → SwR → Design → Test → Risk Control Measure. Every safety requirement traces to a hazard in ISO 14971 risk analysis. |
| **Regulatory outcome** | FDA 510(k) approved. Reviewer specifically checked: (1) every hazard has ≥1 safety requirement, (2) every safety requirement has ≥1 verification test, (3) all tests pass. Traceability in DOORS was the primary evidence. |

### 9.2 Automotive: ADAS Feature Requirements

| Aspect | Detail |
|--------|--------|
| **Feature** | Adaptive Cruise Control (ACC) with Stop & Go |
| **Stakeholder needs** | Driver: "I want the car to maintain safe distance without my input." Regulator: "Must comply with UNECE R79 (steering) and national speed limits." Insurance: "Reduce rear-end collisions by 50%." |
| **System requirements (selection)** | SyR-ACC-001: "System shall maintain time-gap to lead vehicle ≥ 1.0s (configurable: 1.0/1.5/2.0/2.5s)." SyR-ACC-002: "System shall decelerate at ≤ 3.5 m/s² during following (comfort limit)." SyR-ACC-003: "System shall bring vehicle to full stop if lead vehicle stops (Stop & Go)." SyR-ACC-004: "System shall resume from stop automatically if stopped < 3s; otherwise require driver confirmation." |
| **Verification methods** | SyR-ACC-001: Test (SIL simulation + vehicle track test). SyR-ACC-002: Test (HIL + vehicle measurement). SyR-ACC-003: Test (track test with stopped target). SyR-ACC-004: Test (vehicle with instrumented driver). |
| **Requirements volatility** | During development: 15% of requirements changed (new regulation UNECE R157 added; customer changed comfort targets; new radar sensor changed detection range requirements). Change managed via CR process in DOORS; impact analysis for each change; regression tests re-run. |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **AI-assisted requirements writing** | 2024-2026 | LLMs suggest requirements from natural language descriptions; auto-check quality (ambiguity, completeness) |
| **Natural language processing (NLP) for RE** | 2023-2027 | Automated quality checking: detect ambiguity, passive voice, missing conditions; tools: QVscribe, DOOORS AI |
| **SysML v2 requirements** | 2024-2027 | Requirements as formal model elements (not just text); machine-verifiable constraints; automated traceability |
| **Continuous requirements** | Now (ongoing) | Requirements evolve with product (DevOps); living requirements in backlog; not frozen documents |
| **Requirements mining** | 2025-2028 | Extract requirements from existing code/tests/field data automatically |
| **Digital twin requirements** | 2026-2030 | Digital twin validates requirements continuously in operation; feeds back need for new/changed requirements |
| **Formal requirements + AI verification** | 2027-2030 | Formal specification + AI theorem prover → prove requirement satisfaction without exhaustive testing |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What are the characteristics of a good requirement according to ISO 29148?

**A:** ISO/IEC/IEEE 29148 defines these quality characteristics for individual requirements:

1. **Necessary** — its removal would cause a deficiency (no gold-plating)
2. **Unambiguous** — only one interpretation possible
3. **Complete** — fully stated; no TBD, no implicit assumptions
4. **Consistent** — doesn't contradict other requirements
5. **Verifiable** — can be proven true by test, inspection, analysis, or demonstration
6. **Traceable** — origin documented; linked to design and test
7. **Feasible** — achievable within constraints (technology, budget, schedule)
8. **Atomic** — states a single thing (no compound statements with "and"/"or")

**Example of applying these:**
- Bad: "The system shall quickly and reliably process data" 
  - Violates: unambiguous ("quickly"?), verifiable (how to measure?), atomic ("quickly AND reliably" = 2 things)
- Good: "The system shall process incoming CAN messages within 5ms of reception (P99)" + "The system shall achieve message processing success rate ≥ 99.999%"
  - Satisfies: specific value (5ms), measurable (timing tool), single concern per requirement, testable

### Tier 2: Mid-Level

**Q2:** Explain the difference between stakeholder requirements, system requirements, and software requirements. Why is this separation important?

**A:**

| Level | Stakeholder Requirements (StRS) | System Requirements (SyRS) | Software Requirements (SRS) |
|:-----:|:---:|:---:|:---:|
| **Perspective** | Stakeholder (user, regulator, operator) | System-as-a-whole (technical; precise) | Software specifically |
| **Language** | Natural language; user's vocabulary | Technical; unambiguous; formal where possible | Technical; SW-specific; implementation-free |
| **Example** | "Driver wants to be warned of collision" | "System shall display visual + audio warning when TTC < 2.5s for objects in ego lane" | "AEB SW shall compute TTC from radar track list using formula $TTC = d / v_{rel}$ every 40ms; output to HMI via SOME/IP" |
| **Verifiable?** | Often not directly (too high-level) | Yes (system test can measure) | Yes (SW test can verify) |
| **Who writes** | Business analyst / requirements engineer + stakeholder | Systems engineer | Software requirements engineer |
| **Who validates** | Stakeholder (is this what you need?) | V&V engineer (does system meet spec?) | SW test team (does SW meet SRS?) |

**Why separation matters:**
1. **Different audiences:** Stakeholders don't understand technical details; engineers need precision
2. **Traceability:** Requirement → design → test at each level; enables impact analysis
3. **Decomposition:** 1 stakeholder need may generate 10 system requirements; 1 system requirement may generate 5 SW requirements
4. **Verification levels:** System requirements → system test; SW requirements → SW test (V-model correspondence)
5. **Change management:** Stakeholder need changes? → trace forward to see which system/SW requirements are impacted
6. **Allocation:** System requirements get allocated to HW or SW (or both); SRS only covers the SW-allocated subset

### Tier 3: Senior

**Q3:** Design the requirements engineering process for a safety-critical automotive platform (ADAS, ASIL D). Cover: tools, traceability strategy, quality assurance, variant management, and supplier requirements exchange.

**A:**

**Requirements architecture:**

```
┌──────────────────────────────────────────────────────────────┐
│ DOORS Next Gen — Requirements Database                        │
├──────────────────────────────────────────────────────────────┤
│ Module: Stakeholder Requirements (StRS)                       │
│   Attributes: ID, Text, Source, Priority, Status              │
│   Links: derives_to → System Requirements                     │
│                                                               │
│ Module: System Requirements (SyRS)                            │
│   Attributes: ID, Text, ASIL, Verification_Method,           │
│     Allocation (SW/HW/Both), Variant_Applicability            │
│   Links: derives_from ← StRS; refines_to → SRS; tested_by → TC │
│                                                               │
│ Module: SW Requirements (SRS)                                 │
│   Attributes: ID, Text, ASIL, Component_Allocation,          │
│     Safety_Mechanism, Verification_Method                     │
│   Links: derives_from ← SyRS; satisfied_by → Design;         │
│     verified_by → Test_Case                                    │
│                                                               │
│ Module: Safety Requirements (from HARA)                        │
│   Attributes: Hazard_ID, Safety_Goal, ASIL, FSR, TSR          │
│   Links: derives_from ← HARA; refines_to → SyRS/SRS          │
│                                                               │
│ Module: Test Cases                                             │
│   Attributes: TC_ID, Type (unit/integration/qualification),   │
│     Status (pass/fail/not-run), Coverage                       │
│   Links: verifies → SRS; result → Test_Results                │
└──────────────────────────────────────────────────────────────┘
```

**Quality assurance strategy:**

| Gate | When | Checks | Tool |
|:----:|:----:|--------|:----:|
| QG1: Requirement quality | After writing each requirement | INCOSE quality rules (no ambiguity, atomic, verifiable, etc.) | QVscribe (NLP tool) + peer review |
| QG2: Completeness | After baseline | All stakeholder needs have ≥1 system req; all system req have ≥1 test; no orphans | DOORS DXL script (automated) |
| QG3: Consistency | After baseline | No contradictions; ASIL consistent (child ≥ parent ASIL); allocation complete | Automated consistency checker |
| QG4: Traceability | Before SWE.6 | Full bidirectional trace: StR → SyR → SWR → Design → Test; no suspect links | DOORS traceability report |
| QG5: Review | At each V-model gate (SRR, PDR, CDR) | Formal inspection (Fagan method); review checklist; defect log | Review tool (DOORS review module) |

**Variant management:**

| Vehicle Variant | Sensor Config | Feature Config | Requirement Applicability |
|:---:|:---:|:---:|---|
| Base | 1 radar + 1 camera | ACC only | SyR marked: "Applies to: Base, Premium, Sport" |
| Premium | 1 radar + 3 cameras + 1 LiDAR | ACC + LKA + AEB | Additional SyR for LKA/AEB; marked "Applies to: Premium, Sport" |
| Sport | Same as Premium + performance radar | ACC + LKA + AEB + SportDrive | Additional SyR for sport mode; marked "Applies to: Sport" |

Implementation: DOORS attribute `Variant_Applicability` (multi-select); filter views per variant; generate variant-specific SRS by filtering.

**Supplier exchange (ReqIF):**

```
OEM (DOORS) → [Export ReqIF] → Supplier (Polarion/Jama)
  Supplier adds: feasibility response; implementation notes
Supplier (Polarion) → [Export ReqIF] → OEM (DOORS)
  OEM reviews: acceptance; link to OEM architecture
  
Change sync: monthly ReqIF re-export; delta comparison; suspect links flagged
```

---

## Chapter 12 — Cheat Sheet & Quick Reference

```
═══════════════════════════════════════════
REQUIREMENTS ENGINEERING — QUICK REFERENCE
═══════════════════════════════════════════

STANDARD: ISO/IEC/IEEE 29148:2018
  Covers: Stakeholder Req; System Req; SW Req
  Supersedes: IEEE 830, IEEE 1233, IEEE 1362

═══════════════════════════════════════════
REQUIREMENT QUALITY ATTRIBUTES:
  N — Necessary (needed for mission)
  U — Unambiguous (one interpretation)
  C — Complete (fully stated; no TBD)
  C — Consistent (no contradictions)
  V — Verifiable (testable/measurable)
  T — Traceable (origin known; linked)
  F — Feasible (achievable)
  A — Atomic (single thing; no "and")

═══════════════════════════════════════════
REQUIREMENTS HIERARCHY:
  Business Req → Stakeholder Req (StRS) →
  System Req (SyRS) → SW Req (SRS)
  
  Each level: derives from above; refines into below
  Traceability: bidirectional (up and down)

═══════════════════════════════════════════
RE PROCESSES:
  1. ELICITATION (gather needs)
  2. ANALYSIS (classify; prioritize; resolve conflicts)
  3. SPECIFICATION (write; structure; assign attributes)
  4. VALIDATION (review; prototype; simulate)
  5. MANAGEMENT (version; change control; traceability)

═══════════════════════════════════════════
REQUIREMENT TEMPLATE:
  [Condition] [Subject] shall [Action] [Object] [Constraint]
  
  Example: "When vehicle speed > 5 km/h, the BCM shall
  activate daytime running lights within 500ms."

═══════════════════════════════════════════
VERIFICATION METHODS (TIAD):
  T = Test (execute; measure; compare to expected)
  I = Inspection (formal review of artifact)
  A = Analysis (mathematical/logical reasoning)
  D = Demonstration (show operation in scenario)

═══════════════════════════════════════════
TOOLS:
  IBM DOORS (NG/Classic) — industry standard (safety-critical)
  Polarion — automotive/industrial ALM
  Jama — medical/aerospace
  Codebeamer — automotive/medical (Agile-friendly)
  Azure DevOps — IT/general (work items = req)

═══════════════════════════════════════════
TRACEABILITY CHAIN:
  Stakeholder Need → System Req → SW Req →
  Design Element → Test Case → Test Result
  (bidirectional: both forward and backward)

═══════════════════════════════════════════
COMMON REQUIREMENT SMELLS (BAD):
  × "System shall be user-friendly" (ambiguous)
  × "TBD" (incomplete)
  × "System shall encrypt AND compress" (compound)
  × "System shall never fail" (not verifiable)
  × "System shall use MySQL" (premature design)
  × "Data shall be transmitted" (passive; who?)

═══════════════════════════════════════════
INTERCHANGE:
  ReqIF — standard XML format for tool-to-tool exchange
  OSLC — REST-based tool integration (linked data)
  SysML v2 — requirements as model elements (API access)
```

---

*End of Document — 06_Requirements_Engineering.md*
