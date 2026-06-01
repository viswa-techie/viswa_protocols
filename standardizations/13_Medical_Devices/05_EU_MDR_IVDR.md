# EU MDR 2017/745 & IVDR 2017/746 — European Medical Device Regulations

**Topic:** EU regulatory framework for medical devices and in-vitro diagnostic medical devices  
**Standard:** Regulation (EU) 2017/745 (MDR) & Regulation (EU) 2017/746 (IVDR)  
**SDO:** European Commission; competent authorities of EU Member States  
**Audience:** Regulatory affairs professionals, quality managers, clinical affairs specialists, manufacturers placing devices on EU market  
**Prerequisites:** ISO 13485, CE marking fundamentals, understanding of Notified Bodies

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 1990 | Active Implantable Medical Devices Directive (AIMDD 90/385/EEC) | First EU medical device legislation |
| 1993 | Medical Devices Directive (MDD 93/42/EEC) | Established CE marking; classification rules; conformity assessment |
| 1998 | In-Vitro Diagnostic Directive (IVDD 98/79/EC) | IVD devices regulated; List A/B system |
| 2010 | PIP breast implant scandal | Revealed weaknesses in Notified Body oversight and surveillance |
| 2012 | European Commission proposal for new regulations | Response to PIP scandal; harmonization across EU |
| 2017 | **MDR (EU) 2017/745 published** | Replaces MDD and AIMDD; major overhaul |
| 2017 | **IVDR (EU) 2017/746 published** | Replaces IVDD; risk-based classification for IVDs |
| 2020 | MDR delayed by 1 year (COVID-19) | Application date moved to May 26, 2021 |
| 2021 | **MDR application date: May 26, 2021** | All new devices must comply; transition for existing MDD devices |
| 2022 | **IVDR application date: May 26, 2022** | All new IVDs must comply |
| 2023 | MDR transition period extended | Regulation (EU) 2023/607: extended timelines for legacy devices |
| 2024-2028 | Transitional provisions | Class III/implantable: Dec 2027; Class IIb: Dec 2028; Class IIa/I (sterile/measuring): Dec 2028 |

### 1.2 MDD vs. MDR — Paradigm Shift

| Dimension | MDD (Directive) | MDR (Regulation) |
|-----------|:---:|:---:|
| Legal instrument | Directive (transposed into national law differently) | **Regulation** (directly applicable in all EU Member States) |
| Classification | 4 classes (I, IIa, IIb, III) | Same 4 classes + new rules (Rule 11 for software) |
| Clinical evidence | Clinical evaluation required | **Strengthened**: clinical investigation for Class III/implantable unless justified; PMCF mandatory |
| Post-market surveillance | General requirement | **Mandatory PMS plan, PSUR, PMCF** for all classes |
| Notified Body oversight | Variable oversight | **Strengthened**: unannounced audits; technical documentation review; NB designation strengthened |
| Traceability | No UDI requirement | **UDI mandatory** (EUDAMED database) |
| Transparency | Limited public information | **EUDAMED**: device registration, certificates, vigilance, clinical investigations all public |
| Economic operators | Manufacturer responsibility | **All economic operators** (importer, distributor, authorized representative) have defined obligations |
| Software classification | Often classified low (Rule 12 under MDD) | **Rule 11**: software generally classified higher (Class IIa minimum if clinical decision support) |

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 MDR Structure (123 Articles + 17 Annexes)

| Chapter | Articles | Content |
|:-------:|:--------:|---------|
| I | 1-4 | Scope, definitions |
| II | 5-24 | Making available/putting into service; obligations of economic operators |
| III | 25-34 | Identification and traceability (UDI) |
| IV | 35-50 | **Notified Bodies** — designation, monitoring, functioning |
| V | 51-60 | **Classification and conformity assessment** |
| VI | 61-82 | **Clinical evaluation and clinical investigations** |
| VII | 83-86 | **Post-market surveillance, vigilance, market surveillance** |
| VIII | 87-92 | Vigilance |
| IX | 93-100 | Market surveillance |
| X | 101-108 | Specific devices (custom-made; investigation; compassionate use) |
| XI | 109-112 | EUDAMED |

### 2.2 Key Annexes

| Annex | Title | Content |
|:-----:|-------|---------|
| **I** | General Safety and Performance Requirements (GSPRs) | 23 chapters of requirements (replaces Essential Requirements) |
| **II** | Technical documentation | Structure of technical file |
| **III** | Technical documentation — post-market surveillance | PMS plan; PSUR; PMCF |
| **IV** | EU Declaration of Conformity | Content requirements |
| **V-XI** | CE marking; conformity assessment procedures | Different routes per device class |
| **VIII** | Classification rules | 22 rules for device classification |
| **IX** | Conformity assessment based on QMS + technical documentation | Full QMS route (most common for Class IIa, IIb, III) |
| **X** | Conformity assessment based on type-examination | Type testing route |
| **XI** | Conformity assessment based on product verification | Individual verification route |
| **XIV** | Clinical evaluation | Clinical evaluation methodology; PMCF |
| **XV** | Clinical investigations | Requirements for clinical studies |
| **XVI** | Non-medical devices (aesthetic) | Devices without medical purpose (listed: contact lenses, liposuction, brain stimulation, etc.) |
| **XVII** | Correlation table (MDR ↔ MDD) | Mapping between old and new requirements |

### 2.3 MDR Classification Rules (Annex VIII)

