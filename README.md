# 911 Trust Ledger (911TL)

A cryptographically sealed, governance-controlled ledger protocol 
for verifiable 911 event records — the trusted data layer for AI 
in public safety.

---

## The Problem

Without a trusted data layer, public safety data is fragmented 
across thousands of jurisdictions, vendors, and systems. No single 
actor can verify the chain of custody for a 911 event from call 
ingress through resolution.

As AI is deployed at scale in 911, this fragmentation becomes 
critical. There is no verifiable way to understand the why behind 
the decisions made by AI systems operating on unverified data. 
Vendors control the data. Vendors grade their own homework.

This isn't a technology problem. It's an infrastructure problem.

## The Solution

911TL is an open, permissioned ledger protocol that creates a 
cryptographically sealed, tamper-resistant record of every 911 
event — from the moment a call originates through final incident 
resolution.

No vendor controls it. No single jurisdiction owns it. Every 
record is verifiable by any authorized party.

911TL is not built for humans. It's built for AI — providing the 
evidentiary foundation that makes AI decisions in public safety 
auditable, accountable, and trustworthy.

## Why It Matters Now

The AI procurement wave is hitting PSAPs today. Vendors are 
pitching AI-enabled dispatch, predictive routing, and call 
analytics to 911 centers across the country — without any 
independent framework for verifying whether the AI is performing 
as claimed.

911TL is the missing infrastructure layer. And without it, the 
architecture of AI in public safety gets set by default — by the 
vendors selling it.

## Technical Architecture

911TL is built on Hyperledger Fabric — a permissioned, 
governance-controlled distributed ledger framework. Core design 
decisions:

- **Ledger framework:** Hyperledger Fabric 2.5 LTS
- **Primary language:** Go
- **Cryptographic stack:** ECDSA P-256 / SHA-256
- **Trust anchor:** NENA PKI / Public Certificate Authority
- **Data source:** NENA i3 standard (NG911 event data)
- **Governance:** 911 Trust Ledger Foundation (nonprofit)
- **Jurisdictional isolation:** Channel-based sharding

The MVP is defined as a self-contained demonstration using 
simulated i3 data on a single local Fabric node — production- 
credible architecture, minimal operational complexity.

We are open to technical challenge on any of these decisions. 
The right architecture matters more than the current architecture.

## Current Status

Early stage. The protocol concept, governance model, and technical 
architecture are defined. The MVP is not yet built.

This is an honest accounting of where we are — because the right 
technical co-founder deserves to know exactly what they're walking 
into.

## What We're Looking For

We are looking for a technical co-founder — not just a contributor.

Someone who can own the full technical vision for 911TL: 
architecture, build, and external credibility. Someone with 
experience in distributed systems, permissioned ledger networks, 
or web3 infrastructure. Someone who may look at our current 
technical decisions and see a more elegant solution.

911 domain expertise is not required. Mission alignment is.

This is a pre-revenue, equity-based opportunity. We are not 
looking for someone who needs a salary. We are looking for someone 
who needs a mission.

If that's you — or if you know someone it might be — we want 
to hear from you.

## About the Foundation

The 911 Trust Ledger Foundation is a nonprofit organization 
stewarding the 911TL open standard. The Foundation is governed 
independently of any vendor or commercial interest.

Jon Whirledge, Founder & Executive Director, brings 20+ years 
of senior executive experience in the 911/NG911 ecosystem.

## Get In Touch

jon@911trustledger.org

Read more: [911trustledger.org](https://911trustledger.org)
