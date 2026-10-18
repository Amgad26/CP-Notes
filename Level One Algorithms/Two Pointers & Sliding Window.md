# Two Pointers & Sliding Window

> [!abstract] Big idea
> Both techniques use **two indices** to walk over an array/string so we avoid checking every pair or subarray.
> Typical improvement: **O(n²) → O(n)** (or O(n log n) if sorting is needed first).

## Table of Contents
- [[#1. Two Pointers]]
- [[#2. Sliding Window]]
- [[#3. Two Pointers vs Sliding Window]]
- [[#4. Common Mistakes]]

---

# 1. Two Pointers

## 1.1 Definition

Two Pointers is a technique where **two indices (pointers)** traverse a data structure, often **from different directions** or **at different speeds**. It solves problems on **subarrays, substrings, or pairs** efficiently.

## 1.2 The Main Patterns

| Pattern | Start positions | Movement | Typical use |
|---|---|---|---|
| **Opposite ends** | `l = 0`, `r = n-1` | move towards each other | palindrome, pair sum in sorted array, squares of sorted array |
| **Same direction** (fast/slow, reader/writer) | `l = 0`, `r = 0` | both move right, `r` leads | remove duplicates, partition, merge, window |
| **Two arrays** | `i` on A, `j` on B | each moves on its own array | merge two sorted arrays, intersection, subsequence check |

> [!tip] When should I think "two pointers"?
> - The array is **sorted** (or you can sort it without breaking the problem).
> - You need a **pair / triplet** satisfying a condition.
> - You need a **contiguous segment** with some property.
> - Moving a pointer has a **clear effect** (the sum gets bigger or smaller, the window gets valid or invalid).

## 1.3 Pattern A: Opposite Ends

### Example 1: Check Palindrome

Compare the first and last characters, then move inward.

```
A B B C B B A
^           ^     A == A  -> move in
  ^       ^       B == B  -> move in
    ^   ^         B == B  -> move in
      ^           l == r  -> done, it IS a palindrome
```

```cpp
bool isPalindrome(const string& s) {
    int l = 0, r = (int)s.size() - 1;
    while (l < r) {
        if (s[l] != s[r]) return false;   // mismatch -> not a palindrome
        l++, r--;
    }
    return true;
}
```

- **Time:** O(n)
- **Space:** O(1)

### Example 2: Two Sum II (LeetCode 167): sorted array

Given a **sorted** array, find two numbers that add up to `target`.

**Why it works:** the array is sorted, so
- `sum < target` → we need a bigger sum → move `l` right (take a bigger small value)
- `sum > target` → we need a smaller sum → move `r` left (take a smaller big value)

```cpp
vector<int> twoSum(vector<int>& a, int target) {
    int l = 0, r = (int)a.size() - 1;
    while (l < r) {
        int sum = a[l] + a[r];
        if (sum == target) return {l + 1, r + 1};   // 1-indexed answer
        else if (sum < target) l++;
        else r--;
    }
    return {};
}
```

> [!note] Why we never miss the answer
> When `a[l] + a[r] < target`, `a[l]` can't pair with *any* element at or before `r` (they're all ≤ `a[r]`), so `l` can be safely discarded. Same logic for `r` in the other case.

### Example 3: Squares of a Sorted Array (LeetCode 977)

Input is sorted but has **negatives**, so squares are not sorted. The largest square is always at **one of the two ends**.

```
nums = [-4, -1, 0, 3, 10]
         ^             ^        |-4|=4 < |10|=10  -> put 100 at the END
         ^         ^            |-4|=4 > |3|=3    -> put 16 next
...
result = [0, 1, 9, 16, 100]
```

```cpp
vector<int> sortedSquares(vector<int>& a) {
    int n = a.size();
    vector<int> res(n);
    int l = 0, r = n - 1;
    for (int pos = n - 1; pos >= 0; pos--) {   // fill from the back
        if (abs(a[l]) > abs(a[r])) {
            res[pos] = a[l] * a[l];
            l++;
        } else {
            res[pos] = a[r] * a[r];
            r--;
        }
    }
    return res;
}
```

- **Time:** O(n) (instead of O(n log n) for square + sort)
- **Space:** O(n) for output only

## 1.4 Pattern B: Same Direction

### Example 4: Remove duplicates from a sorted array (in place)

`slow` = where the next unique value goes, `fast` = scanner.

```cpp
int removeDuplicates(vector<int>& a) {
    if (a.empty()) return 0;
    int slow = 0;
    for (int fast = 1; fast < (int)a.size(); fast++) {
        if (a[fast] != a[slow]) {
            slow++;
            a[slow] = a[fast];
        }
    }
    return slow + 1;   // new length
}
```

## 1.5 Pattern C: Two Arrays

### Example 5: Merge two sorted arrays

```cpp
vector<int> mergeSorted(const vector<int>& A, const vector<int>& B) {
    vector<int> res;
    int i = 0, j = 0;
    while (i < (int)A.size() && j < (int)B.size()) {
        if (A[i] <= B[j]) res.push_back(A[i++]);
        else              res.push_back(B[j++]);
    }
    while (i < (int)A.size()) res.push_back(A[i++]);
    while (j < (int)B.size()) res.push_back(B[j++]);
    return res;
}
```

### Example 6: Is `s` a subsequence of `t`?

```cpp
bool isSubsequence(const string& s, const string& t) {
    int i = 0;
    for (int j = 0; j < (int)t.size() && i < (int)s.size(); j++)
        if (s[i] == t[j]) i++;
    return i == (int)s.size();
}
```

## 1.6 Extension: 3Sum (fix one, two-pointer the rest)

Sort, fix `i`, then run the opposite-ends two-pointer on the rest → **O(n²)**.

```cpp
vector<vector<int>> threeSum(vector<int>& a) {
    sort(a.begin(), a.end());
    int n = a.size();
    vector<vector<int>> res;
    for (int i = 0; i < n - 2; i++) {
        if (i > 0 && a[i] == a[i - 1]) continue;       // skip duplicate i
        int l = i + 1, r = n - 1;
        while (l < r) {
            int s = a[i] + a[l] + a[r];
            if (s == 0) {
                res.push_back({a[i], a[l], a[r]});
                l++, r--;
                while (l < r && a[l] == a[l - 1]) l++;   // skip duplicates
                while (l < r && a[r] == a[r + 1]) r--;
            } else if (s < 0) l++;
            else r--;
        }
    }
    return res;
}
```

## 1.7 Two Pointers Template Cheat Sheet

```cpp
// opposite ends
int l = 0, r = n - 1;
while (l < r) {
    // evaluate a[l], a[r]
    // move l++ or r-- (or both) depending on the condition
}

// same direction
int j = 0;
for (int i = 0; i < n; i++) {
    while (j < n && /* can extend */) j++;
    // use [i, j)
    // then i moves forward in the for loop
}
```

---

# 2. Sliding Window

## 2.1 Definition

A **sliding window** is a contiguous segment `[l, r]` of the array. Instead of recomputing everything for each segment, we **slide** it: add the new element on the right, remove the old element on the left, and **update the answer incrementally**.

It is a special case of two pointers where both pointers move **only forward** (`l ≤ r`).

> [!tip] When should I think "sliding window"?
> - The problem asks about **contiguous subarrays / substrings**.
> - Keywords: *longest*, *shortest*, *maximum sum of size k*, *at most k distinct*, *no more than*, *count subarrays*.
> - The condition is **monotonic**: if a window is valid, shrinking it keeps it valid (or the opposite for "at least" problems).

## 2.2 Two Types

| | **Fixed-size window** | **Variable-size window** |
|---|---|---|
| Size | always `k` | grows and shrinks |
| Move | add `a[i]`, remove `a[i-k]` | `r` expands, `l` shrinks while invalid |
| Question | "best/any window of size k" | "longest/shortest/count windows satisfying a condition" |

## 2.3 Fixed Sliding Window

### Template

```cpp
// build the first window [0, k-1]
for (int i = 0; i < k; i++) add(a[i]);
update_answer();

// slide
for (int i = k; i < n; i++) {
    add(a[i]);        // new element enters on the right
    remove(a[i - k]); // old element leaves on the left
    update_answer();
}
```

### Example 1: Maximum sum of a subarray of size k

```cpp
long long maxSumK(const vector<int>& a, int k) {
    int n = a.size();
    long long sum = 0, best = LLONG_MIN;
    for (int i = 0; i < n; i++) {
        sum += a[i];
        if (i >= k) sum -= a[i - k];             // drop the element that left
        if (i >= k - 1) best = max(best, sum);   // window is full
    }
    return best;
}
```

### Example 2: Contains Duplicate II (LeetCode 219)

> Return `true` if there are `i != j` with `nums[i] == nums[j]` and `|i - j| <= k`.

Keep a **set of the last `k` elements** (window of size `k`). If the new element is already inside, we found a close duplicate.

```cpp
bool containsNearbyDuplicate(vector<int>& nums, int k) {
    unordered_set<int> window;
    for (int i = 0; i < (int)nums.size(); i++) {
        if (window.count(nums[i])) return true;
        window.insert(nums[i]);
        if (i >= k) window.erase(nums[i - k]);   // keep window size <= k
    }
    return false;
}
```

```
nums = [1,2,3,1], k = 3
i=0 {1}  i=1 {1,2}  i=2 {1,2,3}  i=3 -> 1 is in set -> true
```

- **Time:** O(n) average
- **Space:** O(k)

## 2.4 Variable Sliding Window

### Template (the one to memorize)

```cpp
int l = 0;
for (int r = 0; r < n; r++) {
    add(a[r]);                    // 1) expand: include a[r]

    while (/* window is INVALID */) {
        remove(a[l]);             // 2) shrink until valid again
        l++;
    }

    // 3) now [l, r] is the longest valid window ending at r
    ans = max(ans, r - l + 1);    //    (longest)  OR
    // cnt += r - l + 1;          //    (count of valid subarrays ending at r)
}
```

> [!important] Counting trick
> If **every subarray of a valid window is also valid**, then the number of valid subarrays **ending at `r`** is exactly `r - l + 1`. Sum that over all `r`.

### Example 3: Balanced Team (Codeforces 1133C)

> Pick the largest team such that any two skills differ by **at most 5**.

**Idea:** sort the skills. A team is a contiguous block in sorted order, and it is balanced when `a[r] - a[l] <= 5` (max minus min). Slide the window.

```cpp
void solve() {
    int n; cin >> n;
    vector<int> a(n);
    for (auto& x : a) cin >> x;
    sort(a.begin(), a.end());

    int l = 0, ans = 0;
    for (int r = 0; r < n; r++) {
        while (a[r] - a[l] > 5) l++;      // window invalid -> shrink
        ans = max(ans, r - l + 1);
    }
    cout << ans << "\n";
}
```

```
1 10 17 12 15 2  ->  sorted: 1 2 10 12 15 17
window [10,12,15] : 15-10 = 5 <= 5  OK  -> size 3
answer = 3
```

### Example 4: Subarray Distinct Values (CSES): count subarrays with **at most k** distinct values

**Window state:** a frequency map + number of distinct values.
**Invalid** when `distinct > k`.

```cpp
void solve() {
    int n, k; cin >> n >> k;
    vector<int> a(n);
    for (auto& x : a) cin >> x;

    map<int, int> freq;          // or unordered_map with a custom hash
    int distinct = 0, l = 0;
    long long cnt = 0;

    for (int r = 0; r < n; r++) {
        if (++freq[a[r]] == 1) distinct++;          // add a[r]

        while (distinct > k) {                       // shrink
            if (--freq[a[l]] == 0) distinct--;
            l++;
        }
        cnt += r - l + 1;                            // all windows ending at r
    }
    cout << cnt << "\n";
}
```

```
n=5, k=2, a = 1 2 3 1 1
r=0 [1]        +1
r=1 [1 2]      +2
r=2 [2 3]      +2   (1 2 3 had 3 distinct -> shrink)
r=3 [3 1]      +2
r=4 [3 1 1]    +3
total = 10  ✓
```

- **Time:** O(n log n) with `map`, O(n) average with `unordered_map`
- **Space:** O(n)

> [!tip] "Exactly k" trick
> `exactly(k) = atMost(k) - atMost(k - 1)`

## 2.5 Fixed vs Variable: Quick Summary

```
FIXED                                VARIABLE
-----------------------------------  -----------------------------------
size is given (k)                    size is what we are looking for
one move per step (in + out)         r always moves, l moves while invalid
no while loop                        while loop to shrink
```

---

# 3. Two Pointers vs Sliding Window

| | Two Pointers | Sliding Window |
|---|---|---|
| Pointers move | towards each other **or** same direction | **same direction only** |
| Works on | pairs, sorted arrays, palindromes, merging | contiguous subarrays / substrings |
| Needs sorted? | often yes (for pair problems) | not required (needed in Balanced Team only because the *set* is chosen, not a segment) |
| Window concept | not necessarily | the segment `[l, r]` **is** the state |
| Time | O(n) | O(n) (each element enters and leaves once) |

> [!note]
> Sliding window ⊂ two pointers. If both pointers go left → right and you maintain the range between them, call it a sliding window.

## Why is it O(n) even with a `while` inside the `for`?

`l` only moves forward and never exceeds `n`, `r` only moves forward and never exceeds `n`. Total moves ≤ 2n → **amortized O(n)**.

---

# 4. Common Mistakes

> [!warning] Watch out
> 1. **Forgetting to sort** when the pair-sum two pointers needs a sorted array.
> 2. **Off-by-one on window size:** `r - l + 1` (inclusive), not `r - l`.
> 3. **Fixed window:** removing `a[i-k]` when `i >= k`, and reading the answer only when `i >= k-1`.
> 4. **Using `int` for sums/counts:** the number of subarrays can reach ~n²/2 → use `long long`.
> 5. **`l > r` after shrinking:** make sure the `while` can't run past `r` (e.g. when a single element is already invalid).
> 6. **Negative numbers:** the classic "sum ≥ target, shrink" window **only works for non-negative** values. With negatives use prefix sums + map.
> 7. **Updating the frequency map in the wrong order:** decrement first, then check for zero when removing; increment first, then check for one when adding.
> 8. **`unordered_map` hacks on Codeforces:** use `map` or a custom hash if the input can be adversarial.

---
# Quick Recap

> [!success] Remember
> - **Two pointers (opposite ends):** sorted + pair condition → move the pointer that fixes the sum/comparison.
> - **Fixed window:** add new, remove old, update answer. Size never changes.
> - **Variable window:** expand `r`, shrink `l` while invalid, then update (`max` for longest, `+= r-l+1` for counting).
> - Everything is **O(n)** because each pointer only moves forward.
