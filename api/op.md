# Op / OpSum — Operator Construction

## Op — Symbolic Operator
```python
from renormalizer.model import Op
```

### Simple operators (one symbol)
```python
Op("X", 0)                          # σ_x on DoF 0, factor=1
Op("Z", 1, 0.5)                     # 0.5 σ_z on DoF 1
Op("a", 0, 1.0)                     # annihilation on DoF 0
Op(r"a^\dagger", 1)                 # creation on DoF 1
Op("n", 0)                          # occupation n = a†a
Op(r"b^\dagger b", "v_0")           # phonon occupation
Op(r"b^\dagger + b", "v_0")         # phonon position
Op("x", "v_0", factor=0.1)          # position operator
Op("p^2", "v_0", factor=0.5)        # momentum squared
Op("sigma_z", "spin", 1.0)          # Pauli Z
Op("sigma_x", "spin", 1.0)          # Pauli X
Op("sigma_+", 0)                    # raising operator
Op("sigma_-", 0)                    # lowering operator
```

### Complex operators (multi-symbol product)
```python
# a†_i a_j — hopping
Op(r"a^\dagger a", [0, 1], factor=1.0)

# σ_z^0 σ_z^1 — Ising coupling
Op("sigma_z sigma_z", [0, 1], factor=1.0/4)

# a†_i a_i b†_j — electron-phonon coupling
Op(r"a^\dagger a b^\dagger", [0, 0, "v_1"], factor=1.0)

# Σ spin operators with quantum numbers
Op("sigma_+ sigma_-", [0, 1], factor=0.5)
```

### Quantum Numbers
```python
# Default: a† → +1, a → -1, others → 0
Op(r"a^\dagger a", [0, 1])          # qn = [+1, -1] = 0 total

# Explicit quantum numbers
Op("X", 0, qn=0)
Op(r"a^\dagger a", [0, 1], qn=[1, -1])

# Multiple quantum numbers (e.g., Hubbard: alpha and beta electrons)
Op("Z + Z -", dofs, qn=[[0,0], [-1,0], [0,0], [1,0]])
```

### Factor: use Quantity for unit conversion
```python
from renormalizer.utils import Quantity
Op("X", 0, factor=Quantity(100, "cm-1"))  # auto-converted to a.u.
```

### Identity
```python
Op.identity(0)                     # I on DoF 0
Op.identity([0, 1])                # I⊗I on DoFs 0 and 1
```

### squeeze_identity
```python
Op("X I Y I", [0, 1, 2, 3]).squeeze_identity()  # → Op("X Y", [0, 2])
```

## OpSum — Sum of Operators (list subclass)
`Op + Op` returns `OpSum`. All arithmetic is supported.

### Construction
```python
# Addition creates OpSum automatically
opsum = Op("X", 0, 1.0) + Op("Z", 1, 2.0)
opsum = Op("X", 0) - Op("Y", 0)    # subtraction

# Manual construction
opsum = OpSum([Op("X", 0), Op("Z", 1)])
```

### Arithmetic
```python
# Scalar multiplication
opsum * 2.0
2.0 * opsum
opsum / 0.5

# OpSum × OpSum (Cartesian product)
opsum1 * opsum2
OpSum.product([opsum1, opsum2])

# Op × OpList
Op("X", 0) * [Op("Y", 1), Op("Z", 1)]  # distributes
```

### simplify
```python
opsum = Op("X", 0, 0.5) + Op("X", 0, 0.5) + Op("Y", 1, 1e-5)
opsum.simplify()                    # → [Op("X", [0], 1.0), Op("Y", [1], 1e-5)]
opsum.simplify(atol=1e-3)          # → [Op("X", [0], 1.0)]
```
- Combines same terms (same symbol + same dofs).
- Removes terms with |factor| \leq atol.

### split_elementary
```python
op = Op("X Y Z", [0, 3, 1], 2.0)
dof_to_siteidx = {0: 0, 1: 0, 3: 1}
op.split_elementary(dof_to_siteidx)
# → ([Op('Y Z', [1, 1], 1.0), Op('X', [0], 1.0)], 2.0)
```

## Direct use with Model, Mpo, Mps
```python
# Model accepts Op or OpSum directly
model = Model(basis, ham_terms)

# Mpo from Op/OpSum
mpo = Mpo(model, Op("X", 0))
mpo = Mpo(model, OpSum([...]))

# Mps.expectation accepts Op/OpSum directly
val = mps.expectation(Op("X", 0))
val = mps.expectation(OpSum([op1, op2]))
```

## Supported Operator Symbols
Symbols are defined by each BasisSet. Common ones:

| Basis | Symbols |
|-------|---------|
| BasisHalfSpin | `X`, `Y`, `Z`, `sigma_z`, `sigma_x`, `sigma_y`, `sigma_+`, `sigma_-`, `n`, `I` |
| BasisSimpleElectron | `a`, `a^\dagger`, `a^\dagger a` (n) |
| BasisSHO | `b`, `b^\dagger`, `b^\dagger b` (n), `b^\dagger + b`, `x`, `x^2`, `p`, `p^2` |
| BasisSineDVR | `x`, `x^2`, `p`, `p^2` |
| BasisMultiElectron | `a`, `a^\dagger`, `a^\dagger a` etc. |

Empty space in complex symbols separates simple symbols:
- `"a^\dagger a"` \rightarrow two symbols
- `r"b^\dagger + b"` \rightarrow one symbol (replaced internally)
