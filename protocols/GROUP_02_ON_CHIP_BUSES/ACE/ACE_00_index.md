# ACE (AXI Coherency Extensions) — COMPLETE ENCYCLOPEDIA
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA ACE | Documents: 00-08 Combined
# ════════════════════════════════════════════════════════════════════

---

# DOCUMENT 00: INDEX

## PROTOCOL IDENTITY
| Field | Value |
|-------|-------|
| Full Name | AMBA 4 AXI Coherency Extensions |
| Family | AMBA |
| Spec | IHI0022E (Part of AXI4 spec) |
| Creator | ARM Ltd. |
| Type | Cache coherent on-chip interconnect |
| Key Feature | Extends AXI4 with snoop channels for hardware coherence |
| Variants | ACE (full), ACE-Lite (I/O coherent, no snoops received) |
| SA8295P Usage | Coherent masters connecting to CCI/interconnect |
| Successor | CHI (for larger systems) |

## 50 KEY TERMS
| # | Term | Brief |
|---|------|-------|
| 1 | ACE | AXI Coherency Extensions |
| 2 | ACE-Lite | Simplified ACE (no snoop receipt) |
| 3 | Snoop channel | AC (address), CR (response), CD (data) |
| 4 | AC | Snoop address channel |
| 5 | CR | Snoop response channel |
| 6 | CD | Snoop data channel |
| 7 | ARSNOOP | Read snoop type |
| 8 | AWSNOOP | Write snoop type |
| 9 | ARDOMAIN | Read shareability domain |
| 10 | AWDOMAIN | Write shareability domain |
| 11 | RACK | Read acknowledge |
| 12 | WACK | Write acknowledge |
| 13 | ReadOnce | Non-allocating coherent read |
| 14 | ReadShared | Read, shared OK |
| 15 | ReadClean | Read, clean copy only |
| 16 | ReadUnique | Read, exclusive |
| 17 | MakeUnique | Upgrade to exclusive (no data) |
| 18 | CleanUnique | Clean + get unique |
| 19 | MakeInvalid | Invalidate without writeback |
| 20 | WriteBack | Dirty line eviction |
| 21 | WriteClean | Write dirty without invalidate |
| 22 | Evict | Clean line eviction notification |
| 23 | WriteUnique | Write full line, not in cache |
| 24 | WriteLineUnique | Write full cache line |
| 25 | CRRESP | Snoop response bits |
| 26 | PassDirty | Snoop resp: passing dirty responsibility |
| 27 | IsShared | Snoop resp: keeping shared copy |
| 28 | DataTransfer | Snoop resp: providing data |
| 29 | WasUnique | Snoop resp: had unique copy |
| 30 | MOESI | Modified/Owned/Exclusive/Shared/Invalid |
| 31 | Inner domain | Cluster-level shareability |
| 32 | Outer domain | System-level shareability |
| 33 | Non-shareable | Not coherent |
| 34 | System domain | Full system (barrier) |
| 35 | DVM | Distributed Virtual Memory |
| 36 | Barrier | Ordering enforcement |
| 37 | CCI | Cache Coherent Interconnect (ARM IP) |
| 38 | CCN | Cache Coherent Network (ARM IP) |
| 39 | Snoop filter | Tracks cached copies |
| 40 | Shareable | Memory region requiring coherence |
| 41 | Cache state | I/SC/UC/UD/SD |
| 42 | Read allocate | Read that allocates in cache |
| 43 | Write allocate | Write that allocates in cache |
| 44 | Line fill | Fetching full cache line |
| 45 | BRESP | Write response with pass dirty |
| 46 | RRESP | Read response with cache state info |
| 47 | Clean | Data matches memory |
| 48 | Dirty | Data modified, memory stale |
| 49 | Unique | Only cached copy |
| 50 | Shared | Multiple cached copies may exist |

---

# DOCUMENT 01: MASTER THEORY

## §1: WHAT IS ACE
ACE adds coherency to AXI4. It keeps all 5 AXI channels (AR, R, AW, W, B) and adds 3 snoop channels:
- **AC** (Snoop Address): Interconnect sends snoop address to master
- **CR** (Snoop Response): Master responds with cache state info
- **CD** (Snoop Data): Master provides dirty data if needed

Total: 8 channels per ACE master (5 AXI + 3 snoop).

## §2: ACE vs ACE-LITE

