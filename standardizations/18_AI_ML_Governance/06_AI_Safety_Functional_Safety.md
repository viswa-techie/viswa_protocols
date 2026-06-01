# AI Safety & Functional Safety Standards

**Topic:** AI in safety-critical systems; functional safety for ML; ISO/IEC CD 5469; IEC TR 63317; SOTIF (ISO 21448) for AI perception; EASA learning systems; assurance of ML in safety-critical applications  
**Standards:** ISO/IEC CD 5469 (AI Functional Safety), IEC TR 63317, ISO/SAE 21448 (SOTIF), ISO 26262, IEC 61508, DO-178C + ML considerations  
**SDO:** ISO/IEC JTC 1/SC 42, IEC TC 65, ISO TC 22, RTCA/EUROCAE  
**Audience:** Safety engineers, ML engineers, functional safety managers, certification authorities, system architects  
**Prerequisites:** Functional safety basics (ISO 26262 or IEC 61508), ML/DL fundamentals, V-model development, safety integrity levels

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 The Fundamental Problem

Traditional functional safety (ISO 26262, IEC 61508, DO-178C) assumes:
- System behavior is **deterministic** and **specifiable**
- Requirements can be **complete** — all behavior explicitly defined
- Testing can **verify** correct implementation against specification
- Code can be **inspected** and logic **traced** to requirements

AI/ML breaks ALL of these assumptions:

| Traditional | AI/ML Reality |
|:---:|---|
| Deterministic behavior | Probabilistic; different runs → different outputs |
| Complete specification | Cannot specify all edge cases; learned behavior |
| Verification against spec | No explicit specification to verify against |
| Code inspection | Model weights (millions of parameters) not human-inspectable |
| Known failure modes | Unknown unknowns; emergent behaviors |
| Worst-case analysis | Statistical performance; no guaranteed worst case |

### 1.2 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 1998 | IEC 61508 first edition | Functional safety foundation; no AI consideration |
| 2011 | ISO 26262 first edition | Automotive functional safety; traditional SW only |
| 2012 | Deep learning revolution (AlexNet) | ML enters engineering domains |
| 2016 | Tesla Autopilot fatality (first known) | AI perception failure in safety system → public awareness |
| 2018 | Uber ATG fatality (Tempe, AZ) | ADAS perception failure; classification → catastrophic |
| 2018 | ISO 26262:2018 second edition | Still no AI/ML guidance |
| 2019 | **ISO/SAE 21448 (SOTIF)** published | Safety of Intended Functionality — first standard addressing AI perception limits |
| 2020 | EASA AI Roadmap 1.0 | European aviation: ML in certified systems |
| 2021 | IEC TR 63317 published | AI in safety-related systems (technical report) |
| 2022 | ISO/IEC CD 5469 development begins | AI Functional Safety — dedicated standard |
| 2023 | EASA AI Roadmap 2.0 | Concept of Learning Systems (CoLS) for aviation |
| 2023 | ISO TR 4804 (Automated driving safety) | Safety validation for AD systems using AI |
| 2024 | ISO/IEC CD 5469 Committee Draft | AI functional safety standard progressing |
| 2025+ | ISO/IEC 5469 expected publication | First international AI functional safety standard |

---

## Chapter 2 — The Safety Challenge for AI/ML

### 2.1 Why Traditional Safety Methods Fail for AI

| Safety Activity | Traditional Approach | Challenge with AI/ML |
|:---:|---|---|
| **Hazard analysis** | Identify all hazardous behaviors | Cannot enumerate all AI failure modes; ODD boundaries unclear |
| **Requirements** | Complete specification of behavior | AI learns behavior; not explicitly programmed; behavior emerges |
| **Verification** | Test against requirements | What to test against? Performance is statistical, not binary |
| **Code review** | Inspect logic for errors | Model weights (millions) are not human-readable logic |
| **Structural coverage** | MC/DC; statement coverage | What is "coverage" for a neural network? |
| **Determinism** | Same input → same output | Floating-point non-determinism; model updates change behavior |
| **Worst case** | WCET; worst-case analysis | Statistical performance; no provable worst-case guarantee |
| **Change impact** | Traceability of change | Retraining changes ALL weights; impact analysis impossible traditionally |
| **Independence** | V&V independent from development | Who validates ML? Need ML expertise (same people who build) |

