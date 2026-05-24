# PROMPT 02 — COMPLETE DIAGRAMS & VISUAL REFERENCE DOC
# Usage: Replace every {{PROTOCOL_NAME}} with the actual protocol name before sending.
# ─────────────────────────────────────────────────────────────────────────────

You are a world-class systems architect and technical illustrator with deep expertise
in protocol engineering. Generate a **complete visual reference document** for the
protocol: **{{PROTOCOL_NAME}}**.

This document contains ONLY diagrams, visual structures, and timing charts.
Every diagram must be in valid Mermaid syntax unless otherwise specified.
Do not explain in prose — let the diagrams speak. Add only minimal labels.

---

## DIAGRAM 01 — Protocol Family Tree

Generate a Mermaid graph TD showing:
- All predecessors of {{PROTOCOL_NAME}}
- {{PROTOCOL_NAME}} itself at center
- All variants / sub-protocols branching from it
- All derived / higher-layer protocols built on top
- Successor protocols
- Competing protocols (in a separate branch labeled "Competitors")

Use node shapes to distinguish:
- Predecessor: ([round])
- Current protocol: [BOLD rectangle]
- Variant: [rectangle]
- Derived/Higher-layer: ((circle))
- Competitor: {diamond}
- Successor: >arrow shaped]

---

## DIAGRAM 02 — OSI Layer Mapping

Generate a Mermaid graph showing a vertical stack of OSI layers (7 down to 1),
with annotations showing which layer(s) {{PROTOCOL_NAME}} covers, which layers
are handled by companion protocols, and which are left undefined.

---

## DIAGRAM 03 — Physical Layer Topology

Generate a Mermaid graph showing:
- The typical physical topology (bus / star / ring / point-to-point)
- All nodes
- Termination points
- Signal direction indicators
- Cable segments with distance labels

---

## DIAGRAM 04 — Complete Frame / Packet Structure

For EACH frame type defined by {{PROTOCOL_NAME}}, generate an ASCII art
byte-level frame diagram using this format:

```
 ┌─────────────────────────────────────────────────────────────┐
 │  FRAME TYPE: [name]                                         │
 ├──────────┬──────────┬────────────────┬──────────┬──────────┤
 │  SOF     │  ID      │  CTRL          │  DATA    │  CRC     │
 │  1 bit   │  11 bits │  6 bits        │  0-64B   │  15 bits │
 ├──────────┴──────────┴────────────────┴──────────┴──────────┤
 │ Byte 0   │ Byte 1   │ Byte 2...                            │
 └──────────────────────────────────────────────────────────── ┘
```

Show ALL fields with exact bit/byte widths.
Below each frame, add a hex example of a real minimal packet.

---

## DIAGRAM 05 — Bit-Level Timing Diagram

Generate an ASCII art timing diagram showing:
- Clock signal (if synchronous)
- Data line(s)
- Control signals (CS, EN, ACK, etc.)
- Bit boundaries
- Sample points
- Named timing parameters (tSU, tHD, tLOW, tHIGH, etc.)

Format:
```
CLK  : ___/‾‾‾\___/‾‾‾\___/‾‾‾\___/‾‾‾\___
MOSI : ---< D7  >< D6  >< D5  >< D4  >---
MISO : ---< Q7  >< Q6  >< Q5  >< Q4  >---
CS   : ‾‾‾\___________________________/‾‾‾
         |<--- tSU ->|<- tHD ->|
```

Show all defined timing parameters.

---

## DIAGRAM 06 — Hardware Block Diagram

Generate a Mermaid graph LR showing:
- MCU/SoC internal blocks (CPU, DMA, IRQ controller, registers)
- Protocol controller block
- PHY / Transceiver block
- Physical bus/medium
- Remote node (mirror image)

Include:
- Clock connections
- Data path (solid arrows)
- Control path (dashed arrows)
- IRQ lines
- DMA channels

---

## DIAGRAM 07 — Complete Software Stack

Generate a Mermaid graph TD showing the full software stack from hardware
to application:

Layer 1 (bottom): Hardware / PHY
Layer 2: Interrupt / DMA handler
Layer 3: Protocol Driver / HAL
Layer 4: Kernel Subsystem (e.g., SocketCAN / PCIe subsystem)
Layer 5: Kernel Socket / Device Layer
Layer 6: Userspace Library / API
Layer 7: Application / Middleware
(Add AUTOSAR stack as parallel if automotive)
(Add Android HAL stack as parallel if mobile/AAOS)

---

## DIAGRAM 08 — Initialization Sequence Diagram

Generate a Mermaid sequenceDiagram showing the complete initialization
flow between all actors (Application / Driver / Controller / PHY / Bus):

- Power on
- Clock enable
- Reset sequence
- Configuration register writes
- PHY negotiation (if applicable)
- Bus join / sync
- Ready state confirmation

---

## DIAGRAM 09 — Data Transmission Sequence Diagram

Generate a Mermaid sequenceDiagram showing:

For TRANSMIT side:
- Application calls send
- Buffer enqueue
- Controller arbitration
- PHY encoding
- Bus transmission
- ACK reception
- ISR / callback to application

For RECEIVE side:
- Bus activity detected
- PHY decoding
- Controller frame validation
- CRC check
- Buffer storage
- IRQ to driver
- Driver reads frame
- Application callback

---

## DIAGRAM 10 — Error Handling State Machine

Generate a Mermaid stateDiagram-v2 for EACH error state machine defined
by {{PROTOCOL_NAME}}:

- All states (name + brief description in comment)
- All transitions with:
  * Trigger condition [labeled on arrow]
  * Action taken
- Error counter increment/decrement transitions
- Bus-off or equivalent fatal state
- Recovery path

