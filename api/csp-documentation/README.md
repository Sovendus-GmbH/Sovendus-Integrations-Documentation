# Content Security Policy for Voucher Network and Checkout Benefits

## Headers

| Directive   | Value                                                                                                                                                         |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| connect-src | https://\*.sovendus.com https://www.sovendus-benefits.com https://www.sovendus-campaign.com https://www.sovendus-connect.com https://www.sovendus-network.com |
| font-src    | https://\*.sovendus.com                                                                                                                                       |
| frame-src   | https://\*.sovendus.com https://www.sovendus-benefits.com https://www.sovendus-campaign.com https://www.sovendus-connect.com https://www.sovendus-network.com |
| img-src     | data: https://\*.sovendus.com                                                                                                                                 |
| script-src  | 'unsafe-inline' https://\*.sovendus.com                                                                                                                       |
| style-src   | 'unsafe-inline' https://\*.sovendus.com                                                                                                                       |

## Example code

> [!WARNING]
> Think about it before using it! Do not use on production!

This code can be copied into the public directory of whatever API you want. Then simply call it via your browser.

```php
<?php
$contentSecurityPolicies = [
    'default-src \'none\'',
    'connect-src https://*.sovendus.com https://www.sovendus-benefits.com https://www.sovendus-campaign.com https://www.sovendus-connect.com https://www.sovendus-network.com',
    'font-src https://*.sovendus.com',
    'frame-src https://*.sovendus.com https://www.sovendus-benefits.com https://www.sovendus-campaign.com https://www.sovendus-connect.com https://www.sovendus-network.com',
    'img-src data: https://*.sovendus.com',
    'script-src \'unsafe-inline\' https://*.sovendus.com',
    'style-src \'unsafe-inline\' https://*.sovendus.com',
];

if (! empty($contentSecurityPolicies)) {
    header(sprintf('Content-Security-Policy: %s', implode('; ', $contentSecurityPolicies)));
}
?>
<html>
    <head>
    </head>
    <body>
        <p>
            hello world
        </p>
        <div id="sovendus-container-1">
            <!-- the integration loads the content into this div element -->
        </div>
        <script type="text/javascript">
            window.sovIframes = window.sovIframes || [];
            window.sovIframes.push({
                trafficSourceNumber     : '2',
                trafficMediumNumber     : '2',
                sessionId               : 'c27bffa5-cf85-48ff-a435-773fa1761d5d',
                timestamp               : '1681728336',
                orderId                 : '4bcfb7fc-afad-4ca5-ba2a-a20e1d190f71',
                orderValue              : '17.17',
                orderCurrency           : 'EUR',
                usedCouponCode          : 'FOOBAR17',
                iframeContainerId       : 'sovendus-container-1'
            });

            window.sovConsumer = window.sovConsumer || {};
            window.sovConsumer = {
                consumerSalutation      : 'Mr.',
                consumerFirstName       : 'Foo',
                consumerLastName        : 'Bar',
                consumerEmail           : 'foo@bar.net',
                consumerStreet          : 'Fakestreet',
                consumerStreetNumber    : '17',
                consumerCountry         : 'DE',
                consumerZipcode         : '50997'
            };

            var script = document.createElement("script");
            script.type = "text/javascript";
            script.async = true;
            script.src = window.location.protocol + "//api.sovendus.com/sovabo/common/js/flexibleIframe.js";
            document.body.appendChild(script);
        </script>
    </body>

</html>
```
