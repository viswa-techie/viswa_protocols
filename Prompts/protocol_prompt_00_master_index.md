# UNIVERSAL PROTOCOL KNOWLEDGE SYSTEM
# MASTER PROMPT INDEX
# ═══════════════════════════════════════════════════════════════════
#
# HOW TO USE THIS SYSTEM
# ─────────────────────────────────────────────────────────────────
# 1. Choose the protocol you want to master (e.g., "CAN FD")
# 2. Open the relevant prompt file below
# 3. Find-and-replace ALL occurrences of {{PROTOCOL_NAME}} with your protocol
# 4. Send the complete prompt to Claude (or any capable LLM)
# 5. Save the output as your protocol reference document
# 6. Repeat for each prompt file to build the full 7-doc system
#
# TOTAL DOCUMENTS PER PROTOCOL: 7
# ESTIMATED TOTAL PAGES PER PROTOCOL: 200–400 pages
# ═══════════════════════════════════════════════════════════════════

---

# DOCUMENT MAP

## DOC 01 — Master Theory & Comprehensive Learning
**File:** protocol_prompt_01_master_theory.md
**Output name:** [PROTOCOL]_01_master_theory.md
**What it generates:**
  - Complete history from invention to 2026
  - Physical layer deep dive (electrical, timing, topology)
  - Frame/packet structure at bit level
  - Arbitration & bus access mechanics
  - Error detection & recovery (exhaustive)
  - Hardware architecture (controller, PHY, SoC integration)
  - Software stack (bare metal → Linux → Android → AUTOSAR)
  - Production code examples (initialization, TX, RX, ISR, DMA)
  - Security analysis (threats, vulnerabilities, mitigations)
  - Safety analysis (ISO 26262, ASIL, FMEA)
  - Real product usage across all industries
  - Recent developments 2022–2026
  - Migration & interoperability guide
**Size estimate:** 80–120 pages
**Use for:** Deep learning, production reference, architecture decisions

---

## DOC 02 — Complete Diagrams & Visual Reference
**File:** protocol_prompt_02_diagrams.md
**Output name:** [PROTOCOL]_02_diagrams.md
**What it generates:**
  - Protocol family tree (Mermaid)
  - OSI layer mapping (Mermaid)
  - Physical topology diagram (Mermaid)
  - All frame types at bit/byte level (ASCII art)
  - Bit timing diagram (ASCII art)
  - Hardware block diagram (Mermaid)
  - Complete software stack (Mermaid)
  - Initialization sequence (Mermaid sequenceDiagram)
  - TX/RX data flow (Mermaid sequenceDiagram)
  - Error state machines (Mermaid stateDiagram)
  - Arbitration sequence (Mermaid)
  - Register memory map (table)
  - DMA flow (Mermaid)
  - Linux kernel architecture (Mermaid)
  - AUTOSAR stack (Mermaid)
  - Android HAL stack (Mermaid)
  - Security threat model (Mermaid)
  - Performance comparison (table)
  - System integration example (Mermaid)
  - Migration path diagram (Mermaid)
  - Debugging decision tree (Mermaid flowchart)
  - Protocol interaction knowledge graph (Mermaid)
**Size estimate:** 30–50 pages
**Use for:** Visual learning, presentations, whiteboard prep, architecture diagrams

---

## DOC 03 — Complete Interview Questions
**File:** protocol_prompt_03_interview_questions.md
**Output name:** [PROTOCOL]_03_interview_questions.md
**What it generates:**
  - 20 Junior-level Q&A with model answers
  - 25 Mid-level Q&A with model answers
  - 25 Senior-level Q&A with model answers
  - 20 Staff/Principal/Architect Q&A
  - 15 Debugging scenarios (full worked answers)
  - 10 Code review questions (find the bug)
  - 10 System design questions (full model answers)
  - 15 Behavioral/STAR interview questions
  - 15 Trick questions & common misconceptions
  - 10 Gotcha questions (trips up senior engineers)
  - 30 Quick-fire questions with 60-second answers
  - Interview strategy guide
  - 10 Questions to ask the interviewer
  - 8 Red-line topics (instant failure indicators)
