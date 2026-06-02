# Finite Temperature Methods

## Thermal State Construction

### Via Imaginary-Time Evolution
```python
from renormalizer.mps import ThermalProp, MpDm
from renormalizer.utils import EvolveConfig, EvolveMethod

# Start from infinite temperature (identity MpDm)
mpdm = MpDm.max_entangled_ex(model)

thermal = ThermalProp(
    init_mpdm=mpdm,
    h_mpo_model=model,
    exact=False,                         # exact=False for general Hamiltonians
    evolve_config=EvolveConfig(
        method=EvolveMethod.tdvp_mu_cmf,
        adaptive=True,
        guess_dt=1e-3/1j,
        adaptive_rtol=5e-4,
    ),
)

# Evolve to target β/2
thermal.evolve(evolve_dt=1e-3/1j, nsteps=500)

# Access thermal state
rho_half = thermal.latest_mps     # MpDm at β/2

# Save for reuse
rho_half.dump("thermal_beta_half.npz")
```

### Exact Propagation (Holstein GS/EX space)
For Holstein model in GS or EX space, use exact propagation (no bond dimension growth):
```python
thermal = ThermalProp(
    init_mpdm=mpdm,
    h_mpo_model=model,
    exact=True,
    space="GS",                          # "GS" or "EX"
    evolve_config=evolve_config,
)
```

### Load Saved Thermal State
```python
from renormalizer.mps import load_thermal_state
rho_half = load_thermal_state(model, "thermal_beta_half.npz")
```

## MpDm — Matrix Product Density Operator
```python
from renormalizer.mps import MpDm

# Infinite temperature (identity)
mpdm = MpDm.max_entangled_ex(model)

# From MPS: |ψ⟩⟨ψ|
mpdm = MpDm.from_mps(mps)

# Expectation value (trace)
energy = mpdm.expectation(Mpo(model))
```

## Thermal Properties
```python
# During ThermalProp, properties are logged automatically
# After propagation, access arrays:
thermal.energies              # energy at each step
thermal.e_occupations_array   # electronic occupations
thermal.ph_occupations_array  # phonon occupations
thermal.vn_entropy_array      # von Neumann entropy
```

### Custom Properties via Property Class
```python
from renormalizer.property import Property

class MyProperties(Property):
    def calc_properties(self, mps):
        # Store results in self.prop_res dict
        # mps is the MpDm at current step
        z_val = mps.expectation(Mpo(self.model, Op("sigma_z", "spin")))
        self.prop_res.setdefault("sigma_z", []).append(z_val)

thermal = ThermalProp(
    init_mpdm=mpdm,
    properties=MyProperties(model),
    ...
)
```

## Which Temperature Range?
| Temperature | it_nsteps (dt=1e-3) | Notes |
|-------------|---------------------|-------|
| \infty \rightarrow 300K | 200-300 | Fast convergence |
| \infty \rightarrow 200K | 300-500 | Most common |
| \infty \rightarrow 100K | 500-1000 | Slow convergence |
| \infty \rightarrow 50K | 1000+ | May need smaller dt |

## Two-Step Strategy for Low Temperature
```python
# Step 1: Coarse cooling
thermal1 = ThermalProp(init_mpdm=mpdm, evolve_config=coarse_config)
thermal1.evolve(evolve_dt=5e-3/1j, nsteps=200)

# Step 2: Fine cooling from saved state
thermal1.latest_mps.dump("thermal_coarse.npz")
rho_half = load_thermal_state(model, "thermal_coarse.npz")

thermal2 = ThermalProp(init_mpdm=rho_half, evolve_config=fine_config)
thermal2.evolve(evolve_dt=1e-3/1j, nsteps=300)
```

## Thermofield Approach
Alternative to imaginary-time evolution — uses doubled system:
```python
# Not a public API yet; contact developers for details.
# See renormalizer.utils.tdmps.TdMpsJob for infrastructure.
```

## Finite-T Spectra / Transport
Use the thermal MpDm directly:
- Spectra: `SpectraFiniteT` accepts temperature directly.
- Transport: `TransportKubo` accepts `init_mpdm` or `init_mpdm_path`.
