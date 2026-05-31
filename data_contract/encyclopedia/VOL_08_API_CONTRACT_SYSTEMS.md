# VOLUME 08 — API CONTRACT SYSTEMS

## Universal Data Contract, Serialization, IDL & Interoperability Architecture Encyclopedia

---

## 08.0 — What is an API Contract System (Formal Definition)

**Definition:** An API contract system is a machine-readable specification that describes the endpoints or channels a service exposes, the request and response data shapes, authentication requirements, error structures, versioning policies, and operation semantics — enabling documentation, SDK generation, validation, and governance without requiring code generation as the primary output.

**API Contract vs IDL vs Schema:**

| Dimension | API Contract | IDL (Volume 02) | Schema (Volume 03) |
|-----------|-------------|-----------------|---------------------|
| Primary output | Documentation + validation + governance | Generated code (stubs/skeletons) | Validation rules for data |
| Scope | Full API surface (URLs, auth, errors) | Interface methods + types | Data structure only |
| Transport awareness | Yes (HTTP, AMQP, Kafka, etc.) | Minimal (framework assumes transport) | None |
| Examples | OpenAPI, AsyncAPI, WSDL, OData | Protobuf, Thrift, AIDL, FIDL | JSON Schema, XSD, Avro |
| Overlap | Contains schema for request/response | Implicitly defines an API contract | Used by API contracts |

**Stack Position:**
```
    IDL / Schema (Volumes 02/03)
           ↓
    ┌───────────────────────────────────┐
    │      API CONTRACT SYSTEMS         │  ← THIS VOLUME
    │  (describe full API surface)      │
    └───────────────────────────────────┘
           ↓
    Transport (HTTP/WebSocket/AMQP/Kafka)
```

---

## 08.1 — OpenAPI Specification (3.1)

### History

| Version | Year | Key Change |
|---------|------|-----------|
| Swagger 1.x | 2010 | JSON API description (Wordnik) |
| Swagger 2.0 | 2014 | Widely adopted, single JSON/YAML doc |
| OpenAPI 3.0 | 2017 | Renamed; components, links, callbacks |
| OpenAPI 3.1 | 2021 | Full JSON Schema 2020-12 alignment |

### Governance

OpenAPI Initiative (Linux Foundation) — Tier 3

### Document Structure (3.1)

| Top-Level Key | Purpose |
|--------------|---------|
| `openapi` | Version string (`"3.1.0"`) |
| `info` | Title, version, description, contact, license, termsOfService |
| `servers` | Base URLs with variables |
| `paths` | Path → HTTP method → operation |
| `webhooks` | (3.1 new) Incoming webhook definitions |
| `components` | Reusable schemas, responses, parameters, headers, securitySchemes, links, callbacks |
| `security` | Global security requirements |
| `tags` | Tag metadata for grouping |
| `externalDocs` | Link to external documentation |

### Annotated Example

```yaml
openapi: "3.1.0"
info:
  title: Vehicle Telemetry API
  version: "2.0.0"
  description: Real-time vehicle telemetry data access
  contact:
    name: Platform Team
    email: platform@example.com
  license:
    name: Apache 2.0
    identifier: Apache-2.0

servers:
  - url: https://api.vehicles.example.com/v2
    description: Production
  - url: https://staging-api.vehicles.example.com/v2
    description: Staging

paths:
  /vehicles/{vehicleId}/speed:
    get:
      operationId: getVehicleSpeed
      summary: Get current vehicle speed
      tags: [telemetry]
      parameters:
        - name: vehicleId
          in: path
          required: true
          schema:
            type: string
            pattern: "^[a-f0-9]{8}-[a-f0-9]{4}-4[a-f0-9]{3}-[89ab][a-f0-9]{3}-[a-f0-9]{12}$"
        - name: unit
          in: query
          required: false
          schema:
            type: string
            enum: [kmh, mph, ms]
            default: kmh
      responses:
        "200":
          description: Current speed reading
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/SpeedReading'
        "404":
          $ref: '#/components/responses/NotFound'
        "429":
          $ref: '#/components/responses/RateLimited'
      security:
        - bearerAuth: []

components:
  schemas:
    SpeedReading:
      type: object
      required: [value, unit, timestamp]
      properties:
        value:
          type: number
          minimum: 0
          maximum: 500
          description: Speed value in the requested unit
        unit:
          type: string
          enum: [kmh, mph, ms]
        timestamp:
          type: string
          format: date-time
        accuracy:
          type: ["number", "null"]   # 3.1 nullable syntax
          minimum: 0
          maximum: 100

  responses:
    NotFound:
      description: Resource not found
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
    RateLimited:
      description: Too many requests
      headers:
        Retry-After:
          schema:
            type: integer

  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
```

