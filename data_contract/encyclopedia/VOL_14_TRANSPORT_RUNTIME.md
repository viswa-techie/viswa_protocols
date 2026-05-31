# VOLUME 14 — TRANSPORT LAYER & RUNTIME BINDING

## Universal Data Contract, Serialization, IDL & Interoperability Architecture Encyclopedia

---

## 14.0 — What the Transport Layer Provides and Does NOT Provide

### Definition

Transport = the mechanism that moves encoded bytes from one process or machine to another. It sits BELOW the RPC/IPC framework layer.

```
┌─────────────────────────────────┐
│  Application / Business Logic   │
├─────────────────────────────────┤
│  RPC / IPC Framework            │  ← Volume 06 (RPC), Volume 07 (IPC)
│  (gRPC, Thrift, AIDL, DDS)     │
├─────────────────────────────────┤
│  TRANSPORT LAYER                │  ← THIS VOLUME
│  (HTTP/2, TCP, UDP, Binder...)  │
├─────────────────────────────────┤
│  Network / Hardware / OS        │
└─────────────────────────────────┘
```

### What Transport PROVIDES

| Capability | Examples |
|-----------|----------|
| Byte stream / datagram delivery | TCP stream, UDP datagram |
| Connection establishment & teardown | TCP 3-way handshake, QUIC 1-RTT |
| Reliability (optional) | TCP retransmission, QUIC packet recovery |
| Ordering (optional) | TCP sequence numbers |
| Flow control (optional) | TCP sliding window, HTTP/2 per-stream flow |
| Security (optional) | TLS 1.3, DTLS, mTLS |
| Multiplexing (optional) | HTTP/2 streams, QUIC streams |

### What Transport Does NOT Provide

| NOT Provided | Responsibility Of |
|-------------|------------------|
| Message framing / serialization | Serialization layer (Vol 04, 05) |
| Service discovery | Service registry / DNS-SD / SOME/IP-SD |
| Method dispatch | RPC framework (Vol 06) |
| Schema evolution | Schema layer (Vol 10) |
| Load balancing | L4/L7 proxy (Envoy, HAProxy) |
| Retry / timeout policies | RPC framework or client middleware |

---

## 14.1 — HTTP/1.1

### RFC 9112 — Request-Response over TCP

| Property | Value |
|----------|-------|
| RFC | 9112 (HTTP/1.1 Message Syntax) |
| Transport | TCP (port 80 or 443 with TLS) |
| Model | Request-response, text-based headers |
| Connection | Persistent by default (keep-alive) |

### Request Format

```
GET /api/vehicles/123 HTTP/1.1
Host: fleet.example.com
Accept: application/json
Authorization: Bearer <token>
```

### Response Format

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 142
Transfer-Encoding: chunked

