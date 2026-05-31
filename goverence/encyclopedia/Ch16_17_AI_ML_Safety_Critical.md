# Chapters 16 & 17 — AI/ML/Data Standards & Safety-Critical/Functional Safety Standards

## Universal Technology Standards Governance & Regulatory Architecture Encyclopedia
### First Edition, 2024–2026

---

# CHAPTER 16: AI, ML, DATA EXCHANGE & ANALYTICS STANDARDS

## 16.1 AI Governance Standards Landscape

### The AI Standards Gap (2024)

The AI/ML ecosystem represents the most fragmented governance landscape in technology today. Unlike networking (IETF/IEEE dominate) or automotive safety (ISO 26262 is mandatory), AI standardization is in its formative period (2023–2026), with three simultaneous and largely uncoordinated tracks:

```
Track 1: TECHNICAL STANDARDS (model formats, benchmarks, interoperability)
    │  ONNX, PMML, Safetensors, GGUF, MLPerf
    │  Status: Mostly de-facto (L6–L8), few formal standards
    ▼
Track 2: GOVERNANCE/MANAGEMENT STANDARDS (risk, management systems)
    │  ISO/IEC 42001, NIST AI RMF, ISO/IEC 23894
    │  Status: Published but early adoption (L1–L2)
    ▼
Track 3: ETHICS & SAFETY STANDARDS (fairness, explainability, bias)
    │  IEEE 7000 series, ISO/IEC TR 24028, EU AI Act obligations
    │  Status: Framework stage, limited testability
```

**Key observation:** Most ML tools (PyTorch, TensorFlow, Hugging Face) are de-facto standards (L6–L8) with NO formal governance. The EU AI Act is creating regulatory demand for formal standards that largely do not yet exist, forcing ISO/IEC SC42 to accelerate publication.

### International AI Governance Bodies

| Body           | Geography | Output Type               | Key Publications                  |
|----------------|-----------|---------------------------|-----------------------------------|
| ISO/IEC JTC1 SC42 | Global | International Standards  | 42001, 23894, 24028, 24029      |
| NIST           | USA       | Framework, guidance        | AI RMF (AI 100-1), AI 600-1     |
| EU (via CEN/CENELEC) | EU  | Harmonized standards      | AI Act implementing standards    |
| OECD           | Global    | Policy principles          | OECD AI Principles (2019, updated 2024) |
| IEEE SA        | Global    | Ethics standards           | IEEE 7000 series                 |
| MLCommons      | Global    | Benchmarks                 | MLPerf Training, Inference       |
| Partnership on AI | Global | Best practices             | Voluntary industry consortium    |

---

## 16.2 AI Management System Standards

### ISO/IEC 42001:2023 — AI Management System

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Information Technology — Artificial Intelligence — AI Management System |
| Standard Number              | ISO/IEC 42001:2023                                            |
| Entry Type                   | Certifiable management system standard                        |
| Governance Tier              | L1 (ISO/IEC JTC1/SC42)                                       |
| Published                    | December 2023                                                 |
| Structure                    | ISO High-Level Structure (Annex SL) — harmonized with ISO 27001/9001 |
| Certification                | Third-party accredited certification (like ISO 27001)        |
| Annex A                      | Reference control objectives for responsible AI              |
| Annex B                      | Implementation guidance for AI-specific controls             |

**Key Clauses:**

| Clause | Title                    | AI-Specific Content                           |
|--------|--------------------------|-----------------------------------------------|
| 4      | Context of Organization  | AI stakeholder identification, AI system scope |
| 5      | Leadership               | AI policy, responsibility assignment          |
| 6      | Planning                 | AI risk assessment, opportunities             |
| 7      | Support                  | AI competence, awareness, data management     |
| 8      | Operation                | AI system lifecycle, impact assessment        |
| 9      | Performance Evaluation   | Monitoring AI system outputs, internal audit  |
| 10     | Improvement              | Nonconformity, continual improvement          |

### NIST AI Risk Management Framework (AI 100-1)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Artificial Intelligence Risk Management Framework             |
| Document                     | AI 100-1 (January 2023)                                      |
| Governance Tier              | L2 (NIST — US government guidance)                           |
| Mandatory?                   | Voluntary (but referenced by EO 14110 and federal acquisitions)|
| Companion                    | NIST AI RMF Playbook (implementation guidance)                |
| Companion (Generative AI)    | NIST AI 600-1: GenAI Profile (July 2024)                    |

