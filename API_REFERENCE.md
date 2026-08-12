# KLYVEN API reference

All money values are integer paise. Public endpoints never return private keys, password hashes or payment secrets.

## Customer

- `POST /api/auth/register`, `POST /api/auth/login`, `POST /api/auth/verify-email`
- `GET /api/me`, `GET|POST /api/addresses`
- `GET /api/products`, `GET /api/products/:slug`
- `POST /api/cart/items`, `GET|PUT|DELETE /api/wishlist/:productId`
- `POST /api/orders`, `GET /api/track-order?orderNumber=...&identifier=...`
- `POST /api/reviews`, `POST /api/chatbot/answer`

`POST /api/orders` calculates item prices, shipping, COD and coupons within a database transaction. Browser totals are display-only and cannot be trusted.

## Admin

All `/api/admin/*` endpoints require `Authorization: Bearer <JWT>` and server-side role checks. There is no client-side admin bypass.

- `GET /api/admin/dashboard`, `/orders`, `/products`, `/coupons`, `/customers`, `/notifications`
- `POST|PATCH /api/admin/products`, `POST /api/admin/products/:id/variants`
- `POST /api/admin/coupons`
- `POST /api/admin/orders/:id/verify-upi`
- `PATCH /api/admin/orders/:id/status`, `PATCH /api/admin/reviews/:id`

## Payment behaviour

- `manual_upi`: `transactionReference` is stored as a reference only; order starts in `payment_verification` and requires admin verification.
- `cod`: total includes configured `cod.feePaise`; payment remains unpaid until fulfilment flow confirms it.
- Payment-gateway support is intentionally not present until a verified provider adapter and webhook signature validation are added.
