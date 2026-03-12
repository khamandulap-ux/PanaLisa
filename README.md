## Pana Lisa Website

Landing page for Pana Lisa social media services.

## Global reviews (Supabase)

The testimonials section now uses **Supabase REST API directly from the website**.

- No local Express server
- No SQLite database
- No `npm install` / `npm start` required for visitors
- Reviews are shared globally across all users

### How it works

- On page load, the site fetches reviews from Supabase and renders them in testimonials.
- On review submit, the site writes to Supabase.
- The new review appears immediately via optimistic UI, then is confirmed from Supabase response.
- All future visitors load the same shared reviews from the cloud table.

### One-time Supabase setup

1. Create a Supabase project.
2. In SQL Editor, run:

```sql
create table if not exists public.reviews (
  id bigint generated always as identity primary key,
  name text not null,
  text text not null,
  rating int not null check (rating between 1 and 5),
  created_at timestamptz not null default now()
);

alter table public.reviews enable row level security;

create policy "Anyone can read reviews"
on public.reviews
for select
using (true);

create policy "Anyone can insert reviews"
on public.reviews
for insert
with check (
  length(trim(name)) > 0
  and length(trim(text)) > 0
  and rating between 1 and 5
);
```

3. In `index.html`, set:
   - `SUPABASE_URL`
   - `SUPABASE_ANON_KEY`

Once set, deploy as a normal static site (e.g., GitHub Pages) and reviews will persist globally.
