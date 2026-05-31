# CXL — CHEATSHEET
# ════════════════════════════════════════════════════════════════════
# Protocol: CXL | Document: 06 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. DEVICE TYPE QUICK REFERENCE
| Type | CXL.io | CXL.cache | CXL.mem | Example |
|------|--------|-----------|---------|---------|
| 1 | ✓ | ✓ | ✗ | SmartNIC, NIC |
| 2 | ✓ | ✓ | ✓ | GPU, FPGA |
| 3 | ✓ | ✗ | ✓ | Memory expander |

## 2. CXL.cache OPCODES

### D2H Request (Device → Host)
| Opcode | Purpose |
|--------|---------|
| RdCurr | Read current, no cache alloc |
| RdOwn | Read for ownership (get M/E) |
| RdShared | Read shared (get S/E) |
| RdAny | Read any state (flexible) |
| ItoMWr | Write 64B (I→M transition) |
| WrCur | Write current, no alloc |
| CLFlush | Flush cache line to memory |
| CleanEvict | Notify clean line eviction |
| DirtyEvict | Writeback dirty line |
| CleanEvictNoData | Clean eviction (no data) |

### H2D Request/Snoop (Host → Device)
| Opcode | Purpose |
|--------|---------|
| SnpInv | Invalidate device line |
| SnpData | Get data, may downgrade |
| SnpCur | Get data, no state change |

### H2D Response (Host → Device)
| Opcode | Meaning |
|--------|---------|
| GO-I | Writeback accepted (line→Invalid) |
| GO-S | Grant Shared state |
| GO-E | Grant Exclusive state |
| GO-M | Grant Modified state |
| GO-WritePull | Ack write, pull data later |
| FastGO-WritePull | Fast ack + pull |
| ExtCmp | Extended completion |

## 3. CXL.mem OPCODES

### M2S (Host → Device)
| Opcode | Purpose |
|--------|---------|
| MemRd | Read from device memory |
| MemRdData | Read expecting data |
| MemInv | Invalidate at device |
| MemWr | Write to device memory |
| MemWrPtl | Partial write (byte enables) |

### S2M (Device → Host)
| Opcode | Purpose |
|--------|---------|
| MemData | Data response (read) |
| Cmp | Completion (write ack) |
| Cmp-S | Completion, line now Shared |
| Cmp-E | Completion, line now Exclusive |

## 4. COHERENCE STATES (MESI at Device)
| State | Host sees | Device has |
|-------|-----------|------------|
| M | Invalid/Shared | Dirty Exclusive |
| E | Invalid | Clean Exclusive |
| S | Shared | Shared |
| I | Any | Not cached |

## 5. META-STATES (CXL.mem, Type 2)
| Meta | Host line state | Device may have |
|------|----------------|-----------------|
| 0 (I) | Not cached | Any |
| 1 (A) | Any | Any (device manages) |
| 2 (S) | Shared | Shared |
| 3 (E) | Exclusive | Invalid |

## 6. FLIT FORMATS
```
CXL 1.x/2.0: 68B = [2B Header][64B Payload (16 slots × 4B)][2B CRC]
CXL 3.0:    256B = [Header/Ctrl][236B Payload][CRC]
```

## 7. CHANNEL SUMMARY
| Sub-protocol | Channels | Direction |
|-------------|----------|-----------|
| CXL.cache | D2H Req, D2H Resp, D2H Data | Dev→Host |
| CXL.cache | H2D Req, H2D Resp, H2D Data | Host→Dev |
| CXL.mem | M2S Req, M2S RwD | Host→Dev |
| CXL.mem | S2M NDR, S2M DRS | Dev→Host |

## 8. LATENCY COMPARISON
| Path | Latency |
|------|---------|
| Local DRAM | ~80-100ns |
| CXL direct attached | ~150-250ns |
| CXL through switch | ~200-350ns |
| Remote NUMA | ~150-200ns |
| PCIe MMIO | ~1-2μs |

## 9. BANDWIDTH (x16 link)
| Generation | Per-direction BW |
|-----------|-----------------|
| CXL 1.0/1.1 (PCIe 5.0) | ~32 GB/s |
| CXL 2.0 (PCIe 5.0) | ~32 GB/s |
| CXL 3.0 (PCIe 6.0) | ~64 GB/s |
| CXL 3.1 (PCIe 6.0) | ~64 GB/s |

## 10. BIAS MODE QUICK REFERENCE
```
Host Bias:   Host accesses HDM → fast (no device involvement)
             Device accesses HDM → must request from host (slow)
             Use when: CPU-dominant workload phase

Device Bias: Device accesses HDM → fast (direct, no host)
             Host accesses HDM → must BI device cache (slow)
             Use when: Device-dominant workload phase
```

## 11. KEY PCIE CONFIG REGISTERS
| Register | Purpose |
|----------|---------|
| DVSEC (CXL) | CXL capabilities, range config |
| HDM Decoder | Maps host PA ranges to device memory |
| CXL.cache/mem enable | Enables sub-protocols |
| IDE capability | Security configuration |
| DOE | Management data exchange |

---

END OF DOCUMENT 06 — CHEATSHEET
