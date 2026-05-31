# IEC 62368-1 — Audio/Video, IT & Communication Equipment Safety

**Topic:** Hazard-Based Safety Engineering (HBSE) for A/V, IT, and Telecom Equipment  
**Standards:** IEC 62368-1:2018 (3rd edition), EN 62368-1:2014+A11:2017, UL 62368-1  
**SDO:** IEC TC 108, CENELEC, UL/CSA  
**Audience:** Product safety engineers, compliance engineers, design engineers, test lab personnel  
**Prerequisites:** Electrical safety fundamentals, basic circuit design, IEC 60950-1 or IEC 60065 familiarity helpful

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event |
|------|-------|
| 1970s | IEC 60950 (IT equipment safety) and IEC 60065 (A/V equipment safety) developed separately |
| 1999 | IEC 60950-1 First Edition (IT equipment — prescriptive rules) |
| 2001 | IEC 62368-1 project starts (merge 60950 + 60065 under HBSE methodology) |
| 2010 | IEC 62368-1 First Edition published (alternative to 60950/60065) |
| 2014 | IEC 62368-1 Second Edition (EN 62368-1:2014 in EU) |
| 2018 | IEC 62368-1 Third Edition (current definitive version) |
| 2020 | IEC 60950-1 withdrawn (December 20, 2020) — 62368-1 now mandatory |
| 2020 | IEC 60065 withdrawn — merged into 62368-1 |
| 2023 | UL 62368-1 (3rd Ed.) fully adopted in North America (cUL/CSA) |
| 2024 | All new product certifications must use 62368-1 (no grandfathering for new models) |

### 1.2 Why 62368-1 Replaced Two Standards

| Issue | Old Approach | 62368-1 Approach |
|-------|-------------|-----------------|
| Convergence | IT (60950) and A/V (60065) were separate; modern products are both | Single standard for all A/V + IT + telecom |
| Methodology | Prescriptive: "wire must be X gauge, clearance must be Y mm" | Performance-based: "prevent injury from energy source" |
| New technologies | Required amendments for each new technology | HBSE framework accommodates any technology |
| Flexibility | Little room for innovation without standard revision | Designer chooses safeguard that meets performance criteria |
| Global | Different interpretations between CB Scheme members | Unified hazard-based approach (less ambiguity) |

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 IEC 62368-1 Structure

| Part/Clause | Topic |
|-------------|-------|
| Clause 1-3 | Scope, references, definitions |
| Clause 4 | General requirements (HBSE methodology, compliance criteria) |
| Clause 5 | Electrically-caused injury (electric shock, energy sources) |
| Clause 6 | Electrically-caused fire (ignition, flame spread) |
| Clause 7 | Injury caused by hazardous substances (chemical) |
| Clause 8 | Mechanically-caused injury (moving parts, stability) |
| Clause 9 | Thermal burn injury (surface temperatures) |
| Clause 10 | Radiation injury (laser, LED, UV, acoustic) |
| Annexes | A-V (application guides, national differences, component requirements) |

### 2.2 HBSE Model (Core Concept)

```mermaid
graph LR
    subgraph "Hazard-Based Safety Engineering Model"
        ES[Energy Source<br/>Classified: ES1, ES2, ES3]
        TM[Transfer Mechanism<br/>(how energy reaches person)]
        BP[Body Part<br/>(hand, finger, face, ear)]
        INJURY[Potential Injury<br/>(severity determined by<br/>energy class + body part)]
    end
    
    ES -->|"Energy flows through"| TM
    TM -->|"Energy reaches"| BP
    BP -->|"Causes"| INJURY
    
    subgraph "Safeguards (prevent injury)"
        SG1[Equipment Safeguard<br/>(inherent in design)]
        SG2[Instructional Safeguard<br/>(marking, manual)]
        SG3[Personal Safeguard<br/>(user PPE — rare)]
    end
    
    SG1 -.->|"Blocks energy transfer"| TM
    SG2 -.->|"Warns user"| BP
```

---

## Chapter 3 — Technical Deep Dive

