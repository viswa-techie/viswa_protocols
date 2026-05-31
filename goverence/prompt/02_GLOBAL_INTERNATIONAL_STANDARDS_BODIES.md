# CHAPTER 2 — GLOBAL INTERNATIONAL STANDARDS BODIES (L1 / L2)
# Prompt for: Universal Technology Standards Governance Encyclopedia
# Depends on: MASTER_PROMPT.md (load first), Chapter 1 (governance tier model)

---

## CHAPTER SCOPE

**Covers (L1 — International):**
- ISO (International Organization for Standardization)
- IEC (International Electrotechnical Commission)
- ISO/IEC JTC1 (Joint Technical Committee 1 — Information Technology)
- ITU-T (International Telecommunication Union — Telecommunication)
- ITU-R (ITU — Radiocommunication)
- ITU-D (ITU — Development)
- ICAO (International Civil Aviation Organization — standards relevant to avionics/embedded)
- ISO/IEC JTC2 (Security, IoT, AI overlap)

**Covers (L2 — Intergovernmental/Regulatory):**
- UN agencies with standards relevance: UNECE, UNCITRAL
- IMO (International Maritime Organization) — relevant to embedded/safety
- WTO TBT Agreement (how international standards support trade)
- The role of ANSI (American National Standards Institute) as US national body to ISO

**Defers to:**
- IETF, W3C, IEEE → Chapter 3 (Internet/Web) or Chapter 5 (Professional)
- NIST, FCC, EU regulatory bodies → Chapter 10 (Government/Regulatory)
- Domain standards (automotive, safety, etc.) → Chapters 11–17

---

## GENERATION INSTRUCTIONS FOR THIS CHAPTER

Act as a world-class standards architect specializing in international standards governance.
Generate the complete **Chapter 2: Global International Standards Bodies** encyclopedia entries.

### SECTION 2.1 — INTERNATIONAL STANDARDS BODIES OVERVIEW

Explain:
- Why international (treaty-based) standards bodies occupy the apex of the governance hierarchy
- The relationship between national standards bodies and international bodies
- What a "national body" is and how countries delegate to ISO/IEC/ITU
- The WTO TBT (Technical Barriers to Trade) Agreement and why ISO/IEC standards
  are the preferred basis for national regulations under WTO rules
- Why ISO/IEC standards frequently cost money to purchase (the "standards economy")
- The ISO/IEC Directives — the rules that govern how ISO/IEC standards are written

### SECTION 2.2 — ISO (INTERNATIONAL ORGANIZATION FOR STANDARDIZATION)

Full encyclopedia entry:

| Field                        | Value                                      |
|------------------------------|--------------------------------------------|
| Full Name                    | International Organization for Standardization |
| Abbreviation / Alias         | ISO (note: NOT an acronym — from Greek "isos" meaning equal) |
| Entry Type                   | Governing Body — International Standards Organization |
| Governance Tier              | L1                                         |
| Founded                      | 1947                                       |
| Headquarters                 | Geneva, Switzerland                        |
| Members                      | 169 national member bodies (as of 2024)    |
| Member categories            | Full member (P-member), Correspondent, Subscriber |
| Voting model                 | One national body = one vote; P-members vote, O-members observe |
| Governance structure         | ISO Council → Technical Management Board → Technical Committees |
| Standard numbering           | ISO NNNNN or ISO/IEC NNNNN (joint)         |
| Specification access         | Paid — purchased from ISO or national member body |
| Open / Proprietary           | Open process, paid publication             |
| IP/Patent policy             | ISO Patent Policy — FRAND licensing required for essential patents |
| Domains covered              | All — manufacturing, software, services, food, healthcare, and technology |
| Key technical committees     | ISO/TC 68 (Finance), ISO/TC 176 (Quality), ISO/TC 307 (Blockchain), etc. |

**Technical Committees relevant to technology:**
Produce a table of ISO Technical Committees (TCs) and Subcommittees (SCs) relevant to software,
networking, automotive, security, AI, and embedded systems:

