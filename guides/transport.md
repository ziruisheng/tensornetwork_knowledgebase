# Transport Calculations (Kubo)

## Overview
Carrier mobility via the Green-Kubo formula:
\mu = (1/k_B T) \int_{0}^\infty dt \langlej(t) j(0)\rangle

where j = -(i/\hbar)[P, H] is the current operator.

## Kubo Transport Workflow

```python
from renormalizer.transport import TransportKubo
from renormalizer.utils import Quantity, EvolveConfig, EvolveMethod, CompressConfig
from renormalizer.mps import ThermalProp, MpDm

# Step 1: Imaginary-time propagation (thermal state)
mpdm = MpDm.max_entangled_ex(model)  # infinite-T initial state

thermal = ThermalProp(
    init_mpdm=mpdm,
    h_mpo_model=model,
    exact=False,
    evolve_config=EvolveConfig(
        method=EvolveMethod.tdvp_mu_cmf,
        adaptive=True,
        guess_dt=1e-3/1j,
        adaptive_rtol=5e-4,
    ),
)

# Propagate to β/2
thermal.evolve(evolve_dt=1e-3/1j, nsteps=500)

# Dump thermal state for reuse
thermal.latest_mps.dump("thermal_beta_half.npz")
```

### Or load a pre-computed thermal state
```python
from renormalizer.mps import load_thermal_state
init_mpdm = load_thermal_state(model, "thermal_beta_half.npz")
```

### Real-time propagation for current correlation
```python
import numpy as np

# Define distance matrix (positions of each site)
# For a 1D chain with spacing R:
n_sites = model.n_edofs
R = 0.1  # in some unit
positions = np.arange(n_sites) * R
distance_matrix = np.abs(positions[:, np.newaxis] - positions[np.newaxis, :])

# Handle periodic boundary condition
# distance_matrix = np.minimum(distance_matrix, n_sites*R - distance_matrix)

transport = TransportKubo(
    model=model,
    temperature=Quantity(300, "K"),
    distance_matrix=distance_matrix,
    evolve_config=EvolveConfig(
        method=EvolveMethod.tdvp_mu_cmf,
        adaptive=True,
        guess_dt=0.1,
        adaptive_rtol=5e-4,
    ),
    compress_config=CompressConfig(threshold=1e-3, max_bonddim=64),
    init_mpdm=init_mpdm,       # or set init_mpdm_path
    init_mpdm_path=None,
)

transport.propagate(evolve_dt=0.1, nsteps=2000)
mobility = transport.calc_mobility()
```

## Distance Matrix Construction

### 1D chain (open BC)
```python
positions = np.arange(n_sites) * R
D = np.abs(positions[:, None] - positions[None, :])
```

### 1D chain (periodic BC)
```python
D_raw = np.abs(positions[:, None] - positions[None, :])
D = np.minimum(D_raw, n_sites * R - D_raw)
```

### The distance matrix replaces P in the polarization operator:
P = e_{0} \Sigma_m R_m a^\dagger_m a_m
\rightarrow j = -ie_{0} \Sigma_{mn} D_{mn} J_{mn} (a^\dagger_m a_n - a^\dagger_n a_m)

## How it works
1. **Imaginary time**: \rho(T) = exp(-\betaH/2) via thermal propagation.
2. **Split state**: Construct two MPS chains from \rho^{1/2}(T).
3. **Real time**: Evolve one chain forward, the other backward.
4. **Correlation**: C(t) = Tr{\rho^{1/2} e^{iHt} j e^{-iHt} j \rho^{1/2}}.
5. **Integration**: \mu = (1/k_B T) \int C(t) dt.

## Key Parameters
| Parameter | Typical | Notes |
|-----------|---------|-------|
| `temperature` | `Quantity(300, "K")` | Must be > 0 |
| `evolve_dt` (real) | 0.05-0.2 | Balance accuracy and total time |
| `nsteps` (real) | 1000-5000 | C(t) must decay to zero |
| `it_evolve_dt` | `1e-3/1j` | Imaginary time step |
| `it_nsteps` | 300-500 | Sufficient for T \geq 200K |
| `max_bonddim` | 64-128 | Transport needs higher M |

## Pitfalls
- The distance matrix is critical for periodic systems. Get it right.
- C(t) must decay to near-zero for accurate integration.
- If C(t) oscillates without decay, extend propagation time.
- Temperature scaling: lower T requires more imaginary-time steps.
