# CHAPTER 10 — GOVERNMENT & REGULATORY BODIES
# Prompt for: Universal Technology Standards Governance Encyclopedia
# Depends on: MASTER_PROMPT.md, Chapter 1 (taxonomy), Chapter 2 (ISO/IEC/ITU L1/L2)

---

## CHAPTER SCOPE

**Covers (L2 — Regulatory/Government with standards relevance):**
- NIST (National Institute of Standards and Technology) — USA
- FCC (Federal Communications Commission) — USA
- ETSI (European Telecommunications Standards Institute) — EU
- ENISA (European Union Agency for Cybersecurity)
- European Commission — key technology regulations
- BSI (Bundesamt für Sicherheit in der Informationstechnik) — Germany
- NCSC (National Cyber Security Centre) — UK
- DHS CISA (Cybersecurity and Infrastructure Security Agency) — USA
- UNECE WP.29 — automotive regulation
- GDPR, NIS2, CRA — EU digital regulations
- CCPA/CPRA — California Privacy
- HIPAA — USA healthcare
- FDA — medical device software
- FAA — aviation software
- NERC CIP — energy sector cybersecurity (North America)
- PCI DSS (Payment Card Industry — technically L3 but regulatory in practice)
- FedRAMP — US federal cloud security

---

## GENERATION INSTRUCTIONS FOR THIS CHAPTER

Act as a world-class compliance strategist and regulatory architecture expert.
Generate the complete **Chapter 10: Government & Regulatory Bodies** encyclopedia entries.

### SECTION 10.1 — REGULATORY GOVERNANCE MODEL

Explain the L2 regulatory governance model:
- Distinction between:
  - **Regulation**: legally binding, enacted by government/parliament
  - **Standard**: voluntary specification by standards body
  - **Mandate**: regulation that mandates compliance with a specific standard
  - **Guideline**: non-binding best-practice document
  - **Certification**: third-party confirmation of compliance
- How regulatory mandates link to voluntary standards:
  - EU: GDPR references "state of the art" → points to ISO 27001, ISO 27701
  - FDA: 510(k) clearance for software references IEC 62304 (software lifecycle)
  - FAA: AC 20-115D mandates DO-178C compliance for airborne software
  - UNECE WP.29 Reg 155: mandates ISO/SAE 21434 compatible CSMS
- The "harmonized standard" concept in EU regulatory framework:
  - EU Directive/Regulation → references harmonized standard → compliance = presumption of conformity
  - Example: Radio Equipment Directive (RED) → EN 303 645 (IoT security)
- The US "NIST framework" vs. EU "harmonized standard" approach comparison

### SECTION 10.2 — NIST (NATIONAL INSTITUTE OF STANDARDS AND TECHNOLOGY)

Full encyclopedia entry:

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | National Institute of Standards and Technology    |
| Abbreviation                 | NIST                                              |
| Entry Type                   | US Federal Agency + Standards Development Body   |
| Parent agency                | US Department of Commerce                        |
| Governance Tier              | L2 (government) — NIST publications are normative in US federal procurement |
| Founded                      | 1901 (as NBS — National Bureau of Standards)     |
| Legal basis                  | NIST Act (15 U.S.C. § 271–278)                   |
| Publication series           | FIPS (Federal Information Processing Standards), SP (Special Publications), IR (Internal Reports) |
| Specification access         | Free — all NIST publications at csrc.nist.gov / nvlpubs.nist.gov |

**Key NIST Publication Series:**

| Series    | Full Name                              | Authority Level                          |
|-----------|----------------------------------------|------------------------------------------|
| FIPS      | Federal Information Processing Standards| Mandatory for US federal agencies (Title 15 + OMB Circular A-130) |
| SP 800    | Special Publications — Computer Security| Guidance; recommended; often referenced in contracts |
| SP 1800   | NIST Cybersecurity Practice Guides     | Practical implementation guides          |
| NISTIR    | NIST Interagency/Internal Reports      | Research and background                  |
| SP 500    | Computer Science & IT standards        | IT management, SW testing                |

**Critical NIST Standards for Technology Engineers:**

