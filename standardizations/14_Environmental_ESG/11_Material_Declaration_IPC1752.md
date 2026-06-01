# Material Declaration — IPC-1752A & Supply Chain Data Exchange

**Topic:** Material Declaration standards for electronics — IPC-1752A (Materials Declaration Management), full material disclosure (FMD), substance compliance reporting, and supply chain material data exchange  
**Standard:** IPC-1752A (Materials Declaration Management); IEC 62474 (Material declaration for products of and for the electrotechnical industry); IPC-1754 (Materials Declaration in Automotive); IMDS (International Material Data System); SCIP Database (EU)  
**SDO:** IPC (Association Connecting Electronics Industries); IEC TC 111; IMDS (managed by DXC/HP); ECHA (SCIP database)  
**Audience:** Materials compliance engineers, supplier quality engineers, EHS professionals, product compliance managers, supply chain managers, electronics component engineers  
**Prerequisites:** Basic chemistry (compounds, CAS numbers), electronics BOM structure, regulatory knowledge (RoHS, REACH), XML/data exchange concepts

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 2000 | IMDS (International Material Data System) launched | Automotive industry creates centralized system for material data collection from supply chain; model for other industries |
| 2002 | EU RoHS Directive 2002/95/EC adopted | Restrictions on hazardous substances in electronics → need to collect material data from supply chain for compliance |
| 2003 | JGPSSI/JIG joint industry guides (Japan) | Japanese electronics manufacturers create material declaration format (precursor to IPC-1752) |
| 2004 | IPC-1751 (Generic Requirements for Declaration Process Management) | First IPC standard addressing material declaration process; framework document |
| 2005 | IPC-1752 version 1.0 released | First version of Materials Declaration standard for electronics; XML schema for data exchange |
| 2006 | EU REACH Regulation adopted (1907/2006) | Extensive substance reporting requirements → increased need for material data from supply chain |
| 2007 | IEC 62474 development begins | International standard for material declaration; harmonized approach across electrotechnical industry |
| 2008 | IPC-1752A released | Major revision; added Full Material Declaration (FMD) class; improved XML schema; alignment with regulatory needs |
| 2012 | IEC 62474:2012 published | International material declaration standard; declarable substance lists; XML data format |
| 2013 | EU RoHS 2 (2011/65/EU) full enforcement | Expanded scope; CE marking; technical documentation requires substance evidence → more declaration pressure |
| 2014 | China RoHS 2 (2016 enforcement) | SJ/T 11364 requires material declaration labels on electronics; need for supplier data |
| 2018 | SCIP database requirement (EU Waste Framework Directive amendment) | Articles containing SVHCs >0.1% must be notified to SCIP (from 2021); requires compositional knowledge |
| 2019 | IPC-1752B development | Update to address IEC 62474 alignment; EU regulatory changes; improved usability |
| 2020 | IPC-1754 published | Material declaration specifically for automotive electronics applications; IMDS-compatible |
| 2021 | **SCIP database operational** (EU; managed by ECHA) | Mandatory notification for articles with SVHC >0.1% w/w; supply chain must provide material composition data |
| 2023 | IEC 62474 database update; EU Digital Product Passport development | Declarable substance list continuously updated; DPP will reference material declarations |
| 2024 | EU ESPR/DPP standards reference material declaration data | Digital Product Passport requires material composition information; IPC-1752/IEC 62474 as data source |

### 1.2 Why Material Declaration Matters

| Driver | Requirement | How Material Declaration Helps |
|--------|------------|-------------------------------|
| **RoHS compliance** | Prove restricted substances <threshold (Pb <1000 ppm; Cd <100 ppm; etc.) | Supplier declares substance content; evidence for technical documentation |
| **REACH SVHC** | Communicate SVHC >0.1% w/w in articles to customers | Material declaration identifies all substances → screen for SVHC presence |
| **REACH Annex XVII** | Restricted substances (e.g., PFAS proposal; Cr6+; phthalates) | FMD provides complete composition → check against all current & future restrictions |
| **SCIP notification** | Report articles with SVHC >0.1% to ECHA database | Material composition data enables identification of SVHC-containing articles |
| **EU Battery Regulation** | Recycled content; hazardous substance content; material composition for passport | Material declaration provides compositional data for compliance calculations |
| **EU Digital Product Passport** | Material composition; recyclability; hazardous substances in product | Material declaration is data foundation for DPP material information |
| **Conflict minerals** | Identify presence of 3TG (tin, tantalum, tungsten, gold) | FMD identifies all metals/materials → screen for 3TG content |
| **Customer requirements** | OEM material specifications; banned substance lists; voluntary commitments | Material declarations prove conformity to customer specifications |
| **Circular economy** | Know what materials are in products for recycling and recovery | Complete material composition enables efficient recycling and material recovery |

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 Declaration Classes (IPC-1752A)

