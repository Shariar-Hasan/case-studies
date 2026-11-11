# Key Features

Features are grouped by domain. Each bullet is tagged with the repo where it lives, so you can delete the lines you did not work on and keep the rest for your portfolio.

**Repo tags**

- `[management-app]` = `hic-school-management` (Next.js admin dashboard)
- `[management-api]` = `hic-school-management-api` (NestJS backend for the dashboard)
- `[web]` = `hic-school-web` (Next.js public site and parent portal)
- `[web-api]` = `hic-school-web-api` (NestJS backend for the public site)

Every item below was verified in source code (routes, controllers, services, entities, or pages). Items that exist in code but are disabled are called out explicitly.

---

## 1. Public Website and Marketing

- **Public landing page** `[web]` - Home page with hero, program highlights, and calls to action for prospective families.
- **Program catalog and detail pages** `[web]` `[web-api]` - Browse active programs and open a slug-based detail page with description, features, and pricing options.
- **Public events listing** `[web]` `[web-api]` - Families can see upcoming school events published by the administration.
- **About and contact pages** `[web]` - Static informational pages (about us, contact us).

## 2. Parent / Guardian Accounts and Authentication

- **Email signup with verification** `[web]` `[web-api]` - Parents register with email and password and must verify their email (with a resend-verification option) before use.
- **Google sign-in (OAuth 2.0)** `[web]` `[web-api]` - Parents can sign in with Google through a Passport Google strategy and callback flow.
- **Forgot / reset / change password** `[web]` `[web-api]` - Self-service password recovery via emailed reset links, plus an authenticated change-password flow.
- **Session validation at the edge** `[web]` - Next.js proxy checks token expiry and asks the API whether the account is still valid (disabled, locked, or force-logged-out accounts are signed out with a reason).
- **Parent profile with completion tracking** `[web]` `[web-api]` - Profile editing with image upload and a "profile eligibility" check that guides parents to complete required details before registering.
- **Cookie-based JWT sessions** `[management-api]` `[web-api]` - HttpOnly cookie tokens (with bearer-token support for API clients), refresh endpoint, and logout.
- **Management login, profile, and password change** `[management-app]` `[management-api]` - Staff sign in, view their profile and effective permissions, and change their password.
- **Admin-initiated account actions for users and guardians** `[management-app]` `[management-api]` - Send reset link, set a password, force logout, enable / disable, and delete accounts for staff and parents from the dashboard.
- **Forced password change flow** `[management-api]` - A Redis flag requires users with system- or admin-generated passwords to set their own before continuing.
- **Generated-credentials and welcome emails** `[management-api]` - New users receive welcome and generated-credential emails from the queue.

## 3. Role-Based Access Control (RBAC) and Impersonation

- **Permission-slug authorization on every endpoint** `[management-api]` - A global guard checks `@Permissions([...])` metadata against the user's effective permissions, with public-route and super-admin handling (170+ slugs defined).
- **Roles and role-permission management** `[management-app]` `[management-api]` - Create and edit roles, assign permissions to roles, and assign users to roles (and roles to users) from the UI.
- **Direct per-user permissions with source tracing** `[management-app]` `[management-api]` - Grant permissions directly to a user and view where each effective permission comes from (role or direct).
- **Permission catalog CRUD** `[management-app]` `[management-api]` - Admin pages for managing permission records (title, slug, category, sub-category).
- **Permission-aware UI** `[management-app]` - `PermissionGuard`, `usePermissionGuard`, and permission-tree hooks hide or disable navigation and actions the user cannot use.
- **Redis-cached permissions with live refresh** `[management-api]` `[management-app]` - Effective permissions are cached in Redis and a `user-roles:updated` WebSocket event makes open sessions pick up changes immediately.
- **Admin impersonation ("login as")** `[management-app]` `[management-api]` - Super admins can act as another user for a 15-minute Redis-backed session, with a banner and countdown, exit action, and automatic expiry.
- **Impersonation safety rules** `[management-api]` - Effective permissions are capped by the admin's own, administrators can only be impersonated by super admins, and `@NoImpersonation()` blocks sensitive actions.
- **Impersonation audit trail** `[management-api]` - Start and end events are audited exactly once (including abandoned sessions swept every minute), and audit rows record the `impersonated_by` actor.

## 4. Admissions and Registration

