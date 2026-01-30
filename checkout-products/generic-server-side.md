# Server-Side Integration

Track conversions from Sovendus partners with a server-to-server API integration.

## 🚀 Quick Start

1. **Get credentials** from Sovendus (Product ID + API Key)
2. **Capture token** on landing page
3. **Send API call** on order completion
4. **Test** the integration

---

## ⚙️ How It Works

![Workflow](https://raw.githubusercontent.com/Sovendus-GmbH/Sovendus-Integrations-Documentation/main/checkout-products/workflowimg.png)

---

## 🔑 Your Credentials

Sovendus will provide you with:

| Credential | Example | Description |
|------------|---------|-------------|
| `Product ID` | `9bc72425-902c-3abf-9db8-8b186d7cb160` | Your campaign identifier |
| `API Key` | `XXXX-XXXXX-XXXXXX-XXX` | Authentication key |

> [!WARNING]
> **Keep these credentials secure** - never expose them in client-side code.

---

## 📝 Step 1: Capture Token

When users land on your page with `?sovReqToken=abc123`, store it temporarily:

```python
# Python/Django example
from django.core.cache import cache

def capture_token(request):
    token = request.GET.get('sovReqToken')
    if token:
        session_id = request.session.session_key
        cache.set(f"sovendus_token_{session_id}", token, 2592000)  # 30 days expiry
        print(f"✅ Token captured: {token[:8]}...")
```

```javascript
// Node.js example
function captureToken(req, res, next) {
    const token = req.query.sovReqToken;
    if (token) {
        const key = `sovendus_token_${req.sessionID}`;
        redis.setex(key, 2592000, token);  // 30 days expiry
        console.log(`✅ Token captured: ${token.substring(0, 8)}...`);
    }
    next();
}
```

---

## 🎯 Step 2: Send API Call

When an order completes, send the token back to Sovendus:

```python
# Python example
import requests

def send_conversion(token, product_id, api_key):
    url = f"https://press-order-api.sovendus.com/ext/{product_id}/{token}/api"

    response = requests.post(url, json={"apiKey": api_key}, timeout=30)

    if response.status_code == 200:
        print("✅ Conversion sent successfully")
        return True
    else:
        print(f"❌ API error: {response.status_code}")
        return False

# Usage in order completion
def process_order(request, order_id):
    session_id = request.session.session_key
    token = cache.get(f"sovendus_token_{session_id}")

    if token:
        success = send_conversion(token, PRODUCT_ID, API_KEY)
        if success:
            cache.delete(f"sovendus_token_{session_id}")  # Clean up
```

```javascript
// Node.js example
async function sendConversion(token, productId, apiKey) {
    const url = `https://press-order-api.sovendus.com/ext/${productId}/${token}/api`;

    try {
        const response = await fetch(url, {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify({ apiKey }),
        });

        if (response.ok) {
            console.log('✅ Conversion sent successfully');
            return true;
        } else {
            console.log(`❌ API error: ${response.status}`);
            return false;
        }
    } catch (error) {
        console.log(`❌ Network error: ${error.message}`);
        return false;
    }
}

// Usage in order completion
async function processOrder(sessionId, orderId) {
    const token = await redis.get(`sovendus_token_${sessionId}`);

    if (token) {
        const success = await sendConversion(token, PRODUCT_ID, API_KEY);
        if (success) {
            await redis.del(`sovendus_token_${sessionId}`);  // Clean up
        }
    }
}
```

---

## 📡 API Reference

### Endpoint

```
POST https://press-order-api.sovendus.com/ext/{PRODUCT_ID}/{TOKEN}/api
```

### Request Body

```json
{
  "apiKey": "YOUR_API_KEY"
}
```

### Response Codes

| Code | Meaning |
|------|---------|
| `200` | ✅ Success |
| `403` | ❌ Invalid API key |
| `422` | ❌ Invalid Product ID or token |
| `404` | ❌ Wrong URL |

> [!WARNING]
> ** Optional **
> In addition to the above, you may also wish to store the sovReqToken alongside the order’s information - such as by sending the token to your backend - to persist it for a longer time. This way, you will be able to locate the token to be sent to Sovendus for cancellation purposes, if needed.

---

## 🧪 Test Your Integration

### Quick Test

1. **Add test token** to your landing page URL:

   ```
   https://yoursite.com/landing?sovReqToken=12345-test-123-12345
   ```

2. **Check token storage** in your server logs or cache

3. **Complete test order** and verify API call in logs:

   ```
   POST https://press-order-api.sovendus.com/ext/YOUR_PRODUCT_ID/12345-test-123-12345/api
   ```

### Debug Helper

```python
def debug_sovendus(token, product_id, api_key):
    print(f"Token exists: {bool(token)}")
    print(f"Product ID: {product_id}")
    print(f"API Key: {'*' * (len(api_key) - 4)}{api_key[-4:]}")
```

---

## 🔧 Troubleshooting

### Common Issues

- **403 Forbidden**: Check your API key
- **422 Invalid**: Verify Product ID and token format
- **No token found**: Ensure token capture is working on landing page

---

## 📞 Need Help?

Contact your Sovendus representative with:

- Your Product ID
- Example token values
- Error messages
- Your tech stack (Python/Node.js/etc.)
