# Otome-CD-LITE

**Current version: 0.5.8** — shown next to the logo and at the bottom of the page. If the number in your copy is
lower than this, download the newest `index.html` again (your CDs stay in your browser, but make a Backup first).

A self-hosted, single-file Otome Drama CD collection tracker. No Python, no server, no build step:
open `index.html` in a browser.

## Features
- Pastel lavender "bubble" UI: filter bar on top, dense jacket-art card grid, **Toggle Labels** for a pure cover grid
- Detail popup with tracklist, synopsis, bonuses, ownership, source type (CD RIP / WEB RIP), rating and notes
- Voice actors shown in both romaji and 日本語; series names Japanese-first (a name dictionary that learns as you save)
- Add / edit CDs with online metadata lookup and a live cover preview
- Bulk **Import CDs** from rukojiDB, HIBIKIFORUM, DLsite Girls and SKiT Dolce (in-stock filter off by default)
- Everything is stored in your browser (localStorage); use **Backup / Restore** for JSON backups

## Versions
The version lives in one line near the top of `index.html` (`var APP_VERSION = '0.5.8';`). Raise it, and the
"Current version" line above, every time a new `index.html` is published. Backup files also record the version
that made them. Older copies are kept with the version in the file name, e.g. `otome-cd-database version 2026-10-29-0.5.1.html`.

## Notes
- Online lookups from a local page go through a free public CORS proxy (editable in the Add CD / Import dialogs).
- The rukojiDB reader was built from real pages; the DLsite, SKiT Dolce detail-page and HIBIKIFORUM readers are
  written to standard layouts and may need tuning against the live sites.
- Cover art and metadata belong to their respective labels, artists and source sites.
