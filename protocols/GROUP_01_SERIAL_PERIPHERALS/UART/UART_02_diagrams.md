# UART — COMPLETE DIAGRAMS & VISUAL REFERENCE
# ════════════════════════════════════════════════════════════════════
# Protocol: UART | Document: 02 of 08 — Diagrams
# All diagrams in Mermaid or ASCII art for rendering in Markdown viewers
# ════════════════════════════════════════════════════════════════════

---

## DIAGRAM 01 — Protocol Family Tree

```mermaid
graph TD
    subgraph Predecessors
        BAUDOT([Baudot Code 1870])
        TELEGRAPH([Telegraph 1850s])
        TTY([Teletypewriter 1930s])
        CURRENTLOOP([20mA Current Loop])
    end

    subgraph "UART Core"
        UART[<b>UART</b><br/>Universal Async Receiver/Transmitter<br/>1960s–present]
    end

    subgraph Variants
        USART[USART<br/>+sync clock mode]
        RS232[RS-232<br/>±12V levels, DB-9]
        RS422[RS-422<br/>Differential, multi-RX]
        RS485[RS-485<br/>Differential, multi-drop]
        IRDA[IrDA<br/>Infrared UART]
        KLINE[K-Line<br/>ISO 9141, single-wire]
        LIN_V[LIN<br/>Single-wire, scheduled]
    end

    subgraph "Higher Protocols"
        AT((AT Commands))
        NMEA((NMEA 0183))
        MODBUS((Modbus RTU))
        DMX((DMX512))
        MIDI((MIDI))
        SLIP((SLIP/PPP))
        HCI((BT HCI H4))
        CONSOLE((Linux Console))
    end

    subgraph Competitors
        SPI{SPI}
        I2C{I2C}
        USB_C{USB}
        CAN{CAN}
    end

    subgraph Successors
        USBCDC>USB-CDC VCP]
        SWO>SWO Trace]
    end

    BAUDOT --> TTY
    TELEGRAPH --> TTY
    TTY --> CURRENTLOOP
    CURRENTLOOP --> UART

    UART --> USART
    UART --> RS232
    UART --> RS422
    RS422 --> RS485
    UART --> IRDA
    UART --> KLINE
    UART --> LIN_V

    UART --> AT
    UART --> NMEA
    RS485 --> MODBUS
    RS485 --> DMX
    UART --> MIDI
    UART --> SLIP
    UART --> HCI
    UART --> CONSOLE

    UART -.->|replaced by| USBCDC
    UART -.->|supplemented by| SWO
```

---

## DIAGRAM 02 — OSI Layer Mapping

```mermaid
graph TD
    subgraph "OSI Layers"
        L7[Layer 7: Application<br/><i>NOT defined by UART</i><br/>AT cmd, NMEA, Modbus add this]
        L6[Layer 6: Presentation<br/><i>NOT defined</i>]
        L5[Layer 5: Session<br/><i>NOT defined</i>]
        L4[Layer 4: Transport<br/><i>NOT defined</i><br/>No retransmit, no flow ctrl]
        L3[Layer 3: Network<br/><i>NOT defined</i><br/>No addressing]
        L2[Layer 2: Data Link<br/><b>PARTIAL: Framing only</b><br/>Start/Stop/Parity]
        L1[Layer 1: Physical<br/><b>DEFINED by variant</b><br/>TTL/RS-232/RS-485]
    end

    L7 --> L6 --> L5 --> L4 --> L3 --> L2 --> L1

    style L2 fill:#4CAF50,color:#fff
    style L1 fill:#2196F3,color:#fff
```

---

## DIAGRAM 03 — Physical Layer Topology

```mermaid
graph LR
    subgraph "Point-to-Point (Standard UART)"
        A[Device A<br/>MCU/SoC] -->|TX → RX| B[Device B<br/>Module/MCU]
        B -->|TX → RX| A
        A ---|GND| B
    end
```

```mermaid
graph LR
    subgraph "RS-485 Multi-drop (Bus Topology)"
        TERM_A[120Ω<br/>Termination] --- N1
        N1[Node 1<br/>Master] --- BUS[Differential Bus<br/>A/B lines<br/>Up to 1200m]
        BUS --- N2[Node 2<br/>Slave]
        BUS --- N3[Node 3<br/>Slave]
        BUS --- N4[Node 4<br/>Slave]
        BUS --- TERM_B[120Ω<br/>Termination]
    end
```

