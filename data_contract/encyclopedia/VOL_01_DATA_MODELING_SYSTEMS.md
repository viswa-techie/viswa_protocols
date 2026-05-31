# VOLUME 01 — DATA MODELING SYSTEMS

## Universal Data Contract, Serialization, IDL & Interoperability Architecture Encyclopedia

---

## 01.0 — Volume Overview

Data modeling is the foundational discipline of defining, organizing, and constraining the structure, relationships, and semantics of information before it is serialized, contracted, or transmitted. This volume provides an encyclopedic treatment of every major paradigm—relational, object-oriented, semantic, network-centric, industry-specific, and API-oriented—used across enterprise systems, embedded platforms, cloud services, automotive, telecommunications, healthcare, and finance. Every technology is classified within the governance tier ladder and positioned within the universal conceptual stack.

---

## 01.1 — Formal Definition of Data Modeling

### Definition

**Data modeling** is the formal process of defining the structure, relationships, constraints, and semantics of data within a bounded domain, independent of any particular physical implementation, serialization format, or transport mechanism.

### Data Modeling vs Data Schema vs Data Format

| Concept | Definition | Layer | Example |
|---------|-----------|-------|---------|
| **Data Model** | Abstract description of entities, attributes, relationships, and business rules within a domain | Conceptual / Logical | ER diagram, UML class diagram, YANG module |
| **Data Schema** | Machine-enforceable structural description specifying types, constraints, required fields, and validation rules | Logical / Physical | JSON Schema, XSD, Avro schema, Protobuf message definition |
| **Data Format** | The specific encoding/representation of data for storage or transmission | Physical | JSON, XML, Protobuf binary wire format, CBOR, CSV |

**Critical distinction:** A data model is prescriptive (what SHOULD exist), a schema is enforceable (what MUST conform), and a format is descriptive (how bytes ARE arranged).

### Three Levels of Data Modeling

#### Conceptual Level
- **Purpose:** Capture business concepts and relationships without implementation detail
- **Audience:** Business analysts, domain experts, architects
- **Notation:** ER diagrams (Chen), UML class diagrams (conceptual mode), DDD context maps
- **Artifacts:** Entity names, relationship names, cardinality, business rules in natural language
- **Independence:** Platform-independent, language-independent, technology-independent

#### Logical Level
- **Purpose:** Formalize the conceptual model into precisely typed structures with explicit constraints
- **Audience:** Data architects, schema designers, API designers
- **Notation:** Normalized relational schema, UML with data types, JSON Schema, YANG modules
- **Artifacts:** Data types, nullability, uniqueness, referential integrity, inheritance hierarchies
- **Independence:** Platform-independent but technology-aware (knows target paradigm)

#### Physical Level
- **Purpose:** Map the logical model to a specific runtime, storage engine, or wire format
- **Audience:** Database administrators, serialization engineers, compiler engineers
- **Notation:** SQL DDL, Protobuf .proto file, Avro .avsc, FlatBuffers .fbs schema
- **Artifacts:** Column types, index definitions, partition keys, field tags, byte offsets, alignment padding
- **Independence:** Technology-specific; tied to storage engine or serialization format

### Structural vs Behavioral vs Semantic Modeling

| Dimension | What it describes | Example |
|-----------|------------------|---------|
| **Structural** | Shape of data: types, fields, nesting, arrays, references | JSON Schema properties, Protobuf message fields |
| **Behavioral** | Operations, state transitions, method signatures | gRPC service/rpc definitions, AIDL interface methods |
| **Semantic** | Meaning, ontological relationships, inference rules | OWL class axioms, RDF predicates, SKOS broader/narrower |

### Domain Model vs Data Model vs Information Model

| Concept | Scope | Audience | Example |
|---------|-------|----------|---------|
| **Domain Model** | Business logic entities and their behaviors within a bounded context | Developers, architects | DDD Aggregate, Entity, Value Object |
| **Data Model** | Persistent data structure and relationships | Data architects, DBAs | ER model, normalized tables |
| **Information Model** | Abstract representation of managed objects for a network or system | Network engineers, protocol designers | YANG module, CIM schema, SNMP MIB |

### The Role of Data Modeling as Foundation

Data modeling serves as the upstream origin for:

1. **IDL definition** — Domain entities become message types in .proto, .aidl, .fidl
2. **Schema design** — Logical model maps to JSON Schema, XSD, Avro schema
3. **API contract design** — Resources in OpenAPI derive from domain entities
4. **Database schema** — Physical model becomes SQL DDL, document collections
5. **Code generation targets** — Model drives generated classes, structs, interfaces

---

## 01.2 — Conceptual vs Logical vs Physical Level (Extended)

### Transformation Pipeline

```
    Business Requirements
           ↓
    ┌─────────────────────┐
    │  Conceptual Model   │  ← What entities exist? What are their relationships?
    │  (ER, DDD, UML)     │
    └─────────────────────┘
           ↓
    ┌─────────────────────┐
    │    Logical Model    │  ← What types, constraints, keys, nullability?
    │  (Normalized schema)│
    └─────────────────────┘
           ↓
    ┌─────────────────────┐
    │   Physical Model    │  ← What DDL? What .proto? What byte layout?
    │ (SQL, Protobuf, etc)│
    └─────────────────────┘
```

### Key Properties at Each Level

| Property | Conceptual | Logical | Physical |
|----------|-----------|---------|----------|
| Data types | Generic (text, number) | Precise (string(255), int32) | Machine-specific (VARCHAR(255), sint32) |
| Keys | Identified entities | Primary/Foreign keys | Column indexes, field tags |
| Relationships | Named associations | Referential constraints | Join tables, embedded refs |
| Constraints | Business rules | Formal predicates | CHECK, UNIQUE, NOT NULL |
| Normalization | Not applicable | 1NF–5NF applied | Denormalization for perf |
| Technology | None | Paradigm-aware | Fully bound to runtime |

---

## 01.3 — Relational Data Modeling

### 01.3.1 — Entity-Relationship (ER) Model (Chen, 1976)

**Definition:** The Entity-Relationship model, proposed by Peter Chen in 1976, is a conceptual data modeling technique that represents a domain as a set of entities, their attributes, and the relationships between entities.

**Core Constructs:**

| Construct | Symbol (Chen) | Definition |
|-----------|--------------|------------|
| **Entity** | Rectangle | A distinguishable real-world object or concept with independent existence |
| **Weak Entity** | Double rectangle | An entity that cannot be uniquely identified by its own attributes alone |
| **Attribute** | Ellipse | A property or characteristic of an entity |
| **Key Attribute** | Underlined ellipse | An attribute that uniquely identifies an entity instance |
| **Multivalued Attribute** | Double ellipse | An attribute that can hold multiple values |
| **Derived Attribute** | Dashed ellipse | An attribute whose value is computed from other attributes |
| **Relationship** | Diamond | An association among two or more entities |
| **Identifying Relationship** | Double diamond | Relationship that provides identity to a weak entity |

**Cardinality Notations:**

