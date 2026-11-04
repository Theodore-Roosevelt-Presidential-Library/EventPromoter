---
name: publish-facebook
description: Create or update Facebook events on the Theodore Roosevelt Presidential Library page, including cover images and ticket links. Use when publishing an event to Facebook, fixing a published Facebook event, adding a cover photo, or when a promotion run reaches the Facebook step. Covers the page-profile switch, the 14-day event cap and the Mountain-time default that Facebook gets wrong.
---

# Publishing Facebook events

~53K followers. Highest-reach channel the Library controls, and Claude can do the whole job
here including images.

## ⚠️ Step one: browse as the page, not as Matt

**This is the step everything else depends on.** From Matt's personal profile the Host
dropdown offers only his profile, and the event will publish under the wrong identity.

1. Click the avatar, top right
2. Click **Theodore Roosevelt Presidential Library**
3. Wait for the reload
4. *Then* go to `https://www.facebook.com/events/create/`

As of 2026-09, neither Meta Business Suite nor the page's own Events tab had a create-event
button — switching profiles was the only route. If that's changed, file it via
`report-channel-issue`.

If a navigation is blocked by a "Leave site?" dialog, retry with `force: true`.

## The form, in order

1. **Event name** — 100 char limit
2. **Start date** — the picker shows the current month; next month's days sit in the last row
3. **Start time**
4. **Time zone** → ⚠️ see below
5. **"+ End date and time"** to reveal end fields
6. **End time**
7. **Location** — pre-fills to 3410 Chateau Rd. For off-site events, overwrite it and pick the
   venue's own Facebook **place page** from autocomplete (e.g. "University of Mary" →
   7500 University Dr). A real place page beats a typed address — it links the event to that venue.
8. **Description**
9. **"Add tickets"** — collapsed accordion, easy to miss → **Ticket URL** = the event's own
   trlibrary.com page. Do this for every event, free ones included.
10. **Cover image** — see below
11. **Create event**

## ⏰ Timezone — Medora is MOUNTAIN

Facebook defaults to **Central**, which is wrong for every Medora event. Click the Time zone
field, select-all, type `Denver`, and pick **America/Denver**. Set it on the **start** field;
the end field follows.

Pick the *zone*, not a label. Facebook shows "MDT" in summer and "MST" in winter — both are
correct for America/Denver, and the zone handles the switch. Don't hunt for a specific label.

The event's start time itself comes from the feed with its offset attached, so take the
wall-clock time from `startDate` and let the zone do the rest.

An event page then displays in each viewer's own timezone — a Central-time viewer correctly
sees an 8 AM MDT event as 9 AM CDT. That's right, not a bug.

Bismarck events (U Mary, the Foundation office) genuinely are Central — leave those as CDT.

## 📅 14-day maximum

Facebook refuses events longer than two weeks, and it counts **hours**, not just dates.
Oct 3 9:00 AM → Oct 17 6:00 PM was rejected ("Enter an end date that's within 2 weeks of the
start date") because it's 14 days *plus nine hours*. Dates past the window are greyed out.

For a long-running exhibition: pick a two-week window and put the **true run dates in the
first line of the description**.

## 🖼️ Cover images — Claude can do these

Find the hidden file input and upload directly. Do **not** click the camera/Edit button —
that opens a native file picker Claude cannot see.

1. `find` → "file input for event cover photo upload". Identify it by **position** — it sits
   just above the "Edit event cover photo or video" button. (Ref numbers like `ref_60` are
   per-snapshot, not stable; don't hard-code one.)
2. `file_upload` with that `ref` and an absolute path
3. Wait ~5s for the preview to render
4. `find` the **Save** button (edit dialog) or **Create event** (new event) and click

Works in both the Create dialog and the Edit dialog, so covers can be back-filled onto
already-published events. Limit 10 MB per call; 1200×628 JPEGs run 50–250 KB.

Sizing and sourcing: `prepare-event-artwork`.

## After publishing

Facebook opens an **"Invite followers"** dialog offering 500 invites. **Close it.** Never
mass-invite without being asked.

The close button can be unreliable — if a click misses, the underlying page may navigate.
Safest: navigate directly to the next URL rather than hunting for the X.

## Editing a published event

`https://www.facebook.com/events/edit/<event-id>/` — but you must already be browsing as
the page, or you'll get "This page isn't available right now."

## Published event IDs

Keep these in the run log so later cycles can edit rather than duplicate. Format:

```
| Event | FB ID | When (MT) | Cover |
```

---

**If this channel misbehaves** — a selector is gone, a form rejects valid input, or a
workaround documented above stops working — use `report-channel-issue` to file it (it checks
for an existing issue first). Platforms change constantly; the fix only happens if the
divergence gets captured.
