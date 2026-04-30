# Trainer Website project

## What this is

An internal staff portal for F45 Crows Nest trainers. Public URL but gated by a simple name-entry login: trainer types their name, if it matches the allowlist, they're in. Staff content (coaching manual, schedules, daily class info, policies) lives behind that gate.

## Stack

- **Astro** (static site, content-first, simple)
- **Vercel** for hosting (free tier, deploys from GitHub)
- **Single serverless function** at `/api/auth` for name-gate login
- **HTTP-only cookie** session after successful name match
- **Allowlist** of trainer names stored as a Vercel environment variable, NOT committed to the repo

## Auth design

- Public landing page = name input form
- Submit → POST to `/api/auth` with name
- Server compares (case-insensitive, trimmed) to env var `TRAINER_NAMES` (comma-separated list)
- On match: set signed session cookie, redirect to dashboard
- On miss: friendly error message
- Session lasts 30 days (configurable)
- Logout clears the cookie

Security note: this is light protection (anyone who knows a trainer's name from F45's public Instagram could get in). Acceptable for internal staff content. Can be upgraded later (PIN, magic link, etc.).

## Source content

The parent folder `../` (F45 Crows Nest) contains existing content the website pulls from:

- `../Coaching Manual Scripts/` — coaching scripts
- `../Coaching Manual B-Roll/` — supporting media
- `../Trainers/` — trainer info
- `../Weekly Routine.md` — weekly cadence
- `../Premium Membership/` — premium content
- `../F45 Coaching Philosophy - Video Script.md` — philosophy doc

When pages reference this content, copy or import it into `src/content/` as markdown rather than reading the parent folder at runtime.

## First task

Scaffold the Astro project, set up the auth function skeleton, build a minimal login page + placeholder dashboard, push to GitHub, deploy to Vercel. Get a working public URL with the name gate functioning before adding content pages.

## Conventions

- Trainer's owner is **Jino** (Studio Manager). Owners of the gym are **Armand and Brigid**.
- Casual, manager-to-team tone in any user-facing copy.
- No mention of brand competitors or "72 Training" anywhere.
- Mobile-first design — trainers will mostly view this on phones between classes.