| Rule | Scope | Examples | Class |
|:----:|-------|---------|:-----:|
| 1-4 | Non-invasive devices | Wound dressings; collection devices; blood bags | I to IIb |
| 5-8 | Invasive devices | Surgical instruments; implants; contact lenses | I to III |
| 9-13 | Active devices | Software; radiation; energy delivery; monitoring | IIa to III |
| **11** | **Software** | Clinical decision support; diagnostic; monitoring | **IIa to III** |
| 14-22 | Special rules | Contraceptives; disinfectants; nanomaterials; substances | Various |

**Rule 11 (Software) — Critical Change from MDD:**

| Software Intended Purpose | MDR Classification |
|--------------------------|:------------------:|
| Software intended to provide information used to take decisions with diagnosis or therapeutic purposes | **Class IIa** (minimum) |
| Software intended to monitor physiological processes | **Class IIa** (minimum) |
| Software decisions that may cause death or irreversible deterioration of health | **Class III** |
| Software decisions that may cause serious deterioration of health or surgical intervention | **Class IIb** |
| All other diagnostic/monitoring software | **Class IIa** |
| Software driving/influencing a device | Same class as the device |
| Software NOT providing decisions for diagnosis/therapy (general wellness, administrative) | **Class I** |

---

## Chapter 3 — Technical Deep Dive

### 3.1 General Safety and Performance Requirements (GSPRs — Annex I)

| Chapter | GSPR Area | Key Requirements |
|:-------:|-----------|-----------------|
| I (1-9) | General requirements | Risk management; design for safety; benefit-risk acceptable; performance as intended |
| II (10) | Chemical, physical, biological properties | Biocompatibility; toxicology; material safety |
| II (11) | Infection and microbial contamination | Sterility; contamination control; tissues/cells safety |
| II (12) | Devices with energy source | Electrical safety (reference IEC 60601); EMC; radiation protection |
| II (13) | Devices with diagnostic/measuring function | Accuracy; precision; stability; measurement standards |
| II (14) | Protection against radiation | Justification; dose optimization; indicators; controls |
| II (15) | Electronic programmable systems (software) | IEC 62304; state-of-art development; V&V; cybersecurity |
| II (16) | Active devices with diagnostic function | Sensitivity; specificity; accuracy; repeatability |
| II (17) | **Cybersecurity** | IT security requirements; protection against unauthorized access; verification of software integrity |
| III (18-22) | Requirements for specific device groups | Implantable; devices for administration; derivatives |
| III (23) | Information on label and instructions | Labeling requirements; symbols; IFU |

### 3.2 Technical Documentation (Annex II)

| Section | Content | Detail |
|:-------:|---------|--------|
| 1 | Device description and specification | Intended purpose; patient population; indications; contraindications; working principle; variants |
| 2 | Information supplied by manufacturer | Labels; IFU; marketing materials; UDI |
| 3 | Design and manufacturing information | Design stages; manufacturing processes; suppliers; sterilization; quality system |
| 4 | **General Safety and Performance Requirements** | GSPR checklist: for EACH GSPR, state if applicable; method of demonstrating conformity; harmonized standards used; evidence location |
| 5 | Benefit-risk analysis and risk management | ISO 14971 risk management file; benefit-risk determination |
| 6 | **Product verification and validation** | Pre-clinical testing (biocompatibility, electrical safety, performance, shelf life, software V&V); all test reports |
| 6.1 | Clinical evidence | Clinical evaluation report (CER); clinical investigation data; PMCF plan; literature review |

### 3.3 Clinical Evaluation (Annex XIV / MEDDEV 2.7/1 Rev 4 → MDCG Guidelines)

| Route | When Used | Requirements |
|-------|-----------|-------------|
| **Literature-based** | Well-established technology; substantial literature; equivalence claim | Systematic literature review; equivalence demonstration (technical, biological, clinical); gap analysis; PMCF plan |
| **Equivalence** | Claiming equivalence to existing device | Demonstrate TECHNICAL equivalence (design, materials, specifications) + BIOLOGICAL (contact, materials) + CLINICAL (same clinical condition, same intended purpose, same population); must have CONTRACT with equivalent device manufacturer (if different manufacturer) |
| **Clinical investigation** | New technology; Class III implantable; insufficient clinical data | Per Annex XV; Ethics committee approval; informed consent; clinical investigation plan; sponsor obligations; serious adverse event reporting |
| **Combination** | Most common | Literature + own clinical data (registry, PMCF study) |

**Equivalence Requirements (MDR vs. MDD):**

| Criterion | MDD (Previous) | MDR (Current) |
|-----------|:---:|:---:|
| Technical equivalence | Required | Required (stricter: same materials, design specifications) |
| Biological equivalence | Required | Required |
| Clinical equivalence | Required | Required |
| Access to competitor's technical documentation | Not explicitly required | **Required** (contract/access to sufficient data to justify equivalence) |
| Implantable/Class III equivalence | Possible | **Severely restricted** (clinical investigation generally required unless specific justification) |

### 3.4 Post-Market Surveillance (PMS) — Annex III

