
# STL — Set, Multiset, Ordered Set, Unordered Set

> [!info] Session agenda
> 1. **Set** (set, multiset, ordered set, unordered set)
> 2. **Map** → see [[STL - Map]]
>
> Notes marked **Added** are extras I put in that are not in the slides.

---

## 0. Why do we need a set? (Easy Challenge)

> Read `n` numbers. After reading each number, insert it into the array and print the **first number greater than it**, or `-1` if there is none.

With a **vector** you pick one of two bad options:

| Option | insert | search |
|---|---|---|
| unsorted push_back | O(1) | O(n) |
| keep it sorted | O(n) | O(log n) with binary search |

A **set** keeps elements sorted and does both in **O(log n)**.

> [!tip] Added — solution
> ```cpp
> set<int> s;
> while (n--) {
>     int x; cin >> x;
>     s.insert(x);
>     auto it = s.upper_bound(x);          // first element > x
>     cout << (it == s.end() ? -1 : *it) << "\n";
> }
> ```

---

## 1. Set basics

```cpp
#include <set>
set<Data_Type> Set_Name;
set<int> s;
```

A set is **sorted** and holds **unique** elements (duplicates are ignored). Internally it is a balanced binary search tree (red-black tree).

### insert vs emplace
```cpp
set<int> s;
s.insert(5);
s.insert(4);
s.insert(40);
s.insert(90);
s.insert(4);        // duplicate, ignored
// s => 4 5 40 90
```
`emplace(x)` works the same way (it constructs the element in place).

> [!tip] Added
> `insert` and `emplace` return `pair<iterator,bool>`. The `bool` is `false` if the element was already there.

---

## 2. Time complexity cheat sheet

| Function | Complexity |
|---|---|
| `insert()` | O(log n) |
| `emplace()` | O(log n) |
| `erase()` | O(log n) |
| `size()` | **O(1)** |
| `lower_bound()` | O(log n) |
| `upper_bound()` | O(log n) |
| `count()` | O(log n) |
| `find()` | O(log n) |

---

## 3. Min / max and iterators

Given `2 5 7 10`:

```
s.begin()  ->  points to min (2)
s.rbegin() ->  points to max (10)
```

| Expression | Meaning |
|---|---|
| `s.begin()` | **iterator** to the min element |
| `*s.begin()` | the **value** of the min element |
| `s.rbegin()` | **iterator** to the max element (reverse iterator) |
| `*s.rbegin()` | the **value** of the max element |
| `s.end()` | **iterator** one past the last element (does not point to a real element) |

> [!warning] Never dereference `s.end()`
> Always check `it != s.end()` before using `*it`.

> [!tip] Added
> The max can also be written `*prev(s.end())`. Calling `*s.begin()` on an empty set is undefined behaviour, so check `!s.empty()` first.

---

## 4. Looping

```cpp
// iterator loop      (it => iterator)
for (auto it = st.begin(); it != st.end(); it++)
    cout << *it << endl;

// range-based loop   (element => the data type, e.g. int, string)
for (auto element : st)
    cout << element << endl;
```

---

## 5. Accessing the element at index 3

Sets have **no random access** (`st[3]` does not exist).

```cpp
// 1) loop until the target element        -> O(n)
int index = 3;
auto it = st.begin();
for (int i = 0; i < index; i++)
    it++;

// 2) next() function                       -> O(n)
auto it = next(st.begin(), 3);
```
Both are **O(n)**. If you need fast index access, use the **ordered set** (section 9).

---

## 6. erase

```cpp
s.erase(7);          // erase by value     (2 5 7 9 10 -> 2 5 9 10)
s.erase(iterator);   // erase by iterator  (this is faster, no search)
s.erase(it1, it2);   // erase a range
```
Range erase: the first iterator points to the **start** of the range, the second points **one past the end** of the range, i.e. `[it1, it2)`.

---

## 7. find, count, lower_bound, upper_bound

Example set: `set<int> s = {1, 3, 20, 8, 1, 5};` → stored as `1 3 5 8 20`.

| Call | Returns |
|---|---|
| `s.find(x)` | iterator to `x` if it exists, else `s.end()` |
| `s.count(x)` | `1` if it exists, else `0` |
| `s.lower_bound(x)` | iterator to the first element **≥ x**, else `s.end()` |
| `s.upper_bound(x)` | iterator to the first element **> x**, else `s.end()` |

```cpp
auto it = s.find(3);         // points to 3
auto c  = s.count(3);        // 1
auto lb = s.lower_bound(3);  // points to 3
auto ub = s.upper_bound(3);  // points to 5
```

