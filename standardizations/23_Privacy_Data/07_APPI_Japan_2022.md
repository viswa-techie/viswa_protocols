# APPI — Japan's Act on Protection of Personal Information (2022 Amendments)

**Topic:** Act on the Protection of Personal Information (APPI / 個人情報保護法)  
**Law:** Act No. 57 of 2003; Major Amendments: 2015 (effective 2017), 2020 (effective April 1, 2022)  
**Enforcement Authority:** PPC (Personal Information Protection Commission / 個人情報保護委員会)  
**Domain:** Personal information protection; Japan; Asia-Pacific  
**Audience:** Privacy professionals managing Japan operations, DPOs, compliance teams, technology companies serving Japanese market  
**Prerequisites:** Basic privacy law concepts; understanding of GDPR (for comparison); awareness of Japanese business culture

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Milestone |
|------|-----------|
| 2003 | **Original APPI enacted** (Act No. 57); first comprehensive privacy law in Japan |
| 2005 | APPI takes full effect; applies to businesses handling >5,000 individuals' data |
| 2015 | **Major amendment** — establishes PPC; removes 5,000 threshold; introduces anonymously processed information |
| 2016 | PPC established as independent supervisory authority (replaced sectoral oversight) |
| 2017 | 2015 amendments take effect; PPC operational |
| 2019 | EU recognizes Japan (mutual adequacy decision); Japan recognizes EU — free data flow |
| 2020 | **2020 Amendment** passed (Act No. 44 of 2020) — most significant overhaul |
| 2022 | **2020 amendments take effect** (April 1, 2022) — pseudonymized data; individual rights enhanced; cross-border transfer tightened; breach notification mandatory |
| 2023 | PPC issues further guidance; enforcement activity increases; three-year review cycle begins |
| 2024 | Next amendment cycle discussions; AI/generative AI guidance from PPC |

### 1.2 Key Characteristics of APPI

| Characteristic | Description |
|:-:|---|
| **Omnibus law** | Applies to ALL sectors (unlike US sectoral approach) |
| **Principles-based** | Sets principles; PPC issues detailed guidelines |
| **Business-friendly** | Designed to BALANCE privacy protection WITH data utilization |
| **Mutual adequacy with EU** | Enables free data flows between Japan and EU (since 2019) |
| **Three-year review cycle** | Law reviewed every three years; ensures currency with technology/society changes |
| **PPC-centralized** | Single authority (PPC) for all sectors; replaced fragmented sectoral oversight |

---

## Chapter 2 — APPI Architecture & Structure (Post-2022)

### 2.1 Key Definitions