| Element | Class I | Class IIa | Class IIb/III |
|---------|:-------:|:---------:|:-------------:|
| PMS Plan | ✅ | ✅ | ✅ |
| PMS Report | ✅ (updated as needed) | — | — |
| Periodic Safety Update Report (PSUR) | — | ✅ (every 2 years) | ✅ (annually) |
| Post-Market Clinical Follow-up (PMCF) | If applicable | ✅ (PMCF plan required) | ✅ (PMCF plan + PMCF evaluation report) |
| Summary of Safety and Clinical Performance (SSCP) | — | — | ✅ (Class III and implantable; published in EUDAMED) |
| Trend reporting (Article 88) | ✅ | ✅ | ✅ |
| Vigilance (serious incidents) | ✅ | ✅ | ✅ |

### 3.5 Vigilance & Incident Reporting

| Event | Reporting Timeline | To Whom |
|-------|:-----------------:|---------|
| Serious incident (death/serious deterioration of health) | **15 days** (serious public health threat: **2 days**; death/unexpected serious deterioration: **10 days**) | Competent authority of Member State where incident occurred |
| Field Safety Corrective Action (FSCA) | Without delay; before or simultaneous with action | Competent authority; NB; economic operators |
| Field Safety Notice (FSN) | With FSCA | Users/patients affected |
| Trend report (statistically significant increase in events) | Without undue delay | Competent authority |

---

## Chapter 4 — Implementation Guide

### 4.1 MDR Conformity Assessment Routes

```mermaid
graph TB
    subgraph "Class I"
        CI[Class I (non-sterile, non-measuring)<br/>━━━━━━━━━━━━━<br/>Self-certification<br/>Annex IV: EU DoC<br/>No Notified Body needed<br/>• Technical documentation<br/>• QMS (basic)<br/>• PMS system]
    end
    
    subgraph "Class I (sterile/measuring/reusable surgical)"
        CIS[Class I sterile/measuring<br/>━━━━━━━━━━━━━<br/>NB involvement for:<br/>• Sterile: manufacturing<br/>• Measuring: metrological aspects<br/>Annex IX Chapter I + III, or<br/>Annex XI Part A]
    end
    
    subgraph "Class IIa"
        CIIA[Class IIa<br/>━━━━━━━━━━━━━<br/>Annex IX (QMS + TD assessment)<br/>— NB audits QMS<br/>— NB assesses TD per representative sample<br/>OR Annex XI Part A (product verification)]
    end
    
    subgraph "Class IIb"
        CIIB[Class IIb<br/>━━━━━━━━━━━━━<br/>Annex IX (QMS + TD assessment)<br/>— NB audits QMS<br/>— NB assesses TD per device<br/>(or representative sample for<br/>non-implantable Class IIb)<br/>OR Annex X + XI Part A]
    end
    
    subgraph "Class III"
        CIII[Class III<br/>━━━━━━━━━━━━━<br/>Annex IX (QMS + TD assessment)<br/>— NB audits full QMS<br/>— NB assesses EACH device TD<br/>— NB reviews clinical evidence<br/>— May request expert panel opinion<br/>Clinical investigation usually required]
    end
    
    CI --- CIS --- CIIA --- CIIB --- CIII
```

### 4.2 Technical Documentation Structure

| Section | Content Requirements | Tips |
|:-------:|---------------------|------|
| Cover | Device name; manufacturer; UDI-DI; classification; NB number; version control | Living document; version every major update |
| 1. Description | Intended purpose; indications; contraindications; patient population; user profile; variants/accessories; predecessor; working principle; images/diagrams | Be SPECIFIC — "intended to diagnose X in population Y by measuring Z" |
| 2. Labeling | Labels (all variants); IFU; packaging artwork; symbols per ISO 15223-1 | Include ALL languages for target markets |
| 3. Design & Manufacturing | Development stages; design verification; manufacturing process; critical processes; suppliers (critical); sterilization; quality system ISO 13485 certificate | Include process flow diagram; validation records summary |
| 4. GSPR Checklist | For each of 23 GSPR chapters: Applicable? → If yes: harmonized standard used? → Verification method → Evidence reference | Use GSPR table template; cross-reference to test reports |
| 5. Risk Management | Complete ISO 14971 risk management file; risk-benefit analysis; residual risk evaluation | Include risk management plan, FMEA/FTA, risk analysis, risk evaluation, benefit-risk, risk management report |
| 6.1. Pre-clinical | Biocompatibility (ISO 10993); electrical safety (IEC 60601); EMC; performance testing; shelf life/stability; software V&V (IEC 62304); usability (IEC 62366) | Full test reports or summaries with reference to reports |
| 6.2. Clinical | Clinical Evaluation Report (CER); PMCF plan; clinical investigation reports (if applicable); literature review | CER is CRITICAL — most common deficiency cited by NB |

### 4.3 Notified Body Interaction

| Activity | Frequency | What Happens |
|----------|:---------:|-------------|
| Initial certification audit | Once (QMS + TD) | On-site QMS audit (ISO 13485); TD review for each device (Class III) or sample (IIa/IIb); clinical evidence review |
| Surveillance audit | Annual | On-site audit of QMS; sample of TD; review PMS/PSUR/vigilance; may include **unannounced audit** |
| Unannounced audit | At least once per 5-year certificate cycle | Short-notice or no-notice visit; check manufacturing; sample testing; label/packaging verification |
| Certificate renewal | Every 5 years | Full re-assessment (QMS + TD); updated clinical evidence; PMCF results |
| Significant change | As needed | Notify NB of design changes; NB evaluates if supplement or new certification needed |
| Expert panel consultation | Class III implantable (specific types) | NB must consult EU expert panel before issuing certificate |

