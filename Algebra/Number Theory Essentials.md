# Number Theory Essentials

- Sieve of Eratosthenes: O(n log log n) primes up to n
- Prime factorization via smallest prime factor (SPF) sieve: O(log n) per query after O(n log log n) precompute
- GCD/LCM: gcd(a,b) via Euclidean algorithm; lcm(a,b) = a·b/gcd(a,b)
- Extended Euclid: finds x,y such that ax + by = gcd(a,b)
- CRT (Chinese Remainder Theorem): solve x ≡ r₁ (mod m₁), x ≡ r₂ (mod m₂) when gcd(m₁,m₂)=1
- Number of divisors: if n = p₁^e₁·p₂^e₂..., d(n) = ∏(eᵢ+1)
- Sum of divisors: σ(n) = ∏((p^(e+1) − 1)/(p − 1))

## Useful Identities for Problem Solving

- Sum of GP mod p (r ≠ 1): careful with modular inverse of (r−1)
- Lucas' theorem: compute C(n,r) mod p (p prime, n/r large) by splitting into base-p digits
- Wilson's theorem: (p−1)! ≡ −1 (mod p) if p prime
- Fermat's little theorem: aᵖ⁻¹ ≡ 1 (mod p) if p prime, gcd(a,p)=1

## Common Modulus Values in CP

- 1e9+7 (1000000007), 998244353 (NTT-friendly prime)
- Always take mod after every multiplication to avoid overflow (use long long / __int128 in C++)

## Parity Rules

- odd + odd = even
- even + even = even
- odd + even = odd

---
