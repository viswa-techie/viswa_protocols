# Chapter 3 — Internet & Web Standards Bodies

## Universal Technology Standards Governance & Regulatory Architecture Encyclopedia
### First Edition, 2024–2026

---

## 3.1 Chapter Overview

This chapter covers the organizations that define how the internet and World Wide Web work. These bodies produce the protocols, formats, APIs, and naming systems that underpin all networked computing.

**Bodies Covered:**
- IETF (Internet Engineering Task Force)
- IAB (Internet Architecture Board)
- IANA (Internet Assigned Numbers Authority)
- ICANN (Internet Corporation for Assigned Names and Numbers)
- W3C (World Wide Web Consortium)
- WHATWG (Web Hypertext Application Technology Working Group)
- Unicode Consortium
- ECMA International (TC39 — ECMAScript/JavaScript)
- OASIS (Organization for the Advancement of Structured Information Standards)
- OpenID Foundation
- FIDO Alliance

---

## 3.2 IETF — Internet Engineering Task Force

### IETF

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Internet Engineering Task Force                                |
| Abbreviation / Alias         | IETF                                                           |
| Entry Type                   | Body                                                          |
| Governance Tier              | L3                                                            |
| Governing Organization       | IETF (under ISOC — Internet Society umbrella; became IETF LLC in 2018) |
| Parent / Umbrella Body       | Internet Society (ISOC) — administrative; IETF is technically independent |
| Founded / Published          | 1986                                                          |
| Specification Availability   | **Free** — all RFCs at rfc-editor.org                         |
| Open / Proprietary / Mixed   | Open — no membership fee; anyone can participate              |
| Formal / Informal            | Formal (rigorous process) but informal culture (no dress code, "humming") |
| Scope                        | Global — Internet protocols and architecture                  |
| Primary Domain               | Internet protocols: transport, routing, security, applications, operations |
| Secondary Domains            | Identity (OAuth, SCIM), real-time comms (WebRTC), IoT (CoAP)  |
| Industry Relevance           | Critical — every device connected to the internet uses IETF protocols |
| Cross-Platform               | Yes                                                           |
| Cross-Language               | Yes                                                           |
| Cross-Domain                 | Yes                                                           |
| Automotive Relevance         | High (TLS, IPv6, DoIP transport, V2X security)                |
| Cloud Relevance              | Very High (HTTP/2, HTTP/3, QUIC, TLS, gRPC transport)         |
| Embedded Relevance           | High (CoAP, DTLS, 6LoWPAN, CBOR)                             |
| Safety-Critical Relevance    | Medium (TLS for secure communication in safety systems)       |
| Enterprise Relevance         | Very High (DNS, SMTP, IMAP, HTTP, TLS, OAuth)                 |
| Compliance / Certification   | No formal certification program — conformance is voluntary    |
| Versioning Model             | RFC numbers are sequential and immutable; new RFCs obsolete old ones |
| Adoption Model               | Voluntary — "rough consensus and running code"                |
| Competing / Adjacent Bodies  | W3C (web layer above), IEEE (link layer below), 3GPP (mobile transport) |
| Relationship to Other Stds   | IETF builds on IEEE 802 (L2); W3C builds on IETF (HTTP/TLS) |
| Typical Use Cases            | Any internet-connected system                                 |
| Technology Stack Position    | Layers 3–7 of the TCP/IP stack (network, transport, security, application) |
| Implementation Maturity      | Mature                                                        |
| Notes                        | "We reject kings, presidents, and voting. We believe in rough consensus and running code." — Dave Clark, 1992 |

### IETF Organizational Structure

```
ISOC (Internet Society) — administrative support
    │
    └── IETF LLC (since 2018) — operational management
            │
            ├── IAB (Internet Architecture Board) — architectural oversight
            │       └── IRTF (Internet Research Task Force) — long-term research
            │
            ├── IESG (Internet Engineering Steering Group) — standards approval
            │       └── Area Directors (ADs) — one per area
            │
            ├── RFC Editor — publication of RFCs
            │
            └── IANA (protocol parameters registry)
```

