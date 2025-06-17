# 🌐 Voucher Network & Checkout Benefits Integration

> [!INFO]
> **Integration Overview**
> This documentation helps you successfully integrate Sovendus Voucher Network and/or Checkout Benefits into your shop using JavaScript on the order success page.

## 🎯 What You'll Achieve

- **💰 Increase Revenue**: Offer customers exclusive vouchers and cashback rewards
- **🔄 Boost Retention**: Keep customers engaged with personalized offers
- **📈 Track Performance**: Monitor voucher redemptions and customer engagement
- **🎁 Enhance Experience**: Provide value-added services at checkout

## 📋 Integration Checklist

- **Obtain Sovendus Identifiers**
  Contact your account manager to receive your unique traffic source and medium numbers.

- **Prepare Order Success Page**
  Identify where to place the Sovendus container in your thank you page.

- **Implement HTML Markup**
  Add the container div to your order success page.

- **Configure JavaScript Integration**
  Replace placeholder values with your actual data.

- **Test Integration**
  Verify implementation using test orders.

- **Switzerland Setup** (if applicable)
  Add landing page script for Swiss customers.

---

## 🚀 How It Works

The implementation uses JavaScript on your shop's order success page. An iFrame connects to Sovendus servers to:

1. **Retrieve personalized content** - Individual reward banners and offers
2. **Embed on your page** - Seamlessly integrate with your checkout flow
3. **Track redemptions** - Monitor successful voucher usage

Your personal banner and/or offer list will be created by Sovendus and made available on our servers.

> [!WARNING]
> **Need Help?**
> If you have any questions or encounter issues during the process, please reach out to your Sovendus account manager for assistance.

---

## 🛠️ Implementation Guide

### Step 1: HTML Container

Add this HTML markup to your order success/thank you page where you want the Sovendus content to appear:

```html
<!-- Sovendus Container -->
<div id="sovendus-container-1">
  <!-- the integration loads the content into this div element -->
</div>
```

> [!WARNING]
> **Container Placement**
> The position of this container doesn't affect the placement of the Sovendus sticky banner and overlay vartiants if you're using one.

> [!INFO]
> **When is the container required?**
> The container is only required when one of the following is true for you:
>
> - you are using an inline/embedded version
> - you are using a SPA (single page app) as the existence of this container will trigger the sovendus overlay removal process. If you are not using an inline/embedded version and not using a SPA, you can safely ignore this container.

### Step 2: Replace Placeholder Variables

Before implementing the JavaScript code, obtain these values from your Sovendus account manager:

| Variable | Description | Example |
|----------|-------------|---------|
| `TRAFFIC_SOURCE_NUMBER` | Your unique traffic source identifier | `12345` |
| `TRAFFIC_MEDIUM_NUMBER` | Your unique traffic medium identifier | `67890` |

### Step 3: JavaScript Integration

Add this script right after the container div above, or if not possible, at the end of the `<body>` section of your order success/thank you page:

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

### Step 4: Customize Consumer Data

Replace the placeholder values with actual data from your order form or user database.

> [!INFO]
> **Parameter Documentation**
> For detailed information on all parameters, examples, and requirements, visit: [Parameter Documentation](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Parameter)

### Step 5: Test the Integration

After implementing the code:

1. **Place a test order** on your website
2. **Check the order success page** for the Sovendus banner or overlay
3. **Verify data transmission** using our integration tools
4. **Test different scenarios** (with/without coupons, different order values)

---

## 🇨🇭 Switzerland Requirements

For Switzerland, additional landing page integration is required on your home page:

### Option 1: JavaScript Implementation

```javascript
// Add to your home/landing page
var script = document.createElement("script");
script.type = "text/javascript";
script.async = true;
script.src = "https://api.sovendus.com/js/landing.js";
document.head.appendChild(script);
```

### Option 2: HTML Implementation

```html
<!-- Add to your home/landing page HTML -->
<script async="true" src="https://api.sovendus.com/js/landing.js"></script>
```

---

## 🔒 User Consent Integration

> [!INFO]
> **Privacy Compliance**
> While Sovendus works without cookies and processes data only to improve user experience, you may want to pass personal data only with explicit user consent.

### Method 1: Split Integration

Use consent attributes on script tags:

```html
<!-- Container (always load) -->
<div id="sovendus-container-1">
  <!-- the integration loads the content into this div element -->
</div>

<!-- Marketing consent required -->
<script type="text/javascript" my-consent-solution-attribute="Sovendus-personalized">
  // Only execute when consent is given
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
</script>

<!-- Always execute -->
<script type="text/javascript" my-consent-solution-attribute="Sovendus">
  // Always execute this part
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
    integrationType: "genericConsentScript-1.4.0",
  });

  var script = document.createElement("script");
  script.type = "text/javascript";
  script.async = true;
  script.src = "https://api.sovendus.com/sovabo/common/js/flexibleIframe.js";
  document.head.appendChild(script);
</script>
```

### Method 2: Consent Variable

Use consent state functions:

```html
<div id="sovendus-container-1">
  <!-- the integration loads the content into this div element -->
</div>

<script type="text/javascript" my-consent-solution-attribute="Sovendus">
  // Only set consumer data if consent is given
  if (isConsentAccepted("Sovendus-personalized")) {
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
  }

  // Always execute order data
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
    integrationType: "genericConsentScript-1.4.0"
  });

  var script = document.createElement("script");
  script.type = "text/javascript";
  script.async = true;
  script.src = "https://api.sovendus.com/sovabo/common/js/flexibleIframe.js";
  document.head.appendChild(script);
</script>
```

---

## 🔗 Additional Resources

- [📖 Parameter Documentation](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Parameter)
- [🛠️ Integration Testing](https://developer-hub.sovendus.com/Integration-Tester)

---

**🎉 Your Sovendus integration is now ready to provide customers with valuable offers and rewards!**
