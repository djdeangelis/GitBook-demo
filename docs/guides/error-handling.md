# Error Handling

Robust error handling is essential for building reliable payment integrations. This guide explains the errors you might encounter when using the Evolve Payments API and how to handle them gracefully.

## Error Response Format

When an error occurs, the API returns a JSON response with an `error` object:

```json
{
  "error": {
    "type": "card_error",
    "code": "card_declined",
    "message": "Your card was declined.",
    "decline_code": "insufficient_funds",
    "param": "payment_method",
    "payment_id": "pay_1234567890abcdef"
  }
}
```

### Error Object Fields

| Field | Description |
|-------|-------------|
| `type` | The category of error (see error types below) |
| `code` | Specific error code for programmatic handling |
| `message` | Human-readable error message |
| `param` | The parameter that caused the error (if applicable) |
| `decline_code` | Reason for card decline (for card_error type) |
| `payment_id` | ID of the failed payment (if applicable) |

## HTTP Status Codes

Evolve Payments uses standard HTTP status codes:

| Status Code | Meaning |
|-------------|---------|
| `200` | Success |
| `400` | Bad Request - Invalid parameters |
| `401` | Unauthorized - Invalid API key |
| `402` | Request Failed - Valid request but failed (e.g., card declined) |
| `403` | Forbidden - Valid API key but insufficient permissions |
| `404` | Not Found - Resource doesn't exist |
| `429` | Too Many Requests - Rate limit exceeded |
| `500` | Internal Server Error - Something went wrong on our end |
| `503` | Service Unavailable - Temporary server issue |

## Error Types

### API Errors

**`api_error`**
An error occurred on Evolve's servers. These are rare and usually temporary.

```json
{
  "error": {
    "type": "api_error",
    "code": "internal_error",
    "message": "An internal error occurred. Please try again."
  }
}
```

**How to handle:**
- Retry the request with exponential backoff
- If the error persists, contact support with the request ID

### Authentication Errors

**`authentication_error`**
The API key is invalid, missing, or expired.

```json
{
  "error": {
    "type": "authentication_error",
    "code": "invalid_api_key",
    "message": "Invalid API key provided."
  }
}
```

**Common causes:**
- Incorrect API key
- Deleted or rotated API key
- Missing Authorization header
- Using test key with live mode data (or vice versa)

**How to handle:**
- Verify your API key is correct
- Check that the Authorization header is properly formatted
- Ensure you're using the right key type (test vs live)

### Card Errors

**`card_error`**
The card was declined or is invalid.

```json
{
  "error": {
    "type": "card_error",
    "code": "card_declined",
    "message": "Your card was declined.",
    "decline_code": "insufficient_funds"
  }
}
```

**Common decline codes:**

| Decline Code | Description | User Action |
|--------------|-------------|-------------|
| `insufficient_funds` | Not enough funds | Try a different card |
| `lost_card` | Card reported lost | Contact card issuer |
| `stolen_card` | Card reported stolen | Contact card issuer |
| `expired_card` | Card has expired | Use a different card |
| `incorrect_cvc` | Invalid CVC code | Re-enter CVC |
| `processing_error` | Temporary processing issue | Retry or use different card |
| `card_not_supported` | Card type not supported | Use a different card |
| `generic_decline` | Declined for unspecified reason | Try different card or contact issuer |

**How to handle:**
- Display a clear, user-friendly error message
- Suggest specific actions based on decline code
- Never retry card_declined errors automatically
- Allow user to try a different payment method

### Invalid Request Errors

**`invalid_request_error`**
The request has invalid parameters.

```json
{
  "error": {
    "type": "invalid_request_error",
    "code": "parameter_invalid",
    "message": "Amount must be at least 50 cents.",
    "param": "amount"
  }
}
```

**Common codes:**
- `parameter_missing` - Required parameter not provided
- `parameter_invalid` - Parameter value is invalid
- `parameter_unknown` - Unknown parameter sent

**How to handle:**
- Check the `param` field to identify the problematic parameter
- Validate input before sending to the API
- Review API documentation for parameter requirements

### Rate Limit Errors

**`rate_limit_error`**
Too many requests in a short period.

```json
{
  "error": {
    "type": "rate_limit_error",
    "code": "rate_limit_exceeded",
    "message": "Too many requests. Please slow down."
  }
}
```

**Rate limits:**
- Test mode: 100 requests per second
- Live mode: 100 requests per second per API key

**How to handle:**
- Implement exponential backoff
- Check the `Retry-After` header for wait time
- Batch requests when possible
- Consider request queuing

### Validation Errors

**`validation_error`**
The request contains data that failed validation.

