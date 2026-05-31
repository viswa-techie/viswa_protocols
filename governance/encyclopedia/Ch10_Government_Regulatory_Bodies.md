# Chapter 10 — Government & Regulatory Bodies (L2)

## Universal Technology Standards Governance & Regulatory Architecture Encyclopedia
### First Edition, 2024–2026

---

## 10.1 Regulatory Governance Model (L2)

### The Regulatory Spectrum: Key Definitions

| Term             | Binding?         | Issued By                          | Example                                    |
|------------------|------------------|------------------------------------|--------------------------------------------|
| **Regulation**   | Legally binding  | Government/Parliament              | GDPR (EU 2016/679)                        |
| **Directive**    | Binding (transposed to national law) | EU Parliament/Council | NIS2 (EU 2022/2555)                |
| **Standard**     | Voluntary        | Standards body (ISO, IEC, IEEE)    | ISO/IEC 27001                              |
| **Mandate**      | Binding          | Regulation referencing a standard  | UNECE R155 mandating ISO 21434-compatible CSMS |
| **Guideline**    | Non-binding (recommended) | Regulatory agency          | NIST SP 800-53 (for non-federal entities)  |
| **Certification**| Attestation      | Third-party conformity body        | ISO 27001 certificate from BSI/TÜV        |
| **Framework**    | Non-binding (voluntary) | Government agency            | NIST CSF 2.0                               |

### How Regulatory Mandates Link to Voluntary Standards

The fundamental mechanism by which governments leverage voluntary standards:

```
Parliament enacts REGULATION (legally binding)
    ↓
Regulation references "appropriate technical measures" or "state of the art"
    ↓
Standards body publishes STANDARD meeting regulatory intent
    ↓
Standard becomes "harmonized" or "recognized" under the regulation
    ↓
Organization implements the standard → PRESUMPTION OF CONFORMITY with the regulation
```

**Real-world examples of this mechanism:**

| Regulation              | Regulatory Text                              | Linked Standard                 | Relationship          |
|-------------------------|----------------------------------------------|---------------------------------|-----------------------|
| GDPR Art. 32            | "appropriate technical and organisational measures" | ISO 27001, ISO 27701   | Referenced; not mandatory |
| FDA 510(k)              | Software lifecycle requirements              | IEC 62304                       | Explicitly mandated   |
| FAA AC 20-115D          | Airborne software assurance                  | DO-178C (RTCA)                  | Mandatory compliance  |
| UNECE WP.29 Reg 155     | Cybersecurity management system             | ISO/SAE 21434                   | Explicitly referenced |
| EU CRA                  | Cybersecurity for digital products          | EN 303 645, IEC 62443          | Harmonized standard   |
| EU RED Art. 3.3(d)(e)(f)| Cybersecurity for wireless equipment        | EN 303 645 (ETSI)              | Harmonized standard   |

### The EU "Harmonized Standard" Concept

In the EU regulatory framework, a **harmonized standard** has special legal significance:

```
EU Regulation published in Official Journal
    ↓
European Commission requests standardization to CEN/CENELEC/ETSI
    ↓
Standard developed and published (EN designation)
    ↓
EU Commission publishes reference in Official Journal → HARMONIZED
    ↓
Manufacturer complies with harmonized standard → Presumption of conformity
    (no need to prove compliance through other means)
```

**Key EU harmonized standards bodies:**

| Body        | Domain                              | Types of Standards                |
|-------------|-------------------------------------|-----------------------------------|
| CEN         | General (non-electrical)            | EN standards (machinery, construction, etc.) |
| CENELEC     | Electrotechnical                    | EN IEC standards (electronics, EMC) |
| ETSI        | Telecommunications + ICT            | EN (telecom), TS (technical specifications) |

### US "NIST Framework" vs. EU "Harmonized Standard" Approach

| Aspect                | US Approach                        | EU Approach                          |
|-----------------------|------------------------------------|--------------------------------------|
| Primary mechanism     | NIST publishes frameworks/guidelines | EU Commission mandates harmonized standards |
| Legal binding         | Binding for federal; guidance for private sector | Binding for all entities in scope |
| Compliance demonstration | Self-attestation or third-party assessment (varies) | CE marking + declaration of conformity |
| Standard integration  | NIST SP referenced in contracts/procurement | EN standards provide presumption of conformity |
| Enforcement           | Agency-specific (OCR, FTC, SEC)    | National market surveillance authorities |
| Penalty philosophy    | Per-incident fines; criminal liability | Revenue-percentage fines (GDPR model) |

