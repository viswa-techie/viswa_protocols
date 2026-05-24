# Intel UPI (Ultra Path Interconnect) — CHEATSHEET
# ════════════════════════════════════════════════════════════════════
# Protocol: Intel UPI | Document: 06 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. BANDWIDTH CALCULATIONS

```
BW per link per direction = lanes × data_rate / 8
                          = 20 × 16 GT/s / 8 = 40 GB/s (raw)
                          × ~80% efficiency = ~32 GB/s (effective)

Total 2S BW (SPR): 4 links × 32 GB/s = 128 GB/s per direction
                    Bidirectional: 256 GB/s aggregate
```

| Platform | Links | GT/s | BW/link/dir | Total 2S BW/dir |
|----------|-------|------|-------------|-----------------|
| SKX (Gen 1) | 3 | 10.4 | 20.8 GB/s | 62.4 GB/s |
| CLX (Gen 2) | 3 | 10.4 | 20.8 GB/s | 62.4 GB/s |
| ICX (Gen 3) | 3 | 11.2 | 22.4 GB/s | 67.2 GB/s |
| SPR (Gen 4) | 4 | 16.0 | 32.0 GB/s | 128.0 GB/s |
| GNR (Gen 5) | 4 | 20.0 | 40.0 GB/s | 160.0 GB/s |

---

## 2. LATENCY REFERENCE

| Access Type | Latency | Notes |
|-------------|---------|-------|
| L1 cache hit | ~1 ns | Per-core |
| L2 cache hit | ~4-5 ns | Per-core |
| LLC hit (local CHA) | ~15-20 ns | On-die mesh |
| LLC hit (remote CHA) | ~25-35 ns | Cross-mesh |
| Local DRAM | ~80-100 ns | Through local MC |
| Remote DRAM (1 hop UPI) | ~130-150 ns | UPI + remote MC |
| Remote DRAM (2 hop, 8S) | ~200-250 ns | Two UPI traversals |
| UPI link latency (idle) | ~40-50 ns | Just the link |

---

## 3. MESIF STATE TRANSITIONS

| Current | Event | New State | Action |
|---------|-------|-----------|--------|
| I | RdData (exclusive) | E | Fetch from home/peer |
| I | RdData (shared) | S or F | Fetch, F if first sharer |
| E | Local write | M | Silent (no bus traffic) |
| E | Remote SnpInv | I | Respond RspI |
| E | Remote SnpData | I/S | Send data, transition |
| S | Local write | M | Send RdInvOwn to home |
| S | Remote SnpInv | I | Respond RspI |
| F | Remote SnpData | I | Forward data, → I |
| M | Remote SnpData | I | WB data via snoop resp |
| M | Eviction | I | WbMtoI to home |

---

## 4. KEY OPCODES

### Request (REQ) — CA → HA
| Opcode | Purpose |
|--------|---------|
| RdData | Read shared/exclusive |
| RdInvOwn | Read for ownership (write intent) |
| RdCur | Read current (no state change) |
| ItoM | Invalid to Modified (write, no data needed) |
| WbMtoI | Writeback Modified → Invalid |
| WbMtoS | Writeback Modified → Shared |
| NonSnpRd | Non-snooped read (MMIO) |
| NonSnpWr | Non-snooped write (MMIO) |

### Snoop (SNP) — HA → CA
| Opcode | Purpose |
|--------|---------|
| SnpInv | Invalidate (no data return needed) |
| SnpData | Get data + invalidate |
| SnpCur | Get data, no state change |
| SnpCode | Get instruction (code fetch) |

### Response (RSP) — Various
| Opcode | Purpose |
|--------|---------|
| RspI | Acknowledge, line now Invalid |
| RspS | Acknowledge, line now Shared |
| RspFwdI | Data forwarded, line now Invalid |
| RspFwdS | Data forwarded, line now Shared |
| CmpD | Completion with data |

---

## 5. FLOW CONTROL CREDITS

| Credit Pool | Allocation | Purpose |
|-------------|-----------|---------|
| VNA | Shared across all classes | High utilization |
| VN0_REQ | Dedicated (2-4 credits) | Guarantee REQ forward progress |
| VN0_SNP | Dedicated (2-4 credits) | Guarantee SNP delivery |
| VN0_RSP | Dedicated (2-4 credits) | Guarantee RSP completion |
| VN0_DATA | Dedicated (4-8 credits) | Guarantee DATA delivery |
| VN1 | Dedicated (routing) | Deadlock avoidance in multi-hop |

