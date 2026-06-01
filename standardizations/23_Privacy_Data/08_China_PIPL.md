# China PIPL — Personal Information Protection Law

**Topic:** Personal Information Protection Law of the People's Republic of China (PIPL / 个人信息保护法)  
**Law:** Adopted August 20, 2021; Effective November 1, 2021  
**Enforcement Authorities:** CAC (Cyberspace Administration of China); Ministry of Public Security; relevant industry regulators  
**Related Laws:** Cybersecurity Law (CSL, 2017); Data Security Law (DSL, 2021)  
**Domain:** Personal information protection; China; cross-border data; extraterritorial  
**Audience:** Privacy/compliance professionals operating in China, multinational company counsel, technology companies, data localization architects  
**Prerequisites:** Understanding of Chinese legal system; basic privacy law concepts; awareness of GDPR (for comparison); Chinese data governance framework (CSL + DSL + PIPL "three pillars")

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Milestone |
|------|-----------|
| 2012 | National People's Congress Decision on Strengthening Network Information Protection |
| 2016 | **Cybersecurity Law (CSL)** adopted (effective June 2017) — data localization for CIIOs; network security obligations |
| 2017 | CSL effective; first enforcement of data localization requirements |
| 2019 | GB/T 35273-2020 — Personal Information Security Specification (national standard; guidance) |
| 2020 | Civil Code (Book IV, Personality Rights) includes privacy and PI protection rights |
| 2020 | PIPL draft released for public comment (first and second readings) |
| 2021 | **Data Security Law (DSL)** adopted (effective September 2021) — data classification; important data |
| 2021 | **PIPL adopted** (August 20, 2021); **effective November 1, 2021** |
| 2022 | CAC issues: Security Assessment for Outbound Data Transfer; Standard Contract for Cross-Border Transfer |
| 2023 | Standard Contract mechanism effective (June 2023); Certification mechanism introduced; first enforcement actions |
| 2024 | Relaxation for some transfers (Provisions on Promoting and Regulating Cross-Border Data Flows); free trade zone exemptions; AI/algorithm regulations interaction |

### 1.2 China's "Three Pillars" of Data Governance

| Law | Focus | Effective |
|:---:|:---:|:---:|
| **Cybersecurity Law (CSL)** | Network security; critical information infrastructure (CII); data localization for CII operators | June 2017 |
| **Data Security Law (DSL)** | Data classification (core/important/general); national security; important data export restrictions | September 2021 |
| **Personal Information Protection Law (PIPL)** | Individual PI protection; consent; rights; cross-border PI transfer; processors/handlers | November 2021 |

Together, these three laws create a comprehensive data governance framework. A single data processing activity may be subject to ALL THREE simultaneously.

---

## Chapter 2 — PIPL Architecture & Structure

### 2.1 Document Structure

| Chapter | Articles | Content |
|:-------:|:--------:|---------|
| I | Art. 1-12 | General provisions (scope; definitions; principles; state role) |
| II | Art. 13-27 | Rules on PI processing (legal bases; consent; sensitive PI; automated decisions; publicly available PI) |
| III | Art. 28-32 | Special provisions: state organs processing PI |
| IV | Art. 33-43 | **Rules on cross-border provision of PI** |
| V | Art. 44-50 | Rights of individuals |
| VI | Art. 51-64 | Obligations of PI handlers |
| VII | Art. 65-70 | Responsibilities of departments performing PI protection duties |
| VIII | Art. 71-74 | Legal liabilities (penalties) |
| IX | Art. 75-76 | Supplementary provisions (definitions; effective date) |

### 2.2 Key Definitions

