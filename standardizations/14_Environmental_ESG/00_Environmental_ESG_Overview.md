# Environmental, ESG & Hazardous Substance Compliance — Overview

**Topic:** Environmental compliance landscape covering hazardous substance restrictions, circular economy, greenhouse gas reporting, and ESG frameworks for electronics and manufacturing  
**Standard:** RoHS 3 (EU 2015/863); REACH (EC 1907/2006); WEEE (2012/19/EU); GHG Protocol; ISO 14001:2015; EU CSRD; ISSB IFRS S1/S2  
**SDO:** European Commission; EPA (US); ISO/TC 207; WRI/WBCSD; ISSB/IFRS Foundation; GRI  
**Audience:** Environmental compliance engineers, sustainability managers, product stewardship teams, supply chain managers, ESG analysts, electronics hardware engineers  
**Prerequisites:** Basic chemistry (materials science), supply chain fundamentals, corporate governance concepts

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 1972 | UN Conference on Human Environment (Stockholm) | First global environmental awareness; UNEP established |
| 1987 | Montreal Protocol | CFCs banned; first successful substance restriction treaty |
| 1992 | Rio Earth Summit; UNFCCC established | Climate framework; Agenda 21; biodiversity convention |
| 1994 | Basel Convention on hazardous waste | Transboundary waste movement controls |
| 1996 | ISO 14001:1996 (first edition) | Environmental Management System standard published |
| 2001 | Stockholm Convention (POPs) | Persistent Organic Pollutants restricted globally |
| 2003 | **EU RoHS (2002/95/EC)** + WEEE (2002/96/EC) published | Banned Pb, Hg, Cd, Cr(VI), PBB, PBDE in electronics; producer take-back |
| 2004 | GHG Protocol Corporate Standard (revised) | Scope 1/2/3 framework established as global standard |
| 2006 | **REACH (EC 1907/2006)** enters force | Most comprehensive chemical regulation globally; "no data, no market" |
| 2010 | US Dodd-Frank Act Section 1502 | Conflict minerals (3TG) disclosure for SEC-reporting companies |
| 2011 | RoHS 2 (2011/65/EU) | Expanded scope; CE marking requirement; open scope |
| 2015 | **RoHS 3** (Delegated Directive EU 2015/863) | Added 4 phthalates (DEHP, BBP, DBP, DIBP); Paris Agreement signed |
| 2017 | TCFD Recommendations published | Climate risk disclosure framework; adopted by FSB |
| 2019 | EU Green Deal announced | Carbon neutrality by 2050; circular economy action plan |
| 2020 | EU Taxonomy Regulation (2020/852) | Green finance classification system |
| 2021 | EU Conflict Minerals Regulation full enforcement | Due diligence for 3TG importers |
| 2022 | EU CSRD adopted; EU Battery Regulation | Mandatory ESG reporting; battery passports; recycled content |
| 2023 | **ISSB IFRS S1/S2** published | Global sustainability disclosure baseline; subsumes TCFD |
| 2024 | EU ESPR (Ecodesign for Sustainable Products); EU PFAS restriction proposed; SEC Climate Rules (US) | Digital product passport; PFAS universal ban proposal; US emissions disclosure |
| 2025 | CSRD reporting begins (large companies); EU Battery Regulation obligations phase in | First wave of mandatory EU sustainability reports |

### 1.2 Regulatory Landscape Map

```mermaid
graph TB
    subgraph "Hazardous Substances"
        ROHS[RoHS 3<br/>(EU 2015/863)<br/>━━━━━━━━━━━<br/>10 restricted substances<br/>in electronics<br/>Max 0.1% / 0.01% Cd]
        REACH_S[REACH<br/>(EC 1907/2006)<br/>━━━━━━━━━━━<br/>Registration of chemicals<br/>250+ SVHCs<br/>Authorization required]
        PFAS_S[PFAS Restrictions<br/>━━━━━━━━━━━<br/>EU universal PFAS ban (proposed)<br/>Stockholm Convention<br/>US EPA PFAS roadmap]
        TSCA_S[TSCA (US)<br/>━━━━━━━━━━━<br/>EPA chemical regulation<br/>Significant New Use Rules<br/>Risk evaluation]
    end
    
    subgraph "Circular Economy & Waste"
        WEEE_S[WEEE Directive<br/>(2012/19/EU)<br/>━━━━━━━━━━━<br/>Producer responsibility<br/>Collection: 65%<br/>Recycling targets]
        BATTERY[EU Battery Regulation<br/>(2023/1542)<br/>━━━━━━━━━━━<br/>Battery passport<br/>Recycled content<br/>Carbon footprint]
        ESPR[ESPR<br/>(Ecodesign)<br/>━━━━━━━━━━━<br/>Digital product passport<br/>Right to repair<br/>Recyclability]
    end
    
    subgraph "Climate & GHG"
        GHG[GHG Protocol<br/>━━━━━━━━━━━<br/>Scope 1, 2, 3<br/>Corporate accounting<br/>Value chain]
        PARIS[Paris Agreement<br/>━━━━━━━━━━━<br/>1.5°C target<br/>NDCs<br/>Net-zero by 2050]
        SBTI_S[SBTi<br/>━━━━━━━━━━━<br/>Science-based targets<br/>1.5°C alignment<br/>Net-zero standard]
    end
    
    subgraph "ESG Reporting"
        CSRD[EU CSRD<br/>━━━━━━━━━━━<br/>Mandatory ESG reporting<br/>ESRS standards<br/>Double materiality]
        ISSB_S[ISSB IFRS S1/S2<br/>━━━━━━━━━━━<br/>Global baseline<br/>Climate disclosure<br/>Financial materiality]
        GRI_S[GRI Standards<br/>━━━━━━━━━━━<br/>Impact reporting<br/>Stakeholder materiality<br/>300+ indicators]
    end
    
    subgraph "Supply Chain"
        CONFLICT[Conflict Minerals<br/>━━━━━━━━━━━<br/>3TG (Ta, Sn, W, Au)<br/>Dodd-Frank / EU Reg.<br/>OECD Due Diligence]
        ISO14001[ISO 14001<br/>━━━━━━━━━━━<br/>EMS certification<br/>Continuous improvement<br/>Legal compliance]
    end
```

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 Regulatory Categories Overview

