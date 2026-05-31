# VOLUME 05 — BINARY ENCODING RULES & ASN.1

## Universal Data Contract, Serialization, IDL & Interoperability Architecture Encyclopedia

---

## 05.0 — What Are Encoding Rules (Formal Definition)

**Definition:** Encoding rules are formal specifications that define how abstract data values (described by a notation like ASN.1) are converted into specific, deterministic byte sequences. Unlike serialization formats (Volume 04) which combine format definition WITH implementation, encoding rules exist as separate specifications applied to an independent abstract syntax.

**Key Distinction:**
- **Volume 04 (Serialization Formats):** Describes FORMAT behavior — how Protobuf, JSON, CBOR etc. work in practice
- **Volume 05 (Encoding Rules):** Describes FORMAL RULE SYSTEMS — the mathematical/syntactic rules that convert abstract types into byte streams, especially the ASN.1 family

**Separation of Concerns in ASN.1:**
```
┌──────────────────────────────────────────────────┐
│        ABSTRACT SYNTAX (ASN.1 — X.680)           │
│   "What data looks like" — types and values      │
└──────────────────────────────────────────────────┘
                     ↓  ↓  ↓  ↓  ↓
        ┌────┐ ┌────┐ ┌────┐ ┌────┐ ┌────┐
        │BER │ │DER │ │PER │ │XER │ │OER │
        └────┘ └────┘ └────┘ └────┘ └────┘
         ↓       ↓       ↓       ↓       ↓
      (bytes) (bytes) (bits)  (XML)  (bytes)
```

This is the ONLY family of standards with this separation. Protobuf, Avro, CBOR etc. combine abstract syntax + encoding in a single specification.

---

## 05.1 — ASN.1 Overview, History, and Governance

### Full Name
Abstract Syntax Notation One

### Governing Standards

| Standard | ISO Number | ITU-T Number | Content |
|----------|-----------|--------------|---------|
| Basic Notation | ISO/IEC 8824-1 | X.680 | Core type system |
| Information Object Classes | ISO/IEC 8824-2 | X.681 | Open type system |
| Constraint Specification | ISO/IEC 8824-3 | X.682 | Constraints |
| Parameterization | ISO/IEC 8824-4 | X.683 | Generic types |
| BER, CER, DER | ISO/IEC 8825-1 | X.690 | Basic/Canonical/Distinguished encoding |
| PER | ISO/IEC 8825-2 | X.691 | Packed encoding |
| XER | ISO/IEC 8825-4 | X.693 | XML encoding |
| OER | ISO/IEC 8825-7 | X.696 | Octet encoding |
| JER | ISO/IEC 8825-8 | X.697 | JSON encoding |

### History

| Year | Event |
|------|-------|
| 1984 | First standardized (ISO 8824, ITU-T X.409) |
| 1988 | Revised as X.208/X.209; BER formalized |
| 1994 | PER added (X.691) — packed encoding for telecom |
| 1997 | Major revision: X.680-X.683 replace X.208 |
| 2002 | XER added (X.693) — XML mapping |
| 2008 | Constraint specification enhancements |
| 2015 | OER (X.696) and JER (X.697) added |
| 2021 | Latest edition updates |

### Why ASN.1 Matters Today

| Domain | Usage |
|--------|-------|
| **PKI/TLS** | X.509 certificates, CRLs, OCSP — all DER-encoded |
| **3GPP LTE/5G** | RRC, NAS, NGAP, XnAP, E1AP, F1AP — unaligned PER |
| **SNMP** | MIBs use SMIv2 (ASN.1 subset); PDUs encoded in BER |
| **PKCS standards** | PKCS#1, #7, #8, #10, #12 — DER encoding |
| **CMS/S-MIME** | SignedData, EnvelopedData — DER |
| **GSM/UMTS** | SS7/MAP signaling — BER |
| **Aviation** | ACARS, ATN — PER |
| **Automotive** | V2X (ETSI ITS) — UPER (Unaligned PER) |

### Governance

| Aspect | Value |
|--------|-------|
| Bodies | ISO (ISO 8824/8825) + ITU-T (X.680-X.697) — **Joint maintenance** |
| Tier | **1** (ISO) + **2** (ITU-T) — highest governance tier |
| Access | ITU-T Recommendations available free online; ISO documents paid |
| Stability | Backward-compatible since 1984 |

