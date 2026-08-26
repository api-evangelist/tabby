---
name: Tabby
description: Use when integrating Buy Now Pay Later (BNPL) payment solutions into e-commerce platforms, custom websites, or mobile apps. Reach for this skill when building checkout flows, handling payment processing, managing webhooks, testing integrations, or troubleshooting payment status issues in MENA region (KSA, UAE, Kuwait).
metadata:
    mintlify-proj: tabby
    version: "1.0"
---

# Tabby Integration Skill

## Product summary

Tabby is MENA's largest Buy Now Pay Later (BNPL) service operating in KSA, UAE, and Kuwait. It lets merchants offer customers interest-free installment payments (4 payments, or up to 12 months) online, in-store via QR codes, or through payment links. Agents use Tabby to integrate payment flows into e-commerce platforms (Shopify, WooCommerce, Magento 2, OpenCart, Odoo, Salla, Zid, Salesforce, ExpandCart) or build custom API integrations for web and mobile apps.

**Key resources:**
- API Base URLs: `https://api.tabby.ai` (UAE/Kuwait) or `https://api.tabby.sa` (KSA)
- Merchant Dashboard: `merchant.tabby.ai` or `merchant.tabby.sa`
- Primary docs: https://docs.tabby.ai
- API Reference: https://docs.tabby.ai/api-reference/overview
- Test credentials: Use `pk_test_*` and `sk_test_*` keys; production uses `pk_*` and `sk_*`

## When to use

Reach for this skill when:
- **Building checkout flows**: Integrating Tabby as a payment method on product, cart, or checkout pages
- **Handling payment processing**: Verifying payment status, capturing authorized payments, processing refunds
- **Setting up webhooks**: Registering endpoints to receive real-time payment status notifications
- **Testing integrations**: Running test scenarios with test credentials before going live
- **Troubleshooting payment issues**: Diagnosing why payments are rejected, not captured, or stuck in AUTHORIZED status
- **Mobile app integration**: Opening Tabby checkout in WebView or system browser with proper state recovery
- **Offline payments**: Creating QR code sessions for POS terminals or custom payment links
- **Platform-specific setup**: Installing and configuring Tabby plugins for Shopify, WooCommerce, Magento 2, etc.

## Quick reference