| Category | Key Regulations | Focus | Applies To |
|----------|----------------|-------|-----------|
| **Hazardous substance restriction** | RoHS 3; REACH; TSCA; Prop 65 | Ban/limit toxic chemicals in products | Manufacturers placing products on market |
| **Waste & end-of-life** | WEEE; EU Battery Reg; Basel Convention | Producer responsibility; recycling; take-back | Producers of EEE; battery manufacturers |
| **Chemical management** | REACH Registration/Authorization; CLP; GHS | Ensure chemical safety throughout lifecycle | Chemical manufacturers; importers; downstream users |
| **Conflict minerals** | Dodd-Frank §1502; EU Reg 2017/821; OECD | Responsible mineral sourcing; human rights | Importers; manufacturers using 3TG |
| **Environmental management** | ISO 14001; ISO 50001; EMAS | Systematic environmental improvement | Any organization (voluntary certification) |
| **Climate & GHG** | GHG Protocol; Paris Agreement; SBTi | Measure and reduce emissions | Corporations; governments; value chains |
| **ESG disclosure** | CSRD/ESRS; ISSB; GRI; SEC; TCFD | Transparent sustainability reporting | Listed companies; large enterprises; financial institutions |
| **Circular economy** | ESPR; EU Packaging; Right to Repair | Design for longevity, repairability, recyclability | Product manufacturers (especially electronics) |

### 2.2 Substance Restrictions Summary

| Regulation | Substances | Threshold | Scope | Enforcement |
|-----------|-----------|:---------:|-------|:-----------:|
| **RoHS 3** (EU) | 10: Pb, Hg, Cd, Cr(VI), PBB, PBDE, DEHP, BBP, DBP, DIBP | 0.1% (0.01% Cd) by weight per homogeneous material | EEE (11 categories); CE marking | Market surveillance; fines; product withdrawal |
| **REACH SVHC** | 250+ (candidate list; growing) | 0.1% w/w in article (communication duty); >1t/yr (notification) | All articles placed on EU market | ECHA enforcement; fines; market access denied |
| **REACH Authorization** | Annex XIV (specific substances requiring authorization to use) | Any presence (must apply for authorization or substitute) | Specific uses of listed substances | Cannot use without authorization; sunset dates |
| **REACH Restriction** | Annex XVII (restricted conditions of use) | Varies per entry | Specific substances/uses | Member state enforcement |
| **TSCA** (US) | Significant New Use Rules; risk evaluation chemicals | Varies | US manufacture/import/use | EPA enforcement; SNURs |
| **California Prop 65** | ~900+ chemicals (carcinogens, reproductive toxicants) | "No significant risk level" or "warning" | Products sold in California | Citizen suits; penalties $2,500/day |
| **China RoHS** | Same 6 as EU RoHS (10 substances pending) | Same thresholds | EEE sold in China | Marking; voluntary certification (CCC) |

### 2.3 GHG Protocol Scope Framework

| Scope | Definition | Examples | % of Total (typical manufacturing) |
|:-----:|-----------|---------|:---:|
| **Scope 1** | Direct emissions from owned/controlled sources | Company vehicles; on-site natural gas combustion; refrigerant leaks; manufacturing process emissions | 5-15% |
| **Scope 2** | Indirect emissions from purchased energy | Purchased electricity; purchased steam/heating/cooling | 10-25% |
| **Scope 3** | All other indirect emissions in value chain | Purchased goods (materials); transportation; employee commuting; use of sold products; end-of-life; investments | 60-85% |

---

## Chapter 3 — Technical Deep Dive

### 3.1 RoHS Compliance Technical Requirements

