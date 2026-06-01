# Miscellaneous Industry Standards — Comprehensive Reference

**Category:** 99 — Miscellaneous Industries  
**Document:** 00 — Miscellaneous Industries Standards Overview  
**Scope:** Oil & Gas, Mining, Agriculture, Food Safety, Chemicals, Pharmaceuticals, Broadcasting  
**Format:** Concise reference guide (500-1000 words per industry)  
**Audience:** Cross-domain engineers, regulatory analysts, technology generalists

---

## 1. Oil & Gas

### Core Standards

| Standard | Title | Scope |
|----------|-------|-------|
| API 6A | Wellhead & Christmas Tree Equipment | Pressure-containing equipment design |
| API 650 | Welded Tanks for Oil Storage | Atmospheric storage tank design |
| API 570 | Piping Inspection Code | In-service piping inspection |
| API 580/581 | Risk-Based Inspection (RBI) | Inspection prioritization methodology |
| IEC 61511 | Functional Safety (Process Industry) | SIS (Safety Instrumented Systems) |
| IEC 61508 | Functional Safety (Generic) | SIL 1-4 lifecycle |
| NORSOK | Norwegian shelf standards | Offshore petroleum regulations |
| ISO 13628 | Subsea Production Systems | Subsea wellhead/tree equipment |
| ATEX (EU 2014/34/EU) | Explosive Atmospheres | Equipment for hazardous zones |
| IECEx | International Ex certification | Explosive atmosphere equipment |

### Zone Classification (IEC 60079-10)

| Zone | Gas/Vapor | Dust | Frequency of Explosive Atmosphere |
|------|-----------|------|----------------------------------|
| Zone 0 / 20 | Continuous (>1000 hr/yr) | Continuous dust cloud | Equipment: Category 1 (EPL Ga/Da) |
| Zone 1 / 21 | Likely in normal operation (10-1000 hr/yr) | Occasional dust cloud | Equipment: Category 2 (EPL Gb/Db) |
| Zone 2 / 22 | Not likely, brief if occurs (<10 hr/yr) | Abnormal dust deposit | Equipment: Category 3 (EPL Gc/Dc) |

### Digital Oilfield Standards
- **WITSML** (Wellsite Information Transfer Standard Markup Language): Real-time drilling data exchange
- **PRODML**: Production data exchange
- **RESQML**: Reservoir characterization data
- **OPC UA for Oil & Gas**: Companion specification for process data

---

## 2. Mining

### Core Standards

| Standard | Title | Scope |
|----------|-------|-------|
| ISO 19296:2018 | Mining Autonomous Systems Safety | Autonomous haulage safety |
| IEC 60079 | Explosive Atmospheres Equipment | Methane detection in underground |
| IEC 62745 | Safety of Machinery — Proximity Detection | Collision avoidance |
| ISO 17757:2019 | Earth-moving — Autonomous Machine Safety | General autonomous mobile equipment |
| AS/NZS 4240 | Controlled Start/Stop (Australia) | Mining electrical safety |
| MSHA 30 CFR Part 7/18/75 | US Mine Safety (ventilation, electrical) | US regulatory compliance |

### Key Technologies & Standards
- **Autonomous Haulage Systems (AHS)**: ISO 17757 + ISO 19296 + site-specific SMS
- **Proximity Detection Systems (PDS)**: IEC 62745 (Levels 1-9 warning→intervention)
- **Underground Communication**: Leaky feeder (VHF), WiFi 6, Private LTE/5G (3GPP)
- **Ventilation on Demand (VOD)**: Real-time gas monitoring (CH₄, CO, CO₂) per MSHA

---

## 3. Agriculture (Precision Agriculture)

### Core Standards

| Standard | Title | Scope |
|----------|-------|-------|
| ISO 11783 (ISOBUS) | Tractors & Machinery — Serial Data Network | Agricultural equipment communication |
| ISO 11786 | Link between tractor and implements | Sensing and signaling |
| AEF ISOBUS certification | Agricultural Electronics Foundation | Interoperability testing |
| OGC (Open Geospatial) | GeoJSON, WFS, WMS | Geospatial data for field mapping |
| ISO 22006 | QMS for crop production | Agriculture quality management |

### ISOBUS (ISO 11783) Parts

| Part | Title | Function |
|------|-------|----------|
| Part 1 | General standard | Architecture overview |
| Part 3 | Data link layer | CAN 250 kbps |
| Part 5 | Network management | Address claiming |
| Part 6 | Virtual terminal (VT) | Universal display interface |
| Part 7 | Implement messages | Task data exchange |
| Part 10 | Task Controller | Prescription map execution |
| Part 11 | Mobile data dictionary | Standard parameter definitions |
| Part 14 | Sequence control | Automated sequences |

