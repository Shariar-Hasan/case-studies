# Key Features

Each bullet is one feature verified in the code or in the repos' own docs. Delete the bullets you did not work on and keep the rest for the portfolio.

Tags: `[frontend]` = `mb-customer-support-frontend`, `[backend]` = `mb-customer-support-backend`, `[both]` = spans both repos. `[external]` marks a dependency on another Muslim Bangla service (admin API / admin panel) that this app integrates with but that lives outside these two repos.

---

## 1. Authentication, Identity & Roles

- **Shared-login SSO** `[both]` - Re-uses the Muslim Bangla login: the token is read from a shared `currentUser` cookie (or a `?token=` hand-off) and validated against the platform's auth service; no separate sign-up.
- **Token hand-off from the app and site** `[frontend]` - A `createSupportUrl()` helper builds support links carrying token, topic and device/referrer info; a URL token replaces a stale cookie when it comes from the mobile-app hand-off.
- **Redis-cached token validation** `[backend]` - Validated users are cached in Redis for 24 hours so most requests skip the auth-service round trip.
- **Automatic user provisioning** `[backend]` - First-time users are upserted from the auth payload as `CUSTOMER` (or `AGENT` for super admins); agent status is thereafter managed from the admin panel.
- **Role guards for HTTP and WebSocket** `[backend]` - Separate HTTP and WS auth guards, a roles guard and a permissions guard, plus a `@CurrentUser()` decorator, keep authorisation consistent across REST and sockets.
- **Granular per-agent permissions** `[both]` - A jsonb permission set (send, attach, reply, emoji, delete, share, forward, block, assign agent, change topic, manage notes/FAQ/tags, view log, view deleted messages, message a customer, and more) is enforced by the server and used by the UI to hide forbidden actions.
- **Live permission updates** `[both]` - When an admin changes an agent's permissions, a `user:permissions_updated` socket event updates the open console without a reload.
- **Per-section customer-info permissions** `[both]` - Admins choose which customer-info sections (basic, numbers, marriage, payments, donations, masala, orders, jobs, blood) each agent may open.
- **Expired-session handling** `[frontend]` - Any 401 clears the cookie and store and shows a "sign in" screen linking back to the platform login with a return URL.

## 2. Customer Chat Experience

- **Single persistent conversation per customer** `[both]` - Each customer has one always-open conversation, auto-created on first visit, so they can message instantly with no ticket form or waiting screen.
- **Topic-based routing** `[both]` - Customers arrive with a topic slug (general, job, masala, product, donation, marriage, zakat) or choose one; the topic decides which agents see the chat, and topic changes are logged in the thread.
- **Topic selection and change prompts** `[frontend]` - Customers get a topic picker and a change-topic prompt when they reopen support through a different topic.
- **Business-hours and bot replies** `[both]` - Outside working hours, or when no agent assigned to the topic is online, the backend posts the admin-configured automatic reply, rate-limited by a per-conversation cooldown.
- **Site-unavailable screen** `[both]` - An admin "site off" switch makes the backend refuse customer messages and the frontend show a dedicated unavailable screen (agents unaffected).
- **FAQ quick questions** `[both]` - A chip bar above the composer offers the topic's published questions (with a searchable "see all" list); picking one sends it over the `conv:ask_question` socket event and the stored answer is posted back.
- **Auto-open FAQ bar** `[frontend]` - For customers only, the FAQ bar opens once per browser session when the conversation is new or the last message is from a previous day.
- **Phone-number prompt** `[both]` - A lazy-loaded popup asks customers without a contactable number to provide one, which the backend forwards to the platform.
- **Customer settings dialog** `[frontend]` - Customers can change topic, chat background, dark mode and other device-local preferences from the chat header menu.
- **Android app header integration** `[frontend]` - Inside the Android WebView the chat header shows the app logo, and tapping it closes the WebView via the native bridge.

## 3. Messaging Core

