# Sovendus iOS (Swift) Integration Guide

## 🍎 Complete Implementation Guide for iOS Apps

This guide provides detailed instructions for implementing Sovendus Voucher Network and Checkout Benefits integration in iOS applications using Swift.

## 📋 Overview

The Sovendus integration consists of:

1. **Data Models** - Customer and order data structures
2. **HTML Generation** - Dynamic HTML content with embedded JavaScript
3. **WebView Integration** - Displaying the HTML content in a WKWebView
4. **JavaScript Bridge** - Communication between WebView and native code
5. **URL Handling** - Opening external URLs in Safari
6. **Error Handling** - Logging errors to your application's logging system

## 🏗️ Core Architecture

### Data Flow

```
Order Data → HTML Generation → WKWebView → JavaScript Bridge → Native Actions
```

### Key Components

- **Customer Data Model**: Stores customer information
- **Order Data Model**: Stores transaction details
- **HTML Sanitizer**: Prevents XSS attacks by sanitizing input data
- **WebView Controller**: Manages the embedded web content
- **JavaScript Handler**: Processes messages from the WebView
- **Error Handler**: Logs errors to your application's logging system

## 📦 Dependencies

Add to your project (no external dependencies required):

```swift
import UIKit
import WebKit
import SafariServices
import Foundation
```

## 🔧 Implementation

### 1. Data Models

```swift
import Foundation

struct SovendusCustomerData {
    let salutation: String?
    let firstName: String?
    let lastName: String?
    let email: String?
    let phone: String?
    let yearOfBirth: Int?
    let dateOfBirth: String?
    let street: String?
    let streetNumber: String?
    let zipcode: String?
    let city: String?
    let country: String?
    
    init(salutation: String? = nil, firstName: String? = nil, lastName: String? = nil, 
         email: String? = nil, phone: String? = nil, yearOfBirth: Int? = nil,
         dateOfBirth: String? = nil, street: String? = nil, streetNumber: String? = nil,
         zipcode: String? = nil, city: String? = nil, country: String? = nil) {
        self.salutation = salutation
        self.firstName = firstName
        self.lastName = lastName
        self.email = email
        self.phone = phone
        self.yearOfBirth = yearOfBirth
        self.dateOfBirth = dateOfBirth
        self.street = street
        self.streetNumber = streetNumber
        self.zipcode = zipcode
        self.city = city
        self.country = country
    }
}

struct SovendusOrderData {
    let sessionId: String
    let orderId: String
    let currencyCode: String
    let usedCouponCode: String
    let trafficSourceNumber: Int
    let trafficMediumNumber: Int
    let netOrderValue: Double
    let customerData: SovendusCustomerData?

    init(sessionId: String, orderId: String, currencyCode: String, usedCouponCode: String,
         trafficSourceNumber: Int, trafficMediumNumber: Int, netOrderValue: Double,
         customerData: SovendusCustomerData? = nil) {
        self.sessionId = sessionId
        self.orderId = orderId
        self.currencyCode = currencyCode
        self.usedCouponCode = usedCouponCode
        self.trafficSourceNumber = trafficSourceNumber
        self.trafficMediumNumber = trafficMediumNumber
        self.netOrderValue = netOrderValue
        self.customerData = customerData
    }
}
```

### 2. HTML Sanitizer

```swift
import Foundation

class HtmlSanitizer {
    private let trafficSourceNumber: Int
    private let trafficMediumNumber: Int
    private let onError: ((String, Error) -> Void)?
    
    init(trafficSourceNumber: Int, trafficMediumNumber: Int, onError: ((String, Error) -> Void)? = nil) {
        self.trafficSourceNumber = trafficSourceNumber
        self.trafficMediumNumber = trafficMediumNumber
        self.onError = onError
    }
    
    func sanitize(_ input: String) -> String {
        do {
            let jsonData = try JSONSerialization.data(withJSONObject: input, options: [])
            let jsonString = String(data: jsonData, encoding: .utf8) ?? "\"\""
            // Remove surrounding quotes
            return String(jsonString.dropFirst().dropLast())
        } catch {
            reportError("Error sanitizing string input", error)
            return ""
        }
    }
    
    func sanitizeOptional(_ input: String?) -> String? {
        guard let input = input else { return nil }
        return sanitize(input)
    }
    
    func sanitizeInt(_ input: Int) -> String {
        return String(input)
    }
    
    func sanitizeIntOptional(_ input: Int?) -> String? {
        guard let input = input else { return nil }
        return sanitizeInt(input)
    }
    
    func sanitizeDouble(_ input: Double) -> String {
        if input.isNaN || input.isInfinite {
            return "0"
        }
        return String(input)
    }
    
    private func reportError(_ message: String, _ error: Error) {
        onError?(message, error)
        // Log error to your application's logging system
    }
}
```

