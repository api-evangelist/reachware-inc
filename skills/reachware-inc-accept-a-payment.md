---
name: Accept a payment with Reach Pay
description: Initiate a hosted checkout, redirect the payer, then confirm the result.
api: openapi/reachware-inc-reachpay-openapi.yml
operations: [requestPayment, getPaymentDetails]
---

# Accept a payment with Reach Pay

Reach Pay is a hosted-redirect payment API. You never handle card data — the
payer completes payment on the gateway's hosted page.

## Auth
Send `Authorization: Bearer {reachToken}` on every request. Tokens come from the
Reach Pay Partner Portal (sandbox tokens for testing; production after
verification). Base URL: `https://api.reachware.com` (sandbox).

## Steps
1. Call `requestPayment` (POST `/pay/RequestPayment`) with `client_id`,
   `order_id`, `payment_id`, `amount`, and `return_url`. Optional: `card_token`
   (to charge a saved card), `redirect_url`, `lang`, `card_save`, `location_id`.
2. Redirect the payer to the returned `payment_gateway_url`.
3. When the payer returns to your `return_url`, call `getPaymentDetails`
   (GET `/pay/PaymentDetails`) with the `client_id` header and the
   `gateway_payment_id` query param to read the final `status`, `amount`, and
   `card_mask`.

## Rules
- No idempotency key exists; a duplicate `requestPayment` returns 500
  "Payment Submitted before". Do not blindly retry — check `getPaymentDetails`.
- Errors are a flat `{error, message}` JSON envelope (see errors/). A 404 means
  "Config not found for id" — verify the `client_id` configuration.
