# Game Hub â€” Project Specification

**Version:** 1.1
**Last Updated:** December 31, 2025
**Project Type:** Web Application
**Goal:** Learning/Portfolio â†’ Personal Tool â†’ Public Product

---

## Executive Summary

Game Hub is a web-based game library aggregator that unifies collections across PC storefronts (Steam, Epic, GOG) and console ecosystems (PlayStation, Xbox) into a single, searchable interface. Unlike existing solutions, Game Hub combines **automatic account sync** (eliminating manual entry) with **social features** (reviews, lists, friend activity) â€” filling the gap between desktop launchers (Playnite, GOG Galaxy) and manual trackers (Backloggd).

**Core Value Proposition:**
"Everything you love about Backloggd, but your library imports automatically."

---

## Part 1: Product Requirements

### 1.1 Target Users

**Primary:** Multi-platform gamers (PC + console) who:

- Own games across 3+ storefronts/platforms
- Want to track their backlog, completion status, and playtime
- Are frustrated by manual entry on existing platforms
- Value social features (sharing collections, seeing what friends play)

**Secondary:**

- Achievement hunters wanting cross-platform trophy aggregation
- Deal hunters who want price alerts on wishlisted games
- Gaming communities organizing shared challenges or "book clubs"

### 1.2 Problems Solved

| Problem                          | Current Solutions                    | Game Hub Approach                   |
| -------------------------------- | ------------------------------------ | ----------------------------------- |
| Games scattered across platforms | GOG Galaxy, Playnite (desktop only)  | Web-based, accessible anywhere      |
| Manual entry is tedious          | Backloggd requires typing every game | Auto-sync from connected accounts   |
| No mobile access to library      | Desktop apps require PC              | Responsive web + future PWA         |
| Console integration is weak      | Most tools ignore PlayStation/Xbox   | PSN + Xbox integration from Phase 2 |
| "What should I play next?"       | No smart recommendations             | AI recommendations in Phase 3       |
| Duplicate games across platforms | Limited duplicate detection          | Automatic duplicate flagging        |

### 1.3 Feature Specification

#### Library Management

| Feature                  | Description                                                           | User Interaction                                           |
| ------------------------ | --------------------------------------------------------------------- | ---------------------------------------------------------- |
| **Multi-platform sync**  | Connect Steam, Epic, GOG, PlayStation, Xbox accounts                  | OAuth flow â†’ authorize â†’ library imports automatically |
| **Unified library view** | All games in one searchable, filterable list                          | Grid/list view toggle, search bar, filter sidebar          |
| **Status tracking**      | Mark games as: Playing, Backlog, Completed, Abandoned, 100%, Wishlist | Click game â†’ status dropdown or quick-action buttons     |
| **Duplicate detection**  | Flag when same game owned on multiple platforms                       | Badge on game card: "Owned on 2 platforms"                 |
| **Manual game entry**    | Add games not in connected accounts                                   | Search IGDB database â†’ add to collection                 |
| **Playtime display**     | Show hours played (synced from platforms)                             | Displayed on game detail page                              |
| **Platform tags**        | Show which platforms you own each game on                             | Platform icons on game cards                               |

#### Game Details & Metadata

| Feature             | Description                                     | Data Source                                       |
| ------------------- | ----------------------------------------------- | ------------------------------------------------- |
| **Cover art**       | High-quality game artwork                       | IGDB / SteamGridDB                                |
| **Game info**       | Description, release date, developer, publisher | IGDB API                                          |
| **Genres & tags**   | Categorization for filtering                    | IGDB + user custom tags                           |
| **Ratings**         | Community average rating                        | Aggregated from Game Hub users                    |
| **Completion time** | How long to beat (main/extras/completionist)    | HowLongToBeat (if API available) or user-reported |

#### Social Features

