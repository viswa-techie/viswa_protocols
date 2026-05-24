# UCIe (Universal Chiplet Interconnect Express) — CHEATSHEET
# ════════════════════════════════════════════════════════════════════
# Protocol: UCIe | Document: 06 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. SPECIFICATION SUMMARY

| Parameter | Standard Packaging | Advanced Packaging |
|-----------|-------------------|-------------------|
| Bump pitch | 25-55 μm | 10-25 μm (<2μm hybrid) |
| Lanes/module (data) | 16 per direction | 64 per direction |
| Data rate | 4, 8, 12, 16, 24, 32 GT/s | 4, 8, 12, 16 GT/s |
| Reach | 2-10 mm | 0.5-2 mm |
| BW/module/dir | Up to 64 GB/s | Up to 128 GB/s |
| BW density | ~28 GB/s/mm | ~165 GB/s/mm |
| Energy | 0.5-1.0 pJ/bit | 0.25-0.5 pJ/bit |
| Latency (D2D) | ~3-5 ns | ~2-3 ns |
| Signaling | SE/Diff NRZ or PAM4 | SE NRZ |
| Clock | Forwarded | Forwarded |
| Redundancy | Yes (spare lanes) | Yes (spare lanes) |
| CRC | 16 or 32 bit | 16 or 32 bit |

---

## 2. BANDWIDTH TABLE

| Config | Lanes | Rate | BW/dir | Bidirectional |
|--------|-------|------|--------|---------------|
| Std 1× module @ 4GT/s | 16 | 4 | 8 GB/s | 16 GB/s |
| Std 1× module @ 16GT/s | 16 | 16 | 32 GB/s | 64 GB/s |
| Std 1× module @ 32GT/s | 16 | 32 | 64 GB/s | 128 GB/s |
| Adv 1× module @ 4GT/s | 64 | 4 | 32 GB/s | 64 GB/s |
| Adv 1× module @ 16GT/s | 64 | 16 | 128 GB/s | 256 GB/s |
| Std 4× modules @ 32GT/s | 64 | 32 | 256 GB/s | 512 GB/s |
| Adv 4× modules @ 16GT/s | 256 | 16 | 512 GB/s | 1024 GB/s |

```
Formula: BW (GB/s) = lanes × rate (GT/s) / 8
```

---

## 3. LAYER RESPONSIBILITIES

| Layer | Responsibilities | Interface Above | Interface Below |
|-------|-----------------|-----------------|-----------------|
| Protocol | CXL/PCIe/Stream/Raw semantics | Application | FDI |
| Adapter | Framing, CRC, retry, credits, mux | FDI | RDI |
| Physical | AFE, training, clocking, sideband | RDI | Bumps |

---

## 4. LINK TRAINING PHASES

| Phase | Name | Activity |
|-------|------|----------|
| 1 | SBINIT | Sideband: detect, capability exchange |
| 2 | MBINIT.CAL | Tx/Rx calibration |
| 3 | MBINIT.LFSR | Pattern training, bit lock |
| 4 | MBINIT.DESKEW | Lane-to-lane alignment |
| 5 | MBINIT.SPEED | Data rate negotiation |
| 6 | ADAPTER INIT | CRC/retry/credit bring-up |
| 7 | L0 | Operational (data transfer) |

---

## 5. PROTOCOL COMPARISON

| Protocol | Coherent? | Overhead | Latency | Use Case |
|----------|-----------|----------|---------|----------|
| CXL | Yes (cache/mem) | Medium | ~5ns | Compute, memory chiplets |
| PCIe | No | Medium | ~5ns | I/O chiplets |
| Streaming | No | Low | ~3ns | AI accelerators, custom |
| Raw | No | Minimal | ~2ns | Proprietary, test |

---

## 6. PACKAGING TECHNOLOGIES

