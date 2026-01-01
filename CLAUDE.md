# Game Hub - AI Assistant Guide

## Project Overview

Game Hub is a web-based game library aggregator that unifies collections across PC storefronts (Steam, Epic, GOG) and console ecosystems (PlayStation, Xbox) into a single, searchable interface.

**Core Value Proposition:** "Everything you love about Backloggd, but your library imports automatically."

**Project Type:** Learning/Portfolio → Personal Tool → Public Product

---

## Tech Stack

| Layer | Technology | Version |
|-------|------------|---------|
| Framework | Next.js (App Router) | 16.1 |
| UI | React | 19.2 |
| Styling | Tailwind CSS + shadcn/ui | 4.1 |
| Database | Supabase (PostgreSQL) | Latest |
| ORM | Prisma | 7.2 |
| Authentication | Better Auth | 1.4.9 |
| Game Metadata | IGDB API (Twitch) | - |
| Email | Resend | Latest |
| Hosting | Vercel | - |

---

## Project Structure

```
game-hub/
├── app/                    # Next.js App Router
│   ├── (auth)/            # Auth routes (login, register)
│   ├── (main)/            # Main app routes
│   │   ├── library/       # User's game library
│   │   ├── games/[id]/    # Game detail pages
│   │   ├── u/[username]/  # Public profiles
│   │   └── lists/         # User lists
│   ├── api/               # API routes
│   │   ├── auth/          # Better Auth handlers
│   │   ├── library/       # Library CRUD
│   │   ├── games/         # Game search/details
│   │   └── sync/          # Platform sync endpoints
│   └── layout.tsx         # Root layout
├── components/
│   ├── ui/                # shadcn/ui components
│   ├── library/           # Library-specific components
│   ├── games/             # Game card, detail components
│   └── social/            # Profile, activity feed components
├── lib/
│   ├── auth.ts            # Better Auth config
│   ├── db.ts              # Prisma client
│   ├── igdb.ts            # IGDB API client
│   └── platforms/         # Platform sync adapters
│       └── steam.ts       # MVP: Steam only (Epic, GOG, Xbox, PSN in Phase 2)
├── prisma/
│   ├── schema.prisma      # Database schema
│   └── migrations/        # Database migrations
├── docs/                  # Project documentation
│   ├── architecture.md
│   ├── changelog.md
│   └── project-status.md
└── public/                # Static assets
```

---

## Key Conventions

### Code Style
- Use TypeScript strict mode
- Prefer `async/await` over `.then()` chains
- Use named exports for components, default exports for pages
- Colocate tests with source files (`*.test.ts`)

### Naming
- Components: PascalCase (`GameCard.tsx`)
- Utilities/hooks: camelCase (`useLibrary.ts`)
- API routes: kebab-case in URL, camelCase in code
- Database fields: camelCase (matches TypeScript)

### Component Patterns
- Use Server Components by default
- Add `'use client'` only when needed (interactivity, hooks)
- Prefer composition over prop drilling
- Use shadcn/ui components as base, customize via className

### Database
- Always use Prisma for database operations
- Run `pnpm prisma generate` after schema changes
- Run `pnpm prisma migrate dev` for migrations
- Use transactions for multi-table operations
- Use camelCase for schema fields (Prisma handles DB mapping)

### Authentication
- Better Auth handles all auth flows
- Access session via `auth()` in Server Components
- Access session via `useSession()` in Client Components
- Protected routes use middleware

---

## Git Workflow

### Branch Naming
- `feature/` — New features (`feature/steam-sync`)
- `fix/` — Bug fixes (`fix/login-redirect`)
- `chore/` — Maintenance, deps, config (`chore/update-deps`)
- `refactor/` — Code improvements (`refactor/auth-flow`)

### Commit Messages (Conventional Commits)
```
type(scope): description

feat(auth): add email verification
fix(library): correct playtime display
chore(deps): update prisma to 7.2
refactor(api): simplify game search endpoint
docs: update README with setup instructions
```