| Feature               | Description                                         | User Interaction                                  |
| --------------------- | --------------------------------------------------- | ------------------------------------------------- |
| **Public profiles**   | Shareable profile URL showing collection & stats    | `/u/username` â€” toggle public/private           |
| **Follow system**     | Follow friends to see their activity                | Follow button on profiles                         |
| **Activity feed**     | See what friends are playing, completing, reviewing | Home page feed (like Backloggd)                   |
| **Reviews & ratings** | Rate games (5-star), write reviews                  | Game detail page â†’ rate/review section          |
| **Lists**             | Create custom, shareable collections                | "Top 10 RPGs", "2025 Backlog", ranked or unranked |
| **Comments**          | Comment on reviews and lists                        | Threaded comments under content                   |

#### Discovery & Organization

| Feature              | Description                                                  |
| -------------------- | ------------------------------------------------------------ |
| **Filters**          | By platform, status, genre, release year, playtime, rating   |
| **Sort options**     | Alphabetical, recently added, playtime, rating, release date |
| **Custom tags**      | User-created tags for personal organization                  |
| **Search**           | Full-text search across game titles in your library          |
| **Trending/Popular** | See what's popular in the Game Hub community                 |

#### Data & Privacy

| Feature               | Description                                      |
| --------------------- | ------------------------------------------------ |
| **CSV export**        | Download your entire library as CSV              |
| **Data portability**  | Export all data (games, ratings, reviews, lists) |
| **Privacy controls**  | Choose what's public vs. private on profile      |
| **Account unlinking** | Disconnect platforms, delete synced data         |

---

## Part 2: Technical Requirements

### 2.1 Tech Stack

| Layer              | Technology                   | Version           | Rationale                                            |
| ------------------ | ---------------------------- | ----------------- | ---------------------------------------------------- |
| **Frontend**       | Next.js (App Router) + React | 16.1 + React 19.2 | SSR, Turbopack default, Cache Components             |
| **Styling**        | Tailwind CSS + shadcn/ui     | 4.1               | CSS-first config, 5x faster builds, text shadows     |
| **Backend**        | Next.js API Routes           | 16.1              | Simplicity for MVP, can scale                        |
| **Database**       | Supabase (PostgreSQL)        | Latest            | Relational data, built-in storage, free tier         |
| **ORM**            | Prisma                       | 7.2               | Rust-free, 3.4x faster queries, TypeScript config    |
| **Authentication** | Better Auth                  | 1.4.9             | Email + password, plugin ecosystem, Prisma adapter   |
| **Game Metadata**  | IGDB API (Twitch)            | Latest            | Largest free game database                           |
| **Hosting**        | Vercel                       | Latest            | Free tier, native Next.js 16 support                 |
| **File Storage**   | Supabase Storage             | Latest            | User avatars, custom covers (included with Supabase) |
| **Email**          | Resend                       | Latest            | Transactional email (verification, password reset)   |
| **Payments**       | Stripe                       | Latest            | Future monetization (not needed for MVP)             |
| **AI Models**      | Anthropic Claude             | Latest            | For "what to play next" recommendations (Phase 3)    |

**Key Version Notes:**

- **Next.js 16.1** â€” Turbopack is now the default bundler (2-5x faster builds), React 19.2 with View Transitions
- **Tailwind CSS 4.1** â€” CSS-first configuration (no `tailwind.config.js`), text shadows, masks
- **Prisma 7.2** â€” Rust-free architecture, uses TypeScript `prisma.config.ts`, 90% smaller bundle
- **Better Auth 1.4** â€” Stateless auth option, SCIM support, optimized database queries
- **React 19.2** â€” View Transitions, `useEffectEvent`, Activity components

### 2.2 Platform API Integration

