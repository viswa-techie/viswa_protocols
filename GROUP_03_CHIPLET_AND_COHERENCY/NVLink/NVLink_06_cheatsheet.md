# NVLink — CHEATSHEET
# ════════════════════════════════════════════════════════════════════
# Protocol: NVLink | Document: 06 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. BANDWIDTH EVOLUTION
| Gen | Year | Per Link | Links/GPU | Total/GPU | Signaling |
|-----|------|----------|-----------|-----------|-----------|
| 1.0 | 2016 | 40 GB/s | 4 | 160 GB/s | 20G NRZ |
| 2.0 | 2017 | 50 GB/s | 6 | 300 GB/s | 25G NRZ |
| 3.0 | 2020 | 50 GB/s | 12 | 600 GB/s | 50G PAM4 |
| 4.0 | 2022 | 50 GB/s | 18 | 900 GB/s | 50G PAM4 |
| 5.0 | 2024 | 100 GB/s | 18 | 1800 GB/s | 100G PAM4 |

## 2. LINK STRUCTURE
```
1 NVLink = 2 sub-links (TX + RX) = full duplex
1 sub-link = 1 brick = 8 differential lane pairs
BW per direction = 8 lanes × lane_rate / 8
NVLink 4.0: 8 × 50 Gbps / 8 = 50 GB/s per direction
```

## 3. PROTOCOL LAYERS
| Layer | Function | Key Feature |
|-------|----------|-------------|
| TL (Transaction) | Requests/responses | Virtual channels, ordering |
| DL (Data Link) | Flits, reliability | CRC, replay, credits |
| PL (Physical) | Electrical | SERDES, PAM4, CDR |

## 4. VIRTUAL CHANNELS
| VC | Traffic | Purpose |
|----|---------|---------|
| Request | Reads, Writes, Atomics | Data access |
| Probe | Coherence snoops | Cache state changes |
| Response | Completions, Acks | Avoiding deadlock |

## 5. NVSWITCH GENERATIONS
| Gen | GPU Arch | Ports | Aggregate BW | GPUs |
|-----|----------|-------|-------------|------|
| 1 | Volta | 18 | 900 GB/s | 8 |
| 2 | Ampere | 36 | 1.8 TB/s | 8 |
| 3 | Hopper | 64 | 3.6 TB/s | 8 |
| 4 | Blackwell | — | — | 576 |

## 6. LATENCY REFERENCE
| Path | Typical Latency |
|------|----------------|
| GPU local HBM | ~100ns |
| GPU-GPU (NVLink, 1 hop) | ~200-300ns |
| GPU-CPU (NVLink-C2C) | ~200ns |
| GPU-CPU (PCIe) | ~1-2μs |
| GPU-GPU (PCIe) | ~5-10μs |

## 7. NVIDIA PLATFORMS
| Platform | GPUs | NVLink Gen | Topology |
|----------|------|-----------|----------|
| DGX A100 | 8×A100 | 3.0 | NVSwitch 2 |
| DGX H100 | 8×H100 | 4.0 | NVSwitch 3 |
| DGX GB200 | 72×B200 | 5.0 | NVSwitch 4 |
| Grace Hopper | 1 GPU+1 CPU | C2C | Point-to-point |
| GB200 | 2 GPU+1 CPU | C2C+5.0 | C2C + NVSwitch |

## 8. KEY COMMANDS/TOOLS
```bash
# Check NVLink status
nvidia-smi nvlink -s         # Status
nvidia-smi nvlink -e         # Error counts
nvidia-smi topo -m           # Topology matrix

# Bandwidth test
./nvbandwidth                 # NVIDIA bandwidth benchmark
./p2pBandwidthLatencyTest    # CUDA sample

# NCCL test
./all_reduce_perf -b 1M -e 1G -g 8   # All-reduce benchmark
```

## 9. ALL-REDUCE FORMULAS
```
Ring all-reduce:
  Time = 2 × (N-1)/N × DataSize / LinkBW
  Steps = 2 × (N-1)

NVSwitch all-reduce (all-to-all):
  Time = DataSize / (N × LinkBW)
  Steps = 1 (simultaneous)

Speedup(NVSwitch vs Ring) ≈ 2(N-1) / 1 for large N
  For N=8: ~14x fewer steps
```

## 10. NVLINK-C2C vs STANDARD
| Feature | NVLink (GPU-GPU) | NVLink-C2C |
|---------|-----------------|-------------|
| Reach | Board (cm-m) | Package (mm-cm) |
| Coherence | Optional | Full hardware |
| Power/bit | Higher | Lower |
| Use case | Multi-GPU | CPU-GPU superchip |
| Products | DGX, HGX | Grace Hopper, GB200 |

---

END OF DOCUMENT 06 — CHEATSHEET
