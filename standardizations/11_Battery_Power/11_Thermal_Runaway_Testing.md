# Thermal Runaway Testing — UL 9540A & Propagation Prevention

**Topic:** Thermal Runaway Characterization, Propagation Testing, and Prevention Strategies for Battery Energy Storage Systems  
**Standards:** UL 9540A:2023, IEC 62619, GB 38031:2020, SAE J2464, UN GTR No. 20, NFPA 855, IFC 2021  
**SDO:** UL (Underwriters Laboratories), IEC, SAE, NFPA, GB (Chinese national standards)  
**Audience:** Battery safety engineers, fire protection engineers, ESS system integrators, test lab technicians, building code officials  
**Prerequisites:** Lithium-ion battery chemistry, thermal management basics, fire science fundamentals

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event |
|------|-------|
| 2011 | Chevrolet Volt NHTSA investigation (coolant leak → delayed TR after crash test) |
| 2012 | First UL 9540 standard published (ESS safety — no propagation test method) |
| 2013 | Boeing 787 Dreamliner grounding (Li-ion battery thermal runaway in flight) |
| 2016 | Samsung Galaxy Note 7 recall (TR from manufacturing defect + design flaw) |
| 2017 | UL 9540A **first edition** published — dedicated TR test method for ESS |
| 2018 | APS McMicken ESS explosion (Arizona, USA — 4 firefighters injured; triggered industry reckoning) |
| 2019 | 23 ESS fires in South Korea (LG Chem cells; inadequate ventilation/detection) |
| 2019 | UL 9540A 2nd edition (enhanced gas analysis requirements) |
| 2020 | GB 38031:2020 published (China — mandatory 5-minute warning for EV batteries) |
| 2021 | UL 9540A 3rd edition (expanded installation-level testing; 4-level structure) |
| 2021 | Victorian Big Battery fire (Australia — Tesla Megapack; TR propagation during commissioning) |
| 2022 | UL 9540A 4th edition (current — enhanced module-level propagation prevention requirements) |
| 2022 | NFPA 855 updated (references UL 9540A for ESS installations) |
| 2023 | GM Bolt recall completed ($1.8B cost — TR from manufacturing defect in LG cells) |
| 2023 | IFC 2021/2024 codes integrate UL 9540A for ESS building permits |
| 2024 | IEC 63330 (international propagation test standard) under development |
| 2025 | UN GTR No. 20 Phase 2 (global EV thermal propagation requirements) |

### 1.2 Key Incidents That Shaped Standards

| Incident | Root Cause | Regulation Impact |
|----------|-----------|-------------------|
| APS McMicken (2018) | Single cell TR → propagation → gas accumulation in sealed enclosure → explosive ignition | Drove UL 9540A adoption; ventilation requirements in NFPA 855 |
| South Korea ESS fires (2019) | Cell defects + inadequate BMS monitoring + poor ventilation; 23 fires in 2 years | Korean ESS safety standards rewritten; mandatory gas detection |
| Boeing 787 (2013) | Cell internal short → TR → inadequate containment design (titanium box failed) | DO-311A (aviation battery TR containment); FAA battery rules |
| Samsung Note 7 (2016) | Aggressive design (thin separator, tight fit) + manufacturing defect → internal short | IEC 62133 updates; enhanced cell-level QC standards |
| GM Bolt (2020-2021) | Manufacturing defect (torn anode tab + folded separator in same cell) → field TR | ASIL B BMS unable to detect manufacturing defects; enhanced incoming inspection |
| Victorian Big Battery (2021) | Coolant leak → electrical fault → TR; fire suppression insufficient | Improved commissioning procedures; cooling system monitoring |
| Felicity Ace cargo ship (2022) | EV battery fire in vehicle transport ship → total loss | IMO guidelines for EV transport; enhanced fire suppression on RORO ships |

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 UL 9540A Test Structure — Four Levels

```mermaid
graph TB
    subgraph "Level 1: Cell Level"
        L1[Single Cell Test<br/>─────────────────<br/>• Characterize TR behavior<br/>• Peak temperature<br/>• Gas generation (volume, composition)<br/>• Heat release rate<br/>• Mass loss<br/>• Time to TR<br/>• Ejecta behavior]
    end
    
    subgraph "Level 2: Module Level"
        L2[Module/Unit Test<br/>─────────────────<br/>• Initiate TR in one cell<br/>• Observe propagation to adjacent cells<br/>• Measure: time to propagation,<br/>  temperatures, gas release<br/>• Test WITH module-level<br/>  safety features active<br/>• GOAL: demonstrate propagation<br/>  PREVENTION or controlled propagation]
    end
    
    subgraph "Level 3: Unit Level"
        L3[Unit/Rack Test<br/>─────────────────<br/>• Initiate TR in one module<br/>• Observe propagation to adjacent modules<br/>• Test WITH system-level<br/>  protections active<br/>  (BMS, cooling, fire suppression)<br/>• Measure gas accumulation<br/>  in enclosure<br/>• GOAL: no propagation beyond<br/>  initiating module; OR controlled<br/>  propagation with mitigation]
    end
    
    subgraph "Level 4: Installation Level"
        L4[Installation Test<br/>─────────────────<br/>• Full-scale ESS installation<br/>• Initiate TR in one unit<br/>• Evaluate: fire spread to<br/>  adjacent units/structures<br/>• Evaluate: explosion risk<br/>  from gas accumulation<br/>• Evaluate: fire suppression<br/>  effectiveness<br/>• GOAL: inform building code<br/>  separation distances, ventilation]
    end
    
    L1 -->|"Data feeds"| L2
    L2 -->|"Data feeds"| L3
    L3 -->|"Data feeds"| L4
```

### 2.2 Standards Landscape for Thermal Runaway