| Platform        | API Type                      | Auth Method                  | Data Available                               | Difficulty               |
| --------------- | ----------------------------- | ---------------------------- | -------------------------------------------- | ------------------------ |
| **Steam**       | Official Public API           | API Key + Steam ID           | Owned games, playtime, achievements, friends | âœ… Easy                 |
| **GOG**         | Unofficial (gogapidocs)       | OAuth refresh tokens         | Owned games, achievements                    | âš ï¸ Medium             |
| **Epic Games**  | Reverse-engineered            | OAuth (breaks sometimes)     | Owned games only                             | âš ï¸ Medium             |
| **Xbox**        | XAPI.us (paid) or reverse-eng | OAuth / paid API key         | Games, achievements, gamerscore              | âš ï¸ Medium             |
| **PlayStation** | psn-api (unofficial)          | NPSSO token (manual refresh) | Games, trophies, playtime                    | âš ï¸ Medium             |
| **Nintendo**    | Complex reverse-eng           | Multi-step OAuth             | Games only (no achievements system)          | âŒ Hard â€” deprioritize |

**API Key Strategy:**

- Steam: Users provide their own API key (free, easy)
- Others: Server-side OAuth with encrypted token storage + clear privacy disclosure

### 2.3 Database Schema (Prisma 7)

**Note:** Prisma 7 uses `prisma.config.ts` for configuration. The schema below goes in `schema.prisma`.

```prisma
// ==================== USERS & AUTH ====================

model User {
  id            String    @id @default(cuid())
  email         String    @unique
  username      String    @unique
  avatarUrl     String?
  bio           String?
  isPublic      Boolean   @default(true)
  createdAt     DateTime  @default(now())
  updatedAt     DateTime  @updatedAt

  // Relations
  linkedAccounts LinkedAccount[]
  gameEntries    GameEntry[]
  lists          List[]
  activities     Activity[]
  followers      Follow[]  @relation("following")
  following      Follow[]  @relation("follower")
}

model LinkedAccount {
  id             String   @id @default(cuid())
  userId         String
  platform       Platform
  platformUserId String
  accessToken    String   // encrypted
  refreshToken   String?  // encrypted
  lastSynced     DateTime?
  createdAt      DateTime @default(now())

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@unique([userId, platform])
  @@unique([platform, platformUserId])
}

enum Platform {
  STEAM
  EPIC
  GOG
  XBOX
  PSN
}

// ==================== GAMES ====================

model Game {
  id          String   @id @default(cuid())
  igdbId      Int      @unique
  steamAppId  Int?     @unique
  title       String
  coverUrl    String?
  releaseDate DateTime?
  summary     String?
  genres      String[] // PostgreSQL array
  platforms   String[] // PostgreSQL array
  igdbData    Json?    // Full IGDB response for flexibility
  cachedAt    DateTime @default(now())

  // Relations
  gameEntries GameEntry[]
  listItems   ListItem[]
  activities  Activity[]
}

// ==================== USER LIBRARY ====================

model GameEntry {
  id             String      @id @default(cuid())
  userId         String
  gameId         String
  status         GameStatus
  ownedPlatforms Platform[]  // Which platforms user owns it on
  playtimeMinutes Int        @default(0)
  rating         Int?        // 1-5
  review         String?
  addedAt        DateTime    @default(now())
  completedAt    DateTime?
  lastPlayedAt   DateTime?

  user User @relation(fields: [userId], references: [id], onDelete: Cascade)
  game Game @relation(fields: [gameId], references: [id])

  @@unique([userId, gameId])
  @@index([userId, status])
}

enum GameStatus {
  PLAYING
  BACKLOG
  COMPLETED
  HUNDRED_PERCENT
  ABANDONED
  WISHLIST
}

// ==================== LISTS ====================

model List {
  id          String   @id @default(cuid())
  userId      String
  title       String
  description String?
  isRanked    Boolean  @default(false)
  isPublic    Boolean  @default(true)
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt

  user  User       @relation(fields: [userId], references: [id], onDelete: Cascade)
  items ListItem[]
}

model ListItem {
  id       String  @id @default(cuid())
  listId   String
  gameId   String
  position Int
  notes    String?

  list List @relation(fields: [listId], references: [id], onDelete: Cascade)
  game Game @relation(fields: [gameId], references: [id])

  @@unique([listId, gameId])
  @@index([listId, position])
}

// ==================== SOCIAL ====================

model Follow {
  id          String   @id @default(cuid())
  followerId  String
  followingId String
  createdAt   DateTime @default(now())

  follower  User @relation("follower", fields: [followerId], references: [id], onDelete: Cascade)
  following User @relation("following", fields: [followingId], references: [id], onDelete: Cascade)

  @@unique([followerId, followingId])
}

model Activity {
  id        String       @id @default(cuid())
  userId    String
  type      ActivityType
  gameId    String?
  listId    String?
  metadata  Json?        // Flexible data based on type
  createdAt DateTime     @default(now())

  user User  @relation(fields: [userId], references: [id], onDelete: Cascade)
  game Game? @relation(fields: [gameId], references: [id])

  @@index([userId, createdAt])
}

enum ActivityType {
  ADDED_GAME
  COMPLETED_GAME
  REVIEWED_GAME
  CREATED_LIST
  UPDATED_LIST
}
```