**Four Core Functions:**

| Function | Purpose                                          | Key Activities                    |
|----------|--------------------------------------------------|-----------------------------------|
| GOVERN   | Cultivate culture of risk management             | Policies, roles, accountability   |
| MAP      | Contextualize risks within mission/use case      | Identify stakeholders, map impacts|
| MEASURE  | Assess, analyze, and track AI risks              | Metrics, testing, evaluation      |
| MANAGE   | Prioritize and respond to identified risks       | Mitigation, monitoring, incident response |

### ISO/IEC SC42 Standards Pipeline

| Standard             | Title                                             | Status (2024)  |
|----------------------|---------------------------------------------------|----------------|
| ISO/IEC 42001:2023   | AI Management System                             | Published       |
| ISO/IEC 23894:2023   | AI Risk Management Guidance                      | Published       |
| ISO/IEC 24028:2020   | Overview of Trustworthiness in AI                | Published       |
| ISO/IEC 24029-1:2021 | Assessment of Robustness of Neural Networks      | Published       |
| ISO/IEC 24029-2:2023 | Methodology for formal robustness verification   | Published       |
| ISO/IEC 25059:2023   | Quality model for AI systems                     | Published       |
| ISO/IEC 42005       | AI System Impact Assessment                       | In progress     |
| ISO/IEC 42006       | Requirements for Bodies Certifying AI Management  | In progress     |
| ISO/IEC 5469        | Functional Safety and AI                          | In progress     |
| ISO/IEC 12791       | Treatment of Unwanted Bias in Classification     | In progress     |

---

## 16.3 EU AI Act — Risk Classification

| Risk Category | Obligation                          | Examples                             |
|---------------|--------------------------------------|--------------------------------------|
| Unacceptable  | **Prohibited** (banned in EU)       | Social scoring, real-time biometric ID in public (with exceptions), manipulative AI |
| High-Risk     | Conformity assessment, registration, monitoring | CV screening, credit scoring, autonomous vehicles, medical devices |
| Limited Risk  | Transparency obligations            | Chatbots (must disclose), deepfakes (must label) |
| Minimal Risk  | No specific obligations             | Spam filters, video game AI          |

**Relationship to ISO 42001:** ISO/IEC 42001 certification can support (but does NOT automatically satisfy) EU AI Act high-risk system requirements. CEN/CENELEC is developing harmonized standards that reference ISO/IEC SC42 work.

---

## 16.4 ML Model Interchange Formats

### ONNX (Open Neural Network Exchange)

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Open Neural Network Exchange                                  |
| Governance                   | ONNX Community / Linux Foundation AI & Data                   |
| Governance Tier              | L5                                                            |
| Founded                      | 2017 (Microsoft + Facebook/Meta)                              |
| File Format                  | Protobuf-serialized computation graph (`.onnx`)               |
| Current Opset                | Opset 21 (2024)                                               |
| Supported Frameworks (export)| PyTorch (torch.onnx.export), TensorFlow (tf2onnx), scikit-learn (skl2onnx) |
| Runtimes                     | ONNX Runtime (Microsoft), TensorRT (NVIDIA), OpenVINO (Intel), CoreML (Apple via converter) |
| Limitations                  | Static graph focus; dynamic shapes partially supported; no training primitives |

### Model Format Comparison

| Format       | Governance      | Tier | Use Case                       | Schema? | Deep Learning? | Classical ML? |
|--------------|-----------------|------|--------------------------------|---------|----------------|---------------|
| ONNX         | LF AI & Data    | L5   | Cross-framework inference      | Yes     | **Yes**        | Partial       |
| PMML 4.4.1   | DMG             | L3   | Classical ML portability        | Yes (XML)| No            | **Yes**       |
| Safetensors  | Hugging Face    | L6   | LLM weight storage (safe)      | Metadata | Storage only  | No            |
| GGUF         | llama.cpp       | L8   | Quantized LLM for CPU inference| Yes     | LLM only      | No            |
| TorchScript  | Meta/PyTorch    | L6   | PyTorch model serialization    | No      | Yes           | No            |
| SavedModel   | Google/TensorFlow| L6  | TF model serving               | Yes     | Yes           | No            |
| CoreML       | Apple           | L7   | Apple device inference          | Yes     | Yes           | Yes           |
| TFLite       | Google          | L6   | Mobile/edge inference           | FlatBuffers | Yes       | Partial       |

