# CHAPTER 16 — DOMAIN: AI, ML, DATA EXCHANGE & ANALYTICS STANDARDS
# CHAPTER 17 — DOMAIN: SAFETY-CRITICAL, FUNCTIONAL SAFETY & RELIABILITY
# Prompt for: Universal Technology Standards Governance Encyclopedia
# Depends on: MASTER_PROMPT.md, Chapters 2, 5, 10, 11, 13

---

# CHAPTER 16: AI, ML, DATA EXCHANGE & ANALYTICS STANDARDS

## CHAPTER 16 SCOPE
- AI governance standards: ISO/IEC 42001, ISO/IEC 23894, ISO/IEC 42005, EU AI Act alignment
- ML model formats: ONNX, PMML, MLEAP, Safetensors, GGUF, TorchScript
- ML frameworks: TensorFlow (Google L6), PyTorch (Meta L6→LF), JAX (Google), scikit-learn
- AI benchmarking: MLCommons (MLPerf), AI2 ARC, MMLU, BIG-bench
- Data exchange formats: Apache Parquet, Apache Avro, Apache Arrow, ORC, Feather, HDF5, NetCDF
- Data serialization: JSON (RFC 8259), CBOR (RFC 8949), MessagePack, Protobuf, FlatBuffers
- Schema standards: JSON Schema 2020-12, Apache Avro Schema, Apache Arrow Schema, XML Schema
- Database query languages: SQL (ISO/IEC 9075), SPARQL (W3C), Cypher (Neo4j→openCypher), GraphQL
- Time series: InfluxDB Line Protocol, OpenTSDB, Prometheus exposition format
- Big data lineage: OpenLineage (Linux Foundation), Apache Atlas
- AI safety and ethics: IEEE 7000 series, ISO/IEC TR 24028, NIST AI RMF (AI 100-1)
- Federated learning: FATE, TensorFlow Federated (no formal standard yet)
- Generative AI and LLMs: OpenAI API (de-facto), Hugging Face model hub conventions

## GENERATION INSTRUCTIONS FOR CHAPTER 16

Act as a world-class AI systems architect and data standards specialist.
Generate **Chapter 16: AI, ML, Data Exchange & Analytics Standards**.

### SECTION 16.1 — AI GOVERNANCE STANDARDS LANDSCAPE

Explain:
- Why AI governance standardization is in early phases (2023–2026 is the formative period)
- Three tracks of AI standardization:
  1. **Technical standards**: model formats, interoperability, benchmarks
  2. **Governance/management standards**: AI management systems, risk management
  3. **Ethics and safety standards**: fairness, explainability, bias detection
- The regulatory pull: EU AI Act → creating demand for conformance to ISO/IEC 42001
- The international landscape: ISO/IEC JTC1/SC42, NIST AI RMF, EU AI Act, OECD AI Principles
- The "AI standards gap": most ML tools are de-facto (PyTorch, TensorFlow) with no formal standards

### SECTION 16.2 — AI MANAGEMENT SYSTEM STANDARDS

**ISO/IEC 42001:2023 (AI Management System):**

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | ISO/IEC 42001:2023 — Information Technology — AI — AI Management System |
| Entry Type                   | Management system standard (certifiable, like ISO 27001 for AI) |
| Governance Tier              | L1 (ISO/IEC JTC1/SC42)                            |
| Published                    | December 2023                                     |
| Structure                    | ISO High-Level Structure (Annex SL) — aligns with ISO 27001, ISO 9001 |
| Key clauses                  | Context (4), Leadership (5), Planning (6), Support (7), Operation (8), Performance evaluation (9), Improvement (10) |
| Annex B                      | AI risk controls catalog                          |
| Relationship to EU AI Act    | ISO 42001 certification supports but does not equal EU AI Act compliance |
| Relationship to ISO 27001    | Can be integrated into existing ISMS             |

**ISO/IEC 23894:2023 (AI Risk Management):**
- Risk management guidance for AI systems
- Supplements ISO 31000 (risk management) with AI-specific guidance
- Provides risk identification, analysis, and treatment for AI

**NIST AI RMF (AI Risk Management Framework) — AI 100-1:**
- NIST's voluntary framework for managing AI risks
- Four core functions: GOVERN, MAP, MEASURE, MANAGE
- AI RMF Playbook: implementation guidance for each function
- Used by US federal agencies and industry

