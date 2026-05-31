# NVLink — LABS & DEBUGGING
# ════════════════════════════════════════════════════════════════════
# Protocol: NVLink | Document: 07 of 08
# ════════════════════════════════════════════════════════════════════

---

## LAB 1: VERIFY NVLINK TOPOLOGY

```bash
# Step 1: Check GPU topology
nvidia-smi topo -m

# Expected output (DGX H100):
#         GPU0  GPU1  GPU2  GPU3  GPU4  GPU5  GPU6  GPU7
# GPU0     X    NV18  NV18  NV18  NV18  NV18  NV18  NV18
# GPU1    NV18   X    NV18  NV18  NV18  NV18  NV18  NV18
# ...
# NV18 = connected via 18 NVLinks (through NVSwitch)

# Step 2: Check individual link status
nvidia-smi nvlink -s -i 0    # GPU 0 NVLink status

# Step 3: Check for errors
nvidia-smi nvlink -e -i 0    # Error counters
# CRC errors, replay count, recovery count
```

---

## LAB 2: MEASURE PEER-TO-PEER BANDWIDTH

```cpp
// CUDA peer-to-peer bandwidth test
#include <cuda_runtime.h>

void measureP2PBandwidth(int srcDev, int dstDev) {
    cudaSetDevice(srcDev);
    cudaDeviceEnablePeerAccess(dstDev, 0);
    
    void *d_src, *d_dst;
    size_t size = 256 * 1024 * 1024; // 256 MB
    
    cudaSetDevice(srcDev); cudaMalloc(&d_src, size);
    cudaSetDevice(dstDev); cudaMalloc(&d_dst, size);
    
    cudaEvent_t start, stop;
    cudaEventCreate(&start); cudaEventCreate(&stop);
    
    cudaSetDevice(srcDev);
    cudaEventRecord(start);
    for (int i = 0; i < 100; i++)
        cudaMemcpyPeerAsync(d_dst, dstDev, d_src, srcDev, size);
    cudaEventRecord(stop);
    cudaEventSynchronize(stop);
    
    float ms;
    cudaEventElapsedTime(&ms, start, stop);
    float bw = (float)size * 100 / (ms * 1e6); // GB/s
    printf("GPU%d→GPU%d: %.2f GB/s\n", srcDev, dstDev, bw);
    // Expected: ~48-50 GB/s per link (NVLink 4.0 unidirectional)
}
```

---

## LAB 3: NCCL ALL-REDUCE BENCHMARK

```bash
# Build and run NCCL tests
git clone https://github.com/NVIDIA/nccl-tests
cd nccl-tests && make

# Run all-reduce with 8 GPUs
./build/all_reduce_perf -b 1M -e 4G -f 2 -g 8

# Expected output (H100 8-GPU NVSwitch):
# Size(B)    Time(us)    Algo BW(GB/s)   Bus BW(GB/s)
# 1048576       25.2        41.6           72.8
# ...
# 4294967296   5120.0       838.8          838.8
# 
# Bus BW should approach ~800+ GB/s for large messages (8 GPUs)
# If <<800 GB/s: NVLink issue or wrong NCCL algorithm
```

---

## LAB 4: UNIFIED MEMORY PAGE MIGRATION

```cpp
// Observe page migration over NVLink
__global__ void accessKernel(float* data, int n) {
    int i = threadIdx.x + blockIdx.x * blockDim.x;
    if (i < n) data[i] *= 2.0f;
}

int main() {
    float *uvm;
    size_t size = 1 << 28; // 256M floats
    cudaMallocManaged(&uvm, size * sizeof(float));
    
    // Initialize on CPU (pages on CPU memory)
    for (int i = 0; i < size; i++) uvm[i] = 1.0f;
    
    // First GPU access: triggers page migration CPU→GPU over NVLink
    // Monitor with: nvidia-smi dmon -s m
    accessKernel<<<size/256, 256>>>(uvm, size);
    cudaDeviceSynchronize();
    
    // CPU access after: triggers migration GPU→CPU over NVLink
    float sum = 0;
    for (int i = 0; i < 1000; i++) sum += uvm[i];
    
    // With Grace Hopper NVLink-C2C: NO migration needed (coherent access)
}
```

---

## DEBUG SCENARIO 1: NVLINK CRC ERRORS

**Symptoms**: nvidia-smi nvlink -e shows increasing CRC error count, effective bandwidth reduced.

**Diagnosis**:
1. Check error rate: `nvidia-smi nvlink -e -i <gpu>`
2. Identify affected link (which link index has errors)
3. Check if replay count increasing (retransmissions eating BW)
4. Common causes:
   - Signal integrity (cable/connector issue)
   - Thermal (SERDES temperature too high)
   - Manufacturing defect in lane

**Fix**: 
- Reseat NVLink cable/connector
- Check GPU/NVSwitch temperatures
- If persistent: GPU RMA (hardware fault)
- If intermittent: may degrade gracefully (lane removal)

---

## DEBUG SCENARIO 2: NCCL PERFORMANCE BELOW EXPECTED

**Symptoms**: all_reduce_perf shows 400 GB/s instead of expected 800 GB/s on 8×H100.

**Diagnosis**:
1. Check topology: `nvidia-smi topo -m` — all NV18?
2. Check NCCL algorithm: `NCCL_DEBUG=INFO` — is it using NVSwitch (NVLS) or falling back to ring?
3. Check NCCL version (old versions may not support NVSwitch optimally)
4. Check NVLink status: any links down?
5. Check GPU clock throttling: `nvidia-smi -q -d CLOCK`
6. Check for NVSwitch errors: `nvidia-smi nvswitch` 

**Fix**:
- Update NCCL to latest version
- Set `NCCL_ALGO=NVLS` to force NVLink SHARP algorithm
- Fix link/switch issues if found
- Disable power throttling for benchmarks

---

## DEBUG SCENARIO 3: PEER ACCESS FAILURE

**Symptoms**: `cudaDeviceEnablePeerAccess()` returns error, or P2P copy falls back to staging through CPU.

**Diagnosis**:
1. Check P2P capability: `cudaDeviceCanAccessPeer(&can, dev0, dev1)`
2. Check NVLink connection exists between those GPUs
3. Check BAR space: GPU BAR1 must be large enough for peer mapping
4. Check driver version (peer access requirements vary)
5. Check IOMMU settings (may block P2P on some systems)

**Fix**:
- Ensure NVLink physically connected between GPU pair
- Set large BAR in BIOS (above 4G decoding enabled)
- Disable IOMMU or configure for passthrough
- Update GPU driver

---

## DEBUG SCENARIO 4: GRACE HOPPER COHERENCE ISSUE

**Symptoms**: GPU and CPU see different values for same address (coherence violation).

**Diagnosis**:
1. Verify NVLink-C2C is active (not PCIe fallback)
2. Check memory attributes: coherent access requires correct mapping
3. Verify no explicit cache flush/invalidate interfering
4. Check for driver bug (coherence protocol implementation)
5. Verify memory ordering: GPU memory model is relaxed — need __threadfence_system() for CPU visibility

**Fix**:
- Use proper memory fences: `__threadfence_system()` for GPU→CPU visibility
- Use `volatile` or atomic for shared variables
- Verify NVLink-C2C link health
- Update driver/firmware for coherence bug fixes

---

END OF DOCUMENT 07 — LABS & DEBUGGING