| TC / SC         | Title                                              | Key Standards Output              |
|-----------------|----------------------------------------------------|-----------------------------------|
| ISO/IEC JTC1    | Information Technology (joint with IEC)            | C language, SQL, MPEG, cryptography|
| ISO/IEC JTC1/SC6| Telecommunications and information exchange        | OSI model, HDLC                   |
| ISO/IEC JTC1/SC7| Software and systems engineering                   | ISO 9001 applied to SW, SPICE     |
| ISO/IEC JTC1/SC22| Programming languages, their environments         | C, C++, Ada, Fortran, COBOL       |
| ISO/IEC JTC1/SC25| Interconnection of IT equipment                   | LAN standards                     |
| ISO/IEC JTC1/SC27| Information security, cybersecurity, privacy      | ISO 27001, ISO 27002              |
| ISO/IEC JTC1/SC29| Coding of audio, picture, multimedia               | JPEG, MPEG, MP3                   |
| ISO/IEC JTC1/SC38| Cloud computing and distributed platforms          | ISO 17788, ISO 17789              |
| ISO/IEC JTC1/SC40| IT service management and IT governance            | ISO 20000, ISO 38500              |
| ISO/IEC JTC1/SC42| Artificial intelligence                            | ISO/IEC 42001 (AI management)     |
| ISO/TC 22       | Road vehicles                                      | ISO 26262, ISO 21434              |
| ISO/TC 204      | Intelligent transport systems                      | ITS architecture standards        |

**ISO Standardization Lifecycle (ISO-specific):**
Produce a precise table showing ISO's own internal lifecycle with stage codes:

| Stage Code | Stage Name                        | Description                                        |
|------------|-----------------------------------|----------------------------------------------------|
| 00         | Preliminary Stage                 | Preliminary work item — not yet formally accepted  |
| 10         | Proposal Stage (NP)               | New Work Item Proposal — P-member ballot required  |
| 20         | Preparatory Stage (WD)            | Working Draft developed by working group           |
| 30         | Committee Stage (CD)              | Committee Draft — reviewed by TC/SC members        |
| 40         | Enquiry Stage (DIS/FDIS)          | Draft International Standard — 5-month ballot      |
| 50         | Approval Stage (FDIS)             | Final Draft — 2-month approval ballot              |
| 60         | Publication Stage                 | Standard published                                 |
| 90         | Review Stage                      | Systematic review every 5 years                   |
| 95         | Withdrawal Stage                  | Standard withdrawn                                 |

### SECTION 2.3 — IEC (INTERNATIONAL ELECTROTECHNICAL COMMISSION)

Full encyclopedia entry covering:
- Founded 1906 — oldest of the big-three international bodies
- Scope: electrotechnology — electrical, electronic, magnetic, electromagnetic, electroacoustic
- Governance structure: IEC Council Board → Standardization Management Board → Technical Committees
- Member structure: Full member, Associate member, Affiliate member
- Key IEC Technical Committees for technology:

| TC    | Title                                          | Key Standards                              |
|-------|------------------------------------------------|--------------------------------------------|
| TC 65  | Industrial-process measurement, control, automation | IEC 61511 (functional safety), IEC 62443 (industrial cybersecurity) |
| TC 57  | Power systems management                       | IEC 61850, IEC 61968, IEC 61970           |
| TC 77  | Electromagnetic compatibility (EMC)            | IEC 61000 series                          |
| TC 100 | Audio, video and multimedia systems            | IEC 62240 series                          |
| TC 108 | Safety of electronic equipment                 | IEC 62368-1                               |
| TC 56  | Dependability                                  | IEC 61508 (functional safety)             |
| TC 45  | Nuclear instrumentation                        | Safety-critical nuclear standards          |

- IEC's role as co-owner of ISO/IEC JTC1
- IEC conformity assessment: IECEE, IECEX, IECQ, IECRB systems
- IEC Electropedia (International Electrotechnical Vocabulary)

### SECTION 2.4 — ISO/IEC JTC1 (JOINT TECHNICAL COMMITTEE 1)

Full encyclopedia entry — this is the most important committee for software and IT standards:

| Field                        | Value                                      |
|------------------------------|--------------------------------------------|
| Full Name                    | ISO/IEC Joint Technical Committee 1 — Information Technology |
| Entry Type                   | Joint Standards Committee                  |
| Parent Bodies                | ISO + IEC                                  |
| Founded                      | 1987 (merger of ISO/TC 97 + IEC/TC 83)    |
| Scope                        | All information technology standardization |
| Structure                    | Subcommittees (SC) + Working Groups (WG)  |
| Secretariat                  | ANSI (USA)                                 |

Produce a complete table of all JTC1 subcommittees:

