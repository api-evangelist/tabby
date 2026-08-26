---
name: tabby-checkout-to-capture
description: >-
  Take a Tabby BNPL order from checkout session creation through buyer authorization to a captured,
  settled payment. Use when integrating Tabby Pay-in-4 or Pay Monthly into a custom checkout and you
  need the server-side half of the flow done correctly, including the cases where the buyer never
  comes back.
api: Tabby Checkout + Payments API
version: '1.0.0'
generated: '2026-08-26'
method: generated
source: >-
  openapi/_original/tabby-api-openapi.yml (operationIds verified),
  https://docs.tabby.ai/pay-in-4-custom-integration/checkout-flow,
  https://docs.tabby.ai/pay-in-4-custom-integration/payment-processing
operations:
  - postCheckoutSession
  - getPayment
  - postPaymentCapture
  - closePayment
---

# Tabby: checkout session to captured payment

## Before you start

- Base URL is regional: `https://api.tabby.ai` for UAE and Kuwait, `https://api.tabby.sa` for KSA.
  Paths and payloads are identical; pick by the merchant's operating country.
- Auth is `Authorization: Bearer <secret_key>`. The key prefix, not the host, selects the
  environment: `sk_test_` is test, `sk_` is live.
- Amounts are strings in the currency's minor-unit precision: 2 decimals for AED and SAR, 3 for KWD.
  Dates are ISO 8601 UTC. Strings cap at 255 characters. Encode UTF-8.

## Steps

### 1. Check eligibility, then create the session — `postCheckoutSession`

`POST /api/v2/checkout`

The same operation serves both purposes. Send a minimal payload (amount, currency, buyer email and
phone, merchant_code) early to decide whether to show Tabby at all; send the complete order when the
buyer clicks Place order.

Read `status` on the response:

- `created` — pre-scoring passed. `web_url` is present and `payment.id` is your durable handle.
  **Store `payment.id` before redirecting.**
- `rejected` — Tabby declined. `web_url` may be absent. Show the rejection copy and do not redirect.
  Do not retry; there is no reason code and a retry only burns Create Session rate budget.

There is **no idempotency key on this operation**. A retried call creates a second session and a
second payment. If the call times out, look up the buyer's existing session before calling again.

### 2. Send the buyer to `web_url`

Redirect, or open in a WebView. Your `merchant_urls.success`, `.cancel` and `.failure` receive the
buyer back with `payment_id` as a query parameter.

The session expires **20 minutes** after creation, and the payment moves to `EXPIRED` roughly
10 minutes after that.

### 3. Verify server-to-server — `getPayment`

`GET /api/v2/payments/{id}`

**Never trust the redirect alone.** Read `status`:

| Redirect landed on | Expected `status` | What you do |
|---|---|---|
| Success URL | `AUTHORIZED` | Process the order, then capture |
| Cancel URL | `EXPIRED` | Cancel the order in your OMS |
| Failure URL | `REJECTED` | Cancel the order in your OMS |
| — | `CREATED` | Checkout unfinished; keep checking until it changes or expires |

### 4. Catch the buyers who never came back

A payment can reach `AUTHORIZED` with no redirect at all — the buyer closed the tab or the WebView,
or the network dropped. Two ways to catch it:

- **Webhooks (recommended).** Register an endpoint once with `postWebhook` and act on the
  `authorized` event. See `tabby-webhook-intake`.
- **Polling (fallback).** Re-read the payment every couple of minutes until it reaches `AUTHORIZED`,
  `REJECTED` or `EXPIRED`. Stop at any of those — all three are terminal for this purpose.

Webhook payloads use **lowercase** statuses (`authorized`); `getPayment` returns **uppercase**
(`AUTHORIZED`). Compare case-insensitively.

### 5. Capture — `postPaymentCapture`

`POST /api/v2/payments/{id}/captures`

Only an `AUTHORIZED` payment can be captured; any other status returns `400` with
`errorType: bad_data` and a message like `could not capture not authorized payments`.

Capture the **full amount**. Tabby verifies the capture amount against the payment. A successful
capture returns `200` and the payment becomes `CLOSED`. Only captured payments are settled to you.

Pass your own unique key in `reference_id`. That field **is** the idempotency key: a retry with the
same `reference_id` will not create a second capture. Always set it before you retry anything.

Partial capture is supported — the payment stays `AUTHORIZED` until you capture the rest or close
it — but a full capture immediately after verification is the recommended flow.

Do not leave payments `AUTHORIZED`. Payout happens only after close, and after **21 days** Tabby may
capture the remaining amount itself on the assumption that the merchant hit a technical fault.

### 6. Close instead, when part of the order will not ship — `closePayment`

`POST /api/v2/payments/{id}/close`

- **After a partial capture** — close cancels and refunds everything uncaptured. Payment becomes
  `CLOSED` (Dashboard: `CAPTURED`).
- **With no capture at all** — close cancels the whole payment. Payment becomes `CLOSED`
  (Dashboard: `CANCELLED`). This is how you cancel an authorization.

## Error handling

Every error is `{ "status": "error", "errorType": "...", "error": "..." }`. Match on `errorType`
(`bad_data`, `not_authorized`, `no_permission`, `not_found`, `conflict`); `error` is an English
string for your logs and Tabby says explicitly it is not machine-parseable. Full catalogue in
`errors/tabby-problem-types.yml`.

On `429`, back off. There are no rate-limit headers to read — the live budget is 200 Create Session
per 10s and 100 requests per second for everything else, and you must track it yourself.

If you get anything other than `200` on a capture, **investigate before retrying** — and when you do
retry, reuse the same `reference_id`.

## Reversibility

- An authorization can be cancelled by closing without capture, any time before capture (and before
  Tabby's 21-day auto-capture).
- A capture can be reversed by a refund within **180 days of payment creation**, on a `CLOSED`
  payment, up to the captured amount.
- A processed refund **cannot** be cancelled. Treat it as terminal.
