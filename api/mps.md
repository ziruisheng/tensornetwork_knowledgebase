# MPS — Matrix Product State

## Construction

### From Hartree product state
```python
from renormalizer import Mps, Model, BasisHalfSpin, BasisSHO

# Each key:value pair sets one site's local state
mps = Mps.hartree_product_state(model, condition={0: [0, 1], "v_3": 2})
```
- The default local state is `0` for any DoF not in `condition`.
- For `BasisMultiElectron`/`BasisMultiElectronVac`: the key can be ANY ONE of the DoF names in that basis; the value specifies the state for ALL DoFs in that basis.
- `qn_idx`: optional, the site index of the quantum number center. Default is the last site.

### From random state
```python
mps = Mps.random(model, qntot, m_max, percent=1.0)
```
- `qntot`: total quantum number (e.g., `[nelec_alpha, nelec_beta]` for Hubbard).
- `m_max`: max bond dimension of the random state.
- `percent`: randomness factor. Higher = more random. 1.0 recommended.

### Ground state (T=0 or T=\infty)
```python
mps = Mps.ground_state(model, max_entangled=False, normalize=True, condition=None)
```
- `max_entangled=False`: vibrational DoFs at ground state, electronic DoFs at ground state.
- `max_entangled=True`: vibrational DoFs at infinite temperature (uniform distribution).

### Load from file
```python
mps = Mps.load(model, "mps.npz")
```
- Save with `mps.dump("mps.npz")`.

## Key Properties

| Property | Description |
|----------|-------------|
| `mps.coeff` | Scalar coefficient (float or complex) |
| `mps.model` | Associated `Model` |
| `mps.nsite` | Number of sites |
| `mps.bond_dims` | List of bond dimensions |
| `mps.pbond_list` | Physical bond dimensions (local basis sizes) |
| `mps.qntot` | Total quantum number |
| `mps.is_left_canonical` | Whether MPS is in left-canonical form |
| `mps.e_occupations` | Electronic occupations `<a^\dagger_i a_i>` for each e-DoF |
| `mps.ph_occupations` | Phonon occupations `<b^\dagger_i b_i>` for each v-DoF |

## Expectation Values

### Single operator
```python
val = mps.expectation(mpo)                    # <ψ|O|ψ>
val = mps.expectation(mpo, bra=mps2)          # <φ|O|ψ>
val = mps.expectation(Op("X", 0))             # Op auto-converted to MPO
val = mps.expectation(OpSum([op1, op2]))      # OpSum auto-converted to MPO
```

### Multiple operators (optimized)
```python
vals = mps.expectations([mpo1, mpo2, mpo3], opt=True)
vals = mps.expectations([Op("X", 0), Op("Z", 0)])
```
- `opt=True` (default): caches intermediates for speed. Slight hash-collision risk; rerun if RuntimeError.

## Time Evolution

```python
mps.evolve_config = EvolveConfig(method=EvolveMethod.tdvp_ps, adaptive=True)
new_mps = mps.evolve(mpo, evolve_dt, normalize=True)
```

**Available methods** (set via `EvolveConfig.method`):

| Method | Description | Best for |
|--------|-------------|----------|
| `prop_and_compress` | Taylor expansion + compression | Small systems, simple Hamiltonians |
| `prop_and_compress_tdrk4` | Classical RK4 for time-dep H | Time-dependent Hamiltonians |
| `prop_and_compress_tdrk` | General RK for time-dep H | Adaptive time-dep evolution |
| `tdvp_ps` | TDVP projector splitting 1-site | Large systems, MPS-efficient |
| `tdvp_ps2` | TDVP projector splitting 2-site | More robust than 1-site |
| `tdvp_vmf` | TDVP variable mean field | Standard choice for TDVP |
| `tdvp_mu_vmf` | TDVP matrix unfolding + VMF | When SVD singular values are small |
| `tdvp_mu_cmf` | TDVP matrix unfolding + CMF | Constant mean field; often fastest |

- `evolve_dt` real: real-time evolution (Schroedinger). `evolve_dt` imaginary: imaginary-time evolution (cooling).
- `normalize="mps_only"` (real time) or `"mps_and_coeff"` (imag time).
- Adaptive step: set `EvolveConfig(adaptive=True, guess_dt=1e-1, adaptive_rtol=5e-4)`.

## Normalization
```python
mps.normalize("mps_only")          # normalize MPS tensors, coeff unchanged
mps.normalize("mps_norm_to_coeff") # MPS norm absorbed into coeff
mps.normalize("mps_and_coeff")     # both normalized
```

## Configuration Objects
```python
mps.optimize_config = OptimizeConfig(procedure=[[40, 0.2], [40, 0]]*5)
mps.evolve_config = EvolveConfig(method=EvolveMethod.tdvp_vmf)
mps.compress_config = CompressConfig(threshold=1e-3, max_bonddim=64)
```

## Common Patterns

### Imaginary-time evolution for ground state
```python
evolve_config = EvolveConfig(EvolveMethod.tdvp_ps, adaptive=True, guess_dt=1e-3/1j)
mps.evolve_config = evolve_config
for istep in range(100):
    mps = mps.evolve(mpo, 0.5/1j)
    energy = mps.expectation(mpo)
    if abs(energy - energy_old) < 1e-5: break
```

### Expand bond dimension for TDVP
```python
mps = mps.expand_bond_dimension(hint_mpo=mpo, coef=1e-10)
```

## Dump/Load
```python
mps.dump("state.npz")                          # save
mps2 = Mps.load(model, "state.npz")            # load
```
