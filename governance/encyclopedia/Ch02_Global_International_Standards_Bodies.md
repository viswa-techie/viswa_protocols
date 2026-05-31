# Chapter 2 — Global International Standards Bodies (L1/L2)

## Universal Technology Standards Governance & Regulatory Architecture Encyclopedia
### First Edition, 2024–2026

---

## 2.1 Chapter Overview

This chapter covers the global treaty-level international organizations that produce the most authoritative, widely-adopted standards in technology. These bodies operate at **Tier L1** (International Standards Bodies) and represent the highest level of formal multi-national standardization governance.

**Bodies Covered:**
- ISO (International Organization for Standardization)
- IEC (International Electrotechnical Commission)
- ISO/IEC JTC1 (Joint Technical Committee 1 — Information Technology)
- ITU-T (International Telecommunication Union — Telecommunication Standardization Sector)
- ITU-R (ITU — Radiocommunication Sector)
- ITU-D (ITU — Development Sector)
- ANSI (American National Standards Institute — as ISO member body and US accreditation body)
- UNECE (United Nations Economic Commission for Europe — WP.29 vehicle regulations)

**Defers to:**
- IETF, W3C, OASIS → Chapter 3
- Consortium bodies (AUTOSAR, PCI-SIG) → Chapter 4
- IEEE SA, SAE → Chapter 5
- EU regulations (GDPR, AI Act) → Chapter 10

---

## 2.2 ISO — International Organization for Standardization

### ISO

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | International Organization for Standardization                 |
| Abbreviation / Alias         | ISO (from Greek "isos" = equal; NOT an acronym)               |
| Entry Type                   | Body                                                          |
| Governance Tier              | L1                                                            |
| Governing Organization       | ISO (self-governing, independent of any government)           |
| Parent / Umbrella Body       | None — top-level international body                           |
| Founded / Published          | 1947, Geneva, Switzerland                                      |
| Current Version / Edition    | N/A (body, not a standard)                                    |
| Specification Availability   | Paid — ISO store, national body stores                        |
| Open / Proprietary / Mixed   | Mixed — participation requires national body membership; specs are paid |
| Formal / Informal            | Formal — treaty-linked international organization             |
| Scope                        | Global                                                        |
| Primary Domain               | All technology and non-technology domains (25,000+ standards published) |
| Secondary Domains            | Management systems, healthcare, food safety, construction, etc. |
| Industry Relevance           | Universal — referenced in law, contracts, and procurement globally |
| Cross-Platform               | Yes                                                           |
| Cross-Language               | Yes (standards published in English, French, Russian)         |
| Cross-Domain                 | Yes                                                           |
| Automotive Relevance         | High (ISO 26262, ISO 21434, ISO 11898, ISO 14229, ISO 17987) |
| Cloud Relevance              | High (ISO/IEC 17788, 17789, 27017, 27018)                    |
| Embedded Relevance           | High (ISO 26262, IEC 61508 via IEC partnership)               |
| Safety-Critical Relevance    | High (ISO 26262, ISO 13849, ISO 25119)                        |
| Enterprise Relevance         | High (ISO 9001, ISO 27001, ISO 42001)                         |
| Compliance / Certification   | ISO management system standards enable third-party certification |
| Versioning Model             | Year-based: ISO XXXXX:YYYY (e.g., ISO 27001:2022)            |
| Adoption Model               | Voluntary (but regulatory reference makes many de-facto mandatory) |
| Competing / Adjacent Bodies  | IEC (electrotechnical), ITU (telecom), IEEE (some overlap)    |
| Relationship to Other Stds   | ISO/IEC JTC1 partnership; Vienna Agreement with CEN           |
| Typical Use Cases            | Quality management, information security, automotive safety, environmental management |
| Technology Stack Position    | Governance layer — defines requirements that implementations must meet |
| Implementation Maturity      | Mature                                                        |
| Notes                        | ISO is NOT a government body. It's an independent NGO with national body members. |

### ISO Organizational Structure