| Class | Name | Content | Depth | When Used |
|:-----:|------|---------|:-----:|-----------|
| **Class A** | Basic | Regulatory compliance statement; presence/absence of restricted substances (e.g., RoHS substances); threshold compliance | Lowest | Simple compliance confirmation; small/commodity components |
| **Class B** | Detailed | Substance-level information for declarable/restricted substances; CAS numbers; concentrations; homogeneous material identification | Medium | Standard compliance; REACH communication; most B2B exchange |
| **Class C** | FMD (Full Material Declaration) | Complete material composition: ALL substances and materials declared (not just restricted ones); mass percentages; all homogeneous materials | High | Automotive (IMDS requirement); high-risk applications; proactive compliance; future-proofing |
| **Class D** | Advanced FMD | Class C + additional information: REACH registration status; recycled content; renewable content; material origin/sourcing | Highest | EU Battery Regulation; Digital Product Passport; circular economy; advanced regulatory |

### 2.2 IPC-1752A Data Structure (XML Schema)

```mermaid
graph TB
    subgraph "IPC-1752A XML Structure"
        DOC[Document Header<br/>━━━━━━━━━━━<br/>• Declaration ID<br/>• Version<br/>• Date<br/>• Class (A/B/C/D)<br/>• Standard version<br/>• Language]
        
        SUPPLIER[Supplier Information<br/>━━━━━━━━━━━<br/>• Company name<br/>• Address<br/>• Contact<br/>• DUNS number]
        
        PRODUCT[Product Information<br/>━━━━━━━━━━━<br/>• Part number<br/>• Part name<br/>• Manufacturer<br/>• Mass (weight)<br/>• Unit of measure]
        
        subgraph "Composition (Class B/C/D)"
            HM[Homogeneous Material<br/>━━━━━━━━━━━<br/>• Material name<br/>• Material class<br/>• Mass<br/>• Mass %]
            
            SUBST[Substance<br/>━━━━━━━━━━━<br/>• Substance name<br/>• CAS number<br/>• Mass<br/>• Mass %<br/>• Above threshold?]
        end
        
        REG[Regulatory Compliance<br/>━━━━━━━━━━━<br/>• RoHS compliance (Y/N)<br/>• REACH SVHC (above?>0.1%?)<br/>• Exemption claimed?<br/>• Exemption reference]
    end
    
    DOC --> SUPPLIER --> PRODUCT --> HM --> SUBST
    PRODUCT --> REG
```

### 2.3 IPC-1752A vs. IEC 62474

| Aspect | IPC-1752A | IEC 62474 |
|--------|:---:|:---:|
| **Scope** | Electronics industry (broad) | Electrotechnical industry (IEC scope; broader than electronics) |
| **Region** | Global (strong US/Asia adoption) | Global (strong EU adoption; IEC membership) |
| **Data format** | XML schema (IPC-defined) | XML schema (IEC-defined; different structure) |
| **Declaration classes** | A, B, C, D (increasingly detailed) | Based on declarable substance list (DSL) approach |
| **Substance lists** | References external lists (RoHS, REACH, GADSL, etc.) | Maintains own Declarable Substance List (DSL) on IEC 62474 database |
| **Automotive** | IPC-1754 (automotive variant) | Not automotive-focused (IMDS dominates automotive) |
| **Relationship** | Complementary; some overlap | Complementary; some overlap |
| **Tool ecosystem** | BOMcheck; Assent; SiliconExpert; various | Same tools often support both |

---

## Chapter 3 — Technical Deep Dive

### 3.1 Homogeneous Material Concept

| Term | Definition | Example in Electronics |
|------|-----------|----------------------|
| **Homogeneous material** | A material that cannot be mechanically disjointed into different materials (ISO def; EU RoHS def) | Solder alloy (Sn96.5Ag3Cu0.5); FR-4 epoxy resin; copper conductor; gold plating; plastic housing material; silicone potting |
| **Article** | An object given a specific shape, surface, or design during manufacturing which determines its function | PCB; connector housing; IC package; wire; screw |
| **Component** | Assembly of articles and/or homogeneous materials | Capacitor (ceramic body + termination metal + solder + plating); IC (die + leadframe + mold compound + solder balls) |

### 3.2 Substance Identification

| Identifier | Description | Example |
|-----------|-------------|---------|
| **CAS Number** | Chemical Abstracts Service Registry Number; unique numerical identifier for chemical substances | Lead: 7439-92-1; Cadmium: 7440-43-9; DEHP: 117-81-7; PFOA: 335-67-1 |
| **EC Number** | European Community number (EINECS/ELINCS); unique identifier in EU chemical inventory | Lead: 231-100-4 |
| **Substance name** | IUPAC name or common name | "Lead"; "Bis(2-ethylhexyl) phthalate"; "Perfluorooctanoic acid" |
| **Material class** | Category of material | Metal; polymer; ceramic; composite; coating; adhesive |

### 3.3 Data Collection Process