---

## DIAGRAM 04 — UART Frame Structure (All Configurations)

### Standard Frame: 8N1 (Most Common)

```
 ┌──────────────────────────────────────────────────────────────────────────┐
 │  IDLE │START│ D0  │ D1  │ D2  │ D3  │ D4  │ D5  │ D6  │ D7  │STOP│IDLE│
 │(HIGH) │ 0  │ LSB │     │     │     │     │     │     │ MSB │ 1  │(HI)│
 ├───────┼─────┼─────┼─────┼─────┼─────┼─────┼─────┼─────┼─────┼────┼────┤
 │  1    │  0  │  x  │  x  │  x  │  x  │  x  │  x  │  x  │  x  │ 1  │ 1  │
 └──────────────────────────────────────────────────────────────────────────┘
        │← 1 →│←──────────── 8 bits data ─────────────→│← 1 →│
        │ bit │                                          │ bit │
        Total: 10 bits per character
```

### Frame with Even Parity: 8E1

```
 ┌───────────────────────────────────────────────────────────────────────────────┐
 │ IDLE │START│ D0  │ D1  │ D2  │ D3  │ D4  │ D5  │ D6  │ D7  │ PAR │STOP│IDLE│
 │(HIGH)│  0  │ LSB │     │     │     │     │     │     │ MSB │EVEN │ 1  │(HI)│
 └───────────────────────────────────────────────────────────────────────────────┘
        │← 1 →│←──────────── 8 bits data ─────────────→│← 1 →│← 1 →│
        Total: 11 bits per character
```

### Frame with 2 Stop Bits: 8N2

```
 ┌────────────────────────────────────────────────────────────────────────────────┐
 │ IDLE │START│ D0  │ D1  │ D2  │ D3  │ D4  │ D5  │ D6  │ D7  │STOP1│STOP2│IDLE│
 │(HIGH)│  0  │ LSB │     │     │     │     │     │     │ MSB │  1  │  1  │(HI)│
 └────────────────────────────────────────────────────────────────────────────────┘
        │← 1 →│←──────────── 8 bits data ─────────────→│←── 2 bits ──→│
        Total: 11 bits per character
```

### 9-bit Mode (Multi-processor): 9N1

```
 ┌───────────────────────────────────────────────────────────────────────────────────┐
 │ IDLE │START│ D0  │ D1  │ D2  │ D3  │ D4  │ D5  │ D6  │ D7  │ D8  │STOP│ IDLE  │
 │(HIGH)│  0  │ LSB │     │     │     │     │     │     │     │ADDR/│ 1  │ (HI)  │
 │      │     │     │     │     │     │     │     │     │     │DATA │    │       │
 └───────────────────────────────────────────────────────────────────────────────────┘
        │← 1 →│←──────────────── 9 bits data ──────────────────→│← 1 →│
        D8=1: Address frame (all slaves wake up)
        D8=0: Data frame (only addressed slave listens)
```

---

## DIAGRAM 05 — Bit-Level Timing Diagram

### Transmitting ASCII 'U' (0x55 = 01010101) at 115200 baud, 8N1

```
Voltage
(TTL 3.3V)
    │
3.3V├─ ─ ─┐     ┌──┐  ┌──┐  ┌──┐  ┌──┐  ┌─────────
    │  IDLE│     │  │  │  │  │  │  │  │  │  STOP+IDLE
    │      │     │  │  │  │  │  │  │  │  │
    │      │     │  │  │  │  │  │  │  │  │
 0V ├──────┴─────┘  └──┘  └──┘  └──┘  └──┘
    │ START  D0   D1   D2   D3   D4   D5   D6   D7  STOP
    │   0    1    0    1    0    1    0    1    0    1
    │
    │←8.68→│
    │  µs  │  (1 bit period at 115200 baud)
    │      │
    ├──────┼──────┼──────┼──────┼──────┼──────┼──────┼──────┼──────┼──────┤
    0    8.68  17.36  26.04  34.72  43.40  52.08  60.76  69.44  78.13  86.81 µs
    
    Total frame time: 86.81 µs (10 bits × 8.68 µs/bit)
    
    Note: 'U' (0x55) creates a nice alternating pattern — 
    this is why 'U' is used for auto-baud detection!
```