| Substance | CAS Number | Threshold | Common Uses in Electronics | Alternatives |
|-----------|:----------:|:---------:|--------------------------|-------------|
| Lead (Pb) | 7439-92-1 | 0.1% | Solder (Sn-Pb); PVC stabilizer; glass; brass | SAC305 (Sn96.5/Ag3.0/Cu0.5); lead-free glass; RoHS-compliant brass |
| Mercury (Hg) | 7439-97-6 | 0.1% | LCD backlights (CCFL); switches; batteries | LED backlights; solid-state switches |
| Cadmium (Cd) | 7440-43-9 | **0.01%** | Plating; contacts; NiCd batteries; CdS photoresistors | Nickel/tin plating; Li-ion; photodiodes |
| Hexavalent Chromium (Cr⁶⁺) | 18540-29-9 | 0.1% | Corrosion protection (chromate conversion coating); paint pigments | Trivalent chromium; anodizing; zinc-nickel plating |
| PBB | varies | 0.1% | Flame retardants (historic) | Phosphorus-based FRs; metal hydroxides |
| PBDE | varies | 0.1% | Flame retardants (historic) | Non-halogen FRs (phosphorus, nitrogen-based) |
| DEHP | 117-81-7 | 0.1% | PVC plasticizer (cables, housings) | DINCH; DEHT; ATBC (bio-based) |
| BBP | 85-68-7 | 0.1% | PVC plasticizer | Same alternatives as DEHP |
| DBP | 84-74-2 | 0.1% | PVC plasticizer; adhesives | Same alternatives |
| DIBP | 84-69-5 | 0.1% | PVC plasticizer | Same alternatives |

### 3.2 REACH SVHC Management Process

```mermaid
flowchart TB
    START[New SVHC Added to<br/>Candidate List<br/>(ECHA publishes update;<br/>typically 2×/year)]
    
    SCREEN[Screen Product Portfolio<br/>━━━━━━━━━━━<br/>• Check all articles vs. new SVHC<br/>• Check Full Material Declarations<br/>• Check supplier declarations<br/>• Threshold: >0.1% w/w in article]
    
    ASSESS{SVHC present<br/>>0.1% w/w?}
    
    NO_ISSUE[No Action Required<br/>(document assessment)]
    
    PRESENT[SVHC Present >0.1%<br/>━━━━━━━━━━━<br/>Obligations triggered]
    
    COMM[Communication Duty<br/>(Article 33)<br/>━━━━━━━━━━━<br/>• Inform professional customers<br/>  (sufficient information for safe use)<br/>• Inform consumers (on request;<br/>  within 45 days; free of charge)]
    
    NOTIFY{>1 tonne/year<br/>total in articles?}
    
    NOTIF_ECHA[ECHA Notification<br/>(Article 7.2)<br/>━━━━━━━━━━━<br/>• Notify ECHA within 6 months<br/>• Substance identity<br/>• Tonnage band<br/>• Brief description of use]
    
    SUBSTITUTE[Substitution Assessment<br/>━━━━━━━━━━━<br/>• Technical alternatives?<br/>• Economic feasibility?<br/>• Risk comparison?<br/>• Timeline for substitution?]
    
    AUTH_CHECK{SVHC moved to<br/>Authorization List<br/>(Annex XIV)?}
    
    AUTHORIZE[Must Apply for Authorization<br/>OR Substitute Before Sunset Date<br/>━━━━━━━━━━━<br/>• Analysis of alternatives<br/>• Socioeconomic analysis<br/>• Substitution plan<br/>• Apply to ECHA (€50K-€200K+)]
    
    START --> SCREEN --> ASSESS
    ASSESS -->|No| NO_ISSUE
    ASSESS -->|Yes| PRESENT
    PRESENT --> COMM
    PRESENT --> NOTIFY
    NOTIFY -->|Yes| NOTIF_ECHA
    NOTIFY -->|No| SUBSTITUTE
    NOTIF_ECHA --> SUBSTITUTE
    SUBSTITUTE --> AUTH_CHECK
    AUTH_CHECK -->|Yes| AUTHORIZE
    AUTH_CHECK -->|No (monitoring)| SUBSTITUTE
```

### 3.3 ESG Reporting Standards Comparison

| Framework | Materiality | Mandatory? | Scope | Metrics |
|-----------|:-----------:|:---:|-------|---------|
| **ISSB IFRS S1/S2** | Financial (investor-focused) | Becoming mandatory (jurisdictional adoption) | Sustainability risks/opportunities affecting enterprise value | Climate: Scope 1/2/3; governance; strategy; risk management |
| **EU CSRD/ESRS** | **Double** (financial + impact) | **Mandatory** (EU large companies; phased 2025-2028) | Full ESG: environmental, social, governance | 1,000+ data points; 12 ESRS standards; detailed climate/biodiversity/workforce |
| **GRI Standards** | Impact (stakeholder-focused) | Voluntary (but referenced by CSRD) | Comprehensive sustainability impacts | 300+ topic-specific indicators; GRI 300 (environmental); GRI 400 (social) |
| **CDP** | Climate/water/forests | Voluntary (investor-driven) | Climate change; water security; deforestation | Aligned with TCFD; supply chain programs; scoring A-D |
| **SEC Climate** (US) | Financial (investor-focused) | Mandatory (large US-listed companies; phased) | GHG emissions; climate risk | Scope 1/2 (mandatory); Scope 3 (certain conditions); climate risk disclosures |
| **TCFD** (superseded by ISSB) | Financial | Was voluntary; now subsumed into ISSB S2 | Climate-related risks and opportunities | Governance; strategy; risk management; metrics & targets |

