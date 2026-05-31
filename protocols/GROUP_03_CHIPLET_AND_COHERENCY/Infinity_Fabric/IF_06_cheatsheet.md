# AMD Infinity Fabric — CHEATSHEET
# ════════════════════════════════════════════════════════════════════
# Protocol: Infinity Fabric | Document: 06 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. EPYC GENOA QUICK SPECS

| Parameter | Value |
|-----------|-------|
| Max CCDs | 12 |
| Cores/CCD | 8 (Zen 4) |
| Max cores | 96 |
| L3/CCD | 32 MB |
| Total L3 | 384 MB |
| DDR5 channels | 12 |
| DDR5 speed | 4800 MT/s |
| DDR5 BW | ~460 GB/s |
| PCIe | 5.0 × 128 lanes |
| xGMI links | 4 |
| xGMI BW | ~36 GB/s/link/dir |
| Total xGMI | ~144 GB/s/dir |
| CCD node | 5nm |
| IOD node | 6nm |

---

## 2. LATENCY HIERARCHY

| Access | Latency | Notes |
|--------|---------|-------|
| L1 hit | ~1 ns | Per-core |
| L2 hit | ~4 ns | Per-core |
| L3 hit (local CCD) | 10-15 ns | Same CCD |
| L3 hit (remote CCD) | 40-50 ns | Via IOD fabric |
| Local DRAM | 70-90 ns | Through IOD→MC |
| Remote DRAM (other socket) | 130-170 ns | xGMI + remote IOD |

---

## 3. NPS MODES REFERENCE

| Mode | NUMA Nodes/Socket | Channels/Node | CCDs/Node | Best For |
|------|-------------------|---------------|-----------|----------|
| NPS1 | 1 | 12 | 12 | Max BW, simple apps |
| NPS2 | 2 | 6 | 6 | Balanced |
| NPS4 | 4 | 3 | 3 | Low latency, VMs |

---

## 4. MOESI STATE TABLE

| State | Dirty? | Exclusive? | Shareable? | Valid? |
|-------|--------|-----------|------------|--------|
| M (Modified) | Yes | Yes | No | Yes |
| O (Owned) | Yes | No | Yes | Yes |
| E (Exclusive) | No | Yes | No | Yes |
| S (Shared) | No | No | Yes | Yes |
| I (Invalid) | — | — | — | No |

---

## 5. FCLK/MCLK TUNING

| Scenario | FCLK | MCLK | Ratio | Latency Impact |
|----------|------|------|-------|----------------|
| DDR5-4800 (JEDEC) | 2400 | 2400 | 1:1 | Optimal |
| DDR5-5200 | 2600 | 2600 | 1:1 | Optimal |
| DDR5-6000 | 2000 | 3000 | 2:3 | +5-10ns penalty |
| OC DDR5-6400 | 2000 | 3200 | 2:1 | +10ns penalty |

**Rule**: Keep 1:1 whenever possible. Desync only when MEM OC > IF ceiling.

---

## 6. xGMI SPECIFICATIONS (BY GENERATION)

| Platform | xGMI Gen | Links | Per-Link BW | Total BW/dir |
|----------|----------|-------|-------------|--------------|
| EPYC Rome | xGMI 2 | 4 | ~18 GB/s | ~72 GB/s |
| EPYC Milan | xGMI 3 | 4 | ~25 GB/s | ~100 GB/s |
| EPYC Genoa | xGMI 3+ | 4 | ~36 GB/s | ~144 GB/s |
| EPYC Turin | xGMI 4 | 4 | ~40+ GB/s | ~160+ GB/s |

---

## 7. KEY COMPONENTS

| Component | Role |
|-----------|------|
| SDF | Data path (crossbar) |
| SCF | Control path (coherence) |
| CAKE | Socket extender (xGMI controller) |
| CCM | CCD-facing port on SDF |
| CCS | Memory-controller-facing port on SDF |
| GMI | On-package die-to-die link |
| xGMI | Inter-socket link |
| Probe Filter | Directory for coherence |
| Home Agent | MC that owns address range |

---

## 8. LINUX TOOLS FOR IF/NUMA

```bash
# NUMA topology
numactl --hardware          # Show nodes, CPUs, distances
lscpu                       # CPU layout with NUMA
lstopo                      # Graphical/text topology (hwloc)

# Memory placement
numactl --membind=0 ./app   # Force memory on node 0
numactl --cpunodebind=0     # Force CPU on node 0
numactl --interleave=all    # Round-robin across nodes

# Performance monitoring
numastat                    # Per-node allocation stats
numastat -p <pid>           # Per-process NUMA stats
perf stat -e node-load-misses,node-store-misses ./app

# AMD-specific
cat /sys/devices/system/node/node*/meminfo
cat /sys/devices/system/cpu/cpu*/topology/die_id
# AMD μProf for IF utilization
```

---

## 9. BIOS SETTINGS (AMD CBS)

| Setting | Path | Options |
|---------|------|---------|
| NPS mode | CBS→DF→NUMA nodes per socket | NPS0/1/2/4 |
| Memory interleaving | CBS→DF→Memory interleaving | Channel/Die/Socket |
| FCLK | CBS→NBIO→SMU→FCLK | Auto/Manual MHz |
| xGMI link width | CBS→DF→xGMI | x16/x8 |
| L3 as NUMA | CBS→DF→ACPI SRAT L3 | Enable/Disable |
| IOMMU | CBS→NBIO→IOMMU | Enable/Disable |

---

## 10. COMPARISON TABLE

| Feature | Infinity Fabric | Intel UPI | NVLink |
|---------|----------------|-----------|--------|
| Use case | CPU chiplets/sockets | CPU sockets | GPU-GPU |
| Coherence | MOESI | MESIF | Custom (NVLink) |
| Topology | Star (IOD hub) | Ring/Mesh | NVSwitch |
| Bandwidth (socket-socket) | ~144 GB/s | ~64 GB/s | ~900 GB/s |
| Latency | 130-170ns (remote) | ~130ns | ~1.5μs (GPU) |
| Open spec? | No (proprietary) | No | No |
| Die-to-die? | Yes (GMI) | EMIB | NVLink-C2C |

---

## 11. EPYC PRODUCT STACK

| Processor | Cores | CCDs | L3 Cache | xGMI | Socket |
|-----------|-------|------|----------|------|--------|
| 9654 | 96 | 12 | 384 MB | Yes | SP5 |
| 9554 | 64 | 8 | 256 MB | Yes | SP5 |
| 9354 | 32 | 4 | 256 MB | Yes | SP5 |
| 9754 (V-Cache) | 128 | 16 | 256+768 MB | Yes | SP5 |

---

## 12. KEY FORMULAS

```
IF Bandwidth = FCLK × bytes_per_clock_cycle

Remote Access Latency ≈ Local_latency + 2×(CAKE_latency + xGMI_propagation)

NUMA penalty ratio = Remote_latency / Local_latency  ≈ 1.5-2.0×

Memory BW per channel = DDR5_MT/s × 8 bytes / 2  (DDR = double data rate)

Total System BW = num_channels × per_channel_BW
                = 12 × (4800 × 8 / 2) = ~460 GB/s
```

---

END OF DOCUMENT 06 — CHEATSHEET
