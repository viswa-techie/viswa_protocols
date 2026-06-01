# Data Center Energy Efficiency — PUE, WUE, CUE & Thermal Management

**Topic:** Data center energy efficiency metrics (PUE, WUE, CUE, ERE), ASHRAE thermal guidelines, ISO/IEC 30134 KPIs, EU Code of Conduct for Data Centres, and optimization strategies  
**Standard:** PUE/WUE/CUE/ERE (The Green Grid); ASHRAE TC 9.9; ISO/IEC 30134 series; EU Code of Conduct for Energy Efficiency in Data Centres (2023)  
**SDO:** The Green Grid; ASHRAE (American Society of Heating, Refrigerating and Air-Conditioning Engineers); ISO/IEC; European Commission JRC  
**Audience:** Data center facility engineers, mechanical engineers, sustainability managers, operations teams, energy managers, infrastructure architects  
**Prerequisites:** Basic thermodynamics, HVAC concepts, electrical power distribution, data center architecture fundamentals, Uptime Institute Tier concepts

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 2006 | EPA "Report to Congress on Server and Data Center Energy Efficiency" | First major government report quantifying DC energy consumption problem |
| 2007 | **The Green Grid** founded | Industry consortium (AMD, APC, Dell, HP, IBM, Intel, Microsoft, others) to standardize DC efficiency metrics |
| 2007 | **PUE metric introduced** by The Green Grid | Power Usage Effectiveness becomes universal efficiency metric |
| 2008 | Google first to publicly report PUE | Transparency initiative; claimed PUE of 1.21; set industry benchmark |
| 2009 | WUE, CUE metrics introduced | Water and carbon efficiency complementing PUE |
| 2009 | EU Code of Conduct for Data Centres (v1) | European voluntary code for energy efficiency best practices |
| 2011 | ASHRAE TC 9.9 expanded thermal envelopes | A1-A4 classification allowing higher temperatures; reduced cooling energy |
| 2012 | The Green Grid: PUE measurement methodology refined (Category 1-3) | Addressed inconsistent PUE reporting; standardized measurement points |
| 2014 | **ISO/IEC 30134-2** published (PUE as ISO standard) | PUE becomes international standard; formal measurement requirements |
| 2016 | ISO/IEC 30134 series expanded (Parts 1-4) | REF (Renewable Energy Factor); ERE; WUE standardized |
| 2018 | Facebook reports PUE of 1.10 (Lulea, Sweden) | Demonstrates ultra-efficient design with free cooling |
| 2020 | EU Green Deal includes data centers | DC energy efficiency becomes regulatory priority in Europe |
| 2022 | EU Energy Efficiency Directive (EED) recast | EU member states must collect and publish DC energy data; PUE <1.3 expected for new DCs |
| 2023 | **EU CoC for Data Centres updated** (v2023) | Comprehensive best practices catalog; 300+ efficiency recommendations |
| 2024 | AI DC power density crisis | AI workloads (100+ kW/rack) fundamentally change efficiency equations; traditional PUE assumptions challenged |

### 1.2 The Energy Problem

| Metric | Value (2024) | Context |
|--------|:---:|---|
| Global DC electricity consumption | ~400-500 TWh/year | 1-2% of global electricity production |
| Projected DC consumption (2030) | ~800-1000 TWh/year | AI/ML training driving exponential growth |
| Average industry PUE | 1.55-1.60 | Significant waste; 35-38% of power goes to non-IT overhead |
| Hyperscaler PUE | 1.08-1.20 | Best-in-class; minimal overhead |
| Cooling as % of total DC power | 30-40% (average); <10% (hyperscale) | Largest opportunity for efficiency improvement |
| Water consumption (average DC) | 1.8 L per kWh IT energy | Growing concern in water-scarce regions |

---

## Chapter 2 — Metric Architecture

### 2.1 PUE — Power Usage Effectiveness

$$PUE = \frac{\text{Total Facility Power}}{\text{IT Equipment Power}}$$

