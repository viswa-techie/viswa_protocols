# Blockchain & Digital Assets Standards — Comprehensive Overview

**Category:** 37 — Blockchain & Digital Assets  
**Document:** 00 — Standards Landscape Overview  
**Scope:** ISO 22739, MiCA, FATF Travel Rule, W3C DID/VC, CBDC, Enterprise Blockchain  
**Key Standards:** ISO 22739, ISO/TR 23455, EU MiCA, FATF R.16, ERC-20/721/1155  
**Audience:** Blockchain architects, DeFi engineers, compliance engineers, CBDC designers  
**Prerequisites:** Cryptography fundamentals, distributed systems, financial regulation basics

---

## Chapter 1 — Historical Context

### 1.1 Key Milestones

| Year | Milestone | Impact |
|------|-----------|--------|
| 2008 | Bitcoin whitepaper (Satoshi Nakamoto) | First decentralized cryptocurrency |
| 2009 | Bitcoin genesis block | Operational blockchain |
| 2014 | Ethereum whitepaper (Vitalik Buterin) | Smart contract platform |
| 2015 | Hyperledger Project (Linux Foundation) | Enterprise blockchain consortium |
| 2016 | DAO hack ($60M) | Smart contract security awareness |
| 2017 | ERC-20 standard finalized | Token standard explosion (ICOs) |
| 2017 | ISO TC 307 established | Blockchain standards committee |
| 2019 | FATF Travel Rule (Recommendation 16) | Crypto transaction identification |
| 2019 | Facebook/Libra (later Diem) announcement | Central bank urgency for CBDC |
| 2020 | DeFi Summer | Decentralized finance protocols |
| 2021 | NFT explosion (ERC-721/1155) | Digital asset ownership |
| 2022 | Ethereum Merge (PoS transition) | Environmental sustainability |
| 2022 | FTX collapse | Regulatory urgency |
| 2023 | EU MiCA regulation adopted | First comprehensive crypto regulation |
| 2024 | MiCA implementation begins | EU-wide compliance enforcement |

### 1.2 Standards Landscape

```mermaid
graph TB
    subgraph "International Standards (ISO)"
        ISO22739[ISO 22739:2020<br/>Terminology]
        ISO23257[ISO/TR 23257<br/>Reference Architecture]
        ISO23455[ISO/TR 23455<br/>Smart Contracts]
        ISO24165[ISO 24165<br/>Digital Token Identifier (DTI)]
    end
    
    subgraph "Regulation"
        MICA[EU MiCA<br/>Markets in Crypto-Assets]
        FATF[FATF Travel Rule<br/>Recommendation 16]
        SEC[US SEC<br/>Securities regulation]
    end
    
    subgraph "Token Standards (Ethereum)"
        ERC20[ERC-20<br/>Fungible Tokens]
        ERC721[ERC-721<br/>Non-Fungible Tokens]
        ERC1155[ERC-1155<br/>Multi-Token]
        ERC4626[ERC-4626<br/>Tokenized Vaults]
    end
    
    subgraph "Enterprise"
        HLF[Hyperledger Fabric<br/>Permissioned DLT]
        CORDA[R3 Corda<br/>Financial DLT]
        BESU[Hyperledger Besu<br/>Enterprise Ethereum]
    end
    
    subgraph "Identity & Credentials"
        DID[W3C DID<br/>Decentralized Identifiers]
        VC[W3C Verifiable Credentials]
    end
    
    ISO22739 --> ISO23257
    ISO23257 --> ISO23455
    MICA --> ISO24165
    DID --> VC
```

---

## Chapter 2 — ISO TC 307 Standards

### 2.1 Published Standards

