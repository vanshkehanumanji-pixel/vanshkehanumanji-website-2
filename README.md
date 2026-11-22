# VanshkeHanumanji Team — Official Website

*एकता, सेवा और सनातन की आवाज़।*

React + Vite + Supabase (database, login, image storage). Plain CSS design system, no UI framework lock-in.
You own everything here: the code, the database, the hosting. Nothing depends on Claude or any hidden service.

> **Status:** the source code is complete, but it has **not been built or run in a browser yet** (the environment it was written in
> could not install packages). Steps 1–2 below are the first real test. Report any error text you see and it can be fixed quickly.

---

## 1. Run it on your computer

1. Install **Node.js 20+** from nodejs.org.
2. Put your files in the `public/` folder (see `public/ADD-YOUR-IMAGES-HERE.txt`):
   `logo.png` (your original logo), `hero.jpg`, `founder.jpg`.
3. In this folder run:
   ```bash
   npm install
   npm run dev
   ```
4. Open the address shown (usually http://localhost:5173).

Without Supabase the public website works, but forms say honestly that they are **not connected** and nothing is saved.
Pages show "Coming Soon" until content is published.

## 2. Database configuration (Supabase, free plan)

1. Create an account at **supabase.com** → *New project* (choose a region near India, e.g. Mumbai). Save the database password.
2. **SQL Editor → New query** → paste all of `supabase/schema.sql` → **Run**. This creates the tables, the security rules
   (Row Level Security) and the `media` storage bucket.
3. **Project Settings → API**: copy the **Project URL** and the **anon public** key.
4. Copy `.env.example` to `.env` and fill in:
   ```
   VITE_SUPABASE_URL=...
   VITE_SUPABASE_ANON_KEY=...
   ```
   The anon key is *meant* to be public. Visitors can only do what the security rules allow (read published content, submit a form).
   **Never put the `service_role` key anywhere in this project.**
5. Restart `npm run dev`.

## 3. Admin account setup (owner)

There is no default or hard-coded password. You create the only admin yourself:

1. Supabase → **Authentication → Users → Add user → Create new user**: enter **your email** and a **strong password**
   (tick *Auto confirm user*).
2. **SQL Editor** → run (use your email):
   ```sql
   insert into public.admins (user_id)
   select id from auth.users where email = 'your-email@example.com';
   ```
3. Supabase → **Authentication → Sign In / Providers → Email** → turn **off** "Allow new users to sign up",
   so nobody else can create accounts.
4. Open `/admin/login` on your site and sign in. To add a second admin later, repeat steps 1–2 with their email.

Admin pages are protected in the app, and the database independently refuses admin actions from anyone not in the `admins` table.

## 4. Using the dashboard

| Menu | What it does |
|---|---|
| Overview | Counts of pending applications / unread messages, and **Download data backup**. |
| Homepage, About Us, Founder, Contact & Social | Edit text in English and Hindi, replace the hero/founder photo, change Instagram / WhatsApp / email / Facebook / YouTube links (empty links are hidden). |
| Team Members | Add / edit / hide / delete members. |
| Seva Activities | Title, description, photograph, date, location, category. |
| Gallery | Upload photos or videos (or paste a YouTube link), pick the section. |
| Announcements | Publish notices, events and updates with dates. |
| Applications | Read, **Approve / Reject**, export CSV, delete. |
| Messages | Contact-form messages. |

### Adding team members in the future
Admin → **Team Members → + Add new** → choose the category (*Core Team / Main Team Members / Team Members / Volunteers*),
enter name, designation, city, description, upload the member's **real photo**, **Save**. It appears on `/team` immediately.
Use *Display order* to arrange (lower number = shown first). Use **Hide** to remove someone from the site without deleting them.

## 5. Put the code on GitHub (you stay the owner)

1. Create a free account at github.com → **New repository** (e.g. `vanshkehanumanji-website`), private or public, empty.
2. In this folder:
   ```bash
   git init
   git add .
   git commit -m "Initial website"
   git branch -M main
   git remote add origin https://github.com/YOUR-USERNAME/vanshkehanumanji-website.git
   git push -u origin main
   ```
   (`.env` is git-ignored, so your keys are not uploaded.)

## 6. Deploy free on Vercel

1. vercel.com → sign up with your GitHub account → **Add New → Project** → import the repository.
2. Framework preset: **Vite** (auto-detected). Build command `npm run build`, output `dist`.
3. **Environment Variables**: add `VITE_SUPABASE_URL` and `VITE_SUPABASE_ANON_KEY` (same values as `.env`).
4. **Deploy.** The project name decides the address: naming the project `vanshkehanumanji` gives
   `vanshkehanumanji.vercel.app` **if that name is still free** (it is first-come, first-served and cannot be checked from here).
   If it is taken, try `vanshkehanumanji-team`, `vanshkehanumanji-bareilly`, or `vkh-team`.
5. After you know the final address, replace `vanshkehanumanji.vercel.app` in: `index.html` (og:url, og:image),
   `public/sitemap.xml`, `public/robots.txt`. Commit and push — Vercel redeploys automatically.
6. In Supabase → **Authentication → URL Configuration**, set *Site URL* to your live address.

**Cloudflare Pages alternative:** Workers & Pages → Create → Connect to Git → build command `npm run build`, output `dist`,
add the same two environment variables. (`public/_redirects` already handles page routing.)

**Own domain later:** buy a domain from any registrar, then Vercel → Project → Domains → Add and follow the DNS steps.
You hold the registrar account, so you control the domain.

## 7. Backups & moving to another host

- **Content:** Admin → Overview → *Download data backup* (JSON). Do this monthly.
- **Full database:** Supabase → Database → Backups, or CLI: `supabase db dump -f backup.sql`.
- **Uploaded photos/videos:** Supabase → Storage → `media` → download, or keep originals on your phone/computer.
- **Code:** it lives in your GitHub repository.
- **Move hosting:** the site is a static build (`npm run build` → `dist/`). Upload `dist/` to Netlify, Cloudflare Pages, GitHub Pages
  (needs SPA fallback), or any web server, and set the same two environment variables at build time.
- **Move database:** create a new Supabase project, run `schema.sql`, restore your dump, copy the storage files, update the two env variables.

## 8. Project structure

```
public/            logo.png, hero.jpg, founder.jpg, robots.txt, sitemap.xml, _redirects
supabase/schema.sql  tables + security policies + storage
src/lib/           defaults.js (all default text), ui.js (button/label text, Hindi+English), supabase client, helpers
src/context/       language, site content, auth
src/components/    Navbar, Footer, Logo, Lightbox, cards, loader, WhatsApp button
src/pages/         public pages        src/pages/admin/   dashboard
src/styles/index.css   design system (colours at the top)
```

## 9. Content rules followed

- Only the text you supplied is used. English text is a faithful translation of your Hindi; edit it in the dashboard if you prefer other wording.
- No team names, activities, announcements, achievements, statistics or testimonials are included. Empty sections show "Coming Soon".
- The logo and photographs are displayed as supplied (no recolouring, filters or AI changes). Large uploads are only reduced in pixel size.
- Application/message forms **never claim success** unless the database accepted the entry.
- `Privacy Policy` and `Terms` are plain starter texts describing how this site works — please have a legal adviser review them.

## 10. Testing checklist (do this after first deploy)

Open every menu link; switch हिन्दी/English; try the mobile menu; submit the Join form (check it appears under Admin → Applications);
send a contact message; add one test announcement and a test photo, confirm they appear, then delete them; open a wrong URL to see the 404 page.