| Component | Detail |
|-----------|--------|
| **Total Facility Power** | Everything consumed at utility meter: IT load + cooling + lighting + security + UPS losses + transformer losses + PDU losses + generator parasitic loads |
| **IT Equipment Power** | Power delivered to compute, storage, and network equipment (measured at PDU output or rack input) |
| **Ideal PUE** | 1.0 (impossible in practice: no overhead at all) |
| **Best achievable** | 1.03-1.08 (hyperscale; free cooling climate; minimal conversion losses) |
| **Good** | 1.2-1.4 |
| **Average** | 1.5-1.6 |
| **Poor** | >1.8 (legacy facilities; over-cooled; inefficient UPS) |

### 2.2 PUE Measurement Categories

| Category | Measurement Method | Accuracy | Typical Use |
|:--------:|:---:|:---:|---|
| **Category 1** | UPS output power (IT) vs. utility meter (total) | Lowest (includes UPS efficiency in IT measurement) | Quick assessment; legacy facilities |
| **Category 2** | PDU output power (IT) vs. utility meter (total) | Medium | Standard reporting; most companies |
| **Category 3** | IT equipment input power (measured at server PSU) vs. utility meter (total) | Highest | Detailed analysis; hyperscalers; ISO 30134 compliant |

### 2.3 PUE Breakdown

```
PUE components (for PUE = 1.50):

Total Facility Power = 1.50 MW
├── IT Equipment Power = 1.00 MW  (66.7% of total)
├── Cooling = 0.30 MW             (20.0% of total)
│   ├── Chillers: 0.15 MW
│   ├── CRAH/CRAC fans: 0.08 MW
│   ├── Cooling towers/pumps: 0.05 MW
│   └── Humidification: 0.02 MW
├── Power delivery losses = 0.12 MW (8.0% of total)
│   ├── UPS losses: 0.06 MW
│   ├── Transformer losses: 0.03 MW
│   └── PDU/switchgear losses: 0.03 MW
├── Lighting = 0.03 MW            (2.0% of total)
└── Other = 0.05 MW               (3.3% of total)
    ├── Security systems
    ├── Fire suppression
    └── Office/ancillary
```

### 2.4 Other Efficiency Metrics

| Metric | Formula | Unit | Target | Purpose |
|--------|---------|:----:|:------:|---------|
| **WUE** | $\frac{\text{Annual Water Usage (liters)}}{\text{IT Equipment Energy (kWh)}}$ | L/kWh | <1.0 | Water consumption per unit IT energy; critical in water-scarce regions |
| **CUE** | $\frac{\text{Total CO₂ emissions (kgCO₂)}}{\text{IT Equipment Energy (kWh)}}$ | kgCO₂/kWh | →0 | Carbon intensity; depends on grid mix + on-site generation |
| **ERE** | $\frac{\text{Total Energy – Reused Energy}}{\text{IT Equipment Energy}}$ | ratio | <1.0 (possible with reuse) | Measures waste heat reuse; ERE < PUE if heat is recaptured |
| **REF** | $\frac{\text{Renewable Energy Used}}{\text{Total Energy Used}}$ | % (0-1) | →1.0 | Fraction of energy from renewable sources |
| **DCIE** | $\frac{\text{IT Equipment Power}}{\text{Total Facility Power}} × 100$ | % | →100% | Inverse of PUE as percentage; deprecated (PUE preferred) |
| **GEC** (Green Energy Coefficient) | $\frac{\text{Green Energy}}{\text{Total Energy}}$ | % | →100% | ISO 30134-3; similar to REF |

---

## Chapter 3 — ASHRAE TC 9.9 Thermal Guidelines

### 3.1 ASHRAE Server Environment Classes

| Class | Dry Bulb Temperature | Humidity Range | Dew Point | Target Environment |
|:-----:|:---:|:---:|:---:|---|
| **A1** | 15-32°C (59-89.6°F) | 20-80% RH; DP ≤17°C | -12°C to 17°C | Mission-critical; tightly controlled; traditional DC |
| **A2** | 10-35°C (50-95°F) | 20-80% RH; DP ≤21°C | -12°C to 21°C | Most IT equipment; modern enterprise servers; slightly relaxed |
| **A3** | 5-40°C (41-104°F) | 8-85% RH; DP ≤24°C | -12°C to 24°C | Extended range; ITE with wider thermal tolerance; enables more free cooling hours |
| **A4** | 5-45°C (41-113°F) | 8-90% RH; DP ≤24°C | -12°C to 24°C | Widest envelope; purpose-built hardware; maximum free cooling; hyperscale/OCP designs |
| **B** | 5-35°C | 8-80% RH | — | Office/storage environments |
| **C** | 5-40°C | 8-80% RH | — | Point-of-sale/industrial environments |

