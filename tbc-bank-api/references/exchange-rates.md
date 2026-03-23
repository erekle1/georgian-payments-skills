# TBC Exchange Rates API

Authentication: `apikey` header only (no Bearer token needed).

## Base URLs

| Environment | URL |
|-------------|-----|
| Test | `https://test-api.tbcbank.ge` |
| Production | `https://api.tbcbank.ge` |

---

## Get Commercial Rates

```http
GET /v1/exchange-rates/commercial?currency=USD,EUR,JPY
apikey: {api_key}
```

**Response:**
```json
{
  "base": "GEL",
  "commercialRatesList": [
    { "currency": "USD", "buy": 2.6200, "sell": 2.6800 },
    { "currency": "EUR", "buy": 2.8500, "sell": 2.9200 }
  ]
}
```

---

## Convert with Commercial Rates

```http
GET /v1/exchange-rates/commercial/convert?amount=1000&from=USD&to=GEL
apikey: {api_key}
```

**Response:**
```json
{ "from": "USD", "to": "GEL", "amount": 1000, "value": 2650.00 }
```

---

## Get Official (NBG) Rates

```http
GET /v1/exchange-rates/nbg?currency=USD,EUR
apikey: {api_key}
```

---

## Convert with Official Rates

```http
GET /v1/exchange-rates/nbg/convert?amount=1000&from=USD&to=GEL
apikey: {api_key}
```

**Response:**
```json
{ "from": "USD", "to": "GEL", "amount": 1000, "value": 2645.00 }
```

---

## Go-Live

Automatically available for production after testing. Get status 200 in test → ready for production.
