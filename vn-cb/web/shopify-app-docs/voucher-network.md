# Sovendus App for Shopify

## ✅ Prerequisites

### Verify Your Checkout Version

<Alert title="New Shopify Checkout Required" type="warning"> 
    This app only works with Shopify's new "Checkout Extensibility" version. [View docs for the old version](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Web-Integration/Shopify-Integration-(old-version))
</Alert>

Check if you're using the new checkout: **Settings → Checkout**

![New Shopify Checkout Version](https://raw.githubusercontent.com/Sovendus-GmbH/Sovendus-Integrations-Documentation/main/vn-cb/web/shopify-app-docs/new-shopify-checkout-version.png)

### Optional: Bulk Discount Codes

<Alert title="Voucher Network Users" type="info"> 
    Consider using [Bulk Discounts](https://apps.shopify.com/bulk-discounts) to create multiple discount codes efficiently.
</Alert>
---

## 🚀 Setup Process

### Step 1: Installation Link

- **Contact your Sovendus account manager** with your website domain and let them know you want to use the Shopify Sovendus app
- **Receive the installation link** your Sovendus account manager will provide you with an installation link

<Alert title="Installation Link Validity" type="info"> 
    The installation link is valid for all your stores linked to the same Shopify organization / main account.
    If you need to install the app on multiple organizations, please contact your account manager with the shops domains to request additional installation links
</Alert>

### Step 2: Install the App

- Open the installation URL provided by Sovendus
- Click **Install** to add the app to your store

### Step 3: Configure Settings

<Alert title="Voucher Network / Checkout Benefits Configuration" type="info"> 
    1. Navigate to the Sovendus App in your Shopify admin
    2. Click on the configure button in the Voucher Network & Checkout Benefits section
    3. Scroll down to the country settings and enter the traffic source and medium numbers provided by Sovendus for your country/countries
    4. To apply the change simply close the settings overlay on the green exit button on the top right
</Alert>

<Alert title="Sovendus Widget Required" type="warning"> 
    1. Go to "Settings" -> "Checkout" -> click on "Customize" to customize your checkout pages
    2. Click on "Checkout" in the top middle and then on "Thank you"
    3. Click on "Add App block" on the bottom left, then on "Sovendus App" and then "Save"
    4. Ensure the banners position above eventual other widgets
    ![Shopify App Positioning](https://raw.githubusercontent.com/Sovendus-GmbH/Sovendus-Integrations-Documentation/main/vn-cb/web/shopify-app-docs/thank-you-position.png)
    5. Click on "Thank you" in the top middle and then on "Order status"
    6. Click on "Add App block" on the bottom left, then on "Sovendus App" and then "Save"
    7. Ensure the banners position above eventual other widgets
    ![Shopify App Positioning](https://raw.githubusercontent.com/Sovendus-GmbH/Sovendus-Integrations-Documentation/main/vn-cb/web/shopify-app-docs/order-status-position.png)
</Alert>

### Step 4: Testing & Verification

Testing requirements depend on your enabled Sovendus products:

#### 🎯 Voucher Network / Checkout Benefits

<Alert title="Testing App Note" type="info"> 
    The new Shopify app isn't supported by the Sovendus testing app yet. Use visual verification as described below instead:
</Alert>

- 🛒 **Place a test order** in your store
- 👀 **Check the "Thank You" page** for the Sovendus banner
- ✅ **Verify positioning** matches the example below
- 📸 **Send a screenshot** of the "Thank You" page to your Sovendus account manager similar to the one below:

    ![Shopify App Integration Example](https://raw.githubusercontent.com/Sovendus-GmbH/Sovendus-Integrations-Documentation/main/vn-cb/web/shopify-app-docs/Shopify-App.png)