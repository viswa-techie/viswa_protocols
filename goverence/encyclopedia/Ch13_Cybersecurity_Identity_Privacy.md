# Chapter 13 — Domain: Cybersecurity, Identity & Privacy Standards

## Universal Technology Standards Governance & Regulatory Architecture Encyclopedia
### First Edition, 2024–2026

---

## 13.1 Cybersecurity Standards Ecosystem Overview

### The Five Domains of Cybersecurity Standardization

```
┌────────────────────────────────────────────────────────────────┐
│           CYBERSECURITY STANDARDS DOMAINS                       │
├────────────────┬───────────────────┬───────────────────────────┤
│ 1. CRYPTO      │ 2. PROTOCOL SEC   │ 3. IDENTITY & ACCESS      │
│ FIPS (NIST)    │ TLS, IPsec, SSH   │ OAuth 2.x, OIDC, SAML    │
│ RFC (IETF)     │ WireGuard, QUIC   │ FIDO2/WebAuthn, SCIM      │
│ ISO/IEC 19790  │ DTLS, mTLS        │ SPIFFE, Kerberos          │
├────────────────┼───────────────────┼───────────────────────────┤
│ 4. RISK/GOV    │ 5. SW SUPPLY CHAIN│                           │
│ ISO 27001      │ SBOM (SPDX, CDX)  │                           │
│ NIST CSF 2.0   │ SLSA, Sigstore    │                           │
│ SOC 2, CIS     │ SSDF (NIST)       │                           │
└────────────────┴───────────────────┴───────────────────────────┘
```

### Governance Fragmentation in Cybersecurity

| Body     | Geography | Output Type              | Key Publications                  |
|----------|-----------|--------------------------|-----------------------------------|
| NIST     | USA       | FIPS (mandatory), SP (guidance) | FIPS 140-3, SP 800-53, CSF 2.0 |
| IETF     | Global    | RFC (protocol specifications) | TLS 1.3, OAuth, SSH, IPsec       |
| ISO/IEC  | Global    | International Standards   | 27001, 27002, 27701, 19790       |
| ENISA    | EU        | Guidelines, frameworks    | Threat landscape, NIS2 guidance   |
| OASIS    | Global    | Technical standards       | SAML, STIX/TAXII, KMIP           |
| FIDO     | Global    | Authentication specs      | FIDO2, CTAP2, Passkeys            |
| OpenSSF  | Global    | Frameworks, tooling       | SLSA, Scorecard, Sigstore         |

---

## 13.2 Cryptographic Algorithm Standards

### Symmetric Encryption

| Algorithm   | Key Sizes (bits) | Standard       | Governance | Status      | Notes                        |
|-------------|------------------|----------------|------------|-------------|------------------------------|
| AES         | 128/192/256      | FIPS 197       | NIST       | Current     | Rijndael — selected 2001     |
| ChaCha20    | 256              | RFC 8439       | IETF       | Current     | TLS 1.3, WireGuard, mobile   |
| 3DES        | 112/168          | FIPS 46-3      | NIST       | **Retired 2023** | Do not use for new systems |
| Blowfish    | 32–448           | —              | De-facto   | Legacy      | Replaced by AES              |
| Camellia    | 128/192/256      | RFC 3713       | IETF       | Active      | NTT/Mitsubishi; used in Japan|

### AES Modes of Operation

| Mode    | Full Name                       | Use Case                        | Provides Auth? |
|---------|---------------------------------|---------------------------------|----------------|
| ECB     | Electronic Codebook             | **NEVER use** (pattern leakage)| No             |
| CBC     | Cipher Block Chaining           | Legacy disk encryption          | No             |
| CTR     | Counter Mode                    | Stream cipher behavior          | No             |
| GCM     | Galois/Counter Mode             | **Recommended** — TLS, IPsec  | Yes (AEAD)     |
| CCM     | Counter with CBC-MAC            | IoT, Bluetooth, Wi-Fi           | Yes (AEAD)     |
| SIV     | Synthetic Initialization Vector | Nonce-misuse resistant          | Yes (AEAD)     |
| XTS     | XEX-based Tweaked-codebook with CipherText Stealing | Disk encryption (FIPS 140-3) | No |

### Asymmetric (Public Key) Cryptography