### 3.1 Energy Source Classification (Electrical)

| Class | Voltage (AC peak) | Voltage (DC) | Current | Power | Injury Risk |
|-------|-------------------|--------------|---------| ------|-------------|
| ES1 | ≤42.4V peak (≤30V RMS) | ≤60V | ≤2mA (normal), ≤70mA (fault) | — | No injury (safe under normal/fault) |
| ES2 | >ES1 but ≤42.4V abnormal | >ES1 but ≤60V abnormal | Various thresholds | ≤100 VA (capacitor) | Pain or injury possible; safeguard for ordinary persons |
| ES3 | >ES2 (mains voltage) | >60V DC | >ES2 thresholds | >100 VA | Serious injury or death possible; safeguard for ALL persons |

### 3.2 Safeguard Types

| Safeguard Type | Purpose | Example |
|----------------|---------|---------|
| Type 1 (Basic) | Protects ordinary person from ES2 under normal conditions | Basic insulation on wire, enclosure cover |
| Type 2 (Supplementary) | Second safeguard in case Type 1 fails | Supplementary insulation, earth bonding |
| Type 3 (Reinforced) | Single safeguard equivalent to Basic + Supplementary | Reinforced insulation (double insulation) |
| Type 4 (Infrastructure) | Part of building installation that acts as safeguard | Service entrance disconnect, protective earth in wiring |
| Type 5 (Personal) | User-applied protection | Insulated gloves (for service personnel only) |

### 3.3 Electrically-Caused Injury (Clause 5)

**Accessible Parts Classification:**

| Class | Accessible by | Safeguard requirement |
|-------|--------------|----------------------|
| ES1 accessible | Anyone (ordinary person) | None needed (inherently safe) |
| ES2 accessible | Ordinary person + Instructed person | Safeguard for ordinary person (Type 1 minimum) |
| ES3 accessible | Service person only (tool-required) | Safeguard to prevent ordinary person access (Type 1+2 or Type 3) |

**Clearances and Creepage (Clause 5.4):**

| Insulation Type | Between | Minimum |
|-----------------|---------|---------|
| Basic (Type 1) | ES3 to accessible part | Per Table 12 (based on voltage + pollution degree) |
| Supplementary (Type 2) | Same as basic | Per Table 12 |
| Reinforced (Type 3) | ES3 to accessible part | 2× basic or per Table 12 (stricter column) |
| Functional | Within same circuit (no safety function) | Per application need |

**Typical values (240V AC, Pollution Degree 2):**

| Insulation | Clearance | Creepage |
|------------|-----------|----------|
| Basic | 2.5 mm | 3.2 mm |
| Supplementary | 2.5 mm | 3.2 mm |
| Reinforced | 5.0 mm | 6.4 mm |

### 3.4 Electrically-Caused Fire (Clause 6)

| Concept | 62368-1 Approach |
|---------|-----------------|
| Ignition sources | Classified as PS1 (limited), PS2 (moderate), PS3 (hazardous) |
| PS1 | ≤15W or ≤30VA — unlikely to ignite materials |
| PS2 | ≤100W single fault — limited fire spread possible |
| PS3 | >PS2 — significant fire risk if not safeguarded |
| Fire enclosure | Required for PS2/PS3 (V-1 rated material minimum) |
| Materials | V-0 (self-extinguishing) or HB (slow burning) based on PS classification |
| Wiring | Current capacity vs. conductor size vs. insulation rating |
| Overload protection | Fuses, PTC, or electronic current limiting |

### 3.5 Thermal Burn Injury (Clause 9)

| Surface | Maximum Temperature (accessible, metallic) |
|---------|---------------------------------------------|
| Touched during normal operation | 70°C (low probability of contact), 55°C (held in hand) |
| Likely to be touched by hand | 70°C (metal), 80°C (non-metallic) |
| Not normally touched | 100°C (metal), 120°C (non-metallic) |

---

## Chapter 4 — Implementation Guide

### 4.1 Design for 62368-1 Compliance

