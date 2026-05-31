# PROMPT 04 — COMPLETE FLASH CARD SYSTEM
# Usage: Replace every {{PROTOCOL_NAME}} with the actual protocol name before sending.
# ─────────────────────────────────────────────────────────────────────────────

You are a technical author and spaced-repetition learning expert. Generate a
**complete flash card system** for {{PROTOCOL_NAME}} that enables an engineer to
quickly memorize, retain, and recall all critical knowledge.

Each card has FRONT and BACK. Keep fronts as short as possible.
Backs: complete enough to fully answer, not so long they overwhelm.
Format every card EXACTLY as:

---
**CARD [DECK]-[NUMBER]**
**FRONT:** [question / prompt]
**BACK:** [answer]
**Tags:** [comma-separated tags]
**Difficulty:** [Beginner / Intermediate / Advanced / Expert]
---

Generate the following decks:

═══════════════════════════════════════════════════════════════
## DECK 1 — IDENTITY & METADATA CARDS (20 cards)
═══════════════════════════════════════════════════════════════

Cover:
- Full name and abbreviation
- Inventor / organization
- Year invented and standardized
- Standard document number
- Domain(s) of use
- OSI layers covered
- Physical medium
- Max speed (each generation)
- Max cable distance
- Max nodes
- Topology
- Synchronous or asynchronous
- Serial or parallel
- Safety classification
- Open or proprietary
- Status (active/legacy)
- Direct successor
- Main competitor(s)
- Standards body

═══════════════════════════════════════════════════════════════
## DECK 2 — PHYSICAL LAYER CARDS (25 cards)
═══════════════════════════════════════════════════════════════

Cover:
- Dominant / recessive / idle voltage levels
- Differential voltage swing
- Termination value and placement
- Cable impedance
- Max stub length
- Connector type
- Encoding scheme (NRZ / 8B10B / etc.)
- Bit stuffing rule (if any)
- Clock source
- Bit timing segment names and purpose
- Sample point typical value
- What causes reflection
- Effect of wrong termination
- EMI/EMC measures
- Max baud rate at each distance

═══════════════════════════════════════════════════════════════
## DECK 3 — FRAME STRUCTURE CARDS (30 cards)
═══════════════════════════════════════════════════════════════

For EACH frame type, generate cards covering:
- Frame name and purpose
- Total frame length (min/max)
- First field name, size, purpose
- Each subsequent field (one card per key field)
- SOF / EOF markers and their values
- CRC field: polynomial, bit count, coverage
- ACK field: sender behavior vs receiver behavior
- Payload maximum size
- Frame efficiency formula
- How to identify frame type from first byte/bits

═══════════════════════════════════════════════════════════════
## DECK 4 — ARBITRATION & TIMING CARDS (20 cards)
═══════════════════════════════════════════════════════════════

Cover:
- Access method (CSMA / TDMA / etc.)
- Arbitration mechanism (1-sentence exact description)
- Who wins arbitration
- What losers do
- Non-destructive vs destructive
- How priority is encoded
- Priority inversion risk
- Worst-case latency formula
- How to calculate max bus load %
- Bit timing segments (names + purpose)
- SJW purpose
- Baud Rate Prescaler formula
- Sample point formula
- What happens if two nodes have same ID

═══════════════════════════════════════════════════════════════
## DECK 5 — ERROR HANDLING CARDS (25 cards)
═══════════════════════════════════════════════════════════════

For EACH error type defined by the protocol:
- Error name
- Cause
- Detection mechanism
- Consequence (sender / receiver / bus)

Additional cards:
- TEC counter: increments when? decrements when?
- REC counter: increments when? decrements when?
- Error Active threshold values
- Error Passive threshold values
- Bus Off threshold value
- Bus Off recovery procedure
- CRC polynomial (exact)
- What CRC cannot detect
- Retransmission: how many? policy?
- Bit stuffing error: cause and effect

