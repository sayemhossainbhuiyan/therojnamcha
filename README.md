# The Rojnamcha

A digital literary journal, public archive and creative community. Black and white, quiet, editorial.
Visitors read; **chroniclers** (members) publish, rate and write correspondence; **archivists** (administrators)
curate, moderate and approve.

## What it does

- **Six portals**: Poetry, Literature, Journal, Cinema, Music, Personal Stories. Each has exactly two folders:
  an **Archivist** folder (administrators only) and a public contribution folder (Verses, Pages, Entries, Reels, Notes, Accounts).
- **Identities**: members are `name.chronicler`, administrators are `name.archivist`. The suffix comes from the
  stored role, never from what the browser sends. Anonymous work appears as plain `chronicler`.
- **Permanent archive**: members cannot delete posts. Every member has a public archive page (`/u/name`).
- **Ratings**: whole numbers 1 to 5, one per person per post, never on your own post. The member average is shown
  separately from archivist ratings. No likes or reactions.
- **Correspondence** (never called "reply"): members write; an archivist approves or rejects; only approved
  entries are public.
- **Administration** at `/admin` (archivists only): correspondence queue, accounts (promote / demote),
  statistics, audit log.
- **Search** at `/search`: titles, text and author names, with portal, folder, date and rating filters.

## How it is built

| Layer | Choice |
|---|---|
| Frontend | React 19, TypeScript, Vite, plain CSS (no UI framework) |
| Backend and database | Supabase: PostgreSQL, Auth, Storage |
| Hosting | Vercel (frontend) + Supabase (backend) |
| Tests | Vitest (code) and SQL scripts (database) |

```
Browser (React)  ──anon key──▶  Supabase
   │                              ├─ Auth: sign-up, email verification, sessions, password reset
   │                              ├─ Postgres: tables + Row Level Security + functions
   │                              └─ Storage: avatars, post-images
   └─ never holds a service-role key, never decides who is an administrator
```

**The key idea:** the browser is not trusted. Every rule that matters (who may publish where, who may approve,
who may change roles, who may delete) is enforced inside the database by Row Level Security policies and
`security definer` functions. Hiding a button is only decoration. For example, `/admin` shows "page not found"
to non-archivists, but the real protection is that each `admin_*` database function re-checks the caller's role.

### Folder map

```
src/
  App.tsx                 routes
  context/AuthContext.tsx session + profile
  lib/                    talking to Supabase (identity, posts, ratings, correspondence, admin, search)
  pages/                  one file per screen; pages/admin/ for the dashboard
  components/             shared pieces (cards, dialogs, route guards, panels)
  styles/                 tokens.css (colours, fonts) + one stylesheet per area
  data/portals.ts         the six portals
supabase/
  009_correspondence.sql  010_admin.sql  011_search.sql    (this stage's scripts; see supabase/README.md)
  tests/security_audit.sql        read-only checklist of database protections
  tests/live_break_in_test.sql    tries to break the rules, rolls everything back
tests/                    automated Vitest tests
docs/                     DEPLOYMENT.md, TESTING.md, SECURITY.md
```

## Running it on your own computer (Windows)

1. Install **Node.js** (the LTS version) from nodejs.org, and **VS Code**.
2. Open the `rojnamcha` folder in VS Code, then open a terminal (**Terminal → New Terminal**). It should show the project folder path.
3. `npm install` — downloads the libraries (takes a minute; ends with "added … packages").
4. Copy `.env.example` to a new file named `.env.local` and fill in the two values from Supabase
   (**Project Settings → API**: the Project URL and the `anon` public key). Never use the `service_role` key.
5. `npm run dev` — prints `Local: http://localhost:5173/`. Open that address.

| Command | What it does |
|---|---|
| `npm run dev` | runs the site locally |
| `npm run build` | type-checks and builds the production version into `dist/` |
| `npm test` | runs the automated tests (expect all green) |
| `npm run lint` | style and mistake checker |

## Database setup

Scripts are run in the Supabase **SQL Editor**, in order. See `supabase/README.md` for the order and for
how to keep your earlier scripts in the project.

## Documentation

- **docs/DEPLOYMENT.md** — putting it online, step by step, and what needs an account or a setting
- **docs/TESTING.md** — automated tests, database tests, and a manual checklist
- **docs/SECURITY.md** — the security model, administrator bootstrap, privacy of anonymity, and known gaps

## Honest status

Everything above is implemented. A few things from the original brief are **not** built yet; they are listed
in `docs/SECURITY.md` under "Known gaps" (for example hiding or editing individual posts from the dashboard,
and a content-reporting feature). Nothing here should be called "production-secure" until the checks in
`docs/TESTING.md` have been run against your live database and you have read the known gaps.
