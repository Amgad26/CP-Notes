
# Stack

## 1. What is a Stack?

A **Stack** is a linear data structure that follows **LIFO** (Last In, First Out) order — the last element inserted is the first one removed.

Think of a stack of plates: you add plates to the top, and you remove plates from the top.

### Core Operations
| Operation | Description | Time Complexity |
|---|---|---|
| `push(x)` | Insert element `x` on top | O(1) |
| `pop()` | Remove and return the top element | O(1) |
| `top()` / `peek()` | Return top element without removing | O(1) |
| `isEmpty()` | Check whether stack is empty | O(1) |
| `size()` | Number of elements | O(1) |

All operations are **O(1)** because we only ever touch the top.

---

## 2. Real-World / CS Uses
- Function call stack (recursion)
- Undo/Redo in editors
- Browser back/forward history
- Expression evaluation (infix → postfix, postfix evaluation)
- Balanced parentheses / bracket matching
- Backtracking algorithms (maze solving, DFS)
- Syntax parsing (compilers)
- Reversing data (string, list)

---

## 3. Implementation

### Using STL (`std::stack`) — Practical Usage
```cpp
#include <stack>
using namespace std;

stack<int> s;
s.push(10);
s.push(20);
s.push(30);

cout << s.top() << "\n"; // 30
s.pop();
cout << s.top() << "\n"; // 20
cout << s.size() << "\n"; // 2
cout << s.empty() << "\n"; // 0 (false)
```

---

## 4. Classic Problems & Patterns

### 4.1 Balanced Parentheses
```cpp
bool isValid(string s) {
    stack<char> st;
    unordered_map<char, char> match = {{')', '('}, {']', '['}, {'}', '{'}};

    for (char c : s) {
        if (c == '(' || c == '[' || c == '{') {
            st.push(c);
        } else {
            if (st.empty() || st.top() != match[c]) return false;
            st.pop();
        }
    }
    return st.empty();
}
```

### 4.2 Infix to Postfix Conversion
```cpp
int precedence(char op) {
    if (op == '+' || op == '-') return 1;
    if (op == '*' || op == '/') return 2;
    if (op == '^') return 3;
    return 0;
}

string infixToPostfix(string expr) {
    stack<char> st;
    string result;

    for (char c : expr) {
        if (isalnum(c)) {
            result += c;
        } else if (c == '(') {
            st.push(c);
        } else if (c == ')') {
            while (!st.empty() && st.top() != '(') {
                result += st.top();
                st.pop();
            }
            st.pop(); // remove '('
        } else { // operator
            while (!st.empty() && precedence(st.top()) >= precedence(c)) {
                result += st.top();
                st.pop();
            }
            st.push(c);
        }
    }
    while (!st.empty()) {
        result += st.top();
        st.pop();
    }
    return result;
}
```

### 4.3 Evaluate Postfix Expression
```cpp
int evalPostfix(string expr) {
    stack<int> st;
    for (char c : expr) {
        if (isdigit(c)) {
            st.push(c - '0');
        } else {
            int b = st.top(); st.pop();
            int a = st.top(); st.pop();
            switch (c) {
                case '+': st.push(a + b); break;
                case '-': st.push(a - b); break;
                case '*': st.push(a * b); break;
                case '/': st.push(a / b); break;
            }
        }
    }
    return st.top();
}
```

### 4.4 Next Greater Element (Monotonic Stack)
```cpp
vector<int> nextGreaterElement(vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n, -1);
    stack<int> st; // stores indices

    for (int i = 0; i < n; i++) {
        while (!st.empty() && nums[st.top()] < nums[i]) {
            result[st.top()] = nums[i];
            st.pop();
        }
        st.push(i);
    }
    return result;
}
```
**Pattern:** Monotonic stacks are used for "next greater/smaller element" problems, histogram area, stock span, etc. Key idea: maintain a stack that is always increasing or decreasing; pop elements when the invariant breaks.

### 4.5 Largest Rectangle in Histogram (Monotonic Stack)
```cpp
int largestRectangleArea(vector<int>& heights) {
    stack<int> st;
    int maxArea = 0;
    heights.push_back(0); // sentinel

    for (int i = 0; i < heights.size(); i++) {
        while (!st.empty() && heights[st.top()] > heights[i]) {
            int h = heights[st.top()];
            st.pop();
            int width = st.empty() ? i : i - st.top() - 1;
            maxArea = max(maxArea, h * width);
        }
        st.push(i);
    }
    return maxArea;
}
```

