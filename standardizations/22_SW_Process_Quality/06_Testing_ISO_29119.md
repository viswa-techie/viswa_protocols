# ISO/IEC/IEEE 29119 — Software Testing Standard

**Standard:** ISO/IEC/IEEE 29119 (Parts 1-5)  
**SDO:** ISO/IEC JTC 1/SC 7 + IEEE  
**Version:** Current editions: Part 1 (2022), Part 2 (2021), Part 3 (2021), Part 4 (2021), Part 5 (2016)  
**Companion:** ISTQB (International Software Testing Qualifications Board) — aligned certification  
**Audience:** Test managers, test analysts, QA engineers, test architects, automation engineers  
**Prerequisites:** Software development lifecycle knowledge; basic testing concepts

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Milestone |
|------|-----------|
| 1979 | Glenford Myers publishes "The Art of Software Testing" |
| 1988 | IEEE 829 (Test Documentation standard) published |
| 1998 | IEEE 1008 (Unit Testing standard) |
| 2001 | BS 7925-1/2 (Software testing — vocabulary + component testing) |
| 2005 | ISO/IEC 29119 project initiated (harmonize testing standards) |
| 2008 | ISTQB Foundation Level syllabus v2008 (widely adopted) |
| 2013 | **ISO/IEC/IEEE 29119-1:2013** (Part 1: Concepts & definitions) |
| 2013 | **ISO/IEC/IEEE 29119-2:2013** (Part 2: Test processes) |
| 2013 | **ISO/IEC/IEEE 29119-3:2013** (Part 3: Test documentation) |
| 2015 | **ISO/IEC/IEEE 29119-4:2015** (Part 4: Test techniques) |
| 2016 | **ISO/IEC/IEEE 29119-5:2016** (Part 5: Keyword-driven testing) |
| 2021 | Parts 2, 3, 4 revised (Agile/DevOps integration; modern practices) |
| 2022 | Part 1 revised (updated terminology; aligned with modern concepts) |

### 1.2 Standards Consolidated into ISO 29119

| Previous Standard | Topic | Now Covered By |
|:---:|---|:---:|
| IEEE 829 | Test documentation | ISO 29119-3 |
| IEEE 1008 | Unit testing | ISO 29119-2 (test process at unit level) |
| BS 7925-1 | Testing vocabulary | ISO 29119-1 |
| BS 7925-2 | Component testing | ISO 29119-4 (techniques) |
| IEEE 1012 | Verification & validation | ISO 29119-2 (partially; 1012 still exists) |

---

## Chapter 2 — Standard Architecture

### 2.1 Five Parts Structure

```mermaid
graph TB
    subgraph "ISO/IEC/IEEE 29119 — Five Parts"
        P1[Part 1: Concepts & Definitions<br/>━━━━━━━━━<br/>• Testing vocabulary<br/>• Testing concepts<br/>• Testing context<br/>• Test levels; test types]
        
        P2[Part 2: Test Processes<br/>━━━━━━━━━<br/>• Organizational test process<br/>• Test management process<br/>• Dynamic test process<br/>• (Static testing in annex)]
        
        P3[Part 3: Test Documentation<br/>━━━━━━━━━<br/>• Test policy<br/>• Test strategy<br/>• Test plan<br/>• Test specification<br/>• Test report]
        
        P4[Part 4: Test Techniques<br/>━━━━━━━━━<br/>• Specification-based (black-box)<br/>• Structure-based (white-box)<br/>• Experience-based<br/>• 23 techniques described]
        
        P5[Part 5: Keyword-Driven Testing<br/>━━━━━━━━━<br/>• Keyword framework<br/>• Keyword library<br/>• Data-driven testing<br/>• Automation architecture]
    end
    
    P1 --> P2
    P1 --> P3
    P1 --> P4
    P1 --> P5
    P2 --> P3
    P4 --> P5
```

---

## Chapter 3 — Part 2: Test Processes

### 3.1 Three-Layer Process Model

