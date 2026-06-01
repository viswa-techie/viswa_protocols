# NIST FIPS 203 — ML-KEM (Module-Lattice Key Encapsulation Mechanism)

**Standard:** FIPS 203 (August 13, 2024)  
**Title:** Module-Lattice-Based Key-Encapsulation Mechanism Standard  
**Based on:** CRYSTALS-Kyber (winner of NIST PQC KEM competition)  
**SDO:** National Institute of Standards and Technology (NIST), US Department of Commerce  
**Domain:** Post-quantum cryptography; key encapsulation; key exchange  
**Audience:** Cryptographic engineers, protocol designers, HSM developers, security architects  
**Prerequisites:** Linear algebra; modular arithmetic; polynomial rings; understanding of IND-CCA2 security; PKI/TLS concepts

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Milestone |
|------|-----------|
| 2005 | Regev introduces Learning With Errors (LWE) problem |
| 2010 | Lyubashevsky, Peikert, Regev: Ring-LWE (efficient lattice-based crypto) |
| 2012 | Module-LWE (generalization: tunable module rank for flexible security/performance) |
| 2016 | NIST PQC Competition announced (Call for Proposals) |
| 2017 | CRYSTALS-Kyber submitted by: Roberto Avanzi, Joppe Bos, Léo Ducas, Eike Kiltz, Tancrède Lepoint, Vadim Lyubashevsky, John Schanck, Peter Schwabe, Gregor Seiler, Damien Stehlé |
| 2019 | Kyber advances to NIST Round 2 (26 → 15 candidates) |
| 2020 | Kyber advances to Round 3 as finalist (7 candidates) |
| 2022 | **NIST selects Kyber** as the KEM standard (July 5, 2022) |
| 2023 | FIPS 203 draft published for public comment; minor modifications from Kyber submission |
| **2024** | **FIPS 203 published** (August 13, 2024) — ML-KEM is official US federal standard |
| 2024 | Chrome, Signal, iMessage, OpenSSH deploy ML-KEM in production |

### 1.2 Why Kyber Won

| Criterion | Kyber's Advantage |
|:---------:|-------------------|
| **Security** | Based on well-studied Module-LWE problem; conservative parameters; extensive cryptanalysis |
| **Performance** | Fastest KEM among finalists; small key/ciphertext; fast key generation/encapsulation/decapsulation |
| **Implementation** | Simple arithmetic (polynomial multiplication in $\mathbb{Z}_q[x]/(x^n+1)$); resistant to timing side-channels (constant-time by design) |
| **Flexibility** | Three parameter sets covering security levels 1, 3, 5 |
| **Maturity** | Extensive third-party analysis; multiple independent implementations; real-world deployment testing |

---

## Chapter 2 — Mathematical Foundation

### 2.1 The Learning With Errors (LWE) Problem

**Definition:** Given a secret vector $\mathbf{s} \in \mathbb{Z}_q^n$, matrix $\mathbf{A} \in \mathbb{Z}_q^{m \times n}$, and error vector $\mathbf{e}$ (small entries from distribution $\chi$):

$$\mathbf{b} = \mathbf{A} \cdot \mathbf{s} + \mathbf{e} \pmod{q}$$

**Problem:** Given $(\mathbf{A}, \mathbf{b})$, find $\mathbf{s}$ (search-LWE) or distinguish $(\mathbf{A}, \mathbf{b})$ from uniform random (decision-LWE).

**Hardness:** Equivalent (quantum reduction) to worst-case lattice problems (Shortest Vector Problem; SIVP).

### 2.2 Module-LWE (MLWE)

Module-LWE generalizes Ring-LWE by working over a module $R_q^k$ where $R_q = \mathbb{Z}_q[x]/(x^n + 1)$:

$$\mathbf{b} = \mathbf{A} \cdot \mathbf{s} + \mathbf{e} \in R_q^k$$

Where:
- $\mathbf{A} \in R_q^{k \times k}$ is a public random matrix (module rank $k$)
- $\mathbf{s} \in R_q^k$ is the secret vector (small coefficients)
- $\mathbf{e} \in R_q^k$ is the error vector (small coefficients from centered binomial distribution)
- $n = 256$ (polynomial degree in ML-KEM)
- $q = 3329$ (modulus)

