---
description: Get paid with crypto!
---

# Welcome to the SpankPay Developer Documentation!

* [How To Use the SpankPay API ](#how-to-use-the-spankpay-api)
  1. [Add the SpankPay JavaScript](#1-add-the-spankpay-javascript)
  2. [Generate an API Key](#2-generate-an-api-key)
  3. [Add a **Pay with SpankPay** button](#3-add-a-pay-with-spankpay-button)
  4. [Implement a Webhook Endpoint](#4-implement-a-webhook-endpoint)
* [Testing Your Integration](#testing-your-integration)


## How To Use the SpankPay API ##

The SpankPay Merchant SDK makes it easy to get paid with cryptocurrency: easy for your users, and easy for you \(the developer\).

Getting started with SpankPay is a four-step process:

#### 1. Add the SpankPay JavaScript

Add the following line to the bottom of the page on which you will be accepting payments:

```markup
<script src="https://pay.spankchain.com/spankpay.js"></script>
```
Or via `npm`:

```text
$ npm install --save spankpay
```

###### See [SpankPay APIs](api-reference.md) for complete API documentation for the `spankpay.js` library.


#### 2. Generate an API key

Sign up for a [SpankPay](https://spankpay.com) account. Create testing + production API keys via Account Settings > Developer
   

#### **3. Add a** _**Pay with SpankPay**_ **button**

In SpankPay, an `Invoice` is used to request payment from a user. An invoice is generated when a user clicks the _**Pay with SpankPay**_ button. 

This button can be implemented with either **HTML** or a direct call to the **JavaScript API**

* **[HTML Button](api-reference.md#creating-a-purchase-with-a-button) with Javascript.** When the _Pay with SpankPay!_ button is clicked, the SpankPay payment page will be displayed. Once the payment has been completed and the webhook callback has been called, the `onSpankPayPayment` function will be called


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
* or **Call the [JavaScript API directly](api-reference.md#creating-an-invoice-with-the-spankpay-javascript-api) via `spankpay.show(...)` method**
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

#### 4. Implement a Webhook endpoint
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

###### This is covered in detail in [Webhook Callbacks](api-reference.md#webhook-callbacks).
    
***

###### **Want more control?** SpankPay comes with a [complete JavaScript API](api-reference.md) to give you full control of the payment process.


***



## Testing Your Integration

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



