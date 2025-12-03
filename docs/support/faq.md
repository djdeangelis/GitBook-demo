# Frequently Asked Questions

Find answers to common questions about Evolve Payments. If you don't see your question here, email us at support@evolvepayments.com.

## Getting Started

### How do I create an account?

Sign up at [dashboard.evolvepayments.com/signup](https://dashboard.evolvepayments.com/signup). You'll get immediate access to test mode and can start integrating right away. To process live payments, you'll need to complete account verification.

### What information do I need to verify my account?

To accept live payments, you'll need to provide:
- Business information (legal name, address, tax ID)
- Bank account details for payouts
- Personal information for business owners
- Business documentation (varies by country)

The verification process typically takes 1-2 business days.

### Where do I find my API keys?

Your API keys are available in the [Dashboard](https://dashboard.evolvepayments.com/api-keys) under **Settings** → **API Keys**. Test keys are available immediately. Live keys become available after account verification.

## Pricing & Fees

### What does Evolve Payments cost?

Our pricing is simple and transparent:

**Standard Pricing**
- 2.9% + $0.30 per successful card payment
- No setup fees
- No monthly fees
- No hidden charges

**Volume Pricing**
- Custom rates available for businesses processing over $100,000/month
- Contact sales@evolvepayments.com for volume pricing

**International Cards**
- Additional 1% fee for cards issued outside the US

**Currency Conversion**
- 1% fee for currency conversion

### When do I get paid?

**Standard Payouts**
- Daily automatic payouts
- Funds arrive in 2 business days
- Free bank transfers

**Instant Payouts**
- Available for eligible accounts
- Funds arrive in minutes
- 1% fee (maximum $10)

### Are there any refund fees?

No. Refunds are free, and we refund the processing fees for the original payment. However, the $0.30 fixed fee is not refunded.

**Example:**
- Original payment: $100.00
- Fees paid: $3.20 (2.9% + $0.30)
- Refund amount: $100.00
- Fees refunded: $2.90
- Net cost to you: $0.30

### Do you charge for failed payments?

No. You only pay for successful payments. Failed payments, declined cards, and test mode transactions are all free.

## Supported Features

### Which countries can I accept payments from?

You can accept payments from customers in **195+ countries** worldwide using major credit and debit cards (Visa, Mastercard, American Express, Discover).

### Which countries can I operate in?

You can create an Evolve Payments account if your business is based in:
- United States
- Canada
- United Kingdom
- European Union (all member states)
- Australia
- New Zealand
- Singapore
- Japan

More countries are being added regularly. Check our [supported countries page](https://evolvepayments.com/global) for the latest list.

### What currencies do you support?

We support **135+ currencies**, including:

**Popular currencies:**
- USD - US Dollar
- EUR - Euro
- GBP - British Pound
- CAD - Canadian Dollar
- AUD - Australian Dollar
- JPY - Japanese Yen
- CHF - Swiss Franc
- SGD - Singapore Dollar

You can charge customers in their local currency and receive payouts in your preferred currency. See the full list in the [Dashboard](https://dashboard.evolvepayments.com/settings/currencies).

### What payment methods do you support?

**Credit & Debit Cards**
- Visa
- Mastercard
- American Express
- Discover
- Diners Club
- JCB

**Digital Wallets** (coming soon)
- Apple Pay
- Google Pay

**Local Payment Methods** (select markets)
- SEPA Direct Debit (Europe)
- ACH (United States)
- BACS (United Kingdom)

## Security & Compliance

### Is Evolve Payments PCI compliant?

Yes. Evolve Payments is PCI DSS Level 1 certified, the highest level of compliance. When you use our API:

- Card data is encrypted in transit and at rest
- We handle card data so you don't have to
- Your servers never touch sensitive card information
- We maintain PCI compliance on your behalf

**Your responsibilities:**
- Use HTTPS for all payment pages
- Never log card numbers
- Follow our security best practices
- Use test mode for development

### How do you prevent fraud?

Evolve Payments includes built-in fraud detection:

**Automatic Protection**
- Machine learning models analyze every transaction
- Risk scoring for all payments
- Velocity checks for suspicious patterns
- Card testing detection
- IP address analysis

**Advanced Features**
- 3D Secure authentication
- Custom risk rules
- Manual review queues
- Blocklists and allowlists

Fraud protection is included at no extra cost.

### What happens to declined payments?

Declined payments are not charged. The customer sees an error message and can try a different payment method. You receive:
- A failed payment object with the decline reason
- A `payment.failed` webhook event
- Details in the Dashboard

Common decline reasons include insufficient funds, expired cards, or fraud prevention blocks.

### How secure are API keys?

API keys are highly secure when properly managed:

**Our security measures:**
- Keys are encrypted at rest
- All API requests use HTTPS
- Keys can be rotated instantly
- Separate test and live keys

**Your security responsibilities:**
- Never commit keys to version control
- Store keys in environment variables
- Rotate keys regularly
- Use separate keys per environment
- Revoke compromised keys immediately

See our [authentication guide](../getting-started/authentication.md) for detailed security practices.

## Payments

### What's the minimum/maximum payment amount?

**Minimum:** $0.50 USD (or equivalent in other currencies)

**Maximum:**
- $999,999.99 USD per transaction
- Contact us for higher limits

### How long does a payment take to process?

**Card payments:**
- Most payments complete in 2-3 seconds
- Some cards require 3D Secure authentication (adds 5-10 seconds)
- You receive immediate confirmation via API response

**Settlement:**
- Funds are settled with the card networks within 24 hours
- Payouts arrive in your bank account in 2 business days

### Can I refund a payment?

Yes. You can issue full or partial refunds through the API or Dashboard:

**Full refund:**
```bash
curl https://api.evolvepayments.com/v1/refunds \
  -H "Authorization: Bearer ek_live_abc123" \
  -d "payment=pay_123"
```

**Partial refund:**
```bash
curl https://api.evolvepayments.com/v1/refunds \
  -H "Authorization: Bearer ek_live_abc123" \
  -d "payment=pay_123" \
  -d "amount=1000"
```

**Refund timeline:**
- Refund processed immediately
- Appears on customer's statement in 5-10 business days
- You receive a `refund.created` webhook

**Limitations:**
- Refunds must be issued within 180 days
- Cannot refund more than the original payment amount
- Partial refunds can be issued multiple times

### What happens if a customer disputes a payment?

If a customer disputes a payment (files a chargeback):

1. **Notification:** You receive an email and `dispute.created` webhook
2. **Evidence submission:** You have 7 days to submit evidence
3. **Review:** The card network reviews the case
4. **Decision:** Typically takes 60-90 days

**Best practices to prevent disputes:**
- Provide clear product descriptions
- Use recognizable business names
- Send confirmation emails
- Provide tracking information
- Respond to customer inquiries promptly
- Process refunds for valid complaints

**Chargeback fees:** $15 per dispute (waived if you win)

### Can I save customer payment information?

Yes. Save payment methods for future use:

```bash
# Create a customer with saved card
curl https://api.evolvepayments.com/v1/customers \
  -H "Authorization: Bearer ek_test_abc123" \
  -d email="customer@example.com" \
  -d payment_method[card][number]="4242424242424242" \
  -d payment_method[card][exp_month]=12 \
  -d payment_method[card][exp_year]=2025
```

**Benefits:**
- Faster checkout for returning customers
- Enable subscription billing
- Reduce cart abandonment

**Security:**
- Card details are stored securely by Evolve
- PCI compliance handled for you
- Customers can delete saved cards anytime

## Testing

### How do I test without charging real cards?

Use test mode with your test API key (prefix: `ek_test_`):

**Test card numbers:**
- Success: `4242 4242 4242 4242`
- Decline: `4000 0000 0000 0002`
- See full list in our [testing guide](../guides/testing-payments.md)

**Test mode features:**
- All features available (same as live)
- No real cards charged
- Separate data from live mode
- Free unlimited testing

### Can I clear my test data?

Yes. Go to **Settings** → **Account** → **Clear test data**. This deletes all test payments, customers, and related data. Live data is never affected.

## Webhooks

### What are webhooks and why do I need them?

Webhooks are HTTP callbacks that notify your server when events occur (e.g., successful payment, refund created). You need webhooks because:

- Users may close their browser before seeing confirmation
- Some events are asynchronous (disputes, payouts)
- Reliable notification mechanism
- Enables automated workflows

See our [webhooks guide](../guides/handling-webhooks.md) for implementation details.

### How do I verify webhook authenticity?

Verify the `X-Evolve-Signature` header using your webhook signing secret:

```javascript
const crypto = require('crypto');

function verifyWebhook(payload, signature, secret) {
  const expectedSignature = crypto
    .createHmac('sha256', secret)
    .update(payload)
    .digest('hex');

  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(expectedSignature)
  );
}
```

Always verify signatures to ensure webhooks are from Evolve Payments.

### What if my webhook endpoint is down?

Evolve automatically retries failed webhooks:
- Immediately
- After 1 minute
- After 5 minutes
- After 30 minutes
- After 2 hours
- After 6 hours

After 6 failed attempts, the webhook is marked as failed. You can manually retry from the Dashboard.

## Technical Questions

### What are idempotency keys?

Idempotency keys prevent duplicate payments when a request is retried:

```bash
curl https://api.evolvepayments.com/v1/payments \
  -H "Authorization: Bearer ek_test_abc123" \
  -H "Idempotency-Key: order_123_payment_1" \
  -d "..."
```

If you retry with the same key within 24 hours, you'll get the original response without creating a duplicate payment.

### What are the API rate limits?

- Test mode: 100 requests/second
- Live mode: 100 requests/second per API key

If exceeded, you'll receive a `429 Too Many Requests` response. Use exponential backoff to retry.

### Do you provide SDKs?

Yes. Official libraries for:
- Node.js
- Python
- Ruby
- PHP
- Java
- Go

See the [API Reference](../api-reference/overview.md) for installation instructions.

### Can I use Evolve Payments in mobile apps?

Yes. Use our API from your backend server (never expose API keys in mobile apps). For mobile best practices:

1. Create payment intents from your server
2. Use our mobile SDKs to collect card details securely
3. Confirm payments from your server
4. Use webhooks for payment confirmation

Never include API keys in mobile app code.

### What's the API uptime?

Evolve Payments maintains 99.99% uptime. Check our [status page](https://status.evolvepayments.com) for:
- Current status
- Incident history
- Scheduled maintenance
- Subscribe to notifications

## Account & Settings

### Can I have multiple accounts?

Yes. You can create separate accounts for different businesses or use cases. Each account has its own:
- API keys
- Payment history
- Settings
- Bank accounts for payouts

### How do I change my bank account?

Go to **Settings** → **Payouts** → **Bank Accounts**:
1. Add new bank account
2. Verify with micro-deposits
3. Set as default
4. Remove old account

Changes take effect for the next payout.

### Can I customize the payment receipt?

Yes. Customize receipts in **Settings** → **Branding**:
- Add your logo
- Set business name
- Add support email
- Customize colors

### How do I delete my account?

Contact support@evolvepayments.com to close your account. Before closing:
- Process any pending refunds
- Download transaction history
- Complete pending payouts
- Cancel active subscriptions

## Support

### How do I get help?

**Documentation**
- Start with our [guides](../guides/handling-webhooks.md)
- Check this FAQ
- Review the [API Reference](../api-reference/overview.md)

**Email Support**
- support@evolvepayments.com
- Response within 24 hours
- Technical support available

**Community**
- [Developer community](https://community.evolvepayments.com)
- Share knowledge with other developers
- Get answers from the community

**Emergency Support**
- For live mode issues affecting your business
- Emergency hotline available for verified accounts
- Contact via Dashboard for emergency contact info

### How do I report a security issue?

Email security@evolvepayments.com with details. We take security seriously:
- Responsible disclosure program
- Security acknowledgments
- Bug bounty program

Never post security issues publicly.

### Where can I see service status?

Visit [status.evolvepayments.com](https://status.evolvepayments.com) for:
- Current system status
- Incident reports
- Scheduled maintenance
- Subscribe to updates via email, SMS, or Slack

### How do I request a new feature?

We love hearing from developers:
- Email feedback@evolvepayments.com
- Vote on feature requests in our [community](https://community.evolvepayments.com)
- Share your use case to help us prioritize

## Still Have Questions?

Can't find what you're looking for? We're here to help:

- **Email:** support@evolvepayments.com
- **Community:** [community.evolvepayments.com](https://community.evolvepayments.com)
- **Documentation:** Browse our [guides](../guides/handling-webhooks.md) and [API reference](../api-reference/overview.md)
- **Sales:** sales@evolvepayments.com for pricing and enterprise inquiries
