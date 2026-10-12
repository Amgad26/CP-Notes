
# Counting — Discrete Math for CP

> [!abstract] How to use this note
> 1. Read a concept → 2. try the **Your turn** callout *before* opening the answer (click to unfold) → 3. copy the Python template into your CP library.
> Code: Python in §5, **C++ in §5C**.
> Sections: [[#1. Basic Counting]] · [[#2. Binomial Coefficients and Subsets]] · [[#3. Equivalence Relations and Counting]] · [[#4. Counting cheat-sheet]] · [[#5. CP toolkit (Python)]] · [[#6. Textbook problems with answers]] · [[#7. Extra topics CP needs]] · [[#8. Practice problems]] · [[#9. Videos]]

---

# 1. Basic Counting

## 1.1 Analyzing loops
The book's idea: *to count how many times something runs, count the elements of a set of "events", splitting it into pieces you can count.*

```text
for i = 1 to n
  for j = i+1 to n
    if A[i] > A[j]: swap          # selection-sort style
```
- For a given `i` the inner loop does $n-i$ comparisons.
- Total $=(n-1)+(n-2)+\dots+1+0 = \dfrac{n(n-1)}{2}$.

## 1.2 The Sum Principle
> [!note] Sum principle
> The size of a union of **mutually disjoint** sets is the sum of their sizes:
> $$\left|\bigcup_{i=1}^{n} S_i\right|=\sum_{i=1}^{n}|S_i|$$
> *(disjoint = no common elements; a "family of mutually disjoint sets" = every two are disjoint).*

This is **abstraction**: the reasoning ("split into disjoint pieces, add") matters more than the formula.

### Gauss trick for $1+2+\dots+(n-1)$
Write the sum forwards and backwards; each column adds to $n$, there are $n-1$ columns, so twice the sum is $n(n-1)$:
$$\sum_{i=1}^{n-1} i=\frac{n(n-1)}{2}$$
The book later shows a **trick-free** way: it equals $\binom n2$ (see [[#1.4 Ordered vs unordered pairs → n choose 2]]).

## 1.3 The Product Principle
```text
for i = 1 to r
  for j = 1 to m
    S = 0
    for k = 1 to n
      S = S + A[i,k]*B[k,j]     # matrix multiplication
    C[i,j] = S
```
Inner loop: $n$ multiplications. Middle loop repeats it $m$ times → $nm$. Outer loop repeats $r$ times → **$rmn$ multiplications**.
Why multiply? *Multiplication is repeated addition* (sum principle applied to equal-size disjoint sets).

> [!note] Product principle (version 1)
> A union of $m$ disjoint sets, all of size $n$, has size $mn$.

> [!note] Product principle (version 2 — lists)
> If a set of lists of length $k$ satisfies: there are $i_1$ choices for the 1st element, and for each $j>1$ and each choice of the first $j-1$ elements there are $i_j$ choices for position $j$, then there are $i_1 i_2\cdots i_k$ lists.

### Mixed example (Exercise 1.1-3)
```text
for i = 1 to n-1
  minval = A[i]; minindex = i
  for j = i to n
    if A[j] < A[i] ...           # n-i+1 comparisons
  exchange A[i], A[minindex]
for i = 2 to n
  if A[i] < 2*A[i-1] ...         # n-1 comparisons
```
Loop 1: $\sum_{i=1}^{n-1}(n-i+1)=\frac{n(n+1)}2-1$. Loop 2: $n-1$. **Sum principle** (two disjoint sets of comparisons) → $\frac{n(n+1)}2+n-2$.

### Passwords (Exercise 1.1-4)
Passwords of length 4–8 using upper+lower case letters. Length $i$ → $52^i$ (product principle v2). Sum principle over lengths:
$$52^4+52^5+52^6+52^7+52^8$$
Fraction with 4 letters ≈ $\dfrac{52^4}{52^8}=52^{-4}\approx 1.4\times10^{-7}$ → **0.000014 %** (guessing a 4-letter password is ~7 million times easier).

> [!tip] CP lesson — estimate by the dominant term
> In a sum of geometric-like terms, the largest term dominates. Useful for complexity estimation too.

## 1.4 Ordered vs unordered pairs → n choose 2
Every pair $\{i,j\}$ is compared exactly once in the selection loop, so #comparisons = #2-element subsets of $\{1..n\}$.
- Ordered pairs: $n(n-1)$ (product principle).
- Each unordered pair appears **twice** (as $(a,b)$ and $(b,a)$) → divide by 2:
$$\binom n2=\frac{n(n-1)}2 \;\Rightarrow\; 1+2+\dots+(n-1)=\binom n2$$

> [!example] Your turn 1.1 — loop counting
> ```python
> cnt = 0
> for i in range(n):
>     for j in range(i+1, n):
>         cnt += 1
> ```
> (a) What is `cnt`? (b) For $n=2\cdot10^5$, will this pass in Python? (c) Count pairs $(i<j)$ with $a_i+a_j$ even **without** a double loop.
>
> > [!success]- Answer
> > (a) $\binom n2=n(n-1)/2$. (b) ≈ $2\times10^{10}$ iterations → no (even C++ ~1e9/s is too slow). (c) Pair is even iff both even or both odd (parity rules: odd+odd=even, even+even=even, odd+even=odd). With $e$ evens and $o$ odds: $\binom e2+\binom o2$, in $O(n)$.

> [!example] Your turn 1.2 — sum vs product
> A licence plate = 3 letters (A–Z) followed by 4 digits. (a) How many plates? (b) If the 3 letters must be all different? (c) Plates where the first digit is not 0?
>
> > [!success]- Answer
> > (a) $26^3\cdot10^4=175{,}760{,}000$. (b) $26\cdot25\cdot24\cdot10^4=156{,}000{,}000$. (c) $26^3\cdot9\cdot10^3=158{,}184{,}000$.

> [!example] Your turn 1.3 — complement counting
> How many 5-digit PINs (digits 0–9) contain **at least one repeated digit**?
>
> > [!success]- Answer
> > Total $10^5=100000$. All different: $10\cdot9\cdot8\cdot7\cdot6=30240$. Answer $=100000-30240=69760$. (Same idea as the textbook's "ten-digit numbers with at least one equal consecutive pair" = $9\cdot10^9-9^{10}$.)

---

# 2. Binomial Coefficients and Subsets

## 2.1 Correspondence principle (bijection)
> [!note] Correspondence principle
> Two sets have the same size **iff** there is a one-to-one function from one **onto** the other (a *bijection*).
> - one-to-one (injective): different inputs → different outputs.
> - onto (surjective): every element of the target is hit.

Example: the loops `i<j<k` examine each *increasing triple* once. The map $(i,j,k)\mapsto\{i,j,k\}$ is a bijection between increasing triples and 3-element subsets ⇒ the loop runs $\binom n3$ times.

## 2.2 Counting k-element subsets
1. Lists of $k$ **distinct** elements from $n$: $n(n-1)\cdots(n-k+1)=\dfrac{n!}{(n-k)!}$ (product principle v2). Notation: $n^{\underline k}$ ("n to the k falling", Knuth).
2. Each $k$-subset appears in $k!$ lists.
3. Divide:

> [!important] Theorem 1.2.1
> For $0\le k\le n$:
> $$\binom nk=\frac{n!}{k!\,(n-k)!}$$
> ($k=0$ gives 1 because $0!=1$ and the only 0-subset is $\emptyset$.)

## 2.3 Pascal's triangle & Pascal relationship
|n\k|0|1|2|3|4|5|6|
|---|---|---|---|---|---|---|---|
|0|1| | | | | | |
|1|1|1| | | | | |
|2|1|2|1| | | | |
|3|1|3|3|1| | | |
|4|1|4|6|4|1| | |
|5|1|5|10|10|5|1| |
|6|1|6|15|20|15|6|1|

Properties visible in the table: row starts/ends with 1; rows rise then fall; **symmetry** $\binom nk=\binom n{n-k}$; each entry = entry above-left + entry above.
Next row (n=7): `1 7 21 35 35 21 7 1`.

> [!important] Theorem 1.2.2 (Pascal relationship)
> For $n>0$, $0<k<n$:
> $$\binom nk=\binom{n-1}{k-1}+\binom{n-1}{k}$$
> **Combinatorial proof (the book's style):** split the $k$-subsets of $\{x_1..x_n\}$ into those that **contain** $x_n$ (remove it → a $(k-1)$-subset of the other $n-1$: $\binom{n-1}{k-1}$) and those that **don't** ($\binom{n-1}{k}$). Disjoint pieces → sum principle.

> [!tip] Guiding principle
> When a formula relates sizes of sets, look for a **relationship among the sets**, not just algebra.

## 2.4 The Binomial Theorem
$$(x+y)^n=\sum_{i=0}^{n}\binom ni x^{n-i}y^i$$
**Why:** multiply $n$ copies of $(x+y)$; pick $y$ from $k$ of them and $x$ from the rest. The number of ways to pick which $k$ copies give $y$ is $\binom nk$ ⇒ coefficient of $x^{n-k}y^k$.
Small cases: $(x+1)^4=x^4+4x^3+6x^2+4x+1$; $(x+y)^4=x^4+4x^3y+6x^2y^2+4xy^3+y^4$; $(2+y)^4=16+32y+24y^2+8y^3+y^4$.

## 2.5 Labellings and multinomials
- $k$ labels of type A and $n-k$ of type B on $n$ objects: $\binom nk$ ways (choose which get A).
- 3 types ($k_1+k_2+k_3=n$): $\binom n{k_1}\binom{n-k_1}{k_2}=\dfrac{n!}{k_1!\,k_2!\,k_3!}$ (product principle).
- This is the coefficient of $x^{k_1}y^{k_2}z^{k_3}$ in $(x+y+z)^n$ (**multinomial coefficient**):
$$\binom{n}{n_1,n_2,\dots,n_k}=\frac{n!}{n_1!\,n_2!\cdots n_k!}$$
- Two proofs exist for labellings: (i) the sequential choosing above, (ii) the *equivalence-class* proof in [[#3. Equivalence Relations and Counting]] (explains the symmetric denominator).

> [!example] Your turn 2.1
> (a) $\binom{8}{3}$, $\binom{8}{5}$? (b) How many ways to arrange the letters of **BANANA**? (c) Coefficient of $x^2y^3$ in $(2x+y)^5$? (d) A group of 10 people: pick a committee of 4 **with a designated captain**, two ways.
>
> > [!success]- Answer
> > (a) 56 and 56 (symmetry). (b) $\frac{6!}{3!\,2!\,1!}=60$. (c) $\binom52\cdot2^2=40$. (d) Pick 4 then captain: $\binom{10}4\cdot4=840$; or captain first then 3 others: $10\cdot\binom93=840$. ✔ Same answer, two proofs.

> [!example] Your turn 2.2 — lattice paths
> Grid paths using only Right/Up steps from $(0,0)$ to $(5,3)$: (a) how many? (b) how many pass through $(2,1)$?
>
> > [!success]- Answer
> > A path is a string of 5 R's and 3 U's → (a) $\binom83=56$. (b) $(0,0)\to(2,1)$: $\binom31=3$; $(2,1)\to(5,3)$: 3 R + 2 U → $\binom52=10$; product $=30$. (Textbook Problem 8: paths to $(m,n)$ = $\binom{m+n}{m}$.)

> [!example] Your turn 2.3 — no two adjacent
> Binary strings of length 10 with exactly 4 ones: (a) any positions, (b) no two ones adjacent?
>
> > [!success]- Answer
> > (a) $\binom{10}4=210$. (b) Place 6 zeros, which creates 7 gaps; choose 4 gaps for ones: $\binom74=35$ (general formula $\binom{n-k+1}{k}$).

---

# 3. Equivalence Relations and Counting

## 3.1 Equivalence classes
A relation that splits a set into **mutually exclusive classes** is an *equivalence relation*; the classes form a **partition**.
Formal definition (footnote): reflexive (x~x), symmetric (x~y ⇒ y~x), transitive (x~y, y~z ⇒ x~z). Both definitions are equivalent.
Example: integers 0..12, related if same remainder mod 3 → classes {0,3,6,9,12}, {1,4,7,10}, {2,5,8,11} (sizes differ!).

> [!important] Theorem 1.3.1 — Equivalence principle
> If an equivalence relation on an $s$-element set has $m$ classes **each of size $t$**, then $m=s/t$.
> *Proof:* product principle gives $s=mt$.

**Method (CP gold):** count *lists* (easy), find how many lists are "the same object", divide.

## 3.2 Re-proving $\binom nk$ symmetrically
Lists of all $n$ objects: $n!$. Group lists that are equal as a labelling (shuffle the $k$ "in" among themselves and the $n-k$ "out" among themselves): each class has $k!(n-k)!$ lists ⇒ $m\,k!(n-k)!=n!$. This shows *why* the denominator is symmetric. Extends to any number of labels.

## 3.3 Circular arrangements
- **Round table, n people** (rotations equal): each class has $n$ lists ⇒ $\dfrac{n!}{n}=(n-1)!$. (4 people → 6.)
- **Necklace / ring network, n distinct beads** (rotations **and** flips equal): class size $2n$ ⇒ $\dfrac{n!}{2n}=\dfrac{(n-1)!}{2}$ (for $n\ge3$).
  - Why $2n$: rotating then reversing equals reversing then rotating by another amount, so the group is $n$ rotations × 2 (flip or not).
- **n men and n women alternating around a table:** lists $2\,(n!)^2$, class size $2n$ ⇒ $n!\,(n-1)!$.
- **n red and n+1 black checkers in a circle:** $\binom{2n+1}{n}/(2n+1)=\frac1{n+1}\binom{2n}n$ = **Catalan number** $C_n$.

## 3.4 Multisets (repetition allowed, order ignored)
A multiset is given by multiplicities $m(1),\dots,m(n)$ with total $k$. Encode as a row of $k$ red checkers (items) and $n-1$ black checkers (separators): e.g. $m=(2,0,1)$ → `R R | | R`.
Count with distinguishable checkers: $(n+k-1)!$; divide by $k!\,(n-1)!$ (shuffling reds, shuffling blacks):
$$\#\text{k-multisets of an n-set}=\binom{n+k-1}{k}$$
This is **stars and bars** — the single most used formula in CP counting.

## 3.5 When classes have different sizes
Square with 2 red + 2 black checkers, rotations equal: lists = $\binom42=6$. Classes: {RRBB, RBBR, BBRR, BRRB} (size 4) and {RBRB, BRBR} (size 2) → 2 arrangements, but Theorem 1.3.1 **fails** (unequal sizes). Fix = **Burnside's lemma** ([[#7. Extra topics CP needs]]).

> [!example] Your turn 3.1
> (a) 6 people at a round table, but A and B must sit next to each other. (b) Necklaces from 5 *distinct* beads (flips allowed). (c) Ways to give 7 identical candies to 3 children, each child gets ≥ 1. (d) Same, but ≥ 0.
>
> > [!success]- Answer
> > (a) Glue A,B into a block (2 internal orders): 5 units around a table → $4!\cdot2=48$. (b) $4!/2=12$. (c) Give 1 each first, distribute 4 freely: $\binom{4+3-1}{4}=\binom62=15$ (equivalently $\binom{k-1}{n-1}=\binom62$). (d) $\binom{7+2}{2}=36$.

---

# 4. Counting cheat-sheet

The 4 formulas from Problem 12 (§1.3). Ask: **Is order important? Is repetition allowed?**

| | Order matters (lists) | Order ignored (sets) |
|---|---|---|
| **Repetition allowed** | $n^k$ | $\binom{n+k-1}{k}$ |
| **No repetition** | $n^{\underline k}=\frac{n!}{(n-k)!}$ | $\binom nk$ |

Translations:
- $k$ **different** candy bars → $n$ people, anyone can get many: $n^k$ (= number of functions $\{1..k\}\to\{1..n\}$). Nobody gets 2: $n^{\underline k}$ (one-to-one functions).
- $k$ **identical** candy bars → $n$ people: $\binom{n+k-1}k$ (many allowed), $\binom nk$ (≤1 each).
- Choose $k$ pieces from $n$ candy *types*: $\binom{n+k-1}k$. $k$ children each pick a different type: $n^{\underline k}$.
- Top $k$ ranked officials from $n$: $n^{\underline k}$.
- Functions $S\to T$ with $|S|=s,|T|=t$: $t^s$. Permutations of $n$: $n!$.
- $k$ distinct books on $n$ shelves (order on shelf matters): $n(n+1)\cdots(n+k-1)=\frac{(n+k-1)!}{(n-1)!}$.

**Techniques list:** Sum principle (split into cases) · Product principle (independent choices) · Complement (total − bad) · Bijection (map to easier object) · Equivalence-class division · Pascal recurrence (DP) · Labelling/multinomial.

---

# 5. CP toolkit (Python)

> [!warning] Constraints → method
> | Situation | Use |
> |---|---|
> | $n\le 60$ exact, no mod | `math.comb(n,k)` (Python big ints) |
> | $n,k\le 5000$, any modulus | Pascal DP $O(n^2)$ (no division!) |
> | $n\le10^6$–$10^7$, prime mod | factorial + inverse factorial, $O(1)$ query |
> | huge $n$, small prime mod $p$ | Lucas theorem ([[#7. Extra topics CP needs]]) |
> | composite modulus | Pascal DP, or factor + CRT |

## 5.1 Exact small values
```python
from math import comb, perm, factorial
comb(10, 3)      # 120   -> C(n,k)
perm(10, 3)      # 720   -> n^(falling k)
```

## 5.2 Pascal DP (works for ANY modulus)
```python
def pascal(N, MOD):
    C = [[0]*(N+1) for _ in range(N+1)]
    for n in range(N+1):
        C[n][0] = 1
        for k in range(1, n+1):
            C[n][k] = (C[n-1][k-1] + C[n-1][k]) % MOD   # Theorem 1.2.2
    return C
```
This is the **fast** version of the textbook's slow recursive `pascal(n,k)` (Problem 11): the recursion recomputes the same subproblems and makes $\Theta\binom nk$ calls; DP / memoisation fixes it.

## 5.3 Factorials + inverse factorials (prime modulus) — Python (C++ version in [[#5C. C++ versions (use these)]])
```python
MOD = 10**9 + 7
N = 10**6 + 5

fact = [1] * N
for i in range(1, N):
    fact[i] = fact[i-1] * i % MOD

inv_fact = [1] * N
inv_fact[N-1] = pow(fact[N-1], MOD-2, MOD)      # Fermat: a^(p-2) = a^(-1)
for i in range(N-1, 0, -1):
    inv_fact[i-1] = inv_fact[i] * i % MOD

def nCr(n, r):
    if r < 0 or r > n: return 0
    return fact[n] * inv_fact[r] % MOD * inv_fact[n-r] % MOD

def nPr(n, r):
    if r < 0 or r > n: return 0
    return fact[n] * inv_fact[n-r] % MOD
```
Why modular inverse: you can't divide under a modulus, but $a/b \equiv a\cdot b^{-1}$ when $\gcd(b,\text{mod})=1$.

## 5.4 Formulas as functions
```python
def multinomial(parts):                 # n!/(n1! n2! ...)
    res = fact[sum(parts)]
    for p in parts: res = res * inv_fact[p] % MOD
    return res

def stars_bars(n, k):                   # k identical items -> n boxes, boxes >= 0
    return nCr(n + k - 1, k)

def stars_bars_pos(n, k):               # each box >= 1
    return nCr(k - 1, n - 1)

def lattice(m, n):                      # R/U paths (0,0)->(m,n)
    return nCr(m + n, m)

def catalan(n):                         # balanced bracket sequences with n pairs
    return nCr(2*n, n) * pow(n+1, MOD-2, MOD) % MOD

def circular(n):                        # round table
    return fact[n-1]

def necklace_flip(n):                   # n>=3 distinct beads, flips allowed
    return fact[n-1] * pow(2, MOD-2, MOD) % MOD
```

## 5.5 Derangements and Stirling numbers (from Problem 7)
```python
def derangements(n):                    # permutations with no fixed point
    D = [1, 0] + [0]*(n-1)
    for i in range(2, n+1):
        D[i] = (i-1) * (D[i-1] + D[i-2]) % MOD
    return D[n]

def stirling2(N):                       # S(n,k): partitions of n-set into k classes
    S = [[0]*(N+1) for _ in range(N+1)]
    S[0][0] = 1
    for n in range(1, N+1):
        for k in range(1, n+1):
            S[n][k] = (S[n-1][k-1] + k * S[n-1][k]) % MOD
    return S
```
Stirling recurrence reason (Problem 7b): element $n$ is either alone in its class ($S(n-1,k-1)$) or joins one of the $k$ classes of a partition of the other $n-1$ ($k\cdot S(n-1,k)$).

## 5.6 CP example — paths with blocked cells (Gerald & Giant Chess idea)
Number of paths from $(1,1)$ to $(h,w)$ avoiding black cells: sort obstacles, let `dp[i]` = paths from start to obstacle `i` that touch **no other obstacle**:
```python
def paths(r1,c1,r2,c2):                 # R/D paths between cells
    if r2 < r1 or c2 < c1: return 0
    return nCr((r2-r1)+(c2-c1), r2-r1)

def solve(h, w, blocked):
    pts = sorted(blocked) + [(h, w)]
    dp = []
    for i, (r, c) in enumerate(pts):
        v = paths(1, 1, r, c)
        for j in range(i):
            v -= dp[j] * paths(pts[j][0], pts[j][1], r, c)   # subtract paths through earlier block
        dp.append(v % MOD)
    return dp[-1]
```
Pattern: *total − bad*, with **bad** classified by the **first** forbidden thing hit (sum principle on disjoint cases).

## 5.7 Recognition checklist
1. Are objects **distinguishable**? (identical → divide / multisets)
2. Does **order** matter? Is **repetition** allowed? → [[#4. Counting cheat-sheet]]
3. "At least one…" → **complement**.
4. "Circular / symmetric / rotations equal" → **divide by class size** (or Burnside if sizes vary).
5. "Sum $x_1+\dots+x_n=k$" → **stars and bars** (add lower bounds by pre-giving).
6. Counting loop iterations / pairs / triples → $\binom n2$, $\binom n3$.
7. Answers "mod $10^9+7$" → §5.3.

## 5C. C++ versions (use these)

> [!tip] C++ pitfalls for counting
> - Use `long long`; reduce `% MOD` after **every** multiplication (`a*b` of two values < 1e9+7 fits in `long long`, three do not).
> - Subtraction: `(a - b % MOD + MOD) % MOD` — `%` can be negative in C++.
> - Size `N` to the largest `n` you will ever pass to `nCr` (e.g. `n+m` for grid paths, `2n` for Catalan).
> - Loop-counting rule of thumb: ~1e8–5e8 simple ops per second budget, so $\binom n2$ loops are fine only up to $n\approx 10^4$–$10^5$.

### Core template (prime modulus, O(1) queries)
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

const ll MOD = 1'000'000'007;
const int N = 1'000'005;          // >= max n used in nCr
ll fact[N], inv_fact[N];

ll power(ll b, ll e, ll m = MOD) {
    ll r = 1; b %= m;
    while (e) { if (e & 1) r = r * b % m; b = b * b % m; e >>= 1; }
    return r;
}

void init() {
    fact[0] = 1;
    for (int i = 1; i < N; i++) fact[i] = fact[i-1] * i % MOD;
    inv_fact[N-1] = power(fact[N-1], MOD - 2);          // Fermat
    for (int i = N - 1; i > 0; i--) inv_fact[i-1] = inv_fact[i] * i % MOD;
}

ll nCr(int n, int r) {
    if (r < 0 || r > n) return 0;
    return fact[n] * inv_fact[r] % MOD * inv_fact[n-r] % MOD;
}
ll nPr(int n, int r) {
    if (r < 0 || r > n) return 0;
    return fact[n] * inv_fact[n-r] % MOD;
}
```

### Formulas
```cpp
ll multinomial(const vector<int>& parts) {          // n!/(n1! n2! ...)
    int n = accumulate(parts.begin(), parts.end(), 0);
    ll res = fact[n];
    for (int p : parts) res = res * inv_fact[p] % MOD;
    return res;
}
ll stars_bars(int n, int k)     { return nCr(n + k - 1, k); }   // k identical -> n boxes, >= 0
ll stars_bars_pos(int n, int k) { return nCr(k - 1, n - 1); }   // each box >= 1
ll lattice(int m, int n)        { return nCr(m + n, m); }       // R/U paths (0,0)->(m,n)
ll catalan(int n)               { return nCr(2*n, n) * power(n + 1, MOD - 2) % MOD; }
ll circular(int n)              { return fact[n-1]; }           // round table
ll necklace_flip(int n)         { return fact[n-1] * power(2, MOD - 2) % MOD; } // n >= 3
```

### Exact small values (no modulus, n <= ~60)
```cpp
unsigned long long C_exact(int n, int k) {
    k = min(k, n - k);
    __int128 res = 1;
    for (int i = 1; i <= k; i++) res = res * (n - k + i) / i;   // always divisible at each step
    return (unsigned long long)res;
}
```

### Pascal DP — any modulus, no division (n <= ~2000)
```cpp
static int Cn[2005][2005];
void pascal(int n, int mod) {
    for (int i = 0; i <= n; i++) {
        Cn[i][0] = 1 % mod;
        for (int j = 1; j <= i; j++)
            Cn[i][j] = (Cn[i-1][j-1] + (j <= i-1 ? Cn[i-1][j] : 0)) % mod;
    }
}
```
(Fast replacement for the textbook's exponential recursive `pascal(n,k)` — Problem 11.)

### Derangements & Stirling numbers
```cpp
ll derangements(int n) {
    vector<ll> D(max(n + 1, 2));
    D[0] = 1; D[1] = 0;
    for (int i = 2; i <= n; i++) D[i] = (ll)(i - 1) * ((D[i-1] + D[i-2]) % MOD) % MOD;
    return D[n];
}
// S(n,k) = S(n-1,k-1) + k*S(n-1,k)
static int S2[2005][2005];
void stirling2(int n) {
    S2[0][0] = 1;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= i; j++)
            S2[i][j] = (S2[i-1][j-1] + (ll)j * S2[i-1][j]) % MOD;
}
```

### Paths with blocked cells (Gerald & Giant Chess pattern)
```cpp
ll paths(int r1, int c1, int r2, int c2) {          // R/D moves
    if (r2 < r1 || c2 < c1) return 0;
    return nCr((r2 - r1) + (c2 - c1), r2 - r1);     // N must be >= h + w
}
ll solve(int h, int w, vector<pair<int,int>> b) {
    sort(b.begin(), b.end());
    b.push_back({h, w});
    int k = b.size();
    vector<ll> dp(k);
    for (int i = 0; i < k; i++) {
        dp[i] = paths(1, 1, b[i].first, b[i].second);
        for (int j = 0; j < i; j++)
            dp[i] = (dp[i] - dp[j] * paths(b[j].first, b[j].second, b[i].first, b[i].second)) % MOD;
        dp[i] = (dp[i] % MOD + MOD) % MOD;
    }
    return dp[k-1];
}
```

### Example main (CSES "Binomial Coefficients")
```cpp
int main() {
    ios::sync_with_stdio(false); cin.tie(nullptr);
    init();
    int q; cin >> q;
    while (q--) { int a, b; cin >> a >> b; cout << nCr(a, b) << "\n"; }
}
```

### Loop-count example in C++ (parity pairs, from Your turn 1.1)
```cpp
// pairs (i<j) with a_i + a_j even  ->  C(e,2) + C(o,2), O(n)
long long e = 0, o = 0;
for (int i = 0; i < n; i++) (a[i] % 2 == 0 ? e : o)++;
cout << e*(e-1)/2 + o*(o-1)/2;
```

---

# 6. Textbook problems with answers

> [!info] Try each first. Answers are condensed; "(assume …)" shows where the statement is ambiguous.

## Section 1.1 (in-text exercises)
- **1.1-1** $\binom n2=n(n-1)/2$ comparisons.
- **1.1-2** $rmn$ multiplications.
- **1.1-3** $\frac{n(n+1)}2+n-2$.
- **1.1-4** $52^4+\dots+52^8$ passwords; 4-letter fraction ≈ 0.000014 %.

## Section 1.1 problems
1. Insertion sort: max comparisons $\binom n2$; occurs for a list in **strictly decreasing order** (every element must move all the way to the front).
2. $52\cdot51=2652$.
3. $52\cdot51\cdot50=132{,}600$.
4. Day $i$ gives $2^{i-1}$ pennies: day 20 receipt $=2^{19}=524{,}288$; **total** after 20 days $=2^{20}-1=1{,}048{,}575$; day $n$: receive $2^{n-1}$, total $2^n-1$. Product principle for each day's amount, sum principle for the total.
5. $5\cdot3\cdot3\cdot3=135$.
6. Ten-digit numbers (no leading 0): $9\cdot10^9$. No two consecutive equal: $9^{10}=3{,}486{,}784{,}401$. At least one equal pair: $9\cdot10^9-9^{10}=5{,}513{,}215{,}599$.
7. Administrators together & students together: $2\cdot4!\cdot4!=1152$. Alternating: also $2\cdot4!\cdot4!=1152$.
8. Two-scoop cone, order ignored, (assume two **different** flavors) $\binom{10}2=45$; if the same flavor twice is allowed: $\binom{11}2=55$.
9. Order matters: $10\cdot9=90$ (or $10^2=100$ if repeats allowed).
10. Sundae: $45\cdot3\cdot2^3=1080$ (allowing repeats: $55\cdot3\cdot8=1320$).
11. Three-way sundae: $\binom{10}3\cdot3\cdot8=120\cdot24=2880$ (with repeats: $\binom{12}3\cdot24=5280$).
12. Functions $S\to T$: $t^s$.
13. (a) $k$-element permutations: $n^{\underline k}=\frac{n!}{(n-k)!}$. (b) Permutations (bijections) of $N$: $n!$ = the $k=n$ case of (a), because a list of all $n$ elements *is* a one-to-one function from positions to elements. (c) On a finite set: if $f$ is one-to-one, the $|S|$ images are distinct so they fill $S$ ⇒ onto; if onto, $|S|$ elements must map to $|S|$ values with none missing ⇒ no collisions ⇒ one-to-one.

## Section 1.2 problems
1. $\binom{12}3=\binom{12}9=220$; $\binom{12}4=495$, so multiply by $9/4$.
2. Row 10: `1 10 45 120 210 252 210 120 45 10 1`.
3. Algebraic proof: $\frac{(n-1)!}{(k-1)!(n-k)!}+\frac{(n-1)!}{k!(n-k-1)!}=\frac{(n-1)!}{k!(n-k)!}\,[k+(n-k)]=\binom nk$.
4. (a) $x^5+5x^4+10x^3+10x^2+5x+1$ (b) $x^5+5x^4y+10x^3y^2+10x^2y^3+5xy^4+y^5$ (c) $x^5+10x^4+40x^3+80x^2+80x+32$ (d) $x^5-5x^4+10x^3-10x^2+5x-1$.
5. In $(x+y)^4$: $\binom4k$ counts which $k$ of the 4 factors give $y$ ($x^{4-k}y^k$): $1,4,6,4,1$.
6. $\frac{10!}{3!\,3!\,4!}=4200$ — it's a labelling of 10 chairs with 3 labels (green×3, blue×3, red×4).
7. Coefficient of $x_1^{n_1}\cdots x_k^{n_k}$ in $(x_1+\dots+x_k)^n$ is the multinomial coefficient (label each of the $n$ factors by which $x_i$ you pick).
8. $\binom{m+n}{m}$.
9. Choosing $k$ x's gives $\sum_k\binom nk x^k y^{n-k}$ — same theorem, by symmetry $\binom nk=\binom n{n-k}$.
10. Labelling = the four groups are **distinguishable** ($\frac{12!}{(3!)^4}=369{,}600$); choosing disjoint sets = groups **unordered** (divide by $4!$): four 3-sets $=15{,}400$; three 4-sets $=\frac{12!}{(4!)^3\,3!}=5775$ (labelled: 34,650).
11. Officers distinct, none on committee: $20\cdot19\cdot18\cdot17\cdot\binom{16}3=116{,}280\cdot560=65{,}116{,}800$. Officers may serve on committee: $116{,}280\cdot\binom{20}3=116{,}280\cdot1140=132{,}559{,}200$.
12. $\binom nk\binom kj=\binom nj\binom{n-j}{k-j}$: choose a $k$-committee then a $j$-subcommittee, **or** choose the $j$-subcommittee first then fill in the other $k-j$ members. (Algebraic proof: expand both sides, factorials cancel.)
13. $\binom96=\binom85+\binom86$; need $\binom74,\binom75,\binom76$ → $\binom85=56,\binom86=28$ ⇒ **84**.
14. Put $x=1,y=-1$ in $(x+y)^n$ ($n>0$): $0=\sum(-1)^i\binom ni$ ⇒ even-size subsets = odd-size subsets $=2^{n-1}$.
15. Differentiate $(1+x)^n=\sum\binom ni x^i$: $n(1+x)^{n-1}=\sum i\binom ni x^{i-1}$; set $x=1$: $\sum i\binom ni=n2^{n-1}$.
16. **False.** $n=4,k=2$: $\binom42=6$ but $\binom20+\binom21+\binom22=4$. True analogue: $\binom nk=\binom{n-2}{k-2}+2\binom{n-2}{k-1}+\binom{n-2}{k}$ (apply Pascal twice).

## Section 1.3 problems
1. $(n-1)!$.
2. $n!$ if the ends of the scarf are distinguishable (assume); $n!/2$ if the scarf can be flipped.
3. $\binom52=10$; equivalence: $\frac{5!}{3!\,2!}=10$.
4. Distributing $k$ identical apples to $n$ children: $\binom{n+k-1}{k}$.
5. $n!\,(n-1)!$.
6. $\frac{1}{n+1}\binom{2n}{n}$ (Catalan).
7. (a) $S(n,n)=1$ (all singletons), $S(n,1)=1$ (one class). (b) Recurrence as in §5.5. (c) Table:

|n\k|1|2|3|4|5|6|
|---|---|---|---|---|---|---|
|1|1| | | | | |
|2|1|1| | | | |
|3|1|3|1| | | |
|4|1|7|6|1| | |
|5|1|15|25|10|1| |
|6|1|31|90|65|15|1|

8. $k$ distinct books on $n$ shelves: $n(n+1)\cdots(n+k-1)=\frac{(n+k-1)!}{(n-1)!}$. Three views: (i) place books one by one — the $i$-th book has $n+i-1$ spots (next to any earlier book or at the start of a shelf); (ii) arrange the $k$ books in a list ($k!$) and insert $n-1$ dividers ($\binom{n+k-1}{k}$ positions) ⇒ $k!\binom{n+k-1}k$ ✔ same; (iii) equivalence-class count of lists with $n-1$ identical separators.
9. Lists: $\binom42=6$; classes {RRBB,RBBR,BBRR,BRRB}, {RBRB,BRBR}; Theorem 1.3.1 doesn't apply (class sizes 4 and 2).
10. "Greater than": only transitive. "Brother of": none of the three in general (not reflexive, not symmetric if one is a sister, not transitive since $A=C$ is excluded). "Sibling of": symmetric only. "Sibling of **or is**": reflexive + symmetric + transitive ⇒ equivalence. (a) Equivalence classes ⇒ same-class relation is reflexive/symmetric/transitive. (b) If $z\in S_x\cap S_y$ then by symmetry+transitivity $S_x=S_y$, so classes are disjoint or identical, and reflexivity puts $x\in S_x$ so they cover $S$ ⇒ partition. (c) Parts (a)+(b) give both directions.
11. The recursive function makes about $\binom nk$ (exponential) calls, recomputing subproblems. Faster: DP table (§5.2) or the multiplicative loop `res = res*(n-i)//(i+1)` in $O(k)$.
12. (a) $n^k$ (b) $n^{\underline k}$ (c) $\binom{n+k-1}k$ (d) $\binom nk$ (e) $n^{\underline k}$ (f) $n^k$ (g) $\binom nk$ (h) $\binom{n+k-1}k$ (i) $n^{\underline k}$ (j) $\binom{n+k-1}k$ (k) $n^{\underline k}$.

---

# 7. Extra topics CP needs
*(not in this PDF chapter, but the very next things you will meet)*

- **Inclusion–exclusion (PIE):** $|A\cup B|=|A|+|B|-|A\cap B|$; general: alternating sum over intersections. Used for "count with forbidden properties", derangements $D_n=n!\sum_{i=0}^n\frac{(-1)^i}{i!}$.
- **Hockey stick:** $\sum_{i=r}^{n}\binom ir=\binom{n+1}{r+1}$.
- **Vandermonde:** $\sum_i\binom mi\binom n{k-i}=\binom{m+n}k$.
- **Lucas theorem:** $\binom nk\bmod p=\prod\binom{n_i}{k_i}$ over base-$p$ digits (huge $n$, small prime $p$).
- **Burnside's lemma:** #distinct objects under symmetry = average number of arrangements fixed by each symmetry (solves the unequal-class case, e.g. necklaces).
- **Pigeonhole, double counting, generating functions** (the NPTEL course below covers these).
- **Catalan applications:** balanced brackets, binary trees with $n$ nodes, non-crossing paths/triangulations.

---

# 8. Practice problems
*(search by name; verify tags/difficulty on the site)*

**CSES — Introductory / Math** (clean, good order)
1. Exponentiation → Exponentiation II (prerequisite for inverses)
2. **Binomial Coefficients** (template §5.3)
3. **Creating Strings II** (multinomial)
4. **Distributing Apples** (stars and bars)
5. **Christmas Party** (derangements)
6. **Bracket Sequences I** (Catalan) → II
7. **Counting Necklaces** (Burnside)

**Codeforces**
- 300C *Beautiful Numbers* (nCr mod)
- 559C *Gerald and Giant Chess* (§5.6)
- 1342E *Placing Rooks* (inclusion–exclusion + Stirling-like counting)
- Filter problemset by tag **combinatorics**, difficulty 1200–1600, solve ~30.

**Mix into your current binary-search sheet:** after solving a counting task, ask "can I count this with a formula instead of a loop?"

---

# 9. Videos

> [!note] I'm giving titles/channels rather than links (links change). Search the exact title on YouTube.

**Foundations (read-along with this PDF)**
- **NPTEL — "Combinatorics" video course** (NPTEL, free): modules on basic counting, binomial theorem, bijective proofs, inclusion–exclusion. Closest match to this chapter's proof style.
- **MIT 6.042J "Mathematics for Computer Science"** (OCW lectures): counting, binomial coefficients, generalized product rule.
- **Neso Academy — "Discrete Mathematics" playlist** (permutations, combinations, pigeonhole): easy pacing.
- **Trevor Bazett — "Discrete Math" / combinatorics videos**: very clear for stars and bars, circular permutations.
- **The Organic Chemistry Tutor — Permutations and Combinations**: many quick worked examples for drilling.

**For competitive programming**
- **Errichto (Kamil Debowski)** — combinatorics / binomial coefficients / expected value videos (also linked in the USACO Guide combinatorics module).
- **USACO Guide → Gold → Combinatorics** (text + links; best structured CP path).
- **Arabic:** search **Mostafa Saad** (Egyptian ICPC trainer) for his *Combinatorics / Math for CP* lectures, and Egyptian community channels (ECPC/ICPC "Basecamp"-style math sessions).
- **Codeforces EDU / Codeforces blog tutorials**: search "Inclusion-Exclusion tutorial" and "Calculate modulo inverses efficiently" (both exist as CF blog tutorials).
- **AoPS (Art of Problem Solving) — "Introduction to Counting & Probability"** (text/videos, cited by USACO Guide) for the proof-based side.

**Suggested order (≈2 weeks)**
1. Neso or Trevor Bazett: permutations/combinations/stars-and-bars (2–3 videos).
2. NPTEL/MIT: binomial theorem + bijective proofs.
3. Errichto/USACO Guide: nCr mod p, inverse factorials.
4. CSES counting problems (§8) → CF combinatorics tag.

---
*Source structure follows Bogart & Stein, Ch. 1 §1.1–1.3. Worked examples, Python templates, and extra topics are additions for CP use.*
