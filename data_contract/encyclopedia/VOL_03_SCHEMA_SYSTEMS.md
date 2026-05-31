# VOLUME 03 — SCHEMA SYSTEMS

## Universal Data Contract, Serialization, IDL & Interoperability Architecture Encyclopedia

---

## 03.0 — Volume Overview

A schema system provides machine-enforceable structural validation — the ability to verify that a piece of data conforms to a declared shape, type, and constraint set before it is processed. This volume covers all major schema technologies, from JSON Schema and XML Schema Definition (XSD) through runtime validators (Pydantic, Zod) to centralized schema registries (Confluent, Apicurio). Each technology is positioned within the universal stack and classified by governance tier.

**Stack Position:**
```
    IDL Layer (Volume 02)
           ↓
    ┌───────────────────────┐
    │     SCHEMA SYSTEMS    │  ← THIS VOLUME
    │  (validation + types) │
    └───────────────────────┘
           ↓
    Serialization (Volume 04)
```

**Critical Distinction:** An IDL defines services AND types (code generation is primary). A schema defines structure AND constraints (validation is primary). Some technologies serve both roles (Protobuf, Avro).

---

## 03.1 — What is a Schema System (Formal Definition)

**Definition:** A schema system is a formal language or framework for declaring the expected structure, types, constraints, and relationships of data instances, coupled with a mechanism (validator, parser, or runtime checker) to verify that specific data conforms to the declaration.

### Schema vs IDL vs Data Model

| Dimension | Schema | IDL | Data Model |
|-----------|--------|-----|-----------|
| Primary role | Validate data instances | Generate code for types + services | Describe domain concepts |
| Enforcement | Machine-enforced at runtime or build time | Compiler-enforced at build time | Design-time guidance |
| Service definitions | No (schema defines data only) | Yes (services + types) | No |
| Output | Pass/fail validation result | Generated source code | Diagrams, specifications |
| Examples | JSON Schema, XSD, Avro schema | Protobuf .proto, AIDL, FIDL | ER diagram, UML |

### Schema Location Patterns

| Pattern | Description | Example |
|---------|------------|---------|
| **Inline** | Schema embedded in the data document | JSON-LD `@context`, XML namespace |
| **External** | Schema in separate file, referenced by URI | `$schema` in JSON Schema, `xsi:schemaLocation` |
| **Registry** | Schema stored in a central service, referenced by ID/subject | Confluent Schema Registry, Apicurio |
| **Compiled-in** | Schema compiled into producer/consumer code at build time | Protobuf (no runtime schema needed) |

---

## 03.2 — JSON Schema (2020-12)

### Overview

**Definition:** JSON Schema is a declarative language for describing the structure of JSON documents, providing vocabulary for validation, annotation, and hyper-schema (linking). The current specification is draft 2020-12.

**Governance:** Community specification with IETF Internet-Draft status (draft-bhutton-json-schema). Tier 2/8.

