# Sovendus iOS SDK Integration Guide

## Quick Integration Checklist

**Get up and running in 5 minutes:**

**Add dependency** via SPM or drop-in  
**Get your credentials** from Sovendus  
**Add SDK component** to your app  
**Test integration** with sandbox mode  
**Configure production** with real credentials

---

## Step 1: Installation

### Option A: Swift Package Manager (Recommended)

Add the SDK dependency to your Xcode project:

1. Open Xcode project
2. Go to **File > Add Package Dependencies**
3. Enter repository URL: `https://github.com/Sovendus-GmbH/sovendus-sdk-ios`
4. Select version and add to target

### Option B: Local Drop-In

Download and drag the `SovendusSDK` folder into your Xcode project.

**Requirements:**
- iOS 16.0+
- Xcode 15.0+
- Swift 5.7+

---

## Step 2: Get Your Credentials

**Contact Sovendus to receive:**
- **Traffic Source Number** - (e.g. 1234)
- **Traffic Medium Number** - (e.g. 5678)

**Integration Support:** Contact your Sovendus representative for credentials and support.

---

## Step 3: Add the Component

Create your SDK configuration:

```swift
let config = SovendusConfig(
    trafficSourceNumber: 1234,
    trafficMediumNumber: 5678,
    consumerData: nil,
    orderData: nil,
    sandbox: true, // Omit or set to false for production
    enableDebugLogs: true,  // Omit or set to false for production
    onLinkOpened: { url in
        // Optional: Custom link handling
        // If omitted, uses UIApplication.shared.open(url)
        print("Opening: \(url)")
        UIApplication.shared.open(url)
    }
)
```

### SwiftUI

```swift
import SwiftUI
import SovendusSDK

struct ContentView: View {
    private let config = SovendusConfig(
        trafficSourceNumber: 1234,
        trafficMediumNumber: 5678,
        consumerData: ConsumerData(
            firstName: "Max",
            lastName: "Mustermann",
            email: "max@example.com",
            country: "DE"
        ),
        orderData: OrderData(
            sessionId: "SESSION-001",
            orderId: "ORDER-001",
            orderCurrency: "EUR",
            usedCouponCode: nil,
            orderValue: 99.99
        ),
        sandbox: true,
        enableDebugLogs: true
    )
    
    var body: some View {
        NavigationView {
            ScrollView {
                VStack(spacing: 20) {
                    Text("Welcome!")
                        .font(.title)
                    
                    VoucherBenefitsView(
                        config: config,
                        onStateChanged: { state in
                            switch state {
                            case .loading:
                                print("Loading benefits...")
                            case .success:
                                print("Benefits loaded successfully")
                            case .error(let error):
                                print("Error loading benefits: \(error)")
                            }
                        }
                    )
                }
                .padding()
            }
            .navigationTitle("My App")
        }
    }
}
```

### UIKit

```swift
import UIKit
import SwiftUI
import SovendusSDK

class ViewController: UIViewController {
    private lazy var config = SovendusConfig(
        trafficSourceNumber: 1234,
        trafficMediumNumber: 5678,
        consumerData: ConsumerData(
            firstName: "Max",
            lastName: "Mustermann",
            email: "max@example.com"
        ),
        sandbox: true,
        enableDebugLogs: true
    )
    
    override func viewDidLoad() {
        super.viewDidLoad()
        setupSovendusViews()
    }
    
    private func setupSovendusViews() {
        let stackView = UIStackView()
        stackView.axis = .vertical
        stackView.spacing = 16
        stackView.translatesAutoresizingMaskIntoConstraints = false
        
        view.addSubview(stackView)
        
        NSLayoutConstraint.activate([
            stackView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 20),
            stackView.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 16),
            stackView.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -16)
        ])
        
        let stateHandler: (SovendusState) -> Void = { state in
            print("Sovendus state: \(state)")
        }
        
        // Add Sovendus components
        let voucherBenefitsView = UIHostingController(
            rootView: VoucherBenefitsView(
                config: config,
                onStateChanged: stateHandler
            )
        )
        
        addChild(voucherBenefitsView)
        
        stackView.addArrangedSubview(voucherBenefitsView.view)
        
        voucherBenefitsView.didMove(toParent: self)
    }
}
```

### Theming & Gradient Backgrounds

The SDK automatically applies themes from the Sovendus backend, including gradient backgrounds. No additional configuration is required - gradients are applied automatically when the API response includes gradient definitions.

**Supported gradient types:**
- **Linear** - Directional gradients at any angle
- **Radial** - Circular gradients with configurable center and radius
- **Angular** - Sweep gradients around a center point