| Document            | Title                                                     | Domain               |
|---------------------|-----------------------------------------------------------|----------------------|
| FIPS 140-3          | Security Requirements for Cryptographic Modules          | Crypto module validation|
| FIPS 197            | Advanced Encryption Standard (AES)                       | Symmetric encryption |
| FIPS 186-5          | Digital Signature Standard (DSS) — ECDSA, EdDSA         | Digital signatures   |
| FIPS 180-4          | Secure Hash Standard (SHA-2 family)                      | Hash functions       |
| FIPS 202            | SHA-3 Standard (Keccak)                                  | Hash functions       |
| FIPS 203            | ML-KEM (post-quantum key encapsulation — Kyber)         | Post-quantum crypto  |
| FIPS 204            | ML-DSA (post-quantum digital signature — Dilithium)     | Post-quantum crypto  |
| FIPS 205            | SLH-DSA (post-quantum stateless hash signature — SPHINCS+)| Post-quantum crypto |
| SP 800-53 Rev. 5    | Security and Privacy Controls for IS                    | Risk management      |
| SP 800-63B          | Digital Identity Guidelines — Authentication             | Identity/Auth        |
| SP 800-171          | Protecting CUI in Non-Federal Systems                    | Defense supply chain |
| SP 800-190          | Application Container Security Guide                     | Container security   |
| SP 800-218          | Secure Software Development Framework (SSDF)             | SW security          |
| SP 800-207          | Zero Trust Architecture                                  | Network security     |
| NIST CSF 2.0        | Cybersecurity Framework 2.0                              | Risk management      |

**NIST Post-Quantum Cryptography Standardization (2024):**
NIST completed its PQC standardization process in 2024 — FIRST new public-key cryptographic standards in 20+ years:
- FIPS 203: ML-KEM (Key Encapsulation) based on CRYSTALS-Kyber
- FIPS 204: ML-DSA (Digital Signature) based on CRYSTALS-Dilithium
- FIPS 205: SLH-DSA (Hash-based signatures) based on SPHINCS+
- FIPS 206 (in progress): FN-DSA based on FALCON
Timeline: Threat model — "harvest now, decrypt later" attacks on RSA/ECC by quantum computers

### SECTION 10.3 — FCC (FEDERAL COMMUNICATIONS COMMISSION)

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Federal Communications Commission                |
| Entry Type                   | US Independent Regulatory Agency                 |
| Governance Tier              | L2                                                |
| Key technical regulations    | FCC Part 15 (unlicensed RF devices), FCC Part 22/24 (cellular), FCC ID certification |
| Relevance to hardware        | Any wireless device sold in USA must have FCC Part 15 authorization |
| FCC Part 15                  | Covers Wi-Fi, Bluetooth, ZigBee, 5G mmWave unlicensed |
| FCC certification marks      | FCC ID marking on all authorized devices          |
| Relationship to ETSI/CE      | FCC (USA) ≈ CE marking (EU) for RF/EMC compliance |

### SECTION 10.4 — ETSI (EUROPEAN TELECOMMUNICATIONS STANDARDS INSTITUTE)

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | European Telecommunications Standards Institute  |
| Abbreviation                 | ETSI                                              |
| Entry Type                   | Regional Standards Body + EU Official Body       |
| Governance Tier              | L2/L3 (officially recognized by EU for harmonized standards) |
| Founded                      | 1988                                              |
| Headquarters                 | Sophia Antipolis, France                         |
| Key function                 | Produces "European Standards" (EN) — CE marking   |
| Relationship to 3GPP         | ETSI is one of the 7 organizational partners of 3GPP |
| Key standards                | 3GPP (cellular), EN 303 645 (IoT security), TS 119 000 (e-signatures) |

**Key ETSI Standards:**

| Standard           | Title                                              | Domain            |
|--------------------|----------------------------------------------------|-------------------|
| EN 303 645         | Cyber Security for Consumer IoT                   | IoT security      |
| EN 319 401         | Electronic Signatures and Infrastructures (ESI)  | PKI/digital sigs  |
| TS 102 042         | Policy requirements for CAs                      | PKI               |
| ETSI ES 203 523    | Middleboxes — TLS interception implications      | Network security  |

### SECTION 10.5 — EU TECHNOLOGY REGULATIONS

Produce full entries for each major EU regulation:

