# Channel Directory

Every free channel the Library uses to promote events, what it's good for, and what it costs
in effort. Ordered by value per minute spent.

Legend: **Instant** = live within minutes · **Moderated** = human review, allow lead time ·
**Email** = a person reads it

> Source events from the site feeds, not by scraping:
> `https://www.trlibrary.com/calendar/feed.jsonld?category=special_event`
> See `reference/event-feeds.md`. **`special_event` is the right slice for most channels** —
> notably Facebook and Yelp. Specialty tours and daily activities are recurring or ongoing
> and usually warrant one listing rather than many.

---

## Tier 1 — Do these first

### Facebook Events (TRPL page)
- **Reach:** ~53K followers. The single highest-reach channel the Library controls.
- **Speed:** Instant
- **Login:** Matt's Chrome session; **must switch profile to the page** (see `publish-facebook`)
- **Good for:** Everything. Ticketed and free, single programs and multi-day.
- **Limits:** 14-day maximum event length. Medora events are **Mountain** time; Facebook
  defaults to Central.
- **Images:** Claude can upload these directly.

### Google Business Profile
- **Reach:** Surfaces in "things to do near me," Maps, and the Library's knowledge panel.
  Highest-*intent* audience — people already deciding where to go.
- **Speed:** Instant (minutes to live)
- **Login:** business.google.com, Matt's Chrome session. Survives well between runs.
- **Good for:** Anything within the next ~3 months. The only channel fast enough for an event
  a few days out.
- **Profiles:** TRPL · Salt+Scoria · TRPL Foundation (Bismarck) are verified. Eagle Ridge Lodge
  and the Field Office need verification. **Bismarck events also go on the Foundation profile.**
- **Images:** ❌ Claude cannot — native OS file picker.

### ND Tourism Partner Portal
- **Reach:** Statewide, and it's how ND Tourism chooses what *it* promotes in its own
  marketing. Punches above its traffic.
- **Speed:** Moderated — up to 48 business hours
- **Login:** partners.ndtourism.com. The session has expired on most runs so far — **expect
  to ask Matt to sign in.** Never enter credentials yourself.
- **Good for:** Everything with **7+ days** of lead time. Review runs up to 48 *business*
  hours, which is about six calendar days over a weekend — don't promise it for anything sooner.
- **Note:** Drive the form with JavaScript, not clicks — see `publish-nd-tourism`. Saves
  enormous time and sidesteps two field-masking bugs.

### Yelp
- **Reach:** Trip-planning and "things to do" discovery; feeds Apple Maps and several
  aggregators.
- **Speed:** Instant once posted
- **Login:** Yelp for Business, TRPL listing
- **Good for:** `special_event` — one-off public programs. Not recurring tours or ongoing
  exhibitions.
- ⚠️ **Mechanics not yet documented.** Nobody has driven this end-to-end yet. Work it out on
  first use and write the steps into `skills/publish-regional-calendars` (or its own skill)
  rather than leaving the next run to rediscover them.

### Email roundup (6 outlets, one message each)
- **Reach:** ND Living alone goes to ~200K households.
- **Speed:** Email
- **Good for:** A monthly or per-cycle digest, not single events.
- **Outlets:** see `reference/email-outlets.md`
- **Deadline:** ND Living wants copy **by the 10th of the prior month**.

---

## Tier 2 — Regional calendars, no login

### Prairie Public
- `https://news.prairiepublic.org/community-calendar`
- Statewide public radio/TV. **Cleanest form of the lot** — no login, no CAPTCHA friction.
- One submission per event. Recurring events can be entered as daily.

### Visit Dickinson CVB
- `https://www.visitdickinson.com/p/events/submit-event`
- Moderated. Travelers already planning a Dickinson-area trip.
- ⚠️ **Criteria exclude lectures and seminars** — they rejected the WHCA panels and Bleyer talk.
  Submit exhibitions, conventions and festivals only.
- Requires a phone number. Claude fills the form; the operator ticks the reCAPTCHA and submits, last, or it expires.

### Watford City Events (McKenzie County)
- `https://www.watfordcityevents.com/all/Calendar/List`
- Moderated. Handles multi-day ranges well.
- ⚠️ **"Save Changes" silently does nothing** — jQuery validation passes but `requestSubmit()`
  gets swallowed. Force it:
  `HTMLFormElement.prototype.submit.call(document.querySelector('form'))`

