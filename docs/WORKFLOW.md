# End-to-End: from offline maps to live plotting

This repository is the **plotting** side of the workflow — getting live positions onto your own offline ArcGIS Earth maps. The maps themselves are produced with the companion [TPKX map-production tools](https://github.com/Jim-dc95811/QGIS-Mbtile-to-ArcGIS-Earth-TPKX). The whole chain, start to finish:

## 1. Make the offline maps (companion repo)

Build your offline TPKX imagery once with the production tools:

- **[Master 4 Grid Maker](https://github.com/Jim-dc95811/QGIS-Mbtile-to-ArcGIS-Earth-TPKX/blob/main/MASTER4_GRID_MAKER.md)** — split a 1-degree box into 100 numbered cells with exact EPSG:3857 extents.
- **ArcGIS TPKX Batch Factory** (ArcGIS Pro add-in) — paste up to 10 cell extents and build Satellite Imagery, Road Overlay, or Both at Z16-Z20.
- **[QGIS → TPKX converter](https://github.com/Jim-dc95811/QGIS-Mbtile-to-ArcGIS-Earth-TPKX)** — the MBTiles route to the same offline packages.

## 2. Deploy the maps to field hardware

Verify the completed TPKX files, archive them, and copy them to the field devices. This project has exercised TPKX archives on Windows PCs/laptops, Android phones/tablets, and an Amazon Fire tablet.

ArcGIS Earth Mobile supports local TPK/TPKX files. In project testing, an Android phone opened TPKX files from internal storage, microSD, and exFAT USB storage. Removable-storage behavior can vary by Android file picker/OEM; opening the file through Android's file manager has been more reliable than ArcGIS Earth's in-app picker on some devices.

## 3. Plot live positions on the maps (this repo)

With the offline maps loaded in ArcGIS Earth, start a tracker here and the units draw straight onto your own imagery — no network needed:

- **[P25 GPS Tracker](P25_GPS_TRACKER.md)** — GPS from BK P25 radios, each unit plotted by name. Connect the base radio over USB, start tracking, and name each unit once.
- **[PRAVE / ME Tracker](PRAVE_ME_TRACKER.md)** — reads mixed RMC/PRAVE serial data and uses ArcGIS Earth's local Automation API for drawings and camera control. The operator keeps the map at a useful incident scale while ME remains centered. A **trifecta** build folds in the P25 feed so ME, the Raveon fleet, and P25 share one map.
- **[PRAVE / RMC Field Decoder Tester](PRAVE_RMC_Field_Decoder_Tester_v1_0_Gold_Instructions.pdf)** — a simulated radio stream for trying the PRAVE / ME tracker on the bench.

Each tool is strictly read-and-draw: positions in, markers out through the documented local API. Nothing is written back to the radios (beyond the one P25 subscribe request), and nothing goes to the internet.
