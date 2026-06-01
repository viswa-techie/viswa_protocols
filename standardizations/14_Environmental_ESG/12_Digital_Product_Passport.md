# Digital Product Passport — EU ESPR & DPP Framework

**Topic:** Digital Product Passport (DPP) — EU framework for providing lifecycle product information via digital means, enabling circular economy, transparency, sustainability verification, and informed decision-making across the value chain  
**Standard:** EU Ecodesign for Sustainable Products Regulation (ESPR) — Regulation (EU) 2024/1781; Delegated acts per product category (upcoming); ISO/IEC standards for data carriers (QR/RFID); GS1 standards for identification  
**SDO:** European Commission; CEN/CENELEC (standardization); GS1 (identification); CIRPASS (pilot consortium); Catena-X (automotive); Battery Pass (batteries)  
**Audience:** Product designers, sustainability engineers, compliance managers, supply chain professionals, IT/digital transformation leads, quality managers, circular economy specialists  
**Prerequisites:** EU regulatory framework, product lifecycle concepts, data management/databases, QR codes/RFID, sustainability metrics (LCA, carbon footprint), supply chain traceability

---

## Chapter 1 — Historical Context & Origin Story

### 1.1 Timeline

| Year | Event | Significance |
|------|-------|-------------|
| 2009 | EU Ecodesign Directive (2009/125/EC) | First EU ecodesign framework (energy-related products only); energy efficiency labeling/requirements; did NOT include material/circularity requirements |
| 2015 | EU Circular Economy Action Plan (first) | EU strategic priority: move from linear ("take-make-dispose") to circular economy; identified need for product information |
| 2019 | EU Green Deal announced | Overarching strategy: climate neutrality by 2050; circular economy; sustainable products; identified Digital Product Passport as key enabler |
| 2020 | EU Circular Economy Action Plan (second, updated) | Specifically proposes Digital Product Passport; sustainable product policy framework; electronics and textiles priority |
| 2022 | EU Commission proposes ESPR (Ecodesign for Sustainable Products Regulation) | Replaces 2009 Ecodesign Directive with vastly expanded scope (nearly all products); introduces DPP as mandatory requirement |
| 2022 | CIRPASS consortium launched (EU-funded pilot) | 30+ organizations piloting DPP for batteries, electronics, textiles; developing architecture and standards |
| 2023 | **EU Battery Regulation (2023/1542)** adopted | First product-specific DPP: Battery Passport mandatory from 2027; pilot/precursor for broader DPP |
| 2024 | **ESPR adopted** — Regulation (EU) 2024/1781 (published July 2024) | Enters into force; DPP framework established; delegated acts to specify per product category |
| 2024-2025 | First delegated acts in preparation | Priority product categories: textiles (esp. apparel/footwear); electronics (smartphones, tablets); batteries (already done); iron/steel; furniture |
| 2025-2026 | DPP pilot implementations | Industry pilots for electronics, textiles, construction products; testing interoperability |
| 2027 | Battery Passport mandatory | EV + industrial batteries >2 kWh must have digital passport (first operational DPP) |
| 2027-2030 | DPP rollout per product category | Delegated acts specify DPP requirements for each product group as they are published |
| 2030+ | Full DPP ecosystem operational | Mature infrastructure; cross-product interoperability; integration with customs, market surveillance, recyclers |

### 1.2 Why Digital Product Passport?

| Problem | How DPP Solves It |
|---------|-------------------|
| Consumers can't verify sustainability claims | DPP provides verified, standardized environmental data accessible via QR scan |
| Repairers don't have product information | DPP includes repair instructions, spare parts info, disassembly guides |
| Recyclers don't know product composition | DPP provides material composition, hazardous substances, disassembly instructions |
| Regulators can't verify compliance efficiently | DPP provides machine-readable compliance data; enables digital market surveillance |
| Supply chain sustainability is opaque | DPP traces materials, carbon footprint, due diligence through supply chain |
| Circular economy needs material knowledge | DPP enables second-hand markets, refurbishment, material recovery with better information |
| Green claims are unsubstantiated | DPP provides data basis for verifiable environmental claims (Green Claims Directive link) |

---

## Chapter 2 — Standard Architecture & Structure

### 2.1 ESPR Framework — DPP Architecture

```mermaid
graph TB
    subgraph "Physical Product"
        PROD[Product<br/>━━━━━━━━━━━<br/>With data carrier<br/>(QR code / RFID / NFC)<br/>• Unique product identifier<br/>• Link to digital passport]
    end
    
    subgraph "Digital Product Passport"
        DPP_STATIC[Static Information<br/>━━━━━━━━━━━<br/>• Product identification<br/>• Manufacturer<br/>• Materials/composition<br/>• Carbon footprint<br/>• Compliance data<br/>• Durability/repairability<br/>• Recycled content<br/>• Disassembly instructions]
        
        DPP_DYNAMIC[Dynamic Information<br/>━━━━━━━━━━━<br/>• Ownership changes<br/>• Repair/maintenance history<br/>• State of health (batteries)<br/>• End-of-life status<br/>• Refurbishment records]
    end
    
    subgraph "DPP Infrastructure"
        REGISTRY[DPP Registry<br/>━━━━━━━━━━━<br/>• Central/federated<br/>• Product ID → passport URL<br/>• Operated by EU]
        
        HOSTING[Data Hosting<br/>━━━━━━━━━━━<br/>• Manufacturer systems<br/>• OR authorized platforms<br/>• Decentralized storage<br/>• API access]
    end
    
    subgraph "Users"
        CONSUMER[Consumer<br/>Scan QR → see<br/>sustainability info;<br/>repair guides; comparison]
        
        REPAIRER[Repair Professional<br/>Access repair manuals;<br/>spare parts; diagnostics]
        
        RECYCLER[Recycler<br/>Material composition;<br/>disassembly instructions;<br/>hazardous substances]
        
        AUTHORITY[Market Surveillance<br/>Compliance verification;<br/>digital enforcement;<br/>customs check]
        
        BUSINESS[Business Partner<br/>Supply chain data;<br/>due diligence;<br/>procurement decisions]
    end
    
    PROD -->|Data carrier scanned| DPP_STATIC & DPP_DYNAMIC
    DPP_STATIC & DPP_DYNAMIC --> HOSTING
    HOSTING <--> REGISTRY
    REGISTRY --> CONSUMER & REPAIRER & RECYCLER & AUTHORITY & BUSINESS
```

