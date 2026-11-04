---
name: publish-regional-calendars
description: Submit events to the regional web calendars and listing sites — Prairie Public, Visit Dickinson, Watford City, Bismarck-Mandan CVB, Dakota Media Access, evvnt/Bismarck Tribune and Yelp. Use when a promotion run reaches the regional calendars, or when asked to post an event to any of these, including Yelp. Each has a specific failure mode documented here.
---

# Regional web calendars

Six no-login calendars. Each one is small, each one is worth doing, and **each one breaks in
its own particular way.** Read the entry before you start.

Standard values for all of them:

- Theodore Roosevelt Presidential Library · 3410 Chateau Road, Medora, ND 58645
- **hello@trlibrary.com** (public contact) · 701-892-6070
- Link the event's own trlibrary.com page

---

## Prairie Public — start here

`https://news.prairiepublic.org/community-calendar`

Statewide public radio and TV. **The cleanest form of the lot** — no login, no CAPTCHA
friction, no known bugs. If you only have time for one regional calendar, make it this one.

One submission per event. Long exhibitions can go in as recurring daily; multi-day
conventions as a daily run across the dates.

---

## Visit Dickinson

`https://www.visitdickinson.com/p/events/submit-event`

Moderated. Reaches travelers already planning a Dickinson-area trip — good geographic fit
for Medora.

- ⚠️ **Criteria exclude lectures and seminars.** Talks and panels get rejected. Submit
  exhibitions, conventions and festivals only.
- **Phone is required.**
- ⚠️ **Click the reCAPTCHA last, immediately before submitting.** It expires if you solve it
  and then keep filling fields.
- Watch that the subject/title text lands in the title field and not the To field.

---

## Watford City Events (McKenzie County)

`https://www.watfordcityevents.com/all/Calendar/List`

Moderated. Handles multi-day date ranges cleanly.

⚠️ **"Save Changes" silently does nothing.** jQuery validation passes, then `requestSubmit()`
gets swallowed and the page just sits there. Force the native submit:

```js
HTMLFormElement.prototype.submit.call(document.querySelector('form'))
```

---

## Bismarck-Mandan CVB — **Bismarck events only**

`https://noboundariesnd.com/events/` (Yodel platform)

Largest ND metro within day-trip range. **Do not send Medora events here.**

⚠️ **Typing into the date or time fields crashes the app** — *"Application error: a
client-side exception has occurred."* Use **only** the calendar and clock picker buttons,
never the keyboard. If it crashes, reload and start over.

Dropped keystrokes have also produced typos in URL fields (`govrnor` for `governor`). If a
field looks wrong, fix it with the React value setter rather than retyping:

```js
Object.getOwnPropertyDescriptor(window.HTMLInputElement.prototype,'value').set
```

---

## Dakota Media Access — **Bismarck events only**

`https://dakotamediaaccess.org/community-bulletin/#submit-event`

Free community bulletin on cable and streaming for Bismarck-Mandan nonprofits.

Tiny form: name, email, title, date, location, description. The Cloudflare check passes
automatically.

The bulletin displays **16:9 landscape**. Artwork goes to **info@freetv.org** — they'll
often follow up asking for it. **Draft that message, don't send it** (see
`draft-outreach-emails`); the no-send boundary applies here too.

---

## evvnt / Bismarck Tribune — **Bismarck events only**

Reached through the Bismarck Tribune events page.

- ⚠️ **Medora is outside the Tribune's coverage area.** evvnt will warn you that the event
  won't publish in the Tribune — it would only land on evvnt's generic calendar. Not worth it.
  **Only Bismarck-venue events belong here.**
- ⚠️ **The category you pick sets the price tier.** Choosing "Conferences" forces the paid
  Enterprise package. Pick a category that allows the free package.
- ⚠️ **Select the FREE package at checkout — it defaults to $89.**

---

## Yelp — mechanics not yet established

Yelp for Business, TRPL listing. Good for `special_event` items; not for recurring tours or
ongoing exhibitions.

**Nobody has driven this end-to-end yet.** There is no documented flow, no known selector,
no confirmed field list. On first use: work it out, publish carefully, and then **write the
steps into this section** so the next run doesn't rediscover them. If it breaks or behaves
oddly, `report-channel-issue`.

Don't guess at a flow and present it as known.

---

## Order of play

1. Prairie Public (clean, statewide)
2. Watford City (if Medora)
3. Visit Dickinson (if Medora **and** not a lecture)
4. Bismarck-Mandan CVB, Dakota Media Access, evvnt (if Bismarck)
5. Yelp (special events only — see above)

## Logging

All of these are moderated or fire-and-forget — there's no listing ID to capture. Record
the submission date, the channel, and whether a confirmation page or email appeared. That
confirmation is the only evidence the submission landed.

---

**If this channel misbehaves** — a selector is gone, a form rejects valid input, or a
workaround documented above stops working — use `report-channel-issue` to file it (it checks
for an existing issue first). Platforms change constantly; the fix only happens if the
divergence gets captured.
