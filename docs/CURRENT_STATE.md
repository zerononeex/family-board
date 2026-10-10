# Current State

- **Version:** v15.1 — micro-fix on v15: **person names always stay on one line.** On the family's iPad, "Alexander" split mid-word in the Tasklist column header ("Alexande" / "r"). Commit `2aad340`.
- **File:** `index.html`, 166,805 bytes, 2,654 lines (v15 was 165,258 bytes)
  - sha256 `2a8da704bcb092650faea44f575a254e566964247c2ecd8279fa21ac6dd0d290`
- **Branch:** `claude/family-board-v15-yzn8zg` (this session's assigned branch, continued from `main`), merged to `main` (fast-forward)
- **Storage:** unchanged. `family-board-data-v1` and `family-board-ui-v1` are read and written exactly as in v15. The fix is display-only and never changes a stored name.

## What changed in v15.1

- **Root cause:** `.person-name` (the shared header used by every layout) had `overflow-wrap: break-word` and no `white-space: nowrap`. In a narrow column the name shares its row with the 44px avatar and the 50px 📅 button, so a long name was squeezed and broken mid-word. The rail name (`.pr-entry-name`) had the same problem through `overflow-wrap: anywhere`.
- **Fix:**
  - `.person-name`, `.pr-entry-name`, `.kid-name` and `.tp-name` are now `white-space: nowrap`, and every mid-word breaking rule is gone.
  - After every render, and on view change, resize and font load, `fitNames()` checks each visible name. If it is wider than its box, the font steps down 1px at a time to a **15px floor** (CSS px).
  - The line height is pinned first, so a shrunk name never changes the header's height.
  - Only if a name still doesn't fit at 15px does it get the `name-clip` class (overflow hidden + ellipsis).
  - Names that already fit are untouched, so they render pixel-identical to v15.

### Verification (2026-10-10, v15.1)

Headless Chromium with the real Google Fonts (Fraunces) loaded.

| Check | Result |
|---|---|
| Reproduced on v15: "Alexander" wraps to 2 lines in Layout A at 1180×820 (5 people) | confirmed |
| "Alexander" with 5 people, Layouts A and C, 1180×820 and 834×1112: one line (20px at 1180, 21px at 834), no overlap with 📅 | PASS |
| Stress names at 834×1112 in A and C: "Christopher" 21px, "Alexandria-Rose" 16px, "Maximilian-Alexander" (20 chars) reaches the 15px floor and ends in an ellipsis. All are one line and none break mid-word. (At 1180×820, where 5 columns are narrower, "Christopher" fits at 16px, and "Alexandria-Rose" and the 20-char name end in an ellipsis at 15px.) | PASS |
| Layouts B and D (main panel + rail), Kid View and the Today rows, with "Alexander" and the stress names, at both sizes: every name on one line. The only ellipsis is the 20-char name in B's 300px identity block at 1180 | PASS |
| Header heights uniform across people in every layout (A 141, C 140, D panel 169, D rail entries 113 / 59, Today rows 101; B name rows 47). v15 had 141/185 headers with long names. Name tap target 45–47px and still opens Kid View | PASS |
| Nothing else moved: with normal names, screenshots of every tab + Kid View are pixel-identical to v15 in Layouts A, B, C, D and with Christmas/C and New Year/D (reduced motion, so the fireworks don't animate) at both sizes | PASS |
| v15 parity suite 24/24 in each layout; the v15 combined checklist 53/53 (data byte-identical, export/import, themes, network); both `<script>` blocks pass `node --check`; zero console errors | PASS |

## Previous version: v15

- v15 (head `1fede88`): four switchable Tasklist layouts and five seasonal themes. 165,258 bytes, sha256 `e3ea3ac501e9de464c083846e02bdaf2dd88b7fa56df141d75e66590a7e95095`. Details follow.
- **Board data:** `localStorage` key `family-board-data-v1`, **unchanged** since v14 — no new fields, no shape changes. Export/import work exactly as before.
- **UI preferences (v15):** a separate key, `family-board-ui-v1`, holds `{"layout":"A","railPersonId":null,"theme":"off"}`.
  - `layout`: `"A"`, `"B"`, `"C"` or `"D"`. `railPersonId`: the person selected in Layout D (or `null`). `theme`: `"off"`, `"auto"`, `"halloween"`, `"christmas"`, `"newyear"`, `"valentines"` or `"canadaday"`.
  - A missing, unreadable or partial key falls back to those defaults field by field. The key is only written when a choice is made.
  - It is per-device. It never goes into a backup file, and importing a backup never changes it.

## What changed in v15

| Stage | Commit | Change |
|---|---|---|
| a | `5411b67` | UI-prefs key with defaults; Settings → **Tasklist layout** section; `renderChores` rebuilt on shared helpers (`personDayModel`, `personColumnHeadHtml`, `taskRowHtml`, `skippedRowHtml`, `goalBoxesHtml`, `addTaskBtnHtml`) so every layout uses the same header, rows and actions. |
| b1 | `426f33f` | **Layout C — Colour Columns**: A's columns, washed in each person's colour. |
| b2 | `1dd987b` | **Layout B — Family Rows**: one full-width band per person; Today-tab jump made layout-aware. |
| b3 | `8651882` | **Layout D — People Rail**: person rail + one big list; `railPersonId` saved in the UI key. |
| c | `bec40cc` | Theme engine (`<html data-theme>`), Settings → **Theme** section, Auto by date, themed celebration emoji, **Halloween**. |
| d | `67668c1` | **Christmas**, **New Year** (header-only fireworks, off under reduced motion), **Valentine's**, **Canada Day**. |
| e | `1bd04f8` | Portrait-iPad fix: the themed title motif shrinks below 1024px so the header stays on one line. |

All four layouts and all five themes shipped; nothing was held back.

## Verification (2026-10-10, v15 branch)

Served over http and driven in headless Chromium (Playwright). Test data: 5 people (daily and weekly tasks, past completions, a skipped task, reward and perfect-day goals, events) including "Choose and Fill Snack and Fill Water Bottle".

**Layouts**

| Check | Result |
|---|---|
| Fresh load with no `family-board-ui-v1`: Layout A, `data-theme="none"`, key not written. Screenshots of Today, Tasklist, Calendar, Analytics and Kid View are **byte-identical** to v14.2 at 1180×820 and 834×1112 | PASS |
| Settings shows A/B/C/D (60px rows); switching re-renders behind the sheet at once and survives reload | PASS |
| Item 3 parity suite (24 checks) in **each** layout at 1180×820 **and** 834×1112: whole-row tap completes and persists after reload; skip + 5s Undo toast; show/hide skipped and ↺ un-skip; viewing yesterday and back-filling a checkmark writes yesterday; "+ Add task" pre-selects the person and the task appears; long-press opens edit without toggling; ✎ edits; 100% celebration fires (and flashes the rail entry in D) and a tap 60ms later still registers; switching layouts keeps the viewed date, theme and data byte-identical; zero errors | PASS (96/96 + 96/96) |
| Rendered floors at 0.9× (min across the board): circles 44.1 (A/C), 46.8 (B), 50.4 (D); rows/tiles 99 (A/C), 106 (B), 82.8 (D); buttons ≥44.1; titles 17.1px (D 19.8px); nothing truncated; page `scrollWidth` = viewport in every layout at both sizes (A/C keep their snap strip in portrait) | PASS |
| Today-tab person jump: A/C scroll the column into view and flash it; B scrolls the band to the top and flashes it; D selects the person in the rail (saved) and flashes their list | PASS |
| Kid View unchanged (pixel-identical with defaults; same interactions) | PASS |

**Themes**

| Check | Result |
|---|---|
| Settings shows Off, Auto and the five themes; each manual theme applies immediately and persists after reload; body, Today, Tasklist, Calendar, Analytics, Kid View and the Settings sheet all pick it up | PASS |
| Every theme checked at 1180×820 in Layout A; Halloween and Christmas run through the full parity suite in B, C and D; all 4 layouts × 7 theme preferences render, and switching one never resets the other | PASS |
| Auto resolver (`window.resolveSeasonalTheme(pref, date)`): Oct 15 → halloween, Dec 25 → christmas, Dec 31 / Jan 1 → newyear, Feb 14 → valentines, Jul 1 → canadaday, Mar 15 → none, plus window edges (Dec 1/26/27, Jun 24/25, Sep 30, Oct 31, Feb 1/15, Jan 2). "Off" → none on every date; manual Christmas → christmas on every date | PASS |
| Auto on the device date (Oct 10) → Halloween; manual Christmas on that date stays Christmas | PASS |
| Person colours (`--coral` … `--slate`) and the calendar Event colour are identical in every theme; task titles stay `#1F2A24` at 19px; strip, motif and fireworks have `pointer-events: none`; night-dim still sets `brightness(0.55)` in every theme | PASS |
| Contrast: every themed text role (soft text on bg/surfaces, headings, white on accent buttons, tabs and day chip) is at least as high as the base theme's (automated WCAG ratio check) | PASS |
| New Year fireworks run only on `.topbar::after` (7s fade); with `prefers-reduced-motion: reduce` the computed animation is `none` | PASS |
| Celebration emoji: Halloween 🎃👻🦇🍬🌙✨, Christmas 🎄🎅🎁❄️⛄🔔, Off → the previous set | PASS |

**Data + technical safety**

| Check | Result |
|---|---|
| Export: the JSON equals board data and has no `layout`, `railPersonId` or `theme`. Import of that file: layout and theme unchanged | PASS |
| Parsed `family-board-data-v1` deep-diffed before/after 28 layout/theme switches: no new keys, no changed values (byte-identical) | PASS |
| v14.2-shaped data and v13-shaped data (old `chores`, events with `date`) load with nothing lost; a garbage or partial UI key (`{"layout":"Z","theme":"purple"}`, `{"layout":"D"}`) falls back to defaults field by field; zero console errors | PASS |
| Both `<script>` blocks pass `node --check` | PASS |
| Network: only the page, Google Fonts and Open-Meteo are requested; no new files, fonts, images or libraries | PASS |

**Known limits:**
- On a portrait iPad (834px) a themed header has only ~10px to spare once the title motif is added. It fits on one line with the real fonts in Chromium. If iPad emoji render a little wider, the ⚙️ button would drop to a second header line; nothing breaks.
- The midnight re-check of Auto runs on the existing 15s day-rollover timer and when the app becomes visible again. It was checked through the date resolver, not by changing the device clock.
- Earlier known limits (sub-14px text outside the Tasklist at 0.9×, non-sticky date bar) are unchanged.

## Previous version: v14.2

- Micro-fix on v14.1 (commit `f5242ec`, docs `0e5db21`): the per-row "+N ⭐" label was removed from task rows. 139,257 bytes, sha256 `e7494117049a697b08b586388528406657900a6187e1b5e488803ba292152d9e`.

## What changed in v14.1

| Item | Commit | Change |
|---|---|---|
| 5 | `df7bee0` | Home-screen meta tags: `apple-mobile-web-app-capable`, `-status-bar-style` (default), `-title` ("Our Week"), `theme-color` `#EEF3ED`. |
| 2 | `7a17809` | **Settings is a plain tap again.** The v14 2-second hold (D4) opened Settings while held, but it vanished on release. The hold timers, ring, label and handlers are deleted. Settings stays open until Done or a backdrop tap. |
| 3 | `f0d1523` | **No more size picker.** The 🔍 Small/Normal/Large button is gone and the app always renders at the old "Small" factor (0.9×, `BOARD_SCALE`). `applyZoom()` keeps its wrapper-scaling and min-height layout code. Data with `zoomIndex` 0, 1 or 2 renders identically. |
| 1 | `20b2c3a` | **Option A Tasklist: everyone on one row.** ≥1024px: one equal-width row for 2–6 people; 7–10 people keep one row and scroll sideways. <1024px: one horizontal snap-scroll strip (~252px columns on screen); columns never stack and the page never scrolls sideways. Width comes from spacing and header chrome (10px gap, 9px list padding, compact head: 40px avatar, ~19px name, ⭐ badge, thin bar, "x of y done"). Very narrow columns (e.g. 6 people at 1024px) move ✎ × onto their own line via a container query. |
| 4 | `599497f`, `ef2baa5` | **Tasklist date bar** replaces the ‹ day › stepper: a 7-day strip (weekday letter + date; today ringed marigold, viewed day filled sage), ‹ › jump a whole week, the date label ("Saturday, Oct 10 ▾") opens the existing month date-picker, and a Today pill appears whenever the viewed day isn't today. The viewed day is UI state only; checking a task on a past day still writes to that date. On phones the day chips scroll inside the strip. |
| — | `2b82092` | Calendar and Analytics nav buttons went from 48 to 50px CSS so they still render at ≥44px at 0.9×. |

### Sizing at the fixed 0.9× scale

Everything inside `#zoomWrapper` renders at 0.9× its CSS size (modals are outside it and render at 1×). The Tasklist and date bar are sized so the **rendered** floors hold: 50px CSS → 45px on screen. Measured at 1180×820 with 5 people: columns 218px, rows ≥72px (79.9), circles and buttons ≥44px (44.1), task titles 17.1px, wrapping and never truncated. Week chips are 47×54px, chevrons 45×54px.

## Verification (2026-10-10, v14.1 branch)

Served over http (`python3 -m http.server`) and driven in headless Chromium (Playwright). Test data had 5 people (daily, weekly, monthly, once and range tasks; past completions and skips; reward and perfect-day goals; weekly, monthly and multi-day events) and the long title "Choose and Fill Snack and Fill Water Bottle".

| Check | Result |
|---|---|
| 1180×820: all 5 columns on one row at the fixed Small size, none underneath; whole-row tap completes and persists after reload; celebration and skip + Undo still work | PASS |
| 1024×768 with 6 people, and 1180×820 with 8 people (scrolls in one row): one row, floors hold | PASS |
| 834×1112: one snap-scroll row (252px columns); page `scrollWidth` = viewport; every column reachable; a touch swipe scrolls and snaps | PASS |
| 390×844 phone: no sideways page scroll; the week strip scrolls inside itself and keeps the viewed day in view | PASS |
| Settings: one click or touch tap opens it and it stays open (checked 1.5s later); a name edit persists after Done + reload; backdrop tap closes it; no hold code or markup remains | PASS |
| No size/zoom control anywhere; `zoomIndex` 0, 1 and 2 render identically at `scale(0.9)` and the stored value is untouched | PASS |
| Date bar: yesterday's chip shows yesterday's tasks and completions; checking a task while viewing yesterday writes to yesterday and persists after switching tabs and reloading; › advances exactly 7 days; the month popup jumps to a picked date; the Today pill appears off-today and returns in one tap | PASS |
| Today tab person-row tap jumps to the Tasklist and flashes the right column, at 1180×820 and in the 834×1112 snap strip | PASS |
| Kid view: opens, completes a task, Back shows the same state; its CSS is unchanged (56px circle, 84px rows) | PASS |
| v13-shaped dataset (events with `date`, tasks missing `order`/`skippedDates`) loads with nothing lost; after use the saved JSON has no new or removed fields; zero JS/console errors on every tab | PASS |
| Both `<script>` blocks pass `node --check`; the four meta tags are in `<head>` | PASS |
| No interactive element renders under 44px on any tab (automated sweep) | PASS |

**Known limits:**
- Fixing the app at 0.9× means text set at 14–15px CSS outside the Tasklist renders at about 12.6–13.5px: the weather description and place, the Up-next kicker, map links, Calendar weekday headers and event chips, and some Analytics labels. That is under the v14 14px type floor. The Tasklist and date bar meet it. See BACKLOG.
- Kid view is unchanged in CSS but, like everything, renders at 0.9×. Rows are about 76px and circles about 50px, still above its 72px/44px floors.
- The date bar is not sticky. `position: sticky` drifts inside the scaled wrapper, so the bar sits as the first row under the tabs instead.
- `api.open-meteo.com` is blocked by the builder sandbox (weather shows "Tap to retry"). github.io was not checked from the sandbox; Kakashi verifies the live site.

## Previous version: v14

- Tier 1 commit `9524cee`, Tier 2 commit `2cffe3c`, docs `402ebaa` (`claude/our-week-v14-kid-design-hnpgn4`, merged to `main`). 136,498 bytes, sha256 `4c5a29a239aabbfb1b0315737c72854d11fddefcf7de7d43558c6b2147c5175f`.
- v14 changes **no** saved-data shapes: every item is presentation/interaction only, computed from existing data.

### What changed in v14

The research behind this pass: NN/g child-UX testing (2cm targets for under-9s), and the Skylight, Hearth, Cozyla and DAKboard boards plus kid chore apps (3-field add flows, emoji for pre-readers, a celebration on a finished list).

#### Tier 1 — big taps and simple adding

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

#### Tier 2 — kid view and parent-safe touches

- **D1 Kid view:** tap a person's avatar or name on the Tasklist to open a single-person view.
  - It has rows at least 80px tall (84px), 22px titles, 56px circles, their badges, progress and goal shelves, and one 60px "← Back".
  - Tabs, zoom and Settings are hidden.
  - Completing, the celebration, skip and Undo all work there the same way.
  - The person's star calendar (which used to open from the name) is now a 📅 button on the column header.
- **D2 Streak flame:** each person's current run of consecutive perfect days, computed from existing completions.
  - Days with nothing due are skipped, and today doesn't break the run while it is still in progress.
  - "🔥 N" shows beside ⭐ when N is 2 or more.
- **D3 Reward shelf:** the reward-goal box became a shelf card: a big ⭐, the reward as the headline, "points / target" and a thick bar. The perfect-day goal got a matching 🌟 card. It is display only, with no redemption.
- **D4 Settings guard** *(removed in v14.1: Settings opens on a plain tap again)*: Settings opened only after a 2-second press-and-hold on the gear.
  - A ring fills and a "Keep holding…" label shows while holding. A short tap shows "Hold for 2 seconds".
  - It works with keyboard Enter/Space held down. Nothing is stored and there is no PIN.

#### v14 verification (2026-10-10, v14 branch)

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

v14's known limits (sub-44px targets at "Small"; a 5-person board wrapping on narrower screens) are resolved in v14.1.

## Project rules

- The owner has authorized this project to run **in parallel with Math Quest**, in its own separate repo (`zerononeex/family-board`).
- Only one builder works inside Family Board at a time.
- Agents working here never touch Math Quest repos.

## Older version: v13

v13 (`60289e9`, 105,621 bytes, sha256 `580735d7…6610e42`) was the owner's upload, committed verbatim and verified on 2026-10-09/10. Its full verification notes are in git history.

## Next step

Kakashi verifies the live site after the v15 merge. Further ideas go in [BACKLOG.md](BACKLOG.md).
