# Example: Holstein Model

Source: `example/dynamics.py`, `example/fmo.py`, `example/ssh.py`

## Physics
Holstein Hamiltonian (standard):
H = \Sigma J_ij a^\dagger_i a_j + \Sigma \omega_i\lambda b^\dagger_i\lambda b_i\lambda + \Sigma g_i\lambda \omega_i\lambda a^\dagger_i a_i (b^\dagger_i\lambda + b_i\lambda)

Applications: organic semiconductor charge transport, molecular aggregate spectroscopy.

## Pattern 1: From YAML parameters (dynamics/transport)

```python
from renormalizer.model import load_from_dict
from renormalizer.transport import ChargeDiffusionDynamics
from renormalizer.utils import EvolveConfig, EvolveMethod, CompressConfig
import yaml

with open("std.yaml") as f:
    param = yaml.safe_load(f)

# YAML format:
# mol num: 10
# j constant: [100, "cm-1"]
# temperature: [300, "K"]
# ph modes:
#   - [[1500, "cm-1"], [1.0]]
#   - [[500, "cm-1"], [0.5]]
# output dir: "./output"
# fname: "test"

model, temperature = load_from_dict(param, scheme=3, lam=False)

cdd = ChargeDiffusionDynamics(
    model, temperature=temperature,
    compress_config=CompressConfig(max_bonddim=16),
    evolve_config=EvolveConfig(EvolveMethod.tdvp_ps, adaptive=True, guess_dt=2),
    rdm=False,
)
cdd.evolve(evolve_dt=2, nsteps=1000)
```

## Pattern 2: From explicit Mol/Phonon (FMO complex)

```python
from renormalizer.model import Phonon, Mol, HolsteinModel
from renormalizer.utils import Quantity, cm2au

# Read spectral density from file
sdf_values = np.array(json.load(open("fmo_sdf.json")))
omegas_au = np.linspace(2, 300, 35) * cm2au
hr_factors = np.interp(omegas_au, sdf_values[:, 0], sdf_values[:, 1])

# Construct phonons from Huang-Rhys factors
phonons = [Phonon.simplest_phonon(Quantity(o), Quantity(l), lam=True)
           for o, l in zip(omegas_au, hr_factors * omegas_au)]

# J matrix from literature (cm⁻¹ → a.u.)
j_matrix_au = j_matrix_cm * cm2au

mlist = [Mol(Quantity(j), phonons) for j in np.diag(j_matrix_au)]
model = HolsteinModel(mlist, j_matrix_au)

# Reorder molecules per FMO arrangement
mol_arrangement = np.array([7, 5, 3, 1, 2, 4, 6]) - 1
model = HolsteinModel(
    list(np.array(mlist)[mol_arrangement]),
    j_matrix_au[mol_arrangement][:, mol_arrangement]
)
```

## Pattern 3: Custom SSH model (manual Op construction)

```python
from renormalizer.model.op import Op
from renormalizer import Model, BasisSimpleElectron, BasisSHO

# Custom e-ph coupling: g (a†_{i+1} a_i + a†_i a_{i+1}) (X_{i+1} - X_i)
for i in range(nsites - 1):
    ham.append(Op(r"a^\dagger a", [i+1, i]) * Op("x", (i+1, 0)) * (-g))
    ham.append(Op(r"a^\dagger a", [i+1, i]) * Op("x", (i, 0)) * g)
    ham.append(Op(r"a^\dagger a", [i, i+1]) * Op("x", (i+1, 0)) * g)
    ham.append(Op(r"a^\dagger a", [i, i+1]) * Op("x", (i, 0)) * (-g))
```

## Key Patterns
- Use `Phonon.simplest_phonon` for harmonic modes with linear e-ph coupling.
- `lam=True`: interpret coupling as reorganization energy (\lambda = g^{2}\omega).
- `load_from_dict`: standardized YAML \rightarrow model pipeline for batch simulations.
- Scheme 3: interleaved [e_{0}, ph_{0}, e_{1}, ph_{1}, ...] ordering.
- `ChargeDiffusionDynamics` for charge transport (mean squared displacement).
