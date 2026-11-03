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
    implementation("com.sovendus:android-sdk:1.4.1")
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
yearOfBirth, hasConsent
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

> **Important:** The SDK supports dynamic font weight customization via the Sovendus API theme.
> For font weight changes to render correctly, your app must **bundle all font weight files** that may be requested.
> If only Regular (400) and Bold (700) are bundled but the API requests SemiBold (600), the system will fall back to the closest available weight.

**Recommended weights to bundle:** Light (300), Normal (400), Medium (500), SemiBold (600), Bold (700)

1. **Add font files** to `res/font/` directory (TTF or OTF format):
   ```
   app/src/main/res/font/
   ├── brand_light.ttf       (300)
   ├── brand_regular.ttf     (400)
   ├── brand_medium.ttf      (500)
   ├── brand_semibold.ttf    (600)
   └── brand_bold.ttf        (700)
   ```

2. **Create a FontFamily** with all the weights:
   ```kotlin
   import androidx.compose.ui.text.font.Font
   import androidx.compose.ui.text.font.FontFamily
   import androidx.compose.ui.text.font.FontWeight

   val BrandFontFamily = FontFamily(
       Font(R.font.brand_light, FontWeight.Light),
       Font(R.font.brand_regular, FontWeight.Normal),
       Font(R.font.brand_medium, FontWeight.Medium),
       Font(R.font.brand_semibold, FontWeight.SemiBold),
       Font(R.font.brand_bold, FontWeight.Bold),
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

Variable fonts (single file with multiple weights) are the easiest way to support all weights:

```kotlin
import androidx.compose.ui.text.ExperimentalTextApi
import androidx.compose.ui.text.font.FontVariation