**Status Values:**

- `PLAYING` â€” Currently playing
- `BACKLOG` â€” Want to play
- `COMPLETED` â€” Beat main content
- `HUNDRED_PERCENT` â€” 100% completion / all achievements
- `ABANDONED` â€” Stopped, won't return
- `WISHLIST` â€” Don't own, want to buy

### 2.4 Key API Endpoints

```
Authentication:
POST   /api/auth/[...nextauth]     â€” NextAuth handlers
GET    /api/auth/steam/callback    â€” Steam OAuth callback
GET    /api/auth/psn/callback      â€” PSN OAuth callback

User:
GET    /api/users/:username        â€” Public profile
PATCH  /api/users/me               â€” Update profile
GET    /api/users/me/stats         â€” User statistics

Library:
GET    /api/library                â€” User's game library (filterable)
POST   /api/library/sync           â€” Trigger sync from connected accounts
POST   /api/library/games          â€” Manually add a game
PATCH  /api/library/games/:id      â€” Update status, rating, review
DELETE /api/library/games/:id      â€” Remove from library

Games:
GET    /api/games/search           â€” Search IGDB for games
GET    /api/games/:id              â€” Game details + community stats

Social:
GET    /api/feed                   â€” Activity feed from followed users
POST   /api/follow/:userId         â€” Follow a user
DELETE /api/follow/:userId         â€” Unfollow
GET    /api/lists                  â€” User's lists
POST   /api/lists                  â€” Create list
GET    /api/lists/:id              â€” List details

Export:
GET    /api/export/csv             â€” Download library as CSV
GET    /api/export/json            â€” Download all user data as JSON
```

### 2.5 External Services

| Service         | Purpose                                            | Cost                                    |
| --------------- | -------------------------------------------------- | --------------------------------------- |
| **Supabase**    | Database (PostgreSQL) + File Storage               | Free tier (500MB DB, 1GB storage)       |
| **IGDB API**    | Game metadata, cover art, search                   | Free (Twitch account required)          |
| **SteamGridDB** | Alternative/custom cover art                       | Free                                    |
| **XAPI.us**     | Xbox API access                                    | Â£5-50/month (or reverse-engineer free) |
| **Resend**      | Transactional email (verification, password reset) | Free tier (100 emails/day)              |
| **Vercel**      | Frontend hosting                                   | Free tier for MVP                       |
| **Stripe**      | Payments (future)                                  | % per transaction                       |

---

## Part 3: Milestones

### MVP (Version 1.0) â€” "It Works"

**Goal:** Core library sync + basic tracking. Prove the concept.

**Features:**

- [ ] User authentication (email + password)
- [ ] Connect Steam account (OAuth for library sync, not login)
- [ ] Steam library auto-sync (owned games + playtime)
- [ ] Manual game search & add (via IGDB)
- [ ] Status tracking (Playing, Backlog, Completed, Abandoned, Wishlist)
- [ ] Basic library view with search & filters
- [ ] Game detail page with metadata
- [ ] Simple public profile (`/u/username`)
- [ ] CSV export
- [ ] Responsive design (mobile-friendly)

