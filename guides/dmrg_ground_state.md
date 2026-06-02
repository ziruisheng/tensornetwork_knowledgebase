# DMRG Ground State Optimization

## Basic Workflow
```python
from renormalizer import Mps, Mpo, optimize_mps, Model, Op, BasisHalfSpin
from renormalizer.utils import OptimizeConfig

# 1. Build model and MPO
model = Model([BasisHalfSpin(i) for i in range(nsite)], ham_terms)
mpo = Mpo(model)

# 2. Create initial MPS
mps = Mps.random(model, qntot=[0], m_max=20, percent=1.0)

# 3. Configure optimization
mps.optimize_config = OptimizeConfig(
    procedure=[[10, 0.4], [20, 0.2], [30, 0.1], [40, 0], [40, 0]]
)

# 4. Run DMRG
energies, mps = optimize_mps(mps, mpo)
print(f"Ground state energy: {min(energies)}")
```

## Procedure Tuning
The `procedure` is a list of `[bond_dim, noise_percent]` pairs:

| Phase | Example | Purpose |
|-------|---------|---------|
| Initial | `[10, 0.4]` | Low bond dim, high noise to explore Hilbert space |
| Middle | `[20, 0.2]` \rightarrow `[30, 0.1]` | Gradually increase bond dim, reduce noise |
| Final | `[40, 0]` \times 5 | Pure optimization with zero noise |

**Rules of thumb:**
- Start with `bond_dim` \approx 10-20, end with target `bond_dim`.
- Noise helps avoid local minima. Start at 0.3-0.5, decrease to 0.
- Typically 8-15 sweeps total.

## Energy Convergence Checks
```python
energies, mps = optimize_mps(mps, mpo)
final_e = min(energies)

# Check sweep history
for i, e in enumerate(energies):
    print(f"Sweep {i}: E = {e:.8f}")

# Validate with bond dimension scaling
for M in [16, 32, 64, 128]:
    mps = Mps.random(model, qntot, M, percent=1.0)
    mps.optimize_config = OptimizeConfig(procedure=[[M, 0.2], [M, 0]]*5)
    e, mps = optimize_mps(mps, mpo)
    print(f"M={M}: E = {min(e):.10f}")
```
Converged when `|E(M) - E(M/2)| < 1e-5`.

## State-Averaged Excited States
```python
optimize_config = OptimizeConfig(procedure=[[40, 0.2], [40, 0]]*5)
optimize_config.nroots = 3            # optimize 3 lowest states
optimize_config.algo = "davidson"     # davidson (default) | arpack | primme

mps.optimize_config = optimize_config
energies_list, mps = optimize_mps(mps, StackedMpo([mpo] * 3))

# energies_list[i] is the list of 3 energies at sweep i
```

## Algorithm Options
| Parameter | Options | Notes |
|-----------|---------|-------|
| `algo` | `"davidson"` (default), `"arpack"`, `"primme"` | `primme` requires separate installation |
| `method` | `"2site"` (only) | 2-site algorithm for robustness |
| `nroots` | 1 (default) or more | State-averaged when >1 |
| `e_rtol` | `1e-6` | Relative energy convergence |
| `e_atol` | `1e-8` | Absolute energy convergence |
| `inverse` | `1.0` (smallest), `-1.0` (largest) | Eigenvalue target |

## Large Bond Dimensions with BlockEnv
For large systems (M > 64), enable block environment:
```python
optimize_config.use_block_env = True
optimize_config.block_env_threshold = 1e-14
optimize_config.block_env_min_bond_dim = 64
```

## On-the-fly Swapping (OFS)
Reorders DoFs to reduce entanglement during DMRG:
```python
from renormalizer.utils import CompressConfig, CompressCriteria, OFS

compress_config = CompressConfig(
    criteria=CompressCriteria.both,
    max_bonddim=64,
    threshold=1e-3,
    ofs=OFS.ofs_ds          # hybrid entropy/discarded-weight scheme
)
mps.compress_config = compress_config
```
After optimization, `mps.model.basis` will reflect the new ordering; reconstruct `mpo` with the updated model.

## Imaginary-Time Alternative
For Hamiltonians where DMRG struggles:
```python
from renormalizer.utils import EvolveConfig, EvolveMethod

evolve_config = EvolveConfig(EvolveMethod.tdvp_ps, adaptive=True, guess_dt=1e-3/1j)
mps.evolve_config = evolve_config

for istep in range(200):
    mps = mps.evolve(mpo, 0.5/1j)
    energy = mps.expectation(mpo)
    if abs(energy - energy_old) < 1e-5:
        break
    energy_old = energy
```
