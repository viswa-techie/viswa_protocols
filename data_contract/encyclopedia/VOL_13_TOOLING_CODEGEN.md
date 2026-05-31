# VOLUME 13 — TOOLING & CODE GENERATION

## Universal Data Contract, Serialization, IDL & Interoperability Architecture Encyclopedia

---

## 13.0 — Why Tooling Quality Determines Adoption

The best-designed IDL or schema system fails in practice without quality tooling. Developer adoption correlates directly with:

| Tooling Dimension | Impact |
|------------------|--------|
| Code generation quality | Poor codegen = manual workarounds = abandoned IDL |
| Build system integration | No Maven/Gradle/Bazel plugin = friction at every build |
| Breaking change detection | No CI check = accidental breakage in production |
| IDE support | No autocomplete/validation = slow development |
| Error messages | Cryptic errors = hours of debugging |
| Documentation generation | No auto-docs = stale API documentation |

---

## 13.1 — Protobuf Toolchain (protoc + buf)

### protoc (Protocol Buffers Compiler)

| Property | Value |
|----------|-------|
| Input | `.proto` files |
| Output | Language-specific code via plugins |
| Plugin system | Executes `protoc-gen-X` binary via stdin/stdout |
| Core outputs | `--cpp_out`, `--java_out`, `--python_out` (built-in) |

**Key Flags:**
| Flag | Purpose |
|------|---------|
| `--proto_path` (`-I`) | Search path for imports |
| `--descriptor_set_out` | Emit binary FileDescriptorSet |
| `--include_imports` | Include imported protos in descriptor |
| `--X_out=path` | Invoke protoc-gen-X plugin |

### protoc Plugins

| Plugin | Output |
|--------|--------|
| `protoc-gen-go` | Go structs + marshal/unmarshal |
| `protoc-gen-go-grpc` | Go gRPC service stubs |
| `protoc-gen-grpc-java` | Java gRPC stubs |
| `protoc-gen-grpc-web` | Browser gRPC-Web client |
| `protoc-gen-ts` (protobuf-ts) | TypeScript with full types |
| `protoc-gen-rust` (prost) | Rust structs |
| `protoc-gen-validate` (PGV) | Field validation rules |
| `protoc-gen-doc` | Documentation (Markdown, HTML) |
| `protoc-gen-openapi` | .proto → OpenAPI 3.0 |
| `protoc-gen-grpc` | C++ gRPC stubs |

### buf (Modern Protobuf CLI)

| Command | Purpose |
|---------|---------|
| `buf lint` | Enforce style rules (naming, package structure) |
| `buf format` | Auto-format .proto files |
| `buf breaking` | Detect breaking changes vs baseline |
| `buf generate` | Run code generation (replaces protoc) |
| `buf build` | Compile to FileDescriptorSet |
| `buf push` | Push module to Buf Schema Registry (BSR) |
| `buf dep update` | Update module dependencies |

**Configuration:**
- `buf.yaml` — module definition (name, deps, lint rules, breaking rules)
- `buf.gen.yaml` — code generation pipeline (plugins + outputs)

### Build System Integration

| System | Plugin/Method |
|--------|-------------|
| Maven | `protobuf-maven-plugin` (xolstice/os72) |
| Gradle | `com.google.protobuf` plugin |
| Bazel | `rules_proto`, `rules_go`, `java_proto_library` |
| CMake | `FindProtobuf` module + `protobuf_generate_cpp()` |
| Cargo | `prost-build` in `build.rs` |

---

## 13.2 — Thrift Toolchain

### thrift Compiler

| Property | Value |
|----------|-------|
| Input | `.thrift` files |
| Generators | `--gen cpp`, `--gen java`, `--gen py`, `--gen go`, `--gen js`, `--gen rb` |
| Options | `--gen java:bean`, `--gen cpp:cob_style` |

### Build Integration

| System | Method |
|--------|--------|
| Maven | `libthrift-maven-plugin` |
| Gradle | Custom `exec {}` task |
| Bazel | Community rules (unofficial) |

---

## 13.3 — AIDL Toolchain (Android)

### aidl Compiler

| Property | Value |
|----------|-------|
| Input | `.aidl` files |
| Languages | `--lang=java` (default), `--lang=cpp`, `--lang=ndk`, `--lang=rust` |
| Stability | `--stability vintf` for stable AIDL verification |