### 16× Oversampling Detail (Receiver Perspective)

```
TX Signal:
    ‾‾‾‾‾‾\_________/‾‾‾‾‾‾‾‾‾\_________/‾‾‾‾‾‾‾‾‾
           |← START →|←── D0 ──→|←── D1 ──→|

RX Sampling (16× clock):
    ||||||||||||||||||||||||||||||||||||||||||||||||||||
    ^               ^               ^               ^
    │               │               │               │
    Falling edge    Sample point    Sample point    Sample point
    detected!       (sample 8/16)   (sample 24/32)  (sample 40/48)
    Start counter   → START = 0 ✓   → D0 value      → D1 value

    Sample positions:  1  2  3  4  5  6  7 [8] 9 10 11 12 13 14 15 16
                                            ↑
                                      Middle sample used
                                      (±3 sample tolerance)
```

---

## DIAGRAM 06 — Hardware Block Diagram

```mermaid
graph LR
    subgraph "MCU / SoC"
        CPU[CPU Core<br/>ARM Cortex-M/A]
        DMA[DMA Controller]
        NVIC[Interrupt<br/>Controller]
        BUS[APB/AHB Bus]
        
        subgraph "UART Peripheral"
            BRG[Baud Rate<br/>Generator<br/>Divider + 16x]
            TXFIFO[TX FIFO<br/>16-256 bytes]
            TXSR[TX Shift Reg<br/>parallel→serial]
            TXLOGIC[TX Logic<br/>+start/stop/parity]
            RXLOGIC[RX Logic<br/>start detect<br/>16x sampling]
            RXSR[RX Shift Reg<br/>serial→parallel]
            RXFIFO[RX FIFO<br/>16-256 bytes]
            CTRL[Control Regs<br/>LCR/MCR/FCR]
            STATUS[Status Regs<br/>LSR/MSR/IIR]
            IRQGEN[IRQ Generator<br/>TX empty/RX full<br/>Error/Modem]
        end
    end

    subgraph "External"
        XCVR[Transceiver<br/>MAX232/MAX485<br/>or direct TTL]
        WIRE[Physical Wire<br/>TX/RX/GND]
        REMOTE[Remote Device]
    end

    CPU --> BUS
    BUS --> CTRL
    BUS --> TXFIFO
    RXFIFO --> BUS
    STATUS --> BUS
    DMA --> TXFIFO
    RXFIFO --> DMA
    IRQGEN --> NVIC
    NVIC --> CPU
    
    BRG --> TXLOGIC
    BRG --> RXLOGIC
    TXFIFO --> TXSR
    TXSR --> TXLOGIC
    RXLOGIC --> RXSR
    RXSR --> RXFIFO
    
    TXLOGIC -->|TX pin| XCVR
    XCVR -->|RX pin| RXLOGIC
    XCVR --> WIRE
    WIRE --> REMOTE
```

---

## DIAGRAM 07 — Complete Software Stack

```mermaid
graph TD
    subgraph "User Space"
        APP[Application<br/>minicom / screen / custom app]
        LIBC[C Library<br/>read/write/ioctl/tcsetattr]
    end

    subgraph "Kernel Space"
        VFS[VFS Layer<br/>open/read/write/close on /dev/ttyXX]
        TTY[TTY Core<br/>struct tty_struct<br/>N_TTY line discipline]
        SERCORE[Serial Core<br/>uart_port, uart_ops<br/>serial_core.c]
        DRIVER[Platform UART Driver<br/>8250 / PL011 / STM32 / MSM]
        IRQ[IRQ Handler<br/>Top-half ISR]
        DMA_K[DMA Engine<br/>dmaengine API]
    end

    subgraph "Hardware"
        REGS[UART Registers<br/>MMIO mapped]
        FIFO_HW[Hardware FIFO<br/>16-256 bytes]
        PHY_HW[TX/RX Pins<br/>GPIO Alt Function]
    end

    APP --> LIBC
    LIBC --> VFS
    VFS --> TTY
    TTY --> SERCORE
    SERCORE --> DRIVER
    DRIVER --> IRQ
    DRIVER --> DMA_K
    IRQ --> REGS
    DMA_K --> REGS
    REGS --> FIFO_HW
    FIFO_HW --> PHY_HW
```

---

## DIAGRAM 08 — Initialization Sequence

