# REACH SVHC Management — Registration, Evaluation, Authorisation & Restriction of Chemicals

**Topic:** EU REACH regulation compliance for product manufacturers — SVHC identification, communication obligations, authorization, restriction compliance, and SCIP database notification  
**Standard:** EC Regulation 1907/2006 (REACH); ECHA Guidance on Substances in Articles; SCIP Database requirements  
**SDO:** European Chemicals Agency (ECHA); European Commission DG ENV/DG GROW  
**Audience:** Chemical compliance specialists, product stewardship engineers, supply chain managers, regulatory affairs professionals, materials engineers  
**Prerequisites:** Basic chemistry, EU product regulatory framework, supply chain management concepts

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 1967 | EU Dangerous Substances Directive (67/548/EEC) | First EU chemical classification system |
| 1976 | Marketing and Use Directive (76/769/EEC) | First EU chemical restriction mechanism |
| 1981 | Existing substances: >100,000 chemicals grandfathered without safety data | The "data gap" problem |
| 1998 | EU Commission identifies chemical regulation failure | Existing chemicals rarely assessed; system broken |
| 2001 | EU White Paper "Strategy for a Future Chemicals Policy" | REACH concept proposed; "no data, no market" principle |
| 2003 | REACH proposal published by Commission | Industry lobbying begins; 6-year legislative process |
| 2006 | **REACH Regulation (EC 1907/2006) adopted** (December 18) | Most comprehensive chemical regulation globally; 849 pages |
| 2007 | ECHA (European Chemicals Agency) established in Helsinki | Central administration of REACH; helpdesk; guidance |
| 2008 | REACH enters into force (June 1); first SVHC candidate list (15 substances) | Pre-registration window opens for existing substances |
| 2010 | First registration deadline (>1000 t/yr substances) | ~25,000 registrations; heaviest industry burden |
| 2013 | Second registration deadline (>100 t/yr) | ~3,000 substances registered |
| 2018 | **Final registration deadline** (>1 t/yr) | All chemicals >1 t/yr must now be registered; "no data, no market" fully enforced |
| 2019 | EU Waste Framework Directive amendment → SCIP database requirement | Articles containing SVHC >0.1% must be notified to SCIP |
| 2021 | **SCIP database mandatory** (January 5) | All suppliers of articles with SVHC >0.1% must submit SCIP notification |
| 2022 | REACH revision proposed (Chemicals Strategy for Sustainability; CSS) | Expansion of "essential use" concept; more substances to be restricted; mixture assessment factor |
| 2023 | SVHC candidate list grows to 235 substances (June 2023 update) | Continuous additions (~2 per year in batches) |
| 2024 | Candidate list: 240+ substances; Universal PFAS restriction proposal (Annex XV) | Largest-ever single restriction proposal |
| 2025 | REACH revision ongoing; PFAS restriction decision expected | Potential fundamental changes to REACH framework |

### 1.2 REACH Conceptual Framework

```mermaid
graph TB
    subgraph "REACH Core Processes"
        REG[REGISTRATION<br/>(Title II)<br/>━━━━━━━━━━━<br/>Manufacturers/importers<br/>>1 tonne/year<br/>Must register with ECHA<br/>"No data, no market"]
        
        EVAL[EVALUATION<br/>(Title VI)<br/>━━━━━━━━━━━<br/>ECHA evaluates dossiers<br/>• Compliance check<br/>• Substance evaluation<br/>• Testing proposals]
        
        AUTH[AUTHORISATION<br/>(Title VII)<br/>━━━━━━━━━━━<br/>Annex XIV substances<br/>Cannot be used without<br/>authorization from ECHA<br/>Must prove: adequate control<br/>OR socioeconomic benefit]
        
        REST[RESTRICTION<br/>(Title VIII)<br/>━━━━━━━━━━━<br/>Annex XVII<br/>Conditions of use restricted<br/>Applies to all actors<br/>in supply chain]
    end
    
    subgraph "Information in Supply Chain (Title IV)"
        SDS[Safety Data Sheet<br/>(Article 31)<br/>━━━━━━━━━━━<br/>For chemical substances<br/>and mixtures]
        
        ART33[Article 33<br/>Communication Duty<br/>━━━━━━━━━━━<br/>SVHC >0.1% in articles<br/>→ Inform downstream]
        
        ART7[Article 7(2)<br/>Notification Duty<br/>━━━━━━━━━━━<br/>SVHC >0.1% in articles<br/>AND >1 tonne/year<br/>→ Notify ECHA]
        
        SCIP_N[SCIP Database<br/>(WFD Art 9(1)(i))<br/>━━━━━━━━━━━<br/>SVHC >0.1% in articles<br/>→ Notify to SCIP<br/>(waste operators info)]
    end
    
    REG --> EVAL --> AUTH
    EVAL --> REST
    AUTH --> ART33
    REST --> ART33
    ART33 --> SCIP_N
    ART7 --> SCIP_N
```

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 Key REACH Definitions