### OpenAPI 3.0 vs 3.1 Key Differences

| Feature | 3.0 | 3.1 |
|---------|-----|-----|
| Nullable | `nullable: true` | `type: ["string", "null"]` |
| Type | Single string | Array of strings |
| JSON Schema | Extended subset | Full 2020-12 |
| `$ref` siblings | Not allowed | Allowed (summary, description) |
| Webhooks | Not supported | `webhooks` top-level key |
| `if/then/else` | Not available | Available (JSON Schema) |

### Security Schemes

| Type | Description |
|------|------------|
| `apiKey` | API key in header, query, or cookie |
| `http` | HTTP auth (Bearer JWT, Basic) |
| `oauth2` | OAuth 2.0 flows (authorizationCode, implicit, clientCredentials, password) |
| `openIdConnect` | OpenID Connect Discovery |

### Tooling Ecosystem

| Category | Tools |
|----------|-------|
| Editors | Swagger Editor, Stoplight Studio, Postman |
| Validators | swagger-parser, ibm-openapi-validator, vacuum |
| SDK generators | openapi-generator (50+ languages), swagger-codegen |
| Documentation | Swagger UI, Redoc, Scalar, Elements |
| Mocking | Prism (Stoplight), Mockoon |
| Testing | Dredd, Schemathesis (property-based), REST-assured |
| Linting | Spectral (Stoplight), Redocly CLI |

---

## 08.2 — AsyncAPI 3.0

### Overview

| Property | Value |
|----------|-------|
| Purpose | API contract for event-driven and asynchronous messaging systems |
| History | 1.x (2017) → 2.0 (2020) → 2.6 (2022) → 3.0 (2023) |
| Governance | AsyncAPI Initiative (Linux Foundation) — Tier 3 |
| Format | YAML or JSON |

### Supported Protocols

AMQP 0.9.1/1.0, MQTT 3.1.1/5.0, Kafka, WebSocket, HTTP (SSE), JMS, NATS, Redis Streams, SNS/SQS, Mercure, Solace, Google Pub/Sub, Pulsar

### Document Structure (3.0)

| Key | Purpose |
|-----|---------|
| `asyncapi` | Version (`"3.0.0"`) |
| `info` | Title, version, description |
| `servers` | Broker connection details (protocol, host, bindings) |
| `channels` | Logical destinations (topics, queues) |
| `operations` | Send/receive actions on channels |
| `components` | Reusable schemas, messages, bindings |

### Key Concepts

| Concept | Description |
|---------|------------|
| Channel | Logical destination (topic, queue, exchange+routing key) |
| Message | Event/command definition (headers + payload schema) |
| Operation | `send` or `receive` action on a channel |
| Bindings | Protocol-specific options (Kafka partition key, MQTT QoS, AMQP exchange type) |

### Message Object

| Field | Purpose |
|-------|---------|
| `name` | Machine name |
| `title` | Human title |
| `contentType` | `application/json`, `application/avro`, etc. |
| `headers` | Header schema |
| `payload` | Data schema (JSON Schema, Avro, Protobuf) |
| `correlationId` | For request-reply patterns |
| `traits` | Reusable message trait refs |
| `bindings` | Protocol-specific (Kafka key, MQTT retain) |

---

## 08.3 — GraphQL as API Contract

### Schema as Contract

GraphQL's SDL (Schema Definition Language) serves as the API contract:
- **Single endpoint** — typically `/graphql`
- **Operations:** Query (read), Mutation (write), Subscription (real-time)
- **Type system** defines all possible requests and responses

### Introspection (Built-in Contract Discovery)

```graphql
{
  __schema {
    types { name kind }
    queryType { name }
    mutationType { name }
  }
  __type(name: "Vehicle") {
    fields { name type { name kind } }
  }
}
```

