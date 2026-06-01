# RoHS 3 Directive — Restriction of Hazardous Substances in Electronics

**Topic:** EU RoHS Directive compliance for electronics manufacturing — substance restrictions, exemption management, testing, declaration of conformity, and global RoHS equivalents  
**Standard:** EU Directive 2011/65/EU (RoHS 2) amended by EU 2015/863 (RoHS 3); IEC 62321 (testing); EN 50581 (technical documentation)  
**SDO:** European Commission DG ENV; CENELEC (EN 50581); IEC TC 111 (IEC 62321)  
**Audience:** Product compliance engineers, hardware engineers, procurement/sourcing, quality managers, regulatory affairs, environmental compliance managers  
**Prerequisites:** Basic electronics manufacturing knowledge, EU CE marking process, materials science fundamentals

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 1998 | EU Commission proposal for RoHS | Response to growing e-waste and lead contamination concerns |
| 2002 | Directive 2002/95/EC published (RoHS 1) | Original RoHS; restricted 6 substances; 2 product categories |
| 2003 | WEEE Directive published (2002/96/EC) | Companion to RoHS; waste collection/recycling obligations |
| 2004 | Lead-free soldering R&D intensifies | Industry scrambles to develop Sn-Ag-Cu (SAC) solder alternatives |
| 2006 | **RoHS 1 enforcement** (July 1) | 6 substances banned in EEE; massive supply chain transformation |
| 2011 | **RoHS 2** (Directive 2011/65/EU) | Open scope (11 categories); CE marking; Annex III exemptions; substance review mechanism |
| 2013 | EN 50581 published | Harmonized standard for technical documentation (demonstrating RoHS compliance) |
| 2015 | **RoHS 3** (Delegated Directive EU 2015/863) | Added 4 phthalates (DEHP, BBP, DBP, DIBP) to restricted list |
| 2016 | Category 8 (medical devices) and 9 (monitoring/control) fully in scope | Previously transitional; now all categories covered |
| 2019 | Phthalate restrictions enforce for all EEE except Cat 8/9 | Industry must eliminate phthalates above 0.1% |
| 2021 | Phthalate restrictions enforce for Category 8 and 9 | Final categories brought into full phthalate compliance |
| 2022 | EU Commission RoHS review/evaluation (fitness check) | Assessment of potential RoHS 4; additional substances considered (TBBPA, MCCP, beryllium) |
| 2023 | Exemption renewals and pack assessments | Multiple exemptions reviewed; some renewed; some revoked; some with reduced scope |
| 2024 | RoHS exemption backlog addressed; new restriction proposals discussed | TBBPA (Tetrabromobisphenol A) proposed for addition; timing uncertain |
| 2025+ | Potential RoHS 4 proposal; additional substances; ESPR alignment | Integration with Digital Product Passport; potential alignment with circular economy |

### 1.2 The Lead-Free Revolution

The most impactful RoHS requirement was the ban on lead (Pb) in solder. Before RoHS:
- Standard electronics solder: Sn63/Pb37 (eutectic tin-lead), melting point 183°C
- Well-understood, reliable, 50+ years of manufacturing experience
- Lead toxicity concerns in landfill leaching and recycling

After RoHS enforcement:
- Primary replacement: SAC305 (Sn96.5/Ag3.0/Cu0.5), melting point 217-220°C
- Higher reflow temperatures (+30-40°C) → component and board thermal stress
- Different wetting characteristics, grain structure, and failure modes
- New reliability concerns: tin whiskers, pad cratering, head-in-pillow defects
- $billions in industry investment for process, material, and reliability qualification

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 RoHS 2/3 Directive Structure

| Article/Annex | Content | Key Points |
|:---:|---------|-----------|
| Article 1 | Subject matter | Restriction of hazardous substances in EEE |
| Article 2 | Scope | 11 categories of EEE; open scope (anything electrical) |
| Article 3 | Definitions | EEE; homogeneous material; manufacturer; distributor; etc. |
| Article 4 | Prevention | Member States shall ensure EEE does not contain restricted substances above limits |
| Article 5 | Adaptation of Annexes (substance review) | Methodology for reviewing and adding new substances |
| Article 6 | Review and amendment of restricted substances list | Process for proposing new restrictions |
| **Annex I** | **Categories of EEE** | 11 categories (see below) |
| **Annex II** | **Restricted Substances and Maximum Concentrations** | 10 substances with thresholds |
| **Annex III** | **Exemptions (general)** | Specific applications where restricted substances are allowed |
| **Annex IV** | **Exemptions (Cat 8 & 9)** | Additional exemptions for medical devices and monitoring/control instruments |
| Annex V | Applications for exemptions | How to apply for new exemptions |
| Annex VI | EU Declaration of Conformity | Template for RoHS DoC |

### 2.2 EEE Categories (Annex I)