### IETF Areas (Working Group Organization)

| Area    | Full Name                          | Key Topics                                  | Key WGs                    |
|---------|------------------------------------|---------------------------------------------|----------------------------|
| ART     | Applications and Real-Time         | HTTP, email, messaging, codecs, WebRTC      | httpbis, jmap, mls         |
| GEN     | General                            | Cross-area process                          | —                          |
| INT     | Internet                           | IPv6, addressing, routing                   | 6man, dhc, intarea         |
| OPS     | Operations and Management          | Network management, YANG, NETCONF           | netconf, opsawg            |
| RTG     | Routing                            | BGP, OSPF, IS-IS, MPLS, SRv6              | idr, ospf, spring          |
| SEC     | Security                           | TLS, IPsec, SAAG, OAuth, ACME              | tls, ipsecme, oauth, acme  |
| TSV     | Transport                          | TCP, UDP, QUIC, SCTP, congestion control   | quic, tcpm, tsvwg          |
| WIT     | Web and Internet Transport         | HTTP/3, WebTransport                        | webtrans                   |

### RFC Publication Pipeline

```
Individual Submission ──→ Internet-Draft (I-D) ──→ (optional) WG Adoption
    ↓                                                        ↓
    └── Independent Submission ──→ RFC Editor review ──→ Published as Informational/Experimental RFC
                                                             
WG I-D ──→ WG discussion + revision ──→ WG Last Call (2 weeks)
    ↓
IETF Last Call (2 weeks — cross-area review)
    ↓
IESG Review (Area Directors evaluate)
    ↓
IESG Ballot: Approve / Discuss / Abstain / Block (DISCUSS must be resolved)
    ↓
RFC Editor Queue ──→ AUTH48 (author final review) ──→ Published RFC
```

### RFC Document Categories

| Category             | RFC 2026 Term           | Normative? | Maturity                    | Example                |
|----------------------|-------------------------|------------|-----------------------------|------------------------|
| Standards Track      | Proposed Standard       | Yes        | Mature enough for implementation | RFC 8446 (TLS 1.3) |
| Standards Track      | Internet Standard (STD) | Yes        | Highest maturity — proven interop | RFC 9293 (TCP)    |
| Best Current Practice| BCP                     | Partially  | Process/operational guidance | RFC 2119 (keywords)   |
| Informational        | Informational           | No         | Reference/educational       | RFC 9107 (WireGuard)  |
| Experimental         | Experimental            | No         | Early-stage protocol        | Various               |
| Historic             | Historic                | No         | Deprecated/obsolete         | RFC 2616 (old HTTP)   |

### Key IETF RFC Families