| Step | Action | Tools/Systems |
|:----:|--------|---------------|
| 1 | **Identify BOM** — complete Bill of Materials with all components, materials, packaging | ERP/PLM system (SAP, Oracle, Windchill, Teamcenter) |
| 2 | **Request declarations from suppliers** — send request per IPC-1752A class required | Compliance platforms (BOMcheck, Assent, SiliconExpert, Z2Data); email; supplier portals |
| 3 | **Collect responses** — receive XML or platform-based declarations from each supplier | Same platforms; store in compliance database |
| 4 | **Validate declarations** — check completeness (mass balance ≥95%); CAS accuracy; threshold compliance; consistency | Automated validation (platform rules); manual review for anomalies |
| 5 | **Aggregate** — roll up component declarations to product level (assembly BOM × component compositions = product composition) | Compliance platform calculation; custom tools |
| 6 | **Screen against regulations** — compare aggregated composition against regulatory substance lists (RoHS, REACH SVHC, customer banned lists) | Automated screening; substance list databases (GADSL, REACH SVHC, etc.) |
| 7 | **Report/Declare** — generate product-level declaration for customers; submit regulatory notifications (SCIP); flag non-compliances | Declaration generation (IPC-1752 XML); SCIP submission (IUCLID); customer response |
| 8 | **Maintain** — update declarations when: substances added to SVHC list; BOM changes; supplier updates declaration; regulations change | Change management process; periodic refresh (annual minimum) |

### 3.4 Compliance Data Platforms

| Platform | Focus | Features | Cost |
|----------|-------|----------|:----:|
| **BOMcheck** (Assent Compliance) | Electronics industry | IPC-1752 native; multi-regulation screening; SCIP support; supplier management; free for suppliers to declare | €€ (subscriber) |
| **Assent Compliance** | Broad compliance | Supply chain data collection; regulations (RoHS, REACH, conflict minerals); analytics; risk management | €€€ |
| **SiliconExpert** (Arrow) | Component data + compliance | Component lifecycle; datasheet; compliance status (RoHS/REACH); substance data; obsolescence | €€ |
| **Z2Data** | Component intelligence | Risk scoring; compliance; part cross-reference; regulatory monitoring | €€ |
| **IMDS** (DXC Technology) | Automotive | Full Material Disclosure (automotive standard); MDS creation/acceptance; recycling assessment | Free for OEM members; fee for suppliers |
| **CDX** (formerly BOMcheck-CDX) | Chemical data exchange | IPC-1752 format; substance data exchange between companies; node-to-node transfer | €€ |
| **iPoint** (now Sphera part) | Broad product compliance | Material declaration; LCA; product sustainability; supply chain management | €€€ |

---

## Chapter 4 — Implementation Guide

### 4.1 Setting Up a Material Declaration Program

| Phase | Duration | Activities | Deliverables |
|:-----:|:--------:|-----------|-------------|
| **1. Scope & Policy** | 1-2 months | Define which products need declarations; determine class required (A/B/C); identify regulations to cover; establish corporate restricted substance list (RSL); define declaration policy (acceptance criteria) | Corporate RSL; declaration policy; scope document |
| **2. System Selection** | 2-3 months | Evaluate and select compliance platform; integrate with ERP/PLM (BOM feed); configure regulatory substance lists; set up supplier portal | Platform selected and configured; integrations built |
| **3. Supplier Engagement** | 3-6 months | Communicate requirements to suppliers; train suppliers on declaration format/platform; establish timelines; prioritize by risk/volume | Supplier communication; training materials; priority list |
| **4. Data Collection** | Ongoing | Request and collect declarations from suppliers; validate quality; follow up on non-responses; escalate where needed | Declarations in system; coverage metrics |
| **5. Validation & Screening** | Ongoing | Validate mass balance; screen against regulations; identify non-compliances; work with suppliers on issues | Compliance reports; non-compliance resolution; alerts |
| **6. Reporting** | Ongoing | Generate declarations for customers; submit SCIP notifications; prepare technical documentation for RoHS/CE; respond to customer queries | Customer declarations; SCIP submissions; documentation |
| **7. Maintenance** | Ongoing | Monitor regulatory changes (new SVHCs; new restrictions); BOM change triggers; annual supplier refresh | Updated declarations; regulatory monitoring; change management |

### 4.2 Supplier Declaration Request Template

| Field | Content |
|-------|---------|
| Subject | Material Declaration Request — IPC-1752A Class [B/C] |
| Part numbers | [List of parts requiring declaration] |
| Standard | IPC-1752A [version]; declaration Class [B/C]; XML format preferred |
| Regulations covered | EU RoHS 2011/65/EU + Delegated Directive 2015/863; EU REACH SVHC (current candidate list); Customer Restricted Substance List (attached) |
| Threshold for reporting | Report all substances >0.1% w/w in homogeneous material (Class B); or all substances (Class C — Full Material Declaration) |
| Format | IPC-1752A XML; or platform submission via [BOMcheck/Assent/other] |
| Deadline | [Date; typically 30-60 days] |
| Mass balance requirement | ≥95% of total part mass must be declared (sum of reported substances ≥95%) |
| Special requirements | CAS numbers required for all reported substances; indicate REACH SVHC presence (Y/N); state RoHS compliance status |

### 4.3 Quality Checks for Received Declarations

