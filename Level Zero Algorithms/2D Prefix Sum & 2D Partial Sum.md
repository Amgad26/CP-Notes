
# 2D Prefix Sum & 2D Partial Sum (2D Difference Array)

## 1. What is 2D Prefix Sum?

A **2D Prefix Sum** array extends the 1D idea to a matrix, so that the sum of any **rectangular submatrix** can be answered in **O(1)** after an O(n·m) build, instead of recomputing the sum every time (which would cost O(n·m) per query).

`pre[i][j]` = sum of all elements in the rectangle from `(0,0)` to `(i,j)` (top-left corner fixed at origin).

---

## 2. 2D Prefix Steps

**STEP 1 — Make a 2D array for prefix**
```cpp
int pre[N][N];
```

**STEP 2 — Prefix sum for each row**
```cpp
pre[i][j] += pre[i][j - 1];
```

**STEP 3 — Prefix sum for each column**
```cpp
pre[i][j] += pre[i - 1][j];
```

**STEP 4 — Calculate the answer** (submatrix sum with corners `Up-Left = (U, L)` and `Down-Right = (D, R)`)
```cpp
Ans = pre[D][R]
    - pre[D][L - 1]
    - pre[U - 1][R]
    + pre[U - 1][L - 1];
```

> The `+ pre[U-1][L-1]` term corrects for the region subtracted **twice** by the two subtraction terms (inclusion-exclusion principle).

---

## 3. Full Build (Combining Steps 1-3)

```cpp
int arr[N][N]; // original matrix (1-indexed for safety)
int pre[N][N] = {0};

// Copy values first
for (int i = 1; i <= n; i++)
    for (int j = 1; j <= m; j++)
        pre[i][j] = arr[i][j];

// STEP 2: row-wise prefix sum
for (int i = 1; i <= n; i++)
    for (int j = 1; j <= m; j++)
        pre[i][j] += pre[i][j - 1];

// STEP 3: column-wise prefix sum
for (int i = 1; i <= n; i++)
    for (int j = 1; j <= m; j++)
        pre[i][j] += pre[i - 1][j];
```

> ✅ Tip: These two loops can actually be merged into **one single pass** using the standard 2D prefix formula:
> ```cpp
> pre[i][j] = arr[i][j] + pre[i-1][j] + pre[i][j-1] - pre[i-1][j-1];
> ```
> This is mathematically equivalent (inclusion-exclusion) but only needs one nested loop instead of two — both are valid, the notes' version (row pass then column pass) is more intuitive to derive.

---

## 4. Query: Submatrix Sum [U, L] to [D, R]

```cpp
int query(int U, int L, int D, int R) {
    return pre[D][R]
         - pre[D][L - 1]
         - pre[U - 1][R]
         + pre[U - 1][L - 1];
}
```

- `U, L` = Up (top row), Left (left column) of the rectangle
- `D, R` = Down (bottom row), Right (right column) of the rectangle
- **1-indexing is strongly recommended** here so `L - 1` and `U - 1` never go negative.

### Visual Intuition (Inclusion-Exclusion)
```
pre[D][R]        -> everything from (0,0) to (D,R)
- pre[D][L-1]    -> remove everything left of the rectangle
- pre[U-1][R]    -> remove everything above the rectangle
+ pre[U-1][L-1]  -> add back the top-left corner (removed twice above)
```

---

## 5. Full Working Example: 2D Prefix Sum

```cpp
#include <bits/stdc++.h>
using namespace std;

int arr[1005][1005];
int pre[1005][1005];
int n, m;

void build() {
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            pre[i][j] = arr[i][j] + pre[i - 1][j] + pre[i][j - 1] - pre[i - 1][j - 1];
        }
    }
}

int query(int U, int L, int D, int R) {
    return pre[D][R] - pre[D][L - 1] - pre[U - 1][R] + pre[U - 1][L - 1];
}

int main() {
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            cin >> arr[i][j];

    build();

    int q;
    cin >> q;
    while (q--) {
        int U, L, D, R;
        cin >> U >> L >> D >> R;
        cout << query(U, L, D, R) << "\n";
    }
}
```

---

## 6. 2D Partial Sum (2D Difference Array)

Used when you need to apply **many rectangular range updates** (add `val` to every cell in the rectangle `[U,L]` to `[D,R]`) efficiently, then read the final matrix **once** at the end — same motivation as the 1D difference array, extended to 2D.

### 2D Partial Sum Steps (from original notes)

**STEP 1 — Make a 2D array for update**
```cpp
int update[N][N];
```

**STEP 2 — Update the array**
```cpp
update[U][L]++;
update[D + 1][L]--;
update[U][R + 1]--;
update[D + 1][R + 1]++;
```