**Size estimate:** 60–90 pages
**Use for:** Interview preparation at all levels

---

## DOC 04 — Flash Card System
**File:** protocol_prompt_04_flashcards.md
**Output name:** [PROTOCOL]_04_flashcards.md
**What it generates:**
  - Deck 1: Identity & Metadata (20 cards)
  - Deck 2: Physical Layer (25 cards)
  - Deck 3: Frame Structure (30 cards)
  - Deck 4: Arbitration & Timing (20 cards)
  - Deck 5: Error Handling (25 cards)
  - Deck 6: Hardware Architecture (20 cards)
  - Deck 7: Linux Kernel (25 cards)
  - Deck 8: Software Programming (20 cards)
  - Deck 9: Performance & Calculations (15 cards)
  - Deck 10: Security & Safety (15 cards)
  - Deck 11: Debugging (20 cards)
  - Deck 12: Comparisons & Tradeoffs (20 cards)
  - Deck 13: AUTOSAR (15 cards, automotive only)
  - Deck 14: History & Evolution (15 cards)
  - Deck 15: Formula Cards (10 cards)
  - Deck 16: Analogy & Mental Model (10 cards)
  - TOTAL: 300+ cards
**Size estimate:** 40–60 pages
**Use for:** Daily spaced repetition, last-minute revision, concept memorization
**Compatible with:** Anki (can be imported as CSV)

---

## DOC 05 — Behavioral Question Cards
**File:** protocol_prompt_05_behavioral_cards.md
**Output name:** [PROTOCOL]_05_behavioral_cards.md
**What it generates:**
  - 43 full behavioral question cards (STAR format)
    - 8 Debugging & Problem-Solving
    - 8 Architecture & Design
    - 6 Performance & Optimization
    - 6 Collaboration & Communication
    - 5 Learning & Growth
    - 5 Safety & Compliance
    - 5 Bring-up & Integration
  - Story Preparation Worksheets (3 worksheets)
  - Interviewer Lens Cards (8 scoring rubrics)
  - 20 Anti-Pattern Cards (what NOT to say)
**Size estimate:** 50–70 pages
**Use for:** Behavioral interview preparation, story crafting, self-assessment

---

## DOC 06 — Cheatsheet & Quick Reference
**File:** protocol_prompt_06_cheatsheet.md
**Output name:** [PROTOCOL]_06_cheatsheet.md
**What it generates:**
  - 60-second overview card
  - Physical layer quick reference (tables)
  - Frame structure reference (all frames, hex examples)
  - Timing & baud rate formulas (with worked examples)
  - Key registers quick reference
  - Linux command reference (complete command set)
  - Code snippets library (8 ready-to-use templates)
  - Error handling quick reference
  - Protocol comparisons at a glance
  - Debugging quick reference (symptom → cause → fix)
  - Performance quick reference
  - Interview-day revision card (10 things to remember)
**Size estimate:** 20–30 pages
**Use for:** Desk reference, interview-day revision, production debugging

---

## DOC 07 — Hands-On Labs & Debugging Playbook
**File:** protocol_prompt_07_labs_debugging.md
**Output name:** [PROTOCOL]_07_labs_debugging.md
**What it generates:**
  - Lab 01: Physical layer inspection
  - Lab 02: Software loopback on Linux
  - Lab 03: Bare metal MCU implementation
  - Lab 04: Linux kernel driver walkthrough
  - Lab 05: Protocol analyzer deep dive
  - Lab 06: Error injection & recovery testing
  - Lab 07: Performance benchmarking
  - Lab 08: QEMU simulation (no hardware)
  - 6-stage debugging framework
  - 25 failure scenario playbooks (symptom → root cause → fix)
  - Debugging tools reference