**GDPR (General Data Protection Regulation):**

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | General Data Protection Regulation               |
| Regulation number            | EU 2016/679                                       |
| Entry Type                   | EU Regulation (directly binding law)             |
| Governance Tier              | L2                                                |
| Effective                    | May 25, 2018                                     |
| Enforcement                  | National Data Protection Authorities (DPAs) + EU Commission |
| Key Articles for engineering | Art. 25 (Privacy by Design), Art. 32 (Security), Art. 35 (DPIA), Art. 83 (fines) |
| Technical standards referenced| ISO 27001, ISO 27701, ENISA guidelines           |
| Max fine                     | €20 million or 4% of global annual turnover     |
| Adequacy decisions           | Countries with equivalent protection: UK, Japan, South Korea, Switzerland, others |

**NIS2 Directive (Network and Information Security 2):**

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Directive on measures for a high common level of cybersecurity (NIS2) |
| Directive number             | EU 2022/2555                                      |
| Entry Type                   | EU Directive (transposed to national law)        |
| Effective                    | October 2024 (national transposition deadline)   |
| Scope                        | Essential and Important entities in 18 sectors   |
| Key requirements             | Risk management, incident reporting (24h/72h), supply chain security, CISO responsibility |
| Relationship to NIS1         | Supersedes NIS1 (2016/1148) — expanded scope, stricter penalties |

**EU Cyber Resilience Act (CRA):**

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | EU Cyber Resilience Act                          |
| Regulation number            | EU 2024/2847                                      |
| Entry Type                   | EU Regulation                                    |
| Effective                    | December 2024 (adopted); 36-month implementation |
| Scope                        | ALL products with digital elements sold in EU    |
| Key requirements             | Secure by design, vulnerability handling, SBOM, CE marking for cybersecurity |
| Relevance                    | First regulation requiring CE marking for software cybersecurity |
| Open source impact           | Stewards of open source (commercial entities) must comply |

**EU AI Act:**

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | EU Artificial Intelligence Act                   |
| Regulation number            | EU 2024/1689                                      |
| Entry Type                   | EU Regulation                                    |
| Risk categories              | Unacceptable risk (banned), High risk, Limited risk, Minimal risk |
| High risk AI examples        | Medical devices, biometric ID, critical infrastructure, autonomous vehicles |
| GPAI models                  | General Purpose AI Models (GPT-4, Gemini) — specific obligations |
| Technical standards          | CEN/CENELEC working groups, ISO/IEC 42001, ISO/IEC 23894 |

**EU Radio Equipment Directive (RED) + EN 303 645:**
- RED Article 3.3(d)(e)(f) — cybersecurity requirements for wireless products
- EN 303 645 = ETSI consumer IoT security standard (CE marking)

### SECTION 10.6 — USA SECTOR REGULATIONS

**HIPAA (Healthcare):**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Full Name           | Health Insurance Portability and Accountability Act |
| Enacted             | 1996; Security Rule finalized 2003               |
| Enforcer            | HHS Office for Civil Rights (OCR)                |
| Key technical reqs  | Access control, audit controls, encryption (addressable), transmission security |
| PHI definition      | Protected Health Information — 18 HIPAA identifiers |
| Relationship to FHIR| HIPAA governs data protection; FHIR governs interoperability — both apply to health IT |

**FISMA / FedRAMP (Federal Cloud):**

| Standard      | Description                                        | Enforcer      |
|---------------|----------------------------------------------------|---------------|
| FISMA 2014    | Federal Information Security Modernization Act     | OMB / CISA   |
| FedRAMP       | Federal Risk and Authorization Management Program  | FedRAMP PMO  |
| FedRAMP authorization levels | Low, Moderate, High                  | Cloud service risk |
| NIST SP 800-53| Security controls basis for FedRAMP               | NIST          |

**PCI DSS (Payment Card Industry Data Security Standard):**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Full Name           | Payment Card Industry Data Security Standard     |
| Governance body     | PCI Security Standards Council (PCI SSC)         |
| Governance Tier     | L3 (consortium: Visa, Mastercard, Amex, Discover, JCB) — but contractually mandatory |
| Current version     | PCI DSS v4.0 (March 2022; v4.0.1 2024)          |
| Scope               | All entities that store, process, or transmit cardholder data |
| Key requirements    | 12 requirements across 6 goals: network security, cardholder data protection, vulnerability management, access control, monitoring, policy |

### SECTION 10.7 — REGULATORY COMPLIANCE FRAMEWORK COMPARISON TABLE

