# GHG Protocol — Scope 1, 2, 3 Emissions Accounting

**Topic:** Greenhouse Gas (GHG) Protocol Corporate Standard — methodology for measuring and reporting corporate GHG emissions across Scope 1, 2, and 3; foundation for climate disclosure and target-setting  
**Standard:** GHG Protocol Corporate Accounting and Reporting Standard (Revised Edition); GHG Protocol Scope 2 Guidance (2015); GHG Protocol Corporate Value Chain (Scope 3) Standard  
**SDO:** World Resources Institute (WRI) + World Business Council for Sustainable Development (WBCSD); partnership with UNFCCC, EPA, ISO  
**Audience:** Sustainability managers, carbon accountants, ESG reporting professionals, environmental engineers, climate strategy managers, supply chain sustainability teams  
**Prerequisites:** Basic climate science (greenhouse effect; GHGs); understanding of corporate accounting; supply chain concepts; environmental management fundamentals

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 1992 | UNFCCC (UN Framework Convention on Climate Change) | International framework for addressing climate change |
| 1997 | Kyoto Protocol adopted | First binding GHG reduction targets for developed countries; created carbon markets |
| 1998 | **GHG Protocol Initiative launched** (WRI + WBCSD partnership) | First multi-stakeholder effort to create standardized corporate GHG accounting |
| 2001 | **GHG Protocol Corporate Standard** (1st edition) published | First comprehensive corporate-level GHG accounting framework; immediate global adoption |
| 2004 | **GHG Protocol Corporate Standard** (Revised Edition) published | Current edition; improved guidance; added Scope 2 dual reporting approach |
| 2005 | ISO 14064-1 published (based on GHG Protocol) | ISO standardization of GHG accounting; GHG Protocol recognized as equivalent |
| 2006 | Stern Review on Economics of Climate Change | Established economic case for climate action; increased corporate GHG reporting demand |
| 2011 | **GHG Protocol Scope 3 Standard** published | Comprehensive framework for value chain (Scope 3) emissions; 15 categories defined |
| 2011 | GHG Protocol Product Life Cycle Standard published | Product-level carbon footprint methodology |
| 2013 | GHG Protocol Policy and Action Standard | GHG impact of policies/actions |
| 2015 | **GHG Protocol Scope 2 Guidance** published | Dual reporting (location-based + market-based); resolved renewable energy accounting |
| 2015 | Paris Agreement adopted | 1.5°C/2°C targets; NDCs; accelerated corporate climate action and disclosure |
| 2017 | TCFD Recommendations published | Climate risk disclosure framework; references GHG Protocol for Scope 1/2/3 reporting |
| 2019 | SBTi (Science Based Targets initiative) gains momentum | Uses GHG Protocol as foundation for science-based corporate targets |
| 2022 | SEC proposed Climate Disclosure Rule (US) | References GHG Protocol methodology; mandatory Scope 1/2 and material Scope 3 |
| 2023 | EU CSRD/ESRS enters force | European Sustainability Reporting Standards; GHG Protocol referenced as methodology |
| 2023 | ISSB IFRS S2 (Climate Disclosure) finalized | Global climate disclosure standard; GHG Protocol required methodology |
| 2024 | GHG Protocol begins revision/update process | Land sector/removals guidance; Scope 2 update; Scope 3 improvements; market-based methods |
| 2025 | Multiple jurisdictions mandating GHG reporting | EU CSRD, US SEC (finalized), ISSB adoption globally, California SB 253 |

### 1.2 The Three Scopes

```mermaid
graph TB
    subgraph "Scope 1: DIRECT Emissions"
        S1[Company-Owned/Controlled Sources<br/>━━━━━━━━━━━<br/>• Stationary combustion (boilers, generators)<br/>• Mobile combustion (company vehicles)<br/>• Process emissions (chemical/physical)<br/>• Fugitive emissions (refrigerants, SF₆)]
    end
    
    subgraph "Scope 2: INDIRECT — Purchased Energy"
        S2[Purchased Electricity, Heat, Steam, Cooling<br/>━━━━━━━━━━━<br/>• Electricity consumption × grid factor<br/>  (location-based) OR<br/>• Contractual instruments/RECs<br/>  (market-based)<br/>• Purchased steam/heat/cooling]
    end
    
    subgraph "Scope 3: INDIRECT — Value Chain"
        S3_UP[UPSTREAM (purchased goods/services;<br/>capital goods; fuel/energy; transport;<br/>waste; business travel; commuting;<br/>leased assets)]
        S3_DOWN[DOWNSTREAM (transport/distribution;<br/>processing of sold products; use of<br/>sold products; end-of-life; leased assets;<br/>franchises; investments)]
    end
    
    COMPANY[Company Operations<br/>━━━━━━━━━━━<br/>Manufacturing; offices;<br/>vehicles; facilities]
    
    S1 --> COMPANY
    S2 --> COMPANY
    S3_UP --> COMPANY
    COMPANY --> S3_DOWN
```

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 GHG Protocol Standards Family