---

## 05.2 — ASN.1 Module Syntax (X.680)

### Module Structure

```asn1
ModuleName { iso(1) identified-organization(3) ... } 
DEFINITIONS AUTOMATIC TAGS ::= BEGIN

    EXPORTS TypeA, TypeB;
    IMPORTS TypeC, TypeD FROM OtherModule;

    -- Type assignment
    MyType ::= SEQUENCE {
        field1   INTEGER,
        field2   BOOLEAN
    }
    
    -- Value assignment
    myValue MyType ::= { field1 42, field2 TRUE }

END
```

### Tagging Modes

| Mode | Keyword | Effect |
|------|---------|--------|
| AUTOMATIC TAGS | `DEFINITIONS AUTOMATIC TAGS ::=` | Tags assigned automatically (most common modern practice) |
| IMPLICIT TAGS | `DEFINITIONS IMPLICIT TAGS ::=` | Context tags replace underlying tag |
| EXPLICIT TAGS | `DEFINITIONS EXPLICIT TAGS ::=` | Context tags wrap underlying tag |

### Type System Reference

| Category | Types |
|----------|-------|
| **Boolean** | `BOOLEAN` |
| **Integer** | `INTEGER`, `INTEGER (0..255)`, `INTEGER { idle(0), active(1) }` |
| **Real** | `REAL` (IEEE 754 or base-10 triple) |
| **Bit/Byte** | `BIT STRING`, `OCTET STRING` |
| **Null** | `NULL` |
| **Identifiers** | `OBJECT IDENTIFIER`, `RELATIVE-OID` |
| **Enumerated** | `ENUMERATED { a(0), b(1), c(2), ... }` |
| **Structured** | `SEQUENCE { ... }`, `SET { ... }` |
| **Collection** | `SEQUENCE OF Type`, `SET OF Type` |
| **Choice** | `CHOICE { alt1 Type1, alt2 Type2 }` |
| **String** | `UTF8String`, `PrintableString`, `IA5String`, `VisibleString`, `BMPString`, `UniversalString`, `NumericString` |
| **Time** | `UTCTime`, `GeneralizedTime`, `DATE`, `TIME-OF-DAY`, `DATE-TIME`, `DURATION` |
| **Open type** | Via Information Object Classes (IOC) |

### Constraints

```asn1
-- Size constraint
Username ::= UTF8String (SIZE (1..64))

-- Value range
Port ::= INTEGER (0..65535)

-- Pattern constraint
PhoneNumber ::= IA5String (PATTERN "[0-9+\-() ]+")

-- From constraint (alphabet restriction)
UpperCase ::= VisibleString (FROM ("A".."Z"))

-- Containing constraint
Payload ::= OCTET STRING (CONTAINING Vehicle)

-- Table constraint (for open types)
Message ::= SEQUENCE {
    id      OBJECT IDENTIFIER,
    content ANY DEFINED BY id
}
```

### Extension Markers

```asn1
-- Version 1
Vehicle ::= SEQUENCE {
    vin     VisibleString (SIZE (17)),
    make    UTF8String,
    model   UTF8String,
    ...,   -- Extension marker: fields below may not be present in v1 decoders
    -- Version 2 additions:
    year    INTEGER (1886..2200) OPTIONAL,
    fuelType FuelType OPTIONAL
}
```

Extension markers enable **backward-compatible** evolution: v1 decoders skip unknown extensions; v2 encoders always include the extension preamble.

---

## 05.3 — BER (Basic Encoding Rules) — X.690

### TLV Structure

Every BER-encoded value follows Tag-Length-Value:

```
┌──────────────┬───────────────┬──────────────────────────┐
│     Tag      │    Length     │         Value            │
│  (1+ bytes)  │  (1+ bytes)  │    (0+ bytes)            │
└──────────────┴───────────────┴──────────────────────────┘
```

### Tag Encoding

