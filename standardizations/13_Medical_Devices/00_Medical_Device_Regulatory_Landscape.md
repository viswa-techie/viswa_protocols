# Medical Device Regulatory Landscape — Global Overview

**Topic:** Medical device regulatory frameworks, classification systems, and compliance pathways across global markets  
**Standard:** FDA 21 CFR, EU MDR 2017/745, EU IVDR 2017/746, TGA, PMDA, Health Canada, MDSAP  
**SDO:** FDA (US), European Commission (EU), TGA (Australia), PMDA (Japan), Health Canada, IMDRF  
**Audience:** Regulatory affairs professionals, medical device engineers, quality managers, product managers, startup founders in healthtech  
**Prerequisites:** Basic understanding of product development lifecycles, quality management concepts

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline of Major Events

| Year | Event | Significance |
|------|-------|-------------|
| 1906 | US Pure Food and Drug Act | First federal regulation of drugs/devices |
| 1938 | FD&C Act (after sulfonamide deaths) | FDA authority over drugs; devices loosely covered |
| 1962 | Kefauver-Harris Amendment | Drug efficacy requirement (thalidomide tragedy) |
| 1976 | **US Medical Device Amendments** | Device classification (Class I/II/III); 510(k); PMA |
| 1985 | **Therac-25 radiation overdoses** | 6 patients killed; transformed software safety regulation |
| 1990 | Safe Medical Devices Act | Post-market reporting; device tracking |
| 1993 | EU Medical Device Directive (93/42/EEC) | CE marking for medical devices (MDD) |
| 1996 | HIPAA | Patient data protection (US) |
| 1997 | FDA Modernization Act | 510(k) improvements; de novo pathway |
| 2002 | EU Active Implantable MDD revised | Updated implantable device regulation |
| 2006 | **IEC 62304** published | Medical device software lifecycle standard |
| 2012 | FDA Safety and Innovation Act | Breakthrough device designation |
| 2016 | ISO 13485:2016 | Updated QMS for medical devices |
| 2017 | **EU MDR 2017/745** published | Major overhaul replacing MDD; 3-year transition |
| 2017 | **EU IVDR 2017/746** published | New IVD regulation replacing IVDD |
| 2020 | EU MDR transition extended (COVID) | Enforcement delayed to May 2021 (MDR) |
| 2021 | EU MDR enforcement begins | Full application: May 26, 2021 |
| 2022 | EU MDR transition extensions approved | Existing devices: extended deadlines (2027/2028) |
| 2022 | FDA Cybersecurity guidance | Mandatory SBOM; premarket cyber requirements |
| 2023 | FDA 21 CFR 820 alignment with ISO 13485 | Harmonizing US QSR with international standard |
| 2024 | EU AI Act — high-risk medical devices | AI in Class IIa+ devices under AI Act obligations |
| 2024 | IVDR full application (extended transition ongoing) | IVD classification changes; notified body capacity issues |

### 1.2 Key Regulatory Bodies

| Authority | Jurisdiction | Role |
|-----------|:----------:|------|
| **FDA** (CDRH) | United States | 510(k), PMA, De Novo clearance; post-market surveillance |
| **European Commission** (via Notified Bodies) | EU/EEA | MDR/IVDR CE marking; performance evaluation |
| **TGA** | Australia | ARTG registration; classification aligned with GHTF |
| **PMDA** | Japan | Shonin (approval); classification; J-PAL |
| **Health Canada** | Canada | Class I-IV licensing; MDSAP recognition |
| **NMPA** (formerly CFDA) | China | Registration certificate; clinical trials required |
| **ANVISA** | Brazil | Good Manufacturing Practice certificate; MDSAP |
| **KFDA** (MFDS) | South Korea | Medical device approval/notification |
| **CDSCO** | India | Import/manufacturing license; classification |
| **HSA** | Singapore | Product registration; ASEAN harmonization |

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 Device Classification Comparison

```mermaid
graph TB
    subgraph "US FDA Classification"
        FDA_I[Class I<br/>━━━━━━━━━━━━━<br/>Low Risk<br/>General Controls<br/>Most 510(k) exempt<br/>Example: tongue depressor,<br/>bandage, stethoscope]
        FDA_II[Class II<br/>━━━━━━━━━━━━━<br/>Moderate Risk<br/>General + Special Controls<br/>510(k) clearance<br/>Example: infusion pump,<br/>X-ray, powered wheelchair]
        FDA_III[Class III<br/>━━━━━━━━━━━━━<br/>High Risk<br/>General Controls + PMA<br/>Premarket Approval<br/>Example: pacemaker,<br/>heart valve, total joint]
    end
    
    subgraph "EU MDR Classification"
        EU_I[Class I / Is / Im / Ir<br/>━━━━━━━━━━━━━<br/>Low Risk<br/>Self-declaration (I)<br/>NB needed (Is/Im/Ir)<br/>Example: wheelchair,<br/>examination gloves]
        EU_IIA[Class IIa<br/>━━━━━━━━━━━━━<br/>Medium Risk<br/>Notified Body audit<br/>Example: hearing aid,<br/>ultrasound scanner,<br/>surgical clamp]
        EU_IIB[Class IIb<br/>━━━━━━━━━━━━━<br/>Medium-High Risk<br/>Notified Body audit<br/>Example: ventilator,<br/>blood bags,<br/>implantable screws]
        EU_III_M[Class III<br/>━━━━━━━━━━━━━<br/>High Risk<br/>Full NB review<br/>Expert panel<br/>Example: pacemaker,<br/>drug-eluting stent,<br/>breast implant]
    end
```

