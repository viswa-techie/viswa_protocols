# SPI — ENCYCLOPEDIA INDEX
# ════════════════════════════════════════════════════════════════════
# Protocol: SPI (Serial Peripheral Interface) | Document: 00 — Index
# ════════════════════════════════════════════════════════════════════

---

## QUICK IDENTITY CARD

| Field | Value |
|-------|-------|
| Full Name | Serial Peripheral Interface |
| Origin | Motorola, mid-1980s (68HC11 MCU) |
| Standard | No formal standard — de facto industry convention |
| Layer | Physical + Data Link (partial) |
| Topology | Master-Slave, star (shared bus with individual CS) |
| Wires | 4 (SCLK, MOSI, MISO, CS) minimum |
| Speed | 1–100+ MHz typical; 200 MHz+ possible |
| Duplex | Full-duplex (simultaneous TX/RX) |
| Addressing | Hardware chip-select (no software addressing) |
| Domain | Embedded, Automotive, Sensors, Flash, ADC/DAC, Display |

---

## DOCUMENT MAP

| # | Document | Description | Pages |
|---|----------|-------------|-------|
| 00 | **SPI_00_index.md** | This file — navigation & checklist | — |
| 01 | [SPI_01_master_theory.md](SPI_01_master_theory.md) | Complete theory: history, electrical, protocol, modes, variants, Linux, Android, AUTOSAR, security | ~600 lines |
| 02 | [SPI_02_diagrams.md](SPI_02_diagrams.md) | 20+ Mermaid/ASCII diagrams: timing, topology, state machines, stack | ~400 lines |
| 03 | [SPI_03_interview_questions.md](SPI_03_interview_questions.md) | 100+ questions: junior→staff, debugging scenarios, system design | ~500 lines |
| 04 | [SPI_04_flashcards.md](SPI_04_flashcards.md) | 250+ flashcards across 10 decks | ~500 lines |
| 06 | [SPI_06_cheatsheet.md](SPI_06_cheatsheet.md) | Desk reference: formulas, registers, code, commands | ~300 lines |
| 07 | [SPI_07_labs_debugging.md](SPI_07_labs_debugging.md) | 8 labs + 7 debug scenarios + automotive playbook | ~400 lines |
| 08 | [SPI_08_references.md](SPI_08_references.md) | Books, standards, GitHub, websites, tools, study path | ~300 lines |

---

## STUDY CHECKLIST

- [ ] Read theory doc — understand all 4 SPI modes (CPOL/CPHA)
- [ ] Study diagrams — draw timing from memory
- [ ] Attempt interview questions without looking at answers
- [ ] Review flashcards (3 passes minimum)
- [ ] Complete at least Labs 01–04
- [ ] Keep cheatsheet printed on desk
- [ ] Bookmark key references

---

## SPI vs UART — KEY DIFFERENCES AT A GLANCE

| Aspect | UART | SPI |
|--------|------|-----|
| Clock | No (async) | Yes — master provides SCLK |
| Speed | ~115 kbps typical | 1–100+ MHz |
| Wires | 2 (TX/RX) | 4+ (SCLK/MOSI/MISO/CS) |
| Duplex | Full | Full |
| Multi-device | No (point-to-point) | Yes (shared bus + CS per slave) |
| Overhead | Start/stop bits (20%) | Zero protocol overhead |
| Distance | meters (RS-232/485) | cm–1m (board-level) |
| Complexity | Simple | Moderate |

---

END OF INDEX
