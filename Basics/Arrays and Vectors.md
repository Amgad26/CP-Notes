# Arrays & Vectors — Essential Methods

> [!info] Core idea Most `<algorithm>` functions (`sort`, `reverse`, `min_element`, etc.) work identically on **both** arrays and vectors, since they operate on iterator/pointer ranges `[first, last)`. The difference is in the **container itself** — arrays have fixed size and no member functions; vectors are resizable and have built-in methods like `.push_back()`, `.size()`, `.resize()`.

## Declaration & Sizing

```cpp
// --- ARRAY (fixed size, size = n must be known/constant) ---
int arr[10];                  // uninitialized (garbage values)
int arr2[10] = {0};           // all zeros
int arr3[10];
fill(arr3, arr3 + 10, -1);    // fill with -1 (no direct constructor like vector)
int n = 10;
int arr4[n];                  // VLA — works in GCC, NOT standard C++ (avoid in real code)

// --- VECTOR (resizable) ---
int n = 10, m = 5;
vector<int> v(n);             // size n, default 0
vector<int> v2(n, -1);        // size n, all -1
vector<vector<int>> grid(n, vector<int>(m, 0)); // n x m grid of 0s
vector<int> v4;
v4.resize(n);                 // resize an existing (possibly empty) vector

// reading n elements
int arrIn[n];
for (int i = 0; i < n; i++) cin >> arrIn[i];

vector<int> vIn(n);
for (int i = 0; i < n; i++) cin >> vIn[i];
```

|Goal|Array|Vector|
|---|---|---|
|Fixed size at compile time|`int arr[10];`|not needed|
|Size n, all zero|`int arr[10] = {0};`|`vector<int> v(n);`|
|Size n, all value x|`fill(arr, arr+n, x);`|`vector<int> v(n, x);`|
|Get length|`sizeof(arr)/sizeof(arr[0])` ⚠️ only if size known at compile time|`v.size()` ✅ always works|
|Resize later|❌ not possible|`v.resize(newSize);`|

## Shared `<algorithm>` Functions (work on both)

```cpp
int arr[] = {5, 3, 8, 1, 9, 2};
int n = 6;
vector<int> v = {5, 3, 8, 1, 9, 2};
```

### Sort

```cpp
sort(arr, arr + n);                         // array: ascending
sort(v.begin(), v.end());                   // vector: ascending
sort(arr, arr + 5);                         // array: only first 5 elements
sort(v.begin(), v.begin() + 5);             // vector: only first 5 elements
sort(arr + 2, arr + 7);                     // array: subrange [2,7)
sort(v.begin() + 2, v.begin() + 7);         // vector: subrange [2,7)
sort(arr, arr + n, greater<int>());         // descending
sort(v.rbegin(), v.rend());                 // descending (vector-only trick)
  
// Custom comparator (lambda) - sort by absolute value ascending
sort(arr, arr + n, [](int a, int b) {
    return abs(a) < abs(b);
});

// Sort descending using a lambda
sort(v.begin(), v.end(), [](int a, int b) {
    return a > b;
});

// Sort pairs by second element, then first element as tiebreaker
sort(v.begin(), v.end(), [](pair<int,int> a, pair<int,int> b) {
    if (a.second != b.second) return a.second < b.second;
    return a.first < b.first;
});

// Sort strings by length, shortest first
sort(v.begin(), v.end(), [](const string &a, const string &b) {
    return a.size() < b.size();
});

// Sort indices of an array based on the array's values (common CP trick)
vector<int> idx(n);
iota(idx.begin(), idx.end(), 0);
sort(idx.begin(), idx.end(), [&](int i, int j) {
    return arr[i] < arr[j];
});

// Using a named comparator function instead of a lambda
bool cmp(int a, int b) {
    return a > b; // descending
}
sort(arr, arr + n, cmp);

// Using a functor (struct with operator())
struct Cmp {
    bool operator()(int a, int b) const {
        return a < b;
    }
};
sort(v.begin(), v.end(), Cmp());
```

### Reverse

```cpp
reverse(arr, arr + n);
reverse(v.begin(), v.end());
```

### min / max element

> [!warning] Returns a pointer/iterator — dereference with `*`

```cpp
"first occurrence"
int mnA = *min_element(arr, arr + n);
int mxA = *max_element(arr, arr + n);
int mnV = *min_element(v.begin(), v.end());
int mxV = *max_element(v.begin(), v.end());
```