| Feature | ACE (Full) | ACE-Lite |
|---------|-----------|----------|
| Issues coherent reads/writes | Yes | Yes |
| Receives snoops | Yes | No |
| Has cache | Yes | No (or non-coherent) |
| Snoop channels (AC/CR/CD) | Yes | No |
| RACK/WACK | Yes | No |
| Use case | CPU cores | DMA, GPU, accelerators |

## §3: SHAREABILITY DOMAINS
ARDOMAIN/AWDOMAIN[1:0]:
| Value | Domain | Scope |
|-------|--------|-------|
| 00 | Non-shareable | No coherence needed |
| 01 | Inner shareable | Within cluster |
| 10 | Outer shareable | Between clusters |
| 11 | System | Full system (barriers) |

## §4: READ OPERATIONS (ARSNOOP)
| ARSNOOP | Name | Effect |
|---------|------|--------|
| 0000 | ReadOnce | Read without allocate |
| 0001 | ReadShared | Allocate, shared OK |
| 0010 | ReadClean | Allocate, want clean |
| 0111 | ReadUnique | Allocate, want exclusive |
| 1011 | CleanUnique | Make unique (no data needed) |
| 1100 | MakeUnique | Invalidate others (no data) |

## §5: WRITE OPERATIONS (AWSNOOP)
| AWSNOOP | Name | Effect |
|---------|------|--------|
| 000 | WriteUnique | Write, don't have line |
| 001 | WriteLineUnique | Write full line, don't have |
| 010 | WriteClean | Write dirty, keep clean |
| 011 | WriteBack | Dirty eviction |
| 100 | Evict | Clean eviction notification |
| 101 | WriteEvict | Clean line eviction with data |

## §6: SNOOP OPERATIONS (on AC channel)
| ACSNOOP | Name | Requested action |
|---------|------|------------------|
| 0000 | ReadOnce | Return data, no state change |
| 0001 | ReadShared | Downgrade to SC |
| 0010 | ReadClean | Return clean data |
| 0111 | ReadUnique | Invalidate, return data |
| 1000 | CleanShared | Make clean |
| 1001 | CleanInvalid | Clean + invalidate |
| 1101 | MakeInvalid | Invalidate (may lose data) |
| 1110 | DVM Complete | DVM acknowledgement |
| 1111 | DVM Message | TLB invalidate |

## §7: SNOOP RESPONSE (CRRESP[4:0])
| Bit | Name | Meaning when 1 |
|-----|------|----------------|
| [0] | DataTransfer | Data provided on CD |
| [1] | Error | Error occurred |
| [2] | PassDirty | Passing dirty responsibility |
| [3] | IsShared | Keeping a shared copy |
| [4] | WasUnique | Had unique (exclusive) copy |

## §8: COHERENCE STATES (same as CHI)
| State | Unique | Dirty | Meaning |
|-------|--------|-------|---------|
| I | — | — | Invalid |
| SC | No | No | Shared Clean |
| UC | Yes | No | Unique Clean |
| UD | Yes | Yes | Unique Dirty (Modified) |
| SD | No | Yes | Shared Dirty (Owned) |

## §9: READ TRANSACTION FLOW

### ReadShared (miss, clean in memory):
```
Master → ICN:     AR (ReadShared, addr)
ICN checks snoop filter, no copies:
  ICN → Memory:  Read request
  Memory → ICN:  Data
ICN → Master:    R (data, RRESP=shared_clean or unique_clean)
Master → ICN:    RACK (acknowledge)
```

### ReadUnique (hit in another cache, dirty):
```
Master₀ → ICN:    AR (ReadUnique, addr)
ICN → Master₁:   AC (ReadUnique snoop, addr)
Master₁ → ICN:   CR (DataTransfer=1, PassDirty=1) + CD (dirty data)
ICN → Master₀:   R (data, RRESP=unique_dirty)
Master₀ → ICN:   RACK
```

## §10: BARRIERS
ACE supports memory barriers through special transactions:
- **ReadBarrier**: on AR channel (ARBAR[1:0])
- **WriteBarrier**: on AW channel (AWBAR[1:0])
- Barrier types: Memory barrier, Synchronization barrier

## §11: DVM (Distributed Virtual Memory)
For TLB maintenance broadcasts:
```
Master₀ → ICN:   AR (DVM message, TLBI info)
ICN → All masters: AC (DVM Message)
Masters → ICN:    CR (DVM Complete)
ICN → Master₀:   R (DVM completion)
```

