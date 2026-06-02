# Example: FMO Complex

Source: `example/fmo.py`

## Physics
Fenna-Matthews-Olson (FMO) pigment-protein complex.
8 BChl pigments with site energies, electronic couplings, and a dissipative
vibrational bath characterized by a continuous spectral density J(\omega).

## Key features
- Multi-site Holstein model with experimentally determined J matrix.
- Continuous spectral density discretized into 35 modes per site.
- Huang-Rhys factor interpolation from spectral density data.
- Non-uniform molecular arrangement (FMO crystal structure order).
- Charge diffusion dynamics via `ChargeDiffusionDynamics`.

## Spectral Density Processing
```python
# Load SDF: 107×2 matrix [ω (cm⁻¹), J(ω)]
sdf_values = np.array(json.load(open("fmo_sdf.json")))

# Discretize: 35 modes from 2 to 300 cm⁻¹
omegas_cm = np.linspace(2, 300, 35)
omegas_au = omegas_cm * cm2au

# Interpolate and normalize to total HR factor = 0.42
hr_factors = np.interp(omegas_cm, sdf_values[:, 0], sdf_values[:, 1])
hr_factors *= 0.42 / hr_factors.sum()

# λ = S × ω where S is HR factor
lams = hr_factors * omegas_au
phonons = [Phonon.simplest_phonon(Quantity(o), Quantity(l), lam=True)
           for o, l in zip(omegas_au, lams)]
```

## System Parameters
| Site | Energy (cm^{-}^{1}) |
|------|---------------|
| 1 | 310 |
| 2 | 230 |
| 3 | 0 |
| 4 | 180 |
| 5 | 405 |
| 6 | 320 |
| 7 | 270 |
| 8 | 505 |

J matrix: 8\times8 from literature (Moix et al.).

## Molecular Ordering
```python
# FMO physical arrangement reverses some site pairs
mol_arrangement = np.array([7, 5, 3, 1, 2, 4, 6]) - 1
```

## Dynamics
```python
from renormalizer.transport import ChargeDiffusionDynamics, InitElectron

ct = ChargeDiffusionDynamics(
    model,
    evolve_config=EvolveConfig(EvolveMethod.tdvp_ps, guess_dt=160),
    compress_config=CompressConfig(CompressCriteria.fixed, max_bonddim=32),
    init_electron=InitElectron.fc       # Franck-Condon initial excitation
)
```

## Computational Scale
- 8 sites \times 35 phonons = 280 DoFs total.
- With scheme 2, 8 + 8\times35 = 288 MPS sites.
- tdvp_ps with M=32 is feasible on a single node.
- Typical wall time: hours to days depending on evolution time.
