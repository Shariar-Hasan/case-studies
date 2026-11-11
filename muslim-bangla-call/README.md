# Muslim Bangla Call

> Real-time voice calling that connects Bangla-speaking users with qualified Muftis for live Islamic Q&A.

## Description

Muslim Bangla Call is the voice-call service of the Muslim Bangla platform. A signed-in user (the *customer*) opens a web page, taps one button, and is routed to an available Mufti (the *receiver*). The two speak over a peer-to-peer WebRTC audio connection. The backend handles signalling, call routing, presence, call history, and the credit and billing hand-off to the wider Muslim Bangla platform.

The product is built around the realities of a human-staffed helpline. Muftis set a master "I am taking calls" switch and an optional weekly schedule in Bangladesh time. The server decides who is actually reachable and spreads calls fairly using a priority score. If no Mufti is online, available Muftis are woken with push notifications and the caller waits. A Mufti can also hand a customer over to a colleague, and the customer is then routed only to that colleague on their next call.

The same backend serves two clients: a React web app (this case study's frontend, used by customers and by Muftis) and a native Android receiver app for Muftis, which lives in a separate repository. A set of integration documents in the frontend repo keeps the web and Android contracts aligned.

## At a glance

| | |
|---|---|
| **Type** | Real-time audio-calling service (signalling backend and web client) for a Q&A helpline |
| **Platforms** | Web (React SPA, embedded in the Muslim Bangla ecosystem); Android receiver app for Muftis (separate repo, documented here only through integration docs) |
| **Roles** | Customer (caller), Mufti / admin (receiver) |
| **Repos** | `mb-call-back`: NestJS signalling and REST backend. `mb-call-react-front`: React + Vite web client for customers and Muftis |
| **Status** | TODO: owner to confirm (in production / maintained / archived). Source shows production deployment config (PM2, migration SQL notes) and active development through late 2026. |
| **Live URL** | TODO: add live URL |
| **Timeline** | TODO: add dates |

## Tech stack

| Layer | Technology |
|---|---|
| **Frontend** | React 19, TypeScript, Vite, Zustand (state), React Router, Tailwind CSS 4, shadcn/ui + Radix UI, Sonner (toasts), Axios, lucide-react |
| **Backend** | NestJS 11, TypeScript, class-validator / class-transformer, Winston (daily-rotating logs), Jest |
| **Database** | PostgreSQL via TypeORM 0.3, with three connections: the call DB (call history, audio credits, preferences, chat), plus the admin and customer DBs for role and customer lookups |
| **Realtime** | Socket.IO 4 (client and server), `@socket.io/redis-adapter` for multi-instance fan-out, WebRTC (browser `RTCPeerConnection`, audio only), STUN/TURN relay |
| **Cache / state** | Redis (presence, call state, pending calls, distribution counters, scheduler lock) via `redis` and `ioredis` |
| **Infra** | PM2 (`ecosystem.config.js`), Node.js, environment-based config. Hosting details TODO: owner to confirm |
| **Integrations** | Firebase Cloud Messaging (push via `firebase-admin` / FCM v1), external Muslim Bangla auth API (token validation and credit charging), Muslim Bangla customer-support site (deep link), Android native receiver app |

## Contents

- [case-study.md](./case-study.md): the full narrative (problem, solution, architecture, challenges, outcome)
- [key-features.md](./key-features.md): feature inventory tagged by repo, to prune down to your own contributions
- [architecture.md](./architecture.md): repo-by-repo breakdown, data model, signalling and call flow diagrams
- [assets/](./assets/): screenshots and diagrams (see below)

## Screenshots to add (`assets/`)

TODO: drop images in `assets/` and reference them from here or from `case-study.md`:

1. Customer idle screen with the call button and Mufti selector
2. Customer in-call screen (timer, mute, speaker options)
3. Mufti incoming-call dialog
4. Mufti dashboard: availability toggle plus pending call history list
5. Weekly availability schedule editor (`/availability`)
6. "Assign to another Mufti" dialog
7. Reconnecting / poor-connection banner
8. Optional: architecture or sequence diagram exported from `architecture.md`