```mermaid
graph TB
    subgraph "ISO 29119-2 Test Process Architecture"
        subgraph "Layer 1: Organizational Test Process"
            OTP[Organizational Test Process<br/>━━━━━━━━━<br/>• Define organizational test policy<br/>• Define organizational test strategy<br/>• Monitor & control (organizational level)]
        end
        
        subgraph "Layer 2: Test Management Process"
            TMP[Test Management Process<br/>━━━━━━━━━<br/>• Test planning<br/>• Test monitoring & control<br/>• Test completion]
        end
        
        subgraph "Layer 3: Dynamic Test Process"
            DTP[Dynamic Test Process<br/>━━━━━━━━━<br/>• Test design<br/>• Test implementation<br/>• Test execution<br/>• Test incident reporting]
        end
    end
    
    OTP -->|"governs"| TMP
    TMP -->|"directs"| DTP
    DTP -->|"reports to"| TMP
    TMP -->|"reports to"| OTP
```

### 3.2 Test Management Process

| Activity | Description | Key Outputs |
|:--------:|-------------|:-----------:|
| **Test Planning** | Define test approach, scope, resources, schedule, risks, entry/exit criteria | Test Plan |
| **Test Monitoring & Control** | Track progress against plan; take corrective action | Status reports; Metrics |
| **Test Completion** | Evaluate test results; archive test assets; lessons learned | Test Completion Report |

### 3.3 Dynamic Test Process

| Activity | Description | Key Outputs |
|:--------:|-------------|:-----------:|
| **Test Design** | Identify test conditions; derive test cases from requirements/specifications | Test cases (logical); Coverage items |
| **Test Implementation** | Create test procedures; set up test data; prepare test environment | Test procedures (executable); Test data |
| **Test Execution** | Run test procedures; compare actual vs expected; log results | Test results; Incident reports |
| **Test Incident Reporting** | Report discrepancies between actual and expected results | Incident reports (defects) |

### 3.4 Test Levels

| Test Level | Focus | Typical Responsibility | Verifies Against |
|:----------:|-------|:---:|:---:|
| **Unit/Component Testing** | Individual code units/modules | Developer | Detailed design |
| **Integration Testing** | Interfaces between components/systems | Developer/Test engineer | Architecture/interface specs |
| **System Testing** | Complete system end-to-end | Test team (independent) | System requirements |
| **Acceptance Testing** | Fitness for use; stakeholder needs | Customer/User | Business requirements; contracts |

### 3.5 Test Types

| Test Type | Purpose | Examples |
|:---------:|---------|---------|
| **Functional** | Verify what the system DOES | Feature testing; workflow testing; use case testing |
| **Non-functional** | Verify HOW WELL (quality attributes) | Performance; security; usability; reliability testing |
| **Structural** | Verify internal structure/coverage | Code coverage; path testing; integration completeness |
| **Change-related** | Verify changes don't break existing functionality | Regression testing; confirmation testing (re-testing fixed defects) |

---

## Chapter 4 — Part 3: Test Documentation

### 4.1 Documentation Hierarchy

```mermaid
graph TB
    subgraph "ISO 29119-3 Documentation Hierarchy"
        TP_DOC[Test Policy<br/>━━━━━━━━━<br/>• Organizational level<br/>• Why we test; principles<br/>• One per organization]
        
        TS_DOC[Test Strategy<br/>━━━━━━━━━<br/>• Project/program level<br/>• How we approach testing<br/>• Test levels; types; techniques<br/>• Entry/exit criteria templates]
        
        TPLAN_DOC[Test Plan<br/>━━━━━━━━━<br/>• Per test level/phase<br/>• Specific scope; schedule; resources<br/>• Risk analysis; mitigation<br/>• Entry/exit criteria (specific)]
        
        TSPEC_DOC[Test Specification<br/>━━━━━━━━━<br/>• Test design spec (conditions; coverage items)<br/>• Test case spec (inputs; expected results)<br/>• Test procedure spec (step-by-step execution)]
        
        TRES_DOC[Test Results<br/>━━━━━━━━━<br/>• Test execution log<br/>• Test incident reports (defects)<br/>• Test status reports]
        
        TCOMP_DOC[Test Completion Report<br/>━━━━━━━━━<br/>• Summary of testing<br/>• Coverage achieved<br/>• Outstanding defects<br/>• Assessment; recommendation]
    end
    
    TP_DOC --> TS_DOC --> TPLAN_DOC --> TSPEC_DOC --> TRES_DOC --> TCOMP_DOC
```