| Check | Pass Criteria | Action if Failed |
|-------|:---:|------|
| **Mass balance** | Declared mass ≥ 95% of total part mass | Request updated declaration; identify missing materials |
| **CAS validity** | All CAS numbers are valid (exist in CAS registry) | Correct CAS numbers; request supplier correction |
| **Substance plausibility** | Declared substances match expected materials for that component type (e.g., solder should contain Sn) | Investigate; may indicate data error; request verification |
| **Concentration plausibility** | Concentrations within expected ranges (e.g., Pb in RoHS-exempt solder should be 35-40 wt%) | Verify with supplier; may indicate unit error |
| **Regulatory completeness** | All regulated substances are addressed (even if "not present") | Request confirmation of absence; cannot assume absence |
| **Currency** | Declaration date within last 2-3 years; or confirmation still valid | Request updated declaration |
| **Consistency** | Same part from same supplier should have consistent declarations over time | Investigate changes; confirm if reformulation occurred |

---

## Chapter 5 — Verification & Audit

### 5.1 Declaration Verification Methods

| Method | Description | When Used | Confidence |
|--------|-------------|-----------|:---:|
| **Documentary review** | Review supplier declaration against known specifications; check plausibility; verify CAS numbers | Routine; all parts | Medium |
| **Analytical testing (XRF)** | X-ray fluorescence screening: non-destructive; identifies elements and approximate concentrations | Incoming inspection; risk-based; RoHS screening | Medium-High (elements only; screening) |
| **Analytical testing (ICP-OES/MS)** | Inductively Coupled Plasma: destructive; accurate quantification of metals/elements | Verification of specific concerns; referee method for RoHS metals | High |
| **Analytical testing (GC-MS)** | Gas Chromatography-Mass Spectrometry: identifies organic substances (phthalates; brominated flame retardants) | Organic substance verification; phthalates in RoHS | High |
| **Supplier audit** | Visit supplier; review manufacturing process; verify material inputs; check process controls | High-risk suppliers; strategic parts; post-incident | High |
| **Third-party certification** | Supplier obtains third-party certification of compliance (e.g., IECQ QC 080000 HSPM; ISO 9001 with compliance scope) | Supply chain risk management; large programs | Medium-High |

### 5.2 IECQ QC 080000 — Hazardous Substance Process Management

| Aspect | Detail |
|--------|--------|
| **What** | Process management system standard for hazardous substance-free electronics manufacturing; certifiable |
| **Based on** | ISO 9001 quality management system (extension/add-on specifically for hazardous substance management) |
| **Scope** | Covers processes for ensuring products meet hazardous substance restrictions (RoHS, REACH, customer requirements) |
| **Key elements** | Substance management plan; supplier management; incoming material control; process control; testing/verification; customer communication; corrective action; continuous improvement |
| **Certification** | Third-party audit by IECQ-approved certification body; 3-year certificate with annual surveillance |
| **Benefit** | Demonstrates systematic approach to substance compliance; reduces need for individual customer audits; recognized globally (especially Asia-Pacific) |
| **Prevalence** | Very common among Asian electronics manufacturers (Taiwan, China, Korea, Japan); less common in Europe/US |

---

## Chapter 6 — Regional Context

### 6.1 Material Declaration Requirements by Region

| Region | Key Regulation | Declaration Need | Format |
|--------|---------------|:---:|:---:|
| **EU** | RoHS 2011/65/EU; REACH; SCIP | Demonstrate RoHS compliance (technical documentation); communicate SVHC >0.1% to customers; SCIP notification | IPC-1752A (industry); IEC 62474; IUCLID (SCIP) |
| **EU Automotive** | ELV Directive; REACH; IMDS requirement | Full Material Disclosure mandatory for all automotive components | IMDS (mandatory); IPC-1754 (supplementary) |
| **US** | Prop 65 (California); TSCA; state-level (NY, WA) | Know substances for Prop 65 warnings; TSCA compliance; state chemical reporting | IPC-1752A; various formats |
| **China** | China RoHS (SJ/T 11363; GB/T 26572); China REACH | Declare presence of RoHS substances; label; environmental use period (EFUP) | China-specific format + IPC-1752A for international companies |
| **Japan** | J-MOSS (JIS C 0950); chemSHERPA | Chemical content marking on electronics; industry data exchange | **chemSHERPA** (CI format — replaced JAMP AIS/MSDSplus) |
| **Korea** | K-RoHS (Act on Resource Circulation); K-REACH | RoHS substance restrictions; K-REACH registration/evaluation | Korean format; IPC-1752A for international |
| **Global** | Customer RSLs; industry initiatives (IEC 62474) | Supplier declarations for customer compliance programs | IPC-1752A; IEC 62474; platform-specific |

### 6.2 chemSHERPA (Japan)

| Aspect | Detail |
|--------|--------|
| **What** | Chemical information SHaring and Exchange under Reporting PArtnership in supply chain (Japan industry standard since 2018) |
| **Replaces** | JAMP AIS (Article Information Sheet) and JAMP MSDSplus |
| **Format** | Standardized data format (CI — Chemical Information format for articles; AI — for chemical products) |
| **Content** | Substance composition; compliance with regulations (J-MOSS, RoHS, REACH, etc.); uses IEC 62474 declarable substance list as basis |
| **Adoption** | Mandatory de-facto standard for Japan electronics supply chain; used by Sony, Panasonic, Canon, Toshiba, etc. |
| **Relationship to IPC-1752** | Different format but overlapping purpose; tools often convert between formats; IEC 62474 substance list shared |