Gradients are applied to containers, product boxes, buttons, savings badges, and banners. When no gradient is configured, the SDK falls back to solid background colors.

### Layout Options

Layout modes are determined by the API response - no integrator action required. Sovendus controls content and layout per placement via backend configuration.

**List Layouts** (`list.template.layoutId`):

| Layout ID | Style             | Description                                      |
|-----------|-------------------|--------------------------------------------------|
| 1         | Horizontal Slider | Swipeable pager with page indicators, offer advantages inline in each card (default) |
| 2         | Vertical List     | Cards stacked vertically                         |
| 3         | Carousel          | Stacked card deck with arrow navigation and animated transitions |

**Banner Layouts** (`banner.template.layoutId`):

| Layout ID | Style         | Description                                              |
|-----------|---------------|----------------------------------------------------------|
| 1         | Inline Banner | Compact horizontal layout with image and text (default)  |
| 2         | Card Banner   | Centered vertical layout with larger image               |

**How it works:**
- The component renders **whatever the API returns**: banner only, list only, or both
- Each `VoucherBenefitsView` instance with a different `SovendusConfig` (unique `trafficMediumNumber`) triggers its own API call
- Sovendus controls content and layout per placement

### Component Sizing

SDK components report the height they need and grow to fit their content — there is no fixed card height. That height depends on the layout the API returns, how long the product copy is, and the reader's system text size, so it is not something you can predict at integration time.

Give the component room to grow rather than pinning it:

```swift
// Good — the component reports the height it needs
ScrollView {
    VStack {
        VoucherBenefitsView(config: config)
        // your own content
    }
}

// Avoid — a hard-coded height clips the component
VoucherBenefitsView(config: config)
    .frame(height: 320)
```

In UIKit, let the hosting controller's view size itself from its SwiftUI content — pin it with Auto Layout constraints (as in the [UIKit example](#uikit) above), not a fixed height constant.

### Multiple Placements

Partners can use multiple `VoucherBenefitsView` instances with different configurations. Each instance triggers its own API call, allowing Sovendus to control content and layout per placement:

```swift
// Placement 1 - e.g. checkout page
VoucherBenefitsView(
    config: SovendusConfig(
        trafficSourceNumber: 1234,
        trafficMediumNumber: 100,
        consumerData: consumerData,
        orderData: orderData
    )
)

// Placement 2 - e.g. confirmation page
VoucherBenefitsView(
    config: SovendusConfig(
        trafficSourceNumber: 1234,
        trafficMediumNumber: 200,
        consumerData: consumerData,
        orderData: orderData
    )
)
```

---

## Step 4: Add Personalization (Optional)

**Personalize benefits** by providing consumer information:

```swift
let consumerData = ConsumerData(
    salutation: .mr,
    firstName: "Max",
    lastName: "Mustermann", 
    email: "customer@example.com",
    country: "DE",
    zipCode: "10115",
    city: "Berlin",
    hasConsent: true // Customer's privacy consent
)

let orderData = OrderData(
    sessionId: "SESSION-2025-001",
    orderId: "ORDER-2025-001",
    orderCurrency: "EUR",
    usedCouponCode: "WELCOME20",
    orderValue: 149.99
)

let config = SovendusConfig(
    trafficSourceNumber: 1234,
    trafficMediumNumber: 5678,
    consumerData: consumerData,
    orderData: orderData,
    sandbox: false,
    enableDebugLogs: false
)
```

**Privacy:** Email is automatically hashed before sending. No plain text personal data leaves your app.

**Consent tracking:** Pass `hasConsent: true` when the customer has granted privacy consent, or `false` when they have explicitly declined. Leave it unset (`nil`) when consent state is unknown or not collected. The value is forwarded as-is in the API request.

---

## Step 5: Production Configuration

Configure for production deployment:

```swift
let productionConfig = SovendusConfig(
    trafficSourceNumber: 1234, // Your real TSN from Sovendus
    trafficMediumNumber: 5678, // Your real TMN from Sovendus
    consumerData: consumerData,
    orderData: orderData,
    sandbox: false,           // Disable sandbox mode
    enableDebugLogs: false    // Disable debug logging
)
```

**Production Requirements:**
- Use real `trafficSourceNumber` and `trafficMediumNumber` from Sovendus
- Set `sandbox: false` for production behavior
- Set `enableDebugLogs: false` to disable debug output
- Both traffic parameters are required

---

## Step 6: Handle State Changes

**Monitor component states** for better UX:

```swift
private func handleSovendusState(_ state: SovendusState) {
    switch state {
    case .loading:
        // Show loading indicator if needed
        print("Loading Sovendus content...")
    case .success:
        // Content loaded successfully
        print("Sovendus content loaded")
    case .error(let error):
        // Handle errors gracefully
        print("Sovendus error: \(error.localizedDescription)")
        if config.enableDebugLogs {
            // Additional debug information in development
        }
    }
}
```

