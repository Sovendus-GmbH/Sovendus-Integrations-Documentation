# Sovendus Native Mobile App Integration

## 🎯 Overview

This guide provides comprehensive instructions for integrating Sovendus Voucher Network and Checkout Benefits into native mobile applications using WebView components.

## 🏗️ Architecture

The Sovendus integration consists of:

1. **Data Models** - Customer and order data structures
2. **HTML Generation** - Dynamic HTML content with embedded JavaScript
3. **WebView Integration** - Displaying the HTML content in a WebView
4. **JavaScript Bridge** - Communication between WebView and native code
5. **URL Handling** - Opening external URLs in native browser
6. **Error Handling** - Logging errors to your application's logging system

### Data Flow

```
Order Data → HTML Generation → WebView → JavaScript Bridge → Native Actions
```

## 📝 Implementation Steps

### 1. Create a WebView Component

Use the following HTML template for the WebView:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="initial-scale=1" />
  </head>
  <body id="body" style="margin: 0; background-color: #fff">
    <div id="sovendus-voucher-banner"></div>
    <script type="text/javascript">
      // Height Management: Add your ResizeObserver implementation here
      // See section 4 for detailed implementation examples

      window.sovApi = "v1";
      // Post Message Bridge: Handle URL opening requests from Sovendus content
      // This prevents navigation within the WebView and opens URLs in native browser
      window.addEventListener("message", (event) => {
        if (event.data.channel === "sovendus:integration") {
          console.log("URL to open", event.data.payload.url);
          // Forward this URL to your native code for opening in browser
          // Implementation depends on your platform's JavaScript bridge
        }
      });

      window.sovIframes = window.sovIframes || [];
      window.sovIframes.push({
       trafficSourceNumber: "$trafficSourceNumber",
       trafficMediumNumber: "$trafficMediumNumber",
       iframeContainerId: "sovendus-voucher-banner",
       sessionId: "$sessionId",
       orderId: "$orderId",
       orderValue: "$netOrderValue",
       orderCurrency: "$currencyCode",
       usedCouponCode: "$usedCouponCode",
       integrationType: "genericnative-1.2.0",
      });
      window.sovConsumer = {
        consumerSalutation: "$salutation",
        consumerFirstName: "$firstName",
        consumerLastName: "$lastName",
        consumerEmail: "$email",
        consumerPhone: "$phone",
        consumerYearOfBirth: "$yearOfBirth",
        consumerDateOfBirth: "$dateOfBirth",
        consumerStreet: "$street",
        consumerStreetNumber: "$streetNumber",
        consumerZipcode: "$zipcode",
        consumerCity: "$city",
        consumerCountry: "$country",
      };
    </script>
    <script
      type="text/javascript"
      src="https://api.sovendus.com/sovabo/common/js/flexibleIframe.js"
      async="true"
    ></script>
  </body>
