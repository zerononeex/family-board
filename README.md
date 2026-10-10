# Our Week — Family Board

A family board for the home: one shared screen for the week's tasks, events and weather. It is designed for a landscape, touch-first tablet.

The whole app is a single file, `index.html`, written in vanilla HTML, CSS and JavaScript. There is no build step, no framework, no backend and no API keys.

## Live site

**https://zerononeex.github.io/family-board/** (GitHub Pages, served from `main`, root folder)

> Status: GitHub Pages is **enabled and live** (verified 2026-10-09). See [docs/CURRENT_STATE.md](docs/CURRENT_STATE.md).

## What it does

- **Today:** current weather, today's events, and a summary of tasks still left.
- **Tasklist:** each person's tasks with points, progress, reward and daily goals, and skip-for-today, in one of four layouts (Compact Columns, Family Rows, Colour Columns, People Rail). A date bar jumps to any day of the week, any date, or back to today.
- **Calendar:** Event, Birthday, Holiday, Vacation and School Break entries.
- **Analytics:** week and month stats.
- **Settings:** Tasklist layout, seasonal theme (Off, Auto by date, Halloween, Christmas, New Year, Valentine's, Canada Day), up to 10 people, goals, night-dim, location, and backup/restore.

## Your data stays in your browser

- Everything you enter is saved **only in this browser's `localStorage`**, under the key **`family-board-data-v1`**.
- Nothing you enter is sent to a server. The repo holds no personal data, which is why it is public.
- The chosen layout and theme are per-device preferences, saved separately under **`family-board-ui-v1`**. They are not part of backups.
- Each browser or device keeps its own copy. To move or back up data, use **Settings → Export / Import**.
- Clearing site data in the browser erases the board. Export a backup first.
- The only network calls are:
  - Google Fonts, for Fraunces and Figtree.
  - Open-Meteo, for weather and the city search. These requests send only the chosen location.

## Run locally

Serve the folder over http. Opening the file directly (`file://`) can behave differently.

```sh
python3 -m http.server 8000
# then open http://localhost:8000/
```

## Update flow

1. Make changes on a **branch**, never directly on `main`.
2. **Verify** the branch before merging:
   - Serve it locally over http.
   - Check for zero console errors on load.
   - Check that all 4 tabs render and that Settings opens.
   - Add a task, check it off, reload, and confirm it persisted. Then clear the test data.
   - Never rename the `family-board-data-v1` key, or existing boards lose their data.
3. **Merge to `main`.** GitHub Pages republishes from `main`.

Project notes are in [`docs/`](docs/):

- [CURRENT_STATE.md](docs/CURRENT_STATE.md)
- [BACKLOG.md](docs/BACKLOG.md)
- [DESIGN_NOTES.md](docs/DESIGN_NOTES.md)
