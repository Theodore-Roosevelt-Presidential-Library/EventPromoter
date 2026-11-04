---
name: find-events
description: Find events on trlibrary.com and detect what is new or changed since the last promotion run. Use when asked what events are coming up, to check the calendar for new events, to see what needs promoting, or as the first step of a promotion cycle. Reads the site's JSON-LD and iCalendar feeds, which carry dates, timezones, prices and images as structured data.
---

# Finding events

The site publishes machine-readable feeds. **Use them.** Do not scrape unless you need
something the feed doesn't carry.

Full feed reference: `${CLAUDE_PLUGIN_ROOT}/reference/event-feeds.md`

## The feeds

```
https://www.trlibrary.com/calendar/feed.jsonld     ← schema.org, richest
https://www.trlibrary.com/calendar/feed.ics        ← iCalendar
```

Filter by type with `?category=special_event`, `?category=specialty_tour` or
`?category=daily_activity`.

**`special_event` is the right default for promotion** — one-off public programs. Specialty
tours are recurring offerings and daily activities are ongoing; both are usually promoted
once, not per occurrence.

## ⚠️ Parents vs. occurrences

`@graph` lists each event **and** its occurrences, flattened as siblings. Counting every
`Event` node double-reports badly — 11 real events arrive as 37 nodes.

**Use the feed's own `ItemList`**, which enumerates exactly the parents:

```python
import json, urllib.request
req = urllib.request.urlopen(
    "https://www.trlibrary.com/calendar/feed.jsonld?category=special_event")
assert req.status == 200          # a bad category returns 400 + plain text
g = json.load(req)["@graph"]

by_id  = {n["@id"]: n for n in g}
lst    = next(n for n in g if n.get("@type") == "ItemList")
events = [by_id[i] for i in lst["itemListElement"] if i in by_id]

for e in events:
    offers = e.get("offers") or {}
    print(e["name"], e["startDate"], offers.get("price", "—"), e["url"])
```

Fallbacks if `ItemList` is ever absent: occurrences carry `superEvent` (parents don't), and
parent `@id`s end `#event` while occurrences end `#event-YYYY-MM-DD`.

## ⚠️ `subEvent` is truncated

It caps at 3–4 entries and is absent on some parents. Free Final Hour shows 3 `subEvent`
against **87** actual occurrences. Never use it to enumerate showings.

- **Span** → the parent's `startDate` / `endDate` (these are correct)
- **Every occurrence** → the `.ics` feed

## What the feed gives you

`name` · `url` · `description` · `image[]` (square, 4:3, 16:9) · `startDate` / `endDate`
with offset · full `location` + `geo` · `offers.price` · `isAccessibleForFree` ·
`eventSchedule` · `superEvent` · `subEvent[]`

**Read the timezone offset from the timestamp** — `2026-11-04T09:00:00-07:00`.
`-07:00` is Mountain **Standard**; `-06:00` is Mountain **Daylight** *and also* Central
Standard, so don't infer the venue from the offset — read `location.address.addressLocality`.
When a platform asks for a timezone, pick **America/Denver** for Medora and let it handle
the seasonal switch.

⚠️ **Absent `offers` is not the same as free** — it means no ticketing attached.
`isAccessibleForFree` is the cleaner signal where present.

⚠️ **Not every event URL is under `/calendar/`** — `/midwinter-table` sits at the site root.
Don't parse slugs positionally.

## What the feed does NOT give you

Fall back to the event page or a browser for:

- **Room name** — Barry Auditorium, Microsoft Gallery, the Store. Worth having; it makes
  listings concrete.
- **Full body copy** — the feed's `description` is a summary.
- **Program landing pages** — a multi-event weekend may have its own page
  (e.g. `/dhk-opening-weekend`) with framing the feed doesn't express.
- **Whether a price is "free" or "included with general admission"** — different things.

⚠️ **Don't scrape the calendar for an inventory.** `/calendar/special-event` server-renders
no event links at all (it builds the list in JavaScript); `/calendar` renders only a partial
set; the homepage "Upcoming Events" section links only some `special_event` items. None is
complete. **Never conclude there are no events from a fetch of these pages** — use the feed.

## Change detection

Diff against the newest file in `events/`:

- **New** — `url` not in any prior log → full promotion run
- **Changed** — name, start/end, price or location differs → update existing listings, and
  note which channels carry the stale version
- **Unchanged** — skip
- **Past** — `endDate` before today → skip

Check today's date before deciding anything is upcoming.

⚠️ **Events get retitled and moved between cycles.** The Kennerly exhibition went from
"60 Years" to "**Sixty** Years" and changed URL inside six weeks. Old URLs 301-redirect, so
published links survive — but verify rather than assume:

```bash
curl -sL -o /dev/null -w "%{http_code}  %{url_effective}\n" "<url>"
```

A 200 at a *different* `url_effective` means redirect: links work, listings are stale.

## Output

Per event: title, url, start, end, timezone offset, price, venue city, room (if fetched),
one-line summary, image URL, and status (new / changed / unchanged / past).

Hand to **`route-event-channels`**.