---

## Step 7: Custom Link Handling (Optional)

**Control how voucher and legal links are opened** with a custom callback:

```swift
let config = SovendusConfig(
    trafficSourceNumber: 1234,
    trafficMediumNumber: 5678,
    consumerData: consumerData,
    orderData: orderData,
    onLinkOpened: { url in
        // optional:  Custom link handling - useful for:
        // Open in SFSafariViewController instead of external Safari
        let safariVC = SFSafariViewController(url: url)
        UIApplication.shared.windows.first?.rootViewController?.present(safariVC, animated: true)
    }
)
```

### Common Use Cases

#### In-App Browser with SFSafariViewController
```swift
import SafariServices

let config = SovendusConfig(
    trafficSourceNumber: 1234,
    trafficMediumNumber: 5678,
    consumerData: consumerData,
    orderData: orderData,
    onLinkOpened: { url in
        let safariVC = SFSafariViewController(url: url)
        
        if let topVC = UIApplication.shared.windows.first?.rootViewController {
            var presentingVC = topVC
            while let presented = presentingVC.presentedViewController {
                presentingVC = presented
            }
            presentingVC.present(safariVC, animated: true)
        }
    }
)
```

**Default Behavior:** If `onLinkOpened` is not provided, the SDK uses `UIApplication.shared.open(url)` to open links in the system browser.

---

## Step 8: Custom Font Configuration (Optional)

**Use your app's custom font** in Sovendus SDK components by providing either a font family name or any registered `UIFont` from the family:

```swift
// Option A — pass the family name directly:
let config = SovendusConfig(
    trafficSourceNumber: 1234,
    trafficMediumNumber: 5678,
    consumerData: consumerData,
    orderData: orderData,
    customFontFamily: "Your Custom Font"
)

// Option B — pass any UIFont from the family (the face/size you choose doesn't matter,
// only the family is used):
let config = SovendusConfig(
    trafficSourceNumber: 1234,
    trafficMediumNumber: 5678,
    consumerData: consumerData,
    orderData: orderData,
    customFont: UIFont(name: "YourCustomFont-Regular", size: 14)
)
```

Either way, the SDK applies a weight to that family for every text element and SwiftUI resolves the matching registered face. The weight comes from the SDK's per-style defaults (regular, medium, semibold, bold) **or** from the dynamic theme returned by the API, which can request any weight from 100 (Thin) to 900 (Black). SwiftUI can only render a weight that is actually bundled — if the matching face is missing it falls back to the closest registered face, which usually looks worse than a real cut.

> **⚠️ Bundle the full weight range.** Because API-driven theming may request any weight (100–900), you must bundle **all weights of your font family** for weight customization to render correctly. Bundling only Regular and Bold means medium/semibold/etc. requests silently fall back to the nearest available cut.

### Bundling Custom Fonts in Your App

To use a custom font, you must first add it to your iOS app:

1. **Add the font files** (`.ttf` or `.otf`) to your Xcode project — include **every weight variant of the family** (Thin, ExtraLight, Light, Regular, Medium, SemiBold, Bold, ExtraBold, Black, plus any italics you need), not just Regular and Bold. This ensures any weight the API theme requests resolves to a real face.
2. **Include in target** - Ensure each font file is added to your app target's "Copy Bundle Resources" build phase
3. **Register in Info.plist** - Add every font filename to `UIAppFonts` (Fonts provided by application):

```xml
<key>UIAppFonts</key>
<array>
    <string>YourCustomFont-Thin.ttf</string>
    <string>YourCustomFont-ExtraLight.ttf</string>
    <string>YourCustomFont-Light.ttf</string>
    <string>YourCustomFont-Regular.ttf</string>
    <string>YourCustomFont-Medium.ttf</string>
    <string>YourCustomFont-SemiBold.ttf</string>
    <string>YourCustomFont-Bold.ttf</string>
    <string>YourCustomFont-ExtraBold.ttf</string>
    <string>YourCustomFont-Black.ttf</string>
</array>
```

> **Tip:** A variable font (a single `.ttf` exposing the full weight axis, like Inter) covers the whole range with one file and is the simplest way to guarantee every weight is available.

### Finding the Family Name

The string passed to `customFontFamily` is the font's **family name** (e.g. `"Noto Sans"`, `"Open Sans"`, `"Poppins"`), not a PostScript face name. You can find it by:
- Opening the font file in Font Book and checking "Family" in the info panel
- Printing the registered families at runtime: `UIFont.familyNames.forEach { print($0) }`
- Reading it from any registered face: `UIFont(name: "YourCustomFont-Regular", size: 14)?.familyName`