**ISO/IEC SC42 Key Standards Pipeline:**

| Standard             | Title                                             | Status      |
|----------------------|---------------------------------------------------|-------------|
| ISO/IEC 42001:2023   | AI Management System                             | Published   |
| ISO/IEC 23894:2023   | AI Risk Management Guidance                      | Published   |
| ISO/IEC 24028:2020   | Overview of trustworthiness in AI                | Published   |
| ISO/IEC 24029-1      | Assessment of robustness of neural networks      | Published   |
| ISO/IEC 42006        | Requirements for AI governance frameworks        | In progress |
| ISO/IEC 5469         | Functional safety and AI                         | In progress |

### SECTION 16.3 — ML MODEL INTERCHANGE FORMATS

**ONNX (Open Neural Network Exchange):**

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Open Neural Network Exchange                     |
| Governance                   | ONNX community (Linux Foundation AI & Data)      |
| Governance Tier              | L5                                                |
| Founded                      | 2017 (Microsoft + Facebook/Meta co-founders)     |
| File format                  | Protobuf-serialized graph (`.onnx` file)         |
| Key features                 | Computation graph, operator set versioning, metadata |
| Supported frameworks         | PyTorch (export), TensorFlow (via tf2onnx), scikit-learn (sklearn-onnx) |
| Runtimes                     | ONNX Runtime (Microsoft), TensorRT (NVIDIA, via parser), OpenVINO (Intel) |
| Opset versions               | ONNX opset 1 to opset 21 (as of 2024)           |
| Limitations                  | Static graph only; dynamic models partially supported; no training semantics |

**PMML (Predictive Model Markup Language):**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Standard            | PMML 4.4.1 (DMG — Data Mining Group)             |
| Governance Tier     | L3                                                |
| Use case            | Classical ML model portability (decision trees, regression, SVM) |
| Limitation          | Does not support deep learning models            |

**Safetensors:**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Governance          | Hugging Face (L6)                                |
| Purpose             | Safe, fast tensor serialization for LLM weights  |
| Key advantage       | Zero-copy loading; prevents arbitrary code execution (vs. Pickle) |
| Typical use         | LLM weight storage: Llama, Mistral, Falcon, etc. |

**GGUF (GPT-Generated Unified Format):**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Governance          | llama.cpp project (Georgi Gerganov) — de-facto   |
| Purpose             | Quantized LLM storage + inference on CPU        |
| Replaces            | GGML (older format)                               |
| Metadata            | Contains model architecture, tokenizer, hyperparameters|

### SECTION 16.4 — ML FRAMEWORKS GOVERNANCE TABLE

| Framework    | Vendor          | Tier | License      | Foundation     | Key Use Case                 |
|--------------|-----------------|------|--------------|----------------|------------------------------|
| TensorFlow 2.x | Google        | L6   | Apache 2.0   | TF Foundation (Linux Fdn) | Production ML, mobile (TFLite) |
| PyTorch 2.x  | Meta            | L6→L5| BSD-3       | PyTorch Foundation (Linux Fdn) | Research, LLM training  |
| JAX          | Google          | L6   | Apache 2.0   | No foundation  | High-performance ML, TPU    |
| scikit-learn | Community       | L5   | BSD-3        | No foundation  | Classical ML, preprocessing  |
| XGBoost      | Community/DMLC  | L5   | Apache 2.0   | No foundation  | Gradient boosting, tabular  |
| LightGBM     | Microsoft       | L6   | MIT          | No foundation  | Fast gradient boosting       |
| Keras        | Google (François Chollet) | L6| Apache 2.0 | No foundation | High-level NN API          |
| MXNet        | Apache          | L5   | Apache 2.0   | Apache         | AWS deep learning (declining)|
| PaddlePaddle | Baidu           | L6   | Apache 2.0   | No neutral fdn | China ecosystem              |

### SECTION 16.5 — DATA EXCHANGE FORMATS

**Columnar Storage Formats:**

