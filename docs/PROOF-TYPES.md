# Proof Types

## Overview

911TL records 911 events as cryptographic proofs — not raw data.
No personal information, no call audio, no sensitive content ever
touches the ledger. Only cryptographic fingerprints and essential
metadata are committed on-chain.

Proof types define the schema for each category of event that
911TL records. They are the vocabulary of the ledger.

Proof types are governed by the 911 Trust Ledger Foundation.
Changes to proof type definitions require Foundation review.

---

## MVP Proof Domain — Call Handling Equipment (CHE)

The MVP implements the CHE Evidence Graph — a complete,
NENA i3 and EIDO-aligned proof schema for call handling events.

Every 911 call generates a chain of CHE events from first ring
to final report. The CHE Evidence Graph records each state
change as a verifiable, immutable entry on the 911TL ledger.

The underlying call data never leaves the PSAP. Only a
cryptographic proof of each event — its timestamp, state,
and hash of associated artifacts — is committed on-chain.

---

## CHE Event Types

### CallInitiated
The moment a 911 call enters the system.

**Required fields:**
- event_id, event_type, prev_event_id
- call_id, sip_call_id, ts, seq
- origin_type (wireless | wireline | VoIP | NGCS | i3-text)
- service_urn (e.g., urn:service:sos.police)
- media_types, location_source
- issuer, issuer_cert_chain_id, signature, schema_version

**Artifacts:** che-log

---

### CallAnswered
The moment a call-taker accepts the call.

**Required fields:**
- event_id, event_type, prev_event_id
- call_id, ts, seq
- agent_id, agent_station_id
- call_state (InProgress)
- issuer, signature, schema_version

**Artifacts:** che-log, optional audio segment start

---

### CallUpdated *(repeatable)*
Location updates, language changes, accessibility changes,
operator notes. May occur multiple times per call.

**Required fields:**
- event_id, event_type, prev_event_id
- call_id, ts, seq
- call_state
- One or more of: location_hash, language,
  accessibility_mode, notes_hash

**Artifacts:** che-log, optional rtt-transcript

---

### RecordingCaptured
Proof that a call recording exists and is intact.
The recording never touches the ledger — only its hash.

**Required fields:**
- event_id, event_type, prev_event_id
- call_id, ts, seq
- artifacts[] with type=audio, hash, uri,
  retention_class, segment info
- issuer, signature, schema_version

---

### Hold / Resume
Proof that a call was placed on hold and resumed.

**Required fields:**
- event_id, event_type, prev_event_id
- call_id, ts, seq
- call_state, hold_reason

**Artifacts:** che-log

---

### Transfer
Proof of call transfer between positions or PSAPs.

**Required fields:**
- event_id, event_type, prev_event_id
- call_id, ts, seq
- call_state (Transferred)
- target_psap_id, transfer_type, route_reason

**Artifacts:** che-log

---

### CallReleased
The call is closed. Final state. Chain of custody complete
for the call handling portion of the event arc.

**Required fields:**
- event_id, event_type, prev_event_id
- call_id, ts, seq
- call_state (Released)
- duration_ms

**Artifacts:** che-log

---

### RetentionAction
Proof that a retention policy was applied — deletion,
archival, or case file designation. The ledger records
that the action occurred without storing the content.

---

### Error
Any CHE system error that affects call handling integrity.

**Required fields:**
- event_id, event_type, prev_event_id
- call_id, ts, seq
- error_code, error_detail_hash

---

## CHE Field Reference

### A) Identity & linking
| Field | Type | Description |
|-------|------|-------------|
| event_id | UUID | Unique event identifier |
| event_type | enum | See event types above |
| prev_event_id | UUID | Links to prior event — forms the chain |
| call_id | string | CHE logical CallID, stable across transfers |
| sip_call_id | string | Raw SIP Call-ID header |
| incident_id | string | Optional — populated once linked to CAD |
| ts | ISO-8601 | Event creation time, UTC with ms |
| seq | integer | Monotonic per call_id |

### B) Provenance
| Field | Type | Description |
|-------|------|-------------|
| issuer | string | System identity (e.g., CHE-PSAP01) |
| issuer_cert_chain_id | string | PKI chain reference |
| signature | string | Detached ECDSA P-256 signature |
| schema_version | string | e.g., che.v1.0 |
| software_version | string | CHE vendor and build |

### C) Parties (PII-minimized)
| Field | Type | Description |
|-------|------|-------------|
| agent_id | string | Operator ID or position |
| agent_station_id | string | Console or position |
| caller_id_hash | hash | SHA-256 of ANI — never raw |
| callback_number_hash | hash | SHA-256 — never raw |
| service_urn | string | e.g., urn:service:sos.fire |
| language | string | BCP-47 |
| accessibility_mode | enum | TTY, RTT, TCC, VRS, None |
| origin_type | enum | wireless, wireline, VoIP, NGCS, i3-text |

### D) Location (minimized)
| Field | Type | Description |
|-------|------|-------------|
| location_source | enum | HELD, PIDF-LO, LIS, Manual, None |
| location_hash | hash | Hash of PIDF-LO or civic/geo blob |
| dispatchable_location_hash | hash | Hash of derived location |
| confidence | 0-100 | Optional |

### E) Call state
| Field | Type | Description |
|-------|------|-------------|
| call_state | enum | Ringing, InProgress, OnHold, Transferred, Released, Abandoned, Error |
| duration_ms | integer | For terminal states |
| transfer_type | enum | blind, consult, supervised |

### F) Artifacts (off-chain references)
```
artifacts[]: {
  type: "che-log | audio | rtt-transcript | screen-recording",
  hash: "sha256:...",
  uri: "che://... | recorder://...",
  retention_class: "90d_audio | 2y_logs | casefile",
  size: <bytes>,
  segment: { index: 1, start_ts: "...", end_ts: "..." }
}
```

---

## MVP minimal field set

For the MVP, the following fields are required on every
CHE event. All others are recommended but optional.
```
event_id, event_type, prev_event_id, call_id, ts, seq,
issuer, signature, schema_version, call_state,
artifacts[], software_version
```

---

## Example call lifecycle
```
CallInitiated
     │
     ▼
CallAnswered
     │
     ▼
CallUpdated (location update)
     │
     ▼
RecordingCaptured
     │
     ▼
CallReleased
     │
     ▼
RetentionAction
```

Each arrow is a prev_event_id link.
The chain is cryptographically verifiable end to end.

---

## Phase 2 — CAD Evidence Graph

The CAD Evidence Graph extends 911TL coverage from call
handling through incident dispatch and resolution.

Where CHE records what happened on the call, CAD records
what happened in the field — unit dispatch, status changes,
incident updates, and closure.

The CAD Evidence Graph links to CHE records via call_id,
forming a continuous, verifiable chain of custody from
first ring through incident resolution.

CAD proof types are out of scope for the MVP.
Full CAD schema documentation will be published ahead
of Phase 2 development.

---

## Phase 3 — Full event arc

The complete 911TL vision extends the chain of custody
beyond the PSAP — through EMS transport, hospital handoff,
and outcome recording.

This is the data layer that makes outcome-based federal
funding of public safety auditable, accountable, and
real.

Phase 3 scope will be defined following MVP deployment
and Phase 2 validation.

---

*Proof type definitions are governed by the 911 Trust Ledger
Foundation. Proposed additions or changes should be opened
as issues in this repository or directed to
jon@911trustledger.org*
