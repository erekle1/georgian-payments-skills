# TBC Checkout (TPay) — E-Commerce Payments

## API Base

| Environment | URL |
|-------------|-----|
| Sandbox | `https://test-api.tbcbank.ge` |
| Production | `https://api.tbcbank.ge` |

Endpoint pattern: `{base-url}/v1/tpay/{resource}`

All requests require `apikey` header + `Authorization: Bearer {token}` (except access-token endpoint).

---

## Get Access Token

```http
POST /v1/tpay/access-token
apikey: {api_key}
Content-Type: application/x-www-form-urlencoded

client_id={client_id}&client_secret={client_secret}
```

Response: `{ "access_token": "...", "token_type": "Bearer", "expires_in": 86400 }`

---

## Create Payment

```http
POST /v1/tpay/payments
Authorization: Bearer {token}
apikey: {api_key}
Content-Type: application/json
```

```json
{
  "amount": {
    "currency": "GEL",
    "total": 100.50,
    "subtotal": 90.00,
    "tax": 10.50,
    "shipping": 0
  },
  "returnurl": "https://yourshop.com/payment/return",
  "callbackUrl": "https://yourshop.com/webhooks/tbc",
  "preAuth": false,
  "language": "KA",
  "merchantPaymentId": "ORDER-001",
  "saveCard": false,
  "expirationMinutes": 12,
  "description": "Order #001",
  "installmentProducts": [
    { "name": "Product A", "price": 90.00, "quantity": 1 }
  ],
  "methods": [5]
}
```

**Key parameters:**
- `amount.currency` — GEL, USD, or EUR
- `returnurl` (required) — Redirect after payment
- `callbackUrl` — Webhook for status changes (POST with `{"PaymentId":"..."}`)
- `preAuth` — `true` for hold without capture (max 30 days)
- `saveCard` — `true` to save card for recurring
- `expirationMinutes` — Max 12 min recommended
- `methods` — Array of payment method IDs
- `description` — Max 30 chars

**Response:**
```json
{
  "payId": "6azzo13uw5u2116332",
  "status": "Created",
  "recId": "abcd1234",
  "httpStatusCode": 200,
  "links": [
    { "uri": "https://api.tbcbank.ge/v1/tpay/payments/6azzo13uw5u2116332", "method": "GET", "rel": "self" },
    { "uri": "https://checkout.tbcbank.ge/...", "method": "REDIRECT", "rel": "approval_url" }
  ],
  "transactionId": "UFC_TX_ID"
}
```

Redirect customer to `approval_url` link.

---

## Get Payment Details

```http
GET /v1/tpay/payments/{payId}
Authorization: Bearer {token}
apikey: {api_key}
```

**Response:**
```json
{
  "payId": "6azzo13uw5u2116332",
  "status": "Succeeded",
  "currency": "GEL",
  "amount": 100.50,
  "confirmedAmount": 100.50,
  "returnedAmount": 0,
  "paymentMethod": 5,
  "preAuth": false,
  "RRN": "123456789012",
  "recurringCard": { "recId": "...", "cardMask": "4***1234", "expiryDate": "12/26" },
  "resultCode": "approved",
  "operationType": 0
}
```

**Payment Statuses:**
- `Created` — Payment initiated
- `Processing` — In progress
- `Succeeded` — Payment completed ✓
- `Failed` — Payment failed
- `Expired` — Timed out
- `WaitingConfirm` — Pre-auth awaiting completion
- `Returned` — Full refund
- `PartialReturned` — Partial refund

**Final statuses:** Succeeded, Failed, Expired, WaitingConfirm (for preauth)

---

## Cancel / Refund Payment

```http
POST /v1/tpay/payments/{payId}/cancel
Authorization: Bearer {token}
apikey: {api_key}
Content-Type: application/json

{ "amount": 50.00 }
```

- Omit `amount` for full refund
- Partial cancellation: only once per day (same day)
- Next-day: refund remaining amount
- Pre-auth: cannot cancel partial before completion
- Split payments: use `extra` (IBAN) and `extra2` (amount) fields

---

## Complete Pre-Authorized Payment

```http
POST /v1/tpay/payments/{payId}/completion
Authorization: Bearer {token}
apikey: {api_key}
Content-Type: application/json

{ "amount": 100.50 }
```

- Must complete within 30 days
- Amount must be ≤ original authorized amount
- Difference auto-returned to customer

---

## Execute Recurring Payment

```http
POST /v1/tpay/payments/execution
Authorization: Bearer {token}
apikey: {api_key}
Content-Type: application/json

{
  "recId": "saved_card_id",
  "money": { "amount": 50.00, "currency": "GEL" },
  "preAuth": false,
  "initiator": "merchant",
  "merchantPaymentId": "RECURRING-001"
}
```

Response: `{ "payId": "...", "status": "Succeeded", "recurringCard": {...} }`

### Delete Recurring Card

```http
POST /v1/tpay/payments/{recId}/delete
Authorization: Bearer {token}
apikey: {api_key}
```

---

## QR Payments (v2)

### Create QR Payment

```http
POST /v2/tpay/qr
Authorization: Bearer {token}
apikey: {api_key}
Content-Type: application/json

{
  "amount": 10.00,
  "currency": "GEL",
  "terminalNo": "TERM001",
  "merchantPaymentId": "QR-001",
  "callbackUrl": "https://yourshop.com/webhooks/tbc-qr"
}
```

Response: `{ "payId": "...", "qrCode": "url_to_qr_image", "amount": 10.00, "expirationMinutes": 5 }`

### Get QR Payment Status

```http
GET /v2/tpay/qr/{payId}
Authorization: Bearer {token}
apikey: {api_key}
```

---

## Callback IP Whitelist

Whitelist these IPs for incoming webhooks:
- `193.104.20.44`
- `193.104.20.45`
- `185.52.80.44`
- `185.52.80.45`

## Result Codes

| Code | Description |
|------|-------------|
| `approved` | Transaction approved |
| `decline_general` | General error |
| `decline_expired_card` | Card expired |
| `decline_suspected_fraud` | Suspected fraud |
| `decline_restricted_card` | Card restricted |
| `decline_invalid_card_number` | Invalid card number |
| `decline_not_sufficient_funds` | Insufficient funds |
| `decline_card_not_effective` | Card not active |
| `check_with_acquirer` | Needs verification |
