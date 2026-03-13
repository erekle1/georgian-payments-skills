---
name: bank-of-georgia-api
description: Expert guide for integrating Bank of Georgia (BOG) APIs — Open Banking PSD2, iPay payment gateway, installment loans, BOG SDK (BOG.Calculator, BOG.SmartButton), OAuth2, and banking products. Use this skill whenever the user mentions Bank of Georgia, BOG, bog.ge, api.bog.ge, iPay, BOG installments, BOG SDK, Georgian bank integration, or any BOG-related development. Trigger even if they just say "BOG" in a tech/banking context.
---

# Bank of Georgia API Integration Guide

Complete reference for building with Bank of Georgia's Open Banking, iPay, and installment APIs.

## Quick Reference

| Topic | Reference File |
|-------|---------------|
| OAuth2 & Authentication | `references/auth.md` |
| Open Banking / PSD2 (AIS + PIS) | `references/openbanking.md` |
| iPay Payment Gateway | `references/ipay.md` |
| Installment Loans + BOG SDK | `references/installments.md` |
| Banking Products & Statements | `references/products.md` |

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

## Response Conventions

- Monetary amounts are **strings** (`"amount": "100.50"`)
- Dates use **ISO 8601** format
- HATEOAS `_links` pattern throughout — follow links, don't hardcode paths
- `transactionStatus`: `RCVD`, `ACTC`, `ACCP`, `ACFC`, `ACSP`, `ACCC`, `RJCT`
- `scaStatus`: `received`, `psuIdentified`, `psuAuthenticated`, `scaMethodSelected`, `finalised`, `failed`
