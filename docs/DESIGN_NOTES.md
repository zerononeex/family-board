# Design Notes

This describes the current design system as found in `index.html` (v15). It records what exists today and does not propose changes.

## Typography

- **Headings and display text:** Fraunces (serif), weight 600, letter-spacing -0.01em.
  - Used for `h1`–`h3` and `.display`.
  - Also used for person names, weather temperature, event titles and analytics titles.
- **Body and UI text:** Figtree (sans-serif). Buttons and inputs inherit it.
- Both fonts load from Google Fonts:
  - Fraunces opsz 9..144, weights 400/600/700.
  - Figtree weights 400–800.

## Colour variables (`:root`)

| Token | Value | Token | Value |
|---|---|---|---|
| `--bg` | `#EEF3ED` | `--surface` | `#FFFFFF` |
| `--surface-2` | `#F7FAF6` | `--ink` | `#1F2A24` |
| `--ink-soft` | `#5B6960` | `--line` | `#DCE5DC` |
| `--marigold` | `#E8A33D` | `--marigold-ink` | `#7A4E12` |
| `--sage` | `#4E7B6D` | `--sage-deep` | `#34544A` |
| `--coral` | `#E8735F` | `--plum` | `#8B5FBF` |
| `--teal` | `#3E9B8C` | `--sky` | `#4A90C2` |
| `--danger` | `#C4523F` | `--berry` | `#B0475A` |
| `--olive` | `#7A8B4E` | `--indigo` | `#5A6FB0` |
| `--amber` | `#C98A2B` | `--slate` | `#55707A` |
| `--cat-event` | `#34544A` | | |

- **Person colours** are `--coral`, `--marigold`, `--teal`, `--plum`, `--sky`, `--berry`, `--olive`, `--indigo`, `--amber`, `--slate` (in that order). They identify people, so no theme ever overrides them.
- `--cat-event` (v15) is the calendar "Event" chip colour. It used to read `--sage-deep`, which themes recolour, so it is now a fixed token with the same value.

## Radius and shadow tokens

- `--radius-lg: 22px`, `--radius-md: 14px`, `--radius-sm: 9px`
- `--shadow: 0 2px 0 rgba(31,42,36,0.06), 0 10px 24px rgba(31,42,36,0.06)`

## Layout and interaction

- **Landscape, touch-first:** built for a wall or kitchen tablet.
  - One breakpoint, 1024px, for the task board. At ≥1024px every person is on one row (`grid-auto-flow: column`, equal columns for 2–6 people, 250px minimum and sideways scroll for 7–10). Below 1024px the board is one horizontal snap-scroll strip of 280px (CSS) columns.
  - A container query on each column moves ✎ × to their own line when a column is narrower than 236px (CSS).
  - The calendar has 7 columns.
- **Touch targets:** see *Kid-first floors* below. `touch-action: manipulation` is set.
- **Scale:** the whole `#zoomWrapper` is scaled to a fixed 0.9× (`BOARD_SCALE`, the old "Small" zoom) with a CSS transform. There is no zoom setting any more. Modals sit outside the wrapper and render at 1×.
- **Night-dim:** a CSS filter dims the page within a set time window.
- **Tap guard:** a script for the Documents by Readdle app sends taps to the page so that app's toolbar stays hidden. Tapping the title still shows the toolbar.

## Kid-first floors (v14)

These are hard minimums at the **rendered** size. Because the UI renders at 0.9×, CSS sizes inside `#zoomWrapper` must be about 11% larger (50px CSS → 45px on screen; 16px CSS → 14.4px). Adult guidelines (Apple 44pt, Material 48dp) are floors, not goals. NN/g found that children under 9 need targets of about 2cm × 2cm, so primary actions go bigger.

### Touch targets

| Element | Minimum |
|---|---|
| Any interactive element (button, chip, input, select, link, calendar cell) | **44 × 44px** |
| Main tabs, topbar icon buttons | 52px tall |
| Calendar / date nav buttons | 48 × 48px |
| Modal primary / secondary buttons | 52px tall (Save 60px) |
| Big "+ Add" buttons (Today, Calendar) | 56px tall |
| Day-picker chips, person chips, repeat chips | 44px (day) / 48px (person, repeat), gaps ≥10px |
| Task rows (Tasklist) | 72px tall, 44px state circle, whole row is the hit area |
| Tasklist week-strip chips | 52px tall, 44px wide (54 × 47 rendered); week ‹ › 44px |
| Task rows (kid view) | 80px tall (84px used), 56px circle |
| Kid view Back button | 56px (60px used) |
| Text inputs / selects | 48px tall, 17px text |

