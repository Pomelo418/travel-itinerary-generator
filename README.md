# ✈️ Travel Itinerary Generator

A single-file, client-side trip planner. Enter a destination, trip length, number of travelers, and budget — get back a day-by-day itinerary with real activities, dining, and lodging pulled live from OpenStreetMap.

**[Live demo →](https://pomelo418.github.io/travel-itinerary-generator/)**

No backend, no build step, no API key, no signup.

---

## What it does

1. You fill in a destination, number of days, number of travelers, and total budget.
2. The app geocodes your destination and looks up real nearby places — attractions, restaurants/cafés, and hotels — from OpenStreetMap.
3. It assembles a day-by-day plan (morning / afternoon / evening activities, three meals, a suggested stay) and estimates costs based on a budget tier derived from your inputs.
4. If OpenStreetMap is unreachable, or a destination doesn't have enough listed places, it falls back to a simulated itinerary so you always get a result.

## Features

- **Real place data, no API key** — geocoding and points-of-interest come from free, public OpenStreetMap services.
- **Step-by-step generation screen** — an animated checklist reflects the actual work happening (geocoding → searching → assembling), not a fake spinner.
- **Graceful degradation** — real data is preferred, but every slot (an activity, a meal, the hotel) independently falls back to a reasonable placeholder if live data comes up short, rather than the app breaking.
- **Transparent about estimates** — venue names and locations are real; prices are not (see [Disclaimer](#disclaimer)), and the UI says so.
- **Responsive, theme-aware UI** — works on mobile and desktop, supports light/dark mode via `prefers-color-scheme`.
- **Clear error recovery** — network or rate-limit failures show a specific reason and a one-click "use simulated data instead" fallback, instead of a dead end.

## Running it locally

Because the app calls external APIs via `fetch`, some browsers (notably Safari) restrict this when the file is opened directly as `file://`. Serve it over HTTP instead:

```bash
git clone https://github.com/Pomelo418/travel-itinerary-generator.git
cd travel-itinerary-generator
python3 -m http.server 8000
```

Then open `http://localhost:8000`.

## How it works

The whole app is one HTML file — inline CSS and vanilla JS, no framework, no dependencies, no bundler.

**UI flow** is a simple state machine over three screens (`form` → `generating` → `results`, plus an `error` screen), toggled by a single `showScreen()` call.

**Generation pipeline**: a small `runSteps(stepDefs)` runner drives the animated checklist. Each step is `{ label, task }`, where `task` is an async function — in real mode those tasks are actual `fetch` calls (geocode, then search nearby places); in simulated mode they're just timers. The UI code doesn't know or care which mode it's in.

**Data sources**:
| Purpose | Service | Notes |
|---|---|---|
| Geocoding | [Nominatim](https://nominatim.org/) | Resolves a destination name to coordinates |
| Places | [Overpass API](https://wiki.openstreetmap.org/wiki/Overpass_API) | Queries OpenStreetMap for `tourism`, `historic`, and `amenity` tags within ~9km of the destination |

Overpass results are messy by nature (inconsistent tagging, many unnamed nodes), so the app:
- classifies raw tags into `activity` / `dining` / `lodging`,
- deduplicates by name,
- computes real distances client-side with a Haversine formula (Overpass doesn't return distance directly),
- and cycles through the results to fill each day without repeats.

**Cost modeling** is intentionally a separate layer from the place data. Free map APIs don't expose pricing, so the app buckets the trip into a budget tier (`budget` / `comfort` / `luxury`) from `budget ÷ days ÷ travelers`, then estimates each activity/meal/night cost from a range appropriate to that tier. Place names are real; the numbers next to them are modeled, and the app says so.

## Project structure

```
.
├── index.html   # the entire app — markup, styles, and logic
└── README.md
```

## Disclaimer

Activity, dining, and lodging names and locations are sourced from OpenStreetMap (© OpenStreetMap contributors, [ODbL](https://opendatacommons.org/licenses/odbl/)). Prices are illustrative estimates only, not live rates — always verify availability, hours, and pricing before booking anything.

## Possible next steps

- A thin backend to cache/proxy Overpass and Nominatim calls instead of every client hitting the public API directly.
- Unit tests for the itinerary-building logic (it's pure functions — place pools + budget in, a day plan out).
- Persisting generated trips (localStorage, or accounts + a real database).

## License

[MIT](LICENSE)
