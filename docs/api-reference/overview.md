# API Reference

Welcome to the Evolve Payments API Reference. This section provides comprehensive documentation for all API endpoints, request parameters, and response formats.

## About This Reference

The complete API reference is automatically generated from our OpenAPI specification, ensuring accuracy and consistency with the actual API implementation. The reference includes:

- **All API endpoints** with detailed descriptions
- **Request parameters** with types, validation rules, and examples
- **Response schemas** showing all possible fields
- **Error responses** for each endpoint
- **Interactive examples** you can try directly in your browser

## Accessing the API Reference

The full API reference documentation is generated from our OpenAPI specification located at:

```
../openapi/evolve-enhanced.yaml
```

When viewing this documentation in GitBook, you'll find the complete API reference in the **API Reference** section of the navigation menu. Each endpoint is documented with:

### Endpoint Details
- HTTP method (GET, POST, PUT, DELETE)
- Full URL path
- Authentication requirements
- Rate limiting information

### Request Information
- Path parameters
- Query parameters
- Request body schema
- Header requirements
- Example requests in multiple languages

### Response Information
- Success response schemas
- Error response formats
- Status codes
- Example responses

### Try It Out
Many endpoints include an interactive "Try It" feature where you can:
- Enter your test API key
- Modify request parameters
- Execute the request
- See the live response

## Quick Reference

### Base URL

All API requests should be made to:
```
https://api.evolvepayments.com/v1
```

### Authentication

Include your API key in the Authorization header:
```
Authorization: Bearer YOUR_API_KEY
```

### Content Type

All requests and responses use JSON:
```
Content-Type: application/json
```

## Core Resources

Here's a quick overview of the main resources in the API:

### Payments
Create, retrieve, update, and list payments.
- `POST /v1/payments` - Create a payment
- `GET /v1/payments/:id` - Retrieve a payment
- `GET /v1/payments` - List all payments

### Refunds
Issue full or partial refunds for payments.
- `POST /v1/refunds` - Create a refund
- `GET /v1/refunds/:id` - Retrieve a refund
- `GET /v1/refunds` - List all refunds

### Customers
Manage customer information and saved payment methods.
- `POST /v1/customers` - Create a customer
- `GET /v1/customers/:id` - Retrieve a customer
- `PUT /v1/customers/:id` - Update a customer
- `DELETE /v1/customers/:id` - Delete a customer
- `GET /v1/customers` - List all customers

### Payment Methods
Store and manage payment methods for future use.
- `POST /v1/payment_methods` - Create a payment method
- `GET /v1/payment_methods/:id` - Retrieve a payment method
- `PUT /v1/payment_methods/:id` - Update a payment method
- `DELETE /v1/payment_methods/:id` - Delete a payment method

### Subscriptions
Manage recurring billing and subscriptions.
- `POST /v1/subscriptions` - Create a subscription
- `GET /v1/subscriptions/:id` - Retrieve a subscription
- `PUT /v1/subscriptions/:id` - Update a subscription
- `DELETE /v1/subscriptions/:id` - Cancel a subscription

### Balance & Payouts
View your account balance and payout history.
- `GET /v1/balance` - Retrieve account balance
- `GET /v1/payouts` - List payouts
- `GET /v1/payouts/:id` - Retrieve a payout

## Common Patterns

### Pagination

List endpoints return paginated results:

```bash
curl "https://api.evolvepayments.com/v1/payments?limit=10&starting_after=pay_123" \
  -H "Authorization: Bearer ek_test_abc123"
```

Response includes pagination metadata:
```json
{
  "object": "list",
  "data": [...],
  "has_more": true,
  "url": "/v1/payments"
}
```

### Filtering

Many list endpoints support filtering:

```bash
# Filter payments by status
curl "https://api.evolvepayments.com/v1/payments?status=succeeded" \
  -H "Authorization: Bearer ek_test_abc123"

# Filter by date range
curl "https://api.evolvepayments.com/v1/payments?created[gte]=1704067200&created[lte]=1704153600" \
  -H "Authorization: Bearer ek_test_abc123"

# Filter by customer
curl "https://api.evolvepayments.com/v1/payments?customer=cus_123" \
  -H "Authorization: Bearer ek_test_abc123"
```