| Term | REACH Definition | Practical Meaning |
|------|-----------------|-------------------|
| **Substance** | Chemical element and its compounds (natural or manufactured) | Pure chemical or UVCB (unknown/variable composition) |
| **Mixture** | Mix of 2+ substances (not a reaction product) | Paints, adhesives, cleaning products, inks |
| **Article** | Object with specific shape/surface/design determining function MORE than chemical composition | Electronic device; cable; connector; PCB; housing; screw |
| **SVHC** | Substance of Very High Concern (Article 57 criteria) | Carcinogenic; mutagenic; toxic to reproduction (CMR); PBT; vPvB; endocrine disruptor; equivalent concern |
| **Candidate List** | List of SVHCs identified per Article 59 | Currently 240+ substances; updated ~2×/year |
| **Authorization List** (Annex XIV) | SVHCs that cannot be used without authorization | Subset of candidate list; sunset dates apply |
| **Restriction List** (Annex XVII) | Conditions under which substances are restricted | Specific uses/concentrations banned; applies to all |
| **0.1% w/w** | SVHC concentration threshold in article | Per article (as produced or imported); NOT per homogeneous material (different from RoHS!) |

### 2.2 SVHC Categories (Article 57 Criteria)

| Category | Criteria (Article 57) | Examples |
|----------|:---:|---------|
| **CMR** (Cat 1A/1B) | Carcinogenic; Mutagenic; Toxic to Reproduction per CLP Regulation | Lead compounds; cadmium compounds; formaldehyde; cobalt dichloride |
| **PBT** | Persistent + Bioaccumulative + Toxic (per Annex XIII criteria) | Short-chain chlorinated paraffins; some flame retardants; PFOS |
| **vPvB** | very Persistent + very Bioaccumulative | Certain PFAS; musk xylene; D4/D5 siloxanes |
| **Endocrine disruptor** | Disrupts hormone systems (equivalent level of concern per Art 57(f)) | Bisphenol A (BPA); DEHP; some phthalates; 4-nonylphenol |
| **Equivalent concern** (Art 57(f)) | Other properties meeting "equivalent level of concern" | Sensitizers; specific organ toxicants; combination effects |

### 2.3 REACH Obligations by Actor

| Actor | Registration | SDS | Article 33 | Article 7(2) | SCIP | Restriction |
|-------|:---:|:---:|:---:|:---:|:---:|:---:|
| Chemical manufacturer (EU) >1t/yr | **Yes** | Provide | N/A | N/A | N/A | Must comply |
| Chemical importer (EU) >1t/yr | **Yes** | Provide | N/A | N/A | N/A | Must comply |
| Downstream user (formulator) | No (but may report uses) | Receive + pass | N/A | N/A | N/A | Must comply |
| **Article manufacturer (EU)** | Only if substance released intentionally | N/A | **Yes** (if SVHC >0.1%) | **Yes** (if >0.1% AND >1t/yr) | **Yes** | Must comply |
| **Article importer (EU)** | Only if substance released intentionally | N/A | **Yes** | **Yes** | **Yes** | Must comply |
| Distributor | No | Pass along | **Yes** (pass info) | No | **Yes** | Must comply |

---

## Chapter 3 — Technical Deep Dive

### 3.1 Article 33 — Communication Duty

| Aspect | Requirement | Detail |
|--------|-------------|--------|
| **Trigger** | Article contains SVHC from candidate list at concentration >0.1% w/w | Applies on the day of candidate list inclusion |
| **To professional customers** | Provide information automatically (proactive) | Sufficient information to allow safe use; at minimum: name of SVHC |
| **To consumers** | Provide information on request | Within 45 days; free of charge; at minimum: name of SVHC |
| **"Article" interpretation** | Per article as supplied/produced (ECHA "once an article, always an article" — complex object = multiple articles) | Each component article within a complex object assessed individually; 0.1% per component article |
| **Calculation** | $\text{Concentration} = \frac{\text{Mass of SVHC in article}}{\text{Total mass of that article}} \times 100\%$ | Mass-based; per individual article within complex object |
| **What info to provide** | At minimum: name of SVHC; enough info for safe use (handling, end-of-life) | Best practice: CAS number; location in product; safe handling instructions; disposal guidance |
| **Timing** | Immediately upon candidate list update (no transition period) | Must monitor candidate list; process for rapid assessment needed |

### 3.2 SCIP Database Notification

| Aspect | Requirement |
|--------|-------------|
| **What** | SCIP = Substances of Concern In articles as such or in complex objects (Products) |
| **Legal basis** | Waste Framework Directive (2008/98/EC as amended), Article 9(1)(i) + (2) |
| **Who must notify** | Any supplier (manufacturer, importer, distributor) of articles containing SVHC >0.1% w/w placed on EU market |
| **When** | From January 5, 2021 (mandatory) |
| **What to submit** | Article identification (name, identifiers); SVHC identity (name, CAS, EC number); concentration range; article category (TARIC code); safe use/disassembly instructions |
| **How** | ECHA SCIP notification tool (online portal or system-to-system via IUCLID format) |
| **Purpose** | Inform waste operators about SVHCs in articles → enable safe recycling and waste treatment |
| **Complexity** | For complex products (electronics), must declare at component-article level → can require 100s of notifications per product |

### 3.3 Candidate List Assessment Process