---

## 10.2 NIST (National Institute of Standards and Technology)

### Full Entry

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | National Institute of Standards and Technology                 |
| Abbreviation                 | NIST                                                           |
| Entry Type                   | US Federal Agency + Standards Development Body                |
| Parent Agency                | US Department of Commerce                                      |
| Governance Tier              | L2 (government — NIST publications are normative for US federal procurement) |
| Founded                      | 1901 (as NBS — National Bureau of Standards); renamed NIST 1988 |
| Legal Basis                  | NIST Act (15 U.S.C. § 271–278); FISMA (gives NIST standards-making role for federal IT security) |
| Publication Series           | FIPS, SP 800, SP 1800, NISTIR, SP 500                        |
| Specification Access         | **Free** — all publications at csrc.nist.gov / nvlpubs.nist.gov |
| International Influence      | Very High — NIST frameworks adopted globally (CSF used in Japan, Israel, Italy, etc.) |
| Process                      | Public comment periods, workshops, multi-year development      |
| Relationship to ISO/IEC      | NIST participates in JTC1 SC27 (security); some NIST work feeds into ISO standards |

### NIST Publication Series

| Series    | Full Name                              | Authority Level                                  | Example             |
|-----------|----------------------------------------|--------------------------------------------------|---------------------|
| FIPS      | Federal Information Processing Standards| **Mandatory** for US federal agencies (Title 15 + OMB Circular A-130) | FIPS 140-3, FIPS 197 |
| SP 800    | Special Publications — Computer Security| Guidance; **recommended**; often referenced in federal contracts | SP 800-53, SP 800-171 |
| SP 1800   | NIST Cybersecurity Practice Guides     | Practical implementation guides (how-to)         | SP 1800-25 (data integrity) |
| SP 500    | Computer Science & IT Standards        | IT management, software testing                   | SP 500-291 (cloud computing) |
| NISTIR    | NIST Interagency/Internal Reports      | Research and technical background                 | NISTIR 8259 (IoT core baseline) |

### Critical NIST Standards for Technology Engineers

#### Cryptography (FIPS)

| Document    | Title                                              | Domain                    | Status (2024)  |
|-------------|-----------------------------------------------------|---------------------------|----------------|
| FIPS 140-3  | Security Requirements for Cryptographic Modules    | Crypto module validation  | Current (2019) |
| FIPS 197    | Advanced Encryption Standard (AES)                  | Symmetric encryption      | Current (2001) |
| FIPS 186-5  | Digital Signature Standard (ECDSA, EdDSA)          | Digital signatures        | Current (2023) |
| FIPS 180-4  | Secure Hash Standard (SHA-2: SHA-256, SHA-512)     | Hash functions            | Current (2015) |
| FIPS 202    | SHA-3 Standard (Keccak)                            | Hash functions            | Current (2015) |
| FIPS 203    | ML-KEM (Kyber — Post-Quantum Key Encapsulation)   | Post-quantum crypto       | **NEW (2024)** |
| FIPS 204    | ML-DSA (Dilithium — Post-Quantum Digital Signature)| Post-quantum crypto       | **NEW (2024)** |
| FIPS 205    | SLH-DSA (SPHINCS+ — Stateless Hash Signatures)    | Post-quantum crypto       | **NEW (2024)** |

#### Post-Quantum Cryptography (PQC) Standardization — 2024

NIST completed the most significant cryptographic standardization since AES (2001):

| New Standard | Algorithm Family   | Based On         | Purpose                     | Security Basis        |
|--------------|--------------------|--------------------|-----------------------------|-----------------------|
| FIPS 203     | ML-KEM             | CRYSTALS-Kyber    | Key Encapsulation Mechanism | Module Learning with Errors (MLWE) |
| FIPS 204     | ML-DSA             | CRYSTALS-Dilithium| Digital Signature           | Module Learning with Errors (MLWE) |
| FIPS 205     | SLH-DSA            | SPHINCS+          | Digital Signature           | Hash functions (conservative) |
| FIPS 206 (draft) | FN-DSA         | FALCON            | Digital Signature           | NTRU lattices         |

