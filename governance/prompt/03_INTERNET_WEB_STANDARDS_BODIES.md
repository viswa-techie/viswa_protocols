# CHAPTER 3 — INTERNET & WEB STANDARDS BODIES
# Prompt for: Universal Technology Standards Governance Encyclopedia
# Depends on: MASTER_PROMPT.md, Chapter 1 (taxonomy), Chapter 2 (ISO/IEC/ITU)

---

## CHAPTER SCOPE

**Covers:**
- IETF (Internet Engineering Task Force) — Internet protocols, RFC process
- IRTF (Internet Research Task Force) — long-horizon internet research
- IAB (Internet Architecture Board) — architectural oversight
- IANA (Internet Assigned Numbers Authority) — name/number registries
- ICANN (Internet Corporation for Assigned Names and Numbers)
- W3C (World Wide Web Consortium) — web standards
- WHATWG (Web Hypertext Application Technology Working Group) — HTML living standard
- Unicode Consortium — character encoding and internationalization
- ECMA International — ECMAScript (JavaScript), JSON (ECMA-404)
- OASIS (Organization for the Advancement of Structured Information Standards)
- OpenID Foundation — identity protocols

**Defers to:**
- Network hardware (IEEE 802.x) → Chapter 12 (Networking)
- OAuth/OIDC implementation security → Chapter 13 (Cybersecurity)
- API specifications (OpenAPI) → Chapter 14 (Cloud/Web/API)

---

## GENERATION INSTRUCTIONS FOR THIS CHAPTER

Act as a world-class internet standards and web governance specialist.
Generate the complete **Chapter 3: Internet & Web Standards Bodies** encyclopedia entries.

### SECTION 3.1 — THE INTERNET GOVERNANCE MODEL

Explain the unique governance architecture of the internet:
- Why the internet has NO single governing body — it is a multi-stakeholder model
- The "rough consensus and running code" philosophy (Tao of IETF, RFC 1958)
- The Internet Society (ISOC) as the organizational umbrella
- The IETF/IAB/IRTF/IANA governance stack
- ICANN's role in DNS and IP address governance
- Why IANA matters (port numbers, protocol parameters, TLDs, ASNs, IP blocks)
- The NTIA transition (2016): US government relinquishing IANA oversight
- The multi-stakeholder vs. multi-lateral governance debate (ICANN vs. ITU)

### SECTION 3.2 — IETF (INTERNET ENGINEERING TASK FORCE)

Full encyclopedia entry:

| Field                        | Value                                         |
|------------------------------|-----------------------------------------------|
| Full Name                    | Internet Engineering Task Force               |
| Abbreviation                 | IETF                                          |
| Entry Type                   | Standards Development Organization (SDO)      |
| Governance Tier              | L3 (Industry Consortium — though uniquely open)|
| Organizational Parent        | Internet Society (ISOC)                       |
| Founded                      | 1986 (first meeting)                          |
| Headquarters                 | Virtual — no physical HQ. Registered under ISOC.|
| Membership                   | Open — no membership fees. Participation-based.|
| Governance                   | IAB appoints IESG. IESG approves RFCs. NOMCOM selects IESG/IAB.|
| Decision Model               | "Rough consensus and running code" — no formal vote |
| Output Documents             | RFC (Request for Comments)                    |
| RFC Categories               | Standards Track, Informational, Experimental, BCP, Historic |
| RFC Numbering                | Sequential — RFC 1 (1969) to present (RFC 9000+ in 2024) |
| Specification Access         | Free — all RFCs publicly available at rfc-editor.org |
| Open / Proprietary           | Open                                          |
| IP/Patent Policy             | RFC 8179 — IETF IPR policy. Contributor must disclose patents. |
| Key Working Groups           | Transport (QUIC, TCP), Security (TLS, ACME), HTTP, DNS, OAUTH |
| Competing Bodies             | ITU-T (telecom), IEEE (hardware), W3C (web)  |

**IETF RFC Publication Pipeline:**

