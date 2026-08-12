# KLYVEN backend release guide

## Implemented in this project

- PostgreSQL relational schema for customers, addresses, catalog, any number of colours, variants, sizes through XXXL, product size charts, carts, wishlists, coupons, orders, payment records, reviews, tracking events and notifications.
- Password hashing, signed authentication tokens, email-verification token architecture, customer profiles and role-based access control.
- Customer APIs, server-side order calculation, inventory locking, coupon usage limits, Manual UPI **Payment Verification** state, configurable COD fee, reviews with delivered-order verification, and privacy-safe tracking lookup.
- Admin APIs for dashboard metrics, products/variants, coupon creation, customers, orders, UPI verification, status updates, review moderation and notification history.
- Provider-neutral email queue abstraction and approved-information-only chatbot configuration.

## Setup

1. Install Node.js 20+ and PostgreSQL 15+.
2. Copy `.env.example` to `.env`, then replace `DATABASE_URL` and `JWT_SECRET`. Never commit `.env`.
3. Run `npm install`.
4. Create a PostgreSQL database, then run `npm run db:migrate`.
5. Create the initial owner directly in PostgreSQL, set a bcrypt password hash, and change its `role` to `owner`. There is intentionally no public “make me admin” API.
6. Run `npm run dev`. The API starts on `http://localhost:4000` by default.
7. Connect the existing frontend to the `/api/*` endpoints only after setting the production API origin and testing in staging.

## Manual UPI policy

The checkout API accepts a reference ID, but never treats it as proof of payment. Manual UPI orders enter `payment_verification` and the payment stays `verification_required`. Only an authorized admin/order manager can call `POST /api/admin/orders/:id/verify-upi`, which records the reviewer and timestamp.

## Notification architecture

Orders queue a customer **order received** email and an optional owner summary. Status changes queue payment-confirmed, processing, shipped, delivered, cancelled and refunded messages. The email text includes the required KLYVEN tracking notice and only provides tracking after an actual tracking value exists. `EMAIL_PROVIDER=console` is safe for local development; connect a provider in `server/services/notifications.js` for production.

## Chatbot policy

`server/chatbot-config.js` holds only approved KLYVEN facts. It never queries or guesses order details. Tracking requests are sent to `/track-order`; unknown questions show the configured support contact.

## Release classification

| Category | Included now |
|---|---|
| FREE | Static frontend, schema, API architecture, local PostgreSQL development, console email mode, chatbot rules. |
| REQUIRES CONFIGURATION | `.env`, database migration, owner account, real UPI ID/QR, store settings, CORS origin, shipping/return/contact details. |
| REQUIRES EXTERNAL PROVIDER | Hosted PostgreSQL, production email, payment gateway (if added), shipping/tracking carrier, domain/hosting, WhatsApp/SMS/analytics. |
| OPTIONAL LATER | Razorpay/Cashfree gateway adapter, image storage/CDN, WhatsApp notifications, staff admin UI, scheduled retry worker, search service, analytics dashboard. |

## Security release checklist

- Use HTTPS and a production database with backups.
- Generate a 32+ byte random `JWT_SECRET`; rotate it if exposed.
- Keep provider/database keys server-side only; do not put them in `config.js` or browser code.
- Restrict production CORS to your storefront domain.
- Add rate limiting, transactional email retry worker and audit logging before high-volume traffic.
- Test UPI verification, COD calculation, coupon limits, stock races, role checks and order notifications in staging.