### 4.2 Test Plan Contents (ISO 29119-3)

| Section | Contents |
|:-------:|----------|
| **Context** | Project overview; system under test; test level |
| **Scope** | What is in/out of scope; features to test; features not to test |
| **Risk analysis** | Product risks (what can go wrong); risk-based testing priority |
| **Approach** | Test design techniques to use; coverage criteria; automation approach |
| **Entry criteria** | What must be true before testing starts (e.g., build stable; environment ready) |
| **Exit criteria** | What defines "testing complete" (e.g., 100% test cases executed; 0 critical defects open) |
| **Schedule** | Milestones; test phases; dependencies |
| **Resources** | People; tools; environments; test data |
| **Deliverables** | What test artifacts will be produced |
| **Communication** | Reporting frequency; escalation path; stakeholders |

---

## Chapter 5 — Part 4: Test Techniques

### 5.1 Technique Categories

| Category | Focus | Approach |
|:--------:|-------|----------|
| **Specification-based (Black-box)** | What the system should do | Derive tests from requirements/specifications; no code knowledge needed |
| **Structure-based (White-box)** | How the system is built | Derive tests from code structure; coverage metrics |
| **Experience-based** | Tester expertise | Leverage tester knowledge; intuition; exploratory testing |

### 5.2 Specification-Based Techniques

| Technique | Description | When to Use |
|:---------:|-------------|-------------|
| **Equivalence Partitioning** | Divide input domain into partitions where all values in partition behave identically; test one value per partition | Any input with ranges/categories |
| **Boundary Value Analysis** | Test at boundaries of equivalence partitions (min, min+1, max-1, max) | Numeric inputs; range boundaries |
| **Decision Table Testing** | Enumerate all combinations of conditions → expected actions | Multiple interacting conditions (Boolean logic) |
| **State Transition Testing** | Test transitions between states; valid + invalid transitions | Systems with finite states (protocols; workflows) |
| **Classification Tree Method** | Systematic combination of test factors (graphical; hierarchical) | Complex multi-parameter inputs |
| **Pairwise Testing** | Cover all 2-way combinations of parameters (without full combinatorial) | Many parameters; need to reduce test count |
| **Use Case Testing** | Derive tests from use cases (main flow + alternative + exception flows) | Functional requirements as use cases |
| **Syntax Testing** | Test against formal syntax rules (grammar) | Protocol testing; input validation |

### 5.3 Structure-Based Techniques

| Technique | Coverage Criterion | Definition |
|:---------:|:---:|---|
| **Statement Coverage** | % statements executed | Every executable statement executed at least once |
| **Branch/Decision Coverage** | % branches taken | Every branch (true/false at each decision point) executed |
| **Condition Coverage** | % conditions evaluated to both T/F | Each atomic condition evaluated to both true and false |
| **MC/DC** | Modified Condition/Decision Coverage | Each condition independently affects the decision outcome |
| **Path Coverage** | % paths executed | Every possible path through code executed (often infeasible) |

**Coverage hierarchy:**

$$\text{Statement} \subset \text{Branch} \subset \text{Condition} \subset \text{MC/DC} \subset \text{Path}$$

**Industry requirements:**
| Domain | Minimum Coverage | Standard |
|:------:|:---:|:---:|
| Automotive (ASIL D) | MC/DC (or equivalent) | ISO 26262 |
| Automotive (ASIL A/B) | Statement + Branch | ISO 26262 |
| Aviation (DAL A) | MC/DC | DO-178C |
| Aviation (DAL C) | Statement | DO-178C |
| Medical (Class C) | Branch | IEC 62304 |