```
Single-byte tag (tag number 0-30):
┌─────┬───┬───────┐
│Class│P/C│ Number│
│[7:6]│[5]│[4:0]  │
└─────┴───┴───────┘

Multi-byte tag (tag number ≥ 31):
First byte: [Class][P/C][11111]
Subsequent: [1xxxxxxx]* [0xxxxxxx]  (base-128, MSB=continuation)
```

**Class Bits:**

| Bits [7:6] | Class | Meaning |
|-----------|-------|---------|
| 00 | Universal | Built-in ASN.1 types |
| 01 | Application | Application-specific |
| 10 | Context-specific | Field-level disambiguation |
| 11 | Private | Organization-specific |

**Universal Tag Numbers:**

| Tag | Type | Tag | Type |
|-----|------|-----|------|
| 1 | BOOLEAN | 12 | UTF8String |
| 2 | INTEGER | 16 | SEQUENCE / SEQUENCE OF |
| 3 | BIT STRING | 17 | SET / SET OF |
| 4 | OCTET STRING | 19 | PrintableString |
| 5 | NULL | 22 | IA5String |
| 6 | OBJECT IDENTIFIER | 23 | UTCTime |
| 10 | ENUMERATED | 24 | GeneralizedTime |

### Length Encoding

| Form | Byte Pattern | Meaning |
|------|-------------|---------|
| Short definite | `0xxxxxxx` (0x00-0x7F) | Length value is the byte itself (0-127 bytes) |
| Long definite | `1nnnnnnn` + N bytes | First byte gives N (number of length bytes); following N bytes give actual length |
| Indefinite | `0x80` | Length unknown; content terminated by EOC (0x00 0x00) |

**Examples:**
- Length 38: `0x26` (short form)
- Length 201: `0x81 0xC9` (long form: 1 extra byte)
- Length 1000: `0x82 0x03 0xE8` (long form: 2 extra bytes)

### Value Encoding Per Type

| Type | BER Encoding |
|------|-------------|
| **BOOLEAN** | `0x00` = FALSE; any non-zero = TRUE (DER requires `0xFF` for TRUE) |
| **INTEGER** | Big-endian two's complement, minimum bytes, padded with `0x00` or `0xFF` if needed to preserve sign |
| **BIT STRING** | First content byte = number of unused trailing bits (0-7); remaining = bit data |
| **OCTET STRING** | Raw bytes |
| **NULL** | Zero-length value |
| **OID** | First two arcs merged: `(40×arc1 + arc2)`; remaining arcs in base-128 |
| **SEQUENCE** | Concatenated TLV of each member in definition order |
| **SET** | Concatenated TLV of members (order undefined in BER, sorted in DER) |
| **CHOICE** | TLV of the selected alternative |

### Example: INTEGER encoding

Value 256:
```
Tag:    02          (Universal, Primitive, tag number 2 = INTEGER)
Length: 02          (2 bytes of value)
Value:  01 00       (big-endian 256 = 0x0100)
```

Value -128:
```
Tag:    02
Length: 01
Value:  80          (two's complement -128)
```

---

## 05.4 — DER (Distinguished Encoding Rules) — X.509 Case Study

### DER Restrictions (Canonical BER)

| Rule | BER Allows | DER Requires |
|------|-----------|--------------|
| Length form | Short, long, or indefinite | Shortest definite form only |
| BOOLEAN TRUE | Any non-zero | Only `0xFF` |
| SET order | Any order | Ascending tag order |
| BIT STRING padding | Any trailing bits | All unused bits must be zero |
| String encoding | Primitive or constructed | Primitive only |
| Default values | May include or omit | Must omit DEFAULT values |

**Why DER is essential for cryptography:** A digital signature signs specific bytes. If the same abstract value could be encoded differently (BER allows this), a valid signature could fail verification after re-encoding. DER guarantees exactly one byte encoding per value.

### X.509 Certificate Structure (DER)

