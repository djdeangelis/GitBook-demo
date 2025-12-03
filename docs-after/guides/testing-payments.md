# Testing Payments

Testing is crucial for building a reliable payment integration. Evolve Payments provides a complete test environment where you can simulate payments, refunds, and various payment scenarios without charging real cards.

## Test Mode vs Live Mode

Evolve Payments operates in two distinct modes:

### Test Mode
- Uses API keys prefixed with `ek_test_`
- Never charges real credit cards
- Perfect for development and testing
- Isolated from live data
- Free to use extensively

### Live Mode
- Uses API keys prefixed with `ek_live_`
- Processes real payments and charges real cards
- Used in production
- Requires completed account verification

> **Important**: Always start with test mode. Only switch to live mode when you're ready to accept real payments.

## Test API Keys

Get your test API keys from the [Dashboard](https://dashboard.evolvepayments.com/api-keys). Test keys are available immediately when you create an account.

Example test key:
```
ek_test_abc123def456ghi789jkl012mno345
```

Use test keys exactly like live keys:

```bash
curl https://api.evolvepayments.com/v1/payments \
  -H "Authorization: Bearer ek_test_abc123def456ghi789jkl012mno345" \
  -H "Content-Type: application/json" \
  -d '{...}'
```

## Test Card Numbers

Use these test card numbers to simulate different payment scenarios. These cards only work in test mode and will be declined in live mode.

### Successful Payments

**Visa - Basic Success**
```
Number: 4242 4242 4242 4242
CVC: Any 3 digits
Date: Any future date
```
This card always succeeds with no additional authentication required.

**Visa - 3D Secure Required**
```
Number: 4000 0027 6000 3184
CVC: Any 3 digits
Date: Any future date
```
This card requires 3D Secure authentication. In test mode, authentication will automatically succeed.

**Mastercard**
```
Number: 5555 5555 5555 4444
CVC: Any 3 digits
Date: Any future date
```

**American Express**
```
Number: 3782 822463 10005
CVC: Any 4 digits
Date: Any future date
```

**Discover**
```
Number: 6011 1111 1111 1117
CVC: Any 3 digits
Date: Any future date
```

### Card Declines

**Generic Decline**
```
Number: 4000 0000 0000 0002
CVC: Any 3 digits
Date: Any future date
```
The card will be declined with a generic decline code.

**Insufficient Funds**
```
Number: 4000 0000 0000 9995
CVC: Any 3 digits
Date: Any future date
```
The card will be declined due to insufficient funds.

**Lost Card**
```
Number: 4000 0000 0000 9987
CVC: Any 3 digits
Date: Any future date
```
The card will be declined as lost.

**Stolen Card**
```
Number: 4000 0000 0000 9979
CVC: Any 3 digits
Date: Any future date
```
The card will be declined as stolen.

**Expired Card**
```
Number: 4000 0000 0000 0069
CVC: Any 3 digits
Date: Any future date
```
The card will be declined as expired (even with a future expiration date).

**Incorrect CVC**
```
Number: 4000 0000 0000 0127
CVC: Any 3 digits
Date: Any future date
```
The card will be declined due to incorrect CVC.

**Processing Error**
```
Number: 4000 0000 0000 0119
CVC: Any 3 digits
Date: Any future date
```
The card will fail with a processing error.

### Fraud Prevention

**Card Triggers Radar Fraud Block**
```
Number: 4100 0000 0000 0019
CVC: Any 3 digits
Date: Any future date
```
This payment will be blocked by Evolve's fraud prevention system.

## Testing Scenarios

### Basic Payment Flow

Test a successful payment:

```bash
curl https://api.evolvepayments.com/v1/payments \
  -H "Authorization: Bearer ek_test_abc123def456ghi789jkl012mno345" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 2000,
    "currency": "usd",
    "payment_method": {
      "type": "card",
      "card": {
        "number": "4242424242424242",
        "exp_month": 12,
        "exp_year": 2025,
        "cvc": "123"
      }
    },
    "description": "Test payment"
  }'
```

### Testing Declined Payments

Test how your application handles declined payments:

```bash
curl https://api.evolvepayments.com/v1/payments \
  -H "Authorization: Bearer ek_test_abc123def456ghi789jkl012mno345" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 2000,
    "currency": "usd",
    "payment_method": {
      "type": "card",
      "card": {
        "number": "4000000000000002",
        "exp_month": 12,
        "exp_year": 2025,
        "cvc": "123"
      }
    }
  }'
```

Response:
```json
{
  "error": {
    "type": "card_error",
    "code": "card_declined",
    "message": "Your card was declined.",
    "decline_code": "generic_decline",
    "payment_id": "pay_failed_1234567890"
  }
}
```

### Testing Refunds

Create a successful payment, then refund it:

```bash
# 1. Create a payment
PAYMENT_ID=$(curl -s https://api.evolvepayments.com/v1/payments \
  -H "Authorization: Bearer ek_test_abc123def456ghi789jkl012mno345" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 2000,
    "currency": "usd",
    "payment_method": {
      "type": "card",
      "card": {
        "number": "4242424242424242",
        "exp_month": 12,
        "exp_year": 2025,
        "cvc": "123"
      }
    }
  }' | jq -r '.id')

# 2. Refund the payment
curl https://api.evolvepayments.com/v1/refunds \
  -H "Authorization: Bearer ek_test_abc123def456ghi789jkl012mno345" \
  -H "Content-Type: application/json" \
  -d "{
    \"payment\": \"$PAYMENT_ID\",
    \"amount\": 2000,
    \"reason\": \"requested_by_customer\"
  }"
```

### Testing Partial Refunds

Refund only part of a payment:

```bash
curl https://api.evolvepayments.com/v1/refunds \
  -H "Authorization: Bearer ek_test_abc123def456ghi789jkl012mno345" \
  -H "Content-Type: application/json" \
  -d '{
    "payment": "pay_1234567890abcdef",
    "amount": 500,
    "reason": "requested_by_customer"
  }'
```

### Testing Webhooks

Trigger webhook events by performing actions in test mode:

1. Create a payment → triggers `payment.succeeded` or `payment.failed`
2. Create a refund → triggers `refund.created`
3. Update a customer → triggers `customer.updated`

You can also send test webhooks manually from the Dashboard:
- Go to **Settings** → **Webhooks**
- Select your endpoint
- Click **Send test webhook**
- Choose the event type

### Testing International Payments

Test with different currencies:

```bash
# EUR payment
curl https://api.evolvepayments.com/v1/payments \
  -H "Authorization: Bearer ek_test_abc123def456ghi789jkl012mno345" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 1500,
    "currency": "eur",
    "payment_method": {
      "type": "card",
      "card": {
        "number": "4242424242424242",
        "exp_month": 12,
        "exp_year": 2025,
        "cvc": "123"
      }
    }
  }'
```

Supported test currencies: USD, EUR, GBP, CAD, AUD, JPY, and more.

### Testing Different Amounts

Certain amounts trigger specific behaviors in test mode:

| Amount | Behavior |
|--------|----------|
| Any amount with card `4242424242424242` | Succeeds |
| Any amount with card `4000000000000002` | Declines |
| Any amount with card `4000000000009995` | Insufficient funds |

### Testing Idempotency

Test that duplicate requests with the same idempotency key return the same result:

```bash
# First request
curl https://api.evolvepayments.com/v1/payments \
  -H "Authorization: Bearer ek_test_abc123def456ghi789jkl012mno345" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: test_idempotency_123" \
  -d '{
    "amount": 2000,
    "currency": "usd",
    "payment_method": {
      "type": "card",
      "card": {
        "number": "4242424242424242",
        "exp_month": 12,
        "exp_year": 2025,
        "cvc": "123"
      }
    }
  }'

# Second request with same key - returns same payment, doesn't create duplicate
curl https://api.evolvepayments.com/v1/payments \
  -H "Authorization: Bearer ek_test_abc123def456ghi789jkl012mno345" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: test_idempotency_123" \
  -d '{
    "amount": 2000,
    "currency": "usd",
    "payment_method": {
      "type": "card",
      "card": {
        "number": "4242424242424242",
        "exp_month": 12,
        "exp_year": 2025,
        "cvc": "123"
      }
    }
  }'
```

## Testing Checklist

Before going live, make sure you've tested:

- [ ] Successful payment with `4242424242424242`
- [ ] Declined payment with `4000000000000002`
- [ ] Insufficient funds with `4000000000009995`
- [ ] Different card brands (Visa, Mastercard, Amex)
- [ ] Different currencies (USD, EUR, GBP)
- [ ] Full refunds
- [ ] Partial refunds
- [ ] Webhook receipt and verification
- [ ] Idempotency key handling
- [ ] Error handling for all scenarios
- [ ] Network timeout handling
- [ ] Concurrent payment requests
- [ ] Payment method reuse (saved cards)

## Viewing Test Data

All test mode activity is visible in your Dashboard:

1. Log in to [Dashboard](https://dashboard.evolvepayments.com)
2. Toggle to **Test Mode** using the switch in the top-right
3. View test payments, refunds, and customers
4. Review webhook delivery attempts
5. Search and filter test data

> **Note**: Test data is completely separate from live data. Switching between test and live mode in the Dashboard shows different data sets.

## Clearing Test Data

You can clear all test data at any time:

1. Go to **Settings** → **Account**
2. Scroll to **Clear test data**
3. Click **Clear all test data**
4. Confirm the action

This removes all test payments, customers, and related data. Live data is never affected.

## Testing Best Practices

### Test Early and Often
Integrate testing into your development workflow. Run tests with every significant change.

### Automate Testing
Create automated tests for your payment integration:

```javascript
// Example with Jest
describe('Payment Integration', () => {
  it('should create a successful payment', async () => {
    const payment = await createPayment({
      amount: 2000,
      currency: 'usd',
      card: {
        number: '4242424242424242',
        exp_month: 12,
        exp_year: 2025,
        cvc: '123'
      }
    });

    expect(payment.status).toBe('succeeded');
    expect(payment.amount).toBe(2000);
  });

  it('should handle declined payments', async () => {
    await expect(
      createPayment({
        amount: 2000,
        currency: 'usd',
        card: {
          number: '4000000000000002',
          exp_month: 12,
          exp_year: 2025,
          cvc: '123'
        }
      })
    ).rejects.toThrow('card_declined');
  });
});
```

### Test Edge Cases
Don't just test the happy path:
- Network failures
- Partial responses
- Webhook retries
- Concurrent requests
- Very large amounts
- Very small amounts (minimum is 50 cents USD)

### Test User Experience
Test how your UI handles:
- Loading states during payment processing
- Error messages for declined cards
- Success confirmations
- Webhook delays (webhooks may arrive after the API response)

### Monitor Test Mode Usage
Review your test mode activity regularly:
- Check for failed payments
- Review error patterns
- Verify webhook delivery
- Ensure tests are comprehensive

## Moving to Production

When you're ready to go live:

1. **Complete testing checklist** - Verify all scenarios work
2. **Complete account verification** - Submit required information in the Dashboard
3. **Switch to live keys** - Replace `ek_test_` with `ek_live_` in your environment
4. **Deploy to production** - Deploy your tested integration
5. **Monitor closely** - Watch your first live payments carefully
6. **Keep testing** - Continue using test mode for new features

> **Warning**: Double-check that you've updated to live keys. Using test keys in production means no real payments will be processed.

## Common Testing Mistakes

### Using Test Cards in Live Mode
Test cards like `4242424242424242` only work in test mode. They will be declined in live mode.

### Using Live Keys in Development
Never use live keys in development. You risk accidentally charging real cards.

### Not Testing Webhooks
Many developers only test the API response and forget to test webhook handling. Always test webhooks.

### Testing Only Success Cases
Test failures, declines, and errors just as thoroughly as successful payments.

### Not Testing Idempotency
Network issues can cause duplicate requests. Always test idempotency key handling.

## Need Help?

- Review our [error handling guide](error-handling.md) for handling failures
- Check the [webhook guide](handling-webhooks.md) for testing webhooks
- See our [FAQ](../support/faq.md) for common questions
- Email support@evolvepayments.com for assistance