═══════════════════════════════════════════════════════════════
## DECK 6 — HARDWARE ARCHITECTURE CARDS (20 cards)
═══════════════════════════════════════════════════════════════

Cover:
- Controller block: key internal components
- PHY / transceiver: key function
- Key register names + one-line purpose each
- How to enable the controller (sequence)
- How to configure baud rate (register + formula)
- How to set up filters / acceptance mask
- DMA: how TX DMA works
- DMA: how RX DMA works
- IRQ: which events trigger interrupt
- ISR: what must be done in ISR
- Key transceiver ICs by vendor (NXP, TI, Infineon)
- Loopback mode: purpose and when to use
- Listen-only mode: purpose
- Bus monitoring mode: purpose

═══════════════════════════════════════════════════════════════
## DECK 7 — LINUX KERNEL CARDS (25 cards)
═══════════════════════════════════════════════════════════════

Cover:
- Kernel subsystem name for {{PROTOCOL_NAME}}
- Kernel tree location (drivers/xxx/)
- Key struct names (one card per struct)
- probe() function: what it does
- remove() function: what it does
- IRQ registration API name
- DMA mapping APIs
- Socket API: socket(), bind(), sendto(), recvfrom()
- Key ioctl commands
- netlink socket vs character device
- How to enable in Kconfig
- Key device tree bindings
- sysfs entries available
- procfs entries available
- Key kernel config symbols
- Useful kernel debugging tools for this subsystem
- can-utils commands (if CAN) / equivalent tools
- How to trace with ftrace
- How to decode with Wireshark plugin

═══════════════════════════════════════════════════════════════
## DECK 8 — SOFTWARE PROGRAMMING CARDS (20 cards)
═══════════════════════════════════════════════════════════════

Cover (one card each):
- Initialization code: minimum steps
- TX code: minimum steps bare metal
- RX code: minimum steps interrupt-driven
- Filter configuration: key steps
- Error handling: minimum steps in production code
- Key API functions (one card per function: name + signature + purpose)
- DMA TX setup steps
- DMA RX setup steps
- Linux socket TX steps
- Linux socket RX steps
- AUTOSAR API: key function names (if automotive)
- Android HAL: key interface (if AAOS)
- Common pitfalls in code (one per card)

═══════════════════════════════════════════════════════════════
## DECK 9 — PERFORMANCE & CALCULATIONS CARDS (15 cards)
═══════════════════════════════════════════════════════════════

Each card: formula on front, derivation + worked example on back

- Theoretical max throughput formula
- Effective throughput with overhead formula
- Bus utilization formula
- Worst-case latency formula
- Baud rate prescaler calculation
- Bit timing calculation (tSEG1, tSEG2)
- Sample point percentage formula
- Max number of nodes formula
- Cable length vs speed formula
- Frame transmission time formula
- Propagation delay formula
- CRC error detection capability (% coverage)
- FIFO depth calculation
- DMA descriptor count formula
- Interrupt latency budget calculation

═══════════════════════════════════════════════════════════════
## DECK 10 — SECURITY & SAFETY CARDS (15 cards)
═══════════════════════════════════════════════════════════════

Cover:
- Main security vulnerability of {{PROTOCOL_NAME}}
- Spoofing attack: how it works + mitigation
- Replay attack: how it works + mitigation
- Bus flooding attack: how it works + mitigation
- MITM attack: feasibility + mitigation
- Native security mechanisms (what protocol provides)
- SecOC: what it adds + when needed
- TLS/MACsec: applicable or not?
- Physical security measures
- ISO 26262 ASIL achievable with this protocol
- E2E protection: purpose + standard
- Fail-safe behavior
- Fail-operational behavior
- Redundancy approaches
- Safety certification steps

═══════════════════════════════════════════════════════════════
## DECK 11 — DEBUGGING CARDS (20 cards)
═══════════════════════════════════════════════════════════════

Format: FRONT = symptom, BACK = causes + diagnostic steps + fix

