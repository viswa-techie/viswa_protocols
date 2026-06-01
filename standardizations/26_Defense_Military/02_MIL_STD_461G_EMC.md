# MIL-STD-461G — EMC/EMI Requirements for Defense Equipment

**Category:** 26 — Defense & Military Standards  
**Document:** 02 — MIL-STD-461G EMC  
**Standard:** MIL-STD-461G (11 December 2015), MIL-STD-464C  
**Scope:** Electromagnetic interference (EMI) control and electromagnetic compatibility (EMC)  
**Audience:** EMC test engineers, defense electronics designers, shielding specialists  
**Prerequisites:** Basic electromagnetics, RF fundamentals, shielded room/anechoic chamber concepts

---

## Chapter 1 — Standard Overview

### 1.1 MIL-STD-461G vs. MIL-STD-464C

| Standard | Level | Scope |
|----------|-------|-------|
| **MIL-STD-461G** | Equipment/subsystem | EMI limits and test methods for individual boxes/LRUs |
| **MIL-STD-464C** | System/platform | System-level E3 (electromagnetic environmental effects) |

### 1.2 Test Requirement Categories

| Prefix | Category | Direction | Description |
|--------|----------|-----------|-------------|
| **CE** | Conducted Emissions | Equipment → Power lines | Unwanted signals on power/signal cables |
| **CS** | Conducted Susceptibility | Power lines → Equipment | Equipment must tolerate interference on cables |
| **RE** | Radiated Emissions | Equipment → Free space | Unwanted RF radiation from equipment |
| **RS** | Radiated Susceptibility | Free space → Equipment | Equipment must tolerate external RF fields |

---

## Chapter 2 — Complete Test Requirements Table

### 2.1 All 17 Test Requirements

| Test ID | Title | Frequency Range | Applicability |
|---------|-------|----------------|---------------|
| **CE101** | Conducted Emissions, Power Leads | 30 Hz – 10 kHz | Navy (submarine/ship), aircraft (with LISN) |
| **CE102** | Conducted Emissions, Power Leads | 10 kHz – 10 MHz | ALL platforms |
| **CS101** | Conducted Susceptibility, Power Leads | 30 Hz – 150 kHz | Navy, aircraft |
| **CS103** | Conducted Susceptibility, Antenna Port, Intermodulation | 15 kHz – 10 GHz | Receiver-only |
| **CS104** | Conducted Susceptibility, Antenna Port, Rejection of Undesired Signals | 30 Hz – 20 GHz | Receiver-only |
| **CS105** | Conducted Susceptibility, Antenna Port, Cross-Modulation | 30 Hz – 20 GHz | Receiver-only |
| **CS109** | Conducted Susceptibility, Structure Current | 60 Hz – 100 kHz | All with metallic structure bond |
| **CS114** | Conducted Susceptibility, Bulk Cable Injection | 10 kHz – 200 MHz | ALL platforms |
| **CS115** | Conducted Susceptibility, Bulk Cable Injection, Impulse Excitation | — (time-domain pulse) | ALL platforms |
| **CS116** | Conducted Susceptibility, Damped Sinusoidal Transients | 10 kHz – 100 MHz | ALL platforms |
| **RE101** | Radiated Emissions, Magnetic Field | 30 Hz – 100 kHz | Navy (degaussing), aircraft |
| **RE102** | Radiated Emissions, Electric Field | 10 kHz – 18 GHz | ALL platforms |
| **RE103** | Radiated Emissions, Antenna Spurious and Harmonic Outputs | Transmitter harmonics | Transmitters only |
| **RS101** | Radiated Susceptibility, Magnetic Field | 30 Hz – 100 kHz | Navy, submarine |
| **RS103** | Radiated Susceptibility, Electric Field | 2 MHz – 40 GHz | ALL platforms |
| **RS105** | Radiated Susceptibility, Transient Electromagnetic Field | EMP-like pulse | All (EMP hardening requirement) |

---

## Chapter 3 — Critical Tests Deep Dive

### 3.1 CE102: Conducted Emissions, Power Leads (10 kHz – 10 MHz)

| Parameter | Specification |
|-----------|--------------|
| Frequency range | 10 kHz to 10 MHz |
| Measurement setup | LISN (Line Impedance Stabilization Network), 50 µH/50 Ω |
| Detector | Peak and average (MIL limits are typically peak) |
| Resolution bandwidth | Per CISPR receiver specifications |
| Limit (Navy surface ship) | Varies: ~94 dBµV at 10 kHz → ~60 dBµV at 10 MHz |
| Limit (Ground Army) | ~80 dBµV at 10 kHz → ~50 dBµV at 2 MHz |
| Limit (Aircraft) | ~90 dBµV at 10 kHz → ~50 dBµV at 2 MHz |

### 3.2 RE102: Radiated Emissions (10 kHz – 18 GHz)

