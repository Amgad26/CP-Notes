# Fast Exponentiation (Binary Exponentiation)

- Compute aⁿ mod m in O(log n)
- pow(a, n, m): if n==0 return 1; half = pow(a, n/2, m); res = half*half % m; if n odd: res = res*a % m

---
