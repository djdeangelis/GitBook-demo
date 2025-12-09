---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/N5k8pHgqDB1McsDpSWlO/getting-started/collect-your-first-payment
---

# Collect your first payment

<div align="left"><figure><img src="../.gitbook/assets/Card-14.png" alt="" width="563"><figcaption></figcaption></figure></div>

## Before You Start <a href="#before-you-start" id="before-you-start"></a>

* Get your test API key from the Evolve Payments dashboard
* Make sure you're in sandbox mode for testing

## Step 1: Set Up Your Client <a href="#step-1-set-up-your-client" id="step-1-set-up-your-client"></a>

Initialize the Evolve Payments client with your test API key and sandbox environment.

## Step 2: Collect Payment Details <a href="#step-2-collect-payment-details" id="step-2-collect-payment-details"></a>

Gather the customer's payment information:

* Card number
* Expiration month and year
* Security code (CVC)
* Payment amount

{% hint style="success" %}
Once you can reliably collect these fields and perform basic validation (for example, required fields and simple format checks), you're ready to move on to creating your first **test payment** in Step 3.
{% endhint %}

## Step 3: Create the Payment <a href="#step-3-create-the-payment" id="step-3-create-the-payment"></a>

Send a payment request with the collected information:

Copy

```
const payment = await evolve.charges.create({
  amount: 2999, // $29.99 in cents
  currency: 'usd',
  paymentMethod: {
    type: 'card',
    number: '4242424242424242',
    expMonth: 12,
    expYear: 2025,
    cvc: '123'
  },
  description: 'First test payment'
});
```

## Step 4: Handle the Result <a href="#step-4-handle-the-result" id="step-4-handle-the-result"></a>

Check the payment status and respond accordingly:

* **Succeeded**: Payment completed successfully
* **Failed**: Payment was declined (show error message)
* **Pending**: Payment is still processing

## Test Your Integration <a href="#test-your-integration" id="test-your-integration"></a>

Use these test card numbers:

* **4242424242424242** - Successful payment
* **4000000000000002** - Declined payment
* **4000000000009995** - Insufficient funds

## Important Notes <a href="#important-notes" id="important-notes"></a>

* Always use amounts in cents (e.g., $10.00 = 1000)
* Test thoroughly before switching to live mode
* Handle errors gracefully with user-friendly messages
* Store the payment ID for your records

## Go Live <a href="#go-live" id="go-live"></a>

Once testing is complete:

1. Switch to your live API key
2. Change environment to 'production'
3. Start processing real payments

Your first payment integration is now ready!

<br>