@OptIn(ExperimentalTextApi::class)
val BrandVariableFontFamily = FontFamily(
    Font(R.font.brand_variable, weight = FontWeight.Light,
        variationSettings = FontVariation.Settings(FontVariation.weight(300))),
    Font(R.font.brand_variable, weight = FontWeight.Normal,
        variationSettings = FontVariation.Settings(FontVariation.weight(400))),
    Font(R.font.brand_variable, weight = FontWeight.Medium,
        variationSettings = FontVariation.Settings(FontVariation.weight(500))),
    Font(R.font.brand_variable, weight = FontWeight.SemiBold,
        variationSettings = FontVariation.Settings(FontVariation.weight(600))),
    Font(R.font.brand_variable, weight = FontWeight.Bold,
        variationSettings = FontVariation.Settings(FontVariation.weight(700))),
)
```

### Default Behavior

- If `fontFamily` is **not provided** (or set to `null`), the SDK uses the system font
- The custom font applies to **all SDK text elements**: headlines, body text, buttons, and footer links
- **Font size, weight, and line height** are controlled by the Sovendus API theme and applied automatically
- Font weights are dynamic — the API may request any weight from 100–900. Ensure your FontFamily includes all weights you want to support
- If a requested weight isn't bundled, the system falls back to the closest available weight

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

## Upgrading to 1.4.0

Drop-in for most integrations — the Layout 1 and component sizing changes are API-driven and
need no code change. Two things to check:

- **`onInfoClick` is deprecated** on `StandaloneComposables.VoucherBenefits` and
  `StandaloneComposables.BenefitsList`. The info icon it belonged to was removed from
  Layout 1, so the callback is never invoked. A no-op overload is kept, so existing code
  still compiles with a deprecation warning — drop the argument and use the overload
  without it
- **Verify your surrounding layout doesn't impose a fixed height** on the component. Cards
  now grow to fit their content, so a fixed-height parent can clip them — see
  [Component Sizing](#component-sizing)

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

### Cards look cut off, clipped, or overlap other views

Since 1.4.0 cards grow to fit their content instead of using a fixed height, so a container
that constrains them will clip the extra height:

- ✅ Give the component an unbounded height — `wrap_content` in XML, no `Modifier.height(...)`
  in Compose
- ✅ Check for a fixed-height or clipping parent (fixed-size `Box`, `clipToOutline`, a
  collapsed `ConstraintLayout` chain)
- ✅ Test at a large system font scale — that's when cards get tallest
- 📖 See [Component Sizing](#component-sizing) for the full behavior

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
| 1         | Horizontal Slider | Swipeable pager with page indicators; offer advantages render inline in the card (default) |
| 2         | Vertical List     | Two-column cards stacked vertically              |
| 3         | Carousel          | Stacked card deck with arrow navigation and animated transitions |

> **Changed in 1.4.0 — Layout 1 (Horizontal Slider):** the info icon and its slide-up
> advantages overlay were removed. `offerAdvantages.mobile` now renders directly in the card
> body, so all three list layouts present advantages the same way. `offerAdvantages.desktop`
> is not used on Android, so products without `offerAdvantages.mobile` render without
> advantage text. This is API-driven and requires no integrator action — the only source-level
> impact is that `onInfoClick` is deprecated and never invoked (see
> [Upgrading to 1.4.0](#upgrading-to-140)).

**Banner Layouts:**

| Layout ID | Style          | Description                                         |
|-----------|----------------|-----------------------------------------------------|
| 1         | Inline Banner  | Compact horizontal layout with image and CTA (default) |
| 2         | Overlay Banner | Centered vertical card with prominent image and CTA (ideal for modals) |

**How it works:**
- The layout is **determined by the API response** (`template.layoutId`)
- **No integrator action required**—the SDK automatically renders the correct layout
- Sovendus configures the layout per `trafficMediumNumber` via backend settings

### Component Sizing

**As of 1.4.0, SDK cards and buttons size themselves to their content.** They grow as tall
as the text inside them needs to be, and their previous fixed heights are kept only as
*minimums* so sparse content still renders consistently. (Product artwork keeps a fixed
size — only the text-driven parts of a card grow.)

This matters because the SDK's text can grow in two ways you don't control:

1. **The user's system font scale** (Settings → Display → Font size), up to 2x on recent
   Android versions
2. **Font sizes sent in the Sovendus API theme**, which Sovendus can change per placement
   without an app update

Previously a card kept its hardcoded height and enlarged text was clipped. Now the card
grows instead, so the content stays readable in full.

**What this means for your layout:**

- ✅ **Give the component a `wrap_content` / unbounded height.** In XML use
  `android:layout_height="wrap_content"` (as in the snippets above). In Compose, don't
  constrain it with `Modifier.height(...)` or place it in a fixed-height parent
- ✅ **Expect the height to change at runtime** — after data loads, and when the user
  changes their system font size. If you measure or animate around the component, read its
  height instead of hardcoding one
- ⚠️ **Avoid clipping parents.** A fixed-height `Box`, a collapsed `ConstraintLayout` chain,
  or a parent with `clipToOutline` can cut off a card that grew

**Sizing details per component:**

| Component                      | Behavior                                        |
|--------------------------------|-------------------------------------------------|
| Slider card (layout 1)         | Grows with its content; 210dp minimum. All cards in one slider share the height of the tallest, measured rather than guessed, so pages stay uniform |
| Vertical list / carousel cards | Grow with their content                         |
| CTA buttons (all layouts)      | Grow with their label; 40dp minimum. The label autosizes down only to fit the button's *width* on a single line |
| Card titles                    | Up to 2 lines, then ellipsized                  |
| Offer advantages               | Up to 3 lines, then ellipsized                  |

The line caps are a backstop against unusually long copy — within them the card lengthens
rather than cutting text off mid-line, and anything beyond them is visibly ellipsized rather
than silently dropped.

### Multi-Placement Architecture

Use the same `VoucherBenefits` component everywhere—inline, in dialogs, in bottom sheets,
or any other placement. Each instance with a different `trafficMediumNumber` triggers its own
independent API call. Sovendus controls per placement:

- **What content** to return (banner only, list only, or both)
- **Which layout** to use (inline vs overlay banner, horizontal slider vs vertical list vs carousel)
- **Themes and styling** (colors, fonts, border radius)

For modal placements, use `VoucherBenefits` with a dedicated `trafficMediumNumber`.
Sovendus configures banner-only content with overlay layout (layoutId 2) for that
medium number via the backend.

---

**Questions?** Contact your Sovendus integration specialist for support.