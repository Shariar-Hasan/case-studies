# Muslim Bangla Chat

> A realtime chat platform for the Muslim Bangla ecosystem, with rule-driven conversation types, including an anonymous, quota-limited "marriage private chat" that embeds into the Muslim Bangla marriage site.

## Description

Muslim Bangla Chat is a realtime messaging web app built for the Muslim Bangla product family. It signs users in through the existing Muslim Bangla single sign-on (SSO), so there is no separate registration. Users can chat one-to-one, create group conversations, share invite links, send images, video, audio and documents, and see typing indicators, delivery state, and read receipts. The interface is in English with Bengali user-facing messages and errors.

The platform is built as a generic chat engine in which each conversation type (`PRIVATE`, `GROUP`, `MARRIAGE_PRIVATE`, `SUPPORT`) is simply a set of default settings. Those settings cover message length, whether attachments, replies, edits and deletes are allowed, whether phone numbers, emails or links may be shared, and how many messages each participant may send. The most distinctive type is the **marriage private chat**: an anonymous, strictly alternating conversation between two people connected through a biodata request on the Muslim Bangla marriage site. It has a 50-character message limit, no attachments, no contact-information sharing, and a lifetime cap of three messages per person, after which the conversation closes automatically.

The chat is delivered as a React single-page app plus a NestJS API. The same app also runs inside an `<iframe>` as a floating chat widget on the marriage site, where it receives its session token through `postMessage`. Moderation is handled by a database-driven role and permission system, with moderators able to oversee conversations platform-wide by type. Offline users are notified through a RabbitMQ-backed notification pipeline shared with other Muslim Bangla services.

## At a glance

| | |
|---|---|
| **Type** | Realtime chat / messaging platform (web app + API), embeddable as an iframe widget |
| **Platforms** | Web (responsive SPA, runs standalone and embedded in another site's iframe) |
| **Repos** | `mb-chat-app-site`: React + Vite frontend (chat UI, admin screens, iframe embedding). `mb-chat-app-api`: NestJS backend (REST + Socket.IO gateway, persistence, permissions, notifications, marriage-chat integration) |
| **Status** | Actively developed; commit history spans roughly Jan to Oct 2026. TODO: confirm production/launch status |
| **Live URL** | TODO: add public URL if shareable |
| **My role** | TODO: see [case-study.md](./case-study.md#my-role--contributions) |

## Tech stack

| Area | Technologies |
|---|---|
| **Frontend** | React 19, TypeScript, Vite, React Router 7, TanStack Query 5, Zustand, Tailwind CSS 4, Radix UI primitives, TipTap rich-text composer, Sonner toasts, lucide-react icons, axios, socket.io-client |
| **Backend** | NestJS 11, TypeScript, class-validator / class-transformer, `@nestjs/event-emitter` (domain events), Multer (uploads), Winston with daily-rotating logs, axios |
| **Database** | PostgreSQL via TypeORM (migrations, snake-case naming strategy, JSONB settings, entity subscribers) |
| **Realtime** | Socket.IO 4 (`@nestjs/websockets`) with the Redis adapter (`@socket.io/redis-adapter`) for multi-instance fan-out; Redis-backed presence |
| **Cache / Infra** | Redis (ioredis) for token cache, presence and link-preview cache; RabbitMQ (amqplib) producer for notifications; local-disk uploads |
| **Integrations** | Muslim Bangla SSO / identity service (bearer-token validation, customer lookup), shared notification consumer (push / socket / email channels), the Muslim Bangla marriage site and its services (embedded chat, shared database contract) |
| **Tooling** | ESLint + Prettier + Jest (API), oxlint (site), Tailwind Vite plugin |

## Documentation

- [case-study.md](./case-study.md): the narrative case study (problem, solution, technical challenges, learnings, role placeholder)
- [key-features.md](./key-features.md): features grouped by module, tagged by repo; trim to what you worked on
- [architecture.md](./architecture.md): repo-by-repo structure, data model, realtime, auth, deployment, integrations

## Screenshots

The `assets/` folder is a placeholder. Suggested screenshots to drop in:

1. Conversation list + open conversation (desktop, light and dark)
2. Message thread showing reply quote, attachment, link preview, and read ticks
3. Marriage private chat embedded in the marriage site (floating window) and the rules dialog
4. Conversation settings panel (toggles for replies, edits, link/phone/email sharing)
5. Role management page with the permission picker
6. Admin users page and "Manage roles" modal
7. Personalization settings (theme, text size, chat theme colours, chat backgrounds)
8. Mobile layout of the list and thread views

TODO: add screenshots and reference them here.
