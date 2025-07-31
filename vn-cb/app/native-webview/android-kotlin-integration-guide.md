# Sovendus Android (Kotlin) Integration Guide

## 🤖 Complete Implementation Guide for Android Apps

This guide provides detailed instructions for implementing Sovendus Voucher Network and Checkout Benefits integration in Android applications using Kotlin.

## 📋 Overview

The Sovendus integration consists of:

1. **Data Models** - Customer and order data structures
2. **HTML Generation** - Dynamic HTML content with embedded JavaScript
3. **WebView Integration** - Displaying the HTML content in a WebView
4. **JavaScript Bridge** - Communication between WebView and native code
5. **URL Handling** - Opening external URLs in browser
6. **Error Handling** - Logging errors to your application's logging system

## 🏗️ Core Architecture

### Data Flow

```
Order Data → HTML Generation → WebView → JavaScript Bridge → Native Actions
```

### Key Components

- **Customer Data Model**: Stores customer information (optional)
- **Order Data Model**: Stores transaction details (required)
- **HTML Sanitizer**: Prevents XSS attacks by sanitizing input data
- **WebView Controller**: Manages the embedded web content
- **JavaScript Handler**: Processes messages from the WebView
- **Error Handler**: Logs errors to your application's logging system

## 📦 Dependencies

Add to your `build.gradle` (Module: app):

```kotlin
dependencies {
    implementation 'androidx.webkit:webkit:1.8.0'
    implementation 'com.squareup.okhttp3:okhttp:4.12.0'
    implementation 'com.google.code.gson:gson:2.10.1'
}
```

## 🔧 Implementation

### 1. Data Models

```kotlin
data class SovendusCustomerData(
    val salutation: String? = null,
    val firstName: String? = null,
    val lastName: String? = null,
    val email: String? = null,
    val phone: String? = null,
    val yearOfBirth: Int? = null,
    val dateOfBirth: String? = null,
    val street: String? = null,
    val streetNumber: String? = null,
    val zipcode: String? = null,
    val city: String? = null,
    val country: String? = null
)

data class SovendusOrderData(
    val sessionId: String,
    val orderId: String,
    val currencyCode: String,
    val usedCouponCode: String,
    val trafficSourceNumber: Int,
    val trafficMediumNumber: Int,
    val netOrderValue: Double,
    val customerData: SovendusCustomerData?
)
```

### 2. HTML Sanitizer

```kotlin
import com.google.gson.Gson

class HtmlSanitizer(
    private val trafficSourceNumber: Int,
    private val trafficMediumNumber: Int,
    private val onError: ((String, Throwable) -> Unit)? = null
) {
    private val gson = Gson()
    
    fun sanitize(input: String): String {
        return try {
            val jsonString = gson.toJson(input)
            // Remove surrounding quotes
            jsonString.substring(1, jsonString.length - 1)
        } catch (e: Exception) {
            onError?.invoke("Error sanitizing string input", e)
            ""
        }
    }
    
    fun sanitizeOptional(input: String?): String? {
        return input?.let { sanitize(it) }
    }
    
    fun sanitizeInt(input: Int): String {
        return try {
            input.toString()
        } catch (e: Exception) {
            onError?.invoke("Error sanitizing int input: $input", e)
            "0"
        }
    }
    
    fun sanitizeIntOptional(input: Int?): String? {
        return input?.let { sanitizeInt(it) }
    }
    
    fun sanitizeDouble(input: Double): String {
        return try {
            when {
                input.isNaN() -> "0"
                input.isInfinite() -> "0"
                else -> input.toString()
            }
        } catch (e: Exception) {
            onError?.invoke("Error sanitizing double input: $input", e)
            "0"
        }
    }
}
```

### 3. Main Banner Component

