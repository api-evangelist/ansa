---
name: Accept a wallet payment
description: Initialize a client payment session and spend a customer's stored balance.
api: openapi/ansa-openapi-original.yml
operations: [initialize-payment-session, use-balance, list-transactions]
---

# Accept a wallet payment

Spend a customer's Ansa wallet balance at checkout.

## Auth & conventions
- Server-side calls use the merchant secret key in `Authorization`. For client/mobile checkout, mint a short-lived **client secret** via a payment session and use it on the client.
- Idempotency-protect the spend with a unique `Ansa-Idempotency-Key` header.

## Steps
1. **initialize-payment-session** — `POST /v1/initialize-payment-session` with the merchant secret key to create a client secret (limited TTL) safe to hand to web/mobile frontends and Ansa SDKs. The session also manages the idempotency key for SDK calls.
2. **use-balance** — `POST /v1/customers/{customerId}/use-balance` with `amount` and `currency` to draw down stored value for the purchase. Handle `insufficient_funds` (inspect the `deficit` object) by falling back to a payment method or partial capture.
3. **list-transactions** — `GET /v1/customers/{customerId}/transactions` (cursor + limit paging; filter by `transactionType`/`label`) to confirm and reconcile the resulting transaction.

## Error handling
- `402 Payment Failed` on the spend: inspect `error.type` / `error.code`; for card declines show the friendly `declineCode` message (errors/ansa-decline-codes.yml). `409` = idempotency conflict.