| Standard | Scope | Application | Key Requirement |
|----------|-------|-------------|-----------------|
| **UL 9540A** | ESS TR test method (4 levels) | Stationary energy storage | Characterize TR; evaluate propagation at each level |
| **GB 38031:2020** | EV battery safety | Chinese market EV | TR must NOT propagate to other cells AND 5-min warning before external fire |
| **UN GTR No. 20** | Global EV battery safety | All EV (global) | Thermal propagation requirements (Phase 2: no propagation or warning) |
| **SAE J2464** | EV abuse testing | EV cells and packs (US) | Overcharge, nail penetration, crush, external short, thermal abuse |
| **IEC 62619 Annex B** | Industrial battery TR | Industrial BESS | TR propagation resistance (informative annex; becoming normative) |
| **NFPA 855** | ESS installation code | US building/fire code | References UL 9540A; sets separation distances, ventilation, suppression |
| **IFC 2021/2024** | International Fire Code | Building permits (US/intl) | ESS installation requirements; references UL 9540A |
| **IEC 63330** (developing) | International TR propagation test | Global (all applications) | Harmonized international propagation test (to replace national variations) |
| **EUCAR Hazard Levels** | Severity classification | European automotive (R&D) | Levels 0-7 (no effect → explosion); Level 4+ = fire |

### 2.3 EUCAR Hazard Level Classification

| Level | Description | Outcome | Acceptable? |
|-------|-------------|---------|-------------|
| 0 | No effect | No observable change | ✅ Pass |
| 1 | Passive protection activated | CID/PTC/vent activated, no leakage | ✅ Pass |
| 2 | Defect/damage | No leakage; permanent capacity loss | ✅ Pass |
| 3 | Leakage (Δmass <50%) | Electrolyte leakage; no fire, no explosion | ⚠️ Conditional |
| 4 | Venting (Δmass ≥50%) | Gas/electrolyte ejection; no fire | ⚠️ Conditional |
| 5 | Fire | Flames and/or hot sparks | ❌ (for consumer) |
| 6 | Rupture | Mechanical failure of cell casing; flying debris | ❌ |
| 7 | Explosion | Rapid disassembly with pressure wave; projectiles | ❌ |

---

## Chapter 3 — Technical Deep Dive

### 3.1 Thermal Runaway Mechanism

```mermaid
graph TB
    subgraph "Trigger Events"
        MECH[Mechanical<br/>• Nail penetration<br/>• Crush/deformation<br/>• Impact/drop]
        ELEC[Electrical<br/>• Overcharge (>4.5V)<br/>• External short circuit<br/>• Internal short (dendrite)]
        THERM[Thermal<br/>• External heating (>130°C)<br/>• Adjacent cell TR<br/>• Coolant failure]
    end
    
    subgraph "Thermal Runaway Cascade"
        SEI[Stage 1: SEI Decomposition<br/>~90-120°C (exothermic)<br/>• Metastable SEI layer decomposes<br/>• Exposes fresh anode to electrolyte<br/>• Generates gas (CO₂, C₂H₄)]
        
        SEP[Stage 2: Separator Melting<br/>~130-160°C (PE: 130°C, PP: 165°C)<br/>• Internal short circuit forms<br/>• Massive current flow → heating<br/>• Point of no return (for most cells)]
        
        CATH[Stage 3: Cathode Decomposition<br/>~170-250°C (chemistry dependent)<br/>• NMC: ~210°C<br/>• NCA: ~170°C<br/>• LFP: ~270°C (most stable)<br/>• Releases O₂ → feeds combustion]
        
        ELYT[Stage 4: Electrolyte Combustion<br/>~250-300°C<br/>• Organic solvents ignite<br/>• Carbonate decomposition<br/>• Produces: CO, CO₂, HF, CH₄, C₂H₄<br/>• Vigorous burning]
        
        TR[Stage 5: Full Thermal Runaway<br/>>300°C (can reach >700°C)<br/>• Self-sustaining reaction<br/>• Cell venting (gas ejection)<br/>• Possible fire/explosion<br/>• Complete energy release]
    end
    
    MECH --> SEI
    ELEC --> SEI
    THERM --> SEI
    SEI -->|"+30-50°C rise"| SEP
    SEP -->|"Rapid heating"| CATH
    CATH -->|"O₂ release"| ELYT
    ELYT -->|"Combustion"| TR
```

### 3.2 Thermal Runaway Gas Analysis

| Gas Species | Source | Concentration (typical) | Hazard |
|-------------|--------|------------------------|--------|
| CO (carbon monoxide) | Electrolyte decomposition, carbonate reduction | 10-30% of vent gas | **Toxic** (fatal at >1200 ppm), flammable |
| CO₂ (carbon dioxide) | Electrolyte oxidation, SEI decomposition | 15-40% | Asphyxiant at high concentration |
| H₂ (hydrogen) | Electrolyte reduction at anode | 10-30% | **Highly flammable** (LEL: 4%) |
| CH₄ (methane) | Electrolyte decomposition | 2-10% | **Flammable** (LEL: 5%) |
| C₂H₄ (ethylene) | EC/DMC decomposition | 5-15% | **Flammable** (LEL: 2.7%) |
| C₂H₆ (ethane) | Electrolyte decomposition | 1-5% | Flammable |
| HF (hydrogen fluoride) | LiPF₆ salt decomposition | 0.1-2% | **Highly toxic** (IDLH: 30 ppm); corrosive |
| POF₃ (phosphoryl fluoride) | LiPF₆ decomposition | Trace | Toxic |
| EMC/DMC/EC vapors | Evaporated electrolyte solvents | Variable | Flammable |

**Gas generation rates (per kWh of cell energy):**

| Cell Chemistry | Gas Volume (per kWh) | Peak Flow Rate | Notes |
|---------------|---------------------|----------------|-------|
| NMC 811 (high energy) | 1.5-3.0 L/Wh (= 1500-3000 L/kWh) | Burst over 5-30 seconds | Most gas of common chemistries |
| NMC 622 | 1.0-2.0 L/Wh | Similar burst profile | Moderate |
| NCA | 1.5-2.5 L/Wh | Fast release | Similar to NMC 811 |
| LFP | 0.5-1.5 L/Wh | Slower, lower temperature | Less gas, less energetic TR |
| NMC 111 | 0.8-1.5 L/Wh | Moderate | Older chemistry, less common |

### 3.3 Thermal Propagation Mechanisms