```
ISO General Assembly (all member bodies)
    │
    ├── ISO Council (governance)
    │
    ├── Technical Management Board (TMB)
    │       │
    │       ├── Technical Committees (TC) — ~250 active TCs
    │       │       │
    │       │       ├── Subcommittees (SC)
    │       │       │       │
    │       │       │       └── Working Groups (WG)
    │       │       │
    │       │       └── Working Groups (directly under TC)
    │       │
    │       └── Project Committees (PC) — for single-standard projects
    │
    └── Policy Development Committees
            ├── CASCO (Conformity Assessment)
            ├── COPOLCO (Consumer Policy)
            └── DEVCO (Developing Countries)
```

### ISO Stage Codes (Development Pipeline)

| Stage Code | Stage Name                           | Abbreviation | Meaning                                  |
|------------|--------------------------------------|--------------|------------------------------------------|
| 00         | Preliminary Stage                    | PWI          | Preliminary Work Item — earliest stage   |
| 10         | Proposal Stage                       | NP           | New Work Item Proposal (NP ballot)       |
| 20         | Preparatory Stage                    | WD           | Working Draft — WG internal              |
| 30         | Committee Stage                      | CD           | Committee Draft — TC/SC ballot           |
| 40         | Enquiry Stage                        | DIS          | Draft International Standard — P-member vote |
| 50         | Approval Stage                       | FDIS         | Final Draft IS — final P-member vote     |
| 60         | Publication Stage                    | IS           | International Standard — published       |
| 90         | Review Stage                         | —            | Systematic Review (every 5 years)        |
| 95         | Withdrawal Stage                     | —            | Standard withdrawn                       |

**Substage codes** add precision:
- 40.20 = DIS ballot initiated
- 40.60 = DIS ballot completed — approved
- 40.93 = DIS ballot — not approved (sent back)
- 60.60 = IS published

### ISO Voting Rules

| Vote Type | Approval Threshold | Negative Threshold | Who Votes |
|-----------|--------------------|--------------------|-----------|
| NP ballot (stage 10) | Simple majority of P-members | — | TC/SC P-members |
| CD ballot (stage 30) | Simple majority | — | TC/SC P-members |
| DIS ballot (stage 40) | ≥ 2/3 P-members approve | ≤ 1/4 of all votes negative | All ISO members |
| FDIS ballot (stage 50) | ≥ 2/3 P-members approve | ≤ 1/4 of all votes negative | All ISO members |

### Key ISO Technical Committees for Technology

| TC / JTC | Title                                              | Key Standards                              |
|----------|----------------------------------------------------|--------------------------------------------|
| ISO/IEC JTC1 | Information Technology                        | 27001, 42001, 14882 (C++), 9899 (C)       |
| ISO TC22  | Road Vehicles                                     | 26262, 21434, 14229, 11898, 20077         |
| ISO TC176 | Quality Management                                | ISO 9001, ISO 9000 family                  |
| ISO TC199 | Safety of Machinery                               | ISO 13849, ISO 12100                       |
| ISO TC204 | Intelligent Transport Systems                     | ISO 14813, ISO 17419                       |
| ISO TC215 | Health Informatics                                | ISO 13606, ISO 18308                       |
| ISO TC307 | Blockchain and DLT                                | ISO 22739 (terminology)                    |

---

## 2.3 IEC — International Electrotechnical Commission