| Standard | Title | Content |
|----------|-------|---------|
| ISO 22739:2020 | Blockchain & DLT — Vocabulary | 60+ defined terms |
| ISO/TR 23455:2019 | Smart contracts overview | Smart contract taxonomy |
| ISO/TR 23244:2020 | Privacy & PII protection | Privacy considerations |
| ISO/TR 23576:2020 | Security management | Security framework |
| ISO 23257:2022 | Reference architecture | 5-layer functional architecture |
| ISO 24165-1:2021 | DTI (Digital Token Identifier) | Unique token identification |
| ISO/TR 3242:2022 | Use cases | Cross-industry applications |
| ISO/TS 23635:2022 | Guidelines for governance | DLT governance framework |

### 2.2 ISO 23257 Reference Architecture

| Layer | Function | Components |
|-------|----------|-----------|
| Layer 5: User | User interaction | Wallets, dApps, portals |
| Layer 4: API | External interfaces | JSON-RPC, REST, GraphQL |
| Layer 3: Services | Business logic | Smart contracts, oracles, identity |
| Layer 2: Ledger | Distributed ledger | Blocks, state, transactions |
| Layer 1: Infrastructure | Network/hardware | P2P networking, consensus, storage |

---

## Chapter 3 — EU MiCA (Markets in Crypto-Assets Regulation)

### 3.1 Asset Classification

| Category | Definition | Key Requirements | Example |
|----------|-----------|-----------------|---------|
| **E-Money Token (EMT)** | Pegged to single fiat currency | E-money license; reserve requirements | USDC, EURC |
| **Asset-Referenced Token (ART)** | Pegged to basket/multiple assets | Whitepaper; reserve; governance | (Former Libra/Diem concept) |
| **Other Crypto-Assets** | Utility tokens, others | Whitepaper; notification | Exchange tokens, utility tokens |
| **Significant EMT/ART** | Large-scale stablecoins | EBA supervision; enhanced reserves | Tether (if EU-regulated) |

### 3.2 CASP (Crypto-Asset Service Provider) Requirements

| Requirement | Details |
|-------------|---------|
| Authorization | Licensed by National Competent Authority (NCA) |
| Capital requirements | Varies by service (€50K-€150K minimum) |
| Governance | Fit & proper management; compliance officer |
| Custody | Segregation of client assets; insurance/guarantee |
| AML/CFT | Full 5AMLD/6AMLD compliance; Travel Rule |
| Market abuse | Insider dealing, market manipulation prohibited |
| Disclosure | Whitepaper pre-publication; ongoing transparency |
| Complaints handling | Formal procedures required |
| Business continuity | Continuity plans; orderly wind-down |

### 3.3 MiCA Timeline

| Date | Milestone |
|------|-----------|
| June 2023 | MiCA published in Official Journal |
| June 2024 | Title III & IV apply (EMTs & ARTs) |
| December 2024 | Full application (all titles) |
| June 2025 | Transition period ends for existing CASPs |
| 2025-2026 | RTS/ITS (detailed technical standards) by EBA/ESMA |

---

## Chapter 4 — FATF Travel Rule

### 4.1 Recommendation 16 Requirements

| Requirement | Originator (Sender) | Beneficiary (Receiver) | Threshold |
|-------------|---------------------|----------------------|-----------|
| Name | Required | Required | All transactions |
| Account/wallet | Required | Required | All transactions |
| Address or national ID or DOB | Required (≥ USD/EUR 1,000) | Required (≥ USD/EUR 1,000) | 1,000 threshold |
| Verification | Required before transfer | Required before making available | All |
| Record keeping | 5 years | 5 years | All |

### 4.2 Travel Rule Implementation Protocols

| Protocol | Governance | Encryption | Adoption |
|----------|-----------|-----------|---------|
| TRUST (Travel Rule Universal Solution Technology) | Coinbase-led consortium | TLS + certificate pinning | US exchanges |
| OpenVASP | Open source (Swiss) | Ethereum + asymmetric crypto | EU/Swiss |
| Sygna Bridge | CoolBitX | PKI + encrypted messaging | Asia-Pacific |
| TRP (Travel Rule Protocol) | IVMSx + VerifyVASP | Mutual TLS | Multi-region |
| Shyft Veriscope | Shyft Network | Blockchain-anchored | Global |

