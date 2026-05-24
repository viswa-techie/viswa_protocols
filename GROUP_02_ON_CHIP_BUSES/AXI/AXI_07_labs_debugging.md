# AXI — LABS & DEBUGGING
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA AXI | Document: 07 of 08
# ════════════════════════════════════════════════════════════════════

---

## LAB 1: AXI WRITE TRANSACTION (RTL Simulation)

### Objective
Simulate a 4-beat INCR write burst on a 64-bit AXI4 bus.

### Setup (SystemVerilog)
```systemverilog
// AXI4 Write — Master drives AW + W, slave responds B
task axi_write_burst(input [31:0] addr, input [3:0] len);
  // Address phase
  @(posedge clk);
  AWADDR  <= addr;
  AWLEN   <= len;          // 4 beats: len=3
  AWSIZE  <= 3'b011;       // 8 bytes/beat (64-bit)
  AWBURST <= 2'b01;        // INCR
  AWVALID <= 1;
  
  wait(AWREADY);
  @(posedge clk);
  AWVALID <= 0;
  
  // Data phase (4 beats)
  for (int i = 0; i <= len; i++) begin
    @(posedge clk);
    WDATA  <= 64'hDEAD_0000 + i;
    WSTRB  <= 8'hFF;        // All bytes valid
    WLAST  <= (i == len);   // Last beat
    WVALID <= 1;
    wait(WREADY);
  end
  @(posedge clk);
  WVALID <= 0;
  
  // Response phase
  BREADY <= 1;
  wait(BVALID);
  assert(BRESP == 2'b00) else $error("Write error!");
  @(posedge clk);
  BREADY <= 0;
endtask
```

### Verification Checks
- [ ] AWVALID held until AWREADY
- [ ] Exactly 4 W beats sent (matching AWLEN+1)
- [ ] WLAST asserted only on beat 4
- [ ] WSTRB correct for each beat
- [ ] BRESP = OKAY received

---

## LAB 2: AXI READ WITH OUT-OF-ORDER RESPONSE

### Objective
Issue 3 reads with different IDs, verify out-of-order response handling.

```systemverilog
// Issue 3 reads to different-speed slaves
fork
  axi_read(32'h0000_1000, 4'h1, 4'd0);  // ID=1, slow DDR
  axi_read(32'h8000_0000, 4'h2, 4'd0);  // ID=2, fast SRAM
  axi_read(32'h0000_2000, 4'h1, 4'd0);  // ID=1, slow DDR
join_none

// Expected response order:
// R (ID=2) first — fast SRAM
// R (ID=1, addr 0x1000) second — slow DDR
// R (ID=1, addr 0x2000) third — same ID, must be in-order
```

### Verification
- [ ] ID=2 response arrives before ID=1 (out-of-order between IDs)
- [ ] Both ID=1 responses arrive in issue order (in-order within same ID)
- [ ] RID matches ARID for each response

---

## LAB 3: EXCLUSIVE ACCESS (Spinlock)

### Objective
Simulate two masters competing for an exclusive lock.

```systemverilog
// Master 0: Acquire lock
task acquire_lock(input [31:0] lock_addr);
  logic [63:0] rdata;
  logic [1:0] resp;
  
  do begin
    // Exclusive read
    axi_exclusive_read(lock_addr, rdata, resp);
    if (rdata == 0) begin  // Lock is free
      // Exclusive write (claim it)
      axi_exclusive_write(lock_addr, 64'h1, resp);
    end else begin
      resp = 2'b00;  // Not EXOKAY, retry
    end
  end while (resp != 2'b01);  // Loop until EXOKAY
  
  $display("Lock acquired!");
endtask
```

