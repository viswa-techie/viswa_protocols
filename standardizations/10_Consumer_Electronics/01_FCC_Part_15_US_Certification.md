# FCC Part 15 — US Radio Frequency Device Certification

**Topic:** FCC CFR 47 Part 15 — Unintentional & Intentional Radiators, SDoC, Certification  
**Standards:** FCC CFR 47 Part 15 (Subparts A-F), FCC OET Bulletins, ANSI C63.4, ANSI C63.10  
**SDO:** Federal Communications Commission (FCC), Office of Engineering & Technology (OET)  
**Audience:** RF compliance engineers, EMC test engineers, product certification managers, hardware designers  
**Prerequisites:** Basic RF/EMC theory, antenna fundamentals, spectrum concepts

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event |
|------|-------|
| 1934 | FCC established by Communications Act |
| 1938 | First radio interference rules |
| 1968 | FCC Part 15 formalized (rules for RF devices) |
| 1979 | Part 15 devices: personal computers generate interference |
| 1985 | FCC Part 15.109 Class A/B radiated emission limits codified |
| 1989 | Part 15.247 — 2.4 GHz ISM band spread spectrum rules |
| 1997 | Part 15.407 — U-NII (5 GHz) rules for Wi-Fi |
| 2002 | UWB rules (Part 15 Subpart F) |
| 2014 | FCC 14-30 — expanded 5 GHz (U-NII-2/3) |
| 2017 | SDoC replaces Verification and Declaration of Conformity |
| 2020 | 6 GHz unlicensed (FCC 20-51): 5.925-7.125 GHz for Wi-Fi 6E |
| 2022 | AFC (Automated Frequency Coordination) for 6 GHz standard power |
| 2024 | Further 6 GHz rules (outdoor AP, higher power considerations) |

### 1.2 FCC Part 15 Scope

| Section | What It Covers |
|---------|---------------|
| 15.1 | Scope — devices that can radiate RF energy (intentionally or not) |
| 15.5 | General conditions — no harmful interference; must accept interference |
| 15.15 | Equipment authorization requirements |
| 15.19 | Labeling requirements (FCC ID format) |
| 15.21 | User information requirements (manual statements) |
| 15.101-15.109 | Subpart B: Unintentional radiators (emissions limits) |
| 15.201-15.255 | Subpart C: Intentional radiators (license-free transmitters) |
| 15.301-15.323 | Subpart D: Unlicensed PCS |
| 15.401-15.407 | Subpart E: U-NII devices (5 GHz / 6 GHz) |
| 15.501-15.525 | Subpart F: Ultra-Wideband (UWB) |

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 FCC Equipment Authorization Types

```mermaid
graph TB
    subgraph "FCC Equipment Authorization (post-2017)"
        SDOC[Supplier's Declaration<br/>of Conformity (SDoC)]
        CERT[Certification<br/>(via TCB)]
    end
    
    subgraph "Product Types"
        UNINT[Unintentional Radiators<br/>PCs, monitors, peripherals<br/>Subpart B]
        INT[Intentional Radiators<br/>Wi-Fi, BT, ZigBee, LoRa<br/>Subpart C/D/E/F]
    end
    
    UNINT --> SDOC
    INT --> CERT
    
    subgraph "Process Flow"
        TEST[Testing at<br/>Accredited Lab]
        FILE[Filing<br/>(via TCB)]
        FCCID[FCC ID Granted<br/>(public database)]
    end
    
    CERT --> TEST
    TEST --> FILE
    FILE --> FCCID
```

### 2.2 FCC ID Format

```
Format: GRANTEE_CODE + PRODUCT_CODE
Example: A3LSMG998B
         ^^^ = Grantee (Samsung)
            ^^^^^^^ = Product (Galaxy S21)

FCC ID rules:
- Grantee code: 3-5 characters (assigned by FCC)
- Product code: up to 14 characters (manufacturer's choice)
- Total: maximum 19 characters
- Must be displayed on product (or e-label for small devices)
```

---

## Chapter 3 — Technical Deep Dive

### 3.1 Subpart B — Unintentional Radiators

**Radiated Emission Limits (FCC 15.109):**

