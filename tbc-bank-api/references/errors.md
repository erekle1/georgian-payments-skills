# TBC Error Handling

## REST API Error Structure

```json
{
  "status": 400,
  "type": "https://api.tbcbank.ge/problems/bad-request",
  "title": "Bad Request",
  "detail": "Missing required parameter: amount",
  "systemCode": "TBC-ERR-001",
  "code": "INVALID_REQUEST"
}
```

---

## HTTP Status Codes

### 4xx Client Errors

| Code | Meaning | Common Causes |
|------|---------|---------------|
| 400 | Bad Request | Missing params, wrong types, invalid format (IBAN=22 chars), bad dates |
| 401 | Unauthorized | Invalid/expired token, wrong apikey, bad Basic auth, missing OAuth scopes |
| 403 | Forbidden | No authorization for resource, insufficient rights, wrong scopes |
| 404 | Not Found | Wrong URL, resource doesn't exist |
| 405 | Method Not Allowed | Wrong HTTP method (e.g., GET instead of POST) |
| 409 | Conflict | Entity state changed by another process |
| 413 | Payload Too Large | Response too large — reduce filter params |
| 415 | Unsupported Media Type | Wrong Content-Type header |
| 429 | Too Many Requests | Rate limited — back off and retry |

### 5xx Server Errors

| Code | Meaning | Action |
|------|---------|--------|
| 500 | Internal Server Error | Contact TBC support |
| 503 | Service Unavailable | Temporary — check `Retry-After` header |

---

## SOAP Fault Codes (DBI Service)

| Code | Meaning |
|------|---------|
| `GENERAL_ERROR` | General server error |
| `INCORRECT_INPUT_DATA` | Invalid input parameters |
| `USER_IS_NOT_ACTIVE` | DBI user deactivated |
| `USER_IS_BLOCKED` | User account blocked |
| `CREDENTIALS_MUST_BE_CHANGED` | Password change required |
| `SECURITY_POLICIES_NOT_MET` | Security policy violation |
| `INCORRECT_CREDENTIALS` | Wrong username/password |
| `OTP_FAILED` | OTP verification failed |
| `NOT_AUTHORIZED` | Not authorized for operation |
| `PAYMENT_REQUEST_NOT_FOUND` | Payment not found |

---

## Checkout Result Codes

| Code | Description |
|------|-------------|
| `approved` | Transaction approved |
| `decline_general` | General decline |
| `decline_expired_card` | Card expired |
| `decline_suspected_fraud` | Suspected fraud |
| `decline_restricted_card` | Card in restricted list |
| `decline_invalid_card_number` | Invalid card number |
| `decline_not_sufficient_funds` | Insufficient funds |
| `decline_card_not_effective` | Card not active |
| `check_with_acquirer` | Details need verification |

---

## Troubleshooting Quick Reference

| Symptom | Check |
|---------|-------|
| 400 on create payment | Verify all required fields, amount format, currency code |
| 401 on any endpoint | Regenerate access token, verify apikey header |
| 404 on payment status | Confirm payId is correct and payment exists |
| Callback not received | Check IP whitelist (193.104.20.44/45, 185.52.80.44/45), verify callback URL accessible |
| Token expired | Tokens last 86400s (1 day) — refresh before expiry |
