# EventPromoter

A set of AI agents that help spread the word about events at the Theodore Roosevelt
Presidential Library.

Reads the Library's event feeds, works out which of the free regional channels each event
belongs on, publishes across them, and keeps a log the next run can diff against.

## Install

Claude Code / Cowork plugin. Point your plugin config at this repo, or clone it and
install locally.

## Use it

> "Promote the new events"
> "What's coming up on the calendar?"
> "Where should the Butchery Workshop be posted?"
> "Check whether our Facebook listings are still accurate"

The orchestrator is **`promote-events`**; the rest are pulled in as needed.

## What's in here

### Skills

| Skill | Does |
|---|---|
| `promote-events` | The full cycle: find → verify → route → publish → log |
| `find-events` | Reads the JSON-LD / iCal feeds; detects new and changed events |
| `route-event-channels` | Decides where each event goes — geography, lead time, type |
| `publish-facebook` | TRPL page events, covers, ticket links |
| `publish-google-business` | GBP event posts across the Library's profiles |
| `publish-nd-tourism` | Partner Portal listings, driven through the form's own APIs |
| `publish-regional-calendars` | Prairie Public, Visit Dickinson, Watford City, Bismarck-Mandan, Dakota Media Access, evvnt, Yelp |
| `draft-outreach-emails` | Newsroom roundups drafted in Outlook on the web; sent only on Matt's word |
| `prepare-event-artwork` | Recovers full-res art from the site; sizes per channel |
| `report-channel-issue` | Files a GitHub issue when a channel breaks — dedupe-checked |

### Agents

| Agent | Does |
|---|---|
| `event-scout` | Read-only inventory of upcoming events, new/changed detection |
| `listing-auditor` | Checks published listings against the truth; reports discrepancies |

### Reference

`reference/` holds the knowledge the skills lean on: the channel directory, the event-feed
spec, house style, email outlets, and the run-log template.

## Event feeds

The site publishes structured feeds — use these, don't scrape:

```
https://www.trlibrary.com/calendar/feed.jsonld
https://www.trlibrary.com/calendar/feed.ics
```

Filter with `?category=special_event` · `specialty_tour` · `daily_activity`.
`special_event` is the right slice for most promotion.

Three traps, all documented in `reference/event-feeds.md`:

- The feed lists each event **plus** its occurrences. Use the `ItemList` node for the real
  events — 11 events arrive as 37 nodes.
- **`subEvent` is truncated** to 3–4 entries. Free Final Hour shows 3 against 87 real
  occurrences. Use the parent's date range for the span, the `.ics` for every occurrence.
- Scraping the calendar pages gives an **incomplete** list, never a complete one. Don't
  conclude there are no events from a fetch.

## Run logs

Each cycle writes `events/YYYY-MM-<cycle>.md` recording what went where, with platform IDs
so later runs edit rather than duplicate. Template in `reference/log-template.md`.

## Not in scope

**Broadcast social** — Instagram, LinkedIn, X, Threads, Bluesky. Those go through Hootsuite
and `otd-social` owns that path. A Facebook *event* is a listing and is in scope; an
Instagram *post* is a promotion and is not. Ask and we'll draft copy for you to schedule.

## Boundaries

These are deliberate and the skills enforce them:

- **Never enters passwords.** Expired logins get handed back to a human.
- **Drafts email, never sends.**
- **Never mass-invites** Facebook followers.
- **Never publishes an unverified historical claim** — hands off to the `historian-panel`
  plugin's `accuracy-review` instead.

## When something breaks

Platforms change. `report-channel-issue` files it against this repo, checking for an
existing issue first so a recurring break accumulates evidence on one thread instead of
spawning duplicates. The workaround field is the valuable part — it becomes the next
version of the skill.
