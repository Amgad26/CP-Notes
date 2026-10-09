
# Queue

## 1. What is a Queue?

A **Queue** is a linear data structure that follows **FIFO** (First In, First Out) order — the first element inserted is the first one removed.

Think of people standing in line: the first person to join is the first person served.

### Core Operations
| Operation | Description | Time Complexity |
|---|---|---|
| `enqueue(x)` / `push(x)` | Insert element `x` at the back | O(1) |
| `dequeue()` / `pop()` | Remove and return the front element | O(1) |
| `front()` | Return front element without removing | O(1) |
| `back()` | Return last element without removing | O(1) |
| `isEmpty()` | Check whether queue is empty | O(1) |
| `size()` | Number of elements | O(1) |

---

## 2. Real-World / CS Uses
- CPU task scheduling, print queue, printer spooling
- BFS (Breadth-First Search) in graphs/trees
- Handling requests in web servers (one thread, many requests)
- Message queues (Kafka, RabbitMQ concepts)
- Buffering (IO buffers, streaming data)
- Level-order traversal of a tree
- Caching (LRU cache uses a deque)
- Sliding window problems (using a deque)

---

## 3. Types of Queues
| Type | Description |
|---|---|
| **Simple Queue** | Standard FIFO, insert at back, remove from front |
| **Circular Queue** | Fixed-size array queue where rear wraps to front, avoiding wasted space |
| **Deque (Double-Ended Queue)** | Insert/remove from both front and back |
| **Priority Queue** | Elements dequeued by priority, not insertion order (usually via heap) |

---

## 4. Implementations

### 4.2 Circular Queue (Array-based, capacity-fixed)
```cpp
class CircularQueue {
    vector<int> arr;
    int frontIdx = -1, rearIdx = -1, capacity, count = 0;

public:
    CircularQueue(int cap) : capacity(cap) { arr.resize(cap); }

    bool isFull() { return count == capacity; }
    bool isEmpty() { return count == 0; }

    void enqueue(int x) {
        if (isFull()) { cout << "Overflow\n"; return; }
        if (isEmpty()) frontIdx = 0;
        rearIdx = (rearIdx + 1) % capacity;
        arr[rearIdx] = x;
        count++;
    }

    void dequeue() {
        if (isEmpty()) { cout << "Underflow\n"; return; }
        frontIdx = (frontIdx + 1) % capacity;
        count--;
        if (isEmpty()) frontIdx = rearIdx = -1;
    }

    int front() { return arr[frontIdx]; }
    int size() { return count; }
};
```

### 4.4 Using STL (`std::queue`) — Practical Usage
```cpp
#include <queue>
using namespace std;

queue<int> q;
q.push(10);
q.push(20);
q.push(30);

cout << q.front() << "\n"; // 10
cout << q.back() << "\n";  // 30
q.pop();
cout << q.front() << "\n"; // 20
cout << q.size() << "\n";  // 2
cout << q.empty() << "\n"; // 0 (false)
```

### 4.5 Using `std::deque` (double-ended queue)
```cpp
#include <deque>
using namespace std;

deque<int> dq;
dq.push_back(1);
dq.push_front(0);
dq.push_back(2);       // dq = [0, 1, 2]
dq.pop_front();          // removes 0
dq.pop_back();            // removes 2
cout << dq.front() << " " << dq.back(); // both = 1
```

### 4.6 Priority Queue (STL)
```cpp
#include <queue>
using namespace std;

// Max-heap by default
priority_queue<int> maxHeap;
maxHeap.push(5); maxHeap.push(1); maxHeap.push(10);
cout << maxHeap.top(); // 10

// Min-heap
priority_queue<int, vector<int>, greater<int>> minHeap;
minHeap.push(5); minHeap.push(1); minHeap.push(10);
cout << minHeap.top(); // 1

// Custom comparator (pairs sorted by second element ascending)
auto cmp = [](pair<int,int>& a, pair<int,int>& b) {
    return a.second > b.second;
};
priority_queue<pair<int,int>, vector<pair<int,int>>, decltype(cmp)> pq(cmp);

// Custom struct comparator
struct Cmp {
	bool operator()(const pair<int,int>& a, const pair<int,int>& b) const {
		return a.first > b.first;
	}
};
priority_queue<pair<int,int>, vector<pair<int,int>>, Cmp> pq2;
```

---

## 5. Classic Problems & Patterns

### 5.1 BFS (Breadth-First Search) on a Graph
```cpp
void bfs(int start, vector<vector<int>>& adj) {
    vector<bool> visited(adj.size(), false);
    queue<int> q;

    visited[start] = true;
    q.push(start);

    while (!q.empty()) {
        int node = q.front(); q.pop();
        // process node here

        for (int neighbor : adj[node]) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                q.push(neighbor);
            }
        }
    }
}
```

### 5.2 Level-Order Tree Traversal
```cpp
struct TreeNode {
    int val;
    TreeNode *left, *right;
};

vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> result;
    if (!root) return result;

    queue<TreeNode*> q;
    q.push(root);

    while (!q.empty()) {
        int levelSize = q.size();
        vector<int> level;

        for (int i = 0; i < levelSize; i++) {
            TreeNode* node = q.front(); q.pop();
            level.push_back(node->val);

            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
        result.push_back(level);
    }
    return result;
}
```

### 5.3 Implement Stack Using Two Queues
```cpp
class StackUsingQueues {
    queue<int> q1, q2;

public:
    void push(int x) {
        q2.push(x);
        while (!q1.empty()) {
            q2.push(q1.front());
            q1.pop();
        }
        swap(q1, q2);
    }

    void pop() { q1.pop(); }

    int top() { return q1.front(); }

    bool isEmpty() { return q1.empty(); }
};
```