| Standard | Published | Scope | Key Use |
|----------|:---------:|-------|---------|
| **Corporate Accounting & Reporting Standard** (Revised) | 2004 | Scope 1 + 2 corporate-level | Foundation for corporate GHG inventories |
| **Scope 2 Guidance** | 2015 | Scope 2 methodology | Location-based vs. market-based dual reporting; RE accounting |
| **Corporate Value Chain (Scope 3) Standard** | 2011 | All 15 Scope 3 categories | Comprehensive value chain emissions accounting |
| **Product Life Cycle Standard** | 2011 | Product-level carbon footprint | Cradle-to-gate or cradle-to-grave product GHG |
| **GHG Protocol for Cities** | 2014 | City-level GHG inventory | Urban GHG accounting framework |
| **Policy and Action Standard** | 2014 | Policy/project GHG impact | Evaluate emission effects of policies/actions |
| **Land Sector and Removals Guidance** (draft) | 2024 | Carbon removals; land use | Accounting for carbon removals; biogenic carbon |

### 2.2 Five Principles of GHG Accounting

| Principle | Description | Application |
|-----------|-------------|-------------|
| **Relevance** | Ensure GHG inventory appropriately reflects emissions; serves decision-making needs | Scope/boundary definition; materiality consideration |
| **Completeness** | Account for and report all GHG emission sources within chosen boundary | Disclose/justify any exclusions; cover all scopes |
| **Consistency** | Use consistent methodologies to allow meaningful comparisons over time | Maintain approaches year-to-year; document changes; recalculate base year if structural changes |
| **Transparency** | Address all relevant issues clearly, factually, and coherently | Document assumptions, methodologies, data sources; enable verification |
| **Accuracy** | Reduce bias and uncertainties as far as practicable | Use best available data; quantify uncertainty where possible; improve data quality over time |

### 2.3 Organizational Boundaries

| Approach | Definition | When to Use |
|----------|-----------|-------------|
| **Equity share** | Account for GHG emissions proportional to ownership share in operations | Complex ownership structures; joint ventures; partial ownership; aligns with financial reporting |
| **Financial control** | Account for 100% of emissions from operations over which financial control exists | Organization can direct financial and operating policies; aligns with financial consolidation |
| **Operational control** | Account for 100% of emissions from operations over which operational control exists | Organization has authority to introduce operating policies; **most commonly used**; practical |

### 2.4 The Seven GHGs (Kyoto Protocol)

| GHG | Formula | GWP (100-year, AR5) | Primary Source | Common in Electronics? |
|-----|:-------:|:---:|----------------|:---:|
| Carbon dioxide | CO₂ | 1 | Fossil fuel combustion; industrial processes | Yes (energy) |
| Methane | CH₄ | 28 | Natural gas; waste decomposition; agriculture | Yes (natural gas) |
| Nitrous oxide | N₂O | 265 | Combustion; agriculture; industrial processes | Minor |
| Hydrofluorocarbons | HFCs | 12-14,800 | Refrigeration/air conditioning; aerosols | Yes (HVAC refrigerants) |
| Perfluorocarbons | PFCs | 7,390-12,200 | Semiconductor manufacturing; aluminum production | **Yes** (semiconductor etch) |
| Sulphur hexafluoride | SF₆ | 23,500 | Electrical switchgear insulation; semiconductor | **Yes** (switchgear; semi) |
| Nitrogen trifluoride | NF₃ | 17,200 | Semiconductor manufacturing (chamber cleaning) | **Yes** (semiconductor) |

---

## Chapter 3 — Technical Deep Dive

### 3.1 Scope 1 — Direct Emissions

| Source Category | Examples (Electronics Manufacturing) | Calculation Method |
|----------------|--------------------------------------|-------------------|
| **Stationary combustion** | Natural gas boilers (heating); diesel generators (backup power); gas-fired processes | Fuel consumption × emission factor (kg CO₂e per unit fuel) |
| **Mobile combustion** | Company-owned vehicles (fleet cars, delivery trucks, forklifts with combustion engines) | Fuel consumption × emission factor per fuel type; OR distance × emission factor per vehicle type |
| **Process emissions** | Chemical reactions in manufacturing (e.g., semiconductor etch processes releasing PFCs; soldering producing CO₂ from flux decomposition) | Process-specific: measured emissions OR mass balance OR equipment-specific emission factors |
| **Fugitive emissions** | Refrigerant leaks (HFC-134a, R-410A from HVAC/chillers); SF₆ leaks (switchgear); fire suppression system leaks | Equipment capacity × annual leak rate × GWP; OR refill amount × GWP |

**Calculation formula:**

$$\text{Scope 1 emissions} = \sum_{i} (AD_i \times EF_i \times GWP_i)$$

Where:
- $AD_i$ = Activity Data (quantity of fuel, refrigerant, etc.)
- $EF_i$ = Emission Factor (kg GHG per unit activity)
- $GWP_i$ = Global Warming Potential (converts to CO₂ equivalent)

### 3.2 Scope 2 — Purchased Energy

| Method | Calculation | When to Use | Data Source |
|--------|-------------|-------------|-------------|
| **Location-based** | Electricity (kWh) × grid average emission factor (kgCO₂e/kWh) for location | Always required; reflects physical grid reality | National/regional grid emission factors (IEA; EPA eGRID; EU registry) |
| **Market-based** | Electricity (kWh) × emission factor from contractual instrument | Always required alongside location-based; reflects contractual choices | Energy Attribute Certificates (EACs): Guarantees of Origin (EU); RECs (US); I-RECs (international); supplier-specific factor; residual mix |

**Location-based example (electronics factory in Germany):**

$$\text{Scope 2}_{loc} = 10{,}000{,}000 \text{ kWh} \times 0.380 \text{ kgCO₂e/kWh} = 3{,}800 \text{ tCO₂e}$$

**Market-based example (same factory, 50% renewable energy contract with GOs):**