```json
{
  "error": {
    "type": "validation_error",
    "code": "invalid_card_number",
    "message": "The card number is invalid.",
    "param": "payment_method.card.number"
  }
}
```

**How to handle:**
- Validate card numbers using the Luhn algorithm client-side
- Validate expiration dates before submitting
- Check field formats and requirements
- Display clear validation messages to users

## Handling Specific Scenarios

### Declined Payments

Show clear, actionable messages to users:

```javascript
function handlePaymentError(error) {
  if (error.type === 'card_error') {
    switch (error.decline_code) {
      case 'insufficient_funds':
        return 'Your card has insufficient funds. Please try a different card.';
      case 'expired_card':
        return 'Your card has expired. Please use a different card.';
      case 'incorrect_cvc':
        return 'The security code (CVC) is incorrect. Please check and try again.';
      case 'lost_card':
      case 'stolen_card':
        return 'This card cannot be used. Please contact your bank or use a different card.';
      default:
        return 'Your card was declined. Please try a different payment method or contact your bank.';
    }
  }
  return 'An error occurred. Please try again.';
}
```

### Network Errors

Handle network timeouts and connection issues:

```javascript
async function createPaymentWithRetry(paymentData, maxRetries = 3) {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      const response = await fetch('https://api.evolvepayments.com/v1/payments', {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${apiKey}`,
          'Content-Type': 'application/json',
          'Idempotency-Key': paymentData.idempotencyKey
        },
        body: JSON.stringify(paymentData),
        timeout: 30000 // 30 second timeout
      });

      if (!response.ok) {
        const error = await response.json();
        throw new PaymentError(error);
      }

      return await response.json();

    } catch (error) {
      if (error.code === 'ETIMEDOUT' || error.code === 'ECONNREFUSED') {
        if (attempt === maxRetries) {
          throw new Error('Unable to connect to payment processor. Please try again later.');
        }
        // Exponential backoff: 1s, 2s, 4s
        await sleep(Math.pow(2, attempt - 1) * 1000);
        continue;
      }
      throw error;
    }
  }
}
```

### API Errors

Handle temporary server errors:

```javascript
async function handleApiError(error, paymentData) {
  if (error.type === 'api_error' || error.type === 'rate_limit_error') {
    // Retry with exponential backoff
    const maxRetries = 3;
    for (let i = 0; i < maxRetries; i++) {
      await sleep(Math.pow(2, i) * 1000);
      try {
        return await createPayment(paymentData);
      } catch (retryError) {
        if (i === maxRetries - 1) {
          throw new Error('Payment service temporarily unavailable. Please try again in a few moments.');
        }
      }
    }
  }
  throw error;
}
```

## Retry Logic

### When to Retry

**DO retry:**
- `api_error` - Internal server errors (with exponential backoff)
- `rate_limit_error` - Rate limit exceeded (use Retry-After header)
- Network timeouts and connection errors
- `500` and `503` status codes

**DO NOT retry:**
- `card_error` - Card declines (requires user action)
- `authentication_error` - Invalid API key
- `invalid_request_error` - Bad parameters
- `400` and `404` status codes

### Implementing Exponential Backoff

```javascript
async function retryWithBackoff(fn, maxRetries = 3, baseDelay = 1000) {
  for (let attempt = 1; attempt <= maxRetries; attempt++) {
    try {
      return await fn();
    } catch (error) {
      // Don't retry client errors
      if (error.type === 'card_error' ||
          error.type === 'invalid_request_error' ||
          error.type === 'authentication_error') {
        throw error;
      }

      // Last attempt - throw error
      if (attempt === maxRetries) {
        throw error;
      }

      // Calculate delay with jitter
      const delay = baseDelay * Math.pow(2, attempt - 1);
      const jitter = Math.random() * 1000;
      await sleep(delay + jitter);

      console.log(`Retry attempt ${attempt} after ${delay}ms`);
    }
  }
}

