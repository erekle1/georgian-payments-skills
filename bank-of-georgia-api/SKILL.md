---
name: bank-of-georgia-api
description: >
  Expert guide for integrating Bank of Georgia (BOG) APIs — Open Banking PSD2, iPay payment gateway,
  installment loans, BOG SDK (BOG.Calculator, BOG.SmartButton), OAuth2, BOG-ID (SSO), Business Online
  (BOnline) corporate banking, Payment Gateway (billing/service providers), and banking products.
  Covers both bank-side API usage AND merchant-side implementation: webhook/callback handlers, redirect URLs,
  idempotency, environment setup, registration documents, and go-live checklist.
  Use this skill whenever the user mentions Bank of Georgia, BOG, bog.ge, api.bog.ge, iPay, BOG installments,
  BOG SDK, BOG-ID, BOnline, Georgian bank integration, callback handling, merchant registration, or any BOG-related development.
  Trigger even if they just say "BOG" in a tech/banking context.
---

# Bank of Georgia API Integration Guide

End-to-end reference for integrating BOG payment services — covering both the bank's APIs and everything the **merchant must build and register**.

## Quick Reference

| Topic | Reference File |
|-------|---------------|
| OAuth2 & Authentication | `references/auth.md` |
| Open Banking / PSD2 (AIS + PIS) | `references/openbanking.md` |
| iPay Payment Gateway | `references/ipay.md` |
| Installment Loans + BOG SDK | `references/installments.md` |
| Banking Products & Statements | `references/products.md` |
| BOG-ID (Single Sign-On) | `references/bogid.md` |
| Business Online (BOnline) | `references/bonline.md` |
| Payment Gateway (Billing) | `references/payment-gateway.md` |
| **Merchant Integration Guide** | **`references/merchant-integration.md`** |
| **Merchant Registration & Documents** | **`references/merchant-documents.md`** |

## Base URLs

| Environment | Base URL |
|-------------|----------|
| PSD2 Sandbox | `https://xs2a-sandbox.bog.ge/0.8/` |
| iPay Sandbox | `https://ipay.ge` (test mode via credentials) |
| Installment | `https://installment.bog.ge/v1/` |
| Production API | `https://api.bog.ge` |
| WebStatic (SDK) | `https://webstatic.bog.ge` |

## Universal Headers

```http
Authorization: Bearer {jwt_token}
Content-Type: application/json
X-Request-ID: {uuid4}
PSU-IP-Address: {user_ip}
```

For some endpoints use Basic auth:
```http
Authorization: Basic {base64(client_id:client_secret)}
```

For signed requests:
```http
Digest: SHA-256={base64_body_hash}
x-jws-signature: {jws_signature}
```

## Common Error Response

```json
{
  "type": "https://...",
  "title": "Error summary",
  "status": 400,
  "detail": "Detailed description",
  "instance": "/path/to/resource",
  "code": "APP_ERROR_CODE",
  "traceId": "abc123"
}
```

## Key Workflows

### 1. Account/Transaction Data Access (AIS)
1. Create consent
2. User authorizes via SCA redirect or OAuth
3. Use `Consent-ID` header on AIS requests

→ Read `references/openbanking.md`

### 2. Payment Initiation (PIS)
1. POST payment → get `paymentId` + `_links`
2. Redirect or OAuth SCA authorization
3. Poll status or receive callback

→ Read `references/openbanking.md`

### 3. E-commerce Payments (iPay)
For simple card payments, pre-auth, and refunds.

→ Read `references/ipay.md`

### 4. Installment Loans
Two options:
- **API only** — backend integration
- **SDK** — `BOG.Calculator` frontend modal

→ Read `references/installments.md`

### 5. Authentication
→ Read `references/auth.md` first

### 5a. BOG-ID (Single Sign-On)
Authenticate users with their BOG credentials. Supports scopes for personal info, documents, contacts.

→ Read `references/bogid.md`

### 5b. Business Online (Corporate Banking)
Domestic/foreign transfers, statements, exchange rates, document signing with OTP.

→ Read `references/bonline.md`

### 5c. Payment Gateway (Billing/Service Providers)
For utility/telecom service providers accepting payments via BOG interfaces. Supports Basic, OAuth2, API Key, HMAC-SHA256 auth.

→ Read `references/payment-gateway.md`

### 6. Merchant Integration (What YOU Must Build)
1. Set up webhook/callback endpoints (HTTPS, idempotent, returns 200)
2. Validate `shop_order_id` and `amount` before fulfilling
3. Set up OAuth2 redirect handler (state param, code exchange)
4. Configure environment variables (credentials + URLs)
5. Handle duplicate webhook delivery (idempotency by `order_id` + `event`)

→ Read `references/merchant-integration.md`

### 7. Bank Registration & Go-Live Documents
1. Register at `https://bonline.bog.ge` (iPay/installments) or `https://api.bog.ge` (PSD2)
2. Prepare technical specification with your URLs
3. Gather required business documents
4. Submit go-live request after sandbox testing

→ Read `references/merchant-documents.md`

## Response Conventions

- Monetary amounts are **strings** (`"amount": "100.50"`)
- Dates use **ISO 8601** format
- HATEOAS `_links` pattern throughout — follow links, don't hardcode paths
- `transactionStatus`: `RCVD`, `ACTC`, `ACCP`, `ACFC`, `ACSP`, `ACCC`, `RJCT`
- `scaStatus`: `received`, `psuIdentified`, `psuAuthenticated`, `scaMethodSelected`, `finalised`, `failed`
