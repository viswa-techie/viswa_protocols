# CHAPTER 13 — DOMAIN: CYBERSECURITY, IDENTITY & PRIVACY STANDARDS
# Prompt for: Universal Technology Standards Governance Encyclopedia
# Depends on: MASTER_PROMPT.md, Chapters 2, 3, 10

---

## CHAPTER SCOPE

**Domain Coverage — Cybersecurity, Identity & Privacy:**
- Cryptographic standards: AES, RSA, ECC, SHA-2/3, HMAC, PBKDF2, Argon2
- Post-quantum cryptography: FIPS 203/204/205 (NIST PQC)
- PKI and certificate standards: X.509, PKIX (RFC 5280), ACME
- TLS/SSL: TLS 1.2, TLS 1.3, DTLS, mTLS
- Authentication: FIDO2, WebAuthn, passkeys, TOTP, HOTP
- Authorization: OAuth 2.x, OpenID Connect, SAML 2.0, SCIM
- Key management: KMIP (OASIS), PKCS#11, HSM standards
- Zero trust: NIST SP 800-207, BeyondCorp, SPIFFE/SPIRE
- Supply chain security: SLSA, SBOM (SPDX, CycloneDX), Sigstore
- Vulnerability management: CVE, CWE, CVSS, NVD, VEX
- Security frameworks: ISO/IEC 27001, NIST CSF 2.0, SOC 2, CIS Benchmarks
- Privacy standards: ISO/IEC 29101, ISO/IEC 27701, GDPR (see Chapter 10)
- Secure coding: OWASP Top 10, CERT C, SEI CERT standards
- Network security protocols: IPsec, IKEv2, WireGuard, SSH, OpenPGP
- Automotive cybersecurity: ISO/SAE 21434 (see Chapter 11), IEC 62443
- Industrial cybersecurity: IEC 62443 (see Chapter 10 and 11)

---

## GENERATION INSTRUCTIONS FOR THIS CHAPTER

Act as a world-class cybersecurity architect and standards specialist.
Generate the complete **Chapter 13: Cybersecurity, Identity & Privacy Standards** encyclopedia.

### SECTION 13.1 — CYBERSECURITY STANDARDS ECOSYSTEM OVERVIEW

Explain:
- The five domains of cybersecurity standardization:
  1. **Cryptographic primitives** — algorithm standards (FIPS, IETF)
  2. **Protocol security** — TLS, IPsec, SSH (IETF)
  3. **Identity and Access Management** — AuthN/AuthZ (IETF, FIDO, OASIS)
  4. **Risk and governance frameworks** — ISO 27001, NIST CSF (ISO, NIST)
  5. **Software and supply chain security** — SBOM, SLSA, SSDF (CNCF, NIST)
- The governance fragmentation problem in cybersecurity: NIST (US), ENISA (EU), ISO (global), IETF (internet) all produce overlapping guidance
- The "alphabet soup" problem: FIPS, SP, RFC, EN, ISO/IEC, TR, WG, CMVP — how to navigate
- The certification vs. compliance distinction (ISO 27001 certified org ≠ GDPR compliant org)
- The shift from compliance-first to risk-based security (NIST CSF, ISO 27001:2022)

### SECTION 13.2 — CRYPTOGRAPHIC ALGORITHM STANDARDS

**Symmetric Encryption:**

| Algorithm  | Key Sizes    | Standard      | Governance | Status     | Notes                         |
|------------|--------------|---------------|------------|------------|-------------------------------|
| AES        | 128/192/256  | FIPS 197      | NIST       | Current    | Rijndael — selected 2001      |
| 3DES       | 112/168      | FIPS 46-3     | NIST       | Deprecated | Retired 2023; legacy only     |
| ChaCha20   | 256          | RFC 8439      | IETF       | Current    | Used in TLS 1.3, WireGuard    |
| Blowfish   | 32–448       | —             | De-facto   | Legacy     | Replaced by AES               |

**Asymmetric (Public Key) Cryptography:**