### 2.2 AI Failure Taxonomy for Safety

```mermaid
graph TB
    FAIL[AI Failures in<br/>Safety-Critical Systems]
    
    FAIL --> PERF[Performance Failures<br/>━━━━━━━━━━━<br/>• Misclassification<br/>• False negative (missed hazard)<br/>• False positive (nuisance action)<br/>• Confidence miscalibration<br/>• Performance degradation over time]
    
    FAIL --> ROB[Robustness Failures<br/>━━━━━━━━━━━<br/>• Out-of-distribution inputs<br/>• Adversarial perturbations<br/>• Edge cases (rare scenarios)<br/>• Environmental changes<br/>• Sensor degradation]
    
    FAIL --> SPEC[Specification Failures<br/>━━━━━━━━━━━<br/>• Incomplete training data<br/>• Wrong objective function<br/>• Reward hacking (RL)<br/>• Distribution shift<br/>• Underspecification]
    
    FAIL --> IMPL[Implementation Failures<br/>━━━━━━━━━━━<br/>• Training bugs<br/>• Data pipeline errors<br/>• Inference engine bugs<br/>• Hardware faults<br/>• Numerical instability]
```

---

## Chapter 3 — ISO/SAE 21448 (SOTIF) for AI Perception

### 3.1 SOTIF Scope

| Aspect | Detail |
|--------|--------|
| **Full title** | Road vehicles — Safety of the Intended Functionality |
| **Focus** | Safety concerns from limitations of the INTENDED function (not hardware failures = ISO 26262) |
| **AI relevance** | Specifically addresses ML perception limitations (cameras, lidar, radar + AI) |
| **Key concept** | Even if the AI WORKS CORRECTLY (no fault), its intended behavior may be unsafe in some scenarios |

### 3.2 SOTIF Scenario Areas

| Area | Description | Relationship to Safety |
|:----:|-------------|:---:|
| **Area 1** | Known safe scenarios | ✅ Safe (known; validated) |
| **Area 2** | Known unsafe scenarios | ❌ Identified; mitigated by design |
| **Area 3** | Unknown unsafe scenarios | ⚠️ CRITICAL — These are the "unknown unknowns" |
| **Area 4** | Unknown safe scenarios | Acceptable (safe but unknown — no harm) |

**SOTIF goal**: Reduce Area 3 (unknown unsafe) to an acceptable level. You can NEVER prove Area 3 is empty (you don't know what you don't know), but you can drive it below an acceptable residual risk threshold.

### 3.3 SOTIF Process for AI/ML Perception

```mermaid
flowchart TD
    START[AI Perception System<br/>(camera + DNN object detection)]
    
    START --> TC[Identify Triggering Conditions<br/>━━━━━━━━━━━<br/>What can cause AI to fail?<br/>• Weather (rain, fog, snow, sun glare)<br/>• Lighting (low light, high contrast)<br/>• Occlusion (partial visibility)<br/>• Unusual objects (debris, animals)<br/>• Adversarial inputs (modified signs)<br/>• Rare scenarios (construction, accident)]
    
    TC --> ANALYZE[Analyze Functional Insufficiency<br/>━━━━━━━━━━━<br/>What are the AI's limitations?<br/>• Detection range at night: 50m (vs. 150m day)<br/>• Confidence < 50% in heavy rain<br/>• Known misclassification: motorcycle vs. bicycle<br/>• Edge case: pedestrian in costume]
    
    ANALYZE --> HAZARD[Link to Vehicle-Level Hazard<br/>━━━━━━━━━━━<br/>What happens if AI fails?<br/>• Missed pedestrian → no AEB → collision<br/>• False positive → unnecessary braking<br/>• Wrong classification → wrong behavior]
    
    HAZARD --> MITIGATE[Mitigation Strategies<br/>━━━━━━━━━━━<br/>Design:<br/>• Sensor fusion (camera + radar + lidar)<br/>• Redundant AI models<br/>• Conservative default (brake if uncertain)<br/>• ODD restriction (don't operate in fog)<br/>Validation:<br/>• Extensive testing (millions of miles)<br/>• Simulation of triggering conditions<br/>• Field monitoring + OTA update capability]
    
    MITIGATE --> RESIDUAL[Residual Risk Assessment<br/>━━━━━━━━━━━<br/>Is remaining risk acceptable?<br/>• Exposure × severity × controllability<br/>• Compare to human driver baseline<br/>• Quantitative target: < 10⁻⁸ per hour<br/>  for catastrophic scenarios]
    
    RESIDUAL -->|"Acceptable"| RELEASE[Release to field<br/>with monitoring]
    RESIDUAL -->|"Not acceptable"| TC
```

