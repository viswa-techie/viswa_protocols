# AHB — LABS & DEBUGGING
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA AHB | Document: 07 of 08
# ════════════════════════════════════════════════════════════════════

---

## LAB 1: SINGLE WRITE/READ TRANSFER

```systemverilog
// Drive single write
task ahb_write(input [31:0] addr, input [31:0] data);
  @(posedge HCLK);
  HADDR <= addr; HTRANS <= 2'b10; HWRITE <= 1; HSIZE <= 3'b010;
  @(posedge HCLK);  // Data phase
  HWDATA <= data; HTRANS <= 2'b00;
  wait(HREADY); @(posedge HCLK);
  assert(HRESP == 0);
endtask

// Drive single read
task ahb_read(input [31:0] addr, output [31:0] data);
  @(posedge HCLK);
  HADDR <= addr; HTRANS <= 2'b10; HWRITE <= 0; HSIZE <= 3'b010;
  @(posedge HCLK);
  HTRANS <= 2'b00;
  wait(HREADY); data = HRDATA; @(posedge HCLK);
endtask
```

---

## LAB 2: 4-BEAT INCR BURST

```systemverilog
task ahb_burst_write(input [31:0] base_addr, input [31:0] data[4]);
  @(posedge HCLK);
  HADDR <= base_addr; HTRANS <= 2'b10; HBURST <= 3'b011; // INCR4
  HWRITE <= 1; HSIZE <= 3'b010;
  
  for (int i = 0; i < 4; i++) begin
    @(posedge HCLK);
    HWDATA <= data[i];
    if (i < 3) begin
      HADDR <= base_addr + (i+1)*4;
      HTRANS <= 2'b11; // SEQ
    end else begin
      HTRANS <= 2'b00; // IDLE after last
    end
    wait(HREADY);
  end
  @(posedge HCLK);
endtask
```

---

## LAB 3: WAIT STATE HANDLING

```systemverilog
// Slave with configurable wait states
module slow_slave #(parameter WAIT_CYCLES = 2) (...);
  reg [3:0] wait_cnt;
  
  always @(posedge HCLK or negedge HRESETn)
    if (!HRESETn) wait_cnt <= 0;
    else if (valid_transfer) wait_cnt <= WAIT_CYCLES;
    else if (wait_cnt > 0) wait_cnt <= wait_cnt - 1;
  
  assign HREADYOUT = (wait_cnt == 0);
endmodule
```

---

## DEBUG SCENARIO 1: BUS HANG (HREADY STUCK LOW)

**Symptoms**: All bus activity stops, watchdog fires.
**Diagnosis**:
1. Which slave has HSEL active? → That slave is holding HREADY=0
2. Check slave FSM — stuck in data phase waiting for internal resource
3. Common causes: slave accessing external interface that's dead, uninitialized FSM
**Fix**: Add timeout in slave (force HREADYOUT=1 + ERROR after N cycles). Add system bus watchdog.

---

## DEBUG SCENARIO 2: WRONG DATA ON READS

**Symptoms**: Read returns data from wrong address.
**Diagnosis**:
1. Check address-phase latching: slave must sample HADDR when HSEL & HREADY & HTRANS[1]
2. Common bug: Slave uses HADDR directly in data phase (should use latched version!)
3. Pipeline means HADDR has NEXT transfer's address during current data phase
**Fix**: Always register HADDR in address phase, use registered version for data phase.

---

## DEBUG SCENARIO 3: ERROR RESPONSE TIMING

**Symptoms**: Master doesn't detect error, continues normally.
**Diagnosis**:
1. Slave must drive error for exactly 2 cycles
2. Cycle 1: HREADYOUT=0, HRESP=1 (first cycle, pipeline stalls)
3. Cycle 2: HREADYOUT=1, HRESP=1 (second cycle, master sees error)
4. Bug: Slave drives only 1 cycle of error → master may miss it
**Fix**: Implement proper 2-cycle error state machine in slave.

---

## DEBUG SCENARIO 4: BURST ADDRESS MISMATCH

**Symptoms**: Slave receives unexpected addresses in burst.
**Diagnosis**:
1. Verify: first beat NONSEQ, subsequent SEQ
2. Address must increment by HSIZE each SEQ beat
3. WRAP burst: verify wrap boundary calculation
4. Common bug: master doesn't increment for SEQ (sends same addr)
**Fix**: Verify address generation logic in master matches HBURST type.

---

END OF DOCUMENT 07 — LABS & DEBUGGING
