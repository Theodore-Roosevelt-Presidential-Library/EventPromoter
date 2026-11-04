---
name: publish-nd-tourism
description: Create or update event listings in the North Dakota Tourism Partner Portal (partners.ndtourism.com). Use when publishing events to ND Tourism, updating a tourism listing, or when a promotion run reaches the ND Tourism step. Includes a scripted approach that fills the form through its own jQuery APIs, which is far faster than clicking and avoids the masked zip and phone bugs.
---

# Publishing to the ND Tourism Partner Portal

Statewide reach, and it's how ND Tourism decides what *it* promotes in its own marketing.
Worth more than its raw traffic suggests.

- **Login:** `https://partners.ndtourism.com/login` — the session has expired on most runs
  so far. **Expect to ask Matt to sign in.** Never enter credentials yourself.
- **Account dashboard:** `/accounts/<account-id>` · **Create:** `/accounts/<account-id>/events/create`
- **Review:** up to 48 business hours before anything appears on ndtourism.com

## 🚀 Script the form — don't click it

The form is jQuery-based and every awkward widget exposes an API. Driving it with
`javascript_tool` is dramatically faster than clicking *and* sidesteps two field-masking bugs
that have corrupted data on past runs. Seven events went in cleanly this way in one sitting.

### The pieces

| Field | How |
|---|---|
| **Address / city / zip / Publish-under** | Click the **Pick** dropdown option containing `3410 Chateau Road`. It auto-fills all four. Use a real click, since a scripted one may not fill them. **This is the fix for the masked-zip bug** — typing the zip produces `_____-5864`. Never type it. |
| **Date** | `$(dateInput).data('daterangepicker').setStartDate(moment('MM/DD/YYYY','MM/DD/YYYY'))` then `setEndDate(...)`, then `.trigger('change')` |
| **Hours** | Uncheck **All Day** first (it's the first unnamed checkbox), then `$(input).timepicker('setTime','3:00 PM')` |
| **Commit the date** | Click **Add Date**, wait ~2s, confirm the row appears in the table |
| **Phone** | `$(ph).inputmask('remove')` **first**, then set `(701) 892-6070` directly |
| **Description** | wysihtml5 editor — set the `textarea[name=description]` value **and** write `iframe.wysihtml5-sandbox`'s `body.innerHTML` |
| **Categories** | `input[name="categories[]"][value="<id>"]` then `.click()` |
| **Rates** | Fill the Name + Rate inputs, then click **Add Rate**. Rate is numeric only — no `$`, written like `7.00`. With several rates, target the last `js-rate-value` input. |

### ⚠️ Two traps that fail silently

**Times:** plain value-setting on the hour fields does **not** stick. The timepicker widget
keeps its own state, and you will silently get the default 8:00 AM–5:00 PM. Always use
`timepicker('setTime', ...)` and always confirm the committed row reads the times you meant.

**Phone:** typing digits into the live mask produces garbage — `(701) -89-2-60`. Remove the
mask first, then set the full formatted string.

### Category IDs

| Category | ID | | Category | ID |
|---|---|---|---|---|
| America's 250th | 112 | | Outdoor Events | 44 |
| Concerts & Live Music | 42 | | Seasonal Events | 45 |
| Culinary Experience Events | 118 | | Shows & Performances | 46 |
| **Exhibits & Displays** | **43** | | Sporting Events | 47 |
| Festivals & Fairs | 41 | | **Teddy Experiences** | **115** |
| **Free Events** | **108** | | Top Events (Legendary, Featured) | 40 |
| Wellness | 116 | | Workshops & Classes | 49 |

The IDs above are verified portal data. What follows is editorial judgment from past runs,
not a portal rule: **Teddy Experiences (115)** has been applied to every TRPL event so far as
the Library's signature category, paired with **Exhibits & Displays (43)** for exhibition
programming or **Free Events (108)** for free ones. Revisit if ND Tourism changes guidance.

### Submitting

The **Create** button is `button[type=submit]` whose text is exactly "Create". It navigates,
so click it in its own call — a long `await` after it throws *"Inspected target navigated
or closed."*

### Times are exact here

4:15 PM works fine, unlike Google Business Profile's 30-minute dropdown. No need to round.

## Editing an existing listing

Each panel — Event Information, Event URLs, Phones, Emails — has its own inline **Edit**
toggle and its own **Save**.

⚠️ Changing the event name does **not** change the page `<h1>` or `<title>` until ND
Tourism's review syncs. Confirm by reading the **"Event name"** row, not the heading.

## Photos

Added post-create via each event's **Photos** section. Not yet automated — a manual step.

## Log the IDs

Every created event returns a numeric ID (e.g. 14818). Record it — editing later needs it,
and it's the proof the submission landed.

---

**If this channel misbehaves** — a selector is gone, a form rejects valid input, or a
workaround documented above stops working — use `report-channel-issue` to file it (it checks
for an existing issue first). Platforms change constantly; the fix only happens if the
divergence gets captured.