## §12: ACE IN SA8295P
- CPU cores: ACE full interface to coherent interconnect
- GPU compute: ACE-Lite (issues coherent requests, not snooped)
- DMA: ACE-Lite
- Interconnect: Qualcomm custom (CCI-equivalent role)
- System cache: Integrated in coherent interconnect fabric

---

# DOCUMENT 02: DIAGRAMS

## ACE Interface Channels
```
        ACE Master (CPU Core)
    ┌─────────────────────────────┐
    │                             │
    │  AR ─────────────────────►  │  Read Address
    │  R  ◄─────────────────────  │  Read Data
    │  AW ─────────────────────►  │  Write Address
    │  W  ─────────────────────►  │  Write Data
    │  B  ◄─────────────────────  │  Write Response
    │                             │
    │  AC ◄─────────────────────  │  Snoop Address (from ICN)
    │  CR ─────────────────────►  │  Snoop Response (to ICN)
    │  CD ─────────────────────►  │  Snoop Data (to ICN)
    │                             │
    │  RACK ───────────────────►  │  Read Acknowledge
    │  WACK ───────────────────►  │  Write Acknowledge
    │                             │
    └─────────────────────────────┘
```

## ACE System Architecture
```
┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐
│  CPU₀    │  │  CPU₁    │  │  GPU     │  │  DMA     │
│  (ACE)   │  │  (ACE)   │  │(ACE-Lite)│  │(ACE-Lite)│
│ 8 chan   │  │ 8 chan   │  │ 5 chan   │  │ 5 chan   │
└────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘
     │              │              │              │
┌────┴──────────────┴──────────────┴──────────────┴────┐
│         COHERENT INTERCONNECT (CCI/CCN)               │
│    ┌─────────────────────────────────────┐           │
│    │         Snoop Filter / Directory     │           │
│    └─────────────────────────────────────┘           │
└──────────────┬──────────────────────┬────────────────┘
               │                      │
         ┌─────┴─────┐         ┌─────┴─────┐
         │  DDR MC   │         │  DDR MC   │
         │  (AXI)    │         │  (AXI)    │
         └───────────┘         └───────────┘
```

## Snoop Transaction Flow (ReadUnique)
```
  CPU₀ (requester)     Interconnect       CPU₁ (has UD)
       │                    │                    │
  AR   │──ReadUnique───────►│                    │
       │                    │──AC(ReadUnique)───►│
       │                    │                    │
       │                    │◄──CR(DataXfer=1)──│
       │                    │◄──CD(dirty data)──│
       │                    │                    │
  R    │◄──Data(UD)─────────│                    │
       │                    │                    │
  RACK │──Acknowledge──────►│                    │
       │                    │                    │
```

---

# DOCUMENT 03: INTERVIEW QUESTIONS

## JUNIOR (5)
**Q1**: What channels does ACE add to AXI4? **A:** AC (snoop address), CR (snoop response), CD (snoop data) + RACK/WACK.
**Q2**: What's ACE-Lite? **A:** Simplified ACE without snoop channels — can issue coherent requests but not receive snoops (for DMA/GPU).
**Q3**: What do ARDOMAIN/AWDOMAIN encode? **A:** Shareability domain: non-shareable, inner, outer, system.
**Q4**: How does a master report snoop results? **A:** CRRESP[4:0] bits: DataTransfer, Error, PassDirty, IsShared, WasUnique.
**Q5**: What's RACK? **A:** Read acknowledge — master signals it has committed read data to its cache.

## MID (5)
**Q6**: Explain PassDirty in snoop response. **A:** Snooped master transfers dirty (writeback) responsibility to the requester or interconnect. The receiving party must eventually write back to memory.
**Q7**: How does ACE handle a write to a shared line? **A:** Master issues MakeUnique (or CleanUnique). Interconnect snoops all sharers with MakeInvalid. After all respond with IsShared=0, master gets exclusive access and can write.
**Q8**: What's the difference between WriteBack and Evict? **A:** WriteBack evicts a dirty line (provides data). Evict notifies eviction of a clean line (no data) — interconnect updates snoop filter.
**Q9**: How do barriers work in ACE? **A:** ARBAR/AWBAR signal barrier type. All transactions before barrier must complete (RACK/WACK received) before transactions after barrier are visible.
**Q10**: When would you use ACE vs CHI? **A:** ACE for ≤4-8 coherent masters (simpler, channel-based). CHI for larger systems (scalable, flit-based, direct transfers).

