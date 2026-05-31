# VOLUME 04 — SERIALIZATION FORMATS

## Universal Data Contract, Serialization, IDL & Interoperability Architecture Encyclopedia

---

## 04.0 — What is Serialization (Formal Definition and Stack Placement)

**Definition:** Serialization is the deterministic process of converting an in-memory data structure into a linear sequence of bytes suitable for storage, transmission, or inter-process communication. Deserialization is the reverse: reconstructing an in-memory data structure from a byte sequence.

A serialization **format** specifies:
1. How primitive types are encoded (integers, floats, strings, booleans, null)
2. How composite types are encoded (arrays, objects/maps, nested structures)
3. How framing and delimiters work (length-prefixed, self-framing, delimited)
4. Whether schema is embedded, referenced, or assumed
5. Whether the format is self-describing (type info in payload) or schema-dependent

**Stack Position:**
```
    Schema Layer (Volume 03)
           ↓
    ┌─────────────────────────────────────┐
    │       SERIALIZATION FORMAT          │  ← THIS VOLUME
    │  (bytes on wire / bytes on disk)    │
    └─────────────────────────────────────┘
           ↓
    Binary Encoding Rules (Volume 05)
           ↓
    RPC / IPC / Transport (Volume 06/07/14)
```

**Key Taxonomy:**