```asn1
Certificate ::= SEQUENCE {
    tbsCertificate       TBSCertificate,
    signatureAlgorithm   AlgorithmIdentifier,
    signatureValue       BIT STRING
}

TBSCertificate ::= SEQUENCE {
    version         [0] EXPLICIT INTEGER DEFAULT v1,
    serialNumber         CertificateSerialNumber,
    signature            AlgorithmIdentifier,
    issuer               Name,
    validity             Validity,
    subject              Name,
    subjectPublicKeyInfo SubjectPublicKeyInfo,
    issuerUniqueID  [1]  IMPLICIT UniqueIdentifier OPTIONAL,
    subjectUniqueID [2]  IMPLICIT UniqueIdentifier OPTIONAL,
    extensions      [3]  EXPLICIT Extensions OPTIONAL
}

Validity ::= SEQUENCE {
    notBefore   Time,
    notAfter    Time
}

AlgorithmIdentifier ::= SEQUENCE {
    algorithm   OBJECT IDENTIFIER,
    parameters  ANY DEFINED BY algorithm OPTIONAL
}
```

### DER-Encoded Certificate Hex Dump (fragment)

```
30 82 03 48          -- SEQUENCE, length 840 bytes
  30 82 02 30        -- TBSCertificate SEQUENCE, length 560
    A0 03             -- [0] EXPLICIT (version)
      02 01 02        --   INTEGER 2 (v3)
    02 10             -- serialNumber: INTEGER, 16 bytes
      04 1E ...       --   (serial number bytes)
    30 0D             -- signature AlgorithmIdentifier
      06 09 2A 86 48 86 F7 0D 01 01 0B  -- OID: sha256WithRSAEncryption
      05 00           -- NULL parameters
    30 45             -- issuer: Name (SEQUENCE of RDN)
      31 0B           -- SET (RelativeDistinguishedName)
        30 09         -- SEQUENCE (AttributeTypeAndValue)
          06 03 55 04 06  -- OID: countryName (2.5.4.6)
          13 02 55 53      -- PrintableString "US"
    ...
```

### PEM Format

PEM (Privacy Enhanced Mail) is base64-encoded DER with armor headers:

```
-----BEGIN CERTIFICATE-----
MIIDSjCCAjKgAwIBAgIQRK+wgNajJ7qJMDmGLvhAazANBgkqhkiG9w0BAQUFADA/
MSQwIgYDVQQKExtEaWdpdGFsIFNpZ25hdHVyZSBUcnVzdCBDby4xFzAVBgNVBAMT
...
-----END CERTIFICATE-----
```

---

## 05.5 — PER (Packed Encoding Rules) — 3GPP Case Study

### Core Principle

PER exploits ASN.1 constraints to minimize encoded size. If the schema says `INTEGER (0..7)`, only 3 bits are needed — no tag, no length.

### Aligned vs Unaligned PER

| Property | Aligned PER | Unaligned PER |
|----------|-------------|---------------|
| Bit alignment | Fields start on byte boundaries | Fields start on next available bit |
| Size | Slightly larger (padding) | Smallest possible |
| Decode speed | Faster (byte-aligned reads) | Slower (bit manipulation) |
| 3GPP usage | Some older protocols | RRC, NAS, NGAP, XnAP (most 3GPP) |

### PER Encoding Examples

**Constrained Integer:**
```asn1
Speed ::= INTEGER (0..500)
```
- Range 0..500 → needs ⌈log₂(501)⌉ = 9 bits
- Value 85: binary `001010101` → encoded in 9 bits (UPER) or 2 bytes (APER with padding)

**Enumerated:**
```asn1
FuelType ::= ENUMERATED { petrol(0), diesel(1), electric(2), hybrid(3) }
```
- 4 values → ⌈log₂(4)⌉ = 2 bits
- `electric(2)` → bits `10`

**Sequence with optional fields:**
```asn1
Vehicle ::= SEQUENCE {
    vin     VisibleString (SIZE(17)),
    make    UTF8String,
    speed   INTEGER (0..500) OPTIONAL,
    location GeoLocation OPTIONAL
}
```
PER encoding:
```
[Preamble: 2 bits for 2 OPTIONAL fields]
  bit 0: speed present? (1=yes, 0=no)
  bit 1: location present? (1=yes, 0=no)
[vin: 17 bytes, directly encoded (size known from constraint)]
[make: length-determinant + UTF-8 bytes]
[if speed present: 9 bits for value 0..500]
[if location present: encoded GeoLocation fields]
```

### 3GPP Usage

