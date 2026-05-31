# IEC 62443 — Industrial Automation & Control Systems Security (Full Series)

**Topic:** IEC 62443 (ISA/IEC 62443) — Complete Series Analysis  
**Standards:** IEC 62443-1-1 through 62443-4-2 (all parts), ISA-99 legacy  
**SDO:** IEC TC 65/WG 10, ISA (International Society of Automation)  
**Audience:** OT security architects, ICS engineers, product developers, system integrators, asset owners  
**Prerequisites:** Network security fundamentals, ICS/SCADA awareness, risk assessment concepts

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Evolution Timeline

| Year | Event |
|------|-------|
| 2002 | ISA-99 committee formed (ISA Security for IACS) |
| 2007 | ISA-99.00.01 published (first ICS security standard) |
| 2009 | IEC 62443-1-1 (concepts) + IEC 62443-3-1 (technologies) |
| 2010 | Stuxnet — validates need for ICS security standard |
| 2010 | ISA-99.02.01 → IEC 62443-2-1 (CSMS) |
| 2013 | IEC 62443-3-3 (system security requirements — SL1-4) |
| 2015 | IEC 62443-2-4 (service provider requirements) |
| 2015 | IEC 62443-2-3 (patch management) |
| 2018 | IEC 62443-4-1 (secure development lifecycle — SDL) |
| 2019 | IEC 62443-4-2 (component technical requirements) |
| 2020 | IEC 62443-3-2 (security risk assessment) |
| 2022 | EU Cyber Resilience Act references IEC 62443 |
| 2023 | NIS2 Directive recognizes IEC 62443 for OT |
| 2024 | IEC 62443-2-1 Ed2 revision (cloud, remote access) |

### 1.2 Why IEC 62443 Was Needed

| Problem | Impact |
|---------|--------|
| No OT-specific security standard | IT standards (ISO 27001) don't address availability-first OT |
| Fieldbus protocols have no security | Modbus, PROFIBUS designed without authentication |
| 15-30 year lifecycles | Cannot patch like IT; need compensating controls |
| Safety vs security conflict | Security updates can disrupt safety-certified systems |
| Multiple stakeholders | Asset owners, integrators, product vendors need different guidance |
| Threat escalation | Stuxnet, TRITON, Industroyer prove nation-state threats to ICS |

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 IEC 62443 Series Organization

```mermaid
graph TB
    subgraph "Series 1: General"
        S11[1-1: Concepts & Models<br/>(terminology, architecture)]
        S12[1-2: Master Glossary<br/>(definitions)]
        S13[1-3: System Security<br/>Conformance Metrics]
        S14[1-4: IACS Security<br/>Lifecycle & Use Cases]
        S15[1-5: IACS Security<br/>Scheme Assurance]
    end
    
    subgraph "Series 2: Policies & Procedures"
        S21[2-1: CSMS Requirements<br/>(asset owner)]
        S22[2-2: IACS Protection<br/>Rating]
        S23[2-3: Patch Management<br/>in IACS]
        S24[2-4: Service Provider<br/>Requirements]
    end
    
    subgraph "Series 3: System"
        S31[3-1: Security Technologies<br/>(catalog)]
        S32[3-2: Security Risk<br/>Assessment for Design]
        S33[3-3: System Security<br/>Requirements (SL)]
    end
    
    subgraph "Series 4: Component"
        S41[4-1: Secure Product<br/>Development Lifecycle]
        S42[4-2: Technical Security<br/>Requirements (Components)]
    end
    
    S11 --> S21
    S11 --> S31
    S11 --> S41
    S21 --> S24
    S31 --> S32
    S32 --> S33
    S41 --> S42
```

### 2.2 Stakeholder Mapping

| Stakeholder | Primary Standards | Role |
|-------------|------------------|------|
| Asset Owner | 2-1 (CSMS), 3-2 (risk assessment), 2-3 (patch) | Operates the plant; defines security requirements |
| System Integrator | 2-4 (service provider), 3-3 (system), 3-2 (design) | Designs & builds the system; implements zones/conduits |
| Product Supplier | 4-1 (SDL), 4-2 (component requirements) | Builds PLCs, HMIs, switches; embeds security |
| Maintenance Provider | 2-4 (service), 2-3 (patch management) | Maintains system; performs updates |
| Auditor/Certifier | 3-3 (system assessment), 4-2 (component assessment) | Evaluates compliance; issues certificates |

---