---

## Chapter 4 — IEC TR 63317: AI in Safety-Related Systems

### 4.1 Overview

| Aspect | Detail |
|--------|--------|
| **Type** | Technical Report (guidance, not requirements) |
| **Scope** | AI technology in safety-related systems across all industrial sectors |
| **Published** | 2021 (IEC TC 65 — Industrial Process Measurement, Control and Automation) |
| **Relationship** | Provides guidance on how AI might fit within IEC 61508 framework |

### 4.2 Key Concepts

| Concept | Guidance |
|:-------:|---------|
| **AI as safety function** | AI performing safety function (e.g., emergency shutdown) — highest scrutiny required |
| **AI as non-safety function with safety impact** | AI for optimization but failure could create hazard — moderate scrutiny |
| **AI complexity levels** | Simple (rule-based/lookup) → Medium (shallow ML) → High (deep learning) — scrutiny increases with complexity |
| **Learning phases** | Training (offline) vs. Inference (online) vs. Online learning (continuous) — different safety challenges for each |
| **Data dependency** | AI performance depends on data quality — data governance is safety-critical |

### 4.3 Lifecycle Considerations

| Phase | IEC 61508 Activity | AI-Specific Addition (per TR 63317) |
|:-----:|---|---|
| Concept | Hazard analysis | Include AI-specific failure modes; data-dependent hazards |
| Design | Safety requirements | Specify AI performance requirements (accuracy, robustness); define operational domain |
| Implementation | Software development | ML training pipeline; data management; model validation |
| Verification | Testing | Statistical testing; robustness testing; edge case testing; adversarial testing |
| Validation | System validation | Operational validation; field testing; monitoring strategy |
| Operation | Maintenance | Model monitoring; drift detection; retraining governance; change management |
| Modification | Change process | Model retraining = potential safety-relevant change; impact analysis needed |

---

## Chapter 5 — ISO/IEC CD 5469: AI Functional Safety

### 5.1 Overview (Based on Committee Draft)

| Aspect | Detail |
|--------|--------|
| **Title** | Artificial Intelligence — Functional safety and AI systems |
| **Status** | Committee Draft (CD) as of 2024; publication expected 2025-2026 |
| **Scope** | Requirements and guidance for AI in safety-related systems; sector-independent |
| **Relationship** | Complements IEC 61508; ISO 26262; IEC 62443; aviation standards |

### 5.2 Expected Key Concepts

| Concept | Expected Approach |
|:-------:|---|
| **AI safety integrity** | New concept: AI-specific integrity levels (may map to SIL/ASIL but with AI-appropriate requirements) |
| **Data safety requirements** | Training data quality requirements proportional to safety integrity |
| **Model assurance** | Evidence requirements for AI model trustworthiness (robustness, out-of-distribution detection, calibration) |
| **Online learning governance** | Strict requirements for systems that learn in operation (most restrictive for highest integrity) |
| **Explainability for safety** | Minimum explainability requirements for safety investigation/incident analysis |
| **Continuous assurance** | Post-deployment monitoring requirements; performance thresholds triggering safety review |

### 5.3 Proposed Assurance Approaches

| Level | Approach | Applicable To |
|:-----:|----------|:---:|
| **Level 1** | Statistical performance evidence only | Lowest safety integrity; advisory AI |
| **Level 2** | + Robustness testing; OOD detection; sensor fusion | Moderate safety integrity |
| **Level 3** | + Formal verification where feasible; diverse AI; redundant architectures | High safety integrity |
| **Level 4** | + Runtime monitoring with safe fallback; formal coverage analysis; provably safe envelope | Highest safety integrity |

---

## Chapter 6 — Approaches to AI Safety Assurance

