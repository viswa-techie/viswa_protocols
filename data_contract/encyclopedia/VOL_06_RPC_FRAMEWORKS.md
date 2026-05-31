# VOLUME 06 — RPC FRAMEWORKS

## Universal Data Contract, Serialization, IDL & Interoperability Architecture Encyclopedia

---

## 06.0 — What is RPC (Formal Definition)

**Definition:** Remote Procedure Call (RPC) is a communication paradigm that allows a program to execute a procedure (function) in another address space — either on the same machine or across a network — as if it were a local function call, with the framework transparently handling serialization, transport, dispatch, and deserialization.

**Stack Position:**
```
    Schema / Serialization (Volume 03/04)
           ↓
    ┌───────────────────────────────────┐
    │         RPC FRAMEWORKS            │  ← THIS VOLUME
    │  (stub → serialize → transport    │
    │   → dispatch → execute → return)  │
    └───────────────────────────────────┘
           ↓
    Transport Layer (Volume 14)
```

**RPC vs IPC vs Messaging:**

| Dimension | RPC | IPC (Volume 07) | Messaging (Async) |
|-----------|-----|-----------------|-------------------|
| Paradigm | Request-response (synchronous-looking) | Shared memory, pipes, channels | Publish-subscribe, queue |
| Abstraction | Looks like a function call | OS-level communication primitives | Decoupled producer/consumer |
| Coupling | Tight (client knows server interface) | Medium (shared contract) | Loose (event schema only) |
| Blocking | Typically blocking (or async with futures) | Varies (blocking/non-blocking) | Non-blocking by design |
| Location | Cross-machine or same-machine | Same machine only | Cross-machine or same |
| Examples | gRPC, Thrift, SOAP | Binder, pipes, shared memory | Kafka, RabbitMQ, MQTT |

**RPC Framework Components:**
1. **IDL / Contract** — defines methods and types
2. **Code Generator** — produces client stubs and server skeletons
3. **Serialization** — encodes arguments and return values
4. **Transport** — carries encoded bytes (HTTP/2, TCP, etc.)
5. **Dispatcher** — routes incoming calls to the correct handler
6. **Error Handling** — structured error codes and propagation

---

## 06.1 — gRPC

### Overview

| Property | Value |
|----------|-------|
| Origin | Google internal "Stubby" (2001) → gRPC open-sourced (2015) |
| Governance | Google / CNCF (graduated project, 2017) — Tier 6/5 |
| IDL | Protocol Buffers .proto (service + rpc definitions) |
| Transport | HTTP/2 (mandatory); HTTP/3 (QUIC) experimental |
| Serialization | Protobuf (default); JSON (via grpc-gateway/transcoding) |
| License | Apache 2.0 |

### Four RPC Types

| Type | Client | Server | Use Case |
|------|--------|--------|----------|
| **Unary** | Single request | Single response | Standard API calls |
| **Server Streaming** | Single request | Stream of responses | Real-time feeds, logs |
| **Client Streaming** | Stream of requests | Single response | File upload, telemetry batching |
| **Bidirectional Streaming** | Stream of requests | Stream of responses | Chat, multiplayer, live sync |

### HTTP/2 Framing of gRPC

```
CLIENT                                         SERVER
  │                                              │
  │── HEADERS frame ────────────────────────────►│
  │   :method = POST                             │
  │   :path = /automotive.vehicle.v1.VehicleService/GetVehicle
  │   :scheme = https                            │
  │   content-type = application/grpc            │
  │   te = trailers                              │
  │   grpc-timeout = 5S                          │
  │                                              │
  │── DATA frame ───────────────────────────────►│
  │   [0x00][0x00 0x00 0x00 0x0F][protobuf bytes]│
  │    ↑compress  ↑4-byte length    ↑payload     │
  │                                              │
  │                                              │
  │◄──────────────────────── HEADERS frame ──────│
  │   :status = 200                              │
  │   content-type = application/grpc            │
  │                                              │
  │◄──────────────────────── DATA frame ─────────│
  │   [0x00][0x00 0x00 0x2A][protobuf bytes]     │
  │                                              │
  │◄──────────────────────── TRAILERS frame ─────│
  │   grpc-status = 0                            │
  │   grpc-message =                             │
  │                                              │
```

