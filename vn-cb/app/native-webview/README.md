# Sovendus generic native App integration for Voucher Network and Checkout Benefits

This documentation is for mobile Apps where the order success page is native.

1. Create a webview component with the following HTML:
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
2. Make sure all variables in the HTML starting with a $ are defined
[Click here for detailed information on the parameters and which ones are required.](https://developer-hub.sovendus.com/Voucher-Network-Checkout-Benefits/Parameter)

4. Clicks on external links need to be forwarded to the native browser, this can be done with one of the following ways:\
   a. Prevent all navigation requests and forward the URL it tried to navigate to, to the native browser

   b. Prevent all navigation requests, catch the javascript event "openInNativeBrowser" and open the url in the event in the native browser. This is how you can catch the event in javascript and get the URL it should navigate to:

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

5. The height of your native component should be based on the height of the body inside the webview. You can achieve this by adding a resize observer on the body inside the webview. For example like that:
   ```javascript
   const _body = document.getElementById("body");
   new ResizeObserver(() => {
     console.log(_body.clientHeight);
   }).observe(_body);
   ```
6. Use the component you've just created where you want the Sovendus Banner to appear
