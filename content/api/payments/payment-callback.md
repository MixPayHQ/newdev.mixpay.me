---
summary:  When creating a payment, you can pass a `callbackUrl` parameter to the MixPay API. 
---

When creating a payment, you can pass a `callbackUrl` parameter to the API. 

`callbackUrl` only supports HTTPS and has to be [URL encoded](https://www.w3schools.com/tags/ref_urlencode.ASP).

:::warning
Callback only happens when MixPay successfully received the customer's payment.
:::

## Receiving result

After payment successfully, MixPay will issue a POST request to this URL, with the following JSON content as an example:

```json
{
  "orderId": "xxxxxxxxxxxx",
  "traceId": "xxxxxxxxxxxx",
  "payeeId": "xxxxxxxxxxxx"
}
```

:::note
**For security reasons, we're not passing the payment result on purpose. You can follow the instruction below for getting the payment result.**
:::

When your callback endpoint receives a call:

- First, in your database, look for the incoming `orderId` or `traceId` value. **This step is essential, be careful anyone can post a fake value to your endpoint**;
- If the previous step has a match, then call the [payments-results API](https://mixpay.me/developers/api/payments/payments-results), and check for `status` field to be `success`;
- If the `status` field is `success`, make sure to confirm the `quoteAmount` and `quoteAssetId` are both as expected before marking your order as completed.
- Response to the callback (see below).

:::warning
**Caution**: To determine whether the payment was successful, you must check `quoteAmount` and `quoteAssetId`.
:::

## Response to the callback

Your endpoint should return an HTTP status 200 with the following JSON data:

```json
{  
  "code": "SUCCESS"
}
```

Anything `code` not equal to `SUCCESS`, MixPay server will see it as a failure, then our server will retry at 0s/15s/15s/30s/180s/1800s/1800s/1800s/1800s/3600s, a total 10 times。

:::note
You can use [postbin](https://www.toptal.com/developers/postbin/) to test it out.
:::
