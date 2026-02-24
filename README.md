# Galaxy Cluster Baryonic Mass Analysis

## Overview

This project analyzes galaxy clusters using X-ray observations to study the Baryonic Tully-Fisher Relation (BTFR) and test theories of modified gravity. The analysis applies Markov Chain Monte Carlo (MCMC) methods to fit gravitational permittivity parameters and study the mass-velocity relationship in galaxy clusters.
This project was contained in my Master's Thesis in Physics discussed in April 2024, which you can consult [here](Master_s_Thesis.pdf).

## Project Description

This Python notebook performs an analysis of galaxy clusters, investigating:
- **Baryonic mass calculations** from X-ray luminosity, temperature, and radius measurements
- **MCMC parameter fitting** for gravitational permittivity models
- **Comparison with MOND** (Modified Newtonian Dynamics) predictions
- **Mass profile and density profile analysis** using the beta-model
- **Gravitational potential calculations**
- **Testing different theoretical frameworks** (vacuum permittivity vs. density-dependent permittivity)

## Data Sources

The analysis uses data from:
- **Sohn et al. (2020)**: Galaxy cluster observations including velocity dispersion, temperature, and radius measurements
- Two main datasets available:
  - 99 clusters (full dataset)
  - 75 clusters (purged dataset with lower temperature uncertainty)
For more information on the dataset you can consult my [thesis](Master_s_Thesis.pdf).

### Input Data Structure

```
in/data_clusters_general/
├── data_75/                      # Purged dataset (recommended)
│   ├── list_clusters_75.txt
│   ├── luminosity_75.txt         # X-ray luminosity (10^44 erg/s)
│   ├── radius_75.txt             # R_500 radius (Mpc)
│   ├── redshift_75.txt
│   ├── temperature_75.txt        # Temperature (keV)
│   ├── uncertainty_L_75.txt
│   ├── uncertainty_Temperature_75.txt
│   ├── uncertainty_vel_dispersion_75.txt
│   └── velocity_dispersion_75.txt  # Velocity dispersion (km/s)
├── dati_finali_99/               # Full dataset
│   └── [similar structure]
├── data_titos/                   # Reference data from Titos
└── data_galactic_scales/         # Additional galaxy-scale data
```

## Configuration Parameters

The notebook allows various configuration options set at the beginning:

### Core Settings

1. **N_CLUSTERS**: Choose dataset size
   - `75`: Purged dataset with lower temperature uncertainty (recommended)
   - `99`: Full dataset

2. **TEST**: Controls run mode
   - `0`: Test mode (faster, lower resolution)
     - DPI: 100
     - Walkers: 100
     - Iterations: 10,000
     - Burn-in: 100
   - `1`: Production mode (higher resolution)
     - DPI: 600
     - Walkers: 200
     - Iterations: 80,000
     - Burn-in: 6,000

3. **w**: Baryonic mass fraction
   - `1.0`: Hot gas only
   - `1.3`: Hot gas + galaxies (galaxies ≈ 30% of hot gas mass)

4. **ANALYSIS**: Analysis type
   - `0`: Initial study of ε₀ (vacuum permittivity) and BTFR slope
   - `1`: Complete gravitational permittivity with density dependence

5. **NPARAMS**: Number of fitted parameters
   - When `ANALYSIS = 0`:
     - `1`: Slope only (α fixed)
     - `2`: ε₀ and α
   - When `ANALYSIS = 1`:
     - `2`: ε₀ and critical density
     - `3`: ε₀, critical density, and steepness Q
     - `4`: All parameters including BTFR slope

## Scientific Background

### Baryonic Mass Calculation

The baryonic mass is derived from X-ray observations using:

```
M_bar = C × T^(-0.2) × L^0.5 × R^1.5
```

Where:
- T: Temperature (keV)
- L: X-ray luminosity (10^44 erg/s)
- R: R_500 radius (Mpc)
- C: Constant incorporating cooling function and physical constants

### Density Profile Model

The code uses the Cavaliere-Fusco-Femiano beta-model for isothermal spheres:

```
ρ(r) = ρ₀ / (1 + (r/r_s)²)^(1.5β)
```

Where:
- ρ₀: Central density
- r_s: Scale radius (0.25 Mpc)
- β: Beta parameter derived from temperature and velocity dispersion

### Gravitational Permittivity Model

For `ANALYSIS = 1`, the code tests a density-dependent gravitational permittivity:

```
ε(ρ) = ε₀ + 0.5(1 - ε₀)[tanh(Q × log(ρ/ρ_c)) + 1]
```

Parameters:
- ε₀: Vacuum permittivity
- ρ_c: Critical density
- Q: Steepness of transition

### MCMC Analysis

The code uses the `emcee` package to perform Bayesian parameter estimation:
- **Likelihood function**: Χ² minimization
- **Priors**: Uniform priors with physical bounds
- **Convergence diagnostics**: 
  - Autocorrelation time
  - Gelman-Rubin statistic
  - Chain visualization

## Output

### Generated Plots

The analysis produces multiple visualizations:

1. **Mass Profiles**
   - Cumulative mass vs. radius
   - Mass density profiles

