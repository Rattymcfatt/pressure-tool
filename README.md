# pressure-tool
# Tyre Pressure Coach (Single-File Web App)

A single-HTML kart tyre pressure “coach” that:
- Calculates **next cold pressures** based on target hot pressures and observed/learned hot−cold gain
- Builds **confidence scoring** based on data quality + learning count
- Optional **AI double-check** via Groq (OpenAI-compatible endpoint)
- Optional **cloud sync + login** via Supabase
- Optional **GPS weather** via Open-Meteo

---

## Features

### Pressure Coaching
- Targets: Hot Front / Hot Rear
- Inputs: Cold + Hot pressures (best accuracy), offsets per tyre, session details
- Learns expected gain per **tyre + track + setup key** using EMA (exponential moving average)
- Adds small bounded adjustments based on:
  - grip level, session length, heat cycles
  - ambient temperature & humidity
  - chassis/setup signals (axle, bar, rim, seat, engine, conditions)

### Confidence Meter
- Confidence score (0–100%) based on:
  - whether hot values were entered
  - whether measured gain is plausible
  - number of learned sessions for the exact setup key
  - whether ambient/humidity were provided

### AI Double-Check (Groq)
- Runs after Analyze (when enabled)
- Strict prompt rules:
  - measured gain wins if plausible
  - if hot finished above target: never raise cold
  - small steps recommended
- Parses a strict output format:
  - `VERDICT: CONFIRM|CORRECT`
  - `RECOMMENDED_COLD_F`, `RECOMMENDED_COLD_R`

### Cloud Sync + Login (Supabase)
- Stores/retrieves state in a Supabase table keyed by `user_id`
- Supports email/password + OAuth (Google/Apple if configured)
- Can be configured to **force login every load** (no persisted session)

### GPS Weather (Open-Meteo)
- Uses browser geolocation to fetch:
  - current temperature (°C)
  - current humidity (%)

---

## Project Structure

This project is intentionally simple:

- `index.html` — the entire app (UI + logic)
- `README.md` — this file

No build step required.

---

## Requirements

- A modern browser (Chrome, Safari, Firefox)
- Optional:
  - Supabase project for login/sync
  - Groq API key for AI checks

---

## Quick Start (Local)

1. Save the app as `index.html`
2. Open it in a browser.

**Important:** Some features require HTTPS or a local server:
- Browser geolocation often requires `https://` or `http://localhost`
- OAuth redirects require a real origin (not `file://`)

Recommended local server:

```bash
# Option A: Python
python -m http.server 5173
# then open http://localhost:5173

# Option B: Node
npx serve .
