# Google Sheets setup — one Sheet, one Script, five tabs

All five trainings write to **the same Google Sheet**, each into its own tab, using **one Apps Script deployment** shared by every training page.

## 1. Create the sheet

1. Create a new Google Sheet, e.g. **"Volunteer Training — Results"**.
2. Rename the default first tab (bottom-left) to **Crowd Medics**.
3. Add four more tabs (right-click the tab bar → Insert sheet), named exactly:
   - **Roaming Medics**
   - **Group Lead**
   - **Coordinator**
   - **Testimony Vetting**
4. In row 1 of **every** tab, add these headers exactly: `Timestamp | Name | Volunteer ID | Phone | Score | Total`

*(If you skip step 3–4, the script will auto-create any missing tab with the right headers the first time someone submits — but pre-creating them keeps things tidy and lets you check them before going live.)*

## 2. Add the Apps Script

1. In the Sheet, go to **Extensions → Apps Script**.
2. Delete anything in the editor and paste the script below.
3. Click **Save**, then **Deploy → New deployment**.
4. Deployment type: **Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
5. Click **Deploy**, authorize the permissions it asks for, and copy the **Web app URL**.

```javascript
function doPost(e) {
  var ss = SpreadsheetApp.getActiveSpreadsheet();
  var data;
  try {
    data = JSON.parse(e.postData.contents);
  } catch (err) {
    return respond({ status: 'error', message: 'Bad payload' });
  }

  var tabName = data.training || 'Untitled';
  var sheet = ss.getSheetByName(tabName);
  if (!sheet) {
    sheet = ss.insertSheet(tabName);
    sheet.appendRow(['Timestamp', 'Name', 'Volunteer ID', 'Phone', 'Score', 'Total']);
  }

  var idCol = 3; // Volunteer ID is column C
  var values = sheet.getDataRange().getValues();

  if (data.action === 'check') {
    var vid = String(data.volunteerId || '').trim().toLowerCase();
    for (var i = 1; i < values.length; i++) {
      if (String(values[i][idCol - 1]).trim().toLowerCase() === vid) {
        return respond({ completed: true });
      }
    }
    return respond({ completed: false });
  }

  if (data.action === 'submit') {
    var vid2 = String(data.volunteerId || '').trim().toLowerCase();
    for (var j = 1; j < values.length; j++) {
      if (String(values[j][idCol - 1]).trim().toLowerCase() === vid2) {
        return respond({ status: 'duplicate' });
      }
    }
    sheet.appendRow([
      data.timestamp || new Date().toISOString(),
      data.name || '',
      data.volunteerId || '',
      data.phone || '',
      data.score,
      data.total
    ]);
    return respond({ status: 'success' });
  }

  return respond({ status: 'error', message: 'Unknown action' });
}

function respond(obj) {
  return ContentService
    .createTextOutput(JSON.stringify(obj))
    .setMimeType(ContentService.MimeType.JSON);
}
```

## 3. Wire it into all five training pages

Each training already sends its own tab name automatically (`Crowd Medics`, `Roaming Medics`, `Group Lead`, `Coordinator`, `Testimony Vetting`) — you don't need to configure that part. You just need to paste the **same** Web app URL into all five files.

In each of `crowd-medics/index.html`, `roaming-medics/index.html`, `group-lead/index.html`, and `coordinator/index.html`, find:

```javascript
const SCRIPT_URL = "REPLACE_WITH_YOUR_APPS_SCRIPT_WEB_APP_URL";
```

and replace the placeholder with the **same** Web app URL you copied in step 2 — every training gets an identical line. Commit and push all five files.

**Until you do this**, every training still runs end-to-end for previewing (registration → content → quiz → results) but won't check for duplicates or record anything anywhere.

## Notes

- Each tab has its own independent duplicate check — a volunteer who completes Crowd Medics can still separately complete Roaming Medics, Group Lead, etc.
- The duplicate check matches on **Volunteer ID**, case-insensitively, trimmed of spaces, within that tab only.
- If someone needs to redo a specific training, delete their row from that tab only.
- To re-deploy after editing the Apps Script code later, use **Deploy → Manage deployments → edit (pencil) → New version → Deploy** — a fresh "New deployment" changes the URL and would need re-pasting into all five files; editing an existing version does not.
