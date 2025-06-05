# Shopify Sovendus Voucher Network & Checkout Benefits Integration Documentation

## This documentation is only for the old Shopify version

> [!WARNING]
> The integration only works if you have NOT upgraded your thank you page and order status page to the "Checkout extensibility" version, the [docs for the new version can be found here](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Web-Integration/Shopify-App-(new-Version))

> [!WARNING]
> On August 2025 this integration method will be not supported anymore, make sure to get in touch with us regarding the upgrade process in time!
>
### How to check if you are using the old Shopify checkout version

To check your thank you / order status page version go to "Settings -> Checkout", if you are on the OLD version it should look like on the screenshot below:

![Old Shopify Checkout Version](https://raw.githubusercontent.com/Sovendus-GmbH/Sovendus-Integrations-Documentation/main/docs/vn-cb/web/shopify-script-old/old-shopify-checkout-version.png)

## Create discount codes in bulk

If you use the Voucher Network, it's recommended to use a tool to create voucher codes in bulk \
You can use for example [Bulk Discounts](https://apps.shopify.com/bulk-discounts) to create multiple discount codes at once

## Add Sovendus script

1. Copy the [Shopify script from here](https://github.com/Sovendus-GmbH/Sovendus-Integrations-Documentation/blob/main/docs/vn-cb/web/shopify-script-old/shopify.template.html) and replace YOUR_SOURCE_NUMBER and YOUR_MEDIUM_NUMBER with the one we provide you in your integration documentation. Make sure to use the right country code. If you are using sovendus for multiple countries, make sure you define YOUR_SOURCE_NUMBER and YOUR_MEDIUM_NUMBER in the script as well.
2. In your Shopify backend go to: Settings -> Checkout -> Order status page -> Additional scripts - paste the script and save it.

## Additional steps for Switzerland

For Switzerland it is also required to complete the following steps.

1. In your Shopify backend go to: Settings -> Customer events -> Click on Add custom pixel
2. Add a name e.g. Sovendus Landing Page and click on Add pixel
3. Paste the following code, click on Save on the top right and then on connect

```javascript
var script = document.createElement("script");
script.type = "text/javascript";
script.async = true;
script.src = "https://api.sovendus.com/js/landing.js";
document.head.appendChild(script);
```
