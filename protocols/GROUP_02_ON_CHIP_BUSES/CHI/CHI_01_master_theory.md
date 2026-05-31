# CHI — MASTER THEORY
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA CHI | Document: 01 of 08
# ════════════════════════════════════════════════════════════════════

---

## §1: WHAT IS CHI

CHI (Coherent Hub Interface) is ARM's scalable interconnect protocol for multi-core SoCs. It replaces ACE for high-end systems. Key characteristics:
- **Flit-based** (not channel-based like AXI): Packets called "flits" carry transaction info
- **Layered architecture**: Protocol layer, Network layer, Link layer
- **Point-to-point**: Each node connects to interconnect via dedicated links
- **Scalable**: Works from 2-core to 100+ core systems
- **Full coherence**: Hardware cache coherence across all participating caches

---

## §2: NODE TYPES

### Requester Nodes (RN)
| Type | Description | Example |
|------|-------------|---------|
| RN-F | Fully coherent, has cache, snoopable | CPU core, GPU |
| RN-I | I/O coherent, no cache, issues coherent requests | DMA, PCIe RC |
| RN-D | DVM only, receives TLB invalidates | SMMU |

### Home Nodes (HN)
| Type | Description |
|------|-------------|
| HN-F | Fully coherent home, has snoop filter/directory, serializes coherent requests |
| HN-I | I/O home, non-coherent, for device memory regions |

### Subordinate Nodes (SN)
| Type | Description |
|------|-------------|
| SN-F | Memory controller, can respond to forwarded requests |
| SN-I | I/O subordinate (peripheral) |

### Miscellaneous Nodes (MN)
- Handles DVM operations (TLB invalidates)

---

## §3: CHANNEL ARCHITECTURE

CHI has 4 logical channels per direction:

| Channel | Direction (RN view) | Purpose |
|---------|---------------------|---------|
| TXREQ | RN → HN | Requests (read, write, atomics) |
| TXRSP | RN → HN | Responses to snoops |
| TXDAT | RN → HN/SN | Data (write data, snoop data) |
| RXRSP | HN → RN | Completion responses |
| RXDAT | HN/SN → RN | Read data |
| RXSNP | HN → RN | Snoop requests |

Total: 3 TX channels (REQ, RSP, DAT) + 3 RX channels (RSP, DAT, SNP) per node.

---

## §4: FLIT FORMAT

Each channel carries "flits" (flow control units). A flit is a fixed-width packet:

### Request Flit Fields (key)
| Field | Bits | Description |
|-------|------|-------------|
| TxnID | 8-12 | Transaction identifier |
| Addr | 44-52 | Target address |
| Opcode | 6 | Operation type |
| SrcID | node | Source node ID |
| TgtID | node | Target node ID |
| Size | 3 | Data size (1B-64B) |
| MemAttr | 4 | Memory attributes (cacheable, etc.) |
| SnpAttr | 1 | Snoopable |
| Order | 2 | Ordering requirement |

### Data Flit Fields (key)
| Field | Description |
|-------|-------------|
| TxnID | Links to request |
| HomeNID | Node ID of home |
| Opcode | Data type (CompData, SnpRespData, etc.) |
| Data | 128/256/512 bits per flit |
| BE | Byte enable |
| Resp | Cache state info |

---

## §5: COHERENCE STATES

CHI uses an extended MOESI model:

| State | Symbol | Meaning |
|-------|--------|---------|
| Invalid | I | Line not in cache |
| Shared Clean | SC | Shared, not dirty |
| Unique Clean | UC | Only copy, clean |
| Unique Dirty | UD | Only copy, dirty (must writeback) |
| Shared Dirty | SD | Shared, this copy responsible for writeback |

Transitions managed by HN-F through snoop/request protocol.

---

## §6: READ TRANSACTIONS

### ReadShared (cache miss, shared OK)
```
RN-F → HN-F:  ReadShared(addr)
HN-F checks snoop filter:
  Case A: No other copy → HN-F → SN-F: ReadNoSnp
                           SN-F → RN-F: CompData(SC or UC)
  Case B: Dirty in RN-X → HN-F → RN-X: SnpShared
                           RN-X → HN-F: SnpRespData(SC)
                           HN-F → RN-F: CompData(SC)
RN-F → HN-F:  CompAck
```

### ReadUnique (cache miss, need exclusive)
```
RN-F → HN-F:  ReadUnique(addr)
HN-F → all sharers: SnpUnique (invalidate all copies)
  Sharers → HN-F: SnpResp(I) [or SnpRespData if dirty]
HN-F → RN-F:  CompData(UD or UC)
RN-F → HN-F:  CompAck
```

---

## §7: WRITE TRANSACTIONS

### WriteBack (dirty eviction)
```
RN-F → HN-F:  WriteBackFull(addr)
HN-F → RN-F:  CompDBIDResp (allocate buffer, grant data credit)
RN-F → HN-F:  CBWrData (64B cache line)
```

### WriteUnique (write, don't have line)
```
RN-F → HN-F:  WriteUniqueFull(addr)
HN-F: Snoops + allocates → when all invalidated:
HN-F → RN-F:  CompDBIDResp
RN-F → HN-F:  NCBWrData
```

---

## §8: SNOOP TRANSACTIONS

Snoops flow from HN-F to RN-F(s) via SNP channel:

