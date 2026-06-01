# AI/ML in Medical Devices — Regulatory Frameworks & Standards

**Topic:** Artificial Intelligence and Machine Learning in medical device development, regulatory pathways, and lifecycle management  
**Standard:** FDA AI/ML Action Plan (2021+); Predetermined Change Control Plan (PCCP, 2023); WHO AI Guidance (2021); EU AI Act (2024); ISO/IEC 42001:2023  
**SDO:** FDA/CDRH; WHO; EU Commission; ISO/IEC JTC 1/SC 42  
**Audience:** AI/ML engineers developing medical devices, regulatory affairs professionals, clinical algorithm developers, data scientists in healthcare, quality engineers  
**Prerequisites:** Machine learning fundamentals, medical device regulatory basics (510(k)/CE marking), IEC 62304, ISO 14971 risk management

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 1998 | First AI/ML-based medical device cleared (CADe mammography) | Computer-Aided Detection; FDA 510(k) cleared |
| 2017 | FDA Digital Health Innovation Action Plan | Framework for regulating AI/ML software as medical device (SaMD) |
| 2018 | FDA clears IDx-DR (autonomous AI for diabetic retinopathy) | First AI device authorized for autonomous diagnosis without physician oversight |
| 2019 | FDA Proposed Regulatory Framework for AI/ML-Based SaMD | Introduced "locked" vs "continuously learning" algorithm concept; Total Product Lifecycle approach |
| 2020 | FDA AI/ML Action Plan (5 priorities) | PCCP concept; GMLP; real-world performance; bias elimination; transparency |
| 2021 | **WHO Guidance on AI for Health** | 6 core principles for ethical AI in healthcare |
| 2021 | FDA releases list of AI/ML-enabled devices cleared/authorized | Transparency initiative: 300+ AI devices cataloged |
| 2022 | EU AI Act proposed (Article 6 — high-risk AI) | Medical AI classified as high-risk; conformity assessment required |
| 2023 | **FDA PCCP Final Guidance** | Predetermined Change Control Plan: framework for adaptive AI that changes post-market without new submission per change |
| 2023 | **ISO/IEC 42001:2023** published | AI Management System standard; applicable to AI in medical devices |
| 2024 | **EU AI Act enacted** (Regulation 2024/1689) | Legally binding; medical AI = high-risk; mandatory conformity assessment |
| 2024 | FDA AI/ML cleared devices exceed 950+ | Rapid growth; radiology, cardiology, pathology dominant |
| 2024 | IMDRF AI/ML Medical Device Guidance | International harmonized guidance for AI/ML SaMD |
| 2025 | EU AI Act provisions phase in | Prohibited AI practices (Feb 2025); high-risk requirements (Aug 2026) |

### 1.2 Growth of FDA-Cleared AI/ML Devices

| Specialty | ~Number of Cleared Devices (2024) | Dominant Use Cases |
|-----------|:---------------------------------:|-------------------|
| Radiology | 500+ | Detection (CADe); triage; quantification; workflow prioritization |
| Cardiology | 150+ | ECG interpretation; arrhythmia detection; cardiac imaging AI |
| Pathology | 40+ | Whole-slide image analysis; cancer detection; cell counting |
| Neurology | 30+ | Stroke detection (LVO); brain hemorrhage triage |
| Ophthalmology | 25+ | Diabetic retinopathy screening; glaucoma; AMD detection |
| Orthopedics | 20+ | Fracture detection; surgical planning AI |
| Gastroenterology | 15+ | Polyp detection during colonoscopy (CADe) |

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 Regulatory Framework Overview

```mermaid
graph TB
    subgraph "Global Frameworks"
        WHO[WHO AI for Health<br/>Guidance 2021<br/>━━━━━━━━━━━<br/>6 ethical principles<br/>Governance<br/>Equity & inclusion]
        IMDRF_AI[IMDRF AI/ML SaMD<br/>2024<br/>━━━━━━━━━━━<br/>Lifecycle approach<br/>GMLP<br/>International harmonization]
    end
    
    subgraph "US (FDA)"
        FDA_PLAN[FDA AI/ML Action Plan<br/>2021<br/>━━━━━━━━━━━<br/>5 priorities:<br/>1. Regulatory framework<br/>2. GMLP<br/>3. Patient-centered approach<br/>4. Bias/robustness<br/>5. Real-world performance]
        PCCP[Predetermined Change<br/>Control Plan (PCCP)<br/>2023 Final Guidance<br/>━━━━━━━━━━━<br/>Define allowed changes<br/>Modification protocol<br/>Performance assessment]
        GMLP[Good Machine Learning<br/>Practice (GMLP)<br/>2021<br/>━━━━━━━━━━━<br/>10 guiding principles<br/>FDA + Health Canada + MHRA]
    end
    
    subgraph "EU"
        AI_ACT[EU AI Act<br/>Regulation 2024/1689<br/>━━━━━━━━━━━<br/>Risk-based classification<br/>High-risk (medical)<br/>Conformity assessment<br/>Transparency obligations]
        MDR_AI[EU MDR + AI<br/>━━━━━━━━━━━<br/>SaMD classification<br/>Rule 11<br/>Clinical evaluation<br/>for AI outputs]
    end
    
    subgraph "Standards"
        ISO42001[ISO/IEC 42001:2023<br/>AI Management System<br/>━━━━━━━━━━━<br/>AI governance<br/>Risk management<br/>Lifecycle controls<br/>Certification]
        IEC62304[IEC 62304 + AI<br/>━━━━━━━━━━━<br/>Software lifecycle<br/>applied to AI/ML<br/>Development process]
        ISO14971[ISO 14971 + AI risks<br/>━━━━━━━━━━━<br/>AI-specific hazards<br/>Bias as hazard<br/>Performance degradation]
    end
    
    WHO --> FDA_PLAN & AI_ACT
    IMDRF_AI --> FDA_PLAN & AI_ACT
    FDA_PLAN --> PCCP & GMLP
    AI_ACT --> MDR_AI
    ISO42001 --> FDA_PLAN & AI_ACT
    IEC62304 --> FDA_PLAN & MDR_AI
    ISO14971 --> FDA_PLAN & MDR_AI
```

