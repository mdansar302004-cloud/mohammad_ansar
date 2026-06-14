## 1. Product Overview

NovaPlay is an online gaming portal delivering browser-playable games, competitive leaderboards, and community tournaments for casual and competitive players. It combines a Miniclip-style game library with lightweight social and esports features — saved games, achievements, rankings, and scheduled tournament play.

**Target users:**
- Casual gamers looking for quick, browser-playable titles across genres
- Competitive players tracking leaderboard rank and tournament standings
- Tournament organizers and admins managing game listings, schedules, and prize pools

## 2. Visual & Brand Direction

Energetic, arcade-modern aesthetic with high-contrast dark surfaces and neon accent highlights; dense game grids balanced by generous card spacing.

### Color System

- **Void** `#0F0F1A` Primary page background
- **Surface** `#1A1A2E` Card and panel backgrounds
- **Cloud** `#F4F4F8` Inverted text on light surfaces
- **Neon** `#7C3AED` Primary CTAs, active nav state, badges
- **Pulse** `#22D3EE` Secondary accent for live indicators and highlights
- **Text** `#E5E7EB` Primary text on dark surfaces
- **Subtle** `#9CA3AF` Metadata, timestamps, secondary labels
- **Success** `#4ADE80` Confirmation toasts, online/live status
- **Border** `#2E2E45` Card outlines and dividers

Neon is reserved for a single primary CTA per viewport; Pulse is used only for "live" or "new" indicators, never as a CTA color.

### Typography

- **Space Grotesk** - headlines and game titles, weights 500, 600, 700.
- **Inter** - UI labels, navigation, body copy, weights 400, 500, 600.
- **JetBrains Mono** - scores, ranks, and stat callouts, weight 500.

Desktop 1920x1080 scale: H1 60/68 weight 700, H2 36/44 weight 600, H3 22/30 weight 600, body 16/24 weight 400, small 13/18 weight 400.

### Layout

12-column grid, 1440px max-width, 24px gutters. Header 72px fixed on scroll with shadow after 4px scroll offset. Sidebar: none on public pages; 240px left rail in dashboard and admin. Footer: four columns (Games, Community, Company, Connect).

## 3. Technical Ambition

### Core Stack

- Framework: Next.js (App Router, static + ISR-simulated via fixtures)
- Styling: Tailwind CSS
- State: Zustand for session, saved-games, and favorites store
- Animation: Motion
- Game embedding: sandboxed `<iframe>` player with postMessage score reporting
- Forms / validation: React Hook Form + Zod
- Routing: file-based Next.js routes
- Build / deploy: Netlify free tier
- Fonts: Fontsource self-hosted woff2
- Images: next/image with static imports
- Fixtures: per-entity JSON modules under `/data`
- Mock auth: localStorage token-string; mock user switcher in dev toolbar

### Performance Targets

Lighthouse 95+ (Performance, Accessibility, SEO). LCP 1.8s, CLS 0.05, INP 100ms p75, bundle 130kB gzipped initial route, 60fps scroll and animation.

## 4. Site Architecture & Page Specifications

### 4.1 Global Elements

Header: 72px, logo left, primary nav center, search icon, notification bell, and profile avatar right. Sticky with 1px Border bottom after scroll. Footer: four link columns; no legal copy. Toasts: bottom-right, auto-dismiss 4000ms. Skeleton: pulse placeholder on every async card grid. Landmark order: banner, nav, main, contentinfo.

### 4.2 Pages & Navigation Flows

**Primary nav:** Games, Leaderboards, Tournaments, Community
**Secondary nav:** Search icon, Notifications bell, Profile avatar
**Post-login default route:** `/dashboard`
**Unauthenticated redirect:** `/login`
**Role-gated nav items:** Admin sees 'Manage' in secondary nav

- **Home**
  - Path: `/`
  - Connects From: Logo, direct visit
  - Key Interactions: Genre filter chips, infinite scroll game grid, "Play Now" hero CTA, 'No games found' empty state on filter miss

- **Game Detail / Player**
  - Path: `/game/[slug]`
  - Connects From: Home cards, search results, leaderboard rows
  - Key Interactions: Embedded sandboxed game player, fullscreen toggle, favorite/save icon, related games carousel, "How to Play" controls panel

- **Genre / Category Page**
  - Path: `/category/[slug]`
  - Connects From: Primary nav Games dropdown, genre chips
  - Key Interactions: Filter by sub-genre, sort by popularity or release date, skeleton on load

- **Search**
  - Path: `/search`
  - Connects From: Header search icon
  - Key Interactions: Instant filter by keyword, genre, and rating; 'No results' empty state

- **Leaderboards**
  - Path: `/leaderboards`
  - Connects From: Primary nav, game detail page rank widget
  - Key Interactions: Global and per-game leaderboard tables, time-range filter (daily/weekly/all-time), highlight current user's row

- **Tournaments**
  - Path: `/tournaments/[slug]`
  - Connects From: Primary nav, home tournament banner
  - Key Interactions: Registration button, bracket view, prize pool and deadline display, countdown timer to next match

- **Dashboard**
  - Path: `/dashboard`
  - Connects From: Post-login redirect, nav profile menu
  - Key Interactions: Saved games list, achievements grid, tournament standings card, 'No saved games yet' empty state

- **Login**
  - Path: `/login`
  - Connects From: Profile icon (unauthenticated), "Join Tournament" CTA
  - Key Interactions: Email and password fields, 'Create account' link, inline errors

- **Admin Manage**
  - Path: `/admin/games`
  - Connects From: Admin nav item 'Manage'
  - Key Interactions: Game listing table (publish, feature, remove), tournament scheduler, leaderboard moderation panel

### 4.3 Interactive Elements Specification

