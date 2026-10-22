# NC Regeneration Biology Symposium — website

Static site for GitHub Pages. No build step.

## Files
- `index.html` — the whole site (styles and script inline)
- `assets/logo.png` — PotentialLogo, resized for web
- `assets/emblem.png`, `assets/favicon.png` — cell/infinity mark cropped from the logo
- `assets/keynote-seifert.jpg` — keynote photo, cropped from flyerdraftV1
- `assets/nc-regen-symposium-2027.ics` — calendar file behind "Save the date → Apple Calendar / other"

## Publish on GitHub Pages
1. Create a repo (e.g. `nc-regen-symposium`) and upload `index.html` and the `assets/` folder.
2. Settings → Pages → Source: "Deploy from a branch" → `main` / root → Save.
3. The site appears at `https://<user-or-org>.github.io/nc-regen-symposium/` within a minute or two.

## Save the date
The "Save the date" button offers Google Calendar, Outlook/Office 365 and an .ics download
(Feb 18, 2027, 10am–6pm Eastern, Biotech Place). If the time or details change, edit the two
links in `index.html` (search for `calendar.google.com` and `outlook.office.com`) and the `.ics` file.
Once the site has a URL, consider adding it to the event description.

## Contact form (keeps your email off the site)
The form posts to a Google Apps Script web app that saves each message to a Google Sheet and
emails it to you. Your address lives only inside the script, never in this repo.

1. Create a Google Sheet (e.g. "Symposium contact"). Extensions → Apps Script.
2. Replace the code with the script below and set `NOTIFY` to your email.
3. Deploy → New deployment → type **Web app** → Execute as **Me** → Who has access **Anyone** → Deploy.
   Approve the permissions prompt.
4. Copy the `/exec` URL and paste it into `index.html`:
   `const CONTACT_ENDPOINT = "https://script.google.com/macros/s/.../exec";`

```js
const NOTIFY = 'you@example.edu';   // where messages are emailed
const SHEET_NAME = 'Contact';

function doPost(e) {
  const p = e.parameter;
  if (p.website) return ContentService.createTextOutput('ok');   // spam trap
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  const sh = ss.getSheetByName(SHEET_NAME) || ss.insertSheet(SHEET_NAME);
  if (sh.getLastRow() === 0) sh.appendRow(['Timestamp', 'Name', 'Email', 'Institution', 'Topic', 'Message']);
  sh.appendRow([new Date(), p.name, p.email, p.affiliation, p.topic, p.message]);
  MailApp.sendEmail({
    to: NOTIFY,
    replyTo: p.email,
    subject: '[NC Regen Symposium] ' + (p.topic || 'Message') + ' from ' + p.name,
    body: p.message + '\n\n— ' + p.name + ' <' + p.email + '>\n' + (p.affiliation || '')
  });
  return ContentService.createTextOutput('ok');
}
```

Replies go straight to the sender (Reply-To is set), so you answer from your own inbox.
If several organizers should get messages, use a comma-separated list in `NOTIFY`.

## Turning on registration (Google Sheet)
Find `const REGISTRATION` near the bottom of `index.html` and edit:

```js
const REGISTRATION = {
  status: "open",                       // "soon" | "open" | "closed"
  url: "https://forms.gle/XXXXXXXX",    // your form link
  deadline: "January 23, 2027"          // optional
};
```

Every registration button (top bar, hero, Register section) updates from this one setting.
Simplest route to a Sheet: a Google Form with Responses → "Link to Sheets".

## Still placeholders
- Sponsor logo slots
- "Parking details will be shared with registrants" (Getting there section)
- Confirm the venue address before launch
