# Conflict Minerals — 3TG Due Diligence & Responsible Sourcing

**Topic:** Conflict minerals (tantalum, tin, tungsten, gold — 3TG) due diligence, supply chain transparency, regulatory compliance (Dodd-Frank, EU Conflict Minerals Regulation), and responsible sourcing frameworks  
**Standard:** US Dodd-Frank Act §1502; EU Conflict Minerals Regulation (2017/821); OECD Due Diligence Guidance for Responsible Supply Chains; RMI (Responsible Minerals Initiative) frameworks  
**SDO:** US SEC; European Commission; OECD; Responsible Minerals Initiative (RMI); London Bullion Market Association (LBMA)  
**Audience:** Supply chain compliance managers, procurement specialists, CSR/ESG professionals, electronics manufacturers, conflict minerals program managers, legal/regulatory affairs  
**Prerequisites:** Supply chain management fundamentals, basic understanding of mineral extraction, electronics manufacturing awareness

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 1996-2003 | Second Congo War | Deadliest conflict since WWII; minerals financed armed groups; 5.4 million deaths |
| 2002 | UN Panel of Experts reports on DRC mineral exploitation | Documents how armed groups fund conflict through mineral trade |
| 2006 | OECD Risk Awareness Tool for Multinational Enterprises in Weak Governance Zones | Early guidance on conflict-affected areas |
| 2010 | **US Dodd-Frank Act §1502** signed into law | First mandatory conflict minerals disclosure; SEC-reporting companies; 3TG from DRC/adjoining countries |
| 2011 | OECD Due Diligence Guidance for Responsible Supply Chains | International framework; 5-step due diligence process; gold supplement |
| 2012 | CFSI (now RMI) launches CMRT (Conflict Minerals Reporting Template) | Standardized supply chain survey tool; industry-wide adoption |
| 2013 | SEC Final Rule for §1502 | Detailed requirements for Conflict Minerals Report; SD filing |
| 2014 | First Dodd-Frank conflict minerals filings (Form SD) by SEC companies | >1,200 companies file; massive supply chain data collection effort |
| 2016 | EU Conflict Minerals Regulation (2017/821) adopted | Mandatory due diligence for EU importers of 3TG; smelter/refiner focus |
| 2017 | RMI (Responsible Minerals Initiative) rebranded from CFSI | Expanded scope beyond conflict minerals; cobalt; mica added |
| 2019 | EU Regulation enters into force (formally) | Preparatory phase; importers begin compliance |
| 2021 | **EU Conflict Minerals Regulation full enforcement** (January 1) | EU importers of 3TG minerals/metals above volume thresholds must conduct OECD due diligence |
| 2022 | RMI launches EMRT (Extended Minerals Reporting Template) for cobalt | Expanding beyond 3TG; cobalt supply chain due diligence |
| 2023 | EU CSDDD (Corporate Sustainability Due Diligence Directive) adopted | Broader human rights/environmental due diligence; encompasses mineral sourcing |
| 2024 | EU Battery Regulation due diligence provisions | Cobalt, lithium, nickel supply chain due diligence for batteries |
| 2025 | OECD updated guidance; growing scope (cobalt, lithium, mica, rare earths) | Moving beyond 3TG to all "responsible minerals" |

### 1.2 The 3TG Minerals

| Mineral | Metal | Symbol | Primary DRC Use | Electronics Applications |
|---------|-------|:------:|----------------|--------------------------|
| **Coltan** (Columbite-tantalite) | Tantalum | Ta | Capacitors; superalloys | Tantalum capacitors (smartphones, laptops, automotive ECUs); surgical implants |
| **Cassiterite** | Tin | Sn | Solder; tin plating | Solder alloys (SAC305); tin plating on connectors/leads; tinplate |
| **Wolframite** | Tungsten | W | Electrical contacts; carbide tools | Tungsten contacts; filaments; vibration motors; carbide tooling |
| **Gold ore** | Gold | Au | Wire bonding; plating; contacts | Wire bonding in ICs; gold plating on connectors; PCB surface finish (ENIG); circuitry |

### 1.3 Why Conflict Minerals Matter

```mermaid
graph TB
    subgraph "The Problem"
        MINING[Artisanal Mining (DRC)<br/>━━━━━━━━━━━<br/>• Small-scale; manual<br/>• Often controlled by armed groups<br/>• Child labor; forced labor<br/>• Environmental destruction<br/>• Dangerous conditions]
        
        ARMED[Armed Groups<br/>━━━━━━━━━━━<br/>• Tax/extort miners<br/>• Control mines/trade routes<br/>• Revenue funds weapons<br/>• Perpetuates conflict<br/>• Human rights abuses]
        
        TRADE[Opaque Supply Chain<br/>━━━━━━━━━━━<br/>• Minerals traded through<br/>  multiple intermediaries<br/>• Mixed with legitimate supply<br/>• Impossible to trace<br/>  without due diligence]
    end
    
    subgraph "The Solution"
        DD[OECD Due Diligence<br/>━━━━━━━━━━━<br/>• 5-step framework<br/>• Supply chain mapping<br/>• Risk identification<br/>• Smelter/refiner audit<br/>• Public reporting]
        
        RMAP[Smelter Audit (RMAP)<br/>━━━━━━━━━━━<br/>• Third-party audit<br/>  of smelters/refiners<br/>• Verify source of minerals<br/>• Conformant = legitimate<br/>• Non-conformant = risk]
        
        CMRT_S[CMRT<br/>━━━━━━━━━━━<br/>• Standardized reporting<br/>• Supplier-to-customer<br/>• Identifies smelters<br/>• Country of origin<br/>• Due diligence program]
    end
    
    MINING --> ARMED --> TRADE
    TRADE --> DD --> RMAP --> CMRT_S
```

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 Regulatory Framework Comparison