---

## 16.5 ML Frameworks Governance

| Framework      | Primary Vendor | Tier | License    | Foundation           | Primary Use Case             |
|----------------|----------------|------|------------|----------------------|------------------------------|
| PyTorch 2.x    | Meta           | L6→L5| BSD-3     | PyTorch Foundation (LF) | Research → production     |
| TensorFlow 2.x | Google        | L6   | Apache 2.0 | TF Foundation (LF)  | Production ML, mobile (TFLite)|
| JAX            | Google         | L6   | Apache 2.0 | No foundation        | High-perf ML, TPU optimization|
| scikit-learn   | Community      | L5   | BSD-3      | No foundation        | Classical ML, preprocessing  |
| XGBoost        | Community/DMLC | L5   | Apache 2.0 | No foundation        | Gradient boosting, tabular   |
| LightGBM       | Microsoft      | L6   | MIT        | No foundation        | Fast gradient boosting       |
| Keras 3        | Google (Chollet)| L6  | Apache 2.0 | No foundation        | Multi-backend NN API         |
| MXNet          | Apache ASF     | L5   | Apache 2.0 | Apache               | AWS deep learning (declining)|
| PaddlePaddle   | Baidu          | L6   | Apache 2.0 | No neutral foundation| China AI ecosystem           |
| Hugging Face Transformers | Hugging Face | L6 | Apache 2.0 | No foundation | LLM inference + fine-tuning |

---

## 16.6 Data Exchange Formats

### Columnar Storage Formats

| Format          | Governance  | Tier | Storage    | Schema       | Best For                    |
|-----------------|-------------|------|------------|--------------|------------------------------|
| Apache Parquet  | Apache ASF  | L5   | Columnar, compressed | Embedded (Thrift) | Data lakes, analytics |
| Apache ORC      | Apache ASF  | L5   | Columnar, compressed | Embedded   | Hive ecosystem              |
| Apache Arrow    | Apache ASF  | L5   | Columnar, in-memory  | IPC        | Zero-copy query engines     |
| HDF5            | HDF Group   | L5   | Hierarchical binary  | Self-describing | Scientific computing   |
| NetCDF          | Unidata     | L5   | Array-oriented       | Self-describing | Climate, geoscience   |

### Open Table Formats (Lakehouse)

| Format         | Governance    | Tier | Base Format     | ACID? | Time Travel? | Key Feature        |
|----------------|---------------|------|-----------------|-------|--------------|---------------------|
| Delta Lake     | Linux Foundation | L5 | Parquet + JSON log | Yes | Yes        | Databricks-origin   |
| Apache Iceberg | Apache ASF    | L5   | Parquet/ORC/Avro | Yes  | Yes          | Schema evolution    |
| Apache Hudi    | Apache ASF    | L5   | Parquet          | Yes   | Yes          | Incremental ingestion|

### Serialization Format Comparison

| Format       | Governance | Tier | Binary? | Schema Required? | Human Readable? | Zero-Copy? | Best For           |
|--------------|------------|------|---------|------------------|-----------------|------------|---------------------|
| JSON         | IETF/ECMA  | L3/L8| No     | No               | Yes             | No         | REST APIs, config   |
| Protobuf     | Google     | L6   | Yes     | Yes (.proto)     | No              | No         | gRPC, storage       |
| Apache Avro  | Apache     | L5   | Yes     | Yes (JSON schema)| No              | No         | Kafka, data exchange|
| FlatBuffers  | Google     | L6   | Yes     | Yes (.fbs)       | No              | **Yes**    | Games, mobile, embedded |
| MessagePack  | Community  | L8   | Yes     | No               | No              | No         | Compact JSON replacement |
| CBOR         | IETF       | L3   | Yes     | No               | No              | No         | IoT, constrained devices |
| Cap'n Proto  | Community  | L8   | Yes     | Yes (.capnp)     | No              | **Yes**    | High-performance IPC |

---

## 16.7 Database Query Language Standards

| Language   | Standard          | Governance | Tier | Data Model    | Status            |
|------------|-------------------|------------|------|---------------|-------------------|
| SQL        | ISO/IEC 9075:2023 | ISO (L1)   | L1   | Relational    | Universal standard|
| SPARQL 1.1 | W3C Recommendation| W3C (L3)  | L3   | RDF (graph)   | Current           |
| Cypher     | openCypher        | Neo4j→community | L6→L8 | Property graph | Growing     |
| GQL        | ISO/IEC 39075     | ISO (L1)   | L1   | Property graph | Published 2024  |
| GraphQL    | GraphQL Foundation| LF (L5)    | L5   | API query     | Current           |
| PartiQL    | Amazon            | L6         | L6   | Multi-model   | Open-sourced      |