| Mechanism | Description | Mitigation |
|-----------|-------------|-----------|
| Conduction (cell-to-cell) | Direct heat transfer through cell casing contact, tabs, busbars | Thermal barriers (mica, aerogel, intumescent) between cells |
| Convection (hot gas) | Vent gas at >600°C impinges on adjacent cells | Vent gas directional management; gas barriers |
| Radiation | Radiant heat from burning cell (>700°C → significant IR radiation) | Radiation shields; reflective barriers |
| Ejecta/sparks | Hot molten particles ejected from venting cell ignite adjacent cell vent | Ejecta shields; directional venting away from neighbors |
| Electrical (busbar) | Copper busbar conducts heat from failed cell to adjacent (thermal bridge) | Fusible links; thermal breaks in busbar design |
| Flame impingement | Direct flame contact from burning cell | Fire-resistant barriers (rated 15+ minutes) |

### 3.4 UL 9540A Cell-Level Test Parameters (Level 1)

| Measurement | Method | Purpose |
|-------------|--------|---------|
| Peak cell surface temperature | Thermocouples (Type K) on cell surface | Characterize thermal output |
| Time-temperature profile | Continuous recording at 1+ Hz sampling | Determine heat release kinetics |
| Gas generation volume | Gas collection chamber (calibrated volume) | Size ventilation systems |
| Gas composition | FTIR spectroscopy and/or GC-MS | Identify toxic/flammable gases; size detection/scrubbing |
| Heat release rate (HRR) | Cone calorimeter (per ASTM E1354) or ARC | Characterize fire load |
| Mass loss | Load cell (continuous measurement) | Track material ejection |
| Ejecta behavior | High-speed camera + mass collection | Assess projectile risk |
| Lower Flammability Limit (LFL) of vent gas | Calculate from composition using Le Chatelier's rule | Determine explosion risk |
| Initiation method | Nail penetration (preferred) OR overcharge OR external heater | Standardized trigger |
| Cell SOC for test | **100% SOC** (worst case) | Maximum energy for TR |

---

## Chapter 4 — Implementation Guide

### 4.1 Thermal Runaway Prevention Strategy (System-Level)

```mermaid
graph TB
    subgraph "Layer 1: Cell Level"
        CELL_QC[Cell Quality Control<br/>• 100% incoming inspection<br/>• X-ray for internal defects<br/>• Impedance screening<br/>• Formation data analysis<br/>• Reject outliers]
        CELL_DESIGN[Cell Design Features<br/>• Ceramic-coated separator<br/>• Shutdown separator (PE/PP/PE)<br/>• CID (Current Interrupt Device)<br/>• PTC element<br/>• Safety vent (controlled release)]
    end
    
    subgraph "Layer 2: Module Level"
        MOD_BARRIER[Thermal Barriers<br/>• Mica sheets (1-3 mm)<br/>• Aerogel blankets (2-5 mm)<br/>• Intumescent materials<br/>• Phase-change materials<br/>• Between every cell OR cell group]
        MOD_COOL[Active Cooling<br/>• Liquid cooling plates<br/>• Immersion cooling (dielectric)<br/>• Can remove heat from TR cell<br/>  if caught early enough<br/>• Maintains neighbors below TR onset]
        MOD_VENT[Venting Management<br/>• Directional vent paths<br/>• Vent gas away from adjacent cells<br/>• Flame arrestors at vent exits]
    end
    
    subgraph "Layer 3: System Level"
        BMS[BMS Detection<br/>• Cell voltage anomaly (ΔV)<br/>• Temperature rate-of-rise (dT/dt)<br/>• Impedance change detection<br/>• Gas sensor (CO, H₂, VOC)<br/>• EARLY warning (pre-TR)]
        SUPPRESS[Fire Suppression<br/>• Water mist (direct cooling)<br/>• Clean agent (halon alternative)<br/>• Aerosol generators<br/>• Combination systems]
        DISCONNECT[Electrical Isolation<br/>• Contactors open (BMS command)<br/>• Fuses blow (overcurrent)<br/>• Pyrotechnic disconnect (crash)<br/>• Prevents continued energy feed]
    end
    
    subgraph "Layer 4: Installation Level"
        DISTANCE[Separation Distance<br/>• Unit-to-unit spacing<br/>• Unit-to-building distance<br/>• Based on UL 9540A L4 testing]
        VENT_SYS[Ventilation System<br/>• Mechanical ventilation<br/>• Dilute vent gas below LFL<br/>• Prevent accumulation<br/>• Maintain tenable conditions]
        FIRE_RATED[Fire-Rated Construction<br/>• Fire barriers between units<br/>• Fire-rated enclosures<br/>• Structural fire protection]
        DET_ALARM[Detection & Alarm<br/>• Smoke detection (aspirating)<br/>• Gas detection (CO, H₂, LEL)<br/>• Heat detection<br/>• Connected to fire alarm panel]
    end
    
    CELL_QC --> MOD_BARRIER
    CELL_DESIGN --> MOD_BARRIER
    MOD_BARRIER --> BMS
    MOD_COOL --> BMS
    MOD_VENT --> SUPPRESS
    BMS --> DISCONNECT
    SUPPRESS --> DISTANCE
    DISCONNECT --> VENT_SYS
```

### 4.2 Thermal Barrier Materials Comparison

| Material | Thermal Conductivity (W/m·K) | Thickness Needed | Temperature Rating | Cost | Application |
|----------|------------------------------|-----------------|-------------------|------|-------------|
| Mica sheet | 0.3-0.5 | 1-3 mm | >1000°C | Low | Cell-to-cell barrier (proven, widely used) |
| Aerogel blanket | 0.015-0.025 | 2-5 mm | >650°C | High | Premium EV packs (excellent insulation, space-efficient) |
| Intumescent pad | 0.1-0.3 (expands when heated) | 1-2 mm (expands 5-10×) | >800°C after expansion | Medium | Swells to fill gaps when heated; seals vent paths |
| Ceramic fiber | 0.05-0.1 | 3-10 mm | >1200°C | Medium | Industrial ESS; thick but very effective |
| Phase-change material (PCM) | Varies (absorbs latent heat) | 2-5 mm | Up to PCM melting point (~40-80°C) | Medium-High | Absorbs heat during early TR; delays propagation |
| Silicone foam | 0.1-0.2 | 2-5 mm | ~300°C | Low-Medium | Consumer electronics; limited temperature range |
| Stainless steel + insulation | Composite (barrier) | 0.5-2 mm steel + insulation | Steel survives; insulation per type | Medium | Structural barrier + thermal insulation combined |