### 6.3 SCIP Database (EU)

| Aspect | Detail |
|--------|--------|
| **Legal basis** | Waste Framework Directive (2008/98/EC), Article 9(1)(i) — amended by Directive 2018/851 |
| **Managed by** | ECHA (European Chemicals Agency) |
| **Requirement** | Suppliers of articles containing SVHCs (Substances of Very High Concern) >0.1% w/w (per article) must notify ECHA |
| **Who must notify** | EU manufacturers; EU importers; EU assemblers; downstream article suppliers |
| **Information** | Article identifier; SVHC name/CAS/EC; concentration range; article category (TARIC); safe use instructions |
| **Purpose** | Provide information to waste operators for better waste management; improve recycling; track SVHCs in articles |
| **Format** | IUCLID 6 format (System-to-System); or online simplified notification (SCIP format) |
| **Link to material declaration** | IPC-1752/IEC 62474 declarations provide the substance composition data needed to determine if SCIP notification is required |

---

## Chapter 7 — Comparison

### 7.1 Material Declaration Standards Comparison

| Feature | IPC-1752A | IEC 62474 | chemSHERPA | IMDS |
|---------|:---:|:---:|:---:|:---:|
| **Industry** | Electronics (broad) | Electrotechnical (IEC scope) | Electronics (Japan) | Automotive (global) |
| **Declaration classes** | A, B, C, D | DSL-based | Based on IEC 62474 DSL | Full material (always 100%) |
| **Full Material Disclosure** | Class C/D | Possible (request all) | Possible | **Required** (all materials) |
| **Substance list** | References external (RoHS, REACH, GADSL, etc.) | Maintains own DSL (Declarable Substance List) on 62474 database | Uses IEC 62474 DSL | GADSL (Global Automotive Declarable Substance List) |
| **Data format** | XML | XML | AI/CI format (proprietary) | IMDS format (web-based system) |
| **Exchange mechanism** | File exchange; platform | File exchange; platform | chemSHERPA tool (free) | IMDS system (web-based; account required) |
| **Automotive use** | IPC-1754 variant | Limited in automotive | Rare in automotive | **Standard** (mandatory for all automotive) |
| **Cost** | Standard purchase; free tools available | Standard purchase | Free tool | Free for OEM-chain users |
| **Strength** | Flexible; global electronics; widely supported by platforms | Internationally standardized (IEC); maintained DSL | Japan market coverage; free tool; IEC 62474 aligned | Automotive industry standard; comprehensive; centralized |

### 7.2 Declaration Class Selection Guide

| Situation | Recommended Class | Rationale |
|-----------|:---:|------|
| Simple commodity component; low risk (resistor, standard capacitor) | **A** | Basic compliance statement sufficient; substance-specific data not needed |
| Standard electronic component; RoHS + REACH compliance needed | **B** | Identifies restricted/declarable substances; meets most regulatory evidence needs |
| Automotive electronic component (to be used in vehicle) | **C** (or IMDS) | Automotive OEMs require Full Material Disclosure; IMDS mandatory; need 100% composition |
| Component for EU Battery Regulation product | **C or D** | Need recycled content tracking; complete composition for passport; future-proof |
| High-risk substance concern (new SVHC candidate; emerging restriction) | **C** | Full composition allows screening against ANY substance list (current and future) |
| Customer requests FMD (proactive compliance) | **C** | Complete data enables screening against any current or future regulation without re-requesting |
| Digital Product Passport preparation | **D** | Need material origin; recycled content; full composition for passport data |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 Material Declaration Data Flow in Supply Chain

```mermaid
graph LR
    subgraph "Tier 3+ Suppliers"
        T3A[Raw Material Producer<br/>━━━━━━━━━━━<br/>Metals; Chemicals;<br/>Polymers; Ceramics<br/>━━━━━━━━━━━<br/>Provides: Material Safety<br/>Data Sheet; substance content]
    end
    
    subgraph "Tier 2 Suppliers"
        T2A[Component Manufacturer<br/>━━━━━━━━━━━<br/>ICs; Capacitors;<br/>Connectors; PCBs<br/>━━━━━━━━━━━<br/>Provides: IPC-1752A<br/>Class B/C declaration]
    end
    
    subgraph "Tier 1 Supplier"
        T1[Assembly / Module Mfg<br/>━━━━━━━━━━━<br/>PCBA; Sub-assemblies<br/>━━━━━━━━━━━<br/>Aggregates: component<br/>declarations + own process<br/>materials → product declaration]
    end
    
    subgraph "OEM / Brand"
        OEM[Product Manufacturer<br/>━━━━━━━━━━━<br/>Final product assembly<br/>━━━━━━━━━━━<br/>Uses: Aggregated data for<br/>• RoHS technical documentation<br/>• REACH SVHC communication<br/>• SCIP notification<br/>• Customer declarations<br/>• Digital Product Passport]
    end
    
    subgraph "Regulatory / Customer"
        REG[Authorities & Customers<br/>━━━━━━━━━━━<br/>• Market surveillance (RoHS)<br/>• ECHA (SCIP database)<br/>• B2B customers<br/>• End consumers (DPP)]
    end
    
    T3A -->|SDS; Composition data| T2A
    T2A -->|IPC-1752A declarations| T1
    T1 -->|Product declarations| OEM
    OEM -->|Compliance evidence; SCIP; declarations| REG
```