| Algorithm  | Key Type     | Standard           | Governance | Status     | Notes                              |
|------------|--------------|--------------------|------------|------------|------------------------------------|
| RSA        | Integer factoring | FIPS 186-5, RFC 8017 | NIST/IETF | Legacy (still used) | ≥3072-bit recommended |
| ECDSA      | Elliptic curve| FIPS 186-5        | NIST       | Current    | P-256, P-384, P-521 curves         |
| EdDSA (Ed25519)| Edwards curve| RFC 8032, FIPS 186-5| IETF/NIST| Current  | Preferred for new systems          |
| DH (FFDHE) | Finite field | RFC 7919, FIPS 186-5| IETF/NIST| Current   | FFDHE-3072, FFDHE-4096            |
| ECDH       | Elliptic curve| RFC 8422          | IETF       | Current    | X25519, X448 preferred             |

**Hash Functions:**

| Algorithm  | Output (bits)| Standard      | Governance | Status     | Notes                         |
|------------|--------------|---------------|------------|------------|-------------------------------|
| SHA-256    | 256          | FIPS 180-4    | NIST       | Current    | Most widely used              |
| SHA-384    | 384          | FIPS 180-4    | NIST       | Current    | TLS cipher suites             |
| SHA-512    | 512          | FIPS 180-4    | NIST       | Current    | Long-term security            |
| SHA-3-256  | 256          | FIPS 202      | NIST       | Current    | Keccak — different design     |
| SHAKE128/256| Variable    | FIPS 202      | NIST       | Current    | Extendable output (XOF)       |
| MD5        | 128          | RFC 1321      | IETF       | Broken     | DO NOT USE for security       |
| SHA-1      | 160          | —             | NIST       | Deprecated | Deprecated 2022 — collision attacks|

**Post-Quantum Cryptography (PQC) — 2024 NIST Standards:**

| FIPS     | Algorithm          | Based On          | Use Case                         |
|----------|--------------------|-------------------|----------------------------------|
| FIPS 203 | ML-KEM (Kyber)    | Module Lattice    | Key encapsulation (replaces ECDH)|
| FIPS 204 | ML-DSA (Dilithium)| Module Lattice    | Digital signatures               |
| FIPS 205 | SLH-DSA (SPHINCS+)| Hash-based        | Digital signatures (stateless)   |
| FIPS 206 | FN-DSA (FALCON)   | NTRU Lattice      | Digital signatures (compact)     |

**Hybrid PQC approach**: NIST and IETF recommend combining classical + PQC algorithms during transition period (e.g., X25519 + ML-KEM in TLS handshake)

### SECTION 13.3 — TLS (TRANSPORT LAYER SECURITY)

Full encyclopedia entry:

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Transport Layer Security                         |
| Abbreviation                 | TLS                                               |
| Entry Type                   | Security protocol (authentication + encryption + integrity) |
| Governance Tier              | L3 (IETF)                                         |
| Current version              | TLS 1.3 (RFC 8446, 2018)                          |
| Deprecated versions          | SSL 2.0, SSL 3.0 (POODLE attack), TLS 1.0, TLS 1.1 (all deprecated per RFC 8996) |

**TLS 1.3 Key Improvements over TLS 1.2:**
- 1-RTT handshake (vs. 2-RTT in TLS 1.2) + 0-RTT resumption
- Removed weak/legacy cipher suites (RC4, 3DES, MD5, SHA-1, RSA key exchange)
- Forward Secrecy mandatory (ECDHE/DHE only)
- All handshake messages after ServerHello are encrypted
- Simplified cipher suite list: TLS_AES_128_GCM_SHA256, TLS_AES_256_GCM_SHA384, TLS_CHACHA20_POLY1305_SHA256

**TLS Certificate Chain:**
```
Root CA (self-signed) — stored in OS/browser trust store
        ↓
Intermediate CA (cross-signed by Root)
        ↓
End-Entity Certificate (domain certificate — server/client)
        ↓
Certificate contains: Subject, SubjectAltName (SAN), Public Key, Validity, Extensions
```

