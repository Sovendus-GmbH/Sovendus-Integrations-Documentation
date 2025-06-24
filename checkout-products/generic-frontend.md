# Frontend Integration

Track conversions from Sovendus partners with a simple JavaScript integration.

## 🚀 Quick Start

1. **Capture token** on landing page
2. **Fire pixel** on order completion
3. **Test** the integration

---

## 🔗 How It Works

When users click your offer on partner sites, they arrive with a `sovReqToken` parameter:

```
https://yoursite.com/landing?sovReqToken=abc123-def456-ghi789
```

**Your job**: Capture this token and send it back when they complete an order.

---

## 📝 Step 1: Capture Token

Add this to your **landing page**:

```javascript
// Capture token from URL and store it
function captureSovendusToken() {
  const token = new URLSearchParams(window.location.search).get('sovReqToken');
  if (token) {
    localStorage.setItem('sovReqToken', token);
    console.log('✅ Sovendus token captured');
  }
}

// Run on page load
document.addEventListener('DOMContentLoaded', captureSovendusToken);
```

---

## 🎯 Step 2: Fire Pixel

Add this to your **order completion page**:

```javascript
// Send token back to Sovendus
function fireSovendusPixel() {
  const token = localStorage.getItem('sovReqToken');

  if (token) {
    // Create tracking pixel
    const pixel = document.createElement('img');
    pixel.src = `https://press-order-api.sovendus.com/ext/image?sovReqToken=${token}`;
    pixel.style.display = 'none';
    document.body.appendChild(pixel);

    // Clean up
    localStorage.removeItem('sovReqToken');
    console.log('✅ Sovendus pixel fired');
  }
}

// Run on page load
document.addEventListener('DOMContentLoaded', fireSovendusPixel);
```

---

## 🧪 Test Your Integration

### Quick Test

1. **Add test token** to your landing page URL:

   ```
   https://yoursite.com/landing?sovReqToken=test-token-123
   ```

2. **Check localStorage** in browser dev tools:

   ```javascript
   localStorage.getItem('sovReqToken') // Should return: test-token-123
   ```

3. **Complete test order** and check Network tab for pixel request to:

   ```
   https://press-order-api.sovendus.com/ext/image?sovReqToken=test-token-123
   ```

---

## ⚠️ Important Notes

> [!WARNING]
> **Only fire the pixel if a token exists**
> This ensures you only track users who came from Sovendus partners.

> [!INFO]
> **Token parameter name**
> The token parameter name can be customized. Contact Sovendus if you need to change `sovReqToken`.

---

## 📞 Need Help?

Contact your Sovendus representative if you have questions or need assistance with the integration.
