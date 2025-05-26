# Custom font for voucher network & checkout benefits

## 1. Host the font file(s) on your server

Make sure the font file(s) are hosted on the same domain as your website.

## 2. Make sure the font file(s) are accessible

To check if your font is accessible by any domain, open the font URL in the browser. If it's accessible, the font will be displayed or downloaded.

**Example font URL:** `my-domain.com/fonts/my-font.woff2`

If you get an error message when opening the font URL, you need to set an `Access-Control-Allow-Origin` header for the font, for the domains `www.sovendus-connect.com`, `*.sovendus.com`, and `www.sovendus-benefits.com`, or to `*` (all domains).

Configuration of `Access-Control-Allow-Origin` is different for every web server. To find the documentation for your web server, you can search for “access-control-allow-origin MY-WEBSERVER-NAME”.

For example, on an Apache server, you can add the following to your `.htaccess`:

```apache
<FilesMatch "fonts\.(woff2)$">
    <IfModule mod_headers.c>
        Header set Access-Control-Allow-Origin "*"
    </IfModule>
</FilesMatch>
```

## 3. Send the font URL(s) to Sovendus UX

Once your font is accessible from the Sovendus server, our UX team can use the custom font for your Sovendus banner.
