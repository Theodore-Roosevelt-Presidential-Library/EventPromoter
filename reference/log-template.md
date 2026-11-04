# Run log template

Each promotion cycle writes one file: `events/YYYY-MM-<cycle-name>.md`

The log exists so the **next** run can diff against it. Two things make it useful: the
event URLs (the key for change detection) and the platform IDs (so later cycles edit
rather than duplicate).

---

```markdown
# Promotion run — <cycle name>

Source: trlibrary.com feeds · Run <YYYY-MM-DD>
Default venue: Theodore Roosevelt Presidential Library, 3410 Chateau Road, Medora, ND 58645
All times Mountain unless noted.

## Events in this cycle

| Event | URL slug | When (MT) | Price | Room | Status |
|---|---|---|---|---|---|
| | | | | | new / changed |

### Changes caught this cycle
<!-- retitles, redates, URL moves, corrected facts. Say what was wrong and what it is now. -->

## Submission log

| Date | Event | Channel | Status | ID / notes |
|---|---|---|---|---|
| | | Facebook | ✅ Published | ID … · cover … |
| | | Google Business Profile | ✅ Posted | Learn-more → event page |
| | | ND Tourism | ✅ Created | ID … |
| | | Prairie Public | ✅ Submitted | confirmation seen |
| | | — | ⏭️ Skipped | reason |
| | | — | ⏸️ Blocked | what's needed and who |

**Totals:** N placements across M channels.

## Open items

- [ ] <!-- manual steps that will otherwise silently never happen -->
```

---

## Status vocabulary

Use these consistently — the next run greps for them.

| Marker | Means |
|---|---|
| ✅ | Live, or submitted and confirmed |
| ⏸️ | Blocked — name what's needed and who can unblock it |
| ⏭️ | Deliberately skipped — give the reason |
| 📷 | Published but missing its image |
| ❌ | Channel dead or permanently unusable |

## What always belongs in Open items

These are the steps that quietly don't happen unless written down:

- **Unsent Outlook drafts** — the single most common silent failure
- **GBP images** — Claude cannot upload them; native OS picker
- **ND Tourism photos** — manual, per event
- **Logins that expired** — especially ND Tourism, which expires most runs
- **Site copy problems noticed in passing** — contradictory dates, stale text. Worth
  reporting even though fixing them isn't this plugin's job.