</html>
```

### 2. Parameter Configuration

> [!INFO]
> **Parameter Documentation**
> For detailed information on all parameters, examples, and requirements, visit: [Parameter Documentation](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Parameter)

### 3. Navigation Suppression and URL Handling

> [!CRITICAL]
> **All navigation requests within the WebView MUST be suppressed** to prevent external links from opening within the WebView. Instead, use the post message bridge to handle URL opening in the native browser.

**Required Implementation:**

1. **Suppress Navigation**: Configure your WebView to prevent all navigation requests
2. **Post Message Bridge**: Implement JavaScript message handling to catch URL opening requests

```javascript
window.sovApi = "v1";
// Post Message Bridge: Handle URL opening requests from Sovendus content
// This prevents navigation within the WebView and opens URLs in native browser
window.addEventListener("message", (event) => {
  if (event.data.channel === "sovendus:integration") {
    console.log("URL to open", event.data.payload.url);
    // Forward this URL to your native code for opening in browser
    // Implementation depends on your platform's JavaScript bridge
  }
});
```

**Platform-Specific Navigation Suppression:**

- **iOS (WKWebView)**: Return `.cancel` in `decidePolicyFor navigationAction`
- **Android (WebView)**: Return `true` in `shouldOverrideUrlLoading()`
- **Other platforms**: Use equivalent navigation prevention methods

### 4. Implement Dynamic Height Adjustment

> [!IMPORTANT]
> **Dynamic Height Management Required**: The WebView height must dynamically adjust based on content size to provide optimal user experience.

**Standard Implementation:**

```javascript
// Height Management: Monitor content size changes and notify native code
new ResizeObserver(() => {
  const height = document.body.scrollHeight;
  // Send height to native code for component resizing
  // Implementation depends on your platform's JavaScript bridge
  console.log("New height:", height);
}).observe(document.body);
```

**Implementation Notes:**

- Only apply height updates > 100px to avoid layout issues
- Ensure your JavaScript bridge properly handles height update messages

### 5. Add Input Sanitization

> [!WARNING]
> **Security Requirement**
> Always sanitize user input data before inserting it into the HTML template to prevent XSS attacks. Use JSON encoding for all string values.

### 6. Implement Error Handling

Add comprehensive error handling for:

- WebView loading failures
- JavaScript execution errors
- Network connectivity issues
- Invalid parameter values

## 🔧 Key Implementation Requirements

### Navigation Suppression
>
> [!CRITICAL]
> **All navigation requests within the WebView MUST be suppressed** to prevent external links from opening within the WebView. Configure your WebView to cancel/prevent all navigation attempts and handle URLs via the post message bridge instead.

### Post Message Bridge for URL Handling
>
> [!IMPORTANT]
> **URL Opening Pattern**: The integration uses a post message bridge to handle URL opening:
>
> 1. Sovendus content sends messages via `window.postMessage()` with channel `"sovendus:integration"`
> 2. JavaScript listener catches these messages and forwards them to native code
> 3. Native code receives the message and opens the URL in the default browser

### Dynamic Height Management
>
> [!IMPORTANT]
> **Height Adjustment Pattern**: The WebView height must dynamically adjust based on content:
>
> 1. `ResizeObserver` monitors changes to `document.body.scrollHeight`
> 2. Height changes are sent to native code via your platform's JavaScript bridge
> 3. Native code updates the component height and triggers layout update
> 4. Only heights > 100px should be applied to avoid layout issues

## 🔧 Implementation Features

### Security

- **XSS Prevention**: All user input must be properly sanitized
- **Content Security**: Restrict WebView navigation and file access
- **Error Handling**: Implement proper error logging to your application's logging system

### Performance

- **Memory Management**: Properly dispose of WebView resources
- **Loading Optimization**: Use async script loading
- **Height Optimization**: Implement efficient resize handling

### Platform Considerations

- **WebView Selection**: Use the most secure and performant WebView component available on your platform
- **External Link Handling**: Implement proper browser integration for external links
- **Memory Management**: Handle WebView lifecycle and memory warnings appropriately
- **Permissions**: Add required internet permissions as needed by your platform
- **Performance**: For Voucher Network-only integrations, consider optimizations to avoid unnecessary delays

## 🚀 Testing

> [!INFO]
> **Testing Requirements**
> Test your integration with both consent granted and consent denied scenarios. Send screenshots to your account manager for verification.

### Test Checklist

1. **Integration Testing**: Verify banner loads and displays correctly
2. **Height Adjustment**: Test dynamic height changes work properly
3. **URL Handling**: Verify external links open in native browser
4. **Error Handling**: Test error scenarios and reporting
5. **Memory Management**: Ensure no memory leaks in WebView usage
6. **Consent Scenarios**: Test both with and without user consent

## 📞 Support

For technical support and parameter configuration, contact your Sovendus account manager or visit the [Sovendus Developer Hub](https://developer-hub.sovendus.com/).
