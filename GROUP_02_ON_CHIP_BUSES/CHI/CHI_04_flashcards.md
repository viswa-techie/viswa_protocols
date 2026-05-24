# CHI — FLASHCARDS
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA CHI | Document: 04 of 08
# ════════════════════════════════════════════════════════════════════

---

## DECK 1: FUNDAMENTALS (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | CHI stands for? | Coherent Hub Interface |
| 2 | CHI generation? | AMBA 5 |
| 3 | CHI replaced? | ACE for high-end coherent systems |
| 4 | Key innovation over ACE? | Flit-based + scalable + direct transfers |
| 5 | CHI packet unit? | Flit (flow control unit) |
| 6 | Protocol layers? | Protocol, Network, Link |
| 7 | Protocol layer handles? | Transactions, coherence, ordering |
| 8 | Network layer handles? | NodeID routing, packet formation |
| 9 | Link layer handles? | Credit flow control, flit transfer |
| 10 | Topology? | Point-to-point (not shared bus) |
| 11 | Scalability? | 2 to 100+ nodes |
| 12 | Cache line size? | 64 bytes (typical) |
| 13 | SAM? | System Address Map (routing table) |
| 14 | PoC? | Point of Coherence |
| 15 | PoS? | Point of Serialization (at HN-F) |
| 16 | CHI-A release year? | 2014 |
| 17 | CHI spec document? | IHI0050 |
| 18 | CHI-E added? | MPAM, enhanced atomics |
| 19 | Main use case? | Multi-core CPU coherent interconnect |
| 20 | SA8295P CHI role? | CPU cluster to system cache coherent link |

## DECK 2: NODES (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | RN? | Requester Node (initiates transactions) |
| 2 | HN? | Home Node (manages coherence for address) |
| 3 | SN? | Subordinate Node (memory/device) |
| 4 | MN? | Miscellaneous Node (DVM handler) |
| 5 | RN-F? | Fully coherent requester (has cache, snoopable) |
| 6 | RN-I? | I/O coherent requester (no cache, not snooped) |
| 7 | RN-D? | DVM-only requester (receives TLB invalidates) |
| 8 | HN-F? | Fully coherent home (snoop filter + serialization) |
| 9 | HN-I? | I/O home (device/non-coherent regions) |
| 10 | SN-F? | Subordinate with forwarding (memory controller) |
| 11 | SN-I? | Subordinate I/O (peripheral) |
| 12 | Who has snoop filter? | HN-F |
| 13 | Who is snooped? | RN-F only |
| 14 | CPU core is? | RN-F |
| 15 | DMA engine is? | RN-I |
| 16 | DDR controller is? | SN-F |
| 17 | SMMU is? | RN-D |
| 18 | NodeID purpose? | Unique identifier for routing |
| 19 | TgtID? | Target node for request routing |
| 20 | SrcID? | Source node (for responses) |

## DECK 3: CHANNELS & FLITS (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | TXREQ? | Transmit request (RN→HN) |
| 2 | TXRSP? | Transmit response (snoop resp, CompAck) |
| 3 | TXDAT? | Transmit data (write data, snoop data) |
| 4 | RXSNP? | Receive snoop (HN→RN) |
| 5 | RXRSP? | Receive response (completions) |
| 6 | RXDAT? | Receive data (read data) |
| 7 | FLITV? | Flit valid signal |
| 8 | FLIT? | Flit payload (fixed width) |
| 9 | LCRDV? | Link credit valid (credit return) |
| 10 | REQ flit has? | TxnID, Addr, Opcode, SrcID, TgtID |
| 11 | DAT flit has? | TxnID, Data, BE, Resp, HomeNID |
| 12 | SNP flit has? | TxnID, Addr, Opcode, SrcID, FwdNID |
| 13 | RSP flit has? | TxnID, Opcode, Resp, DBID |
| 14 | TxnID purpose? | Identify outstanding transaction |
| 15 | DBID? | Data buffer ID (write credit) |
| 16 | FwdNID? | Forward node ID for DCT |
| 17 | ReturnNID? | Node to return data to (DMT) |
| 18 | Total channels per RN? | 6 (3 TX + 3 RX) |
| 19 | Flit width? | Implementation specific (typically 200-500 bits) |
| 20 | Credit exhaustion? | Transmitter must stop until credits returned |