---

## Chapter 5 — Certification & CE Marking

### 5.1 CE Marking Process

| Step | Activity | Output |
|:----:|----------|--------|
| 1 | Determine classification (Annex VIII rules) | Classification justification document |
| 2 | Identify conformity assessment route (Annex IX/X/XI) | Conformity assessment strategy |
| 3 | Prepare technical documentation (Annex II) | Complete technical file |
| 4 | Implement QMS (ISO 13485 + MDR requirements) | ISO 13485 certificate (from NB) |
| 5 | Conduct clinical evaluation (Annex XIV) | Clinical Evaluation Report (CER) |
| 6 | Establish PMS system (Article 83-86; Annex III) | PMS plan; PMCF plan |
| 7 | Apply to Notified Body | Application with fees |
| 8 | NB audit and TD review | Audit report; TD assessment report |
| 9 | NB issues EU certificate (Annex IX or equivalent) | MDR certificate (5-year validity) |
| 10 | Manufacturer draws up EU Declaration of Conformity | DoC (Annex IV) |
| 11 | Affix CE marking + NB number | CE marking on device/packaging |
| 12 | Register in EUDAMED (UDI; device registration; economic operators) | EUDAMED entries |

### 5.2 EUDAMED Modules

| Module | Content | Status |
|:------:|---------|:------:|
| Actor registration | Manufacturers, authorized reps, importers — SRN (Single Registration Number) | Active |
| UDI/Device registration | Device identification; UDI-DI; basic UDI-DI | Active |
| Notified Bodies and certificates | NB designation; certificates issued/suspended/withdrawn | Active |
| Clinical investigations | Registration of clinical investigations | Active |
| Vigilance | Serious incident reports; FSCAs; FSNs; trend reports | In development |
| Market surveillance | Competent authority actions; information exchange | In development |

---

## Chapter 6 — Regional & Domain Variants

### 6.1 MDR vs. IVDR Key Differences

| Dimension | MDR 2017/745 | IVDR 2017/746 |
|-----------|:---:|:---:|
| Scope | Medical devices | In-vitro diagnostic medical devices |
| Classification system | 4 classes (I, IIa, IIb, III); 22 rules | 4 classes (A, B, C, D); 7 rules |
| Highest risk examples | Class III: cardiac implants; hip joints | Class D: HIV tests; blood typing; companion diagnostics |
| Clinical evidence | Clinical evaluation + PMCF | **Performance evaluation** + PMPF (Post-Market Performance Follow-up) |
| Application date | May 26, 2021 | May 26, 2022 |
| Biggest change from predecessor | Strengthened clinical evidence; NB oversight | **Risk-based classification** (replaced List A/B with 4-class system; many IVDs reclassified upward) |
| NB requirement | Class IIa and above | Class B and above (majority of IVDs now need NB) |
| EU Reference Laboratory | Not applicable | **Yes** — for Class D and some C (batch verification; performance evaluation review) |

### 6.2 National Competent Authorities

| Country | Competent Authority | Role |
|---------|--------------------|----|
| Germany | BfArM (devices); PEI (IVDs) | Vigilance; clinical investigation approval; market surveillance |
| France | ANSM | Vigilance; market surveillance; clinical investigation |
| UK (post-Brexit) | MHRA | Independent UK framework (UKCA marking; own regulations planned 2025+) |
| Italy | Ministry of Health | Registration; vigilance; market surveillance |
| Netherlands | IGJ (Healthcare Inspectorate) | Market surveillance; inspection |
| Ireland | HPRA | Competent authority |
| EU-wide | MDCG (Medical Device Coordination Group) | Guidance documents; harmonized interpretation; coordination |

---

## Chapter 7 — Comparison

### 7.1 MDR vs. FDA System

| Dimension | EU MDR | US FDA |
|-----------|:---:|:---:|
| Legal basis | EU Regulation (directly applicable) | Federal Food, Drug, and Cosmetic Act + CFR |
| Classification | I, IIa, IIb, III (rule-based) | I, II, III (product-code-based; predicate) |
| Pre-market for highest risk | Notified Body certification (CE) | FDA PMA (direct FDA review) |
| Pre-market for medium risk | Notified Body certification (CE) | 510(k) (demonstrate substantial equivalence) |
| Clinical evidence | CER mandatory; clinical investigation for Class III implantable | Clinical data for PMA; 510(k) may or may not require clinical |
| Post-market surveillance | Mandatory PMS plan; PSUR; PMCF | MDR reporting; no mandatory PSUR equivalent (but 522 orders) |
| Quality system | ISO 13485 + MDR-specific requirements | 21 CFR 820 (QMSR from 2026 → ISO 13485) |
| Software | Rule 11 (often IIa-III) | Class II (most SaMD); AI/ML guidance; De Novo |
| UDI | EU UDI (EUDAMED) | US UDI (GUDID) — both aligned to IMDRF UDI guidance |
| Transparency | EUDAMED (public access to certificates, vigilance) | FDA databases (MAUDE, 510(k) summaries, PMA summaries) |
| Recall/FSCA | FSCA + FSN; competent authority oversight | FDA recall classification (I/II/III); firm-initiated or mandated |

### 7.2 MDR vs. MDD (Key Changes Summary)