| Category | Description | Examples |
|:--------:|-------------|---------|
| 1 | Large household appliances | Refrigerators, washing machines, ovens, air conditioners |
| 2 | Small household appliances | Vacuum cleaners, toasters, coffee makers, clocks |
| 3 | IT and telecommunications equipment | Computers, printers, phones, routers, servers |
| 4 | Consumer electronics | TVs, audio equipment, video cameras, game consoles |
| 5 | Lighting equipment | LED lamps, luminaires (excluding filament bulbs) |
| 6 | Electrical and electronic tools | Drills, saws, sewing machines (except large-scale industrial) |
| 7 | Toys, leisure and sports equipment | Electric trains, video games, coin-slot machines |
| 8 | Medical devices | Diagnostic equipment; therapeutic equipment; IVD |
| 9 | Monitoring and control instruments | Smoke detectors, thermostats, lab instruments, industrial controls |
| 10 | Automatic dispensers | Vending machines, ATMs, ticket machines |
| 11 | Other EEE (open scope) | Anything with electrical function not in categories 1-10 |

### 2.3 Restricted Substances (Annex II)

| # | Substance | Formula/Type | CAS | Threshold | Added |
|:-:|-----------|:-----------:|:---:|:---------:|:-----:|
| 1 | Lead | Pb (metal/compounds) | 7439-92-1 | 0.1% (1000 ppm) | RoHS 1 (2006) |
| 2 | Mercury | Hg | 7439-97-6 | 0.1% | RoHS 1 |
| 3 | Cadmium | Cd | 7440-43-9 | **0.01% (100 ppm)** | RoHS 1 |
| 4 | Hexavalent Chromium | Cr(VI) | — | 0.1% | RoHS 1 |
| 5 | Polybrominated Biphenyls | PBB | — | 0.1% | RoHS 1 |
| 6 | Polybrominated Diphenyl Ethers | PBDE | — | 0.1% | RoHS 1 |
| 7 | Bis(2-ethylhexyl) phthalate | DEHP | 117-81-7 | 0.1% | RoHS 3 (2019) |
| 8 | Butyl benzyl phthalate | BBP | 85-68-7 | 0.1% | RoHS 3 |
| 9 | Dibutyl phthalate | DBP | 84-74-2 | 0.1% | RoHS 3 |
| 10 | Diisobutyl phthalate | DIBP | 84-69-5 | 0.1% | RoHS 3 |

---

## Chapter 3 — Technical Deep Dive

### 3.1 Homogeneous Material Concept

The threshold (0.1% or 0.01%) applies per **homogeneous material**, NOT per product or component.

| Term | Definition | Examples |
|------|-----------|---------|
| **Homogeneous material** | A material that cannot be mechanically disjointed into different materials | A single alloy; a single polymer; a single glass type; a single plating layer; a single paint layer |
| **NOT homogeneous** | An assembly or component with multiple materials | A PCB (many layers); a cable (conductor + insulation + jacket); a connector (housing + pins + plating) |

**Practical assessment hierarchy:**

```
Product → Sub-assemblies → Components → Homogeneous Materials
                                              ↑
                                    THRESHOLD APPLIED HERE
```

Example: A connector
- Housing (PBT plastic) — one homogeneous material → check for phthalates
- Contact pins (copper alloy CuSn6) — one material → check for Pb in alloy
- Plating on pins (nickel underplate) — one material → check for Cd
- Plating on pins (tin overplate) — one material → check for Pb
- Solder (SAC305) — one material → check for Pb content

### 3.2 Common Lead Sources in Electronics

| Component/Material | Lead Source | Typical Level | RoHS Solution |
|-------------------|-------------|:----:|---------|
| Solder (Sn-Pb eutectic) | Intentional ingredient (37% Pb) | 37% | SAC305; SAC105; SN100C; SnBi |
| Brass (CuZn alloy) | Alloying element for machinability | 1-4% | Lead-free brass (CuZn39 with Bi); stainless steel |
| Glass (in displays, components) | PbO for optical properties | 10-70% | Exemption 7(c)-I; lead-free glass alternatives |
| Ceramic (PZT piezoelectric) | PbZrTiO₃ (lead zirconate titanate) | 60%+ | Exemption 7(c)-I; lead-free piezoelectrics (limited) |
| Steel alloy | Alloying element (machinability) | 0.05-0.35% | Exemption 6(a/b/c); lead-free machining steels |
| Copper alloy (contacts) | Alloying element | 0.5-4% | Exemption 6(a/b/c); lead-free copper alloys |
| BGA solder balls (high-Pb) | High-Pb solder for high-reliability | 85-97% Pb | Exemption 7(a); tin-lead-silver high-reliability |
| Thick-film paste (resistors) | PbO glass frit in paste | 20-50% | Exemption 7(c)-I (glass); lead-free pastes (limited) |

### 3.3 Key RoHS Exemptions (Annex III — Selected)

| Exemption # | Application | Substances Exempted | Status (2024) |
|:-----------:|-------------|:---:|:---:|
| 6(a) | Lead as alloying element in steel (≤0.35% by weight) | Pb | Active; renewed |
| 6(b) | Lead as alloying element in aluminum (≤0.4%) | Pb | Active; renewed |
| 6(c) | Lead in copper alloys (≤4%) | Pb | Active; renewed |
| 7(a) | Lead in high-melting-temperature solder (>85% Pb) | Pb | Active; under review |
| 7(c)-I | Lead in electrical and electronic components (glass/ceramic) | Pb | Active; essential for many components |
| 7(c)-IV | Lead in PZT-based dielectric ceramics for capacitors | Pb | Active; no lead-free alternative for high-performance |
| 13(b) | Lead in white glasses for optical applications | Pb | Active |
| 15(a) | Lead in solder for flip chip packages (with ≤80µm pitch) | Pb | Active; under review |
| 34 | Lead in cermet-based trimmer potentiometer elements | Pb | Expired/revoked in some cases |

