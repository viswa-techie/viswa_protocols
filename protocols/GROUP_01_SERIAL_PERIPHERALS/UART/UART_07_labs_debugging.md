# UART — LABS & DEBUGGING PLAYBOOK
# ════════════════════════════════════════════════════════════════════
# Protocol: UART | Document: 07 of 08 — Hands-On Labs + Debug Guide
# Progressive difficulty: Beginner → Advanced → Automotive
# ════════════════════════════════════════════════════════════════════

---

# PART A — PROGRESSIVE LAB EXERCISES

---

## LAB 01: HELLO UART — LOOPBACK TEST (Beginner, 30 min)

### Objective
Confirm UART TX/RX works by physically connecting TX→RX on same device.

### Hardware
- 1× STM32 Nucleo (or Arduino) board
- 1× jumper wire
- USB cable for power/debug

### Procedure
1. Configure UART1 at 115200-8N1
2. Connect TX pin → RX pin with jumper wire
3. Send "HELLO" from code
4. Read back in ISR/polling; verify match
5. Remove jumper → verify read times out (no data)

### Expected Outcome
- TX→RX loopback: every byte sent is received identically
- Without loopback: RX buffer stays empty

### Validation Criteria
- Zero framing errors
- Zero overrun errors
- 100% byte match

### Stretch Goals
- Try different baud rates (9600, 115200, 921600)
- Add a timing measurement: how long to transfer 1000 bytes?
- Break parity intentionally (force wrong parity bit) → observe PE flag

---

## LAB 02: TWO-BOARD COMMUNICATION (Beginner, 45 min)

### Objective
Establish bidirectional UART between two microcontrollers.

### Hardware
- 2× MCU boards (can be different: e.g., STM32 + Arduino)
- 3× wires (TX↔RX crossed + GND)

### Procedure
1. Board A: TX → Board B: RX
2. Board B: TX → Board A: RX
3. Connect GNDs together
4. Board A sends "PING\n"
5. Board B receives, sends back "PONG\n"
6. Verify round-trip

### Key Learning Points
- TX/RX MUST be crossed (most common wiring error)
- GND MUST be shared
- Both sides MUST agree on: baud rate, data bits, parity, stop bits

### Variations
- Mismatch baud rate intentionally → observe framing errors
- Try 3.3V board ↔ 5V board without level shifter → discuss risk
- Add flow control (RTS/CTS)

---

## LAB 03: RING BUFFER + ISR (Intermediate, 1.5 hours)

### Objective
Implement production-quality interrupt-driven UART with ring buffer.

### Design
```c
#define BUF_SIZE 256  // Must be power of 2

typedef struct {
    volatile uint8_t buf[BUF_SIZE];
    volatile uint16_t head;  // ISR writes
    volatile uint16_t tail;  // Main reads
} ring_buf_t;

static ring_buf_t rx_ring;

void UART_IRQHandler(void) {
    if (UART->STATUS & RX_READY) {
        uint16_t next = (rx_ring.head + 1) & (BUF_SIZE - 1);
        if (next != rx_ring.tail) {  // Not full
            rx_ring.buf[rx_ring.head] = UART->DATA;
            rx_ring.head = next;
        } else {
            (void)UART->DATA;  // Overflow: discard
            overflow_count++;
        }
    }
}

int uart_read(uint8_t *byte) {
    if (rx_ring.head == rx_ring.tail) return -1;  // Empty
    *byte = rx_ring.buf[rx_ring.tail];
    rx_ring.tail = (rx_ring.tail + 1) & (BUF_SIZE - 1);
    return 0;
}
```

### Exercises
1. Implement the above ring buffer
2. Stress test: send data at max speed without pause
3. Measure: at what byte rate does overflow_count start increasing?
4. Double buffer size → re-measure
5. Add TX ring buffer (ISR-driven TX)

### Pass Criteria
- No overflow at 115200 with BUF_SIZE=256
- Main loop can do other work without losing bytes
- Latency from byte arrival to main-loop availability < 100 µs

---

## LAB 04: DMA TRANSFER (Intermediate, 2 hours)

### Objective
Implement zero-CPU-overhead UART receive using DMA circular buffer.

### Concept
```
DMA Controller
  ┌──────────────────────────┐
  │  UART_DR → Memory[0..N]  │ (circular)
  │  Triggers: HT (Half), TC │ (Complete)
  └──────────────────────────┘
  
Application reads from memory, tracks position via DMA_NDTR
```