### 2.2 Regulatory Pathways (US FDA)

| Pathway | When to Use | Timeline | Data Required | Cost |
|---------|------------|:--------:|---------------|:----:|
| **510(k)** | Substantially equivalent to predicate device | 3-12 months | Comparison to predicate; bench testing; some clinical | ~$13K (FDA fee) |
| **De Novo** | Novel device, low-moderate risk, no predicate | 6-12 months | Risk-based classification; performance data | ~$6K |
| **PMA** | High-risk Class III; no predicate or special controls insufficient | 12-36 months | Clinical trials; manufacturing data; full safety/efficacy | ~$420K |
| **Breakthrough** | Life-threatening condition; breakthrough technology | Accelerated (3-6 months faster) | Interactive review; priority access | Same as base pathway |
| **EUA** | Emergency (pandemic/public health emergency) | Days-weeks | Limited data; risk-benefit; duration of emergency | $0 |
| **HDE** | Humanitarian device; <8,000 patients/year | 6-12 months | Probable benefit; safety data (no efficacy requirement) | $0 |

### 2.3 EU MDR Conformity Assessment Routes

| Class | Route | Notified Body | Documentation |
|-------|-------|:----------:|---------------|
| Class I (non-sterile, non-measuring) | Self-declaration | ❌ Not required | Technical documentation; EU Declaration of Conformity |
| Class I sterile (Is) | NB for sterilization only | ✅ | Technical documentation + sterilization validation |
| Class I measuring (Im) | NB for metrological aspects | ✅ | Technical documentation + measurement verification |
| Class I reusable surgical (Ir) | NB for reprocessing | ✅ | Technical documentation + reprocessing validation |
| Class IIa | Annex IX (QMS + TD review) OR Annex XI (product verification) | ✅ | QMS (ISO 13485); clinical evaluation; technical documentation |
| Class IIb | Annex IX (QMS) + Annex X (type examination) | ✅ | Full technical documentation; clinical investigation or clinical evaluation |
| Class IIb implantable | Annex IX + Expert Panel consultation | ✅ | Clinical evaluation with clinical investigation data (usually) |
| Class III | Annex IX (full QMS audit) + Annex X (type examination) | ✅ + Expert Panel | Clinical investigation; full QMS; PMS/PMCF plan; expert panel review |

### 2.4 Key Standards Ecosystem for Medical Devices

| Standard | Purpose | Mandatory? |
|----------|---------|:----------:|
| **ISO 13485:2016** | Quality Management System for medical devices | Required (EU MDR, MDSAP, most markets) |
| **IEC 62304:2006+A1:2015** | Software lifecycle for medical device software | Required for software devices |
| **IEC 60601-1:2005+Amd2** | Electrical safety for medical electrical equipment | Required for electrical devices |
| **ISO 14971:2019** | Risk management for medical devices | **Universal requirement** |
| **IEC 62366-1:2015** | Usability engineering (application of) | Required (EU MDR, FDA) |
| **ISO 10993 series** | Biocompatibility evaluation | Required for body-contact devices |
| **IEC 60601-1-2:2014+Amd1** | EMC (electromagnetic compatibility) | Required for electrical devices |
| **ISO 11135 / ISO 11137** | Sterilization (EtO / Radiation) | Required for sterile devices |
| **IEC 62443** | Industrial cybersecurity (for connected devices) | Increasingly referenced |
| **ISO 14708 series** | Active implantable medical devices | Required for implantables |

---

## Chapter 3 — Technical Deep Dive

### 3.1 EU MDR Classification Rules (Annex VIII)