### Android Build Integration

**Soong (Android.bp):**
```
aidl_interface {
    name: "android.hardware.vehicle",
    srcs: ["*.aidl"],
    stability: "vintf",
    versions: ["1", "2", "3"],
    backend: {
        java: { enabled: true },
        cpp: { enabled: true },
        ndk: { enabled: true },
        rust: { enabled: true },
    },
}
```

**Gradle (app-level):** AGP automatically compiles `.aidl` files in `src/main/aidl/`.

### API Freeze & Hash Verification

| Command | Purpose |
|---------|---------|
| `m check-api` | Verify AIDL API hasn't changed |
| `m update-api` | Record new API state |
| `aidl_freeze_api` | Create versioned snapshot |
| `.hash` file | SHA256 of all .aidl files for CI verification |

---

## 13.4 — FIDL Toolchain (Fuchsia)

### fidlc (FIDL Compiler)

| Property | Value |
|----------|-------|
| Input | `.fidl` files |
| Output | JSON IR (Intermediate Representation) |
| Build | Fuchsia's GN/Ninja; `fidl()` GN template |

### Backend Code Generators

| Generator | Output |
|-----------|--------|
| `fidlgen_cpp` | C++ (HLCPP and LLCPP) bindings |
| `fidlgen_rust` | Rust bindings |
| `fidlgen_go` | Go bindings |
| `fidlgen_dart` | Dart bindings |

---

## 13.5 — OpenAPI Toolchain

### openapi-generator

| Property | Value |
|----------|-------|
| Generators | 50+ (java, go, python, typescript-axios, kotlin, swift5, rust, etc.) |
| Server stubs | spring, python-fastapi, go-gin, aspnet, nodejs-express |
| Templates | Mustache (customizable per generator) |
| Command | `openapi-generator-cli generate -i spec.yaml -g java -o ./out` |

### Validation & Linting

| Tool | Purpose |
|------|---------|
| **Spectral** (Stoplight) | Rules-based OpenAPI/AsyncAPI linting |
| **vacuum** (quobix) | Fast OpenAPI linter (Go-based) |
| **ibm-openapi-validator** | IBM API design standards |
| **Redocly CLI** | Style guide enforcement |

### Breaking Change Detection

| Tool | Command |
|------|---------|
| **oasdiff** | `oasdiff breaking old.yaml new.yaml --fail-on-diff` |
| **openapi-diff** | Visual HTML diff |

### Mock & Test

| Tool | Purpose |
|------|---------|
| **Prism** (Stoplight) | Mock server from spec (`prism mock spec.yaml`) |
| **Schemathesis** | Property-based fuzzing from OpenAPI |
| **Dredd** | Validates server against spec |

### Documentation Renderers

Swagger UI, Redoc, Scalar, Elements

---

## 13.6 — Avro Toolchain

### avro-tools.jar

| Command | Purpose |
|---------|---------|
| `compile schema schema.avsc out/` | Generate Java from .avsc |
| `compile idl schema.avdl out/` | Compile .avdl → .avsc → Java |
| `tojson data.avro` | Convert Avro binary to JSON |
| `fromjson --schema-file s.avsc data.json` | JSON → Avro binary |

### Build Integration

| System | Plugin |
|--------|--------|
| Maven | `avro-maven-plugin` |
| Gradle | `avro-gradle-plugin` |
| Python | `avro` / `fastavro` libraries (runtime, no codegen needed) |

### Schema Registry CLI

| Tool | Purpose |
|------|---------|
| `kafka-avro-console-producer` | Produce Avro messages with schema registration |
| `kafka-avro-console-consumer` | Consume with automatic schema resolution |
| `curl` to Registry REST API | Register/retrieve/test compatibility |

---

## 13.7 — ASN.1 Compilers

| Tool | License | Languages | Encodings | Notes |
|------|---------|-----------|-----------|-------|
| **asn1c** (Lev Walkin) | BSD | C | BER/DER/PER/XER | Open source, widely used |
| **OSS Nokalva** | Commercial | C/C++/Java/C# | All | Full IDE + Studio |
| **Objsys ASN1C** | Commercial | C/C++/Java/C# | All | High performance |
| **pyasn1** | BSD | Python | BER/DER | Runtime (no codegen) |
| **asn1tools** | MIT | Python | BER/DER/PER/XER | Parse + encode/decode |
| **openssl asn1parse** | Apache 2.0 | CLI | DER/BER | Inspect encoded data |
| **dumpasn1** | Public domain | CLI | DER/BER | Detailed TLV dump |

