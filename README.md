# Lagos coastal subsidence from Sentinel-1 InSAR: ISCE2 multi-burst processing scripts

**Author:** Abayomi Glory Kehinde (INRS, 2026)

This package contains the Jupyter notebooks used to produce the results of the report
*Lagos Nigeria Coastal Subsidence from Sentinel-1 InSAR: Reprocessing with ISCE2 Multi-Burst Interferograms*.

The workflow:

1. checks the earlier HyP3 INSAR_GAMMA results for phase-unwrapping errors;
2. orders new Sentinel-1 interferograms from ASF HyP3 as ISCE2 multi-burst products;
3. computes line-of-sight (LOS) velocities with MintPy, with unwrapping-error correction;
4. decomposes the ascending and descending velocities into vertical and east-west velocity;
5. compares the results with Ohenhen and Shirzaei (2022).

The time series were processed twice, with two different reference points:

| Run | Reference point | Coordinates |
|---|---|---|
| Run 1 | UNILAG (University of Lagos, ULAG GNSS station) | 6.517° N, 3.398° E |
| Run 2 | Reference point of Ohenhen and Shirzaei (2022) | 6.87027° N, 3.190534° E |

## Study set-up

| Item | Value |
|---|---|
| Satellite and mode | Sentinel-1, Interferometric Wide swath (IW), VV polarisation |
| Tracks | Ascending relative orbit 1; descending relative orbit 95 |
| Period | 1 March 2018 - 31 October 2021 (same as Ohenhen and Shirzaei, 2022) |
| Burst search box | 3.00-3.80° E, 6.40-6.90° N |
| Bursts | Ascending: 9 (IW2 bursts 32-36, IW3 bursts 32-35); descending: 8 (IW2 and IW3 bursts 202942-202945) |
| Pair limits | Temporal baseline ≤ 60 days; perpendicular baseline ≤ 250 m |
| Network | Ascending: 108 dates, 509 interferograms; descending: 107 dates, 501 interferograms |
| HyP3 product | INSAR_ISCE_MULTI_BURST, 20 × 4 looks (80 m pixels), water mask on |
| Software | ASF OpenSARlab, kernel `opensarlab_mintpy_recipe_book`, MintPy 1.6.4 |
| Coordinate system | WGS 84 / UTM zone 31N (EPSG:32631) |

## How to use this repository

All notebooks are in **`Lagos_ISCE2_MultiBurst_Scripts.zip`**. Download the zip (click the file name, then the download button) and unzip it. Inside, the notebooks are arranged in the numbered folders described below.

## Contents and run order

The folders are numbered in the order the notebooks were used.

| Folder | Notebook | What it does |
|---|---|---|
| `00_GAMMA_unwrap_error_check` | `Lagos_GAMMA_UnwrapError_Check.ipynb` | Counts, for every pixel of the earlier INSAR_GAMMA stacks, the interferogram triplets that do not close (phase-unwrapping errors), maps the percentage for both tracks and samples it at the hotspot points P1-P11 and UNILAG. This check justified the reprocessing. |
| `01_OpenSARlab_setup` | `1_Software_Environment.ipynb` | Installs the `opensarlab_mintpy_recipe_book` software environment (run once). |
| | `2_CDS_Access.ipynb` | Stores the Copernicus Climate Data Store access token needed for the ERA5 tropospheric correction (run once). |
| `02_order_multiburst_HyP3` | `Lagos_MultiBurst_Order.ipynb` | Finds the Sentinel-1 bursts over the study area, checks that they form a valid multi-burst set, builds the pair network, shows the credit cost (about 5,050 credits in total), submits the INSAR_ISCE_MULTI_BURST jobs `Lagos_MB_asc` and `Lagos_MB_desc`, and checks their progress. |
| `03_download_HyP3_data` | `3_Access_HyP3_Data.ipynb` | Downloads the finished products of each HyP3 project to OpenSARlab, checks the projection and subsets each stack to the area covered by all its products. Run once per track. |
| `04_load_data_MintPy` | `a_Load_Data.ipynb` | Writes the MintPy configuration file with the paths of all HyP3 files, including the connected-components files, and loads the interferogram stack (`ifgramStack.h5`) and geometry (`geometryGeo.h5`). Run once per track. |
| `05_configure_MintPy` | `b_Update_Configuration.ipynb` | Sets the MintPy options (see *Key settings*). One cell was added to write the unwrapping-error correction settings, which are not among the notebook's quick options. |
| `06_MintPy_time_series` | `c_MintPy_Time_Series.ipynb` | Runs MintPy step by step: network modification, reference point, unwrapping-error correction, network inversion, ERA5 tropospheric correction, linear deramping, DEM-error correction and velocity. Run once per track and per run. |
| `07_rerun_with_Ohenhen_reference_point` | `Lagos_MB_OhenhenRef_Reprocessing_Helper.ipynb` | Prepares Run 2. It backs up the Run 1 results, checks that MintPy will accept the new reference point, writes the new reference point and the descending exclusions into both configuration files, and deletes the old connected-components mask. After the rerun, it checks the result. |
| `08_decomposition_vertical_eastwest` | `MB_ISCE2_ASC_DESC_Vertical_Decomposition.ipynb` | Run 1 (UNILAG): masks each track with its temporal-coherence mask, crops both tracks to their common area, runs MintPy `asc_desc2horz_vert.py`, writes GeoTIFFs in mm/yr and makes time-series charts at the hotspot points. |
| | `MB_ISCE2_ASC_DESC_Vertical_Decomposition_OhenhenRef.ipynb` | The same for Run 2, in a separate output folder. |
| `09_save_results_GeoTIFF` | `Lagos_UNILAGRef_Export_All_Results.ipynb` | Saves all Run 1 deformation and quality layers as GeoTIFF (velocities, total displacement, velocity uncertainty, temporal and spatial coherence, unwrapping-error count, mask). It reads the Run 1 backup folders and checks that they are referenced to UNILAG. |
| | `Lagos_OhenhenRef_Export_All_Results.ipynb` | The same for Run 2. |
| `10_comparison_with_Ohenhen_Shirzaei_2022` | `Lagos_Report_Maps_vs_Ohenhen.ipynb` | Report maps for Run 2: both studies side by side, common pixels only, and the difference map. |
| | `Lagos_OhenhenRef_Comparison_Figures.ipynb` | Run 2: processing-facts table, agreement statistics, scatter plots, east-west maps, point and LGA tables. |
| | `Lagos_UNILAGRef_Comparison_Figures.ipynb` | The same for Run 1, with Ohenhen and Shirzaei's values shifted to UNILAG. |
| | `Lagos_Compare_UNILAGrun_vs_OhenhenRefrun.ipynb` | Compares Run 1 and Run 2 pixel by pixel (difference maps, scatter plots and tables). |