| Notation Style | 1:1 | 1:N | M:N |
|---------------|-----|-----|-----|
| Chen (1976) | 1—1 | 1—N | M—N |
| Crow's Foot (IE) | ‖——‖ | ‖——<∈ | ∋>——<∈ |
| UML multiplicity | 1..1 — 1..1 | 1..1 — 0..* | 0..* — 0..* |
| Min-max (ISO) | (1,1)—(1,1) | (1,1)—(0,n) | (0,n)—(0,n) |

**Strong vs Weak Entities:**
- **Strong entity:** Has a primary key composed entirely of its own attributes. Example: `Customer(CustomerID, Name, Email)`
- **Weak entity:** Requires a foreign key from an identifying (owner) entity to form its primary key. Example: `OrderItem(OrderID, LineNumber, ProductID, Quantity)` — depends on `Order`

**ISA Hierarchies (Generalization/Specialization):**
- Total vs partial participation (must every supertype instance belong to a subtype?)
- Disjoint vs overlapping subtypes (can an instance belong to multiple subtypes?)
- Example: `Vehicle ISA {Car, Truck, Motorcycle}` — disjoint, total

**Governance:** ISO/IEC 9075 (SQL standard) incorporates relational concepts; ER modeling itself is a de-facto standard (Tier 8).

### 01.3.2 — Extended ER (EER) Model

The Extended Entity-Relationship model adds higher-level abstraction constructs:

| Construct | Definition | Example |
|-----------|-----------|---------|
| **Generalization** | Bottom-up: combining entity types into a supertype | Combining `Car`, `Truck` into `Vehicle` |
| **Specialization** | Top-down: decomposing an entity into subtypes | Decomposing `Employee` into `Engineer`, `Manager` |
| **Aggregation** | Treating a relationship as a higher-level entity | `Project–WorksOn–Employee` treated as `Assignment` |
| **Category (Union type)** | A subtype that has multiple possible supertypes | `RegisteredOwner` is either a `Person` or `Company` |

### 01.3.3 — Relational Model (Codd, 1970)

**Definition:** The relational model, formalized by E.F. Codd in 1970, represents data as mathematical relations (tables of tuples) and provides relational algebra and relational calculus as formal query languages.

**Fundamental Concepts:**

| Concept | Definition |
|---------|-----------|
| **Relation** | A named table consisting of a set of tuples with a fixed schema (heading) |
| **Tuple** | A single row; an ordered set of attribute values conforming to the relation's schema |
| **Attribute** | A named column with a declared domain (data type) |
| **Domain** | The set of allowable values for an attribute (e.g., positive integers, ISO dates) |
| **Primary Key** | A minimal set of attributes that uniquely identifies each tuple |
| **Foreign Key** | An attribute set referencing the primary key of another relation |
| **Candidate Key** | Any minimal superkey (a relation may have multiple candidate keys) |

**Normal Forms:**

| Normal Form | Requirement | Eliminates |
|-------------|------------|-----------|
| **1NF** | All attributes are atomic (no repeating groups, no nested tables) | Repeating groups |
| **2NF** | 1NF + every non-key attribute depends on the entire primary key | Partial dependencies |
| **3NF** | 2NF + no transitive dependencies among non-key attributes | Transitive dependencies |
| **BCNF** | Every determinant is a candidate key | Remaining anomalies from overlapping keys |
| **4NF** | BCNF + no multivalued dependencies | Independent multivalued facts |
| **5NF (PJNF)** | 4NF + no join dependencies not implied by candidate keys | Spurious tuples on decomposition |

**Functional Dependency:** A constraint `X → Y` meaning that for any two tuples, if they agree on X, they must agree on Y.

**Governance:** ISO/IEC 9075 (SQL) — Tier 1. The relational model has been the foundation of RDBMS standards since 1986.

### 01.3.4 — UML Class Diagrams as Data Models

**Definition:** UML (Unified Modeling Language) class diagrams represent the static structure of a system by showing classes, their attributes, operations, and relationships.

**Core Diagram Elements for Data Modeling:**

| Element | Notation | Data Modeling Use |
|---------|----------|------------------|
| **Class** | Rectangle (3 compartments) | Entity / data type |
| **Attribute** | `visibility name : type [multiplicity]` | Field / column |
| **Association** | Solid line with optional arrow | Relationship between entities |
| **Aggregation** | Open diamond at container end | "has-a" (shared lifecycle) |
| **Composition** | Filled diamond at container end | "owns-a" (cascade delete) |
| **Generalization** | Triangle arrow to parent | Inheritance / ISA |
| **Multiplicity** | `1`, `0..1`, `*`, `1..*`, `m..n` | Cardinality constraint |
| **Navigability** | Arrow on association end | Read direction |

**OCL (Object Constraint Language):**
- Formal constraint language for UML models
- Example: `context Order inv: self.lineItems->size() >= 1`
- Specifies invariants, pre/post conditions, derivation rules

**Governance:** ISO/IEC 19505:2012 (UML 2.4.1), OMG (Tier 3). UML is an ISO standard with OMG as the primary maintainer.

---

## 01.4 — Object-Oriented Data Modeling

### 01.4.1 — Object Model Core Concepts

| Concept | Definition | Data Modeling Implication |
|---------|-----------|-------------------------|
| **Identity** | Each object has a unique, immutable identifier distinct from its state | Primary keys, OIDs, UUIDs |
| **State** | The collection of attribute values at a point in time | Record fields, column values |
| **Behavior** | Operations that the object can perform or that can be performed on it | Methods in IDL, service operations |
| **Encapsulation** | Internal state hidden behind a public interface | Access modifiers, interface-based contracts |
| **Inheritance** | Subtype inherits structure and behavior from supertype | Schema evolution, type hierarchies |
| **Polymorphism** | Different types respond to the same interface differently | Union types, oneof in Protobuf, CHOICE in ASN.1 |

### 01.4.2 — Domain-Driven Design (DDD) Data Modeling

**Origin:** Eric Evans, "Domain-Driven Design" (2003).

**DDD Building Blocks:**

| Pattern | Definition | Data Contract Relevance |
|---------|-----------|----------------------|
| **Entity** | An object with a distinct identity that persists over time. Two entities are equal iff they have the same identity, regardless of attribute values. | Maps to a message type with an ID field in Protobuf; a Binder Parcelable with a key. |
| **Value Object** | An immutable object with no identity; equality determined by all attribute values. | Maps to an embedded message in Protobuf; a nested struct in FlatBuffers. |
| **Aggregate** | A cluster of entities and value objects with a single root entity (Aggregate Root) that enforces consistency boundaries. | Maps to a top-level API resource or a root message. External references only point to the root. |
| **Aggregate Root** | The single entity through which all external access to the aggregate occurs. | The "document" in document databases; the top-level Protobuf message in an API. |
| **Domain Event** | A record of something that happened in the domain. Immutable once published. | Kafka event, AsyncAPI message, CloudEvent payload. |
| **Repository** | Abstraction providing access to aggregates as if they were in-memory collections. | Not a wire-format concern but informs which messages are "root-level" in API design. |
| **Bounded Context** | A clear semantic boundary within which a domain model applies consistently. | Maps to a separate .proto package, a separate microservice, a separate OpenAPI spec. |

**Ubiquitous Language:** Within a bounded context, all team members (developers, domain experts, testers) use the same terms, which appear directly in code, schemas, and documentation.