---

## 16.8 AI Benchmarking Standards

| Benchmark          | Governance       | Tier | What It Measures                    | Status      |
|--------------------|------------------|------|-------------------------------------|-------------|
| MLPerf Training    | MLCommons        | L3   | Training throughput across models   | Active (v4.0)|
| MLPerf Inference   | MLCommons        | L3   | Inference latency/throughput        | Active (v4.1)|
| MLPerf Tiny        | MLCommons        | L3   | Edge/MCU ML inference               | Active      |
| MMLU               | Academic (UCB)   | L8   | LLM knowledge (57 subjects)        | De-facto    |
| MMLU-Pro           | Academic         | L8   | Harder MMLU variant                 | De-facto    |
| BIG-bench          | Community        | L8   | LLM capability evaluation (200+ tasks) | De-facto |
| HELM               | Stanford         | L8   | Holistic LLM evaluation framework  | De-facto    |
| HumanEval          | OpenAI           | L8   | Code generation (Python)            | De-facto    |
| SWE-bench          | Academic         | L8   | Real-world software engineering tasks| Emerging   |
| MT-Bench           | LMSYS            | L8   | Multi-turn conversation quality     | De-facto    |
| Chatbot Arena (ELO)| LMSYS            | L8   | Human preference ranking            | De-facto    |

---

## 16.9 Data Governance and Lineage

### OpenLineage

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | OpenLineage                                                   |
| Governance                   | Linux Foundation (AI & Data)                                  |
| Governance Tier              | L5                                                            |
| Purpose                      | Standard API for collecting data lineage metadata             |
| Core Concepts                | Run (execution instance), Job (process definition), Dataset (input/output) |
| Integrations                 | Apache Airflow, Apache Spark, dbt, Flink, Dagster, Marquez   |

### Data Catalog/Metadata Standards

| Project/Standard | Governance       | Purpose                              | Status       |
|------------------|------------------|--------------------------------------|--------------|
| OpenLineage      | LF AI & Data     | Lineage event collection             | Active       |
| OpenMetadata     | Community (L5)   | Centralized metadata platform        | Active       |
| Apache Atlas     | Apache ASF       | Data governance and metadata         | Active       |
| DataHub          | LinkedIn→Acryl   | Metadata management platform         | Active       |
| Unity Catalog    | Databricks→LF    | Unified governance for data + AI     | Emerging     |

---

## 16.10 Generative AI Standards (Emerging)

| Standard/Protocol  | Governance      | Purpose                              | Status       |
|--------------------|-----------------|--------------------------------------|--------------|
| OpenAI API format  | OpenAI (L7/L8)  | Chat completions, embeddings API     | De-facto standard (widely imitated) |
| Model Card (Mitchell et al.) | Community | ML model documentation         | De-facto     |
| Hugging Face Hub conventions | Hugging Face | Model hosting, README format  | De-facto     |
| NIST AI 600-1      | NIST            | Generative AI risk profile           | Published 2024|
| EU AI Act (GPAI)   | EU              | General-Purpose AI model obligations | Effective 2025|

---

# CHAPTER 17: SAFETY-CRITICAL, FUNCTIONAL SAFETY & RELIABILITY STANDARDS

## 17.1 Functional Safety Standards Hierarchy

```
┌─────────────────────────────────────────────────────────┐
│         IEC 61508:2010 (Root Standard)                  │
│    Functional Safety of E/E/PE Safety-Related Systems   │
│              SIL 1–4 (all industries)                   │
└──────────────────────┬──────────────────────────────────┘
                       │
    ┌──────────────────┼──────────────────────────────────┐
    │                  │                                   │
    ▼                  ▼                                   ▼
┌─────────┐    ┌─────────────┐    ┌───────────────────────┐
│ISO 26262 │    │ EN 50128    │    │ IEC 61511:2016        │
│Automotive│    │ Railway SW  │    │ Process Industry      │
│ASIL A–D  │    │ SIL 0–4    │    │ SIL 1–3              │
└─────────┘    └─────────────┘    └───────────────────────┘
    │
    ├──→ ISO 21448 (SOTIF)
    └──→ ISO/SAE 21434 (Cybersecurity)

┌─────────────┐    ┌─────────────┐    ┌───────────────────┐
│ IEC 62061    │    │ ISO 13849   │    │ DO-178C/ED-12C    │
│ Machinery    │    │ Machinery   │    │ Aviation Software  │
│ SIL 1–3     │    │ PL a–e     │    │ DAL A–E           │
└─────────────┘    └─────────────┘    └───────────────────┘
```