### 3.2 Free Cooling Implications

| Class | Annual Free Cooling Hours (Northern Europe) | Annual Free Cooling Hours (US Southeast) | Energy Savings vs. A1 |
|:-----:|:---:|:---:|:---:|
| A1 (max 32°C) | ~5,500 hours | ~2,000 hours | Baseline |
| A2 (max 35°C) | ~6,500 hours | ~3,000 hours | 10-15% cooling energy reduction |
| A3 (max 40°C) | ~7,500 hours | ~5,000 hours | 25-35% cooling energy reduction |
| A4 (max 45°C) | ~8,200 hours | ~6,500 hours | 40-50% cooling energy reduction |

### 3.3 Temperature Impact on IT Equipment

| Temperature | Server Power Consumption | Fan Power | Reliability Impact |
|:-----------:|:---:|:---:|---|
| 20°C (reference) | 100% (baseline) | 100% | Baseline failure rate |
| 25°C | +2% | +5-10% | Minimal increase |
| 30°C | +4% | +15-25% | Slight increase in component stress |
| 35°C (A2 max) | +7% | +30-50% | Measurable increase; within spec |
| 40°C (A3 max) | +10% | +60-100% | Server fans at high speed; some throttling possible |
| 45°C (A4 max) | +15% | +100-150% | Designed for; purpose-built hardware needed |

---

## Chapter 4 — Implementation Guide

### 4.1 PUE Improvement Strategies (by impact)

| Strategy | PUE Impact | Cost | Implementation Complexity |
|----------|:---:|:---:|:---:|
| **Raise supply air temperature** (to 25-27°C from 18-20°C) | -0.05 to -0.15 PUE | Low (operational change) | Low |
| **Blanking panels & hot/cold aisle containment** | -0.05 to -0.20 PUE | Low-Medium | Low |
| **Free cooling (economizer) — air-side** | -0.10 to -0.30 PUE | Medium | Medium (climate dependent) |
| **Free cooling — water-side economizer** | -0.10 to -0.25 PUE | Medium-High | Medium |
| **High-efficiency UPS** (>96% efficiency) | -0.03 to -0.08 PUE | Medium | Low (upgrade) |
| **Variable speed drives** (fans, pumps) | -0.05 to -0.15 PUE | Medium | Medium |
| **Liquid cooling** (direct-to-chip or rear-door) | -0.10 to -0.25 PUE | High | High |
| **Immersion cooling** | -0.15 to -0.30 PUE | Very High | Very High |
| **Eliminate raised floor** (overhead cable tray; in-row cooling) | -0.02 to -0.10 PUE | High (new construction) | High |
| **DC power distribution** (eliminate AC→DC→AC conversions) | -0.03 to -0.06 PUE | High | Very High (non-standard) |

### 4.2 Cooling Architecture Comparison

| Technology | PUE Achievable | WUE | Best Climate | Capital Cost | Maintenance |
|-----------|:---:|:---:|:---:|:---:|:---:|
| **Traditional DX (split units)** | 1.6-2.0 | 0 (no water) | Any (but expensive in hot) | Low | Medium |
| **Chilled water + CRAH** | 1.4-1.6 | 1.5-3.0 | Any | Medium | Medium |
| **Air-side economizer** | 1.1-1.3 | 0-1.0 | Cool/temperate (Nordics; Pacific NW) | Low-Medium | Low |
| **Water-side economizer** | 1.2-1.4 | 0.5-2.0 | Cool/temperate | Medium | Medium |
| **Evaporative cooling** | 1.1-1.3 | 2.0-4.0 | Hot-dry (Arizona; ME) | Medium | Medium |
| **Rear-door heat exchanger** | 1.1-1.3 | 0.5-1.5 | Any (with cool water source) | Medium-High | Low |
| **Direct liquid cooling (DLC)** | 1.02-1.10 | 0-0.5 | Any (hot liquid can reject to ambient easily) | High | Medium |
| **Immersion cooling** | 1.02-1.06 | 0 | Any | Very High | Low (once deployed) |