### 4.3 Fire Suppression Approaches for BESS

| Method | Mechanism | Effectiveness | Limitations |
|--------|-----------|--------------|-------------|
| Water mist | Direct cooling of cells (absorbs heat via evaporation) | **Most effective** for cooling; prevents propagation | Water damage to electronics; large water supply needed; re-ignition possible |
| Clean agent (FK-5-1-12 / Novec 1230) | Oxygen displacement + heat absorption | Extinguishes open flame quickly | Does NOT cool cells; re-ignition within minutes as cells still hot; cannot stop TR chemistry |
| Inert gas (N₂, CO₂) | Oxygen displacement | Prevents flame; slows oxidation | Does not cool cells; TR continues (TR is self-oxidizing from cathode O₂); useful for gas explosion prevention |
| Aerosol generators | Potassium-based particles interrupt flame chemistry | Fast deployment; no piping needed | Limited cooling; re-ignition risk; residue; doesn't address TR root cause |
| Foam (AFFF / fluorine-free) | Smothering + mild cooling | Moderate | Limited penetration into battery enclosure; environmental concerns (PFAS) |
| Immersion (post-event) | Submerge entire pack in water | Definitive — stops all reactions | Only for post-fire; requires containment vessel; not practical during operation |
| Combination: clean agent + water mist | Agent suppresses flame immediately; water mist provides sustained cooling | **Best practice** for large BESS | Complex system; higher cost; requires both agent supplies |

### 4.4 Gas Detection for Early TR Warning

| Gas | Detector Technology | Detection Threshold | Response Time | Use Case |
|-----|--------------------|--------------------|---------------|----------|
| CO (carbon monoxide) | Electrochemical cell | 5-50 ppm | 15-30 seconds | **Best early indicator** — released before visible smoke |
| H₂ (hydrogen) | Catalytic bead / electrochemical | 100-500 ppm (or % LEL) | 10-30 seconds | Early indicator; present in vent gas before ignition |
| VOC (volatile organic) | PID (photoionization detector) | 1-10 ppm | 5-15 seconds | Electrolyte vapor — very early detection |
| Hydrocarbon (LEL) | Catalytic / infrared | 10-25% LEL | 10-30 seconds | Explosion prevention (vent gas below ignition) |
| Smoke (particles) | Aspirating smoke detection (ASD) | VERY early (0.005% obscuration) | 30-90 seconds (with piping) | Detects particulates from overheating |
| Temperature anomaly | IR thermal camera / thermocouples | ΔT > 5°C from baseline | Seconds | Detect hot spot before vent |

---

## Chapter 5 — Certification & Testing

### 5.1 UL 9540A Testing Costs and Timeline

| Test Level | Typical Cost | Duration | Samples Required |
|-----------|-------------|----------|-----------------|
| Level 1 (cell) | $30,000-$60,000 | 2-4 weeks | 5-10 cells (100% SOC) |
| Level 2 (module) | $50,000-$100,000 | 4-6 weeks | 3-5 modules |
| Level 3 (unit) | $100,000-$250,000 | 6-10 weeks | 1-2 complete units |
| Level 4 (installation) | $200,000-$500,000+ | 8-16 weeks | Full-scale installation (often requires outdoor test facility) |
| Complete UL 9540A (all levels) | $400,000-$900,000 | 4-8 months | Multiple samples at each level |

### 5.2 Test Laboratories

| Laboratory | Location | Capabilities |
|-----------|----------|-------------|
| UL (Underwriters Laboratories) | Northbrook IL, USA; multiple global | Full UL 9540A all levels; outdoor test facility |
| TÜV Rheinland | Germany; global | IEC 62619, UN 38.3, TR characterization |
| TÜV SÜD | Germany; Singapore | Battery abuse testing, propagation |
| DNV | Norway; global | Maritime + stationary ESS testing |
| Southwest Research Institute (SwRI) | San Antonio TX, USA | Large-scale fire testing; installation-level |
| FM Global | Johnston RI, USA | Large-scale fire testing; insurance approval |
| Sandia National Laboratories | Albuquerque NM, USA | DOE ESS safety research; full-scale testing |
| RISE (Research Institutes of Sweden) | Borås, Sweden | Fire testing; propagation; calorimetry |
| Bureau Veritas | France; global | Marine and stationary ESS certification |
| Intertek | Multiple locations | UL 9540, UL 9540A testing |
| DEKRA | Germany; global | Automotive + ESS abuse testing |

### 5.3 GB 38031:2020 — China 5-Minute Warning Requirement

| Requirement | Specification | Rationale |
|-------------|---------------|-----------|
| Thermal propagation test | Trigger TR in single cell (nail penetration or heater) within full battery pack | Evaluate real-world pack behavior |
| Propagation prevention (preferred) | TR must NOT propagate to adjacent cells | Best outcome — no fire |
| 5-minute warning (minimum) | If TR propagates: BMS must warn occupants ≥5 minutes before fire/explosion reaches passenger compartment | Allow occupant evacuation |
| Warning method | Audible + visual alarm to driver (instrument cluster + buzzer) | Clear warning to exit vehicle |
| Test SOC | 100% SOC (worst case) | Maximum energy available for TR |
| Pass criteria | EITHER no propagation OR 5-minute warning successfully demonstrated | Regulatory pass/fail |
| Application | All electric vehicles sold in China | Mandatory for CCC certification |

---

## Chapter 6 — Regional Requirements

### 6.1 Thermal Runaway Testing Requirements by Region

| Region | Standard | Requirement | Enforcement |
|--------|----------|-------------|-------------|
| China | GB 38031:2020 | No TR propagation OR 5-min warning (EV) | **Mandatory** (CCC certification) |
| EU | UN R100.03 + UN GTR 20 | Thermal propagation requirements (Phase 2 pending) | Mandatory (type approval 2025+) |
| US (EV) | FMVSS 305 (+ SAE J2464 voluntary) | No specific TR propagation requirement (crash-focused) | Voluntary (TR testing); mandatory (crash) |
| US (ESS) | **UL 9540A** + NFPA 855 + IFC | UL 9540A required for building permits; NFPA 855 for installations | **Mandatory** (building code) |
| Korea | KMVSS + KBIA guidelines | TR propagation limits (aligned with UN GTR 20) | Mandatory (type approval) |
| Japan | JIS D 0601 + MLIT regulations | TR test included; no propagation preferred | Mandatory |
| India | AIS 156 (based on UN R100) | Aligned with UN R100; phasing in TR requirements | Developing |
| Global (UN) | UN GTR No. 20 Phase 2 | Thermal propagation: no propagation or adequate warning time | Being adopted globally (2025+) |