| Aspect | US Dodd-Frank §1502 | EU Conflict Minerals Reg (2017/821) |
|--------|:---:|:---:|
| **Who must comply** | SEC-reporting companies (manufacturers/contractors) using 3TG | EU importers of 3TG minerals/metals above volume thresholds |
| **Minerals covered** | Tantalum, Tin, Tungsten, Gold (3TG) | Same: Tantalum, Tin, Tungsten, Gold |
| **Geographic focus** | DRC (Democratic Republic of Congo) + 9 adjoining countries | ALL conflict-affected and high-risk areas (CAHRAs) globally |
| **Obligation type** | Disclosure (file Form SD with SEC; Conflict Minerals Report) | Due diligence (implement OECD 5-step framework; third-party audit for smelters) |
| **Supply chain coverage** | Entire supply chain (manufacturer must trace to smelter) | Direct importers of minerals/metals (upstream focus); downstream users have indirect obligation |
| **Volume thresholds** | No threshold (any use of 3TG triggers) | Yes: specific thresholds per mineral (e.g., >100kg tin; >5000kg iron/steel alloys with tin) |
| **Reporting** | Annual Form SD filing to SEC; Conflict Minerals Report (CMR) | Annual compliance report (to national authority); third-party audit of management system |
| **Standard** | OECD Due Diligence Guidance (recommended, not mandated) | OECD Due Diligence Guidance (**mandated** as the framework) |
| **Penalty** | SEC enforcement (limited; mostly disclosure-based) | National authority enforcement; fines per member state |

### 2.2 OECD 5-Step Due Diligence Framework

| Step | Title | Actions |
|:----:|-------|---------|
| **1** | Establish strong company management systems | • Adopt supply chain policy for conflict minerals<br/>• Structure internal management (team, process)<br/>• Establish supply chain traceability<br/>• Strengthen supplier engagement |
| **2** | Identify and assess risks in supply chain | • Map supply chain (identify smelters/refiners)<br/>• Use CMRT/EMRT for data collection<br/>• Assess smelter RMAP status<br/>• Identify red flags (non-conformant smelters; CAHRAs; armed group control) |
| **3** | Design and implement strategy to respond to identified risks | • Risk mitigation plan<br/>• Engage with suppliers on risk<br/>• Support smelter audit (RMAP participation)<br/>• Escalation: suspend/disengage from non-conformant smelters |
| **4** | Carry out independent third-party audit of supply chain due diligence | • Third-party audit of management system (for EU importers)<br/>• Support RMAP audits of smelters/refiners<br/>• Verify due diligence process effectiveness |
| **5** | Report on supply chain due diligence | • Public reporting (SEC Form SD; EU compliance report)<br/>• CMRT/EMRT responses to customers<br/>• Describe due diligence efforts and findings<br/>• Disclose list of smelters/refiners used |

### 2.3 Conflict Minerals Reporting Template (CMRT)

| Section | Content | Purpose |
|---------|---------|---------|
| **Declaration** | Company information; contact; authorization; reporting scope | Identify reporting entity and scope |
| **Declaration scope** | Company-level or product-level reporting; which minerals applicable | Define what the report covers |
| **Minerals questions** | Per mineral (Ta, Sn, W, Au): necessary for product function? Source from DRC/CAHRA? Known smelters? | High-level risk assessment per mineral |
| **Due diligence** | Due diligence measures taken; policy; management system | Demonstrate OECD compliance |
| **Smelter list** | List ALL identified smelters/refiners in supply chain (name, ID, country, RMAP status) | Core data for supply chain transparency |
| **Product list** (optional) | Products containing conflict minerals (if product-level reporting) | Traceability to specific products |

---

## Chapter 3 — Technical Deep Dive

### 3.1 Supply Chain Mapping (Mine to Product)