**X.509 Certificate Standard:**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Standard            | ITU-T X.509 (ISO/IEC 9594-8)                    |
| PKIX profile        | RFC 5280 — Internet profile of X.509             |
| Governance Tier     | L1 (ITU-T) + L3 (IETF PKIX)                     |
| Key extensions      | SubjectAltName (SAN), KeyUsage, ExtendedKeyUsage, BasicConstraints, CRL Distribution Points, OCSP, CT (SCT) |
| Certificate formats | PEM (base64), DER (binary), PKCS#12 (P12/PFX bundle), PKCS#7 |

**Certificate Transparency (CT):**
- RFC 9162 (Certificate Transparency 2.0)
- Governance: Google-initiated → IETF standardized
- All publicly trusted certificates must be logged in CT logs
- Browsers require CT compliance for trust

### SECTION 13.4 — IDENTITY AND ACCESS MANAGEMENT (IAM) STANDARDS

**FIDO2 / WebAuthn / Passkeys:**

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | FIDO2 = WebAuthn (W3C) + CTAP2 (FIDO Alliance)  |
| WebAuthn                     | Web Authentication API — W3C Recommendation (Level 2, 2021) |
| CTAP2                        | Client-to-Authenticator Protocol — FIDO Alliance spec |
| Entry Type                   | Authentication protocol (phishing-resistant MFA) |
| Governance                   | W3C (WebAuthn) + FIDO Alliance (CTAP)            |
| Governance Tier              | L3 (W3C) + L3 (FIDO Alliance)                   |
| Passkeys                     | Discoverable FIDO2 credentials (synced or device-bound) |
| Authentication flows         | Platform authenticator (TouchID, FaceID, Windows Hello) + Roaming authenticator (YubiKey) |

**OAuth 2.x / OIDC Stack:**

```
OAuth 2.0 (RFC 6749) — Authorization framework
        ↓ built on
OAuth 2.1 (draft) — Consolidates best practices, removes implicit flow
        ↓
OpenID Connect 1.0 (OIDC) — Identity layer on top of OAuth 2.0
        ↓
FAPI 2.0 (Financial-grade API) — High-security OAuth for banking/finance
```

**OAuth 2.0 Grant Types:**

| Grant Type              | RFC         | Use Case                                 | Security Level |
|-------------------------|-------------|------------------------------------------|----------------|
| Authorization Code      | RFC 6749    | Web apps with server-side callback       | High           |
| Authorization Code + PKCE| RFC 7636  | Mobile/SPA apps (no client secret)       | High           |
| Client Credentials      | RFC 6749    | Machine-to-machine (service accounts)   | High           |
| Device Authorization    | RFC 8628    | TV, CLI, limited-input devices           | Medium         |
| Implicit (deprecated)   | RFC 6749    | DEPRECATED — do not use                 | Low (removed in 2.1)|
| ROPC (deprecated)       | RFC 6749    | DEPRECATED — password grant             | Very Low       |

**SAML 2.0 (Security Assertion Markup Language):**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Governance          | OASIS (L3)                                        |
| Use case            | Enterprise SSO — XML-based identity federation   |
| Comparison to OIDC  | SAML: enterprise, XML, browser redirect; OIDC: modern, JSON, API-first |
| Still dominant in   | Legacy enterprise apps, government, education (Shibboleth) |

**SCIM (System for Cross-domain Identity Management):**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Standard            | RFC 7642, 7643, 7644                             |
| Use case            | Automated user provisioning/deprovisioning across systems |
| Typical deployment  | Azure AD / Okta → target SaaS applications       |

### SECTION 13.5 — ZERO TRUST ARCHITECTURE