- **Parent-led program registration** `[web]` `[web-api]` - Parents pick a program and fee structure, select participating children, choose products, and review a purchase summary before paying.
- **Child fee structures (multi-child pricing)** `[management-api]` `[web-api]` - Programs define fee structures by number of children, price, early vs regular registration, and one-time vs installment payment mode; the API validates the participant count against the structure.
- **Admin-side purchase and registration** `[management-app]` `[management-api]` - Staff can register students into a program on behalf of a family from the dashboard ("purchase program" flow and admin-registration endpoint).
- **Admission orders list and detail** `[management-app]` `[management-api]` - Order lists with filters and status counts; a detail page showing customer info, student info, items, payments, and an activity log.
- **Program-specific admission questions** `[management-app]` `[management-api]` - Assessment questions (text, select, multi-select, checkbox, radio) can be attached to programs.
- **Seat limits per program and session** `[management-api]` - Program configs carry a max-seat value per session, which class-section logic uses to detect full programs (0/null means uncapped).
- **Student registrations management** `[management-app]` `[management-api]` - Registration list with status counts, per-program stats and rosters, detail view, and exports.
- **Remove student from a level** `[management-app]` `[management-api]` `[web]` - Staff can remove a registration from a class section; families see and are emailed about it.
- **Migrate student to another program** `[management-api]` - An endpoint moves a student between programs with notification emails to family and admin.
- **Post-payment enrollment** `[management-api]` - After a successful payment, registrations are completed and students are enrolled into class sections automatically (idempotent).

## 5. Fees, Orders and Payments

- **Orders with line items** `[management-api]` `[management-app]` - Each order (student fee) holds items of type program, product, item, discount, or scholarship, with subtotal, discount, refund, scholarship, and total calculations.
- **Installment schedules** `[management-api]` `[management-app]` `[web]` - Orders can be paid in installments; schedule rows track installment number, due date, amount, and status, and the UI lets staff add or edit installments.
- **Record and edit payments** `[management-app]` `[management-api]` - Staff record payments (cash, bank transfer, cheque, online, Zelle, other), update them, and allocate them across installments.
- **Online card checkout (Clover)** `[management-api]` `[web-api]` `[web]` - Pending payments can generate a hosted Clover checkout URL; parents can create new payments or retry a failed one from their order page.
- **Payment confirmation processing** `[management-api]` - A RabbitMQ consumer and idempotent confirmation logic (with a pessimistic DB lock) mark payments paid, post accounting entries, and complete registration.
- **Refunds with online and offline parts** `[management-api]` `[management-app]` - Refund an order across Clover (card) and manual refund lines in one transaction, with rollback handling and loud logging if a card refund succeeded but the DB commit failed.
- **Add items to an existing order** `[management-api]` `[management-app]` - Add products or items to an order, creating the extra balance and notifying the customer.
- **Change program on an order** `[management-api]` `[management-app]` - Move an order to another active program and compatible fee structure for the session, handling price differences.
- **Cancel an order** `[management-api]` `[management-app]` - Cancel orders and reflect status in lists and reports.
- **Discounts applied through the refund flow** `[management-api]` - A discount line can be added to an order; pending payments/installments are consumed from the last one backwards (cancelled or reduced) before any cash refund is issued.
- **Invoices (print / PDF) and email** `[management-api]` `[management-app]` - Print an order invoice and email it to the customer.
- **Fees stats and exports** `[management-app]` `[management-api]` - Total paid, status counts, filterable fee tables, and CSV / PDF exports.
- **Parent order and fees views** `[web]` `[web-api]` - Parents see their orders, order details, payment history, and installment status in "Purchases and Fees" and "Admissions".
- **Payment result pages** `[web]` `[management-app]` - Status pages shown after returning from checkout (`/payment/[status]`, `/payment`).
- **Installment reminder emails (disabled)** `[management-api]` - A nightly cron job and email templates for installment reminders exist, but the cron call is currently commented out pending changed reminder settings.

## 6. Scholarships

- **Scholarship applications by parents** `[web]` `[web-api]` - Parents can see ongoing scholarships and apply for a scholarship per child.
- **Scholarship review workflow** `[management-app]` `[management-api]` - Staff review, approve, reject, or adjust applications (statuses: pending, under review, approved, rejected, cancelled) with decision notes.
- **Per-program scholarship budgets** `[management-api]` - Each program config tracks total, remaining, and used scholarship amounts.
- **Apply scholarship to an order** `[management-app]` `[management-api]` - Options endpoint calculates the applicable amount per student; applying it creates scholarship items and reduces or refunds the balance.
- **Deferred scholarship finalization** `[management-api]` - Scholarships are reserved at application time and finalized (marked used, budget updated) only when the payment is confirmed.
- **Scholarship report and detail modals** `[management-app]` - Scholarship list, details, adjustment modal, and a scholarships report table.