| Design Area | Key Considerations |
|-------------|-------------------|
| Power supply | ES1/ES2/ES3 classification of all outputs; insulation between primary and secondary |
| PCB layout | Creepage/clearance maintained at all isolation barriers; slots where needed |
| Enclosure | Fire enclosure requirements (V-1 material for PS2); no openings toward ignition source |
| Batteries | IEC 62133 compliance; protection circuit (overcurrent, overvoltage, short circuit) |
| Thermal | Surface temperature limits; thermal cutoff for charging circuits |
| Grounding | Protective earth connection (if Class I); bonding impedance <0.1Ω |
| Markings | Ratings, warnings, symbols per IEC 60417 / ISO 7000 |
| User manual | Safety instructions in language of destination market |

### 4.2 Comparison: IEC 60950-1 vs. IEC 62368-1

| Aspect | IEC 60950-1 (old) | IEC 62368-1 (new) |
|--------|-------------------|-------------------|
| Approach | Prescriptive (rules-based) | Performance-based (HBSE) |
| Scope | IT equipment only | A/V + IT + Communication |
| Energy classification | SELV, TNV, HAZARDOUS | ES1, ES2, ES3 |
| Fire | LPS (Limited Power Source) | PS1, PS2, PS3 |
| Safeguards | Basic + supplementary/reinforced insulation | Type 1-5 safeguards |
| Touch current | Fixed 0.5mA limit | Based on ES class + body contact scenario |
| Creepage/clearance | Tables 2H, 2J, 2K | Table 12 (simplified from voltage + PD) |
| Status | WITHDRAWN (Dec 2020) | CURRENT (mandatory for new products) |
| Transition | Existing certs valid until expiry (no new certs) | Required for all new products from 2020 |

### 4.3 Test Program

| Test | Clause | What It Verifies |
|------|--------|-----------------|
| Touch current measurement | 5.3.3 | ES1/ES2/ES3 accessible part current |
| Dielectric strength (hipot) | 5.4.10 | Insulation withstands rated voltage |
| Creepage/clearance measurement | 5.4.2 | Physical spacing meets Table 12 |
| Temperature rise (steady state) | 9.2 | Surface and component temperatures |
| Abnormal operation | 4.7 | Single fault behavior (ES classification maintained) |
| Fire enclosure test (V-0/V-1) | 6.5 | Material flammability (needle flame, UL 94) |
| Ball pressure test | 6.5.2 | Material deformation at temperature |
| Drop/impact test | 8.4 | Mechanical integrity after impact |
| Stability test | 8.5 | Product doesn't topple under specified tilt |
| Battery tests (if applicable) | Clause 5 + IEC 62133 | Overcharge, short circuit, crush, thermal abuse |

---

## Chapter 5 — Certification & Compliance

### 5.1 Certification Paths

| Path | Process | Timeline |
|------|---------|----------|
| CB Scheme (IECEE) | Test at NCB → CB Test Report → accepted globally | 6-8 weeks (test + report) |
| UL Listing (North America) | Test at UL lab → UL mark (US + Canada) | 6-10 weeks |
| CSA (North America) | Test at CSA lab → CSA mark | 6-10 weeks |
| TÜV (EU) | Test at TÜV lab → GS mark (voluntary) + CE support | 6-10 weeks |
| National differences | CB report + national deviations → national cert | 2-4 weeks additional per market |

### 5.2 CB Scheme National Differences

| Country | Key Differences from IEC 62368-1 |
|---------|----------------------------------|
| US/Canada | Energy barriers per Annex DVB; higher humidity consideration |
| Japan | AC plug/socket requirements; specific markings |
| Korea | KS C IEC 62368-1 (minor editorial differences) |
| China | GB 4943.1 (Chinese adoption — may lag 1-2 years behind IEC edition) |
| Australia | Mains plug AS/NZS 3112; additional thermal requirements |
| India | IS 13252 (Indian adoption of IEC 62368-1) |

### 5.3 Factory Inspections

