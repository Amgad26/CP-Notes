
# Divisibility Rules

## Digit Sum Rules
- **÷3** → sum of digits divisible by 3
- **÷9** → sum of digits divisible by 9

## Last Digit(s) Rules
- **÷2** → last digit is even
- **÷5** → last digit is 0 or 5
- **÷10** → last digit is 0
- **÷4** → last 2 digits divisible by 4
- **÷8** → last 3 digits divisible by 8
- **÷25** → last 2 digits are 00, 25, 50, or 75

## Alternating Sum Rule
- **÷11** → alternating sum of digits (right to left) divisible by 11
	- Example: `3168` → 8 - 6 + 1 - 3 = 0 → divisible by 11

## Trickier Rules
- **÷7** → double the last digit, subtract from the rest, repeat until small; check if divisible by 7
	- Example: `203` → 20 - (3×2) = 14 → divisible by 7
- **÷6** → divisible by both 2 **and** 3
- **÷12** → divisible by both 3 **and** 4

## Why It Works (3 and 9)
Because `10 ≡ 1 (mod 3)` and `10 ≡ 1 (mod 9)`, each digit's place value contributes only the digit itself to the remainder.

Similarly, `10 ≡ -1 (mod 11)`, which is why 11 uses an **alternating** sum instead of a plain sum.

---
