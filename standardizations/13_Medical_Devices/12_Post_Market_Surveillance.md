# Post-Market Surveillance for Medical Devices — PMS, PMCF, Vigilance

**Topic:** Post-market surveillance systems, clinical follow-up, vigilance reporting, and field safety corrective actions for medical devices  
**Standard:** EU MDR (2017/745) Articles 83-92; FDA 21 CFR 803/806/822; MEDDEV 2.12/1 rev 8; IMDRF PMS Guidance; ISO 13485:2016 Clause 8.2.1  
**SDO:** EU Commission/MDCG; FDA/CDRH; IMDRF; ISO/TC 210  
**Audience:** Post-market surveillance specialists, regulatory affairs, quality engineers, clinical affairs, vigilance officers, QARA managers  
**Prerequisites:** Medical device regulatory basics (EU MDR/FDA), quality management (ISO 13485), risk management (ISO 14971)

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 1984 | FDA Medical Device Reporting (MDR) regulation (21 CFR 803) | First mandatory adverse event reporting for US medical devices |
| 1993 | EU Medical Device Directive (93/42/EEC) | Introduced CE marking; basic vigilance requirements |
| 1998 | EU MEDDEV 2.12/1 (Vigilance Guidance) | Harmonized incident reporting and field safety corrective actions in EU |
| 2005 | FDA 21 CFR 822 (Postmarket Surveillance) | FDA authority to require postmarket surveillance studies for specific devices |
| 2010 | GHTF/SG2/N54R8 (Post-market Surveillance) | Global harmonized guidance on PMS |
| 2012 | PIP breast implant scandal (EU) | Fraudulent implants; exposed weaknesses in EU post-market surveillance; catalyst for MDR |
| 2017 | **EU MDR (2017/745) published** | Dramatically strengthened PMS requirements; PMCF mandatory; PSUR; PMS plan/report |
| 2018 | IMDRF PMS Guidance documents | International harmonization of post-market surveillance approaches |
| 2019 | MDCG guidance documents begin (2019-6; 2020-7; 2020-8) | EU implementing guidance for PMS, PMCF, PSUR, trend reporting |
| 2021 | EU MDR applies (May 26) | Full PMS/PMCF/PSUR requirements enforceable |
| 2022 | FDA MDUFA V (reauthorization) | Enhanced post-market authorities; real-world evidence |
| 2023 | MDCG 2023-8 (PMCF guidance update) | Detailed PMCF methodology guidance |
| 2024 | EUDAMED partially functional | EU database for vigilance reporting; device registration; PMS data |
| 2025 | EU MDR transition period ends | All devices must have full MDR PMS systems in place |

### 1.2 PMS Framework Overview

```mermaid
graph TB
    subgraph "Post-Market Surveillance System"
        PMS_PLAN[PMS Plan<br/>(Article 84)<br/>━━━━━━━━━━━<br/>• Data sources<br/>• Methods<br/>• Frequency<br/>• Responsibilities]
        
        PMS_DATA[Data Collection<br/>━━━━━━━━━━━<br/>• Complaints<br/>• Vigilance reports<br/>• Clinical data (PMCF)<br/>• Literature<br/>• Market experience<br/>• Registry data]
        
        PMS_ANALYSIS[Analysis & Evaluation<br/>━━━━━━━━━━━<br/>• Trend analysis<br/>• Risk-benefit update<br/>• Clinical evaluation update<br/>• State-of-the-art comparison]
    end
    
    subgraph "Outputs"
        PMS_REPORT[PMS Report<br/>(Class I)<br/>━━━━━━━━━━━<br/>Updated when necessary<br/>Part of technical documentation]
        
        PSUR[PSUR<br/>(Class IIa/IIb/III)<br/>━━━━━━━━━━━<br/>Periodic Safety Update Report<br/>IIa: every 2 years<br/>IIb/III: annually]
        
        PMCF[PMCF<br/>(All classes)<br/>━━━━━━━━━━━<br/>Post-Market Clinical Follow-up<br/>Ongoing clinical data collection<br/>Part of clinical evaluation]
        
        VIGILANCE[Vigilance Reporting<br/>━━━━━━━━━━━<br/>• Serious incidents<br/>• Field Safety Corrective Actions<br/>• Trend reporting<br/>• Periodic Summary Reporting]
    end
    
    subgraph "Actions"
        CAPA[CAPA<br/>━━━━━━━━━━━<br/>Corrective actions<br/>Preventive actions<br/>Design changes]
        
        FSCA[Field Safety Corrective Action<br/>━━━━━━━━━━━<br/>• Recall<br/>• Modification<br/>• Destruction<br/>• Information supplement]
        
        FSN[Field Safety Notice<br/>━━━━━━━━━━━<br/>Communication to users<br/>about FSCA or safety issue]
    end
    
    PMS_PLAN --> PMS_DATA --> PMS_ANALYSIS
    PMS_ANALYSIS --> PMS_REPORT & PSUR & PMCF
    PMS_ANALYSIS --> VIGILANCE
    VIGILANCE --> FSCA --> FSN
    PMS_ANALYSIS --> CAPA
```

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 EU MDR PMS Requirements (Articles 83-92)

| Article | Title | Content |
|:-------:|-------|---------|
| 83 | Post-market surveillance system | General obligation; proactive and systematic; part of QMS |
| 84 | **PMS plan** | Documented plan per device/device group; data sources; methods; schedule |
| 85 | **PMS report** (Class I) | Summary of PMS results and conclusions; updated when necessary |
| 86 | **PSUR** (Class IIa/IIb/III) | Periodic Safety Update Report; conclusions on risk-benefit; sales volumes; serious incidents summary |
| 87 | **Vigilance** (serious incident reporting) | Manufacturer reports serious incidents to competent authority within specified timelines |
| 88 | **Trend reporting** | Statistically significant increase in incidents/expected side effects |
| 89 | Analysis of serious incidents and FSCAs | Competent authority evaluation |
| 90 | Analysis by competent authorities (CA) | Assessment and follow-up by CAs |
| 91 | Implementing acts | Commission can adopt further implementing rules |
| 92 | Electronic system on vigilance and PMS (EUDAMED) | EU-wide database for incident reporting and FSCA recording |

