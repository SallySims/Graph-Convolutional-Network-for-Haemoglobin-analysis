# Prioritising areas for low haemoglobin screening among non-pregnant females in Gabon

Code for the analysis *Prioritising Areas for Low Haemoglobin Screening among Non-Pregnant Females in Gabon:
Vulnerability, Expected Cases and Travel Distance*.

The script analyses the 2019-21 Gabon Demographic and Health Survey (DHS). It estimates the vulnerability of
non-pregnant females to low haemoglobin (Hb below 12 g/dL) for areas within regions with a graph convolutional
network (GCN) regression, ranks regions and areas, and reports the expected cases and travel distance along the
priority list.

## What the script does
1. Prepares the sample (non-pregnant females aged 15-49, at least 3 months postpartum, valid Hb) and links GPS coordinates.
2. Descriptive tables: Table 1 (unweighted and weighted) and Supplementary Table S1 (survey-design estimates with 95% CIs).
3. Merges small clusters into 365 units, builds an 8-nearest-neighbour graph and fits the GCN (numpy implementation, no deep-learning framework needed).
4. Converts predicted mean Hb into the probability of low Hb, recalibrates it, and assesses the model (out-of-fold R², calibration, Moran's I, leave-one-region-out).
5. Builds areas (GADM level-2 departments, or nearest GeoNames town), merges areas with fewer than five units, and estimates regional and area vulnerability with 95% CIs and p-values.
6. Ranks areas, and computes expected cases and travel distance along the priority list, with reference routes and a sensitivity check of the road-distance factor.
7. Writes Table 2 (vulnerability) and Table 3 (priority and travel distance), a raster map, and CSV/Excel exports.

## Data (not included)
The DHS data cannot be redistributed. Obtain them yourself and place them in a folder (default `data/`):

| File | Source |
|---|---|
| `GAIR71FL.DTA` (individual recode) | DHS Program, https://dhsprogram.com/data/ (free after registration) |
| `GAGE71FL.shp` and companion files (cluster GPS) | DHS Program, same request |
| `gadm41_GAB_2.shp` or `gadm41_GAB_shp.zip` | GADM 4.1, https://gadm.org (needed for `--area-source gadm`) |

GADM is free for academic use; check its current terms and cite the version used.

## Install and run
```bash
pip install -r requirements.txt
python gabon_lowhb_vulnerability.py --data-dir data --output-dir outputs_lowhb
```

Options (`python gabon_lowhb_vulnerability.py --help`):

| Option | Default | Meaning |
|---|---|---|
| `--area-source` | `gadm` | `gadm` (departments) or `geonames` (nearest listed town; needs `reverse-geocoder`) |
| `--priority-rule` | `area_vulnerability` | areas sorted by vulnerability, or `region_first` (regions in rank order, then areas) |
| `--priority-decimals` | `3` | decimals at which vulnerability is compared for ranking (negative = exact) |
| `--n-boot` | `0` | bootstrap replicates for the regional ranks (40 takes about 10 minutes) |
| `--skip-loro` | off | skip the leave-one-region-out assessment |
| `--gadm` | searched in `--data-dir` | path to the GADM file |

A full run without the bootstrap takes about 5 minutes on one CPU core.

## Main outputs (in `--output-dir`)
`Table1_descriptive.csv`, `Table2_vulnerability_regions_areas.xlsx/.csv`, `Table3_priority_travel_distance.xlsx/.csv`,
`Gabon_LowHb_Vulnerability_Results.xlsx` (all results), `validation.csv`, `gcn_vs_direct_*.csv/.png`,
`map_vulnerability_raster_*.png`, `predicted_vulnerability_raster.tif`.

## Data protection
The outputs include unit-level results derived from DHS cluster coordinates (displaced by the DHS for confidentiality).
Do not commit the output folder or any DHS file to a public repository (see `.gitignore`).

## Reproducibility
Random seeds are fixed (`GCN_SEEDS`, `FOLD_SEED`, `LORO_SEEDS`), so repeated runs give identical results.

## Citation
Cite the article and the DHS Program (Direction Générale de la Statistique and ICF, Gabon DHS 2019-21).

## Licence
Add a licence of your choice (for example MIT) before publishing.