| Algorithm    | Problem Basis     | Standard             | Governance | Status         | Min Key Size (2024) |
|--------------|-------------------|----------------------|------------|----------------|---------------------|
| RSA          | Integer factoring | FIPS 186-5, RFC 8017| NIST/IETF  | Legacy (still used) | 3072-bit       |
| ECDSA        | ECDLP             | FIPS 186-5           | NIST       | Current        | P-256 (128-bit sec) |
| EdDSA (Ed25519) | Twisted Edwards | RFC 8032, FIPS 186-5| IETF/NIST | **Preferred**  | Ed25519 (128-bit)   |
| X25519       | ECDH on Curve25519| RFC 7748            | IETF       | **Preferred** for key exchange | — |
| DH (FFDHE)   | Discrete log      | RFC 7919            | IETF       | Current        | 3072-bit            |

### Hash Functions

| Algorithm  | Output (bits) | Standard   | Governance | Status           | Notes                      |
|------------|---------------|------------|------------|------------------|----------------------------|
| MD5        | 128           | RFC 1321   | IETF       | **BROKEN**       | Collision attacks since 2004|
| SHA-1      | 160           | FIPS 180-4 | NIST       | **Deprecated (2022)** | SHAttered attack 2017 |
| SHA-256    | 256           | FIPS 180-4 | NIST       | Current          | Most widely used           |
| SHA-384    | 384           | FIPS 180-4 | NIST       | Current          | TLS cipher suites          |
| SHA-512    | 512           | FIPS 180-4 | NIST       | Current          | Long-term security         |
| SHA-3-256  | 256           | FIPS 202   | NIST       | Current          | Keccak sponge construction |
| SHAKE128   | Variable      | FIPS 202   | NIST       | Current          | Extendable-output (XOF)   |
| BLAKE2b    | Up to 512     | RFC 7693   | IETF       | Current          | Faster than SHA-2; Argon2 uses it |
| BLAKE3     | 256           | —          | De-facto   | Current          | Parallelizable, very fast  |

### Post-Quantum Cryptography (NIST PQC — 2024)

| FIPS     | Algorithm Name | Algorithm Family | Based On          | Purpose                    | Security Basis        |
|----------|----------------|------------------|-------------------|----------------------------|-----------------------|
| FIPS 203 | ML-KEM         | Key Encapsulation| CRYSTALS-Kyber   | Replace ECDH/RSA key exchange | Module-LWE         |
| FIPS 204 | ML-DSA         | Digital Signature| CRYSTALS-Dilithium| Replace ECDSA/RSA signatures | Module-LWE         |
| FIPS 205 | SLH-DSA        | Digital Signature| SPHINCS+          | Conservative signature (hash-only) | Hash functions |
| FIPS 206 (draft) | FN-DSA  | Digital Signature| FALCON           | Compact signatures         | NTRU lattices        |

**Threat Model — "Harvest Now, Decrypt Later" (HNDL):**
```
Adversary records encrypted traffic TODAY (RSA/ECDH key exchange)
    ↓ (stores ciphertext for years)
Quantum computer available in 10–15 years
    ↓
Shor's algorithm breaks RSA/ECC → decrypt stored data
    
Impact: Long-lived secrets (medical records, state secrets, financial data)
must migrate to PQC NOW despite quantum computers not yet existing.
```

**Migration Timeline:**
- 2024: NIST PQC standards finalized (FIPS 203/204/205)
- 2025–2030: Hybrid mode (X25519 + ML-KEM combined) — TLS, SSH, VPN
- 2030–2035: Pure PQC deployment for new systems
- 2035: NIST targets deprecation of RSA-2048 and ECC P-256 for protecting long-lived secrets

---

## 13.3 TLS (Transport Layer Security)

### Full Entry

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Transport Layer Security                                       |
| Abbreviation                 | TLS (formerly SSL — Secure Sockets Layer)                     |
| Entry Type                   | Security protocol (authentication + confidentiality + integrity)|
| Governance Tier              | L3 (IETF)                                                     |
| Current Version              | TLS 1.3 (RFC 8446, August 2018)                              |
| Deprecated Versions          | SSL 2.0, SSL 3.0, TLS 1.0, TLS 1.1 (all per RFC 8996, 2021)|
| Minimum Acceptable           | TLS 1.2 (RFC 5246) — with modern cipher suites only          |
| Relationship to HTTPS        | HTTPS = HTTP over TLS (port 443)                              |
| Relationship to QUIC         | QUIC integrates TLS 1.3 handshake (RFC 9001)                 |

