# Strings — Essential Methods

## Searching & Slicing

### find()

```cpp
// return the index of the first occurrence of a char/substring
string st = "ICPC";
int pos = st.find("C");
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

// Prefix check (pre-C++20)
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

## Size

### size() / length()

```cpp
// return the size of the string
int length = st.size();
int length = st.length();
```

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

> [!warning] Leftover newline If a `cin >> x` was used before `getline`, it leaves a trailing `\n` in the buffer, which `getline` will read as an empty line. Fix with `cin >> ws` before it — `ws` skips leading whitespace/newlines.

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

## Quick Reference Table

|Method|Purpose|
|---|---|
|`find()`|index of first occurrence of a char/substring|
|`substr(pos, len)`|slice a substring|
|`replace(pos, len, str)`|replace part of the string|
|`insert(pos, str)`|insert a substring|
|`push_back(ch)` / `pop_back()`|add / remove last char|
|`size()` / `length()`|get string length|
|`swap()`|swap two whole strings|
|`sort(st.begin(), st.end())`|sort chars in the string|
|`getline(cin, st)`|read a full line/phrase|
|`reverse(st.begin(), st.end())`|reverse the string|
|`isupper()` / `islower()`|check char case|
|`toupper()` / `tolower()`|convert char case|

---