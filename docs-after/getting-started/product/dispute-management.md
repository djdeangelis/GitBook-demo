---
description: Handle chargebacks and disputes efficiently with Evolve Payments.
---

# Dispute management

### Dispute Lifecycle <a href="#dispute-lifecycle" id="dispute-lifecycle"></a>

1. Dispute created
2. Evidence submission
3. Resolution

### API Example <a href="#api-example" id="api-example"></a>

```
curl https://api.evolvepay.com/v1/disputes/dsp_123/evidence \
  -X POST \
  -u sk_test_YOUR_API_KEY: \
  -d evidence="Proof of delivery"
```

### Best Practices <a href="#best-practices" id="best-practices"></a>

* Respond promptly to disputes
* Provide clear evidence
* Monitor dispute rates in your dashboard

<br>
