# Travel Itinerary Generator

A single-file, client-side trip planner. Enter a destination, trip length,
number of travelers, and budget, and it generates a day-by-day itinerary —
activities, dining, and lodging.

## How it works

- **Geocoding**: [Nominatim](https://nominatim.org/) resolves the destination to coordinates.
- **Place data**: the [Overpass API](https://wiki.openstreetmap.org/wiki/Overpass_API) pulls real nearby attractions, restaurants/cafes, and lodging from OpenStreetMap within a ~9km radius. No API key or signup required.
- **Cost estimates**: since free map APIs don't expose pricing, costs are modeled from a budget-tier heuristic (budget / comfort / luxury) derived from your total budget, trip length, and group size — not live rates.
- **Fallback**: if OpenStreetMap is unreachable, or a destination has too few real listings, the app falls back to a simulated itinerary so it always produces a result.

No build step, no framework, no backend — just one HTML file with inline CSS/JS.

## Running it

Because it calls external APIs via `fetch`, some browsers restrict this when
the file is opened directly (`file://`). Serve it locally instead:

```
python3 -m http.server 8000
```

then open `http://localhost:8000/index.html`.

## Disclaimer

Activity/dining/lodging names and locations are real (sourced from
OpenStreetMap contributors, ODbL license). Prices shown are illustrative
estimates only — always verify availability and pricing before booking.