### Complete Annotated Example

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://automotive.example.com/schemas/vehicle.schema.json",
  "title": "Vehicle",
  "description": "A motor vehicle in the fleet management system.",
  "type": "object",
  
  "required": ["vin", "make", "model", "year", "fuelType"],
  
  "properties": {
    "vin": {
      "type": "string",
      "title": "Vehicle Identification Number",
      "description": "ISO 3779 17-character VIN",
      "pattern": "^[A-HJ-NPR-Z0-9]{17}$",
      "minLength": 17,
      "maxLength": 17
    },
    "make": {
      "type": "string",
      "minLength": 1,
      "maxLength": 100,
      "examples": ["Toyota", "BMW", "Tesla"]
    },
    "model": {
      "type": "string",
      "minLength": 1,
      "maxLength": 100
    },
    "year": {
      "type": "integer",
      "minimum": 1886,
      "maximum": 2100,
      "description": "Manufacturing year (1886 = first automobile)"
    },
    "fuelType": {
      "$ref": "#/$defs/FuelType"
    },
    "speed": {
      "type": "number",
      "minimum": 0,
      "maximum": 500,
      "description": "Current speed in km/h"
    },
    "location": {
      "$ref": "#/$defs/GeoLocation"
    },
    "activeDTCs": {
      "type": "array",
      "items": { "$ref": "#/$defs/DiagnosticCode" },
      "uniqueItems": true
    },
    "metadata": {
      "type": "object",
      "additionalProperties": { "type": "string" },
      "description": "Arbitrary key-value metadata"
    },
    "lastUpdated": {
      "type": "string",
      "format": "date-time",
      "description": "ISO 8601 timestamp of last state update"
    }
  },
  
  "additionalProperties": false,
  
  "$defs": {
    "FuelType": {
      "type": "string",
      "enum": ["PETROL", "DIESEL", "ELECTRIC", "HYBRID", "HYDROGEN"],
      "description": "Fuel/energy source classification"
    },
    "GeoLocation": {
      "type": "object",
      "required": ["latitude", "longitude"],
      "properties": {
        "latitude": {
          "type": "number",
          "minimum": -90,
          "maximum": 90
        },
        "longitude": {
          "type": "number",
          "minimum": -180,
          "maximum": 180
        },
        "altitude": {
          "type": "number",
          "description": "Meters above sea level"
        },
        "accuracy": {
          "type": "number",
          "minimum": 0,
          "description": "Horizontal accuracy in meters"
        }
      },
      "additionalProperties": false
    },
    "DiagnosticCode": {
      "type": "object",
      "required": ["code", "severity"],
      "properties": {
        "code": {
          "type": "string",
          "pattern": "^[A-Z][0-9]{4}$",
          "description": "OBD-II code (e.g., P0301)"
        },
        "description": {
          "type": "string",
          "maxLength": 500
        },
        "severity": {
          "type": "string",
          "enum": ["INFO", "WARNING", "CRITICAL"]
        },
        "detectedAt": {
          "type": "string",
          "format": "date-time"
        }
      },
      "additionalProperties": false
    }
  },
  
  "if": {
    "properties": { "fuelType": { "const": "ELECTRIC" } }
  },
  "then": {
    "required": ["batteryCapacityKwh"],
    "properties": {
      "batteryCapacityKwh": {
        "type": "number",
        "minimum": 0,
        "maximum": 500
      }
    }
  }
}
```

### JSON Schema Keywords Reference

| Category | Keywords | Purpose |
|----------|----------|---------|
| **Type** | `type`, `enum`, `const` | Declare allowed JSON type or values |
| **Numeric** | `minimum`, `maximum`, `exclusiveMinimum`, `exclusiveMaximum`, `multipleOf` | Numeric constraints |
| **String** | `minLength`, `maxLength`, `pattern`, `format` | String constraints |
| **Array** | `items`, `prefixItems`, `minItems`, `maxItems`, `uniqueItems`, `contains`, `minContains`, `maxContains` | Array validation |
| **Object** | `properties`, `patternProperties`, `additionalProperties`, `required`, `propertyNames`, `minProperties`, `maxProperties`, `dependentRequired`, `dependentSchemas` | Object structure |
| **Composition** | `allOf`, `anyOf`, `oneOf`, `not` | Schema combination |
| **Conditional** | `if`, `then`, `else` | Conditional validation |
| **Reference** | `$ref`, `$defs`, `$anchor`, `$dynamicRef`, `$dynamicAnchor` | Schema reuse |
| **Annotation** | `title`, `description`, `default`, `examples`, `readOnly`, `writeOnly`, `deprecated` | Documentation |
| **Unevaluated** | `unevaluatedProperties`, `unevaluatedItems` | Stricter composition control |

### Format Values (Defined)

| Format | Validates | RFC |
|--------|----------|-----|
| `date-time` | ISO 8601 date-time | RFC 3339 |
| `date` | Full date (YYYY-MM-DD) | RFC 3339 |
| `time` | Time with timezone | RFC 3339 |
| `duration` | ISO 8601 duration | RFC 3339 |
| `email` | Email address | RFC 5321 |
| `uri` | URI | RFC 3986 |
| `uri-reference` | URI or relative reference | RFC 3986 |
| `uuid` | UUID | RFC 4122 |
| `ipv4` | IPv4 address | RFC 2673 |
| `ipv6` | IPv6 address | RFC 4291 |
| `hostname` | Internet hostname | RFC 1123 |
| `json-pointer` | JSON Pointer | RFC 6901 |
| `regex` | Regular expression | ECMA-262 |

### JSON Schema Versions History

| Draft | Year | Key Changes |
|-------|------|-------------|
| Draft-04 | 2013 | First widely adopted; id, $ref, definitions |
| Draft-06 | 2017 | $id replaces id; const, contains, propertyNames |
| Draft-07 | 2018 | if/then/else; readOnly/writeOnly; $comment |
| 2019-09 | 2019 | $defs replaces definitions; unevaluatedProperties; $anchor |
| **2020-12** | 2020 | prefixItems (tuple); $dynamicRef; vocabulary system; current |

---

## 03.3 — XML Schema Definition (XSD 1.1)

### Overview

**Definition:** XSD (XML Schema Definition) is a W3C Recommendation for describing the structure and constraining the content of XML documents, providing a rich type system with inheritance, derivation, and assertion-based constraints.

**Governance:** W3C — Tier 2. XSD 1.0 (2001), XSD 1.1 (2012).

### Annotated Example

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
           xmlns:tns="http://automotive.example.com/vehicle"
           targetNamespace="http://automotive.example.com/vehicle"
           elementFormDefault="qualified">

    <!-- Simple type: enumeration -->
    <xs:simpleType name="FuelType">
        <xs:restriction base="xs:string">
            <xs:enumeration value="PETROL"/>
            <xs:enumeration value="DIESEL"/>
            <xs:enumeration value="ELECTRIC"/>
            <xs:enumeration value="HYBRID"/>
            <xs:enumeration value="HYDROGEN"/>
        </xs:restriction>
    </xs:simpleType>

    <!-- Simple type: pattern-constrained string -->
    <xs:simpleType name="VIN">
        <xs:restriction base="xs:string">
            <xs:pattern value="[A-HJ-NPR-Z0-9]{17}"/>
            <xs:length value="17"/>
        </xs:restriction>
    </xs:simpleType>

    <!-- Simple type: ranged integer -->
    <xs:simpleType name="ManufacturingYear">
        <xs:restriction base="xs:int">
            <xs:minInclusive value="1886"/>
            <xs:maxInclusive value="2100"/>
        </xs:restriction>
    </xs:simpleType>

    <!-- Complex type: GeoLocation -->
    <xs:complexType name="GeoLocation">
        <xs:sequence>
            <xs:element name="latitude" type="xs:double"/>
            <xs:element name="longitude" type="xs:double"/>
            <xs:element name="altitude" type="xs:double" minOccurs="0"/>
            <xs:element name="accuracy" type="xs:float" minOccurs="0"/>
        </xs:sequence>
    </xs:complexType>

    <!-- Complex type with extension (inheritance) -->
    <xs:complexType name="VehicleBase">
        <xs:sequence>
            <xs:element name="vin" type="tns:VIN"/>
            <xs:element name="make" type="xs:string"/>
            <xs:element name="model" type="xs:string"/>
            <xs:element name="year" type="tns:ManufacturingYear"/>
            <xs:element name="fuelType" type="tns:FuelType"/>
        </xs:sequence>
    </xs:complexType>

    <xs:complexType name="Vehicle">
        <xs:complexContent>
            <xs:extension base="tns:VehicleBase">
                <xs:sequence>
                    <xs:element name="speed" type="xs:float" minOccurs="0"/>
                    <xs:element name="location" type="tns:GeoLocation" minOccurs="0"/>
                    <xs:element name="activeDTCs" minOccurs="0">
                        <xs:complexType>
                            <xs:sequence>
                                <xs:element name="dtc" type="tns:DiagnosticCode" 
                                            maxOccurs="unbounded"/>
                            </xs:sequence>
                        </xs:complexType>
                    </xs:element>
                    <xs:element name="lastUpdated" type="xs:dateTime" minOccurs="0"/>
                </xs:sequence>
            </xs:extension>
        </xs:complexContent>
        <!-- XSD 1.1 assertion -->
        <xs:assert test="if (@fuelType = 'ELECTRIC') then exists(batteryCapacityKwh) else true()"/>
    </xs:complexType>

    <!-- DiagnosticCode -->
    <xs:complexType name="DiagnosticCode">
        <xs:sequence>
            <xs:element name="code">
                <xs:simpleType>
                    <xs:restriction base="xs:string">
                        <xs:pattern value="[A-Z][0-9]{4}"/>
                    </xs:restriction>
                </xs:simpleType>
            </xs:element>
            <xs:element name="description" type="xs:string"/>
            <xs:element name="severity">
                <xs:simpleType>
                    <xs:restriction base="xs:string">
                        <xs:enumeration value="INFO"/>
                        <xs:enumeration value="WARNING"/>
                        <xs:enumeration value="CRITICAL"/>
                    </xs:restriction>
                </xs:simpleType>
            </xs:element>
        </xs:sequence>
    </xs:complexType>

    <!-- Root element -->
    <xs:element name="vehicle" type="tns:Vehicle"/>
    
</xs:schema>
```

