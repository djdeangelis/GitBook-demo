# Handling Webhooks

Webhooks allow Evolve Payments to notify your application when events happen in your account, such as successful payments, failed charges, or created refunds. This guide explains how to receive and process webhook events securely.

## Why Use Webhooks?

Webhooks provide real-time notifications about asynchronous events. Instead of polling the API to check if something happened, webhooks push updates to your server as they occur.

Use webhooks to:
- Update order status when a payment succeeds
- Send confirmation emails to customers
- Track failed payment attempts
- Handle refund notifications
- Monitor subscription changes
- Reconcile your database with Evolve Payments

> **Important**: Webhooks are the recommended way to handle payment completion. Don't rely solely on client-side responses, as users may close their browser before the response is received.

## Setting Up Webhooks

### 1. Create an Endpoint

Create an HTTPS endpoint in your application that can receive POST requests. This endpoint should:
- Accept POST requests
- Verify the webhook signature
- Return a `200 OK` response quickly
- Process events asynchronously

Example endpoint in Node.js:

```javascript
const express = require('express');
const app = express();

app.post('/webhooks/evolve', express.raw({type: 'application/json'}), (req, res) => {
  const signature = req.headers['x-evolve-signature'];
  const payload = req.body;

  // Verify signature (see verification section below)
  if (!verifySignature(payload, signature)) {
    return res.status(400).send('Invalid signature');
  }

  const event = JSON.parse(payload);

  // Process event asynchronously
  processEvent(event);

  // Return 200 immediately
  res.status(200).send('Webhook received');
});

app.listen(3000);
```

### 2. Register the Endpoint

