---
description: Get paid with crypto!
---

# Welcome to the SpankPay (v2) Developer Documentation!

## Overview

The SpankPay Merchant SDK makes it easy to get paid with cryptocurrency: easy for your users, and easy for you \(the developer\).

Getting started with SpankPay is a four-step process:

#### 1. Include the SpankPay JavaScript

Add the following line to the bottom of the page on which you will be accepting payments:

```markup
<script src="https://unpkg.com/@spankchain-dev/spankpay-sdk"></script>
```

#### **2. Grab your API Key**

In the Developer section of your SpankPay account, you will find both test and production API keys to use. You can also turn on test currencies to run test transactions without using actual money.

#### **3. Add a** _**Pay with SpankPay**_ **button**

Using either vanilla Javascript or our NPM package, create a button for starting a SpankPay payment

#####HTML+Vanilla JS

```
<script src="https://unpkg.com/@spankchain-dev/spankpay-sdk"></script>
<button
    data-spankpay-key="test_quickstart_key"
    data-amount="69.69"
    data-currency="USD"
    data-callback-url="https://pay-api.spankchain.com/quickstart/callback"
    data-on-payment="onSpankPayPayment"
    data-description="Test Invoice HTML">
  Pay with SpankPay!
</button>

<script>
function onSpankPayPayment(payment) {
    console.log(`Payment ${payment.status}:`, payment)
}
</script>
```

#####NPM Package+JS framework

Function

```
import spankpay from "@spankchain-dev/spankpay-sdk";

  const showSpankPay = async (fiatEnabled, guestOnly) => {
    await spankpay.show({
      username: "spank-user",
      message: "message",
      amount: "10",
      redirectUrl: "https://spankpay.com",
      acceptedCurrencies: [
       'BTC',
        'ETH',
        'USDC',
      ],
      currency: 'USD',
      callbackUrl: 'https://your-webhook-endpoint.com',
      description: 'Test Invoice Show',
      metadata: JSON.stringify({ orderId: 'sc696969' }),
      fiatEnabled: fiatEnabled || false,
      guestOnly: guestOnly || false,
      allowPartial: false,
      premiumPct: 0.04,
      apiKey: 'test-api-key',
    });
  };
```

Render

```
  <button onClick={() => showSpankPay(false, false)}>
        Button using the JS show method
    </button>
```

!!! info
We strongly advise our merchants to allow "partial payments" on invoices by passing in the SDK option `allowPartial: true`. This means that any money paid on an invoice will be passed into your SpankPay account and a webhook will be sent - if you have multiple payment tiers, you will need to account for that in the endpoint that receives webhooks from SpankPay by checking the `amount_received` on the invoice at the time of webhook receipt.
For example, if you have multiple payment tiers like the following:
$0 - $10: 1 token per dollar
$10 - $100: 1.1 tokens per dollar
$100+: 1.2 tokens per dollar
A user might initiate an invoice for $500 but only send $5, you would need to implement appropriate logic for handling that on your end by checking the actual
amount of money the user sent instead of relying on the total amount invoiced.

!!! info
**Want more control?** SpankPay comes with a [complete JavaScript API](api-reference.md) to give you full control of the payment process.

#### **4. Implement an endpoint for webhook callbacks**

Implement an endpoint to receive SpankPay callbacks for notifying your application of a successful payment

