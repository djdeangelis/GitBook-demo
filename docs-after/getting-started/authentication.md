# Authentication

All API requests to Evolve Payments must be authenticated using API keys. This ensures that only authorized applications can access your account and process payments.

## API Keys

Evolve Payments uses API keys to authenticate requests. You can view and manage your API keys in the [Dashboard](https://dashboard.evolvepayments.com/api-keys).

### Key Types

We provide two types of API keys:

**Test Keys** (prefix: `ek_test_`)
- Use these for development and testing
- Never charge real cards
- Safe to use in development environments
- Can be regenerated at any time

**Live Keys** (prefix: `ek_live_`)
- Use these for production
- Process real payments and charge actual cards
- Keep these secure and never commit to version control
- Rotate regularly for security

> **Warning**: Never share your live API keys or commit them to version control. Anyone with your live key can process payments on your account.

## Finding Your API Keys

1. Log in to your [Evolve Payments Dashboard](https://dashboard.evolvepayments.com)
2. Navigate to **Settings** → **API Keys**
3. Copy your test key to start developing
4. Your live keys will be available once you complete account verification

## Using API Keys

Include your API key in the `Authorization` header of every request using Bearer authentication:

```bash
curl https://api.evolvepayments.com/v1/payments \
  -H "Authorization: Bearer ek_test_abc123def456ghi789" \
  -H "Content-Type: application/json"
```

### Request Headers

Every authenticated request should include:

```
Authorization: Bearer YOUR_API_KEY
Content-Type: application/json
```

## Example: Authenticated Request

Here's a complete example of an authenticated API request to retrieve a payment:

```bash
curl https://api.evolvepayments.com/v1/payments/pay_1234567890 \
  -H "Authorization: Bearer ek_test_abc123def456ghi789"
```

Response:
```json
{
  "id": "pay_1234567890",
  "object": "payment",
  "amount": 2000,
  "currency": "usd",
  "status": "succeeded",
  "created": 1704067200
}
```

## Security Best Practices

### Keep Keys Secret

- **Never** commit API keys to Git repositories
- **Never** expose keys in client-side code (JavaScript, mobile apps)
- **Never** share keys in support requests or public forums

### Store Keys Securely

Use environment variables to store your API keys:

```bash
# .env file
EVOLVE_API_KEY=ek_test_abc123def456ghi789
```

```python
# Python example
import os
api_key = os.environ.get('EVOLVE_API_KEY')
```

```javascript
// Node.js example
const apiKey = process.env.EVOLVE_API_KEY;
```

> **Tip**: Use different environment variables for test and live keys (e.g., `EVOLVE_TEST_KEY` and `EVOLVE_LIVE_KEY`).

### Rotate Keys Regularly

Rotate your API keys periodically, especially:
- After a team member with key access leaves
- If you suspect a key has been compromised
- As part of regular security maintenance (every 90 days)

To rotate a key:
1. Create a new key in the Dashboard
2. Update your application to use the new key
3. Test thoroughly
4. Delete the old key

### Use Test Keys in Development

Always use test keys during development. This prevents:
- Accidental charges to real cards
- Production data contamination
- Unexpected costs during testing

### Restrict Key Access

- Limit which team members have access to live keys
- Use the Dashboard's team management features to control permissions
- Consider using separate keys for different services or environments

## Handling Authentication Errors

If authentication fails, the API returns a `401 Unauthorized` error:

```json
{
  "error": {
    "type": "authentication_error",
    "message": "Invalid API key provided",
    "code": "invalid_api_key"
  }
}
```

Common authentication errors:

| Error Code | Description | Solution |
|------------|-------------|----------|
| `invalid_api_key` | The API key is invalid or malformed | Check that you're using the correct key |
| `expired_api_key` | The API key has been deleted | Generate a new key in the Dashboard |
| `missing_authorization` | No Authorization header provided | Add the Authorization header |
| `test_live_mismatch` | Using a test key with live mode data | Use the correct key type for your mode |

## Testing Authentication

Verify your authentication is working correctly:

```bash
curl https://api.evolvepayments.com/v1/account \
  -H "Authorization: Bearer ek_test_abc123def456ghi789"
```

If successful, you'll see your account details:

```json
{
  "id": "acct_1234567890",
  "object": "account",
  "email": "developer@example.com",
  "created": 1704067200
}
```

## Next Steps

Now that you understand authentication, you're ready to [make your first payment](making-your-first-payment.md).