### Example

```swift
let config = SovendusConfig(
    trafficSourceNumber: 1234,
    trafficMediumNumber: 5678,
    consumerData: consumerData,
    orderData: orderData,
    customFontFamily: "Poppins"
)
```

With the full range of Poppins faces (`Poppins-Thin.ttf` … `Poppins-Black.ttf`) registered in `UIAppFonts`, body text renders with the regular face, badges with medium, headlines with semibold, and buttons with bold automatically — and any weight an API theme requests (100–900) resolves to its real cut. If you register only Regular and Bold, those mid-range weights fall back to the nearest available face.

**Default Behavior:** If neither `customFontFamily` nor `customFont` is provided (or both are `nil`), the SDK uses the system font.

### System Text Size

Every size the SDK renders — its own defaults and any `fontSize` an API theme provides — is scaled against the reader's system text size (Settings → Accessibility → Display & Text Size → Larger Text), for the system font and for a bundled custom family alike. SDK content therefore grows and shrinks alongside the rest of your app; you do not need to configure anything for this.

An enlarged text size makes a card taller rather than truncating it, so the component must be free to grow — see [Component Sizing](#component-sizing).

---

## Error Handling & Debugging

### Development Mode (enableDebugLogs = true, sandbox = true)
- **Console logging** for debugging
- **Network error details**
- **Configuration validation**
- **Sandbox benefits** for testing

### Production Mode (enableDebugLogs = false, sandbox = false)
- **Silent failures** - no console spam
- **Graceful degradation** if SDK fails
- **Real benefits** from production API

**Monitor Production Issues:**
```bash
# Check device logs for production errors
# Use Xcode Console or device logs
# Look for SovendusSDK related messages
```

**Common Issues:**
- **Missing traffic numbers** in production
- **Network connectivity** issues
- **Invalid customer data** format

---

## Modal Integration Example

### SwiftUI

```swift
struct CheckoutSuccessView: View {
    @State private var showBenefitsModal = false
    
    var body: some View {
        VStack {
            Text("Order Complete!")
            Button("See Exclusive Benefits") {
                showBenefitsModal = true
            }
        }
        .sheet(isPresented: $showBenefitsModal) {
            NavigationView {
                VoucherBenefitsView(
                    config: config,
                    onStateChanged: { state in
                        switch state {
                        case .loading:
                            print("Loading benefits...")
                        case .success:
                            print("Benefits loaded successfully")
                        case .error(let error):
                            print("Error loading benefits: \(error)")
                        }
                    }
                )
                .navigationBarTitleDisplayMode(.inline)
                .toolbar {
                    ToolbarItem(placement: .navigationBarTrailing) {
                        Button("Close") {
                            showBenefitsModal = false
                        }
                    }
                }
            }
        }
    }
}
```

### UIKit

```swift
class CheckoutSuccessViewController: UIViewController {
    private var benefitsViewController: UIViewController?
    
    @IBAction func showBenefitsPressed(_ sender: UIButton) {
        let benefitsView = VoucherBenefitsView(
            config: config,
            onStateChanged: { state in
                switch state {
                case .loading:
                    print("Loading benefits...")
                case .success:
                    print("Benefits loaded successfully")
                case .error(let error):
                    print("Error loading benefits: \(error)")
                }
            }
        )
        
        benefitsViewController = UIHostingController(rootView: benefitsView)
        
        if let benefitsViewController {
            let navController = UINavigationController(rootViewController: benefitsViewController)
            benefitsViewController.navigationItem.rightBarButtonItem = UIBarButtonItem(
                barButtonSystemItem: .close,
                target: self,
                action: #selector(closeBenefits)
            )
            present(navController, animated: true)
        }
    }
    
    @objc private func closeBenefits() {
        benefitsViewController?.dismiss(animated: true)
    }
}
```

---

## Troubleshooting

### "Components don't render"
- Check console logs with `enableDebugLogs: true`
- Verify traffic source/medium numbers are correct
- Test network connectivity
- Try sandbox mode for testing

### "No personalized content"
- Verify consumer data is provided correctly
- Check email format and validity
- Ensure order data is accurate

### "Performance issues"
- Components load asynchronously
- Use state callbacks to show loading indicators
- Content appears when ready

### "Integration errors"
- Import `SovendusSDK` in your files
- Verify minimum iOS version (16.0+)
- Check callback implementations are correct

---

**Ready to launch?** You now have everything needed to integrate Sovendus benefits into your iOS app!

**Need help?** Contact your Sovendus integration specialist for support.