### 3.4 Phthalate Compliance Challenges

```mermaid
flowchart TB
    subgraph "Where Phthalates Hide"
        PVC[PVC Cables & Wires<br/>━━━━━━━━━━━<br/>Plasticizer in insulation/jacket<br/>Can be 20-40% by weight<br/>Most common source]
        
        GASKET[Gaskets & Seals<br/>━━━━━━━━━━━<br/>Flexible rubber/PVC<br/>O-rings; enclosure seals<br/>Often overlooked]
        
        LABELS[Labels & Printing<br/>━━━━━━━━━━━<br/>Adhesive layers<br/>Flexible label materials<br/>Transfer from packaging]
        
        HOUSING[Housings & Enclosures<br/>━━━━━━━━━━━<br/>Flexible PVC parts<br/>Soft-touch coatings<br/>Flexible housings]
        
        PACK[Packaging (indirect)<br/>━━━━━━━━━━━<br/>PVC packaging<br/>that contacts product<br/>Transfer contamination]
    end
    
    subgraph "Testing Challenges"
        SCREENING[Screening Method<br/>━━━━━━━━━━━<br/>XRF cannot detect<br/>phthalates! (organic<br/>compounds; no characteristic<br/>X-ray emission)]
        
        LAB[Lab Analysis Required<br/>━━━━━━━━━━━<br/>GC-MS per IEC 62321-8<br/>Solvent extraction +<br/>gas chromatography<br/>Cost: €150-400 per sample]
        
        CROSS[Cross-Contamination<br/>━━━━━━━━━━━<br/>Phthalates can migrate<br/>from one material to another<br/>(packaging → product;<br/>PVC cable → housing)]
    end
    
    PVC & GASKET & LABELS & HOUSING & PACK --> SCREENING
    SCREENING -->|"XRF ineffective<br/>for organics"| LAB
    LAB --> CROSS
```

---

## Chapter 4 — Implementation Guide

### 4.1 RoHS Compliance Program Structure

| Phase | Activities | Deliverables |
|:-----:|-----------|-------------|
| **1. Scope determination** | Identify which products are EEE; which categories; which markets; identify exclusions (military; space; large-scale industrial) | Product scope register; market applicability matrix |
| **2. Supply chain management** | Require supplier declarations; collect material data (IPC-1752A, FMD); supplier qualification for RoHS; contracts | Supplier declaration database; approved supplier list (RoHS-qualified) |
| **3. Material assessment** | Screen all components/materials for restricted substances; identify risk items; determine exemption applicability | Compliance status per BOM item; exemption register |
| **4. Testing/verification** | XRF screening for metals/halogens; GC-MS for phthalates; IEC 62321 lab testing for high-risk items; incoming inspection | Test reports; screening results; compliance evidence |
| **5. Design controls** | DfE checklist in design reviews; restricted substance check at component selection; change control triggers re-assessment | Design review records; component approval process; change impact assessment |
| **6. Technical documentation** | Per EN 50581: manufacturer declarations; supplier evidence; test results; risk assessment documentation | Technical file (per product family); readily available for market surveillance |
| **7. Declaration of Conformity** | RoHS DoC per Annex VI (can be combined with other CE directives); CE marking | Signed DoC per product/model; CE mark applied to product |
| **8. Market surveillance readiness** | Document retention (10 years from last product placed on market); response plan for authority inquiries; recall plan | Document archive; surveillance response procedure |

### 4.2 EN 50581 — Technical Documentation for RoHS

| Evidence Level | Method | When to Use | Strength |
|:-:|---------|-------------|:--------:|
| **Level 1** | Material declarations from suppliers (contractual; supplier-signed) | All components; primary evidence method | Medium |
| **Level 2** | Analytical testing results (XRF, GC-MS per IEC 62321) | High-risk components; verification; incoming inspection | High |
| **Level 3** | Supplier certifications (ISO 14001; IECQ QC 080000; RoHS certificates) | Supporting evidence; supplier qualification | Low (alone) |
| **Level 4** | Reliable information (industry knowledge; substance unlikely in material type) | Low-risk items; e.g., "pure copper wire unlikely to contain phthalates" | Low |

**EN 50581 requires a risk-based approach:**
- **High risk** (substance likely present): Analytical testing (Level 2) required
- **Medium risk** (substance possible): Material declarations (Level 1) with periodic verification (Level 2)
- **Low risk** (substance highly unlikely): Reliable information (Level 4) with supplier declaration (Level 1) acceptable

### 4.3 Incoming Material Screening

