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

- [ ] **Obtain Sovendus Identifiers**
  Contact your account manager to receive your unique traffic source and medium numbers.

- [ ] **JavaScript Integration**
  Replace placeholder values with your actual data and add the script to your order success page.

- [ ] **Test Integration**
  Verify implementation using test orders.

---

## 🚀 How It Works

The implementation uses JavaScript on your shop's order success page. An iFrame connects to Sovendus servers to:

1. **Retrieve personalized content** - Individual reward banners and offers
2. **Embed on your page** - Seamlessly integrate with your checkout flow
3. **Track redemptions** - Monitor successful voucher usage
4. **Provide analytics** - Detailed performance insights

Your personal banner and offer list will be created by Sovendus and made available on our servers.

> [!WARNING]
> **Need Help?**
> If you have any questions or encounter issues during the process, please reach out to your Sovendus account manager for assistance.

---

## 🛠️ Implementation Guide

### Step 1: Replace Placeholder Variables

Before implementing the JavaScript code, obtain these values from your Sovendus account manager:

| Variable | Description | Example |
|----------|-------------|---------|
| `TRAFFIC_SOURCE_NUMBER` | Your unique traffic source identifier | `12345` |
| `TRAFFIC_MEDIUM_NUMBER` | Your unique traffic medium identifier | `67890` |

### Step 2: JavaScript Integration

Add this script at the beginning of the `<body>` section of your order success/thank you page:

```html
<!--sovendus code begin -->
<script type="text/javascript">
  window.sovIframes = window.sovIframes || [];
  window.sovIframes.push({
    trafficSourceNumber: YOUR_TRAFFIC_SOURCE_NUMBER,
    trafficMediumNumber: YOUR_TRAFFIC_MEDIUM_NUMBER,
    orderId: "ORDER_ID",
    orderValue: "ORDER_VALUE",
    orderCurrency: "ORDER_CURRENCY",
    usedCouponCode: "COUPON_CODE",
    integrationType: "genericScript-1.4.0",
  });
</script>
<script async="true" src="https://api.sovendus.com/sovabo/common/js/flexibleIframe.js"></script>
<!--sovendus code end -->
```

## 🔗 Additional Resources

- [📖 Parameter Documentation](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Parameter)
- [🛠️ Integration Testing](https://developer-hub.sovendus.com/Integration-Tester)

---

**🎉 Your Sovendus integration is now ready to provide customers with valuable offers and rewards!**
