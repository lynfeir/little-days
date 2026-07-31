# Little Days 🌙

A free, private baby milestone tracker — built because the "free" apps paywall the part you actually need.

**No backend. No account. No ads. No tracking.** Everything lives in `localStorage` on your device.

## What it does

- **Today** — exact age (months/days/weeks/days-old), three age-matched activity ideas that rotate daily, "what's typical around now" notes, next special day countdown
- **Milestones** — all 12 CDC "Learn the Signs. Act Early." checklists (2 months → 5 years, 2022 revision), tap to check off with the date, per-checklist progress
- **Firsts** — a memory log for first smile, first laugh, first steps… with dates and notes, plus custom firsts
- **Days** — auto-generated special days (month-days, 100 days, half birthdays, 1,000 days, birthdays) plus your own
- **More** — profile, adjusted age for preemies, light/dark theme, JSON backup export/import, install help

## PWA

Installable on iOS (Safari → Share → Add to Home Screen), Android, and desktop Chrome/Edge. Works fully offline after first load (service worker precaches the shell).

## Run locally

Any static server, e.g.:

```
npx http-server . -p 4173
```

## Content sources & disclaimer

Milestone checklists are from the CDC's public-domain ["Learn the Signs. Act Early."](https://www.cdc.gov/act-early/milestones/index.html) program — items 75%+ of babies do by each age. This app is a keepsake and guide, **not medical advice**; concerns belong with your pediatrician.
