
# Frequency Array

## 1. What is a Frequency Array?

A **Frequency Array** (a.k.a. counting array / hash array) is a fixed-size array used to count how many times each distinct value appears in a dataset (array, string, etc.), in **O(1)** time per lookup/update instead of using a map.

> Core idea: map each possible value to an index in a small, bounded range, then use that index to count occurrences.

**Why use it instead of `std::map` / `unordered_map`?**
- O(1) guaranteed access (no hashing overhead, no collisions)
- Very fast in practice (plain array indexing)
- Simple to reset / iterate
- Works great when the **range of values is small and known in advance**

**When NOT to use it:**
- Values span a huge range (e.g. up to 10^9) with few distinct values sparsely spread → use a `map`/`unordered_map` instead, or coordinate compression.

---

## 2. The General Pattern

```cpp
int freq[MAX_RANGE] = {0}; // initialize with zeros

for (int i = 0; i < n; i++) {
    freq[ index_mapping(x[i]) ]++;
}
```

The whole trick of frequency arrays is choosing the correct **index mapping function** so that every possible input value maps to a valid, non-negative array index.

---

## 3. Index Mapping Cases (Cheat Sheet)

| Data Type | Mapping | Notes |
|---|---|---|
| Array of Numbers (non-negative, small range) | `freq[arr[i]]++;` | Direct indexing, values are already valid indices |
| Array of Negative Numbers | `freq[arr[i] + SHIFT]++;` | `SHIFT` = abs(min value) to push everything ≥ 0 |
| String of Digits ('0'-'9') | `freq[s[i] - '0']++;` | Maps '0'→0, '1'→1, ..., '9'→9 |
| Array/String of Lowercase Letters ('a'-'z') | `freq[s[i] - 'a']++;` | Maps 'a'→0, 'b'→1, ..., 'z'→25 |
| Array/String of Lowercase Letters (ASCII form) | `freq[s[i] - 97]++;` | Same as above; 97 = ASCII code of 'a' |
| Array/String of Uppercase Letters ('A'-'Z') | `freq[s[i] - 'A']++;` | Maps 'A'→0, 'B'→1, ..., 'Z'→25 |
| Array/String of Uppercase Letters (ASCII form) | `freq[s[i] - 65]++;` | Same as above; 65 = ASCII code of 'A' |

### Quick ASCII Reference
- `'0'` = 48 → `'9'` = 57
- `'A'` = 65 → `'Z'` = 90
- `'a'` = 97 → `'z'` = 122

---

## 4. Detailed Case Breakdown

### 4.1 Array of Numbers (small non-negative range)
```cpp
int freq[MAXN] = {0};
for (int i = 0; i < n; i++)
    freq[arr[i]]++;
```
- Works directly because the value itself is a valid index.
- Array size must be ≥ (max possible value + 1).

### 4.2 Array of Negative Numbers
```cpp
int SHIFT = 1000; // e.g. if values range from -1000 to 1000
int freq[2 * SHIFT + 1] = {0};
for (int i = 0; i < n; i++)
    freq[arr[i] + SHIFT]++;
```
- Since array indices can't be negative, shift every value by a constant so the minimum possible value maps to index 0.
- `SHIFT` should equal the absolute value of the smallest number in the range.
- To recover the original value later: `original = index - SHIFT`.

### 4.3 String of Digits
```cpp
int freq[10] = {0};
for (int i = 0; i < s.size(); i++)
    freq[s[i] - '0']++;
```
- Subtracting the character `'0'` converts a digit character directly to its numeric value (0–9).

### 4.4 String of Lowercase Letters
```cpp
int freq[26] = {0};
for (int i = 0; i < s.size(); i++)
    freq[s[i] - 'a']++;   // or: freq[s[i] - 97]++;
```
- Converts 'a'..'z' → 0..25.
- `'a' - 'a' = 0`, `'z' - 'a' = 25`.

### 4.5 String of Uppercase Letters
```cpp
int freq[26] = {0};
for (int i = 0; i < s.size(); i++)
    freq[s[i] - 'A']++;   // or: freq[s[i] - 65]++;
```
- Converts 'A'..'Z' → 0..25.

### 4.6 Mixed-case Strings
If a string has both cases and you want one combined frequency array (case-insensitive counting):
```cpp
int freq[26] = {0};
for (int i = 0; i < s.size(); i++) {
    char c = tolower(s[i]);
    freq[c - 'a']++;
}
```

---

## 5. Full Working Examples

