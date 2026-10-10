# Design Notes

This describes the current design system as found in `index.html` (v14.1). It records what exists today and does not propose changes.

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