| Protocol | Layer | ASN.1 Encoding | Specification |
|----------|-------|---------------|---------------|
| **RRC** (Radio Resource Control) | Access Stratum | Unaligned PER | TS 36.331 (LTE), TS 38.331 (5G NR) |
| **NAS** (Non-Access Stratum) | NAS | Unaligned PER | TS 24.301 (LTE), TS 24.501 (5G) |
| **NGAP** | N2 interface | Aligned PER | TS 38.413 |
| **XnAP** | Xn interface | Aligned PER | TS 38.423 |
| **E1AP** | E1 interface | Aligned PER | TS 38.463 |
| **F1AP** | F1 interface | Aligned PER | TS 38.473 |
| **S1AP** | S1 interface | Aligned PER | TS 36.413 |

---

## 05.6 — XER (XML Encoding Rules) — X.693

### Two Variants

| Variant | Purpose |
|---------|---------|
| **BXER** (Basic XER) | Full verbose XML; human-readable |
| **CXER** (Canonical XER) | Canonical form for digital signatures over XML |

### Example

Given ASN.1:
```asn1
Vehicle ::= SEQUENCE {
    vin    VisibleString,
    speed  INTEGER,
    active BOOLEAN
}
```

BXER encoding:
```xml
<Vehicle>
  <vin>WBA3A5C55CF256789</vin>
  <speed>85</speed>
  <active><true/></active>
</Vehicle>
```

---

## 05.7 — OER (Octet Encoding Rules) — X.696

### Design Goals

OER provides a balance between PER's compactness and BER's simplicity:
- No bit-level manipulation (always byte-aligned)
- Fixed-width integers based on constraints (1, 2, 4, or 8 bytes)
- No tags for known-present fields
- Simpler encoder/decoder than PER

### Encoding Rules

| Type | OER Encoding |
|------|-------------|
| INTEGER (0..255) | 1 byte unsigned |
| INTEGER (0..65535) | 2 bytes unsigned, big-endian |
| INTEGER (-128..127) | 1 byte signed |
| BOOLEAN | 1 byte: 0x00=FALSE, 0xFF=TRUE |
| ENUMERATED | Integer index (size based on enum count) |
| SEQUENCE | Presence bitmap + field values in order |
| OCTET STRING (fixed) | Raw bytes (no length prefix) |
| OCTET STRING (variable) | Length determinant + bytes |
| CHOICE | Choice index + value |

---

## 05.8 — JER (JSON Encoding Rules) — X.697

### Mapping Rules

| ASN.1 Type | JSON Representation |
|-----------|-------------------|
| BOOLEAN | `true` / `false` |
| INTEGER | JSON number |
| REAL | JSON number (or special string for Infinity/NaN) |
| ENUMERATED | JSON string (enumeration identifier name) |
| BIT STRING | JSON object `{"length": N, "value": "hex_string"}` |
| OCTET STRING | JSON string (base64-encoded) |
| NULL | JSON `null` |
| UTF8String | JSON string |
| SEQUENCE | JSON object |
| SEQUENCE OF | JSON array |
| SET | JSON object |
| SET OF | JSON array |
| CHOICE | JSON object with single key = alternative name |
| OBJECT IDENTIFIER | JSON string (dot notation) |

### Use Case

JER enables RESTful APIs to expose data defined by ASN.1 schemas in JSON format — bridging telecom/PKI systems (traditionally BER/DER/PER) with web services.

---

## 05.9 — IEEE 754 Floating Point

### Standard

IEEE 754-2019 (revision of 754-2008). Governance: IEEE — Tier 2.

### Formats

| Format | Total Bits | Sign | Exponent | Mantissa | Decimal Digits |
|--------|-----------|------|----------|----------|---------------|
| binary16 (half) | 16 | 1 | 5 | 10 | ~3.3 |
| binary32 (float) | 32 | 1 | 8 | 23 | ~7.2 |
| binary64 (double) | 64 | 1 | 11 | 52 | ~15.9 |
| binary128 (quad) | 128 | 1 | 15 | 112 | ~34.0 |
| decimal32 | 32 | 1 | — | — | 7 |
| decimal64 | 64 | 1 | — | — | 16 |
| decimal128 | 128 | 1 | — | — | 34 |

### binary32 Layout