---

## Chapter 4 — Implementation Guide

### 4.1 Product Environmental Compliance Program

| Phase | Activities | Output |
|:-----:|-----------|--------|
| **1. Regulatory monitoring** | Track new regulations; candidate list updates; restriction proposals; upcoming enforcement dates | Regulatory watchlist; compliance calendar; impact assessment |
| **2. Supply chain declarations** | Collect material declarations from all suppliers (IPC-1752A; Full Material Declaration; IMDS for automotive) | Material composition database; SVHC status per component |
| **3. Compliance assessment** | Compare product composition to all applicable regulations (RoHS, REACH, regional equivalents) | Compliance status per product per market; gap analysis |
| **4. Testing (verification)** | XRF screening; IEC 62321 analytical testing for restricted substances | Test reports; evidence of compliance |
| **5. Substitution/redesign** | Replace non-compliant materials; qualify alternatives; validate performance | Engineering change orders; qualification reports |
| **6. Documentation** | Technical documentation; declarations of conformity; certificates | RoHS DoC; REACH SVHC communication; compliance files |
| **7. Ongoing management** | Periodic supplier re-declarations; regulatory updates; new product assessment | Updated compliance status; audit records |

### 4.2 GHG Inventory Implementation

| Step | Action | Output |
|:----:|--------|--------|
| 1 | Define organizational boundary (operational control vs. equity share) | Boundary documentation; included entities |
| 2 | Define operational boundary (Scope 1, 2, 3 categories included) | Scope determination; category selection rationale |
| 3 | Identify emission sources per scope | Source inventory (fuel combustion, refrigerants, electricity, purchased goods, transport...) |
| 4 | Collect activity data | Fuel consumption (liters/kWh); electricity bills; travel records; procurement data; logistics data |
| 5 | Select emission factors | Source: DEFRA, EPA, IEA, ecoinvent; location-based vs. market-based (Scope 2) |
| 6 | Calculate emissions: $\text{Emissions} = \text{Activity Data} \times \text{Emission Factor}$ | GHG inventory (tCO₂e) by scope and category |
| 7 | Verify (third-party if required) | Verification statement (ISO 14064-3; or assurance per ISAE 3000/3410) |
| 8 | Report | GHG inventory report; CDP response; CSRD/ESRS disclosure; SBTi tracking |

---

## Chapter 5 — Certification & Compliance

### 5.1 Compliance Pathways

| Standard/Regulation | Compliance Method | Evidence Required |
|--------------------|:-----------------:|-------------------|
| RoHS 3 | Self-declaration (DoC); CE marking; technical documentation | Material declarations; test reports (IEC 62321); DoC per product |
| REACH | No "REACH certification"; obligations: registration (manufacturers/importers >1t/yr); communication (SVHC >0.1%); authorization (Annex XIV) | Safety Data Sheets; SVHC communication to customers; SCIP database notification |
| WEEE | Producer registration per EU member state; compliance scheme membership; marking | Registration numbers; recycling scheme contracts; WEEE symbol on product |
| ISO 14001 | Third-party certification audit (ISO 17021 accredited CB) | Certificate (3-year cycle; annual surveillance audits) |
| ISO 50001 | Third-party certification audit | Certificate; energy performance improvement evidence |
| GHG Protocol | No certification; voluntary reporting; can be assured | GHG inventory report; optional third-party verification |
| SBTi | Target validation by SBTi | Validated target letter; annual progress reporting |
| CSRD/ESRS | Mandatory assurance (limited; moving to reasonable) | Sustainability report; auditor assurance statement |
| EU Taxonomy | Self-assessment (alignment to technical screening criteria) | Disclosure of % revenue/CapEx/OpEx aligned with Taxonomy |
| Conflict minerals | Due diligence (OECD framework); CMRT reporting; third-party audit (RMI RMAP) | CMRT; due diligence report; smelter audit status |

### 5.2 Testing Methods for RoHS (IEC 62321 Series)

| Part | Method | Substance | Technique |
|:----:|--------|-----------|-----------|
| 62321-2 | Sample preparation | All | Disassembly; homogeneous material identification; sample prep |
| 62321-3-1 | Screening | Pb, Hg, Cd, Cr, Br | XRF (X-ray fluorescence) — rapid, non-destructive screening |
| 62321-4 | Determination | Pb, Cd, Cr total | ICP-OES or ICP-MS (digestion + spectrometry) |
| 62321-5 | Determination | Cd, Pb, Cr in polymers/electronics | AAS, ICP-OES |
| 62321-6 | Determination | PBB, PBDE | GC-MS (gas chromatography - mass spectrometry) |
| 62321-7-1 | Determination | Cr(VI) — chromium coatings | Spot test + spectrophotometry (diphenylcarbazide method) |
| 62321-7-2 | Determination | Cr(VI) — polymers/electronics | Alkaline digestion + IC or spectrophotometry |
| 62321-8 | Determination | Phthalates (DEHP, BBP, DBP, DIBP) | GC-MS (solvent extraction + chromatography) |
| 62321-9 | Determination | Hg | ICP-OES; cold vapor AAS |
| — | Screening (industry practice) | All RoHS substances | Handheld XRF for incoming inspection; lab XRF for qualification |