### 3. Main Banner Component

```swift
import UIKit
import WebKit
import SafariServices

class SovendusBanner: UIView {
    private var webView: WKWebView!
    private var heightConstraint: NSLayoutConstraint!
    private let orderData: SovendusOrderData
    private let onError: ((String, Error) -> Void)?
    
    static let versionNumber = "1.0.0"
    // Initial height for the WebView
    static let initialHeight: CGFloat = 348.0
    
    init(trafficSourceNumber: Int, trafficMediumNumber: Int, sessionId: String = "",
         orderId: String = "", netOrderValue: Double = 0, currencyCode: String = "",
         usedCouponCode: String = "", customerData: SovendusCustomerData? = nil,
         onError: ((String, Error) -> Void)? = nil) {

        self.orderData = SovendusOrderData(
            sessionId: sessionId, orderId: orderId, currencyCode: currencyCode,
            usedCouponCode: usedCouponCode, trafficSourceNumber: trafficSourceNumber,
            trafficMediumNumber: trafficMediumNumber, netOrderValue: netOrderValue,
            customerData: customerData
        )
        self.onError = onError
        
        super.init(frame: .zero)
        setupWebView()
        loadContent()
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    private func setupWebView() {
        let config = WKWebViewConfiguration()
        config.userContentController.add(self, name: "sovHandler")
        
        webView = WKWebView(frame: .zero, configuration: config)
        webView.navigationDelegate = self
        webView.translatesAutoresizingMaskIntoConstraints = false
        
        addSubview(webView)
        NSLayoutConstraint.activate([
            webView.topAnchor.constraint(equalTo: topAnchor),
            webView.leadingAnchor.constraint(equalTo: leadingAnchor),
            webView.trailingAnchor.constraint(equalTo: trailingAnchor),
            webView.bottomAnchor.constraint(equalTo: bottomAnchor)
        ])
        
        heightConstraint = heightAnchor.constraint(equalToConstant: Self.initialHeight)
        heightConstraint.isActive = true
    }
    
    private func loadContent() {
        let html = generateHtml()
        webView.loadHTMLString(html, baseURL: nil)
    }
}
```

### 4. WebView Delegate and Message Handling

> [!WARNING]
> **Navigation Suppression Required**
> All navigation requests within the WebView must be suppressed to prevent external links from opening within the WebView. Instead, use the post message bridge to handle URL opening in the native browser.

```swift
extension SovendusBanner: WKNavigationDelegate, WKScriptMessageHandler {
    func webView(_ webView: WKWebView, decidePolicyFor navigationAction: WKNavigationAction,
                 decisionHandler: @escaping (WKNavigationActionPolicy) -> Void) {
        // WARNING: Always cancel navigation to prevent links from opening in WebView
        // Links will be handled via the post message bridge instead
        decisionHandler(.cancel)
    }

    func userContentController(_ userContentController: WKUserContentController,
                              didReceive message: WKScriptMessage) {
        guard message.name == "sovHandler",
              let messageBody = message.body as? [String: Any],
              let type = messageBody["type"] as? String,
              let value = messageBody["value"] else {
            return
        }

        switch type {
        case "height":
            // Handle dynamic height updates from WebView content
            if let heightValue = value as? NSNumber {
                updateHeight(heightValue.doubleValue)
            } else if let heightString = value as? String,
                      let height = Double(heightString) {
                updateHeight(height)
            }
        case "openUrl":
            // Handle URL opening requests from the post message bridge
            if let urlString = value as? String {
                openUrlInBrowser(urlString)
            }
        default:
            onError?("Unknown message type: \(type)", NSError(domain: "SovendusBanner", code: 1))
        }
    }

    private func updateHeight(_ height: Double) {
        // Only update height if it's reasonable (> 100px) to avoid layout issues
        guard height > 100 else { return }

        DispatchQueue.main.async {
            self.heightConstraint.constant = CGFloat(height)
            self.layoutIfNeeded()
        }
    }

    private func openUrlInBrowser(_ urlString: String) {
        guard let url = URL(string: urlString),
              let viewController = findViewController() else {
            onError?("Invalid URL or no view controller found", NSError(domain: "SovendusBanner", code: 2))
            return
        }

        // Open URL in Safari View Controller for better user experience
        let safariVC = SFSafariViewController(url: url)
        viewController.present(safariVC, animated: true)
    }

    private func findViewController() -> UIViewController? {
        var responder: UIResponder? = self
        while responder != nil {
            if let viewController = responder as? UIViewController {
                return viewController
            }
            responder = responder?.next
        }
        return nil
    }
}
```