| Snoop | Purpose | Expected Response |
|-------|---------|-------------------|
| SnpShared | Downgrade to SC | SnpResp(SC) or SnpRespData |
| SnpUnique | Invalidate | SnpResp(I) or SnpRespData(I) |
| SnpClean | Make clean | SnpResp(SC/I) |
| SnpOnce | Read without state change | SnpResp + optional data |
| SnpCleanInvalid | Clean + invalidate | SnpResp(I) |

---

## §9: DIRECT TRANSFERS (DMT/DCT/DWT)

### DMT (Direct Memory Transfer)
- SN sends CompData directly to RN (skips HN data path)
- HN sends ReturnNID/ReturnTxnID in request to SN

### DCT (Direct Cache Transfer)
- Snooped RN-F sends data directly to requesting RN-F
- Reduces latency (peer-to-peer)
- HN coordinates via FwdNID in snoop

### DWT (Direct Write Transfer)
- RN sends write data directly to SN
- HN provides DBIDResp with SN as target

---

## §10: FLOW CONTROL (LINK LAYER)

CHI uses credit-based flow control:
- Receiver advertises available buffer slots ("L-Credits")
- Transmitter can only send if it has credits
- One credit consumed per flit sent
- Credits returned when receiver processes flit

```
L-Credit flow:
  Receiver → Transmitter: LCRDV=1 (one credit available)
  Transmitter → Receiver: FLITV=1 (send one flit, consume one credit)
```

No flit can be sent without credit → prevents receiver overflow.

---

## §11: TRANSACTION ID AND ORDERING

- **TxnID**: Unique per source node, identifies outstanding transactions
- Max outstanding = TxnID space (e.g., 256 with 8-bit TxnID)
- **Ordering**: 
  - No ordering (default): max performance
  - Request order: same-address requests serialized at HN
  - Endpoint order: responses in request order

---

## §12: CACHE MAINTENANCE OPERATIONS (CMO)

| Operation | Effect |
|-----------|--------|
| CleanShared | Write dirty data to memory, keep shared |
| CleanInvalid | Write dirty data, invalidate all copies |
| MakeInvalid | Invalidate without writeback (data loss!) |
| CleanSharedPersist | Ensure data reaches persistence point |

---

## §13: ATOMIC OPERATIONS (CHI-B+)

| Type | Operations |
|------|-----------|
| AtomicStore | ADD, CLR, SET, EOR, SMAX, SMIN, UMAX, UMIN |
| AtomicLoad | Same ops, returns old value |
| AtomicSwap | Exchange value |
| AtomicCompare | Compare-and-swap |

Executed at HN (near-memory) → avoids coherence round-trips.

---

## §14: EXCLUSIVE ACCESS

For lock-free synchronization:
```
1. RN: ReadUnique(addr, Excl=1) → gets UD, HN records exclusive monitor
2. RN: modifies data locally
3. RN: CleanUnique(addr, Excl=1) → HN checks monitor
   - Pass: CompExclOK → RN writes back
   - Fail: Comp (non-exclusive) → RN retries
```

---

## §15: SYSTEM ADDRESS MAP (SAM)

Determines routing:
- Which HN owns each address region
- Whether address is snoopable (Normal) or device (Device)
- Programmed at boot, static during operation

---

## §16: LAYERED ARCHITECTURE

```
┌─────────────────────────┐
│   Protocol Layer        │  Transactions, coherence, ordering
├─────────────────────────┤
│   Network Layer         │  NodeID routing, packet assembly
├─────────────────────────┤
│   Link Layer            │  Credits, flit transfer, flow control
├─────────────────────────┤
│   Physical (impl.)      │  Wires, CDC, timing
└─────────────────────────┘
```

---

## §17: CHI IN QUALCOMM SA8295P

- **CPU clusters**: Kryo cores connected via CHI to system cache (HN-F role)
- **Coherent interconnect**: Qualcomm custom interconnect implementing CHI
- **System cache**: Acts as HN-F with snoop filter + LLC
- **Non-coherent masters**: Connected as RN-I (GPU compute, DMA)
- **Memory controllers**: SN-F role

---

## §18: CHI vs ACE COMPARISON

| Feature | ACE | CHI |
|---------|-----|-----|
| Topology | Shared bus/crossbar | Point-to-point mesh |
| Packet type | Channel signals | Flits |
| Scalability | ~4-8 masters | 100+ nodes |
| Flow control | AXI READY/VALID | Credit-based |
| Directory | Optional | Required (HN-F) |
| Atomics | No (use exclusive) | Yes (near-memory) |
| Direct transfer | No | DMT/DCT/DWT |
| Spec complexity | Medium | High |

---

## §19: PERFORMANCE FEATURES

1. **Outstanding transactions**: Hundreds per node (TxnID width)
2. **DCT**: Cache-to-cache transfer without memory roundtrip
3. **DMT**: Memory direct to requester, bypass HN data path
4. **Atomic ops**: Execute at HN, avoid coherence traffic
5. **Snoop filter**: Only snoop nodes that actually have the line
6. **Prefetch**: ReadOnce with prefetch hint

---

## §20: PROTOCOL ORDERING RULES

1. Same-address requests: Serialized at HN-F (Point of Serialization)
2. CompAck: Required for requests needing ordering guarantee
3. ReadReceipt: For ordered reads, confirms request accepted
4. Transaction ID reuse: Only after completion acknowledged

---

END OF DOCUMENT 01 — MASTER THEORY