### 8.2 Material Declaration Processing Workflow

```mermaid
sequenceDiagram
    participant BOM as ERP/PLM System
    participant COMP as Compliance Platform
    participant SUPP as Supplier
    participant ENG as Compliance Engineer
    participant REG as Regulatory Database
    
    BOM->>COMP: BOM data feed (parts, quantities, suppliers)
    COMP->>COMP: Identify parts needing declarations<br/>(new parts; expired declarations; BOM changes)
    COMP->>SUPP: Declaration request<br/>(IPC-1752A Class B; part list; deadline)
    SUPP->>COMP: Submit declaration<br/>(XML or platform entry)
    COMP->>COMP: Auto-validate<br/>(mass balance; CAS check; completeness)
    
    alt Validation passed
        COMP->>REG: Screen against substance lists<br/>(RoHS; REACH SVHC; customer RSL)
        REG->>COMP: Screening results
        
        alt Compliant
            COMP->>ENG: Status: COMPLIANT ✓
        else Non-compliant detected
            COMP->>ENG: ALERT: Substance exceedance detected
            ENG->>SUPP: Investigate; request correction/confirmation
        end
    else Validation failed
        COMP->>SUPP: Rejection: [reason]<br/>(mass balance <95%; invalid CAS; etc.)
        SUPP->>COMP: Corrected declaration
    end
    
    ENG->>COMP: Generate customer declaration<br/>(aggregate BOM-level; IPC-1752A output)
    COMP->>BOM: Compliance status flag per part
```

### 8.3 Full Material Declaration Example (Component)

```mermaid
graph TB
    subgraph "MLCC Capacitor — Full Material Disclosure (Class C)"
        PART[Part: MLCC 100nF 50V 0805<br/>Total mass: 25.0 mg (100%)]
        
        subgraph "Ceramic Dielectric (55%)"
            D1[BaTiO₃ - Barium Titanate<br/>CAS: 12047-27-7<br/>13.0 mg (52%)]
            D2[Additives (Nd₂O₃, MgO, etc.)<br/>0.75 mg (3%)]
        end
        
        subgraph "Electrode (Internal) (20%)"
            E1[Nickel<br/>CAS: 7440-02-0<br/>5.0 mg (20%)]
        end
        
        subgraph "Termination (External) (15%)"
            T1[Copper<br/>CAS: 7440-50-8<br/>2.5 mg (10%)]
            T2[Nickel barrier<br/>CAS: 7440-02-0<br/>0.75 mg (3%)]
            T3[Tin (Sn) plating<br/>CAS: 7440-31-5<br/>0.5 mg (2%)]
        end
        
        subgraph "Epoxy Coating (10%)"
            EP[Epoxy resin<br/>CAS: 25068-38-6<br/>2.5 mg (10%)]
        end
        
        PART --> D1 & D2 & E1 & T1 & T2 & T3 & EP
    end
    
    style PART fill:#f9f,stroke:#333
```

---

## Chapter 9 — Case Studies

### 9.1 Case Study: REACH SVHC Candidate List Update — Supply Chain Impact

| Aspect | Detail |
|--------|--------|
| Scenario | ECHA adds new substance to SVHC Candidate List (e.g., medium-chain chlorinated paraffins, MCCPs; CAS 85535-85-9); electronics manufacturer must determine if any products are affected |
| Company | Electronics manufacturer (industrial control systems); 2,000 active part numbers; 300 suppliers |
| Without FMD | Must contact ALL suppliers asking "do any of your products contain MCCPs >0.1% w/w?" → 300 requests; 60-90% response rate initially; months to get complete picture; unknown exposure in the meantime |
| With FMD (Class C) | Already have full material composition for all parts; immediately screen all declarations against new CAS number; within HOURS identify: 12 parts potentially containing MCCPs (flame retardant additive in certain cable insulations and flexible plastic parts); immediately know which products are affected; contact only 5 relevant suppliers for confirmation |
| Outcome | With FMD: identified affected parts in <1 day; confirmed 4 parts actually contain MCCP above threshold; initiated SCIP notification for 2 products containing those parts; communicated to customers within 2 weeks. Without FMD: would have taken 3-6 months to establish same understanding. |
| Cost-benefit | FMD data collection costs more upfront (supplier effort; validation); but saves enormous effort on EVERY new regulatory change. With 2-4 SVHC additions per year (current rate), FMD pays for itself within 2 years for companies with >500 parts. |

### 9.2 Case Study: SCIP Database Compliance

