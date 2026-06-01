# Safety Methods in Systems Engineering — FHA, PSSA, SSA, CCA

**Standards:** SAE ARP4754A, SAE ARP4761A, DO-178C, DO-254, ISO 26262, IEC 61508, MIL-STD-882E  
**Methods:** FHA, PSSA, SSA, CCA (Common Cause Analysis), FTA, FMEA/FMECA, ZSA, PRA, STPA  
**SDOs:** SAE International, FAA/EASA (aerospace), ISO/IEC (automotive/industrial), US DoD (military)  
**Audience:** Safety engineers, systems engineers, certification engineers (DER/CVE), ASIL assessors, SIL assessors  
**Prerequisites:** V-Model understanding; basic probability theory; familiarity with safety standards (ISO 26262 or DO-178C)

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Milestone |
|------|-----------|
| 1949 | First FMEA (US military procedure MIL-P-1629) |
| 1962 | Fault Tree Analysis (FTA) developed at Bell Labs for Minuteman missile |
| 1969 | MIL-STD-882 — System Safety Program Requirements (DoD) |
| 1974 | WASH-1400 (NUREG-75/014) — Reactor Safety Study (PRA for nuclear) |
| 1996 | **SAE ARP4754** — Certification Considerations for Highly-Integrated or Complex Aircraft Systems |
| 1996 | **SAE ARP4761** — Guidelines and Methods for Conducting the Safety Assessment Process |
| 2000 | IEC 61508 — Functional Safety of E/E/PE Systems (SIL concept) |
| 2010 | **SAE ARP4754A** — current revision (enhanced system development assurance) |
| 2011 | **ISO 26262:2011** — Functional Safety for Automotive (ASIL concept; HARA) |
| 2012 | MIL-STD-882E — Standard Practice for System Safety |
| 2018 | ISO 26262:2018 (2nd edition; adds motorcycles, trucks, semiconductor guidelines) |
| 2019 | ISO/PAS 21448 (SOTIF) — Safety of the Intended Functionality |
| 2021 | **SAE ARP4761A** — current revision (adds MBSA, STPA, expanded CCA) |
| 2023 | DO-178C + ML supplement (emerging; AI/ML in safety-critical aviation) |

### 1.2 The Safety Assessment Paradigm

**Core philosophy:** You cannot TEST safety into a system. You must DESIGN it in from the beginning by:
1. **Identifying hazards** early (before design exists)
2. **Assessing risk** (severity × probability)
3. **Allocating safety requirements** (to mitigate risk)
4. **Verifying** that safety requirements are satisfied
5. **Validating** that residual risk is acceptable

**The three pillars of safety assessment (ARP4761A):**
- **FHA** (Functional Hazard Assessment) — top-level; identifies failure conditions
- **PSSA** (Preliminary System Safety Assessment) — design-time; evaluates proposed architecture
- **SSA** (System Safety Assessment) — verification; confirms implementation meets safety targets

---

## Chapter 2 — Safety Assessment Framework (ARP4754A/4761A)

### 2.1 Safety Assessment Process Overview

```mermaid
graph TB
    subgraph "Safety Assessment Process (ARP4754A + ARP4761A)"
        subgraph "Development Lifecycle (Left Side of V)"
            FD[Aircraft/System Function<br/>Development]
            ARCH_DEV[System Architecture<br/>Development]
            IMPL_DEV[Implementation<br/>(HW/SW Development)]
        end
        
        subgraph "Safety Assessment (parallel to development)"
            FHA_P[FHA<br/>Functional Hazard Assessment<br/>━━━━━━━━━━━<br/>• Identify functions<br/>• Identify failure conditions<br/>• Classify severity<br/>• Assign DAL<br/>━━━━━━━━━━━<br/>Output: Failure conditions + DALs]
            
            PSSA_P[PSSA<br/>Preliminary System Safety Assessment<br/>━━━━━━━━━━━<br/>• Evaluate architecture<br/>• FTA (top-down)<br/>• FMEA (bottom-up)<br/>• CCA (common cause)<br/>• Allocate probabilities<br/>━━━━━━━━━━━<br/>Output: Safety requirements;<br/>architecture validation]
            
            SSA_P[SSA<br/>System Safety Assessment<br/>━━━━━━━━━━━<br/>• Verify implementation<br/>• Confirm FTA probabilities<br/>• Validate FMEA coverage<br/>• Complete CCA<br/>• Confirm residual risk<br/>━━━━━━━━━━━<br/>Output: Safety case;<br/>compliance demonstration]
        end
        
        subgraph "Common Cause Analysis (CCA)"
            ZSA_P[ZSA<br/>Zonal Safety Analysis<br/>━━━━━━━━━━━<br/>• Physical proximity<br/>• Environmental threats]
            PRA_P[PRA<br/>Particular Risks Analysis<br/>━━━━━━━━━━━<br/>• Fire; bird strike; HIRF;<br/>  lightning; burst rotor]
            CMA_P[CMA<br/>Common Mode Analysis<br/>━━━━━━━━━━━<br/>• Design errors; mfg defects<br/>• Maintenance errors;<br/>  software errors]
        end
    end
    
    FD --> FHA_P
    FHA_P -->|"safety requirements"| ARCH_DEV
    ARCH_DEV --> PSSA_P
    PSSA_P -->|"derived safety req"| IMPL_DEV
    IMPL_DEV --> SSA_P
    
    PSSA_P --> ZSA_P
    PSSA_P --> PRA_P
    PSSA_P --> CMA_P
```