### Precision Ag Technologies
- **RTK GNSS**: cm-level positioning (ISO 12188 for testing)
- **Variable Rate Technology (VRT)**: ISO 11783-10 task controller
- **Drone/UAV spraying**: Regulated per national aviation authority + ISO 11783 integration
- **Farm Management Information Systems (FMIS)**: ISO 11783-10 + cloud platforms

---

## 4. Food Safety

### Core Standards

| Standard | Title | Scope |
|----------|-------|-------|
| ISO 22000:2018 | Food Safety Management Systems | HACCP + PRP + management system |
| FSSC 22000 | Food Safety System Certification | ISO 22000 + sector-specific PRPs |
| HACCP (Codex Alimentarius) | Hazard Analysis & Critical Control Points | 7 principles, 12 steps |
| BRC Global Standard | British Retail Consortium Food Safety | UK retailer requirement |
| IFS Food | International Featured Standard | European retailer requirement |
| SQF (Safe Quality Food) | Level 1-3 food safety | GFSI-recognized scheme |
| EU Regulation 852/2004 | Hygiene of Foodstuffs | EU food hygiene law |
| FDA 21 CFR Part 117 | Current Good Manufacturing Practice (cGMP) | US food safety regulation |
| GS1 standards | Barcodes + traceability | Supply chain identification |

### HACCP 7 Principles
1. Conduct Hazard Analysis
2. Determine Critical Control Points (CCPs)
3. Establish critical limits
4. Establish monitoring procedures
5. Establish corrective actions
6. Establish verification procedures
7. Establish record-keeping

### GFSI-Recognized Schemes
FSSC 22000 | BRC | IFS | SQF | GLOBALG.A.P | Canada GAP

---

## 5. Chemicals (REACH, GHS)

### Core Standards

| Standard | Title | Scope |
|----------|-------|-------|
| EU REACH (1907/2006) | Registration, Evaluation, Authorisation of Chemicals | EU chemical safety regulation |
| GHS (UN) / CLP (EU 1272/2008) | Globally Harmonized System of Classification & Labelling | Hazard communication |
| ISO 11014 | Safety Data Sheet (SDS) content | 16-section SDS standard |
| IEC 60079 | Explosive Atmosphere Equipment | Chemical plant electrical equipment |
| IEC 61511 | SIS for Process Industry | Chemical plant safety systems |
| ATEX 2014/34/EU | Equipment for explosive atmospheres | Chemical facility equipment |
| API 752/753 | Building Siting, Portable Buildings | Blast-resistant design |

### GHS Hazard Categories

| Hazard Type | Examples | Pictogram |
|-------------|----------|-----------|
| Physical | Flammable, explosive, oxidizing, pyrophoric | Flame, exploding bomb |
| Health | Acute toxicity, carcinogenicity, mutagenicity | Skull, health hazard |
| Environmental | Aquatic toxicity (acute/chronic) | Dead fish/tree |

### Process Safety Management (PSM)
- **US OSHA 29 CFR 1910.119**: PSM of Highly Hazardous Chemicals (14 elements)
- **EU Seveso III Directive (2012/18/EU)**: Major accident prevention (upper/lower tier)
- **IEC 61511 (SIS)**: Safety instrumented systems for chemical processes

---

## 6. Pharmaceuticals (GxP)

### Core Standards

| Standard | Title | Scope |
|----------|-------|-------|
| FDA 21 CFR Part 211 | Current Good Manufacturing Practice (cGMP) | US pharmaceutical manufacturing |
| EU GMP (EudraLex Vol. 4) | Good Manufacturing Practice | EU pharmaceutical manufacturing |
| ICH Q7 | GMP for Active Pharmaceutical Ingredients | API manufacturing |
| ICH Q8/Q9/Q10 | Pharmaceutical Development / Risk / Quality System | Quality by Design (QbD) |
| GAMP 5 (ISPE) | Good Automated Manufacturing Practice | Computerized system validation |
| FDA 21 CFR Part 11 | Electronic Records, Electronic Signatures | Digital compliance |
| EU Annex 11 | Computerised Systems | EU electronic system compliance |
| ISO 13485 | Medical Devices QMS | Medical device quality (related) |
| USP <1058> | Analytical Instrument Qualification | Equipment qualification |

### GAMP 5 Software Categories

| Category | Type | Validation Effort | Example |
|----------|------|------------------|---------|
| 1 | Infrastructure software | Minimal | OS, databases, middleware |
| 3 | Non-configured products | Low-moderate | Standard instruments, firmware |
| 4 | Configured products | Moderate | ERP, LIMS, MES (configured) |
| 5 | Custom applications | High (full lifecycle) | Custom control systems, bespoke software |

