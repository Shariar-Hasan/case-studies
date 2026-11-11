# Key Features

Every bullet below was verified against code in the two repositories. Tags show where the work lives:

- `[frontend]` = `mb-akd` (Next.js web app)
- `[api]` = `mb-akd-api` (Node.js/Express REST API)

**How to use this file:** delete the bullets for features you did not work on, then lift the rest into the "My Role & Contributions" section of [case-study.md](case-study.md).

---

## 1. Authentication & Session

- **Multi-method login** `[frontend]` - Login dialog and dedicated login page offering Google sign-in (OAuth handled by the central auth service), email/OTP login, and a verify-OTP step, also available as an intercepted modal route (`@modal/(login)`).
- **Token refresh with single-flight mutex** `[frontend]` - `AuthService` reads the token from a cookie (server and client) and refreshes it through the login API with one shared in-flight refresh so parallel requests do not race.
- **Session-expired handling** `[frontend]` - Dedicated error type, banner/dialog, and logout side-effects that clear user, biodata, and form state when a session is no longer valid.
- **Protected-route proxy** `[frontend]` - Next.js `proxy.ts` guards `/marriage-form`, `/dashboard`, and `/settings`, persists `app_id`/`type` query params into cookies, and redirects signed-in owners to the opposite-gender search by default.
- **Customer token verification (dev vs. prod)** `[api]` - Customer routes validate the Bearer token against an external auth service in non-production and verify the JWT locally in production; admin routes use a separate admin-auth check.
- **Encrypted client storage** `[frontend]` - Wrapper that encrypts both keys and values before writing to `localStorage`.

## 2. Biodata (Marriage Profile) Creation & Management

- **Nine-step biodata wizard** `[frontend]` - Guided multi-step form (personal, educational, professional, location, family, religious, partner expectations, profile image, marriage-proposal details) with progress indicator, per-step Zod validation, and scroll-to-error.
- **Create / edit / delete biodata** `[api]` - `POST`, `PUT`, `GET`, `DELETE /marriage-profile` for the owner's biodata, backed by a Sequelize model with 100+ fields and soft delete (`paranoid`).
- **Deletion with recorded reason** `[frontend]` `[api]` - Owner must give a reason when deleting; the reason is stored on the record and the delete flow also clears related marks and pending connections.
- **Rich Bangladeshi location model** `[frontend]` `[api]` - Division/district/upazila hierarchy stored as DB models and static data, plus a country picker and country-aware address handling for expatriate (প্রবাসী) members.
- **Avatar selection** `[frontend]` `[api]` - Members pick from a gallery of illustrated avatars filtered by gender, complexion, clothing type, and colour instead of uploading photos; avatars are served from a CDN.
- **Biodata correction form** `[frontend]` - Separate form that asks existing members to fill newly required fields, with visible-field logic driven by what is missing.
- **Phone verification request** `[frontend]` `[api]` - Members with an unverified number can send a verification request that the API forwards as a message to the customer-support service.
- **Biodata preview mode** `[frontend]` - Owners can preview their own biodata as others see it, with preview/exit-preview controls.
- **Profile card customisation** `[frontend]` - Settings option to choose between the default card and a custom short "about me" text shown on search cards.
- **Deactivate / reactivate biodata** `[frontend]` `[api]` - Owners can hide their biodata (`PATCH /marriage-profile/inactive`, `/activate`); the UI enforces a 7-day wait before reactivation and the API records an `inactivated_reason` (self, biodata_fee, admin).
- **Biodata-ID shortcuts and sharing** `[frontend]` - Share menu and share modal, copy-to-clipboard of the biodata ID, and generated SEO meta descriptions per biodata.

## 3. Search & Discovery

- **Public biodata directory with rich filters** `[api]` - `GET /marriage-profiles` supports gender, age, height, complexion, marital status, current/permanent address and district, education medium/syllabus, occupation, family status, beard/dress per Sunnah, hafiz, verified number, payment-verified, fee-paid, and second-wife-agreement filters, with pagination.
- **Filter panel with tabs and chips** `[frontend]` - Desktop filter panel and mobile filter sheet, tabs for filter search, advanced search, and biodata-ID search, plus removable filter chips.
- **Infinite-scroll results with client cache** `[frontend]` - Search page with infinite scroll, a persisted Zustand search store, data-freshness tracking, and scroll-to-top.
- **Bride/groom tabs and featured section** `[frontend]` - Toggle between brides and grooms (or all), plus a featured biodata section.
- **"According to my preferences" filter** `[frontend]` `[api]` - One-tap chip that fills the filter panel from the viewer's own stated partner expectations (age, height, status, complexion, education, occupation, location, etc.) and clears when another chip is chosen.
- **Reverse expectation match ("do I meet their expectations?")** `[api]` `[frontend]` - The API computes the match percentage in SQL per candidate (including education-ladder logic across general, madrasha, and English mediums) so it can sort the whole result set; the client mirrors the same rules for the detail-page match table and badge.
- **Viewer-aware result enrichment** `[api]` - Each listed biodata is annotated with the viewer's connection state and favourite/dislike mark so cards render correct actions without extra calls.
- **Own-biodata search handling** `[frontend]` - Default gender filter, cookie-synced viewer gender/biodata ID, and a notice when the viewer's own fee is still due.
- **Public biodata detail page** `[api]` `[frontend]` - `GET /marriage-profile/:id` returns the limited public view for anonymous users and the fuller view once a connection is accepted; the page is split into personal, educational, professional, locational, family, religious, expectation, and proposal sections.
- **Profile views and last-visit tracking** `[api]` - View counts and last-visit timestamps accumulate in Redis during the day and are flushed to PostgreSQL by a midnight job under a distributed lock.

