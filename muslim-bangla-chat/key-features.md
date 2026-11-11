# Key Features

Each bullet is one verified feature. Delete the bullets you did not work on, and keep the rest for your portfolio.

Tags: `[site]` = `mb-chat-app-site` (React frontend), `[api]` = `mb-chat-app-api` (NestJS backend), `[site+api]` = implemented across both.

---

## 1. Authentication & Session

- **Single sign-on via Muslim Bangla SSO** `[api]`: Validates the bearer token against the external identity service and syncs a local user row on every authenticated request.
- **Cached token validation** `[api]`: Caches validated identity payloads in Redis with a configurable TTL so most requests skip the SSO round trip.
- **Token hand-off from the main site** `[site]`: Captures a `?token=` query parameter on landing, stores it, and strips it from the URL.
- **Iframe `postMessage` auth handshake** `[site]`: Signals `CHAT_READY` to an embedding host page and accepts an `AUTH_TOKEN` message so the chat can sign in inside a cross-origin iframe.
- **Resilient token storage** `[site]`: Holds the token in memory as well as a cookie, so third-party-cookie blocking in iframes does not drop the session.
- **Blocked-account enforcement** `[api]`: Rejects requests from accounts flagged as blocked, with a Bengali error message.
- **Socket authentication** `[api]`: Authenticates each WebSocket connection once at handshake (auth token or query token) and disconnects invalid clients.
- **Current-user and permissions endpoint** `[site+api]`: The app loads the signed-in user's profile and effective permissions from `GET /auth/me` to gate the UI.
- **Dev sign-in shortcut** `[site]`: Development-only sign-in page with quick-login buttons for test accounts.

## 2. Conversations

- **One-to-one chats** `[site+api]`: Starts a private conversation with another user and reuses the existing one if it already exists.
- **Group chats** `[site+api]`: Creates named group conversations with an initial set of participants chosen by the creator.
- **Conversation list with pagination** `[site+api]`: Lists the user's conversations with last-message preview, timestamps and unread state.
- **Unread indicators and mark-as-unread** `[site+api]`: Shows unread conversations and lets a user manually mark a conversation as unread.
- **Edit conversation info** `[site+api]`: Updates a conversation's name, description and image.
- **Conversation status control** `[site+api]`: Switches a conversation between active, archived and blocked, restricted to users with the right permission.
- **Typed conversations with default settings** `[api]`: `PRIVATE`, `GROUP`, `MARRIAGE_PRIVATE` and `SUPPORT` types each carry their own defaults, without changing the chat engine.
- **Sparse settings storage** `[api]`: Entity subscribers store only the settings that differ from type defaults, and expand them to full objects on load.
- **Per-conversation settings panel** `[site+api]`: Admin UI with toggles for invites, adding/removing members, leaving, replies, edits, deletes, phone/email/link sharing, history for new members, and participant list visibility.
- **Generated conversation names** `[api]`: Derives display names for private and marriage chats, and falls back to a default group name.
- **Conversation URLs by UID** `[site+api]`: Routes address conversations by an external UUID instead of an internal integer ID.

## 3. Messaging

