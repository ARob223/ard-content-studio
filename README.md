# ARD Content Studio

A phone-first content planning app for ARD Solutions. Plan Reels/TikTok, Instagram, Facebook, YouTube, and LinkedIn content, generate on-brand prompts, track posting, and earn badges as you ship.

## Hosting (Netlify via GitHub)

1. Push this repo to GitHub.
2. In Netlify: **Add new site → Import an existing project → GitHub**, pick this repo.
3. No build command needed. **Publish directory:** `/` (root). Deploy.
4. Netlify serves `index.html` at your site URL automatically.

Every push to the main branch will auto-deploy.

## Cloud sync (Supabase)

The app syncs each signed-in user's plan to Supabase so it works across phone and laptop.

Required one-time setup in the Supabase SQL Editor:

```sql
create table content_state (
  user_id uuid references auth.users not null primary key,
  data jsonb not null default '{}'::jsonb,
  updated_at timestamptz default now()
);

alter table content_state enable row level security;

create policy "own rows" on content_state
  for all using (auth.uid() = user_id) with check (auth.uid() = user_id);
```

Then in **Authentication → Providers → Email**, optionally turn off "Confirm email" for instant sign-in.

The Supabase project URL and anon (publishable) key are set near the top of the `<script>` in `index.html`. The anon key is safe to commit — row-level security keeps each account's data private.

## Local use

Open `index.html` in a browser. Without signing in it saves to that browser only; sign in to sync across devices.