### 2.2 AI/ML Device Classification

| Algorithm Type | FDA Pathway | EU MDR Classification | Risk Level |
|---------------|:-----------:|:---------------------:|:----------:|
| **CADe** (Computer-Aided Detection) — flags findings for physician review | 510(k) (Class II) | Class IIa/IIb (Rule 11) | Moderate |
| **CADx** (Computer-Aided Diagnosis) — provides diagnosis/recommendation | 510(k) or De Novo (Class II) | Class IIb (Rule 11) | Moderate-High |
| **CADt** (Computer-Aided Triage) — prioritizes review order | 510(k) (Class II) | Class IIa/IIb | Moderate |
| **Autonomous AI** — makes clinical decision without physician | De Novo (Class II) or PMA (Class III) | Class III | High |
| **Clinical Decision Support** (non-SaMD criteria met) | Exempt (not a device) | Not a device (if criteria met) | Low |

### 2.3 "Locked" vs "Adaptive" AI

| Characteristic | Locked Algorithm | Adaptive Algorithm (with PCCP) |
|---------------|:---:|:---:|
| Definition | Algorithm does not change after clearance/approval | Algorithm can be updated/retrained based on new data |
| Regulatory handling | Standard device submission; changes require new submission | PCCP describes allowed modifications; within PCCP = no new submission |
| Clinical validation | Fixed at time of submission | Ongoing (real-world performance monitoring validates each update) |
| Example | AI detects pneumothorax using model trained and frozen at v1.0 | AI detects pneumothorax; model periodically retrained on new hospital data within PCCP bounds |
| Advantages | Simpler regulatory; known performance | Improves over time; adapts to new populations/equipment |
| Challenges | May degrade as imaging technology/population changes | Complex validation; monitoring burden; bias monitoring |

---

## Chapter 3 — Technical Deep Dive

### 3.1 Good Machine Learning Practice (GMLP) — 10 Principles

| # | Principle | Practical Implementation |
|:-:|-----------|-------------------------|
| 1 | Multi-disciplinary expertise | Team includes: ML engineers + clinicians + regulatory + human factors + cybersecurity + ethicists |
| 2 | Good software engineering practices (IEC 62304) | Version control (models, data, code); reproducible training pipelines; configuration management; documentation |
| 3 | Representative clinical study participants and datasets | Training data representative of intended patient population (demographics, comorbidities, device types, imaging parameters) |
| 4 | Training datasets independent of test sets | Strict separation; no data leakage; patient-level splitting (not image-level); temporal validation |
| 5 | Reference datasets based on best available methods | Ground truth labeling by expert consensus; multi-reader studies; comparison to gold standard (biopsy, follow-up) |
| 6 | Model design suited for available data | Appropriate complexity for dataset size; regularization; avoid overfitting; architecture justified |
| 7 | Focus on performance of human-AI team | Evaluate AI + clinician together (not AI alone); consider attention/fatigue effects; measure workflow impact |
| 8 | Testing demonstrates device performance in clinically relevant conditions | Test with realistic clinical variability (motion artifacts, equipment variation, patient diversity, edge cases) |
| 9 | Users provided clear, essential information | Transparency: intended use, limitations, known failure modes, performance by subgroup |
| 10 | Deployed models monitored; risks managed when models are updated | Post-market surveillance; distribution shift detection; performance monitoring; retraining governance |

### 3.2 Predetermined Change Control Plan (PCCP) — FDA 2023

| PCCP Component | Content | Example |
|:--------------:|---------|---------|
| **Description of Modification** | What specific changes are planned | "Retrain model with additional training data from new scanner types (GE, Philips, Siemens) to expand compatibility" |
| **Modification Protocol** | HOW changes will be developed and validated | "Retraining protocol: minimum 10,000 images from new scanner; same architecture; hyperparameter bounds (learning rate ≤0.001); validation on held-out set" |
| **Performance Assessment** | HOW performance will be verified after modification | "Performance metrics must meet: AUC ≥0.95 (no decrease >0.02 from baseline); sensitivity ≥90%; specificity ≥85%; tested on 1000-image balanced test set from each new scanner" |
| **Update Records** | Documentation requirements for each update | "Maintain: training data description; model version; performance metrics; comparison to prior version; any failures; deployment date" |
| **Guardrails** | Boundaries — when does a change exceed PCCP scope? | "PCCP does NOT cover: changing output classes (adding new pathology); changing intended use; architecture change; expansion to new anatomy; performance below minimum thresholds" |

### 3.3 AI-Specific Risk Management (ISO 14971 Extended)

| AI-Specific Hazard | Source | Harm Pathway | Mitigation |
|-------------------|--------|--------------|------------|
| **Bias** (demographic, selection, measurement) | Non-representative training data; historical bias in labels | Disparate performance across subgroups → missed diagnosis in underrepresented group → delayed treatment | Representative training data; subgroup performance analysis; bias testing; diverse labeling panel |
| **Distribution shift** (covariate shift) | Different patient population, imaging equipment, or protocols vs. training data | AI performance degrades on new population → false negatives increase | Input data monitoring; out-of-distribution detection; performance monitoring by site; PCCP for retraining |
| **Overfitting** | Model memorizes training data artifacts; insufficient generalization | Good performance on test set, poor in real world → unreliable clinical decisions | Cross-validation; external validation; regularization; appropriate model complexity; multi-site testing |
| **Adversarial vulnerability** | Intentional or unintentional perturbations to input | Small input changes cause large output changes → incorrect diagnosis | Adversarial robustness testing; input validation; anomaly detection; fail-safe design |
| **Automation bias** | Clinicians over-rely on AI output; reduced vigilance | Incorrect AI output accepted without critical review → patient harm | Human factors design; uncertainty communication; attention management; training on AI limitations |
| **Lack of explainability** | Black-box model; clinician cannot understand AI reasoning | Clinician cannot verify AI logic → either rejects useful AI or accepts incorrect AI | Explainability methods (Grad-CAM, SHAP, attention maps); clinical rationale generation; confidence scores |
| **Data poisoning** | Corrupted training data (malicious or accidental) | Model learns incorrect patterns → systematic errors in deployment | Data quality controls; provenance tracking; anomaly detection in training data; secure data pipeline |
| **Concept drift** | Clinical practice changes over time; disease prevalence shifts | Model trained on historical patterns becomes outdated → degraded performance | Continuous monitoring; drift detection algorithms; scheduled revalidation; PCCP for updates |

