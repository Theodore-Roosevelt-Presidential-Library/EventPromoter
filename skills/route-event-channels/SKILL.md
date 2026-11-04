---
name: route-event-channels
description: Decide which promotion channels each event should go to, based on where it happens, how soon it is, what it costs and what kind of event it is. Use after finding events and before publishing, or whenever asked where an event should be posted, which channels make sense, or whether there is still time to promote something.
---

# Routing events to channels

Not every event belongs on every channel. Sending a Medora lecture to a Bismarck calendar
wastes the slot and trains the moderator to ignore the Library.

Full channel details: `${CLAUDE_PLUGIN_ROOT}/reference/channels.md`

## Routing in four questions

### 1. Is it still promotable?

Check today's date against the event date. Then apply the lead-time gate:

| Days out | Channels worth using |
|---|---|
| 14+ | Everything |
| 7–13 | Everything except Visit Dickinson (moderation too tight). ND Tourism needs 7+. |
| 3–6 | Facebook, Google Business Profile, Prairie Public, Watford City |
| 1–2 | **Google Business Profile only** |
| Past | Nothing — report it and move on |

### 2. Where does it physically happen?

| Venue | Send to | Never send to |
|---|---|---|
| **Medora** (the Library) | Visit Dickinson, Watford City, Prairie Public, ND Tourism, Facebook, GBP *(TRPL profile)* | Bismarck-Mandan CVB, Dakota Media Access, evvnt/Tribune |
| **Bismarck** (Foundation office, U Mary, etc.) | Bismarck-Mandan CVB, Dakota Media Access, evvnt/Tribune, KFYR, ND Tourism, Facebook, GBP *(**both** TRPL and Foundation profiles)* | Visit Dickinson |
| **Elsewhere** | Judge by who would drive to it | — |

Bismarck events are **Central** time. Medora events are **Mountain**. Get this right at the
source — several platforms default to Central and will quietly mislabel a Medora event.

### 3. What kind of event is it?

The feed's `category` already sorts this for you:

| Feed category | What it is | Routing |
|---|---|---|
| `special_event` | One-off public programs | **Promote broadly.** The default slice for Facebook, Yelp and the regional calendars. |
| `specialty_tour` | Recurring guided tours | One listing describing the offering, not one per occurrence. Good for GBP and trip-planning channels. |
| `daily_activity` | Ongoing exhibitions and everyday offerings | Usually a single long-run listing. Don't flood calendars with daily repeats. |

Then the finer distinctions:

| Type | Routing note |
|---|---|
| **Lecture / talk / panel / seminar** | ⚠️ **Visit Dickinson rejects these** — their criteria exclude lectures and seminars. Everything else is fine. |
| **Exhibition / long run** | Good everywhere. Facebook caps events at 14 days — see `publish-facebook` for handling long runs. |
| **Convention / multi-day** | Everywhere. Lead with any registration deadline. |
| **Ticketed, higher price** | Worth the extra channels — these need to actually sell. |
| **Free program** | Tag as a free event where the platform has that category; it's a real filter people use. |
| **Recurring class / activity** | Low marginal value on moderated calendars. Facebook + GBP is usually enough. |

### 4. Is there artwork?

Not a gate, but it changes the order. Channels where the image carries the post — Facebook,
Dakota Media Access — are worth doing *after* art exists. Channels that are mostly text —
ND Tourism, Prairie Public — can go immediately.

See `prepare-event-artwork`.

## Priority order within a run

When time is limited, this is the order that returns the most:

1. **Google Business Profile** — instant, highest intent, works at any lead time
2. **Facebook** — largest owned audience, instant
3. **ND Tourism** — statewide, and gatekeeps ND Tourism's own promotion
4. **Email roundup** — six outlets in one pass, ~200K households via ND Living alone
5. **Prairie Public** — cleanest form, statewide
6. Everything else

## Batching

A multi-event weekend is still individual events on most platforms, but **ask before
assuming**. Seven separate programs can be:

- **Seven listings** — maximum discoverability, each surfaces on its own
- **One umbrella listing** — cleaner in a follower's feed, one image, one link
- **Hybrid** — one umbrella plus standalone listings for the ticketed ones that need to sell

There's no universally right answer. On Facebook seven posts in a short window may crowd the
feed; on ND Tourism seven entries are fine. If the operator hasn't said, ask.

## Output

For each event, a list of target channels with: channel name, why it qualifies, any
event-specific note (timezone, profile to use, category to pick), and anything blocking it
(no art, too late, wrong geography).
