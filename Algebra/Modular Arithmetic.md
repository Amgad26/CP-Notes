# Modular Arithmetic

- (a + b) mod m = ((a%m) + (b%m)) % m
- (a − b) mod m = ((a%m) − (b%m) + m) % m
- (a · b) mod m = ((a%m) · (b%m)) % m
- Modular inverse (m prime): a⁻¹ ≡ a^(m−2) mod m (Fermat)
- Modular inverse (general, gcd(a,m)=1): extended Euclidean algorithm
- Division mod m: a/b mod m = a · b⁻¹ mod m (only when inverse exists)
- Euler's theorem: a^φ(m) ≡ 1 (mod m) if gcd(a,m)=1
- φ(n) (Euler totient): n·∏(1 − 1/p) over prime factors p of n

---

