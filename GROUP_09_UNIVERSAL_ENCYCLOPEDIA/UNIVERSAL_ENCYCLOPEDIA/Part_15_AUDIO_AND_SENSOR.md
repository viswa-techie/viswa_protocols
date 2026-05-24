# ╔══════════════════════════════════════════════════════════════════════════════╗
# ║  UNIVERSAL PROTOCOL ENCYCLOPEDIA                                            ║
# ║  Part 15: AUDIO & SENSOR INTERFACE PROTOCOLS                                ║
# ║  I2S, SoundWire, TDM, A2B, PMBus, SVID, SensorHub                        ║
# ╚══════════════════════════════════════════════════════════════════════════════╝

---

## CATEGORY OVERVIEW

Audio and sensor interface protocols connect DACs, ADCs, codecs, microphones, speakers, power regulators, and sensor hubs to processors. While individually low-bandwidth, they are essential for every device — from smartphones (multiple mics, speakers) to vehicles (ANC, hands-free, chimes) to data centers (power management).

---

## PROTOCOL FAMILY TABLE

| Protocol | Type | Speed | Wires | Purpose | Domain |
|----------|------|-------|-------|---------|--------|
| I2S | Audio | 12.288 MHz typical | 3 (SCK, WS, SD) | PCM audio data | Universal |
| TDM | Audio | Multi-channel I2S | 3+ | Multi-channel audio | Pro/Auto |
| SoundWire | Audio | 12.288 MHz (2-lane) | 1-2 data + clock | Codec interconnect | Mobile |
| A2B (Analog Devices) | Audio/control | 50 Mbps | 1 UTP pair | Automotive audio network | Automotive |
| SPDIF/AES3 | Digital audio | 3.072 Mbps | 1 (coax/optical) | Consumer/pro audio | Consumer |
| HDMI ARC/eARC | Audio return | 1-37 Mbps | HDMI cable | TV↔soundbar | Consumer |
| PMBus | Power mgmt | 400 kHz (I2C) | 2 (SDA, SCL) | Voltage regulator control | Server/embedded |
| SVID | Power mgmt | ~1 MHz | 2-3 | Intel CPU VR control | Intel platforms |
| AVS Bus | Power mgmt | Serial | 2 | AMD CPU VR control | AMD platforms |
| SensorHub | Sensor fusion | I2C/SPI | 2-4 | Low-power sensor aggregation | Mobile/IoT |

---

## I2S (Inter-IC Sound)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Inter-IC Sound |
| Standard | Philips specification (1986), no formal ISO |
| Year | 1986 |
| Creator | Philips Semiconductor |
| Wires | SCK (bit clock), WS (word select/LRCK), SD (serial data) |
| Speed | Bit clock = sample rate × bits × channels (e.g., 48kHz × 32 × 2 = 3.072 MHz) |
| Topology | Master → Slave(s) |
| Data | PCM audio (uncompressed) |
| Use | SoC ↔ codec, DAC, ADC, DSP |

### I2S Signal Timing
```
SCK:  ─┐┌─┐┌─┐┌─┐┌─┐┌─┐┌─┐┌─┐┌─┐┌─┐┌─  (bit clock)
        └┘ └┘ └┘ └┘ └┘ └┘ └┘ └┘ └┘ └┘
WS:   ────────────────┐                ┌──  (word select: 0=Left, 1=Right)
                      └────────────────┘
SD:   ─X──D31─D30─D29─...─D0──X──D31─D30  (MSB first, left then right)
```

### I2S Key Points
- **PCM**: Uncompressed audio (lossless within sample depth)
- **Word Select (WS)**: Indicates left/right channel (frame sync)
- **MSB first**: Highest bit transmitted first
- **Variants**: Left-justified, right-justified, DSP mode
- **No addressing**: Point-to-point (or multi-drop with routing)

### I2S in Linux (ALSA/ASoC)
- `sound/soc/` — ASoC (ALSA System on Chip) framework
- Platform driver: DMA + I2S peripheral (e.g., `sound/soc/qcom/`)
- Codec driver: DAC/ADC/codec chip (e.g., `sound/soc/codecs/`)
- Machine driver: Connects platform + codec + routing
- DT: `dai-link` nodes define I2S connections

---