### 3.4 AI/ML Development Lifecycle (Mapped to IEC 62304)

```mermaid
graph TB
    subgraph "Problem Definition (IEC 62304: Requirements)"
        CLIN[Clinical Problem<br/>Definition<br/>━━━━━━━━━━━<br/>• Intended use<br/>• Target population<br/>• Clinical workflow<br/>• Performance targets<br/>• Risk classification]
    end
    
    subgraph "Data Engineering"
        DATA[Data Management<br/>━━━━━━━━━━━<br/>• Data collection protocol<br/>• Annotation guidelines<br/>• Quality criteria<br/>• Representativeness analysis<br/>• Train/validation/test split<br/>• Data governance]
        LABEL[Ground Truth Labeling<br/>━━━━━━━━━━━<br/>• Expert panel (≥3 readers)<br/>• Adjudication protocol<br/>• Inter-rater agreement (κ)<br/>• Reference standard definition]
    end
    
    subgraph "Model Development (IEC 62304: Design & Implementation)"
        ARCH[Architecture Selection<br/>━━━━━━━━━━━<br/>• CNN / Transformer / Ensemble<br/>• Complexity vs. data balance<br/>• Computational constraints<br/>• Explainability requirements]
        TRAIN[Model Training<br/>━━━━━━━━━━━<br/>• Hyperparameter optimization<br/>• Cross-validation<br/>• Regularization<br/>• Augmentation strategy<br/>• Training reproducibility]
    end
    
    subgraph "Verification & Validation (IEC 62304: V&V)"
        VERI[Verification<br/>━━━━━━━━━━━<br/>• Unit tests<br/>• Integration tests<br/>• Performance on held-out set<br/>• Subgroup analysis<br/>• Robustness testing<br/>• Adversarial testing]
        CLIN_VAL[Clinical Validation<br/>━━━━━━━━━━━<br/>• Prospective study<br/>• Multi-site<br/>• Clinically relevant endpoints<br/>• Comparison to standard of care<br/>• Reader study (human-AI team)]
    end
    
    subgraph "Regulatory & Deployment"
        REG[Regulatory Submission<br/>━━━━━━━━━━━<br/>• 510(k) / De Novo / PMA<br/>• Clinical evidence<br/>• PCCP (if adaptive)<br/>• Algorithmic description<br/>• Performance summary]
        DEPLOY[Deployment<br/>━━━━━━━━━━━<br/>• Integration testing<br/>• Site validation<br/>• User training<br/>• Monitoring setup]
    end
    
    subgraph "Post-Market (IEC 62304: Maintenance)"
        MONITOR[Performance Monitoring<br/>━━━━━━━━━━━<br/>• Real-world performance<br/>• Distribution shift detection<br/>• Bias monitoring<br/>• User feedback<br/>• Adverse event tracking]
        UPDATE[Model Updates (per PCCP)<br/>━━━━━━━━━━━<br/>• Retrain with new data<br/>• Validate per PCCP protocol<br/>• Version management<br/>• Deployment orchestration]
    end
    
    CLIN --> DATA --> LABEL --> ARCH --> TRAIN --> VERI --> CLIN_VAL --> REG --> DEPLOY --> MONITOR
    MONITOR -->|"Drift detected/<br/>New data available"| UPDATE
    UPDATE -->|"Within PCCP bounds"| DEPLOY
    UPDATE -->|"Exceeds PCCP bounds"| REG
```

---

## Chapter 4 — Implementation Guide

### 4.1 Data Management for Medical AI

| Requirement | Implementation | Regulatory Expectation |
|-------------|----------------|----------------------|
| **Data governance** | Data Use Agreements (DUA); IRB approval; consent documentation; de-identification (HIPAA Safe Harbor or Expert Determination) | FDA expects documented data governance; EU GDPR compliance for training data |
| **Representativeness** | Demographic analysis of training data (age, sex, race/ethnicity distribution); comparison to intended use population; gap analysis | FDA: document demographics; identify underrepresented groups; test performance by subgroup |
| **Annotation quality** | Multi-reader annotation (≥3 experts); Cohen's kappa / Fleiss's kappa for agreement; adjudication protocol for disagreements; annotation guidelines document | Reference standard must be documented; labeling methodology disclosed |
| **Data splitting** | Patient-level split (never image-level for same patient in train + test); temporal split (train on older data, test on newer); site-level split for multi-site studies | FDA: independence of test data from training; no information leakage |
| **Version control** | Dataset versioning (DVC, git-lfs); immutable test sets; hash-verified dataset integrity; changelog | Full traceability from submission back to exact training/test data |
| **Bias detection** | Measure performance metrics by: sex, age group, race/ethnicity, imaging device, clinical site, disease severity, comorbidities | FDA: subgroup analysis required; identify disparities |

### 4.2 Clinical Validation Study Design