**Threat model:** "Harvest Now, Decrypt Later" — adversaries record encrypted traffic today; break RSA/ECC with future quantum computers.

**Migration timeline:**
- 2024: Standards published
- 2025–2030: Transition period (hybrid classical + PQC)
- 2035: NIST recommends deprecation of RSA-2048, ECC P-256 for long-lived secrets

#### Security Frameworks (SP 800 Series)

| Document        | Title                                              | Impact                          |
|-----------------|----------------------------------------------------|---------------------------------|
| SP 800-53 Rev. 5| Security and Privacy Controls for Information Systems | **Foundational** — basis for FedRAMP, CMMC |
| SP 800-171 Rev. 3| Protecting CUI in Non-Federal Systems            | Defense supply chain (CMMC)     |
| SP 800-63B      | Digital Identity Guidelines — Authentication       | Password/MFA requirements       |
| SP 800-190      | Application Container Security Guide               | Container security              |
| SP 800-207      | Zero Trust Architecture                            | Network architecture paradigm   |
| SP 800-218      | Secure Software Development Framework (SSDF)       | Secure SDLC practices           |

#### NIST Cybersecurity Framework (CSF) 2.0

| Field                    | Value                                                    |
|--------------------------|----------------------------------------------------------|
| Version                  | CSF 2.0 (February 2024 — major update from 1.1)        |
| Structure                | 6 Functions → Categories → Subcategories                 |
| New in 2.0               | GOVERN function added (previously 5 functions)           |
| Applicability            | All organizations, all sectors (expanded from critical infrastructure) |

| Function  | Purpose                                    | Key Categories              |
|-----------|--------------------------------------------|-----------------------------|
| GOVERN    | Establish cybersecurity governance strategy| Risk strategy, supply chain |
| IDENTIFY  | Understand organizational context          | Asset management, risk assessment |
| PROTECT   | Implement safeguards                       | Access control, awareness training |
| DETECT    | Identify cybersecurity events              | Continuous monitoring, anomaly detection |
| RESPOND   | Act on detected incidents                  | Response planning, communications |
| RECOVER   | Restore capabilities                       | Recovery planning, improvements |

---

## 10.3 FCC (Federal Communications Commission)

### Full Entry

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Federal Communications Commission                              |
| Abbreviation                 | FCC                                                            |
| Entry Type                   | US Independent Regulatory Agency                              |
| Governance Tier              | L2 (US federal regulation)                                    |
| Founded                      | 1934 (Communications Act)                                     |
| Jurisdiction                 | United States radio frequency spectrum, interstate/international communications |
| Key Technical Regulations    | FCC Part 15, Part 22, Part 24, Part 27, Part 30, Part 96     |
| Hardware Relevance           | **Any wireless device sold in USA must have FCC authorization** |
| Certification Mark           | FCC ID (required on all authorized RF devices)                |
| Testing Standard             | OET Bulletin 65 (RF exposure), ANSI C63.4 (EMC measurements) |
| EU Equivalent                | CE marking (Radio Equipment Directive)                        |

### FCC Part 15 — Unlicensed Devices

| Subpart    | Frequency / Application                          | Key Technologies           |
|------------|--------------------------------------------------|----------------------------|
| Part 15.247| 2.4 GHz ISM band spread spectrum                 | Wi-Fi (802.11b/g/n), Bluetooth, ZigBee |
| Part 15.407| 5 GHz UNII bands                                 | Wi-Fi (802.11a/ac/ax)     |
| Part 15 Subpart E | Ultra-Wideband (UWB)                     | UWB positioning, Apple AirTag |
| Part 15 Subpart F | 60 GHz unlicensed                       | WiGig (802.11ad/ay)       |
| Part 15 Subpart G | 6 GHz (new in 2020)                    | Wi-Fi 6E/7 (802.11ax/be)  |

### FCC vs. CE vs. Other Regional Marks

