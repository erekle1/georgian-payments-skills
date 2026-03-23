---
name: tbc-bank-api
description: >
  Expert guide for integrating TBC Bank's APIs — Checkout (TPay) e-commerce payments, online installment loans,
  exchange rates, Open Banking PSD2 (AIS, PIS), TBC ID, QR payments, mortgage leads, and DBI integration service.
  Covers both bank-side API usage AND merchant-side implementation: callback/webhook handlers, redirect URLs,
  signature verification, environment setup, registration documents, and go-live checklist.
  Use this skill whenever the user mentions TBC Bank, tbcbank.ge, TBC API, TPay, TBC checkout,
  TBC installments, TBC exchange rates, TBC Open Banking, PSD2 with TBC, TBC ID,
  callback handling, merchant registration with TBC, or any TBC Bank developer API work.
  Trigger even if they just say "TBC" in a banking/payments context.
---

# TBC Bank API Integration Guide

End-to-end reference for integrating TBC Bank payment and banking services — covering both the bank's APIs and everything the **merchant must build and register**.

## Quick Reference

| Topic | Reference File |
|-------|---------------|
| Authentication & Certificates | `references/auth.md` |
| Checkout / TPay (E-Commerce) | `references/checkout.md` |
| Online Installment Loans | `references/installments.md` |
| Exchange Rates API | `references/exchange-rates.md` |
| Open Banking / PSD2 + DBI | `references/openbanking.md` |
| Error Handling & Codes | `references/errors.md` |
| Design & Branding Guidelines | `references/design.md` |
| **Merchant Integration Guide** | **`references/merchant-integration.md`** |

## Base URLs

| Environment | URL |
|-------------|-----|
| Sandbox (Checkout) | `https://test-api.tbcbank.ge` |
| Production | `https://api.tbcbank.ge` |
| Dev OpenBanking | `https://dev-openbanking.tbcbank.ge` |
| OpenBanking | `https://openbanking.tbcbank.ge` |
| Merchant Dashboard | `https://ecom.tbcpayments.ge` |

## Universal Headers

```http
apikey: {your_api_key}
Authorization: Bearer {access_token}
Content-Type: application/json
```

## Common Error Response

```json
{
  "status": 400,
  "type": "https://api.tbcbank.ge/problems/bad-request",
  "title": "Bad Request",
  "detail": "Detailed description",
  "systemCode": "TBC-ERR-001",
  "code": "INVALID_REQUEST"
}
```

→ For full error reference, read `references/errors.md`

## Key Workflows

### 1. E-Commerce Payments (Checkout / TPay)
1. Get access token (`POST /v1/tpay/access-token`)
2. Create payment (`POST /v1/tpay/payments`) → get `payId` + `approval_url`
3. Redirect customer to `approval_url`
4. Receive callback POST with `PaymentId`
5. Get payment details (`GET /v1/tpay/payments/{payId}`)
6. Fulfill order if `status === "Succeeded"`

→ Read `references/checkout.md`

### 2. Pre-Authorization (Hold & Capture)
1. Create payment with `"preAuth": true`
2. Status becomes `WaitingConfirm` after authorization
3. Complete within 30 days: `POST /v1/tpay/payments/{payId}/completion`
4. Or cancel: `POST /v1/tpay/payments/{payId}/cancel`

→ Read `references/checkout.md`

### 3. Recurring Payments
1. First payment with `"saveCard": true` → get `recId`
2. Charge saved card: `POST /v1/tpay/payments/execution` with `recId`
3. Delete card: `POST /v1/tpay/payments/{recId}/delete`

→ Read `references/checkout.md`

### 4. Online Installment Loans
1. Create application → get `sessionId` + redirect URL
2. Redirect customer to fill loan application
3. Poll status or confirm/cancel application
4. Monitor via merchant status changes endpoint

→ Read `references/installments.md`

### 5. Exchange Rates
- Commercial: `GET /v1/exchange-rates/commercial`
- Official (NBG): `GET /v1/exchange-rates/nbg`
- Convert: `GET /v1/exchange-rates/{type}/convert?amount=X&from=USD&to=GEL`

→ Read `references/exchange-rates.md`

### 6. Authentication
→ Read `references/auth.md` first — covers OAuth2, apikey, mTLS certificates, DBI auth

### 7. Open Banking / PSD2
Account movements, statements, payment initiation via PSD2 standard with mTLS.

→ Read `references/openbanking.md`

### 8. Design & Branding
TBC brand colors (`#00AEEF` primary blue), button variants, input styles, typography (`TBC Sailec Regular`), and card components for merchant payment UI.

→ Read `references/design.md`

### 9. Merchant Integration (What YOU Must Build)
1. Set up callback endpoint (HTTPS, returns 200, idempotent)
2. Whitelist TBC IPs: `193.104.20.44/45`, `185.52.80.44/45`
3. After receiving callback, GET payment details to verify status and amount
4. Configure environment variables (credentials + URLs)
5. Handle duplicate callback delivery (idempotency by PaymentId)

→ Read `references/merchant-integration.md`

### 10. Go-Live
- **Checkout:** Ensure website has Terms, Return Policy, Privacy Policy, Delivery Policy, Contact Info → activate via dashboard
- **Installments:** Email `merchant.support@tbcbank.ge` for production keys
- **Exchange Rates:** Automatic after testing
- **PSD2:** Certificate from authorized provider + TBC approval

→ Read `references/merchant-integration.md`

## Response Conventions

- Monetary amounts are **decimal numbers** (`"amount": 100.50`)
- Currency: `GEL`, `USD`, `EUR`
- Payment statuses: `Created`, `Processing`, `Succeeded`, `Failed`, `Expired`, `WaitingConfirm`, `Returned`, `PartialReturned`
- Callback body is minimal (`{"PaymentId":"..."}`) — always GET full details after receiving
