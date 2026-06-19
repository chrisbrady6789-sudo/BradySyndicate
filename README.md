# Brady Syndicate

A single-file HTML betting syndicate tracker for a family group. Tracks weekly bet requests, placements, results, and P&L across all members. Hosted on GitHub Pages, backed by Firebase Realtime Database, with bet screenshot uploads via Cloudinary.

---

## Live App

Once deployed to GitHub Pages the app will be available at:
```
https://<your-github-username>.github.io/<repo-name>/syndicate.html
```

---

## Features

- **PIN-based login** — each member has a 4-digit PIN stored in Firebase
- **Submit a bet** — type a selection or upload a screenshot (e.g. an acca from Paddy Power)
- **WhatsApp deep link** — after submitting, a pre-filled WhatsApp message is generated for the group chat
- **Feed** — live feed of all bets with status badges (Pending / Placed / Won / Lost); red dot badge on nav when your bet status changes
- **Leaderboard** — all-time and this-week P&L rankings
- **Admin queue** — admins (Chris, Fran, Jason) can mark bets as placed (entering actual odds), settle results, or void bets
- **Retroactive bet entry** — admins can add bets that were placed outside the app
- **Weekly advance** — Friday reset awards new bets to all members, applies activity rule
- **Auto weekly backup** — on week advance, a full JSON export is emailed to placers via EmailJS
- **Manual export** — download full Firebase data as JSON at any time (Admin → Import tab)

---

## Members

| Name | Role |
|------|------|
| Keith | Member |
| Fran | Member + **Admin/Placer** |
| Shane | Member |
| Jack | Member |
| Luke | Member |
| Jason | Member + **Admin/Placer** |
| Liv | Member |
| Chris | Member + **Admin/Placer** |
| Liam | Member |
| Paddy | Member |

**Admins/Placers** (Chris, Fran, Jason) have access to the bet queue, admin panel, and all management tools. Regular members can submit bets, view the feed, leaderboard, and change their own PIN.

---

## Syndicate Rules (as configured)

| Setting | Value |
|---------|-------|
| Bets awarded per week | 2 |
| € value per bet | €5 |
| Max bets carried over | 4 |
| Big odds threshold (bonus bet) | 10/1+ |
| Starting bets per member | 4 |
| Activity rule | Must place 1 bet every 2 weeks or forfeit 1 |

These can be adjusted in **Admin → Config → Syndicate Settings**.

---

## Tech Stack

| Service | Purpose | Link |
|---------|---------|------|
| GitHub Pages | Hosting (free, static) | https://pages.github.com |
| Firebase Realtime Database | Live data sync, user PINs, bets | https://console.firebase.google.com |
| Cloudinary | Bet screenshot image uploads | https://cloudinary.com |
| EmailJS | Email notifications + weekly backup | https://emailjs.com |
| Discord Webhook | New bet notifications to Discord | Optional |

---

## Setup Guide

### 1. Firebase

The app is already connected to the `brady-syndicate` Firebase project.

- **Project:** `brady-syndicate`
- **Database URL:** `https://brady-syndicate-default-rtdb.europe-west1.firebasedatabase.app`
- **Console:** https://console.firebase.google.com/project/brady-syndicate

**To re-seed the database** (start fresh or restore):
1. Firebase console → Realtime Database → ⋮ menu → **Import JSON**
2. Upload `syndicate_seed.json`
3. This sets current week to **26**, seeds all-time P&L from the Year 2 spreadsheet, and gives everyone 4 bets

> ⚠️ Importing JSON **wipes all existing data**. Export a backup first via Admin → Import tab → Export Full Data.

Firebase Database Rules (test mode — already configured):
```json
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

---

### 2. Cloudinary (Screenshot Uploads)

Bet screenshots are uploaded to Cloudinary and stored in a `syndicate/` folder.

- **Account:** https://cloudinary.com
- **Cloud name:** `dfbtotwyr`
- **Upload preset:** `syndicate` (Unsigned)
- **Media Library folder:** `syndicate/` — all bet screenshots land here

**Verify the preset is Unsigned:**
1. Cloudinary dashboard → Settings (gear icon) → Upload
2. Scroll to **Upload presets** → click `syndicate`
3. Confirm **Signing mode** = `Unsigned` → Save

**Image cleanup:** Screenshots are automatically unlinked from Firebase when a bet is settled or voided. To delete the actual image files, go to Cloudinary → Media Library → `syndicate/` folder → select all → delete. Recommended weekly after settling bets.

**Free tier:** 25 GB storage, 25 GB bandwidth/month — more than sufficient.

---

### 3. EmailJS (Optional — Notifications & Weekly Backup)

Used for:
- Notifying placers when a new bet is submitted
- Auto-emailing a full JSON backup when the week is advanced

**Setup:**
1. Sign up at https://emailjs.com (free tier: 200 emails/month)
2. Create a **Service** (connect your Gmail or other provider)
3. Create a **Template** with these variables: `{{subject}}`, `{{from_name}}`, `{{body}}`
4. In the app go to **Admin → Config** and enter:
   - EmailJS Service ID
   - EmailJS Template ID
   - EmailJS Public Key
   - Placer email addresses (comma separated)

---

### 4. Discord Webhook (Optional)

New bet notifications posted to a Discord channel.

1. Discord server → channel settings → Integrations → Webhooks → New Webhook
2. Copy the webhook URL
3. Paste into **Admin → Config → Discord Webhook URL**

---

### 5. GitHub Pages Deployment

1. Create a new GitHub repository
2. Upload `syndicate.html` and `README.md` to the repo root
3. Go to **Settings → Pages → Source** → select `main` branch → `/ (root)` → Save
4. App goes live at `https://<username>.github.io/<repo-name>/syndicate.html` within a minute

No build step, no dependencies — it's a single HTML file.

---

## Weekly Admin Workflow

1. **Friday** — click **Advance to Next Week** in Admin → Week tab
   - Awards 2 bets to each member (capped at 4)
   - Applies activity rule (forfeit 1 bet if no bet placed in last 2 weeks)
   - Auto-emails a JSON backup to placers
2. **During the week** — members submit bets; WhatsApp message auto-generated for the group chat
3. **Placers** — check Admin → Queue, mark bets as Placed and enter actual odds
4. **Results** — mark bets as Won/Lost, enter returns
5. **Screenshots** — clean up `syndicate/` folder in Cloudinary after settling the week's bets

---

## Data Seed

Historical P&L from Year 2 is pre-loaded via `syndicate_seed.json`. The app starts at **Week 26** with the following all-time P&L carried over:

| Member | All-Time P&L |
|--------|-------------|
| Paddy | +€180.70 |
| Jason | +€99.60 |
| Liv | +€0.25 |
| Liam | -€10.25 |
| Chris | -€11.20 |
| Fran | -€56.72 |
| Keith | -€47.62 |
| Luke | -€85.25 |
| Shane | -€97.87 |
| Jack | -€182.88 |

---

## Files

| File | Description |
|------|-------------|
| `syndicate.html` | The entire app — one file |
| `syndicate_seed.json` | Firebase import to seed Week 26 with historical P&L |
| `README.md` | This file |