### 01.4.3 — ORM Data Models (Object-Relational Mapping)

**The Impedance Mismatch Problem:**

| OO Concept | Relational Equivalent | Mismatch |
|-----------|---------------------|----------|
| Identity (reference) | Primary key (value) | Object references ≠ foreign keys |
| Inheritance hierarchy | No native equivalent | Must choose: single table, table-per-class, table-per-concrete |
| Polymorphic collection | Requires JOIN or UNION | Performance and query complexity |
| Encapsulation | All columns exposed | No data hiding at the table level |
| Navigation (object.getX().getY()) | Explicit JOIN | N+1 query problem |

**Major ORM Frameworks:**

| Framework | Language | Governance | Mapping Approach |
|-----------|---------|-----------|-----------------|
| Hibernate / JPA | Java | Eclipse (JPA spec), Red Hat (Hibernate impl) | Annotations + XML |
| Entity Framework | C# | Microsoft | Code-first or DB-first |
| SQLAlchemy | Python | Community (MIT) | Declarative / imperative |
| Django ORM | Python | Django Software Foundation | Model classes |
| ActiveRecord | Ruby | Rails community | Convention over config |

---

## 01.5 — Semantic and Ontological Data Modeling

### 01.5.1 — RDF (Resource Description Framework)

**Definition:** RDF is a W3C standard for representing information as a directed graph of subject–predicate–object triples, enabling machine-readable semantic data on the web.

**Triple Structure:**
```
<Subject>  <Predicate>  <Object> .
```

- **Subject:** An IRI (Internationalized Resource Identifier) or blank node
- **Predicate:** An IRI (always a property)
- **Object:** An IRI, blank node, or literal value

**Example (Turtle syntax):**
```turtle
@prefix ex: <http://example.org/> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .

ex:Vehicle123 rdf:type ex:Vehicle ;
    ex:speed "120.5"^^xsd:float ;
    ex:manufacturer ex:Toyota ;
    ex:vin "1HGCM82633A123456"^^xsd:string .
```

**RDF Serialization Formats:**

| Format | MIME Type | Use Case |
|--------|----------|----------|
| Turtle | text/turtle | Human-readable |
| N-Triples | application/n-triples | Line-based, streaming |
| RDF/XML | application/rdf+xml | Legacy XML tools |
| JSON-LD | application/ld+json | Web APIs, JSON ecosystem |
| N-Quads | application/n-quads | Named graphs |

**Governance:** W3C — Tier 2. RDF 1.1 (2014).

### 01.5.2 — OWL 2 (Web Ontology Language)

**Definition:** OWL 2 is a W3C standard for defining ontologies — formal, machine-interpretable specifications of a domain's concepts, properties, and axioms — enabling automated reasoning.

**OWL 2 Profiles (subsets for tractable reasoning):**

| Profile | Reasoning Complexity | Use Case |
|---------|---------------------|----------|
| OWL 2 EL | Polynomial time | Large ontologies (SNOMED CT, Gene Ontology) |
| OWL 2 QL | Polynomial (rewritable to SQL) | Ontology-based data access |
| OWL 2 RL | Polynomial (rule-based) | Scalable reasoning with RDF stores |
| OWL 2 DL | Decidable (worst-case 2-NEXPTIME) | Full description logic reasoning |
| OWL 2 Full | Undecidable | Maximum expressivity |

**Key OWL Constructs:**

| Construct | Purpose | Example |
|-----------|---------|---------|
| `owl:Class` | Defines a concept | `ex:Vehicle rdf:type owl:Class` |
| `owl:ObjectProperty` | Relates two individuals | `ex:hasManufacturer` |
| `owl:DatatypeProperty` | Relates individual to literal | `ex:hasSpeed` |
| `owl:equivalentClass` | Two classes have same extension | `ex:Car owl:equivalentClass ex:Automobile` |
| `owl:disjointWith` | No individual can be in both classes | `ex:Car owl:disjointWith ex:Truck` |
| `owl:Restriction` | Constrains property usage | `ex:Vehicle owl:someValuesFrom ex:Engine` |
| `owl:cardinality` | Exact cardinality constraint | `owl:cardinality "4"^^xsd:int` (4 wheels) |

**Governance:** W3C — Tier 2. OWL 2 (2012).

### 01.5.3 — RDFS (RDF Schema)

**Definition:** RDFS provides basic vocabulary for describing the structure of RDF graphs — defining classes, properties, and their hierarchical relationships.

| Term | Definition |
|------|-----------|
| `rdfs:Class` | Declares a class |
| `rdfs:subClassOf` | Declares class inheritance |
| `rdfs:Property` | Declares a property |
| `rdfs:subPropertyOf` | Declares property inheritance |
| `rdfs:domain` | Restricts which classes can be subjects of a property |
| `rdfs:range` | Restricts which classes/datatypes can be objects of a property |

**Governance:** W3C — Tier 2.

### 01.5.4 — JSON-LD (JSON for Linked Data)

**Definition:** JSON-LD is a W3C standard for embedding Linked Data (RDF) in JSON documents, using a `@context` mechanism to map JSON keys to RDF IRIs.

**Key Constructs:**
```json
{
  "@context": {
    "name": "http://schema.org/name",
    "speed": "http://example.org/speed",
    "Vehicle": "http://example.org/Vehicle"
  },
  "@type": "Vehicle",
  "@id": "http://example.org/vehicle/123",
  "name": "Model S",
  "speed": 250
}
```

**Governance:** W3C — Tier 2. JSON-LD 1.1 (2020).

---

## 01.6 — Network and Telecom Data Models

### 01.6.1 — YANG (RFC 7950)

**Definition:** YANG (Yet Another Next Generation) is an IETF data modeling language for defining the structure of configuration and state data, RPCs, and notifications for network devices.

**Core Constructs:**

| Statement | Purpose | Example |
|-----------|---------|---------|
| `module` | Top-level container for a YANG model | `module ietf-interfaces { ... }` |
| `container` | Grouping of related data nodes (no instances) | `container system { ... }` |
| `leaf` | Single scalar value | `leaf hostname { type string; }` |
| `leaf-list` | Ordered/unordered list of scalar values | `leaf-list dns-server { type inet:ipv4-address; }` |
| `list` | Ordered sequence of keyed entries | `list interface { key "name"; ... }` |
| `choice` | Mutually exclusive branches | `choice address-family { case ipv4 {...} case ipv6 {...} }` |
| `grouping` | Reusable template of data nodes | `grouping endpoint { leaf ip {...} leaf port {...} }` |
| `uses` | Instantiates a grouping | `uses endpoint;` |
| `augment` | Adds nodes to another module's tree | `augment "/sys:system" { leaf ntp-server {...} }` |
| `deviation` | Declares non-conformance to a published model | `deviation "/if:interfaces/if:interface/if:mtu" { deviate replace { type uint16; } }` |
| `rpc` | Defines a remote procedure call | `rpc restart { input { leaf reason { type string; } } }` |
| `notification` | Defines an asynchronous event | `notification link-failure { leaf if-name { type string; } }` |