### TLS 1.3 vs. TLS 1.2

| Feature                    | TLS 1.2 (RFC 5246)              | TLS 1.3 (RFC 8446)              |
|----------------------------|---------------------------------|----------------------------------|
| Handshake RTT              | 2-RTT                           | 1-RTT (+ 0-RTT resumption)      |
| Forward secrecy            | Optional (RSA key exchange)     | **Mandatory** (ECDHE/DHE only)  |
| Cipher suites              | Many (including weak: RC4, 3DES)| 5 strong AEAD-only suites        |
| RSA key exchange           | Supported (no FS)               | **Removed**                      |
| Handshake encryption       | ClientHello/ServerHello in clear| Only ClientHello in clear        |
| Compression                | Supported (CRIME vulnerability) | **Removed**                      |
| Renegotiation              | Supported                       | **Removed** (KeyUpdate instead)  |

### TLS 1.3 Cipher Suites (Complete Set)

| Cipher Suite                        | AEAD          | Hash    | Key Exchange |
|-------------------------------------|---------------|---------|--------------|
| TLS_AES_128_GCM_SHA256             | AES-128-GCM   | SHA-256 | ECDHE/DHE    |
| TLS_AES_256_GCM_SHA384             | AES-256-GCM   | SHA-384 | ECDHE/DHE    |
| TLS_CHACHA20_POLY1305_SHA256       | ChaCha20-Poly1305 | SHA-256 | ECDHE/DHE |
| TLS_AES_128_CCM_SHA256             | AES-128-CCM   | SHA-256 | ECDHE/DHE    |
| TLS_AES_128_CCM_8_SHA256           | AES-128-CCM-8 | SHA-256 | ECDHE/DHE    |

### X.509 Certificate Standard

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Standard                     | ITU-T X.509 v3 (ISO/IEC 9594-8)                              |
| Internet Profile             | RFC 5280 (PKIX — Internet X.509 PKI Certificate and CRL Profile) |
| Governance Tier              | L1 (ITU-T) + L3 (IETF PKIX WG)                              |
| Certificate Format           | ASN.1 DER encoding (binary) or PEM (Base64-armored DER)      |
| Key Fields                   | Subject, Issuer, Validity, Public Key, Extensions, Signature  |
| Critical Extensions          | BasicConstraints, KeyUsage, SubjectAltName (SAN), ExtendedKeyUsage |
| Revocation                   | CRL (RFC 5280), OCSP (RFC 6960), OCSP Stapling (RFC 6066)   |
| Certificate Transparency     | RFC 9162 (CT 2.0) — all public certs must be logged          |

### ACME (Automated Certificate Management Environment)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Standard                     | RFC 8555                                                      |
| Governance                   | IETF (L3)                                                     |
| Implementation               | Let's Encrypt (ISRG) — largest public CA (~400M active certs)|
| Purpose                      | Automated domain validation + certificate issuance            |
| Validation Methods           | HTTP-01 (/.well-known/acme-challenge), DNS-01, TLS-ALPN-01   |

---

## 13.4 Identity and Access Management (IAM)

### FIDO2 / WebAuthn / Passkeys

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | FIDO2 = WebAuthn (W3C) + CTAP2 (FIDO Alliance)               |
| WebAuthn                     | Web Authentication API — W3C Recommendation (Level 3, 2023)   |
| CTAP2                        | Client-to-Authenticator Protocol 2 — FIDO Alliance            |
| Entry Type                   | Phishing-resistant authentication protocol                    |
| Governance Tier              | L3 (W3C) + L3 (FIDO Alliance)                                |
| Passkeys                     | Discoverable FIDO2 credentials (synced via iCloud/Google/1Password or device-bound) |
| Phishing Resistance          | **Yes** — credential bound to origin (RP ID); cannot be phished |
| Platform Authenticators      | TouchID, FaceID, Windows Hello, Android biometric             |
| Roaming Authenticators       | YubiKey, Titan Key, NFC keys                                  |

### OAuth 2.x / OpenID Connect Stack