| Aspect | Detail |
|--------|--------|
| Company | Electronic connector manufacturer; produces 500 connector types; sells to EU industrial customers (Tier 1 to machinery OEMs) |
| Obligation | As EU-based manufacturer of articles: must notify ECHA via SCIP for any connector article containing SVHC >0.1% w/w |
| Challenge | Must determine SVHC content per ARTICLE (not per product). A connector has multiple articles (housing, pins, spring, seal). Each article assessed separately. Some connectors have >10 individual articles. Total ~3,000 articles across portfolio. |
| Approach | (1) Mapped connector designs to article structure (housing = 1 article; each pin type = 1 article; seal = 1 article; etc.). (2) Used existing IPC-1752A Class B declarations from material suppliers to identify SVHC content. (3) Identified: lead in copper alloy (CuZn39Pb3) free-cutting brass pins → lead is SVHC; concentration in brass alloy = 3% w/w (well above 0.1% in that homogeneous material). (4) Approximately 200 pin articles use leaded brass → all require SCIP notification. (5) Created SCIP submissions using IUCLID format; batch upload for 200 articles. (6) Also communicated downstream: customers informed per REACH Art 33 that connectors with brass pins contain lead (SVHC) >0.1%. |
| Resolution | Submitted SCIP notifications (compliant); parallel project to qualify lead-free alternatives (CuNi-Si alloys, CuZn without Pb) for pin materials → 18-month qualification program to eliminate SVHC from most connector types. |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **Digital Product Passport (DPP)** integration | 2025-2030 | Material declarations become core data feed for EU DPP; IPC-1752/IEC 62474 data mapped to DPP requirements; standardized APIs for data exchange |
| **Full Material Disclosure as default** | Now-2027 | Moving from Class B (minimum) to Class C/D (FMD) as standard expectation; driven by circular economy needs (recyclers need to know ALL materials) |
| **Recycled content tracking** | 2025-2031 | EU Battery Regulation + ESPR require recycled content declaration → material declarations must include material origin/source information (Class D) |
| **Automated BOM-to-declaration** | Now | AI/ML tools auto-generate preliminary declarations from component datasheets + known material compositions; reduce manual supplier effort |
| **Blockchain/distributed ledger** | 2025+ | Immutable material declaration trail through supply chain; prevent data falsification; enable trustless verification |
| **PFAS universal restriction** | 2025-2027 | If EU PFAS restriction enacted: massive material declaration campaign needed; FMD essential for identifying all PFAS-containing materials |
| **Substance list expansion** | Ongoing | SVHC Candidate List growing (~2-4 substances/year); increasing regulatory substance lists globally; FMD approach becomes essential to keep up |
| **Harmonization efforts** | 2024-2026 | IPC-1752, IEC 62474, chemSHERPA converging in data model; tools increasingly interoperable; reduce supplier burden of multiple formats |
| **Real-time compliance monitoring** | Now | Continuous screening against updated substance lists; immediate alerts when new regulations affect existing declarations |
| **Supply chain transparency** | 2025+ | Tier N visibility (beyond Tier 1); due diligence requirements drive deeper material origin tracking; links to conflict minerals and carbon footprint |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is IPC-1752A and what are its declaration classes?  
**A:** IPC-1752A is an industry standard for material declarations in the electronics supply chain. It defines a structured format (XML-based) for suppliers to communicate the material composition of their products to customers. It has four declaration classes: **Class A** (basic — just a compliance statement, e.g., "this part is RoHS compliant"); **Class B** (detailed — reports specific declarable/restricted substances with CAS numbers and concentrations; this is the most common level); **Class C** (Full Material Declaration / FMD — reports ALL materials and substances in the product, not just restricted ones; declares 100% of the composition); **Class D** (Advanced FMD — Class C plus additional information like recycled content, material origin, REACH registration status). Higher classes provide more data but require more effort from suppliers. The trend is toward Class C/D because regulatory lists keep expanding — if you have full composition data, you can screen against ANY new restriction immediately without going back to suppliers.

**Q2:** What is a "homogeneous material" in the context of material declarations?  
**A:** A homogeneous material is a material of uniform composition throughout that cannot be mechanically separated into different materials. Think of it as the most basic material unit. Examples: the tin plating on a connector pin (pure tin layer); the copper conductor in a wire; the FR-4 epoxy resin in a PCB laminate; the solder alloy (Sn/Ag/Cu); the plastic (e.g., PBT) of a connector housing. This concept is important because RoHS substance thresholds are measured per HOMOGENEOUS MATERIAL, not per whole product. For example, RoHS allows <1000 ppm lead in homogeneous materials — so if you have a brass alloy pin with 3% lead (30,000 ppm), that homogeneous material exceeds the threshold (unless an exemption applies), even though the lead content of the entire connector might be only 500 ppm when averaged across all materials. Material declarations must identify substances at the homogeneous material level for regulatory compliance.

### Tier 2: Mid-Level

