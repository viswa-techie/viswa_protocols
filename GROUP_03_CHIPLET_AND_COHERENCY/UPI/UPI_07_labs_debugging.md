# Intel UPI (Ultra Path Interconnect) — LABS & DEBUGGING
# ════════════════════════════════════════════════════════════════════
# Protocol: Intel UPI | Document: 07 of 08
# ════════════════════════════════════════════════════════════════════

---

## LAB 1: UPI TOPOLOGY DISCOVERY & NUMA MAPPING

**Objective**: Identify UPI topology, NUMA nodes, and inter-socket distances.

```bash
# Step 1: Check NUMA topology
numactl --hardware
# 2S output example:
#   available: 2 nodes (0-1)
#   node 0 cpus: 0-59
#   node 1 cpus: 60-119
#   node distances:
#     node  0   1
#       0: 10  21
#       1: 21  10

# Step 2: Check with SNC enabled (SNC2, 2-socket → 4 NUMA nodes)
numactl --hardware
# SNC2 output:
#   available: 4 nodes (0-3)
#   node 0 cpus: 0-29     (socket 0, cluster 0)
#   node 1 cpus: 30-59    (socket 0, cluster 1)
#   node 2 cpus: 60-89    (socket 1, cluster 0)
#   node 3 cpus: 90-119   (socket 1, cluster 1)
#   distances:
#     node  0  1  2  3
#       0: 10 11 21 21
#       1: 11 10 21 21
#       2: 21 21 10 11
#       3: 21 21 11 10

# Step 3: Visualize with hwloc
lstopo --of txt
# Shows: Machine → Package → NUMANode → L3 → Core

# Step 4: Check UPI links in dmesg
dmesg | grep -i "upi\|qpi\|intel_uncore"
# Should show UPI link detection during boot

# Step 5: Verify number of UPI links
ls /sys/devices/uncore_upi_*/
# Count entries = number of UPI links per socket
```

---

## LAB 2: MEASURING UPI BANDWIDTH WITH PCM

**Objective**: Measure real-time UPI utilization and bandwidth.

```bash
# Step 1: Install Intel PCM (Processor Counter Monitor)
git clone https://github.com/intel/pcm.git
cd pcm && mkdir build && cd build
cmake .. && make -j$(nproc)

# Step 2: Run PCM to see UPI bandwidth
sudo ./pcm.x 1   # 1-second interval
# Output shows per-socket:
#   UPI0: xx GB/s read, yy GB/s write
#   UPI1: xx GB/s read, yy GB/s write
#   ...

# Step 3: Generate cross-socket traffic
# In another terminal:
numactl --cpunodebind=0 --membind=1 stream_c.avx512
# Forces cores on socket 0 to access memory on socket 1
# PCM should show UPI link utilization spike

# Step 4: Measure per-link bandwidth
sudo ./pcm.x -csv=upi_data.csv 1
# Post-process CSV to analyze link utilization over time

# Step 5: Compare with local memory access
numactl --cpunodebind=0 --membind=0 stream_c.avx512
# UPI utilization should be near-zero
# Local DRAM BW should be maximum
```

---

## LAB 3: SNOOP MODE IMPACT ON LATENCY

**Objective**: Measure coherence latency differences between snoop modes.

