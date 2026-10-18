# Strings — Essential Methods

> [!note] Which standard?
> Anything marked **C++20 / C++23 / C++26** needs the matching compiler flag (`-std=c++20`, `-std=c++23`, ...). Check what the judge supports before relying on it. Pre-C++20 fallbacks are included where they exist.

## Searching & Slicing

### find()

```cpp
// return the index of the first occurrence of a char/substring
string st = "ICPC";
int pos = st.find("C");

// returns string::npos if not found, so check before using it
if (st.find("X") == string::npos) {
    // not found
}
```

### substr()

```cpp
std::string substr(size_t pos = 0, size_t len = npos) const;
```

- Returns a **new string** (copy), doesn't modify original
- `pos`: start index (0-based) — throws `out_of_range` if `pos > size()`
- `len`: number of chars (optional) — auto-clamped if too large

#### Examples
```cpp
std::string s = "Hello, World!";

s.substr(7);     // "World!"
s.substr(7, 5);  // "World"
s.substr(0, 5);  // "Hello"
```

#### Common Uses
```cpp
// Get file extension
s.substr(s.find('.') + 1);

// Remove last char
s.substr(0, s.size() - 1);

// Prefix check (pre-C++20), use starts_with() on C++20+
s.substr(0, 5) == "Hello";

// Python: s[start:end]
s.substr(start, end - start);
```

#### Tip
Use `std::string_view` on C++17+ for no-copy substrings:
```cpp
std::string_view sv = s;
sv.substr(7, 5); // no copy
```

## Prefix, Suffix & Contains

### starts_with() / ends_with() — C++20

```cpp
// check the beginning / end of a string
string s = "competitive";
s.starts_with("comp");   // true
s.ends_with("ive");      // true
s.starts_with('c');      // also accepts a single char
```

- Overloads: `string_view`, `char`, `const char*`

### contains() — C++23

```cpp
// check if a substring/char exists anywhere in the string
string s = "competitive";
s.contains("petit");     // true
s.contains('z');         // false
```

### Pre-C++20 fallbacks

```cpp
// starts_with
bool starts_with(const string& str, const string& prefix) {
    return str.compare(0, prefix.size(), prefix) == 0;
}

// ends_with
bool ends_with(const string& str, const string& suffix) {
    if (suffix.size() > str.size()) return false;
    return str.compare(str.size() - suffix.size(), suffix.size(), suffix) == 0;
}

// contains
bool contains = s.find("petit") != string::npos;
```

> [!tip] Why not `rfind` for ends_with?
> `str.rfind(suffix) == str.size() - suffix.size()` works, but `rfind` scans from the back through the whole string. `compare` only looks at the last `suffix.size()` chars.

## Modifying

### replace()

```cpp
// replace one or more than one char in string
st.replace(position, length, string);
```

### insert()

```cpp
// insert substring into string
st.insert(position, string);
```

### push_back() / pop_back()

```cpp
// add / remove char to the end of the string
st.push_back(ch);
st.pop_back();
```

### erase() (member)

```cpp
// remove `length` chars starting at `position`
st.erase(position, length);
```

### std::erase() / std::erase_if() — C++20

```cpp
// non-member: remove ALL occurrences of a char
string s = "a b c d";
std::erase(s, ' ');                                  // "abcd"

// remove all chars matching a condition
string t = "a1b2c3";
std::erase_if(t, [](char c) { return isdigit(c); }); // "abc"
```

- Both return the number of removed chars
- Pre-C++20 equivalent (erase-remove idiom):
```cpp
s.erase(remove(s.begin(), s.end(), ' '), s.end());
s.erase(remove_if(s.begin(), s.end(), ::isdigit), s.end());
```

### Range methods — C++23

```cpp
// work with any range (vector, array, another string, views...)
vector<char> v = {'a', 'b', 'c'};
string s = "xy";

s.append_range(v);                  // "xyabc"
s.assign_range(v);                  // "abc"
s.insert_range(s.begin(), v);       // insert at an iterator position
s.replace_with_range(s.begin(), s.begin() + 1, v);  // replace [i1, i2) with range

string t(std::from_range, v);       // construct from a range -> "abc"
```

### resize_and_overwrite() — C++23