**Not in MVP:**

- Other platform integrations (Epic, GOG, PlayStation, Xbox)
- Reviews, ratings, lists
- Social features (follows, feed)
- Achievements

**Success Criteria:**

- Can sign in with Steam
- Library syncs within 30 seconds
- Can filter/search 500+ game library without lag
- Works on mobile browser

**Estimated Timeline:** 4-6 weeks

---

### Version 2.0 â€” "Social & Multi-Platform"

**Goal:** Add social layer + more platform integrations.

**Features:**

- [ ] Epic Games Store sync
- [ ] GOG sync
- [ ] PlayStation Network sync (trophies optional)
- [ ] Xbox sync (achievements optional)
- [ ] Duplicate detection across platforms
- [ ] 5-star ratings
- [ ] Written reviews (with spoiler tags)
- [ ] Follow system
- [ ] Activity feed (home page)
- [ ] User lists (custom collections)
- [ ] Notifications (follows, comments)

**Success Criteria:**

- 3+ platforms can be connected
- Activity feed loads < 2 seconds
- Users creating & sharing lists

**Estimated Timeline:** 6-8 weeks after MVP

---

### Version 3.0 â€” "Differentiation"

**Goal:** Features that set Game Hub apart from competitors.

**Features:**

- [ ] AI-powered "What should I play next?" recommendations
- [ ] Completion time integration (HLTB or user-reported)
- [ ] Deal alerts (IsThereAnyDeal / GG.deals integration)
- [ ] Subscription tracking (Game Pass, PS Plus â€” show "free with subscription")
- [ ] Cross-platform achievement aggregation view
- [ ] "Friends who own this game" for co-op planning
- [ ] PWA support with push notifications
- [ ] Community challenges / game clubs
- [ ] Advanced stats page (genres played, completion rate, etc.)

**Estimated Timeline:** Ongoing after V2

---

## Part 4: Design Principles

### User Experience

1. **Sync should be magic** â€” Connect account, see games. No manual work.
2. **Fast by default** â€” Library with 1000+ games must feel instant.
3. **Mobile-first responsive** â€” Many users will browse on phones.
4. **Minimal friction** â€” Status updates should be 1-2 clicks max.

### Technical

1. **Reliability over features** â€” Better to sync 3 platforms perfectly than 6 poorly.
2. **Graceful degradation** â€” If an API breaks, show cached data, don't crash.
3. **Privacy-conscious** â€” Encrypt tokens, clear data policies, easy account deletion.
4. **Data portability** â€” Users can always export everything.

### Competitive Positioning

- **vs. Playnite/GOG Galaxy:** Web-based, no install, mobile access, social features
- **vs. Backloggd:** Automatic sync instead of manual entry
- **vs. Infinite Backlog:** Better UI, faster development, AI features

---

## Part 5: Decisions Made

1. **Authentication strategy:** Email + password for MVP. Social login (Steam, Google) can be added later.
2. **Monetization:** None for now. Future consideration.
3. **Platform priority:** Steam (MVP) â†’ Epic â†’ GOG â†’ Xbox â†’ PlayStation â†’ Nintendo (last/maybe never)
4. **Hosting:** Vercel (free tier)
5. **Name:** Game Hub

---

## Appendix: Research Sources

- GOG Galaxy 2.0: https://www.gogalaxy.com/en/
- Playnite: https://playnite.link/
- Backloggd: https://backloggd.com/
- Infinite Backlog: https://infinitebacklog.net/
- Steam Web API: https://steamcommunity.com/dev
- IGDB API: https://api-docs.igdb.com/
- psn-api: https://github.com/achievements-app/psn-api
- GOG unofficial API docs: https://gogapidocs.readthedocs.io/

---

_This document should be updated as decisions are made and requirements evolve._
