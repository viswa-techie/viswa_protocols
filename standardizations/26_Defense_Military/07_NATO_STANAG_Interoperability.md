# NATO STANAGs — Interoperability Standards

**Category:** 26 — Defense & Military Standards  
**Document:** 07 — NATO STANAG Interoperability  
**Standard:** NATO Standardization Agreements (STANAGs)  
**Scope:** Multinational defense interoperability across communications, UAVs, weapons, data  
**Audience:** Systems engineers, defense program managers, interoperability architects  
**Prerequisites:** Military communications fundamentals, NATO organizational structure

---

## Chapter 1 — NATO Standardization Framework

### 1.1 STANAG Hierarchy

| Level | Name | Description |
|-------|------|-------------|
| STANAG | Standardization Agreement | Agreement between NATO nations to adopt a standard |
| AP (Allied Publication) | Allied Publication | Detailed technical specification referenced by STANAG |
| AEDP | Allied Engineering Documentation Publication | Engineering implementation documents |
| AOP | Allied Ordnance Publication | Ammunition/explosives standards |
| AQAP | Allied Quality Assurance Publication | Quality management |
| ADatP | Allied Data Publication | Data exchange formats |

### 1.2 Ratification Process

```mermaid
graph LR
    A[Requirement<br/>Identified] --> B[Study Draft]
    B --> C[Working Group<br/>Development]
    C --> D[NATO Committee<br/>Review]
    D --> E[Nations<br/>Ratification]
    E --> F[Promulgation]
    F --> G[Implementation<br/>by Nations]
```

### 1.3 Levels of Implementation

| Status | Meaning |
|--------|---------|
| **Ratified** | Nation agrees to the standard |
| **Implemented** | Standard in use in national forces |
| **Reserved** | Nation reserves position (does not commit) |
| **Not ratified** | Nation explicitly does not adopt |
| **With reservations** | Partial adoption with documented deviations |

---

## Chapter 2 — UAV/UAS STANAGs

### 2.1 STANAG 4671 — UAV Systems Airworthiness

| Aspect | Detail |
|--------|--------|
| Title | UAV Systems Airworthiness Requirements (USAR) |
| Scope | Fixed-wing UAVs 150-20,000 kg MTOW |
| Based on | CS-23 (EASA light aircraft) adapted for unmanned |
| Key difference from manned | No occupant protection; focus on ground risk |
| Certification approach | Type certificate for UAV platform |
| Software | DO-178C applied (DAL based on ground risk) |
| Structure | Subparts A-J (flight, structure, design, powerplant, equipment) |

### 2.2 STANAG 4586 — UAV Control System Interfaces

| Aspect | Detail |
|--------|--------|
| Title | Standard Interfaces of UAV Control System (UCS) for NATO UAV Interoperability |
| Purpose | Allow any NATO ground station to control any NATO UAV |
| Architecture | 5 Levels of Interoperability (LOI) |
| Data Link | Defines command/control and payload data interfaces |
| Current edition | Edition 4 |

**Levels of Interoperability (LOI):**

| LOI | Capability | Example |
|-----|-----------|---------|
| 1 | Indirect receipt of UAV payload data (relay) | Receive video from another GCS |
| 2 | Direct receipt of payload data from UAV | Directly receive ISR imagery |
| 3 | Control of UAV payload (sensors) | Operate camera/sensors on foreign UAV |
| 4 | Control of UAV flight (navigation) | Fly another nation's UAV |
| 5 | Monitor/control launch and recovery | Full operational control |

### 2.3 STANAG 7085 — Interoperable Data Links for ISR

| Aspect | Detail |
|--------|--------|
| Title | Interoperable Data Links for Imaging Systems |
| Purpose | Standardize wideband data links for UAV video/imagery |
| Bandwidth | Supports high-definition video (H.264/H.265) |
| Key protocol | MISB (Motion Imagery Standards Board) metadata |
| Encryption | Interoperable COMSEC for coalition sharing |
| Waveform | CDL (Common Data Link) derivatives |

### 2.4 STANAG 4609 — Digital Motion Imagery