```mermaid
flowchart TB
    UPDATE[ECHA Publishes Candidate<br/>List Update<br/>━━━━━━━━━━━<br/>New SVHCs added<br/>(typically June + January)]
    
    SCREEN[Screen Against Product Portfolio<br/>━━━━━━━━━━━<br/>• Check new SVHCs vs. material declarations<br/>• Check supply chain data<br/>• Search FMD database for substance<br/>• Identify potentially affected products]
    
    ASSESS{New SVHC<br/>present >0.1% in<br/>any article?}
    
    NO_IMPACT[No Impact<br/>━━━━━━━━━━━<br/>• Document assessment<br/>• Record in compliance file<br/>• Monitor for future changes]
    
    IDENTIFY[Identify Affected Products<br/>━━━━━━━━━━━<br/>• Which product(s)?<br/>• Which component article(s)?<br/>• What concentration?<br/>• What volume (>1t/yr total?)]
    
    ART33_ACT[Trigger Article 33<br/>━━━━━━━━━━━<br/>• Notify professional customers<br/>• Prepare consumer response<br/>• Update product documentation<br/>• Communicate safe use info]
    
    VOLUME{>1 tonne/year<br/>of that SVHC<br/>in articles?}
    
    ART7_ACT[Trigger Article 7(2)<br/>━━━━━━━━━━━<br/>• Notify ECHA within 6 months<br/>• Provide: substance ID; tonnage;<br/>  use description; exposure info]
    
    SCIP_ACT[SCIP Notification<br/>━━━━━━━━━━━<br/>• Submit to SCIP database<br/>• Per article containing SVHC<br/>• Include safe use info<br/>• Update upon product changes]
    
    SUBSTITUTE[Substitution Assessment<br/>━━━━━━━━━━━<br/>• Technical alternatives?<br/>• Performance comparison?<br/>• Cost-benefit?<br/>• Timeline for phase-out?<br/>• Track Annex XIV progress]
    
    UPDATE --> SCREEN --> ASSESS
    ASSESS -->|No| NO_IMPACT
    ASSESS -->|Yes| IDENTIFY
    IDENTIFY --> ART33_ACT
    IDENTIFY --> VOLUME
    VOLUME -->|Yes| ART7_ACT
    VOLUME -->|No| SCIP_ACT
    ART7_ACT --> SCIP_ACT
    SCIP_ACT --> SUBSTITUTE
    ART33_ACT --> SUBSTITUTE
```

### 3.4 Authorization Process (Annex XIV)

| Phase | Action | Timeline |
|:-----:|--------|----------|
| **Inclusion** | SVHC moved from candidate list to Annex XIV (authorization list) | Commission decision; substance-specific |
| **Application deadline** | Date by which authorization application must be submitted to ECHA | Typically 18-24 months before sunset date |
| **Sunset date** | Date after which substance cannot be used/placed on market without authorization | Substance-specific; published in Annex XIV |
| **Application content** | Analysis of alternatives (AoA); substitution plan; socioeconomic analysis (SEA); chemical safety report (CSR) | Extensive dossier; cost €50K-€500K+ |
| **ECHA evaluation** | RAC (Risk Assessment Committee) + SEAC (Socio-Economic Analysis Committee) opinions | 10-18 months |
| **Commission decision** | Grants or refuses authorization; may impose conditions; time-limited (review period) | After RAC/SEAC opinions |
| **Review** | Authorization holder must submit review report before review period expires | Typically 4-12 years |

---

## Chapter 4 — Implementation Guide

### 4.1 SVHC Compliance Program for Article Manufacturers

| Step | Action | Tools/Methods |
|:----:|--------|--------------|
| 1 | **Establish substance inventory** | Collect Full Material Declarations (IPC-1752A) from all suppliers; build internal material database |
| 2 | **Map candidate list to products** | Cross-reference candidate list substances with material database; identify presence per article |
| 3 | **Calculate concentrations** | For each SVHC identified: mass of SVHC / mass of article × 100%; compare to 0.1% threshold |
| 4 | **Assess obligations** | If >0.1%: Article 33 + SCIP triggered; if also >1t/yr: Article 7(2) triggered |
| 5 | **Communicate (Article 33)** | Proactively inform professional customers; prepare consumer response; update product information |
| 6 | **Notify ECHA (Article 7.2)** | Submit notification via REACH-IT (if >1t/yr SVHC in articles) |
| 7 | **Submit SCIP notification** | Use ECHA SCIP portal or IUCLID system-to-system; per article containing SVHC |
| 8 | **Monitor ongoing** | Subscribe to ECHA candidate list updates; re-assess with each update; track Annex XIV movements |
| 9 | **Substitution roadmap** | For each SVHC present: assess alternatives; timeline; prioritize based on Annex XIV progression risk |

### 4.2 Supply Chain Data Collection Strategy

| Tier | Method | Coverage | Quality |
|:----:|--------|:--------:|:-------:|
| **Tier 1** (Best) | Full Material Declaration (FMD) per IPC-1752A | All substances; exact composition | High |
| **Tier 2** | Substance-specific declaration (only SVHC candidate list) | SVHCs only; yes/no per substance | Medium |
| **Tier 3** | Generic RoHS/REACH compliance statement | Limited; only confirms general compliance | Low |
| **Tier 4** | Industry databases (BOMcheck; Assent; Material Compliance) | Varies by component | Variable |

**Recommended approach:**
- Critical/high-risk components (flexible polymers, coatings, flame-retarded materials): Tier 1 (FMD)
- Standard components (metals, ceramics, rigid plastics): Tier 2 (SVHC declaration)
- Low-risk components (pure metals, glass): Tier 3 acceptable with periodic verification
- Supplement all with periodic analytical testing (GC-MS, ICP) for verification

