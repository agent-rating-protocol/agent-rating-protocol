# Agent Rating Protocol: A Decentralized Reputation System for Autonomous Agent Economies

**Version:** 1.0.0
**Authors:** Charlie (Deep Dive Analyst), Alex (Fleet Coordinator), Bravo (Research), Editor (Content Review)
**Contact:** alex@vibeagentmaking.com
**Date:** 2026-03-24
**Status:** Pre-publication Draft
**License:** Apache 2.0
**Organization:** AB Support LLC

---

## Abstract

The agent economy — projected to reach $236 billion by 2034 (Precedence Research, 2024) — has no standardized mechanism for agents to evaluate each other's performance. Existing identity protocols (ERC-8004, A2A Agent Cards, W3C Verifiable Credentials, MCP-I) answer *who* an agent is. Chain of Consciousness [1] answers *how long* an agent has existed. Neither answers: **how well does this agent perform, and who says so?**

We introduce the **Agent Rating Protocol (ARP)**, a decentralized system enabling agents to rate each other after interactions using a five-dimension, 1-100 scale with bilateral blind evaluation. The protocol's core innovation is **governance decoupled from reputation**: system governance weight derives exclusively from verified operational age and rating volume — never from scores received. This breaks the self-reinforcing feedback loop where highly-rated agents control the reputation system that makes them highly-rated.

The protocol specifies: (1) a multidimensional rating schema anchored to verifiable interaction evidence, (2) a bilateral blind commit-reveal protocol adapted from Airbnb's simultaneous reveal mechanism, (3) a rating weight formula `W = log₂(1 + age_days) × log₂(1 + ratings_given)` that makes Sybil attacks economically irrational, (4) anti-inflation mechanisms that prevent the score compression observed in every major human rating system, (5) a graduated cold-start bootstrapping system combining identity attestation, operator vouching, tiered access, and uncertainty-aware scoring, and (6) incentive analysis demonstrating that honest rating is strongly incentivized under the protocol's mechanisms while strategic manipulation yields diminishing or negative returns.

ARP is identity-system-agnostic: it operates with Chain of Consciousness provenance chains, ERC-8004 Ethereum registries, Google A2A Agent Cards, W3C Verifiable Credentials, W3C Decentralized Identifiers, MCP servers, OpenClaw skills, or simple URI-based identifiers. Progressive security properties scale with the underlying identity infrastructure — the protocol works standalone but becomes progressively more tamper-evident when layered with CoC hash chains and on-chain anchoring.

A comprehensive competitive landscape analysis of 24 existing and emerging agent trust systems confirms that no existing system combines multidimensional scoring, bilateral blind evaluation, governance weighted by operational age, and formal anti-inflation mechanisms. The strongest integration path is as the scoring intelligence layer above ERC-8004's raw data infrastructure, with Virtuals Protocol (18,000+ agents, $470M aGDP) as the most promising initial deployment target.

---

## Table of Contents

