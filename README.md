# CommerceHub — Full-Stack E-Commerce Demo

CommerceHub is a polished portfolio demonstration of a full-stack fashion
store, customized by **Glacier InfoTech** from the open-source
[ecommerce-store project](https://github.com/TahirPK007/ecommerce-store).
The original project was created by [Tahir Mehmood](https://tahirmehmood-dev.vercel.app).

## Features

- **Storefront** — hero, category browsing, search, sorting, product detail
  pages, and a persistent cart backed by local storage.
- **Authentication** — email/password sign up and sign in through Supabase
  Auth, with protected account pages.
- **Database-backed catalog** — products served from Supabase Postgres with
  Row Level Security, with a static demo catalog fallback when Supabase is
  not configured.
- **Checkout** — Stripe Checkout in test mode with server-side price
  validation.
- **Orders** — paid orders recorded through a Stripe webhook and an
  idempotent success-page confirmation flow.
- **Admin panel** — role-gated dashboard for product CRUD and order review.

## Tech stack

| Area | Technology |
|------|------------|
| Framework | Next.js 14 App Router |
| Language | TypeScript |
| Styling | Tailwind CSS |
| Database and auth | Supabase (Postgres, Auth, and RLS) |
| Payments | Stripe Checkout |
| UI | Framer Motion, react-hot-toast, and react-icons |

## Local setup

### 1. Install dependencies

```bash
npm install
```

### 2. Configure environment variables

```bash
cp .env.example .env.local
```

Fill in the values documented in
[`.env.example`](./.env.example):

- **Supabase**: `NEXT_PUBLIC_SUPABASE_URL`,
  `NEXT_PUBLIC_SUPABASE_ANON_KEY`, and the server-only
  `SUPABASE_SERVICE_ROLE_KEY`.
- **Stripe**: `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY`,
  `STRIPE_SECRET_KEY`, and `STRIPE_WEBHOOK_SECRET` for webhook verification.
- **Site URL**: `NEXT_PUBLIC_SITE_URL`, used for Stripe redirect URLs.

The application can run its storefront and static demo catalog without
Supabase or Stripe credentials. Authentication, persistent products, orders,
admin features, and live checkout require the corresponding external
services. Never expose or commit the Supabase service-role key, Stripe secret
key, webhook secret, or a real `.env.local` file.

### 3. Set up Supabase

Run these files in the Supabase SQL Editor, in order:

1. [`supabase/schema.sql`](./supabase/schema.sql) — products, profiles, RLS,
   seed data, and the new-user trigger.
2. [`supabase/orders.sql`](./supabase/orders.sql) — orders and order RLS.

After signing up, make an account an admin:

```sql
update public.profiles set is_admin = true
where id = (select id from auth.users where email = 'you@example.com');
```

### 4. Run the application

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

## Test checkout

Use Stripe's test card on the checkout page:

- Card: `4242 4242 4242 4242`
- Expiry: any future date
- CVC: any three digits
- ZIP: any value

To exercise the production webhook locally, install the
[Stripe CLI](https://stripe.com/docs/stripe-cli) and run:

```bash
stripe listen --forward-to localhost:3000/api/webhooks/stripe
```

Copy the resulting `whsec_...` value into `STRIPE_WEBHOOK_SECRET`.

## Project structure

```text
app/
  (storefront)        # home, products, product detail, cart, checkout
  account/            # protected account and order history
  admin/              # role-gated dashboard, product CRUD, orders
  api/                # checkout, order confirmation, Stripe webhooks
  login/ signup/      # authentication pages
components/           # Navbar, Footer, ProductCard, and UI components
context/              # cart and auth providers
lib/                  # Supabase clients, Stripe, data, and orders
supabase/             # schema and orders SQL
```

## Deploy to Vercel

1. Import the repository into [Vercel](https://vercel.com/new).
2. Keep the existing Next.js build settings; Vercel detects them from
   `package.json`.
3. Add the variables from [`.env.example`](./.env.example) in the Vercel
   project settings. Set `NEXT_PUBLIC_SITE_URL` to the deployed URL.
4. In Stripe, optionally create a webhook endpoint at
   `https://your-app.vercel.app/api/webhooks/stripe` and set its signing
   secret as `STRIPE_WEBHOOK_SECRET`.
5. Configure the Supabase project URL, API keys, SQL schema, and redirect
   URLs for the deployed domain.

## Attribution and license

CommerceHub is a customized open-source-based portfolio demo. The original
application and its MIT license statement are preserved from
[TahirPK007/ecommerce-store](https://github.com/TahirPK007/ecommerce-store).
Original author attribution: [Tahir Mehmood](https://tahirmehmood-dev.vercel.app).

This repository does not claim authorship of the original application.
CommerceHub-specific branding and customization are by Glacier InfoTech.