### API Endpoints (all regions use same paths)

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/api/v2/checkout` | POST | Eligibility check or create payment session |
| `/api/v2/payments/{payment.id}` | GET | Retrieve payment status (source of truth) |
| `/api/v2/payments/{payment.id}/captures` | POST | Capture authorized payment |
| `/api/v2/payments/{payment.id}/refunds` | POST | Refund captured payment |
| `/api/v2/payments/{payment.id}/close` | POST | Cancel/close payment without capturing |
| `/api/v2/webhooks` | POST/GET/PATCH/DELETE | Register and manage webhooks |

### Payment Statuses

| Status | Meaning | Your action |
|--------|---------|------------|
| `CREATED` | Checkout in progress | Keep polling or wait for webhook |
| `AUTHORIZED` | Customer approved, not yet captured | Capture the payment immediately |
| `CLOSED` | Payment captured and confirmed | Order fulfilled, payment settled |
| `REJECTED` | Payment declined by Tabby | Show failure message, allow retry |
| `EXPIRED` | Session expired or customer cancelled | Allow customer to retry or use different method |

### Test Credentials by Country

| Country | Email | Phone | OTP |
|---------|-------|-------|-----|
| **UAE (Success)** | otp.success@tabby.ai | +971500000001 | 8888 |
| **UAE (Reject)** | otp.rejected@tabby.ai | +971500000001 | 8888 |
| **KSA (Success)** | otp.success@tabby.ai | +966500000001 | 8888 |
| **KSA (Reject)** | otp.rejected@tabby.ai | +966500000001 | 8888 |
| **Kuwait (Success)** | otp.success@tabby.ai | +96590000001 | 8888 |
| **Kuwait (Reject)** | otp.rejected@tabby.ai | +96590000001 | 8888 |

### Webhook Security

**Allowed Tabby server IPs** (allowlist these):
```
34.166.36.90, 34.166.35.211, 34.166.34.222, 34.166.37.207
34.93.76.191, 34.166.128.182, 34.166.170.3, 34.166.249.7
```

**Webhook payload status format**: Lowercase (`"authorized"`, `"closed"`, `"rejected"`)  
**API response status format**: Uppercase (`"AUTHORIZED"`, `"CLOSED"`, `"REJECTED"`)

## Decision guidance

### When to use eligibility check vs session creation

| Aspect | Eligibility Check | Session Creation |
|--------|-------------------|------------------|
| **Purpose** | Determine if customer can use Tabby | Create actual payment session |
| **Timing** | Before showing Tabby on checkout | When customer clicks "Place order" |
| **Payload** | Minimal (amount, currency, buyer email/phone) | Complete (all order details, items, shipping, URLs) |
| **Response** | Status + rejection reason | Status + `web_url` + `payment.id` |
| **Endpoint** | `POST /api/v2/checkout` | `POST /api/v2/checkout` (same endpoint, different payload) |

### When to use webhooks vs polling

| Approach | Best for | Trade-offs |
|----------|----------|-----------|
| **Webhooks (recommended)** | Real-time payment updates, catching authorized payments after redirect closes | Requires endpoint, IP allowlisting, deduplication logic |
| **Polling (fallback)** | Simple integrations, testing, catching missed webhooks | Higher latency, more API calls, less reliable |
| **Both** | Production systems | Webhooks as primary, polling as safety net for missed notifications |

### When to use capture vs close

| Operation | Use when | Effect |
|-----------|----------|--------|
| **Capture** | Order is confirmed and ready to fulfill | Funds released to merchant, customer charged, disputes possible |
| **Close** | Order cancelled, customer requested refund, or technical error | Payment cancelled, downpayment returned to customer, no funds to merchant |

## Workflow

### 1. Set up credentials and environment

1. Register merchant account at `merchant.tabby.ai` (UAE/Kuwait) or `merchant.tabby.sa` (KSA)
2. Retrieve test API keys (`pk_test_*`, `sk_test_*`) from Merchant Dashboard or account manager
3. Identify your region's API base URL: `api.tabby.ai` or `api.tabby.sa`
4. Store credentials securely (never commit to version control)

### 2. Implement eligibility check (before showing Tabby)

1. When customer enters checkout with basic order details, call `POST /api/v2/checkout` with minimal payload:
   - `payment.amount`, `payment.currency`, `payment.buyer.email`, `payment.buyer.phone`, `merchant_code`
2. Check response `status`:
   - `"created"` → show Tabby as payment option
   - `"rejected"` → hide Tabby or show rejection message (extract from `configuration.products.installments.rejection_reason`)
3. If API times out or errors, default to showing Tabby (fail-safe approach)

### 3. Create payment session (when customer clicks "Place order")

1. Call `POST /api/v2/checkout` again with **complete payload** including order items, shipping address, and merchant URLs
2. Validate response:
   - Check `status == "created"`
   - Verify `web_url` is present (if missing, show rejection message)
   - **Store `payment.id`** for later verification and capture
3. Redirect customer to `web_url` (Tabby Hosted Payment Page)

### 4. Handle payment verification (after checkout)

1. Customer completes Tabby checkout and is redirected to one of your `merchant_urls` (success/cancel/failure)
2. **Never trust the redirect alone** — always verify server-side:
   - Call `GET /api/v2/payments/{payment.id}` with your secret key
   - Check the `status` field
3. Based on status:
   - `AUTHORIZED` → process order, proceed to capture
   - `REJECTED` / `EXPIRED` → cancel order, show failure message
   - `CREATED` → payment still pending, keep checking

### 5. Capture the payment

1. Once payment is `AUTHORIZED`, call `POST /api/v2/payments/{payment.id}/captures`
2. Include `reference_id` (your unique key) for idempotency
3. Capture the **full amount** (partial captures supported but not recommended)
4. Response status `200` with `CLOSED` status = payment confirmed and settled to you
5. Only captured payments are settled; only captured payments can be disputed

### 6. Set up webhooks (recommended for production)

1. Register webhook endpoint: `POST /api/v2/webhooks` with your URL and optional auth header
2. Tabby sends POST requests to your endpoint on payment status changes
3. Respond with `200` immediately, process asynchronously
4. Webhook statuses are lowercase (`"authorized"`, `"closed"`); API responses are uppercase
5. Treat webhooks as notifications only — always confirm with `getPayment` API
6. Handle out-of-order delivery and deduplication (same event may arrive twice)

### 7. Test before going live

1. Use test credentials from the Quick Reference table
2. Run all test scenarios: success, rejection, cancellation, failure, corner case (browser close)
3. Verify captures occur automatically or via webhook/polling
4. Check that refunds work via API or dashboard
5. Complete the full testing checklist before submitting for Tabby QA review
6. After QA approval, request live keys and deploy to production

## Common gotchas

- **Never trust redirects as proof of payment.** The redirect is a UX signal only. Always verify status server-side via `getPayment` API or webhooks. Customers can close the browser, lose connection, or the OS can kill the app before they reach your success page.

- **Webhook statuses are lowercase, API responses are uppercase.** `"authorized"` in webhooks = `"AUTHORIZED"` in API responses. This is expected and correct.

- **Don't capture without verifying AUTHORIZED status first.** Attempting to capture a `CREATED`, `EXPIRED`, `CLOSED`, or `REJECTED` payment returns a `400` error.

- **Eligibility check ≠ session creation.** Both use the same endpoint but different payloads. Eligibility check uses minimal data; session creation requires complete order details. Calling eligibility check with full payload wastes data; calling session creation with minimal payload may fail.

- **Missing captures are auto-captured after 21 days.** If you don't capture within 21 days, Tabby may auto-capture on its side. Check Merchant Dashboard for `NEW` status payments and resolve them.

- **Webhook delivery order is not guaranteed.** A capture event may arrive before an authorization event. Use a finite state machine instead of assuming order.

- **Webhooks are not idempotent.** The same event may be delivered twice. Deduplicate by `payment_id` and `reference_id`.

- **Country mismatch causes rejection.** Tabby requires order currency to match customer's country: UAE-AED, KSA-SAR, Kuwait-KWD. If currency doesn't match, Tabby won't be displayed and session creation fails.

- **API keys not fully populated causes "issue processing payment" error.** Check Merchant Dashboard that both public and secret keys are saved. Also verify your integration has been set up on Tabby's side (contact partner support if not).

- **Closing WebView before redirect doesn't mean payment failed.** In mobile apps, the customer may pay successfully but close the WebView before being redirected. Always persist `payment_id` and re-verify status on app resume.

- **Rejection reasons are not disclosed to merchants.** Due to compliance restrictions, Tabby won't tell you why a specific payment was rejected. Contact partner support if you see systematic rejection patterns.

- **Don't set hardcoded transaction limits.** Tabby applies dynamic limits per customer. Hardcoded limits on your end may conflict with Tabby's risk model.

## Verification checklist

Before submitting your integration for Tabby QA review:

- [ ] Eligibility check is implemented and hides/marks Tabby unavailable when rejected
- [ ] Session creation includes all required fields: amount, currency, buyer, items, shipping, merchant URLs
- [ ] `payment.id` is stored immediately after session creation (for mobile state recovery)
- [ ] Payment verification calls `getPayment` API server-side, not relying on redirect URL alone
- [ ] Capture is triggered only for `AUTHORIZED` payments with full amount
- [ ] Webhooks are registered and respond with `200` immediately
- [ ] Webhook deduplication logic is in place (same event may arrive twice)
- [ ] Refund API is tested (full and partial refunds)
- [ ] All test scenarios pass: success, rejection, cancellation, failure, corner case
- [ ] Snippets are added to product, cart, and checkout pages
- [ ] Language parameter (`lang: "ar"` or `"en"`) is sent in session creation if applicable
- [ ] Error messages match approved Tabby messaging (English and Arabic)
- [ ] Mobile apps persist `payment_id` and re-verify on app resume
- [ ] Mobile apps handle WebView permissions (camera, gallery) if using embedded WebView
- [ ] Status page (https://www.tabby-status.com/) is monitored for incidents

## Resources

**Comprehensive page navigation:**  
https://docs.tabby.ai/llms.txt

**Critical documentation pages:**
- [Quick Start & Integration Overview](https://docs.tabby.ai/pay-in-4-custom-integration/quick-start) — end-to-end integration steps
- [Checkout Flow Guide](https://docs.tabby.ai/pay-in-4-custom-integration/checkout-flow) — eligibility checks, session creation, redirects
- [Payment Processing](https://docs.tabby.ai/pay-in-4-custom-integration/payment-processing) — verification, capture, refunds
- [Webhooks](https://docs.tabby.ai/pay-in-4-custom-integration/webhooks) — real-time payment notifications
- [Testing Credentials](https://docs.tabby.ai/testing-guidelines/testing-credentials) — test scenarios and expected results
- [API Reference](https://docs.tabby.ai/api-reference/overview) — complete endpoint documentation with OpenAPI spec
- [FAQ](https://docs.tabby.ai/introduction/faq) — common questions, troubleshooting, best practices

**Support contacts:**
- Partner/Integration support: `partner@tabby.ai` (UAE/Kuwait) or `partner@tabby.sa` (KSA)
- Customer support: `help@tabby.ai` or `help@tabby.sa`

---

> For additional documentation and navigation, see: https://docs.tabby.ai/llms.txt