| Method | Detects | Speed | Cost | Accuracy |
|--------|---------|:-----:|:----:|:--------:|
| **Handheld XRF** | Pb, Hg, Cd, Cr(total), Br | 30-120 seconds per measurement | €30K-60K (instrument) | Screening only; ±20% at low concentrations |
| **Desktop XRF** (ED-XRF) | Same + better sensitivity | 2-10 minutes | €80K-150K | Better; ±10% |
| **Lab XRF** (WD-XRF) | Same with highest precision | 10-30 minutes (prep + analysis) | €150K-300K | Near-definitive for metals |
| **ICP-OES/ICP-MS** | Pb, Hg, Cd, Cr (after digestion) | 1-2 days (sample prep + analysis) | €50-200/sample | Definitive (reference method) |
| **GC-MS** | Phthalates (DEHP, BBP, DBP, DIBP); PBB; PBDE | 1-3 days (extraction + analysis) | €150-400/sample | Definitive |
| **Spot test** | Cr(VI) specifically (diphenylcarbazide) | 5-30 minutes | €5-20/test | Qualitative/semi-quantitative |

---

## Chapter 5 — Certification & Compliance

### 5.1 CE Marking Process for RoHS

```mermaid
flowchart TB
    SCOPE{Is product EEE<br/>and in scope of RoHS?}
    
    EXCL{Any exclusions?<br/>(military; space;<br/>large-scale fixed;<br/>non-road mobile)}
    
    COMPLY[Ensure Compliance<br/>━━━━━━━━━━━<br/>• All homogeneous materials<br/>  below thresholds<br/>• Identify applicable exemptions<br/>• Verify supply chain declarations<br/>• Test where required]
    
    TECH_DOC[Prepare Technical Documentation<br/>(per EN 50581)<br/>━━━━━━━━━━━<br/>• Product description<br/>• Material/substance inventory<br/>• Supplier declarations<br/>• Test reports<br/>• Risk assessment<br/>• Exemption justification]
    
    DOC[EU Declaration of Conformity<br/>(Annex VI)<br/>━━━━━━━━━━━<br/>• Manufacturer name/address<br/>• Product identification (model; type)<br/>• Reference to RoHS Directive<br/>• Reference to harmonized standard (EN 50581)<br/>• Signed by authorized person<br/>• Date and place]
    
    CE[Apply CE Marking<br/>━━━━━━━━━━━<br/>• On product (visible, legible, indelible)<br/>• OR on packaging/accompanying documents<br/>  (if not practical on product)<br/>• Minimum 5mm height]
    
    RETAIN[Retain Documentation<br/>━━━━━━━━━━━<br/>• 10 years after last product<br/>  placed on market<br/>• Available for market surveillance<br/>• Supply chain traceability]
    
    SCOPE -->|"Yes"| EXCL
    SCOPE -->|"No (not EEE)"| STOP[RoHS not applicable]
    EXCL -->|"No exclusion"| COMPLY
    EXCL -->|"Excluded"| STOP
    COMPLY --> TECH_DOC --> DOC --> CE --> RETAIN
```

### 5.2 Exemption Management

| Process Step | Action | Documentation |
|:---:|--------|---------|
| 1 | Identify which exemptions your products use | Exemption register (list per product: which exemptions relied upon) |
| 2 | Track exemption expiry dates (typically 5-7 years) | Calendar/watchlist; alert 24 months before expiry |
| 3 | Monitor renewal status (OEKO-Institut evaluations; Commission decisions) | Track EU Official Journal; stakeholder consultations |
| 4 | Assess alternatives continuously | R&D effort for substitute materials; monitor technology readiness |
| 5 | Plan phase-out (if exemption to be revoked) | Transition plan: timeline, redesign, qualification, re-certification |
| 6 | Apply for renewal (if still technically justified) | Submit evidence: no technically feasible alternative; ongoing R&D for substitution |

### 5.3 Market Surveillance Enforcement

| Action | Authority | Consequence |
|--------|-----------|-------------|
| Document request | Market surveillance authority (MSA) of EU member state | Must provide technical documentation within 10 days |
| Product testing | MSA sends product to test laboratory | If non-compliant: withdrawal; recall; penalties |
| Non-compliance finding | MSA issues non-conformity notice | Must demonstrate compliance or withdraw product |
| Serious non-compliance | Notification to all EU MSAs (RAPEX/Safety Gate) | EU-wide market withdrawal; reputational damage |
| Penalties | Per member state national law | Fines: €10K-€500K+ (varies by country); criminal in some jurisdictions |

---

## Chapter 6 — Regional Context

### 6.1 Global RoHS-Equivalent Regulations

| Region | Regulation | Substances | Scope | Key Difference from EU |
|--------|-----------|:---------:|-------|----------------------|
| **EU** | RoHS 3 (2015/863/EU) | 10 | All EEE (11 categories); CE marking | Reference standard; most comprehensive |
| **China** | SJ/T 11364-2014; GB/T 26572 | 6 (+ expanding) | EEE (per published product catalogs) | Marking-based system (orange/green labels); voluntary certification (CCC) for "e-catalog" products |
| **South Korea** | Act on Resource Circulation of EEE (RoHS K) | 10 (aligned with EU RoHS 3) | EEE (similar scope) | Closely aligned with EU; self-declaration |
| **Japan** | JIS C 0950 (J-MOSS); Green Procurement | 6 (+ expanding toward 10) | 7 specified product categories | Voluntary JIS marking system (cooperative industry approach) |
| **India** | E-Waste Management Rules 2022 (India RoHS) | 10 (same as EU RoHS 3) | EEE (per CPCB schedule) | Recent; enforcement developing; EPR integration |
| **Turkey** | AEEE Regulation (aligned with EU RoHS 2) | 10 | EEE | Direct transposition of EU RoHS |
| **UAE** | ECAS RoHS (Emirates) | 6 (expanding) | EEE products | Based on EU RoHS; ESMA conformity |
| **Taiwan** | CNS 15663 | 6 | Specified product categories | Marking requirement; import controls |
| **US** | No federal RoHS | — | — | State-level only (California; Washington) |
| **California** | RoHS (Health & Safety Code §25214.10.2) | Same as EU original 6 | Covered electronic devices (subset) | Limited scope; criminal penalties |

