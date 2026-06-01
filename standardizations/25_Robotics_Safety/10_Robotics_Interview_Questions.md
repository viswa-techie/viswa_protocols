# Robotics Safety Interview Questions — All Levels

**Category:** 25 — Robotics Safety  
**Document:** 10 — Interview Questions (Graduate to Chief Robotics Safety Engineer)  
**Scope:** Comprehensive question bank covering all robotics safety standards  
**Audience:** Candidates and interviewers for robotics safety roles  
**Prerequisites:** Documents 00-09 of this category

---

## Chapter 1 — Role Hierarchy & Competency Framework

### 1.1 Role Levels

| Level | Title | Experience | Core Competency |
|-------|-------|-----------|-----------------|
| L1 | Graduate / Junior Safety Engineer | 0-2 years | Standards awareness, basic risk assessment |
| L2 | Safety Engineer | 2-5 years | Apply standards, perform calculations, validate |
| L3 | Senior Safety Engineer | 5-8 years | Lead risk assessments, design safety systems, certify |
| L4 | Lead / Principal Safety Engineer | 8-12 years | Architecture decisions, multi-standard integration, innovation |
| L5 | Chief Robotics Safety Engineer / Director | 12+ years | Strategy, regulatory influence, organizational safety culture |

### 1.2 Competency Areas

| Area | Standards | Documents (This Category) |
|------|-----------|--------------------------|
| A: Industrial Robot Safety | ISO 10218, ANSI/RIA R15.06 | 01 |
| B: Collaborative Robot Safety | ISO/TS 15066 | 02 |
| C: Safety Control Systems | ISO 13849, IEC 62061 | 03, 07 |
| D: Mobile Robot Safety (AMR/AGV) | ISO 3691-4, ANSI B56.5 | 04 |
| E: Personal Care Robots | ISO 13482, IEC 60601 | 05 |
| F: Software & Cybersecurity | ROS 2, SROS2, IEC 62443 | 06 |
| G: Functional Safety | IEC 61508, IEC 62061 | 07 |
| H: EU Regulation | 2023/1230, EU AI Act | 08 |
| I: Incident Investigation | Case studies, root cause | 09 |

---

## Chapter 2 — Level 1: Graduate / Junior Safety Engineer

### 2.1 Area A: Industrial Robot Safety

1. What is the difference between ISO 10218-1 and ISO 10218-2?
2. Name the four collaborative operation methods defined in ISO 10218.
3. What does "safeguarded space" mean in the context of an industrial robot cell?
4. What is an enabling device and when is it required?
5. Explain the difference between an emergency stop and a protective stop.

### 2.2 Area B: Collaborative Robots

6. What is ISO/TS 15066 and why is it a Technical Specification (not a full standard)?
7. Define Power and Force Limiting (PFL) as a collaborative method.
8. What body regions does ISO/TS 15066 Table A.2 define force limits for?
9. What is the maximum recommended TCP speed for a PFL cobot near a human?
10. Explain the difference between transient and quasi-static contact.

### 2.3 Area C: Safety Control Systems

11. What does PLr stand for and how many levels exist (a-e)?
12. Name the five categories in ISO 13849 (B, 1, 2, 3, 4).
13. What is MTTF_d and why is it important for PL calculation?
14. What does DC (Diagnostic Coverage) measure?
15. What software tool is used for ISO 13849 PL calculations?

### 2.4 Area D: Mobile Robots

16. What is the difference between an AGV and an AMR?
17. Name the three detection zones for an AMR (protective, warning, slowdown).
18. What stop categories (0, 1, 2) are defined in IEC 60204-1?
19. What sensor type is most commonly used for AMR protective fields?
20. What happens when an AMR loses communication with its fleet controller?

### 2.5 Area E: Personal Care Robots

21. What are the three types of personal care robots in ISO 13482?
22. Why can't ISO 10218 be used for home robots?
23. What is the key difference between ISO 13482 and IEC 60601-1?

### 2.6 Area F: Software/Cybersecurity

24. What is DDS in the context of ROS 2?
25. What three protections does SROS2 provide?
26. Why can't ROS 2 on standard Linux be safety-certified?

### 2.7 Area G: Functional Safety

27. What is SIL and how many levels are defined for machinery?
28. What is a safety function? Give three examples for robots.
29. Explain the difference between SIL 1 and SIL 3 in terms of risk reduction.

### 2.8 Area H: EU Regulation

30. When does the new EU Machinery Regulation 2023/1230 become mandatory?
31. What is the difference between a Directive and a Regulation?
32. What marking indicates conformity to EU machinery requirements?

### 2.9 Area I: Incidents