- No communication at all
- CRC errors intermittently
- Bus-off state
- High error rate under load only
- Communication works on bench, fails in vehicle
- Baud rate mismatch symptom
- Missing ACK
- Frame loss at high load
- Stuck in initialization
- DMA buffer overflow
- Interrupt storm
- Sporadic frame corruption
- Node not joining the network
- Increasing error counters with no communication failure
- Filter not passing expected frames
- Wrong data received (correct ID, wrong payload)
- TX never completes
- RX FIFO always empty
- One direction works, other doesn't
- Works at room temperature, fails at temperature extremes

═══════════════════════════════════════════════════════════════
## DECK 12 — COMPARISON & TRADEOFF CARDS (20 cards)
═══════════════════════════════════════════════════════════════

Each card compares {{PROTOCOL_NAME}} with one alternative on one dimension:

Format:
FRONT: "{{PROTOCOL_NAME}} vs [Competitor]: [dimension]"
BACK: clear winner + numbers + reasoning + when to choose each

Comparisons:
- vs [Competitor 1]: speed
- vs [Competitor 1]: cost
- vs [Competitor 1]: determinism
- vs [Competitor 1]: node count
- vs [Competitor 2]: speed
- vs [Competitor 2]: safety
- vs [Competitor 2]: wiring complexity
- etc. (cover top 3–4 competitors across key dimensions)
- vs successor protocol: migration tradeoff
- vs predecessor: what was gained

═══════════════════════════════════════════════════════════════
## DECK 13 — AUTOSAR CARDS (15 cards — skip if not automotive)
═══════════════════════════════════════════════════════════════

Cover:
- BSW module name(s) for {{PROTOCOL_NAME}}
- PDU Router: purpose
- Transport Protocol module: purpose
- Interface module: purpose
- Network Management module: purpose
- Diagnostic module: purpose
- COM module: signal extraction
- Signal packaging: how signals map to PDUs
- Configuration parameter: BitPosition
- Configuration parameter: DataLength
- E2E protection profile: which one used
- Classic vs Adaptive AUTOSAR difference for this protocol
- SOME/IP relationship (if applicable)
- ara::com relationship (if applicable)

═══════════════════════════════════════════════════════════════
## DECK 14 — HISTORY & EVOLUTION CARDS (15 cards)
═══════════════════════════════════════════════════════════════

Cover:
- Who invented and when
- What problem triggered invention
- Version 1 key limitation
- Version 2 what it fixed
- (Continue for each generation)
- First major industry adoption
- When it became dominant
- What is replacing it and why
- Expected end-of-life

═══════════════════════════════════════════════════════════════
## DECK 15 — RAPID RECALL FORMULA CARDS (10 cards)
═══════════════════════════════════════════════════════════════

One formula per card.
FRONT: Formula name
BACK: Formula + all variable definitions + worked numeric example

═══════════════════════════════════════════════════════════════
## DECK 16 — MENTAL MODEL / ANALOGY CARDS (10 cards)
═══════════════════════════════════════════════════════════════

Each card: one core concept of {{PROTOCOL_NAME}} explained via an analogy.

FRONT: "Analogy: How is {{PROTOCOL_NAME}} [concept] like [real-world thing]?"
BACK: Full analogy explanation that makes the concept unforgettable.

Cover: arbitration, framing, error handling, flow control, PHY, clock sync,
bus topology, protocol stack layers, DMA, interrupts.

---

## TOTAL CARD COUNT

Aim for 300+ cards total across all decks.
Every card must be self-contained — answerable without any other card.

## FORMATTING REQUIREMENTS

- Every card must have the exact format shown above
- Difficulty levels used consistently:
  Beginner: any software engineer can answer after 1 hour reading
  Intermediate: requires hands-on experience
  Advanced: requires deep implementation experience
  Expert: requires architecture-level understanding
- Tags must be comma-separated, lowercase, no spaces within tag
- Number cards sequentially within each deck: 01, 02, 03...

---

END OF PROMPT 04