| Study Element | Requirement | Rationale |
|:-------------:|-------------|-----------|
| Study type | Prospective or retrospective with adequate design | Prospective preferred; retrospective acceptable with proper controls |
| Sample size | Powered for primary endpoint; typically N=200-2000+ for imaging AI | Statistical confidence; adequate subgroup sizes |
| Ground truth | Best available reference standard (biopsy, surgical confirmation, expert consensus, follow-up) | Must be independent of AI; cannot use AI output as truth |
| Test data independence | NOT used in training or architecture selection | Prevent optimistic bias |
| Multi-site | ≥3 sites (different equipment, patient populations, operators) | Demonstrate generalizability |
| Reader study | Compare AI-only, physician-only, physician+AI | Show AI adds clinical value; measure human-AI team performance |
| Primary endpoint | Clinically relevant metric (sensitivity, specificity, AUC, time-to-diagnosis, patient outcome) | Meaningful to clinical use |
| Subgroup analysis | Performance by demographics, disease severity, equipment | Identify populations where AI may underperform |
| Pre-specification | Statistical analysis plan written BEFORE unblinding | Prevent data dredging |

### 4.3 Model Documentation Template (Model Card)

| Section | Content |
|---------|---------|
| Model details | Name; version; developer; date; architecture (CNN type, size, parameters); training hardware; training duration |
| Intended use | Clinical indication; target population; clinical setting; user (physician specialty); workflow integration point |
| Training data | Source; size; demographics; date range; inclusion/exclusion criteria; annotation method; reference standard |
| Performance metrics | AUC, sensitivity, specificity, PPV, NPV overall AND by subgroup (age, sex, race/ethnicity, device, site) |
| Limitations | Known failure modes; populations not well represented; imaging conditions where performance degrades; cases where AI should not be used |
| Ethical considerations | Potential bias sources; fairness metrics; actions taken to address bias; patient consent for training data |
| Operational requirements | Inference time; hardware requirements; input specifications (image format, resolution, modality); network requirements |
| Monitoring plan | KPIs to track in deployment; drift detection method; performance review frequency; trigger for retraining |
| Version history | Changes from prior version; performance comparison; reason for update |

---

## Chapter 5 — Certification & Regulatory Submissions

### 5.1 FDA Submission for AI/ML Device

| Submission Component | AI-Specific Content |
|---------------------|-------------------|
| **Device description** | Algorithm architecture; inputs/outputs; integration in clinical workflow; locked vs. adaptive; intended use (detection, diagnosis, triage, autonomous) |
| **Substantial equivalence** (510(k)) | Predicate device comparison; algorithm comparison; performance comparison; same clinical question; same input/output type |
| **Performance testing** | Standalone performance (sensitivity, specificity, AUC, ROC); reader study (human-AI); multi-site validation; subgroup analysis |
| **Software documentation** | IEC 62304 process; software description (architecture, languages, libraries); level of concern; cybersecurity (SBOM for ML frameworks); software requirements specification |
| **Risk analysis** | ISO 14971 risk management; AI-specific hazards (bias, drift, adversarial, automation bias); hazard mitigations; residual risk |
| **PCCP** (if adaptive) | Modification description; modification protocol; assessment method; performance boundaries; guardrails |
| **Clinical evidence** | Clinical study report; statistical analysis; comparison to standard of care; clinical utility argument |
| **Labeling** | Intended use; indications for use; limitations; performance summary; warnings (not for standalone diagnosis if CADe); subgroup performance |

### 5.2 EU AI Act — High-Risk AI Requirements (Medical AI)

| Requirement | Implementation for Medical AI Device |
|-------------|--------------------------------------|
| **Risk management system** (Art. 9) | Continuous risk management throughout lifecycle; identify foreseeable risks; estimate risks; adopt measures; residual risk acceptable; testing against foreseeable misuse |
| **Data governance** (Art. 10) | Training, validation, test datasets: relevant, representative, free of errors, complete; statistical properties examined; bias addressed; data governance practices documented |
| **Technical documentation** (Art. 11) | General description; elements of AI system; development process; monitoring/functioning; risk management results; changes; standards applied; EU declaration of conformity |
| **Record-keeping** (Art. 12) | Automatic logging: input data characteristics; periods of use; reference database used; search coincidences; natural persons involved in verification |
| **Transparency** (Art. 13) | Information to deployers: intended purpose, capabilities, limitations; level of accuracy, robustness, cybersecurity; known circumstances of misuse; human oversight measures |
| **Human oversight** (Art. 14) | AI designed for effective human oversight; deployer can: understand output; override/interrupt; decide not to use; monitor operation |
| **Accuracy, robustness, cybersecurity** (Art. 15) | Appropriate and consistent accuracy levels; resilient to errors/faults/adversarial attempts; cybersecurity measures against unauthorized access/manipulation |
| **Conformity assessment** (Art. 43) | For medical devices: conformity assessment under MDR (Art. 43(3)) — Notified Body assessment under EU MDR Annex IX/X includes AI Act requirements |

### 5.3 Performance Metrics for AI/ML Medical Devices

| Metric | Formula | Interpretation | Typical Threshold (FDA-cleared) |
|--------|---------|---------------|---------------------------------|
| **AUC (ROC)** | Area under ROC curve | Overall discriminative ability | ≥0.90 (often ≥0.95 for clearance) |
| **Sensitivity (Recall)** | $\frac{TP}{TP + FN}$ | Ability to detect disease (miss rate = 1 - sensitivity) | ≥85-95% (varies by indication) |
| **Specificity** | $\frac{TN}{TN + FP}$ | Ability to correctly identify normal | ≥80-95% |
| **PPV (Precision)** | $\frac{TP}{TP + FP}$ | Proportion of positive predictions that are correct | Context-dependent (prevalence affects PPV) |
| **NPV** | $\frac{TN}{TN + FN}$ | Proportion of negative predictions that are correct | High (often >99% for screening) |
| **Cohen's Kappa** | Agreement corrected for chance | Inter-rater agreement (AI vs. expert) | ≥0.75 (substantial agreement) |
| **Dice coefficient** (segmentation) | $\frac{2|A \cap B|}{|A| + |B|}$ | Overlap between AI and ground truth segmentation | ≥0.85 for anatomical structures |

