# UCIe (Universal Chiplet Interconnect Express) — REFERENCES & STUDY PLAN
# ════════════════════════════════════════════════════════════════════
# Protocol: UCIe | Document: 08 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. PRIMARY REFERENCES

### Official Specifications
| Resource | Description |
|----------|-------------|
| UCIe 1.0 Specification (March 2022) | Full protocol, adapter, PHY spec |
| UCIe 1.1 Specification (August 2023) | Updates: retimer, streaming, flit mode |
| UCIe Consortium Website (uciexpress.org) | Membership, news, spec access |
| UCIe Compliance Test Specification | Interoperability testing methodology |

### Related Specifications
| Resource | Description |
|----------|-------------|
| CXL 3.0/3.1 Specification | Protocol layer (CXL over UCIe) |
| PCIe 6.0 Specification | Protocol layer (PCIe over UCIe) |
| OCP BoW (Bunch of Wires) | Alternative D2D PHY spec (simpler) |
| Intel AIB/AIB2 Specification | Predecessor D2D standard from Intel |

### Industry Publications
| Resource | Description |
|----------|-------------|
| Hot Chips: UCIe Introduction (2022) | Architecture presentation by consortium |
| ISSCC: D2D interconnect papers | Circuit-level implementations |
| IEEE JSSC: SerDes for D2D | PHY design papers |
| IEDM: Advanced packaging papers | Bump technology, hybrid bonding |
| ECTC (Electronic Components & Tech Conf) | Packaging technology papers |

---

## 2. PACKAGING TECHNOLOGY REFERENCES

| Technology | Key Source |
|-----------|-----------|
| TSMC CoWoS | TSMC Technology Symposium papers |
| TSMC InFO | TSMC Advanced Packaging documentation |
| TSMC SoIC (hybrid bond) | TSMC 3DFabric documentation |
| Intel EMIB | Intel Technology publications |
| Intel Foveros | Intel Labs publications |
| Samsung I-Cube / X-Cube | Samsung Foundry documentation |
| ASE FOEB | ASE advanced packaging papers |

---

## 3. TOOLS & ECOSYSTEM

| Tool/Resource | Purpose | Source |
|---------------|---------|--------|
| Synopsys UCIe IP | UCIe PHY + controller IP core | Synopsys |
| Cadence UCIe IP | UCIe PHY + adapter IP | Cadence |
| Alphawave UCIe IP | UCIe PHY silicon-proven IP | Alphawave |
| Rambus UCIe IP | UCIe controller + PHY | Rambus |
| Ansys/HFSS | EM simulation for bump/trace design | Ansys |
| Cadence Allegro | Package design & routing | Cadence |
| Synopsys ICC2 | Die-level physical design | Synopsys |
| SPICE simulation | AFE design verification | Various |
| Protocol analyzers | UCIe compliance testing | Keysight, Tektronix |

---

## 4. COMMUNITY & INDUSTRY RESOURCES

| Resource | Description |
|----------|-------------|
| UCIe Consortium blog | Official announcements, member updates |
| Semiconductor Engineering | UCIe coverage, expert articles |
| WikiChip | Chiplet technology database |
| AnandTech / Tom's Hardware | Product-level chiplet coverage |
| Chips and Cheese | Technical deep dives |
| EE Times | Industry news on D2D |
| OCP (Open Compute Project) | BoW spec, chiplet working groups |
| DARPA CHIPS program | Government-funded chiplet research |
| IEEE conferences (ISSCC, VLSI, IEDM) | Academic D2D research |

---

## 5. 10-DAY STUDY PLAN

### Week 1: Fundamentals & Architecture

| Day | Topic | Activity |
|-----|-------|----------|
| 1 | UCIe Overview | Read docs 00+01. Understand motivation, three layers, why open standard matters |
| 2 | Physical Layer | Study bump pitches, signaling, clocking. Compare standard vs advanced packaging |
| 3 | Adapter Layer | CRC, retry, credits, FDI/RDI interfaces. Understand protocol agnosticism |
| 4 | Protocol Mapping | How CXL/PCIe/Streaming map to UCIe. Compare latency/BW vs board-level |
| 5 | Packaging | Study CoWoS, EMIB, Foveros, hybrid bonding. Understand manufacturing trade-offs |