## 7. Academics: Programs, Sessions, Levels, Subjects

- **Programs** `[management-app]` `[management-api]` - Create and manage programs with slug, image, description, features, status, approval, display sequence, and drag-and-drop reordering.
- **Sessions (academic periods)** `[management-app]` `[management-api]` - Define sessions that scope program configs, fees, and class sections.
- **Program configs** `[management-app]` `[management-api]` - Per-session program configuration (seats, scholarship budget) and fee setup.
- **Program settings** `[management-app]` `[management-api]` - Reusable program settings with list, dropdown, and export endpoints.
- **Products catalog** `[management-app]` `[management-api]` - Sellable products (linked to subjects/programs) that can be added to orders.
- **Class sections ("Levels")** `[management-app]` `[management-api]` - Create levels per program, subject, and session with capacity and weekly hours; assign teachers; enroll or unassign students.
- **My Levels / My Classes / My Students (teacher views)** `[management-app]` `[management-api]` - Teachers see only the levels and students assigned to them.
- **Subjects and topics** `[management-app]` `[management-api]` - Manage subjects, topics, and subject-topic relationships.
- **Bulk email to a level's students** `[management-app]` `[management-api]` - Send an email to everyone in a class section.
- **Teacher proxy access** `[management-app]` `[management-api]` - A teacher can grant another teacher temporary access to a section (pending, accepted, rejected, revoked) with supporting documents and email notification.

## 8. Students, Parents and Records

- **Student CRUD with profile images** `[management-app]` `[management-api]` - Create, edit, upload photos for, and view students, with enrollment, attendance, grade, payment, and scholarship tabs.
- **Parent contacts management** `[management-app]` `[management-api]` - Manage guardians and their contact information, with detail modals.
- **Link and unlink students and parents** `[management-app]` `[management-api]` - Attach or detach students from a guardian record from the parent details modal.
- **Parents manage their own children** `[web]` `[web-api]` - Add, edit, and remove children (with photo upload) from "My Children".
- **Student documents** `[management-api]` `[web]` - Store and retrieve student documents; parents can view documents for their child.
- **Shared fee across siblings** `[management-api]` - One order can span multiple student registrations, so amounts are divided by the number of sharing students.
- **Student report (stats and PDF)** `[management-app]` `[management-api]` - Per-student report with statistics and export.

## 9. Attendance, Exams, Grading and Homework

- **Attendance marking (single and bulk)** `[management-app]` `[management-api]` - Mark attendance per section, bulk mark, edit records, and view summaries per student.
- **Attendance stats and exports** `[management-app]` `[management-api]` - Stat views and CSV / Excel-style exports.
- **Parent view of attendance** `[web]` `[web-api]` - Parents see attendance summaries and per-section details for each child.
- **Exams with approval and cancellation** `[management-app]` `[management-api]` - Schedule exams, approve or cancel them, and attach documents; exam-scheduled emails are sent.
- **Grading systems** `[management-app]` `[management-api]` - Define grading scales (for example A+, B, C) and export them.
- **Marking and publishing results** `[management-app]` `[management-api]` - Initialize and mark grades per exam, import and export results, publish them, and print student results.
- **Result downloads for parents** `[web]` `[web-api]` - Parents view grades and download single or all result documents.
- **Homework assignment and publishing** `[management-app]` `[management-api]` - Teachers create homework per level, attach files, publish, and review submissions.
- **Homework submission and grading** `[web]` `[web-api]` `[management-api]` - Students/parents submit homework with uploaded documents; teachers grade each submission.

## 10. Scheduling and Events

- **Timetable builder** `[management-app]` `[management-api]` - Weekly timetable with time-slot modal, weekly routine view, and conflict-aware scheduling data (rooms, teachers, days).
- **School events** `[management-app]` `[management-api]` `[web]` `[web-api]` - Create and manage events with documents and status; published events appear on the public site and trigger an email.
- **Rich-text content editor** `[management-app]` `[web]` - TipTap editor with formatting, colors, links, images, and mentions for descriptions and notices.

## 11. Staff, Payroll and HR

