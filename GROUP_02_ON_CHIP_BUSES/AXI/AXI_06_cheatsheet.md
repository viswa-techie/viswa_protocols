# AXI — CHEATSHEET
# ════════════════════════════════════════════════════════════════════
# Protocol: AMBA AXI | Document: 06 of 08
# ════════════════════════════════════════════════════════════════════

---

## 1. SIGNAL TABLE — WRITE ADDRESS CHANNEL (AW)

| Signal | Width | Source | Description |
|--------|-------|--------|-------------|
| AWID | ID_W | Master | Transaction ID |
| AWADDR | ADDR_W | Master | Write address |
| AWLEN | 8 | Master | Burst length - 1 (0-255) |
| AWSIZE | 3 | Master | Bytes per beat (encoded) |
| AWBURST | 2 | Master | Burst type (FIXED/INCR/WRAP) |
| AWLOCK | 1 | Master | Exclusive (1) or Normal (0) |
| AWCACHE | 4 | Master | Memory type attributes |
| AWPROT | 3 | Master | Protection (priv/sec/inst) |
| AWQOS | 4 | Master | Quality of Service |
| AWREGION | 4 | Master | Region identifier |
| AWUSER | User_W | Master | User-defined sideband |
| AWVALID | 1 | Master | Address valid |
| AWREADY | 1 | Slave | Address ready (can accept) |

---

## 2. SIGNAL TABLE — WRITE DATA CHANNEL (W)

| Signal | Width | Source | Description |
|--------|-------|--------|-------------|
| WDATA | DATA_W | Master | Write data |
| WSTRB | DATA_W/8 | Master | Byte strobes (1 bit per byte) |
| WLAST | 1 | Master | Last beat in burst |
| WUSER | User_W | Master | User-defined |
| WVALID | 1 | Master | Data valid |
| WREADY | 1 | Slave | Data ready |

Note: AXI4 removed WID (was present in AXI3 for interleaving)

---

## 3. SIGNAL TABLE — WRITE RESPONSE CHANNEL (B)

| Signal | Width | Source | Description |
|--------|-------|--------|-------------|
| BID | ID_W | Slave | Response ID (matches AWID) |
| BRESP | 2 | Slave | Write response |
| BUSER | User_W | Slave | User-defined |
| BVALID | 1 | Slave | Response valid |
| BREADY | 1 | Master | Response ready |

---

## 4. SIGNAL TABLE — READ ADDRESS CHANNEL (AR)

| Signal | Width | Source | Description |
|--------|-------|--------|-------------|
| ARID | ID_W | Master | Transaction ID |
| ARADDR | ADDR_W | Master | Read address |
| ARLEN | 8 | Master | Burst length - 1 |
| ARSIZE | 3 | Master | Bytes per beat (encoded) |
| ARBURST | 2 | Master | Burst type |
| ARLOCK | 1 | Master | Exclusive/Normal |
| ARCACHE | 4 | Master | Memory type |
| ARPROT | 3 | Master | Protection |
| ARQOS | 4 | Master | Quality of Service |
| ARREGION | 4 | Master | Region |
| ARUSER | User_W | Master | User-defined |
| ARVALID | 1 | Master | Address valid |
| ARREADY | 1 | Slave | Address ready |

---

## 5. SIGNAL TABLE — READ DATA CHANNEL (R)

| Signal | Width | Source | Description |
|--------|-------|--------|-------------|
| RID | ID_W | Slave | Response ID (matches ARID) |
| RDATA | DATA_W | Slave | Read data |
| RRESP | 2 | Slave | Read response |
| RLAST | 1 | Slave | Last beat in burst |
| RUSER | User_W | Slave | User-defined |
| RVALID | 1 | Slave | Data valid |
| RREADY | 1 | Master | Data ready |

---

## 6. BURST ENCODING

### AxSIZE Encoding
| AxSIZE[2:0] | Bytes/beat | Typical Use |
|-------------|------------|-------------|
| 000 | 1 | Byte access |
| 001 | 2 | Halfword |
| 010 | 4 | Word (32-bit) |
| 011 | 8 | Doubleword (64-bit) |
| 100 | 16 | 128-bit bus full |
| 101 | 32 | 256-bit bus full |
| 110 | 64 | 512-bit bus full |
| 111 | 128 | 1024-bit bus full |

### AxBURST Encoding
| AxBURST[1:0] | Type | Description |
|--------------|------|-------------|
| 00 | FIXED | Same address every beat |
| 01 | INCR | Incrementing address |
| 10 | WRAP | Wraps at boundary |
| 11 | Reserved | — |