> [!tip] Added — memory trick
> **lower** = "can be equal" (≥), **upper** = "strictly after" (>).
> Largest element **≤ x**: `auto it = s.upper_bound(x); if (it != s.begin()) --it;`

> [!tip] Added — don't use `std::lower_bound(s.begin(), s.end(), x)`
> It is O(n) on a set because set iterators are not random access. Always use the member function `s.lower_bound(x)`.

---

## 8. Multiset

Same as set, but **duplicates are allowed** (the difference is *not unique*).

```cpp
multiset<int> s = {2, 9, 5, 30, 10, 2, 30};
for (auto it : s) cout << it << " ";
// 2 2 5 9 10 30 30
```

Same functions and same complexity as set, but two behave differently:

| | `multiset` | `set` |
|---|---|---|
| `count(x)` | number of occurrences of `x` | 1 if exists, 0 otherwise |
| `erase(x)` | **deletes all** occurrences of `x` | deletes `x` if it exists |

```cpp
s.erase(element);    // removes ALL occurrences of the element
s.erase(iterator);   // removes ONE occurrence
```

> [!tip] Added
> To remove just one copy by value: `s.erase(s.find(x));` (check `find(x) != s.end()` first).

**Related:** `priority_queue` can be replaced by a `multiset` when you also need to delete arbitrary elements or read both min and max.

### Custom comparator
A custom comparator changes the sorting rule of a set or multiset. The default is `less<>` (lowest to highest).

```cpp
struct AbsoluteValueComparator {
    bool operator()(int a, int b) const {
        return abs(a) < abs(b);
    }
};

set<int, AbsoluteValueComparator> mySet;
mySet.insert(-5); mySet.insert(3); mySet.insert(-8); mySet.insert(2);
for (auto element : mySet) cout << element << " ";
// 2 3 -5 -8
```

> [!tip] Added
> Descending order without writing a struct: `set<int, greater<int>> s;`

---

## 9. Ordered Set (policy-based data structure)

A normal set cannot answer "how many elements are smaller than x?" or "what is the element at index k?" faster than O(n). The **ordered set** can, in O(log n).

| Function | What it does | Complexity |
|---|---|---|
| `order_of_key(x)` | number of elements **smaller than x** | O(log n) |
| `find_by_order(k)` | **iterator** to the element at index `k` (0-based) | O(log n) |

### Template
```cpp
#include <bits/stdc++.h>
#include <ext/pb_ds/assoc_container.hpp>
#include <ext/pb_ds/tree_policy.hpp>
using namespace __gnu_pbds;
using namespace std;

template <class T>
using ordered_set = tree<T, null_type, less<T>, rb_tree_tag,
                         tree_order_statistics_node_update>;

int main() {
    ordered_set<int> os;
    os.insert(7); os.insert(5); os.insert(9); os.insert(1);
    for (auto val : os) cout << val << ' ';      // 1 5 7 9
    cout << os.order_of_key(9) << '\n';          // 3
    os.erase(7);
    cout << os.order_of_key(9) << '\n';          // 2
    int second_element = *os.find_by_order(1);   // 5
    cout << second_element << '\n';
}
```

| Sort order | Comparator |
|---|---|
| Ascending | `less<T>` |
| Descending | `greater<T>` |

### The problem: set vs vector vs ordered set

> Start with an empty set and process Q queries:
> 1. insert X  2. remove X  3. print the number of elements smaller than X  4. print the element at index X

| Operation | vector | set | ordered set |
|---|---|---|---|
| 1. insert x | O(N) (keep sorted) | O(log N) | O(log N) |
| 2. remove x | O(N) | O(log N) | O(log N) |
| 3. count smaller than x | O(log N) `lower_bound` | O(N) | **O(log N)** `order_of_key` |
| 4. element at index x | O(1) | O(N), no index access | **O(log N)** `find_by_order` |

The ordered set is the only one where **all four** are O(log N).

---

## 10. Ordered Multiset

Replace `less<T>` with `less_equal<T>` so equal keys are kept.

```cpp
template <class T>
using ordered_set = tree<T, null_type, less_equal<T>, rb_tree_tag,
                         tree_order_statistics_node_update>;

ordered_set<int> os;
os.insert(5); os.insert(9); os.insert(5);
for (auto &val : os) cout << val << " ";    // 5 5 9
```

| Sort order | Comparator |
|---|---|
| Ascending | `less_equal<T>` |
| Descending | `greater_equal<T>` |

> [!bug] Problems with the ordered multiset
> - `os.find()` **can't find elements**; it always returns `os.end()`.
> - `os.erase(value)` **has no effect**.
> - `os.lower_bound()` and `os.upper_bound()` are **swapped**: `lower_bound` returns `> x`, `upper_bound` returns `≥ x`.

