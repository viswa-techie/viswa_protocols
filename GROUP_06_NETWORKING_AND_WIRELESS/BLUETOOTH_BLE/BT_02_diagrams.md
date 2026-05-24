# BLUETOOTH / BLE PROTOCOL — DIAGRAMS & VISUAL REFERENCES
# ════════════════════════════════════════════════════════════════════
# Protocol: Bluetooth Classic & BLE | Document: 02 of 08
# Format: ASCII art, Mermaid, state machines, timing diagrams
# ════════════════════════════════════════════════════════════════════

---

## 1. BLUETOOTH CLASSIC PROTOCOL STACK

```
┌─────────────────────────────────────────────────────────────────┐
│                         APPLICATIONS                              │
│   Phone Call │ Music │ Contacts │ Messages │ Serial Data         │
├──────────────┼───────┼──────────┼──────────┼────────────────────┤
│     HFP      │ A2DP  │  PBAP   │   MAP    │       SPP          │
│              │ AVRCP │         │          │                     │
├──────────────┤       │         │          ├────────────────────┤
│   AT Cmds    │ AVDTP │  OBEX   │  OBEX    │      RFCOMM        │
│              │ AVCTP │         │          │  (Serial Emulation)│
├──────────────┴───────┴─────────┴──────────┴────────────────────┤
│                          SDP                                      │
│              (Service Discovery Protocol)                         │
├─────────────────────────────────────────────────────────────────┤
│                         L2CAP                                     │
│   (Logical Link Control & Adaptation Protocol)                   │
│   Multiplexing | Segmentation | Flow Control | QoS              │
├─────────────────────────────────────────────────────────────────┤
│                  HCI (Host Controller Interface)                  │
│════════════════════ UART / USB / SDIO ══════════════════════════│
├─────────────────────────────────────────────────────────────────┤
│         Link Manager Protocol (LMP)                              │
│   (Authentication, Encryption, Power Control, QoS)              │
├─────────────────────────────────────────────────────────────────┤
│              Baseband / Link Controller (LC)                      │
│   (Packet Assembly, FH Sequence, ARQ, FEC, Whitening)          │
├─────────────────────────────────────────────────────────────────┤
│                      RADIO (PHY)                                 │
│   GFSK (1M) │ π/4-DQPSK (2M) │ 8DPSK (3M)                    │
│   2.4 GHz ISM Band │ 79 Channels │ FHSS                        │
└─────────────────────────────────────────────────────────────────┘
```

---

## 2. BLE PROTOCOL STACK

```
┌─────────────────────────────────────────────────────────────────┐
│                         APPLICATIONS                              │
│   Heart Rate │ Battery │ Custom │ Digital Key │ Mesh            │
├──────────────┴─────────┴────────┴─────────────┴────────────────┤
│              GATT (Generic Attribute Profile)                     │
│         Client/Server model — Services & Characteristics        │
├─────────────────────────────────────────────────────────────────┤
│              ATT (Attribute Protocol)                             │
│         Read/Write/Notify/Indicate operations                   │
├─────────────────────────────┬───────────────────────────────────┤
│   GAP (Generic Access       │  SMP (Security Manager Protocol)  │
│   Profile — Discovery,      │  Pairing, Key Distribution,       │
│   Connection, Roles)        │  Encryption                       │
├─────────────────────────────┴───────────────────────────────────┤
│                         L2CAP (LE)                                │
│   Fixed Channels: ATT(0x04) │ SMP(0x06) │ Signal(0x05)         │
│   Dynamic: L2CAP CoC (credit-based flow control)                │
├─────────────────────────────────────────────────────────────────┤
│                  HCI (Host Controller Interface)                  │
│════════════════════ UART / USB / SDIO ══════════════════════════│
├─────────────────────────────────────────────────────────────────┤
│                    Link Layer (LL)                                │
│   Advertising │ Scanning │ Initiating │ Connection              │
│   Channel Map │ Hop Algorithm │ Encryption │ Data PDU           │
├─────────────────────────────────────────────────────────────────┤
│                      PHY (Physical Layer)                         │
│   LE 1M │ LE 2M │ LE Coded (S=2, S=8)                         │
│   2.4 GHz │ 40 Channels (37 data + 3 advertising)             │
└─────────────────────────────────────────────────────────────────┘
```

