# Key Features

Features are grouped by website section. Each bullet was verified against the code (routes, components, API clients or stores). Delete the bullets you did not work on and keep the rest as your contribution list.

> Note: the repository has several contributors (git history shows 8+ authors), so this is an inventory of the product, not of one person's work.

## Home page

- **Hero and app promotion**: a hero section with app-download promotion and showcase components introduces the platform and drives users to the mobile apps.
- **Menu options grid**: a home menu gives quick access to the main sections (Quran, Hadith, prayer times, Dua, Masail and others).
- **Live prayer times on the home page**: the home page shows prayer times for the visitor, added in changelog 0.2.9.
- **"Today" section**: today's verse, Hadith, Dua and Masail cards surface fresh daily content on the landing page.
- **Platform stats and testimonials**: a stats section and a testimonials section present usage highlights and social proof.
- **Marriage and Masail entry points**: dedicated home sections promote the Muslim marriage service and the ask-a-question flow.
- **Home search modal**: a search modal on the home page lets users search across content from the first screen.

## Prayer times, calendar and Ramadan

- **World prayer times by country and city**: clean URLs of the form `/world/{countryCode}/{city}` show prayer times for a chosen location.
- **Location validation and fallback**: the middleware checks that the country and city exist in the location API and redirects invalid URLs to Dhaka, Bangladesh.
- **Client-side prayer time calculation**: times are calculated in the browser with the `adhan` library, with the calculation logic split into its own util and hooks files.
- **Location selector**: a searchable country and city selector (keyboard accessible) lets users switch location, with country and city data cached in the browser and refetched only when stale.
- **Manual time adjustment**: users can adjust prayer times (offsets) and Hijri date, persisted via a dedicated Zustand store.
- **Hijri date with adjustment**: the Hijri date shown on the site honours a server-provided date adjustment.
- **Asr warning dialog**: a warning dialog explains Asr time considerations, and a sunset warning message is shown near Maghrib.
- **Forbidden times and Nafl sections**: the prayer page lists makruh (forbidden) prayer times and optional (Nafl) prayer times.
- **Ramadan timetable**: `/ramadan/{countryCode}/{city}` shows Sehri and Iftar times with its own time adjustment.
- **Prayer calendar**: `/salat-calender/{countryCode}/{city}` provides a month-based calendar of prayer times.
- **Masjid iqamah promotion on prayer pages**: prayer pages link to local mosque iqamah times.

## Quran

- **Sura list and reader**: all suras are listed and each sura has its own page, statically generated from the sura list at build time.
- **Para (Juz) navigation**: a sidebar toggles between sura and para lists for navigation.
- **Multiple translations**: readers can select translators (the default Bangla translation is Taqi Usmani), driven by a Zustand translation store.
- **Word-by-word mode**: the verse API supports word-by-word translation.
- **Audio recitation**: per-ayah and full recitation with a choice of reciters, a player modal, and Bangla-translation-with-recitation playback.
- **Verse and tafsir pages**: individual verse pages and tafsir pages per verse (`/sura/{id}/verse/{id}` and `/sura/{id}/tafsir/{id}`).
- **Quran search**: a search route for finding verses, with a verse search card list.
- **Quran topics**: topic-based browsing of verses (`/topic-ayat/{id}`) with topic cards and lists.
- **Reader settings sidebar**: a settings sidebar for translator selection and reading preferences.
- **Bookmarking verses**: verses can be bookmarked with tags (see Bookmarks below).

## Hadith

- **Hadith books**: a list of Hadith books with book descriptions, chapters and per-chapter title pages.
- **Hadith detail pages**: each Hadith has its own route (`/hadith/{id}`).
- **Narrators (Rabi)**: narrator list and narrator detail pages, plus Hadith by narrator.
- **Topic-based Hadith**: topic books with categories and subcategories, statically generated.
- **Hadith search**: a search result page for Hadith.
- **Hadith audio**: a Zustand store and components support audio playback for Hadith.
- **User Hadith correction**: an API client for submitting corrections to Hadith text.

## Dua and Sunnah

- **Dua list, categories and detail**: browse Duas by category with detail pages (`/dua/{id}/{title}`), fetched server-side.
- **Dua audio and Arabic/Bangla text**: Dua list and detail cards include audio playback and show Arabic text with Bangla meaning.
- **Sunnah section**: Sunnah categories and Sunnah detail pages under `/others/sunnah`.

## Today's Amal (daily content and quiz)