**Type System:**
- Built-in types: `int8`, `int16`, `int32`, `int64`, `uint8`–`uint64`, `string`, `boolean`, `empty`, `binary`, `decimal64`, `enumeration`, `bits`, `identityref`, `leafref`, `union`, `instance-identifier`
- Derived types via `typedef`
- Pattern and range constraints

**Transport Bindings:**

| Protocol | RFC | Data Encoding | Transport |
|----------|-----|---------------|-----------|
| NETCONF | RFC 6241 | XML | SSH (TCP 830) |
| RESTCONF | RFC 8040 | JSON or XML | HTTPS (TCP 443) |
| gNMI | OpenConfig | Protobuf | gRPC/HTTP2 |

**Governance:** IETF — Tier 2. RFC 7950 (YANG 1.1, 2016).

### 01.6.2 — SMIv2 (Structure of Management Information v2)

**Definition:** SMIv2 defines the syntax for writing SNMP MIB (Management Information Base) modules, specifying managed objects for network monitoring.

**Key Macros:**
- `MODULE-IDENTITY`: Module metadata (contact, description, revision)
- `OBJECT-TYPE`: Defines a managed object (syntax, access, status, description)
- `OBJECT-IDENTITY`: Named node in the MIB tree
- `NOTIFICATION-TYPE`: SNMP trap/notification definition

**Data Types:** `INTEGER`, `OCTET STRING`, `OBJECT IDENTIFIER`, `Counter32`, `Counter64`, `Gauge32`, `TimeTicks`, `IpAddress`

**Object Identifier (OID) Tree:**
```
iso(1).org(3).dod(6).internet(1).mgmt(2).mib-2(1).interfaces(2).ifTable(2).ifEntry(1).ifDescr(2)
   →  1.3.6.1.2.1.2.2.1.2
```

**Governance:** IETF — Tier 2. RFC 2578 (SMIv2, 1999).

### 01.6.3 — CIM (Common Information Model)

**Definition:** CIM is a DMTF standard providing a conceptual framework for describing managed elements in an IT environment using object-oriented modeling.

**Managed Object Format (MOF) Syntax:**
```mof
[Description("A network interface card")]
class CIM_NetworkAdapter : CIM_LogicalDevice {
    [Key] string DeviceID;
    string MACAddress;
    uint64 Speed;
    boolean AutoSense;
};
```

**Usage:** Windows Management Instrumentation (WMI), Web-Based Enterprise Management (WBEM).

**Governance:** DMTF (Distributed Management Task Force) — Tier 3.

### 01.6.4 — OpenConfig Models

**Definition:** OpenConfig is a vendor-neutral set of YANG models for network device configuration and telemetry, developed by operators (Google, Microsoft, AT&T, etc.).

**Key Characteristics:**
- Tree-structured models (separate from IETF YANG models)
- Operational state and configuration in same model (`config` vs `state` containers)
- gNMI (gRPC Network Management Interface) as primary transport
- Streaming telemetry support

**Governance:** OpenConfig Working Group — Tier 3 (industry consortium of network operators).

---

## 01.7 — Industry-Specific Data Models

### 01.7.1 — FHIR (Fast Healthcare Interoperability Resources)

**Definition:** FHIR is an HL7 International standard for exchanging healthcare information electronically, based on a set of modular resource types with well-defined data models.

**Core Resource Types:**

| Resource | Description | Key Fields |
|----------|------------|-----------|
| Patient | Demographics of a person receiving care | identifier, name, birthDate, gender |
| Observation | Measurements, vital signs, lab results | code (LOINC), value, effectiveDateTime |
| Condition | Diagnosis or clinical condition | code (SNOMED CT), clinicalStatus, subject |
| Medication | Drug or substance | code, form, ingredient |
| Encounter | Healthcare interaction (visit) | status, class, period, participant |
| Procedure | Clinical intervention performed | code, status, performedDateTime |
| DiagnosticReport | Findings (radiology, pathology) | code, conclusion, result references |

**FHIR Extensibility Model:**
- **Profiles:** Constrain base resources for specific use cases (e.g., US Core Patient)
- **Extensions:** Add new elements not in base resource (structured, registered)
- **Value Sets:** Bound terminology (SNOMED CT, LOINC, ICD-10, RxNorm)

**Representations:** JSON (primary), XML, RDF (Turtle)

**Governance:** HL7 International — Tier 4. FHIR R4 (2019), R5 (2023).

### 01.7.2 — ISO 20022 (Financial Messaging)

**Definition:** ISO 20022 is an international standard for financial messaging that provides a metadata-driven methodology for creating financial message definitions using UML-based modeling.

**ISO 20022 Methodology Pipeline:**

```
Business Process Catalogue (BPC)
         ↓
Logical Data Dictionary (LDD)  →  Business Components, Data Types
         ↓
Message Definition Reports (MDR)  →  Message Definitions
         ↓
XML Schema (XSD)  →  Physical Implementation
```

**Key Concepts:**
- **Business Component:** Reusable business concept (e.g., FinancialInstitution, Account, Party)
- **Message Component:** Message-specific usage of business components
- **Message Definition:** Complete message structure (pacs.008 = FI-to-FI Payment)
- **Code Sets:** Standardized enumerated values

**Examples of ISO 20022 Messages:**
- `pacs.008` — Financial Institution to Financial Institution Customer Credit Transfer
- `pain.001` — Customer Credit Transfer Initiation
- `camt.053` — Bank to Customer Statement

**Governance:** ISO TC68/SC9 — Tier 1. SWIFT is the registration authority.

### 01.7.3 — AUTOSAR Data Types

**Definition:** AUTOSAR (AUTomotive Open System ARchitecture) defines standardized data types for communication between software components in automotive ECUs.

**Type Categories:**

| Category | Purpose | Example |
|----------|---------|---------|
| **Implementation Data Type** | C-level type for code generation | `uint16`, `float32`, `boolean` |
| **Application Data Type** | Logical type with physical semantics | `VehicleSpeed` (0–400 km/h, resolution 0.01) |
| **Base Type** | Platform-specific primitive | `uint8` (8-bit unsigned) |

**Port Interface Data Types:**
- **Sender-Receiver:** Asynchronous data element exchange
- **Client-Server:** Synchronous operation call with parameters

**Data Constraint:** Specifies valid range, resolution, offset for physical quantities
- Example: `VehicleSpeed` = raw * 0.01 + 0 (range: 0.00–655.35 km/h)

**Governance:** AUTOSAR Consortium — Tier 3.

### 01.7.4 — COVESA VSS (Vehicle Signal Specification)

**Definition:** VSS is a standardized vehicle signal catalog organized as a tree structure, providing a common naming convention and data model for vehicle data.

**Tree Structure:**
```
Vehicle
├── Speed                    (sensor, float, km/h)
├── CurrentLocation
│   ├── Latitude             (sensor, double, degrees)
│   ├── Longitude            (sensor, double, degrees)
│   └── Altitude             (sensor, double, meters)
├── Body
│   ├── Lights
│   │   ├── IsHighBeamOn     (actuator, boolean)
│   │   └── IsLowBeamOn      (actuator, boolean)
│   └── Trunk
│       └── IsOpen           (actuator, boolean)
├── Powertrain
│   ├── FuelSystem
│   │   └── Level            (sensor, uint8, percent)
│   └── ElectricMotor
│       └── Power            (sensor, uint16, kW)
└── ADAS
    ├── ABS
    │   └── IsActive         (sensor, boolean)
    └── ESC
        └── IsActive         (sensor, boolean)
```