| Certification Body | Factory Audit | Frequency |
|-------------------|---------------|-----------|
| UL | Initial + quarterly follow-up inspections | 4×/year |
| CSA | Initial + annual or semi-annual | 1-2×/year |
| TÜV | Initial + annual factory inspection | 1×/year |
| CB Scheme | Not required (test report only) | N/A |
| CCC (China) | Initial + annual factory inspection | 1×/year |

---

## Chapter 6 — Regional & Domain Variants

| Application | Standard | Notes |
|-------------|----------|-------|
| Consumer IT/AV | IEC 62368-1 | Primary scope (laptops, TVs, routers, speakers) |
| Household appliances | IEC 60335 series | NOT covered by 62368-1 (separate standard for washing machines, fridges, etc.) |
| Medical equipment | IEC 60601-1 | Separate safety standard (much stricter requirements) |
| Industrial equipment | IEC 61010-1 | Measurement, control, laboratory equipment |
| Power supplies | IEC 62368-1 (if for IT/AV) | UL 1310 (Class 2 power unit) is alternative approach in US |
| LED lighting | IEC 62368-1 or IEC 60598 | Depends on primary function (display vs. illumination) |
| Telecom infrastructure | IEC 62368-3 | Part 3: Telecom network equipment (outdoor, central office) |
| EV chargers | IEC 61851 | Separate (not 62368-1) |

---

## Chapter 7 — Comparison of Safety Standards

| Dimension | IEC 62368-1 | IEC 60950-1 (legacy) | IEC 60335 | IEC 60601-1 |
|-----------|-------------|---------------------|-----------|-------------|
| Scope | A/V + IT + Comms | IT equipment only | Household appliances | Medical electrical |
| Methodology | HBSE (hazard-based) | Prescriptive rules | Prescriptive + parts | Risk management (ISO 14971) |
| Energy classes | ES1/ES2/ES3 | SELV/TNV/Hazardous | — | — |
| Touch current | ES class dependent | 0.5 mA (normal), 3.5 mA (fault) | 0.5-3.5 mA | 0.01-0.5 mA (very strict) |
| Fire | PS1/PS2/PS3 | LPS | Glow wire per part-2 | — |
| Leakage (patient) | N/A | N/A | N/A | 10μA (Type BF/CF) |
| Earthing | Class I or II | Class I or II | Class I or II or III | Class I or II (patient applied) |
| Approx pages | ~600 | ~400 | ~100 + part-2 | ~800 |
| Status | Current (mandatory) | Withdrawn (2020) | Current | Current |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 Energy Source Classification Flow

```mermaid
graph TB
    START[Identify energy source<br/>in the circuit]
    START --> Q1{Voltage ≤ 42.4V AC peak<br/>or ≤ 60V DC?}
    Q1 -->|"Yes"| Q2{Current ≤ limits?<br/>Power ≤ limits?}
    Q2 -->|"Yes"| ES1[ES1 — No injury<br/>No safeguard needed]
    Q2 -->|"No"| ES2[ES2 — Pain possible<br/>Safeguard for ordinary person]
    Q1 -->|"No"| Q3{Voltage > 42.4V AC<br/>or > 60V DC?}
    Q3 -->|"Yes, and<br/>available energy<br/>can cause injury"| ES3[ES3 — Serious injury<br/>Safeguard mandatory<br/>(Type 1+2 or Type 3)]
    Q3 -->|"Very limited<br/>energy (capacitor<br/>≤100 VA)"| ES2
```

### 8.2 Power Supply Safety Architecture

```mermaid
graph LR
    subgraph "Primary Side (ES3 — Mains)"
        MAINS[AC Mains Input<br/>100-240V AC<br/>(HAZARDOUS)]
        FUSE[Fuse / Overcurrent<br/>Protection]
        PFC[PFC + Switching<br/>Circuit]
    end
    
    subgraph "Isolation Barrier"
        XFMR[Transformer<br/>Reinforced Insulation<br/>(Type 3 safeguard)]
        OPTO[Optocoupler<br/>(Reinforced insulation)]
    end
    
    subgraph "Secondary Side (ES1 — SELV)"
        RECT[Rectifier +<br/>Output Filter]
        REG[Voltage<br/>Regulation]
        OUTPUT[DC Output<br/>5V/12V/20V<br/>(ES1: ≤60V DC)]
    end
    
    MAINS --> FUSE --> PFC --> XFMR
    XFMR --> RECT --> REG --> OUTPUT
    PFC -.->|"Feedback<br/>(isolated)"| OPTO
    OPTO -.-> REG
    
    style MAINS fill:#ff9999
    style OUTPUT fill:#99ff99
```