| Area | MDD Impact | MDR Impact |
|------|-----------|------------|
| Software classification | Rule 12 → mostly Class I or IIa | **Rule 11 → mostly IIa, IIb, or III** |
| Clinical evidence for equivalence | Could claim equivalence without access to competitor data | **Must have contract/access to equivalent device data** (effectively prevents predicate claims for different manufacturers) |
| Implantable Class III | NB certification based on clinical evaluation | **Expert panel consultation**; clinical investigation strongly expected |
| Person Responsible for Regulatory Compliance (PRRC) | Authorized representative | **Dedicated PRRC** required (Article 15) with specific qualifications |
| Implant cards | Not required | **Required for implants** (patient receives card with device info) |
| Unique Device Identification (UDI) | Not required | **Mandatory** (timeline based on class) |
| Post-market clinical follow-up | Recommended | **Mandatory PMCF plan for all classes** (unless justified) |
| Periodic Safety Update Report (PSUR) | Not required | **Mandatory for IIa (2yr), IIb/III (annual)** |
| Annex XVI devices (non-medical) | Not covered | **Covered** (aesthetic devices: colored contacts, fillers, lasers, etc.) |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 MDR Lifecycle Overview

```mermaid
graph TB
    subgraph "Pre-Market"
        DEV[Device Development<br/>• Design controls<br/>• Risk management<br/>• V&V testing]
        CLASS[Classification<br/>Annex VIII Rules<br/>━━━━━━━━━━━<br/>22 classification rules<br/>Rule 11 for software]
        TD[Technical Documentation<br/>Annex II<br/>━━━━━━━━━━━<br/>Device description<br/>GSPR compliance<br/>Risk management<br/>Pre-clinical evidence]
        CER[Clinical Evaluation<br/>Annex XIV<br/>━━━━━━━━━━━<br/>Literature review<br/>Equivalence analysis<br/>Clinical investigation<br/>Clinical Evaluation Report]
        QMS[Quality Management System<br/>ISO 13485 + MDR<br/>━━━━━━━━━━━<br/>Design & development<br/>Production controls<br/>CAPA<br/>Documented per MDR]
        NB[Notified Body Assessment<br/>━━━━━━━━━━━<br/>QMS audit (Annex IX Ch I)<br/>TD review (Annex IX Ch II/III)<br/>Clinical evidence review]
    end
    
    subgraph "Market Placement"
        CE[CE Marking<br/>+ NB Number<br/>+ EU DoC]
        UDI_M[UDI Assignment<br/>& EUDAMED Registration]
        DOC_M[EU Declaration of<br/>Conformity (Annex IV)]
    end
    
    subgraph "Post-Market"
        PMS[Post-Market Surveillance<br/>Annex III<br/>━━━━━━━━━━━<br/>PMS plan<br/>PSUR (IIa: 2yr; IIb/III: 1yr)<br/>Proactive data collection]
        PMCF[Post-Market Clinical Follow-up<br/>━━━━━━━━━━━<br/>PMCF plan<br/>PMCF evaluation report<br/>Update CER]
        VIG[Vigilance<br/>Articles 87-92<br/>━━━━━━━━━━━<br/>Serious incident reporting<br/>FSCA & FSN<br/>Trend reporting]
        SURV[NB Surveillance<br/>━━━━━━━━━━━<br/>Annual audits<br/>Unannounced audits<br/>Certificate renewal (5yr)]
    end
    
    DEV --> CLASS --> TD
    DEV --> CER --> TD
    DEV --> QMS
    TD --> NB
    QMS --> NB
    NB --> CE --> UDI_M
    CE --> DOC_M
    CE --> PMS & PMCF & VIG
    PMS --> SURV
    PMCF --> CER
    VIG --> PMS
    SURV --> NB
```

### 8.2 Classification Decision Tree (Software — Rule 11)

```mermaid
graph TB
    START[Software Medical Device<br/>(intended for medical purpose)]
    
    Q1{Does the software<br/>drive or influence<br/>a hardware device?}
    
    START --> Q1
    
    Q1 -->|"Yes"| SAME[Same class as<br/>the hardware device<br/>it drives/influences]
    
    Q1 -->|"No (standalone SaMD)"| Q2{Does the software provide<br/>information for decisions on<br/>diagnosis or therapy?}
    
    Q2 -->|"No (admin, wellness,<br/>general health info)"| CLASS_I[Class I<br/>━━━━━━━<br/>Self-certification<br/>No NB required]
    
    Q2 -->|"Yes"| Q3{Could the decision based on<br/>the software information<br/>cause DEATH or IRREVERSIBLE<br/>deterioration of health?}
    
    Q3 -->|"Yes"| CLASS_III[Class III<br/>━━━━━━━<br/>NB full TD review<br/>Clinical investigation<br/>Expert panel possible]
    
    Q3 -->|"No"| Q4{Could the decision cause<br/>SERIOUS deterioration of health<br/>or SURGICAL intervention?}
    
    Q4 -->|"Yes"| CLASS_IIB[Class IIb<br/>━━━━━━━<br/>NB QMS + TD review]
    
    Q4 -->|"No"| CLASS_IIA[Class IIa<br/>━━━━━━━<br/>NB QMS audit<br/>TD sampling]
```

### 8.3 Post-Market Surveillance Architecture