### Data Integrity (ALCOA+)
- **A**ttributable: Who performed and when?
- **L**egible: Readable throughout retention
- **C**ontemporaneous: Recorded at time of activity
- **O**riginal: First capture (or certified true copy)
- **A**ccurate: Error-free, truthful
- **+** Complete, Consistent, Enduring, Available

---

## 7. Broadcasting & Media

### Core Standards

| Standard | Title | Scope |
|----------|-------|-------|
| SMPTE ST 2110 | Professional Media Over IP | Uncompressed video/audio over IP |
| SMPTE ST 2022-6/7 | SDI over IP | Legacy SDI transport over IP |
| SMPTE ST 12M | Time Code | LTC/VITC time code |
| AES67 | Audio over IP | High-performance audio networking |
| AES3 (AES/EBU) | Digital Audio Interface | Professional digital audio |
| NMOS (AMWA IS-04/05/06) | Networked Media Open Specifications | Discovery, registration, control |
| DVB-T2/S2/C2 | Digital Video Broadcasting | Terrestrial/satellite/cable delivery |
| ATSC 3.0 | Advanced Television Systems Committee | Next-gen US broadcast (IP-based) |
| ITU-R BT.2020 | Ultra High Definition TV | Wide color gamut, HDR |
| ITU-R BT.2100 | HDR TV | PQ/HLG transfer functions |

### SMPTE ST 2110 Suite

| Part | Media Type | Protocol |
|------|-----------|----------|
| ST 2110-10 | System timing & definitions | PTP (IEEE 1588) |
| ST 2110-20 | Uncompressed active video | RTP (RFC 4175) |
| ST 2110-21 | Traffic shaping & delivery timing | Narrow/wide senders |
| ST 2110-22 | Constant bit-rate compressed video | JPEG XS (ST 2124) |
| ST 2110-30 | PCM digital audio | AES3-based (RTP) |
| ST 2110-31 | AES3 transparent transport | Full AES3 frame |
| ST 2110-40 | Ancillary data | Captions, metadata |

### Key Broadcast Concepts
- **PTP (Precision Time Protocol)**: IEEE 1588-2019, SMPTE ST 2059 profile
- **NMOS IS-04**: Discovery & Registration (service advertisement)
- **NMOS IS-05**: Device Connection Management (signal routing)
- **JT-NM TR-1001**: Reference architecture for IP broadcast facilities

---

## Cross-Industry Comparison Matrix

| Feature | Oil & Gas | Mining | Pharma | Food | Broadcast |
|---------|-----------|--------|--------|------|-----------|
| Safety standard | IEC 61511 | ISO 19296 | GMP/GAMP 5 | HACCP | Electrical safety |
| Explosion risk | ATEX/IECEx | IEC 60079 | Clean room | N/A | N/A |
| Data integrity | PI System | Historian | 21 CFR Part 11 | Traceability (GS1) | SMPTE metadata |
| Automation | OPC UA | Autonomous (17757) | GAMP Cat. 4/5 | Industry 4.0 | NMOS/ST 2110 |
| Cybersecurity | IEC 62443 | IEC 62443 | 21 CFR Part 11 | Supply chain | EBU R143 |

---

## Interview Questions (Cross-Industry)

### Tier 1: Entry-Level
1. What is IEC 61511 and how does it differ from IEC 61508?
2. Explain HACCP's 7 principles for food safety.
3. What is GAMP 5 and its software categories?
4. What are ATEX zones 0, 1, and 2?

### Tier 2: Mid-Level
1. How does ISOBUS (ISO 11783) enable precision agriculture implement interoperability?
2. Explain SMPTE ST 2110 architecture for IP-based broadcast facilities.
3. How do you implement REACH compliance for a chemical substance registration?
4. Walk through 21 CFR Part 11 compliance for a pharmaceutical MES system.

### Tier 3: Senior/Lead
1. Design a safety instrumented system (SIS) per IEC 61511 for an offshore oil platform.
2. Architect an autonomous haulage system meeting ISO 19296 for an open-pit mine.
3. How do you validate a GAMP Category 5 custom application for pharmaceutical manufacturing?
4. Design an NMOS/ST 2110 IP broadcast infrastructure for a live sports production facility.

### Tier 4: Principal
1. How should process safety standards evolve for fully autonomous chemical plants?
2. Propose a unified data standard for precision agriculture integrating ISOBUS + IoT + AI.
3. Design a global pharmaceutical supply chain traceability system meeting US DSCSA + EU FMD.
4. How do you architect a converged broadcast/streaming platform meeting both SMPTE ST 2110 and CMAF/DASH?

---

*Document Version: 1.0 | Last Updated: May 2026 | Author: Technology Standards Team*