### 6.2 NFPA 855 Key Requirements for ESS Installation

| Requirement | Specification | Purpose |
|-------------|---------------|---------|
| UL 9540A testing required | All BESS must have UL 9540A report | Characterize hazard for installation design |
| Maximum energy per unit | 50 kWh per battery system (indoor, without UL 9540A installation-level data) | Limit fire load without full testing |
| Spacing (unit-to-unit) | 3 feet minimum (more based on UL 9540A L4 data) | Prevent propagation between units |
| Spacing (unit-to-wall) | 3 feet minimum | Fire access; prevent building ignition |
| Ventilation | Mechanical ventilation to maintain vent gas below 25% LFL | Explosion prevention |
| Gas detection | Combustible gas detection + smoke detection within ESS room | Early warning |
| Fire suppression | Required per UL 9540A data (type based on testing) | Limit fire severity |
| Exhaust | Exhaust to safe outdoor location (not into occupied space) | Protect occupants from toxic gas |
| Signage | Hazard signs, emergency procedures posted | First responder information |
| Fire department access | Clear access; emergency disconnect accessible from outside | Safe emergency response |
| Seismic (where applicable) | Anchorage per local seismic code | Prevent toppling/damage in earthquake |

---

## Chapter 7 — Standard Comparison Matrix

| Dimension | UL 9540A | GB 38031 | UN GTR 20 | SAE J2464 | IEC 62619 Annex B |
|-----------|----------|----------|-----------|-----------|-------------------|
| Application | Stationary ESS | Chinese EV | Global EV | EV (US research/reference) | Industrial batteries |
| Test levels | 4 (cell → module → unit → installation) | Pack-level (one cell triggered in full pack) | Pack-level | Cell and module level | Module/pack level |
| TR initiation | Nail penetration (preferred), heater, overcharge | Nail penetration or heater | Heater (preferred) or nail | Multiple methods | Heater |
| Propagation requirement | Characterize (no mandatory pass/fail — informs installation) | **No propagation OR 5-min warning** | No propagation or warning (Phase 2) | Characterize (research) | Propagation resistance (informative) |
| Gas analysis | **Required** (composition + volume + flow rate) | Required (basic) | Limited | Optional | Not required |
| Installation guidance | **YES** (Level 4 data → separation distances) | N/A (vehicle-level only) | N/A | N/A | N/A |
| Fire suppression evaluation | YES (Level 4 includes suppression test) | N/A | N/A | N/A | N/A |
| SOC for test | 100% SOC | 100% SOC | 100% SOC | Multiple SOC levels | 100% SOC |
| Mandatory/voluntary | Mandatory for US building permits (via NFPA 855/IFC) | **Mandatory** (China CCC) | Mandatory (when adopted by countries) | Voluntary (reference standard) | Part of IEC 62619 certification |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 UL 9540A Four-Level Test Progression

```mermaid
graph LR
    subgraph "Level 1: Cell"
        L1_IN[Input:<br/>Single cell at 100% SOC]
        L1_TEST[Test:<br/>• Nail penetration<br/>• OR overcharge<br/>• OR heater]
        L1_OUT[Output:<br/>• Peak temperature<br/>• Gas volume & composition<br/>• Heat release rate<br/>• Mass loss<br/>• Time-to-TR<br/>• Ejecta characterization]
    end
    
    subgraph "Level 2: Module"
        L2_IN[Input:<br/>Full module (all cells)<br/>One cell initiated]
        L2_TEST[Test:<br/>• Trigger TR in one cell<br/>• Monitor all adjacent cells<br/>• Module protections active]
        L2_OUT[Output:<br/>• Propagation yes/no<br/>• Time to propagation<br/>• Propagation pattern<br/>• Gas from module<br/>• Temperature map<br/>• Effectiveness of barriers]
    end
    
    subgraph "Level 3: Unit"
        L3_IN[Input:<br/>Full unit/rack<br/>One module initiated]
        L3_TEST[Test:<br/>• Trigger TR in one module<br/>• System protections active<br/>  (BMS, cooling, suppression)<br/>• Monitor enclosure conditions]
        L3_OUT[Output:<br/>• Module-to-module propagation?<br/>• Enclosure gas concentration<br/>• Temperature vs time<br/>• Suppression effectiveness<br/>• Deflagration risk assessment]
    end
    
    subgraph "Level 4: Installation"
        L4_IN[Input:<br/>Full installation<br/>(multiple units)<br/>One unit initiated]
        L4_TEST[Test:<br/>• Full-scale fire test<br/>• Evaluate unit-to-unit spread<br/>• Building exposure<br/>• Suppression system test<br/>• Ventilation effectiveness]
        L4_OUT[Output:<br/>• Required separation distances<br/>• Required ventilation rates<br/>• Fire suppression sizing<br/>• Exposure hazard distances<br/>• Installation design basis]
    end
    
    L1_IN --> L1_TEST --> L1_OUT
    L2_IN --> L2_TEST --> L2_OUT
    L3_IN --> L3_TEST --> L3_OUT
    L4_IN --> L4_TEST --> L4_OUT
    L1_OUT -->|"Characterization<br/>data feeds"| L2_IN
    L2_OUT -->|"Module data<br/>feeds"| L3_IN
    L3_OUT -->|"Unit data<br/>feeds"| L4_IN
```

### 8.2 Thermal Runaway Detection and Response Timeline