### 2.2 PMS Plan Required Content (Article 84)

| Element | Content | Detail |
|---------|---------|--------|
| Scope | Device(s) covered; device group justification | Which devices share similar enough risk profiles to be grouped |
| Data sources (proactive) | Active data collection methods | PMCF studies; registries; user surveys; market research; social media monitoring |
| Data sources (reactive) | Passive data collection | Complaints; vigilance reports; literature; recalls; standards changes |
| Methods | How data will be collected, analyzed, interpreted | Statistical methods; signal detection; trend analysis; literature review protocol |
| Indicators and thresholds | What triggers action | Complaint rate thresholds; incident rate limits; performance degradation triggers |
| Schedule/frequency | How often PMS activities occur | Continuous monitoring; periodic analysis (monthly/quarterly); annual PSUR |
| Interface with CAPA | How PMS findings feed corrective actions | Escalation criteria; CAPA trigger thresholds; management review integration |
| Interface with risk management | How PMS updates risk analysis | Risk management file update triggers; risk-benefit re-evaluation criteria |
| Interface with clinical evaluation | How PMS feeds clinical evaluation report (CER) | PMCF data integration; CER update schedule; clinical evidence sufficiency assessment |
| Responsibilities | Who does what | PMS owner; vigilance officer; clinical affairs; quality; regulatory |

### 2.3 Vigilance Reporting Timelines

| Event Type | EU MDR Timeline | FDA Timeline | Report To |
|-----------|:--------------:|:------------:|-----------|
| **Serious incident — death or unexpected serious deterioration** | 10 days (from awareness) | 30 calendar days (from awareness); 5 days for reportable malfunction if correction would be recall | EU: Competent Authority (via EUDAMED); FDA: via MedWatch/eMDR |
| **Serious incident (life-threatening)** | 10 days | 30 calendar days | Same |
| **Serious incident (requiring public health threat action)** | 2 days | 5 working days (if awareness of remedial action) | Same |
| **Trend report** (statistically significant increase in expected incidents) | Without undue delay (upon detecting trend) | N/A (covered by MDR reports) | Competent Authority |
| **Field Safety Corrective Action (FSCA)** | Report to CA; issue FSN to users | Report to FDA as recall; register with FDA recall database | CA + users (FSN) |
| **Periodic Summary Reporting (PSR)** | Allowed for well-characterized, non-serious known events (per MDCG guidance) | N/A | Competent Authority |

---

## Chapter 3 — Technical Deep Dive

### 3.1 PMCF (Post-Market Clinical Follow-up)

| PMCF Aspect | Content | Detail |
|-------------|---------|--------|
| **Purpose** | Proactively collect and evaluate clinical data on deployed devices | Confirm ongoing safety and performance; identify emerging risks; validate risk-benefit |
| **When mandatory** | **All devices** under EU MDR (unless justified exception per Annex XIV) | Even Class I devices need PMCF plan (can be simplified if justified) |
| **PMCF Plan** | Documented plan for clinical data collection | General methods; specific methods; rationale; objectives; timelines; endpoints |

**PMCF Methods:**

| Method | Description | When to Use |
|--------|-------------|-------------|
| **PMCF study** (prospective) | Dedicated clinical study on device already on market | Highest evidence level; when specific clinical questions need answering; novel devices; Class III; implants |
| **Registry participation** | Enroll device in established device/disease registry | Long-term outcome tracking; implants; large population; comparative data |
| **Survey/questionnaire** | Structured data collection from users/patients | User experience; satisfaction; real-world use patterns; rare events |
| **Real-world data analysis** | Analysis of EHR/claims data; hospital databases | Large-scale safety signals; comparative effectiveness; long-term outcomes |
| **Literature review** (systematic) | Systematic search for published clinical evidence on device or equivalents | Ongoing; identifies new safety signals; state-of-the-art updates |
| **Complaint analysis** | Structured analysis of customer complaints for clinical signal | Continuous; identifies emerging patterns; early warning |
| **Explant analysis** | Analysis of retrieved/returned devices | Implants; understand failure modes; material degradation; wear |
| **Post-market clinical study** | Formal study per ISO 14155 conducted post-market | When specific endpoints need rigorous evidence; when pre-market data insufficient |

### 3.2 PSUR (Periodic Safety Update Report) — Content

| Section | Content | Source |
|---------|---------|--------|
| Summary | Device description; intended purpose; indications; period covered | Technical documentation |
| Volume data | Units sold/distributed per region per period | Sales/distribution records |
| Serious incidents summary | Number and types of serious incidents reported | Vigilance database |
| FSCAs | Field safety corrective actions taken during period | FSCA records |
| Trend analysis | Statistical analysis of incident rates; comparison to previous periods; comparison to expected rates | PMS data; statistical analysis |
| Clinical data update | PMCF results; new clinical evidence; literature review findings | PMCF reports; literature review |
| Risk-benefit determination | Updated risk-benefit analysis based on all post-market data | ISO 14971 risk file; clinical evaluation |
| Conclusions | Overall conclusions on safety and performance; changes needed; actions planned | Analysis synthesis |
| Frequency | Class IIa: at least every 2 years; Class IIb and III: **annually** | EU MDR Article 86 |

### 3.3 Signal Detection and Trend Analysis