### 5. HTML Generation

```swift
extension SovendusBanner {
    private func generateHtml() -> String {
        let sanitizer = HtmlSanitizer(
            trafficSourceNumber: orderData.trafficSourceNumber,
            trafficMediumNumber: orderData.trafficMediumNumber,
            onError: onError
        )

        let sanitizedOrderData = sanitizeOrderData(orderData, with: sanitizer)
        let consumerJson = createConsumerJson(from: sanitizedOrderData.customerData, with: sanitizer)
        let resizeObserver = createResizeObserver()

        return buildHtmlTemplate(
            orderData: sanitizedOrderData,
            consumerJson: consumerJson,
            resizeObserver: resizeObserver
        )
    }

    private func sanitizeOrderData(_ data: SovendusOrderData, with sanitizer: HtmlSanitizer) -> SovendusOrderData {
        return SovendusOrderData(
            sessionId: sanitizer.sanitize(data.sessionId),
            orderId: sanitizer.sanitize(data.orderId),
            currencyCode: sanitizer.sanitize(data.currencyCode),
            usedCouponCode: sanitizer.sanitize(data.usedCouponCode),
            trafficSourceNumber: data.trafficSourceNumber,
            trafficMediumNumber: data.trafficMediumNumber,
            netOrderValue: data.netOrderValue,
            customerData: data.customerData
        )
    }

    private func createConsumerJson(from customerData: SovendusCustomerData?, with sanitizer: HtmlSanitizer) -> String {
        let customer = customerData ?? SovendusCustomerData()

        let consumerMap: [String: Any] = [
            "consumerSalutation": sanitizer.sanitizeOptional(customer.salutation) ?? "",
            "consumerFirstName": sanitizer.sanitizeOptional(customer.firstName) ?? "",
            "consumerLastName": sanitizer.sanitizeOptional(customer.lastName) ?? "",
            "consumerEmail": sanitizer.sanitizeOptional(customer.email) ?? "",
            "consumerPhone": sanitizer.sanitizeOptional(customer.phone) ?? "",
            "consumerYearOfBirth": sanitizer.sanitizeIntOptional(customer.yearOfBirth) ?? "",
            "consumerDateOfBirth": sanitizer.sanitizeOptional(customer.dateOfBirth) ?? "",
            "consumerStreet": sanitizer.sanitizeOptional(customer.street) ?? "",
            "consumerStreetNumber": sanitizer.sanitizeOptional(customer.streetNumber) ?? "",
            "consumerZipcode": sanitizer.sanitizeOptional(customer.zipcode) ?? "",
            "consumerCity": sanitizer.sanitizeOptional(customer.city) ?? "",
            "consumerCountry": sanitizer.sanitizeOptional(customer.country) ?? ""
        ]

        do {
            let jsonData = try JSONSerialization.data(withJSONObject: consumerMap, options: [])
            return String(data: jsonData, encoding: .utf8) ?? "{}"
        } catch {
            onError?("Error creating consumer JSON", error)
            return "{}"
        }
    }

    private func createResizeObserver() -> String {
        return """
        // Height Management: Monitor content size changes and notify native code
        new ResizeObserver(() => {
          window.webkit.messageHandlers.sovHandler.postMessage({
            type: 'height',
            value: document.body.scrollHeight
          });
        }).observe(document.body);
        """
    }

    private func buildHtmlTemplate(orderData: SovendusOrderData, consumerJson: String,
                                  resizeObserver: String) -> String {
        let sanitizer = HtmlSanitizer(
            trafficSourceNumber: orderData.trafficSourceNumber,
            trafficMediumNumber: orderData.trafficMediumNumber,
            onError: onError
        )

        return """
        <!DOCTYPE html>
        <html>
            <head>
              <meta name="viewport" content="initial-scale=1" />
            </head>
            <body id="body" style="margin: 0; background-color: #fff">
                <div id="sovendus-voucher-banner"></div>
                <script type="text/javascript">
                    \(resizeObserver)
                    window.sovApi = "v1";
                    // Post Message Bridge: Handle URL opening requests from Sovendus content
                    // This prevents navigation within the WebView and opens URLs in native browser
                    window.addEventListener("message", (event) => {
                      if (event.data.channel === "sovendus:integration") {
                        window.webkit.messageHandlers.sovHandler.postMessage({
                          type: 'openUrl',
                          value: event.data.payload.url
                        });
                      }
                    });
                    window.sovIframes = [];
                    window.sovIframes.push({
                        trafficSourceNumber: "\(orderData.trafficSourceNumber)",
                        trafficMediumNumber: "\(orderData.trafficMediumNumber)",
                        iframeContainerId: "sovendus-voucher-banner",
                        sessionId: "\(orderData.sessionId)",
                        orderId: "\(orderData.orderId)",
                        orderValue: "\(sanitizer.sanitizeDouble(orderData.netOrderValue))",
                        orderCurrency: "\(orderData.currencyCode)",
                        usedCouponCode: "\(orderData.usedCouponCode)",
                        integrationType: "ios-\(Self.versionNumber)",
                    });
                    window.sovConsumer = \(consumerJson);
                </script>
                <script type="text/javascript" src="https://api.sovendus.com/sovabo/common/js/flexibleIframe.js" async=true></script>
            </body>
        </html>
        """
    }
}
```

