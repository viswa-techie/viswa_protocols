# CHI — CHEATSHEET
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA CHI | Document: 06 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. NODE TYPE QUICK REFERENCE
| Node | Snoopable | Cache | Role |
|------|-----------|-------|------|
| RN-F | Yes | Yes | CPU, GPU |
| RN-I | No | No | DMA, PCIe |
| RN-D | DVM only | No | SMMU |
| HN-F | — | Snoop filter + LLC | Coherent home |
| HN-I | — | — | Device home |
| SN-F | — | — | Memory controller |
| SN-I | — | — | Device |

## 2. CHANNEL MAP
```
RN → ICN:  TXREQ, TXRSP, TXDAT
ICN → RN:  RXSNP, RXRSP, RXDAT
HN → SN:   REQ, DAT
SN → HN:   RSP, DAT
```

## 3. COHERENCE STATES
| State | Unique? | Dirty? | Can evict silently? |
|-------|---------|--------|---------------------|
| I | — | — | — |
| SC | No | No | Yes |
| UC | Yes | No | Yes |
| UD | Yes | Yes | No (must WB) |
| SD | No | Yes | No (must WB) |

## 4. KEY REQUEST OPCODES
| Opcode | Purpose | Response |
|--------|---------|----------|
| ReadShared | Cache miss, shared OK | CompData(SC/UC) |
| ReadUnique | Need exclusive | CompData(UC/UD) |
| ReadOnce | Non-cacheable read | CompData |
| ReadNoSnp | Non-coherent region | CompData |
| MakeUnique | Upgrade SC→UD | Comp |
| WriteBackFull | Evict dirty 64B | CompDBIDResp |
| WriteUniqueFull | Write, may not have line | CompDBIDResp |
| WriteNoSnp | Non-coherent write | CompDBIDResp |
| CleanInvalid | CMO: flush+invalidate | Comp |

## 5. KEY SNOOP OPCODES
| Snoop | Target State | Data? |
|-------|-------------|-------|
| SnpShared | SC | If dirty |
| SnpUnique | I | If dirty |
| SnpClean | SC | If dirty |
| SnpOnce | No change | Optional |
| SnpSharedFwd | SC (+ forward to Req) | Yes (DCT) |
| SnpUniqueFwd | I (+ forward to Req) | Yes (DCT) |

## 6. KEY RESPONSE OPCODES
| Response | Meaning |
|----------|---------|
| CompData | Completion + data |
| DataSepResp | Data without completion (separate) |
| Comp | Completion without data |
| CompDBIDResp | Completion + data buffer ID (write grant) |
| CompAck | Ordering acknowledgement |
| SnpResp | Snoop response (no data) |
| SnpRespData | Snoop response with data |
| RetryAck | HN busy, retry later |
| PCrdGrant | Protocol credit granted (after retry) |

## 7. TRANSACTION FLOW TEMPLATES

### Read (clean miss):
```
RN→HN: ReadShared → HN→SN: ReadNoSnp → SN→RN: CompData → RN→HN: CompAck
```

### Read (dirty in peer):
```
RN→HN: ReadUnique → HN→Peer: SnpUnique → Peer→HN: SnpRespData → HN→RN: CompData → RN→HN: CompAck
```

### WriteBack:
```
RN→HN: WriteBackFull → HN→RN: CompDBIDResp → RN→HN: CBWrData
```

### Atomic:
```
RN→HN: AtomicLoad → HN executes at PoS → HN→RN: CompData(old value)
```

## 8. CREDIT FLOW CONTROL
```
Rule: FLITV can assert ONLY if credits > 0
Credits_available = Σ(LCRDV received) - Σ(FLITV sent)
Initial credits = 0 (receiver must issue credits after reset)
```

## 9. ORDERING RULES
| Field | Value | Meaning |
|-------|-------|---------|
| Order[1:0]=00 | No order | No ordering guarantee |
| Order[1:0]=01 | Request accepted | ReadReceipt sent |
| Order[1:0]=10 | Request order | Same-addr ordering at HN |
| Order[1:0]=11 | Endpoint order | Ordered completion |

## 10. CHI VERSION FEATURES
| Version | Key Addition |
|---------|-------------|
| CHI-A | Base spec |
| CHI-B | Atomics, WriteZero |
| CHI-C | Enhanced stash, persistent CMO |
| CHI-D | MPAM Phase 1, tag operations |
| CHI-E | MPAM Phase 2, enhanced atomics |
| CHI-F | Further optimizations |

---

END OF DOCUMENT 06 — CHEATSHEET
