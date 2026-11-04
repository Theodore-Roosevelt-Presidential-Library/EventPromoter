---
name: report-channel-issue
description: File a GitHub issue when a promotion channel breaks or behaves differently than its skill describes — a selector that no longer exists, a form that changed, a new validation error, an expired login flow. Checks for an existing issue first and comments on it rather than filing a duplicate. Use when a publish step fails, when a documented workaround stops working, or when asked to report a problem with a channel.
---

# Reporting channel breakage

Platforms change under us constantly. Facebook moves a button, ND Tourism swaps a widget,
Google redesigns a dialog. When that happens the knowledge is only worth capturing if it
lands somewhere the next run will read.

**Repo:** `Theodore-Roosevelt-Presidential-Library/EventPromoter`

## When to file

File when reality diverges from what a skill documents:

- A selector, button or field the skill names no longer exists
- A form rejects input the skill says is correct
- A new validation rule, cap or limit appears
- A documented workaround stops working
- A login or permission flow changed
- A channel starts silently failing — the submission appears to work but nothing publishes

**Don't file for:** an expired session (that's normal — ND Tourism expires most runs), a
missing image you were always going to add manually, or a one-off network blip. Retry first.

## ⚠️ Check for duplicates first — always

A recurring break should **accumulate evidence on one issue**, not spawn a twin every cycle.
A second occurrence is useful information *on the existing issue*.

### 1. Search open issues

```
search_issues  →  repo:Theodore-Roosevelt-Presidential-Library/EventPromoter
                  is:issue is:open label:channel-breakage <channel name>
```

Then widen: search the distinctive part of the error text, the selector name, or the
field label. Titles follow a convention (below) so searches are predictable, but don't
rely on the title alone — someone may have worded it differently.

### 2. Judge whether it's the same problem

Same **channel** + same **step** + same **symptom** = duplicate. A different symptom on the
same channel is a separate issue; two different Facebook problems should not share a thread.

### 3. Act on what you find

| Found | Do |
|---|---|
| **Open issue, same problem** | `add_issue_comment` with a new-occurrence note. Do **not** create. |
| **Closed issue, same problem** | It regressed. Comment with the recurrence and reopen (`issue_write` method `update`, `state: open`). Reopening beats filing fresh — the history is the point. |
| **Nothing matching** | Create a new issue. |

A new-occurrence comment is short:

```markdown
Recurred <YYYY-MM-DD>.

- Event: <name>
- Still: <what happened — confirm it's identical, or note how it differs>
- Workaround: <what got it published, or "none, still blocking">
```

## Creating the issue

**Title:** `[<channel>] <short symptom>` — e.g. `[Facebook] Cover-photo file input ref no longer found`

Keep the channel in brackets. It makes future searches work.

**Body** — follow `.github/ISSUE_TEMPLATE/channel-breakage.yml`:

```markdown
**Channel:** Facebook
**Skill:** `skills/publish-facebook/SKILL.md`

### What the skill says should happen
> find → "file input for event cover photo upload" → usually `ref_60`

### What actually happened
`find` returns no file input. Page now renders the cover control inside a
shadow root; no `input[type=file]` is present in the accessibility tree.
URL: https://www.facebook.com/events/create/

### Severity
Degraded — events publish fine, covers must be added by hand.

### Workaround
None found this run. Published without covers and flagged for manual upload.

### Run context
- Date: 2026-11-04
- Events affected: 4
- Tool: Claude in Chrome
- Worked in a previous run: yes, 2026-09-22
```

**Labels:** `channel-breakage` plus a channel label if one exists. If the API rejects a
label that doesn't exist in the repo, retry without it rather than failing the report —
getting the issue filed matters more than the taxonomy.

## Ask before filing

Creating an issue writes to a shared repo. **Draft it, show the title and body, and get a
yes before submitting** — unless the operator already said to file issues without asking in
this session.

If they decline, offer to save the draft into the run log's Open items instead so it isn't lost.

## What makes a report worth filing

The **workaround field is the most valuable part.** "Blocked, don't know why" ages into
noise. "Blocked, but `HTMLFormElement.prototype.submit.call(form)` forces it through"
becomes the next version of the skill.

Be specific and verbatim. Exact error strings, exact selectors, the real URL. A future
reader — possibly you, six weeks on — needs to recognise the problem instantly.

## After filing

Report back with the issue number and link, and say plainly whether the channel is usable
in the meantime. Add it to the run log's **Open items** as well — an issue nobody looks at
is the same as no issue.
