---
name: tabby-dispute-response
description: >-
  Work a Tabby dispute end to end — list open disputes, read one, upload evidence, and either
  approve (refund the customer) or challenge it. Use when automating chargeback-style handling for a
  Tabby merchant account, and note that disputes are live-only with no test mode.
api: Tabby Disputes API
version: '1.0.0'
generated: '2026-08-26'
method: generated
source: >-
  openapi/_original/tabby-api-openapi.yml (operationIds verified),
  https://docs.tabby.ai/pay-in-4-custom-integration/disputes,
  https://docs.tabby.ai/pay-in-4-custom-integration/dispute-webhooks
operations:
  - getDisputes
  - getDispute
  - postUploadAttachment
  - postDisputeProvideEvidence
  - postDisputesApprove
  - postDisputesChallenge
---

# Tabby: responding to disputes

## Before you start

Disputes operate **exclusively on live payments with live credentials**. There is no test mode and
no sandbox path — a `sk_test_` key will never find a dispute. Everything below moves real money.

The Disputes API mirrors what a human can do in Merchant Dashboard. It is on `/api/v1`.

## 1. Find what is open — `getDisputes`

`GET /api/v1/disputes`

Returns the **100 most recently created disputes**. That is a hard cap, not a page size: there is no
cursor and no pagination. If you have more than 100 open, the API cannot show you the rest — work
the list down or use Merchant Dashboard.

Each dispute carries `id`, `payment_id`, `amount`, `currency`, `status`, `reason`, `created_at`,
`expired_at` and `days_left`. **`days_left` is your clock** — it is the field that tells you how
long the response window has left.

## 2. Read one — `getDispute`

`GET /api/v1/disputes/{disputeId}`

Adds `history[]`, `items[]`, `comment` and `attachments`. Correlate to your order through
`payment_id` — call `getPayment` on it to pull the order reference and the capture/refund arrays.

## 3. Decide

| Decision | Operation | Effect |
|---|---|---|
| The customer is right | `postDisputesApprove` | The disputed amount is refunded to the customer |
| The customer is wrong | `postDisputesChallenge` | Tabby support reviews the case (arbitration) |
| Tabby asked for proof | `postDisputeProvideEvidence` | Submit text and/or attachments |

### Approve — `postDisputesApprove`

`POST /api/v1/disputes/approve`

Batched: a **maximum of 20 disputes per request**. Batch in chunks of 20.

This **refunds the customer** and no reversal is documented. Treat it as terminal and put a human
confirmation in front of it.

### Challenge — `postDisputesChallenge`

`POST /api/v1/disputes/challenge`

Also capped at **20 per request**. Only disputes with status **`new`** can be challenged — once a
dispute has advanced, the option is gone. Check `status` immediately before calling, not from a
cached list.

### Provide evidence — `postUploadAttachment` then `postDisputeProvideEvidence`

1. `POST /api/v1/disputes/attachments/upload` — upload each file. Constraints: **5 MB maximum**,
   and only `image/png`, `image/jpeg` or `application/pdf`.
2. `POST /api/v1/disputes/{disputeId}/provide-evidence` — submit the text and the uploaded
   attachments together.

This is the operation to use when the dispute status is `evidence_merchant` — Tabby support has
asked you for proof.

## 4. React to changes instead of polling

Dispute webhooks push every status change: `pending` (customer opened it), `arbitration` (you
challenged), `evidence_merchant` (proof requested), `approved`, `declined`, `cancelled`.

They cannot be registered through the API — ask the Tabby Integrations Team to enable them for your
`merchant_code`. See `tabby-webhook-intake`.

## Error handling

- `404` `dispute not found` — most often a test key against a live-only surface, or a stale id.
- `409` `conflict` — the dispute has moved on (for example you tried to challenge one that is no
  longer `new`). Re-read it.
- Attempting `postPaymentRefund` on a disputed payment returns `409` `payment is disputed`. Resolve
  it here, not through the Payments API.

Full catalogue: `errors/tabby-problem-types.yml`.
