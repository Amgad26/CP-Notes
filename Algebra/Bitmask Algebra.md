# Bitmask Algebra

- Check bit i: (x >> i) & 1
- Set bit i: x | (1 << i)
- Clear bit i: x & ~(1 << i)
- Toggle bit i: x ^ (1 << i)
- Count set bits: __builtin_popcount(x)
- Iterate submasks of mask: for(sub = mask; sub; sub = (sub−1)&mask)
- XOR properties: a^a=0, a^0=a, XOR is commutative/associative — used in subset sum / linear basis problems

---
