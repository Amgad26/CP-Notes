# Casting

## 1. `static_cast`

### Numeric ↔ Numeric
```cpp
int i = 65;
double d = static_cast<double>(i);      // 65.0
float f = static_cast<float>(d);        // 65.0f
long l = static_cast<long>(i);          // 65L
short sh = static_cast<short>(i);       // 65
char c = static_cast<char>(i);          // 'A'
bool b = static_cast<bool>(i);          // true (non-zero)
unsigned int u = static_cast<unsigned int>(i); // 65
```
### char ↔ int
```cpp
char c = 'A';
int i = static_cast<int>(c);   // 65
char c2 = static_cast<char>(97); // 'a'
```
### double → int (truncation)
```cpp
double d = 9.99;
int i = static_cast<int>(d);   // 9 (truncates, not rounds)
```
### bool ↔ int
```cpp
bool flag = true;
int i = static_cast<int>(flag); // 1
bool b = static_cast<bool>(0);  // false
```
### enum ↔ int
```cpp
enum Color { RED, GREEN, BLUE };
Color c = RED;
int i = static_cast<int>(c);       // 0
Color c2 = static_cast<Color>(2);  // BLUE
```
### Pointer upcast/downcast (class hierarchy)
```cpp
class Base { public: virtual ~Base() {} };
class Derived : public Base {};

Derived d;
Base* b = static_cast<Base*>(&d);       // upcast (safe, implicit anyway)
Derived* dp = static_cast<Derived*>(b); // downcast (unsafe if b isn't really Derived)
```
### void* ↔ typed pointer
```cpp
int x = 10;
void* vp = static_cast<void*>(&x);
int* ip = static_cast<int*>(vp);
```
### Explicit constructor conversion (class types)
```cpp
class Fraction {
public:
    explicit Fraction(int n) : num(n) {}
    int num;
};
int n = 5;
Fraction fr = static_cast<Fraction>(n); // calls explicit constructor
```

## 2. `dynamic_cast`

### Downcast pointer (polymorphic types)
```cpp
class Base { public: virtual ~Base() {} };
class Derived : public Base { public: void hello() {} };

Base* b = new Derived();
Derived* d = dynamic_cast<Derived*>(b);
if (d) d->hello();  // succeeds

Base* b2 = new Base();
Derived* d2 = dynamic_cast<Derived*>(b2);
if (!d2) std::cout << "cast failed, got nullptr\n"; // fails
```
### Downcast reference (throws on failure)
```cpp
Base* b = new Derived();
try {
    Derived& d = dynamic_cast<Derived&>(*b); // succeeds
} catch (std::bad_cast& e) {
    std::cout << e.what() << "\n";
}

Base* b2 = new Base();
try {
    Derived& d2 = dynamic_cast<Derived&>(*b2); // throws std::bad_cast
} catch (std::bad_cast& e) {
    std::cout << "failed: " << e.what() << "\n";
}
```
### Sideways cast (between siblings)
```cpp
class Base { public: virtual ~Base() {} };
class A : public Base {};
class B : public Base {};

Base* ba = new A();
B* bp = dynamic_cast<B*>(ba); // nullptr, A is not a B
```
### dynamic_cast to void*
```cpp
Base* b = new Derived();
void* vp = dynamic_cast<void*>(b); // gets pointer to most-derived object
```

## 3. `const_cast`

### Remove const from pointer
```cpp
const int x = 10;
const int* cp = &x;
int* p = const_cast<int*>(cp);
// *p = 20; // UB if x is truly const
```
### Add const
```cpp
int y = 5;
int* p = &y;
const int* cp = const_cast<const int*>(p); // usually unnecessary (implicit conversion works)
```
### Remove const from reference
```cpp
void modify(int& ref) { ref = 100; }

const int x = 10;
modify(const_cast<int&>(x)); // UB if x is truly const, legal if x was originally non-const
```
### Practical safe use: calling legacy non-const API on const data that is NOT actually const
```cpp
void legacyPrint(char* s) { std::cout << s; }

int main() {
    char buf[] = "hello"; // non-const underlying data
    const char* cs = buf;
    legacyPrint(const_cast<char*>(cs)); // safe, buf is not truly const
}
```
### Remove volatile
```cpp
volatile int v = 5;
int i = const_cast<int&>(v);
```

## 4. `reinterpret_cast`

### Pointer ↔ integer
```cpp
int x = 42;
int* p = &x;
uintptr_t addr = reinterpret_cast<uintptr_t>(p); // pointer to integer
int* p2 = reinterpret_cast<int*>(addr);          // integer back to pointer
```
### Pointer ↔ unrelated pointer type
```cpp
int i = 65;
int* ip = &i;
char* cp = reinterpret_cast<char*>(ip); // reinterpret raw bytes
std::cout << cp[0]; // first byte of int (endian-dependent)
```
### Function pointer casting
```cpp
void foo() { std::cout << "foo\n"; }
using FuncPtr = void(*)();
FuncPtr fp = reinterpret_cast<FuncPtr>(&foo);
```
### struct reinterpretation (type punning — technically UB but common)
```cpp
struct Point { int x, y; };
int arr[2] = {1, 2};
Point* p = reinterpret_cast<Point*>(arr); // treat int array as Point
```
### Pointer to unrelated class type
```cpp
class A {};
class B {};

A a;
B* b = reinterpret_cast<B*>(&a); // dangerous — no relationship between A and B
```

## 5. C-style Cast (legacy, avoid)
```cpp
int i = (int)3.14;          // like static_cast
char* p = (char*)&i;        // like reinterpret_cast
const int x = 5;
int* p2 = (int*)&x;         // like const_cast
Base* b = (Derived*)basePtr; // like static_cast (no runtime check)
```
Combines behavior of `static_cast`, `const_cast`, and `reinterpret_cast` depending on context — avoid due to unpredictability.

## Quick Reference Table

| From \ To         | int/double/etc. | char        | bool       | enum       | pointer (related) | pointer (unrelated) | reference  |
|-------------------|------------------|-------------|------------|------------|--------------------|----------------------|------------|
| Numeric           | `static_cast`    | `static_cast` | `static_cast` | `static_cast` | —                  | —                     | —          |
| Pointer (base↔derived) | —          | —           | —          | —          | `static_cast` / `dynamic_cast` | —          | —          |
| Pointer (unrelated)| —               | —           | —          | —          | —                  | `reinterpret_cast`    | —          |
| const removal      | —               | —           | —          | —          | `const_cast`       | —                      | `const_cast` |
| Polymorphic downcast (checked) | —    | —           | —          | —          | `dynamic_cast`     | —                      | `dynamic_cast` |

## Rule of Thumb
- **Numeric/related type conversions** → `static_cast`
- **Safe polymorphic downcasting** → `dynamic_cast`
- **Const/volatile manipulation** → `const_cast`
- **Raw bit reinterpretation** → `reinterpret_cast` (last resort)
- **Never** use C-style casts in modern C++

---