---

## Chapter 6 — Regional Context

### 6.1 Environmental Regulations by Region

| Region | Substance Restrictions | Waste/Recycling | Climate/ESG | Key Differences |
|--------|:-----:|:-----:|:-----:|----------------|
| **EU** | RoHS 3; REACH; CLP; PFAS restriction (pending) | WEEE; Battery Reg; Packaging; ESPR | CSRD/ESRS; EU Taxonomy; ETS; CBAM | Most comprehensive; "precautionary principle"; chemical-by-chemical approach |
| **US** | TSCA; Prop 65; state-level (no federal RoHS) | EPA e-waste (fragmented; state-level) | SEC Climate; state programs (CA) | Fragmented; sector-specific; litigation-driven (Prop 65) |
| **China** | China RoHS (SJ/T 11364); China REACH equivalent evolving | WEEE-like (extended producer responsibility) | Carbon trading scheme; dual carbon goals | Rapid evolution; marking-focused; GB standards |
| **Japan** | J-MOSS (JIS C 0950); Chemical Substances Management | Home Appliance Recycling Law; Small Electronics | TCFD disclosure; carbon neutrality 2050 | Cooperative industry approach; green procurement |
| **South Korea** | K-REACH; K-RoHS (Act on Resource Circulation) | WEEE equivalent; EPR | K-Taxonomy; carbon trading | Aligned with EU; rapid adoption |
| **India** | India RoHS (E-Waste Management Rules 2022); updated EPR | E-Waste Management Rules; EPR | National Climate targets; ESG disclosure (BRSR) | Rapidly developing; enforcement growing |

---

## Chapter 7 — Comparison

### 7.1 RoHS Variants Worldwide

| Dimension | EU RoHS 3 | China RoHS | Japan (J-MOSS) | US (no federal) |
|-----------|:---:|:---:|:---:|:---:|
| Mandatory | Yes (CE marking) | Yes (labeling + catalog) | Voluntary (JIS marking) | No federal; state varies |
| Substances | 10 | 6 (original) + expanding | 6 | Varies by state |
| Threshold | 0.1% (Cd: 0.01%) | Same as EU | Same as EU | N/A |
| Scope | All EEE (11 categories) | EEE (product catalog) | 7 product categories | N/A |
| Enforcement | Market surveillance + penalties | SAMR + local authorities | Industry self-regulation | State enforcement (CA/WA/NY) |
| Exemptions | ~200 specific exemptions; renewable | Different exemption list | Industry-managed | N/A |

### 7.2 ESG Frameworks Comparison

| Dimension | CSRD/ESRS (EU) | ISSB S1/S2 | GRI | SEC (US) |
|-----------|:---:|:---:|:---:|:---:|
| Materiality | Double (financial + impact) | Financial only (investor) | Impact (stakeholder) | Financial (investor) |
| Mandatory | Yes (phased 2025-2028) | Jurisdictional (IOSCO endorsed) | Voluntary (but CSRD references) | Yes (phased; SEC registrants) |
| Scope | Full ESG (E + S + G); 12 standards | Climate primary (S2); general (S1) | All sustainability topics | Climate focus (Scope 1/2; limited Scope 3) |
| Assurance | Required (limited → reasonable) | Expected (per jurisdiction) | Optional | Required (Scope 1/2) |
| Applicability | EU large companies + listed SMEs + non-EU with EU revenue >€150M | Global (per jurisdiction) | Any organization globally | US-listed companies |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 Product Environmental Compliance Lifecycle

```mermaid
graph TB
    subgraph "Design Phase"
        DESIGN[Product Design<br/>━━━━━━━━━━━<br/>• Material selection (RoHS/REACH compliant)<br/>• DfE (Design for Environment)<br/>• LCA consideration<br/>• Recyclability design<br/>• Hazardous substance avoidance]
    end
    
    subgraph "Supply Chain"
        SUPPLIER[Supplier Management<br/>━━━━━━━━━━━<br/>• Material declarations (IPC-1752A)<br/>• Supplier SVHC declarations<br/>• Conflict mineral due diligence (CMRT)<br/>• Audit / qualification]
    end
    
    subgraph "Manufacturing"
        MFG[Manufacturing<br/>━━━━━━━━━━━<br/>• Lead-free soldering (SAC305)<br/>• Process chemical controls<br/>• Waste management<br/>• Energy management (ISO 50001)<br/>• Emissions tracking]
    end
    
    subgraph "Market Placement"
        MARKET[Market Access<br/>━━━━━━━━━━━<br/>• RoHS DoC + CE marking<br/>• WEEE registration + marking<br/>• REACH SCIP notification<br/>• Battery compliance<br/>• Country-specific requirements]
    end
    
    subgraph "Use Phase"
        USE[Product Use<br/>━━━━━━━━━━━<br/>• Energy consumption reporting<br/>• Consumables compliance<br/>• Service/repair (right to repair)<br/>• Product carbon footprint]
    end
    
    subgraph "End of Life"
        EOL[End-of-Life<br/>━━━━━━━━━━━<br/>• WEEE collection & recycling<br/>• Battery take-back<br/>• Material recovery<br/>• Proper disposal of hazardous<br/>• Circular economy (reuse/refurbish)]
    end
    
    DESIGN --> SUPPLIER --> MFG --> MARKET --> USE --> EOL
    EOL -->|"Circular economy<br/>(materials back to design)"| DESIGN
```

