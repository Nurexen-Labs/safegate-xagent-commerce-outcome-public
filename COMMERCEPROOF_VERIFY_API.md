# SafeGate CommerceProof Verify API

**Status:** Live controlled pilot · **Network:** Base Mainnet (8453), USDC · **Date:** 11 November 2026

## Overview

SafeGate CommerceProof Verify is a read-only API that checks the integrity of an **existing** signed CommerceProof. It is designed for AI agents, x402 integrators, merchants, developers, and independent reviewers. It does **not** initiate payments, use a signing wallet, or certify merchant fulfillment.

In a controlled Base Mainnet pilot, the verifier returned `ok=true` and `independently_verified=true` when rechecking real signed evidence, including the SafeGate Ed25519 signature, NSGoods EIP-191 signature, signed hash bindings, and specified Base USDC payment records using two independent RPC observations. These results apply to the evidence and trust anchors evaluated, **not** to arbitrary future proof submissions.

## Live endpoints

Base URL: **https://safegate-commerceproof-verify-pilot.vercel.app**

| Method | Endpoint | Purpose | Access |
| --- | --- | --- | --- |
| `GET` | `/api/health` | Service readiness and capability flags | Public |
| `GET` | `/.well-known/safegate-keys.json` | Published SafeGate signing key registry | Public |
| `POST` | `/api/commerceproof/verify` | Verify one CommerceProof | Approved pilot Bearer token |
| `POST` | `/v1/commerceproof/verify` | Alias of verification endpoint | Approved pilot Bearer token |

**There is no homepage at `/`.** A 404 at the root URL is normal for this API-only project. Open `/api/health` to see its live status.

## Authentication and request

The POST endpoint requires a private pilot access token, distributed **only to approved participants**. Content type: `application/json`. Maximum body size: **1 MiB**.

The `proof` value must be a full, structured, signed SafeGate CommerceProof JSON **object**, not a text string:

```json
{
  "proof": {
    "...": "your authorized existing signed CommerceProof object"
  }
}
```

This is a schema illustration, not a valid test proof. With a locally prepared `request.json` containing an authorized proof, and an environment variable named `PILOT_KEY` set privately:

```bash
curl -X POST "https://safegate-commerceproof-verify-pilot.vercel.app/api/commerceproof/verify" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $PILOT_KEY" \
  --data-binary @request.json
```

Do not place real tokens, original private proof files, confidential payment data, or customer evidence into a public repository or public issue. Submit another party's evidence only with permission.

## Response and assurance

A successful example response excerpt (the actual response contains more fields):

```json
{
  "schema": "SAFEGATE_COMMERCEPROOF_VERIFY_RESPONSE_V1",
  "ok": true,
  "independently_verified": true,
  "blockers": [],
  "verified_claims": {
    "safegate_ed25519_signature": "PASS",
    "signed_payload_hash_chain": "PASS",
    "nsgoods_eip191_signature": "VERIFIED_WITH_NSGOODS_OWN_MANIFEST",
    "merchant_payment_onchain": "VERIFIED_TWO_INDEPENDENT_RPC",
    "safegate_fee_onchain": "VERIFIED_TWO_INDEPENDENT_RPC"
  },
  "assurance": {
    "level": "OBSERVED",
    "independently_validated": false,
    "commerce_verified": false
  }
}
```

`independently_verified=true` indicates that the **checks within the stated evidence scope** passed with the stated trust anchors; it is **not** a general claim that the commerce transaction or fulfillment was independently validated. The actual response separates `merchant_payment`, `safegate_fee`, and `nsgoods_screening_fee`.

**Not independently verified by this API:** original merchant/order authorization; delivery or fulfillment; current sanctions clearance; the NSGoods screening-fee settlement. Observed payments must never be described as proof of delivered goods or services.

## Trust model

1. Obtain the public signing key/fingerprint from [SafeGate's key registry](https://safegate-commerceproof-verify-pilot.vercel.app/.well-known/safegate-keys.json) **outside** the submitted proof and pin the expected signer identity.
2. Validate Ed25519 signatures, canonical hashes, and request-ID bindings.
3. Validate the third-party EIP-191 evidence against NSGoods' **own published signer manifest**.
4. Re-check the referenced Base Mainnet USDC transfers and their exact chain, token, recipient, payer, and amount through independent RPC observations.
5. Preserve `assurance.level=OBSERVED`, `commerce_verified=false`, and all unverified claims in downstream systems.

The signing-key registry is issuer-published. It provides a trust anchor *outside the submitted proof*, but by itself does **not** establish an independent real-world certification of SafeGate's identity. Independent on-chain checks and the third-party manifest are separate evidence sources.

## Access controls and limits

- POST requires a pilot Bearer token. The authentication token is never public.
- The tested limit is **5 requests per minute per server instance**, not a durable per-user or monthly quota.
- Oversized payloads are rejected at 1 MiB; errors may include 401, 413, 429, or 503.
- The deployed verifier has **no signing capability** and does not transfer funds.
- This is an early controlled pilot. No public SLA, comprehensive audit certification, or guaranteed future availability is represented.

## Proposed pricing — not yet billable

| Plan | Planned price | Proposed usage |
| --- | --- | --- |
| Developer | **$29/month** | 1,000 verifications/month |
| Business | **$99/month** | 5,000 verifications/month |
| AI agent x402 | **0.05 USDC/call** | Pay per verification |

These prices are introductory proposals, **not a live payment offer**. Before paid access, the product still needs customer-specific credentials, an atomic cross-instance quota ledger, and proven billing/USDC settlement. **Do not send funds expecting automatic access.**

## More information

- [SafeGate Developers](https://api.safegatelabs.xyz/)
- [Live service health](https://safegate-commerceproof-verify-pilot.vercel.app/api/health)
- [Public SafeGate signing-key registry](https://safegate-commerceproof-verify-pilot.vercel.app/.well-known/safegate-keys.json)
- [SafeGate × NSGoods real Base pilot case study](./SAFEGATE_NSGOODS_LIVE_BASE_CASE_STUDY_2026-11-11.md)

**Controlled pilot access and partnerships:** nurexenlabs@gmail.com

*This public document is a usage guide, not the full deployed Verify API source code. It does not include original private CommerceProof JSON, confidential authorization data, environment variables, or credentials.*