| SC     | Title                                          | Key Standards Output                          |
|--------|------------------------------------------------|-----------------------------------------------|
| SC 2   | Coded character sets                           | ISO/IEC 10646 (Unicode alignment)             |
| SC 6   | Telecommunications and information exchange   | OSI protocols, HDLC, X.25                    |
| SC 7   | Software and systems engineering               | ISO/IEC 25010 (software quality), SPICE       |
| SC 17  | Cards and security devices for personal identification | Smart card standards, ISO 7816        |
| SC 22  | Programming languages, their environments     | C (ISO/IEC 9899), C++ (ISO/IEC 14882), Fortran|
| SC 23  | Digitally recorded media for information interchange | optical/flash storage standards        |
| SC 25  | Interconnection of IT equipment               | Home network, LAN standards                   |
| SC 27  | Information security, cybersecurity, privacy   | ISO/IEC 27001, 27002, 27017, 27018           |
| SC 28  | Office equipment                               | Printer cartridge environmental standards      |
| SC 29  | Coding of audio, picture, multimedia          | JPEG (ISO/IEC 10918), MPEG (ISO/IEC 13818)   |
| SC 31  | Automatic identification and data capture      | Bar codes, RFID (ISO/IEC 18000 series)        |
| SC 32  | Data management and interchange               | SQL (ISO/IEC 9075), metadata standards        |
| SC 34  | Document description and processing languages | SGML, HTML conformance, ODF, OOXML           |
| SC 35  | User interfaces                               | Accessibility standards                        |
| SC 36  | Information technology for learning, education| e-Learning standards                           |
| SC 37  | Biometrics                                    | ISO/IEC 19794 series                          |
| SC 38  | Cloud computing and distributed platforms     | ISO/IEC 17788, 17789, 19086                   |
| SC 40  | IT service management and IT governance        | ISO/IEC 20000, 38500                          |
| SC 41  | Internet of Things and digital twin           | IoT architecture standards                     |
| SC 42  | Artificial intelligence                        | ISO/IEC 42001 (AI management system)          |
| SC 43  | Quantum computing                              | Emerging — post-quantum cryptography          |

### SECTION 2.5 — ITU (INTERNATIONAL TELECOMMUNICATION UNION)

Full entry covering all three sectors:

**ITU-T (Telecommunication Standardization Sector)**
- Produces "Recommendations" (not "standards") — but treated as international standards
- Study Groups (SG) — permanent groups organized by topic
- SG numbering and major output:

| SG    | Focus Area                              | Key Recommendations                      |
|-------|-----------------------------------------|------------------------------------------|
| SG 2  | Operational aspects of service provision| E.164 (telephone numbering)             |
| SG 11 | Signalling requirements, protocols      | SS7, ISDN, SIP-related                  |
| SG 13 | Future networks including cloud         | Y-series (IMT, SDN, NFV)                |
| SG 15 | Transport, access and home              | G-series (DSL, OTN, DWDM)               |
| SG 16 | Multimedia                              | H.261, H.264 (video), G.711 (audio)     |
| SG 17 | Security                                | X.509, X.800, X.1500 series             |
| SG 20 | IoT and smart cities                    | Y.4000 series                           |

**ITU-R (Radiocommunication Sector)**
- Manages the radio frequency spectrum — global resource
- Radio Regulations: legally binding treaty document
- World Radiocommunication Conferences (WRC) — revision mechanism

**ITU-D (Development Sector)**
- Telecommunications development in developing countries
- Less relevant to technical standards

**Key ITU-T Standards for technology encyclopedia:**
- X.509 — PKI certificate standard (used in TLS/SSL everywhere)
- X.25 — Packet switching (superseded but historically important)
- G.711 / G.722 — Audio codecs (telephony/VoIP)
- H.264 (AVC) — Video codec (joint with ISO/IEC, also MPEG-4 Part 10)
- H.265 (HEVC) — Video codec (joint with ISO/IEC)
- E.164 — International telephone numbering plan
- T.120 — Data conferencing (foundation of early collaborative tools)

### SECTION 2.6 — ANSI (AMERICAN NATIONAL STANDARDS INSTITUTE)

| Field                        | Value                                      |
|------------------------------|--------------------------------------------|
| Full Name                    | American National Standards Institute     |
| Role                         | US national member body to ISO and IEC    |
| Entry Type                   | National Standards Body + Accrediting Body|
| Governance Tier              | L1 (proxy) / L4 (national level)          |
| Key function                 | Accredits US standards developers (IEEE, ASTM, SAE, etc.) as ANSI-accredited bodies |
| ANSI Standard designation    | "ANSI/[body] [number]" e.g., ANSI/IEEE 802.3 |

### SECTION 2.7 — L2 INTERGOVERNMENTAL BODIES (TECHNOLOGY-RELEVANT)

For each body, produce a concise entry (same entry template, abbreviated):

**UNECE (United Nations Economic Commission for Europe)**
- WP.29: World Forum for Harmonization of Vehicle Regulations
- Key regulations: UN Regulation No. 155 (vehicle cybersecurity), No. 156 (software updates)
- Mandatory in EU, Japan, Korea — affects all automotive OEMs
- Relationship to ISO/SAE automotive standards

**UNCITRAL (UN Commission on International Trade Law)**
- Model laws for e-commerce, electronic signatures, digital trade
- Less hardware-relevant but critical for digital commerce and API contract law