### 4.3 PUE Measurement Implementation

| Metering Point | What to Measure | Equipment |
|:---:|---|---|
| **Utility entrance** | Total facility power (kW; kWh) | Revenue-grade power meter (±0.5% accuracy); CT/PT rated for full load |
| **Generator output** | On-site generation when on generator | Integrated generator metering; kW/kWh |
| **UPS output** | Total critical power (IT + critical cooling if on UPS) | UPS integrated meter or external CT |
| **PDU output** (Category 2) | IT power per rack row/zone | Intelligent PDUs with per-outlet metering |
| **Server PSU input** (Category 3) | Individual server power | IPMI/Redfish/iLO power reporting; or branch circuit metering |
| **Cooling plant** | Chiller kW; pump kW; fan kW; tower kW | Sub-metering per cooling component |
| **BMS integration** | All environmental data; setpoints; outside air temp | BMS with trend logging; 15-minute intervals minimum |

---

## Chapter 5 — ISO/IEC 30134 & EU Regulations

### 5.1 ISO/IEC 30134 Series

| Part | Title | Metric | Status |
|:----:|-------|:------:|:------:|
| Part 1 | Overview and general requirements | Framework | Published 2016 |
| Part 2 | Power Usage Effectiveness (PUE) | PUE | Published 2016 (ISO standard) |
| Part 3 | Renewable Energy Factor (REF) | REF | Published 2016 |
| Part 4 | IT Equipment Energy Efficiency (ITEEsv/ITEEuv) | ITE efficiency | Published 2017 |
| Part 5 | IT Equipment Utilization (ITEUsv/ITEUuv) | Utilization | Published 2021 |
| Part 6 | Energy Reuse Factor (ERF) | ERF | Published 2021 |
| Part 7 | Cooling Efficiency Ratio (CER) | CER | Published 2023 |
| Part 8 | Carbon Usage Effectiveness (CUE) | CUE | Published 2023 |
| Part 9 | Water Usage Effectiveness (WUE) | WUE | Published 2023 |

### 5.2 EU Energy Efficiency Directive (EED) — Data Center Requirements

| Requirement | Detail | Timeline |
|-------------|--------|:---:|
| **Reporting obligation** | DC operators (>500 kW) must report energy performance data to national authorities | May 2024 (first reports) |
| **Data reported** | Total energy; IT energy; PUE; WUE; cooling setpoints; waste heat reuse; renewable energy %; floor area; installed capacity | Annual |
| **Public database** | European Commission to publish aggregated DC energy data | 2025+ |
| **PUE target** | New data centers should achieve PUE <1.3 | Best practice target |
| **Waste heat** | Encourage waste heat reuse (district heating); report waste heat availability | Reporting from 2024 |
| **Audit obligation** | Energy audits per EED for large enterprises (incl. DC operations) | Ongoing |

### 5.3 EU Code of Conduct for Data Centres (2023)

| Category | Best Practices (examples from 300+ recommendations) |
|:---:|---|
| **IT equipment** | Virtualization; right-sizing; decommission unused (zombie) servers; power management (C-states); server refresh cycles |
| **Cooling** | Raise setpoints; containment; free cooling; variable speed drives; hot/cold aisle; eliminate humidity control where possible |
| **Power** | High-efficiency UPS (>96%); modular UPS matching load; eliminate unnecessary transformations; power factor correction |
| **Building** | Insulation; building orientation; use of thermal mass; commissioning verification |
| **Management** | PUE monitoring (real-time); capacity planning; decommissioning processes; energy-aware procurement |
| **Renewables** | On-site generation; green energy procurement (PPAs); RECs; energy storage |

---

## Chapter 6 — Regional Variants & Climate Strategies

### 6.1 Climate-Specific Cooling Strategies

