# EASA AI in Aviation — Roadmap & Concept of Learning Systems

**Topic:** AI/ML in aviation safety; EASA AI Roadmap 2.0; Concept of Learning Systems (CoLS); DO-178C + ML extensions; Design Assurance Level (DAL) for AI; trustworthiness in aerospace AI  
**Standards:** EASA AI Roadmap 2.0 (2023), EASA Concept Paper on Learning Systems, DO-178C (Software), DO-254 (Hardware), ARP4754A (System), EUROCAE ED-324/WG-114  
**SDO:** EASA (European Union Aviation Safety Agency), RTCA (SC-228), EUROCAE (WG-114), SAE International  
**Audience:** Aviation safety engineers, ML engineers in aerospace, certification engineers (DERs/CVEs), aviation authorities, avionics developers  
**Prerequisites:** DO-178C basics, Design Assurance Levels (DAL A-E), aviation certification process, ML fundamentals, safety assessment (ARP4761)

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 1992 | DO-178B published | Software standard for airborne systems |
| 2011 | **DO-178C** published | Current software certification standard; supplements for model-based, OOT, formal methods — but NO AI/ML supplement |
| 2016 | First ML applications in aviation (non-safety) | Predictive maintenance; fuel optimization; operations |
| 2018 | Boeing 737 MAX MCAS failures | Highlighted software/system complexity in aviation; increased scrutiny on novel automation |
| 2020 | **EASA AI Roadmap 1.0** | First aviation authority AI strategy; identified certification gaps for ML |
| 2021 | EASA/Daedalean partnership | Certifying AI-based vision for landing systems |
| 2022 | EUROCAE WG-114 / RTCA SC-228 initiated | Working group on AI/ML in aviation (joint EU/US) |
| 2023 | **EASA AI Roadmap 2.0** | Updated with Concept of Learning Systems (CoLS) framework |
| 2023 | **EASA Concept Paper: First Usable Guidance for Level 1 ML** | Guidance for non-safety-critical ML (Level 1 and 2) |
| 2024 | EUROCAE ED-324 (early draft) | AI/ML guideline development |
| 2025+ | Expected Level 1/2 ML certification guidance | Formal means of compliance for ML in aviation |
| 2027+ | High-assurance ML (DAL B/A) guidance expected | ML in safety-critical flight systems |

### 1.2 The Aviation Safety Challenge for AI

| Aviation Requirement | Challenge for AI/ML |
|:---:|---|
| **Deterministic behavior** | ML is probabilistic; non-deterministic (floating-point, training randomness) |
| **Complete specification** | ML learns from data; behavior not explicitly specified; emergent capabilities |
| **DO-178C structural coverage (MC/DC)** | No equivalent coverage metric for neural networks |
| **Traceability (requirements → code → tests)** | Model weights not traceable to requirements |
| **Configuration management** | Training data, model weights, hyperparameters = new CM artifacts |
| **Tool qualification** | Training frameworks (PyTorch, TensorFlow) not qualified per DO-330 |
| **Independence of V&V** | ML expertise scarce; V&V team may overlap with development |
| **Design assurance levels** | How to map DAL A-E concepts to ML assurance? |
| **In-service experience** | ML models may be newly trained; no accumulated field experience |
| **Regression** | Model retraining changes ALL weights; regression testing challenging |

---

## Chapter 2 — EASA AI Roadmap 2.0

### 2.1 Overview

| Aspect | Detail |
|--------|--------|
| **Published** | 2023 (update of 2020 Roadmap 1.0) |
| **Purpose** | Define EASA's approach to certifying AI/ML in aviation across all domains (airborne, air traffic management, airport operations) |
| **Key concept** | "Building blocks" approach: start with lower-risk, lower-assurance applications → progressively allow higher-criticality as experience grows |
| **Framework** | Concept of Learning Systems (CoLS) — multi-level assurance framework for ML |

### 2.2 EASA AI Application Categories

| Level | AI Criticality | Examples | Assurance Approach |
|:-----:|:---:|---|---|
| **Level 1** | AI assists human (human makes decision) | Pilot decision support; maintenance recommendation; ATC advisory | Limited assurance; human oversight is primary safety mitigation |
| **Level 2** | AI acts, human oversees (can override) | Auto-taxi guidance; approach monitoring; automation aids | Moderate assurance; human-in-the-loop as safety net |
| **Level 3** | AI acts autonomously (human backup) | Autonomous flight phases; auto-land with ML perception; collision avoidance | High assurance; traditional + AI-specific means of compliance |