## TDM (Time Division Multiplexing Audio)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Time Division Multiplexed audio |
| Basis | Extension of I2S for multiple channels |
| Channels | 2-32+ channels per frame |
| Use | Multi-mic arrays, surround sound, automotive (ANC) |
| Frame | Each WS period contains N time slots (channels) |

### TDM Example (8 channels)
```
WS Period (1 frame):
┌──────┬──────┬──────┬──────┬──────┬──────┬──────┬──────┐
│ Ch 0 │ Ch 1 │ Ch 2 │ Ch 3 │ Ch 4 │ Ch 5 │ Ch 6 │ Ch 7 │
│(L mic)│(R mic)│(rear)│(ANC) │(chime)│(nav) │(call)│(media)│
└──────┴──────┴──────┴──────┴──────┴──────┴──────┴──────┘
```

### Automotive TDM Use (AAOS)
- 8-16 channel TDM carrying: microphones, speakers, chimes, ANC, hands-free
- SA8295P audio subsystem: multiple TDM ports to external audio DSP/codecs
- Mixing done in software (AudioFlinger/AudioPolicyService) or hardware DSP

---

## SoundWire (MIPI)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | MIPI SoundWire |
| Standard | MIPI Alliance SoundWire specification |
| Year | 2014 |
| Creator | MIPI Alliance |
| Wires | 1 clock + 1-2 data (or combined clock+data) |
| Speed | 12.288 MHz (single data pin) |
| Topology | Bus (multi-drop, up to 11 slaves) |
| Features | Multi-channel, low-latency, clock synchronization, power |
| Use | Smartphone codecs, mics, speakers (replacing I2S in mobile) |

### SoundWire vs I2S
| Feature | I2S/TDM | SoundWire |
|---------|---------|-----------|
| Wires | 3+ per link | 2 (clock + data) |
| Multi-device | Separate links or TDM | Bus (multi-drop) |
| Channel allocation | Fixed | Dynamic (runtime) |
| Control | Separate I2C for config | In-band (no extra bus) |
| Pin count | High (many links) | Low (2 wires, many devices) |
| Power | Separate | Integrated control |

### SoundWire in Linux
- `drivers/soundwire/` — SoundWire bus subsystem
- `sound/soc/codecs/` — SoundWire codec drivers (e.g., WCD938x for Qualcomm)
- Used in Qualcomm platforms: SoC → WCD codec → speakers/mics via SoundWire

---

## A2B (Automotive Audio Bus)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Automotive Audio Bus |
| Creator | Analog Devices |
| Year | 2014 |
| Speed | 50 Mbps |
| Wires | Single unshielded twisted pair (UTP) |
| Distance | Up to 15m per hop, daisy-chained |
| Features | Audio + control + power over single cable |
| Channels | Up to 32 audio channels (TDM slots) |
| Use | Automotive: microphones, speakers, ANC sensors |
| Advantage | 75% weight reduction vs traditional audio wiring |

### A2B Architecture
```
Master (SoC/DSP)
    │ UTP (15m)
    ▼
┌────────┐  UTP  ┌────────┐  UTP  ┌────────┐
│Slave 0 │──────►│Slave 1 │──────►│Slave 2 │
│(Amp)   │       │(Mic)   │       │(Speaker)│
└────────┘       └────────┘       └────────┘
    Daisy-chain up to 10 nodes
    Audio + I2C/GPIO + Power all over 1 UTP
```

### A2B vs Traditional Audio Wiring
| Feature | Traditional (discrete) | A2B |
|---------|----------------------|-----|
| Wires per speaker | 2 (analog) + control | 1 UTP (shared) |
| 8-speaker system | 16+ wires | 1 UTP daisy-chain |
| Weight | Heavy (copper) | 75% lighter |
| Noise | Susceptible (analog) | Digital (immune) |
| Flexibility | Hardwired | Software-configurable |

---

## PMBus (Power Management Bus)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Power Management Bus |
| Standard | PMBus specification (SMIF/PMBus group) |
| Basis | I2C / SMBus extension |
| Year | 2005 |
| Speed | 100-400 kHz (I2C compatible) |
| Purpose | Monitor and control voltage regulators, power supplies |
| Commands | Standardized (voltage set, current read, temperature, status) |

### PMBus Key Commands
| Command | Code | Purpose |
|---------|------|---------|
| VOUT_COMMAND | 0x21 | Set output voltage |
| IOUT_OC_FAULT_LIMIT | 0x46 | Overcurrent threshold |
| READ_VIN | 0x88 | Read input voltage |
| READ_VOUT | 0x8B | Read output voltage |
| READ_IOUT | 0x8C | Read output current |
| READ_TEMPERATURE | 0x8D | Read temperature |
| STATUS_WORD | 0x79 | Overall status |

