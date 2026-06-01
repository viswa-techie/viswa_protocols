# MBSE Tools & Methodologies

**Topic:** Model-Based Systems Engineering tools, methodologies, and adoption frameworks  
**Key Tools:** Capella/Arcadia, IBM Rhapsody, Cameo Systems Modeler, Enterprise Architect, Modelica/Simulink  
**Key Methodologies:** Arcadia (Thales), Harmony SE (IBM), OOSEM (INCOSE), MagicGrid (No Magic)  
**Audience:** MBSE practitioners, tool evaluators, SE process improvement leads, digital engineering architects  
**Prerequisites:** SysML basics (v1 or v2); systems engineering lifecycle (ISO 15288); general modeling concepts

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 MBSE Evolution

| Era | Period | Approach | Tools |
|:---:|:------:|----------|-------|
| **Document-Based SE** | 1960-2000 | Word/Excel requirements; manual traceability; paper-based reviews | Word, Excel, DOORS (requirements only) |
| **Early MBSE** | 2000-2010 | UML/SysML v1 modeling; tool-centric; limited adoption | Rhapsody, MagicDraw, Artisan Studio |
| **MBSE Expansion** | 2010-2020 | Methodology-driven (Arcadia, Harmony); broader tool ecosystem; aerospace/defense adoption | Capella, Cameo, Rhapsody, DOORS NG |
| **Digital Engineering** | 2020-present | Model as authoritative source; API-driven; SysML v2; DevOps for models | Capella, Cameo, Eclipse SysON, Syndeia |

### 1.2 Key Milestones

| Year | Milestone |
|------|-----------|
| 2001 | IBM acquires Rational → Rhapsody becomes enterprise MBSE tool |
| 2003 | SysML v1 development begins (INCOSE/OMG SE-DSIG) |
| 2006 | SysML v1.0 standardized → MBSE has a standard language |
| 2007 | Capella development begins at Thales (internal tool) |
| 2010 | INCOSE MBSE Challenge → formal push for industry adoption |
| 2012 | No Magic (now Dassault) releases MagicGrid methodology |
| 2014 | Capella open-sourced (Eclipse Foundation) → democratizes MBSE |
| 2017 | DoD Digital Engineering Strategy → US defense mandates model-centric |
| 2018 | NASA adopts MBSE for Artemis program |
| 2020 | INCOSE SE Vision 2035 → MBSE as standard practice by 2035 |
| 2024 | SysML v2 adopted; Eclipse SysON released; AI-assisted modeling emerges |

---

## Chapter 2 — MBSE Methodology Comparison

### 2.1 Major Methodologies

```mermaid
graph TB
    subgraph "MBSE Methodologies Landscape"
        ARCADIA[ARCADIA (Thales)<br/>━━━━━━━━━━━<br/>• Operational Analysis<br/>• System Need Analysis<br/>• Logical Architecture<br/>• Physical Architecture<br/>━━━━━━━━━━━<br/>Tool: Capella]
        
        HARMONY[Harmony SE (IBM)<br/>━━━━━━━━━━━<br/>• Requirements Analysis<br/>• System Functional Analysis<br/>• Design Synthesis<br/>━━━━━━━━━━━<br/>Tool: Rhapsody]
        
        OOSEM[OOSEM (INCOSE)<br/>━━━━━━━━━━━<br/>• Object-Oriented SE Method<br/>• Use case driven<br/>• Architecture-centric<br/>━━━━━━━━━━━<br/>Tool: Any SysML tool]
        
        MAGICDRID[MagicGrid (Dassault)<br/>━━━━━━━━━━━<br/>• Problem domain<br/>• Solution domain<br/>• 4 pillars × 3 levels<br/>━━━━━━━━━━━<br/>Tool: Cameo Systems Modeler]
        
        RFLP[RFLP (German Auto)<br/>━━━━━━━━━━━<br/>• Requirements<br/>• Functional<br/>• Logical<br/>• Physical<br/>━━━━━━━━━━━<br/>Tool: Various (Teamcenter, etc.)]
    end
```

### 2.2 Methodology Comparison Matrix