```
RFC 6749: OAuth 2.0 Authorization Framework
    ↓ (identity layer added)
OpenID Connect 1.0: Authentication + ID Token (JWT)
    ↓ (security hardened)
OAuth 2.1 (draft-ietf-oauth-v2-1): Consolidates best practices
    ↓ (financial grade)
FAPI 2.0: Financial-grade API security profile (OpenID Foundation)
```

### OAuth 2.0 Grant Types

| Grant Type                 | RFC         | Use Case                              | Status (2024)    |
|----------------------------|-------------|---------------------------------------|------------------|
| Authorization Code + PKCE  | RFC 7636    | Web/mobile/SPA (universal grant)     | **Recommended**  |
| Client Credentials         | RFC 6749    | Machine-to-machine (M2M)            | Current          |
| Device Authorization       | RFC 8628    | TV, CLI, limited-input devices       | Current          |
| Implicit                   | RFC 6749    | SPA (token in fragment)              | **DEPRECATED** (removed in 2.1) |
| Resource Owner Password    | RFC 6749    | Direct password grant                | **DEPRECATED** (removed in 2.1) |
| Token Exchange             | RFC 8693    | Token impersonation/delegation       | Current          |
| CIBA                       | OIDC CIBA   | Backchannel-initiated authentication | Current (banking)|

### SAML 2.0 vs. OpenID Connect

| Feature           | SAML 2.0 (OASIS)              | OpenID Connect (OIDF)            |
|-------------------|-------------------------------|----------------------------------|
| Token format      | XML assertion (signed)        | JWT (JSON Web Token)             |
| Transport         | SOAP, HTTP-Redirect/POST      | REST (HTTPS + JSON)              |
| Token size        | Large (XML overhead)          | Compact (Base64url-encoded JWT)  |
| Discovery         | Metadata XML                  | .well-known/openid-configuration |
| Mobile/API        | Poor (XML parsing, large tokens) | **Excellent** (JSON, compact)  |
| Enterprise SSO    | **Dominant** (legacy apps)    | Growing (modern apps)            |
| Best for          | Legacy enterprise, government | Modern apps, APIs, mobile        |

### SCIM (Provisioning)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | System for Cross-domain Identity Management                   |
| Standard                     | RFC 7642 (concepts), RFC 7643 (schema), RFC 7644 (protocol)  |
| Governance Tier              | L3 (IETF)                                                     |
| Purpose                      | Automated user provisioning/deprovisioning across SaaS apps   |
| Operations                   | Create, Read, Update, Delete, Patch, Search (REST-based)     |
| Typical Flow                 | IdP (Okta/Azure AD) → SCIM API → Target SaaS application    |

---

## 13.5 Zero Trust Architecture

### NIST SP 800-207 — Seven Tenets

| Tenet | Principle                                                            |
|-------|----------------------------------------------------------------------|
| 1     | All data sources and computing services are considered resources      |
| 2     | All communication is secured regardless of network location          |
| 3     | Access to resources is granted on a per-session basis                |
| 4     | Access is determined by dynamic policy (identity + device + context) |
| 5     | Enterprise monitors and measures integrity/security of all assets    |
| 6     | All resource authentication and authorization is dynamic and enforced|
| 7     | Enterprise collects information about current state for security improvement |

### Zero Trust Standards and Implementations

| Standard/Project    | Governance      | Tier | Description                              |
|---------------------|-----------------|------|------------------------------------------|
| NIST SP 800-207     | NIST            | L2   | Authoritative ZTA reference architecture |
| BeyondCorp          | Google          | L6   | Original ZTA implementation (2011)       |
| SPIFFE              | CNCF (Graduated)| L5   | Workload identity specification          |
| SPIRE               | CNCF            | L5   | SPIFFE Runtime Environment (implementation)|
| mTLS                | IETF (RFC 8446) | L3   | Mutual TLS — bidirectional cert auth     |
| NIST SP 800-207A    | NIST            | L2   | Zero Trust for 5G networks               |

### SPIFFE (Secure Production Identity Framework for Everyone)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Entry Type                   | Workload identity specification                               |
| Governance                   | CNCF Graduated project                                        |
| SPIFFE ID format             | spiffe://trust-domain/workload-path                           |
| SVID types                   | X.509-SVID (certificate), JWT-SVID (token)                   |
| Purpose                      | Cryptographic identity for workloads (not humans) in zero trust|
| Integrations                 | Envoy, Istio, Consul Connect, AWS IAM Roles Anywhere         |