### PMBus/SVID in Server/SoC Power
```
┌──────────────┐    PMBus/I2C    ┌──────────────────────┐
│   BMC/PMIC   │◄──────────────►│  Voltage Regulator    │
│   Controller │                 │  (VRM for CPU core)   │
│              │   SVID (Intel)  │                       │
│   CPU ──────────────────────►│  Set VID = 0.85V      │
└──────────────┘                 └──────────────────────┘
```

---

## SVID (Serial Voltage Identification)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Serial VID (Intel) |
| Purpose | CPU tells voltage regulator what voltage it needs |
| Speed | ~1 MHz serial |
| Wires | SVID_CLK, SVID_DATA, SVID_ALERT |
| Protocol | Command/response (master=CPU, slave=VR) |
| Use | Intel CPU power management (DVFS) |

### SVID Operation
- CPU enters different power states (P-states, C-states)
- CPU sends VID command to VRM: "set voltage to 0.85V"
- VRM adjusts output within microseconds
- Enables Dynamic Voltage and Frequency Scaling (DVFS)

---

## FLASH CARDS (12)

| # | Front | Back |
|---|-------|------|
| 1 | I2S wires? | SCK (bit clock), WS (word select), SD (serial data) |
| 2 | I2S data format? | PCM, MSB first, left-justified by default |
| 3 | TDM purpose? | Multi-channel audio over I2S-like interface (N time slots/frame) |
| 4 | SoundWire advantage? | 2 wires for multi-device bus (replaces multiple I2S links) |
| 5 | A2B advantage? | Audio + control + power over single UTP (75% weight saving) |
| 6 | A2B max hops? | Up to 10 daisy-chained nodes |
| 7 | PMBus basis? | I2C/SMBus extension for power management |
| 8 | PMBus READ_VOUT? | Command 0x8B — read output voltage of regulator |
| 9 | SVID purpose? | CPU tells VRM what voltage to set (DVFS) |
| 10 | Linux audio framework? | ASoC (ALSA System on Chip): platform + codec + machine drivers |
| 11 | SoundWire in Qualcomm? | SoC → WCD codec (WCD938x) via SoundWire |
| 12 | I2S sample rate formula? | BitClock = SampleRate × BitsPerSample × NumChannels |

---

## INTERVIEW QUESTIONS (3)

**Q1: Explain the ASoC (ALSA System on Chip) architecture and I2S's role.**
A: ASoC separates audio into three components: (1) Platform driver: handles DMA and I2S/TDM peripheral hardware (moves PCM data between memory and I2S TX/RX FIFOs). (2) Codec driver: controls the external codec/DAC/ADC chip (register configuration over I2C/SPI, routing, volume). (3) Machine driver: connects them — defines which platform port links to which codec, sets DAI format (I2S/TDM), clocking. I2S carries the actual audio samples between SoC and codec. This separation allows reuse: same codec driver works across different SoCs.

**Q2: Why is A2B significant for automotive audio architecture?**
A: Traditional: each microphone/speaker needs dedicated analog wires (2 per channel) → 16+ wires for 8 speakers, heavy harness, susceptible to EMI. A2B: single UTP cable daisy-chains up to 10 nodes (microphones, amplifiers, speakers). Carries 32 TDM audio channels + I2C control + power over ONE wire pair. Benefits: 75% weight reduction, digital transport (noise-immune), software-reconfigurable routing, simpler harness manufacturing. Critical for modern vehicles with 20+ speakers, ANC, hands-free, and in-cabin communication systems.

**Q3: How does SVID enable CPU power efficiency?**
A: When CPU workload decreases, it can lower frequency (save dynamic power ∝ f) and voltage (save power ∝ V²). SVID provides the fast signaling path for the CPU to command its voltage regulator in real-time (~µs response). Flow: CPU determines optimal V/F point → sends VID command over SVID serial bus → VRM adjusts output voltage within µs → CPU changes frequency. Without fast VID, voltage changes would be too slow for aggressive DVFS, losing efficiency. SVID enables fine-grained per-microsecond voltage adaptation.

---

END OF PART 15 — AUDIO & SENSOR INTERFACE PROTOCOLS