### 4.3 SCIP Notification Practical Guide

| Element | Content | Source |
|---------|---------|--------|
| **Article name** | Clear identification of the article | Product catalog; BOM; naming convention |
| **Primary article identifier** | Article number, EAN, product code | Internal system; barcode |
| **Article category** | Combined Nomenclature (CN) code or TARIC code | EU customs classification |
| **SVHC** | Substance name, CAS number, EC number | ECHA candidate list |
| **Concentration range** | Predefined ranges (e.g., >0.1% to ≤0.3%; >0.3% to ≤1%; etc.) | Material declarations; analytical data |
| **Material/mixture category** | Category of material containing SVHC (e.g., plastic; metal; textile) | Material data |
| **Safe use instructions** | How to use/handle/dispose safely regarding SVHC content | Product safety assessment |
| **Complex object structure** | For complex products: hierarchical structure of component articles | BOM; product architecture |

---

## Chapter 5 — Regulatory Enforcement

### 5.1 REACH Enforcement Landscape

| Aspect | Detail |
|--------|--------|
| **Enforcement authority** | Member state competent authorities (not ECHA directly for market enforcement) |
| **ECHA role** | Registration dossier evaluation; SVHC identification; guidance; IT systems; coordination |
| **Market surveillance** | National inspectors can request Article 33 evidence; check SCIP compliance; verify restriction compliance |
| **Penalties** | Per member state national law; typically €5K-€1M+ per violation; criminal liability possible |
| **REACH-EN-FORCE projects** | Coordinated EU-wide enforcement projects (REF); annual focus areas (e.g., restrictions compliance; online sales; articles) |
| **Common violations** | Missing/inadequate Article 33 communication; non-compliance with Annex XVII restrictions; missing SCIP notifications; inadequate SDS |

### 5.2 Key Annex XVII Restrictions (Electronics-Relevant)

| Entry | Substance(s) | Restriction | Impact on Electronics |
|:-----:|-------------|-------------|----------------------|
| 23 | Cadmium and compounds | Limited in plastics, paints, plating (certain applications) | Cadmium plating restricted; some exemptions for safety-critical contacts |
| 27 | Nickel | Restricted in articles in prolonged skin contact (>0.5 µg/cm²/week release) | Watch straps; wearables; user-facing metal parts |
| 47 | Hexavalent chromium in cement | Max 2 ppm in cement | Not electronics-relevant (construction) |
| 50 | PAHs in rubber/plastic | Max 1 mg/kg (0.0001%) in consumer products with skin contact | Cable grips; keypads; handles; any rubber user-contact surface |
| 51 | DEHP, DBP, BBP, DIBP in plasticized materials | Restricted in toys/childcare; REACH restriction overlaps RoHS for electronics | PVC cables; housings; overlap with RoHS phthalate restriction |
| 63 | Lead in articles for consumer use | Max 0.05% in accessible parts (with exceptions) | Metal parts accessible to consumers; connectors; controls |
| 67 | BPA in thermal paper | Restricted in thermal paper receipts | Printers producing thermal paper output |
| 68 | PFOA and PFOA-related substances | Max 25 ppb PFOA; 1 mg/kg PFOA-related | Coatings; water/oil-repellent treatments; some soldering flux |
| 72 | Substances in tattoo inks/PMU | Specific limits | Not electronics |
| XX (proposed) | PFAS universal restriction | Potential ban on all PFAS with limited exemptions | Massive impact: O-rings; cable insulation; coatings; lubricants; solder masks |

---

## Chapter 6 — Regional Context

### 6.1 Chemical Regulations Comparison by Region

| Region | Framework | Substance Identification | Article Obligations | Key Difference from EU REACH |
|--------|-----------|:---:|:---:|---------------------------|
| **EU** | REACH (1907/2006) | SVHC candidate list (240+) | Article 33; Article 7(2); SCIP | Reference framework; most developed |
| **UK** | UK REACH (retained EU law post-Brexit) | UK SVHC list (separate from EU; initially identical) | Same obligations transposed | Diverging from EU; separate registrations needed; UK SVHC list managed by HSE |
| **South Korea** | K-REACH (Act on Registration and Evaluation of Chemical Substances) | Priority Existing Chemicals (PEC); restricted/prohibited | Similar article obligations developing | Separate registration required for importers >1t/yr; CMR substances prioritized |
| **Turkey** | KKDIK (Turkish REACH; aligned with EU REACH) | Turkish candidate list (aligned with EU) | Article obligations (aligned) | Same framework; separate registration with Turkish REACH |
| **US** | TSCA (Toxic Substances Control Act); state laws | EPA risk evaluations; TSCA inventory | Limited article obligations (reporting rules; TSCA §8) | No SVHC candidate list equivalent; less article-focused; more substance-manufacture-focused |
| **China** | China REACH (MEE Order 12; revised 2020) | Inventory of Existing Chemical Substances; Priority Chemicals | New substance notification; limited article obligations | Less developed for articles; registration for manufacturers/importers of chemicals |
| **Japan** | Chemical Substances Control Law (CSCL); PRTR Law | Class I/II specified substances | Limited article obligations | Evaluation/restriction focused; less supply chain communication |

### 6.2 REACH vs. RoHS for Articles