$$\text{Scope 2}_{mkt} = 5{,}000{,}000 \text{ kWh} \times 0 + 5{,}000{,}000 \text{ kWh} \times 0.459 \text{ (residual mix)} = 2{,}295 \text{ tCO₂e}$$

### 3.3 Scope 3 — Value Chain (15 Categories)

| Cat | Category | Direction | Description | Typical % for Electronics Mfg |
|:---:|----------|:---------:|-------------|:---:|
| 1 | Purchased goods and services | Upstream | GHG from production of purchased materials/components/services | **40-60%** (largest for most) |
| 2 | Capital goods | Upstream | GHG from production of purchased capital equipment | 2-5% |
| 3 | Fuel- and energy-related activities | Upstream | Upstream emissions of purchased fuels/electricity (not in Scope 1/2) | 3-8% |
| 4 | Upstream transportation and distribution | Upstream | Transportation of purchased goods (paid by reporting company) | 3-8% |
| 5 | Waste generated in operations | Upstream | Disposal/treatment of waste from operations | 1-3% |
| 6 | Business travel | Upstream | Employee air travel, hotels, car rental | 1-3% |
| 7 | Employee commuting | Upstream | Employees traveling to/from work | 1-3% |
| 8 | Upstream leased assets | Upstream | Emissions from leased assets (not in Scope 1/2) | 0-2% |
| 9 | Downstream transportation and distribution | Downstream | Transportation of sold products to customers (not paid by reporting company) | 2-5% |
| 10 | Processing of sold products | Downstream | Emissions from processing sold intermediate products by downstream companies | 0-5% (component manufacturers) |
| 11 | Use of sold products | Downstream | Emissions from customer use of sold products (energy during lifetime) | **10-40%** (if products consume energy) |
| 12 | End-of-life treatment of sold products | Downstream | Disposal/recycling of sold products at end of life | 1-3% |
| 13 | Downstream leased assets | Downstream | Emissions from assets owned by company but leased to others | 0-2% |
| 14 | Franchises | Downstream | Emissions from franchise operations | 0% (rarely applicable) |
| 15 | Investments | Downstream | Emissions from investments (equity/debt) | 0% (financial institutions primarily) |

### 3.4 Scope 3 Calculation Methods

| Method | Data Quality | Effort | When to Use |
|--------|:---:|:---:|-------------|
| **Supplier-specific** | Highest | High | Primary data from suppliers (actual emissions per product/service supplied); preferred for key suppliers |
| **Hybrid** | High | Medium-High | Combine supplier data with secondary data for gaps |
| **Average-data** | Medium | Medium | Industry-average emission factors per unit of activity (e.g., kgCO₂e per kg material purchased; per $ spent) |
| **Spend-based** | Lowest | Low | Procurement spend ($) × EEIO emission factor (kgCO₂e per $ in sector); useful for initial screening |

---

## Chapter 4 — Implementation Guide

### 4.1 GHG Inventory Implementation Roadmap

| Phase | Duration | Activities | Output |
|:-----:|:--------:|-----------|--------|
| **1. Scope & boundaries** | 2-4 weeks | Define organizational boundary (operational control recommended); set operational boundary (Scope 1, 2, 3); determine base year; identify reporting requirements | Boundary documentation; base year policy; reporting requirements |
| **2. Source identification** | 2-4 weeks | Identify all emission sources per scope; map activities to GHG categories; determine relevant Scope 3 categories (screening) | Source inventory; Scope 3 relevance assessment |
| **3. Data collection system** | 4-8 weeks | Design data collection process; identify data owners; set up templates/tools; engage suppliers (Scope 3); establish QA/QC procedures | Data collection procedures; templates; tools; responsibilities |
| **4. Calculate emissions** | 4-6 weeks | Collect activity data; apply emission factors; calculate Scope 1, 2, 3; validate results; uncertainty assessment | GHG inventory (tCO₂e by scope, category, source) |
| **5. Verify** | 2-4 weeks | Internal review; external verification (optional but recommended for disclosure); ISO 14064-3 verification | Verification statement; corrective actions |
| **6. Report & disclose** | 2-4 weeks | Prepare disclosure (CDP, CSRD/ESRS, SEC, annual report); communicate results; set targets (SBTi) | GHG report; disclosure submissions; improvement targets |
| **7. Improve** | Ongoing | Improve data quality (supplier-specific data; metering); expand Scope 3 coverage; reduce emissions; track progress | Year-over-year improvement; target tracking |

### 4.2 Emission Factors — Common Sources

| Source | Coverage | Access | Best For |
|--------|----------|--------|----------|
| **IEA Emission Factors** | Global electricity grid factors by country | Subscription (annual) | Scope 2 location-based (international) |
| **EPA eGRID** | US electricity grid factors by sub-region | Free | Scope 2 US operations |
| **EU EEA** | European grid factors by country/year | Free | Scope 2 EU operations |
| **DEFRA/BEIS (UK)** | Comprehensive: fuels, transport, materials, waste, water | Free | Scope 1 (fuels), Scope 3 (various) — widely used globally |
| **ecoinvent** | Life cycle inventory database (thousands of processes) | Subscription | Scope 3 (materials, processes; high quality) |
| **IPCC Guidelines** | Default emission factors for national inventories | Free | Reference; methodology |
| **Supplier-specific** | Actual emissions from your supply chain | Requested from suppliers | Scope 3 Category 1 (best quality) |
| **EEIO databases** (Exiobase, USEEIO) | Spend-based factors (kgCO₂e per $ by sector) | Free/academic | Scope 3 screening; spend-based initial estimates |