| Climate Zone | Strategy | Examples | Achievable PUE |
|:---:|---|:---:|:---:|
| **Nordic (cold, dry)** | Air-side free cooling year-round; minimal mechanical cooling | Stockholm; Helsinki; Iceland; Norway | 1.03-1.10 |
| **Maritime (cool, humid)** | Air-side economizer 8-10 months; evaporative boost; dehumidification management | Ireland; Netherlands; UK; Pacific NW | 1.10-1.20 |
| **Continental (seasonal)** | Air-side free cooling 6-8 months; water-side economizer; partial mechanical cooling in summer | Central Europe; Northern US; Canada | 1.15-1.30 |
| **Hot-dry (desert)** | Evaporative cooling (very effective in dry air); water-side; no air-side in summer | Arizona; Nevada; Middle East | 1.10-1.25 (but high WUE) |
| **Hot-humid (tropical)** | Mechanical cooling dominant; high-efficiency chillers; thermal storage; raised setpoints | Singapore; India; Southeast Asia | 1.30-1.50 |
| **Extreme cold** | Free cooling with anti-freeze; exhaust air recirculation; freeze protection | Scandinavia; Northern Canada | 1.03-1.08 |

### 6.2 Notable Efficient Data Centers

| Facility | Location | PUE | Key Technology |
|----------|:---:|:---:|---|
| Google Hamina | Finland | 1.10 | Seawater cooling from Baltic Sea; waste heat to district heating |
| Facebook Lulea | Sweden | 1.07 | 100% outside air free cooling; Arctic climate; hydro power |
| Microsoft Quincy | Washington, USA | 1.12 | Air-side free cooling; hydroelectric power; 100% carbon-free |
| Equinix SG3 | Singapore | 1.35 | Exceptional for tropical; high-efficiency chiller plant; raised setpoints to A2 |
| Green Mountain DC1 | Norway | 1.15 | Former NATO ammunition storage in mountain; fjord water cooling; 100% hydro |
| Microsoft Natick | Scotland (subsea) | 1.07 | Experimental: underwater DC; ocean cooling; sealed; no humidity issues |

---

## Chapter 7 — Comparison: Efficiency Approaches

### 7.1 PUE vs. WUE Tradeoff

| Cooling Method | PUE | WUE | Tradeoff |
|:---:|:---:|:---:|---|
| Air-cooled chiller (no water) | 1.5-1.8 | 0 L/kWh | High PUE but zero water; suitable for water-scarce regions |
| Water-cooled chiller + cooling tower | 1.3-1.5 | 1.5-3.0 L/kWh | Lower PUE but significant water consumption |
| Evaporative cooling | 1.1-1.3 | 2.0-5.0 L/kWh | Excellent PUE in hot-dry climate; highest water use |
| Air-side free cooling | 1.05-1.15 | 0 L/kWh | Best of both worlds; climate-limited |
| Liquid cooling (closed loop) | 1.02-1.10 | 0-0.3 L/kWh | Excellent PUE; minimal water; high capex |

### 7.2 Cooling Technology Comparison

| Technology | PUE Range | CAPEX | OPEX | Density Support | Retrofit? |
|-----------|:---:|:---:|:---:|:---:|:---:|
| Traditional CRAC (DX) | 1.6-2.0 | Low | High | 5-8 kW/rack | Existing |
| In-row cooling | 1.3-1.5 | Medium | Medium | 10-20 kW/rack | Yes |
| Hot aisle containment | -0.1 to -0.2 improvement | Low | Low (reduced fan energy) | +30% capacity | Yes |
| Rear-door heat exchanger | 1.1-1.3 | Medium | Low | 20-40 kW/rack | Yes |
| Direct liquid cooling (DLC) | 1.02-1.10 | High | Low | 50-100 kW/rack | Partial |
| Immersion (single-phase) | 1.02-1.06 | Very High | Very Low | 100-250 kW/rack | No (purpose-built) |
| Immersion (two-phase) | 1.01-1.04 | Highest | Very Low | 100-300+ kW/rack | No (purpose-built) |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 PUE Measurement Architecture

