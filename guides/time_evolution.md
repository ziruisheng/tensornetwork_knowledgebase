# Time Evolution Workflow

## Overview
Time evolution in Renormalizer evolves an MPS under a Hamiltonian:
```
|ψ(t+dt)⟩ = exp(-i H dt) |ψ(t)⟩    (real time)
|ψ(τ+dτ)⟩ = exp(-H dτ) |ψ(τ)⟩      (imaginary time)
```

## Real-Time Evolution (Schroedinger)

### Standard TDVP setup
```python
from renormalizer import Mps, Mpo, Model, Op
from renormalizer.utils import EvolveConfig, EvolveMethod, CompressConfig, CompressCriteria

# Build initial state
mps = Mps.hartree_product_state(model, condition={0: [0, 1]})
mpo = Mpo(model)

# Configure evolution
mps.evolve_config = EvolveConfig(
    method=EvolveMethod.tdvp_vmf,
    adaptive=True,
    guess_dt=0.1,
    adaptive_rtol=5e-4,
    ivp_rtol=1e-5,
    ivp_atol=1e-8,
)

# Evolve
results = []
times = []
for istep in range(1000):
    mps = mps.evolve(mpo, 0.1)
    t = (istep + 1) * 0.1
    obs = mps.expectation(Mpo(model, Op("Z", 0)))
    results.append(obs)
    times.append(t)
```

### Choosing the method

| Scenario | Method |
|----------|--------|
| Small system, time-indep H | `prop_and_compress` |
| Time-dependent H | `prop_and_compress_tdrk4` or `prop_and_compress_tdrk` |
| Large MPS, standard | `tdvp_vmf` |
| SVD singular values small | `tdvp_mu_vmf` |
| Max speed, large systems | `tdvp_mu_cmf` |

### Compression during evolution
Set `compress_config` on the MPS before evolving:
```python
mps.compress_config = CompressConfig(
    criteria=CompressCriteria.threshold,
    threshold=1e-3,
    max_bonddim=64
)
```

## Imaginary-Time Evolution (Ground State Search)

```python
evolve_config = EvolveConfig(
    method=EvolveMethod.tdvp_ps,
    adaptive=True,
    guess_dt=1e-3/1j,          # NOTE: imaginary timestep
    adaptive_rtol=5e-4,
)
mps.evolve_config = evolve_config

for istep in range(200):
    mps = mps.evolve(mpo, 0.5/1j)
    energy = mps.expectation(mpo)
    if istep > 0 and abs(energy - energy_old) < 1e-5:
        break
    energy_old = energy
```

## Adaptive vs Fixed Timestep

### Adaptive (recommended)
```python
EvolveConfig(adaptive=True, guess_dt=0.1, adaptive_rtol=5e-4)
```
- Step size adjusts to keep local error below tolerance.
- Slower per step but more reliable.

### Fixed
```python
EvolveConfig(adaptive=False, guess_dt=0.05)
```
- Constant step size. Faster but requires manual tuning.

## Observables During Evolution

```python
# Pre-construct MPOs for efficiency
x_mpo = Mpo(model, Op("X", 0))
occ_mpos = [Mpo(model, Op("n", d)) for d in model.e_dofs]

mps = initial_mps
for istep in range(nsteps):
    mps = mps.evolve(mpo, dt)
    x_val = mps.expectation(x_mpo)
    occ_vals = mps.expectations(occ_mpos)
    logger.info(f"t={(istep+1)*dt:.3f}: X={x_val:.6f}, occ={occ_vals}")

# or use Mps properties
mps.e_occupations    # [<n_0>, <n_1>, ...]
mps.ph_occupations   # [<b†_0 b_0>, ...]
```

## Time-Dependent Hamiltonian
Pass a callable instead of MPO:
```python
def mpo_t(t, mps=None):
    """Return MPO at time t."""
    factor = np.cos(t)  # example: oscillating field
    return Mpo(model, Op("X", 0, factor))

new_mps = mps.evolve(mpo_t, dt)  # works with P&C TD methods
```

## Bond Dimension Growth Control
During evolution, bond dimensions can grow. Control it:
```python
# Restrict compression
mps.compress_config = CompressConfig(
    criteria=CompressCriteria.both,
    max_bonddim=64,
    threshold=1e-3
)

# Or expand before TDVP
mps = mps.expand_bond_dimension(hint_mpo=mpo, coef=1e-10)
```

## Dump/Restart
```python
# Save checkpoint
mps.dump(f"mps_step{istep}.npz")

# Resume
mps = Mps.load(model, "mps_step50.npz")
mps.evolve_config = evolve_config
```