```
┌───┬──────────┬───────────────────────┐
│ S │ Exponent │      Mantissa         │
│ 1 │  8 bits  │      23 bits          │
└───┴──────────┴───────────────────────┘
 31   30    23   22                   0

Value = (-1)^S × 2^(E-127) × 1.Mantissa  (normal)
Value = (-1)^S × 2^(-126) × 0.Mantissa   (subnormal, E=0)
```

### Special Values

| Exponent | Mantissa | Meaning |
|----------|----------|---------|
| 0 | 0 | +0 or -0 (based on sign) |
| 0 | ≠0 | Subnormal (denormalized) |
| 255 (all 1s) | 0 | +∞ or -∞ |
| 255 (all 1s) | ≠0 | NaN (quiet if MSB=1, signaling if MSB=0) |

### Serialization Format Mappings

| Format | Float Encoding | Byte Order |
|--------|---------------|-----------|
| Protobuf `float` | IEEE 754 binary32 | Little-endian |
| Protobuf `double` | IEEE 754 binary64 | Little-endian |
| Avro `float` | IEEE 754 binary32 | Little-endian |
| Avro `double` | IEEE 754 binary64 | Little-endian |
| CBOR (major 7) | IEEE 754 binary16/32/64 | Big-endian |
| MessagePack 0xCA | IEEE 754 binary32 | Big-endian |
| MessagePack 0xCB | IEEE 754 binary64 | Big-endian |
| JSON `number` | Text decimal (implementation-dependent precision) | N/A (text) |
| FlatBuffers `float32` | IEEE 754 binary32 | Little-endian (native) |

---

## 05.10 — Network Byte Order and Endianness

### Byte Order Types

| Order | Name | MSB Position | Used By |
|-------|------|-------------|---------|
| **Big-endian** | Network byte order | Lowest address | TCP/IP headers, ASN.1 BER/DER, CBOR, MessagePack, Java |
| **Little-endian** | Intel/ARM default | Highest address | x86/x64, ARM (default), Protobuf, Avro, FlatBuffers, USB |
| **Mixed/Bi** | PDP-endian | Middle | Historical (PDP-11); SOME/IP (header BE, payload configurable) |

### POSIX Byte Order Functions

| Function | Purpose |
|----------|---------|
| `htons()` / `htonl()` | Host-to-network short/long (convert to big-endian) |
| `ntohs()` / `ntohl()` | Network-to-host short/long (convert from big-endian) |

### Alignment and Padding

For zero-copy formats, alignment is critical:

```
struct Example {    // Natural alignment:
  uint8_t  a;      // offset 0, size 1
  // 3 bytes padding
  uint32_t b;      // offset 4, size 4 (aligned to 4)
  uint16_t c;      // offset 8, size 2 (aligned to 2)
  // 2 bytes padding
  uint64_t d;      // offset 12 → padded to 16, size 8 (aligned to 8)
};                  // Total: 24 bytes (vs 15 bytes unpadded)
```

---

## 05.11 — PKCS Standards (DER in PKI)

### PKCS Standards Using ASN.1 DER

| Standard | Full Name | Content |
|----------|----------|---------|
| PKCS#1 | RSA Cryptography Standard | `RSAPublicKey`, `RSAPrivateKey` structures |
| PKCS#7/CMS | Cryptographic Message Syntax | `SignedData`, `EnvelopedData`, `EncryptedData` |
| PKCS#8 | Private Key Information Syntax | `PrivateKeyInfo` (algorithm-agnostic wrapper) |
| PKCS#10 | Certificate Signing Request | `CertificationRequest` sent to CA |
| PKCS#12 | Personal Information Exchange | `.p12`/`.pfx` container for cert + private key |

### RSAPublicKey ASN.1 Structure

```asn1
RSAPublicKey ::= SEQUENCE {
    modulus           INTEGER,  -- n (typically 2048 or 4096 bits)
    publicExponent    INTEGER   -- e (commonly 65537 = 0x10001)
}
```

DER encoding (fragment):
```
30 82 01 22       -- SEQUENCE, length 290 bytes
  02 82 01 01     -- INTEGER (modulus), length 257 bytes
    00 BB 11 ...  -- modulus value (padded with 0x00 for positive)
  02 03           -- INTEGER (exponent), length 3 bytes
    01 00 01      -- value 65537
```