---

## Chapter 9 — Case Studies

### 9.1 USB-C Laptop Charger — 62368-1 Compliance

| Aspect | Detail |
|--------|--------|
| Product | 65W USB-C GaN charger (100-240V AC input, 5-20V DC output) |
| Classification | Primary: ES3 (240V mains). Secondary: ES1 (≤20V DC, ≤3.25A) |
| Insulation | Reinforced (Type 3) between primary and secondary (per Table 12) |
| Clearance | 5.0 mm minimum (reinforced, 240V RMS, Pollution Degree 2) |
| Creepage | 6.4 mm minimum (reinforced, CTI of PCB material dependent) |
| Fire enclosure | PS2 (65W output) → V-1 minimum housing material |
| Thermal | Surface: ≤95°C (non-metallic, not normally held) per Clause 9 |
| Tests | Hipot 4000V AC (reinforced), touch current <0.5mA, temp rise, drop |
| Certification | UL 62368-1 (US/CA) + CB test report → CE + other markets |
| Timeline | 8 weeks (including PCB layout review for clearances) |

### 9.2 Gaming Monitor — Transition from 60950-1

| Aspect | Detail |
|--------|--------|
| Product | 32" 4K gaming monitor with USB hub + internal power supply |
| Transition | Previously certified to IEC 60950-1; new model requires 62368-1 |
| Key changes | SELV → ES1 (conceptually similar but different documentation) |
| LPS → PS | LPS circuit → PS1/PS2 classification (similar effect) |
| New requirement | Clause 10 (radiation): LED backlight photobiological assessment per IEC 62471 |
| Insulation | Minimal change (clearance/creepage tables slightly different) |
| Effort | ~4 weeks incremental testing (mainly documentation update) |
| Lesson | Structure of compliance evidence changes significantly even though many physical requirements are similar |

---

## Chapter 10 — Future Evolution & Industry Trends

| Trend | Timeline | Description |
|-------|----------|-------------|
| IEC 62368-1 Edition 4 | 2025-2026 | Updates for GaN/SiC chargers, higher power USB-C, wireless charging |
| IEC 62368-3 | Current | Telecom infrastructure (outdoor, higher power) — growing adoption |
| USB-C PD 3.1 (240W) | Now | Safety implications of 48V/5A over USB-C cable |
| Wireless charging (Qi2/MPP) | Growing | IEC 63345 (wireless power safety) — complements 62368-1 |
| GaN/SiC power supplies | Now | Higher power density → thermal challenges, smaller clearances |
| AI-embedded devices | Emerging | Safety of always-on processing (thermal, battery) |
| Sustainability requirements | 2025+ | Repairability ↔ safety conflict (user-replaceable batteries vs. safety) |
| Lithium battery evolution | Growing | Solid-state batteries — new safety profile (IEC 62133 update) |
| Harmonized standard updates | Ongoing | CENELEC EN 62368-1 amendments for EU national differences |
| Remote/virtual safety testing | Growing | COVID accelerated remote witnessing; becoming standard practice |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is HBSE and how does it differ from the prescriptive approach of IEC 60950-1?  
**A:** **HBSE (Hazard-Based Safety Engineering)** is the core methodology of IEC 62368-1. Instead of prescriptive rules ("conductor shall be at least 0.75mm² cross-section"), HBSE identifies hazards and requires that energy transfer to body parts is prevented or reduced to safe levels. **The HBSE model has three elements:** (1) **Energy Source** — classified by severity: ES1 (no injury possible), ES2 (pain/injury for ordinary persons), ES3 (serious injury/death for anyone). (2) **Transfer Mechanism** — how energy could reach a person (direct contact, thermal radiation, acoustic energy, mechanical). (3) **Body Part** — what body part is exposed (hand, finger, ear, eye, torso). **Safeguard** — prevents energy from reaching the body part (basic insulation, enclosure, distance, etc.). **Difference from IEC 60950-1:** Old (60950-1): "For mains-powered IT equipment, basic insulation clearance shall be 2.5mm at 240V RMS, Pollution Degree 2." — This is a fixed RULE. You either meet it or you don't. No flexibility. New (62368-1): "An ES3 energy source (mains) shall be separated from accessible parts by a safeguard that prevents energy transfer. A Type 3 (reinforced) safeguard has clearance requirements per Table 12." — This FIRST identifies the hazard (ES3), THEN requires a safeguard (Type 3), THEN specifies performance criteria (Table 12 clearances). **Practical benefit of HBSE:** The designer has more flexibility in HOW to achieve safety. If a new technology doesn't fit the old prescriptive rules, HBSE allows: assess the energy → identify the hazard → apply an appropriate safeguard → verify performance. No standard amendment needed for new technologies.