### 4.3 Electronics Manufacturing — Typical GHG Profile

| Source | Scope | Typical Contribution | Key Data Needed |
|--------|:-----:|:---:|-----------------|
| Electricity (manufacturing + offices) | Scope 2 | 15-25% of total | kWh consumption; grid factor or contractual factor |
| Natural gas (heating/processes) | Scope 1 | 2-5% | m³ or therms consumed |
| Refrigerants (HVAC leaks) | Scope 1 | 1-3% | Type and quantity refilled annually |
| Company vehicles | Scope 1 | 1-3% | Fuel consumption or distance by vehicle type |
| **Purchased goods & services** (components, materials, PCBs, ICs) | **Scope 3 Cat 1** | **40-60%** | Spend or mass by material type × emission factor |
| Upstream transport (inbound logistics) | Scope 3 Cat 4 | 3-8% | Weight × distance × mode; or spend |
| Use of sold products (energy in operation) | Scope 3 Cat 11 | 10-30% | Product power consumption × lifetime hours × grid factor |
| Business travel | Scope 3 Cat 6 | 1-3% | Distance by mode (air class; car) |
| Waste treatment | Scope 3 Cat 5 | 1-2% | Waste mass by type × treatment method factor |
| Employee commuting | Scope 3 Cat 7 | 1-2% | Employees × distance × mode × working days |
| **Total typical** | All | **100%** | Scope 3 typically 70-85% of total footprint |

---

## Chapter 5 — Verification & Assurance

### 5.1 GHG Verification Levels

| Level | Description | Standard | When Required |
|:-----:|-------------|----------|---------------|
| **No assurance** | Self-reported; no external review | — | Internal reporting only; early-stage programs |
| **Limited assurance** | Plausibility check; inquiries and analytical procedures; negative statement ("nothing came to our attention...") | ISO 14064-3; ISAE 3410 | CDP disclosure; first-time verification; most common |
| **Reasonable assurance** | Detailed testing; substantive procedures; positive statement ("in our opinion, the data is fairly stated...") | ISO 14064-3; ISAE 3410 | Regulated disclosures (EU CSRD after transition); ETS compliance; highest credibility |

### 5.2 Common Verification Findings

| Finding | Category | Impact | Prevention |
|---------|:--------:|--------|-----------|
| Incomplete Scope 3 boundary (categories excluded without justification) | Completeness | Understated total footprint | Screen all 15 categories; document exclusion rationale with quantitative justification |
| Incorrect grid emission factor (outdated year; wrong region) | Accuracy | Misstated Scope 2 | Use most recent available factors; document source and year; match to actual grid region |
| Refrigerant leaks not tracked | Completeness | Missing Scope 1 emissions | HVAC maintenance log; annual refrigerant purchase records; calculate from recharge quantities |
| Spend-based Scope 3 without supplier engagement plan | Data quality | Low-quality Scope 3 estimate | Develop supplier-specific data collection for top contributors; improve over time |
| Base year not recalculated after acquisition/divestiture | Consistency | Non-comparable trends | Define base year recalculation policy; trigger recalculation for structural changes >5% |
| Double counting between Scope 2 and Scope 3 Cat 3 | Accuracy | Overstated total | Category 3 covers upstream emissions of purchased energy (T&D losses; upstream extraction); must not include Scope 2 emissions |

---

## Chapter 6 — Regional Context

### 6.1 GHG Reporting Requirements by Jurisdiction

| Jurisdiction | Regulation | Mandatory Scopes | Who Must Report | GHG Protocol Referenced? |
|:---:|-----------|:---:|---|:---:|
| **EU** | CSRD/ESRS E1 (Climate Change) | Scope 1, 2, 3 (material categories) | Large companies + listed SMEs (~50,000 companies) | Yes (required methodology) |
| **US (Federal)** | SEC Climate Disclosure Rule (finalized 2024) | Scope 1, 2 (with materiality); Scope 3 Safe Harbor (delayed/challenged) | SEC-registered public companies | Yes (referenced) |
| **US (California)** | SB 253 (Climate Corporate Data Accountability Act) | Scope 1, 2, 3 | Companies >$1B revenue doing business in CA | Yes (required methodology) |
| **UK** | SECR (Streamlined Energy & Carbon Reporting) | Scope 1, 2 (+ significant Scope 3 voluntary) | Quoted companies; large unquoted companies | Yes (referenced) |
| **Japan** | GHG Reporting under Act on Rational Use of Energy | Scope 1, 2 (large emitters) | Companies >3,000 tCO₂e/year | Japan-specific but aligned |
| **China** | National ETS reporting; expanding requirements | Scope 1, 2 (power sector first; expanding) | Power generators first; expanding to other sectors | China GHG Protocol (based on international) |
| **Global (voluntary)** | CDP | Scope 1, 2, 3 | Any company (investor/customer-requested) | Yes (required methodology) |
| **Global** | ISSB IFRS S2 | Scope 1, 2, 3 (all material) | Being adopted by jurisdictions globally | Yes (required methodology) |

### 6.2 Science Based Targets (SBTi) and GHG Protocol