- **Realtime send and receive** `[site+api]`: Messages are sent over Socket.IO and broadcast to everyone in the conversation room.
- **REST send endpoint** `[api]`: The same send pipeline is also exposed over HTTP, so both transports share one set of rules.
- **Optimistic sending with retry** `[site]`: Shows a message immediately as "sending", reconciles it with the server copy via a client message ID, and offers "Failed, retry" on error or timeout.
- **Acknowledgement-based error reporting** `[api]`: Returns validation and permission errors as an ack payload so the sender's UI can react.
- **Reply to message** `[site+api]`: Quote-replies to a previous message, validated server-side against the conversation's reply setting.
- **Edit message** `[site+api]`: Edits own messages (or others', with permission) and shows an "Edited" label.
- **Delete message** `[site+api]`: Soft-deletes messages and shows "This message was deleted" in place.
- **Cursor-based pagination of history** `[site+api]`: Loads older messages on scroll using an IntersectionObserver and preserves scroll position when prepending.
- **Rich-text composer** `[site]`: A TipTap-based editor that flattens to plain text for sending, with reply preview and attachment chip.
- **Message bubble context menu** `[site]`: Right-click or action menu with Reply, Copy text, Edit and Delete, shown only when permitted.
- **Clickable links in messages** `[site]`: Detects URLs in message text and renders them as links.
- **Message validation service** `[api]`: Centrally enforces maximum length, allowed attachment types, and phone, email and link detection.
- **Contact-information blocking** `[api]`: Rejects messages containing phone numbers, emails or links when a conversation disallows them.
- **Consecutive-message limit** `[api]`: Enforces "strictly alternating" conversations by rejecting too many messages in a row.
- **Per-participant message quota** `[api]`: Caps the total messages each participant may ever send in a conversation.

## 4. Read State, Typing & Presence

- **Read receipts (ticks)** `[site+api]`: Sent and read ticks are derived from per-participant read cursors rather than per-message flags.
- **Auto mark-read** `[site]`: Marks a conversation as read on open and whenever a new message arrives while it is visible.
- **Live read-state sync** `[site]`: Applies read events to cached conversation data in place, without refetching.
- **Typing indicators** `[site+api]`: Relays typing start/stop events to the other members of a conversation.
- **Online presence** `[api]`: Tracks online status in Redis with a 5-minute TTL refreshed on every socket event.
- **Reconnection handling** `[site]`: Reconnects with backoff indefinitely and shows a "reconnecting" toast that resolves on success.
- **Cross-instance realtime scaling** `[api]`: A Redis adapter lets Socket.IO events reach clients connected to any API instance.
- **Room management via domain events** `[api]`: Joins and leaves per-user and per-conversation rooms automatically when conversations are created or members change.
- **Sync-required signal** `[site+api]`: A server event that tells clients to refetch when membership or conversation lists change.

## 5. Attachments & Media

- **Message attachments** `[site+api]`: Sends images, video, audio and documents (PDF, Office, text, zip) with a 25 MB limit.
- **Attachment type allow-lists** `[api]`: Each conversation can allow all, none, or specific attachment types.
- **Upload endpoint with MIME filtering** `[api]`: Stores uploads with randomised file names and rejects unsupported types.
- **Conversation image upload** `[site+api]`: Uploads group or conversation images (JPEG, PNG, WEBP, GIF, up to 5 MB).
- **Attachment rendering** `[site]`: Shows inline image, video, audio and file attachments with name and formatted size.
- **Link previews** `[site+api]`: Fetches Open Graph, Twitter and title metadata server-side and renders a preview card under the message.
- **Link-preview safety and caching** `[api]`: Blocks private/internal hosts and caches previews in Redis for seven days.
- **Client-side preview cache** `[site]`: Caches link previews in the browser to avoid repeat requests.

## 6. Members, Invitations & Moderation

- **Member list and details** `[site+api]`: Shows participants and a detail modal for each, hidden when the conversation disables the participant list.
- **Add and remove members** `[site+api]`: Adds or removes participants, with realtime events and notifications.
- **Member search by email** `[site+api]`: Looks up users and customers (via the identity service) by email when adding members.
- **Leave conversation** `[site+api]`: Participants can leave where the conversation settings allow it.
- **Invite links** `[site+api]`: Generates shareable invite links with optional expiry and maximum uses, and lets admins list and revoke them.
- **Join-by-link flow** `[site+api]`: A signed-in user can preview an invitation and accept it, with the API validating expiry, use count and existing membership.
- **Self-service mute** `[site+api]`: Mutes a conversation for one hour, one day, one week or forever, and unmutes it.
- **Moderator mute** `[site+api]`: Moderators can mute a participant for a duration, blocking them from sending messages.
- **Block participant** `[site+api]`: Moderators can block a participant from a conversation.
- **Platform-wide oversight** `[site+api]`: Moderators can view or manage every conversation of a given type, without being a participant.

## 7. Roles, Permissions & Administration

- **Code-defined permission catalogue** `[api]`: A fixed set of permissions grouped into Messages, Members, Conversation, Moderation, Administration and Oversight categories.
- **Dynamic roles** `[site+api]`: Admins create, edit and delete roles and assign permissions to them.
- **System roles** `[api]`: Seeded, undeletable Super Admin and Moderator roles.
- **Super-admin bypass** `[api]`: A Super Admin permission that implicitly grants every current and future permission.
- **Baseline participant permissions** `[api]`: Every active participant automatically gets core chat permissions without a role.
- **Permission guard and decorator** `[api]`: A `RequirePermission` decorator with a guard to protect endpoints.
- **Permission-aware UI** `[site]`: Permission gate component, permission picker and grid, and hooks to show or hide actions.
- **Role management page** `[site]`: Admin screen for browsing roles and editing their permissions in an accordion picker.
- **Admin user management** `[site+api]`: Lists all app users, blocks or unblocks accounts, and assigns global roles.
- **My permissions page** `[site]`: A profile page showing the signed-in user's effective permissions.
- **Super-admin bootstrap scripts** `[api]`: CLI scripts to seed system roles and assign Super Admin to a user by email.

## 8. Marriage Private Chat (Marriage Site Integration)

- **Anonymous marriage chat type** `[api]`: A two-person conversation type with no names or avatars, shown as "Biodata N".
- **Strict marriage-chat rules** `[api]`: 50-character limit, no attachments, no phone/email/link sharing, alternating messages, three messages per person.
- **Auto-close on quota exhaustion** `[api]`: Blocks the conversation automatically once both people have used their message allowance.
- **Edit-only-while-latest** `[api]`: Allows editing a marriage-chat message only while it is still the most recent one.
- **Chat availability endpoint** `[api]`: `GET /marriage-chat/by-biodata/:id` tells the marriage site whether the viewer has a running or completed chat, and the iframe URL.
- **Connection-pinned lookup** `[api]`: Optionally pins the lookup to one connection so an older chat never answers for a newer request.
- **Marriage biodata link table** `[api]`: Correlates conversations to biodata and connections, with a running/completed status and a partial unique index for one running chat per connection.
- **Final completed chats** `[api]`: Any block completes the link in the same transaction, and re-enabling opens a brand-new conversation.
- **Shared database contract** `[api]`: Documents the tables that the marriage, payment and admin services write directly.
- **Embeddable iframe mode** `[site+api]`: `hideHeader` and `showInstruction` query parameters customise the chat for use as a floating widget.
- **Rules dialog** `[site]`: A Bengali "read the rules first" dialog, shown on arrival or once per conversation when embedded, and remembered locally.
- **Read-only completed chats** `[site+api]`: Ended chats stay visible with the composer blocked and a Bengali reason displayed.
- **Anonymous typing and display names** `[site]`: Hides the other party's name and avatar everywhere in the UI for marriage chats.

## 9. Notifications

- **Offline push for new messages** `[api]`: Publishes a notification for each recipient who has no active socket, excluding the sender and muted participants.
- **Member added/removed notifications** `[api]`: Notifies users when they are added to or removed from a conversation.
- **RabbitMQ notification producer** `[api]`: Durable, confirm-channel publisher with reconnect and retry-once logic.
- **Multi-channel payload** `[api]`: Payloads carry channel flags for push (FCM), socket, email, SMS and storage, interpreted by a downstream consumer.
- **Marriage-chat notification deep link** `[api]`: Links straight to the sender's biodata page on the marriage site with the chat auto-opening.
- **Anonymous Bengali notification text** `[api]`: Uses "Biodata N" labels with Bengali digits instead of real names for marriage chats.
- **Transient notification lifecycle** `[api]`: Marks marriage-chat notifications as transient so they disappear once read.

## 10. User Experience & Personalization

- **Light, dark and system themes** `[site]`: Persisted theme preference with Tailwind-based theming.
- **Chat theme colours** `[site]`: Six accent palettes (emerald, blue, purple, rose, amber, teal) with light and dark variants.
- **Chat backgrounds** `[site]`: Selectable tiled patterns (hearts, bubbles, stars, dots, leaves, geometric) behind the message list.
- **Text size setting** `[site]`: Small, default and large text sizes.
- **Responsive layout** `[site]`: Sidebar and thread layout with a mobile back button and a hamburger menu.
- **Reusable UI kit** `[site]`: Radix-based common components (dialog, dropdown, popover, tabs, select, table, command palette and others).
- **Reusable data table** `[site]`: Search, pagination and row actions for admin tables.
- **Global confirm dialogs and context menu** `[site]`: Provider-based confirm dialog and a global context-menu system.
- **Empty states and skeleton loaders** `[site]`: Friendly placeholders for loading and empty lists.

## 11. Platform, Quality & Operations

- **Bengali user-facing errors** `[api]`: A global exception filter and validation pipe produce simple Bengali messages with per-field details retained.
- **Domain events architecture** `[api]`: Services emit events consumed by the realtime gateway and notification triggers.
- **TypeORM migrations** `[api]`: A versioned migration history of 16 migrations, with a snake-case naming strategy.
- **Structured logging** `[api]`: Winston logger with daily-rotating log files.
- **Configurable CORS for embedding** `[api]`: Comma-separated allowed origins so both the chat site and the marriage site can call the API.
- **Unit test for marriage-chat service** `[api]`: A Jest spec covering the marriage chat lookup service.
- **Typed constants shared in spirit across repos** `[site+api]`: Socket event names, permissions and conversation constants are mirrored on both sides.