### STM32 Implementation Outline
```c
// DMA config
DMA_Stream->PAR  = (uint32_t)&USART1->DR;
DMA_Stream->M0AR = (uint32_t)dma_buf;
DMA_Stream->NDTR = DMA_BUF_SIZE;
DMA_Stream->CR   = DMA_CIRC | DMA_MINC | DMA_P2M | DMA_EN;

// Enable USART DMA
USART1->CR3 |= USART_CR3_DMAR;
```

### Exercises
1. Configure DMA circular mode for UART RX
2. Implement position tracking: `bytes_available = BUF_SIZE - DMA_NDTR - read_pos`
3. Process received lines (detect '\n')
4. Measure CPU usage: compare ISR vs DMA at 921600 baud
5. Implement idle-line detection (IDLE interrupt) for variable-length messages

### Key Insight
DMA + Idle Line interrupt = optimal for unknown-length messages (GPS, AT modems, etc.)

---

## LAB 05: LINUX SERIAL DRIVER EXPLORATION (Intermediate, 2 hours)

### Objective
Understand the full Linux serial stack from userspace to hardware.

### Exercises

**Part 1: Userspace (30 min)**
```bash
# Exercise 1: List available ports and identify type
ls -la /dev/tty* | grep -E "(USB|ACM|AMA|S[0-9])"
dmesg | grep -i "tty\|serial\|uart"

# Exercise 2: Configure and use
stty -F /dev/ttyS0 115200 cs8 -parenb -cstopb raw
echo "test" > /dev/ttyS0

# Exercise 3: Write C program using termios
# (open, configure, read/write, close)
```

**Part 2: Kernel Investigation (45 min)**
```bash
# Find the serial driver for your hardware
cat /proc/tty/driver/serial
cat /sys/class/tty/ttyS0/type

# Trace the call path
echo 'p:uart_write drivers/tty/serial/8250/8250_port.c:serial8250_start_tx' \
    > /sys/kernel/debug/tracing/kprobe_events
echo 1 > /sys/kernel/debug/tracing/events/kprobes/uart_write/enable
cat /sys/kernel/debug/tracing/trace_pipe
# Send data, watch trace
```

**Part 3: Device Tree (45 min)**
```dts
// Find and modify UART device tree node
uart0: serial@7e201000 {
    compatible = "brcm,bcm2835-pl011", "arm,pl011";
    reg = <0x7e201000 0x200>;
    interrupts = <2 25>;
    clocks = <&clocks BCM2835_CLOCK_UART>;
    clock-frequency = <48000000>;
    status = "okay";
    pinctrl-names = "default";
    pinctrl-0 = <&uart0_pins>;
};
```
Exercises: Change baud, add/remove second UART, enable flow control pins.

---

## LAB 06: AUTOMOTIVE CONSOLE DEBUG (Advanced, 2 hours)

### Objective
Set up and use UART debug console for automotive SoC (Qualcomm/NXP style).

### Scenario
You have an automotive head unit with Qualcomm SA8155P. UART2 is the debug console.

### Procedure
1. Identify debug UART pins on dev board schematic
2. Connect USB-UART adapter (3.3V TTL, NOT RS-232!)
3. Configure terminal: 115200-8N1 (QC default) or 921600 (high-speed)
4. Capture boot log from power-on
5. Parse boot stages: PBL → SBL → ABL → Kernel → Android

### Boot Log Analysis Exercise
```
[PBL] Boot mode: 0x05
[SBL1] Build: Oct 2023
[SBL1] DDR: 8GB LPDDR5 @ 2750MHz
[ABL] Display: OK
[ABL] Starting Linux...
[    0.000000] Booting Linux on physical CPU 0x0000000000
[    0.000000] Linux version 5.15.78-android13-...
[    1.234567] 7e201000.serial: ttyMSM0 at MMIO 0x7e201000 (irq = 33)
...
[    5.678901] init: starting service 'console'
```

### Tasks
1. Calculate boot time from PBL to Android shell ready
2. Identify if UART output stalls (possible baud rate change mid-boot)
3. Drop into fastboot/EDL mode via UART command
4. Capture kernel panic log when system crashes

---

## LAB 07: PROTOCOL BRIDGE (Advanced, 3 hours)

### Objective
Build a UART ↔ CAN bridge (common automotive debug tool).

### Architecture
```
ECU (CAN) ←──CAN Bus──→ [MCU Bridge] ←──UART──→ PC Terminal
                          │
                          ├─ CAN RX → Parse → Format → UART TX
                          └─ UART RX → Parse → Build → CAN TX
```