### 2.3 Building Blocks Approach

```mermaid
flowchart TD
    subgraph "Near-Term (2024-2026)"
        L1[Level 1: AI Assists Human<br/>━━━━━━━━━━━<br/>• ML predictive maintenance<br/>• Pilot advisory systems<br/>• Ground operations optimization<br/>• Weather prediction<br/>━━━━━━━━━━━<br/>Assurance: Data quality +<br/>performance monitoring +<br/>human decision authority]
    end
    
    subgraph "Mid-Term (2026-2028)"
        L2[Level 2: AI Acts, Human Oversees<br/>━━━━━━━━━━━<br/>• Advanced flight deck automation<br/>• AI-based surveillance<br/>• Automated inspection systems<br/>• Enhanced vision systems<br/>━━━━━━━━━━━<br/>Assurance: ML development assurance +<br/>runtime monitoring +<br/>human override capability]
    end
    
    subgraph "Long-Term (2028+)"
        L3[Level 3: AI Autonomous<br/>━━━━━━━━━━━<br/>• Single-pilot operations (AI copilot)<br/>• Urban Air Mobility (autonomous)<br/>• Fully autonomous cargo<br/>• ML-based collision avoidance<br/>━━━━━━━━━━━<br/>Assurance: Full ML certification +<br/>formal verification +<br/>continuous airworthiness]
    end
    
    L1 -->|"Experience gained;<br/>methods proven"| L2
    L2 -->|"Higher assurance<br/>methods mature"| L3
```

---

## Chapter 3 — Concept of Learning Systems (CoLS)

### 3.1 CoLS Framework

| Concept | Definition |
|:-------:|-----------|
| **Learning System** | System using ML where behavior is determined (in part) by training data, not only explicit programming |
| **Learning Phase** | When the model acquires its behavior (training) — typically offline for aviation |
| **Inference Phase** | When the trained model processes new inputs and produces outputs — runtime |
| **Operational Domain** | Defined set of conditions within which the ML model is assured to perform (like ODD in automotive) |
| **Assurance Objective** | What must be demonstrated for certification: model performs within specification in operational domain |

### 3.2 CoLS Assurance Pillars

| Pillar | Scope | DO-178C Analogy |
|:------:|-------|:---:|
| **Learning Assurance** | Quality and sufficiency of the learning process (training) | Development process objectives (DO-178C §5) |
| **Data Management Assurance** | Quality, governance, and representativeness of training/validation/test data | Requirements process (DO-178C §5.1) |
| **Model Verification** | Evidence that trained model meets performance requirements | Verification activities (DO-178C §6) |
| **System-Level Integration** | ML model correctly integrated into aircraft system; failure modes handled | System integration verification |
| **Runtime Assurance** | Monitoring ML performance in operation; detecting degradation; safe fallback | Configuration management + in-service |
| **Continuous Assurance** | Maintaining ML airworthiness over time; change management; performance tracking | Continued airworthiness |

### 3.3 Learning Assurance (Training Process)

| Requirement | Detail | Rationale |
|:---:|---|---|
| **Training process documentation** | Full documentation of training process: architecture choice, hyperparameters, optimization, regularization, stopping criteria | Reproducibility; auditability |
| **Training environment qualification** | Training tools (framework, hardware) qualified for intended use; deterministic training where feasible | Tool confidence (DO-330 equivalent) |
| **Training completeness** | Demonstrate training covers the operational domain sufficiently; no undertrained regions | Coverage confidence |
| **Overfitting prevention** | Evidence of generalization: validation set; early stopping; regularization; cross-validation | Model works on new data, not just training data |
| **Learning stability** | Training process reproducible; sensitivity analysis on hyperparameters; stable convergence | Confidence in trained model |

### 3.4 Data Management Assurance

| Requirement | Detail |
|:---:|---|
| **Data quality** | Accuracy, consistency, completeness of data; annotation quality; inter-rater agreement |
| **Data representativeness** | Data covers operational domain: all conditions, scenarios, edge cases, environmental factors |
| **Data independence** | Training, validation, and test sets genuinely independent (no leakage); representative of operation |
| **Data provenance** | Full traceability: where data came from; who collected it; how it was processed; chain of custody |
| **Data sufficiency** | Statistical evidence that data is sufficient for model to learn the operational domain |
| **Data governance** | Version control; access control; change management; data lifecycle management |
| **Bias assessment** | Training data assessed for biases that could affect safety performance in operation |