### 2.2 Relationship: FHA → PSSA → SSA

| Phase | When | Input | Method | Output |
|:-----:|:----:|:-----:|:------:|--------|
| **FHA** | Early (before architecture) | System functions; operational context | Functional analysis; HAZOP-like | Failure conditions; severity classification; DAL assignment |
| **PSSA** | During architecture design | FHA results + proposed architecture | FTA; FMEA; CCA; Markov; dependency diagrams | Safety requirements; architecture adequacy assessment |
| **SSA** | After implementation (right side of V) | PSSA results + implementation data | Updated FTA/FMEA; test results; field data | Safety case; compliance demonstration; residual risk statement |

---

## Chapter 3 — Functional Hazard Assessment (FHA)

### 3.1 FHA Process

```mermaid
graph TB
    subgraph "FHA Process"
        F1[Step 1: Identify System Functions<br/>━━━━━━━━━━━<br/>• What does the system DO?<br/>• List all functions (normal operation)<br/>• Include all phases of flight/operation]
        
        F2[Step 2: Identify Failure Conditions<br/>━━━━━━━━━━━<br/>• For each function: what if it FAILS?<br/>• Loss of function<br/>• Malfunction (wrong output)<br/>• Inadvertent operation<br/>• Combination failures]
        
        F3[Step 3: Classify Severity<br/>━━━━━━━━━━━<br/>• Catastrophic (loss of aircraft)<br/>• Hazardous (large reduction in safety)<br/>• Major (significant reduction)<br/>• Minor (slight reduction)<br/>• No Safety Effect]
        
        F4[Step 4: Assign DAL and Probability<br/>━━━━━━━━━━━<br/>• Catastrophic → DAL A; ≤ 10⁻⁹/FH<br/>• Hazardous → DAL B; ≤ 10⁻⁷/FH<br/>• Major → DAL C; ≤ 10⁻⁵/FH<br/>• Minor → DAL D; no specific prob<br/>• No Effect → DAL E]
        
        F5[Step 5: Document & Validate<br/>━━━━━━━━━━━<br/>• FHA worksheet/table<br/>• Review with flight crew; operations<br/>• Certification authority (DER) review]
    end
    
    F1 --> F2 --> F3 --> F4 --> F5
```

### 3.2 FHA Classification Table (Aerospace — AMC 25.1309)

| Classification | Effect on Aircraft | Effect on Crew | Effect on Passengers | Probability Target |
|:-:|---|---|---|:---:|
| **Catastrophic** | Loss of aircraft; prevented from continued safe flight and landing | Fatal injuries; incapacitation | Fatal injuries | ≤ $10^{-9}$ per FH |
| **Hazardous** | Large reduction in functional capabilities or safety margins | Physical distress; excessive workload; injuries | Serious injuries; some fatalities | ≤ $10^{-7}$ per FH |
| **Major** | Significant reduction in capabilities or margins | Physical discomfort; significant increase in workload | Physical discomfort | ≤ $10^{-5}$ per FH |
| **Minor** | Slight reduction | Slight increase in workload | Inconvenience | No specific target |
| **No Safety Effect** | No effect on safety | None | None | N/A |

### 3.3 FHA Worksheet Example

| Function | Failure Condition | Phase | Severity | Probability Target | DAL | Rationale |
|:--------:|:-:|:---:|:---:|:---:|:---:|---|
| Provide pitch control | Loss of all pitch control | All | Catastrophic | ≤ $10^{-9}$/FH | A | Total loss of pitch → loss of aircraft |
| Provide pitch control | Hardover (uncommanded full nose-down) | Cruise | Catastrophic | ≤ $10^{-9}$/FH | A | Insufficient time to recover |
| Provide pitch control | Reduced authority | Approach | Major | ≤ $10^{-5}$/FH | C | Degraded handling; pilot compensates |
| Display airspeed | Loss of airspeed display | Approach | Hazardous | ≤ $10^{-7}$/FH | B | Loss of critical speed info during critical phase |
| Display airspeed | Misleading (incorrect) airspeed | Cruise | Hazardous | ≤ $10^{-7}$/FH | B | Crew may stall or overspeed without knowing |

---

## Chapter 4 — PSSA (Preliminary System Safety Assessment)

### 4.1 PSSA Purpose and Methods

| Aspect | Detail |
|:------:|--------|
| **When** | After architecture is proposed; iterative with architecture development |
| **Purpose** | Evaluate if the proposed architecture can meet the FHA safety targets (probability/DAL) |
| **Key question** | "Is this architecture SAFE ENOUGH?" — can it achieve the required failure probability? |
| **Input** | FHA results + system/subsystem architecture |
| **Output** | Derived safety requirements; architecture adequacy confirmation; residual risk assessment |

### 4.2 PSSA Methods

