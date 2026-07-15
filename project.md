# Friends of Parque Niños Unidos — Park Cleanup Site

**Status:** Live at https://friendsofsfparks.org (custom domain, 2026-07-11). Fully working, no known open items.

**Brand note:** Lauren bought both friendsofsfparks.com and .org intending this to grow into a citywide (not just Mission District) park stewardship brand — this Parque Niños Unidos page is the first park, more may be added later. The on-page content/title still reads "Friends of Parque Niños Unidos" specifically; revisit copy/framing if/when a second park is added.

---

## What this is
A neighbor-led effort to keep Parque Niños Unidos (3090 23rd St, Mission District) clean via a 15-minutes-at-a-time volunteer signup. Evolved from the ideas in [../mission-parks.md](../mission-parks.md).

## Files
- `index.html` — single-page bilingual (EN/ES) site: hero, how-it-works, FAQ, signup section
- Git repo pushed to https://github.com/queenbirdie/parque-ninos-park (public, required for free GitHub Pages)

## What's done
- Native signup form built in `index.html`, replacing the Google Form iframe — matches the parchment/Fraunces/dark-signup-box aesthetic, bilingual (EN/ES) like the rest of the site
- Apps Script Web App deployed (Lauren completed the OAuth consent step herself; hit a transient `invalid_client` 401 on first attempt, fixed by reloading the Apps Script editor and retrying)
- Verified end-to-end: submitted a real test entry through the rendered form in a local preview, confirmed it landed correctly in the Google Sheet, then deleted the test row
- Pushed to GitHub and published via GitHub Pages (main branch, root):
  - Dedicated SSH key generated for this Mac and added to Lauren's GitHub account (`queenbirdie`) — Lauren added the key herself via GitHub's UI
  - Lauren created the `parque-ninos-park` repo herself (public, no README) via GitHub's UI
  - I pushed the local commits and enabled Pages (Settings → Pages → Deploy from branch → main → /root)
  - Confirmed live, no console errors
- Custom domain wired up: friendsofsfparks.org (primary) + friendsofsfparks.com (redirects to .org), both bought on Namecheap (account: rosieqofc)
  - DNS: 4 A records on `.org` apex → GitHub Pages IPs (185.199.108/109/110/111.153) + `www` CNAME → queenbirdie.github.io; `.com` uses an unmasked URL Redirect Record → https://friendsofsfparks.org
  - GitHub Pages custom domain set to friendsofsfparks.org, DNS check passed, HTTPS enforced and certificate issued
  - Hit a snag: Namecheap session expired mid-edit (session had been idle too long), which silently failed the first save attempt — fixed by logging back in and redoing the DNS edits