## 💻 Usage Example

### Complete Implementation

```swift
import UIKit

class OrderSuccessViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()

        setupUI()
        addSovendusBanner()
    }

    private func setupUI() {
        view.backgroundColor = .white
        title = "Order Confirmation"

        // Add your order confirmation content here
        let thankYouLabel = UILabel()
        thankYouLabel.text = "Thank you for your order!"
        thankYouLabel.textAlignment = .center
        thankYouLabel.translatesAutoresizingMaskIntoConstraints = false

        view.addSubview(thankYouLabel)
        NSLayoutConstraint.activate([
            thankYouLabel.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 20),
            thankYouLabel.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
            thankYouLabel.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -20)
        ])
    }

    private func addSovendusBanner() {
        let customerData = SovendusCustomerData(
            salutation: "Mr.",
            firstName: "John",
            lastName: "Smith",
            email: "example@example.com",
            phone: "+4915546456456",
            yearOfBirth: 1990,
            dateOfBirth: "01.12.1990",
            street: "Main Street",
            streetNumber: "12/1",
            zipcode: "76135",
            city: "Karlsruhe",
            country: "DE"
        )

        let sovendusBanner = SovendusBanner(
            trafficSourceNumber: YOUR_TRAFFIC_SOURCE_NUMBER, // Replace with your actual number
            trafficMediumNumber: YOUR_TRAFFIC_MEDIUM_NUMBER, // Replace with your actual number
            sessionId: "unique-session-id",
            orderId: "Order-123",
            netOrderValue: 120.5,
            currencyCode: "EUR",
            usedCouponCode: "CouponCodeFromThePurchase",
            customerData: customerData,
            onError: { message, error in
                print("Integration Error: \(message) - \(error)")
                // Log to your application's logging system
            }
        )

        sovendusBanner.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(sovendusBanner)

        NSLayoutConstraint.activate([
            sovendusBanner.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 100),
            sovendusBanner.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            sovendusBanner.trailingAnchor.constraint(equalTo: view.trailingAnchor)
        ])
    }
}
```

## 🔧 Key Implementation Requirements

### Navigation Suppression

> [!WARNING]
> **All navigation requests within the WebView MUST be suppressed**
> by returning `.cancel` in `decidePolicyFor navigationAction`. This prevents external links from opening within the WebView and ensures they are handled via the post message bridge instead.

### Post Message Bridge for URL Handling

> [!WARNING]
> **URL Opening Pattern:**
> The integration uses a post message bridge to handle URL opening:
>
> 1. Sovendus content sends messages via `window.postMessage()` with channel `"sovendus:integration"`
> 2. JavaScript listener catches these messages and forwards them to native code via `window.webkit.messageHandlers.sovHandler.postMessage()`
> 3. Native code receives the message and opens the URL in Safari View Controller

### Dynamic Height Management

> [!WARNING]
> **Height Adjustment Pattern:**
> The WebView height must dynamically adjust based on content:
>
> 1. `ResizeObserver` monitors changes to `document.body.scrollHeight`
> 2. Height changes are sent to native code via the JavaScript bridge
> 3. Native code updates the height constraint and triggers layout update
> 4. Only heights > 100px are applied to avoid layout issues

## 🔧 Parameter Configuration

> [!INFO]
> **Parameter Documentation**
> For detailed information on all parameters, examples, and requirements, visit: [Parameter Documentation](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Parameter)

## 🚀 Testing

1. **Integration Testing**: Verify banner loads and displays correctly
2. **Height Adjustment**: Test dynamic height changes work properly
3. **URL Handling**: Verify external links open in Safari
4. **Error Handling**: Test error scenarios and reporting
5. **Memory Management**: Ensure no memory leaks in WebView usage

## 📞 Support

For technical support and parameter configuration, contact your Sovendus account manager or visit the [Sovendus Developer Hub](https://developer-hub.sovendus.com/).