### IEC

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | International Electrotechnical Commission                      |
| Abbreviation / Alias         | IEC                                                            |
| Entry Type                   | Body                                                          |
| Governance Tier              | L1                                                            |
| Governing Organization       | IEC (self-governing)                                           |
| Parent / Umbrella Body       | None — peer to ISO (electrotechnical domain)                   |
| Founded / Published          | 1906, London (oldest electrical standards body); now Geneva     |
| Current Version / Edition    | N/A (body)                                                    |
| Specification Availability   | Paid — IEC Webstore                                           |
| Open / Proprietary / Mixed   | Mixed                                                         |
| Formal / Informal            | Formal — international treaty-level                           |
| Scope                        | Global — electrotechnical domain (electrical, electronic, related technologies) |
| Primary Domain               | Electrotechnology — power, electronics, EMC, safety, batteries |
| Secondary Domains            | Medical devices, renewable energy, nuclear, audio/video        |
| Industry Relevance           | Universal for any product with electrical/electronic components|
| Cross-Platform               | Yes                                                           |
| Cross-Language               | Yes                                                           |
| Cross-Domain                 | Yes                                                           |
| Automotive Relevance         | High (IEC 61508 → ISO 26262 parentage, EV standards)         |
| Cloud Relevance              | Low (except data center power/cooling)                        |
| Embedded Relevance           | High (IEC 61508, IEC 62443, IEC 61131)                        |
| Safety-Critical Relevance    | Very High (IEC 61508 is THE root functional safety standard)  |
| Enterprise Relevance         | Medium (through ISO/IEC JTC1 partnership)                     |
| Compliance / Certification   | IECEE CB Scheme — mutual recognition of test certificates     |
| Versioning Model             | IEC XXXXX:YYYY (same pattern as ISO)                          |
| Adoption Model               | Voluntary + regulatory reference (EU low-voltage, machinery directives) |
| Competing / Adjacent Bodies  | ISO (non-electrical), IEEE (some overlap in power)            |
| Relationship to Other Stds   | ISO/IEC JTC1 partnership; IEC leads JTC1/SC25, SC27 (delegated) |
| Typical Use Cases            | Functional safety, EMC compliance, electrical safety, battery certification |
| Technology Stack Position    | Governance layer — safety and electrical requirements          |
| Implementation Maturity      | Mature                                                        |
| Notes                        | IEC is separate from ISO — they are sister organizations, not parent-child |

### Key IEC Technical Committees

| TC     | Title                                             | Key Standards                              |
|--------|---------------------------------------------------|--------------------------------------------|
| TC 44  | Safety of Machinery — Electrotechnical Aspects    | IEC 62061                                  |
| TC 56  | Dependability                                     | IEC 60812 (FMEA), IEC 61025 (FTA)         |
| TC 57  | Power Systems Management                          | IEC 61850 (substation automation)          |
| TC 65  | Industrial Process Measurement, Control and Automation | **IEC 61508** (Functional Safety), IEC 62443 (Industrial Cybersecurity), IEC 61131 (PLC programming) |
| TC 77  | Electromagnetic Compatibility (EMC)               | IEC 61000 series                           |
| TC 79  | Alarm and Electronic Security Systems             | IEC 62676 (video surveillance)             |
| TC 91  | Electronics Assembly Technology                   | IEC 61188 (PCB)                            |
| SC 45A | Nuclear Instrumentation                           | IEC 61513 (Nuclear safety)                 |

### IEC 61508 — The Mother Standard of Functional Safety

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Functional Safety of Electrical/Electronic/Programmable Electronic Safety-related Systems |
| Standard Number              | IEC 61508:2010 (Edition 2)                                     |
| Entry Type                   | Standard                                                       |
| Governance Tier              | L1                                                            |
| Governing Organization       | IEC TC 65                                                      |
| Parts                        | 7 parts (requirements + guidance)                              |
| SIL Levels                   | SIL 1, SIL 2, SIL 3, SIL 4                                    |
| Key concept                  | Probability of dangerous failure (PFD/PFH) determines SIL      |
| Daughter standards           | ISO 26262 (automotive), EN 50128 (railway), IEC 61511 (process), IEC 62061 (machinery), IEC 62304 (medical software) |
| Status                       | Active (Edition 3 in development)                              |

**IEC 61508 Parts:**

| Part | Title                                                          |
|------|----------------------------------------------------------------|
| 1    | General Requirements                                            |
| 2    | Requirements for E/E/PE Safety-Related Systems                  |
| 3    | Software Requirements                                           |
| 4    | Definitions and Abbreviations                                   |
| 5    | Examples of Methods for Determination of Safety Integrity Levels|
| 6    | Guidelines on Application of IEC 61508-2 and IEC 61508-3       |
| 7    | Overview of Techniques and Measures                             |

---

## 2.4 ISO/IEC JTC1 — Joint Technical Committee 1