---

## Chapter 4 — DO-178C + ML: Gap Analysis

### 4.1 What DO-178C Already Covers (Applicable to ML)

| DO-178C Activity | ML Applicability |
|:---:|---|
| Planning | ✅ Applies: plan ML development, V&V, configuration management, quality assurance |
| Configuration management | ✅ Applies: CM of training data, model weights, hyperparameters, training pipeline |
| Quality assurance | ✅ Applies: independent QA over ML development process |
| System requirements | ✅ Applies: define WHAT the ML function must do (performance requirements) |
| Tool qualification (DO-330) | ⚠️ Partially: ML frameworks need some form of qualification or confidence argument |

### 4.2 DO-178C Gaps for ML

| DO-178C Requirement | Gap |
|:---:|---|
| **Software requirements** (§5.1) | ML does NOT have explicit low-level requirements (LLR); behavior emerges from training |
| **Software design** (§5.2) | Architecture (network topology) is designed, but "logic" is learned weights — not designable in traditional sense |
| **Source code** (§5.3) | Model weights are the "source code" — but are not human-readable or inspectable |
| **Structural coverage** (§6.4) | MC/DC and equivalent for neural networks does NOT exist; no agreed-upon coverage metric |
| **Requirements-based testing** (§6.3) | What are the "requirements" to test against for a learned function? Performance specifications, but not logic |
| **Traceability** (§5.5) | Cannot trace from requirement → specific weights implementing that requirement |
| **Dead code analysis** (§6.4.4.3) | Concept of "dead neurons" exists but relationship to safety unclear |

### 4.3 Proposed ML Equivalents

| DO-178C Concept | Proposed ML Equivalent | Status |
|:---:|---|:---:|
| Requirements-based testing | Performance-based testing (statistical; over operational domain) | Emerging |
| Structural coverage (MC/DC) | Neuron coverage; feature coverage; operational domain coverage; adversarial coverage | Research (no consensus) |
| Code review | Model interpretability analysis; architecture review; training pipeline review | Emerging |
| Deterministic behavior | Bounded non-determinism (output within acceptable envelope); formal robustness bounds | Research |
| Traceability | Data-to-performance traceability (which training data → which capability) | Emerging |
| Regression testing | Model regression testing (full re-evaluation after any change) | Practice |

---

## Chapter 5 — Certification & Approval Process

### 5.1 Current Certification Pathway

```mermaid
flowchart TD
    APPLICANT[Applicant<br/>(aircraft/system manufacturer)]
    
    APPLICANT --> CRI[Certification Review Item (CRI)<br/>━━━━━━━━━━━<br/>Special condition for ML<br/>(since no existing standard)<br/>Negotiated with EASA/FAA<br/>on case-by-case basis]
    
    CRI --> PLAN[ML Development Plan<br/>━━━━━━━━━━━<br/>• ML architecture rationale<br/>• Data management plan<br/>• Training plan<br/>• Verification plan<br/>• Safety mitigation strategy<br/>• Runtime monitoring plan]
    
    PLAN --> DEV[ML Development<br/>━━━━━━━━━━━<br/>Per CoLS pillars:<br/>• Data collection & qualification<br/>• Model training & validation<br/>• Performance verification<br/>• Robustness testing<br/>• Integration verification]
    
    DEV --> EVIDENCE[Assurance Evidence<br/>━━━━━━━━━━━<br/>• Data quality report<br/>• Training process evidence<br/>• Performance test results<br/>• Robustness analysis<br/>• Safety analysis (with ML)<br/>• Runtime monitoring spec]
    
    EVIDENCE --> REVIEW[Authority Review<br/>━━━━━━━━━━━<br/>EASA / FAA reviews:<br/>• Development process<br/>• Assurance evidence<br/>• Safety argument<br/>• Compliance demonstration]
    
    REVIEW -->|"Acceptable"| APPROVE[Type Certificate /<br/>Supplemental Type Certificate<br/>with ML conditions]
    
    REVIEW -->|"Insufficient"| DEV
```

### 5.2 DAL Considerations for ML

