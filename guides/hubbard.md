# Example: Hubbard Model

Source: `example/hubbard.py`

## Physics
1D Hubbard model with open boundary condition:
H = t \Sigma (a^\dagger_i a_{i+1} + h.c.) + U \Sigma n_{i\uparrow} n_{i\downarrow}

Jordan-Wigner transform maps fermions to spins:
- a_j \rightarrow \Pi_{l<j} \sigma_z[l] \cdot \sigma_+[j]
- a^\dagger_j \rightarrow \Pi_{l<j} \sigma_z[l] \cdot \sigma_-[j]

## Key concepts demonstrated
- Jordan-Wigner transformation in `Op` notation
- Multi-component quantum numbers (alpha/beta electrons)
- DMRG ground state via `optimize_mps`
- Imaginary-time evolution as alternative to DMRG
- Step-by-step convergence monitoring

## Code walkthrough

### 1. Model construction via JW strings
```python
nsites = 10; t = -1; U = 4

# Site ordering: 0up, 0down, 1up, 1down, ...
# JW string: Z ... Z + Z - for hopping
for i in range(2*(nsites-1)):
    op = Op("Z + Z -", [i, i, i+1, i+2], factor=t, qn=qn_list)
    ham_terms.append(op)

# Onsite U: n_{up} n_{down}
for i in range(0, 2*nsites, 2):
    op = Op("- + - +", [i, i, i+1, i+1], factor=U,
            qn=[qn_up["-"], qn_up["+"], qn_down["-"], qn_down["+"]])
```

### 2. Custom sigmaqn for up/down distinction
```python
for i in range(2*nsites):
    if i % 2 == 0:
        sigmaqn = np.array([[0, 0], [1, 0]])  # up site
    else:
        sigmaqn = np.array([[0, 0], [0, 1]])  # down site
    basis.append(BasisHalfSpin(i, sigmaqn=sigmaqn))
```

### 3. DMRG optimization
```python
nelec = [5, 5]  # half-filling
M = 100
mps = Mps.random(model, nelec, M, percent=1.0)
mps.optimize_config.procedure = [[M, 0.4], [M, 0.2], [M, 0.1], [M, 0]]*2
mps.optimize_config.method = "2site"
energies, mps = optimize_mps(mps.copy(), mpo)
gs_e = min(energies)
```

### 4. Imaginary-time alternative
```python
evolve_config = EvolveConfig(EvolveMethod.tdvp_ps,
    adaptive=True, guess_dt=1e-3/1j, adaptive_rtol=5e-4)
mps.evolve_config = evolve_config

for istep in range(100):
    mps = mps.evolve(mpo, 0.5/1j)
    energy = mps.expectation(mpo)
    if abs(energy - energy_old) < 1e-5: break
    energy_old = energy
```

## Computational notes
- JW strings produce O(nsites) bond dimension in MPO.
- Quantum number conservation reduces computational cost significantly.
- DMRG is faster than imaginary-time for this model.
- DMRG energy: ~-20.9 for nsites=10, U=4, t=-1, half-filling.