!!! info
This is covered in detail in [Webhook Callbacks](api-reference.md#webhook-callbacks).

## Integrating SpankPay

### 1. Include the SpankPay JavaScript

The first step in integrating SpankPay into your website is including the SpankPay JavaScript.

This can be done directly with a `<script>` tag:

```markup
<script src="https://unpkg.com/@spankchain-dev/spankpay-sdk"></script>
```

Or via `npm`:

```text
$ npm install —save @spankchain-dev/spankpay-sdk
```

See [SpankPay APIs](api-reference.md) for complete API documentation for the `spankpay.js` library.

### 2. Create an Invoice

In SpankPay, an `Invoice` is used to request payment from a user. Invoices can be created through a [SpankPay Button](api-reference.md#creating-a-purchase-with-a-button), either rendered in HTML or JS.

```markup
<button
    data-spankpay-key="test_quickstart_key"
    data-amount="69.69"
    data-currency="USD"
    data-callback-url="https://yoursite.com/quickstart/callback"
    data-on-payment="onSpankPayPayment">
  Pay with SpankPay!
</button>

<script src="https://unpkg.com/@spankchain-dev/spankpay-sdk"></script>
```

Or via the [`spankpay.show(...)` method](api-reference.md#creating-an-invoice-with-the-spankpay-javascript-api):

```javascript
import spankpay from "@spankchain-dev/spankpay-sdk";

const showSpankPay = async (fiatEnabled, guestOnly) => {
  await spankpay.show({
    username: "spank-user",
    message: "message",
    amount: "10",
    redirectUrl: "https://spankpay.com",
    acceptedCurrencies: ["BTC", "ETH", "USDC"],
    currency: "USD",
    callbackUrl: "https://yoursite.com/quickstart/callback",
    description: "Test Invoice Show",
    metadata: JSON.stringify({ orderId: "sc696969" }),
    fiatEnabled: fiatEnabled || false,
    guestOnly: guestOnly || false,
    allowPartial: false,
    premiumPct: 0.04,
    apiKey: "test-api-key",
    onSpankPayPayment: function (payment) {
      console.log(`Payment ${payment.status}`, payment);
    },
  });
};
```

Render

```
  <button onClick={() => showSpankPay(true, true)}>
        Button using the JS show method
    </button>
```

````

The _Pay with SpankPay!_ button will take user to the SpankPay SDK (payment page.) Once the invoice has been paid, Spankpay will send a callback to the callback URL provided (in this example- "https://yoursite.com/quickstart/callback") and will run the function you’ve built (in this example- yourCallbackFunctionHere()) to work with that data, for example, give the payer their site tokens 

And For a complete description of each of these methods, including their options, see the [Invoice API](api-reference.md#invoice).

### 3. Implement a Webhook Callback URL

When SpankPay receives confirmation of a user's payment, it will call the [`callback` webhook](api-reference.md#webhook-callbacks) URL with a `Payment` to notify your application that the payment was completed successfully.

```json
POST /quickstart/callback
Content-Type: text/plain
X-SpankPay-Key: test_quickstart_key
X-SpankPay-Signature: t=1551389518&s=b613679a0814d9ec…

{
    "type": "payment",
    "paymentId": "pay_c493715653c",
    "createdOn": "1969-06-09T06:09:06.969Z",
    "invoiceId": "inv_f95d778c35f",
    "invoice": { ... },
    "amount": "69.69",
    "amountCurrency": "USD",
    "inputAmount": "0.6969",
    "inputCurrency": "ETH",
    "inputTx": "0x2144292c5ad…",
    "receipt": {
        "type": "webhook",
        "url": "https://yoursite.com/yourserver/callback",
        "status": "called",
        "calledOn": "1969-06-09T06:09:06.969Z",
        "responseStatusCode": 200,
        "response": ...,
        ...
    }
    ...
}
````

For more, see:

- [Implementing Webhooks](api-reference.md#webhook-callbacks)
- [The Payment object](api-reference.md#payment)

### **4. Testing Your Integration**

#### **Testing API Keys**

Turn on Test Currencies via the toggle in your account's Developer section. Now the username associated with this account will allow for a “Pay With Test” button to be displayed in the SDK.

We recommend using the testing key in your development and staging environments.

At this moment, test payments in the SDK will not create a payment object, and therefore won’t trigger a webhook to your callback URL. Feature update coming soon.

We recommend using the testing key in your development and staging environments.

#### Testing Webhooks

For more details on effectively testing webhooks during development, see: [Testing Webhooks](api-reference.md#testing-webhooks).

####
