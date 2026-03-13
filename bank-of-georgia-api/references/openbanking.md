# Bank of Georgia Open Banking (PSD2)

Base: `https://xs2a-sandbox.bog.ge/0.8/`

Implements PSD2 XS2A standard. All AIS endpoints require a valid consent.

## Account Information Services (AIS)

### Create Consent

```http
POST /0.8/psd2/v1/consents
Authorization: Bearer {token}
Content-Type: application/json
X-Request-ID: {uuid}
PSU-IP-Address: {ip}
```

```json
{
  "access": {
    "accounts": [{ "iban": "GE50BG0000000001234567" }],
    "balances": [{ "iban": "GE50BG0000000001234567" }],
    "transactions": [{ "iban": "GE50BG0000000001234567" }]
  },
  "recurringIndicator": true,
  "validTo": "2024-12-31",
  "frequencyPerDay": 4,
  "combinedServiceIndicator": false
}
```

**Response:**
```json
{
  "consentId": "consent-bog-xyz",
  "consentStatus": "received",
  "_links": {
    "scaRedirect": { "href": "https://..." },
    "scaOAuth": { "href": "https://api.bog.ge/.well-known/oauth-authorization-server" },
    "self": { "href": "/psd2/v1/consents/consent-bog-xyz" },
    "status": { "href": "/psd2/v1/consents/consent-bog-xyz/status" },
    "startAuthorisation": { "href": "/psd2/v1/consents/consent-bog-xyz/authorisations" }
  }
}
```

### Get Consent Status
```http
GET /0.8/psd2/v1/consents/{consentId}/status
X-Request-ID: {uuid}

Response: { "consentStatus": "valid" }
```

### Manage Consent Authorisations

Retrieve all authorisation sub-resources or check a specific SCA status.

```http
GET /0.8/psd2/v1/consents/{consentId}/authorisations
GET /0.8/psd2/v1/consents/{consentId}/authorisations/{authorisationId}
Authorization: Bearer {token}
X-Request-ID: {uuid}
```

**Response (List):**
```json
{
  "_links": {
    "authorisation-1": { "href": "/v1/consents/{id}/authorisations/{authId}" }
  }
}
```

**Response (Status):**
```json
{ "scaStatus": "received" }
```

### List Accounts

```http
GET /0.8/v1/accounts
Authorization: Bearer {token}
Consent-ID: {consentId}
X-Request-ID: {uuid}
PSU-IP-Address: {ip}
```

**Response:**
```json
{
  "accounts": [
    {
      "resourceId": "acct-bog-abc",
      "iban": "GE50BG0000000001234567",
      "currency": "GEL",
      "ownerName": "John Doe",
      "product": "Current Account",
      "cashAccountType": "CACC",
      "status": "enabled",
      "_links": {
        "balances": { "href": "/0.8/v1/accounts/acct-bog-abc/balances" },
        "transactions": { "href": "/0.8/v1/accounts/acct-bog-abc/transactions" }
      }
    }
  ]
}
```

### Get Account Balances

```http
GET /0.8/v1/accounts/{accountId}/balances
Authorization: Bearer {token}
Consent-ID: {consentId}
X-Request-ID: {uuid}
```

### Get Transactions

```http
GET /0.8/v1/accounts/{accountId}/transactions
  ?dateFrom=2023-01-01
  &dateTo=2023-12-31
  &bookingStatus=booked
Authorization: Bearer {token}
Consent-ID: {consentId}
X-Request-ID: {uuid}
```

**Response:**
```json
{
  "account": { "iban": "GE50BG0000000001234567" },
  "transactions": {
    "booked": [
      {
        "transactionId": "txn-123",
        "creditorName": "Shop Name",
        "creditorAccount": { "iban": "GE..." },
        "transactionAmount": { "currency": "GEL", "amount": "-49.99" },
        "bookingDate": "2023-06-15",
        "valueDate": "2023-06-15",
        "remittanceInformationUnstructured": "Online purchase"
      }
    ],
    "pending": []
  }
}
```

