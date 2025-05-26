# Upgrading from Profity to Sovendus Voucher Network

This guide provides a clear, step-by-step process to upgrade your integration from Profity to the Sovendus Voucher Network. Follow the instructions below to ensure a smooth transition.



---

> **Note:** If you’re using **GetBack** (now called **Sovendus Optimize**) alongside Profity, there is no need to modify your GetBack integration. Only replace your Profity integration with the new Sovendus Voucher Network code.

---

## TL;DR Checklist

- [ ] **Obtain Sovendus Identifiers:**  
  - If you haven't already, contact your account manager to receive your unique Sovendus traffic source and medium numbers.

- [ ] **Choose Your Integration Method:**  
  - **Platform-Specific Plugins/Components:** Ideal for a quick, streamlined setup.
  - **Manual Integration:** Follow the detailed code examples provided below.  
  - [Full instructions are available here](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Web-Integration)

- [ ] **Remove Old Profity Code:**  
  - Delete all existing Profity conversion and landing scripts from your pages.

- [ ] **Test Your Integration:**  
  - Run tests using our integration testing app and verify that the integration has no issues.
  - [Here you can find the complete instructions for the Sovendus Testing App](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Integration-Tester/VN).

---

## 1. Previous Integration Setup

Before upgrading, your Profity integration typically looked like this:

### Order Success/Thank You Page

```html
<script async src="https://domain/clients/conversion.js?s=PROFITY_ID&ordervalue=%orderValue%&ordernumber=%orderNumber%&vouchercode=%voucherCode%"></script>
```

### Landing Page

```html
<script async src="https://domain/clients/main.js"></script>
```

**Important:** Remove these Profity scripts entirely to avoid conflicts with the new Sovendus integration.

---

## 2. Integration Options

Sovendus offers two alternatives for integration:

### A. Platform-Specific Plugins/Components

For a fast and hassle-free setup, we recommend using our platform-specific plugins or components. These options are optimized for your platform and reduce manual configuration.  
Learn more at: [Sovendus Plugins & Components](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Web-Integration).

### B. Manual Integration

#### i. Order Success/Thank You Page

1. **Insert the Container Markup**  
   Place the following `<div>` in your Order Success/Thank You page where you want the Sovendus content to be displayed. (This does not affect the positioning of the Sovendus sticky banner or overlay.)

   ```html
   <div id="sovendus-container-1">
     <!-- Sovendus integration loads content here -->
   </div>
   ```

2. **Add the Integration Script**  
   Insert the script below at the end of your `<body>` tag. Replace all placeholder values with the appropriate dynamic data from your system:

   - **YOUR_TRAFFIC_SOURCE_NUMBER:** Your Sovendus traffic source number (provided by your account manager).
   - **YOUR_TRAFFIC_MEDIUM_NUMBER:** Your Sovendus traffic medium number (provided by your account manager).
   - **Session-ID, Timestamp, Order-ID, Order Value, Order Currency, Used Coupon Code:** Replace these with dynamic values from your system.
   - **Customer Data:** Replace with the actual customer information from your system.

   ```html
   <!--sovendus code begin -->
   <script type="text/javascript">
     window.sovIframes = window.sovIframes || [];
     window.sovIframes.push({
       trafficSourceNumber: YOUR_TRAFFIC_SOURCE_NUMBER,  // Replace with your Sovendus traffic source number
       trafficMediumNumber: YOUR_TRAFFIC_MEDIUM_NUMBER,  // Replace with your Sovendus traffic medium number
       orderId: "Order-ID",                              // Replace with your dynamic order ID
       orderValue: "Order value",                        // Replace with your dynamic order value
       orderCurrency: "Order currency",                  // Replace with the currency code (e.g., "EUR")
       usedCouponCode: "Used coupon code",               // Replace with the coupon code if applicable
       iframeContainerId: "sovendus-container-1",
       integrationType: "genericScript-1.3.4"
     });
     window.sovConsumer = {
       consumerSalutation: "Salutation ('Mr.' or 'Mrs.')",    // Replace with customer salutation
       consumerFirstName: "First name",                       // Replace with customer first name
       consumerLastName: "Last Name",                         // Replace with customer last name
       consumerEmail: "client address (e-mail address)",      // Replace with customer email address
       consumerStreet: "client address (street)",             // Replace with street name
       consumerStreetNumber: "client address (house number)", // Replace with house number
       consumerZipcode: "client address (zip code)",          // Replace with postal code
       consumerCity: "client address (city)",                 // Replace with city name
       consumerCountry: "client address (Country)",           // Replace with country name
       consumerPhone: "client phone number",                  // Replace with customer phone number
       consumerYearOfBirth: "client year of birth",           // Replace with customer's birth year
       consumerDateOfBirth: "client date of birth"            // Replace with customer's full date of birth
     };
     // Append Sovendus script to the document head
     var script = document.createElement("script");
     script.type = "text/javascript";
     script.async = true;
     script.src = "https://api.sovendus.com/sovabo/common/js/flexibleIframe.js";
     document.head.appendChild(script);
   </script>
   <!--sovendus code end -->
   ```

#### ii. Landing Page Integration

Replace the old Profity landing page script with the new Sovendus landing page script:

```html
<script async="true" src="https://api.sovendus.com/js/landing.js"></script>
```

---

## 3. Testing Your Integration

After updating your integration, ensure everything works as expected by following these testing steps:

### Quick Testing Overview

- **Install the Integration Tester Extension:**  
  Available for Chrome, Edge, or Firefox, this extension allows you to run self-tests on your integration.

- **Place a Test Order:**  
  Use a voucher code and place an order on both desktop and mobile devices. Confirm that the self-test overlay displays correct order, consumer, and integration data.

- **Review Test Results:**  
  Use the overlay’s info icons to check for any errors. Once the test is successful, forward the results to your account manager.

For detailed testing instructions, refer to the [Sovendus Testing Docs](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Integration-Tester/VN).

---

By following this guide, you can successfully upgrade your integration from Profity to the Sovendus Voucher Network. Remember to remove all outdated Profity code and ensure that only the new Sovendus scripts are active. If you have any questions or require assistance, please contact your Sovendus account manager.

Happy integrating!

---