---

## 3. PICONET TOPOLOGY

```
                    ┌───────────────────────┐
                    │   MASTER (M)          │
                    │   Controls timing     │
                    │   Determines hop seq  │
                    │   Polls slaves        │
                    └───┬───┬───┬───┬───┬──┘
                        │   │   │   │   │
            ┌───────────┘   │   │   │   └───────────┐
            │               │   │   │               │
     ┌──────▼──────┐ ┌─────▼─┐ │ ┌─▼─────┐ ┌──────▼──────┐
     │  Slave 1    │ │ S2    │ │ │ S3    │ │  Slave 4    │
     │  (Active)   │ │(Active)│ │ │(Active)│ │  (Active)   │
     │  Phone A    │ │Phone B│ │ │Headset│ │  OBD-II     │
     └─────────────┘ └───────┘ │ └───────┘ └─────────────┘
                                │
                         ┌──────▼──────┐
                         │  Slave 5    │
                         │  (Parked)   │
                         │  Low power  │
                         └─────────────┘

    Rules:
    • 1 Master + up to 7 Active Slaves
    • Up to 255 Parked Slaves (low power, can't transmit)
    • Master assigns 3-bit Active Member Address (AMA)
    • Parked slaves get 8-bit Parked Member Address (PMA)
    • TDD: Master TX even slots, Slave TX odd slots
    • Slave can only transmit when polled by Master
```

---

## 4. SCATTERNET (OVERLAPPING PICONETS)

```
       Piconet A                         Piconet B
    ┌─────────────┐                   ┌─────────────┐
    │  Master A   │                   │  Master B   │
    └──┬──┬──┬────┘                   └──┬──┬──┬────┘
       │  │  │                           │  │  │
    ┌──▼┐ │  ▼──┐                     ┌──▼┐ │  ▼──┐
    │S1 │ │ │S2 │                     │S4 │ │ │S5 │
    └───┘ │ └───┘                     └───┘ │ └───┘
          │                                 │
          └────────────┐   ┌────────────────┘
                       │   │
                    ┌──▼───▼──┐
                    │  S3/M?   │  ← Bridge device
                    │  Slave   │     (Slave in A,
                    │  in both │      Slave/Master in B)
                    └──────────┘
    
    • Device can be slave in multiple piconets
    • Device can be master in one piconet only
    • Time-sharing between piconets (performance impact)
```

---

## 5. 2.4 GHz SPECTRUM — BLUETOOTH CHANNELS

```
WiFi Channel 1          WiFi Channel 6          WiFi Channel 11
  (2412 MHz)              (2437 MHz)              (2462 MHz)
    ┌──22MHz──┐           ┌──22MHz──┐           ┌──22MHz──┐
    │         │           │         │           │         │
────┤█████████├───────────┤█████████├───────────┤█████████├────
    │         │           │         │           │         │
    └─────────┘           └─────────┘           └─────────┘
2401 MHz                                                  2480 MHz
├─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┼─┤
 0 1 2 3 4 5 6 7 8 9 . . . . . . . . . . . . . . . . 78
 └──────────────── 79 BT Classic Channels (1 MHz each) ──┘

BLE Channels (2 MHz spacing):
├──┤  ├──┤  ├──┤  ├──┤  ├──┤ . . . . . . . ├──┤  ├──┤  ├──┤
 37   0   1   2   3   4                      36  38      39
 Adv  ←── 37 Data Channels (2 MHz each) ──→  Adv       Adv
 2402     2404-2478 MHz                       2426      2480

BLE Advertising channels placed to AVOID WiFi overlap:
  Ch 37 (2402): Below WiFi Ch 1
  Ch 38 (2426): Between WiFi Ch 1 and Ch 6
  Ch 39 (2480): Above WiFi Ch 11
```

