# Signed and Unsigned Data Type Limits

## The Rule

For an **n-bit** data type:

|Type|Range|
|---|---|
|**Unsigned**|`0` to `2ⁿ − 1`|
|**Signed**|`−2ⁿ⁻¹` to `2ⁿ⁻¹ − 1`|

With `n` bits you can represent `2ⁿ` distinct values total. Unsigned uses all of them for non-negative numbers. Signed splits them roughly in half — one bit is used as the sign bit, so you lose one power of 2 from the positive side to make room for negatives (there's a slight asymmetry because 0 is counted on the positive side).

## Applied to Common C++ Types

|Type|Bits|Unsigned Range|Signed Range|
|---|---|---|---|
|`char`|8|0 to 255 (2⁸−1)|−128 to 127 (−2⁷ to 2⁷−1)|
|`short`|16|0 to 65,535 (2¹⁶−1)|−32,768 to 32,767 (−2¹⁵ to 2¹⁵−1)|
|`int`|32|0 to 4,294,967,295 (2³²−1)|−2,147,483,648 to 2,147,483,647 (−2³¹ to 2³¹−1)|
|`long long`|64|0 to 18,446,744,073,709,551,615 (2⁶⁴−1)|−9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 (−2⁶³ to 2⁶³−1)|

## Quick Way to Remember

- **Unsigned max** = `2ⁿ − 1`
- **Signed max** = `2ⁿ⁻¹ − 1`
- **Signed min** = `−2ⁿ⁻¹`

## Get Limits in Code

No need to memorize — use `<climits>`:

```cpp
#include <iostream>
#include <climits>
using namespace std;

int main() {
    cout << "int max: " << INT_MAX << endl;
    cout << "int min: " << INT_MIN << endl;
    cout << "long long max: " << LLONG_MAX << endl;
    cout << "unsigned int max: " << UINT_MAX << endl;
    return 0;
}
```

---