### COSE vs CMS (CBOR-based PKI)

| Aspect | CMS (RFC 5652) | COSE (RFC 9052) |
|--------|---------------|-----------------|
| Encoding | ASN.1 DER | CBOR |
| Structure | `SignedData { ... }` | `COSE_Sign / COSE_Sign1` |
| Ecosystem | Traditional PKI, S/MIME, code signing | IoT, FIDO2/WebAuthn, CWT (CBOR Web Token) |
| Size | Larger (DER overhead) | More compact (CBOR) |

---

## 05.12 — ASN.1 Tools and Compilers

| Tool | Type | Output | License | Key Features |
|------|------|--------|---------|-------------|
| **asn1c** | Open source | C | BSD | Widely used in telecom, embedded; BER/DER/PER/XER/OER |
| **OSS Nokalva** | Commercial | C, C++, Java, C# | Proprietary | Full-featured studio; all encoding rules |
| **Objsys ASN1C** | Commercial | C, C++, Java, C# | Proprietary | High-performance; 3GPP protocol packs |
| **pyasn1** | Open source | Python (runtime) | BSD | Encode/decode without code generation |
| **asn1tools** | Open source | Python (runtime) | MIT | Analysis, codec, compile |
| **Bouncy Castle** | Open source | Java, C# | MIT | ASN.1/DER support for crypto libraries |
| **openssl asn1parse** | Open source | CLI output | Apache 2.0 | Debugging and inspection |
| **ffasn1dump** | Open source | CLI output | MIT | Pretty-print DER/BER structures |
| **Erlang asn1** | Open source | Erlang | Apache 2.0 | OTP standard library; BER/PER |

---

## 05.13 — Encoding Rules Comparison Matrix

| Encoding Rule | Standard | Tier | Self-Describing | Wire Size | Tags on Wire | Schema Required | Parse Complexity | Primary Domain |
|--------------|----------|------|-----------------|-----------|-------------|----------------|-----------------|---------------|
| **BER** | X.690 | 1/2 | Partial (type tags) | Medium | Yes (TLV) | Yes | Moderate | General ASN.1 |
| **DER** | X.690 | 1/2 | Partial | Medium | Yes (TLV) | Yes | Moderate | PKI, X.509, crypto |
| **Aligned PER** | X.691 | 1/2 | No | Very small | No | Yes | Complex | Some telecom |
| **Unaligned PER** | X.691 | 1/2 | No | Smallest | No | Yes | Complex | 3GPP RRC/NAS/NGAP |
| **XER (BXER)** | X.693 | 1/2 | Yes | Large | Yes (XML) | Yes | Moderate (XML) | Debugging, HR view |
| **OER** | X.696 | 1/2 | Partial | Small | No (known fields) | Yes | Simple | IoT, embedded |
| **JER** | X.697 | 1/2 | Yes | Large | Yes (JSON keys) | Yes | Simple (JSON) | REST/web exposure |
| **Protobuf wire** | protobuf.dev | 6 | No | Small | Tag numbers (varint) | Yes (.proto) | Simple | gRPC, cloud |
| **Avro binary** | avro.apache.org | 5 | No | Very small | No | Yes (Avro schema) | Simple | Kafka, Hadoop |
| **CBOR** | RFC 8949 | 2 | Yes | Small | Type in initial byte | No | Simple | IoT, FIDO2 |
| **IEEE 754** | IEEE 754-2019 | 2 | No | Fixed (4/8) | N/A | Context | Trivial | All float encodings |

---

## 05.14 — Cross-References

| Topic | Volume |
|-------|--------|
| ASN.1 as data model and IDL | Volume 01 (Data Modeling), Volume 02 (IDL) |
| Serialization format behavior | Volume 04 — Serialization Formats |
| 3GPP and telecom industry specifics | Volume 11 — Industry-Specific Standards |
| PKI governance and standards | Volume 12 — Governance & Standards |
| ASN.1 compiler tooling details | Volume 13 — Tooling & Code Generation |
| Schema evolution with extension markers | Volume 10 — Schema Evolution |

---

*End of Volume 05 — Binary Encoding Rules & ASN.1*