**STEP 3 — Do Prefix sum for update array** (2D prefix sum, same as Section 2/3 above)
```cpp
for (int i = 1; i <= n; i++)
    for (int j = 1; j <= m; j++)
        update[i][j] += update[i-1][j] + update[i][j-1] - update[i-1][j-1];
```

After this, `update[i][j]` directly holds how much was added to cell `(i,j)` — add it to the original array if needed:
```cpp
arr[i][j] += update[i][j];
```

### Why the 4-Corner Trick Works (Inclusion-Exclusion again)
- `+val` at `(U, L)`: "start adding val from here, extending right and down"
- `-val` at `(D+1, L)`: "cancel the downward effect past row D"
- `-val` at `(U, R+1)`: "cancel the rightward effect past column R"
- `+val` at `(D+1, R+1)`: "add back — this region was cancelled twice by the two subtractions above"

After the 2D prefix-sum pass, exactly the rectangle `[U,L]` → `[D,R]` ends up with `+val` added, and everywhere else nets to zero.

### Full Working Example: 2D Partial Sum
```cpp
#include <bits/stdc++.h>
using namespace std;

int update[1005][1005];
int n, m;

int main() {
    cin >> n >> m;

    int q;
    cin >> q;
    while (q--) {
        int U, L, D, R, val;
        cin >> U >> L >> D >> R >> val;

        update[U][L]         += val;
        update[D + 1][L]     -= val;
        update[U][R + 1]     -= val;
        update[D + 1][R + 1] += val;
    }

    // STEP 3: 2D prefix sum on the update array
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++) {
            update[i][j] += update[i - 1][j] + update[i][j - 1] - update[i - 1][j - 1];
        }
    }

    // update[i][j] now holds the final value added at each cell
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= m; j++)
            cout << update[i][j] << " ";
        cout << "\n";
    }
}
```

---

## 7. Common Adhoc Problem Patterns

**Using 2D Prefix Sum:**
- Submatrix sum queries in O(1) after O(n·m) preprocessing
- Counting cells/regions satisfying a sum condition (e.g., "number of k×k submatrices with sum ≥ X")
- Grid-based DP optimizations where you need rectangle sums repeatedly
- Image processing style problems (integral image / summed-area table — same exact technique)

**Using 2D Partial Sum (Difference Array):**
- Multiple rectangular range-add updates, single final read (e.g., "add 1 to every cell in this rectangle, q times, then print final grid")
- Simulating overlapping rectangles / events on a grid (e.g., counting how many rectangles cover each cell)
- Chessboard / battlefield simulation problems with repeated rectangle painting
- Any "range update, point query (at the end)" problem on a 2D grid

---

## 8. Key Tips / Gotchas

- **Always use 1-indexing** for both prefix and difference arrays in 2D — this avoids constant special-casing for `U-1`, `L-1`, `D+1`, `R+1` going out of bounds.
- Make array size `(N+2) x (N+2)` (extra padding) to safely handle `D+1`/`R+1` when `D` or `R` equals `n` or `m`.
- Don't forget **all 4 corners** in the difference-array update — missing any one of the four breaks the inclusion-exclusion cancellation.
- The **order matters**: for 2D partial sum, you must do the update markers first, **then** run the full 2D prefix sum pass — doing it in the wrong order gives garbage.
- Use `long long` if the grid values or number of updates are large (sums can overflow `int` quickly in 2D).
- The 1D difference array only needs 2 markers per update (`l`, `r+1`); the 2D version needs **4 markers** because a rectangle has 4 corners instead of 2 endpoints — this generalizes further to 3D with 8 corners if ever needed.
- 2D prefix sum answers **only sum** by default — for 2D prefix max/min, you'd generally need Sparse Tables (2D) or Segment Trees, since max/min aren't invertible like sum is (no subtraction trick).

---

## 9. Summary Table

| Technique | Build | Query / Effect |
|---|---|---|
| 2D Prefix Sum | `pre[i][j] = arr[i][j] + pre[i-1][j] + pre[i][j-1] - pre[i-1][j-1]` | `pre[D][R] - pre[D][L-1] - pre[U-1][R] + pre[U-1][L-1]` = sum of rectangle `[U,L]`→`[D,R]` |
| 2D Partial Sum (Diff Array) | 4-corner update: `update[U][L]++; update[D+1][L]--; update[U][R+1]--; update[D+1][R+1]++;` then 2D prefix sum on `update` | Adds `val` to every cell in rectangle `[U,L]`→`[D,R]` in O(1) per update, final grid in O(n·m) |

---
