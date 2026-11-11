# P25 GPS Tracker — Operator Guide

Live GPS from BK P25 radios, plotted as named units on your own offline ArcGIS Earth maps. Free and open — no cloud, no vendor server, no BK dongle. The tracker only ever **reads** the radio.

> 🔒 **For IT / security review:** [P25 GPS Tracker — Security Summary (PDF)](P25_GPS_Tracker_Security_Summary.pdf) — a plain-language, cyber-audit-style write-up: exactly what it does, every network call, dependencies (standard library only), file hashes, and how to run it in a managed environment.

![How a field radio's GPS reaches the map](../images/p25_gps_tracker_pictogram.png)

---

## Installing — read this first

1. **Extract the zip** (right-click → **Extract All**), then open the new `P25_GPS_Tracker_v0.4` folder. Double-clicking files inside the zip does not work.
2. **Python 3** is required (the standard [python.org](https://www.python.org/downloads/windows/) installer, which includes Tk for the window). Tick **“Add python.exe to PATH”** during setup. No other libraries are needed.
3. **Dependency — ArcGIS Earth with the Automation API on:** ArcGIS Earth → Settings → Advanced application settings → enable **Automation API** (the tracker uses `http://localhost:8000`).
4. **Dependency — the base radio connected** as the USB **Ethernet/RNDIS Gadget** (it appears at `10.250.46.6`), with Base Station Relay feeding it received GPS.
5. Double-click **`P25 GPS Tracker.bat`** to open the control panel.

---

## How it works

Over its USB link the BK base radio is a small networked device. It relays each received field-radio GPS fix as **two standard NMEA sentences** on a TCP service:

```
$GNGGA,173713.00,3023.62611,N,08156.26128,W,1,12,0.81,19.6,M,...   ← position, fix quality, satellites, altitude
$PP25,9076801,                                                     ← the sending radio's P25 unit ID
```

The tracker subscribes with one short request, reads the pair per fix, and draws **one marker per unit** on your ArcGIS Earth map through the local Automation API. The P25 unit ID is the per-unit key, so each radio keeps its own dot and its own name.

Nothing is decrypted, no password is used, and the radio is never reprogrammed — this is standard interoperability with your own equipment on your own licensed channels.

## Using it

1. With ArcGIS Earth open (API on) and the base radio connected, open the app and click **Start tracking**. The camera flies to the first fix.
2. **Set the retirement time** (top-right, *“Drop idle units after”*) if 2 hours isn't right for the day — a short incident might want 30 minutes; a long operation might want 8 hours or **Never remove**.
3. **Send a GPS from a field radio.** That unit appears in the Units table (status **Live**, green) and a marker drops on the map.
4. **Name a unit:** double-click its row, type the alias (e.g. `9076801 → Jax 24`), Save. The map label updates immediately and the name is remembered.
5. **Stop** ends tracking; markers stay. Closing the window saves your aliases and settings.

## Status and aging

| Status | Meaning |
|---|---|
| **Live** (green) | Reporting normally. |
| **Stale** (amber) | No new fix for a few minutes; the marker turns amber and its label shows the age (`Jax 24 · 12 min`). |
| **Expired** (grey) | Silent past the retirement time; the marker is removed. Set **Never remove** and this won't happen. |

## Aliases and settings

- **Aliases** are saved in `System Files\aliases.json`, keyed by the P25 unit ID, and reloaded every session — name a radio once and it sticks across reconnects and reboots. An un-named unit simply shows its raw ID, so no unit is ever a mystery dot.
- **Retirement time** is saved in `System Files\settings.json`.
- **Finer tuning** in `System Files\p25_core.py` (CONFIG): `RADIO_IP/PORT`, `STALE_AFTER_MINUTES` (when amber starts), `KEEPALIVE_SECONDS`, `WATCHDOG_SECONDS`.
- Every fix is written to a dated CSV in the `Logs` folder (`p25_track_YYYYMMDD.csv`): time, unit, lat, lon, altitude, satellites — ready for after-action or import.

## Hardening built in

- **Keepalive & watchdog** keep the link alive on long runs and reconnect on their own if it goes quiet.
- **Coordinate sanity** rejects empty or impossible fixes, so a garbled packet can never place a false dot.
- **Visible aging** turns a quiet unit amber with its age, so an old position never looks current.
- **Adjustable retirement** (v0.4) — you choose how long an idle unit stays on the map.

## Field-proven

Live-tested on real BK radios: two portables reporting through a base M150 relay appeared as two separate, correctly-separated, named units on offline imagery, with live aging and adjustable retirement — pinpoint accurate.

## One map with ME and the Raveon fleet (trifecta — in testing)

The same P25 feed also folds into the [PRAVE / ME Tracker](PRAVE_ME_TRACKER.md) as a third source, so ME (own position), the Raveon `$PRAVE` fleet, and the P25 radios share one map and one ArcGIS Earth API owner. That integrated build is in bench testing; the standalone P25 GPS Tracker above is the proven, everyday tool and the one most operators will use.