| Region     | Mark / Authority          | Frequency Plan          | Notes                    |
|------------|---------------------------|-------------------------|--------------------------|
| USA        | FCC ID                    | FCC Table of Allocations| Required for US sale     |
| EU/EEA     | CE (Radio Equipment Dir.) | CEPT/ECC frequency plan | Required for EU sale     |
| UK         | UKCA                      | Ofcom UK Plan           | Post-Brexit (CE accepted until 2025) |
| Japan      | MIC Technical Conformity  | ARIB standards          | Telec certification      |
| China      | SRRC (State Radio)        | MIIT frequency plan     | Type approval required   |

---

## 10.4 ETSI (European Telecommunications Standards Institute)

### Full Entry

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | European Telecommunications Standards Institute                |
| Abbreviation                 | ETSI                                                           |
| Entry Type                   | Regional Standards Body (officially recognized by EU)          |
| Governance Tier              | L2/L3 (produces EU harmonized standards; operates as membership organization) |
| Founded                      | 1988                                                          |
| Headquarters                 | Sophia Antipolis, France                                      |
| Members                      | 900+ members from 65 countries (industry, research, national administrations) |
| Key Function                 | Produces European Standards (EN) that enable CE marking        |
| Relationship to 3GPP         | ETSI is one of 7 organizational partners of 3GPP (cellular standards) |
| Relationship to EU           | One of three European Standards Organizations (ESO) — alongside CEN and CENELEC |
| Specification Access         | **Free** — all ETSI standards downloadable from etsi.org      |

### Key ETSI Standards

| Standard         | Title                                          | Domain               | Significance                   |
|------------------|------------------------------------------------|----------------------|--------------------------------|
| EN 303 645       | Cyber Security for Consumer IoT               | IoT security         | Harmonized under EU RED; first IoT cybersecurity standard |
| EN 319 401       | General Policy Requirements for TSP            | PKI/Digital signatures| eIDAS Trust Service Providers  |
| TS 103 645       | Consumer IoT Security (technical spec version)| IoT                  | Basis for EN 303 645           |
| EN 300 328       | Wideband data transmission (2.4 GHz)          | Wi-Fi/BT compliance  | CE marking for 2.4GHz devices  |
| EN 301 489-x     | EMC for radio equipment                       | EMC                  | Series covering different services |
| EN 303 687       | Smart Secure Platform (IoT)                   | IoT security         | Secure element requirements    |

### EN 303 645 — IoT Security Baseline (13 Provisions)

| Provision | Requirement                                              |
|-----------|----------------------------------------------------------|
| 1         | No universal default passwords                           |
| 2         | Implement a means to manage reports of vulnerabilities   |
| 3         | Keep software updated                                    |
| 4         | Securely store sensitive security parameters             |
| 5         | Communicate securely                                     |
| 6         | Minimize exposed attack surfaces                         |
| 7         | Ensure software integrity                                |
| 8         | Ensure personal data is secure                           |
| 9         | Make systems resilient to outages                        |
| 10        | Examine system telemetry data                            |
| 11        | Make it easy for users to delete user data               |
| 12        | Make installation and maintenance easy                   |
| 13        | Validate input data                                      |

---

## 10.5 EU Technology Regulations (Major Entries)

### GDPR (General Data Protection Regulation)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | General Data Protection Regulation                             |
| Regulation Number            | EU 2016/679                                                   |
| Entry Type                   | EU Regulation (directly binding in all EU/EEA member states)  |
| Governance Tier              | L2 (EU law — highest regulatory authority)                    |
| Adopted                      | April 27, 2016                                                |
| Effective                    | May 25, 2018                                                  |
| Enforcement                  | National Data Protection Authorities (DPAs) + European Data Protection Board (EDPB) |
| Territorial Scope            | Any organization processing EU residents' data (regardless of location) |
| Max Fine                     | €20 million or 4% of global annual turnover (whichever is higher) |
| Adequacy Decisions           | UK, Japan, South Korea, Switzerland, Israel, New Zealand, Canada (commercial), others |
| Replaces                     | Data Protection Directive 95/46/EC                            |

**Key Articles for Technology Engineers:**

