# AMD Infinity Fabric — LABS & DEBUGGING
# ════════════════════════════════════════════════════════════════════
# Protocol: Infinity Fabric | Document: 07 of 08
# ════════════════════════════════════════════════════════════════════

---

## LAB 1: NUMA TOPOLOGY EXPLORATION

**Objective**: Understand EPYC NUMA topology and verify NPS configuration.

```bash
# Step 1: Check NUMA topology
numactl --hardware
# Expected output (NPS1, 2-socket):
#   available: 2 nodes (0-1)
#   node 0 cpus: 0-95
#   node 1 cpus: 96-191
#   node 0 size: 256000 MB
#   node 1 size: 256000 MB
#   node distances:
#     node   0   1
#       0:  10  32
#       1:  32  10

# Step 2: Visualize topology with hwloc
lstopo --of txt
# Shows: Machine → Package → NUMANode → L3 → L2 → L1 → Core → PU

# Step 3: Check die/CCD mapping
cat /sys/devices/system/cpu/cpu0/topology/die_id
cat /sys/devices/system/cpu/cpu8/topology/die_id  # should be different CCD

# Step 4: Verify memory distances
cat /sys/devices/system/node/node0/distance
# NPS1: "10 32" (local=10, remote=32)
# NPS4: "10 12 12 12 32 ..." (local=10, intra-socket=12, remote=32)

# Step 5: Run latency test
# Using Intel MLC (works on AMD too):
./mlc --latency_matrix
# Shows access latency from each NUMA node to every other
```

**Analysis questions**:
1. What's the NUMA distance ratio (remote/local)?
2. Which CPUs share an L3 (same CCD)?
3. Does NPS4 show more NUMA nodes than NPS1?

---

## LAB 2: FCLK TUNING & BANDWIDTH MEASUREMENT

**Objective**: Measure impact of FCLK:MCLK ratio on bandwidth and latency.

```bash
# Step 1: Check current FCLK
# (requires root, AMD-specific)
cat /sys/devices/system/cpu/cpufreq/boost  # check boost status
# Use zenpower or k10temp for monitoring
modprobe zenpower 2>/dev/null || modprobe k10temp

# Step 2: STREAM benchmark for memory bandwidth
# Compile:
gcc -O3 -fopenmp -DSTREAM_ARRAY_SIZE=100000000 stream.c -o stream
export OMP_NUM_THREADS=96
export OMP_PROC_BIND=spread

# Run on local node:
numactl --cpunodebind=0 --membind=0 ./stream
# Expected: ~40-50 GB/s per node (NPS1)

# Step 3: Compare local vs remote
numactl --cpunodebind=0 --membind=1 ./stream   # remote memory!
# Expected: lower BW due to xGMI bottleneck

# Step 4: Test with all nodes (full system)
numactl --interleave=all ./stream
# Expected: max aggregate BW

# Step 5: Impact of FCLK (if adjustable in BIOS)
# BIOS: Set FCLK = 2000 MHz, MCLK = 2000 MHz (1:1)
# Run stream → record BW
# BIOS: Set FCLK = 1500 MHz, MCLK = 3000 MHz (1:2)
# Run stream → record BW (should be lower)
```

**Key observations**:
- 1:1 mode gives best latency
- Remote node BW limited by xGMI (~144 GB/s)
- NPS4 shows higher per-node local BW but lower total per-node

---

## LAB 3: INTER-CCD LATENCY MEASUREMENT

**Objective**: Measure cache-to-cache transfer latency across CCDs.

