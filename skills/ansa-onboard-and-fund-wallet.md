---
name: Onboard a customer and fund their wallet
description: Create an Ansa customer, attach a payment method, and load stored value onto their wallet.
api: openapi/ansa-openapi-original.yml
operations: [create-customer, create-payment-method, add-balance, fund-customer-wallet, get-customer]
---

# Onboard a customer and fund their wallet

Use the Ansa stored-value API to onboard a wallet holder and load funds.

## Auth & conventions
- Send your merchant secret key in the `Authorization` header (no `Bearer` prefix). Use `ansa_sk_sandbox_...` against `https://api-sandbox.getansa.com` first.
- Put a unique `Ansa-Idempotency-Key` header on every state-changing POST (create, add-balance, fund) so retries never double-charge. A conflicting reuse returns `409`.
- Bodies are JSON; timestamps are RFC 3339; HTTPS required.

## Steps
1. **create-customer** — `POST /v1/customers` with `billingDetails` (firstName, lastName) and an `email` and/or `phone`. Duplicate email/phone returns `409`. Capture the returned `customerId`.
2. **create-payment-method** — `POST /v1/customers/{customerId}/payment-methods` from a token (or use create-payment-method-with-card-data). Capture the `paymentMethodId`.
3. **add-balance** — `POST /v1/customers/{customerId}/add-balance` with `amount`, `currency`, and `payment_method_id` to load stored value. On insufficient funds you receive an `insufficient_funds` error with a `deficit` object; on card decline inspect `error.declineCode` (see errors/ansa-decline-codes.yml) and show the user-friendly message — never the raw code.
4. **fund-customer-wallet** — `POST /v1/customers/{customerId}/fund-wallet` as the alternate wallet-funding entry point when funding outside a direct balance add.
5. **get-customer** — `GET /v1/customers/{customerId}` to confirm the resulting balance.

## Error handling
- `401` missing/invalid key, `403` wrong key scope (merchant vs client), `402` payment failed, `429` back off exponentially. See errors/ansa-problem-types.yml.