```mermaid
graph TB
    subgraph "Utility Metering"
        UTIL[Utility Meter<br/>━━━━━━━━━━━<br/>Total Facility Power<br/>(denominator measure point)]
    end
    
    subgraph "Power Distribution"
        XFMR[Transformer<br/>losses: 1-3%]
        UPS[UPS System<br/>losses: 2-6%]
        PDU[PDU/Switchgear<br/>losses: 1-2%]
    end
    
    subgraph "IT Load (numerator)"
        IT[IT Equipment<br/>━━━━━━━━━━━<br/>Servers; Storage;<br/>Network; Comms<br/><br/>Category 2: PDU output<br/>Category 3: Server PSU input]
    end
    
    subgraph "Non-IT Overhead"
        COOL[Cooling<br/>━━━━━━━━━━━<br/>Chillers; CRAHs;<br/>Pumps; Towers; Fans<br/>(typically 30-40% of total)]
        
        LIGHT[Lighting +<br/>Security + Fire<br/>(typically 2-5%)]
        
        LOSS[Distribution Losses<br/>━━━━━━━━━━━<br/>Transformer; UPS; PDU<br/>(typically 8-12%)]
    end
    
    UTIL --> XFMR --> UPS --> PDU --> IT
    UTIL --> COOL
    UTIL --> LIGHT
    XFMR -.->|"loss"| LOSS
    UPS -.->|"loss"| LOSS
    PDU -.->|"loss"| LOSS
```

### 8.2 Cooling System Comparison

```mermaid
graph LR
    subgraph "Traditional (PUE 1.5-1.8)"
        T1[Chiller<br/>COP: 3-5]
        T2[Cooling Tower<br/>or Condenser]
        T3[CRAH/CRAC<br/>raised floor]
        T4[IT Load<br/>5-8 kW/rack]
        T1 --> T3 --> T4
        T1 --> T2
    end
    
    subgraph "Free Cooling (PUE 1.05-1.15)"
        F1[Outside Air<br/>filtered; controlled]
        F2[Economizer<br/>dampers; mixing]
        F3[IT Load<br/>in A3/A4 class]
        F1 --> F2 --> F3
    end
    
    subgraph "Liquid Cooling (PUE 1.02-1.10)"
        L1[Warm Water<br/>45°C supply<br/>to CPU/GPU]
        L2[CDU<br/>Coolant<br/>Distribution]
        L3[Dry Cooler<br/>(no chiller needed!<br/>water is warm enough)]
        L4[IT Load<br/>50-100 kW/rack]
        L2 --> L1 --> L4
        L2 --> L3
    end
```

---

## Chapter 9 — Case Studies

### 9.1 Case Study: PUE Improvement Program — Legacy to Efficient

| Aspect | Detail |
|--------|--------|
| Facility | Enterprise data center; 2 MW IT load; built 2008; original PUE: 2.10 |
| Problem | Energy costs $3.7M/year; 52% of electricity consumed by cooling; over-cooled (18°C supply air); no economizer; poor airflow management (mixing hot and cold) |
| Program | **Phase 1 (Quick wins — 3 months; $50K):** (1) Installed blanking panels in empty rack positions. (2) Sealed cable cutouts in raised floor tiles. (3) Raised supply air temperature from 18°C to 24°C (within ASHRAE A2). (4) Eliminated unnecessary humidification (humidity was already within range). Result: PUE 2.10 → 1.75 (−17% energy). **Phase 2 (Containment — 6 months; $200K):** (5) Hot aisle containment installed. (6) Variable speed drives retrofitted to CRAH fans. (7) Cooling control system upgraded (temperature-based vs. fixed speed). Result: PUE 1.75 → 1.50 (−14% additional). **Phase 3 (Economizer — 12 months; $500K):** (8) Water-side economizer added (plate heat exchanger in chilled water loop). (9) Exploits ambient temperatures below 15°C (available 5 months/year in this climate). (10) Chiller stages reduced; some shut down during winter entirely. Result: PUE 1.50 → 1.30 (−13% additional). |
| Financial | Original: $3.7M/year electricity. After program: $2.3M/year (−$1.4M annually). Investment: $750K. Payback: 6.4 months. 5-year ROI: 833%. |

### 9.2 Case Study: Hyperscale AI Data Center — Liquid Cooling Mandate

