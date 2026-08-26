---
name: tabby-webhook-intake
description: >-
  Register and operate a Tabby webhook endpoint so payment status changes reach your system even
  when the buyer never returns to your site. Use when building the event side of a Tabby
  integration, or when payments are silently stuck in AUTHORIZED because nothing is listening.
api: Tabby Webhooks API
version: '1.0.0'
generated: '2026-08-26'
method: generated
source: >-
  openapi/_original/tabby-api-openapi.yml (operationIds verified),
  https://docs.tabby.ai/pay-in-4-custom-integration/webhooks,
  https://docs.tabby.ai/pay-in-4-custom-integration/dispute-webhooks
operations:
  - postWebhook
  - getWebhooks
  - getWebhook
  - putWebhook
  - deleteWebhook
---

# Tabby: webhook intake

## Register — `postWebhook`

`POST /api/v1/webhooks`

Note the version: webhooks are on **`/api/v1`**, while checkout and payments are on `/api/v2`.

- One registration per `merchant_code` + secret key pair, **maximum 4 endpoints** per pair.
- The environment is decided by the key you register with — an `sk_` key subscribes production
  payments, an `sk_test_` key subscribes test payments. There is no environment field.
- You may supply an optional auth header at registration. Tabby echoes it on every delivery so you
  can verify the request. There is no HMAC signature and no signing secret, so this header plus the
  source-IP allowlist is the whole authenticity story.

Manage the registrations with `getWebhooks`, `getWebhook`, `putWebhook` and `deleteWebhook` on
`/api/v1/webhooks` and `/api/v1/webhooks/{id}`.

## Receive

Deliveries are `POST` requests with a JSON body that **is the payment object** — its top-level `id`
is the payment id. There is no separate event id.

Fields: `id`, `created_at`, `expires_at`, `closed_at`, `status`, `is_test`, `is_expired`, `amount`,
`currency`, `order.reference_id`, `captures[]`, `refunds[]`, `meta`, `token`.

### The events

| Event | Payload `status` | What changed | Your action |
|---|---|---|---|
| Authorize | `authorized` | `status` set | Process the order if not already, then capture |
| Capture | `authorized` | entry appended to `captures[]` | none |
| Close | `closed` | `status` set, `closed_at` updated | none |
| Reject | `rejected` | `status` set | Cancel the order in your OMS |
| Expire (opt-in) | `expired` | `status`, `expired_at`, `is_expired` | Cancel the order |
| Refund | `closed` | entry appended to `refunds[]` | none |
| Update | unchanged | `order.reference_id` updated | none |

The Expire event is **off by default** — ask the Tabby team to enable it for your store.

A normal successful order produces three deliveries: `authorized`, `authorized` (with the capture
appended), then `closed`.

## Operate it correctly

1. **Answer `200` immediately**, then process asynchronously. Anything else — or no answer within
   **60 seconds** — counts as a delivery error.
2. **Retries**: Tabby resends up to **4 more times** at exponentially increasing intervals between
   1 and 4 minutes. Retries do not block other events.
3. **Ordering is not guaranteed.** A capture event can arrive before its authorization. Use a state
   machine; never assume sequence.
4. **Deduplicate.** The same event may be delivered twice. There is no event id, so key on
   (payment id, status, length of `captures[]`/`refunds[]`).
5. **Filter.** You receive every payment event for the account, not only the ones you use.
6. **Allowlist the source IPs** at your edge:
   `34.166.36.90`, `34.166.35.211`, `34.166.34.222`, `34.166.37.207`, `34.93.76.191`,
   `34.166.128.182`, `34.166.170.3`, `34.166.249.7`.
7. **Case.** Webhook statuses are lowercase (`authorized`); `getPayment` returns uppercase
   (`AUTHORIZED`). Compare case-insensitively.

## Dispute webhooks are a separate, unmanaged channel

Dispute notifications use the same endpoints, auth header, retry policy and source IPs — but they
**cannot be registered through the API**. Ask the Tabby Integrations Team to enable them for your
`merchant_code` and give them your URL. They exist for live payments only; disputes have no test
mode.

Payload: `status`, `dispute_id`, `payment_id`, `amount`, `currency`, `created_at`. Deduplicate on
`dispute_id` + `status`. Statuses: `pending`, `arbitration`, `evidence_merchant`, `approved`,
`declined`, `cancelled`. Fetch the detail with `getDispute`.

## Polling as a safety net

Webhooks are the primary signal, but a cron job calling `getPayment` every couple of minutes until
the payment reaches `AUTHORIZED`, `REJECTED` or `EXPIRED` costs little and catches a missed
delivery. Budget it against the published limit of 100 requests per second per live key.