```
Idea / Problem Statement
        ↓
Birds-of-a-Feather (BoF) meeting — gauge interest
        ↓
Working Group (WG) chartered by IESG
        ↓
Internet-Draft (I-D) — expires in 6 months, unlimited revision
        ↓
WG Last Call — working group consensus achieved
        ↓
IETF Last Call — community-wide review (2 weeks minimum)
        ↓
IESG Review — Area Directors review for technical quality
        ↓
IESG Ballot — formal approval
        ↓
RFC Editor — editorial quality, boilerplate, canonical formatting
        ↓
RFC Published — permanently assigned number, no further changes
```

**Key IETF Standard Families (produce a table):**

| RFC Family        | Technology                                 | Current Canonical RFC(s)                    |
|-------------------|--------------------------------------------|---------------------------------------------|
| HTTP/1.1          | Hypertext Transfer Protocol v1.1           | RFC 9110, 9112                              |
| HTTP/2            | Binary multiplexed HTTP                    | RFC 9113                                    |
| HTTP/3 + QUIC     | HTTP over QUIC (UDP-based)                 | RFC 9114, RFC 9000                          |
| TLS 1.3           | Transport Layer Security                   | RFC 8446                                    |
| DNS               | Domain Name System                         | RFC 1034, 1035 (base), RFC 8484 (DoH)      |
| DNSSEC            | DNS Security Extensions                    | RFC 4033–4035                               |
| TCP               | Transmission Control Protocol              | RFC 9293 (updated)                          |
| UDP               | User Datagram Protocol                     | RFC 768                                     |
| IP v4             | Internet Protocol version 4                | RFC 791                                     |
| IP v6             | Internet Protocol version 6                | RFC 8200                                    |
| BGP               | Border Gateway Protocol                    | RFC 4271                                    |
| OSPF              | Open Shortest Path First                   | RFC 5340 (OSPFv3)                           |
| SMTP              | Simple Mail Transfer Protocol              | RFC 5321                                    |
| IMAP              | Internet Message Access Protocol           | RFC 9051                                    |
| XMPP              | Extensible Messaging and Presence Protocol | RFC 6120                                    |
| JSON              | JavaScript Object Notation                 | RFC 8259                                    |
| CBOR              | Concise Binary Object Representation       | RFC 8949                                    |
| JWT               | JSON Web Token                             | RFC 7519                                    |
| OAuth 2.0         | Authorization framework                    | RFC 6749 (base), RFC 9700 series            |
| ACME              | Automatic Certificate Management           | RFC 8555                                    |
| PKIX              | Internet PKI (X.509 profile for internet)  | RFC 5280                                    |
| SIP               | Session Initiation Protocol                | RFC 3261                                    |
| RTP               | Real-Time Transport Protocol               | RFC 3550                                    |
| CoAP              | Constrained Application Protocol (IoT)    | RFC 7252                                    |
| MQTT (OASIS)      | Message Queue Telemetry Transport          | OASIS MQTT 5.0 (see OASIS entry)            |

### SECTION 3.3 — IAB (INTERNET ARCHITECTURE BOARD)

| Field                        | Value                                         |
|------------------------------|-----------------------------------------------|
| Full Name                    | Internet Architecture Board                  |
| Governance Tier              | L3 (oversight role within IETF/ISOC)         |
| Role                         | Architectural oversight of internet protocols |
| Key responsibilities          | Appoints RFC Editor; IESG oversight; liaison with other SDOs |
| Key outputs                  | Architectural statements, RFC 1958 (Internet Architecture) |

### SECTION 3.4 — IANA (INTERNET ASSIGNED NUMBERS AUTHORITY)

| Field                        | Value                                         |
|------------------------------|-----------------------------------------------|
| Full Name                    | Internet Assigned Numbers Authority           |
| Governance Tier              | L2 (quasi-governmental — NTIA-linked history)|
| Operated by                  | ICANN (since 2000)                            |
| Key registries               | Port numbers, protocol parameters, IP address blocks (delegated to RIRs), TLDs, ASNs |
| Importance                   | Every RFC that defines a new number/code/port must register with IANA |
| IANA Considerations in RFCs  | Mandatory section in IETF RFCs                |

### SECTION 3.5 — W3C (WORLD WIDE WEB CONSORTIUM)

Full encyclopedia entry:

| Field                        | Value                                         |
|------------------------------|-----------------------------------------------|
| Full Name                    | World Wide Web Consortium                     |
| Abbreviation                 | W3C                                           |
| Entry Type                   | Consortium Standards Body                    |
| Governance Tier              | L3                                            |
| Founded                      | 1994 by Tim Berners-Lee                       |
| Organizational Model         | Hosted by MIT, ERCIM, Keio University, Beihang — transitioning to W3C Inc. (2023) |
| Membership                   | Fee-based — corporations, academic institutions, government agencies |
| Decision Model               | W3C Advisory Committee vote (supermajority) + Director/CEO approval |
| Output Documents             | W3C Recommendation (normative), Working Draft, Note |
| Specification Access         | Free — all specifications publicly available |
| IP/Patent Policy             | W3C Patent Policy — RF (Royalty Free) for Recommendations |
| Key domains                  | HTML, CSS, DOM, SVG, XML, JSON-LD, WebAssembly, Accessibility, Semantic Web |

**W3C Standards Track:**
```
Editor's Draft (informal)
        ↓
First Public Working Draft (FPWD)
        ↓
Working Draft(s) — iterative revisions
        ↓
Candidate Recommendation (CR) — feature-complete, seeking implementation
        ↓
Candidate Recommendation Snapshot
        ↓
Proposed Recommendation (PR) — AC vote initiated
        ↓
W3C Recommendation (REC) — published standard
        ↓
(Optional) Revised/Superseded Recommendation
```

**Key W3C Recommendations (produce table):**

| Standard     | Technology                                    | Status     | Year   |
|--------------|-----------------------------------------------|------------|--------|
| HTML         | HyperText Markup Language (now with WHATWG)  | Living Std | —      |
| CSS Level 3  | Cascading Style Sheets (modular)             | Modular REC| Ongoing|
| DOM           | Document Object Model                        | Living Std | —      |
| SVG 2         | Scalable Vector Graphics                     | CR/REC     | 2018   |
| XML 1.0       | Extensible Markup Language                   | 5th Edition| 2008   |
| XML Schema   | XML Schema Definition Language (XSD)         | REC        | 2001/2004|
| XSLT 3.0     | XSL Transformations                          | REC        | 2017   |
| XPath 3.1    | XML Path Language                             | REC        | 2017   |
| SOAP 1.2     | Simple Object Access Protocol                | REC        | 2003   |
| WSDL 2.0     | Web Services Description Language            | REC        | 2007   |
| JSON-LD 1.1  | JSON Linked Data                             | REC        | 2020   |
| WebAssembly  | Binary instruction format for stack VM       | REC        | 2019   |
| WebRTC 1.0   | Real-Time Communication for the Web         | REC        | 2021   |
| WCAG 2.2     | Web Content Accessibility Guidelines         | REC        | 2023   |
| ARIA 1.2     | Accessible Rich Internet Applications        | REC        | 2023   |
| ActivityPub  | Federated social networking protocol          | REC        | 2018   |

### SECTION 3.6 — WHATWG (WEB HYPERTEXT APPLICATION TECHNOLOGY WORKING GROUP)

| Field                        | Value                                         |
|------------------------------|-----------------------------------------------|
| Full Name                    | Web Hypertext Application Technology Working Group |
| Abbreviation                 | WHATWG                                        |
| Entry Type                   | Community Standards Body                     |
| Governance Tier              | L5 (community-driven, though dominated by browser vendors) |
| Founded                      | 2004 (breakaway from W3C over XHTML2)        |
| Members / Steering Group     | Apple, Google, Microsoft, Mozilla            |
| Decision Model               | Editor-driven with steering group override    |
| Output                       | "Living Standards" — no versioned releases   |
| Key standards                | HTML Living Standard, DOM Living Standard, Fetch Living Standard, URL Living Standard |
| Relationship to W3C          | 2019 Memorandum of Understanding: WHATWG maintains HTML/DOM, W3C publishes snapshots |

