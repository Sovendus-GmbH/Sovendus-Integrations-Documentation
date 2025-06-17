# 🔒 Content Security Policy Configuration

Configure your Content Security Policy (CSP) headers to ensure Sovendus Voucher Network & Checkout Benefits works seamlessly with your website's security settings.

---

## 🛡️ Required CSP Directives

### Essential Headers

Configure these CSP directives to allow Sovendus functionality:

| Directive   | Value                                                                                                                                                         | Explanation |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| connect-src | https://\*.sovendus.com <https://www.sovendus-benefits.com> <https://www.sovendus-campaign.com> <https://www.sovendus-connect.com> <https://www.sovendus-network.com> | Controls which URLs the site can make network requests to (XMLHttpRequest, WebSocket, fetch). Required for API calls and data fetching from Sovendus services. |
| font-src    | https://\*.sovendus.com                                                                                                                                       | Specifies valid sources for fonts loaded with @font-face. Allows custom fonts from Sovendus domains to ensure consistent branding and typography. |
| frame-src   | https://\*.sovendus.com <https://www.sovendus-benefits.com> <https://www.sovendus-campaign.com> <https://www.sovendus-connect.com> <https://www.sovendus-network.com> | Controls which URLs can be embedded as frames/iframes. Essential for displaying Sovendus voucher banners and embedded content securely. |
| img-src     | data: https://\*.sovendus.com                                                                                                                                 | Defines valid sources for images. Includes **data:** for inline base64 images and Sovendus domains for voucher logos, brand images, and promotional graphics. |
| script-src  | 'unsafe-inline' https://\*.sovendus.com                                                                                                                       | Specifies valid sources for JavaScript. **'unsafe-inline'** allows inline scripts, and Sovendus domains enable loading of tracking and banner functionality scripts. |
| style-src   | 'unsafe-inline' https://\*.sovendus.com                                                                                                                       | Controls valid sources for stylesheets. **'unsafe-inline'** permits inline styles, and Sovendus domains allow custom CSS for proper banner styling and responsive design. |

---

## 📋 Implementation Guide

### Step 1: Identify Your CSP Method

Choose the appropriate implementation method for your setup:

- **HTTP Headers** (recommended)
- **HTML Meta Tags**
- **Server Configuration**
- **CDN/Proxy Settings**

### Step 2: Update CSP Configuration

#### Option A: HTTP Headers

```http
Content-Security-Policy: 
  connect-src 'self' https://*.sovendus.com https://www.sovendus-benefits.com https://www.sovendus-campaign.com https://www.sovendus-connect.com https://www.sovendus-network.com;
  font-src 'self' https://*.sovendus.com;
  frame-src 'self' https://*.sovendus.com https://www.sovendus-benefits.com https://www.sovendus-campaign.com https://www.sovendus-connect.com https://www.sovendus-network.com;
  img-src 'self' data: https://*.sovendus.com;
  script-src 'self' 'unsafe-inline' https://*.sovendus.com;
  style-src 'self' 'unsafe-inline' https://*.sovendus.com;
```

#### Option B: HTML Meta Tag

```html
<meta http-equiv="Content-Security-Policy" content="
  connect-src 'self' https://*.sovendus.com https://www.sovendus-benefits.com https://www.sovendus-campaign.com https://www.sovendus-connect.com https://www.sovendus-network.com;
  font-src 'self' https://*.sovendus.com;
  frame-src 'self' https://*.sovendus.com https://www.sovendus-benefits.com https://www.sovendus-campaign.com https://www.sovendus-connect.com https://www.sovendus-network.com;
  img-src 'self' data: https://*.sovendus.com;
  script-src 'self' 'unsafe-inline' https://*.sovendus.com;
  style-src 'self' 'unsafe-inline' https://*.sovendus.com;
">
```

---

## 🔧 Server-Specific Configuration

### Apache (.htaccess)

```apache
<IfModule mod_headers.c>
    Header always set Content-Security-Policy "connect-src 'self' https://*.sovendus.com https://www.sovendus-benefits.com https://www.sovendus-campaign.com https://www.sovendus-connect.com https://www.sovendus-network.com; font-src 'self' https://*.sovendus.com; frame-src 'self' https://*.sovendus.com https://www.sovendus-benefits.com https://www.sovendus-campaign.com https://www.sovendus-connect.com https://www.sovendus-network.com; img-src 'self' data: https://*.sovendus.com; script-src 'self' 'unsafe-inline' https://*.sovendus.com; style-src 'self' 'unsafe-inline' https://*.sovendus.com;"
</IfModule>
```

### Nginx