### 2.3 Key Parameters

| Symbol | Value | Description |
|:------:|:-----:|-------------|
| $n$ | 256 | Polynomial degree (ring dimension) |
| $q$ | 3329 | Modulus (prime; NTT-friendly: $q \equiv 1 \pmod{2n}$) |
| $k$ | 2, 3, or 4 | Module rank (determines security level) |
| $\eta_1$ | 3, 2, or 2 | CBD parameter for secret/error in key generation |
| $\eta_2$ | 2, 2, or 2 | CBD parameter for encryption noise |
| $d_u$ | 10, 10, or 11 | Compression bits for ciphertext component $\mathbf{u}$ |
| $d_v$ | 4, 4, or 5 | Compression bits for ciphertext component $v$ |

### 2.4 Number Theoretic Transform (NTT)

ML-KEM uses NTT for efficient polynomial multiplication in $R_q$:
- NTT converts polynomial to evaluation form: $O(n \log n)$
- Pointwise multiplication in NTT domain: $O(n)$
- Inverse NTT converts back: $O(n \log n)$
- Total multiplication: $O(n \log n)$ instead of $O(n^2)$

$q = 3329 = 2^8 \cdot 13 + 1$ → primitive 512th root of unity exists in $\mathbb{Z}_{3329}$.

---

## Chapter 3 — Algorithm Specification

### 3.1 ML-KEM Operations

| Operation | Input | Output | Purpose |
|:---------:|-------|--------|---------|
| **KeyGen** | Security parameter $k$ | $(ek, dk)$ — encapsulation key + decapsulation key | Generate key pair |
| **Encaps** | Encapsulation key $ek$ | $(c, K)$ — ciphertext + shared secret | Sender encapsulates secret |
| **Decaps** | Decapsulation key $dk$, ciphertext $c$ | $K$ — shared secret (or $\perp$) | Receiver recovers secret |

### 3.2 Key Generation (ML-KEM.KeyGen)

| Step | Operation |
|:----:|-----------|
| 1 | Generate random seed $d \leftarrow \{0,1\}^{256}$ |
| 2 | Derive $(\rho, \sigma) = G(d)$ where $G$ is SHA3-512 |
| 3 | Generate matrix $\hat{\mathbf{A}} \in R_q^{k \times k}$ from $\rho$ using XOF (SHAKE-128) |
| 4 | Sample secret $\mathbf{s} \leftarrow \text{CBD}_{\eta_1}(\sigma, 0..k-1)$ |
| 5 | Sample error $\mathbf{e} \leftarrow \text{CBD}_{\eta_1}(\sigma, k..2k-1)$ |
| 6 | Compute $\hat{\mathbf{t}} = \hat{\mathbf{A}} \cdot \hat{\mathbf{s}} + \hat{\mathbf{e}}$ (in NTT domain) |
| 7 | $ek = (\hat{\mathbf{t}}, \rho)$ — encapsulation key (public) |
| 8 | $dk = (\hat{\mathbf{s}}, ek, H(ek), z)$ — decapsulation key (private); $z$ random for implicit rejection |

### 3.3 Encapsulation (ML-KEM.Encaps)

| Step | Operation |
|:----:|-----------|
| 1 | Generate random message $m \leftarrow \{0,1\}^{256}$ |
| 2 | $(K, r) = G(m \| H(ek))$ — derive shared secret and randomness |
| 3 | Sample vectors $\mathbf{r}, \mathbf{e_1}, e_2$ from CBD using seed $r$ |
| 4 | Compute $\mathbf{u} = \text{Compress}_{d_u}(\hat{\mathbf{A}}^T \cdot \hat{\mathbf{r}} + \mathbf{e_1})$ |
| 5 | Compute $v = \text{Compress}_{d_v}(\hat{\mathbf{t}}^T \cdot \hat{\mathbf{r}} + e_2 + \lceil q/2 \rceil \cdot m)$ |
| 6 | $c = (\mathbf{u}, v)$ — ciphertext |
| 7 | Output $(c, K)$ — ciphertext + shared secret |