### Quick Formula
```
Total bytes = (AxLEN + 1) × 2^AxSIZE
4KB check:  Start_addr + Total_bytes must not cross 4KB boundary
WRAP boundary = (AxLEN + 1) × 2^AxSIZE bytes
```

---

## 7. RESPONSE CODES

| RESP[1:0] | Name | Meaning | Who Generates |
|-----------|------|---------|---------------|
| 00 | OKAY | Normal success | Slave |
| 01 | EXOKAY | Exclusive success | Slave/Monitor |
| 10 | SLVERR | Slave error | Slave |
| 11 | DECERR | Decode error | Interconnect |

---

## 8. AxCACHE ENCODING

| Bit[3:0] | Name | Description |
|----------|------|-------------|
| 0000 | Device Non-bufferable | Strict ordering, no buffer |
| 0001 | Device Bufferable | Can buffer, no cache |
| 0010 | Normal Non-cache Non-buf | Shared memory, no cache |
| 0011 | Normal Non-cache Buf | Normal, bufferable |
| 0110 | Write-Through RA | Cache WT, read-allocate |
| 1110 | Write-Back RA/WA | Cache WB, read+write alloc |
| 1111 | Write-Back RA/WA | Same (both allocate bits) |

---

## 9. AxPROT ENCODING

| Bit | Value=0 | Value=1 |
|-----|---------|---------|
| [0] | Unprivileged | Privileged |
| [1] | Secure | Non-secure |
| [2] | Data access | Instruction fetch |

---

## 10. AXI4 vs AXI3 DIFFERENCES

| Feature | AXI3 | AXI4 |
|---------|------|------|
| Max burst length | 16 | 256 |
| Write interleaving | Yes (WID) | Removed |
| Locked transfers | Yes (AWLOCK=10) | Removed |
| QoS | No | Yes (AxQOS) |
| Region | No | Yes (AxREGION) |
| W before AW | Allowed | Not allowed |
| WID signal | Present | Removed |

---

## 11. AXI4-LITE SUBSET

| Feature | Present? |
|---------|----------|
| Burst (AxLEN) | No (always 1 beat) |
| IDs (AxID) | No |
| AxSIZE | No (always full width) |
| AxBURST | No |
| AxLOCK | No |
| AxCACHE | No |
| AxQOS | No |
| AxREGION | No |
| WSTRB | Yes |
| AxPROT | Yes |
| VALID/READY | Yes (same handshake) |
| Data width | 32 or 64 bits only |

---

## 12. HANDSHAKE RULES (CRITICAL)

```
RULE 1: VALID must NOT depend on READY
  Source asserts VALID independent of READY state
  
RULE 2: VALID must NOT deassert until transfer
  Once VALID=1, must hold until READY=1 (transfer occurs)
  
RULE 3: READY CAN depend on VALID
  Destination may wait to see VALID before asserting READY
  
RULE 4: READY CAN deassert anytime before VALID
  No obligation to hold READY

RULE 5: AW.VALID must not wait for W.VALID (AXI4)
  Write address and data are independent

RULE 6: Slave must respond to every transaction
  Even if error — must drive B or R channel
```

---

## 13. 4KB BOUNDARY RULE

```
No burst may cross a 4KB address boundary.

Why: Matches page size, prevents transaction spanning two slaves.

Check: (Start_Addr mod 4096) + Total_Bytes ≤ 4096

If would cross:
  Master must split into two separate bursts
  Interconnect may also split automatically
```

---

## 14. COMMON FORMULAS

```
Burst total bytes    = (AxLEN + 1) × 2^AxSIZE
Aligned address      = (Addr >> AxSIZE) << AxSIZE  
WRAP lower boundary  = (Addr / Wrap_size) × Wrap_size
WRAP upper boundary  = Lower + Wrap_size
WRAP_size            = (AxLEN + 1) × 2^AxSIZE
Next INCR address    = Prev_addr + 2^AxSIZE
Bandwidth (peak)     = Data_width × Clock_freq
Effective BW         = Peak × Utilization (VALID&READY cycles / total)
```

---

## 15. ORDERING QUICK REFERENCE

```
SAME ID:
  Write → Write:  In order at slave ✓
  Read  → Read:   Data returns in order ✓
  Write → Read:   Read sees write (same addr) ✓

DIFFERENT ID:
  Any → Any:      NO ordering guarantee ✗
  Need order?     Use barriers (DMB/DSB in ARM)

ACROSS SLAVES:
  Same ID, different slaves: No ordering guarantee ✗
  Need order? Wait for BRESP before next write
```

---

END OF DOCUMENT 06 — CHEATSHEET