---

## 17.2 IEC 61508 — The Root Functional Safety Standard

### Full Entry

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Functional Safety of Electrical/Electronic/Programmable Electronic Safety-Related Systems |
| Standard Number              | IEC 61508:2010 (2nd edition, 7 parts)                        |
| Governing Body               | IEC TC 65 (Industrial-process measurement, control and automation) |
| Governance Tier              | L1                                                            |
| Scope                        | Generic — applicable to ALL industries using E/E/PE systems  |
| Key Concept                  | Safety Integrity Level (SIL 1–4)                             |
| Safety Lifecycle             | 16 phases from concept to decommissioning                    |
| Failure Classification       | Random hardware failures + Systematic failures (software, design) |

### SIL Levels — Quantitative Requirements

| SIL | PFD (Low Demand Mode)  | PFH (High Demand / Continuous) | Risk Reduction Factor | Example Applications                |
|-----|------------------------|--------------------------------|----------------------|--------------------------------------|
| SIL 1 | ≥10⁻² to <10⁻¹     | ≥10⁻⁶ to <10⁻⁵ per hour     | 10–100               | Conveyor e-stop, elevator control    |
| SIL 2 | ≥10⁻³ to <10⁻²     | ≥10⁻⁷ to <10⁻⁶ per hour     | 100–1,000            | Gas burner management, pressure relief|
| SIL 3 | ≥10⁻⁴ to <10⁻³     | ≥10⁻⁸ to <10⁻⁷ per hour     | 1,000–10,000         | Chemical plant ESD, railway signaling|
| SIL 4 | ≥10⁻⁵ to <10⁻⁴     | ≥10⁻⁹ to <10⁻⁸ per hour     | 10,000–100,000       | Nuclear protection systems           |

### IEC 61508 Parts

| Part | Title                                              | Focus                        |
|------|----------------------------------------------------|------------------------------|
| Part 1 | General requirements                            | Safety lifecycle, SIL concept|
| Part 2 | Requirements for E/E/PE safety-related systems | Hardware requirements        |
| Part 3 | Software requirements                           | Software development V-model |
| Part 4 | Definitions and abbreviations                   | Terminology                  |
| Part 5 | Examples of SIL determination methods           | Risk graph, LOPA, risk matrix|
| Part 6 | Guidelines on application of Parts 2 and 3      | Implementation guidance      |
| Part 7 | Overview of techniques and measures              | Reference (informative)      |

---

## 17.3 ISO 26262 — Automotive Functional Safety

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Road Vehicles — Functional Safety                             |
| Standard Number              | ISO 26262:2018 (2nd edition, 12 parts)                       |
| Governing Body               | ISO TC 22/SC 32                                               |
| Governance Tier              | L1                                                            |
| Scope                        | Safety-related E/E systems in road vehicles (passenger cars)  |
| Integrity Levels             | ASIL A (lowest) to ASIL D (highest) + QM (no safety requirement) |
| Derived From                 | IEC 61508 (adapted for automotive lifecycle)                  |
| Key Difference from 61508    | Automotive-specific: ASIL decomposition, hardware metrics (SPFM, LFM, PMHF) |

### ASIL Determination

| Parameter          | Values                                            |
|--------------------|---------------------------------------------------|
| Severity (S)       | S0 (no injuries) – S3 (life-threatening/fatal)   |
| Exposure (E)       | E0 (incredible) – E4 (high probability)          |
| Controllability (C)| C0 (controllable) – C3 (uncontrollable)          |

| ASIL Result | Combination Example          | Safety Measures Required             |
|-------------|------------------------------|--------------------------------------|
| QM          | S1 + E1 + C1                | Standard quality management only     |
| ASIL A      | S1 + E3 + C2                | Basic safety process                 |
| ASIL B      | S2 + E3 + C2                | Structured design, testing           |
| ASIL C      | S3 + E3 + C2                | Semi-formal methods, MC/DC for SW    |
| ASIL D      | S3 + E4 + C3                | Formal methods, highest HW metrics   |