### Apollo Federation v2

Composes multiple GraphQL services into a single "supergraph" contract:

| Directive | Purpose |
|-----------|---------|
| `@key` | Entity primary key for cross-service resolution |
| `@shareable` | Field can be resolved by multiple subgraphs |
| `@external` | Field defined in another subgraph |
| `@provides` | Declares fields provided when resolving a parent type |
| `@requires` | Fields needed from other subgraphs to resolve |

### Breaking Change Detection

| Change Type | Breaking? |
|-------------|-----------|
| Remove field | Yes |
| Change field type | Yes |
| Add required argument | Yes |
| Add optional argument | No |
| Add field | No |
| Deprecate field | No |

### Governance Tools

- **GraphQL Inspector** — schema change detection, coverage analysis
- **graphql-schema-linter** — rules-based schema linting
- **Apollo Studio** — schema registry, breaking change CI checks

---

## 08.4 — WSDL (Web Services Description Language)

### Summary (Full coverage in Volume 02)

| Property | Value |
|----------|-------|
| Version | WSDL 1.1 (2001), WSDL 2.0 (2007 W3C Recommendation) |
| Governance | W3C — Tier 2 |
| Purpose | Complete API contract for SOAP services |

### WSDL as API Contract

| Section | Contract Role |
|---------|--------------|
| `types` (XSD) | Request/response data schema |
| `message` | Named typed parameters |
| `portType`/`interface` | Operations (API methods) |
| `binding` | Protocol mapping (SOAP/HTTP) |
| `service` | Network endpoint address |
| WS-Policy | Quality of service assertions |

### Status

Active in enterprise banking, healthcare (HL7 v2/v3), telecom. New projects use OpenAPI.

---

## 08.5 — JSON:API

| Property | Value |
|----------|-------|
| Specification | jsonapi.org v1.1 |
| Governance | Community — Tier 8 |
| Purpose | Standardized JSON response format for REST APIs |

### Response Structure

```json
{
  "data": {
    "type": "vehicles",
    "id": "abc-123",
    "attributes": {
      "make": "Toyota",
      "model": "Camry",
      "year": 2024
    },
    "relationships": {
      "owner": {
        "data": { "type": "users", "id": "user-456" },
        "links": { "related": "/users/user-456" }
      }
    },
    "links": { "self": "/vehicles/abc-123" }
  },
  "included": [
    {
      "type": "users",
      "id": "user-456",
      "attributes": { "name": "Alice" }
    }
  ],
  "meta": { "totalPages": 5 }
}
```

### Key Features

| Feature | Description |
|---------|------------|
| Sparse fieldsets | `?fields[vehicles]=make,model` |
| Sorting | `?sort=-year,make` |
| Pagination | `?page[number]=2&page[size]=25` |
| Filtering | `?filter[year]=2024` |
| Compound documents | `?include=owner,dealer` |

---

## 08.6 — HAL (Hypertext Application Language)

| Property | Value |
|----------|-------|
| Specification | draft-kelly-json-hal (IETF) |
| Governance | Tier 8 (community) |
| Purpose | HATEOAS (Level 3 REST) link representation |
| Variants | HAL-JSON, HAL-XML |

### Structure

```json
{
  "_links": {
    "self": { "href": "/vehicles/abc-123" },
    "owner": { "href": "/users/user-456" },
    "maintenance": { "href": "/vehicles/abc-123/maintenance" }
  },
  "_embedded": {
    "latestService": {
      "_links": { "self": { "href": "/services/svc-789" } },
      "date": "2024-03-15",
      "type": "oil_change"
    }
  },
  "make": "Toyota",
  "model": "Camry"
}
```

---

## 08.7 — OData (Open Data Protocol)

| Property | Value |
|----------|-------|
| Origin | Microsoft → OASIS standard |
| Governance | OASIS OData TC — Tier 3 |
| Version | OData 4.01 (latest) |
| Format | JSON (primary), AtomPub/XML |
| Metadata | EDMX/CSDL at `$metadata` endpoint |

### URL Query Conventions