Add your webhook endpoint in the [Dashboard](https://dashboard.evolvepayments.com/webhooks):

1. Go to **Settings** → **Webhooks**
2. Click **Add endpoint**
3. Enter your endpoint URL (must be HTTPS)
4. Select the events you want to receive
5. Save the endpoint and note your webhook signing secret

> **Note**: For testing, you can use tools like [ngrok](https://ngrok.com) to create a public HTTPS URL that forwards to your local development server.

## Webhook Events

Evolve Payments sends webhooks for various events. Here are the most common ones:

### Payment Events

**`payment.succeeded`**
Sent when a payment is successfully processed.

```json
{
  "id": "evt_1234567890abcdef",
  "type": "payment.succeeded",
  "created": 1704067200,
  "data": {
    "object": {
      "id": "pay_1234567890abcdef",
      "object": "payment",
      "amount": 2000,
      "currency": "usd",
      "status": "succeeded",
      "payment_method": {
        "type": "card",
        "card": {
          "brand": "visa",
          "last4": "4242"
        }
      },
      "metadata": {
        "order_id": "order_123"
      }
    }
  }
}
```

**`payment.failed`**
Sent when a payment fails.

```json
{
  "id": "evt_abcdef1234567890",
  "type": "payment.failed",
  "created": 1704067200,
  "data": {
    "object": {
      "id": "pay_abcdef1234567890",
      "object": "payment",
      "amount": 2000,
      "currency": "usd",
      "status": "failed",
      "failure_code": "card_declined",
      "failure_message": "Your card was declined.",
      "metadata": {
        "order_id": "order_124"
      }
    }
  }
}
```

### Refund Events

**`refund.created`**
Sent when a refund is created.

```json
{
  "id": "evt_fedcba0987654321",
  "type": "refund.created",
  "created": 1704067200,
  "data": {
    "object": {
      "id": "ref_1234567890abcdef",
      "object": "refund",
      "amount": 2000,
      "currency": "usd",
      "payment": "pay_1234567890abcdef",
      "status": "succeeded",
      "reason": "requested_by_customer",
      "metadata": {
        "order_id": "order_123"
      }
    }
  }
}
```

**`refund.updated`**
Sent when a refund's status changes.

**`refund.failed`**
Sent when a refund fails.

### Other Events

- `customer.created` - New customer created
- `customer.updated` - Customer details updated
- `payment_method.attached` - Payment method saved to customer
- `payout.created` - Funds transferred to your bank account
- `payout.paid` - Payout successfully deposited

## Verifying Webhook Signatures

Always verify webhook signatures to ensure requests are from Evolve Payments and haven't been tampered with.

### How Signature Verification Works

Evolve Payments signs webhook payloads with your webhook signing secret using HMAC-SHA256. The signature is sent in the `X-Evolve-Signature` header.

### Verification Steps

1. Extract the signature from the `X-Evolve-Signature` header
2. Compute the expected signature using your signing secret
3. Compare the signatures using a constant-time comparison
4. Process the event only if signatures match

### Implementation Examples

**Node.js:**

```javascript
const crypto = require('crypto');

function verifySignature(payload, signature, secret) {
  const expectedSignature = crypto
    .createHmac('sha256', secret)
    .update(payload)
    .digest('hex');

  // Use constant-time comparison to prevent timing attacks
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(expectedSignature)
  );
}

app.post('/webhooks/evolve', express.raw({type: 'application/json'}), (req, res) => {
  const signature = req.headers['x-evolve-signature'];
  const secret = process.env.EVOLVE_WEBHOOK_SECRET;

  if (!verifySignature(req.body, signature, secret)) {
    console.error('Invalid signature');
    return res.status(400).send('Invalid signature');
  }

  const event = JSON.parse(req.body);
  // Process event...
  res.status(200).send('OK');
});
```

**Python:**

```python
import hmac
import hashlib

def verify_signature(payload, signature, secret):
    expected_signature = hmac.new(
        secret.encode('utf-8'),
        payload,
        hashlib.sha256
    ).hexdigest()

    return hmac.compare_digest(signature, expected_signature)

@app.route('/webhooks/evolve', methods=['POST'])
def webhook():
    signature = request.headers.get('X-Evolve-Signature')
    secret = os.environ.get('EVOLVE_WEBHOOK_SECRET')

    if not verify_signature(request.data, signature, secret):
        return 'Invalid signature', 400

    event = request.json
    # Process event...
    return 'OK', 200
```

> **Warning**: Always use constant-time comparison functions (`crypto.timingSafeEqual` in Node.js, `hmac.compare_digest` in Python) to prevent timing attacks.

## Processing Events

### Return 200 Quickly

Respond to webhooks quickly (within 5 seconds) to avoid timeouts. Process events asynchronously:

```javascript
app.post('/webhooks/evolve', async (req, res) => {
  const event = JSON.parse(req.body);

  // Return 200 immediately
  res.status(200).send('OK');

  // Process asynchronously
  processEventAsync(event);
});

async function processEventAsync(event) {
  try {
    switch (event.type) {
      case 'payment.succeeded':
        await handlePaymentSuccess(event.data.object);
        break;
      case 'payment.failed':
        await handlePaymentFailure(event.data.object);
        break;
      case 'refund.created':
        await handleRefund(event.data.object);
        break;
      default:
        console.log(`Unhandled event type: ${event.type}`);
    }
  } catch (error) {
    console.error(`Error processing webhook: ${error.message}`);
  }
}
```

### Handle Events Idempotently

Webhooks may be sent multiple times. Make your event processing idempotent by:

1. Storing processed event IDs
2. Checking if an event was already processed before handling it
3. Using database transactions for atomic operations

```javascript
async function processEventAsync(event) {
  // Check if already processed
  const exists = await db.events.findOne({ eventId: event.id });
  if (exists) {
    console.log(`Event ${event.id} already processed`);
    return;
  }

  // Process event and store ID atomically
  await db.transaction(async (trx) => {
    await processEvent(event, trx);
    await trx.events.insert({ eventId: event.id, processedAt: new Date() });
  });
}
```

### Handle Events in Order

Events are not guaranteed to arrive in order. Use the `created` timestamp if order matters:

```javascript
async function handlePaymentSuccess(payment) {
  const existing = await db.payments.findOne({ id: payment.id });

  // Only update if this event is newer
  if (!existing || payment.created > existing.created) {
    await db.payments.update({ id: payment.id }, payment);
  }
}
```

## Best Practices

### Monitor Webhook Health

- Set up monitoring for webhook failures
- Review failed webhook attempts in the Dashboard
- Set up alerts for repeated failures

### Use Event Types Strategically

Only subscribe to events you need. This reduces processing overhead and makes debugging easier.

### Store Raw Events

Consider storing the raw webhook payload for debugging:

```javascript
await db.webhookLogs.insert({
  eventId: event.id,
  type: event.type,
  payload: event,
  receivedAt: new Date()
});
```

### Test Webhook Handling

Test your webhook endpoint thoroughly:

1. Use the Dashboard's "Send test webhook" feature
2. Test with all event types you handle
3. Test signature verification with invalid signatures
4. Test idempotency by sending the same event twice
5. Test error handling and retries

### Secure Your Endpoint

- Always use HTTPS
- Verify signatures on every request
- Keep your signing secret secure
- Consider rate limiting
- Log suspicious requests

## Retries

If your endpoint returns a non-200 status code or times out, Evolve Payments will retry the webhook:

- Immediately
- After 1 minute
- After 5 minutes
- After 30 minutes
- After 2 hours
- After 6 hours

After 6 failed attempts, the webhook is marked as failed. You can manually retry failed webhooks from the Dashboard.

## Testing Webhooks

### Local Testing with ngrok

1. Install [ngrok](https://ngrok.com)
2. Start your local server (e.g., `node server.js`)
3. Create a tunnel: `ngrok http 3000`
4. Add the ngrok URL to your webhooks in the Dashboard
5. Test by creating payments in test mode

### Manual Testing

Send test webhooks from the Dashboard:

1. Go to **Settings** → **Webhooks**
2. Click on your endpoint
3. Click **Send test webhook**
4. Select an event type
5. Click **Send**

## Troubleshooting

### Webhooks Not Received

- Check that your endpoint URL is correct and publicly accessible
- Verify your server is running and listening on the correct port
- Check firewall settings
- Review webhook logs in the Dashboard

### Signature Verification Failing

- Ensure you're using the raw request body, not parsed JSON
- Verify you're using the correct signing secret
- Check that the secret hasn't been rotated
- Ensure you're using HMAC-SHA256

### Timeouts

- Return 200 response within 5 seconds
- Move processing to a background job
- Avoid long-running operations in the webhook handler

## Example: Complete Webhook Handler

Here's a production-ready webhook handler:

```javascript
const express = require('express');
const crypto = require('crypto');
const app = express();

const WEBHOOK_SECRET = process.env.EVOLVE_WEBHOOK_SECRET;

app.post('/webhooks/evolve',
  express.raw({type: 'application/json'}),
  async (req, res) => {
    const signature = req.headers['x-evolve-signature'];

    // Verify signature
    if (!verifySignature(req.body, signature, WEBHOOK_SECRET)) {
      console.error('Invalid webhook signature');
      return res.status(400).send('Invalid signature');
    }

    const event = JSON.parse(req.body);

    // Log event
    await logWebhook(event);

    // Return 200 immediately
    res.status(200).send('OK');

    // Process asynchronously
    processEventAsync(event).catch(err => {
      console.error(`Error processing webhook ${event.id}:`, err);
    });
  }
);

function verifySignature(payload, signature, secret) {
  const expectedSignature = crypto
    .createHmac('sha256', secret)
    .update(payload)
    .digest('hex');

  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(expectedSignature)
  );
}

async function processEventAsync(event) {
  // Check for duplicate
  const processed = await db.isEventProcessed(event.id);
  if (processed) return;

  // Handle event
  switch (event.type) {
    case 'payment.succeeded':
      await handlePaymentSuccess(event.data.object);
      break;
    case 'payment.failed':
      await handlePaymentFailure(event.data.object);
      break;
    case 'refund.created':
      await handleRefund(event.data.object);
      break;
  }

  // Mark as processed
  await db.markEventProcessed(event.id);
}

async function handlePaymentSuccess(payment) {
  await db.orders.update(
    { id: payment.metadata.order_id },
    { status: 'paid', paymentId: payment.id }
  );
  await emailService.sendConfirmation(payment);
}

app.listen(3000, () => console.log('Server running on port 3000'));
```

## Next Steps

- Learn about [testing payments](testing-payments.md) to simulate webhook events
- Review [error handling](error-handling.md) for robust webhook processing
- Check the [API Reference](../api-reference/overview.md) for complete event schemas