### Metadata

Most resources support metadata for storing custom key-value pairs:

```json
{
  "amount": 2000,
  "currency": "usd",
  "metadata": {
    "order_id": "order_123",
    "customer_name": "John Doe",
    "product_sku": "WIDGET-001"
  }
}
```

Metadata can be used to:
- Store internal IDs
- Link payments to your system
- Add custom tags
- Store additional context

### Expanding Nested Objects

Use the `expand` parameter to include related objects:

```bash
# Expand customer details in payment response
curl "https://api.evolvepayments.com/v1/payments/pay_123?expand[]=customer" \
  -H "Authorization: Bearer ek_test_abc123"
```

### Idempotency

Use idempotency keys to safely retry requests:

```bash
curl "https://api.evolvepayments.com/v1/payments" \
  -H "Authorization: Bearer ek_test_abc123" \
  -H "Idempotency-Key: order_123_attempt_1" \
  -H "Content-Type: application/json" \
  -d '{...}'
```

## Response Format

All API responses follow a consistent format:

### Success Response
```json
{
  "id": "pay_1234567890abcdef",
  "object": "payment",
  "created": 1704067200,
  ...
}
```

### Error Response
```json
{
  "error": {
    "type": "card_error",
    "code": "card_declined",
    "message": "Your card was declined.",
    "param": "payment_method"
  }
}
```

### List Response
```json
{
  "object": "list",
  "data": [
    {...},
    {...}
  ],
  "has_more": false,
  "url": "/v1/payments"
}
```

## Versioning

The API is versioned. The current version is `v1`, indicated in the base URL:
```
https://api.evolvepayments.com/v1
```

We maintain backward compatibility and announce deprecations well in advance. Subscribe to our [developer changelog](https://evolvepayments.com/changelog) for updates.

## Rate Limits

API rate limits:
- **Test mode**: 100 requests per second
- **Live mode**: 100 requests per second per API key

When you exceed the rate limit, you'll receive a `429 Too Many Requests` response. Use the `Retry-After` header to determine when to retry.

## Webhook Events

The API sends webhooks for asynchronous events. See the [Webhooks guide](../guides/handling-webhooks.md) for details on handling webhook events.

Common webhook events:
- `payment.succeeded`
- `payment.failed`
- `refund.created`
- `customer.created`
- `subscription.updated`

## SDKs and Libraries

Official client libraries are available for popular languages:

- **Node.js**: `npm install @evolvepayments/node`
- **Python**: `pip install evolvepayments`
- **Ruby**: `gem install evolvepayments`
- **PHP**: `composer require evolvepayments/evolvepayments-php`
- **Java**: Available via Maven Central
- **Go**: `go get github.com/evolvepayments/evolvepayments-go`

Example using the Node.js library:

```javascript
const evolve = require('@evolvepayments/node')('ek_test_abc123');

const payment = await evolve.payments.create({
  amount: 2000,
  currency: 'usd',
  payment_method: {
    type: 'card',
    card: {
      number: '4242424242424242',
      exp_month: 12,
      exp_year: 2025,
      cvc: '123'
    }
  }
});
```

## Postman Collection

Import our Postman collection to quickly explore the API:

1. Download the [Postman Collection](https://api.evolvepayments.com/postman/collection.json)
2. Import into Postman
3. Set your API key in the environment variables
4. Start making requests

## OpenAPI Specification

Download the complete OpenAPI specification:
- **Format**: OpenAPI 3.0
- **Location**: `../openapi/evolve-enhanced.yaml`
- **Use cases**: Generate client libraries, import into API tools, automated testing

## Need Help?

- **Getting Started**: See our [Getting Started guide](../getting-started/introduction.md)
- **Common Tasks**: Check out our [Guides](../guides/handling-webhooks.md)
- **Support**: Email support@evolvepayments.com
- **Community**: Join our [developer community](https://community.evolvepayments.com)
- **Status**: Monitor [status.evolvepayments.com](https://status.evolvepayments.com)

## Next Steps

- [View the complete API Reference](.) (navigate to API Reference section)
- [Make your first payment](../getting-started/making-your-first-payment.md)
- [Set up webhooks](../guides/handling-webhooks.md)
- [Test your integration](../guides/testing-payments.md)