{"vin": "WBA12345...", "status": "active"}
```

### Key Features

| Feature | Description |
|---------|-------------|
| Keep-alive | Reuse TCP connection for multiple requests |
| Chunked encoding | Stream variable-length bodies (`Transfer-Encoding: chunked`) |
| Pipelining | Send multiple requests without waiting (rarely implemented) |
| Content negotiation | `Accept` / `Content-Type` headers |

### Limitations

| Problem | Impact |
|---------|--------|
| Head-of-line blocking | One request at a time per connection (pipelining broken in practice) |
| No multiplexing | Requires multiple TCP connections for parallelism |
| Text-based headers | Verbose, no compression |
| No server push | Server cannot proactively send resources |

### Use Cases

- REST APIs (OpenAPI/Swagger)
- SOAP/WSDL services (HTTP POST + SOAPAction header)
- Webhook delivery
- Legacy API integrations

---

## 14.2 — HTTP/2 (Binary Framing, Multiplexing, HPACK)

### RFC 9113

| Property | Value |
|----------|-------|
| RFC | 9113 (HTTP/2) |
| Transport | TCP (port 443 via ALPN "h2", or cleartext "h2c") |
| Framing | Binary frames (not text) |
| Multiplexing | Multiple streams over single TCP connection |

### Key Improvements Over HTTP/1.1

| Feature | Mechanism |
|---------|-----------|
| Stream multiplexing | Multiple concurrent request/response pairs on one connection |
| Binary framing | Structured frames replace text parsing |
| Header compression | HPACK (RFC 7541): static + dynamic table + Huffman encoding |
| Server push | Server sends resources before client requests (`PUSH_PROMISE`) |
| Flow control | Per-stream and per-connection `WINDOW_UPDATE` frames |
| Stream priority | `PRIORITY` frame (deprecated in favor of Extensible Priorities) |

### Frame Types

| Frame | Type ID | Purpose |
|-------|---------|---------|
| DATA | 0x0 | Request/response body |
| HEADERS | 0x1 | Request/response headers |
| PRIORITY | 0x2 | Stream dependency (deprecated) |
| RST_STREAM | 0x3 | Abort a stream |
| SETTINGS | 0x4 | Connection parameters |
| PUSH_PROMISE | 0x5 | Server push notification |
| PING | 0x6 | Connection liveness check |
| GOAWAY | 0x7 | Graceful connection shutdown |
| WINDOW_UPDATE | 0x8 | Flow control adjustment |
| CONTINUATION | 0x9 | Continuation of HEADERS |

### gRPC Over HTTP/2

```
Request:
  :method     = POST
  :path       = /package.ServiceName/MethodName
  :scheme     = https
  content-type = application/grpc+proto
  te           = trailers
  grpc-timeout = 5S

DATA Frame:
  [1 byte: compressed flag] [4 bytes: message length] [N bytes: protobuf payload]

Response Trailers:
  grpc-status  = 0
  grpc-message = (empty on success)