| Aspect | REACH | RoHS |
|--------|:---:|:---:|
| Scope (product type) | **ALL articles** on EU market | Only EEE (electronics) |
| Substances covered | 240+ SVHCs (candidate list) + restrictions (Annex XVII) | 10 substances |
| Threshold basis | Per **article** (as placed on market or as produced) | Per **homogeneous material** |
| Threshold value | 0.1% w/w (communication); varies (restrictions) | 0.1% (Cd: 0.01%) |
| If above threshold... | **Communicate** (Article 33); **Notify** (Article 7.2 if >1t); **SCIP** | **Banned** (cannot place on market; unless exempted) |
| Can you still sell? | **Yes** (with communication obligations met) | **No** (unless exempted) |
| CE marking affected? | No (REACH not part of CE) | Yes (RoHS is CE marking directive) |
| Penalty for breach | Fines; market access issues | Product withdrawal; CE marking invalidated |

---

## Chapter 7 — Comparison

### 7.1 REACH Candidate List vs. Authorization List vs. Restriction List

| Dimension | Candidate List (Art 59) | Authorization List (Annex XIV) | Restriction List (Annex XVII) |
|-----------|:---:|:---:|:---:|
| Number of substances | 240+ (growing) | ~60 entries (specific uses) | ~70 entries (specific conditions) |
| How substances get there | ECHA/member state SVHC proposal → public consultation → inclusion | Commission moves from candidate list → Annex XIV | ECHA/member state Annex XV restriction proposal → opinions → Commission decision |
| Effect on articles | Communication (Art 33); Notification (Art 7.2); SCIP | Cannot USE the substance without authorization (applies to manufacturers/users) | Specific conditions/concentrations restricted (applies to all) |
| For article suppliers | Inform customers; notify ECHA; SCIP | If SVHC in article due to authorized use → supply chain communication | Must ensure article complies with restriction conditions |
| Timeline pressure | Immediate (upon inclusion) | Sunset date (after which: no use without authorization) | Enforcement date in restriction entry |
| Exemptions | None (obligations are automatic) | Authorization can be granted (time-limited) | Specific derogations in restriction entry |

### 7.2 Chemical Compliance Software Comparison

| Platform | Strengths | Coverage | Best For |
|----------|-----------|----------|----------|
| **Assent Compliance** | Full material disclosure; supply chain portal; SCIP integration; regulatory monitoring | Global regulations; all substances | Large electronics/automotive OEMs; complex supply chains |
| **BOMcheck** | Free for suppliers; widely adopted in electronics industry; IPC-1752A support | RoHS; REACH; SCIP; TSCA | Electronics component suppliers; cost-effective |
| **Sphera (Product Compliance)** | Deep material database; LCA integration; environmental compliance | EU + global; integration with SAP | Manufacturing enterprises; SAP environments |
| **Substance Compliance Engine (iPoint)** | Automated screening; supply chain data collection; regulatory intelligence | Global chemical regulations | Automotive; complex products; high automation need |
| **Chemycal** (free ECHA tool) | SVHC screening; regulatory tracking; free to use | EU REACH/CLP only | Small companies; basic compliance tracking |
| **IMDS/CDX** (automotive) | Industry standard for automotive material declarations | Automotive REACH + ELV; GADSL | Automotive supply chain specifically |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 SVHC Lifecycle: From Identification to Substitution

```mermaid
graph TB
    subgraph "SVHC Identification"
        PROPOSE[SVHC Proposal<br/>(Member State or ECHA)<br/>━━━━━━━━━━━<br/>Annex XV dossier<br/>demonstrating SVHC criteria<br/>(CMR/PBT/vPvB/ED/equiv)]
        
        CONSULT[Public Consultation<br/>(45 days)<br/>━━━━━━━━━━━<br/>Stakeholder comments<br/>Industry can provide data<br/>on alternatives, uses, volumes]
        
        MSC[Member State Committee<br/>━━━━━━━━━━━<br/>Unanimous agreement<br/>(or Commission referral)]
        
        CANDIDATE[Added to Candidate List<br/>━━━━━━━━━━━<br/>SVHC status confirmed<br/>Article 33/7(2)/SCIP triggered]
    end
    
    subgraph "Regulatory Progression"
        PRIO[Prioritization for<br/>Authorization List<br/>━━━━━━━━━━━<br/>ECHA recommends substances<br/>for Annex XIV based on:<br/>• Inherent properties<br/>• Wide dispersive use<br/>• High volumes]
        
        ANNEX_XIV[Included in Annex XIV<br/>(Authorization List)<br/>━━━━━━━━━━━<br/>• Application deadline set<br/>• Sunset date set<br/>• Must apply for authorization<br/>  OR substitute]
        
        REST_PROP[Restriction Proposed<br/>(Annex XV dossier)<br/>━━━━━━━━━━━<br/>Alternative path:<br/>Restrict specific uses/conc.<br/>via Annex XVII]
    end
    
    subgraph "Industry Response"
        ASSESS_ALT[Assess Alternatives<br/>━━━━━━━━━━━<br/>• Technical feasibility<br/>• Economic viability<br/>• Risk comparison<br/>• Timeline]
        
        SUBSTITUTE_ACT[Substitute<br/>━━━━━━━━━━━<br/>• Replace SVHC<br/>• Qualify alternative<br/>• Update product<br/>• Update documentation]
        
        APPLY_AUTH[Apply for Authorization<br/>━━━━━━━━━━━<br/>• No adequate alternative yet<br/>• Demonstrate: adequate control<br/>  OR socioeconomic benefit<br/>• Cost: €50K-€500K+]
    end
    
    PROPOSE --> CONSULT --> MSC --> CANDIDATE
    CANDIDATE --> PRIO --> ANNEX_XIV
    CANDIDATE --> REST_PROP
    CANDIDATE --> ASSESS_ALT
    ANNEX_XIV --> ASSESS_ALT
    ASSESS_ALT -->|"Alternative found"| SUBSTITUTE_ACT
    ASSESS_ALT -->|"No alternative yet"| APPLY_AUTH
```