| Format      | Governance    | Tier | Encoding  | Schema Support | Key Use               |
|-------------|---------------|------|-----------|----------------|-----------------------|
| Apache Parquet| Apache ASF  | L5   | Columnar, compressed | Yes (embedded) | Data lake, analytics |
| Apache ORC  | Apache ASF    | L5   | Columnar, compressed | Yes (embedded) | Hive, Spark         |
| Apache Arrow| Apache ASF    | L5   | Columnar, in-memory | Yes (IPC)   | Query engines, pandas|
| HDF5        | HDF Group     | L5   | Hierarchical binary | Yes         | Scientific data       |
| NetCDF      | Unidata/UCAR  | L3/L5| Hierarchical array | Yes         | Geoscience, climate  |
| Feather     | Apache Arrow project | L5 | Arrow on disk | Yes       | R/Python exchange    |
| Delta Lake  | Linux Foundation | L5 | Parquet + transaction log | Yes | ACID data lakes  |
| Apache Iceberg| Apache ASF  | L5   | Table format (Parquet/ORC/Avro) | Yes | Open table format |
| Apache Hudi | Apache ASF    | L5   | Incremental processing | Yes     | Streaming data lake  |

**Schema and Serialization:**

| Format/Schema| Governance   | Tier | Wire Format | Schema Evolution | Key Use              |
|--------------|--------------|------|-------------|-----------------|----------------------|
| JSON         | IETF/ECMA    | L3/L8| UTF-8 text  | None (schema-less)| REST APIs, configs |
| JSON Schema  | json-schema.org | L8  | N/A (validation) | Via $id refs | API validation  |
| Apache Avro  | Apache ASF   | L5   | Binary compact | Yes (schema in message) | Kafka, data exchange |
| Protobuf     | Google       | L6   | Binary (varint) | Yes (field numbers) | gRPC, storage    |
| Apache Thrift| Apache ASF   | L5   | Binary/JSON | Yes (field IDs) | Legacy RPC, storage |
| MessagePack  | Community    | L8   | Binary JSON  | No (schema-less) | Compact JSON alt  |
| CBOR         | IETF         | L3   | Binary JSON  | No (schema-less) | IoT, constrained   |
| FlatBuffers  | Google       | L6   | Zero-copy binary | Yes (schema) | Games, embedded  |
| Cap'n Proto  | Community    | L8   | Zero-copy binary | Yes (schema) | High performance  |
| Bond         | Microsoft    | L6   | Binary       | Yes (schema)    | Azure internal     |

### SECTION 16.6 — AI BENCHMARKING STANDARDS

| Benchmark/Body | Governance      | What It Measures                               | Status     |
|----------------|-----------------|------------------------------------------------|------------|
| MLPerf (MLCommons) | MLCommons (L3) | Training and inference throughput/latency   | Active     |
| MLPerf Inference v4.0 | MLCommons | Edge and data center inference across hardware| Active   |
| MMLU           | Academic (UC Berkeley) | LLM knowledge across 57 subjects         | De-facto   |
| BIG-bench      | Academic community | LLM capability evaluation                  | De-facto   |
| HELM (Stanford)| Stanford        | Holistic LLM evaluation                       | De-facto   |
| GLUE / SuperGLUE| NYU/DeepMind   | NLP benchmark suite                           | Legacy/de-facto|
| HumanEval      | OpenAI          | Code generation benchmark                    | De-facto   |
| SWE-bench      | Academic        | Software engineering LLM evaluation          | Emerging   |

### SECTION 16.7 — DATA GOVERNANCE AND LINEAGE

**OpenLineage:**

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Full Name           | OpenLineage                                      |
| Governance          | Linux Foundation (sub-project)                   |
| Governance Tier     | L5                                                |
| Purpose             | Standard API for data lineage metadata collection |
| Key concept         | Run/Job/Dataset events with facets                |
| Integrations        | Apache Airflow, dbt, Spark, Flink, Marquez       |

**Data Catalog Standards:**
- Apache Atlas: data governance and metadata framework (Apache L5)
- OpenMetadata: community project (L5) for centralized metadata management
- DataHub: LinkedIn-origin → open source (L6→L5)

---

# CHAPTER 17: SAFETY-CRITICAL, FUNCTIONAL SAFETY & RELIABILITY STANDARDS