| DAL | Failure Condition | Traditional Software | ML Approach (Proposed) |
|:---:|:---:|---|---|
| **E** | No safety effect | Minimal objectives | Basic data quality; performance testing; no special ML assurance |
| **D** | Minor | Some process objectives | Data management; performance verification; basic robustness testing |
| **C** | Major | Moderate process + testing | Full CoLS pillars; runtime monitoring; operational domain verification |
| **B** | Hazardous | Rigorous process + coverage | CoLS + formal methods where feasible; diverse redundancy; proven-safe fallback |
| **A** | Catastrophic | Most rigorous (MC/DC etc.) | Full CoLS + formal verification + runtime assurance + independent monitoring + deterministic backup. Currently: ML alone NOT accepted at DAL A without extraordinary mitigation |

---

## Chapter 6 — Runtime Assurance for Aviation AI

### 6.1 Runtime Monitoring Architecture

```mermaid
graph TB
    INPUT[Sensor Input<br/>(cameras, radar, lidar)]
    
    INPUT --> ML_MODEL[ML Model<br/>(perception, prediction)]
    INPUT --> MON[Runtime Monitor<br/>━━━━━━━━━━━<br/>• Input validity (OOD detection)<br/>• Confidence assessment<br/>• Output plausibility<br/>• Temporal consistency<br/>• Cross-sensor agreement]
    
    ML_MODEL --> OUTPUT[ML Output<br/>(detection, classification)]
    
    OUTPUT --> MON
    
    MON --> DECIDE{Within assured<br/>envelope?}
    
    DECIDE -->|"Yes: confident"| USE[Use ML Output<br/>(primary function)]
    
    DECIDE -->|"No: uncertain/anomalous"| FALLBACK[Activate Fallback<br/>━━━━━━━━━━━<br/>• Conventional system (if available)<br/>• Conservative default action<br/>• Alert crew for takeover<br/>• Graceful degradation<br/>• Record incident for analysis]
    
    USE --> FLIGHT[Flight System<br/>(FMS, autopilot, displays)]
    FALLBACK --> FLIGHT
```

### 6.2 Continuous Airworthiness for ML

| Traditional | ML-Enhanced |
|:---:|---|
| Maintenance schedule (fixed intervals) | + Performance monitoring (continuous) |
| Service Bulletins (reactive) | + Drift detection (proactive) |
| Part replacement | + Model update (with safety governance) |
| Flight data monitoring | + ML output quality monitoring |
| AD (Airworthiness Directive) for fixes | + ML update governance process (equivalent to AD for software) |

---

## Chapter 7 — Comparison: Aviation AI Approaches Globally

| Dimension | EASA (EU) | FAA (US) | TCCA (Canada) | CAAC (China) |
|:---------:|:---:|:---:|:---:|:---:|
| **AI Strategy** | AI Roadmap 2.0 + CoLS | Incremental (AIR-6988, CRI) | Following FAA/EASA lead | National AI aviation plan |
| **Framework** | Concept of Learning Systems (CoLS) | Assurance of ML (ASTM, SAE) | Harmonizing | Developing |
| **Standard activity** | EUROCAE WG-114 (ED-324) | RTCA SC-228; SAE AIR-6988 | Following | National standards |
| **First certifications** | Level 1 (2025-2026 expected) | Limited approvals (CRI-based) | TBD | TBD |
| **Open issues** | DAL A ML; online learning; LLM | Same + liability | Same | Different regulatory context |
| **Harmonization** | EASA-FAA bilateral; joint working groups | Same | Aligned via bilateral | Separate development |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 CoLS Assurance Framework

```mermaid
graph TB
    COLS[Concept of Learning Systems<br/>EASA CoLS Framework]
    
    COLS --> LA[Learning Assurance<br/>━━━━━━━━━━━<br/>• Training process quality<br/>• Architecture selection<br/>• Hyperparameter justification<br/>• Convergence evidence<br/>• Overfitting prevention<br/>• Stability analysis]
    
    COLS --> DMA[Data Management Assurance<br/>━━━━━━━━━━━<br/>• Data quality (accuracy, completeness)<br/>• Representativeness<br/>• Independence (train/val/test)<br/>• Provenance & traceability<br/>• Sufficiency analysis<br/>• Bias assessment]
    
    COLS --> MV[Model Verification<br/>━━━━━━━━━━━<br/>• Performance testing<br/>• Robustness testing<br/>• Operational domain coverage<br/>• Edge case analysis<br/>• Formal verification (where feasible)<br/>• Requirements compliance]
    
    COLS --> SI[System Integration<br/>━━━━━━━━━━━<br/>• ML in system context<br/>• Interface verification<br/>• Failure mode analysis<br/>• Safety mitigation validation<br/>• Human-ML interaction]
    
    COLS --> RA[Runtime Assurance<br/>━━━━━━━━━━━<br/>• Input monitoring<br/>• Output monitoring<br/>• Confidence assessment<br/>• Safe fallback mechanism<br/>• Performance degradation detection]
    
    COLS --> CA[Continuous Assurance<br/>━━━━━━━━━━━<br/>• In-service monitoring<br/>• Performance tracking<br/>• Change management<br/>• Incident reporting<br/>• Re-certification triggers]
```