| Feature | Arcadia/Capella | Harmony SE | OOSEM | MagicGrid |
|:-------:|:---:|:---:|:---:|:---:|
| **Organization** | Thales | IBM (legacy Telelogic) | INCOSE | Dassault (No Magic) |
| **Language** | Custom (not pure SysML) | SysML v1 | SysML v1/v2 | SysML v1 |
| **Approach** | Top-down functional analysis | Scenario-driven design | Use-case driven | Grid-based (systematic) |
| **Phases** | 4 (OA→SA→LA→PA) | 3 (Req→Functional→Design) | Iterative | 12 cells (4 pillars × 3 levels) |
| **Strengths** | Structured; guided; free tool | Strong behavioral modeling | Standard-aligned; flexible | Systematic; complete coverage |
| **Weaknesses** | Not pure SysML; proprietary metamodel | Complex; steep learning curve | Less prescriptive (methodology-lite) | Tool-specific |
| **Domain** | Defense; aerospace; telecom; transportation | Defense; aerospace; automotive | General (any domain) | Automotive; aerospace; general |
| **Adoption** | High (especially Europe; ESA; Airbus) | High (defense; legacy customers) | Moderate (academic; standard) | Growing (automotive OEMs) |
| **Cost** | **Free** (open source Capella) | Commercial (IBM license) | N/A (methodology only) | Commercial (Dassault license) |
| **Learning curve** | Medium (guided workflow helps) | High (SysML + methodology) | Medium (standard SysML) | Medium |

---

## Chapter 3 — Capella & Arcadia Deep Dive

### 3.1 Arcadia Methodology

```mermaid
graph LR
    subgraph "ARCADIA Method — 4 Phases"
        OA[OPERATIONAL ANALYSIS<br/>━━━━━━━━━━━<br/>• Who are the actors/users?<br/>• What do they do? (activities)<br/>• What interactions exist?<br/>• What are operational constraints?<br/>━━━━━━━━━━━<br/>Focus: PROBLEM SPACE<br/>Output: Operational capabilities]
        
        SA[SYSTEM NEED ANALYSIS<br/>━━━━━━━━━━━<br/>• What must the SYSTEM do?<br/>  (not how; just functions)<br/>• System boundaries<br/>• System functions/capabilities<br/>• Non-functional requirements<br/>━━━━━━━━━━━<br/>Focus: SYSTEM BLACK-BOX<br/>Output: System functions]
        
        LA[LOGICAL ARCHITECTURE<br/>━━━━━━━━━━━<br/>• How is the system organized<br/>  LOGICALLY? (components)<br/>• Functional allocation<br/>• Interfaces between components<br/>• Technology-independent<br/>━━━━━━━━━━━<br/>Focus: INTERNAL STRUCTURE<br/>Output: Logical components]
        
        PA[PHYSICAL ARCHITECTURE<br/>━━━━━━━━━━━<br/>• How is it BUILT? (hardware,<br/>  software, deployment)<br/>• Technology choices<br/>• Physical interfaces (protocols)<br/>• Resource allocation<br/>━━━━━━━━━━━<br/>Focus: IMPLEMENTATION<br/>Output: Physical design]
    end
    
    OA -->|"derive"| SA -->|"decompose"| LA -->|"allocate"| PA
```

### 3.2 Capella Tool Features

| Feature | Description |
|:-------:|-------------|
| **Eclipse-based** | Free; open-source; runs on Windows/Linux/Mac |
| **Guided workflow** | Activity Explorer guides through Arcadia phases (OA→SA→LA→PA) |
| **Diagram types** | Operational Entity Blank (OEB), System Architecture Blank (SAB), Logical Architecture Blank (LAB), Physical Architecture Blank (PAB) |
| **Viewpoints** | Performance, Mass, Safety (PVMT plugin); Cybersecurity (CDS plugin) |
| **Model validation** | Built-in rules check model consistency (unused elements, disconnected ports, missing allocations) |
| **Export** | HTML documentation generation; Word/PDF export; model-to-model exchange |
| **Extensions** | PVMT (Properties & Values Management Tool); System-to-Subsystem transition; Requirements interchange (ReqIF) |
| **Collaboration** | Team for Capella (server-based; multi-user; concurrent editing) |

### 3.3 Capella vs. SysML Tools

