# Key Features

Each bullet is one feature verified in code. Delete the bullets you did not work on, and keep the ones you want to claim.

Tags: `[backend]` = `mb-call-back` (NestJS), `[frontend]` = `mb-call-react-front` (React + Vite). Features that need both sides are tagged `[frontend][backend]`.

---

## 1. Calling and WebRTC signalling

- **One-tap voice call** `[frontend][backend]`: A customer starts a call with a single button, and the backend allocates a call ID and routes the request.
- **Socket.IO signalling relay** `[backend]`: SDP offers/answers and ICE candidates are relayed between the two parties through a per-call room, and the audio itself flows peer to peer.
- **Browser WebRTC audio engine** `[frontend]`: A dedicated store manages the `RTCPeerConnection`, local microphone stream, remote audio element and offer/answer/ICE handling.
- **STUN/TURN relay configuration** `[frontend]`: The ICE server list uses STUN plus TURN over UDP and TCP (including port 443) to connect users behind restrictive networks.
- **Fixed caller/receiver roles** `[frontend][backend]`: The customer always creates the SDP offer and the Mufti always answers, which avoids offer collisions and keeps the web and Android clients consistent.
- **Incoming call dialog with accept/reject** `[frontend]`: A Mufti sees a modal for each incoming call and can accept or decline it.
- **Call cancel and leave** `[frontend][backend]`: A caller can cancel before answer, either party can leave an active call, and the backend notifies the other side and cleans up.
- **Mute / unmute relay** `[backend]`: Mute and unmute events are relayed to the peer so clients can show a muted indicator (the web client does not yet consume them, per the parity docs).
- **Ringing timeout and no-answer handling** `[backend]`: Each ring lasts 30 seconds, after which the Mufti's dialog is closed with a `call-timeout` event and the call moves on.
- **Stale-call cleanup and already-in-call guard** `[backend]`: A new request is rejected with `already-in-call` when the user is genuinely in a call, and leftover Redis call data is cleared when it is stale.

## 2. Call state machine and reconnection

- **Call lifecycle state machine** `[backend]`: Calls move through pending, active, reconnecting, completed, cancelled and missed, with state kept in Redis and persisted to Postgres.
- **60-second reconnection window** `[backend]`: When one side disconnects, the call is parked in a reconnecting state and ended with a `reconnection_timeout` reason only if the user does not return in time.
- **Advanced reconnection context** `[backend]`: On return the server tells the client whether it was in a call, whether WebRTC needs a reset, who should re-initiate the handshake, the call duration and the attempt count.
- **Reconnect and call-status queries** `[backend]`: Clients can ask for their current call status and rejoin an existing call after a refresh or network change.
- **ICE-restart renegotiation** `[frontend]`: The web client can create an ICE-restart offer to recover audio after a connection drop.
- **Poor-connection monitor** `[frontend]`: A watcher triggers recovery after roughly five seconds of degraded connectivity and shows a "recovering" status with attempt counts.
- **Call event batching** `[backend]`: Call events and quality metrics (drops, reconnection attempts) are batched and synced to the database every five seconds.
- **Call quality feedback** `[backend]`: Clients can submit a rating, comment and issue list for a call, which is recorded as an event on the call's Redis data and flushed with the batched sync.

## 3. Call routing and distribution

- **Priority-based Mufti selection** `[backend]`: Among online and available Muftis, the one with the lowest score wins, with a random tie-break.
- **Rejection penalty** `[backend]`: A Mufti who rejected a given customer today receives a heavy score penalty for that customer, so others are tried first.
- **Daily fairness counters** `[backend]`: Unique completed customers per Mufti are tracked in Redis with 24-hour expiry to balance workload.
- **Rotation on reject or no-answer** `[backend]`: A rejected or unanswered call moves to the next untried Mufti, and the tried list is tracked per call.
- **Offline wake-up and wait** `[backend]`: When no Mufti is online, available Muftis receive an FCM push and the caller waits up to 30 seconds for one to connect.
- **Targeted "old call" routing** `[frontend][backend]`: A follow-up call can specify a recipient and goes to that Mufti directly.
- **Select a specific Mufti** `[frontend][backend]`: The customer can pick from the list of online Muftis, using a public online-users endpoint.
- **Missed-call creation** `[backend]`: When nobody can take the call, a missed-call record is created with the reason and the list of Muftis tried.
- **"No Mufti available" prompt** `[frontend]`: The customer is asked whether to try another Mufti or stop, and the choice restarts or resets the call.
- **Distribution tuning API** `[backend]`: Admin endpoints expose config, scenario presets, stats, score breakdown, rejection checks, and `simulate`/`compare` runs of the algorithm.