| Aspect | Detail |
|--------|--------|
| Title | NATO Digital Motion Imagery Standard |
| Scope | Metadata encoding for full-motion video (FMV) |
| Based on | MISB standards (US GEOINT) |
| Key content | KLV (Key-Length-Value) metadata embedding |
| Applications | ISR video with geospatial metadata |
| Format | MPEG-2 TS or H.264/H.265 with KLV packets |

---

## Chapter 3 — Communications STANAGs

### 3.1 STANAG 5066 — HF Data Communications

| Aspect | Detail |
|--------|--------|
| Title | Profile for HF Radio Data Communications |
| Scope | Beyond-line-of-sight (BLOS) data communications via HF radio |
| Frequency | 2-30 MHz (HF band) |
| Data rate | Up to 9600 bps (with advanced waveforms) |
| Layers | Data link, subnet access, application support |
| Key feature | ARQ (Automatic Repeat reQuest) for reliable delivery |
| Applications | Ship-to-shore, tactical BLOS messaging |

### 3.2 STANAG 4774/4778 — Metadata Binding & Confidentiality Labeling

| STANAG | Title | Purpose |
|--------|-------|---------|
| 4774 | Confidentiality Metadata Label Syntax | Standard format for security labels on data |
| 4778 | Metadata Binding Mechanism | Cryptographically bind labels to content |

### 3.3 STANAG 5516 — Link 16

| Aspect | Detail |
|--------|--------|
| Title | Tactical Data Exchange (Link 16) |
| Scope | Real-time tactical data exchange (air/sea/ground) |
| Waveform | JTIDS/MIDS (J-series messages) |
| Data rate | Up to 238 kbps per net |
| Architecture | TDMA (Time Division Multiple Access) |
| Range | ~300 nm (500+ km) LOS |
| Participants | Aircraft, ships, ground stations, SAM systems |
| Encryption | MSEC (Multinet Security) — KGV-127 or equivalent |
| Key applications | Air picture, weapons coordination, IFF |

### 3.4 STANAG 4607 — Ground Moving Target Indicator (GMTI)

| Aspect | Detail |
|--------|--------|
| Title | NATO Ground Moving Target Indicator Format |
| Scope | Standard format for radar GMTI data exchange |
| Applications | JSTARS, E-8, ground surveillance radar |
| Content | Target reports (location, velocity, classification) |
| Format | Binary message format with segments |

---

## Chapter 4 — Protection & Survivability STANAGs

### 4.1 STANAG 2920 — Ballistic Protection

| Aspect | Detail |
|--------|--------|
| Title | Ballistic Test Method for Personal Armour Materials and Combat Clothing |
| Scope | Standard test methodology for body armor |
| Key metric | V₅₀ (velocity at which 50% of projectiles penetrate) |
| Fragment type | 1.1g FSP (Fragment Simulating Projectile) |
| Threat levels | Defined fragment velocities for protection levels |

### 4.2 STANAG 4569 — Vehicle Protection Levels

| Level | Kinetic Energy (KE) | Artillery/Mine |
|-------|---------------------|----------------|
| 1 | 7.62×51 NATO ball (M80) at 30m | Hand grenades, AP mines |
| 2 | 7.62×39 AP at 30m | 6 kg AT mine (blast) |
| 3 | 7.62×51 AP at 30m | 8 kg AT mine |
| 4 | 14.5×114 AP at 200m | 10 kg AT mine |
| 5 | 25mm APDS-T at 500m | Large IED blast |
| 6 | 30mm APFSDS at 500m | — |

### 4.3 STANAG 4439 — Nuclear, Biological, Chemical (NBC) Protection

| Aspect | Detail |
|--------|--------|
| Title | NBC Defence Equipment — Performance Standards |
| Scope | Protection factors for individual and collective NBC protection |
| Key metrics | Protection Factor (PF), breakthrough time |
| Categories | Individual (masks, suits), collective (vehicle filtration, shelters) |

---

## Chapter 5 — Ordnance & Weapons STANAGs

### 5.1 Key Ammunition STANAGs

