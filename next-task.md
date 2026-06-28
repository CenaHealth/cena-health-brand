# COPY-01: Dummy Copy Consistency Fixes

_Generated: 2026-03-18_
_Refs: .project-docs/dummy-copy.md (canonical name registry)_

---

## Objective

Fix four copy inconsistencies identified during the dummy-copy audit.
All changes are text-only — no CSS, no JS, no new classes.

| # | File | Issue | Fix |
|---|------|-------|-----|
| 1 | `pattern-library/components/kv-table.html` | `Maria Santos` should be `Maria Rivera` | Rename |
| 2 | `pattern-library/components/slide-quote.html` | `Maria Santos` should be `Maria Rivera` | Rename |
| 3 | `pattern-library/components/timeline.html` | `Dr. Michael Chen` should be `Dr. James Chen` | Rename |
| 4 | `pattern-library/components/input.html` | Lorem ipsum in character-count static demos | Replace with Cena Health copy |

---

## Pre-Build Audit Gate

Before touching any file, grep to confirm the exact strings that need replacing:

```bash
grep -n "Maria Santos" pattern-library/components/kv-table.html
grep -n "Maria Santos" pattern-library/components/slide-quote.html
grep -n "Michael Chen" pattern-library/components/timeline.html
grep -n "Lorem ipsum" pattern-library/components/input.html
```

All four should return matches. If any returns empty, stop and report — do not
proceed on a file that doesn't match.

---

## Fix 1 — kv-table.html: Maria Santos → Maria Rivera

**File:** `pattern-library/components/kv-table.html`

Find and replace the two occurrences:

```
Maria Santos  →  Maria Rivera
```

The KV table uses `Maria Santos` as both the Full Name value and implicitly as
the patient identity. Replace with `Maria Rivera` to match the canonical patient
used across `table.html`, `datatable.html`, `form-group.html`, `badge.html`,
`blockquote.html`, `modal.html`, `drawer.html`, `popover.html`, and `input.html`.

Do not change any other field values (DOB, MRN, Program, etc.) — they are
appropriate for Maria Rivera.

---

## Fix 2 — slide-quote.html: Maria Santos → Maria Rivera

**File:** `pattern-library/components/slide-quote.html`

There are two quote sections that use `Maria Santos`:

**Section `default` — patient testimonial:**

Current attribution:
```html
<p class="slide-quote__name">Maria Santos</p>
<p class="slide-quote__role">Program Participant, Diabetes Management</p>
```

Replace name only:
```html
<p class="slide-quote__name">Maria Rivera</p>
<p class="slide-quote__role">Program Participant, Diabetes Management</p>
```

**Section `sage` — Sarah Kim quote:**

The `sage` section correctly attributes to `Sarah Kim, RD` — do not touch it.
Only the `default` section patient name changes.

---

## Fix 3 — timeline.html: Dr. Michael Chen → Dr. James Chen

**File:** `pattern-library/components/timeline.html`

Current:
```html
<span class="timeline-event-actor">Dr. Michael Chen</span>
```

Replace with:
```html
<span class="timeline-event-actor">Dr. James Chen</span>
```

There is one occurrence. Do not change the badge label, event text, or icon type.

---

## Fix 4 — input.html: Replace Lorem ipsum in character-count static demos

**File:** `pattern-library/components/input.html`

There are two static demo textareas in section `character-count` that contain
Lorem ipsum filler. Replace the body text of each with Cena Health copy of
approximately the same character length.

**Near-limit demo (~423 chars):**

Current `value` content (inside `<textarea ... readonly>`):
```
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia.
```

Replace with (~423 chars of Cena Health copy):
```
Maria Rivera has been receiving medically tailored meals since January 2025. Her plan was last updated following her February 12 visit with Dr. Anand, who reduced her daily sodium target from 2,000 mg to 1,500 mg in response to elevated blood pressure readings. Since the adjustment, she has logged three consecutive weeks of full meal compliance — the longest streak since her enrollment in the Diabetes Management program.
```

**At-limit demo (~500 chars):**

Current `value` content (inside `<textarea ... readonly>`):
```
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum sed.
```

Replace with (~500 chars of Cena Health copy):
```
Every patient in the Cena Health program receives a personalized nutrition plan developed in collaboration with their care team. Plans are reviewed monthly, adjusted after clinical visits, and updated in real time when diagnoses or medications change. Patients can view their current plan, log meal feedback, and message their dietitian directly from this app. Delivery windows are confirmed 24 hours in advance, and missed windows can be rescheduled at no cost within the same billing period.
```

Do not change the `textarea-count-label` values (`423 / 500` and `500 / 500`),
the CSS classes, or any surrounding markup.

---

## Verification Checklist

After all edits, run:

```bash
grep -n "Maria Santos" pattern-library/components/kv-table.html
grep -n "Maria Santos" pattern-library/components/slide-quote.html
grep -n "Michael Chen" pattern-library/components/timeline.html
grep -n "Lorem ipsum" pattern-library/components/input.html
```

All four commands must return **no output**. If any still match, the edit did
not apply — do not mark complete.

Also confirm:
- [ ] `kv-table.html` still has `Dr. Chen, Internal Medicine` as Primary Provider (unchanged)
- [ ] `slide-quote.html` sage section still reads `Sarah Kim` (unchanged)
- [ ] `timeline.html` badge label still reads `Clinical` (unchanged)
- [ ] `input.html` `textarea-count-label` values still read `423 / 500` and `500 / 500`

---

## Completion Report Format

```
COPY-01 complete.

Fixes applied:
1. kv-table.html — Maria Santos → Maria Rivera ✅
2. slide-quote.html — Maria Santos → Maria Rivera (default section only) ✅
3. timeline.html — Dr. Michael Chen → Dr. James Chen ✅
4. input.html — Lorem ipsum replaced in both static textarea demos ✅

Verification grep: all four patterns return no output ✅

No CSS, JS, or class changes made.
```