33. Who was Robert Williams and why is his case historically significant?
34. Name three common root causes of robot-related fatalities.
35. What is LOTO (Lockout/Tagout) and why is it critical?

---

## Chapter 3 — Level 2: Safety Engineer (2-5 Years)

### 3.1 Area A: Industrial Robot Safety

36. Walk through a risk assessment for a robot welding cell per ISO 12100.
37. Design the safeguarding layout for a 6-axis robot palletizing cell (what devices, where placed).
38. Explain the requirements for robot restart after a safety stop (ISO 10218-2).
39. How do you determine if an operator needs to enter the robot workspace during normal operation?
40. What are the requirements for a safety-rated soft axis limit (SLP)?

### 3.2 Area B: Collaborative Robots

41. Calculate the maximum permissible speed for a cobot TCP given: payload = 5 kg, effective mass = 8 kg, contact area = 4 cm², body region = chest.
42. Explain how to set up a Speed and Separation Monitoring (SSM) system.
43. What measurements must you perform to validate a PFL cobot installation?
44. How do you handle a situation where the cobot picks up different tools with varying masses?
45. Explain the concept of "effective mass" in ISO/TS 15066 collision calculations.

### 3.3 Area C: Safety Control Systems

46. Perform a PLr determination using the risk graph for a robot cell guard door interlock (S=?, F=?, P=?).
47. Calculate MTTF_d from B10d for a contactor used 20 times per day with B10d = 1,000,000.
48. Explain what CCF (Common Cause Failure) is and how the 65-point score is achieved.
49. How does Category 3 differ from Category 4 in practice?
50. Compare ISO 13849 and IEC 62061 — when would you choose one over the other?

### 3.4 Area D: Mobile Robots

51. Calculate the minimum protective field distance for an AMR at 2 m/s with 200 ms response time.
52. Design a sensor architecture for a bi-directional AMR in a shared warehouse.
53. Explain dynamic field switching and configure zones for: straight aisle, intersection approach, turning.
54. What are the requirements for fleet emergency stop functionality?
55. How do you validate braking performance for an AMR?

### 3.5 Area E: Personal Care Robots

56. Perform a hazard analysis for a Type B (exoskeleton) robot for elderly users.
57. How do you determine if a rehabilitation robot is a medical device (IEC 60601) or personal care (ISO 13482)?
58. What collision force limits apply to a Type A mobile servant robot?

### 3.6 Area G: Functional Safety

59. Explain the difference between STO, SS1, and SS2 safety functions.
60. Walk through a PFH_d calculation for a simple emergency stop circuit.
61. What is the purpose of a proof test and how often should it be performed?
62. Explain the T3 tool classification and give examples in robotics.

### 3.7 Area H: EU Regulation

63. What is a "substantial modification" under Regulation 2023/1230 and how does it affect robot integrators?
64. What are the new cybersecurity requirements in Annex I, 1.1.9?
65. When is a Notified Body required under the new Regulation?

---

## Chapter 4 — Level 3: Senior Safety Engineer (5-8 Years)

### 4.1 Area A: Industrial Robot Safety (Complex Design)

66. Design a complete safety concept for a flexible manufacturing cell with 3 robots, 2 conveyors, and operator stations.
67. How do you handle robot cell modifications (adding a new tool) without requiring full re-certification?
68. Propose a strategy for safe collaborative zones within an otherwise guarded industrial robot cell.
69. Design the safety system for a robot cell that requires operator access during automatic mode for quality inspection.
70. How do you validate safety-rated speed monitoring (SLS) at the system level?

### 4.2 Area B: Collaborative Robots (Advanced Applications)

71. Design a risk assessment and safety concept for a dual-arm cobot performing assembly with a human co-worker.
72. How should force measurement validation be performed — what instrumentation, methodology, and acceptance criteria?
73. Propose a solution for a cobot application where TS 15066 force limits cannot be met with the required payload.
74. Design a safety system for a cobot that must change between collaborative and non-collaborative modes dynamically.
75. How do you handle the quasi-static clamping hazard at the interface between cobot end-effector and workpiece fixture?

### 4.3 Area C: Safety Control Systems (Architecture)

76. Design a safety circuit achieving PL e for a robot cell combining guard doors, light curtains, and laser scanners.
77. How do you validate a safety PLC application program per ISO 13849-2?
78. Perform a complete Sistema calculation for a dual-channel e-stop circuit with EDM.
79. Design a safety system where multiple robots share a common safeguarded zone with managed access.
80. How do you handle diversity requirements for CCF when both channels use the same PLC manufacturer?

### 4.4 Area D: Mobile Robots (Fleet & Complex Environments)