## Chapter 3 — Technical Deep Dive

### 3.1 Security Levels (SL)

| Level | Threat Actor | Capability | Example |
|-------|------------|-----------|---------|
| SL 0 | None | No security needed | Isolated demo system |
| SL 1 | Casual/unintentional | Basic means, low motivation | Accidental misconfiguration |
| SL 2 | Intentional (generic) | Moderate resources, specific intent | Disgruntled employee, hacktivist |
| SL 3 | Intentional (IACS-specific) | Sophisticated, IACS knowledge | Organized crime, competitor espionage |
| SL 4 | State-sponsored | Extensive resources, nation-state | APT (Stuxnet, TRITON, Volt Typhoon) |

### 3.2 Security Level Types

| Type | Definition | Purpose |
|------|-----------|---------|
| SL-T (Target) | Required security level for a zone | Defined by asset owner (risk assessment) |
| SL-C (Capability) | Security level a system CAN provide | Defined by integrator/supplier |
| SL-A (Achieved) | Actual security level MEASURED in operation | Verified by auditor |

**Compliance criterion:** SL-A ≥ SL-T (achieved must meet or exceed target)

### 3.3 Foundational Requirements (FR)

| FR | Name | Description |
|----|------|-------------|
| FR 1 | Identification & Authentication Control | Who/what is accessing the system |
| FR 2 | Use Control | Authorization — what they can do |
| FR 3 | System Integrity | Protection from unauthorized changes |
| FR 4 | Data Confidentiality | Protection of information from disclosure |
| FR 5 | Restricted Data Flow | Network segmentation, zones & conduits |
| FR 6 | Timely Response to Events | Monitoring, detection, alerting |
| FR 7 | Resource Availability | Resilience against denial of service |

### 3.4 IEC 62443-4-1 — Secure Development Lifecycle (SDL)

| Practice Area | Requirements |
|---------------|-------------|
| SM (Security Management) | Security organization, training, process |
| SR (Specification of Security Requirements) | Threat modeling, security requirements |
| SD (Secure by Design) | Defense-in-depth, least privilege, attack surface |
| SI (Secure Implementation) | Coding standards, static analysis, peer review |
| SVV (Security Verification & Validation) | Penetration testing, fuzz testing, vulnerability scanning |
| DM (Defect Management) | Vulnerability handling, CVE process, patching |
| SUM (Security Update Management) | Update delivery, integrity, rollback |
| SG (Security Guidelines) | Documentation for secure deployment |

**Maturity Levels (4-1):**

| Level | Name | Characteristic |
|-------|------|----------------|
| 1 | Initial | Ad-hoc, reactive security |
| 2 | Managed | Documented processes, basic implementation |
| 3 | Defined | Consistent, organization-wide, proactive |
| 4 | Improving | Continuous improvement, metrics-driven |

### 3.5 IEC 62443-4-2 — Component Requirements

| CR (Component Requirement) | SL 1 | SL 2 | SL 3 | SL 4 |
|---------------------------|-------|-------|-------|-------|
| CR 1.1 (Human user ID & auth) | Username/password | Unique accounts | MFA | MFA + certificate |
| CR 1.2 (Software process ID) | — | Process identity | Cryptographic identity | Mutual authentication |
| CR 2.1 (Authorization enforcement) | Basic roles | Role-based (RBAC) | Permission-based | Attribute-based |
| CR 3.3 (Security functionality verification) | — | Integrity check at boot | Secure boot chain | Hardware root of trust |
| CR 4.1 (Information confidentiality) | — | Encryption at rest | Strong encryption | Validated cryptography |
| CR 5.1 (Network segmentation) | — | Logical segmentation | Physical segmentation | Complete isolation option |
| CR 6.1 (Audit log accessibility) | Local logs | Protected logs | Tamper-evident logs | Centralized + integrity |
| CR 7.1 (DoS protection) | — | Basic limits | Rate limiting + monitoring | Redundancy + failover |

### 3.6 Zones and Conduits (IEC 62443-3-2)

| Concept | Definition |
|---------|-----------|
| Zone | Grouping of assets with the same security requirements (same SL-T) |
| Conduit | Controlled communication path between zones |
| Channel | Logical communication link within a conduit |
| Security Level-Target (SL-T) | Assigned to each zone based on risk assessment |

**Zone definition criteria:**
1. Logical grouping (same function)
2. Physical location (same area)
3. Security requirements (same SL-T)
4. Communication requirements (what talks to what)
5. Consequence of compromise (same impact)

