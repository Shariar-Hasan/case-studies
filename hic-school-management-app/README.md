# Hic School Management App

> A full-stack school management platform for an Islamic educational institution: admissions, fees and online payments, academics, staff payroll, and role-based administration, with a separate parent-facing portal.

## Overview

Hic School Management App is a two-sided web platform. The **management app** is an internal admin and staff dashboard used to run the institution day to day: programs and sessions, student admissions and registrations, class sections ("levels"), attendance, exams and grading, homework, timetables and events, fees collection, scholarships, refunds, expenses, staff payroll, accounting entries, and communication. Access to every screen and API endpoint is controlled by a granular permission system (170+ permission slugs, roles, and per-user overrides).

The **public website and parent portal** lets families discover programs, create an account (email or Google sign-in), add their children, register them for a program, pay online (card checkout), track orders and installments, apply for scholarships, and follow each child's attendance, grades, and homework. Both sides talk to NestJS REST APIs backed by one shared PostgreSQL database, with RabbitMQ handling asynchronous email and payment-confirmation work.

The codebase includes a number of production-minded details: transactional fee and refund logic with a double-entry accounting ledger, idempotent payment webhooks, an audit log that captures every data change, time-boxed admin impersonation with its own audit trail, and real-time UI sync over WebSockets.

## At a glance

| | |
|---|---|
| **Type** | Full-stack web platform (admin dashboard + public site / parent portal + 2 REST APIs) |
| **Domain** | Education / school administration (Islamic educational institution, Quran academy branding in templates) |
| **Platforms** | Responsive web (desktop and mobile browsers); containerized with Docker |
| **Status** | TODO: confirm status (in production / pilot / internal). Repos show active development (recent work on admin impersonation and guardian account management). |
| **Live URL** | TODO: add live URL(s) if public |
| **Timeline / team size** | TODO |

### Repositories

| Repo | Role | Stack |
|---|---|---|
| `hic-school-management` | **management-app**: admin / staff / teacher dashboard | Next.js 16, React 19, TypeScript, Tailwind CSS 4 |
| `hic-school-management-api` | **management-api**: backend for the management app; owns DB migrations, RBAC, fees, accounting, payroll, queues, WebSockets | NestJS 11, TypeORM, PostgreSQL, Redis, RabbitMQ |
| `hic-school-web` | **web**: public school website and parent/guardian portal | Next.js 16, React 19, TypeScript, Tailwind CSS 4 |
| `hic-school-web-api` | **web-api**: backend for the public site and parent portal; registration, orders, parent/child data, Google OAuth | NestJS 11, TypeORM, PostgreSQL, RabbitMQ |

## Tech stack

| Group | Technologies |
|---|---|
| **Frontend** | Next.js 16 (App Router), React 19, TypeScript, Tailwind CSS 4, Radix UI primitives (shadcn-style components), React Hook Form + Zod, Zustand, Axios, Recharts, TipTap rich-text editor, `@react-pdf/renderer`, dnd-kit (drag and drop), Framer Motion, Sonner toasts, socket.io-client, dayjs |
| **Backend** | NestJS 11, TypeScript, Passport (JWT, local, Google OAuth), class-validator / class-transformer, Swagger (OpenAPI), `@nestjs/schedule` (cron), `@nestjs/throttler`, Socket.IO gateway, Winston logging, Helmet, compression, cookie-parser |
| **Database** | PostgreSQL via TypeORM (shared schema across both APIs; 40+ migrations maintained in management-api), JSONB audit log columns |
| **Cache / Messaging** | Redis (ioredis: permission cache, force-logout flags, impersonation sessions), RabbitMQ (email queue, payment-webhook events, retry with backoff) |
| **Infra / DevOps** | Docker and Docker Compose, Jenkins pipelines (`Jenkinsfile` per repo, branch-named containers), shell deploy scripts, Node 22 Alpine images |
| **Integrations** | Clover (hosted card checkout, payment details, refunds), Google OAuth 2.0 (parent sign-in), SMTP email via Nodemailer with 35+ HTML email templates (TypeScript template builders), Puppeteer and PDFKit (PDF generation), ExcelJS / json2csv (exports) |

## Documentation in this folder

| File | What it contains |
|---|---|
| [case-study.md](./case-study.md) | Narrative case study: problem, goals, solution, architecture, technical challenges and decisions, learnings, and a placeholder for your role |
| [key-features.md](./key-features.md) | Feature inventory grouped by module, tagged by repo, one bullet per feature (delete the ones you did not build) |
| [architecture.md](./architecture.md) | Repo-by-repo breakdown: folder structure, modules, data model, auth, deployment, integrations, diagrams |
| [assets/](./assets/) | Placeholder folder for screenshots |

## Screenshots to add (`assets/`)

TODO: drop images into `assets/` and reference them from this README or the case study. Suggested shots:

1. Management dashboard home (widgets / stats)
2. Admission order detail page (items, payments, installments, activity log)
3. Fees collection list and payment / refund modal
4. Scholarship review and adjustment modal
5. Roles and permissions editor (role-permission modal)
6. Impersonation banner with the countdown timer
7. Timetable, attendance marking, and grading screens
8. Payroll / salary generation and disbursement
9. Public site: home page, program detail page
10. Parent portal: My Children, order and installment view, child attendance / grades
11. Registration flow (participants + product selector + purchase summary)
12. Audit log view (if a UI is exposed) and a sample generated PDF (invoice / report card)

## Notes on accuracy

All feature and technology claims in these documents were taken from the source code, configuration files, and package manifests of the four repositories. Anything that could not be verified from code (metrics, user counts, dates, business outcomes, your personal contributions) is left as a `TODO:` placeholder.
