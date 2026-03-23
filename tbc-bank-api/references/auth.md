# TBC Bank Authentication

## Authorization Types

| Type | Use Case | Header/Method |
|------|----------|---------------|
| apikey | App identification for public APIs | `apikey: {key}` header |
| Client Credentials (OAuth 2.0) | Server-to-server, no user | POST token endpoint |
| Authorization Code (OAuth 2.0) | User-authorized access | Redirect + code exchange |
| Bearer Token | Access protected resources | `Authorization: Bearer {token}` |
| Certificate Based (mTLS) | PSD2 Open Banking | QWAC/QSEAL certificates |

---

## Checkout / TPay Access Token

```http
POST https://api.tbcbank.ge/v1/tpay/access-token
apikey: {your_api_key}
Content-Type: application/x-www-form-urlencoded

client_id={merchant_client_id}
&client_secret={merchant_client_secret}
```

**Response:**
```json
{
  "access_token": "eyJhbGci...",
  "token_type": "Bearer",
  "expires_in": 86400
}
```

> Error 400 usually means incorrect `client_id` or `client_secret`.

---

## Open Banking / PSD2 (mTLS + OAuth2)

### OpenID Configuration
```
GET https://api.tbcbank.ge/.well-known/openid-configuration
```

### User Authorization (Redirect SCA)
```
GET https://api.tbcbank.ge/psd2/openbanking/oauth/authorize
  ?response_type=code
  &client_id={tpp_client_id}
  &redirect_uri={your_callback}
  &scope=openid accounts payments
  &state={random_state}
```

### CIBA (Client Initiated Backchannel Auth)
```http
POST https://api.tbcbank.ge/psd2/openbanking/oauth/bc-authorization
```

### Token Exchange
```http
POST https://api.tbcbank.ge/openbanking/oauth/token
Content-Type: application/x-www-form-urlencoded
# mTLS certificate required

grant_type=authorization_code
&code={auth_code}
&redirect_uri={your_callback}
&client_id={tpp_client_id}
```

### JWKS for Token Verification
```
GET https://api.tbcbank.ge/.well-known/keys
```

### User Info
```http
GET https://api.tbcbank.ge/userinfo
Authorization: Bearer {access_token}
# mTLS certificate required
```

---

## Installments / Exchange Rates Token

```http
POST https://api.tbcbank.ge/openbanking/oauth/token
Content-Type: application/x-www-form-urlencoded
# mTLS certificate required for OAuth products

grant_type=client_credentials
&client_id={client_id}
&client_secret={client_secret}
```

---

## Certificates

**PSD2 APIs** (AIS, PIS) require certificates in both test and production.

**Open API Products** (Installments, E-commerce, Exchange Rates, Mortgage, TBC ID) require certificates for mTLS/OAuth 2.0.

### Getting Certificates

Email `developers@tbcbank.ge` with:
- Company name and ID code
- API product name
- Environment (test/production)

**For production:**
- Banks: Certificate from trusted provider licensed by National Bank
- FinTech/Microfinance: QWAC/QSeal from authorized provider

---

## DBI (Integration Service) Authentication

For direct bank integration (SOAP):
- Username + Password + Digital Certificate
- Test endpoint: `https://secdbitst.tbconline.ge/dbi/dbiService`
- Required files: username, password (temporary), digital certificate (.pfx), root certificate (.cer)

### SOAP Security Header (C#)
```csharp
[XmlRoot(Namespace = "http://docs.oasis-open.org/wss/2004/01/oasis-200401-wsswssecurity-secext-1.0.xsd")]
public partial class Security : SoapHeader {
    public UsernameToken UsernameToken { get; set; }
}
public partial class UsernameToken {
    public string Username { get; set; }
    public string Password { get; set; }
    public string Nonce { get; set; }
}
```