**Q3:** How would you handle a situation where 30% of your supply chain is not providing material declarations?  
**A:** [Detailed answer covering: (1) Prioritize by risk: classify the non-responsive 30% by risk level (volume; substance risk; regulatory exposure); focus on highest-risk first. (2) Escalate strategically: start with purchasing leverage (commercial consequence for non-response); include declaration as part of supplier agreement/terms; make it a supplier qualification requirement. (3) Alternative data sources: for standard components (passives, ICs from major manufacturers), use component databases (SiliconExpert, Z2Data) which may already have compliance/substance data; use industry-average compositions for known material types as interim data (with clear flagging as estimates). (4) Analytical testing: for highest-risk parts where suppliers won't respond, conduct XRF/ICP screening to verify at least RoHS compliance; this provides assurance but not full declaration. (5) Supplier development: offer training/support to smaller suppliers unfamiliar with IPC-1752; provide templates; offer free compliance platforms (BOMcheck is free for declaring suppliers). (6) Second-source strategy: for persistently non-responsive suppliers, qualify alternative sources who can provide declarations. (7) Track and report: maintain metrics (% coverage; response rate by supplier; time-to-respond); report to management; set targets (e.g., 95% coverage within 12 months). (8) Accept residual risk with mitigation: for very minor parts from suppliers who truly cannot provide data, document the risk acceptance; apply conservative assumptions (worst case); re-evaluate periodically.]

### Tier 3: Senior

**Q4:** Design a material declaration and compliance data architecture that supports current regulations (RoHS, REACH, SCIP) AND prepares for EU Digital Product Passport, EU Battery Regulation recycled content, and potential PFAS restriction — for a company with 10,000 part numbers and 1,000 suppliers.  
**A:** [Comprehensive answer covering: system architecture (central compliance platform + ERP/PLM integration + supplier portal + regulatory intelligence feed); data model (IPC-1752A Class D as target; substance-level + material origin + recycled content fields); migration strategy from current Class B baseline to Class C/D (phased by criticality); automation (BOM-triggered declaration requests; auto-validation; auto-screening against live substance lists); DPP preparation (material composition data mapped to DPP schema; API-ready for future DPP platform integration); recycled content tracking (Mass Balance or Segregated approach; chain of custody from recycled material supplier through processing to final component); PFAS preparation (immediate screening of current FMD data for known PFAS CAS numbers; flag all fluoropolymer/fluorinated materials; prepare substitution roadmap); governance (data quality KPIs; coverage targets; annual certification cycle; audit program); team and budget (2-3 FTE compliance engineers + 0.5 FTE IT + platform license + testing budget ≈ €400K/year).]

---

## Chapter 12 — Cheat Sheet & Quick Reference

### Material Declaration Decision Tree

```
1. IS THE PART FOR AUTOMOTIVE APPLICATION?
   → YES: Full Material Declaration required (IMDS mandatory)
         Use: IMDS or IPC-1754 (Class C equivalent)
   → NO: Continue to step 2

2. WHAT REGULATORY REQUIREMENTS APPLY?
   → RoHS only (simple compliance): Class A may suffice
     (but Class B preferred for evidence)
   → RoHS + REACH SVHC: Class B minimum (need substance-level data)
   → Multiple regulations + future-proofing: Class C recommended (FMD)
   → EU Battery Reg / DPP / recycled content: Class D (advanced FMD)

3. WHAT IS THE RISK LEVEL OF THE COMPONENT?
   → LOW risk (standard digital IC from major manufacturer; well-characterized):
     Class B acceptable
   → MEDIUM risk (specialty materials; mixed compositions; multiple material types):
     Class B with enhanced validation
   → HIGH risk (unknown supplier; complex composition; uses materials of concern):
     Class C (FMD) + analytical verification
```

### Key Data Fields Required

```
CLASS A (Basic):
□ Supplier name + contact
□ Part number + description
□ RoHS compliance statement (Y/N)
□ REACH SVHC present >0.1%? (Y/N)
□ If exempt: exemption reference

CLASS B (Detailed) — adds:
□ List of reportable substances
□ CAS number per substance
□ Concentration (ppm or wt%) per homogeneous material
□ Homogeneous material identification
□ Mass of part

CLASS C (FMD) — adds:
□ ALL substances (not just restricted)
□ ALL homogeneous materials listed
□ Complete mass balance (≥95%)
□ Material classification per material

CLASS D (Advanced) — adds:
□ Recycled content per material (%)
□ Material origin/source country
□ REACH registration status
□ Renewable content
□ Material supplier identity
```

### Common Pitfalls

```
1. THRESHOLD CONFUSION: RoHS threshold (0.1% = 1000 ppm) is per
   HOMOGENEOUS MATERIAL, not per whole component or product.
   → Always report at homogeneous material level.

2. MASS BALANCE: Declarations accounting for <95% of total mass
   are unreliable. Missing mass = unknown composition = risk.
   → Require ≥95% mass balance; ideal ≥99%.

3. "NOT PRESENT" vs. "NOT TESTED": Very different meanings.
   "Not present" = positive assertion (substance is absent).
   "Not tested" = unknown (could be present).
   → Require positive assertions; don't accept ambiguity.

4. CAS NUMBER ACCURACY: Wrong CAS = wrong substance identified.
   → Validate all CAS numbers against CAS registry.
   → Common error: CAS for oxide vs. metal form.

5. ARTICLE vs. MIXTURE: SCIP/REACH require per-article assessment.
   A product has multiple articles. Don't average across whole product.
   → Map product to article structure; assess per article.

6. EXPIRY: Declarations become stale. Substance lists update biannually.
   → Refresh declarations minimum every 2-3 years or on BOM change.
   → Re-screen against updated substance lists upon every update.
```

---

*End of Document — 11_Material_Declaration_IPC1752.md*
