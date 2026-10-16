# Parallel Yarns — Studio V6 (handmade crochet storefront)

Responsive static storefront built with HTML, CSS and vanilla JavaScript, with a WhatsApp-first checkout. Online payment is intentionally disabled for now. Studio V6 uses a simple left sidebar with no category submenu, a shared top-right wishlist/cart area, horizontally scrollable category filters inside Shop, and a smooth product-to-cart flight animation.

## The one-file product catalogue
**All product information lives in the single root-level `products.json` file.** To update products, replace that file with your exported JSON (keep the filename exactly `products.json`) and redeploy the project. You do not need to edit any product section in JavaScript.

The JSON must be an array of product objects. Each object should have `id`, `name`, `category`, `price`, `image`, and `description`. Optional fields include `oldPrice`, `badge`, `colors`, `featured`, and `newest`. Keep image paths relative to the project root, for example `assets/images/tulip-bouquet.svg`.

Example:
```json
[
  {
    "id": "tulip-bouquet",
    "name": "Tulip Bouquet",
    "category": "Bouquets",
    "price": 1299,
    "oldPrice": 1499,
    "badge": "Bestseller",
    "image": "assets/images/tulip-bouquet.svg",
    "description": "A handmade crochet tulip bouquet.",
    "colors": ["Blush pink", "Lilac", "Cream"],
    "featured": true,
    "newest": 9
  }
]
```

## Run locally
Use VS Code Live Server or another local web server. Opening the HTML directly as `file://` may block the browser from fetching `products.json`.

## Before launch
1. Edit `js/data.js` only for store settings: WhatsApp number, email, shipping fee, free-shipping threshold and Razorpay public key.
2. Replace sample images with your own optimized photos.
3. Test product browsing, cart, wishlist, checkout and WhatsApp order flow on mobile and desktop.
4. Drag the project folder into Netlify or deploy the repository. `netlify.toml` configures the publish directory and functions.

## Admin editor
Open `admin.html` on the deployed Netlify site to manage the catalogue. Follow `SUPABASE_SETUP.md` to enable live JSON imports into Supabase and upload embedded product photos to Supabase Storage. Until Supabase is configured, the storefront falls back to the static `products.json`. Individual product edits in the admin editor remain local drafts; the live import endpoint upserts imported products online.

## Important limitations
- Cart and wishlist are stored in the visitor's browser.
- WhatsApp orders are only sent after the customer reviews the prefilled message and presses Send.
- Online payment is disabled in checkout. The existing optional Razorpay server-function files are retained in the project but are not connected to the customer checkout UI.
- This starter does not include a database-backed order-management system or production webhook reconciliation.
- Sample product prices, contact details and illustrations must be replaced before launch.


## V16: price range and admin sign-in

### Price filtering
The Shop sidebar includes presets for Under ₹100, ₹100–₹500, ₹500–₹1,000 and Over ₹1,000, plus a custom dual-slider range and numeric min/max fields. Apply the range to filter the visible catalogue. The filter/dropdown stacking order is intentionally kept low to avoid covering unrelated UI. Product cards also open their details when the user clicks the card background; the heart, cart and text links retain their own actions.

### Configure the admin password securely on Netlify
The admin password is **not stored in HTML or JavaScript**. The login is checked by `netlify/functions/admin-auth.mjs`, which uses server-side environment variables and sets a short-lived `HttpOnly; Secure; SameSite=Strict` session cookie.

1. In Netlify, open **Site configuration → Environment variables**.
2. Add `ADMIN_PASSWORD` with a long, unique password.
3. Add `ADMIN_SESSION_SECRET` with a long random secret (at least 32 random bytes). Generate one locally, for example with `openssl rand -base64 48`. Do not put either value in `js/data.js`, HTML, Git, or a public `.env` file.
4. Redeploy the site so the function receives the variables.
5. Open `/admin.html` over the deployed HTTPS site and sign in. Localhost/127.0.0.1 intentionally bypasses this sign-in for local development only.

**Important limitation:** the current admin is still a static catalogue editor. It edits browser-local data and downloads `products.json`; it does not write changes to a live database or commit files to Netlify. The login keeps the password out of public client files and gates the normal admin interface, but a fully tamper-proof live admin requires a server-side database/API that verifies the session on every write. Treat the JSON export + redeploy as the publish workflow for this version.

## V19 updates — slow shop glide, secure admin credentials and custom yarn colours

- Shop category chips now use the same gentle mobile-only auto-glide speed as the homepage category strip. Touch, pointer, wheel and keyboard interaction pause the glide; reduced-motion settings are respected.
- Product details include coordinated colour swatches plus a native custom colour picker. The chosen named colour or custom HEX shade is included in WhatsApp enquiries and is saved as the cart variant, then shown in cart and checkout summaries.
- The deployed `/admin.html` sign-in uses the server-side Netlify Function. Keep `ADMIN_PASSWORD` and `ADMIN_SESSION_SECRET` in **Netlify environment variables** only. They are read by the function at runtime and are not placed in HTML, browser JavaScript, `products.json`, or the built client bundle. Never paste their values into public files or commit a `.env` file.
- To configure access, go to **Netlify → Site configuration → Environment variables**, add those two variables, then redeploy. Use a unique password and a long random session secret. On a deployed HTTPS domain, the admin page asks for the password before revealing the editor.

**Publishing limitation still applies:** admin sign-in protects the normal hosted editor interface, but this version is a static catalogue editor. Edits are local to that browser until you download `products.json`, replace the project file and redeploy. It does not write to a live database or publish edits by itself. For multi-device live editing, connect a database-backed API and verify the admin session for every write.
