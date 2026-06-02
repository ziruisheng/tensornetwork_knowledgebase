# TTNS/TTNO — Tree Tensor Networks

## Overview
Renormalizer supports tree tensor network states (TTNS) and operators (TTNO)
as an alternative to MPS/MPO. TTNS can be more efficient for systems with
long-range interactions or complex connectivity.

## When to use TTNS
- Systems where MPS bond dimensions grow too large.
- Long-range electronic couplings (not just nearest-neighbor).
- Molecular aggregates with complex interaction graphs.
- When DMRG optimization struggles with local minima.

## Basic TTNS Usage

### Construction
```python
from renormalizer.tn import Tree, TTNS, TTNO, optimize_ttns

# Define tree structure
tree = Tree.binary_tree(n_leaves=8)  # or build manually

# Build TTNS (tree tensor network state)
ttns = TTNS.random(model, tree, qntot, m_max=32)

# Build TTNO (tree tensor network operator)
ttno = TTNO(model, tree)

# Optimize (TTNS ground state)
energy, ttns = optimize_ttns(ttns, ttno)
```

### Tree Structure
```python
from renormalizer.tn import Tree, Node

# Manual tree construction
root = Node("root")
left = Node("left")
right = Node("right")
root.add_child(left)
root.add_child(right)

tree = Tree(root)

# Built-in structures
tree = Tree.binary_tree(8)       # perfect binary tree with 8 leaves
```

## TTNS Ground State
```python
from renormalizer.tn import optimize_ttns

# Similar API to MPS DMRG
ttns.optimize_config = OptimizeConfig(
    procedure=[[20, 0.3], [30, 0.1], [40, 0], [40, 0]]
)
energy, ttns = optimize_ttns(ttns, ttno)
```

## TTNS Time Evolution
```python
ttns.evolve_config = EvolveConfig(
    method=EvolveMethod.tdvp_ps,
    adaptive=True,
    guess_dt=0.1,
)
new_ttns = ttns.evolve(ttno, dt)
```

## Complex Vibronic (CV) with TTNS
```python
from renormalizer.cv import SpectraCVZeroT

spec = SpectraCVZeroT(
    model,
    spectratype="abs",
    nexciton=1,
    m_max=32,
    tree_type="binary",      # tree structure
    evolve_config=evolve_config,
)

spec.propagate(nsteps=1000, evolve_dt=0.1)
```

## Key Differences from MPS
| Aspect | MPS | TTNS |
|--------|-----|------|
| Connectivity | Linear chain | Tree (any tree) |
| Bond dimension scaling | O(nsite) | O(log(nsite)) for binary tree |
| Entanglement handling | Limited to 1D area law | Can capture more complex entanglement |
| DMRG algorithm | Standard 1D sweep | Modified tree sweep |
| Time evolution | TDVP-MPS | Adapted TDVP-TTNS |
| Performance | Optimized for 1D | Better for complex topologies |

## Limitations
- TTNS support is less mature than MPS in Renormalizer.
- Not all MPS features available (check API for specific methods).
- Tree structure must be chosen carefully for the problem.
- Bond dimension management is more complex.
- Quantum number conservation in TTNS may have restrictions.

## References
- Weitang Li, Jiajun Ren, Hengrui Yang, Haobin Wang, Zhigang Shuai.
  "Optimal tree tensor network operators for tensor network simulations:
  Applications to open quantum systems." J. Chem. Phys. 161, 054116 (2024).