| Aspect | Capella (Arcadia) | SysML Tools (Cameo, Rhapsody) |
|:------:|:---:|:---:|
| **Language** | Capella-specific (guided; constrained) | SysML (flexible; expressive) |
| **Freedom** | Low (must follow Arcadia phases) | High (model anything any way) |
| **Risk** | Low (methodology prevents modeling mistakes) | Higher (freedom → potential inconsistency) |
| **Learning curve** | Easier (guided; fewer concepts) | Harder (SysML has many diagram types; patterns) |
| **Standardization** | Proprietary metamodel (Capella-specific) | OMG standard (SysML; tool-independent) |
| **Interoperability** | Limited (Capella-to-Capella; ReqIF for requirements) | Better (SysML XMI; v2 API) |
| **Best for** | Teams new to MBSE; want structure | Experienced modelers; need maximum flexibility |
| **Cost** | **Free** | $5,000-$20,000/seat/year |

---

## Chapter 4 — IBM Rhapsody & Harmony SE

### 4.1 Harmony SE Method

| Phase | Activities | Diagrams Used |
|:-----:|-----------|:---:|
| **Requirements Analysis** | Capture use cases; define system context; identify actors; derive scenarios | Use Case Diagram; Requirements Diagram; Activity Diagram |
| **System Functional Analysis** | Decompose functions; identify states/modes; define functional architecture | Activity Diagram; State Machine; Sequence Diagram |
| **Design Synthesis** | Allocate functions to physical components; define interfaces; trade studies | BDD; IBD; Sequence Diagram; State Machine |

### 4.2 Rhapsody Capabilities

| Capability | Description |
|:----------:|-------------|
| **SysML modeling** | Full SysML v1.6 support (all 9 diagram types) |
| **UML modeling** | Full UML for software design |
| **Simulation (AnimateS)** | Execute state machines; step through sequences; validate behavior |
| **Code generation** | Generate C/C++/Java from state machines and classes |
| **DOORS integration** | Bidirectional traceability (Rhapsody model ↔ DOORS requirements) |
| **TestConductor** | Model-based testing; generate test cases from state machines |
| **Team collaboration** | Rhapsody Model Manager; Design Manager (Jazz platform) |
| **Safety profiles** | AUTOSAR profile; DO-178C/DO-331 model-based development |

---

## Chapter 5 — Cameo Systems Modeler & MagicGrid

### 5.1 MagicGrid Methodology

| | **Requirements** | **Behavior** | **Structure** | **Parameters** |
|:---:|:---:|:---:|:---:|:---:|
| **Problem (Black-box)** | Stakeholder needs; use cases | Operational scenarios; workflows | System context; external interfaces | Performance requirements; KPIs |
| **Solution (White-box: Logical)** | System requirements | System functions; modes | Logical components; internal interfaces | Constraints; equations |
| **Solution (White-box: Physical)** | Allocated requirements | Physical behavior; protocols | Physical components; deployment | Physical properties; budgets |

### 5.2 Cameo Features

| Feature | Description |
|:-------:|-------------|
| **SysML v1.6** | Full support; best-in-class SysML implementation |
| **SysML v2 (pilot)** | Early v2 support; textual notation editor |
| **MagicGrid** | Built-in methodology support (templates, patterns) |
| **Teamwork Cloud** | Server-based collaboration; branching/merging of models |
| **Model validation** | Customizable rules; automatic consistency checks |
| **Simulation** | Cameo Simulation Toolkit; execute behaviors; Monte Carlo |
| **Systems of Systems** | Model federation; cross-model references |
| **Report generation** | Customizable HTML/Word/PDF reports from model |
| **DOORS integration** | Cameo DataHub; bidirectional sync with DOORS |
| **AUTOSAR** | AUTOSAR Architect plugin; generate AUTOSAR ARXML from model |
| **Safety** | Safety profile (ASIL, DAL annotations); FMEA support |

---

## Chapter 6 — Modelica & Physical Simulation

### 6.1 Modelica Overview

