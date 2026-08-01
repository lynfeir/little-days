# Little Days — design spec

## Product

One question drives the app: **"what should I do with my baby today, and is she on track?"** — answered without accounts, subscriptions, or data leaving the device.

## Platform requirements

- **PWA-first**: manifest (`standalone`, maskable icons), service worker (precache app shell, network-first navigations, cache-first statics, stale-while-revalidate fonts), installable on iOS/Android/desktop, fully offline after first load.
- **Mobile-app functionality**: fixed bottom tab bar in the thumb zone (research: bottom third of screen is the one-handed reach zone; top bar is status-only), 44–48px minimum touch targets, bottom sheets instead of centered dialogs on mobile, safe-area insets, `overscroll-behavior` locked, no tap highlight flash.
- **Desktop-functional**: at ≥900px the tab bar becomes a floating left rail, dashboard cards flow into a 2-column grid, sheets become centered modals. Keyboard focus visible everywhere.

## Screens

| Tab | Job |
|-----|-----|
| Today | Age hero (name, months+days, weeks/days chips, adjusted-age chip), next special day, 3 rotating daily activities, "around this age" phase notes, current checklist progress, recent first |
| Milestones | 12 CDC brackets as pill tabs (current one dotted), 4 categories per bracket, tap-to-check with date stamp + confetti, gentle "earlier items unchecked" note, act-early guidance |
| Firsts | Preset grid of 18 classic firsts + custom entries; each stores date + note |
| Days | Auto-generated: month-days 1–11, 100th day, ½ birthdays, 1,000th day, birthdays 1–5; custom one-time/yearly days; past days collapsed |
| More | Profile (name, DOB, weeks-early for adjusted age), theme, install helper, backup export/copy/import, about + disclaimer, reset |

## Data model (`localStorage["littledays.v1"]`)

```json
{
  "v": 1,
  "profile": { "name": "", "dob": "YYYY-MM-DD", "earlyWeeks": 0 },
  "checks": { "<bracket>:<category>:<index>": "YYYY-MM-DD" },
  "firsts": [{ "id": "", "e": "", "t": "", "date": "", "note": "", "custom": true }],
  "customDays": [{ "id": "", "t": "", "date": "", "yearly": false, "e": "" }],
  "settings": { "theme": "auto|light|dark", "installDismissed": false, "lastBackup": null }
}
```

## Rules that matter

- All date math is **local-time** (`YYYY-MM-DD` parsed by parts — never `new Date(string)` UTC drift).
- Month-iversaries clamp to month length (born the 31st → Feb 28).
- **Adjusted age** (if born ≥3 weeks early) drives checklist bracket, activities, and phase notes until 24 months — standard pediatric practice.
- Current bracket = first checklist age ≥ (adjusted) age in months.
- Daily activities are seeded by date, so they rotate every day deterministically; "More ideas" reshuffles.
- Milestone framing follows CDC 2022: "most babies (75%+) do this by…" — reassuring, never alarmist; concerns → pediatrician, with the act-early link.
- iOS can evict web storage after long disuse → backup export/import is a first-class feature, and the app nags gently in More.

## Design system (v2 — "sticker book nursery")

- Palette (light, primary): shell cream `#fff5ef`, cocoa-plum ink `#4d3b52`, melon `#ff8b6a`, butter `#f7b955`, leaf `#48b187`, berry `#f27da0`. Dark ("nursery at night"): cocoa `#2a2033`, surfaces `#352941`, glow-soft accents (`#ffa184` etc.).
- Type: Fredoka (chubby rounded) for display, headings, buttons, numbers; system stack for body; tabular numerals on counters.
- Signature: **the nursery arch** — every photo lives in an arch frame (hero portrait, monthly grid, share cards, sheet pickers), plus sticker-tilted emoji chips, drifting sorbet blobs behind the hero, emoji tab bar (☀️ ⭐ 💛 🎈 🧸).
- Both themes are token-driven (`prefers-color-scheme` + manual `data-theme` override).

## Photos (v2)

- Stored in IndexedDB (`littledays` → `photos`), keyed `first:<id>`, `month:<n>`, `year:<k>`; localStorage only holds boolean flags (`firsts[].photo`, `settings.monthPhotos`).
- On-device compression before save: ≤1600px longest edge, JPEG q0.82 (`createImageBitmap` with EXIF orientation, `<img>` fallback).
- Rendering: views emit `<img data-photo="id">`, `hydratePhotos()` fills `src` from cached object URLs after each render.
- Sheets stage changes in `state.pendingPhoto` (blob | 'remove' | undefined) and commit only on Save.
- Share cards: 1080×1350 canvas (cream ground, sorbet blobs, arch-clipped photo with dashed melon frame, Fredoka text, age-at-the-time line) → `navigator.share({files})` with download fallback.
- Backup v2: export embeds photos as data URLs (`backup: 2, photos: {}`); import accepts v1 (no photos) and v2.