| Method | Direction | What It Does | When to Use |
|:------:|:---------:|---|---|
| **FTA (Fault Tree Analysis)** | Top-down | Starts from failure condition; decomposes into contributing causes using AND/OR gates | Primary method for probability assessment; shows how failures propagate |
| **FMEA/FMECA** | Bottom-up | Starts from component failures; traces effects upward to system level | Comprehensive component-level analysis; identifies single-point failures |
| **DD (Dependency Diagram)** | Top-down | Reliability block diagram; shows redundancy paths | Quick assessment of redundant architectures |
| **Markov Analysis** | State-based | Models system states and transitions; handles sequence-dependent failures | Standby redundancy; latent failures; complex failure/repair sequences |
| **CCA (Common Cause Analysis)** | Cross-cutting | Identifies events that can fail multiple independent channels | Validates independence assumptions in FTA/DD |

### 4.3 Fault Tree Analysis (FTA)

```mermaid
graph TB
    subgraph "Example FTA: Loss of Braking"
        TOP[Loss of Normal Braking<br/>P ≤ 10⁻⁵/FH (Major)]
        
        OR1{OR Gate}
        
        A1[Loss of Hydraulic<br/>Pressure to Brakes]
        A2[Loss of Brake<br/>Control Electronics]
        A3[Mechanical Brake<br/>Failure]
        
        AND1{AND Gate<br/>(dual hydraulic)}
        
        B1[Loss of Hydraulic<br/>System 1<br/>P = 10⁻³/FH]
        B2[Loss of Hydraulic<br/>System 2<br/>P = 10⁻³/FH]
        
        OR2{OR Gate}
        
        C1[BSCU Channel A<br/>Failure<br/>P = 10⁻⁴/FH]
        C2[BSCU Channel B<br/>Failure<br/>P = 10⁻⁴/FH]
        AND2{AND Gate<br/>(dual channel)}
    end
    
    TOP --> OR1
    OR1 --> A1
    OR1 --> A2
    OR1 --> A3
    A1 --> AND1
    AND1 --> B1
    AND1 --> B2
    A2 --> AND2
    AND2 --> C1
    AND2 --> C2
```

**FTA probability calculations:**

$$P(\text{OR gate}) = P(A) + P(B) - P(A) \cdot P(B) \approx P(A) + P(B) \text{ (for small probabilities)}$$

$$P(\text{AND gate}) = P(A) \cdot P(B) \text{ (independent events)}$$

**Example calculation:**
- $P(\text{Loss of Hydraulic}) = P(\text{Sys1}) \times P(\text{Sys2}) = 10^{-3} \times 10^{-3} = 10^{-6}$
- $P(\text{Loss of Electronics}) = P(\text{Ch A}) \times P(\text{Ch B}) = 10^{-4} \times 10^{-4} = 10^{-8}$
- $P(\text{Mechanical}) = 10^{-6}$ (assumed)
- $P(\text{Loss of Braking}) = 10^{-6} + 10^{-8} + 10^{-6} \approx 2 \times 10^{-6} < 10^{-5}$ ✓ MEETS TARGET

### 4.4 FMEA/FMECA

| Column | Content | Example |
|:------:|---------|---------|
| **Item** | Component being analyzed | Hydraulic pump #1 |
| **Function** | What it does | Provides hydraulic pressure (3000 psi) to brake system |
| **Failure Mode** | How it can fail | Loss of output pressure |
| **Failure Cause** | Root cause | Seal degradation; cavitation; motor failure |
| **Local Effect** | Effect on immediate subsystem | No pressure in hydraulic line 1 |
| **System Effect** | Effect on system | System switches to hydraulic system 2 (redundant); degraded capability |
| **End Effect** | Effect on aircraft/vehicle | No immediate effect (redundancy active); maintenance action required |
| **Severity** | Classification | Minor (with redundancy); Major (if both systems fail) |
| **Detection** | How detected | Low-pressure warning; BITE; crew annunciation |
| **Probability** | Failure rate | $5 \times 10^{-5}$ per FH |
| **RPN / Criticality** | Risk Priority Number (FMECA) | Severity × Occurrence × Detection |
| **Mitigation** | Safety mechanism | Dual hydraulic system; accumulator for emergency braking |

---

## Chapter 5 — Common Cause Analysis (CCA)

### 5.1 CCA Overview

| Aspect | Detail |
|:------:|--------|
| **Why** | FTA assumes independent failures. CCA validates this assumption by looking for common events that could fail MULTIPLE supposedly-independent channels. |
| **When** | During PSSA (preliminary) and SSA (final verification) |
| **Output** | Identification of common causes; requirements to prevent/mitigate them |
| **Subanalyses** | ZSA (Zonal Safety Analysis), PRA (Particular Risks Analysis), CMA (Common Mode Analysis) |

### 5.2 CCA Sub-Analyses