- **Search modal:** triggered by header search icon; opens overlay 220ms cubic-bezier(0.4,0,0.2,1); input auto-focused; results stream as user types; close via close icon or Escape. Error: 'Enter at least 2 characters'.
- **Game player modal:** triggered by "Play Now" or game card click; loads sandboxed iframe with loading skeleton; fullscreen toggle button; exit via close icon or Escape; reports session score via postMessage on exit.
- **Tournament registration drawer:** triggered by "Register" button on tournament page; slides in from right 250ms cubic-bezier(0.22,1,0.36,1); displays entry fee (if any), rules summary, and confirm button; success toast 'You're registered!'.
- **Favorite/save toggle:** icon on game card and detail page fills with Neon color on click; fixture creates SavedGame record; toast 'Added to your library'.
- **Notification bell:** badge count updates on poll; dropdown lists recent leaderboard changes, tournament reminders, and achievement unlocks.

### 4.4 Real-time Simulation & Notifications

Leaderboard rank widget polled every 30s from fixture array; notification bell badge count polled every 60s; "players online now" counter on game cards refreshed every 20s via simulated fetch from JSON fixture.

## 5. Motion Language

Default transitions 200ms cubic-bezier(0.4,0,0.2,1). Route transitions: crossfade 180ms cubic-bezier(0.4,0,0.2,1). Stagger on game grids: 40ms per item, cubic-bezier(0.4,0,0.2,1). Hover on game cards: scale(1.03) with glow shadow 150ms cubic-bezier(0.22,1,0.36,1). Scroll-triggered fade-in on section entry 300ms cubic-bezier(0.16,1,0.3,1). `prefers-reduced-motion`: all durations to 0ms, no transforms.

## 6. Application Logic

### User Roles (UI gating)

1. **Visitor:** browse and play free games, view leaderboards, no saving or tournament entry.
2. **Player:** full game access, dashboard, saved games, achievements, tournament registration.
3. **Admin:** all Player surfaces plus game management, tournament scheduling, leaderboard moderation.

### Session UI Shell

Login and signup screens with email/password fields. Mock user switcher in dev toolbar cycles Visitor, Player, Admin. localStorage stores `{role, name, token}` JSON string. Session-timeout banner: 'Session expired, please log in again' after 30min idle.

### Sign-up / Sign-in UI Flow

User clicks 'Create account' on login page -> fills email, password, display_name -> 'Sign up' button -> simulated 1200ms delay -> success toast 'Welcome to NovaPlay' -> redirect to `/dashboard`.

### View Models & Fixtures

- **Game:** id, title, slug, description, thumbnail_url, embed_url, genre_id (id ref to Genre), rating, release_date
- **Genre:** id, name, slug, description, icon_name
- **Player:** id, display_name, slug, avatar_url, bio, joined_at
- **ScoreEntry:** id, player_id (id ref to Player), game_id (id ref to Game), score, recorded_at
- **Tournament:** id, name, slug, game_id (id ref to Game), prize_pool_cents, registration_deadline, start_at, status
- **Achievement:** id, name, description, icon_name, criteria
- **SavedGame:** id, player_id (id ref to Player), game_id (id ref to Game), saved_at
- **Plan:** id, name, price_cents, interval, feature_list, is_active

Fixtures stored as per-entity JSON modules under `/data`. Sample values reference the NovaPlay brand.

**Relationships:** Game belongs to Genre. ScoreEntry belongs to Player and Game. SavedGame belongs to Player and Game. Tournament belongs to Game. Plan has many Player.

### Main UI Flows

**Play Game (Visitor/Player):** User clicks game card -> game player modal opens with loading skeleton -> iframe loads embed_url -> on exit, postMessage reports session score -> if Player, fixture appends ScoreEntry -> toast 'Score saved!' (Player only).

**Join Tournament (Player):** Player clicks 'Register' on tournament page -> registration drawer opens -> reviews rules and prize pool -> clicks 'Confirm' -> simulated 1200ms delay -> fixture adds player to tournament roster -> success toast 'You're registered!' -> tournament appears in dashboard standings card.

**Manage Games (Admin):** Admin opens 'Manage' in admin nav -> views game table with genre and status filters -> clicks 'Edit' on a row -> drawer opens with title, genre dropdown, embed URL, and publish toggle -> clicks 'Save' -> simulated 1200ms delay -> fixture updates -> toast 'Game updated'.

### Checkout & Billing UI

Plan picker on `/dashboard` settings tab shows Free and Premium (ad-free) tiers. Selecting Premium opens mock payment form (card number, expiry, CVC). 'Subscribe' triggers 1200ms delay then confirmation screen with plan summary.

### Admin UI Surfaces

- Game management table (publish, feature, remove)
- Player list with role and plan filters
- Tournament scheduler with deadline and prize pool fields
- Leaderboard moderation log (flag/remove suspicious scores)

## 7. Accessibility & Quality

Contrast: 4.5:1 body text (Text on Void/Surface), 3:1 large text. Touch targets 44px minimum. Focus ring: 2px solid Pulse, 2px offset. ARIA labels on icon-only buttons (search, favorite, fullscreen, close). Form fields with associated labels. Live regions for toast notifications and notification bell updates. Reduced-motion: see Section 5.

## 8. Content & SEO

Semantic HTML: one `<h1>` per route. Open Graph: `og:image` 1200x630 per game, `og:site_name 'NovaPlay'`. Structured data: Organization (NovaPlay), VideoGame on game detail pages, Event on tournament pages. Robots.txt, sitemap.xml, canonical URLs on all pages; noindex on `/login`, `/admin/games`. Meta titles: `'{Game Title} | NovaPlay'`, `'Play Free Online Games | NovaPlay'`.