```

### ALPN Negotiation

| Protocol | ALPN Token |
|----------|-----------|
| HTTP/2 over TLS | `h2` |
| HTTP/1.1 over TLS | `http/1.1` |
| HTTP/2 cleartext | `h2c` (via Upgrade header or prior knowledge) |

---

## 14.3 — HTTP/3 / QUIC

### RFC 9000 (QUIC) + RFC 9114 (HTTP/3)

| Property | Value |
|----------|-------|
| Transport | UDP (QUIC provides reliability on top) |
| TLS | Built-in TLS 1.3 (mandatory, not optional) |
| Multiplexing | True stream independence (no HoL blocking) |
| Connection ID | Survives IP address changes (connection migration) |

### QUIC vs TCP

| Dimension | TCP | QUIC |
|-----------|-----|------|
| Head-of-line blocking | Yes (one lost packet blocks all) | No (per-stream recovery) |
| Handshake | 1-RTT (TCP) + 1-RTT (TLS) = 2 RTT | 1-RTT combined (or 0-RTT resumption) |
| Connection migration | Breaks on IP change | Survives (Connection ID-based) |
| Congestion control | Kernel-space (CUBIC/BBR) | User-space (flexible, per-connection) |
| Encryption | Optional (TLS layer) | Mandatory (TLS 1.3 integral) |

### Header Compression — QPACK (RFC 9204)

QPACK replaces HPACK for HTTP/3 to handle QUIC's out-of-order delivery. Uses separate encoder/decoder streams to maintain header table consistency.

### gRPC Over HTTP/3

- Experimental support in grpc-go, Envoy
- Same framing as HTTP/2 gRPC (5-byte prefix + protobuf)
- Benefits: reduced latency on lossy networks (mobile, satellite)

---

## 14.4 — TCP (Reliability Model, Congestion Control, Nagle)

### RFC 9293 — Transmission Control Protocol

| Property | Value |
|----------|-------|
| Model | Connection-oriented, reliable, ordered byte stream |
| Ports | 16-bit source + destination (0-65535) |
| Handshake | 3-way: SYN → SYN-ACK → ACK |
| Teardown | 4-way: FIN → ACK → FIN → ACK |

### Reliability Mechanisms

| Mechanism | Purpose |
|-----------|---------|
| Sequence numbers | Byte-level ordering |
| Acknowledgments | Confirm receipt |
| Retransmission timer | Resend lost segments |
| SACK (RFC 2018) | Selective acknowledgment of non-contiguous data |
| Timestamps (RFC 7323) | RTT measurement for timeout calculation |

### Flow Control

- **Receiver window**: Advertised in every ACK; limits sender's in-flight data
- **Window scaling** (RFC 7323): Allows windows > 64KB (scale factor in SYN options)

### Congestion Control

| Algorithm | Characteristics |
|-----------|----------------|
| Reno | Classic AIMD; slow start + congestion avoidance |
| CUBIC | Linux default; cubic function for window growth |
| BBR (Google) | Model-based; estimates bandwidth and RTT |
| DCTCP | Data center TCP; uses ECN for fine-grained control |

### Nagle Algorithm (RFC 896)

- Coalesces small writes: buffers data until previous segment ACKed
- **Disable for RPC**: `TCP_NODELAY` socket option
- gRPC always sets `TCP_NODELAY` (latency-critical)
- Interaction with delayed ACK: can cause 40ms delay if both enabled

### Key Socket Options for RPC

| Option | Purpose |
|--------|---------|
| `TCP_NODELAY` | Disable Nagle (low latency) |
| `SO_KEEPALIVE` | Detect dead connections |
| `TCP_QUICKACK` | Disable delayed ACK |
| `SO_REUSEADDR` | Allow port reuse after restart |
| `TCP_FASTOPEN` | Data in SYN (0-RTT for repeat connections) |

---

## 14.5 — UDP (Datagrams, Multicast)

### RFC 768

| Property | Value |
|----------|-------|
| Model | Connectionless, unreliable, unordered datagrams |
| Header | 8 bytes (src port, dst port, length, checksum) |
| Max payload | 65507 bytes (limited by IP packet size) |
| Guarantees | None (no reliability, no ordering, no flow control) |

### Multicast

| Property | Value |
|----------|-------|
| Address range | 224.0.0.0 – 239.255.255.255 |
| Join/Leave | `IP_ADD_MEMBERSHIP` / `IP_DROP_MEMBERSHIP` socket option |
| TTL | Controls multicast scope (1 = link-local) |
| IGMP | Internet Group Management Protocol (host ↔ router) |

### Use Cases

| Protocol | UDP Usage |
|----------|-----------|
| DDS RTPS | Multicast for discovery, unicast for data |
| SOME/IP-SD | Service discovery: multicast 224.0.0.1:30490 |
| SOME/IP events | Fire-and-forget notifications |
| CoAP | IoT application protocol (port 5683) |
| DNS | Name resolution (port 53) |
| SNMP | Network management (port 161/162) |
| QUIC | Reliable transport built on UDP |
| mDNS | Multicast DNS (224.0.0.251:5353) |

---

## 14.6 — Unix Domain Sockets

### Overview

| Property | Value |
|----------|-------|
| Address family | `AF_UNIX` (also `AF_LOCAL`) |
| Socket types | `SOCK_STREAM`, `SOCK_DGRAM`, `SOCK_SEQPACKET` |
| Addressing | Filesystem path or abstract namespace (`@name`) |
| Scope | Same host only (kernel-mediated) |

### Performance vs TCP Loopback

| Dimension | UDS | TCP loopback |
|-----------|-----|-------------|
| Throughput | ~2x TCP loopback | Baseline |
| Latency | ~50% lower | Baseline |
| Kernel overhead | No TCP/IP stack | Full TCP/IP processing |
| Zero-copy | Possible with `splice()` | No |

### Special Capabilities

| Capability | Mechanism |
|-----------|-----------|
| Credential passing | `SCM_CREDENTIALS` (PID, UID, GID — kernel-enforced) |
| File descriptor passing | `SCM_RIGHTS` (pass open FDs between processes) |
| Abstract namespace | `\0name` — no filesystem entry needed |

### Use Cases

| Service | Socket Path |
|---------|-------------|
| D-Bus system bus | `/var/run/dbus/system_bus_socket` |
| Docker daemon | `/var/run/docker.sock` |
| containerd | `/run/containerd/containerd.sock` |
| gRPC (local) | `unix:///tmp/grpc.sock` |
| systemd activation | File descriptor inheritance |