### Type

| Text | Minimum |
|---|---|
| Any kid-facing text | **14px** |
| Task / event titles in lists | 16px (Tasklist 19px, kid view 22px, Today events 19px) |
| Section titles | 20px |
| Today clock | 28px (34px used) |
| Today "N of M done" | 16px (17px used) |

Never shrink type to fit more content. Cut content instead.

### Interaction rules

- Celebrations never block input. Overlays use `pointer-events: none` and are never awaited.
- No confirm dialogs for skip or uncheck. Skip offers a 5-second Undo toast instead.
- A long-press on a task row (550ms) opens the edit sheet, and the click that ends that gesture is swallowed.
- Settings opens on a plain tap of the gear (the v14 press-and-hold was removed in v14.1).
- Add flows ask for the title only. Everything else has a sensible default, and the rarer fields sit behind "More options".

### New colour uses (existing palette)

- Streak badge: `#FDE6DF` background, `#9A3B23` text.
- Reward shelf: a warm `#FFF8EA → #FBF0DC` gradient with `--marigold` fill. The perfect-day shelf uses a `#F7F1FC → #F1EBF7` gradient with `--plum` fill.
- Up-next card: a `--marigold` left border.

## Tasklist layouts (v15)

Settings → **Tasklist layout** picks one of four arrangements. They all render the same data for the date chosen in the date bar, which stays above the board unchanged. Every layout builds each person from the same helpers: the header (`personColumnHeadHtml`: avatar/photo, name → Kid View, 📅, ⭐ / 🔥 / ✅ badges, progress bar, "x of y done"), task rows (`taskRowHtml`, `skippedRowHtml`), goal shelves (`goalBoxesHtml`) and "+ Add task". One delegated handler on `#choreBoard` handles row tap, long-press, ✎, ×, ↺ and rail selection for every layout. Layouts only change arrangement and styling.

| Layout | Container class | Arrangement | Rendered sizes at 0.9× |
|---|---|---|---|
| **A — Compact Columns** (default) | `.chore-board` | Exactly v14.2: every person on one row; snap-scroll strip below 1024px. | rows ≥72 (≈99 with stacked ✎ ×), circles 44, buttons 44 |
| **C — Colour Columns** | `.chore-board.layout-c` | A's columns, sizes and snap-scroll. Each column has a 10% tint of the person's colour, a 24% band behind the header, a 45% edge, white 20px-radius task cards, and the done check as a filled disc in the person's colour. | same as A |
| **B — Family Rows** | `.family-rows` | One full-width band per person, with an 8px left edge in their colour. A 300px identity block (shared header on a light tint) sits beside big task tiles (`repeat(auto-fill, minmax(300px,1fr))`), followed by the goal shelves and "+ Add task" in a wrapping row. Below 1024px the identity block sits above the tiles. | tiles ≥86 (96 CSS), circle 47, buttons 45 |
| **D — People Rail** | `.people-rail` | A 300px rail of person cards (avatar, name, badges, progress) and one main panel for the selected person: shared header, then Kid-View-scale rows **with** ✎ ×, goal shelves and "+ Add task". The selection is saved as `railPersonId` and falls back to the first person. Below 1024px the rail is a horizontal strip of pill chips that scrolls inside itself. | rows ≥83 (92 CSS), circle 50 (56 CSS), buttons 47 (52 CSS), titles 19.8 (22 CSS) |

- **Person colours in B, C and D** come from each person's hex (`COLOR_HEX`), set inline as `--pc`, `--pc-tint` (10%), `--pc-band` (24%) and `--pc-edge` (45%). They never come from the theme.
- **Celebration:** the burst plays over the person's column, band or main panel (`[data-person-col]`). In D the rail entry also flashes.
- **Today-tab jump:** A and C centre and flash the column. B scrolls the band to the top and flashes it. D selects the person in the rail and flashes the main panel.

## Seasonal themes (v15)

Settings → **Theme**: Off (default), Auto, 🎃 Halloween, 🎄 Christmas, 🎆 New Year, 💖 Valentine's, 🇨🇦 Canada Day. The preference is saved in `family-board-ui-v1` and resolves to `<html data-theme="none|halloween|christmas|newyear|valentines|canadaday">`.