| Aspect | Detail |
|:------:|--------|
| **What** | Open-source object-oriented language for modeling physical systems |
| **Domains** | Mechanical, electrical, thermal, hydraulic, pneumatic, control systems — multi-domain |
| **Approach** | Equation-based (declare physics; solver figures out causality) |
| **Key difference from SysML** | SysML models STRUCTURE and BEHAVIOR (what); Modelica models PHYSICS (how it behaves mathematically) |
| **Complementary** | SysML (architecture) + Modelica (simulation) = complete MBSE |

### 6.2 Modelica vs. SysML/Capella

| Aspect | SysML / Capella | Modelica | Simulink |
|:------:|:---:|:---:|:---:|
| **Purpose** | System architecture; requirements; structure | Physics simulation; continuous dynamics | Control system design; signal processing |
| **What it models** | Components, interfaces, functions, requirements | Physical equations; multi-domain dynamics | Transfer functions; discrete/continuous control |
| **Execution** | Limited (state machine animation) | **Full simulation** (solve ODEs/DAEs) | **Full simulation** (signal flow; Stateflow) |
| **Code generation** | No (architecture model) | C code from Modelica → real-time targets | C code from Simulink → ECUs |
| **Integration** | SysML → allocate to → Modelica components | Modelica simulates SysML-defined architecture | Simulink implements SysML-defined control |
| **Tools** | Cameo, Capella, Rhapsody | OpenModelica, Dymola, SimulationX | MATLAB/Simulink (MathWorks) |

### 6.3 MBSE Integration Pattern

```mermaid
graph TB
    subgraph "Integrated MBSE Toolchain"
        REQ_TOOL[Requirements Management<br/>━━━━━━━━━━━<br/>IBM DOORS / Polarion / Jama<br/>• Stakeholder requirements<br/>• System requirements<br/>• Safety requirements<br/>• Traceability]
        
        ARCH_TOOL[Architecture Modeling<br/>━━━━━━━━━━━<br/>Capella / Cameo / Rhapsody<br/>• System architecture (SysML)<br/>• Component definitions<br/>• Interfaces<br/>• Behavior (states, sequences)]
        
        SIM_TOOL[Simulation & Analysis<br/>━━━━━━━━━━━<br/>Modelica (Dymola) / Simulink<br/>• Physics simulation<br/>• Control design<br/>• Performance analysis<br/>• Timing analysis]
        
        TEST_TOOL[Test & Verification<br/>━━━━━━━━━━━<br/>VectorCAST / Tessy / dSPACE<br/>• Model-in-the-Loop (MIL)<br/>• Software-in-the-Loop (SIL)<br/>• Hardware-in-the-Loop (HIL)]
        
        PLM[PLM / ALM<br/>━━━━━━━━━━━<br/>Teamcenter / Windchill / Jira<br/>• Configuration management<br/>• Change management<br/>• Project tracking]
        
        BRIDGE[Integration Hub<br/>━━━━━━━━━━━<br/>Syndeia / Capella-to-Simulink /<br/>ReqIF / OSLC / FMI<br/>• Model-to-model links<br/>• Traceability between tools<br/>• Change propagation]
    end
    
    REQ_TOOL <-->|"ReqIF / OSLC"| BRIDGE
    ARCH_TOOL <-->|"OSLC / API"| BRIDGE
    SIM_TOOL <-->|"FMI / API"| BRIDGE
    TEST_TOOL <-->|"API"| BRIDGE
    PLM <-->|"OSLC"| BRIDGE
```

---

## Chapter 7 — Tool Selection Guide

### 7.1 Decision Matrix