---

## Chapter 4 — Implementation Guide

### 4.1 Risk Assessment Process (IEC 62443-3-2)

```mermaid
graph TB
    subgraph "ZCR Process"
        SCOPE[1. Define System Under<br/>Consideration (SUC)]
        ASSESS[2. Initial Risk Assessment<br/>(high-level threats)]
        ZONE[3. Partition into<br/>Zones & Conduits]
        SLT[4. Assign SL-T<br/>to each zone]
        DETAIL[5. Detailed Risk Assessment<br/>(per zone)]
        COUNTER[6. Define Countermeasures<br/>(technical + procedural)]
        RESIDUAL[7. Document Residual Risk<br/>(accept or mitigate further)]
    end
    
    SCOPE --> ASSESS --> ZONE --> SLT --> DETAIL --> COUNTER --> RESIDUAL
    RESIDUAL -->|"Risk unacceptable"| COUNTER
```

### 4.2 Implementation by Stakeholder

**Asset Owner (Series 2):**
1. Establish CSMS (IEC 62443-2-1) — policies, procedures, organization
2. Conduct risk assessment (IEC 62443-3-2) — zones, SL-T
3. Define procurement requirements (reference SL for purchased equipment)
4. Implement patch management process (IEC 62443-2-3)
5. Manage service providers (IEC 62443-2-4 compliance)

**System Integrator (Series 3):**
1. Design zones & conduits per 62443-3-2
2. Select components meeting SL requirements (verified per 62443-4-2)
3. Implement system security per 62443-3-3 (FR1-FR7)
4. Document security architecture and test results
5. Provide security maintenance procedures

**Product Supplier (Series 4):**
1. Implement SDL per 62443-4-1 (maturity level 2+ minimum)
2. Design component per 62443-4-2 (target SL)
3. Perform threat modeling + penetration testing
4. Provide security hardening guidelines
5. Maintain vulnerability response process (CVE)

### 4.3 Patch Management (IEC 62443-2-3)

| Phase | Activities |
|-------|-----------|
| Identification | Monitor vendor advisories, CVEs, ICS-CERT |
| Evaluation | Assess relevance, severity (CVSS), compatibility with OT |
| Planning | Schedule during maintenance window; define rollback plan |
| Testing | Test patch on representative system (NOT production) |
| Deployment | Apply during scheduled outage; monitor for issues |
| Verification | Confirm patch applied, system operational, security improved |
| Documentation | Record all patch actions, deviations, compensating controls |

---

## Chapter 5 — Certification & Audit

### 5.1 ISASecure Certification Programs

| Program | Standard | Scope |
|---------|----------|-------|
| EDSA (Embedded Device) | IEC 62443-4-2 | PLC, RTU, HMI, switch (component) |
| SDLA (Development Lifecycle) | IEC 62443-4-1 | Vendor development process |
| SSA (System Security) | IEC 62443-3-3 | Complete system (integrator) |
| CSA (Cybersecurity Assessment) | IEC 62443-4-1 + 4-2 | Combined process + product |

### 5.2 Certification Bodies

| Body | Region | Programs |
|------|--------|----------|
| ISASecure (ISCI) | Global | EDSA, SDLA, SSA |
| TÜV SÜD | Europe/Global | IEC 62443 all parts |
| TÜV Rheinland | Europe/Global | IEC 62443 certification |
| Exida | Global | IEC 62443-4-1, 4-2 |
| Bureau Veritas | Global | IEC 62443 certification |
| UL (2900 + 62443) | North America | Component + system |

### 5.3 Audit Evidence Requirements

| FR | Evidence for SL 2 |
|----|-------------------|
| FR 1 (IAC) | Unique accounts, password policy, account lockout configured |
| FR 2 (UC) | Role definitions documented, least privilege verified |
| FR 3 (SI) | Integrity monitoring active, baseline documented |
| FR 4 (DC) | Encryption of sensitive data, key management |
| FR 5 (RDF) | Zone diagram, firewall rules, conduit documentation |
| FR 6 (TRE) | Logs collected, retention policy, alert procedures |
| FR 7 (RA) | Backup procedures, recovery testing, redundancy design |

---

## Chapter 6 — Regional & Domain Variants