**Node Types:**

| Type | Definition | Example |
|------|-----------|---------|
| **Branch** | Organizational container | `Vehicle.Body` |
| **Sensor** | Read-only value from vehicle | `Vehicle.Speed` |
| **Actuator** | Writable value to control vehicle | `Vehicle.Body.Trunk.IsOpen` |
| **Attribute** | Static or rarely-changing property | `Vehicle.VehicleIdentification.VIN` |

**Export Formats:** JSON, YAML, CSV, Protobuf, GraphQL, Franca IDL

**Governance:** COVESA (Connected Vehicle Systems Alliance) / Eclipse Foundation — Tier 3.

### 01.7.5 — ROS2 Message Types (.msg / .srv / .action)

**Definition:** ROS2 (Robot Operating System 2) uses .msg, .srv, and .action files to define the data types exchanged between nodes via DDS-based publish-subscribe and service-call patterns.

**.msg File (topic data):**
```
# geometry_msgs/msg/Twist.msg
Vector3 linear
Vector3 angular
```

**.srv File (service request/response):**
```
# std_srvs/srv/SetBool.srv
bool data
---
bool success
string message
```

**.action File (long-running goal + feedback + result):**
```
# navigation_msgs/action/NavigateToPose.action
geometry_msgs/PoseStamped pose
---
std_msgs/Empty result
---
geometry_msgs/PoseStamped current_pose
float32 distance_remaining
```

**Primitive Types:** `bool`, `byte`, `char`, `float32`, `float64`, `int8`, `int16`, `int32`, `int64`, `uint8`–`uint64`, `string`, `wstring`

**Code Generation:** `rosidl` generates C++, Python, and other language bindings via DDS IDL.

**Governance:** Open Robotics Foundation — Tier 5.

---

## 01.8 — API-Oriented Data Models

### 01.8.1 — OpenAPI Schema Object as Data Model

**Definition:** The OpenAPI Schema Object (derived from JSON Schema) serves as the data modeling language for REST API request/response payloads, providing a type system for HTTP-based services.

**Key Schema Object Properties:**

| Property | Purpose | Example |
|----------|---------|---------|
| `type` | Declares the JSON type | `object`, `array`, `string`, `integer`, `number`, `boolean` |
| `properties` | Named fields of an object | `{ "name": { "type": "string" } }` |
| `required` | Fields that must be present | `["id", "name"]` |
| `$ref` | Reference to a reusable schema | `$ref: '#/components/schemas/Vehicle'` |
| `allOf` | Intersection / inheritance | Compose base + extension schemas |
| `oneOf` | Discriminated union (exactly one) | Polymorphic responses |
| `anyOf` | Union (one or more) | Flexible validation |
| `enum` | Enumerated values | `["ACTIVE", "INACTIVE", "DELETED"]` |
| `format` | Semantic format hint | `date-time`, `email`, `uuid`, `uri` |
| `nullable` | Allow JSON null (OAS 3.0) | `nullable: true` |
| `discriminator` | Polymorphism discriminator field | `{ propertyName: "vehicleType" }` |

**Example:**
```yaml
components:
  schemas:
    Vehicle:
      type: object
      required: [vin, make, model]
      properties:
        vin:
          type: string
          pattern: '^[A-HJ-NPR-Z0-9]{17}$'
        make:
          type: string
          example: "Toyota"
        model:
          type: string
        year:
          type: integer
          minimum: 1886
          maximum: 2100
        speed:
          type: number
          format: float
          minimum: 0
          maximum: 500
```

**Governance:** OpenAPI Initiative (Linux Foundation) — Tier 3. OpenAPI 3.1 aligns fully with JSON Schema 2020-12.

### 01.8.2 — JSON Schema as Data Modeling Language

**Definition:** JSON Schema is a declarative language for describing the structure, constraints, and semantics of JSON documents, serving as both a validation tool and a data modeling specification.

**JSON Schema 2020-12 Key Features:**

| Keyword | Purpose |
|---------|---------|
| `$schema` | Declares which JSON Schema draft is used |
| `$id` | Canonical URI for the schema |
| `$ref` | Cross-references another schema |
| `$defs` | Local schema definitions (reusable) |
| `type` | Constrains the JSON type |
| `properties` | Object field definitions |
| `additionalProperties` | Controls whether extra properties are allowed |
| `unevaluatedProperties` | Stricter: controls properties not matched by any subschema |
| `items` | Schema for array elements |
| `prefixItems` | Tuple validation (positional array elements) |
| `required` | Mandatory properties |
| `if/then/else` | Conditional schema application |
| `dependentRequired` | If property X present, property Y must also be present |
| `pattern` | Regex constraint on strings |
| `minimum/maximum` | Numeric range constraints |
| `const` | Fixed value |

**Governance:** JSON Schema is maintained by a community group with IETF Internet-Draft status (draft-bhutton-json-schema-01). The specification is referenced by OpenAPI 3.1. Tier 2/8 (IETF draft + de-facto).

### 01.8.3 — GraphQL Type System as Data Model

**Definition:** The GraphQL type system defines the shape of data available in a GraphQL API through a schema-first approach using the Schema Definition Language (SDL).

**Type Kinds:**

| Kind | Syntax | Purpose |
|------|--------|---------|
| **Scalar** | `scalar DateTime` | Leaf types (String, Int, Float, Boolean, ID + custom) |
| **Object** | `type Vehicle { ... }` | Entity with named fields |
| **Interface** | `interface Node { id: ID! }` | Abstract type with required fields |
| **Union** | `union SearchResult = Car \| Truck` | Polymorphic result without shared fields |
| **Enum** | `enum FuelType { PETROL DIESEL ELECTRIC }` | Enumerated values |
| **Input** | `input CreateVehicleInput { ... }` | Input-only object type (for mutations) |
| **List** | `[Vehicle]` | Ordered collection |
| **Non-Null** | `String!` | Value must be present (not null) |

**Example:**
```graphql
type Vehicle {
  id: ID!
  vin: String!
  make: String!
  model: String!
  year: Int!
  speed: Float
  fuelType: FuelType!
  owner: Person
}

enum FuelType {
  PETROL
  DIESEL
  ELECTRIC
  HYBRID
}

type Query {
  vehicle(id: ID!): Vehicle
  vehicles(make: String, limit: Int = 10): [Vehicle!]!
}
```

**Governance:** GraphQL Foundation (Linux Foundation) — Tier 5.

### 01.8.4 — Protobuf Message as Data Model

**Definition:** Protocol Buffers message definitions serve as language-neutral, strongly-typed data models that simultaneously define the logical structure and the physical serialization layout.

**Key Features as Data Model:**
- Explicit field numbering (tags) — evolution-safe
- Strongly typed: scalar types, nested messages, enums, maps, repeated fields
- Well-known types provide standard modeling constructs:
  - `google.protobuf.Timestamp` — date/time
  - `google.protobuf.Duration` — time interval
  - `google.protobuf.Struct` — dynamic key-value
  - `google.protobuf.Any` — self-describing message
  - `google.protobuf.FieldMask` — partial update specification