81. Design a safety architecture for a fleet of 50 AMRs in a shared human-robot warehouse.
82. How do you certify an AMR that uses AI-based person detection as its primary safety sensor?
83. Propose safety measures for an AMR transitioning between indoor (warehouse) and outdoor (yard) operation.
84. Design a multi-vehicle intersection management system with safety guarantees.
85. How do you perform a risk assessment for a new AMR deployment where historical data is unavailable?

### 4.5 Area F: Software/Cybersecurity

86. Design a complete safety architecture combining ROS 2 navigation with a certified safety controller.
87. How would you implement and validate a safety watchdog for ROS 2 nodes?
88. Propose a testing methodology for the safety gateway between ROS 2 (QM) and the safety domain (SIL 2).
89. How do you address cybersecurity threats to robot safety functions per IEC 62443?
90. Design a secure OTA update mechanism that cannot compromise robot safety.

### 4.6 Area G: Functional Safety

91. Perform a complete SIL determination and PFH calculation for a collaborative robot safety system.
92. Design a fault injection test protocol for a Category 4 safety circuit.
93. How do you qualify a third-party safety component (sensor) for use in your SIL 2 system?
94. Propose a proof testing strategy for a 24/7 robot operation where downtime is costly.
95. Design a safety architecture using 1oo2D with diverse processing for a surgical robot.

### 4.7 Area H: EU Regulation & Certification

96. Lead a certification project: plan the conformity assessment for a novel cobot product under 2023/1230.
97. How do you address the EU AI Act requirements for a robot using ML-based safety monitoring?
98. Design the technical documentation package for a personal care robot targeting EU market.

### 4.8 Area I: Incident Investigation

99. Design a commissioning safety plan that prevents incidents like the VW Baunatal case.
100. Propose a safety incident investigation methodology for a cobot facility.

---

## Chapter 5 — Level 4: Lead / Principal Safety Engineer (8-12 Years)

### 5.1 Architecture & Strategy Questions

101. Design a universal safety architecture framework applicable to industrial, collaborative, and mobile robots from the same product family.
102. Propose a methodology combining ISO 13849 subsystems with IEC 62061 for a multi-technology, multi-vendor robot cell.
103. How should functional safety standards evolve to address ML-based robot safety functions that adapt over time?
104. Design a digital twin-based approach for continuous PL/SIL validation of degrading safety components.
105. Propose a cross-standard harmonization framework for a flexible manufacturing system containing industrial robots, cobots, and AMRs.

### 5.2 Innovation & Future Technology

106. How should ISO 13849 and IEC 62061 be revised to handle safety functions implemented partly in AI?
107. Design a safety architecture for a surgical robot that must achieve SIL 3 while using computer vision for guidance.
108. Propose a predictive maintenance strategy integrated with functional safety (predict failures before SIL degrades).
109. How would you certify a swarm of micro-robots operating collectively for a medical procedure?
110. Design a safety-as-a-service platform for SMEs deploying cobots — what is certified centrally vs. per-installation?

### 5.3 Organizational & Multi-Disciplinary

111. How do you build a functional safety competency program for a robotics company growing from 50 to 500 engineers?
112. Propose a safety management system (per IEC 61508 Part 1 Clause 6) for a robot integrator with 20 simultaneous projects.
113. Design an investigation and learning system that captures near-misses across a global fleet of 10,000 AMRs.
114. How do you manage the interface between robot OEM safety certification and integrator-level system certification?
115. Propose a method for continuous regulatory compliance monitoring as standards evolve (ISO revisions, EU regulation changes).

### 5.4 Multi-Standard Integration

116. A customer requires compliance with ISO 10218 (robot), ISO 3691-4 (AMR), IEC 62443 (cybersecurity), and EU AI Act simultaneously. Design the unified compliance approach.
117. How do you handle contradictions between national regulations (OSHA USA) and international standards (ISO 10218) for a global product?
118. Design a conformity assessment strategy for a robot sold in EU (CE/2023-1230), UK (UKCA), USA (OSHA/ANSI), Japan (JIS), and China (GB).
119. Propose an approach to harmonize ISO 13482 (personal care robot) and IEC 60601-1 (medical device) for a dual-purpose assistive robot.
120. How should safety certification handle a robot that receives regular software updates via OTA while maintaining its original type-examination certificate?

---

## Chapter 6 — Level 5: Chief Robotics Safety Engineer / Director

### 6.1 Strategic & Regulatory Influence