### 8.2 Complex Object SVHC Assessment

```mermaid
graph TB
    subgraph "Complex Product (e.g., Electronic Device)"
        PRODUCT[Product<br/>(Complex Object)<br/>━━━━━━━━━━━<br/>Total mass: 500g]
        
        PCB[PCB Assembly<br/>(Article 1)<br/>Mass: 80g<br/>━━━━━━━━━━━<br/>Contains lead in solder<br/>(exempt via RoHS;<br/>REACH Art 33: Pb compounds<br/>on candidate list → assess)]
        
        CABLE[Cable Assembly<br/>(Article 2)<br/>Mass: 30g<br/>━━━━━━━━━━━<br/>PVC insulation contains<br/>DEHP at 15% by weight<br/>→ SVHC >0.1% ✓<br/>→ Article 33 triggered]
        
        HOUSING[Plastic Housing<br/>(Article 3)<br/>Mass: 200g<br/>━━━━━━━━━━━<br/>ABS plastic; no SVHC<br/>identified above threshold<br/>→ No obligation]
        
        PSU[Power Supply Unit<br/>(Article 4)<br/>Mass: 150g<br/>━━━━━━━━━━━<br/>Contains capacitors with<br/>boric acid (on candidate list)<br/>at 0.05% of PSU mass<br/>→ Below 0.1% → No obligation]
        
        DISPLAY[Display Module<br/>(Article 5)<br/>Mass: 40g<br/>━━━━━━━━━━━<br/>Lead oxide in glass<br/>at 8% of glass (3g glass)<br/>→ 0.24g Pb / 40g article<br/>= 0.6% → >0.1% ✓<br/>→ Article 33 triggered]
    end
    
    PRODUCT --> PCB & CABLE & HOUSING & PSU & DISPLAY
    
    subgraph "Obligations Summary"
        OBL[This Product Requires:<br/>━━━━━━━━━━━<br/>1. Article 33 for CABLE (DEHP)<br/>2. Article 33 for DISPLAY (Lead oxide)<br/>3. SCIP notification for CABLE<br/>4. SCIP notification for DISPLAY<br/>5. Article 7(2) if >1t/yr of<br/>   DEHP or lead in articles<br/>━━━━━━━━━━━<br/>Communication to customers:<br/>"This product contains DEHP<br/>(CAS 117-81-7) in cable insulation<br/>and lead oxide (CAS 1317-36-8)<br/>in display glass"]
    end
    
    CABLE --> OBL
    DISPLAY --> OBL
```

---

## Chapter 9 — Case Studies

### 9.1 Case Study: SVHC Discovery in Supply Chain (Electronics OEM)

| Aspect | Detail |
|--------|--------|
| Company | Electronics OEM; 200+ product variants; 5,000+ components; 400 suppliers; selling globally |
| Trigger | January 2023 candidate list update: new SVHC added — a specific flame retardant (TBBPA derivative) commonly used in electronics PCB laminates |
| Initial assessment | Screening of material declarations identified 3 suppliers of PCB laminate materials potentially containing the substance. 60% of product portfolio uses PCBs from these suppliers. |
| Challenge | (1) Existing supplier declarations (IPC-1752A) did not specifically declare this substance (not on previous screening lists). (2) Needed rapid confirmation: is it present >0.1% in the PCB (as an article)? (3) PCB laminate typically contains 15-25% flame retardant by weight; if this specific SVHC is the FR used, concentration >> 0.1%. |
| Investigation | Contacted 3 laminate suppliers: Supplier A confirmed using TBBPA (not the SVHC derivative) — no impact. Supplier B confirmed using the listed SVHC derivative at ~18% in FR-4 laminate — SVHC >0.1% confirmed. Supplier C: initially unresponsive; sent formal inquiry with 30-day deadline. Analytical testing (GC-MS) of representative PCBs from Supplier B confirmed: 15.3% SVHC in laminate material. |
| Compliance actions | (1) **Article 33**: Issued SVHC communication to ALL customers receiving products containing Supplier B PCBs (within 45 days of candidate list publication). Template: "Product [model] contains [SVHC name, CAS number] in printed circuit board material at concentration >0.1% w/w. Safe use: the substance is encapsulated within the PCB laminate and not accessible during normal use. Disposal: treat as WEEE; do not incinerate." (2) **SCIP notification**: Submitted notifications for all affected product variants (85 products; batch upload via IUCLID). (3) **Article 7(2)**: Calculated annual volume: Supplier B PCBs → total SVHC mass in articles placed on EU market = ~3.2 tonnes/year → above 1 tonne threshold → ECHA notification filed within 6 months. |
| Substitution | Long-term: qualified alternative FR-4 laminate from Supplier A (using non-SVHC flame retardant). Transition timeline: 12 months (material qualification + reliability testing + design re-validation for high-reliability products). Cost: ~€500K (qualification, testing, documentation). After transition: all products SVHC-free for this substance. |
| Lessons | (1) Generic "REACH compliant" declarations insufficient; need substance-specific data. (2) PCB laminates are common SVHC source (flame retardants). (3) Rapid response capability needed (45-day consumer inquiry deadline). (4) SCIP notifications for complex products require structured BOM data + hierarchical article definition. (5) Dual-sourcing (different laminate suppliers) creates complexity — need consistent material specifications. |