| Term (Japanese) | English | Definition |
|:---:|:---:|---|
| **個人情報 (Kojin Jōhō)** | Personal Information | Information relating to a living individual that can identify the specific individual (by name, DOB, or other description; or individual identification code) |
| **個人識別符号 (Kojin Shikibetsu Fugō)** | Individual Identification Code | Codes converted from physical characteristics (biometric); or assigned by government/business (passport #; driver's license #; My Number) |
| **要配慮個人情報 (Yōhairyo Kojin Jōhō)** | Special Care-Required Personal Information | Race; creed; social status; medical history; criminal record; being a crime victim; other information requiring special care to avoid unjust discrimination |
| **仮名加工情報 (Kamei Kakō Jōhō)** | Pseudonymously Processed Information | (2022 NEW) Personal information processed so individual cannot be identified without additional information |
| **匿名加工情報 (Tokumei Kakō Jōhō)** | Anonymously Processed Information | Personal information processed so individual cannot be identified AND original info cannot be restored |
| **個人関連情報 (Kojin Kanren Jōhō)** | Personal-Related Information | (2022 NEW) Information relating to living individual that is NOT personal information for the holder (e.g., cookies; browsing history held by third party) |
| **個人情報取扱事業者 (Kojin Jōhō Toriatsukai Jigyōsha)** | Personal Information Handling Business Operator (PIHBO) | Entity using personal information database for business |
| **本人 (Honnin)** | Individual/Data Subject | The specific individual identified by personal information |

### 2.2 Data Categories Hierarchy

```mermaid
graph TB
    subgraph "APPI Data Categories (Post-2022)"
        PI[Personal Information<br/>━━━━━━━━━<br/>Identifies living individual<br/>(name; DOB; address +; identification codes)]
        
        SCRPI[Special Care-Required PI<br/>━━━━━━━━━<br/>Race; creed; social status;<br/>medical; criminal; crime victim<br/>★ Requires consent for acquisition]
        
        PPI[Pseudonymously Processed Info<br/>━━━━━━━━━<br/>NEW (2022)<br/>Cannot identify without additional info<br/>★ Relaxed obligations for internal use]
        
        API[Anonymously Processed Info<br/>━━━━━━━━━<br/>Cannot identify; cannot restore<br/>★ May be shared without consent<br/>(with disclosure)]
        
        PRI[Personal-Related Info<br/>━━━━━━━━━<br/>NEW (2022)<br/>Relates to individual but not PI<br/>for the holder (cookies; web history)<br/>★ Consent required for TRANSFER<br/>that creates PI at recipient]
    end
    
    PI --> SCRPI
    PI --> PPI
    PI --> API
    PRI -.->|"becomes PI<br/>when transferred<br/>+ combined"| PI
```

---

## Chapter 3 — 2022 Amendment Deep Dive

### 3.1 Major Changes in 2022

| Change | Before (pre-2022) | After (2022) |
|:------:|:---:|:---:|
| **Breach notification** | Effort-based (recommended; not mandatory) | **Mandatory** — notify PPC and affected individuals for qualifying breaches |
| **Individual rights** | Limited (disclosure; correction; cessation) | **Expanded** — disclosure includes digital format; right to deletion; right to cessation of third-party provision; right to know transfer records |
| **Pseudonymously processed info** | Concept did not exist | **New category** — enables internal analysis with relaxed rules (no individual consent for change of purpose if pseudonymized) |
| **Personal-related information** | Not regulated | **New regulation** — consent required before providing to third party if it becomes PI at recipient |
| **Cross-border transfers** | Consent or adequate country or contractual measures | **Tightened** — must provide specific information about destination country's regime; continuous monitoring of transfer partner's compliance |
| **Penalties** | Fines up to ¥300K for individuals; ¥300K for entities | **Increased** — up to ¥100M for entities (for illegal commands); ¥1M for entities (for other violations) |
| **Disclosure format** | Paper-based assumed | **Digital format** (electromagnetic records) upon individual request |
| **Extraterritorial** | Limited applicability to foreign entities | **Strengthened** — PPC can issue recommendations/orders to foreign entities; report to foreign authorities |

### 3.2 Breach Notification (Art. 26, 2022)

| Aspect | Requirement |
|:------:|-------------|
| **Qualifying breaches** | (1) Special care-required PI involved; (2) PI subject to potential unauthorized use (financial); (3) Potentially malicious purpose; (4) Affects >1,000 individuals |
| **Notify PPC** | Preliminary report: promptly (3-5 days). Full report: within 30 days (60 days for malicious breaches — to allow investigation) |
| **Notify individuals** | Promptly after becoming aware; content: nature of breach; PI categories; approximate number; measures taken; contact point |
| **Exceptions** | If individual notification difficult/impracticable: alternative measures (public announcement + inquiry point) acceptable |

### 3.3 Cross-Border Transfer Rules (2022)

| Mechanism | Requirements |
|:---:|---|
| **Individual consent** | Must INFORM individual BEFORE consent: (a) destination country name; (b) that country's privacy regime (or lack thereof); (c) measures taken by foreign third party. Individual then gives informed consent. |
| **Adequate country** | Country recognized by PPC as having equivalent system. Currently: EU/EEA (mutual adequacy) + UK |
| **Contractual measures (APECs/equivalent)** | Ensure foreign recipient takes equivalent measures. PIHBO must: (a) take necessary measures to ensure continued compliance; (b) provide information to individual upon request about measures taken; (c) take action if recipient fails to comply |
| **APEC CBPR** | Japan participates in APEC Cross-Border Privacy Rules (CBPR); certification accepted |

---

## Chapter 4 — Individual Rights

### 4.1 Rights Under APPI (Post-2022)

| Right | Article | Description |
|:-----:|:-------:|-------------|
| **Disclosure** | Art. 33 | Request disclosure of retained personal data (2022: includes digital format option) |
| **Correction** | Art. 34 | Request correction, addition, or deletion of inaccurate data |
| **Cessation of use** | Art. 35(1) | Request cessation of use/deletion when: (a) no longer necessary; (b) breach occurred; (c) obtained without proper legal basis; or rights/interests likely to be harmed |
| **Cessation of third-party provision** | Art. 35(3) | Request stop of third-party transfer when: obtained without proper basis; or rights/interests likely to be harmed |
| **Explanation** | Art. 32(2) | PIHBO must endeavor to explain reasons for decisions on requests |
| **Access to transfer records** | Art. 33(5) | (2022 NEW) Request disclosure of third-party provision/receipt records |

### 4.2 Response Requirements

| Aspect | Requirement |
|:------:|-------------|
| **Timeline** | Without delay (no specific day count; interpreted as 2-4 weeks by PPC guidelines) |
| **Format** | By method requested by individual (2022: electromagnetic record format if requested; unless operational difficulty) |
| **Fee** | May charge reasonable fee; must be publicly disclosed |
| **Refusal** | May refuse if: (a) would harm life/safety/property of individual or third party; (b) would significantly impede proper conduct of PIHBO's business; (c) would violate other laws. Must notify individual of refusal and reasons. |

---

## Chapter 5 — Obligations of PIHBOs

### 5.1 Core Obligations

| Obligation | Article | Description |
|:---:|:---:|---|
| **Purpose specification** | Art. 17 | Specify purpose of use as clearly as possible |
| **Purpose limitation** | Art. 18 | Do not use beyond scope necessary to achieve specified purpose (without consent) |
| **Proper acquisition** | Art. 20 | Do not acquire by deception or other improper means |
| **Special care consent** | Art. 20(2) | Obtain consent BEFORE acquiring special care-required PI |
| **Accuracy/currency** | Art. 22 | Endeavor to keep data accurate and up-to-date within scope necessary for purpose |
| **Security management** | Art. 23 | Take necessary and appropriate measures for security management of personal data |
| **Employee supervision** | Art. 24 | Supervise employees to ensure security of personal data |
| **Subcontractor supervision** | Art. 25 | Exercise necessary and appropriate supervision over subcontractors |
| **Third-party restriction** | Art. 27 | May not provide to third parties without prior consent (exceptions: legal obligation; life/safety; public health; cooperation with government) |
| **Records** | Art. 29-30 | Maintain records of third-party transfers and receipts |
| **Privacy policy** | Art. 32 | Make publicly available: PIHBO identity; purpose; procedure for requests; complaints contact |

### 5.2 Third-Party Transfer Rules

| Transfer Type | Requirement |
|:---:|---|
| **Domestic (Japan→Japan)** | Prior consent of individual required (Art. 27). Exceptions: legal obligation; life/safety; public health; cooperation with public bodies; academic research; successors in business transfer |
| **Cross-border (Japan→Foreign)** | Prior consent + informed consent about foreign country regime (Art. 28). OR: adequate country. OR: contractual safeguards with continuous monitoring |
| **Opt-out provision** | Can provide to third parties via opt-out mechanism: must (a) notify PPC in advance; (b) publicly disclose categories, method, opt-out procedure; (c) NOT use opt-out for: special care-required PI; illegally acquired PI; PI received via opt-out from another PIHBO |
| **Personal-related information** | (2022) If providing personal-related info (cookies etc.) to third party who can link it to PI → must confirm individual's consent before providing |

---

## Chapter 6 — EU-Japan Mutual Adequacy

### 6.1 Adequacy Decision Details

| Aspect | Detail |
|:------:|--------|
| **EU → Japan** | EU Commission adequacy decision (January 2019); enables transfers from EU to Japan without SCCs/consent |
| **Japan → EU** | PPC recognition of EU as adequate (mutual); enables transfers from Japan to EU |
| **Supplementary Rules** | Japan committed to "Supplementary Rules" providing additional protections for EU-origin data |
| **Supplementary Rules content** | (1) "Special care-required PI" includes EU sensitive data categories. (2) Retention limit: data deleted when no longer needed. (3) Onward transfers from Japan: adequacy or consent. (4) Access by Japanese public authorities: proportionate; subject to oversight |
| **Mutual benefit** | Companies can transfer EU↔Japan data freely; critical for automotive, electronics, pharmaceutical industries |
| **Review** | Periodic review every 2 years to confirm adequacy continues |

### 6.2 Practical Impact

| Scenario | Without Adequacy | With Adequacy |
|:---:|:---|:---|
| EU company → Japan subsidiary | Need SCCs or BCRs or explicit consent | Free transfer (no additional mechanism required) |
| Japanese company → EU entity | Need Art. 28 consent with country information | Free transfer (EU recognized as adequate) |
| Japan company with EU customer data | Must apply supplementary rules to EU-origin data | Supplementary rules = higher protection floor for EU data |

---

## Chapter 7 — Comparison: APPI vs. GDPR vs. CCPA

| Aspect | APPI (Japan) | GDPR (EU) | CCPA/CPRA (US-CA) |
|:------:|:---:|:---:|:---:|
| **Consent approach** | Generally opt-out for basic PI; opt-in for special care-required PI and cross-border | Opt-in (consent must be affirmative for all legal bases requiring consent) | Opt-out of sale/sharing |
| **Legal bases** | Purpose specification + proper acquisition (simpler than GDPR's 6 bases) | 6 explicit legal bases (Art. 6) | N/A (business can process unless consumer opts out) |
| **Sensitive data** | "Special care-required PI" (narrower: race; creed; medical; criminal) | Broader Art. 9 (+ genetic; biometric; political; trade union; sexual orientation) | "Sensitive PI" (CPRA: SSN; financial; biometric; etc.) |
| **Breach notification** | Mandatory (2022); 3-5 days preliminary; 30-60 days full report | 72 hours to DPA; without undue delay to individuals | "Most expedient time"; no strict deadline |
| **Cross-border** | Consent + country information; OR adequacy; OR contractual + monitoring | SCCs; BCRs; adequacy; derogations | No specific restriction (general obligations apply) |
| **Penalties** | Max ¥100M for entities (~$670K USD) | Max 4% global revenue or €20M | $2,500-$7,500 per violation |
| **Private right of action** | Not under APPI (civil code applies for damages) | Yes (Art. 82) | Limited (data breach only) |
| **Pseudonymized data** | Specific category with relaxed rules (internal use) | Incentivized but still personal data | Not a distinct category |
| **DPO** | Not required (but PIHBO must designate complaint handler) | Required in specific cases | Not required |
| **Extraterritorial** | Yes (2022 strengthened; PPC can issue orders to foreign entities) | Yes (Art. 3) | Yes (doing business in California) |

---

## Chapter 8 — Architecture Diagrams

### 8.1 APPI Compliance Architecture

```mermaid
graph TB
    subgraph "APPI Compliance Architecture (Post-2022)"
        subgraph "Governance"
            PURPOSE[Purpose Specification<br/>━━━━━━━━━<br/>• Specify purpose as clearly as possible<br/>• Publicly disclose/notify<br/>• Cannot change beyond reasonable relation]
            POLICY[Privacy Policy (公表事項)<br/>━━━━━━━━━<br/>• PIHBO identity & contact<br/>• Purpose of use<br/>• Third-party provision scope<br/>• Individual request procedures<br/>• Complaints contact]
        end
        
        subgraph "Data Processing"
            ACQUISITION[Proper Acquisition<br/>━━━━━━━━━<br/>• No deception/improper means<br/>• Special care: prior consent<br/>• Personal-related info:<br/>  confirm recipient consent (2022)]
            
            SECURITY_M[Security Management (安全管理措置)<br/>━━━━━━━━━<br/>• Organizational measures<br/>• Personnel measures<br/>• Physical measures<br/>• Technical measures<br/>• Understanding of foreign regime]
            
            TRANSFER[Third-Party Transfer<br/>━━━━━━━━━<br/>• Prior consent (default)<br/>• Opt-out mechanism (limited)<br/>• Cross-border: informed consent<br/>• Records maintained]
        end
        
        subgraph "Individual Rights"
            RIGHTS_J[Rights Response<br/>━━━━━━━━━<br/>• Disclosure (digital format 2022)<br/>• Correction<br/>• Cessation of use/deletion<br/>• Cessation of third-party provision<br/>• Transfer record disclosure (2022)]
        end
        
        subgraph "Incident Response"
            BREACH_J[Breach Response (2022)<br/>━━━━━━━━━<br/>• Qualifying breach assessment<br/>• PPC report: 3-5 days (prelim)<br/>• Full report: 30-60 days<br/>• Individual notification: promptly]
        end
    end
```

### 8.2 Cross-Border Transfer Decision Flow

```mermaid
graph TB
    subgraph "APPI Cross-Border Transfer Decision (2022)"
        START_T{Transfer personal data<br/>to foreign country?}
        
        ADEQUATE{Is destination country<br/>PPC-recognized adequate?<br/>(Currently: EU/EEA, UK)}
        
        FREE_T[Free Transfer<br/>Same as domestic transfer rules<br/>(just need consent for provision)]
        
        CONTRACTUAL{Does foreign recipient<br/>have equivalent measures<br/>(contract/APEC CBPR)?}
        
        CONT_T[Transfer with Conditions:<br/>• Take necessary measures<br/>• Continuous monitoring<br/>• Inform individual on request<br/>• Take action on non-compliance]
        
        CONSENT_T{Can you get individual's<br/>INFORMED consent?}
        
        INFO_CONSENT[Transfer with Informed Consent:<br/>• Inform: country name<br/>• Inform: country's privacy regime<br/>• Inform: measures taken by recipient<br/>• Then: obtain consent]
        
        NO_TRANSFER[Cannot Transfer<br/>(must find alternative)]
    end
    
    START_T --> ADEQUATE
    ADEQUATE -->|"Yes"| FREE_T
    ADEQUATE -->|"No"| CONTRACTUAL
    CONTRACTUAL -->|"Yes"| CONT_T
    CONTRACTUAL -->|"No"| CONSENT_T
    CONSENT_T -->|"Yes (with information)"| INFO_CONSENT
    CONSENT_T -->|"No"| NO_TRANSFER
```

---

## Chapter 9 — Case Studies

### 9.1 Global E-Commerce: Japan Market Entry with APPI Compliance

| Aspect | Detail |
|--------|--------|
| **Company** | US-based e-commerce platform entering Japanese market; 500K Japanese users expected; servers in US and Singapore |
| **Challenges** | (1) Cross-border transfer (Japan→US): stricter 2022 rules require informing users about US privacy regime. (2) Cookie handling: personal-related information rules (third-party cookies creating PI). (3) No prior Japan privacy program. |
| **Implementation** | |

| Workstream | Actions |
|:---:|---|
| **Purpose specification** | Defined purposes: (a) order fulfillment; (b) customer support; (c) product recommendations; (d) marketing. Each published in Japanese privacy policy. |
| **Cross-border disclosure** | Privacy policy states: "Your data will be processed in the United States. The US does not have comprehensive federal privacy legislation equivalent to APPI. We apply the following protective measures: [contract with US entity; encryption; access controls; security audit]." |
| **Personal-related info** | Third-party analytics cookies (Google Analytics; ad platforms): confirmed that when these cookies are combined with user accounts → creates PI at recipient. Solution: consent banner explaining third-party data sharing; individual can opt-out. |
| **Security management measures** | Documented per PPC guidelines: (a) Organizational: designated privacy manager; incident response team. (b) Personnel: training; NDAs; access provisioning/deprovisioning. (c) Physical: data center access control. (d) Technical: encryption; logging; intrusion detection. (e) Understanding of foreign regime: documented US and Singapore privacy environments. |
| **Breach response** | Established process: detect → assess (qualifying?) → PPC notification (3-5 days) → individual notification (promptly) → full report (30 days). Challenge: time zone differences (PPC business hours JST). Solution: Japan-based incident coordinator for immediate PPC contact. |

### 9.2 Japanese Manufacturer: Pseudonymously Processed Information for Analytics

| Aspect | Detail |
|--------|--------|
| **Company** | Japanese automotive manufacturer; uses connected car data (driving patterns; location; vehicle diagnostics) |
| **Challenge** | Wants to analyze driving data for product improvement; original purpose was "vehicle diagnostics and maintenance." New purpose (product R&D) not covered by original consent. |
| **Solution (pre-2022)** | Would need: re-consent from all drivers for new purpose; or anonymize data completely (losing utility). |
| **Solution (2022: Pseudonymously Processed Information)** | |

| Step | Action |
|:---:|---|
| 1 | **Create pseudonymized dataset**: Remove driver name; replace with random ID; generalize location to prefecture level; aggregate trips to daily patterns. Retain: driving speed patterns; brake usage; route type preference. |
| 2 | **Benefits under Art. 41**: No need for consent to use for DIFFERENT purpose (product improvement) — because data is pseudonymously processed. No need for individual requests handling on this dataset. Reduced security management obligations. |
| 3 | **Restrictions**: CANNOT provide pseudonymized data to third parties. MUST delete the linking table (pseudonym→real identity) if not needed. MUST NOT attempt to re-identify. Internal use ONLY. |
| 4 | **Publicly disclose**: That company processes pseudonymously processed information; categories of PI used to create it; how individuals can request information. |

**Outcome:** Enabled product improvement analytics without re-consenting millions of users; maintained privacy protection via pseudonymization; fully compliant with 2022 APPI.

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **Next three-year review** | 2025-2026 | Potential amendment cycle; AI/generative AI considerations; further rights expansion |
| **AI regulation** | 2024-2026 | PPC guidance on generative AI (training data; personal information in prompts); potential legislative changes |
| **Children's privacy** | 2025+ | Enhanced protections for minors (currently minimal special provisions beyond "proper acquisition") |
| **Cookie/tracking regulation** | 2024-2025 | Further clarification of personal-related information rules; potential expansion |
| **Adequacy expansion** | 2025+ | PPC considering additional adequacy recognitions (UK confirmed 2024; other countries under review) |
| **Penalty increases** | 2025-2026 | Discussion of increasing penalties closer to GDPR levels (currently much lower: ¥100M vs. billions of yen under GDPR equivalence) |
| **Decentralized identity** | 2026+ | My Number integration; digital ID; impact on personal information handling |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is "Special Care-Required Personal Information" under APPI, and how does acquiring it differ from regular personal information?

**A:**

| Category | Examples |
|:---:|---|
| Race | Racial or ethnic origin |
| Creed | Religious beliefs; political beliefs |
| Social status | Caste; social standing |
| Medical history | Disease; disability; medical treatment; health checkup results; guidance by doctor |
| Criminal record | History of criminal offenses; arrests |
| Crime victimization | Fact of being a crime victim |
| Other (Cabinet Order) | Physical/intellectual/mental disability; results of medical examinations |

**Key difference in acquisition:**

| Aspect | Regular PI | Special Care-Required PI |
|:---:|:---:|:---:|
| **Consent for acquisition** | NOT required (purpose notification sufficient) | **REQUIRED** — must obtain prior consent BEFORE acquiring |
| **Opt-out mechanism** | Allowed (can provide to third parties via opt-out) | **NOT allowed** — opt-out mechanism CANNOT be used for special care-required PI |
| **Why stricter** | Disclosure could lead to unjust discrimination against individual | Higher risk of harm; social discrimination in Japan context |

### Tier 2: Mid-Level

**Q2:** Explain the concept of "Personal-Related Information" (個人関連情報) introduced in the 2022 amendments. Why was it needed?

**A:**

**Definition:** Information relating to a living individual that does NOT constitute personal information, pseudonymously processed information, or anonymously processed information FOR THE ENTITY THAT HOLDS IT.

**Example:** A data management platform (DMP) holds cookie IDs, browsing history, purchase behavior linked to cookie identifiers. The DMP CANNOT identify the individual (no name; no account). Therefore for the DMP, this is "personal-related information" (NOT personal information).

**The problem it solves:**

| Before 2022 | After 2022 |
|:---:|:---:|
| DMP holds browsing data (cookie-based; not PI for DMP) | Same — not PI for DMP |
| DMP provides to advertiser who can link cookie to user account → BECOMES PI at advertiser | Same — becomes PI at advertiser |
| **No APPI obligation on DMP** (since not PI for them) | **DMP must confirm** that advertiser has obtained individual's consent before providing the data |
| Result: data enrichment without consent | Result: consent required in the chain |

**Rule (Art. 31):** When providing personal-related information to a third party who is expected to acquire it as personal information (i.e., recipient can link to individual), the PROVIDER must confirm that the third party has obtained the individual's consent.

**Practical impact:** Affects: ad-tech companies; data brokers; DMP providers; analytics platforms. Must obtain confirmation of downstream consent before sharing cookie-level/device-level data.

### Tier 3: Senior

**Q3:** Your company operates in both EU and Japan with mutual adequacy. A Japanese subsidiary receives EU-origin data. What supplementary rules apply, and how do you operationalize them?

**A:**

| Supplementary Rule | Operationalization |
|:---:|---|
| **1. Sensitive data expanded** | EU "special categories" (Art. 9 GDPR) treated as "special care-required PI" in Japan. Implementation: tag all EU-origin data with "EU_ORIGIN" flag in system; apply special care-required PI controls to fields that are sensitive under GDPR but not traditionally under APPI (e.g., sexual orientation; political opinions; trade union membership). |
| **2. Retention limitation** | Must specify retention periods and delete when no longer needed (APPI normally just says "endeavor to keep current"). Implementation: for EU-origin data, enforce GDPR-style retention schedules (document retention period at collection; automated deletion at expiry). |
| **3. Onward transfer restrictions** | When transferring EU-origin data FROM Japan to a THIRD country, must ensure either: (a) recipient is in adequate country; (b) obtain individual's consent. Cannot rely on APPI's general cross-border mechanisms alone. Implementation: maintain "EU-origin" data lineage; apply additional controls before any onward transfer; document adequacy of final destination. |
| **4. Government access limitations** | Japanese public authority access to EU-origin data limited to what is necessary and proportionate; subject to independent oversight. Implementation: if receiving government request for EU-origin data, assess proportionality; document legal basis; consider challenging disproportionate requests; maintain log of government access requests for EU adequacy review. |
| **5. Individual rights enhanced** | EU individuals retain GDPR-level rights even when data in Japan. Implementation: honor right to erasure (stronger than APPI's "cessation of use"); honor right to restriction; honor data portability in structured format. |

---

## Chapter 12 — Cheat Sheet & Quick Reference

```
═══════════════════════════════════════════
APPI (JAPAN) — QUICK REFERENCE (2022 AMENDMENTS)
═══════════════════════════════════════════

LAW: Act on Protection of Personal Information (Act No. 57/2003)
MAJOR AMENDMENTS: 2015 (eff. 2017); 2020 (eff. April 2022)
AUTHORITY: PPC (Personal Information Protection Commission)
MUTUAL ADEQUACY: EU/EEA ↔ Japan (since 2019)

═══════════════════════════════════════════
KEY DATA CATEGORIES:
  Personal Information (個人情報):
    Identifies living individual

  Special Care-Required PI (要配慮個人情報):
    Race; creed; social status; medical;
    criminal; crime victim
    ★ Prior consent needed for ACQUISITION

  Pseudonymously Processed (仮名加工情報) [2022 NEW]:
    Individual not identifiable without additional info
    ★ Relaxed rules for INTERNAL use only
    ★ No consent for different purpose
    ★ CANNOT provide to third parties

  Anonymously Processed (匿名加工情報):
    Cannot identify; cannot restore
    ★ Can share with disclosure obligations

  Personal-Related Info (個人関連情報) [2022 NEW]:
    Relates to individual; NOT PI for holder
    (cookies; browsing data at DMP)
    ★ Must confirm recipient's consent before sharing

═══════════════════════════════════════════
CONSENT MODEL:
  Regular PI:
    • Acquisition: NO consent needed
    • Purpose change: consent needed
    • Third-party provision: consent needed
    • Cross-border: INFORMED consent needed
  
  Special Care-Required PI:
    • Acquisition: consent REQUIRED
    • No opt-out mechanism allowed

═══════════════════════════════════════════
INDIVIDUAL RIGHTS (2022):
  • Disclosure (digital format option)
  • Correction (inaccurate data)
  • Cessation of use / deletion
  • Cessation of third-party provision
  • Transfer record disclosure (NEW 2022)

═══════════════════════════════════════════
BREACH NOTIFICATION (2022 — MANDATORY):
  Qualifying: special care PI; financial;
    malicious; >1,000 individuals
  PPC notification: 3-5 days (preliminary)
  Full report: 30 days (60 for malicious)
  Individual notification: promptly

═══════════════════════════════════════════
CROSS-BORDER TRANSFER (2022 — TIGHTENED):
  Option 1: Adequate country (EU/EEA; UK)
  Option 2: Contract + continuous monitoring
  Option 3: Informed consent (must tell:
    country name + privacy regime + measures)
  Option 4: APEC CBPR certification

═══════════════════════════════════════════
PENALTIES (2022 — INCREASED):
  Entity: up to ¥100M (~$670K) for non-compliance
            with PPC orders
  Individual: up to ¥1M or 1 year imprisonment
  
  (Still much lower than GDPR; increase expected)

═══════════════════════════════════════════
SECURITY MANAGEMENT MEASURES (4+1):
  1. Organizational (designated manager; rules; audits)
  2. Personnel (training; NDAs; supervision)
  3. Physical (access control; device management)
  4. Technical (access control; encryption; monitoring)
  5. Understanding of foreign legal regime (2022 NEW)

═══════════════════════════════════════════
EU-JAPAN SUPPLEMENTARY RULES (for EU-origin data):
  • Treat EU sensitive categories as special care PI
  • Enforce retention limits + deletion
  • Restrict onward transfers to third countries
  • Government access: necessity + proportionality
  • Honor GDPR-level individual rights
```

---

*End of Document — 07_APPI_Japan_2022.md*