---

## 14.7 — Android Binder as Transport

### Architecture

| Property | Value |
|----------|-------|
| Type | Kernel driver (`/dev/binder`) |
| System call | `ioctl(fd, BINDER_WRITE_READ, &bwr)` |
| Copy count | 1 (sender → kernel mmap region readable by receiver) |
| Max transaction | 1MB per transaction (Binder buffer limit) |

### Copy Mechanism

```
Sender process:
  copy_from_user(kernel_buffer, user_buffer, size)

Receiver process:
  mmap(/dev/binder) → virtual address maps to same physical pages
  → receiver reads from mmap'd region (zero additional copy)
```

### FD Passing

Binder transfers file descriptors with ownership semantics — the kernel translates sender's FD number to a new FD in receiver's process.

### gRPC-Binder Transport

| Property | Value |
|----------|-------|
| Library | `grpc-binder` (part of gRPC Android) |
| Use case | gRPC between Android apps (same device, cross-process) |
| Binding | `IBinder` as transport primitive |
| Advantage | Avoids TCP/IP overhead for local communication |

---

## 14.8 — Mach Ports (macOS/iOS)

### XNU Kernel Mach IPC

| Property | Value |
|----------|-------|
| Primitive | Mach port (kernel-managed message queue) |
| System call | `mach_msg()` (send and/or receive) |
| Message | Header + inline data + out-of-line memory + port rights |
| Security | Port rights (send, receive, send-once) controlled by kernel |

### Message Structure

```c
typedef struct {
    mach_msg_header_t    header;    // 24 bytes
    mach_msg_body_t      body;
    mach_msg_ool_descriptor_t ool;  // out-of-line memory
    mach_msg_port_descriptor_t port; // port right transfer
} my_message_t;
```

### Out-of-Line Memory

- Large data transferred via copy-on-write page mapping
- Kernel maps sender's pages into receiver's address space
- Actual copy only occurs on write (COW fault)

### XPC (libxpc)

- High-level API built on Mach ports
- XPC services: launchd-managed, sandboxed helper processes
- Connection-oriented: `xpc_connection_create()`
- Serialization: XPC objects (dictionary, array, data, string, etc.)

---

## 14.9 — Zircon Channels (Fuchsia)

### Fundamental Primitives

| Property | Value |
|----------|-------|
| System calls | `zx_channel_write()`, `zx_channel_read()` |
| Message content | Bytes + handles (kernel object references) |
| Channel | Bidirectional, two-endpoint pipe |
| Capacity | Bounded (messages queue in kernel) |

### FIDL Transport

- All FIDL messages travel over Zircon channels
- No alternative transport — channels are THE IPC mechanism in Fuchsia
- Service discovery: component framework provides channel endpoints

### Handle Types Transferable

| Handle Type | Purpose |
|------------|---------|
| `ZX_OBJ_TYPE_CHANNEL` | Nested channels |
| `ZX_OBJ_TYPE_VMO` | Virtual Memory Object (shared memory) |
| `ZX_OBJ_TYPE_EVENT` | Signaling |
| `ZX_OBJ_TYPE_SOCKET` | Streaming data |

---

## 14.10 — Shared Memory as Transport

### POSIX Shared Memory

```c
// Producer
int fd = shm_open("/my_data", O_CREAT | O_RDWR, 0666);
ftruncate(fd, SIZE);
void *ptr = mmap(NULL, SIZE, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);

// Consumer (separate process)
int fd = shm_open("/my_data", O_RDONLY, 0);
void *ptr = mmap(NULL, SIZE, PROT_READ, MAP_SHARED, fd, 0);
```

### Iceoryx (Eclipse)

| Property | Value |
|----------|-------|
| Model | True zero-copy pub/sub |
| Mechanism | Shared memory segments with lock-free queues |
| Notification | `eventfd` / semaphore for subscriber wake-up |
| Latency | < 1 μs for intra-host message passing |
| Use case | ADAS, autonomous driving data pipelines |

### DDS Shared Memory Transport (FastDDS)