### 9.2 Case Study: PFAS Restriction Impact Assessment

| Aspect | Detail |
|--------|--------|
| Scenario | EU universal PFAS restriction proposed (2023 Annex XV dossier by 5 member states); potential enforcement 2025-2027; broadest-ever restriction proposal covering ~10,000 PFAS substances |
| Affected applications (electronics) | (1) Fluoropolymer wire/cable insulation (PTFE, FEP, ETFE) — critical for high-temperature, high-frequency, aerospace/military. (2) O-rings and seals (FKM/Viton fluoroelastomers) — chemical resistance, temperature range. (3) Solder masks/flux (some contain fluorosurfactants). (4) Conformal coatings (fluorinated coatings for moisture protection). (5) Lubricants (PTFE-based dry lubricants on connectors, mechanisms). (6) Semiconductor manufacturing (PFAS in photolithography, etching chemicals). |
| Impact magnitude | Estimated 30-60% of electronic product portfolio uses at least one PFAS-containing material. Some applications have NO known alternatives (high-temp wire insulation; semiconductor chemicals). |
| Company response | (1) **Inventory**: identified all PFAS-containing materials in product portfolio (cross-reference with CAS numbers in PFAS definition). (2) **Risk categorization**: Critical (no alternative; product cannot function without) vs. Non-critical (alternatives exist; substitution feasible). (3) **Stakeholder consultation**: participated in public consultation on restriction proposal; submitted use-case data for derogation/transition period requests. (4) **Substitution R&D**: initiated projects for non-critical applications (alternative solder masks; alternative lubricants; silicone-based seals where temperature allows). (5) **Derogation strategy**: for critical applications (fluoropolymer insulation; semiconductor chemicals), prepared detailed technical justification for longer transition periods or essential-use derogations. |
| Current status | Restriction under ECHA evaluation (RAC + SEAC opinions expected 2024-2025); enforcement likely 2026-2027 with multi-year transition periods (5-12 years) for specific uses; semiconductor expected to receive derogation |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **PFAS universal restriction** | 2026-2027 (enforcement) | Potentially 10,000 substances restricted; 5-12 year transition periods for critical uses; €billions in substitution costs |
| **REACH revision (CSS)** | 2024-2026 (proposal/adoption) | "Essential use" concept; mixture assessment factor; extension to polymers; more substances restricted faster |
| **One substance, one assessment** | 2025+ | Harmonize hazard assessment across regulations (REACH, CLP, biocides, pesticides, food contact) |
| **Candidate list expansion** | Ongoing (2×/year) | Expect 10-20 new SVHCs per year; focus on endocrine disruptors; sensitizers; PMT/vPvM (persistent, mobile) |
| **Digital compliance** | Now | Machine-readable material declarations; automated SVHC screening; AI regulatory prediction; blockchain traceability |
| **Polymer registration** | 2025+ (CSS proposal) | Requiring registration of polymers (currently exempt); massive new registration burden |
| **Safe and Sustainable by Design (SSbD)** | Emerging | EU framework for chemicals/materials designed to be inherently safe throughout lifecycle; proactive vs. reactive regulation |
| **UK REACH divergence** | Ongoing | UK developing separate SVHC/restriction lists; separate registrations; potential double compliance burden for UK + EU market access |
| **Global PFAS regulations** | 2024-2030 | US EPA PFAS rules; state-level bans; Japanese restrictions; global trend toward PFAS elimination |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is a SVHC and what obligations does it create for article manufacturers?  
**A:** SVHC = Substance of Very High Concern, identified under REACH Article 57. A substance qualifies as SVHC if it is: Carcinogenic, Mutagenic, or Toxic to Reproduction (CMR Category 1A/1B); Persistent, Bioaccumulative, and Toxic (PBT); very Persistent and very Bioaccumulative (vPvB); or of equivalent concern (endocrine disruptors, sensitizers). SVHCs are placed on the "candidate list" by ECHA. For article manufacturers/importers/suppliers, if their article contains an SVHC from the candidate list at concentration >0.1% by weight: (1) Article 33: must proactively communicate to professional customers (name of SVHC + safe use info); must respond to consumer requests within 45 days; (2) Article 7(2): if total SVHC in articles placed on EU market >1 tonne/year, must notify ECHA; (3) SCIP: must submit notification to ECHA SCIP database (for waste operators). These obligations apply immediately upon candidate list inclusion — no transition period.

**Q2:** How do you determine if an SVHC exceeds 0.1% in an article?  
**A:** The calculation is: (mass of SVHC in the article) ÷ (total mass of that article) × 100%. Critically, for a complex product containing multiple component articles, you assess EACH component article separately (not the whole product). For example: a 500g product containing a 30g cable with DEHP at 15% of cable mass → DEHP concentration in cable article = 15% (well above 0.1%) → obligation triggered for this component article. The threshold is NOT: 4.5g DEHP / 500g total product = 0.9% (this is WRONG because you assess per article). You need material composition data from suppliers (IPC-1752A declarations) or analytical testing to determine the actual concentration.

