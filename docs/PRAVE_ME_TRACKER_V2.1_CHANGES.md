# PRAVE / ME Tracker 2.1 - what changed

**Released 2026-11-08** (re-hardening pass). Jim chose to publish before his live test ("won't be able to prove it tonight, but go ahead and publish"), so 2.1 is **proven by the test rig only** until Jim's radio or Gold-tester run. Download name unchanged: [`PRAVE_ME_Tracker_v1.zip`](../downloads/PRAVE_ME_Tracker_v1.zip) (folder `PRAVE_ME_Tracker_v2.1`). The 2.0 zip is in [`archive/`](../archive/).

**Gold decoding unchanged:** checksums, PRAVE field positions, district + 3-digit IDs, RSSI levels and the drawings for a normal run are the same as 2.0 - the rig sends the same radio feed to both and ArcGIS Earth receives the same 82 requests, in the same order, byte for byte.

| # | Before (2.0) | Now (2.1) |
|---|---|---|
| 1 | ArcGIS Earth closed or restarted while tracking: a "not reachable" log line for every GPS and unit report (84 lines in 4 s in the rig), and after the restart the map stayed empty until each unit reported again. | `ARCGIS_EARTH_LOST` once, `ARCGIS_EARTH_BACK` once; radio reports keep being logged; when ArcGIS Earth answers again (tried every 5 s), every known unit and ME are redrawn at once at their last positions. |
| 2 | A wrong or busy COM port: pyserial's raw text (`could not open port 'COM12': FileNotFoundError(...)`) every 5 seconds. | Plain words, said once: `COM12 was not found. Serial ports on this PC: COM3, COM7 ...` or `COM12 is in use by another program (the PRAVE tester, ...)`. Then it keeps trying quietly and reports when it connects. Same for ArcGIS Earth being closed when the tracker starts. |
| 3 | Logs grew forever in one file each (the GPS file about 13 MB a day). | One file per day: `AE_PRAVE_Live_Activity_2026-11-08.log`, `AE_PRAVE_Packet_History_2026-11-08.csv`, `AE_RMC_Position_History_2026-11-08.csv` (same columns). |
| 4 | A unit not heard from for hours looked as live as one heard seconds ago. | After `STALE_AFTER_MINUTES` (5; 0 = off) the label shows its age: `7-101 · 12 min`, `7-101 · 2 h 05 min`, updated once a minute; the next report puts `7-101` back. |

Left as Gold (Jim): the 20-70 N, west-longitude filter.

## Proof here

Rig [`tools/test-rigs/prave_tracker_test_v21.py`](https://github.com/Jim-dc95811/ArcGIS-Earth-Unlocked/blob/main/tools/test-rigs/prave_tracker_test_v21.py) (ArcGIS Earth Unlocked): the real tracker reads a scripted radio feed (Gold sentence formats, units 7-101..7-106) from a virtual serial cable and talks to a stand-in ArcGIS Earth that can be switched off and on. 19 checks ALL PASS: normal run identical to 2.0; ArcGIS Earth gone 4 s then restarted empty -> lost/back once, all 6 units + ME back with no new reports; closed at start -> one ERROR line; COM port missing (Linux and Windows wording) and busy (Windows wording) -> one plain line; port plugged in later -> connects; daily log names; stale labels; self-test PASS.

## To check live (Jim)

With the Gold tester on a virtual COM pair: (a) start the tracker with `INPUT_PORT` set to a port that does not exist - one line listing your ports; (b) close ArcGIS Earth while it runs, reopen it - the six trucks come back within a few seconds; (c) stop the tester for 5+ minutes - labels gain `· 5 min`, `· 6 min`...

## Checksums (SHA-256)

- `downloads/PRAVE_ME_Tracker_v1.zip` (2.1): `b4491914254377fe09b3146323bdf4a905deab967e53a6b37662c8b0a46d54d7`
- `System Files/PRAVE_ME_Tracker.py` (2.1): `e7821c1d4d8bddecf1c38eb919cf5025fa83e6fac15840539cfb99733960d3a5`