| Aspect | Detail |
|--------|--------|
| **What is SBTi** | Initiative that validates corporate GHG reduction targets as consistent with Paris Agreement (1.5°C/well-below 2°C) |
| **Foundation** | SBTi targets are set on GHG Protocol inventory (Scope 1, 2, 3 baseline) |
| **Near-term targets** | 5-10 year targets: typically 42-50% reduction in Scope 1+2; Scope 3 (if >40% of total): target required (25%+ reduction or 67% supplier engagement) |
| **Net-zero targets** | Long-term (by 2050 latest): 90%+ reduction across all scopes; residual emissions neutralized by removals |
| **Scope 3 requirement** | If Scope 3 is ≥40% of total Scope 1+2+3 (which it almost always is for electronics/manufacturing): Scope 3 target required |
| **Relevance** | >4,000 companies with validated targets; many customers/investors require SBTi-validated targets; GHG Protocol inventory is the non-negotiable foundation |

---

## Chapter 7 — Comparison

### 7.1 GHG Protocol vs. ISO 14064

| Dimension | GHG Protocol Corporate Standard | ISO 14064-1:2018 |
|-----------|:---:|:---:|
| **Nature** | Voluntary standard (framework) | International standard (certifiable) |
| **Publisher** | WRI/WBCSD | ISO |
| **Structure** | Scopes 1, 2, 3 | Categories: direct; indirect energy; transportation; products used; products from org; other |
| **Scope 2** | Dual reporting (location + market) mandatory | Single approach; mentions both |
| **Scope 3** | 15 defined categories (separate standard) | 4 indirect emission categories (less granular) |
| **Cost** | Free (publicly available) | Purchased standard |
| **Adoption** | De-facto global standard; referenced by all major frameworks (CDP, TCFD, SBTi, CSRD, SEC) | Used for formal verification; national programs; ETS |
| **Verification** | Guidance provided; ISO 14064-3 referenced | ISO 14064-3 (Verification standard) paired with Part 1 |
| **Compatibility** | ISO 14064-1 designed to be compatible with GHG Protocol | GHG Protocol recognized as conforming foundation |
| **Recommendation** | Use GHG Protocol for inventory and disclosure; reference ISO 14064-1 for verification framework | |

### 7.2 Scope 2 Methods Comparison