| Method | Description | Application |
|--------|-------------|-------------|
| **Complaint rate monitoring** | Track complaints per unit sold/per device-year | Rate increase = signal; comparison to baseline established in first year |
| **Proportional reporting ratio (PRR)** | Compare proportion of specific event for device vs. other similar devices in vigilance database | PRR > 2 + chi-squared significance = signal |
| **Observed-to-expected (O/E) ratio** | Compare observed event rate to expected rate (from clinical data/literature) | O/E > 1 + statistically significant = signal |
| **Bayesian signal detection** | BCPNN (Bayesian Confidence Propagation Neural Network) or MGPS (Multi-item Gamma Poisson Shrinker) | Pharmacovigilance-derived methods adapted for device vigilance |
| **Control charts** | Statistical process control (SPC) applied to complaint/incident rates | Upper control limit exceeded = out-of-control signal |
| **Time-series analysis** | Trend analysis over time; seasonal adjustment; detect acceleration | Increasing trend = investigate; step change = event-driven |

### 3.4 Complaint Handling → Vigilance Decision Tree

```mermaid
flowchart TB
    COMPLAINT[Complaint Received]
    
    ASSESS{Is this a<br/>reportable event?}
    
    SERIOUS{Is it a<br/>SERIOUS INCIDENT?<br/>(death, serious deterioration,<br/>life-threatening, hospitalization,<br/>permanent impairment)}
    
    DEVICE{Did device<br/>contribute?<br/>(causal or contributing factor)}
    
    REPORT[File Vigilance Report<br/>━━━━━━━━━━━<br/>EU: to Competent Authority<br/>(via EUDAMED)<br/>FDA: MedWatch 3500A<br/>Timeline: per event severity]
    
    INVESTIGATE[Investigate Root Cause<br/>━━━━━━━━━━━<br/>• Failure analysis<br/>• Use error analysis<br/>• Design review<br/>• Statistical assessment]
    
    TREND{Does this indicate<br/>a TREND?<br/>(statistically significant<br/>increase)}
    
    TREND_REPORT[File Trend Report<br/>━━━━━━━━━━━<br/>Report to CA<br/>"without undue delay"]
    
    FSCA_CHECK{Is a FSCA<br/>needed?}
    
    FSCA_ACTION[Execute FSCA<br/>━━━━━━━━━━━<br/>• Define corrective action<br/>• Issue FSN to users<br/>• Report to CA<br/>• Track effectiveness]
    
    PMS_ONLY[PMS Database Only<br/>━━━━━━━━━━━<br/>• Record in PMS system<br/>• Include in trend analysis<br/>• Feed into next PSUR<br/>• No immediate report]
    
    COMPLAINT --> ASSESS
    ASSESS -->|"Not a device issue<br/>(user inquiry; logistics)"| PMS_ONLY
    ASSESS -->|"May be device-related"| SERIOUS
    SERIOUS -->|"Yes"| DEVICE
    SERIOUS -->|"No (minor; no serious harm)"| PMS_ONLY
    DEVICE -->|"Yes/Cannot exclude"| REPORT
    DEVICE -->|"Clearly NOT device-related"| PMS_ONLY
    REPORT --> INVESTIGATE
    INVESTIGATE --> TREND
    TREND -->|"Yes"| TREND_REPORT
    TREND -->|"No"| FSCA_CHECK
    TREND_REPORT --> FSCA_CHECK
    FSCA_CHECK -->|"Yes"| FSCA_ACTION
    FSCA_CHECK -->|"No"| PMS_ONLY
```

---

## Chapter 4 — Implementation Guide

### 4.1 PMS System Implementation

| Component | Implementation | Tools/Systems |
|-----------|---------------|---------------|
| **Complaint management** | Electronic system for receiving, recording, investigating, trending complaints | QMS software (Veeva Vault Quality; MasterControl; TrackWise); integrated with CRM |
| **Vigilance reporting** | Process for assessing reportability; filing reports within timelines; tracking CA interactions | Regulatory information management (RIM) system; EUDAMED account; FDA eMDR/eSTAR |
| **Literature monitoring** | Systematic literature review at defined intervals; automated alerts for new publications on device/equivalent | PubMed alerts; Ovid; Embase; AI-powered literature monitoring (Evidera; Eversana) |
| **Registry data collection** | Participation in relevant registries; data contribution; outcome tracking | Device-specific registries (NJR for orthopedics; MAUDE for adverse events); national registries |
| **Clinical data (PMCF)** | PMCF studies; real-world evidence; survey platforms | EDC systems (Medidata; REDCap); survey tools; hospital partnerships |
| **Signal detection** | Statistical analysis tools; automated trend detection; threshold monitoring | Statistical software (R, SAS); custom dashboards; automated alerting |
| **Document management** | PMS plan; PMS reports; PSURs; PMCF evaluation reports; FSN templates | Quality/regulatory document management system |
| **Management review** | Regular review of PMS data with decision-making authority | QMS management review (ISO 13485 clause 5.6); dedicated PMS review board |

### 4.2 FSCA Types and Examples

| FSCA Type | Definition | Example |
|-----------|-----------|---------|
| **Recall (return)** | Device returned to manufacturer or designated location | Implant with fracture risk: all unimplanted units returned; implanted patients monitored |
| **Recall (modification)** | Device modified (in field or at manufacturer) | Software update to fix calculation error; hardware modification to fix connector |
| **Recall (destruction)** | Device destroyed | Single-use device with contamination risk: destroyed on-site |
| **Device modification** | Permanent change to device or accessories | Firmware update correcting dosing algorithm; label update; IFU revision |
| **Information supplement** | Additional information provided to users without physical change | FSN informing users of proper technique to avoid identified risk; additional training provided |
| **Monitoring/follow-up** | Enhanced monitoring of patients with implanted devices | Cardiac lead advisory: increased monitoring frequency; imaging protocol for early detection |
| **Temporary suspension** | Halt distribution/use pending investigation | Device temporarily removed from market during investigation; reinstated after fix confirmed |

### 4.3 Field Safety Notice (FSN) Content