```kotlin
import android.content.Context
import android.content.Intent
import android.net.Uri
import android.util.AttributeSet
import android.webkit.*
import android.widget.FrameLayout
import org.json.JSONObject

class SovendusBanner @JvmOverloads constructor(
    context: Context,
    attrs: AttributeSet? = null,
    defStyleAttr: Int = 0
) : FrameLayout(context, attrs, defStyleAttr) {
    
    private var webView: WebView? = null
    private var orderData: SovendusOrderData? = null
    private var onError: ((String, Throwable) -> Unit)? = null

    
    companion object {
        const val VERSION_NUMBER = "1.3.0"
        const val INITIAL_HEIGHT = 348
    }
    
    fun initialize(
        trafficSourceNumber: Int,
        trafficMediumNumber: Int,
        sessionId: String = "",
        orderId: String = "",
        netOrderValue: Double = 0.0,
        currencyCode: String = "",
        usedCouponCode: String = "",
        customerData: SovendusCustomerData? = null,

        onError: ((String, Throwable) -> Unit)? = null
    ) {
        this.orderData = SovendusOrderData(
            sessionId, orderId, currencyCode, usedCouponCode,
            trafficSourceNumber, trafficMediumNumber, netOrderValue, customerData
        )
        this.onError = onError

        
        setupWebView()
        loadContent()
    }
    
    private fun setupWebView() {
        webView = WebView(context).apply {
            layoutParams = LayoutParams(LayoutParams.MATCH_PARENT, INITIAL_HEIGHT)
            
            settings.apply {
                javaScriptEnabled = true
                domStorageEnabled = true
                allowFileAccess = false
                allowContentAccess = false
                setSupportZoom(false)
                builtInZoomControls = false
                displayZoomControls = false
            }
            
            webViewClient = object : WebViewClient() {
                override fun shouldOverrideUrlLoading(view: WebView?, request: WebResourceRequest?): Boolean {
                    // CRITICAL: Always return true to prevent navigation within WebView
                    // All URL opening will be handled via the post message bridge instead
                    return true
                }

                override fun onConsoleMessage(consoleMessage: ConsoleMessage?): Boolean {
                    consoleMessage?.let { message ->
                        onError?.invoke(
                            "Unexpected console message received",
                            Exception("Level: ${message.messageLevel()}, Message: ${message.message()}")
                        )
                    }
                    return super.onConsoleMessage(consoleMessage)
                }
            }
            
            addJavaScriptInterface(JavaScriptInterface(), "sovHandler")
        }
        
        addView(webView)
    }
    
    private inner class JavaScriptInterface {
        @JavascriptInterface
        fun postMessage(message: String) {
            try {
                val json = JSONObject(message)
                val type = json.getString("type")
                val value = json.getString("value")

                when (type) {
                    "height" -> {
                        // Handle dynamic height updates from WebView content
                        updateHeight(value.toDoubleOrNull() ?: 0.0)
                    }
                    "openUrl" -> {
                        // Handle URL opening requests from the post message bridge
                        openUrlInBrowser(value)
                    }
                }
            } catch (e: Exception) {
                onError?.invoke("Error processing JS message", e)
            }
        }
    }
    
    private fun updateHeight(height: Double) {
        // Only update height if it's reasonable (> 100px) to avoid layout issues
        if (height > 100) {
            post {
                webView?.layoutParams = LayoutParams(LayoutParams.MATCH_PARENT, height.toInt())
                requestLayout()
            }
        }
    }
    
    private fun openUrlInBrowser(url: String) {
        try {
            val intent = Intent(Intent.ACTION_VIEW, Uri.parse(url))
            context.startActivity(intent)
        } catch (e: Exception) {
            onError?.invoke("Error launching URL", e)
        }
    }
    
    private fun loadContent() {
        orderData?.let { data ->
            val html = generateHtml(data)
            webView?.loadDataWithBaseURL(null, html, "text/html", "UTF-8", null)
        }
    }
}
```

### 4. HTML Generation