| Criterion | Weight | Capella | Cameo | Rhapsody | Enterprise Architect |
|:-------:|:---:|:---:|:---:|:---:|:---:|
| **Cost** | High | ★★★★★ (Free) | ★★☆☆☆ ($15K/seat) | ★★☆☆☆ ($12K/seat) | ★★★★☆ ($500/seat) |
| **SysML compliance** | High | ★★☆☆☆ (Custom) | ★★★★★ (Full SysML) | ★★★★★ (Full SysML) | ★★★★☆ (Good SysML) |
| **Methodology support** | Medium | ★★★★★ (Arcadia built-in) | ★★★★☆ (MagicGrid) | ★★★★☆ (Harmony) | ★★★☆☆ (Flexible) |
| **Learning curve** | Medium | ★★★★☆ (Guided) | ★★★☆☆ (Complex) | ★★☆☆☆ (Steep) | ★★★★☆ (Easy start) |
| **Collaboration** | High | ★★★★☆ (Team for Capella) | ★★★★★ (Teamwork Cloud) | ★★★☆☆ (Model Manager) | ★★★★☆ (Pro Cloud Server) |
| **Simulation** | Medium | ★★☆☆☆ (Limited) | ★★★★☆ (Simulation Toolkit) | ★★★★★ (AnimateS) | ★★☆☆☆ (Basic) |
| **Code generation** | Low | ★☆☆☆☆ (No) | ★★★☆☆ (Templates) | ★★★★★ (C/C++/Java) | ★★★☆☆ (Templates) |
| **DOORS integration** | High | ★★★☆☆ (ReqIF) | ★★★★★ (DataHub) | ★★★★★ (Native) | ★★★☆☆ (Add-in) |
| **SysML v2 readiness** | Medium | ★★☆☆☆ (Exploring) | ★★★★☆ (Pilot) | ★★★☆☆ (Planned) | ★★★☆☆ (Planned) |
| **Scalability** | High | ★★★★☆ (Large models OK) | ★★★★★ (Enterprise-proven) | ★★★★☆ (Good) | ★★★☆☆ (Large models slow) |

### 7.2 Recommendation Guide

| Scenario | Recommended Tool | Rationale |
|:--------:|:---:|---|
| Small team; first MBSE adoption; limited budget | **Capella** | Free; guided methodology; fast start |
| Large aerospace/defense program; full SysML needed | **Cameo Systems Modeler** | Best SysML support; scalable collaboration; DoDAF/UPDM |
| Embedded systems; need code generation from models | **IBM Rhapsody** | State machine → C code; simulation; safety certification |
| Enterprise architecture + some SE | **Enterprise Architect** | Affordable; UML+SysML+ArchiMate+BPMN; wide use |
| Automotive OEM; AUTOSAR integration | **Cameo + AUTOSAR plugin** | SysML → AUTOSAR ARXML; ASPICE compliance |
| Multi-tool environment; integration priority | **Syndeia + any tool** | Integration hub; connects any combination of tools |
| New project; want latest technology | **Eclipse SysON** (SysML v2) | Open-source; SysML v2 native; future-proof |

---

## Chapter 8 — Integration Standards & Protocols

### 8.1 OSLC (Open Services for Lifecycle Collaboration)

| Aspect | Detail |
|:------:|--------|
| **What** | Standard for tool integration in SE/SW engineering |
| **Organization** | OASIS (formerly IBM-led) |
| **Approach** | REST/HTTP based; linked data (RDF); tools expose resources as web resources |
| **Domains** | Requirements Management (RM); Change Management (CM); Quality Management (QM); Architecture Management (AM) |
| **Use** | Link DOORS requirements ↔ Rhapsody model elements ↔ test cases (without proprietary adapters) |

### 8.2 ReqIF (Requirements Interchange Format)

| Aspect | Detail |
|:------:|--------|
| **What** | XML-based standard for exchanging requirements between tools |
| **Standard** | OMG specification (derived from RIF by HIS automotive consortium) |
| **Use case** | OEM exports requirements from DOORS → supplier imports into Polarion/Jama/Capella |
| **Supports** | Requirements, attributes, traceability links, embedded objects |

### 8.3 FMI (Functional Mock-up Interface)

| Aspect | Detail |
|:------:|--------|
| **What** | Standard interface for co-simulation and model exchange |
| **Use** | Connect Modelica model + Simulink model + SysML model in co-simulation |
| **FMI 2.0** | FMU (Functional Mock-up Unit) packages: model + solver as black-box |
| **FMI 3.0** | Added: clocks, arrays, terminals; better for automotive |

---

## Chapter 9 — Case Studies

### 9.1 Thales/ESA: Satellite Design with Capella