> [!tip] Added — safe workaround
> Insert `pair<int,int>` as `{value, unique_id}` into a normal `less<>` ordered set. Every key is then unique, and `find`, `erase`, `lower_bound` and `order_of_key` all behave correctly.
> ```cpp
> ordered_set<pair<int,int>> os;
> os.insert({x, timer++});
> int smaller = os.order_of_key({x, -1});   // count of values < x
> ```

---

## 11. Unordered Set / Unordered Multiset

Same idea, but **not sorted** (hash table).

```cpp
#include <unordered_set>
unordered_set<int> us;
```

| | `set` | `unordered_set` |
|---|---|---|
| Order | sorted | **not sorted** |
| insert / find / erase | O(log n) | O(1) average |
| `lower_bound` / `upper_bound` | yes | **no** |

> [!tip] Added
> Worst case for `unordered_*` is O(n) per operation. On Codeforces, anti-hash tests can hit this. Add a custom hash or use a sorted `set`/`map` when hacks are likely.

---

## 12. Practice problems from the slides

### Easy Challenge
Solved in section 0.

### Just Do It (Codeforces gym)
> Empty set, `q` queries (`q ≤ 10^5`):
> - `insert x`
> - `lower_bound x`: first element ≥ x, or `-1`
> - `upper_bound x`: first element > x, or `-1`
> - `find x`: print `found` / `not found`

Direct use of `insert`, `lower_bound`, `upper_bound`, `find`.

### Concert Tickets (CSES)
> `n` tickets with prices, `m` customers each give a max price. Each takes the **closest price that does not exceed** their max, and that ticket is removed. Print the price, or `-1`.

> [!tip] Added — solution
> Use a **multiset** (prices can repeat).
> ```cpp
> multiset<int> s(h.begin(), h.end());
> for (int t : customers) {
>     auto it = s.upper_bound(t);              // first price > t
>     if (it == s.begin()) { cout << -1 << "\n"; continue; }
>     --it;                                    // largest price <= t
>     cout << *it << "\n";
>     s.erase(it);                             // erase ONE copy (by iterator)
> }
> ```

### Cellular Network (Codeforces 702C)
> `n` cities and `m` towers on a line. Find the minimal `r` so every city is within distance `r` of some tower.

> [!tip] Added — solution
> For each city, find the nearest tower with `lower_bound`. Check the tower at the iterator and the one before it. The answer is the **max** of those nearest distances.
> ```cpp
> set<int> towers(b.begin(), b.end());
> int ans = 0;
> for (int city : a) {
>     auto it = towers.lower_bound(city);
>     int best = INT_MAX;
>     if (it != towers.end())   best = min(best, *it - city);
>     if (it != towers.begin()) best = min(best, city - *prev(it));
>     ans = max(ans, best);
> }
> ```

### Permutation inversions (ordered set)
> Given a permutation `p`, for each `i` print the number of `j < i` with `p[j] > p[i]`.

> [!tip] Added — solution
> ```cpp
> ordered_set<int> os;
> for (int i = 0; i < n; i++) {
>     cout << i - os.order_of_key(p[i]) << " ";   // i inserted so far, minus those smaller
>     os.insert(p[i]);
> }
> ```

### Aliens in the room (slides, multiset / set)
> Aliens enter one by one. Alien `(A, S)` kills every alien in the room whose age is in `[A, A+S]`. Print the ages of the survivors.

> [!tip] Added — idea
> Keep ages in a set. When `(A, S)` arrives: `it = lower_bound(A)`, then erase while `it != end && *it <= A + S`. Then insert `A`. Each alien is inserted and erased at most once, so the total is O(n log n).
> If ages can repeat, use `multiset`. If the output must follow entry order, store `age → entry index` in a map and sort by index at the end.

### Cups (ordered set)
> `N` cups with capacity `C[i]`. Queries: `1 i X` add X liters (ignore if it overflows), `2 i X` remove X liters (ignore if not enough), `3 K` print how many cups hold at least `K` liters.

> [!tip] Added — idea
> Keep `amount[i]` plus an `ordered_set<pair<int,int>>` of `{amount, i}`. For an update, erase the old pair and insert the new one. For query 3, the answer is `N - os.order_of_key({K, -1})`.

---

## 13. Quick decision table

| I need... | Use |
|---|---|
| Unique + sorted, fast lookup | `set` |
| Duplicates + sorted | `multiset` |
| Count smaller than x / k-th element | `ordered_set` |
| Only membership, no order | `unordered_set` |
| Key → value | [[STL - Map]] |
