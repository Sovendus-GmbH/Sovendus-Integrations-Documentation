# 🎁 Sovendus Rewards Integration

> [!INFO]
> **Rewards Portal Overview**
> Sovendus Rewards allows you to reward your customers with exclusive discounts and vouchers, enhancing customer loyalty and engagement through a dedicated rewards portal.

## 🎯 What You'll Achieve

- **🤝 Strengthen Brand Loyalty**: Build deeper customer relationships
- **😊 Enhance Customer Satisfaction**: Provide valuable rewards and benefits
- **📈 Increase Recommendation Rates**: Boost word-of-mouth marketing
- **🔄 Improve Customer Activation**: Encourage repeat purchases and engagement

## 📋 Integration Checklist

- [ ] **Choose Entry Point**: Decide where to place the rewards portal
- [ ] **Get Sovendus Credentials**: Obtain traffic source and medium numbers
- [ ] **Add HTML Container**: Insert the rewards portal container
- [ ] **Configure JavaScript**: Set up the integration script
- [ ] **Provide Customer Data**: Map user information to Sovendus format
- [ ] **Test Portal**: Verify rewards portal loads and functions correctly

---

## 🛠️ Implementation Guide

### Step 1: Choose Entry Point and Add HTML Container

Decide where the rewards portal will be accessible in your customer area:

#### 🎯 Recommended Locations

- **"My Account" submenu** - Dedicated rewards section
- **Customer dashboard** - Prominent rewards widget
- **Profile page** - Integrated rewards overview
- **Navigation menu** - "My Rewards" link

Once you've chosen the location, insert the HTML container:

```html
<!-- Sovendus Rewards Portal Container -->
<div id="sovendus-container-1">
  <!-- The integration will load the rewards content into this div element -->
</div>
```

> [!INFO]
> **Container Placement**
> The container should be placed where you want the rewards portal to appear. The content will be dynamically loaded into this div.

### Step 2: Get Your Sovendus Credentials

Contact your Sovendus account manager to receive:

| Credential | Description | Example |
|------------|-------------|---------|
| `TRAFFIC_SOURCE_NUMBER` | Your unique traffic source identifier | `12345` |
| `TRAFFIC_MEDIUM_NUMBER` | Your unique traffic medium identifier | `67890` |

### Step 3: Configure JavaScript Integration

Add this script to the end of the `<body>` section of your rewards page:

```html
<!--sovendus code begin -->
<script type="text/javascript">
  window.sovIframes = window.sovIframes || [];
  window.sovIframes.push({
    trafficSourceNumber: YOUR_TRAFFIC_SOURCE_NUMBER,
    trafficMediumNumber: YOUR_TRAFFIC_MEDIUM_NUMBER,
    iframeContainerId: "sovendus-container-1",
    integrationType: "generic-rewards-script-1.4.0",
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

### Step 4: Provide Customer Data

Replace the placeholder values with actual customer data from your user database:

| Placeholder | Description | Required | Example |
|-------------|-------------|----------|---------|
| `TRAFFIC_SOURCE_NUMBER` | Your Sovendus traffic source number | ✅ | `12345` |
| `TRAFFIC_MEDIUM_NUMBER` | Your Sovendus traffic medium number | ✅ | `67890` |
| `SALUTATION` | Customer salutation | ❌ | `Mr.` or `Mrs.` |
| `FIRST_NAME` | Customer first name | ❌ | `John` |
| `LAST_NAME` | Customer last name | ❌ | `Doe` |
| `EMAIL_ADDRESS` | Customer email address | ✅ | `john@example.com` |
| `STREET_ADDRESS` | Customer street address | ❌ | `Main Street` |
| `STREET_NUMBER` | House/building number | ❌ | `123` |
| `ZIP_CODE` | Postal code | ❌ | `12345` |
| `CITY` | Customer city | ❌ | `Berlin` |
| `COUNTRY_CODE` | Country code | ❌ | `DE` |
| `PHONE_NUMBER` | Customer phone number | ❌ | `+49123456789` |
| `YEAR_OF_BIRTH` | Birth year | ❌ | `1990` |
| `DATE_OF_BIRTH` | Full birth date | ❌ | `1990-01-01` |

> [!WARNING]
> **Dynamic Data Required**
> Ensure the `sovConsumer` object contains real customer data by dynamically replacing placeholders with actual values from your user database or session.

### Step 5: Test the Integration

#### ✅ Testing Checklist

- [ ] **Portal Loading**: Verify the Sovendus rewards portal loads correctly
- [ ] **Customer Data**: Ensure customer information is passed accurately
- [ ] **Console Errors**: Check browser console for JavaScript errors
- [ ] **Responsive Design**: Test on different screen sizes and devices
- [ ] **User Experience**: Verify smooth navigation and functionality

#### 🔍 Troubleshooting

**Portal not loading**

- Check if container div is present and has correct ID
- Verify traffic source and medium numbers are correct
- Ensure script loads without errors

**Customer data issues**

- Verify all required fields are populated
- Check data format matches expected values
- Ensure customer is logged in when accessing rewards

**JavaScript errors**

- Check browser console for error messages
- Verify script URL is accessible
- Ensure no conflicts with other scripts

---

## 🎁 Customer Benefits

- **Exclusive Rewards**: Access to special discounts and offers
- **Easy Redemption**: Simple voucher claiming process

---

## 📞 Support

Need help with your rewards integration?

- **Technical Support**: Contact your Sovendus account manager
- **Documentation**: [Parameter Documentation](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Parameter)
- **Testing Tools**: Use browser developer tools for debugging

---

**🎉 Your customers will love their new rewards portal experience!**