### Message Format (UART side)
```
# CAN frame display
CAN_RX: ID=0x123 DLC=8 DATA=[AA BB CC DD EE FF 00 11] TS=12345ms

# CAN frame send command
CAN_TX:0x456:8:11223344AABBCCDD
```

### Implementation Steps
1. Initialize CAN peripheral (500 kbps)
2. Initialize UART (115200)
3. CAN RX ISR → format string → UART TX ring buffer
4. UART RX → parse command → CAN TX
5. Add filtering: only forward specific CAN IDs
6. Add timestamping

---

## LAB 08: SIGNAL INTEGRITY ANALYSIS (Expert, 2 hours)

### Objective
Use oscilloscope to analyze UART signal quality and diagnose issues.

### Equipment
- Oscilloscope (≥100 MHz bandwidth, digital preferred)
- Logic analyzer (Saleae or similar)
- Signal generator (optional)
- Bread board setup with intentional issues

### Exercises

**Exercise 1: Clean Signal Baseline**
- Capture 'U' character (0x55 = alternating bits) at 115200
- Measure: rise time, fall time, bit width, jitter
- Expected: bit width = 8.68 µs ± 0.2 µs

**Exercise 2: Induce and Analyze Problems**
- Add 100pF capacitor on TX line → observe rise time degradation
- Add 100Ω series resistor → observe voltage drop
- Add 1m cable → observe ringing
- Mix 3.3V TX → 5V RX without level shifter → measure actual HIGH voltage

**Exercise 3: Long Cable Analysis**
- 1m, 5m, 10m, 15m cables at 115200
- At what length does communication fail?
- What does the waveform look like at failure point?

**Exercise 4: EMI Injection**
- Place UART cable near running motor/relay
- Observe noise coupling
- Add ferrite bead → measure improvement
- Add shielding → measure improvement

---

# PART B — DEBUGGING PLAYBOOK

---

## DEBUG SCENARIO 01: NO COMMUNICATION AT ALL

### Symptoms
- TX side sends, RX side sees nothing
- No activity on scope/logic analyzer

### Decision Tree
```
START
├─ Is TX pin toggling on scope?
│   ├─ NO → Check: clock enabled? GPIO AF config? UART enabled? TX enabled?
│   └─ YES → Is RX pin seeing signal?
│       ├─ NO → Check: wire connection? TX→RX crossed? GND connected?
│       └─ YES → Is software receiving?
│           ├─ NO → Check: RX enabled? ISR registered? FIFO enabled? 
│           │        Peripheral clock? Pull-up on idle line?
│           └─ YES but garbage → See Scenario 02
```

### Systematic Checks (Order Matters)
1. **Power**: Both boards powered? Voltages correct?
2. **Clock**: Peripheral clock enabled in RCC?
3. **GPIO**: Correct pin? AF mode (not input/output)? Correct AF number?
4. **Wire**: TX→RX (not TX→TX)? GND shared?
5. **Config**: UART peripheral enabled? TX/RX enabled?
6. **Software**: ISR enabled in NVIC? ISR handler name correct?

---

## DEBUG SCENARIO 02: GARBAGE DATA / GARBLED OUTPUT

### Symptoms
- Data received but incorrect
- Random characters instead of expected text
- Intermittent correct bytes mixed with wrong ones

### Root Causes (by frequency)
1. **Baud rate mismatch** (80% of cases)
2. **Config mismatch** (parity/stop bits/data bits)
3. **Voltage level issue** (3.3V→5V or RS-232 without converter)
4. **Inverted logic** (some modules use active-low)
5. **Wrong byte order** (endianness in multi-byte protocols)

### Diagnostic Procedure
```bash
# Step 1: Verify with known character
# Send 0x55 ('U') — alternating bit pattern, easy to spot on scope
# Expected at 115200: 8.68µs per bit, pattern 0-1-0-1-0-1-0-1-0-1

# Step 2: Send 0x00 and 0xFF
# 0x00: START(low)+8×low+STOP(high) = long low pulse + one high
# 0xFF: START(low)+8×high+STOP(high) = short low pulse + long high

# Step 3: If bit timing looks wrong, calculate actual baud
# Measure one bit width on scope → 1/width = actual baud rate
```