### 8.2 ML Certification Evidence Structure

```mermaid
flowchart LR
    subgraph "Planning"
        PLAN_D[ML Development Plan<br/>ML Verification Plan<br/>Data Management Plan<br/>Configuration Mgmt Plan]
    end
    
    subgraph "Development Evidence"
        DATA_E[Data Quality Report<br/>Representativeness Analysis<br/>Provenance Records<br/>Sufficiency Argument]
        
        TRAIN_E[Training Process Records<br/>Architecture Rationale<br/>Convergence Evidence<br/>Stability Analysis]
        
        VERIFY_E[Performance Test Results<br/>Robustness Test Results<br/>Coverage Analysis<br/>Edge Case Results]
    end
    
    subgraph "Safety Evidence"
        SAFETY_E[Safety Assessment (with ML)<br/>Failure Mode Analysis<br/>Runtime Monitor Specification<br/>Fallback Validation<br/>Residual Risk Assessment]
    end
    
    subgraph "Certification"
        CERT_E[Compliance Matrix<br/>ML Assurance Case<br/>Authority Review Record<br/>Approval Conditions]
    end
    
    PLAN_D --> DATA_E --> TRAIN_E --> VERIFY_E --> SAFETY_E --> CERT_E
```

---

## Chapter 9 — Case Studies

### 9.1 Vision-Based Landing System (Daedalean + EASA)

| Aspect | Detail |
|--------|--------|
| **System** | AI-based visual approach and landing guidance using camera + DNN object detection/segmentation for runway identification |
| **Developer** | Daedalean AG (Swiss AI aviation company) |
| **Authority** | EASA (collaborative certification project) |
| **Criticality** | DAL C (major failure condition; pilot can recover using other instruments) |
| **Approach** | (1) ML perceives runway using camera (primary function in visual conditions). (2) Traditional ILS/GPS provides independent reference (backup). (3) Runtime monitor: cross-checks ML output against expected geometry. (4) If disagreement: alert pilot; revert to traditional instruments. |
| **Data management** | (1) Training data: >100K runway approach images across airports, weather, lighting, seasons. (2) Representativeness argument: statistical analysis showing coverage of operational conditions. (3) Independent test set: held-out airports NOT in training data → tests generalization. (4) Annotation: professional annotators; double-check; inter-annotator agreement >0.95. |
| **Verification** | (1) Performance: runway detection accuracy >99.9% in operational domain (measured on independent test set). (2) Robustness: tested with degraded images (fog, rain, glare, partial occlusion); performance bounded. (3) Edge cases: unusual runways; construction; lighting failures; tested specifically. (4) OOD: tested with inputs outside operational domain → confirmed runtime monitor catches. |
| **Certification status** | EASA engagement ongoing; expected among first ML-certified aviation systems (Level 1/2) |
| **Key innovation** | Safety argument NOT based on proving ML is perfect; based on: ML performance + independent backup + runtime monitoring + pilot authority = system-level safety |

### 9.2 Predictive Maintenance AI (Level 1 — Assists Human)