| Region/Domain | Mapping |
|---------------|---------|
| EU: NIS2 Directive | Recognizes IEC 62443 for essential entity OT compliance |
| EU: Cyber Resilience Act | IEC 62443-4-1/4-2 as harmonized standard (proposed) |
| EU: Machine Regulation 2023/1230 | References IEC 62443 for connected machinery security |
| Germany: IT-Sicherheitsgesetz 2.0 | BSI technical guidelines reference 62443 |
| USA: NIST CSF | Maps to IEC 62443 (NIST SP 800-82 references it) |
| USA: TSA Pipeline Security | References IEC 62443 for pipeline OT |
| China: GB/T 22239 (MLPS 2.0) | Chinese ICS security maps to IEC 62443 concepts |
| Process industry (NAMUR) | NAMUR NA 163 (security requirements) references 62443 |
| Water | AWWA Cybersecurity Guidance references 62443 |
| Manufacturing | ISA/IEC 62443 is primary standard |
| Oil & Gas | IEC 62443 + API 1164 (pipelines) |

---

## Chapter 7 — Comparison with Other Security Standards

| Dimension | IEC 62443 | ISO 27001 | NIST CSF | IEC 61511 (SIS Security) |
|-----------|-----------|-----------|----------|--------------------------|
| Scope | OT/ICS specific | IT (general information security) | All sectors (risk framework) | Safety instrumented systems |
| Focus | Availability + Integrity | Confidentiality first | Risk management framework | Safety + security interaction |
| Levels | SL 1-4 (technical) + Maturity 1-4 (process) | Controls (Annex A) | Tiers 1-4 (organizational) | SIL 1-4 (safety) |
| Stakeholders | Asset owner, integrator, supplier | Organization (any) | Organization (any) | SIS operator |
| Lifecycle | 15-30 years (OT) | 3-5 years (IT) | Not prescriptive | 15-30 years (SIS) |
| Patching | Explicit standard (2-3) | General control | Mentioned in Protect | Must not compromise safety |
| Certification | ISASecure, TÜV | ISO 27001 certificate | No formal certification | IEC 61511 assessment |
| Real-time | Addresses determinism | Not applicable | Not applicable | Safety timing requirements |
| Legacy | Explicitly addresses | Assumes modern systems | Not prescriptive | Addresses installed base |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 IEC 62443 Zone & Conduit Example

```mermaid
graph TB
    subgraph "Zone: Enterprise (SL-T: 2)"
        ERP[ERP Server]
        EMAIL[Email Server]
    end
    
    subgraph "Conduit: Enterprise↔DMZ"
        FW1[Firewall<br/>Application-layer inspection]
    end
    
    subgraph "Zone: Industrial DMZ (SL-T: 3)"
        HIST[Historian Mirror]
        PATCH[Patch Server]
        JUMP[Jump Host<br/>(MFA required)]
        OPCUA_GW[OPC UA Gateway<br/>(aggregation)]
    end
    
    subgraph "Conduit: DMZ↔Control"
        FW2[Industrial Firewall<br/>OT-aware DPI]
    end
    
    subgraph "Zone: Control (SL-T: 3)"
        PLC_Z[PLCs / DCS]
        HMI_Z[HMI / SCADA]
        ENG[Engineering<br/>Workstation]
    end
    
    subgraph "Conduit: Control↔Safety"
        FW3[Unidirectional Gateway<br/>Data diode]
    end
    
    subgraph "Zone: Safety (SL-T: 4)"
        SIS[Safety Instrumented<br/>System (SIS)]
    end
    
    ERP --> FW1
    EMAIL --> FW1
    FW1 --> HIST
    FW1 --> JUMP
    HIST --> FW2
    OPCUA_GW --> FW2
    FW2 --> PLC_Z
    FW2 --> HMI_Z
    PLC_Z --> FW3
    FW3 --> SIS
```

### 8.2 IEC 62443-4-1 SDL Process

```mermaid
graph LR
    subgraph "Secure Development Lifecycle"
        SM[Security<br/>Management<br/>(SM)]
        SR[Security<br/>Requirements<br/>(SR)]
        SD[Secure<br/>Design<br/>(SD)]
        SI[Secure<br/>Implementation<br/>(SI)]
        SVV[Security V&V<br/>Testing<br/>(SVV)]
        DM[Defect<br/>Management<br/>(DM)]
        SUM[Security Update<br/>Management<br/>(SUM)]
        SG[Security<br/>Guidelines<br/>(SG)]
    end
    
    SM --> SR --> SD --> SI --> SVV
    SVV --> DM
    DM --> SUM
    SD --> SG
```

