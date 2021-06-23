---
description: Get paid with crypto!
---

# Welcome to the SpankPay Developer Documentation!

## Overview

The SpankPay Merchant SDK makes it easy to get paid with cryptocurrency: easy for your users, and easy for you \(the developer\).

Getting started with SpankPay is a three-step process:

#### 1. Include the SpankPay JavaScript

Add the following line to the bottom of the page on which you will be accepting payments:

```markup
<script src="https://pay.spankchain.com/spankpay.js"></script>
```

#### **2. Add a** _**Pay with SpankPay**_ **button**

Add the following code to create a button which will start a SpankPay payment. Alternatively, you can call the JavaScript API directly.


``` html tab="HTML" 
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

``` javascript tab="Javascript API"
const { spankpay } = require('spankpay')

spankpay.show({
    apiKey: 'test_quickstart_key',
    amount: '69.69',
    currency: 'USD',
    callbackUrl: 'https://pay-api.spankchain.com/quickstart/callback',
    description: 'Test Invoice JS API',
    onPayment: function(payment) {
        console.log(`Payment ${payment.status}`, payment)
    },
})
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

#### 3. Generate an API key and write a Webhook endpoint

After you've evaluated SpankPay and would like to fully integrate it with your site, you will need to sign up for an account, create testing + production API keys, and implement a webhook endpoint which SpankPay will use to notify your application on a successful payment.

!!! info
    This is covered in detail in [Webhook Callbacks](api-reference.md#webhook-callbacks).

## Integrating SpankPay

### 1. Include the SpankPay JavaScript

The first step in integrating SpankPay into your website is including the SpankPay JavaScript. This can be done directly with a `<script>` tag:

```markup
<script src="https://pay.spankchain.com/spankpay.js"></script>
```

Or via `npm`:

```text
$ npm install --save spankpay
```

See [SpankPay APIs](api-reference.md) for complete API documentation for the `spankpay.js` library.

### 2. Create an Invoice

In SpankPay, an `Invoice` is used to request payment from a user. Invoices can be created through either a [SpankPay Button](api-reference.md#creating-a-purchase-with-a-button).

```markup
<button
    data-spankpay-key="test_quickstart_key"
    data-amount="69.69"
    data-currency="USD"
    data-callback-url="https://pay-api.spankchain.com/quickstart/callback"
    data-on-payment="onSpankPayPayment">
  Pay with SpankPay!
</button>
```

Which will show the SpankPay payment page when the _Pay with SpankPay!_ button is clicked, and call the `onSpankPayPayment` callback once the payment has been completed and the webhook callback has been called.

Or via the [`spankpay.show(...)` method](api-reference.md#creating-an-invoice-with-the-spankpay-javascript-api):

```javascript
const { spankpay } = require('spankpay')

spankpay.show({
    apiKey: 'test_quickstart_key',
    amount: '69.69',
    currency: 'USD',
    callbackUrl: 'https://pay-api.spankchain.com/quickstart/callback',
    onPayment: function(payment) {
        console.log(`Payment ${payment.status}`, payment)
    },
})
```

And For a complete description of each of these methods, including their options, see the [Invoice API](api-reference.md#invoice).

### 3. Implement a Webhook Callback URL

When SpankPay receives confirmation of a user's payment, it will call the [`callback` webhook](api-reference.md#webhook-callbacks) URL with a `Payment` to notify your application that the payment was completed successfully.

``` json
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
```

For more, see:

* [Implementing Webhooks](api-reference.md#webhook-callbacks)
* [The Payment object](api-reference.md#payment)

### **4. Testing Your Integration**

#### **Testing API Keys**

Every API key has a corresponding "testing" key. This testing key shares common settings \(such as accepted coins\) with the main key, but the currency list in the payment iframe will also include the option to pay with "test" versions of each coin.

When a test coin is selected, the user will be presented with a "send fake payment" button, which will immediately simulate a payment with that coin.

Test payments are identical to real payments, except the currencies will be prefixed with "TEST":

``` json
{
    "amount": "69.69",
    "amountCurrency": "TEST-USD",
    "inputAmount": "0.6969",
    "inputCurrency": "TEST-ETH",
    ...
}
```

Note that the testing API key also uses a different secret key.

We recommend using the testing key in your development and staging environments.

#### Testing Webhooks

For more details on effectively testing webhooks during development, see: [Testing Webhooks](api-reference.md#testing-webhooks).

#### 