| Aspect | Detail |
|--------|--------|
| **System** | ML-based engine health monitoring; predicts component remaining useful life (RUL) from sensor data |
| **Criticality** | Level 1 (assists maintenance engineer; human decides; no direct flight safety) |
| **Assurance approach** | (1) Lighter assurance (Level 1 CoLS): data quality evidence + performance validation + operational monitoring. (2) ML recommends inspection timing; qualified engineer makes maintenance decision. (3) If ML wrong (predicts longer life than actual) → existing time-based maintenance limits still apply (regulatory minimum). (4) ML can only SHORTEN maintenance intervals (recommend earlier inspection), never extend beyond regulatory limits. |
| **Certification** | Approved as "advisory tool" under existing maintenance program; does not change approved maintenance intervals; no airworthiness impact |
| **Value** | 30% reduction in unscheduled maintenance events; 15% reduction in maintenance costs; safety margin maintained (never extends intervals) |
| **Lesson** | Level 1 (advisory) AI can be deployed NOW with existing regulatory framework because: (1) Human authority preserved. (2) Existing safety margins maintained. (3) AI can only improve safety, never degrade it (one-directional benefit). This is the pragmatic entry point for aviation AI. |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **EUROCAE ED-324 publication** | 2025-2026 | First industry standard for AI/ML in aviation (EASA/FAA recognized) |
| **Level 1/2 certifications** | 2025-2027 | First officially certified ML systems in type certificates |
| **Single pilot operations** | 2028-2032 | AI copilot for commercial aviation; requires Level 3 AI assurance |
| **Urban Air Mobility (UAM)** | 2026-2030 | eVTOL vehicles with high automation; ML-dependent flight control |
| **DO-178C revision or supplement** | 2027-2030 | Formal DO-178C/ML supplement (like DO-333 for formal methods) |
| **Online learning in aviation** | 2030+ | Currently prohibited (no in-service learning); may be allowed with extremely robust assurance |
| **Formal verification for aviation DNN** | 2026-2030 | Scalable formal methods enabling higher DAL certification |
| **AI-specific airworthiness directives** | 2026+ | Regulatory mechanism for mandating ML model updates fleet-wide |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** Why can't we simply apply DO-178C to AI/ML software in aviation? What are the fundamental gaps?

**A:** DO-178C was designed for DETERMINISTIC, REQUIREMENTS-DRIVEN software development. It assumes you can: (1) write complete requirements for every software behavior, (2) design software logic that implements those requirements, (3) inspect source code for correctness, (4) achieve structural coverage (MC/DC) proving all code paths exercised, and (5) trace from requirements → design → code → tests bidirectionally.

AI/ML breaks ALL of these:

(1) **Requirements gap**: For a DNN that detects runways — you CANNOT write requirements like "IF pixel pattern matches [specific description] THEN runway detected." The model LEARNS what a runway looks like from 100K images. The "requirement" is statistical: "detect runways with >99.9% accuracy under specified conditions."

(2) **Code inspection gap**: ML "source code" = millions of floating-point weights. A DO-178C code review of `weight[3456789] = 0.0034521` is meaningless. You cannot read a neural network like you read C code.

(3) **Structural coverage gap**: DO-178C Level A requires MC/DC coverage. What's the equivalent for a neural network? "Neuron coverage" has been proposed but doesn't guarantee safety the way MC/DC does for boolean logic. There's NO agreed-upon coverage metric for DNNs.

(4) **Traceability gap**: In DO-178C, you trace: system requirement → software requirement → design → code → test. For ML: you can trace system requirement → performance requirement → test dataset, but you CANNOT trace to specific weights that implement that requirement.

(5) **Determinism gap**: DO-178C assumes same input → same output. ML training is non-deterministic (initialization, batch ordering); even inference can vary across hardware due to floating-point.

EASA's CoLS addresses this by REDEFINING what assurance means for ML: instead of code-level verification, use DATA quality + TRAINING process quality + STATISTICAL performance evidence + RUNTIME monitoring + SYSTEM-LEVEL safety architecture.

### Tier 2: Mid-Level

**Q2:** Explain EASA's Concept of Learning Systems (CoLS). How does it provide assurance equivalent to DO-178C for ML systems?

**A:** CoLS provides assurance through SIX pillars that together achieve confidence equivalent to DO-178C's process-based assurance, but adapted for ML characteristics:

**Pillar 1 — Learning Assurance** (equivalent to DO-178C Development Process):
Instead of verifying correct code implementation, verify the TRAINING PROCESS: documented architecture selection rationale; justified hyperparameters; convergence evidence; stability analysis (small changes to training → stable model); overfitting prevention (validation-based early stopping; regularization). The argument: "a rigorous, well-controlled training process produces a trustworthy model" (analogous to "a rigorous development process produces trustworthy code").

**Pillar 2 — Data Management Assurance** (equivalent to Requirements):
Training data IS the ML equivalent of requirements (data defines what the model learns). Therefore: data quality (accuracy, annotation quality); representativeness (covers operational domain); independence (train/val/test truly separate); provenance (where data came from; how collected); sufficiency (statistical evidence that enough data was provided). The argument: "high-quality, representative data covering the operational domain → model learned correct behavior for that domain."