| Term (Chinese) | English | Definition |
|:---:|:---:|---|
| **个人信息 (gèrén xìnxī)** | Personal Information (PI) | Various kinds of information recorded electronically or otherwise relating to identified or identifiable natural persons; excluding anonymized information |
| **敏感个人信息 (mǐngǎn gèrén xìnxī)** | Sensitive Personal Information | PI that if leaked or illegally used may easily lead to infringement of human dignity or harm to personal/property safety (biometric; religious belief; specific identity; medical/health; financial; location tracking; minors' information) |
| **个人信息处理者 (gèrén xìnxī chǔlǐ zhě)** | Personal Information Handler (PI Handler) | Organization or individual that independently determines processing purposes, methods, etc. (≈ "controller" in GDPR) |
| **委托处理 (wěituō chǔlǐ)** | Entrusted Processing | Processing entrusted to another party (≈ "processor" in GDPR; Art. 21) |
| **同意 (tóngyì)** | Consent | Voluntary, explicit indication of intent by individual under conditions of full knowledge |
| **单独同意 (dāndú tóngyì)** | Separate Consent | Specific, individually obtained consent for particular processing (required for: sensitive PI; cross-border; public disclosure; image processing in public places) |
| **匿名化 (nìmíng huà)** | Anonymization | Processing so individual cannot be identified AND cannot be restored; anonymized info is NOT PI |
| **去标识化 (qù biāoshí huà)** | De-identification | Processing so PI cannot identify individual without using additional information |

### 2.3 Principles (Art. 5-9)

| Principle | Article | Content |
|:---------:|:-------:|---------|
| **Lawfulness, legitimacy, necessity, good faith** | Art. 5 | Processing must follow these principles |
| **Purpose limitation** | Art. 6 | Clear, reasonable purpose; directly related to processing purpose; minimal impact on individual rights |
| **Minimum necessary** | Art. 6 | Minimum scope necessary; no excessive collection |
| **Transparency** | Art. 7 | Open, transparent; inform individual of processing rules |
| **Accuracy** | Art. 8 | Ensure quality and accuracy; avoid adverse impact from inaccuracy |
| **Accountability** | Art. 9 | PI handler responsible for processing activities; take necessary measures |
| **Security** | Art. 9 | Ensure security of PI processed |

---

## Chapter 3 — Legal Bases for Processing (Art. 13)

### 3.1 Seven Legal Bases

| # | Legal Basis | Notes |
|:-:|:---:|---|
| 1 | **Consent of the individual** | Primary basis; must be voluntary, explicit, fully informed (Art. 14). Separate consent required for: sensitive PI; cross-border transfer; public disclosure |
| 2 | **Necessary for contract** | Necessary for concluding or performing a contract to which individual is a party; or HR management per labor rules/collective contract |
| 3 | **Statutory duty** | Necessary for performing statutory duties or obligations |
| 4 | **Public health emergency** | Necessary for responding to public health emergency or protecting safety of natural persons in emergency |
| 5 | **Public interest** | Processing within reasonable scope for news reporting, public opinion supervision, or other public interest activities |
| 6 | **Reasonable scope, already public** | Processing within reasonable scope of PI already made public by individual or otherwise lawfully disclosed |
| 7 | **Other circumstances** | Other circumstances provided by laws/administrative regulations |

**Notable: NO explicit "legitimate interest" basis** (unlike GDPR Art. 6(1)(f)). This is a significant difference — organizations cannot rely on self-assessed legitimate interest.

### 3.2 Consent Requirements

| Type | When Required | Standard |
|:---:|:---:|---|
| **General consent** | Default for consent-based processing | Voluntary; explicit; fully informed; specific purpose |
| **Separate consent (单独同意)** | (a) Sensitive PI processing. (b) Cross-border transfer. (c) Public disclosure of PI. (d) Processing images from public surveillance for non-security purposes. (e) Sharing PI with other handlers | Must be INDIVIDUALLY obtained (not bundled with other terms); specific to the particular processing |
| **Written consent** | Some sensitive PI processing (per regulation) | Written form (may include electronic signature) |
| **Parental/guardian consent** | Processing minors' PI (under 14) | Parent/guardian must provide consent |
| **Withdrawal** | At any time (Art. 15) | Handler must provide convenient mechanism; withdrawal does not affect prior lawful processing |

---

## Chapter 4 — Cross-Border Data Transfer (Art. 38-43)

### 4.1 Transfer Mechanisms

| Mechanism | Article | Applicability |
|:---------:|:-------:|:---|
| **CAC Security Assessment** | Art. 40 | MANDATORY for: (a) CII operators; (b) handlers processing PI of ≥1M individuals; (c) handlers who have cumulatively transferred PI of ≥100K individuals or sensitive PI of ≥10K individuals abroad since Jan 1 of previous year |
| **Standard Contract with CAC filing** | Art. 38(3) | For handlers NOT meeting security assessment thresholds; file with provincial CAC |
| **PI Protection Certification** | Art. 38(2) | Certification by institution recognized by CAC (GB/T certification scheme) |
| **International treaties/agreements** | Art. 38(1) | Where China has entered relevant agreements |
| **Free Trade Zone provisions** | 2024 relaxation | Negative list approach in FTZ areas |

### 4.2 Security Assessment (CAC)

| Aspect | Requirement |
|:------:|-------------|
| **Triggers** | ≥1M individuals' PI processed; ≥100K PI cumulative outbound; ≥10K sensitive PI cumulative outbound; CIIO |
| **Process** | Self-assessment → submit to provincial CAC → CAC review (45 working days; extendable by 15) → approval/rejection |
| **Assessment criteria** | (1) Legality, legitimacy, necessity. (2) Quantity, scope, type of outbound PI and risks. (3) Overseas recipient's obligations and ability to fulfill. (4) Risk of PI being infringed after transfer. (5) Whether contract adequately ensures rights |
| **Validity** | 2 years (must re-apply before expiry if transfer continues) |
| **Rejection** | If risks too high; must stop transfer; or restructure to eliminate need |

### 4.3 Standard Contract (2023)

| Aspect | Detail |
|:------:|--------|
| **Template** | CAC-issued standard contract (non-negotiable core terms; supplementary terms may be added but must not conflict) |
| **Filing** | File with provincial CAC within 10 working days of contract taking effect |
| **Content** | Purposes; types; scope; retention; overseas recipient obligations; individual rights; security measures; dispute resolution (Chinese courts/arbitration); CAC supervision rights |
| **Individual rights** | Individuals are THIRD-PARTY BENEFICIARIES of the standard contract (can enforce against overseas recipient) |

### 4.4 2024 Relaxations

| Change | Detail |
|:------:|--------|
| **Quantity thresholds raised** | Handlers of <1M individuals and cumulative transfer <100K (non-sensitive) or <10K (sensitive) since Jan 1 of prior year: no security assessment needed |
| **Free Trade Zone** | FTZs may apply "negative list" — data NOT on list can flow freely without any mechanism |
| **Exempt scenarios** | (a) Cross-border e-commerce (contract performance; delivery). (b) HR management of overseas employees (labor contract necessity). (c) Emergency (protect life/property) |
| **Overall direction** | Moving toward risk-based approach; reducing burden for lower-risk transfers |

---

## Chapter 5 — Sensitive Personal Information (Art. 28-32)

### 5.1 Categories

| Category | Examples |
|:--------:|---------|
| **Biometric** | Facial recognition; fingerprints; voiceprint; iris; gait |
| **Religious belief** | Religious affiliation; practices |
| **Specific identity** | Race; ethnicity; specific social/political identities |
| **Medical health** | Medical records; health examination; disease diagnosis; treatment |
| **Financial accounts** | Bank accounts; financial transactions; credit information |
| **Location tracking** | Real-time location; historical location tracks; trajectory |
| **Minors' information** | ALL PI of persons under 14 years old is sensitive PI |

### 5.2 Special Requirements for Sensitive PI

| Requirement | Detail |
|:-----------:|--------|
| **Specific purpose** | Must have specific purpose and sufficient necessity |
| **Separate consent** | Must obtain individual's SEPARATE CONSENT (单独同意) |
| **PIPIA** | Must conduct Personal Information Protection Impact Assessment BEFORE processing |
| **Notification** | Must inform individual: necessity of processing; impact on individual's rights (in addition to standard notice items) |
| **Retention minimization** | Retain for shortest period necessary |
| **Minors** | If under 14: parental/guardian consent required; dedicated PI processing rules |

---

## Chapter 6 — Individual Rights (Art. 44-50)

### 6.1 Rights Catalog

| Right | Article | Description |
|:-----:|:-------:|-------------|
| **Right to know** | Art. 44 | Right to know about processing (informed via notice) |
| **Right to decide** | Art. 44 | Right to make decisions on their PI processing; right to restrict/refuse (except as provided by law) |
| **Right to access/copy** | Art. 45 | Request access to their PI; obtain copy |
| **Right to portability** | Art. 45 | Request transfer to designated handler (where conditions provided by CAC are met) |
| **Right to correction** | Art. 46 | Request correction of inaccurate/incomplete PI |
| **Right to deletion** | Art. 47 | Request deletion when: (a) purpose achieved/unachievable; (b) product/service discontinued; (c) retention period expired; (d) consent withdrawn; (e) handler violated law/agreement |
| **Right to explanation** | Art. 48 | Request explanation of PI processing rules |
| **Right re: deceased** | Art. 49 | Close relatives may exercise rights of deceased (for their lawful interests) — unless deceased arranged otherwise |

### 6.2 Automated Decision-Making (Art. 24)

| Aspect | Requirement |
|:------:|-------------|
| **Transparency** | Must ensure transparency of decision-making |
| **Fairness** | Must not impose unreasonable differential treatment (discrimination) in trading conditions (pricing; etc.) |
| **Opt-out** | If making decisions that significantly affect individual rights → must provide mechanism for individual to opt-out of AUTOMATED decision |
| **Marketing** | Push information/commercial marketing via automated decisions → must provide opt-out + non-targeted option |
| **Human review** | Individual has right to request explanation and demand handler refuse solely automated decisions |

---

## Chapter 7 — Obligations of PI Handlers (Art. 51-59)

### 7.1 Core Obligations

| Obligation | Article | Description |
|:---:|:---:|---|
| **Formulate internal rules** | Art. 51 | Internal management systems; operating procedures for PI processing |
| **Classification management** | Art. 51 | Classify PI; apply differentiated management |
| **Technical measures** | Art. 51 | Encryption; de-identification; access control; security measures |
| **Determine authority** | Art. 51 | Set access authorities for processing operations; provide regular security training |
| **Incident response** | Art. 51 | Emergency response plan for PI security incidents |
| **Periodic compliance audit** | Art. 54 | Regular compliance audits (may be conducted by professional institution) |
| **PIPIA** | Art. 55-56 | Personal Information Protection Impact Assessment before: sensitive PI processing; automated decision-making; entrustment to another; cross-border provision; public disclosure; processing significantly affecting individuals |
| **Breach notification** | Art. 57 | Immediately take remedial measures; notify supervising department; notify affected individuals (unless measures effectively avoid harm — but supervisor may order notification) |
| **Designate responsible person** | Art. 52 | Handlers processing PI above CAC-specified quantity must designate PI protection responsible person; publish their contact |
| **Overseas handler's representative** | Art. 53 | Foreign handler subject to PIPL must establish dedicated institution or appoint representative in China; file with supervising department |

### 7.2 PIPIA (Impact Assessment) Requirements

| When Required | Assessment Content |
|:---:|---|
| Processing sensitive PI | (1) Whether processing purpose is lawful, legitimate, necessary |
| Automated decision-making using PI | (2) Impact on individuals' rights and degree of impact |
| Entrustment; sharing; disclosure | (3) Whether protective measures are lawful, effective, proportionate to risk |
| Cross-border transfer | (4) Risk assessment results |
| Significantly affecting individual rights | Content must be retained for at least 3 years |

---

## Chapter 8 — Penalties & Enforcement (Art. 66-71)

### 8.1 Penalty Framework

| Violation Level | Penalty |
|:---:|---|
| **General violations** | Order to correct; warning; confiscate illegal gains; order app to suspend/terminate service. Fine: up to ¥1M for handler; ¥10K-¥100K for directly responsible personnel |
| **Serious violations** (refuse to correct; or severe circumstances) | Fine: up to ¥50M OR up to 5% of prior year's annual revenue; suspend relevant business operations; order to suspend business for rectification; revoke business permit/license. Directly responsible: ¥100K-¥1M; may be prohibited from serving as director, supervisor, senior manager, or PI protection officer for certain period |
| **Criminal liability** | Where violations constitute crime → criminal investigation (per Criminal Law) |
| **Civil liability** | Infringement of PI rights → civil compensation. If multiple handlers/recipients bear fault → joint and several liability. Where PI handler cannot prove no fault → presumed at fault (REVERSED BURDEN OF PROOF) |

### 8.2 Notable Enforcement

| Year | Action | Detail |
|:----:|:------:|--------|
| 2021 | DiDi (ride-hailing) | Suspended from app stores; investigated for illegal data collection; massive fine (¥8.026B / ~$1.2B) in 2022 |
| 2022 | DiDi final | ¥8.026 billion fine (CSL + DSL + PIPL violations); executives fined ¥1M each |
| 2023 | Various apps | Removed from stores for excessive collection; forced rectification; public naming |
| 2023 | CNKI (academic) | Fined ¥50M for PI violations (combining CSL + PIPL) |
| 2024 | Ongoing | Increased enforcement of cross-border transfer rules; app store compliance sweeps |

---

## Chapter 9 — Architecture Diagrams

### 9.1 China's Three-Pillar Data Governance

```mermaid
graph TB
    subgraph "China Data Governance Framework"
        CSL[Cybersecurity Law 2017<br/>━━━━━━━━━<br/>• Network operators obligations<br/>• CII operators data localization<br/>• Real-name registration<br/>• Security reviews<br/>• Incident reporting]
        
        DSL[Data Security Law 2021<br/>━━━━━━━━━<br/>• Data classification (core/important/general)<br/>• Important data catalog<br/>• National security review<br/>• Data export restrictions<br/>• Government data sharing]
        
        PIPL_D[PIPL 2021<br/>━━━━━━━━━<br/>• Personal information protection<br/>• Individual consent/rights<br/>• Cross-border PI transfer<br/>• Handler obligations<br/>• Penalties (5% revenue)]
    end
    
    CSL --> |"Network + CII"| OVERLAP[Overlap: Data processing<br/>by network operators<br/>involving personal<br/>information of Chinese<br/>individuals must comply<br/>with ALL THREE]
    DSL --> |"All data types"| OVERLAP
    PIPL_D --> |"Personal info"| OVERLAP
```

### 9.2 Cross-Border Transfer Decision Tree

```mermaid
graph TB
    subgraph "PIPL Cross-Border Transfer Decision"
        Q1{Are you a CII Operator?}
        
        Q2{Do you process PI of<br/>≥1M individuals?}
        
        Q3{Cumulative outbound since Jan 1:<br/>≥100K PI or ≥10K sensitive PI?}
        
        SA[CAC Security Assessment<br/>━━━━━━━━━<br/>MANDATORY<br/>• Self-assessment<br/>• Submit to CAC<br/>• 45+15 working days<br/>• Valid 2 years]
        
        Q4{Choose mechanism:}
        
        SC[Standard Contract<br/>━━━━━━━━━<br/>• CAC template<br/>• File with provincial CAC<br/>• Within 10 working days<br/>• Individual = third-party beneficiary]
        
        CERT[PI Protection Certification<br/>━━━━━━━━━<br/>• Recognized institution<br/>• GB/T standards<br/>• Periodic renewal]
        
        EXEMPT{2024 exemptions apply?<br/>(e-commerce; HR; emergency;<br/>FTZ negative list)}
        
        FREE[Transfer without mechanism<br/>(exemption applies)]
    end
    
    Q1 -->|"Yes"| SA
    Q1 -->|"No"| Q2
    Q2 -->|"Yes"| SA
    Q2 -->|"No"| Q3
    Q3 -->|"Yes"| SA
    Q3 -->|"No"| EXEMPT
    EXEMPT -->|"Yes"| FREE
    EXEMPT -->|"No"| Q4
    Q4 --> SC
    Q4 --> CERT
```

---

## Chapter 10 — Case Studies

### 10.1 Multinational Technology Company: China Data Localization + Cross-Border

| Aspect | Detail |
|--------|--------|
| **Company** | US technology company; cloud services; 5M Chinese users; needs to serve China market while transferring some data to US for product improvement |
| **Regulatory situation** | Processes >1M individuals' PI → MANDATORY CAC security assessment for any outbound transfer |
| **Architecture** | |

| Component | Implementation |
|:---:|---|
| **Data localization** | Primary data center in China (mainland); ALL personal information stored locally; local encryption keys |
| **Minimized transfer** | Only transfer: aggregated analytics (anonymized where possible); model training outputs (not raw PI); necessary support data (with separate consent) |
| **Security assessment** | Filed CAC security assessment for limited transfer: (a) documented necessity; (b) described PI types transferred; (c) risk mitigation measures; (d) overseas recipient obligations |
| **Separate consent** | For any PI transferred abroad: obtained separate consent (单独同意) from each user; clear disclosure in Chinese: "your [specific data] will be transferred to [US company name] in [United States] for [specific purpose]" |
| **Local team** | Appointed Chinese-based PI protection responsible person; established local institution for PIPL compliance; registered with CAC |
| **PIPIA** | Conducted impact assessment for cross-border transfer; retained for 3+ years; documented necessity and proportionality |
| **Ongoing monitoring** | Quarterly review of transfer volumes (ensure staying within assessment scope); annual re-assessment before 2-year expiry |

### 10.2 Chinese Company: Automated Decision-Making Compliance

| Aspect | Detail |
|--------|--------|
| **Company** | Chinese e-commerce platform; algorithmic pricing + recommendation; 50M users |
| **PIPL compliance issues** | (1) Personalized pricing (different prices for different users based on profiling). (2) Algorithmic recommendations (content; products). (3) No opt-out mechanism existed. |
| **Violations found** | Art. 24: (a) no transparency about automated decision-making logic; (b) price discrimination via algorithm (unreasonable differential treatment); (c) no opt-out of personalized recommendations |
| **Remediation** | |

| Action | Implementation |
|:---:|---|
| **Transparency** | Published "algorithm description" in app: "We use your browsing history and purchase patterns to recommend products and determine pricing adjustments." |
| **Fair treatment** | Removed user-specific price manipulation; pricing may differ by: objective factors (location-based shipping; bulk discount); NOT by individual's perceived willingness-to-pay |
| **Opt-out** | Added prominent toggle: "Turn off personalized recommendations" — shows non-personalized generic feed. Added: "Turn off algorithmic pricing" — shows standard price (same for all users) |
| **Right to explanation** | User can tap "Why was this recommended?" and receive simplified explanation. User can request human review of any automated decision affecting their rights (e.g., account restriction; credit limit) |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What are the key differences between PIPL and GDPR regarding consent?

**A:**

| Aspect | PIPL (China) | GDPR (EU) |
|:------:|:---:|:---:|
| **Standard consent** | Voluntary; explicit; fully informed (similar to GDPR) | Freely given; specific; informed; unambiguous |
| **"Separate consent" (单独同意)** | PIPL-specific: required for sensitive PI; cross-border; public disclosure; sharing. Must be INDIVIDUALLY obtained (not bundled) | GDPR: "explicit consent" for Art. 9 (sensitive); otherwise standard consent |
| **Legitimate interest** | **NO legitimate interest basis in PIPL** | Available (Art. 6(1)(f)) — major difference |
| **Withdrawal** | Must be easy; prior processing remains valid | Same approach |
| **Children** | Under 14: parental consent required (ALL minors' PI is "sensitive") | Under 16 (varies by state): parental consent for ISS |
| **Bundling** | Not explicitly prohibited but "separate consent" requirements effectively prevent bundling for sensitive/cross-border | Explicitly: consent cannot be bundled with other T&C (Art. 7(4)) |

**Critical point:** Absence of "legitimate interest" basis in PIPL means many activities that rely on legitimate interest under GDPR (fraud prevention; direct marketing; network security) must find another legal basis in China — typically "necessary for contract" or "statutory duty," or must rely on consent.

### Tier 2: Mid-Level

**Q2:** Explain the three cross-border transfer mechanisms under PIPL and when each is required versus optional.

**A:**

| Mechanism | When MANDATORY | When OPTIONAL |
|:---:|:---|:---|
| **1. CAC Security Assessment** | MANDATORY when: (a) CIIO transferring PI abroad; (b) Handler processes ≥1M individuals' PI; (c) Cumulative outbound ≥100K PI or ≥10K sensitive PI since Jan 1 | Never optional when thresholds met — MUST complete assessment before any transfer |
| **2. Standard Contract (filed with CAC)** | Not mandatory per se; it's one of the CHOICES when security assessment is NOT required | Available for handlers BELOW security assessment thresholds; must file contract with provincial CAC within 10 working days |
| **3. PI Protection Certification** | Not mandatory; alternative to standard contract | Available when security assessment not required; obtained from recognized certification body |

**Additional requirements (ALL mechanisms):**
- Separate consent from individual (informed about: overseas recipient; purpose; types; rights)
- PIPIA conducted (cross-border = automatic trigger)
- Individual informed of: overseas recipient name; contact; processing purpose; PI types; method for exercising rights with overseas recipient

**2024 relaxation:** Some scenarios exempt from ALL mechanisms (e-commerce delivery; HR cross-border; emergency; FTZ negative list data).

### Tier 3: Senior

**Q3:** Design a compliance architecture for a company that is simultaneously a CIIO (Critical Information Infrastructure Operator), processes >5M Chinese individuals' PI, and operates globally across EU, US, and China.

**A:**

| Layer | Architecture |
|:---:|---|
| **Data sovereignty** | THREE data environments: (1) China mainland (primary; all PI stored locally); (2) EU (GDPR-compliant; separate from China); (3) US (residual). China PI NEVER leaves China except through assessed pathway. EU PI NEVER goes to China without Art. 49 derogation or adequacy (none exists). |
| **China-specific** | |

| Obligation | Implementation |
|:---:|---|
| **CIIO data localization** | ALL personal information and "important data" stored in mainland China. No exceptions. If MUST transfer: CAC security assessment mandatory (regardless of volume). |
| **CAC security assessment** | CIIO status = automatic security assessment. File for EACH category of outbound transfer. Assessment scope: specific purposes; specific data types; specific recipients. Re-file every 2 years. |
| **Important data** | Separately classify per DSL: important data catalog (per industry regulator). Important data: additional restrictions beyond PI (may require national security review). |
| **PI protection responsible person** | Senior executive level; Chinese-based; published contact; registered with CAC. Reports directly to board-equivalent on PI compliance. |
| **Annual compliance audit** | Engage professional institution (China-based auditor) for annual PI compliance audit per Art. 54. Report to supervising department if required. |
| **Separate consent** | For any remaining outbound transfer (assessed by CAC): individual-level separate consent with: (a) recipient name + contact; (b) purpose of transfer; (c) PI types; (d) method to exercise rights with foreign recipient; (e) country/region; (f) country's PI protection level. |
| **PIPIA** | Before: each category of sensitive PI processing; each cross-border transfer; each automated decision system; each public disclosure. Content: necessity; individual impact; security measures; risk level. Retain 3+ years. |
| **Algorithm compliance** | If using automated decisions affecting individuals (pricing; recommendations; risk scoring): (a) algorithm registration with CAC (if meeting threshold); (b) transparency; (c) opt-out; (d) no discrimination; (e) human review available. |
| **Global coordination** | China legal entity operates independently for PI decisions (cannot be directed by US HQ to transfer PI without assessment approval). EU entity operates under GDPR. Data sharing between regions: only through compliant pathways (assessed transfers China→outside; SCCs EU→outside). |

---

## Chapter 12 — Cheat Sheet & Quick Reference

```
═══════════════════════════════════════════
CHINA PIPL — QUICK REFERENCE
═══════════════════════════════════════════

LAW: Personal Information Protection Law (PIPL)
EFFECTIVE: November 1, 2021
AUTHORITY: CAC (Cyberspace Administration of China)
           + Ministry of Public Security + sector regulators
CONTEXT: Third of "Three Pillars" (CSL + DSL + PIPL)

═══════════════════════════════════════════
LEGAL BASES (Art. 13) — 7 BASES:
  1. Consent (primary; separate consent for sensitive/cross-border)
  2. Necessary for contract
  3. Statutory duty/obligation
  4. Public health emergency
  5. Public interest (news; public supervision)
  6. Already lawfully public (reasonable scope)
  7. Other (per laws/regulations)
  
  ★ NO "LEGITIMATE INTEREST" BASIS ★
  (Major difference from GDPR)

═══════════════════════════════════════════
SENSITIVE PI (Art. 28):
  • Biometric data
  • Religious belief
  • Specific identity (race; ethnicity)
  • Medical/health
  • Financial accounts
  • Location tracking
  • ALL PI of minors under 14
  
  Requirements: separate consent + PIPIA + specific purpose
  + inform of necessity and impact

═══════════════════════════════════════════
SEPARATE CONSENT (单独同意) — REQUIRED FOR:
  • Processing sensitive PI
  • Cross-border transfer of PI
  • Public disclosure of PI
  • Sharing PI with other handlers
  • Public place image processing (non-security purpose)
  
  Must be INDIVIDUALLY obtained (not bundled)

═══════════════════════════════════════════
CROSS-BORDER TRANSFER (Art. 38):
  
  MANDATORY Security Assessment when:
    • CIIO operator
    • Process ≥1M individuals' PI
    • Cumulative outbound ≥100K PI or ≥10K sensitive PI
  
  Otherwise choose:
    • Standard Contract (file with CAC within 10 days)
    • PI Protection Certification
  
  ALL transfers require:
    • Separate consent (informed about overseas recipient)
    • PIPIA conducted
    • Individual informed of rights exercise method

═══════════════════════════════════════════
PENALTIES (Art. 66):
  General: up to ¥1M (handler); ¥10K-100K (individual)
  Serious: up to ¥50M OR 5% annual revenue
          + suspend/revoke business license
          + directors/officers: ¥100K-1M + possible ban
  
  DiDi precedent: ¥8.026B ($1.2B USD)

═══════════════════════════════════════════
INDIVIDUAL RIGHTS (Art. 44-50):
  • Right to know and decide
  • Access and copy
  • Portability (conditions per CAC)
  • Correction
  • Deletion
  • Explanation of processing rules
  • Rights of deceased (close relatives)

═══════════════════════════════════════════
HANDLER OBLIGATIONS:
  • Internal rules and classification
  • Security measures (encryption; access control)
  • Incident response plan
  • Breach notification (immediately to authority + individuals)
  • PIPIA (sensitive; automated; cross-border; etc.)
  • Compliance audit (regular; may be by professional body)
  • Designate PI protection responsible person (large handlers)
  • Foreign handler: establish China institution or representative

═══════════════════════════════════════════
KEY DIFFERENCES FROM GDPR:
  • No legitimate interest basis
  • "Separate consent" (stricter than GDPR explicit consent)
  • Security assessment for cross-border (government approval)
  • Reversed burden of proof (handler presumed at fault)
  • 5% revenue penalty (vs. GDPR 4%)
  • ALL minors' data (<14) is sensitive PI
  • Extraterritorial + China representative required
  • Interacts with CSL + DSL (three-law framework)
```

---

*End of Document — 08_China_PIPL.md*