| Section | Content | Purpose |
|---------|---------|---------|
| **Identification** | Manufacturer name; device name; model/catalog numbers; lot/serial numbers affected; UDI (if applicable) | User identifies if they have affected device |
| **Description of problem** | What the issue is; how it manifests; under what conditions | User understands the risk |
| **Hazard/risk assessment** | What harm could occur; probability; severity | User assesses urgency |
| **Advice/action** | What user must do: stop using; inspect; update; return; monitor patients; specific clinical actions | Clear actionable instructions |
| **Timeline** | When action must be completed; urgency level | User prioritizes |
| **Contact information** | Who to contact for questions; return instructions; support hotline | Enable communication |
| **Acknowledgment** | Request user to acknowledge receipt and completion of actions | Verify FSCA effectiveness |
| **Transmittal** | Record of distribution (who received FSN; when) | Track FSCA coverage |

---

## Chapter 5 — Regulatory Reporting

### 5.1 EU Vigilance Reporting (MDR Article 87)

| Report Type | Trigger | Timeline | Content |
|-------------|---------|:--------:|---------|
| **Individual Serious Incident Report** | Death or unanticipated serious deterioration | 10 days (from awareness) | Device info; incident description; patient outcome; manufacturer assessment |
| **Individual Serious Incident Report** | Public health threat (imminent) | **2 days** | As above; urgency justification |
| **Follow-up Report** | Investigation progress; new information | As needed; within 10 days of new info | Updated assessment; investigation findings; planned actions |
| **Final Report** | Investigation complete | Within 10 days of completion | Root cause; corrective actions; risk assessment; conclusions |
| **Trend Report** | Statistically significant increase in known/expected events | Without undue delay (upon detection) | Trend data; statistical analysis; assessment; planned actions |
| **FSCA Report** | Decision to take field safety corrective action | Before FSCA implementation (or simultaneously for urgent) | FSCA description; scope; timeline; FSN draft |
| **Periodic Summary Report (PSR)** | Agreed events (non-serious; well-characterized) | Per agreement with CA (e.g., quarterly) | Event counts; types; no new safety concerns |

### 5.2 FDA Reporting Requirements

| Report | Trigger | Timeline | Regulation |
|--------|---------|:--------:|:----------:|
| **MDR (Medical Device Report)** — Death | Device may have caused or contributed to death | **30 calendar days** from awareness | 21 CFR 803 |
| **MDR** — Serious injury | Device may have caused or contributed to serious injury | **30 calendar days** | 21 CFR 803 |
| **MDR** — Malfunction | Malfunction would likely cause death or serious injury if it recurred | **30 calendar days** | 21 CFR 803 |
| **5-Day Report** | Manufacturer becomes aware that remedial action is needed AND device has caused death/serious injury | **5 working days** | 21 CFR 803.53 |
| **Recall** | Correction or removal (FSCA equivalent) | Report to FDA District Office; 10 working days for written report | 21 CFR 806 |
| **Annual Certification** | Annual summary to FDA of all MDRs filed | Annual | 21 CFR 803 |
| **522 Postmarket Surveillance** | FDA orders specific post-market surveillance study | Per FDA order (typically within 15 months) | 21 CFR 822 |

### 5.3 CAPA Integration

| CAPA Phase | PMS Input | Output |
|:----------:|-----------|--------|
| **Identification** | Complaint trends; vigilance signals; PMCF findings; audit findings; literature | Problem statement; risk assessment; decision to open CAPA |
| **Investigation** | Complaint root cause; device failure analysis; use error analysis; clinical data | Root cause determination; scope of impact |
| **Corrective action** | Risk assessment update; design options; clinical evidence for change | Corrective action plan; implementation timeline; effectiveness criteria |
| **Implementation** | Change control (design change? labeling change? manufacturing change?) | Updated device/documentation; validation complete |
| **Effectiveness verification** | Post-implementation PMS data; complaint rate monitoring; PMCF | Evidence that CAPA resolved the issue; no new issues introduced |
| **Closure** | Confirmed effectiveness; management review | CAPA closed; PMS plan updated; risk file updated; PSUR reflects |

---

## Chapter 6 — Regional Context

### 6.1 PMS Requirements by Region

| Region | Framework | PMCF Mandatory? | PSUR Required? | Vigilance System |
|--------|-----------|:---:|:---:|---|
| **EU** | MDR 2017/745 Articles 83-92 | **Yes** (all classes) | Yes (Class IIa: 2yr; IIb/III: 1yr) | EUDAMED; Competent Authority reports |
| **US (FDA)** | 21 CFR 803/806/822 | FDA 522 orders (specific devices only) | Not as "PSUR" — annual MDR certification | MedWatch; MAUDE database |
| **Canada** | CMDR (MDSAP) | Expected via MDSAP | Mandatory (problem reports) | MDPR (Mandatory Problem Reporting) |
| **Japan** | PMD Act | Post-market surveillance studies required | Re-examination/re-evaluation system | PMDA adverse event reporting |
| **Australia** | TGA (Therapeutic Goods Act) | Expected | Mandatory (IRIS reports) | IRIS (Incident Report Investigation Scheme) |
| **International** | IMDRF PMS Guidance | Best practice | Best practice | Per national system |

### 6.2 EU MDR vs. MDD (What Changed in PMS)

| Aspect | MDD (93/42/EEC) | EU MDR (2017/745) |
|--------|:---:|:---:|
| PMS plan | Not explicitly required | **Mandatory** (Article 84); documented per device |
| PMS report | Not required | **Mandatory** for Class I (Article 85) |
| PSUR | Not required | **Mandatory** for Class IIa/IIb/III (Article 86) |
| PMCF | Mentioned in MEDDEV 2.12/2; often minimal | **Mandatory** (Annex XIV Part B); detailed plan and report; integral to clinical evaluation |
| Trend reporting | Limited | **Explicit requirement** (Article 88); manufacturer must report statistical increases |
| Vigilance timelines | Varied; less strict | Stricter; 2 days for public health threats; 10 days standard |
| EUDAMED | Not available | Central database for vigilance, FSCA, PMS data (partially functional 2024) |
| Notified Body role | Limited PMS oversight | NB reviews PSUR; audits PMS system; assesses PMCF adequacy |
| Transparency | Limited public access | EUDAMED partially public; FSN publicly accessible |