// Usage
const payment = await retryWithBackoff(() =>
  createPayment(paymentData)
);
```

## Idempotency

Always use idempotency keys to safely retry requests without creating duplicates:

```javascript
async function createPaymentSafely(paymentData) {
  // Generate a unique idempotency key for this payment
  const idempotencyKey = `payment_${paymentData.orderId}_${Date.now()}`;

  try {
    const response = await fetch('https://api.evolvepayments.com/v1/payments', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${apiKey}`,
        'Content-Type': 'application/json',
        'Idempotency-Key': idempotencyKey
      },
      body: JSON.stringify(paymentData)
    });

    return await response.json();
  } catch (error) {
    // Safe to retry with same idempotency key
    // If payment was created, you'll get the same response
    return retryPayment(paymentData, idempotencyKey);
  }
}
```

**Idempotency key best practices:**
- Use unique keys per payment attempt
- Include order/transaction ID in the key
- Keys are valid for 24 hours
- Store keys with payment records
- Use same key for all retries of the same operation

## Logging and Monitoring

### Log All Errors

```javascript
function logPaymentError(error, context) {
  console.error('Payment error:', {
    type: error.type,
    code: error.code,
    message: error.message,
    payment_id: error.payment_id,
    timestamp: new Date().toISOString(),
    context: {
      user_id: context.userId,
      order_id: context.orderId,
      amount: context.amount
    }
  });

  // Send to error tracking service
  errorTracker.captureException(error, {
    tags: {
      error_type: error.type,
      error_code: error.code
    },
    extra: context
  });
}
```

### Monitor Error Rates

Track these metrics:
- Decline rate by decline_code
- Error rate by error type
- Failed payment attempts
- Retry attempts and success rates
- Network timeout frequency

### Set Up Alerts

Alert on:
- Sudden increase in decline rate (>10% change)
- High rate of api_error responses
- Authentication errors (may indicate compromised keys)
- Repeated rate limit errors

## User-Friendly Error Messages

Never expose raw API errors to users. Translate technical errors into clear, actionable messages:

```javascript
function getUserMessage(error) {
  const messages = {
    // Card errors
    'card_declined': {
      'insufficient_funds': 'Your card has insufficient funds. Please use a different card.',
      'expired_card': 'Your card has expired. Please update your payment method.',
      'incorrect_cvc': 'The security code is incorrect. Please check and try again.',
      'default': 'Your card was declined. Please contact your bank or try a different card.'
    },

    // API errors
    'api_error': 'We\'re experiencing technical difficulties. Please try again in a moment.',
    'rate_limit_error': 'We\'re receiving too many requests. Please wait a moment and try again.',

    // Network errors
    'network_error': 'Connection problem. Please check your internet and try again.',

    // Validation errors
    'invalid_request_error': 'Please check your payment information and try again.'
  };

  if (error.type === 'card_error' && error.decline_code) {
    return messages.card_declined[error.decline_code] || messages.card_declined.default;
  }

  return messages[error.type] || 'An unexpected error occurred. Please try again.';
}
```

## Testing Error Handling

Test your error handling with these test cards:

```javascript
// Test suite for error handling
describe('Payment Error Handling', () => {
  it('should handle card decline', async () => {
    const result = await createPayment({
      card: { number: '4000000000000002' }, // Declined card
      amount: 2000
    });

    expect(result.error).toBe(true);
    expect(result.message).toContain('declined');
  });

  it('should handle insufficient funds', async () => {
    const result = await createPayment({
      card: { number: '4000000000009995' },
      amount: 2000
    });

    expect(result.error.decline_code).toBe('insufficient_funds');
  });

  it('should retry on network error', async () => {
    // Mock network failure
    mockNetworkFailure();

    const result = await createPaymentWithRetry(paymentData);

    expect(retryAttempts).toBe(3);
  });
});
```

## Error Handling Checklist

Before going live, ensure you:

- [ ] Handle all error types appropriately
- [ ] Implement retry logic with exponential backoff
- [ ] Use idempotency keys for all payment requests
- [ ] Display user-friendly error messages
- [ ] Log errors with sufficient context
- [ ] Monitor error rates and set up alerts
- [ ] Test all error scenarios
- [ ] Handle network timeouts
- [ ] Validate input before API calls
- [ ] Never automatically retry card declines

## Common Mistakes

### Auto-Retrying Card Declines
Never automatically retry when a card is declined. This can trigger fraud detection and won't help the user.

### Exposing Technical Errors
Don't show raw API errors to users. Translate them into friendly, actionable messages.

### Ignoring Idempotency
Without idempotency keys, network retries can create duplicate charges.

### Insufficient Logging
Log enough context to debug issues: user ID, order ID, amount, error details.

### Not Handling Network Errors
Always implement timeout handling and retry logic for network failures.

## Best Practices

1. **Validate Early**: Check data validity before sending to the API
2. **Use Idempotency**: Always include idempotency keys for payment requests
3. **Retry Smartly**: Use exponential backoff for retryable errors only
4. **Log Everything**: Comprehensive logging helps debug production issues
5. **Monitor Proactively**: Set up alerts for unusual error patterns
6. **Test Thoroughly**: Test every error scenario in your integration
7. **Communicate Clearly**: Give users actionable error messages
8. **Fail Gracefully**: Always have a fallback when payments fail

## Next Steps

- Review [testing scenarios](testing-payments.md) for error testing
- Learn about [webhooks](handling-webhooks.md) as a backup notification method
- Check the [API Reference](../api-reference/overview.md) for complete error documentation
- See the [FAQ](../support/faq.md) for common troubleshooting questions