---

## 6. BLE ADVERTISING & CONNECTION ESTABLISHMENT

```mermaid
sequenceDiagram
    participant P as Peripheral (Advertiser)
    participant C as Central (Scanner/Initiator)
    
    Note over P: Advertising state
    P->>C: ADV_IND [ch 37] (Address, Flags, Name, UUID)
    P->>C: ADV_IND [ch 38] (same data)
    P->>C: ADV_IND [ch 39] (same data)
    Note over P: Wait advertising interval (20ms-10.24s)
    
    P->>C: ADV_IND [ch 37]
    Note over C: Scanning, receives ADV_IND
    
    C->>P: SCAN_REQ [ch 37]
    P->>C: SCAN_RSP [ch 37] (Additional data: TX Power, Services)
    
    Note over C: Decides to connect
    P->>C: ADV_IND [ch 38]
    C->>P: CONNECT_IND [ch 38] (Access Address, CRC Init, Window, Interval, Latency, Timeout, Channel Map, Hop)
    
    Note over P,C: Connection established!
    Note over P: Becomes Slave (Peripheral)
    Note over C: Becomes Master (Central)
    
    Note over P,C: First connection event after transmitWindowOffset + transmitWindowSize
    C->>P: Data PDU (empty or with data)
    P->>C: Data PDU (empty or with data)
    Note over P,C: Hop to next channel for next event
```

---

## 7. BLE CONNECTION EVENT TIMING

```
              Connection Interval (CI = 7.5ms - 4s)
    ├────────────────────────────────────────────────────┤
    
    Event N                                    Event N+1
    ┌──────────────────┐                      ┌──────────────────┐
    │                  │                      │                  │
    │  M──▶S  S──▶M   │     (Sleep)          │  M──▶S  S──▶M   │
    │  TX    RX        │                      │  TX    RX        │
    │                  │                      │                  │
    └──────────────────┘                      └──────────────────┘
    │◀─ T_IFS ─▶│                             
    │  (150µs)  │
    
    Master TX        Slave TX        
    ┌─────────┐     ┌─────────┐     
    │ Header  │     │ Header  │     
    │ Payload │     │ Payload │     
    │(0-251B) │     │(0-251B) │     
    └─────────┘     └─────────┘     
    
    Multiple packets per event (if data pending):
    ┌────┐  ┌────┐  ┌────┐  ┌────┐  ┌────┐  ┌────┐
    │M→S │  │S→M │  │M→S │  │S→M │  │M→S │  │S→M │
    └────┘  └────┘  └────┘  └────┘  └────┘  └────┘
    ◀────── 150µs between each (T_IFS) ──────────▶
    
    Slave Latency = 3:
    Event N   Event N+1  Event N+2  Event N+3   Event N+4
    ┌──────┐  ┌──────┐   (skip)     (skip)     ┌──────┐
    │M→S   │  │M→S   │                          │M→S   │
    │S→M   │  │Empty │   (Slave sleeps)         │S→M   │
    └──────┘  └──────┘                          └──────┘
```

---

## 8. BLE PACKET FORMAT

