# Sovendus generic native App integration for Voucher Network and Checkout Benefits

## 📝 Steps to Integrate Sovendus in a Native App

1. **Create a WebView Component**
   - Use the following HTML for the WebView:

   ```html
   <!DOCTYPE html>
   <html>
     <head>
       <meta name="viewport" content="initial-scale=1" />
     </head>
     <body id="body">
       <div id="sovendus-container"></div>
       <script type="text/javascript">
         window.sovIframes = window.sovIframes || [];
         window.sovIframes.push({
          trafficSourceNumber: "$trafficSourceNumber",
          trafficMediumNumber: "$trafficMediumNumber",
          iframeContainerId: "sovendus-container",
          timestamp: "$orderUnixTime",
          sessionId: "$sessionId",
          orderId: "$orderId",
          orderValue: "$netOrderValue",
          orderCurrency: "$currencyCode",
          usedCouponCode: "$usedCouponCode",
          integrationType: "genericnative-1.1.1",
         });
         window.sovConsumer = {
           consumerSalutation: "$salutation",
           consumerFirstName: "$firstName",
           consumerLastName: "$lastName",
           consumerEmail: "$email",
           consumerPhone: "$phone",
           consumerYearOfBirth: "$yearOfBirth",
           consumerDateOfBirth: "01.12.2020",
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

2. **Define All Variables**
   - Ensure all variables in the HTML starting with a `$` are defined.
   - [Click here for detailed information on the parameters and which ones are required.](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Parameter)

3. **Handle External Links**
   - Forward clicks on external links to the native browser. This can be done in one of the following ways:

     a. Prevent all navigation requests and forward the URL it tried to navigate to, to the native browser.

     b. Prevent all navigation requests, catch the JavaScript event `openInNativeBrowser`, and open the URL in the event in the native browser. Use the following code to catch the event and get the URL:

     ```javascript
     window.sovApi = "v1";
     window.addEventListener("message", (event) => {
       if (event.data.channel === "sovendus:integration") {
         if (event.data.payload.action === "openInNativeBrowser") {
           console.log("URL to open", event.data.payload.url);
         }
       }
     });
     ```

4. **Adjust Component Height**
   - The height of your native component should be based on the height of the body inside the WebView. Use a resize observer on the body inside the WebView:

     ```javascript
     const _body = document.getElementById("body");
     new ResizeObserver(() => {
       console.log(_body.clientHeight);
     }).observe(_body);
     ```

5. **Use the Component**
   - Use the component you've just created where you want the Sovendus Banner to appear.