---

## Chapter 7 — Comparison

### 7.1 EU MDR vs. FDA Post-Market Requirements

| Dimension | EU MDR | FDA |
|-----------|:---:|:---:|
| PMS plan/system | Mandatory for ALL devices | Required by QSR (21 CFR 820.198 complaints); less prescriptive |
| Clinical follow-up | **PMCF mandatory** for all classes | 522 postmarket surveillance for specific devices (FDA-ordered) |
| Periodic reporting | **PSUR** (annual for Class IIb/III; biennial for IIa) → to Notified Body | Annual MDR certification (simple attestation); no PSUR equivalent |
| Vigilance timeline | 10 days (serious); 2 days (public health threat) | 30 days (death/serious injury); 5 days (awareness of needed remedial action) |
| Trend reporting | **Explicit requirement** (Article 88) | Not as specific requirement; covered by ongoing MDR obligations |
| FSCA reporting | Report to CA before/during FSCA; issue FSN | Report as recall (21 CFR 806); voluntary or FDA-ordered |
| Database | EUDAMED (central EU database; partially public) | MAUDE (public searchable database of MDRs); recalls database |
| NB/FDA oversight of PMS | NB reviews PSUR; audits PMS; assesses PMCF annually | FDA may inspect complaint files; post-market inspections; 522 reviews |
| Public transparency | EUDAMED data partially public; FSN searchable | MAUDE fully public; recall database public |
| Consequence of non-compliance | CE certificate suspension/withdrawal; market removal | Warning letter; consent decree; mandatory recall; civil/criminal penalties |

### 7.2 PMS vs. Pharmacovigilance (Drug Safety Monitoring)

| Aspect | Medical Device PMS | Drug Pharmacovigilance |
|--------|:---:|:---:|
| Regulatory basis | EU MDR/IVDR; FDA 21 CFR 803 | EU (Directive 2010/84); FDA 21 CFR 314.80 |
| Periodic reporting | PSUR (device) | PSUR/PBRER (drug) — similar name, different content |
| Signal detection | Emerging; adapted from pharma methods | Mature; well-established methods (PRR, ROR, BCPNN) |
| Clinical follow-up | PMCF studies (post-market clinical) | Post-authorization safety studies (PASS) |
| Registry data | Growing use (device registries) | Well-established (national drug registries) |
| Causality assessment | Device-related vs. use error vs. patient condition | Drug-related vs. underlying disease vs. concomitant medication |
| Volume of reports | Lower (fewer reporters; less awareness) | Higher (healthcare professionals and patients both report) |
| Unique challenges | Device iterations (same device, many versions); use errors; combination products; long-lived implants | Drug interactions; rare side effects; large populations; off-label use |
| Benefit-risk framework | ISO 14971 (device risk management) | ICH E2C (R2) (drug PBRER) |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 Complete PMS Data Flow

```mermaid
graph TB
    subgraph "Data Sources (Proactive)"
        PMCF_S[PMCF Studies<br/>━━━━━━━━━━━<br/>Clinical follow-up<br/>Registry data<br/>Surveys<br/>Real-world evidence]
        LIT[Literature Monitoring<br/>━━━━━━━━━━━<br/>PubMed/Embase alerts<br/>Conference abstracts<br/>Standards updates<br/>Competitor data]
        MARKET[Market Surveillance<br/>━━━━━━━━━━━<br/>User surveys<br/>Sales data<br/>Training feedback<br/>Field service reports]
    end
    
    subgraph "Data Sources (Reactive)"
        COMP[Complaints<br/>━━━━━━━━━━━<br/>Customer complaints<br/>User feedback<br/>Distributor reports]
        VIGIL[Vigilance Reports<br/>━━━━━━━━━━━<br/>Incident reports<br/>Adverse events<br/>Near misses]
        AUDIT[Internal/External Audits<br/>━━━━━━━━━━━<br/>NB audit findings<br/>Internal audits<br/>Supplier findings]
    end
    
    subgraph "PMS System (Analysis)"
        DB[(PMS Database<br/>━━━━━━━━━━━<br/>All data consolidated<br/>Searchable<br/>Trend-capable)]
        
        SIGNAL[Signal Detection<br/>━━━━━━━━━━━<br/>Statistical analysis<br/>Threshold monitoring<br/>Trend detection<br/>Anomaly identification]
        
        RISK_UPDATE[Risk Management Update<br/>━━━━━━━━━━━<br/>Update ISO 14971 file<br/>New hazards identified?<br/>Risk-benefit changed?<br/>Controls adequate?]
        
        CER_UPDATE[Clinical Evaluation Update<br/>━━━━━━━━━━━<br/>Update CER with new evidence<br/>Benefit-risk conclusion<br/>State-of-art comparison<br/>Equivalence re-assessment]
    end
    
    subgraph "Outputs & Actions"
        PSUR_O[PSUR<br/>(to NB annually/biennially)]
        PMCF_R[PMCF Evaluation Report<br/>(update CER)]
        VIG_REPORT[Vigilance Reports<br/>(to CA per timelines)]
        FSCA_O[FSCA / FSN<br/>(if needed)]
        CAPA_O[CAPA<br/>(corrective actions)]
        DESIGN[Design Changes<br/>(feed back to R&D)]
    end
    
    PMCF_S & LIT & MARKET --> DB
    COMP & VIGIL & AUDIT --> DB
    DB --> SIGNAL
    SIGNAL --> RISK_UPDATE & CER_UPDATE
    RISK_UPDATE --> PSUR_O & VIG_REPORT & FSCA_O & CAPA_O
    CER_UPDATE --> PMCF_R & PSUR_O
    CAPA_O --> DESIGN
    FSCA_O --> VIG_REPORT
```