```
BLE Link Layer PDU (on air):
┌──────────┬──────────┬────────────────────────────┬────────┐
│ Preamble │ Access   │         PDU                 │  CRC   │
│  (1-2B)  │ Address  │   (Header + Payload)       │  (3B)  │
│          │  (4B)    │                            │        │
└──────────┴──────────┴────────────────────────────┴────────┘

Advertising PDU:
┌──────────────────────────────────────────────────────────────┐
│ Header (2B)              │ Payload (6-37B or 0-255B ext)     │
│ ┌──────┬─────┬────────┐ │ ┌──────────┬─────────────────────┐│
│ │PDU   │TxAdd│Length   │ │ │AdvA      │ AdvData             ││
│ │Type  │RxAdd│(6 bits)│ │ │(6 bytes) │ (0-31 or 0-254B)   ││
│ │(4bit)│(2b) │        │ │ │          │                     ││
│ └──────┴─────┴────────┘ │ └──────────┴─────────────────────┘│
└──────────────────────────────────────────────────────────────┘

Data PDU:
┌──────────────────────────────────────────────────────────────┐
│ Header (2-3B)            │ Payload (0-251B)     │ MIC (4B)  │
│ ┌──────┬────┬───┬──────┐│                      │(encrypted)│
│ │LLID  │NESN│SN │Length ││ L2CAP or LL Control  │           │
│ │(2bit)│(1b)│(1b│(8bit)││                      │           │
│ └──────┴────┴───┴──────┘│                      │           │
└──────────────────────────────────────────────────────────────┘

LLID values:
  01 = L2CAP (continuation fragment)
  10 = L2CAP (start or complete)
  11 = LL Control PDU (connection management)
```

---

## 9. BLE STATE MACHINE

```mermaid
stateDiagram-v2
    [*] --> Standby
    
    Standby --> Advertising : Start advertising
    Standby --> Scanning : Start scanning
    Standby --> Initiating : Start initiating
    
    Advertising --> Standby : Stop advertising
    Advertising --> Connection_Slave : CONNECT_IND received
    
    Scanning --> Standby : Stop scanning
    Scanning --> Initiating : Found target, connect
    
    Initiating --> Standby : Cancel / timeout
    Initiating --> Connection_Master : Connection established
    
    Connection_Master --> Standby : Disconnect
    Connection_Slave --> Standby : Disconnect
    
    Standby --> Synchronization : Sync to periodic adv
    Synchronization --> Standby : Sync lost / terminate
    
    Standby --> Isochronous_Broadcast : BIS established
    Connection_Master --> Connection_Master : Data exchange
    Connection_Slave --> Connection_Slave : Data exchange
```

---

## 10. GATT SERVICE HIERARCHY

```
┌─────────────────────────────────────────────────────────────────┐
│                    GATT DATABASE (Server)                         │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌─── Service: Generic Access (0x1800) ──────────────────────┐  │
│  │  ├── Char: Device Name (0x2A00) [Read, Write]             │  │
│  │  │       Value: "Car_HeadUnit_BLE"                        │  │
│  │  └── Char: Appearance (0x2A01) [Read]                     │  │
│  │          Value: 0x0341 (Automotive)                       │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                   │
│  ┌─── Service: Heart Rate (0x180D) ──────────────────────────┐  │
│  │  ├── Char: HR Measurement (0x2A37) [Notify]               │  │
│  │  │   ├── Value: [Flags=0x06][HR=72][RR=820ms]            │  │
│  │  │   └── Desc: CCCD (0x2902) = 0x0001 (notify enabled)   │  │
│  │  ├── Char: Body Sensor Location (0x2A38) [Read]           │  │
│  │  │       Value: 0x01 (Chest)                              │  │
│  │  └── Char: HR Control Point (0x2A39) [Write]              │  │
│  │          Value: 0x01 (reset energy expended)              │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                   │
│  ┌─── Service: Custom (128-bit UUID) ────────────────────────┐  │
│  │  ├── Char: Digital Key Auth (custom UUID) [Write, Indicate]│  │
│  │  │   ├── Value: [challenge-response data]                 │  │
│  │  │   └── Desc: CCCD (0x2902) = 0x0002 (indicate enabled) │  │
│  │  └── Char: Vehicle Status (custom UUID) [Read, Notify]    │  │
│  │          Value: [locked=1, engine=0, temp=22]             │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘

Handle layout:
Handle 0x0001: Service Declaration (Generic Access)
Handle 0x0002: Char Declaration (Device Name)
Handle 0x0003: Char Value (Device Name)
Handle 0x0004: Char Declaration (Appearance)
Handle 0x0005: Char Value (Appearance)
Handle 0x0010: Service Declaration (Heart Rate)
Handle 0x0011: Char Declaration (HR Measurement)
Handle 0x0012: Char Value (HR Measurement)
Handle 0x0013: CCCD Descriptor
...
```

