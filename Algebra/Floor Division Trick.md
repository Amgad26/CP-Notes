# Counting Multiples in a Range (Floor Division Trick)

## Problem
Given `L`, `R`, `N`, count how many numbers in `[L, R]` are **not** divisible by `N`.

Constraints can be large (up to `10^9`), so brute force is too slow.

## The Brute Force (Too Slow)

```cpp
long long l, r, n;
cin >> l >> r >> n;

long long first = n;
long long counter = 0;
while (n <= r) {
    if (n >= l && n <= r)
        counter++;
    n += first;
}

cout << ((r - l) + 1) - counter << "\n";
```

This walks through every multiple of `N` one at a time: `N, 2N, 3N, ...`
- Correct, but **O(R/N)** iterations.
- If `N = 1` and `R = 10^9`, this loops a billion times → TLE (time limit exceeded).

## The Trick: Counting Multiples with Floor Division

> Number of multiples of `N` in `[1, X]` = `X / N` (integer/floor division)

**Why this works:** Multiples of `N` are `N, 2N, 3N, ..., kN`. We want the largest `k` such that `kN ≤ X`, i.e. `k ≤ X/N`. Since `k` must be an integer, the largest valid `k` is exactly `floor(X/N)`.

## Extending to a Range [L, R]

- `R/N` → multiples of `N` in `[1, R]`
- `(L-1)/N` → multiples of `N` in `[1, L-1]`
- Subtracting removes everything below `L`, leaving only multiples inside `[L, R]`

### ⚠️ Why `L-1` and not `L`
- `R/N` counts multiples up to **and including** `R`.
- We need to subtract multiples **strictly before** `L` — not including `L` itself, since `L` is part of our target range.
- Using `L/N` instead would count `L` itself (if `L` is a multiple of `N`) and wrongly subtract it, even though it should be counted as "in range."

**Example:** L=5, R=12, N=5
- Multiples of 5 in [1,12]: `5, 10` → `12/5 = 2`
- Multiples of 5 in [1,4] (below L): none → `4/5 = 0` ✅ correct
- If we'd used `L/N = 5/5 = 1` instead → wrongly subtracts `5`, which is inside `[L,R]` and should count.

## Fast Solution (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    long long l, r, n;
    cin >> l >> r >> n;

    long long total = r - l + 1;
    long long divisible = r / n - (l - 1) / n;

    cout << (total - divisible) << "\n";
}
```

- **O(1)** time — no loop needed at all.

## Key Takeaway
This is a direct application of the **floor function**. Counting "how many multiples of N are ≤ X" is exactly integer division `X // N` — one step instead of a loop.

---