### 3.4 Decapsulation (ML-KEM.Decaps) — Fujisaki-Okamoto Transform

| Step | Operation |
|:----:|-----------|
| 1 | Decompress $\mathbf{u}$ and $v$ |
| 2 | Compute $m' = \text{Compress}_1(v - \hat{\mathbf{s}}^T \cdot \text{NTT}(\mathbf{u}))$ — decrypt |
| 3 | $(K', r') = G(m' \| H(ek))$ — re-derive |
| 4 | Re-encrypt: compute $c'$ from $m'$ and $r'$ |
| 5 | **If** $c' = c$: output $K = K'$ (valid decapsulation) |
| 6 | **Else**: output $K = \text{KDF}(z \| H(c))$ (implicit rejection; $z$ from $dk$) |

**Implicit rejection:** If ciphertext is invalid (tampered), output a pseudorandom key instead of failure. This provides IND-CCA2 security and prevents chosen-ciphertext attacks from distinguishing rejection.

---

## Chapter 4 — Parameter Sets

### 4.1 ML-KEM Parameter Sets

| Parameter | ML-KEM-512 | ML-KEM-768 | ML-KEM-1024 |
|:---------:|:---:|:---:|:---:|
| NIST Security Level | 1 (~AES-128) | 3 (~AES-192) | 5 (~AES-256) |
| Module rank $k$ | 2 | 3 | 4 |
| $n$ | 256 | 256 | 256 |
| $q$ | 3329 | 3329 | 3329 |
| $\eta_1$ | 3 | 2 | 2 |
| $\eta_2$ | 2 | 2 | 2 |
| $d_u$ | 10 | 10 | 11 |
| $d_v$ | 4 | 4 | 5 |
| **Public key size** | **800 bytes** | **1,184 bytes** | **1,568 bytes** |
| **Secret key size** | **1,632 bytes** | **2,400 bytes** | **3,168 bytes** |
| **Ciphertext size** | **768 bytes** | **1,088 bytes** | **1,568 bytes** |
| **Shared secret** | **32 bytes** | **32 bytes** | **32 bytes** |

### 4.2 Comparison with Classical Algorithms

| Algorithm | Public Key | Ciphertext/Shared | Total Handshake | Security |
|:---------:|:---:|:---:|:---:|:---:|
| X25519 (ECDH) | 32 B | 32 B | 64 B | ~128-bit classical |
| RSA-2048 (KEM) | 256 B | 256 B | 512 B | ~112-bit classical |
| **ML-KEM-512** | **800 B** | **768 B** | **1,568 B** | Level 1 (PQ) |
| **ML-KEM-768** | **1,184 B** | **1,088 B** | **2,272 B** | Level 3 (PQ) |
| **ML-KEM-1024** | **1,568 B** | **1,568 B** | **3,136 B** | Level 5 (PQ) |

### 4.3 Performance Benchmarks (Reference Implementation)

| Operation | ML-KEM-512 | ML-KEM-768 | ML-KEM-1024 | X25519 |
|:---------:|:---:|:---:|:---:|:---:|
| KeyGen | ~30 μs | ~50 μs | ~75 μs | ~50 μs |
| Encaps | ~35 μs | ~60 μs | ~90 μs | ~50 μs |
| Decaps | ~40 μs | ~65 μs | ~95 μs | ~50 μs |
| Total KEM | ~105 μs | ~175 μs | ~260 μs | ~150 μs |

*Approximate; platform-dependent (x86-64 with AVX2); ML-KEM is comparable to or faster than X25519 for key exchange operations.

---

## Chapter 5 — Security Analysis

### 5.1 Security Properties

| Property | Guarantee |
|:--------:|-----------|
| **IND-CCA2** | Indistinguishability under adaptive chosen-ciphertext attack (strongest KEM security) |
| **Implicit rejection** | Decapsulation of invalid ciphertext produces pseudorandom key (not error) |
| **Key confirmation** | Shared secret depends on both parties' contributions |
| **Forward secrecy** | When used with ephemeral keys (new key pair per session) |

### 5.2 Known Attack Complexities

| Attack | ML-KEM-512 | ML-KEM-768 | ML-KEM-1024 |
|:------:|:---:|:---:|:---:|
| Best known classical (primal lattice attack) | ~$2^{118}$ | ~$2^{183}$ | ~$2^{254}$ |
| Best known quantum (same; Grover speedup) | ~$2^{107}$ | ~$2^{166}$ | ~$2^{230}$ |
| Core-SVP (classical) | $2^{118}$ | $2^{182}$ | $2^{253}$ |
| Core-SVP (quantum) | $2^{107}$ | $2^{165}$ | $2^{229}$ |

### 5.3 Side-Channel Considerations

| Vector | Risk | Mitigation |
|:------:|:----:|------------|
| **Timing** | Variable-time operations (NTT; comparison in decaps) | Constant-time implementation mandatory; no branching on secrets |
| **Power/EM** | Polynomial multiplication leaks via power traces | Masking; shuffling of NTT butterfly operations |
| **Fault injection** | Skip decapsulation check → leak key material | Redundant computation; comparison hardening |
| **Cache timing** | Table lookups in NTT | Bitsliced/vector implementation avoiding table access |

---

## Chapter 6 — Implementation Guide

### 6.1 Integration Patterns

| Protocol | Integration Point | Hybrid Approach |
|:--------:|:-:|---|
| **TLS 1.3** | Key exchange (ClientHello → ServerHello) | X25519+ML-KEM-768 (concatenated key shares) |
| **SSH** | Key exchange (kex_init) | x25519-mlkem768@openssh.com |
| **Signal** | Initial key exchange (X3DH → PQXDH) | X25519 + ML-KEM-768 |
| **IPsec/IKEv2** | Key exchange (IKE_SA_INIT) | Hybrid: classical + PQC |
| **S/MIME** | Key transport (RecipientInfo) | ML-KEM-768 for content encryption key |
| **WireGuard** | Noise_IK pattern | Rosenpass (external PQC layer) |

### 6.2 TLS 1.3 Hybrid Key Exchange (X25519+ML-KEM-768)

```
Client:
  1. Generate X25519 ephemeral key pair (ek_c, dk_c)
  2. Generate ML-KEM-768 key pair (ek_kem, dk_kem)
  3. ClientHello: key_share = X25519_pk || ML-KEM-768_ek
  
Server:
  1. X25519: compute shared_1 = X25519(dk_s, ek_c)
  2. ML-KEM: (ct, shared_2) = ML-KEM.Encaps(ek_kem)
  3. ServerHello: key_share = X25519_pk || ct
  4. combined_secret = shared_1 || shared_2
  5. Derive session keys from combined_secret via HKDF

Client:
  1. X25519: compute shared_1 = X25519(dk_c, pk_s)
  2. ML-KEM: shared_2 = ML-KEM.Decaps(dk_kem, ct)
  3. combined_secret = shared_1 || shared_2
  4. Derive session keys (same as server)
```

### 6.3 Library Usage (liboqs — C)

```c
#include <oqs/oqs.h>

// Key Generation
OQS_KEM *kem = OQS_KEM_new(OQS_KEM_alg_ml_kem_768);
uint8_t *public_key = malloc(kem->length_public_key);
uint8_t *secret_key = malloc(kem->length_secret_key);
OQS_KEM_keypair(kem, public_key, secret_key);

// Encapsulation (sender)
uint8_t *ciphertext = malloc(kem->length_ciphertext);
uint8_t *shared_secret_enc = malloc(kem->length_shared_secret);
OQS_KEM_encaps(kem, ciphertext, shared_secret_enc, public_key);

// Decapsulation (receiver)
uint8_t *shared_secret_dec = malloc(kem->length_shared_secret);
OQS_KEM_decaps(kem, shared_secret_dec, ciphertext, secret_key);

// shared_secret_enc == shared_secret_dec (32 bytes)
OQS_KEM_free(kem);
```

---

## Chapter 7 — Comparison with Other KEMs

| KEM | Math Basis | Public Key | Ciphertext | Security | Performance | Status |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ML-KEM-768** | Module-LWE (lattice) | 1,184 B | 1,088 B | Level 3 | Very fast | **FIPS 203 (2024)** |
| Classic McEliece | Code-based (Goppa codes) | **1,044,992 B** (~1 MB!) | 188 B | Level 5 | Fast decaps | NIST Round 4 |
| BIKE | Code-based (QC-MDPC) | 3,114 B | 3,114 B | Level 3 | Moderate | NIST Round 4 |
| HQC | Code-based (Hamming quasi-cyclic) | 4,522 B | 9,026 B | Level 3 | Moderate | NIST Round 4 |
| FrodoKEM | Standard LWE (conservative) | 15,632 B | 15,744 B | Level 3 | Slow | Not standardized |
| SIKE | Isogeny-based | 330 B | 346 B | Level 1 | Very slow | **BROKEN (2022)** |

**ML-KEM wins because:** Best balance of key size + ciphertext size + performance + security confidence. Classic McEliece has smallest ciphertext but impractical 1 MB public key.

---

## Chapter 8 — Architecture Diagrams

### 8.1 ML-KEM Internal Structure

```mermaid
graph TB
    subgraph "ML-KEM Key Generation"
        SEED[Random Seed d<br/>256 bits] --> G[G = SHA3-512<br/>→ (ρ, σ)]
        G --> GEN_A[Generate Â from ρ<br/>SHAKE-128 (XOF)<br/>k×k matrix in NTT domain]
        G --> GEN_S[Sample s from σ<br/>CBD_η₁<br/>k polynomials]
        G --> GEN_E[Sample e from σ<br/>CBD_η₁<br/>k polynomials]
        GEN_A --> COMP_T["Compute t̂ = Â·ŝ + ê<br/>(NTT domain)"]
        GEN_S --> COMP_T
        GEN_E --> COMP_T
        COMP_T --> EK["ek = (t̂, ρ)<br/>Public Key"]
        GEN_S --> DK["dk = (ŝ, ek, H(ek), z)<br/>Secret Key"]
    end
```

### 8.2 Encapsulation/Decapsulation Flow

```mermaid
sequenceDiagram
    participant S as Sender (Alice)
    participant R as Receiver (Bob)
    
    Note over R: KeyGen → (ek, dk)
    R->>S: Send ek (public key; 1,184 bytes)
    
    Note over S: m ← random 256 bits
    Note over S: (K, r) = G(m || H(ek))
    Note over S: Encrypt m using ek with randomness r
    Note over S: c = (Compress(Â^T·r̂ + e₁), Compress(t̂^T·r̂ + e₂ + ⌈q/2⌉·m))
    
    S->>R: Send c (ciphertext; 1,088 bytes)
    
    Note over R: Decrypt: m' = Decompress(v - ŝ^T·NTT(u))
    Note over R: Re-derive: (K', r') = G(m' || H(ek))
    Note over R: Re-encrypt → c'
    Note over R: If c' == c: output K = K'
    Note over R: Else: output K = KDF(z || H(c)) [implicit reject]
    
    Note over S,R: Both have shared secret K (32 bytes)
    Note over S,R: Use K to derive symmetric session keys (AES-256-GCM)
```

---

## Chapter 9 — Case Studies

### 9.1 Google Chrome: First Browser to Deploy ML-KEM

| Aspect | Detail |
|--------|--------|
| **When** | Chrome 124 (stable; April 2024) → ML-KEM-768 + X25519 hybrid as DEFAULT |
| **Predecessor** | Chrome 116 (Aug 2023): Kyber-768 draft + X25519 (experimental) |
| **Mechanism** | TLS 1.3 hybrid key exchange; new NamedGroup: X25519MLKEM768 |
| **Impact** | +~1 KB to ClientHello; +~1 KB to ServerHello; negligible latency impact |
| **Scale** | Billions of TLS connections per day using PQC |
| **Compatibility** | Some middleboxes broke on larger ClientHello → Chrome implemented fragmentation handling |
| **Lesson** | PQC deployment reveals "ossification" in network infrastructure (devices that reject oversized handshakes) |

### 9.2 Apple iMessage PQ3

| Aspect | Detail |
|--------|--------|
| **When** | iOS 17.4 (March 2024) |
| **Mechanism** | PQ3 protocol: ML-KEM-768 for initial key exchange + periodic PQC rekeying |
| **Innovation** | Post-compromise security: even if current key compromised, future messages protected via PQC ratcheting |
| **Architecture** | Hybrid: ECDH (P-256) + ML-KEM-768; both must be broken |
| **Deployment** | Automatic for all iMessage users (no opt-in required) |
| **Security level** | Apple classifies as "Level 3" (their highest; post-quantum + post-compromise) |

---

## Chapter 10 — Future Evolution

| Trend | Description | Timeline |
|:-----:|-------------|:--------:|
| **FIPS validation** | CMVP validation of ML-KEM implementations (required for US federal) | 2024-2025 |
| **Hardware acceleration** | ASIC/FPGA accelerators for NTT; lattice operations (HSMs; TPMs) | 2025-2027 |
| **ML-KEM in X.509** | PQC public keys in certificates; hybrid certificate format | 2025-2026 |
| **Pure PQC (no hybrid)** | Transition from hybrid (X25519+ML-KEM) to ML-KEM-only once confidence established | 2028-2030 |
| **Embedded/IoT** | Lightweight ML-KEM implementations for constrained devices | 2025-2027 |
| **Formal verification** | Machine-checked proofs of ML-KEM correctness (Jasmin; EasyCrypt; F*) | 2024-2026 |
| **Key size optimization** | Research on tighter parameters; potentially smaller keys in future versions | 2025+ |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What problem does ML-KEM solve, and what classical algorithm does it replace?

**A:** ML-KEM is a Key Encapsulation Mechanism — it allows two parties to establish a shared secret key over an insecure channel. It replaces RSA key transport and ECDH key agreement (X25519, P-256), which are vulnerable to Shor's algorithm on a quantum computer. ML-KEM's security is based on the Module-LWE lattice problem, which is believed to be hard even for quantum computers.

### Tier 2: Mid-Level

**Q2:** Why does ML-KEM use the Fujisaki-Okamoto (FO) transform, and what is "implicit rejection"?

**A:** The base encryption scheme (ML-KEM's inner PKE) is IND-CPA secure (secure against passive attacks). The FO transform upgrades this to IND-CCA2 security (secure against adaptive chosen-ciphertext attacks) by: (1) deriving encryption randomness deterministically from the message and public key, and (2) re-encrypting during decapsulation to verify ciphertext validity. "Implicit rejection" means: instead of outputting $\perp$ (failure) for invalid ciphertexts — which would enable a distinguishing oracle — the algorithm outputs a pseudorandom key $K = \text{KDF}(z \| H(c))$ derived from the secret $z$ and the ciphertext. The attacker cannot distinguish a valid from invalid decapsulation, preventing CCA attacks.

### Tier 3: Senior

**Q3:** Design the migration path from ECDH X25519 to ML-KEM-768 for a TLS-based microservices architecture handling 100K req/sec, considering: performance, certificate infrastructure, backward compatibility, and HSM constraints.

**A:**

| Phase | Action | Duration |
|:-----:|--------|:--------:|
| **1. Assessment** | Measure current handshake overhead; identify HSM models (PQC-capable?); catalog all TLS endpoints; test ML-KEM library performance on production hardware | 4 weeks |
| **2. Hybrid deployment** | Deploy X25519+ML-KEM-768 hybrid on edge proxies first (where TLS terminates). Most internal service-mesh traffic uses mTLS with short-lived certs — lower HNDL risk; can defer. Performance impact: +~1.5 KB per handshake; <1ms additional latency (NTT is fast); connection reuse means overhead is amortized | 8 weeks |
| **3. HSM strategy** | Current HSMs (e.g., Thales Luna, AWS CloudHSM) may not support ML-KEM in hardware. Strategy: (a) Use software ML-KEM for ephemeral key exchange (session keys; HSM not needed). (b) HSM for long-lived signing keys (ML-DSA migration is the HSM-critical path; ML-KEM ephemeral keys don't require HSM). (c) Plan HSM firmware upgrade for ML-KEM-1024 if key escrow/archival needed | Ongoing |
| **4. Certificate handling** | ML-KEM keys in certificates are large (~1.2 KB vs 32 B for X25519). Impact: certificate chain size increases. Mitigation: (a) Use hybrid certs (classical + PQC) during transition. (b) For service mesh: short-lived certs (SPIFFE); larger cert acceptable for mTLS. (c) Consider delegated credentials (smaller; short-lived) | 12 weeks |
| **5. Performance validation** | At 100K req/sec with persistent connections: handshake overhead only affects new connections (~5-10% of traffic). Total overhead: minimal (<0.1% CPU for ML-KEM at this rate). If connection churn is high: consider session resumption (PSK) to minimize full handshakes | 4 weeks |
| **6. Deprecate hybrid** | Once confidence in ML-KEM established (3-5 years post-deployment); all clients support ML-KEM; FIPS validation obtained → remove X25519 fallback; pure ML-KEM-768 | 2028+ |

---

## Chapter 12 — Cheat Sheet & Quick Reference

```
═══════════════════════════════════════════
FIPS 203: ML-KEM — QUICK REFERENCE
═══════════════════════════════════════════

STANDARD: NIST FIPS 203 (August 13, 2024)
BASIS: CRYSTALS-Kyber (NIST PQC winner)
PROBLEM: Module Learning With Errors (MLWE)
PURPOSE: Key Encapsulation (replaces RSA KEM; ECDH)
SECURITY: IND-CCA2

═══════════════════════════════════════════
PARAMETER SETS:
  ML-KEM-512:  pk=800B  ct=768B  Level 1
  ML-KEM-768:  pk=1184B ct=1088B Level 3 ★ RECOMMENDED
  ML-KEM-1024: pk=1568B ct=1568B Level 5

  All: shared secret = 32 bytes; n=256; q=3329

═══════════════════════════════════════════
OPERATIONS:
  KeyGen: seed → (ek, dk)
  Encaps: ek → (ciphertext, shared_secret)
  Decaps: dk + ciphertext → shared_secret

═══════════════════════════════════════════
KEY INTERNALS:
  Ring: Zq[x]/(x^256 + 1), q=3329
  NTT: fast polynomial multiplication
  CBD: Centered Binomial Distribution (noise sampling)
  FO transform: CPA→CCA2 upgrade + implicit rejection
  Compression: reduce ciphertext size (lossy)

═══════════════════════════════════════════
DEPLOYMENT (2024):
  Chrome 124+: X25519+ML-KEM-768 (hybrid TLS)
  Signal: PQXDH (X25519+ML-KEM-768)
  iMessage PQ3: ECDH+ML-KEM-768 (ratcheting)
  OpenSSH 9.x: hybrid key exchange
  AWS/Cloudflare: TLS PQC support

═══════════════════════════════════════════
HYBRID KEY EXCHANGE:
  combined_secret = KDF(classical_secret || pqc_secret)
  Both must be broken to compromise session
  Transition strategy: hybrid now → pure PQC later

═══════════════════════════════════════════
PERFORMANCE:
  KeyGen: ~50 μs (ML-KEM-768; x86-64 AVX2)
  Encaps: ~60 μs
  Decaps: ~65 μs
  Comparable to X25519 (~50 μs)
  
  Bandwidth: +2.2 KB per TLS handshake (vs X25519)

═══════════════════════════════════════════
vs CLASSICAL:
  X25519: 32B pk + 32B ct = 64B total
  ML-KEM-768: 1184B pk + 1088B ct = 2272B total
  Tradeoff: ~35× more bandwidth for quantum safety

═══════════════════════════════════════════
CNSA 2.0 REQUIREMENT:
  National security systems: ML-KEM-1024 by 2030
  General federal: ML-KEM-768 minimum
```

---

*End of Document — 02_NIST_FIPS_203_ML_KEM.md*
