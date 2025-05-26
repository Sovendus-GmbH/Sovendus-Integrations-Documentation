# Sovendus generic documentation for Voucher Network and Checkout Benefits

## Introduction Voucher Network

This documentation is intended to help you successfully integrate Sovendus Voucher Network and/or Checkout Benefits into your shop.

The implementation is done via JavaScript on the shop’s order success page. An iFrame is used to connect to the Sovendus servers to retrieve the individual reward banner and embed it on the page. At the same time, the successful voucher redemption is transmitted to Sovendus.

Your personal banner and/or list with special offers will be created by Sovendus and made available on our servers.

If you have any questions or encounter any issues during the process, please do not hesitate to reach out to us for assistance.

## Integrating Sovendus on Your Order Success/Thank You Page

### 1. Place the HTML Markup

Copy the HTML markup provided into the appropriate location in your order success/thank you page's HTML code. This code creates a container where the Sovendus content will be loaded. The position of this container doesn't affect the placement of the Sovendus sticky banner and overlay if you're using one.

```html
<div id="sovendus-container-1">
  <!-- the integration loads the content into this div element -->
</div>
```

### 2. Replace Placeholder Variables

Replace the placeholder variables TRAFFIC_SOURCE_NUMBER and TRAFFIC_MEDIUM_NUMBER with the actual traffic source number and traffic medium number you have received from Sovendus.

### 3. Insert JavaScript Code

Copy the HTML code below and paste it into the end of the <body> section of your order success/thank you page. This code initializes the Sovendus integration and provides necessary data about the order and the consumer.

```html
<!--sovendus code begin -->
<script type="text/javascript">
  window.sovIframes = window.sovIframes || [];
  window.sovIframes.push({
    trafficSourceNumber: YOUR_TRAFFIC_SOURCE_NUMBER,
    trafficMediumNumber: YOUR_TRAFFIC_MEDIUM_NUMBER,
    sessionId: "Session-ID",
    orderId: "Order-ID",
    orderValue: "Order value",
    orderCurrency: "Order currency",
    usedCouponCode: "Used coupon code",
    iframeContainerId: "sovendus-container-1",
    integrationType: "genericScript-1.3.4",
  });
  window.sovConsumer = {
    consumerSalutation: "Salutation ('Mr.' or 'Mrs.')",
    consumerFirstName: "First name",
    consumerLastName: "Last Name",
    consumerEmail: "client address (e-mail address)",
    consumerStreet: "client address (street)",
    consumerStreetNumber: "client address (house number)",
    consumerZipcode: "client address (zip code)",
    consumerCity: "client address (zip code)",
    consumerCountry: "client address (Country)",
    consumerPhone: "client phone number",
    consumerYearOfBirth: "client year of birth",
    consumerDateOfBirth: "client date of birth",
  };
  // Append Sovendus script to the head
  var script = document.createElement("script");
  script.type = "text/javascript";
  script.async = true;
  script.src = "https://api.sovendus.com/sovabo/common/js/flexibleIframe.js";
  document.head.appendChild(script);
</script>
<!--sovendus code end -->
```

### 4. Customize Consumer Data

Ensure that the sovConsumer and sovIframes objects contains actual data about the consumer / order. Replace the placeholder values ("Salutation", "First name", etc.) with the corresponding data retrieved from your order form or user database.

[Click here for detailed information on the parameters and which ones are required.](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Parameter)

### 5: Test the Integration

After implementing the code, test your order success/thank you page to ensure that the Sovendus integration works as expected and that the content is loaded.

## Additional steps for Switzerland

For Switzerland it is also required to add the following script to your home page / the page where users coming from Sovendus will land on:

### Variant 1 - add the script via JavaScript

To add the script via JavaScript just add the following script to the home / landing page:

```javascript
var script = document.createElement("script");
script.type = "text/javascript";
script.async = true;
script.src = "https://api.sovendus.com/js/landing.js";
document.head.appendChild(script);
```

### Variant 2 - add the script into HTML

Just add the following to the HTML of the home / landing page:

```html
<script async="true" src="https://api.sovendus.com/js/landing.js"></script>
```

## User consent based integration

While Sovendus works without cookies and processes data only to improve the user experience, some might want to only pass on personal data when the user explicitly gives consent.