### Tier 2: Mid-Level

**Q2:** Design the safety architecture for a 100W USB-C power delivery charger and identify all IEC 62368-1 compliance requirements.  
**A:** **Product:** 100W USB-C PD charger (100-240V AC → 5V/3A, 9V/3A, 15V/3A, 20V/5A). **1. Energy source classification:** Primary side (AC input): ES3 (240V RMS mains voltage → serious injury). Secondary side: At 5V/9V/15V: ES1 (below 60V DC AND current limited per Table 2 or dedicated overcurrent protection). At 20V/5A (100W): ES1 (still below 60V DC; 100W is within PS2 fire classification). Internal capacitors >100VA: Need discharge time assessment (ES2/ES3 consideration during servicing). **2. Isolation barrier design:** Type 3 (reinforced) insulation between primary (ES3) and secondary (ES1): Clearance: 5.0mm (reinforced, 240V peak, PD2, material group IIIa). Creepage: 6.4mm (reinforced, CTI ≥100; or 5.0mm if CTI ≥400). Transformer: reinforced insulation between primary and secondary windings (triple-insulated wire or separate bobbins with ≥8mm creepage on bobbin). Optocoupler (feedback): reinforced insulation rated (e.g., Vishay VOF1 8mm clearance through device). Y-capacitor (EMC): safety-rated Y1 (reinforced) or 2×Y2 (basic + supplementary). **3. Fire classification (Clause 6):** 100W output → PS2 (Limited Power Source is <100W; but 100W is at boundary — verify per 6.3). If PS2: fire enclosure required (V-1 rated UL94 material minimum). GaN MOSFET area: assess as potential ignition source → routing copper current capacity. PCB: FR-4 (V-0 rated) — meets fire enclosure requirements if no ventilation openings toward ignition. **4. Protection circuits:** Overcurrent: fuse on AC input (time-delay, 2A/250V rated). Short circuit: electronic fold-back or shutdown on each output voltage. Overvoltage: output OVP clamp (prevent >20V reaching connected device). Over-temperature: NTC sensor + thermal shutdown (derates or shuts off above 100°C internal). **5. Thermal (Clause 9):** Surface temperature: ≤95°C for non-metallic surfaces not normally held during use. For surfaces in continuous hand contact: ≤60°C (metallic) or ≤70°C (non-metallic). Internal component temperature: each component within its ratings (capacitor, transformer). **6. Testing required:** Hipot: 4000V AC (or 5656V DC) between primary and secondary — 1 minute (production: 3000V AC for 1 sec). Touch current: ≤0.5 mA from any accessible part under normal + single fault conditions. Temperature rise: steady-state at full load (25°C ambient) — measure all critical points. Abnormal tests: single fault (shorted component, open protection) → verify ES3 not accessible. Drop test: 1m drop on concrete — verify insulation intact after (hipot re-test). Ball pressure: housing material at 75°C (non-current carrying) or 125°C (current carrying). Creepage/clearance: physical measurement of all isolation barriers. **7. Markings required:** Input ratings (100-240V~, 50/60Hz, 1.5A). Output ratings (5V/3A, 9V/3A, 15V/3A, 20V/5A; 100W max). Safety symbols: IEC 60417-5172 (Class II double insulation square). Certification marks (UL, CE). "For indoor use only" (if applicable).