### 8.2 FSCA Execution Timeline

```mermaid
sequenceDiagram
    participant MFG as Manufacturer
    participant CA as Competent Authority
    participant NB as Notified Body
    participant USER as Healthcare Facilities
    participant PATIENT as Patients (if applicable)
    
    Note over MFG,PATIENT: Safety Issue Identified
    MFG->>MFG: Risk assessment<br/>Decision: FSCA needed
    
    Note over MFG,PATIENT: Day 0: FSCA Decision
    MFG->>CA: FSCA Report (initial notification)
    MFG->>NB: Inform NB of FSCA
    
    Note over MFG,PATIENT: Day 0-3: Prepare FSN
    MFG->>MFG: Draft Field Safety Notice (FSN)<br/>Content: problem; risk; action required
    MFG->>CA: Submit FSN for review (if required by CA)
    
    Note over MFG,PATIENT: Day 3-10: Issue FSN
    MFG->>USER: Distribute FSN to all affected users<br/>(all facilities with affected devices)
    USER->>USER: Identify affected devices<br/>Implement required actions
    
    Note over MFG,PATIENT: If patient notification needed
    USER->>PATIENT: Patient notification<br/>(if implant/patient-level action needed)
    
    Note over MFG,PATIENT: Ongoing: Track Effectiveness
    USER-->>MFG: Acknowledgment of FSN receipt
    USER-->>MFG: Confirmation of action completion
    MFG->>MFG: Track: % users acknowledged<br/>% actions completed<br/>Target: >95% completion
    
    Note over MFG,PATIENT: Close-out (30-90 days)
    MFG->>CA: Final FSCA report<br/>Effectiveness data<br/>Closure documentation
    MFG->>NB: FSCA closure notification
    CA-->>MFG: FSCA closure accepted
```

---

## Chapter 9 — Case Studies

### 9.1 Case Study: Implant FSCA (Hip Prosthesis)

| Aspect | Detail |
|--------|--------|
| Device | Metal-on-metal (MoM) hip prosthesis system; ~93,000 implanted worldwide (2003-2010) |
| Signal detection | PMS data revealed: (1) Registry data (National Joint Registry UK) showed higher-than-expected revision rates at 5 years (12% vs. 5% for ceramic-on-poly). (2) Vigilance reports increasing: metallosis, pseudotumor, elevated blood metal ions, pain. (3) PMCF data: cohort studies showed progressive performance degradation. (4) Literature: multiple publications correlating large-head MoM with adverse reactions to metal debris (ARMD). |
| Analysis | Manufacturer PMS system identified trend (statistically significant revision rate increase) at Year 3 post-launch in registry data. Internal debate on causality (design vs. surgical technique) delayed action by ~18 months. Regulatory pressure from MHRA, BfArM accelerated response. |
| FSCA | (1) **Phase 1 (Year 4)**: FSN issued to surgeons: enhanced patient monitoring recommended (annual blood metal ion testing; cross-sectional imaging if symptomatic); no new implantations recommended pending investigation. (2) **Phase 2 (Year 5)**: Voluntary recall of unimplanted devices; market withdrawal. (3) **Phase 3 (ongoing)**: Long-term patient monitoring program (10+ years); individual patient risk assessment; revision surgery if indicated. FSN to all implanting hospitals with patient tracking requirements. |
| Lessons | (1) Registry data was the earliest and strongest signal — manufacturers should actively participate in and monitor device registries. (2) PMS plan should include registry data as primary data source for implants. (3) Delay in action due to internal resistance to acknowledging issue → need clear escalation criteria in PMS plan (predetermined thresholds). (4) PMCF should have been designed before market launch with specific revision rate thresholds as triggers. (5) Patient notification for implants requires careful coordination (minimize alarm; provide clear guidance; enable monitoring). |
| Outcome | Market withdrawal; extensive patient monitoring program; regulatory changes (EU MDR strengthened implant surveillance requirements); >10,000 revision surgeries; significant litigation |

### 9.2 Case Study: Software FSCA (Dosimetry Error)