### XSD Type System

| Category | Types |
|----------|-------|
| **Primitive** | string, boolean, decimal, float, double, duration, dateTime, time, date, hexBinary, base64Binary, anyURI, QName, NOTATION |
| **Derived numeric** | integer, long, int, short, byte, unsignedLong, unsignedInt, unsignedShort, unsignedByte, positiveInteger, negativeInteger, nonNegativeInteger, nonPositiveInteger |
| **Derived string** | normalizedString, token, language, Name, NCName, NMTOKEN, ID, IDREF |
| **Facets** | length, minLength, maxLength, pattern, enumeration, whiteSpace, maxInclusive, maxExclusive, minInclusive, minExclusive, totalDigits, fractionDigits |

### XSD 1.0 vs 1.1

| Feature | XSD 1.0 | XSD 1.1 |
|---------|---------|---------|
| Assertions | No | Yes (`xs:assert` with XPath 2.0) |
| Conditional type assignment | No | Yes (`xs:alternative`) |
| Open content | `xs:any` only | `xs:openContent` (flexible extensibility) |
| Type restriction | Strict subset only | More flexible derivation |
| Co-occurrence constraints | Not possible | Via assertions |

---

## 03.4 — Apache Avro Schema

### Overview

**Definition:** Avro schema is a JSON-based schema language used by Apache Avro for both serialization and schema evolution. The schema is always present with the data (either inline or via Schema Registry), enabling reader/writer schema resolution without code generation.

