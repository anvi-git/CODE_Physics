# Galaxy Cluster Baryonic Mass Analysis

## Table of Contents

- [Overview](#overview)
- [Project Description](#project-description)
- [How to run the notebook](#how-to-run-the-notebook)
- [Data Sources](#data-sources)
  - [Input Data Structure](#input-data-structure)
- [Configuration Parameters](#configuration-parameters)
  - [Core Settings](#core-settings)
- [Scientific Background](#scientific-background)
  - [Baryonic Mass Calculation](#baryonic-mass-calculation)
  - [Density Profile Model](#density-profile-model)
  - [Gravitational Permittivity Model](#gravitational-permittivity-model)
  - [MCMC Analysis](#mcmc-analysis)
- [Output](#output)
  - [Generated Plots](#generated-plots)
  - [Statistical Output](#statistical-output)
- [Dependencies](#dependencies)
- [Key Physical Constants](#key-physical-constants)
- [References](#references)
- [Notes](#notes)
- [Author](#author)
- [License](#license)

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
├── data_titos/                   # Reference data from Matsakos & Diaferio 2016
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

## How to run the notebook

1. Download `notebook.ipynb` and the folders `in` and `out`, placing in the same general folder.
2. Set your configuration parameters in the **Run Configuration** cell (cell 4)
3. Run all cells sequentially

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