| Frequency (MHz) | Class A (10m) dBμV/m | Class B (3m) dBμV/m |
|-----------------|----------------------|---------------------|
| 30-88 | 39.1 | 40.0 |
| 88-216 | 43.5 | 43.5 |
| 216-960 | 46.4 | 46.0 |
| >960 | 49.5 | 54.0 |

**Conducted Emission Limits (FCC 15.107):**

| Frequency (MHz) | Class A QP (dBμV) | Class A Avg (dBμV) | Class B QP (dBμV) | Class B Avg (dBμV) |
|-----------------|--------------------|--------------------|--------------------|--------------------|
| 0.15-0.5 | 79 | 66 | 66-56 | 56-46 |
| 0.5-5 | 73 | 60 | 56 | 46 |
| 5-30 | 73 | 60 | 60 | 50 |

### 3.2 Subpart C — Intentional Radiators (2.4 GHz ISM)

**FCC 15.247 — Spread Spectrum & Digital Modulation (2400-2483.5 MHz):**

| Parameter | Requirement |
|-----------|------------|
| Max conducted power | 1 W (30 dBm) for FHSS; 1 W for DSS with processing gain ≥10 dB |
| Max EIRP | 4 W (36 dBm) for point-to-multipoint with directional antenna |
| Bandwidth | ≥500 kHz (FHSS: 75 hopping channels, 400ms max dwell) |
| Spurious emissions | 20 dB below fundamental (or Part 15.209 limits, whichever less restrictive) |
| Antenna | Fixed antenna required (or gain reduction formula for higher gain) |
| Power spectral density | ≤8 dBm in any 3 kHz bandwidth (FHSS); specific rules for DSS |

### 3.3 Subpart E — U-NII Devices (5 GHz + 6 GHz)

**FCC 15.407 — U-NII Band Rules:**

| Band | Frequency | Max Power (EIRP) | Use |
|------|-----------|-------------------|-----|
| U-NII-1 | 5.15-5.25 GHz | 1 W (indoor) / 4W (outdoor with AFC) | Indoor/outdoor |
| U-NII-2A | 5.25-5.35 GHz | 1 W | Indoor + DFS required |
| U-NII-2C | 5.47-5.725 GHz | 1 W | Indoor + DFS required |
| U-NII-3 | 5.725-5.85 GHz | 4 W | Indoor/outdoor |
| U-NII-5 (LPI) | 5.925-6.425 GHz | 30 mW/MHz (indoor) | Indoor low-power |
| U-NII-5 (SP) | 5.925-6.425 GHz | 36 dBm EIRP (outdoor) | AFC required (standard power) |
| U-NII-6 (LPI) | 6.425-6.525 GHz | 30 mW/MHz (indoor) | Indoor only |
| U-NII-7 (LPI) | 6.525-6.875 GHz | 30 mW/MHz (indoor) | Indoor low-power |
| U-NII-8 (LPI) | 6.875-7.125 GHz | 30 mW/MHz (indoor) | Indoor only |

**DFS (Dynamic Frequency Selection) Requirements:**

| Parameter | Requirement |
|-----------|------------|
| Purpose | Detect and avoid radar signals in 5.25-5.35 + 5.47-5.725 GHz |
| Detection threshold | -62 dBm (for 1μs radar pulse) |
| Channel availability check | 60 seconds monitoring before use |
| Channel move time | 10 seconds after radar detected |
| Non-occupancy period | 30 minutes (cannot reuse channel for 30 min) |
| Testing | Per KDB 905462 (DFS test procedures) |

### 3.4 Measurement Standards

| Standard | Scope |
|----------|-------|
| ANSI C63.4 | Measurement of radio-noise emissions (unintentional radiators) |
| ANSI C63.10 | Measurement of intentional radiators (licensed-exempt transmitters) |
| ANSI C63.26 | Compliance testing of transmitters using measurement techniques |
| KDB 558074 | Equipment authorization guidance (General RF exposure) |
| KDB 447498 | RF exposure procedures for mobile/portable devices |
| KDB 905462 | DFS (Dynamic Frequency Selection) test procedures |

---

## Chapter 4 — Implementation Guide

### 4.1 FCC Certification Process (Intentional Radiator)

