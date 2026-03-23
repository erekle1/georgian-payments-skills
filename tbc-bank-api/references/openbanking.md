# TBC Open Banking (PSD2)

Framework: NextGenPSD2 v1.3.6. Requires mTLS certificates.

## Base URLs

| Environment | URL |
|-------------|-----|
| Dev OpenBanking | `https://dev-openbanking.tbcbank.ge` |
| Production | `https://openbanking.tbcbank.ge` |

## SCA Approaches

- **Redirect SCA**: User redirected to TBC for authentication
- **OAuth SCA**: Standard OAuth2 authorization code flow

---

## TBC ID (OpenID Connect)

### Discovery
```
GET https://api.tbcbank.ge/.well-known/openid-configuration
```

### Authorization
```
GET https://api.tbcbank.ge/psd2/openbanking/oauth/authorize
  ?response_type=code
  &client_id={tpp_client_id}
  &redirect_uri={callback}
  &scope=openid
  &state={random}
```

### Token Exchange
```http
POST https://api.tbcbank.ge/openbanking/oauth/token
Content-Type: application/x-www-form-urlencoded
# mTLS required

grant_type=authorization_code&code={code}&redirect_uri={callback}&client_id={tpp_id}
```

### User Info
```http
GET https://api.tbcbank.ge/userinfo
Authorization: Bearer {token}
# mTLS required
```

---

## Account Information Services (AIS)

### Account Movements

Max 700 entries per page.

```http
POST /account-movements
Content-Type: application/json

{
  "accountNumber": "GE00TB0000000012345678",
  "accountCurrencyCode": "GEL",
  "periodFrom": "2024-01-01T00:00:00.000",
  "periodTo": "2024-01-31T23:59:59.999",
  "pageIndex": 0,
  "pageSize": 700
}
```

**Response fields:**
- `movementId`, `paymentId`, `debitCredit` (0=Debit, 1=Credit)
- `valueDate`, `documentDate`
- `Amount` (with amount and currency)
- `partnerAccountNumber`, `partnerName`, `partnerTaxCode`, `partnerBankCode`
- `exchangeRate`, `statusCode`, `transactionType`

**Transaction Status:** 1=Waiting auth, 2=Waiting 2nd auth, 3=Authorized

**Transaction Subtypes:** 1=Own transfer, 5=Currency exchange, 20=Income, 30=Transfer, 31=Bills, 32=Treasury, 33=Other

### Account Statement (SOAP)

```http
POST /dbi/dbiService
Content-Type: text/xml
# Digital certificate + SOAP Security header required
```

**Request attributes:**
- `accountNumber`, `accountCurrencyCode`
- `periodFrom` (YYYY-MM-DD), `periodTo` (YYYY-MM-DD)

**Response:** `openingDate`, `openingBalance`, `closingDate`, `closingBalance`, `debitSum`, `creditSum`, `currency`

---

## Payment Initiation Services (PIS)

Uses SOAP XML format via DBI service. Supports:
- Single payment orders (`ImportSinglePaymentOrders`)
- Batch payment orders (`ImportBatchPaymentOrder`)

Authentication: Username + Password + Digital Certificate via SOAP Security header.

---

## DBI Integration Service

For linking accounting software with bank accounts.

### Setup
1. Contact banker to activate DBI
2. Receive username/password (temporary, must change on first login)
3. Get test digital certificate
4. Download WSDL/XSD: `https://tbcbank-developers.apigee.io/files/WSDL_XSD_and_Single_WSDL.zip`

### Test Endpoint
```
https://secdbitst.tbconline.ge/dbi/dbiService
```

### Required Files
- Username and password (from banker)
- Digital certificate: `SecDbiTst.tbconline.ge.pfx` + password
- Root certificate: `TBCRootCer.cer` (install locally)