- **Daily content page**: `/today` shows the day's Quran verse, Hadith, Dua, Masail and more as tabs, with Gregorian, Hijri and Arabic dates formatted in Bangla.
- **Daily topic banner**: a banner shows the topic of the day.
- **Daily audio**: audio buttons for the day's ayah, Hadith and Dua, coordinated so only one plays at a time, with reciter selection.
- **Quiz tab**: a daily quiz with exam data cached in IndexedDB so repeat visits within the same exam period skip the API.
- **Leaderboard, history and profile tabs**: users can see rankings, past results and their own quiz profile.
- **Copy and share bar**: daily content can be copied or shared.
- **Notification image bundle**: daily notification images are downloaded as a monthly zip from the CDN, unpacked in the browser (`fflate`) and cached in IndexedDB.

## Masail (Islamic Q&A)

- **Masail list and categories**: browse Masail by topic category, with server-side category data.
- **Masail detail pages**: SEO-friendly detail URLs (`/masail/{id}/{subtitle}`) with sanitised HTML answers.
- **Pinned content**: pinned posts and Masail are highlighted in lists.
- **Ask a Masail**: signed-in users can submit questions from `/user/ask-masail`, with a floating ask-question button across the site and a user "my Masail" list.
- **Masail comments and bookmarks**: Masail pages have a comment store and bookmarkable answers.

## Articles and blogs

- **Articles list with search**: paginated article list with a search box (including voice search), category sidebar and pinned articles.
- **Article detail**: article page with header, share modal, left sidebar, related articles and print support.
- **Article categories, tags and writers**: pages by tag (`/articles/tag/...`), by writer, and a writers directory.
- **Blogs and blog writers**: a separate blog section with its own list, detail, writer directory and per-writer pages.
- **Comments with replies**: a threaded comment component with reply, delete confirmation, skeleton and fallback states.
- **Article bookmarks**: a bookmark store and a `/bookmarks` page for saved articles.
- **View counting**: page views for articles are counted through Redis-backed server actions.

## Books and authors

- **Book catalogue**: book list, book detail, book categories and book-by-author pages.
- **Author directory**: authors list with search and per-author detail pages.
- **In-browser PDF reader**: a dynamically loaded PDF viewer (react-pdf / pdfjs) for reading books and reports in the browser.
- **PDF proxy route**: `/api/proxy-pdf` proxies PDFs so they can be rendered client-side.

## Lectures and speakers

- **Latest and trending lectures**: dedicated lecture lists for latest and trending content.
- **Lecture detail with video**: lecture pages with YouTube thumbnails and video utilities.
- **Speaker directory and profile**: speakers list, speaker detail and lectures by speaker.

## Prayer learning (Namaz Shikkha) and purity

- **Learn Salat books**: two Namaz Shikkha books are readable on the site (`/others/learn-salat?type=one|two`), with a table of contents and article pages.
- **Offline-style bundled content**: the two source SQLite databases in the repo root were converted to static JSON and imported at build time, so pages render without a runtime database or fetch.
- **Taharat (purity) section**: pages for purification rules and details.
- **Salat menu**: a Salat learning landing page linking the sections.

## Hajj and Umrah

- **Hajj guide**: a guide with steps, rules, Dua, Masala, Rawda visit and a visual timeline.
- **Step checklist with saved progress**: a checklist per Hajj type whose progress is saved in the browser.
- **Do's and Don'ts tables**: tabular guidance for Hajj.
- **Hajj type selector**: switch between Hajj types in the guide.
- **Dual clock**: a Mecca time clock shown next to local time.
- **Hajj and Umrah picture guides and Masail**: picture-based Hajj and Umrah walkthroughs and a Hajj Masail page.

## Zakat and donations

- **Zakat calculator**: an interactive calculator with metal entries, Bajus gold purity price data, nisab modal and Zakat masail.
- **Zakat fund and recipients**: pages for online Zakat, the Zakat fund, recipients of Zakat and land purchase.
- **Donation page**: general donation page, plus a "my donations" page for signed-in users.
- **Online payments**: payments start via the payment API (bKash hosted checkout; a Nagad request is also defined), then return to success and error pages.
- **Monthly report**: monthly PDF reports are listed and viewable in the browser.

## Online shop (Products)

