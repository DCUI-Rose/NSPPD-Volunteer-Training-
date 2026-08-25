# Volunteer Training Modules

One repository, one live site, one folder per training — and each training gets its own dedicated link. Crowd Medics is the first. Future trainings (Ushering, Stewarding, etc.) get added the same way, one at a time, without touching what's already live.

```
repo/
├── index.html                  ← landing page, links to every training
├── assets/
│   └── theme.css                ← shared colours, fonts, components — used by every training
├── crowd-medics/
│   ├── index.html                ← the training itself (its own dedicated URL)
│   └── apps-script-setup.md      ← Google Sheets setup notes for THIS training
└── README.md                     ← this file
```

Once this is live, Crowd Medics will have its own link that's completely separate from any other training you add later, e.g.:

```
https://yourname.github.io/volunteer-trainings/crowd-medics/
```

That's the exact link you hand to volunteers for that module — nothing else on the site.

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

## Part B — Connect Crowd Medics to Google Sheets

Full walkthrough is in [`crowd-medics/apps-script-setup.md`](crowd-medics/apps-script-setup.md). Short version:

1. Create a Google Sheet with headers `Timestamp | Name | Volunteer ID | Phone | Score | Total`.
2. Paste the provided Apps Script into **Extensions → Apps Script**, deploy as a **Web app** (Execute as: Me, Access: Anyone).
3. Copy the Web app URL it gives you.
4. In `crowd-medics/index.html`, find:
   ```javascript
   const SCRIPT_URL = "REPLACE_WITH_YOUR_APPS_SCRIPT_WEB_APP_URL";
   ```
   and replace the placeholder with your real URL.
5. Re-upload that one file to GitHub (or edit it directly on GitHub with the pencil icon) and commit.
6. Wait ~1 minute for the site to rebuild, then test the live link with a throwaway Volunteer ID before sending it out for real.

---

## Part C — Adding the next training (e.g. Ushering)

1. **Duplicate the folder:** copy `crowd-medics/` and rename the copy `ushering/`.
2. **Edit `ushering/index.html`:** update the title, hero copy, all content sections, and the 12 quiz questions for the new subject. Leave `<link rel="stylesheet" href="../assets/theme.css">` as-is — that's what keeps the colours identical across every training. Reset `SCRIPT_URL` back to the placeholder.
3. **Set up its own Google Sheet + Apps Script** (copy `apps-script-setup.md` into `ushering/` too, and follow it again — each training needs its **own** Sheet, don't reuse Crowd Medics' rows will mix otherwise).
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

Ushering will now live at `https://yourname.github.io/volunteer-trainings/ushering/` — its own link, its own Sheet, its own quiz — while Crowd Medics keeps working exactly as before.

## Changing the colour scheme everywhere at once

Edit `assets/theme.css` only. Every training links to that one file, so a change there updates all of them at once.
