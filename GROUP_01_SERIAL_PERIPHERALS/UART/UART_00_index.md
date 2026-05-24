# UART — Protocol Encyclopedia Index
# ════════════════════════════════════════════════════════════════════
# Domain: Serial Communication | Level: Foundation (Tier 1)
# Status: In Progress | Started: 2026-05-23
# ════════════════════════════════════════════════════════════════════

## Document Map

| # | File | Description | Size | Priority |
|---|------|-------------|------|----------|
| 00 | `UART_00_index.md` | This index | — | — |
| 01 | `UART_01_master_theory.md` | Complete A-Z theory, history, hardware, software | Core | ⭐⭐⭐⭐⭐ |
| 02 | `UART_02_diagrams.md` | All Mermaid + ASCII diagrams | Reference | ⭐⭐⭐⭐ |
| 03 | `UART_03_interview_questions.md` | 120+ Q&A across all levels | Interview | ⭐⭐⭐⭐⭐ |
| 04 | `UART_04_flashcards.md` | 300+ spaced-repetition cards | Memory | ⭐⭐⭐⭐ |
| 05 | `UART_05_behavioral_cards.md` | 43 STAR-format behavioral cards | Interview | ⭐⭐⭐ |
| 06 | `UART_06_cheatsheet.md` | Quick reference, formulas, code snippets | Daily Use | ⭐⭐⭐⭐⭐ |
| 07 | `UART_07_labs_debugging.md` | Hands-on labs + debugging playbook | Practice | ⭐⭐⭐⭐ |
| 08 | `UART_08_references.md` | Books, websites, GitHub, standards | Resources | ⭐⭐⭐⭐ |

---

## Quick Identity Card

| Field | Value |
|-------|-------|
| Full Name | Universal Asynchronous Receiver/Transmitter |
| Abbreviation | UART |
| Domain | Embedded Systems, Automotive, Industrial, Telecom |
| OSI Layer | L1 (Physical) + L2 (Data Link — framing only) |
| Topology | Point-to-point |
| Speed Range | 110 bps — 15+ Mbps (common: 115200 bps — 921600 bps) |
| Distance | 1–15m (TTL), up to 1200m (RS-485) |
| Wires | 2 minimum (TX, RX) + optional GND, RTS, CTS |
| Synchronous? | NO — asynchronous (no clock line) |
| Standard Body | No single body; RS-232 = EIA/TIA-232 |
| Status | Active — fundamental, used everywhere |

---

## Study Checklist

- [ ] Can draw UART waveform on whiteboard from memory
- [ ] Can calculate baud rate register value for any clock
- [ ] Know all 4 error types and their causes
- [ ] Can write bare-metal UART init + TX + RX in C
- [ ] Understand 16550 UART register map
- [ ] Know Linux termios API (cfsetspeed, tcsetattr, etc.)
- [ ] Know TTL vs RS-232 voltage levels and why inverted
- [ ] Understand 16x oversampling and sampling point
- [ ] Can debug common UART failures on oscilloscope
- [ ] Know RTS/CTS hardware flow control mechanism
- [ ] Understand FIFO operation and watermark interrupts
- [ ] Know DMA-based UART for zero-CPU-overhead transfer

---

## Learning Path Position

```
You are here:
[UART] → SPI → I2C → JTAG → CAN FD → LIN → USB → AXI → PCIe → ...
  ↑
  Foundation. Master this first.
  Every other protocol builds on serial comms intuition.
```