```cpp
// resize, then write straight into the buffer (useful with C functions)
string s;
s.resize_and_overwrite(32, [](char* buf, size_t n) {
    return (size_t)snprintf(buf, n, "%d", 12345);  // return the final size
});
// s == "12345"
```

- Avoids zero-filling the buffer first, as `resize()` would

## Size

### size() / length()

```cpp
// return the size of the string
int length = st.size();
int length = st.length();
```

## Comparison

### operator<=> — C++20

```cpp
// three-way comparison
string a = "abc", b = "abd";
auto r = a <=> b;       // std::strong_ordering
if (r < 0)  { /* a < b */ }
if (r == 0) { /* equal */ }
```

- The compiler uses it to generate `<`, `<=`, `>`, `>=`
- The usual `<`, `==` etc. work exactly as before, so you rarely need to write `<=>` yourself in contests

## Swap, Sort, Reverse

### swap()

```cpp
// swap two strings
string a = "Ahmed";
string b = "Zeyad";
a.swap(b);
```

### sort()

```cpp
// sort string in asc/dec order
sort(st.begin(), st.end());
```

### reverse()

```cpp
// reverse the string
reverse(st.begin(), st.end());
```

## Input

### getline()

```cpp
// get a phrase as an input rather than only one word
string st;
getline(cin, st);
```

> [!warning] Leftover newline
> If a `cin >> x` was used before `getline`, it leaves a trailing `\n` in the buffer, which `getline` will read as an empty line. Fix with `cin >> ws` before it. `ws` skips leading whitespace/newlines.

```cpp
int n;
cin >> n;
string st;
getline(cin >> ws, st);   // skips leftover '\n', then reads the actual line
```

## Case Checking & Conversion

### isupper() / islower()

```cpp
// check if the char is uppercase or lowercase
isupper(st[3]);
islower(st[1]);
```

### toupper() / tolower()

```cpp
// converts a lowercase alphabet to an
// uppercase and the opposite
toupper(st[2]);
tolower(st[0]);
```

## Other Changes

### constexpr strings — C++20

- All `std::string` members can be used at compile time (`constexpr std::string`)
- Mostly irrelevant for contests

### std::u8string — C++20

- `basic_string<char8_t>`: a new string type for UTF-8, not a method

### nullptr construction — C++23

```cpp
string s = nullptr;   // compile error now (was undefined behavior)
```

### substr() on rvalues — C++23

- `std::move(s).substr(...)` now moves instead of copying

## C++26 (limited compiler support)

```cpp
// subview: substring as a string_view, no allocation
string s = "Hello, World!";
auto sv = s.subview(7, 5);        // "World"

// concatenating string + string_view
string r = s + string_view(" :)");
```

- The zero-argument `reserve()` (deprecated in C++20) is removed

## Quick Reference Table

|Method|Purpose|Since|
|---|---|---|
|`find()`|index of first occurrence of a char/substring|C++98|
|`substr(pos, len)`|slice a substring|C++98|
|`replace(pos, len, str)`|replace part of the string|C++98|
|`insert(pos, str)`|insert a substring|C++98|
|`erase(pos, len)`|remove part of the string|C++98|
|`push_back(ch)` / `pop_back()`|add / remove last char|C++98 / C++11|
|`size()` / `length()`|get string length|C++98|
|`swap()`|swap two whole strings|C++98|
|`sort(st.begin(), st.end())`|sort chars in the string|C++98|
|`getline(cin, st)`|read a full line/phrase|C++98|
|`reverse(st.begin(), st.end())`|reverse the string|C++98|
|`isupper()` / `islower()`|check char case|C++98|
|`toupper()` / `tolower()`|convert char case|C++98|
|`starts_with()` / `ends_with()`|check prefix / suffix|**C++20**|
|`std::erase(s, ch)`|remove all occurrences of a char|**C++20**|
|`std::erase_if(s, pred)`|remove all chars matching a predicate|**C++20**|
|`operator<=>`|three-way comparison|**C++20**|
|`contains()`|check if a substring/char exists|**C++23**|
|`append_range()` / `assign_range()`|append / assign from a range|**C++23**|
|`insert_range()` / `replace_with_range()`|insert / replace with a range|**C++23**|
|`resize_and_overwrite(n, op)`|resize and fill the buffer directly|**C++23**|
|`subview(pos, n)`|substring as `string_view`, no copy|**C++26**|

---