| Parameter | Purpose | Example |
|-----------|---------|---------|
| `$filter` | Filter results | `$filter=year gt 2020` |
| `$select` | Choose fields | `$select=make,model` |
| `$expand` | Include related entities | `$expand=owner` |
| `$orderby` | Sort | `$orderby=year desc` |
| `$top` / `$skip` | Pagination | `$top=10&$skip=20` |
| `$count` | Include total count | `$count=true` |
| `$batch` | Batch multiple requests | POST to `$batch` |

### Key Users

Microsoft Power Platform, Azure DevOps, SharePoint Online, SAP OData services, Dynamics 365

---

## 08.8 — RAML (RESTful API Modeling Language)

| Property | Value |
|----------|-------|
| Origin | MuleSoft (2013) |
| Governance | Tier 6/7 |
| Format | YAML-based |
| Status | Largely superseded by OpenAPI in community adoption |

### Features

| Feature | Description |
|---------|------------|
| Traits | Reusable request/response patterns |
| Resource types | Template-based resource definitions |
| Types | RAML type system (with JSON Schema support) |
| Overlays | Non-functional metadata layered on top |
| Extensions | Functional extensions to base API |

---

## 08.9 — CloudEvents

| Property | Value |
|----------|-------|
| Specification | CNCF CloudEvents 1.0.2 |
| Governance | CNCF (graduated) — Tier 5 |
| Purpose | Standard envelope for event data across sources/sinks |

### Required Attributes

| Attribute | Type | Description |
|-----------|------|------------|
| `id` | String | Event identifier (unique per source) |
| `source` | URI-reference | Event origin |
| `specversion` | String | CloudEvents version (`"1.0"`) |
| `type` | String | Event type (e.g., `com.vehicle.speed.changed`) |

### Optional Attributes

| Attribute | Type | Description |
|-----------|------|------------|
| `datacontenttype` | String (RFC 2046) | `application/json`, `application/avro` |
| `dataschema` | URI | Schema for the `data` field |
| `subject` | String | Subject within source context |
| `time` | Timestamp (RFC 3339) | Event occurrence time |

### Transport Bindings

HTTP (structured/binary mode), Kafka (headers), AMQP (properties), MQTT (user properties), NATS (headers)

### Key Users

Knative Eventing, Argo Events, Azure Event Grid, AWS EventBridge, Google Eventarc

---

## 08.10 — TypeSpec (Microsoft)

| Property | Value |
|----------|-------|
| Origin | Microsoft (formerly "Cadl") |
| Governance | Microsoft / open-source — Tier 6 |
| Purpose | High-level API description language that compiles to multiple outputs |

### Language Features

| Feature | Description |
|---------|------------|
| `namespace` | Package scoping |
| `model` | Data structure definition |
| `interface` | Logical operation grouping |
| `op` | Operation (method) definition |
| Templates | Generic parameterized definitions |
| Decorators | `@route`, `@get`, `@post`, `@query`, `@path`, `@body` |

### Compilation Targets

| Emitter | Output |
|---------|--------|
| `@typespec/openapi3` | OpenAPI 3.0/3.1 YAML/JSON |
| `@typespec/json-schema` | JSON Schema |
| `@typespec/protobuf` | Protobuf .proto files |
| Custom emitters | Any format via plugin API |

### Value Proposition

Write once → emit OpenAPI + Protobuf + JSON Schema. Eliminates drift between REST and gRPC contracts.

---

## 08.11 — API Versioning Strategies

| Strategy | Format | Advantages | Disadvantages | Used By |
|----------|--------|-----------|---------------|---------|
| **URL path** | `/v1/resource` | Explicit, cacheable, easy routing | Breaks REST semantics, parallel maintenance | GitHub, Google Maps, Twitter |
| **Header** | `Accept: application/vnd.api.v2+json` | Clean URIs, RESTful | Less visible, harder to test | GitHub (also supports) |
| **Query param** | `/resource?version=2` | Simple implementation | Cache-unfriendly | Rarely recommended |
| **Date-based** | `API-Version: 2024-01-01` | API snapshot at date, gradual rollout | Complex to maintain many dates | Stripe, Azure |
| **Semantic** | `MAJOR.MINOR.PATCH` | Clear breaking change signal | Requires discipline | gRPC, AIDL |

### Deprecation Mechanisms

