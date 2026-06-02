# Example: Kubo Transport

Source: `example/transport_kubo.py`

## Physics
Carrier mobility from Green-Kubo current-current correlation:
\mu = (1/k_B T) \int C(t) dt
C(t) = \langlej(t) j(0)\rangle

## Code

```python
from renormalizer.model import load_from_dict
from renormalizer.transport import TransportKubo
from renormalizer.utils import EvolveConfig, CompressConfig
import yaml

# Load parameters from YAML
with open("std.yaml") as f:
    param = yaml.safe_load(f)

model, temperature = load_from_dict(param, scheme=3, lam=False)

# Separate configs for imaginary and real time
ievolve_config = EvolveConfig(
    adaptive=True,
    guess_dt=temperature.to_beta() / 1000j    # β/1000 per step
)
evolve_config = EvolveConfig(
    adaptive=True,
    guess_dt=2                                 # real time step
)
compress_config = CompressConfig(threshold=1e-4)

# Run Kubo transport
ct = TransportKubo(
    model,
    temperature=temperature,
    ievolve_config=ievolve_config,     # imaginary-time config
    evolve_config=evolve_config,        # real-time config
    compress_config=compress_config,
    dump_dir=param["output dir"],
    job_name=param["fname"] + "_autocorr"
)

ct.evolve(
    param.get("evolve dt"),
    param.get("nsteps"),
    param.get("evolve time")
)
```

## Two-Step Process
1. **Imaginary time**: \rho(\infty) \rightarrow \rho(T/2) via `ThermalProp`.
2. **Real time**: Split \rho(T/2) into two chains, evolve separately, compute C(t).

## Key Parameters from YAML
```yaml
mol num: 10
j constant: [100, "cm-1"]
temperature: [300, "K"]
ph modes:
  - [[1500, "cm-1"], [1.2]]
nsteps: 2000
evolve dt: 0.1
evolve time: null   # if set, overrides nsteps
```
