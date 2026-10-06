# Matrix Exponentiation (Linear Recurrences in O(log n))

- Represent the recurrence as a transition matrix $M$, compute $M^n$ via **binary exponentiation**
- **Example (Fibonacci):**

$$ M = \begin{bmatrix} 1 & 1 \\ 1 & 0 \end{bmatrix} $$

$$ M^n = \begin{bmatrix} F(n+1) & F(n) \\ F(n) & F(n-1) \end{bmatrix} $$

```
M   = [1 1]      Mⁿ  = [F(n+1)  F(n)  ]
      [1 0]            [F(n)    F(n-1)]
```

- A general linear recurrence of order $k$ → a $k \times k$ transition matrix
- Complexity: $O(k^3 \log n)$ — matrix multiply cost $O(k^3)$, times $O(\log n)$ multiplications from fast exponentiation
## Recurrence-based Series (Closed Forms)

- For aₙ = c₁aₙ₋₁ + c₂aₙ₋₂: solve x² = c₁x + c₂ for roots r₁, r₂
- Closed form: aₙ = A·r₁ⁿ + B·r₂ⁿ (solve A, B from initial terms)
- If repeated root r: aₙ = (A + Bn)·rⁿ
- Sum of a linear recurrence sequence: often computed alongside the sequence itself via an augmented matrix (add a "running sum" state to the matrix exponentiation vector)

## Quick Recurrence Patterns

- Fibonacci: F(n) = F(n−1) + F(n−2)
- Linear recurrence → matrix exponentiation for O(log n)
- DP recurrences often reduce to polynomial/algebraic identities — look for telescoping or closed forms before brute-forcing

---