```mermaid
graph TB
    subgraph "Data Sources"
        COMP[Complaints<br/>Customer feedback]
        VIG_D[Vigilance data<br/>Incident reports]
        LIT[Literature monitoring<br/>Scientific publications]
        REG[Registry data<br/>Patient outcomes]
        PMCF_D[PMCF study data<br/>Clinical follow-up]
        FSCA_D[FSCA reports from<br/>similar devices]
        TREND[Trend analysis<br/>Statistical monitoring]
    end
    
    subgraph "PMS System"
        PMS_PLAN[PMS Plan<br/>(per device/device group)<br/>━━━━━━━━━━━<br/>• Data collection methods<br/>• Analysis methodology<br/>• Triggers for action<br/>• Responsible persons]
        ANALYSIS[Data Analysis<br/>━━━━━━━━━━━<br/>• Trend identification<br/>• Signal detection<br/>• Benefit-risk update<br/>• Residual risk evaluation]
    end
    
    subgraph "Outputs"
        PSUR_O[PSUR<br/>━━━━━━━━━━━<br/>Class IIa: every 2 years<br/>Class IIb/III: annually<br/>Conclusions on B/R<br/>Submitted to NB]
        PMS_REP[PMS Report<br/>━━━━━━━━━━━<br/>Class I: updated as needed<br/>Summarizes PMS results]
        CER_UP[CER Update<br/>━━━━━━━━━━━<br/>Updated with new<br/>clinical evidence<br/>At least annually for<br/>Class III/implantable]
        FSCA_O[FSCA/FSN<br/>━━━━━━━━━━━<br/>If safety issue identified<br/>Competent authority notification]
        SSCP[SSCP<br/>━━━━━━━━━━━<br/>Class III + implantable<br/>Published in EUDAMED<br/>Public summary]
    end
    
    COMP & VIG_D & LIT & REG & PMCF_D & FSCA_D & TREND --> PMS_PLAN
    PMS_PLAN --> ANALYSIS
    ANALYSIS --> PSUR_O & PMS_REP & CER_UP & FSCA_O & SSCP
```

---

## Chapter 9 — Case Studies

### 9.1 Software as Medical Device — Classification Upgrade Under MDR

| Aspect | Detail |
|--------|--------|
| Device | Clinical decision support software for oncology (analyzes lab results + imaging reports; provides treatment protocol recommendation) |
| MDD classification | Class IIa (Rule 12: "active device intended to administer or exchange energy" — loosely interpreted for software) |
| MDR classification | **Class IIb** (Rule 11: software providing information used for therapeutic decisions that could lead to serious deterioration of health if incorrect — wrong cancer treatment protocol) |
| Impact of reclassification | (1) **NB assessment level**: under MDD IIa, NB did sample TD review. Under MDR IIb, NB must review TD for EACH device type (not just sample). (2) **Clinical evidence**: must demonstrate clinical benefit of the software (not just that the algorithm works — must show improved clinical outcomes or non-inferiority). (3) **PMCF**: mandatory PMCF plan to continuously monitor algorithm performance in real-world clinical use. (4) **PSUR**: annual PSUR required (vs. biennial for IIa). (5) **Cybersecurity**: GSPR 17 requires specific cybersecurity measures (software used for treatment decisions is high-value target). (6) **Cost**: NB fees increased significantly; clinical evidence generation (retrospective study + prospective PMCF) added 18 months and €300,000+ to timeline. |
| Actions taken | (1) Updated technical documentation to MDR Annex II format. (2) Conducted retrospective clinical performance study (5,000 cases; compared algorithm recommendations to tumor board decisions). (3) Established PMCF study (ongoing real-world monitoring of algorithm vs. actual treatment decisions and patient outcomes). (4) Implemented cybersecurity per IEC 81001-5-1. (5) Updated labeling per MDR Annex I Chapter III. (6) Applied to new MDR-designated Notified Body (previous NB not designated under MDR). |

### 9.2 Case Study: Transition Failure — Legacy Device Without NB