---

## 13.8 — FlatBuffers Toolchain

### flatc Compiler

| Flag | Output |
|------|--------|
| `--cpp` | C++ headers |
| `--java` | Java classes |
| `--python` | Python classes |
| `--rust` | Rust structs |
| `--ts` | TypeScript |
| `--kotlin` | Kotlin |
| `--swift` | Swift |
| `--go` | Go |
| `--gen-mutable` | Mutable C++ accessors |
| `--json` | Binary → JSON conversion |

### Build Integration

| System | Method |
|--------|--------|
| CMake | `add_custom_command` invoking flatc |
| Bazel | `rules_flatbuffers` |
| Gradle | Custom `exec` task |

---

## 13.9 — Cap'n Proto Toolchain

### capnp Compiler

| Command | Purpose |
|---------|---------|
| `capnp compile -oc++ schema.capnp` | Generate C++ |
| `capnp compile -orust schema.capnp` | Generate Rust (capnproto-rust) |
| `capnp id` | Generate random 64-bit type ID |
| `capnp decode schema.capnp Type < data.bin` | Decode binary to text |

### Build Integration

| System | Method |
|--------|--------|
| CMake | `find_package(CapnProto)` + `capnp_generate_cpp()` |
| Bazel | `rules_capnproto` |
| Cargo | `capnpc` crate in `build.rs` |

---

## 13.10 — GraphQL Toolchain

### graphql-code-generator (The Guild)

| Plugin | Output |
|--------|--------|
| `@graphql-codegen/typescript` | TypeScript types |
| `@graphql-codegen/typescript-operations` | Operation types |
| `@graphql-codegen/typescript-react-apollo` | React hooks |
| `@graphql-codegen/typescript-resolvers` | Server resolvers |

**Config (codegen.yaml):**
```yaml
schema: "http://localhost:4000/graphql"
documents: "src/**/*.graphql"
generates:
  src/generated/types.ts:
    plugins:
      - typescript
      - typescript-operations
```

### Other Tools

| Tool | Purpose |
|------|---------|
| **GraphQL Inspector** | Breaking change detection, coverage |
| **graphql-schema-linter** | Schema style rules |
| **Apollo Studio** | Schema registry + composition checks |
| **Apollo Client Codegen** | iOS/Android typed clients |

---

## 13.11 — JSON Schema Toolchain

### Validators

| Tool | Language | Speed |
|------|----------|-------|
| **ajv** | JavaScript | Fastest (compiled) |
| **jsonschema** | Python | Standard |
| **networknt json-schema-validator** | Java | High performance |
| **Zod** / **Yup** | TypeScript | Runtime validation (not JSON Schema native) |

### Code Generation

| Tool | Input | Output |
|------|-------|--------|
| **quicktype** | JSON Schema / JSON samples | TypeScript, Go, Python, Rust, Java, C++ |
| **json-schema-to-typescript** | JSON Schema | TypeScript interfaces |
| **jsonschema2pojo** | JSON Schema | Java POJOs |
| **datamodel-code-generator** | JSON Schema | Python (Pydantic models) |

---

## 13.12 — Build System Integration Patterns

### Maven (Protobuf Example)

```xml
<plugin>
  <groupId>com.github.os72</groupId>
  <artifactId>protoc-jar-maven-plugin</artifactId>
  <executions>
    <execution>
      <phase>generate-sources</phase>
      <goals><goal>run</goal></goals>
      <configuration>
        <outputTargets>
          <outputTarget><type>java</type></outputTarget>
          <outputTarget><type>grpc-java</type></outputTarget>
        </outputTargets>
      </configuration>
    </execution>
  </executions>
</plugin>
```

### Gradle (Protobuf Example)

```groovy
plugins { id "com.google.protobuf" version "0.9.4" }
protobuf {
    protoc { artifact = "com.google.protobuf:protoc:4.26.1" }
    plugins { grpc { artifact = "io.grpc:protoc-gen-grpc-java:1.63.0" } }
    generateProtoTasks { all()*.plugins { grpc {} } }
}
```

### Bazel