| Mechanism | Standard | Purpose |
|-----------|----------|---------|
| `deprecated: true` | OpenAPI | Mark operation/field as deprecated |
| `Sunset` header | RFC 8594 | HTTP header announcing removal date |
| `Deprecation` header | RFC 8594 | HTTP header with deprecation date |
| Schema evolution rules | Protobuf/Avro | Reserved fields, compatibility checks |

---

## 08.12 — API Contract Governance Patterns

### API-First Development

Contract written before implementation:
1. Design API contract (OpenAPI/AsyncAPI)
2. Review with consumers (breaking change check)
3. Generate server stubs + client SDKs
4. Implement business logic behind stubs
5. Contract tests validate implementation matches spec

### Linting & Validation

| Tool | Target | Rules |
|------|--------|-------|
| **Spectral** (Stoplight) | OpenAPI, AsyncAPI | Customizable rule sets (200+ built-in) |
| **Redocly CLI** | OpenAPI | Style guide enforcement |
| **vacuum** | OpenAPI | High-performance validator |
| **ibm-openapi-validator** | OpenAPI | IBM API design standards |

### Contract Testing

| Tool | Approach |
|------|----------|
| **Pact** | Consumer-driven contract testing (consumer defines expectations) |
| **Dredd** | Validates server responses against OpenAPI spec |
| **Schemathesis** | Property-based fuzzing against OpenAPI spec |
| **REST-assured** | Java DSL for API testing (can validate against OpenAPI) |

### Breaking Change Detection

| Tool | Target |
|------|--------|
| **oasdiff** | OpenAPI 3.x diff + breaking change detection |
| **openapi-diff** | OpenAPI comparison |
| **Buf breaking** | Protobuf breaking change rules |
| **GraphQL Inspector** | GraphQL schema breaking changes |
| **Apollo Studio** | Federation schema composition checks |

---

## 08.13 — API Contract System Comparison Matrix

| System | Protocol Style | Format | Service Definition | Schema | Code Gen | Streaming | Event-Driven | Governance Tier |
|--------|---------------|--------|-------------------|--------|---------|-----------|-------------|-----------------|
| **OpenAPI 3.1** | REST/HTTP | YAML/JSON | Yes (paths/ops) | JSON Schema 2020-12 | Yes (50+ langs) | Via WebSocket | Via webhooks | 3 |
| **AsyncAPI 3.0** | Async/Event | YAML/JSON | Yes (channels/ops) | JSON Schema/Avro/Proto | Yes | Native | Primary | 3 |
| **GraphQL SDL** | Graph query | SDL | Yes (query/mutation/sub) | GraphQL types | Yes | Subscriptions | Via subscription | 5 |
| **WSDL 2.0** | SOAP/REST | XML | Yes (interface) | XSD | Yes | No | No | 2 |
| **gRPC .proto** | RPC | Protobuf | Yes (service/rpc) | Protobuf | Yes (protoc) | 4 modes | Via streaming | 5/6 |
| **Smithy** | REST/RPC | Smithy IDL | Yes (service) | Smithy model | Yes | No | No | 6 |
| **CloudEvents** | Event envelope | JSON | No (envelope only) | External $ref | No | No | Primary | 5 |
| **JSON:API** | REST/JSON | JSON | Implicit | No formal | No | No | No | 8 |
| **HAL** | REST/JSON | JSON | Implicit | No formal | No | No | No | 8 |
| **OData** | REST/OData | JSON/XML | Yes ($metadata) | EDMX/CSDL | Yes | No | No | 3 |
| **RAML** | REST | YAML | Yes | RAML types | Yes | No | No | 7 |
| **TypeSpec** | REST+RPC | TypeSpec DSL | Yes | Yes | Yes (→OpenAPI/Proto) | No | No | 6 |

---

## 08.14 — Cross-References

| Topic | Volume |
|-------|--------|
| IDLs that define services (Protobuf, Thrift, Smithy) | Volume 02 — IDLs |
| Schema systems used by API contracts | Volume 03 — Schema Systems |
| RPC frameworks with built-in contracts | Volume 06 — RPC Frameworks |
| API governance and standards bodies | Volume 12 — Governance & Standards |
| Code generation from API contracts | Volume 13 — Tooling & Code Generation |
| Transport bindings for API protocols | Volume 14 — Transport & Runtime Binding |

---

*End of Volume 08 — API Contract Systems*
