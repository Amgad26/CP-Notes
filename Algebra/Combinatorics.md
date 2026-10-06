# Combinatorics

- nCr = n! / (r!(n−r)!)
- Precompute factorials + inverse factorials mod p for O(1) nCr queries
- Pascal's identity: C(n,r) = C(n−1,r−1) + C(n−1,r)
- C(n,0..n) sum = 2ⁿ
- Permutations: nPr = n!/(n−r)!
- Stars and bars: ways to split n identical items into k groups = C(n+k−1, k−1)
- Catalan numbers: Cₙ = C(2n,n)/(n+1) — balanced parentheses, BST counts, etc.
- Derangements: D(n) = n! · Σ((−1)^k / k!) for k=0..n

---
