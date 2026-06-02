# MPO — Matrix Product Operator

## Construction
```python
from renormalizer import Mpo, Model, Op, OpSum

# From Model + Hamiltonian terms (most common)
mpo = Mpo(model, ham_terms)

# From Model + Op/OpSum
mpo = Mpo(model, Op("X", 0))
mpo = Mpo(model, OpSum([Op("X", 0), Op("Z", 1)]))

# With energy offset
mpo = Mpo(model, ham_terms, offset=Quantity(-e_ground))
```

The MPO is constructed automatically from the symbolic operators. The bond dimension
depends on the Hamiltonian complexity — check `mpo.bond_dims` after construction.

## Key Properties
| Property | Description |
|----------|-------------|
| `mpo.bond_dims` | Bond dimensions of the MPO |
| `mpo.pbond_list` | Physical bond dimensions |
| `mpo.model` | Associated Model |
| `mpo.nsite` | Number of sites |

## Special Class Methods

### onsite — single-site operator on all electronic DoFs
```python
# Construct ∑_i μ_i a†_i MPO (for dipole operator)
dipole_mpo = Mpo.onsite(model, "a", dipole=True)
dipole_mpo = Mpo.onsite(model, r"a^\dagger", dipole=True)
```
- `dipole`: if True, multiplies each operator by the dipole from `model.dipole[doe]`.
- `dof_set`: optional, override which DoFs to act on (default: all `e_dofs`).

### ph_onsite — phonon operator on a specific molecular site
```python
# Only for HolsteinModel
ph_mpo = Mpo.ph_onsite(holstein_model, r"b^\dagger b", mol_idx=0, ph_idx=0)
```
- `opera`: one of `"b"`, `r"b^\dagger"`, `r"b^\dagger b"`.

### intersite — electronic \times phonon product operator
```python
# Only for HolsteinModel
mpo = Mpo.intersite(model,
    e_opera={0: "a", 1: r"a^\dagger"},          # electronic operators
    ph_opera={(0, 1): "b"},                     # phonon operators
    scale=Quantity(1.0, "cm-1"))
```
Builds an arbitrary inter-site product operator.

### exact_propagator — exact e^{xH} for Holstein GS/EX space
```python
# Only for HolsteinModel
prop_mpo = Mpo.exact_propagator(model, x, space="GS", shift=0.0)
```
- `x`: real or complex propagation parameter.
- `space`: `"GS"` (zero exciton) or `"EX"` (one exciton).
- `shift`: constant shift H \rightarrow H+shift before exponentiation.

## Operations

### contract — apply MPO to MPS (or MpDm, or MPO)
```python
new_mps = mpo.contract(mps)        # H|ψ⟩
new_mpo = mpo.contract(mpo2)       # MPO × MPO
```
Returns a compressed result. Respects `compress_config` on the MPS.

### apply — same as contract but canonicalises afterward
```python
new_mps = mpo.apply(mps, canonicalise=True)
```

### scale — multiply by scalar
```python
scaled_mpo = mpo.scale(2.0)
mpo.scale(2.0, inplace=True)
```

## StackedMpo
For state-averaged DMRG, multiple MPOs stacked together:
```python
from renormalizer.mps import StackedMpo
stacked = StackedMpo([mpo1, mpo2, mpo3])
```

## Common Patterns
Check the bond dimension after construction — it determines computational cost:
```python
mpo = Mpo(model, ham_terms)
logger.info(f"mpo bond dims: {mpo.bond_dims}")
```