---

## 11. BLUETOOTH CLASSIC CONNECTION FLOW

```mermaid
sequenceDiagram
    participant M as Master (Head Unit)
    participant S as Slave (Phone)
    
    Note over M: Inquiry (discover devices)
    M->>S: Inquiry (ID packet on 32 hop frequencies)
    S->>M: FHS packet (BD_ADDR, clock, class)
    
    Note over M: Paging (connect to specific device)
    M->>S: Page (ID packet, slave's hop sequence)
    S->>M: Page Response (ID)
    M->>S: FHS (master clock, BD_ADDR)
    S->>M: FHS ACK
    
    Note over M,S: Baseband connection established (ACL)
    
    M->>S: LMP_host_connection_req
    S->>M: LMP_accepted
    
    Note over M,S: Authentication (if bonded)
    M->>S: LMP_au_rand (challenge)
    S->>M: LMP_sres (response using link key)
    
    Note over M,S: Encryption
    M->>S: LMP_encryption_mode_req
    S->>M: LMP_accepted
    M->>S: LMP_start_encryption_req
    S->>M: LMP_accepted
    
    Note over M,S: Encrypted ACL link ready
    
    M->>S: L2CAP Connection Request (PSM=0x0003, RFCOMM)
    S->>M: L2CAP Connection Response
    M->>S: L2CAP Configuration Request
    S->>M: L2CAP Configuration Response
    
    Note over M,S: L2CAP channel established → Profile setup
```

---

## 12. A2DP STREAMING FLOW

```mermaid
sequenceDiagram
    participant Src as Source (Phone)
    participant Snk as Sink (Head Unit)
    
    Note over Src,Snk: ACL + L2CAP already connected
    
    Snk->>Src: AVDTP Discover
    Src->>Snk: Discover Response (SEP: SBC, AAC, aptX)
    
    Snk->>Src: AVDTP Get Capabilities (SEID=1, SBC)
    Src->>Snk: Capabilities (SBC params: 44.1kHz, Joint Stereo, 53 blocks)
    
    Snk->>Src: AVDTP Get Capabilities (SEID=2, AAC)
    Src->>Snk: Capabilities (AAC params: 44.1kHz, VBR, 256kbps)
    
    Note over Snk: Selects best codec (AAC)
    Snk->>Src: AVDTP Set Configuration (SEID=2, AAC, 44.1kHz)
    Src->>Snk: Accept
    
    Snk->>Src: AVDTP Open
    Src->>Snk: Accept
    
    Note over Src,Snk: Streaming channel (L2CAP) opened
    
    Snk->>Src: AVDTP Start
    Src->>Snk: Accept
    
    Note over Src,Snk: Audio streaming begins
    loop Every 20-30ms (frame interval)
        Src->>Snk: Media packet (RTP header + AAC frame)
    end
    
    Note over Snk: User presses Pause (AVRCP)
    Snk->>Src: AVRCP PassThrough (Pause)
    Src->>Snk: AVRCP Response (Accepted)
    Snk->>Src: AVDTP Suspend
    Src->>Snk: Accept
```

---

## 13. HFP CALL FLOW