### ISO 26262 Hardware Metrics

| Metric | Full Name                          | Target (ASIL D) | Purpose                           |
|--------|------------------------------------|-----------------|-----------------------------------|
| SPFM   | Single-Point Fault Metric          | ≥99%           | Coverage of single-point faults   |
| LFM    | Latent Fault Metric                | ≥90%           | Detection of latent faults        |
| PMHF   | Probabilistic Metric for random HW Failures | <10⁻⁸/hour | Random HW failure rate target |

---

## 17.4 DO-178C — Aviation Software

### Full Entry

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Software Considerations in Airborne Systems and Equipment Certification |
| Document Numbers             | DO-178C (RTCA) / ED-12C (EUROCAE)                            |
| Governance Tier              | L4 (Professional associations: RTCA + EUROCAE)               |
| Regulatory Mandate           | FAA AC 20-115D; EASA CS-25.1309 and AMC 20-115D             |
| Published                    | 2011 (supersedes DO-178B of 1992)                            |
| Scope                        | Software for airborne systems/equipment                       |
| Integrity Levels             | Design Assurance Level (DAL) A through E                     |

### DAL Levels

| DAL | Failure Condition  | Effect                          | Coverage Requirement    | Key Process                    |
|-----|--------------------|---------------------------------|-------------------------|--------------------------------|
| A   | Catastrophic       | Aircraft loss, multiple fatalities | MC/DC coverage       | Formal methods supplement permitted |
| B   | Hazardous/Severe-Major | Large reduction in safety margins, serious injury | Decision coverage | Pair testing               |
| C   | Major              | Significant reduction in safety margins | Statement coverage | Requirements-based testing  |
| D   | Minor              | Slight reduction in safety margins | Requirements-based testing | Basic development assurance |
| E   | No Safety Effect   | No contribution to safe operation| None required           | Standard development          |

### MC/DC (Modified Condition/Decision Coverage)

Required for DAL A software — the most stringent structural coverage criterion:

- Every entry and exit point must be invoked
- Every condition in a decision has been shown to independently affect the decision outcome
- Every decision has taken all possible outcomes
- This is MORE demanding than branch coverage or condition coverage alone

### DO-178C Supplements

| Document | Title                                              | Purpose                          |
|----------|----------------------------------------------------|----------------------------------|
| DO-330   | Software Tool Qualification Considerations         | Qualifying dev/verification tools|
| DO-331   | Model-Based Development and Verification Supplement| MBD for avionics (Simulink, SCADE)|
| DO-332   | Object-Oriented Technology Supplement              | OOP guidance (C++, Java)         |
| DO-333   | Formal Methods Supplement                          | Proof-based verification         |

---

## 17.5 EN 50128 / IEC 62279 — Railway Software

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Railway Applications — Communication, Signalling and Processing Systems — Software for Railway Control and Protection Systems |
| Standard Number              | EN 50128:2011 + A1:2020 (CENELEC) / IEC 62279 (International)|
| Governing Body               | CENELEC TC 9X / IEC TC 9                                     |
| Governance Tier              | L1                                                            |
| Integrity Levels             | SIL 0–4 (same scale as IEC 61508)                           |
| Key Application              | ETCS (European Train Control System), ATP, ATC, interlocking |
| Relationship to 61508        | Railway-specific adaptation of IEC 61508 Part 3              |

### Railway Safety Standard Family

| Standard     | Title                                    | Scope                          |
|--------------|------------------------------------------|--------------------------------|
| EN 50126     | RAMS specification and demonstration     | System level (Reliability, Availability, Maintainability, Safety) |
| EN 50128     | Software for railway control/protection  | Software development           |
| EN 50129     | Safety-related electronic systems        | Hardware/system acceptance      |
| EN 50657     | Software on-board rolling stock          | Train-borne software           |

---

## 17.6 IEC 61511 — Process Industry Safety