- **Real-time messaging over Socket.IO** `[both]` - Messages, edits, deletions, reactions, topic changes and read receipts are pushed live through user- and conversation-scoped rooms.
- **Typing indicators** `[both]` - `conv:typing` / `conv:stop_typing` events show who is typing, auto-clearing after a short timeout.
- **Message types and soft delete** `[backend]` - TEXT, IMAGE, VIDEO, AUDIO, FILE and SYSTEM messages, with soft-delete so history and audit trails are preserved.
- **Rich-text composer** `[frontend]` - A Tiptap editor supports bold, italic, underline, strike and links via shortcuts or a floating menu, rendered safely with DOMPurify and without `dangerouslySetInnerHTML`.
- **Legacy-format compatibility** `[frontend]` - Older marker-based formatting (`**bold**`, `_italic_`) is still rendered through the same rich-text helpers.
- **Reply, edit and delete** `[both]` - Messages can be quote-replied, edited (with edited-at tracking) and soft-deleted, subject to admin audience rules.
- **Emoji reactions** `[both]` - Per-message reactions with an emoji picker, per-user quick-reaction ordering, and an admin switch to turn reactions on or off.
- **Forwarding** `[both]` - Agents can forward messages into other conversations or groups via a forward-target picker, with an optional "forwarded" label.
- **Cursor-paginated history** `[both]` - 30-message pages load older history on scroll-up using a `(created_at, id)` cursor that preserves database-clock precision.
- **Jump to message** `[both]` - Tapping a reply quote or a `?scroll_to=` link fetches a window centred on that message, highlights it, and pages forward from there.
- **Server-side message filters** `[both]` - Deleted, system and topic filters are applied in SQL so pages are never padded with hidden rows; the client filter only keeps live arrivals consistent.
- **Read receipts and "seen" ticks** `[both]` - Customers see when an agent has read their messages; ticks are only stamped when the reader is an active participant.
- **Link previews with failure caching** `[both]` - The backend fetches link previews and caches successes and failures in Redis with status-aware TTLs; the client also remembers dead links per session.
- **Draft persistence** `[frontend]` - Unsent composer text is kept per conversation in a local store.

## 4. Media, Voice & File Handling

- **Image, video, audio and file uploads** `[both]` - Uploads go through one endpoint with per-role size limits and MIME validation; the returned URL is attached to the message.
- **Image and GIF compression** `[backend]` - sharp resizes and recompresses images to JPEG and animated GIFs to WebP.
- **Video and audio processing** `[backend]` - ffmpeg handles video and re-encodes recorded audio to mp3; a semaphore caps concurrent compression jobs.
- **Voice messages** `[frontend]` - A MediaRecorder-based recorder with pause/resume, level meter, 5-minute cap and guaranteed microphone release produces AUDIO messages.
- **Audio player workaround** `[frontend]` - The player copes with MediaRecorder clips that report an infinite duration.
- **Media preview and caption** `[frontend]` - Selected files open in a preview dialog where the sender adds a caption before sending.
- **Built-in image editor** `[frontend]` - A canvas editor offers crop, rotate, pen, arrow, rectangle, ellipse and text objects that can be moved, resized, restyled and reordered, with undo/redo.
- **Media gallery** `[frontend]` - A swipe/arrow/thumbnail slider with zoom, download and agent share covers every image, video, audio and file in the conversation, loaded lazily.
- **Android WebView share and download** `[frontend]` - `AndroidNative` wrappers share text and save images/PDFs through the app's native viewer, falling back to normal browser behaviour elsewhere.
- **Native bridge specification** `[backend]` - A written spec (`webview-share-download-methods.md`) documents the app's `share`, `shareScreenshot`, `downloadPdf` and `downloadFile` calls and their rules.
- **Cross-origin download support** `[backend]` - CORS is enabled before the static `/uploads` middleware so cross-origin `fetch()` downloads of media work (documented in a code comment).
- **WebView back-button buffer** `[frontend]` - A history-seeding hook makes the first Back press from a notification deep link land on the inbox instead of exiting the app.
- **Group avatars** `[backend]` - Uploaded group pictures are validated, centre-cropped to a 512x512 JPEG and stored separately from chat media.

## 5. Agent Console (Inbox)