```mermaid
sequenceDiagram
    participant App as Application
    participant Driver as UART Driver
    participant HW as UART Hardware
    participant Pin as GPIO/Pinmux

    App->>Driver: open("/dev/ttyS0")
    Driver->>HW: Enable peripheral clock (RCC)
    Driver->>Pin: Configure TX pin as AF push-pull
    Driver->>Pin: Configure RX pin as AF input
    Driver->>HW: Disable UART (UE=0)
    Driver->>HW: Set baud rate (BRR register)
    Driver->>HW: Set frame format (LCR: 8N1)
    Driver->>HW: Enable FIFO (FCR: FIFO enable + reset)
    Driver->>HW: Set FIFO trigger level (FCR: RX trigger=8)
    Driver->>HW: Enable interrupts (IER: RXNE, ERR)
    Driver->>HW: Enable TX + RX + UART (CR1: TE+RE+UE=1)
    HW-->>Driver: Status: UART Ready
    Driver-->>App: fd returned (success)
    Note over HW: TX line goes HIGH (idle state)
    Note over HW: RX ready to detect falling edge
```

---

## DIAGRAM 09 — Data Transmission Sequence

```mermaid
sequenceDiagram
    participant App as Application
    participant Kernel as TTY/Serial Core
    participant Driver as UART Driver
    participant HW as UART Hardware
    participant Wire as Physical Wire
    participant Remote as Remote Device

    Note over App,Remote: === TRANSMIT PATH ===
    App->>Kernel: write(fd, "Hello", 5)
    Kernel->>Kernel: Line discipline processing
    Kernel->>Driver: uart_ops->start_tx()
    Driver->>HW: Write 'H' to TX Data Register
    HW->>HW: TX FIFO → TX Shift Register
    HW->>Wire: START(0) + 01001000 + STOP(1)
    Wire->>Remote: Electrical signal propagates
    HW-->>Driver: TX Empty interrupt (TXE)
    Driver->>HW: Write 'e' to TX Data Register
    Note over Driver,HW: Repeat for 'l','l','o'

    Note over App,Remote: === RECEIVE PATH ===
    Remote->>Wire: START(0) + data + STOP(1)
    Wire->>HW: Signal on RX pin
    HW->>HW: Detect falling edge (START)
    HW->>HW: 16× oversample each bit
    HW->>HW: Assemble byte in RX Shift Reg
    HW->>HW: Check parity + stop bit
    HW->>HW: Move to RX FIFO
    HW-->>Driver: RX Not Empty interrupt (RXNE)
    Driver->>HW: Read RX Data Register
    Driver->>Kernel: Push byte to TTY buffer
    Kernel->>Kernel: Line discipline (echo, CR/LF)
    Kernel-->>App: read() returns data
```

---

## DIAGRAM 10 — Error State Handling

```mermaid
stateDiagram-v2
    [*] --> Idle: Power on / UART enabled

    Idle --> Receiving: Falling edge on RX (Start bit)
    Receiving --> ByteReady: All bits sampled + stop bit OK
    Receiving --> FramingError: Stop bit = 0 (expected 1)
    Receiving --> ParityError: Parity mismatch
    Receiving --> NoiseError: Sample voting disagrees
    
    ByteReady --> FIFOStore: FIFO not full
    ByteReady --> OverrunError: FIFO full, byte lost
    
    FIFOStore --> Idle: Byte stored, wait next
    
    FramingError --> ErrorFlag: Set FE bit in LSR
    ParityError --> ErrorFlag: Set PE bit in LSR
    NoiseError --> ErrorFlag: Set NE bit in LSR
    OverrunError --> ErrorFlag: Set OE bit in LSR
    
    ErrorFlag --> IRQ: If error IRQ enabled
    IRQ --> ISR: CPU reads LSR, handles error
    ISR --> Idle: Error cleared, resume

    Idle --> BreakDetected: RX LOW > 1 frame time
    BreakDetected --> ErrorFlag: Set BI bit in LSR

    note right of FramingError: Most common cause:\nBaud rate mismatch
    note right of OverrunError: Most critical:\nData permanently lost
```

---

## DIAGRAM 11 — DMA Data Flow

