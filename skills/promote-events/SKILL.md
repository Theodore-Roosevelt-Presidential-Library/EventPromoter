---
name: promote-events
description: Run a full event promotion cycle for the Theodore Roosevelt Presidential Library — find what is new on trlibrary.com, decide where each event belongs, publish across the channels, and log what was placed. Use when asked to promote the events, get the word out, push the new events out, do the monthly promotion run, or when told there are new events on the calendar.
---

# Promotion run

The orchestrator. Pulls the other skills in order and leaves behind a log that the next
cycle can diff against.

## The pipeline

```
find-events  →  verify  →  route-event-channels  →  publish  →  log
```

### 1. Find

Use **`find-events`**, which reads the site's JSON-LD feed:

```
https://www.trlibrary.com/calendar/feed.jsonld?category=special_event
```

`special_event` is the right default. Don't scrape the calendar pages — they don't
server-render a complete list.

Diff against the newest file in `events/` to separate new from changed from already-done.

**First run, or no log in `events/`?** Everything looks new, including long-running
exhibitions and daily activities that are almost certainly already listed everywhere.
Don't blast them. Inventory first, show the operator what you found, and agree the scope
before publishing anything.

### 2. Verify before you publish

Two different checks, both cheap:

**Arithmetic.** Any age, span, count or "X years later" gets checked against the Library's
own page. This has already failed once in production — see the Kennerly note in
`${CLAUDE_PLUGIN_ROOT}/reference/house-style.md`.

**History.** Anything beyond date math — a claim about T.R., a quote, an attribution, a
"first" — hands off to the **historian-panel** plugin's `accuracy-review` skill. Don't
adjudicate historical claims inside a promotion run.

Also re-check that previously published URLs still resolve; events get retitled and moved
between cycles.

### 3. Route

Use **`route-event-channels`**. Geography, lead time, event type, artwork. Produces a
per-event channel list with the reason and any blockers.

### 4. Ask, if the shape is genuinely open

Two decisions are worth one short question rather than a guess:

- **A multi-event weekend** — seven separate listings, one umbrella listing, or a hybrid?
- **Scope** — everything, or Facebook-and-Google first with the rest after approval?

If the operator has already said, don't re-ask.

### 5. Check it isn't already there

The log is the first defence against duplicate listings, but it isn't sufficient — a run
can be interrupted, a log can go unmerged, two people can work the same cycle. **Before
creating a listing, check the platform itself** where that's cheap:

| Channel | Check |
|---|---|
| Facebook | `facebook.com/TRPresidentialLibrary/events` — scan upcoming titles |
| Google Business Profile | Search `Theodore Roosevelt Presidential Library Medora events` |
| ND Tourism | The account's Events list |
| Moderated calendars | **No ID to check.** Rely on the log, and when uncertain say so rather than risking a duplicate submission. |

If a listing already exists but is **wrong**, edit it — don't create a second one. That's
what the IDs in the log are for.

### 6. Publish

In priority order, because runs get interrupted:

| # | Channel | Skill |
|---|---|---|
| 1 | Google Business Profile | `publish-google-business` |
| 2 | Facebook | `publish-facebook` |
| 3 | ND Tourism | `publish-nd-tourism` |
| 4 | Email roundup | `draft-outreach-emails` |
| 5 | Regional calendars + Yelp | `publish-regional-calendars` |

Artwork first where the image carries the post: `prepare-event-artwork`.

**When a channel misbehaves** — a selector is gone, a form rejects valid input, a documented
workaround stops working — use **`report-channel-issue`**. It checks for an existing issue
before filing so recurring breaks accumulate on one thread. Don't just note it in the log
and move on; platforms change constantly and the fix only happens if it's captured.

### 7. Log

Write `events/YYYY-MM-<cycle-name>.md` — see `${CLAUDE_PLUGIN_ROOT}/reference/log-template.md`.

Record **IDs** (Facebook event IDs, ND Tourism numeric IDs) — the next cycle needs them to
edit rather than duplicate.

## Running on a schedule

If invoked unattended (a scheduled task rather than a person asking):

- **Don't ask the clarifying questions** in step 4 — there's nobody to answer. Default to
  one listing per event, and to the Facebook + Google + ND Tourism core.
- **Don't publish on a first run with no log.** Inventory, write the log, and report what
  *would* be published. A human approves the first cycle.
- **Monthly is the natural cadence** — the log filename assumes it, and ND Living's
  10th-of-the-prior-month deadline is the one hard date in the calendar. A run in the first
  week of the month catches it.

## Credentials and boundaries

- **Never enter passwords.** ND Tourism in particular expires between runs; ask Matt to sign
  in and wait.
- **Never click Send** on an email unless Matt says to after seeing the draft.
- **Never mass-invite** Facebook followers. Close that dialog.
- **Never publish a historical claim you could not verify.** Flag it instead.

## Reporting back

Lead with what is live, then what is blocked and who has to unblock it. Be specific about
manual steps — GBP images, ND Tourism photos, unsent drafts — because those are the things
that quietly never happen.

Say plainly when something could not be done and why. "GBP images need a native file picker
I can't drive" is useful; silence is not.