```c
/* lat_ccd.c — Ping-pong between cores on different CCDs */
#define _GNU_SOURCE
#include <pthread.h>
#include <stdio.h>
#include <stdatomic.h>
#include <sched.h>
#include <time.h>

static atomic_int flag = 0;
static volatile int data __attribute__((aligned(64)));
#define ITERATIONS 1000000

void pin_to_cpu(int cpu) {
    cpu_set_t set;
    CPU_ZERO(&set);
    CPU_SET(cpu, &set);
    sched_setaffinity(0, sizeof(set), &set);
}

void* pong_thread(void* arg) {
    int cpu = *(int*)arg;
    pin_to_cpu(cpu);
    for (int i = 0; i < ITERATIONS; i++) {
        while (atomic_load(&flag) != 1) {}  // wait for ping
        data = i;                            // write (forces coherence)
        atomic_store(&flag, 2);              // signal pong
    }
    return NULL;
}

int main(int argc, char* argv[]) {
    int cpu_a = 0;   // CCD 0
    int cpu_b = 8;   // CCD 1 (adjust based on topology)
    if (argc > 2) { cpu_a = atoi(argv[1]); cpu_b = atoi(argv[2]); }

    pin_to_cpu(cpu_a);
    pthread_t t;
    pthread_create(&t, NULL, pong_thread, &cpu_b);

    struct timespec start, end;
    clock_gettime(CLOCK_MONOTONIC, &start);

    for (int i = 0; i < ITERATIONS; i++) {
        data = i;
        atomic_store(&flag, 1);              // ping
        while (atomic_load(&flag) != 2) {}   // wait for pong
    }

    clock_gettime(CLOCK_MONOTONIC, &end);
    pthread_join(t, NULL);

    double ns = (end.tv_sec - start.tv_sec) * 1e9 + (end.tv_nsec - start.tv_nsec);
    printf("CPU %d ↔ CPU %d: %.1f ns/roundtrip (%.1f ns one-way)\n",
           cpu_a, cpu_b, ns / ITERATIONS, ns / ITERATIONS / 2);
    return 0;
}
```

```bash
# Compile and run:
gcc -O2 -pthread lat_ccd.c -o lat_ccd

# Same CCD (cores 0 and 1):
./lat_ccd 0 1          # Expected: ~15-20ns (L3 hit)

# Different CCD, same socket (cores 0 and 8):
./lat_ccd 0 8          # Expected: ~40-50ns (via IOD)

# Different socket (cores 0 and 96):
./lat_ccd 0 96         # Expected: ~130-170ns (via xGMI)
```

---

## LAB 4: NPS MODE PERFORMANCE COMPARISON

**Objective**: Compare application performance across NPS modes.

```bash
# Prerequisites: Reboot with different NPS modes (BIOS setting)
# Test 1: NPS1 | Test 2: NPS4

# === Database workload (latency-sensitive) ===
# Using sysbench with NUMA pinning:

# NPS1 (baseline):
numactl --cpunodebind=0 sysbench oltp_read_write \
    --tables=10 --table-size=1000000 \
    --threads=16 --time=60 run

# NPS4 (optimized):
numactl --cpunodebind=0 --membind=0 sysbench oltp_read_write \
    --tables=10 --table-size=1000000 \
    --threads=16 --time=60 run
# Expected: NPS4 with pinning → lower latency, higher TPS

# === HPC workload (bandwidth-sensitive) ===
# Using HPL (High Performance Linpack):
# NPS1: max BW → better for HPL
# NPS4: reduced BW per node → worse for HPL

# === Measure NUMA misses ===
perf stat -e node-load-misses,node-store-misses \
    numactl --cpunodebind=0 ./workload

# === Check memory distribution ===
numastat -p $(pidof workload)
# Look for: "other_node" column — should be near 0 if well-tuned
```

---

## DEBUG SCENARIO 1: UNEXPECTED NUMA PERFORMANCE DEGRADATION

**Symptoms**: Application runs 40% slower than expected despite having sufficient cores.

```bash
# Step 1: Check NUMA allocation
numastat -p $(pidof app)
# Look for: high "other_node" allocations

# Step 2: Check thread placement
ps -eo pid,tid,psr,comm | grep app
# Are threads scattered across NUMA nodes?

# Step 3: Check NPS mode
numactl --hardware
# If NPS1 with scattered allocation → memory is far from compute

# Step 4: Check for page migration
cat /proc/vmstat | grep numa
# numa_hit, numa_miss, numa_foreign

# Root cause: First-touch allocation from wrong node
# Fix: Either:
# (a) numactl --membind=0 --cpunodebind=0 ./app
# (b) Initialize data from the thread that will use it
# (c) Use numactl --interleave=all for uniform access patterns
```

