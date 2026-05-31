# ╔══════════════════════════════════════════════════════════════════════════════╗
# ║  UNIVERSAL PROTOCOL ENCYCLOPEDIA                                            ║
# ║  Part 19: SECURITY PROTOCOLS                                                ║
# ║  TLS, IPsec, MACsec, SecOC, WPA3, HSM, Secure Boot, ISO 26262            ║
# ╚══════════════════════════════════════════════════════════════════════════════╝

---

## CATEGORY OVERVIEW

Security protocols protect data confidentiality, integrity, and authenticity across all communication layers. From TLS (protecting web traffic) to MACsec (encrypting Ethernet frames) to automotive SecOC (authenticating CAN messages), security is now a mandatory requirement in every domain.

---

## PROTOCOL FAMILY TABLE

| Protocol | Layer | Purpose | Domain | Status |
|----------|-------|---------|--------|--------|
| TLS 1.3 | L5-L7 | Transport encryption | Internet/Cloud | Standard |
| IPsec | L3 | Network encryption | VPN/Enterprise | Active |
| MACsec (802.1AE) | L2 | Ethernet frame encryption | Datacenter/Auto | Growing |
| WPA3 | L2 | WiFi security | Wireless | Standard |
| SecOC (AUTOSAR) | Data layer | CAN/Ethernet PDU auth | Automotive | Mandated |
| Secure Boot | Boot chain | Firmware integrity | Universal | Standard |
| ARM TrustZone | HW isolation | TEE (Trusted Execution) | Mobile/Embedded | Active |
| OP-TEE | TEE OS | Trusted applications | Embedded/Auto | Active |
| DICE (TCG) | HW root | Device identity/attestation | IoT/Embedded | Growing |
| DMTF SPDM | Transport | Device authentication | Server/PCIe | Growing |
| WireGuard | L3 | Modern VPN (simple IPsec alt) | VPN | Growing |

---

## TLS 1.3

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Transport Layer Security 1.3 |
| Standard | RFC 8446 (2018) |
| Purpose | Encrypted, authenticated transport |
| Handshake | 1-RTT (0-RTT for resumption) |
| Ciphers | AES-128-GCM, AES-256-GCM, ChaCha20-Poly1305 |
| Key Exchange | ECDHE (X25519, P-256) |
| Authentication | X.509 certificates (RSA, ECDSA, Ed25519) |

### TLS 1.3 Handshake (1-RTT)
```
Client                                    Server
  │── ClientHello ────────────────────────►│
  │   (supported ciphers, key_share,       │
  │    supported_versions)                  │
  │                                         │
  │◄── ServerHello + EncryptedExtensions ──│
  │    Certificate + CertificateVerify      │
  │    Finished                             │
  │                                         │
  │── Finished ───────────────────────────►│
  │                                         │
  │◄═══════ Application Data ════════════►│
  │         (encrypted with AES-GCM)        │
```

### TLS 1.3 vs 1.2 Improvements
| Feature | TLS 1.2 | TLS 1.3 |
|---------|---------|---------|
| Handshake | 2-RTT | 1-RTT (0-RTT resumption) |
| Removed | RSA key exchange, CBC, RC4, SHA-1 | — (only secure options) |
| PFS | Optional | Mandatory (always ECDHE) |
| Encrypted | After handshake | Certificate encrypted too |
| Complexity | Many cipher combos | 5 cipher suites only |

---

## IPsec

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Internet Protocol Security |
| Standard | RFC 4301-4309 |
| Mode | Transport (encrypt payload) or Tunnel (encrypt whole packet) |
| Protocols | ESP (Encapsulating Security Payload), AH (Authentication Header) |
| Key Exchange | IKEv2 (RFC 7296) |
| Use | Site-to-site VPN, remote access VPN |

### IPsec Modes
```
Transport Mode (host-to-host):
[IP Header][ESP Header][Original Payload (encrypted)][ESP Trailer]

Tunnel Mode (gateway-to-gateway):
[New IP Header][ESP Header][Original IP Header + Payload (encrypted)][ESP Trailer]
```