| Step | Duration | Activity |
|------|----------|----------|
| 1 | 1-2 weeks | Select accredited test lab (FCC-recognized TCB or A2LA lab) |
| 2 | 1-2 weeks | Prepare test samples (3-5 production-representative units) |
| 3 | 2-4 weeks | RF testing per ANSI C63.10 (power, spurious, bandwidth, DFS if needed) |
| 4 | 1-2 weeks | EMC testing per ANSI C63.4 (unintentional emissions if host device) |
| 5 | 1 week | Prepare FCC filing package (test report, photos, schematics, user manual) |
| 6 | 2-4 weeks | TCB review and grant (FCC ID issued) |
| 7 | 1 day | FCC ID appears in public OET database (transition.fcc.gov) |

### 4.2 Filing Documentation Requirements

| Document | Content |
|----------|---------|
| FCC Form 731 | Application form (applicant info, product description) |
| Test Report | Full measurement data per applicable standard |
| External photos | Clear photos of product exterior (all sides) |
| Internal photos | PCB photos (top/bottom), shielding, antenna location |
| Schematics | Block diagram + schematic (RF section at minimum) |
| Operational description | How the device works (modulation, frequency, power control) |
| User manual | Include FCC compliance statements (15.19, 15.21, 15.105) |
| Label/location | FCC ID label mockup + label location on product |
| Confidentiality request | Short-term (permanent for schematics/internal photos) |

### 4.3 Required FCC Statements in User Manual

```
FCC 15.19(a)(3):
"This device complies with Part 15 of the FCC Rules. Operation is subject to 
the following two conditions: (1) This device may not cause harmful interference, 
and (2) this device must accept any interference received, including interference 
that may cause undesired operation."

FCC 15.21:
"Changes or modifications not expressly approved by the party responsible for 
compliance could void the user's authority to operate the equipment."

FCC 15.105(b) (Class B):
"This equipment has been tested and found to comply with the limits for a Class B 
digital device, pursuant to Part 15 of the FCC Rules. These limits are designed to 
provide reasonable protection against harmful interference in a residential 
installation."
```

---

## Chapter 5 — Certification & Compliance

### 5.1 TCB (Telecommunication Certification Body) System

| Aspect | Detail |
|--------|--------|
| What | FCC-designated organizations that review applications and grant equipment authorizations |
| Examples | UL (Demko), TÜV Rheinland, Bureau Veritas, Intertek, SGS |
| Scope | Each TCB has defined scope (frequency bands, equipment types) |
| Process | Manufacturer submits filing → TCB reviews → TCB grants FCC ID on behalf of FCC |
| Timeline | 2-4 weeks typical (TCB review) vs. 6-12 weeks (direct FCC review — rare now) |
| Cost | $1,000-$5,000 per filing (TCB review fee) |

### 5.2 Permissive Changes vs. New Application

| Change Type | Treatment |
|-------------|-----------|
| New model (different RF module) | New FCC ID (full certification) |
| Higher power output | Class II Permissive Change (testing + filing) |
| New antenna (same type, different gain) | Class II Permissive Change |
| Different antenna type | May require new certification (consult TCB) |
| Cosmetic change only | No action required |
| Firmware update (same RF parameters) | No action required |
| New frequency band added | New certification required |
| Host device change (pre-certified module) | Module integration procedures (KDB 996369) |

### 5.3 Module Certification (FCC)

| Aspect | Detail |
|--------|--------|
| Limited Modular Approval | Module certified with specific conditions (antenna, host requirements) |
| Full Modular Approval | Module can be used in any host without additional testing |
| Conditions (8 criteria) | Shielding, buffered modulation, power, antenna connector, labeling, etc. |
| Host integration | Must follow module manufacturer's integration guide |
| Co-location | Multiple transmitters → SAR/RF exposure assessment required |
| KDB 996369 | Module integration procedures (detailed FCC guidance) |

---

## Chapter 6 — Regional Comparison (US vs. Others)