**Size estimate:** 50–70 pages
**Use for:** Practical skill building, production debugging, new hire training

---

# PROTOCOL PRIORITY ORDER

Based on career ROI for Embedded / AAOS / Semiconductor path:

## TIER 1 — Master These First (Core Protocols)
1. CAN FD
2. UART / USART
3. SPI / QSPI
4. I2C / I3C
5. Ethernet / TCP/IP
6. USB
7. PCIe
8. AXI (ARM AMBA)
9. JTAG / SWD
10. DDR Memory Interface

## TIER 2 — Master Next (Domain Specialist)
11. SOME/IP
12. UDS (Unified Diagnostic Services)
13. LIN
14. FlexRay
15. MIPI CSI/DSI
16. Binder / AIDL (Android)
17. SocketCAN (Linux)
18. CXL
19. NVMe
20. GMSL

## TIER 3 — Advanced Mastery
21. MIL-STD-1553
22. ARINC 429
23. SpaceWire
24. UCIe
25. RDMA / RoCE
26. MAVLink
27. DDS
28. EtherCAT
29. OPC UA
30. NVLink

---

# QUICK START: FIRST PROTOCOL TO DO

**Recommended first protocol: CAN FD**

Replace {{PROTOCOL_NAME}} with "CAN FD" in all 7 prompts.
Run Prompt 01 first. It generates your foundation.
Then run 02 (diagrams), 06 (cheatsheet), 04 (flashcards).
Save all outputs in a folder: /protocols/CAN_FD/

**Folder structure to maintain:**
```
/protocols/
  ├── CAN_FD/
  │   ├── CAN_FD_01_master_theory.md
  │   ├── CAN_FD_02_diagrams.md
  │   ├── CAN_FD_03_interview_questions.md
  │   ├── CAN_FD_04_flashcards.md
  │   ├── CAN_FD_05_behavioral_cards.md
  │   ├── CAN_FD_06_cheatsheet.md
  │   └── CAN_FD_07_labs_debugging.md
  ├── AXI/
  │   └── [same structure]
  ├── PCIe/
  │   └── [same structure]
  ...
```

---

# SUPPLEMENTARY PROMPTS (OPTIONAL)

These can be generated when needed:

## PROMPT 08 — AUTOSAR Deep Dive (Automotive Protocols Only)
*Generate: AUTOSAR stack for this protocol, COM/PDUR/IF module config,
signal mapping, E2E protection, Network Management, Diagnostics integration*

## PROMPT 09 — Semiconductor / RTL Deep Dive (On-Chip Protocols)
*Generate: Verilog interface spec, UVM testbench structure, synthesis constraints,
timing closure guide, CDC analysis, formal verification approach*

## PROMPT 10 — Certification & Compliance Guide
*Generate: ISO/IEEE/SAE certification steps, test specifications, tool chain,
audit checklist, DO-178C / ISO 26262 evidence requirements*

## PROMPT 11 — Production Case Studies
*Generate: 5 detailed case studies of real-world {{PROTOCOL_NAME}} usage
in Tesla / Qualcomm / NVIDIA / Airbus / ISRO / Bosch products*

## PROMPT 12 — Knowledge Graph & Concept Map
*Generate: Complete concept map of all {{PROTOCOL_NAME}} knowledge areas
and their interconnections, for Obsidian/Roam/Notion knowledge graph*

---

# NOTES ON LLM USAGE

- These prompts are optimized for Claude (Sonnet or Opus)
- For very long outputs (Prompt 01, 03, 07), break into sections if needed:
  "Generate sections 0–10 first, then I'll ask for 11–20, etc."
- Always specify: "Do not truncate. Generate the complete document."
- If output is cut off, continue with: "Continue from Section [X] where you stopped."
- Save outputs immediately — do not rely on conversation history

---

END OF MASTER INDEX
