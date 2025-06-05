# 🖥️ Generic Server-Side Integration

> [!INFO]
> **Server-Side Overview**
> Sovendus displays your product at numerous partners following transactions. This server-to-server API integration ensures proper order registration and partner attribution.

## 📋 Integration Checklist

- [ ] **Obtain Credentials**
  Receive your Product ID and API key from Sovendus.

- [ ] **Understand Token Flow**
  Learn how the `sovReqToken` parameter works.

- [ ] **Implement Token Capture**
  Capture and store tokens from landing page URLs.

- [ ] **Implement API Integration**
  Set up server-to-server API calls.

- [ ] **Handle Responses**
  Implement proper response handling and error management.

- [ ] **Test Integration**
  Verify the complete token flow and API communication.

---

## 🔑 Core Concepts

### Token-Based Workflow

![Workflow](https://raw.githubusercontent.com/Sovendus-GmbH/Sovendus-Integrations-Documentation/main/docs/checkout-products/workflowimg.png)

1. **Token Generation**: Sovendus adds `sovReqToken` to your landing page URL
2. **Token Capture**: Your system captures and stores the token
3. **Order Processing**: Customer completes transaction
4. **API Call**: Your server sends token back to Sovendus via API

> [!WARNING]
> **Critical Success Factor**
> Prerequisite for successful cooperation is the return transfer of the token. Without proper back transfer, the Sovendus algorithm cannot handle your product as intended, and your product won't be displayed on partner pages.

---

## 🆔 Authentication & Credentials

### Required Credentials

| Credential | Description | Source |
|------------|-------------|--------|
| **Product ID** | Fixed alphanumerical identifier for your campaign | Provided by Sovendus |
| **API Key** | Authentication key for server-to-server calls | Provided by Sovendus |

> [!WARNING]
> **Security Notice**
> Keep your login credentials confidential (Product ID and API key). These will be sent to you by your Sovendus contact.

### Example Credentials Format

```json
{
  "productId": "9bc72425-902c-3abf-9db8-8b186d7cb160",
  "apiKey": "XXXX-XXXXX-XXXXXX-XXX"
}
```

---

## 🎯 Token Handling

### Token Parameter Structure

By default, the `sovReqToken` parameter is appended to your landing page URL:

```
https://www.website.com/landingpage?channel=sovendus&sovReqToken=XXXXX-XXXXX-XXX-XXXXX
```

**Breakdown**:

- **Your landing page URL**: `https://www.website.com/landingpage?channel=sovendus`
- **Token parameter**: `sovReqToken=XXXXX-XXXXX-XXX-XXXXX`

> [!INFO]
> **Parameter Customization**
> The parameter `sovReqToken` can be renamed by request. Please inform Sovendus about any changes.

### Token Syntax

#### Production Tokens

- **Format**: `aaaaa-aaaa-aaa-aaaaa`
- **Characteristics**: Dynamic, changes with every click
- **Example**: `12a3b-4c5d-6e7-8f9g0`

#### Test Tokens

Test tokens should follow this structure:

- **Format**: `aaaaa-test-aaa-aaaaa`
- **Second part must be**: `test`
- **Valid examples**:
  - `11111-test-111-11111`
  - `fffff-test-fff-fffff`
  - `12345-test-123-12345`
  - `1a2b3-test-1a2-1a2b3`

---

## 💻 Implementation Guide

### Step 1: Token Capture (Landing Page)

```python
# Python/Django example
from urllib.parse import urlparse, parse_qs
from django.core.cache import cache

def capture_sovendus_token(request):
    """Capture and store Sovendus token from URL parameters"""
    try:
        # Get token from URL parameters
        sov_req_token = request.GET.get('sovReqToken')
        
        if sov_req_token:
            # Store token with session ID for later retrieval
            session_id = request.session.session_key
            cache_key = f"sovendus_token_{session_id}"
            
            # Store token with 24-hour expiration
            cache.set(cache_key, sov_req_token, 86400)
            
            return {
                'success': True,
                'token': sov_req_token,
                'message': 'Token captured successfully'
            }
        
        return {
            'success': False,
            'message': 'No sovReqToken found in URL'
        }
        
    except Exception as e:
        return {
            'success': False,
            'message': f'Error capturing token: {str(e)}'
        }
```

```javascript
// Node.js/Express example
const redis = require('redis');
const client = redis.createClient();

function captureSovendusToken(req, res, next) {
    try {
        const sovReqToken = req.query.sovReqToken;
        
        if (sovReqToken) {
            // Store token with session ID
            const sessionId = req.sessionID;
            const cacheKey = `sovendus_token_${sessionId}`;
            
            // Store token with 24-hour expiration
            client.setex(cacheKey, 86400, sovReqToken);
            
            console.log(`Sovendus token captured: ${sovReqToken.substring(0, 8)}...`);
        }
        
        next();
    } catch (error) {
        console.error('Error capturing Sovendus token:', error);
        next();
    }
}
```

### Step 2: API Integration (Order Completion)

```python
# Python implementation
import requests
import json
from django.conf import settings

class SovendusAPI:
    def __init__(self):
        self.base_url = "https://press-order-api.sovendus.com/ext"
        self.product_id = settings.SOVENDUS_PRODUCT_ID
        self.api_key = settings.SOVENDUS_API_KEY
    
    def send_conversion(self, token):
        """Send conversion data to Sovendus API"""
        try:
            # Construct API URL
            url = f"{self.base_url}/{self.product_id}/{token}/api"
            
            # Prepare request data
            headers = {
                'Content-Type': 'application/json'
            }
            
            data = {
                'apiKey': self.api_key
            }
            
            # Make POST request
            response = requests.post(
                url=url,
                headers=headers,
                json=data,
                timeout=30
            )
            
            return self.handle_response(response)
            
        except requests.exceptions.RequestException as e:
            return {
                'success': False,
                'error': f'Request failed: {str(e)}'
            }
    
    def handle_response(self, response):
        """Handle API response"""
        if response.status_code == 200:
            return {
                'success': True,
                'data': response.json(),
                'message': 'Conversion sent successfully'
            }
        elif response.status_code == 403:
            return {
                'success': False,
                'error': 'Unauthorized - check API key'
            }
        elif response.status_code == 404:
            return {
                'success': False,
                'error': 'Invalid API call - check URL'
            }
        elif response.status_code == 405:
            return {
                'success': False,
                'error': 'Method not allowed - use POST'
            }
        elif response.status_code == 422:
            return {
                'success': False,
                'error': 'Invalid parameters - check Product ID and token'
            }
        else:
            return {
                'success': False,
                'error': f'Unexpected error: {response.status_code}'
            }

# Usage in order completion
def process_order_completion(request, order_id):
    try:
        # Retrieve stored token
        session_id = request.session.session_key
        cache_key = f"sovendus_token_{session_id}"
        token = cache.get(cache_key)
        
        if token:
            # Send conversion to Sovendus
            sovendus_api = SovendusAPI()
            result = sovendus_api.send_conversion(token)
            
            if result['success']:
                # Clean up token after successful use
                cache.delete(cache_key)
                print(f"Sovendus conversion sent for order {order_id}")
            else:
                print(f"Sovendus API error: {result['error']}")
        else:
            print("No Sovendus token found for this session")
            
    except Exception as e:
        print(f"Error processing Sovendus conversion: {str(e)}")
```

```javascript
// Node.js implementation
const axios = require('axios');

class SovendusAPI {
    constructor() {
        this.baseUrl = 'https://press-order-api.sovendus.com/ext';
        this.productId = process.env.SOVENDUS_PRODUCT_ID;
        this.apiKey = process.env.SOVENDUS_API_KEY;
    }

    async sendConversion(token) {
        try {
            // Construct API URL
            const url = `${this.baseUrl}/${this.productId}/${token}/api`;
            
            // Prepare request
            const config = {
                method: 'POST',
                url: url,
                headers: {
                    'Content-Type': 'application/json'
                },
                data: {
                    apiKey: this.apiKey
                },
                timeout: 30000
            };
            
            // Make request
            const response = await axios(config);
            
            return {
                success: true,
                data: response.data,
                message: 'Conversion sent successfully'
            };
            
        } catch (error) {
            return this.handleError(error);
        }
    }

    handleError(error) {
        if (error.response) {
            const status = error.response.status;
            const data = error.response.data;
            
            switch (status) {
                case 403:
                    return { success: false, error: 'Unauthorized - check API key' };
                case 404:
                    return { success: false, error: 'Invalid API call - check URL' };
                case 405:
                    return { success: false, error: 'Method not allowed - use POST' };
                case 422:
                    return { success: false, error: 'Invalid parameters - check Product ID and token' };
                default:
                    return { success: false, error: `API error: ${status}` };
            }
        } else {
            return { success: false, error: `Request failed: ${error.message}` };
        }
    }
}

// Usage in order completion
async function processOrderCompletion(sessionId, orderId) {
    try {
        // Retrieve stored token
        const cacheKey = `sovendus_token_${sessionId}`;
        const token = await client.get(cacheKey);
        
        if (token) {
            // Send conversion to Sovendus
            const sovendusAPI = new SovendusAPI();
            const result = await sovendusAPI.sendConversion(token);
            
            if (result.success) {
                // Clean up token after successful use
                await client.del(cacheKey);
                console.log(`Sovendus conversion sent for order ${orderId}`);
            } else {
                console.error(`Sovendus API error: ${result.error}`);
            }
        } else {
            console.log('No Sovendus token found for this session');
        }
        
    } catch (error) {
        console.error('Error processing Sovendus conversion:', error);
    }
}
```

---

## 📡 API Reference

### Endpoint

```
POST https://press-order-api.sovendus.com/ext/{externalProductID}/{token}/api
```

### Request Format

```http
POST /ext/9bc72425-902c-3abf-9db8-8b186d7cb160/12345-test-123-12345/api HTTP/1.1
Host: press-order-api.sovendus.com
Content-Type: application/json

{
  "apiKey": "XXXX-XXXXX-XXXXXX-XXX"
}
```

### Response Codes

| Status Code | Description | Response Body |
|-------------|-------------|---------------|
| **200 OK** | Successful request | `{"externalProductId": "...", "sovReqToken": "..."}` |
| **403 Forbidden** | Incorrect/missing API key | `{"message": "This API call is unauthorized..."}` |
| **404 Not Found** | Incorrect URL/endpoint | `{"message": "Invalid API call."}` |
| **405 Method Not Allowed** | Wrong HTTP method | `{"message": "Invalid API call."}` |
| **422 Unprocessable Entity** | Invalid parameters | `{"message": "Invalid API parameters."}` |
| **500 Internal Server Error** | Server error | Retry with delay |
| **No Response** | Timeout | Retry with delay |

---

## 🧪 Testing Your Integration

### Test Token Validation

```python
def validate_test_token(token):
    """Validate test token format"""
    import re
    
    # Test token pattern: aaaaa-test-aaa-aaaaa
    pattern = r'^[a-zA-Z0-9]{5}-test-[a-zA-Z0-9]{3}-[a-zA-Z0-9]{5}$'
    
    if re.match(pattern, token):
        return True
    return False

# Test examples
test_tokens = [
    "11111-test-111-11111",  # Valid
    "fffff-test-fff-fffff",  # Valid
    "12345-test-123-12345",  # Valid
    "1a2b3-test-1a2-1a2b3",  # Valid
    "12345-prod-123-12345",  # Invalid (not 'test')
]

for token in test_tokens:
    print(f"{token}: {'Valid' if validate_test_token(token) else 'Invalid'}")
```

### Integration Testing Steps

1. **Token Capture Test**
   - Visit landing page with test token
   - Verify token is stored correctly
   - Check token expiration handling

2. **API Communication Test**
   - Send test token via API
   - Verify 200 response with valid credentials
   - Test error handling with invalid data

3. **End-to-End Test**
   - Complete full user journey
   - Verify token flow from landing to conversion
   - Check cleanup after successful API call

---

## 🔒 Security Best Practices

### API Key Management

```python
# Environment-based configuration
import os
from django.conf import settings

class SovendusConfig:
    def __init__(self):
        self.product_id = os.getenv('SOVENDUS_PRODUCT_ID')
        self.api_key = os.getenv('SOVENDUS_API_KEY')
        
        if not self.product_id or not self.api_key:
            raise ValueError("Sovendus credentials not configured")
    
    def get_credentials(self):
        return {
            'product_id': self.product_id,
            'api_key': self.api_key
        }
```

### Token Security

- **Server-Side Only**: Never expose API keys to client-side code
- **Token Validation**: Validate token format before API calls
- **Secure Storage**: Use secure session storage or cache
- **Cleanup**: Remove tokens after successful use

---

## 🐛 Troubleshooting

### Common Issues

#### 403 Forbidden Error

**Cause**: Incorrect or missing API key
**Solution**: Verify API key is correct and included in request body

#### 422 Unprocessable Entity

**Cause**: Invalid Product ID or token format
**Solution**: Check Product ID and token format match requirements

#### No Response/Timeout

**Cause**: Network issues or server overload
**Solution**: Implement retry logic with exponential backoff

### Debug Implementation

```python
import logging

logger = logging.getLogger(__name__)

def debug_sovendus_integration(token, product_id, api_key):
    """Debug helper for Sovendus integration"""
    logger.info(f"Debugging Sovendus integration:")
    logger.info(f"Token: {token[:8]}... (length: {len(token)})")
    logger.info(f"Product ID: {product_id}")
    logger.info(f"API Key: {'*' * (len(api_key) - 4)}{api_key[-4:]}")
    
    # Validate token format
    if validate_test_token(token):
        logger.info("Token format: Valid test token")
    else:
        logger.warning("Token format: Production token or invalid")
```

---

## 📞 Support

For technical implementation questions regarding the Sovendus API for products with external order routes, contact Sovendus via email or phone.

### Before Contacting Support

Please have the following information ready:

- Your Product ID
- Example token values
- Error messages or response codes
- Implementation language/framework

---

**🎉 Your server-side integration is now ready to track conversions accurately via API!**