### gRPC Status Codes

| Code | Name | HTTP Equiv | Description |
|------|------|-----------|-------------|
| 0 | OK | 200 | Success |
| 1 | CANCELLED | 499 | Client cancelled |
| 2 | UNKNOWN | 500 | Unknown error |
| 3 | INVALID_ARGUMENT | 400 | Bad request |
| 4 | DEADLINE_EXCEEDED | 504 | Timeout |
| 5 | NOT_FOUND | 404 | Resource not found |
| 6 | ALREADY_EXISTS | 409 | Conflict |
| 7 | PERMISSION_DENIED | 403 | Forbidden |
| 8 | RESOURCE_EXHAUSTED | 429 | Rate limited |
| 9 | FAILED_PRECONDITION | 400 | Precondition failed |
| 10 | ABORTED | 409 | Concurrency conflict |
| 11 | OUT_OF_RANGE | 400 | Out of range |
| 12 | UNIMPLEMENTED | 501 | Not implemented |
| 13 | INTERNAL | 500 | Internal error |
| 14 | UNAVAILABLE | 503 | Service unavailable |
| 15 | DATA_LOSS | 500 | Unrecoverable data loss |
| 16 | UNAUTHENTICATED | 401 | Not authenticated |

### gRPC Ecosystem

| Component | Purpose |
|-----------|---------|
| **gRPC-Web** | Browser-compatible subset (no HTTP/2 trailers in browser) |
| **gRPC-Gateway** | REST/JSON ↔ gRPC/Protobuf transcoding reverse proxy |
| **gRPC-Binder** | gRPC transport over Android Binder IPC (same-device) |
| **gRPC Health** | Standard `grpc.health.v1.Health` protocol |
| **gRPC Reflection** | Runtime service introspection (like REST's Swagger UI) |
| **Interceptors** | Client/server middleware chain (logging, auth, tracing) |

---

## 06.2 — Apache Thrift RPC

### Architecture

```
┌──────────────────────────────────────┐
│          Generated Stubs             │
├──────────────────────────────────────┤
│          TProcessor                  │  Method dispatch
├──────────────────────────────────────┤
│          TProtocol                   │  Serialization
│  (Binary | Compact | JSON)           │
├──────────────────────────────────────┤
│          TTransport                  │  I/O
│  (Socket | Framed | HTTP | Buffered) │
├──────────────────────────────────────┤
│          TServer                     │  Threading model
│  (Simple | Threaded | NonBlocking)   │
└──────────────────────────────────────┘
```

### Transport Options

| Transport | Description |
|-----------|-------------|
| TSocket | Blocking TCP socket |
| TFramedTransport | Length-prefixed frames (required for non-blocking servers) |
| TBufferedTransport | Buffered I/O wrapper |
| THttpTransport | HTTP/HTTPS transport |
| TMemoryBuffer | In-memory buffer for testing |
| TZlibTransport | Compressed transport wrapper |

### Server Models

| Server | Model | Use Case |
|--------|-------|----------|
| TSimpleServer | Single-threaded, blocking | Testing only |
| TThreadedServer | One thread per connection | Low concurrency |
| TThreadPoolServer | Fixed thread pool | Bounded concurrency |
| TNonblockingServer | NIO/epoll event-driven | High concurrency |
| THsHaServer | Half-sync/half-async | Balanced load |

### Governance

| Aspect | Value |
|--------|-------|
| Body | Apache Software Foundation |
| Tier | 5 |
| License | Apache 2.0 |

---

## 06.3 — JSON-RPC

### Specification

JSON-RPC 2.0 (2010) — Tier 8 (Community standard)

### Protocol

**Request:**
```json
{
  "jsonrpc": "2.0",
  "method": "vehicle.getSpeed",
  "params": {"vehicleId": "abc-123"},
  "id": 1
}
```

**Success Response:**
```json
{
  "jsonrpc": "2.0",
  "result": {"speed": 85.5, "unit": "kmh"},
  "id": 1
}
```

**Error Response:**
```json
{
  "jsonrpc": "2.0",
  "error": {
    "code": -32601,
    "message": "Method not found",
    "data": {"method": "vehicle.getSpeed"}
  },
  "id": 1
}
```

**Notification (no response expected):**
```json
{
  "jsonrpc": "2.0",
  "method": "vehicle.telemetryEvent",
  "params": {"speed": 90.1, "lat": 48.85}
}
```

### Standard Error Codes

| Code | Message | Meaning |
|------|---------|---------|
| -32700 | Parse error | Invalid JSON |
| -32600 | Invalid Request | Not a valid JSON-RPC object |
| -32601 | Method not found | Method does not exist |
| -32602 | Invalid params | Invalid method parameters |
| -32603 | Internal error | Server-side error |
| -32000 to -32099 | Server error | Reserved for server implementation |

### Notable Users

- **Ethereum / Web3** — eth_getBalance, eth_sendTransaction, etc.
- **Language Server Protocol (LSP)** — VS Code language services
- **Neovim** — Remote plugin protocol (over msgpack-rpc, JSON-RPC variants)
- **Bitcoin Core** — JSON-RPC API for wallet/node interaction

---

## 06.4 — XML-RPC

| Property | Value |
|----------|-------|
| Specification | xmlrpc.com (1998) |
| Governance | Tier 8 (Community) |
| Transport | HTTP POST |
| Types | int, boolean, string, double, dateTime.iso8601, base64, struct, array |
| Status | Largely superseded by JSON-RPC and REST |
| Historical significance | Precursor to SOAP |

---

## 06.5 — SOAP (Simple Object Access Protocol)

### Overview

| Property | Value |
|----------|-------|
| Specification | SOAP 1.1 (W3C Note, 2000), SOAP 1.2 (W3C Recommendation, 2003) |
| Governance | W3C — Tier 2 |
| Transport | HTTP (primary), SMTP, JMS |
| Contract | WSDL (Web Services Description Language) |
| Serialization | XML with SOAP Envelope |

### Message Structure

```xml
<?xml version="1.0" encoding="UTF-8"?>
<soap:Envelope xmlns:soap="http://www.w3.org/2003/05/soap-envelope"
               xmlns:veh="http://automotive.example.com/vehicle">
  <soap:Header>
    <wsse:Security>
      <!-- WS-Security token -->
    </wsse:Security>
  </soap:Header>
  <soap:Body>
    <veh:GetVehicleRequest>
      <veh:VehicleId>abc-123</veh:VehicleId>
    </veh:GetVehicleRequest>
  </soap:Body>
</soap:Envelope>
```

### WS-* Extension Stack

| Specification | Purpose |
|--------------|---------|
| WS-Security | Authentication, integrity, encryption |
| WS-Addressing | Endpoint references, async correlation |
| WS-ReliableMessaging | Guaranteed delivery |
| WS-AtomicTransaction | Distributed transactions |
| WS-Policy | Declarative policy attachment |
| WS-MetadataExchange | Runtime WSDL/schema discovery |

---

## 06.6 — REST (Representational State Transfer)

### Definition

REST is an **architectural style** (Roy Fielding, 2000), NOT a protocol or framework. It has no IDL, no code generation, and no built-in RPC mechanism. OpenAPI provides the contract layer.

### REST Constraints

| Constraint | Meaning |
|-----------|---------|
| Client-Server | Separation of concerns |
| Stateless | Each request contains all info needed |
| Cacheable | Responses declare cacheability |
| Uniform Interface | Resources + representations + self-descriptive + HATEOAS |
| Layered System | Client can't tell if connected directly to server |
| Code on Demand (optional) | Server can extend client functionality |

### Richardson Maturity Model

| Level | Description | Example |
|-------|------------|---------|
| 0 | HTTP as tunnel | Single URI, POST everything (like SOAP) |
| 1 | Resources | Multiple URIs (`/vehicles/123`) |
| 2 | HTTP Verbs | GET=read, POST=create, PUT=update, DELETE=delete |
| 3 | HATEOAS | Response contains links to related actions |

### REST + OpenAPI as Contract

REST itself provides no formal contract. OpenAPI (Volume 08) fills this gap:
- Defines paths, operations, parameters, request/response bodies
- Enables code generation (client SDKs, server stubs)
- Provides machine-readable API documentation

---

## 06.7 — CORBA

| Property | Value |
|----------|-------|
| Specification | CORBA 3.4 (latest), original CORBA 1.1 (1991) |
| Governance | Object Management Group (OMG) — Tier 3 |
| IDL | OMG IDL |
| Transport | GIOP (General Inter-ORB Protocol) / IIOP (over TCP) |
| Serialization | CDR (Common Data Representation) |
| Object lifecycle | Portable Object Adapter (POA) |

### Key Architecture

```
Client                              Server
  │                                   │
  │ client_stub.getSpeed()            │
  │     ↓                             │
  │ ┌────────────┐                    │
  │ │Client Stub │ Marshal → CDR      │
  │ └────────────┘                    │
  │     ↓                             │
  │ ┌────────────┐    IIOP/TCP    ┌────────────┐
  │ │    ORB     │───────────────►│    ORB     │
  │ └────────────┘                └────────────┘
  │                                   │     ↓
  │                               ┌────────────┐
  │                               │   POA      │ Dispatch
  │                               └────────────┘
  │                                   │     ↓
  │                               ┌────────────┐
  │                               │  Servant   │ Business logic
  │                               └────────────┘
```

### Status

Largely replaced by gRPC, REST, and messaging in new systems. Still present in telecom (SS7/TMN), defense (DDS often co-exists with CORBA heritage), and some legacy enterprise systems.

---

## 06.8 — Java RMI

| Property | Value |
|----------|-------|
| Scope | Java-only RPC |
| Interface | `extends java.rmi.Remote`, methods throw `RemoteException` |
| Transport | JRMP (Java Remote Method Protocol) over TCP |
| Serialization | Java Object Serialization |
| Status | Legacy — replaced by gRPC/REST in modern Java |
| Security concern | Java deserialization vulnerabilities (CVE-2015-4852, etc.) |

---

## 06.9 — WCF / .NET Remoting

### WCF (Windows Communication Foundation)

| Component | Options |
|-----------|---------|
| **Bindings** | BasicHttpBinding, WSHttpBinding, NetTcpBinding, NetNamedPipeBinding, NetMsmqBinding |
| **Contract** | `[ServiceContract]`, `[OperationContract]`, `[DataContract]` |
| **Serialization** | DataContractSerializer (default), XmlSerializer |
| **Security** | WS-Security, transport TLS, Windows auth |

### Migration Path

Microsoft officially recommends migrating WCF services to:
- **gRPC** (for high-performance internal services)
- **ASP.NET Core Web API** (for REST/JSON APIs)

---

## 06.10 — Cap'n Proto RPC

| Property | Value |
|----------|-------|
| Key Innovation | **Promise pipelining** — call methods on not-yet-received results |
| Model | Object capabilities — interfaces passed as parameters |
| Transport | TCP, Unix domain sockets |
| Governance | Community — Tier 8 |

### Promise Pipelining

```
Traditional RPC (3 round trips):
  admin = await service.getAdmin()
  vehicle = await admin.getVehicle("abc")
  speed = await vehicle.getSpeed()

Cap'n Proto (1 round trip):
  admin = service.getAdmin()         // promise
  vehicle = admin.getVehicle("abc")  // pipelined call on promise
  speed = vehicle.getSpeed()         // pipelined call on promise
  await speed                        // single network round trip
```

---

## 06.11 — Apache Dubbo

| Property | Value |
|----------|-------|
| Origin | Alibaba (2011) → Apache (2018) |
| Governance | Apache Software Foundation — Tier 5 |
| IDL | Protobuf .proto (gRPC mode) or Java interface annotations |
| Protocols | Dubbo (binary TCP), Triple (HTTP/2, gRPC-compatible), REST |
| Service Registry | Zookeeper, Nacos, Consul, etcd |
| Load Balancing | Random, RoundRobin, LeastActive, ConsistentHash |
| Primary market | Chinese internet ecosystem (Alibaba, Didi, Dangdang) |

---

## 06.12 — ONC-RPC / XDR

| Property | Value |
|----------|-------|
| Origin | Sun Microsystems |
| Specification | RFC 5531 (ONC RPC), RFC 4506 (XDR encoding) |
| Governance | IETF — Tier 2 |
| IDL | XDR (External Data Representation) language |
| Compiler | `rpcgen` (generates C stubs) |
| Key Usage | **NFS** (Network File System), NIS, Unix RPC infrastructure |

### XDR Encoding Rules

- 4-byte alignment (all values padded to 4-byte boundaries)
- Big-endian (network byte order)
- Fixed-size: int (4B), unsigned int (4B), hyper (8B), float (4B), double (8B)
- Variable-size: length (4B) + data + padding to 4-byte boundary
- Strings: length (4B) + chars + padding

---

## 06.13 — GraphQL (as RPC-Equivalent)

While GraphQL is often classified as a query language, it functions as an RPC system:

| RPC Concept | GraphQL Equivalent |
|-------------|-------------------|
| Service definition | Schema (SDL) |
| Method call | Query / Mutation operation |
| Request parameters | Operation arguments |
| Response type | Return type |
| Streaming | Subscription (server → client) |
| Error handling | `errors` array in response |
| Code generation | graphql-codegen, Apollo |

### Key Differences from Traditional RPC

- **Client-driven selection:** Client specifies exactly which fields to return
- **Single endpoint:** All operations go to one URL (typically `/graphql`)
- **Batching:** Multiple operations in one request (reduces round trips)
- **No streaming uploads:** No client → server streaming equivalent

---

## 06.14 — RPC Comparison Matrix

| Framework | IDL | Serialization | Transport | Streaming | Bidirectional | Code Gen | Governance Tier | Primary Domain |
|-----------|-----|--------------|-----------|-----------|--------------|---------|-----------------|---------------|
| **gRPC** | Protobuf .proto | Protobuf | HTTP/2 | 4 types | Yes | Yes (protoc) | 6/5 | Cloud, Mobile, Auto |
| **Thrift** | Thrift IDL | Binary/Compact | TCP/HTTP | No | No | Yes | 5 | Enterprise legacy |
| **JSON-RPC** | None | JSON | HTTP/WS/TCP | No | No | No | 8 | Blockchain, LSP |
| **XML-RPC** | None | XML | HTTP | No | No | No | 8 | Legacy |
| **SOAP** | WSDL + XSD | XML | HTTP/SMTP | No | No | Yes | 2 | Enterprise, Finance |
| **REST** | OpenAPI (opt) | JSON/XML | HTTP | Via WebSocket | Via WebSocket | Yes (opt) | 8 | Web APIs |
| **CORBA** | OMG IDL | CDR | TCP (IIOP) | No | No | Yes | 3 | Telecom, Defense |
| **Java RMI** | Java interface | Java Serial | TCP (JRMP) | No | No | Yes | 7 | Java legacy |
| **WCF** | .NET attrs | DataContract | HTTP/TCP/Pipe | No | No | Yes | 6 | .NET enterprise |
| **Cap'n Proto** | .capnp | Cap'n Proto | TCP/Unix | Promises | Yes | Yes | 8 | Low-latency |
| **Dubbo** | Proto/.NET | Multi | TCP/HTTP/2 | Yes | Yes | Yes | 5 | Chinese internet |
| **ONC-RPC** | XDR | XDR | UDP/TCP | No | No | Yes (rpcgen) | 2 | NFS, Unix |
| **GraphQL** | SDL | JSON | HTTP/WS | Subscriptions | No | Yes | 5 | Web APIs |

---

## 06.15 — Cross-References

| Topic | Volume |
|-------|--------|
| IPC mechanisms (same-machine communication) | Volume 07 — IPC Mechanisms |
| IDLs that define RPC services | Volume 02 — IDLs |
| Serialization formats used by RPC frameworks | Volume 04 — Serialization |
| Transport protocols carrying RPC messages | Volume 14 — Transport & Runtime Binding |
| API contracts (OpenAPI, AsyncAPI) | Volume 08 — API Contract Systems |
| Code generation tooling for RPC | Volume 13 — Tooling & Code Generation |

---

*End of Volume 06 — RPC Frameworks*