2. **Gravitational Potential**
   - Potential vs. radius
   - Gravitational acceleration profiles
   - Velocity profiles

3. **Mass-Velocity Relations**
   - Baryonic mass vs. circular velocity
   - Comparison with MOND predictions
   - Temperature vs. velocity dispersion

4. **Data Distributions**
   - Histograms of redshift, luminosity, temperature, velocity dispersion, radius, mass, and β-values

5. **MCMC Results**
   - Chain convergence plots
   - Autocorrelation functions
   - Corner plots (posterior distributions)
   - Parameter correlations

6. **Gravitational Permittivity**
   - ε(ρ) as a function of density
   - Comparison with different Q values
   - Comparison with Cesare et al. (2020, 2022)

### Statistical Output

- Chi-squared values and reduced χ²
- P-values for goodness of fit
- Autocorrelation times for each parameter
- Gelman-Rubin convergence statistics
- Parameter values with uncertainties
- Comparison with literature values (σ-level differences)

## Dependencies

```python
numpy
matplotlib
scipy
emcee        # MCMC sampling
pandas
getdist      # Corner plots and posterior analysis
```

## Usage

### Basic Run

1. Open `notebook.ipynb`
2. Set your configuration parameters in the **Run Configuration** cell (cell 4)
3. Run all cells sequentially

### Typical Configuration

For a quick test:
```python
N_CLUSTERS = 75
TEST = 0
w = 1.0
ANALYSIS = 0
NPARAMS = 2
```

For production analysis:
```python
N_CLUSTERS = 75
TEST = 1
w = 1.0
ANALYSIS = 1
NPARAMS = 3
```

## Notebook Walkthrough

The notebook (`notebook.ipynb`) is fully documented with markdown cells between every code cell. Below is a quick-reference guide to each step — what it takes as input, and what it produces as output.

| # | Step | Input | Output |
|---|------|-------|--------|
| 1 | **Library Imports** | — | All packages in memory (`numpy`, `scipy`, `emcee`, `pandas`, `getdist`, …) |
| 2 | **Run Configuration** | User-edited constants (`N_CLUSTERS`, `TEST`, `w`, `ANALYSIS`, `NPARAMS`) | Config DataFrame; scalar run variables; `DPI`, `nwalkers`, `niter`, `nburnin` |
| 3 | **Plot Style and Color Configuration** | `ANALYSIS`, `NPARAMS` | `color_chain`, `color_cornerplot` for MCMC figures |
| 4 | **Physical Constants** | Hard-coded values | `constants_df` DataFrame; scalars `G`, `a₀`, `μ`, `m_p`, `c`, `Λ`, … |
| 5 | **Literature Reference Parameters** | Cesare et al. (2020, 2022) | `params_df`; scalars `Ell_eps0`, `Disk_eps0`, `Ell_rhoc`, `Ell_Q`, `slope_BTFR`, … |
| 6 | **Observational Data Loading** | `.txt` files in `in/data_clusters_general/` | Arrays `L`, `R`, `T`, `v_disp`, `z`, `names` with their uncertainties |
| 7 | **Output Directory Setup** | Timestamp | Timestamped `out/run_*/` folder tree; path variables |
| 8 | **Results and Configuration File Initialization** | Config variables | `config.txt`, `results_recap.txt`; helper `log_result()` |
| 9 | **Baryonic Mass Calculation** | `L`, `T`, `R`, uncertainties, `w` | `Mass_SM` [M☉], `sigma_Mass_SM`, `rel_err_mass` |
| 10 | **Mass Statistics Summary** | `Mass_SM` | Printed min/max/mean/median; section in `results_recap.txt` |
| 11 | **Beta Parameter Calculation** | `v_disp`, `T`, constants | `beta` array [dimensionless]; `mean_beta` |
| 12 | **Central Mass Density (β-Model Normalization)** | `R`, `beta`, `Mass_SM`, `w`, `r_scale` | `rho_0_SI` [g/cm³] — β-model central density per cluster |
| 13 | **Non-Isothermal Model (Currently Disabled)** | *(commented out — not used in the thesis)* | *(disabled — see cell comments for T-gradient extension)* |
| 14 | **Radial Mass, Density, and Potential Profiles** | `rho_0_SI`, `beta`, `r_scale` | 2-D arrays `Massa_r`, `density_r_…`, `phi`; `probed_mass`; `rho_0_astro_units` |
| 15 | **Gravitational Profiles: Potential, Acceleration, and Velocity** | `phi`, `Massa_r`, `r` | Inline plots: φ(r), \|g(r)\|, V_c(r); arrays `m`, `u`, `p`, `gravpot`, `accel`, `velocity`, `g_newton` |
| 16 | **Gravitational Acceleration — Newtonian Comparison** | `accel`, `g_newton`, `r` | Inline plot: effective vs Newtonian acceleration |
| 17 | **Cumulative Mass and Density Profile Plots (Saved)** | `m`, `u`, `p`, `r` | `mass_profile.png`, `mass_density_profile.png` → `plots/profiles/` |
| 18 | **Density at R₅₀₀** | `rho_0_astro_units`, `R`, `beta` | `density_500_SI_units_log_10` [log₁₀ g/cm³] — input to ε(ρ) |
| 19 | **BTFR Input: Circular Velocity and Mass Arrays** | `v_disp`, masses, `ANALYSIS` | `v`, `Mass`, `error_v`, `error_Mass` in log₁₀ space |
| 20 | **Baryonic Tully–Fisher Relation (BTFR) Plot** | `v`, `Mass`, errors | `Mbar_vs_Vc_with_mass_value.png` → `plots/histograms/` |
| 21 | **Temperature vs. Velocity Dispersion Scatter Plot** | `T`, `v_disp`, errors | `T_v_disp.png` → `plots/mass_velocity/` |
| 22 | **Load External Reference Data** | `data_titos/*.txt` | `v_titos`, `m_titos` (log-space) |
| 23 | **Observational Data Histograms** | `z`, `L`, `T`, `v_disp`, `R`, `Mass_SM`, `beta` | 7 PNGs → `plots/histograms/` |
| 24 | **Package Data into MCMC Input Arrays** | `v`, `Mass`, errors | `X`, `Y`, `error`, `data` tuple |
| 25 | **MCMC Hyperparameter Summary** | `nwalkers`, `niter`, `nburnin` | Printed summary |
| 26 | **MOND Normalization Constant** | `G`, `a₀`, `PC` | `Gb`, `a` = log₁₀(G·a₀) |
| 27 | **MCMC: Model Definition, Priors, Likelihood, and Sampling** | `data`, model functions, priors | `sampler`, `samples`, `best_fit_parameters`; chain/autocorr/corner plots → `plots/mcmc/` |
| 28 | **Parameter Comparison with Literature** | Fitted means, reference params | Printed σ-level differences for each parameter |
| 29 | **MCMC Results Logging** | All MCMC outputs | MCMC section appended to `results_recap.txt` |
| 30 | **Gravitational Permittivity Function Plot** | Hard-coded Q, ε₀, ρ_c values | Inline plot of gravitational permittivity function with literature curves |

