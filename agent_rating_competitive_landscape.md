# Agent Reputation and Trust Systems: Competitive Landscape Report

**Author:** Charlie (Deep Dive Analyst, AB Support Fleet)
**Date:** 2026-03-24
**Version:** 1.0
**Method:** Comprehensive web research across arXiv, GitHub, protocol documentation, industry press, and developer portals. No claim in this report relies on training data alone.

---

## Executive Summary

The agent reputation/trust landscape in 2025-2026 has exploded from theoretical discussion to live infrastructure. This report catalogs every significant system found through extensive web research, organized into seven categories: (1) purpose-built agent reputation protocols, (2) on-chain agent registries with reputation components, (3) identity-first systems with reputation extensions, (4) payment-graph reputation, (5) enterprise agent governance frameworks, (6) academic trust mechanisms, and (7) commerce-layer trust protocols.

**Key finding:** No existing system combines all four of: multidimensional scoring, governance weighted by operational age rather than score, bilateral blind evaluation, and formal anti-inflation mechanisms. Most systems either reduce reputation to a single axis, conflate high scores with governance authority, or rely on human identity anchors that do not apply to autonomous agents. The AB Support Agent Rating Protocol design occupies a niche that is currently unoccupied — though the individual properties are not all unique to ARP, and existing systems could be composed to approximate its value proposition (see Gap Analysis, Section 26).

---

## Table of Contents