**IMO (International Maritime Organization)**
- SOLAS (Safety of Life at Sea) — safety-critical maritime systems
- Maritime cybersecurity guidelines (MSC-FAL.1/Circ.3)
- Relevance to embedded navigation and communication systems

### SECTION 2.8 — STANDARDS BODY COMPARISON TABLE (L1 vs L2)

Produce a master comparison table for all bodies covered in this chapter:

| Body         | Type    | Tier | Founded | HQ       | Members | Spec Access | IP Policy | Enforcement | Key Domain      |
|--------------|---------|------|---------|----------|---------|-------------|-----------|-------------|-----------------|
| ISO          | Treaty  | L1   | 1947    | Geneva   | 169     | Paid        | FRAND     | WTO/trade   | All             |
| IEC          | Treaty  | L1   | 1906    | Geneva   | 90+     | Paid        | FRAND     | Market      | Electrotechnology|
| ISO/IEC JTC1 | Joint TC| L1   | 1987    | Geneva   | —       | Paid        | FRAND     | Market      | IT              |
| ITU-T        | UN Agency| L2  | 1865    | Geneva   | 193     | Free/Paid   | FRAND     | Treaty      | Telecom         |
| ITU-R        | UN Agency| L2  | 1906    | Geneva   | 193     | Free/Paid   | Treaty    | Treaty      | Radio spectrum  |
| ANSI         | National| L1   | 1918    | New York | 125k+   | Paid        | Varies    | Market      | US standards    |
| UNECE WP.29  | UN/Gov  | L2   | 1958    | Geneva   | 56+     | Free        | N/A       | Regulatory  | Automotive      |
| IMO          | UN Agency| L2  | 1948    | London   | 175     | Paid        | N/A       | Treaty      | Maritime        |

### SECTION 2.9 — KEY ISO STANDARDS TECHNOLOGY REFERENCE TABLE

Produce a reference table of the most important ISO/IEC standards for software and hardware engineers:

| Standard             | Full Title                                       | Domain             | Status    |
|----------------------|--------------------------------------------------|--------------------|-----------|
| ISO/IEC 9899:2018    | C Programming Language (C17)                    | Programming Language| Current  |
| ISO/IEC 14882:2020   | C++ Programming Language (C++20)                | Programming Language| Current  |
| ISO/IEC 9075-1:2023  | SQL — Framework                                  | Database            | Current  |
| ISO/IEC 27001:2022   | Information Security Management Systems         | Cybersecurity       | Current  |
| ISO/IEC 27002:2022   | Information Security Controls                    | Cybersecurity       | Current  |
| ISO/IEC 27017:2015   | Cloud Security                                   | Cloud Security      | Current  |
| ISO/IEC 25010:2023   | Systems and Software Quality Requirements        | SW Quality          | Current  |
| ISO/IEC 42001:2023   | AI Management System                             | Artificial Intelligence| Current|
| ISO/IEC 17788:2014   | Cloud Computing Overview and Vocabulary          | Cloud               | Current  |
| ISO 26262:2018       | Road Vehicles — Functional Safety                | Automotive Safety   | Current  |
| ISO/SAE 21434:2021   | Road Vehicles — Cybersecurity Engineering        | Automotive Security | Current  |
| ISO/IEC 10646:2020   | Universal Coded Character Set (UCS)              | Character encoding  | Current  |
| ISO/IEC 19794        | Biometric data interchange formats               | Biometrics          | Current  |
| IEC 61508:2010       | Functional Safety of E/E/PE Systems              | Functional Safety   | Current  |
| IEC 62443            | Industrial Cybersecurity (IACS)                  | OT/ICS Security     | Current  |
| ITU-T X.509          | The Directory: Public-key and attribute cert.    | PKI/TLS             | Current  |

### SECTION 2.10 — CHAPTER SUMMARY AND CROSSLINKS

- Summary: Why international bodies (L1/L2) are the authority apex
- Key crosslinks:
  - ISO/IEC JTC1/SC22 → Chapter 7 (C, C++ vendor implementations)
  - ISO/IEC JTC1/SC27 → Chapter 13 (Cybersecurity domain)
  - ISO 26262, IEC 61508 → Chapter 17 (Safety-Critical domain)
  - ITU-T X.509 → Chapter 13 (TLS/PKI)
  - UNECE WP.29 → Chapter 11 (Automotive domain)
  - ISO/IEC JTC1/SC38 → Chapter 14 (Cloud domain)

---

## OUTPUT FORMAT REQUIREMENTS

- Use all tables from the entry template — no missing fields
- Every TC/SC must include at least one real standard number (not generic)
- Minimum length: 4,000 words
- No placeholder text — all cells must be filled with real data