### 6.2 Multi-Regional Compliance Strategy

```mermaid
graph TB
    subgraph "Design for Global Compliance"
        DESIGN[Design to EU RoHS 3 Standard<br/>(most stringent globally)<br/>━━━━━━━━━━━<br/>• 10 substances compliant<br/>• Meet 0.1%/0.01% thresholds<br/>• Minimize exemption use<br/>• Document per EN 50581]
    end
    
    subgraph "Regional Adaptations"
        EU_A[EU<br/>• CE marking<br/>• EU DoC<br/>• EN 50581 tech doc<br/>• Annex III/IV exemptions]
        CN_A[China<br/>• SJ/T 11364 marking<br/>• Green/orange labels<br/>• Chinese material declarations<br/>• CCC (if applicable)]
        KR_A[South Korea<br/>• Self-declaration<br/>• Korean-language DoC<br/>• KC marking integration]
        IN_A[India<br/>• EPR registration<br/>• CPCB compliance<br/>• Third-party testing<br/>• Import declaration]
    end
    
    DESIGN --> EU_A & CN_A & KR_A & IN_A
```

---

## Chapter 7 — Comparison

### 7.1 RoHS 1 vs. RoHS 2 vs. RoHS 3

| Dimension | RoHS 1 (2002/95/EC) | RoHS 2 (2011/65/EU) | RoHS 3 (EU 2015/863) |
|-----------|:---:|:---:|:---:|
| Substances | 6 | 6 | **10** (+4 phthalates) |
| Scope | 8 categories (closed) | **11 categories (open)** | Same as RoHS 2 |
| CE marking | Not required by RoHS | **Required** (RoHS in CE marking scope) | Same as RoHS 2 |
| DoC required | No | **Yes** (per Annex VI) | Same |
| Categories 8 & 9 | Excluded | Included (with transition) | Fully included |
| Exemption management | Informal | Formal process (Article 5; time-limited; renewable) | Same |
| Review mechanism | None | Substance review methodology (Article 6) | Same |
| Technical documentation | Not specified | EN 50581 (harmonized standard for compliance evidence) | Same |

### 7.2 Lead-Free Solder Alloys Comparison

| Alloy | Composition | Melting Point (°C) | Strengths | Weaknesses | Use Case |
|-------|:-----------:|:------------------:|-----------|-----------|----------|
| **Sn-Pb eutectic** | Sn63/Pb37 | 183 | Best wettability; reliable; proven | **Contains lead** (RoHS banned) | Exempt applications only (military; aerospace; high-Pb exemption) |
| **SAC305** | Sn96.5/Ag3.0/Cu0.5 | 217-220 | Good reliability; industry standard | Higher temp; more expensive; tin whisker risk | General electronics (default lead-free choice) |
| **SAC105** | Sn98.5/Ag1.0/Cu0.5 | 217-227 | Lower cost; better drop shock | Lower thermal fatigue than SAC305; wider pasty range | Consumer electronics (handheld; portable) |
| **SN100C** | Sn/Cu0.7/Ni0.05/Ge | 227 | Low cost (no silver); good joint | Higher melting point; limited for complex assemblies | Wave soldering; cost-sensitive applications |
| **SnBi** (low-temp) | Sn42/Bi58 (eutectic) | 138 | Low temp (suitable for temp-sensitive); energy savings | Brittle; limited thermal cycling performance | Temperature-sensitive components; rework |
| **SnAgBi** | Sn/Ag/Bi (various) | 200-215 | Good wettability; lower than SAC305 | Bismuth embrittlement risk at >3% Bi with Pb contamination | Specific applications; emerging |

### 7.3 RoHS vs. REACH (for electronics)

| Dimension | RoHS | REACH |
|-----------|:---:|:---:|
| Product type | EEE only | **ALL products** (articles, chemicals, mixtures) |
| Mechanism | Ban above threshold | Registration; communication; authorization; restriction |
| Threshold for action | 0.1% / 0.01% in homogeneous material | 0.1% w/w in article (for SVHC communication); varies for restrictions |
| Number of restricted substances | 10 | 250+ SVHCs (candidate list); 60+ authorizations; 70+ restrictions (Annex XVII) |
| Can you still use the substance? | NO (unless exempted) | Sometimes yes (with communication/authorization) |
| Documentation | Technical documentation + DoC | Safety Data Sheets; SVHC communication; SCIP notification |
| CE marking | Required | NOT required for REACH |
| Enforcement | Market surveillance; product withdrawal | ECHA; member state enforcement; market access denied |
| Overlap with RoHS substances? | — | Yes: Pb, Cd, Cr(VI), DEHP, DBP, DIBP all also REACH-relevant |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 RoHS Compliance Decision Tree

