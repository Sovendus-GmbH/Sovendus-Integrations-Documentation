# 🛍️ Sovendus App for Shopify

## ✅ Prerequisites

### Verify Your Checkout Version

> [!WARNING]
> **New Shopify Checkout Required**
> This app only works with Shopify's new "Checkout Extensibility" version. [View docs for the old version](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Web-Integration/Shopify-Integration-(old-version))

Check if you're using the new checkout: **Settings → Checkout**

![New Shopify Checkout Version](https://raw.githubusercontent.com/Sovendus-GmbH/Sovendus-Integrations-Documentation/main/vn-cb/web/shopify-app-docs/new-shopify-checkout-version.png)

### Optional: Bulk Discount Codes

> [!INFO]
> **Voucher Network Users**
> Consider using [Bulk Discounts](https://apps.shopify.com/bulk-discounts) to create multiple discount codes efficiently.

---

## 🚀 Setup Process

### Step 1: Installation Link

- **Contact your Sovendus account manager** with your website domain and let them know you want to use the Shopify Sovendus app
- **Receive the installation link** your Sovendus account manager will provide you with an installation link

> [!INFO]
> **Installation Link Validity**
> The installation link is valid for all your stores linked to the same Shopify organization / main account.
> If you need to install the app on multiple organizations, please contact your account manager with the shops domains to request additional installation links

### Step 2: Install the App

- Open the installation URL provided by Sovendus
- Click **Install** to add the app to your store

### Step 3: Configure Settings

#### Voucher Network / Checkout Benefits

> [!INFO]
> **Voucher Network / Checkout Benefits Configuration**
>
> 1. Navigate to the Sovendus App in your Shopify admin
> 2. Click on the configure button in the Voucher Network & Checkout Benefits section
> 3. Scroll down to the country settings and enter the traffic source and medium numbers provided by Sovendus for your country/countries
> 4. To apply the change simply close the settings overlay on the green exit button on the top right

> [!WARNING]
> **Sovendus Widget Required**
>
> 1. Go to "Settings" -> "Checkout" -> click on "Customize" to customize your checkout pages
> 2. Click on "Checkout" in the top middle and then on "Thank you"
> 3. Click on "Add App block" on the bottom left, then on "Sovendus App" and then "Save"
> 4. Ensure the banners position above eventual other widgets
    ![Shopify App Positioning](https://raw.githubusercontent.com/Sovendus-GmbH/Sovendus-Integrations-Documentation/main/vn-cb/web/shopify-app-docs/thank-you-position.png)
> 5. Click on "Thank you" in the top middle and then on "Order status"
> 6. Click on "Add App block" on the bottom left, then on "Sovendus App" and then "Save"
> 7. Ensure the banners position above eventual other widgets
    ![Shopify App Positioning](https://raw.githubusercontent.com/Sovendus-GmbH/Sovendus-Integrations-Documentation/main/vn-cb/web/shopify-app-docs/order-status-position.png)

#### Checkout Products

Just Enable the "Checkout Products" toggle in the app settings and you are done.

#### Optimize

> [!INFO]
> **Optimize Configuration**
>
> 1. Enter your optimize ID provided by Sovendus in the app settings, depending on your setup, you might want to set a different ID for each country
> 2. To apply the change simply close the settings overlay on the green exit button on the top right

> [!WARNING]
> **Storefront Script Required**
>
> 1. In your Shopify backend click on "Online Store" and then on "Themes"
> 2. On your current theme click on "Customize"
> 3. Click on "App embeds" on the left in the sidebar
> 4. Enable the Storefront Script for the Sovendus App and click on "Save"

### Step 4: Testing & Verification

Testing requirements depend on your enabled Sovendus products:

#### 🎯 Voucher Network / Checkout Benefits

> [!INFO]
> **Testing App Note**
> The new Shopify app isn't supported by the Sovendus testing app yet. Use visual verification as described below instead:

- 🛒 **Place a test order** in your store
- 👀 **Check the "Thank You" page** for the Sovendus banner
- ✅ **Verify positioning** matches the example below
- 📸 **Send a screenshot** of the "Thank You" page to your Sovendus account manager similar to the one below:

    ![Shopify App Integration Example](https://raw.githubusercontent.com/Sovendus-GmbH/Sovendus-Integrations-Documentation/main/vn-cb/web/shopify-app-docs/Shopify-App.png)

#### 🛍️ Checkout Products

- ❌ No testing is required from your side
- 📞 Simply inform your Sovendus account manager once Checkout Products is enabled in the app settings
- ✅ Your account manager will handle any necessary verification

#### 🚀 Optimize

- ❌ Testing is handled entirely by your Sovendus account manager
- 📞 Simply inform your Sovendus account manager once Optimize is configured in the app settings
- ✅ Your account manager will conduct all necessary tests and optimizations

---

**🎉 Your Sovendus integration is ready to boost customer engagement and drive sales!**
