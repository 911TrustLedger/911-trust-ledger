# MVP Scope

## What the MVP is

The 911TL MVP is a self-contained, locally runnable demonstration 
of the core protocol. It proves that the architecture is real and 
buildable — not a whitepaper, not a pitch deck.

**The MVP answers one question:**
Can a 911 event be cryptographically sealed, immutably recorded, 
and independently verified on a governance-controlled ledger?

The answer has to be yes before anything else matters.

---

## What it includes

**A single local Hyperledger Fabric node**
One peer, one orderer, one channel. Minimal operational 
complexity. Production-credible architecture.

**A simulated i3 data source**
Synthetic NENA i3-standard event data standing in for a live 
PSAP feed. The adapter logic is real — the data source is not.

**Four proof types**
The MVP implements the four core proof types that represent 
a 911 event from ingress through routing:

- `call.ingress` — the moment a call enters the ESInet
- `caller.identity` — verified caller identity assertion
- `location.asserted` — location data as provided to the PSAP
- `routing.decision` — the dispatch routing decision made

Each proof is cryptographically signed, hashed for PII 
protection, and committed to the ledger as an immutable record.

**A proof constructor and submission queue**
The component that takes raw i3 event data, constructs a 
signed proof, and submits it to the Fabric chaincode.

**A basic query interface**
The ability to retrieve and verify a committed proof by 
event ID. Proves the chain of custody is readable and 
independently verifiable.

---

## What it does not include

The MVP is deliberately minimal. The following are explicitly 
out of scope:

- Multi-node network topology (Phase 2)
- Live PSAP data connection (Phase 2)
- Jurisdictional sharding via Fabric channels (Phase 2)
- Identity provider integration (Phase 2)
- Production governance tooling (Phase 2)
- UI or dashboard (Phase 2)
- EMS or hospital data integration — the full event arc 
  (Phase 3)

---

## Definition of done

The MVP is complete when a developer can:

1. Clone the repository and run the local Fabric node
2. Feed simulated i3 event data through the adapter
3. Watch four proof types get constructed, signed, and 
   committed to the ledger
4. Query a committed proof by event ID and verify its 
   cryptographic integrity
5. Demonstrate that no committed record can be altered 
   without detection

That's it. Nothing more. The MVP is a proof of concept, 
not a product.

---

## Why this scope

The MVP is designed to be demo-ready for a specific audience: 
state 911 directors, federal program officers, and potential 
technical co-founders evaluating whether 911TL is real.

It does not need to handle production data volumes. It does 
not need to run in the cloud. It does not need a UI.

It needs to answer the one question that matters: 
**can this be built?**

Yes. This is how.

---

## Technical prerequisites

Before MVP development can begin, the following must be 
resolved:

- [ ] Hyperledger Fabric version confirmed (2.5 LTS recommended)
- [ ] Go development environment established
- [ ] ECDSA P-256 / SHA-256 cryptographic library selected
- [ ] Four MVP proof type schemas formally defined
- [ ] i3 simulated data set created
- [ ] Technical co-founder / lead engineer identified

---

*This scope is intentionally conservative. The goal is a 
working demonstration in the hands of a credible audience 
before NENA Annual (June 29 - July 1, 2026).*