**Governance:** Apache Software Foundation — Tier 5. Apache 2.0 license.

### Schema Example

```json
{
  "namespace": "com.automotive.vehicle",
  "type": "record",
  "name": "Vehicle",
  "doc": "Represents a vehicle in the fleet management system.",
  "fields": [
    {
      "name": "id",
      "type": "string",
      "doc": "Unique vehicle identifier (UUID)"
    },
    {
      "name": "vin",
      "type": {
        "type": "string",
        "logicalType": "vin"
      },
      "doc": "ISO 3779 VIN"
    },
    {
      "name": "make",
      "type": "string"
    },
    {
      "name": "model",
      "type": "string"
    },
    {
      "name": "year",
      "type": "int"
    },
    {
      "name": "fuelType",
      "type": {
        "type": "enum",
        "name": "FuelType",
        "symbols": ["PETROL", "DIESEL", "ELECTRIC", "HYBRID", "HYDROGEN"],
        "default": "PETROL"
      }
    },
    {
      "name": "speed",
      "type": ["null", "float"],
      "default": null,
      "doc": "Current speed in km/h; null if unavailable"
    },
    {
      "name": "location",
      "type": ["null", {
        "type": "record",
        "name": "GeoLocation",
        "fields": [
          {"name": "latitude", "type": "double"},
          {"name": "longitude", "type": "double"},
          {"name": "altitude", "type": ["null", "float"], "default": null}
        ]
      }],
      "default": null
    },
    {
      "name": "activeDtcs",
      "type": {
        "type": "array",
        "items": {
          "type": "record",
          "name": "DiagnosticCode",
          "fields": [
            {"name": "code", "type": "string"},
            {"name": "description", "type": "string"},
            {"name": "severity", "type": {"type": "enum", "name": "Severity", "symbols": ["INFO", "WARNING", "CRITICAL"]}}
          ]
        }
      },
      "default": []
    },
    {
      "name": "metadata",
      "type": {"type": "map", "values": "string"},
      "default": {}
    },
    {
      "name": "lastUpdatedMs",
      "type": {
        "type": "long",
        "logicalType": "timestamp-millis"
      }
    }
  ]
}
```

