# Output a Number with Fixed Precision

To print a floating-point number with a specific number of decimal places, use `<iomanip>` with `fixed` and `setprecision`.

## Syntax

```cpp
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    double num = 12.56637;
    cout << fixed << setprecision(4) << num << endl;
    // Output: 12.5664
    return 0;
}
```

## Key Points

- `fixed` — forces decimal notation (not scientific) and locks the digit count _after_ the decimal point.
- `setprecision(n)` — sets how many digits appear after the decimal point **when combined with `fixed`**.
    - Without `fixed`, `setprecision(n)` instead controls _total_ significant digits (not what you usually want).
- Both `fixed` and `setprecision` stay in effect for all subsequent output until changed — no need to repeat them every line.
- Rounding is automatic: `setprecision(4)` on `12.56637` rounds to `12.5664`.

## Example: Multiple Values

```cpp
double a = 3.14159;
double b = 100.005;
cout << fixed << setprecision(2);
cout << a << endl;   // 3.14
cout << b << endl;   // 100.01 (rounded, careful with binary float rounding)
```

## Quick Reference

|Goal|Code|
|---|---|
|2 decimal places|`fixed << setprecision(2)`|
|4 decimal places|`fixed << setprecision(4)`|
|Reset to default (6 sig figs)|`cout.unsetf(ios::fixed);`|

## printf Equivalent (C-style)

```cpp
printf("%.4f\n", num);  // 4 decimal places
```

---