| Aspect | Detail |
|--------|--------|
| Device | Radiation therapy treatment planning software (Class IIb/Class III depending on jurisdiction) |
| Signal | 3 complaints over 4 months from different hospitals reporting unexpected dose distributions; patients received ~20% higher doses than planned to critical structures (spinal cord, brain stem) |
| Investigation | Root cause: software update (version 4.2.1) introduced calculation error in specific multi-leaf collimator (MLC) configuration; error only manifests with specific combination of: beam angle >30°; MLC leaf gap <5mm; specific machine model. Affected configuration used in ~15% of plans at hospitals with that machine model. |
| Risk assessment | Severity: potentially life-threatening (radiation-induced myelopathy from spinal cord overdose); Probability: ~15% of plans on affected machines; Population: ~2,000 patients potentially affected (estimated from sales + usage patterns) |
| FSCA | **Immediate (Day 0-2)**: (1) Urgent FSN to all hospitals running v4.2.1: "STOP using v4.2.1 for [specific MLC configuration]; revert to v4.2.0 for affected cases; MANUALLY verify all plans created since update using independent dose calculation." (2) Report to CAs in all markets (EU: multiple CAs; US: FDA). **Short-term (Day 2-14)**: (3) Emergency patch (v4.2.2) fixing calculation error; validated; distributed with installation guidance. (4) Patient-level review: work with each hospital to identify ALL patients treated with affected configuration since update; clinical physicist re-calculation of actual delivered dose. **Medium-term (Day 14-90)**: (5) Each hospital: patient notification for those receiving >5% overdose; clinical follow-up plan; specialist referral if indicated. (6) Root cause (internal): code review failure; unit test gap for this specific configuration; regression test suite inadequate. **Long-term**: (7) Enhanced testing: expanded regression test suite (now covers all MLC configurations × beam angles × machine models); automated configuration-specific testing in CI/CD pipeline; software verification per IEC 62304 strengthened. |
| Regulatory outcome | FSCA reported; Class II recall (US); CA followed up; manufacturer corrective action reviewed and accepted; lessons fed into IEC 62304 compliance (enhanced verification); PSUR documented the event and corrective actions |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **EUDAMED full functionality** | 2025-2026 | Central EU database for vigilance; FSCA tracking; UDI; device registration; transparency |
| **Real-world evidence (RWE)** | Now | Using EHR data, claims data, registries for post-market safety evaluation; reduces need for expensive prospective studies |
| **AI-powered signal detection** | Now-2026 | Machine learning for complaint text mining; automated causality assessment; predictive signal detection from complaint patterns |
| **Unique Device Identification (UDI)** traceability | Now (mandatory EU/US) | Track devices from manufacturer to patient; enable rapid recall notification; post-market performance tracking per device unit |
| **Patient registries expansion** | Now | More devices tracked in registries; mandatory registry participation for implants (some EU countries); long-term outcome data |
| **Proactive PMS (predictive)** | 2025-2028 | Shift from reactive (wait for complaints) to predictive (detect early signals from usage patterns, IoT data, connected device telemetry) |
| **Connected device PMS** | Now | IoT medical devices transmit performance data; real-time monitoring; automated anomaly detection; fleet-wide updates |
| **International harmonization** | Ongoing | IMDRF working toward harmonized reporting forms; single report accepted by multiple regulators; reduce manufacturer burden |
| **Patient involvement in PMS** | Growing | Direct patient reporting (not just through healthcare professionals); patient apps for outcome reporting; patient-reported outcomes (PROs) |
| **Cross-border data sharing** | 2025+ | EU EHDS enables cross-border safety signal detection; larger datasets for rare event detection |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is the difference between PMS, PMCF, and Vigilance?  
**A:** Three related but distinct post-market activities: **PMS (Post-Market Surveillance)** = the OVERALL SYSTEM for monitoring a device after market placement. It's the umbrella that encompasses ALL post-market activities. Per EU MDR Article 83, every manufacturer must have a PMS system that proactively and systematically collects, records, and analyzes data on quality, performance, and safety throughout the device's lifetime. PMS includes: complaint handling, vigilance, PMCF, literature review, market feedback, registry data — everything. **PMCF (Post-Market Clinical Follow-up)** = a SUBSET of PMS focused specifically on collecting CLINICAL DATA to confirm ongoing safety and clinical performance. It's the clinical evidence component of PMS. Methods: clinical studies, registry participation, surveys of clinical outcomes, literature review of clinical publications. Output: PMCF Evaluation Report (feeds into Clinical Evaluation Report). Required for ALL device classes under EU MDR. **Vigilance** = the regulatory REPORTING component. When something goes wrong (serious incident) or when you take corrective action (FSCA), you must REPORT to the Competent Authority within specified timelines. Vigilance is reactive (triggered by events). It includes: individual incident reports, trend reports, FSCA reports, Field Safety Notices. **Relationship**: PMS is the system; PMCF is one data collection method within PMS (clinical data); Vigilance is the regulatory reporting obligation when PMS identifies reportable events.

**Q2:** What is a PSUR and what does it contain?  
**A:** **PSUR = Periodic Safety Update Report** — a periodic document that summarizes the results of post-market surveillance and draws conclusions on the benefit-risk ratio of the device. Required by EU MDR Article 86 for: Class IIa (at least every 2 years); Class IIb and III (at least annually). Submitted to: Notified Body (who reviews it as part of ongoing conformity assessment). Content: (1) **Summary**: device description, intended purpose, period covered. (2) **Volume**: number of devices sold/distributed (units, by region). (3) **Serious incidents**: summary of all reported serious incidents during period; types; outcomes; comparison to previous periods. (4) **FSCAs**: any field safety corrective actions taken. (5) **Trend analysis**: statistical evaluation of incident rates; comparison to expected rates; trending over time. (6) **PMCF data**: results from clinical follow-up; new clinical evidence; literature findings. (7) **Risk-benefit conclusion**: based on ALL post-market data, does the benefit-risk ratio remain acceptable? Are new risks identified? Do current risk controls remain adequate? (8) **Actions planned**: any planned changes, investigations, or additional data collection. The PSUR is NOT just a data dump — it requires ANALYSIS and CONCLUSIONS. The manufacturer must actively assess whether the device remains safe and performant. The NB reviews the PSUR and may require additional actions.

### Tier 2: Mid-Level

**Q3:** How would you set up a PMS system for a new Class IIb implantable device being launched in the EU?  
**A:** PMS system for Class IIb implant: **PMS Plan** (documented before launch): (1) **Data sources**: Customer complaints (electronic system; 24/7 intake; categorization); Vigilance reports (internal assessment of reportability within 48 hours; reporting within 10 days); PMCF: prospective registry study (5-year follow-up; primary endpoint: revision-free survival; secondary: patient-reported outcomes, complications); national/international implant registries (NJR, NJRR equivalents in target markets); systematic literature review (quarterly PubMed + Embase search with predefined search strategy); explant analysis (protocol for returned devices); distributor feedback (quarterly surveys). (2) **Analysis methods**: complaint rate per 1000 device-years (monthly calculation); control chart monitoring with predefined upper control limit (2σ above baseline established in first 12 months); Kaplan-Meier survival analysis (annual from registry); subgroup analysis (by patient demographics, surgeon, technique, indication). (3) **Thresholds/triggers**: Complaint rate exceeding upper control limit → investigation; Any death or serious deterioration → immediate vigilance assessment; Revision rate >2× expected (from clinical investigation) → FSCA assessment; Any new hazard not in risk file → ISO 14971 update + CAPA evaluation. (4) **Timelines**: PSUR annually (submitted to NB ≥2 weeks before audit); PMCF evaluation report: annually (interim); at study completion (final); CER update: at least annually (per MDCG 2020-13). (5) **Responsibilities**: PMS Manager (overall system ownership); Vigilance Officer (reportability assessment, regulatory submissions); Clinical Affairs (PMCF study management, CER update); Quality (complaint handling, CAPA); Regulatory (CA interaction, NB communication). (6) **Infrastructure**: QMS software for complaints; statistical analysis tools; literature monitoring service; registry data agreement; EUDAMED registration and reporting.