### Avro Type System

| Category | Types |
|----------|-------|
| **Primitive** | null, boolean, int, long, float, double, bytes, string |
| **Complex** | record, enum, array, map, union, fixed |
| **Logical types** | date, time-millis, time-micros, timestamp-millis, timestamp-micros, duration, decimal, uuid |

### Avro Schema Evolution Rules

| Change | Backward Compat | Forward Compat | Full Compat |
|--------|----------------|----------------|-------------|
| Add field with default | ✅ | ✅ | ✅ |
| Add field without default | ❌ | ✅ | ❌ |
| Remove field with default | ✅ | ❌ | ❌ |
| Remove field without default | ✅ | ❌ | ❌ |
| Rename field | ❌ (use aliases) | ❌ | ❌ |
| Change type (int→long promotion) | ✅ | ❌ | ❌ |
| Add enum symbol (at end) | ❌ | ✅ | ❌ |
| Add enum symbol (with default) | ✅ | ✅ | ✅ |

### Reader/Writer Schema Resolution

Avro's unique approach: data is ALWAYS written with the **writer's schema** and read using the **reader's schema**. The Avro runtime resolves differences at deserialization time:

```
Writer Schema (used to write)     Reader Schema (used to read)
         │                                    │
         ▼                                    ▼
    ┌─────────────────────────────────────────────┐
    │         SCHEMA RESOLUTION ENGINE            │
    │  - Match fields by NAME (not position)      │
    │  - Apply defaults for missing fields        │
    │  - Skip unknown fields                      │
    │  - Promote compatible types (int→long)      │
    │  - Error on incompatible changes            │
    └─────────────────────────────────────────────┘
                          │
                          ▼
                  Resolved data object
```

---

## 03.5 — Protobuf as Schema Layer

### Overview

While Protocol Buffers is primarily an IDL (Volume 02), its message definitions also function as a schema system — defining the expected structure that serialized bytes must conform to.

### Schema Properties of Protobuf

| Property | Behavior |
|----------|---------|
| **Validation** | Generated code validates types at compile time; malformed wire data causes parse errors at runtime |
| **Unknown fields** | Preserved (proto3); enables forward compatibility |
| **Required fields** | Removed in proto3 (schema cannot enforce mandatory fields at wire level) |
| **Default values** | Zero-value for absent fields (0, "", false, empty list) |
| **Field presence** | Only with `optional` keyword (proto3.15+) or wrapper types |
| **Constraint validation** | Not built-in; use protoc-gen-validate (PGV) / protovalidate for constraints |

### protovalidate (buf.build/bufbuild/protovalidate)