```mermaid
graph TB
    subgraph "Pre-TR Detection (Minutes to Hours Before)"
        PRE1[Impedance anomaly<br/>• Cell resistance increases<br/>• Detectable by AC impedance BMS<br/>• HOURS before TR]
        PRE2[Self-discharge anomaly<br/>• Micro-short developing<br/>• Voltage drops faster than peers<br/>• HOURS to DAYS before TR]
        PRE3[Gas detection<br/>• CO/VOC at very low ppm<br/>• Electrolyte micro-leak<br/>• MINUTES before TR onset]
    end
    
    subgraph "TR Onset (Seconds)"
        TR1[Cell temperature spike<br/>• dT/dt > 1°C/s<br/>• Detected by NTC sensor<br/>• TR confirmed]
        TR2[Voltage collapse<br/>• Cell voltage drops rapidly<br/>• Internal short confirmed<br/>• BMS triggers alarm]
        TR3[Gas venting begins<br/>• Gas sensor saturation<br/>• Visible smoke possible<br/>• ~5-30 seconds into TR]
    end
    
    subgraph "Response Actions (Seconds to Minutes)"
        R1[BMS Response<br/>• Open contactors (isolate HV)<br/>• Occupant warning activated<br/>• 5-min countdown starts<br/>• Log event data]
        R2[Thermal Management<br/>• Cooling to maximum<br/>• Absorb heat from neighbors<br/>• Delay propagation]
        R3[Fire Suppression<br/>• Clean agent release (immediate)<br/>• Water mist activation (sustained)<br/>• Prevent flame spread]
        R4[Ventilation<br/>• Emergency ventilation mode<br/>• Dilute gas below LFL<br/>• Exhaust to safe area]
    end
    
    subgraph "Propagation Window"
        PROP[Adjacent Cell Heating<br/>• 30 seconds to 5+ minutes<br/>  (barrier dependent)<br/>• If neighbor reaches ~130-150°C<br/>  → cascading TR<br/>• BARRIER BUYS TIME for response]
    end
    
    PRE1 --> TR1
    PRE2 --> TR1
    PRE3 --> TR1
    TR1 --> R1
    TR2 --> R1
    TR3 --> R3
    R1 --> R2
    R2 --> PROP
    R3 --> PROP
    R4 --> PROP
```

---

## Chapter 9 — Case Studies

### 9.1 APS McMicken ESS Explosion (2019)

| Aspect | Detail |
|--------|--------|
| Location | McMicken, Arizona, USA (APS utility substation) |
| System | 2 MW / 2 MWh Li-ion NMC BESS (Samsung SDI cells, Fluence Energy system) |
| Event | April 19, 2019 — explosion injured 4 firefighters, 1 critically |
| Root cause sequence | (1) Single cell internal short circuit → thermal runaway. (2) TR propagated to adjacent cells in module (no thermal barriers). (3) Module TR generated large volume of flammable gas (CO, H₂, CH₄, C₂H₄). (4) Gas accumulated in sealed container (inadequate ventilation). (5) Gas concentration exceeded LFL (lower flammability limit). (6) Firefighters opened container door → fresh air introduced → deflagration/explosion. |
| Key failures | • No thermal barriers between cells (propagation was rapid). • No ventilation system (gas accumulated to explosive concentration). • No gas detection inside container (no warning of explosive atmosphere). • No explosion-proof electrical equipment inside (potential ignition source). • First responders not trained on BESS fire — opened door creating ignition conditions. |
| Investigation | DNV GL (now DNV) investigation report published July 2020 |
| Industry impact | (1) UL 9540A became de facto mandatory for US ESS installations. (2) NFPA 855 written and adopted (2020). (3) Ventilation requirements added to all codes. (4) Gas detection mandatory. (5) First responder training programs developed. (6) Industry moved to systems with thermal barriers and propagation prevention. |
| Regulatory change | APS McMicken directly drove: UL 9540A mandatory in IFC/NFPA; ventilation calculations; deflagration analysis requirements |
| Cost | System loss + investigation + legal + industry-wide design changes = hundreds of millions industry-wide |
| Lesson | **Gas accumulation + ignition source = explosion. Ventilation is not optional for enclosed BESS.** |

### 9.2 GB 38031 Compliance — EV Pack Design for 5-Minute Warning

| Aspect | Detail |
|--------|--------|
| Project | Design 75 kWh NMC 811 EV battery pack to pass GB 38031 thermal propagation test |
| Cell format | Prismatic 156 Ah NMC 811 (CATL-type form factor) |
| Pack configuration | 192 cells (96S2P); 16 modules × 12 cells each |
| Challenge | NMC 811 has very energetic TR (~250°C onset, large gas generation); propagation prevention is difficult |
| Design approach | Combination strategy: thermal barriers + directed venting + active cooling + BMS detection |
| Thermal barriers | 2 mm mica sheet between every cell; aerogel wrap around each module |
| Vent design | Cell vent direction: upward (away from adjacent cells); vent channel directs gas to pack exterior |
| Active cooling | Liquid cooling plates on both faces of each cell; maximum cooling activated upon TR detection |
| BMS detection | Multi-signal: voltage drop (>100 mV sudden) + temperature rise (>1°C/s) + gas sensor (CO >50 ppm) |
| Warning system | CAN message to vehicle cluster (visual + audio warning); hardwired buzzer backup |
| Test result | TR initiated in center cell via nail penetration at 100% SOC. TR propagated to 2 adjacent cells (total 3 cells failed). Propagation STOPPED at 3rd cell (mica barrier + cooling held). BMS detected TR within 3 seconds of initiation. Warning activated at T+5 seconds. No external fire observed until T+12 minutes. **PASS: 5-minute warning with large margin (12 minutes to external effect)** |
| Key metrics | Detection time: 3 seconds. Warning activation: 5 seconds. Propagation stopped: 3 cells. Time to external hazard: 12 minutes (>>5 min required). |
| Design iterations | 3 iterations required: (1) First design: no barriers → full pack propagation in 45 seconds (FAIL). (2) Second design: 1 mm mica barriers → propagation stopped at 8 cells, warning at 4 min (marginal FAIL). (3) Third design: 2 mm mica + aerogel + directed venting → propagation stopped at 3 cells, 12 min margin (PASS). |
| Cost impact | Thermal barriers + gas sensor added ~$800 per pack to BOM (vs. no-barrier design) |
| Lesson | **NMC 811 requires multi-layer protection strategy. No single measure is sufficient. Combination of barriers + venting + detection + cooling provides robust margin.** |

---

## Chapter 10 — Future Evolution & Industry Trends

