# AXI — FLASHCARDS
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA AXI | Document: 04 of 08
# ════════════════════════════════════════════════════════════════════

---

## DECK 1: FUNDAMENTALS (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | AXI stands for? | Advanced eXtensible Interface |
| 2 | AMBA stands for? | Advanced Microcontroller Bus Architecture |
| 3 | AXI belongs to which family? | ARM AMBA |
| 4 | How many channels in AXI? | 5 (AW, W, B, AR, R) |
| 5 | AXI first version year? | 2003 (AMBA 3) |
| 6 | AXI4 year? | 2010 (AMBA 4) |
| 7 | Is AXI synchronous? | Yes (clock-edge based) |
| 8 | AXI transfer condition? | VALID=1 AND READY=1 same clock edge |
| 9 | Who drives VALID? | Source (master for AW/W/AR, slave for R/B) |
| 10 | Who drives READY? | Destination (slave for AW/W/AR, master for R/B) |
| 11 | Can VALID deassert before transfer? | NO — must hold until READY |
| 12 | Can READY deassert before VALID? | Yes — no obligation to stay high |
| 13 | AXI master examples? | CPU, GPU, DMA controller |
| 14 | AXI slave examples? | Memory controller, peripherals |
| 15 | Full-duplex in AXI? | Yes — separate read/write channels |
| 16 | AXI topology? | Point-to-point (via interconnect/crossbar) |
| 17 | AXI is on-chip or off-chip? | On-chip (inside SoC) |
| 18 | AXI4 max data width? | Architecture allows up to 1024 bits |
| 19 | Common AXI widths? | 32, 64, 128, 256, 512 bits |
| 20 | AXI license cost? | Free (ARM open specification) |

---

## DECK 2: CHANNELS (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | AW channel carries? | Write address + attributes (AWADDR, AWLEN, AWSIZE...) |
| 2 | W channel carries? | Write data + strobes (WDATA, WSTRB, WLAST) |
| 3 | B channel carries? | Write response (BRESP, BID) |
| 4 | AR channel carries? | Read address + attributes (ARADDR, ARLEN, ARSIZE...) |
| 5 | R channel carries? | Read data + response (RDATA, RRESP, RLAST, RID) |
| 6 | B channel direction? | Slave → Master |
| 7 | R channel direction? | Slave → Master |
| 8 | AW channel direction? | Master → Slave |
| 9 | W channel direction? | Master → Slave |
| 10 | AR channel direction? | Master → Slave |
| 11 | Why separate AW and W? | Decouple address and data phases (pipeline) |
| 12 | Why no separate read response channel? | R channel combines data + status (RRESP) |
| 13 | When does slave send BRESP? | After receiving ALL write data (WLAST) |
| 14 | AWVALID/AWREADY belong to? | AW (Write Address) channel |
| 15 | WVALID/WREADY belong to? | W (Write Data) channel |
| 16 | BVALID/BREADY belong to? | B (Write Response) channel |
| 17 | ARVALID/ARREADY belong to? | AR (Read Address) channel |
| 18 | RVALID/RREADY belong to? | R (Read Data) channel |
| 19 | Can AW and AR be active simultaneously? | Yes — independent channels |
| 20 | Can W data arrive before AW address (AXI4)? | No — AXI4 requires AW before/same as first W |

---

## DECK 3: BURSTS (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | AxLEN=0 means? | 1 beat (length = AxLEN + 1) |
| 2 | AxLEN=255 means? | 256 beats |
| 3 | AXI3 max AxLEN? | 15 (16 beats max) |
| 4 | AXI4 max AxLEN? | 255 (256 beats max) |
| 5 | AxSIZE=0 means? | 1 byte per beat |
| 6 | AxSIZE=3 means? | 8 bytes per beat |
| 7 | AxSIZE=6 means? | 64 bytes per beat |
| 8 | FIXED burst (AxBURST=00) use? | FIFO register access |
| 9 | INCR burst (AxBURST=01) use? | Normal sequential memory |
| 10 | WRAP burst (AxBURST=10) use? | Cache line fill (critical word first) |
| 11 | WRAP boundary formula? | AxSIZE × (AxLEN+1) |
| 12 | WRAP burst valid lengths? | 2, 4, 8, 16 beats only |
| 13 | Can INCR burst cross 4KB? | No — must not cross 4KB boundary |
| 14 | Why 4KB limit? | Matches page size; prevents crossing slave boundaries |
| 15 | Total bytes in burst? | (AxLEN+1) × 2^AxSIZE |
| 16 | Narrow transfer? | AxSIZE < data bus width |
| 17 | What indicates last write beat? | WLAST = 1 |
| 18 | What indicates last read beat? | RLAST = 1 |
| 19 | Who counts beats? | Both master and slave independently |
| 20 | Can burst length change mid-burst? | No — set at address phase, fixed for burst |

---