### Example 1: Most frequent character in a string
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    string s;
    cin >> s;

    int freq[26] = {0};
    for (int i = 0; i < (int)s.size(); i++)
        freq[s[i] - 'a']++;

    int maxFreq = 0, maxIdx = 0;
    for (int i = 0; i < 26; i++) {
        if (freq[i] > maxFreq) {
            maxFreq = freq[i];
            maxIdx = i;
        }
    }

    cout << "Most frequent char: " << char(maxIdx + 'a')
         << " -> " << maxFreq << " times\n";
}
```

### Example 2: Counting digits in a numeric string
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    string s;
    cin >> s;

    int freq[10] = {0};
    for (char c : s)
        freq[c - '0']++;

    for (int i = 0; i < 10; i++)
        cout << i << ": " << freq[i] << " times\n";
}
```

### Example 3: Frequency of numbers including negatives
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n;
    cin >> n;
    vector<int> arr(n);
    for (auto &x : arr) cin >> x;

    const int SHIFT = 1000; // assume values in [-1000, 1000]
    vector<int> freq(2 * SHIFT + 1, 0);

    for (int x : arr)
        freq[x + SHIFT]++;

    // print non-zero frequencies
    for (int i = 0; i < (int)freq.size(); i++) {
        if (freq[i] > 0)
            cout << (i - SHIFT) << " -> " << freq[i] << "\n";
    }
}
```

### Example 4: Check Anagram using frequency arrays
```cpp
#include <bits/stdc++.h>
using namespace std;

bool isAnagram(string a, string b) {
    if (a.size() != b.size()) return false;

    int freq[26] = {0};
    for (char c : a) freq[c - 'a']++;
    for (char c : b) freq[c - 'a']--;

    for (int i = 0; i < 26; i++)
        if (freq[i] != 0) return false;

    return true;
}

int main() {
    string a, b;
    cin >> a >> b;
    cout << (isAnagram(a, b) ? "Anagram" : "Not Anagram") << "\n";
}
```

### Example 5: Find first non-repeating character
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    string s;
    cin >> s;

    int freq[26] = {0};
    for (char c : s) freq[c - 'a']++;

    for (int i = 0; i < (int)s.size(); i++) {
        if (freq[s[i] - 'a'] == 1) {
            cout << "First non-repeating: " << s[i] << "\n";
            return 0;
        }
    }
    cout << "None\n";
}
```

---

## 6. Common Adhoc Problem Patterns Using Frequency Arrays

- **Anagram check** — compare two frequency arrays for equality (or increment/decrement and check all zeros).
- **Most/least frequent element** — single pass to build freq array, then scan for max/min.
- **First unique character** — build freq array, then scan original sequence for first count == 1.
- **Check if two strings are permutations of each other** — same as anagram check.
- **Counting distinct elements** — count how many indices in freq array are > 0.
- **Majority element** (if value range is small) — element with freq > n/2.
- **Check if array/string can be rearranged into a palindrome** — at most one character can have an odd frequency.
- **Digit frequency problems** — counting how many times each digit (0–9) appears in a number/string.
- **Range-shifted counting sort** — using frequency array to sort a small-range integer array in O(n + range).

---

## 7. Key Tips / Gotchas

- Always make sure the frequency array size covers the **entire possible range** of mapped indices (off-by-one errors are common).
- Always **initialize the array to 0** (`{0}` in C++, or `memset(freq, 0, sizeof freq)`).
- When values can be **negative**, don't forget the `SHIFT` — direct negative indexing is undefined behavior / crash.
- Character math (`c - '0'`, `c - 'a'`, `c - 'A'`) relies on **ASCII contiguity** — digits, lowercase, and uppercase letters are each contiguous blocks.
- Careful mixing character math with `int` vs `char` types — cast when needed to avoid sign issues.
- For large ranges with sparse values, prefer `unordered_map<int,int>` over a raw array (memory/time tradeoff).
- Resetting a frequency array between test cases: prefer `fill(freq, freq+SIZE, 0)` or `memset` over reallocating.

---

## 8. Summary Table 

| Input Type | Formula |
|---|---|
| Array of Numbers | `Freq[arr[i]]++;` |
| Array of Negative Numbers | `Freq[arr[i] + SHIFT]++;` |
| String of Digits | `Freq[s[i] - '0']++;` |
| Array of Characters (lowercase, char form) | `Freq[s[i] - 'a']++;` |
| Array of Characters (lowercase, ASCII form) | `Freq[s[i] - 97]++;` |
| Array of Characters (uppercase, char form) | `Freq[s[i] - 'A']++;` |
| Array of Characters (uppercase, ASCII form) | `Freq[s[i] - 65]++;` |

---