| Trend | Timeline | Description |
|-------|----------|-------------|
| Cell-level propagation prevention (zero propagation) | 2024-2027 | Industry goal: ZERO propagation from any single cell TR (not just 5-min warning) |
| IEC 63330 (international standard) | 2025-2027 | Harmonized global TR propagation test standard (replacing fragmented national approaches) |
| UN GTR 20 Phase 2 enforcement | 2025-2026 | Mandatory thermal propagation requirements for EVs globally |
| Solid-state battery TR behavior | 2027+ | Different failure mode (no flammable liquid electrolyte); may simplify some safety requirements |
| LFP dominance for safety | Now | LFP's higher TR onset temperature (~270°C vs ~210°C for NMC) drives adoption where safety margin is critical |
| AI-based TR prediction | 2025+ | ML models detect TR precursors (impedance anomaly, micro-short signatures) days before event |
| Advanced fire suppression | Now-2025 | Encapsulated water mist systems; aerosol + water hybrid; enhanced clean agents |
| Direct immersion cooling (safety benefit) | 2024+ | Dielectric fluid immersion eliminates air for combustion; significantly slows propagation |
| Cell-to-pack (CTP) safety challenges | Now | Removing module enclosure increases propagation risk; requires enhanced cell-level barriers |
| Sodium-ion battery TR characteristics | 2025+ | Different TR behavior (lower energy density, lower temperature); may require separate test standards |
| Digital twin for TR simulation | Now | CFD + electrochemical simulation of TR propagation for design optimization before physical testing |
| ESS fire insurance requirements | Growing | Insurers requiring UL 9540A Level 4 data + comprehensive fire protection for coverage |
| Recyclability-aware safety design | 2025+ | Thermal barriers must be removable for recycling (EU Battery Regulation conflict resolution) |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** Explain the five stages of lithium-ion battery thermal runaway and the temperature at which each stage occurs.  
**A:**

**Stage 1: SEI Layer Decomposition (~90-120°C)**
The Solid Electrolyte Interphase (SEI) is a thin protective layer on the anode (graphite) surface formed during the first charge. It's metastable — when heated above ~90°C, it begins to decompose exothermically. This exposes fresh lithiated graphite to the electrolyte, causing further exothermic reactions. This stage generates heat (self-heating begins) and gases (CO₂, ethylene). It's still potentially recoverable if external heating is removed — but the cell is now self-heating at a low rate.

**Stage 2: Separator Collapse (~130-160°C)**
The polymer separator (typically PE at 130°C, PP at 165°C, or trilayer PE/PP/PE) melts and loses its mechanical integrity. When the separator collapses, the anode and cathode make direct contact — creating a massive internal short circuit. This stage marks the **point of no return** for most cell designs. The internal short drives enormous current, generating intense ohmic heating. Shutdown separators (ceramic-coated or trilayer) attempt to delay or prevent this by maintaining structural integrity at higher temperatures.

**Stage 3: Cathode Decomposition (~170-270°C, chemistry-dependent)**
- NCA: ~170-200°C (least stable — releases O₂ earliest)
- NMC 811: ~200-210°C 
- NMC 622: ~220-240°C
- NMC 111: ~250-270°C
- LFP: ~270-310°C (most thermally stable — olivine structure retains oxygen)

The cathode crystal structure decomposes and releases oxygen gas. This oxygen is critical — it enables combustion of the electrolyte even in a sealed cell (no external air needed). This is why lithium battery fires cannot be extinguished simply by removing oxygen from the environment.

**Stage 4: Electrolyte Combustion (~250-300°C)**
The organic electrolyte solvents (ethylene carbonate EC, dimethyl carbonate DMC, diethyl carbonate DEC) ignite. They combust with the oxygen released from the cathode. This stage produces large volumes of toxic and flammable gases: CO, CO₂, HF (from LiPF₆ salt), H₂, CH₄, C₂H₄. The cell typically vents at this stage — the vent mechanism (safety valve or casing failure) releases high-pressure hot gas.

**Stage 5: Full Thermal Runaway (>300°C, can reach 700-1000°C)**
Self-sustaining exothermic reaction. All remaining energy in the cell is released rapidly (seconds to tens of seconds depending on cell format and size). Cell surface temperature can exceed 700°C. Jet-like flame may emerge from vent. Molten material may be ejected. The reaction cannot be stopped — it must burn itself out. Total energy release is proportional to cell capacity × average voltage (e.g., a 50 Ah cell at 3.7V = 185 Wh = 666 kJ of electrical energy; thermal energy release can be 2-4× this due to chemical reactions).

### Tier 2: Mid-Level

**Q2:** You are designing a battery module for UL 9540A Level 2 testing. What design features would you incorporate to prevent cell-to-cell thermal propagation, and how would you validate them before the formal test?  
**A:**

**Design features for propagation prevention:**

**1. Inter-cell thermal barriers:**
- Material: 2 mm mica sheet (>1000°C rated) between every cell
- Alternative for premium: 3 mm aerogel blanket (λ = 0.02 W/m·K)
- Design: barrier must extend beyond cell dimensions (no thermal short-circuit paths around edges)
- Compression consideration: barriers must maintain integrity under pack compression (cells swell during cycling)

**2. Directed venting architecture:**
- Cell vent cap oriented AWAY from adjacent cells (upward or toward dedicated vent channel)
- Vent channel: stainless steel (>500°C rated) directing hot gas to module exterior
- Flame arrestor at channel exit (prevent flashback)
- Vent path must not impinge hot gas on adjacent cells or plastic components

**3. Thermal bridges minimization:**
- Busbars (copper): add thermal break or fusible link at each cell connection
- If cell tab welds to busbar, consider narrowed busbar section between cells (increases thermal resistance)
- Module housing: use thermally insulating material near cells (avoid aluminum housing directly contacting multiple cells)

**4. Active cooling integration:**
- Liquid cooling plates in contact with cell faces (bottom cooling common)
- Design cooling to maximize heat removal from cells adjacent to failed cell
- Emergency cooling mode: if TR detected, pump maximum coolant flow (absorb propagation heat)
- Even if cooling doesn't prevent TR in initiating cell, it can keep neighbors below 130°C (below separator melt)