| STANAG | Subject | Significance |
|--------|---------|-------------|
| 2310 | Ammunition lot numbering | Common identification |
| 4107 | 5.56×45 NATO cartridge (SS109/M855) | Standard rifle ammunition |
| 4172 | 9×19 NATO cartridge | Standard pistol ammunition |
| 4355 | Modified Point Mass trajectory model | Fire control calculations |
| 4425 | 12.7×99 NATO (.50 BMG) | Heavy machine gun standardization |
| 4512 | 155mm artillery modular charge | Common propellant system |
| 4586 | 25mm × 137 ammunition | Autocannon standardization |

### 5.2 STANAG 4187 — Fuze Interfaces

| Aspect | Detail |
|--------|--------|
| Scope | Standard fuze thread sizes and interfaces for artillery/mortar |
| Purpose | Any NATO fuze fits any NATO munition of same caliber |
| Example | M557 fuze thread compatible across 155mm shells |
| Safety | STANAG 4187 also addresses fuze arming sequences |

---

## Chapter 6 — C4ISR & Data Exchange STANAGs

### 6.1 ADatP-3 — NATO Message Text Formatting

| Aspect | Detail |
|--------|--------|
| Title | NATO Message Text Formatting System (FORMETS) |
| Scope | Standard message formats for operational communications |
| Content | Message sets (OPREP, INTREP, MEDEVAC, etc.) |
| Format | Structured text with defined fields and sets |

### 6.2 STANAG 5500 — NATO Core GIS Services

| Aspect | Detail |
|--------|--------|
| Purpose | Interoperable geospatial services across NATO systems |
| Based on | OGC (Open Geospatial Consortium) standards |
| Services | WMS (Web Map Service), WFS (Web Feature Service) |
| Coordinate reference | WGS 84 (STANAG 2211) |

### 6.3 STANAG 5524 — MIP Data Exchange Mechanism (DEM)

| Aspect | Detail |
|--------|--------|
| Title | Multilateral Interoperability Programme Data Exchange |
| Scope | C2 system interoperability (Command & Control) |
| Architecture | Publish-subscribe data exchange between C2 systems |
| Format | JC3IEDM (Joint C3 Information Exchange Data Model) |
| Successor | NIEM (NATO Information Exchange Model) evolving |

---

## Chapter 7 — Quality & Logistics STANAGs

### 7.1 AQAP (Allied Quality Assurance Publications)

| AQAP | Title | Equivalent |
|------|-------|-----------|
| AQAP-2110 | NATO Quality Assurance Requirements for Design, Development, Production | ISO 9001 + military requirements |
| AQAP-2210 | NATO Supplementary Software Quality Requirements | DO-178C alignment + NATO specifics |
| AQAP-2310 | NATO Quality Assurance Requirements for Aviation | AS9100 equivalent for NATO |
| AQAP-2070 | NATO Mutual Government Quality Assurance (GQA) Process | International inspection process |

### 7.2 STANAG 2961 — Classes of Supply

| Class | Description | Examples |
|-------|-------------|---------|
| I | Subsistence | Food, water |
| II | Clothing, equipment | Uniforms, tools |
| III | POL (Petroleum, Oil, Lubricants) | Fuel, hydraulic fluid |
| IV | Construction materials | Barrier materials, lumber |
| V | Ammunition | All calibers, missiles |
| VI | Personal demand items | PX supplies |
| VII | Major end items | Vehicles, aircraft |
| VIII | Medical | Pharmaceuticals, blood |
| IX | Spare parts | Repair components |
| X | Non-military programs | Agricultural, economic aid |

---

## Chapter 8 — Emerging & Cyber STANAGs

### 8.1 STANAG 4778 — Cyber Defence

| Area | STANAGs | Focus |
|------|---------|-------|
| Information assurance | STANAG 4774, 4778 | Data labeling, confidentiality |
| Cyber operations | Emerging standards | Offensive/defensive cyber coordination |
| TEMPEST | SDIP-27/28/29 | Emanations security |
| CIS security | AC/322 documents | NATO communications security |

### 8.2 Federated Mission Networking (FMN)