```mermaid
flowchart LR
    MINE[Mine<br/>(Artisanal or Industrial)<br/>━━━━━━━━━━━<br/>• DRC; Rwanda; Indonesia<br/>  Bolivia; China; etc.<br/>• Location determines<br/>  conflict risk]
    
    TRADER[Traders / Exporters<br/>━━━━━━━━━━━<br/>• Aggregate minerals<br/>• Export from source country<br/>• Multiple trading steps<br/>• Hardest to trace]
    
    SMELTER[Smelter / Refiner<br/>━━━━━━━━━━━<br/>• CHOKEPOINT in supply chain<br/>• Converts ore to metal<br/>• ~300 smelters globally for 3TG<br/>• RMAP audit target<br/>• Once smelted: cannot trace<br/>  origin of metal]
    
    METAL[Metal Producer<br/>━━━━━━━━━━━<br/>• Produces refined metal<br/>  (ingots, powder, wire, foil)<br/>• Ships to component makers]
    
    COMPONENT[Component Manufacturer<br/>━━━━━━━━━━━<br/>• Capacitor; connector; IC<br/>• Uses metal in manufacturing<br/>• May have multiple smelter<br/>  sources]
    
    OEM[OEM / Product Manufacturer<br/>━━━━━━━━━━━<br/>• Assembles product<br/>• Queries supply chain (CMRT)<br/>• Reports to SEC/EU<br/>• Customer-facing reporting]
    
    MINE --> TRADER --> SMELTER --> METAL --> COMPONENT --> OEM
    
    style SMELTER fill:#ff9900,stroke:#cc7700
```

### 3.2 RMAP (Responsible Minerals Assurance Process)

| Aspect | Detail |
|--------|--------|
| **What** | Third-party audit program for smelters/refiners; verifies they source responsibly |
| **Managed by** | RMI (Responsible Minerals Initiative) |
| **Scope** | Tantalum, Tin, Tungsten, Gold smelters/refiners globally |
| **Audit** | Independent auditor (approved by RMI) assesses smelter's procurement practices against RMAP standard |
| **Assessment criteria** | Smelter must demonstrate: (1) Know Your Supplier (KYS) process; (2) Source documentation (mine of origin; transport records); (3) No sourcing from armed group-controlled mines; (4) Policies for CAHRA sourcing |
| **Status outcomes** | **Conformant**: passed audit; demonstrates responsible sourcing. **Active**: engaged in audit process; not yet conformant. **Non-conformant**: failed audit or refused audit. |
| **Coverage** | ~400+ smelters/refiners identified globally; ~60-80% currently conformant or active |
| **Recognition** | RMAP conformant smelters recognized by EU Regulation; accepted by SEC; referenced by major OEMs as evidence of responsible sourcing |

### 3.3 3TG in Electronics — Typical Content

| Product | Tantalum | Tin | Tungsten | Gold |
|---------|:--------:|:---:|:--------:|:----:|
| Smartphone | Capacitors (Ta capacitors) | Solder (SAC alloy); tin plating on connectors | Vibration motor (tungsten weight) | Wire bonds in ICs; gold contacts; ENIG finish |
| Laptop/PC | Ta capacitors in power supply; motherboard | Solder; tin plating | Tooling in manufacturing (indirect) | IC wire bonds; connector plating; PCB finish |
| Automotive ECU | Ta capacitors (reliability preference) | Solder; component terminations | Contacts (relays; switches) | Wire bonds; connector contacts; PCB finish |
| Server | Multiple Ta capacitors (power stability) | Extensive solder; plating | Heat sinks (W-Cu composites) | High-density wire bonds; connector arrays |
| LED lighting | Ta capacitors (driver circuit) | Solder; lead frame plating | Filament/electrode (some types) | Wire bonds in LED packages |

---

## Chapter 4 — Implementation Guide

### 4.1 Conflict Minerals Program Implementation

| Phase | Actions | Output |
|:-----:|---------|--------|
| **1. Policy & governance** | Adopt conflict minerals policy (publicly available); assign program manager; establish cross-functional team (procurement, legal, compliance, engineering) | Published policy; program charter; team structure |
| **2. Scope determination** | Identify which products contain 3TG; determine if 3TG is "necessary to the functionality or production" of products (Dodd-Frank language) | Product-mineral matrix; scope determination |
| **3. Supply chain survey** | Distribute CMRT to ALL suppliers of 3TG-containing components; set response deadlines; follow up on non-responses | CMRT database; supplier response rate tracking |
| **4. Data collection & validation** | Collect CMRT responses; validate completeness; flag incomplete/inconsistent responses; request corrections; compile smelter list | Validated smelter list; data quality metrics |
| **5. Risk assessment** | Cross-reference identified smelters against RMAP conformant list; identify non-conformant or unknown smelters; assess country-of-origin risk | Risk assessment report; red flag smelters identified |
| **6. Risk mitigation** | Engage with suppliers using non-conformant smelters; request smelter participation in RMAP; escalation plan (timeline to conform or switch) | Mitigation actions; supplier engagement log |
| **7. Reporting** | Prepare SEC Form SD + Conflict Minerals Report (if SEC-reporting) OR EU compliance report (if EU importer); public disclosure of smelter list | Filed report; public disclosure; annual update |
| **8. Continuous improvement** | Annual survey cycle; increase response rate; increase RMAP-conformant smelter %; engage industry programs; training | Year-over-year improvement metrics |

### 4.2 CMRT Response Management