| Field                        | Value                                                          |
|------------------------------|----------------------------------------------------------------|
| Full Name                    | Functional Safety — Safety Instrumented Systems for the Process Industry Sector |
| Standard Number              | IEC 61511:2016 (2nd edition, 3 parts)                        |
| Scope                        | Safety Instrumented Systems (SIS) for oil/gas, chemical, pharmaceutical |
| Integrity Levels             | SIL 1–3 (SIL 4 rare in process industry — requires special justification) |
| Key Concept                  | Safety Instrumented Function (SIF), Safety Instrumented System (SIS) |
| SIL Determination Methods    | LOPA (Layers of Protection Analysis), Risk Graph, Risk Matrix |
| Lifecycle Approach           | Mirrors IEC 61508 but tailored for continuous process operations |

---

## 17.7 Machinery Safety Standards

### IEC 62061 vs. ISO 13849

| Feature          | IEC 62061:2021              | ISO 13849-1:2023           |
|------------------|-----------------------------|----------------------------|
| Scope            | E/E/PE safety systems       | All safety-related control system parts |
| Integrity metric | SIL (SIL 1–3)             | Performance Level (PL a–e) |
| Approach         | IEC 61508-derived (probabilistic) | Designated architecture + MTTFd/DCavg/CCF |
| Best for         | Complex electronic/programmable systems | Pneumatic, hydraulic, electromechanical |
| Coverage         | Electronics-focused          | Technology-independent     |

### PL to SIL Approximate Mapping

| Performance Level (ISO 13849) | Approximate SIL (IEC 62061) |
|-------------------------------|-----------------------------|
| PL a                          | < SIL 1                     |
| PL b                          | SIL 1                       |
| PL c                          | SIL 1                       |
| PL d                          | SIL 2                       |
| PL e                          | SIL 3                       |

---

## 17.8 Safety Case Notation

### GSN (Goal Structuring Notation)

| Element       | Symbol     | Purpose                                     |
|---------------|------------|---------------------------------------------|
| Goal          | Rectangle  | Claim about system safety                   |
| Strategy      | Parallelogram | Argument linking goals to evidence       |
| Solution      | Circle     | Evidence supporting a goal                  |
| Context       | Rounded rectangle | Contextual information for a goal   |
| Assumption    | Ellipse (A)| Stated assumption (may need validation)    |
| Justification | Ellipse (J)| Rationale for a strategy choice           |

### CAE (Claims-Arguments-Evidence)

Alternative to GSN (used primarily in UK defense):
- **Claim**: Assertion about safety property
- **Argument**: Reasoning connecting claims to evidence
- **Evidence**: Testing results, analysis, reviews that substantiate claims

---

## 17.9 Formal Methods in Safety-Critical Systems

| Tool/Language | Type                    | Language | Safety Domain              | Certification Support       |
|---------------|-------------------------|----------|----------------------------|-----------------------------|
| SPARK Ada     | Formally verified subset| Ada      | Aviation, Railway, Nuclear | DO-178C DAL A, EN 50128 SIL 4 |
| Frama-C       | Static analysis + WP    | C        | Nuclear, Medical devices   | DO-178C, IEC 62304          |
| SCADE         | Synchronous dataflow    | Lustre   | Aviation, Railway          | DO-178C (KCG qualified to DAL A) |
| TLA+          | Temporal logic          | TLA+     | Distributed systems        | Amazon (protocol verification)|
| Coq           | Proof assistant         | Gallina  | Research, CompCert         | Academic verification       |
| Isabelle/HOL  | HOL proof assistant     | Isar     | seL4 microkernel          | Formal OS verification      |
| CompCert      | Verified C compiler     | Coq      | Aviation                   | DO-178C research            |
| Astrée        | Abstract interpretation | C        | Aviation (Airbus)          | DO-178C, Ariane V           |
| PolySpace     | Abstract interpretation | C/C++    | Automotive, Aviation       | ISO 26262, DO-178C          |
| CBMC          | Bounded model checking  | C        | Embedded systems           | Verification support        |

---

## 17.10 Reliability Engineering Standards

| Standard       | Title                                         | Governance | Purpose                      |
|----------------|-----------------------------------------------|------------|------------------------------|
| IEC 60812:2018 | Failure Mode and Effects Analysis (FMEA)     | IEC (L1)   | Systematic failure analysis  |
| IEC 61025:2006 | Fault Tree Analysis (FTA)                    | IEC (L1)   | Top-down failure logic       |
| IEC 61078:2016 | Reliability Block Diagrams (RBD)             | IEC (L1)   | System reliability modeling  |
| IEC 62740:2015 | Root Cause Analysis (RCA)                    | IEC (L1)   | Incident investigation       |
| MIL-HDBK-217F  | Reliability Prediction of Electronic Equipment| US DoD (L2)| Component failure rates     |
| FIDES          | Reliability Methodology for Electronic Systems| DGA (France)| Modern alternative to 217F |

