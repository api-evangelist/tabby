# Tabby (tabby)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Tabby is the MENA region's largest buy-now-pay-later (BNPL) provider, founded in 2019 by Hosam Arab (ex-Namshi) and Daniil Barkalov, originally in Dubai and now headquartered in Riyadh ahead of a planned IPO. Tabby reached a $3.3B valuation in a February 2025 Series E ($160M co-led by Blue Pool Capital and Hassana Investment Company), making it the most valuable fintech in the Middle East, and reports 15M+ users, 40,000+ merchants, and $10B+ in annualized transaction volume across KSA, UAE, and Kuwait. The Tabby API powers split-purchase checkouts (Pay-in-4 interest-free, monthly plans up to 12 months), payment lifecycle management, webhooks, and dispute resolution, complemented by Tabby Card (Visa-enabled), Tabby Shop discovery, Tabby Care purchase protection, and the Tabby Plus loyalty programme. Public developer surface includes a versioned REST API across two regional hosts (api.tabby.ai for UAE/Kuwait, api.tabby.sa for KSA), an OpenAPI 3.1 specification, iOS / Android / Flutter / React Native SDKs, and certified Magento 2, Shopify, WooCommerce, Salla, Zid, OpenCart, ExpandCart, Matjrah, Salesforce, and Odoo plugins.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/tabby/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/tabby/refs/heads/main/apis.yml)

## Scope

- **Access:** 3rd-Party

## Tags

- BNPL
- Buy Now Pay Later
- Consumer Finance
- E-commerce
- Fintech
- Installments
- MENA
- Payments
- Saudi Arabia
- UAE

## APIs

### Tabby Checkout API

Create and retrieve Tabby Checkout sessions. Posting customer, order, and buyer-history data to /api/v2/checkout creates a Session plus Payment and returns a pre-scoring result with a hosted web_url (or QR code) that the buyer follows to complete Pay-in-4 or monthly-installment authorization. Supports AED, SAR, and KWD across two regional production hosts (api.tabby.ai for UAE/Kuwait, api.tabby.sa for KSA).