| Rule | Scope | Key Factor | Results in Class |
|------|-------|-----------|:----------------:|
| 1 | Non-invasive (don't touch patient) | Channeling/storing | I |
| 2 | Non-invasive (channeling blood/body fluids) | Connected to active device | IIa |
| 3 | Non-invasive (modify biological/chemical composition) | For infusion/exchange | IIb |
| 4 | Non-invasive (contact with injured skin) | Barrier/compression; wounds | I-IIb |
| 5 | Invasive (body orifice — transient) | < 60 minutes | I |
| 6 | Invasive (body orifice — short-term) | 60 min to 30 days | IIa |
| 7 | Invasive (body orifice — long-term) | > 30 days | IIb |
| 8 | Surgically invasive (transient) | < 60 minutes | IIa |
| 9 | Surgically invasive (short-term) | 60 min to 30 days | IIb |
| 10 | Surgically invasive (long-term/implant) | > 30 days | IIb-III |
| 11 | Active therapeutic devices | Administer/exchange energy | IIa-IIb |
| 12 | Active diagnostic devices | Monitor vital parameters | IIa-IIb |
| 13 | Other active devices | Not covered by 11/12 | I |
| 14 | Devices incorporating substance (medicinal) | Ancillary medicinal substance | III |
| 15 | Contraceptive/STI prevention devices | Implantable or long-term invasive | III |
| 16 | Disinfection/sterilization of devices | Cleaning/sterilization products | IIa-IIb |
| 17 | **Software** | Drives/influences treatment → IIa minimum; diagnosis serious → IIb; life-threatening → III | IIa-III |
| 18 | Nanomaterial-containing devices | Internal exposure: high risk | IIb-III |
| 19 | Devices incorporating tissues of animal origin | Biological risk | III |
| 20 | Devices utilizing human blood/plasma | Blood derivatives | III |
| 21 | Devices that are introduced into body through orifice (surgically) and emit ionizing radiation | Radiotherapy | IIb-III |
| 22 | Active therapeutic device with integrated diagnostic function (closed-loop) | AI-driven treatment | III |

### 3.2 Software as a Medical Device (SaMD) Classification

**IMDRF SaMD Classification Framework:**

| Significance of Information | State of Healthcare Situation |
|:--|:---:|
|  | **Critical** | **Serious** | **Non-serious** |
| **Treat or diagnose** | IV (highest) | III | II |
| **Drive clinical management** | III | II | I (lowest) |
| **Inform clinical management** | II | I | I |

**FDA SaMD Examples:**

| Device | Classification | Pathway |
|--------|:-------------:|---------|
| AI detecting diabetic retinopathy | Class II (De Novo) | De Novo (IDx-DR: first autonomous AI diagnostic) |
| Clinical decision support (non-serious) | Exempt (under 21st Century Cures Act) | No submission required (if meets criteria) |
| AI for mammography (cancer detection) | Class II | 510(k) |
| Closed-loop insulin delivery (artificial pancreas) | Class III | PMA (De Novo for some) |
| Mental health app (CBT-based) | Class II | 510(k) or De Novo |
| Fitness tracker (general wellness) | Not a device | No regulation (general wellness exemption) |

### 3.3 MDSAP (Medical Device Single Audit Program)

| Aspect | Detail |
|--------|--------|
| Purpose | Single audit satisfies multiple regulatory authorities |
| Participating Authorities | FDA (US), Health Canada, TGA (Australia), ANVISA (Brazil), PMDA (Japan) |
| Standard audited | ISO 13485:2016 + country-specific requirements (overlaid) |
| Benefit | One audit → recognition by 5 markets (reduces audit burden) |
| Audit scope | QMS; design controls; production; CAPA; post-market; purchasing |
| Mandatory? | **Canada: Mandatory** (since 2019). Others: voluntary but recognized |
| Auditing organizations | ~16 recognized MDSAP auditing organizations (BSI, TÜV, SGS, etc.) |

### 3.4 Essential Requirements Overview

| Requirement Area | EU MDR Annex I | FDA 21 CFR 820 | ISO 13485 |
|-----------------|:-:|:-:|:-:|
| Risk management | GSPR 1-9 (General Safety & Performance Requirements) | §820.30 (Design Controls) | Clause 7.1 (Planning) |
| Design and manufacturing | GSPR 10-22 | §820.30, §820.70 | Clause 7.3, 7.5 |
| Clinical evidence | Art 61; Annex XIV | Clinical data per pathway | N/A (referenced) |
| Biocompatibility | GSPR 10 | §820.30(c) | Referenced (ISO 10993) |
| Software lifecycle | GSPR 17 | §820.30 + IEC 62304 | Referenced (IEC 62304) |
| Cybersecurity | GSPR 17.2, 17.4 | FDA Cybersecurity Guidance (2023) | Referenced |
| Labeling | GSPR 23; Annex I Ch.III | 21 CFR 801 | Clause 7.3 |
| Post-market | Art 83-86; PMCF | §820.198; MDR reporting | Clause 8.2.1, 8.2.2 |

---

## Chapter 4 — Implementation Guide

### 4.1 Medical Device Development Lifecycle

```mermaid
graph TB
    subgraph "Phase 1: Concept & Planning"
        P1A[User Needs & Intended Use<br/>• Clinical need identification<br/>• Intended patient population<br/>• Intended use environment<br/>• Use scenarios]
        P1B[Regulatory Strategy<br/>• Classification determination<br/>• Market selection (US/EU/global)<br/>• Pathway selection (510k/PMA/CE)<br/>• Standards identification]
        P1C[Risk Management Init<br/>• ISO 14971 risk management plan<br/>• Initial hazard identification<br/>• Risk acceptability criteria<br/>• Benefit-risk analysis]
    end
    
    subgraph "Phase 2: Design & Development"
        P2A[Design Input<br/>• Design requirements<br/>• Performance specifications<br/>• Safety requirements<br/>• Regulatory requirements]
        P2B[Design & Implementation<br/>• Hardware/software development<br/>• IEC 62304 (software)<br/>• IEC 60601 (electrical)<br/>• Usability (IEC 62366)]
        P2C[Verification & Validation<br/>• Design verification (meets specs?)<br/>• Design validation (meets needs?)<br/>• Clinical evaluation/investigation<br/>• Biocompatibility (ISO 10993)]
    end
    
    subgraph "Phase 3: Regulatory Submission"
        P3A[Technical Documentation<br/>• Device description<br/>• Design & manufacturing info<br/>• Risk management report<br/>• Clinical evidence<br/>• Labeling]
        P3B[Submission<br/>• 510(k)/PMA/De Novo (FDA)<br/>• Notified Body audit (EU MDR)<br/>• TGA/PMDA submissions<br/>• MDSAP audit]
        P3C[Clearance/Approval<br/>• FDA clearance letter<br/>• CE certificate<br/>• Market authorization<br/>• UDI registration]
    end
    
    subgraph "Phase 4: Post-Market"
        P4A[Manufacturing & Quality<br/>• ISO 13485 QMS<br/>• Production controls<br/>• Process validation<br/>• Supplier management]
        P4B[Post-Market Surveillance<br/>• PMS plan (MDR Art 84)<br/>• PMCF (clinical follow-up)<br/>• Vigilance reporting<br/>• Customer complaints<br/>• Field safety actions]
        P4C[Lifecycle Management<br/>• Design changes<br/>• Regulatory updates<br/>• Re-certification<br/>• End of life planning]
    end
    
    P1A --> P2A --> P3A --> P4A
    P1B --> P2B --> P3B --> P4B
    P1C --> P2C --> P3C --> P4C
```

### 4.2 Regulatory Submission Checklist

| Item | FDA 510(k) | EU MDR CE (Class IIa+) | Both |
|------|:----------:|:---------------------:|:----:|
| Device description | ✅ | ✅ | ✅ |
| Intended use / indications for use | ✅ | ✅ (intended purpose) | ✅ |
| Classification & applicable rules | ✅ (product code) | ✅ (Annex VIII rule) | ✅ |
| Substantial equivalence / predicate | ✅ | ❌ (not applicable) | — |
| General Safety & Performance Requirements | ❌ | ✅ (GSPR checklist, Annex I) | — |
| Risk management (ISO 14971) | ✅ | ✅ | ✅ |
| Software documentation (IEC 62304) | ✅ (if SW) | ✅ (if SW) | ✅ |
| Electrical safety (IEC 60601) | ✅ (if applicable) | ✅ (if applicable) | ✅ |
| Biocompatibility (ISO 10993) | ✅ (if body contact) | ✅ (if body contact) | ✅ |
| EMC testing (IEC 60601-1-2) | ✅ | ✅ | ✅ |
| Usability (IEC 62366) | ✅ | ✅ | ✅ |
| Clinical evaluation | ✅ (clinical data) | ✅ (MDR Art 61; Annex XIV) | ✅ |
| Cybersecurity (SBOM) | ✅ (FDA 2023 guidance) | ✅ (GSPR 17.2/17.4) | ✅ |
| Labeling (IFU, symbols) | ✅ (21 CFR 801) | ✅ (Annex I Ch.III; UDI) | ✅ |
| Sterilization validation | ✅ (if sterile) | ✅ (if sterile) | ✅ |
| Post-market surveillance plan | ❌ (separate) | ✅ (mandatory in TD) | — |
| Quality Management System | ✅ (820 inspection) | ✅ (ISO 13485 audit) | ✅ |
| UDI (Unique Device Identification) | ✅ | ✅ | ✅ |

---

## Chapter 5 — Certification & Audit

### 5.1 Notified Bodies (EU MDR)

| NB | Number | Designation | Capacity Notes |
|----|:------:|-------------|---------------|
| BSI (UK/NL) | 2797 | Full MDR scope | Largest NB; high demand |
| TÜV SÜD | 0123 | Full MDR scope | Major NB for implantables |
| TÜV Rheinland | 0197 | Full MDR scope | Strong in IVD |
| SGS | 0120 | Full MDR scope | Global presence |
| DEKRA | 0124 | Full MDR scope | Growing medical device scope |
| IMQ | 0051 | MDR (limited scope) | Italian NB |

**NB Capacity Crisis (2021-2024):** Only ~35 NBs designated under MDR (vs. ~80 under MDD). Significant bottleneck; audit wait times 12-18 months. EU Commission extended transition periods (2027/2028) for legacy devices.

### 5.2 FDA Inspection Types

| Inspection Type | Trigger | Focus | Duration |
|----------------|---------|-------|----------|
| Pre-Approval Inspection (PAI) | PMA submission | Manufacturing; QMS; data integrity | 1-2 weeks |
| Routine Surveillance | Periodic (every 2-4 years) | 21 CFR 820 compliance; CAPA; complaints | 1-2 weeks |
| For Cause | Complaint; MDR; signal | Specific issue investigation | Variable |
| Compliance Follow-up | Previous violation (483/Warning Letter) | Corrective action verification | 3-5 days |

**FDA 483 Observations (Top Deficiencies):**

| Rank | 820 Section | Issue | Frequency |
|:----:|------------|-------|:---------:|
| 1 | §820.198 | Complaint handling inadequate | Very high |
| 2 | §820.22 | Quality audit deficiencies | High |
| 3 | §820.30 | Design control failures | High |
| 4 | §820.90 | CAPA ineffective | High |
| 5 | §820.184 | Device history record incomplete | Medium |
| 6 | §820.75 | Process validation insufficient | Medium |
| 7 | §820.50 | Purchasing controls lacking | Medium |

---

## Chapter 6 — Regional & Domain Variants

### 6.1 Global Market Access Comparison

| Dimension | US (FDA) | EU (MDR) | Japan (PMDA) | China (NMPA) | Canada (HC) |
|-----------|----------|----------|-------------|-------------|-------------|
| Classification levels | 3 (I, II, III) | 4 (I, IIa, IIb, III) | 4 (I, II, III, IV) | 3 (I, II, III) | 4 (I, II, III, IV) |
| Pathway | 510(k), PMA, De Novo | Conformity assessment via NB | Shonin (approval), Todokede (notification) | Registration certificate | License application |
| Clinical data | Varies by pathway | Clinical evaluation (all); investigation (most IIb/III) | Japanese clinical data usually required | Chinese clinical trials often required | Clinical evidence per classification |
| QMS standard | 21 CFR 820 (→ ISO 13485 aligned 2024) | ISO 13485 (via NB audit) | QMS compliance (MHLW Ordinance 169) | GB/T 42061 (≈ISO 13485) | ISO 13485 (MDSAP) |
| Timeline | 510(k): 3-12mo; PMA: 12-36mo | 6-18 months (NB dependent) | 12-24 months | 12-36 months | 6-12 months |
| Post-market | MDR reports; recalls; 5-day reports | PMS; PMCF; vigilance; PSUR; annual safety report | PMDA safety; ADR reporting | Adverse event reporting | Mandatory problem reporting |
| MDSAP recognized | ✅ (not mandatory) | ❌ (not recognized for MDR) | ✅ (limited) | ❌ | ✅ (**mandatory** since 2019) |
| UDI | ✅ (GUDID) | ✅ (EUDAMED — phased) | ✅ (in development) | ✅ (implemented) | ✅ |
| Language | English | Local language + English | Japanese | Chinese (Mandarin) | English + French |

### 6.2 Emerging Markets Strategy

| Market | Approach | Key Consideration |
|--------|----------|------------------|
| India (CDSCO) | Manufacturing license + import license | Price sensitivity; local testing often needed |
| Brazil (ANVISA) | GMP certificate + registration | MDSAP accepted; Portuguese documentation |
| South Korea (MFDS) | Technical documentation review + Korean testing | K-MFDS specific requirements; Korean test reports |
| Saudi Arabia (SFDA) | Relies on FDA/EU clearance + local registration | GCC mutual recognition |
| Mexico (COFEPRIS) | Registration using FDA/EU clearance as basis | Free trade agreement benefits (USMCA) |
| ASEAN (harmonized) | AMDD (ASEAN Medical Device Directive) | Moving toward mutual recognition; not all states implemented |

---

## Chapter 7 — Comparison

### 7.1 EU MDD vs. EU MDR

| Dimension | MDD (93/42/EEC) | MDR (2017/745) |
|-----------|-----------------|----------------|
| Legal type | Directive (transposed nationally) | **Regulation (directly applicable)** |
| Classification | Class I, IIa, IIb, III | Same + **Rule 17 (software)** upclassified |
| Clinical evidence | Clinical evaluation (often literature-based) | **Strengthened clinical evaluation** + PMCF mandatory |
| Notified Body audit | Less rigorous; unannounced audits rare | **More rigorous; unannounced audits mandatory** |
| Post-market | Limited PMS requirements | **Comprehensive PMS + PMCF + PSUR + annual safety report** |
| Transparency | Limited (no central database) | **EUDAMED** (public database; UDI; clinical investigations) |
| Person Responsible for Regulatory Compliance | Not required | **PRRC mandatory** (Art 15) |
| Labeling | Essential requirements | **UDI mandatory; implant card; symbols per EN ISO 15223** |
| Reprocessing of single-use | Left to member states | **EU-wide rules** (Art 17) |
| Economic operators | Limited chain | **All in chain responsible** (manufacturer, AR, importer, distributor) |
| Software classification | Often Class I (if not part of device) | **Rule 17: minimum Class IIa** (standalone diagnostic/therapeutic SW) |
| Penalties | Determined by member states | Guidance for penalties; member states set amounts |
| Legacy devices | Certificate valid until expiry | **Extended transitions (2027/2028)** due to NB capacity issues |

### 7.2 510(k) vs. PMA vs. De Novo

| Dimension | 510(k) | PMA | De Novo |
|-----------|--------|-----|---------|
| Risk class | Class II (some Class I) | Class III | Novel low-moderate risk (Class I/II) |
| Requirement | Substantial equivalence to predicate | Safety and effectiveness demonstrated | General/special controls sufficient |
| Clinical data | Usually bench/literature; sometimes clinical | **Clinical trials usually required** | Risk-based (usually bench + limited clinical) |
| Review time | 90-day statutory (reality: 3-12 months) | 180-day statutory (reality: 12-36 months) | 150 days (reality: 6-12 months) |
| User fee (2024) | ~$21,760 (small business: $5,440) | ~$421,000 (small business: $105,000) | ~$6,500 |
| Approval type | "Clearance" (not approval) | "Approval" | "Authorization" (creates new classification) |
| Post-market | 21 CFR 820; MDR reporting | 21 CFR 820; PMA supplements for changes; MDR reporting | Same as 510(k) once classified |
| Number per year | ~3,000 submissions/year | ~30-50 original PMAs/year | ~200-300/year (growing) |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 Global Regulatory Pathway Decision Tree

```mermaid
graph TB
    START[Medical Device —<br/>Where to market?]
    
    START --> US{US Market?}
    START --> EU{EU Market?}
    START --> JP{Japan?}
    START --> GLOBAL{Multiple<br/>Markets?}
    
    US --> USCLASS{Classification?}
    USCLASS -->|"Class I"| EXEMPT[510(k) Exempt<br/>(most Class I)]
    USCLASS -->|"Class II"| K510[510(k)<br/>Find predicate device]
    USCLASS -->|"Novel, low-mod risk"| DENOVO[De Novo<br/>No predicate needed]
    USCLASS -->|"Class III"| PMA_P[PMA<br/>Clinical trials]
    
    EU --> EUCLASS{EU MDR Rule?}
    EUCLASS -->|"Class I"| EUSELF[Self-Declaration<br/>+ Technical Documentation]
    EUCLASS -->|"Class IIa+"| EUNB[Notified Body<br/>ISO 13485 audit<br/>Technical documentation review]
    EUCLASS -->|"Class III"| EUNBFULL[Full NB Review<br/>+ Expert Panel<br/>+ Clinical Investigation]
    
    JP --> JPCLASS{Japan Class?}
    JPCLASS -->|"Class I"| JPTODO[Todokede<br/>(Notification)]
    JPCLASS -->|"Class II (certified)"| JPNINSHO[Ninsho<br/>(Third-party certification)]
    JPCLASS -->|"Class III-IV"| JPSHONIN[Shonin<br/>(PMDA Approval)]
    
    GLOBAL --> MDSAP_G[MDSAP Audit<br/>Single audit for 5 markets<br/>+ Individual submissions]
```

### 8.2 Medical Device Technical Documentation Structure (EU MDR)

```mermaid
graph TB
    subgraph "Technical Documentation (Annex II + III)"
        DD[Device Description<br/>• Intended purpose<br/>• Variants/accessories<br/>• Principles of operation<br/>• Raw materials<br/>• Functional elements]
        
        DM[Design & Manufacturing<br/>• Design stages<br/>• Manufacturing process<br/>• Facilities<br/>• Suppliers<br/>• Process validation]
        
        RM[Risk Management<br/>• ISO 14971 process<br/>• Hazard identification<br/>• Risk estimation<br/>• Risk control measures<br/>• Benefit-risk analysis<br/>• Residual risk acceptance]
        
        VV[Verification & Validation<br/>• Pre-clinical: bench testing<br/>• Biocompatibility (ISO 10993)<br/>• Electrical safety (IEC 60601)<br/>• Software (IEC 62304)<br/>• Usability (IEC 62366)<br/>• Sterilization validation]
        
        CE[Clinical Evaluation<br/>• Clinical evaluation plan (CEP)<br/>• Literature review (MEDDEV 2.7/1)<br/>• Clinical investigation data<br/>• Equivalence justification<br/>• Clinical evaluation report (CER)]
        
        PMS[Post-Market Surveillance<br/>• PMS plan<br/>• PMCF plan<br/>• PSUR template<br/>• Vigilance procedures<br/>• Trend reporting]
    end
```

---

## Chapter 9 — Case Studies

### 9.1 Startup — SaMD (AI Diagnostic) from Concept to Market

| Phase | Detail | Timeline |
|-------|--------|----------|
| **Product** | AI algorithm detecting skin cancer from dermoscopy images; standalone mobile app; Class IIa (EU) / Class II (US) | - |
| **Concept (Month 1-6)** | (1) Clinical need validated: dermatologists miss 10% melanomas; AI can assist. (2) Regulatory strategy: De Novo (FDA — no predicate for autonomous AI skin dx); MDR Class IIa (EU — Rule 11/17). (3) Standards: IEC 62304 Class B software; ISO 14971; IEC 82304-1; FDA AI/ML guidance. (4) Training data: 150,000 annotated dermoscopy images; multi-center; diverse skin types. |  |
| **Development (Month 6-18)** | (1) IEC 62304 software development process (Class B): software development plan, architecture, detailed design, unit testing, integration testing. (2) Algorithm development: CNN architecture; training/validation/test split (60/20/20); bias testing across skin tones (Fitzpatrick I-VI). (3) Risk management per ISO 14971: false negative (missed melanoma) = highest risk; false positive (unnecessary biopsy) = medium risk. Risk controls: confidence thresholds; "see dermatologist" output for borderline cases. (4) Usability (IEC 62366): formative studies (2 rounds); summative study (15 dermatologists; 15 GP users). |  |
| **Validation (Month 18-24)** | (1) Clinical performance study: prospective, multi-center, 2,000 lesions; compared to histopathology gold standard. Results: sensitivity 95.2%, specificity 89.1% (exceeds average dermatologist). (2) Software verification: regression testing; performance testing; security testing; SBOM generated. (3) Predetermined Change Control Plan (PCCP): defined how algorithm can be updated post-market without new submission (per FDA AI/ML guidance). |  |
| **Submission (Month 24-30)** | (1) FDA De Novo: submitted with clinical data, software documentation, PCCP, cybersecurity (SBOM). Received "authorization" after 10 months (interactive review). (2) EU MDR: Technical documentation prepared per Annex II/III; Notified Body (BSI) audit of QMS + TD review. CE certificate issued (Class IIa). | Total: 30 months concept to first market |
| **Post-Market (Ongoing)** | (1) PMCF study: real-world performance monitoring (prospective registry; 10,000 patients/year). (2) Algorithm updates via PCCP: retrained quarterly with new data; performance monitored for drift. (3) Vigilance: 2 false negative reports in first year → investigated → within acceptable risk levels per risk management file. |  |
| **Key Learnings** | Regulatory: start early (regulatory strategy at concept); PCCP enabled iterative AI improvement without repeat submissions. Technical: bias testing critical (early versions performed poorly on dark skin → addressed with balanced training data). Business: De Novo created new classification → subsequent competitors can use 510(k) pathway (predicate exists now). |  |

### 9.2 Recall Failure Analysis — Infusion Pump Software Defect

| Aspect | Detail |
|--------|--------|
| Device | Infusion pump (Class II, 510(k) cleared); software-controlled drug delivery |
| Incident | Software defect caused pump to deliver 10x programmed dose under specific sequence of button presses during dose change |
| Discovery | 3 adverse events reported over 6 months (2 serious injuries; 1 near-miss caught by nurse) |
| Root cause | Software defect in dose-change interrupt handler: race condition between display update and motor control. When user pressed "start" during dose display refresh (200ms window), previous dose variable was used instead of updated dose. |
| Regulatory failure | (1) IEC 62304 Class B software (should have been Class C — could cause death). (2) Risk management: race condition not identified in hazard analysis. (3) Testing: unit tests existed but no integration test for concurrent user input + display + motor control. (4) Design review: interrupt handling reviewed by software team only (no clinical input on dose scenarios). |
| FDA action | (1) Class I recall (most serious). (2) Warning letter (21 CFR 820.30 — design controls; 820.90 — CAPA). (3) Consent decree: independent review of all software before new releases; enhanced QMS requirements. |
| Corrective actions | (1) Software fix: atomic dose update (display + motor + variable in single critical section). (2) Reclassified software to IEC 62304 Class C. (3) Full FMEA for all software-controlled drug delivery paths. (4) Added integration test suite: 500+ scenarios including concurrent operations. (5) Independent verification of all safety-critical code paths. |
| Industry impact | FDA 2014 Infusion Pump Improvement Initiative; increased scrutiny of software in drug delivery; cited in FDA cybersecurity guidance as example of software risk |

---

## Chapter 10 — Future Evolution & Industry Trends

| Trend | Timeline | Impact |
|-------|----------|--------|
| FDA-EU convergence | 2024-2026 | 21 CFR 820 alignment with ISO 13485; reduced dual documentation |
| AI/ML predetermined change control | Now | PCCP enables post-market algorithm updates without new submission |
| Software as Medical Device (SaMD) growth | Now-2030 | Fastest growing segment; digital therapeutics; AI diagnostics |
| Cybersecurity as market requirement | 2023+ | SBOM mandatory (FDA); connected device security essential |
| Real-World Evidence (RWE) | Now-2025 | EHR data; registries; wearable data for regulatory decisions |
| EU MDR transition completion | 2027-2028 | All legacy devices must have MDR certificates or exit market |
| EUDAMED full deployment | 2025-2027 | Public transparency; UDI database; clinical investigations registry |
| Regulatory convergence (IMDRF) | Ongoing | Global harmonization of definitions, principles, SaMD classification |
| Digital health regulation simplification | 2024+ | International Medical Device Regulators Forum (IMDRF) streamlining for low-risk digital health |
| 3D printing / point-of-care manufacturing | Now-2026 | New regulatory frameworks for patient-matched devices |
| Companion diagnostics + theranostics | Now | Combined product regulation; CDx guidance updates |
| Sustainability / environmental requirements | 2025+ | IEC 60601-1-9; EU REACH/RoHS for medical devices; MDR GSPR 10.4 |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level (RA Associate / Quality Engineer)

**Q1:** Explain the difference between FDA 510(k) clearance and EU MDR CE marking.  
**A:** These are the two major market authorization pathways: **FDA 510(k)** = US market. Core concept is "substantial equivalence" — you prove your device is substantially equivalent to an already-marketed predicate device (similar intended use, same/different technology but equivalent performance). You compare to the predicate with bench testing, possibly clinical data. If FDA agrees you're substantially equivalent, you get "clearance" (not "approval"). Key: you need a predicate device that's already on market. **EU MDR CE marking** = EU/EEA market. No predicate concept. Instead, you demonstrate compliance with **General Safety and Performance Requirements** (GSPR, Annex I of MDR) through technical documentation. A Notified Body audits your QMS (ISO 13485) and reviews your technical documentation. You need: risk management (ISO 14971), clinical evaluation (MDR Art 61), applicable standards compliance (IEC 60601, IEC 62304, etc.), and post-market surveillance plan. Key differences: (1) Predicate vs. GSPR approach. (2) FDA reviews the submission directly; EU uses third-party Notified Bodies. (3) CE marking requires ongoing NB surveillance (annual audits); 510(k) doesn't require ongoing FDA review (though 820 inspections happen). (4) EU MDR requires comprehensive post-market surveillance (PMS plan, PMCF, PSUR); FDA post-market requirements are separate from the 510(k) process.

**Q2:** What is ISO 14971 and why is it considered the most important standard in medical devices?  
**A:** ISO 14971:2019 is the international standard for **application of risk management to medical devices**. It's considered the most important because: (1) **Universal requirement** — every medical device regulatory system in the world requires risk management. FDA (design controls require risk analysis), EU MDR (GSPR 1 explicitly references ISO 14971), Japan, China, Canada — all require it. (2) **Connects everything** — risk management feeds into: design inputs (risk-derived requirements), testing (risk-based test coverage), clinical evaluation (benefit-risk), usability (use-related risks), post-market surveillance (risk monitoring). (3) **Lifecycle process** — not a one-time activity. Risk management file lives throughout device lifecycle: initial hazard analysis → updated during design → updated with post-market data → retirement. Key process: Intended use → Hazard identification → Risk estimation (severity × probability) → Risk evaluation (acceptable?) → Risk control (reduce/eliminate) → Residual risk evaluation → Benefit-risk analysis → Production & post-production monitoring. Output: Risk Management File (RMF) containing: risk management plan, risk analysis (FMEA/FTA), risk evaluation, risk control measures, overall residual risk assessment, risk management report.

### Tier 2: Mid-Level (RA Manager / Software Quality Lead)

**Q3:** Your company has a Class IIb implantable device under MDD certificate expiring in 2027. What's your transition strategy to EU MDR?  
**A:** [Full answer covers: (1) Gap analysis: MDD Essential Requirements vs. MDR GSPR; identify new requirements (GSPR 17 cybersecurity, enhanced clinical evidence, UDI, PRRC, PMS/PMCF/PSUR). (2) Clinical evidence: biggest gap — MDR requires clinical evaluation per MEDDEV 2.7/1 Rev4 AND likely PMCF study (ongoing clinical data collection for implantables); assess if current clinical data sufficient or if supplemental clinical investigation needed. (3) Notified Body: apply NOW to designated MDR Notified Body (wait times 12-18 months); schedule audit. (4) Technical documentation: rewrite per Annex II/III format (not just update MDD file); structured TD per MDCG guidance. (5) Post-market: develop PMS plan, PMCF plan, PSUR template, trend reporting procedures. (6) Timeline: start 30+ months before MDD certificate expiry; NB review for Class IIb implantable takes 12-18 months after submission. (7) Risk: if NB capacity unavailable, device may lose CE marking → cannot sell in EU → revenue impact. Mitigation: engage NB early; consider second NB as backup; join industry associations lobbying for further extensions.]

### Tier 3: Senior (VP Regulatory Affairs / Head of Quality)

**Q4:** Design a global regulatory strategy for a novel AI-powered closed-loop insulin delivery system (artificial pancreas) targeting US, EU, Japan, and China simultaneously.  
**A:** [Full answer covers classification in each market (US: Class III PMA or De Novo for novel; EU: Class III MDR Rule 22 — active therapeutic with integrated diagnostic; Japan: Class IV Shonin; China: Class III registration). Standards: IEC 62304 Class C software; ISO 14971; IEC 60601-1; IEC 62366-1; FDA AI/ML guidance (PCCP); interoperability (connected to CGM). Clinical strategy: single pivotal trial designed to meet all 4 regulators simultaneously (FDA IDE + multi-center; EU clinical investigation per MDR; include Japanese sites for PMDA bridging; China will likely require separate local trial but negotiate). Regulatory interactions: pre-submission (FDA); scientific advice (NB/competent authority); consultation (PMDA); CDE consultation (NMPA). Cybersecurity: SBOM; insulin dosing safety (over/under delivery); wireless protocol security (CGM→algorithm→pump); FDA cybersecurity guidance 2023 compliance. AI/ML: PCCP for algorithm learning; EU AI Act high-risk (medical device Class IIa+); locked algorithm for initial submission → PCCP for post-market updates. Timeline: 4-5 years from concept to multi-market approval; stagger launches (US first typically, EU parallel, Japan/China following).]

---

## Chapter 12 — Cheat Sheet & Quick Reference

### Device Classification Quick Guide

```
US FDA:
  Class I   → Low risk → 510(k) exempt (most) → General Controls only
  Class II  → Moderate → 510(k) clearance → General + Special Controls
  Class III → High risk → PMA approval → Clinical trials required
  Novel low/moderate → De Novo authorization

EU MDR:
  Class I    → Self-declaration (no NB for basic Class I)
  Class IIa  → Notified Body (QMS audit + TD review)
  Class IIb  → Notified Body (full TD review + design dossier)
  Class III  → Notified Body + Expert Panel + Clinical Investigation (usually)
```

### Key Standards for Medical Devices

```
UNIVERSAL:        ISO 14971 (Risk Management) — REQUIRED EVERYWHERE
QMS:              ISO 13485:2016 (Quality Management System)
Software:         IEC 62304 (Class A/B/C software lifecycle)
Electrical:       IEC 60601-1 (Safety) + IEC 60601-1-2 (EMC)
Usability:        IEC 62366-1 (Usability engineering)
Biocompatibility: ISO 10993 series (material characterization + testing)
Sterilization:    ISO 11135 (EtO) / ISO 11137 (Radiation) / ISO 17665 (Steam)
Clinical:         ISO 14155 (Clinical investigations)
Labeling:         EN ISO 15223-1 (Symbols)
```

### IEC 62304 Software Safety Classes

```
Class A: No injury or damage to health possible → Minimal documentation
Class B: Non-serious injury possible         → Moderate documentation
Class C: Death or serious injury possible     → Full documentation + unit testing
```

### Regulatory Timelines (Realistic)

```
FDA 510(k):        6-12 months (with preparation: 12-18 months total)
FDA De Novo:       9-15 months
FDA PMA:           18-36 months (clinical trial time adds 2-5 years before)
EU MDR Class IIa:  12-18 months (NB audit + review)
EU MDR Class III:  18-30 months (including clinical investigation)
Japan (Shonin):    12-24 months
China (NMPA):      18-36 months (often requires local clinical trial)
Canada:            6-12 months (MDSAP expedites)
```

### Post-Market Requirements (EU MDR)

```
PMS Plan:     Required for ALL classes (Art 84)
PMCF Plan:    Required for Class IIa+ (Annex XIV Part B)
PSUR:         Class IIa+: every 2 years | Class IIb/III: annually
Vigilance:    Serious incidents → competent authority (within 15 days; trending: per FSN)
Trend Report: Report statistically significant increase in incidents
Field Safety: FSCA (Field Safety Corrective Action) + FSN (Field Safety Notice)
```

### Critical Deadlines

```
EU MDR full application:     May 26, 2021 ✅
MDD certificates validity:   Class III: May 2027; Others: May 2028 (extended)
IVDR full application:       May 26, 2022 (with extended transitions to 2025-2028)
FDA QMSR (820 update):       February 2, 2026 (compliance date)
FDA Cybersecurity (SBOM):    Mandatory for new submissions (Oct 2023+)
```

---

*End of Document — 00_Medical_Device_Regulatory_Landscape.md*
