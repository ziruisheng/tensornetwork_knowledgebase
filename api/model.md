# Model — System Definition

## Model (General)
The base class. Supports any Hamiltonian in sum-of-product form.

```python
from renormalizer import Model, Op, BasisHalfSpin, BasisSHO

basis = [BasisHalfSpin(0), BasisHalfSpin(1)]
ham_terms = Op("sigma_+ sigma_-", [0, 1]) + Op("sigma_+ sigma_-", [1, 0])
model = Model(basis, ham_terms)
```

### Key Properties
| Property | Description |
|----------|-------------|
| `model.basis` | List of `BasisSet` (one per MPS site) |
| `model.ham_terms` | List of `Op` Hamiltonian terms |
| `model.nsite` | Number of MPS sites (= len(basis)) |
| `model.dofs` | All DoF names in order |
| `model.e_dofs` | Electronic DoF names |
| `model.v_dofs` | Vibrational DoF names |
| `model.n_edofs` / `model.n_vdofs` | Counts |
| `model.qn_size` | Number of quantum number components (0, 1, or 2) |
| `model.dof_to_siteidx[doe_name]` | Map DoF name \rightarrow site index |
| `model.dof_to_basis[doe_name]` | Map DoF name \rightarrow BasisSet |
| `model.dipole` | Dict: `{doe_name: dipole_value}` |
| `model.pbond_list` | Physical bond dimensions = `[b.nbas for b in basis]` |

### `model.get_mpos(key, fun)` — Cached MPO Construction
```python
# First call: constructs and caches
mpos = model.get_mpos("my_ops", lambda m: [Mpo(m, Op("X", d)) for d in m.e_dofs])
# Subsequent calls with same key: returns cached
mpos = model.get_mpos("my_ops", ...)  # fun is ignored
```

## HolsteinModel
Convenience constructor for Holstein (electron-phonon) Hamiltonian:
H = \Sigma J_ij a^\dagger_i a_j + \Sigma \omega_i\lambda b^\dagger_i\lambda b_i\lambda + \Sigma g_i\lambda \omega_i\lambda a^\dagger_i a_i (b^\dagger_i\lambda + b_i\lambda)

```python
from renormalizer import HolsteinModel, Mol, Phonon
from renormalizer.utils import Quantity

ph_list = [Phonon.simplest_phonon(
    omega=Quantity(1500, "cm-1"),
    displacement=Quantity(1.0),
    temperature=Quantity(300, "K")
)]
mol = Mol(Quantity(0, "cm-1"), ph_list)  # 0 = elocalex
mol_list = [mol] * nmols

model = HolsteinModel(
    mol_list=mol_list,
    j_matrix=Quantity(100, "cm-1"),        # constant NN coupling
    scheme=2,                               # 1/2/3: interleaved, 4: grouped electrons
    periodic=False
)
```

### Schemes
| Scheme | Layout |
|--------|--------|
| 2 (default) | [e_{0}, ph_{0}_{0}, ph_{0}_{1}, ..., e_{1}, ph_{1}_{0}, ph_{1}_{1}, ...] |
| 4 | All phonons first, then all electrons in one `BasisMultiElectronVac` |

### Methods
- `model.switch_scheme(4)`: convert to scheme 4.
- `model.gs_zpe`: ground state zero-point energy.
- `model.j_constant`: extract J if uniform.
- `model.mol_num`: number of molecules (= n_edofs).

## SpinBosonModel
H = \epsilon \sigma_z + \Delta \sigma_x + 1/2 \Sigma (p_i^{2} + \omega_i^{2} q_i^{2}) + \sigma_z \Sigma c_i q_i

```python
from renormalizer import SpinBosonModel, Phonon

ph_list = [Phonon.simplest_phonon(Quantity(omega, "cm-1"), Quantity(1.0))]
model = SpinBosonModel(
    epsilon=Quantity(100, "cm-1"),
    delta=Quantity(50, "cm-1"),
    ph_list=ph_list,
    dipole=1.0                               # transition dipole
)
```

## TI1DModel — Translational Invariant 1D
For periodic 1D systems. Automatically replicates basis and Hamiltonian.

```python
from renormalizer import TI1DModel, BasisHalfSpin, Op

unit_basis = [BasisHalfSpin("e")]
local_ham = [Op("Z", "e", 1.0)]
nonlocal_ham = [Op(r"sigma_+ sigma_-", [(0, "e"), (1, "e")], 1.0),
                Op(r"sigma_- sigma_+", [(0, "e"), (1, "e")], 1.0)]

model = TI1DModel(unit_basis, local_ham, nonlocal_ham, ncell=10)
```
- `local_ham_terms`: per-unit-cell terms (automatically summed).
- `nonlocal_ham_terms`: inter-cell terms. DoF names are tuples `(distance, original_doe)`.
- Periodic BC handled automatically: `(i + distance) % ncell`.
- No dipole support.

## Mol and Phonon
```python
from renormalizer.model import Mol, Phonon

# Simplest phonon (harmonic, linear coupling)
ph = Phonon.simplest_phonon(
    omega=Quantity(1500, "cm-1"),      # frequency
    displacement=Quantity(1.0),        # dimensionless displacement
    temperature=Quantity(300, "K"),    # for thermal occupation
    lam=True                           # use reorganization energy for coupling
)

# Explicit phonon (for non-harmonic or arbitrary coupling)
ph = Phonon(
    omega=[Quantity(1500, "cm-1"), Quantity(1500, "cm-1")],
    dis=[Quantity(0), Quantity(1.2)],
    n_phys_dim=8                       # basis truncation (nbas)
)

mol = Mol(
    Quantity(0, "cm-1"),               # elocalex: local excitation energy
    [ph],                               # list of phonons
    dipole=1.0                          # transition dipole
)
```

### Phonon types
- `is_simple`: harmonic, linear coupling (\omega_{0} = \omega_{1}).
- `is_anharmonic`: \omega_{0} \neq \omega_{1}.
- `n_phys_dim`: number of phonon basis states.

## Custom Model with Operator Composition
```python
from renormalizer import Model, Op, OpSum, BasisHalfSpin, BasisSHO

# Heisenberg chain
nspin = 6
basis = [BasisHalfSpin(i) for i in range(nspin)]
ham_terms = []
for i in range(nspin - 1):
    ham_terms.append(Op("sigma_z sigma_z", [i, i+1], 1.0/4))
    ham_terms.append(Op("sigma_+ sigma_-", [i, i+1], 1.0/2))
    ham_terms.append(Op("sigma_- sigma_+", [i, i+1], 1.0/2))

model = Model(basis, ham_terms)
```