---

## Chapter 6 — Regional Context

### 6.1 AI/ML Medical Device Regulation by Region

| Region | Framework | Classification | Key Differences |
|--------|-----------|:---:|----------------|
| US (FDA) | 510(k), De Novo, PMA + PCCP | Class II (most); Class III (autonomous, high-risk) | PCCP allows adaptive AI; GMLP guidance; performance-based |
| EU | MDR Rule 11 + AI Act | Class IIa-III depending on role | Dual regulation (MDR + AI Act); NB conformity assessment; transparency obligations |
| UK (MHRA) | UK MDR 2002 (being reformed) + Software/AI Guidance 2023 | To be determined (post-Brexit evolution) | Collaborated with FDA/HC on GMLP; agile regulation; sandbox approach |
| Canada | Health Canada + AI/ML Guidance 2023 | Class II-IV | PCCP-like framework; GMLP co-author; aligned with FDA |
| Japan | PMDA SaMD Guidance + AI supplement | Classification similar to FDA | Expedited pathway for AI (SAKIGAKE); real-world evidence emphasis |
| China | NMPA AI/ML Guidance (2022) | Registration classes I-III | Large domestic AI industry; registration pathway + post-market obligations |
| South Korea | MFDS AI/ML Guidance (2022) | Classes I-IV | Active AI regulatory sandbox; real-world data pathways |

### 6.2 International Harmonization Efforts

| Initiative | Participants | Output |
|-----------|:------------:|--------|
| GMLP (Good Machine Learning Practice) | FDA + Health Canada + MHRA | 10 guiding principles for AI/ML development (2021) |
| IMDRF AI/ML Working Group | All IMDRF members | Harmonized terminology; lifecycle framework; key principles (2024) |
| WHO AI for Health | Global | 6 ethical principles; guidance document; implementation support |
| IEEE P2801 | Industry | Recommended practice for AI/ML in healthcare (quality of ML models) |
| AAMI/BSI CR 34971 | US/UK | Guidance applying ISO 14971 risk management to AI/ML in medical devices |

---

## Chapter 7 — Comparison

### 7.1 AI Regulation: Medical vs. Non-Medical

| Dimension | Medical AI (FDA + EU MDR + AI Act) | General AI (EU AI Act only) |
|-----------|:---:|:---:|
| Risk classification | Medical risk (patient safety) + AI risk (high-risk) | AI risk only (prohibited/high/limited/minimal) |
| Conformity assessment | Notified Body (EU); FDA review (US) | Third-party (high-risk) or self-declaration (limited risk) |
| Clinical evidence | Required (clinical studies; reader studies) | Not required (performance testing only) |
| Post-market surveillance | Active monitoring (PMS); vigilance; PMCF | Market surveillance; incident reporting |
| Data requirements | Representative clinical data; bias analysis; subgroup testing | Representative data; bias addressed |
| Human oversight | Often mandatory (physician in loop except autonomous AI) | Required for high-risk; design measures |
| Transparency | Labeling; IFU; physician training | User notification; documentation |
| Update pathway | PCCP (FDA); significant change assessment (EU MDR) | General conformity maintenance |

### 7.2 AI/ML Frameworks: Key Standard Comparison

| Feature | ISO/IEC 42001 | IEC 62304 (+ AI) | FDA GMLP | EU AI Act |
|---------|:---:|:---:|:---:|:---:|
| Type | Management system standard (certifiable) | Product development standard | Guidance (non-binding) | Regulation (legally binding) |
| Scope | AI systems (any domain) | Medical device software lifecycle | Medical AI/ML development | All AI systems (risk-based) |
| Focus | Organizational governance; AI policy; risk management | Development process; V&V; maintenance | Development best practices | Market access requirements |
| Applicability | Organizational certification | Product compliance | Informing development practices | Legal compliance |
| Risk approach | AI risk assessment (organizational + system) | ISO 14971 (patient safety) | ML-specific risks; GMLP principles | Risk classification (4 levels) |
| Certification | Yes (ISO 42001 certification) | NB assessment (as part of QMS) | No (guidance only) | Conformity assessment + CE marking |

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 AI/ML Medical Device — Complete System Architecture

```mermaid
graph TB
    subgraph "Data Acquisition"
        DEVICE[Imaging Device<br/>(CT/MR/US/X-ray)<br/>━━━━━━━━━━━<br/>DICOM output]
        PACS_S[PACS<br/>━━━━━━━━━━━<br/>Image archive<br/>DICOMweb access]
    end
    
    subgraph "AI Processing Pipeline"
        INGEST[Input Processing<br/>━━━━━━━━━━━<br/>DICOM parsing<br/>Preprocessing<br/>Normalization<br/>Quality check<br/>OOD detection]
        
        MODEL[AI Model Inference<br/>━━━━━━━━━━━<br/>CNN / Transformer<br/>Inference engine<br/>GPU acceleration<br/>Model version: v2.3]
        
        POST[Post-Processing<br/>━━━━━━━━━━━<br/>Confidence thresholding<br/>Anatomical validation<br/>Output formatting<br/>Explanation generation]
    end
    
    subgraph "Output & Integration"
        RESULTS[Results Generation<br/>━━━━━━━━━━━<br/>DICOM SR (Structured Report)<br/>DICOM Presentation State<br/>DICOM Segmentation<br/>HL7/FHIR CDS Hook]
        
        VIEWER[Clinical Viewer<br/>━━━━━━━━━━━<br/>AI results overlay<br/>Confidence display<br/>Explanation (heatmap)<br/>Accept/Reject workflow]
        
        EHR[EHR Integration<br/>━━━━━━━━━━━<br/>FHIR DiagnosticReport<br/>CDS Alert<br/>Worklist prioritization]
    end
    
    subgraph "Monitoring & Governance"
        PERF_MON[Performance Monitor<br/>━━━━━━━━━━━<br/>Real-world accuracy<br/>Distribution shift detection<br/>Throughput / latency<br/>Error rate tracking]
        
        BIAS_MON[Bias Monitor<br/>━━━━━━━━━━━<br/>Subgroup performance<br/>Demographic analysis<br/>Fairness metrics<br/>Alert on disparity]
        
        VERSION[Model Registry<br/>━━━━━━━━━━━<br/>Version management<br/>A/B testing<br/>Rollback capability<br/>PCCP compliance check]
    end
    
    DEVICE --> PACS_S --> INGEST --> MODEL --> POST --> RESULTS
    RESULTS --> VIEWER & EHR
    MODEL --> PERF_MON & BIAS_MON
    VERSION --> MODEL
    PERF_MON -->|"Drift alert"| VERSION
```

