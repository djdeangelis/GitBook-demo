---
description: Handle chargebacks and disputes efficiently with Evolve Payments.
metaLinks:
  alternates:
    - https://app.gitbook.com/s/N5k8pHgqDB1McsDpSWlO/product/dispute-management
---

# Dispute management

## Dispute Management <a href="#dispute-management" id="dispute-management"></a>

Disputes happen whenever a cardholder questions a charge with their bankmaybe they dont recognize the transaction, a package arrived late, or they believe they were charged twice. Evolve Payments helps you track these disputes, respond with clear evidence, and monitor their impact on your business.

This page walks through what a typical dispute looks like from creation to resolution, how to submit evidence via the API, and practical habits that keep dispute rates under control.

### Dispute Lifecycle <a href="#dispute-lifecycle" id="dispute-lifecycle"></a>

Every dispute follows a predictable path. Understanding each stage makes it easier to respond in time and with the right information.

1. **Dispute created**\
   A cardholder contacts their bank to contest a charge. The bank opens a dispute and notifies the card network and your processor. In your Evolve Payments dashboard, the payment appears with a disputed status, along with key details like amount, reason code, and response deadline.
2. **Evidence submission**\
   You gather and submit evidence that shows the charge was valid. This might include proof of delivery, invoices, customer communication, or refund records. Evidence can be submitted directly in the dashboard or programmatically via the API (see example below). Submitting complete evidence before the deadline gives you the best chance of winning the case.
3. **Resolution**\
   The bank reviews the evidence and makes a decision. If the dispute is resolved in your favor, the funds may be returned to you. If the cardholder wins, the chargeback stands and the funds remain with them. The final outcome and reason are recorded in your dashboard so you can learn from patterns over time.

### API Example <a href="#api-example" id="api-example"></a>

Use the disputes API when you want to automate evidence submission from your own tools (for example, a support system that collects delivery confirmations or chat transcripts).

```
curl https://api.evolvepay.com/v1/disputes/dsp_123/evidence \
  -X POST \
  -u sk_test_YOUR_API_KEY: \
  -d evidence="Proof of delivery"
```

In practice youll often attach more structured informationorder IDs, tracking links, timestamps, or customer messagesso that reviewers have enough context to make a confident decision.

### Best Practices <a href="#best-practices" id="best-practices"></a>

Building a simple internal process around disputes can significantly reduce lost revenue and future dispute rates.

* **Respond promptly to disputes**\
  Check your dashboard regularly and treat response deadlines as hard cutoffs. Submitting evidence even a day late can mean an automatic loss.
* **Provide clear, organized evidence**\
  Prioritize documents that directly connect the cardholder to the purchase (order confirmations, delivery receipts, signed agreements). Add brief explanations so reviewers can quickly understand what each document proves.
* **Monitor dispute rates in your dashboard**\
  Look for patterns by product, region, or payment method. If you see disputes clustering around a specific offer or workflow, adjust your checkout copy, refund policy, or fulfillment process to address the root cause.

Well-managed disputes turn one of the most stressful parts of payments into a predictable, trackable process your team can handle confidently.

<br>
