# Sovendus Android SDK Integration Guide

## Quick Start Checklist

Get integrated in **5 minutes:**

1. ✅ Add dependency to `build.gradle.kts`
2. ✅ Get credentials from Sovendus
3. ✅ Add one component to your app
4. ✅ Test with sandbox mode
5. ✅ Go live with production credentials

---

## Step 1: Installation

Add the SDK to your `build.gradle.kts`:

```kotlin
dependencies {
    implementation("com.sovendus:android-sdk:1.0.5")
}
```

**Requirements:** Android API 26+, Kotlin 2.1.0+, Jetpack Compose (for Compose integration)

---

## Step 2: Get Credentials

Contact Sovendus to receive:
- **Traffic Source Number** (e.g., 123)
- **Traffic Medium Number** (e.g., 456)

---

## Step 3: Add the Component

### Compose (Recommended)

```kotlin
@Composable
fun YourScreen() {
    SovendusSDK.VoucherBenefits(
        config = SovendusConfig.Builder()
            .trafficSourceNumber(123)
            .trafficMediumNumber(456)
            .build()
    )
}
```

### XML (Traditional)

**Layout file:**
```xml
<com.sovendus.sdk.ui.xml.SovendusVoucherBenefitsView
    android:id="@+id/sovendus_benefits"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

**Activity/Fragment:**
```kotlin
findViewById<SovendusVoucherBenefitsView>(R.id.sovendus_benefits).apply {
    config = SovendusConfig.Builder()
        .trafficSourceNumber(123)
        .trafficMediumNumber(456)
        .build()
}
```

**That's it!** The component automatically displays products and/or vouchers based on what the API
returns.

---

## Step 4: Test Your Integration

Use sandbox mode to test without production credentials:

```kotlin
val testConfig = SovendusConfig.Builder()
    .sandbox(true)  // Uses test data
    .build()

SovendusSDK.VoucherBenefits(config = testConfig)
```

---

## Step 5: Add Personalization (Optional)

Provide customer data for personalized recommendations:

```kotlin
val config = SovendusConfig.Builder()
    .trafficSourceNumber(123)
    .trafficMediumNumber(456)
    .consumer(ConsumerData(
        firstName = "Max",
        email = "max@example.com",
        zipCode = "12345"
    ))
    .order(OrderData(
        orderId = "ORDER-123",
        orderValue = 99.99,
        orderCurrency = "EUR"
    ))
    .build()
```

**Available Fields:**

**Consumer Data:** firstName, lastName, email, zipCode, city, country, phone, dateOfBirth,
yearOfBirth
**Order Data:** orderId, orderValue, orderCurrency, usedCouponCode, sessionId

All fields are optional. Email is automatically hashed before transmission.

---

## Step 6: Handle Loading States (Optional)

### Custom Loading
```kotlin
SovendusSDK.VoucherBenefits(
    config = config,
    onStateChanged = { state ->
        when (state) {
            is LoadingState.Loading -> showYourLoader()
            is LoadingState.Success -> hideYourLoader()
            is LoadingState.Error -> showError(state.message)
        }
    }
)
```

---

## Step 7: Dialog/Overlay Display (Optional)

Use `BannerView` to show a voucher banner in a dialog or overlay:

```kotlin
@Composable
fun BannerDialog() {
    var bannerRequested by remember { mutableStateOf(false) }
    var bannerReady by remember { mutableStateOf(false) }

    // Button to trigger banner
    Button(onClick = { bannerRequested = true }) {
        Text("Show Banner")
    }

    // Show dialog immediately with loading spinner
    if (bannerRequested) {
        val dismissBanner = {
            bannerRequested = false
            bannerReady = false
        }

        Dialog(
            onDismissRequest = dismissBanner,
            properties = DialogProperties(usePlatformDefaultWidth = false)
        ) {
            Box(
                modifier = Modifier.fillMaxSize(),
                contentAlignment = Alignment.Center
            ) {
                // Show loading spinner while data is loading
                if (!bannerReady) {
                    CircularProgressIndicator()
                }

                // BannerView with custom close button
                Box(modifier = Modifier.padding(horizontal = 24.dp)) {
                    SovendusSDK.BannerView(
                        config = config,
                        onStateChanged = { state ->
                            when (state) {
                                is LoadingState.Success -> bannerReady = true
                                is LoadingState.Error -> dismissBanner()
                                else -> {}
                            }
                        }
                    )

                    // Custom close button at top-right of banner
                    if (bannerReady) {
                        IconButton(
                            onClick = dismissBanner,
                            modifier = Modifier.align(Alignment.TopEnd)
                        ) {
                            Icon(Icons.Default.Close, contentDescription = "Close")
                        }
                    }
                }
            }
        }
    }
}
```

**Key points:**
- Dialog opens immediately with a loading spinner (smooth animation)
- Banner content replaces spinner when data arrives
- Implement close button, dimming, and dismiss logic externally

---

## Step 9: Customize Link Behavior (Optional)

By default, links open in the system browser. To customize:

```kotlin
val config = SovendusConfig.Builder()
    .trafficSourceNumber(123)
    .trafficMediumNumber(456)
    .onLinkOpened { url ->
        // Custom logic (e.g., Chrome Custom Tabs, in-app browser)
        openInCustomTabs(url)
        true  // Return true if handled
    }
    .build()
