# Crowd Medics — Google Sheets setup

Every training module gets **its own Google Sheet and its own Apps Script deployment** (even though the steps are identical each time). Keep this file next to that training's `index.html` so the two stay paired.

## 1. Create the sheet

1. Create a new Google Sheet named e.g. **"Crowd Medics Training — Results"**.
2. In row 1, add these headers exactly: `Timestamp | Name | Volunteer ID | Phone | Score | Total`

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
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var data;
  try {
    data = JSON.parse(e.postData.contents);
  } catch (err) {
    return respond({ status: 'error', message: 'Bad payload' });
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

## 3. Wire it into `index.html`

Open `trainings/crowd-medics/index.html`, find this line near the top of the `<script>` block:

```javascript
const SCRIPT_URL = "REPLACE_WITH_YOUR_APPS_SCRIPT_WEB_APP_URL";
```

Replace the placeholder with the Web app URL you copied in step 2. Commit and push the change.

**Until you do this**, the module still runs end-to-end for previewing (registration → content → quiz → results) but won't check for duplicates or record anything anywhere.

## Notes

- The duplicate check matches on **Volunteer ID**, case-insensitively, trimmed of spaces.
- If someone needs to redo the training, delete their row from the Sheet.
- To re-deploy after editing the Apps Script code later, use **Deploy → Manage deployments → edit (pencil) → New version → Deploy** — a fresh "New deployment" changes the URL, an edited version doesn't.