| Technology | Vendor | Bump Pitch | Use With UCIe |
|-----------|--------|-----------|---------------|
| CoWoS-S | TSMC | 25-40 μm | Advanced |
| CoWoS-R | TSMC | 25 μm + RDL | Advanced |
| InFO | TSMC | 40-130 μm | Standard |
| EMIB | Intel | 36-55 μm | Std/Advanced |
| Foveros | Intel | 10-36 μm | Advanced |
| Hybrid bond (SoIC) | TSMC | <2 μm | Advanced (future) |
| Organic substrate | Various | 100+ μm | Standard |

---

## 7. ENERGY EFFICIENCY COMPARISON

| Interconnect | pJ/bit | Distance | Notes |
|--------------|--------|----------|-------|
| UCIe Advanced | 0.25-0.5 | <2mm | Simplest (no CDR) |
| UCIe Standard | 0.5-1.0 | 2-10mm | Light equalization |
| HBM3 | ~1.0 | 1-3mm | Wide bus, through TSV |
| PCIe 5.0 | ~6 | 10-30cm | Full CDR, ESD, EQ |
| PCIe 6.0 | ~8 | 10-30cm | PAM4, FEC overhead |
| Ethernet 100G | ~15 | 1-10m | Optical or heavy DSP |

---

## 8. UCIe MODULE BUMP COUNT

| Module Type | Tx Data | Rx Data | Clock | Sideband | GND/Power | Total Approx |
|-------------|---------|---------|-------|----------|-----------|-------------|
| Standard | 16 | 16 | 2 | 4 | ~30 | ~70 bumps |
| Advanced | 64 | 64 | 2 | 4 | ~80 | ~220 bumps |

---

## 9. COMPETITOR/RELATED TECHNOLOGIES

| Standard/Tech | Scope | Open? | vs UCIe |
|---------------|-------|-------|---------|
| UCIe | D2D protocol + PHY spec | Yes | — |
| BoW (Bunch of Wires) | D2D PHY only (OCP) | Yes | UCIe adds protocol layer |
| LIPINCON | D2D PHY (TSMC) | No | Proprietary, high density |
| AIB/AIB2 | D2D PHY (Intel) | Yes | Predecessor, simpler |
| AMD IF (GMI) | D2D full stack | No | Proprietary to AMD |
| NVLink-C2C | D2D for NVIDIA | No | Custom for Grace Hopper |
| CXL | Protocol (board-level) | Yes | UCIe carries CXL in-package |

---

## 10. DESIGN DECISIONS QUICK GUIDE

| Decision | Choose Standard | Choose Advanced |
|----------|----------------|-----------------|
| Cost sensitivity | ✓ (organic is cheap) | ✗ (interposer expensive) |
| BW density needed | Low-medium | High (>100 GB/s/mm) |
| Chiplet spacing | >2mm | <2mm |
| Power budget | Relaxed | Tight (need <0.5 pJ/bit) |
| Manufacturing maturity | ✓ (well established) | Improving (some risk) |
| HBM co-location | ✗ | ✓ (same interposer) |

---

## 11. KEY FORMULAS

```
Bandwidth:
  BW (GB/s) = num_lanes × data_rate (GT/s) / 8

BW Density:
  BW_density (GB/s/mm) = BW_per_module / module_edge_width

Energy per bit:
  Total power = BW (Gb/s) × energy (pJ/bit)
  Example: 128 GB/s × 8 × 0.5 pJ/bit = 512 mW

Yield (per module):
  Y_module = (1 - p_defect)^num_bumps × lane_repair_recovery

Die edge utilization:
  Beachfront_needed (mm) = Required_BW / BW_density
  Example: 512 GB/s / 165 = 3.1 mm of die edge (advanced)
```

---

## 12. TIMELINE

| Date | Milestone |
|------|-----------|
| 2022 Q1 | UCIe 1.0 specification released |
| 2022 | Consortium formed (Intel, AMD, ARM, TSMC, etc.) |
| 2023 Q3 | UCIe 1.1 released (retimer, streaming updates) |
| 2024+ | First UCIe-compliant products expected |
| 2025+ | UCIe 2.0 expected (switching, higher speeds) |
| Future | Chiplet marketplace maturity |

---

END OF DOCUMENT 06 — CHEATSHEET