---

## MACsec (IEEE 802.1AE)

### Protocol Identity
| Field | Value |
|-------|-------|
| Full Name | Media Access Control Security |
| Standard | IEEE 802.1AE |
| Layer | L2 (Ethernet frame level) |
| Purpose | Encrypt + authenticate Ethernet frames hop-by-hop |
| Overhead | 16 bytes (SecTAG) + 16 bytes (ICV) = 32 bytes per frame |
| Algorithm | AES-128-GCM or AES-256-GCM |
| Use | Datacenter (switch-to-switch), Automotive Ethernet |

### MACsec in Automotive
- Automotive Ethernet security between ECUs
- Complements SecOC (which works at PDU level)
- Hardware acceleration in automotive Ethernet switches (Marvell, NXP)
- Protection against: wire-tapping, message injection on Ethernet segments

---

## SECURE BOOT CHAIN

### Concept
```
┌────────────────────────────────────────────────────────────────────┐
│  SECURE BOOT CHAIN (Root of Trust → Application)                    │
├────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  ┌─────────────┐  verifies  ┌─────────────┐  verifies  ┌───────┐ │
│  │ ROM (HW RoT)│──────────►│ Bootloader  │──────────►│ Kernel│ │
│  │ (immutable) │            │ (signed)    │            │(signed)│ │
│  └─────────────┘            └─────────────┘            └───┬───┘ │
│                                                             │      │
│                              verifies                       ▼      │
│                             ┌──────────────────────────────────┐  │
│                             │  Root FS / System Image (dm-verity)│  │
│                             └──────────────────────────────────┘  │
│                                                                     │
│  Each stage verifies the NEXT stage's signature before executing   │
│  If verification fails → HALT (don't boot compromised code)       │
└────────────────────────────────────────────────────────────────────┘
```

### Android Verified Boot (AVB)
- `vbmeta`: Contains hash/signature of boot, system, vendor partitions
- dm-verity: Kernel verifies every block read from system partition
- Rollback protection: Prevents downgrade to vulnerable versions
- SA8295P AAOS: Full secure boot chain (Qualcomm RoT → TZ → ABL → kernel)

---

## ARM TRUSTZONE

### Architecture
```
┌─────────────────────────────────────────────────────────────────┐
│                      ARM Processor                                │
├───────────────────────────┬─────────────────────────────────────┤
│     Normal World          │      Secure World (TrustZone)        │
│                           │                                      │
│  ┌─────────────────┐     │  ┌─────────────────────────────┐    │
│  │ Linux / Android  │     │  │ Secure OS (OP-TEE / QSEE)  │    │
│  │                  │     │  │                             │    │
│  │ Apps, Services   │     │  │ Trusted Apps (DRM, crypto,  │    │
│  │                  │     │  │  secure storage, keymaster)  │    │
│  └─────────────────┘     │  └─────────────────────────────┘    │
│                           │                                      │
├───────────────────────────┤  Hardware-enforced isolation         │
│  NS=1 (Non-Secure)       │  NS=0 (Secure)                      │
│  Cannot access Secure     │  Can access both worlds             │
└───────────────────────────┴─────────────────────────────────────┘
         ↕ SMC (Secure Monitor Call) ↕
```

### TrustZone in AAOS/SA8295P
- **Keymaster**: Cryptographic key storage (hardware-backed)
- **Gatekeeper**: PIN/pattern verification
- **DRM (Widevine L1)**: Protected media path
- **Secure boot**: First-stage verification
- **RPMB access**: Replay-protected storage key management

---

## AUTOMOTIVE SECURITY STACK