| Aspect | FCC (US) | IC (Canada) | CE (EU) | MIC (Japan) |
|--------|---------|-------------|---------|-------------|
| Authority | Federal Communications Commission | Innovation, Science and Economic Development (ISED) | Notified Bodies (self-declaration) | Ministry of Internal Affairs and Communications |
| Mark | FCC ID: XXXYYYYY | IC: XXXXXXXXX-YYYYY | CE (no unique number) | 技適マーク (Giteki mark) + number |
| Process | Certification via TCB | Certification (mirrors FCC) | Self-declaration (DoC) or Notified Body | Certification via registered body |
| Mutual recognition | US-Canada MRA (same test report accepted) | US-Canada MRA | Not with US/Canada directly | Limited (CB scheme for safety) |
| Test standards | ANSI C63.4/C63.10 | RSS-Gen, RSS-247, RSS-248 | EN 300 328, EN 301 893 | ARIB STD-T66/T71 |
| 6 GHz | 5.925-7.125 GHz (1200 MHz) | 5.925-7.125 GHz | 5945-6425 MHz (480 MHz only) | 5925-6425 MHz |
| DFS required (5 GHz) | Yes (U-NII-2A/2C) | Yes (same as FCC) | Yes (5.25-5.35, 5.47-5.725) | Yes |

---

## Chapter 7 — Comparison of FCC Authorization Procedures

| Dimension | SDoC | Certification |
|-----------|------|---------------|
| Applies to | Unintentional radiators (Subpart B) | Intentional radiators (Subpart C/D/E/F) |
| FCC involvement | None (self-declaration) | Via TCB (FCC-designated) |
| FCC ID assigned | No | Yes (public database) |
| Testing required | Yes (must retain records) | Yes (report submitted to TCB) |
| Documentation | Kept on file by manufacturer | Submitted to FCC/TCB |
| Market surveillance | Random FCC audits possible | Grant can be revoked if non-compliant |
| Cost | Lower (no filing fee) | Higher (TCB review fee + filing) |
| Label requirement | "Contains FCC ID: ..." if using module; or compliance statement | FCC ID must appear on product (or e-label) |
| Typical products | Desktop PCs, monitors, USB drives | Wi-Fi routers, Bluetooth devices, IoT |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 FCC Part 15 Decision Tree

```mermaid
graph TB
    START[Does your device<br/>emit RF energy?]
    START -->|"Yes"| Q1[Does it intentionally<br/>transmit RF?]
    START -->|"No"| EXEMPT[May be exempt<br/>from Part 15]
    
    Q1 -->|"Yes"| INT[INTENTIONAL RADIATOR<br/>Subpart C/D/E/F]
    Q1 -->|"No"| UNINT[UNINTENTIONAL RADIATOR<br/>Subpart B]
    
    INT --> Q2[Which frequency band?]
    Q2 -->|"2.4 GHz ISM"| SP15247[FCC 15.247<br/>Spread spectrum rules]
    Q2 -->|"5/6 GHz U-NII"| SP15407[FCC 15.407<br/>U-NII rules + DFS]
    Q2 -->|"Sub-1 GHz"| SP15249[FCC 15.249<br/>Low-power radiators]
    Q2 -->|"UWB"| SP15F[Subpart F<br/>UWB rules]
    
    SP15247 --> CERT_REQ[CERTIFICATION<br/>Required (via TCB)]
    SP15407 --> CERT_REQ
    SP15249 --> CERT_REQ
    SP15F --> CERT_REQ
    
    UNINT --> SDOC_REQ[SDoC<br/>Self-declaration]
```

### 8.2 FCC Measurement Setup (Radiated Emissions)

```mermaid
graph TB
    subgraph "Semi-Anechoic Chamber"
        EUT[EUT on turntable<br/>(80cm height)]
        ANT[Broadband antenna<br/>(biconical + log-periodic)]
        GP[Ground plane<br/>(reflective floor)]
        TURN[360° turntable<br/>rotation]
    end
    
    subgraph "Measurement System"
        PREAMP[Pre-amplifier<br/>(if needed)]
        RX[EMI Receiver<br/>or Spectrum Analyzer]
        SW[Antenna switching<br/>(H/V polarization)]
        MAST[Antenna mast<br/>(height scan 1-4m)]
        PC[Control PC<br/>+ automation software]
    end
    
    EUT -->|"Radiated emission"| ANT
    ANT --> PREAMP --> RX
    RX --> PC
    PC --> TURN
    PC --> MAST
    PC --> SW
```

---

