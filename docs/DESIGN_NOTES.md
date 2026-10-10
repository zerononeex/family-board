# Design Notes

This describes the current design system as found in `index.html` (v13). It records what exists today and does not propose changes.

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
  - There are no `@media` breakpoints.
  - Grids flex instead: the task board uses `repeat(auto-fit, minmax(210px, 1fr))` and the calendar has 7 columns.
- **Touch targets:** round icon buttons are 46px tall, and `touch-action: manipulation` is set.
- **Zoom:** a zoom setting scales the whole `#zoomWrapper` with a CSS transform.
- **Night-dim:** a CSS filter dims the page within a set time window.
- **Tap guard:** a script for the Documents by Readdle app sends taps to the page so that app's toolbar stays hidden. Tapping the title still shows the toolbar.
