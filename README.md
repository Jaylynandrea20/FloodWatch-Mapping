**Automated Sentinel-1 flood detection and impact assessment.**
*Detect. Understand. Prioritize.*

RapidFlood turns a pair of pre- and post-flood Sentinel-1 SAR images into a flood extent map, an impact assessment (settlements, roads, cropland), and rule-based decision support, all in a single workflow.

> ⚠️ **Demo data notice.** The pages in this repository run on a **fixed-seed synthetic scene**, not real satellite imagery. Results are reproducible but do **not** describe a real flood event. Live analysis with real Sentinel-1 data requires the Streamlit app with Google Earth Engine (see [Live mode](#live-mode)).

---

## Contents

| File | Description |
|------|-------------|
| `RapidFlood___Sentinel-1_Flood_Detection__Demo_.html` | Full offline demo dashboard with 7 analysis tabs (map, detection, replay, impact, SafeRoute, decision support, methodology). |
| `RapidFlood___Live_Analysis.html` | Interactive in-browser pipeline with adjustable thresholds, a live replay of the flood, a sensitivity analysis, and auto-generated findings. |

Both files are **self-contained** (no dependencies, no build step, no network requests). Just open them in a browser.

> 💡 **Tip:** To publish with GitHub Pages, rename the main demo to `index.html` (and keep or rename the live page as you like), then enable Pages under *Settings → Pages*.

---

## Quick start

```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
# open either file in any modern browser
open RapidFlood___Sentinel-1_Flood_Detection__Demo_.html      # macOS
xdg-open RapidFlood___Sentinel-1_Flood_Detection__Demo_.html  # Linux
start RapidFlood___Sentinel-1_Flood_Detection__Demo_.html     # Windows
```

---

## Features

### 1. Flood Detection Demo

Click **RUN FLOOD ANALYSIS** to process the demo scene, then explore:

- **Dashboard & Map**: flooded area, water expansion, affected settlements/roads/cropland, an interactive layered map with clickable zone analysis, the Impact Index, and a rainfall context chart.
- **Flood Detection**: pre/post SAR, backscatter change, water masks and final flood extent, a step-by-step pixel funnel, a backscatter histogram with the water threshold, and a "Why was this detected as flooded?" explainer.
- **Flood Replay**: before/after wipe comparison with a play control.
- **Impact Analysis**: per-settlement exposure, per-route road status, flooded cropland, and 8×8 impact-zone scoring.
- **SafeRoute**: per-route flood exposure for six routes (satellite/GIS-based, *not* real-time traffic or official closures).
- **Decision Support**: prioritized response zones and rule-based recommendations.
- **Methodology**: pipeline, datasets, and limitations.

### 2. Live Analysis

Runs the whole pipeline in your browser on the synthetic scene:

- Adjustable **water threshold**, **change threshold**, and **minimum region size**
- A **live run** that replays detected flood pixels (strongest change first), with an event feed and trend chart
- **Threshold sensitivity** charts (flooded area vs. each threshold)
- Automated findings plus settlement and road tables

> The live run is a replay of detected pixels ordered by strength of change. It is **not** a measured time series.

---

## How it works

```
Sentinel-1 SAR (pre + post)
   ↓ Backscatter (dB) + Lee speckle filter
   ↓ Change detection (post − pre)
   ↓ Flood extent (threshold + change + spatial filter)
   ↓ Water expansion
   ├── Settlements ── Roads ── Agriculture
   ↓ Flood Impact Index
   ↓ Interactive map → Decision support → Response priorities
```

**Detection logic**

- **Data:** Sentinel-1 C-band SAR, IW GRD, VV polarization
- **Speckle filter:** Lee filter (ENL 4.4, 5×5)
- **Water threshold:** Otsu, clamped to −22…−15 dB for VV
- **Change criterion:** backscatter drop of at least 3 dB
- **Newly flooded** = post-flood water ∧ not pre-flood water ∧ significant drop
- **Cleanup:** 3×3 opening/closing, then removal of small regions
- Low backscatter alone is **not** classed as flood, so permanent water and dark surfaces (e.g. an airstrip) are excluded

**Impact metrics**

- **Settlements:** counted as affected when at least 5% of the footprint is inundated
- **Roads:** sampled along each route and classified as `SAFE`, `PARTIALLY AFFECTED`, or `FLOOD-AFFECTED`
- **Agriculture:** flooded area within cropland (ESA WorldCover class 40 equivalent)
- **Impact Index (0–100):** weighted combination of flood extent (0.30), settlement exposure (0.25), road exposure (0.20), and agriculture impact (0.25), classified LOW / MEDIUM / HIGH
- **Confidence:** a transparent heuristic (mean of availability, change strength, spatial consistency, and data completeness). It is *not* a calibrated probability.

---

## Demo scenario

| Item | Value |
|------|-------|
| Study area | Demo floodplain, ~353 km² (91.70–91.90°E, 26.10–26.26°N) |
| Pre-flood | 2024-07-04 · Sentinel-1A (simulated) · VV |
| Post-flood | 2024-07-16 · Sentinel-1A (simulated) · VV |
| Newly flooded | ≈ 34.0 km² (≈ 9.6% of study area) |
| Impact Index | ≈ 39 / 100 (MEDIUM) |

---

## Live mode

Live analysis against real Sentinel-1 imagery uses **Google Earth Engine** and runs in a separate **Streamlit** app (with PDF/CSV/GeoJSON export). That app is not part of these HTML demos. <!-- Add a link or setup instructions here if you include the Streamlit app in this repo. -->

---

## Data sources (live mode)

- **Sentinel-1 SAR:** Copernicus / ESA
- **Land cover:** ESA WorldCover, Dynamic World
- **Infrastructure:** OpenStreetMap
- **Rainfall context:** CHIRPS / GPM

---

## Limitations

- SAR can miss flooding under dense vegetation and in built-up areas.
- Wind-roughened water and radar shadow can cause false detections.
- The demo uses **synthetic** data: do not draw conclusions about real places or events from it.
- The Impact Index is an event-impact indicator from measured exposure, **not** a prediction of future flooding.
- Recommendations are rule-based and derived from satellite results. They are **not official emergency instructions**. Always follow guidance from local authorities.

---

## Tech

Plain HTML, CSS, and vanilla JavaScript. Each page is a single file with no frameworks, dependencies, or network calls.

---

## License

Add your license here (e.g. MIT) and include a `LICENSE` file.

## Acknowledgements

Contains modified Copernicus Sentinel data concepts. Sentinel-1 is operated by ESA under the Copernicus Programme.
