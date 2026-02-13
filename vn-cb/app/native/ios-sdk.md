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
    city: "Berlin"
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

**Use your app's custom font** in Sovendus SDK components by providing a `UIFont`:

```swift
let config = SovendusConfig(
    trafficSourceNumber: 1234,
    trafficMediumNumber: 5678,
    consumerData: consumerData,
    orderData: orderData,
    customFont: UIFont(name: "YourCustomFont-Regular", size: 14)
)
```

### Bundling Custom Fonts in Your App

To use a custom font, you must first add it to your iOS app:

1. **Add the font file** (`.ttf` or `.otf`) to your Xcode project
2. **Include in target** - Ensure the font is added to your app target's "Copy Bundle Resources" build phase
3. **Register in Info.plist** - Add the font filename to `UIAppFonts` (Fonts provided by application):

```xml
<key>UIAppFonts</key>
<array>
    <string>YourCustomFont-Regular.ttf</string>
    <string>YourCustomFont-Bold.ttf</string>
</array>
```

### Creating the UIFont

```swift
// Create a custom font with the PostScript name
let customFont = UIFont(name: "YourCustomFont-Regular", size: 14)

// You can also use font descriptors for more control
let descriptor = UIFontDescriptor(name: "YourCustomFont", size: 14)
let customFont = UIFont(descriptor: descriptor, size: 14)
```

**Finding the font name:** The font name used in `UIFont(name:size:)` is the PostScript name, which may differ from the filename. You can find it by:
- Opening the font file in Font Book and checking "PostScript name" in the info panel
- Printing all available fonts: `UIFont.familyNames.forEach { print(UIFont.fontNames(forFamilyName: $0)) }`

### Example with Multiple Font Weights

```swift
// Define font helpers
extension UIFont {
    static var appFontRegular: UIFont? {
        UIFont(name: "Poppins-Regular", size: 14)
    }

    static var appFontBold: UIFont? {
        UIFont(name: "Poppins-Bold", size: 14)
    }
}

// Use in config
let config = SovendusConfig(
    trafficSourceNumber: 1234,
    trafficMediumNumber: 5678,
    consumerData: consumerData,
    orderData: orderData,
    customFont: .appFontRegular
)
```

**Default Behavior:** If `customFont` is not provided or is `nil`, the SDK uses the system font.

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

## Using BannerView Component

**BannerView** provides a compact, banner-style display of benefits that can be overlaid on your UI. It's ideal for non-intrusive benefit presentation.

### SwiftUI Integration

```swift
import SwiftUI
import SovendusSDK

struct MyView: View {
    private let config = SovendusConfig(
        trafficSourceNumber: 1234,
        trafficMediumNumber: 5678,
        consumerData: consumerData,
        orderData: orderData,
        sandbox: true,
        enableDebugLogs: true
    )

    var body: some View {
        ScrollView {
            VStack {
                Text("Main Content")

                BannerView(
                    config: config,
                    onStateChanged: { state in
                        switch state {
                        case .loading:
                            print("Loading banner...")
                        case .success:
                            print("Banner loaded")
                        case .error(let error):
                            print("Banner error: \(error)")
                        }
                    }
                )
            }
        }
    }
}
```

### BannerView Parameters

- **config**: `SovendusConfig` - Your Sovendus configuration (required)
- **onStateChanged**: `OnSovendusStateChanged?` - Callback for state changes (optional)

### UIKit Integration

```swift
import UIKit
import SwiftUI
import SovendusSDK

class MyViewController: UIViewController {
    private var bannerHostingController: UIHostingController<BannerView>?

    private lazy var config = SovendusConfig(
        trafficSourceNumber: 1234,
        trafficMediumNumber: 5678,
        consumerData: consumerData,
        orderData: orderData,
        sandbox: true,
        enableDebugLogs: true
    )

    func showBanner() {
        let bannerView = BannerView(
            config: config,
            onStateChanged: { state in
                print("Banner state: \(state)")
                switch state {
                case .loading:
                    break
                case .success:
                    print("Banner loaded successfully")
                case .error(let error):
                    print("Banner error: \(error)")
                }
            }
        )

        let hostingController = UIHostingController(rootView: bannerView)
        hostingController.view.backgroundColor = .clear

        addChild(hostingController)
        view.addSubview(hostingController.view)
        hostingController.didMove(toParent: self)

        hostingController.view.translatesAutoresizingMaskIntoConstraints = false

        NSLayoutConstraint.activate([
            hostingController.view.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            hostingController.view.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            hostingController.view.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor)
        ])

        self.bannerHostingController = hostingController
    }
}
```

**Important:** BannerView only starts loading data after being added to the view hierarchy. The component remains invisible until data is successfully loaded. Integrators should observe the `onStateChanged` callback (`.success` case) to control when to present or make the banner visible to users.

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