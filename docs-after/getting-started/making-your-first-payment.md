# Making Your First Payment

This guide walks you through creating your first payment with Evolve Payments. We'll process a test payment using our API to help you understand the complete payment flow.

## Prerequisites

Before you begin, make sure you have:
- An Evolve Payments account ([sign up here](https://dashboard.evolvepayments.com/signup))
- Your test API key (found in your [Dashboard](https://dashboard.evolvepayments.com/api-keys))
- A terminal or API client like curl, Postman, or Insomnia

## Overview

Creating a payment involves:
1. Creating a payment intent with amount and currency
2. Providing payment method details (card information)
3. Confirming the payment
4. Handling the response

## Step 1: Create a Payment

Let's create a payment for $20.00 USD. Note that amounts are specified in cents, so $20.00 is `2000` cents.

```bash
curl https://api.evolvepayments.com/v1/payments \
  -H "Authorization: Bearer ek_test_abc123def456ghi789" \
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
    "description": "My first payment",
    "metadata": {
      "order_id": "order_123",
      "customer_name": "John Doe"
    }
  }'
```

### Request Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `amount` | integer | Yes | Amount in cents (e.g., 2000 = $20.00) |
| `currency` | string | Yes | Three-letter ISO currency code (e.g., "usd") |
| `payment_method` | object | Yes | Payment method details |
| `description` | string | No | Description of the payment |
| `metadata` | object | No | Additional data (up to 50 keys) |

> **Note**: For this test, we're using the test card number `4242424242424242`, which always succeeds. See our [testing guide](../guides/testing-payments.md) for more test cards.

## Step 2: Handle the Response

If successful, you'll receive a response like this:

```json
{
  "id": "pay_1234567890abcdef",
  "object": "payment",
  "amount": 2000,
  "currency": "usd",
  "status": "succeeded",
  "payment_method": {
    "id": "pm_9876543210fedcba",
    "type": "card",
    "card": {
      "brand": "visa",
      "last4": "4242",
      "exp_month": 12,
      "exp_year": 2025
    }
  },
  "description": "My first payment",
  "metadata": {
    "order_id": "order_123",
    "customer_name": "John Doe"
  },
  "created": 1704067200,
  "receipt_url": "https://dashboard.evolvepayments.com/receipts/pay_1234567890abcdef"
}
```

### Response Fields

| Field | Description |
|-------|-------------|
| `id` | Unique identifier for the payment |
| `status` | Payment status (succeeded, pending, failed) |
| `amount` | Amount charged in cents |
| `currency` | Currency code |
| `payment_method` | Payment method used (card details are partially masked) |
| `created` | Unix timestamp of when payment was created |
| `receipt_url` | URL to view/download the receipt |

## Step 3: Verify the Payment

The `status` field tells you the payment outcome:

- **`succeeded`**: Payment completed successfully. The funds will be included in your next payout.
- **`pending`**: Payment requires additional action (e.g., 3D Secure authentication).
- **`failed`**: Payment was declined. Check the `failure_code` and `failure_message` fields for details.

## What Happens Next?

Once a payment succeeds:

1. **Funds are secured**: The amount is captured from the customer's card
2. **Webhooks are sent**: You'll receive a `payment.succeeded` webhook event
3. **Receipt is generated**: A receipt URL is created for your customer
4. **Payout is scheduled**: Funds are included in your next automatic payout

## Checking Payment Status

You can retrieve a payment at any time using its ID:

```bash
curl https://api.evolvepayments.com/v1/payments/pay_1234567890abcdef \
  -H "Authorization: Bearer ek_test_abc123def456ghi789"
```

## Common Use Cases

### One-Time Payment

The example above demonstrates a simple one-time payment. This is perfect for:
- E-commerce checkouts
- Service bookings
- Digital downloads
- Event tickets

### Saving a Payment Method

To charge a customer later without asking for card details again:

```bash
curl https://api.evolvepayments.com/v1/customers \
  -H "Authorization: Bearer ek_test_abc123def456ghi789" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "customer@example.com",
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

Then charge the saved payment method:

```bash
curl https://api.evolvepayments.com/v1/payments \
  -H "Authorization: Bearer ek_test_abc123def456ghi789" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 2000,
    "currency": "usd",
    "customer": "cus_abc123",
    "payment_method": "pm_xyz789",
    "description": "Subscription renewal"
  }'
```

### International Payments

Accept payments in different currencies:

```bash
curl https://api.evolvepayments.com/v1/payments \
  -H "Authorization: Bearer ek_test_abc123def456ghi789" \
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
    },
    "description": "Payment from EU customer"
  }'
```

## Best Practices

### Always Use HTTPS
Never send card details over unencrypted connections. All requests must use HTTPS.

### Handle Errors Gracefully
Payments can fail for many reasons. Always check the status and handle errors appropriately. See our [error handling guide](../guides/error-handling.md).

### Use Idempotency Keys
Prevent duplicate charges by including an idempotency key:

```bash
curl https://api.evolvepayments.com/v1/payments \
  -H "Authorization: Bearer ek_test_abc123def456ghi789" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: order_123_payment_attempt_1" \
  -d '{
    "amount": 2000,
    "currency": "usd",
    "payment_method": {...}
  }'
```

If the request is retried with the same idempotency key within 24 hours, the API will return the original response without creating a duplicate payment.

### Store Payment IDs
Always store the payment ID in your database. You'll need it to:
- Issue refunds
- Check payment status
- Reconcile payments with payouts
- Provide customer support

### Use Metadata
Store your internal IDs in the metadata field:

```json
{
  "metadata": {
    "order_id": "order_12345",
    "customer_id": "cust_67890",
    "product_sku": "WIDGET-001"
  }
}
```

This makes it easier to reconcile payments with your system and appears in webhook events.

## Testing Your Integration

Before going live:

1. Test successful payments with `4242424242424242`
2. Test declined payments with `4000000000000002`
3. Test different currencies
4. Verify webhook handling (see [handling webhooks](../guides/handling-webhooks.md))
5. Test error scenarios (expired cards, insufficient funds)

> **Tip**: Use test mode extensively. Test API keys never charge real cards, so you can test freely without risk.

## Going Live

When you're ready to accept real payments:

1. Complete account verification in the Dashboard
2. Replace your test API key with your live API key
3. Update your environment variables
4. Deploy and monitor your first live payments
5. Set up webhook endpoints in production

## Next Steps

- [Learn about webhooks](../guides/handling-webhooks.md) for real-time payment notifications
- [Explore testing scenarios](../guides/testing-payments.md) to build a robust integration
- [Understand error handling](../guides/error-handling.md) for production readiness
- [Browse the API Reference](../api-reference/overview.md) for advanced features

## Need Help?

If you run into issues:
- Check our [FAQ](../support/faq.md) for common questions
- Email us at support@evolvepayments.com
- Visit our [developer community](https://community.evolvepayments.com)