| Parameter | Specification |
|-----------|--------------|
| Frequency range | 10 kHz to 18 GHz |
| Below 200 MHz | Rod antenna (41-inch monopole), magnetic loop (for RE101 overlap) |
| 200 MHz – 1 GHz | Biconical/log-periodic antenna |
| Above 1 GHz | Horn antenna |
| Measurement distance | 1 meter from EUT boundary |
| Environment | Shielded room (chamber ambient must be 6 dB below limit) |
| Limit (typical) | 24 dBµV/m at 200 MHz → 44 dBµV/m at 2 GHz (Army/Navy); Aircraft varies |

### 3.3 RS103: Radiated Susceptibility (2 MHz – 40 GHz)

| Parameter | Specification |
|-----------|--------------|
| Frequency range | 2 MHz to 40 GHz (platform dependent) |
| Test field strength (Army ground) | 10 V/m (30 MHz – 2 GHz); varies outside |
| Test field strength (Navy surface) | 10 V/m baseline; up to 200 V/m for sensitive EW equipment |
| Test field strength (Aircraft internal) | 20 V/m (HIRF equivalent for military) |
| Modulation | 1 kHz AM, 80% depth (pulse for radar freq) |
| Dwell time | > 3 seconds per frequency step |
| Susceptibility criteria | No degradation in performance; no malfunction |

### 3.4 CS114: Conducted Susceptibility, Bulk Cable Injection (10 kHz – 200 MHz)

| Parameter | Specification |
|-----------|--------------|
| Frequency range | 10 kHz to 200 MHz |
| Injection method | Bulk current injection (BCI) probe around cable harness |
| Calibration | Into 50 Ω fixture first; then apply to EUT cables |
| Level | Platform-dependent: typically 20-60 mA (varies with frequency) |
| Modulation | 1 kHz AM, 80% depth |
| Cable types tested | All cables (power, signal, data) individually |

---

## Chapter 4 — Test Setup Requirements

### 4.1 Shielded Room / Anechoic Chamber

| Requirement | Specification |
|-------------|--------------|
| Shielding effectiveness | > 100 dB from 10 kHz to 10 GHz |
| Ground plane | Copper or aluminum, bonded to room walls |
| EUT placement | 10 cm above ground plane on non-conductive table |
| Cable routing | Per installation drawing; cables 5 cm above ground plane |
| Power source | LISN-filtered or isolated (no external noise) |
| Ambient check | All antenna/receiver combinations; ambient must be 6 dB below limit |

### 4.2 Key Test Equipment

| Equipment | Function | Specification |
|-----------|----------|--------------|
| EMI Receiver | Measure emissions | CISPR-compliant, peak + quasi-peak + average |
| LISN (50 µH / 50 Ω) | Standardize power impedance | Per MIL-STD-461G Figure 4 |
| BCI Probe | Inject current on cables | 10 kHz – 400 MHz (calibrated) |
| RF Amplifiers | Generate RS103 field levels | 10 kHz – 40 GHz (multiple bands) |
| Antennas | Transmit/receive | Rod, biconical, log-periodic, horn, loop |
| Power meter / Field probe | Verify field strength | Isotropic E-field probe |
| Oscilloscope | CS115/CS116 waveforms | ≥ 1 GHz bandwidth |

---

## Chapter 5 — Emission Limits by Platform

### 5.1 RE102 Emission Limits Comparison

| Platform | 2 MHz | 30 MHz | 200 MHz | 1 GHz | 10 GHz |
|----------|--------|---------|---------|--------|---------|
| Army (Ground) | 40 dBµV/m | 34 dBµV/m | 24 dBµV/m | 34 dBµV/m | 44 dBµV/m |
| Navy (Surface Ship) | 40 dBµV/m | 34 dBµV/m | 24 dBµV/m | 34 dBµV/m | 44 dBµV/m |
| Navy (Submarine) | 24 dBµV/m | 24 dBµV/m | 24 dBµV/m | 34 dBµV/m | 44 dBµV/m |
| Aircraft (Internal) | 38 dBµV/m | 32 dBµV/m | 24 dBµV/m | 37 dBµV/m | 44 dBµV/m |
| Aircraft (External) | 28 dBµV/m | 28 dBµV/m | 24 dBµV/m | 34 dBµV/m | 44 dBµV/m |
| Space | Per MIL-STD-1541A | Custom tailored | — | — | — |

*Note: Submarine limits are most stringent due to stealth requirements*

---

## Chapter 6 — Design Guidelines for EMC Compliance

### 6.1 Common Design Techniques

| Technique | Emissions Benefit | Susceptibility Benefit | Application |
|-----------|------------------|----------------------|-------------|
| Shielding (conductive enclosure) | Reduces RE by 40-80 dB | Improves RS by 40-80 dB | All electronics |
| Cable filtering (feedthrough caps) | Reduces CE by 20-40 dB | Improves CS by 20-40 dB | Power/signal I/O |
| Ground plane partitioning | Reduces crosstalk | Reduces coupling | PCB design |
| Differential signaling | Reduces emissions | Improves immunity | High-speed data |
| Spread-spectrum clocking | Reduces narrowband emissions | N/A | Digital circuits |
| Ferrite cores on cables | 10-20 dB CM suppression | 10-20 dB improvement | Cable harnesses |
| Connector filtering (pi-filter pins) | 40-60 dB per pin | 40-60 dB per pin | MIL-DTL-38999 filtered connectors |