| Aspect | Detail |
|--------|--------|
| **Program** | ESA Earth observation satellite (Sentinel series follow-on) |
| **Challenge** | 200+ engineers across 5 countries; complex system (optical payload, communication, AOCS, power, thermal); needed single source of truth |
| **Methodology** | Arcadia (full 4 phases: OA→SA→LA→PA) |
| **Tool** | Capella + Team for Capella (collaborative server) |
| **Model size** | ~50,000 model elements; 400+ diagrams |
| **Key benefit** | Interface management: Capella model defined all 2,000+ inter-subsystem interfaces unambiguously. Before Capella: 40% of integration problems were interface mismatches. After: reduced to < 5% |
| **Schedule impact** | Integration phase shortened by 4 months (interface issues caught in model, not on hardware) |
| **Cost savings** | ~€12M estimated (avoided integration rework; reduced late design changes) |

### 9.2 Automotive OEM: ADAS Platform with Cameo + DOORS

| Aspect | Detail |
|--------|--------|
| **Program** | Level 2+ ADAS platform (ACC, LKA, AEB) for vehicle family (3 variants) |
| **Challenge** | ASPICE Level 3 required; ISO 26262 compliance; 8 suppliers; need full traceability |
| **Toolchain** | DOORS (requirements) ↔ Cameo (SysML architecture) ↔ Simulink (control) ↔ dSPACE (HIL test) |
| **MagicGrid application** | Problem domain: stakeholder needs (driver, regulator, insurance). Solution: system functions → logical components → physical ECU allocation. |
| **Traceability** | Bidirectional: DOORS requirement → Cameo element → Simulink model → test case → test result. Automated via Cameo DataHub + OSLC. |
| **Variant management** | Platform model with variability: sensor configurations vary by vehicle variant. SysML model uses feature diagrams + conditional allocations. |
| **ASPICE audit result** | Level 3 achieved for SWE.1-6 + SUP.8. Auditor specifically praised traceability completeness enabled by integrated toolchain. |
| **Lesson learned** | Tool integration took 6 months to configure properly. Recommend: invest early in integration; don't leave it to "later." |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact on MBSE Tools |
|-------|----------|---------------------|
| **SysML v2 ecosystem** | 2024-2027 | All major tools migrate to v2; textual notation becomes primary; API-first architecture |
| **AI-assisted modeling** | 2024-2026 | LLMs generate SysML from natural language; suggest patterns; auto-complete models |
| **Digital twin integration** | 2025-2030 | MBSE models become digital twin blueprints; runtime data feeds back to update models |
| **Cloud-native MBSE** | 2024-2026 | Browser-based modeling (SaaS); no local install; collaboration-first |
| **Model verification** | 2025-2028 | Formal methods on SysML v2 models; prove safety properties without testing |
| **Low-code MBSE** | 2025-2028 | Domain-specific modeling (auto-generate vehicle architecture from feature list) |
| **Open-source dominance** | 2025-2030 | Capella + Eclipse SysON challenge commercial tools; ecosystem grows |
| **Tool consolidation** | 2025-2027 | Fewer tools needed; one tool does architecture + simulation + requirements |
| **Continuous MBSE** | 2025-2030 | Model changes trigger: re-simulation → re-verification → deployment (like CI/CD for code) |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is MBSE and why is it replacing document-based SE?

**A:** MBSE (Model-Based Systems Engineering) uses formal models (SysML, Capella, Modelica) as the primary artifacts for system development — replacing Word documents, Excel spreadsheets, and PowerPoint slides.

**Why replacing documents:**

| Problem with Documents | MBSE Solution |
|:---:|:---:|
| Inconsistency (Word doc says A; Excel says B) | Single source of truth (the model) |
| Manual traceability (copy-paste links break) | Automatic traceability (model relationships) |
| Impact analysis is hard ("what breaks if I change this requirement?") | Click requirement → see all affected elements |
| Stale copies (old version of design doc floating around) | Model is always current; views generated live |
| Review is slow (read 200-page document) | Model is queryable ("show me all ASIL D components") |
| Reuse is poor (copy-paste across projects) | Model libraries (import; specialize; reuse) |

