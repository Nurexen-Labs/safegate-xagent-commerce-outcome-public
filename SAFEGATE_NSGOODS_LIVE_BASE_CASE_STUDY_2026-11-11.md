# SafeGate × NSGoods: Live Signed Commerce Evidence on Base

**Date:** 11 November 2026  
**Status:** Controlled paid integration test completed. External reviewer acceptance is pending.  
**Environment:** Base Mainnet (chain ID 8453), USDC  
**Scope:** Cryptographic evidence of observed payments and a signed third-party screening result; **not** independent verification of merchant fulfilment.

## Summary

This live pilot demonstrates how SafeGate can bind a real NSGoods EIP-191-signed screening verdict to an observed Base USDC merchant transfer and issue an Ed25519-signed CommerceProof. The resulting evidence is portable and can be cryptographically checked without treating an ordinary token transfer as proof of delivery or order fulfilment.

NSGoods and SafeGate remain distinct services. This note does not imply a formal endorsement by NSGoods or a third-party security audit.

## Recorded live results

| Stage | Result | Scope |
| --- | --- | --- |
| NSGoods paid screening | HTTP 200; signed result `clean`; EIP-191 verifier PASS | Historical address screening, not ongoing clearance |
| Base merchant transfer | 0.01 USDC; `PAYMENT_VERIFIED`; two-RPC local verification PASS | Payment observed, not original order authorization or fulfilment |
| SafeGate x402 proof fee | 0.07 USDC; `PAYMENT_VERIFIED` | Separate proof-service fee |
| Unified evidence response | `ok=true`; `COMMERCE_EVIDENCE_DELIVERED` | Product successfully delivered |
| SafeGate Ed25519 signed proof | Signature VALID | Cryptographic integrity of the signed statement and referenced payload |
| NSGoods raw signed verdict | 2,012 bytes; SHA-256 MATCH; request ID and recipient MATCH | Third-party result bound into signed evidence |
| Freshness at merchant transfer | 205.949 seconds | Within the 900-second integration window |

Local regression tests passed the three negative cases: incorrect merchant recipient, fabricated or tampered NSGoods signature, and declared amount inconsistent with the observed transfer. Local x402 tests also passed facilitator/challenge consistency, `PAYMENT-SIGNATURE` precedence, and canonical base-unit amount handling. These are **local tests**, not independent acceptance by an external reviewer.

## Integrity verification

The saved artifact uses `SAFEGATE_X402_SIGNED_COMMERCE_EVIDENCE_V1` and incorporates an Ed25519-signed `SAFEGATE_AGENT_COMMERCE_PROOF_V1` payload.

```text
Ed25519 signature on signing_statement
  -> matching payload_sha256
  -> canonical CommerceProof payload
  -> matching execution.response_hash
  -> canonical evidence_result
  -> third_party_evidence
  -> exact signed NSGoods response bytes
```

The signed statement's Ed25519 signature was independently checked against its included public key. The canonical payload hash, execution response hash, and embedded NSGoods raw-byte hash matched. The original NSGoods EIP-191 verdict was reported valid by the local verifier, with the same request ID and screened recipient as the proof.

**Original delivered CommerceProof JSON SHA-256:**

`05e0fbf8325bc81a36b0a08294e71888374fbc6e871c4aeb047168ceeadaed11`

**Embedded NSGoods raw response SHA-256:**

`24a407ad1b285d05cee2eb06a5595c36cff3f0a1e193547151b461ae0a02e951`

These fingerprints refer to the original artifacts delivered privately to the intended technical reviewer. The full signed records, account addresses, transaction hashes, payment authorizations, and sensitive operational files are **not** committed to this public repository.

## Explicit assurance boundaries

The actual result declares:

- `assurance.level = OBSERVED`
- `commerce_verified = false`
- `assurance.independently_validated = false`
- `original_merchant_order_binding = NOT_VERIFIED`
- `third_party_evidence.screening_fee_payment = NOT_INDEPENDENTLY_VERIFIED`
- `third_party_evidence.current_compliance_clearance = false`

Accordingly, this pilot does **not** demonstrate independent merchant delivery verification, continuous sanctions clearance, independent original order authorization, completion of a formal audit, or general production readiness. NSGoods' fee settlement was not independently verified by SafeGate; SafeGate validated the provider-signed screening record. The distinct `x402-agent-pay.com` payment rail was **not** tested as part of this pilot.

This document summarizes the saved SafeGate result and operator test logs; it does not claim a separate live blockchain recheck was performed in preparing this public note.

---

**SafeGate / Nurexen Labs**  
*Payment proves value moved. Evidence makes the observed result verifiable.*