### Tier 3: Senior

**Q3:** You're developing a product that doesn't fit cleanly into IEC 62368-1 categories — a high-power (500W) industrial IoT gateway with 48V DC Power-over-Ethernet outputs, mains input, and USB-C 240W pass-through. How do you handle the safety architecture and certification?  
**A:** **Challenge:** This product spans multiple safety domains: Mains input (240V AC) → IEC 62368-1 primary. 48V PoE output → potentially ES2 (above 42.4V DC for short-circuit current scenarios). USB-C 240W (48V/5A) → new territory (PD 3.1). Industrial environment → potentially IEC 61010-1 instead of 62368-1. **1. Standard selection:** Primary standard: IEC 62368-1 (IT/communication equipment — gateway function). Secondary consideration: IEC 62368-3 (telecom infrastructure — if installed in telecom environments). PoE output: IEEE 802.3bt (physical layer) + IEC 62368-1 safety (energy classification). USB-C 240W: IEC 62368-1 + IEC 62680-1-2 (USB Type-C) — per RED Art 3.3(a) for EU. Industrial: if sold as general IT equipment in industrial environment → 62368-1 applies (not 61010-1, which is for measurement/control equipment). **2. Energy source classification:** AC mains input: ES3 (240V AC — full reinforced isolation required). 48V PoE outputs: Analysis: 48V DC > 42.4V AC peak equivalent → borderline. Under normal load: 48V/≤30W per port — ES1 if power limited (check Table 2). Under fault (all power to one port): 48V at potentially higher current. Decision: Classify PoE output as ES2 (precautionary). Safeguard: Basic insulation (Type 1) between PoE ports and accessible metalwork. User cannot access PoE pins without tool (RJ45 housed in enclosed equipment). USB-C 240W output: 48V DC (PD 3.1) → same analysis as PoE. Per IEC 62368-1 and USB-IF safety: 48V is >42.4V AC peak equivalent. However: USB-C PD 3.1 requires EPR (Extended Power Range) negotiation before 48V applied. Before negotiation: 5V only (ES1). After negotiation: 48V only on verified cable with E-marker. Classification: ES1 during startup → ES2 after EPR negotiation. Safeguard: automatic disconnect within fault tolerance time + cable detection. **3. Isolation architecture:**
```
[AC MAINS (ES3)] ←—— Reinforced (Type 3) ——→ [Internal DC bus (ES2/ES3)]
                                                      |
[Internal DC] ←—— Basic (Type 1) ——→ [48V PoE outputs (ES2)]
[Internal DC] ←—— Basic (Type 1) ——→ [USB-C 48V output (ES2)]
[Internal DC] ←—— Functional ——→ [Logic (ES1 — 3.3V/5V/12V)]
[PoE ports] ←—— Basic (Type 1) ——→ [Chassis/accessible parts]
```
**4. Fire safety (500W):** 500W total → PS3 (significantly above PS1/PS2 thresholds). Requirements: Full fire enclosure (V-0 rated material required for PS3). Internal wiring: current capacity verified for maximum load per conductor. Overload protection: electronic + fuse (dual protection for PS3). PCB: FR-4 V-0; or metal-core PCB in high-current areas. Chassis: metal preferred (inherent fire containment). **5. Thermal management:** 500W at 90% efficiency → 50W heat dissipation internally. Forced air cooling likely required (fan) → test at blocked ventilation (abnormal). Surface temperature: industrial IP-rated enclosure may exceed consumer limits — acceptable if "not normally touched" classification (≤100°C metallic surface). Component derating: 80% rule for all semiconductors at maximum ambient (40°C or 50°C industrial). **6. PoE safety considerations:** IEEE 802.3bt Type 4: 71.3W per port maximum (90W at PSE, ~71W at PD). Port-to-port isolation: Basic insulation between PoE ports (prevent single fault propagating). Detection protocol: PoE detection (resistance signature) before power applied — prevents powering non-PoE device. Overload: per-port current limiting + total power budget management. Cable: must not exceed 100m Ethernet spec (voltage drop → thermal in cable). **7. USB-C 240W safety:** IEC 62680-1-2 (Type-C connector safety): EPR cable required (E-marker IC in cable verifies 48V/5A capability). Gateway checks cable E-marker before applying 48V. If cable doesn't support EPR → limit to 20V/5A (100W) or lower. Short-circuit protection: <100μs disconnect (protect cable from fire). Overcurrent: electronic fuse (eFuse) with <50ms trip time. **8. Certification strategy:** CB Scheme: test to IEC 62368-1 (3rd Ed.) at a recognized NCB. National differences: US (UL 62368-1), EU (EN 62368-1), Japan, Korea. PoE: reference IEEE 802.3bt for power delivery compliance (not safety-certified but functionally verified). USB-C PD 3.1: USB-IF certification (functional) + 62368-1 safety (energy classification). Industrial rating: if marketed for industrial, consider additional testing (vibration, temperature range) though not part of 62368-1. **9. Expected challenges:** 48V classification ambiguity: discuss with certification body (CB/UL/TÜV) BEFORE design finalization. USB-C 240W: relatively new (PD 3.1) — limited certified products as reference. Pre-engagement: meet with UL/TÜV assessor for design review before prototype. This prevents expensive redesign after testing.