### 5.4 Experience-Based Techniques

| Technique | Description |
|:---------:|-------------|
| **Exploratory Testing** | Simultaneous learning + test design + test execution; session-based; charter-guided |
| **Error Guessing** | Use tester experience to anticipate likely defects; test edge cases; common mistakes |
| **Checklist-Based Testing** | Use domain-specific checklists (e.g., security checklist; accessibility checklist) |

---

## Chapter 6 — Part 5: Keyword-Driven Testing

### 6.1 Keyword-Driven Architecture

```mermaid
graph TB
    subgraph "Keyword-Driven Test Framework (ISO 29119-5)"
        subgraph "Test Specification Layer"
            TEST_CASES_K[Test Cases<br/>(keyword sequences)<br/>━━━━━━━━━<br/>Click "Login"<br/>Enter "user@test.com"<br/>Enter "password123"<br/>Click "Submit"<br/>Verify "Dashboard" displayed]
        end
        
        subgraph "Keyword Library Layer"
            KEYWORDS[Keywords<br/>━━━━━━━━━<br/>• Click (element)<br/>• Enter (text)<br/>• Verify (condition)<br/>• Wait (duration)<br/>• Navigate (URL)]
        end
        
        subgraph "Automation Engine Layer"
            ENGINE[Automation Engine<br/>━━━━━━━━━<br/>• Maps keywords to code<br/>• Executes on SUT<br/>• Reports results<br/>• Handles data]
        end
        
        subgraph "System Under Test"
            SUT_K[Application<br/>━━━━━━━━━<br/>• Web UI<br/>• API<br/>• Desktop<br/>• Mobile]
        end
    end
    
    TEST_CASES_K --> KEYWORDS --> ENGINE --> SUT_K
```

### 6.2 Benefits of Keyword-Driven Approach

| Benefit | Description |
|:-------:|-------------|
| **Separation of concerns** | Test designers don't need programming skills; automation engineers focus on keyword implementation |
| **Reusability** | Keywords used across multiple test cases; one change in keyword → all tests updated |
| **Maintainability** | When UI changes, update keyword implementation; test cases unchanged |
| **Readability** | Test cases are human-readable (business language, not code) |
| **Tool independence** | Test cases defined abstractly; can switch automation tools by re-implementing keywords |

---

## Chapter 7 — Comparison: ISO 29119 vs ISTQB vs Other Frameworks

| Criterion | ISO 29119 | ISTQB | TMap | Google Testing | Context-Driven Testing |
|:---------:|:---------:|:-----:|:----:|:-:|:-:|
| **Type** | International standard | Certification body + syllabus | Methodology (Sogeti) | Engineering practices (book) | Philosophy/school |
| **Focus** | Process + documentation + techniques | Knowledge + certification | Complete test methodology | Practical engineering; automation | Context matters most |
| **Prescriptive?** | Process framework (what to do) | What to know (for exam) | How to do (complete method) | What works at Google | "It depends" |
| **Documentation** | Comprehensive templates | References 29119 | Own templates | Minimal (code is documentation) | Minimal (favor exploration) |
| **Agile** | Addressed (2021 revisions) | Agile tester extension | TMap HD (high-tech) | Native (built for CI/CD) | Embraced (exploration is agile) |
| **Certification** | No | Yes (Foundation → Expert) | Yes (TMap Next) | No | No |
| **Industry** | Any (especially regulated) | Any | Dutch origin; Europe | Tech companies | Any (especially startup/agile) |

### 7.1 ISTQB Certification Levels

| Level | Title | Focus |
|:-----:|:-----:|-------|
| **Foundation** | CTFL (Certified Tester Foundation Level) | Basic testing concepts; techniques; management |
| **Advanced** | CTAL (Test Manager / Test Analyst / Technical Test Analyst) | Deep knowledge in one specialty |
| **Expert** | CTEL (Test Management / Improving Test Process / Test Automation) | Strategic/organizational test expertise |
| **Specialist** | Various (Agile, Performance, Security, AI, Automotive, etc.) | Domain-specific testing |