121. You are advising ISO/TC 299 on the next generation of robot safety standards. What fundamental changes would you propose?
122. How should the robotics industry collectively respond to the EU AI Act's requirements for high-risk AI in safety functions?
123. Design a global regulatory harmonization strategy for autonomous robots operating across multiple jurisdictions.
124. Propose a framework for "continuous certification" replacing point-in-time type examination for learning robots.
125. How should liability frameworks evolve for robots that cause harm through learned behavior not foreseeable at certification time?

### 6.2 Industry Transformation

126. Design the safety culture transformation program for a traditional industrial robot company transitioning to collaborative and autonomous systems.
127. How do you balance innovation speed with safety assurance in a competitive market where time-to-market is critical?
128. Propose an industry consortium model for sharing safety-critical component failure data across robot manufacturers.
129. Design a certification framework for open-source safety software (e.g., making parts of ROS 2 certifiable).
130. How should safety engineering education evolve to prepare the next generation for AI-era robotics?

### 6.3 Emerging Technology Safety

131. What safety framework should govern humanoid robots operating in public spaces?
132. Propose safety requirements for brain-computer interface (BCI) controlled exoskeletons.
133. How should safety standards address quantum-computing-controlled robots (future)?
134. Design a safety governance model for autonomous construction robots operating on dynamic worksites.
135. Propose safety architecture requirements for general-purpose household robots with manipulation capabilities.

### 6.4 Ethics & Societal Impact

136. How should safety engineers handle the ethical dilemma when maximum productivity requires accepting higher residual risk?
137. Propose a framework for "acceptable risk" in personal care robots for vulnerable populations (elderly, children, disabled).
138. How should liability be allocated between robot OEM, integrator, and operator when safety relies on AI decisions?
139. Design transparency requirements for AI-based safety functions: what must be explainable, to whom, and when?
140. How should the profession handle the potential for "safety washing" — manufacturers claiming safety compliance while minimizing investment?

---

## Chapter 7 — Scenario-Based Questions (Any Level)

### 7.1 Design Scenarios

| # | Scenario | Target Level |
|---|----------|-------------|
| S1 | Design a safety system for a robot performing surgery through a 5mm port | L3-L4 |
| S2 | An AMR fleet operates in a hospital: patients, visitors, wheelchairs, IV poles | L3-L4 |
| S3 | A cobot must hand a sharp object (scalpel, glass panel) to a human | L2-L3 |
| S4 | Three cobots share a workspace; human moves between them | L3-L4 |
| S5 | Robot operates underwater (ROV) with remote operator; fiber optic fails | L2-L3 |
| S6 | Agricultural robot sprays pesticide autonomously in open field near public path | L3-L4 |
| S7 | Humanoid robot guides visitors in a museum; children present | L2-L3 |
| S8 | Exoskeleton user falls down stairs while wearing the device | L3-L4 |
| S9 | AMR carrying lithium batteries catches fire in warehouse | L3-L4 |
| S10 | Cobot system receives an OTA update that changes its speed profile | L4-L5 |

### 7.2 Troubleshooting Scenarios

| # | Scenario | Expected Analysis |
|---|----------|-------------------|
| T1 | Safety laser scanner gives false positives in dusty environment | Sensor selection, environmental rating, filter configuration |
| T2 | Cobot occasionally exceeds force limit by 10% for 20ms | Measurement methodology, transient vs. quasi-static, inertia compensation |
| T3 | Safety PLC reports intermittent Channel B fault | Wiring check, EMC, connector quality, environmental |
| T4 | AMR fleet has 3 near-misses per week at same intersection | Zone design, traffic management, visibility, sensor coverage |
| T5 | After firmware update, robot e-stop response time increased from 50ms to 120ms | Version control, regression testing, safety function validation |

---

## Chapter 8 — Practical Exercises

### 8.1 Take-Home Assignments (Typical for Senior Roles)

| # | Exercise | Duration | Deliverable |
|---|----------|----------|-------------|
| E1 | Perform risk assessment for provided robot cell layout (drawing given) | 4-8 hours | Risk assessment document per ISO 12100 |
| E2 | Calculate PL for provided safety circuit (component data given) | 2-4 hours | Sistema file or hand calculation + report |
| E3 | Write safety requirements specification for a new cobot product | 4-8 hours | Safety requirement document |
| E4 | Review provided safety concept and identify gaps | 2-4 hours | Gap analysis report with recommendations |
| E5 | Design validation test plan for safety functions | 4-6 hours | Test protocol document |

### 8.2 Whiteboard Exercises (Interview)

