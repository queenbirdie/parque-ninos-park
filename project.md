# Friends of Parque Niños Unidos — Park Cleanup Site

**Status:** Site built, awaiting Apps Script deployment before native form can go live

---

## What this is
A neighbor-led effort to keep Parque Niños Unidos (3090 23rd St, Mission District) clean via a 15-minutes-at-a-time volunteer signup. Evolved from the ideas in [../mission-parks.md](../mission-parks.md).

## Files
- `index.html` — single-page bilingual (EN/ES) site: hero, how-it-works, FAQ, signup section
- Git repo initialized locally, no remote yet

## Current blocker
The signup section still uses a **Google Form iframe**, which clashes with the site's parchment/Fraunces aesthetic. Plan is to replace it with a native HTML form that POSTs to a Google Apps Script Web App, which writes to the same backing Google Sheet.

**Linked Google Sheet:** https://docs.google.com/spreadsheets/d/11BU8vYH6DaOBcNt1fE_WJmJhqzonHXL_PoGQAHwDChE/edit?resourcekey=&gid=573077395#gid=573077395

Sheet columns (from earlier inspection): timestamp, first name, last name, email, frequency, has picker?, notes, email (dup).

## Next steps
1. **Lauren:** Deploy the Apps Script Web App (I can't do this step — it requires clicking through Google's UI):
   - Open the Google Sheet above → **Extensions → Apps Script**
   - Delete any existing code, paste the `doPost` handler (see below)
   - **Deploy → New deployment → Type: Web app → Execute as: Me → Who has access: Anyone → Deploy**
   - Copy the Web App URL and paste it back here
2. Once I have the URL, I'll rebuild the signup section as a native form (matching hero/card styling), wire it to POST to that URL, and verify a test submission lands in the sheet.
3. Set up GitHub Pages hosting for `index.html` (need a GitHub repo — will ask before creating/pushing anything).

## Apps Script code (paste into Extensions → Apps Script)
```javascript
function doPost(e) {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheets()[0];
  const data = JSON.parse(e.postData.contents);
  sheet.appendRow([
    new Date(),
    data.firstName,
    data.lastName,
    data.email,
    data.frequency,
    data.picker,
    data.notes,
    data.email
  ]);
  return ContentService
    .createTextOutput(JSON.stringify({result: 'success'}))
    .setMimeType(ContentService.MimeType.JSON);
}
```

## Separate, related thread (not yet actioned)
Lauren is also trying to identify the right SF Rec & Parks contact for a broader park cleanliness/safety initiative — has already confirmed with the district supervisor's office that no neighborhood park association exists for this park. Next step there: reach out to the SF Parks Alliance to ask about existing programs/points of contact before building anything new (avoid reinventing the wheel).