---

## 13.6 Vulnerability Management Standards

### CVE / CWE / CVSS Ecosystem

| Artifact | Full Name                             | Governance      | Purpose                              |
|----------|---------------------------------------|-----------------|--------------------------------------|
| CVE      | Common Vulnerabilities and Exposures  | MITRE / CISA    | Unique identifier for known vulns    |
| CWE      | Common Weakness Enumeration           | MITRE           | Classification of software weaknesses|
| CVSS     | Common Vulnerability Scoring System   | FIRST           | Severity scoring (0.0–10.0)          |
| NVD      | National Vulnerability Database       | NIST            | CVE enrichment (CVSS scores, CPE)    |
| EPSS     | Exploit Prediction Scoring System     | FIRST           | Probability of exploitation in 30 days|
| VEX      | Vulnerability Exploitability eXchange | CISA/NTIA       | Machine-readable status for SBOM     |
| KEV      | Known Exploited Vulnerabilities       | CISA            | Authoritative list of actively exploited CVEs |

### CVSS v4.0 (2023)

| Metric Group    | Metrics                                              | Purpose                    |
|-----------------|------------------------------------------------------|----------------------------|
| Base            | Attack Vector (AV), Attack Complexity (AC), Attack Requirements (AT), Privileges Required (PR), User Interaction (UI) | Exploitability |
| Base (Impact)   | Vulnerable System: Confidentiality (VC), Integrity (VI), Availability (VA); Subsequent System: SC, SI, SA | Impact severity |
| Threat          | Exploit Maturity (E)                                 | Real-world threat context  |
| Environmental   | Security Requirements (CR, IR, AR), Modified Base metrics | Organization-specific context |
| Supplemental    | Safety (S), Automatable (AU), Recovery (R), Value Density (V), Provider Urgency (U) | Additional context |

### CVSS Score Ranges

| Score Range | Severity | Action Guidance                        |
|-------------|----------|----------------------------------------|
| 0.0         | None     | No action required                     |
| 0.1–3.9     | Low      | Patch in regular cycle                 |
| 4.0–6.9     | Medium   | Patch within 30 days                   |
| 7.0–8.9     | High     | Patch within 7 days                    |
| 9.0–10.0    | Critical | Emergency patch immediately            |

---

## 13.7 Supply Chain Security

### SBOM (Software Bill of Materials)

| Format      | Full Name                        | Governance           | Tier | Format    | Focus                    |
|-------------|----------------------------------|----------------------|------|-----------|--------------------------|
| SPDX        | Software Package Data Exchange   | Linux Foundation     | L5   | JSON/XML/RDF/Tag-value | License compliance + security |
| CycloneDX   | CycloneDX                        | OWASP                | L5   | JSON/XML  | Security focus (VEX, VDR)|
| SWID Tags   | Software Identification Tags     | ISO/IEC 19770-2      | L1   | XML       | Software asset management|

### SLSA (Supply-chain Levels for Software Artifacts)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Supply-chain Levels for Software Artifacts (pronounced "salsa")|
| Governance                   | OpenSSF (Linux Foundation)                                    |
| Governance Tier              | L5                                                            |
| Purpose                      | Framework for ensuring integrity of software build pipeline   |

| Level    | Requirements                                              | Protection Against         |
|----------|-----------------------------------------------------------|-----------------------------|
| SLSA 1   | Provenance generated (build metadata exists)             | Post-build tampering        |
| SLSA 2   | Hosted build service + authenticated provenance          | Build compromise (basic)    |
| SLSA 3   | Hardened build platform + unforgeable provenance          | Build platform compromise   |

### Sigstore

| Component    | Purpose                                           | Analogy              |
|--------------|---------------------------------------------------|----------------------|
| Cosign       | Container/artifact signing                        | GPG for containers   |
| Fulcio       | Short-lived certificate issuance (OIDC → cert)  | Let's Encrypt for code signing |
| Rekor        | Transparency log (immutable record)               | CT log for signatures|

### NIST SSDF (Secure Software Development Framework)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Standard                     | NIST SP 800-218                                               |
| Purpose                      | Practices for secure software development lifecycle           |
| Practices                    | Prepare Organization (PO), Protect Software (PS), Produce Well-Secured Software (PW), Respond to Vulnerabilities (RV) |
| Mandated By                  | US EO 14028 (May 2021 — Improving Cybersecurity)             |