### Baud Rate Reverse Engineering
```
Measured bit width: 104.2 µs → 1/104.2µs = 9596 baud → likely 9600
Measured bit width: 8.68 µs → 1/8.68µs = 115207 → likely 115200
Measured bit width: 26.04 µs → 1/26.04µs = 38402 → likely 38400
```

---

## DEBUG SCENARIO 03: OVERRUN ERRORS

### Symptoms
- OE (Overrun Error) flag set
- Missing bytes in stream
- Works at low speed, fails at high speed
- Works with small messages, fails with large ones

### Root Causes
1. ISR latency too high (higher-priority interrupts blocking)
2. Main loop not reading fast enough (polling mode)
3. FIFO not enabled
4. DMA not configured
5. Software spending too long in critical sections

### Diagnostic Steps
```c
// 1. Check if FIFO is enabled
// If FIFO disabled: only 1-byte buffer → must respond within 1 byte time
// At 115200: 1 byte = 86.8µs → ISR must complete in <86.8µs

// 2. Measure ISR response time
GPIO_SET(DEBUG_PIN);   // At ISR entry
// ... ISR work ...
GPIO_CLEAR(DEBUG_PIN); // At ISR exit
// Measure pin HIGH time on scope

// 3. Check for interrupt priority inversion
// Is a lower-priority ISR blocking the UART ISR?
```

### Solutions (escalating)
1. Enable 16-byte FIFO → buys 16× more time
2. Raise UART IRQ priority
3. Switch to DMA circular buffer
4. Reduce ISR work (just stuff in ring buffer, process in main)
5. Use idle-line interrupt + DMA for burst reception

---

## DEBUG SCENARIO 04: WORKS IN DEBUG, FAILS IN RELEASE

### Symptoms
- Single-stepping in debugger → works perfectly
- Run at full speed → data lost or corrupted

### Root Causes
1. **Timing-dependent**: debugger slows everything → masks race
2. **Optimizer removes volatile reads**: variable not `volatile`
3. **Breakpoint halts CPU but UART keeps receiving**: overrun on resume
4. **Printf debugging changes timing**: adding prints "fixes" issue

### Solutions
```c
// 1. Mark ALL shared variables volatile
volatile uint8_t rx_buffer[256];
volatile uint16_t rx_head;

// 2. Use memory barriers where needed
__DMB();  // Data Memory Barrier (ARM)

// 3. Never rely on timing in ISR — use hardware flow control
// 4. Use logic analyzer instead of printf for debugging
// 5. Check compiler optimization: -O0 vs -O2 behavior difference
```

---

## DEBUG SCENARIO 05: INTERMITTENT FAILURES

### Symptoms
- Works 99% of the time, fails randomly
- Fails after hours of operation
- Fails when motor/relay activates
- Fails at temperature extremes

### Diagnostic Approach
```
1. ADD STATISTICS:
   - Frame error count
   - Overrun count
   - Parity error count
   - Good byte count
   → Calculate error RATE per million bytes

2. CORRELATE WITH EVENTS:
   - Timestamp each error
   - Log environmental events (temperature, motor on/off)
   - Plot: errors vs. time → find pattern

3. PHYSICAL CHECKS:
   - Connector: wiggle test while monitoring errors
   - Cable: swap with shorter/shielded cable
   - EMI: add ferrite, improve grounding
   - Temperature: heat gun / freeze spray on suspect component
```

### Common Solutions
| Pattern | Likely Cause | Fix |
|---------|-------------|-----|
| Errors correlate with motor | EMI | Shield, ferrite, route away |
| Errors increase over time | Thermal drift | Better crystal, temp-compensated osc |
| Errors after connector touch | Loose connection | Re-crimp, add strain relief |
| Errors during high CPU load | ISR latency | DMA, higher priority |
| Errors at high baud only | Signal integrity | Shorter cable, better termination |

---

## DEBUG SCENARIO 06: LINUX-SPECIFIC ISSUES

### Symptom: Port Not Found
```bash
# Check if driver loaded
lsmod | grep -i serial
# Check dmesg for errors
dmesg | grep -i "tty\|serial\|uart" | tail -20
# Check if device tree enabled the port
cat /sys/firmware/devicetree/base/soc/serial@*/status
# Check permissions
ls -la /dev/ttyS0  # Need rw access, user in 'dialout' group
```

### Symptom: Permission Denied
```bash
sudo usermod -aG dialout $USER
# Then LOGOUT/LOGIN (group change needs new session)
```

### Symptom: Port Busy
```bash
# Find who's using it
fuser /dev/ttyS0
lsof /dev/ttyS0
# Kill if appropriate
fuser -k /dev/ttyS0
```

