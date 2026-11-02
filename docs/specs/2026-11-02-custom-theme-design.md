# Custom theme + palette suggestions — design

Status: **awaiting approval** (nothing built yet)

## Intent
Alongside Lavender, Ocean, Midnight and Sepia, let the user make their own theme by choosing 2–3 colours (hex text box or colour picker). The app fills in everything else so the result looks designed, not random. A "describe a vibe" helper suggests palettes so the user doesn't have to hunt for hex codes.

## Goals
- A fifth swatch, "Custom", in the existing theme switcher. Clicking it opens a popup.
- Three slots, each with a colour picker and a hex box kept in sync: **Main** (required), **Accent** (optional), **Background** (optional). An empty optional slot is filled automatically.
- Everything else derives from those colours (soft/faint tints, ink shades, tag/secondary set, card colour, shadows, blobs, star/heart colours).
- A dark Background automatically switches the theme to a dark layout with light text.
- Live preview inside the popup before saving. Cancel leaves the current theme unchanged.
- Palette suggestions, two ways:
  1. **Vibe box**: type words like "sunny ocean"; the app shows up to 3 palette options from a built-in dictionary; tapping one fills the slots.
  2. **Paste box**: paste anything containing hex codes (e.g. copied from colormagic.app); the app pulls out the codes and offers them as one palette option. An "Open ColorMagic" link sits beside it.
- The choice persists across sessions, like the existing themes.

## Non-goals / anti-goals
- No network calls for suggestions. ColorMagic's search API sends no CORS headers (checked with curl, not in a real browser fetch), so a direct call from the file-based app would likely be blocked.
- No AI-generation or API-key setup (possible later).
- No multiple saved custom themes in v1 (one custom theme).
- No per-element colour editing. Three colours in, the rest derived.
- Prices/sales stay out of the collection screen (unrelated; see shopping-list-design memory).

## Constraints (from the existing code, `index.html`)
- Themes are blocks of ~35 CSS variables (lines 26–29) selected by `data-theme`; choice stored under localStorage `otomeCdTheme`; applied before paint by the inline script on line 15; switcher logic at ~line 3458 with a `META` allow-list; swatch buttons at ~lines 532–535 with CSS at ~429–432.
- Midnight has extra rules keyed to `[data-theme="midnight"]` (glowing cards, shopping-list tag colours, `color-scheme:dark`, blend mode). A dark custom theme should get the same treatment.
- Header label art is a pre-made PNG per theme (`--label`, lines 64–67).
- Single-file app, works from `file://`.

## Approaches considered
1. **Fixed labelled slots + derivation + paste box + built-in vibe dictionary** (chosen). Always works offline, predictable output.
2. Unlabelled colours, app guesses roles. Rejected: surprising results; user asked to see an example and chose labelled slots.
3. Live ColorMagic lookup via proxy, or Claude API with a stored key. Deferred: setup, CORS, cost.

## Design

### Derivation (prototype exists; the builder should port it)
Working in HSL from the Main colour (hue `mh`, saturation capped around 75):
- Accent (if blank): hue `mh+50`, saturation 45–85, lightness 72. If given, used exactly.
- Light theme: ink = Main at L27; ink-soft L45; soft tint L90; faint tint L96; tag background = Accent hue L92, tag text = Accent hue L30; card = background (white stays white; tinted backgrounds get a lighter card).
- Dark theme (background luminance < 0.25): ink L90; ink-soft L72; soft L26; faint L19; card = background lightened (+7 L, max 30); tag bg/text darker/lighter equivalents.
- Text on Main-coloured buttons: dark or white, chosen by Main's luminance.
- Output must populate the full variable set used by the four built-in themes (`--bg`, `--card`, `--accent*`, `--ink*`, `--pink*` (secondary set), `--err`, `--ok`, `--star-*`, `--shadow-rgb`, `--scrim-rgb`, `--b1..b7`, `--chev` SVG with the accent colour, etc.).

### Popup
- Opens from the Custom swatch (re-opening edits the saved custom theme).
- Layout as in the mockup shown in the brainstorm: slots on the left, live preview and derived-palette strip on the right; Cancel / Save theme.
- Validation: accept `#RGB`/`#RRGGBB`, with or without `#`; inline error text for bad codes; Main required.
- Vibe box and paste box live in the same popup, above the slots. Suggestions appear as 5-colour strips, tap to apply (same interaction as ColorMagic's "which fits best" row).

### Palette → slots mapping (for suggestions and pasted palettes)
- Background: lightest colour, or darkest if the palette is mostly dark.
- Main: most saturated mid-tone of the rest.
- Accent: of the remaining colours, the one furthest in hue from Main.
- Slots remain editable after applying.

### Vibe dictionary
- ~30–40 keywords (e.g. sunny, ocean, sakura, midnight, matcha, cozy, autumn, candy, forest, lavender, ice, sunset), each tied to 2–3 hand-written 5-colour palettes. A prompt's words are matched (loosely, normalised); palettes are ranked by how many words hit; top 3 shown.
- No match: message "No match for that. Try ColorMagic and paste the codes" with the link.

### Persistence
- `otomeCdTheme = "custom"` and a new key (e.g. `otomeCdCustomTheme`) holding the three picked colours (not the derived values, so derivation can improve later).
- The pre-paint script on line 15 must apply the custom variables immediately to avoid a flash of the wrong theme.

### Where this goes in the file
Add `custom` to the switcher's allow-list; apply derived variables as inline style on the root element (or a generated style tag) when `data-theme="custom"`.

## Left to the builder (my calls, flagged)
- **Header label art:** my call is to reuse the Lavender label and recolour it with a CSS hue-rotate/filter matched to Main; fall back to plain Lavender if it looks bad.
- **Dark mode extras:** generalise the Midnight-only rules (glow, `color-scheme`, shopping tags) so a dark custom theme gets them, e.g. via a `data-mode="dark"` attribute set alongside `data-theme`.
- Exact dictionary contents and wording of messages.
- Contrast guard: ensure ink on background and button text on Main stay legible for extreme picks (very pale or very dark Main).
- Version bump and commit message following the repo's convention (last was v0.6.1).

## Size
Project-sized but self-contained: one popup, one derivation function, one dictionary, small edits to theme switching and a few CSS selectors.
