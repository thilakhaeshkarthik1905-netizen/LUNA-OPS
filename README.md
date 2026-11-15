# LUNA-OPS: Lunar Landing Site Navigator

**Explore. Analyse. Compare. Plan for the lunar south pole.**

LUNA-OPS is an interactive lunar landing-site analysis and mission-planning platform that brings sunlight analysis, Earth visibility, terrain screening, communication geometry and multi-site comparison into one environment.

Powered by NASA LRO LOLA elevation models and NASA/JPL SPICE ephemerides, LUNA-OPS places the Sun and Earth against the real lunar terrain horizon to help users understand how location and time affect illumination and direct-to-Earth visibility.

## 🎬 Watch the 1-Minute Demo

**See LUNA-OPS in action:** explore the 3D Moon, inspect terrain, analyse sunlight and Earth visibility, and compare candidate landing sites.

[▶ Watch the LUNA-OPS Demo](https://drive.google.com/file/d/1XlIoJi-T7OZcu5OtBsue38V_ZGQpDjq9/view?usp=sharing)


## 🚀 What LUNA-OPS Offers

- **Sunlight Analysis:** Illumination windows, sunlit duration and periods of darkness.
- **Earth Visibility:** Direct-to-Earth visibility and terrain-horizon obstruction.
- **Operational Overlap:** Periods when sunlight and Earth visibility occur together.
- **3D Lunar Exploration:** Interactive Moon globe and local terrain visualization.
- **Terrain Screening:** Slope, roughness, elevation, uncertainty and depression candidates.
- **Communication Simulation:** User-defined link budgets and hypothetical relay geometry.
- **Multi-Site Comparison:** Compare candidate locations across multiple dates and operational parameters.

## 🛰️ Built with NASA Data

- NASA Lunar Reconnaissance Orbiter (LRO) LOLA elevation models
- NASA/JPL SPICE ephemerides and NAIF kernels
- NASA Planetary Geosciences Data Archive (PGDA)
- Lunar Reconnaissance Orbiter Camera (LROC) Wide Angle Camera data
- JPL Horizons reference data

## ⚙️ How It Works

**NASA terrain and SPICE data → Python scientific engine → FastAPI → React interface**

The Python engine calculates scientific quantities using terrain elevation and planetary geometry. FastAPI serves the results, and the React interface presents them through interactive maps, 3D visualizations and analytical dashboards.

## 🌍 Why It Matters

LUNA-OPS brings several important lunar site-analysis capabilities into one interactive platform. It helps users explore how terrain, sunlight, Earth visibility and communication conditions vary across candidate locations and time windows, supporting more informed preliminary planning for future lunar south-pole missions.

---

**Note:** LUNA-OPS is a research and preliminary planning aid. It is not NASA-approved or certified for landing safety, and it is not integrated with DSN or LunaNet. See the validation and limitations sections below for scientific status and known constraints.

Two preliminary modules sit beside the scientific engine:
- **landing hazard screening** from terrain models;
- **communication simulation**: RF link budgets and a hypothetical relay.



## Screenshots

**South polar map** — server hillshade of the registered NASA LOLA terrain, with the selected site (Site01, 89.463° S, 222.510° E)

![South polar map](docs/screenshots_current/15-south-polar-map-site01.png)

**3D Moon globe** — south polar view with the selected site (globe imagery is for orientation; elevations come only from LOLA terrain)

![3D Moon globe](docs/screenshots_current/16-3d-moon-globe-site01.png)

 Terrain View
![Terrain View](docs/screenshots_current/Terrain%20view.jpeg)

 Sun and Earth Visibility
![Sun and Earth Visibility](docs/screenshots_current/sun%20and%20earth.jpeg)

**Landing hazard map** — preliminary terrain screening at Site01 (5 m terrain): slope layer, depression candidates, landing footprint

![Landing hazard map](docs/screenshots_current/05-landing-hazard-map-site01-1920.png)

**Communication** — mission communication simulation: direct-to-Earth geometry and communication timeline

![Communication simulation](docs/screenshots_current/08-communication-1920.png)

More screenshots: [`docs/screenshots_current/`](docs/screenshots_current/). Feature list: [PROJECT_FEATURES.md](PROJECT_FEATURES.md).

## What the application does

| View | Purpose |
|---|---|
| **Mission** | Overview of the selected site, terrain coverage, system status, current analysis, south polar locator |
| **Site** | Sun and Earth visibility analysis: horizon, sunlight and illumination, Earth-in-view, events and intervals, power indicator, 3D Moon, south polar map, local 3D terrain |
| **Landing** | Preliminary terrain hazard screening:<ul><li>hazard map (elevation, slope, roughness, data coverage, height uncertainty, slope above threshold);</li><li>depression candidates;</li><li>landing-footprint findings.</li></ul> |
| **Communication** | Direct-to-Earth windows from the engine; user-entered RF link budgets; a hypothetical relay orbit; store-and-forward planning |
| **Compare** | Several sites × several date windows in one matrix, with CSV export |
| **Guide** | How to read the results |
| **Data and validation** (`/#data`) | Data products, provenance and the validation record. Reachable by URL; it is no longer in the navigation bar. |

All scientific quantities are computed by the Python engine (`backend/lunaops`). The API serves them unchanged; the browser only displays them.

## Data is not included in this repository

NASA terrain models, SPICE kernels and reference rasters are **not** redistributed. They are large, and the redistribution terms of several sources are unconfirmed (licence decision D5). Download them from their sources as described below; every file is checked by SHA-256 when registered.

| Data | Where it goes | How to get it |
|---|---|---|
| NAIF SPICE kernels | `data/kernels/` | `python -m lunaops.acquire kernels` (step 2) |
| NASA PGDA LOLA terrain (5 m Site01, 20 m, 80 m, 240 m) | `data/dem/` | step 3. `data/dem/registry.json` lists the files and checksums used in development. |
| PGDA 78 Site01 auxiliary maps (LOLA count, NASA slope) | `data/landing/pgda78_site01/` | URLs and SHA-256 in `data/landing/manifest.json` |
| NAIF `gm_de440.tpc` (communication module) | `data/comms/` | URL and SHA-256 in `data/comms/manifest.json` |
| Validation reference rasters (PGDA LPSR, PDS AVGVISIB, LROC WAC, PDS 2017 LDEM) | `data/reference_validation/` | Validation only; see the evidence READMEs. Never commit these. |

## Fastest way to see it running (demo data, any OS)

```bash
python -m pip install -r backend/requirements.txt
python start.py --demo
```

On Windows use `py` if `python` is not found.
- The launcher builds the interface once (Node 20+ needed), prepares **labelled synthetic test data**, starts one server and opens the browser (usually http://localhost:8000).
- Demo results are labelled "not a lunar result" everywhere and never mix with NASA data.

Requirements:
- Python 3.11 to 3.13;
- Node 20 or later;
- about 11 GB of free disk for the core NASA terrain set (not needed for the demo).

## Full setup with NASA data

### 1. Python environment

macOS / Linux:
```bash
cd luna-ops/backend
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Windows (PowerShell):
```powershell
cd luna-ops\backend
py -m venv .venv
.venv\Scripts\Activate.ps1          # if blocked: Set-ExecutionPolicy -Scope Process Bypass
pip install -r requirements.txt
```

All later commands run from `luna-ops/backend` with the environment active.

### 2. Pinned NAIF kernels (production)

```bash
python -m lunaops.acquire kernels
python -m lunaops.acquire verify-kernels
```

`kernels` installs the set pinned in `backend/lunaops/manifests/kernels.production.json` into `data/kernels`:

| Role | File | NAIF-published check |
| --- | --- | --- |
| Leap seconds | `naif0012.tls` | 5,257 bytes, MD5 `25a2fff30b0dedb4d76c06727b1895b1` (NAIF PDS4 label) |
| Text PCK | `pck00011.tpc` | none published; SHA-256 pinned on first download |
| Planetary ephemeris | `de440s.bsp` (DE440, 1849-2150, 31 MB) | MD5 from NAIF's `aa_checksums.txt`, fetched at download time |
| Lunar orientation (DE440) | `moon_pa_de440_200625.bpc` | none published; SHA-256 pinned on first download |
| Lunar frames (DE440) | `moon_de440_250416.tf` (authoritative; IB-2 resolved), fallback `moon_de440_220930.tf` from NAIF's `a_old_versions` | 250416: 19,478 bytes, MD5 `3556dde4bb7ca54a89917c301e650bbf` (NAIF CLPS PDS4 label) |
| DE421 validation PCK/FK (optional, for V-02e) | `moon_pa_de421_1900-2050.bpc`, `moon_080317.tf` | none published; SHA-256 pinned on first download |

Every file is downloaded into `data/kernels/.staging`, checked against the published values above (a mismatch rejects the file and tries the next source or the fallback), and the staged set is test-loaded with SPICE (frames, coverage, radii). Only then does it replace the installed set, with `lock.json` written last; the previous set is kept in `data/kernels/superseded_<UTC>/`. Any failure leaves the installed set untouched. `verify-kernels` re-checks sizes, SHA-256 against the lock and the NAIF-published values, test-loads the set, and says whether V-02e can run.

**Lunar frame kernel (IB-2, resolved 2026-11-09).** `moon_de440_250416.tf` is the authoritative DE440 lunar frame kernel: NAIF's frame-kernel readme (`generic_kernels/fk/satellites/aareadme.txt`, revised 2025-04-16) names it as the frame kernel for `moon_pa_de440_200625.bpc`, and `fk/satellites` holds it while the earlier `moon_de440_220930.tf` has moved to `a_old_versions`. NAIF's PCK readme (revised 2026-10-03) still names 220930 at its former location, which now returns 404. The two kernels define `MOON_PA_DE440` and `MOON_ME_DE440_ME421` identically (only the ID codes of the generic `MOON_PA`/`MOON_ME` aliases differ), and the J2000 → `MOON_ME_DE440_ME421` rotation is bitwise the same with either. 220930 stays a fallback, fetched from `a_old_versions`. `moon_080317.tf` (DE421 frames, for V-02e) is kept; it also defines the generic `MOON_ME`/`MOON_PA` aliases and, loaded last, points them at the DE421 frames (up to 0.86 m from `MOON_ME_DE440_ME421` at the surface). LUNA-OPS therefore names every lunar frame explicitly (`MOON_ME_DE440_ME421`, `MOON_PA_DE440`, and `MOON_ME_DE421` for V-02e), never by an alias, and `tests/test_phase4_frames.py` checks both the names and that DE440 results are bitwise unchanged with or without the DE421 kernels. `lock.json` records which kernel was installed and from where; the load check requires `MOON_ME_DE440_ME421` to be defined. Evidence: `docs/validation_evidence/ib2_2026-11-09/`.

**Production and demo kernels are separate.** `python start.py --demo` and `acquire sandbox-kernels` use `data/kernels_demo`; `python start.py`, `acquire kernels` and the validation report use `data/kernels`. An install from an earlier version that kept the demo set in `data/kernels` is moved to `data/kernels_demo` automatically (nothing is deleted). The engine refuses to load a demo set as production or the reverse. `acquire status` lists both sets.

**If Python cannot reach naif.jpl.nasa.gov** (for example behind a proxy), download the files yourself and install them through the same checks:

```powershell
# Windows PowerShell, from the luna-ops folder: downloads, checks published sizes and MD5s, then installs
powershell -ExecutionPolicy Bypass -File scripts\get_production_kernels.ps1 -Python backend\.venv\Scripts\python.exe
```

or download the eight files from the URLs in the manifest into any folder and run `python -m lunaops.acquire kernels --from-dir <folder>`.

Then run V-02e (DE421/DE440 frame residual, rule: at most 1 m) and the report:

```bash
python -m pytest -s -k V02e tests/test_engine.py
python -m validation.run_validation
```

Production kernels alone do not make results Validated: V-04, V-05, V-09 to V-13, U-04 and U-06 must also pass (see Validation).

### 3. Terrain (core set, about 10.9 GB of height files)

Download from the PGDA product pages, then register each file. All of these are GeoTIFFs in south polar stereographic X/Y metres, MOON_ME frame of DE421, heights in metres above the 1,737.4 km sphere. Registration reads the embedded CRS, size, units and nodata, checks the projection to better than 1 mm, refuses values that look like km or undeclared nodata, and computes SHA-256. Several files may share a resolution: the 5 m mosaic and any number of PGDA 78 site DEMs can be registered together, and finer products are always sampled first.

File names, sizes and links below were read from the product pages on 2026-11-07; PGDA may change them.

| Product id | Product page | Height file | Error map (optional, `--error-map`) |
| --- | --- | --- | --- |
| `pgda81_mosaic` | https://pgda.gsfc.nasa.gov/products/81 | `ldem_87s_5mpp.tif` (87-90 S, 3.3 GB) | none published |
| `pgda78_site` (repeat per site) | https://pgda.gsfc.nasa.gov/products/78 | `<Site>_final_adj_5mpp_surf.tif` | `<Site>_final_adj_5mpp_toterr.tif` (total Z uncertainty, m) |
| `pgda90_10m` | https://pgda.gsfc.nasa.gov/products/90 | `LDEM_83S_10MPP_ADJ.TIF` (4.8 GB) | `LDEM_83S_10MPP_ADJ_ERR.TIF` (7.5 GB) |
| `pgda90_20m` | https://pgda.gsfc.nasa.gov/products/90 | `LDEM_80S_20MPP_ADJ.TIF` (2.6 GB) | `LDEM_80S_20MPP_ADJ_ERR.TIF` (3.7 GB) |
| `pgda90_80m` | https://pgda.gsfc.nasa.gov/products/90 | `LDEM_80S_80MPP_ADJ.tiff` (80-90 S, 189 MB) | `LDEM_80S_80MPP_ADJ_ERR.tiff` (255 MB) |
| `pgda90_240m` | https://pgda.gsfc.nasa.gov/products/90 | `LDEM_60S_240MPP_ADJ.TIF` (210 MB) | `LDEM_60S_240MPP_ADJ_ERR.TIF` (255 MB) |

PGDA 78 lists 27 regions (Site01, Site04, Site06, Site07, Site11, Site20, Site20v2, Site23, Site42, Haworth, Shoemaker, DM1, DM2, SL2, SL3, NPA to NPD, LM1 to LM8); files are under `https://pgda.gsfc.nasa.gov/data/LOLA_5mpp/<Site>/`. Product 90 files are under `https://pgda.gsfc.nasa.gov/data/LOLA_20mpp/`. Do not register the `_EFFRES`, `_HILL`, `LDEC`, `LDSM` or clone files as heights or error maps.

```bash
python -m lunaops.acquire register-dem --product pgda90_80m --file /path/to/LDEM_80S_80MPP_ADJ.tiff --error-map /path/to/LDEM_80S_80MPP_ADJ_ERR.tiff
python -m lunaops.acquire register-dem --product pgda90_20m --file /path/to/LDEM_80S_20MPP_ADJ.TIF --error-map /path/to/LDEM_80S_20MPP_ADJ_ERR.TIF
python -m lunaops.acquire register-dem --product pgda78_site --file /path/to/Site01_final_adj_5mpp_surf.tif --error-map /path/to/Site01_final_adj_5mpp_toterr.tif
python -m lunaops.acquire register-dem --product pgda90_10m --file ... --replace     # swap a single-file product
python -m lunaops.acquire unregister-dem --file <file name>
python -m lunaops.acquire status
python -m lunaops.acquire verify          # re-hash every registered DEM and error map (slow on large files)
```

Error maps are optional; when registered they give per-bin horizon uncertainty (see "Horizon uncertainty" under Remaining limitations for the model and its assumptions). An error map belongs to exactly one DEM: it must share the DEM's projection, pixel size and pixel grid, and it is recorded with the DEM's SHA-256. Each horizon bin reads only the error map of the product that supplied its blocking terrain sample. Replacing or re-registering a DEM drops an error map that was registered for other bytes.

If a registered file is later moved, deleted or changed in size, analyses that would use it are refused with the reason; LUNA-OPS never falls back to another product silently. Restore the file or unregister it.

### 4. Curated sites (optional)

```bash
python -m lunaops.acquire register-catalog --usgs /path/to/usgs_artemis3_grids.zip      # sciencebase.gov item 671a6fa8d34efed5620f89f8
python -m lunaops.acquire register-catalog --pgda /path/to/Site01_ROIs.tgz              # repeat for each site's ROI archive
```

The USGS release (McClernan 2024, "Down Selected Artemis III Candidate Landing Site Navigational Grids", doi:10.5066/P1MEQ6UK) contains Lunar Grid Reference System navigation grids in Artemis Condensed Coordinate format at 1 km, 100 m and 10 m for nine down-selected Artemis III candidate regions, projected in the USGS LTM or LPS projections; the regions were sourced from LROC QuickMap. It is a set of grids, not a set of region outlines: every shapefile becomes its own catalogue entry, and its extent is that grid's bounding box (labelled "not an official NASA boundary"). PGDA 78 publishes ROI shapefiles as one `<Site>_ROIs.tgz` per site (not every site has one); `.tgz`, `.tar.gz`, `.tar`, `.zip` and folders are accepted. Every catalogue point is labelled as the derived centre of a bounding box, not a published landing point. Catalogue IDs include the archive name (for example `pgda_site:Site01_ROIs/<shapefile>`), so identical shapefile names in different archives do not collide.

### 5. Run

```bash
cd ..                      # back to luna-ops
python start.py            # or: cd backend && uvicorn lunaops.api.main:app --port 8000
```

Open the printed address. API documentation is at `/docs` (its page loads assets from a public CDN, so it needs internet). For interface development: `cd frontend && npm ci && npm run dev` (proxies `/api` to port 8000).

The API accepts browser requests only from local origins (`localhost`, `127.0.0.1`, `[::1]`, any port), so other websites cannot use a running LUNA-OPS server through your browser, and the interface route serves only files inside `frontend/dist`.

### 6. Data for the Landing and Communication modules (optional)

```bash
# Communication: NASA/NAIF lunar GM constant (12 KB text kernel); the SHA-256 is checked at use
curl -o data/comms/gm_de440.tpc https://naif.jpl.nasa.gov/pub/naif/generic_kernels/pck/gm_de440.tpc
# Landing: PGDA 78 Site01 LOLA count and NASA slope maps (about 41 MB each), into data/landing/pgda78_site01/
#   URLs and SHA-256 are in data/landing/manifest.json
```

Without these files:
- the communication module reports that its constant is missing;
- landing screening still runs, but cannot separate measured from interpolated pixels and has no NASA slope cross-check.

## Continuous integration

`.github/workflows/ci.yml` runs on every push and pull request:

- **backend**: installs `backend/requirements.txt` on Python 3.12, fetches the sandbox test kernels (cached; only this job saves the cache), runs `python -m pytest` and checks that `data/dem/registry.json` is unchanged. Tests that need the real PGDA files or the production NAIF kernels are skipped there, and every result stays Pending validation;
- **frontend**: `npm ci && npm run build` on Node 22 (TypeScript check and Vite build);
- **demo-smoke**: restores (never saves) the kernel cache, then `python scripts/smoke_demo.py` starts `start.py --demo` with the built interface and checks the interface, coverage, tiles, a one-day analysis (labelled synthetic), the CORS policy and the frontend-route containment over HTTP.

Run the same checks locally from `backend/` with `python -m pytest`, and from the repository root with `python scripts/smoke_demo.py`. The actions are on their Node 24 majors (checkout v5, setup-python v6, setup-node v5, cache v5, upload-artifact v6, download-artifact v7).

**Speed.** A 30-day analysis of the synthetic golden site takes about 9 s on the CI-class machine used for development (15 s before Phase 3 item 8), dominated by the adaptive event detector's exact Earth-limb geometry and SPICE calls. To find hotspots on your own terrain, run `python ../scripts/profile_analysis.py` from `backend/` (cProfile of one analysis; `--lat/--lon/--start/--days`).

## Running the tests

```bash
cd backend && python -m pytest tests            # engine and validation tests (the suite the validation report uses)
python -m pytest lunaops_comms lunaops_landing lunaops_tiles   # preliminary modules (run them separately; see Limitations)
cd ../frontend && npm ci && npm test && npm run build
```

## Known limitations

**Landing screening (preliminary, outside the validated engine):**
- **Obstacle detection is not available.**
  - No imagery-based obstacle product (for example LROC NAC boulder maps) is registered.
  - Elevation models cannot identify boulders: even the finest product (PGDA 78, 5 m pixels) holds a LOLA laser return in only about 9.5 % of its pixels, and the rest are interpolated.
  - Every screening therefore reports **"Not available - manual review required"** for obstacles.
- **Resolution limits.**
  - A footprint must span at least 3 DEM pixels; otherwise its findings read "Requires higher-resolution data".
  - With the default 50 m radius this needs the 5 m or 20 m product. The 80 m and 240 m products cannot resolve it.
  - The footprint card names finer products when one covers the site.
- **Not a safety verdict.** No score, ranking, probability or safe/unsafe verdict is produced. Depression candidates are unconfirmed, and no crater catalogue is integrated.
- **Server memory.** The server keeps only the 3 latest screenings. The interface re-runs a dropped screening with identical inputs, which gives the identical result.

**Communication (preliminary):**
- Link budgets use values the user enters: no hardware values and no defaults.
- The relay is a hypothetical orbit, never an actual NASA spacecraft.
- Geometric visibility is not a radio link.
- There is no DSN or LunaNet schedule or integration.

**Validation:**
- **V-10 failed** (see above).
- V-09/V-10 assume a 0 m observer height (NASA's readme gives none).
- U-04 was tested in headless Chrome only (no screen-reader session), and is not a claim of full WCAG 2.2 AA conformance.
- U-06 is not measured under the current engine.
- The full pytest suite was not completed in Phase 7. In a combined run of every test folder, 12 communication tests fail because `tests/conftest.py` redirects the data folder; they pass when run separately.

**Engine:**
- **Horizon uncertainty** rests on an uncalibrated 250 m correlation length.
- **Near-field horizon** at 0 m observer height depends on the DEM's resolution limit.
- **Power indicator** is dimensionless and Estimated, not measured power.
- Full details: [`docs/TECHNICAL_REFERENCE_2026-11-12.md`](docs/TECHNICAL_REFERENCE_2026-11-12.md), "Remaining limitations".

**Licensing:**
- The licence inventory is complete, but some issues are unresolved (decision D5): 44 of 49 native libraries in the Python wheels ship no licence text, and data redistribution terms are unconfirmed.
- Hence no data is included.
- Full licensing clearance is **not** claimed.

## Repository layout

```
backend/lunaops/          validated scientific engine (SPICE geometry, DEM pyramid, horizon, disk visibility, events, power) and FastAPI app
backend/lunaops_landing/  preliminary landing hazard screening (outside the engine fingerprint)
backend/lunaops_comms/    preliminary communication simulation (outside the engine fingerprint)
backend/lunaops_tiles/    display-only hillshade tile renderer
backend/validation/       validation runner, merge guard, external-reference scripts, reference metadata
backend/tests/            engine test suite (its fingerprint gates validation labels)
frontend/                 React + TypeScript interface (OpenLayers, D3, Three.js); checks in frontend/scripts
data/                     manifests and registries only; downloaded data goes here
docs/                     audit, traceability, technical reference, screenshots and validation evidence
scripts/                  kernel download, diagnostics and smoke test
start.py                  one-command launcher
```

## Documentation

| Document | Contents |
|---|---|
| [PROJECT_FEATURES.md](PROJECT_FEATURES.md) | Feature-by-feature description |
| [PRESENTATION_SOURCE_GUIDE.md](PRESENTATION_SOURCE_GUIDE.md) | Facts, figures and screenshots for a presentation, each with its source |
| [EXPORT_MANIFEST.md](EXPORT_MANIFEST.md) | What this public export contains and excludes, and its security review |
| [docs/TECHNICAL_REFERENCE_2026-11-12.md](docs/TECHNICAL_REFERENCE_2026-11-12.md) | The full earlier README: setup, validation commands, algorithms, limitations (status as of 2026-11-12) |
| [docs/validation_evidence/](docs/validation_evidence/) | Evidence folders with logs, screenshots and SHA256SUMS manifests, one per task |
| [ATTRIBUTION.md](ATTRIBUTION.md) | Data and software attributions |

## Event rules

NASA Space Apps Challenge 2026 starts on November 14, 2026, at 9:00 a.m. local time. Parts of this project were developed before the event start; eligibility is to be confirmed with the Local Lead.

## Licence

Apache-2.0 (see [LICENSE](LICENSE)). Data and software attributions are in [ATTRIBUTION.md](ATTRIBUTION.md). NASA data remain subject to their providers' terms.