| Standard/Framework     | Governance    | Description                                     |
|------------------------|---------------|-------------------------------------------------|
| NIST SP 800-207        | NIST          | Zero Trust Architecture — definitive framework  |
| BeyondCorp             | Google (L6)   | Original ZTA implementation — influenced NIST 800-207 |
| SPIFFE/SPIRE           | CNCF (L5)     | Workload identity in zero trust environments    |
| SPIFFE STS             | CNCF          | Secure Production Identity Framework for Everyone|
| mTLS                   | IETF (RFC 8446)| Mutual TLS — bidirectional cert authentication |

**Zero Trust Principles (NIST SP 800-207):**
1. All data sources and computing services are resources
2. All communication is secured regardless of network location
3. Access to individual enterprise resources is granted per-session
4. Access determined by dynamic policy including identity, application, observable state
5. Enterprise monitors and measures the integrity of assets
6. Authentication and authorization are dynamic and strictly enforced
7. Enterprise collects information about assets and uses it to improve security

### SECTION 13.6 — VULNERABILITY MANAGEMENT STANDARDS

**CVE / CWE / CVSS / NVD:**

| Artifact    | Full Name                                    | Governance     | Description                              |
|-------------|----------------------------------------------|----------------|------------------------------------------|
| CVE         | Common Vulnerabilities and Exposures         | MITRE (US CISA)| Unique identifier for known vulnerabilities|
| CWE         | Common Weakness Enumeration                  | MITRE          | Classification of software weaknesses   |
| CVSS        | Common Vulnerability Scoring System          | FIRST          | Severity scoring 0.0–10.0               |
| CVSS v4.0   | CVSS version 4.0 (2023)                     | FIRST          | Added supplemental metrics, exploitability|
| NVD         | National Vulnerability Database              | NIST           | US government CVE enrichment database   |
| EPSS         | Exploit Prediction Scoring System           | FIRST          | Probability of exploitation in 30 days  |
| VEX          | Vulnerability Exploitability eXchange        | CISA/NTIA      | Machine-readable exploit status for SBOM|
| KEV          | Known Exploited Vulnerabilities catalog      | CISA           | Authoritative list of exploited CVEs    |

**CVSS v4.0 Metrics:**
- Base Score: Exploitability (AV, AC, AT, PR, UI) + Impact (VC, VI, VA, SC, SI, SA)
- Threat Score: Exploit Maturity (E)
- Environmental Score: Security Requirements (CR, IR, AR) + Modified Base
- Supplemental: Safety (S), Automatable (A), Recovery (R), Value Density (V)

### SECTION 13.7 — SUPPLY CHAIN SECURITY STANDARDS

**SBOM (Software Bill of Materials):**

| Format     | Full Name                                  | Governance          | Tier  |
|------------|--------------------------------------------|---------------------|-------|
| SPDX       | Software Package Data Exchange             | Linux Foundation     | L5    |
| CycloneDX  | CycloneDX                                  | OWASP               | L5    |
| SWID Tags  | Software Identification Tags               | ISO/IEC 19770-2     | L1    |

**SLSA (Supply-chain Levels for Software Artifacts):**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Governance          | OpenSSF (Linux Foundation)                       |
| Framework           | 4 levels (SLSA 0–3) of build and provenance integrity |
| Level 1             | Documentation of provenance (no verification)   |
| Level 2             | Tamper-resistant provenance (hosted source + signed)  |
| Level 3             | Hardened build process (isolated, auditable builds) |

**Sigstore:**
- Free, open infrastructure for software signing
- Components: Fulcio (CA), Rekor (transparency log), Cosign (container signing)
- Governance: OpenSSF / Linux Foundation

### SECTION 13.8 — SECURITY FRAMEWORKS AND CONTROLS

