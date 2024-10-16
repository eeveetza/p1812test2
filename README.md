# MATLAB/Octave Implementation of Recommendation ITU-R P.1812

<!---[![DOI](https://zenodo.org/badge/459641442.svg)](https://zenodo.org/badge/latestdoi/459641442)

This code repository contains a MATLAB/Octave software implementation of [Recommendation ITU-R P.1812-6](https://www.itu.int/rec/R-REC-P.1812/en) with a path-specific propagation prediction method for point-to-area terrestrial services in the frequency range 30 MHz to 6000 MHz.  

This version of the code corresponds to the reference version  approved by ITU-R Working Party 3K and published on [ITU-R SG 3 Software, Data, and Validation Web Page](https://www.itu.int/en/ITU-R/study-groups/rsg3/Pages/iono-tropo-spheric.aspx).
-->
This development version of Recommendation ITU-R P.1812-6 implements the troposcatter model from PDR ITU-R P.617 (3M/106 Annex 7).

This is development code that does not necessarily correspond to the reference version approved by ITU-R Working Party 3M and published by Study Group 3 on [ITU-R SG 3 Software, Data, and Validation Web Page](https://www.itu.int/en/ITU-R/study-groups/rsg3/Pages/iono-tropo-spheric.aspx).

The following table describes the structure of the folder `./matlab/` containing the MATLAB/Octave implementation of Recommendation ITU-R P.1812.

| File/Folder               | Description                                                         |
|----------------------------|---------------------------------------------------------------------|
|`tl_p1812.m`                | MATLAB function implementing Recommendation ITU-R P.1812-6          |
|`initiate_digital_maps.m`| MATLAB script that processes the ITU-R maps and generates the necessary functions. It needs to be run prior to using this software implementation. For details, see [Integrating ITU Digital Products](#integrating-itu-digital-products). |
|`validate_p1812.m`          | MATLAB script used to validate the implementation of Recommendation ITU-R P.1812-6 in `tl_p1812.m`             |
|`./validation_profiles/`    | Folder containing a proposed set of terrain profiles and inputs for validation of MATLAB implementation (or any other software implementation) of this Recommendation |
|`./validation_results/`	   | Folder containing all the results written during the transmission loss computations for the set of terrain profiles defined in the folder `./validation_profiles/` |
|`./private/`   |             Folder containing the functions called by `tl_p1812.m` and `validate_p1812.m`|
|`./C3_1_profiles/`   |             Folder containing terrain profiles and measurement files for Terrestrial trans-horizon links in DBSG3 (`CG-3M-2/DBSG3 Repository/Part II Terrestrial trans-horizon.../ III-01)`|
|`read_C3_1_profile.m`   |             MATLAB script for reading the terrain profile|
|`read_data_table_C3_1.m`   |             MATLAB script for reading the measurement data|
|`compute_btl_table_C3_1.m`   |             MATLAB script for computing the basic transmission loss according to ITU-R P.1812-6 and the PDR on troposcatter for those paths from table C3_1 which have complete set of input parameters and computes the prediction errors of the two approaches in an Excel file `Results_Table_C3_1_P1812.xls`|

## Integrating ITU Digital Products

This software uses ITU digital products that are integral part of Recommendations. These products must not be reproduced or distributed without explicit written permission from the ITU.

### Setup Instructions

1. **Download and extract the required maps** to `./private/maps`:

   - From ITU-R P.452-18:
     - `N050.TXT`
     - `DN50.TXT`
   - From ITU-R P.2001-4:
     - `TropoClim.txt`

2. **Run the script** `initiate_digital_maps.m` to generate the necessary functions for retrieving and interpolating data from from the maps.

### Notes

- Ensure all files are placed in `./private/maps` before running the script.
- The script processes the maps, which are critical for the software’s functionality.
- The resulting `*.m` files are placed in the folder `./private`.
<!--
## Function Call

The function `tl_p1812` can be called


1. by invoking only the required input arguments including latitude/longitude of Tx/Rx as Name-Value pairs:
~~~
[Lb,Ep] = tl_p1812(f, p, d, h, R, Ct, zone, htg, hrg, pol,...
    'phi_t', phi_t, 'phi_r', phi_r, 'lam_t', lam_t, 'lam_r', lam_r);
~~~

2. by invoking only the required input arguments including latitude of path centre as a Name-Value pair:
~~~
[Lb,Ep] = tl_p1812(f, p, d, h, R, Ct, zone, htg, hrg, pol, 'phi_path', phi_path);
~~~
3. by invoking optional input arguments as Name-Value pairs in addition to the required input arguments:
~~~
[Lb, Ep] = tl_p1812(f, p, d, h, R, Ct, zone, htg, hrg, pol, 'phi_path', phi_path, 'DN', DN, 'N0', N0);
~~~

## Required input arguments of function `tl_p1812`

| Variable          | Type   | Units | Limits       | Description  |
|-------------------|--------|-------|--------------|--------------|
| `f`               | scalar double | GHz   | 0.03 ≤ `f` ≤ 6 | Frequency   |
| `p         `      | scalar double | %     | 1 ≤ `p` ≤ 50 | Time percentage for which the calculated basic transmission loss is not exceeded |
| `d`               | array double | km    | ~0.25 ≤ `max(d)` ≤ ~3000 | Terrain profile distances (in the ascending order from the transmitter)|
| `h`          | array double | m (asl)   |   | Terrain profile heights |
| `R`           | array double    | m      |              |  Representative clutter heights |
| `Ct`           | array int    |       |  1 - Water/sea, 2 - Open/rural, 3 - Suburban, 4 - Urban/trees/forest, 5 - Dense urban             |  Array of representative clutter types. If empty or all zeros, the default clutter type used is Open/rural |
| `zone`           | array int    |       | 1 - Sea, 3 - Coastal land, 4 - Inland             |  Radio-climatic zone types |
| `htg`           | scalar double    | m      |   1 ≤ `htg`  ≤ 3000          |  Tx antenna height above ground level |
| `hrg`           | scalar double    | m      |   1 ≤ `hrg`  ≤ 3000          |  Rx antenna height above ground level |
| `phi_t`           | scalar double    | deg      |   -80 ≤ `phi_t`  ≤ 80          |  Latitude of Tx station |
| `phi_r`           | scalar double    | deg      |   -80 ≤ `phi_r`  ≤ 80          |  Latitude of Rx station |
| `lam_t`           | scalar double    | deg      |   -180 ≤ `lam_t`  ≤ 180          |  Longitude of Tx station |
| `lam_r`           | scalar double    | deg      |   -180 ≤ `lam_r`  ≤ 180          |  Longitude of Rx station |
| `pol`           | scalar int    |       |   `pol`  = 1, 2          |  Polarization of the signal: 1 - horizontal, 2 - vertical |

Instead of Tx/Rx latitudes and longitudes (`phi_t`, `phi_r`, `lam_t`, `lam_r`), one can include only the latitude of path center `phi_path`.

## Optional input arguments of function `tl_p1812`
| Variable          | Type   | Units | Limits       | Description  |
|-------------------|--------|-------|--------------|--------------|
| `pL`           | scalar double    | %      |   1 ≤ `pL`  ≤ 99          |  Location percentage for which the calculated basic transmission loss is not exceeded. Default is 50%. |
| `sigmaL`           | scalar double    | dB      |             |  location variability standard deviations computed using stdDev.m according to §4.8 and §4.10; the value of 5.5 dB used for planning Broadcasting DTT; Default: 0 dB. |
| `Ptx`           | scalar double    | kW      |   `Ptx` > 0          |  Tx power; Default: 1. |
| `DN`            | scalar double    | N-units/km      | `DN`> 0           | The average radio-refractivity lapse-rate through the lowest 1 km of the atmosphere at the path-center. It can be derived from an appropriate map. Default: 45. |
| `N0`           | scalar double    | N-units      |             | The sea-level surface refractivity at the path-centre. It can be derived from an appropriate map. Default: 325.|
| `dct`           | scalar double    | km      |   `dct` ≥ 0          |  Distance over land from the Tx antenna to the coast along the great-circle interference path. Default: 500 km. Set to zero for a terminal on a ship or sea platform.|
| `dcr`           | scalar double    | km      |   `dcr` ≥ 0          |  Distance over land from the Rx antenna to the coast along the great-circle interference path. Default: 500 km. Set to zero for a terminal on a ship or sea platform.|
| `flag4`           | scalar int    |       |             |  If `flag4`= 1, the alternative method from Attachment 4 to Annex 1 is used to calculate `Lbulls` without using terrain profile. Default: 0. |
| `debug`           | scalar int    |       |             |  If `debug`= 1, the results are written in log files. Default: 0. |
| `fid_log`           | scalar int    |       |     Only used if `debug`= 1        |  File identifier of the log file opened for writing outside the function. If not provided, a default file with a filename containing a timestamp will be created. |


 
## Outputs ##

| Variable   | Type   | Units | Description |
|------------|--------|-------|-------------|
| `Lb`    | double | dB    | Basic transmission loss |
| `Ep`    | double | dB(uV/m)    | Electric field strength |
-->
## Software Versions
The code was tested and runs on:
* MATLAB versions 2022a (Windows OS)

## References

* [Recommendation ITU-R P.1812](https://www.itu.int/rec/R-REC-P.1812/en)

* [ITU-R SG 3 Software, Data, and Validation Web Page](https://www.itu.int/en/ITU-R/study-groups/rsg3/Pages/iono-tropo-spheric.aspx)