## 4. Mufti availability and scheduling

- **Master availability switch** `[frontend][backend]`: A Mufti toggles "I am taking calls" (available, unavailable or do-not-disturb), which also registers their FCM token.
- **Weekly schedule in Bangladesh time** `[frontend][backend]`: Muftis define up to 10 slots per day, each with its own enable flag, evaluated in Asia/Dhaka regardless of device timezone.
- **Schedule validation module** `[backend]`: A pure module validates time format, ordering, overlaps, per-day limits and unknown fields, and returns structured error codes for the whole document.
- **Effective-availability resolver** `[backend]`: The master switch overrides slots, and with it on, a day with enabled slots is available only inside them while a day with none is open all day.
- **Next-change calculation** `[backend]`: The API reports when availability will next flip, so the UI can show what happens next.
- **Availability scheduler** `[backend]`: A 20-second background tick syncs scheduled changes into Redis, protected by a distributed lock so multiple instances do not collide.
- **Schedule editor page** `[frontend]`: A dedicated `/availability` page lets a Mufti add, edit, disable and remove slots, with client-side validation mirroring the server.
- **Unit-tested availability logic** `[backend]`: The scheduling module ships with a Jest spec covering its rules.
- **"Mufti is now online" notifications** `[backend]`: Customers waiting on a specific Mufti are notified when that Mufti switches on or a slot starts.

## 5. Presence and status

- **Redis presence tracking** `[backend]`: Each user's online, busy, ringing or offline status is stored in Redis with socket IDs and admin flag.
- **Live admin-count broadcast** `[frontend][backend]`: Clients receive `admin-status-update` events with online counts, shown as a status badge.
- **Status health-check job** `[backend]`: A 30-second job scans presence keys and repairs inconsistent or stale entries.
- **Redis adapter for Socket.IO** `[backend]`: Events and rooms are shared across instances, with retry and a single-instance fallback if Redis is unavailable.
- **Connection status UI** `[frontend]`: The header shows connection state and call-state-dependent status text and colours.

## 6. Credits, billing and settlement

- **Token validation against platform API** `[backend]`: Sockets and REST calls authenticate by forwarding the bearer token to the platform's auth service.
- **Credit-type derivation** `[backend]`: The backend classifies callers as admin, payment, donation or not-available based on balances and expiry.
- **Not-enough-credit flow** `[frontend][backend]`: Callers without usable credit are told so and shown a dedicated view instead of a call button.
- **Audio credits** `[backend]`: A one-use credit granted after a Mufti answers a question is consumed first-in-first-out and takes priority over normal routing.
- **Credit-locked routing** `[backend]`: An audio credit tied to one Mufti rings only that Mufti, with no rotation, and gives up cleanly when they are unavailable.
- **Recipient-offline notices** `[frontend]`: The customer sees the assigned Mufti's name or signature, and a clear alert when that Mufti is offline.
- **Complete / await a call** `[frontend][backend]`: A Mufti marks a finished call as completed (which charges credit or marks the audio credit used) or awaiting.

## 7. Mufti-to-Mufti assignment

- **Assign customer to another Mufti** `[frontend][backend]`: From the call-history row, a Mufti hands the customer to a colleague, which settles the call and grants a credit routed only to the target.
- **Mufti directory endpoint** `[backend]`: `GET mufti/list` returns eligible Muftis (role-based, cached for a minute) with an online indicator, excluding the requester.
- **Assignment dialog** `[frontend]`: A confirmation dialog with a Mufti picker and online/offline badges (Bengali UI).
- **Safe error semantics** `[backend]`: The assignment returns distinct 400/403/404/409/502 errors and leaves state unchanged when charging fails, so retries are safe.
- **Customer live update on assignment** `[frontend][backend]`: The customer receives an `audio-credit-updated` socket event and an FCM push with the new assignment.