| Framework           | Governance      | Tier | Type                    | Certification Available |
|---------------------|-----------------|------|-------------------------|-------------------------|
| ISO/IEC 27001:2022  | ISO/IEC JTC1/SC27| L1  | ISMS — certifiable      | Yes — third-party audit |
| ISO/IEC 27002:2022  | ISO/IEC JTC1/SC27| L1  | Controls guidance       | No — reference only    |
| NIST CSF 2.0        | NIST            | L2   | Risk framework          | No — voluntary          |
| NIST SP 800-53 Rev.5| NIST            | L2   | Controls catalog        | Yes (FedRAMP)           |
| SOC 2 Type II       | AICPA           | L4   | Audit report (Trust Services) | Yes — CPA audit    |
| CIS Benchmarks      | CIS             | L3   | Configuration hardening | CIS SecureSuite         |
| OWASP Top 10        | OWASP           | L5   | Web app risk awareness  | No — reference          |
| OWASP ASVS          | OWASP           | L5   | App security requirements| No — used in contracts |
| MITRE ATT&CK        | MITRE           | L5   | Threat intelligence matrix| No — reference        |

**OWASP Top 10 (2021 edition):**

| Rank | Category                                     |
|------|----------------------------------------------|
| A01  | Broken Access Control                        |
| A02  | Cryptographic Failures                       |
| A03  | Injection (SQL, NoSQL, OS, LDAP)             |
| A04  | Insecure Design                              |
| A05  | Security Misconfiguration                    |
| A06  | Vulnerable and Outdated Components           |
| A07  | Identification and Authentication Failures   |
| A08  | Software and Data Integrity Failures         |
| A09  | Security Logging and Monitoring Failures     |
| A10  | Server-Side Request Forgery (SSRF)           |

### SECTION 13.9 — NETWORK SECURITY PROTOCOLS

| Protocol    | RFC/Standard       | Purpose                                   | Status      |
|-------------|--------------------|--------------------------------------------|-------------|
| IPsec       | RFC 4301-4309      | Layer 3 network encryption                | Active      |
| IKEv2       | RFC 7296           | IPsec key exchange                        | Current     |
| SSH v2      | RFC 4251-4256      | Secure shell — remote access + SFTP       | Active      |
| WireGuard   | RFC 9107 (informational) | Modern VPN protocol (ChaChaPoly)  | Active      |
| OpenPGP     | RFC 4880, RFC 9580 | End-to-end email and file encryption     | Active      |
| S/MIME      | RFC 8551           | Email security (X.509-based)             | Active      |
| DNSSEC      | RFC 4033-4035      | DNS response authentication              | Active      |
| DoH         | RFC 8484           | DNS over HTTPS                           | Active      |
| DoT         | RFC 7858           | DNS over TLS                             | Active      |

### SECTION 13.10 — CYBERSECURITY STANDARDS CROSSWALK TABLE

Show mappings between major frameworks for implementation guidance:

| Control Domain            | ISO 27001 Annex A | NIST CSF 2.0 | NIST 800-53 | CIS Controls v8 |
|---------------------------|-------------------|--------------|-------------|-----------------|
| Asset management          | 5.9               | ID.AM        | CM-8        | CIS 1, CIS 2    |
| Access control            | 8.3               | PR.AA        | AC-2, AC-3  | CIS 5, CIS 6    |
| Cryptography              | 8.24              | PR.DS        | SC-28, SC-12| CIS 3           |
| Network security          | 8.20, 8.21        | PR.IR        | SC-7, SI-3  | CIS 12, CIS 13  |
| Vulnerability management  | 8.8               | ID.RA, RS.MI | RA-5, SI-2  | CIS 7           |
| Incident response         | 5.26              | RS, RC       | IR-4, IR-8  | CIS 17          |
| Supply chain              | 5.19-5.22         | GV.SC, ID.SC | SA-12       | CIS 15          |
| Logging/monitoring        | 8.15, 8.16        | DE.CM        | AU-2, SI-4  | CIS 8           |

---

## OUTPUT FORMAT REQUIREMENTS

- All FIPS numbers must be current (post-2024 PQC: FIPS 203/204/205)
- OAuth grant type deprecations must be explicit
- CVE/CWE/CVSS version must be current (CVSSv4.0)
- TLS deprecated versions must be correctly identified (TLS 1.0, 1.1 deprecated by RFC 8996)
- OWASP Top 10 year must be specified (2021)
- Minimum length: 5,500 words
