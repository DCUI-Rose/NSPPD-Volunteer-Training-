# Volunteer Training Modules

One repository, one live site, one module per folder. Crowd Medics is the first module — future trainings (Ushering, Stewarding, etc.) get added the same way, one at a time, without touching what's already live.

```
repo/
├── index.html                          ← landing page, links to every training
├── assets/
│   └── theme.css                       ← shared colours, fonts, components — used by every training
├── trainings/
│   └── crowd-medics/
│       ├── index.html                  ← the training itself
│       └── apps-script-setup.md        ← Google Sheets setup notes for THIS training
└── README.md                           ← this file
```

---

## Part A — Put it on GitHub

You only do this section once, when setting the repo up for the first time.

### 1. Create the repository
1. Go to [github.com/new](https://github.com/new).
2. Name it something like `volunteer-trainings`.
3. Set it to **Public** (GitHub Pages on a free account needs a public repo, unless you're on a paid plan with private Pages).
4. Don't add a README/gitignore/license from GitHub's UI — you already have these files locally.
5. Click **Create repository**.

### 2. Push these files
From the folder containing this README (i.e. the repo root), in a terminal:

```bash
git init
git add .
git commit -m "Add Crowd Medics training"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/volunteer-trainings.git
git push -u origin main
```

(No terminal handy? GitHub's web UI also lets you drag-and-drop the whole folder in via **Add file → Upload files** on the repo page — just make sure the folder structure above is preserved.)

### 3. Turn on GitHub Pages
1. On the repo page, go to **Settings → Pages**.
2. Under **Build and deployment → Source**, choose **Deploy from a branch**.
3. Branch: **main**, folder: **/ (root)**. Save.
4. GitHub gives you a live URL after a minute or two, typically:
   `https://YOUR-USERNAME.github.io/volunteer-trainings/`
5. The Crowd Medics training will be live at:
   `https://YOUR-USERNAME.github.io/volunteer-trainings/trainings/crowd-medics/`

That URL is what you share with volunteers or link to from the portal.

---

## Part B — Connect Crowd Medics to Google Sheets

Full step-by-step is in [`trainings/crowd-medics/apps-script-setup.md`](trainings/crowd-medics/apps-script-setup.md). Short version:

1. Create a Google Sheet with headers `Timestamp | Name | Volunteer ID | Phone | Score | Total`.
2. Paste the provided Apps Script into **Extensions → Apps Script**, deploy as a **Web app** (Execute as: Me, Access: Anyone).
3. Copy the Web app URL it gives you.
4. In `trainings/crowd-medics/index.html`, replace:
   ```javascript
   const SCRIPT_URL = "REPLACE_WITH_YOUR_APPS_SCRIPT_WEB_APP_URL";
   ```
   with your real URL.
5. Commit and push:
   ```bash
   git add trainings/crowd-medics/index.html
   git commit -m "Connect Crowd Medics to Google Sheet"
   git push
   ```
6. Wait ~1 minute for GitHub Pages to rebuild, then test the live link end-to-end with a throwaway Volunteer ID before sending it out for real.

This gives you one-completion-per-volunteer enforcement (checked against the Sheet itself, so it holds across devices) and every submission logged automatically with a timestamp and score.

---

## Part C — Adding the next training (e.g. Ushering)

Each new training follows the exact same pattern, dropped into its own folder — nothing above needs to change.

1. **Duplicate the folder:**
   ```bash
   cp -r trainings/crowd-medics trainings/ushering
   ```
2. **Edit `trainings/ushering/index.html`:**
   - Update the title, hero copy, and all content sections for the new subject.
   - Replace the 12 quiz questions with ones for the new content.
   - Leave the `<link rel="stylesheet" href="../../assets/theme.css">` line as-is — this is what keeps the colour scheme identical across every training.
   - Reset `SCRIPT_URL` back to the placeholder until you've made a new Sheet for it (each training needs its **own** Sheet — don't reuse Crowd Medics' Sheet or its rows will mix with Ushering's).
3. **Set up its own Google Sheet + Apps Script**, following `apps-script-setup.md` again (copy that file into `trainings/ushering/` and update it too).
4. **Add it to the landing page** — open `index.html` at the repo root and add a new card inside `.train-list`, following the commented example already in that file:
   ```html
   <a class="train-card" href="trainings/ushering/">
     <div>
       <div class="tname">Ushering</div>
       <div class="tdesc">Guest flow, seating, and hospitality standards.</div>
     </div>
     <div class="arrow">→</div>
   </a>
   ```
5. **Commit and push:**
   ```bash
   git add trainings/ushering index.html
   git commit -m "Add Ushering training"
   git push
   ```

Repeat for each subsequent module. Because the colours, fonts, form gate, and slide mechanics all live in the shared `assets/theme.css` (styling) and are copied fresh per training (structure/behaviour), every module looks and behaves identically while staying independently editable and independently trackable in its own Sheet.

## Making a global style change later

If you ever want to tweak a colour, font, or component (e.g. the red accent, the card shadow) across **every** training at once, edit `assets/theme.css` only — every training under `trainings/` picks it up automatically since they all link to the same file.