**Types:** `feat`, `fix`, `chore`, `refactor`, `docs`, `style`, `test`

### PR Process
1. Create feature branch from `main`
2. Make changes, commit with conventional format
3. Push branch, open PR
4. Merge to `main` when ready (squash recommended)
5. Delete branch after merge

---

## Common Commands

```bash
# Development
pnpm dev                 # Start dev server (Turbopack)
pnpm build               # Production build
pnpm lint                # Run ESLint
pnpm typecheck           # Run TypeScript check

# Database
pnpm prisma generate     # Generate Prisma client
pnpm prisma migrate dev  # Create migration
pnpm prisma studio       # Open Prisma Studio (DB browser)
pnpm prisma db push      # Push schema changes (no migration)
```

> **Note:** Testing will be added post-MVP. For now, focus on shipping features.

---

## Environment Variables

See `.env.example` for all required variables. Critical ones:
- `DATABASE_URL` - Supabase PostgreSQL connection string
- `BETTER_AUTH_SECRET` - Auth encryption key (min 32 chars)
- `TWITCH_CLIENT_ID` / `TWITCH_CLIENT_SECRET` - For IGDB API

---

## Current Development Phase

**MVP (Version 1.0)** - Core library sync + basic tracking

### MVP Features (in priority order):
1. ⏳ User authentication (email + password)
2. ⏳ Connect Steam account (OAuth for library sync)
3. ⏳ Steam library auto-sync (owned games + playtime)
4. ⏳ Manual game search & add (via IGDB)
5. ⏳ Status tracking (Playing, Backlog, Completed, Abandoned, Wishlist)
6. ⏳ Basic library view with search & filters
7. ⏳ Game detail page with metadata
8. ⏳ Simple public profile (`/u/username`)
9. ⏳ CSV export
10. ⏳ Responsive design (mobile-friendly)

### NOT in MVP:
- Other platform integrations (Epic, GOG, PlayStation, Xbox)
- Reviews, ratings, lists
- Social features (follows, feed)
- Achievements

---

## API Integration Notes

### Steam Web API
- Users provide their own API key (free from Steam)
- Endpoints: `IPlayerService/GetOwnedGames`, `ISteamUser/GetPlayerSummaries`
- Returns: owned games, playtime, last played
- Rate limits: Generous, no strict documentation

### IGDB API (Twitch)
- Uses Twitch OAuth (Client Credentials flow)
- Access token expires, cache and refresh as needed
- Primary source for game metadata, covers, genres
- Query language: Apicalypse (their custom format)

---

## Database Schema Overview

**Core Models:**
- `User` - Auth + profile data
- `LinkedAccount` - Connected platforms (Steam, Epic, etc.)
- `Game` - Game metadata (cached from IGDB)
- `GameEntry` - User's ownership + status for a game
- `List` / `ListItem` - Custom game collections
- `Follow` - Social connections
- `Activity` - Activity feed events

**Key Relationships:**
- User → many LinkedAccounts
- User → many GameEntries → Game
- User → many Lists → many ListItems → Game
- User → many Followers/Following (self-referential)

---

## Troubleshooting

### Prisma issues
```bash
# Reset everything
pnpm prisma migrate reset
pnpm prisma generate
```

### IGDB API 401 errors
- Access token expired, refresh it
- Check Twitch app credentials

### Better Auth session issues
- Clear cookies and re-login
- Check `BETTER_AUTH_SECRET` is set

---

## Resources

- [Project Spec](/docs/GameHub-Project-Spec.md)
- [Next.js 16 Docs](https://nextjs.org/docs)
- [Prisma Docs](https://www.prisma.io/docs)
- [Better Auth Docs](https://www.better-auth.com/docs)
- [IGDB API Docs](https://api-docs.igdb.com/)
- [shadcn/ui](https://ui.shadcn.com/)