---

## 13.8 Security Frameworks Comparison

| Framework        | Governance | Tier | Certifiable? | Scope                    | Structure                |
|------------------|------------|------|--------------|--------------------------|--------------------------|
| ISO/IEC 27001:2022 | ISO      | L1   | **Yes** (accredited certification) | ISMS (any organization) | 11 clauses + Annex A (93 controls) |
| NIST CSF 2.0     | NIST       | L2   | No (self-assessment)    | All organizations        | 6 Functions → Categories → Subcategories |
| SOC 2 Type II    | AICPA      | L4   | **Yes** (audit report)  | Service organizations    | 5 Trust Service Criteria |
| CIS Benchmarks   | CIS        | L5   | No (hardening guides)   | System configurations    | Per-technology benchmarks|
| COBIT 2019       | ISACA      | L4   | No (governance framework)| IT governance            | 40 governance objectives |

### ISO/IEC 27001:2022

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Information security, cybersecurity and privacy protection — ISMS requirements |
| Standard Number              | ISO/IEC 27001:2022 (4th edition)                              |
| Governance Tier              | L1 (ISO/IEC JTC1 SC27)                                       |
| Certification                | Third-party accredited certification (3-year cycle + surveillance audits) |
| Annex A Controls             | 93 controls in 4 themes: Organizational (37), People (8), Physical (14), Technological (34) |
| Companion Standard           | ISO/IEC 27002:2022 (implementation guidance for controls)     |
| Key Change from 2013         | Restructured Annex A; added 11 new controls (threat intelligence, cloud security, data masking, etc.) |

---

## 13.9 Network Security Protocols

| Protocol    | Standard           | Purpose                           | Status        | Notes                    |
|-------------|--------------------|------------------------------------|---------------|--------------------------|
| IPsec       | RFC 4301–4309      | Network-layer VPN encryption       | Current       | IKEv2 for key exchange   |
| IKEv2       | RFC 7296           | Key exchange for IPsec             | Current       | Replaced IKEv1           |
| WireGuard   | —                  | Modern VPN (kernel-level)          | Current       | ChaCha20, Curve25519, Noise protocol |
| SSH 2.0     | RFC 4251–4254      | Secure remote shell + tunneling    | Current       | Ed25519 keys preferred   |
| OpenPGP     | RFC 9580           | Email/file encryption + signatures | Current (2024)| Updated from RFC 4880    |
| MACsec      | IEEE 802.1AE       | Layer-2 Ethernet encryption        | Current       | GCM-AES-256              |
| DNSSEC      | RFC 4033–4035      | DNS authentication (integrity)     | Current       | Does NOT encrypt (use DoH/DoT) |
| DoH         | RFC 8484           | DNS over HTTPS                     | Current       | Privacy for DNS queries  |
| DoT         | RFC 7858           | DNS over TLS                       | Current       | Port 853                 |

### IPsec vs. WireGuard vs. OpenVPN

| Feature        | IPsec (IKEv2)        | WireGuard                 | OpenVPN                  |
|----------------|----------------------|---------------------------|--------------------------|
| Governance     | IETF (L3)           | De-facto (L8)             | OpenVPN Inc (L6)         |
| Code size      | Large (~400K lines)  | **~4K lines** (kernel)    | ~100K lines              |
| Cipher agility | Many options         | Fixed: ChaCha20, Curve25519| Configurable (OpenSSL)  |
| Performance    | Good (hardware accel)| **Excellent** (minimal overhead) | Moderate (userspace)|
| Key exchange   | IKEv2 (X.509 or PSK)| Static public keys (Noise)| TLS 1.2/1.3             |
| NAT traversal  | NAT-T (RFC 3947)    | Built-in (UDP)            | Built-in                 |
| Formal verification | No             | **Yes** (Tamarin prover)  | No                       |

---

## 13.10 Secure Coding Standards

### OWASP Top 10 (2021)

