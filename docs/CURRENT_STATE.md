# Current State

- **Version:** v14 — kid-first design/interface pass (built on v13)
- **File:** `index.html`, 136,498 bytes, 2,192 lines (v13 was 105,621 bytes)
  - sha256 `4c5a29a239aabbfb1b0315737c72854d11fddefcf7de7d43558c6b2147c5175f`
- **Branch:** `claude/our-week-v14-kid-design-hnpgn4`, merged to `main`
  - Tier 1 commit `9524cee`, Tier 2 commit `2cffe3c`
- **Storage:** browser `localStorage`, key `family-board-data-v1` (unchanged). v14 changes **no** saved-data shapes: every item is presentation/interaction only, computed from existing data.

## What changed in v14

The research behind this pass: NN/g child-UX testing (2cm targets for under-9s), and the Skylight, Hearth, Cozyla and DAKboard boards plus kid chore apps (3-field add flows, emoji for pre-readers, a celebration on a finished list).

### Tier 1 — big taps and simple adding

- **A1 Whole-row tasks:** tap anywhere on a task row to toggle it.
  - Rows are at least 72px tall, with a 44px state circle and 19px titles.
  - ✎ and × are separate, quiet 44px buttons.
  - A long-press on a row opens the edit sheet.
- **A2 Target floor:** no interactive element is under 44px.
  - Tabs 52px, topbar icons 52px, calendar nav 48px, modal buttons 52px (Save 60px).
  - Day and person chips are 44–48px with 10px gaps.
- **A3 Celebration:** when a person's due list for the viewed day goes from not-complete to 100%, an emoji burst plays over their column and the progress and points count up.
  - It is non-blocking (`pointer-events: none`), so the next tap works at once.
  - Unchecking a task re-arms it.
- **A4 Undo toast:** skipping shows "Undo" for 5 seconds. Undo restores the task and its done state. There are no confirm dialogs for skip or uncheck.
- **A5 Today rows:** big per-person rows (52px avatar, "3 of 5 done" at 17px, a 14px bar, 28px dots). Tapping a row jumps to that person's Tasklist column and highlights it.
- **B1 Quick-add event:** tapping a Calendar day opens the add sheet with that date filled in.
  - That day's events are listed compactly at the top.
  - The sheet shows only a title, person chips (none picked = family) and a 60px Save.
  - Repeat, time, dates/multi-day, type and location sit under a collapsed "More options". The defaults are all-day, Event, no repeat.
  - The sheet closes after Save.
- **B2 Repeat chips:** Never / Every day / Weekdays / Every week / Monthly, in both forms.
  - They map onto the existing structures:
    - Events: Every day = weekly [0–6], Weekdays = weekly [1–5], Every week = weekly [that weekday], Monthly = monthly on that date.
    - Tasks: Never = one-day `range`, Every day = `daily`.
  - The custom day picker stays: under More options for events, and below the chips for tasks.
- **B3 Task quick-add:** the title is the only required field.
  - The person is preselected from the column, as a chip row that can also reassign the task.
  - Repeat chips are visible, and points and dates sit under More options.
  - An emoji strip (🧹 🦷 🛏️ 📚 🍽️ 🧺 👕 🐶 🐱 🗑️ 🪴 🛁 🎒 🎹 🧸 🚿) prepends the tapped emoji to the title.
- **B4 Big add buttons:** a 56px "+ Add event" on Today, and a 56px "+ Add" in the Calendar header. The Calendar one uses today, or the 1st of the month being viewed.
- **C1 Today order:** date and a live clock (34px, updates each minute), weather (unchanged), an Up-next card, today's events, then the person rows.
  - Up next shows the next timed event still to come today. If there is none, it shows the first event tomorrow, labelled "Tomorrow". If neither exists, the card is hidden.
- **C2 Type floor:** no visible text is under 14px.
  - Titles are at least 16px (19px on the Tasklist, 22px in kid view), and section titles are at least 20px.
  - Analytics chart labels are 14px, and the chart scrolls sideways instead of shrinking.

### Tier 2 — kid view and parent-safe touches

- **D1 Kid view:** tap a person's avatar or name on the Tasklist to open a single-person view.
  - It has rows at least 80px tall (84px), 22px titles, 56px circles, their badges, progress and goal shelves, and one 60px "← Back".
  - Tabs, zoom and Settings are hidden.
  - Completing, the celebration, skip and Undo all work there the same way.
  - The person's star calendar (which used to open from the name) is now a 📅 button on the column header.
