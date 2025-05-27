# 🛒 Generic Frontend Integration

> [!INFO]
> **Checkout Products Overview**
> Sovendus places your offers with numerous advertising partners following an order/transaction. This integration ensures accurate order recording and proper attribution to advertising partners.

## 📋 Integration Checklist

- [ ] **Understand Token Flow**
  Learn how the `sovReqToken` parameter works.

- [ ] **Implement Token Capture**
  Capture and cache the token on your landing page.

- [ ] **Implement Billing Pixel**
  Add the tracking pixel to your order completion page.

- [ ] **Test Integration**
  Verify token flow and pixel firing.

---

## 🔑 Core Concepts

### Token-Based Tracking System

Sovendus uses a dynamic token system to track conversions:

1. **Token Generation**: A unique `sovReqToken` is generated for each visitor
2. **Token Transmission**: The token is appended to your landing page URL
3. **Token Caching**: You must capture and store the token temporarily
4. **Token Return**: The cached token is sent back via the billing pixel

> [!WARNING]
> **Critical Success Factor**
> The basic prerequisite for successful cooperation is the correct return of the token value. If the token value is transmitted incorrectly or not at all, the Sovendus algorithm stops working and your offer will not be displayed.

---

## 🎯 Token Value Handling

### Token Parameter

By default, the `sovReqToken` parameter is appended to your landing page URL on every call.

### Token Characteristics

- **Dynamic**: Value changes with every click on the offer
- **Unique**: Each visitor receives a different token
- **Temporary**: Must be cached until order completion
- **Required**: Essential for proper conversion tracking

### Example URL Structure

```
https://partner.domain/path-to-landing-page?param=value&sovReqToken=1095752fb409-457a-af14-17a09e6be522
```

**Breakdown**:

- **Your landing page URL**: `partner.domain/path-to-landing-page?param=value`

> [!WARNING]
> **Parameter Customization**
> The parameter `sovReqToken` can be renamed. Please inform your Sovendus contact person about any changes.

---

## 💻 Implementation Guide

### Step 1: Landing Page Token Capture

Implement token capture on your landing page:

```javascript
/**
 * Capture and store the Sovendus request token
 */
function captureSovendusToken() {
  try {
    // Get URL parameters
    const urlParams = new URLSearchParams(window.location.search);
    const sovReqToken = urlParams.get('sovReqToken');
    
    if (sovReqToken) {
      // Store token in localStorage with expiration
      const tokenData = {
        value: sovReqToken,
        timestamp: Date.now(),
        expires: Date.now() + (24 * 60 * 60 * 1000 * 30) // 30 days
      };
      
      localStorage.setItem('sovReqToken', JSON.stringify(tokenData));
      console.log('Sovendus token captured:', sovReqToken.substring(0, 8) + '...');
      
      return true;
    }
    
    return false;
  } catch (error) {
    console.error('Error capturing Sovendus token:', error);
    return false;
  }
}

// Execute on page load
document.addEventListener('DOMContentLoaded', captureSovendusToken);
```

### Step 2: Token Retrieval

Retrieve the stored token on your order completion page:

```javascript
/**
 * Retrieve the stored Sovendus token
 */
function getSovendusToken() {
  try {
    const tokenData = localStorage.getItem('sovReqToken');
    
    if (tokenData) {
      const parsed = JSON.parse(tokenData);
      
      // Check if token has expired
      if (Date.now() < parsed.expires) {
        return parsed.value;
      } else {
        // Clean up expired token
        localStorage.removeItem('sovReqToken');
        console.log('Sovendus token expired and removed');
      }
    }
    
    return null;
  } catch (error) {
    console.error('Error retrieving Sovendus token:', error);
    return null;
  }
}
```

---

## 📊 Billing Pixel Implementation

### Pixel Structure

The billing pixel is a simple image tag that fires when the order completion page loads:

```html
<img src="https://press-order-api.sovendus.com/ext/image?sovReqToken=[TOKEN_VALUE]" 
     width="1" 
     height="1" 
     style="display: none;" 
     alt="Sovendus Tracking Pixel">
```

### Dynamic Pixel Generation

```javascript
/**
 * Generate and fire the Sovendus billing pixel
 */
function fireSovendusBillingPixel() {
  const sovReqToken = getSovendusToken();
  
  if (sovReqToken) {
    // Create the tracking pixel
    const pixel = document.createElement('img');
    pixel.src = `https://press-order-api.sovendus.com/ext/image?sovReqToken=${sovReqToken}`;
    pixel.width = 1;
    pixel.height = 1;
    pixel.style.display = 'none';
    pixel.alt = 'Sovendus Tracking Pixel';
    
    // Add pixel to page
    document.body.appendChild(pixel);
    
    // Clean up token after use
    localStorage.removeItem('sovReqToken');
    
    console.log('Sovendus billing pixel fired successfully');
    return true;
  } else {
    console.log('No Sovendus token found - pixel not fired');
    return false;
  }
}

// Fire pixel on order completion page
document.addEventListener('DOMContentLoaded', fireSovendusBillingPixel);
```

---

## 🔒 Security Considerations

### Token Validation

```javascript
/**
 * Validate token format before use
 */
function isValidSovendusToken(token) {
  if (!token || typeof token !== 'string') {
    return false;
  }
  
  // Basic format validation (adjust based on actual token format)
  const tokenPattern = /^[a-f0-9]{8}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{12}$/i;
  return tokenPattern.test(token);
}
```

### Conditional Pixel Firing

```javascript
/**
 * Only fire pixel if user came from Sovendus
 */
function shouldFireSovendusPixel() {
  const token = getSovendusToken();
  
  // Check if token exists and is valid
  if (!token || !isValidSovendusToken(token)) {
    return false;
  }
  
  // Additional checks can be added here
  // e.g., referrer validation, user session checks
  
  return true;
}

// Conditional pixel firing
if (shouldFireSovendusPixel()) {
  fireSovendusBillingPixel();
}
```

---

## 🧪 Testing Your Integration

### Testing Checklist

1. **Token Capture Test**
   - Visit your landing page with a test token
   - Verify token is stored in localStorage
   - Check browser console for capture confirmation

2. **Token Persistence Test**
   - Navigate through your site
   - Verify token remains in storage
   - Test token expiration handling

3. **Pixel Firing Test**
   - Complete a test order
   - Check browser Network tab for pixel request
   - Verify pixel fires only once per order

### Debug Tools

```javascript
// Debug function to check token status
function debugSovendusIntegration() {
  const token = getSovendusToken();
  console.log('Current Sovendus token:', token);
  console.log('Token valid:', isValidSovendusToken(token));
  console.log('Should fire pixel:', shouldFireSovendusPixel());
}

// Call in browser console for debugging
debugSovendusIntegration();
```

---

## ⚠️ Important Requirements

> [!WARNING]
> **Mandatory Requirements**
>
> - The billing pixel must only be called if the user reached your page via Sovendus
> - The `sovReqToken` parameter must be passed in the billing pixel
> - Other parameters cannot be processed by Sovendus

---

## 📞 Support

If you encounter issues with the integration:

1. **Verify token capture** is working on your landing page
2. **Confirm pixel firing** on order completion
3. **Contact your Sovendus representative** for assistance

---

**🎉 Your Checkout Products frontend integration is now ready to track conversions accurately!**
