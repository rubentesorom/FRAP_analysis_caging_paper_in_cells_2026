# FRAP diffusion analysis under confinement

Analysis code for FRAP (fluorescence recovery after photobleaching) experiments comparing the **6FP** and **11FP**
constructs under PDMS pillar confinement, imaged on a Zeiss LSM 880 with Airyscan. Intensity traces were measured in Fiji
and exported as one `.xlsx` file per cell.

## What the notebook does

[`FRAP_diffusion_analysis.ipynb`](FRAP_diffusion_analysis.ipynb):

1. **Double normalization.** The bleach-region intensity is background-subtracted, scaled to its pre-bleach mean
   (first 10 frames), and corrected for acquisition photobleaching using the whole-cell signal.
2. **Curve fitting.** Post-bleach frames are fitted with a single exponential, with the bleach time fixed at the first
   post-bleach frame:

   $$I(t) = I_0 + I_1\left(1 - e^{-(t - t_\mathrm{bleach})/\tau}\right)$$

   The fit uses `scipy.optimize.curve_fit` (trust-region reflective, bounded), with data-driven initial guesses.
3. **Derived parameters** for each cell:
   - half-time $t_{1/2} = \tau \ln 2$
   - mobile fraction $I_1 / (I_0 + I_1)$
   - apparent diffusion coefficient $D = 0.22\, r^2 / t_{1/2}$, where $r$ is the bleach-spot radius (1.63 µm here)
4. **Statistics.** Fits with R² ≤ 0.5 are excluded. Conditions are compared with a two-sided Mann–Whitney U test,
   reporting the rank-biserial effect size. D and t½ are not normally distributed (Shapiro–Wilk), so this is the
   main test. Welch's t-test, one-way ANOVA and Levene's test are also reported for reference.

## Outputs

Everything is written to `output/`:

- one PNG per cell showing the data, the fit, the plateau and the bleach time
- `FRAP_fit_results.csv` with all fit parameters, t½, D, the mobile fraction and % recovery
- summary figures as SVG: `Fitting_Calculations.svg`, `R_Squares_Distributions.svg` and `D_vs_Recovery_logscale.svg`

## Usage

```bash
pip install -r requirements.txt
jupyter notebook FRAP_diffusion_analysis.ipynb
```

1. Put the Fiji `.xlsx` exports in `data/`, or point `DATA_FOLDER` at them in the **Settings** cell. After one header
   row, each file must have these columns: `Index, Time (s), Bleach Region, Control Fluorescence, Background, Whole Cell`.
2. The condition is read from the filename, which must contain `6FP` or `11FP`.
3. Set `BLEACH_RADIUS_UM`, `BLEACH_INDEX` and `R2_THRESHOLD` to match your acquisition, then run all cells.

Tested with Python 3.10, numpy 2.2, pandas 2.3, scipy 1.15, scikit-learn 1.7, matplotlib 3.10 and seaborn 0.13.

## Data

Raw data is not included in this repository. Contact the author for access.

## Author

Ruben Tesoro Moreno