```mermaid
flowchart TB
    START[Product Assessment]
    
    EEE{Is product EEE?<br/>(requires electricity<br/>to function)}
    
    SCOPE{In scope?<br/>(not excluded per<br/>Article 2(4))}
    
    BOM[Assess BOM<br/>━━━━━━━━━━━<br/>• List all components<br/>• Identify homogeneous materials<br/>• Check each material vs. 10 substances]
    
    THRESH{Any material<br/>>threshold?<br/>(0.1% or 0.01% Cd)}
    
    EXEMPT{Exemption<br/>available?<br/>(Annex III/IV)}
    
    EXEMPT_VALID{Exemption still<br/>valid? (not expired<br/>or revoked)}
    
    SUBSTITUTE[Must Substitute<br/>━━━━━━━━━━━<br/>• Find compliant alternative<br/>• Qualify new material<br/>• Redesign if needed<br/>• Cannot place on EU market until compliant]
    
    COMPLIANT[Product COMPLIANT<br/>━━━━━━━━━━━<br/>• Prepare technical documentation<br/>• Sign EU DoC<br/>• Apply CE marking<br/>• Place on market]
    
    NOT_EEE[RoHS not applicable<br/>(but check REACH)]
    
    START --> EEE
    EEE -->|No| NOT_EEE
    EEE -->|Yes| SCOPE
    SCOPE -->|Excluded| NOT_EEE
    SCOPE -->|In scope| BOM
    BOM --> THRESH
    THRESH -->|"All below threshold"| COMPLIANT
    THRESH -->|"One or more above"| EXEMPT
    EXEMPT -->|"Yes"| EXEMPT_VALID
    EXEMPT -->|"No exemption"| SUBSTITUTE
    EXEMPT_VALID -->|"Valid"| COMPLIANT
    EXEMPT_VALID -->|"Expired/Revoked"| SUBSTITUTE
```

### 8.2 Supply Chain RoHS Data Flow

```mermaid
graph TB
    subgraph "Tier 2+ Suppliers (Raw Materials)"
        RAW[Raw Material Suppliers<br/>━━━━━━━━━━━<br/>• Metal alloy compositions<br/>• Polymer formulations<br/>• Chemical compositions<br/>• Test certificates]
    end
    
    subgraph "Tier 1 Suppliers (Components)"
        COMP_SUP[Component Suppliers<br/>━━━━━━━━━━━<br/>• Material declarations (IPC-1752A)<br/>• RoHS compliance certificates<br/>• Test reports (XRF/GC-MS)<br/>• Full Material Disclosure<br/>• REACH SVHC declarations]
    end
    
    subgraph "Manufacturer (OEM/EMS)"
        RECEIVE[Incoming Inspection<br/>━━━━━━━━━━━<br/>• XRF screening (risk-based)<br/>• Compare to declarations<br/>• Approve/reject/quarantine]
        
        DB[(Compliance Database<br/>━━━━━━━━━━━<br/>• Per-component status<br/>• Substance concentrations<br/>• Exemptions used<br/>• Declaration validity dates<br/>• Test history)]
        
        ASSESS[Compliance Assessment<br/>━━━━━━━━━━━<br/>• Per-BOM analysis<br/>• Per-product status<br/>• Exemption applicability<br/>• Gap identification]
        
        TECH_FILE[Technical File<br/>(EN 50581)<br/>━━━━━━━━━━━<br/>• Evidence per component<br/>• Risk assessment<br/>• Test reports<br/>• Supplier declarations<br/>• Exemption justification]
    end
    
    subgraph "Output"
        DOC_OUT[EU DoC + CE Marking<br/>━━━━━━━━━━━<br/>• Signed declaration<br/>• CE mark on product<br/>• Retained 10 years]
        
        CUSTOMER[Customer Communication<br/>━━━━━━━━━━━<br/>• RoHS compliance statement<br/>• Material declarations to OEM customers<br/>• Exemption information]
    end
    
    RAW --> COMP_SUP
    COMP_SUP --> RECEIVE
    RECEIVE --> DB
    DB --> ASSESS --> TECH_FILE
    TECH_FILE --> DOC_OUT & CUSTOMER
```

---

## Chapter 9 — Case Studies

### 9.1 Case Study: Lead-Free Transition in Automotive Electronics

