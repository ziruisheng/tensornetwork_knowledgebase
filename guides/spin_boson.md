# Example: Spin-Boson Model

Source: `example/sbm.py`

## Physics
Dissipative two-level system:
H = \epsilon \sigma_z + \Delta \sigma_x + \Sigma \omega_i b^\dagger_i b_i + \sigma_z \Sigma c_i (b^\dagger_i + b_i)

The continuous bath spectral density J(\omega) = (\pi/2) \alpha \omega_c (\omega/\omega_c)^s exp(-\omega/\omega_c)
is discretized into n_phonons modes.

## Code

```python
from renormalizer.sbm import SpinBosonDynamics, param2mollist
from renormalizer.utils import Quantity, CompressConfig, EvolveConfig

# Physical parameters
alpha = 0.05                # coupling strength
delta = Quantity(1)         # tunneling (a.u.)
omega_c = Quantity(20)      # cutoff frequency (a.u.)
n_phonons = 300             # bath modes
renormalization_p = 1       # discretization scheme

# Build model
model = param2mollist(alpha, delta, omega_c, renormalization_p, n_phonons)

# Simulation config
compress_config = CompressConfig(threshold=1e-4)
evolve_config = EvolveConfig(adaptive=True, guess_dt=0.1)

# Run dynamics
sbm = SpinBosonDynamics(
    model, Quantity(0),                     # temperature (0 for T=0K)
    compress_config=compress_config,
    evolve_config=evolve_config,
    dump_dir="./",
    job_name="sbm"
)
sbm.evolve(evolve_dt=0.1, evolve_time=20)
```

## Key concepts
- `param2mollist`: convenience function to construct SpinBosonModel from physical parameters.
- `SpinBosonDynamics`: high-level wrapper that handles initial state, evolution, and logging.
- Outputs: \sigma_x(t), \sigma_z(t), reduced density matrix \rho(t), bond entropy.

## Parameter Scaling
| alpha | Regime | n_phonons needed |
|-------|--------|-----------------|
| 0.01-0.1 | Weak coupling | 100-300 |
| 0.1-0.5 | Intermediate | 200-500 |
| > 0.5 | Strong coupling | 500+ |

## Output
The job saves to `sbm/` directory (based on `job_name`):
- `sbm.log`: energies, occupations, entropy at each step.
- `sbm.npz`: dump of all observables arrays.