### 8.2 PCCP Decision Flow

```mermaid
flowchart TB
    START[Modification Identified<br/>(New data; performance issue;<br/>new scanner; algorithm improvement)]
    
    PCCP_CHECK{Does modification<br/>fall within approved<br/>PCCP scope?}
    
    WITHIN[Within PCCP Bounds<br/>━━━━━━━━━━━<br/>• Same intended use<br/>• Same output type<br/>• Architecture unchanged<br/>• Performance within bounds<br/>• Modification protocol followed]
    
    OUTSIDE[Exceeds PCCP Bounds<br/>━━━━━━━━━━━<br/>• New intended use<br/>• New output classes<br/>• Architecture change<br/>• Performance below threshold<br/>• New patient population]
    
    MODIFY[Execute Modification Protocol<br/>━━━━━━━━━━━<br/>1. Collect new data per protocol<br/>2. Retrain per defined parameters<br/>3. Validate on predefined test set<br/>4. Compare to performance thresholds]
    
    ASSESS{Performance meets<br/>PCCP thresholds?}
    
    DEPLOY_INT[Deploy Update<br/>━━━━━━━━━━━<br/>• Version model<br/>• Update SBOM<br/>• Document changes<br/>• Update records per PCCP<br/>• No new FDA submission]
    
    FAIL[Cannot Deploy<br/>━━━━━━━━━━━<br/>• Investigate root cause<br/>• Additional data/tuning<br/>• May need new approach<br/>• Consider exceeds PCCP]
    
    NEW_SUB[New Regulatory Submission<br/>━━━━━━━━━━━<br/>• 510(k) supplement or new 510(k)<br/>• Updated clinical evidence<br/>• New risk analysis<br/>• New/updated PCCP<br/>• Full review cycle]
    
    START --> PCCP_CHECK
    PCCP_CHECK -->|Yes| WITHIN --> MODIFY
    PCCP_CHECK -->|No| OUTSIDE --> NEW_SUB
    MODIFY --> ASSESS
    ASSESS -->|Yes| DEPLOY_INT
    ASSESS -->|No| FAIL
    FAIL -->|"Retry within protocol"| MODIFY
    FAIL -->|"Fundamentally exceeds bounds"| OUTSIDE
```

---

## Chapter 9 — Case Studies

### 9.1 Case Study: Autonomous AI for Diabetic Retinopathy Screening

| Aspect | Detail |
|--------|--------|
| Device | IDx-DR (Digital Diagnostics) — first FDA-authorized autonomous AI diagnostic |
| Regulatory pathway | De Novo classification (2018) — no predicate; new device type; Class II with special controls |
| Intended use | Autonomous detection of more-than-mild diabetic retinopathy (mtmDR) and diabetic macular edema (DME) in adults; no specialist interpretation needed |
| Training data | >1 million retinal images; multi-ethnic; multiple camera types; expert grading against 3-field stereoscopic fundus photography (gold standard); multi-reader grading panels |
| Clinical validation | Prospective, multi-site pivotal study (10 primary care sites; 900 patients); compared AI output to expert reading center (Wisconsin Fundus Photograph Reading Center) |
| Performance | **mtmDR**: Sensitivity 87.2%, Specificity 90.7%; **Vision-threatening DR**: Sensitivity 97.6% |
| Key regulatory aspects | (1) Autonomous = no physician interpretation (device makes the screening decision). (2) Designed for PRIMARY CARE (non-ophthalmologists can use). (3) Special controls: imageability assessment (AI determines if image quality adequate); referral (any positive → refer to ophthalmologist); training requirements for operators. (4) AI cannot be overridden — output is binary (refer / no refer). |
| Post-market | Real-world performance monitoring; published real-world studies confirming performance; PCCP-like approach for updates (expand to new camera types) |
| Impact | Enabled diabetic retinopathy screening in primary care settings where ophthalmologists are unavailable; reduced screening access barriers; particularly impactful in rural/underserved areas |

### 9.2 Case Study: AI Bias Detection and Remediation

