# Welcome to Evolve Payments

Evolve Payments is a modern payment processing platform built for SaaS companies who need to accept payments globally with minimal friction. We handle the complexity of payment processing so you can focus on growing your business.

## What is Evolve Payments?

Evolve Payments provides a simple, developer-friendly API for accepting payments, managing subscriptions, and processing refunds. Whether you're building a new product or scaling an existing business, our platform grows with you.

## Key Features

### Simple Integration
Get started in minutes with our RESTful API and comprehensive documentation. Our client libraries for popular programming languages make integration straightforward, and our test mode lets you build with confidence.

### Global Payments
Accept payments from customers worldwide with support for:
- 135+ currencies
- Major credit and debit cards (Visa, Mastercard, American Express, Discover)
- Local payment methods in key markets
- Automatic currency conversion

### Instant Payouts
Access your funds quickly with daily automatic payouts to your bank account. No more waiting weeks for your money.

### Developer-Friendly
Built by developers, for developers:
- Clean, predictable API design
- Comprehensive webhooks for real-time updates
- Detailed error messages
- Test mode with realistic test data
- Interactive API reference

### Enterprise-Grade Security
- PCI DSS Level 1 compliant
- Strong encryption for sensitive data
- Advanced fraud detection
- 3D Secure support

## Who It's For

Evolve Payments is perfect for:

- **SaaS Companies**: Accept recurring payments for subscriptions with automatic billing
- **E-commerce Platforms**: Process one-time payments with support for global customers
- **Marketplaces**: Split payments between multiple parties with our Connect platform
- **Mobile Apps**: Accept in-app payments with our mobile SDKs
- **Growing Startups**: Scale from your first payment to millions with the same API

## Getting Started

Ready to start accepting payments? Here's what you need to do:

1. [Create an account](https://dashboard.evolvepayments.com/signup) to get your API keys
2. [Learn about authentication](authentication.md) and how to use your API keys
3. [Make your first payment](making-your-first-payment.md) in test mode
4. Review our [API Reference](../api-reference/overview.md) for detailed endpoint documentation

## Need Help?

We're here to support you:

- **Documentation**: Comprehensive guides and API reference
- **Support**: Email us at support@evolvepayments.com
- **Community**: Join our [developer community](https://community.evolvepayments.com)
- **Status**: Check our [status page](https://status.evolvepayments.com) for uptime information

## API Basics

All API requests use the base URL:
```
https://api.evolvepayments.com/v1
```

The API uses standard HTTP verbs (GET, POST, PUT, DELETE) and returns JSON responses. All requests require authentication using your API key.

> **Note**: Start in test mode to experiment safely. Your test API key begins with `ek_test_` and will never charge real cards.

Let's get you set up with [authentication](authentication.md) next.