### 8.2 ESG Reporting Ecosystem

```mermaid
graph TB
    subgraph "Data Collection"
        ENV_DATA[Environmental Data<br/>━━━━━━━━━━━<br/>• GHG emissions (Scope 1/2/3)<br/>• Energy consumption<br/>• Water use<br/>• Waste generation<br/>• Biodiversity impact<br/>• Pollution]
        SOC_DATA[Social Data<br/>━━━━━━━━━━━<br/>• Workforce metrics<br/>• Health & safety<br/>• Human rights<br/>• Supply chain labor<br/>• Community impact<br/>• DEI metrics]
        GOV_DATA[Governance Data<br/>━━━━━━━━━━━<br/>• Board composition<br/>• Ethics & compliance<br/>• Risk management<br/>• Executive compensation<br/>• Anti-corruption<br/>• Data privacy]
    end
    
    subgraph "Frameworks & Standards"
        ESRS_F[ESRS<br/>(EU CSRD)]
        ISSB_F[ISSB S1/S2<br/>(Global)]
        GRI_F[GRI Standards<br/>(Impact)]
        CDP_F[CDP<br/>(Climate/Water)]
    end
    
    subgraph "Outputs"
        SUST_REPORT[Sustainability Report<br/>(Annual; assured)]
        FIN_FILING[Financial Filings<br/>(Climate risk in annual report)]
        CDP_RESP[CDP Response<br/>(Supply chain rating)]
        REG_FILING[Regulatory Filings<br/>(EU Taxonomy disclosure;<br/>SEC filing)]
    end
    
    subgraph "Stakeholders"
        INVEST[Investors<br/>(ESG ratings; risk)]
        REG[Regulators<br/>(Compliance)]
        CUST[Customers<br/>(Supply chain requirements)]
        PUBLIC[Public<br/>(Transparency)]
    end
    
    ENV_DATA & SOC_DATA & GOV_DATA --> ESRS_F & ISSB_F & GRI_F & CDP_F
    ESRS_F --> SUST_REPORT
    ISSB_F --> FIN_FILING
    GRI_F --> SUST_REPORT
    CDP_F --> CDP_RESP
    SUST_REPORT & FIN_FILING & CDP_RESP & REG_FILING --> INVEST & REG & CUST & PUBLIC
```

---

## Chapter 9 — Case Studies

### 9.1 Case Study: Electronics Company RoHS + REACH Compliance Transformation

| Aspect | Detail |
|--------|--------|
| Company | Mid-size electronics manufacturer (industrial controls); 500 products; 3,000 components; 200 suppliers; selling to EU, US, Asia |
| Challenge | Originally compliant with RoHS 2 (6 substances). RoHS 3 added 4 phthalates (2019 enforcement for all categories). REACH candidate list grew from 170 to 250+ SVHCs. Multiple products at risk. |
| Approach | (1) **Material declaration program**: required ALL suppliers to submit IPC-1752A Full Material Declarations (FMD) — not just restricted substance declarations. Built internal database of exact material compositions. (2) **Risk screening**: automated screening of FMD database against RoHS + REACH SVHC list. Identified 47 components with phthalate risk (PVC cables, gaskets, flexible housings). 12 components with SVHC >0.1%. (3) **Substitution program**: prioritized by risk (phthalate-containing PVC cables → LSZH cables; PVC housings → TPE/polycarbonate alternatives; SVHC-containing components → alternative suppliers/materials). (4) **Qualification**: reliability testing of alternatives (thermal cycling, vibration, accelerated aging — critical for industrial environment). (5) **Supplier management**: contractual requirements for ongoing declarations; automatic trigger for re-declaration when candidate list updated; audit program for critical suppliers. |
| Results | Full RoHS 3 + REACH compliance achieved 6 months before enforcement deadline. SVHC communication automated (customer portal with per-product SVHC status). Substitution program eliminated 85% of SVHC-containing components. Total cost: ~€2M (engineering + qualification + procurement). Avoided: market access loss in EU (estimated revenue at risk: €50M/year). |
| Lessons | (1) Full Material Declarations (not just restricted substance) enable proactive compliance. (2) Start 18+ months before enforcement. (3) Supplier cooperation critical — establish contractual requirements. (4) Material database is a strategic asset (reusable for future regulations). |

### 9.2 Case Study: Scope 3 GHG Reporting for Automotive Supplier