| # | Exercise | Duration | Assessment Criteria |
|---|----------|----------|-------------------|
| W1 | Draw safety architecture for a 2-robot cell with operator access | 30 min | Completeness, standard compliance, practicality |
| W2 | Sketch state machine for cobot safety modes | 20 min | Mode transitions, failure states, recovery |
| W3 | Calculate stopping distance and protective field size | 15 min | Correct formula, reasonable assumptions |
| W4 | Draw fault tree for "Robot strikes operator" top event | 30 min | Logic gates, basic events, independence |
| W5 | Design sequence diagram for collaborative operation mode switching | 20 min | Timing, safety signals, human factors |

---

## Chapter 9 — Answer Evaluation Criteria

### 9.1 Scoring Rubric

| Score | Description | Indicators |
|-------|-------------|------------|
| 5 — Expert | Exceptional depth; references specific standard clauses; provides design alternatives; considers edge cases | Cites clause numbers, provides quantitative examples, discusses trade-offs |
| 4 — Strong | Comprehensive answer; correct standard application; practical considerations | Applies correct standard, reasonable design, minor gaps only |
| 3 — Adequate | Correct direction; applies relevant standard; some gaps in detail | Identifies correct standard, basic application, missing nuances |
| 2 — Basic | Partial understanding; misses key aspects; generic answer | Knows standard exists, cannot apply correctly, conceptual errors |
| 1 — Insufficient | Fundamental misunderstanding or inability to address | Wrong standard, incorrect concepts, no practical knowledge |

### 9.2 Red Flags (Immediate Concerns)

| Red Flag | Implication |
|----------|------------|
| "We can bypass the safety during setup" | Does not understand commissioning safety |
| "PL c is enough for all robot applications" | Inadequate risk assessment understanding |
| "Cobots don't need risk assessment" | Fundamental misconception |
| "Linux is real-time so it's fine for safety" | Does not understand certification requirements |
| "We can use AI for the emergency stop function" | Does not understand deterministic safety requirements |
| "The robot manufacturer certifies everything" | Misunderstands integrator responsibility |

### 9.3 Green Flags (Positive Indicators)

| Green Flag | Implication |
|------------|------------|
| References specific clause numbers without prompting | Deep standard knowledge |
| Discusses residual risk and ALARP | Mature risk assessment thinking |
| Mentions validation after every modification | Lifecycle awareness |
| Considers maintenance and proof testing upfront | Practical experience |
| Asks clarifying questions about application context | Engineering judgment |
| Discusses trade-offs between safety levels and project constraints | Commercial awareness |

---

## Chapter 10 — Study Resources & Preparation Guide

### 10.1 Essential Reading

| Resource | Level | Coverage |
|----------|-------|----------|
| ISO 10218-1:2011 (full text) | L1-L5 | Industrial robot safety |
| ISO 10218-2:2011 (full text) | L1-L5 | Robot system integration |
| ISO/TS 15066:2016 (full text) | L2-L5 | Collaborative operation |
| ISO 13849-1:2023 (full text) | L2-L5 | Safety control systems |
| IEC 61508 (Parts 1-7) | L3-L5 | Functional safety foundation |
| ISO 3691-4:2020 | L2-L4 | AMR/AGV safety |
| ISO 13482:2014 | L2-L4 | Personal care robots |
| EU Regulation 2023/1230 (Official Journal text) | L3-L5 | EU legal framework |
| EU AI Act 2024/1689 | L4-L5 | AI regulation |

### 10.2 Recommended Certifications

| Certification | Provider | Level | Focus |
|--------------|----------|-------|-------|
| CFSE (Certified Functional Safety Engineer) | TÜV Rheinland | L3+ | IEC 61508 / 62061 |
| CMSE (Certified Machinery Safety Expert) | TÜV Nord + Pilz | L2-L3 | ISO 13849, machinery safety |
| Functional Safety Engineer (TÜV SÜD) | TÜV SÜD | L3+ | IEC 61508 / ISO 26262 |
| CRIS (Certified Robot Integrator Safety) | RIA (A3) | L2-L3 | ISO 10218 / RIA R15.06 |
| CMSP (Certified Machine Safety Professional) | TÜV Rheinland | L2-L3 | General machinery safety |

### 10.3 Preparation Strategy by Level

| Level | Focus Areas | Practice |
|-------|-------------|----------|
| L1 | Memorize standard numbers, scopes, key definitions | Flashcards, standard summaries |
| L2 | Apply calculations (PL, PFH, stopping distance) | Practice problems, Sistema exercises |
| L3 | Design complete safety systems, lead assessments | Case studies, real project experience |
| L4 | Multi-standard integration, innovation, architecture | White papers, conference presentations |
| L5 | Strategy, regulation, industry leadership | Committee participation, published thought leadership |

---

*Document Version: 1.0 | Last Updated: May 2026 | Author: Robotics Safety Standards Team*