| Aspect | Detail |
|--------|--------|
| Company | Automotive Tier 1 (ECU manufacturer); 150+ ECU variants; operating temperature -40°C to +150°C; 15-year design life |
| Challenge | Automotive exemptions in RoHS were broad until 2019 (ELV Directive handled automotive; but ECUs as standalone EEE fall under RoHS for aftermarket). Customer OEMs also pushing lead-free for corporate sustainability. Reliability requirements far exceed consumer electronics. |
| Technical Issues | (1) SAC305 at high temperature: reduced creep resistance vs. SnPb at >125°C; thermal cycling reliability concern (underhood). (2) Tin whisker risk in high-humidity; tin whiskers can short fine-pitch components. (3) Component availability: some automotive components only available in SnPb (high-reliability ceramic capacitors, some power modules). (4) Mixed assembly: some components must remain SnPb (exempt); rest must be lead-free → forward/backward compatibility challenges. |
| Solution | (1) **Solder alloy selection**: SAC305 for standard assembly; SAC387 (higher Ag for creep resistance) for high-temp applications; innolot (SAC+Bi+Sb+Ni) for extreme underhood. (2) **Tin whisker mitigation**: conformal coating on all boards (acrylic/urethane); minimum 2µm nickel underplate on all tin-plated leads; annual inspection program. (3) **Qualification program**: 2,000 thermal cycles (-40/+150°C) with post-test cross-sectioning; vibration testing (random + sine sweep); 1,000hr HAST (130°C/85%RH). (4) **Exemption tracking**: identified 12 RoHS exemptions relied upon; transition plan for each with 18-month lead time. (5) **Mixed assembly controls**: clear zone separation on production line (SnPb exempt area vs. lead-free); contamination prevention; XRF verification of solder paste. |
| Outcome | Full lead-free transition for non-exempt assemblies achieved. Reliability validation passed (>2,000 cycles; no solder-related failures). Exemption register maintained for remaining SnPb components (high-reliability flip chip; power modules under exemption 7(a)). |

### 9.2 Case Study: Phthalate Non-Compliance Discovery

| Aspect | Detail |
|--------|--------|
| Company | Industrial instrument manufacturer (Category 9); ~80 product families; global market |
| Discovery | During routine material declaration update (ahead of 2021 phthalate enforcement for Cat 8/9), one supplier (cable assemblies) declared DEHP at 0.3% in PVC jacket material (above 0.1% threshold). Supplier had previously declared "RoHS compliant" without specifically testing for phthalates (declarations were for original 6 substances only). |
| Impact | Affected: 35 product families using this cable type; all products potentially non-compliant once phthalate restriction applies; 18 months to enforcement date; estimated 500,000 units in field (not affected; enforcement is prospective); new production must be compliant from enforcement date. |
| Corrective Actions | (1) **Immediate**: identified all products using affected cable (BOM search); notified supply chain team; set non-compliance flag in compliance database. (2) **Short-term (3 months)**: sourced alternative cables (LSZH jacket: halogen-free, phthalate-free, better flame properties); validated electrical performance (impedance; capacitance; signal integrity for data cables); mechanical qualification (bend radius; pull strength; temperature range). (3) **Medium-term (6-12 months)**: design change implementation (ECO for each affected product); update BOMs; update technical documentation; new XRF + GC-MS testing of replacement cables; update supplier declarations; inform Notified Body (for certified products). (4) **Systemic fix**: updated supplier qualification process (now explicitly requires phthalate declarations; GC-MS test report for any PVC/rubber component); updated incoming inspection (added GC-MS spot-check for flexible materials); supplier audit checklist updated for phthalate awareness. |
| Result | All products compliant before enforcement date. Total cost: ~€300K (engineering, qualification, testing, premium for alternative cables). No market surveillance action (proactive correction). Lesson: RoHS compliance declarations must be specific to the CURRENT restricted substance list (not just "RoHS compliant" generically). |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **Additional substances (RoHS 4?)** | 2025-2028 | TBBPA (flame retardant); medium-chain chlorinated paraffins (MCCP); beryllium; nickel? Under evaluation |
| **Exemption phase-outs** | Rolling | Exemptions for lead in high-temp solder (7a); lead in glass/ceramic (7c); lead in alloys — all under review; possible revocation/restriction |
| **ESPR integration** | 2027+ | RoHS may be absorbed into or aligned with EU Ecodesign for Sustainable Products Regulation; Digital Product Passport |
| **Circular economy requirements** | 2025-2030 | Recyclability design requirements; material recovery consideration alongside substance restriction |
| **Lead-free alternatives improvement** | Ongoing | Better high-reliability lead-free solders; lead-free piezoelectrics; lead-free glass; reducing need for exemptions |
| **Enforcement strengthening** | Now | More cross-border market surveillance; RAPEX notifications increasing; online marketplace enforcement |
| **Substance testing innovation** | Now | AI-assisted material screening; rapid handheld phthalate detection (FTIR/Raman); in-line testing |
| **Digital compliance** | 2026+ | Machine-readable material declarations (IPC-1752 XML); automated compliance checking; blockchain material traceability |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What are the 10 RoHS-restricted substances and their thresholds?  
**A:** Lead (0.1%), Mercury (0.1%), Cadmium (0.01% — most stringent), Hexavalent Chromium (0.1%), PBB (0.1%), PBDE (0.1%), DEHP (0.1%), BBP (0.1%), DBP (0.1%), DIBP (0.1%). The threshold is per homogeneous material by weight. The first 6 were restricted from RoHS 1 (2006); the 4 phthalates were added by RoHS 3 (enforced 2019/2021).

**Q2:** What is a "homogeneous material" and why does it matter?  
**A:** A homogeneous material is one that cannot be mechanically separated into different materials. Examples: a specific alloy; a specific polymer compound; a single plating layer. The RoHS threshold applies per homogeneous material, NOT per product or component. This means each individual material layer in a multi-material component must independently meet the threshold. For example: a connector pin with nickel plating + tin plating + copper base → each layer is separately assessed against 0.1%/0.01% limits.