## 8. Call history and reviews

- **Persistent call history** `[backend]`: Each call stores participants, type, status, admin status, duration, reconnection attempts, end reason and metadata in Postgres.
- **Call history REST API** `[backend]`: Endpoints list by customer, by provider, by id, with statistics and an analytics summary.
- **Mufti pending-calls list** `[frontend][backend]`: A Mufti sees calls awaiting review with complete, await and assign actions.
- **Customer last-call card** `[frontend]`: The customer sees the last call's state, including a "processing" card while the Mufti has not yet settled it.
- **Call-complete and awaiting handshake** `[frontend][backend]`: Status changes propagate live via `call-history` broadcasts to both parties.

## 9. Notifications

- **FCM v1 incoming-call push** `[backend]`: A data push wakes the Mufti's device so the app can ring even when closed.
- **Missed-call alerts to Muftis** `[backend]`: All other Muftis are notified when a call could not be answered, with a message varying by situation.
- **Assignment push to customer** `[backend]`: The customer is told a Mufti has been assigned to them.
- **Multi-device notification helpers** `[backend]`: Notifications can be sent to a single token, many tokens, or all devices of a customer.
- **In-app missed-call toast** `[frontend]`: Missed-call notices on the Mufti side surface as dismissible toasts.

## 10. Customer experience (web)

- **Periodic instruction dialog** `[frontend]`: A Bengali guidance dialog (keep it short, one question, end the call when answered) shows to customers at most once an hour, tracked in local storage.
- **Media permission checks** `[frontend]`: The client detects microphone and speaker readiness and displays their status before a call.
- **Earpiece / loudspeaker switching** `[frontend]`: Mobile users can switch audio output, using device selection where the browser supports `setSinkId`.
- **Remote audio autoplay handling** `[frontend]`: An "enable audio" control handles browser autoplay restrictions.
- **Mobile device detection** `[frontend]`: The UI adapts its controls based on device type.
- **Bengali-language UI** `[frontend]`: User-facing messages, dialogs and errors are written in Bangla.
- **Token-based entry and error page** `[frontend]`: The app reads a token from the URL, stores a session cookie, and redirects to an error page when authentication is missing.
- **Customer-support deep link helper** `[frontend]`: A utility builds a support-site URL with topic, token and parsed user-agent context.

## 11. Platform, quality and operations

- **Whitelist validation and global exception filter** `[backend]`: Requests are validated with `class-validator` (unknown fields rejected) and errors are normalised.
- **Structured logging** `[backend]`: Winston logging with daily-rotated files and an HTTP logger middleware.
- **Multi-database access** `[backend]`: TypeORM connects to the call DB plus read-only mappings of the admin and customer databases for roles and customer data.
- **Production SQL migrations** `[backend]`: Hand-written, re-runnable scripts document schema changes (availability column, assignment columns, enum value) because production does not auto-sync.
- **PM2 deployment config** `[backend]`: An ecosystem file defines the process and port arguments.
- **Global state with Zustand** `[frontend]`: Auth, socket, WebRTC and call logic are isolated in four stores.
- **Design system** `[frontend]`: Tailwind CSS 4 with shadcn/Radix UI components, Sonner toasts and a Geist font.

## 12. Cross-platform integration docs

- **Android receiver flow documentation** `[frontend]`: A roughly 1,300-line description of the native receiver (service bootstrap, FCM wake-up, notifications, accept/reject, WebRTC, reconnection, known gaps).
- **Web vs Android contract comparison** `[frontend]`: A point-by-point check of backend, web and Android behaviour with file and line references.
- **Android availability and assignment contracts** `[frontend]`: Implementation-ready API specs for the schedule and hand-over features.
- **Customer-session design (planned)** `[backend]`: A design plan and frontend guide for a 24-hour credit session with group chat and designated receivers. This is design only, no matching module exists in `src/`.
- **Standalone call-service prompts (design)** `[backend]`: Two prompt documents describe extracting signalling into a reusable multi-tenant microservice. This is design only; the running code keeps signalling in-process.
- **Peer chat module (dormant)** `[backend]`: REST endpoints and entities for two-party chat exist, but the socket chat handler is currently commented out in the gateway.