```mermaid
graph TB
    subgraph "Common Cause Analysis (CCA)"
        CCA_TOP[CCA<br/>━━━━━━━━━━━<br/>Validates independence<br/>assumptions in FTA/DD]
        
        ZSA[ZSA — Zonal Safety Analysis<br/>━━━━━━━━━━━<br/>• Analyze each PHYSICAL ZONE<br/>• Identify items in same zone<br/>• What if zone-wide event?<br/>  (fire; decompression; overheat)<br/>• Are independent channels<br/>  in same zone? → PROBLEM]
        
        PRA[PRA — Particular Risks Analysis<br/>━━━━━━━━━━━<br/>• Specific external threats:<br/>  - Bird strike<br/>  - Uncontained engine failure<br/>  - Lightning/HIRF<br/>  - Fire<br/>  - Burst tire/wheel<br/>  - Hail/ice<br/>• Can threat defeat multiple<br/>  independent channels?]
        
        CMA[CMA — Common Mode Analysis<br/>━━━━━━━━━━━<br/>• Design errors (same HW design<br/>  used in both channels)<br/>• Manufacturing defects (same batch)<br/>• Maintenance errors (same procedure<br/>  on both channels same day)<br/>• Software errors (identical SW<br/>  in redundant channels)<br/>• Environmental (same specification)]
    end
    
    CCA_TOP --> ZSA
    CCA_TOP --> PRA
    CCA_TOP --> CMA
```

### 5.3 CCA Examples

| Analysis | Finding | Mitigation |
|:--------:|---------|------------|
| **ZSA** | Both hydraulic lines (System 1 and System 2) routed through same wheel well → single fire could sever both | Reroute System 2 through different zone; add fire suppression; add thermal shielding |
| **ZSA** | Primary and backup flight computers in same avionics bay → single cooling failure could overheat both | Separate bays; or dissimilar cooling (air + liquid) |
| **PRA** | Uncontained engine rotor burst trajectory intersects both hydraulic reservoirs → loss of both hydraulic systems | Reroute hydraulic lines outside burst zone; add ballistic protection; add third system (different routing) |
| **PRA** | HIRF (High Intensity Radiated Fields) can corrupt both flight computers simultaneously → common mode electromagnetic upset | EMI shielding (Level A per DO-160G Section 20); HIRF-resistant protocols (CRC + temporal redundancy) |
| **CMA** | Same RTOS used in both flight control channels → software defect would affect both simultaneously → common mode software failure | Dissimilar software (different algorithms; different programming teams; different compilers) OR formal verification for shared RTOS |
| **CMA** | Same maintenance crew services both engines on same day → same error could affect both | Split maintenance schedule; different crews for left/right engine; independent inspection |

---

## Chapter 6 — Automotive Safety Methods (ISO 26262)

### 6.1 HARA (Hazard Analysis and Risk Assessment)

```mermaid
graph TB
    subgraph "ISO 26262 HARA Process"
        H1[Step 1: Define Item<br/>━━━━━━━━━━━<br/>• Item boundary<br/>• Functions<br/>• Operating conditions]
        
        H2[Step 2: Situation Analysis<br/>━━━━━━━━━━━<br/>• Operational situations<br/>• Operating modes<br/>• Environmental conditions]
        
        H3[Step 3: Hazard Identification<br/>━━━━━━━━━━━<br/>• For each function:<br/>  - Malfunction<br/>  - Loss of function<br/>  - Unintended function<br/>• In each situation]
        
        H4[Step 4: Classify Hazardous Events<br/>━━━━━━━━━━━<br/>• Severity (S0-S3)<br/>• Exposure (E0-E4)<br/>• Controllability (C0-C3)]
        
        H5[Step 5: Determine ASIL<br/>━━━━━━━━━━━<br/>• ASIL = f(S, E, C)<br/>• QM, A, B, C, D<br/>• ASIL D = highest integrity]
        
        H6[Step 6: Define Safety Goals<br/>━━━━━━━━━━━<br/>• One safety goal per hazardous event<br/>• High-level safety requirement<br/>• ASIL tagged]
    end
    
    H1 --> H2 --> H3 --> H4 --> H5 --> H6
```

### 6.2 ASIL Determination Matrix

| | C1 (Simply controllable) | C2 (Normally controllable) | C3 (Difficult to control) |
|:---:|:---:|:---:|:---:|
| **S1 + E1** | QM | QM | QM |
| **S1 + E2** | QM | QM | QM |
| **S1 + E3** | QM | QM | A |
| **S1 + E4** | QM | A | B |
| **S2 + E1** | QM | QM | QM |
| **S2 + E2** | QM | QM | A |
| **S2 + E3** | QM | A | B |
| **S2 + E4** | A | B | C |
| **S3 + E1** | QM | QM | A |
| **S3 + E2** | QM | A | B |
| **S3 + E3** | A | B | C |
| **S3 + E4** | B | C | D |

### 6.3 Automotive vs. Aerospace Safety Comparison

