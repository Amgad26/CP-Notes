
# Prefix Sum & Partial Sum (Difference Array)

## 1. What is Prefix Sum?

A **Prefix Sum** array stores running aggregates of a base array, so that a query over any range `[L, R]` can be answered in **O(1)** instead of **O(n)**.

> ⚠️ **Prefix is not only Sum!** The same idea generalizes to any associative/combinable operation:
> - Prefix **Sum**
> - Prefix **Product**
> - Prefix **Max**
> - Prefix **Min**

The general pattern is:
```
pre[i] = combine(pre[i - 1], arr[i])
```
where `combine` depends on what you're tracking.

---

## 2. Prefix Sum

### Build
```cpp
pre[0] = arr[0];
for (int i = 1; i < n; i++)
    pre[i] = pre[i - 1] + arr[i];
```

### Range Sum Query [L, R]
```cpp
int rangeSum(int L, int R) {
    if (L == 0) return pre[R];
    return pre[R] - pre[L - 1];
}
```

---

## 3. Prefix Product

### Build
```cpp
prefix[0] = arr[0];
for (int i = 1; i < n; i++)
    prefix[i] *= prefix[i - 1];   // i.e. prefix[i] = prefix[i-1] * arr[i]
```

### Range Product Query [L, R]
```cpp
ans = prefix[R] / prefix[L - 1];
```

**⚠️ Gotchas with Prefix Product:**
- Division-based range query breaks if any element in the range is **0** (division by/of zero).
- Watch for overflow — products grow extremely fast; consider `long long`, or work in **log-space** (sum of logs) for very large products, or track sign + zero-count separately if zeros/negatives are possible.
- If MOD arithmetic is involved, you **cannot** divide directly — you need **modular inverse** instead of plain division.

---

## 4. Prefix Max

### Build
```cpp
pre[i] = max(pre[i - 1], arr[i]);
```

### Full Example
```cpp
pre[0] = arr[0];
for (int i = 1; i < n; i++)
    pre[i] = max(pre[i - 1], arr[i]);
// pre[i] now holds the max of arr[0..i]
```
- Useful for "max so far" style problems, but note this only gives max of a **prefix ending at i**, not an arbitrary range `[L, R]` — for arbitrary range max/min queries you'd need a Sparse Table / Segment Tree instead.

---

## 5. Prefix Min

### Build
```cpp
pre[i] = min(pre[i - 1], arr[i]);
```

### Full Example
```cpp
pre[0] = arr[0];
for (int i = 1; i < n; i++)
    pre[i] = min(pre[i - 1], arr[i]);
// pre[i] now holds the min of arr[0..i]
```

---

## 6. Partial Sum (a.k.a. Difference Array Technique)

Used when you need to apply **many range updates** (add `val` to every element in `[L, R]`) efficiently, then read the final array **once** at the end.

- Naive approach: updating a range `[L, R]` directly costs O(n) per update → O(n·q) total for q updates.
- Partial Sum / Difference Array approach: each update costs **O(1)**, and a single O(n) prefix-sum pass at the end reconstructs the final array → **O(n + q)** total.

### The 5 Steps

**Step 1 — Make an array for updates**
```cpp
int updates[N]; // same size as original array, initialized to 0
```

**Step 2 — Add the value to updates[L]**
```cpp
updates[l] += val;
```

**Step 3 — Add the value with opposite polarity to updates[R + 1]**
```cpp
updates[r + 1] -= val;
```

**Step 4 — Prefix sum on the updates array**
```cpp
for (int i = 1; i < N; i++)
    updates[i] += updates[i - 1];
```

**Step 5 — Add updates to the original array**
```cpp
for (int i = 0; i < N; i++)
    arr[i] += updates[i];
```

### Full Working Example
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n, q;
    cin >> n >> q;

    vector<int> arr(n, 0);
    vector<int> updates(n + 1, 0); // +1 to safely handle r+1 == n

    for (int i = 0; i < q; i++) {
        int l, r, val;
        cin >> l >> r >> val;

        updates[l] += val;
        if (r + 1 < (int)updates.size())
            updates[r + 1] -= val;
    }

    // Step 4: prefix sum on updates
    for (int i = 1; i < n; i++)
        updates[i] += updates[i - 1];

    // Step 5: apply updates to original array
    for (int i = 0; i < n; i++)
        arr[i] += updates[i];

    for (int i = 0; i < n; i++)
        cout << arr[i] << " \n"[i == n - 1];
}
```

### Why It Works
- Adding `val` at index `L` means "from here onward, add val" (like a running total).
- Subtracting `val` at index `R+1` means "cancel that addition from here onward."
- After the prefix-sum pass, every index within `[L, R]` has accumulated exactly `+val`, and everything outside the range is untouched (net effect cancels out).

---

## 7. Common Adhoc Problem Patterns

**Using Prefix Sum:**
- Range sum queries in O(1)
- Subarray sum equals K (combined with hashing/frequency array of prefix sums)
- Equilibrium index (where left sum == right sum)
- 2D prefix sum for submatrix sum queries

**Using Prefix Product:**
- Range product queries (careful with zeros/mod)
- Counting subarrays with product properties (even/odd, divisibility)

**Using Prefix Max / Min:**
- "Best value up to index i" style DP problems
- Stock buy-sell problems (max profit with prefix min of prices)
- Leaders in an array (element greater than all elements to its right — computed via suffix version)

**Using Partial Sum / Difference Array:**
- Multiple range-add updates, single final read
- Simulating range increments efficiently (e.g., booking/reservation counting, "number of overlapping intervals at each point")
- Range update + point query problems (as opposed to point update + range query, which is more of a Segment Tree/Fenwick use case)

---

## 8. Key Tips / Gotchas

- Always decide 0-indexed vs 1-indexed **before** coding — prefix sum off-by-one errors are extremely common (`pre[L-1]` needs bounds checking when `L == 0`).
- For partial sum / difference arrays, always allocate **at least size n+1** to safely handle `updates[r + 1]` when `r == n - 1`.
- Prefix Max/Min from index 0 only answers "prefix ending at i" queries, not arbitrary `[L, R]` — don't confuse this with a Sparse Table's O(1) arbitrary-range min/max.
- Prefix Product with division needs special handling for zeros and negative numbers (sign tracking), and modular arithmetic needs **modular inverse**, not plain `/`.
- Difference array technique only supports **range update → final point query**; if you need **range update AND range query** interleaved with updates, you need a Fenwick Tree / Segment Tree with lazy propagation instead.
- Use `long long` for sums/products when values or n are large, to avoid overflow.

---

## 9. Summary Table

| Technique | Build Formula | Query |
|---|---|---|
| Prefix Sum | `pre[i] = pre[i-1] + arr[i]` | `pre[R] - pre[L-1]` |
| Prefix Product | `prefix[i] *= prefix[i-1]` | `prefix[R] / prefix[L-1]` |
| Prefix Max | `pre[i] = max(pre[i-1], arr[i])` | `pre[i]` = max of arr[0..i] |
| Prefix Min | `pre[i] = min(pre[i-1], arr[i])` | `pre[i]` = min of arr[0..i] |
| Partial Sum (Diff Array) | `updates[l] += val; updates[r+1] -= val;` then prefix-sum `updates`, then `arr[i] += updates[i]` | Range update in O(1), final array in O(n) |

---