| Aspect | Detail |
|--------|--------|
| Company | Tier 1 automotive electronics supplier; €5B revenue; 30 manufacturing sites globally; 2,000+ direct material suppliers |
| Challenge | Customer (OEM) requires SBTi-validated climate target including Scope 3; 85% of emissions are Scope 3 (purchased goods = 60%; use of sold products = 15%; logistics = 10%) |
| Approach | (1) **Scope 3 screening** (Category relevance): all 15 GHG Protocol Scope 3 categories assessed; top 3 by magnitude: Category 1 (purchased goods), Category 11 (use of sold products), Category 4 (upstream transportation). (2) **Category 1 (purchased goods)**: primary data from top 50 suppliers (cover 70% of spend); industry-average emission factors (ecoinvent) for remainder; activity data = procurement data (kg materials × emission factor). (3) **Category 11 (use of sold products)**: estimated energy consumption of products during use-phase (watts × operating hours × grid emission factor × product lifetime). Key assumption: average grid factor of customer markets. (4) **Category 4 (transport)**: freight data from logistics providers; tonne-km × mode-specific emission factors. (5) **SBTi target**: 42% absolute reduction in Scope 1+2 by 2030 (1.5°C aligned); 25% reduction in Scope 3 intensity (per unit revenue) by 2030 (well-below 2°C). |
| Actions | Scope 1+2: 100% renewable electricity (PPA + certificates); electrification of heating; fleet EV transition. Scope 3 (purchased goods): supplier engagement program (top 50 suppliers set own SBTi targets); material efficiency improvement (10% less material per product through design); low-carbon material selection (recycled aluminum; low-carbon steel). Scope 3 (use phase): energy efficiency improvement of products (20% reduction in power consumption per product generation). |
| Result | Scope 1+2: reduced 35% (Year 1-3); on track for 42% by 2030. Scope 3: intensity reduced 12% (Year 1-3); tracking toward 25% by 2030. SBTi target validated. CDP score: A- (from C two years prior). Customer satisfaction: meets OEM supplier sustainability requirements. |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **EU PFAS universal restriction** | 2025-2027 (proposal → enforcement) | Potentially broadest chemical restriction ever; impacts electronics (solder masks, O-rings, lubricants, cable insulation) |
| **Digital Product Passport (DPP)** | 2027+ (EU ESPR) | QR code on every product linking to environmental data (materials, carbon footprint, repairability, recyclability) |
| **Scope 3 mandatory reporting** | 2025-2028 | CSRD requires value chain emissions; SEC for certain companies; industry data quality must improve |
| **Nature/biodiversity disclosure** | 2024-2026 | TNFD (Taskforce on Nature); ESRS E4 (biodiversity); beyond just carbon |
| **Carbon Border Adjustment Mechanism (CBAM)** | 2026 (full enforcement EU) | Carbon price on imports; affects supply chain decisions |
| **Right to Repair** | 2025+ (EU) | Spare parts availability; repair manuals; software updates; design for repairability |
| **Extended Producer Responsibility expansion** | Ongoing | Beyond WEEE: textiles, packaging, batteries; increased financial responsibility |
| **AI in compliance** | Now | Automated substance screening; predictive regulatory change; supply chain risk AI; GHG calculation automation |
| **Circular economy metrics** | 2025+ | Recycled content requirements (batteries: 16% cobalt, 6% lithium by 2031); recyclability scoring |
| **Supply chain due diligence** | 2024-2026 | EU CSDDD (Corporate Sustainability Due Diligence Directive); human rights + environmental due diligence mandatory |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is RoHS and what substances does it restrict?  
**A:** RoHS = **Restriction of Hazardous Substances** (EU Directive 2011/65/EU as amended by EU 2015/863 "RoHS 3"). It restricts 10 substances in Electrical and Electronic Equipment (EEE) placed on the EU market. The 10 restricted substances with maximum concentration limits (per homogeneous material, by weight): Lead (Pb): 0.1%; Mercury (Hg): 0.1%; Cadmium (Cd): **0.01%** (most stringent); Hexavalent Chromium (Cr⁶⁺): 0.1%; Polybrominated Biphenyls (PBB): 0.1%; Polybrominated Diphenyl Ethers (PBDE): 0.1%; Bis(2-ethylhexyl) phthalate (DEHP): 0.1%; Butyl benzyl phthalate (BBP): 0.1%; Dibutyl phthalate (DBP): 0.1%; Diisobutyl phthalate (DIBP): 0.1%. Key points: applies to 11 categories of EEE; requires CE marking; manufacturer must maintain technical documentation demonstrating compliance; specific exemptions exist (time-limited, renewable); measured per "homogeneous material" (not per product — each material layer/alloy/coating assessed separately).