---

## DEBUG SCENARIO 2: HIGH L3 MISS RATE DESPITE LARGE CACHE

**Symptoms**: 384MB total L3 should be enough, but seeing excessive DRAM accesses.

```bash
# Step 1: Measure L3 hit rate
perf stat -e LLC-loads,LLC-load-misses,LLC-store-misses ./app

# Step 2: Check if workload spans multiple CCDs
perf stat -e l3_lookup_state.all_l3_req_typs -a --per-core sleep 5

# Step 3: Key insight — L3 is 32MB per CCD, NOT shared across CCDs!
# Working set > 32MB per CCD → constant cross-CCD misses

# Root cause: Threads share data > 32MB → data bounces between CCD L3 caches
# Each CCD sees only its 32MB, not the full 384MB

# Fix options:
# (a) Pin cooperating threads to SAME CCD (cores 0-7 = 1 CCD)
# (b) Partition data so each thread's working set < 32MB
# (c) Use 3D V-Cache SKU (64-96MB per CCD)
# (d) Accept cross-CCD latency, optimize data layout
```

---

## DEBUG SCENARIO 3: xGMI BANDWIDTH SATURATION

**Symptoms**: Dual-socket performance doesn't scale — 2 sockets barely faster than 1.

```bash
# Step 1: Measure xGMI traffic (AMD μProf)
AMDuProfCLI collect --event df/xgmi_data_read,xgmi_data_write/ -a -- sleep 10

# Step 2: Calculate utilization
# xGMI max: ~144 GB/s/direction
# If measured traffic near max → saturated

# Step 3: Check workload access pattern
perf stat -e node-load-misses ./app
# High remote misses = lots of xGMI traffic

# Root cause: Workload data spread across both sockets, all threads access all data
# Fix:
# (a) Replicate read-only data on both NUMA nodes
# (b) Partition data: socket 0 owns half, socket 1 owns half
# (c) Use NPS4 + careful binding to maximize local access
# (d) Consider single-socket if BW is sufficient
```

---

## DEBUG SCENARIO 4: FCLK DESYNC CAUSING LATENCY SPIKES

**Symptoms**: Memory latency is ~10ns higher than expected for DDR5-4800.

```bash
# Step 1: Check current clocks
# AMD μProf or zenmonitor:
zenmonitor  # shows FCLK, MCLK, UCLK

# Step 2: Verify FCLK:MCLK ratio
# Expected 1:1 for DDR5-4800: FCLK=2400, MCLK=2400
# If showing FCLK=1800, MCLK=2400 → desync!

# Step 3: Measure actual latency
./mlc --latency_matrix
# Compare against expected 70-90ns for local DRAM

# Root cause: BIOS set FCLK to "Auto" which chose 2:1 ratio
# Or: FCLK couldn't reach 2400 MHz (silicon quality)

# Fix:
# (a) BIOS → CBS → NBIO → SMU → Set FCLK = MCLK manually
# (b) If FCLK can't reach 2400: use DDR5-4000 with FCLK=2000 (1:1)
# (c) Accept penalty if DDR5 OC bandwidth > latency cost
```

---

## PERFORMANCE TUNING CHECKLIST

```
□ Verify NPS mode matches workload (NPS1 for BW, NPS4 for latency)
□ Confirm FCLK:MCLK is 1:1 (check BIOS or zenmonitor)
□ Pin threads to appropriate NUMA nodes (numactl / taskset)
□ Allocate memory on local node (first-touch or membind)
□ Keep per-CCD working set < 32MB (or use V-Cache)
□ Monitor xGMI utilization for dual-socket (AMD μProf)
□ Check numastat for unexpected remote allocations
□ Disable transparent huge pages if NUMA-unaware
□ Use lstopo to verify actual topology matches expected
□ Profile with perf for node-load-misses events
```

---

END OF DOCUMENT 07 — LABS & DEBUGGING
