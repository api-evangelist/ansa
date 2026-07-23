---
name: Run an incentive campaign
description: Create an Ansa incentive campaign, grant incentives to customers, and redeem promos and gift cards.
api: openapi/ansa-openapi-original.yml
operations: [create-incentive, post_v1fund-customer-balance-1, redeem-promo, redeem-gift-card]
---

# Run an incentive campaign

Drive retention with Ansa's incentive engine.

## Auth & conventions
- Merchant secret key in `Authorization`. Idempotency-protect grants and redemptions with `Ansa-Idempotency-Key`.
- Incentive funds come from a promotional account (see create-promotional-account / fund-promotional-account and data-model/ansa-data-model.yml).

## Steps
1. **create-incentive** — `POST /v1/merchants/{merchantId}/campaigns` to create an incentive campaign for the merchant.
2. **post_v1fund-customer-balance-1** (add-incentive) — `POST /v1/{customerId}/add-incentive` to grant incentive/promotional value to a specific customer's wallet.
3. **redeem-promo** — `POST /v1/customers/{customerId}/redeem` to redeem a promo code onto a customer's wallet.
4. **redeem-gift-card** — `POST /v1/customers/{customerId}/redeem-gift-card` to redeem a gift card into stored value.

## Error handling
- `invalid_parameter` / `missing_parameter` on malformed campaign or redemption bodies; `high_risk` if the redemption trips risk controls. See errors/ansa-problem-types.yml.