**Example:**
```protobuf
syntax = "proto3";
package automotive.vehicle;

import "google/protobuf/timestamp.proto";

message Vehicle {
  string vin = 1;
  string make = 2;
  string model = 3;
  int32 year = 4;
  
  message Location {
    double latitude = 1;
    double longitude = 2;
    double altitude = 3;
  }
  
  Location current_location = 5;
  float current_speed_kmh = 6;
  FuelType fuel_type = 7;
  google.protobuf.Timestamp last_service_date = 8;
}

enum FuelType {
  FUEL_TYPE_UNSPECIFIED = 0;
  FUEL_TYPE_PETROL = 1;
  FUEL_TYPE_DIESEL = 2;
  FUEL_TYPE_ELECTRIC = 3;
  FUEL_TYPE_HYBRID = 4;
}
```

**Governance:** Google / CNCF — Tier 6.

---

## 01.9 — Data Contract Patterns and Principles

### 01.9.1 — Contract-First vs Code-First Development

| Approach | Workflow | Advantages | Disadvantages |
|----------|---------|-----------|---------------|
| **Contract-First** | Write IDL/schema → generate code → implement logic | Precise cross-team contracts; language-neutral; better API governance; avoids drift | Requires upfront design investment; generated code may need wrapping |
| **Code-First** | Write implementation → extract schema/IDL from code | Faster initial development; schema reflects reality | Schema drift risk; harder to govern; language bias leaks into contract |

**When to use Contract-First:**
- Multi-team or multi-organization integration
- Public APIs with external consumers
- Long-lived interfaces that must evolve safely (Stable AIDL, gRPC services)
- Any situation where producer and consumer are deployed independently

**When Code-First is acceptable:**
- Rapid prototyping (single team, single service)
- Internal-only services with co-deployed consumer (monorepo)
- When tooling can extract and enforce the schema (e.g., Pydantic → OpenAPI)

### 01.9.2 — Producer vs Consumer Contracts

| Perspective | Definition | Enforcement |
|-------------|-----------|-------------|
| **Producer Contract** | "I promise to emit data conforming to this schema." | Schema validation on output; backward-compatible changes only |
| **Consumer Contract** | "I require data with at least these fields present." | Consumer-driven contract tests (Pact); tolerant reader pattern |
| **Full Contract** | Producer and consumer agree on exact shape. | Both sides validate; versioned; breaking changes require coordination |

**Consumer-Driven Contract Testing (Pact):**
1. Consumer defines expectations (a "pact") specifying required fields and values
2. Pact is published to a Pact Broker
3. Provider verifies against all consumer pacts before deployment
4. `can-i-deploy`: checks all pacts are verified before release gate

### 01.9.3 — Temporal Data Modeling

**Bitemporal Model:**
- **Transaction time:** When the fact was recorded in the system
- **Valid time:** When the fact is/was true in the real world
- Both axes are tracked independently, enabling historical corrections

| Query Type | Description |
|-----------|------------|
| Current state | valid_time = now AND transaction_time = now |
| Point-in-time | valid_time = T₁ AND transaction_time = now |
| As-was | valid_time = T₁ AND transaction_time = T₂ |

**Event Sourcing:**
- State is derived from an append-only log of domain events
- No destructive updates; full audit trail
- Rebuild state by replaying events from any point

**CQRS (Command Query Responsibility Segregation):**
- Write model (command side): normalized, event-driven, consistency-enforced
- Read model (query side): denormalized, optimized for specific queries
- Separate data models for write path and read path

---

## 01.10 — Comparison Table: Data Modeling Approaches

| Paradigm | Notation | Governance Body | Tier | Primary Layer | Code Gen | Schema Output | Primary Industry |
|----------|----------|----------------|------|---------------|----------|---------------|-----------------|
| ER Model (Chen) | Chen / Crow's Foot | ISO (via SQL) | 1/8 | Conceptual | Partial (DDL) | SQL DDL | Enterprise |
| UML Class Diagram | UML 2.5 | OMG / ISO | 3/1 | Logical | Yes (EMF, xjc) | XSD, ORM | Enterprise, Automotive |
| Domain-Driven Design | None (patterns) | Community | 8 | Conceptual | No | None | Software Architecture |
| RDF 1.1 | Turtle / RDF/XML | W3C | 2 | Semantic | Partial | OWL, SPARQL | Knowledge Graphs, Government |
| OWL 2 | OWL/XML, Turtle | W3C | 2 | Semantic | Partial | Inference rules | Life Sciences, Biomedical |
| YANG (RFC 7950) | YANG syntax | IETF | 2 | Network config | Yes (pyang) | NETCONF XML / RESTCONF | Telecom, Cloud Networking |
| SMIv2 | MIB syntax | IETF | 2 | Network mgmt | Partial | SNMP MIBs | Telecom (legacy) |
| Protobuf message | .proto | Google / CNCF | 6 | Contract/Physical | Yes (protoc) | Binary wire | Cloud, Automotive, Mobile |
| JSON Schema | JSON | Community / IETF | 2/8 | Logical | Yes (quicktype) | JSON validation | Cloud, Enterprise, Web |
| OpenAPI Schema | YAML/JSON | OpenAPI Initiative | 3 | API model | Yes (codegen) | REST API | Enterprise, Cloud |
| GraphQL SDL | GraphQL | GraphQL Foundation | 5 | API graph | Yes (codegen) | GraphQL API | Web, Cloud |
| VSS | YAML tree | COVESA | 3 | Domain (vehicle) | Partial | JSON, Protobuf, Franca | Automotive |
| ISO 20022 | UML/XML | ISO TC68 | 1 | Financial domain | Yes | XSD (MX messages) | Finance, Banking |
| FHIR Resource | JSON/XML | HL7 International | 4 | Healthcare domain | Yes (HAPI) | JSON, XML, RDF | Healthcare |
| AUTOSAR Types | ARXML | AUTOSAR | 3 | Automotive domain | Yes (BSW gen) | C code, ARXML | Automotive |
| ROS2 .msg | .msg/.srv/.action | Open Robotics | 5 | Robotics domain | Yes (rosidl) | C++, Python via DDS | Robotics |
| CIM/MOF | MOF syntax | DMTF | 3 | IT management | Partial | WMI/WBEM | IT Operations |

---

## 01.11 — Individual Technology Entries

### TECHNOLOGY: Entity-Relationship (ER) Model

| Field | Value |
|-------|-------|
| **Category** | Data Modeling |
| **Sub-category** | Relational / Conceptual Modeling |
| **Full name** | Entity-Relationship Model |
| **Common abbreviations** | ER model, ERD |

**WHAT IT IS:** A conceptual data modeling technique that represents a domain as entities (things), attributes (properties of things), and relationships (associations between things), using a graphical diagram notation.

**WHAT PROBLEM IT SOLVES:** Provides a technology-independent way to capture business data requirements and communicate them visually between business stakeholders and database designers before any physical implementation decisions are made.

**ARCHITECTURAL LAYER:** Conceptual modeling layer — above all physical schema, serialization, and transport layers.