## SENIOR (3)
**Q11**: Design a snoop filter for a 4-core ACE system with 32KB L1 each. How many entries needed? What happens on eviction?
**Q12**: Explain deadlock avoidance in ACE — why can't a master delay snoop response indefinitely?
**Q13**: How does ACE handle memory ordering for DMA (ACE-Lite) accessing memory that CPUs also access?

## QUICK-FIRE (10)
| # | Q | A |
|---|---|---|
| 1 | ACE total channels? | 8 (5 AXI + 3 snoop) |
| 2 | ACE-Lite channels? | 5 (standard AXI + domain/snoop signals on AR/AW) |
| 3 | Snoop address channel? | AC |
| 4 | Snoop response channel? | CR |
| 5 | Snoop data channel? | CD |
| 6 | CRRESP bit for data? | [0] DataTransfer |
| 7 | Full ACE spec? | IHI0022E |
| 8 | ARM interconnect for ACE? | CCI-400/500 |
| 9 | DVM purpose? | TLB invalidate broadcast |
| 10 | Inner shareable value? | ARDOMAIN=01 |

---

# DOCUMENT 04: FLASHCARDS (60 cards, 3 decks)

## DECK 1: SIGNALS & CHANNELS (20)
| # | Front | Back |
|---|-------|------|
| 1 | ACE stands for? | AXI Coherency Extensions |
| 2 | Channels added by ACE? | AC, CR, CD (+ RACK, WACK) |
| 3 | AC channel direction? | Interconnect → Master (snoop address) |
| 4 | CR channel direction? | Master → Interconnect (snoop response) |
| 5 | CD channel direction? | Master → Interconnect (snoop data) |
| 6 | RACK purpose? | Read acknowledge (ordering completion) |
| 7 | WACK purpose? | Write acknowledge (ordering completion) |
| 8 | ARSNOOP width? | 4 bits |
| 9 | AWSNOOP width? | 3 bits |
| 10 | CRRESP width? | 5 bits |
| 11 | ARDOMAIN width? | 2 bits |
| 12 | AWDOMAIN width? | 2 bits |
| 13 | ARBAR purpose? | Read barrier indication |
| 14 | AWBAR purpose? | Write barrier indication |
| 15 | CD data width? | Same as read data width |
| 16 | CDLAST? | Last beat of snoop data |
| 17 | ACVALID/ACREADY? | Snoop address handshake |
| 18 | CRVALID/CRREADY? | Snoop response handshake |
| 19 | CDVALID/CDREADY? | Snoop data handshake |
| 20 | ACE-Lite has AC/CR/CD? | No |

## DECK 2: TRANSACTIONS (20)
| # | Front | Back |
|---|-------|------|
| 1 | ReadShared? | Read, shared OK (ARSNOOP=0001) |
| 2 | ReadUnique? | Read, need exclusive (ARSNOOP=0111) |
| 3 | ReadOnce? | Non-allocating read (ARSNOOP=0000) |
| 4 | ReadClean? | Read, want clean (ARSNOOP=0010) |
| 5 | MakeUnique? | Invalidate others, no data (ARSNOOP=1100) |
| 6 | CleanUnique? | Make unique (ARSNOOP=1011) |
| 7 | WriteBack? | Evict dirty line (AWSNOOP=011) |
| 8 | WriteClean? | Write dirty, keep clean (AWSNOOP=010) |
| 9 | WriteUnique? | Write partial, don't have line (AWSNOOP=000) |
| 10 | WriteLineUnique? | Write full line (AWSNOOP=001) |
| 11 | Evict? | Notify clean eviction (AWSNOOP=100) |
| 12 | PassDirty bit? | CRRESP[2] — passing WB responsibility |
| 13 | IsShared bit? | CRRESP[3] — retaining shared copy |
| 14 | DataTransfer bit? | CRRESP[0] — providing data on CD |
| 15 | WasUnique bit? | CRRESP[4] — had exclusive copy |
| 16 | RRESP[3:2] for UD? | Unique + Dirty indication |
| 17 | When is RACK sent? | After master commits read data |
| 18 | When is WACK sent? | After master sees write response |
| 19 | DVM on which channel? | AR (special ARSNOOP) |
| 20 | Barrier on which channel? | AR (ARBAR) and AW (AWBAR) |