---

## Chapter 9 — Case Studies & Failure Analysis

### 9.1 Pharmaceutical Plant — IEC 62443 Implementation

| Aspect | Detail |
|--------|--------|
| Scope | Multi-site pharmaceutical manufacturing (GMP regulated) |
| Motivation | FDA cybersecurity expectations + ransomware incidents in industry |
| Approach | Risk assessment (3-2) → zone design → SL-T assignment |
| Zones | Safety (SL-T:3), Batch control (SL-T:3), Packaging (SL-T:2), MES (SL-T:2) |
| Key challenge | 21 CFR Part 11 (electronic records) + IEC 62443 alignment |
| Outcome | Achieved SL-2/SL-3 across all critical zones; insurance premium reduced 30% |
| Timeline | 18 months (assessment → implementation → verification) |

### 9.2 Water Utility — Post-Oldsmar Response

| Aspect | Detail |
|--------|--------|
| Context | After Oldsmar FL water attack (2021), utility mandated OT security |
| Assessment finding | Flat network, shared accounts, TeamViewer on SCADA, no monitoring |
| Zone design | 4 zones: enterprise, DMZ, SCADA, field controllers |
| Key controls | Network segmentation, MFA for remote access, OT IDS deployment |
| Standard | IEC 62443-2-1 (CSMS) + 62443-3-3 (system requirements) |
| Result | Compliant with AWWA + state requirements; SL-2 achieved |

---

## Chapter 10 — Future Evolution & Industry Trends

| Trend | Timeline | Description |
|-------|----------|-------------|
| Edition 2 updates | 2024-2026 | Cloud, remote access, AI integrated into all parts |
| EU CRA alignment | 2025+ | IEC 62443 as harmonized standard under Cyber Resilience Act |
| NIS2 implementation | 2024+ | Essential entities must demonstrate OT security (62443) |
| IT/OT convergence | Now | Single security framework spanning IT + OT |
| Zero Trust for OT | Growing | Micro-segmentation, continuous authentication in OT |
| Supply chain security | Growing | 62443-2-4 extended for SBOM, third-party risk |
| AI/ML security | Emerging | New threat models for AI in control systems |
| 5G industrial | 2024+ | Wireless security within 62443 framework |
| Quantum readiness | 2027+ | Post-quantum cryptography for long-lifecycle OT |
| Automated compliance | Growing | Continuous monitoring for SL verification |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What are Security Levels in IEC 62443, and how do they differ from IT security maturity?  
**A:** **Security Levels (SL)** define the degree of protection against threats of increasing sophistication: SL 1: casual/accidental (someone accidentally clicks wrong button), SL 2: intentional with simple means (disgruntled employee with basic tools), SL 3: sophisticated and IACS-specific (organized group with ICS knowledge), SL 4: state-sponsored (nation-state with extensive resources and time). **Three types:** SL-T (target: what you need), SL-C (capability: what the system can do), SL-A (achieved: what you actually measured). **Difference from IT maturity:** SL is about the THREAT you're defending against (attack capability), while IT maturity (like CMMI or NIST CSF Tiers) is about your ORGANIZATION's process maturity. IEC 62443-4-1 also has maturity levels (1-4) for development process — but these are separate from the SL concept. A system at SL-3 means it can defend against sophisticated ICS-knowledgeable attackers regardless of how mature the developing organization is.

### Tier 2: Mid-Level

**Q2:** Explain Zones and Conduits and design a zone architecture for a chemical plant.  
**A:** **Zones:** Logical/physical groupings of assets sharing the same security requirements (same SL-T). Every asset belongs to exactly one zone. Zone boundary is a security perimeter. **Conduits:** Communication paths between zones. Must be secured to the HIGHER SL of the two connected zones. Control data flow between zones. **Design for chemical plant:** Zone 1 — Safety (SL-T: 4): SIS (Triconex/HIMA), dedicated engineering workstation, physically isolated, data diode for monitoring output only. Justification: TRITON proved SIS is targeted; catastrophic consequence if compromised. Zone 2 — Process Control (SL-T: 3): DCS controllers, field I/O, operator workstations. OT-specific firewall at boundary. Justification: loss of control = potential chemical release. Zone 3 — Batch/Recipe Management (SL-T: 3): Batch servers, recipe databases. Separate from real-time control. Justification: recipe manipulation = product contamination. Zone 4 — Industrial DMZ (SL-T: 3): Historian mirror, OPC UA aggregation server, patch repository, remote access jump box (MFA). Zone 5 — Enterprise (SL-T: 2): MES, ERP, corporate IT. Standard IT security. Conduits: Safety→Control: unidirectional gateway (data diode), monitoring only. Control→DMZ: OT firewall, allow specific OPC UA/Modbus ports only. DMZ→Enterprise: standard firewall, HTTPS/MQTT only outbound.