**What MBSE is NOT:**
- NOT just drawing diagrams (that's just "drawing-based SE")
- IS creating a semantic model where elements have meaning, relationships, and constraints
- The model can be ANALYZED (simulate, query, validate) — documents cannot

### Tier 2: Mid-Level

**Q2:** Compare Capella/Arcadia with Cameo/SysML for an automotive ADAS project. Which would you recommend and why?

**A:**

| Criterion | Capella (Arcadia) | Cameo (SysML) | Winner for ADAS |
|:-------:|:---:|:---:|:---:|
| **Cost** | Free | $15K/seat/year × 50 engineers = $750K/year | Capella |
| **Methodology** | Arcadia built-in (guided) | MagicGrid (optional; less enforced) | Capella (for teams new to MBSE) |
| **SysML standard** | No (proprietary metamodel) | Yes (OMG SysML → tool independence) | Cameo (supplier interop) |
| **DOORS integration** | ReqIF (import/export; not real-time) | DataHub (real-time bidirectional sync) | Cameo |
| **AUTOSAR** | No native support | AUTOSAR plugin (generate ARXML) | Cameo |
| **ISO 26262** | Safety viewpoint (PVMT) | Safety profile (ASIL annotations; FMEA) | Cameo (slightly better) |
| **ASPICE evidence** | Can generate (but less standard) | Well-established (assessors know SysML) | Cameo |
| **Collaboration** | Team for Capella (good) | Teamwork Cloud (excellent) | Tie |
| **Simulation** | Limited | Simulation Toolkit (Monte Carlo) | Cameo |

**My recommendation for ADAS automotive project:**

**Cameo Systems Modeler** — because:
1. **Supplier interoperability:** SysML is standard; suppliers can open/use SysML models (not locked to one tool)
2. **AUTOSAR integration:** ADAS ECU will use AUTOSAR; Cameo generates ARXML directly
3. **DOORS integration:** OEMs standardize on DOORS for requirements; real-time bidirectional sync is critical for traceability evidence in ASPICE/ISO 26262 audits
4. **Safety compliance:** Mature safety profile; assessors/auditors recognize SysML artifacts
5. **Ecosystem:** ADAS needs Simulink (control), dSPACE (HIL), DOORS (req) — all integrate better with SysML-based tools via OSLC

**When I'd choose Capella instead:**
- Team has zero MBSE experience (Capella's guided approach reduces mistakes)
- Budget is severely constrained (free tool)
- No AUTOSAR; no DOORS; simpler system
- European defense/aerospace project (Capella dominates there)

### Tier 3: Senior

**Q3:** Design a complete MBSE toolchain for an automotive OEM developing an autonomous driving platform. Cover: requirements, architecture modeling, simulation, verification, integration between tools, and governance. Address how to achieve ASPICE Level 3 and ISO 26262 compliance.

**A:**

**Toolchain architecture:**

| Layer | Tool | Purpose | License Cost (100 engineers) |
|:-----:|:----:|---------|:---:|
| **Requirements** | IBM DOORS Next Gen | Manage all requirements (stakeholder, system, SW, safety, security) | ~$500K/year |
| **Architecture** | Cameo Systems Modeler | SysML architecture model (system → logical → physical) | ~$1.5M/year |
| **Simulation (System)** | Dymola (Modelica) | Multi-physics simulation (vehicle dynamics, thermal, electrical) | ~$200K/year |
| **Simulation (Control)** | MATLAB/Simulink | Control algorithm design; MIL simulation | ~$800K/year |
| **Test Management** | Polarion ALM | Test cases, test execution, defect tracking | ~$300K/year |
| **HIL Testing** | dSPACE SCALEXIO | Hardware-in-the-loop verification | ~$2M (hardware + software) |
| **CI/CD** | Jenkins + Artifactory | Automated build, test, deploy | ~$50K/year |
| **Integration Hub** | Syndeia (Intercax) | OSLC-based tool linking; traceability bridge | ~$200K/year |
| **Configuration Management** | Git (model: Teamwork Cloud; code: GitLab) | Version control for all artifacts | ~$100K/year |
| **PLM** | Siemens Teamcenter | BOM management; change management; release | ~$500K/year |

**Integration architecture:**

```
DOORS ←→ [Syndeia/OSLC] ←→ Cameo ←→ [FMI] ←→ Simulink/Dymola
  ↕                           ↕                        ↕
Polarion ←→ [OSLC] ←→ dSPACE ←→ Jenkins
  ↕                                    ↕
Teamcenter ←→ [OSLC/PLM4MBSE] ←→ GitLab
```

**Traceability chain (for ASPICE/ISO 26262 compliance):**

```
Stakeholder Need (DOORS) → System Requirement (DOORS)
  → SysML System Element (Cameo) → SW Requirement (DOORS)
    → SysML SW Component (Cameo) → Simulink Model (code gen)
      → Test Case (Polarion) → Test Result (Polarion/dSPACE)
        → HARA Item (DOORS) → Safety Goal → Safety Requirement
```

**Governance model:**

| Governance Element | Implementation |
|:------------------:|---------------|
| **Baseline management** | Monthly baselines in Teamwork Cloud (model) + DOORS (requirements); CCB approval |
| **Change control** | Change request in Polarion → impact analysis (Syndeia traces which models/tests affected) → CCB → implement → verify |
| **Model quality** | Weekly automated model validation (Cameo rules); monthly peer review |
| **Traceability audits** | Quarterly: automated report from Syndeia ("orphan requirements? Untested elements? Missing allocations?") |
| **ASPICE process** | Process defined in Quality Manual; each tool maps to ASPICE work products; assessments annually |
| **Training** | All engineers: 40h MBSE training; tool-specific certification; annual refresher |

---

## Chapter 12 — Cheat Sheet & Quick Reference

```
═══════════════════════════════════════════
MBSE TOOLS & METHODOLOGIES — QUICK REFERENCE
═══════════════════════════════════════════

TOOL SELECTION (QUICK):
  Budget-constrained / new to MBSE → Capella (free)
  Full SysML / automotive / ASPICE → Cameo Systems Modeler
  Code generation / embedded / defense → IBM Rhapsody
  Affordable / wide modeling (UML+SysML+BPMN) → Enterprise Architect
  SysML v2 / open source / future → Eclipse SysON

═══════════════════════════════════════════
METHODOLOGIES:
  Arcadia (Capella): OA → SA → LA → PA (4 phases; top-down)
  Harmony SE (Rhapsody): Req → Functional → Design Synthesis
  MagicGrid (Cameo): 4 pillars × 3 levels (systematic grid)
  OOSEM (any tool): Use-case driven; architecture-centric
  RFLP (automotive): Requirements → Function → Logical → Physical

═══════════════════════════════════════════
INTEGRATION STANDARDS:
  OSLC   — Tool-to-tool integration (REST; linked data)
  ReqIF  — Requirements interchange (XML)
  FMI    — Co-simulation interface (model coupling)
  XMI    — Model interchange (SysML v1; often unreliable)
  SysML v2 API — New standard (REST + JSON; reliable)

═══════════════════════════════════════════
TOOL CAPABILITIES MATRIX:
             Capella  Cameo  Rhapsody  EA
  SysML:       ✗      ★★★★★  ★★★★★    ★★★★
  Free:        ✓      ✗      ✗        ✗
  Simulation:  ✗      ★★★★   ★★★★★    ✗
  Code Gen:    ✗      ★★★    ★★★★★    ★★★
  DOORS:       ★★★    ★★★★★  ★★★★★    ★★★
  AUTOSAR:     ✗      ★★★★★  ★★★      ✗
  Collab:      ★★★★   ★★★★★  ★★★      ★★★★

═══════════════════════════════════════════
WHEN TO USE MBSE vs DOCUMENTS:
  MBSE when: complex system; many interfaces; long lifecycle;
    safety-critical; multiple stakeholders; reuse needed;
    traceability required (ASPICE, DO-178C, ISO 26262)
  Documents when: simple system; small team; short lifecycle;
    one-off project; team has no MBSE skills (yet)

═══════════════════════════════════════════
COST COMPARISON (per seat/year):
  Capella: FREE (open source)
  Eclipse SysON: FREE (open source; SysML v2)
  Enterprise Architect: ~$500
  Cameo Systems Modeler: ~$15,000
  IBM Rhapsody: ~$12,000
  DOORS Next Gen: ~$5,000

═══════════════════════════════════════════
ARCADIA PHASES (CAPELLA):
  OA (Operational Analysis) → WHO does WHAT?
  SA (System Need Analysis) → WHAT must system do?
  LA (Logical Architecture) → HOW organized internally?
  PA (Physical Architecture) → HOW built physically?
```

---

*End of Document — 04_MBSE_Tools_Methodologies.md*