## Chapter 9 — Case Studies

### 9.1 Wi-Fi 6E Router — First 6 GHz Certification

| Aspect | Detail |
|--------|--------|
| Product | Tri-band Wi-Fi 6E router (2.4 + 5 + 6 GHz) |
| Challenge | First product certified under new 6 GHz rules (FCC 20-51) |
| AFC requirement | Standard-power outdoor: must implement AFC client |
| LPI (Low Power Indoor) | Indoor-only mode: simpler (no AFC needed) |
| Testing | ANSI C63.10 + KDB guidance for 6 GHz (new procedures) |
| DFS | NOT required in 6 GHz (only in 5.25-5.35 and 5.47-5.725 GHz) |
| Power validation | 30 mW/MHz PSD limit across 160/320 MHz channel widths |
| Client Indi | AFC registration and database queries tested per FCC requirements |
| Timeline | 8 weeks (new procedures → lab/TCB learning curve) |
| Lesson | Engage TCB early for novel technology certifications |

### 9.2 IoT Sensor Module — Modular Certification Strategy

| Aspect | Detail |
|--------|--------|
| Product | BLE + 802.15.4 (Thread/Zigbee) combo module |
| Strategy | Obtain Limited Modular Approval → sell to multiple host manufacturers |
| Advantage | Host device makers don't need RF certification (major cost saving) |
| Requirements | Meet all 8 modular criteria (KDB 996369) |
| Antenna | U.FL connector (removable) — easier for host integration |
| Conditions | Host must provide ground plane ≥50×50mm; antenna clearance ≥10mm |
| Certification cost | $12,000 (module certification) vs. $8,000 × N hosts avoided |
| Grantee | Module manufacturer holds FCC ID; hosts reference it |

---

## Chapter 10 — Future Evolution & Industry Trends

| Trend | Timeline | Description |
|-------|----------|-------------|
| Wi-Fi 7 (802.11be) | 2024-2025 | 320 MHz channels, 4096-QAM, Multi-Link Operation |
| AFC maturity | 2024+ | Automated Frequency Coordination databases operational |
| E-labeling expansion | Now | Electronic display of FCC ID (small devices, wearables) |
| 6 GHz outdoor expansion | 2025+ | Standard power outdoor AP (with AFC) expanding |
| Software-Defined Radio rules | Emerging | FCC considering SDR-specific certification updates |
| AI/ML in RF | Research | Cognitive radio, dynamic spectrum sharing |
| Spectrum sharing (CBRS model) | Growing | More shared spectrum bands (3.5 GHz model expands) |
| FCC cyber security rules | 2025+ | IoT labeling (US Cyber Trust Mark) |
| Part 15 modernization | Ongoing | Updated test procedures, measurement uncertainty |
| Millimeter wave (60 GHz+) | Growing | WiGig, automotive radar, updated limits |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is the difference between FCC Class A and Class B digital devices? When does each apply?  
**A:** **Class A (commercial/industrial):** Limits are LESS stringent (devices cause less interference concern because only used in commercial environments). Tested at 10m distance (US) or 30m equivalent. Required FCC statement: warns that device may cause interference in residential environments; user must accept interference. Typical products: industrial test equipment, servers (data center), enterprise networking. **Class B (residential):** Limits are MORE stringent (must protect home radio/TV reception from interference). Tested at 3m distance with lower dBμV/m limits. Required FCC statement: informational about interference resolution. Typical products: laptops, monitors, printers, consumer electronics, USB peripherals, home routers. **Key difference:** A device marketed to consumers or that may end up in a residential environment MUST be Class B. Even if sold as "commercial," if it could reasonably be used at home → Class B. If genuinely ONLY for industrial/commercial → Class A is acceptable. **Practical note:** Most consumer products today certify as Class B because: (1) retailers often require it, (2) it covers both markets, (3) the cost difference in design/testing is minimal.

### Tier 2: Mid-Level