| Aspect | Detail |
|--------|--------|
| Scenario | AI-based chest X-ray diagnostic tool deployed across 15 hospitals; post-market monitoring detects performance disparity |
| Discovery | Performance monitoring dashboard shows: AUC for pneumonia detection: overall 0.94; White patients: 0.95; Black patients: 0.89; Hispanic patients: 0.87. Sensitivity gap: 6-8 percentage points for minority groups. |
| Root cause analysis | (1) Training data composition: 72% White, 15% Black, 8% Hispanic, 5% other — not representative of deployment population (hospital serves 40% Black, 25% Hispanic). (2) Labeling bias: radiology reports used as labels reflected known disparities in detection rates. (3) Feature bias: model learned spurious correlations with hardware/positioning differences correlated with demographics (different sites serving different populations used different equipment). |
| Remediation (within PCCP) | (1) **Data rebalancing**: acquired additional 15,000 chest X-rays from high-minority sites; balanced training set by demographic group. (2) **Label audit**: re-annotated subset (5,000 images) with diverse expert panel; corrected labeling discrepancies. (3) **Debiasing techniques**: applied calibration per subgroup; added fairness constraint to loss function; tested counterfactual fairness. (4) **Retrained model**: new model AUC: overall 0.93, White 0.94, Black 0.92, Hispanic 0.91 — maximum gap reduced from 8% to 3%. (5) **Validation**: validated on new diverse test set (balanced demographics); all subgroups above minimum threshold. |
| PCCP compliance | Modification within PCCP bounds: same architecture; same intended use; retraining with additional data (explicitly allowed); performance meets or exceeds minimum thresholds per all subgroups; no new submission required; documented in PCCP update records. |
| Ongoing monitoring | Enhanced monitoring: real-time subgroup performance tracking; automated alerts if any demographic subgroup AUC drops below 0.88; quarterly fairness reports; annual diverse test set evaluation. |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact on Medical AI |
|-------|----------|---------------------|
| **Foundation models in medicine** | Now-2026 | Large pre-trained models (Med-PaLM, BiomedCLIP) fine-tuned for specific tasks; reduces training data needs; raises new regulatory questions (can you validate a model you didn't train from scratch?) |
| **Generative AI in healthcare** | Now | Report generation; clinical summarization; image synthesis for training data augmentation; raises hallucination risk (false clinical content) |
| **Federated learning** | Now-2027 | Train models across institutions without sharing data; privacy-preserving; addresses data access limitations; regulatory: how to validate federated model? |
| **Continuous learning (truly adaptive AI)** | 2025-2028 | Models that learn continuously from deployment data; PCCP evolution needed; major regulatory challenge (how to ensure safety of continuously changing model?) |
| **Multimodal AI** | Now-2026 | Models combining imaging + EHR + genomics + labs; richer clinical context; regulatory challenge: multiple data sources = complex validation |
| **Causal AI** | 2025-2030 | Moving from correlation to causation; treatment effect prediction; more robust under distribution shift; but harder to validate |
| **Real-world evidence (RWE)** | Now | Using deployment data to validate/update AI performance; reduces need for prospective trials; accepted by FDA for certain applications |
| **Digital twins** | 2025-2030 | Patient-specific computational models; simulation-based validation; synthetic data for training; regulatory: can synthetic data support submissions? |
| **Explainable AI (XAI) requirements** | Now (AI Act 2026) | EU AI Act requires transparency/explainability; drives adoption of interpretable architectures; model cards; decision audit trails |
| **Post-quantum for AI security** | 2027-2030 | Protecting model IP and patient data in AI pipelines against quantum computing threats |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is the difference between a "locked" AI algorithm and an "adaptive" AI algorithm in the context of FDA regulation?  
**A:** A **locked algorithm** produces the same output every time for a given input — the model does not change after regulatory clearance/approval. Any change to the model (retraining, architecture change, new output) requires a new regulatory submission (new 510(k) or PMA supplement). This is the traditional approach: device is validated, cleared, and frozen. An **adaptive algorithm** is designed to change after clearance — it may be retrained with new data, updated to handle new imaging equipment, or refined based on real-world performance. To handle adaptive AI, FDA created the **Predetermined Change Control Plan (PCCP)**. With an approved PCCP, the manufacturer can make pre-specified types of changes following a pre-specified protocol without filing a new submission for each change. The PCCP defines: what can change (e.g., model weights via retraining); how it will change (retraining protocol); how performance will be verified (validation methodology and minimum thresholds); and boundaries (what changes exceed PCCP scope and DO require new submission). Key distinction: adaptive ≠ autonomous learning in deployment. Even adaptive AI typically means periodic, controlled updates by the manufacturer — not unsupervised real-time learning from each patient.

**Q2:** Explain what "automation bias" is and how it relates to AI/ML medical devices.  
**A:** Automation bias is the tendency for humans to over-rely on automated/AI outputs, reducing their own vigilance and critical thinking. In medical AI context: a radiologist using AI-assisted detection may: (1) Accept AI-flagged findings without sufficient independent verification (false positive accepted → unnecessary biopsy). (2) Dismiss or overlook findings NOT flagged by AI (false negative not caught → missed cancer). Studies show that when AI provides incorrect output, physicians with AI sometimes perform WORSE than physicians without AI — the AI's error becomes the physician's error. Mitigation strategies: (1) Human factors design — present AI output in a way that encourages verification (e.g., show AI finding AFTER physician's initial review; require explicit acceptance). (2) Confidence communication — show uncertainty/confidence levels; don't present all AI outputs with equal weight. (3) Known limitation disclosure — clearly communicate when AI may be unreliable (low-quality images, unusual anatomy). (4) Training — clinicians trained on AI limitations and failure modes; encouraged to maintain independent clinical judgment. (5) Workflow design — some regulatory submissions demonstrate performance of physician + AI as a TEAM (reader study), specifically measuring whether AI helps or hurts physician accuracy. This is why FDA GMLP Principle #7 states "Focus on the performance of the human-AI team" — evaluating AI alone is insufficient.

### Tier 2: Mid-Level

**Q3:** You are developing a PCCP for an AI-based chest X-ray triage device. What would you include and what would be your performance boundaries?  
**A:** PCCP structure for AI chest X-ray triage: **Description of Modifications**: (1) Periodic retraining with additional annotated chest X-rays (new sites, new equipment, new patient populations). (2) Threshold adjustment based on real-world performance data. (3) Preprocessing pipeline updates for new DICOM transfer syntaxes or imaging parameters. **Modification Protocol**: (a) Data requirements: minimum 5,000 new annotated images per retraining cycle; annotation by ≥3 board-certified radiologists; consensus adjudication; demographic balance within 10% of intended use population. (b) Training protocol: same architecture (fixed); hyperparameter bounds defined (learning rate ∈ [1e-5, 1e-3]); regularization requirements; maximum training epochs; reproducibility seed. (c) Testing protocol: validate on fixed held-out multi-site test set (N=2,000; never used in training; demographics locked); plus new site-specific test set if expanding to new sites. **Performance Boundaries (Guardrails)**: minimum AUC ≥ 0.92 (critical finding detection); minimum sensitivity ≥ 87% for each critical finding class (pneumothorax, tension pneumothorax, large pleural effusion); specificity ≥ 80%; no demographic subgroup drops below sensitivity 82%; median time-to-result < 60 seconds; model size must run on approved hardware. **What EXCEEDS PCCP (requires new submission)**: changing from triage to diagnosis (intended use change); adding new finding categories (e.g., adding lung nodule detection); architecture change (e.g., switching from ResNet to Vision Transformer); expansion to non-chest imaging; any subgroup performance below minimum threshold that cannot be resolved within protocol; failure of automated quality checks.

### Tier 3: Senior

**Q4:** How would you architect a post-market performance monitoring system for an AI medical device deployed across 50 hospitals, and how would it support PCCP compliance?  
**A:** [Comprehensive answer covering: (1) **Data collection infrastructure**: automated performance capture at each site; inference logging (input hash, model version, output, confidence, latency); outcome correlation (link AI output to eventual diagnosis — via pathology, follow-up imaging, clinical outcome at 30/90/365 days); de-identified performance telemetry aggregated centrally. (2) **Performance dashboards**: real-time metrics (AUC, sensitivity, specificity) calculated on rolling windows (7-day, 30-day, 90-day); stratified by: site, equipment type, patient demographics, finding type; statistical process control charts with control limits tied to PCCP thresholds. (3) **Distribution shift detection**: input data monitoring — statistical tests (KS test, MMD) comparing current input distributions to training distribution; feature drift monitoring (imaging parameters, patient characteristics); output distribution monitoring (if positive rate suddenly shifts, investigate). (4) **Automated alerting**: alert if any PCCP threshold approached (warning at 95% of minimum; critical at minimum); alert if subgroup performance diverges; alert if data distribution shift detected; alert if throughput/latency degrades; escalation path defined (Level 1: data science team; Level 2: regulatory; Level 3: executive). (5) **PCCP compliance engine**: tracks each modification against PCCP bounds; validates that modification protocol was followed (checklist); verifies performance assessment completed; generates PCCP records automatically; flags if modification approaches or exceeds PCCP boundaries; maintains complete audit trail for FDA inspection. (6) **Retraining pipeline (supports PCCP)**: when monitoring indicates performance degradation or new data available — automated retraining pipeline triggered per PCCP protocol; validation against locked test set; comparison to PCCP thresholds; human-in-the-loop approval before deployment; canary deployment (rollout to subset of sites first; monitor; then full deployment). (7) **Regulatory reporting**: automated generation of periodic PCCP update reports; adverse event correlation (did AI output contribute to any reported adverse event?); field safety data integration; ready for FDA inspection at any time.]

---

## Chapter 12 — Cheat Sheet & Quick Reference

### AI/ML Medical Device Regulatory Decision Tree

```
Is it software?
  └── Does it provide clinical decision support?
       ├── YES → Does it meet ALL 4 CDS exemption criteria (21st Century Cures)?
       │    ├── YES → Not a medical device (exempt)
       │    └── NO → It's SaMD → Continue below
       └── NO → Standard device rules apply

Is AI/ML involved?
  └── YES → Classification:
       ├── CADe (detection; physician reviews) → 510(k), Class II
       ├── CADx (diagnosis/recommendation) → 510(k) or De Novo, Class II
       ├── CADt (triage/prioritization) → 510(k), Class II  
       └── Autonomous (no physician needed) → De Novo or PMA, Class II/III

Will algorithm change after clearance?
  ├── NO (locked) → Standard pathway; any change = new submission
  └── YES (adaptive) → Include PCCP in submission
```

### GMLP 10 Principles (Quick Memory Aid)

```
1.  Multi-disciplinary team          (Not just ML engineers)
2.  Good SWE practices               (IEC 62304; version control)
3.  Representative data              (Reflects intended patients)
4.  Independent train/test           (No leakage)
5.  Best available ground truth      (Expert consensus; gold standard)
6.  Appropriate model complexity     (Fit for data size)
7.  Human-AI team performance        (Not just AI alone)
8.  Clinically relevant testing      (Real-world conditions)
9.  Clear user information           (Limitations disclosed)
10. Monitor deployed models          (Post-market surveillance)
```

### PCCP Quick Template

```
1. WHAT can change:      [Retraining; threshold tuning; preprocessing; NOT architecture/intended use]
2. HOW it will change:   [Protocol: data requirements; training parameters; validation method]
3. HOW to verify:        [Test set; metrics; minimum thresholds; subgroup analysis]
4. BOUNDARIES:           [What exceeds PCCP → requires new submission]
5. RECORDS:              [Documentation for each modification; audit trail]
```

### EU AI Act — Medical AI Quick Reference

```
Classification:  HIGH-RISK (medical devices under MDR Annex II)
Conformity:      Via MDR Notified Body assessment (Art. 43(3))
Key requirements: Risk management; data governance; documentation;
                  transparency; human oversight; accuracy/robustness
Timeline:        Published 2024; high-risk provisions apply Aug 2026
```

### Key Performance Metrics

```
Detection (CADe):     Sensitivity ≥ 85-95%; Specificity ≥ 80-90%; AUC ≥ 0.90
Diagnosis (CADx):     AUC ≥ 0.90; Non-inferior to physician; Reader study required
Triage (CADt):        Sensitivity ≥ 90%; Time reduction demonstrated
Segmentation:         Dice ≥ 0.85; Hausdorff distance < clinical threshold
Subgroup equity:      Max performance gap < 5-10% across demographics
```

---

*End of Document — 09_AI_ML_In_Medical_Devices.md*