### 6.2 Shielding Effectiveness Requirements

| Application | Required SE | Method |
|-------------|-------------|--------|
| General electronics enclosure | 60 dB (100 kHz – 1 GHz) | Aluminum/steel housing with gaskets |
| EW receiver housing | 80-100 dB | Double-wall construction + EMI gaskets |
| Classified processing (TEMPEST) | 100+ dB | NSA-specified construction |
| Antenna system isolation | 80+ dB between systems | Physical separation + filtering |

---

## Chapter 7 — MIL-STD-461G Changes from Previous Versions

| Version | Year | Key Changes |
|---------|------|-------------|
| MIL-STD-461A | 1967 | Original issue; basic requirements |
| MIL-STD-461B | 1980 | Added CS tests; revised limits |
| MIL-STD-461C | 1986 | Reorganized; harmonized with platforms |
| MIL-STD-461D | 1993 | Significant restructure; combined CE/RE |
| MIL-STD-461E | 1999 | Added RS105 (EMP); revised limits |
| MIL-STD-461F | 2007 | Updated antenna methods; CS109 added |
| **MIL-STD-461G** | **2015** | Extended RE102 to 18 GHz; CS114 to 200 MHz; new figures |

---

## Chapter 8 — EMP / HEMP Hardening (RS105 + MIL-STD-2169B)

### 8.1 EMP Threat Parameters

| Parameter | HEMP (High-Altitude) | SREMP (Source Region) |
|-----------|---------------------|----------------------|
| Peak E-field | 50 kV/m | > 100 kV/m (very close) |
| Rise time | 2-5 ns | < 10 ns |
| Pulse width | ~25 ns (E1), ~1 µs (E2), ~100s (E3) | Short |
| Frequency content | DC to > 300 MHz | DC to > 1 GHz |
| Area of effect | Continental (1000+ km radius) | Local (km) |
| Primary damage mechanism | Burnout of unprotected electronics | System destruction |

### 8.2 EMP Protection Measures

| Measure | Protection Level | Application |
|---------|-----------------|-------------|
| Faraday cage (shielded enclosure) | 80-120 dB at EMP frequencies | Critical systems housing |
| Surge protection devices (SPD) | Clamp to safe levels | All penetration points |
| Point-of-entry (POE) filtering | 80 dB insertion loss | Power, signal, antenna |
| Fiber optic isolation | Complete galvanic isolation | Data links between facilities |
| Hardened components | Mil-grade with EMP spec | Component selection |
| Waveguide-below-cutoff penetration | > 100 dB at EMP frequencies | Ventilation, conduit |

---

## Chapter 9 — Relationship to Other EMC Standards

| Standard | Application | Key Difference from MIL-STD-461G |
|----------|-------------|----------------------------------|
| RTCA DO-160G Section 21 | Civil avionics EMC | Lower susceptibility levels; different categories |
| IEC 61000-4 series | Commercial EMC immunity | 3-10 V/m (vs 10-200 V/m military) |
| CISPR 32 / EN 55032 | Commercial emissions | Higher limits allowed (30+ dBµV/m) |
| Automotive (CISPR 25) | Vehicle electronics | Antenna + LISN methods; different limits |
| MIL-STD-1541A | Space systems | Unique environment (plasma, charging) |
| DEF STAN 59-411 | UK Defence EMC | Aligned with 461G but UK-specific platform tables |

---

## Chapter 10 — Interview Questions

### Entry-Level
1. What do the prefixes CE, CS, RE, RS stand for in MIL-STD-461G?
2. What is a LISN and what is its purpose in conducted emission tests?
3. Name three critical test requirements from MIL-STD-461G.

### Mid-Level
1. Explain the RE102 test setup including antenna placement, measurement distance, and frequency sweep.
2. How do emission limits differ between submarine and ground Army equipment, and why?
3. Design a filtering approach for a power connector to meet CE102 limits.

### Senior
1. Your LRU fails RS103 at 150 MHz by 6 dB. Diagnose likely causes and propose fixes without major redesign.
2. How do you tailor MIL-STD-461G requirements for a non-standard platform (e.g., UAS)?
3. Design a complete EMC test plan for a multi-function radar subsystem.

### Principal
1. How should MIL-STD-461G evolve to address wideband cognitive EW threats and 5G co-existence?
2. Propose a model-based EMC pre-compliance methodology using computational electromagnetics (CEM).
3. Design a system-level EMC architecture (per MIL-STD-464C) for an integrated multi-domain platform with radar, EW, and communications sharing the same mast structure.

---

*Document Version: 1.0 | Last Updated: May 2026 | Author: Defense Standards Engineering Team*