```protobuf
syntax = "proto3";
import "buf/validate/validate.proto";

message Vehicle {
  string vin = 1 [(buf.validate.field).string = {
    pattern: "^[A-HJ-NPR-Z0-9]{17}$",
    min_len: 17,
    max_len: 17
  }];
  
  int32 year = 2 [(buf.validate.field).int32 = {
    gte: 1886,
    lte: 2100
  }];
  
  float speed_kmh = 3 [(buf.validate.field).float = {
    gte: 0,
    lte: 500
  }];
  
  string email = 4 [(buf.validate.field).string.email = true];
}
```

---

## 03.6 — GraphQL Schema (Type System as Schema)

### Overview

GraphQL's type system serves as a schema that validates all incoming queries and mutations at runtime. The server MUST reject any query that doesn't conform to the schema.

### Schema Validation Rules

| Rule | Effect |
|------|--------|
| Every field must be defined in the type | Unknown field → error |
| Non-null fields (`!`) must return a value | Null on non-null → error |
| Arguments must match declared types | Type mismatch → error |
| Fragments must target valid types | Invalid fragment → error |
| Input objects validate against input types | Extra/missing required fields → error |

### Introspection as Schema Discovery

```graphql
# Query the schema itself
{
  __schema {
    types {
      name
      kind
      fields {
        name
        type { name kind ofType { name } }
      }
    }
  }
}
```

---

## 03.7 — OpenAPI Schema Object

### Overview

**Definition:** The OpenAPI Schema Object (a superset of JSON Schema) defines the data types used in API request bodies, response payloads, path parameters, query parameters, and headers.

### OpenAPI 3.0 vs 3.1 Schema Differences

| Feature | OpenAPI 3.0 | OpenAPI 3.1 |
|---------|------------|------------|
| JSON Schema version | Modified subset of Draft-04 | **Full JSON Schema 2020-12** |
| Nullable | `nullable: true` property | `type: ["string", "null"]` (JSON Schema union) |
| Exclusive min/max | `exclusiveMinimum: true` + `minimum` | `exclusiveMinimum: <value>` (direct) |
| Examples | `example` (singular) | `examples` (array, JSON Schema) |
| `$ref` siblings | No other properties alongside `$ref` | Properties allowed alongside `$ref` |
| `const` | Not supported | Supported |
| `if/then/else` | Not supported | Supported |
| `prefixItems` | Not supported | Supported (tuple validation) |
| `unevaluatedProperties` | Not supported | Supported |

---

## 03.8 — YANG as Schema System

YANG (covered in Volume 01 as data model) also functions as a schema system:

| Schema Function | YANG Capability |
|----------------|-----------------|
| Type validation | Built-in types + typedef with pattern/range |
| Mandatory enforcement | `mandatory true` on leaf nodes |
| Referential integrity | `leafref` (foreign key equivalent) |
| Uniqueness | `unique` statement on list entries |
| Cross-field constraints | `must` expressions (XPath 1.0) |
| Conditional presence | `when` expressions |
| Value set restriction | `type enumeration`, `type bits` |
| Range validation | `range "1..65535"` |
| Pattern validation | `pattern '[a-zA-Z]+'` |

---

## 03.9 — Runtime Schema Validators

### Pydantic (Python)

```python
from pydantic import BaseModel, Field, field_validator
from typing import Optional, Literal
from datetime import datetime

class GeoLocation(BaseModel):
    latitude: float = Field(ge=-90, le=90)
    longitude: float = Field(ge=-180, le=180)
    altitude: Optional[float] = None

class Vehicle(BaseModel):
    vin: str = Field(pattern=r'^[A-HJ-NPR-Z0-9]{17}$', min_length=17, max_length=17)
    make: str = Field(min_length=1, max_length=100)
    model: str = Field(min_length=1, max_length=100)
    year: int = Field(ge=1886, le=2100)
    fuel_type: Literal["PETROL", "DIESEL", "ELECTRIC", "HYBRID", "HYDROGEN"]
    speed_kmh: Optional[float] = Field(None, ge=0, le=500)
    location: Optional[GeoLocation] = None
    last_updated: Optional[datetime] = None

    @field_validator('vin')
    @classmethod
    def validate_vin_checksum(cls, v: str) -> str:
        # VIN check digit validation (position 9)
        # ... actual validation logic
        return v
```