- **Human URL:** [https://docs.tabby.ai/api-reference/checkout/create-a-session](https://docs.tabby.ai/api-reference/checkout/create-a-session)

#### Tags

- BNPL
- Buy Now Pay Later
- Checkout
- Payments
- Sessions

#### Properties

- [Documentation](https://docs.tabby.ai/api-reference/checkout/create-a-session)
- [Documentation](https://docs.tabby.ai/api-reference/overview)
- [OpenAPI](openapi/tabby-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/tabby-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/tabby-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [JSON Schema](json-schema/tabby-checkout-session-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/tabby-order-item-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON-LD](json-ld/tabby-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)

### Tabby Payments API

Lifecycle management for an authorized Tabby BNPL payment. Retrieve a payment by id, list payments with pagination/status filters, update the merchant reference_id, capture authorized funds (full or partial), refund a closed payment (full or partial with line items), and close a payment when fulfilment is complete. All endpoints require a secret-key Bearer token.

- **Human URL:** [https://docs.tabby.ai/api-reference/payments/retrieve-a-payment](https://docs.tabby.ai/api-reference/payments/retrieve-a-payment)

#### Tags

- BNPL
- Buy Now Pay Later
- Captures
- Payments
- Refunds

#### Properties

- [Documentation](https://docs.tabby.ai/api-reference/payments/retrieve-a-payment)
- [Documentation](https://docs.tabby.ai/api-reference/payments/capture-a-payment)
- [Documentation](https://docs.tabby.ai/api-reference/payments/refund-a-payment)
- [Documentation](https://docs.tabby.ai/api-reference/payments/close-a-payment)
- [OpenAPI](openapi/tabby-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/tabby-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/tabby-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [JSON Schema](json-schema/tabby-payment-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/tabby-capture-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/tabby-refund-schema.json) — [JSON Schema](https://json-schema.org/specification)

### Tabby Webhooks API

Register, list, retrieve, update, and remove Tabby webhook endpoints scoped to a merchant_code. Tabby fires authorize, capture, close, reject, expire, refund, and update events as JSON POSTs to your HTTPS endpoint with optional arbitrary auth-header signing. Failed deliveries retry with exponential backoff (1m timeout, up to 4 retries at 1-4 minute intervals).

- **Human URL:** [https://docs.tabby.ai/api-reference/webhooks/register-a-webhook](https://docs.tabby.ai/api-reference/webhooks/register-a-webhook)

#### Tags

- BNPL
- Buy Now Pay Later
- Events
- Webhooks

#### Properties

- [Documentation](https://docs.tabby.ai/api-reference/webhooks/register-a-webhook)
- [OpenAPI](openapi/tabby-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/tabby-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/tabby-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [JSON Schema](json-schema/tabby-webhook-event-schema.json) — [JSON Schema](https://json-schema.org/specification)

### Tabby Disputes API

Programmatic dispute handling mirroring the Tabby Merchant Dashboard. List the 100 most recent disputes, retrieve a single dispute, provide evidence, and bulk-approve (up to 20 at a time, refunds the customer) or challenge new disputes for support review. Upload PNG/JPEG/PDF evidence attachments up to 5MB. Live payments only with secret-key Bearer auth.

- **Human URL:** [https://docs.tabby.ai/api-reference/disputes](https://docs.tabby.ai/api-reference/disputes)

#### Tags

- BNPL
- Buy Now Pay Later
- Disputes
- Chargebacks

#### Properties

- [Documentation](https://docs.tabby.ai/api-reference/disputes)
- [OpenAPI](openapi/tabby-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/tabby-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/tabby-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [JSON Schema](json-schema/tabby-dispute-schema.json) — [JSON Schema](https://json-schema.org/specification)

## Common Properties

- [Website](https://tabby.ai)
- [Website](https://tabby.ai/en-AE/business)
- [Portal](https://docs.tabby.ai)
- [Documentation](https://docs.tabby.ai/api-reference/overview)
- [Getting Started](https://docs.tabby.ai/introduction/quick-start)
- [OpenAPI](https://docs.tabby.ai/openapi.yaml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Documentation](https://docs.tabby.ai/pay-in-4-custom-integration/testing-credentials)
- [Documentation](https://docs.tabby.ai/pay-in-4-custom-integration/full-testing-checklist)
- [Documentation](https://docs.tabby.ai/pay-in-4-custom-integration/webhooks)
- [Documentation](https://docs.tabby.ai/pay-in-4-custom-integration/payment-statuses)
- [Github](https://github.com/tabby-ai)
- [SDK](https://github.com/tabby-ai/tabby-ios-sdk)
- [SDK](https://github.com/tabby-ai/tabby-android-sdk)
- [SDK](https://github.com/tabby-ai/tabby_flutter_inapp_sdk)
- [SDK](https://github.com/tabby-ai/react-native-example)
- [Plugin](https://github.com/tabby-ai/m2-checkout)
- [Plugin](https://github.com/tabby-ai/m2-payments)
- [Plugin](https://github.com/tabby-ai/m2-feed)
- [Plugin](https://github.com/tabby-ai/m2-sub)
- [Plugin](https://github.com/tabby-ai/odoo)
- [SDK](https://github.com/tabby-ai/hijri-converter)
- [Plugin](https://docs.tabby.ai/e-commerce-platforms/shopify/shopify-plugin-installation)
- [Plugin](https://docs.tabby.ai/e-commerce-platforms/woocommerce)
- [Plugin](https://docs.tabby.ai/e-commerce-platforms/salla)
- [Plugin](https://docs.tabby.ai/e-commerce-platforms/zid)
- [Plugin](https://docs.tabby.ai/e-commerce-platforms/opencart/opencart-plugin-installation)
- [Plugin](https://docs.tabby.ai/e-commerce-platforms/expandcart)
- [Plugin](https://docs.tabby.ai/e-commerce-platforms/matjrah)
- [Plugin](https://docs.tabby.ai/e-commerce-platforms/salesforce)
- [Documentation](https://docs.tabby.ai/offline-payment-methods/pos-integration)
- [Documentation](https://docs.tabby.ai/offline-payment-methods/custom-payment-links)
- [Documentation](https://docs.tabby.ai/mobile-app-sdks/ios-sdk)
- [Logos](https://docs.tabby.ai/marketing-resources/brand-assets)
- [LinkedIn](https://www.linkedin.com/company/tabby/)
- [Twitter](https://twitter.com/tabby)
- [Instagram](https://www.instagram.com/tabby/)
- [Blog](https://tabby.ai/en-AE/newsroom)
- [Pricing](https://tabby.ai/en-AE/help-business/about-tabby/pricing)
- [Plans](plans/tabby-plans-pricing.yml)
- [Rate Limits](rate-limits/tabby-rate-limits.yml)
- [Fin Ops](finops/tabby-finops.yml)
- [Spectral Rules](rules/tabby-rules.yml)
- [Vocabulary](vocabulary/tabby-vocabulary.yml)

## Maintainers

**FN:** Tabby
**Email:** partners@tabby.ai
**URL:** https://docs.tabby.ai