```mermaid
sequenceDiagram
    participant Phone as Phone (AG - Audio Gateway)
    participant HU as Head Unit (HF - Hands-Free)
    
    Note over Phone,HU: SLC (Service Level Connection) already established via RFCOMM
    
    Note over Phone: Incoming call from +1234567890
    Phone->>HU: +CIEV: (callsetup=1) [incoming call]
    Phone->>HU: RING
    Phone->>HU: +CLIP: "+1234567890","John" [caller ID]
    
    Note over HU: Display "Incoming: John" + ring tone
    
    HU->>Phone: ATA [Answer call]
    Phone->>HU: OK
    Phone->>HU: +CIEV: (call=1, callsetup=0)
    
    Note over Phone,HU: SCO/eSCO link established (audio path)
    Note over Phone,HU: Voice audio flowing bidirectionally
    
    Note over HU: User presses hangup
    HU->>Phone: AT+CHUP [Hang up]
    Phone->>HU: OK
    Phone->>HU: +CIEV: (call=0)
    
    Note over Phone,HU: SCO link released
```

---

## 14. BLE PAIRING (LE SECURE CONNECTIONS)

```mermaid
sequenceDiagram
    participant I as Initiator (Central/Phone)
    participant R as Responder (Peripheral/Car)
    
    Note over I,R: BLE connection established
    
    I->>R: Pairing Request (IO Cap, OOB, AuthReq, Key Size)
    R->>I: Pairing Response (IO Cap, OOB, AuthReq, Key Size)
    
    Note over I,R: Phase 2: Key Generation (ECDH)
    I->>R: Public Key (Qa)
    R->>I: Public Key (Qb)
    Note over I,R: Both compute DHKey = ECDH(SKa, Qb) = ECDH(SKb, Qa)
    
    Note over I,R: Authentication (Numeric Comparison shown)
    I->>R: Confirm Value (Ca = f4(Qa, Qb, Na, 0))
    R->>I: Confirm Value (Cb = f4(Qb, Qa, Nb, 0))
    I->>R: Random (Na)
    R->>I: Random (Nb)
    
    Note over I,R: Both compute 6-digit number: g2(Qa, Qb, Na, Nb)
    Note over I: Display "123456"
    Note over R: Display "123456"
    Note over I,R: User confirms match on both devices
    
    Note over I,R: Phase 3: Key Distribution
    Note over I,R: Derive LTK from DHKey + Na + Nb
    I->>R: Encryption (start encrypted link with LTK)
    
    I->>R: IRK + Identity Address (for privacy)
    R->>I: IRK + Identity Address
    I->>R: CSRK (signing key)
    R->>I: CSRK
    
    Note over I,R: Bonding complete — keys stored
```

---

## 15. FREQUENCY HOPPING PATTERN (CLASSIC)

```
Time (slots, 625µs each):
──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──►
  S0 S1 S2 S3 S4 S5 S6 S7 S8 S9 ...

Frequency (channel):
79 ┤                              ■
   │              ■                           ■
60 ┤    ■                   ■
   │                                    ■
40 ┤         ■                                     ■
   │                        
20 ┤                   ■              ■
   │ ■                                        
 0 ┤──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──►
     S0 S1 S2 S3 S4 S5 S6 S7 S8 S9 S10 ...

  ■ = Frequency used in that slot
  Pattern: Pseudo-random based on Master clock + BD_ADDR
  Rate: 1600 hops/second (every 625µs)

With AFH (channels 20-40 marked bad → WiFi):
79 ┤                              ■
   │              ■                           ■
60 ┤    ■                   ■
   │                                    ■
40 ┤─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ 
   │ ╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳╳  (excluded channels)
20 ┤─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
   │ ■       ■                            ■
 0 ┤──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──┬──►
     (hops remap to non-excluded channels)
```

---

## 16. ANDROID BLUETOOTH ARCHITECTURE