**Q2:** Explain the FCC DFS requirements for 5 GHz U-NII devices. What happens during testing?  
**A:** **DFS (Dynamic Frequency Selection) — Purpose:** Protect radar systems (weather radar, military, aviation) operating in 5.25-5.35 GHz (U-NII-2A) and 5.47-5.725 GHz (U-NII-2C). The unlicensed Wi-Fi device must detect radar and vacate the channel. **Requirements (FCC 15.407):** (1) **Channel Availability Check (CAC):** Before using any DFS channel, device must monitor for 60 seconds. If radar detected during CAC → cannot use that channel. (2) **In-Service Monitoring (ISM):** While transmitting, device continuously monitors for radar. If radar pulse detected → must vacate within 10 seconds. (3) **Channel Move Time:** Maximum 10 seconds to cease transmission + 200ms of aggregate transmission during move. (4) **Non-Occupancy Period (NOP):** After detecting radar on a channel, cannot reuse that channel for 30 minutes. (5) **Detection threshold:** Must detect radar at -62 dBm (for pulse width 1μs). Different thresholds for different pulse widths and radar types. **DFS Testing (per KDB 905462):** (a) **Test signals:** Multiple radar waveforms simulated: Short pulse (1μs, PRF 700-1000). Long pulse (various widths up to 20μs). Staggered PRI. Chirp radar. Frequency hopping radar. (b) **Test procedure:** Inject radar signal into device's receiver (via conducted or radiated). Verify detection probability: ≥60% detection rate per trial. Measure channel move time (must be ≤10 seconds). Verify 30-minute non-occupancy. Test across all DFS channels. (c) **Pass/fail:** Device must detect radar ≥60% of trials for each waveform type. Must vacate within 10 seconds for 100% of detected events. Must not reuse channel within 30-minute NOP. **Implementation impact:** DFS adds significant software complexity (radar detection algorithm). Adds 60-second delay when changing to DFS channel (poor user experience). Many consumer products avoid DFS channels (use U-NII-1 and U-NII-3 only). Enterprise APs must support DFS (more available channels = less congestion). **Note:** 6 GHz band does NOT require DFS (different incumbent protection: AFC system instead).

### Tier 3: Senior

**Q3:** Your company is developing a multi-radio IoT gateway with Wi-Fi 6E, BLE, Thread, and LTE Cat-M. Describe the complete FCC certification strategy.  
**A:** **Product definition:** Wi-Fi 6E (2.4 + 5 + 6 GHz), BLE 5.3 (2.4 GHz), 802.15.4 Thread (2.4 GHz), LTE Cat-M1 (licensed bands — Parts 22/24/27). Four co-located transmitters in one device. **Certification strategy:** **1. Regulatory scope per radio:** Wi-Fi 2.4/5/6 GHz: FCC Part 15.247 (2.4 GHz) + 15.407 (5/6 GHz) → Certification. BLE 5.3: FCC Part 15.247 → Certification. Thread (802.15.4): FCC Part 15.247 → Certification. LTE Cat-M1: FCC Part 22/24/27 → Certification (licensed transmitter). **2. Module vs. device-level strategy:** Option A (preferred): Use pre-certified Wi-Fi 6E + BLE combo module (FCC modular certified). Use pre-certified LTE Cat-M1 module (e.g., Quectel BG95, u-blox SARA). Only Thread radio (802.15.4) = device-level certification (simple, low power). Benefit: significant reduction in testing time and cost. Only need host integration assessment + RF exposure for co-location. Option B: All radios designed-in (no modules). Full certification for each radio → $40K-$60K in testing alone. Only choose if module constraints unacceptable (size, performance, cost at volume). **3. Co-location / simultaneous transmission analysis:** FCC requires assessment of RF exposure when multiple transmitters operate simultaneously. Per KDB 447498 / 248227: Power density summation for all simultaneously-active transmitters. $\sum_{i=1}^{n} \frac{P_i}{S_i} \leq 1.0$ where $P_i$ = power of transmitter $i$ and $S_i$ = SAR/MPE limit for that frequency. For tabletop device (>20cm from body): MPE (Maximum Permissible Exposure) calculation. For portable device (<20cm): full SAR testing required (expensive). **Strategy:** Design as fixed device (installed >20cm from users) → MPE calculation only (no SAR lab needed). This saves $15K-$25K in SAR testing. **4. Filing strategy:** Single FCC ID covering all intentional radiators in the device (compound filing). Alternative: separate FCC IDs per radio (if using modules → each has own FCC ID). Compound filing: one application with multiple test reports per radio. **5. DFS consideration:** 5 GHz (U-NII-2A/2C): DFS required → adds 2-4 weeks testing. 6 GHz: No DFS required (AFC for standard power; LPI exempted from both). Decision: Support LPI (indoor) only for initial launch → no AFC implementation needed. Add standard power (outdoor + AFC) in firmware update later (permissive change filing). **6. Testing timeline:**
```
Week 1-2:  Pre-compliance (verify all radios operational, preliminary measurements)
Week 3-4:  Wi-Fi 6E conducted power, OBW, spurious (15.247 + 15.407)
Week 5-6:  Wi-Fi 6E DFS testing (KDB 905462) — 5 GHz only
Week 5-6:  BLE + Thread testing (15.247) — can parallel with DFS
Week 7:    LTE Cat-M1 testing (Part 22/24/27) — if not using pre-certified module
Week 7-8:  Unintentional emissions (ANSI C63.4) — host device
Week 8:    RF exposure assessment (MPE calculation, or SAR if portable)
Week 9-10: Report preparation + TCB filing
Week 11-13: TCB review + grant
```
**7. Cost breakdown:** Testing (pre-certified modules used): ~$25K. Testing (all device-level): ~$55K. TCB filing fee: ~$3K. Confidentiality fees: ~$500. Total with modules: **~$28K**. Total without modules: **~$58K**. **8. Risk mitigation:** Pre-certified module vendors: validate FCC grant covers your frequency/power needs. Module integration: follow KDB 996369 exactly (antenna specifications, ground plane). LTE bands: confirm Part 22/24/27 (not Part 15 — different rules). Simultaneous transmission: ensure all radios tested in worst-case co-location scenario.