## DECK 4: RESPONSES & ERRORS (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | OKAY (0b00) means? | Normal access success |
| 2 | EXOKAY (0b01) means? | Exclusive access success |
| 3 | SLVERR (0b10) means? | Slave error (device-specific) |
| 4 | DECERR (0b11) means? | Decode error (no slave at address) |
| 5 | Who generates DECERR? | Interconnect (default slave) |
| 6 | Who generates SLVERR? | The addressed slave device |
| 7 | BRESP is per-burst or per-beat? | Per-burst (one response for all W beats) |
| 8 | RRESP is per-burst or per-beat? | Per-beat (each read beat has own response) |
| 9 | Can RRESP differ between beats? | Yes (e.g., first beat OK, second SLVERR) |
| 10 | Exclusive read response? | EXOKAY (monitor set up) or OKAY (no monitor) |
| 11 | Failed exclusive write response? | OKAY (not EXOKAY — write discarded) |
| 12 | Successful exclusive write response? | EXOKAY (write completed atomically) |
| 13 | SLVERR example? | Write to read-only register |
| 14 | DECERR example? | Access address with no mapped slave |
| 15 | What happens to data on SLVERR? | Undefined (slave may or may not have written) |
| 16 | Must slave respond to every transaction? | Yes — even with error response |
| 17 | Can a write complete with SLVERR and partial write? | Implementation-defined |
| 18 | What is a "default slave"? | Interconnect component that returns DECERR for unmapped addresses |
| 19 | BID must match which signal? | AWID of the corresponding write transaction |
| 20 | RID must match which signal? | ARID of the corresponding read transaction |

---

## DECK 5: IDs & ORDERING (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | Purpose of transaction IDs? | Track outstanding, enable out-of-order |
| 2 | Same ID ordering rule? | Responses in same order as requests |
| 3 | Different ID ordering? | No ordering guarantee (out-of-order OK) |
| 4 | AWID purpose? | Identifies write transaction (for response matching) |
| 5 | ARID purpose? | Identifies read transaction (for response matching) |
| 6 | BID matches? | AWID of the write being responded to |
| 7 | RID matches? | ARID of the read being responded to |
| 8 | Typical ID width? | 4-16 bits (implementation-specific) |
| 9 | Why do interconnects widen IDs? | Append source-port bits to distinguish masters |
| 10 | Outstanding transactions? | Multiple requests issued before first response |
| 11 | What limits outstanding depth? | Slave capability + master ID count |
| 12 | Interleaving (AXI3)? | W beats from different IDs can mix |
| 13 | Interleaving (AXI4)? | Removed — W beats must be contiguous |
| 14 | Write ordering to same address, same ID? | Must arrive at slave in issue order |
| 15 | Read ordering to same address, different ID? | No guarantee — out-of-order possible |
| 16 | RAW hazard (same ID)? | Read after write must see write data |
| 17 | RAW hazard (different ID)? | No guarantee — software must use barriers |
| 18 | Maximum simultaneous outstanding (typical)? | 8-32 per master (design parameter) |
| 19 | How does out-of-order help performance? | Fast slaves respond without waiting for slow ones |
| 20 | Response reordering buffer? | Master-side buffer to match responses to requests |

---

## DECK 6: EXCLUSIVE & ATOMIC (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | AxLOCK=1 means? | Exclusive access (AXI4) |
| 2 | AxLOCK=0 means? | Normal access |
| 3 | What is exclusive monitor? | Hardware that tracks exclusive claims |
| 4 | Exclusive read sets up? | Monitor records {address, ID, size} |
| 5 | Exclusive write checks? | If monitor still valid for {address, ID} |
| 6 | What clears exclusive monitor? | Write from another master to same address |
| 7 | EXOKAY on write means? | Exclusive write succeeded (atomic) |
| 8 | OKAY on exclusive write means? | FAILED — another write happened, retry needed |
| 9 | Use case for exclusive? | Spinlocks, atomic counters, mutex |
| 10 | AXI3 locked access? | AxLOCK=0b10 (locked, blocks other masters) — deprecated |
| 11 | Why was locked access removed in AXI4? | Blocks entire bus, terrible for performance |
| 12 | AXI5 AtomicStore? | Hardware add/clear/set at endpoint (no read-back) |
| 13 | AXI5 AtomicLoad? | Add/clear/set at endpoint, returns old value |
| 14 | AXI5 AtomicSwap? | Exchange values at endpoint |
| 15 | AXI5 AtomicCompare? | Compare-and-swap at endpoint |
| 16 | Why AXI5 atomics better than exclusive? | Single transaction, less traffic, no retry loop |
| 17 | Where do AXI5 atomics execute? | At the endpoint (slave/memory controller) |
| 18 | Monitor granularity? | Implementation-defined (often cache-line aligned) |
| 19 | Can exclusive span multiple transactions? | No — one exclusive read + one exclusive write |
| 20 | What if slave doesn't support exclusive? | Returns OKAY (never EXOKAY) — software sees failure always |

---