### 6.1 Multi-Layer Safety Architecture for AI

```mermaid
graph TB
    subgraph "Layer 1: AI Model Quality"
        L1[Model Development<br/>━━━━━━━━━━━<br/>• Quality training data<br/>• Validated architecture<br/>• Rigorous V&V<br/>• Performance bounds documented]
    end
    
    subgraph "Layer 2: Runtime Monitoring"
        L2[Runtime Safety Monitor<br/>━━━━━━━━━━━<br/>• Input validity check (OOD detection)<br/>• Confidence monitoring<br/>• Output plausibility check<br/>• Performance degradation detection]
    end
    
    subgraph "Layer 3: Safe Fallback"
        L3[Deterministic Safety Layer<br/>━━━━━━━━━━━<br/>• Non-AI safety function<br/>• Guaranteed safe behavior<br/>• Activated when AI uncertain<br/>• Simple, verifiable, deterministic]
    end
    
    subgraph "Layer 4: System Architecture"
        L4[Diverse Redundancy<br/>━━━━━━━━━━━<br/>• Multiple AI models (diverse training)<br/>• AI + non-AI redundancy<br/>• Sensor fusion (diverse sensors)<br/>• Cross-checking between channels]
    end
    
    L1 --> L2
    L2 -->|"AI confidence low"| L3
    L2 -->|"AI confidence OK"| USE[Use AI Output]
    L4 --> L1
    L4 --> L3
```

### 6.2 Comparison of Safety Approaches

| Approach | Mechanism | Strength | Weakness |
|:--------:|-----------|:--------:|:--------:|
| **Statistical testing** | Test on large dataset; measure accuracy | Quantifiable; standard practice | Cannot prove safety; finite test coverage |
| **Robustness testing** | Test with adversarial inputs; noise; perturbations | Finds fragility | Cannot test all perturbations |
| **Formal verification** | Prove properties of NN mathematically | Guarantees (within scope) | Scalability limited; properties limited |
| **Runtime monitoring** | Check AI behavior at runtime; detect anomalies | Catches unknown failures live | Detection ≠ prevention; latency |
| **Safe envelope** | Restrict AI output to provably safe space | Guaranteed safety within envelope | May be overly conservative |
| **Diverse redundancy** | Multiple independent AI; disagreement → safety action | Common-mode failure resistance | Cost; cannot guarantee true diversity |
| **OOD detection** | Detect when input is outside training distribution | Prevents extrapolation errors | Not all OOD detectors reliable |
| **Simplex architecture** | AI for performance; simple verified controller as backup | Best of both (performance + safety) | Switching logic complexity |

---

## Chapter 7 — Comparison with Traditional Functional Safety

| Aspect | Traditional (ISO 26262/IEC 61508) | AI-Enhanced Safety |
|:------:|:-:|:-:|
| **Specification** | Complete requirements specification | Operational Design Domain (ODD) + performance requirements |
| **Verification** | Requirements-based testing; structural coverage | Statistical testing; robustness testing; adversarial testing |
| **Integrity levels** | SIL 1-4 / ASIL A-D (failure rate-based) | AI safety integrity (performance + robustness-based) |
| **Code review** | Line-by-line inspection | Model interpretability; training pipeline review |
| **Determinism** | Same input → same output (required) | Statistical consistency; bounded uncertainty |
| **Change management** | Impact analysis per code change | Retraining impact analysis; data change impact |
| **Independence** | Independent V&V team | ML V&V expertise required (scarce; may overlap with developers) |
| **Certification evidence** | Test reports; coverage reports; FMEA | Performance reports; bias analysis; robustness evidence; monitoring capability |
| **Lifecycle** | V-model → waterfall-aligned | ML lifecycle: data-centric; iterative; continuous learning possible |
| **Failure modes** | Hardware random + systematic faults | Data-dependent; distribution shift; adversarial; specification incompleteness |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 AI Safety Assurance Framework