### Example: Minimal Test Run

Open `notebook.ipynb`, set the following in cell 2, then run all cells in order:

```python
N_CLUSTERS = 75   # purged dataset (recommended)
TEST       = 0    # fast mode: 100 walkers, 10 000 iterations
w          = 1.0  # hot gas only
ANALYSIS   = 0    # fit ε₀ and BTFR slope α
NPARAMS    = 2    # two free parameters
```

**What happens:**
1. Steps 1–8 load libraries, configure the run, set constants, load data, and create output directories.
2. Steps 9–14 compute baryonic masses, β parameters, and cluster density profiles.
3. Steps 15–23 produce all exploratory visualisations (profiles, BTFR scatter, T–σᵥ, histograms).
4. Steps 24–27 package data and run the MCMC sampler with convergence diagnostics.
5. Steps 28–30 compare results with the literature and plot the gravitational permittivity function ε(ρ).

**Outputs produced** (in `out/run_YYYYMMDD_HHMMSS/`):
- `config.txt` — full run configuration
- `results_recap.txt` — mass statistics, β statistics, MCMC results
- `plots/profiles/` — cumulative mass and density profile PNGs
- `plots/mcmc/` — chain traces, autocorrelation, corner plot
- `plots/histograms/` — 7 distribution histograms + BTFR scatter
- `plots/mass_velocity/` — temperature vs. velocity dispersion scatter

---

## Output Structure

Results are organized in the following directory structure:

```
out/
├── run_YYYYMMDD_HHMMSS/          # Each run gets timestamped folder
│   ├── plots/
│   │   ├── profiles/             # Mass and density profiles
│   │   ├── mcmc/                 # MCMC diagnostics
│   │   ├── distributions/        # Histograms
│   │   └── results/              # Final result plots
│   ├── results_recap.txt         # Summary of all results
│   └── config.txt                # Run configuration
└── [previous runs...]
```

## Key Physical Constants

- Gravitational constant: G = 0.00430 pc/(M_☉ (km/s)²)
- MOND acceleration scale: a₀ = 1.2×10⁻¹⁰ m/s²
- Speed of light: c = 299,792.458 km/s
- Proton mass: m_p = 938.27 MeV/c²
- Mean molecular weight: μ = 0.58
- Cosmological parameters: Ω_m = 0.3, Ω_Λ = 0.7, h₀ = 0.7

## References

- **Cesare et al. (2022)**: Gravitational permittivity in elliptical galaxies
- **Cesare et al. (2020)**: Gravitational permittivity in disk galaxies
- **Sohn et al. (2020)**: Galaxy cluster observations
- **McGaugh et al. (2000)**: Baryonic Tully-Fisher Relation
- **Matsakos and Diaferio (2016)**
## Notes

- The analysis assumes hydrostatic equilibrium
- The code includes both isothermal and non-isothermal models
- Temperature gradient effects can be studied with γ parameter
- Results depend on the choice of scale radius (default: 0.25 Mpc)
- The beta-model is fitted to match observed masses at R_500

## Author

Antonio Viscusi 

## License

Please cite appropriately if using this code for research purposes.