## Payment Initiation Services (PIS)

### Payment Products

| Product | Description |
|---------|-------------|
| `sepa-credit-transfers` | SEPA standard transfers |
| `domestic` | Local GEL transfers |
| `aspsp` | BOG's own product (supports FX) |
| `instant` | Instant payment |
| `foreign` | Foreign currency transfers |

### Initiate Payment

```http
POST /0.8/psd2/v1/payments/{paymentProduct}
Authorization: Bearer {token}
Content-Type: application/json
X-Request-ID: {uuid}
PSU-IP-Address: {ip}
```

**Common Products:** `domestic-credit-transfers-gel`, `sepa-credit-transfers`, `aspsp`, `instant`, `foreign`

**Periodic SCT Request Example:**
```json
{
  "instructedAmount": { "currency": "GEL", "amount": "123.00" },
  "debtorAccount": { "iban": "GE50BG..." },
  "creditorName": "Merchant123",
  "creditorAccount": { "iban": "GE00BG..." },
  "startDate": "2024-03-01",
  "frequency": "Monthly",
  "dayOfExecution": "01",
  "executionRule": "preceding"
}
```

**Response (multiple authorization modes):**
```json
{
  "transactionStatus": "RCVD",
  "paymentId": "1234-pay-983",
  "_links": {
    "scaRedirect": { "href": "https://www.bog.ge/authorize/..." },
    "self": { "href": "/psd2/v1/payments/1234-pay-983" },
    "status": { "href": "/psd2/v1/payments/1234-pay-983/status" },
    "scaStatus": { "href": "/psd2/v1/payments/1234-pay-983/authorisations/123auth456" }
  }
}
```

For OAuth2 flow the response includes:
```json
{ "_links": { "scaOAuth": { "href": "https://api.bog.ge/.well-known/..." } } }
```

### Get Payment Status

```http
GET /0.8/psd2/v1/payments/{paymentProduct}/{paymentId}/status
X-Request-ID: {uuid}
```

### Manage Payment Authorisations

Retrieve all authorisation sub-resources or check a specific SCA status.

```http
GET /0.8/psd2/v1/payments/{paymentProduct}/{paymentId}/authorisations
GET /0.8/psd2/v1/payments/{paymentProduct}/{paymentId}/authorisations/{authorisationId}
GET /0.8/v1/{paymentService}/{paymentProduct}/{paymentId}/cancellation-authorisations/{authorisationId}
```

**Response (Cancellation Status):**
```json
{ "scaStatus": "psuAuthenticated" }
```

## Signing Baskets (Batch Authorization)

Authorize multiple payments in a single SCA:

```http
POST /0.8/psd2/v1/signing-baskets
Authorization: Bearer {token}
Content-Type: application/json
X-Request-ID: {uuid}

{
  "paymentIds": ["1234pay567", "1234pay568", "1234pay888"]
}
```

**Response:**
```json
{
  "transactionStatus": "RCVD",
  "basketId": "1234-basket-567",
  "_links": {
    "self": { "href": "/psd2/v1/signing-baskets/1234-basket-567" },
    "status": { "href": "/psd2/v1/signing-baskets/1234-basket-567/status" },
    "startAuthorisation": { "href": "/psd2/v1/signing-baskets/1234-basket-567/authorisations" }
  }
}
```

### Get Signing Basket
```http
GET /0.8/psd2/v1/signing-baskets/{basketId}
```

**Response:**
```json
{
  "payments": ["1234pay567", "1234pay568", "1234pay888"],
  "transactionStatus": "ACTC"
}
```

## Banking Products Catalog

```http
GET /0.8/banking/products/{locale}
Host: xs2a-sandbox.bog.ge
```

- `locale` values: `en`, `ka` (Georgian)
- Optional query params: `page`, `size` (pagination)

```http
GET /banking/products/{locale}/personal-loans
GET /banking/products/{locale}/business-loans
GET /banking/products/{locale}/deposits
```