| Aspect | Location-Based | Market-Based |
|--------|:---:|:---:|
| **What it reflects** | Physical grid average emissions (where you consume electricity) | Contractual instruments (what you've purchased/claimed) |
| **Calculation** | kWh × grid average emission factor | kWh × instrument-specific factor (0 for renewables with certificates) |
| **Purpose** | Grid reality; physical impact; comparability | Market signals; investment in renewables; incentivize clean procurement |
| **Renewable energy** | Has NO effect (grid average unchanged by your purchase) | Full effect (claim zero emissions for certified RE) |
| **Required by GHG Protocol** | Yes (must always report) | Yes (must always report alongside location-based) |
| **Used by SBTi** | Target can use either (depends on method chosen) | Preferred for RE accounting (demonstrates actual procurement action) |
| **Example**: 10 GWh in Germany, 100% RE contract with GOs | 10,000 MWh × 0.380 = **3,800 tCO₂e** | 10,000 MWh × 0 = **0 tCO₂e** |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 GHG Inventory Process Flow

```mermaid
flowchart TB
    subgraph "1. Define Boundaries"
        ORG[Organizational Boundary<br/>━━━━━━━━━━━<br/>• Operational control<br/>  (most common)<br/>• Financial control<br/>• Equity share]
        
        OPER[Operational Boundary<br/>━━━━━━━━━━━<br/>• Scope 1: direct<br/>• Scope 2: purchased energy<br/>• Scope 3: value chain<br/>  (15 categories)]
    end
    
    subgraph "2. Collect Data"
        S1_DATA[Scope 1 Data<br/>━━━━━━━━━━━<br/>• Fuel invoices (natural gas, diesel)<br/>• Vehicle fuel records<br/>• Refrigerant recharge logs<br/>• Process emissions data]
        
        S2_DATA[Scope 2 Data<br/>━━━━━━━━━━━<br/>• Electricity bills (kWh)<br/>• Steam/heat invoices<br/>• Grid emission factors<br/>• RE certificates/contracts]
        
        S3_DATA[Scope 3 Data<br/>━━━━━━━━━━━<br/>• Procurement spend / quantities<br/>• Supplier-specific data<br/>• Travel records<br/>• Waste manifests<br/>• Product energy specs<br/>• Logistics data]
    end
    
    subgraph "3. Calculate"
        CALC[Apply Emission Factors<br/>━━━━━━━━━━━<br/>Activity Data × Emission Factor<br/>= Emissions (tCO₂e)<br/><br/>Convert all GHGs to CO₂e<br/>using GWP values]
    end
    
    subgraph "4. Report & Act"
        REPORT_OUT[Report<br/>━━━━━━━━━━━<br/>• CDP submission<br/>• CSRD/ESRS disclosure<br/>• Annual sustainability report<br/>• SEC filing<br/>• SBTi target tracking]
        
        TARGET[Set & Track Targets<br/>━━━━━━━━━━━<br/>• SBTi near-term & net-zero<br/>• Reduction roadmap<br/>• Year-over-year progress<br/>• Intensity metrics]
    end
    
    ORG --> OPER
    OPER --> S1_DATA & S2_DATA & S3_DATA
    S1_DATA & S2_DATA & S3_DATA --> CALC
    CALC --> REPORT_OUT & TARGET
```

### 8.2 Scope 3 Category Mapping for Electronics

```mermaid
graph LR
    subgraph "UPSTREAM (Categories 1-8)"
        C1[Cat 1: Purchased Goods<br/>━━━━━━━━━━━<br/>• ICs, PCBs, passives<br/>• Raw materials (metals)<br/>• Packaging materials<br/>• Services (contract mfg)<br/>▓▓▓▓▓▓▓▓▓▓ 50%]
        
        C2[Cat 2: Capital Goods<br/>━━━━━━━━━━━<br/>• SMT lines, test equipment<br/>• Buildings, infrastructure<br/>░░ 3%]
        
        C3[Cat 3: Fuel & Energy<br/>━━━━━━━━━━━<br/>• T&D losses<br/>• Upstream fuel production<br/>░░░ 5%]
        
        C4[Cat 4: Upstream Transport<br/>━━━━━━━━━━━<br/>• Inbound component logistics<br/>• Air freight (urgent)<br/>░░░ 5%]
        
        C567[Cats 5-7: Waste, Travel,<br/>Commuting<br/>░░ 4%]
    end
    
    subgraph "DOWNSTREAM (Categories 9-15)"
        C9[Cat 9: Downstream Transport<br/>━━━━━━━━━━━<br/>• Product delivery to customer<br/>░░ 3%]
        
        C11[Cat 11: Use of Sold Products<br/>━━━━━━━━━━━<br/>• Product power consumption<br/>  × lifetime × grid factor<br/>▓▓▓▓▓▓▓ 25%]
        
        C12[Cat 12: End-of-Life<br/>━━━━━━━━━━━<br/>• Product disposal/recycling<br/>░ 2%]
        
        OTHER[Cats 10,13-15<br/>░ 3%]
    end
```

---

## Chapter 9 — Case Studies

### 9.1 Case Study: Electronics Manufacturer GHG Inventory

| Aspect | Detail |
|--------|--------|
| Company | Electronics manufacturer; 5 sites globally; 4,000 employees; annual revenue €800M; products: industrial sensors, automotive electronics, communication equipment |
| Motivation | Customer requirements (automotive OEMs demanding Scope 3 data); CDP Supply Chain request; preparing SBTi target; upcoming CSRD reporting obligation |
| Base year | 2019 (pre-COVID; representative of normal operations) |
| **Scope 1** | Natural gas (heating): 2,400 tCO₂e; diesel generators (backup): 180 tCO₂e; company vehicles: 650 tCO₂e; refrigerant leaks (R-410A): 320 tCO₂e; **Total Scope 1: 3,550 tCO₂e** |
| **Scope 2 (location-based)** | Electricity: 45 GWh × weighted average 0.42 kgCO₂e/kWh = 18,900 tCO₂e; steam/heat: 500 tCO₂e; **Total Scope 2 (loc): 19,400 tCO₂e** |
| **Scope 2 (market-based)** | 30% renewable energy (GOs/RECs): 0 tCO₂e for 13.5 GWh; remaining 31.5 GWh × residual mix 0.48 = 15,120 tCO₂e; **Total Scope 2 (mkt): 15,620 tCO₂e** |
| **Scope 3** | Cat 1 (purchased goods): 85,000 tCO₂e (spend-based initial; improving to supplier-specific for top 50 suppliers); Cat 2 (capital): 5,200 tCO₂e; Cat 3 (fuel/energy upstream): 4,800 tCO₂e; Cat 4 (upstream transport): 8,500 tCO₂e; Cat 5 (waste): 1,200 tCO₂e; Cat 6 (travel): 2,800 tCO₂e; Cat 7 (commuting): 3,100 tCO₂e; Cat 9 (downstream transport): 4,200 tCO₂e; Cat 11 (use of products): 42,000 tCO₂e; Cat 12 (end-of-life): 2,500 tCO₂e; **Total Scope 3: 159,300 tCO₂e** |
| **Total footprint** | Scope 1: 3,550 (2%); Scope 2 (loc): 19,400 (11%); Scope 3: 159,300 (87%); **Total: 182,250 tCO₂e** |
| Key finding | Scope 3 is 87% of total footprint — dominated by purchased goods (47% of total) and use-of-product (23% of total). Reducing total footprint requires supply chain engagement + product energy efficiency. |
| SBTi target set | Near-term (2030): reduce Scope 1+2 by 50% (absolute) vs. 2019; reduce Scope 3 by 30% per unit revenue. Net-zero: 2045. |
| Reduction roadmap | Scope 1+2: 100% renewable electricity by 2027 (reduces Scope 2 market-based to ~0); electrify heating; EV fleet. Scope 3: supplier engagement (top 50 suppliers set own SBTi targets); product design for lower power consumption; increase recycled content. |

### 9.2 Case Study: Semiconductor Company Scope 1 PFC Management

| Aspect | Detail |
|--------|--------|
| Company | Semiconductor fab (logic); advanced node manufacturing; Scope 1 PFC emissions from plasma etch and CVD chamber cleaning |
| Problem | PFC/HFC emissions (CF₄, C₂F₆, C₃F₈, NF₃, SF₆) have extremely high GWP (7,000-23,500); even small volumes create massive CO₂e; industry-wide problem |
| Emissions profile | Total Scope 1 PFCs: 180,000 tCO₂e (before abatement); represents 60% of total Scope 1; 12% of total Scope 1+2; small absolute quantity of gas but enormous CO₂e due to GWP |
| Mitigation | (1) **Process optimization**: reduce gas flow rates; optimize etch recipes to use less PFC without affecting quality (10-15% reduction achievable). (2) **Gas substitution**: replace high-GWP gases where possible (C₃F₈ → C₄F₈ in some etch steps; reduce CF₄ use). (3) **Point-of-use abatement**: install combustion/plasma abatement systems on etch and CVD tools; **>99% destruction efficiency** for most PFCs. (4) **Centralized scrubbing**: additional centralized treatment of fab exhaust before atmospheric release. |
| Results | PFC destruction efficiency: 95-99%+ (point-of-use abatement); effective Scope 1 PFC reduction: from 180,000 to 18,000 tCO₂e (90% reduction); capital investment: $25M for abatement systems across fab; operational cost: ~$3M/year (consumables + energy). |
| Industry context | WSC (World Semiconductor Council) voluntary PFC reduction agreement: 10% absolute reduction from 1995 baseline by 2010 (achieved); continued improvement; SEMI S23 Safety Guideline for PFC abatement. Industry-wide PFC emissions declining per unit area of silicon processed despite production growth. |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **Mandatory Scope 3 reporting** | Now-2026 | EU CSRD (2024+), California SB 253 (2026), ISSB S2 adoption globally; Scope 3 no longer optional |
| **GHG Protocol updates** | 2024-2026 | Land sector/removals guidance; Scope 2 update (market-based methodology); Scope 3 calculation improvements |
| **Supplier-specific data** | Now | Shift from spend-based to primary data for Scope 3 Cat 1; platforms (CDP, Catena-X, PACT) enabling product-level carbon data exchange |
| **Product Carbon Footprints (PCF)** | Now-2026 | Demand for component-level CO₂e data (automotive CATENA-X; EU Battery Regulation; Digital Product Passport); PCF replaces spend-based Scope 3 |
| **AI in GHG accounting** | Now | Automated data collection; emission factor matching; anomaly detection; forecasting; real-time monitoring |
| **Carbon removal accounting** | 2024-2026 | GHG Protocol Land Sector Guidance; how to account for purchased carbon removal credits; integrity of offsets |
| **Scope 2 market-based evolution** | 2024-2025 | Temporal matching (24/7 clean energy vs. annual matching); additionality requirements; higher standards for market-based claims |
| **Digital MRV** | Now | Digital measurement, reporting, verification; IoT sensors; satellite monitoring; blockchain for carbon credits |
| **Financial integration** | Now-2025 | Carbon pricing internalization; shadow carbon price; TCFD → ISSB mandating connection between GHG data and financial risk |
| **Net-zero standard** | Now | SBTi Corporate Net-Zero Standard; ISO Net Zero Guidelines (IWA 42:2022); convergence on what "net-zero" means |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** Explain the difference between Scope 1, 2, and 3 emissions with examples from an electronics factory.  
**A:** **Scope 1 (Direct)**: GHG emissions from sources the company owns/controls. Electronics factory examples: natural gas burned in boilers for heating; diesel fuel in backup generators; refrigerant leaks from air conditioning systems (HFCs with high GWP); company-owned delivery vehicles. **Scope 2 (Indirect – Energy)**: GHG emissions from purchased electricity, steam, heat, or cooling that the company consumes but doesn't generate. Electronics factory: electricity powering SMT lines, reflow ovens, test equipment, lighting, HVAC — the power plant emits CO₂, but it's caused by the company's electricity demand. Reported two ways: location-based (grid average) and market-based (contractual instruments like renewable energy certificates). **Scope 3 (Indirect – Value Chain)**: All other indirect emissions in the company's value chain (upstream and downstream). Electronics factory: upstream = emissions from producing purchased components (ICs, PCBs, metals), shipping them to the factory, employee commuting; downstream = energy consumed by the product during its lifetime (e.g., a server running for 5 years), end-of-life disposal. Scope 3 is typically 70-90% of an electronics company's total footprint.

**Q2:** What is a CO₂ equivalent (CO₂e) and why is it used?  
**A:** CO₂ equivalent (CO₂e) is a metric that converts all greenhouse gases to a common unit based on their **Global Warming Potential (GWP)** — their relative warming impact compared to CO₂ over a given time period (usually 100 years). CO₂ has GWP = 1 (reference). Other GHGs are more potent: methane (CH₄) GWP = 28 (28× more warming per molecule than CO₂ over 100 years); SF₆ GWP = 23,500; HFC-134a GWP = 1,430. The conversion: tCO₂e = mass of gas × GWP. Example: 1 kg of SF₆ leaked from switchgear = 1 kg × 23,500 = 23.5 tCO₂e. We use CO₂e so we can: (1) aggregate different GHGs into a single total footprint number; (2) compare the climate impact of different emission sources; (3) set meaningful reduction targets across all GHGs. Without CO₂e, you'd have separate inventories for each gas and couldn't easily total or compare them.

### Tier 2: Mid-Level

**Q3:** Your company's Scope 3 Category 1 (purchased goods) is 60% of total footprint, calculated using spend-based method. How would you improve this calculation and reduce emissions?  
**A:** [Detailed answer covering: (1) **Improve data quality**: transition from spend-based (lowest quality: $ × EEIO factor) to average-data (mass × industry factor) for major material categories, then to supplier-specific for top contributors. Identify top 20 suppliers by Scope 3 contribution (likely 80% of Cat 1); request their product carbon footprints (PCFs) or company-level intensity data. (2) **Engagement**: join CDP Supply Chain or similar platform; request suppliers measure and disclose their own Scope 1+2; use their data directly for your Scope 3. (3) **Reduction strategies**: (a) Specify lower-carbon materials (recycled metals; bio-based plastics; low-carbon aluminum); (b) Supplier selection criteria include carbon intensity; (c) Request suppliers set SBTi targets (creates systemic reduction); (d) Product design for dematerialization (less material = less Scope 3); (e) Localize supply chain (reduces transport emissions in Cat 4 as well). (4) **Tools/platforms**: PACT (Partnership for Carbon Transparency) for product-level data exchange; Catena-X (automotive); industry LCA databases (ecoinvent) for better average factors.]

### Tier 3: Senior

**Q5:** Design a comprehensive corporate climate strategy for a €2B electronics company that includes GHG inventory, SBTi target-setting, reduction roadmap, and governance — considering CSRD, CDP, and customer requirements.  
**A:** [Comprehensive answer covering: (1) **Governance**: Board-level climate committee; C-suite accountability (CEO/CFO); dedicated sustainability team (5-10 FTEs); carbon budget integrated into business planning; internal carbon price ($100-200/tCO₂e for investment decisions). (2) **GHG inventory**: full Scope 1/2/3 per GHG Protocol; automated data collection system; ERP integration; supplier data platform; third-party verification (limited assurance → reasonable within 3 years). (3) **SBTi targets**: 1.5°C-aligned near-term (2030): Scope 1+2 absolute -50%; Scope 3 intensity -30%/unit revenue. Net-zero by 2045: -90% absolute all scopes; neutralize residual with high-quality removals. (4) **Reduction roadmap**: Scope 1+2: 100% renewable electricity (PPAs + GOs) by 2027; electrify heating (heat pumps); EV fleet by 2030; refrigerant management. Scope 3: supplier engagement (top 50 suppliers → SBTi targets; PCF data exchange); product design (30% energy reduction in use phase per product generation); recycled content targets; logistics optimization (nearshoring; mode shift). (5) **Reporting**: CSRD/ESRS E1 (double materiality; transition plan); CDP (aim A-list); SBTi progress reporting; TCFD/ISSB climate risk disclosure; automotive customer requirements (Catena-X PCF exchange). (6) **Investment**: estimated €20-50M over 5 years (renewables, efficiency, supplier programs, data systems, organizational capability); ROI: risk mitigation + cost savings (energy) + revenue enablement (customer requirements for low-carbon products).]

---

## Chapter 12 — Cheat Sheet & Quick Reference

### Scope Summary

```
SCOPE 1 — DIRECT (you burn/emit it)
  • Natural gas, diesel, gasoline (stationary + mobile combustion)
  • Refrigerant leaks (HFCs, R-410A, R-134a)
  • Process emissions (PFCs in semiconductor etch; SF₆ in switchgear)
  • Fugitive emissions (gas leaks)
  → CALCULATION: fuel volume × emission factor; refrigerant charge × leak rate × GWP

SCOPE 2 — INDIRECT ENERGY (you buy it)
  • Electricity (kWh)
  • Purchased steam, heat, cooling
  → TWO METHODS (both required):
    Location-based: kWh × grid average EF (physical reality)
    Market-based:   kWh × contractual instrument EF (market signal)
  → Renewable energy only reduces MARKET-BASED (not location-based)

SCOPE 3 — VALUE CHAIN (everything else)
  • 15 categories (8 upstream + 7 downstream)
  • Typically 70-90% of electronics company's total footprint
  • Category 1 (purchased goods) and Cat 11 (use of products) dominate
  → METHODS: spend-based (easy/low quality) → average-data → supplier-specific (best)
```

### Quick Calculations

```
SCOPE 1 (fuel):
  tCO₂e = Fuel volume (L or m³) × Emission Factor (kgCO₂e/unit) / 1000
  
  Natural gas: 1 MWh (thermal) = 0.205 tCO₂e (UK BEIS 2024)
  Diesel:      1 liter = 2.68 kgCO₂e
  Gasoline:    1 liter = 2.31 kgCO₂e

SCOPE 1 (refrigerant):
  tCO₂e = kg leaked (or refilled) × GWP
  R-410A: GWP = 2,088 → 1 kg leak = 2.088 tCO₂e
  R-134a: GWP = 1,430 → 1 kg leak = 1.430 tCO₂e

SCOPE 2 (electricity):
  tCO₂e = MWh × Grid Factor (tCO₂e/MWh)
  
  Grid factors (2024 approx):
  Germany:     0.380 tCO₂e/MWh (location-based)
  USA (avg):   0.370 tCO₂e/MWh
  UK:          0.207 tCO₂e/MWh
  France:      0.052 tCO₂e/MWh (nuclear dominant)
  China:       0.555 tCO₂e/MWh
  India:       0.720 tCO₂e/MWh

SCOPE 3 Cat 1 (spend-based):
  tCO₂e = Spend ($) × EEIO factor (tCO₂e/$M for sector)
  Electronics components: ~200-400 tCO₂e per $M purchased (varies by type)
```

### Reporting Obligations Summary

```
Framework     Scopes Required    Who                         Deadline (annual)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
CDP           1, 2, 3            Requested companies          July (typically)
EU CSRD/ESRS  1, 2, 3 (material) Large EU + listed SMEs       With financial report
ISSB S2       1, 2, 3 (material) Per jurisdiction adoption    With financial report
SEC (US)      1, 2               Public companies (US)        With 10-K filing
SBTi          1, 2, 3            Voluntary (committed)        Annual progress report
California    1, 2, 3            >$1B revenue (in CA)         2026 start
UK SECR       1, 2 (+ voluntary) Large UK companies           With directors' report
```

---

*End of Document — 07_GHG_Protocol_Scope_1_2_3.md*