### argmin / argmax (index of min/max)

```cpp
"first occurrence"
int argmaxA = max_element(arr, arr + n) - arr;
int argminA = min_element(arr, arr + n) - arr;
int argmaxV = max_element(v.begin(), v.end()) - v.begin();
int argminV = min_element(v.begin(), v.end()) - v.begin();

"last occurrence"
int argmaxA = n - 1 - (max_element(reverse_iterator(arr + n), reverse_iterator(arr)) - reverse_iterator(arr + n));
int argminA = n - 1 - (min_element(reverse_iterator(arr + n), reverse_iterator(arr)) - reverse_iterator(arr + n));
int argmaxV = v.size() - 1 - (max_element(v.rbegin(), v.rend()) - v.rbegin());
int argminV = v.size() - 1 - (min_element(v.rbegin(), v.rend()) - v.rbegin());
```

### Accumulate (sum)

```cpp
long long sumA = accumulate(arr, arr + n, 0LL);
long long sumV = accumulate(v.begin(), v.end(), 0LL);
```

### Find

```cpp
auto itA = find(arr, arr + n, 8);
if (itA != arr + n) cout << "found at index " << itA - arr << endl;

auto itV = find(v.begin(), v.end(), 8);
if (itV != v.end()) cout << "found at index " << itV - v.begin() << endl;
```

### Count

```cpp
int cA = count(arr, arr + n, 3);
int cV = count(v.begin(), v.end(), 3);
```

### Fill

```cpp
fill(arr, arr + n, 0);
fill(v.begin(), v.end(), 0);
```

### Unique

> [!note] Needs a **sorted** range first; only removes _consecutive_ duplicates

```cpp
sort(arr, arr + n);
int newLen = unique(arr, arr + n) - arr;      // array: shrinks logically, track new length yourself

sort(v.begin(), v.end());
v.erase(unique(v.begin(), v.end()), v.end()); // vector: actually shrinks the container
```

### Binary search

> [!note] Needs a sorted range

```cpp
bool foundA = binary_search(arr, arr + n, 8);
bool foundV = binary_search(v.begin(), v.end(), 8);
```

### Iterate

```cpp
for (int x : arr) cout << x << " ";   // only if arr's true size is known (not a decayed pointer)
for (int x : v) cout << x << " ";
```

## Vector-Only Methods (no array equivalent)

```cpp
vector<int> v = {5, 3, 8, 1, 9, 2};

v.push_back(100);        // append element — arrays can't grow
v.pop_back();              // remove last element
v.size();                  // current element count — arrays need sizeof trick (unreliable)
v.empty();                 // true if size == 0
v.insert(v.begin() + 1, 42);  // insert 42 at index 1 — shifts everything after
v.erase(v.begin() + 2);    // remove element at index 2 — shifts everything after
v.clear();                 // remove all elements, size becomes 0
v.resize(10);              // grow/shrink, pad new slots with 0
v.resize(10, -1);          // grow/shrink, pad new slots with -1

vector<int> v2 = {1, 2, 3};
v.swap(v2);                 // swap two whole vectors in O(1) — arrays need manual element-by-element swap
swap(v[0], v[1]);           // swap two elements — this one IS array-compatible too
```

## Quick Reference Table

|Method|Array|Vector|
|---|:-:|:-:|
|`sort`, `reverse`, `min_element`, `max_element`, `find`, `count`, `accumulate`, `fill`, `binary_search`, `unique`|✅ (via pointers)|✅ (via iterators)|
|`swap(a[i], a[j])` (single elements)|✅|✅|
|`.size()`|❌|✅|
|`.push_back()` / `.pop_back()`|❌|✅|
|`.resize()`|❌|✅|
|`.insert()` / `.erase()` (single elements)|❌|✅|
|`.clear()`|❌|✅|
|`.swap()` (whole container)|❌|✅|
|Range-based `for(x : container)`|✅ (real array only)|✅ always|

> [!tip] Rule of thumb for CP Use **arrays** when the size is fixed/known upfront and you want raw speed with zero overhead. Use **vectors** when you need dynamic sizing, `.push_back()`, or convenient built-in methods — vectors are almost always the safer default unless you're chasing micro-optimizations.

---