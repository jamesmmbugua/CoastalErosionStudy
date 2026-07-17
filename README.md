# CoastalErosionStudy
Geospatial analysis of Kenyan coastline erosion (2000–2024) using remote sensing and spatial statistics to support shoreline management and blue economy decision-making.
# Kenyan Coast Shoreline Dynamics (2000–2024)

Geospatial analysis of shoreline change along the Kenyan coast over 25 annual Landsat-derived shoreline positions (2000–2024, a 24-year change window), combining DSAS-based change statistics with spatial econometrics and geostatistics to link erosion/accretion patterns to sea-level anomalies (SLA).

**Status:** Private / working repository — supports two manuscripts in preparation (target journals: *Frontiers in Marine Science* and a second outlet TBC).

---

## Overview

This repository holds the data processing, geostatistical modeling, and figure-generation workflow behind a 24-year (2000–2024) shoreline change assessment of ~400 km of the Kenyan coast (Tana–Manda, Kilifi, and Kwale–Mombasa zones). The workflow:

1. Extracts annual shoreline positions from Landsat imagery in Google Earth Engine (GEE).
2. Computes shoreline change statistics (NSM, EPR, WLR) via DSAS.
3. Tests spatial autocorrelation in change rates (Global/Local Moran's I).
4. Models the relationship between sea-level anomaly (SLA) and shoreline change using OLS, a Spatial Lag Model (SLM), and Geographically Weighted Regression (GWR).
5. Validates results against field survey data (site-level erosion/accretion/stability classification).

## Repository structure

> Adjust this section to match your actual folder layout — filled in with a reasonable structure based on the workflow; update paths as needed.

```
.
├── data/
│   ├── raw/                  # Source Landsat scenes / SLA rasters (or links to GEE assets)
│   ├── shorelines/           # 25 annual vector shoreline shapefiles (2000-2024)
│   └── field_survey/         # Site validation data (82 stations)
├── notebooks/
│   └── SeaLevelAnomaly_Kriging_FIXED.ipynb   # SLA kriging + Moran's I / OLS / SLM / GWR pipeline
├── scripts/                  # Standalone processing scripts (GEE extraction, DSAS prep, etc.)
├── figures/                  # Output figures (maps, plots) referenced in the manuscript(s)
├── manuscript/                # Manuscript drafts, tracked-changes versions, figure lists
└── README.md
```

## Data sources

| Dataset | Source | Notes |
|---|---|---|
| Shoreline positions | Landsat Collection 2 Level-2 (surface reflectance), via Google Earth Engine | Annual median composites, 2000–2024 (25 positions) |
| Water/land classification | AWEI (Feyisa et al., 2014) + Otsu (1979) thresholding | Implemented in GEE |
| Shoreline change statistics | DSAS (Digital Shoreline Analysis System) | NSM, EPR, WLR per transect (n = 14,959 transects) |
| Sea-level anomaly (SLA) | [add source, e.g. satellite altimetry product/provider] | Interpolated via ordinary kriging |
| Field validation | Site survey, 82 stations along the coast | Erosion hotspot / stable / accretion / other classification |

## Methods pipeline

- **Shoreline extraction:** Automated water/land classification in GEE (AWEI + Otsu thresholding), raster-to-vector conversion, positional uncertainty accounting for pixel resolution and tidal variability.
- **Change statistics:** DSAS-derived Net Shoreline Movement (NSM), End Point Rate (EPR), and Weighted Linear Regression rate (WLR) per transect.
- **Spatial autocorrelation:** Global Moran's I and Local Indicators of Spatial Association (LISA) on WLR values (`libpysal`, `esda`, `splot`).
- **SLA–shoreline relationship:**
  - OLS (`statsmodels`) — baseline, non-spatial association.
  - Spatial Lag Model (`spreg.ML_Lag`) — accounts for spatial dependence.
  - Geographically Weighted Regression (`mgwr`) — captures spatial non-stationarity in the SLA effect.
- **Interpolation:** Ordinary kriging of SLA (`pykrige`).
- **Field validation:** Cross-check of erosion/accretion/stability classifications against DSAS outputs.

## Key findings (summary)

- Strong positive spatial autocorrelation in shoreline change rates (Moran's I = 0.799, p < 0.001); ~67% of transects are Low–Low (stable), ~27% High–High (erosion/accretion hotspots).
- SLA shows a weak *global* relationship with shoreline change (OLS R² = 0.006; SLM direct effect not significant, p = 0.245).
- GWR reveals substantial spatial non-stationarity: the SLA effect is strong locally but highly variable in sign and magnitude, which is why it washes out in national-scale/global models — sea-level influence is real but only visible at local scale.

## Requirements

Core Python dependencies used in the analysis notebook(s):

```
numpy
pandas
xarray
geopandas
matplotlib
rasterio
scipy
shapely
pykrige
libpysal
esda
splot
statsmodels
spreg
mgwr
```

Install via:

```bash
pip install numpy pandas xarray geopandas matplotlib rasterio scipy shapely pykrige libpysal esda splot statsmodels spreg mgwr
```

(Recommend a dedicated virtual environment, e.g. `conda` or `venv`, given the GDAL/geopandas dependency stack.)

## Usage

1. Set up the environment (see Requirements).
2. Run the shoreline extraction workflow in GEE (see `scripts/` — not included in this repo if run server-side; link to GEE script repository here if applicable).
3. Run DSAS in ArcGIS/ArcPro on the extracted shoreline shapefiles to generate transect-level change statistics.
4. Open `notebooks/SeaLevelAnomaly_Kriging_FIXED.ipynb` to reproduce the SLA kriging, Moran's I/LISA, OLS, SLM, and GWR analysis.
5. Figures used in the manuscript are generated from the notebook outputs and saved to `figures/`.

## Manuscript status

- **Target 1:** *Frontiers in Marine Science* — Research Topic: "Marine environmental data applications for blue economy decision support." Tracked-changes review in progress.
- **Target 2:** TBC.

## Citation

> Citation details to be added upon publication / preprint release.

## Contact

James Mbugua — jamesmmbugua@gmail.com
