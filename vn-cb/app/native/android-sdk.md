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
    implementation("com.sovendus:android-sdk:1.1.0")
}
```

**Requirements:** Android API 24+, Kotlin 2.1.0+, Jetpack Compose (for Compose integration)

---

## Step 2: Get Credentials

Contact Sovendus to receive:
- **Traffic Source Number** (e.g., 123)
- **Traffic Medium Number** (e.g., 456)

---

## Step 3: Add the Component

Use `VoucherBenefits` for **all** Sovendus placements. Each placement gets its own
`SovendusConfig` with a unique `trafficMediumNumber` provided by Sovendus.
The component renders whatever the API returns (banner, list, or both).

### Compose (Recommended)

```kotlin
@Composable
fun YourScreen() {
    // Each VoucherBenefits instance uses a unique trafficMediumNumber
    SovendusSDK.VoucherBenefits(
        config = SovendusConfig.Builder()
            .trafficSourceNumber(123)
            .trafficMediumNumber(456)  // Assigned by Sovendus for this placement
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
        .trafficMediumNumber(456)  // Assigned by Sovendus for this placement
        .build()
}
```

**That's it!** The component automatically displays products and/or vouchers based on what the API
returns for that `trafficMediumNumber`.

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

## Step 7: Multiple Placements (Optional)

Use multiple `VoucherBenefits` instances with different `trafficMediumNumber` values for
different placements in your app. Sovendus assigns each placement a unique medium number
and configures what content/layout to return via the backend.

```kotlin
@Composable
fun YourApp() {
    // Placement 1: Inline benefits list + banner on checkout success page
    SovendusSDK.VoucherBenefits(
        config = SovendusConfig.Builder()
            .trafficSourceNumber(123)
            .trafficMediumNumber(456)  // Inline placement
            .build()
    )

    // Placement 2: Different content/layout for another area
    // Sovendus configures what each medium number returns via the backend
    SovendusSDK.VoucherBenefits(
        config = SovendusConfig.Builder()
            .trafficSourceNumber(123)
            .trafficMediumNumber(789)  // Second placement
            .build()
    )
}
```

### Modal/Dialog Placement

```kotlin
@Composable
fun ModalPlacement() {
    var showDialog by remember { mutableStateOf(false) }
    var dataReady by remember { mutableStateOf(false) }

    // Use a dedicated trafficMediumNumber for the modal placement
    // Sovendus configures: banner-only content + overlay layout (layoutId 2)
    val modalConfig = remember {
        SovendusConfig.Builder()
            .trafficSourceNumber(123)
            .trafficMediumNumber(789)  // Sovendus-assigned for modal
            .build()
    }

    Button(onClick = { showDialog = true }) {
        Text("Show Reward")
    }

    if (showDialog) {
        val dismiss = {
            showDialog = false
            dataReady = false
        }

        Dialog(
            onDismissRequest = dismiss,
            properties = DialogProperties(usePlatformDefaultWidth = false)
        ) {
            Box(
                modifier = Modifier.fillMaxSize(),
                contentAlignment = Alignment.Center
            ) {
                // Loading state
                if (!dataReady) {
                    CircularProgressIndicator(
                        modifier = Modifier.align(Alignment.Center)
                    )
                }

                // VoucherBenefits renders whatever API returns (banner-only for this TMN)
                Box(modifier = Modifier.padding(horizontal = 24.dp)) {
                    SovendusSDK.VoucherBenefits(
                        config = modalConfig,
                        onStateChanged = { state ->
                            when (state) {
                                is LoadingState.Success -> dataReady = true
                                is LoadingState.Error -> dismiss()
                                else -> {}
                            }
                        }
                    )

                    // Close button appears once content is ready
                    if (dataReady) {
                        IconButton(
                            onClick = dismiss,
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
- Each placement uses a unique `trafficMediumNumber` assigned by Sovendus
- Sovendus configures what content and layout each medium number returns
  - **Inline placements**: May return benefits list, banner, or both
  - **Modal placements**: Typically banner-only with overlay layout (layoutId 2)
- Each instance triggers its own independent API call
- The component renders whatever the API returns

---

## Step 8: Customize Link Behavior (Optional)

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

## Step 9: Custom Fonts (Optional)

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
            .trafficMediumNumber(456)  // Sovendus-assigned for this placement
            .build()
    )
}
```

### Multiple Placements (Compose)
```kotlin
@Composable
fun CheckoutSuccessScreen() {
    // Placement 1: Inline content (Sovendus decides what to show)
    SovendusSDK.VoucherBenefits(
        config = SovendusConfig.Builder()
            .trafficSourceNumber(123)
            .trafficMediumNumber(456)  // Inline placement
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

    // Placement 2: Different content/layout for another placement
    SovendusSDK.VoucherBenefits(
        config = SovendusConfig.Builder()
            .trafficSourceNumber(123)
            .trafficMediumNumber(789)  // Different medium number = different content
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
                .trafficMediumNumber(456)  // Sovendus-assigned for this placement
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

### VoucherBenefits — The Single Component for All Placements

The `VoucherBenefits` component is the **only component you need** for all Sovendus placements.
It automatically renders whatever the API returns for the given `trafficMediumNumber`:

| API Returns   | Display                       |
|---------------|-------------------------------|
| Products only | Product recommendation list   |
| Banner only   | Voucher banner                |
| Both          | Both with correct positioning |
| Nothing       | Empty (graceful handling)     |

You don't need to handle these cases—the SDK does it automatically!

### Layout Options

The SDK supports layout modes controlled by Sovendus backend via `template.layoutId`:

**Benefits List Layouts:**

| Layout ID | Style             | Description                                      |
|-----------|-------------------|--------------------------------------------------|
| 1         | Horizontal Slider | Swipeable pager with page indicators (default)   |
| 2         | Vertical List     | Two-column cards stacked vertically              |

**Banner Layouts:**

| Layout ID | Style          | Description                                         |
|-----------|----------------|-----------------------------------------------------|
| 1         | Inline Banner  | Compact horizontal layout with image and CTA (default) |
| 2         | Overlay Banner | Centered vertical card with prominent image and CTA (ideal for modals) |

**How it works:**
- The layout is **determined by the API response** (`template.layoutId`)
- **No integrator action required**—the SDK automatically renders the correct layout
- Sovendus configures the layout per `trafficMediumNumber` via backend settings

### Multi-Placement Architecture

Use the same `VoucherBenefits` component everywhere—inline, in dialogs, in bottom sheets,
or any other placement. Each instance with a different `trafficMediumNumber` triggers its own
independent API call. Sovendus controls per placement:

- **What content** to return (banner only, list only, or both)
- **Which layout** to use (inline vs overlay banner, horizontal slider vs vertical list)
- **Themes and styling** (colors, fonts, border radius)

For modal placements, use `VoucherBenefits` with a dedicated `trafficMediumNumber`.
Sovendus configures banner-only content with overlay layout (layoutId 2) for that
medium number via the backend.

---

**Questions?** Contact your Sovendus integration specialist for support.