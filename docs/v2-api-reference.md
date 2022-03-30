# SpankPay v2 API Reference- WIP

## Invoice

An Invoice is a request for a payment. When created with the Button API or the JavaScript API, the user will be presented with a payment form prompting them to send cryptocurrency equivalent in value to the requested amount. Once the cryptocurrency has been received, the `callback` webhook will be called, and the Button/JavaScript `onPaymentComplete` callback is called.

<table>
  <thead>
    <tr>
      <th style="text-align:right;min-width: 9rem;">Attribute</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:right"><code>id</code>
      </td>
      <td style="text-align:left">The invoice&apos;s ID (assigned by SpankPay).</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>createdOn</code>
      </td>
      <td style="text-align:left">The timestamp the invoice was created (ISO 8601 format, assigned by SpankPay).</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>status</code>
      </td>
      <td style="text-align:left">The invoice&apos;s status. One of <code>pending</code> (awaiting payment), <code>pending-callback</code> (payment
        received, waiting for webhook to complete), <code>failed</code>, or <code>succeeded</code>.</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>apiKey</code>
      </td>
      <td style="text-align:left">The API key used to create this invoice.</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>amount</code>
      </td>
      <td style="text-align:left">The amount of the invoice, in <code>currency</code>. Must be positive and
        rounded to the appropriate number of decimal places for the currency.</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>currency</code>
      </td>
      <td style="text-align:left">The currency being requested. For valid values, see <a href="#output-currencies">Output Currencies</a>.</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>description</code>
      </td>
      <td style="text-align:left">
        A description of the goods / services within the invoice.
        <p></p>
        <p>Optional (but recommended).</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:right"><code>acceptedCurrencies</code>
      </td>
      <td style="text-align:left">
        <p>A list of currencies which should be accepted for this invoice. For valid
          values, see <a href="#input-currency">Input Currencies</a>.</p>
        <p></p>
        <p>Must be a subset of the API Key&apos;s list of accepted currencies.</p>
        <p></p>
        <p>Optional (default: all available currencies)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:right"><code>callbackUrl</code>
      </td>
      <td style="text-align:left">The callback URL used to notify your application when the payment succeeds.
        See also: <a href="#webhook-callbacks">Webhook Callbacks</a>.</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>metadata</code>
      </td>
      <td style="text-align:left">Arbitrary metadata provided by the caller, stored and returned along with
        the invoice. We suggest including the order or invoice number, and an opaque
        customer ID.
        <br />
        <br />Limited to 128kb of JSON data.
        <br />
        <br />Optional (default: <code>{}</code>)</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>allowPartial</code>
      </td>
      <td style="text-align:left">
        <p>If <code>allowPartial</code> is <code>true</code>, the <a href="./#3-implement-a-webhook-callback-url">webhook callback</a> will
          be called unconditionally when the first payment is received, whether the
          payment is less than, equal to, or greater than the invoice amount.</p>
        <p></p>
        <p>Optional (default: <code>false</code>)</p>
      </td>
    </tr>
  </tbody>
</table>

### Input Currency

The Input Currency for an Invoice is the currency sent by the user.

Currently valid input currencies:

| Currency | Description |
| ---: | :--- |
| `BTC` | Bitcoin |
| `ETH` | Ethereum |
| `LTC` | Litecoin |
| `USDC` | USD Coin |

### Output Currencies

The Output Currency for an Invoice is the currency which will be displayed to the user. For example, a US$10 invoice will have `"amount": "10.00"` and `"currency": "USD"`, and when paying, the user will be given the option to pay with \(for example\) 0.074 ETH or 0.0026 BTC.

Currently valid output currencies:

| Currency | Description |
| ---: | :--- |
| `USD` | US Dollars |
| `BTC` | Bitcoin |
| `ETH` | Ethereum |

### Creating an Invoice with a Button

A SpankPay button is the simplest way to accept SpankPay on your site.

When the button is clicked, the user will be presented with the SpankPay payment frame, and the `data-on-payment` callback will be called once the payment is complete and your callback has accepted the payment.

```markup
<script src="https://pay.spankchain.com/spankpay.js"></script>

<script>
function onSpankPayPayment(payment) {
  console.log(`Payment ${payment.status}`, payment)
}
</script>

<button
    data-spankpay-key="test_quickstart_key"
    data-amount="69.69"
    data-currency="USD"
    data-accepted-currencies="ETH,LTC,BTC"
    data-metadata="{&quot;orderId&quot;: &quot;sc696969&quot;}"
    data-callback-url="https://pay-api.spankchain.com/quickstart/callback"
    data-on-payment="onSpankPayPayment">
  Pay with SpankPay!
</button>
```