### 5.4 Sliding Window Maximum (Monotonic Deque)
```cpp
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    deque<int> dq; // stores indices, values decreasing
    vector<int> result;

    for (int i = 0; i < nums.size(); i++) {
        // remove indices out of window
        if (!dq.empty() && dq.front() <= i - k) dq.pop_front();

        // maintain decreasing order
        while (!dq.empty() && nums[dq.back()] < nums[i]) dq.pop_back();

        dq.push_back(i);

        if (i >= k - 1) result.push_back(nums[dq.front()]);
    }
    return result;
}
```
**Pattern:** Monotonic deque keeps candidates for max/min in the current window in O(1) amortized per step — classic sliding window optimization.

### 5.5 First Non-Repeating Character in a Stream
```cpp
void firstNonRepeating(string stream) {
    queue<char> q;
    int freq[26] = {0};

    for (char c : stream) {
        q.push(c);
        freq[c - 'a']++;

        while (!q.empty() && freq[q.front() - 'a'] > 1) {
            q.pop();
        }

        if (q.empty()) cout << -1 << " ";
        else cout << q.front() << " ";
    }
}
```

### 5.6 Rotten Oranges (Multi-source BFS)
```cpp
int orangesRotting(vector<vector<int>>& grid) {
    int rows = grid.size(), cols = grid[0].size();
    queue<pair<int,int>> q;
    int fresh = 0;

    for (int i = 0; i < rows; i++)
        for (int j = 0; j < cols; j++) {
            if (grid[i][j] == 2) q.push({i, j});
            if (grid[i][j] == 1) fresh++;
        }

    int minutes = 0;
    vector<pair<int,int>> dirs = {{0,1},{0,-1},{1,0},{-1,0}};

    while (!q.empty() && fresh > 0) {
        int size = q.size();
        for (int i = 0; i < size; i++) {
            auto [r, c] = q.front(); q.pop();
            for (auto [dr, dc] : dirs) {
                int nr = r + dr, nc = c + dc;
                if (nr >= 0 && nr < rows && nc >= 0 && nc < cols && grid[nr][nc] == 1) {
                    grid[nr][nc] = 2;
                    fresh--;
                    q.push({nr, nc});
                }
            }
        }
        minutes++;
    }
    return fresh == 0 ? minutes : -1;
}
```

### 5.7 Circular Tour / Gas Station (Queue-style greedy)
```cpp
int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
    int total = 0, tank = 0, start = 0;
    for (int i = 0; i < gas.size(); i++) {
        int diff = gas[i] - cost[i];
        total += diff;
        tank += diff;
        if (tank < 0) {
            start = i + 1;
            tank = 0;
        }
    }
    return total >= 0 ? start : -1;
}
```

---

## 6. Queue vs Stack vs Deque

| Feature | Stack | Queue | Deque |
|---|---|---|---|
| Order | LIFO | FIFO | Both ends |
| Insert | Top only | Back only | Front & Back |
| Remove | Top only | Front only | Front & Back |
| Use case | Undo, recursion, DFS | Scheduling, BFS | Sliding window, both-end ops |

---

## 7. Common Pitfalls
- **Using a plain array with front-shifting** → dequeuing from the front is O(n) unless you use a circular buffer or two pointers.
- **Python: using `list.pop(0)`** → O(n); always use `collections.deque` for O(1) queue ops.
- **Circular queue confusion** → must track `count` (or waste one slot) to distinguish "empty" vs "full" when `front == rear`.
- **`std::queue` doesn't support iteration** → it's a container adapter (default: `deque`); use `std::deque` directly if you need indexing/iteration.
- **Priority queue is NOT FIFO** → it's ordered by priority, easy to confuse with a regular queue.

---

## 8. Complexity Summary

| Implementation | Enqueue | Dequeue | Front | Space |
|---|---|---|---|---|
| Array (naive, shifting) | O(1) | O(n) | O(1) | O(n) |
| Circular Array | O(1) | O(1) | O(1) | O(n), fixed cap |
| Linked List | O(1) | O(1) | O(1) | O(n) + pointer overhead |
| `std::deque` / `collections.deque` | O(1) | O(1) | O(1) | O(n) |
| Priority Queue (heap) | O(log n) | O(log n) | O(1) | O(n) |

---

## 9. Practice Problems (by pattern)
- **Basic:** Implement Queue using Array/Linked List, Implement Stack using Queues, Design Circular Queue
- **BFS-based:** Level Order Traversal, Rotten Oranges, Shortest Path in Binary Matrix, Word Ladder
- **Monotonic Deque:** Sliding Window Maximum/Minimum, Shortest Subarray with Sum at Least K
- **Priority Queue / Heap:** Kth Largest Element, Merge K Sorted Lists, Top K Frequent Elements, Dijkstra's Algorithm
- **Stream processing:** First Non-Repeating Character in a Stream, Moving Average from Data Stream
- **Design:** LRU Cache (deque + hashmap), Task Scheduler

---

## 10. Quick Revision Cheat Sheet
- FIFO — first in, first out.
- Core ops (enqueue/dequeue/front) are O(1) with the right implementation.
- Avoid front-shifting arrays; use circular buffer, linked list, or `deque`.
- STL: `#include <queue>`, `queue<T> q; q.push(); q.pop(); q.front(); q.back(); q.empty(); q.size();`
- Deque: `#include <deque>`, supports `push_front/back`, `pop_front/back` — all O(1).
- Priority Queue: `priority_queue<T>` (max-heap default), pass `greater<T>` for min-heap.
- Python: always use `collections.deque`, never a plain `list`, for queue behavior.
- Recognize queue problems by keywords: "level by level", "shortest path (unweighted)", "process in order", "first come first served", "sliding window max/min".
- BFS = queue. DFS = stack (or recursion).