```mermaid
graph TB
    subgraph "Application Layer"
        APP[Android App]
        SETTINGS[Settings/SystemUI]
    end
    
    subgraph "Framework (Java/Kotlin)"
        BM[BluetoothManager]
        BA[BluetoothAdapter]
        BMS[BluetoothManagerService]
        BS[BluetoothService]
    end
    
    subgraph "Native Stack (C++/Rust)"
        FLUORIDE[Fluoride / Gabeldorsche]
        PROFILES[Profile Implementations]
        A2DP_S[A2DP]
        HFP_S[HFP]
        GATT_S[GATT]
        SMP_S[SMP]
        L2CAP_S[L2CAP]
        HCI_L[HCI Layer]
    end
    
    subgraph "HAL Layer"
        AIDL[AIDL HAL: IBluetoothHci]
        VENDOR[Vendor Implementation]
    end
    
    subgraph "Kernel"
        UART_D[hci_uart / btqca driver]
    end
    
    subgraph "Hardware"
        BT_CHIP[QCA6698AQ Controller]
        RF[RF / Antenna]
    end
    
    APP --> BM --> BA
    SETTINGS --> BMS
    BMS --> BS
    BS --> FLUORIDE
    FLUORIDE --> PROFILES
    PROFILES --> A2DP_S
    PROFILES --> HFP_S
    PROFILES --> GATT_S
    FLUORIDE --> SMP_S
    FLUORIDE --> L2CAP_S
    L2CAP_S --> HCI_L
    HCI_L --> AIDL
    AIDL --> VENDOR
    VENDOR --> UART_D
    UART_D --> BT_CHIP
    BT_CHIP --> RF
```

---

## 17. AUTOMOTIVE BT AUDIO ROUTING

```
┌─────────────────────────────────────────────────────────────────┐
│                    SA8295P Audio Subsystem                        │
│                                                                   │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │ BT Audio Inputs                                             │ │
│  │                                                             │ │
│  │  Phone A ──BT──▶ QCA6698AQ ──PCM──▶ ┌───────────────┐    │ │
│  │  (A2DP: Music)              Decode   │               │    │ │
│  │                                      │  Audio HAL    │    │ │
│  │  Phone B ──BT──▶ QCA6698AQ ──PCM──▶ │  (mixer)      │    │ │
│  │  (HFP: Call)                Decode   │               │    │ │
│  │                                      │  Duck/Mix/    │    │ │
│  │  Navigation TTS ───────────────────▶ │  Route        │    │ │
│  │                                      │               │    │ │
│  │  Chime/Alert ──────────────────────▶ │               │────┼─▶ AMP
│  │                                      └───────────────┘    │ │
│  └────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  Audio Policy (priority):                                        │
│  1. Emergency / Chime (highest)                                  │
│  2. Phone Call (HFP) → duck everything else                      │
│  3. Navigation TTS → duck music                                  │
│  4. Music (A2DP) → lowest continuous                             │
│                                                                   │
│  Microphone path:                                                │
│  Mic array ──▶ AEC/NS DSP ──▶ PCM ──▶ QCA6698AQ ──BT──▶ Phone │
└─────────────────────────────────────────────────────────────────┘
```

---

## 18. BLE DIGITAL KEY FLOW

```mermaid
sequenceDiagram
    participant Phone as Phone (BLE + UWB)
    participant Car as Car (BLE + UWB)
    
    Note over Phone: Background BLE advertising (periodic)
    
    Note over Car: BLE scanning (low duty cycle)
    Car->>Phone: Scan Response Request
    Phone->>Car: ADV_IND (Directed or with Filter)
    
    Note over Car: RSSI above threshold (-70 dBm) → phone nearby
    Car->>Phone: CONNECT_IND (BLE connection)
    
    Phone->>Car: GATT: Read Vehicle ID
    Car->>Phone: GATT: Vehicle ID response
    
    Note over Phone,Car: Mutual Authentication (challenge-response)
    Car->>Phone: GATT: Write Auth Challenge (random nonce)
    Phone->>Car: GATT: Indicate Auth Response (signed with private key)
    Phone->>Car: GATT: Write Auth Challenge (random nonce)
    Car->>Phone: GATT: Indicate Auth Response (signed)
    
    Note over Phone,Car: Authentication successful
    
    Note over Phone,Car: Start UWB ranging session
    Car->>Phone: GATT: UWB Session Config (channel, preamble)
    Phone->>Car: GATT: UWB Session Ack
    
    Note over Phone,Car: UWB ranging active
    loop Every 100ms
        Car->>Phone: UWB: Ranging frame
        Phone->>Car: UWB: Ranging response
        Note over Car: Distance = ToF × c / 2
    end
    
    Note over Car: Distance < 2m → Unlock
    Note over Car: Distance < 1m + inside → Enable Start
```