### Test Scenario
1. Master 0 reads lock (=0, free)
2. Master 1 reads lock (=0, free) — races!
3. Master 0 writes lock=1 → EXOKAY (wins)
4. Master 1 writes lock=1 → OKAY (fails, monitor cleared by M0's write)
5. Master 1 retries from read

---

## LAB 4: PROTOCOL VIOLATION DETECTION

### Objective
Insert protocol violations and verify checker catches them.

```systemverilog
// VIOLATION 1: VALID deasserted before READY
@(posedge clk);
AWVALID <= 1;
AWADDR <= 32'h1000;
@(posedge clk);
AWVALID <= 0;  // ← ILLEGAL! READY hasn't come yet!
// Checker should flag: "AWVALID deasserted without transfer"

// VIOLATION 2: Wrong beat count (AWLEN mismatch)
AWLEN <= 4'd3;  // Promises 4 beats
// But only send 3 W beats with WLAST on beat 3
// Checker: "WLAST asserted on beat 3, expected beat 4"

// VIOLATION 3: Missing WLAST
AWLEN <= 4'd1;  // Promises 2 beats
// Send 2 beats but forget WLAST on beat 2
// Checker: "Burst complete without WLAST"
```

---

## LAB 5: PERFORMANCE MEASUREMENT

### Objective
Measure AXI bus utilization and latency.

```systemverilog
// Latency counter
int read_latency_start, read_latency_total, read_count;

always @(posedge clk) begin
  if (ARVALID && ARREADY)
    read_latency_start <= $time;
  if (RVALID && RREADY && RLAST) begin
    read_latency_total += ($time - read_latency_start);
    read_count++;
  end
end

// Utilization counter
int busy_cycles, total_cycles;
always @(posedge clk) begin
  total_cycles++;
  if ((WVALID && WREADY) || (RVALID && RREADY))
    busy_cycles++;
end

// Report
final begin
  $display("Avg read latency: %0d cycles", read_latency_total/read_count);
  $display("Bus utilization: %0.1f%%", 100.0*busy_cycles/total_cycles);
end
```

---

## DEBUG SCENARIO 1: SYSTEM HANG (AXI DEADLOCK)

### Symptoms
- No transactions completing
- VALID signals stuck high
- System frozen

### Diagnosis
```
1. Check each channel's VALID/READY:
   AW: AWVALID=1, AWREADY=0 → Slave not accepting address
   W:  WVALID=1, WREADY=0   → Slave not accepting data
   B:  BVALID=0              → No response
   
2. Check slave state:
   - Is slave waiting for something? (internal FSM stuck)
   - Outstanding buffer full? (can't accept more)
   
3. Check for circular dependency:
   Master A → waiting for B response from Slave X
   Slave X → waiting to read from Master A's buffer
   = DEADLOCK

4. Common cause: Write response path blocked
   - Master's BREADY=0 (master not reading responses)
   - Slave can't send BRESP → won't accept new AW
   - Master can't get AW through → stuck
   
Fix: Master MUST always be able to accept responses (BREADY/RREADY)
     or buffer them. Never gate BREADY on AW acceptance.
```

---

## DEBUG SCENARIO 2: DATA CORRUPTION

### Symptoms
- Reads return wrong data
- Writes appear to succeed but data incorrect

### Diagnosis
```
1. Check WSTRB alignment:
   - 64-bit bus, writing to addr 0x1003 (unaligned)
   - WSTRB must enable only bytes 3-6 (not all bytes!)
   - Wrong WSTRB = wrong bytes written
   
2. Check burst address calculation:
   - INCR: addr + n*size each beat
   - WRAP: verify wrap boundary correct
   - Off-by-one in address = writing to wrong location
   
3. Check width conversion:
   - Upsizing: byte-lane placement correct?
   - Downsizing: beat order correct?
   
4. Check ID routing:
   - Response routed to wrong master (ID corruption)
   - Data for ID=3 delivered to ID=2's buffer
   
5. Check ordering:
   - Read-after-write to same address, different ID
   - No ordering guarantee! Read may get stale data
   - Fix: Use same ID or barrier between W and R
```

---

## DEBUG SCENARIO 3: PERFORMANCE LOWER THAN EXPECTED

### Symptoms
- Bandwidth far below theoretical maximum
- High latency

### Diagnosis
```
Theoretical max: Data_width × Frequency
  128-bit @ 500MHz = 8 GB/s per direction

Actual measurement: 3 GB/s (37.5% utilization)

Check 1: Stall cycles (VALID=1, READY=0)
  → AW stalls: 40% of cycles → Slave can't accept fast enough
  → W stalls: 10% → Slave occasionally backs off
  → Fix: Increase slave FIFO depth or use wider interface

Check 2: Idle cycles (VALID=0, both sides ready)
  → Master not driving: 30% idle → Master has gaps
  → Fix: Increase outstanding depth, prefetch

Check 3: Burst length
  → Average burst = 4 beats → Overhead from address phase
  → Fix: Use longer bursts (64-256 for bulk transfers)

Check 4: Outstanding depth
  → Only 1 outstanding → Fully serialized
  → Memory latency = 20 cycles → 20× waste
  → Fix: Allow 8-16 outstanding to pipeline

Check 5: Narrow transfers
  → 32-bit transfers on 128-bit bus = 25% efficiency
  → Fix: Batch narrow accesses or use appropriate bus width
```

---

## DEBUG SCENARIO 4: DECERR RESPONSES

### Symptoms
- Transactions returning DECERR (0b11)
- System crash or fault

### Diagnosis
```
DECERR = Interconnect cannot find slave at address

1. Check address mapping:
   - Is master using correct address for target?
   - Has address remapping changed? (MPU/MMU config)
   
2. Check slave enable:
   - Slave clock enabled? Power domain on?
   - Slave status = "okay" in device tree?
   
3. Check security:
   - AxPROT[1]=1 (non-secure) accessing secure region?
   - TrustZone TZASC blocking access?
   
4. Check region configuration:
   - Interconnect address map programmed correctly?
   - Hole in address map → default slave → DECERR

5. Common automotive issue:
   - Power domain turned off for unused peripheral
   - But DMA still programmed to access it
   - Result: DECERR → system fault → watchdog reset
```

---

## DEBUG SCENARIO 5: EXCLUSIVE ACCESS ALWAYS FAILS

### Symptoms
- Exclusive writes always return OKAY (never EXOKAY)
- Spinlocks never acquired → infinite loop

### Diagnosis
```
1. Does slave support exclusive?
   - Not all slaves implement exclusive monitor
   - If no monitor: always returns OKAY on exclusive write
   - Fix: Use slave with monitor, or global monitor in interconnect
   
2. Monitor granularity:
   - Monitor may track cache-line (64B) granularity
   - Two unrelated exclusive accesses within same line
   - Interfere with each other → constant failure
   - Fix: Align lock variables to monitor granularity
   
3. Cache coherence issue:
   - Cached access with exclusive → must be handled by cache/ACE
   - Non-cacheable exclusive → must reach slave monitor
   - Mismatch → monitor not properly updated
   
4. Interconnect stripping AxLOCK:
   - Some interconnects don't propagate ARLOCK/AWLOCK
   - Slave never sees exclusive → returns OKAY
   - Fix: Verify interconnect config preserves lock signals
```

---

END OF DOCUMENT 07 — LABS & DEBUGGING