| Aspect | Detail |
|--------|--------|
| Facility | New AI training cluster; 50 MW IT capacity; NVIDIA H100/H200 GPUs; 70 kW per rack average (some racks 120 kW) |
| Challenge | At 70-120 kW/rack, air cooling is physically impossible (air cannot remove heat fast enough even with unlimited airflow); traditional PUE calculations shift (cooling overhead is minimal because liquid cooling is extremely efficient but IT power is enormous) |
| Solution | **Direct liquid cooling (DLC)**: Warm water (35-45°C supply) directly to GPU cold plates. Liquid removes 80% of heat; remaining 20% (memory, VRMs, drives) handled by in-rack fans. **Cooling rejection**: Water returns at 50-60°C — warm enough to reject to ambient using dry coolers (no chiller needed for 9+ months/year in moderate climate). Only extreme summer days require backup chiller. **PUE achieved**: 1.05 (essentially zero cooling overhead because IT waste heat is at useful temperature). **WUE**: 0.1 L/kWh (closed loop; minimal makeup water). **Waste heat reuse**: 25 MW of 55°C water routed to adjacent office campus district heating system; reduces gas consumption. |
| Key metrics | PUE: 1.05; WUE: 0.1 L/kWh; ERE: 0.80 (significant heat reuse); CUE: 0.08 kg/kWh (renewable grid + reuse credit); Total facility power: 52.5 MW for 50 MW IT (only 2.5 MW overhead). |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **PUE approaching physical limits** | 2024+ | Hyperscalers at 1.03-1.08; further gains require eliminating ALL conversions; diminishing returns |
| **Liquid cooling becoming standard** | 2024-2027 | AI workloads mandate liquid; traditional air cooling cannot handle >30-40 kW/rack; all new AI DCs use DLC or immersion |
| **Waste heat reuse** | 2024-2030 | EU pushing district heating from DC waste heat; 45-60°C water from liquid-cooled DCs is directly usable; ERE becomes key metric |
| **WUE gains importance** | 2024-2027 | Water scarcity concerns; regulations limiting water-heavy cooling (evaporative); dry/liquid cooling preference |
| **EU regulation expansion** | 2025-2028 | From reporting to mandatory PUE targets; potential carbon taxation; sustainability requirements for new DC permits |
| **Holistic efficiency metrics** | 2025-2028 | PUE alone insufficient: must consider water, carbon, utilization, workload efficiency; "useful work per total resource" |
| **AI-specific efficiency** | 2024-2027 | Measuring GPU utilization alongside PUE; idle GPUs are the biggest waste (not cooling); MFU (Model FLOPS Utilization) |
| **Immersion cooling maturation** | 2025-2028 | From niche to mainstream for HPC/AI; single-phase immersion for edge; two-phase for extreme density |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is PUE? If a data center has a PUE of 1.8, what does that mean in practical terms?  
**A:** PUE (Power Usage Effectiveness) = Total Facility Power ÷ IT Equipment Power. It measures how efficiently a data center delivers power to IT equipment (the "useful work"). A PUE of 1.8 means: for every 1.0 watts consumed by IT equipment, the TOTAL facility consumes 1.8 watts. Therefore, 0.8 watts (44% of total) goes to NON-IT overhead: cooling, power distribution losses, lighting, etc. In practical terms: if you have a 1 MW IT load with PUE 1.8, you're paying for 1.8 MW at the utility meter — 800 kW is "wasted" on overhead. If the same facility achieved PUE 1.2, you'd only need 1.2 MW total — saving 600 kW continuously. At $0.10/kWh, that's $525K/year in savings. A PUE of 1.8 is considered poor by modern standards (hyperscalers achieve 1.08-1.20). The main contributor to high PUE is usually inefficient cooling (compressor-based chillers; over-cooling; poor airflow management).

### Tier 2: Mid-Level

**Q2:** Design a cooling strategy for a new 10 MW data center in a temperate climate (London). Achieve PUE <1.2 while maintaining ASHRAE A2 compliance and minimizing water consumption.  
**A:** [Detailed answer covering: climate analysis (London: avg temp 11°C; max ~35°C; high humidity; 8+ months below 20°C); strategy: water-side free cooling with economizer as primary (80% of year); high-efficiency centrifugal chiller as backup for peak summer; ASHRAE A2 (max 35°C inlet) allows free cooling when ambient <~28°C with approach temperature; closed-loop system with dry coolers preferred over open cooling towers (WUE → 0); adiabatic assist on dry coolers for peak days only (minimal water use); variable speed everything (pumps, fans, chillers); hot aisle containment; 25°C supply air temperature; PUE projection: ~1.12 annual average; WUE projection: <0.3 L/kWh (adiabatic only during heat waves).]

