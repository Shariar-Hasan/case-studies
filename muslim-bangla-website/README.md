# Muslim Bangla Website

> The web home of Muslim Bangla: Quran, Hadith, prayer times, Masail, lectures, books and more, in Bangla, for Bengali-speaking Muslims worldwide.

## Description

Muslim Bangla Website is the public web platform of the Muslim Bangla ecosystem (site and app brand: "মুসলিম বাংলা"). It brings the content of the Muslim Bangla mobile app to the browser: a Quran reader with Bangla translations, tafsir and audio recitation; Hadith collections; location-based prayer times, Ramadan timetables and calendars; Dua; Masail (Islamic Q&A); articles and blogs; books; lectures and speakers; a daily "Today's Amal" page; Hajj and Umrah guides; Zakat tools; and an online shop. All of it is rendered in Bangla with Arabic text shown in dedicated fonts.

The site is a single Next.js application built on the App Router. Content pages are mostly server-rendered or statically generated with time-based revalidation, with per-page SEO metadata, a generated sitemap and robots rules, so that Bangla Islamic content is discoverable through search. The same application also acts as a web companion for the native apps: when it is opened inside the Android or iOS app (via an app id and token in the URL) it hides the site chrome and signs the user in from that token.

Beyond reading content, the site supports signed-in features: OTP login, bookmarks, article and Masail comments, asking Masail questions, donations and Zakat payments, product orders with invoices, and a masjid "signage" dashboard for managing mosque TV display content and iqamah (congregation) times.

## At a glance

| | |
|---|---|
| **Type** | Public content website / web platform (Next.js, App Router) |
| **Platforms** | Responsive web (desktop and mobile browsers); also loaded as an in-app web view by the Android and iOS apps |
| **Language** | Bangla-first UI, with Arabic and English text in places |
| **Repo** | Single repository, `muslimbangla-web-next` (private GitLab project; about 3,800 commits from several contributors in git history) |
| **Status** | TODO: owner to confirm (in production / maintained). Source shows production deployment config (Docker, Jenkins, PM2) and active commits through late 2026 |
| **Live URL** | TODO: add live URL (the code references `muslimbangla.com` as the canonical domain) |
| **Timeline** | TODO: add dates. Git history begins October 2024; changelog entries start July 2025 |

## Tech stack

| Layer | Technology |
|---|---|
| **Frontend** | Next.js 16 (App Router, Turbopack), React 19, TypeScript, Tailwind CSS 4, shadcn/ui + Radix UI, MUI, Zustand, nuqs (URL state), react-hook-form + Zod, Motion, Embla carousel, Sonner toasts, local fonts via `next/font/local` (Kalpurush, Tajawal, Noorehuda, Uthmanic) |
| **Backend / APIs consumed** | No database of its own. Consumes a set of separate Muslim Bangla REST APIs (content, Quran, Hadith, Masail, login, product, payment, location, notification, signage, promo, quiz, search) through server actions and fetch wrappers. A few Next.js route handlers (`/api/revalidate`, `/api/proxy-pdf`, sitemap, robots) |
| **Infra** | Docker (Node 22 Alpine image), Jenkins pipeline with shell scripts, PM2 cluster config, Docker Compose, Redis (server-side response cache and view counters) |
| **Integrations** | bKash and other payment gateway flows via the payment API, Socket.IO (notifications), PostHog and Google Analytics, Google search integration, `adhan` prayer-time library, Hijri/Bangla calendar libraries, PDF rendering (react-pdf / pdfjs), jsPDF and html-to-image (invoice download), YouTube thumbnails and embeds, `js.foresight` link prefetching |

## Contents

- [case-study.md](./case-study.md): the full narrative (problem, solution, architecture, challenges, outcome)
- [key-features.md](./key-features.md): feature inventory grouped by website section, to prune down to your own contributions
- [architecture.md](./architecture.md): app structure, routes, data fetching, state, SEO and deployment
- `assets/`: screenshots and diagrams

## Screenshots to add

Drop images into `assets/` and link them from here. Suggested set:

- Home page (hero, menu options, "Today" section)
- Prayer times page for a city (with location selector and adjustment dialog)
- Quran sura reader (translation view and recitation player)
- Hadith book / chapter view
- Article detail page (with sidebar, comments, share modal)
- Masail list and detail
- Today's Amal page (tabs: verse, hadith, dua, quiz, leaderboard)
- Zakat calculator
- Products page, cart and checkout
- Signage dashboard (masjid profile, contents, display zones)
- Hajj guide (steps checklist and visual timeline)
- Mobile view of two or three of the above

TODO: add screenshots.
