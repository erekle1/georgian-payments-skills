# TBC Merchant Integration Guide

What the **merchant must build and register** to integrate TBC payment services.

---

## 1. Callback / Webhook Handling

### Checkout Callback

TBC POSTs to your `callbackUrl` when payment reaches final status:

```json
{ "PaymentId": "6azzo13uw5u2116332" }
```

**Your handler must:**
1. Return HTTP 200 immediately
2. Call `GET /v1/tpay/payments/{PaymentId}` to get full payment details
3. Verify amount and status before fulfilling order
4. Be idempotent — same callback may arrive multiple times

```javascript
app.post('/webhooks/tbc', express.json(), async (req, res) => {
  res.sendStatus(200); // Acknowledge immediately

  const { PaymentId } = req.body;

  // Idempotency check
  if (await db.isProcessed(PaymentId)) return;

  // Get full payment details from TBC
  const payment = await tbcGetPaymentDetails(PaymentId);

  // Verify against your order
  const order = await db.getOrderByPaymentId(PaymentId);
  if (payment.status === 'Succeeded' && payment.amount === order.amount) {
    await fulfillOrder(order.id);
    await db.markProcessed(PaymentId);
  }
});
```

### Callback IP Whitelist

Only accept webhooks from:
- `193.104.20.44`
- `193.104.20.45`
- `185.52.80.44`
- `185.52.80.45`

### Callback Setup

Dashboard: `ecom.tbcpayments.ge` → Website Management → Callback field
> Activated same day before 6PM.

---

## 2. Merchant Dashboard & Credentials

### Access
- URL: `https://ecom.tbcpayments.ge`
- Login: Company director's internet banking username/password

### Get Credentials
Navigate to: Website Management → Details
- **Client ID** and **Client Secret** (do not disclose)
- **apikey** for API identification

---

## 3. Environment Configuration

```env
# TBC Checkout Credentials
TBC_API_KEY=your_api_key
TBC_CLIENT_ID=your_client_id
TBC_CLIENT_SECRET=your_client_secret

# Environment
TBC_ENV=sandbox  # or: production
TBC_BASE_URL=https://test-api.tbcbank.ge  # or: https://api.tbcbank.ge

# Your URLs
TBC_RETURN_URL=https://yourshop.com/payment/return
TBC_CALLBACK_URL=https://yourshop.com/webhooks/tbc

# Installments (if applicable)
TBC_MERCHANT_KEY=your_merchant_key
TBC_CAMPAIGN_ID=your_campaign_id
```

### Base URLs

```python
TBC_URLS = {
    "sandbox": "https://test-api.tbcbank.ge",
    "production": "https://api.tbcbank.ge",
    "dev_openbanking": "https://dev-openbanking.tbcbank.ge",
    "openbanking": "https://openbanking.tbcbank.ge"
}
```

---

## 4. Go-Live Requirements

### Checkout (E-Commerce)

Required on your website before activation:
1. Terms and Conditions
2. Return Policy
3. Privacy Policy
4. Delivery Policy
5. Contact Information (physical address)

**Process:** Dashboard (`ecom.tbcpayments.ge`) → Website Management → "რეალური გარემოს გააქტიურება" (Activate Real Environment)

### Installments

Email `merchant.support@tbcbank.ge` for production `merchantKey` and `campaignId`.

### Exchange Rates

Automatically available after testing with status 200.

---

## 5. Testing Checklist

- [ ] Sandbox credentials obtained from `ecom.tbcpayments.ge`
- [ ] Access token generation works
- [ ] Full checkout flow tested end-to-end
- [ ] Callback handler receives and processes webhooks
- [ ] Idempotency verified (send same callback twice)
- [ ] Payment cancellation/refund tested
- [ ] Pre-authorization flow tested (if used)
- [ ] Recurring payment tested (if used)
- [ ] QR payment tested (if used)
- [ ] Installment flow tested (if used) — send sessionIds to `developers@tbcbank.ge`
- [ ] All URLs updated to production before go-live
- [ ] Production credentials configured

---

## 6. Payment Lifecycle

```
[Created] → [Processing] → [Succeeded] → [Returned / PartialReturned]
                           ↓
                         [Failed]
                           ↓
                        [Expired]

Pre-auth: [Created] → [WaitingConfirm] → [Succeeded] (after completion)
```

**Status mapping:**

| TBC Status | Your Order Status | Action |
|-----------|------------------|--------|
| `Created` | `payment_pending` | Show "processing" |
| `Processing` | `payment_processing` | Wait for callback |
| `Succeeded` | `paid` | Fulfill order |
| `Failed` | `payment_failed` | Notify customer |
| `Expired` | `cancelled` | Release inventory |
| `WaitingConfirm` | `awaiting_capture` | Complete pre-auth |
| `Returned` | `refunded` | Process return |

---

## 7. Mortgage Leads (Optional)

```http
POST /v1/online-mortgages/leads
Authorization: Bearer {token}
```

Create mortgage application leads for customers.

```http
POST /v1/online-mortgages/short-leads
Authorization: Bearer {token}
```

Simplified lead creation with minimal info.
