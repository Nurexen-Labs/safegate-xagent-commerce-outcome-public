# SafeGate — Commerce Evidence & Verify API

**Live controlled pilot · Base Mainnet · 11 November 2026**

SafeGate provides portable evidence for AI-agent commerce and an API for checking signed CommerceProof records. The hosted verifier checks selected cryptographic and on-chain claims while explicitly distinguishing **observed payments** from **merchant authorization or fulfillment**.

## Start here

- **[CommerceProof Verify API — documentation](./COMMERCEPROOF_VERIFY_API.md)** — endpoint, pilot access, request format, trust model and planned pricing
- **[Live API health](https://safegate-commerceproof-verify-pilot.vercel.app/api/health)** — verifies deployment readiness, not a particular transaction
- **[Signing-key registry](https://safegate-commerceproof-verify-pilot.vercel.app/.well-known/safegate-keys.json)** — independently retrieve the public key/fingerprint outside the supplied proof
- **[SafeGate Developers](https://api.safegatelabs.xyz/)** — developer portal
- **[SafeGate × NSGoods live Base case study](./SAFEGATE_NSGOODS_LIVE_BASE_CASE_STUDY_2026-11-11.md)** — the earlier documented pilot

## What is checked?

Ed25519 signatures, signed hash chains, NSGoods EIP-191-signed evidence against the provider's published manifest, request bindings, and specified Base USDC transfers using two RPC observations.

**Assurance stays `OBSERVED`, with `commerce_verified=false`.** Cryptographic and transaction verification does not establish original merchant approval, order fulfillment, continuous sanctions clearance, or settlement of the third party's screening fee.

## Access and commercial status

The live verifier's POST endpoint is a **controlled, Bearer-token-protected pilot**. A durable customer quota and automatic x402/subscription billing are **not yet available**. Proposed introductory pricing: Developer $29/month (1,000 checks), Business $99/month (5,000 checks), agent verification 0.05 USDC/call. These are planned prices, **not active checkout offers**.

For pilot/partnership requests: **nurexenlabs@gmail.com**.

## Publication and security boundary

This repository contains existing public commerce-outcome code, a public case study, and verification documentation. It is **not** claimed to contain the complete deployed CommerceProof Verify API source. The deployed pilot is a separate Vercel project. Never commit pilot Bearer tokens, private signing keys, production environment files, original sensitive CommerceProof JSON, or customer data. The legacy `verification/public-key.txt` is **not** the active signer key for the new signed CommerceProof format; use the published signing-key registry and independently pin the intended key identity.

SafeGate / Nurexen Labs