| Property | Value |
|----------|-------|
| Activation | `SHM` transport in participant QoS |
| Scope | Same-host only (falls back to UDP for remote) |
| Benefit | Avoids kernel network stack for large payloads |

### Android HIDL FMQ (Fast Message Queue)

| Property | Value |
|----------|-------|
| Mechanism | Shared memory ring buffer |
| Use case | HAL ↔ framework high-bandwidth data (audio, sensors) |
| Types | Synchronized (blocking) or Unsynchronized (non-blocking) |
| Performance | No Binder transaction per message |

### Android ION / DMABuf

| Property | Value |
|----------|-------|
| Mechanism | GPU/camera buffer sharing without copy |
| Use case | Camera → ISP → GPU → Display pipeline |
| Zero-copy | Same physical memory mapped into multiple processes |

### Synchronization Mechanisms

| Mechanism | Platform | Use Case |
|-----------|----------|----------|
| `pthread_mutex` (PROCESS_SHARED) | POSIX | General shared memory lock |
| `futex` | Linux | Fast user-space mutex |
| `sem_post` / `sem_wait` | POSIX | Counting semaphore |
| `eventfd` | Linux | Lightweight notification (Iceoryx) |
| Atomic operations | All | Lock-free ring buffers |

---

## 14.11 — Transport Binding in RPC Frameworks

### Thrift TTransport Abstraction

| Transport | Underlying | Purpose |
|-----------|-----------|---------|
| `TSocket` | TCP stream | Standard network transport |
| `TServerSocket` | TCP server | Accept incoming connections |
| `TFramedTransport` | 4-byte length prefix | Required for non-blocking servers |
| `TBufferedTransport` | User-space buffer | Write batching |
| `THttpTransport` | HTTP/1.1 POST | Firewall-friendly |
| `TMemoryBuffer` | In-process byte array | Testing / in-process calls |
| `TZlibTransport` | zlib compression | Bandwidth reduction |

### gRPC Transport Abstraction

| Transport | Mechanism | Use Case |
|-----------|-----------|----------|
| chttp2 | HTTP/2 over TCP | Standard gRPC (remote) |
| gRPC-Binder | Android Binder | Cross-app on same device |
| InProcess | Direct function call | Same-process testing |
| Cronet | QUIC + HTTP/2 | Android mobile (Google library) |
| UDS | Unix domain socket | Same-host container-to-container |

**TLS in gRPC:**
| Library | Usage |
|---------|-------|
| BoringSSL | Google's default (fork of OpenSSL) |
| OpenSSL | Community builds |
| mbedTLS | Embedded / IoT |

### DDS Transport Plugins

| Plugin | Protocol | Use Case |
|--------|----------|----------|
| UDP unicast | UDP | Default peer-to-peer |
| UDP multicast | UDP multicast | Many subscribers, efficient |
| TCP | TCP | WAN / firewall traversal |
| Shared Memory | SHM | Same-host large payloads |
| DTLS | UDP + TLS | DDS-Security secure transport |

### SOME/IP Transport Binding

| Message Type | Preferred Transport | Reason |
|-------------|-------------------|--------|
| Methods (request/response) | TCP or UDP | Reliability needed for methods |
| Events (fire-and-forget) | UDP | Low latency, acceptable loss |
| Service Discovery (SD) | UDP multicast 224.0.0.1:30490 | Broadcast discovery |
| Event groups (subscribe) | TCP | Reliable subscription acknowledgment |

---

## 14.12 — TLS as Transport Security

### TLS 1.3 (RFC 8446)

| Property | Value |
|----------|-------|
| Cipher suites | AEAD only (AES-128-GCM, AES-256-GCM, ChaCha20-Poly1305) |
| Key exchange | ECDHE (X25519, P-256) or FFDHE |
| Handshake | 1-RTT (full), 0-RTT (resumption — replay risk) |
| Certificate | X.509 (RSA or ECDSA) |

### TLS Handshake (1-RTT)