```nginx
add_header Content-Security-Policy "connect-src 'self' https://*.sovendus.com https://www.sovendus-benefits.com https://www.sovendus-campaign.com https://www.sovendus-connect.com https://www.sovendus-network.com; font-src 'self' https://*.sovendus.com; frame-src 'self' https://*.sovendus.com https://www.sovendus-benefits.com https://www.sovendus-campaign.com https://www.sovendus-connect.com https://www.sovendus-network.com; img-src 'self' data: https://*.sovendus.com; script-src 'self' 'unsafe-inline' https://*.sovendus.com; style-src 'self' 'unsafe-inline' https://*.sovendus.com;" always;
```

### Node.js/Express

```javascript
const express = require('express');
const app = express();

app.use((req, res, next) => {
    res.setHeader('Content-Security-Policy',
        "connect-src 'self' https://*.sovendus.com https://www.sovendus-benefits.com https://www.sovendus-campaign.com https://www.sovendus-connect.com https://www.sovendus-network.com; " +
        "font-src 'self' https://*.sovendus.com; " +
        "frame-src 'self' https://*.sovendus.com https://www.sovendus-benefits.com https://www.sovendus-campaign.com https://www.sovendus-connect.com https://www.sovendus-network.com; " +
        "img-src 'self' data: https://*.sovendus.com; " +
        "script-src 'self' 'unsafe-inline' https://*.sovendus.com; " +
        "style-src 'self' 'unsafe-inline' https://*.sovendus.com;"
    );
    next();
});
```

### PHP

```php
<?php
header("Content-Security-Policy: connect-src 'self' https://*.sovendus.com https://www.sovendus-benefits.com https://www.sovendus-campaign.com https://www.sovendus-connect.com https://www.sovendus-network.com; font-src 'self' https://*.sovendus.com; frame-src 'self' https://*.sovendus.com https://www.sovendus-benefits.com https://www.sovendus-campaign.com https://www.sovendus-connect.com https://www.sovendus-network.com; img-src 'self' data: https://*.sovendus.com; script-src 'self' 'unsafe-inline' https://*.sovendus.com; style-src 'self' 'unsafe-inline' https://*.sovendus.com;");
?>
```

---

## 📋 Implementation Checklist

### Pre-Implementation

- **Plan implementation method**
  - [ ] Choose server-level vs application-level
  - [ ] Consider staging environment testing
  - [ ] Prepare rollback plan

### Implementation Steps

- **Update CSP configuration**
  - [ ] Add connect-src directives
  - [ ] Add font-src directives
  - [ ] Add frame-src directives
  - [ ] Add img-src directives
  - [ ] Add script-src directives
  - [ ] Add style-src directives

- **Test configuration**
  - [ ] Verify Sovendus banner loads
  - [ ] Check browser console for CSP errors
  - [ ] Test all Sovendus functionality
  - [ ] Validate on multiple browsers

---

## 🛠️ Troubleshooting

### Common CSP Violations

| Error | Cause | Solution |
|-------|-------|----------|
| **Refused to connect** | Missing connect-src | Add Sovendus domains to connect-src |
| **Refused to load font** | Missing font-src | Add **https://*.sovendus.com** to font-src |
| **Refused to frame** | Missing frame-src | Add Sovendus domains to frame-src |
| **Refused to load image** | Missing img-src | Add **data:** and Sovendus domains to img-src |
| **Refused to execute script** | Missing script-src | Add **'unsafe-inline'** and Sovendus domains |
| **Refused to apply style** | Missing style-src | Add **'unsafe-inline'** and Sovendus domains |

### Debug Steps

#### 1. Check Browser Console

```javascript
// Monitor CSP violations in browser console
window.addEventListener('securitypolicyviolation', (e) => {
    console.log('CSP Violation:', {
        directive: e.violatedDirective,
        blockedURI: e.blockedURI,
        originalPolicy: e.originalPolicy
    });
});
```

#### 2. Test CSP Configuration

```bash
# Test CSP headers with curl
curl -I https://yoursite.com | grep -i content-security-policy

# Validate CSP syntax
# Use online CSP validators or browser dev tools
```

#### 3. Gradual Implementation

```http
# Start with report-only mode for testing
Content-Security-Policy-Report-Only: connect-src 'self' https://*.sovendus.com; report-uri /csp-report

# Then switch to enforcement mode
Content-Security-Policy: connect-src 'self' https://*.sovendus.com;
```

---

## ⚠️ Important Warnings

> [!WARNING]
> **Production Safety**
> The example code provided is for testing purposes only. Do not use the test code in production environments without proper security review.

> [!INFO]
> **Testing Recommendation**
> Always test CSP changes in a staging environment before deploying to production.

---

## 📞 Support & Resources

- **🛠️ Integration Tester**: [Test Your Setup](https://developer-hub.sovendus.com/Integration-Tester)
- **💬 Support**: Contact your Sovendus account manager
- **🔒 CSP Resources**: [MDN CSP Guide](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)

---

**🔒 Ready to secure your Sovendus integration? Configure your CSP today!**