| Rank | Category                                | CWE Examples                    |
|------|-----------------------------------------|---------------------------------|
| A01  | Broken Access Control                   | CWE-200, CWE-284, CWE-639     |
| A02  | Cryptographic Failures                  | CWE-259, CWE-327, CWE-331     |
| A03  | Injection                               | CWE-79 (XSS), CWE-89 (SQLi), CWE-78 |
| A04  | Insecure Design                         | CWE-209, CWE-256, CWE-501     |
| A05  | Security Misconfiguration               | CWE-16, CWE-611, CWE-1004     |
| A06  | Vulnerable and Outdated Components      | CWE-1104                        |
| A07  | Identification and Authentication Failures | CWE-287, CWE-384, CWE-613  |
| A08  | Software and Data Integrity Failures    | CWE-345, CWE-502, CWE-829     |
| A09  | Security Logging and Monitoring Failures| CWE-778, CWE-117               |
| A10  | Server-Side Request Forgery (SSRF)      | CWE-918                         |

### IEC 62443 (Industrial Cybersecurity)

| Part       | Title                                      | Audience              |
|------------|--------------------------------------------|-----------------------|
| 62443-1-x  | General concepts and models                | All                   |
| 62443-2-x  | Policies and procedures                    | Asset owners          |
| 62443-3-x  | System requirements                        | System integrators    |
| 62443-4-1  | Secure product development lifecycle       | Component suppliers   |
| 62443-4-2  | Technical security requirements            | Component suppliers   |

**Security Levels (SL):**

| Level | Description                                              | Threat Actor              |
|-------|----------------------------------------------------------|---------------------------|
| SL 1  | Protection against casual/unintentional violation        | Accidental errors         |
| SL 2  | Protection against intentional violation using simple means| Script kiddies          |
| SL 3  | Protection against intentional violation using sophisticated means | Skilled hackers |
| SL 4  | Protection against intentional violation using nation-state resources | APT/nation-state |

---

## 13.11 Key Management Standards

| Standard    | Full Name                          | Governance | Purpose                              |
|-------------|-------------------------------------|------------|--------------------------------------|
| KMIP        | Key Management Interoperability Protocol | OASIS (L3) | Standardized key lifecycle management |
| PKCS#11     | Cryptographic Token Interface      | OASIS (L3) | HSM/token API (C-language interface) |
| PKCS#12     | Personal Information Exchange      | RFC 7292   | Certificate + private key bundle (.p12/.pfx) |
| PKCS#7/CMS  | Cryptographic Message Syntax      | RFC 5652   | Signed/encrypted data format         |

---

## 13.12 Key Disambiguation Notes

### 1. Authentication (AuthN) vs. Authorization (AuthZ)

| Concept          | Authentication                    | Authorization                      |
|------------------|-----------------------------------|------------------------------------|
| Question         | "WHO are you?"                   | "WHAT can you do?"                |
| Protocol         | OIDC, SAML, FIDO2, Kerberos     | OAuth 2.0, RBAC/ABAC policies    |
| Token            | ID Token (JWT — sub claim)       | Access Token (JWT — scope claim) |
| Failure response | 401 Unauthorized                  | 403 Forbidden                     |

### 2. OAuth 2.0 vs. OpenID Connect

- **OAuth 2.0** = authorization framework (gives ACCESS to resources)
- **OpenID Connect** = identity layer ON TOP of OAuth (tells you WHO the user is)
- OAuth alone does NOT authenticate — it delegates access
- OIDC adds: ID Token (JWT), UserInfo endpoint, Discovery, Dynamic Registration

### 3. TLS vs. SSL vs. HTTPS

- **SSL 2.0/3.0** = obsolete (Netscape, 1990s) — DO NOT USE
- **TLS 1.0/1.1** = deprecated (RFC 8996, 2021) — DO NOT USE
- **TLS 1.2** = minimum acceptable (with modern cipher suites)
- **TLS 1.3** = current best practice
- **HTTPS** = HTTP + TLS (not a separate protocol — just HTTP tunneled over TLS)
- "SSL certificate" is a MISNOMER — correctly called "TLS certificate" or "X.509 certificate"

### 4. CVSS Score vs. Risk

- CVSS measures SEVERITY (technical impact + exploitability)
- CVSS does NOT measure RISK (risk = severity × likelihood × business context)
- A CVSS 9.8 in an isolated test system may be lower risk than CVSS 5.0 in production
- Use EPSS (exploit probability) + CVSS + asset value for actual risk prioritization

---

*End of Chapter 13 — Cybersecurity, Identity & Privacy Standards*
*Next: Chapters 14 & 15 — Cloud/API Standards & Hardware/Semiconductor Standards*
