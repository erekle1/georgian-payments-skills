# Bank of Georgia Banking Products & Statements

## Banking Products Catalog

Retrieve available BOG financial products for display on your platform.

### List All Products

```http
GET /0.8/banking/products/{locale}
Host: xs2a-sandbox.bog.ge
Authorization: Bearer {token}
```

- `locale`: `en` (English), `ka` (Georgian/ქართული)
- Optional: `?page=0&size=20` for pagination

### Product Categories

```http
GET /0.8/banking/products/{locale}/personal-loans
GET /0.8/banking/products/{locale}/business-loans
GET /0.8/banking/products/{locale}/deposits
GET /0.8/banking/products/{locale}/cards
GET /0.8/banking/products/{locale}/mortgages
```

**Example Response:**
```json
{
  "products": [
    {
      "id": "prod-001",
      "name": "Personal Loan",
      "category": "personal-loans",
      "currency": "GEL",
      "minAmount": 500,
      "maxAmount": 50000,
      "minTerm": 6,
      "maxTerm": 84,
      "annualRate": 19.9,
      "description": "Fast personal loan with no collateral",
      "features": ["instant approval", "online application"]
    }
  ],
  "total": 15,
  "page": 0,
  "size": 20
}
```

## Account Statements (BOOnline)

For business accounts integrated with BOOnline.

### Generate Statement

```http
GET /api/statement/{accountNumber}/{currency}/{startDate}/{endDate}
Authorization: Bearer {token}
```

**Parameters:**
- `accountNumber` — Full account number
- `currency` — ISO 4217 (GEL, USD, EUR)
- `startDate` — `YYYY-MM-DD`
- `endDate` — `YYYY-MM-DD`

### Statement v2 (Enhanced)

```http
GET /api/statement/v2/{accountNumber}/{currency}/{startDate}/{endDate}
Authorization: Bearer {token}
```

Returns more detailed transaction metadata.

## Exchange Rates

### NBG Historical Rates

```http
GET /api/rates/nbg/{currency}/{startDate}/{endDate}
Authorization: Bearer {token}
```

- `currency` — e.g., `USD`, `EUR`

**Response:**
```json
{
  "rates": [
    {
      "date": "2023-06-15",
      "currency": "USD",
      "rate": 2.6450,
      "change": 0.0020
    }
  ]
}
```

## Payment Documents (BOOnline)

Create domestic payment documents for business customers:

```http
POST /api/documents/domestic
Authorization: Bearer {token}
Content-Type: application/json

{
  "debtorAccountNumber": "GE50BG...",
  "amount": 500.00,
  "currency": "GEL",
  "creditorAccountNumber": "GE99...",
  "creditorName": "Vendor Ltd",
  "purpose": "Invoice payment #INV-001",
  "transactionCode": "00"
}
```

## SCA Process (Websites Integration)

For initiating SCA (Strong Customer Authentication) on BOG's website integration layer:

```http
POST /websites/api_bog_ge_en/start-sca-process
Content-Type: application/json
Authorization: Bearer {token}

{
  "paymentId": "1234-wertiq-983",
  "paymentProduct": "sepa-credit-transfers"
}
```

List payment authorisations:
```http
GET /websites/api_bog_ge_en/v1/{paymentService}/{paymentProduct}/{paymentId}/authorisations
```