---

## 19. HCI PACKET FORMAT

```
HCI Command Packet:
┌────────────┬──────────┬─────────────────────────────────┐
│  Indicator │  OpCode  │ Param  │      Parameters        │
│   (1 byte) │ (2 bytes)│ Length │    (0-255 bytes)       │
│   0x01     │ OGF|OCF  │(1 byte)│                        │
└────────────┴──────────┴────────┴────────────────────────┘
OpCode: OGF (6 bits, group) | OCF (10 bits, command)
  OGF=0x01: Link Control, OGF=0x03: HC & Baseband
  OGF=0x04: Informational, OGF=0x08: LE Controller

HCI Event Packet:
┌────────────┬──────────┬─────────────────────────────────┐
│  Indicator │  Event   │ Param  │     Parameters         │
│   (1 byte) │  Code    │ Length │   (0-255 bytes)        │
│   0x04     │ (1 byte) │(1 byte)│                        │
└────────────┴──────────┴────────┴────────────────────────┘
Common events: 0x0E=Command Complete, 0x0F=Command Status
               0x03=Connection Complete, 0x05=Disconnection
               0x3E=LE Meta Event (subevent code follows)

HCI ACL Data Packet:
┌────────────┬──────────────────────┬─────────────────────┐
│  Indicator │ Handle|PB|BC         │  Data Length │ Data  │
│   (1 byte) │ (2 bytes)            │  (2 bytes)   │       │
│   0x02     │ 12-bit handle + flags│              │       │
└────────────┴──────────────────────┴──────────────┴───────┘
PB (Packet Boundary): 00=continuation, 01=first, 10=first auto-flush
BC (Broadcast): 00=point-to-point, 01=active slave broadcast
```

---

## 20. LE AUDIO — CIS/BIS ARCHITECTURE

```
Connected Isochronous Stream (CIS) — Point-to-Point:
┌──────────┐                                    ┌──────────┐
│  Phone   │ ══CIS #1 (Left ear, LC3 80kbps)══▶│ Left Bud │
│ (Central)│                                    └──────────┘
│          │ ══CIS #2 (Right ear, LC3 80kbps)═▶┌──────────┐
│          │                                    │Right Bud │
└──────────┘                                    └──────────┘
  CIG (Connected Isochronous Group) ties CIS #1 and #2 together
  → Synchronized timing for stereo

Broadcast Isochronous Stream (BIS) — One-to-Many (Auracast):
                    ┌──────────┐
                    │  Source  │
                    │ (Airport)│
                    └────┬─────┘
                         │
            ══ BIS #1 (English, LC3) ══
            ══ BIS #2 (Spanish, LC3) ══
                         │
         ┌───────────────┼───────────────┐
         │               │               │
    ┌────▼────┐    ┌────▼────┐    ┌────▼────┐
    │Receiver1│    │Receiver2│    │Receiver3│
    │(English)│    │(Spanish)│    │(English)│
    └─────────┘    └─────────┘    └─────────┘
    
  BIG (Broadcast Isochronous Group): Contains BIS #1 + #2
  Each receiver selects which BIS to sync with
  Encrypted: Broadcast Code shared via QR/NFC
```

---

END OF DOCUMENT 02 — DIAGRAMS & VISUAL REFERENCES
