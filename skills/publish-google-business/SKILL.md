---
name: publish-google-business
description: Post events to Google Business Profile for the Library's locations so they surface in Maps, the knowledge panel and "things to do near me" searches. Use when publishing to Google Business Profile or GBP, posting a Google event post, or when a promotion run reaches the Google step. The only channel fast enough for an event a few days out.
---

# Publishing Google Business Profile event posts

Highest-**intent** channel available — these people are already deciding where to go. Posts
go live in minutes and show up in Google search for the Library.

- **URL:** `https://business.google.com/locations`
- **Login:** Matt's Chrome session. Holds up well between runs (unlike ND Tourism).

## The profiles

| Profile | Status | Use for |
|---|---|---|
| Theodore Roosevelt Presidential Library | Verified | **All Medora events** |
| TRPL Foundation (Bismarck) | Verified | **Bismarck events — post here too** |
| Salt+Scoria | Verified | Café-specific, rarely events |
| Eagle Ridge Lodge | Needs verification | — |
| TRPL Field Office | Needs verification | — |

## Posting

From the Businesses list, click the **post icon** on the right of the TRPL row — as of
2026-09 that's the third icon, a document with a plus, not the camera. Then the **Event** tab.

Fields in order:

1. **Title** — ⚠️ **58 characters.** The tightest limit of any channel. A Facebook title
   usually won't fit; rewrite rather than truncate.
2. **Description** — 1,500 chars
3. **Start date** → **Start time** → **End date** (auto-fills) → **End time**
4. **Add a button** → **Learn more** → the event's own trlibrary.com page

Then **Post**.

## ⚠️ Three quirks that will cost you a post

**Field positions shift with description length.** The description textarea grows as you
type, pushing the date fields down by a variable amount. Always take a fresh screenshot
after filling the description and *before* clicking into the date fields. Do not reuse
coordinates between posts — this silently drops dates into nothing.

**Times are 30-minute increments — but the field accepts free text.** The dropdown only
offers :00 and :30. For a 4:15 start, type `4:15 PM` and click away; it holds.

**Never press Escape.** Escape closes the whole post dialog and offers *"Discard changes?"* —
click **Cancel** to keep your work. This matters most when dismissing the image picker.

## 🖼️ Images cannot be automated

"Select images and videos" opens a **native OS file picker** with no DOM `input[type=file]`.
Unlike Facebook, there is nothing for `file_upload` to target. GBP post images are a manual
step — tell the operator rather than burning calls on it.

## After posting: the "Copy post" dialog

GBP offers to copy the event to other profiles you manage.

- **Medora event** → skip it
- **Bismarck event** → check **TRPL Foundation** and post

⚠️ This dialog is **not in the accessibility tree** — `find` and JavaScript can't reach its
buttons, and clicking by coordinate is unreliable (a stale click lands on the page behind and
navigates away). **The post is already saved by the time this dialog appears.** The safe move
is to navigate to `business.google.com/locations` for the next post and ignore it.

If you genuinely need to copy to the Foundation profile, click the checkbox and Post
carefully from a fresh screenshot — or do that profile as a separate post.

## Verifying

Search Google for `Theodore Roosevelt Presidential Library Medora events`. Live posts appear
in an Events block in the results within minutes.

---

**If this channel misbehaves** — a selector is gone, a form rejects valid input, or a
workaround documented above stops working — use `report-channel-issue` to file it (it checks
for an existing issue first). Platforms change constantly; the fix only happens if the
divergence gets captured.
