---
name: Refund a transaction
description: Refund an Ansa transaction back to wallet balance or to the original payment method.
api: openapi/ansa-openapi-original.yml
operations: [get_v1-transactions-transactionid-refunds, refund-to-balance, refund-to-payment-method, get-refund]
---

# Refund a transaction

Return funds for a prior Ansa transaction.

## Auth & conventions
- Use the merchant secret key in `Authorization`. Add a unique `Ansa-Idempotency-Key` so a retried refund is not applied twice.

## Steps
1. **get_v1-transactions-transactionid-refunds** — `GET /v1/transactions/{transactionId}/refunds` to see what has already been refunded and the remaining refundable amount.
2. Choose the destination:
   - **refund-to-balance** — `POST /v1/refunds/balance` to credit the customer's wallet balance (instant, no PSP round-trip).
   - **refund-to-payment-method** — `POST /v1/refunds/payment-method` to return funds to the original card/payment method.
3. **get-refund** — `GET /v1/refunds/{refundId}` to poll/confirm the refund status.

## Error handling
- Over-refund or bad amount surfaces as `invalid_amount` / `INVALID_REFUND`; `409` on idempotency-key conflict. See errors/ansa-problem-types.yml and errors/ansa-decline-codes.yml.