```bash
# Step 1: Check current snoop mode
# BIOS: Advanced → Processor → Snoop Mode
# Options: Early Snoop, Home Snoop, Cluster on Die, SNC

# Step 2: Latency measurement (pointer-chasing)
# Allocate array on remote node, chase pointers from local node
cat > lat_test.c << 'EOF'
#define _GNU_SOURCE
#include <stdio.h>
#include <stdlib.h>
#include <numa.h>
#include <time.h>

#define SIZE (64*1024*1024)  // 64MB (exceeds LLC)
#define STRIDE 64            // cache line

int main() {
    // Allocate on node 1
    void *mem = numa_alloc_onnode(SIZE, 1);
    
    // Create pointer chain (random order)
    char **chain = (char**)mem;
    long count = SIZE / STRIDE;
    // ... (create random pointer chain in mem)
    
    // Chase from node 0 cores
    struct timespec start, end;
    volatile char **p = (volatile char**)mem;
    
    clock_gettime(CLOCK_MONOTONIC, &start);
    for (long i = 0; i < 10000000; i++) {
        p = (volatile char**)*p;
    }
    clock_gettime(CLOCK_MONOTONIC, &end);
    
    double ns = (end.tv_sec-start.tv_sec)*1e9 + (end.tv_nsec-start.tv_nsec);
    printf("Remote access latency: %.1f ns\n", ns / 10000000);
    return 0;
}
EOF

gcc -O2 -lnuma lat_test.c -o lat_test
numactl --cpunodebind=0 ./lat_test

# Step 3: Record latency, change snoop mode in BIOS, repeat
# Expected:
#   Early Snoop: ~130ns (lowest for 2S)
#   Home Snoop:  ~140ns (slightly higher, but scales better)
#   SNC2:        ~125ns (local cluster to remote cluster)
```

---

## LAB 4: COHERENCE TRAFFIC ANALYSIS

**Objective**: Observe coherence message patterns using perf counters.

```bash
# Step 1: List available UPI uncore events
perf list | grep uncore_upi

# Step 2: Count UPI flits by type
sudo perf stat -e \
    uncore_upi/event=0x02,umask=0x0f/,\
    uncore_upi/event=0x03,umask=0x0f/ \
    -a -- sleep 5

# Step 3: Run a workload with cross-socket sharing
# Two threads on different sockets sharing a cache line (false sharing):
cat > false_share.c << 'EOF'
#define _GNU_SOURCE
#include <pthread.h>
#include <sched.h>

struct { long a; long b; } shared __attribute__((aligned(64)));
// a and b on same cache line!

void* writer_a(void* arg) {
    cpu_set_t set; CPU_ZERO(&set); CPU_SET(0, &set);
    sched_setaffinity(0, sizeof(set), &set);
    for (long i = 0; i < 100000000; i++) shared.a++;
    return NULL;
}

void* writer_b(void* arg) {
    cpu_set_t set; CPU_ZERO(&set); CPU_SET(60, &set);  // other socket
    sched_setaffinity(0, sizeof(set), &set);
    for (long i = 0; i < 100000000; i++) shared.b++;
    return NULL;
}

int main() {
    pthread_t ta, tb;
    pthread_create(&ta, NULL, writer_a, NULL);
    pthread_create(&tb, NULL, writer_b, NULL);
    pthread_join(ta, NULL);
    pthread_join(tb, NULL);
}
EOF

gcc -O2 -pthread false_share.c -o false_share

# Step 4: Measure UPI traffic during false sharing
sudo perf stat -e uncore_upi/event=0x02/ ./false_share
# Should see massive UPI snoop traffic!

# Step 5: Fix false sharing (pad to separate cache lines)
# Rerun — UPI traffic should drop to near-zero
```

---

## DEBUG SCENARIO 1: UNEXPECTED HIGH REMOTE MEMORY ACCESS

**Symptoms**: Application performance 30-40% below expected. High UPI utilization observed.

```bash
# Step 1: Confirm NUMA issue
numastat -p $(pidof app)
# Check "other_node" column — if high, memory on wrong node

# Step 2: Check thread affinity
ps -eo pid,tid,psr,comm | grep app
# Are threads migrating between sockets?

# Step 3: Check memory allocation policy
cat /proc/$(pidof app)/numa_maps | head -20
# Look for: "N1=xxxxx" on threads running on node 0

# Root causes:
# (a) First-touch from wrong node (initialization thread on socket 1)
# (b) Memory allocator (malloc) not NUMA-aware
# (c) Kernel page migration not active

# Fix:
numactl --cpunodebind=0 --membind=0 ./app    # Strict binding
# Or: Use libnuma in application for explicit placement
# Or: Enable AutoNUMA (kernel.numa_balancing=1)
```

---

## DEBUG SCENARIO 2: UPI CRC ERRORS CAUSING LINK DEGRADATION

**Symptoms**: System running but with degraded inter-socket bandwidth. Error logs show CRC retries.

