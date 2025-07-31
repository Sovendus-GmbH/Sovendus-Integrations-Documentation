
# 🌐 Checkout Incentivise Integration Guide

> [!INFO]
> **Integration Overview**
> This documentation helps you successfully integrate Checkout Incentivise into your shop using JavaScript.


## 🛠️ Implementation Guide

### Step 1: HTML Container

Add this HTML markup to your page where you want the Sovendus content to appear:

```html
<!-- Sovendus Container -->
<div id="sovendus-container-1">
  <!-- the integration loads the content into this div element -->
</div>
```

### Step 2: Replace Placeholder Variables

Before implementing the JavaScript code, obtain these values from your Sovendus account manager:

| Variable | Description | Example |
|----------|-------------|---------|
| `TRAFFIC_SOURCE_NUMBER` | Your unique traffic source identifier | `1234567` |
| `TRAFFIC_MEDIUM_NUMBER` | Your unique traffic medium identifier | `123` |

### Step 3: JavaScript Integration (incent page)

Add this script right after the container div above, or if not possible, at the end of the `<body>` section of your page:

```html
<!--sovendus code begin -->
<script type="text/javascript">
  window.sovIframes = window.sovIframes || [];
  window.sovIframes.push({
    trafficSourceNumber: YOUR_TRAFFIC_SOURCE_NUMBER,
    trafficMediumNumber: YOUR_TRAFFIC_MEDIUM_NUMBER,
    sessionId: "SESSION_ID",
    orderId: "ORDER_ID",
    orderValue: "ORDER_VALUE",
    orderCurrency: "ORDER_CURRENCY",
    usedCouponCode: "COUPON_CODE",
    iframeContainerId: "sovendus-container-1",
    integrationType: "genericScript-1.4.0",
    uniqueId: "",
    page: "incent",
  });

  window.sovConsumer = {
    consumerSalutation: "SALUTATION",
    consumerFirstName: "FIRST_NAME",
    consumerLastName: "LAST_NAME",
    consumerEmail: "EMAIL_ADDRESS",
    consumerStreet: "STREET_ADDRESS",
    consumerStreetNumber: "STREET_NUMBER",
    consumerZipcode: "ZIP_CODE",
    consumerCity: "CITY",
    consumerCountry: "COUNTRY_CODE",
    consumerPhone: "PHONE_NUMBER",
    consumerYearOfBirth: "YEAR_OF_BIRTH",
    consumerDateOfBirth: "DATE_OF_BIRTH",
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

#### Customize Consumer Data

Replace the placeholder values:

| Variable | Description | Example |
|----------|-------------|---------|
| `page` | Page where we offer the product list | `incent` |
| `uniqueId` | Your unique user identifier (uuid format) | `a81bc81b-dead-4e5d-abff-90865d1e13b1` |

> [!INFO]
> **Parameter Documentation**
> For detailed information on all parameters, examples, and requirements, visit: [Parameter Documentation](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Parameter)

### Step 3: JavaScript Integration (confirm page)

Add this script right after the container div above, or if not possible, at the end of the `<body>` section of your page:

```html
<!--sovendus code begin -->
<script type="text/javascript">
  window.sovIframes = window.sovIframes || [];
  window.sovIframes.push({
    trafficSourceNumber: YOUR_TRAFFIC_SOURCE_NUMBER,
    trafficMediumNumber: YOUR_TRAFFIC_MEDIUM_NUMBER,
    sessionId: "SESSION_ID",
    orderId: "ORDER_ID",
    orderValue: "ORDER_VALUE",
    orderCurrency: "ORDER_CURRENCY",
    usedCouponCode: "COUPON_CODE",
    iframeContainerId: "sovendus-container-1",
    integrationType: "genericScript-1.4.0",
    uniqueId: "",
    page: "confirm",
  });

  window.sovConsumer = {
    consumerSalutation: "SALUTATION",
    consumerFirstName: "FIRST_NAME",
    consumerLastName: "LAST_NAME",
    consumerEmail: "EMAIL_ADDRESS",
    consumerStreet: "STREET_ADDRESS",
    consumerStreetNumber: "STREET_NUMBER",
    consumerZipcode: "ZIP_CODE",
    consumerCity: "CITY",
    consumerCountry: "COUNTRY_CODE",
    consumerPhone: "PHONE_NUMBER",
    consumerYearOfBirth: "YEAR_OF_BIRTH",
    consumerDateOfBirth: "DATE_OF_BIRTH",
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

#### Customize Consumer Data

Replace the placeholder values:

| Variable | Description | Example |
|----------|-------------|---------|
| `page` | Confirmation of purchase | `confirm` |
| `uniqueId` | Your unique user identifier (uuid format) | `a81bc81b-dead-4e5d-abff-90865d1e13b1` |

> [!INFO]
> **Parameter Documentation**
> For detailed information on all parameters, examples, and requirements, visit: [Parameter Documentation](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Parameter)

---

## 🔗 Additional Resources

- [📖 Parameter Documentation](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Parameter)
- [🛠️ Integration Testing](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Integration-Tester)

---

**🎉 Your Sovendus integration is now ready to provide customers with valuable offers and rewards!**