The following examples illustrate how you can integrate Sovendus without customer data if no consent is given and only pass on customer data if the user has given consent.

Depending on your user consent solution you can use one of the following two variants:

### Variant 1 - Split up the integration

With most consent solutions you can add an attribute to script tags that lets you define the consent level in your consent tool. Make sure the first script block gets executed only when Marketing consent is given and the second script block should always be executed.

```html
<!--sovendus code begin -->
<div id="sovendus-container-1">
  <!-- the integration loads the content into this div element -->
</div>

<script
  type="text/javascript"
  my-consent-solution-attribute="Sovendus-personalized"
>
  // Make sure this script block gets only executed when consent is given

  window.sovConsumer = {
    consumerSalutation: "Salutation ('Mr.' or 'Mrs.')",
    consumerFirstName: "First name",
    consumerLastName: "Last Name",
    consumerEmail: "client address (e-mail address)",
    consumerStreet: "client address (street)",
    consumerStreetNumber: "client address (house number)",
    consumerZipcode: "client address (zip code)",
    consumerCity: "client address (zip code)",
    consumerCountry: "client address (Country)",
    consumerPhone: "client phone number",
    consumerYearOfBirth: "client year of birth",
    consumerDateOfBirth: "client date of birth",
  };
</script>

<script type="text/javascript" my-consent-solution-attribute="Sovendus">
  // Make sure this script block gets always executed

  window.sovIframes = window.sovIframes || [];
  window.sovIframes.push({
    trafficSourceNumber: YOUR_TRAFFIC_SOURCE_NUMBER,
    trafficMediumNumber: YOUR_TRAFFIC_MEDIUM_NUMBER,
    sessionId: "Session-ID",
    orderId: "Order-ID",
    orderValue: "Order value",
    orderCurrency: "Order currency",
    usedCouponCode: "Used coupon code",
    iframeContainerId: "sovendus-container-1",
    integrationType: "genericConsentScript-1.3.4",
  });

  // Append Sovendus script to the head
  var script = document.createElement("script");
  script.type = "text/javascript";
  script.async = true;
  script.src =
    window.location.protocol +
    "//api.sovendus.com/sovabo/common/js/flexibleIframe.js";
  document.head.appendChild(script);
</script>
<!--sovendus code end -->
```

### Variant 2 - Based on consent variable

With many consent solutions you can get the consent state with a function or variable. In the following example we use such a function to pass on customer data only when consent was given.

```html
<!--sovendus code begin -->
<div id="sovendus-container-1">
  <!-- the integration loads the content into this div element -->
</div>

<script type="text/javascript" my-consent-solution-attribute="Sovendus">
  if (isConsentAccepted("Sovendus-personalized")) {
    // Make sure this part gets only executed if consent is given.
    window.sovConsumer = {
      consumerSalutation: "Salutation ('Mr.' or 'Mrs.')",
      consumerFirstName: "First name",
      consumerLastName: "Last Name",
      consumerEmail: "client address (e-mail address)",
      consumerStreet: "client address (street)",
      consumerStreetNumber: "client address (house number)",
      consumerZipcode: "client address (zip code)",
      consumerCity: "client address (zip code)",
      consumerCountry: "client address (Country)",
      consumerPhone: "client phone number",
      consumerYearOfBirth: "client year of birth",
      consumerDateOfBirth: "client date of birth",
    };
  }
  // The rest should be executed always
  window.sovIframes = window.sovIframes || [];
  window.sovIframes.push({
    trafficSourceNumber: YOUR_TRAFFIC_SOURCE_NUMBER,
    trafficMediumNumber: YOUR_TRAFFIC_MEDIUM_NUMBER,
    sessionId: "Session-ID",
    orderId: "Order-ID",
    orderValue: "Order value",
    orderCurrency: "Order currency",
    usedCouponCode: "Used coupon code",
    iframeContainerId: "sovendus-container-1",
    integrationType: "genericConsentScript-1.3.4",
  });

  // Append Sovendus script to the head
  var script = document.createElement("script");
  script.type = "text/javascript";
  script.async = true;
  script.src =
    window.location.protocol +
    "//api.sovendus.com/sovabo/common/js/flexibleIframe.js";
  document.head.appendChild(script);
</script>
<!--sovendus code end -->
```