**Pillar 3 — Model Verification** (equivalent to DO-178C Verification):
Statistical testing replaces structural coverage: performance metrics on INDEPENDENT test data covering operational domain; robustness testing (noise, perturbations, edge cases); formal verification where feasible (bounded input regions → guaranteed output properties). The argument: "extensive testing demonstrates the model performs within requirements across the operational domain."

**Pillar 4 — System Integration** (equivalent to Integration Testing):
ML doesn't exist alone — it's part of an aircraft system. Verify interfaces; failure modes; timing constraints; resource usage; interaction with other systems. Safety assessment includes ML-specific failure modes (misclassification, confidence miscalibration, OOD failure).

**Pillar 5 — Runtime Assurance** (NEW — no DO-178C equivalent):
This is what ML ADDS beyond traditional software: runtime monitoring that CATCHES failures the previous pillars might miss. Input validation (OOD detection); output plausibility; confidence monitoring; cross-checking with independent references; safe fallback when anomaly detected. The argument: "even if the model encounters a scenario not covered in testing, runtime monitoring catches it and activates safe fallback."

**Pillar 6 — Continuous Assurance** (equivalent to Continued Airworthiness):
ML performance may degrade over time (environment changes; sensor degradation). Track performance in service; detect drift; manage model updates (with full regression testing + safety review); configuration management of operational models.

**Together**: CoLS doesn't try to prove ML is correct (impossible in the DO-178C sense). It proves the ML is TRUSTWORTHY through: rigorous development process + high-quality data + extensive verification + system-level safety + runtime protection + ongoing monitoring. This is EQUIVALENT assurance through DIFFERENT means.

### Tier 3: Senior

**Q3:** Design a certification strategy for an ML-based Enhanced Ground Proximity Warning System (EGPWS) using terrain classification from camera imagery. The function is DAL B (hazardous failure condition). How do you make the case to EASA?

**A:**

**Context**: EGPWS warns pilots of terrain proximity. Traditional EGPWS uses radio altimeter + terrain database. Proposed enhancement: camera-based terrain classification using DNN provides ADDITIONAL terrain awareness (identifies terrain type, obstacles, runway environment).

**Certification Strategy — Multi-Layer Argument:**

*Safety architecture (system-level argument — key to DAL B):*

The ML vision system does NOT replace the traditional EGPWS. Architecture:
1. **Primary EGPWS**: Traditional (radio altimeter + terrain database) — DAL B certified per DO-178C. UNCHANGED. This alone satisfies DAL B safety requirement.
2. **ML Enhancement**: Camera + DNN terrain classification provides ADDITIONAL awareness (earlier detection of terrain in visual conditions; obstacle identification; enhanced situational awareness).
3. **Integration logic**: ML enhancement can ONLY ADD warnings (never suppress traditional EGPWS warnings). ML outputs presented to pilot as advisory information unless confirmed by traditional sensors.
4. **Independence**: ML processing on separate hardware partition (DO-178C/DO-254 compliant partitioning; freedom from interference with primary EGPWS proven).

*DAL decomposition argument:*

| Function | DAL | Justification |
|:---:|:---:|---|
| Primary EGPWS (traditional) | B | Certified per DO-178C (existing) |
| ML terrain classification | C | ML failure = loss of enhanced display (major, not hazardous) because primary EGPWS still functions independently |
| Integration logic | B | Must ensure ML cannot interfere with primary EGPWS; simple verified logic |
| Runtime monitor | C | Monitors ML; activates degradation |

Key argument: ML failure condition is NOT hazardous (DAL B) because:
- If ML fails (misclassifies terrain): primary EGPWS still warns correctly
- ML can only ADD information, never suppress warnings
- Therefore ML failure = loss of enhancement = major (DAL C) not hazardous (DAL B)

*CoLS assurance for ML component (DAL C):*

**Data**: 500K+ terrain images; 50+ terrain types; all operational conditions (day/night/weather/season); 30+ geographic regions; professional annotation with double-verification; statistical representativeness analysis.

**Training**: Documented CNN architecture selection (compared 5 architectures); ablation study; convergence evidence; 5-fold cross-validation; overfitting metrics (train/val gap <2%).

