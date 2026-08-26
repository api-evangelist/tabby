---
name: tabby-refund-and-cancel
description: >-
  Reverse a Tabby payment correctly — full or partial refund of a captured payment, or cancellation
  of an authorization that was never captured. Use when handling returns, cancellations, or an order
  that cannot be fulfilled, and you need to know which operation applies and how long the window is.
api: Tabby Payments API
version: '1.0.0'
generated: '2026-08-26'
method: generated
source: >-
  openapi/_original/tabby-api-openapi.yml (operationIds verified),
  https://docs.tabby.ai/pay-in-4-custom-integration/payment-processing#payment-refund,
  https://docs.tabby.ai/pay-in-4-custom-integration/payment-statuses
operations:
  - getPayment
  - postPaymentRefund
  - closePayment
  - putPayment
---

# Tabby: refunds and cancellations

## Pick the right operation

| Situation | Payment status | Operation | Effect |
|---|---|---|---|
| Order cancelled before you captured | `AUTHORIZED`, no captures | `closePayment` | Payment cancelled, buyer made whole, nothing settles to you. Dashboard shows `CANCELLED`. |
| Part of the order will not ship, after a partial capture | `AUTHORIZED`, some captures | `closePayment` | Cancels and refunds the uncaptured remainder. Dashboard shows `CAPTURED`. |
| Money already captured, buyer returns goods | `CLOSED` | `postPaymentRefund` | Returns funds to the buyer. Dashboard shows `REFUNDED` or `PARTIALLY REFUNDED`. |
| Only the merchant's order number is wrong | `AUTHORIZED` or `CLOSED` | `putPayment` | Updates `order.reference_id`. No money moves. |

Always read the payment first with `getPayment` (`GET /api/v2/payments/{id}`). Acting on a stale
status is the most common cause of a `400` here.

## Refund — `postPaymentRefund`

`POST /api/v2/payments/{id}/refunds`

The request shape is the same for full and partial: the payment id and an amount.

Constraints, all documented:

- The payment must be **`CLOSED`**. A payment with no captures, or one still `AUTHORIZED`, returns
  `400` — `can not refund payment that has no captures and not closed`.
- The refund total across all refunds cannot exceed the **captured** amount.
- Refunds are processed in the **same currency** as the capture.
- Refunds must be initiated **within 180 days of payment creation**.
- A full refund can be performed once. Partial refunds may be repeated up to the captured total.
- The payment stays `CLOSED` throughout.

Pass a unique `reference_id`. It is the idempotency key: retrying with the same `reference_id` will
not issue a second refund. Set it before the first attempt, not after a failure.

**A processed refund cannot be cancelled.** This is the one irreversible money-moving action in the
API. An agent must confirm with a human before calling it.

### When a refund does not return 200

Check, in this order:

1. Is the payment status `CLOSED`?
2. Is the refund amount higher than the captured amount?
3. Has the payment already been fully refunded?
4. Does the amount format match the currency's decimals (2 for AED/SAR, 3 for KWD, sent as a string)?

A `409` with `errorType: conflict` and `payment is disputed` means the payment is under dispute —
resolve it through the Disputes API instead. See `tabby-dispute-response`.

## Cancel — `closePayment`

`POST /api/v2/payments/{id}/close`

Closing is how you cancel. There is no separate void or cancel operation.

- Close with no captures cancels the payment entirely.
- Close after a partial capture releases the remainder.
- `already closed` (`400`, `bad_data`) means someone already did it — re-read the payment rather
  than treating this as a failure.

The window is practical rather than contractual: Tabby leaves a non-captured `AUTHORIZED` payment
untouched for **21 days**, after which it may capture the remaining amount in full on its side,
because a missing capture usually means the merchant hit a technical problem. Close well before
that.

## Finding what needs attention

`getPayments` (`GET /api/v2/payments`) lists successful payments (`AUTHORIZED` or `CLOSED`), most
recent first, with `offset`/`limit` pagination. In Merchant Dashboard the same set is reachable by
filtering orders by the `NEW` status, or by CSV export.

Captures and refunds have no endpoints of their own — they are arrays embedded in the payment. To
reconcile, re-read the payment and diff `captures[]` and `refunds[]`.

## Reversibility summary

| Action | Reversible by | Window |
|---|---|---|
| Authorization | `closePayment` | Any time before capture; Tabby may auto-capture after 21 days |
| Capture | `postPaymentRefund` | Within 180 days of payment creation, payment must be `CLOSED` |
| Refund | nothing | Irreversible |
| `putPayment` reference change | `putPayment` again | No stated deadline |