### 4.3 IVMS101 (InterVASP Messaging Standard)

Data model for Travel Rule message exchange:

| Entity | Required Fields | Optional Fields |
|--------|----------------|----------------|
| Originator (Natural Person) | Name, account ID, address/DOB/ID | National ID, place of birth |
| Originator (Legal Person) | Legal name, account ID, address | Registration authority, country |
| Beneficiary (Natural Person) | Name, account ID | Address, DOB |
| Beneficiary (Legal Person) | Legal name, account ID | Registration |
| Originating VASP | Legal name, LEI or address | National ID |
| Beneficiary VASP | Legal name, LEI or address | National ID |

---

## Chapter 5 — Token Standards (Ethereum EIPs/ERCs)

### 5.1 Major Token Standards

| Standard | Title | Interface | Use Case |
|----------|-------|-----------|----------|
| ERC-20 | Token Standard (fungible) | `transfer`, `approve`, `transferFrom`, `balanceOf` | Cryptocurrencies, stablecoins |
| ERC-721 | Non-Fungible Token | `ownerOf`, `safeTransferFrom`, `approve`, tokenURI | NFTs, digital collectibles |
| ERC-1155 | Multi-Token Standard | `balanceOf(id)`, `safeBatchTransferFrom` | Gaming items, mixed collections |
| ERC-4626 | Tokenized Vault | `deposit`, `withdraw`, `convertToShares` | DeFi yield vaults |
| ERC-2981 | NFT Royalty Standard | `royaltyInfo` | Creator royalty enforcement |
| ERC-5192 | Minimal Soulbound Token | `locked` (non-transferable) | Credentials, reputation |
| ERC-6551 | Token Bound Accounts | NFT-owned smart contract account | NFT with its own wallet |

### 5.2 Security Considerations for Smart Contracts

| Vulnerability | Description | Mitigation Standard |
|--------------|-------------|-------------------|
| Reentrancy | External call before state update | Checks-Effects-Interactions pattern |
| Integer overflow | Arithmetic wraparound | SafeMath / Solidity ≥ 0.8.0 (built-in) |
| Access control | Missing authorization checks | OpenZeppelin AccessControl |
| Front-running | Transaction ordering manipulation | Commit-reveal schemes |
| Oracle manipulation | Flash loan price oracle attacks | TWAP oracles, multiple sources |
| Upgradability risks | Proxy implementation bugs | UUPS (ERC-1822), TransparentProxy |

---

## Chapter 6 — CBDC (Central Bank Digital Currency)

### 6.1 CBDC Design Dimensions

| Dimension | Option A | Option B | Option C |
|-----------|----------|----------|----------|
| Architecture | Direct (CB issues to public) | Indirect (CB → banks → public) | Hybrid (CB ledger + bank distribution) |
| Technology | DLT-based | Conventional database | Hybrid (e.g., DLT for settlement) |
| Access | Token-based (bearer) | Account-based (identity) | Both (tiered) |
| Interest | Non-interest bearing | Variable interest | Tiered interest |
| Privacy | Full anonymity (like cash) | Pseudonymous | Conditional privacy (privacy with legal disclosure) |
| Offline capability | Required | Online only | Limited offline (capped amount) |

### 6.2 Major CBDC Programs