### Zod (TypeScript)

```typescript
import { z } from 'zod';

const GeoLocation = z.object({
  latitude: z.number().min(-90).max(90),
  longitude: z.number().min(-180).max(180),
  altitude: z.number().optional(),
});

const FuelType = z.enum(['PETROL', 'DIESEL', 'ELECTRIC', 'HYBRID', 'HYDROGEN']);

const Vehicle = z.object({
  vin: z.string().regex(/^[A-HJ-NPR-Z0-9]{17}$/).length(17),
  make: z.string().min(1).max(100),
  model: z.string().min(1).max(100),
  year: z.number().int().min(1886).max(2100),
  fuelType: FuelType,
  speedKmh: z.number().min(0).max(500).optional(),
  location: GeoLocation.optional(),
  lastUpdated: z.string().datetime().optional(),
});

// Type inference: TypeScript type derived from schema
type Vehicle = z.infer<typeof Vehicle>;

// Validation
const result = Vehicle.safeParse(inputData);
if (result.success) {
  const vehicle: Vehicle = result.data;  // Type-safe
} else {
  console.error(result.error.format());  // Structured error
}
```

### Runtime Validator Comparison

| Framework | Language | Schema Source | Type Safety | Perf | OpenAPI Export |
|-----------|---------|-------------|-------------|------|--------------|
| **Pydantic** | Python | Python classes | Yes (mypy) | Fast (Rust core in v2) | Yes (via FastAPI) |
| **Zod** | TypeScript | TS builder API | Yes (inferred) | Good | Yes (zod-to-openapi) |
| **Joi** | JavaScript | JS builder API | No (JS) | Good | Partial |
| **Valibot** | TypeScript | TS builder API | Yes (inferred) | Very fast (tree-shakeable) | Partial |
| **Marshmallow** | Python | Python classes | Partial | Moderate | Yes (apispec) |
| **class-validator** | TypeScript | Decorators | Yes | Good | Yes (nestjs/swagger) |

---

## 03.10 — Schema Registries

### Confluent Schema Registry

**Definition:** A centralized service for storing, versioning, and enforcing compatibility of schemas used in Apache Kafka event streams.

**Architecture:**
```
Producer                          Consumer
   │                                 │
   │  1. Register schema             │
   ├──────────► Schema Registry ◄────┤  2. Fetch schema by ID
   │            │                    │
   │            │ (stores subject    │
   │            │  + version +       │
   │            │  compatibility)    │
   │            │                    │
   ▼            ▼                    ▼
   Kafka Broker (topic: my-topic-value)
   [magic byte | schema ID (4 bytes) | Avro/Protobuf/JSON payload]
```

**Key Concepts:**

| Concept | Definition |
|---------|-----------|
| **Subject** | Named scope for schema evolution (default: `<topic>-key` or `<topic>-value`) |
| **Version** | Sequential integer per subject |
| **Schema ID** | Global unique identifier across all subjects |
| **Compatibility level** | BACKWARD, FORWARD, FULL, NONE, BACKWARD_TRANSITIVE, FORWARD_TRANSITIVE, FULL_TRANSITIVE |
| **Wire format** | Magic byte (0x00) + Schema ID (4 bytes, big-endian) + serialized payload |

**Supported Schema Types:**
- Avro (original, primary)
- Protobuf (added in Confluent Platform 5.5)
- JSON Schema (added in Confluent Platform 5.5)

**License:** BSL 1.1 (since Confluent Platform 7.4, 2023). Previously Apache 2.0.

### Apicurio Schema Registry (Red Hat)