- **Live conversation inbox** `[both]` - A master-detail inbox lists customer conversations sorted by last message, with pagination, unread badges and skeleton loading.
- **Topic-scoped visibility** `[backend]` - Agents see only conversations in topics they are assigned to; searching a full customer email can reveal a specific customer outside those topics.
- **Join and leave conversations** `[both]` - Any number of agents can join or leave a conversation; participants update live and affect who receives notifications.
- **Auto-join preference** `[frontend]` - Agents can opt into automatically joining conversations they open.
- **Per-agent read state** `[both]` - Each agent has their own read cursor and "mark as unread" flag per conversation, so one agent reading never clears another's unread count.
- **Unread totals and tab indicator** `[both]` - A server unread count plus tab title/favicon indicators show pending work, skipping muted groups.
- **Conversation filter panel** `[frontend]` - Agents filter the list by search text, topic, tags, unread only, blocked only, initiator and "assigned to me".
- **Block / unblock customers** `[both]` - Agents block a conversation with a preset or custom reason, which is written to the activity log.
- **Assign agents and change topic** `[both]` - A "chat control" tab saves topic and assigned agents together, with every change logged as a system message.
- **Activity log** `[both]` - System messages record joins, topic changes, blocks and similar events, visible only to agents with log permission.
- **Multi-select with drag selection** `[frontend]` - Agents select many messages by press-and-drag (with edge auto-scroll and touch-friendly gestures) for bulk actions such as forward or add-donation.
- **Context menus on bubbles and list items** `[frontend]` - Right-click / long-press menus expose reply, react, forward, select, mark read/unread, leave and block actions.
- **Browser notifications and sounds** `[frontend]` - Agents get a generated ring tone (Web Audio), an OS notification and a flashing tab title when the tab is inactive; muted topics are respected.
- **Topic mute** `[both]` - Agents can mute a topic for preset durations, silencing sounds and pushes while messages still arrive.
- **Proactive outreach** `[both]` - Agents can look up a customer by email and start a conversation with them, marked as agent-initiated.
- **Deep link from the admin panel** `[both]` - `/customer?email=&topic=` opens or creates a customer's conversation, falling back to a "message a user" dialog when the topic is outside the agent's scope.
- **Per-message topic reassignment** `[both]` - An agent can move a single message to a different topic.

## 6. Agent Productivity Tools

- **Notes with slash picker** `[both]` - Reusable canned notes (text and attachments) are inserted by typing `/` in the composer, filterable by topic, with a device-local default view mode.
- **Notes management** `[both]` - Permissioned CRUD for notes, plus "Add as Note" straight from a chat message.
- **FAQ and automated answers** `[both]` - Admin-managed Q&A and automatic-message entries per topic, with draft/publish status, an on-user-join trigger, and "Add as Answer" from a message.
- **Tags** `[both]` - Colour-coded tags that can be attached to conversations, with audit fields and quick-create from the chat.
- **Agent settings page** `[frontend]` - A routed, code-split settings area covers notes, FAQ answers, tags and personal preferences.
- **Order links for agents** `[frontend]` - Each order in the info panel links to the public invoice page and to the matching admin-panel order search.

## 7. Customer Info Panel (CRM Integration)