**5. Module-level BMS detection:**
- Temperature sensor between every 2-4 cells (high density for early detection)
- Voltage monitoring: <100 ms polling interval for sudden voltage drop detection
- Module-level gas sensor (CO): detect vent gas before flame

**Pre-test validation (before formal UL 9540A):**

| Validation Method | Purpose | Cost |
|------------------|---------|------|
| Finite Element Thermal Simulation (CFD) | Model TR heat propagation through barriers; optimize barrier thickness | Engineering time (internal) |
| Single-cell TR characterization (internal test) | Measure actual heat output of your specific cell; calibrate simulation | $5,000-$10,000 (internal lab) |
| Inter-cell heat transfer test | Place heater against barrier + adjacent cell; measure temperature rise vs. time | $5,000-$15,000 |
| Module-scale simulation | Full thermal model with cooling active; predict which cells are at risk | Engineering time |
| Pre-compliance test (informal L2) | Nail-pen one cell in actual module (without formal UL observation); observe propagation | $20,000-$40,000 (internal or friendly lab) |
| Design iteration | Based on pre-compliance results, adjust barrier thickness, venting, cooling | Engineering time |
| Formal UL 9540A Level 2 | Official test with UL observation; formal report issued | $50,000-$100,000 |

### Tier 3: Senior

**Q3:** Design the complete fire safety strategy for a 100 MWh grid-scale BESS installation, incorporating UL 9540A data, NFPA 855 compliance, and learnings from the APS McMicken incident.  
**A:** [This would detail: (1) UL 9540A Level 4 test data interpretation for separation distances. (2) Mechanical ventilation design (calculate airflow to maintain <25% LFL based on gas generation data from UL 9540A Level 1). (3) Gas detection system design (aspirating smoke + CO + H₂ + LEL sensors at multiple heights). (4) Fire suppression: combination system (clean agent for immediate knock-down + water mist for sustained cooling + drencher for exposure protection). (5) Deflagration prevention (ventilation maintains gas below LEL; if ventilation fails, inert gas flooding as backup). (6) Container/enclosure design (pressure relief panels to prevent overpressure if deflagration occurs). (7) First responder plan (SOP for BESS fire: do NOT open doors without gas monitoring; remote monitoring capability; thermal imaging). (8) Separation distance calculations (unit-to-unit: based on UL 9540A L4 radiant heat flux; unit-to-property-line: based on flame height and radiant heat). (9) SCADA integration (BMS → fire alarm → SCADA → remote monitoring center → fire department). (10) Insurance requirements (FM Global or equivalent approval). Full answer would be 2500+ words.]

---

## Chapter 12 — Cheat Sheet & Quick Reference

### Thermal Runaway Stages

```
STAGE 1:  SEI Decomposition         ~90-120°C    (self-heating begins; still reversible)
STAGE 2:  Separator Melting          ~130-160°C   (internal short; POINT OF NO RETURN)
STAGE 3:  Cathode Decomposition      ~170-270°C   (O₂ released; chemistry-dependent)
STAGE 4:  Electrolyte Combustion     ~250-300°C   (gas generation; venting)
STAGE 5:  Full Thermal Runaway       >300-700°C   (self-sustaining; cannot be stopped)
```

### Chemistry Comparison (TR Onset Temperature)

```
LFP:      ~270-310°C onset   → MOST STABLE (no O₂ released below ~270°C)
NMC 111:  ~250-270°C onset   → Moderate stability
NMC 622:  ~220-240°C onset   → Common; moderate risk
NMC 811:  ~200-210°C onset   → High energy but lower TR onset
NCA:      ~170-200°C onset   → LEAST STABLE; earliest O₂ release
```

### UL 9540A Test Levels

```
LEVEL 1 (Cell):         Characterize single cell TR behavior
                        → Gas volume, composition, temperature, heat release

LEVEL 2 (Module):       Test propagation between cells within module
                        → Does TR propagate? How fast? Can barriers stop it?

LEVEL 3 (Unit/Rack):    Test propagation between modules within unit
                        → System protections effectiveness (BMS, cooling, suppression)

LEVEL 4 (Installation): Full-scale fire test
                        → Separation distances, ventilation rates, suppression sizing
```

### Key Gas Hazards from TR

```
GAS         HAZARD              LEL/TOXICITY           DETECTION
CO:         Toxic + Flammable   50 ppm TWA; LEL 12.5%  Electrochemical sensor
H₂:         Highly Flammable    LEL 4%                 Catalytic bead
HF:         Highly Toxic        3 ppm TWA; IDLH 30 ppm Electrochemical
CH₄:        Flammable           LEL 5%                 Infrared/catalytic
C₂H₄:       Flammable           LEL 2.7%               Infrared
```

### GB 38031 5-Minute Warning Decision Tree

```
TR Detected in EV pack?
  │
  ├── Does TR propagate? 
  │     ├── NO → PASS (best case — no propagation)
  │     └── YES → Does BMS warn occupants?
  │               ├── YES → Is warning ≥5 min before external fire?
  │               │           ├── YES → PASS
  │               │           └── NO → FAIL
  │               └── NO → FAIL
```

### Thermal Barrier Selection Guide

```
APPLICATION              MATERIAL             THICKNESS    COST
Consumer electronics:    Silicone foam        1-2 mm       Low
Standard EV:            Mica sheet           1-2 mm       Low
Premium EV:             Aerogel blanket      2-3 mm       High
High-energy EV (NMC811): Mica + aerogel     3-5 mm       Medium-High
Stationary ESS:         Ceramic fiber        5-10 mm      Medium
Critical/aviation:      Multi-layer composite 5-15 mm     Very High
```

### Fire Suppression Selection

```
APPROACH              COOLING?  FLAME STOP?  RE-IGNITION RISK  BEST FOR
Water mist:           YES ✓     YES ✓        Low               Sustained protection
Clean agent (Novec):  Minimal   YES ✓        HIGH              Quick knockdown
Inert gas (N₂):      NO        YES ✓        VERY HIGH         Explosion prevention
Aerosol:             Minimal   YES ✓        HIGH              Compact spaces
Combination (best):   YES ✓     YES ✓        LOW               Large BESS installations
  (clean agent + water mist)
```

---

*End of Document — 11_Thermal_Runaway_Testing.md*