### Tier 3: Senior

**Q3:** The industry is building 100+ MW AI data centers with 100 kW/rack density. Argue that PUE is becoming an inadequate metric for modern data centers and propose a better framework for measuring "true efficiency."  
**A:** [Comprehensive answer covering: PUE limitations (designed for air-cooled era where cooling was 30-40% of overhead; in liquid-cooled AI DCs with PUE 1.03-1.05, the 3-5% overhead is trivial — the REAL efficiency problem is GPU utilization: typical training clusters achieve 30-50% GPU utilization; idle GPU wastes more energy than cooling overhead); proposed framework: (1) Compute Efficiency Ratio (CER) = useful compute output / total energy; incorporates GPU/CPU utilization; (2) include WUE (water scarcity increasingly critical); (3) include CUE (carbon intensity matters regardless of PUE); (4) ERE (waste heat reuse should be credited); (5) Workload Efficiency = useful work / compute cycles (avoids measuring just "power to chip" when chip is idle); (6) Total Cost of Ownership per useful compute unit; real-world example: DC-A with PUE 1.03 but 30% GPU utilization vs. DC-B with PUE 1.10 but 80% GPU utilization — DC-B delivers MORE useful work per total watt despite worse PUE; conclusion: PUE remains relevant for facility efficiency but must be supplemented with workload metrics for holistic assessment).]

---

## Chapter 12 — Cheat Sheet & Quick Reference

### Efficiency Metrics Quick Reference

```
PUE = Total Facility Power / IT Equipment Power
  • Ideal: 1.0 (impossible); Best: 1.03-1.08; Good: 1.2-1.4; Average: 1.5-1.6; Poor: >1.8
  • Main contributor to high PUE: COOLING (30-40% of total in legacy DCs)

WUE = Annual Water (liters) / IT Energy (kWh)
  • Good: <1.0 L/kWh; Average: 1.5-2.5; High (evaporative): 3.0-5.0
  • Zero WUE: air-side free cooling; closed-loop liquid cooling; air-cooled chillers

CUE = Total CO₂ (kg) / IT Energy (kWh)
  • Depends on grid carbon intensity + renewables
  • Target: →0 (100% renewable or nuclear)

ERE = (Total Energy - Reused Energy) / IT Energy
  • Can be LESS than PUE (if waste heat is reused for heating)
  • Example: PUE 1.10 but ERE 0.85 (heat reuse to district heating)

ASHRAE CLASSES (supply air temperature):
  A1: 15-32°C (traditional; tightly controlled)
  A2: 10-35°C (most modern equipment; standard today)
  A3: 5-40°C (extended; enables significant free cooling)
  A4: 5-45°C (widest; purpose-built hardware; hyperscale)
```

### PUE Improvement Quick Wins

```
IMMEDIATE (weeks; low cost):
  □ Raise supply temperature to 24-27°C (ASHRAE A2)
  □ Install blanking panels (empty U spaces)
  □ Seal cable cutouts in raised floor
  □ Turn off unnecessary humidification
  □ Decommission zombie servers (unused but powered)
  → Typical improvement: 0.1-0.3 PUE points

SHORT-TERM (months; medium cost):
  □ Hot aisle or cold aisle containment
  □ Variable speed drives on CRAH fans
  □ Optimize chiller setpoints (raise CHW temp)
  □ Right-size cooling (turn off excess units)
  → Typical improvement: 0.1-0.2 PUE points

MEDIUM-TERM (6-12 months; significant investment):
  □ Add water-side economizer
  □ Upgrade to high-efficiency UPS (>96%)
  □ In-row cooling (replace perimeter CRAHs)
  □ Rear-door heat exchangers for high-density racks
  → Typical improvement: 0.1-0.3 PUE points

LONG-TERM (new construction):
  □ Liquid cooling (DLC) for high-density
  □ Free cooling architecture (climate selection)
  □ Waste heat reuse infrastructure
  □ DC power distribution (eliminate conversions)
  → Achievable: PUE 1.03-1.15
```

---

*End of Document — 02_Data_Center_Energy_PUE_WUE.md*
