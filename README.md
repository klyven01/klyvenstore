# KLYVEN — Storefront MVP

## What is implemented

- Premium responsive storefront (360px through desktop), navigation, home, shop, product detail and order-tracking pages.
- Data-driven products in `config.js`: arbitrary colour variants, per-colour SKU/stock, configurable sizes, product-specific size charts, badges, content and media.
- Local browser cart, wishlist, quantity changes, discount coupons, shipping threshold and configurable COD fee.
- Checkout validation plus safe Manual UPI flow: it never requests PIN/OTP/CVV; the UTR keeps an order in **Payment Verification**.
- Accessible basics: semantic controls, labels, focus target, reduced-motion support, alt text, responsive layout, no horizontal overflow.

## Ready for integration (not live yet)

- Authentication, verification/reset email, customer accounts, persistent orders, inventory, admin moderation, reviews, shipping tracking, analytics, WhatsApp, chat, email and image upload.
- Payment gateways: add a provider adapter server-side. Do not process card details in this app.
- Manual UPI QR: set `STORE.manualUpi.qrImage` after uploading a real QR asset.

## Requires an external service / secrets

| Feature | Recommended connection | Secret/config |
|---|---|---|
| Database & auth | Supabase / PostgreSQL | database URL, service key (server only) |
| Payments | Razorpay / Cashfree | key ID, secret, webhook signature |
| Email | Resend / AWS SES | API key, verified domain |
| WhatsApp | Meta Cloud API | access token, phone-number ID |
| Shipping | Shiprocket / Delhivery | API credentials, webhook secret |
| Analytics | GA4 / PostHog | public project ID; never expose server keys |

Keep secrets in server-side environment variables, never `config.js` or browser code.

## Run locally

This is a dependency-free static MVP. Open `index.html` in a browser or serve this folder with any static host. For deployment, connect the folder to Netlify, Vercel, Cloudflare Pages, or a similar static host. Before public launch, move checkout/order logic into a server/API and replace the demo confirmation.

## Suggested production data model

`products`, `product_variants` (product_id, colour, hex, SKU, stock), `size_charts`, `customers`, `addresses`, `carts`, `orders`, `order_items`, `coupons`, `coupon_redemptions`, `reviews`, `shipments`, `admin_users`, and `audit_logs`.

## Maintenance checklist

1. Add/edit products and variants in the future admin, never UI markup.
2. Review inventory and coupon limits before each drop.
3. Verify UPI payments manually before changing order status to Confirmed.
4. Keep legal, shipping, return and privacy pages accurate.
5. Test checkout, payment webhooks, mobile navigation and all listed viewports with real services before launch.
