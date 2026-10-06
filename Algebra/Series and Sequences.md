# Series & Sequences (in depth)

**Arithmetic Progression (AP):**

- aₙ = a₁ + (n−1)d
- Sₙ = n/2 · (2a₁ + (n−1)d) = n/2 · (a₁ + aₙ)

**Geometric Progression (GP):**

- aₙ = a₁ · r^(n−1)
- Sₙ = a₁(rⁿ − 1)/(r − 1), r ≠ 1
- Infinite sum (|r| < 1): S∞ = a₁/(1 − r)
- Mod p version: Sₙ = a₁ · (rⁿ − 1) · inv(r − 1) mod p

**Power sums (1..n):**

- Σk = n(n+1)/2
- Σk² = n(n+1)(2n+1)/6
- Σk³ = [n(n+1)/2]²
- Σk⁴ = n(n+1)(2n+1)(3n²+3n−1)/30

**Harmonic series:**

- Hₙ = Σ(1/k) for k=1..n ≈ ln(n) + γ (γ ≈ 0.5772)
- Useful for bounding complexity of sieve-like/divisor-sum loops (O(n log n))

**Telescoping series:**

- Σ(f(k) − f(k−1)) = f(n) − f(0)
- Example: Σ 1/(k(k+1)) = Σ(1/k − 1/(k+1)) = 1 − 1/(n+1)

**Sum of first n even/odd numbers:**

- Σ(2k) for k=1..n = n(n+1)
- Σ(2k−1) for k=1..n = n²

**Sum of squares of first n odd numbers:**

- Σ(2k−1)² for k=1..n = n(2n−1)(2n+1)/3

---