---

## Chapter 8 — Risk-Based Testing

### 8.1 Product Risk Analysis (ISO 29119-2)

```mermaid
graph TB
    subgraph "Risk-Based Testing Approach"
        IDENTIFY[Identify Product Risks<br/>━━━━━━━━━<br/>• What can go wrong?<br/>• What's the impact?<br/>• What's the likelihood?<br/>• Sources: requirements; architecture;<br/>  complexity; change history]
        
        ASSESS[Assess Risks<br/>━━━━━━━━━<br/>• Likelihood × Impact = Risk Level<br/>• Categorize: High / Medium / Low<br/>• Consider: safety; financial; reputation]
        
        MITIGATE[Mitigate Through Testing<br/>━━━━━━━━━<br/>• High risk: extensive testing (many techniques;<br/>  high coverage; independent testing)<br/>• Medium risk: standard testing<br/>• Low risk: lightweight testing (sampling;<br/>  experience-based)]
        
        MONITOR_R[Monitor Residual Risk<br/>━━━━━━━━━<br/>• Track defects found vs expected<br/>• Re-assess risk as testing progresses<br/>• Decide: continue testing or release?]
    end
    
    IDENTIFY --> ASSESS --> MITIGATE --> MONITOR_R
```

### 8.2 Risk-Based Test Prioritization

| Risk Level | Testing Approach | Coverage Target | Test Design Technique |
|:----------:|:---:|:---:|:---:|
| **Critical** | Maximum rigor; multiple techniques; independent testers | MC/DC or 100% branch + BVA + decision tables | All applicable: EP, BVA, Decision Table, State Transition, Exploratory |
| **High** | Thorough testing; formal techniques | 100% branch + BVA | EP, BVA, State Transition |
| **Medium** | Standard testing; key scenarios | Statement coverage; key paths | EP, Use Case Testing |
| **Low** | Sampling; checklist-based; exploratory | Spot checks | Exploratory, Checklist |

---

## Chapter 9 — Case Studies

### 9.1 Automotive: ISO 29119 + ISO 26262 Test Process

| Aspect | Detail |
|--------|--------|
| **Context** | ADAS ECU development; ASIL B; 500 KLOC; ISO 26262 Part 6 testing requirements |
| **Challenge** | ISO 26262 defines WHAT to test and coverage targets; ISO 29119 defines HOW to organize the test process |
| **Solution** | Used ISO 29119 for: test organization (3-layer process model → organizational test strategy; project test plans per level; test execution processes). Used ISO 26262 for: specific coverage targets (branch coverage for ASIL B; MC/DC for ASIL D). Combined: test plan references both standards; test techniques selected per ISO 29119-4; coverage targets per 26262. |
| **Test levels applied** | Unit test (SWE.4; developer; white-box: branch coverage ≥ 100%); Integration test (SWE.5; independent tester; interface testing per architecture; state transition); System test (SYS.4; independent test team; black-box: requirement-based; EP+BVA); Qualification (SWE.6; independent; back-to-back testing; fault injection) |
| **Result** | Assessor (ASPICE) satisfied: test processes well-organized and documented per ISO 29119 framework. Safety auditor (ISO 26262) satisfied: coverage targets met; traceability complete; independence levels correct. |

### 9.2 Financial Services: Agile Testing with ISO 29119 Compliance

| Aspect | Detail |
|--------|--------|
| **Context** | Banking platform; 12 Scrum teams; regulatory requirement to demonstrate "structured testing approach" |
| **Challenge** | Regulators expect documented test process (ISO 29119 compliance) but teams are Agile (minimal documentation) |
| **Approach** | Mapped Agile practices to ISO 29119 requirements: |