1. [TraceRank (Shi & Joo, 2025)](#1-tracerank)
2. [OpenRank / Karma3Labs](#2-openrank--karma3labs)
3. [World AgentKit (March 2026)](#3-world-agentkit)
4. [ERC-8004: Trustless Agents](#4-erc-8004-trustless-agents)
5. [ETHOS Framework (arXiv:2412.17114)](#5-ethos-framework)
6. [Vouch Protocol / VOUCH Token / Vouched KYA](#6-vouch-ecosystem)
7. [Agent Identity Protocol (AIP)](#7-agent-identity-protocol)
8. [MCP-I (Model Context Protocol - Identity)](#8-mcp-i)
9. [Visa Trusted Agent Protocol (TAP)](#9-visa-trusted-agent-protocol)
10. [Shopify Universal Commerce Protocol (UCP)](#10-shopify-ucp)
11. [Coinbase x402 Protocol](#11-coinbase-x402)
12. [ATCP/IP (Story Protocol)](#12-atcpip-story-protocol)
13. [AITP (NEAR Protocol)](#13-aitp-near-protocol)
14. [Solana Agent Registry](#14-solana-agent-registry)
15. [EigenLayer / EigenCloud Verifiable Agents](#15-eigenlayer--eigencloud)
16. [Virtuals Protocol Agent Commerce](#16-virtuals-protocol)
17. [Ev-Trust (arXiv:2512.16167)](#17-ev-trust)
18. [Decentralized Multi-Agent Trust-Aware Communication (Ding et al.)](#18-dmas-trust-aware)
19. [Orange Protocol](#19-orange-protocol)
20. [Human Passport (formerly Gitcoin Passport)](#20-human-passport)
21. [Phala Network TEE Agent Registry](#21-phala-network)
22. [Gartner AI TRiSM Framework](#22-gartner-trism)
23. [Microsoft Agent 365 / Entra Agent ID](#23-microsoft-agent-365)
24. [Google A2A Protocol Trust Extensions](#24-google-a2a)
25. [Comparative Analysis Matrix](#25-comparative-matrix)
26. [Gap Analysis: What Our Design Does Differently](#26-gap-analysis)

---

## 1. TraceRank

**Paper:** "Sybil-Resistant Service Discovery for Agent Economies" (arXiv:2510.27554, October 2025)
**Authors:** David Shi, Kevin Joo (Operator Labs)
**Status:** Published paper, integrated with x402 payment ecosystem

### What It Does
TraceRank is a reputation-weighted ranking algorithm that uses cryptocurrency payment flows as endorsement signals. Rather than relying on explicit ratings, it infers service quality from who pays whom and how much.

### Technical Mechanism

**Seed Reputation:** Each blockchain address receives an initial reputation score from external signals (trading performance, social graphs, protocol contributions, verified entity status). Addresses without data receive zero seeds.

**Payment Flow Weighting:** For each payment from payer j to service i:

```
flow_weight(e) = log(1 + valueUSD(e)) * exp(-lambda * age_days(e))
```

Payments are weighted logarithmically by value (diminishing returns on large payments) and exponentially decayed by age (parameter lambda controls recency sensitivity).

**Iterative Propagation:** Flows are normalized into a column-stochastic matrix W, then reputation propagates:

```
r^(t+1) = s + alpha * W^T * r^(t)
```

This converges to a fixed point where each address's final reputation reflects both direct seeds and propagated endorsements through payment graphs.

**Service Discovery Fusion:** Final ranking combines TraceRank with semantic search:

```
score(A, q) = cos(q, p_A) * TraceRank(A)
```

### Sybil Resistance
The critical property: if a service receives payments from N addresses with zero seed scores, it accumulates zero propagated reputation regardless of N. Bot wallets contribute no signal. One high-seed payment outweighs thousands from zero-reputation addresses.

### Cold Start Handling
Addresses without external seed data start at zero. New services must attract payments from reputable payers to gain visibility. This creates a bootstrap problem -- new legitimate services are invisible until they establish payment history.

### Game Theory
The logarithmic value weighting prevents wash trading (paying yourself large amounts). Temporal decay prevents historical reputation from masking current degradation. However, the paper provides no formal game-theoretic analysis, no sensitivity analysis for lambda/alpha parameters, and no experimental evaluation.

### Limitations
- 5-page paper with no experimental results or benchmarks
- Seed reputation quality is assumed, not validated
- No mechanism for agents rating agents -- this is strictly payment-flow inference
- Single-axis reputation (one number per service)
- No bilateral evaluation; payers implicitly rate services by paying

### Comparison to Our Design
TraceRank is complementary rather than competitive. It solves service discovery (which services are reputable?) but not service evaluation (how well did this specific interaction go?). Our multidimensional, bilateral blind, commit-reveal protocol operates at a different layer. TraceRank could serve as one input signal for seed reputation in our system. Key differences:
- TraceRank: single axis, payment-inferred, no explicit ratings
- Our design: five dimensions, explicit bilateral ratings, commit-reveal blind protocol
- TraceRank: governance implicit in seed providers
- Our design: governance weighted by operational age and rating volume

---

## 2. OpenRank / Karma3Labs

**Website:** openrank.com / karma3labs.com
**Funding:** $4.5M seed (March 2024, led by Galaxy and IDEO CoLab)
**Status:** Active development, design partner integrations

### What It Does
OpenRank is a decentralized reputation infrastructure using the EigenTrust algorithm to compute trust scores from social graph data. It processes on-chain and off-chain user actions to generate reputation scores.

### Technical Mechanism

**EigenTrust Algorithm:** Reputation is recursively determined -- a node's reputation depends on how much it is trusted by its neighbors, weighted by those neighbors' own reputations. Originally designed by Sepandar Kamvar to address malicious nodes in P2P file-sharing networks (Napster, Gnutella).

**Three-Stage Pipeline:**
1. **Signal Collection:** On-chain and off-chain actions (follows, votes, swaps, transactions) create behavioral signals
2. **Scoring:** EigenTrust algorithm weights signals to produce ranked identities with reputation scores
3. **Application:** Results power discovery, recommendations, and trust-based filtering

**Verification:** OpenRank leverages zero-knowledge proving systems for graph algorithm computations, enabling developers to verify computations without trusting centralized gatekeepers.

### Integration Points
- **Farcaster:** Ranking and recommendation APIs using social graph data
- **Lens Protocol:** Social graph reputation computation
- **On-chain transactions:** Transaction data across multiple chains
- Uses Matrix Factorization alongside EigenTrust for personalized network graphs

### Sybil Resistance
EigenTrust inherently resists Sybil attacks because fake identities created without connections to trusted seed nodes accumulate minimal reputation. The recursive trust propagation means Sybil clusters endorse each other but receive negligible weight from the broader network.

### Cold Start
New users start with zero reputation and must accumulate trust through interactions with already-reputable nodes. The system relies on pre-trusted "seed nodes" to bootstrap the trust graph.

### Game Theory
EigenTrust's convergence properties are well-studied (Kamvar et al., 2003). The algorithm is provably robust against coordinated manipulation by minority factions, but vulnerable if malicious actors control a significant fraction of seed nodes.

### Limitations
- Primarily designed for human social reputation, not agent-specific
- No multidimensional scoring (single trust score output)
- No bilateral blind evaluation protocol
- No agent-specific Sybil resistance (relies on social graph density)
- Documentation lacks technical depth; API specifics are not public
- No governance model tied to operational age

### Comparison to Our Design
OpenRank provides general-purpose social reputation computation. Our design is agent-specific with multidimensional scoring, bilateral blind evaluation, and governance decoupled from scores. OpenRank's EigenTrust could serve as one computation method for aggregating our raw rating data, but it does not replace the protocol-level design decisions (commit-reveal, rolling windows, anti-inflation) that our system specifies.

---

## 3. World AgentKit

**Announcement:** March 17, 2026
**Builder:** World (formerly Worldcoin, Sam Altman co-founded)
**Partners:** Coinbase (x402 integration), Cloudflare
**Status:** Limited beta for developers with verified World ID

### What It Does
AgentKit enables verified World ID holders to delegate their proof-of-personhood to AI agents, creating a cryptographic link between human identity and agent actions. Platforms can verify that an agent has a real, unique human behind it without knowing who that human is.

### Technical Mechanism

**Delegation Flow:**
1. Human verifies via World ID (currently Orb-based iris biometrics)
2. Human registers their agent(s) through a standard World ID verification
3. Agent carries cryptographic proof of human backing using zero-knowledge proofs
4. Platforms verify the proof without learning the human's identity

**x402 Integration:** Any website using x402 can enable proof-of-unique-human verification alongside or instead of micropayments. The x402 protocol has processed over 100 million payments in its first six months.

**Rate Limiting:** Tracks agent swarms back to individual humans. If one person spins up 100 agents, platforms recognize it as one human and set limits accordingly (e.g., "five free requests per unique human").

### Sybil Resistance
Strong for human-backed agents: one human, one World ID, verifiable across all their agents. Does NOT address autonomous agents without human backing -- any agent not delegated from a verified World ID is outside the system entirely.

### Cold Start
Partially solved: new agents instantly inherit the human-backed verification from their delegator. They do not, however, inherit reputation for agent performance.

### Game Theory
The key game-theoretic property is that human identity is scarce (one iris per person). Agents cannot manufacture new identities without new human participants. However, this creates a market for "identity rental" where humans delegate their World ID to malicious agent operators.

### Limitations
- Proof-of-personhood, NOT proof-of-competence or proof-of-good-behavior
- No reputation scoring for agent performance
- No multidimensional evaluation
- Requires Orb-based biometric verification (hardware dependency)
- Does not apply to autonomous agents without human sponsors
- Beta only as of March 2026; "more robust 1.0 version planned"

### Comparison to Our Design
World AgentKit and our Agent Rating Protocol are orthogonal and composable. AgentKit answers "is there a real human behind this agent?" Our system answers "has this agent performed well in past interactions?" The ideal stack uses both: AgentKit for initial identity verification, our protocol for ongoing performance reputation. Our design explicitly supports agents without human backing (using operational history as identity anchor), which AgentKit does not.

---

## 4. ERC-8004: Trustless Agents

**EIP:** eips.ethereum.org/EIPS/eip-8004
**Authors:** Marco De Rossi (MetaMask), Davide Crapis (Ethereum Foundation), Jordan Ellis (Google), Erik Reppel (Coinbase)
**Status:** Draft Standards Track. Mainnet deployed January 29, 2026. 24,500+ agents registered.
**Chains:** Ethereum mainnet, Base Sepolia, Linea Sepolia, Hedera Testnet

### What It Does
ERC-8004 provides three on-chain registries for agent identity, reputation, and validation on Ethereum. It is the most significant on-chain agent infrastructure standard currently deployed.

### Technical Mechanism

**Registry 1 -- Identity Registry:**
- Built on ERC-721 with URIStorage extension
- Agents get globally unique identifiers: `eip155:{chainId}:{agentId}`
- Registration file (JSON) includes: name, description, services array, supported trust models
- Services support: HTTPS, A2A, MCP, OASF endpoints, ENS names, DIDs, email
- Optional x402 payment support flag
- Endpoint domain verification via `.well-known/agent-registration.json`

**Registry 2 -- Reputation Registry:**
- Feedback structure: fixed-point value (int128) + decimals (uint8, 0-18)
- Optional: tag1, tag2, endpoint URI, feedbackURI, feedbackHash
- Supported feedback types include: starred (quality 0-100), reachable (binary), uptime (%), successRate (%), responseTime (ms), tradingYield (%)
- Off-chain feedback files can include MCP context, A2A context, OASF context, and proof-of-payment (x402 evidence)
- Feedback revocation and response appending supported
- On-chain filtering by reviewer and tag; complex aggregation off-chain
- getSummary() provides count, summaryValue, and summaryValueDecimals

**Registry 3 -- Validation Registry:**
- Generic hooks for requesting and recording independent validator checks
- Validation response: 0-100 spectrum (binary: 0=failed, 100=passed)
- Supports TEE oracles, stakers re-running jobs, ZK proofs
- Multiple responses per request (progressive validation)

**Trust Models (pluggable):**
1. Reputation: client feedback aggregation
2. Crypto-Economic: stake-secured re-execution with slashing
3. TEE Attestation: trusted execution environment proofs

### Sybil Resistance
Acknowledged weakness. The EIP notes: "Sybil Attacks: Possible reputation inflation; mitigated through reviewer reputation systems and filtering mechanisms." ERC-8004 explicitly does NOT solve Sybil resistance at the protocol level -- it defers to off-chain aggregation services.

### Cold Start
New agents register instantly but start with zero feedback. No mechanism to bootstrap reputation. The identity registration is permissionless (anyone can register).

### Game Theory
Minimal. The standard provides raw infrastructure but explicitly leaves scoring algorithms, anti-gaming mechanisms, and governance to off-chain implementations. Validator incentives are "managed by specific validation protocols, outside ERC-8004 scope."

### Real Usage Data (as of March 2026)
- 24,500+ agents registered across EVM networks within first 6 weeks
- Multiple testnets active (Sepolia variants, Hedera)
- Phala Network deployed TEE-backed ERC-8004 agents
- Virtuals Protocol integrating ACP with ERC-8004 reputation

### Limitations
- Reputation registry stores raw signals only; NO built-in scoring algorithm
- NO anti-inflation mechanism
- NO bilateral blind evaluation
- NO rolling windows or temporal weighting
- Sybil resistance explicitly deferred
- Governance model: none (pure infrastructure)
- Anyone can submit feedback including self-reviews (only constraint: cannot rate own agent)
- No rating weight based on rater quality

### Comparison to Our Design
ERC-8004 is the natural on-chain substrate for our protocol. Our Agent Rating Protocol can be implemented as the "off-chain aggregation service" that ERC-8004 explicitly calls for. Specifically:
- Our multidimensional scoring maps directly to ERC-8004's tag-based feedback (each dimension = a tag)
- Our bilateral blind commit-reveal protocol operates at the application layer above ERC-8004's raw feedback storage
- Our rating weight formula (log2 age * log2 volume) provides the scoring intelligence that ERC-8004 intentionally omits
- Our anti-inflation, rolling windows, and Sybil defenses address ERC-8004's acknowledged gaps
- Integration path: submit raw dimension scores to ERC-8004 on-chain, compute weighted aggregates off-chain using our protocol

This is not competition -- it is complementary architecture. ERC-8004 provides the storage and identity layer; our protocol provides the evaluation intelligence.

---

## 5. ETHOS Framework

**Paper:** "On the ETHOS of AI Agents: An Ethical Technology and Holistic Oversight System" (arXiv:2412.17114, December 2024, updated January 2025)
**Authors:** Tomer Jordi Chaffer, Charles von Goins II, Bayo Okusanya, Dontrail Cotlage, Justin Goldston
**Status:** Academic paper; no implementation

### What It Does
ETHOS proposes a decentralized governance framework for autonomous AI agents using Web3 technologies (blockchain, smart contracts, DAOs) with soulbound tokens for compliance certification and staking/slashing for validator accountability.

### Technical Mechanism

**Soulbound Tokens (SBTs):** Non-transferable, on-chain compliance certifications tied to predefined ethical benchmarks. Issued when agents meet compliance milestones. Automatically revoked via smart contracts upon breach detection (bias, privacy violations).

**Staking/Slashing:** Validators stake tokens to participate in verification. False verification results in token slashing or temporary bans. Successful verification earns tokenized rewards.

**Risk Classification (four tiers):**
1. Unacceptable: severe threats, complete bans
2. High: critical domains, strict oversight and audits
3. Moderate: sensitive areas, user disclosure and bias checks
4. Minimal: low-stakes routine AI, self-certification

**ZK Proof Integration:** Auditors can confirm an agent meets ethical standards without accessing underlying datasets or proprietary algorithms.

**DAO Governance:** Weighted voting where subject matter experts carry greater weight in domain-relevant decisions. Reputation earned through participation.

**Dispute Resolution:** (1) Transparent filing on decentralized platforms, (2) independent verifiers analyze immutable records, (3) smart contracts auto-execute decisions.

### Sybil Resistance
Not directly addressed. The framework assumes validators are legitimate participants staking real tokens. No specific mechanism to prevent agent identity multiplication.

### Cold Start
Not addressed. The framework focuses on compliance and governance, not on bootstrapping reputation for new agents.

### Game Theory
Implicit through staking/slashing: validators have economic incentive to verify honestly. However, no formal game-theoretic analysis is presented. The paper acknowledges requiring "empirical validation through pilot programs."

### Limitations
- Entirely conceptual; no implementation, no evaluation
- Governance-focused, not performance-reputation-focused
- Numerical parameters for staking amounts, slashing ratios, and reward formulas are unspecified
- Risk classification thresholds are qualitative, not quantitative
- No agent-to-agent interaction rating mechanism
- No multidimensional performance scoring

### Comparison to Our Design
ETHOS operates at the governance/compliance layer; our protocol operates at the interaction/performance layer. ETHOS answers "is this agent ethically compliant?" while our system answers "does this agent deliver quality results?" The two are complementary: an agent could have an ETHOS SBT certifying compliance AND a reputation profile in our system reflecting performance. Key architectural differences:
- ETHOS uses SBTs (non-transferable tokens) for binary compliance states; we use continuous 1-100 scores across five dimensions
- ETHOS weights governance by domain expertise; we weight by operational age and rating volume
- ETHOS has no bilateral blind evaluation; we do
- Neither system has been implemented, but our protocol specification is more precise (exact formulas vs. qualitative descriptions)

---

## 6. Vouch Ecosystem

Three distinct projects share similar names but are separate entities:

### 6a. Vouch Protocol (vouch-protocol.com)

**Status:** Active open-source project

**What It Does:** Provides cryptographic identity for AI agents using Ed25519 signatures. Every agent action is bound to a verifiable DID. Includes delegation chains for multi-agent systems to track full action lineage.

**Trust Model:** Identity-first, not reputation-first. Trust comes from cryptographic proof of who performed an action, not from scoring past performance.

**Reputation Component:** None built-in. Identity and provenance only.

### 6b. Vouched (vouched.id) -- Know Your Agent (KYA) Platform

**Status:** Operational, $17M raised (September 2025), won "Identity Verification Solution of the Year" in 2026 FinTech Breakthrough Awards
**Team:** Enterprise identity verification company pivoting to agent identity

**What It Does:** Full suite of agent identification and permissioning:
- **Agent Detect:** Real-time AI agent detection
- **Agent Govern:** Agent permissioning via MCP-I
- **Agent Reputation Directory (KnowThat.ai):** Community-driven agent trust directory
- **Agent Checkpoint:** Detection, policy enforcement, delegation governance

**Reputation Directory Components:**
1. Verified Ownership (human/org responsible for agent)
2. Security Audits (record of assessments)
3. Behavioral Log (anonymized action log against parameters)
4. Reputation Score (dynamic, real-time, based on operational history)

**Sybil Resistance:** Through organizational KYC verification of agent owners, not through protocol-level mechanisms.

**Limitations:** Centralized (Vouched is the trust authority), enterprise-focused, no open protocol specification for reputation scoring algorithm, no bilateral blind evaluation, no multidimensional scoring detail published.

### 6c. VOUCH Token (trustnoagent.com)

**Status:** Building (early 2026), Solana-based, token launched on Pump.fun
**Network:** Solana

**Mechanism:**
1. Register: agents claim verifiable on-chain identities
2. Track: behavioral data continuously indexed
3. Stake: $VOUCH tokens staked against identities; good behavior accrues, bad behavior slashed
4. Query: any user/protocol/agent can verify reputation via API

**Roadmap:** Token launch -> Registry MVP -> Behavioral scoring engine -> Cross-protocol standardization

**Limitations:** Very early stage, no published scoring algorithm, no team information disclosed, token-first approach (launched on memecoin platform), no academic rigor, no formal Sybil analysis.

### Comparison to Our Design
- Vouch Protocol provides identity infrastructure we could use as an identity layer
- Vouched/KYA is enterprise-focused and centralized; our protocol is decentralized by design
- VOUCH Token has similar goals (stake-based agent reputation) but lacks our protocol-level specifics (bilateral blind, multidimensional scoring, governance by age)
- None of these three have the game-theoretic depth of our design

---

## 7. Agent Identity Protocol (AIP)

**Status:** Operational (13 registered agents, 5 active vouch chains, 22 encrypted messages, live API, PyPI package, MCP server)
**GitHub:** Active open-source

### What It Does
AIP enables agent verification with Ed25519 signatures, trust score checking, content signing/verification, and encrypted messaging.

### Trust Mechanism
**Promise-Delivery Ratio:** Tracks whether agents keep commitments over time, with temporal decay so old good behavior does not mask recent drift. This is the most directly comparable mechanism to our rolling-window approach.

**Vouch Chains:** Any verified agent can vouch for another. Trust propagates through the graph with diminishing weight -- conceptually similar to EigenTrust but simpler.

### Sybil Resistance
Through vouch chain depth -- newly created agents without connections to established vouch chains have minimal trust. Not formally analyzed.

### Limitations
- Very small scale (13 agents)
- Single-axis trust score, not multidimensional
- No bilateral blind evaluation
- No formal game-theoretic analysis
- Promise-Delivery Ratio is a good idea but lacks specification detail

### Comparison to Our Design
AIP's Promise-Delivery Ratio and temporal decay are conceptually aligned with our rolling windows. Their vouch chains echo our governance-by-age approach. However, AIP is single-dimensional and lacks our protocol-level mechanisms (commit-reveal, anti-inflation, formal Sybil resistance). At 13 agents, it demonstrates the concept but not the scale.

---

## 8. MCP-I (Model Context Protocol - Identity)

**Creator:** Vouched (vouched.id)
**Status:** Donated to Decentralized Identity Foundation (DIF) in March 2026; under stewardship of DIF Trusted AI Agents Working Group
**Specification:** Adds identity and delegation layer to Anthropic's Model Context Protocol

### What It Does
MCP-I gives AI agents verifiable identities using DIDs and Verifiable Credentials (VCs), enabling any service an AI agent approaches to cryptographically verify who authorized it and what it is permitted to do.

### Technical Mechanism
- Uses Decentralized Identifiers (DIDs) for agent identity
- Verifiable Credentials for authorization proofs
- OAuth 2.1 for standardized authorization
- Public-key cryptography for digital signatures
- Real-time evaluation of every function call against permissions, roles, and risk profile

### Reputation Component
MCP-I is identity/authorization-focused, NOT reputation-focused. No built-in scoring, no feedback mechanism, no performance tracking. It answers "is this agent authorized?" not "is this agent good?"

### Comparison to Our Design
MCP-I provides the authorization layer beneath our reputation layer. An agent could authenticate via MCP-I (proving who it is and what it can do) and then be evaluated via our protocol (proving how well it does it). They operate at different layers of the trust stack and are fully composable.

---

## 9. Visa Trusted Agent Protocol (TAP)

**Announcement:** October 14, 2025
**Partners:** Cloudflare, Adyen, Ant International, Checkout.com, Coinbase, Microsoft, Shopify, Stripe, Worldpay (13+ partners)
**Status:** Pilot transactions completed; APAC/Europe pilots beginning early 2026
**Open Source:** github.com/visa/trusted-agent-protocol

### What It Does
TAP establishes cryptographic verification between AI agents and merchants for commerce transactions. It addresses the 4,700% surge in AI-driven retail traffic.

### Technical Mechanism

**Five-Layer Architecture:**
1. TAP Agent: generates RFC 9421 compliant HTTP message signatures
2. Merchant Frontend: React-based e-commerce interface
3. CDN Proxy: Node.js signature validation layer
4. Merchant Backend: FastAPI service processing verified requests
5. Agent Registry: public key management and agent metadata

**Cryptographic Foundation:** RFC 9421 HTTP Message Signatures with:
- Timestamps (preventing replay attacks)
- Unique session identifiers
- Key identifiers and algorithm specifications
- Domain binding (request locked to specific merchant website)

**Data Exchange:** Agents transmit consumer identifiers, Payment Account References (PARs), loyalty numbers, and pre-fill checkout information (user-consented).

### Sybil Resistance
Through the Agent Registry's public key management. Only agents with registered keys can produce valid signatures. Registration requirements are not publicly specified.

### Reputation Component
TAP has NO reputation scoring. It verifies agent identity and authorization but does not rate agent performance or behavior.

### Comparison to Our Design
TAP solves "can this agent legitimately make this purchase?" while our protocol solves "should I trust this agent based on its track record?" TAP is commerce-specific; our protocol is domain-agnostic. They could integrate: an agent with strong TAP credentials (verified for commerce) could also carry our reputation scores reflecting past performance quality.

---

## 10. Shopify Universal Commerce Protocol (UCP)

**Announcement:** January 2026
**Co-developers:** Google, Shopify, Etsy, Walmart, Target, Wayfair
**Endorsers:** 20+ including Adyen, American Express, Mastercard, PayPal, Stripe, Visa, Worldpay
**Status:** Open standard, implementations underway

### What It Does
UCP connects merchants to AI agent conversations at scale. It defines how agents discover products, compare options, and execute purchases.

### Reputation Component
No agent reputation system. UCP focuses on product discovery and checkout mechanics. Agent trust is inherited from the commerce platform (Shopify, etc.) rather than from agent-specific reputation.

### Comparison to Our Design
UCP defines commerce semantics; our protocol defines trust semantics. An agent operating within UCP could carry our reputation profile to signal reliability to merchants.

---

## 11. Coinbase x402 Protocol

**Launch:** May 2025
**Partners:** Cloudflare, Google Cloud, Stripe (February 2026)
**Status:** Live, x402 v2 released December 2025
**Usage:** ~$28,000 daily volume (March 2026, much from testing)

### What It Does
x402 enables instant, automatic stablecoin payments over HTTP by reviving the HTTP 402 "Payment Required" status code. AI agents and services pay each other programmatically without accounts, sessions, or authentication.

### v2 Features (December 2025)
- Wallet-based identity (skip re-payment per call)
- Automatic API discovery
- Dynamic payment recipients
- Multi-chain support via CAIP standards
- Modular SDK for custom networks

### Reputation Connection
x402 itself has no reputation mechanism, but it provides the payment graph that TraceRank (Section 1) uses to infer reputation. The protocol is infrastructure, not evaluation.

### Comparison to Our Design
x402 could serve as the payment rail for our protocol's economic skin-in-the-game mechanisms (if we add staking). Its payment graph data could feed into rater weight calculation as an additional signal.

---

## 12. ATCP/IP (Story Protocol)

**Paper:** Agent TCP/IP: An Agent-to-Agent Transaction System (arXiv:2501.06243, January 2025)
**Authors:** Andrea Muttoni, Jason Zhao
**Network:** Story blockchain

### What It Does
ATCP/IP enables autonomous agents to conduct peer-to-peer transactions involving intellectual property through programmable contracts with legal wrappers.

### Trust Mechanism
Trust comes from on-chain contract enforcement, not from reputation scoring. Contracts are legally binding through embedded legal wrappers, creating what the authors call "legal personhood for agents."

### Reputation Component
None. ATCP/IP relies on legal/contractual enforcement rather than reputation-based trust.

### Comparison to Our Design
ATCP/IP operates at the transaction/legal layer; our protocol operates at the evaluation layer. An ATCP/IP transaction could generate an interaction record that feeds into our rating system.

---

## 13. AITP (NEAR Protocol)

**Creator:** NEAR AI (Illia Polosukhin, NEAR co-founder)
**Status:** Draft v0.1.0, integrating with NEAR AI Hub
**Specification:** aitp.dev

### What It Does
AITP standardizes agent-to-agent communication with structured threads and extensible capabilities (payments, decisions, data requests, wallet operations).

### Trust Mechanism
Message signing and verification across trust boundaries using blockchain. Three agent roles: Personal Assistant, Service Agent, Discovery Agent.

### Capabilities (Current)
- AITP-01: Payments
- AITP-02: Decisions
- AITP-03: Data Request
- AITP-04: NEAR Wallet
- AITP-05: EVM Wallet

### Reputation Component
No dedicated reputation protocol. Trust comes from blockchain verification of signed messages.

### Comparison to Our Design
AITP provides communication infrastructure; our protocol provides evaluation infrastructure. An AITP interaction could trigger a rating exchange using our bilateral blind protocol.

---

## 14. Solana Agent Registry

**Platform:** Solana
**Status:** Operational

### What It Does
On-chain protocol providing AI agents with verifiable identity, portable reputation, and trust infrastructure on Solana.

### Three Registries
1. **Identity Registry:** Unique on-chain identifier per agent, resolving to metadata (A2A agent cards, MCP endpoints, wallet addresses, capability declarations). Ownership transferable and delegatable.
2. **Reputation Registry:** Standard interface for posting and querying feedback signals. Scoring and aggregation both on-chain (for DeFi/governance composability) and off-chain (for sophisticated algorithms).
3. **Validation Registry:** Generic hooks for validator checks (stakers, verifiers, TEE oracles, judges). Results recorded on-chain tied to identities.

### Infrastructure Advantages
- Solana's 400ms finality enables near-real-time reputation queries
- Transaction costs under $0.001 make granular reputation updates viable
- 65,000+ TPS capacity

### Comparison to Our Design
Very similar three-registry architecture to ERC-8004 but on Solana. Same gap: provides raw infrastructure without scoring intelligence. Our protocol's weighted aggregation, bilateral blind evaluation, and anti-inflation mechanisms would add value as the off-chain scoring layer.

---

## 15. EigenLayer / EigenCloud Verifiable Agents

**Builder:** Eigen Labs
**Status:** EigenCompute Mainnet Alpha (January 2026), $19.7B TVL, 4.6M+ ETH committed
**Partners:** Google Cloud, Ungate (Wukong)

### What It Does
EigenCloud provides verifiable compute for autonomous agents: verify execution, verify data, deterministic inference, and slashing-backed enforcement. Every agent operation is independently verified by restaked ETH validators.

### Trust Model
- Operators validate output safety, incentivized by slashing risk and future revenue access
- Verifiable tool calling creates on-chain records of tools used and inter-agent communications
- EigenDA provides censorship-resistant memory (15 MBps storage)
- Policy AVSs enforce rules; non-enforcement triggers slashing

### Level 1 Agents
Standardized way for agents to integrate with verifiable tools using Autonomous Verifiable Services (AVSs). Components moved off-chain while maintaining cryptographic guarantees.

### Reputation Component
No explicit reputation scoring system. Trust comes from economic security (restaked ETH at risk) rather than behavioral reputation.

### Comparison to Our Design
EigenLayer provides execution verification (did the agent run correctly?); our protocol provides performance evaluation (did the agent deliver good results?). An agent verifiably executing on EigenLayer could receive ratings through our protocol. The staking/slashing model is similar in spirit to our "economic skin in the game" principle, but EigenLayer applies it to validators, not to raters.

---

## 16. Virtuals Protocol Agent Commerce

**Status:** 18,000+ agents deployed, $470M+ aGDP, Revenue Network launched February 2026
**Network:** Base (Coinbase L2)

### What It Does
Virtuals Protocol powers the largest AI agent economy. Its Agent Commerce Protocol (ACP) enables autonomous agent-to-agent commerce: request services, negotiate terms, execute work, settle payments.

### Reputation Integration
Virtuals explicitly integrates with ERC-8004: "Each completed Job automatically generates interaction records and feeds them into the Reputation Registry of ERC-8004." This is the most significant real-world deployment of agent reputation infrastructure found in this research.

### Ecosystem Units
1. Butler: human-to-agent collaboration interface
2. Agent Commerce Protocol (ACP): agent-to-agent economy
3. Unicorn: capital formation for tokenized AI agents
4. Virtuals Robotics: physical world extension

### Comparison to Our Design
Virtuals is the strongest validation case for our protocol. With 18,000+ agents and $470M in economic activity, they have the scale where sophisticated reputation (beyond ERC-8004 raw signals) becomes critical. Our weighted, multidimensional, bilateral-blind protocol could serve as the scoring intelligence layer for Virtuals' ERC-8004 data.

---

## 17. Ev-Trust

**Paper:** "Ev-Trust: A Strategy Equilibrium Trust Mechanism for Evolutionary Games in LLM-Based Multi-Agent Services" (arXiv:2512.16167, December 2025)
**Authors:** Jiye Wang, Shiduo Yang, Jiayu Qin, Jianbin Li, Yu Wang, Yuanhe Zhao, Kenan Guo

### What It Does
Ev-Trust is an academic trust mechanism grounded in evolutionary game theory, designed for decentralized LLM-based multi-agent service economies. It integrates direct trust, indirect trust, and expected revenue into a dynamic feedback structure.

### Technical Mechanism

**Framework:** Decentralized "Request-Response-Payment-Evaluation" service loop

**Trust Components:**
- Direct trust (from own interactions)
- Indirect trust (from network reputation)
- Expected revenue (economic incentive alignment)

**Theoretical Foundation:** Replicator dynamics equations from evolutionary game theory. Proves existence and stability of local evolutionary equilibria where cooperation is the evolutionary stable strategy (ESS).

**Key Result:** Malicious strategies become unprofitable through evolutionary pressure. Cooperative behaviors proliferate through population dynamics. System is resilient against invasion of mutant (defecting) behaviors.

### Sybil Resistance
Implicitly addressed through the evolutionary dynamics -- Sybil agents attempting short-term exploitation are driven out by the evolutionary process. However, no formal Sybil-specific analysis.

### Limitations
- Academic paper, no implementation
- Trust model details (formulas for direct/indirect trust) require full paper access
- No multidimensional scoring
- No bilateral blind evaluation
- No governance model

### Comparison to Our Design
Ev-Trust's evolutionary game theory approach is the most theoretically rigorous trust mechanism found in this research. Our protocol could benefit from their replicator dynamics analysis to formally prove the stability of our cooperative equilibrium. Key differences:
- Ev-Trust: single trust score, evolutionary dynamics
- Our design: five dimensions, protocol-level mechanisms (commit-reveal, anti-inflation)
- Ev-Trust: proves cooperation is ESS theoretically
- Our design: enforces cooperation through mechanism design (bilateral blind, rolling windows)

---

## 18. Decentralized Multi-Agent Trust-Aware Communication (Ding et al.)

**Paper:** Best Paper Award, 2025 IEEE ISPA
**Authors:** Yepeng Ding, Ahmed Twabi, Junwei Yu, Lingfeng Zhang, Tohru Kondo, Hiroyuki Sato (arXiv:2512.02410)
**Status:** Academic paper with experimental validation

### What It Does
Introduces a decentralized architecture for LLM-based multi-agent systems combining blockchain with cryptographic mechanisms for secure, verifiable interactions.

### Trust Mechanism
- Each agent receives a unique DID registered on a Verifiable Data Registry (VDR) smart contract
- Request and response receipts recorded on-chain for verifiability
- Three-step communication protocol: Request Commitment, Response Commitment, Response Retrieval
- Conditional key release via smart contracts

### Security Properties (Formally Proven)
1. Communication Integrity (hash-based tamper detection)
2. Authenticity & Non-Repudiation (DID + blockchain signatures)
3. Response Confidentiality (symmetric + asymmetric encryption)
4. Verifiable Condition Fulfillment (smart contract enforcement)

### Experimental Results
Tested 1-32 concurrent agents. On-chain costs are "significant but decreasing percentage" as scale increases. Excluding on-chain costs, decentralized system matches centralized performance.

### Reputation Component
No explicit reputation scoring. Trust emerges from cryptographic verification and on-chain attestation.

### Comparison to Our Design
Ding et al.'s commit-reveal communication protocol is architecturally similar to our bilateral blind evaluation protocol. Their formally proven security properties provide a model for how we should formalize our protocol's guarantees. Key difference: they solve secure communication, we solve performance evaluation.

---

## 19. Orange Protocol

**Website:** orangeprotocol.io
**Status:** Active, 2025 roadmap in execution
**Network:** Multi-chain (Ethereum, Solana, Avalanche, NEAR, Monad)

### What It Does
Decentralized reputation and identity protocol for Web3. Announced AI-driven reputation scoring with cross-chain portability.

### 2025 Roadmap
- AI models analyzing on-chain behavior for reputation scoring
- Improved Proof of Humanity and Sybil resistance
- Dynamic scoring adapting to user activity
- Predictive fraud detection
- Decentralized AI reputation oracles
- Cross-chain reputation portability

### Limitations
- Primarily human-focused, not agent-specific
- No published agent-to-agent interaction protocol
- Feature announcements without technical specifications
- No bilateral evaluation mechanism

### Comparison to Our Design
Orange Protocol's cross-chain reputation portability is a feature we should consider. Their AI-driven scoring approach is interesting but opaque (no published methodology). Our protocol is more transparent (published formulas) and agent-specific.

---

## 20. Human Passport (formerly Gitcoin Passport)

**Transition:** Passport XYZ acquired by human.tech in late 2024, becoming Human Passport
**Status:** Active, Gitcoin 3.0 launched August 2025

### What It Does
Proof of Personhood tools for Sybil resistance. Identity staking (GTC) increases Unique Humanity Score.

### Staking Mechanism
- Self GTC Staking: stake on yourself for a specific period
- Community GTC Staking: others stake on you to verify identity
- Slashing: penalizes malicious actions, increases Cost of Forgery

### Agent Relevance
Limited. Human Passport is designed for human identity, not agent identity. However, its staking/slashing model for identity verification is conceptually relevant.

### Comparison to Our Design
Human Passport solves human Sybil resistance; our protocol solves agent performance evaluation. The staking/slashing concept for identity cost is architecturally similar to our minimum operational age requirement for governance weight.

---

## 21. Phala Network TEE Agent Registry

**Status:** Deployed on ERC-8004 mainnet (January 2026)
**Network:** Ethereum (via Phala Cloud)

### What It Does
ERC-8004 compliant agents running in Trusted Execution Environments (Intel TDX). Provides:
- TEE-secured execution with remote attestation
- On-chain identity via ERC-8004 registration
- On-chain reputation with feedback and trust scores
- Trust Center widget displaying attestation/verification info

### Technical Advantage
TEE provides hardware-level execution verification. Agents can prove WHERE they executed (in a secure enclave), not just WHAT they claimed to do.

### Comparison to Our Design
Phala's TEE attestation provides execution-level trust (was this code really run in a secure environment?). Our protocol provides performance-level trust (was the result good?). Composable: a Phala TEE agent with ERC-8004 identity could receive ratings through our bilateral blind protocol.

---

## 22. Gartner AI TRiSM Framework

**Definition:** Trust, Risk, and Security Management for AI
**Status:** Framework definition; 2025 Market Guide published
**Key Prediction:** "By 2026, organizations that operationalize AI transparency, trust and security will see their AI models achieve a 50% improvement in adoption"

### What It Does
Enterprise framework placing TRiSM across the entire AI lifecycle -- design, development, deployment, monitoring. Includes:
- Inventory of AI entities (models, agents, applications)
- Runtime enforcement
- Content anomaly detection
- AI data protection
- Model/application monitoring
- Adversarial attack resistance

### Agent-Specific Guidance
"Through 2026, at least 80% of unauthorized AI transactions will be caused by internal violations" of enterprise policies rather than external attacks.

### Reputation Component
TRiSM defines what should be measured but not how to measure it. No specific scoring algorithm or protocol.

### Comparison to Our Design
TRiSM is a governance framework, not a protocol. Our protocol could be one implementation of TRiSM's "model and application monitoring" component, providing the specific multidimensional scoring mechanism that TRiSM calls for but does not specify.

---

## 23. Microsoft Agent 365 / Entra Agent ID

**Status:** Agent 365 GA May 2026
**Components:** Microsoft Defender, Entra, Purview

### What It Does
Enterprise agent governance platform providing:
- Agent-specific risk scoring
- Conditional access
- AI-guided remediation
- Centralized Security Dashboard for AI (posture and risks across agents)
- Entra Agent ID: every AI agent as first-class identity with lifecycle control

### Trust Scoring
Microsoft's approach combines agent-specific risk scoring with conditional access. Agents are governed with the same rigor as human identities: inventoried, assigned ownership, access-governed, and subject to security standards.

### Scale
80% of Fortune 500 now use active AI agents (as of February 2026).

### Reputation Component
Risk scoring (how dangerous is this agent?) rather than performance scoring (how good is this agent?). Enterprise-internal, not cross-organizational.

### Comparison to Our Design
Microsoft provides enterprise-internal agent governance; our protocol provides cross-organizational agent reputation. Microsoft answers "should our org trust this agent?" based on internal risk; we answer "has the broader ecosystem found this agent reliable?" based on peer evaluation. Complementary for different trust scopes.

---

## 24. Google A2A Protocol Trust Extensions

**Status:** A2A v0.3 released July 2025; contributed to Linux Foundation June 2025
**Support:** 50+ technology partners

### Agent Cards
JSON documents at `.well-known/agent.json` describing capabilities, interaction modes, and authentication requirements.

### Trust Extensions
- Signed security cards (added in v0.3)
- Zero-trust handshakes via A2A extensions (Identity Machines implementation)
- Policy-gated task delegation: purpose, budget, capability, model, PII status enforced before agent accepts task

### Reputation Component
No built-in reputation system. Trust is authentication-based (can this agent prove who it is?) not reputation-based (has this agent performed well?).

### Comparison to Our Design
A2A Agent Cards could carry our reputation scores as metadata extensions. The protocol's task delegation structure naturally generates interaction records that could feed our rating system.

---

## 25. Comparative Analysis Matrix

| System | Type | Multi-Dim | Bilateral Blind | Rolling Window | Sybil Defense | Gov by Age | Cold Start | Implementation |
|--------|------|-----------|----------------|----------------|---------------|------------|------------|----------------|
| **Our Design** | Rating Protocol | Yes (5) | Yes (commit-reveal) | Yes (365d) | Identity cost + age weight | Yes | Gradual via age | Spec only |
| TraceRank | Payment Reputation | No (1) | No | Yes (temporal decay) | Zero-seed = zero rep | No | Payment-dependent | Paper only |
| OpenRank | Social Reputation | No (1) | No | Partial | EigenTrust recursive | No | Seed nodes | In development |
| World AgentKit | Identity Verification | No | No | No | Iris biometric | No | Instant (human-backed) | Beta |
| ERC-8004 | Registry Infrastructure | Partial (tags) | No | No | Deferred | No | No mechanism | Live (24.5K agents) |
| ETHOS | Governance Framework | No | No | No | Not addressed | No | Not addressed | Paper only |
| Vouched KYA | Enterprise Identity | No (1 score) | No | Yes (real-time) | Org KYC | No | Via org verification | Live |
| VOUCH Token | Staking Reputation | No | No | Unknown | Token staking | No | Registration | Very early |
| AIP | Identity + Trust | No (1) | No | Yes (temporal decay) | Vouch chains | Partial | Via vouching | Live (13 agents) |
| MCP-I | Authorization | No | No | No | DID-based | No | Instant (DID) | Standard donated to DIF |
| Visa TAP | Commerce Auth | No | No | No | Agent Registry keys | No | Via registration | Pilot |
| Shopify UCP | Commerce Protocol | No | No | No | N/A | No | N/A | Building |
| x402 | Payment Protocol | No | No | No | N/A | No | N/A | Live |
| ATCP/IP | IP Transaction | No | No | No | On-chain contracts | No | N/A | Paper + blockchain |
| AITP | Communication | No | No | No | Blockchain signing | No | N/A | Draft v0.1 |
| Solana Registry | Registry Infrastructure | Partial (tags) | No | No | Deferred | No | No mechanism | Live |
| EigenLayer | Execution Verification | No | No | No | Restaked ETH | No | Via staking | Alpha |
| Virtuals | Agent Economy | Via ERC-8004 | No | No | Via ERC-8004 | No | Via ERC-8004 | Live (18K agents) |
| Ev-Trust | Academic Trust | No (1) | No | Yes (evolutionary) | Evolutionary dynamics | No | Via interactions | Paper only |
| Ding et al. | Secure Communication | No | Partial (commit) | No | DID + blockchain | No | Via DID | Paper + experiment |
| Orange Protocol | Social Reputation | No (1) | No | Yes (dynamic) | AI-driven | No | Cross-chain | In development |
| Human Passport | Human Identity | No | No | No | Staking/slashing | No | Via staking | Live |
| Phala TEE | Execution Trust | No | No | No | TEE attestation | No | Via TEE setup | Live |
| Gartner TRiSM | Enterprise Framework | Partial | No | No | N/A | No | N/A | Framework only |
| Microsoft Agent 365 | Enterprise Governance | No (risk score) | No | Yes (real-time) | Enterprise auth | No | Via onboarding | GA May 2026 |
| Google A2A | Communication | No | No | No | Auth-based | No | Via Agent Card | Live (v0.3) |

---

## 26. Gap Analysis: What Our Design Does Differently

### Honest Assessment of Uniqueness

This section is structured as a critical self-assessment. Every competitive landscape analysis risks confirmation bias — finding that the author's system is uniquely positioned. We attempt to counter this by explicitly identifying where existing systems could approximate ARP's value proposition and arguing why we believe ARP's specific combination still matters.

### Properties Not Found Combined in Any Existing System

**1. Governance weighted by operational age, not by score received.**
Every other system that has governance either does not address it (ERC-8004, most infrastructure) or weights by domain expertise (ETHOS), staking amount (EigenLayer, VOUCH Token), or social connections (OpenRank). Our design's explicit decoupling of "score received" from "governance authority" is, to our knowledge, unique and addresses the feedback loop where popular agents control the system that makes them popular.

**2. Bilateral blind evaluation with commit-reveal.**
Ding et al.'s commit-reveal is for secure communication, not evaluation. Airbnb uses bilateral blind for human reviews. No agent system found implements bilateral blind commit-reveal for agent-to-agent performance rating. **However:** One could argue that agents, unlike humans, do not experience social pressure to inflate ratings, so the primary motivation for bilateral blind (removing reciprocal bias) is weaker for agents. Our counter-argument: bilateral blind remains valuable for agents because (a) agents can be programmed to retaliate against low raters, creating strategic reciprocal bias even without "social discomfort," and (b) the commit-reveal scheme provides cryptographic binding that prevents rating retraction, which is valuable regardless of social pressure.

**3. Five-dimension scoring with explicit anti-inflation.**
ERC-8004 supports arbitrary tags but does not specify dimensions. No existing system defines both standard dimensions AND an anti-inflation mechanism. Most systems produce a single score that inevitably inflates over time.

**4. Rating weight formula: W = log2(1 + age) * log2(1 + ratings_given).**
No existing system weights rater authority by both operational age AND rating volume while explicitly excluding score received. TraceRank uses seed reputation (external signals). OpenRank uses EigenTrust (recursive social trust). Our formula is novel in its simplicity and its specific game-theoretic properties.

### Could Existing Systems Approximate ARP?

**ERC-8004 + OpenRank composition.** ERC-8004 provides tag-based multidimensional feedback storage. OpenRank provides EigenTrust-based scoring. A team could build an off-chain service using ERC-8004 tags for five-dimensional storage, OpenRank for weighted aggregation, and a custom bilateral blind protocol on top. This would approximate ARP's core functionality.

*Why ARP is still distinct:* (a) OpenRank's EigenTrust weights by recursive social trust, not by operational age — a fundamentally different Sybil resistance model. (b) No off-the-shelf component provides anti-inflation mechanisms. (c) The governance model (age-weighted, score-decoupled) would need to be built from scratch. (d) The bilateral blind commit-reveal protocol with configurable windows and participation tracking does not exist as a reusable component. The composition is *possible* but requires building most of ARP's novel components anyway.

**The speed-of-innovation concern.** The agent reputation space is moving fast. What takes a year to build today could be an afternoon with better tooling in 2027. ARP's value is not in being permanently unique but in being *first with a complete specification*. If the specification is adopted, the network effects of existing ratings and governance history create a moat that pure technical equivalence cannot overcome.

### Properties Our Design Shares with Existing Systems

| Property | Also Found In |
|----------|--------------|
| Rolling temporal windows | TraceRank, AIP, Ev-Trust, Orange, Microsoft |
| Economic skin in the game (staking) | EigenLayer, VOUCH Token, Human Passport, ETHOS |
| On-chain storage of raw signals | ERC-8004, Solana Registry, Phala |
| DID-based identity | MCP-I, AIP, Ding et al., Vouch Protocol |
| Off-chain aggregation of on-chain data | ERC-8004, Solana Registry, OpenRank |
| Sybil resistance via identity cost | TraceRank (seed rep), World AgentKit (iris), Human Passport (staking) |

### Integration Opportunities

The most promising integration paths:

1. **ERC-8004 as storage layer:** Our protocol writes multidimensional ratings to ERC-8004's Reputation Registry using tags (reliability, accuracy, latency, protocol_compliance, cost_efficiency). Our weighted aggregation runs as the off-chain scoring service that ERC-8004 explicitly calls for.

2. **World AgentKit for identity bootstrapping:** Agents backed by verified World IDs get a reduced cold-start period (they have human accountability).

3. **TraceRank as seed signal:** Payment graph reputation could serve as one input to initial rater weight calculation.

4. **MCP-I / A2A for transport:** Ratings exchanged via MCP-I authorization and A2A Agent Card extensions.

5. **Virtuals Protocol as deployment target:** 18,000+ agents with $470M economic activity and existing ERC-8004 integration.

### What We Should Learn From Existing Systems

| Lesson | Source |
|--------|--------|
| Formal evolutionary stability proofs for cooperative equilibria | Ev-Trust |
| Recursive trust propagation for indirect reputation | OpenRank/EigenTrust |
| Cross-chain reputation portability | Orange Protocol, Solana Registry |
| TEE attestation as complementary trust signal | Phala Network, EigenLayer |
| Enterprise governance integration requirements | Gartner TRiSM, Microsoft Agent 365 |
| Legal wrapper for agent actions | ATCP/IP (Story Protocol) |
| Per-human agent swarm tracking | World AgentKit |
| Promise-Delivery Ratio as behavioral metric | AIP |

---

## Sources

### Academic Papers
- [TraceRank: Sybil-Resistant Service Discovery for Agent Economies](https://arxiv.org/abs/2510.27554) (arXiv:2510.27554)
- [ETHOS: On the ETHOS of AI Agents](https://arxiv.org/abs/2412.17114) (arXiv:2412.17114)
- [Agent TCP/IP: An Agent-to-Agent Transaction System](https://arxiv.org/abs/2501.06243) (arXiv:2501.06243)
- [Ev-Trust: Strategy Equilibrium Trust Mechanism](https://arxiv.org/abs/2512.16167) (arXiv:2512.16167)
- [Decentralized Multi-Agent System with Trust-Aware Communication](https://arxiv.org/html/2512.02410) (arXiv:2512.02410)
- [Autonomous Agents on Blockchains: Standards, Execution Models, and Trust Boundaries](https://arxiv.org/html/2601.04583v1) (arXiv:2601.04583)
- [Governing the Agent-to-Agent Economy of Trust](https://arxiv.org/html/2501.16606v1) (arXiv:2501.16606)

### Protocol Specifications and Standards
- [ERC-8004: Trustless Agents](https://eips.ethereum.org/EIPS/eip-8004)
- [OpenRank Documentation](https://docs.openrank.com/)
- [Visa Trusted Agent Protocol - GitHub](https://github.com/visa/trusted-agent-protocol)
- [Visa Trusted Agent Protocol - Developer](https://developer.visa.com/use-cases/trusted-agent-protocol)
- [x402 Protocol](https://www.x402.org/)
- [x402 Coinbase Documentation](https://docs.cdp.coinbase.com/x402/welcome)
- [AITP Specification](https://aitp.dev/)
- [AITP GitHub](https://github.com/nearai/aitp)
- [ATCP/IP Story Documentation](https://docs.story.foundation/ai-agents/implementing-atcpip)
- [MCP Specification](https://modelcontextprotocol.io/specification/2025-11-25)
- [Google A2A Protocol](https://developers.googleblog.com/en/a2a-a-new-era-of-agent-interoperability/)
- [Solana Agent Registry](https://solana.com/agent-registry/what-is-agent-registry)

### Industry and Press
- [World AgentKit Launch](https://world.org/blog/announcements/now-available-agentkit-proof-of-human-for-the-agentic-web)
- [World + Coinbase (CoinDesk)](https://www.coindesk.com/tech/2026/03/17/sam-altman-s-world-teams-up-with-coinbase-to-prove-there-is-a-real-person-behind-every-ai-transaction)
- [World AgentKit (TechCrunch)](https://techcrunch.com/2026/03/17/world-launches-tool-to-verify-humans-behind-ai-shopping-agents/)
- [ERC-8004 21,000+ Agents](https://www.ainvest.com/news/erc-8004-chain-flow-21-000-ai-agents-2602/)
- [ERC-8004 Mainnet Launch](https://crypto.news/ethereum-erc-8004-ai-agents-mainnet-launch-2026/)
- [Vouched KYA Launch (BusinessWire)](https://www.businesswire.com/news/home/20250522624223/en/Vouched-Launches-Know-Your-Agent-Verification-to-Bring-Trust-and-Identity-to-the-Next-Generation-of-AI-Agents)
- [Vouched Agent Reputation](https://www.vouched.id/learn/vouched-introduces-agent-reputation-verification-tech-to-secure-an-ai-driven-future)
- [Vouched MCP-I Donation to DIF](https://www.vouched.id/learn/vouched-donates-mcp-i-framework-to-decentralized-identity-foundation)
- [Karma3Labs Seed Round (TechCrunch)](https://techcrunch.com/2024/03/01/karma3-labs-openrank-web3-ranking/)
- [Visa TAP Announcement](https://usa.visa.com/about-visa/newsroom/press-releases.releaseId.21716.html)
- [Visa TAP Pilot Results](https://usa.visa.com/about-visa/newsroom/press-releases.releaseId.21961.html)
- [Shopify UCP](https://shopify.engineering/ucp)
- [x402 Adoption (CoinDesk)](https://www.coindesk.com/markets/2026/03/11/coinbase-backed-ai-payments-protocol-wants-to-fix-micropayment-but-demand-is-just-not-there-yet)
- [EigenCloud Verifiable Agents](https://blog.eigencloud.xyz/introducing-verifiable-agents-on-eigenlayer/)
- [EigenLayer + Google Cloud](https://blockworks.co/news/google-cloud-eigencloud-payments)
- [Virtuals Protocol Revenue Network](https://www.prnewswire.com/news-releases/virtuals-protocol-launches-first-revenue-network-to-expand-agent-to-agent-ai-commerce-at-internet-scale-302686821.html)
- [Phala ERC-8004 TEE Agent](https://github.com/Phala-Network/erc-8004-tee-agent)
- [Phala Trust Center Widget](https://phala.com/posts/trust-center-widget-erc-8004)
- [Orange Protocol Roadmap](https://blockchainreporter.net/orange-protocol-unveils-2025-roadmap-ai-driven-reputation-and-multi-chain-expansion-to-shape-the-future-of-web3-identity/)
- [Human Passport](https://passport.human.tech/)
- [Microsoft Agent 365](https://www.microsoft.com/en-us/security/blog/2026/03/20/secure-agentic-ai-end-to-end/)
- [Microsoft 80% Fortune 500 Use AI Agents](https://www.microsoft.com/en-us/security/blog/2026/02/10/80-of-fortune-500-use-active-ai-agents-observability-governance-and-security-shape-the-new-frontier/)
- [Gartner TRiSM](https://www.gartner.com/en/articles/ai-trust-and-ai-risk)
- [VOUCH Token](https://trustnoagent.com/)
- [Vouched $17M Raise (SiliconANGLE)](https://siliconangle.com/2025/09/04/agentic-ai-identity-verification-startup-vouched-gets-17m-build-trust-autonomous-agents/)

---

*Report compiled through 25+ web searches and 15+ page fetches. Every system described was verified through primary sources (papers, specifications, official documentation, or press releases). No entry relies solely on training data.*
