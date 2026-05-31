# I2C — ENCYCLOPEDIA INDEX
# ════════════════════════════════════════════════════════════════════
# Protocol: I2C (Inter-Integrated Circuit) | Document: 00 — Index
# ════════════════════════════════════════════════════════════════════

---

## QUICK IDENTITY CARD

| Field | Value |
|-------|-------|
| Full Name | Inter-Integrated Circuit (I²C) |
| Pronunciation | "I-squared-C" or "I-two-C" |
| Origin | Philips Semiconductor (now NXP), 1982 |
| Standard | NXP UM10204 (I2C-bus specification, Rev 7, 2021) |
| Layer | Physical + Data Link |
| Topology | Multi-Master, Multi-Slave on shared bus |
| Wires | 2: SDA (data) + SCL (clock) + GND |
| Speed | 100 kHz (Std), 400 kHz (Fast), 1 MHz (Fast+), 3.4 MHz (HS), 5 MHz (UFm) |
| Duplex | Half-duplex (single data line) |
| Addressing | 7-bit (128) or 10-bit (1024) software addresses |
| Max Devices | 112 (7-bit, minus reserved) or 1008 (10-bit) |
| Error Detection | ACK/NACK per byte |
| Domain | Sensors, EEPROMs, RTCs, PMICs, touch, audio codecs, automotive |

---

## DOCUMENT MAP

| # | Document | Description |
|---|----------|-------------|
| 00 | **I2C_00_index.md** | This file — navigation & checklist |
| 01 | [I2C_01_master_theory.md](I2C_01_master_theory.md) | Complete theory: history, electrical, protocol, arbitration, clock stretching, variants, Linux, Android, AUTOSAR |
| 02 | [I2C_02_diagrams.md](I2C_02_diagrams.md) | 20+ diagrams: timing, topology, state machines, stack |
| 03 | [I2C_03_interview_questions.md](I2C_03_interview_questions.md) | 120+ questions: junior→staff, debugging, system design |
| 04 | [I2C_04_flashcards.md](I2C_04_flashcards.md) | 250+ flashcards across 10 decks |
| 06 | [I2C_06_cheatsheet.md](I2C_06_cheatsheet.md) | Desk reference: formulas, addresses, code, commands |
| 07 | [I2C_07_labs_debugging.md](I2C_07_labs_debugging.md) | 8 labs + 7 debug scenarios |
| 08 | [I2C_08_references.md](I2C_08_references.md) | Books, standards, GitHub, websites, tools |

---

## STUDY CHECKLIST

- [ ] Read theory — understand START/STOP, ACK/NACK, arbitration, clock stretching
- [ ] Study diagrams — draw read/write timing from memory
- [ ] Attempt interview questions without answers
- [ ] Review flashcards (3 passes minimum)
- [ ] Complete Labs 01–04
- [ ] Print cheatsheet for desk

---

## I2C vs UART vs SPI — AT A GLANCE

| Aspect | UART | SPI | I2C |
|--------|------|-----|-----|
| Wires | 2 (TX/RX) | 4+ (SCLK/MOSI/MISO/CS̄) | **2 (SDA/SCL)** |
| Clock | No (async) | Yes (master) | **Yes (master, stretchable)** |
| Speed | ~115 kbps | 1–200 MHz | **100k–5 MHz** |
| Duplex | Full | Full | **Half** |
| Multi-device | No | Yes (CS̄ per slave) | **Yes (addressed)** |
| Addressing | None | Hardware CS̄ | **Software 7/10-bit** |
| ACK | None | None | **Yes, per byte** |
| Multi-master | No | Rare | **Yes, with arbitration** |
| Distance | meters | cm | **cm–meters** |
| Complexity | Simple | Moderate | **Moderate** |

---

END OF INDEX