```mermaid
flowchart TD
    AI_SYSTEM[AI-Based Safety System]
    
    AI_SYSTEM --> DESIGN_TIME[Design-Time Assurance<br/>━━━━━━━━━━━━━━━━<br/>1. Data quality & governance<br/>2. Model architecture selection<br/>3. Training & validation<br/>4. Robustness testing<br/>5. Performance verification<br/>6. Formal methods (where feasible)<br/>7. Diverse implementation]
    
    AI_SYSTEM --> RUNTIME[Runtime Assurance<br/>━━━━━━━━━━━━━━━━<br/>1. Input monitoring (OOD detection)<br/>2. Confidence checking<br/>3. Output plausibility<br/>4. Cross-model agreement<br/>5. Temporal consistency<br/>6. Degradation detection<br/>7. Safe fallback activation]
    
    AI_SYSTEM --> LIFECYCLE[Lifecycle Assurance<br/>━━━━━━━━━━━━━━━━<br/>1. Post-deployment monitoring<br/>2. Performance drift detection<br/>3. Incident investigation<br/>4. Retraining governance<br/>5. Regression testing<br/>6. OTA update safety<br/>7. Continuous improvement]
    
    DESIGN_TIME --> EVIDENCE[Safety Evidence Package<br/>━━━━━━━━━━━━━━━━<br/>• Data quality report<br/>• V&V test results<br/>• Robustness analysis<br/>• Formal verification results<br/>• SOTIF analysis<br/>• Residual risk assessment]
    
    RUNTIME --> EVIDENCE
    LIFECYCLE --> EVIDENCE
    
    EVIDENCE --> CERT[Safety Assessment/Certification<br/>━━━━━━━━━━━━━━━━<br/>ISO 26262 / IEC 61508 / DO-178C<br/>+ ISO 21448 SOTIF<br/>+ ISO/IEC 5469 (AI safety)]
```

### 8.2 Simplex Architecture for AI Safety

```mermaid
graph TB
    INPUT[Sensor Input]
    
    INPUT --> AI[AI Controller<br/>━━━━━━━━━━━<br/>High performance<br/>Deep learning<br/>Handles complex scenarios<br/>May be unpredictable]
    
    INPUT --> SIMPLE[Verified Controller<br/>━━━━━━━━━━━<br/>Simple, deterministic<br/>Formally verified<br/>Limited capability<br/>ALWAYS safe]
    
    AI --> MONITOR{Decision Module<br/>━━━━━━━━━━━<br/>Is AI output<br/>within safe envelope?}
    
    SIMPLE --> MONITOR
    
    MONITOR -->|"AI output SAFE"| USE_AI[Use AI Output<br/>(high performance)]
    MONITOR -->|"AI output UNSAFE<br/>or uncertain"| USE_SIMPLE[Use Verified Controller<br/>(guaranteed safe)]
    
    USE_AI --> ACTUATOR[Actuator Output]
    USE_SIMPLE --> ACTUATOR
```

---

## Chapter 9 — Case Studies

### 9.1 Automotive ADAS: ASIL D Perception with ML

