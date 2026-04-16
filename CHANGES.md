# What Changed — Live Weather Integration

Compared to the original static `weather-dashboard.html`, three things changed in `index.html`.

## 1. `const CURRENT` → `let CURRENT` (with loading fallback)

**Before:**
```js
const CURRENT = {
  temp:"12°C", cond:"🌫️ Dense fog", wind:"SSE 6 km/h · Thu Apr 16 · 🇨🇦 Toronto",
  refreshed:"Thu Apr 16, 2026 · 9:00 AM"
};
```

**After:**
```js
let CURRENT = {
  temp:"—", cond:"Loading…", wind:"🇨🇦 Toronto",
  refreshed:"Fetching live data…"
};
```

`let` instead of `const` so the fetch can reassign it. The placeholder values appear briefly while the network request is in flight — usually 100–300ms — then get replaced with real data.

## 2. `const DAYS` → `let DAYS` (with minimal placeholder)

The entire 7-day hardcoded block collapsed into a single "Loading…" placeholder. Same reasoning as above — `let` so the fetch can reassign. The placeholder only shows if Open-Meteo is unreachable.

## 3. New async boot at the bottom

The original file ended with a direct `renderCards(); renderDetail(); …` synchronous call. Now those calls happen **inside** `bootDashboard()`, which:

1. Calls Open-Meteo with Toronto's coordinates for 7 days of daily + hourly data
2. Runs `buildDaysFromOpenMeteo(data)` to map the response into the `DAYS` shape the renderer expects
3. Runs `buildCurrentFromOpenMeteo(data)` to set the header's right-now reading
4. Paints the header, then fires all the render functions
5. On fetch failure: logs the error, keeps the fallback values, adds a "⚠️ Live fetch failed" note to the footer

## Key decision: Morning/Afternoon/Evening aggregation

Open-Meteo gives 24 hourly readings per day. The code buckets them:

- Morning = hours 6–11 (6am to 12pm)
- Afternoon = hours 12–17 (12pm to 6pm)
- Evening = hours 18–21 (6pm to 10pm)

Inside each bucket, temp / wind / precip are **averaged**, and the weathercode is picked as the **mode** (most-common value). This feeds directly into your existing walk-score function — it already accepts weathercodes, so no scoring changes were needed.

## What did *not* change

Every bit of rendering logic, the Arc'teryx layer system, walk scoring, Milo's tips, sports / events / coffee sections, CSS — all untouched. The template is still frozen. Only the data layer is different.

## Known limitations

- **No Environment Canada alerts** — Open-Meteo doesn't surface Canadian weather alerts, so the `alert:true` flag that previously highlighted advisory days is no longer set automatically. You can manually add it back in `buildDaysFromOpenMeteo()` if you want a specific day flagged.
- **No live sports data** — still manual via the `CITY_SPORTS` array.
- **Fetch runs once per page load** — pull-to-refresh on mobile or Cmd/Ctrl+R refreshes it. It does not auto-poll.

## Test it yourself

After deploying, open the browser's developer tools → Network tab → reload the page. You should see a request to `api.open-meteo.com/v1/forecast` returning JSON. If that call fails, check the Console tab for the error message.