### Tier 3: Senior

**Q4:** Your company has 15 product families across 40 countries. How do you design a scalable, efficient PMS organization that meets all regulatory requirements while being cost-effective?  
**A:** [Comprehensive answer covering: (1) **Organizational structure**: Central PMS/Vigilance team (10-15 people) responsible for: methodology, tools, signal detection, training, regulatory intelligence. Regional regulatory leads (US, EU, APAC) for jurisdiction-specific reporting. Product-line PMS owners (embedded in each product team) for device-specific clinical/technical knowledge. Matrix structure: central team provides infrastructure/methodology; product teams provide device expertise. (2) **Platform**: single global PMS database (all products, all regions); automated complaint intake from all channels (CRM integration, web portal, distributor feeds); automated reportability screening (rule-based + AI-assisted); automated timeline tracking (alerts before deadlines); regulatory submission integration (electronic reporting to FDA eMDR, EUDAMED, PMDA, TGA). (3) **Harmonized processes**: one complaint process globally (with regional adaptations for timelines); one PSUR template (populate region-specific content); one PMCF methodology (scalable to device risk); harmonized FSN template (NB/CA accepted). (4) **Efficiency gains**: product grouping for PMS plans/PSURs (devices with similar risk profiles grouped per MDCG 2022-1); literature monitoring: automated search with AI relevance screening (human review only for flagged articles); signal detection: automated statistical monitoring with human confirmation; PSUR generation: semi-automated from database (data extraction + standard analysis + human conclusions). (5) **Compliance across 40 countries**: regulatory intelligence function tracking evolving requirements per market; automated regulatory reporting (know which CA gets what, when, in what format, in what language); local vigilance contacts for markets requiring local representation; MDSAP participation (covers US/Canada/Australia/Japan/Brazil with single audit). (6) **Metrics/governance**: monthly PMS dashboard (complaint rates, vigilance compliance, PMCF enrollment, PSUR status, FSCA effectiveness); quarterly management review; annual PMS system effectiveness review; KPIs: reporting on-time rate (target >99%); FSCA effectiveness (>95% user acknowledgment); PMCF enrollment targets met.]

---

## Chapter 12 — Cheat Sheet & Quick Reference

### EU MDR PMS Document Requirements

```
Class I:       PMS Plan + PMS Report (updated when necessary)
Class IIa:     PMS Plan + PSUR (every 2 years) + PMCF Plan + PMCF Eval Report
Class IIb:     PMS Plan + PSUR (annually) + PMCF Plan + PMCF Eval Report
Class III:     PMS Plan + PSUR (annually) + PMCF Plan + PMCF Eval Report
Implants:      All above + specific PMCF requirements (Annex XIV Part B)
```

### Vigilance Reporting Timeline Quick Reference

```
EU MDR:
  Death / unanticipated serious deterioration:     10 days
  Public health threat:                             2 days  
  Trend (statistically significant increase):       Without undue delay
  FSCA:                                             Before implementation (or simultaneous if urgent)

FDA:
  Death or serious injury:                          30 calendar days
  Malfunction (could cause death/serious injury):   30 calendar days
  Remedial action awareness:                        5 working days
  Recall:                                          10 working days (written report)
```

### PSUR Quick Template

```
1. Executive Summary (key conclusions)
2. Device Description (intended purpose; classification; variants)
3. Sales/Distribution Data (units per region per period)
4. PMS Data Summary:
   a. Complaints (number; types; trends; rates)
   b. Serious Incidents (number; types; outcomes)
   c. FSCAs (during period)
   d. Literature findings
   e. PMCF results
5. Trend Analysis (statistical; comparison to previous periods)
6. Risk-Benefit Assessment (updated conclusion)
7. Conclusions and Planned Actions
```

### FSCA Decision Criteria

```
FSCA likely needed if:
□ Device presents unacceptable risk to patients/users
□ Non-compliance with essential requirements that creates risk  
□ Deficiency could lead to death or serious deterioration (even if hasn't yet)
□ Recall is necessary to prevent further harm
□ Information supplement needed to enable safe continued use

FSCA NOT needed if:
□ Issue is customer-specific (single unit; resolved locally)
□ No safety impact (purely cosmetic; non-clinical performance)
□ Issue addressed by normal maintenance/servicing within specifications
```

### PMS Data Sources Checklist

```
PROACTIVE (you go looking):
□ PMCF studies (clinical follow-up)
□ Registry data (device/disease registries)
□ User surveys / satisfaction monitoring
□ Systematic literature review
□ Standards/guidelines monitoring (state-of-art evolution)
□ Competitor device monitoring (recalls; safety issues)
□ Real-world data (EHR; claims; if accessible)

REACTIVE (data comes to you):
□ Customer complaints
□ Vigilance reports (internal + external)
□ Distributor/service partner reports
□ Audit findings (internal; NB; supplier)
□ Published recalls (competitors)
□ Regulatory authority communications
□ Healthcare professional feedback
□ Patient feedback / patient communities
```

---

*End of Document — 12_Post_Market_Surveillance.md*