**Definition:** Open-source schema registry compatible with the Confluent REST API, supporting Avro, Protobuf, JSON Schema, OpenAPI, AsyncAPI, GraphQL, and more.

| Feature | Confluent | Apicurio |
|---------|-----------|----------|
| License | BSL 1.1 (since 2023) | Apache 2.0 |
| Schema types | Avro, Protobuf, JSON Schema | Avro, Protobuf, JSON Schema, OpenAPI, AsyncAPI, GraphQL, WSDL, XSD |
| Storage backends | Kafka (internal topic) | Kafka, PostgreSQL, SQL Server, H2, Infinispan |
| Compatibility | Full Confluent-compatible rules | Compatible + additional rules |
| Cloud managed | Confluent Cloud | Red Hat OpenShift Streams |
| API compatibility | REST API (de-facto standard) | Confluent-compatible REST + CNCF Registry API |

### AWS Glue Schema Registry

- Managed service within AWS Glue
- Supports Avro, JSON Schema, Protobuf
- Integrated with MSK (Managed Streaming for Kafka), Kinesis, Lambda
- License: AWS proprietary (service)

---

## 03.11 — Schema Systems Comparison Matrix

| Schema System | Governance | Tier | Data Format | Validation Location | Evolution Support | Code Gen | Type System Richness | Primary Industry |
|--------------|-----------|------|-------------|--------------------|--------------------|----------|---------------------|-----------------|
| **JSON Schema 2020-12** | Community/IETF | 2/8 | JSON | Runtime (validator) | Partial (no registry) | Yes (quicktype) | Rich (conditional, composition) | Cloud, Web, API |
| **XSD 1.1** | W3C | 2 | XML | Parse-time | Limited (versioned namespaces) | Yes (xjc, xmlbeans) | Very rich (inheritance, assertions) | Enterprise, Gov't |
| **Avro Schema** | Apache ASF | 5 | Avro binary/JSON | Write-time + read-time | Strong (Registry) | Yes (avro-tools) | Moderate | Streaming, Cloud |
| **Protobuf schema** | Google/CNCF | 6 | Protobuf binary | Compile-time + runtime | Strong (tags) | Yes (protoc) | Moderate (needs PGV for constraints) | Cloud, Mobile |
| **GraphQL SDL** | GraphQL Fnd | 5 | JSON (response) | Request-time (server) | Partial (additive only) | Yes (codegen) | Moderate | Web, API |
| **OpenAPI 3.1** | OAI/LF | 3 | JSON/YAML | Tooling (validator) | Partial (versioning) | Yes (openapi-generator) | Rich (JSON Schema 2020-12) | API, Enterprise |
| **YANG** | IETF | 2 | XML/JSON | Server-side (NETCONF) | Strong (RFC 7950 rules) | Yes (pyang) | Very rich (leafref, must, when) | Telecom, Network |
| **Pydantic** | Community | 8 | Python objects | Runtime | None (code-based) | Implicit | Rich (Python typing) | Python apps |
| **Zod** | Community | 8 | TS objects | Runtime | None (code-based) | Implicit (infer) | Rich (TS types) | TypeScript apps |
| **Confluent Registry** | Confluent | 6→7 | Avro/Proto/JSON | Registration-time | Strong (compat levels) | Via format tooling | Depends on format | Kafka Streaming |

---

## 03.12 — Cross-References

| Topic | Volume |
|-------|--------|
| IDLs that function as schemas (Protobuf, Thrift) | Volume 02 — IDLs |
| How schemas constrain serialized bytes | Volume 04 — Serialization Formats |
| Schema evolution strategies in depth | Volume 10 — Schema Evolution |
| Schema Registry tooling (CLI, CI/CD) | Volume 13 — Tooling & Code Generation |
| Industry-specific schemas (FHIR profiles, AUTOSAR) | Volume 11 — Industry-Specific |
| Governance of schema standards | Volume 12 — Governance & Standards |

---

*End of Volume 03 — Schema Systems*