## DECK 7: CACHE & PROTECTION (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | AxCACHE width? | 4 bits |
| 2 | AxCACHE[0] means? | Bufferable |
| 3 | AxCACHE[1] means? | Modifiable (formerly Cacheable) |
| 4 | AxCACHE[2] means? | Read-Allocate |
| 5 | AxCACHE[3] means? | Write-Allocate |
| 6 | AxCACHE=0b0000? | Device Non-bufferable (strict MMIO) |
| 7 | AxCACHE=0b1111? | Normal Write-Back, Read/Write Allocate |
| 8 | Bufferable means? | Write can be held/merged in buffer |
| 9 | Modifiable means? | Attributes can change through interconnect |
| 10 | Non-bufferable use? | MMIO where write must reach device immediately |
| 11 | AxPROT width? | 3 bits |
| 12 | AxPROT[0]? | Privileged (1) or Unprivileged (0) |
| 13 | AxPROT[1]? | Non-secure (1) or Secure (0) |
| 14 | AxPROT[2]? | Instruction (1) or Data (0) |
| 15 | How does TrustZone use AxPROT? | Interconnect blocks non-secure access to secure regions |
| 16 | AxQOS width? | 4 bits (0-15) |
| 17 | Higher AxQOS means? | Higher priority |
| 18 | AxREGION purpose? | Additional address decode hints (AXI4) |
| 19 | AWUSER/ARUSER? | User-defined sideband signals |
| 20 | Who interprets AxCACHE? | Caches and memory controllers in the path |

---

## DECK 8: AXI4-LITE & VARIANTS (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | AXI4-Lite burst length? | 1 only (no bursts) |
| 2 | AXI4-Lite data width? | 32 or 64 bits only |
| 3 | AXI4-Lite has IDs? | No |
| 4 | AXI4-Lite has QoS? | No |
| 5 | AXI4-Lite has WLAST? | Not needed (always 1 beat, implicitly last) |
| 6 | AXI4-Lite has exclusive? | No |
| 7 | AXI4-Lite use case? | Simple register-mapped peripherals |
| 8 | AXI4-Lite still uses 5 channels? | Yes (same handshake protocol) |
| 9 | AXI-Stream purpose? | Streaming data (no address, continuous flow) |
| 10 | AXI-Stream key signals? | TDATA, TVALID, TREADY, TLAST |
| 11 | AXI-Stream has addresses? | No (point-to-point data pipe) |
| 12 | AXI-Stream use case? | Video pipeline, network data, audio |
| 13 | AXI5-Lite? | AXI5 simplified (like AXI4-Lite but with AXI5 features) |
| 14 | Can AXI4-Lite slave connect to AXI4 master? | Yes, via protocol converter in interconnect |
| 15 | AXI4-Lite signals count? | ~30 (vs ~150 for full AXI4) |
| 16 | AXI4-Lite WSTRB present? | Yes (allows sub-word writes) |
| 17 | Can AXI-Lite handle burst from master? | Interconnect splits burst into single beats |
| 18 | Why use AXI-Lite over APB? | Higher performance (pipelined), standard interface |
| 19 | AXI4 vs AXI4-Lite area? | AXI4-Lite much smaller (simpler logic) |
| 20 | AXI4 supports both full and lite slaves? | Yes, interconnect bridges between them |

---

## DECK 9: INTERCONNECT & SoC (20 cards)

| # | Front | Back |
|---|-------|------|
| 1 | Crossbar topology? | Any master can access any slave simultaneously |
| 2 | What is an arbiter? | Selects which master wins when multiple compete |
| 3 | What is a decoder? | Maps address to destination slave port |
| 4 | Round-robin arbitration? | Each master gets equal turns |
| 5 | Priority arbitration? | Higher priority always wins |
| 6 | What is upsizing? | Narrow-to-wide data width conversion |
| 7 | What is downsizing? | Wide-to-narrow data width conversion |
| 8 | Clock domain crossing uses? | Async FIFO bridge per channel |
| 9 | CDC adds latency? | Yes, 2-4 clock cycles per crossing |
| 10 | Default slave purpose? | Returns DECERR for unmapped addresses |
| 11 | ARM NIC-400? | ARM's configurable AXI interconnect IP |
| 12 | ARM NIC-450/500? | Advanced versions with more features |
| 13 | Typical crossbar size in SoC? | 4×4 to 16×16 master×slave |
| 14 | What is a bridge? | Converts between protocols (AXI→APB) |
| 15 | Register slice? | Pipeline stage in AXI path (cuts timing path) |
| 16 | Register slice effect? | +1 cycle latency, better timing closure |
| 17 | FIFO depth in interconnect? | Trades latency tolerance vs area |
| 18 | What is address remapping? | Interconnect translates addresses between regions |
| 19 | Multi-layer bus? | Multiple parallel shared buses for concurrency |
| 20 | When is NoC better than crossbar? | Large SoCs (>8 masters) where crossbar doesn't scale |

---

END OF DOCUMENT 04 — FLASHCARDS
