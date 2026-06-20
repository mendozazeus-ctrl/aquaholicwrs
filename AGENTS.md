# AGENTS.md — AquaFlow Project Architecture

This document describes the project architecture for developers and AI agents working on this codebase.

## Project Overview

AquaFlow is a water refilling station management web application. It covers operations (deliveries, routes), management (customers, riders, inventory, collections), and analytics (reports).

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Framework | TanStack Start (React 19, SSR) |
| Router | TanStack Router v1 (file-based routing) |
| Database | Netlify Database (managed Postgres) |
| ORM | Drizzle ORM @beta with `drizzle-orm/netlify-db` adapter |
| Styling | Tailwind CSS v4 + custom CSS variables in `src/styles.css` |
| Charts | Chart.js + react-chartjs-2 |
| Icons | lucide-react |
| Language | TypeScript (strict mode) |

## Directory Structure

```
├── db/
│   ├── index.ts          # Drizzle client (drizzle-orm/netlify-db)
│   └── schema.ts         # All table definitions (source of truth)
├── drizzle.config.ts     # Points migrations → netlify/database/migrations/
├── netlify/
│   └── database/
│       └── migrations/   # Auto-generated SQL migrations (drizzle-kit generate)
├── src/
│   ├── routes/
│   │   ├── __root.tsx    # App shell: sidebar layout, nav groups
│   │   ├── index.tsx     # Dashboard page
│   │   ├── deliveries.tsx
│   │   ├── riders.tsx
│   │   ├── routes.tsx    # Rider routes page
│   │   ├── customers.tsx
│   │   ├── inventory.tsx
│   │   ├── collections.tsx
│   │   ├── reports.tsx
│   │   └── api/
│   │       └── seed.ts   # Demo data seeder (POST /api/seed, no-op if data exists)
│   ├── server/
│   │   ├── dashboard.functions.ts   # getDashboardStats
│   │   ├── customers.functions.ts   # CRUD for customers
│   │   ├── riders.functions.ts      # CRUD for riders
│   │   ├── deliveries.functions.ts  # CRUD for deliveries
│   │   ├── inventory.functions.ts   # CRUD for products + inventory transactions
│   │   ├── collections.functions.ts # CRUD for collections + reports data
│   │   └── routes.functions.ts      # CRUD for rider routes + stops
│   ├── router.tsx        # TanStack Router setup
│   └── styles.css        # Global CSS (dark theme, design tokens, custom classes)
```

## Database Schema (db/schema.ts)

| Table | Purpose |
|-------|---------|
| `customers` | Customer records (name, phone, address, balance, totalOrders) |
| `products` | Product catalog (name, size, price, stock, lowStockThreshold) |
| `riders` | Rider records (name, phone, status enum) |
| `deliveries` | Delivery orders (FK: customer, rider, product; status enum; isPaid) |
| `inventory_transactions` | Stock in/out log with product FK |
| `collections` | Payment records (FK: customer, delivery; amount; paymentMethod enum) |
| `routes` | Delivery route plans (FK: rider; date; status) |
| `route_stops` | Individual stops in a route (FK: route, delivery; stopOrder; completed) |

### Enums
- `delivery_status`: pending | in_transit | delivered | cancelled
- `rider_status`: active | inactive | on_delivery
- `payment_method`: cash | gcash | maya | bank_transfer | credit
- `tx_type`: in | out

## Design System

All styling uses custom CSS variables in `src/styles.css` — no Tailwind utility classes for layout:

| Token | Value | Use |
|-------|-------|-----|
| `--bg-base` | `#0c1220` | Page background |
| `--bg-surface` | `#111827` | Cards, sidebar |
| `--bg-elevated` | `#1a2438` | Input backgrounds, table headers |
| `--accent` | `#38bdf8` | Links, active states, CTAs |
| `--success` | `#34d399` | Delivered, active, stock OK |
| `--warning` | `#fbbf24` | Pending, low stock alerts |
| `--danger` | `#f87171` | Cancelled, errors, low stock |

**Fonts**: `DM Sans` (body) + `Space Mono` (numbers, monospace, labels)

## Coding Conventions

- **Server functions** live in `src/server/*.functions.ts` — use `createServerFn` from `@tanstack/react-start`
- **Input validation**: always use `.inputValidator(...)` (not `.validator()` which doesn't exist)
- **DB imports**: import `db` from `../../db/index.js` (include `.js` extension)
- **Schema imports**: import from `../../db/schema.js`
- **State pattern**: pages use `useState` initialized from `Route.useLoaderData()`, then call server functions to mutate and reload
- **Modals**: inline modal components rendered conditionally, closed via overlay click

## Schema Changes

Any schema change requires running:
```bash
npx drizzle-kit generate
```
This creates a new migration in `netlify/database/migrations/`. Never modify applied migrations.

## Seed Data

`/api/seed` (POST) seeds demo data: 5 customers, 3 riders, 4 products, 5 deliveries, 2 collections, 3 inventory transactions. It is a no-op if customers table is non-empty.