### 2.2 Key ESPR/DPP Requirements

| Requirement Area | ESPR Article | Content |
|-----------------|:---:|---------|
| **Ecodesign requirements** | Art 5-7 | Performance requirements (minimum/maximum thresholds); information requirements (DPP data); both set via delegated acts per product category |
| **Digital Product Passport** | Art 8-13 | DPP mandatory for products with delegated acts; data carrier on product; unique product identifier; interoperability; access rights |
| **Substance of concern** | Art 7(5) | Products must track and declare substances of concern (links to REACH/SCIP) in DPP |
| **Recycled content** | Art 7(2)(b) | Minimum recycled content requirements possible via delegated acts; declared in DPP |
| **Carbon footprint** | Art 7(2)(a) | Product carbon footprint requirements via delegated acts; declared in DPP |
| **Durability** | Art 7(2)(c) | Minimum durability/reliability requirements; lifetime information in DPP |
| **Repairability** | Art 7(2)(d) | Repair information; spare parts availability; repairability score possible; in DPP |
| **Recyclability** | Art 7(2)(e) | Design for recycling; recyclability assessment; disassembly information in DPP |
| **Destruction ban** | Art 20 | Prohibition on destruction of unsold consumer products (textiles, footwear, electronics) |
| **Market surveillance** | Art 59-70 | Digital tools for market surveillance using DPP data; customs integration |

### 2.3 DPP Data Categories (Expected per ESPR Art 9)

| Category | Data Points | Access |
|----------|-------------|:------:|
| **Product identification** | Unique identifier; product name; model; batch/serial; manufacturer; manufacturing date/location; GTIN/SKU | Public |
| **Materials & composition** | Material composition; substances of concern; critical raw materials; recycled content (%) | Public (some restricted) |
| **Environmental footprint** | Carbon footprint (kgCO₂e); other environmental impacts (PEF-based); energy label class | Public |
| **Durability & performance** | Expected lifetime; warranty; performance parameters; degradation rate | Public |
| **Repairability** | Repairability score; repair instructions; spare parts list & availability; disassembly instructions; firmware update policy | Public + Professional |
| **Compliance** | CE marking; EU DoC reference; test reports reference; regulation compliance statements | Public + Authority |
| **Circularity** | Recyclability assessment; disassembly instructions (for recyclers); material recovery potential; second-life suitability | Professional + Recycler |
| **Supply chain** | Due diligence (summary); supply chain actors (where relevant); origin of critical materials | Restricted (Authority) |
| **End-of-life handling** | Collection instructions; take-back scheme; proper disposal; hazardous waste classification | Public |

---

## Chapter 3 — Technical Deep Dive

### 3.1 Unique Product Identifier

| Aspect | Detail |
|--------|--------|
| **Purpose** | Globally unique identifier for each product instance (or model/batch, depending on granularity) enabling unambiguous link to its digital passport |
| **Granularity options** | Per individual item (serial-level; for high-value products like EVs, batteries); per batch (for mass-produced items); per model (for very simple products) |
| **Identifier systems** | GS1 standards (GTIN for product model; SGTIN for serialized items; SSCC for logistics); ISO/IEC 15459 (unique identification); UUID/URN |
| **Format** | Machine-readable; encodable in data carrier (QR, RFID); resolvable to passport URL (e.g., via GS1 Digital Link) |
| **Example** | GS1 Digital Link: `https://id.gs1.org/01/09506000134352/21/ABC123` → resolves to DPP for that specific product |

### 3.2 Data Carrier Technologies