---

## DIAGRAM 11 — Protocol State Machine (Main)

Generate a Mermaid stateDiagram-v2 for the main protocol state machine:
- All operational states
- All transitions with triggers
- Error entry points
- Power/sleep states if defined

---

## DIAGRAM 12 — Arbitration / Bus Access Sequence

Generate a Mermaid sequenceDiagram showing a multi-node arbitration scenario:

- 3 nodes attempting simultaneous transmission
- Bit-by-bit arbitration
- Winner continues
- Losers back off
- Re-attempt by losers

---

## DIAGRAM 13 — Memory Map / Register Map

Generate a table-based memory/register map showing:

| Offset | Register Name | Reset Value | R/W | Bit Fields                          |
|--------|---------------|-------------|-----|-------------------------------------|
| 0x000  | CTRL          | 0x0000      | R/W | [15:8] reserved, [7] EN, [6:0] MODE |

Cover all major configuration, status, and data registers.

---

## DIAGRAM 14 — DMA Data Flow Diagram

Generate a Mermaid graph showing DMA-based data flow:
- Memory descriptor / scatter-gather list
- DMA engine
- Protocol FIFO
- PHY output
- IRQ completion path
- Memory coherency points

---

## DIAGRAM 15 — Linux Kernel Driver Architecture

Generate a Mermaid graph TD showing:
- VFS layer (open/read/write/ioctl/close)
- Character device / socket / netdev layer
- Protocol subsystem (e.g., net_device / can_dev / spi_controller)
- Driver layer (probe, ISR, xfer_one)
- Platform / PCI / I2C bus layer
- Hardware registers

Include sysfs and procfs nodes branching from appropriate layers.

---

## DIAGRAM 16 — AUTOSAR Communication Stack (if automotive)

Generate a Mermaid graph TD showing the full AUTOSAR stack:

Top: RTE (Application interface)
↓ COM module
↓ PDU Router
↓ Transport Protocol (if used)
↓ Interface module (e.g., CANIF / LinIF / EthIF)
↓ Driver (e.g., CAN / LIN / ETH)
↓ Hardware

Show signal flow in both directions.
Show NM (Network Management) as a side branch.
Show DCM (Diagnostics) as a side branch.

---

## DIAGRAM 17 — Android Vehicle HAL Stack (if AAOS protocol)

Generate a Mermaid graph TD showing:

App layer → CarService → VehicleHAL → Kernel Driver → Hardware

With side branches:
- AIDL interface definition
- Property subscriptions
- Set/Get flow

---

## DIAGRAM 18 — Security Threat Model

Generate a Mermaid graph showing:
- All protocol communication paths
- Attack vectors pointing at each path (labeled with attack type)
- Security controls blocking each attack
- Color coding: red = attack path, green = defense

---

## DIAGRAM 19 — Performance Comparison Chart

Generate a Mermaid xychart-beta or a table comparing {{PROTOCOL_NAME}}
against its top 4 competitors on these axes:
- Max speed
- Max distance
- Max nodes
- Latency
- Determinism level
- Cost index
- Implementation complexity

---

## DIAGRAM 20 — Real Product Usage Map

Generate a Mermaid graph showing:
- Industry domains as top-level nodes (Automotive / Consumer / Industrial / Aerospace / Data Center)
- Under each domain: specific product categories
- Under each product: specific company examples
- Link back to {{PROTOCOL_NAME}} at center

---

## DIAGRAM 21 — Protocol Interaction Knowledge Graph

Generate a Mermaid graph showing how {{PROTOCOL_NAME}} interacts with
related protocols in a real system:

Example for CAN FD:
CAN FD ↔ UDS (diagnostic layer)
CAN FD ↔ ISO-TP (transport segmentation)
CAN FD ↔ SOME/IP (via gateway)
CAN FD ↔ AUTOSAR COM
CAN FD ↔ SocketCAN (Linux)

Show direction of dependency and brief label on each edge.

---

## DIAGRAM 22 — Debugging Decision Tree

Generate a Mermaid flowchart TD representing a systematic debugging procedure:

Start: "Problem observed"
→ Branch on symptom type
→ Each branch leads to specific diagnostic step
→ Each diagnostic step has YES/NO branches
→ Terminal nodes: specific fix + prevention note

Cover at least 8–10 common failure modes.

---

## DIAGRAM 23 — System Integration Example

Generate a Mermaid graph showing a complete realistic system
that uses {{PROTOCOL_NAME}}:

Example: A vehicle ECU network showing all ECUs, all protocols between them,
all sensors, actuators, gateway nodes.

Be specific: use real ECU names (BCM, TCM, ECM, ADAS, IPC, etc.)

---

## DIAGRAM 24 — Chiplet / Die Integration (if on-chip protocol)

Generate a Mermaid graph showing a multi-chiplet system:
- Multiple dies connected via {{PROTOCOL_NAME}}
- Each die's internal blocks
- Interface IP on each die
- Package substrate
- Board connections

---

## DIAGRAM 25 — Migration Path Diagram

Generate a Mermaid graph showing the industry migration path:

Old Protocol → {{PROTOCOL_NAME}} → Successor Protocol

With:
- Timeline annotations
- Reasons for each migration
- Coexistence period

---

## FORMATTING REQUIREMENTS

- Every diagram must have a clear H2 heading
- Every diagram must have a 1-sentence caption below it
- All Mermaid diagrams must be in valid fenced code blocks: ```mermaid
- ASCII art diagrams must be in fenced code blocks: ```
- No prose paragraphs — diagrams and captions only
- If a diagram would be too complex for Mermaid, describe it as structured text tables

---

END OF PROMPT 02