| Article     | Title                          | Engineering Impact                          |
|-------------|--------------------------------|---------------------------------------------|
| Art. 5      | Principles of processing       | Data minimization, purpose limitation       |
| Art. 25     | Data protection by design/default | Privacy architecture requirements        |
| Art. 30     | Records of processing          | Data flow documentation                     |
| Art. 32     | Security of processing         | "Appropriate technical measures" — encryption, pseudonymization |
| Art. 33/34  | Breach notification            | 72-hour notification to DPA                 |
| Art. 35     | Data Protection Impact Assessment (DPIA) | Required for high-risk processing  |
| Art. 44-49  | International transfers        | SCCs, BCRs, adequacy decisions              |
| Art. 83     | Administrative fines           | Penalty calculation criteria                |

### NIS2 Directive

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Directive on measures for a high common level of cybersecurity across the Union |
| Directive Number             | EU 2022/2555                                                  |
| Entry Type                   | EU Directive (must be transposed to national law by each member state) |
| Governance Tier              | L2                                                            |
| Adopted                      | December 14, 2022                                             |
| National Transposition Deadline | October 17, 2024                                          |
| Scope                        | Essential Entities (large, critical sectors) + Important Entities (medium, wider sectors) |
| Sectors Covered              | 18 sectors: energy, transport, banking, healthcare, water, digital infrastructure, ICT management, space, postal, waste, food, manufacturing, chemicals, research |
| Replaces                     | NIS1 Directive (EU 2016/1148)                                |
| Max Penalty (Essential)      | €10 million or 2% of global annual turnover                  |
| Max Penalty (Important)      | €7 million or 1.4% of global annual turnover                 |

**Key Requirements:**

| Requirement Area          | Details                                                      |
|---------------------------|--------------------------------------------------------------|
| Risk management measures  | Art. 21: policies on risk analysis, incident handling, supply chain, encryption, access control, MFA |
| Incident reporting        | 24h early warning → 72h incident notification → 1 month final report |
| Supply chain security     | Must assess and manage supply chain/supplier risks           |
| Management accountability | Management bodies must approve and oversee cybersecurity measures |
| CISO responsibility       | Named responsible officer; personal liability for management |

### EU Cyber Resilience Act (CRA)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Regulation on horizontal cybersecurity requirements for products with digital elements |
| Regulation Number            | EU 2024/2847                                                  |
| Entry Type                   | EU Regulation (directly binding)                              |
| Governance Tier              | L2                                                            |
| Adopted                      | October 2024                                                  |
| Application Timeline         | 36 months phased: reporting obligations (21 months), full application (36 months from entry into force) |
| Scope                        | **ALL products with digital elements** placed on EU market (hardware + software) |
| Key Innovation               | First regulation requiring CE marking for software/firmware cybersecurity |
| Exclusions                   | Medical devices (MDR), aviation (EASA), automotive (UNECE), open source (non-commercial stewards) |

**Key CRA Requirements:**

| Requirement                    | Description                                                  |
|--------------------------------|--------------------------------------------------------------|
| Security by design             | Products must be designed with cybersecurity from inception  |
| Default secure configuration   | Products shipped secure out-of-box                           |
| Vulnerability handling         | Manufacturer must handle vulnerabilities for support period (min 5 years) |
| SBOM                           | Software Bill of Materials must be generated and maintained  |
| Incident reporting             | Actively exploited vulnerabilities reported to ENISA within 24h |
| CE marking                     | Products must carry CE marking indicating CRA conformity     |
| Harmonized standards           | Compliance with EN 303 645 or IEC 62443 provides presumption of conformity |
| Free security updates          | Security patches must be provided free of charge             |

### EU AI Act

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Regulation laying down harmonised rules on artificial intelligence |
| Regulation Number            | EU 2024/1689                                                  |
| Entry Type                   | EU Regulation (directly binding)                              |
| Governance Tier              | L2                                                            |
| Adopted                      | June 2024; entered into force August 2024                    |
| Full Application             | August 2026 (phased — prohibited practices from Feb 2025)    |
| Scope                        | AI systems placed on EU market or affecting EU persons        |

**Risk-based Classification:**

