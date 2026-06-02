# Configuration Objects

## CompressConfig
Controls MPS/MPO compression (truncation) during DMRG sweeps and time evolution.

```python
from renormalizer.utils import CompressConfig, CompressCriteria

compress_config = CompressConfig(
    criteria=CompressCriteria.threshold,   # threshold | fixed | both
    threshold=1e-3,                        # singular value cutoff (threshold mode)
    max_bonddim=64,                        # max bond dimension (fixed/both mode)
    vmethod="2site",                       # "1site" or "2site"
    vprocedure=[[64, 0.5], [64, 0.3], [64, 0.1]] + [[64, 0]]*10,
    vrtol=1e-5,                            # convergence tolerance
    dump_matrix_size=np.inf,               # bytes threshold for disk dumping
    ofs=None,                              # On-the-fly swapping (OFS-S/OFS-D/S)
)
```

### Criteria Modes
| Mode | Behavior |
|------|----------|
| `threshold` | Truncate singular values below `threshold` |
| `fixed` | Keep at most `max_bonddim` singular values |
| `both` | Apply both; use the smaller result |

### Variational Compression
- `vmethod="2site"`: more robust, recommended for initial sweeps.
- `vmethod="1site"`: faster, can get stuck in local minima.
- `vprocedure`: list of `[max_bonddim, percent]` per sweep. `percent` controls how much of each symmetry block to mix in (avoids local minima).

### On-the-fly Swapping (OFS)
Reorders DoFs during compression to minimize entanglement:
- `OFS.ofs_s`: based on entanglement entropy.
- `OFS.ofs_d`: based on discarded weight.
- `OFS.ofs_ds`: hybrid scheme.
- Set `ofs_swap_jw=True` only for ab initio Hamiltonian with `h_qc.qc_model`.

## EvolveConfig
Controls time evolution.

```python
from renormalizer.utils import EvolveConfig, EvolveMethod

evolve_config = EvolveConfig(
    method=EvolveMethod.tdvp_vmf,   # evolution method
    adaptive=True,                   # adaptive time-stepping
    guess_dt=1e-1,                   # initial timestep
    adaptive_rtol=5e-4,              # adaptive tolerance
    rk_solver="C_RK4",               # RK solver for P&C methods
    taylor_order=4,                  # Taylor expansion order
    reg_epsilon=1e-10,               # TDVP-MU regularization
    ivp_rtol=1e-5,                   # IVP relative tolerance (TDVP)
    ivp_atol=1e-8,                   # IVP absolute tolerance (TDVP)
    ivp_solver="krylov",             # "krylov" or "RK45"
    force_ovlp=True,                 # compensate non-orthogonality
    vmf_auto_switch=True,            # auto-switch VMF/MU-VMF
)
```

### Method Selection Guide

**Propagation & Compression (P&C):**
- Best for: small systems, simple Hamiltonians.
- Method: `prop_and_compress` (Taylor), `prop_and_compress_tdrk4` (RK4, time-dep), `prop_and_compress_tdrk` (general RK, time-dep).
- Tuning: increase `taylor_order` for higher accuracy.

**TDVP family (recommended for most cases):**
- `tdvp_vmf`: variable mean field. Default choice. Works well in most cases.
- `tdvp_mu_vmf`: matrix unfolding + VMF. Use when SVD singular values become very small.
- `tdvp_mu_cmf`: matrix unfolding + constant mean field. Often fastest for large systems.
- `tdvp_ps` / `tdvp_ps2`: projector splitting (1-site / 2-site). Legacy; VMF variants preferred.
- With `vmf_auto_switch=True`, switches between `tdvp_vmf` and `tdvp_mu_vmf` automatically.

### Real vs Imaginary Time
- `evolve_dt` real \rightarrow Schroedinger evolution `exp(-iH dt)`.
- `evolve_dt` imaginary \rightarrow cooling / ground state search `exp(-H d\tau)`.
- For imaginary time, use `guess_dt=1e-3/1j`.

### Adaptive Stepping
```python
EvolveConfig(adaptive=True, guess_dt=1e-1, adaptive_rtol=5e-4)
```
- The step size adjusts automatically to keep the local error below `adaptive_rtol`.
- For TDVP: uses scipy's RK45 integration, controlled by `ivp_rtol`/`ivp_atol`.

## OptimizeConfig
Controls DMRG ground state optimization.

```python
from renormalizer.utils import OptimizeConfig

optimize_config = OptimizeConfig(
    procedure=[[10, 0.4], [20, 0.2], [30, 0.1], [40, 0], [40, 0]]
)
```
- `procedure`: list of `[max_bonddim, percent]` for each sweep.
- `method`: always `"2site"`.
- `algo`: `"davidson"` (default), `"arpack"`, or `"primme"` (if installed).
- `nroots`: number of states for state-averaged optimization.
- `e_rtol=1e-6`, `e_atol=1e-8`: energy convergence tolerances.
- `use_block_env=True`: experimental sparse environment contraction for large bond dimensions.

### State-Averaged Excited States
```python
optimize_config = OptimizeConfig(procedure=[[40, 0.2], [40, 0]]*5)
optimize_config.nroots = 3   # optimize 3 lowest states
```
