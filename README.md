# HelloMMTC CRM

A CRM dashboard application — leads, pipeline (drag-and-drop kanban), contacts, analytics, and tasks — built with React, Vite, React Router, and Recharts.

## Project structure

```
hellommtc-crm/
├── index.html
├── package.json
├── vite.config.js
├── vercel.json
├── public/
│   └── favicon.svg
└── src/
    ├── main.jsx
    ├── App.jsx                 # router setup
    ├── index.css                # design tokens (light/dark theme)
    ├── components/
    │   ├── Layout.jsx           # sidebar + topbar shell
    │   └── ui.jsx                # Card, MetricCard, Badge, Avatar, etc.
    ├── pages/
    │   ├── Dashboard.jsx
    │   ├── Leads.jsx
    │   ├── Pipeline.jsx
    │   ├── Contacts.jsx
    │   ├── Analytics.jsx
    │   ├── Tasks.jsx
    │   ├── Settings.jsx
    │   └── NotFound.jsx
    └── data/
        └── mockData.js           # sample leads, activity, tasks, team data
```

## Run it locally

You'll need Node.js 18 or later installed.

```bash
cd hellommtc-crm
npm install
npm run dev
```

This starts a dev server, normally at http://localhost:5173. Open it in your browser to see the app live, with hot-reload on every save.

To produce a production build:

```bash
npm run build
```

This outputs static files to dist/. Preview the production build locally with:

```bash
npm run preview
```

## What's wired up vs. what's mock data

Everything in the UI is fully interactive (dark mode, search, filters, drag-and-drop pipeline, task checkboxes, the lead detail drawer) but all data lives in src/data/mockData.js and resets on page reload — there's no backend yet. To make it persist real data, you'd connect a backend (e.g. a Postgres database via Supabase, or your own API) and replace the imports in each page with API calls.

---

# Deploying to app.hellommtccrm.com

Below is the full process, end to end: get the code online, connect your domain, and go live with HTTPS.

## Step 1 — Get the code into a Git repository

Deployment platforms work by connecting to a Git repo (GitHub, GitLab, or Bitbucket), so push this project there first.

```bash
cd hellommtc-crm
git init
git add .
git commit -m "Initial commit: HelloMMTC CRM"
```

Create a new empty repository on GitHub (no README/license, since you already have files), then:

```bash
git remote add origin https://github.com/YOUR-USERNAME/hellommtc-crm.git
git branch -M main
git push -u origin main
```

## Step 2 — Deploy to a hosting platform

Any static host that builds Vite apps works. Vercel is the simplest path and is what vercel.json in this project is already configured for.

### Option A: Vercel (recommended)

1. Go to vercel.com and sign up/log in (GitHub login is fastest).
2. Click Add New, then Project.
3. Select your hellommtc-crm repository and click Import.
4. Vercel auto-detects Vite. Confirm these build settings (they should be pre-filled):
   - Build command: npm run build
   - Output directory: dist
   - Install command: npm install
5. Click Deploy. In about a minute you'll get a live URL like hellommtc-crm.vercel.app.

### Option B: Netlify

1. Go to netlify.com and sign up/log in.
2. Click Add new site, then Import an existing project, connect GitHub, and pick your repo.
3. Set:
   - Build command: npm run build
   - Publish directory: dist
4. Click Deploy site. Since this is a single-page app, also add a public/_redirects file containing "/* /index.html 200" so client-side routing works on refresh (Vercel's vercel.json already handles this for Option A).

### Option C: Your own server (VPS / Docker / Nginx)

```bash
npm run build
# copy the dist/ folder to your server, e.g.:
scp -r dist/* user@your-server:/var/www/hellommtccrm/
```

Then configure Nginx to serve it as a single-page app:

```nginx
server {
  listen 80;
  server_name app.hellommtccrm.com;
  root /var/www/hellommtccrm;
  index index.html;
  location / {
    try_files $uri /index.html;
  }
}
```

Run "certbot --nginx -d app.hellommtccrm.com" (via Certbot) to get free HTTPS from Let's Encrypt.

## Step 3 — Point your domain (app.hellommtccrm.com) at it

This requires that you already own the hellommtccrm.com domain (register it via Namecheap, GoDaddy, Google Domains, etc. if you haven't).

If using Vercel or Netlify:

1. In your project's dashboard, go to Settings, then Domains.
2. Add app.hellommtccrm.com as a custom domain.
3. The platform will show you a DNS record to add — typically a CNAME record:
   - Type: CNAME
   - Name/Host: app
   - Value: the platform's domain (e.g. cname.vercel-dns.com for Vercel, your-site.netlify.app for Netlify)
4. Log into your domain registrar's DNS settings (where you bought hellommtccrm.com) and add that CNAME record.
5. DNS changes can take a few minutes to a few hours to propagate. Once they do, the platform automatically issues a free SSL certificate, and https://app.hellommtccrm.com goes live.

If using your own server (Option C above):

1. In your registrar's DNS settings, add an A record:
   - Name/Host: app
   - Value: your server's IP address
2. Wait for DNS propagation, then run Certbot as shown above to enable HTTPS.

## Step 4 — Verify and go live

- Visit https://app.hellommtccrm.com and confirm it loads with a valid padlock/HTTPS.
- Test navigation between Dashboard, Leads, Pipeline, Contacts, Analytics, and Tasks — refresh on a non-root route (e.g. /leads) to confirm the SPA redirect rule is working.
- Toggle dark mode and resize the window to confirm the mobile layout (hamburger menu) works.

## Next steps (optional, for production use)

- Backend and auth: connect a real database (Supabase, Firebase, or a custom API) and add login so each rep sees their own data.
- Environment variables: if you add an API, store keys in .env locally and in your hosting platform's environment variable settings (never commit secrets to Git).
- Continuous deployment: once connected to GitHub, both Vercel and Netlify auto-deploy on every git push to main — no manual redeploy needed after the first setup.