### Step-by-step order

1. `00`: check the INSAR_GAMMA results for unwrapping errors.
2. `01`: set up the OpenSARlab environment and ERA5 access, once.
3. `02`: order the multi-burst interferograms. Leave `SUBMIT = False` to see the cost first, then set `SUBMIT = True`.
4. `03` → `04` → `05` → `06`: download, load, configure and process each track (Run 1, reference UNILAG).
5. `08`, first notebook: decomposition of Run 1.
6. `07`: prepare Run 2, then run `06` again for each track, starting from the network-modification step.
7. `08`, second notebook: decomposition of Run 2.
8. `09`: save all layers of both runs as GeoTIFF.
9. `10`: comparisons with Ohenhen and Shirzaei (2022), and between the runs.

Inside each notebook, run the cells from top to bottom and edit only the lines marked `# EDIT`, such as folder paths.

## Key settings

| Setting | Value | MintPy option |
|---|---|---|
| Unwrapping-error correction | Bridging followed by phase closure | `mintpy.unwrapError.method = bridging+phase_closure` |
| Minimum connected-component area | 100 pixels (0.64 km²) | `mintpy.unwrapError.connCompMinArea = 100` |
| Reference point | 6.517, 3.398 (Run 1); 6.87027, 3.190534 (Run 2) | `mintpy.reference.lalo` |
| Reference date | None (time series relative to the first date) | `mintpy.reference.date = no` |
| Tropospheric correction | ERA5 (PyAPS) | `mintpy.troposphericDelay.method = pyaps` |
| Phase deramping | Linear | `mintpy.deramp = linear` |
| Parallel processing | Local, 4 workers | `mintpy.compute.cluster = local`, `mintpy.compute.numWorker = 4` |
| Excluded interferograms (descending) | Run 1: `20201109_20201121` (missing perpendicular baseline). Run 2: the same plus `20180921_20181027`, `20181015_20181214`, `20181027_20181214` (not reliably unwrapped at the Run 2 reference point) | `mintpy.network.excludeDate12` |
| Mask threshold | Temporal coherence 0.7 (default) | `mintpy.networkInversion.minTempCoh` |

Interferograms used: ascending 509 of 509 in both runs; descending 500 of 501 in Run 1 and 497 of 501 in Run 2.

## Data and accounts needed

| Item | Source |
|---|---|
| NASA Earthdata account (HyP3 ordering and download) | https://urs.earthdata.nasa.gov |
| Copernicus Climate Data Store account and token (ERA5) | https://cds.climate.copernicus.eu |
| Ohenhen and Shirzaei (2022) point data, `InSAR_data.csv` | https://doi.org/10.7294/19738957 |
| Lagos LGA boundaries | GRID3 Nigeria, https://open.africa/organization/grid-3-nigeria |