**Disambiguation: W3C vs. WHATWG**
Explicitly explain:
- Why WHATWG was created (W3C's XHTML2 failure, desire for living standards)
- The "fork and rejoin" history of HTML5
- Current division of labor: WHATWG owns the living standard; W3C references it
- What "living standard" means vs. versioned releases
- Why developers should consult WHATWG specs for HTML/DOM, not W3C archived versions

### SECTION 3.7 — UNICODE CONSORTIUM

Full encyclopedia entry:

| Field                        | Value                                         |
|------------------------------|-----------------------------------------------|
| Full Name                    | Unicode Consortium                            |
| Entry Type                   | Standards Body (non-profit consortium)        |
| Governance Tier              | L3                                            |
| Founded                      | 1991                                          |
| Headquarters                 | Mountain View, California, USA               |
| Membership                   | Corporate (Apple, Google, Microsoft, IBM, Adobe, Oracle) + institutional + individual |
| Key output                   | Unicode Standard, Unicode Character Database (UCD), CLDR (Common Locale Data Repository) |
| Relationship to ISO          | Unicode Standard ≡ ISO/IEC 10646 (synchronized, not identical — governance differs) |
| Current version              | Unicode 15.1 (2023)                           |

**Key Unicode Technical Standards and Reports:**

| Identifier | Title                                               |
|------------|-----------------------------------------------------|
| Unicode 15.x| Core standard — character code points               |
| UTS #10    | Unicode Collation Algorithm                         |
| UTS #18    | Unicode Regular Expressions                         |
| UTS #46    | Unicode IDNA Compatibility Processing               |
| UTS #51    | Unicode Emoji                                       |
| UTR #50    | Unicode Vertical Text Layout                        |
| UAX #9     | Unicode Bidirectional Algorithm                     |
| UAX #15    | Unicode Normalization Forms (NFC, NFD, NFKC, NFKD) |
| CLDR       | Common Locale Data Repository — i18n data          |
| ICU        | International Components for Unicode (reference impl.) |

**Disambiguation: Unicode vs. UTF-8 vs. ISO/IEC 10646**
- Unicode = the character repertoire + semantics (the consortium's standard)
- UTF-8 = one of the encodings of Unicode (others: UTF-16, UTF-32, CESU-8)
- ISO/IEC 10646 = the ISO version of the same code point set (synchronized annually)
- UTF-8 was designed by Rob Pike and Ken Thompson; now defined by RFC 3629

### SECTION 3.8 — ECMA INTERNATIONAL

| Field                        | Value                                         |
|------------------------------|-----------------------------------------------|
| Full Name                    | Ecma International (formerly ECMA — European Computer Manufacturers Association) |
| Entry Type                   | Industry Standards Association               |
| Governance Tier              | L3                                            |
| Founded                      | 1961 (oldest European IT standards body)     |
| Headquarters                 | Geneva, Switzerland                          |
| Key Technical Committees     | TC39 (ECMAScript/JavaScript), TC45 (OOXML), TC49 (JSON) |

**Key ECMA Standards:**

| Standard       | Technology                                | TC    |
|----------------|-------------------------------------------|-------|
| ECMA-262       | ECMAScript Language Specification (JavaScript) | TC39 |
| ECMA-404       | JSON Data Interchange Syntax              | TC49  |
| ECMA-376       | Office Open XML (OOXML) — co-published with ISO/IEC 29500 | TC45 |
| ECMA-334       | C# Language Specification                 | TC49  |
| ECMA-335       | Common Language Infrastructure (CLI/.NET) | TC49  |
| ECMA-119       | CD-ROM volume and file structure (ISO 9660)| TC15 |

**TC39 Process (ECMAScript/JavaScript standardization):**
```
Stage 0 — Strawperson: Any idea, no formal requirements
Stage 1 — Proposal: Champions assigned, problem statement written
Stage 2 — Draft: Formal spec text started, syntax/semantics defined
Stage 2.7 — (added 2023) Implementation testing phase
Stage 3 — Candidate: Spec complete, browser vendors implement
Stage 4 — Finished: Two conformant implementations + test262 tests → included in next annual release
```
Annual release cycle: ES2015, ES2016, ..., ES2024, ES2025

### SECTION 3.9 — OASIS (ORGANIZATION FOR THE ADVANCEMENT OF STRUCTURED INFORMATION STANDARDS)

| Field                        | Value                                         |
|------------------------------|-----------------------------------------------|
| Full Name                    | OASIS Open                                    |
| Entry Type                   | Consortium SDO — specializes in open standards for web services, enterprise XML, IoT, security |
| Governance Tier              | L3                                            |
| Founded                      | 1993                                          |
| Key standards                | SAML, WS-*, MQTT, TOSCA, CAP, XLIFF, LegalXML|

**Key OASIS Standards:**

| Standard     | Full Name                                         | Domain         |
|--------------|---------------------------------------------------|----------------|
| SAML 2.0     | Security Assertion Markup Language                | Identity/SSO   |
| MQTT 5.0     | Message Queue Telemetry Transport                 | IoT messaging  |
| AMQP 1.0     | Advanced Message Queuing Protocol (now ISO/IEC 19464) | Enterprise messaging |
| WS-Security  | Web Services Security                             | SOAP security  |
| WS-Trust     | Web Services Trust Language                       | Identity       |
| TOSCA 2.0    | Topology and Orchestration Specification for Cloud| Cloud/DevOps   |
| CAP 1.2      | Common Alerting Protocol                          | Emergency alerts|
| XLIFF 2.2    | XML Localization Interchange File Format          | i18n/l10n      |
| OpenDocument (ODF) | Open Document Format for Office Applications | Document format|

### SECTION 3.10 — OPENID FOUNDATION

| Field                        | Value                                         |
|------------------------------|-----------------------------------------------|
| Full Name                    | OpenID Foundation                            |
| Governance Tier              | L5                                            |
| Key standards                | OpenID Connect 1.0 (OIDC), FAPI, SSIF, MODRNA |
| Relationship to IETF         | OIDC built on top of OAuth 2.0 (IETF RFC 6749) |
| Relationship to FIDO Alliance| Complementary — OIDC = identity layer, FIDO = authentication layer |

### SECTION 3.11 — COMPARISON TABLE: INTERNET/WEB STANDARDS BODIES

| Body         | Tier | Membership | Spec Access | Output Type     | Decision Model    | Key Domain             |
|--------------|------|------------|-------------|-----------------|-------------------|------------------------|
| IETF         | L3   | Open       | Free        | RFC             | Rough consensus   | Internet protocols     |
| W3C          | L3   | Fee-based  | Free        | Recommendation  | AC vote           | Web platform           |
| WHATWG       | L5   | Vendors    | Free        | Living Standard | Editor + steering | HTML, DOM, Fetch, URL  |
| Unicode      | L3   | Fee-based  | Free        | Unicode Std     | Consortium        | Character encoding     |
| ECMA         | L3   | Fee-based  | Free        | ECMA-xxx        | TC vote           | ECMAScript, JSON, C#   |
| OASIS        | L3   | Fee-based  | Free/Paid   | OASIS Standard  | TC ballot         | XML, SAML, MQTT, AMQP  |
| ICANN/IANA   | L2   | Multi-stake| Public      | Registry/Policy | Multi-stakeholder | DNS, IP, ports         |
| OpenID Fdn   | L5   | Open       | Free        | Specification   | Working Group     | Identity (OIDC)        |

### SECTION 3.12 — DISAMBIGUATION: INTERNET STANDARDS CONFUSION PAIRS

Explicitly address:

1. **IETF RFC vs. W3C Recommendation**: Different organizations, different processes, different domains
   - IETF: transport, network, security protocols
   - W3C: web application platform (HTML, CSS, JS APIs)

2. **W3C vs. WHATWG on HTML**: W3C no longer maintains HTML — WHATWG does
   - W3C HTML 5.3 was abandoned; WHATWG HTML Living Standard is canonical

3. **ECMAScript vs. JavaScript**: ECMAScript is the ECMA standard; JavaScript is Google/Mozilla/Apple's implementation name
   - The V8 engine implements ECMAScript; "JavaScript" is not an ECMA trademark

4. **Unicode vs. UTF-8**: Unicode defines code points; UTF-8 is one encoding of those code points
   - UTF-8 is dominant on the web (>98% of websites); but Unicode Standard itself doesn't mandate UTF-8

5. **IANA vs. ICANN**: IANA is a function; ICANN is the organization that performs that function

6. **RFC = Informational ≠ RFC = Standard**: Not all RFCs are standards
   - RFC 1149 (IP over Avian Carriers) is Informational — not a real standard
   - Always check the RFC status: Standards Track, BCP, Informational, Experimental, Historic

---

## OUTPUT FORMAT REQUIREMENTS

- All RFC numbers must be exact (verify against known data)
- All W3C specification status must use official W3C status terms
- Minimum length: 4,500 words
- All tables fully populated — no empty cells
