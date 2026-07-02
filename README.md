# 🎈 High-Altitude Balloon — Flight Analysis Dashboard

Interactive analysis of the HAB flight flown on **1 July 2026**, built from the raw
telemetry logs (GPS, SenseHAT environment, SenseHAT IMU, satellite modem).

## Open it

**Just double-click `index.html`.** No install, no server, **no internet required.**
The flight data (`flight_data.js`) and the charting/map libraries (`vendor/`) are all
bundled locally, so it works fully offline and behind school content filters.

The **only** thing that uses the internet is the map's street/satellite *basemap*
tiles. If those are blocked or you're offline, the map simply shows the
altitude-coloured track, markers and position dot on a plain background — everything
else is unaffected.

> Keep the folder together: `index.html` must stay next to `flight_data.js` and the
> `vendor/` folder. If double-clicking misbehaves, run a tiny server from this folder
> and open <http://localhost:8123> instead:
> ```
> python3 -m http.server 8123
> ```

## What you can do

- **Flight map** — the track is coloured by altitude (blue = ground → red = 26.5 km
  apogee). Launch / burst / landing are marked, and the dashed grey line is the
  post-landing recovery drift. Toggle Map / Satellite in the top-right.
- **Synced charts** — hover any chart and a cursor line + point appears on *all* of
  them, and the map marker + bottom readout jump to that instant.
- **Scrubber & playback** — drag the timeline or press ▶ (or the **spacebar**) to
  fly through the mission. `1× / 60× / 200×` set playback speed. Arrow keys step
  one second (Shift+arrow = one minute).
- **Live readout** — altitude, vertical rate, speed, pressure, temperature,
  humidity, acceleration, attitude, satellites, signal and position at the selected
  moment, with the current flight phase (Pre-launch / Ascent / Descent / Landed).

## Flight summary

| | |
|---|---|
| Launch → Burst → Landing (UTC) | 01:25:04 → 02:20:17 → 02:38:19 |
| Peak altitude | **26,534 m** |
| Flight duration | 1 h 13 m (55 m up / 18 m down) |
| Mean / max ascent rate | 7.9 / 12.9 m/s |
| Max descent rate | 53 m/s (post-burst free-fall in thin air) |
| Max ground speed | 312 km/h (GPS Doppler — jet-stream drift) |
| Track distance / straight line | 154 km / 147 km |
| Min pressure | 16.1 hPa |
| Peak g-force | 6.0 g (descent tumble, ~8 min after burst) |

## Reading the sensors

- **Pressure** is the most reliable altitude cross-check — it falls smoothly to
  16 hPa at apogee and mirrors the GPS altitude curve.
- **Temperature / humidity** come from the SenseHAT *inside* the payload, so they
  reflect internal (partly self-heated) conditions rather than ambient air. Humidity
  reading dips negative in the cold, dry stratosphere — a known sensor artifact.
- **IMU orientation** shows the payload tumbling/spinning throughout the flight
  (yaw sweeps the full 0–360°) and settling once it lands.
- **Acceleration magnitude** peaks at ~6 g during the violent descent tumble
  (~02:28, about 8 min after burst), not at the burst instant.
- **Ground speed** uses the GPS module's own Doppler speed. Differencing raw
  positions instead gives a spurious 489 km/h from occasional single-fix jumps —
  the Doppler figure (~312 km/h, still a genuine strong jet stream) is trustworthy.

## Files

| File | Purpose |
|---|---|
| `index.html` | The dashboard (open this). |
| `flight_data.js` / `flight_data.json` | Processed, resampled flight data. |
| `process_data.py` | Rebuilds the data from the raw CSVs. |
| `vendor/` | Bundled Leaflet + uPlot libraries (so it runs offline). |

## Regenerating the data

`process_data.py` reads the four raw CSVs, auto-detects launch/burst/landing,
resamples every stream onto a common 1-second mission timeline, derives the flight
metrics, and writes `flight_data.js` + `.json`. Pure Python standard library — no
dependencies:

```
python3 process_data.py
```

The raw-CSV location is set at the top of the script (`SRC_DIR`); edit it if you
move the source files.