| ISO 29119 Requirement | Agile Implementation |
|:---:|---|
| Test Policy (29119-3) | One-page testing principles in Wiki; living document; reviewed quarterly |
| Test Strategy (29119-3) | Per-product testing approach in Confluence; test levels; automation strategy; tools |
| Test Plan (29119-3) | Per-sprint: Definition of Done includes test criteria; test approach in sprint planning |
| Test Design (29119-2) | BDD scenarios (Given-When-Then) = test design specifications; reviewed in refinement |
| Test Execution (29119-2) | CI pipeline runs automated tests; manual exploratory testing per sprint |
| Test Reporting (29119-3) | Automated: CI dashboard (pass/fail; coverage); Sprint report (defects; coverage; risk) |
| Test Completion (29119-3) | Release report auto-generated: test pass rate; coverage; open defects; risk assessment |

**Result:** Audit passed. Auditor accepted Agile artifacts as valid ISO 29119 documentation (substance over form).

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact on ISO 29119 |
|-------|----------|---------------------|
| **AI in testing** | 2024+ (now) | AI test generation; self-healing tests; visual testing AI; coverage optimization |
| **Continuous testing** | Now | Testing fully integrated in CI/CD; ISO 29119 must accommodate "testing is always happening" |
| **Shift-left + shift-right** | Now | Testing earlier (design reviews; static analysis) AND later (production monitoring; chaos engineering) |
| **Model-based testing** | Growing | Auto-generate tests from models; formalize approach in future ISO 29119 revision |
| **Performance engineering** | 2024+ | Performance testing as code; continuous performance validation; SRE integration |
| **Security testing standardization** | 2024-2027 | SAST/DAST/SCA integration; DevSecOps testing practices |
| **Test automation maturity model** | Ongoing | How to assess automation maturity (separate model or integrated into 29119) |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What are the 5 parts of ISO/IEC/IEEE 29119? What does each cover?

**A:**

| Part | Title | Covers |
|:----:|:-----:|--------|
| 1 | Concepts & Definitions | Testing vocabulary; concepts; context; types; levels |
| 2 | Test Processes | 3-layer process model (organizational → management → dynamic) |
| 3 | Test Documentation | Templates for: policy, strategy, plan, specs, reports |
| 4 | Test Techniques | 23 techniques: specification-based, structure-based, experience-based |
| 5 | Keyword-Driven Testing | Framework for keyword-based test automation |

**Q2:** Explain the difference between test types and test levels.

**A:**
- **Test levels** = WHEN you test (unit → integration → system → acceptance); related to development phase
- **Test types** = WHAT aspect you test (functional; non-functional; structural; change-related); can apply at any level

Example: You can do **performance testing** (type) at **system level** (level) OR at **component level** (level).

### Tier 2: Mid-Level

**Q3:** Design a risk-based test strategy for a medical device software (IEC 62304 Class C). How do you determine test coverage?

**A:**

| Step | Activity | Output |
|:----:|----------|--------|
| 1 | **Risk identification** | List all functions/features; identify what can go wrong (harm to patient; data loss; incorrect dosage) |
| 2 | **Risk assessment** | Rate each risk: Severity (IEC 62304: death, serious injury, minor injury, no harm) × Probability (exposure × likelihood) = Risk Level |
| 3 | **Risk classification** | Map to test effort: Unacceptable risk → maximum testing; ALARP → proportionate testing; Acceptable → minimal testing |
| 4 | **Coverage targets** | Class C (highest safety): Branch coverage ≥ 100%; MC/DC for critical algorithms; BVA + EP for all inputs; negative testing (invalid inputs, boundary violations); fault injection for error handling |
| 5 | **Technique selection** | Critical paths: Decision Table + State Transition + BVA + Error Guessing. Standard paths: EP + Use Case. Low-risk: Exploratory + Checklist |
| 6 | **Independence** | Class C: Testing must be independent of development (separate team or at minimum separate person) |

### Tier 3: Senior

**Q4:** How would you implement a test process for a continuous delivery environment (50 deployments/day) while maintaining ISO 29119 compliance?

**A:**

**Principles:**
1. "Test process" = automated pipeline (not human-driven process)
2. "Documentation" = code and dashboards (not Word documents)
3. "Test management" = pipeline orchestration + monitoring

**Mapping:**

