# Absorption / Fluorescence Spectra

## Zero Temperature

### Absorption
```python
from renormalizer.spectra import SpectraOneWayPropZeroT, SpectraTwoWayPropZeroT
from renormalizer.utils import Quantity

spec = SpectraOneWayPropZeroT(
    model=model,
    spectratype="abs",               # "abs" or "emi"
    optimize_config=OptimizeConfig(procedure=[[40, 0.2], [40, 0]]*5),
    evolve_config=EvolveConfig(
        method=EvolveMethod.tdvp_vmf,
        adaptive=True,
        guess_dt=0.1,
        adaptive_rtol=5e-4,
    ),
    compress_config=CompressConfig(threshold=1e-3, max_bonddim=64),
    offset=Quantity(-e_ground),       # energy shift for stability
)

# Run propagation
spec.propagate(nsteps=1000, evolve_dt=0.1)

# Get spectrum via Fourier transform
freqs, spectrum = spec.calc_spectrum(
    freq_range=(0, 4),                # eV or a.u.
    freq_resolution=0.001,
    t_slice=None,                     # or slice(start, end)
)
```

### Two-way propagation
```python
# More accurate but ~2x cost
spec = SpectraTwoWayPropZeroT(
    model, spectratype="abs",
    optimize_config=optimize_config,
    evolve_config=evolve_config,
    compress_config=compress_config,
)
```

### How it works
1. DMRG optimization finds ground state.
2. Constructs |\psi(0)\rangle = \mu|\psi_GS\rangle (dipole-applied state) for absorption, or a|\psi_GS\rangle for emission.
3. Real-time propagation of |\psi(t)\rangle.
4. Autocorrelation function C(t) = \langle\psi(0)|\psi(t)\rangle computed.
5. Spectrum = Fourier transform of C(t).

## Finite Temperature

### Absorption
```python
from renormalizer.spectra import SpectraFiniteT

spec = SpectraFiniteT(
    model, spectratype="abs",
    temperature=Quantity(300, "K"),
    evolve_config=evolve_config,
    compress_config=compress_config,
)

# Imaginary time first (thermal state), then real time
spec.propagate(
    evolve_dt=0.05,
    nsteps=2000,
    it_evolve_dt=1e-3/1j,            # imaginary time step
    it_nsteps=500,                    # imaginary time steps
)
freqs, spectrum = spec.calc_spectrum(freq_range=(0, 4), freq_resolution=0.001)
```

### How it works
1. Start from infinite-temperature MpDm (identity).
2. Imaginary-time propagation to finite temperature.
3. Apply dipole operator to obtain \rho(T) \cdot \mu.
4. Real-time propagation.
5. Correlation function \rightarrow spectrum.

## Complex Vibronic (CV) Spectra
For large systems where full thermal propagation is expensive:
```python
from renormalizer.cv import SpectraCVZeroT, SpectraCVFiniteT

# Uses TTNS/TTNO for efficiency
spec = SpectraCVFiniteT(
    model,
    spectratype="abs",
    temperature=Quantity(300, "K"),
    nexciton=1,
    m_max=32,
    evolve_config=evolve_config,
)
```

## Key Parameters
| Parameter | Typical | Notes |
|-----------|---------|-------|
| `evolve_dt` | 0.05-0.1 a.u. | For real time; ~2.4 fs at dt=0.1 |
| `nsteps` | 1000-5000 | Total time = nsteps \times dt |
| `freq_range` | (0, 4) eV | Adjust based on system |
| `freq_resolution` | 0.001 eV | Determines FFT resolution |
| `offset` | `Quantity(-E_GS)` | Improves numerical stability |
| `it_evolve_dt` | `1e-3/1j` | Imaginary time step |
| `it_nsteps` | 300-500 | Sufficient for T \leq 300K typical systems |

## Pitfalls
- Set `offset` to approximately `-E_GS` to avoid numerical overflow.
- Long propagation times need small `evolve_dt` or adaptive stepping.
- For high-frequency resolution, increase total propagation time.
- The `compress_config` must be tight enough to prevent bond dimension explosion.