```kotlin
private fun generateHtml(orderData: SovendusOrderData): String {
    val sanitizer = HtmlSanitizer(
        trafficSourceNumber = orderData.trafficSourceNumber,
        trafficMediumNumber = orderData.trafficMediumNumber,
        onError = onError
    )

    val sanitizedOrderData = sanitizeOrderData(orderData, sanitizer)
    val consumerJson = createConsumerJson(sanitizedOrderData.customerData, sanitizer)
    val resizeObserver = createResizeObserver()

    return buildHtmlTemplate(sanitizedOrderData, consumerJson, resizeObserver)
}

private fun sanitizeOrderData(data: SovendusOrderData, sanitizer: HtmlSanitizer): SovendusOrderData {
    return data.copy(
        sessionId = sanitizer.sanitize(data.sessionId),
        orderId = sanitizer.sanitize(data.orderId),
        currencyCode = sanitizer.sanitize(data.currencyCode),
        usedCouponCode = sanitizer.sanitize(data.usedCouponCode)
    )
}

private fun createConsumerJson(customerData: SovendusCustomerData?, sanitizer: HtmlSanitizer): String {
    val customer = customerData ?: SovendusCustomerData()
    
    val consumerMap = mapOf(
        "consumerSalutation" to (sanitizer.sanitizeOptional(customer.salutation) ?: ""),
        "consumerFirstName" to (sanitizer.sanitizeOptional(customer.firstName) ?: ""),
        "consumerLastName" to (sanitizer.sanitizeOptional(customer.lastName) ?: ""),
        "consumerEmail" to (sanitizer.sanitizeOptional(customer.email) ?: ""),
        "consumerPhone" to (sanitizer.sanitizeOptional(customer.phone) ?: ""),
        "consumerYearOfBirth" to (sanitizer.sanitizeIntOptional(customer.yearOfBirth) ?: ""),
        "consumerDateOfBirth" to (sanitizer.sanitizeOptional(customer.dateOfBirth) ?: ""),
        "consumerStreet" to (sanitizer.sanitizeOptional(customer.street) ?: ""),
        "consumerStreetNumber" to (sanitizer.sanitizeOptional(customer.streetNumber) ?: ""),
        "consumerZipcode" to (sanitizer.sanitizeOptional(customer.zipcode) ?: ""),
        "consumerCity" to (sanitizer.sanitizeOptional(customer.city) ?: ""),
        "consumerCountry" to (sanitizer.sanitizeOptional(customer.country) ?: "")
    )
    
    return try {
        Gson().toJson(consumerMap)
    } catch (e: Exception) {
        onError?.invoke("Error creating consumer JSON", e)
        "{}"
    }
}

private fun createResizeObserver(): String {
    return """
    // Height Management: Monitor content size changes and notify native code
    new ResizeObserver(() => {
      window.sovHandler.postMessage(JSON.stringify({
        type: 'height',
        value: document.body.scrollHeight.toString()
      }));
    }).observe(document.body);
    """
}

private fun buildHtmlTemplate(
    orderData: SovendusOrderData,
    consumerJson: String,
    resizeObserver: String
): String {
    val sanitizer = HtmlSanitizer(orderData.trafficSourceNumber, orderData.trafficMediumNumber, onError)

    return """
    <!DOCTYPE html>
    <html>
        <head>
          <meta name="viewport" content="initial-scale=1" />
        </head>
        <body id="body" style="margin: 0; background-color: #fff">
            <div id="sovendus-voucher-banner"></div>
            <script type="text/javascript">
                $resizeObserver
                window.sovApi = "v1";
                // Post Message Bridge: Handle URL opening requests from Sovendus content
                // This prevents navigation within the WebView and opens URLs in native browser
                window.addEventListener("message", (event) => {
                  if (event.data.channel === "sovendus:integration") {
                    window.sovHandler.postMessage(JSON.stringify({
                      type: 'openUrl',
                      value: event.data.payload.url
                    }));
                  }
                });
                window.sovIframes = [];
                window.sovIframes.push({
                    trafficSourceNumber: "${orderData.trafficSourceNumber}",
                    trafficMediumNumber: "${orderData.trafficMediumNumber}",
                    iframeContainerId: "sovendus-voucher-banner",
                    sessionId: "${orderData.sessionId}",
                    orderId: "${orderData.orderId}",
                    orderValue: "${sanitizer.sanitizeDouble(orderData.netOrderValue)}",
                    orderCurrency: "${orderData.currencyCode}",
                    usedCouponCode: "${orderData.usedCouponCode}",
                    integrationType: "android-$VERSION_NUMBER",
                });
                window.sovConsumer = $consumerJson;
            </script>
            <script type="text/javascript" src="https://api.sovendus.com/sovabo/common/js/flexibleIframe.js" async=true></script>
        </body>
    </html>
    """.trimIndent()
}
```

