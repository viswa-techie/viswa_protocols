# PROMPT 03 — COMPLETE INTERVIEW QUESTIONS DOC
# Usage: Replace every {{PROTOCOL_NAME}} with the actual protocol name before sending.
# ─────────────────────────────────────────────────────────────────────────────

You are a senior principal engineer and hiring manager at a top semiconductor /
automotive / systems company. You have personally conducted 500+ technical interviews
for roles involving {{PROTOCOL_NAME}}.

Generate a **complete interview question bank** for {{PROTOCOL_NAME}} covering
every possible question type, role level, and interview format.

Structure EXACTLY as follows:

---

# PART A — CONCEPTUAL / THEORETICAL QUESTIONS

## A1 — Junior Level (0–2 years)

Generate 20 questions. For each question:

**Q:** [The question exactly as an interviewer would ask it]

**Expected Answer:** [Complete model answer — do not abbreviate. Include all points
the interviewer expects. Written as if the candidate is speaking.]

**Red Flags If Missing:** [List 2–3 things that would cause the interviewer to
mark down the candidate's answer]

**Follow-Up:** [The most common follow-up question]

Topics to cover at junior level:
- Basic definition and purpose
- Physical characteristics (voltage, topology, connectors)
- Frame structure basics
- Baud rate / clock setup
- Simple configuration
- Basic troubleshooting (is it connected? is it powered?)
- Comparison with the 1–2 simplest alternative protocols

---

## A2 — Mid Level (2–5 years)

Generate 25 questions with same format.

Topics to cover:
- Frame structure all fields + purpose of each
- Error detection mechanisms
- Arbitration mechanics
- State machines
- Clock configuration calculations
- DMA vs interrupt tradeoffs
- Linux driver interaction
- Performance calculations
- Filter / acceptance mask configuration
- Bus loading analysis
- Node count and termination
- EMI and signal integrity basics

---

## A3 — Senior Level (5–10 years)

Generate 25 questions with same format.

Topics to cover:
- Protocol stack implementation decisions
- Clock synchronization edge cases
- Error recovery state machine deep dive
- Security vulnerabilities and mitigations
- ASIL/safety compliance approach
- Multi-master race conditions
- Real-time performance analysis (worst-case latency)
- Interrupt latency and jitter analysis
- Protocol bridging and gateways
- Firmware bring-up challenges
- Silicon errata impacts
- Power management integration
- Synchronization with other protocols
- Protocol analyzer interpretation

---

## A4 — Staff / Principal / Architect Level

Generate 20 questions with same format.

Topics to cover:
- Protocol selection justification for complex systems
- Multi-protocol system architecture
- Migration strategy from old to new protocol
- Custom protocol extension design
- Safety architecture using this protocol
- Certification and compliance approach
- Protocol stack from scratch design decisions
- Scaling to very large node counts
- Mixed-criticality system design
- Future-proofing decisions
- Bandwidth reservation and QoS design
- Verification strategy for protocol implementation

---

# PART B — DEBUGGING / SCENARIO QUESTIONS

## B1 — Debugging Scenarios (Each with full worked answer)

Generate 15 debugging scenarios. For each:

**Scenario:** [Describe an actual failure situation in 3–5 sentences. Be specific —
use real symptoms, real numbers, real conditions.]

**Initial Observations:** [What the engineer sees when they first look at the problem]

**Diagnostic Approach:** [Step-by-step what a skilled engineer does, in order]

**Root Cause:** [The actual problem]

**Fix:** [Exactly what to do]

**Prevention:** [How to avoid this in future designs]

**Interview Discussion Points:** [What the interviewer is looking for in this question]

Scenarios must cover:
1. Physical layer / termination issue
2. Baud rate mismatch
3. CRC errors under load
4. Bus-off / error state
5. Intermittent failure
6. EMI / noise-induced corruption
7. Timing / clock issue
8. Driver bug (null pointer, buffer overflow)
9. Filter/mask misconfiguration
10. DMA stall / FIFO overflow
11. Arbitration starvation / priority inversion
12. Node ID conflict
13. Multi-master collision
14. Protocol version mismatch
15. Hardware silicon errata trigger

---

## B2 — Code Review Questions

Generate 10 questions where candidate is given buggy code to find the bug.

For each:
**Code:** [Show a short C/C++ code snippet with a subtle bug]
**Question:** "What is wrong with this code? What would happen at runtime?"
**Answer:** [Bug identification + explanation + correct code]
**Learning Point:** [The deeper concept this tests]

Bug types to include:
- Off-by-one in bit manipulation
- Wrong endianness handling
- Missing volatile qualifier
- Race condition in ISR
- Integer overflow in baud calculation
- Wrong CRC polynomial
- Forgetting to clear flags before re-enabling IRQ
- Stack overflow in callback
- Incorrect DMA alignment
- Null check missing on config struct

---

# PART C — SYSTEM DESIGN QUESTIONS

## C1 — System Design (Full answer + expected discussion)

Generate 10 system design questions. For each:

**Question:** [A realistic system design challenge involving {{PROTOCOL_NAME}}]

**Scope:** [What the question is testing]

**Good Answer Structure:** [How to approach the answer — what to say first, second, third]

**Key Design Decisions:** [List the decisions the candidate should identify and trade off]

**Model Answer:** [A complete model answer that would score top marks]

**Architecture Diagram Description:** [Describe what diagram to draw on whiteboard]

**Follow-up Variations:** [3 follow-up modifications the interviewer might add]

Design questions must cover:
1. Design a {{PROTOCOL_NAME}} network for [specific real system]
2. How would you add redundancy to a {{PROTOCOL_NAME}} system?
3. How would you achieve ISO 26262 ASIL-B with {{PROTOCOL_NAME}}?
4. Design the software stack for {{PROTOCOL_NAME}} from HAL to app
5. How would you migrate a system from [old protocol] to {{PROTOCOL_NAME}}?
6. Design a multi-protocol gateway including {{PROTOCOL_NAME}}
7. How would you test/verify a {{PROTOCOL_NAME}} implementation?
8. How would you secure a {{PROTOCOL_NAME}} network against cyberattacks?
9. How would you design for 500 nodes on {{PROTOCOL_NAME}}?
10. How would you handle {{PROTOCOL_NAME}} in a mixed-criticality RTOS?

---

# PART D — BEHAVIORAL / EXPERIENCE QUESTIONS

## D1 — STAR-Format Technical Experience Questions

Generate 15 questions. For each:

**Question:** [Exactly as interviewer asks it]

**What They're Really Testing:** [Underlying competency being evaluated]

**Strong Answer Elements:** [List 5–7 elements a strong answer includes]

**Example Strong Answer:** [A realistic 250–350 word STAR answer an engineer might give]

**Red Flags:** [Answers that would concern the interviewer]

Questions must include:
1. "Tell me about a time you debugged a difficult {{PROTOCOL_NAME}} issue"
2. "Describe a project where you had to optimize {{PROTOCOL_NAME}} performance"
3. "Tell me about a time you made a wrong protocol selection and how you handled it"
4. "Describe how you brought up {{PROTOCOL_NAME}} on new hardware for the first time"
5. "Tell me about a time a {{PROTOCOL_NAME}} bug caused a production issue"
6. "Describe a time you had to explain {{PROTOCOL_NAME}} to a non-expert stakeholder"
7. "Tell me about a time you had to make a difficult tradeoff involving {{PROTOCOL_NAME}}"
8. "Describe your most complex {{PROTOCOL_NAME}} architecture design"
9. "Tell me about a time you had to debug {{PROTOCOL_NAME}} under extreme time pressure"
10. "Describe a time you improved a {{PROTOCOL_NAME}} system's reliability"
11. "Tell me about a time you mentored someone learning {{PROTOCOL_NAME}}"
12. "Describe a time you pushed back on a {{PROTOCOL_NAME}} design decision"
13. "Tell me about a time you had to learn something new about {{PROTOCOL_NAME}} quickly"
14. "Describe your experience with {{PROTOCOL_NAME}} certification/compliance"
15. "Tell me about the most innovative use of {{PROTOCOL_NAME}} you've worked on"

---

# PART E — TRICK QUESTIONS & EDGE CASES

## E1 — Common Wrong Answers (Misconceptions to Avoid)

Generate 15 entries. For each:

**Wrong Belief:** [A misconception many engineers have about {{PROTOCOL_NAME}}]

**Why People Believe It:** [Where the confusion comes from]

**The Truth:** [Correct understanding with explanation]

**How to Spot This in Interview:** [What wrong answer sounds like]

---

## E2 — Gotcha Questions (Things That Trip Up Even Senior Engineers)

Generate 10 entries. For each:

**Question:** [The tricky question]

**Why It's Tricky:** [What makes it hard]

**Common Wrong Answer:** [What most people say]

**Correct Answer:** [What the right answer is and why]

---

# PART F — QUICK-FIRE ROUND QUESTIONS

## F1 — 60-Second Answer Questions

Generate 30 questions that should be answered in under 60 seconds.
Format as simple numbered list: question → expected answer (1–3 sentences max).

Cover:
- Definitions
- Numbers (speeds, distances, frame sizes)
- Yes/No with brief justification
- Compare X vs Y in one sentence
- What is the purpose of [specific field/feature]

---

# PART G — INTERVIEW STRATEGY & TIPS

## G1 — How to Structure Your Answers for {{PROTOCOL_NAME}} Questions

Write a 300-word guide on how a candidate should structure answers for this
specific protocol's interview questions.

## G2 — Most Common Mistakes in {{PROTOCOL_NAME}} Interviews

List 10 most common mistakes candidates make, with explanation and correction.

## G3 — Questions YOU Should Ask the Interviewer

Generate 10 smart, thoughtful questions a candidate should ask the interviewer
when asked "do you have any questions?" — questions that demonstrate deep
{{PROTOCOL_NAME}} expertise.

## G4 — Red-Line Topics (Things That Instantly Fail a Candidate)

List 8 things that, if said, would cause an experienced interviewer to immediately
know the candidate is not qualified at the claimed level.

---

## FORMATTING REQUIREMENTS

- Every question clearly numbered (A1-01, A1-02, etc.)
- Model answers in blockquotes or clearly indented
- Code snippets in fenced code blocks with language tag
- All numbers and formulas clearly formatted
- Total: no artificial truncation — include all questions completely

---

END OF PROMPT 03