See also:

* [Invoice parameters](api-reference.md#purchase)
* [`payment` Event](api-reference.md#payment-event)
* [Webhook Callbacks](api-reference.md#webhook-callbacks)

### Creating an Invoice with the SpankPay JavaScript API

For complete control over the user's SpankPay experience, the API can be called directly.

The `spankpay.show(...)` method can be used to show the SpankPay payment frame.

```javascript
const { spankpay } = require('spankpay')

const frame = spankpay.show({
  apiKey: 'test_quickstart_key',
  amount: '69.69',
  currency: 'USD',
  metadata: {
    orderId: 'sc696969',
  },
  callbackUrl: 'https://pay-api.spankchain.com/quickstart/callback',
})

frame.on('payment', payment => {
  console.log(`Payment ${payment.status}`, payment)
})

frame.on('open', () => {
  console.log('Frame was opened!')
})

frame.on('close', () => {
  console.log('Frame was closed!')
})
```

See also:

* [Invoice parameters](api-reference.md#purchase)
* [`payment` Event](api-reference.md#payment-event)
* [Webhook Callbacks](api-reference.md#webhook-callbacks)

### Enable Fiat Payments
SpankPay supports fiat payments through [Wyre](https://www.sendwyre.com/). Please reach out to our customer support team to have fiat payments enabled for your site (email [support@spankchain.com](url) and include your site URL in the email.) Then, enable fiat payments by passing the `fiatEnabled: true` option:

```javascript
spankpay.show({
  apiKey: 'test_quickstart_key',
  fiatEnabled: true,
  amount: '69.69',
  currency: 'USD',
  metadata: {
    orderId: 'sc696969',
  },
  callbackUrl: 'https://pay-api.spankchain.com/quickstart/callback',
})
```

### Selecting a SpankPay UI Version
The payment frame is available in both modal and full-screen versions. The modal version will be loaded by default - you can activate the full-page flow by passing the parameter `fullscreen: true` to the `spankpay.show` call:

```javascript
spankpay.show({
  apiKey: 'test_quickstart_key',
  fullscreen: true,
  amount: '69.69',
  currency: 'USD',
  metadata: {
    orderId: 'sc696969',
  },
  callbackUrl: 'https://pay-api.spankchain.com/quickstart/callback',
})
```

#### Full-screen
<img align="right" src="https://docs.spankpay.com/images/spankpay_fullscreen.png" />

#### Modal
<img align="right" src="https://docs.spankpay.com/images/spankpay_modal.png" />


### Payment Frame Events

Events can be handled by binding to the event on the `spankpay` object:

```javascript
spankpay.on('open', () => { ... })
spankpay.on('close', () => { ... })
spankpay.on('payment', payment => { ... })
```

| Event | Description |
| ---: | :--- |
| `open` | Triggered when the frame is opened. |
| `close` | Triggered when the frame is closed. |
| `payment` | Triggered when a payment is received, after the `callback` URL has accepted the payment. See: [`payment` Event](api-reference.md#payment-event). |

### `payment` Event

The `payment` event will be triggered when a payment has been received and the `callback` url has accepted or rejected the payment.

The `payment`  argument will be a [Payment object](api-reference.md#payment), and the `status` should be checked to ensure the payment has succeeded. Note, however, that the payment will only fail if the `callback` rejects the payment \(see: [Webhook Expected Response](api-reference.md#expected-response)\).

For example:

```javascript
function onPayment(payment) {
  console.log(`Payment ${payment.status}:`, payment)
  if (payment.status == "succeeded") {
    window.location.href = '/order-complete'
  } else {
    window.location.href = '/order-failed'
  }
}
```

## Payment

A payment is created when SpankPay receives a user's payment in response to an [Invoice](api-reference.md#invoice).

<table>
  <thead>
    <tr>
      <th style="text-align:right; min-width:10rem;">Attribute</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:right"><code>createdOn</code>
      </td>
      <td style="text-align:left">The timestamp when the payment was first received. ISO 8601 format.</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>status</code>
      </td>
      <td style="text-align:left">
        <p>One of <code>&quot;pending&quot;</code>, <code>&quot;failed&quot;</code>, <code>&quot;rejected&quot;</code>,
          or <code>&quot;succeeded&quot;</code>.</p>
        <p><code>&quot;pending&quot;</code> if the payment is still being verified
          (either waiting for an onchain transaction, or waiting for result of the
          callback).</p>
        <p><code>&quot;failed&quot;</code> if the webhook failed, or if the user navigates
          away from the payment page before completing the payment.</p>
        <p><code>&quot;rejected&quot;</code> if the webhook rejected the payment.</p>
        <p> <code>&quot;succeeded&quot;</code> if the payment has been confirmed onchain
          and the callback has returned success.</p>
      </td>
    </tr>
    <tr>    We _strongly_ recommend validating webhook signatures, otherwise it could be possible for an attacker to create fake payment confirmations.
      <td style="text-align:right"><code>invoiceId</code>
      </td>
      <td style="text-align:left">The ID of the corresponding Invoice.</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>invoice</code>
      </td>
      <td style="text-align:left">The corresponding Invoice object (<a href="#invoice">see above</a>).</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>currency</code>
      </td>
      <td style="text-align:left">The invoice&apos;s currency.</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>amount</code>
      </td>
      <td style="text-align:left">The invoiced amount.</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>exchangeRate</code>
      </td>
      <td style="text-align:left">
        <p>The exchange rate used for this payment.</p>
        <p>
          <br /><code>amount = inputAmount &#x2A09; exchangeRate</code>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:right"><code>inputCurrency</code>
      </td>
      <td style="text-align:left">The input currency selected by the user (ex, &quot;ETH&quot;).</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>inputAmount</code>
      </td>
      <td style="text-align:left">The amount of the input currency that was paid, in <code>inputCurrency</code> (ex,
        &quot;0.6969&quot;).</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>outputCurrency</code>
      </td>
      <td style="text-align:left">The currency which will be credited to the merchant&apos;s account (currently
        the <code>inputCurrency</code>, but in the future this will be configurable)</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>outputAmount</code>
      </td>
      <td style="text-align:left">
        <p>The amount that will be credited to the merchant&apos;s account, in <code>outputCurrency</code>.</p>
        <p></p>
        <p><code>outputAmount = inputAmount - feeAmount</code> (adjusting for their
          respective currencies)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:right"><code>outputAmountTarget</code>
      </td>
      <td style="text-align:left">The amount (in <code>currency</code>) that will be credit to the merchant&apos;s
        account.</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>feeAmountTarget</code>
      </td>
      <td style="text-align:left">
        <p>The fee taken by SpankPay, converted to <code>currency</code>.</p>
        <p>
          <br />Note: SpankPay&apos;s fee is only taken once. It is converted to multiple
          currencies for convenience.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:right"><code>feeAmountInput</code>
      </td>
      <td style="text-align:left">The fee taken by SpankPay, converted to <code>inputCurrency</code>.</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>feeAmountOutput</code>
      </td>
      <td style="text-align:left">The fee taken by SpankPay, converted to <code>outputCurrency</code>.</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>receipt</code>
      </td>
      <td style="text-align:left">The result of the <a href="#webhook-callbacks">webhook callback</a> (or <code>null</code> when
        the webhook is being called).</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>receipt.type</code>
      </td>
      <td style="text-align:left">Always <code>&quot;webhook&quot;</code>
      </td>
    </tr>
    <tr>
      <td style="text-align:right"><code>receipt.url</code>
      </td>
      <td style="text-align:left">The URL which was called</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>receipt.status</code>
      </td>
      <td style="text-align:left">One of <code>&quot;pending&quot;</code>, <code>&quot;failed&quot;</code>,
        or <code>&quot;succeeded&quot;</code>.</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>receipt.calledOn</code>
      </td>
      <td style="text-align:left">The timestamp of the last call (ISO 8601 format).</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>receipt.responseStatus</code>
      </td>
      <td style="text-align:left">
        <p>The HTTP status code of the last request.</p>
        <p>
          <br />The request will be considered successful if the status code is <code>2XX</code>,
          permanently failed if it is <code>4XX</code>, and otherwise the callback
          will be retried.</p>
        <p>
          <br />A <code>responseStatus</code> of <code>999</code> indicates a network or other
          non-HTTP error.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:right"><code>receipt.responseHeaders</code>
      </td>
      <td style="text-align:left">The HTTP headers returned by the last request.</td>
    </tr>
    <tr>
      <td style="text-align:right"><code>receipt.responseBody</code>
      </td>
      <td style="text-align:left">
        <p>The content of the HTTP response.</p>
        <p>
          <br />A JSON object if the response has <code>Content-Type: application/json</code> ,
          and a string otherwise.</p>
        <p>
          <br />Truncated to 128kb.</p>
      </td>
    </tr>
  </tbody>
</table>## Webhook Callbacks

SpankPay will POST a message to your application server when it receives a payment, and the payment will be considered successful once it receives a response containing `{"received": true}`.

The `callback` URL is provided when the [Invoice](api-reference.md#invoice) is created, and we recommend including some metadata in the URL which your application can use to credit the appropriate order.

For example, if you assign each order an ID, the `callback` URL might be `https://your-site.com/api/spankpay/callback?order-id=sc696969`.

### Webhook Format

Webhook messages will take the following format:

``` json
POST /quickstart/callback
Content-Type: text/plain
X-SpankPay-Key: test_quickstart_key
X-SpankPay-Signature: t=1551389518&s=b613679a0814d9ec…

{
    "type": "payment",
    "payment_id": "pay_c493715653c",
    "createdOn": "1969-06-09T06:09:06.969Z",
    "invoiceId": "inv_f95d778c35f",
    "invoice": { ... },
    "amount": "69.69",
    "amountCurrency": "USD",
    "inputAmount": "0.6969",
    "inputCurrency": "ETH",
    "inputTx": "0x2144292c5ad…",
    ...
}
```

The `type` field will always be `"payment"`, although there may be other types in the future. The rest of the body will be a [Payment object](api-reference.md#payment).

Note: the `Content-Type` will be `text/plain` instead of `application/json` as might be expected. This is to ensure that web frameworks like Express do not attempt to parse the request body as JSON, and instead make the raw string available to the request handler so it can more easy check the signature.

### Expected Response

The webhook endpoint must return an HTTP 200 response with a JSON object containing `{ "received": true }`. Other metadata may optionally be included in the JSON object, and it will be returned verbatim in the Payment's `receipt.response` field.

A payment will be considered rejected if the response contains either `{ "received": false }`, or an HTTP status code of `4XX`. On a rejected payment, the user will be refunded their cryptocurrency \(less standard transaction fees\) and the payment will be marked `"rejected"`.

If the webhook endpoint returns a non-200 response, or a body that does not contain `{ "received": true }`, the webhook will be retried according to the following rules:

* 10 times, each 30 seconds apart \(ie, every 30 seconds for 5 minutes\)
* 10 times, each 5 minutes apart \(ie, every 5 minutes for 50 minutes\)

If all retries fail, the API key administrator will be notified. A webhook can be manually retried at any point through the administrative UI.

### Testing Webhooks

The Webhook Test Page \(not yet available\) can be used to send simulated webhooks.

\(this will be a page with inputs for URL, public key, secret key, various payment fields, and a button which - when clicked - will trigger a webhook call to the provided URL\).

Note: the webhook test page can only be used with testing keys \(ie, keys prefixed with "test\_"\), and all currencies will be "TEST-" currencies.

Additionally, we recommend that developers use [ngrok](https://ngrok.com/) to create a public URL which can route to their local development server. During development, your application can be configured to automatically query ngrok for the developer's current public URL:

```javascript
async function getPublicUrl() {
  if (config.PUBLIC_URL)
    return config.PUBLIC_URL

  if (!config.ENVIRONMENT != 'development')
    throw new Error('config.PUBLIC_URL has not been defined!')

  try {
    const res = await fetch('http://localhost:4040/api/tunnels')
  } catch (e) {
    throw new Error(
      'Error connecting to ngrok to fetch public URL ' +
      '(hint: did you run "ngrok"?). Original error: ' + e
    )
  }

  const obj = await res.json()
  for (const tun of obj.tunnels) {
    return tun.public_url
  }

  throw new Error(
    'Unexpected response from ngrok (tunnels found): ' +
    JSON.stringify(obj)
  )
}
```

### Security

!!! tip
    We _strongly_ recommend validating webhook signatures, otherwise it could be possible for an attacker to create fake payment confirmations.

To verify that webhooks are authentically from SpankPay, the content can be verified using the `X-SpankPay-Signature` header.

Additionally, you include query parameters in the callback URL \(for example, `…/quickstart/callback?customer-id=69420`\), there is a very small risk that a man-in-the-middle attack between SpankPay's servers and your servers could alter these query parameters. For complete correctness, we recommend verifying the requested URL against the `receipt.url` field, which will be URL originally requested by SpankPay.

#### Validating Webhook Signatures


```
javascript tab="Node + Express"
const spankpay = require('spankpay')

app.post('/spankpay/callback', (req, res) => {
    const key = req.headers['x-spankpay-key']
    if (key != process.env.SPANKPAY_API_KEY) {
        console.error(
            `Unexpected SpankPay API key: ` +
            `${key} != ${process.env.SPANKPAY_API_KEY}`
        )
        return res.status(400)
    }

    const [data, timestamp, err] = spankpay.decodeWebhook(
        process.env.SPANKPAY_API_SECRET,
        req.headers['x-spankpay-signature'],
        req.body,
    )
    if (err) {
        console.error('Error decoding SpankPay webhook:', err)
        return res.status(400)
    }

    const age = (Date.now() / 1000) - timestamp
    if (age > 60 * 10) {
        console.error(`SpankPay webhook too old (was created ${age}s ago)`)
        return res.status(400)
    }

    // ... handle webhook ...

    return res.json({ received: true })
})
```

``` 
javascript tab="Javascript (manually)"
const crypto = require('crypto')

const crypto = require('crypto')

/**
 * Decodes a SpankPay webhook, returning a triple of:
 *   [data, timestamp, error]
 *
 * Where `data` is the webhook object, and `timestamp` is the
 * call's timestamp (integer seconds since epoch, UTC).
 *
 * If an error is encountered (for example, because the
 * signature is invalid), `error` will be a non-null
 * string describing the error.
 *
 * For example:
 *   const [data, timestamp, error] = decodeSpankPayWebhook(
 *     process.env.SPANKPAY_API_SECRET,
 *     req.headers['x-spankpay-signature'],
 *     req.body,
 *   )
 */
function decodeSpankPayWebhook(secret, sig, data) {
    if (!data || data.slice(0, 1) != '{') {
        const msg = (
            `Empty or non-JSON webhook data: ` +
            JSON.stringify(shorten(data))
        )
        return [null, null, msg]
    }

    const sigData = {}
    sig.split('&').forEach(bit => {
        const [key, val] = bit.split('=')
        sigData[key] = val
    })

    const timestamp = parseInt(sigData.t)
    if (!isFinite(timestamp))
        return [null, null, `Invalid or missing timestamp: ${sig}`]

    const hash = crypto.createHmac('sha256', secret)
    hash.update(`${timestamp}.${data}`)
    const actualSig = hash.digest('hex')
    if (sigData.s !== actualSig)
        return [null, null, `Invalid signature. ${sigData.s} !== ${actualSig}`]

    let dataObj
    try {
        dataObj = JSON.parse(data)
    } catch (e) {
        return [null, null, `Error decoding JSON: ${'' + e}`]
    }

    return [dataObj, timestamp, null]
}

function shorten(s, len) {
    if (!len)
        len = 16

    if (!s || s.length <= len)
        return s

    return s.slice(0, len / 2) + '…' + s.slice(s.length - len / 2)
}

function signSpankPayData(secret, data, t) {
    if (t === undefined)
      t = parseInt(Date.now() / 1000)
    const hash = crypto.createHmac('sha256', secret)
    hash.update(`${t}.${data}`)
    return `t=${t}&s=${hash.digest('hex')}`
}

if (typeof require !== 'undefined' && require.main == module) {
  const secret = 'sk_spankpay'
  const data = '{"SpankPay": "BOOTY"}'
  const sig = signSpankPayData(secret, data, 696969)
  console.log(`Signing '${data}' with secret key '${secret}': ${sig}`)

  examples = [
    ["correctly signed", sig, data],
    ["missing timestamp", "", data],
    ["missing signature", "t=696969", data],
    ["invalid signature", "t=696969&s=invalid", data],
    ["invalid data", sig, '{"invalid": true}'],
    ["empty data", sig, null],
    ["non-JSON data", sig, "invalid"],
  ]
  for (const [name, sig, data] of examples) {
    console.log(`Decoding ${name}:`, decodeSpankPayWebhook(secret, sig, data))
  }
}
```

``` python tab="Python (manually)"
from __future__ import print_function

import sys
PY3 = sys.version_info[0] == 3

import hmac
import time
import json
import hashlib

if PY3:
    from urllib.parse import parse_qsl
else:
    from urlparse import parse_qsl


def decode_spankpay_webhook(secret, sig, data):
    """ Decodes a SpankPay webhook, returning a triple of: `(data, timestamp,
        error)`

        Where `data` is the webhook object, and `timestamp` is the call's
        timestamp (integer seconds since epoch, UTC).

        If an error is encountered (for example, because the signature is
        invalid), `error` will be a non-null string describing the error.

        For example::

            (data, timestamp, error) = decode_spankpay_webhook(
                app.config.SPANKPAY_API_SECRET,
                request.headers['x-spankpay-signature'],
                request.data,
            )
    """

    secret = to_bytes(secret)
    data = data and to_bytes(data)
    if not data or data[:1] != b"{":
        return (None, None, "Empty or non-JSON webhook data: %r" %(shorten(data), ))

    sig_data = dict(parse_qsl(sig))

    try:
        timestamp = int(sig_data.get("t"))
    except (ValueError, TypeError):
        return (None, None, "Invalid or missing timestamp: %r" %(sig, ))

    to_sign = b"%d.%s" %(timestamp, data)
    actual_sig = hmac.new(secret, to_sign, hashlib.sha256).hexdigest()
    if sig_data.get("s") != actual_sig:
        return (None, None, "Invalid signature. %r != %r" %(sig_data.get("s"), actual_sig))

    try:
        data_obj = json.loads(data)
    except ValueError as e:
        return (None, None, "Error decoding JSON: %s" %(e, ))

    return (data_obj, timestamp, None)

def to_bytes(s):
    # Note: use "ascii" instead of "utf-8" here because, in this context, we
    # should only ever get ASCII input (ie, because JSON is ASCII, not unicode)
    # and we should fail early if unicode sneaks in.
    return (
        s if isinstance(s, bytes) else
        bytes(s, "ascii") if PY3 else
        str(s)
    )

def shorten(s, n=16):
    if not s or len(s) < n:
        return s
    return s[:n//2] + b"..." + s[-n//2:]

def sign_spankpay_data(secret, data, timestamp=None):
    secret = to_bytes(secret)
    data = to_bytes(data)
    timestamp = int(timestamp if timestamp is not None else time.time())
    data = b"%d.%s" %(timestamp, data)
    sig = hmac.new(secret, data, hashlib.sha256).hexdigest()
    return "t=%s&s=%s" %(timestamp, sig)

if __name__ == '__main__':
    secret = 'sk_spankpay'
    data = '{"SpankPay": "BOOTY"}'
    sig = sign_spankpay_data(secret, data, 696969)
    print("Signing %r with secret %r: %s" %(data, secret, sig))
    examples = [
        ("correctly signed", sig, data),
        ("missing timestamp", "", data),
        ("missing signature", "t=696969", data),
        ("invalid signature", "t=696969&s=invalid", data),
        ("invalid data", sig, '{"invalid": true}'),
        ("empty data", sig, None),
        ("non-JSON data", sig, "invalid"),
    ]
    for (name, s, d) in examples:
        print("Decoding %s: %s" %(name, decode_spankpay_webhook(secret, s, d), ))
```

``` php tab="PHP (manually)"
<?php

declare(strict_types=1);

/**
 * Decodes a SpankPay webhook, returning: `[$data, $timestamp,
 * $error]`
 *
 * Where `$data` is the webhook object (as an associative array), and
 * `$timestamp` is the call's timestamp (integer seconds since epoch, UTC).
 *
 * If an error is encountered (for example, because the signature is
 * invalid), `$error` will be a non-null string describing the error.
 *
 * For example:
 *
 *     define('SPANKPAY_API_SECRET', "sk_...");
 *
 *     list($data, $timestamp, $error) = spankpay_decode_webhook(
 *         SPANKPAY_API_SECRET,
 *         $_SERVER['HTTP_X_SPANKPAY_SIGNATURE'],
 *         file_get_contents("php://input")
 *     );
 */
function spankpay_decode_webhook(string $secret, string $sig, string $data) {
    $repr = function ($val) {
        return var_export($val, true);
    };

    if (!$data || substr($data, 0, 1) !== '{') {
        $msg = "Empty or non-JSON webhook data: {$repr($data ?? spankpay_shorten($data))}";
        return [null, null, $msg];
    }

    parse_str($sig, $sig_data);

    $timestamp = $sig_data['t'] ?? null;
    if (!is_numeric($timestamp)) {
        $msg = "Invalid or missing timestamp: {$repr($timestamp)}";
        return [null, null, $msg];
    }

    $expected_sig = $sig_data['s'] ?? null;
    $to_sign = "$timestamp.$data";
    $actual_sig = hash_hmac('sha256', $to_sign, $secret);
    if (!hash_equals($expected_sig ?? "", $actual_sig)) {
        spankpay_debug("Secret key: {$repr(spankpay_shorten($secret))}; data: {$repr($to_sign)}");
        $msg = "Invalid signature. {$repr($expected_sig)} !== {$repr($actual_sig)}";
        return [null, null, $msg];
    }

    $data_arr = json_decode($data, true);
    if (!$data_arr) {
        return [null, null, "Error decoding JSON: {$repr($data)}"];
    }

    return [$data_arr, (int) $timestamp, null];
}

function spankpay_debug(string $msg) {
    if (defined('SPANKPAY_DEBUG') && SPANKPAY_DEBUG) {
        echo "SPANKPAY_DEBUG: $msg\n";
    }
}

function spankpay_shorten(string $str, integer $len = null) {
    $len = $len ?? 16;

    if (strlen($str) <= $len) {
        return $str;
    }

    return substr($str, 0, $len / 2) . "..." . substr($str, -$len / 2);
}

function spankpay_sign_data($secret, $data, $t = null) {
    $t = $t ?? time();
    $s = hash_hmac('sha256', "$t.$data", $secret);
    return "t=$t&s=$s";
}

if (!count(debug_backtrace())) {
    define('SPANKPAY_DEBUG', true);
    $secret = 'sk_spankpay';
    $data = '{"SpankPay": "BOOTY"}';
    $sig = spankpay_sign_data($secret, $data, 696969);
    echo "Signing '$data' with secret key '$secret': $sig\n";
    $examples = [
        ["correctly signed", $sig, $data],
        ["missing timestamp", "", $data],
        ["missing signature", "t=696969", $data],
        ["invalid signature", "t=696969&s=invalid", $data],
        ["invalid data", $sig, '{"invalid": true}'],
        ["non-JSON data", $sig, 'invalid']
    ];
    foreach ($examples as $example) {
        $result = spankpay_decode_webhook($secret, $example[1], $example[2]);
        echo "Decoding ${example[0]}: " . var_export($result, true) . "\n";
    }
}
```

#### Preventing Replay Attacks

To ensure your application only processes each webhook once, we recommend using the signature as a nonce. For example:

```javascript
app.post('/spankpay/callback', async (req, res) => {
    const sig = req.headers['x-spankpay-signature']
    // ... validate signature ...

    try {
        const firstUse = await redis.set(`spankpay-webhook:${sig}`, '1', {
            // The nx - Not Exists - flag ensures the key can only be set once
            nx: true,
            // The ex - EXpire - flag ensures the key will expire after an hour
            ex: 60 * 60,
        })
        if (!firstUse)
            return res.json({ received: true })

        // ... handle webhook ...
    } catch (e) {
        // If there is an error, clear the flag so that the webhook
        // will be processed on a subsequent request.
        // NOTE: your application must be careful not to leave the
        //       webhook in a partially processed state, otherwise
        //       there may be inconsistencies when it is retried.
        await redis.del(`spankpay-webhook:${sig}`)
        throw e
    }

    return res.json({ received: true })
})
```

## Public API Endpoints

#### GET Transactions:
`/public/v1/transactions?start={date}&end={date}`
Returns a list of transactions associated with the account ID. Can optionally be filtered by start and/or end date by passing them in as query params.
Will return a maximum of 2500 transactions per request - date-filtered requests should be used to aggregate larger amounts of data from this endpoint.

Example response:
```
[
  {
    publicId: 'invoice_13',
    currency: 'TEST-ETH',
    outputAmount: '417.9',
    amount: '420',
    amountReceived: '420',
    type: 'invoice',
    createdOn: '2020-08-10T16:41:48.520Z',
    status: 'succeeded',
    description: 'Test Invoice'
  },
  {
    publicId: 'invoice_8',
    currency: 'TEST-ETH',
    outputAmount: '4.179',
    amount: '4.20',
    amountReceived: '4.2',
    type: 'invoice',
    createdOn: '2020-08-10T16:41:48.498Z',
    status: 'succeeded',
    description: 'Test Invoice'
  }`
]
```
