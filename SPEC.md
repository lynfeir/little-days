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

## Design system

- Palette (dark, default at night): plum ink `#211b31` / surfaces `#2a2340` / text warm milk `#f5efe6` / accent apricot `#f2a97e` / sage `#a9c7a1` / gold `#e8c87e`. Light: warm blush-linen `#faf3eb`, deep plum text `#38304e`, deeper apricot `#e08856`.
- Type: Fraunces (soft storybook serif) for the age hero and screen titles; system stack for UI; tabular numerals on all counters.
- Signature: the breathing apricot halo behind the age hero — the calm heartbeat of the app — plus star confetti when a milestone or first is logged.
- Both themes are token-driven (`prefers-color-scheme` + manual `data-theme` override).
