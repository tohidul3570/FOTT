# Football On TV Today
Next.js 15 (App Router) · TypeScript · Tailwind 4 · Supabase · Vercel Cron · API-Football.

## Setup
1. `npm install`; copy `.env.example` to `.env.local` and fill in every value.
2. Supabase: run `supabase/migrations/001_init.sql` in the SQL editor. Create a user under Auth, then `insert into admin_users values ('<that user id>');`.
3. `npm run dev`; trigger the first import: `curl -H "Authorization: Bearer $CRON_SECRET" localhost:3000/api/cron/sync-fixtures`.
4. Deploy: push to GitHub, import in Vercel, add the same env vars. `vercel.json` schedules the sync daily at 03:00 UTC (Vercel sends `Authorization: Bearer $CRON_SECRET` automatically).

## How data works
- Fixtures/teams/competitions come from API-Football via `lib/sync.ts` (server-only, retries on 429, upserts by API fixture ID, keeps the original URL slug when a match is rescheduled).
- UK TV data lives separately in `broadcasters` + `fixture_broadcasts` and is never touched by sync. Nothing is invented: a match with no `confirmed` row shows "TV coverage not confirmed".
- Fixtures with `manual_override = true` are skipped by sync.
- Competitions are configured in the `competitions` table (`enabled`, `season`, `priority`, `api_id`).

## Admin
Sign in at `/admin` (Supabase Auth user listed in `admin_users`). Covers overview stats, manual sync, fixture search/correct/delete, broadcaster assignment, broadcasters, and competitions (enable, priority, SEO intro). Access is checked in middleware and again in every server action.

## Not built yet
Mobile hamburger menu, homepage extras (featured/weekend sections), logo upload, SEO settings editor, API usage display, a licensed UK TV listings adapter, shadcn/ui.