| Aspect | ISO 26262 (Automotive) | ARP4754A/4761 (Aerospace) |
|:------:|:---:|:---:|
| **Risk classification** | ASIL (QM, A, B, C, D) | DAL (E, D, C, B, A) |
| **Risk parameters** | Severity × Exposure × Controllability | Severity (failure condition) × Probability |
| **Hazard analysis** | HARA (per item; per situation) | FHA (per aircraft function; per flight phase) |
| **Architecture analysis** | FMEA; FTA; DFA | FTA; FMEA; DD; Markov; CCA |
| **Common cause** | DFA (Dependent Failure Analysis) | CCA (ZSA + PRA + CMA) |
| **Probability targets** | Qualitative (ASIL-dependent rigor) | Quantitative ($10^{-9}$, $10^{-7}$, $10^{-5}$ per FH) |
| **Verification (SW)** | ASIL D: MC/DC; ASIL B: branch coverage | DAL A: MC/DC; DAL B: DC; DAL C: SC |
| **Independence** | ASIL D: independent confirmation (different person) | DAL A/B: independent V&V (different team/org) |
| **Certification authority** | Self-certification (manufacturer declares; type approval by technical service) | Certification authority (FAA/EASA) with DER/CVE review |

---

## Chapter 7 — STPA (Systems-Theoretic Process Analysis)

### 7.1 Overview

| Aspect | Detail |
|:------:|--------|
| **Creator** | Nancy Leveson (MIT); based on STAMP (Systems-Theoretic Accident Model and Processes) |
| **Why** | Traditional methods (FTA, FMEA) focus on component FAILURES. Modern accidents often caused by unsafe INTERACTIONS between components that individually work correctly. |
| **Approach** | Model system as control structure; analyze how control actions can be unsafe |
| **Included in** | ARP4761A (2021) adds STPA as recognized method alongside FTA/FMEA |
| **Applications** | Aviation, automotive (SOTIF/ISO 21448), railway, nuclear, medical, autonomous systems |

### 7.2 STPA Process

```mermaid
graph TB
    subgraph "STPA (Systems-Theoretic Process Analysis)"
        S1[Step 1: Define Losses & Hazards<br/>━━━━━━━━━━━<br/>• Losses: unacceptable outcomes<br/>  (death, injury, damage, mission failure)<br/>• System-level hazards:<br/>  unsafe system states/conditions]
        
        S2[Step 2: Model Control Structure<br/>━━━━━━━━━━━<br/>• Controllers (human; automated)<br/>• Control actions (commands)<br/>• Feedback<br/>• Controlled processes<br/>• Process models (mental models)]
        
        S3[Step 3: Identify Unsafe Control Actions<br/>━━━━━━━━━━━<br/>For each control action, 4 types:<br/>• Not provided (when needed)<br/>• Provided (when NOT needed)<br/>• Wrong timing/order<br/>• Wrong duration/magnitude]
        
        S4[Step 4: Identify Loss Scenarios<br/>━━━━━━━━━━━<br/>• WHY would the UCA occur?<br/>• Controller flaws (incorrect algorithm;<br/>  inadequate process model)<br/>• Feedback inadequacies (missing;<br/>  delayed; incorrect feedback)<br/>• Actuator failures<br/>• External disturbances]
    end
    
    S1 --> S2 --> S3 --> S4
```

### 7.3 STPA vs FTA/FMEA