## 💻 Usage Example

### Complete Implementation

```kotlin
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import android.widget.LinearLayout
import android.widget.TextView

class OrderSuccessActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setupUI()
        addSovendusBanner()
    }

    private fun setupUI() {
        val layout = LinearLayout(this).apply {
            orientation = LinearLayout.VERTICAL
            setPadding(16, 16, 16, 16)
        }

        val thankYouText = TextView(this).apply {
            text = "Thank you for your order!"
            textSize = 18f
            setPadding(0, 0, 0, 32)
        }

        layout.addView(thankYouText)
        setContentView(layout)
    }

    private fun addSovendusBanner() {
        val customerData = SovendusCustomerData(
            salutation = "Mr.",
            firstName = "John",
            lastName = "Smith",
            email = "example@example.com",
            phone = "+4915546456456",
            yearOfBirth = 1990,
            dateOfBirth = "01.12.1990",
            street = "Main Street",
            streetNumber = "12/1",
            zipcode = "76135",
            city = "Karlsruhe",
            country = "DE"
        )

        val sovendusBanner = SovendusBanner(this)
        sovendusBanner.initialize(
            trafficSourceNumber = YOUR_TRAFFIC_SOURCE_NUMBER, // Replace with your actual number
            trafficMediumNumber = YOUR_TRAFFIC_MEDIUM_NUMBER, // Replace with your actual number
            sessionId = "unique-session-id",
            orderId = "Order-123",
            netOrderValue = 120.5,
            currencyCode = "EUR",
            usedCouponCode = "CouponCodeFromThePurchase",
            customerData = customerData,

            onError = { message, error ->
                println("Integration Error: $message - $error")
                // Log to your application's logging system
            }
        )

        // Add banner to your layout
        val layout = findViewById<LinearLayout>(android.R.id.content)
        layout.addView(sovendusBanner)
    }
}
```

## 🔧 Key Implementation Requirements

### Navigation Suppression
>
> [!CRITICAL]
> **All navigation requests within the WebView MUST be suppressed** by returning `true` in `shouldOverrideUrlLoading()`. This prevents external links from opening within the WebView and ensures they are handled via the post message bridge instead.

### Post Message Bridge for URL Handling
>
> [!IMPORTANT]
> **URL Opening Pattern**: The integration uses a post message bridge to handle URL opening:
>
> 1. Sovendus content sends messages via `window.postMessage()` with channel `"sovendus:integration"`
> 2. JavaScript listener catches these messages and forwards them to native code via `window.sovHandler.postMessage()`
> 3. Native code receives the message in the JavaScriptInterface and opens the URL in the default browser

### Dynamic Height Management
>
> [!IMPORTANT]
> **Height Adjustment Pattern**: The WebView height must dynamically adjust based on content:
>
> 1. `ResizeObserver` monitors changes to `document.body.scrollHeight`
> 2. Height changes are sent to native code via the JavaScript bridge
> 3. Native code updates the component height and triggers layout update
> 4. Only heights > 100px are applied to avoid layout issues

## 🔧 Parameter Configuration

> [!INFO]
> **Parameter Documentation**
> For detailed information on all parameters, examples, and requirements, visit: [Parameter Documentation](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Parameter)

## ⚠️ Important Android Considerations

### Permissions

Add to your `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## 🚀 Testing

1. **Integration Testing**: Verify banner loads and displays correctly
2. **Height Adjustment**: Test dynamic height changes work properly
3. **URL Handling**: Verify external links open in browser
4. **Error Handling**: Test error scenarios and reporting
5. **Performance**: Ensure no memory leaks in WebView usage

## 📞 Support

For technical support and parameter configuration, contact your Sovendus account manager or visit the [Sovendus Developer Hub](https://developer-hub.sovendus.com/).