```

---

## Step 10: Custom Fonts (Optional)

By default, the SDK uses the system font (Roboto on Android). You can customize the font to match your app's branding by providing a `FontFamily`.

### Bundling Fonts in Your App

1. **Add font files** to `res/font/` directory (TTF or OTF format):
   ```
   app/src/main/res/font/
   ├── brand_regular.ttf
   └── brand_bold.ttf
   ```

2. **Create a FontFamily** with the font weights you need:
   ```kotlin
   import androidx.compose.ui.text.font.Font
   import androidx.compose.ui.text.font.FontFamily
   import androidx.compose.ui.text.font.FontWeight

   val BrandFontFamily = FontFamily(
       Font(R.font.brand_regular, FontWeight.Normal),
       Font(R.font.brand_bold, FontWeight.Bold)
   )
   ```

3. **Pass the FontFamily** to the SDK config:
   ```kotlin
   val config = SovendusConfig.Builder()
       .trafficSourceNumber(123)
       .trafficMediumNumber(456)
       .fontFamily(BrandFontFamily)  // Custom font
       .build()

   SovendusSDK.VoucherBenefits(config = config)
   ```

### Using Variable Fonts

For variable fonts (single file with multiple weights):

```kotlin
import androidx.compose.ui.text.ExperimentalTextApi
import androidx.compose.ui.text.font.FontVariation

@OptIn(ExperimentalTextApi::class)
val BrandVariableFontFamily = FontFamily(
    Font(
        R.font.brand_variable,
        weight = FontWeight.Normal,
        variationSettings = FontVariation.Settings(
            FontVariation.weight(FontWeight.Normal.weight)
        )
    ),
    Font(
        R.font.brand_variable,
        weight = FontWeight.Bold,
        variationSettings = FontVariation.Settings(
            FontVariation.weight(FontWeight.Bold.weight)
        )
    )
)
```

### Default Behavior

- If `fontFamily` is **not provided** (or set to `null`), the SDK uses the system font
- The custom font applies to **all SDK text elements**: headlines, body text, buttons, and footer links
- Font weights are preserved (Normal, Medium, SemiBold, Bold) - ensure your FontFamily includes the weights you need

---

## Going to Production

1. **Replace sandbox mode** with real credentials:
   ```kotlin
   val config = SovendusConfig.Builder()
       .trafficSourceNumber(123)  // Your real traffic source
       .trafficMediumNumber(456)  // Your real traffic medium
       .debugMode(BuildConfig.DEBUG)  // Auto-disable in production
       .consumer(consumerData)
       .order(orderData)
       .build()
   ```

2. **Test thoroughly** in production environment
3. **Monitor logs** for any issues: `adb logcat | grep SovendusSDK`

---

## Complete Examples

### Minimal Integration (Compose)
```kotlin
@Composable
fun CheckoutSuccessScreen() {
    SovendusSDK.VoucherBenefits(
        config = SovendusConfig.Builder()
            .trafficSourceNumber(123)
            .trafficMediumNumber(456)
            .build()
    )
}
```

### With Personalization (Compose)
```kotlin
@Composable
fun CheckoutSuccessScreen() {
    SovendusSDK.VoucherBenefits(
        config = SovendusConfig.Builder()
            .trafficSourceNumber(123)
            .trafficMediumNumber(456)
            .consumer(ConsumerData(
                firstName = consumerFirstName,
                email = consumerEmail
            ))
            .order(OrderData(
                orderId = orderId,
                orderValue = orderTotal,
                orderCurrency = "EUR"
            ))
            .debugMode(BuildConfig.DEBUG)
            .build(),
    )
}
```

### XML Integration
```kotlin
class CheckoutSuccessActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_checkout_success)

        findViewById<SovendusVoucherBenefitsView>(R.id.sovendus_benefits).apply {
            config = SovendusConfig.Builder()
                .trafficSourceNumber(123)
                .trafficMediumNumber(456)
                .consumer(getConsumerData())
                .order(getOrderData())
                .debugMode(BuildConfig.DEBUG)
                .build()
        }
    }
}
```

---

## Troubleshooting

### Nothing appears on screen

- ✅ Check that you're using correct traffic numbers
- ✅ Verify network connectivity
- ✅ Enable debug mode to see errors: `.debugMode(true)`

### Links don't open

- ✅ Ensure device has a browser app installed
- ✅ Check if custom link handler is working correctly

### No personalized content

- ✅ Verify consumer and order data is provided
- ✅ Check that email format is valid

### Need more help?

- 📖 Check the [sample app](sample/README.md) for working examples
- 📧 Contact your Sovendus integration specialist

---

## What Gets Displayed?

### VoucherBenefits (Inline)
The `VoucherBenefits` component automatically shows:

| API Returns   | Display                       |
|---------------|-------------------------------|
| Products only | Product recommendation list   |
| Banner only   | Voucher banner                |
| Both          | Both with correct positioning |
| Nothing       | Empty (graceful handling)     |

You don't need to handle these cases - the SDK does it automatically!

### BannerView (Dialog/Overlay)
The `BannerView` component shows a voucher banner card with:
- Gift box image/animation
- Title and description
- CTA button
- Legal text

Note: Close button, dimming, and dismiss handling should be implemented externally by integrators.

---

**Questions?** Contact your Sovendus integration specialist for support.