**Verification**: Independent test set (held-out regions NOT in training): accuracy >99.5% for terrain/no-terrain; >98% for terrain type classification. Robustness: Gaussian noise (σ up to 0.1); JPEG compression artifacts; low-light degradation; partial occlusion — performance >97% under all degradations. Formal: verified robustness bounds for critical terrain classes within defined input perturbation regions.

**Runtime monitoring**: OOD detector (reconstruction error); confidence threshold (outputs below 80% → suppress ML display); temporal consistency (terrain classification must be stable across 5+ frames); cross-check with radio altimeter (if terrain classification says "flat" but radio alt says "descending rapidly" → primary EGPWS has authority).

**Continuous assurance**: Fleet-wide performance tracking; sensor degradation monitoring; update governance (any model update → full regression + safety review + EASA notification).

*Precedent and exit strategy:*

If EASA does not accept DAL C for ML component: fall back to DAL D classification by further limiting ML authority (display only, no alerts) + additional human factors argument. The system remains valuable even at lower DAL because any additional terrain awareness is safety-beneficial.

---

## Chapter 12 — Cheat Sheet & Quick Reference

```
═══════════════════════════════════════════
EASA AI IN AVIATION — QUICK REFERENCE
═══════════════════════════════════════════

EASA AI ROADMAP 2.0 — LEVELS:
  Level 1: AI assists human (human decides)
  Level 2: AI acts, human oversees (can override)
  Level 3: AI autonomous (human backup)
  
  Approach: Start Level 1 → gain experience → progress

═══════════════════════════════════════════
CoLS — CONCEPT OF LEARNING SYSTEMS (6 PILLARS):
  1. Learning Assurance    (training process quality)
  2. Data Management       (data quality & representativeness)
  3. Model Verification    (performance & robustness testing)
  4. System Integration    (ML in aircraft system context)
  5. Runtime Assurance     (monitoring + safe fallback)
  6. Continuous Assurance  (in-service performance tracking)

═══════════════════════════════════════════
DO-178C GAPS FOR ML:
  Requirements:    ML behavior not explicitly specified (learned)
  Code review:     Weights not human-inspectable
  Coverage:        No MC/DC equivalent for neural networks
  Traceability:    Cannot trace requirements to specific weights
  Determinism:     ML training non-deterministic

═══════════════════════════════════════════
DAL MAPPING FOR ML:
  DAL E: Basic data quality + performance testing
  DAL D: Data management + performance verification
  DAL C: Full CoLS + runtime monitoring
  DAL B: CoLS + formal methods + diverse redundancy + backup
  DAL A: Currently ML alone NOT accepted; need deterministic backup
  
  Key strategy: DAL decomposition — put ML at lower DAL,
  keep safety function at system level with independent backup

═══════════════════════════════════════════
CERTIFICATION APPROACH (CURRENT):
  • No existing standard covers ML (no DO-178C/ML supplement yet)
  • Use CRI (Certification Review Item) — special condition
  • Negotiate means of compliance with authority per project
  • CoLS pillars as framework for evidence package
  • Safety argument: architecture + multi-layer (not ML alone)

═══════════════════════════════════════════
KEY TIMELINE:
  2024-2025: EASA Level 1/2 guidance published
  2025-2026: EUROCAE ED-324 (industry standard)
  2025-2027: First certified ML systems (Level 1/2)
  2027+:     DO-178C supplement for ML (expected)
  2028+:     Level 3 (autonomous) certification possible

═══════════════════════════════════════════
PRACTICAL CERTIFICATION STRATEGY:
  1. ML never replaces certified safety function
  2. ML provides ENHANCEMENT / ADVISORY / ADDITION
  3. Traditional system remains (certified, deterministic)
  4. ML failure = loss of enhancement (lower criticality)
  5. DAL decomposition: ML at lower DAL than system function
  6. Runtime monitoring detects ML anomalies
  7. Safe degradation: revert to traditional when ML uncertain

═══════════════════════════════════════════
DATA MANAGEMENT REQUIREMENTS:
  □ Quality (accurate, consistent annotations)
  □ Representativeness (covers operational domain)
  □ Independence (train/val/test separate)
  □ Provenance (full traceability)
  □ Sufficiency (enough data; statistical argument)
  □ Governance (CM, access control, versioning)
  □ Bias assessment (safety-relevant biases)
```

---

*End of Document — 11_EASA_AI_Aviation.md*