## DECK 4: COHERENCE (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | I state? | Invalid — line not in cache |
| 2 | SC state? | Shared Clean — may have other copies |
| 3 | UC state? | Unique Clean — only copy, can silently evict |
| 4 | UD state? | Unique Dirty — only copy, must write back |
| 5 | SD state? | Shared Dirty — shared but WB responsibility |
| 6 | ReadShared result? | SC or UC (if no other copies) |
| 7 | ReadUnique result? | UC or UD (all others invalidated) |
| 8 | MakeUnique? | Get ownership without data transfer |
| 9 | SnpShared effect? | Downgrade to SC |
| 10 | SnpUnique effect? | Invalidate (→I) |
| 11 | WriteBack when? | Evicting UD line |
| 12 | CleanInvalid? | CMO: flush dirty + invalidate |
| 13 | Snoop filter tracks? | Which RN-Fs have which lines |
| 14 | Back-invalidation? | Forced evict when snoop filter full |
| 15 | CompData? | Data + completion combined |
| 16 | SnpRespData? | Snoop response with dirty data |
| 17 | UC → SC requires? | Snoop not needed (only 1 copy) |
| 18 | SD → I requires? | SnpUnique + data provided |
| 19 | Silent eviction? | UC/SC can be evicted without telling HN |
| 20 | UD eviction? | MUST WriteBack (dirty data) |

## DECK 5: TRANSACTIONS (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | ReadOnce? | Read without caching |
| 2 | ReadClean? | Read, want clean copy (SC/UC) |
| 3 | ReadShared? | Read, shared OK |
| 4 | ReadUnique? | Read, need exclusive |
| 5 | WriteBackFull? | Evict full dirty line |
| 6 | WriteCleanFull? | Write dirty data but keep clean |
| 7 | WriteUniqueFull? | Write full line (may not have it) |
| 8 | WriteNoSnp? | Write to non-snoopable region |
| 9 | ReadNoSnp? | Read from non-snoopable region |
| 10 | CleanShared? | CMO: make clean but keep |
| 11 | MakeInvalid? | CMO: invalidate without WB (data loss) |
| 12 | AtomicStore? | Atomic op at HN, no return |
| 13 | AtomicLoad? | Atomic op at HN, return old value |
| 14 | AtomicSwap? | Exchange at HN |
| 15 | AtomicCompare? | CAS at HN |
| 16 | DVMOp? | TLB invalidate |
| 17 | CompDBIDResp? | HN grants write buffer + credits |
| 18 | CompAck? | RN acknowledges ordering point |
| 19 | RetryAck? | HN has no resources, retry later |
| 20 | PCrdGrant? | HN grants protocol credit after retry |

## DECK 6: OPTIMIZATIONS & SYSTEM (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | DMT? | Direct Memory Transfer (SN→RN direct) |
| 2 | DCT? | Direct Cache Transfer (RN→RN direct) |
| 3 | DWT? | Direct Write Transfer (RN→SN direct) |
| 4 | DMT benefit? | Reduces HN data path latency |
| 5 | DCT benefit? | Cache-to-cache without memory round-trip |
| 6 | Stash? | Hint to push data into target cache |
| 7 | Prefetch in CHI? | ReadOnce with prefetch hint |
| 8 | MPAM? | Memory Partitioning and Monitoring |
| 9 | Exclusive access? | ReadUnique(Excl) + monitor at HN |
| 10 | Retry mechanism? | RetryAck + PCrdGrant when resources free |
| 11 | Ordering: No order? | Max performance, no guarantees |
| 12 | Ordering: Request order? | Same-addr serialized at HN |
| 13 | Ordering: Endpoint order? | Responses in request order |
| 14 | TxnID reuse? | After transaction fully complete |
| 15 | Max outstanding? | TxnID space (e.g., 256) |
| 16 | QoS in CHI? | QoS field in request flit |
| 17 | System cache role? | HN-F with LLC (reduces SN traffic) |
| 18 | AMBA CHI successor? | CHI continues evolving (CHI-F 2022) |
| 19 | CHI vs AXI? | CHI = coherent interconnect; AXI = master-slave bus |
| 20 | CHI verification? | Protocol checker at each node interface |

---

END OF DOCUMENT 04 — FLASHCARDS (120 cards, 6 decks)