- **Product catalogue**: all products, categories, category pages, featured products and a search page with a "vanish input" placeholder animation.
- **Product detail and reviews**: product pages with a rating component and product reviews.
- **Cart and wishlist**: client-side cart store (persisted) and wishlist page.
- **Coupon / promo codes**: a coupon handler backed by a promo API.
- **Checkout and payment**: checkout page with success, fail and cancel return pages.
- **Invoices**: invoice pages with PDF and image download (jsPDF, html-to-image) and a user order history.
- **Shop policy pages**: pricing policy, return policy, terms and sell goals.
- **Grid/list view toggle**: product views can be switched via a view-mode provider.

## Masjid tools

- **Masjid iqamah time**: search a masjid by name or address and view its monthly iqamah schedule.
- **Create and request a masjid**: authorised users can create a masjid, and others can request one be added.
- **Update iqamah time**: a dialog for updating prayer times, with confirmation.
- **Masjid signage dashboard**: `/signage` has masjid profile, contents, display zones, prayer times and settings for managing mosque TV displays.
- **Signage content management**: content modal and workspace with image upload fields and list/option hooks.
- **Mosque TV app landing page**: `/mosque` presents the Android TV app for mosques.

## Islamic names

- **Islamic name browser**: browse Islamic names by category or by starting letter, search names, and copy a chosen name.

## Search

- **Unified search**: `/combined` searches across content types with tabs and server pagination, and can be switched into a maintenance mode via a flag.
- **Google search proxy**: a Google-search API client and shared Google search components.
- **Voice search**: a voice search button with silence detection for hands-free queries.
- **Debounced inputs and URL state**: search and filter state is kept in the URL with `nuqs`.

## User accounts and personalisation

- **OTP login**: email or phone OTP login via dialog, plus a login page and an intercepted modal route (`@modal/(login)`).
- **Protected routes**: middleware redirects unauthenticated users from `/user`, `/profile` and `/masjid-iqamah-time` to login with a `next` return path.
- **In-app single sign-on**: opening the site with an `app_id` and `token` in the URL verifies the user and sets cookies, so the web view of the mobile apps is already signed in.
- **Profile page**: profile details and image.
- **Bookmarks with tags**: a unified bookmark store with tags, soft delete, backup and restore.
- **Role guard**: a role guard component and hook gate features by user role.
- **Session expiry page**: `/expire-user` clears cookies and handles expired sessions.
- **Realtime notifications**: a Socket.IO provider connects signed-in web users to the notification server (disabled inside the app).

## Platform, app integration and admin

- **App-aware layout**: header, footer, download modal, support button and notice banner are hidden when the page runs inside the Android or iOS app.
- **Notice banners**: targeted notices filtered by screen type, country and device type.
- **App download pages**: a download page and a Windows download guide with step-by-step screenshots.
- **App permissions page**: explains why the app needs notification, location, phone, microphone and other permissions.
- **Customer support button**: a floating support button and a `/support` page with category-based contact numbers by country.
- **Cache revalidation panel**: `/user/revalidate` (login-gated) lets staff revalidate cached sections by tag, backed by `/api/revalidate`.
- **Muslim marriage handoff**: `/marriage` redirects a token-bearing user to the separate Muslim marriage site.
- **Privacy policy and terms pages**: legal pages.
- **Storage reset utilities**: `/clear-storage/local` and `/clear-storage/cookie` for support-led troubleshooting.

## Typography, i18n and content rendering

- **Bangla and Arabic font stack**: Kalpurush (Bangla), Tajawal and Uthmanic (Arabic), and Noorehuda are loaded locally with `next/font` and exposed as CSS variables.
- **Arabic-Bangla-English text components**: shared components render mixed-script text with the right font per script.
- **Bangla dates and numerals**: Bangla calendar (`date-bengali-revised`), Hijri libraries and `toLocaleDateString("bn")` formatting.
- **Safe HTML rendering**: an `HtmlRenderer` sanitises and renders API-provided HTML with DOMPurify, including Arabic font fixes.
- **Adjustable font size**: a custom font-size control for reading pages.

## Performance and SEO

- **Per-page metadata**: a shared `createMetadata` helper provides titles, descriptions and canonical URLs; most pages use `generateMetadata`.
- **Sitemap and robots**: generated `sitemap.xml` and `robots.txt` route handlers.
- **Time-based revalidation**: pages revalidate hourly (some every 60 seconds), with tagged fetch caching.
- **Redis response cache**: server-side GET responses can be cached in Redis with short, medium and long TTLs.
- **Predictive prefetching**: `js.foresight` links prefetch routes based on cursor intent.
- **Loading UX**: top loader, skeletons, Suspense fallbacks and loading routes.
- **Analytics**: Google Analytics and PostHog.