| Risk Category      | Examples                                              | Requirements                      |
|--------------------|-------------------------------------------------------|-----------------------------------|
| Unacceptable (banned) | Social scoring, real-time biometric mass surveillance, manipulative AI | Prohibited                    |
| High Risk          | Medical devices, biometric ID, critical infrastructure, autonomous vehicles, recruitment AI | Conformity assessment, risk management, human oversight |
| Limited Risk       | Chatbots, deepfake generation, emotion recognition   | Transparency obligations          |
| Minimal Risk       | Spam filters, AI-enabled video games                  | No requirements (voluntary codes) |

**General Purpose AI (GPAI) Models:**

| GPAI Category      | Trigger                                         | Obligations                            |
|--------------------|-------------------------------------------------|----------------------------------------|
| GPAI (standard)    | Any general-purpose model (GPT-4, Gemini, etc.)| Technical documentation, copyright compliance, downstream provider information |
| GPAI (systemic risk) | Models trained with >10^25 FLOPs of compute  | Above + model evaluation, adversarial testing, incident reporting, energy consumption reporting |

---

## 10.6 USA Sector-Specific Regulations

### HIPAA (Healthcare)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Health Insurance Portability and Accountability Act            |
| Entry Type                   | US Federal Law (Title II — Administrative Simplification)     |
| Enacted                      | 1996; Privacy Rule 2003; Security Rule 2003; Breach Rule 2009|
| Enforcer                     | HHS Office for Civil Rights (OCR)                             |
| Governance Tier              | L2 (US federal law)                                           |
| Scope                        | Covered entities (providers, plans, clearinghouses) + Business Associates |
| PHI Definition               | Protected Health Information — any of 18 identifiers linked to health data |
| Max Penalty                  | Up to $1.9M per violation category per year; criminal penalties possible |

**HIPAA Security Rule — Technical Safeguards:**

| Safeguard              | Standard                    | Required/Addressable |
|------------------------|-----------------------------|---------------------|
| Access Control         | Unique user ID              | Required            |
| Access Control         | Emergency access procedure  | Required            |
| Access Control         | Automatic logoff            | Addressable         |
| Access Control         | Encryption (at rest)        | Addressable         |
| Audit Controls         | Audit logging               | Required            |
| Integrity              | Authentication of ePHI      | Addressable         |
| Transmission Security  | Integrity controls          | Addressable         |
| Transmission Security  | Encryption (in transit)     | Addressable         |

### FISMA / FedRAMP (Federal Cloud)

| Framework    | Full Name                                        | Authority          | Scope                    |
|--------------|--------------------------------------------------|--------------------|--------------------------|
| FISMA 2014   | Federal Information Security Modernization Act   | OMB / CISA         | All US federal agency IT systems |
| FedRAMP      | Federal Risk and Authorization Management Program| FedRAMP PMO (GSA)  | Cloud Service Providers serving federal agencies |
| CMMC 2.0     | Cybersecurity Maturity Model Certification       | DoD               | Defense Industrial Base (DIB) contractors |

**FedRAMP Authorization Levels:**

| Level    | Impact               | Controls (from SP 800-53) | Use Case                         |
|----------|----------------------|---------------------------|----------------------------------|
| Low      | Low confidentiality  | ~125 controls             | Public information               |
| Moderate | Moderate CIA         | ~325 controls             | Most government SaaS (80% of authorizations) |
| High     | High CIA             | ~420 controls             | Law enforcement, financial, health |

### PCI DSS (Payment Card Industry Data Security Standard)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Payment Card Industry Data Security Standard                  |
| Governance Body              | PCI Security Standards Council (PCI SSC)                      |
| Governance Tier              | L3 (consortium: Visa, Mastercard, Amex, Discover, JCB) — contractually mandatory |
| Current Version              | PCI DSS v4.0.1 (June 2024)                                   |
| Effective                    | March 2024 (v4.0); future-dated requirements March 2025      |
| Scope                        | All entities that store, process, or transmit cardholder data |
| Validation                   | SAQ (Self-Assessment) for small merchants; ROC (Report on Compliance) for large merchants/processors |
| QSA                          | Qualified Security Assessor (third-party auditor)             |

**PCI DSS v4.0 — 12 Requirements:**