---

## Chapter 12 — Cheat Sheet & Quick Reference

### Energy Source Classification

```
ES1 (Safe):     ≤42.4V AC peak / ≤60V DC AND current-limited → No safeguard needed
ES2 (Moderate): >ES1 but limited energy → Safeguard for ordinary person (Type 1)
ES3 (Dangerous): Mains voltage (>42.4V AC pk / >60V DC, high energy) → Type 3 safeguard
```

### Power Source (Fire) Classification

```
PS1: ≤15W or ≤30VA          → No fire enclosure needed
PS2: >PS1 but ≤100W         → V-1 rated fire enclosure required
PS3: >100W                   → V-0 rated fire enclosure required
```

### Insulation Requirements (240V, PD2)

```
Type         Purpose              Clearance    Creepage
Basic        ES3 to ES1 barrier   2.5 mm       3.2 mm
Supplementary  Backup to basic    2.5 mm       3.2 mm
Reinforced   Single barrier       5.0 mm       6.4 mm
                                  (equivalent to basic + supplementary)
```

### Key Tests

```
Test                      Clause    Pass Criteria
Hipot (dielectric)        5.4.10    No breakdown at test voltage
Touch current             5.3.3     ≤0.5 mA (normal), ≤3.5 mA (fault)
Temperature rise          9.2       Below material/component ratings
Abnormal operation        4.7       No ES3 accessible; no fire
Drop test                 8.4       Insulation intact (re-hipot)
Ball pressure             6.5.2     Impression ≤2mm diameter
Creepage/clearance        5.4.2     Meets Table 12 minimums
```

### Mapping: IEC 60950-1 → IEC 62368-1

```
60950-1 Concept        →  62368-1 Equivalent
SELV                   →  ES1 circuit
TNV                    →  ES2 (limited energy telecom)
Hazardous voltage      →  ES3
LPS                    →  PS1 (≤15W) or PS2 (≤100W)
Basic insulation       →  Type 1 safeguard
Reinforced insulation  →  Type 3 safeguard
Class I (earthed)      →  Class I (unchanged concept)
Class II (double)      →  Class II (unchanged concept)
Accessible part test   →  Accessibility + ES classification
```

---

*End of Document — 03_IEC_62368_1_Safety.md*