### Symptom: Canonical Mode Corruption
```bash
# If you see \r\n instead of \n, or missing characters, or 
# ctrl-characters being interpreted:
stty -F /dev/ttyS0 raw -echo -echoe -echok
# Or in C: disable ICANON, ECHO, ISIG, IEXTEN
```

### Symptom: Read() Returns 0 Bytes or Blocks Forever
```c
// Check VMIN and VTIME settings:
// VMIN=0, VTIME=0 → non-blocking, returns immediately
// VMIN=1, VTIME=0 → blocks until at least 1 byte
// VMIN=0, VTIME=10 → returns after 1 second timeout
// VMIN=5, VTIME=10 → returns when 5 bytes OR 1sec, whichever first
```

---

## DEBUG SCENARIO 07: AUTOMOTIVE BOOT CONSOLE ISSUES

### Symptom: No Output on Debug UART During Boot

**Qualcomm Platform Checks:**
```
1. Correct UART port? (QC uses UART2 for debug by default)
2. Correct baud? (115200 for SBL, may change at kernel)
3. Board-specific: Check if DEBUG_UART routed to header
4. Check fuse: Is UART debug disabled in production fuse?
5. Check CDT: Console UART config in CDT (Config Data Table)
```

**NXP i.MX Checks:**
```
1. Check BOOT_CFG switches for UART console selection
2. Verify pin mux in DCD (Device Configuration Data)
3. Check if secure boot disabled console output
```

### Symptom: Boot Log Stops Mid-Way
```
Possible baud rate change! Some platforms:
- Bootloader: 115200
- Kernel: 921600 (configured in device tree / cmdline)

Solution: Watch scope for bit timing change, switch terminal baud
Or: Add "console=ttyMSM0,115200" to kernel cmdline
```

---

# PART C — DEBUGGING TOOLS REFERENCE

## Recommended Toolchain

| Tool | Purpose | Cost |
|------|---------|------|
| Saleae Logic Pro | Logic analyzer, protocol decode | $500 |
| PulseView + FX2 | Open-source logic analyzer | $15 |
| Bus Pirate | Universal serial tool | $30 |
| minicom/picocom | Terminal emulators | Free |
| stty | Quick port config | Built-in |
| trace-cmd | Kernel tracing | Free |
| Serial Monitor (VS Code) | IDE-integrated monitor | Free |
| Realterm | Windows serial terminal | Free |
| SerialPlot | Real-time data plotting | Free |
| sigrok/PulseView | Protocol decode + capture | Free |

## Logic Analyzer Decode Settings
```
Protocol: Async Serial / UART
Baud: 115200 (or actual)
Data bits: 8
Parity: None
Stop bits: 1
Bit order: LSB first
Signal inversion: Normal (HIGH=idle) or Inverted (for RS-232)
```

## Oscilloscope Measurements for UART
```
Required bandwidth: ≥5× baud rate (575 kHz for 115200)
Time/div for one frame: 10µs/div (115200) or 100µs/div (9600)
Trigger: Falling edge on TX/RX (catches start bit)
```

---

# PART D — QUICK DEBUGGING FLOWCHART

```
UART NOT WORKING?
│
├─ Nothing at all
│   ├─ Check with scope/LA: Is TX toggling?
│   │   ├─ NO → Hardware/config issue (clock, GPIO, enable)
│   │   └─ YES → Wiring or RX-side issue
│   │
│   └─ Quick SW test: Can you read your own TX? (loopback)
│       ├─ YES → Wire/connection issue
│       └─ NO → Peripheral config issue
│
├─ Garbage received
│   ├─ Measure bit width on scope
│   │   ├─ Wrong width → Baud rate mismatch
│   │   └─ Correct width → Config mismatch or voltage issue
│   │
│   └─ Send 0x55, verify alternating pattern on scope
│       ├─ Pattern inverted → Logic inversion (use NOT or SW flag)
│       └─ Pattern garbled → Electrical issue (level, cable, EMI)
│
├─ Data lost (overrun)
│   ├─ Enable FIFO (16 bytes buys time)
│   ├─ Switch to DMA
│   └─ Check ISR latency
│
└─ Works sometimes, fails randomly
    ├─ Add error counters
    ├─ Correlate with events
    └─ Check physical layer (connectors, cable, EMI)
```

---

END OF DOCUMENT 07 — LABS & DEBUGGING PLAYBOOK
