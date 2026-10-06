
# STL — Map and Unordered Map

> [!info]
> `map` is a set of **keys**, where each key has an attached **value**. Most of it works like [[STL - Set]]. Notes marked **Added** are extras that are not in the slides.

---

## 1. Why map?

A map is:
1. **Sorted** (by key)
2. **Unique** (each key appears once)
3. **Fast** (O(log n))
4. Lets you **control the data type of the key** (`int`, `char`, `string`, `pair`, ...)

Typical use: counting frequencies of numbers, chars or strings.

```cpp
#include <map>
map<key, value> Map_Name;
map<int, int> m;
```

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
| **key access** `m[key]` | O(log n) |

---

## 3. insert / emplace

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    map<char, int> mp;
    mp.insert(make_pair('b', 24));
    mp.emplace('a', 25);
    for (auto it = mp.begin(); it != mp.end(); ++it)
        cout << " " << (*it).first << " " << (*it).second << endl;
    // a 25
    // b 24
}
```

Each element is a `pair<key, value>`:
- `it->first` (or `(*it).first`) is the **key**
- `it->second` (or `(*it).second`) is the **value**

> [!tip] Added
> `insert` does **not** overwrite if the key already exists. Using `m[key] = v` does overwrite.
> Structured bindings: `for (auto &[k, v] : mp) cout << k << " " << v;`

---

## 4. Key access with `[]`

```cpp
map<char, int> mp;
mp['a'] = 90;   // key does not exist => insert key & assign value = 90
mp['b'] = 80;   // key does not exist => insert key & assign value = 80
mp['b'] = 3;    // key exists         => assign the new value
mp['c']++;      // key does not exist => insert key with value = 0, then add 1 to value
```

> [!warning] `m[key]` creates the key if it is missing
> Reading `m[x]` for a missing key **inserts** it with value `0` (or the default value). That changes `size()` and can add junk keys. To only check, use `find` or `count`.

---

## 5. erase

```cpp
m.erase(key);          // erase by key
m.erase(iterator);     // erase by iterator (this is faster)
m.erase(it1, it2);     // erase a range [it1, it2)
```
For the range version, the first iterator points to the **start** of the range and the second points **one past the end**.

### Example from the slides
```cpp
map<string,int> mp;
mp["Samer"] = 1;
mp["Ahmed"]  = 2;
mp["Saher"]  = 3;
for (auto it : mp) cout << it.first << " " << it.second << "\n";
/*
 Ahmed 2
 Saher 3
 Samer 1
*/

mp.erase("Saher");
for (auto it : mp) cout << it.first << " " << it.second << "\n";
/*
 Ahmed 2
 Samer 1
*/
```
The keys print **sorted** (alphabetical for strings), not in insertion order.

### Erasing a range
```cpp
map<string,int> mp;
mp["Samer"] = 1;  mp["Ahmed"] = 2;  mp["Saher"] = 3;  mp["Hossam"] = 3;
// sorted: Ahmed 2, Hossam 3, Saher 3, Samer 1

auto iter = mp.find("Hossam");
mp.erase(iter, mp.end());      // erase from "Hossam" to the end
// left: Ahmed 2
```

---

## 6. find, count, lower_bound, upper_bound

| Call | Returns |
|---|---|
| `m.find(key)` | iterator to the element if it exists, else `m.end()` |
| `m.count(key)` | `true` / `1` if the key exists, else `false` / `0` |
| `m.lower_bound(key)` | iterator to the first element with key **≥ key**, else `m.end()` |
| `m.upper_bound(key)` | iterator to the first element with key **> key**, else `m.end()` |

> [!tip] Added
> To read a value **without** inserting a key:
> ```cpp
> auto it = m.find(k);
> if (it != m.end()) cout << it->second;
> ```
> `m.at(k)` throws an exception if the key is missing.

---

## 7. Unordered Map

Same idea as `map`, but the keys are **not sorted** (hash table).

```cpp
#include <unordered_map>
unordered_map<string, int> um;
```

| | `map` | `unordered_map` |
|---|---|---|
| Key order | sorted | **not sorted** |
| Access / insert / erase | O(log n) | O(1) average |
| `lower_bound` / `upper_bound` | yes | **no** |
| Key types | anything with `<` | anything hashable (`pair` needs a custom hash) |

> [!tip] Added
> Worst case is O(n) per operation, and anti-hash tests on Codeforces can trigger it. If you need sorted keys, range queries or `lower_bound`, use `map`.
> `unordered_map` is slightly faster in most cases, but can be slower for small `n` because of the hash overhead.

---

## 8. Problems from the slides

### Frequency of elements
> Output the frequency of each element: **numbers**, **chars**, **strings**.

```cpp
map<string,int> freq;
for (auto &s : arr) freq[s]++;
for (auto &[key, cnt] : freq)
    cout << key << " " << cnt << "\n";
```
The same code works for `map<int,int>` and `map<char,int>`.

### Registration system (Codeforces 4C)
> On each request with a `name`: if it is new, print `OK` and store it. If it already exists, print `name1`, `name2`, ... using the smallest number not yet used.

> [!tip] Added — solution
> Store how many times each name was requested.
> ```cpp
> unordered_map<string,int> cnt;
> while (n--) {
>     string s; cin >> s;
>     if (cnt[s] == 0) cout << "OK\n";
>     else              cout << s << cnt[s] << "\n";
>     cnt[s]++;
> }
> ```
> Each request is O(1) average, so the total is O(n).

---

## 9. Common patterns

> [!tip] Added
> | Task | Code |
> |---|---|
> | Count occurrences | `m[x]++` |
> | Is key present | `m.count(x)` |
> | Min key / max key | `m.begin()->first` / `m.rbegin()->first` |
> | Remove when value hits 0 | `if (--m[x] == 0) m.erase(x);` |
> | Group indices by value | `map<int, vector<int>>` |
> | Descending keys | `map<int, int, greater<int>>` |
> | Pair as key | `map<pair<int,int>, int>` |

---

## 10. Set vs Map summary

| | [[STL - Set]] | Map |
|---|---|---|
| Stores | keys only | key → value |
| Sorted | yes | yes (by key) |
| Unique | yes | yes (by key) |
| Element type | the key itself | `pair<const Key, Value>` |
| Hash version | `unordered_set` | `unordered_map` |
