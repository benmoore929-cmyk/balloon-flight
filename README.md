# 🎈 High-Altitude Balloon — Flight Analysis Dashboard

Interactive analysis of **two high-altitude balloon flights** launched from the same
site in central-west NSW on **1 July 2026**, built from the raw payload telemetry
(GPS, SenseHAT environment, SenseHAT IMU, satellite modem, and — on Balloon 2 — a
camera). Switch between the two flights, scrub through the mission timeline, and see
every sensor at any instant.

**Live:** https://benmoore929-cmyk.github.io/balloon-flight/

## Open it locally

**Just double-click `index.html`.** No install, no server, **no internet required.**
The data (`flight_data.js`) and the charting/map libraries (`vendor/`) are bundled
locally, so it works fully offline and behind school content filters. The only thing
that uses the internet is the map's street/satellite *basemap* — if that's blocked,
the altitude-coloured track still draws on a plain background.

> Keep the folder together: `index.html` must stay next to `flight_data.js` and the
> `vendor/` folder.

## What you can do

- **Switch flights** — the **Balloon 1 / Balloon 2** buttons at the top swap the whole
  dashboard. Both tracks are always shown on the map: the selected flight coloured by
  altitude, the other in faint grey for comparison.
- **Flight map** — track coloured blue (ground) → red (apogee), with launch / burst /
  landing markers and the post-landing recovery drift. Toggle Map / Satellite top-right.
- **Synced charts** — hover any chart and a cursor appears on all of them, with the map
  marker and the bottom readout jumping to that instant.
- **Scrubber & playback** — drag the timeline or press ▶ (or **spacebar**) to fly
  through the mission. `1× / 60× / 200×` set speed; arrow keys step 1 s (Shift = 1 min).

## The two flights

| | Balloon 1 | Balloon 2 |
|---|---|---|
| Launch (UTC) | 01:25:04 | 01:05:27 |
| **Peak altitude** | **26,534 m** | **35,902 m** |
| Flight duration | 1 h 13 m | 1 h 44 m |
| Mean / max ascent rate | 7.9 / 12.9 m/s | 7.6 / 12.4 m/s |
| Max descent rate | 53 m/s | 68 m/s |
| Max ground speed | 312 km/h | 312 km/h |
| Track / straight-line distance | 154 / 147 km | 250 / 239 km |
| Min pressure | 16.1 hPa | 0.4 hPa\* |
| Peak g-force | 6.0 g | 6.6 g |
| Photos captured | — | 104 |

Both balloons lifted off from the same field (≈ −31.50, 145.83) about 20 minutes
apart and drifted east on a strong winter jet stream, landing near Dubbo.

## Reading the sensors

- **Pressure** is the best altitude cross-check — Balloon 1's falls to 16 hPa at 26 km,
  mirroring GPS altitude. \*On **Balloon 2** the sensor bottoms out at ~0.4 hPa near
  36 km (below its rated range); the true pressure there is ≈ 5–6 hPa.
- **Temperature / humidity** are from the SenseHAT *inside* the payload, so they reflect
  internal (self-heated) conditions, not ambient air — Balloon 2's electronics ran hot
  (54–66 °C). Balloon 1's humidity dips negative in the cold stratosphere (sensor artifact).
- **Ground speed** uses the GPS module's own Doppler speed (`speed`/`speed_knots`, knots).
  Differencing raw positions instead yields a spurious ~490 km/h from single-fix jumps;
  the Doppler figure (~312 km/h — a genuine strong jet stream) is trustworthy.
- **Acceleration** peaks at ~6 g during the violent descent tumble, not at burst.
- **Camera** (Balloon 2 only): 104 stills captured during the flight.

## Files

| File | Purpose |
|---|---|
| `index.html` | The dashboard (open this). |
| `flight_data.js` / `flight_data.json` | Processed, resampled data for both flights. |
| `process_data.py` | Rebuilds the data from the raw CSVs. |
| `vendor/` | Bundled Leaflet + uPlot libraries (so it runs offline). |

## Regenerating the data

`process_data.py` reads each flight's raw CSVs, auto-detects launch/burst/landing,
resamples every stream onto a common 1-second mission timeline, derives the metrics,
and writes `flight_data.js` + `.json` with both flights. GPS is parsed *positionally*
(the flights' headers differ but the column order matches). Pure Python standard
library — no dependencies:

```
python3 process_data.py
```

The raw-CSV locations are set in the `FLIGHTS` list at the top of the script.