## CHAPTER 17 SCOPE
- IEC 61508: Functional safety root standard (all industries)
- ISO 26262: Automotive functional safety (ASIL A-D)
- DO-178C / ED-12C: Aviation software
- DO-254 / ED-80: Aviation hardware (FPGA/ASIC)
- EN 50128 / IEC 62279: Railway software
- IEC 61511: Process industry functional safety (SIL 1-4)
- IEC 62061: Machinery functional safety
- ISO 13849: Machinery safety (PLr a-e)
- ISO 25119: Agricultural machinery functional safety
- SOTIF (ISO 21448): Safety of intended functionality
- Reliability engineering: MIL-HDBK-217, IEC 60812, IEC 61025
- RAMS: Reliability, Availability, Maintainability, Safety analysis
- Formal methods: TLA+, Z notation, Frama-C, Coq, SPARK Ada
- SafetyCase: GSN (Goal Structuring Notation), CAE (Claim-Argument-Evidence)

## GENERATION INSTRUCTIONS FOR CHAPTER 17

Act as a world-class functional safety engineer and reliability standards specialist.
Generate **Chapter 17: Safety-Critical, Functional Safety & Reliability Standards**.

### SECTION 17.1 — FUNCTIONAL SAFETY STANDARDS HIERARCHY

```
IEC 61508:2010
(The "mother standard" — Functional Safety of E/E/PE Systems)
        ├─→ ISO 26262:2018 (Road Vehicles)
        ├─→ EN 50128:2011/IEC 62279 (Railway)
        ├─→ IEC 61511:2016 (Process Industry)
        ├─→ IEC 62061:2021 (Machinery)
        ├─→ DO-178C (Aviation SW — derived indirectly via philosophy)
        ├─→ ISO 25119:2010 (Agricultural Machinery)
        └─→ IEC 61513:2011 (Nuclear)
```

### SECTION 17.2 — IEC 61508 (ROOT FUNCTIONAL SAFETY STANDARD)

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Functional Safety of Electrical/Electronic/Programmable Electronic Safety-Related Systems |
| Standard Number              | IEC 61508:2010                                    |
| Governing Body               | IEC TC 65                                         |
| Governance Tier              | L1                                                |
| SIL Levels                   | SIL 1 (lowest), SIL 2, SIL 3, SIL 4 (highest)   |
| Key methodology              | Systematic and random hardware failure analysis   |
| PFD / PFH                    | Probability of Failure on Demand / per Hour       |
| Parts                        | 7 parts covering requirements for hardware, software, and process |

**IEC 61508 SIL Levels:**

| SIL  | PFD (low demand) | PFH (high demand) | Examples                              |
|------|------------------|--------------------|-----------------------------------------|
| SIL 1| 10⁻² to 10⁻¹    | 10⁻⁶ to 10⁻⁵      | Conveyor emergency stop, elevator      |
| SIL 2| 10⁻³ to 10⁻²    | 10⁻⁷ to 10⁻⁶      | Gas burner safety, pressure relief    |
| SIL 3| 10⁻⁴ to 10⁻³    | 10⁻⁸ to 10⁻⁷      | Nuclear reactor protection (non-safety grade)|
| SIL 4| 10⁻⁵ to 10⁻⁴    | 10⁻⁹ to 10⁻⁸      | Nuclear safety systems, spacecraft   |

### SECTION 17.3 — DO-178C (AVIATION SOFTWARE)

Full encyclopedia entry:

| Field                        | Value                                              |
|------------------------------|----------------------------------------------------|
| Full Name                    | Software Considerations in Airborne Systems and Equipment Certification |
| Standard Number              | DO-178C (RTCA), ED-12C (EUROCAE)                  |
| Governance Tier              | L4 (RTCA + EUROCAE professional associations)     |
| Regulatory basis             | FAA AC 20-115D mandates DO-178C; EASA CS-25 mandates ED-12C |
| Current version              | DO-178C (2011) — supersedes DO-178B (1992)        |
| Software levels              | Level A (catastrophic failure) to Level E (no safety effect) |

**DO-178C Software Levels:**

| Level | Failure Condition | Effect                       | Key Activities Required                |
|-------|-------------------|------------------------------|----------------------------------------|
| A     | Catastrophic      | Aircraft loss, fatalities    | MC/DC coverage, formal verification   |
| B     | Hazardous         | Serious injury               | Decision coverage, pair testing        |
| C     | Major             | Injury or workload increase  | Statement coverage                     |
| D     | Minor             | Slight reduction in safety   | Requirements-based testing             |
| E     | No safety effect  | No contribution to safety    | Development assurance not required     |

