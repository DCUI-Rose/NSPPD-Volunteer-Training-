# Volunteer Training Modules

One repository, one live site, one folder per training — and each training gets its own dedicated link. Five modules are live: **Crowd Medics**, **Roaming Medics**, **Group Lead**, **Coordinator**, and **Testimony Vetting**. Future trainings (Ushering, Stewarding, etc.) get added the same way, one at a time, without touching what's already live.

```
repo/
├── index.html                  ← landing page, links to every training
├── assets/
│   └── theme.css                ← shared colours, fonts, components — used by every training
├── crowd-medics/
│   └── index.html                ← its own dedicated URL
├── roaming-medics/
│   └── index.html
├── group-lead/
│   └── index.html
├── coordinator/
│   └── index.html
├── testimony-vetting/
│   └── index.html
├── apps-script-setup.md          ← Google Sheets setup — shared by all trainings
└── README.md                     ← this file
```

Once this is live, each module has its own link that's completely separate from the others, e.g.:

```
https://yourname.github.io/volunteer-trainings/crowd-medics/
https://yourname.github.io/volunteer-trainings/roaming-medics/
https://yourname.github.io/volunteer-trainings/group-lead/
https://yourname.github.io/volunteer-trainings/coordinator/
https://yourname.github.io/volunteer-trainings/testimony-vetting/
```

Those are the exact links you hand to each group of volunteers — nothing else on the site.

## Documenting a Medical Incident is not a standalone module

It's folded into the end of **every** training above (right before the quiz) as its own numbered section, and each training's 12-question quiz includes 3 questions on it. There's nothing to set up separately — it's just part of each module's content and scoring.

---

## Part A — Put it on GitHub

You only do this section once, when setting the repo up for the first time.

### 1. Create the repository
1. Go to [github.com/new](https://github.com/new).
2. Name it something like `volunteer-trainings`.
3. Set it to **Public** (GitHub Pages on a free account needs a public repo).
4. Don't add a README/gitignore/license from GitHub's UI — you already have these files.
5. Click **Create repository**.

### 2. Upload the files
On the empty repo page, click **uploading an existing file**, then drag in the whole unzipped folder (keeping the structure above intact), and click **Commit changes**. No command line needed.

### 3. Turn on GitHub Pages
1. Go to **Settings → Pages**.
2. Under **Source**, choose **Deploy from a branch**.
3. Branch: **main**, folder: **/ (root)**. Save.
4. After a minute, GitHub shows your live site URL:
   `https://yourname.github.io/volunteer-trainings/`
5. Crowd Medics itself is live at:
   `https://yourname.github.io/volunteer-trainings/crowd-medics/`

---

## Part B — Connect all five trainings to one Google Sheet

All five trainings share **one Google Sheet** (with a separate tab per training) and **one Apps Script deployment**. Full walkthrough is in [`apps-script-setup.md`](apps-script-setup.md). Short version:

1. Create a Google Sheet with a tab for each training: **Crowd Medics**, **Roaming Medics**, **Group Lead**, **Coordinator**, **Testimony Vetting** — each with headers `Timestamp | Name | Volunteer ID | Phone | Score | Total`.
2. Paste the provided Apps Script into **Extensions → Apps Script**, deploy as a **Web app** (Execute as: Me, Access: Anyone).
3. Copy the Web app URL it gives you.
4. In **each** of the five `index.html` files, find:
   ```javascript
   const SCRIPT_URL = "REPLACE_WITH_YOUR_APPS_SCRIPT_WEB_APP_URL";
   ```
   and paste in the **same** URL — all five files get an identical line.
5. Re-upload those five files to GitHub (or edit each directly with the pencil icon) and commit.
6. Wait ~1 minute for the site to rebuild, then test each live link with a throwaway Volunteer ID before sending it out for real.

Each training automatically writes to its own tab and checks for duplicates only within that tab — so completing one training doesn't block someone from completing another.

---

## Part C — Adding the next training (e.g. Ushering)

1. **Duplicate the folder:** copy `crowd-medics/` and rename the copy `ushering/`.
2. **Edit `ushering/index.html`:** update the title, hero copy, all content sections, and the 12 quiz questions for the new subject. Leave `<link rel="stylesheet" href="../assets/theme.css">` as-is — that's what keeps the colours identical across every training. Also find `const TRAINING = "Crowd Medics";` and change it to `const TRAINING = "Ushering";` — this is what tells the shared script which tab to write to. Leave `SCRIPT_URL` exactly as it is; it's shared across every training.
3. **Add a new tab to the shared Sheet** — name it to match the training (e.g. "Ushering"), with the same headers `Timestamp | Name | Volunteer ID | Phone | Score | Total`. No new Apps Script or new URL needed — the existing one auto-routes to the right tab based on each training's name, so just reuse the same `SCRIPT_URL` you already have.
4. **Add a card to the landing page** — open the root `index.html`, and add inside `.train-list`:
   ```html
   <a class="train-card" href="ushering/">
     <div>
       <div class="tname">Ushering</div>
       <div class="tdesc">Guest flow, seating, and hospitality standards.</div>
     </div>
     <div class="arrow">→</div>
   </a>
   ```
5. Upload the new `ushering/` folder and the updated root `index.html` to GitHub, commit.

Ushering will now live at `https://yourname.github.io/volunteer-trainings/ushering/` — its own link and its own quiz, logging to its own tab in the same shared Sheet — while every other training keeps working exactly as before.

## Changing the colour scheme everywhere at once

Edit `assets/theme.css` only. Every training links to that one file, so a change there updates all of them at once.
