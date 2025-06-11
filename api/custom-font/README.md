# Custom Font for Voucher Network & Checkout Benefits

## 📝 Steps to Use a Custom Font

1. **Host the Font File(s) on Your Server**
   - Ensure the font file(s) are hosted on the same domain as your website.

2. **Verify Font Accessibility**
   - Open the font URL in a browser to confirm accessibility.
     - **Example Font URL:** `my-domain.com/fonts/my-font.woff2`
   - If the font is not accessible, configure the `Access-Control-Allow-Origin` header for the following domains:
     - `www.sovendus-connect.com`
     - `*.sovendus.com`
     - `www.sovendus-benefits.com`
     - Or set it to `*` (all domains).

   - **Apache Server Example:** Add the following to your `.htaccess` file:

     ```apache
     <FilesMatch "fonts\.(woff2)$">
         <IfModule mod_headers.c>
             Header set Access-Control-Allow-Origin "*"
         </IfModule>
     </FilesMatch>
     ```

3. **Send the Font URL(s) to Sovendus UX**
   - Once the font is accessible from the Sovendus server, share the font URL(s) with the Sovendus UX team.
   - The UX team will use the custom font for your Sovendus banner.
