# Events API

> [!INFO]
> **API Overview**
> The Events API allows your shop to react to changes within Sovendus applications using PostMessage events. This enables real-time interaction with overlay states and user actions.

## 🚀 PostMessage Events (sovApi)

The integration script provides event-driven communication between your shop and Sovendus applications. Events are sent via [postMessage](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage) and can be captured using `addEventListener`.

### 🔧 How It Works

- **Context**: Events are sent within the context where the Sovendus integration script is embedded (normally the window object)
- **Propagation**: Events also propagate to the parent if nested (e.g., in an iframe)
- **Version Control**: You must specify the API version you want to use

### 📋 Setup Requirements

To receive events, you must set the API version:

```javascript
window.sovApi = "latest";
window.addEventListener("message", (event) => {
  // Check event origin for security
  if (event.origin !== "/* origin where the integration script is embedded */") {
    return;
  }

  // Handle overlay events
  if (event.data.channel === "sovendus:overlay") {
    if (event.data.payload.action === "close") {
      console.log("Sovendus overlay closed");
      // Your custom logic here
    }
  }
});
```

---

## 📊 Supported Events

### 🎯 Overlay Events (sovendus:overlay)

| Action | Payload | Description |
|--------|---------|-------------|
| `close` | `{action: "close", version: "v1"}` | Overlay is permanently closed by user (cannot be reopened) |
| `collapse` | `{action: "collapse", version: "v1"}` | Overlay is collapsed to floating button (toggle state) |
| `expand` | `{action: "expand", version: "v1"}` | Overlay is expanded from collapsed state (toggle state) |

> [!INFO]
> **Overlay State Behavior**
>
> - **Toggle overlays**: Use `collapse`/`expand` events for overlays that can be minimized and restored
> - **Permanent close**: Use `close` event for overlays that can only be dismissed, not reopened

### 🔗 Integration Events (sovendus:integration)

| Action | Payload | Description |
|--------|---------|-------------|
| `openInNativeBrowser` | `{action: "openInNativeBrowser", url: "example.com/url", version: "v1"}` | Triggered when user clicks on any external link within Sovendus applications |

> [!INFO]
> **External Link Handling**
> The `openInNativeBrowser` event fires for all external links clicked within Sovendus overlays, allowing you to handle link opening behavior (e.g., opening in native browser for mobile apps).

---

## 💡 Implementation Examples

### Basic Event Listener

```javascript
window.sovApi = "v1"; // Use specific version instead of "latest"

window.addEventListener("message", (event) => {
  // Security check - replace with your actual origin
  if (event.origin !== "https://your-domain.com") {
    return;
  }

  const { channel, payload } = event.data;

  switch (channel) {
    case "sovendus:overlay":
      handleOverlayEvent(payload);
      break;
    case "sovendus:integration":
      handleIntegrationEvent(payload);
      break;
  }
});

function handleOverlayEvent(payload) {
  switch (payload.action) {
    case "close":
      console.log("User permanently closed Sovendus overlay");
      // Track analytics, hide related UI elements, etc.
      // Note: Overlay cannot be reopened after this event
      break;
    case "collapse":
      console.log("Sovendus overlay collapsed to floating button");
      // Update UI to show overlay is minimized but still available
      break;
    case "expand":
      console.log("Sovendus overlay expanded from collapsed state");
      // Update UI to show overlay is now fully visible
      break;
  }
}

function handleIntegrationEvent(payload) {
  if (payload.action === "openInNativeBrowser") {
    console.log("External link clicked:", payload.url);
    // Handle external link opening (e.g., for mobile apps)
    // This fires for ALL external links within Sovendus applications
    window.open(payload.url, '_blank');
  }
}
```

### Advanced Event Handling

```javascript
class SovendusEventHandler {
  constructor() {
    this.setupEventListener();
  }

  setupEventListener() {
    window.sovApi = "v1";
    window.addEventListener("message", this.handleMessage.bind(this));
  }

  handleMessage(event) {
    // Validate origin
    if (!this.isValidOrigin(event.origin)) {
      return;
    }

    const { channel, payload } = event.data;
    
    // Only handle Sovendus events
    if (!channel?.startsWith("sovendus:")) {
      return;
    }

    this.processEvent(channel, payload);
  }

  isValidOrigin(origin) {
    const allowedOrigins = [
      "https://your-domain.com",
      "https://api.sovendus.com"
    ];
    return allowedOrigins.includes(origin);
  }

  processEvent(channel, payload) {
    // Log for debugging
    console.log(`Sovendus Event: ${channel}`, payload);

    // Dispatch custom events for your application
    const customEvent = new CustomEvent('sovendus-event', {
      detail: { channel, payload }
    });
    document.dispatchEvent(customEvent);
  }
}

// Initialize handler
const sovendusEvents = new SovendusEventHandler();
```

---

## ⚠️ Important Guidelines

> [!WARNING]
> **Version Management**
> While we support "latest" as a version, we **strongly recommend** using specific versions (v1, v2, etc.) to prevent breaking changes from affecting your site.

> [!ERROR]
> **Internal Events**
> **DO NOT** base your implementation on internal postMessage events used for communication between Sovendus applications. These can change at any time and will break your integration.

> [!INFO]
> **Security Best Practices**
>
> - Always validate the event origin
> - Only listen to documented channels (prefixed with "sovendus:")
> - Use specific API versions instead of "latest"

### 🔒 Security Checklist

- [ ] **Origin Validation**: Check `event.origin` matches your expected domain
- [ ] **Channel Filtering**: Only process events with "sovendus:" prefix
- [ ] **Version Pinning**: Use specific version numbers (v1, v2) not "latest"
- [ ] **Error Handling**: Implement try-catch blocks for event processing

---

## 🆘 Troubleshooting

### Common Issues

- **Events not firing**
  - Verify `window.sovApi` is set before the Sovendus script loads
  - Check browser console for JavaScript errors
  - Ensure event listener is attached to the correct window object

- **Security errors**
  - Verify origin validation logic
  - Check if your domain matches the expected origin
  - Ensure HTTPS is used in production

- **Version conflicts**
  - Use specific version numbers instead of "latest"
  - Check payload structure matches expected version format

### 🔧 Debug Helper

```javascript
// Debug helper to log all Sovendus events
window.sovApi = "v1";
window.addEventListener("message", (event) => {
  if (event.data?.channel?.startsWith("sovendus:")) {
    console.group("🔍 Sovendus Event Debug");
    console.log("Origin:", event.origin);
    console.log("Channel:", event.data.channel);
    console.log("Payload:", event.data.payload);
    console.groupEnd();
  }
});
```

---

## 📞 Support

Need additional events or have questions about implementation?

- **Contact**: Your Sovendus account manager
- **Integration Testing**: Use our debug helper above

---

**🎉 You're now ready to implement real-time event handling with Sovendus!**
