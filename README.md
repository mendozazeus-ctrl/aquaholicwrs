# Aquaholic — Water Refilling Station Management System

A full-stack web application for managing a water refilling station business. Built with TanStack Start (React + SSR), Netlify Database (Postgres via Drizzle ORM), and deployed on Netlify.

## Features

- **Dashboard** — Real-time stats (customers, deliveries, revenue, riders) with charts and recent activity
- **Deliveries** — Create, track, and manage delivery orders with status filtering
- **Rider Routes** — Plan and track multi-stop delivery routes for riders
- **Customers** — Customer records with address, contact, and balance tracking
- **Riders** — Manage delivery staff with status (active / on delivery / inactive)
- **Inventory** — Product catalog with stock levels, low-stock alerts, and transaction history
- **Collections** — Payment collection records linked to deliveries, multi-payment method support
- **Reports** — Revenue charts, payment method breakdown, delivery status analytics, top customers

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Framework | TanStack Start (React 19 + SSR) |
| Router | TanStack Router v1 (file-based) |
| Database | Netlify Database (managed Postgres) |
| ORM | Drizzle ORM @beta |
| Styling | Tailwind CSS v4 + custom CSS variables |
| Charts | Chart.js + react-chartjs-2 |
| Icons | lucide-react |
| Build | Vite 7 |
| Deployment | Netlify |

## Running Locally

```bash
npm install
netlify dev --port 8889
```

> Requires Netlify CLI and a linked Netlify site for database access.

## Seed Data

On first deploy, visit `/api/seed` (POST) or use the browser dev tools to POST to seed demo data. The seeder is a no-op if data already exists.