| Dimension | Categories |
|-----------|-----------|
| Human-readability | Text (JSON, XML, YAML) vs Binary (Protobuf, Avro, CBOR) |
| Schema dependency | Schema-required (Protobuf, Avro) vs Self-describing (JSON, CBOR) |
| Access pattern | Parse-then-access (JSON, Protobuf) vs Zero-copy/in-place (FlatBuffers, Cap'n Proto) |
| Orientation | Row-oriented (most) vs Columnar (Arrow, Parquet) |

---

## 04.1 — Text-Based Serialization Formats

### 04.1.1 — JSON (JavaScript Object Notation)

| Property | Value |
|----------|-------|
| Specification | ECMA-404 (2017), RFC 8259 (2017) |
| Governance Tier | 5 (ECMA) / 2 (IETF) |
| Character encoding | UTF-8 (MUST per RFC 8259); UTF-16, UTF-32 allowed by ECMA-404 |
| Data types | null, boolean, number, string, array, object |
| Self-describing | Yes (field names in payload) |
| Schema | None native; JSON Schema (Vol 03) for validation |
| Comments | Not supported (except JSONC, JSON5 variants) |

**Type System Limitation:** JSON has a single `number` type — no distinction between integers and floats. The value `1` and `1.0` may parse differently depending on implementation. RFC 8259 warns that numbers outside `[-(2^53)+1, (2^53)-1]` may lose precision in implementations using IEEE 754 double.

**Encoding of values:**
```json
{
  "null_val": null,
  "bool_val": true,
  "int_val": 42,
  "float_val": 3.14159,
  "sci_val": 6.022e23,
  "string_val": "hello\nworld",
  "unicode_val": "\u0041\u0042\u0043",
  "array_val": [1, 2, 3],
  "object_val": {"key": "value"},
  "nested": {"arr": [{"a": 1}]}
}
```

**Streaming Variants:**

| Variant | Mechanism | Use Case |
|---------|-----------|----------|
| NDJSON (Newline-Delimited JSON) | One JSON object per line, `\n` separator | Log ingestion (Elasticsearch), streaming APIs |
| JSON Lines (.jsonl) | Same as NDJSON (different name) | Data processing pipelines |
| JSON Streaming (RFC 7464) | Record separator (0x1E) + JSON value | Unreliable streams |
| JSON Sequence | Concatenated JSON values | Minimal framing |

---

### 04.1.2 — XML (Extensible Markup Language)

| Property | Value |
|----------|-------|
| Specification | W3C XML 1.0 Fifth Edition (2008), XML 1.1 (2004) |
| Governance Tier | 2 (W3C) |
| Character encoding | UTF-8 (default), UTF-16 (alternative), any declared encoding |
| Data types | Text only (types via XSD or application layer) |
| Self-describing | Partially (element names carry semantics; types require schema) |
| Schema | XSD 1.0/1.1, DTD, RelaxNG, Schematron |
| Namespaces | Yes (xmlns declarations) |

**Structural Elements:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- Processing instruction and declaration -->
<vehicle xmlns="http://automotive.example.com/vehicle"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://automotive.example.com/vehicle vehicle.xsd">
    <vin>WBA3A5C55CF256789</vin>
    <make>BMW</make>
    <model>320i</model>
    <year>2023</year>
    <speed unit="kmh">85.5</speed>
    <location>
        <latitude>48.8566</latitude>
        <longitude>2.3522</longitude>
    </location>
    <metadata>
        <![CDATA[Raw text that may contain <special> characters]]></metadata>
</vehicle>
```

**Parsing Models:**

| Parser | Model | Memory | Use Case |
|--------|-------|--------|----------|
| DOM | Tree in memory | O(n) full document | Small documents, random access |
| SAX | Event-driven push | O(1) | Large documents, streaming |
| StAX | Pull parser (cursor) | O(1) | Large documents, selective processing |
| VTD-XML | Virtual Token Descriptor | O(n) | Zero-copy XPath on large docs |

---

### 04.1.3 — YAML (YAML Ain't Markup Language)

| Property | Value |
|----------|-------|
| Specification | yaml.org/spec/1.2.2 (2021) |
| Governance Tier | 8 (Community) |
| Types | string, integer, float, boolean, null, binary (base64), timestamp |
| Superset of JSON | Yes (YAML 1.2 — any valid JSON is valid YAML) |
| Multi-document | Yes (document separator `---`, document end `...`) |
| Comments | Yes (`#` to end of line) |

**Key Features:**
```yaml
# Anchors and aliases (DRY)
defaults: &defaults
  timeout: 30
  retries: 3

development:
  <<: *defaults  # Merge key (inherits defaults)
  debug: true

production:
  <<: *defaults
  debug: false
  retries: 5

# Multi-line strings
description: |
  This is a literal block scalar.
  Newlines are preserved.

folded: >
  This is a folded block scalar.
  Newlines become spaces.

# Complex keys and flow style
{complex_key: [1, 2, 3]}: value
flow_mapping: {key1: val1, key2: val2}
flow_sequence: [a, b, c]
```

**Security Concerns:**
- **YAML deserialization attacks:** Some parsers support language-specific tags (`!!python/object`, `!!ruby/object`) that can execute arbitrary code
- **Billion laughs (YAML bomb):** Recursive anchors/aliases can cause exponential expansion
- **Boolean ambiguity:** `yes`, `no`, `on`, `off`, `true`, `false` all parsed as booleans in YAML 1.1; Norway country code `NO` becomes `false`

---

### 04.1.4 — TOML (Tom's Obvious Minimal Language)

| Property | Value |
|----------|-------|
| Specification | toml.io/en/v1.0.0 (2021) |
| Governance Tier | 8 (Community, GitHub) |
| Types | String, Integer, Float, Boolean, Offset Date-Time, Local Date-Time, Local Date, Local Time, Array, Table, Inline Table |
| Key feature | Unambiguous — no implicit type conversions |

```toml
# vehicle.toml
[vehicle]
vin = "WBA3A5C55CF256789"
make = "BMW"
model = "320i"
year = 2023
fuel_type = "PETROL"

[vehicle.location]
latitude = 48.8566
longitude = 2.3522

[[vehicle.diagnostic_codes]]  # Array of tables
code = "P0301"
severity = "WARNING"

[[vehicle.diagnostic_codes]]
code = "P0420"
severity = "INFO"
```

---

### 04.1.5 — CSV (Comma-Separated Values)

| Property | Value |
|----------|-------|
| Specification | RFC 4180 (2005, Informational) |
| Governance Tier | 2 (IETF) |
| Types | None — all values are text |
| Schema | None; header row provides field names |
| MIME type | `text/csv` |

**Limitations (no formal solutions):**
- No standard for null vs empty string
- No standard for nested data
- Delimiter conflicts (comma in values)
- No type information (dates, numbers, booleans are all strings)
- Encoding ambiguity (UTF-8 vs Latin-1 vs Windows-1252)

---

### 04.1.6 — INI / Properties Files

No formal standard. Key-value pairs with optional sections:

```ini
[database]
host = localhost
port = 5432
name = vehicles

[logging]
level = INFO
file = /var/log/app.log
```

---

## 04.2 — Binary Serialization Formats

### 04.2.1 — Protocol Buffers Wire Format

| Property | Value |
|----------|-------|
| Specification | protobuf.dev/programming-guides/encoding |
| Governance | Google / CNCF — Tier 6 |
| Schema required | Yes (.proto) |
| Self-describing | No (field names not on wire) |
| Byte order | Little-endian for fixed types |

**Encoding Model:**

Each field encoded as: `[tag][value]` where tag = `(field_number << 3) | wire_type`

| Wire Type | ID | Encoding | Used For |
|-----------|----|----------|----------|
| VARINT | 0 | Variable-length integer (7-bit chunks, MSB continuation) | int32, int64, uint32, uint64, sint32, sint64, bool, enum |
| I64 | 1 | Fixed 8 bytes, little-endian | fixed64, sfixed64, double |
| LEN | 2 | Varint length + bytes | string, bytes, embedded messages, packed repeated fields |
| I32 | 5 | Fixed 4 bytes, little-endian | fixed32, sfixed32, float |

**Varint Encoding Example:**
```
Value: 300
Binary: 100101100 (9 bits)
Split into 7-bit groups (LSB first): 0101100 | 0000010
Add continuation bits:                10101100 | 00000010
Hex bytes: AC 02
```

**ZigZag Encoding (for sint32/sint64):**
Maps signed integers to unsigned: `(n << 1) ^ (n >> 31)` (for 32-bit)
- 0 → 0, -1 → 1, 1 → 2, -2 → 3, 2 → 4, ...
- Efficient for values near zero regardless of sign

**Complete Wire Example:**
```protobuf
message Example {
  int32 a = 1;   // value: 150
  string b = 2;  // value: "testing"
}
```
```
Wire bytes (hex):
08 96 01    ← field 1, wire type 0 (varint), value 150
12 07 74 65 73 74 69 6E 67  ← field 2, wire type 2 (LEN), length 7, "testing"

Breakdown:
  08 = (1 << 3) | 0 = tag for field 1, varint
  96 01 = varint 150 (0b10010110 0b00000001)
  12 = (2 << 3) | 2 = tag for field 2, length-delimited
  07 = varint 7 (length of "testing")
  74 65 73 74 69 6E 67 = UTF-8 "testing"
```

---

### 04.2.2 — Apache Avro Binary Encoding

| Property | Value |
|----------|-------|
| Specification | avro.apache.org/docs/current/specification |
| Governance | Apache ASF — Tier 5 |
| Schema required | Yes (Avro schema JSON) |
| Self-describing | No (field order from schema; no field names/tags on wire) |
| Key insight | **No tags, no field names** — fields encoded in declaration order per schema |

**Encoding Rules:**

| Type | Encoding |
|------|----------|
| null | Zero bytes |
| boolean | Single byte: 0x00 (false) or 0x01 (true) |
| int / long | Variable-length ZigZag (same as protobuf sint32/sint64) |
| float | 4 bytes IEEE 754, little-endian |
| double | 8 bytes IEEE 754, little-endian |
| string | Long (byte count as varint) + UTF-8 bytes |
| bytes | Long (byte count as varint) + raw bytes |
| record | Concatenation of field values in schema order |
| array | Series of blocks; each block = long count + items; terminated by 0-count block |
| map | Same as array but each item is key-string + value |
| union | Int index (which type in union list) + encoded value of that type |
| enum | Int index into the enum's symbol list |
| fixed | Exactly N bytes (N from schema) |

**Avro Object Container File Format:**
```
┌──────────────────────────────────────────┐
│  Magic bytes: "Obj" + 0x01 (4 bytes)    │
├──────────────────────────────────────────┤
│  File metadata (map):                    │
│    "avro.schema" → JSON schema string    │
│    "avro.codec"  → "null"|"deflate"|"snappy" │
├──────────────────────────────────────────┤
│  16-byte sync marker (random per file)   │
├──────────────────────────────────────────┤
│  Block 1:                                │
│    Long: object count                    │
│    Long: byte size (after codec)         │
│    Encoded objects (concatenated)        │
│    16-byte sync marker                   │
├──────────────────────────────────────────┤
│  Block 2: ...                            │
├──────────────────────────────────────────┤
│  ...                                     │
└──────────────────────────────────────────┘
```

---

### 04.2.3 — Apache Thrift Protocols

| Protocol | Field Identification | Integer Encoding | String Encoding | Size |
|----------|---------------------|-----------------|-----------------|------|
| **TBinaryProtocol** | Type byte (1) + Field ID (2 bytes, big-endian) | Fixed-width (4 or 8 bytes) | Length (4 bytes) + UTF-8 | Largest |
| **TCompactProtocol** | Delta field ID (4 bits) + Type (4 bits) | ZigZag varint | Varint length + UTF-8 | Smallest |
| **TJSONProtocol** | Full JSON with type annotations | JSON number | JSON string | Largest (text) |

**TCompactProtocol field encoding:**
- If delta (current ID - previous ID) ≤ 15: single byte = `(delta << 4) | type`
- Otherwise: type byte (0x00 | type) + ZigZag varint field ID
- Message ends with STOP byte (0x00)

---

### 04.2.4 — BSON (Binary JSON)

| Property | Value |
|----------|-------|
| Specification | bsonspec.org |
| Governance | MongoDB Inc. — Tier 6 |
| Self-describing | Yes (type codes per element) |
| Byte order | Little-endian |
| Document max size | 16 MB (MongoDB limit) |

**Type Codes:**

| Code | Type | Size |
|------|------|------|
| 0x01 | Double | 8 bytes |
| 0x02 | String | 4-byte length + UTF-8 + null |
| 0x03 | Embedded document | Recursive |
| 0x04 | Array | Recursive (keys are "0", "1", ...) |
| 0x05 | Binary data | 4-byte length + subtype + bytes |
| 0x07 | ObjectId | 12 bytes |
| 0x08 | Boolean | 1 byte |
| 0x09 | UTC datetime | 8 bytes (int64 millis since epoch) |
| 0x0A | Null | 0 bytes |
| 0x10 | Int32 | 4 bytes |
| 0x12 | Int64 | 8 bytes |
| 0x13 | Decimal128 | 16 bytes (IEEE 754-2008) |

**Document Layout:**
```
┌───────────────────────────────────────────────────────────┐
│  int32 total_size (including this 4-byte field)          │
├───────────────────────────────────────────────────────────┤
│  Element 1: type_byte + cstring_name + value_bytes       │
│  Element 2: type_byte + cstring_name + value_bytes       │
│  ...                                                      │
│  0x00 (terminator byte)                                  │
└───────────────────────────────────────────────────────────┘
```

**Note:** BSON is often **larger** than JSON because:
- 4-byte length prefix per document/string (vs no overhead in JSON)
- Type byte per element (vs implicit typing in JSON)
- C-string null terminators for field names

---

### 04.2.5 — CBOR (Concise Binary Object Representation)

| Property | Value |
|----------|-------|
| Specification | RFC 8949 (2020), supersedes RFC 7049 |
| Governance | IETF — Tier 2 |
| Self-describing | Yes (type info per item) |
| Schema | Optional — CDDL (RFC 8610) for validation |
| Diagnostic notation | Human-readable representation for debugging |

**Initial Byte Structure:**
```
┌───────────────────────────┐
│  Major type (3 bits)      │  Determines value category
│  Additional info (5 bits) │  Determines length/value encoding
└───────────────────────────┘
```

**Major Types:**

| Major Type | Code | Meaning | Additional Info |
|-----------|------|---------|-----------------|
| 0 | 000 | Unsigned integer | Direct value (0-23), or length of following bytes |
| 1 | 001 | Negative integer | -1 minus the encoded value |
| 2 | 010 | Byte string | Length, then raw bytes |
| 3 | 011 | Text string (UTF-8) | Length, then UTF-8 bytes |
| 4 | 100 | Array | Item count, then items |
| 5 | 101 | Map | Pair count, then key-value pairs |
| 6 | 110 | Semantic tag | Tag number + enclosed item |
| 7 | 111 | Float / simple values | false(20), true(21), null(22), float16/32/64 |

**Additional Info Encoding:**

| Value | Meaning |
|-------|---------|
| 0-23 | Direct value (no additional bytes) |
| 24 | Following 1 byte is the value |
| 25 | Following 2 bytes (16-bit) |
| 26 | Following 4 bytes (32-bit) |
| 27 | Following 8 bytes (64-bit) |
| 31 | Indefinite length (break stop code) |

**Semantic Tags (Selected):**

| Tag | Semantics |
|-----|-----------|
| 0 | ISO 8601 date-time string |
| 1 | Epoch-based date-time (seconds since 1970-01-01) |
| 2 | Positive bignum (byte string) |
| 3 | Negative bignum (byte string) |
| 32 | URI |
| 33 | base64url-encoded string |
| 34 | base64-encoded string |
| 55799 | Self-describing CBOR (magic tag) |

**CBOR vs MessagePack:**
- CBOR: IETF standard (RFC 8949), semantic tags, indefinite-length, deterministic encoding rules (RFC 8742)
- MessagePack: De-facto standard, simpler, no semantic tags, more language bindings

---

### 04.2.6 — MessagePack

| Property | Value |
|----------|-------|
| Specification | msgpack.org/index.html (community) |
| Governance | Community — Tier 8 |
| Self-describing | Yes (type in format byte) |
| Schema | None native |
| Byte order | Big-endian (network byte order) |

**Format Families:**

| First Byte Range | Type | Description |
|-----------------|------|-------------|
| 0x00-0x7f | positive fixint | Value directly in byte (0-127) |
| 0x80-0x8f | fixmap | Size in lower 4 bits (0-15 pairs) |
| 0x90-0x9f | fixarray | Size in lower 4 bits (0-15 items) |
| 0xa0-0xbf | fixstr | Size in lower 5 bits (0-31 bytes) |
| 0xc0 | nil | |
| 0xc2 | false | |
| 0xc3 | true | |
| 0xc4-0xc6 | bin 8/16/32 | Binary data with length prefix |
| 0xc7-0xc9 | ext 8/16/32 | Extension type (application-defined) |
| 0xca | float 32 | IEEE 754 single |
| 0xcb | float 64 | IEEE 754 double |
| 0xcc-0xcf | uint 8/16/32/64 | Unsigned integers |
| 0xd0-0xd3 | int 8/16/32/64 | Signed integers (two's complement) |
| 0xd9-0xdb | str 8/16/32 | String with length prefix |
| 0xdc-0xdd | array 16/32 | Array with length prefix |
| 0xde-0xdf | map 16/32 | Map with length prefix |
| 0xe0-0xff | negative fixint | Value: byte - 256 (-32 to -1) |

---

### 04.2.7 — SMILE (Jackson Binary JSON)

| Property | Value |
|----------|-------|
| Author | FasterXML (Jackson project) |
| Governance | Tier 8 (Community, single vendor) |
| Self-describing | Yes |
| Key feature | Back-references for repeated field names (shared string lookup table) |
| Header | 3-byte smiley `:)\n` (0x3A 0x29 0x0A) + version byte |

SMILE reduces payload size by 30-50% vs JSON for typical API responses by referencing previously seen field names via a lookup table index rather than repeating them.

---

## 04.3 — Zero-Copy / In-Place Formats (Summary)

Full coverage in **Volume 09**. Brief comparison:

| Format | Access Pattern | Schema | Primary Insight |
|--------|---------------|--------|----------------|
| **FlatBuffers** | vtable + offset indirection | .fbs | `table` = extensible (vtable), `struct` = inline (fixed) |
| **Cap'n Proto** | Pointer-based arena | .capnp | In-memory layout = wire layout; capability-based RPC |
| **Apache Arrow** | Columnar buffers | Schema message | Columnar IPC for analytics; SIMD-friendly |
| **SBE** | Fixed template offsets | XML template | Fixed-size messages for ultra-low latency (finance) |

**Key Principle:** These formats eliminate the parse-copy step. The serialized byte buffer IS the data structure — you read fields directly from the buffer via computed offsets.

---

## 04.4 — Serialization Format Comparison Matrix

| Format | Text/Binary | Schema Required | Self-Describing | Zero-Copy | Streaming | Approx Size vs JSON | Primary Domain | Governance Tier |
|--------|------------|-----------------|-----------------|-----------|-----------|--------------------|--------------|----|
| **JSON** | Text | No | Yes | No | NDJSON | 1.0× (baseline) | REST, Web, Config | 2/5 |
| **XML** | Text | No (optional XSD) | Partially | No | SAX/StAX | 2-4× | Enterprise, SOAP | 2 |
| **YAML** | Text | No | Yes | No | Partial (multi-doc) | ~1.1× | Config, CI/CD | 8 |
| **TOML** | Text | No | Yes | No | No | ~1.0× | Config files | 8 |
| **CSV** | Text | No | No | No | Yes (line-based) | 0.5-0.7× | Analytics, ETL | 2 |
| **Protobuf** | Binary | Yes (.proto) | No | No | Partial (LEN-prefixed) | 0.3-0.7× | gRPC, Cloud, Mobile | 6 |
| **Avro Binary** | Binary | Yes (Avro schema) | No | No | Container file | 0.3-0.6× | Kafka, Hadoop, Spark | 5 |
| **Thrift Binary** | Binary | Yes (.thrift) | No | No | No | 0.4-0.7× | Service RPC | 5 |
| **BSON** | Binary | No | Yes | No | No | 1.0-1.5× | MongoDB | 6 |
| **CBOR** | Binary | No (CDDL opt) | Yes | No | Yes (indefinite) | 0.5-0.8× | IoT, CoAP, FIDO2 | 2 |
| **MessagePack** | Binary | No | Yes | No | No | 0.4-0.7× | Redis, Games | 8 |
| **SMILE** | Binary | No | Yes | No | No | 0.5-0.7× | Jackson Java services | 8 |
| **FlatBuffers** | Binary | Yes (.fbs) | No | **Yes** | No | 1.0-2.0× (padding) | Games, Embedded | 6 |
| **Cap'n Proto** | Binary | Yes (.capnp) | No | **Yes** | No | 0.5-1.0× | Low-latency RPC | 8 |
| **SBE** | Binary | Yes (XML template) | No | **Yes** | Yes | Minimal | Financial/HFT | 8 |
| **Arrow IPC** | Binary (columnar) | Yes (schema msg) | No | **Yes** | Batch-based | Varies | Analytics, OLAP | 5 |

---

## 04.5 — Encoding Pattern Taxonomy

### Pattern 1: Tag-Length-Value (TLV)

```
┌─────────┬─────────┬──────────────────┐
│   Tag   │ Length  │      Value       │
│(type+ID)│(byte ct)│   (raw bytes)    │
└─────────┴─────────┴──────────────────┘
```
**Examples:** ASN.1 BER/DER, Protobuf wire format (tag = field_number + wire_type), SNMP
**Advantages:** Self-delimiting, field-independent parsing, skip unknown fields
**Disadvantages:** Tag overhead per field

### Pattern 2: Schema-Ordered (No Tags)

```
┌───────────┬───────────┬───────────┬─────┐
│  Field 1  │  Field 2  │  Field 3  │ ... │
│  (value)  │  (value)  │  (value)  │     │
└───────────┴───────────┴───────────┴─────┘
   ↑ Reader must know field order from schema
```
**Examples:** Avro binary, SBE (fixed-size)
**Advantages:** Minimal overhead (no tags, no field names)
**Disadvantages:** Schema must be identical on both sides; no field skipping without schema

### Pattern 3: Type-Value (Self-Describing Binary)

```
┌──────────┬──────────────────────────────┐
│Type byte │        Value bytes           │
│(data type│   (encoding per type)        │
│ + length)│                              │
└──────────┴──────────────────────────────┘
```
**Examples:** CBOR (major type in initial byte), MessagePack (format byte), BSON (type code)
**Advantages:** No external schema needed, self-delimiting
**Disadvantages:** Type byte overhead per value; field names still needed for maps

### Pattern 4: Fixed-Layout with vtable

```
┌────────────────────┐
│  vtable            │  (offsets to each field, shared across same-type objects)
├────────────────────┤
│  Object data       │  (inline scalars at known offsets, pointers to variable-size)
│  [field1][field2]  │
│  [ptr→string]      │
└────────────────────┘
```
**Examples:** FlatBuffers
**Advantages:** Zero-copy access via offset calculation; vtable sharing
**Disadvantages:** Padding waste for alignment; complex builder logic

### Pattern 5: Arena-Allocated Pointer Structures

```
┌─────────────────────────────────────────────┐
│  Segment 0 (root)                           │
│  [struct data] [ptr to child] [ptr to list] │
├─────────────────────────────────────────────┤
│  Segment 1 (overflow)                       │
│  [child data] [list data]                   │
└─────────────────────────────────────────────┘
```
**Examples:** Cap'n Proto
**Advantages:** In-memory = wire format (true zero-copy); pointer-based random access
**Disadvantages:** Pointer overhead; alignment padding; complex allocation

### Pattern 6: Columnar Layout

```
Column 0 (int32):   [1] [2] [3] [4] [5] ...
Column 1 (string):  [offsets buffer] [data buffer]
Column 2 (float64): [1.1] [2.2] [3.3] [4.4] [5.5] ...
Validity bitmap:    [1] [1] [0] [1] [1] ...  (null tracking)
```
**Examples:** Apache Arrow, Apache Parquet (on-disk columnar)
**Advantages:** SIMD-friendly vectorized operations; cache-efficient for column scans
**Disadvantages:** Row-level access is expensive; not suited for single-record messages

### Pattern 7: Delimited Text

```
field1,field2,field3\n
value1,value2,value3\n
```
**Examples:** CSV, TSV, NDJSON (one JSON object per `\n`)
**Advantages:** Streamable, human-readable, universal tooling
**Disadvantages:** Escaping/quoting complexity; no type information (CSV)

### Pattern 8: Markup with Attributes

```xml
<element attribute="value">
  <child>text content</child>
</element>
```
**Examples:** XML, HTML, SGML
**Advantages:** Rich structure (elements + attributes), namespaces, extensibility
**Disadvantages:** Verbose, complex parsing, large payload size

---

## 04.6 — Streaming and Framing

### Framing Mechanisms

| Mechanism | Description | Examples |
|-----------|-------------|----------|
| **Length-prefixed** | N-byte big-endian length before payload | gRPC (5-byte header: compress flag + 4-byte length), Kafka (4-byte length), Thrift TFramedTransport |
| **Delimiter-based** | Special byte/character separates messages | NDJSON (`\n`), CSV (`\n`), HTTP/1.1 (`\r\n\r\n`) |
| **Self-framing** | Each item encodes its own length | CBOR items, MessagePack items, Avro blocks |
| **Container format** | Header + blocks + sync markers | Avro Object Container File, Parquet footer |
| **Chunked transfer** | Framework handles framing | HTTP chunked encoding, HTTP/2 DATA frames |
| **End signaling** | Explicit end-of-stream marker | CBOR break code (0xFF), Avro 0-count block, TCP FIN |

### gRPC Length-Prefixed Message Framing

```
┌─────────────────────────────────────────┐
│ Compressed flag (1 byte): 0x00 or 0x01 │
│ Message length (4 bytes, big-endian)    │
│ Message body (Protobuf-encoded bytes)   │
└─────────────────────────────────────────┘
```

Multiple messages can be sent on a single HTTP/2 stream (for streaming RPCs).

### Kafka Record Batch Layout

```
┌──────────────────────────────────────────┐
│ Base offset (int64)                      │
│ Batch length (int32)                     │
│ Partition leader epoch (int32)           │
│ Magic byte (int8) = 2                    │
│ CRC32 (int32) — covers remaining bytes  │
│ Attributes (int16) — compression, etc.  │
│ Last offset delta (int32)               │
│ First timestamp (int64)                 │
│ Max timestamp (int64)                   │
│ Producer ID (int64)                     │
│ Producer epoch (int16)                  │
│ Base sequence (int32)                   │
│ Records count (int32)                   │
│ [Record 1][Record 2]...[Record N]       │
└──────────────────────────────────────────┘
```

---

## 04.7 — Security Considerations

### Text Format Injection Attacks

| Attack | Format | Description | Mitigation |
|--------|--------|-------------|-----------|
| **JSON injection** | JSON | Unescaped user input breaks JSON structure | Always use proper JSON serializer; never string-concatenate |
| **XML External Entity (XXE)** | XML | `<!DOCTYPE>` with SYSTEM entity reads local files / SSRF | Disable DTD processing, disable external entities |
| **XML Bomb (Billion Laughs)** | XML | Recursive entity expansion causes OOM | Limit entity expansion depth; reject DOCTYPE |
| **YAML deserialization RCE** | YAML | `!!python/object` tag executes arbitrary code | Use safe loader (`yaml.safe_load`); reject unknown tags |
| **CSV Formula Injection** | CSV | `=cmd()` in cells executed by Excel/Sheets | Prefix with `'` or validate/sanitize cell content |
| **XPath/XQuery injection** | XML | Dynamic XPath with user input | Parameterized queries; input validation |

### Binary Format Security

| Concern | Description | Mitigation |
|---------|-------------|-----------|
| **Buffer overflow** | Malformed length field causes OOB read/write | Validate lengths against remaining buffer; use safe parsers |
| **Integer overflow** | Varint decoding overflow | Cap varint decode at 10 bytes (Protobuf spec); check bounds |
| **Denial of service** | Deeply nested messages consume stack | Limit recursion depth (Protobuf default: 100) |
| **Schema confusion** | Wrong schema applied to binary data | Validate schema ID/version; use registry + subject binding |
| **Zip bomb equivalent** | Compressed payload decompresses to huge size | Limit decompressed size; streaming decompression with bounds |

### Best Practice

**Always validate at the trust boundary:** Parse the serialized data using the declared schema immediately upon receipt, BEFORE passing to application logic. Never trust wire data.

---

## 04.8 — Cross-References

| Topic | Volume |
|-------|--------|
| Detailed binary encoding rules (ASN.1 BER/DER/PER, CBOR specifics) | Volume 05 — Binary Encoding Rules |
| Zero-copy formats in depth (FlatBuffers, Cap'n Proto, Arrow, SBE) | Volume 09 — Zero-Copy Systems |
| Schema systems that constrain serialized data | Volume 03 — Schema Systems |
| IDLs that drive serialization format selection | Volume 02 — IDLs |
| RPC frameworks using these serialization formats | Volume 06 — RPC Frameworks |
| Transport layers carrying serialized bytes | Volume 14 — Transport & Runtime Binding |
| Schema evolution across format versions | Volume 10 — Schema Evolution |

---

*End of Volume 04 — Serialization Formats*