```
Client                              Server
  │─── ClientHello ──────────────────→│  (supported versions, cipher suites, key shares)
  │←── ServerHello ──────────────────│  (selected version, cipher, key share)
  │←── EncryptedExtensions ─────────│
  │←── Certificate ─────────────────│
  │←── CertificateVerify ──────────│
  │←── Finished ────────────────────│
  │─── Finished ──────────────────────→│
  │←─── Application Data ──────────→│  (encrypted from here)
```

### mTLS (Mutual TLS)

| Usage | Description |
|-------|-------------|
| gRPC service mesh | Istio/Envoy auto-inject mTLS between sidecars |
| Kubernetes API | Client certificate authentication |
| Zero-trust | Every service authenticates to every other service |
| Industrial | OPC UA Secure Channel uses mutual certificates |

### DTLS (RFC 6347 / RFC 9147)

- TLS semantics over UDP datagrams
- Handles packet loss, reordering, fragmentation
- Used by: CoAP+DTLS (IoT), WebRTC (DTLS-SRTP), DDS-Security

### ALPN (RFC 7301)

| Token | Protocol |
|-------|----------|
| `h2` | HTTP/2 |
| `http/1.1` | HTTP/1.1 |
| `h3` | HTTP/3 (in QUIC TLS) |
| `grpc-exp` | Experimental gRPC |

### SNI (RFC 6066)

- Client sends desired hostname in ClientHello (unencrypted)
- Server selects appropriate certificate
- Encrypted Client Hello (ECH) — RFC draft: encrypts SNI

---

## 14.13 — Transport Dependency Matrix

| Framework | HTTP/1.1 | HTTP/2 | HTTP/3 | TCP | UDP | UDS | Binder | Mach | Zircon | SHM | SOME/IP | DDS |
|-----------|----------|--------|--------|-----|-----|-----|--------|------|--------|-----|---------|-----|
| **gRPC** | — | Primary | Exp. | — | — | Yes | Yes | — | — | — | — | — |
| **REST/OpenAPI** | Primary | Yes | Yes | — | — | Rare | — | — | — | — | — | — |
| **SOAP/WSDL** | Primary | Yes | — | — | — | — | — | — | — | — | — | — |
| **Thrift** | Yes | — | — | Yes | — | Yes | — | — | — | — | — | — |
| **JSON-RPC** | Yes | Yes | — | Yes | — | Yes | — | — | — | — | — | — |
| **CORBA (IIOP)** | — | — | — | Yes | — | — | — | — | — | — | — | — |
| **DDS (RTPS)** | — | — | — | — | Primary | — | — | — | — | Yes | — | Primary |
| **SOME/IP** | — | — | — | Yes | Primary | — | — | — | — | — | Primary | — |
| **AIDL/Binder** | — | — | — | — | — | — | Primary | — | — | — | — | — |
| **FIDL** | — | — | — | — | — | — | — | — | Primary | — | — | — |
| **D-Bus** | — | — | — | — | — | Primary | — | — | — | — | — | — |
| **XPC** | — | — | — | — | — | — | — | Yes | — | — | — | — |
| **gRPC-Web** | Yes | Yes | — | — | — | — | — | — | — | — | — | — |
| **CoAP** | — | — | — | — | Primary | — | — | — | — | — | — | — |
| **MQTT** | — | — | — | Yes | — | — | — | — | — | — | — | — |
| **Iceoryx** | — | — | — | — | — | Yes | — | — | — | Primary | — | — |
| **OPC UA** | — | — | — | Yes | — | — | — | — | — | — | — | — |

**Legend:** Primary = canonical/default transport, Yes = supported, Exp. = experimental, — = not applicable

---

## 14.14 — Cross-References

| Topic | Volume |
|-------|--------|
| RPC frameworks that use these transports | Volume 06 — RPC Frameworks |
| IPC mechanisms (Binder, Mach, D-Bus internals) | Volume 07 — IPC Mechanisms |
| Zero-copy transport techniques | Volume 09 — Zero-Copy Systems |
| TLS certificate governance and PKI | Volume 12 — Governance |
| Tooling for transport configuration | Volume 13 — Tooling |
| Cross-platform interoperability over transports | Volume 15 — Interoperability |

---

*End of Volume 14 — Transport Layer & Runtime Binding*