**CLASSIFICATION:**
- Paradigm: Conceptual data modeling
- Schema output: SQL DDL (via transformation)
- Type safety: Informal (types are descriptive, not machine-enforced)
- Format: Graphical diagram (Chen, Crow's Foot, or IDEF1X notation)

**GOVERNANCE:**
- Body: No formal standards body for ER diagrams themselves (Tier 8, de-facto)
- The relational model derived from ER is standardized as ISO/IEC 9075 (SQL)
- UML (ISO/IEC 19505) provides a standardized alternative notation

**STRENGTHS:**
- Universally understood visual notation for data structure
- Technology-independent; applicable before choosing database, language, or format
- Directly translatable to 3NF relational schemas

**WEAKNESSES:**
- No machine-readable standard format (diagrams are visual artifacts)
- Cannot express behavioral contracts (no method signatures, no RPC)
- Limited support for complex constraints (must supplement with text)
- No built-in evolution or versioning semantics

**RELATED TECHNOLOGIES:**
- UML Class Diagram (successor notation for data modeling)
- SQL DDL (physical output)
- JSON Schema, XSD (alternative logical modeling)
- Domain-Driven Design (complements with behavioral patterns)

---

### TECHNOLOGY: YANG (RFC 7950)

| Field | Value |
|-------|-------|
| **Category** | Data Modeling |
| **Sub-category** | Network Configuration Data Modeling |
| **Full name** | Yet Another Next Generation |
| **Common abbreviations** | YANG |

**WHAT IT IS:** A formal data modeling language standardized by the IETF (RFC 7950) for defining the structure of configuration data, state data, remote procedure calls, and notifications for network management protocols.

**WHAT PROBLEM IT SOLVES:** Provides a single, precise, machine-readable description of what configuration a network device accepts and what operational state it reports, enabling multi-vendor interoperability and automated network management.

**ARCHITECTURAL LAYER:** Data modeling layer with direct bindings to transport protocols (NETCONF/XML over SSH, RESTCONF/JSON over HTTPS, gNMI/Protobuf over gRPC).

**CLASSIFICATION:**
- Paradigm: Hierarchical, tree-structured data model
- Schema output: XML (NETCONF), JSON (RESTCONF), Protobuf (gNMI)
- Type safety: Strong (built-in type system with constraints)
- Format: Text source (.yang files)

**GOVERNANCE:**
- Body: IETF — Tier 2
- Specification: RFC 7950 (YANG 1.1, 2016), RFC 6020 (YANG 1.0, 2010)
- IP Policy: IETF BCP 79 (RAND-free preferred for IETF standards)
- Implementation diversity: pyang (Python), libyang (C), ConfD (Cisco/Tail-f), OpenDaylight

**SCHEMA / CONTRACT CAPABILITIES:**
- Hierarchical containment (containers within containers)
- Keyed lists with uniqueness enforcement
- Strong type system with range, pattern, and enumeration constraints
- Must/when expressions for complex cross-field constraints
- Augmentation: extend published models without modifying source
- Deviation: document non-conformance

**COMPATIBILITY & EVOLUTION:**
- RFC 7950 Section 11 defines module update rules
- Nodes must not be removed (only deprecated/obsoleted)
- Types must not become more restrictive
- New mandatory nodes must not be added to existing containers
- Module revision dates track versions

**TRANSPORT & RUNTIME:**
- NETCONF: RFC 6241 — XML encoding, SSH transport, edit-config / get-config operations
- RESTCONF: RFC 8040 — JSON or XML, HTTP methods (GET/PUT/POST/PATCH/DELETE)
- gNMI: OpenConfig — Protobuf encoding, gRPC transport, Set/Get/Subscribe

**INDUSTRY USAGE:**
- Telecom: Primary (all major NOS: Junos, IOS-XR, SONiC, Nokia SR OS)
- Cloud networking: Primary (Google, Microsoft Azure, Meta — OpenConfig models)
- Enterprise: Growing (Arista, Cisco, Juniper vendor models)
- Automotive: Rare
- Embedded: Rare

**STRENGTHS:**
- Formal precision eliminates ambiguity in device configuration
- Multi-vendor interoperability via common model
- Strong evolution rules prevent breaking changes
- Tooling ecosystem: pyang (validation, tree output), yanglint (data validation)

**WEAKNESSES:**
- Steep learning curve compared to informal YAML/JSON modeling
- Verbose for simple use cases
- NETCONF/XML encoding overhead for high-volume telemetry (gNMI addresses this)
- Model diversity: IETF, OpenConfig, and vendor-specific models sometimes conflict

**REFERENCE:**
- Specification: RFC 7950 (YANG 1.1), RFC 6020 (YANG 1.0)
- IETF YANG module repository: https://github.com/YangModels/yang
- OpenConfig models: https://github.com/openconfig/public

---

### TECHNOLOGY: Protocol Buffers (Message as Data Model)

| Field | Value |
|-------|-------|
| **Category** | Data Modeling / Interface Definition |
| **Sub-category** | Cross-language Typed Data Model |
| **Full name** | Protocol Buffers |
| **Common abbreviations** | Protobuf, proto |

**WHAT IT IS:** A language-neutral, platform-neutral mechanism for defining structured data types using `.proto` files. When viewed as a data modeling system, Protobuf message definitions serve as the canonical, versioned description of data structures shared across services.

**WHAT PROBLEM IT SOLVES:** Provides a single source of truth for data structure definition that can generate native code in 10+ languages, with built-in backward/forward compatibility semantics, eliminating manual serialization code and cross-language type mismatches.

**ARCHITECTURAL LAYER:** Spans both the Data Modeling layer (message definitions) and the Serialization layer (wire format). When combined with gRPC, also spans the RPC layer.

**CLASSIFICATION:**
- Paradigm: Strongly-typed, tag-based, schema-mandatory message format
- Schema output: Binary wire format (Protobuf encoding), JSON (via JsonFormat)
- Type safety: Strong (generated code is type-safe in each target language)
- Format: Text source (.proto files) → binary physical format

**GOVERNANCE:**
- Body: Google (primary), CNCF (gRPC graduated project) — Tier 6
- License: Apache 2.0
- Specification: https://protobuf.dev/programming-guides/encoding/
- CLA: Google CLA required for contributions

**SCHEMA / CONTRACT CAPABILITIES:**
- Scalar types: double, float, int32, int64, uint32, uint64, sint32, sint64, fixed32, fixed64, sfixed32, sfixed64, bool, string, bytes
- Composite types: message (struct), enum, oneof (union), map
- Repeated fields (arrays), nested messages
- Well-known types: Timestamp, Duration, Any, Struct, FieldMask, Wrappers

**COMPATIBILITY & EVOLUTION:**
- Field tags provide wire-stable identity (never reuse deleted tag numbers)
- Add optional/repeated fields: backward and forward compatible
- Remove optional fields: backward compatible (old readers ignore unknown tags)
- `reserved` keyword prevents accidental tag reuse
- enum: always include UNSPECIFIED = 0; new values are forward-compatible

**INDUSTRY USAGE:**
- Cloud: Primary (Google, most gRPC-based microservices)
- Mobile: Primary (Android, iOS apps using gRPC)
- Automotive: Secondary (AAOS Vehicle HAL, some ADAS pipelines)
- Telecom: Secondary (gNMI uses Protobuf over gRPC)
- Finance: Secondary (some trading systems)
- Embedded: Secondary (where code size permits)

**STRENGTHS:**
- Compact binary encoding (significantly smaller than JSON/XML)
- Built-in schema evolution with backward/forward compatibility
- Code generation in all major languages with full type safety
- Mature ecosystem: buf, protoc, grpc, well-known types
- Self-describing with FileDescriptorSet (reflection)

**WEAKNESSES:**
- Not human-readable in binary form (requires tools to inspect)
- No inheritance or polymorphism (must use composition + oneof)
- proto3 removed required fields (some view as loss of expressiveness)
- Google single-vendor risk (mitigated by CNCF and Apache 2.0 license)

**REFERENCE:**
- Language Guide: https://protobuf.dev/programming-guides/proto3/
- Encoding: https://protobuf.dev/programming-guides/encoding/
- Well-known types: https://protobuf.dev/reference/protobuf/google.protobuf/

---

### TECHNOLOGY: COVESA Vehicle Signal Specification (VSS)

| Field | Value |
|-------|-------|
| **Category** | Data Modeling |
| **Sub-category** | Automotive Domain Data Model |
| **Full name** | Vehicle Signal Specification |
| **Common abbreviations** | VSS |

**WHAT IT IS:** A standardized, hierarchical catalog of vehicle signals (speed, location, battery level, door status, etc.) organized as a tree, providing a uniform naming convention and data type specification for vehicle data across the automotive industry.

**WHAT PROBLEM IT SOLVES:** Eliminates proprietary, OEM-specific signal naming (e.g., "VEH_SPD_01" vs "vehicle_speed" vs "VS") by providing a single, agreed-upon taxonomy that enables data portability across vehicles, applications, and cloud platforms.

**ARCHITECTURAL LAYER:** Domain data model layer — defines WHAT signals exist and their semantics, independent of HOW they are transported (SOME/IP, MQTT, gRPC, REST).

**GOVERNANCE:**
- Body: COVESA (Connected Vehicle Systems Alliance), hosted by Eclipse Foundation — Tier 3
- License: Mozilla Public License 2.0 (MPL-2.0)
- Repository: https://github.com/COVESA/vehicle_signal_specification

**SCHEMA / CONTRACT CAPABILITIES:**
- Tree-structured hierarchy (branches, leaves)
- Four leaf types: sensor, actuator, attribute, (deprecated: stream)
- Data types: uint8–uint64, int8–int64, float, double, boolean, string, string[]
- Unit system (km/h, degrees, percent, celsius)
- Min/max constraints, allowed values (enum-like)

**INDUSTRY USAGE:**
- Automotive: Primary (COVESA members: BMW, Volvo, Bosch, Continental, etc.)
- Cloud data platforms: Growing (AWS IoT FleetWise uses VSS)
- Other industries: Not applicable

**STRENGTHS:**
- Standardized naming eliminates cross-OEM ambiguity
- Extensible: private branches for OEM-specific signals
- Multiple export formats: JSON, YAML, Franca IDL, Protobuf, GraphQL
- Active community with regular releases

**WEAKNESSES:**
- Not a complete data model (no relationships between signals)
- Limited constraint expressiveness compared to JSON Schema or XSD
- Adoption still growing; not yet mandated by regulations
- No formal versioning/compatibility rules (relies on semantic versioning)

---

### TECHNOLOGY: FHIR (Fast Healthcare Interoperability Resources)

| Field | Value |
|-------|-------|
| **Category** | Data Modeling |
| **Sub-category** | Healthcare Domain Data Model |
| **Full name** | Fast Healthcare Interoperability Resources |
| **Common abbreviations** | FHIR (pronounced "fire") |

**WHAT IT IS:** A standard for exchanging healthcare information electronically, defining a set of modular "Resources" (Patient, Observation, Condition, etc.) with well-specified data models, RESTful API patterns, and extensibility mechanisms.

**WHAT PROBLEM IT SOLVES:** Replaces the complexity of HL7 v2 (pipe-delimited segments) and HL7 v3 (complex RIM-based XML) with a modern, web-friendly standard that is implementable by mainstream developers, enabling healthcare data interoperability.

**ARCHITECTURAL LAYER:** Domain data model + API contract layer. FHIR defines both the data structure (resource models) and the interaction patterns (REST, messaging, documents).

**GOVERNANCE:**
- Body: HL7 International — Tier 4
- Specification: https://hl7.org/fhir/ (R4: 2019, R4B: 2022, R5: 2023)
- IP Policy: HL7 International IP Policy (free to implement)
- Ballot process: HL7 standards track with public comment periods

**SCHEMA / CONTRACT CAPABILITIES:**
- ~150 resource types with defined elements
- Profiles: constrain resources for jurisdictions (US Core, UK Core, AU Base)
- Extensions: structured additions (no breaking the base model)
- Terminology binding: SNOMED CT, LOINC, ICD-10, RxNorm, UCUM
- SearchParameter: queryable fields defined per resource

**COMPATIBILITY & EVOLUTION:**
- Maturity levels per resource (0=Draft through 5=Normative)
- Normative resources have strict backward-compatibility guarantees
- Trial-Use resources may have breaking changes between versions
- Extensions provide forward-compatibility (add without breaking)

**INDUSTRY USAGE:**
- Healthcare: Primary (US ONC regulations mandate FHIR for patient access)
- Life Sciences: Secondary (clinical trial data exchange)
- Other industries: Not applicable

**STRENGTHS:**
- Modern web architecture (REST, JSON, OAuth 2.0)
- Implementable by mainstream developers (vs HL7 v3 complexity)
- Rich ecosystem: HAPI FHIR (Java), SMART on FHIR (apps), CDS Hooks
- Extensibility without breaking base standard

**WEAKNESSES:**
- Large specification surface (~150 resources, each with many elements)
- Profiling complexity (many overlapping profiles per jurisdiction)
- Performance concerns for bulk data operations (mitigated by FHIR Bulk Data)
- Not yet complete coverage of all healthcare domains

---

## 01.12 — Cross-References

| Topic | Relevant Volume |
|-------|----------------|
| IDL as the next layer after data modeling | Volume 02 — Interface Definition Languages |
| Schema systems that enforce data models | Volume 03 — Schema Systems |
| How data models become serialized bytes | Volume 04 — Serialization Formats |
| YANG as part of telecom ecosystem | Volume 11 — Industry-Specific Standards |
| FHIR as part of healthcare ecosystem | Volume 11 — Industry-Specific Standards |
| ISO 20022 as financial standard | Volume 11 — Industry-Specific Standards |
| AUTOSAR data types in automotive | Volume 11 — Industry-Specific Standards |
| Governance tiers applied to each technology | Volume 12 — Governance & Standards Bodies |
| Schema evolution and compatibility | Volume 10 — Schema Evolution |
| Contract-first API design with OpenAPI | Volume 08 — API Contract Systems |

---

*End of Volume 01 — Data Modeling Systems*