| # | Requirement                                                  | Goal                    |
|---|--------------------------------------------------------------|-------------------------|
| 1 | Install and maintain network security controls               | Network Security        |
| 2 | Apply secure configurations to all system components         | Network Security        |
| 3 | Protect stored account data                                  | Protect Cardholder Data |
| 4 | Protect cardholder data with strong cryptography in transit  | Protect Cardholder Data |
| 5 | Protect all systems from malware                             | Vulnerability Mgmt      |
| 6 | Develop and maintain secure systems and software             | Vulnerability Mgmt      |
| 7 | Restrict access by business need to know                     | Access Control          |
| 8 | Identify users and authenticate access                       | Access Control          |
| 9 | Restrict physical access to cardholder data                  | Access Control          |
| 10| Log and monitor all access to system components and cardholder data | Monitoring        |
| 11| Test security of systems and networks regularly              | Regular Testing         |
| 12| Support information security with organizational policies    | Security Policy         |

### NERC CIP (Energy Sector)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | North American Electric Reliability Corporation — Critical Infrastructure Protection |
| Entry Type                   | Mandatory reliability standards for bulk electric system       |
| Enforcer                     | FERC (Federal Energy Regulatory Commission)                   |
| Governance Tier              | L2 (mandatory for all operators of bulk electric system)      |
| Scope                        | Generation, transmission, distribution utilities (North America) |
| Max Penalty                  | Up to $1 million per violation per day                        |
| Key Standards                | CIP-002 through CIP-014 (plus sub-requirements)             |

---

## 10.7 ENISA (EU Agency for Cybersecurity)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | European Union Agency for Cybersecurity                        |
| Abbreviation                 | ENISA                                                          |
| Entry Type                   | EU Agency (technical expertise, not regulation-making)        |
| Governance Tier              | L2 (EU institutional body; supports regulatory implementation)|
| Headquarters                 | Athens, Greece (+ Heraklion, Crete)                          |
| Key Functions                | Threat landscape reports, EU cybersecurity certification framework (EUCC), NIS2 support, CRA reporting |
| Relationship to NIS2         | Receives incident reports; publishes guidance for transposition |
| EU Cybersecurity Act Role    | Maintains EU cybersecurity certification framework            |

---

## 10.8 Regulatory Compliance Comparison Table

| Regulation/Standard | Type        | Tier | Enforcer            | Geography  | Domain            | Max Penalty        |
|---------------------|-------------|------|---------------------|------------|-------------------|--------------------|
| GDPR                | Regulation  | L2   | National DPAs       | EU/EEA     | Data protection   | 4% global revenue  |
| NIS2                | Directive   | L2   | National NCA        | EU         | Cybersecurity     | €10M / 2% revenue |
| CRA                 | Regulation  | L2   | Market surveillance | EU         | Product security  | €15M / 2.5%       |
| EU AI Act           | Regulation  | L2   | National AI authority| EU        | AI systems        | €35M / 7%         |
| HIPAA               | Federal law | L2   | HHS OCR             | USA        | Healthcare data   | $1.9M/yr/category |
| FISMA               | Federal law | L2   | OMB/CISA            | USA federal| Gov IT security   | Operational        |
| FedRAMP             | Program     | L2   | FedRAMP PMO (GSA)   | USA federal cloud | Cloud security | Operational     |
| PCI DSS v4.0.1      | Contractual | L3   | Card networks       | Global     | Payment data      | Contractual fines  |
| CCPA/CPRA           | State law   | L2   | CA Privacy Protection Agency | California | Consumer data | $7,500/violation  |
| UNECE Reg 155       | Regulation  | L2   | National type approval| UN (EU/JP/KR) | Automotive cyber | Type approval denial |
| SOX                 | Federal law | L2   | SEC                 | USA        | Financial IT controls | Criminal charges |
| NERC CIP            | Regulation  | L2   | FERC                | North America | Energy ICS     | $1M/day/violation  |
| DORA                | Regulation  | L2   | National financial supervisors | EU | Financial ICT resilience | Per NCA   |

---

## 10.9 Regulatory → Technical Standards Alignment

