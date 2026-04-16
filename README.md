# Toronto Dashboard

A single-file morning-briefing dashboard for Toronto — live weather, sports, events, and coffee picks, rendered in one page.

🔗 **Live:** [add your Netlify URL here once deployed]

## What it does

- **Live 7-day weather** from [Open-Meteo](https://open-meteo.com) — fetches on every page load
- Morning / Afternoon / Evening breakdown with temperature, precipitation, and wind for each window
- Walk score and layer recommendation per day (base / mid / shell)
- Tap a day card to expand details; tap a time-of-day slot to see slot-specific weather
- City sports, events, and coffee recommendations (manually curated)

## How it works

Everything lives in one `index.html` file — no build step, no dependencies, no backend. The file has two parts:

- A **data block** containing hardcoded arrays for events, coffee picks, etc.
- A **template block** with the rendering logic (forecast cards, detail panel, walk scoring, layer system)

On page load, the script fetches Open-Meteo's public API and maps the response into the data shape the renderer expects. If the fetch fails (offline, API issue), the page gracefully falls back to a loading placeholder.

## Local preview

Open `index.html` directly in a browser — that's it.

## Deploy

Hosted on Netlify with auto-deploy from this repo's `main` branch. Every push triggers a rebuild; changes go live in ~30 seconds.

## Data sources

| What | Source | Auto-refresh? |
|---|---|---|
| Weather (7-day, hourly) | Open-Meteo API | ✅ Every page load |
| Calendar / reminders | Manual `EVENTS` / `REMINDERS` arrays | ❌ Manual |
| Sports schedule | Manual `CITY_SPORTS` array | ❌ Manual |
| City events | Manual `CITY_EVENTS` array | ❌ Manual |
| Coffee picks | Manual `CITY_FOOD` array | ❌ Manual |

## License

Personal project. No license — not intended for redistribution.
