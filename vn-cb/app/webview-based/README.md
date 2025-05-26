# Sovendus generic WebView App integration for Voucher Network and Checkout Benefits

This documentation is for mobile Apps where the order success page is based on a WebView.

1. Add the following HTML code to your HTML based order success page:
   ```html
   <!DOCTYPE html>
   <html>
     <body>
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
           integrationType: "genericwebview-1.1.1",
         });
         window.sovConsumer = {
           consumerSalutation: "$salutation",
           consumerFirstName: "$firstName",
           consumerLastName: "$lastName",
           consumerEmail: "$email",
           consumerPhone: "$phone",
           consumerYearOfBirth: "$yearOfBirth",
           consumerDateOfBirth: "$yearOfBirth",
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
   
4. In case you choose the inline Sovendus banner make sure the <div id="sovendus-voucher-banner"></div> is placed where you want the banner to appear. In case you use the sticky banner, the position of the div doesnt matter.
5. Clicks on external links need to be prevented and forwarded to the native browser. Make sure the navigation requests of following url patterns are prevented:

   ```dart
   url_path != "/banner/api/banner" && !url_path.startsWith("/app-list") && url_path != "blank";
   ```

6. Forward navigation requests to the native browser, this can be done with one of the following ways:\
   a. Forward the URL it tried to navigate to, to the native browser

   b. Catch the javascript event "openInNativeBrowser" and open the url in the event in the native browser. This is how you can catch the event in javascript and get the URL it should navigate to:

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
