# Sovendus Rewards Integration Guide

## Overview

Sovendus Rewards allows you to reward your customers with exclusive discounts and vouchers, enhancing customer loyalty and engagement.

### Benefits of Sovendus Rewards:

- Strengthen brand loyalty
- Enhance customer satisfaction
- Increase recommendation rates and customer activation

## Integration Steps

Follow these steps to integrate Sovendus Rewards into your shop:

### 1. Choose an Entry Point and Add HTML Markup

Decide where the rewards portal will be accessible, such as:

- "My Account" submenu
- Customer login area

Once you've chosen the location, insert the following HTML code into that section of your page:

```html
<div id="sovendus-container-1">
  <!-- The integration will load the content into this <div> element. -->
</div>
```

### 2. Configure Traffic Variables

Replace `YOUR_TRAFFIC_SOURCE_NUMBER` and `YOUR_TRAFFIC_MEDIUM_NUMBER` with the actual values provided by Sovendus.

### 3. Insert JavaScript Code

Add the following JavaScript code at the end of the `<body>` section:

```html
<!--sovendus code begin -->
<script type="text/javascript">
  window.sovIframes = window.sovIframes || [];
  window.sovIframes.push({
    trafficSourceNumber: YOUR_TRAFFIC_SOURCE_NUMBER,
    trafficMediumNumber: YOUR_TRAFFIC_MEDIUM_NUMBER,
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

### 4. Provide Consumer Data

Ensure that the `sovConsumer` object contains real customer data by dynamically replacing the placeholders with actual values from your user database.

### 5. Test the Integration

- Verify that the Sovendus banner loads correctly on the chosen page.
- Ensure customer data is passed accurately.
- Check for any errors in the browser console and resolve them if necessary.

For any issues, contact Sovendus support for assistance.