| Aspect | Detail |
|--------|--------|
| Device | Class IIb orthopedic implant (hip stem); on market since 2005 under MDD; MDD certificate from Notified Body "X" |
| Problem | NB "X" lost its MDR designation in 2022 (could not meet new designation requirements). Manufacturer's MDD certificate expired Dec 2023. No MDR certificate in place. |
| Consequences | (1) **Cannot place new devices on EU market** (no valid certificate = no CE marking). (2) Must find new NB with MDR designation — but demand for NB services exceeds capacity (18-24 month wait for full certification). (3) **Transition provision** (Regulation 2023/607): manufacturer can continue selling under expired MDD certificate IF: (a) device is unchanged; (b) application submitted to NB before certificate expiry; (c) QMS maintained; (d) active PMS/vigilance. (4) New deadline: Class IIb non-implantable → Dec 2028; Class III/implantable → Dec 2027. |
| Lessons | (1) **Start MDR transition early** — NB capacity is limited. (2) **Monitor your NB's status** — if they lose designation, you lose certification. (3) **Clinical evidence gap** is the #1 reason for delays (insufficient CER; no PMCF data; equivalence claims rejected). (4) **Budget**: full MDR transition for an implant costs €200,000-500,000+ and takes 18-36 months. (5) **Regulatory risk**: some manufacturers have withdrawn devices from EU market because cost/complexity of MDR compliance exceeds revenue for low-volume specialty devices. |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| EUDAMED full functionality | 2025-2026 | All modules live; full transparency; mandatory registration; public vigilance data |
| Transition period expiry | 2027-2028 | All devices must have MDR certificate; legacy MDD certificates fully expired |
| UK UKCA framework | 2025-2027 (delayed multiple times) | Separate UK regulatory framework; dual CE+UKCA submissions; mutual recognition unclear |
| MDR review (Article 120 evaluation) | 2027 | European Commission evaluation of MDR effectiveness; potential amendments |
| AI/ML medical devices | Now-2025+ | MDCG guidance on AI; continuous learning devices; clinical evidence challenges |
| Digital health / SaMD growth | Accelerating | More software classified under Rule 11; challenges with rapid iteration vs. certification timelines |
| Cybersecurity regulation (EU CRA) | 2024-2027 | Cyber Resilience Act overlaps with MDR GSPR 17; coordination needed |
| NB capacity | Ongoing challenge | Limited number of MDR-designated NBs; long wait times; concentration risk |
| Sustainability / Green Deal | 2025+ | Environmental requirements for medical devices; right to repair; recyclability |
| IVDR implementation challenges | 2022-2028 | Many IVDs reclassified upward; NB capacity insufficient for IVDs; significant extensions granted |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What are the main differences between the old MDD and the new MDR?  
**A:** Key differences: (1) **Legal instrument**: MDD was a Directive (transposed differently by each country); MDR is a Regulation (directly applicable, identical across all EU Member States). (2) **Clinical evidence**: MDR significantly strengthens clinical evidence requirements — CER is mandatory for all classes; PMCF is mandatory; equivalence claims require access to equivalent device data; clinical investigation generally expected for Class III implantable. (3) **Post-market**: MDR introduces mandatory PSUR (annual for IIb/III), PMS plan, and trend reporting. MDD had minimal post-market requirements. (4) **Software**: Rule 11 classifies most standalone medical software as Class IIa minimum (MDD Rule 12 often allowed Class I). (5) **Transparency**: EUDAMED database makes device information, certificates, and vigilance publicly accessible. (6) **Notified Bodies**: much stricter designation and oversight; unannounced audits mandatory; NB capacity reduced (fewer NBs designated under MDR). (7) **UDI**: mandatory for all devices (phased implementation by class). (8) **Economic operators**: importers and distributors have defined regulatory obligations (not just manufacturers). (9) **Person Responsible for Regulatory Compliance (PRRC)**: manufacturers must have a qualified person responsible for regulatory compliance (Article 15).

**Q2:** What is a GSPR and how do you demonstrate compliance?  
**A:** GSPRs (General Safety and Performance Requirements, Annex I of MDR) are the fundamental requirements that every medical device must meet. There are 23 chapters covering safety, performance, and information requirements. For EACH GSPR, the manufacturer must: (1) Determine if it's **applicable** (with justification if not). (2) Identify the **method of demonstrating conformity** — usually reference to a harmonized standard (e.g., GSPR Chapter 11 for electrical safety → IEC 60601-1; GSPR Chapter 15 for software → IEC 62304). (3) Provide **evidence** — test reports, risk management file, biocompatibility report, clinical evaluation report, etc. This is documented in the GSPR checklist/table in the technical documentation (Annex II, Section 4). Common GSPRs: Chapter 10 (biocompatibility → ISO 10993 test reports); Chapter 14 (radiation → dose measurements); Chapter 15 (software → IEC 62304 compliance evidence); Chapter 17 (cybersecurity → IEC 81001-5-1 compliance); Chapter 23 (labeling → per ISO 15223-1 symbols). The GSPR checklist is one of the most important documents in the technical file — Notified Bodies review it to assess overall compliance structure.

### Tier 2: Mid-Level

**Q3:** How do you write a Clinical Evaluation Report (CER) that meets MDR requirements?  
**A:** The CER is the document demonstrating sufficient clinical evidence for your device's safety and performance. MDR requirements (Annex XIV + MDCG 2020-13): (1) **Scope**: define device, variants, intended purpose, target population, indications, contraindications, claims. (2) **Clinical evaluation plan**: methodology before you start — search strategy, databases (PubMed, Embase, Cochrane), search terms, inclusion/exclusion criteria, appraisal criteria. (3) **Literature review**: systematic; follow MEDLINE search protocol; document searches reproducibly; appraise each paper for relevance and quality. (4) **Equivalence assessment** (if claiming): demonstrate technical + biological + clinical equivalence per MDR Annex XIV. Under MDR: must have **access to equivalent device data** (contract/same manufacturer) — effectively prevents "predicate hopping" to competitor devices. (5) **Clinical data from own device**: clinical investigation data; registry data; PMCF data from previous periods. (6) **Safety analysis**: adverse events from literature + own data + vigilance databases (MAUDE, BfArM, etc.); frequency; severity; compare to state-of-art. (7) **Performance analysis**: clinical performance against defined acceptance criteria; comparison to state-of-art. (8) **Benefit-risk conclusion**: overall benefit-risk acceptable? Residual risks justified by benefits? Align with risk management file. (9) **Gaps identification**: what clinical evidence is missing? → Feeds into PMCF plan. (10) **PMCF plan**: how you'll continue to gather clinical evidence post-market. (11) **Update frequency**: at least annually for Class III/implantable; at each significant new data. Common NB deficiencies: insufficient literature search documentation; equivalence not adequately justified; state-of-art comparators not identified; PMCF plan generic rather than device-specific.

### Tier 3: Senior

