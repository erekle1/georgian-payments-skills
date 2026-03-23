# TBC Online Installment Loans

## Base URLs

| Environment | URL |
|-------------|-----|
| Test | `https://test-api.tbcbank.ge` |
| Production | `https://api.tbcbank.ge` |

Authentication: Bearer token in `Authorization` header.

---

## Create Application

```http
POST /v1/online-installments/applications
Authorization: Bearer {token}
apikey: {api_key}
Content-Type: application/json

{
  "merchantKey": "your_merchant_key",
  "campaignId": "campaign_id",
  "priceTotal": 1500.00,
  "products": [
    { "name": "Samsung TV", "price": 1500.00, "quantity": 1 }
  ],
  "invoiceId": "SHOP-ORDER-001"
}
```

**Response:** HTTP 200 with `Location` header containing redirect URL for customer.
- `sessionId` in response body — use for subsequent operations
- `Id` field always returns null (ignore it)

Redirect customer to URL from `Location` header to fill loan application.

---

## Cancel Application

```http
POST /v1/online-installments/applications/{session-id}/cancel
Authorization: Bearer {token}
apikey: {api_key}
```

---

## Confirm Application

```http
POST /v1/online-installments/applications/{session-id}/confirm
Authorization: Bearer {token}
apikey: {api_key}
```

---

## Check Application Status

```http
GET /v1/online-installments/applications/{session-id}/status
Authorization: Bearer {token}
apikey: {api_key}
```

---

## Merchant Status Changes (Polling)

```http
GET /v1/online-installments/merchant/applications/status-changes
Authorization: Bearer {token}
Content-Type: application/json

{
  "merchantKey": "your_merchant_key",
  "take": 100
}
```

**Response:**
```json
{
  "synchronizationRequestId": "req-uuid",
  "totalCount": 5,
  "statusChanges": [
    {
      "sessionId": "session-uuid",
      "statusId": 1,
      "statusDescription": "In Progress",
      "changeDate": "2024-01-15 10:30:00.0",
      "amount": 1500.00,
      "contributionAmount": 0.00
    }
  ]
}
```

### Confirm Synchronization

```http
POST /v1/online-installments/merchant/applications/status-changes-sync
Authorization: Bearer {token}
Content-Type: application/json

{ "synchronizationRequestId": "req-uuid" }
```

---

## Status IDs

| ID | Description |
|----|-------------|
| 0 | Pending client authentication |
| 1 | In Progress |
| (2-13) | Various processing/completion states |

---

## Go-Live

Email `merchant.support@tbcbank.ge` to receive:
- `merchantKey` — Unique merchant identifier
- `campaignId` — Campaign identifier for installment terms

---

## Testing Flow

1. Get access token
2. Call `POST /v1/online-installments/applications` to initiate
3. Get `sessionId` from response
4. Test cancel: `POST /v1/online-installments/applications/{session-id}/cancel`
5. Test confirm: `POST /v1/online-installments/applications/{session-id}/confirm`
6. `Location` header has redirect URL for customer
7. Send one COMPLETE and one CANCELED sessionId to `developers@tbcbank.ge`

**Common errors:**
- 404: Merchant not found or deactivated
- 400: Missing/invalid parameters
- 401: Expired session or token
