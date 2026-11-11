# Muslim Bangla Bibaho (মুসলিম বাংলা বিবাহ)

> A Bengali-language Muslim matrimony platform where people publish a structured biodata, search for a compatible partner, and connect through a respectful, request-and-consent flow.

## About the product

Muslim Bangla Bibaho (also referred to in the code as "Muslim Bangla Marriage" and, internally, as the *Shabaka* marriage service) is a web-based matrimony platform for Bengali-speaking Muslims. Members create a detailed **biodata** (the traditional Bangla marriage profile) covering personal, educational, professional, family, religious, and location details, together with the expectations they have of a life partner. Biodatas are discoverable through a searchable, filterable directory, and every screen is written in Bangla.

Rather than exposing contact details openly, the platform is built around a **consent-based connection flow**. A member sends a request to see another biodata's full information; the receiving member accepts or rejects it, unanswered requests expire automatically, and the sender's request credit is refunded when appropriate. Members can mark biodatas as liked (পছন্দ) or disliked (অপছন্দ), see how well they match another person's stated expectations, report problematic profiles, and optionally buy an assisted "call/message support" service in which a support agent contacts the other party on their behalf.

The product is delivered as a **Next.js web application** (the folder name `akd` appears to refer to *aqd*/*akd*, the Islamic marriage contract) backed by a **Node.js/Express REST API**. The API shares a PostgreSQL database with separate admin and payment services, and integrates with an external customer-auth service, a chat service, SMS, email, Firebase push notifications, and RabbitMQ.

## At a glance

| | |
|---|---|
| **Type** | Matrimony / marriage-matching web platform (B2C, Bangla-first) |
| **Platforms** | Responsive web app (Next.js). The frontend also shows signs of being embedded in a host mobile app (`app_id`/`type` query handling, theme query param) - TODO: confirm |
| **Primary language of UI** | Bangla (with Bangla numerals and Arabic/Bangla text helpers) |
| **Status** | Actively developed (repo history spans March 2025 to November 2026). TODO: confirm production/live status |
| **Live URL** | TODO: add live URL |
| **Frontend repo** | `mb-akd` - Next.js 16 / React 19 customer-facing web app |
| **Backend repo** | `mb-akd-api` - Node.js/Express REST API (Sequelize, PostgreSQL, Redis, RabbitMQ) |
| **Related services (not in scope here)** | Admin API / admin panel, payment API, chat API, customer-support API, notification service, central login/auth service |
| **My role** | TODO: see the placeholder checklist in [case-study.md](case-study.md#my-role--contributions) |

## Tech stack

| Layer | Technologies |
|---|---|
| **Frontend** | Next.js 16 (App Router, Turbopack dev), React 19, TypeScript 5, Tailwind CSS 4, Radix UI primitives + shadcn-style component set, PrimeReact, Zustand (state), React Hook Form + Zod (forms/validation), Framer Motion / Motion, Lottie, Recharts (stats charts), Embla carousel, Sonner (toasts), next-themes, pino (logging), cryptr (encrypted local storage) |
| **Backend** | Node.js, Express 4, Sequelize 5 ORM, jsonwebtoken, bcryptjs, Axios, `cron` (scheduled jobs), Winston + daily-rotate-file + express-winston (logging), nodemailer, amqplib |
| **Database / cache** | PostgreSQL (multiple logical databases: marriage/"shabaka", customer, quran/QA legacy models, chat), Redis (caching, view counters, distributed locks) |
| **Messaging** | RabbitMQ (notification queue, Telegram log queue) |
| **Infra / deployment** | PM2 cluster mode (8 instances) for the API; environment-driven config (`.env.*`); maintenance-mode switch in the frontend; Winston log rotation. No Dockerfile or CI config found in either repo |
| **Integrations** | Central customer auth service (token validation + Google OAuth + OTP login), bKash / Nagad / SSLCommerz payment flows (initiated via a separate payment service), SMS gateway, SMTP email, Firebase Cloud Messaging (Android + iOS service accounts), Socket.IO realtime notifications, separate chat service, customer-support service, Telegram bot logging, Google Analytics (gtag), country/ads API |

## Documentation in this folder

| File | What it covers |
|---|---|
| [case-study.md](case-study.md) | Narrative case study: problem, goals, solution, users, architecture, technical decisions, learnings, and a placeholder for your role |
| [key-features.md](key-features.md) | Feature inventory grouped by domain, tagged `[frontend]` / `[api]`. Delete the bullets you did not work on |
| [architecture.md](architecture.md) | Repo-by-repo breakdown: structure, modules, data model, auth, deployment, integrations, diagrams |
| [assets/](assets/) | Drop screenshots here (see below) |

### Screenshots to add to `assets/`

TODO: capture and save the following (suggested file names in parentheses):

1. Landing page with hero, biodata counts, and statistics (`landing.png`)
2. Search page with the filter panel, chips, and biodata cards (`search.png`)
3. Biodata detail page including the expectation-match table and connection button (`biodata-detail.png`)
4. Multi-step biodata creation form (`biodata-form.png`)
5. Dashboard: connection tabs (received, sent, connected, rejected, discontinued) and favourites (`dashboard.png`)
6. Biodata-fee pricing/payment modal and payment-method picker (`biodata-fee.png`)
7. Call/message support purchase dialog and the floating chat widget (`support-chat.png`)
8. Statistics dashboard and successful-marriages timeline (`stats.png`)
9. Dark mode and mobile views (`dark-mode.png`, `mobile.png`)

Redact any real member names, phone numbers, and photos before publishing.