| Technology | How It Works | Pros | Cons | Best For |
|-----------|-------------|------|------|----------|
| **QR Code** | 2D barcode; optical scanning by smartphone camera | Universal (any smartphone); low cost; no special hardware; printable | Static (can't update data on carrier itself); requires visibility; can be damaged | Consumer-facing products; packaging; labels |
| **NFC (Near Field Communication)** | Short-range wireless (≤10 cm); embedded chip | Durable (embedded); no line-of-sight needed; rewritable; secure authentication possible | Higher cost per tag (€0.05-0.50); requires NFC reader (most smartphones have); limited data storage on tag | Electronics; apparel tags; high-value items |
| **RFID (UHF)** | Longer-range wireless (up to 10m); embedded chip | Bulk reading; long range; no line-of-sight; durable | Requires dedicated reader (not in all smartphones); higher tag cost; interference with metals/liquids | Logistics; pallets; industrial equipment; warehouse management |
| **Data Matrix** | 2D barcode (smaller than QR) | Very compact; scannable; laser-markable directly on product | Requires higher resolution scanning; less common in consumer apps | Small components; direct part marking; industrial |
| **Digital watermark** | Invisible pattern in product surface/packaging | Invisible; doesn't affect aesthetics; entire surface is scannable | Requires specific app/reader; less established | Packaging (Digimarc/HolyGrail 2.0 for recycling) |

### 3.3 DPP Technical Infrastructure

| Layer | Component | Technology Options |
|:-----:|-----------|-------------------|
| **Data carrier** | Physical link on product | QR code (primary); NFC; RFID; Data Matrix |
| **Identifier resolution** | Convert scanned identifier to passport data URL | GS1 Digital Link resolver; DNS-like resolution service; EU DPP registry |
| **Data hosting** | Store and serve passport data | Manufacturer-hosted (API); authorized DPP service providers; decentralized (IPFS/blockchain anchored); EU-operated central registry |
| **Data model** | Standardized structure of passport information | Product-category-specific schema; based on ESPR delegated acts; machine-readable (JSON-LD, RDF, or similar) |
| **Access control** | Role-based access to different data categories | Authentication (eIDAS-compatible); authorization rules per data field; public vs. restricted vs. authority-only |
| **Interoperability** | Systems can exchange and understand DPP data | Open standards; APIs (RESTful); common vocabulary/ontology; EU-wide registry linking all passports |
| **Verification** | Trust that data is authentic and unaltered | Digital signatures (manufacturer signs data); timestamps; blockchain anchoring (optional); accredited verifier attestations |
| **User interface** | How users access passport information | Web application (link from QR); mobile app; B2B API integration; authority dashboard |

### 3.4 DPP for Electronics — Expected Data Points

| Data Point | Basis | Format |
|-----------|-------|:------:|
| Product model + serial number | Identification | Text/code |
| Manufacturer + location | Identification | Text |
| Energy efficiency class (if applicable) | EU Energy Label regulation | A to G |
| Carbon footprint (production) | EU PEF methodology; ISO 14067 | kgCO₂e |
| Recycled content (%) | Per material type | % per material |
| Repairability score | French-style index (expected EU-wide adoption) | Score /10 or letter grade |
| Spare parts availability | Duration + list | Years + catalog reference |
| Software support period | Duration of security/feature updates | Years from placement |
| Expected product lifetime | Durability testing/design target | Years / cycles |
| Battery information (if applicable) | EU Battery Regulation cross-reference | Link to battery passport |
| Substances of concern | REACH/SCIP link; IPC-1752 data source | Substance list + concentrations |
| WEEE category | Directive 2012/19/EU | Category code |
| Disassembly instructions | Recycler access | Document/video link |
| Material composition (simplified) | % major material groups | Pie chart / table |
| Compliance declarations | CE marking; EU DoC | Document references |

---

## Chapter 4 — Implementation Guide

### 4.1 DPP Implementation Roadmap for Manufacturers

| Phase | Timeline (relative to delegated act) | Activities | Deliverables |
|:-----:|:---:|---------|-------------|
| **1. Assess & Plan** | -24 to -18 months | Monitor delegated act development for your product category; gap assessment (what data you have vs. what DPP requires); identify system integration needs; budget & resource planning | Gap analysis; project plan; budget |
| **2. Data Foundation** | -18 to -12 months | Establish data sources for all required DPP fields: LCA/carbon footprint, material composition (IPC-1752A), repairability info, performance data, compliance data; clean and structure existing data | Data inventory; source mapping; quality plan |
| **3. System Architecture** | -12 to -8 months | Select DPP platform/technology; design data model per product schema; plan integration with ERP/PLM/QMS; select data carrier (QR/NFC); plan identifier system (GS1 or alternative); security/access design | Architecture design; platform selection; integration plan |
| **4. Build & Integrate** | -8 to -4 months | Implement integrations (ERP→DPP; PLM→DPP; LCA tool→DPP; compliance system→DPP); implement data carrier application (label printing; NFC embedding); build API endpoints; implement access control | Working system; integrations tested; pilot-ready |
| **5. Pilot & Validate** | -4 to -1 month | Pilot with limited product range; validate all data fields populated correctly; test user experience (consumer scan; recycler access; authority check); verify interoperability with EU registry; fix issues | Pilot results; validated system; user testing feedback |
| **6. Go-Live & Scale** | 0 (delegated act applies) | Full deployment across product range; all new products have DPP; data carrier on all units; registered in EU system; support processes (updates; corrections; end-of-life updates) | Compliant DPP for all in-scope products |
| **7. Maintain & Update** | Ongoing | Keep data current (product changes; regulatory updates; software support changes); handle lifecycle events (repair; resale; recycling); respond to authority queries; annual review | Maintained, accurate passports throughout product life |

### 4.2 Data Source Mapping (Electronics)

| DPP Data Point | Internal Source | External Source | Challenge Level |
|---------------|:---:|:---:|:---:|
| Product ID / serial | ERP; MES; production system | GS1 (GTIN allocation) | Low (usually exists) |
| Manufacturer details | Master data (ERP) | — | Low |
| Carbon footprint | LCA tool (SimaPro; GaBi; openLCA) | LCI databases (ecoinvent) | **High** (requires LCA capability) |
| Material composition | IPC-1752A declarations; BOM + supplier data | Compliance platform (BOMcheck; Assent) | **Medium-High** (supply chain data collection) |
| Substances of concern | Compliance system; SCIP database | ECHA (SVHC list) | Medium |
| Recycled content | Material suppliers; procurement records | Supply chain tracking | **High** (traceability challenge) |
| Energy efficiency | Test lab; energy label program | — | Low-Medium (if already energy-labeled) |
| Repairability score | Design engineering; service organization | Repair manual system | **Medium** (methodology still evolving) |
| Spare parts availability | Service/spare parts system | — | Low-Medium |
| Software support period | Product management decision | — | Low (decision, not measurement) |
| Durability/lifetime | Reliability testing; warranty data | — | Medium |
| Disassembly instructions | Design engineering; service documentation | — | Medium (needs creation/formatting) |
| Compliance (CE/DoC) | Quality management system | Notified body certificates | Low (usually exists) |
| Due diligence | Supply chain management; ESG team | Supplier audits; certifications | **High** (new for many companies) |

### 4.3 GS1 Digital Link for DPP

| Aspect | Detail |
|--------|--------|
| **What** | GS1 Digital Link is a web-standard way to encode GS1 identifiers (GTIN, serial, batch) in a URL that can be resolved to multiple types of information including DPP data |
| **Format** | `https://[resolver-domain]/01/[GTIN]/21/[serial]` — encoded in QR code |
| **Resolution** | When scanned, the URL resolves (via link resolver) to appropriate content based on context (consumer gets product page; authority gets compliance data; recycler gets disassembly info) |
| **Benefit** | Single QR code serves multiple purposes (marketing; DPP; logistics; anti-counterfeiting); leverages existing GS1 infrastructure; web-native (no app needed) |
| **DPP integration** | GS1 Digital Link is leading candidate for DPP data carrier standard; URI structure maps to DPP identifier requirements; resolver infrastructure can route to DPP hosting systems |
| **Example** | `https://id.example.com/01/09506000134352/21/SN12345` → QR code → scan → resolver checks context → returns DPP JSON data (for B2B API) OR renders DPP web page (for consumer) |

---

## Chapter 5 — Verification & Compliance

### 5.1 DPP Compliance Requirements

| Requirement | Detail | Penalty for Non-Compliance |
|-------------|--------|:---:|
| **DPP exists** | All in-scope products must have functional DPP before placing on EU market | Cannot legally place product on EU market; market surveillance action |
| **Data carrier present** | Physical data carrier (QR/NFC) on product and packaging | Non-compliant product; withdrawal from market possible |
| **Data complete** | All mandatory data fields populated per delegated act requirements | Non-compliant; market surveillance action |
| **Data accurate** | Information must be truthful and verifiable; no misleading claims | Penalties under national enforcement; Green Claims Directive implications |
| **Data accessible** | Passport must be accessible for minimum product lifetime + X years (to be defined); available 24/7 via internet | Non-compliant; manufacturer obligation to maintain |
| **Interoperable** | DPP must be interoperable with EU DPP registry; use standardized data formats and identifiers | Registration failure; not discoverable by authorities |
| **Updated** | Dynamic data must be kept current (where applicable; e.g., battery SOH) | Outdated passport; non-compliance with update requirements |
| **Access control** | Role-based access correctly implemented (public vs. restricted data correctly segregated) | Data protection violations; or insufficient transparency |

### 5.2 Market Surveillance Using DPP

| Capability | How DPP Enables It |
|-----------|-------------------|
| **Remote compliance check** | Authorities scan QR code at border/market; immediately access compliance data without physical testing |
| **Customs integration** | DPP data linked to customs declarations; automated screening for non-compliant products at EU borders |
| **Cross-border cooperation** | All EU authorities access same DPP infrastructure; real-time information sharing |
| **Targeted enforcement** | Data analytics on DPP data identify patterns (e.g., products with missing data; suspicious carbon footprint claims); focus inspection on high-risk |
| **Consumer complaints** | Consumers can report discrepancies between product and DPP data; crowdsourced surveillance |
| **Recall management** | Serial-level identification enables targeted recalls; track affected units through supply chain |

---

## Chapter 6 — Regional & Sectoral Context

### 6.1 DPP by Product Sector (Priority Categories)

| Sector | Timeline (est.) | Key DPP Data Points | Specifics |
|--------|:---:|------|------|
| **Batteries** | 2027 (live) | Carbon footprint; SOH; chemistry; recycled content; due diligence; disassembly; capacity | Battery Regulation (2023/1542) — most advanced DPP; Art 77 |
| **Textiles & apparel** | 2027-2028 | Fiber composition; country of manufacturing; size; durability; repairability; chemical use; recycled content; care instructions | Very high consumer visibility; fashion industry challenge; microplastics |
| **Electronics (smartphones, tablets)** | 2028-2029 (est.) | Repairability score; software support; spare parts; energy efficiency; battery info; materials; lifetime; carbon footprint | Already subject to energy label + repair requirements; DPP adds transparency layer |
| **Iron & steel** | 2027-2028 | Carbon footprint (per ton); recycled content; production route (BF-BOF vs. EAF); origin | Industrial B2B; linked to CBAM (Carbon Border Adjustment Mechanism) |
| **Construction products** | 2028-2030 | Environmental performance (EN 15804 EPD data); durability; recycled content; hazardous substances; fire performance | Links to Construction Products Regulation revision; EPD-based |
| **Furniture** | 2028-2029 | Durability; repairability; material composition; recycled content; disassembly; chemical safety | Circular economy focus; IKEA-type sustainability demands |
| **Tires** | 2028-2029 | Abrasion; durability; retreading suitability; recycled content; carbon footprint; microplastic emission | Safety + sustainability; UNECE regulations reference |
| **Chemicals** (in products) | Cross-cutting | Substances of concern tracking throughout value chain; linked to REACH reform | Horizontal requirement across product DPPs |

### 6.2 International DPP Landscape

| Region/Initiative | Status | Approach |
|-------------------|:------:|---------|
| **EU (ESPR/DPP)** | Regulation adopted (2024); delegated acts in development | Mandatory; regulatory; product-specific; comprehensive (environment + circularity + safety) |
| **US** | No federal DPP mandate; sector-specific (FDA UDI for medical devices; EPA proposals) | Voluntary; industry-driven; repair/right-to-repair legislation (state-level) may include information requirements |
| **China** | Green product passport discussions; existing certification marks (China Energy Label); Carbon label pilots | Government-directed; linked to carbon peaking goals; digital infrastructure investments |
| **Japan** | CFP (Carbon Footprint of Products) programs; chemical information (chemSHERPA) | Voluntary; industry consortia; environmental labeling |
| **Catena-X (automotive)** | Operational (2023+) | Industry-driven DPP for automotive value chain; PCF exchange; material traceability; battery passport; based on Gaia-X/IDSA architecture |
| **GBA (Global Battery Alliance)** | Battery Passport (industry initiative) | Industry-wide battery passport; complements EU Battery Regulation; broader than EU (global scope) |
| **CIRPASS (EU pilot)** | Completed 2024 | EU-funded pilot project testing DPP architecture for batteries, electronics, textiles; recommendations for implementation |

---

## Chapter 7 — Comparison

### 7.1 DPP vs. Existing Product Information Systems

| System | Purpose | Scope | DPP Relationship |
|--------|---------|:-----:|:---:|
| **EU Energy Label** | Energy efficiency communication to consumers | Energy-consuming products; single metric (A-G) | DPP will INCLUDE energy label data + much more |
| **EPD (Environmental Product Declaration)** | LCA-based environmental performance (B2B) | Voluntary; ISO 14025; specific PCR per product | DPP environmental data may reference/include EPD; EPD methodology feeds carbon footprint |
| **CE Marking** | Conformity with EU legislation | All regulated products; indicates compliance | DPP will INCLUDE CE/DoC reference; expand accessible information |
| **Material Safety Data Sheet (SDS)** | Chemical hazard communication | Chemicals and mixtures; GHS-based | DPP for chemical-containing products will reference relevant safety information |
| **IPC-1752A / Material declarations** | Supply chain substance data exchange | Electronics components; B2B | DPP material composition data SOURCED FROM material declarations; IPC-1752 is upstream data |
| **IMDS (automotive)** | Full material disclosure for automotive | Automotive components (mandatory) | Automotive DPP (Catena-X) builds on IMDS data; broader scope |
| **GTIN / barcode** | Product identification for retail/logistics | All retail products; no environmental data | DPP USES existing GTIN as identifier base (GS1 Digital Link); extends with sustainability data |
| **Product datasheet** | Technical specifications | Manufacturer-provided; unstructured | DPP standardizes and makes accessible; machine-readable; adds sustainability layer |

### 7.2 DPP Platform/Architecture Approaches

| Approach | Architecture | Pros | Cons |
|----------|:---:|------|------|
| **Centralized (EU-operated)** | EU runs single DPP database; all data stored centrally | Simple; guaranteed availability; authority control; uniform | Single point of failure; data sovereignty concerns; scalability challenge; manufacturer dependence on EU systems |
| **Decentralized (federated)** | Manufacturers host own data; EU registry only holds pointers (ID → URL mapping) | Manufacturer controls data; scalable; no single failure point; leverages existing IT | Availability depends on manufacturer systems; interoperability harder; varied data quality |
| **Hybrid (registry + federated hosting)** | EU registry for discovery + minimum public data; detailed data hosted by manufacturer or authorized providers | Balance: discoverability + control; EU ensures minimum access; scalable | More complex architecture; needs strong standards for interoperability |
| **Blockchain-anchored** | Data hosted off-chain; hash/anchor on blockchain for integrity proof | Immutability; trust without central authority; tamper-proof audit trail | Complexity; energy concerns (depending on chain); not needed for all data; still needs off-chain storage |

**Expected EU approach:** Hybrid — EU DPP registry for discovery/resolution + decentralized hosting with mandatory interoperability standards.

---

## Chapter 8 — Mermaid Architecture Diagrams

### 8.1 DPP Data Flow — From Manufacturing to End-of-Life

```mermaid
graph LR
    subgraph "Manufacturing"
        DESIGN[Design Phase<br/>━━━━━━━━━━━<br/>• Materials selection<br/>• BOM → composition data<br/>• LCA → carbon footprint<br/>• Repairability design<br/>• Durability targets]
        
        MFG[Production Phase<br/>━━━━━━━━━━━<br/>• Serial number assignment<br/>• QR code/NFC application<br/>• Passport creation<br/>• Data population<br/>• Registration in EU system]
    end
    
    subgraph "Market Placement"
        MARKET[CE Marking + DPP<br/>━━━━━━━━━━━<br/>• Conformity verified<br/>• Passport validated<br/>• Customs clearance<br/>  (DPP data checked)<br/>• Product enters EU market]
    end
    
    subgraph "Use Phase"
        CONSUMER_USE[Consumer Use<br/>━━━━━━━━━━━<br/>• Consumer scans QR<br/>  → views sustainability info<br/>• Repair info accessible<br/>• Software updates tracked<br/>• Warranty registered]
        
        REPAIR[Repair/Service<br/>━━━━━━━━━━━<br/>• Repair professional<br/>  accesses repair manual<br/>• Spare parts ordering<br/>• Repair event logged<br/>  in passport]
    end
    
    subgraph "Second Life"
        RESALE[Resale / Refurbishment<br/>━━━━━━━━━━━<br/>• New owner registered<br/>• Product history available<br/>• Refurbishment status<br/>• Remaining lifetime info]
    end
    
    subgraph "End-of-Life"
        EOL[Recycling<br/>━━━━━━━━━━━<br/>• Recycler accesses passport<br/>• Material composition known<br/>• Disassembly instructions<br/>• Hazardous substance warning<br/>• Optimal recycling route<br/>• Material recovery tracked]
    end
    
    DESIGN --> MFG --> MARKET --> CONSUMER_USE
    CONSUMER_USE --> REPAIR --> CONSUMER_USE
    CONSUMER_USE --> RESALE --> CONSUMER_USE
    CONSUMER_USE --> EOL
    RESALE --> EOL
```

### 8.2 DPP Technical Architecture

```mermaid
graph TB
    subgraph "Product Level"
        QR[Data Carrier<br/>(QR Code / NFC)]
    end
    
    subgraph "Resolution Layer"
        RESOLVER[Link Resolver<br/>━━━━━━━━━━━<br/>GS1 Digital Link resolver<br/>OR EU DPP resolver<br/>━━━━━━━━━━━<br/>Scanned ID → data location]
    end
    
    subgraph "EU Infrastructure"
        REGISTRY[EU DPP Registry<br/>━━━━━━━━━━━<br/>• Product ID registration<br/>• Passport endpoint URLs<br/>• Minimum public data cache<br/>• Authority search/discovery<br/>• Interoperability gateway]
    end
    
    subgraph "Manufacturer Systems"
        DPP_HOST[DPP Hosting Platform<br/>━━━━━━━━━━━<br/>• Full passport data<br/>• Access control (RBAC)<br/>• API endpoints<br/>• Data lifecycle management<br/>• Audit trail]
        
        SOURCES[Data Sources<br/>━━━━━━━━━━━<br/>• ERP (product master)<br/>• PLM (design data)<br/>• LCA tool (footprint)<br/>• Compliance system<br/>• MES (production)<br/>• Service system (repairs)]
    end
    
    subgraph "Access Endpoints"
        WEB[Web Interface<br/>(consumer / public)]
        API[B2B API<br/>(partners / systems)]
        AUTH[Authority Interface<br/>(market surveillance)]
    end
    
    QR -->|Scan| RESOLVER
    RESOLVER -->|Route| REGISTRY
    REGISTRY <-->|Register/Discover| DPP_HOST
    SOURCES -->|Data feed| DPP_HOST
    DPP_HOST --> WEB & API & AUTH
```

### 8.3 DPP Access Control Model

```mermaid
graph TB
    subgraph "Access Levels"
        L1[Level 1: PUBLIC<br/>━━━━━━━━━━━<br/>Anyone scanning QR code<br/>━━━━━━━━━━━<br/>• Product identification<br/>• Carbon footprint class<br/>• Energy label<br/>• Repairability score<br/>• General composition<br/>• Collection instructions<br/>• Manufacturer info]
        
        L2[Level 2: AUTHENTICATED USER<br/>━━━━━━━━━━━<br/>Product owner; registered user<br/>━━━━━━━━━━━<br/>• Detailed performance data<br/>• Repair instructions<br/>• Spare parts catalog<br/>• Software update status<br/>• Warranty status<br/>• Product history (own)]
        
        L3[Level 3: PROFESSIONAL<br/>━━━━━━━━━━━<br/>Authorized repair; recycler<br/>━━━━━━━━━━━<br/>• Full repair manuals<br/>• Diagnostic tools/access<br/>• Disassembly instructions<br/>• Detailed material composition<br/>• Hazardous substance details<br/>• Recycling route optimization]
        
        L4[Level 4: AUTHORITY<br/>━━━━━━━━━━━<br/>Market surveillance; customs<br/>━━━━━━━━━━━<br/>• Full compliance data<br/>• Test reports<br/>• Supply chain information<br/>• Due diligence details<br/>• Audit trail<br/>• All data fields]
    end
```

---

## Chapter 9 — Case Studies

### 9.1 Case Study: Electronics DPP Pilot (Smartphone)

| Aspect | Detail |
|--------|--------|
| Product | Mid-range smartphone; mass market; EU sales ~2 million units/year |
| Context | Preparing for expected ESPR delegated act for smartphones/tablets (~2028-2029); participating in CIRPASS pilot |
| DPP Implementation | **Identifier**: GS1 SGTIN (serialized); encoded in QR code on retail packaging + laser-etched Data Matrix on device back (permanent). **Hosting**: Cloud-based DPP platform (SaaS provider); APIs connected to internal systems. **Data populated**: (1) Product ID + model + serial + IMEI; (2) Carbon footprint: 65 kgCO₂e (cradle-to-gate; LCA per ISO 14067); (3) Materials: 45% metals (steel, aluminum, copper, rare earths); 35% glass/ceramics; 15% plastics; 5% other; (4) Substances of concern: cobalt (battery); indium (display); rare earth elements; (5) Battery: 4500 mAh Li-ion; link to battery passport data; expected 500+ full cycles to 80% SOH; (6) Repairability: score 7.2/10; screen replacement available (€49 OEM part; 15 min procedure); battery replacement available; 4 years spare parts commitment; (7) Software support: 5 years security updates; 3 years OS updates; (8) Durability: IP68; rated 50,000 hours operation; designed for 4-year average use; (9) Recycled content: 95% recycled rare earths (magnets); 100% recycled tin (solder); 20% recycled plastics (housing); (10) End-of-life: WEEE Category 3; take-back via retailer or postal program; dismantling guide for recyclers (26-step document) |
| Consumer experience | Scan QR on box → web page showing: carbon footprint comparison (vs. average); repairability score; repair guide links; recycled content; proper disposal/take-back info |
| Challenges | (1) Carbon footprint: required building LCA capability; collecting supply chain data (IC carbon footprint from fabs was hardest). (2) Repairability scoring: methodology still evolving; French index used as basis; awaiting EU harmonized methodology. (3) Serial-level passport: required integration with production line (serial assignment + QR generation + data linkage in real-time during manufacturing). (4) Lifetime data updates: software support changes; spare parts availability changes → need process to update passports for already-sold products. (5) Cost: ~€2-3 per unit ongoing (platform + data management + QR); plus ~€500K one-time setup (integrations; LCA; content creation). |

### 9.2 Case Study: Battery Passport Early Adopter

| Aspect | Detail |
|--------|--------|
| Company | Industrial battery manufacturer; LFP chemistry; stationary energy storage systems; 50-500 kWh per unit; B2B sales to energy utilities and C&I customers |
| Timeline | Implementing 18 months ahead of 2027 deadline; competitive advantage + customer demand (large utilities asking for lifecycle data) |
| Implementation | (1) **Hardware**: NFC tag embedded in battery housing (survives outdoor/industrial conditions; readable through enclosure); QR code on nameplate (backup). (2) **Data model**: Followed Battery Pass consortium recommendations + Art 77 requirements; JSON-LD format; GS1 SGTIN identifier. (3) **Carbon footprint**: Conducted LCA per draft PEFCR; result: 42 kgCO₂e/kWh (LFP advantage over NMC); verified by third party; declared in passport. (4) **State of health**: BMS sends monthly SOH update to passport platform via secure API (mutual TLS); SOH algorithm validated to ±2% accuracy; displays remaining capacity curve. (5) **Recycled content**: Tracked: lithium (currently 4% recycled — below future 6% target; working on sourcing); iron/phosphate (98% recycled — established supply). (6) **Due diligence**: Lithium sourcing from Australia (low-risk); graphite from certified responsible source; published due diligence report linked from passport. (7) **Second-life**: Passport includes automated assessment: when SOH drops below configurable threshold, flags "suitable for second-life application [type X]" with remaining expected capacity. |
| Business value | (1) Won 3 major contracts where DPP data availability was evaluation criterion (utilities require transparency for ESG reporting). (2) 15% reduced customer support queries (customers self-serve via passport for specifications, maintenance info). (3) Insurance companies offer better rates for batteries with accessible SOH history. (4) End-of-life: recycling partner pre-contracted based on passport chemistry/composition data; optimized logistics. |

---

## Chapter 10 — Future Evolution

| Trend | Timeline | Impact |
|-------|----------|--------|
| **Expansion to all product categories** | 2025-2035 | ESPR delegated acts progressively covering: electronics; textiles; furniture; tires; chemicals; construction; appliances; industrial equipment. Eventually most manufactured goods. |
| **Integration with customs (CBAM)** | 2026+ | DPP data used at EU borders for customs clearance; CBAM (Carbon Border Adjustment Mechanism) uses carbon footprint from DPP; automated border compliance checks |
| **AI-powered sustainability insights** | Now-2027 | AI analyzes DPP data across millions of products; benchmarking; hotspot identification; personalized sustainability recommendations for consumers |
| **Consumer empowerment** | 2027+ | Comparison apps; purchase decisions based on DPP data; sustainability scoring aggregators; competitive pressure for better environmental performance |
| **Circular economy marketplace** | 2027+ | DPP enables trusted second-hand markets (verified product history); repair matchmaking (connect owners with authorized repairers); material marketplace (recyclers bid based on known composition) |
| **Dynamic/real-time DPP** | 2028+ | IoT-connected products continuously update passport (not just batteries); wearable devices; connected appliances; digital twins integrated with DPP |
| **Global DPP interoperability** | 2028-2032 | International mutual recognition of DPP systems (EU ↔ US ↔ China); common standards; avoid trade barriers from incompatible systems |
| **Blockchain maturity** | 2025-2028 | Production-grade blockchain solutions for supply chain verification; energy-efficient consensus; selective disclosure (ZKP); mainstream enterprise adoption |
| **DPP-enabled business models** | 2026+ | Product-as-a-service (PaaS) enabled by DPP tracking; leasing/subscription models with full lifecycle visibility; pay-per-use with transparent environmental accounting |
| **Repair/refurbishment ecosystem** | 2027+ | DPP-enabled repair networks; OEM and independent repairers use same DPP data; repair quality certification linked to passport |

---

## Chapter 11 — Interview Questions & Career Guide

### Tier 1: Entry-Level

**Q1:** What is a Digital Product Passport (DPP) and why is the EU implementing it?  
**A:** A Digital Product Passport (DPP) is a structured digital record that contains key information about a product's environmental sustainability, composition, repairability, and lifecycle — accessible via a data carrier (typically a QR code) on the physical product. The EU is implementing DPP through the Ecodesign for Sustainable Products Regulation (ESPR, 2024) because: (1) **Transparency**: consumers, businesses, and authorities currently lack reliable, comparable product sustainability information. DPP provides verified, standardized data. (2) **Circular economy**: for products to be effectively repaired, refurbished, and recycled, the relevant actors (repairers, recyclers) need product information — what materials are inside, how to disassemble, what's hazardous. (3) **Green claims**: too many unsubstantiated environmental claims ("eco-friendly", "sustainable"); DPP provides the verified data behind any claim. (4) **Market surveillance**: authorities can digitally check product compliance without physical testing. (5) **Level playing field**: imported products must meet same information requirements as EU-manufactured ones. Key data includes: carbon footprint, material composition, repairability score, durability, recycled content, substances of concern, and end-of-life instructions. The battery passport (EU Battery Regulation, 2027) is the first live implementation.

**Q2:** How does a consumer interact with a DPP?  
**A:** A consumer uses their smartphone to scan the QR code (or tap the NFC tag) on the product or its packaging. This takes them to a web page (no app needed) showing the publicly accessible passport information in a user-friendly format. They can see: the product's environmental footprint (e.g., "this product has a carbon footprint of 65 kgCO₂e, which is class B — better than 60% of similar products"); repairability score ("7.2/10; screen and battery are user-replaceable"); expected lifetime; recycled content; where to return it for recycling; software support duration; spare parts availability. For more detailed information (repair manuals, component diagnostics), they might need to register/authenticate. The key benefit: consumers can make informed choices by comparing products on verified sustainability criteria — not just marketing claims.

### Tier 2: Mid-Level

**Q3:** What are the main technical challenges in implementing DPP for electronics at scale?  
**A:** [Detailed answer covering: (1) **Carbon footprint at product level**: requires LCA capability and supply chain data (component-level carbon footprints from hundreds of suppliers; IC carbon footprint from semiconductor fabs is particularly hard to get). (2) **Material composition data**: need complete BOM composition (IPC-1752A Class C from all suppliers; many don't provide FMD today; cascade of requests through multi-tier supply chain). (3) **Serialization at scale**: high-volume electronics (millions of units) need unique identifier per item; integrated into production line (QR printing/verification; NFC embedding); linked in real-time to data system. (4) **Data lifecycle management**: passports must persist for product lifetime + X years; products sold in 2028 need accessible passports in 2040+; hosting costs; data migration; company acquisitions/closures. (5) **Dynamic data for electronics**: software support status changes; spare parts availability changes; firmware versions; need process to update millions of existing passports when policies change. (6) **Interoperability**: EU DPP registry integration; data format compliance; API standards (not fully defined yet); testing against evolving specifications. (7) **Access control**: implementing role-based access correctly — public data vs. repair professional data vs. recycler data vs. authority data — with proper authentication (eIDAS?). (8) **Cost per unit**: must be very low for consumer electronics (target €1-3/unit lifecycle cost); drives need for automation and efficient platforms. Solutions: automation (ERP/PLM integration); industry databases (shared component LCA data); platform-as-a-service (avoid custom build); phased approach (start with model-level, move to serial-level).]

### Tier 3: Senior

**Q4:** Design a DPP architecture for a multi-product electronics company (consumer devices, industrial equipment, automotive electronics) that will scale across product categories, comply with both EU ESPR and sector-specific regulations (Battery Regulation, automotive Catena-X), and provide competitive advantage.  
**A:** [Comprehensive answer covering: multi-category data model design (common core + product-specific extensions); platform architecture (federated approach; manufacturer-hosted with EU registry integration); identifier strategy (GS1 SGTIN with Digital Link resolution); data source integration strategy (ERP, PLM, MES, LCA, compliance system, service system → unified DPP platform); sector-specific considerations (automotive: Catena-X data space integration + EDC connector; batteries: real-time BMS data; consumer electronics: high-volume serialization); technology choices (cloud-native; microservices; event-driven for lifecycle updates; blockchain anchoring optional for high-value/regulatory); access control architecture (OAuth2/OIDC + role-based + EU eIDAS for authority); competitive strategy (early implementation; richer data than required; premium sustainability communication; circular economy services enabled by DPP data); cost model and ROI (implementation cost vs. compliance cost avoidance + revenue premium + operational efficiency); organizational design (cross-functional DPP team; data governance; supplier engagement program).]

---

## Chapter 12 — Cheat Sheet & Quick Reference

### DPP Key Facts

```
WHAT: Digital record of product sustainability & lifecycle information
WHY:  EU circular economy; transparency; compliance; informed decisions
WHEN: Battery passport 2027; other products 2027-2030 (per delegated acts)
WHO:  Manufacturers (create); consumers/repairers/recyclers/authorities (access)
HOW:  QR code/NFC on product → link to digital passport → standardized data

LEGAL BASIS: EU ESPR (Regulation 2024/1781)
  + Product-specific delegated acts
  + Battery Regulation 2023/1542 (first DPP: battery passport)
```

### DPP Minimum Data Set (Generic)

```
IDENTIFICATION:
□ Unique product identifier (serialized or batch)
□ Product name/model
□ Manufacturer (name, address, contact)
□ Manufacturing date + location
□ Product category code

SUSTAINABILITY:
□ Carbon footprint (kgCO₂e; methodology; scope; class)
□ Recycled content (% per material type)
□ Substances of concern (list; concentration; CAS)
□ Environmental footprint score (when PEF applied)

CIRCULARITY:
□ Expected lifetime (years or cycles)
□ Repairability (score or info; spare parts; repair guide)
□ Recyclability (design assessment; instructions)
□ Disassembly instructions (for recyclers)
□ Material composition (major material groups + %)

COMPLIANCE:
□ CE marking confirmation
□ EU Declaration of Conformity (reference)
□ Applicable regulations met
□ Test reports (reference/link)

END-OF-LIFE:
□ Collection/take-back instructions
□ Proper disposal guidance
□ WEEE category (electronics)
□ Hazardous waste classification (if applicable)
```

### Data Carrier Selection Guide

```
QR CODE (default recommendation for most products):
  ✓ Universal smartphone scanning (no app needed)
  ✓ Very low cost (printing)
  ✓ Can encode GS1 Digital Link URL
  ✗ Requires line-of-sight; can be damaged/obscured
  → Best for: consumer products, packaging, labels

NFC (add for high-value or industrial products):
  ✓ Durable (embedded); no line-of-sight
  ✓ Survives harsh conditions
  ✓ Enables authentication (anti-counterfeit)
  ✗ Higher cost (€0.05-0.50/tag + embedding)
  → Best for: electronics devices; industrial equipment; apparel

DUAL (QR + NFC — recommended for electronics):
  ✓ QR on packaging (retail experience)
  ✓ NFC in device (lifetime access after unboxing)
  → Covers all use cases (retail + in-use + recycling)
```

### Timeline Quick Reference

```
2024: ESPR enters into force; framework established
2025: First delegated acts in preparation (textiles, electronics, iron/steel)
2026: Standards finalized (data formats, interoperability, access)
2027: BATTERY PASSPORT LIVE (EV + industrial >2 kWh)
      Textiles DPP (estimated)
      Iron/steel DPP (estimated)
2028: Electronics DPP (smartphones/tablets — estimated)
      Construction products (estimated)
2029: Furniture; tires; other categories
2030+: Progressive expansion to most manufactured products
       Full ecosystem operational; customs integration; global interoperability
```

---

*End of Document — 12_Digital_Product_Passport.md*
