# AHB — CHEATSHEET
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA AHB | Document: 06 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. HTRANS ENCODING
| Value | Name | Meaning |
|-------|------|---------|
| 00 | IDLE | No transfer needed |
| 01 | BUSY | Master busy in burst (slave ignores) |
| 10 | NONSEQ | First beat / single transfer |
| 11 | SEQ | Subsequent burst beat |

## 2. HBURST ENCODING
| Value | Type | Beats | Address |
|-------|------|-------|---------|
| 000 | SINGLE | 1 | Single addr |
| 001 | INCR | Undef | Incrementing |
| 010 | WRAP4 | 4 | Wrapping |
| 011 | INCR4 | 4 | Incrementing |
| 100 | WRAP8 | 8 | Wrapping |
| 101 | INCR8 | 8 | Incrementing |
| 110 | WRAP16 | 16 | Wrapping |
| 111 | INCR16 | 16 | Incrementing |

## 3. HSIZE ENCODING
| Value | Bytes | Name |
|-------|-------|------|
| 000 | 1 | Byte |
| 001 | 2 | Halfword |
| 010 | 4 | Word |
| 011 | 8 | Doubleword |
| 100 | 16 | — |
| 101 | 32 | — |
| 110 | 64 | — |
| 111 | 128 | — |

## 4. HRESP ENCODING
| AHB-Lite | Meaning |
|----------|---------|
| 0 | OKAY |
| 1 | ERROR (2-cycle) |

| Full AHB [1:0] | Meaning |
|----------------|---------|
| 00 | OKAY |
| 01 | ERROR |
| 10 | RETRY |
| 11 | SPLIT |

## 5. HPROT ENCODING
| Bit | 0 | 1 |
|-----|---|---|
| [0] | Opcode fetch | Data access |
| [1] | User mode | Privileged |
| [2] | Non-bufferable | Bufferable |
| [3] | Non-cacheable | Cacheable |

## 6. SLAVE SAMPLE CONDITION
```
Valid transfer = HSEL & HREADY & HTRANS[1]
                 (selected) (not wait) (NONSEQ or SEQ)
```

## 7. PIPELINE TIMING TEMPLATE
```
Zero wait state:
  Cycle N:   Address phase (HADDR, HTRANS, HWRITE)
  Cycle N+1: Data phase (HWDATA/HRDATA, HREADY=1)

One wait state:
  Cycle N:   Address phase
  Cycle N+1: Data phase, HREADY=0 (wait)
  Cycle N+2: Data phase, HREADY=1 (complete)

Error:
  Cycle N:   Address phase
  Cycle N+1: HREADY=0, HRESP=1 (first error cycle)
  Cycle N+2: HREADY=1, HRESP=1 (second error cycle)
```

## 8. ADDRESS CALCULATION
```
INCR:  next_addr = current_addr + 2^HSIZE
WRAP:  boundary = beats × 2^HSIZE
       if (next_addr == upper_wrap) next_addr = lower_wrap

Example WRAP4, HSIZE=2 (4 bytes), start=0x108:
  boundary = 4×4 = 16 bytes → 0x100-0x10F
  Beat 0: 0x108
  Beat 1: 0x10C
  Beat 2: 0x100 (wrap!)
  Beat 3: 0x104
```

## 9. AHB-LITE SLAVE TEMPLATE (Verilog)
```verilog
module ahb_slave (
  input         HCLK, HRESETn,
  input         HSEL, HREADY,
  input  [31:0] HADDR,
  input  [1:0]  HTRANS,
  input         HWRITE,
  input  [2:0]  HSIZE,
  input  [31:0] HWDATA,
  output [31:0] HRDATA,
  output        HREADYOUT,
  output        HRESP
);
  // Address phase: latch when valid transfer
  reg [31:0] addr_reg;
  reg        write_reg;
  wire valid_transfer = HSEL & HREADY & HTRANS[1];
  
  always @(posedge HCLK or negedge HRESETn)
    if (!HRESETn) begin
      addr_reg <= 0; write_reg <= 0;
    end else if (valid_transfer) begin
      addr_reg <= HADDR;
      write_reg <= HWRITE;
    end
  
  // Data phase: read/write
  assign HREADYOUT = 1'b1;  // Zero wait state
  assign HRESP = 1'b0;      // Always OKAY
  assign HRDATA = mem[addr_reg[7:2]]; // Word-aligned read
  
  always @(posedge HCLK)
    if (write_reg & HREADYOUT)
      mem[addr_reg[7:2]] <= HWDATA;
endmodule
```

## 10. QUICK COMPARISON
| Feature | APB | AHB-Lite | AXI4-Lite | AXI4 |
|---------|-----|----------|-----------|------|
| Channels | 1 | 1 | 5 | 5 |
| Pipeline | No | Yes (2-stage) | Yes | Yes |
| Burst | No | Yes (16 max) | No | Yes (256) |
| Outstanding | No | No | No | Yes |
| Out-of-order | No | No | No | Yes |
| Wait states | Yes (PREADY) | Yes (HREADY) | Yes (READY) | Yes (READY) |
| Typical use | Config regs | Periph subsys | Simple regs | High BW |

---

END OF DOCUMENT 06 — CHEATSHEET