- **Customer info sections** `[both]` - The info panel loads, per section, the customer's basic profile, phone numbers, orders, payments, donations, masala, marriage biodata, job profiles and blood-donation records via the platform admin API.
- **Redis-cached, on-demand sections** `[backend]` - Sections are fetched lazily, cached briefly in Redis, and never copied into the user table; a "clear cache" button drops them for one customer.
- **Marriage biodata viewer** `[frontend]` - A lazy dialog renders a customer's full biodata with option codes mapped to readable labels.
- **Orders and payments lists** `[frontend]` - Count plus latest rows are shown, with a retry button on failed sections.
- **Blood and jobs profiles** `[both]` - Blood-donation profile, recent donations and requests, and recruiter / job-seeker profiles are viewable with detail dialogs.
- **Offline donation entry** `[both]` - Agents with the opt-in `canAddDonation` permission record an offline donation and trigger the receipt email, notification and SMS through the admin API's own logic.
- **Donation check and linking** `[both]` - Agents search existing offline payments by any text (other customers' rows are masked) and claim an unclaimed payment for the current customer, with the previous values kept for audit.
- **Screenshot OCR for payments** `[backend]` - Tesseract (English plus Bangla) with sharp pre-processing reads payment screenshots from chat, and a parser extracts transaction id, amount, agent, date and numbers; results are cached per message.
- **Add donation from chat messages** `[frontend]` - Agents pick messages and images in the chat, view them beside a check tab and an add form, and OCR chips pre-fill the form.

## 8. Agent Group Chats

- **Agent-to-agent groups** `[both]` - Groups reuse the conversation model (`kind = GROUP`) and chat window, with members, no customer and no topic.
- **Group roles and member permissions** `[both]` - ADMIN/MEMBER roles plus per-member jsonb permissions, layered under each agent's global permissions; the server enforces both layers.
- **Group lifecycle management** `[both]` - Create, rename, edit description, add/remove members, change roles, leave and delete, with the rule that a group never loses its last admin.
- **Group mute and push** `[both]` - Personal mute silences sound and push while keeping unread counts; offline unmuted members receive queued pushes.
- **Group info panel** `[frontend]` - Members, roles, permissions, name, picture and media in a dedicated panel.

## 9. Admin-Controlled Settings & Live Config

- **Support settings** `[both]` - Working hours (timezone-aware, overnight ranges), bot replies, chat/messaging rules and site status are stored as jsonb rows and mirrored across four codebases.
- **Live settings propagation** `[backend]` - The admin API publishes a RabbitMQ event; the backend reloads its in-memory settings and emits `settings:updated` to every socket (it also reloads after a broker reconnect).
- **Messaging audience rules** `[both]` - Allowed message types, character limits, and who may reply, edit, delete or react are enforced server-side and mirrored in the UI.
- **Topic ordering** `[both]` - Topics carry a `sort_order` set by drag-and-drop in the admin panel and arrive pre-sorted.
- **Admin-API-managed agents and topics** `[external]` - Agent and topic assignment tables are written directly by the admin API, so schema changes need cross-repo coordination.

## 10. Notifications & Eventing

- **Online presence tracking** `[backend]` - Redis-backed presence tells the backend whether a user has an active socket.
- **Offline push via notification queue** `[backend]` - A notification service publishes to a RabbitMQ queue with channel flags (FCM, email, SMS, socket, Redis, DB) and can send only to offline users.
- **Notification dedupe** `[backend]` - A 2-hour (conversation, recipient) key stops chatty conversations from flooding the stored notification list.
- **Event publishing for other systems** `[backend]` - Support events are exchanged on a durable `support` RabbitMQ topic exchange.
- **Targeted socket notifications** `[both]` - New-conversation, new-message, assignment and group events go to per-user rooms and are handled centrally in a single socket hook on the client.
- **Automated message API** `[backend]` - A `POST /send-message` endpoint lets another platform service open a topic conversation on a customer's behalf and post a message preceded by an "automated message" system note.

## 11. UI, Personalisation & Performance

- **Colour themes and dark mode** `[frontend]` - Multiple colour themes work in light and dark, switched via CSS variables on the document root.
- **Chat wallpapers** `[frontend]` - Default, solid, gradient, icon-pattern or user-photo backgrounds, with blur and optional flow animation, stored on-device.
- **Personal preferences** `[frontend]` - Font size, Enter-to-send, reaction order, note view mode and the optional "new chat" tab are device-local settings.
- **Code-splitting by role** `[frontend]` - Customers never download the router or agent-only code; heavy pieces (editor, gallery, image editor, phone data) are lazy chunks with manual vendor chunking.
- **Render-performance discipline** `[frontend]` - Selector-based store reads, memoised bubbles and list items, module-level Intl formatters and no hover popovers on touch devices, targeting phones.
- **Radix-based component library** `[frontend]` - Dialogs, menus, selects, tabs, popovers and context menus built on Radix primitives with a local `ui/` layer.
- **Swipeable slide tabs** `[frontend]` - A swipe-friendly tab component for two-panel mobile dialogs.

## 12. Data & Operations

- **TypeORM migrations** `[backend]` - 33 migrations track the evolution from tickets to conversations to groups, with `synchronize` disabled.
- **Local dev stack** `[backend]` - Docker Compose brings up Postgres, Redis and RabbitMQ for development.
- **Soft-delete and audit conventions** `[backend]` - Messages and tag changes keep timestamps and actors rather than removing rows.