### Tier 2: Mid-Level

**Q3:** How do you manage SCIP notifications for a complex electronic product with hundreds of components?  
**A:** [Detailed answer covering: hierarchical article structure in SCIP (complex object → component articles → SVHCs per article); data requirements per notification (article ID, SVHC identity, concentration range, CN code, safe use info); practical approaches for scale: (1) identify ALL component articles containing SVHC >0.1% from BOM + material declarations; (2) define article hierarchy (product → sub-assemblies → component articles); (3) use SCIP simplified notification for identical articles across products; (4) system-to-system integration (IUCLID format from compliance database to ECHA); (5) reference existing SCIP entries from suppliers (supplier simplification — supplier already notified their article); (6) tools: Assent/iPoint/BOMcheck can generate SCIP-format submissions from existing material data.]

### Tier 3: Senior

**Q4:** Your company sells 500 electronic products in the EU. The PFAS universal restriction is adopted with a 5-year transition. Design a strategic response plan.  
**A:** [Comprehensive answer covering: (1) Impact assessment: inventory ALL PFAS-containing materials across 500 products (fluoropolymers, fluoroelastomers, fluorosurfactants, PFAS-containing coatings/lubricants); estimate % of portfolio affected; categorize by criticality. (2) Technical substitution roadmap: for each PFAS use, assess alternatives (silicones, hydrocarbon polymers, non-fluorinated coatings); prioritize by feasibility and transition period; initiate qualification of alternatives starting Year 1. (3) Derogation/exemption strategy: for critical applications with no alternatives (high-temp insulation; semiconductor), prepare derogation applications with detailed technical justification and R&D roadmap. (4) Supply chain: work with material suppliers on PFAS-free alternatives; multi-source qualification; long-term supply agreements for transition materials. (5) Financial planning: budget for R&D, qualification, certification; estimate cost per product family; timeline for ROI. (6) Regulatory engagement: participate in industry consortia; provide data to ECHA; support derogation requests collectively. (7) Customer communication: inform customers of timeline and substitution plan; manage expectations for product changes.]

---

## Chapter 12 — Cheat Sheet & Quick Reference

### SVHC Obligation Decision Tree

```
1. Does your article contain a CANDIDATE LIST substance?
   → Check: ECHA candidate list (updated ~2×/year)
   → Check: supplier material declarations
   → Check: analytical testing (if uncertain)

2. Is concentration > 0.1% w/w (per article)?
   NO  → No REACH article obligation for this SVHC (but document assessment)
   YES → Continue ↓

3. OBLIGATIONS TRIGGERED:
   a) Article 33 (COMMUNICATION):
      • To professional customers: PROACTIVE (immediately; with delivery)
      • To consumers: ON REQUEST (within 45 days; free)
      • Minimum info: name of SVHC
      • Best practice: CAS number + safe use instructions
   
   b) SCIP NOTIFICATION:
      • Submit to ECHA SCIP database
      • Per article containing SVHC
      • Include: article ID; SVHC; concentration range; safe use
   
   c) Article 7(2) NOTIFICATION (conditional):
      IF total mass of that SVHC in ALL articles you place on EU market > 1 tonne/year:
      → Notify ECHA (REACH-IT) within 6 months of candidate list inclusion
```

### Key Differences: REACH vs. RoHS Threshold

```
                     REACH                          RoHS
Measured per:        ARTICLE (whole component)      HOMOGENEOUS MATERIAL (single layer)
Threshold:           0.1% w/w of article           0.1% (Cd: 0.01%) of homogeneous material
If exceeded:         COMMUNICATE (still sell)        BANNED (cannot sell)
Example:             A 100g connector               A connector's tin plating (0.5g)
                     with 0.15g SVHC                with 0.002g Pb
                     = 0.15% → triggered            = 0.4% → non-compliant (banned)
```

### Critical Timelines

```
Candidate list update → obligations begin:     IMMEDIATELY (day of publication)
Article 33 consumer response deadline:          45 days from request
Article 7(2) notification deadline:            6 months from candidate list inclusion
Annex XIV application deadline:                 Substance-specific (published)
Annex XIV sunset date:                          Substance-specific (after which: no use without auth.)
SCIP notification:                              Before placing article on EU market
Candidate list update frequency:                ~2× per year (January + June typically)
```

### Top SVHCs Found in Electronics

```
SVHC                          Common Location in Electronics        CAS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Lead (Pb)                     Solder; glass; ceramic; brass         7439-92-1
DEHP (phthalate)              PVC cables; flexible housings         117-81-7
Boric acid                    Flux; plating baths; glass            10043-35-3
Lead monoxide                 Glass; ceramic; PZT                   1317-36-8
Cobalt dichloride             Desiccants; humidity indicators       7646-79-9
Diboron trioxide              Glass; ceramics                       1303-86-2
HBCDD                         Flame retardant in EPS/XPS            25637-99-4
4-nonylphenol                 Surfactant in cleaning/flux           84852-15-3
PFOA                          Coatings; water repellents            335-67-1
Bisphenol A                   Epoxy resins; PCB laminate            80-05-7
```

---

*End of Document — 02_REACH_SVHC_Management.md*