### 4.6 Min Stack (O(1) getMin)
```cpp
class MinStack {
    stack<int> st;
    stack<int> minSt;

public:
    void push(int x) {
        st.push(x);
        if (minSt.empty() || x <= minSt.top()) minSt.push(x);
        else minSt.push(minSt.top());
    }

    void pop() {
        st.pop();
        minSt.pop();
    }

    int top() { return st.top(); }

    int getMin() { return minSt.top(); }
};
```

### 4.7 Reverse a String Using Stack
```cpp
string reverseString(string s) {
    stack<char> st;
    for (char c : s) st.push(c);

    string result;
    while (!st.empty()) {
        result += st.top();
        st.pop();
    }
    return result;
}
```

### 4.8 Implement Queue Using Two Stacks
```cpp
class QueueUsingStacks {
    stack<int> inStack, outStack;

    void transfer() {
        if (outStack.empty()) {
            while (!inStack.empty()) {
                outStack.push(inStack.top());
                inStack.pop();
            }
        }
    }

public:
    void enqueue(int x) { inStack.push(x); }

    int dequeue() {
        transfer();
        int val = outStack.top();
        outStack.pop();
        return val;
    }
};
```

---

## 5. Recursion & The Call Stack

Every recursive call is pushed onto the **call stack**; returning pops it off. This is why deep recursion can cause a **stack overflow**.

```cpp
int factorial(int n) {
    if (n == 0) return 1;      // base case
    return n * factorial(n-1); // pushes a new frame each call
}
```

Any recursive algorithm can be converted to an **iterative** one using an explicit stack — useful when recursion depth risks overflow (e.g., DFS on large graphs).

```cpp
// Iterative DFS using explicit stack
void dfsIterative(int start, vector<vector<int>>& adj) {
    stack<int> st;
    vector<bool> visited(adj.size(), false);
    st.push(start);

    while (!st.empty()) {
        int node = st.top(); st.pop();
        if (visited[node]) continue;
        visited[node] = true;
        // process node here

        for (int neighbor : adj[node]) {
            if (!visited[neighbor]) st.push(neighbor);
        }
    }
}
```

---

## 6. Common Pitfalls
- **Popping/peeking an empty stack** → check `isEmpty()` first (undefined behavior / crash otherwise).
- **Array-based stack overflow** → fixed-size arrays need capacity checks; prefer `vector`/dynamic arrays or `deque` in practice.
- **Confusing stack (LIFO) with queue (FIFO)** — different use cases entirely.
- In competitive programming, `std::stack` is a container **adapter** (defaults to `deque` underneath), not a raw container — you can't iterate over it directly.

---

## 7. Complexity Summary

| Implementation | Push | Pop | Top | Space |
|---|---|---|---|---|
| Array (static) | O(1) | O(1) | O(1) | O(n), fixed cap |
| Dynamic array (vector) | O(1) amortized | O(1) | O(1) | O(n) |
| Linked List | O(1) | O(1) | O(1) | O(n) + pointer overhead |

---

## 8. Practice Problems (by pattern)
- **Basic:** Valid Parentheses, Min Stack, Reverse a String/Linked List using Stack
- **Monotonic Stack:** Next Greater Element, Next Smaller Element, Largest Rectangle in Histogram, Daily Temperatures, Stock Span Problem
- **Expression Evaluation:** Infix→Postfix, Postfix→Infix, Evaluate Reverse Polish Notation
- **Simulation:** Asteroid Collision, Remove All Adjacent Duplicates, Simplify Path (Unix path)
- **Two-stack tricks:** Implement Queue using Stacks, Sort a Stack using another Stack

---

## 9. Quick Revision Cheat Sheet
- LIFO — last in, first out.
- All core ops are O(1).
- Backed by array, vector, or linked list.
- STL: `#include <stack>`, `stack<T> s; s.push(); s.pop(); s.top(); s.empty(); s.size();`
- Python: use a plain `list` with `.append()` / `.pop()`.
- Recognize stack problems by keywords: "matching", "nested", "next greater/smaller", "undo", "backtrack", "valid expression".
- Monotonic stack = stack kept increasing or decreasing to answer "next greater/smaller" style questions in O(n).
