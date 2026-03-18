# Architecture

## Overview

911TL is a permissioned, governance-controlled distributed ledger
protocol. It creates a cryptographically sealed, tamper-resistant
record of 911 events — from call ingress through incident
resolution — that no single vendor controls and no single
jurisdiction can manipulate.

This document describes the core architectural decisions, the
rationale behind each, and the open questions that remain.

---

## Core design principles

**Permissioned, not public.**
Every participant — PSAP, ESInet operator, node operator — is
credentialed and known. This is not a public blockchain. It is a
governed network of accountable institutions.

**Governance-controlled, not vendor-controlled.**
The 911 Trust Ledger Foundation governs the protocol, the node
registry, the proof type definitions, and the validator set.
No commercial entity controls the standard.

**Append-only.**
Committed records cannot be modified or deleted at any layer.
Immutability is not a policy — it is an architectural guarantee.

**Built for AI, not humans.**
The ledger is not a reporting tool. It is the evidentiary
infrastructure that makes AI decisions in public safety
auditable, accountable, and trustworthy.

---

## Technical decisions

### Ledger framework — Hyperledger Fabric 2.5 LTS

**Decision:** Hyperledger Fabric is the ledger framework for
911TL.

**Rationale:**
- Fabric's Membership Service Provider (MSP) maps directly to
  911TL's trust anchor interface — NENA PKI credentials
  integrate as the MSP certificate authority
- Fabric channels provide the logical isolation primitive for
  jurisdictional sharding in production
- Fabric's Raft-based ordering service provides the
  Proof-of-Authority-style consensus model required by
  911TL's governance-designated validator architecture
- Fabric chaincode (smart contracts) is the implementation
  vehicle for proof ingestion, validation, and immutable
  commit logic
- Apache 2.0 license is compatible with 911TL's open source
  strategy
- Fabric 2.5 LTS provides production stability baseline

**Note to contributors:** We are open to technical challenge
on this decision. If you see a more elegant solution, we want
to hear it. The right architecture matters more than the
current architecture.

---

### Primary language — Go

**Decision:** Go is the primary development language for 911TL.

**Rationale:**
- Fabric chaincode is written in Go
- The Fabric Go SDK is the most mature and complete client
  integration interface
- Go's cryptographic library support is strong and well-audited
- Go's concurrency primitives are well-suited for the async
  proof submission queue
- Compiled binaries and container deployment model is clean
  and operationally simple

---

### Cryptographic stack — ECDSA P-256 / SHA-256

**Decision:** ECDSA P-256 for signing, SHA-256 for hashing.

**Rationale:**
- Fabric's native cryptographic stack — no third-party
  library dependency
- ECDSA P-256 is the NIST-recommended elliptic curve for
  federal and public safety applications
- SHA-256 is the standard for proof hashing and PII field
  protection
- X.509 certificate compatibility with NENA PKI / Public
  Certificate Authority

**Open item:** NENA PCA X.509 compatibility confirmation
required before trust anchor interface implementation begins.

---

### Consensus — Proof-of-Authority via Raft

**Decision:** Proof-of-Authority consensus model, implemented
via Fabric's Raft-based ordering service.

**Rationale:**
- The 911 ecosystem already has credentialing infrastructure.
  PSAPs are licensed. ESInet operators are contracted. State
  agencies are known entities. PoA maps directly to how trust
  already works in this industry.
- Raft provides strong consistency — all nodes agree on the
  same ordered log
- Fast and efficient — no computational waste
- Governance-designated validators means the Foundation
  controls who can commit blocks, preserving neutrality

---

### Trust anchor — NENA PKI / Public Certificate Authority

**Decision:** NENA PKI credentials serve as the MSP
certificate authority for the 911TL network.

**Rationale:**
- NENA PCA is the existing credentialing infrastructure for
  the NG911 ecosystem
- Integrating 911TL node identity with NENA PCA means
  participants don't need a separate credentialing process
- Anchors 911TL governance to the existing institutional
  authority the industry already trusts

---

### Data source — NENA i3 standard

**Decision:** NENA i3 is the data standard for 911 event
input to the protocol.

**Rationale:**
- i3 is the NG911 standard for IP-based emergency services
  data exchange
- Defines the structured event data format that 911TL's
  adapter layer consumes
- Ensures 911TL is compatible with any NG911-compliant PSAP
  or ESInet operator

**MVP note:** The MVP uses simulated i3 data. A live PSAP
connection is Phase 2.

---

### Network topology — tiered model (Phase 2)

**Decision:** Production network uses a tiered model —
private ESInet nodes sync upstream to public cloud anchor
nodes governed by the Foundation.

**Rationale:**
- Accommodates strict network perimeter requirements common
  in public safety environments
- Private ESInet nodes can operate behind agency firewalls
  without requiring inbound public connectivity
- Public cloud anchor nodes provide the governance layer and
  cross-jurisdictional verification surface
- Accepted tradeoff: introduces hierarchy that reduces pure
  decentralization — mitigated by Foundation governance of
  the anchor node set

**MVP note:** The MVP uses a single local node. Tiered
topology is a Phase 2 concern.

---

### Jurisdictional isolation — Fabric channels

**Decision:** Fabric channels provide logical data isolation
between jurisdictions in production.

**Rationale:**
- A PSAP in Alabama does not need visibility into event
  records from a PSAP in California
- Fabric channels allow multiple independent ledger instances
  on the same network infrastructure
- Channel membership is governed — only credentialed
  participants can join a channel
- Enables jurisdictional data sovereignty without requiring
  separate physical infrastructure

**MVP note:** Single channel. Multi-channel sharding is
Phase 2.

---

## Component map
```
i3 Data Source
     │
     ▼
Reference Adapter          ← converts i3 events to proof candidates
     │
     ▼
Proof Constructor          ← builds typed proof objects
     │
     ▼
Signing Module             ← ECDSA P-256 signature
     │
     ▼
Submission Queue           ← async, handles backpressure
     │
     ▼
Fabric Chaincode           ← validates, commits, enforces immutability
     │
     ▼
Ledger (append-only)       ← permanent record
     │
     ▼
Query Service              ← retrieve and verify by event ID
```

---

## Open decisions

The following architectural questions are unresolved and
represent meaningful contribution opportunities:

- [ ] NENA PCA X.509 compatibility — formal confirmation
      required before trust anchor interface build begins
- [ ] Retention policy floor — minimum retention period
      before jurisdictional policy applies
- [ ] Full replication vs. sharding — storage model for
      production multi-node topology
- [ ] Identity provider registration — scope and governance
      of IdP onboarding

---

## What this is not

911TL is not a surveillance infrastructure. The ledger records
cryptographic proofs of events — not the content of 911 calls.
PII fields are hashed at the proof construction layer. Raw
caller data never touches the ledger.

911TL is not a vendor product. It is an open standard governed
by a nonprofit foundation. Any qualified organization can run
a node. Any NG911-compliant system can connect an adapter.

---

*Architecture decisions are governed by the 911 Trust Ledger
Foundation. Proposed changes require Foundation review.
Technical challenges and alternative approaches are welcome —
open an issue or reach out at jon@911trustledger.org*