**Q2:** What is the difference between RoHS and REACH?  
**A:** **RoHS** is a product-specific restriction: it bans specific substances above concentration limits in a specific product type (EEE). If your electronic product contains >0.1% lead, you cannot place it on the EU market (with limited exemptions). It's a hard ban. **REACH** is a comprehensive chemical management regulation covering ALL chemicals in ALL products (not just electronics). It has multiple mechanisms: (1) Registration: manufacturers/importers of chemicals >1 tonne/year must register with ECHA. (2) SVHC communication: if an article contains a Substance of Very High Concern >0.1%, you must inform customers. (3) Authorization: certain SVHCs moved to Annex XIV require authorization to use (or substitute). (4) Restriction: specific conditions of use restricted (Annex XVII). REACH is broader but often less absolute than RoHS — it doesn't always ban substances outright; it may require communication, registration, or authorization. RoHS is narrower (only EEE) but more definitive (banned above threshold; no authorization option). Both apply to products on EU market. A product may need to comply with BOTH simultaneously.

### Tier 2: Mid-Level

**Q3:** How would you set up a Scope 3 GHG inventory for a manufacturing company with complex supply chains?  
**A:** [Detailed answer covering: GHG Protocol Corporate Value Chain (Scope 3) guidance methodology. Categorize all 15 Scope 3 categories. Screen for relevance (which categories apply and are material). For manufacturing, typically top categories: Cat 1 (purchased goods/services) — use spend-based method initially (spend × industry emission factor per $ from EEIO models), then transition to supplier-specific data for top suppliers. Cat 4 (upstream transport) — use tonne-km × transport mode emission factors (from logistics providers or estimates). Cat 11 (use of sold products) — calculate energy use during product lifetime × appropriate grid emission factor. Data collection strategy: tiered approach (primary data from top 20 suppliers covering 70% of emissions; secondary data/estimates for remainder). Quality improvement plan: engage suppliers for primary data over 3 years. Verification: third-party per ISO 14064-3. Reporting: per GHG Protocol; include in CDP and CSRD/ESRS.]

### Tier 3: Senior

**Q4:** Your company must comply with EU CSRD starting 2025. You have operations in 15 EU countries and significant non-EU supply chains. Design the ESG data management and reporting architecture.  
**A:** [Comprehensive answer covering: (1) Double materiality assessment (financial + impact); (2) ESRS gap analysis (12 standards; 1000+ data points); (3) Data architecture: centralized ESG data platform; automated data collection from ERP/HR/procurement/energy systems; supplier data portal; calculation engines for GHG/water/waste; (4) Process: annual reporting cycle with quarterly data collection; ESRS-specific data owners per topic; internal audit of ESG data; (5) Assurance: limited assurance Year 1 (moving to reasonable); auditor selection; controls over ESG data quality; (6) Integration with financial reporting (same timeline; management report); (7) Technology: ESG platform (Workiva/Sphera/Persefoni/SAP Sustainability); integration with existing systems; (8) Governance: sustainability committee; CSRD project office; roles and responsibilities.]

---

## Chapter 12 — Cheat Sheet & Quick Reference

### RoHS 3 — Quick Reference

```
10 Restricted Substances (max concentration per homogeneous material):
  Pb   (Lead):                 0.1%    (1000 ppm)
  Hg   (Mercury):             0.1%    (1000 ppm)
  Cd   (Cadmium):             0.01%   (100 ppm)  ← STRICTEST
  Cr⁶⁺ (Hexavalent Chromium): 0.1%    (1000 ppm)
  PBB  (Polybrominated Biphenyls):    0.1%
  PBDE (Polybrominated Diphenyl Ethers): 0.1%
  DEHP (phthalate):           0.1%
  BBP  (phthalate):           0.1%
  DBP  (phthalate):           0.1%
  DIBP (phthalate):           0.1%
```

### REACH Key Obligations

```
IF you MANUFACTURE/IMPORT chemicals >1 tonne/year in EU:
  → REGISTER with ECHA ("no data, no market")

IF your ARTICLE contains SVHC >0.1% w/w:
  → COMMUNICATE to professional customers (proactive)
  → COMMUNICATE to consumers (on request, within 45 days)
  → NOTIFY ECHA (if >1 tonne/year of that SVHC in articles)
  → SCIP database notification (waste operators)

IF substance is on AUTHORIZATION LIST (Annex XIV):
  → Cannot use after sunset date WITHOUT authorization
  → Must submit authorization application OR substitute
```

### GHG Protocol Scopes

```
SCOPE 1 (Direct):      Owned/controlled sources (fuel combustion, company vehicles, refrigerants, process emissions)
SCOPE 2 (Indirect):    Purchased energy (electricity, steam, heating, cooling)
SCOPE 3 (Value chain): Everything else (15 categories):
  UPSTREAM:   1-Purchased goods; 2-Capital goods; 3-Fuel/energy; 4-Transport; 5-Waste; 6-Business travel; 7-Commuting; 8-Leased assets
  DOWNSTREAM: 9-Transport; 10-Processing; 11-Use of products; 12-End-of-life; 13-Leased assets; 14-Franchises; 15-Investments
```

### EU CSRD Timeline

```
FY 2024 (report 2025): Large listed companies already under NFRD
FY 2025 (report 2026): All large companies (>250 employees; >€50M revenue; >€25M assets: 2 of 3)
FY 2026 (report 2027): Listed SMEs (opt-out possible until 2028)
FY 2028 (report 2029): Non-EU companies with >€150M EU net turnover
```

---

*End of Document — 00_Environmental_ESG_Overview.md*
