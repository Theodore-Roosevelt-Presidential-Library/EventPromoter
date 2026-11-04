---
name: draft-outreach-emails
description: Draft the event roundup emails to regional newsrooms and calendar editors in Outlook on the web under matt@trlibrary.com. Use when a promotion run reaches the email step, or when asked to email the stations, send listings to ND Living or InForum, or do the newsroom outreach. Sends only when Matt says to.
---

# Newsroom and calendar email

Six outlets, one message each, about fifteen minutes total. Best reach-per-minute of any
channel — ND Living alone reaches roughly 200,000 households.

Outlet list and addresses: `${CLAUDE_PLUGIN_ROOT}/reference/email-outlets.md`

## ⚠️ Draft first, send only on Matt's word

Create drafts in **Outlook on the web under matt@trlibrary.com**, using the Chrome browser
tools. Matt reviews them.

Do not click **Send** until Matt tells you to send, in this conversation, after seeing the
drafts. If he does, send only the drafts he approved and confirm them in Sent Items.

### Outlook mechanics

- **New message:** `https://outlook.office.com/mail/deeplink/compose`. Confirm the From line
  reads `matt@trlibrary.com`.
- Click **To**, type the address followed by `;`, then click the subject and the body in
  turn. Take a fresh screenshot before each click, since the layout shifts.
- **Save:** `cmd+s`, then wait for "Draft saved." A "Leave site?" block means the save has
  not finished. Open the next message in a new tab rather than forcing the navigation.
- **Review:** `https://outlook.office.com/mail/drafts`.
- ⚠️ **Sending from the Drafts folder opens the next draft in the reading pane.** That draft
  is someone else's work in progress. Don't touch it. Go straight to **Sent Items**.

## Deadlines

**North Dakota Living closes on the 10th of the prior month.** It's the biggest-reach
outlet on the list and the only one with a hard deadline — check the date first and lead
with ND Living if the window is closing.

## Geography matching

Don't blast all six with everything:

- **Medora events** → all six
- **Bismarck events** → ND Living, InForum, KFYR. Skip Glendive, KQCD, KUMV.
- **Marquee events** → a calendar listing and a segment pitch are *different asks*. Pitch a
  story angle to KFYR and KX assignment desks separately; don't bury it in a listings email.

## Shape of the message

Short. Warm opening, clean listing block, link, offer of art or an interview. Full template
in `reference/email-outlets.md`.

Two things to get right:

**Real gratitude, not boilerplate.** These are small newsrooms running the Library's
listings for free. One specific sentence beats three generic ones.

**One line per event that says what it actually is.** "An evening of firsthand accounts from
the unit that keeps presidents connected in a crisis" earns a slot. "Join us for a special
program" does not.

## Before drafting

Verify every date, time and price against the live event page — see `find-events`. A wrong
time in an email to 200,000 households cannot be edited after it ships.

Verify the **InForum address** (`calendar@forumcomm.com`) against the paper's own site. It
came from a search result, not from Forum Communications directly, and has never been
confirmed.

## Logging

Record that drafts were created and which outlets they target — then flag clearly in the
run summary that **they are sitting unsent in Outlook** and need Matt. Drafts that nobody sends
are the most common silent failure in this whole pipeline.

---

**If this channel misbehaves** — a selector is gone, a form rejects valid input, or a
workaround documented above stops working — use `report-channel-issue` to file it (it checks
for an existing issue first). Platforms change constantly; the fix only happens if the
divergence gets captured.