### Tier 3: Senior

**Q3:** How do you address the conflict between IEC 62443 (security patching) and IEC 61511 (safety system integrity) for a SIL 3 SIS?  
**A:** **The conflict:** IEC 62443-2-3 requires timely security patching. IEC 61511 requires that any change to a SIL-rated SIS goes through Management of Change (MoC) with proof of no safety degradation. Patching a SIS controller risks: (a) introducing software faults that degrade safety function, (b) causing unintended trip (process shutdown = millions in lost production), (c) voiding safety certification if not re-validated. **Resolution strategy:** (1) Architecture isolation first: SIS on dedicated network, physically separated from control network (IEC 62443 Zone at SL-4). Data diode for monitoring output — no inbound traffic possible. This REDUCES the need for patching by eliminating the attack surface. (2) Compensating controls (per IEC 62443-3-3 FR7): If patching is infeasible, implement: application whitelisting on SIS engineering workstation, USB port blocking, network monitoring (anomaly detection at zone boundary), physical access control (locked cabinet, logged access). (3) When patching IS required (critical CVE affecting SIS controller firmware): MOC process (per IEC 61511 clause 17): formal change request with safety impact analysis. Vendor validation: require SIS vendor (Schneider, HIMA, Yokogawa) to validate patch against safety certification. Test on offline identical SIS: maintain a test SIS (same model, same firmware, same configuration). Apply patch to test SIS → run full diagnostic + proof test → verify safety function unaffected. Scheduled deployment: apply during planned shutdown (turnaround). Full proof test after patch on production SIS. Re-validate SIL rating (at minimum, diagnostic coverage review). (4) Architectural preference: prefer SIS controllers with hardware-enforced separation between safety logic and communication stack (so communication patch doesn't affect safety execution). Newer SIS platforms (e.g., HIMA HIMax) have this separation. (5) Documentation: record in both the cybersecurity patch log (62443-2-3) and the SIS safety lifecycle documentation (61511). Demonstrate that security posture improved AND safety function maintained.

---

## Chapter 12 — Cheat Sheet & Quick Reference

### Series Quick Reference

```
Series 1 (General):
  1-1: Concepts, models, terminology
  1-2: Master glossary
  1-3: Conformance metrics
  1-4: Lifecycle & use cases
  1-5: Scheme assurance

Series 2 (Policies & Procedures — Asset Owner):
  2-1: CSMS (Cybersecurity Management System)
  2-2: Protection rating
  2-3: Patch management
  2-4: Service provider requirements

Series 3 (System — Integrator):
  3-1: Security technologies catalog
  3-2: Risk assessment & zone/conduit design
  3-3: System security requirements (SL mapping)

Series 4 (Component — Product Supplier):
  4-1: Secure Development Lifecycle (SDL)
  4-2: Technical security requirements (CR per SL)
```

### Security Levels

```
SL 1: Casual/unintentional       → Basic password, logging
SL 2: Intentional, simple means  → Unique accounts, encryption, monitoring
SL 3: Sophisticated, IACS-aware  → MFA, segmentation, IDS, hardened devices
SL 4: State-sponsored            → HW root of trust, formal verification, physical isolation
```

### Foundational Requirements (FR)

```
FR 1: Identification & Authentication Control (IAC)
FR 2: Use Control (UC) — authorization
FR 3: System Integrity (SI) — integrity monitoring
FR 4: Data Confidentiality (DC) — encryption
FR 5: Restricted Data Flow (RDF) — zones & conduits
FR 6: Timely Response to Events (TRE) — monitoring/logging
FR 7: Resource Availability (RA) — resilience/redundancy
```

### Zone Design Rules

```
1. Every asset belongs to exactly ONE zone
2. Zone SL-T based on: consequence × threat likelihood
3. Conduit SL ≥ max(SL-T of connected zones)
4. Safety systems: separate zone, highest SL
5. External connections: ALWAYS through DMZ zone
6. Wireless: separate zone or conduit (never direct to control)
```

---

*End of Document — 01_IEC_62443_Full_Series.md*
