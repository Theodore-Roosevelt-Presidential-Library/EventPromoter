# trlibrary.com event feeds

The site publishes machine-readable event feeds. **These are the source of truth for
discovery** — use them instead of scraping.

| Feed | URL |
|---|---|
| JSON-LD (schema.org) | `https://www.trlibrary.com/calendar/feed.jsonld` |
| iCalendar | `https://www.trlibrary.com/calendar/feed.ics` |

*Verified against the live feeds 2026-11-04.*

## Category filter

Append `?category=` to either feed:

| Value | Contains | Typical use |
|---|---|---|
| `special_event` | One-off public programs — screenings, lectures, workshops, dinners | **Facebook, Yelp and most regional calendars.** The best default. |
| `specialty_tour` | Recurring guided tours (Deep-Dive, Architecture & Sustainability, Badlands Landscape) | Google Business Profile, trip-planning channels |
| `daily_activity` | Ongoing exhibitions and everyday offerings (Free Final Hour, the Kennerly exhibition) | Long-run listings; one entry, not many |

Omit for everything.

⚠️ An unknown category returns **HTTP 400** with a plain-text body
(`Unknown category. Use one of: daily_activity, specialty_tour, special_event.`).
`json.load` on that throws. Check the status.

## Getting the real events

`@graph` contains parent events **and** their occurrences flattened as siblings. Counting
every `"@type": "Event"` node double-reports: today that's 11 real events across 37 nodes.

**Use the `ItemList` node** — it's the feed's own authoritative list of parents:

```python
import json, urllib.request

req = urllib.request.urlopen("https://www.trlibrary.com/calendar/feed.jsonld?category=special_event")
assert req.status == 200
g = json.load(req)["@graph"]

by_id = {n["@id"]: n for n in g}
lst   = next(n for n in g if n.get("@type") == "ItemList")
events = [by_id[i] for i in lst["itemListElement"] if i in by_id]
```

Three ways to tell parents from occurrences, most reliable first:

1. **`ItemList.itemListElement`** — the feed's own list. `numberOfItems` agrees with it.
2. **`superEvent`** — present on occurrences, absent on parents.
3. **`@id` suffix** — parents end `#event`; occurrences end `#event-YYYY-MM-DD`
   (date only — *not* a timestamp).

## ⚠️ `subEvent` is truncated — do not use it to enumerate showings

Parents carry a `subEvent` array, but it is **capped at 3–4 entries** and is missing
entirely on some parents. It is a preview, not the occurrence list.

| Event | `subEvent` says | Actual occurrences (.ics) |
|---|---|---|
| Free Final Hour | 3 | **87** |
| In the Room | 3 | 50 |
| T.R. Deep-Dive Tour | 4 | 20 |

An agent told to "iterate `subEvent` for one listing per showing" will publish three of
eighty-seven and report success.

**For the span, use the parent's `startDate` and `endDate`** — those are correct
(Free Final Hour runs to 2027-02-28). **For a complete occurrence list, use the `.ics`.**

## Fields

```
name · url · description · image[] · startDate · endDate
location.name · location.address.{streetAddress,addressLocality,addressRegion,postalCode}
location.geo.{latitude,longitude}
offers.{price,priceCurrency,url,availability} · isAccessibleForFree
eventStatus · eventAttendanceMode · eventSchedule · organizer · performer
inLanguage · alternateName · typicalAgeRange · subEvent[] · superEvent
```

### Timezone comes free — read the offset, don't assume the label

`startDate` is a full ISO-8601 timestamp with offset: `2026-11-04T09:00:00-07:00`.

| Offset | Could be |
|---|---|
| `-07:00` | **Mountain Standard** (MST, winter) — or Pacific Daylight |
| `-06:00` | **Mountain Daylight** (MDT, summer) — **or Central Standard** |
| `-05:00` | Central Daylight |

Mountain switches between `-07:00` and `-06:00` across the year. **`-06:00` is ambiguous
between Mountain-summer and Central-winter**, so don't infer the venue from the offset —
read `location.address.addressLocality`.

When a platform asks you to pick a timezone, choose the **`America/Denver`** zone for Medora
rather than matching a label; it handles the seasonal switch itself. The label the platform
shows (MST or MDT) will vary with the date and that's correct.

### Price

`offers.price` is a string: `"0.00"`, `"7.00"`, `"225.00"`. **Absent `offers` does not mean
free** — it means no ticketing is attached. `isAccessibleForFree` (present on some nodes) is
the cleaner signal when it exists.

### Images

`image[]` carries three renditions — `square__l`, `4_3__l`, `16_9__l`. Start from `16_9__l`
for a 1200×628 cover, or recover the full-resolution original by stripping the style
segment (see `prepare-event-artwork` — all three edits are required).

### URLs are not always under `/calendar/`

`https://www.trlibrary.com/midwinter-table` is a `special_event` living at the site root.
Don't parse slugs by assuming a `/calendar/<type>/<slug>` shape.

## The `.ics` feed

**Occurrence-expanded only.** 203 `VEVENT`s today and **zero parent rows** — every `UID`
ends `#event-<timestamp>`. No `RRULE`, no `TZID`; `DTSTART`/`DTEND` are UTC
(`20261104T160000Z`).

So:

- ❌ **Don't diff by `UID`** to detect new events — you get 203 churning rows on a rolling
  window, not 11 stable events. Use JSON-LD for identity.
- ✅ **Do use it** for the complete occurrence list that JSON-LD truncates, and for
  subscribing a calendar app.

## About scraping

The feeds replaced scraping. Two notes if you ever need the HTML:

- `/calendar/special-event` renders **no event links** server-side — it builds the list in
  JavaScript. Fetching it yields nothing useful.
- `/calendar` *does* server-render a few event links and `ld+json` blocks, but only a
  partial set.
- The homepage "Upcoming Events" section links only `special_event` items, and not all of
  them.

None of these is a complete inventory. **The feed is.** Fall back to an event page only for
what the feed omits: the room name (Barry Auditorium, Microsoft Gallery, the Store), full
body copy, and program landing pages for multi-event weekends.