---

## 6. LINK POWER STATES

| State | Lanes Active | BW | Power | Exit Latency |
|-------|-------------|-----|-------|--------------|
| L0 | 20 | Full | Full | — |
| L0p | 10 | Half | ~60% | ~10ns |
| L1 | 0 | Zero | ~10% | ~1-10μs |

---

## 7. SNOOP MODES COMPARISON

| Mode | Who Snoops | When | Latency | Traffic | Best For |
|------|-----------|------|---------|---------|----------|
| Source Snoop | Requester | Immediately | Lowest | High | 2S |
| Early Snoop | HA (parallel) | With mem fetch | Low | Medium-High | 2S/4S |
| Home Snoop | HA (serial) | After SF check | Medium | Low | 4S/8S |
| Directory | HA (targeted) | SF hit only | Medium | Lowest | Large systems |

---

## 8. SNC CONFIGURATION

| Setting | NUMA Nodes/Socket | LLC/Node | MC Channels/Node |
|---------|-------------------|----------|-----------------|
| Disabled | 1 | Full | All (8) |
| SNC2 | 2 | Half | 4 |
| SNC4 | 4 | Quarter | 2 |

BIOS path: `Advanced → Processor → Sub NUMA Clustering`

---

## 9. LINUX COMMANDS FOR UPI

```bash
# NUMA topology
numactl --hardware                    # NUMA nodes & distances
lscpu                                 # CPU topology
lstopo                                # Graphical topology

# Memory binding
numactl --cpunodebind=0 --membind=0 ./app   # Pin to node 0
numactl --interleave=all ./app              # Interleave all nodes

# UPI counters (perf)
perf stat -e uncore_upi/event=0x02/ -a    # UPI flits sent
perf stat -e uncore_upi/event=0x03/ -a    # UPI data flits

# Intel PCM (Processor Counter Monitor)
pcm.x                                # Real-time UPI BW monitoring
pcm-memory.x                         # Memory + UPI bandwidth

# VTune
vtune -collect memory-access ./app    # Full UPI analysis

# Check UPI link status
rdmsr 0x300   # (model-specific, varies by platform)
dmesg | grep -i upi
```

---

## 10. XEON SCALABLE PLATFORMS

| Gen | Codename | Year | Cores | UPI | DDR | PCIe |
|-----|----------|------|-------|-----|-----|------|
| 1st | Skylake-SP | 2017 | 28 | 3×10.4GT/s | DDR4-2666, 6ch | PCIe 3.0 ×48 |
| 2nd | Cascade Lake | 2019 | 28 | 3×10.4GT/s | DDR4-2933, 6ch | PCIe 3.0 ×48 |
| 3rd | Ice Lake-SP | 2021 | 40 | 3×11.2GT/s | DDR4-3200, 8ch | PCIe 4.0 ×64 |
| 4th | Sapphire Rapids | 2023 | 60 | 4×16GT/s | DDR5-4800, 8ch | PCIe 5.0 ×80 |
| 5th | Granite Rapids | 2024 | 128 | 4×20GT/s | DDR5-6400, 12ch | PCIe 5.0 ×96 |

---

## 11. UPI vs COMPETITORS

| Feature | Intel UPI | AMD xGMI | CXL | NVLink |
|---------|-----------|----------|-----|--------|
| Use case | CPU↔CPU | CPU↔CPU | CPU↔Device/Memory | GPU↔GPU |
| Coherence | MESIF | MOESI | CXL.cache (MESI) | Custom |
| 2S BW/dir | 128 GB/s | 144 GB/s | Varies | N/A |
| Latency | ~130-150ns | ~130-170ns | ~150-300ns | ~1.5μs |
| Links (2S) | 4 | 4 | N/A | 18 (NVL72) |
| Max sockets | 8 | 2 | Switch-based | N/A |
| Open spec? | Partial | No | Yes | No |

---

## 12. TROUBLESHOOTING QUICK REFERENCE

| Symptom | Likely Cause | Check |
|---------|-------------|-------|
| High remote latency | NUMA misplacement | numastat, perf node-misses |
| UPI BW saturation | Cross-socket data sharing | pcm.x UPI utilization |
| CRC errors | Signal integrity | MCA banks, BIOS error log |
| Link degraded | Lane failure | dmesg, link status MSR |
| Latency spikes | L0p/L1 exit | Disable power mgmt in BIOS |
| SF overflow | Working set too large | Back-invalidation counters |

---

END OF DOCUMENT 06 — CHEATSHEET