| Aspect | Detail |
|--------|--------|
| Purpose | Standardize coalition networks for joint operations |
| Architecture | Spiral development (FMN Spiral 4/5 current) |
| Key services | Chat, email, VoIP, VTC, file sharing, C2 services |
| Security | NATO SECRET and below, cross-domain solutions |
| Standards basis | Many STANAGs + commercial standards (IP, HTTP, etc.) |

---

## Chapter 9 — AEDP-3 (Electronic Warfare Data Library)

### 9.1 Overview

| Aspect | Detail |
|--------|--------|
| Title | NATO Electronic Warfare Data Library (EWDL) |
| Purpose | Standardized threat library format for EW systems |
| Content | Radar parameters (frequencies, PRFs, scan patterns, modes) |
| Format | Structured database entries for threat emitters |
| Use | Program EW receivers (RWR, ESM) with threat parameters |
| Classification | Usually NATO SECRET or higher |
| Maintenance | Continuously updated as new threats identified |

### 9.2 AEDP-3 Data Elements

| Parameter | Description |
|-----------|-------------|
| Emitter name/designation | Threat system identification |
| Frequency range | Operating band(s) |
| PRF/PRI | Pulse Repetition Frequency/Interval |
| Pulse width | Duration of radar pulse |
| Scan type | Mechanical, electronic, hybrid |
| Antenna pattern | Beamwidth, sidelobes |
| Power | Peak/average transmitted power |
| Modes | Acquisition, tracking, engagement |
| Associated weapons | Missile/gun systems using this radar |

---

## Chapter 10 — Implementation Challenges

### 10.1 Common Interoperability Issues

| Challenge | Description | Mitigation |
|-----------|-------------|-----------|
| Version mismatch | Nations implementing different STANAG editions | Require minimum version in OPLAN |
| National caveats | Partial implementation or reservations | Interoperability testing (CWIX) |
| Classification barriers | Different national classification policies | Cross-domain solutions, releasability |
| Bandwidth limitations | Coalition networks limited bandwidth | Data compression, prioritization |
| Language | Different operational languages | ADatP-3 standard message formats |
| Legacy systems | Older platforms can't meet newer STANAGs | Gateways, translators |
| Testing | Difficult to test multinational interoperability | Annual exercises (CWIX, Bold Quest) |

### 10.2 Coalition Warfare Interoperability Exercise (CWIX)

| Aspect | Detail |
|--------|--------|
| Frequency | Annual (held in Poland) |
| Participants | 30+ NATO nations |
| Purpose | Test C4ISR interoperability in lab environment |
| Focus areas | Link 16, FMN, ISR data, logistics, cyber |
| Outcome | Interoperability scores, deficiency reports |

---

## Chapter 11 — Interview Questions

### Entry-Level
1. What is a STANAG and how does it differ from a national standard?
2. Explain the five LOI (Levels of Interoperability) for STANAG 4586.
3. What is Link 16 and what tactical information does it exchange?

### Mid-Level
1. How does STANAG 4671 differ from EASA CS-25 for manned aircraft certification?
2. Design a data link architecture for a multinational ISR operation using STANAG 7085 and 4609.
3. Explain the STANAG 4569 vehicle protection levels. How does level 4 differ from level 2?

### Senior
1. Propose an interoperability architecture for a 5-nation coalition air defense operation, identifying which STANAGs are required and where gaps exist.
2. How would you design a gateway system to connect a Link 16 network with a non-NATO ally using STANAG 5066 HF as backup?
3. Evaluate the adequacy of current STANAGs for autonomous systems (AI-enabled UAV swarms). Where are the gaps?

### Principal / Program Director
1. How should NATO evolve its standardization process to keep pace with rapid commercial technology evolution (cloud, AI, quantum)?
2. Propose a strategy for incorporating non-NATO partners (e.g., Japan, Australia, South Korea) into STANAG-based interoperability frameworks.
3. Design a modernization pathway for replacing Link 16 with a next-generation tactical data link that supports contested/congested electromagnetic spectrum operations.

---

*Document Version: 1.0 | Last Updated: May 2026 | Author: Defense Standards Engineering Team*