### Tier 2: Mid-Level

**Q3:** You discover that a component supplier's RoHS declaration is 4 years old and only covers the original 6 substances. What actions do you take?  
**A:** (1) Immediately flag this as a compliance gap (declaration does not cover phthalates added in RoHS 3). (2) Contact supplier to request updated declaration covering all 10 substances per current RoHS 3 requirements. (3) Assess risk of the component containing phthalates (material type: PVC? rubber? flexible? → higher phthalate risk). (4) If high-risk material (PVC cable; flexible gasket), request GC-MS test report from supplier or send component for independent testing. (5) Pending updated evidence, consider: quarantining incoming stock for XRF (metals) + send sample for GC-MS (phthalates); accepting risk-based on material type (solid metal or ceramic = virtually no phthalate risk); blocking new orders until updated declaration received. (6) Update supplier management process to prevent recurrence: contractual requirement for declarations to be updated within 6 months of any RoHS amendment; annual renewal requirement; specific substance list reference in declaration.

### Tier 3: Senior

**Q4:** Design a global RoHS/substance compliance strategy for a company selling electronics in 25+ countries with varying substance restriction laws.  
**A:** [Comprehensive answer covering: (1) "Design to highest standard" principle — design all products to EU RoHS 3 (most restrictive) as global baseline. (2) Material composition database as strategic asset — full material declarations (IPC-1752A FMD) from all suppliers; enables rapid assessment against any current or future regulation. (3) Regulatory intelligence: dedicated tracking of all substance restriction changes globally (EU RoHS; China RoHS; India E-Waste Rules; Korea; state-level US; REACH candidate list updates); automated impact assessment when new substances proposed. (4) Multi-regional documentation: single technical file that satisfies all markets (EN 50581 + regional addenda for China marking; Korea declaration; India CPCB); regional compliance matrix per product. (5) Exemption management: global exemption register; proactive substitution R&D; avoid exemptions where feasible (reduce regulatory risk). (6) Supply chain: contractual flow-down requirements that encompass global requirements; single supplier questionnaire covering all regions; audit program. (7) Testing: centralized test lab (or preferred external lab) with global scope; risk-based testing program; incoming inspection. (8) Organization: central compliance team for methodology/tools/regulatory intelligence; regional regulatory leads for country-specific filings; product-line compliance owners for device-specific expertise.]

---

## Chapter 12 — Cheat Sheet & Quick Reference

### RoHS 3 Quick Compliance Checklist

```
□ Product is EEE and in scope (not excluded)?
□ All 10 substances assessed per homogeneous material?
□ All materials below thresholds (0.1%; Cd: 0.01%)?
□ Any exemptions used → documented and valid?
□ Supplier declarations current (covering all 10 substances)?
□ High-risk items analytically tested (IEC 62321)?
□ Technical documentation prepared (per EN 50581)?
□ EU Declaration of Conformity signed?
□ CE marking applied to product?
□ Documentation retained (10 years from last placement)?
```

### RoHS Exclusions (NOT in scope)

```
• Military equipment (for national security)
• Equipment for use in space
• Large-scale stationary industrial tools
• Large-scale fixed installations
• Means of transport (persons/goods) — except non-type-approved EVs
• Non-road mobile machinery for professional use only
• Active implantable medical devices
• R&D equipment (not available on market)
• Equipment intended to be sent into space
```

### Testing Decision Matrix

```
IF material type is:
  Metal/alloy         → XRF screening (Pb, Cd, Hg, Cr)
  Polymer (rigid)     → XRF (metals) + GC-MS if colored/flame-retarded (phthalates, PBB/PBDE)
  Polymer (flexible)  → XRF (metals) + GC-MS REQUIRED (phthalate risk HIGH)
  PVC                 → GC-MS MANDATORY (phthalate risk VERY HIGH)
  Glass/ceramic       → XRF (Pb, Cd)
  Plating/coating     → XRF (Pb, Cd, Cr) + spot test (Cr⁶⁺ if chromium detected)
  Solder              → XRF or ICP (Pb verification)
  Cable/wire          → XRF (conductor: Pb) + GC-MS (insulation: phthalates)
```

### Key Harmonized Standards

```
EN 50581:2012   — Technical documentation for assessment of EEE (compliance evidence)
IEC 62321-1     — General requirements for substance determination
IEC 62321-3-1   — Screening: XRF method
IEC 62321-4     — Determination of Pb, Cd, Cr (total) by CV-AAS, ICP-OES, ICP-MS
IEC 62321-5     — Determination of Cd, Pb, Cr (polymers/electronics) 
IEC 62321-6     — Determination of PBB and PBDE (GC-MS)
IEC 62321-7-1   — Determination of Cr(VI) — metal coatings
IEC 62321-7-2   — Determination of Cr(VI) — polymers/electronics
IEC 62321-8     — Determination of phthalates (DEHP, BBP, DBP, DIBP) by GC-MS
IEC 62321-9     — Determination of Hg by CV-AAS
```

---

*End of Document — 01_RoHS_Directive_Compliance.md*