```bash
# Step 1: Check machine check architecture (MCA) banks
mcelog --client   # Or check:
cat /sys/devices/system/machinecheck/machinecheck0/bank*

# Step 2: Check UPI specific errors
dmesg | grep -i "upi\|corrected\|link"
# Look for: "UPI: Correctable error detected on link X"

# Step 3: Check link status
sudo rdmsr 0x<UPI_STATUS_MSR>   # Platform-specific
# Or use Intel Platform Diagnostic Tool

# Step 4: Check if link degraded (reduced width)
pcm.x   # Look for reduced BW on specific link

# Diagnosis:
# - Consistent on one link: physical layer (connector, trace, signal integrity)
# - All links intermittent: power delivery issue, thermal
# - Only under load: marginal signal + cross-talk

# Fix:
# (a) Reseat CPU (cleaning socket/pins)
# (b) Check BIOS for UPI link speed override (reduce GT/s)
# (c) Check thermal solution on CPU area
# (d) If persistent: RMA motherboard/CPU
```

---

## DEBUG SCENARIO 3: SNOOP FILTER OVERFLOW CAUSING PERFORMANCE DEGRADATION

**Symptoms**: Performance degrades when working set grows beyond a threshold, despite having enough LLC.

```bash
# Step 1: Monitor snoop filter evictions
# Using perf with CHA events (model-specific):
sudo perf stat -e cha/event=0x35,umask=0x01/ -a -- ./app
# (SF eviction event — varies by platform)

# Step 2: Correlate with working set size
# Run with increasing data sizes:
for size in 64 128 256 512 1024; do
    echo "Working set: ${size}MB"
    ./app --size=${size}M 2>&1 | tail -1
done
# Look for cliff: performance drops sharply at SF capacity

# Step 3: Understand root cause
# SF has finite entries. When full:
# - Must evict oldest entry
# - Eviction → back-invalidation to remote socket
# - Remote socket loses cache line → more DRAM accesses
# - Cascade effect: more SF pressure

# Step 4: Mitigation
# (a) Reduce cross-socket sharing (partition data)
# (b) Use SNC to reduce SF pressure (smaller per-cluster)
# (c) Pin workload to single socket if possible
# (d) Newer platforms: larger SF
```

---

## DEBUG SCENARIO 4: L0p POWER STATE CAUSING LATENCY SPIKES

**Symptoms**: Tail latency (P99) is significantly higher than median, with periodic spikes of ~10ns.

```bash
# Step 1: Check if L0p is enabled
# BIOS: Advanced → Power → UPI Link Power Management
# Or check via perf UPI power events

# Step 2: Measure latency distribution
./lat_test --histogram
# Look for bimodal distribution:
# Peak 1: ~130ns (L0 → immediate transfer)
# Peak 2: ~140ns (L0p → wake 10 lanes → transfer)

# Step 3: Confirm by disabling L0p
# BIOS: Set "UPI Link Disable" = L0p Disable
# Rerun: P99 should align with median

# Step 4: Evaluate trade-off
# L0p saves significant power (>10W per socket)
# But adds ~10ns to first flit after idle period
# Decision: latency-critical (HFT, OLTP) → disable L0p
#           general workload → keep L0p (power savings worth it)
```

---

## PERFORMANCE TUNING CHECKLIST

```
□ Verify NUMA topology matches application expectations
□ Pin threads to cores near their memory (numactl)
□ Check for cross-socket false sharing (perf c2c)
□ Select appropriate snoop mode for topology (2S: source/early, 4S: home)
□ Enable SNC if workload is NUMA-aware (reduces local latency)
□ Disable L0p for ultra-low-latency workloads
□ Monitor UPI utilization (pcm.x) — saturated = bottleneck
□ Check for SF overflow on large working sets
□ Validate no CRC errors (MCA logs)
□ Use Intel VTune for detailed memory/UPI profiling
□ Consider single-socket if cross-socket traffic is excessive
□ Verify DDR channels balanced (all DIMMs populated)
```

---

END OF DOCUMENT 07 — LABS & DEBUGGING