```python
load("@rules_proto//proto:defs.bzl", "proto_library")
load("@io_grpc_grpc_java//:java_grpc_library.bzl", "java_grpc_library")

proto_library(name = "vehicle_proto", srcs = ["vehicle.proto"])
java_proto_library(name = "vehicle_java_proto", deps = [":vehicle_proto"])
java_grpc_library(name = "vehicle_grpc_java", srcs = [":vehicle_proto"])
```

### CMake

```cmake
find_package(Protobuf REQUIRED)
protobuf_generate_cpp(PROTO_SRCS PROTO_HDRS vehicle.proto)
add_library(vehicle_proto ${PROTO_SRCS} ${PROTO_HDRS})
target_link_libraries(vehicle_proto protobuf::libprotobuf)
```

### Cargo (Rust — prost-build)

```rust
// build.rs
fn main() {
    prost_build::compile_protos(&["src/vehicle.proto"], &["src/"]).unwrap();
}
```

---

## 13.13 — CI/CD Integration Patterns

### Protobuf Breaking Change Detection

```yaml
# GitHub Actions with buf
- name: Check breaking changes
  uses: bufbuild/buf-action@v1
  with:
    breaking_against: "https://github.com/org/repo.git#branch=main"
```

### OpenAPI Linting

```yaml
- name: Lint OpenAPI
  run: npx @stoplight/spectral-cli lint openapi.yaml --ruleset .spectral.yaml
```

### Schema Registry Compatibility Check

```bash
# Before Kafka deployment
curl -X POST -H "Content-Type: application/vnd.schemaregistry.v1+json" \
  --data '{"schema": "..."}' \
  http://registry:8081/compatibility/subjects/my-topic-value/versions/latest
```

### Consumer-Driven Contract Testing (Pact)

```
1. Consumer tests generate pact file (expected interactions)
2. Pact published to Pact Broker
3. Provider verifies against all consumer pacts
4. can-i-deploy: gates deployment on verified pacts
```

### AIDL API Check (Android AOSP)

```bash
m check-api    # Verify no unintended API changes
m update-api   # Record new API snapshot (intentional change)
```

---

## 13.14 — Toolchain Comparison Matrix

| Technology | Compiler | Languages | Build Systems | Breaking Detection | Registry | Linting |
|-----------|----------|-----------|--------------|-------------------|---------|---------|
| **Protobuf** | protoc / buf | All major | Maven/Gradle/Bazel/CMake/Cargo | buf breaking | BSR, Confluent | buf lint |
| **Thrift** | thrift | All major | Maven/Gradle | Manual | No | No |
| **AIDL** | aidl | Java/C++/Rust/Kotlin | Soong/Gradle | check-api | No | No |
| **FIDL** | fidlc + fidlgen | C++/Rust/Dart/Go | GN/Ninja | fidl_api_compat | No | fx lint |
| **OpenAPI** | openapi-generator | 50+ | All | oasdiff | Apicurio | Spectral |
| **GraphQL** | graphql-codegen | TS/Java/Kotlin/Swift | npm/Gradle | Inspector | Apollo | Linter |
| **Avro** | avro-tools | Java/Python/C++ | Maven/Gradle | Registry compat | Confluent | No |
| **FlatBuffers** | flatc | All major | CMake/Bazel/Gradle | Manual | No | No |
| **Cap'n Proto** | capnp | C++/Rust/Python | CMake/Bazel | Manual | No | No |
| **JSON Schema** | (validators) | via quicktype | npm/Maven | json-schema-diff | Apicurio | ajv |
| **ASN.1** | asn1c / commercial | C/C++/Java/C# | Make/CMake | Manual | No | No |
| **YANG** | pyang / yanglint | Python/C | Makefile | yangdiff | No | pyang |
| **AsyncAPI** | asyncapi-cli | TS/Python/Java | npm/Maven | asyncapi diff | Apicurio | validate |

---

## 13.15 — Cross-References

| Topic | Volume |
|-------|--------|
| IDL syntax and semantics for each technology | Volume 02 — IDLs |
| API contract specifications (OpenAPI, AsyncAPI) | Volume 08 — API Contracts |
| Schema evolution and compatibility checking | Volume 10 — Schema Evolution |
| Governance of tools and standards bodies | Volume 12 — Governance |
| Build system transport bindings | Volume 14 — Transport & Runtime |

---

*End of Volume 13 — Tooling & Code Generation*
