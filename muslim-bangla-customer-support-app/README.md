# Muslim Bangla Customer Support App

> A real-time, topic-routed customer support inbox for the Muslim Bangla platform: customers chat from the website or the Android app's WebView, and support agents handle those conversations from a rich, permission-controlled web console.

## Description

Muslim Bangla Customer Support is the helpdesk layer for the Muslim Bangla product family (marriage, jobs, donations, products, zakat, masala and more). A customer opens a single, persistent support chat, optionally pre-selecting a topic, and can message immediately: no form, no waiting screen. Agents see all conversations assigned to their topics in a live inbox, join or leave conversations, reply with rich text, voice notes, images, videos and files, and use canned notes, FAQ answers and tags to work faster.

For agents, the console doubles as a lightweight CRM. An info panel pulls the customer's orders, payments, donations, marriage biodata, job profiles and blood-donation records from the platform's admin API, and agents with the right permission can record an offline donation (including reading a payment screenshot with OCR) and send the receipt, all from inside the chat. Agents can also message customers proactively, deep-link in from the admin panel, and talk to each other in agent-only group chats.

Behaviour is controlled centrally from the existing admin panel: working hours, automatic out-of-hours / no-agent-online bot replies, messaging rules, a site on/off switch, per-agent permissions and topic assignment. Changes propagate to connected clients live through RabbitMQ and Socket.IO. The app is built to run inside the Muslim Bangla Android app's WebView, with a native bridge for sharing and downloading files.

## At a glance

| | |
|---|---|
| **Type** | Real-time customer support / helpdesk chat (customer chat + agent console) |
| **Platforms** | Web (desktop and mobile browsers), embedded in the Muslim Bangla Android app WebView |
| **Languages** | Bangla-first UI (agent and customer strings are largely Bangla); English code and docs |
| **Repos** | `mb-customer-support-frontend` - React + Vite SPA (customer chat and agent console)<br>`mb-customer-support-backend` - NestJS REST + Socket.IO API, Postgres, Redis, RabbitMQ |
| **Status** | TODO: confirm status (the code is actively developed; a production URL is referenced in code, confirm whether it is live) |
| **Live URL** | TODO: add live URL if public |
| **My role** | TODO: see [case-study.md](case-study.md#my-role--contributions) |
| **Timeline / team size** | TODO |

## Tech stack

| Area | Technologies |
|---|---|
| **Frontend** | React 19, TypeScript, Vite 5, Tailwind CSS v4, Zustand, Radix UI primitives, cmdk, Tiptap (rich-text composer), DOMPurify, Socket.IO client, Axios, react-router-dom (agent routes only), sonner, lucide-react, react-phone-number-input |
| **Backend** | NestJS 10, TypeScript, Socket.IO gateway (`@nestjs/websockets`), TypeORM 0.3, class-validator, Multer uploads |
| **Database** | PostgreSQL (TypeORM migrations, jsonb columns), Redis (ioredis) for caching, presence and TTL keys |
| **Infra** | Docker Compose for local Postgres, Redis and RabbitMQ; static serving of uploaded media from the API process; no CI/CD or Dockerfile found in the repos |
| **Media processing** | sharp (image resize/compress, group avatars, OCR pre-processing), ffmpeg via fluent-ffmpeg (video/audio, voice notes re-encoded to mp3) |
| **Integrations** | Muslim Bangla auth service (token validation), mb-admin-api (customer info, donations, agents/topics/settings writes), RabbitMQ `support` exchange + notification queue (push/FCM and others), Tesseract.js OCR (English + Bangla), Android WebView `AndroidNative` bridge, link-preview fetching |

## Documentation

- [case-study.md](case-study.md) - narrative case study: problem, solution, architecture, decisions, outcome
- [key-features.md](key-features.md) - feature inventory grouped by module, tagged by repo (edit down to what you built)
- [architecture.md](architecture.md) - repo-by-repo breakdown, data model, auth, deployment, integrations

## Screenshots (drop into `assets/`)

TODO: add screenshots to the [assets/](assets/) folder. Suggested set:

1. Customer chat view (empty state with FAQ quick questions, and an active conversation) on mobile
2. Agent console: conversation list + chat window (desktop)
3. Agent info panel: customer orders / payments / donations / marriage sections
4. Add-donation dialog with the screenshot OCR and donation check tabs
5. Agent group chat and group info panel (members, roles, permissions)
6. Image editor (crop / draw / text) and media gallery
7. Settings page (colour themes, chat wallpaper, notes, FAQ answers, tags)
8. Chat inside the Android app WebView (header with app logo)