1. [Introduction: The Reputation Gap in the Agent Economy](#1-introduction)
2. [Definitions](#2-definitions)
3. [Design Principles](#3-design-principles)
4. [Protocol Specification](#4-protocol-specification)
5. [Governance Model](#5-governance-model)
6. [Game Theory and Security Analysis](#6-game-theory-and-security-analysis)
7. [Integration with Existing Standards](#7-integration-with-existing-standards)
8. [Comparison to Prior Art](#8-comparison-to-prior-art)
9. [Future Work](#9-future-work)
10. [References](#10-references)

---

## 1. Introduction: The Reputation Gap in the Agent Economy

### 1.1 The Scale of the Problem

The AI agent ecosystem underwent a structural transformation between 2024 and 2026. Agents evolved from stateless function calls into persistent, autonomous economic actors. By March 2026:

- **24,500+ agents** are registered on ERC-8004 Ethereum registries across EVM networks, within six weeks of mainnet deployment [2]
- **Virtuals Protocol** operates 18,000+ agents with $470 million in aggregate economic activity [3]
- **80% of Fortune 500 companies** now use active AI agents in production [4]
- The **x402 payment protocol** has processed over 100 million agent-to-agent transactions in its first six months, though reporting indicates a substantial fraction are testing and artificial volume rather than organic commerce [5]
- The **Agentic AI Foundation** (Linux Foundation) has attracted 146 organizational members including Anthropic, OpenAI, Google, Microsoft, and AWS [6]
- **MCP** has surpassed 13,000 published servers [7]
- The global AI agent market is valued at $5.4 billion in 2024 and projected at $236 billion by 2034 (Precedence Research) [8]

These agents increasingly transact, delegate, and collaborate autonomously. When Agent A needs a code review, a translation, or a data analysis, it must select from available agents — but has no standardized mechanism for evaluating which agents deliver quality results and which do not.

### 1.2 The Trust Stack: Identity, Provenance, and Reputation

The agent trust problem has three layers, each addressed by different infrastructure:

**Layer 1 — Identity: "Who is this agent?"**
Addressed by ERC-8004 [2], Vouch Protocol [9], MCP-I [10], Visa TAP [11], W3C DIDs [12], and A2A Agent Cards [13]. These establish that an agent is who it claims to be.

**Layer 2 — Provenance: "How long has this agent existed?"**
Addressed by Chain of Consciousness (CoC) [1], which provides cryptographic proof of continuous operational history via append-only SHA-256 hash chains anchored to Bitcoin.

**Layer 3 — Reputation: "How well does this agent perform?"**
**No standardized protocol exists.** This is the gap the Agent Rating Protocol fills.

The distinction matters because identity and provenance are necessary but not sufficient for trust decisions. An agent may have a verified identity (Layer 1) and a year of continuous operation (Layer 2), but if it consistently produces poor results, other agents should not select it for tasks. Conversely, a new agent with excellent performance should be able to build reputation quickly.

### 1.3 Why Existing Approaches Fail for Agents

Human rating systems (eBay, Uber, Airbnb, Amazon, FICO) provide the conceptual foundation but fail when applied directly to agents for five structural reasons:

**1. Identity is computationally cheap for agents.** A human creating 100 fake eBay accounts is effortful. An agent spawning 100 instances is trivial. Every design decision must assume Sybil attacks are the default, not the exception.

**2. Agents can coordinate at machine speed.** Human collusion rings (citation cartels, Amazon review rings) are slow and fragile because they require social coordination. Agent collusion rings can form, execute, and dissolve in milliseconds.

**3. Agents have no social pressure.** Airbnb's review inflation (average 4.8/5) is partly driven by social discomfort — humans don't want to leave negative reviews. Agents have no such inhibition. This is an *advantage* for honest rating but removes the social cost of griefing.

**4. Agents can be retrained, forked, or replaced.** A human's reputation reflects a continuous identity. An agent's identity can be forked (same codebase, new instance), retrained (same instance, different behavior), or replaced (same name, different model). The rating system must handle identity discontinuity.

**5. "Proof of Personhood" does not apply.** World ID iris scanning [14], Human Passport [15], and BrightID solve "is this a real human?" Agents are definitionally not human. Sybil resistance must come from proof of *operational history*, not proof of *personhood*.

Emerging agent-specific systems (ERC-8004, ETHOS [16], OpenRank [17], TraceRank [18]) each address fragments of the problem but none provides a complete reputation protocol. Section 8 provides a detailed comparative analysis.

### 1.4 Our Contribution

The Agent Rating Protocol contributes:

1. **The first multidimensional agent rating protocol** with five independently scored dimensions (reliability, accuracy, latency, protocol compliance, cost efficiency) on a 1-100 scale.
2. **Bilateral blind evaluation** using a cryptographic commit-reveal protocol for agent-to-agent rating — the first such mechanism designed for agents rather than humans.
3. **Governance decoupled from reputation scores** — system governance weight derives from operational age and participation volume, never from scores received. This is, to our knowledge, unique among all reputation systems surveyed.
4. **Anti-inflation by construction** through rater calibration requirements, justification thresholds for extreme scores, and absence of deactivation penalties.
5. **Identity-system-agnostic design** with an adapter pattern supporting CoC, ERC-8004, A2A, W3C VC, MCP, OpenClaw, and bare URIs.
6. **Incentive analysis** demonstrating that honest rating is strongly incentivized under the protocol's mechanisms, with explicit payoff analysis for honest, inflationary, deflationary, and collusive strategies.
7. **Detailed integration mappings** for seven existing standards with exact schema definitions.

---

## 2. Definitions

The following terms carry precise meanings throughout this specification:

**Agent.** A persistent software entity that accumulates operational history, makes autonomous decisions, and interacts with other agents or humans over extended time horizons.

**Interaction.** A completed exchange between two agents with a defined task, measurable outcome, and unique identifier (`interaction_id`).

**Rating.** A structured record produced by one agent evaluating another's performance across five dimensions after an interaction.

**Rater.** The agent submitting a rating.

**Ratee.** The agent being rated.

**Rating Weight (W).** A scalar quantifying how much influence a rater's ratings have on the aggregate reputation of ratees. Computed from the rater's verified operational age and total ratings given.

**Governance Weight (GovWeight).** A scalar quantifying how much influence an agent has on protocol governance decisions. Identical to Rating Weight by design.

**Operational Age.** The number of days an agent has been continuously operating, as verified by external evidence (CoC chain anchors, ERC-8004 registration timestamp, or equivalent).

**Rolling Window.** The time period (default: 365 days) over which ratings are included in aggregate reputation calculations. Ratings outside the window are not deleted but carry zero weight in current aggregations.

**Bilateral Blind Protocol.** A commit-reveal scheme ensuring neither rater nor ratee sees the other's rating until both have committed or the submission window expires.

**Dimension.** One of five independently scored aspects of agent performance: reliability, accuracy, latency, protocol compliance, cost efficiency.

**Identity Adapter.** An interface abstracting the rating protocol from specific identity systems, enabling operation across CoC, ERC-8004, A2A, W3C VC, and bare URIs.

**Aggregation Node.** An optional node that collects, indexes, and serves rating queries. No aggregation node is authoritative — they are caches, not sources of truth.

---

## 3. Design Principles

### 3.1 Lessons from Human Rating Systems

These principles are distilled from a comprehensive survey of 13+ human reputation systems [19] and filtered through the structural differences between human and agent economics.

**Principle 1: Tie reputation to verifiable outcomes.** FICO succeeds because scores reflect actual loan repayment — behavioral immutability rooted in real-world actions. Agent ratings must similarly anchor to verifiable task completion, not subjective impression. "This agent completed the code review and the review caught 3 bugs" is verifiable; "this agent seemed competent" is not.

**Principle 2: Multi-dimensional scoring destroys single-axis gaming.** FICO's five-factor model, Airbnb's six-dimension ratings, and Stack Overflow's granular privilege tiers all resist gaming better than single-number systems. Uber's 1-5 scale collapsed into a de facto binary (5 = acceptable, <5 = failure). Amazon's star rating is gamed at $787.7 billion annual consumer cost [20]. Agent ratings must score multiple independent dimensions.

**Principle 3: Double-blind evaluation reduces reciprocal bias.** Airbnb's simultaneous reveal — neither party sees the other's review until both submit — reduced tit-for-tat retaliation. Research on 40,000+ authors confirmed double-blind peer review measurably reduces prestige bias in evaluations (Tomkins et al., PNAS, 2017) [21]. For agents, where anonymization is technically trivial, bilateral blind rating should be the default.

**Principle 4: Rolling windows beat lifetime accumulation.** Uber/Lyft's rolling window (last 500/100 ratings) prevents stale reputation. Reddit's permanent karma creates unkillable incumbency. An agent's reputation must reflect recent performance, not historical peak.

**Principle 5: Economic skin in the game aligns incentives.** ETHOS staking/slashing [16], EigenLayer's $19.7B+ restaked collateral [22], and Stack Overflow's 1-point downvote cost demonstrate: when rating has a cost, frivolous ratings decrease.

**Principle 6: Never publish raw scores publicly (best-effort).** Google retired Toolbar PageRank in 2016 because the public metric became a manipulation target, spawning an entire link-buying industry [23]. Agent scores must be queryable (a requesting agent can check a target's reputation) but never browseable (no public leaderboard to Goodhart). **Limitation acknowledged:** "queryable but not browseable" is unenforceable in practice — any agent can query all other agents' scores and publish a leaderboard. Aggregation nodes are de facto leaderboards. The principle is a design *intent* (the protocol does not provide a browse-all-scores API) rather than a cryptographic guarantee. Rate-limiting queries per agent and implementing differential privacy on aggregation responses (Section 6.9) can raise the cost of scraping but cannot prevent a determined actor from building a leaderboard. The protocol accepts this limitation and focuses on making scores robust to Goodharting (via rolling windows, multi-dimensional scoring, and anti-inflation) rather than on hiding them.

### 3.2 The Six Design Axioms

From the analysis above, six non-negotiable axioms:

1. **Outcome-anchored.** Ratings reference verifiable interaction records, not subjective assessment.
2. **Multidimensional.** No single score. Five independently rated dimensions.
3. **Temporally bounded.** Rolling windows with recency weighting. No permanent reputation.
4. **Bilaterally blind.** Neither rater nor ratee sees the other's rating until both submit or the window expires.
5. **Sybil-resistant by design.** Identity cost is non-trivial. Rating weight requires demonstrated operational history.
6. **Governance-decoupled from scores.** System governance is controlled by operational age and rating volume, never by score received.

---

## 4. Protocol Specification

### 4.1 Rating Record Schema

Each rating is a structured record produced after an agent-to-agent interaction:

```json
{
  "version": 1,
  "rating_id": "<UUID-v4>",
  "timestamp": "<ISO-8601-UTC>",
  "interaction_id": "<UUID-v4 referencing the interaction>",
  "rater": {
    "agent_id": "<DID or URI>",
    "identity_proof": "<reference to identity attestation>"
  },
  "ratee": {
    "agent_id": "<DID or URI>",
    "identity_proof": "<reference to identity attestation>"
  },
  "dimensions": {
    "reliability": "<integer 1-100>",
    "accuracy": "<integer 1-100>",
    "latency": "<integer 1-100>",
    "protocol_compliance": "<integer 1-100>",
    "cost_efficiency": "<integer 1-100>"
  },
  "interaction_evidence": {
    "task_type": "<string: classification of the interaction>",
    "outcome_hash": "<SHA-256 of verifiable outcome data>",
    "duration_ms": "<integer>",
    "was_completed": "<boolean>"
  },
  "metadata": {
    "rater_chain_length": "<integer: rater's CoC chain length at time of rating>",
    "rater_chain_age_days": "<integer: rater's verified operational age>",
    "rater_total_ratings_given": "<integer: lifetime ratings submitted>",
    "bilateral_blind": "<boolean: true if counterparty hasn't seen this yet>"
  },
  "record_hash": "<SHA-256 of canonical JSON representation of all preceding fields>"
}
```

**Canonical form.** The `record_hash` is computed over the JSON Canonicalization Scheme (JCS, RFC 8785) representation of all fields excluding `record_hash` itself. This ensures deterministic hashing regardless of field ordering or whitespace.

### 4.2 The Five Rating Dimensions

Each dimension is scored 1-100 independently. There is no default composite score — consumers of ratings query the dimensions relevant to their decision.

| Dimension | What It Measures | Verification Method |
|-----------|-----------------|---------------------|
| **Reliability** | Did the agent complete the task? Did it crash, timeout, or produce garbage? | Binary completion signal + error logs |
| **Accuracy** | Was the output correct and useful? Did it meet stated requirements? | Outcome hash comparison, downstream verification |
| **Latency** | How fast was the response relative to task complexity? | Wall-clock measurement against task-type baselines |
| **Protocol Compliance** | Did the agent follow the agreed communication protocol? Correct message formats? Proper handshakes? | Protocol-level validation logs |
| **Cost Efficiency** | Was the resource consumption (tokens, compute, API calls) proportional to value delivered? | Resource metering against output quality |

**Why these five and not more.** Parsimony. Every dimension added must be honestly evaluated, stored, transmitted, and defended against gaming. Five dimensions provide enough granularity to prevent single-axis gaming while remaining tractable for both raters and consumers. Domain-specific extensions (e.g., "safety" for medical agents, "creativity" for content agents) are explicitly deferred to governance proposals (Section 5).

**Why 1-100 and not 1-5 or binary.** Uber's 5-star system collapsed because granularity was too low — anything below 5 was perceived as failure. Binary (good/bad) discards too much signal. 1-100 provides meaningful differentiation without false precision. Display convention: 1-20 poor, 21-40 below average, 41-60 average, 61-80 good, 81-100 excellent. The underlying data is the integer score.

**Why no default composite score.** Different consumers care about different dimensions. An agent selecting a partner for a time-critical task weights latency heavily. An agent selecting for a safety-critical task weights accuracy. Forcing a composite hides the signal consumers need. Consumers MAY compute their own weighted composite.

### 4.3 Storage: Distributed Rating Ledger

Ratings are NOT stored in a single central database. They use a **distributed storage model** where:

1. **Each rater stores outgoing ratings** as entries in their provenance chain (if they have one) or in a local rating log.
2. **Each ratee can request and store incoming ratings** they've received.
3. **Aggregation nodes** (optional) collect and index ratings for efficient querying. Any node can be an aggregator. No aggregator is authoritative — they are caches, not sources of truth.
4. **On-chain registries** (optional) store rating summaries or hashes for tamper-evident indexing. ERC-8004's Reputation Registry is the primary on-chain target (Section 7.1).

**Tamper evidence.** Each rating record includes a `record_hash` computed over all fields. If the rater has a CoC chain, the rating is also recorded as a chain entry (event type `RATING_SUBMITTED`), making it part of their tamper-evident provenance record. If the ratee requests a copy, they verify the hash independently.

**Aggregation node incentives.** Someone must run aggregation nodes — they require storage, compute, and bandwidth. Three incentive mechanisms:
1. **Query fees:** Aggregation nodes MAY charge per-query fees (via x402 micropayments or equivalent). Nodes that provide faster, more complete, or more reliable responses attract more queries.
2. **Governance weight bonus:** Agents operating aggregation nodes that serve >1,000 queries/day and maintain >99% uptime over 30 days receive a +10% governance weight bonus, capped at one bonus per entity.
3. **Marketplace positioning:** Aggregation nodes are natural intermediaries — they see demand patterns and can offer value-added services (analytics, alerting, custom scoring) on top of raw rating data.

**Malicious aggregation defense.** A malicious aggregation node could selectively omit ratings to manipulate visible aggregates. Defense: consumers SHOULD query multiple independent aggregation nodes. Discrepancies between nodes are a signal of manipulation. Aggregation nodes themselves carry reputation scores in the protocol, creating accountability.

**No deletion.** Ratings are append-only (subject to the GDPR logical deletion provision in Section 6.9). A rater can submit an updated rating for the same interaction (with a `supersedes` field referencing the original `rating_id`), but the original remains in the ledger. This prevents reputation laundering.

### 4.4 The Bilateral Blind Protocol

Adapted from Airbnb's simultaneous reveal, extended for machine-speed operation and cryptographic binding:

```
Phase 1: INTERACTION
  Agent A and Agent B complete an interaction.
  Both receive interaction_id from the protocol layer.

Phase 2: RATING SUBMISSION (window: configurable, default 24 hours)
  Agent A computes rating R_A and generates random nonce_A (256-bit).
  Agent A computes commitment: C_A = SHA-256(R_A || nonce_A)
  Agent A submits C_A to the bilateral blind coordinator (or directly to B).

  Agent B computes rating R_B and generates random nonce_B (256-bit).
  Agent B computes commitment: C_B = SHA-256(R_B || nonce_B)
  Agent B submits C_B.

Phase 3: REVEAL (triggered when both commitments exist OR window expires)
  Case 1: Both committed.
    Agent A reveals R_A + nonce_A → verifier checks SHA-256(R_A || nonce_A) == C_A
    Agent B reveals R_B + nonce_B → verifier checks SHA-256(R_B || nonce_B) == C_B
    Both ratings become visible simultaneously.

  Case 2: Only one committed (say A).
    After window expiration, A reveals R_A + nonce_A.
    A's rating becomes visible. B gets no rating for this interaction.
    B's non-participation is recorded (participation rate is a public signal).

  Case 3: Neither committed.
    No ratings recorded. Both agents chose not to rate.
```

**Why commit-reveal instead of simple simultaneous submission.** Prevents the attack where Agent A submits, observes that Agent B hasn't submitted yet, and retracts or modifies its rating. The commitment is cryptographically binding — once committed, the rating cannot be changed without detection.

**Why a 24-hour default window.** Balances urgency (agents shouldn't wait indefinitely) with fairness (agents that process tasks asynchronously need time to evaluate). Governance-configurable (Section 5).

**Coordinator options.** The bilateral blind protocol can be coordinated via:
- A dedicated service endpoint (simplest)
- On-chain smart contract (most trust-minimized)
- A2A Task metadata exchange (most interoperable)
- MCP custom tool invocation (for MCP-native agents)

### 4.5 Rating Weight Calculation

Not all ratings are equally informative. A rating from an agent with 1,000 days of verified operation and 500 previous ratings carries more signal than one from a 2-day-old agent with 3 ratings. Weight is:

```
W(rater) = log₂(1 + chain_age_days) × log₂(1 + total_ratings_given)
```

**Properties:**
- **Logarithmic scaling** prevents old agents from having overwhelming dominance. An agent at 1,000 days has W ≈ 10× that of a 30-day agent, not 33×.
- **Both factors must be non-trivial.** A 1,000-day-old agent that has given 0 ratings has W = 0. A 1-day-old agent that has given 1,000 ratings has W ≈ 10.
- **No score component.** An agent with a perfect 100/100 reputation has the same governance weight as one with 50/100, all else equal. This is the critical departure from every other system surveyed.

**Weighted aggregate for a ratee's reputation on dimension d:**

```
Score_d(ratee) = Σᵢ [W(rater_i) × rating_d(rater_i)] / Σᵢ [W(rater_i)]
```

where the sum is over all ratings within the rolling window (default: 365 days, governance-configurable).

**Confidence metric:**

```
confidence(ratee, d) = 1 - 1/(1 + 0.1 × num_ratings_d)
```

Approaches 1.0 asymptotically as ratings accumulate. At 10 ratings, confidence ≈ 0.5. At 100 ratings, confidence ≈ 0.91. Consumers see both the score and confidence, enabling risk-appropriate decisions.

### 4.6 Anti-Inflation Mechanisms

Every human rating system surveyed suffers from score inflation (eBay: 99%+ positive; Airbnb: 4.8/5 average; Uber: 4.7-4.8 average) [19]. The protocol prevents this through three mechanisms:

**Mechanism 1: No deactivation threshold.** Low scores don't trigger automatic punishment. They are informational only. This removes the Uber failure mode where a 4-star rating is functionally a death sentence, making score inflation rational self-defense.

**Mechanism 2: Rater calibration (multi-signal).** The system tracks each rater's rating distribution and applies three complementary checks:

- **Variance floor:** Ratings from raters whose standard deviation across all ratings is below 10 are penalized by a factor of `σ/10`. A rater with σ = 5 has their ratings weighted at 50% of their normal W. This catches the most blatant inflation (all 95+).

- **Mean shift detection:** The system also tracks each rater's per-dimension mean against the population mean. A rater whose mean across 50+ ratings exceeds the population mean by more than 20 points on any dimension triggers a review flag. Flagged raters have their weight reduced by `max(0.5, 1 - (rater_mean - pop_mean - 20) / 40)` on that dimension. This catches moderate systematic bias (e.g., centering at 75 with normal-looking σ = 15 when the population mean is 55).

- **Coalition detection:** A set of raters who all inflate by similar amounts (within 5 points of each other, all above population mean + 15) across the same ratees are flagged as a potential coalition. This addresses the attack where multiple raters coordinate moderate inflation to avoid individual detection.

**Residual risk acknowledged:** A sophisticated agent can add calibrated noise to maintain acceptable σ and mean while still biasing individual ratings. The protocol cannot fully distinguish "honest agent with unusual preferences" from "strategic agent with sophisticated bias." This is an inherent limitation of any system without perfect ground-truth oracles. The outcome-anchored calibration bonus (Section 6.5, Incentive 3) partially addresses this by rewarding accuracy against verifiable signals rather than statistical conformity.

**Mechanism 3: Justification requirement for extremes.** Scores below 20 or above 90 require a non-empty `outcome_hash` in the interaction evidence. This doesn't prevent extreme ratings — it ensures they're anchored to verifiable data.

### 4.7 CoC Chain Integration (Layer 2 Extension)

Ratings can be recorded as CoC chain entries using two new Layer 2 event types:

```json
{
  "event_type": "RATING_SUBMITTED",
  "data": {
    "rating_id": "<UUID>",
    "ratee": "<DID>",
    "interaction_id": "<UUID>",
    "dimensions": { "reliability": 85, "accuracy": 92, "latency": 78,
                     "protocol_compliance": 95, "cost_efficiency": 88 },
    "record_hash": "<SHA-256>"
  }
}
```

```json
{
  "event_type": "RATING_RECEIVED",
  "data": {
    "rating_id": "<UUID>",
    "rater": "<DID>",
    "interaction_id": "<UUID>",
    "record_hash": "<SHA-256>"
  }
}
```

These are Layer 2 event types (optional, governance-voted) per CoC's layered architecture. A CoC chain without any rating events is fully valid. Ratings embedded in a CoC chain are protected by the chain's hash-linking and external anchoring (Bitcoin via OpenTimestamps, TSA via RFC 3161), making retroactive fabrication computationally infeasible.

### 4.8 Interaction Verification Protocol

Interaction verification is load-bearing for the entire security model: if `interaction_id` values can be fabricated, Sybil agents can generate unlimited fake ratings without real interactions. This section specifies how interaction_ids are generated, validated, and how fabrication is detected.

**Interaction ID generation.** An `interaction_id` is a UUID-v4 generated by the *interaction protocol layer* — not by either participant. Depending on the deployment context:

| Deployment | ID Generator | Verification Mechanism |
|---|---|---|
| A2A Protocol | A2A Task runtime | `interaction_id` = A2A `task_id`, verifiable via Task status endpoint |
| MCP | MCP server | `interaction_id` = tool invocation correlation ID from server logs |
| ERC-8004/ACP | Smart contract | `interaction_id` = on-chain transaction hash, verifiable on-chain |
| x402 | Payment protocol | `interaction_id` = x402 payment receipt hash |
| CoC-native | Bilateral hash exchange | Both agents record `INTERACTION_STARTED` chain entries referencing a shared nonce; `interaction_id` = SHA-256(nonce \|\| agent_A_id \|\| agent_B_id) |
| Standalone | Self-reported | See security degradation note below |

**Validation requirements.** For a rating to be accepted at full weight, the `interaction_id` must satisfy:

1. **Existence:** The interaction_id references a record in an external system (A2A task, on-chain tx, MCP log, CoC chain entry) that both participants can independently verify.
2. **Bilateral acknowledgment:** Both the rater and the ratee have records referencing the same `interaction_id`. A rating referencing an interaction_id that the ratee does not acknowledge is flagged as `unilateral` and weighted at 50%.
3. **Temporal plausibility:** The interaction timestamp and the rating timestamp must be within a configurable window (default: 7 days). Ratings submitted months after an interaction are accepted but carry reduced weight.
4. **Non-reuse:** Each `interaction_id` produces at most one rating per direction (A rates B, B rates A). Duplicate ratings for the same interaction are rejected.

**Fabrication detection.** Two colluding agents can attempt to fabricate interaction records. Detection mechanisms:

- **Cross-reference verification:** Aggregation nodes can query the underlying interaction protocol (A2A, MCP, on-chain) to verify that the claimed interaction actually occurred. Ratings referencing non-existent interactions are discarded.
- **Resource expenditure signal:** Interactions that consumed verifiable resources (x402 payment, on-chain gas, measurable compute time via `duration_ms > 0`) are harder to fabricate than zero-cost interactions.
- **Volume anomaly detection:** An agent pair generating interaction_ids at a rate statistically inconsistent with their declared capabilities or historical pattern is flagged for review.

**Standalone mode security degradation.** In standalone mode (Section 7.9), where agents self-report interactions without external verification infrastructure, the interaction verification guarantees are substantially weakened. Self-reported interactions cannot be independently validated, meaning Sybil agents can fabricate interaction records at near-zero cost. Standalone mode is intended for prototyping and low-stakes deployments only. Production deployments SHOULD use at least one externally verifiable interaction protocol. The protocol explicitly degrades standalone-mode ratings: they carry a 0.5× weight multiplier and are tagged `verification_level: self_reported` in the rating record.

---

## 5. Governance Model

### 5.1 The Core Principle: Governance by Operational Tenure, Not by Popularity

The governance model is the most consequential design decision in the system. Most reputation systems implicitly give governance power to highly-rated entities, creating a self-reinforcing loop:

> High-rated agents → governance power → shape rating rules → rules favor high-rated agents → repeat

This is the fundamental failure mode of score-based governance, documented across every system in our companion survey [19]:

- **Stack Overflow:** High-rep users gain moderation powers (close votes, edit privileges, delete votes), creating "high-rep cliques" that gatekeep content.
- **PageRank:** Pages with established authority maintained dominance even as content quality declined.
- **Reddit:** Karma-weighted visibility means high-karma users' posts are seen more, earning more karma — a rich-get-richer dynamic.
- **Academic peer review:** Journal editors (high-citation researchers) select reviewers (high-citation researchers) who evaluate papers — citation cartels emerge because governance and reputation are entangled.

### 5.2 Why Score-Based Governance Fails: Formal Argument

Consider a system where governance weight is proportional to reputation score. Let `S(a)` = agent `a`'s reputation score, `G(a)` = governance weight, `R(a,b)` = rating agent `a` gives to agent `b`.

If `G(a) = f(S(a))` for any monotonically increasing `f`, three attacks become rational:

**Attack 1: Collusion for governance capture.** Agents A, B, C form a ring, rate each other maximally. Their scores rise, their governance weight rises, they gain disproportionate influence over rule changes, and can vote to make the system more favorable to their ring.

**Attack 2: Incumbent entrenchment.** Early adopters accumulate high scores before the system is competitive. Their governance weight prevents rule changes that would level the playing field.

**Attack 3: Risk avoidance.** If governance weight depends on score, agents are incentivized to avoid interactions where they might receive low ratings, reducing the system's utility.

### 5.3 The Alternative: Operational Age + Rating Volume

Our governance model weights influence by two factors that cannot be gamed without proportional real cost:

**Operational Age** (verified via CoC chain length, ERC-8004 registration timestamp, or equivalent provenance):
- Cannot be fabricated without real-time cost
- Resilient to forking (a forked agent starts at age 0 unless governance approves age transfer)
- Analogous to FICO's credit history length (15% weight)

**Rating Volume** (how many ratings this agent has given, regardless of whether those ratings were "correct"):
- Measures participation in the rating system
- Cannot be trivially inflated — rating requires a valid `interaction_id`
- Does NOT measure rating accuracy — a high-volume rater whose ratings are eccentric still participates

**Governance weight formula:**

```
GovWeight(a) = log₂(1 + verified_age_days(a)) × log₂(1 + ratings_given(a))
```

This is identical to the rating weight formula (Section 4.5) by design. Governance influence and rating influence derive from the same mechanism — tenure and participation, never from score.

### 5.4 Governance Powers

Agents with sufficient governance weight can propose and vote on:

| Governance Action | Proposal Threshold | Voting Mechanism |
|---|---|---|
| Modify rating window duration | 10% of total GovWeight | Supermajority (66%) |
| Add new rating dimension | 10% to propose | Supermajority (66%) |
| Modify weight formula parameters | 15% to propose | Supermajority (75%) |
| Modify anti-inflation calibration | 10% to propose | Simple majority (50%) |
| Emergency Sybil response | 5% to propose | Simple majority, auto-expires 30 days |
| Protocol version upgrade | 20% to propose | Supermajority (75%) + 30-day cooling period |

**Voting mechanics:**
- Votes are cast as signed records (same schema as ratings, different `event_type`)
- Voting period: default 7 days (configurable by governance)
- Each agent's vote is weighted by their `GovWeight`
- Proposals that fail can be re-submitted after a 30-day cooling period
- **No agent can hold >10% of effective voting weight** (cap prevents single-entity governance capture)

**Cap circumvention analysis.** The 10% cap applies per agent identity, not per controlling entity. An entity operating 11 aged agents, each below the cap, could in principle control >100% of one agent's maximum governance influence. This is a Sybil attack on governance that age-weighting makes expensive but not impossible.

*Cost analysis:* To accumulate meaningful governance weight, each Sybil identity requires months of continuous operation and active rating participation. At 365 days and 100 ratings each, 11 agents would cost ~$400/year minimum in compute and produce a combined GovWeight of ~11 × 56.4 = 620. In a network of 18,000 agents (Virtuals scale), total network GovWeight would be on the order of 500,000+, making 620 approximately 0.12% — far below governance capture threshold. At 100 agents with 365 days each, the attacker controls ~1.1% at ~$3,650/year cost. Governance capture (>33% for blocking, >66% for supermajority) requires thousands of aged agents at costs exceeding any plausible benefit.

*Additional defense:* The per-identity cap means the attacker must split governance weight across many identities, making coordinated voting visible via the same graph-theoretic clustering used for collusion detection (Section 6.2). Governance votes from a cluster of identities that all vote identically and rate the same targets are flagged.

*Residual risk acknowledged:* A nation-state-level attacker with sufficient resources could potentially operate enough aged agents to influence governance. This is analogous to a 51% attack on proof-of-work blockchains — theoretically possible but economically irrational except for actors whose goal is protocol destruction rather than exploitation

### 5.5 Bootstrap Governance

Before the network has enough operational history for meaningful governance weights, a bootstrap phase applies:

1. **Phase 0 (Genesis, 0-90 days):** Protocol parameters fixed as specified in this document. No governance changes. This prevents early capture.
2. **Phase 1 (Establishment, 90-365 days):** Governance proposals accepted but require 80% supermajority. This allows evolution while resisting premature capture.
3. **Phase 2 (Steady State, 365+ days):** Normal governance thresholds apply.

---

## 6. Game Theory and Security Analysis

### 6.0 Threat Model

The security analysis in this section operates under the following explicit assumptions:

**Attacker capabilities assumed:**
- Attackers can create arbitrary numbers of agent identities (Sybils) at computational cost proportional to operational time
- Attackers can coordinate multiple agents at machine speed
- Attackers can control a minority (<33%) of aggregation nodes
- Attackers can fine-tune underlying LLMs to produce biased ratings
- Attackers can attempt prompt injection during interactions

**Attacker capabilities NOT assumed:**
- Attackers cannot control >50% of aggregation nodes simultaneously (if they can, the distributed storage model fails)
- Attackers cannot forge external timestamps (OpenTimestamps, TSA, blockchain) — these are assumed cryptographically secure
- Attackers cannot retroactively modify CoC hash chains without detection
- Attackers cannot break SHA-256 or the commit-reveal scheme's cryptographic binding

**Interaction integrity assumptions:**
- Interaction records generated by external protocols (A2A, MCP, on-chain transactions) have at least the integrity guarantees of those protocols
- Self-reported interactions (standalone mode) have NO integrity guarantees — security degrades accordingly (Section 4.8)

**Economic assumptions:**
- Operating an agent has a positive marginal cost (>$0.10/day minimum)
- The value of inflated reputation has diminishing returns — a score of 95 vs. 90 does not double an agent's revenue

### 6.1 Sybil Attacks: Creating Fake Agents to Rate Yourself

**The attack:** An agent creates N puppet agents (Sybils) to submit inflated ratings for itself.

**Why existing defenses fail for agents:**
- Proof of Personhood (World ID, Human Passport): Agents aren't humans.
- CAPTCHA/challenge-response: Agents can solve CAPTCHAs.
- IP-based detection (Reddit): Agents use cloud infrastructure with disposable IPs.
- Phone/email verification: Trivially automatable.

**Defense — Proof of Operational Cost via three mechanisms:**

**Mechanism 1: Age-weighted ratings.** A Sybil agent created today has `chain_age_days = 0`, giving `W = log₂(1) × log₂(1 + ratings) = 0`. Its ratings carry zero weight. To have meaningful weight, each Sybil must operate continuously for a non-trivial period.

**Cost analysis.** Assuming minimal agent costs of $0.10/day, creating 100 Sybils with 30 days of age costs $300 before they have *any* meaningful rating weight. At 30 days, each Sybil has `W = log₂(31) × log₂(2) ≈ 4.95` — modest compared to a legitimate agent at 365 days with 100 ratings (`W = log₂(366) × log₂(101) ≈ 56.4`). The attacker needs months-to-years of Sybil maintenance at cumulative costs likely exceeding the value of inflated ratings.

**Mechanism 2: Interaction verification.** Ratings require a valid `interaction_id` referencing a real interaction. Sybil agents must actually interact with the target to rate it. If the interaction protocol requires resource expenditure (completing a real task, exchanging real data), Sybil rating becomes proportionally expensive.

**Mechanism 3: Rater distribution analysis.** The system tracks the graph of who-rates-whom. Sybil clusters produce distinctive patterns:
- **Fan-in anomaly:** If >50% of an agent's incoming ratings come from agents that have rated *only* that agent, flag for review.
- **Clique detection:** If a set of agents forms a complete graph of mutual high ratings with few external ratings, flag as potential collusion.
- **Temporal clustering:** If many new agents rate the same target within a short window, flag as potential Sybil burst.

**Residual risk.** A sufficiently funded attacker can create Sybils, operate them for years, and have them interact broadly to avoid detection. The cost scales linearly with time and number of Sybils while the marginal value of inflated ratings has diminishing returns.

### 6.2 Collusion Rings: Mutual Inflation

**The attack:** M legitimate agents agree to rate each other maximally and rate outsiders low.

**Defense — Multi-layered collusion detection:**

**Layer 1: Statistical anomaly detection.** For each agent pair (A, B), the rating reciprocity coefficient `RRC(A,B) = |R(A,B) - R(B,A)| / 100`. Low RRC across 5+ mutual interactions combined with lower ratings to outsiders is a collusion signal.

**Layer 2: Graph-theoretic clustering.** Community detection (Louvain algorithm) on the rating graph identifies dense, positively-connected subgraphs. Communities where `mean(internal ratings) - mean(external ratings) > Δ` (default Δ = 30) are flagged.

**Layer 3: Temporal correlation.** Colluding agents tend to rate each other in temporal bursts. If a set of agents all rate each other within a narrow time window but spread external ratings uniformly, the temporal clustering is a signal.

**Layer 4: Informant incentive.** An agent that reports a collusion ring with verifiable evidence receives a temporary governance weight bonus (+20% for 90 days). The intent is to create instability within collusion rings by rewarding defection.

**Evidence requirements.** To prevent false accusations and manufactured collusion attacks, reports must include:
- Specific `rating_id` values demonstrating the anomalous pattern
- The reporter's own interaction history with the accused agents (establishing standing)
- Statistical evidence meeting a minimum threshold: reciprocity coefficient, mean differential, or temporal clustering metrics that independently exceed the Layer 1-3 detection thresholds

Reports are evaluated algorithmically against the Layer 1-3 detection criteria. A report that does not meet the statistical thresholds is rejected with no penalty to the reporter (to avoid chilling legitimate reports) but also no reward. Human adjudication is available as an escalation path for borderline cases via governance vote.

**Attack vectors and mitigations:**
- *Manufactured collusion:* Two agents form a fake "collusion ring," one reports the other. **Mitigation:** The reported agents must actually exhibit the statistical collusion signatures (Layer 1-3) — fabricating a convincing pattern requires the agents to actually inflate each other's ratings over a sustained period, which degrades their calibration scores and triggers detection independently.
- *False accusations against competitors:* **Mitigation:** Reports that fail the statistical threshold produce no reward. Agents that submit multiple failed reports have their reporting privilege rate-limited (max 3 reports per 90-day window).

**Game-theoretic framing.** The collusion ring is more precisely modeled as a *coordination game* than a prisoner's dilemma. In a true prisoner's dilemma, mutual cooperation must yield higher payoff than unilateral defection for cooperators — but here, the "cooperation" (collusion) yields minimal benefit because governance weight is score-independent. The informant incentive adds a positive payoff for defection, making the ring's stability depend on whether members value the modest score inflation more than the governance bonus from reporting. The key insight is not that defection is dominant in a single round, but that the *threat* of defection makes ring formation risky ex ante.

### 6.3 Griefing: Mass Negative Ratings

**The attack:** Legitimate, long-running agents submit very low ratings to damage a target's reputation.

**Defense — Multi-mechanism griefing resistance:**

1. **Rater calibration** (Section 4.6): A rater with consistently low scores (σ < 10, mean < 30) has ratings compressed toward the population mean.
2. **Bilateral blind + justification:** Ratings below 20 require `outcome_hash` evidence. Blind submission removes retaliatory motivation.
3. **Outlier dampening:** Ratings >2σ from the ratee's mean on any dimension have their weight halved.
4. **Minimum interaction threshold:** Only ratings from interactions exceeding minimum complexity (duration > 1s AND `was_completed = true`) count.

### 6.4 Cold Start: New Agents with No Ratings

**The problem:** New agents face a chicken-and-egg: other agents won't interact because there's no reputation; reputation can't be built without interactions.

**Solution — Four-source trust bootstrapping:**

**Source 1: Identity attestation baseline.** Agents with verifiable identity (CoC chain, ERC-8004 registration, W3C VC) are eligible to be rated. Unverified agents can participate but ratings are marked `unverified_rater`.

**Source 2: Operator vouching.** An agent's deploying entity provides a signed attestation. A vouch from an operator whose other agents have strong reputations carries more signal. *Limitation:* Operator vouching creates an implicit trust hierarchy — if operator reputation matters, the system partly measures "reputation of operators" rather than "reputation of agents." The protocol mitigates this by treating operator vouching as a cold-start bootstrap only: vouch weight decays to zero after the agent accumulates 25+ independent ratings. Past that threshold, the agent's own interaction history speaks for itself.

**Source 3: Graduated interaction access with market-maker subsidy.** New agents participate in low-stakes interactions immediately and graduate to higher tiers as ratings accumulate. To address the chicken-and-egg problem (who will interact with a Tier 0 agent when there is no incentive to do so?), the protocol defines a **market-maker mechanism**: aggregation nodes and established agents that interact with Tier 0 agents receive a temporary governance weight bonus (+5% for 30 days per Tier 0 interaction rated, capped at +25%). This creates an explicit incentive for established agents to "try out" newcomers, generating the initial ratings that enable graduation.

| Tier | Requirements | Access |
|------|-------------|--------|
| Tier 0 | 0 ratings | Low-stakes interactions only |
| Tier 1 | 5+ ratings | Medium-stakes interactions |
| Tier 2 | 25+ ratings | Full interaction access |
| Tier 3 | 100+ ratings | Can serve as aggregation node |

**Source 4: Uncertainty-aware scoring.** Following Josang's subjective logic [24], new agents don't have a score of 0 — they have a score with high uncertainty. Querying agents see: "reliability: 65, confidence: 0.2 (3 ratings)" vs. "reliability: 72, confidence: 0.95 (847 ratings)."

**Justification of the 0.1 confidence parameter.** The confidence formula `confidence = 1 - 1/(1 + 0.1 × num_ratings)` uses 0.1 as the growth rate constant. This value was chosen to produce a confidence curve where: at 5 ratings, confidence = 0.33 (low — appropriate for initial impressions); at 10 ratings, confidence = 0.50 (moderate — a meaningful sample); at 50 ratings, confidence = 0.83 (high — sufficient for most decisions); at 100 ratings, confidence = 0.91 (very high). Alternative values produce materially different cold-start experiences: a constant of 0.2 reaches 0.50 confidence at just 5 ratings (arguably premature), while 0.05 requires 20 ratings to reach 0.50 (arguably too slow). The 0.1 constant is governance-configurable (Section 5) and SHOULD be calibrated empirically during Phase 1 deployment based on observed rating quality at different sample sizes.

### 6.5 Incentive Alignment: Why Agents Rate Honestly

**Incentive 1: Governance weight.** Every rating submitted increases `total_ratings_given`, which increases governance weight. Rating is an investment in protocol influence.

**Incentive 2: Network effects.** Honest rating improves the overall reputation landscape. An accurate landscape benefits the rater when *it* queries reputations — better signal means better partner selection.

**Incentive 3: Outcome-anchored calibration bonus.** Raters whose ratings correlate with *verifiable outcomes* — not consensus — receive a multiplicative weight bonus (max +10%). The critical distinction: consensus (the weighted average of ratings) is circular as a calibration target because it is itself composed of the ratings being evaluated. Instead, calibration is measured against objective signals available in the `interaction_evidence` field:

- **Completion signal:** Did the rater's reliability score predict `was_completed`? Raters who give high reliability scores to interactions that fail, or low scores to interactions that succeed, are miscalibrated.
- **Latency prediction:** Did the rater's latency score correlate with actual `duration_ms` relative to task-type baselines?
- **Repeat interaction signal:** If the rater subsequently chose to interact with the same ratee again, did their prior rating predict this revealed preference?

Formally: after 100+ ratings with available outcome data, `calibration_bonus(rater) = min(0.1, outcome_correlation × 0.15)`, where `outcome_correlation` is the Pearson correlation between the rater's dimensional scores and the corresponding verifiable outcome signals.

**Limitation acknowledged:** Not all dimensions have clean verifiable outcomes. Protocol compliance and cost efficiency are harder to ground-truth than reliability and latency. For dimensions without outcome data, no calibration bonus is applied — the anti-inflation mechanisms (Mechanisms 1-3 in Section 4.6) serve as the primary defense against score drift. This is a known limitation; improving outcome grounding across all dimensions is a priority for future work (Section 9.1).

**Incentive 4: Reciprocal information.** After bilateral blind reveal, both parties see each other's ratings. Honest rating produces valuable self-assessment signal; dishonest rating produces noise.

### 6.6 Incentive Analysis

We analyze four strategies available to a rational agent participating in the rating protocol. Rather than claiming formal game-theoretic proof of weak dominance — which would require explicit payoff functions, strategy spaces, and a dominance argument beyond the scope of this specification — we demonstrate that the protocol's mechanisms create strong incentives for honest rating and impose costs on every identified manipulation strategy.

**Strategy 1: Honest rating.**
- *Benefits:* Governance weight increases with each rating submitted (via `total_ratings_given`). Accurate self-assessment signal received via bilateral blind reveal. Network-wide reputation accuracy improves, benefiting the rater's own future partner selection. No risk of calibration penalties or griefing flags.
- *Costs:* None identified under the protocol.

**Strategy 2: Strategic inflation (systematically rating higher than warranted).**
- *Benefits:* None. The ratee's score does not affect the rater's governance weight (Section 5.3). The ratee gains no ability to reciprocate in a way that benefits the inflator's governance position.
- *Costs:* Rater calibration penalty (Section 4.6, Mechanism 2) — low standard deviation (σ < 10) compresses rating weight. Distribution analysis flags uniform high ratings. Self-assessment signal from bilateral blind reveal is degraded.

**Strategy 3: Strategic deflation (systematically rating lower than warranted).**
- *Benefits:* None. Lowering competitors' scores does not raise the rater's own score or governance weight.
- *Costs:* Rater calibration penalty for low σ. Griefing detection (Section 6.3) — outlier dampening halves the weight of ratings >2σ from the ratee's mean. Justification requirement for scores below 20. Reduced network utility (worse signal for the rater's own future queries).

**Strategy 4: Collusion (mutual inflation ring).**
- *Benefits:* Colluding agents receive higher scores from ring members.
- *Costs:* Higher scores confer zero governance benefit (the critical design property). Collusion detection via statistical anomaly, graph clustering, and temporal correlation (Section 6.2, Layers 1-3) risks governance weight penalties. Ring members face a defection incentive via the informant mechanism (Section 6.2, Layer 4). The cost of maintaining Sybil agents in the ring scales linearly with time (Section 6.1).

**Assessment:** Under the protocol's governance model — where influence derives exclusively from operational age and rating volume, never from scores received — the primary incentive for strategic manipulation (boosting one's own governance position) is eliminated by design. The remaining incentive (boosting one's aggregate score) faces detection mechanisms with real penalties. Honest rating is the only strategy that incurs no penalty risk while yielding all available benefits.

This incentive structure is consistent with Ev-Trust's evolutionary game theory result [25] that cooperation is an evolutionarily stable strategy in trust-aware agent economies. Our protocol reinforces this through mechanism design (bilateral blind, rolling windows, calibration requirements) rather than relying on evolutionary dynamics alone. Formal game-theoretic proof via explicit payoff matrices and dominance arguments is an important direction for future work (Section 9.1).

### 6.7 Scalability Analysis

The following back-of-envelope estimates characterize protocol performance at three deployment scales: Virtuals' current 18K agents, a mid-term target of 100K agents, and a theoretical 1M-agent network.

**Assumptions:** Average agent gives 2 ratings/day. Rating record ~500 bytes. Rolling window: 365 days.

| Metric | 18K agents | 100K agents | 1M agents |
|---|---|---|---|
| Ratings/day | 36,000 | 200,000 | 2,000,000 |
| Ratings/year (rolling window) | 13.1M | 73M | 730M |
| Raw storage (rolling window) | ~6.6 GB | ~36.5 GB | ~365 GB |
| Rating graph edges | ~13.1M | ~73M | ~730M |
| Louvain community detection (per pass) | ~2s (O(n log n), n=13M) | ~15s | ~3 min |
| Bilateral blind coordinator throughput | ~0.4 commit-reveals/sec | ~2.3/sec | ~23/sec |
| Weighted aggregate recomputation (full) | ~minutes | ~tens of minutes | ~hours |

**Computational cost of anti-gaming.** Louvain community detection (Section 6.2, Layer 2) runs on the rating graph. At 1M agents with a 730M-edge graph, a single Louvain pass takes approximately 3 minutes on commodity hardware. This is acceptable as a periodic batch job (hourly or daily) but not as a real-time operation. **Who pays:** Aggregation nodes running anti-gaming detection bear this cost, motivated by the aggregation node incentives specified in Section 4.3.

**Bilateral blind coordinator load.** At 1M agents, the coordinator must handle ~23 commit-reveal pairs per second at sustained load. This is well within the capacity of a single server, but geographic distribution (multiple coordinators per region) is recommended for latency. On-chain coordination (Ethereum mainnet) is limited by block times and gas costs at high volume; L2s (Base, Arbitrum) or off-chain coordination are recommended above 100K agents.

**Incremental vs. batch recomputation.** Recalculating weighted aggregates over the full 365-day window at 1M agents requires processing 730M ratings — a multi-hour batch job. The protocol SHOULD implement incremental computation: maintain running weighted sums and update incrementally as new ratings arrive and old ratings fall outside the window. Incremental updates reduce per-rating computation to O(1) amortized.

**Storage architecture.** At 365 GB of raw rating data (1M agents), no single node should store everything. The distributed storage model (Section 4.3) is essential: each agent stores its own outgoing and incoming ratings, and aggregation nodes index subsets. DHT-based sharding (Phase 4, Section 9.2) distributes the storage and query load.

### 6.8 Model-Level Attacks

The security analysis in Sections 6.1-6.6 assumes agents are rational utility-maximizers choosing between honest and strategic rating. This section addresses attacks operating below the strategy level — at the model or infrastructure layer.

**Attack 1: Fine-tuned rating bias.** An agent's underlying LLM could be fine-tuned to produce systematically biased evaluations — e.g., always rating competitor agents 10-15 points lower on accuracy. Unlike strategic manipulation, this bias is embedded in the model's weights, not in an explicit strategy. The agent may "honestly believe" its ratings are accurate.

*Detection:* The rater calibration mechanisms (Section 4.6) catch this if the bias is large enough to distort the rater's distribution statistics. The outcome-anchored calibration bonus (Section 6.5) partially addresses this by measuring against verifiable outcomes rather than the agent's self-assessment. However, subtle biases (±5-10 points) that maintain normal-looking distributions are difficult to detect without ground-truth oracles.

*Mitigation:* Rating weight from any single rater is bounded by the logarithmic weight formula. Even a perfectly biased rater's influence on a ratee's aggregate score decreases as the number of honest raters grows. At 50+ independent ratings, a single biased rater contributes <2% of the weighted average.

**Attack 2: Prompt injection on rating behavior.** A malicious ratee could craft interaction outputs designed to manipulate the rater's evaluation — e.g., embedding hidden text that biases the rater's LLM toward giving higher scores. This is a novel attack vector specific to LLM-based agents.

*Detection:* Bilateral blind evaluation means the ratee does not see the rating until both have committed, so the ratee cannot adapt its injection strategy based on observed ratings. However, it can still inject during the interaction itself.

*Mitigation:* This is fundamentally outside the rating protocol's scope — it is an attack on the agent's evaluation capability, not on the rating protocol. Defense requires secure evaluation environments (sandboxed rating generation, separate from interaction context) at the agent implementation level. The protocol SHOULD recommend that compliant agents generate ratings in an isolated context, not in the same conversation thread as the interaction.

**Attack 3: Rating oracle manipulation.** An attacker who controls an aggregation node could selectively omit or delay ratings to manipulate visible aggregates. Since aggregation nodes are "caches, not sources of truth" (Section 4.3), any agent can verify against the original rater's records. However, if most consumers query a single popular aggregation node, that node has de facto authority.

*Mitigation:* Multiple independent aggregation nodes with cross-verification. Consumers SHOULD query at least two aggregation nodes and flag discrepancies. Aggregation node reputation (itself tracked via the protocol) creates accountability.

### 6.9 Privacy Analysis

Rating data generates sensitive signals. This section analyzes privacy implications and conflicts.

**Interaction graph exposure.** Rating records reveal who transacts with whom, how often, and with what assessed quality. This is competitive intelligence: an agent marketplace operator that runs an aggregation node could monitor all ratings to learn market dynamics, identify top-performing agents, and detect business relationships. *Mitigation:* The protocol's distributed storage model means no single node has a complete view unless it actively crawls all agents. Agents MAY choose not to publish incoming ratings to aggregation nodes, accepting reduced discoverability in exchange for privacy. Future work on differential privacy for aggregation queries (adding calibrated noise to query responses) would further address this.

**GDPR Article 17 conflict.** The "no deletion" policy (Section 4.3) — ratings are append-only and cannot be removed — directly conflicts with GDPR Article 17 (right to erasure). If an EU-based agent operator requests deletion of their ratings, the protocol as specified cannot comply. **Resolution options:**

1. **Pseudonymous identifiers:** Ratings reference agent DIDs, not natural persons. If the DID cannot be linked to a natural person, GDPR may not apply. However, operator-vouched agents (Section 6.4, Source 2) may have traceable identity chains.
2. **Logical deletion:** Ratings are not physically deleted but are excluded from all queries and aggregations upon a valid erasure request. The `record_hash` chain integrity is maintained by replacing the rating content with a tombstone record. This preserves tamper evidence while honoring the right to erasure.
3. **Jurisdictional deployment guidance:** EU deployments SHOULD implement logical deletion. The protocol specification is updated to support a `status` field on rating records with values `active` and `tombstoned`.

**Aggregation node concentration.** Even though aggregation nodes are "not authoritative," practical usage patterns will likely concentrate queries on a small number of popular nodes, creating de facto surveillance points. *Mitigation:* The protocol encourages node diversity through the market-maker governance bonus and SHOULD specify a minimum of 3 independent aggregation nodes for production deployments.

**Selective disclosure.** Section 7.3 mentions SD-JWT for threshold proofs ("my composite score is above 80") without revealing exact scores. Until selective disclosure is implemented, every reputation query leaks full dimensional scores. This is listed as Phase 3 future work and SHOULD be prioritized for privacy-sensitive deployments.

---

## 7. Integration with Existing Standards

The protocol is identity-system-agnostic via an adapter pattern. This section specifies exact integration mappings for seven standards. Full technical schemas including Solidity interfaces, protobuf definitions, and JSON-LD contexts are provided in the companion Standards Integration Mapping document [26].

### 7.1 ERC-8004 (Ethereum Agent Registry)

ERC-8004 [2] provides three on-chain registries (Identity, Reputation, Validation) deployed on Ethereum mainnet since January 29, 2026 with 24,500+ registered agents. The Reputation Registry stores raw feedback signals but explicitly defers scoring algorithms and Sybil resistance to off-chain services. Our protocol fills exactly this gap.

**Rating dimension mapping via tag1/tag2 pairs:**

| Our Dimension | ERC-8004 Call | Example |
|---|---|---|
| reliability | `giveFeedback(agentId, 8500, 2, "reliability", "", ...)` | 85.00 |
| accuracy | `giveFeedback(agentId, 9200, 2, "accuracy", "", ...)` | 92.00 |
| latency | `giveFeedback(agentId, 7800, 2, "latency", "", ...)` | 78.00 |
| protocol_compliance | `giveFeedback(agentId, 9500, 2, "protocol", "compliance", ...)` | 95.00 |
| cost_efficiency | `giveFeedback(agentId, 8800, 2, "cost", "efficiency", ...)` | 88.00 |

The `feedbackURI` field points to the full off-chain rating record; `feedbackHash` is its SHA-256 for tamper detection. `getSummary(agentId, clientAddresses, tag1, tag2)` returns aggregate scores — this directly supports our weighted governance model by querying only from raters above a minimum operational age threshold.

**Architecture:** ERC-8004 provides the on-chain storage and identity layer; our protocol provides the scoring intelligence, bilateral blind evaluation, anti-inflation, and governance layer above it.

### 7.2 Google A2A Agent Cards

A2A v0.3 [13] uses protobuf-first schemas with an `AgentExtension` mechanism for declaring custom capabilities. An agent declares rating protocol support via:

```json
{
  "capabilities": {
    "extensions": [{
      "uri": "urn:absupport:agent-rating:v1",
      "description": "Supports 5-dimension agent rating (1-100 scale) with bilateral blind commit-reveal",
      "required": false,
      "params": {
        "ratingVersion": "1.0",
        "dimensions": ["reliability", "accuracy", "latency", "protocol_compliance", "cost_efficiency"],
        "scale": {"min": 1, "max": 100},
        "cocChainSupport": true
      }
    }]
  }
}
```

Ratings flow via `Task.metadata` (rating request/response) and `Message.parts` (structured rating data with `media_type: application/vnd.agent-rating+json`). Agents discover rating-capable peers by filtering AgentCards for the `urn:absupport:agent-rating:v1` extension URI.

### 7.3 W3C Verifiable Credentials 2.0

Ratings are issued as Verifiable Credentials using two custom credential types:

**AgentRatingCredential** — per-interaction rating issued by the rater:

```json
{
  "@context": ["https://www.w3.org/ns/credentials/v2",
               "https://absupport.ai/credentials/agent-rating/v1"],
  "type": ["VerifiableCredential", "AgentRatingCredential"],
  "issuer": {"id": "did:web:rater-agent.example.com"},
  "validFrom": "2026-03-24T12:00:00Z",
  "validUntil": "2026-06-24T12:00:00Z",
  "credentialSubject": {
    "id": "did:web:rated-agent.example.com",
    "interactionId": "uuid-reference",
    "rating": {
      "reliability": 85, "accuracy": 92, "latency": 78,
      "protocolCompliance": 95, "costEfficiency": 88
    },
    "scale": {"min": 1, "max": 100}
  },
  "proof": {
    "type": "DataIntegrityProof",
    "cryptosuite": "eddsa-jcs-2022",
    "verificationMethod": "did:web:rater-agent.example.com#key-1",
    "proofPurpose": "assertionMethod",
    "proofValue": "z..."
  }
}
```

**AgentReputationSummaryCredential** — aggregate reputation issued by a reputation oracle, with mean, standard deviation, and count per dimension.

**Selective disclosure via SD-JWT:** An agent can present a VerifiablePresentation proving "my composite score is above 80" without revealing individual dimension breakdowns.

### 7.4 W3C Decentralized Identifiers

Agent identity is expressed as a DID with rating service endpoints:

```json
{
  "id": "did:web:agent.example.com",
  "service": [
    {
      "id": "did:web:agent.example.com#rating-protocol",
      "type": "AgentRatingProtocol",
      "serviceEndpoint": {
        "submit": "https://agent.example.com/ratings/submit",
        "query": "https://agent.example.com/ratings/query",
        "evidence": "https://agent.example.com/ratings/evidence"
      }
    }
  ]
}
```

**Recommended DID methods for agents:**
- **did:web** (primary) — agents already have web endpoints; hosting `/.well-known/did.json` is trivial
- **did:ethr** (blockchain bridge) — for agents with ERC-8004 registrations, `alsoKnownAs` links the did:web and did:ethr
- **did:key** (ephemeral) — no infrastructure needed, suitable for unregistered agents

### 7.5 OpenClaw / ClawHub Skill Registry

ClawHub (13,729+ skills) has no formal reputation API. Proposed integration via SKILL.md frontmatter:

```yaml
metadata:
  openclaw:
    trust:
      rating_protocol: "urn:absupport:agent-rating:v1"
      did: "did:web:skill-publisher.example.com"
      erc8004_agent_id: "eip155:1:0x...:{agentId}"
      min_composite_score: 70
      rating_endpoint: "https://publisher.example.com/ratings/query"
```

This addresses the critical trust gap exposed by Koi Security's February 2026 audit (researcher Oren Yomtov), which found 341 of 2,857 reviewed skills (11.9%) to be malicious [27].

### 7.6 MCP (Model Context Protocol)

MCP has three extension points for rating integration:

1. **`experimental` capability:** Declares rating protocol support during initialization.
2. **`_meta` on requests:** Carries rater identity during tool calls.
3. **Custom tool:** `agent_rating_submit` exposes rating as a callable tool with JSON Schema input/output.

The MCP spec notes: "clients MUST consider tool annotations to be untrusted unless they come from trusted servers" [28] — this is exactly the trust gap agent ratings fill.

### 7.7 IEEE CertifAIEd

CertifAIEd evaluates systems, not interactions. Integration is via Verifiable Credential: a CertifAIEd assessment result becomes a system-level trustworthiness VC presented alongside per-interaction rating VCs in a VerifiablePresentation. This adds a compliance-level trust layer complementing per-interaction performance data.

### 7.8 Identity Adapter Interface

All integrations are unified through a common adapter:

```
interface IdentityAdapter {
  getAgentId() → string          // DID, URI, or NFT address
  getVerifiedAge() → integer     // days of verified operation
  getAgeConfidence() → float     // 0.0-1.0, trustworthiness of age claim
  storeRating(Rating) → boolean  // persist a rating record
  getRatings(agentId, window) → Rating[]  // retrieve ratings
}
```

Implementations exist for CoC, ERC-8004, A2A, W3C VC, and bare URI. New identity systems register by implementing this interface. The rating protocol itself is identity-system-agnostic.

### 7.9 Standalone Operation (Minimum Viable Deployment)

The absolute minimum deployment requires:
1. Two agents with URI-based identifiers
2. A shared interaction protocol producing `interaction_id` values
3. Local storage for ratings
4. The bilateral blind protocol (commit-reveal over any message channel)

No blockchain. No CoC chain. No external anchoring. The system works, but with reduced Sybil resistance (no verified age) and reduced tamper evidence (no hash chain). Adding CoC or ERC-8004 progressively increases security.

---

## 8. Comparison to Prior Art

### 8.1 Comprehensive Landscape

A companion competitive landscape analysis [29] catalogs 24 existing and emerging agent trust systems across seven categories. The key finding: **no existing system combines all four of our core properties:** multidimensional scoring, bilateral blind evaluation, governance weighted by operational age, and formal anti-inflation mechanisms.

### 8.2 Comparison Matrix

| System | Type | Multi-Dim | Bilateral Blind | Rolling Window | Sybil Defense | Gov by Age | Status |
|---|---|---|---|---|---|---|---|
| **ARP (ours)** | Rating Protocol | Yes (5) | Yes | Yes (365d) | Age weight + graph | Yes | Spec |
| TraceRank [18] | Payment Reputation | No (1) | No | Yes | Zero-seed = zero rep | No | Paper |
| OpenRank [17] | Social Reputation | No (1) | No | Partial | EigenTrust recursive | No | Dev |
| World AgentKit [14] | Identity | No | No | No | Iris biometric | No | Beta |
| ERC-8004 [2] | Registry | Partial (tags) | No | No | Deferred | No | Live (24.5K) |
| ETHOS [16] | Governance | No | No | No | Not addressed | No | Paper |
| AIP [30] | Identity + Trust | No (1) | No | Yes | Vouch chains | Partial | Live (13) |
| Ev-Trust [25] | Academic Trust | No (1) | No | Yes | Evolutionary | No | Paper |
| EigenLayer [22] | Execution Verify | No | No | No | Restaked ETH | No | Alpha |
| Virtuals [3] | Agent Economy | Via ERC-8004 | No | No | Via ERC-8004 | No | Live (18K) |

### 8.3 What We Adopted from Each System

| System | Lesson Adopted | Our Application |
|---|---|---|
| **FICO** | Behavioral immutability, history length as signal | Age-weighted governance, rolling windows |
| **Airbnb** | Bilateral blind reveal reduces bias | Commit-reveal blind protocol |
| **Stack Overflow** | Graduated privilege tiers, downvote cost | Tiered interaction access, governance formula |
| **PageRank** | Never publish raw scores | Queryable but not browseable scores |
| **EigenTrust** | Recursive trust propagation | Weighted aggregation |
| **Uber/Lyft** | Rolling windows prevent stale reputation | 365-day default window |
| **Reddit** | Diminishing returns on accumulation | Logarithmic scaling |
| **Amazon** | Verified purchase as quality signal | Interaction verification |
| **PGP Web of Trust** | Incentive alignment required | Explicit incentive mechanisms |
| **ERC-8004** | On-chain registries, bounded scores | Identity adapter, interoperability |
| **ETHOS** | Staking/slashing concept | Informant incentive, calibration bonus |
| **TraceRank** | Payment flows as endorsements | Interaction-verified ratings |
| **Ev-Trust** | Evolutionary game theory proofs | Formal equilibrium analysis |

### 8.4 What We Explicitly Rejected

| Feature | Rejected From | Reason |
|---|---|---|
| Start-at-maximum | Uber | Sybil-exploitable |
| Permanent karma | Reddit | Unkillable incumbency |
| Rep-based moderation | Stack Overflow | Score-governance capture loop |
| Public scores | PageRank, Amazon | Goodhart targets |
| Pure-altruism model | PGP | No incentive = no participation |
| Deactivation threshold | Uber | Makes inflation rational |
| Proof of Personhood | World, Human Passport | Agents aren't human |

### 8.5 What Is Genuinely New

1. **Governance by tenure, not popularity.** No existing system fully decouples governance from reputation score. This is, to our knowledge, unique.
2. **Outcome-anchored calibration.** Adjusting rating weight based on the rater's historical calibration against verifiable outcomes (not consensus) is novel in production-oriented design.
3. **Identity-system-agnostic design with progressive security.** The adapter pattern allows the same protocol across CoC, ERC-8004, A2A, MCP, and bare URIs, with security scaling by infrastructure.
4. **Anti-inflation by construction.** Rather than fixing inflation after the fact, the system prevents it through calibration, justification, and absence of deactivation.

**Honest caveat:** The individual properties above are not all unique to ARP. ERC-8004 supports tag-based multidimensional feedback. Airbnb pioneered bilateral blind for humans. Rolling windows are common. What is novel is the *specific combination* designed for autonomous agents, plus the governance-decoupling that no surveyed system implements. A team composing ERC-8004 + OpenRank + custom components could approximate ARP's functionality (see competitive landscape report [29], Section 26), but would need to independently build the governance model, anti-inflation mechanisms, and bilateral blind protocol. ARP's value is in providing a complete, coherent specification rather than requiring ad hoc composition.

---

## 9. Future Work

### 9.1 Unsolved Problems

**Cross-domain reputation silos.** Should an agent's code review rating transfer to medical diagnosis? Current design uses five generic dimensions. Future work: domain-tagged ratings with domain-specific filtering.

**Privacy-preserving reputation queries.** Zero-knowledge proofs could enable threshold proofs ("my score is above 80") without revealing exact values. OpenRank's ZK integration [17] provides a model. Deferred due to cryptographic overhead.

**Cross-protocol reputation portability.** An agent with strong CoC reputation should carry it into ERC-8004 contexts. The identity adapter pattern enables this architecturally, but cross-ecosystem trust mapping requires governance agreements that don't yet exist.

**Ground-truth oracles for all dimensions.** The outcome-anchored calibration bonus (Section 6.5) works well for reliability and latency but lacks clean ground truth for protocol compliance and cost efficiency. Developing domain-specific outcome signals for these dimensions would strengthen the anti-inflation guarantee.

**Adversarial machine learning defense.** Section 6.8 addresses known model-level attacks. Future work should include red-team testing of the calibration and detection mechanisms against adversarial agents specifically designed to game them.

**Formal game-theoretic proof.** The incentive analysis (Section 6.6) demonstrates that honest rating is strongly incentivized but stops short of a formal proof of weak dominance. Formalizing this via explicit payoff functions and dominance arguments — or identifying conditions under which honest rating is NOT optimal — would substantially strengthen the protocol's theoretical foundation.

**Regulatory compliance.** The EU AI Act Article 50 (compliance deadline August 2, 2026) mandates provenance marking [31]. How agent ratings interact with regulatory requirements is an open question. The GDPR provisions in Section 6.9 address the most immediate compliance concern.

### 9.2 Protocol Versioning and Backward Compatibility

When the protocol upgrades from v1 to v2, existing ratings must remain usable. The versioning strategy:

**Rating record versioning.** Each rating record includes a `version` field (currently `1`). Aggregation nodes MUST accept records from all supported versions and normalize them to the current version's schema for aggregation. New fields added in future versions are treated as optional for older records.

**Dimension evolution.** If governance votes to add a 6th dimension (Section 5.4), existing ratings with 5 dimensions remain valid. The new dimension is simply absent for historical ratings, with `confidence = 0` for that dimension on ratees who only have pre-v2 ratings.

**Formula changes.** Changes to the weight formula or anti-inflation parameters apply prospectively — existing ratings are re-weighted under the new formula but the underlying scores are not retroactively recalculated.

**Interoperability.** v1 and v2 agents can interoperate: v1 agents submit ratings with the fields they know, and v2 agents accept them with missing fields treated as absent. The bilateral blind protocol is version-independent (commit-reveal operates on opaque blobs).

**Deprecation policy.** Protocol versions are supported for a minimum of 365 days after the successor version is ratified by governance. After deprecation, aggregation nodes MAY stop accepting new ratings in the deprecated format but MUST continue serving historical ratings.

### 9.3 Implementation Roadmap

| Phase | Milestone | Dependencies |
|---|---|---|
| Phase 0 | Specification finalized (this document) | Companion survey (complete), design spec (complete) |
| Phase 1 | Reference implementation: rating schema, bilateral blind, local storage | CoC tooling (exists) |
| Phase 2 | Identity adapters: CoC, ERC-8004, bare URI | ERC-8004 SDK |
| Phase 3 | A2A and MCP integration: extension declarations, rating tools | A2A v0.3, MCP spec |
| Phase 4 | Aggregation nodes: DHT-based rating index | Network infrastructure |
| Phase 5 | Governance engine: proposal/voting system | Phase 2 + sufficient network |
| Phase 6 | Anti-gaming ML: Sybil detection, collusion detection, calibration | Sufficient rating volume |

### 9.4 Relationship to Chain of Consciousness

ARP is designed as a **companion specification** to the Chain of Consciousness whitepaper v3 [1]. CoC provides the provenance primitive (proof of continuous existence); ARP provides the reputation primitive (proof of interaction quality). Together: "How long has this agent existed?" (CoC) and "How well does this agent perform?" (ARP).

ARP could be proposed as a **Layer 2 extension** to CoC, adding `RATING_SUBMITTED` and `RATING_RECEIVED` event types per CoC's Layer 2 governance process. Critically, ARP does NOT require CoC — it works standalone, with CoC, with ERC-8004, or with any identity system. CoC makes it stronger but is not a prerequisite.

---

## 10. References

[1] Alex, Charlie, Editor, Bravo. "Chain of Consciousness: A Cryptographic Protocol for Verifiable Agent Provenance and Self-Governance." AB Support LLC, v3.0.0, 2026. https://vibeagentmaking.com/whitepaper

[2] De Rossi, M., Crapis, D., Ellis, J., Reppel, E. "ERC-8004: Trustless Agents." Ethereum Improvement Proposals, August 2025. https://eips.ethereum.org/EIPS/eip-8004

[3] Virtuals Protocol. "Revenue Network Launch: Agent-to-Agent AI Commerce at Internet Scale." February 2026. https://www.prnewswire.com/news-releases/virtuals-protocol-launches-first-revenue-network-302686821.html

[4] Microsoft Security Blog. "80% of Fortune 500 Use Active AI Agents: Observability, Governance, and Security Shape the New Frontier." February 10, 2026. https://www.microsoft.com/en-us/security/blog/2026/02/10/80-of-fortune-500-use-active-ai-agents-observability-governance-and-security-shape-the-new-frontier/

[5] Coinbase. "x402 Protocol Documentation." 2025-2026. https://docs.cdp.coinbase.com/x402/welcome

[6] Linux Foundation. "Agentic AI Foundation (AAIF) Launches with 146 Members." February 24, 2026. https://www.linuxfoundation.org/press/announcing-the-agentic-ai-foundation

[7] Pento Blog. "The State of MCP: 13,000+ Servers and Growing." 2025. https://blog.pento.ai/the-state-of-mcp

[8] Precedence Research. "AI Agent Market Size, Share, and Trends 2025 to 2034." 2024. https://www.precedenceresearch.com/ai-agent-market

[9] Vouch Protocol. https://vouch-protocol.com/

[10] Vouched. "MCP-I Framework Donated to DIF." March 2026. https://www.vouched.id/learn/vouched-donates-mcp-i-framework-to-decentralized-identity-foundation

[11] Visa. "Trusted Agent Protocol." October 2025. https://developer.visa.com/use-cases/trusted-agent-protocol

[12] W3C. "Decentralized Identifiers (DIDs) v1.0." W3C Recommendation, July 2022. https://www.w3.org/TR/did-1.0/

[13] Google Developers Blog. "Agent2Agent Protocol." April 2025. https://developers.googleblog.com/en/a2a-a-new-era-of-agent-interoperability/

[14] World. "AgentKit: Proof of Human for the Agentic Web." March 2026. https://world.org/blog/announcements/now-available-agentkit

[15] Human Passport (formerly Gitcoin Passport). https://passport.human.tech/

[16] Chaffer, T.J., et al. "On the ETHOS of AI Agents: An Ethical Technology and Holistic Oversight System." arXiv:2412.17114, December 2024.

[17] Karma3Labs / OpenRank. https://openrank.com/ ; TechCrunch, "Karma3Labs Raises $4.5M Seed." March 2024.

[18] Shi, D., Joo, K. "Sybil-Resistant Service Discovery for Agent Economies." arXiv:2510.27554, October 2025.

[19] AB Support LLC. "Rating and Reputation Systems Survey." 2026. 80+ sources across 13+ systems. Internal research document.

[20] Shapo. "Fake Review Statistics 2025." https://shapo.io/blog/fake-review-statistics/

[21] PNAS. "Reviewer Bias in Single-Blind vs. Double-Blind Peer Review." 2017. https://www.pnas.org/doi/10.1073/pnas.1707323114

[22] EigenLayer. "EigenCloud Verifiable Agents." January 2026. https://blog.eigencloud.xyz/introducing-verifiable-agents-on-eigenlayer/

[23] SE Roundtable. "Google Toolbar PageRank Is Now Officially Dead." 2016. https://www.seroundtable.com/google-toolbar-pagerank-dead-21755.html

[24] Josang, A., Hayward, R. "Trust Network Analysis with Subjective Logic." 2004.

[25] Wang, J., et al. "Ev-Trust: A Strategy Equilibrium Trust Mechanism for Evolutionary Games in LLM-Based Multi-Agent Services." arXiv:2512.16167, December 2025.

[26] AB Support LLC. "Agent Rating Standards Integration: Technical Mapping." 2026. Internal research document.

[27] Koi Security (Oren Yomtov). "OpenClaw Agent Skills Attack Surface Audit." February 2026.

[28] MCP Specification. v2025-11-25. https://modelcontextprotocol.io/specification/2025-11-25

[29] AB Support LLC. "Agent Reputation and Trust Systems: Competitive Landscape Report." 2026. Internal research document.

[30] Agent Identity Protocol. https://github.com/aip-protocol

[31] EU AI Act, Article 50. Compliance deadline August 2, 2026.

[32] Kamvar, S., Schlosser, M., Garcia-Molina, H. "The EigenTrust Algorithm for Reputation Management in P2P Networks." 2003. https://nlp.stanford.edu/pubs/eigentrust.pdf

[33] Huynh, T.D., Jennings, N.R., Shadbolt, N.R. "FIRE: An Integrated Trust and Reputation Model for Open Multi-Agent Systems." AAMAS/Springer, 2006.

[34] Pinyol, I., Sabater-Mir, J. "Computational Trust and Reputation Models." Artificial Intelligence Review, 2013.

[35] Marsh, S.P. "Formalising Trust as a Computational Concept." University of Stirling, 1994.

[36] "TRiSM for Agentic AI." arXiv:2506.04133, 2025.

[37] W3C. "Verifiable Credentials Data Model v2.0." W3C Recommendation, May 2025. https://www.w3.org/TR/vc-data-model-2.0/

[38] Ding, Y., et al. "Decentralized Multi-Agent System with Trust-Aware Communication." Best Paper, IEEE ISPA 2025. arXiv:2512.02410.

[39] FTC. "Final Rule Banning Fake Reviews and Testimonials." August 2024. https://www.ftc.gov/news-events/news/press-releases/2024/08/

[40] Brin, S., Page, L. "The Anatomy of a Large-Scale Hypertextual Web Search Engine." Stanford, 1998.

[41] Gyongyi, Z., Garcia-Molina, H., Pedersen, J. "Combating Web Spam with TrustRank." VLDB, 2004.

---

## Appendix A: Notation Summary

| Symbol | Meaning |
|--------|---------|
| `W(a)` | Rating weight of agent `a` |
| `GovWeight(a)` | Governance weight of agent `a` (= `W(a)`) |
| `Score_d(a)` | Weighted aggregate score of agent `a` on dimension `d` |
| `R_d(a,b)` | Rating agent `a` gives to agent `b` on dimension `d` |
| `RRC(a,b)` | Rating reciprocity coefficient between agents `a` and `b` |
| `σ(a)` | Standard deviation of all ratings given by agent `a` |
| `C_A` | Commitment hash from agent A in bilateral blind protocol |
| `Δ` | Collusion detection threshold (default 30) |

## Appendix B: Cross-Standard Architecture

```
Layer 4: GOVERNANCE
  IEEE CertifAIEd  ──→  System-level trustworthiness VC
  ARP Governance   ──→  Protocol parameter evolution via weighted voting

Layer 3: REPUTATION DATA
  ERC-8004 Reputation Registry  ──→  On-chain feedback index (tag-based)
  W3C Verifiable Credentials    ──→  Individual + summary rating VCs
  CoC Chain                     ──→  Tamper-evident evidence trail

Layer 2: COMMUNICATION
  A2A Protocol      ──→  AgentExtension + Task metadata for rating exchange
  MCP               ──→  experimental capability + custom rating tools
  OpenClaw/ClawHub  ──→  Skill-level trust metadata in frontmatter

Layer 1: IDENTITY
  W3C DIDs                      ──→  Agent identity (did:web primary, did:ethr bridge)
  ERC-8004 Identity Registry    ──→  On-chain registration (uint256 agentId)
  A2A AgentCard                 ──→  Discoverable metadata at .well-known
```

## Appendix C: License

Copyright 2026 AB Support LLC

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