Credentials are typed in when a notebook asks for them; none are stored in the notebooks. HyP3 products are kept for a limited time after processing (see each job's `expiration_time`), so a repeat of the work needs the interferograms to be ordered again with `02`.

## Folders used on OpenSARlab (`/home/jovyan`)

| Folder | Content |
|---|---|
| `Lagos_MB_asc`, `Lagos_MB_desc` | Downloaded HyP3 products of each track |
| `Lagos_MB_asc/MintPy`, `Lagos_MB_desc/MintPy` | MintPy results (Run 2 after the rerun) |
| `Lagos_MB_asc/MintPy_UNILAG_backup`, `Lagos_MB_desc/MintPy_UNILAG_backup` | MintPy results of Run 1, copied before the rerun |
| `Lagos_MB_Vertical_Subsidence` | Decomposition and GeoTIFFs of Run 1 |
| `Lagos_MB_Vertical_Subsidence_OhenhenRef` | Decomposition and GeoTIFFs of Run 2 |
| `Lagos_Vertical_Subsidence` | Earlier INSAR_GAMMA results (used by `00`) |
| `Lagos_Report_Maps_OhenhenRef`, `Lagos_Report_Maps_UNILAGRef`, `Lagos_Report_Maps_RunComparison` | Comparison figures and tables |

## Notes on the saved outputs

* `03`, `04`, `05` and `06` keep the outputs of their **last run** on OpenSARlab, as a record of the processing:
  * `03`: the descending download (501 products);
  * `04`: the ascending loading;
  * `05`: a session on the descending configuration;
  * `06`: the ascending track of Run 2 (12 October 2026).

  Because each notebook was run several times, these outputs do not show every run. The final settings of both runs are those listed under *Key settings*.
* In `06`, the saved figures were converted to JPEG at a reduced size, to keep the notebook under GitHub's 25 MB browser-upload limit. The code and the text outputs are unchanged.
* The other notebooks are provided without outputs.
* The NASA Earthdata username in the saved output of `03` has been replaced by a placeholder.

## Known issues and how they were handled

* **INSAR_GAMMA products cannot be corrected for unwrapping errors.** They have no connected-components files, so MintPy can count the errors (`00`) but not correct them. This is why the data were reprocessed as ISCE2 multi-burst products.
* **Name of the corrected dataset.** With `bridging+phase_closure`, MintPy writes the corrected phase as `unwrapPhase_bridging_phaseClosure`, not `unwrapPhase_phaseClosure`. A message saying that `unwrapPhase_phaseClosure` was not found is therefore not an error. The added cell in `06` that lists the `unwrapPhase` datasets, and the inversion output ("use dataset unwrapPhase_bridging_phaseClosure"), confirm that the correction was applied.
* **Run 2 reference point rejected in the descending track.** MintPy accepts only a reference pixel that is reliably unwrapped in every interferogram used. Three descending interferograms failed at the Run 2 reference point and were excluded. The old `maskConnComp.h5` had to be deleted so that MintPy rebuilt it without them.
* **Order of `05` and `07`.** Running `b_Update_Configuration.ipynb` after `07` rewrites the configuration from its quick options and removes the descending exclusions. Use `05` before `07`, never after.
* **Reference point stored in metres.** For UTM files, MintPy stores `REF_LAT`/`REF_LON` in UTM metres. The decomposition notebooks and `Lagos_UNILAGRef_Export_All_Results.ipynb` convert them to degrees before checking the reference point.
* **"PROJ … failed" when writing GeoTIFFs.** The notebooks point PROJ to the database supplied with `pyproj`, with a built-in definition of UTM zone 31N as a fallback.

## Credits and licences

* The notebooks in `01`, `03`, `04`, `05` and `06` are from the **OpenSARlab MintPy Recipe Book** by the Alaska Satellite Facility (BSD-3-Clause licence): https://github.com/ASFOpenSARlab/opensarlab_MintPy_Recipe_Book. They are included as used, with two cells added: in `05`, the cell that writes the unwrapping-error settings, and in `06`, a cell that lists the corrected `unwrapPhase` datasets.
* The other notebooks were written for this study.

## References

* Alaska Satellite Facility. *HyP3 Sentinel-1 Burst InSAR product guide*. https://hyp3-docs.asf.alaska.edu/guides/burst_insar_product_guide/
* MintPy. *smallbaselineApp.cfg* (default template and option descriptions). https://github.com/insarlab/MintPy/blob/main/src/mintpy/defaults/smallbaselineApp.cfg
* Ohenhen, L. O., & Shirzaei, M. (2022). Land subsidence hazard and building collapse risk in the coastal city of Lagos, West Africa. *Earth's Future, 10*(12), e2022EF003219. https://doi.org/10.1029/2022EF003219
* Yunjun, Z., Fattahi, H., & Amelung, F. (2019). Small baseline InSAR time series analysis: Unwrapping error correction and noise reduction. *Computers & Geosciences, 133*, 104331. https://doi.org/10.1016/j.cageo.2019.104331