- **Staff / user management** `[management-app]` `[management-api]` - Create staff (admin, teacher, employee, accountant, other), manage status (active, inactive, disabled, muted, locked), documents, and export.
- **Teacher and user profile pages** `[management-app]` `[management-api]` - Detail pages showing levels, attendance, documents, performance, and salary information.
- **Performance notes** `[management-app]` `[management-api]` - Record and review performance notes for staff.
- **Work schedules and work days** `[management-app]` `[management-api]` - Define staff work schedules and track work days with approval.
- **Salary components** `[management-app]` `[management-api]` - Base salary, allowances, bonuses, penalties, and adjustments per user.
- **Salary generation (weekly or monthly)** `[management-app]` `[management-api]` - Generate payroll runs from components and view per-user payroll details.
- **Payroll disbursement and withdrawals** `[management-app]` `[management-api]` - Disburse payroll, track withdrawals, and send disbursement emails to the user and admin.
- **Teacher payment overview** `[management-app]` - Finance pages showing teacher payment history and progress.
- **Payroll types** `[management-app]` `[management-api]` - Configurable payroll component types.

## 12. Finance, Accounting and Reporting

- **Double-entry accounting ledger** `[management-api]` - Chart of accounts, journal entries and entry details, general ledger, and fiscal periods; student payments, refunds, salaries, and expenses post entries.
- **Expenses and expense categories** `[management-app]` `[management-api]` - Record expenses, manage categories and components, and view monthly reports.
- **Finance reports hub** `[management-app]` `[management-api]` - Reports for orders, registrations, scholarships, salaries, expenses, budgets, attendance, results, parents, and rosters, with exports.
- **User and student report endpoints** `[management-api]` - Per-user stats, levels, payrolls, and attendance summary; per-student stats and export.
- **PDF and data exports** `[management-api]` `[management-app]` - PDF generation (Puppeteer, PDFKit, client-side `@react-pdf/renderer`) and CSV / Excel exports across modules.
- **Dashboard home with configurable widgets** `[management-app]` `[management-api]` - Dashboard cards and charts, with widget configuration stored in admin settings and cached in Redis.

## 13. Communication and Notifications

- **Admin notices (site-wide banners)** `[management-app]` `[management-api]` - Publish notices that appear to staff, with live updates pushed to all open sessions.
- **Send mail to recipients** `[management-app]` `[management-api]` - Pick recipients and send custom emails from the dashboard.
- **Queued transactional email** `[management-api]` `[web-api]` - Emails are published to RabbitMQ and sent by a consumer with timeouts and retry with backoff.
- **35+ HTML email templates** `[management-api]` - Registration, payment confirmation / update / refund, items added / exchanged / refunded, result published, proxy access, enrollment, migration, payroll, password reset, and more (each with admin variants where relevant).
- **Email template preview (demo)** `[management-app]` `[management-api]` - Demo pages to preview email and report templates by slug.
- **Realtime notification centre** `[management-app]` `[management-api]` - Socket.IO signaling channel and a notification store keep the UI in sync without polling.

## 14. Documents, Media and Uploads

- **Media library** `[management-app]` `[management-api]` - Folders and tree view, upload, move, trash and restore, bulk delete and bulk download, and usage stats.
- **Document management** `[management-app]` `[management-api]` - Upload documents or create them from media, and attach to users and students.
- **File upload service** `[management-api]` `[web-api]` - Multer-based uploads served from a static uploads path.

## 15. Platform, Security and Operations

- **Audit logging of all data changes** `[management-api]` - A TypeORM subscriber records create, update, and delete with old/new JSONB values, user, IP, and impersonation actor, while skipping sensitive fields.
- **Audit log query API and order activity log** `[management-app]` `[management-api]` - Query audit logs by table, record, or order; the order detail page includes an activity log component.
- **Admin settings (key-value)** `[management-app]` `[management-api]` - Configurable settings with upsert, exposed to the UI as a single object.
- **API hardening** `[management-api]` `[web-api]` - Helmet, compression, throttling (rate limit), global validation pipe (whitelist and forbid unknown fields), global exception filter, and request logging.
- **Swagger API documentation** `[management-api]` - Interactive docs generated from controller decorators.
- **Structured logging** `[management-api]` `[web-api]` - Winston console and file logging (error and combined logs).
- **Containerized CI/CD** `[management-app]` `[management-api]` `[web]` `[web-api]` - Dockerfile, Docker Compose, and a Jenkins pipeline per repo with scripted build, stop, and run steps.