### Week 2: Advanced & Design

| Day | Topic | Activity |
|-----|-------|----------|
| 6 | Link Training | Trace SBINIT → MBINIT → L0. Understand lane repair |
| 7 | Design Exercise | Complete Lab 1 (BW calculation) and Lab 4 (multi-chiplet system design) |
| 8 | Error Handling | Study CRC/retry (Lab 3), debug scenarios 1-4 |
| 9 | Ecosystem | Review IP vendors, EDA tools, compliance testing. Understand market landscape |
| 10 | Review & Prep | Flashcards (6 decks), interview questions, cheatsheet. Compare UCIe with competitors |

---

## 6. GLOSSARY (30 TERMS)

| Term | Definition |
|------|-----------|
| UCIe | Universal Chiplet Interconnect Express |
| D2D | Die-to-Die interconnect |
| Chiplet | Small functional die in multi-chip module |
| Module | Basic UCIe interface unit (lanes + clock + sideband) |
| Cluster | Group of modules forming a wider link |
| FDI | Flit-aware D2D Interface (protocol↔adapter boundary) |
| RDI | Raw D2D Interface (adapter↔PHY boundary) |
| Mainband | High-speed data lanes |
| Sideband | Low-speed management/control channel |
| AFE | Analog Front End (Tx/Rx circuits) |
| Beachfront | Die edge area allocated for I/O |
| BW density | Bandwidth per mm of die edge |
| Bump pitch | Center-to-center distance between bumps |
| μbump | Microbump (~10-55μm solder connection) |
| Hybrid bonding | Direct Cu-Cu connection (<2μm pitch) |
| CoWoS | TSMC's Chip-on-Wafer-on-Substrate (2.5D) |
| EMIB | Intel's Embedded Multi-die Interconnect Bridge |
| Foveros | Intel's 3D die stacking technology |
| TSV | Through-Silicon Via (vertical interconnect) |
| Si interposer | Passive silicon substrate for 2.5D routing |
| LFSR | Linear Feedback Shift Register (training pattern) |
| Lane repair | Remapping failed lanes to spares |
| CRC | Cyclic Redundancy Check (error detection) |
| Retry | Frame replay on error |
| Credit | Flow control token (buffer space unit) |
| KGD | Known Good Die (pre-tested before assembly) |
| NRZ | Non-Return-to-Zero signaling (2-level) |
| PAM4 | 4-level Pulse Amplitude Modulation |
| Clock forwarding | Transmitting clock on dedicated lane |
| MCM | Multi-Chip Module (package with multiple dies) |

---

## 7. CAREER RELEVANCE

| Role | UCIe Knowledge Value |
|------|---------------------|
| Chiplet/Package Architect | Essential — primary design standard |
| Physical Design Engineer | Critical — bump planning, PHY integration |
| ASIC Design Engineer | Important — UCIe IP integration |
| Signal Integrity Engineer | Critical — D2D channel design |
| Manufacturing/Process Engineer | Important — bump yield, assembly |
| System Architect | Important — chiplet-based SoC planning |
| EDA Tool Developer | Relevant — packaging-aware tools |
| IP Vendor (PHY/Controller) | Essential — UCIe compliance |
| Data Center Architect | Relevant — CXL chiplet memory |

---

## 8. FUTURE OUTLOOK

| Trend | Impact on UCIe |
|-------|---------------|
| Chiplet adoption accelerating | UCIe becomes mandatory standard |
| AI/ML explosive growth | Streaming UCIe for accelerator chiplets |
| CXL memory pooling | CXL over UCIe for in-package memory |
| Hybrid bonding maturation | Advanced UCIe at <2μm → massive density |
| UCIe 2.0+ | Switching, higher speeds, more protocols |
| Chiplet marketplace | Third-party UCIe chiplet IP purchasing |
| Automotive chiplets | Safety-certified chiplets over UCIe |
| 3D integration | UCIe on vertical (face-to-face) bonds |

---

END OF DOCUMENT 08 — REFERENCES & STUDY PLAN