- **D2 Streak flame:** each person's current run of consecutive perfect days, computed from existing completions.
  - Days with nothing due are skipped, and today doesn't break the run while it is still in progress.
  - "🔥 N" shows beside ⭐ when N is 2 or more.
- **D3 Reward shelf:** the reward-goal box became a shelf card: a big ⭐, the reward as the headline, "points / target" and a thick bar. The perfect-day goal got a matching 🌟 card. It is display only, with no redemption.
- **D4 Settings guard:** Settings opens only after a 2-second press-and-hold on the gear.
  - A ring fills and a "Keep holding…" label shows while holding. A short tap shows "Hold for 2 seconds".
  - It works with keyboard Enter/Space held down. Nothing is stored and there is no PIN.

## Verification (2026-10-10, v14 branch)

The page was served over http (`python3 -m http.server`) and driven in headless Chromium (Playwright) at 1280×860, plus a 1180×820 touch context.

- **Tier 1:** 62/62 checks passed (commit `9524cee`).
- **Tier 2:** the full suite was re-run and 79/79 passed (commit `2cffe3c`).

| Check | Result |
|---|---|
| Zero JavaScript errors (`pageerror`) and zero non-network console errors, on load and through every check | PASS |
| All 4 tabs, kid view and every modal render (task sheet, event sheet with More open/closed, date picker, person calendar, Settings) | PASS |
| No interactive element under 44px — automated sweep of every button/input/select/link/calendar cell in Today, Tasklist, Calendar, Analytics, kid view, the task sheet, the event sheet, the date picker, the person calendar and Settings | PASS |
| No visible text under 14px — same views and modals | PASS |
| Whole-row tap toggles; reload; state persists. ✎ edits; long-press (mouse and touch) opens edit without toggling | PASS |
| Skip shows the Undo toast; Undo restores the task and its done state; toast auto-hides after 5s | PASS |
| Quick-add: tap a future Calendar day, type only a title, save → all-day family event on that day; persists after reload | PASS |
| "Every week" chip → weekly on that weekday; the event appears the following week too. Weekdays/Every day/Never/Monthly/custom days map to the existing structures (events and tasks) | PASS |
| Completing a person's final due task fires the celebration; the next tap 0.2s later registers while it plays; unchecking re-arms it | PASS |
| Today shows the clock, weather card, Up next (an event added for later today; tomorrow fallback; hidden when nothing) and big rows; tapping a row lands on that person's Tasklist column | PASS |
| Kid view: open it, complete tasks (celebration fires), skip + Undo, Back → Tasklist shows the same state; persists after reload | PASS |
| Settings: a single tap (mouse and touch) does not open it; a 1s hold shows the ring but does not open; a 2s hold opens it | PASS |
| Pre-existing v13 data (3 people, daily/weekly/monthly/once/range tasks, weekly/monthly/multi-day events, goals, past completions and skips): everything renders and nothing is lost. The streak (🔥 6) and reward shelf compute without errors, and the saved JSON's structure is unchanged after interacting | PASS |

**Sandbox notes, not app errors:**
- `api.open-meteo.com` is blocked by the builder sandbox's network policy (`ERR_TUNNEL_CONNECTION_FAILED`). Those are the only console errors, and the weather card shows its "Tap to retry" state.
- github.io was not checked from the sandbox. Kakashi verifies the live site.

**Known limits:**
- The 44px floors hold at the default "Normal" zoom. The existing "Small" zoom (0.9×) scales the whole UI, so targets render at about 40px there.
- Rows were checked at 1180–1280px wide. The Tasklist columns' minimum width went from 210px to 240px, so on narrower screens a 5-person board wraps sooner.

## Project rules

- The owner has authorized this project to run **in parallel with Math Quest**, in its own separate repo (`zerononeex/family-board`).
- Only one builder works inside Family Board at a time.
- Agents working here never touch Math Quest repos.

## Previous version

v13 (`60289e9`, 105,621 bytes, sha256 `580735d7…6610e42`) was the owner's upload, committed verbatim and verified on 2026-10-09/10. Its full verification notes are in git history.

## Next step

Kakashi verifies the live site and the byte-level diff. Further ideas go in [BACKLOG.md](BACKLOG.md).