## 4. Connections (Request & Consent Flow)

- **Send connection request** `[api]` `[frontend]` - Validates the sender has an active, phone-verified biodata, blocks duplicate/cross requests and previously rejected pairs, enforces one pending request at a time, and consumes a free or premium connection credit inside a DB transaction.
- **Accept / reject / cancel / discontinue** `[api]` `[frontend]` - Full lifecycle endpoints plus connection-button states in the UI, a confirmation modal, a safety notice, and a previous-pending-request dialog.
- **Connection dashboard tabs** `[frontend]` - Received, sent, connected, rejected, got-rejected, and discontinued lists with dedicated cards.
- **Free and premium connection credits** `[api]` - New customers get a free package (4 requests, valid one month); paid premium packages are consumed afterwards; limits are exposed via `GET /connection/request-limits` and shown in the purchase-plan UI.
- **Auto-expiry and refunds (cron)** `[api]` - 1 AM job auto-rejects premium, free, and call-support requests left unanswered for 7 days, refunds premium credits, and logs the refund, guarded by a Redis distributed lock so multiple PM2 instances do not double-process.
- **Call-support grace period** `[api]` - Requests with a freshly purchased call-support service are never auto-rejected within 72 hours of purchase.
- **Connection timeline** `[api]` `[frontend]` - Append-only `connection_logs` table filled by a Postgres trigger (so every writing service is captured) and read by `GET /connection/timeline/:biodata_id` to show a request history between two members.
- **Connection status resolver** `[api]` - Shared `ConnectionStatusService` collapses sender/receiver rows into one status shape so list cards and detail page never disagree.
- **Responsiveness score** `[api]` `[frontend]` - Each biodata gets a score (100 minus the share of requests auto-rejected by the system over the last month), updated on rejection events and displayed as a badge.
- **In-app, push, and SMS notifications on requests** `[api]` - Sends a notification-center entry and FCM push via RabbitMQ when a request is sent or accepted, plus an SMS to the receiver for premium requests.

## 5. Likes, Dislikes & Reports

- **Favourite (পছন্দ) and dislike (অপছন্দ) marks** `[api]` `[frontend]` - A single `marked_biodatas` table keyed by the viewer's biodata with a `status` of favourite or dislike, mutually exclusive via a partial unique index; UI buttons and a favourites tab in the dashboard.
- **Favourites to marked-biodatas data migration** `[api]` - Secret-key-guarded, idempotent, batched background migration endpoints (start and status) with documented SQL phases, verification queries, and rollback.
- **Report a biodata** `[api]` `[frontend]` - Members can report a biodata with a reason and description (one report per member per biodata); report counts and details are shown on the biodata page.

## 6. Payments, Packages & Monetisation

- **One-time biodata fee, tiered by family status** `[api]` `[frontend]` - Income-tier pricing (five tiers) plus a flat expatriate rate; new biodatas start inactive until paid, and paying grants premium connections and marks the phone verified. Pricing modal and payment modal in the UI.
- **Fee change / upgrade pricing** `[api]` - `POST /biodata-fee/change-intent` computes the difference for upgrades (and full price for downgrades) when a member's tier changes.
- **Grandfathering via SQL backfill** `[api]` - Existing members who bought any marriage product are marked as fee-exempt through a documented one-time backfill.
- **Deadline enforcement cron** `[api]` - 1 AM job deactivates overdue unpaid biodatas after a configured deadline, running the same cleanup as self-deactivation (closing pending connections, notifying counterparts, refunding eligible premium credits).
- **Fee due banner, modal, and rules** `[frontend]` - Landing-page banner, due modal, rules modal, a standalone `/biodata-fee` page that rehydrates state and redirects away from terminal states to avoid double charging.
- **Multi-gateway payment launcher** `[frontend]` - Popup (desktop) or full-page (iOS/macOS) redirect flow for SSLCommerz, bKash, and Nagad, with a postMessage/channel listener, pending-payment persistence, and success/fail/cancel landing pages.
- **Region-aware payment methods** `[frontend]` - Method picker adapts by region and offers offline options (bank transfer and international wallets) that open an account-details modal.
- **Connection package purchase** `[frontend]` - Plan-purchase UI for buying additional connection requests.
- **Payment-verified badge and promotion** `[frontend]` `[api]` - "Payment verified" flag stored on the biodata, filterable in search, and promoted through a dedicated button and tooltip.

## 7. Call / Message Support Service