## DECK 3: SYSTEM (20)
| # | Front | Back |
|---|-------|------|
| 1 | Non-shareable domain? | 00 (no coherence) |
| 2 | Inner shareable? | 01 (within cluster) |
| 3 | Outer shareable? | 10 (between clusters) |
| 4 | System domain? | 11 (barriers) |
| 5 | CCI-400? | ARM Cache Coherent Interconnect (4 ACE ports) |
| 6 | CCI-500? | ARM CCI with more ports + snoop filter |
| 7 | ACE successor? | CHI |
| 8 | ACE max practical masters? | ~4-8 (broadcast snooping) |
| 9 | CHI advantage over ACE? | Scalable, directory-based, direct transfers |
| 10 | ACE spec part of? | AXI4 spec (IHI0022E) |
| 11 | Snoop filter tracks? | Which masters cache which lines |
| 12 | Without snoop filter? | Broadcast snoops to all ACE masters |
| 13 | ACE coherence model? | MOESI (5-state) |
| 14 | ACE ordering via? | RACK/WACK + barriers |
| 15 | DVM use case? | TLB invalidate after page table change |
| 16 | ACE clock? | Same as AXI (ACLK) |
| 17 | Can ACE+ACE-Lite share ICN? | Yes (CCI supports both) |
| 18 | ACE for GPU? | Usually ACE-Lite (issues coherent, not snooped) |
| 19 | ACE deadlock avoidance? | Must respond to snoops without waiting for own transactions |
| 20 | ACE verification? | Protocol checker for all 8 channels |

---

# DOCUMENT 06: CHEATSHEET

## ARSNOOP ENCODING
| [3:0] | Name | Allocate? | Snoop? |
|-------|------|-----------|--------|
| 0000 | ReadOnce | No | Yes |
| 0001 | ReadShared | Yes | Yes |
| 0010 | ReadClean | Yes | Yes |
| 0111 | ReadUnique | Yes | Yes |
| 1011 | CleanUnique | — | Yes |
| 1100 | MakeUnique | — | Yes |

## AWSNOOP ENCODING  
| [2:0] | Name |
|-------|------|
| 000 | WriteUnique |
| 001 | WriteLineUnique |
| 010 | WriteClean |
| 011 | WriteBack |
| 100 | Evict |
| 101 | WriteEvict |

## CRRESP ENCODING
| Bit | Name | 1=yes |
|-----|------|-------|
| [0] | DataTransfer | Providing data on CD |
| [1] | Error | Error occurred |
| [2] | PassDirty | Passing dirty responsibility |
| [3] | IsShared | Retaining shared copy |
| [4] | WasUnique | Had unique (full line known) |

## ARDOMAIN/AWDOMAIN
| [1:0] | Domain |
|--------|--------|
| 00 | Non-shareable |
| 01 | Inner shareable |
| 10 | Outer shareable |
| 11 | System |

---

# DOCUMENT 07: LABS & DEBUG

## Common ACE Issues

**1. Snoop Response Deadlock**: Master blocks snoop response waiting for own read completion → Interconnect waiting for snoop to complete read. **Fix**: ACE requires snoop responses never depend on outstanding requests.

**2. Missing RACK/WACK**: Interconnect can't free resources. **Fix**: Always send RACK after read data committed, WACK after BRESP received.

**3. Wrong Domain**: DMA issues Non-shareable write to memory CPUs have cached. **Fix**: Program DMA for Inner/Outer shareable domain.

**4. Stale Data After DVM**: TLB invalidate broadcast incomplete before new mapping used. **Fix**: Ensure DVM completion received before proceeding.

---

# DOCUMENT 08: REFERENCES

## Specifications
| Doc | ID |
|-----|----|
| AMBA AXI and ACE Protocol | IHI0022E/F |
| ARM CCI-400 TRM | DDI0470 |
| ARM CCI-500 TRM | — |

## Glossary (15 terms)
| Term | Definition |
|------|------------|
| ACE | AXI Coherency Extensions |
| ACE-Lite | I/O coherent (no snoops received) |
| AC | Snoop address channel |
| CR | Snoop response channel |
| CD | Snoop data channel |
| RACK | Read acknowledge |
| WACK | Write acknowledge |
| CCI | Cache Coherent Interconnect |
| DVM | Distributed Virtual Memory |
| CRRESP | Snoop response encoding (5 bits) |
| PassDirty | Transfer dirty responsibility |
| IsShared | Retain shared copy |
| Barrier | Memory ordering enforcement |
| Domain | Shareability scope (inner/outer/system) |
| Snoop filter | Directory tracking cached line locations |

---

END — ACE ENCYCLOPEDIA COMPLETE (8/8 combined)