| Regulation/Standard | Type       | Tier | Enforcer          | Geography | Domain          | Max Penalty     |
|---------------------|------------|------|-------------------|-----------|-----------------|-----------------|
| GDPR                | Regulation | L2   | National DPAs     | EU/EEA    | Data protection | 4% global revenue|
| NIS2                | Directive  | L2   | National NCA      | EU        | Cybersecurity   | €10M / 2%       |
| CRA                 | Regulation | L2   | Market surveillance| EU       | Product security| Up to €15M / 2.5%|
| EU AI Act           | Regulation | L2   | National AI authority| EU     | AI systems      | €35M / 7%       |
| HIPAA               | Federal law| L2   | HHS OCR           | USA       | Healthcare data | Up to $1.9M/yr  |
| FISMA               | Federal law| L2   | OMB/CISA          | USA federal| Gov IT security | Operational     |
| FedRAMP             | Program    | L2   | FedRAMP PMO       | USA federal cloud| Cloud security| Operational    |
| PCI DSS             | Contractual| L3   | Card networks     | Global    | Payment data    | Contractual fines|
| CCPA/CPRA           | State law  | L2   | CA AG             | California| Consumer data   | $7,500/violation|
| UNECE Reg 155       | Regulation | L2   | National type approval| EU/JP/KR | Automotive cyber| Type approval   |
| SOX (Sarbanes-Oxley)| Federal law| L2   | SEC               | USA       | Financial IT    | Criminal charges|
| NERC CIP            | Regulation | L2   | FERC              | North America| Energy ICS  | $1M/day/violation|

### SECTION 10.8 — REGULATORY STANDARDS ALIGNMENT TABLE

Show how technical standards map to regulatory requirements:

| Regulation     | Referenced/Aligned Technical Standard              | Relationship Type              |
|----------------|-----------------------------------------------------|-------------------------------|
| GDPR Art. 32   | ISO/IEC 27001, NIST SP 800-53                      | Referenced; not mandatory      |
| GDPR Art. 35   | ISO/IEC 29134 (DPIA), ISO/IEC 27701               | Referenced; guidance           |
| NIS2           | ENISA Guidelines, ISO 27001, IEC 62443            | Referenced; not prescriptive   |
| CRA            | EN 303 645, IEC 62443, ETSI standards             | Harmonized standard = conformity|
| EU AI Act      | ISO/IEC 42001, ISO/IEC 23894                      | Technical standards in progress|
| UNECE Reg 155  | ISO/SAE 21434, ISO 24089                          | Explicitly referenced          |
| HIPAA Security | NIST SP 800-66 (guidance for HIPAA)               | Guidance only                  |
| FedRAMP        | NIST SP 800-53 Rev. 5                             | Mandatory baseline             |
| PCI DSS v4.0   | NIST CSF, ISO 27001 (optional mapping)            | Mapping available; not mandated|
| NERC CIP       | IEC 62351 (power systems security)               | Domain-specific alignment      |

### SECTION 10.9 — DISAMBIGUATION: REGULATORY CONFUSION PAIRS

1. **GDPR compliance vs. ISO 27001 certification**: These are different things
   - ISO 27001 certification helps demonstrate GDPR Art. 32 compliance but does NOT equal GDPR compliance
   - GDPR requires legal basis for processing, DPO appointment, breach notification — ISO 27001 does not cover all of these

2. **NIST guidelines vs. NIST standards**: 
   - NIST FIPS = mandatory for US federal agencies
   - NIST SP = guidance/recommendations — highly influential but voluntary (except when incorporated by contract)
   - "NIST compliant" is meaningless unless a specific FIPS or NIST SP is referenced

3. **FedRAMP vs. FISMA**:
   - FISMA: federal law governing all federal agency IT security programs
   - FedRAMP: program for cloud service providers seeking to serve federal agencies
   - FedRAMP is a subset of FISMA compliance applied to cloud services

4. **PCI DSS SAQ vs. ROC**:
   - SAQ (Self-Assessment Questionnaire): for smaller merchants
   - ROC (Report on Compliance): full audit by QSA (Qualified Security Assessor) — required for large merchants/service providers

---

## OUTPUT FORMAT REQUIREMENTS

- All EU regulation numbers must be precise (e.g., EU 2016/679 for GDPR)
- FIPS numbers must be current (use 2024 post-quantum FIPS 203/204/205)
- NIST SP numbers must include revision numbers
- PCI DSS version must be current (v4.0.1)
- Minimum length: 5,000 words