| Metric | Target | Industry Average (2024) |
|--------|:------:|:-----------------------:|
| Supplier CMRT response rate | >95% | 70-85% |
| CMRT completeness (smelters identified) | >90% | 60-80% |
| % smelters RMAP conformant | >90% | 75-85% |
| Unknown smelters (can't identify) | <5% | 10-20% |
| Time to collect all responses | <90 days | 120-180 days |
| Year-over-year improvement | +5% per metric per year | Varies |

### 4.3 Supplier Engagement Strategy

| Tier | Suppliers | Approach |
|:----:|-----------|----------|
| **Tier 1** (Strategic) | Top 20 suppliers by 3TG risk/volume; direct component suppliers of Ta capacitors, solder, connectors | Direct engagement; executive-level communication; joint improvement programs; RMAP promotion; face-to-face meetings |
| **Tier 2** (Important) | Next 50-100 suppliers; components containing 3TG; board assemblies; sub-assemblies | Structured CMRT request; follow-up calls; webinar training; escalation for non-response |
| **Tier 3** (Standard) | Remaining suppliers; lower 3TG content; indirect use | Automated CMRT request via platform (Assent, Source Intelligence, iPoint); reminder cycles; accept company-level CMRT |
| **Non-responsive** | Suppliers who don't respond after 3+ contacts | Escalation to procurement leadership; include in supplier scorecard; possible disqualification for future business |

---

## Chapter 5 — Regulatory Filing

### 5.1 SEC Form SD Filing (US Dodd-Frank)

| Element | Content | Detail |
|---------|---------|--------|
| **Filing** | Form SD (Specialized Disclosure) to SEC | Annual filing; due May 31 for preceding calendar year |
| **Exhibit** | Conflict Minerals Report (CMR) attached as exhibit to Form SD | If RCOI (Reasonable Country of Origin Inquiry) does not determine DRC-conflict-free |
| **RCOI** | Reasonable Country of Origin Inquiry | Good-faith effort to determine if 3TG originated from DRC/covered countries; uses CMRT data; smelter identification |
| **Determination** | Three possible conclusions: (1) DRC Conflict Free; (2) Not been found to be DRC Conflict Free; (3) DRC Conflict Undeterminable | Based on RCOI results; most companies file (2) or (3) |
| **Content of CMR** | Description of due diligence measures; products covered; list of facilities (smelters) used to process 3TG; countries of origin; independent private sector audit (if applicable) | Detailed narrative + smelter data |
| **Independent audit** | Required if products described as "DRC conflict free" | Must be conducted by recognized audit firm |
| **Public availability** | Form SD and CMR publicly available on SEC EDGAR | Transparency mechanism; NGO/investor scrutiny |
| **Penalties** | SEC enforcement for false/misleading statements; no penalty for simply disclosing inability to determine conflict-free status | Disclosure-focused; not performance-focused |

### 5.2 EU Conflict Minerals Regulation Compliance

| Obligation | Threshold | Requirement |
|------------|:---------:|-------------|
| **Tin importers** | >100 kg/year metal/concentrates OR >5000 kg alloys/intermediate | OECD 5-step due diligence; management system; supply chain policy; risk management; third-party audit; annual reporting |
| **Tantalum importers** | >100 kg/year metal/concentrates; lower thresholds for some forms | Same |
| **Tungsten importers** | >250 kg APT/metal; higher for ores/concentrates | Same |
| **Gold importers** | >100 kg/year metal; >5 kg for unwrought | Same |
| **Downstream manufacturers** | No direct obligation (but indirect: supply chain inquiries from importers; voluntary responsible sourcing expected) | Voluntary OECD due diligence; respond to supply chain queries; may face CSDDD obligations |
| **Third-party audit** | Required annually for importers above thresholds | Independent audit per Regulation criteria; competent authority may request |
| **Reporting** | Annual to national competent authority | Due diligence practices; risk management actions; smelter/refiner audit results |
| **Recognized schemes** | EU can recognize equivalent schemes (e.g., RMAP; LBMA) | Recognized scheme participation can satisfy audit requirement |

---

## Chapter 6 — Regional Context

### 6.1 Conflict Minerals Regulations Worldwide

| Region | Regulation | Scope | Approach |
|--------|-----------|-------|:--------:|
| **US** | Dodd-Frank §1502 (2010) | SEC-reporting companies using 3TG | Disclosure (file annually; no performance requirement) |
| **EU** | Regulation 2017/821 (2021 enforcement) | EU importers of 3TG above thresholds | Due diligence (OECD 5-step mandatory; audit; reporting) |
| **China** | Chinese Due Diligence Guidelines for Responsible Mineral Supply Chains (2015) | Chinese companies in mineral supply chain | Voluntary guidelines (aligned with OECD; limited enforcement) |
| **Japan** | JBCE Guidelines; Japan Electronics and IT Industries Association guidance | Japanese electronics companies | Voluntary; industry self-regulation; aligned with RMI/OECD |
| **Australia** | Modern Slavery Act (2018); voluntary mineral supply chain guidance | Large entities (>AUD 100M revenue) | Modern slavery reporting (includes mineral supply chain) |
| **OECD** | Due Diligence Guidance for Responsible Supply Chains | All entities in mineral supply chains | Voluntary international standard (but mandated by EU/referenced by US) |
| **Africa** | ICGLR Regional Certification Mechanism | Great Lakes region countries (DRC, Rwanda, etc.) | Certification of mineral origin; tagging and tracing |

### 6.2 Covered Countries (Dodd-Frank)

| Country | Status | Key Minerals |
|---------|:------:|:---:|
| Democratic Republic of Congo (DRC) | Central focus | Ta, Sn, W, Au (all 4) |
| Republic of Congo | Adjoining | Au |
| Central African Republic | Adjoining | Au, diamonds |
| South Sudan | Adjoining | Au |
| Uganda | Adjoining; transit country | Au (transit) |
| Rwanda | Adjoining; major transit | Ta, Sn, W, Au |
| Burundi | Adjoining | Au, Sn, Ta |
| Tanzania | Adjoining | Au, Ta |
| Zambia | Adjoining | Cu (not 3TG typically) |
| Angola | Adjoining | Au, diamonds |

Note: EU Regulation is NOT geographically limited — it covers ALL conflict-affected and high-risk areas (CAHRAs) globally, including but not limited to DRC region.

---

## Chapter 7 — Comparison

### 7.1 Dodd-Frank vs. EU Regulation vs. OECD Guidance

| Dimension | Dodd-Frank §1502 | EU Reg 2017/821 | OECD Guidance |
|-----------|:---:|:---:|:---:|
| Nature | Law (mandatory disclosure) | Regulation (mandatory due diligence) | Voluntary guidance (international standard) |
| Geographic scope | DRC + 9 adjoining countries | **All CAHRAs globally** | All CAHRAs globally |
| Who must comply | SEC-reporting manufacturers using 3TG | EU importers of minerals/metals above thresholds | Any entity in mineral supply chain (voluntary) |
| Focus in supply chain | Downstream (manufacturers must trace back) | **Upstream** (importers of raw minerals/metals) | Entire supply chain |
| Mechanism | Disclosure to SEC (transparency) | Due diligence implementation (process) + audit | Due diligence framework (5-step process) |
| Smelter focus | Report smelters used | Require smelter audit compliance | Recommend smelter audit |
| Performance requirement | No (just disclose; can say "undeterminable") | Yes (must implement due diligence; mitigate risks) | Yes (must implement 5 steps) |
| Volume thresholds | None (any 3TG use triggers) | Yes (specific kg thresholds) | None |
| Third-party audit | Required only if claiming "DRC Conflict Free" | Mandatory annual audit of management system | Recommended |
| Minerals covered | 3TG only | 3TG only | 3TG + gold (specific supplement); adaptable to others |

### 7.2 RMI Tools and Programs

| Tool/Program | Purpose | Coverage |
|-------------|---------|----------|
| **CMRT** (Conflict Minerals Reporting Template) | Standardized supply chain survey for 3TG smelter identification | 3TG; used by 40,000+ companies |
| **EMRT** (Extended Minerals Reporting Template) | Survey for cobalt and mica supply chains | Cobalt, mica; growing adoption |
| **RMAP** (Responsible Minerals Assurance Process) | Third-party audit program for smelters/refiners | Ta, Sn, W, Au smelters; ~400+ assessed |
| **RMI Smelter Database** | Public list of known smelters with RMAP status | Conformant, Active, Non-conformant, Unknown |
| **RMAP for Cobalt** | Extension of RMAP to cobalt refiners | Cobalt; growing (battery supply chain demand) |
| **RMI Standards** | Audit standards and protocols for RMAP | Technical audit criteria per mineral |
| **RMI Training** | Capacity building; supplier training; webinars | Industry-wide education |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 Annual Conflict Minerals Compliance Cycle

```mermaid
graph TB
    subgraph "Q4 (Oct-Dec): Preparation"
        PREP[Preparation<br/>━━━━━━━━━━━<br/>• Update CMRT template (new version?)<br/>• Identify supplier list for survey<br/>• Update survey communication<br/>• Prepare data collection platform]
    end
    
    subgraph "Q1 (Jan-Mar): Data Collection"
        SURVEY[Survey Distribution<br/>━━━━━━━━━━━<br/>• Send CMRT to all in-scope suppliers<br/>• Deadline: typically 60-90 days<br/>• Automated reminders<br/>• Non-response escalation]
        
        VALIDATE[Data Validation<br/>━━━━━━━━━━━<br/>• Check completeness<br/>• Validate smelter IDs<br/>• Cross-reference RMAP list<br/>• Flag inconsistencies<br/>• Request corrections]
    end
    
    subgraph "Q2 (Apr-May): Analysis & Reporting"
        ANALYZE[Risk Analysis<br/>━━━━━━━━━━━<br/>• Compile smelter list (deduplicate)<br/>• Check RMAP status per smelter<br/>• Identify non-conformant smelters<br/>• Assess geographic risk (CAHRA)<br/>• Determine DRC conflict status]
        
        REPORT[Reporting<br/>━━━━━━━━━━━<br/>• Prepare Conflict Minerals Report<br/>• Draft Form SD (SEC)<br/>• OR EU compliance report<br/>• File by deadline (SEC: May 31)]
    end
    
    subgraph "Q2-Q3 (May-Sep): Mitigation"
        MITIGATE[Risk Mitigation<br/>━━━━━━━━━━━<br/>• Engage suppliers with non-conformant smelters<br/>• Request smelter switch or RMAP participation<br/>• Escalation timeline<br/>• Industry collaboration (RMI)]
    end
    
    subgraph "Ongoing"
        IMPROVE[Continuous Improvement<br/>━━━━━━━━━━━<br/>• Increase response rate<br/>• Increase RMAP conformant %<br/>• Reduce unknown smelters<br/>• Supplier training<br/>• New supplier onboarding]
    end
    
    PREP --> SURVEY --> VALIDATE --> ANALYZE --> REPORT
    REPORT --> MITIGATE --> IMPROVE
    IMPROVE -->|Next cycle| PREP
```

### 8.2 Due Diligence Decision Framework

```mermaid
flowchart TB
    START[Company Uses 3TG<br/>in Products]
    
    SEC{SEC-reporting<br/>company?}
    
    IMPORTER{EU importer of<br/>3TG minerals/metals<br/>above thresholds?}
    
    RCOI[Conduct RCOI<br/>━━━━━━━━━━━<br/>• Survey suppliers (CMRT)<br/>• Identify smelters<br/>• Determine country of origin<br/>• Assess if DRC/adjoining country]
    
    DRC_FREE{Determined<br/>DRC Conflict Free?}
    
    FILE_FREE[File Form SD<br/>"DRC Conflict Free"<br/>+ Independent Audit Required]
    
    FILE_NOT[File Form SD<br/>"Not Determined to be<br/>DRC Conflict Free"<br/>OR "Undeterminable"<br/>+ Conflict Minerals Report]
    
    EU_DD[Implement OECD 5-Step<br/>Due Diligence<br/>━━━━━━━━━━━<br/>• Management system<br/>• Supply chain policy<br/>• Risk identification<br/>• Risk mitigation<br/>• Third-party audit<br/>• Annual reporting]
    
    VOL[Voluntary Due Diligence<br/>━━━━━━━━━━━<br/>• Best practice<br/>• Customer requirements<br/>• Industry programs (RMI)<br/>• Respond to CMRT requests<br/>• Support supply chain transparency]
    
    START --> SEC & IMPORTER
    SEC -->|Yes| RCOI --> DRC_FREE
    SEC -->|No| VOL
    DRC_FREE -->|Yes| FILE_FREE
    DRC_FREE -->|No/Undeterminable| FILE_NOT
    IMPORTER -->|Yes (above thresholds)| EU_DD
    IMPORTER -->|No (below thresholds or not importer)| VOL
```

---

## Chapter 9 — Case Studies

### 9.1 Case Study: Electronics OEM Conflict Minerals Program

| Aspect | Detail |
|--------|--------|
| Company | Major electronics OEM (consumer + enterprise); 10,000+ suppliers; SEC-reporting; selling in EU; 50,000+ components using 3TG |
| Program maturity | Year 8 of Dodd-Frank reporting; program evolved from basic compliance to industry leadership |
| Program structure | Dedicated team: 3 FTEs (program manager + 2 analysts); cross-functional steering committee (procurement VP, legal, sustainability, engineering); budget: ~$500K/year (platform, surveys, team, RMI membership, consulting) |
| Data collection | Platform: Assent Compliance (automated CMRT collection + validation). Surveyed: ~3,000 suppliers (all component suppliers potentially containing 3TG). Response rate: Year 1: 55%; Year 8: **92%**. Smelters identified: 340 unique smelters/refiners across supply chain. |
| Results | RMAP conformant smelters: Year 1: 45%; Year 8: **87%**. Non-conformant smelters: reduced from 80 to 12 (through engagement + supplier switching). Unknown smelters: reduced from 25% to 4%. DRC-sourced minerals: some identified from DRC (legitimate; artisanal mining programs); not conflict-linked (smelters RMAP conformant). |
| Key actions taken | (1) Supplier scorecarding: CMRT response rate and smelter conformance included in supplier performance evaluation. (2) Industry collaboration: founding member of RMI working groups; participated in RMAP standard development. (3) Direct smelter engagement: contacted top 5 non-conformant smelters directly; offered to support RMAP audit costs; 3 became conformant. (4) Supplier disqualification: 2 suppliers disqualified (after 2 years of non-response + non-engagement) — procurement switched to conformant alternatives. (5) Public reporting: annual Conflict Minerals Report filed with SEC; smelter list published on website; progress metrics disclosed. |
| Challenges | (1) Supplier fatigue: different customers ask similar questions on different timelines; addressed by adopting standardized CMRT + RMI tools. (2) Sub-tier visibility: Tier 1 suppliers don't always know their Tier 2-3 smelter sources; improving through industry data sharing. (3) Smelter identification for gold: most difficult (gold supply chain most opaque; many small refiners; recycled gold complicates). (4) Cost: significant ongoing investment; justified by regulatory requirement + customer expectations + reputational risk. |
| Lessons | (1) Year-over-year improvement requires sustained effort and executive support. (2) Industry collaboration (RMI membership) provides tools, data, and collective leverage. (3) Supplier engagement (not just surveying) is key to improvement. (4) Automated platform essential at scale (>1,000 suppliers). (5) Integration with procurement (scorecard, qualification) creates real incentive for suppliers. |

### 9.2 Case Study: EU Importer Compliance (First Year)

| Aspect | Detail |
|--------|--------|
| Company | EU-based metals trading company; imports tin ingots from Southeast Asia; volumes: 500 tonnes/year (well above 100kg threshold) |
| EU obligation | As importer of tin metal >100kg/year: full OECD 5-step due diligence mandatory; third-party audit; annual report to national competent authority |
| Implementation | (1) **Policy**: Adopted public responsible sourcing policy; commitment to not source from armed group-controlled mines; aligned with OECD Guidance. (2) **Management system**: Designated Responsible Sourcing Manager; established internal review committee; documented procedures. (3) **Supply chain mapping**: Identified all upstream sources: 3 primary smelters in Indonesia, Malaysia, China; confirmed smelter identities; requested RMAP status. (4) **Risk assessment**: 2 of 3 smelters RMAP conformant; 1 smelter: RMAP Active (audit in progress). Assessed geographic risk: Indonesia (artisanal tin mining Bangka Island — known social/environmental risks, but not "conflict" per DRC definition; however, CAHRA criteria may apply under EU regulation). (5) **Risk mitigation**: For Bangka-sourced tin: smelter demonstrates traceability to specific mines; no armed group involvement; but labor practices and environmental risks identified → engaged smelter on improvement plan. (6) **Third-party audit**: Engaged approved auditor; annual audit of company's management system and due diligence process (not just smelter audit). (7) **Reporting**: Annual report to national authority documenting all above; published summary. |
| Cost | ~€80K first year (consultant support; audit; management system development; training). ~€40K/year ongoing (annual audit; reporting; system maintenance). |
| Result | Compliant with EU Regulation from enforcement date; identified one ongoing risk (Bangka sourcing) with documented mitigation; received positive audit opinion; national authority accepted report. |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **Scope expansion beyond 3TG** | Now-2026 | Cobalt (batteries; DRC); lithium; mica; rare earths; increasing minerals under due diligence expectations |
| **EU CSDDD** (Corporate Sustainability Due Diligence Directive) | 2024-2027 | Broader human rights and environmental due diligence obligation; encompasses mineral supply chain; applies to large EU companies |
| **EU Battery Regulation due diligence** | 2025-2027 | Cobalt, lithium, nickel supply chain due diligence mandatory for battery manufacturers |
| **Digital traceability** | Now | Blockchain for mineral traceability (e.g., Minespider; Circulor); digital tags; IoT tracking from mine to smelter |
| **Artisanal and Small-scale Mining (ASM) formalization** | Ongoing | Programs to formalize legitimate ASM (not eliminate); fair trade minerals; development vs. exclusion approach |
| **SEC Dodd-Frank evolution** | Uncertain | Political pressure to weaken/strengthen §1502; currently stable; potential expansion to cobalt |
| **RMI tool evolution** | Ongoing | CMRT v7+; EMRT for cobalt/mica; enhanced data validation; API integration for automated collection |
| **Responsible sourcing as competitive advantage** | Now | OEM customers requiring 100% RMAP conformant smelters; suppliers with better programs win business |
| **Due diligence for all minerals** | 2025-2030 | Trend toward comprehensive mineral due diligence (not just "conflict" minerals but all responsible sourcing) |
| **AI in supply chain mapping** | Now | AI tools for supplier identification; smelter prediction; risk scoring; document analysis |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What are "conflict minerals" and why are they regulated?  
**A:** Conflict minerals are **tantalum (Ta), tin (Sn), tungsten (W), and gold (Au)** — known as "3TG." They're called "conflict minerals" because their mining and trade in the Democratic Republic of Congo (DRC) and surrounding region have historically funded armed groups, perpetuating deadly conflict and human rights abuses (child labor, forced labor, violence). The minerals are extracted from mines controlled or taxed by armed groups, with revenue used to buy weapons and fund military operations. Regulations (US Dodd-Frank §1502; EU Regulation 2017/821) require companies to conduct due diligence on their supply chains to determine if their products contain 3TG sourced from conflict-affected areas. The goal is to cut the financial link between mineral trade and armed conflict, while not creating a de-facto embargo that harms legitimate miners. The key mechanism is supply chain transparency — identifying which smelters process the minerals and whether those smelters source responsibly.

**Q2:** What is the CMRT and how is it used?  
**A:** CMRT = **Conflict Minerals Reporting Template** — a standardized Excel-based survey form developed by RMI (Responsible Minerals Initiative) used throughout the electronics and manufacturing supply chain. It's the industry standard tool for collecting conflict minerals information from suppliers. How it's used: a company (OEM or manufacturer) sends the CMRT to its suppliers asking them to identify which smelters/refiners are in their supply chain for each of the 4 minerals (Ta, Sn, W, Au). The supplier completes the template (either at company level or product level) listing all known smelters. The requesting company aggregates responses from all suppliers to compile a complete list of smelters in its supply chain, then cross-references against the RMAP (Responsible Minerals Assurance Process) conformant smelter list to assess risk. The CMRT flows down the supply chain — each tier passes the request to its suppliers until smelters are identified.

### Tier 2: Mid-Level

**Q3:** Your CMRT data shows 15% of identified smelters are non-RMAP-conformant. What's your mitigation strategy?  
**A:** [Detailed answer covering: (1) Identify which suppliers use non-conformant smelters (may be multiple suppliers per smelter). (2) Prioritize by risk: smelters in/near CAHRAs > smelters refusing audit > smelters in process. (3) Supplier engagement: contact affected suppliers; explain requirement; request they either (a) engage their smelter to participate in RMAP, or (b) demonstrate equivalent due diligence, or (c) switch to conformant smelter. (4) Direct smelter engagement: if a smelter is used by many companies in industry, coordinate with RMI to encourage RMAP participation. (5) Set timeline: give suppliers 6-12 months to resolve; escalation path if no progress. (6) Escalation: after reasonable time, if smelter remains non-conformant and supplier can't switch → consider: supplier qualification impact; risk-based decision to continue or discontinue. (7) Document all actions and reasoning (for SEC/EU reporting). (8) Industry collaboration: report non-conformant smelters to RMI; support collective engagement.]

### Tier 3: Senior

**Q4:** Design a responsible minerals program that covers 3TG + cobalt + mica for a large multinational with 20,000 suppliers, including preparing for EU CSDDD requirements.  
**A:** [Comprehensive answer covering: (1) Integrated responsible minerals policy covering all minerals of concern (3TG + cobalt + mica + lithium + any future additions); OECD-aligned; public commitment. (2) Tiered supplier approach: survey top 2,000 suppliers (by mineral risk) with CMRT + EMRT; automated platform (Assent/Source Intelligence) for scale; risk-based sampling for remainder. (3) Technology: compliance platform with automated distribution, collection, validation; integration with procurement/ERP systems; smelter/refiner database; dashboard for management. (4) Governance: executive sponsor; cross-functional committee; regional leads; integration with broader ESG governance. (5) CSDDD preparation: broader due diligence framework (human rights + environment) that encompasses mineral sourcing as one topic; risk-based prioritization across all human rights/environmental risks; stakeholder engagement; remediation mechanisms. (6) KPIs: response rate, conformant %, improvement trajectory; linked to management compensation/objectives. (7) Industry engagement: RMI leadership role; sector-specific working groups; multi-stakeholder initiatives; support ASM formalization programs. (8) Budget: ~$1-2M/year for 20,000 supplier scale (platform, team, audits, programs).]

---

## Chapter 12 — Cheat Sheet & Quick Reference

### 3TG Quick Reference

```
MINERAL → METAL    WHERE IN ELECTRONICS             MAIN SOURCES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Coltan  → Tantalum  Capacitors (Ta caps)            DRC, Rwanda, Australia, Brazil
Cassiterite → Tin   Solder, plating                 Indonesia, China, Myanmar, DRC
Wolframite → Tungsten  Contacts, motors, tools      China, Russia, Bolivia, DRC
Gold ore → Gold     Wire bonds, connectors, PCB     China, Australia, Russia, DRC

SMELTER = CHOKEPOINT
• ~300-400 known smelters globally for 3TG
• Once smelted, mineral origin undetectable
• RMAP audit at smelter level = key mechanism
```

### Compliance Decision

```
IF SEC-reporting company using 3TG:
  → File Form SD annually (May 31)
  → Conduct RCOI (Reasonable Country of Origin Inquiry)
  → Include Conflict Minerals Report
  → Disclose smelter list

IF EU importer of 3TG above thresholds:
  → Implement OECD 5-step due diligence
  → Third-party management system audit (annual)
  → Report to national competent authority (annual)
  → Use RMAP-conformant smelters

IF neither (but in supply chain):
  → Respond to customer CMRT requests
  → Best practice: implement voluntary due diligence
  → Prepare for CSDDD (if large EU company)
```

### CMRT Tips

```
Sending CMRT:
□ Use latest version (RMI updates annually)
□ Set clear deadline (60-90 days)
□ Provide training/support to suppliers
□ Send automated reminders (Day 30, 60, 75)
□ Escalate non-responses to procurement leadership

Receiving/Reviewing CMRT:
□ Check version (reject outdated versions)
□ Verify completeness (all minerals answered; smelters listed)
□ Validate smelter IDs against RMI database
□ Flag "unknown" smelters for follow-up
□ Cross-reference RMAP conformant list
□ Compile and deduplicate across all suppliers
```

### Key Acronyms

```
3TG    = Tantalum, Tin, Tungsten, Gold
CMRT   = Conflict Minerals Reporting Template
EMRT   = Extended Minerals Reporting Template (cobalt, mica)
RMAP   = Responsible Minerals Assurance Process (smelter audit)
RMI    = Responsible Minerals Initiative (program manager)
RCOI   = Reasonable Country of Origin Inquiry
CAHRA  = Conflict-Affected and High-Risk Areas
Form SD = SEC Specialized Disclosure form
CMR    = Conflict Minerals Report (SEC exhibit)
OECD DD = OECD Due Diligence Guidance for Responsible Supply Chains
CSDDD  = Corporate Sustainability Due Diligence Directive (EU)
```

---

*End of Document — 05_Conflict_Minerals_3TG.md*