**MC/DC (Modified Condition/Decision Coverage):**
- Level A software MUST achieve MC/DC coverage
- Each condition in every decision independently affects the outcome
- This requirement forces systematic test case derivation

**DO-178C Companion Documents:**
- DO-330: Software Tool Qualification Considerations
- DO-331: Model-Based Development and Verification Supplement
- DO-332: Object-Oriented Technology and Related Techniques Supplement
- DO-333: Formal Methods Supplement

### SECTION 17.4 — EN 50128 / IEC 62279 (RAILWAY SOFTWARE)

| Field               | Value                                             |
|---------------------|---------------------------------------------------|
| Full Name           | Railway Applications — Communication, Signaling and Processing Systems — Software for Railway Control and Protection Systems |
| Standard            | EN 50128:2011+A1:2020 / IEC 62279                |
| SIL levels          | SIL 0–4 (same numbering as IEC 61508)            |
| Governing body      | CENELEC TC 9X (EU) / IEC TC 9 (international)   |
| Key domains         | Signaling, ETCS (European Train Control System), ATP/ATC |

### SECTION 17.5 — SAFETY CASE AND FORMAL METHODS

**Goal Structuring Notation (GSN):**
- Visual notation for constructing safety cases
- Elements: Goal, Strategy, Solution, Context, Assumption, Justification
- Used for arguing why a system is sufficiently safe (assurance case)
- Standard: No formal standard — GSN Community Standard v3 (from Software Engineering Institute)

**Formal Methods in Safety-Critical Systems:**

| Tool/Language | Description                                      | Certification Use              |
|---------------|--------------------------------------------------|--------------------------------|
| SPARK Ada     | Formally verified Ada subset                     | DO-178C Level A, EN 50128     |
| Frama-C       | C program static analysis + formal verification  | DO-178C, IEC 62304            |
| TLA+          | Temporal Logic of Actions (Leslie Lamport)       | Protocol verification (Amazon use)|
| Coq           | Proof assistant                                  | Academic/research; CompCert    |
| Isabelle/HOL  | Higher-order logic proof assistant               | Academic/research              |
| CompCert      | Formally verified C compiler                     | DO-178C Level A research       |
| Astrée        | Abstract interpretation-based static analyzer    | Airbus, Ariane rocket          |
| PolySpace      | Formal verification for C/C++ safety properties | DO-178C, ISO 26262             |

### SECTION 17.6 — SAFETY STANDARDS COMPARISON TABLE

| Standard        | Domain           | Tier | Integrity Levels | Root Standard | Regulatory Mandate          |
|-----------------|------------------|------|------------------|---------------|-----------------------------|
| IEC 61508:2010  | All E/E/PE       | L1   | SIL 1–4          | Root          | Referenced by most sectors  |
| ISO 26262:2018  | Automotive       | L1   | ASIL A–D         | IEC 61508     | Market (ISO required by OEMs)|
| DO-178C         | Aviation SW      | L4   | DAL A–E          | Derived       | FAA AC 20-115D, EASA        |
| DO-254          | Aviation HW (FPGA/ASIC)| L4 | DAL A–E       | Derived       | FAA AC 20-152A              |
| EN 50128        | Railway SW       | L1   | SIL 0–4          | IEC 61508     | EU ERA mandate              |
| IEC 61511:2016  | Process industry | L1   | SIL 1–3          | IEC 61508     | OSHA PSM, many national regs|
| IEC 62061:2021  | Machinery        | L1   | SIL 1–3          | IEC 61508     | EU Machinery Directive       |
| ISO 13849:2015  | Machinery        | L1   | PL a–e           | Parallel to 62061| EU Machinery Directive    |
| ISO 21448:2022  | Auto ADAS/AD     | L1   | N/A (risk-based) | Complements 26262| UNECE (in progress)      |

---

## OUTPUT FORMAT REQUIREMENTS

- SIL levels must use correct notation (SIL 1 not SIL-1)
- ASIL levels must use correct notation (ASIL-D or ASIL D — both acceptable; be consistent)
- DO-178C version must be C (not B — that's 1992; C is 2011)
- PFD/PFH values must use scientific notation (10⁻² not 0.01)
- ML format comparison tables must clearly distinguish schema from encoding from framework
- ONNX opset version must be current (21 as of 2024)
- Minimum length: Chapter 16 = 5,000 words; Chapter 17 = 4,000 words