**Linked Google Sheet (backs the site's Apps Script):** https://docs.google.com/spreadsheets/d/11BU8vYH6DaOBcNt1fE_WJmJhqzonHXL_PoGQAHwDChE/edit?resourcekey=&gid=573077395#gid=573077395 — has 4 tabs:
  - `Form Responses 1` — raw log of every site form submission (append-only, don't edit)
  - `Roster` — clean master list of everyone who's expressed interest (auto-populated, see below)
  - `Volunteer sign up` — a separate pre-existing day-by-day grid (Date/Name/Phone/Email/Cross Streets/Notes) for actual day assignments, unrelated to the form/Roster automation
  - `About`

**Web App URL (in index.html):** `https://script.google.com/macros/s/AKfycbypxTjfmGaJM7hJ5eIHjuZ17Q_Y3_Aw1DidsXgf9qNtiOkxX4yll966pgBS_XR-J5dUIA/exec` (same URL since first deploy; code now at Version 6, deployed 2026-07-13)

**Columns differ slightly between the two sheets** (both hold the same data, just different layouts):
- `Form Responses 1`: Timestamp, First Name, Last Name, Email, Phone, *Cross Streets (hidden, always blank)*, *Has Picker (hidden, always blank)*, Notes
- `Roster`: Date Signed Up, First Name, Last Name, Email, Phone, Notes

Note: `Form Responses 1`'s entire sheet is linked to the site's original Google Form — Google Sheets blocks deleting *any* column there (tried deleting Cross Streets too; same "Cannot delete column with form data" error), so both unused columns are just hidden and permanently blank going forward. `Roster` has no such restriction, so both columns were deleted outright there. The Apps Script writes a different-length row to each sheet to account for this (see code below). Any pre-existing "Has Picker" / "Frequency" answers from before these fields were removed were preserved by folding them into that row's Notes field rather than being discarded.

## Operations / volunteer coordination
- **The site form is an expression of interest, not a day-claim.** People fill it out to say "I'm in"; it does not ask for a specific date.
- **Automation (2026-07-13):** Every form submission now writes to *both* `Form Responses 1` (raw log) and `Roster` (clean master list) in the same POST — see updated Apps Script below. Verified with a real test submission that landed correctly in both tabs, then cleaned up.
- **Follow-up flow:** After someone appears in the Roster, Lauren personally follows up to actually assign them a day — that's a separate, manual step using the `Volunteer sign up` tab in the main spreadsheet. (2026-07-13: the earlier standalone day-grid sheet, https://docs.google.com/spreadsheets/d/1zFwBV1Mnpv6pTJfLkZ1p_fvTJLzeb2Rj7uta4lx-pbk/edit, has been renamed "DNU — do not use" by Lauren; the `Volunteer sign up` tab is now the single source of truth for day assignments.)
- **WhatsApp group (not yet created):** planned for (1) finding backup coverage when a volunteer's schedule changes, and (2) organizing occasional in-person get-togethers to build community among volunteers. Not set up via any connected integration — WhatsApp personal isn't available through Rube/Composio (see [[reference_rube]]), so this is on Lauren to create manually.

## Next steps
1. Share the live link (friendsofsfparks.org) with neighbors (Nextdoor, building group chats, WhatsApp) — see [[project_community_building]] for existing distribution channels.
2. When/if a second park is added, revisit whether the homepage should introduce the citywide "Friends of SF Parks" brand before drilling into individual park pages.
3. Create the volunteer WhatsApp group (Lauren, manually) and decide whether to link the sign-up sheet from within it or keep it to 1:1 follow-ups only.
4. Draft and wire up a confirmation email (via `MailApp.sendEmail` in the Apps Script `doPost`) so signups get an automatic "we got your request" reply — bilingual copy drafted 2026-07-13, awaiting Lauren's sign-off before implementing.

## Wording changes log
- 2026-07-11: Replaced hardcoded "Open daily 6am–10pm" with a link ("Check current hours") to the official SF Rec & Parks page (https://sfrecpark.org/facilities/facility/details/Parque-Ninos-Unidos-361) — avoids the site showing stale/wrong hours if the park's posted hours change. Official hours at time of writing were 5am–midnight (restrooms have separate seasonal hours), which didn't match the old placeholder text.
- 2026-07-11: Changed address from "3090 23rd St" to "23rd and Treat" to match the cross-street format used on the official SF Rec & Parks listing.
- 2026-07-11: Rebalanced copy away from "no commitments, 15 minutes, that's it" framing now that there's a real day-based sign-up sheet + volunteer group for coverage swaps. Changed: hero tagline ("one day, one neighbor at a time" vs. "15 minutes at a time"), "What we're not" card, signup box subtext, form success message, and two FAQ answers (missing your day → post in the group for a swap; how else to help → mentions informal volunteer get-togethers).
- 2026-07-13: Form fields updated — added Phone and Nearest cross streets, removed the "How often can you pick up trash?" frequency question (Lauren will ask that during her personal follow-up instead). Apps Script and both sheets (Form Responses, Roster) updated to match the new field set; verified end-to-end with a real test submission.
- 2026-07-13: Removed the "Do you have a picker?" question from the form too. Same pattern as frequency — Lauren will ask during personal follow-up. Google Sheets wouldn't allow deleting the "Has Picker" column in `Form Responses 1` (it's tied to the original Google Form), so that column is now hidden and permanently blank there; deleted outright in `Roster` since no such restriction applies there.
- 2026-07-13: Copy revision pass (12 spots): dropped "one day, one neighbor at a time" from the hero tagline (now just ends at "...full of life."); reworded "Cleanliness breeds care. Care breeds community." into a friendlier single sentence; fixed an EN/ES typo ("15 minutos" was showing in the English "What you need" tag); shifted "volunteer group" → "volunteer chat" and "Lauren personally" → "the organizing team" throughout (What we're not card, signup subtext, form success message, two FAQ answers); removed the "Organized by Lauren Rosenthal Turon" byline from the signup box (dead `.organizer` CSS rule also removed) — Lauren said attribution will be handled via FAQ instead; softened/expanded the two FAQ answers about SF Rec & Parks and Parks & Rec's role; added an encouraging line to the "can't make my day" FAQ; added a safety parenthetical to the kids FAQ; reworded "How else can I help?" to drop the Nextdoor mention and point to "the organizing team" instead of Lauren by name.
- 2026-07-13: Removed Lauren's name from the remaining two spots (footer byline, JS network-error fallback — now points to friendsofsfparks@gmail.com instead). Added a new closing FAQ, "Who is on this organizing team? And how do I get involved?", with Lauren's own personal introduction (this is the one intentional place her name still appears) and a public contact email (friendsofsfparks@gmail.com, as a mailto: link). Added matching CSS (`.faq-a a`, `.faq-a+.faq-a` spacing) since this FAQ answer spans two paragraphs — same span+anchor pattern used for the hero-sub hours link, needed because `setLang()` swaps `.textContent` wholesale and can't contain nested HTML within a single `data-en`/`data-es` attribute.
- 2026-07-13: Tightened that same FAQ per Lauren's request ("shorter, less self-congratulatory") — cut the "others come up to thank me" line, condensed the two-clause identity intro ("for those who frequent... for those who live...") into one sentence, and added a warm sign-off ("I'll see you at the park!"). Went through one round of Claude-drafted proposal → Lauren's own final wording before landing here.
- 2026-07-13: Removed "Nearest cross streets" from the form too — Lauren wants the initial ask kept as simple as possible; she'll ask this during personal follow-up like frequency/picker. Form now asks just First name, Last name, Email, Phone, and optional notes.
- 2026-07-13: Another simplification pass: removed "Takes 30 seconds." from the signup box subtext; reworded "No picker? We'll get you one — just note it when you sign up." → "No picker? No problem — we got you!"; simplified the "1, 2, 3" how-it-works section to just the three titles (Sign up / Show up / Pick up), dropping the descriptive sentence under each and renaming step 3 from "15 minutes" to "Pick up"; changed the hero-sub link text from "Check current hours" → "website" (same destination, official SF Rec & Parks page).
- 2026-07-13: Added favicon / home-screen icon support. New `icons/` folder (`icon.svg`, `apple-touch-icon.png` 180×180, `icon-512.png`, `icon-192.png`, `favicon-32.png`, `favicon-16.png`) plus `manifest.json` for "Add to Home Screen." Icon design: dark-brown (`#2C1A0E`) rounded-square background with a simple 3-lobe tree (green canopy `#7BC84A`/`#4CAF82`, tan trunk `#C4A882`) — colors pulled from the site's existing palette. Built by rendering the SVG in-browser and rasterizing via macOS `qlmanage -t` (no SVG-to-PNG converter was installed on this machine), then resizing with `sips`. `<link rel="icon">`/`<link rel="apple-touch-icon">`/`<link rel="manifest">` tags + `theme-color` meta added to `index.html`'s `<head>`.
- 2026-07-13: Lauren couldn't find real photos of the park that fit the site's look, so asked for graphic-style imagery instead to convey "playground for families," not just a green space. Added an inline SVG illustration banner (rolling ground, trees, a slide, a seesaw with two figures, sun) between the hero card and the "1, 2, 3" steps section, using the same flat/rounded style and palette as the favicon and hero dot-grid. First version was just trees/bench/two standing figures — Lauren asked for a slide and/or seesaw specifically so it reads as a playground, not a generic park; added both.
- 2026-07-13: **Corrected an unverified factual claim.** The "Why we're doing this" section had said the park was built by neighbors who "spent a decade advocating" for it — Lauren asked where that came from and neither of us could find any source for it (traced it back to the very first commit of the site, from an earlier session; no record of her having provided that detail). Removed the specific historical claim since it couldn't be confirmed; the section now just describes the park's present-day role ("a neighborhood park where children play and families gather") without asserting unverified history. **Lesson: don't add specific factual/historical claims to site copy without an explicit source from Lauren — flag anything that reads as a factual assertion for her confirmation before it ships, especially on a public-facing page.**
- 2026-07-13: Illustration banner fix — the slide and seesaw overlapped visually and read as confusing/ambiguous, so removed the slide entirely and restored the bench. Also discovered the two seesaw riders weren't actually seated at the plank's true (rotated) endpoints — one appeared to float above the plank rather than ride it; recalculated the rotated-plank endpoint coordinates and repositioned both riders so they sit correctly on the high/low ends. Added a parent (sitting on the bench) and a stroller with a baby, per Lauren's request to convey "playground for families."
- 2026-07-13: Reworded two FAQs to Lauren's own wording: "How else can I help?" (now points to the signup form or friendsofsfparks@gmail.com as a mailto link) and the closing organizing-team FAQ, retitled "Tell me more about this organizing team and how it came to be!" with Lauren's own bio text (dropped the older "for now" / "get involved" framing from the previous version).
- 2026-07-13: Swapped the bench's stroller for a second, smaller figure seated next to the adult — the stroller read as confusing/unclear (its handle faced away from the adult, so it didn't visually connect as "being pushed by this person"). Illustration banner now final: two trees left, bench with parent + child, seesaw with two kids on opposite ends, tree + sun right.
- 2026-07-13: Generalized the organizing-team FAQ's location detail from "near 23rd and Folsom" to "around the Mission" — Lauren flagged that pairing a specific physical description with a specific cross-street was more identifying than intended for a public page.
- 2026-07-13: Resolved the redundant day-grid tracker — Lauren renamed the standalone sheet (https://docs.google.com/spreadsheets/d/1zFwBV1Mnpv6pTJfLkZ1p_fvTJLzeb2Rj7uta4lx-pbk/edit) to "DNU — do not use." The `Volunteer sign up` tab in the main spreadsheet is now the single source of truth for day assignments.
- 2026-07-13: Finalized confirmation-email copy after Lauren's edits — dropped "We've received your info and" (redundant with "we'll follow up"), and simplified the signature from "The Friends of Parque Niños Unidos organizing team / friendsofsfparks@gmail.com" to just "Friends of Parque Niños Unidos." Spanish translation updated to match. Wired into the Apps Script as Version 7 (see code block below) — awaiting Lauren to paste in and deploy.

## Apps Script code (Version 13, drafted 2026-07-14 — not yet deployed, see below)
```javascript
function doPost(e) {
  const ss = SpreadsheetApp.getActiveSpreadsheet();
  const data = JSON.parse(e.postData.contents);
  const formRow = [new Date(), data.firstName, data.lastName, data.email, data.phone, '', data.notes];
  ss.getSheets()[0].appendRow(formRow);
  const rosterRow = [new Date(), data.firstName, data.lastName, data.email, data.phone, data.notes];
  const rosterSheet = ss.getSheetByName('Roster');
  if (rosterSheet) { rosterSheet.appendRow(rosterRow); }

  if (data.email) {
    const subject = 'Thanks for signing up to help at Parque Niños Unidos!';
    const body = `Hi ${data.firstName},

Thanks so much for signing up to help keep Parque Niños Unidos clean! We'll follow up shortly to get you on the schedule, and also add you to our volunteer WhatsApp chat. If you don't have a trash picker, don't worry — we'll get you sorted.

Really appreciate you pitching in — neighbors like you are what make this work.

Friends of Parque Niños Unidos

---

Hola ${data.firstName},

¡Muchas gracias por registrarte para ayudar a mantener limpio el Parque Niños Unidos! Te contactaremos pronto para agregarte al calendario y también al chat de voluntarios en WhatsApp. Si no tienes un recogedor de basura, no te preocupes — te conseguiremos uno.

Agradecemos mucho que te sumes — vecinos como tú son los que hacen esto posible.

Friends of Parque Niños Unidos`;
    try {
      GmailApp.sendEmail(data.email, subject, body, { from: 'friendsofsfparks@gmail.com', name: 'Friends of Parque Niños Unidos', bcc: 'lrturon@gmail.com' });
    } catch (err) {
      let errorSheet = ss.getSheetByName('Errors');
      if (!errorSheet) {
        errorSheet = ss.insertSheet('Errors');
        errorSheet.appendRow(['Timestamp', 'Email attempted', 'Error']);
      }
      errorSheet.appendRow([new Date(), data.email, err.toString()]);
    }
  }

  return ContentService.createTextOutput(JSON.stringify({result: 'success'})).setMimeType(ContentService.MimeType.JSON);
}
```
Note: editing this code alone doesn't update the live `/exec` URL — must deploy a new version via Manage Deployments → pencil icon → Version: New version → Deploy. Sends a bilingual (EN + ES) confirmation email right after the sheet writes succeed. **Version 8 (2026-07-13):** Version 7's `doPost` failed outright in production — confirmed via the new `Errors` tab (added in v8) that the real cause was a missing `https://www.googleapis.com/auth/script.send_mail` authorization (a plain "Deploy new version" never triggers Google's consent prompt for a newly-added scope; fixed by manually running a throwaway function once to trigger it). Also wrapped the email send in try/catch so a future email failure can never block the sheet writes / roster entry again. **Version 9 (2026-07-14):** Lauren wanted the email to actually originate from friendsofsfparks@gmail.com rather than her personal Gmail. Added `{ from: 'friendsofsfparks@gmail.com', name: '...' }` to `MailApp.sendEmail` — confirmed friendsofsfparks@gmail.com is a verified "Send mail as" alias under lrturon@gmail.com (the account that owns/runs this script), but the email still sent as lrturon@gmail.com; `name` applied, `from` didn't. **Version 10 (2026-07-14):** `MailApp.sendEmail`'s `from` option doesn't reliably honor personal Gmail Send-As aliases even when verified — switched to `GmailApp.sendEmail`, which is the API that correctly supports sending as a verified alias. Requires a new one-time authorization (the `gmail.send` scope is broader than `script.send_mail`, so expect a more expansive-looking permission screen — same "run a throwaway function once" pattern as before, see deploy steps). **Version 11 (2026-07-14):** Trimmed the email copy per Lauren's edit — dropped "help you pick a day (or days) that work for you" in favor of "get you on the volunteer schedule," tightened the picker line, changed the closing line to "Really appreciate you pitching in," and dropped the em dash before the sign-off ("Friends of Parque Niños Unidos" now stands alone, not "— Friends of..."). Spanish translation updated to match. **Version 12 (2026-07-14):** Removed the redundant first "volunteer" — "get you on the volunteer schedule" → "get you on the schedule" (the sentence already says "volunteer WhatsApp chat" right after). Spanish updated to match: "calendario de voluntarios" → "calendario". **Version 13 (2026-07-14):** Added `bcc: 'lrturon@gmail.com'` so Lauren gets a copy of every confirmation email — gives her real-time visibility into signups via her inbox, not just by checking the sheet.

## Separate, related thread (not yet actioned)
Lauren is also trying to identify the right SF Rec & Parks contact for a broader park cleanliness/safety initiative — has already confirmed with the district supervisor's office that no neighborhood park association exists for this park. Next step there: reach out to the SF Parks Alliance to ask about existing programs/points of contact before building anything new (avoid reinventing the wheel).