| ISO 29119 Layer | CD Implementation |
|:---:|---|
| **Organizational Test Process** | Test strategy defined in Git (README + ADRs); quality gates defined as pipeline stages; organizational test policy in wiki |
| **Test Management** | Pipeline configuration = test plan (what runs, when, criteria); Dashboards = monitoring & control; Release candidate criteria = exit criteria (automated gate) |
| **Dynamic Test Process** | Pre-commit: linting + unit tests (developer local). PR merge: full unit + integration + contract tests. Staging: E2E + performance + security scan. Production: canary + monitoring + alerting |

**Quality gates (automated exit criteria):**

| Gate | Criteria | Action if Fail |
|:----:|----------|:---:|
| PR merge | Unit tests pass; coverage ≥ 80%; no critical SAST findings; PR reviewed | Block merge |
| Staging deploy | Integration tests pass; E2E pass; performance within baseline ±10%; security scan clean | Block promotion |
| Production canary | Error rate < 0.1%; latency P99 within SLO; no critical errors in logs | Auto-rollback |
| Production full | 24h monitoring: all SLOs met; no regression in key metrics | Alert; investigate |

**Documentation compliance:**
- Test strategy: `docs/testing/strategy.md` in Git (versioned; reviewed; traceable)
- Test plan per release: auto-generated from pipeline config + test inventory
- Test results: CI system (Jenkins/GitHub Actions) logs = test execution records
- Test completion: automated release report (coverage; pass rates; open defects; risk assessment)

---

## Chapter 12 — Cheat Sheet & Quick Reference

```
═══════════════════════════════════════════
ISO/IEC/IEEE 29119 — QUICK REFERENCE
═══════════════════════════════════════════

5 PARTS:
  Part 1: Concepts & Definitions
  Part 2: Test Processes (3-layer model)
  Part 3: Test Documentation (templates)
  Part 4: Test Techniques (23 techniques)
  Part 5: Keyword-Driven Testing

═══════════════════════════════════════════
3-LAYER PROCESS MODEL:
  Layer 1: Organizational Test Process
    (policy + strategy + governance)
  Layer 2: Test Management Process
    (planning + monitoring + completion)
  Layer 3: Dynamic Test Process
    (design + implementation + execution + reporting)

═══════════════════════════════════════════
TEST LEVELS:
  Unit → Integration → System → Acceptance

TEST TYPES:
  Functional | Non-functional | Structural | Change-related

═══════════════════════════════════════════
DOCUMENTATION HIERARCHY:
  Policy → Strategy → Plan → Specification → Results → Completion Report

═══════════════════════════════════════════
TEST TECHNIQUES (KEY):
  Specification-based (Black-box):
    • Equivalence Partitioning (EP)
    • Boundary Value Analysis (BVA)
    • Decision Table
    • State Transition
    • Pairwise/Classification Tree
    • Use Case Testing
  
  Structure-based (White-box):
    • Statement Coverage
    • Branch/Decision Coverage
    • Condition Coverage
    • MC/DC (Modified Condition/Decision)
    • Path Coverage
  
  Experience-based:
    • Exploratory Testing
    • Error Guessing
    • Checklist-Based

═══════════════════════════════════════════
COVERAGE HIERARCHY:
  Statement ⊂ Branch ⊂ Condition ⊂ MC/DC ⊂ Path

SAFETY-CRITICAL COVERAGE:
  ASIL D / DAL A: MC/DC
  ASIL B / DAL B: Branch + Statement
  ASIL A / DAL C: Statement

═══════════════════════════════════════════
RISK-BASED TESTING:
  Risk = Likelihood × Impact
  High risk → more techniques; more coverage; independent
  Low risk → sampling; exploratory; basic coverage

═══════════════════════════════════════════
EXIT CRITERIA (COMMON):
  • 100% planned tests executed
  • Test pass rate ≥ 95%
  • 0 Critical/0 High defects open
  • Coverage targets met
  • Risk assessment: acceptable residual risk
```

---

*End of Document — 06_Testing_ISO_29119.md*