**Q4:** Your company has a portfolio of 200 medical devices under MDD. How do you develop an MDR transition strategy?  
**A:** Portfolio-level MDR transition strategy: (1) **Classification review**: re-classify all 200 devices under MDR Annex VIII rules. Identify devices that UP-classify (especially software under Rule 11; reusable surgical instruments now Class I requiring NB for reprocessing). Impact: some devices may need NB involvement for first time; cost-benefit analysis for low-volume products. (2) **Portfolio rationalization**: identify devices to DISCONTINUE (revenue vs. MDR compliance cost); identify devices to CONSOLIDATE (combine variants to reduce TD count); identify PRIORITY devices (highest revenue, strategic importance). Typical result: 200 → 150 devices carried forward; 50 discontinued or consolidated. (3) **Notified Body strategy**: assess current NB's MDR designation status (are they designated? For which codes?); if NB not designated, find new NB NOW (18-24 month wait); consider splitting portfolio across 2 NBs (risk mitigation); negotiate multi-device assessment packages. (4) **Clinical evidence gap analysis** (the #1 bottleneck): for each device, assess current CER adequacy against MDR requirements; identify devices needing clinical investigation (Class III implantable without adequate equivalence); plan PMCF studies (can start under MDD to have data ready for MDR submission). (5) **Technical documentation conversion**: develop MDR-compliant templates; prioritize highest-class devices; allocate RA/QA resources (typical: 1 person-month per Class IIb TD; 3-6 person-months per Class III TD). (6) **Transition timeline with regulatory deadlines**: Class III/implantable → must have MDR certificate by Dec 2027; Class IIb → Dec 2028; Plan backwards from deadlines (subtract NB assessment time, TD preparation time, clinical evidence generation time). (7) **Resource plan**: typical mid-size company needs 2-5 additional RA professionals for 3-5 years; budget €2-5M total for portfolio transition. (8) **Risk mitigation**: use transition provisions (Regulation 2023/607) to maintain market access during transition; ensure MDD QMS maintained and PMS active (conditions for transitional provisions).

---

## Chapter 12 — Cheat Sheet & Quick Reference

### MDR Classification Quick Reference

```
Class I:   Self-declare; no NB (unless sterile/measuring/reusable surgical)
Class IIa: NB QMS audit + TD sample review; PSUR every 2 years
Class IIb: NB QMS audit + TD review per device; PSUR annually  
Class III:  NB full QMS + full TD + clinical evidence review; PSUR annually; expert panel possible

SOFTWARE (Rule 11):
  Drives/influences device → same class as device
  Clinical decision (could cause death) → Class III
  Clinical decision (serious deterioration/surgery) → Class IIb
  Clinical decision (other) → Class IIa
  No clinical decision → Class I
```

### Key Timelines

```
Serious incident report:        15 days (2 days for public health threat; 10 days for death)
PSUR Class IIa:                 Every 2 years (submitted to NB)
PSUR Class IIb/III:             Annually (submitted to NB)
CER update Class III/implant:   At least annually
CE certificate validity:        5 years (then renewal)
UDI implementation:             By class (phased; consult regulation for dates)
Transition deadline (Class III): December 31, 2027
Transition deadline (IIb):      December 31, 2028
```

### Technical Documentation Checklist

```
□ Device description + intended purpose + classification justification
□ Labels + IFU (all languages for target markets)
□ Design & manufacturing description (process flow, critical processes)
□ GSPR checklist (all 23 chapters; applicable/not; method; evidence)
□ Risk management file (ISO 14971; FMEA/FTA; benefit-risk)
□ Biocompatibility (ISO 10993 if patient contact)
□ Electrical safety (IEC 60601 if applicable)
□ EMC (IEC 60601-1-2 if applicable)
□ Software V&V (IEC 62304 if applicable)
□ Usability (IEC 62366 if applicable)
□ Cybersecurity (if connected/software)
□ Performance testing (device-specific)
□ Shelf life / stability / transport studies
□ Clinical Evaluation Report (CER) + literature search + equivalence
□ PMCF plan
□ PMS plan
□ EU Declaration of Conformity
□ UDI assignment
```

### Vigilance Decision Quick Reference

```
Is it a SERIOUS INCIDENT? (Article 2(65)):
  Death ─────────────────────────── → Report within 15 days (10 days death)
  Serious deterioration of health ─ → Report within 15 days
  Serious public health threat ──── → Report within 2 DAYS

Is a Field Safety Corrective Action (FSCA) needed?
  Action to reduce risk of death/serious deterioration → YES → notify CA + issue FSN

Trend reporting (Article 88):
  Statistically significant increase in frequency/severity → Report WITHOUT UNDUE DELAY
```

### GSPR Chapter Map (Annex I)

```
Ch I  (1-9):   General (risk mgmt, design, performance, benefit-risk)
Ch II (10):    Materials & biocompatibility
Ch II (11):    Infection & microbial contamination
Ch II (12):    Devices with energy source (electrical/mechanical safety)
Ch II (13):    Diagnostic/measuring function (accuracy)
Ch II (14):    Radiation protection
Ch II (15):    Software (IEC 62304; V&V; lifecycle)
Ch II (16):    Active diagnostic devices
Ch II (17):    CYBERSECURITY (IT security; unauthorized access protection)
Ch III (18-22): Specific groups (implantable, substances, etc.)
Ch III (23):   Labeling & IFU requirements
```

---

*End of Document — 05_EU_MDR_IVDR.md*