```mermaid
graph TD
    subgraph "Memory (RAM)"
        TXBUF[TX Buffer<br/>Application data<br/>e.g., 256 bytes]
        RXBUF[RX Circular Buffer<br/>Ping-pong or ring<br/>e.g., 512 bytes]
    end

    subgraph "DMA Engine"
        TXDMA[TX DMA Channel<br/>Memory → Peripheral<br/>Src: TXBUF addr<br/>Dst: UART_DR addr<br/>Count: N bytes]
        RXDMA[RX DMA Channel<br/>Peripheral → Memory<br/>Src: UART_DR addr<br/>Dst: RXBUF addr<br/>Mode: Circular]
    end

    subgraph "UART Hardware"
        TXDR[TX Data Register]
        RXDR[RX Data Register]
        TXSHIFT[TX Shift Register]
        RXSHIFT[RX Shift Register]
        TXPIN[TX Pin →]
        RXPIN[← RX Pin]
    end

    subgraph "Interrupts"
        HTIRQ[Half-Transfer IRQ<br/>First half of RX buffer ready]
        TCIRQ[Transfer Complete IRQ<br/>Second half ready / TX done]
        IDLEIRQ[UART IDLE Line IRQ<br/>No more data coming]
    end

    TXBUF -->|DMA read| TXDMA
    TXDMA -->|DMA write| TXDR
    TXDR --> TXSHIFT
    TXSHIFT --> TXPIN

    RXPIN --> RXSHIFT
    RXSHIFT --> RXDR
    RXDR -->|DMA read| RXDMA
    RXDMA -->|DMA write| RXBUF

    RXDMA --> HTIRQ
    RXDMA --> TCIRQ
    RXDR --> IDLEIRQ
```

---

## DIAGRAM 12 — Linux Kernel Driver Architecture (8250)

```mermaid
graph TD
    subgraph "User Space"
        APP["/dev/ttyS0<br/>open/read/write/ioctl"]
    end

    subgraph "Kernel: TTY Layer"
        TTYCORE["tty_core.c<br/>struct tty_struct"]
        LDISC["n_tty.c<br/>Line Discipline<br/>(canonical/raw mode)"]
    end

    subgraph "Kernel: Serial Core"
        SCORE["serial_core.c<br/>struct uart_port<br/>struct uart_ops"]
    end

    subgraph "Kernel: 8250 Driver"
        M8250["8250_core.c<br/>serial8250_interrupt()<br/>serial8250_tx_chars()"]
        PORT["8250_port.c<br/>serial8250_handle_irq()"]
        PLAT["8250_platform.c / 8250_of.c<br/>Platform/DT binding"]
        DMA8250["8250_dma.c<br/>DMA operations"]
    end

    subgraph "Kernel: Platform"
        PBUS["Platform Bus / DT<br/>compatible = ns16550a"]
        CLK["Clock Framework<br/>clk_prepare_enable()"]
        PINCTRL["Pinctrl<br/>pinctrl-0 = uart_pins"]
    end

    subgraph "Hardware"
        HW["UART 16550 Registers<br/>RBR/THR/IER/IIR/FCR/LCR/MCR/LSR/MSR"]
    end

    APP --> TTYCORE
    TTYCORE --> LDISC
    LDISC --> SCORE
    SCORE --> M8250
    M8250 --> PORT
    PORT --> HW
    PLAT --> PBUS
    PLAT --> CLK
    PLAT --> PINCTRL
    M8250 --> DMA8250
    DMA8250 --> HW
```

---

## DIAGRAM 13 — Voltage Level Comparison

```
    RS-232 Levels              TTL Levels (3.3V)         RS-485 Differential
    
+15V ┤                    3.3V ┤ ─ ─ ─ HIGH ─ ─     +5V ┤
     │   ┌──── SPACE (0)       │ ████████████████         │   A > B → Logic 1
+3V  ┤───┘                     │                          │   ┌─── A line
     │                    1.65V ┤ · · · threshold         │───┘
     │   UNDEFINED              │                     2.5V ┤       (common mode)
-3V  ┤───┐                  0V ┤ ════ LOW ════════        │───┐
     │   └──── MARK (1)        │                          │   └─── B line
     │                          │                          │   B > A → Logic 0
-15V ┤                          │                      0V  ┤

     Logic 1 = NEGATIVE!        Logic 1 = HIGH             Logic 1 = A > B (≥200mV)
     Logic 0 = POSITIVE!        Logic 0 = LOW              Logic 0 = B > A (≥200mV)
     (INVERTED from TTL)        (Standard)                 (Differential — noise immune)
```