- **Individual contact support purchase** `[api]` `[frontend]` - A member who has sent a request can buy a per-connection assisted-contact service, where an agent contacts the other party on their behalf; the API creates the pending purchase record and the UI shows terms and a refund policy.
- **Connection-to-purchase linking** `[api]` - Explicit `connection_id` on the purchase and `call_support_id` on the connection, with a documented SQL migration and backfill.
- **Support involvement tracking** `[api]` - `support_status` (no_response, waiting, completed, cancelled, refunded, not_involved) with logic that marks a connection `not_involved` when the two members resolved it themselves through chat.
- **Chat lifecycle sync with the chat service** `[api]` - When a connection leaves pending (accepted, rejected, cancelled, expired), the API closes the linked chat directly in the chat service's database with a context-specific Bangla reason, and never lets a chat failure block the accept/reject.
- **Floating chat widget on biodata page** `[frontend]` - Step-by-step guidance (create biodata, send request, buy service), polling for the chat to appear after payment, mobile full-screen sheet, and a draggable floating-widget stack.
- **Unread chat indicator** `[api]` `[frontend]` - `GET /chat/unread` reads unread counts per chat from the chat database; a global button refreshes on navigation, tab focus, and a timer.
- **Customer-support floating button** `[frontend]` - Persistent support entry point and a dedicated support-service marketing/consent page.

## 8. Notifications & Realtime

- **Notification center page and toasts** `[frontend]` - Notifications list page with images, header bell with unseen count, and toast display for realtime events.
- **Socket.IO realtime connection** `[frontend]` - Authenticated socket connection tracked in a Zustand store, with HTTP initial unseen count so the badge is correct before the socket connects.
- **Push notification pipeline** `[api]` - Notifications are normalised and published to a RabbitMQ queue for the notification service; Firebase Admin is configured for both Android and iOS.
- **Telegram operational logging** `[api]` - A RabbitMQ queue consumer forwards operational messages to Telegram.

## 9. Statistics & Social Proof

- **Biodata statistics dashboard** `[api]` `[frontend]` - SQL-aggregated breakdowns by age band, marital status, profession, education, family status, division, district, and country (with small groups folded into "others" for privacy), cached in Redis for an hour and charted with Recharts.
- **Registration trend and practice rings** `[frontend]` - Trend chart and religious-practice ring visualisations, animated count-up KPI cards.
- **Successful marriages** `[api]` `[frontend]` - Public list, per-marriage details, and aggregate stats of couples who married through the platform, rendered as a timeline with tabs.
- **Reward claim flow** `[api]` `[frontend]` - Couples submit groom/bride biodata IDs and marriage date; status moves through draft, under_review, verified, or fraud, and verified claims expose a coupon code on a reward page.

## 10. Content, UX & Platform Concerns

- **Bangla-first interface** `[frontend]` - Bangla copy throughout, Bangla numeral conversion, custom Bangla/Arabic fonts (Kalpurush, Uthmanic, Scheherazade, Tajawal, Noorehuda), and Arabic/English/Bangla mixed-text components for duas and phrases.
- **Instructions and guidance pages** `[frontend]` - Accordion-based help with fifteen instruction topics and a "Bibah Fozilot" content page that lists common marriage-custom practices to avoid.
- **Theming** `[frontend]` - Light, dark, and system modes with an optional `?theme=` URL param that is bootstrapped and synced for embedding in a host app.
- **Maintenance mode** `[frontend]` - `DEVELOPMENT_IN_PROGRESS` env switch redirects every page to a no-cache 307 maintenance page and strips API-backed chrome from the root layout; used during the favourites migration window.
- **Targeted advertisement carousel** `[frontend]` - Notice/ad banners filtered by country inclusion, device type, and screen route.
- **SEO and analytics** `[frontend]` - Central metadata builder (Open Graph and titles) and Google Analytics tag.
- **Responsive layout and loading states** `[frontend]` - Responsive modals/drawers (Vaul, Radix), skeletons, initial and navigation loading screens, and error boundaries.

## 11. Backend Platform & Operations

- **Layered Express architecture** `[api]` - Routes, controllers, services, and Sequelize models, with `makeNull` and `trim` input middleware applied globally.
- **Multi-database Sequelize setup** `[api]` - Separate connections for the marriage ("shabaka"), customer, Quran/QA legacy, and chat databases.
- **Redis caching and distributed locks** `[api]` - Cache helpers, atomic counters, and lock acquire/wait/release primitives used by cron jobs.
- **Scheduled jobs** `[api]` - Midnight flush of view counts and last visits; 1 AM refunds, auto-rejections, and biodata-fee deactivation.
- **Structured logging and slow-request warnings** `[api]` - Winston with daily rotation, express-winston request logging with level by status code, and warnings for requests slower than two seconds.
- **PM2 cluster deployment config** `[api]` - Eight-instance cluster definition with auto-restart.
- **Unit tests for deactivation planning** `[api]` - Node test-runner tests for the pure `planClosure` / `planDeactivation` decision functions.
- **Design docs and runbooks** `[api]` - Per-feature docs with SQL, backfills, deployment order, and rollback, plus a written spec and implementation plan for fee enforcement and scheduled reminders.