| Protocol/Topic    | Key RFCs                                          | Status        |
|-------------------|---------------------------------------------------|---------------|
| TCP               | RFC 9293 (2022 — consolidation)                  | Internet Standard |
| IPv6              | RFC 8200 (2017)                                   | Internet Standard |
| HTTP/1.1          | RFC 9110, 9111, 9112 (2022 — semantic split)    | Proposed Standard |
| HTTP/2            | RFC 9113 (2022)                                   | Proposed Standard |
| HTTP/3            | RFC 9114 (2022)                                   | Proposed Standard |
| QUIC              | RFC 9000, 9001 (2021)                            | Proposed Standard |
| TLS 1.3           | RFC 8446 (2018)                                   | Proposed Standard |
| TLS deprecation   | RFC 8996 (2021 — deprecates TLS 1.0/1.1)        | BCP            |
| DNS               | RFC 1034, 1035 (1987); many updates              | Internet Standard |
| DNS over HTTPS    | RFC 8484 (2018)                                   | Proposed Standard |
| DNSSEC            | RFC 4033, 4034, 4035 (2005)                      | Proposed Standard |
| OAuth 2.0         | RFC 6749 (2012)                                   | Proposed Standard |
| OAuth 2.0 PKCE    | RFC 7636 (2015)                                   | Proposed Standard |
| JWT               | RFC 7519 (2015)                                   | Proposed Standard |
| SCIM              | RFC 7643, 7644 (2015)                            | Proposed Standard |
| ACME              | RFC 8555 (2019 — Let's Encrypt protocol)         | Proposed Standard |
| WebSocket         | RFC 6455 (2011)                                   | Proposed Standard |
| SSH               | RFC 4251–4256 (2006)                             | Proposed Standard |
| IPsec             | RFC 4301–4309 (2005)                             | Proposed Standard |
| IKEv2             | RFC 7296 (2014)                                   | Internet Standard |
| SMTP              | RFC 5321 (2008)                                   | Internet Standard |
| IMAP              | RFC 9051 (2021 — IMAPv4rev2)                     | Proposed Standard |
| JSON              | RFC 8259 (2017)                                   | Internet Standard |
| CBOR              | RFC 8949 (2020)                                   | Internet Standard |
| CoAP              | RFC 7252 (2014)                                   | Proposed Standard |
| MQTT              | (OASIS — not IETF)                               | —              |

---

## 3.3 W3C — World Wide Web Consortium

### W3C

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | World Wide Web Consortium                                      |
| Abbreviation / Alias         | W3C                                                            |
| Entry Type                   | Body                                                          |
| Governance Tier              | L3                                                            |
| Governing Organization       | W3C Inc. (US 501c3 since January 2023)                        |
| Parent / Umbrella Body       | Previously hosted by MIT/ERCIM/Keio/Beihang; now independent legal entity |
| Founded / Published          | 1994 by Tim Berners-Lee                                        |
| Specification Availability   | **Free** — all specs at w3.org/TR                             |
| Open / Proprietary / Mixed   | Open (specifications); membership-required (participation in WGs) |
| Formal / Informal            | Formal (W3C Process Document governs)                         |
| Scope                        | Global — World Wide Web standards                             |
| Primary Domain               | Web technologies: HTML (historical), CSS, DOM, WebAssembly, Accessibility, Privacy |
| Secondary Domains            | Identity (WebAuthn, DID/Verifiable Credentials), Payments, Media |
| Membership                   | ~450 member organizations (fee: $2,250–$77,000/year by revenue) |
| Patent Policy                | W3C Royalty-Free Patent Policy — members commit to RF licensing |
| Industry Relevance           | Critical — defines how web browsers render content             |
| Automotive Relevance         | Medium (GENIVI/W3C Automotive WG — Vehicle Information API)    |
| Cloud Relevance              | Medium (WebAssembly, Service Workers)                         |
| Competing / Adjacent Bodies  | WHATWG (HTML/DOM Living Standards), IETF (HTTP transport layer) |
| Notes                        | W3C and WHATWG resolved their HTML dispute in 2019 — WHATWG now owns HTML/DOM/Fetch/URL as Living Standards; W3C no longer publishes separate HTML spec |

### W3C Recommendation Track (Standards Pipeline)

```
Working Draft (WD) — iterative public drafts
    ↓
Candidate Recommendation (CR) — implementation testing
    ↓ (must demonstrate at least 2 independent implementations)
Proposed Recommendation (PR) — Advisory Committee review (4 weeks)
    ↓
W3C Recommendation (REC) — final published standard
    ↓ (if revised)
Amended Recommendation / Superseded
```

### Key W3C Recommendations

| Specification       | Status           | Domain              | Impact                                |
|---------------------|------------------|---------------------|---------------------------------------|
| CSS (multiple modules) | REC (ongoing)  | Styling            | All web page visual presentation     |
| WebAssembly 2.0     | CR               | Runtime            | Near-native performance in browsers  |
| WebAuthn Level 2    | REC (2021)       | Authentication     | Passwordless login (passkeys)        |
| Web Accessibility (WCAG 2.2) | REC (2023) | Accessibility  | Legal requirement in many jurisdictions |
| SVG 2               | CR               | Graphics           | Scalable vector graphics in browsers |
| WebRTC 1.0          | REC (2021)       | Real-time comms    | Video/audio calls in browser         |
| WebGPU              | CR               | Graphics/Compute   | GPU access from browser              |
| Verifiable Credentials | REC (2022)    | Identity           | Decentralized identity claims        |
| ActivityPub         | REC (2018)       | Social Web         | Federated social media (Mastodon)    |
| RDF / SPARQL        | REC              | Semantic Web       | Linked data and querying             |
| Payment Request API | REC (2022)       | Payments           | Browser-native checkout              |

---

## 3.4 WHATWG — Web Hypertext Application Technology Working Group

### WHATWG

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Web Hypertext Application Technology Working Group             |
| Abbreviation / Alias         | WHATWG (pronounced "what-wig")                                 |
| Entry Type                   | Body                                                          |
| Governance Tier              | L3 (multi-vendor consortium, but vendor-dominated — Apple, Google, Mozilla, Microsoft) |
| Governing Organization       | WHATWG Steering Group (Apple, Google, Mozilla, Microsoft)       |
| Founded / Published          | 2004 — formed in opposition to W3C's XHTML 2.0 direction      |
| Specification Availability   | **Free** — all specs on spec.whatwg.org                        |
| Primary Domain               | Core web platform: HTML, DOM, Fetch, URL, Streams, Encoding, Storage |
| Specification Model          | **Living Standards** — continuously updated, no version numbers |
| Relationship to W3C          | 2019 MOU: WHATWG is THE authority for HTML and DOM; W3C defers to WHATWG for these |
| Notes                        | WHATWG specs are "Living Standards" — there is no "HTML 5.1" or "HTML 6"; just "HTML" that evolves daily |

### WHATWG Living Standards

| Specification       | URL                          | Governs                                |
|---------------------|------------------------------|----------------------------------------|
| HTML                | html.spec.whatwg.org         | HTML elements, parsing, APIs, media    |
| DOM                 | dom.spec.whatwg.org          | Document Object Model                  |
| Fetch               | fetch.spec.whatwg.org        | HTTP fetching (replaces XMLHttpRequest semantics) |
| URL                 | url.spec.whatwg.org          | URL parsing and serialization          |
| Encoding            | encoding.spec.whatwg.org     | Character encoding (TextDecoder/Encoder) |
| Streams             | streams.spec.whatwg.org      | ReadableStream, WritableStream, TransformStream |
| Storage             | storage.spec.whatwg.org      | Storage quota and API                  |
| Console             | console.spec.whatwg.org      | Console logging API                    |
| Infra               | infra.spec.whatwg.org        | Common infrastructure definitions      |
| XMLHttpRequest      | xhr.spec.whatwg.org          | Legacy XHR API (superseded by Fetch for new code) |
| Notifications       | notifications.spec.whatwg.org | Push notifications API                |
| Quirks Mode         | quirks.spec.whatwg.org       | Browser quirks mode behavior           |
| MIME Sniffing       | mimesniff.spec.whatwg.org    | Content-Type determination             |
| Compatibility       | compat.spec.whatwg.org       | Non-standard but widely-implemented features |

### W3C vs. WHATWG — Disambiguation

| Aspect              | W3C                                   | WHATWG                                    |
|---------------------|---------------------------------------|-------------------------------------------|
| Founded             | 1994 (Tim Berners-Lee)               | 2004 (reaction to W3C XHTML 2.0)         |
| Spec model          | Versioned (Recommendation snapshots) | Living Standard (continuous update)       |
| HTML ownership      | Deferred to WHATWG (since 2019)      | **Authoritative source for HTML**         |
| CSS ownership       | **W3C owns CSS** (WHATWG does not)   | No CSS work                               |
| WebAuthn ownership  | **W3C owns WebAuthn**                | No identity work                          |
| Governance          | 450+ member organizations            | 4 browser vendors (Steering Group)        |
| Patent policy       | W3C RF Patent Policy                 | WHATWG IPR Policy (similar RF commitment) |
| Who implements      | Same browsers implement both         | Same browsers implement both              |

---

## 3.5 Unicode Consortium

### Unicode Consortium

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Unicode Consortium                                             |
| Abbreviation / Alias         | Unicode                                                        |
| Entry Type                   | Body                                                          |
| Governance Tier              | L3                                                            |
| Governing Organization       | Unicode Inc. (California non-profit)                           |
| Founded / Published          | 1991                                                          |
| Specification Availability   | **Free** — unicode.org                                        |
| Primary Domain               | Character encoding, text processing, internationalization (i18n) |
| Key Output                   | The Unicode Standard (currently Version 16.0, 2024)           |
| Total Characters             | 154,998 characters (as of Unicode 16.0)                       |
| Relationship to ISO          | Synchronized with ISO/IEC 10646 — same character repertoire   |
| Industry Relevance           | Universal — every text system uses Unicode (UTF-8 dominates)  |
| Key Deliverables             | Unicode Standard, UTR (Technical Reports), UAX (Annexes), UTS (Technical Standards), CLDR, ICU |

### Unicode Technical Deliverables

| Document Type | Example                               | Purpose                                      |
|---------------|---------------------------------------|----------------------------------------------|
| The Unicode Standard | Version 16.0 (2024)          | Character repertoire + properties + algorithms |
| UAX (Unicode Standard Annex) | UAX #9 (Bidi Algorithm), UAX #14 (Line Breaking), UAX #29 (Segmentation) | Normative algorithms |
| UTS (Unicode Technical Standard) | UTS #10 (Collation Algorithm), UTS #35 (CLDR/Locale Data Markup Language) | Technical specifications |
| UTR (Unicode Technical Report) | UTR #36 (Security Considerations) | Informational guidance |
| CLDR | Common Locale Data Repository        | Locale-specific formatting (dates, numbers, currencies) |
| ICU  | International Components for Unicode | Reference implementation library (C/C++/Java) |

### UTF Encoding Forms

| Encoding | Code Unit | BMP Characters | Supplementary | Dominance                    |
|----------|-----------|----------------|---------------|------------------------------|
| UTF-8    | 8-bit     | 1–3 bytes      | 4 bytes       | **Web, Linux, modern systems** (98%+ of web pages) |
| UTF-16   | 16-bit    | 2 bytes        | 4 bytes (surrogate pairs) | Windows internals, Java, JavaScript strings |
| UTF-32   | 32-bit    | 4 bytes        | 4 bytes       | Internal processing only (wasteful for storage) |

---

## 3.6 ECMA International — TC39 (ECMAScript/JavaScript)

### ECMA International

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Ecma International — European Computer Manufacturers Association (historical name) |
| Entry Type                   | Body                                                          |
| Governance Tier              | L3                                                            |
| Founded / Published          | 1961, Geneva (originally European; now international despite name) |
| Specification Availability   | **Free** — ecma-international.org                             |
| Primary Domain               | Programming languages, file systems, communications           |
| Key Technical Committees     | TC39 (ECMAScript), TC49 (C#/CLI), TC52 (Dart)                 |
| Key Standards                | ECMA-262 (ECMAScript/JavaScript), ECMA-404 (JSON), ECMA-402 (Intl) |
| Relationship to ISO          | ECMA can "fast-track" standards to ISO (ECMA-262 → ISO/IEC 16262) |
| Notes                        | ECMA publishes ALL standards for free — unusual for a standards body |

### ECMA-262 — ECMAScript (JavaScript)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | ECMAScript Language Specification                              |
| Standard Number              | ECMA-262 (15th Edition, June 2024 — ES2024)                   |
| Entry Type                   | Standard (language specification)                              |
| Governance Tier              | L3 (ECMA TC39)                                                |
| Publication Frequency        | Annual (since ES2015/ES6) — yearly June releases              |
| TC39 Process                 | 5-stage: Stage 0 (Strawperson) → Stage 1 (Proposal) → Stage 2 (Draft) → Stage 3 (Candidate) → Stage 4 (Finished → next annual edition) |
| Implementation Maturity      | Mature — V8, SpiderMonkey, JavaScriptCore all conform          |
| Relationship to JavaScript   | ECMAScript is the standard; JavaScript is Netscape's/Oracle's trademark; they are effectively the same language |

### TC39 Stage Process

| Stage | Name         | Entry Criteria                              | Exit Criteria                              |
|-------|--------------|---------------------------------------------|--------------------------------------------|
| 0     | Strawperson  | Any TC39 member can propose                 | Presented to committee                     |
| 1     | Proposal     | TC39 champion identified; problem articulated| Committee agrees to investigate            |
| 2     | Draft        | Initial spec text; formal language          | Precise semantics described                |
| 2.7   | (Tested)     | Test262 tests written; spec review complete | Ready for implementation                   |
| 3     | Candidate    | Spec complete; 2+ implementations in progress| Needs real-world feedback                  |
| 4     | Finished     | Test262 coverage; 2+ shipping implementations| Included in next annual edition            |

---

## 3.7 OASIS — Organization for the Advancement of Structured Information Standards

### OASIS

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Organization for the Advancement of Structured Information Standards |
| Abbreviation / Alias         | OASIS                                                          |
| Entry Type                   | Body                                                          |
| Governance Tier              | L3                                                            |
| Founded / Published          | 1993 (originally SGML Open; renamed OASIS 1998)               |
| Specification Availability   | **Free** — all OASIS standards freely available               |
| Primary Domain               | Structured information: XML-based standards, messaging, security, IoT |
| Key Standards                | SAML 2.0, MQTT, AMQP, WS-Security, TOSCA, OData, STIX/TAXII, DocBook, DITA |
| Membership                   | 600+ organizations                                             |
| Standard Process             | Technical Committee → Committee Specification → OASIS Standard |
| Notes                        | OASIS can submit standards to ISO via PAS (Publicly Available Specification) route |

### Key OASIS Standards

| Standard     | Full Name                                    | Domain           | Status  | Impact                              |
|--------------|----------------------------------------------|------------------|---------|-------------------------------------|
| SAML 2.0     | Security Assertion Markup Language 2.0       | Identity/SSO     | Active  | Enterprise SSO standard (XML-based)|
| MQTT 5.0     | Message Queuing Telemetry Transport          | IoT messaging    | Active  | Dominant IoT publish/subscribe protocol |
| AMQP 1.0     | Advanced Message Queuing Protocol            | Messaging        | Active  | Enterprise message broker protocol |
| STIX 2.1     | Structured Threat Information Expression     | Cybersecurity    | Active  | Threat intelligence exchange format|
| TAXII 2.1    | Trusted Automated Exchange of Indicator Information | Cybersecurity | Active | Transport for STIX                |
| TOSCA 2.0    | Topology and Orchestration Specification for Cloud | Cloud        | Active  | Cloud application portability      |
| OData 4.01   | Open Data Protocol                           | Data/REST        | Active  | RESTful data protocol (Microsoft-origin) |
| KMIP 2.0     | Key Management Interoperability Protocol     | Security         | Active  | Cryptographic key lifecycle management |
| WS-Security  | Web Services Security                        | Security         | Legacy  | SOAP message security (declining)  |
| DocBook 5.1  | DocBook XML Schema                           | Documentation    | Active  | Technical documentation markup     |
| DITA 1.3     | Darwin Information Typing Architecture       | Documentation    | Active  | Topic-based technical authoring    |
| XLIFF 2.1    | XML Localization Interchange File Format     | i18n             | Active  | Translation file exchange          |

---

## 3.8 OpenID Foundation

### OpenID Foundation

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | OpenID Foundation                                              |
| Entry Type                   | Body                                                          |
| Governance Tier              | L3                                                            |
| Founded / Published          | 2007                                                          |
| Specification Availability   | **Free** — openid.net                                         |
| Primary Domain               | Digital identity — authentication and authorization            |
| Key Specifications           | OpenID Connect 1.0, FAPI 2.0, MODRNA (mobile), Shared Signals Framework |
| Relationship to IETF         | OIDC builds on IETF OAuth 2.0 (RFC 6749) + JWT (RFC 7519)    |
| Industry Relevance           | Very High — OIDC is the dominant modern identity protocol     |

### OpenID Connect 1.0 (OIDC)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | OpenID Connect Core 1.0                                        |
| Entry Type                   | Specification (identity layer on OAuth 2.0)                   |
| Governance Tier              | L3 (OpenID Foundation)                                        |
| Published                    | 2014 (Final Specification)                                     |
| Relationship to OAuth        | OIDC is an identity layer ON TOP OF OAuth 2.0 (which is authorization only) |
| Key Concept                  | ID Token (JWT containing user identity claims)                 |
| Flows                        | Authorization Code Flow, Implicit Flow (deprecated), Hybrid Flow |
| Discovery                    | .well-known/openid-configuration (RFC 8414)                   |
| Implementations              | Azure AD (Entra ID), Okta, Auth0, Google, Keycloak, AWS Cognito |

**OAuth 2.0 vs. OIDC — Critical Disambiguation:**

| Aspect        | OAuth 2.0                              | OpenID Connect 1.0                         |
|---------------|----------------------------------------|--------------------------------------------|
| Purpose       | **Authorization** (access delegation)  | **Authentication** (identity verification) |
| Output        | Access Token (opaque or JWT)           | ID Token (JWT with identity claims)        |
| Answers       | "What can this app do?"                | "Who is this user?"                        |
| Governed by   | IETF (RFC 6749)                        | OpenID Foundation                          |
| Typical use   | API access without sharing passwords   | Single Sign-On, user login                 |

---

## 3.9 FIDO Alliance

### FIDO Alliance

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Fast Identity Online Alliance                                  |
| Entry Type                   | Body                                                          |
| Governance Tier              | L3                                                            |
| Founded / Published          | 2012                                                          |
| Specification Availability   | Free — fidoalliance.org                                       |
| Primary Domain               | Passwordless authentication, phishing-resistant credentials    |
| Key Specifications           | FIDO2 (WebAuthn + CTAP2), FIDO UAF, FIDO U2F (legacy)        |
| Certification                | FIDO Certified program — products can be certified            |
| Members                      | Google, Apple, Microsoft, Amazon, Intel, Samsung, Yubico, etc.|
| Industry Impact              | Passkeys (FIDO2 discoverable credentials) are replacing passwords globally |

### FIDO2 Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                          RELYING PARTY                            │
│                    (Website / Service / App)                      │
│                                                                   │
│   Uses: WebAuthn API (W3C Recommendation)                        │
│   Receives: PublicKeyCredential assertion                        │
└───────────────────────────────┬───────────────────────────────────┘
                                │ WebAuthn API
┌───────────────────────────────┴───────────────────────────────────┐
│                         WEB BROWSER / CLIENT                       │
│                                                                    │
│   Implements: WebAuthn Level 2 (W3C)                              │
│   Talks to authenticator via: CTAP2 protocol                      │
└───────────────────────────────┬────────────────────────────────────┘
                                │ CTAP2 (Client-to-Authenticator Protocol)
┌───────────────────────────────┴────────────────────────────────────┐
│                         AUTHENTICATOR                               │
│                                                                     │
│   Platform: TouchID, FaceID, Windows Hello, Android biometrics      │
│   Roaming: YubiKey, Titan Security Key, SoloKeys                   │
│                                                                     │
│   Stores: Private key (never leaves authenticator)                  │
│   Operations: MakeCredential (registration), GetAssertion (login)   │
└─────────────────────────────────────────────────────────────────────┘
```

### FIDO2 vs. FIDO U2F vs. Passkeys — Disambiguation

| Term            | What It Is                                         | Status          |
|-----------------|-----------------------------------------------------|-----------------|
| FIDO U2F        | First-gen 2FA standard (Universal 2nd Factor) — external key only | Legacy (subsumed by FIDO2) |
| FIDO2           | Second-gen standard: WebAuthn (W3C) + CTAP2 (FIDO Alliance) | Current |
| WebAuthn        | W3C API for browsers to talk to authenticators      | Current (W3C Rec Level 2) |
| CTAP2           | Protocol between client and authenticator (USB/NFC/BLE) | Current (FIDO spec) |
| Passkeys        | Marketing term for discoverable FIDO2 credentials that sync across devices (Apple/Google/Microsoft) | Current (2022+) |
| Security Key    | Physical hardware authenticator (YubiKey, Titan)    | Current (hardware FIDO2) |

---

## 3.10 IANA and ICANN

### IANA — Internet Assigned Numbers Authority

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Internet Assigned Numbers Authority                            |
| Entry Type                   | Body (registry function)                                      |
| Governance Tier              | L3 (operated by ICANN under IETF MOU)                        |
| Function                     | Manages protocol parameter registries for IETF standards      |
| Key Registries               | Port numbers, media types (MIME), DNS root zone, protocol numbers, URI schemes, TLS cipher suites, HTTP status codes, character sets |
| Example                      | HTTP content types (application/json) are registered in IANA Media Types registry |

### ICANN — Internet Corporation for Assigned Names and Numbers

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Internet Corporation for Assigned Names and Numbers           |
| Entry Type                   | Body                                                          |
| Governance Tier              | L3 (private non-profit — multi-stakeholder)                   |
| Founded                      | 1998                                                          |
| Function                     | Coordinates DNS root, IP address allocation (via RIRs), gTLD management |
| Key Responsibilities         | DNS root zone management, gTLD policy, IP address policy coordination |
| Controversy                  | US government oversight ended 2016 (IANA transition to global multistakeholder) |

---

## 3.11 Internet/Web Bodies Comparison Matrix

| Attribute          | IETF           | W3C               | WHATWG           | ECMA TC39        | OASIS           | Unicode         |
|--------------------|----------------|--------------------|------------------|------------------|-----------------|-----------------|
| Founded            | 1986           | 1994               | 2004             | 1961             | 1993            | 1991            |
| Membership fee     | **$0**         | $2,250–$77,000    | By invitation    | $70,000 (max)    | $0–$55,000     | $0–$20,000     |
| Spec access        | Free           | Free               | Free             | Free             | Free            | Free            |
| Decision model     | Rough consensus| WG consensus + AC  | Steering Group   | Stage process    | TC vote         | UTC vote        |
| Output name        | RFC            | Recommendation     | Living Standard  | ECMA-NNN         | OASIS Standard  | Unicode Standard|
| Versioning         | Sequential RFC#| Versioned editions| Continuous/Living| Annual editions  | Versioned       | Annual versions |
| Certification      | No             | No                 | No               | No               | No              | No              |
| Primary domain     | Internet proto.| Web platform       | Core web (HTML/DOM)| JS language   | Messaging/XML   | Characters/text |
| Patent policy      | Note Well (IETF IPR)| RF Patent Policy | WHATWG Patent Policy| RF Royalty-Free| OASIS IPR modes| N/A         |

---

*End of Chapter 3 — Internet & Web Standards Bodies*
*Next: Chapter 4 — Industry Consortium Standards Bodies*
