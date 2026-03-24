## Direct Integration - Voucher Network & Checkout Benefits

##### This documentation helps you successfully integrate Sovendus Voucher Network and/or Checkout Benefits into your shop via JavaScript on your order success page.

### Overview

#### What You'll Achieve

- **💰 Increase Revenue**: Offer customers exclusive vouchers and cashback rewards
- **🔄 Boost Retention**: Keep customers engaged with personalized offers
- **📈 Track Performance**: Monitor voucher redemptions and customer engagement
- **🎁 Enhance Experience**: Provide value-added services at checkout

#### How It Works

The implementation uses JavaScript on your shop's order success page. An iFrame connects to Sovendus servers to:

1. **Retrieve personalized content** - Individual reward banners and offers
2. **Embed on your page** - Seamlessly integrate with your checkout flow
3. **Track redemptions** - Monitor successful voucher usage

Your personal banner and/or offer list will be created by Sovendus and made available on our servers.

> [!WARNING]
> **Need Help?**
> If you have any questions or encounter issues during the process, please reach out to your Sovendus account manager for assistance.

### Implementation Guide

#### Step 1: Add HTML Container (if required)

Add this HTML markup to your order success/thank you page where you want the Sovendus content to appear:

```html
<!-- Sovendus Container -->
<div id="sovendus-container-1">
  <!-- the integration loads the content into this div element -->
</div>
```

> [!WARNING]
> **Container Placement**
> The position of this container doesn't affect the placement of the Sovendus sticky banner and overlay variants if you're using one.

> [!INFO]
> **When is the container required?**
> The container is only required when one of the following is true for you:
>
> - you are using an **inline/embedded** version of our banner or product list
> - you are using a **SPA** (single page app - e.g. react, angular, vue, etc.) as the existence of this container will trigger the sovendus overlay removal process
>
> **If you are not using an inline/embedded version and not using a SPA, you can safely ignore this container.**

#### Step 2: Obtain Integration Variables

Before implementing the JavaScript code, obtain these values from your Sovendus Customer Success Manager. These will replace the corresponding placeholders within the script:

| Variable | Description | Example |
|----------|-------------|---------|
| `TRAFFIC_SOURCE_NUMBER` | Your unique Traffic Source identifier | `1234567` |
| `TRAFFIC_MEDIUM_NUMBER` | Your unique Traffic Medium identifier | `123` |

#### Step 3: Add JavaScript Integration

Add this script right after the container div above, or if not possible, at the end of the `<body>` section of your order success/thank you page:

```html
<!--sovendus code begin -->
<script type="text/javascript">
  window.sovIframes = window.sovIframes || [];
  window.sovIframes.push({
    trafficSourceNumber: YOUR_TRAFFIC_SOURCE_NUMBER,
    trafficMediumNumber: YOUR_TRAFFIC_MEDIUM_NUMBER,
    hasConsent: BOOLEAN_VALUE,
    sessionId: "SESSION_ID",
    orderId: "ORDER_ID",
    orderValue: "ORDER_VALUE",
    orderCurrency: "ORDER_CURRENCY",
    usedCouponCode: "COUPON_CODE",
    iframeContainerId: "sovendus-container-1",
    integrationType: "genericScript-1.5.0-hasConsent",
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

#### Step 4: Customize Integration Data

Replace the placeholder values (integration data, order data and customer data) in the script with actual data from your order form, database, etc.

> [!WARNING]
> **Consent Management**
> In particular, the `hasConsent` parameter is critical for GDPR and privacy compliance. This parameter allows the user's provided level of consent to easily be transferred to the Sovendus integration. Sovendus will then tailor functionality accordingly, as below

| Scenario | Value | Description |
|----------|-------------|---------|
| ✔ Consent has been explicitly provided | `hasConsent` = `true` | The Sovendus integration loads successfully with full functionality, such as additional analytics, user recognition and personalisation of offers |
| ✘ Consent has not been explicitly provided | `hasConsent` = `false` | The Sovendus integration loads successfully, but with minimal usage data (anonymised) |

The exact method you use to determine/pass this value will depend on your particular Consent Management Platform and processes. In any case, please ensure this is passed as a Boolean (`true` or `false`).

> [!INFO]
> **Parameter Documentation**
> For detailed information on all parameters, examples, and requirements, visit: [Parameter Documentation](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Parameter)

#### Step 5: Test the Integration

After implementing the code, please follow these comprehensive testing steps.

> [!INFO]
> **Testing Requirements**
> Complete testing is essential to ensure proper data transmission and banner display. Your account manager needs verification screenshots before going live.

1. **Place a Test Order**
   - Complete a real transaction on your website
   - Use realistic order data (products, quantities, pricing)
   - Include customer information in the checkout process

2. **Verify Banner Display**
   - Check your order success/thank you page immediately after checkout
   - Look for the Sovendus banner, overlay, or embedded content
   - Note the banner position and appearance

3. **Validate Data Transmission**
   - Use our [Integration Testing App](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Integration-Tester)
   - Check that consumer data and order information match your test order

4. **Test Multiple Scenarios**
   - **With coupon codes**: Test orders using discount codes
   - **Without coupon codes**: Test standard orders
   - **With consent given**: Test when users accept marketing/personalized consent
   - **Without consent**: Test when users decline or haven't provided consent

5. **Document and Share Results**
   - Take screenshots of the banner display on your success page
   - Capture screenshots of the integration testing app results
   - Send all screenshots to your Sovendus account manager
   - Include any error messages or unexpected behavior

#### Switzerland Requirements

For Switzerland, additional landing page integration is required on your home page:

##### Option 1: JavaScript Implementation

```javascript
// Add to your home/landing page
var script = document.createElement("script");
script.type = "text/javascript";
script.async = true;
script.src = "https://api.sovendus.com/js/landing.js";
document.head.appendChild(script);
```

##### Option 2: HTML Implementation

```html
<!-- Add to your home/landing page HTML -->
<script async="true" src="https://api.sovendus.com/js/landing.js"></script>
```

#### Additional Resources

- [📖 Parameter Documentation](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Parameter)
- [🛠️ Integration Testing](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Integration-Tester)

**🎉 Your Sovendus integration is now ready to provide customers with valuable offers and rewards!**