### RAMS Analysis

| Parameter        | Definition                                      | Metrics                        |
|------------------|-------------------------------------------------|--------------------------------|
| **R**eliability  | Probability system performs function without failure| MTBF, MTTF, Failure Rate (λ) |
| **A**vailability | Proportion of time system is operational        | A = MTBF / (MTBF + MTTR)      |
| **M**aintainability | Ease of restoring system after failure       | MTTR, MDT                      |
| **S**afety       | Freedom from unacceptable risk                  | SIL, ASIL, PFD/PFH            |

---

## 17.11 Safety Standards Master Comparison

| Standard          | Domain         | Tier | Integrity Levels | Root         | Regulatory Mandate               |
|-------------------|----------------|------|------------------|--------------|----------------------------------|
| IEC 61508:2010    | All E/E/PE     | L1   | SIL 1–4          | Root         | Referenced by all sector standards|
| ISO 26262:2018    | Automotive     | L1   | ASIL A–D (+QM)  | IEC 61508    | OEM requirement (market mandate) |
| DO-178C (2011)    | Aviation SW    | L4   | DAL A–E          | Derived philosophy | FAA AC 20-115D, EASA       |
| DO-254 (2000)     | Aviation HW    | L4   | DAL A–E          | Derived      | FAA AC 20-152A                   |
| EN 50128:2011     | Railway SW     | L1   | SIL 0–4          | IEC 61508    | EU ERA, national rail authorities|
| IEC 61511:2016    | Process industry| L1  | SIL 1–3          | IEC 61508    | OSHA PSM, national regulations   |
| IEC 62061:2021    | Machinery (E/E/PE)| L1| SIL 1–3          | IEC 61508    | EU Machinery Regulation 2023/1230|
| ISO 13849-1:2023  | Machinery (all)| L1  | PL a–e           | Parallel     | EU Machinery Regulation          |
| ISO 21448:2022    | Auto ADAS/AD   | L1   | Risk-based       | Complements 26262 | UNECE developing requirements |
| IEC 62304:2006+A1 | Medical device SW| L1 | Class A/B/C     | Inspired by 61508 | FDA, EU MDR 2017/745       |
| ISO 25119:2010    | Agricultural machinery| L1 | AgPL a–e     | IEC 61508    | EU Machinery Regulation          |

---

## 17.12 Key Disambiguation Notes

### 1. SIL vs. ASIL vs. DAL

| Metric | Standard    | Scale           | Domain       | Meaning                      |
|--------|-------------|-----------------|--------------|------------------------------|
| SIL    | IEC 61508   | 1–4 (4=highest)| Generic      | Safety Integrity Level       |
| ASIL   | ISO 26262   | A–D (D=highest)| Automotive   | Automotive SIL               |
| DAL    | DO-178C     | A–E (A=highest)| Aviation     | Design Assurance Level       |
| PL     | ISO 13849   | a–e (e=highest)| Machinery    | Performance Level            |

### 2. FMEA vs. FTA

| Analysis | Approach    | Direction    | Standard     | Best For                   |
|----------|-------------|--------------|--------------|----------------------------|
| FMEA     | Bottom-up   | Component → System effect | IEC 60812 | Identifying all failure modes |
| FTA      | Top-down    | System failure → Root causes | IEC 61025 | Analyzing specific hazards |

### 3. Functional Safety vs. SOTIF (ISO 21448)

| Aspect           | Functional Safety (ISO 26262) | SOTIF (ISO 21448)               |
|------------------|-------------------------------|----------------------------------|
| Addresses        | Malfunctions (failures)       | Insufficiencies (limitations)    |
| Cause            | HW/SW faults                  | Sensor limits, algorithm gaps, misuse |
| Assumption       | System works as designed       | System works as designed BUT design is insufficient |
| Typical system   | ABS, airbag, ESC              | ADAS (AEB, lane keeping, L3+ AD)|
| Key analysis     | HARA, FMEA, FTA              | Triggering conditions, performance limitations |

---

*End of Chapters 16 & 17 — AI/ML/Data Standards & Safety-Critical/Functional Safety Standards*
*Next: Chapters 18–20 — Crosswalk Tables, Master Matrix & Template Guide*