```
┌─────────────────────────────────────────────────────────────────┐
│  AUTOMOTIVE SECURITY LAYERS                                      │
├─────────────────────────────────────────────────────────────────┤
│  Application Security: Code signing, sandboxing, SELinux         │
├─────────────────────────────────────────────────────────────────┤
│  Communication Security:                                         │
│  ├── SecOC: PDU authentication (CAN/Ethernet)                   │
│  ├── MACsec: Ethernet frame encryption                          │
│  ├── TLS: Cloud connectivity, DoIP                              │
│  └── IPsec: V2X, backend VPN                                    │
├─────────────────────────────────────────────────────────────────┤
│  Platform Security:                                              │
│  ├── Secure Boot: verified boot chain                           │
│  ├── TEE (TrustZone): key storage, DRM                         │
│  ├── HSM: hardware security module (SHE/EVITA)                  │
│  └── Secure storage: RPMB, encrypted partitions                 │
├─────────────────────────────────────────────────────────────────┤
│  Hardware Root of Trust: OTP fuses, PUF, immutable ROM          │
└─────────────────────────────────────────────────────────────────┘
```

---

## FLASH CARDS (12)

| # | Front | Back |
|---|-------|------|
| 1 | TLS 1.3 handshake RTT? | 1-RTT (0-RTT for resumption) |
| 2 | TLS 1.3 key exchange? | Always ECDHE (mandatory PFS) |
| 3 | IPsec modes? | Transport (encrypt payload) + Tunnel (encrypt entire packet) |
| 4 | MACsec layer? | L2 — encrypts Ethernet frames hop-by-hop |
| 5 | SecOC purpose? | Authenticate AUTOSAR PDUs (MAC + freshness) |
| 6 | Secure Boot chain? | ROM → Bootloader → Kernel → Rootfs (each verifies next) |
| 7 | dm-verity? | Block-level integrity checking of system partition |
| 8 | TrustZone isolation? | Hardware: Normal World (NS=1) / Secure World (NS=0) |
| 9 | ARM TrustZone access? | Secure can access Normal, Normal cannot access Secure |
| 10 | Keymaster location? | TrustZone (Secure World) — hardware-backed key storage |
| 11 | WPA3 improvement? | SAE (Simultaneous Auth of Equals) replaces PSK, prevents offline attacks |
| 12 | HSM in automotive? | Hardware Security Module — tamper-resistant crypto processor |

---

## INTERVIEW QUESTIONS (3)

**Q1: Explain the TLS 1.3 handshake and why it's faster than TLS 1.2.**
A: TLS 1.2: 2-RTT (1 for TCP + 1 for TLS key exchange). TLS 1.3: 1-RTT — client sends key_share in ClientHello (guesses server's preferred group). Server responds with its key_share + encrypted certificate in one flight. If guess is wrong, server sends HelloRetryRequest (2-RTT fallback, rare). 0-RTT: resumption uses pre-shared key from previous session to send application data in first flight (risk: replay, so used carefully). Faster because: fewer round-trips + removed negotiation (fixed good options only).

**Q2: How does Android Verified Boot protect against persistent rootkits?**
A: Chain of trust: (1) Hardware RoT (fused key) verifies bootloader signature. (2) Bootloader verifies kernel + ramdisk (vbmeta hash tree). (3) Kernel uses dm-verity to verify every block read from system/vendor partitions against Merkle tree. If ANY verification fails → boot halts or shows warning. Rootkit cannot: modify system partition (dm-verity detects), modify bootloader (RoT detects), downgrade (rollback index in tamper-proof storage). Even with physical access, attacker cannot install persistent malware that survives reboot.

**Q3: Why does automotive need both SecOC and MACsec — aren't they redundant?**
A: Different threat models and scopes: SecOC: authenticates PDUs end-to-end (source ECU → destination ECU), regardless of intermediate network. Protects against: compromised switch/gateway injecting fake messages. Works on ANY bus (CAN, Ethernet). Lightweight (truncated MAC). MACsec: encrypts+authenticates Ethernet frames hop-by-hop (between directly connected devices). Protects against: wire-tapping (confidentiality), injection on physical medium. Does NOT protect through switches that terminate MACsec. Together: MACsec protects physical links (prevent eavesdropping + injection on wire), SecOC ensures message authenticity end-to-end even if an intermediate node is compromised.

---

END OF PART 19 — SECURITY PROTOCOLS