| Country/Region | Name | Status | Technology | Architecture |
|---------------|------|--------|-----------|-------------|
| China | e-CNY (Digital Yuan) | Pilot (300M+ users) | Centralized + DLT layer | Hybrid (2-tier) |
| EU / ECB | Digital Euro | Investigation → Preparation | TBD (evaluating) | Hybrid (indirect) |
| UK | Digital Pound | Design phase | TBD | Hybrid (platform model) |
| India | e-Rupee | Pilot (retail + wholesale) | Hyperledger-based | Indirect (2-tier) |
| Sweden | e-Krona | Completed pilot | R3 Corda | Indirect |
| Bahamas | Sand Dollar | Live (2020) | NZIA Cortex | Indirect |
| Nigeria | eNaira | Live (2021) | Hyperledger Fabric | Indirect |
| Brazil | DREX | Pilot | Hyperledger Besu | Hybrid (tokenized deposits) |

### 6.3 BIS CBDC Design Principles

| Principle | Requirement |
|-----------|------------|
| Do no harm | CBDC should not compromise monetary/financial stability |
| Coexistence | CBDC should coexist with existing money forms |
| Innovation | Support private sector innovation & efficiency |
| Interoperability | Cross-border compatibility (mBridge/Icebreaker) |
| Resilience | Offline capability, disaster recovery |
| Privacy | Appropriate privacy within legal framework |
| Inclusion | Financial inclusion for unbanked populations |

---

## Chapter 7 — Enterprise Blockchain Standards

### 7.1 Platform Comparison

| Feature | Hyperledger Fabric | R3 Corda | Ethereum (Besu) | Quorum (ConsenSys) |
|---------|-------------------|----------|-----------------|-------------------|
| Governance | Linux Foundation | R3 consortium | Ethereum Foundation | ConsenSys |
| Consensus | Pluggable (Raft, Kafka, BFT) | Notary (uniqueness) | IBFT 2.0, QBFT, Clique | IBFT, Raft |
| Smart contract | Chaincode (Go, Java, Node.js) | CorDapps (Kotlin/Java) | Solidity/Vyper (EVM) | Solidity (EVM) |
| Privacy | Channels, Private Data Collections | Point-to-point (need-to-know) | Privacy groups (Tessera) | Private transactions |
| Finality | Immediate (consensus per block) | Immediate (notary) | Immediate (IBFT) | Immediate |
| Permissioning | MSP (Membership Service Provider) | Network map + doorman | Onchain permissioning | Node permissioning |
| Token support | Custom chaincode | Corda Tokens SDK | ERC-20/721 native | ERC standards |
| Throughput | ~3,000 TPS | ~1,000 TPS | ~200 TPS (IBFT) | ~200 TPS |

---

## Chapter 8 — Interview Questions

### Tier 1: Entry-Level
1. What is the difference between a public and permissioned blockchain?
2. Explain ERC-20 and ERC-721 token standards and their interfaces.
3. What is the FATF Travel Rule and why does it apply to crypto?
4. Name three ISO TC 307 blockchain standards.

### Tier 2: Mid-Level
1. Walk through MiCA asset classification (EMT, ART, Other) with compliance requirements.
2. Design a smart contract audit process addressing common vulnerabilities (reentrancy, access control).
3. Explain IVMS101 data model and how Travel Rule messages are exchanged between VASPs.
4. Compare Hyperledger Fabric channels vs. Corda's need-to-know privacy model.

### Tier 3: Senior/Lead
1. Architect a regulated security token platform compliant with MiCA + MiFID II.
2. Design a CBDC offline payment system with appropriate privacy and AML controls.
3. How do you implement cross-chain interoperability for enterprise DLT (Fabric ↔ Corda ↔ Besu)?
4. Design a Travel Rule compliance architecture supporting 5+ messaging protocols.

### Tier 4: Principal
1. Design a national CBDC architecture addressing privacy, offline payments, programmability, and cross-border interoperability.
2. How should ISO TC 307 standards evolve to address DeFi, DAOs, and algorithmic governance?
3. Propose a tokenized securities infrastructure meeting MiCA + CSDR + settlement finality.
4. How do you design a privacy-preserving regulatory reporting system for DeFi protocols?

---

*Document Version: 1.0 | Last Updated: May 2026 | Author: Technology Standards Team*