| Aspect | Detail |
|--------|--------|
| **System** | Autonomous Emergency Braking (AEB) using camera + radar + ML object detection; ASIL D |
| **Challenge** | ML perception is the critical path: if ML misses pedestrian → no braking → fatality |
| **Safety architecture** | Simplex approach: (1) ML perception (camera DNN) = high performance channel. (2) Radar detection = independent verified channel (deterministic processing). (3) Decision logic: if EITHER channel detects obstacle in collision path → brake. (4) ML failure mode: missed detection (false negative) → radar provides independent detection. (5) Radar limitation: no classification (can't distinguish pedestrian from sign) → ML provides classification. |
| **SOTIF analysis** | Triggering conditions identified: fog, rain, night, glare, occlusion, unusual pedestrian appearance, road debris. For each: AI performance measured; residual risk calculated; mitigation designed. Example: heavy rain → camera AI detection range drops from 100m to 40m → radar covers gap (100m+ in rain); combined system maintains 80m minimum detection at 120 km/h (acceptable stopping distance). |
| **Testing** | (1) 10M+ annotated images for training (diverse weather, lighting, geography). (2) 500K edge case scenarios (simulation). (3) 1M km real-world testing (shadow mode → record decisions; compare to human). (4) Adversarial robustness: tested against known perturbation types; bounded perturbation ε=0.03 → accuracy maintained >95%. (5) OOD detection: autoencoder-based novelty detector; triggers radar-only mode when input anomalous. |
| **Assurance argument** | "The AEB system achieves ASIL D because: (1) ML perception provides high detection performance (99.5% sensitivity overall). (2) Independent radar channel provides systematic fault tolerance (diverse channel). (3) Runtime monitoring detects ML degradation and activates radar-only mode. (4) SOTIF analysis demonstrates residual risk < 10⁻⁸/h for pedestrian scenarios. (5) Combined architecture achieves safety target through redundancy, not by proving ML perfection." |

### 9.2 Industrial: Safety PLC with AI Anomaly Detection

| Aspect | Detail |
|--------|--------|
| **System** | Chemical plant emergency shutdown system (ESD); SIL 3; traditional PLC-based |
| **AI addition** | ML-based predictive anomaly detection: monitors process data patterns to predict failures 30 minutes before traditional threshold triggers |
| **Architecture decision** | AI does NOT replace SIL 3 shutdown function. AI provides EARLY WARNING only. Traditional PLC shutdown logic unchanged (fully IEC 61508 compliant). If AI predicts anomaly: alert operator; recommend controlled shutdown. If AI misses: traditional threshold-based shutdown still triggers (existing SIL 3 protection). AI failure mode (missed prediction) = loss of early warning benefit only; NOT loss of safety function. |
| **IEC 61508 claim** | AI classified as "non-safety function with safety benefit" — not part of SIL-rated safety function. Safety function (ESD) remains traditional deterministic logic at SIL 3. AI provides operational benefit; its failure is NOT a safety failure. |
| **Lesson** | Most practical approach currently: use AI for ADVISORY/ENHANCEMENT; keep safety function TRADITIONAL (verifiable, deterministic). This avoids the "how to certify ML at SIL 3" problem entirely while gaining AI benefits. |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **ISO/IEC 5469 publication** | 2025-2026 | First international standard for AI functional safety; sector-independent requirements |
| **ISO 26262 3rd edition** | 2026-2028 | Expected to include AI/ML-specific guidance (Part 6 update or new part) |
| **Formal verification for DNN** | 2025-2030 | Scalable formal methods for neural networks; provable robustness bounds |
| **Continuous safety assurance** | 2025-2028 | Real-time safety case monitoring; automated safety argument updating |
| **Foundation model safety** | 2025-2027 | Safety standards for foundation models used in safety-critical applications |
| **Regulatory acceptance** | 2026-2030 | Certification authorities accepting ML in highest safety integrity levels (SIL 3-4 / ASIL D / DAL A) |
| **AI safety testing standards** | 2025-2027 | Standardized test methodologies for AI safety (robustness benchmarks, OOD test suites) |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** Why can't we simply apply ISO 26262 or IEC 61508 directly to AI/ML-based safety systems? What's fundamentally different?

**A:** Traditional functional safety standards (ISO 26262, IEC 61508) were designed for DETERMINISTIC systems where behavior is explicitly programmed and fully specifiable. AI/ML systems break these fundamental assumptions:

(1) **No complete specification**: In ISO 26262, you write requirements ("brake if obstacle within 50m at >30 km/h"). For ML perception: you CANNOT write a complete specification of "what is a pedestrian?" — the model LEARNS this from data. You can't verify ML against a requirement that doesn't exist explicitly.

(2) **Not inspectable**: ISO 26262 requires code review (a human reads the code, checks logic). ML model weights (millions of floating-point numbers) are NOT human-readable. You cannot "review" a neural network the way you review C code.

(3) **Not deterministic**: Traditional safety functions produce the same output for the same input (required by IEC 61508 SIL 3-4). ML may vary slightly due to floating-point ordering, and retraining produces entirely different weights.

(4) **Testing is statistical, not exhaustive**: ISO 26262 structural coverage (MC/DC) proves code paths are exercised. For ML, there's no equivalent "coverage" — you can test millions of images and still miss the edge case that causes a fatality.

(5) **Data-dependent**: ML behavior depends on training DATA, not just code. Data quality is safety-critical in a way that doesn't map to traditional "software development process" requirements.

This is why new standards (ISO 21448 SOTIF, ISO/IEC 5469) are being developed: to provide appropriate assurance approaches for AI characteristics.

### Tier 2: Mid-Level

**Q2:** Explain the Simplex architecture approach to AI safety. When is it appropriate, and what are its limitations?

**A:** The Simplex architecture uses TWO controllers operating in parallel: a high-performance AI controller (complex, potentially unpredictable) and a simple verified controller (limited capability but provably safe). A decision module selects which output to use.

**How it works**: (1) Both controllers receive the same inputs. (2) The AI controller produces the "ideal" output (optimized performance). (3) A safety monitor checks: "Is the AI output within the provably safe envelope?" (4) If YES → use AI output (best performance). (5) If NO → switch to verified controller (guaranteed safe, possibly suboptimal).

**When appropriate**: Systems where there's a clearly definable "safe state" or "safe envelope" that's simpler than the full operating space. Examples: (1) Autonomous vehicle: AI plans optimal route; verified controller enforces "never exceed these acceleration/deceleration/proximity limits." (2) Robot arm: AI optimizes movements; verified controller enforces joint limits and force limits. (3) ADAS: AI detects objects; if uncertain → apply conservative maximum braking (always safe).

**Limitations**: (1) Requires a DEFINABLE safe envelope (not always possible — what's the "safe output" for a medical diagnosis AI?). (2) Switching between controllers may itself be hazardous (discontinuous behavior). (3) The verified controller may be overly conservative (reduces utility of having AI). (4) The safety monitor must itself be correct (single point of failure if monitor has bugs). (5) Cannot handle scenarios where AI must make a CHOICE between unsafe options (trolley problem — no "safe default" exists).

### Tier 3: Senior

**Q3:** You need to argue to a certification authority that an ML-based perception system achieves ASIL D. There is no ISO 26262 Part 6 guidance for ML. Construct the safety argument.

**A:**

**Argument strategy**: ASIL D requires demonstration that systematic faults are adequately controlled AND that residual risk is acceptable. For ML, we cannot use traditional Part 6 methods alone. We construct a COMPOSITE safety argument combining multiple lines of evidence:

**Claim 1: The ML perception system achieves sufficient performance for the safety function.**

Evidence:
- Performance metrics on representative test dataset: sensitivity >99.5%, specificity >99.0% for all relevant object classes
- Performance disaggregated by operational conditions: night (>98%), rain (>97%), fog (>95%), combinations
- Performance measured across entire Operational Design Domain (ODD) — if below threshold in any condition, ODD is restricted
- Test dataset independently curated (not used in training); size: >1M samples; covers edge cases identified via SOTIF analysis

**Claim 2: The ML system is robust against input perturbations and distribution shift.**

Evidence:
- Adversarial robustness testing: certified robust within ε-ball (verified via formal methods for bounded perturbation)
- OOD detection mechanism active: autoencoder reconstruction error; if input anomalous → safety fallback triggered
- Sensor degradation simulation: progressive degradation tested (dirty lens, partial blockage, defocus)
- Real-world edge case corpus: 100K+ edge cases collected from fleet; all correctly handled or safely degraded

**Claim 3: The system architecture provides defense-in-depth against ML failures.**

Evidence:
- Diverse sensor fusion: camera (ML) + radar (classical) + lidar (hybrid) — no single-point ML failure can cause hazard
- Independent safety channel: radar-based collision detection operates independently of camera ML; provides ASIL D-rated fallback detection
- Runtime monitoring: confidence below threshold → safe state (controlled braking or handoff to driver)
- Architectural independence: ML inference on dedicated hardware; safety monitor on separate certified hardware (freedom from interference per ISO 26262 Part 6 §7.4.3)

**Claim 4: The ML development process ensures systematic fault control.**

Evidence:
- Data governance: training data quality requirements; independent annotation; inter-annotator agreement >95%
- Model validation: k-fold cross-validation + independent test set + operational test
- Training process: reproducible; version-controlled; documented hyperparameters
- Change management: any retraining triggers full regression test + safety review
- Toolchain qualification: training framework verified (compiler qualification equivalence argument)

**Claim 5: Lifecycle monitoring maintains safety performance.**

Evidence:
- Post-deployment monitoring system: performance metrics tracked continuously on fleet data
- Drift detection: statistical tests on prediction distribution; alert if shift detected
- Incident response: any perception-related near-miss → root cause analysis → model improvement
- OTA update governance: safety-qualified update process; rollback capability; staged deployment

**Argument pattern**: "The ML perception system achieves ASIL D NOT by proving the ML is perfect, but by demonstrating: (1) high intrinsic performance, (2) robustness to known perturbations, (3) defense-in-depth architecture where no single ML failure is catastrophic, (4) rigorous development process controlling systematic faults in data and training, (5) continuous assurance throughout operational life."

---

## Chapter 12 — Cheat Sheet & Quick Reference

```
═══════════════════════════════════════════
AI FUNCTIONAL SAFETY — QUICK REFERENCE
═══════════════════════════════════════════

THE FUNDAMENTAL PROBLEM:
  Traditional safety: deterministic, specifiable, verifiable
  AI/ML:            probabilistic, learned, statistically tested
  
  Standards designed for traditional SW → don't fit ML
  New standards needed: SOTIF, ISO/IEC 5469, IEC TR 63317

═══════════════════════════════════════════
KEY STANDARDS:
  ISO/SAE 21448 (SOTIF):    AI perception limits in vehicles
  ISO/IEC CD 5469:          AI functional safety (under dev)
  IEC TR 63317:             AI in safety-related systems (guidance)
  ISO 26262 + SOTIF:        Complete automotive AI safety
  IEC 61508 + TR 63317:     Industrial AI safety
  EASA AI Roadmap:          Aviation AI (CoLS framework)

═══════════════════════════════════════════
SOTIF SCENARIO AREAS:
  Area 1: Known safe (validated)
  Area 2: Known unsafe (mitigated)
  Area 3: UNKNOWN UNSAFE (reduce this!)
  Area 4: Unknown safe (acceptable)
  
  Goal: Reduce Area 3 below residual risk threshold

═══════════════════════════════════════════
AI SAFETY ARCHITECTURE LAYERS:
  Layer 1: AI model quality (training, validation)
  Layer 2: Runtime monitoring (OOD, confidence, plausibility)
  Layer 3: Safe fallback (deterministic, verified)
  Layer 4: Diverse redundancy (multiple channels)
  
  Safety argument = combination of all layers

═══════════════════════════════════════════
SIMPLEX ARCHITECTURE:
  AI Controller (performance) + Verified Controller (safety)
  Decision Module: if AI within safe envelope → use AI
                   if AI outside/uncertain → use verified
  
  Benefit: performance of AI + safety guarantee of simple system

═══════════════════════════════════════════
AI FAILURE MODES FOR SAFETY:
  Performance:  Misclassification; missed detection; false alarm
  Robustness:   OOD failure; adversarial; edge cases
  Specification: Incomplete training; wrong objective; shift
  Implementation: Training bugs; pipeline errors; numerical

═══════════════════════════════════════════
ASSURANCE APPROACHES:
  Statistical testing:    Large test sets; quantify performance
  Robustness testing:     Adversarial; noise; perturbations
  Formal verification:    Prove properties (scalability limited)
  Runtime monitoring:     Detect failures live; trigger fallback
  Safe envelope:          Constrain AI outputs to safe space
  Diverse redundancy:     Multiple independent channels
  OOD detection:          Detect unfamiliar inputs; refuse

═══════════════════════════════════════════
PRACTICAL STRATEGY (CURRENT STATE OF ART):
  For highest safety integrity (ASIL D / SIL 3-4):
    1. AI for performance (perception, planning)
    2. Non-AI for safety function (deterministic fallback)
    3. Runtime monitoring bridges them
    4. Safety argument: architecture + multi-layer evidence
    
  DO NOT: claim ML alone achieves ASIL D
  DO:     show architecture + process + monitoring + evidence

═══════════════════════════════════════════
CERTIFICATION EVIDENCE FOR AI SAFETY:
  □ Data quality report (provenance, quality, bias)
  □ Model V&V results (accuracy, precision, recall)
  □ Robustness analysis (adversarial, OOD)
  □ SOTIF analysis (triggering conditions, residual risk)
  □ Architecture safety analysis (FMEA with AI failures)
  □ Runtime monitoring specification
  □ Post-deployment monitoring plan
  □ Change management process (retraining governance)
```

---

*End of Document — 06_AI_Safety_Functional_Safety.md*
