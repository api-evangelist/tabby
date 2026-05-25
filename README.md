# Tabby (tabby)

Tabby is the MENA region's largest buy-now-pay-later (BNPL) provider, founded in 2019 by Hosam Arab (ex-Namshi) and Daniil Barkalov, originally in Dubai and now headquartered in Riyadh ahead of a planned IPO. Tabby reached a $3.3B valuation in a February 2025 Series E ($160M co-led by Blue Pool Capital and Hassana Investment Company), making it the most valuable fintech in the Middle East, with 15M+ users, 40,000+ merchants, and $10B+ in annualized transaction volume across KSA, UAE, and Kuwait. The Tabby API powers split-purchase checkouts (Pay-in-4 interest-free, monthly plans up to 12 months), payment lifecycle management, webhooks, and dispute resolution, complemented by Tabby Card (Visa-enabled), Tabby Shop discovery, Tabby Care purchase protection, and the Tabby Plus loyalty programme.

**URL:** [Visit APIs.json](https://raw.githubusercontent.com/api-evangelist/tabby/refs/heads/main/apis.yml)

**Run:** [Capabilities Using Naftiko](https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=company-api-evangelist&utm_content=repo)

## Tags

 - BNPL, Buy Now Pay Later, Consumer Finance, E-commerce, Fintech, Installments, MENA, Payments, Saudi Arabia, UAE

## Timestamps

- **Created:** 2026-05-24
- **Modified:** 2026-05-24

## Regions

| Region | Production Host | Currency | Checkout Host |
|---|---|---|---|
| UAE / Kuwait | `https://api.tabby.ai` | AED / KWD | `https://checkout.tabby.ai` |
| Saudi Arabia | `https://api.tabby.sa` | SAR | `https://checkout.tabby.sa` |

All paths and payloads are identical across both hosts.

## APIs

### Tabby Checkout API
Create and retrieve hosted Tabby checkout sessions. Posts the buyer, order, and buyer-history payload to `/api/v2/checkout` and returns a pre-scoring result with a hosted `web_url` (or QR) the customer follows to complete Pay-in-4 or monthly installment authorization.

**Human URL:** [https://docs.tabby.ai/api-reference/checkout/create-a-session](https://docs.tabby.ai/api-reference/checkout/create-a-session)

- [Documentation](https://docs.tabby.ai/api-reference/overview)
- [OpenAPI](openapi/tabby-api-openapi.yml)
- [JSON Schema — Checkout Session](json-schema/tabby-checkout-session-schema.json)
- [JSON Schema — Order Item](json-schema/tabby-order-item-schema.json)
- [JSON-LD](json-ld/tabby-context.jsonld)
- [Naftiko Capability — Checkout Sessions](capabilities/checkout-sessions.yaml)

### Tabby Payments API
Lifecycle management for an authorized Tabby BNPL payment — retrieve, list, update merchant reference_id, capture (full or partial), refund (full or partial with line items), and close.

**Human URL:** [https://docs.tabby.ai/api-reference/payments/retrieve-a-payment](https://docs.tabby.ai/api-reference/payments/retrieve-a-payment)

- [Documentation — Capture](https://docs.tabby.ai/api-reference/payments/capture-a-payment)
- [Documentation — Refund](https://docs.tabby.ai/api-reference/payments/refund-a-payment)
- [Documentation — Close](https://docs.tabby.ai/api-reference/payments/close-a-payment)
- [OpenAPI](openapi/tabby-api-openapi.yml)
- [JSON Schema — Payment](json-schema/tabby-payment-schema.json)
- [JSON Schema — Capture](json-schema/tabby-capture-schema.json)
- [JSON Schema — Refund](json-schema/tabby-refund-schema.json)
- [Naftiko Capability — Payments](capabilities/payments-payments.yaml)
- [Naftiko Capability — Captures](capabilities/payments-captures.yaml)
- [Naftiko Capability — Refunds](capabilities/payments-refunds.yaml)

### Tabby Webhooks API
Register, list, retrieve, update, and remove HTTPS webhook endpoints scoped to a `merchant_code`. Tabby delivers `authorize`, `capture`, `close`, `reject`, `expire`, `refund`, and `update` events with optional arbitrary auth-header signing; failed deliveries retry with exponential backoff (1m timeout, 4 retries, 1-4 minute intervals).

**Human URL:** [https://docs.tabby.ai/api-reference/webhooks/register-a-webhook](https://docs.tabby.ai/api-reference/webhooks/register-a-webhook)

- [OpenAPI](openapi/tabby-api-openapi.yml)
- [JSON Schema — Webhook Event](json-schema/tabby-webhook-event-schema.json)
- [Naftiko Capability — Webhooks](capabilities/webhooks-webhooks.yaml)

### Tabby Disputes API
Programmatic dispute handling mirroring the Tabby Merchant Dashboard — list the 100 most recent disputes, retrieve a single dispute, provide evidence, and bulk-approve (max 20 per request, refunds customer) or challenge new disputes. Upload PNG/JPEG/PDF evidence up to 5MB. Live payments only.

**Human URL:** [https://docs.tabby.ai/api-reference/disputes](https://docs.tabby.ai/api-reference/disputes)

- [OpenAPI](openapi/tabby-api-openapi.yml)
- [JSON Schema — Dispute](json-schema/tabby-dispute-schema.json)
- [Naftiko Capability — Disputes](capabilities/disputes-disputes.yaml)

## SDKs and Plugins

Tabby maintains its open-source integrations under [github.com/tabby-ai](https://github.com/tabby-ai):

| Surface | Repository | Language | License |
|---|---|---|---|
| iOS | [tabby-ios-sdk](https://github.com/tabby-ai/tabby-ios-sdk) | Swift | — |
| Android | [tabby-android-sdk](https://github.com/tabby-ai/tabby-android-sdk) | Kotlin | — |
| Flutter | [tabby_flutter_inapp_sdk](https://github.com/tabby-ai/tabby_flutter_inapp_sdk) | Dart | MIT |
| React Native | [react-native-example](https://github.com/tabby-ai/react-native-example) | TypeScript | — |
| Magento 2 — Checkout | [m2-checkout](https://github.com/tabby-ai/m2-checkout) | PHP | — |
| Magento 2 — Payments | [m2-payments](https://github.com/tabby-ai/m2-payments) | PHP | — |
| Magento 2 — Marketplace Feed | [m2-feed](https://github.com/tabby-ai/m2-feed) | PHP | — |
| Magento 2 — Additional Merchant ID | [m2-sub](https://github.com/tabby-ai/m2-sub) | PHP | — |
| Odoo | [odoo](https://github.com/tabby-ai/odoo) | Python | — |
| Hijri calendar | [hijri-converter](https://github.com/tabby-ai/hijri-converter) | TypeScript | MIT |

Certified storefront plugins (docs-only): Shopify, WooCommerce, Salla, Zid, OpenCart, ExpandCart, Matjrah, Salesforce.

## Examples

- [Create checkout session](examples/tabby-create-checkout-session-example.json)
- [Capture payment](examples/tabby-capture-payment-example.json)
- [Refund payment](examples/tabby-refund-payment-example.json)
- [Register webhook](examples/tabby-register-webhook-example.json)
- [Webhook event payload](examples/tabby-webhook-event-example.json)

## Commercial and FinOps

- [Plans — API Commons 0.1](plans/tabby-plans-pricing.yml)
- [Rate Limits — API Commons 0.1](rate-limits/tabby-rate-limits.yml)
- [FinOps — FOCUS 1.3](finops/tabby-finops.yml)

## Governance

- [Vocabulary](vocabulary/tabby-vocabulary.yml)
- [Spectral Ruleset](rules/tabby-rules.yml)
- [JSON-LD Context](json-ld/tabby-context.jsonld)

## Common Resources

- [Tabby Developer Docs](https://docs.tabby.ai)
- [API Reference](https://docs.tabby.ai/api-reference/overview)
- [Quick Start](https://docs.tabby.ai/introduction/quick-start)
- [Upstream OpenAPI](https://docs.tabby.ai/openapi.yaml)
- [Testing Credentials](https://docs.tabby.ai/pay-in-4-custom-integration/testing-credentials)
- [Testing Checklist](https://docs.tabby.ai/pay-in-4-custom-integration/full-testing-checklist)
- [Webhooks Guide](https://docs.tabby.ai/pay-in-4-custom-integration/webhooks)
- [Payment Statuses](https://docs.tabby.ai/pay-in-4-custom-integration/payment-statuses)
- [POS Integration](https://docs.tabby.ai/offline-payment-methods/pos-integration)
- [Custom Payment Links](https://docs.tabby.ai/offline-payment-methods/custom-payment-links)
- [Brand Assets](https://docs.tabby.ai/marketing-resources/brand-assets)
- [Merchant Pricing FAQ](https://tabby.ai/en-AE/help-business/about-tabby/pricing)
- [Tabby Newsroom](https://tabby.ai/en-AE/newsroom)
- [Tabby on LinkedIn](https://www.linkedin.com/company/tabby/)
- [Tabby on X](https://twitter.com/tabby)