| Regulation       | Referenced/Aligned Technical Standard              | Relationship                  |
|------------------|-----------------------------------------------------|-------------------------------|
| GDPR Art. 32     | ISO/IEC 27001, ISO/IEC 27701, NIST SP 800-53      | Referenced; not mandatory     |
| GDPR Art. 25     | ISO/IEC 27701, ISO 31700                           | Privacy by design guidance    |
| NIS2 Art. 21     | ENISA Guidelines, ISO 27001, IEC 62443            | Referenced; not prescriptive  |
| CRA              | EN 303 645, IEC 62443-4-1/4-2, ISO/IEC 27001     | Harmonized = conformity       |
| EU AI Act        | ISO/IEC 42001, ISO/IEC 23894, ISO/IEC 22989      | Technical standards in development |
| UNECE Reg 155    | ISO/SAE 21434                                      | Explicitly referenced         |
| UNECE Reg 156    | ISO 24089                                          | Explicitly referenced         |
| HIPAA Security   | NIST SP 800-66 Rev. 2                             | Guidance mapping              |
| FedRAMP          | NIST SP 800-53 Rev. 5                             | **Mandatory** baseline        |
| PCI DSS v4.0     | NIST CSF, ISO 27001                               | Mapping available             |
| NERC CIP         | IEC 62351 (power systems security)                | Domain alignment              |
| FDA 510(k)/PMA   | IEC 62304 (SW lifecycle), IEC 62443 (security)    | Referenced in guidance        |
| FAA AC 20-115D   | DO-178C, DO-254, DO-326A                          | **Mandatory** compliance      |

---

## 10.10 Key Disambiguation Notes

### 1. GDPR Compliance vs. ISO 27001 Certification

| Aspect                  | GDPR Compliance                       | ISO 27001 Certification              |
|-------------------------|---------------------------------------|---------------------------------------|
| Nature                  | Legal compliance (regulatory)         | Management system certification       |
| Scope                   | ALL personal data processing          | Scoped ISMS (organization defines)    |
| Requirements            | Legal basis, DPO, DPIA, breach notification, data subject rights | Risk assessment, controls, PDCA cycle |
| Covers each other?      | ISO 27001 helps but does NOT equal GDPR | GDPR compliance not required for ISO 27001 |
| Who checks?             | National DPA (reactive/complaint)     | Accredited certification body (audit) |

### 2. NIST Guidelines vs. NIST Standards

| Type        | Example              | Binding for US Federal? | Binding for Private Sector? |
|-------------|----------------------|-------------------------|-----------------------------|
| FIPS        | FIPS 140-3           | **Yes** (OMB mandate)  | Only if contractually required |
| SP 800      | SP 800-53            | **Recommended** (often required by contract) | Voluntary (but widely adopted) |
| CSF         | CSF 2.0              | Recommended             | Voluntary                   |
| "NIST compliant" | (vague marketing)| Meaningless without specifying WHICH document | Meaningless             |

### 3. FedRAMP vs. FISMA

| Aspect      | FISMA                                   | FedRAMP                              |
|-------------|------------------------------------------|--------------------------------------|
| What        | Federal law for agency IT security       | Program for cloud service providers  |
| Who         | Federal agencies                         | CSPs wanting to serve federal agencies |
| Basis       | NIST SP 800-53 controls                  | NIST SP 800-53 + FedRAMP-specific additions |
| Assessment  | Agency self-assessment + IG review       | 3PAO (Third Party Assessment Org)    |
| Result      | Agency ATO (Authorization to Operate)    | FedRAMP P-ATO or Agency ATO         |

### 4. Regulation vs. Directive vs. Regulation (EU)

| EU Legal Instrument | Binding | Direct Applicability | National Transposition | Example    |
|--------------------|---------|---------------------|------------------------|------------|
| Regulation          | Yes     | **Directly applicable** (no national law needed) | Not required | GDPR, CRA, AI Act |
| Directive           | Yes (goal) | **Not directly** — must be transposed to national law | Required within deadline | NIS2, PSD2 |
| Decision            | Yes     | Only for addressees    | N/A                    | Adequacy decisions |
| Recommendation      | No      | Non-binding guidance   | N/A                    | AI ethics guidelines |

---

*End of Chapter 10 — Government & Regulatory Bodies*
*Next: Chapter 11 — Automotive & Embedded Domain Standards*
