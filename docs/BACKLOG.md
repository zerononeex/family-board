# Backlog

## Design / look

- [x] **v14 C2** Type floor: no kid-facing text under 14px; list titles ≥16px (19px Tasklist, 22px kid view); section titles ≥20px — *shipped v14*
- [x] **v14 A3** Completion celebration: emoji burst over the column + count-up when a person's day hits 100% (non-blocking) — *shipped v14*
- [x] **v14 A5** Today summary as big per-person rows (avatar, "3 of 5 done", thick bar, 28px dots) — *shipped v14*
- [x] **v14 C1** Today order: date + live clock, weather, Up next, today's events, per-person rows — *shipped v14*
- [x] **v14 D2** Streak flame 🔥 N (2+ consecutive perfect days, computed, display only) — *shipped v14*
- [x] **v14 D3** Reward shelf card (⭐, reward headline, points / target, thick bar; display only) — *shipped v14*

## Interface / usability

- [x] **v14 A1** Whole-row task completion (72px rows, 44px circle, long-press to edit, 44px ✎/×) — *shipped v14*
- [x] **v14 A2** 44px target floor everywhere; tabs/topbar 52px; calendar nav 48px; modal buttons 52px — *shipped v14*
- [x] **v14 A4** Undo toast (5s) for skip; no confirm dialogs for skip/uncheck — *shipped v14*
- [x] **v14 B1** Calendar day tap → quick-add sheet (title + who + Save; rest under More options) — *shipped v14*
- [x] **v14 B2** Repeat chips (Never / Every day / Weekdays / Every week / Monthly) in event and task forms — *shipped v14*
- [x] **v14 B3** Task quick-add with person preselected, repeat chips and emoji quick-pick strip — *shipped v14*
- [x] **v14 B4** Big "+ Add" buttons on Today and in the Calendar header — *shipped v14*
- [x] **v14 D1** Kid view: single-person, extra-large rows, one big Back button — *shipped v14*
- [x] ~~**v14 D4** Settings guard: 2-second press-and-hold on the gear~~ — *shipped v14, removed in v14.1 (menu vanished on release; guard not wanted)*

- [x] **v14.1 Item 1** Option A Tasklist: all people on one row (≥1024px, 2–6 equal columns; 7–10 scroll), snap-scroll strip under 1024px, compact person head — *shipped v14.1*
- [x] **v14.1 Item 2** Settings opens on a plain tap; press-and-hold removed entirely — *shipped v14.1*
- [x] **v14.1 Item 3** Size picker removed; the board is fixed at the old "Small" scale (0.9×); stored `zoomIndex` left untouched — *shipped v14.1*
- [x] **v14.1 Item 4** Tasklist date bar: 7-day week strip, week ‹ ›, date label → month picker, Today pill — *shipped v14.1*
- [x] **v14.1 Item 5** Home-screen meta tags (app-capable, status bar, title, theme colour) — *shipped v14.1*
- [x] **v14.2** Remove the "+N ⭐" points label from every task row (Tasklist + Kid View); points still count everywhere else — *shipped v14.2*
- [x] **v15 Items 1–4** Four switchable Tasklist layouts (A Compact Columns, B Family Rows, C Colour Columns, D People Rail) with shared rendering, saved per device in `family-board-ui-v1` — *shipped v15*
- [x] **v15 Items 5–9** Seasonal themes for the whole app (Halloween, Christmas, New Year, Valentine's, Canada Day), Off/Auto/manual, themed celebrations — *shipped v15*
- [x] **v15.1** Person names never wrap or split mid-word (one line everywhere; font shrinks to a 15px floor, ellipsis only past it) — *shipped v15.1*
- [ ] Very long names (about 15+ characters) in narrow 5-person columns at 1180px reach the 15px floor and end in an ellipsis; a nickname/short display name could avoid that if it ever matters
- [ ] Portrait iPad: a themed header has only ~10px to spare; if emoji render wider on a device, shorten the motif to one emoji below 1024px
- [ ] Optional: Kid View "Back" in Layout D could select that person in the rail
- [ ] Text outside the Tasklist that is 14–15px CSS (weather description/place, Up-next kicker, map links, Calendar weekday headers and event chips, some Analytics labels) renders at ~12.6–13.5px at the fixed 0.9× scale; raise to 16px CSS to restore the 14px floor
- [ ] Date bar could stay pinned while scrolling long columns; `position: sticky` drifts inside the scaled `#zoomWrapper`, so it needs a different approach

## Features

## Bugs
