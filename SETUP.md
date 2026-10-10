# Setting up cloud backup (one time, about 15 minutes)

The app works without this. Cloud backup adds: sign-in with email, automatic backups tied to that email, and restore on any device. It uses a free Supabase project as the backend.

## 1. Create the project
1. Sign up at supabase.com and create a new project. Note the **Project URL** and the **public key** (shown as "anon" or "publishable") under Project Settings > API.
2. Open `config.js` in this folder and paste them into `supabaseUrl` and `supabaseKey`. The public key is meant to be in the app. Never paste the `service_role` or secret key.

## 2. Create the tables (SQL Editor > New query > Run)
```sql
create table if not exists public.profiles (
  user_id uuid primary key default auth.uid() references auth.users(id) on delete cascade,
  name text, email text, phone text,
  updated_at timestamptz default now()
);
create table if not exists public.backups (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null default auth.uid() references auth.users(id) on delete cascade,
  kind text not null,
  label text,
  data text not null,
  created_at timestamptz default now(),
  unique (user_id, kind)
);
alter table public.profiles enable row level security;
alter table public.backups  enable row level security;
create policy "own profile" on public.profiles for all
  using (user_id = auth.uid()) with check (user_id = auth.uid());
create policy "own backups" on public.backups for all
  using (user_id = auth.uid()) with check (user_id = auth.uid());
```
These row-level security rules are what keep every person's data private: each signed-in user can only see their own rows.

## 3. Make the email contain a code (not a link)
Links open in a different place from an installed app, so the app uses a 6-digit code.
Authentication > Emails (Templates): edit both **Magic Link** and **Confirm signup** so the body contains `{{ .Token }}`, for example:
```html
<h2>Your Debt Tracker code</h2>
<p>Enter this code in the app: <b>{{ .Token }}</b></p>
```
New people get the Confirm signup email and returning people get the Magic Link email, so both need the code.

## 4. Email sending
Supabase's built-in email sender is only meant for testing and is heavily limited. Before sharing with many people, add your own SMTP provider under Authentication > SMTP Settings (Resend, SendGrid, Amazon SES and others all work). Sign-in emails are rate limited by Supabase to protect against abuse.

## 5. Host the app and share the link
Upload this folder to your static host (see README.md). Each person opens the link, installs it, fills in the welcome screen, and enters the code emailed to them.

## Things to know
- **Free plan pausing:** free Supabase projects can be paused after a week of low activity. While paused, sign-in and backups fail until you restore the project from the dashboard. The Pro plan removes pausing.
- **The database itself is not backed up on the free plan.** Occasionally export the `backups` table, or upgrade.
- **Privacy:** you hold your friends' names, emails, phone numbers and financial backups. As the project owner you can read them in the dashboard. Only collect what you need, tell people what you store, and delete anyone's data on request (the app has a "Delete my cloud data" button; remove the sign-in account itself under Authentication > Users).
- To make sign-in mandatory, set `requireSignIn: true` in `config.js`.
- After editing any file, change `VERSION` in `sw.js` so installed copies update.