### ISO/IEC JTC1

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | ISO/IEC Joint Technical Committee 1 — Information Technology   |
| Abbreviation / Alias         | JTC1                                                           |
| Entry Type                   | Body (Technical Committee)                                     |
| Governance Tier              | L1                                                            |
| Governing Organization       | Joint governance by ISO and IEC                                |
| Parent / Umbrella Body       | ISO + IEC (dual reporting)                                     |
| Founded / Published          | 1987 — formed by merging ISO TC97 and IEC TC83                |
| Scope                        | Global — all information technology standardization            |
| Primary Domain               | Information technology — programming languages, security, AI, cloud, biometrics |
| Key Output Standards         | ISO/IEC 27001, 27002, 42001, 14882 (C++), 9899 (C), 23270 (C#), 17788 (cloud) |
| Notes                        | JTC1 is the single most important body for IT standards at the international level |

### JTC1 Subcommittees (Key Selection)

| SC    | Title                                                | Key Standards / Topics                               |
|-------|------------------------------------------------------|------------------------------------------------------|
| SC 2  | Coded Character Sets                                 | ISO/IEC 10646 (Universal Character Set — aligned with Unicode) |
| SC 6  | Telecommunications and Information Exchange          | OSI model standards (historical)                     |
| SC 7  | Software and Systems Engineering                     | ISO/IEC 12207, 15288, 25010 (SQuaRE quality model)  |
| SC 17 | Cards and Security Devices for Personal Identification | Smart cards, biometric cards                       |
| SC 22 | Programming Languages, Their Environments and System Software | **C (9899)**, **C++ (14882)**, Fortran, Ada, POSIX (9945) |
| SC 25 | Interconnection of IT Equipment                      | Home networking, data center infrastructure          |
| SC 27 | Information Security, Cybersecurity and Privacy      | **ISO/IEC 27001**, 27002, 27005, 27017, 27018, 27701 |
| SC 29 | Coding of Audio, Picture, Multimedia and Hypermedia  | JPEG, MPEG, AV1 (via MPEG)                          |
| SC 32 | Data Management and Interchange                      | SQL (ISO/IEC 9075), metadata                         |
| SC 38 | Cloud Computing and Distributed Platforms            | ISO/IEC 17788, 17789, 19086                          |
| SC 39 | Sustainability, IT and Data Centres                  | Data center energy efficiency                        |
| SC 40 | IT Service Management and IT Governance              | ISO/IEC 20000, 38500                                 |
| SC 41 | Internet of Things and Digital Twin                  | IoT reference architecture                           |
| SC 42 | **Artificial Intelligence**                          | **ISO/IEC 42001** (AI Management System), 23894, 24028, 24029 |
| SC 43 | Brain-Computer Interface                             | Emerging — neurotechnology standardization           |

### Key ISO/IEC JTC1 Standards for Technology Professionals

| Standard Number    | Title                                                | Domain          | Status    |
|--------------------|------------------------------------------------------|-----------------|-----------|
| ISO/IEC 9899:2024  | C Programming Language (C23)                        | Languages       | Published |
| ISO/IEC 14882:2023 | C++ Programming Language (C++23)                    | Languages       | Published |
| ISO/IEC 9075:2023  | SQL (Database Language)                             | Database        | Published |
| ISO/IEC 9945       | POSIX (Portable Operating System Interface)         | OS Interface    | Active    |
| ISO/IEC 10646:2020 | Universal Coded Character Set (UCS)                 | Character sets  | Active    |
| ISO/IEC 27001:2022 | Information Security Management System (ISMS)       | Security        | Active    |
| ISO/IEC 27002:2022 | Information Security Controls                       | Security        | Active    |
| ISO/IEC 27701:2019 | Privacy Information Management System (PIMS)        | Privacy         | Active    |
| ISO/IEC 42001:2023 | Artificial Intelligence Management System (AIMS)    | AI              | Active    |
| ISO/IEC 23894:2023 | AI — Guidance on Risk Management                    | AI              | Active    |
| ISO/IEC 17788:2014 | Cloud Computing — Overview and Vocabulary           | Cloud           | Active    |
| ISO/IEC 15408      | Common Criteria for IT Security Evaluation          | Security        | Active    |
| ISO/IEC 19770-2    | IT Asset Management — SWID Tags                     | Asset Mgmt      | Active    |
| ISO/IEC 12207:2017 | Software Life Cycle Processes                       | SW Engineering  | Active    |
| ISO/IEC 15288:2023 | Systems Life Cycle Processes                        | Systems Eng.    | Active    |

---

## 2.5 ITU — International Telecommunication Union

### ITU

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | International Telecommunication Union                          |
| Abbreviation / Alias         | ITU                                                            |
| Entry Type                   | Body                                                          |
| Governance Tier              | L1 (UN specialized agency — also has L2 characteristics)       |
| Governing Organization       | United Nations specialized agency                              |
| Parent / Umbrella Body       | United Nations system                                          |
| Founded / Published          | 1865 (oldest international organization still active)          |
| Headquarters                 | Geneva, Switzerland                                            |
| Sectors                      | ITU-T (Telecom Standardization), ITU-R (Radiocommunication), ITU-D (Development) |
| Membership                   | 193 Member States + 900+ sector members (companies)            |
| Specification Availability   | Free — all ITU-T Recommendations freely available online       |
| Primary Domain               | Telecommunications — spectrum, signaling, numbering, QoS       |
| Industry Relevance           | Critical for all radio spectrum allocation and telecom         |
| Notes                        | ITU-R manages global radio spectrum allocation (treaty-binding) |

### ITU-T (Telecommunication Standardization Sector)

Produces "Recommendations" (equivalent to standards). Key series:

| Series | Domain                                          | Key Recommendations                              |
|--------|--------------------------------------------------|--------------------------------------------------|
| E      | Telephone network and ISDN                       | E.164 (international phone numbering)            |
| G      | Transmission systems, media, digital systems     | G.711 (PCM audio), G.729 (speech codec)          |
| H      | Audiovisual and multimedia systems               | H.264/AVC, H.265/HEVC, H.266/VVC                |
| T      | Terminals for telematic services                 | T.120 (multimedia conferencing)                  |
| V      | Data communication over telephone network        | V.90 (56K modem — legacy)                        |
| X      | Data networks, OSI, security                     | **X.509** (PKI certificates), X.500 (directory)  |
| Y      | Global information infrastructure, IoT, smart cities | Y.3172 (ML in future networks)              |

**X.509 — The Most Impactful ITU-T Standard:**

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Information Technology — Open Systems Interconnection — The Directory: Public-key and attribute certificate frameworks |
| Standard Number              | ITU-T X.509 (also ISO/IEC 9594-8)                             |
| Entry Type                   | Standard                                                       |
| Governance Tier              | L1 (ITU-T)                                                    |
| Current Version              | X.509 (2019) — Edition 9                                      |
| IETF Profile                 | RFC 5280 (Internet X.509 PKI Certificate and CRL Profile)     |
| Use Cases                    | TLS/HTTPS server certificates, code signing, email (S/MIME), VPN (IPsec) |
| Industry Impact              | Every HTTPS connection on the internet uses X.509 certificates |

### ITU-R (Radiocommunication Sector)

Manages global radio-frequency spectrum allocation through the **Radio Regulations** (a binding international treaty):

| Function                | Description                                                    |
|-------------------------|----------------------------------------------------------------|
| Spectrum allocation     | Divides radio spectrum among services (mobile, satellite, broadcasting, etc.) |
| WRC                     | World Radiocommunication Conference (every 3–4 years)         |
| Key outputs             | Radio Regulations (treaty), ITU-R Recommendations, Reports    |
| Impact                  | Every radio device (phone, Wi-Fi, satellite, radar) must conform to ITU-R allocations |

### ITU Study Groups (ITU-T)

| SG   | Title                                                    | Key Work                           |
|------|----------------------------------------------------------|------------------------------------|
| SG 2 | Operational Aspects                                      | Service quality, numbering         |
| SG 3 | Tariff and Accounting Principles                         | Interconnection charges            |
| SG 5 | Environment, Climate Change, and Circular Economy        | E-waste, energy efficiency         |
| SG 9 | Broadband Cable and TV                                   | Cable TV, IPTV                     |
| SG 11| Signalling Requirements, Protocols, Test Specs           | Signaling protocols                |
| SG 12| Performance, QoS and QoE                                 | MOS (Mean Opinion Score)           |
| SG 13| Future Networks, Cloud, and ICT Infrastructure           | IMT-2020 (5G) network arch         |
| SG 15| Transport, Access, and Home Networks                     | G.fast, G.hn, PON                  |
| SG 16| Multimedia Coding, Systems, and Applications             | H.264, H.265, H.266 codecs        |
| SG 17| Security                                                 | X.509, cybersecurity frameworks    |
| SG 20| IoT, Smart Cities, and Communities                       | IoT reference architecture         |

---

## 2.6 ANSI — American National Standards Institute

### ANSI

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | American National Standards Institute                          |
| Abbreviation / Alias         | ANSI                                                           |
| Entry Type                   | Body (US national standards body + accreditation body)        |
| Governance Tier              | L1 (as US ISO member body) / L2 (as US national accreditor)  |
| Governing Organization       | ANSI (private non-profit)                                      |
| Founded / Published          | 1918                                                          |
| Scope                        | USA — national standards coordination and international representation |
| Primary Role                 | (1) US member body to ISO; (2) Accredits US standards developers; (3) Coordinates US national positions |
| Does NOT write standards     | ANSI accredits other bodies (IEEE, ASTM, UL) to write standards. ANSI itself does not develop technical standards. |
| Key Accredited SDOs          | IEEE SA, ASTM, UL, ASHRAE, NEMA, ASME, API                    |
| ANSI Approval                | Standards developed by accredited SDOs can be submitted for ANSI approval → "American National Standard" (ANS) designation |
| International Role           | ISO member body (represents USA); IEC member (via USNC); ITU member |
| Notes                        | Common confusion: ANSI does NOT write standards. "ANSI C" really means ISO/IEC 9899 (C programming language) — ANSI's role was to submit the US national position to ISO. |

---

## 2.7 UNECE — United Nations Economic Commission for Europe

### UNECE WP.29 (Vehicle Regulations)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | United Nations Economic Commission for Europe — World Forum for Harmonization of Vehicle Regulations (WP.29) |
| Abbreviation / Alias         | UNECE WP.29                                                    |
| Entry Type                   | Body (intergovernmental forum producing binding regulations)   |
| Governance Tier              | L2 (intergovernmental — UN-linked; produces binding regulations for contracting parties) |
| Governing Organization       | UNECE (UN regional commission)                                 |
| Scope                        | Global — 58 contracting parties (Europe, Japan, Australia, Korea, etc.; NOT USA for most regulations) |
| Primary Domain               | Vehicle type approval regulations                              |
| Industry Relevance           | Critical for all automotive manufacturers selling globally     |
| Key Regulations              | UN R155 (Cybersecurity), UN R156 (Software Updates), UN R79 (Steering), UN R13 (Braking) |

**Critical UNECE Vehicle Regulations for Technology:**

| Regulation | Title                                                | Published | Impact                                        |
|------------|------------------------------------------------------|-----------|-----------------------------------------------|
| UN R155    | Cybersecurity and Cybersecurity Management System    | 2021      | Mandates CSMS for type approval; references ISO/SAE 21434 |
| UN R156    | Software Update and Software Update Management System| 2021      | Mandates SUMS for OTA updates                 |
| UN R157    | Automated Lane Keeping Systems (ALKS)                | 2021      | First binding regulation for SAE Level 3 AD   |
| UN R79     | Steering Equipment (including ADAS steering)         | Revised   | Governs steering-related ADAS functions       |

**Why UNECE matters for software engineers:**
- UN R155 makes cybersecurity a **legal requirement** for vehicle type approval in EU, Japan, Korea
- Applies from July 2022 for new types, July 2024 for all vehicles
- Requires demonstration of Cybersecurity Management System (CSMS) — typically using ISO/SAE 21434 as technical implementation
- Non-compliance = vehicle cannot be sold in contracting party markets

---

## 2.8 ISO Standardization Lifecycle — Complete Walkthrough

### Example: How ISO 26262 Was Created

```
Timeline: ISO 26262 — Road Vehicles — Functional Safety

1990s  │ Problem recognition: IEC 61508 exists but is not specific to automotive
       │ Automotive electronics growing rapidly — need domain-specific safety standard
       │
2005   │ ISO TC22/SC32/WG8 formed — begin drafting automotive functional safety standard
       │ Working Drafts (WD1, WD2, WD3...) — internal committee iteration
       │
2009   │ Committee Draft (CD) published — TC22 member ballot
       │ Comment resolution from national bodies
       │
2010   │ DIS (Draft International Standard) ballot — P-member vote
       │ Approved with comments
       │
2011   │ ISO 26262:2011 (Edition 1) published — 10 parts
       │ Immediately adopted by automotive industry globally
       │
2015   │ Systematic Review initiated — industry feedback collected
       │ Known gaps: motorcycles, semiconductors, trucks, software update
       │
2017   │ Revision WDs circulated — new Part 11 (semiconductor) + Part 12 (motorcycles)
       │
2018   │ ISO 26262:2018 (Edition 2) published — 12 parts
       │ Adds: motorcycles, semiconductors, updated ASIL decomposition guidance
       │
2024+  │ Edition 3 under development — addressing OTA, AI/ML, SOC
```

---

## 2.9 Cross-Body Relationships

### ISO / IEC / JTC1 Relationship

```
ISO (general standards)          IEC (electrotechnical standards)
        │                                    │
        └─────── ISO/IEC JTC1 ──────────────┘
                 (Information Technology)
                 
        
```

**Division of scope:**
- **ISO only**: Quality (9001), Automotive safety (26262), Environmental (14001)
- **IEC only**: Functional safety (61508), EMC (61000), Industrial automation (61131)
- **ISO/IEC JTC1**: Information technology (27001, 42001, C/C++ languages, SQL, POSIX)

### Vienna Agreement (ISO + CEN)

- **CEN** (European Committee for Standardization) has a parallel development agreement with ISO
- Under the Vienna Agreement, work can be led by either ISO or CEN with simultaneous publication
- A CEN standard can become an ISO standard and vice versa without duplicate work
- Example: EN ISO 13849 is simultaneously a CEN European Standard and an ISO International Standard

### Frankfurt Agreement (IEC + CENELEC)

- Same principle as Vienna Agreement but for IEC and CENELEC (European electrotechnical standardization)
- Prevents duplicate European and international standards in electrotechnical domain

---

## 2.10 Summary and Key Takeaways

### L1 Body Comparison Matrix

| Attribute          | ISO                 | IEC                  | ITU-T              | ITU-R             |
|--------------------|---------------------|----------------------|--------------------|-------------------|
| Founded            | 1947                | 1906                 | 1865 (as ITU)      | 1927 (as CCIR)    |
| Members            | 170 national bodies | 89 national committees| 193 member states  | 193 member states |
| Scope              | All (non-electrical)| Electrotechnical     | Telecom standards  | Radio spectrum    |
| Output name        | International Standard (IS)| International Standard | Recommendation | Recommendation  |
| Spec access        | Paid ($50–$300)     | Paid ($50–$500)      | **Free**           | Free              |
| Voting             | National body       | National committee   | Study Group consensus| WRC treaty vote  |
| Typical duration   | 3–7 years           | 3–5 years            | 2–4 years          | 4-year WRC cycle  |
| US representative  | ANSI                | USNC (via ANSI)      | US State Dept      | FCC/NTIA          |
| Certifiable?       | Yes (mgmt systems)  | Yes (CB scheme)      | No (advisory)      | Binding (treaty)  |
| Automotive impact  | Very High (26262)   | Very High (61508)    | Low                | Low (V2X spectrum)|
| Cybersecurity      | 27001, 27002        | 62443                | X.509, frameworks  | —                 |
| AI                 | 42001, 23894        | —                    | Y-series AI        | —                 |

### When to Use Each Body's Standards

| Scenario                                              | Go To                         |
|-------------------------------------------------------|-------------------------------|
| Need a management system certification (security, AI) | ISO (27001, 42001)            |
| Building safety-critical E/E/PE system               | IEC 61508 family              |
| Automotive functional safety                         | ISO 26262                     |
| Need PKI certificate format specification            | ITU-T X.509 + IETF RFC 5280  |
| Radio spectrum allocation question                   | ITU-R Radio Regulations       |
| Vehicle cybersecurity type approval                  | UNECE R155 + ISO/SAE 21434   |
| Programming language formal specification            | ISO/IEC JTC1/SC22             |
| Database query language specification                | ISO/IEC 9075 (SQL)            |

---

*End of Chapter 2 — Global International Standards Bodies (L1/L2)*
*Next: Chapter 3 — Internet & Web Standards Bodies*