---

## DIAGRAM 14 — Auto-Baud Detection

```mermaid
sequenceDiagram
    participant TX as Transmitter
    participant RX as Receiver (auto-baud)

    Note over RX: RX does NOT know baud rate yet
    TX->>RX: Send 0x55 ('U' = 01010101)
    Note over RX: Measure time between edges
    RX->>RX: Falling edge (START) → Rising edge (D0=1)
    RX->>RX: Time = 1 bit period
    RX->>RX: Calculate: baud = 1 / measured_time
    RX->>RX: Configure own baud rate
    Note over RX: Ready for normal communication
    TX->>RX: Normal data frames
    RX-->>TX: ACK (communication established)
    
    Note over TX,RX: Why 'U' (0x55)?<br/>Alternating 0/1 pattern gives<br/>maximum edges for measurement.<br/>Also: 0x7F, 'a' (0x61) work well.
```

---

## DIAGRAM 15 — Flow Control (RTS/CTS) Sequence

```mermaid
sequenceDiagram
    participant A as Device A (Sender)
    participant B as Device B (Receiver)

    Note over A,B: Normal operation (CTS asserted = LOW)
    A->>B: Data byte 1
    A->>B: Data byte 2
    A->>B: Data byte ...
    A->>B: Data byte N
    
    Note over B: RX buffer nearly full (watermark reached)
    B-->>A: De-assert RTS (→ A sees CTS go HIGH)
    Note over A: STOP transmitting immediately
    A->>A: Buffer remaining data locally
    
    Note over B: Process buffered data, drain buffer
    B->>B: Application reads data
    
    Note over B: Buffer has space again
    B-->>A: Assert RTS (→ A sees CTS go LOW)
    Note over A: RESUME transmitting
    A->>B: Data byte N+1
    A->>B: Data byte N+2
```

---

## DIAGRAM 16 — Real System Integration Example

```mermaid
graph TD
    subgraph "Automotive Head Unit (SA8155P)"
        AP[Application Processor<br/>Qualcomm SA8155P]
        
        AP -->|UART0: Debug Console<br/>115200 8N1| DBG[JTAG/Serial Header<br/>→ Developer PC]
        AP -->|UART1: BT HCI H4<br/>3 Mbps| BT[Qualcomm QCA6174<br/>Bluetooth 5.0 chip]
        AP -->|UART2: GPS NMEA<br/>115200 8N1| GPS[u-blox NEO-M8N<br/>GPS Module]
        AP -->|UART3: Modem AT<br/>921600 8N1| MODEM[Qualcomm SDX55<br/>5G Modem]
    end

    subgraph "Body Control MCU"
        MCU[STM32H743]
        
        MCU -->|UART4: K-Line ISO 9141<br/>10400 baud| OBD[OBD-II Port<br/>Diagnostics]
        MCU -->|UART5: RS-485 Modbus<br/>19200 8N1| SENSOR[Body Sensors<br/>Temperature, etc.]
        MCU -->|UART6: LIN Master<br/>19200| LIN_BUS[LIN Bus<br/>Seat/Mirror/Window]
    end

    subgraph "Developer Tools"
        PC[Developer PC]
        PC -->|USB-UART Bridge<br/>FTDI FT232R| DBG
        PC -->|adb / fastboot| AP
    end
```

---

## DIAGRAM 17 — Debugging Decision Tree

