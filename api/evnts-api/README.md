# Events API for Sovendus Voucher Network & Checkout Benefits

## PostMessage Events (sovApi)

The integration script provides a way to give the shop the capability to react on events that are related to changes within the applications of Sovendus. The events are sent via postMessage (see https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage ), which can be read using the addEventListener. The messages are thrown within the context where the Sovendus integrations script is embedded (normally the window object), but it also propagates it to the parent in case you nest it, e.g. in an iframe. To be able to receive those events you have to pass the version you want to window.sovApi:

```javascript
window.sovApi = "latest";
window.addEventListener("message", (event) => {
  // Check event origin
  if (
    event.origin !== "/* origin where the integration script is embedded */"
  ) {
    return;
  }

  if (event.data.channel === "sovendus:overlay") {
    if (event.data.payload.action === "close") {
      // ...
    }
  }
});
```

## Following events are currently supported:

| channel                | payload                                                                                     | description                                                    |
| ---------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------------------- |
| `sovendus:overlay`     | {action: "close", version: “v1”}                                                            | Overlay is closed.                                             |
| `sovendus:overlay`     | {action: "collapse", version: “v1”}                                                         | Overlay is collapsed to a floating button.                     |
| `sovendus:overlay`     | {action: "expand", version: “v1”}                                                           | Overlay is shown in its expanded form.                         |
| `sovendus:integration` | {action: "openInNativeBrowser", url: "example.com/url-to-open-in-a-new-tab", version: “v1”} | In case an external link has to be opened on a native browser. |

Though we support "latest" as a version, we do not recommend using it to avoid breaking your site, because the payload may change in future versions. The current latest version is part of the payload (v1, v2, …).

As a common practice, you should check the origin of the event.

We use postMessage() internally for communication between our applications and the integration script. `DO NOT` base your implementation on any of them, as they can change at any time. To prevent your site from breaking, you should only listen to the channels listed above (with semicolon).

If you need to keep track of a specific change in our applications, we may be able to provide you with an appropriate event. Avoid code that reacts on structural changes of your page.