### Bismarck-Mandan CVB (Yodel platform)
- `https://noboundariesnd.com/events/`
- Largest ND metro in day-trip range. **Bismarck-area events only** — don't send Medora events here.
- ⚠️ **Typing into date/time fields crashes the app** ("Application error: a client-side
  exception has occurred"). Use *only* the calendar and clock picker buttons. If it crashes,
  reload and start over.

### Dakota Media Access
- `https://dakotamediaaccess.org/community-bulletin/#submit-event`
- Free cable + streaming bulletin for Bismarck-Mandan nonprofits.
- Tiny form. Bulletin displays **16:9 landscape**; email artwork to info@freetv.org after submitting.

### evvnt (feeds Bismarck Tribune and others)
- Reached via the Bismarck Tribune events page.
- ⚠️ **Category choice sets the price tier** — "Conferences" forces the paid Enterprise package.
  Pick a category that allows the FREE package, and **select FREE at checkout** (it defaults to $89).
- ⚠️ **Medora is outside the Tribune's coverage area.** evvnt will warn you. Only Bismarck-venue
  events are worth submitting here.

---

## Owned social beyond Facebook Events

A Facebook **event** is a listing. An Instagram or LinkedIn **post** is a promotion. They're
different motions and this plugin only does the first.

The Library's accounts (from the feed's `sameAs`): Facebook, **Instagram** `@trlibrary`,
**LinkedIn**, **X** `@TRPresLibrary`, **YouTube**. Bluesky exists but isn't in `sameAs`.

**Broadcast social posting is scheduled through Hootsuite**, and the `otd-social` skill
already owns that path for #OTD content. EventPromoter does **not** post to Instagram,
LinkedIn, X, Threads or Bluesky.

If asked to put an event on those channels: say so plainly, and offer either to draft copy
the operator can schedule in Hootsuite, or to hand off to whatever skill owns social
scheduling. Don't improvise a posting flow.

## Tier 3 — Broadcast and print (email/PSA)

| Outlet | Reach | How |
|---|---|---|
| KFYR-TV / KMOT / KUMV / KQCD (Gray) | Bismarck, Minot, Williston, Dickinson | Tockify calendar has **no public submission form** — email the station |
| KX News (Nexstar) | Bismarck/Minot | Community calendar + assignment desk for story pitches |
| Townsquare Media Bismarck | 4 stations | Station sites take community events; talk shows book local guests |
| ND Newspaper Association weeklies | Beach, Belfield, Killdeer, Bowman, Hettinger | One press release usually runs free as community news |

For marquee events, pitch a **story angle**, not a listing — KFYR and KX morning shows book guests.

---

## Tier 4 — Partner cross-promotion

- **Theodore Roosevelt Medora Foundation** — medora.com and TRMF pre-show announcements
- **Theodore Roosevelt National Park** — visitor center boards, ranger referrals
- **Dickinson State / Bismarck State / U Mary** — campus calendars, history dept listservs
- **Regional libraries and historical societies** — boards, newsletters, their own calendars
- **Medora-area lodging** — front-desk flyers and "what's happening" sheets

---

## Channels deliberately NOT used

| Channel | Why |
|---|---|
| **AllEvents.in** | Publishing requires a banner upload and SMS verification; flow is too clunky. Matt's call: skip. |
| **AmericanTowns** | Event listings appeared to be sunset when checked in Aug 2026. Re-check before relying on this. |
| **Eventbrite** | No account set up. Would feed Google event search if it ever matters. |
| **Williston Herald** | Blocked on a CitySpark account (Google SSO). Untried. |
| **AARP Local** | Untested. Good demographic fit for heritage travel — worth a try someday. |
| **Nextdoor** | Untested. |

---

## Lead-time gates

Check the clock before promising a channel:

| Days until event | What's still worth doing |
|---|---|
| **14+** | Everything |
| **7–13** | Everything except Visit Dickinson (moderation queue is tight) |
| | ⚠️ Email only helps if ND Living's deadline (10th of the prior month) hasn't passed |
| **3–6** | Facebook, GBP, Prairie Public, Watford City |
| **1–2** | **Google Business Profile only** — it's the sole instant-and-indexed channel |
| **Past** | Nothing. Check the date before you start. |

## Geographic relevance

Match the event to where it happens and who would drive to it. This matters — a Bismarck
lecture on a Dickinson calendar wastes the slot and annoys the moderator.

- **Medora events** → Visit Dickinson, Watford City, Prairie Public, ND Tourism, Facebook, GBP (TRPL profile)
- **Bismarck events** → Bismarck-Mandan CVB, Dakota Media Access, evvnt/Tribune, KFYR, GBP (**Foundation profile too**)
- **Statewide value** → ND Tourism, Prairie Public, ND Living
- **Never** → Medora events to Bismarck-Mandan CVB, or Bismarck events to Visit Dickinson
