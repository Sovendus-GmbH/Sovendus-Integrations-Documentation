# Sovendus Checkout Products Postback - generic frontend

## 1. Introduction

Sovendus places your offer with numerous advertising partners following an order/transaction. In order to
accurately record your orders (for billing purposes) and assign them to our advertising partners, a billing pixel must
be integrated on your order completion or thank you page.

For this purpose, Sovendus transmits a generic token value each time a landing page is called up, which forms the
basis of Sovendus' billing. The generic token value is generated dynamically for each request and changes with every
call. This token value must be cached and transmitted back to Sovendus after the order completion on your landing
page through the call of the billing pixel on your order completion or thank you page.

> [!WARNING]
> The basic prerequisite for successful cooperation is the correct return of the token value.
> If the token value is transmitted incorrectly or not at all, the Sovendus algorithm stops to work. Your offer will not be displayed within a short period of time.

## 2. External Product-ID

For each campaign, Sovendus creates a fixed Product ID, which must be permanently stored when the billing pixel is
called up. The Product ID will be communicated to you separately by Sovendus after product creation.

## 3. Token value

By default, the "sovReqToken" parameter is appended to your landing page URL on every call. The value is dynamic
and changes with every click on the offer.

From the URL, you must transfer the token value, cache it temporarily, and output it via the billing pixel after the
order is completed.

> [!EXAMPLE]
> partner.domain/path-to-landing-page?param=value&sovReqToken=1095752fb409-457a-af14-17a09e6be522

### Caption:

**Your landing page. URL:** partner.domain/path-to-landing-page?param=value \
**Token value (variable with each call):** 1095752fb409-457a-af14-17a09e6be522

> [!WARNING]
> The parameter "sovReqToken" can also be renamed.
> Please inform your Sovendus contact person about this.

## 4. The billing pixel

```html
<img
  src="https://press-order-api.sovendus.com/ext/9bc72425-902c-3abf-9db8-8b186d7cb160/image?sovReqToken=1095752fb409-457a-af14-17a09e6be522"
/>
```

### Caption:

**External Product-ID:** 9bc72425-902c-3abf-9db8-8b186d7cb160 \
**Token value (variable with each call):** 1095752fb409-457a-af14-17a09e6be522

## 5. Integration of the billing pixel

The Sovendus billing pixel must be included on your order completion or thank you page. It is mandatory to ensure that the billing pixel is only called if the user has reached your page via Sovendus.

The product ID must be permanently stored, and in addition the cached token value (by default "sovReqToken"), which was appended to the URL when your landing page was called, must be transmitted back to Sovendus via the billing pixel.

> [!WARNING]
> The "sovReqToken" parameter must be passed in the billing pixel.
> Other parameters cannot be processed by Sovendus.