| Criterion | FTA | FMEA | STPA |
|:---------:|:---:|:----:|:----:|
| **Paradigm** | Component failure propagation | Component failure effects | Unsafe control interactions |
| **Direction** | Top-down (from failure condition) | Bottom-up (from component) | Top-down (from losses/hazards) |
| **Handles software?** | Poorly (SW doesn't "fail" randomly) | Poorly | Well (unsafe algorithms; wrong models) |
| **Handles human error?** | Poorly | Poorly | Well (inadequate mental models) |
| **Handles interactions?** | Limited (event combinations only) | Limited (single failures) | Core strength (control + feedback loops) |
| **Quantitative?** | Yes (probabilities) | Yes (RPN; failure rates) | Qualitative (no probabilities) |
| **Best for** | Hardware redundancy analysis | Hardware component analysis | Software; AI/ML; human-automation interaction; SoS |

---

## Chapter 8 — Architecture Diagrams

### 8.1 Complete Safety Assessment V-Model

```mermaid
graph TB
    subgraph "Safety Assessment in V-Model (ARP4754A)"
        subgraph "Left Side (Development)"
            L1[Aircraft-Level<br/>Function Development]
            L2[System-Level<br/>Architecture]
            L3[Sub-System / Item<br/>Design]
            L4[HW/SW<br/>Implementation]
        end
        
        subgraph "Safety Assessment (Parallel)"
            SA1[Aircraft FHA<br/>━━━━━━━━━━<br/>• Aircraft functions<br/>• Failure conditions<br/>• DAL assignment]
            SA2[System FHA + PSSA<br/>━━━━━━━━━━<br/>• System functions<br/>• FTA/FMEA<br/>• CCA (ZSA/PRA/CMA)<br/>• Derived safety req]
            SA3[Item FHA + PSSA<br/>━━━━━━━━━━<br/>• Item-level analysis<br/>• HW/SW safety req<br/>• FMEA details]
            SA4[SSA<br/>━━━━━━━━━━<br/>• Final FTA (actual data)<br/>• Confirm probabilities<br/>• Complete CCA<br/>• Safety case]
        end
        
        subgraph "Right Side (Verification)"
            R4[Unit/Component<br/>Verification]
            R3[Item Integration<br/>& Verification]
            R2[System Integration<br/>& Verification]
            R1[Aircraft Integration<br/>& Validation]
        end
    end
    
    L1 --> L2 --> L3 --> L4
    L4 --> R4 --> R3 --> R2 --> R1
    
    L1 --- SA1
    L2 --- SA2
    L3 --- SA3
    R2 --- SA4
    
    SA1 -->|"safety req"| L2
    SA2 -->|"derived safety req"| L3
    SA3 -->|"HW/SW safety req"| L4
    SA4 -->|"validates"| R1
```

### 8.2 ISO 26262 Safety Lifecycle

```mermaid
graph TB
    subgraph "ISO 26262 Safety Lifecycle"
        subgraph "Concept Phase (Part 3)"
            ITEM_DEF[Item Definition<br/>• Item boundary; interfaces<br/>• Functions; operating conditions]
            HARA_P[HARA<br/>• Hazard identification<br/>• S, E, C classification<br/>• ASIL determination<br/>• Safety goals]
            FSC_P[Functional Safety Concept<br/>• Functional safety requirements<br/>• Allocation to elements<br/>• Warning/degradation concepts]
        end
        
        subgraph "Product Development (Part 4-6)"
            TSC_P[Technical Safety Concept (Part 4)<br/>• Technical safety requirements<br/>• System architecture<br/>• Safety mechanisms<br/>• DFA (dependent failure analysis)]
            HW_P[HW Development (Part 5)<br/>• HW design; safety mechanisms<br/>• Quantitative analysis (FMEDA)<br/>• Hardware metrics (SPFM, LFM, PMHF)]
            SW_P[SW Development (Part 6)<br/>• SW safety requirements<br/>• Architecture (FFI, monitoring)<br/>• Design & implementation (MISRA)<br/>• Verification (coverage per ASIL)]
        end
        
        subgraph "Verification & Validation (Part 4)"
            INTEG[System Integration & Testing<br/>• HW-SW integration<br/>• Safety mechanism testing<br/>• Fault injection]
            SAFETY_VAL_P[Safety Validation<br/>• Validation of safety goals<br/>• Vehicle-level testing<br/>• Field monitoring plan]
            CONF[Confirmation Review<br/>• Functional safety assessment<br/>• Confirmation of safety case<br/>• Release for production]
        end
    end
    
    ITEM_DEF --> HARA_P --> FSC_P --> TSC_P
    TSC_P --> HW_P
    TSC_P --> SW_P
    HW_P --> INTEG
    SW_P --> INTEG
    INTEG --> SAFETY_VAL_P --> CONF
```

---

## Chapter 9 — Case Studies

### 9.1 Aerospace: Flight Control System Safety Assessment

| Aspect | Detail |
|--------|--------|
| **System** | Fly-by-wire flight control system (FCS); quad-redundant; active-active |
| **FHA findings** | Loss of all pitch control → Catastrophic ($\leq 10^{-9}$/FH). Hardover (uncommanded) → Catastrophic. Oscillatory failure → Hazardous ($\leq 10^{-7}$/FH). |
| **PSSA (FTA)** | Top event: "Loss of all pitch control." Architecture: 4 independent flight control computers (FCC); any 1 sufficient. FTA: $P(\text{top}) = P(\text{FCC1}) \times P(\text{FCC2}) \times P(\text{FCC3}) \times P(\text{FCC4})$. With $P(\text{FCC}) = 10^{-4}$/FH: $P(\text{top}) = (10^{-4})^4 = 10^{-16}$/FH. Far exceeds $10^{-9}$ target. BUT: this assumes independence! |
| **CCA findings** | ZSA: All 4 FCC in same avionics bay → fire could destroy all. Mitigation: FCCs split across 2 bays; fire-resistant housing. PRA: Bird strike trajectory could sever 2 control rod paths. Mitigation: reroute rods; add electronic backup path. CMA: Same software in all 4 FCCs → software defect is common mode. Mitigation: Dissimilar pair (2 FCCs use Software A; 2 use Software B; different teams/languages). |
| **SSA verification** | After implementation: confirmed failure rates from component test data; confirmed CCA mitigations implemented; flight test confirmed failure detection < 40ms; safety case accepted by DER. |
| **Key lesson** | Without CCA, the FTA showed $10^{-16}$ (far exceeds requirement). WITH CCA, identified common modes that could reduce this to $10^{-4}$ (single fire could kill all 4). CCA is CRITICAL — it validates independence assumptions. |

### 9.2 Automotive: Electric Power Steering (EPS) Safety

| Aspect | Detail |
|--------|--------|
| **System** | Column-type EPS; ASIL D (loss of steering assist = S3, E4, C3) |
| **HARA** | Hazard: Unintended steering torque during highway driving. S=3 (potential head-on collision); E=4 (highway driving = high exposure); C=3 (high speed = difficult to control). → ASIL D. Safety Goal: "EPS shall not apply unintended steering torque exceeding ±3 Nm for more than 100ms." |
| **Architecture safety concept** | Dual-channel monitoring: Channel A (main) computes torque; Channel B (monitoring) independently computes expected torque range. If A-B disagreement > threshold for > 100ms → shutdown torque (safe state). ASIL D decomposed: Channel A = ASIL D(D); Channel B = ASIL D(D). Both must be ASIL D because: single-channel failure must NOT cause unintended torque. |
| **DFA (ISO 26262 CCA equivalent)** | Dependent failures identified: (1) Same MCU family (same silicon bug) → different MCU families for A/B. (2) Same power supply → independent voltage regulators with cross-monitoring. (3) Same temperature sensor → separate sensors; plausibility check. (4) Same motor driver → dual H-bridge with independent enable; both must agree to apply torque. |
| **Quantitative analysis (Part 5)** | FMEDA: SPFM = 99.2% (target ≥ 99%); LFM = 92% (target ≥ 90%); PMHF = $4.2 \times 10^{-8}$ FIT (target < $10^{-7}$). All metrics pass. |
| **Verification** | Fault injection testing: 2,000 faults injected (stuck-at, transient, drift); all detected within 50ms; safe state achieved within 100ms. Residual: 3 undetected faults identified → additional diagnostic added. Final: 100% fault detection for single-point faults. |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **MBSA (Model-Based Safety Assessment)** | Now-2027 | Safety analysis from architecture models (SysML + AltaRica/HiP-HOPS); auto-generate FTA/FMEA from model |
| **STPA mainstreaming** | 2021+ (now) | ARP4761A includes STPA; automotive adopting for SOTIF (ISO 21448); supplements FTA/FMEA |
| **AI/ML safety** | 2024-2030 | How to certify ML-based systems? No clear failure modes. EASA/FAA developing guidance; ISO/PAS 8800 (automotive AI safety) |
| **Digital twin for safety** | 2025-2030 | Continuous safety assessment using digital twin (real-time FMEA; predictive safety monitoring) |
| **Automated safety analysis** | 2024-2028 | AI generates FTA/FMEA from architecture descriptions; checks completeness; identifies gaps |
| **Safety + security convergence** | Now (ongoing) | Cyber-physical attacks as safety threats; ISO/SAE 21434 + ISO 26262 interaction; threat = failure mode |
| **Assurance cases (GSN)** | Expanding | Goal Structuring Notation for safety arguments; machine-readable safety cases; automated compliance checking |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is the difference between FHA, PSSA, and SSA?

**A:**

| | FHA | PSSA | SSA |
|:---:|:---:|:---:|:---:|
| **When** | Early (before design) | During design | After implementation |
| **Question answered** | "What can go wrong and how bad is it?" | "Is the proposed design safe enough?" | "Does the built system actually meet safety targets?" |
| **Input** | System functions; operational context | FHA results + proposed architecture | PSSA results + actual implementation data |
| **Methods** | Functional analysis; brainstorming; HAZOP-like | FTA; FMEA; CCA; Markov | Updated FTA/FMEA with real failure data; test results |
| **Output** | Failure conditions; severity; DAL/ASIL | Safety requirements; architecture adequacy | Safety case; compliance demonstration |
| **Analogy** | "What could go wrong?" (identify the danger) | "Will this blueprint survive the danger?" (check the plan) | "Did we actually build it to survive?" (verify the product) |

### Tier 2: Mid-Level

**Q2:** Explain Common Cause Analysis (CCA). Why is it critical even when FTA shows very low failure probability?

**A:**

**The problem CCA solves:**
FTA calculates probability assuming INDEPENDENT failures:
- $P(\text{dual channel loss}) = P(\text{Ch1}) \times P(\text{Ch2}) = 10^{-4} \times 10^{-4} = 10^{-8}$

**But independence is an ASSUMPTION!** If a single event can fail BOTH channels, then:
- $P(\text{dual channel loss due to common cause}) = P(\text{common event}) = 10^{-3}$ (much worse!)

**CCA validates the independence assumption** by asking:
1. **ZSA:** "Are both channels physically close enough that a single zone event (fire, flood, overheat) could affect both?"
2. **PRA:** "Could a specific external threat (bird strike, HIRF, lightning) reach both channels?"
3. **CMA:** "Do both channels share design, software, maintenance, or manufacturing commonalities?"

**Example:** FCS with 2 redundant computers:
- FTA says: $P(\text{loss of both}) = 10^{-5} \times 10^{-5} = 10^{-10}$ → seems extremely safe
- CMA finds: both computers run same software → same bug would crash both simultaneously
- True probability: $P(\text{SW bug triggering failure}) = 10^{-4}$ (common mode) → NOT $10^{-10}$!
- Mitigation: dissimilar software (N-version programming) or formal verification

**Without CCA:** You'd believe the system is 1000× safer than it actually is. CCA finds the "hidden" failure paths that destroy redundancy.

### Tier 3: Senior

**Q3:** Design the safety assessment strategy for an autonomous driving system (SAE Level 4). How do you handle the limitations of traditional methods (FTA/FMEA) for software-intensive, AI-based systems?

**A:**

**Challenge:** Traditional FTA/FMEA assumes:
- Components have defined failure MODES (hardware yes; software NO — software executes its logic; bugs are design errors, not random failures)
- Failures are RANDOM (hardware yes; AI performance is scenario-dependent, not random)
- System behavior is DECOMPOSABLE (simple systems yes; emergent behavior of AI = NOT decomposable)

**Multi-method strategy:**

| Safety Concern | Method | Why This Method |
|:---:|:---:|---|
| **Hardware failures** (sensors, actuators, compute) | Traditional FTA + FMEA + FMEDA | Hardware has random failure modes; well-understood |
| **Systematic SW failures** (bugs in deterministic code) | STPA + formal verification + static analysis | STPA captures unsafe interactions; formal methods prove correctness for critical modules |
| **AI/ML failures** (perception errors; wrong decisions) | SOTIF (ISO 21448) + STPA + scenario-based testing | SOTIF addresses performance limitations; STPA handles unsafe control; scenario testing explores ODD boundaries |
| **Common cause** (HW+SW+AI interactions) | Extended CCA + DFA + independence argument | Must prove: HW fault doesn't corrupt AI; AI error doesn't bypass safety monitoring |
| **Sensor fusion errors** | STPA (inadequate feedback) + simulation | STPA captures: what if sensor gives incorrect data and controller has wrong process model? |
| **Unknown unknowns** (edge cases AI hasn't seen) | Operational monitoring + ODD enforcement + safe fallback | No method can find ALL failure scenarios → must have runtime safety net |

**Architecture strategy (defense-in-depth):**

```
Layer 1: PRIMARY CHANNEL (AI-based)
  • Perception → Planning → Control
  • High performance; complex; NOT independently certifiable
  
Layer 2: SAFETY MONITOR (rule-based; certifiable)
  • Independent sensors + independent compute
  • Simple rules: "If distance < safe_distance AND closing_speed > threshold → brake"
  • Certifiable to ASIL D (simple enough for formal verification)
  • Overrides primary channel if safety violation detected

Layer 3: SAFE STATE MECHANISM (fail-safe)
  • Hardware-level safe state (brake apply; steering lock)
  • Activated if both Layer 1 AND Layer 2 fail
  • Minimal functionality; maximum reliability

Safety argument:
  • Layer 1 (AI): provides PERFORMANCE (but not certifiable alone)
  • Layer 2 (monitor): provides SAFETY ASSURANCE (certifiable; catches AI errors)
  • Layer 3 (fail-safe): provides LAST RESORT (purely hardware; ultra-reliable)
```

---

## Chapter 12 — Cheat Sheet & Quick Reference

```
═══════════════════════════════════════════
SAFETY METHODS (SE) — QUICK REFERENCE
═══════════════════════════════════════════

THE THREE PILLARS (ARP4761A):
  FHA  → WHAT can go wrong? How bad?
  PSSA → Is design SAFE ENOUGH?
  SSA  → Does built system MEET targets?

═══════════════════════════════════════════
FHA OUTPUT:
  Function → Failure Condition → Severity → DAL/ASIL
  
  Aerospace (AMC 25.1309):
    Catastrophic → DAL A → ≤ 10⁻⁹/FH
    Hazardous   → DAL B → ≤ 10⁻⁷/FH
    Major       → DAL C → ≤ 10⁻⁵/FH
    Minor       → DAL D → no specific prob
    No Effect   → DAL E

  Automotive (ISO 26262 HARA):
    ASIL = f(Severity, Exposure, Controllability)
    ASIL D (highest) → ASIL A (lowest) → QM (no safety)

═══════════════════════════════════════════
PSSA METHODS:
  FTA  — Top-down; probability; AND/OR gates
  FMEA — Bottom-up; component failure modes; effects
  DD   — Reliability block diagram; redundancy paths
  Markov — State-based; sequence-dependent; latent faults
  CCA  — Validates INDEPENDENCE assumption

═══════════════════════════════════════════
CCA SUB-ANALYSES:
  ZSA — Zonal (physical proximity; fire/flood/heat)
  PRA — Particular Risks (bird strike; HIRF; burst rotor)
  CMA — Common Mode (same SW; same design; same mfg)

═══════════════════════════════════════════
FTA MATH:
  OR gate:  P(top) = P(A) + P(B)  [small P]
  AND gate: P(top) = P(A) × P(B)  [independent]
  
  Redundancy: P(both fail) = P(A) × P(B) → very small
  BUT: common cause → P(common event) >> P(A) × P(B)!

═══════════════════════════════════════════
STPA (Leveson; STAMP):
  1. Define Losses & Hazards
  2. Model Control Structure
  3. Identify Unsafe Control Actions (4 types):
     - Not provided (when needed)
     - Provided (when NOT needed)
     - Wrong timing/order
     - Wrong duration/magnitude
  4. Identify Loss Scenarios (WHY would UCA occur?)

═══════════════════════════════════════════
ISO 26262 vs ARP4761:
  Automotive: HARA → FSC → TSC → DFA → FMEDA
  Aerospace:  FHA → PSSA (FTA/FMEA/CCA) → SSA
  
  Both: identify hazards → assess risk → allocate 
    safety requirements → verify → validate

═══════════════════════════════════════════
KEY INSIGHT:
  FTA tells you "how safe" (probability).
  CCA tells you "if it's REALLY that safe" (independence).
  STPA tells you "what makes it UNSAFE" (control flaws).
  
  Use ALL THREE for comprehensive safety assessment.
```

---

*End of Document — 08_Safety_Methods_SE.md*