---

## Chapter 12 — Cheat Sheet & Quick Reference

### FCC Part 15 Key Sections

```
15.5:    General condition — "no harmful interference"
15.107:  Conducted emission limits (Subpart B)
15.109:  Radiated emission limits (Subpart B)
15.203:  Antenna requirement (integral antenna unless connector allowed)
15.207:  Conducted emissions (intentional radiators on AC mains)
15.209:  Radiated emission limits (intentional radiators)
15.247:  2.4 GHz operation (spread spectrum / digital modulation)
15.249:  Low-power, license-free devices (various bands)
15.407:  5 GHz / 6 GHz U-NII operation
```

### FCC 2.4 GHz Power Limits (15.247)

```
Point-to-multipoint:
  Max conducted power: 30 dBm (1 W)
  Max EIRP: 36 dBm (4 W) — 6 dBi antenna gain allowed

Point-to-point (fixed):
  Max conducted power: 30 dBm
  EIRP: No limit (higher gain antenna allowed with 1:1 power reduction)

Power reduction formula (antenna > 6 dBi):
  Reduce conducted power by 1 dB for every 3 dB antenna gain > 6 dBi
```

### FCC 6 GHz Power Limits (15.407 — Wi-Fi 6E)

```
Low Power Indoor (LPI):
  Max PSD: 5 dBm/MHz
  Max EIRP: 30 dBm (indoor only, no AFC required)

Standard Power (SP):
  Max PSD: 17 dBm/MHz (AP); 11 dBm/MHz (client)
  Max EIRP: 36 dBm (AP); 30 dBm (client)
  Requires AFC (Automated Frequency Coordination)

Very Low Power (VLP):
  Max PSD: -8 dBm/MHz
  Max EIRP: 14 dBm (portable, no restrictions on use case)
```

### FCC Filing Checklist

```
□ Test report (per ANSI C63.10 for intentional radiators)
□ External photos (all sides, clear, high resolution)
□ Internal photos (PCB top/bottom, RF section, antenna, shielding)
□ Block diagram (signal flow, all ICs, antenna path)
□ Schematic (at minimum RF section; full preferred)
□ Operational description (modulation, channels, power control, DFS)
□ User manual (with FCC statements: 15.19, 15.21, 15.105)
□ Label (FCC ID mockup + label location photo/drawing)
□ RF exposure information (SAR report or MPE calculation)
□ Confidentiality letter (for internal photos, schematics)
```

---

*End of Document — 01_FCC_Part_15_US_Certification.md*
