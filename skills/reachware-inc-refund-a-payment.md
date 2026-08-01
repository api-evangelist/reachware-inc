---
name: Refund a Reach Pay payment
description: Look up a payment then issue a full or partial refund.
api: openapi/reachware-inc-reachpay-openapi.yml
operations: [getPaymentDetails, refundPayment]
---

# Refund a Reach Pay payment

## Auth
Send `Authorization: Bearer {reachToken}`. Base URL `https://api.reachware.com`.

## Steps
1. (Optional) Call `getPaymentDetails` (GET `/pay/PaymentDetails`) with the
   `client_id` header and `gateway_payment_id` query param to confirm the
   transaction and its captured `amount`.
2. Call `refundPayment` (POST `/pay/Refund`) with `client_id`, `amount` (the
   portion to refund — supports partial refunds), and `gateway_payment_id`.
3. Read the response `transaction_id`, `status`, `amount`, `fee`, `currency`,
   and `refunded`.

## Rules
- A payment that is already refunded cannot be refunded again — a 404 with
  `type: invalid_request_error` ("refunded payment can't be refunded") is
  returned. A missing record returns 404 `type: record_not_found`.
- `amount` is the refund portion, not necessarily the full original amount.