**How a theme is built**
- A theme overrides only chrome roles: `--bg`, `--surface-2`, `--line`, `--ink-soft`, `--sage` (selected day chip), `--sage-deep` (headings, primary buttons, checks, selected chips).
- It also sets these decoration variables:
  - `--theme-title`: the "Our Week" colour.
  - `--theme-tab`: the active tab.
  - `--theme-motif`: two emoji after the title (20px below 1024px).
  - `--theme-strip`: a 7px strip at the top of the header.
  - `--theme-pattern`: a very faint CSS dot pattern on the page background.
- It never overrides: `--surface` (cards stay white), `--ink` (task text), the person colours, `--marigold` (a person colour, also used for the today ring and flash), or `--cat-event`.
- All decoration is pseudo-elements with `pointer-events: none`.
- Night-dim still works, because it is a filter on `<html>` that no theme touches.
- Every themed text role was checked to be at least as high-contrast as the base theme.

| Theme | `--bg` | `--surface-2` / `--line` | `--ink-soft` | `--sage` / `--sage-deep` | Title / active tab | Strip and pattern | Motif |
|---|---|---|---|---|---|---|---|
| Halloween | `#FBF1E6` | `#FFF6EC` / `#EFD9C2` | `#5E4A55` | `#A4460F` pumpkin / `#4B2A5E` deep plum | `#4B2A5E` / `#8A3A0C` | orange–plum blocks; faint orange and plum dots | 🎃👻 |
| Christmas | `#ECF3EF` | `#F4F8F5` / `#D3E2D9` | `#4A5F55` | `#1E6B48` / `#174A33` pine | `#174A33` / `#9E1C35` cranberry | candy-cane stripes with a gold line; white snow dots | 🎄🎁 |
| New Year | `#EDEFF7` | `#F5F6FB` / `#D8DCEC` | `#4B5270` | `#3A4A8C` / `#1E2A5A` navy | gold `#F3CD6B` on a navy header band (`#141C42 → #232F66`) / `#1E2A5A` | gold dashes; faint gold dots | 🎆✨ |
| Valentine's | `#FBEEF1` | `#FFF5F7` / `#F1D3DA` | `#6A4B53` | `#A8304F` rose / `#7E1A3F` berry | `#7E1A3F` / `#7E1A3F` | rose–pink blocks; faint rose dots | 💖💌 |
| Canada Day | `#F7F4F3` | `#FBF9F8` / `#E7DEDB` | `#57514F` | `#B71C1C` / `#2F3A40` charcoal | `#9A1218` maple / `#A8141B` | red–white–red flag bands; no pattern | 🍁🇨🇦 |

- **New Year fireworks:** a few CSS radial-gradient bursts drawn on `.topbar::after`, behind the header controls. A 7s ease-in-out opacity/scale glow, no flashing. `@media (prefers-reduced-motion: reduce)` turns the animation off.
- **Canada Day** keeps buttons and checks neutral charcoal. Red is only on the title, the active tab, the selected day and the strip.
- **Celebration emoji by theme:**
  - Halloween 🎃 👻 🦇 🍬 🌙 ✨
  - Christmas 🎄 🎅 🎁 ❄️ ⛄ 🔔
  - New Year 🎆 🎇 🥳 ✨ 🎉 🌟
  - Valentine's 💖 💘 ❤️ 🌹 💌 ✨
  - Canada Day 🇨🇦 🍁 🎉 ❤️ 🤍 ✨
  - No theme: the previous set 🎉 ⭐ ✨ 🌟 🎊 💛 🥳
- The `theme-color` meta tag follows the active theme's background.

### Auto: date windows and priority

`autoThemeForDate(date)` uses the device's **local** date, and the windows are inclusive. `resolveTheme(pref, date)` lets a manual choice win over the date. It is exposed read-only as `window.resolveSeasonalTheme` for testing.

| Priority | Theme | Window |
|---|---|---|
| 0 | Manual selection | always |
| 1 | New Year | Dec 31 – Jan 1 |
| 2 | Christmas | Dec 1 – Dec 26 |
| 3 | Canada Day | Jun 25 – Jul 1 |
| 4 | Halloween | Oct 1 – Oct 31 |
| 5 | Valentine's | Feb 1 – Feb 14 |
| — | none | any other date |

Auto is re-resolved:
- on load,
- on the day rollover (the 15-second timer),
- whenever the page becomes visible again, for an iPad left open across midnight.