```mermaid
flowchart TD
    START[Problem: UART not working] --> Q1{Any signal on<br/>TX pin? (scope)}
    
    Q1 -->|No signal| CHK1[Check: Clock enabled?<br/>Pin configured as AF?<br/>UART enabled?<br/>TX enable bit set?]
    Q1 -->|Signal present| Q2{Signal looks<br/>correct on scope?}
    
    Q2 -->|Garbled/wrong timing| CHK2[Baud rate wrong!<br/>Check: BRR value<br/>Clock frequency<br/>Prescaler setting]
    Q2 -->|Looks good| Q3{Remote receiving?}
    
    Q3 -->|No| Q4{TX/RX crossed?}
    Q4 -->|Check wiring| CHK3[TX→RX, RX→TX<br/>Common GND connected?<br/>Voltage level match?<br/>3.3V vs 5V vs RS-232?]
    
    Q3 -->|Partial/garbled| Q5{Framing errors<br/>on receiver?}
    Q5 -->|Yes| CHK4[Config mismatch!<br/>Check: data bits (7/8)<br/>parity (N/E/O)<br/>stop bits (1/2)<br/>BOTH sides must match]
    
    Q5 -->|No framing err<br/>but data wrong| Q6{Overrun errors?}
    Q6 -->|Yes| CHK5[RX too slow!<br/>Fix: Enable FIFO<br/>Use DMA<br/>Raise ISR priority<br/>Reduce baud rate]
    Q6 -->|No| CHK6[Check: byte order<br/>Endianness<br/>Character encoding<br/>Line ending CR/LF]

    Q3 -->|Works but<br/>intermittent| Q7{Temperature<br/>dependent?}
    Q7 -->|Yes| CHK7[Clock source!<br/>RC oscillator drifts.<br/>Use crystal/TCXO]
    Q7 -->|No| CHK8[EMI / noise!<br/>Check: cable shielding<br/>ground loop<br/>nearby switching noise]
```

---

## DIAGRAM 18 — FIFO Operation & Watermarks

```
TX FIFO (16 bytes):                    RX FIFO (16 bytes):
┌──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┐
│  │  │  │  │  │  │  │  │  │  │  │  │  │  │H │e │  ← CPU writes here
└──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┘
                                        ↓ shift out
                                   TX pin → wire

RX FIFO fills from hardware side:
┌──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┐
│W │o │r │l │d │  │  │  │  │  │  │  │  │  │  │  │  ← HW writes here
└──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┴──┘
 ↑ CPU reads here
 
FIFO Trigger Levels (interrupt fires when RX FIFO has ≥ N bytes):
  Level 1:  IRQ after 1 byte   (immediate, high CPU overhead)
  Level 4:  IRQ after 4 bytes  (balanced)
  Level 8:  IRQ after 8 bytes  (efficient, some latency)
  Level 14: IRQ after 14 bytes (maximum efficiency, risk of overrun)
```

---

## DIAGRAM 19 — Complete Register Map (16550)

```
Offset  DLAB=0 Read     DLAB=0 Write    DLAB=1 Read/Write   Bits
──────  ────────────    ─────────────   ─────────────────   ────────────────────
0x00    RBR             THR             DLL                 [7:0] Data / Divisor Low
0x01    IER             IER             DLM                 [7:0] IRQ Enable / Div High
0x02    IIR             FCR             IIR/FCR             IRQ ID / FIFO Control
0x03    LCR             LCR             LCR                 Line Control
0x04    MCR             MCR             MCR                 Modem Control
0x05    LSR             —               LSR                 Line Status (read-only)
0x06    MSR             —               MSR                 Modem Status (read-only)
0x07    SCR             SCR             SCR                 Scratch Register

Key Register Bits:
─────────────────

LCR (Line Control Register) - 0x03:
┌────┬────┬────┬────┬────┬────┬────┬────┐
│ D7 │ D6 │ D5 │ D4 │ D3 │ D2 │ D1 │ D0 │
│DLAB│BRK │StkP│EPS │PEN │STB │WLS1│WLS0│
└────┴────┴────┴────┴────┴────┴────┴────┘
 DLAB: Divisor Latch Access Bit
 BRK:  Break Control
 PEN:  Parity Enable
 EPS:  Even Parity Select
 STB:  Stop Bits (0=1 stop, 1=2 stop)
 WLS:  Word Length (00=5, 01=6, 10=7, 11=8)

LSR (Line Status Register) - 0x05:
┌────┬────┬────┬────┬────┬────┬────┬────┐
│ D7 │ D6 │ D5 │ D4 │ D3 │ D2 │ D1 │ D0 │
│ERF │TEMT│THRE│ BI │ FE │ PE │ OE │ DR │
└────┴────┴────┴────┴────┴────┴────┴────┘
 DR:   Data Ready (byte available in RBR)
 OE:   Overrun Error
 PE:   Parity Error
 FE:   Framing Error
 BI:   Break Interrupt
 THRE: TX Holding Register Empty
 TEMT: Transmitter Empty (shift reg empty too)
 ERF:  Error in FIFO
```

---

END OF DOCUMENT 02 — DIAGRAMS
