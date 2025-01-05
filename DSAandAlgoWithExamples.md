
# Algorithm Solutions in JavaScript and Python

This document provides solutions to 20 common algorithm problems in both JavaScript and Python, with explanations for each.

---

## 1. Reverse a Linked List

**Explanation**: Reversing a linked list involves changing the direction of the links between nodes.

### JavaScript
```javascript
class Node {
    constructor(value) {
        this.value = value;
        this.next = null;
    }
}

function reverseLinkedList(head) {
    let prev = null;
    let current = head;
    while (current !== null) {
        let next = current.next;
        current.next = prev;
        prev = current;
        current = next;
    }
    return prev;
}
```

### Python
```python
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None

def reverse_linked_list(head):
    prev = None
    current = head
    while current:
        next_node = current.next
        current.next = prev
        prev = current
        current = next_node
    return prev
```

---

## 2. Find the Middle Element of a Linked List

**Explanation**: Use two pointers: one moves twice as fast as the other. When the faster pointer reaches the end, the slower pointer is at the middle.

### JavaScript
```javascript
function findMiddle(head) {
    let slow = head;
    let fast = head;
    while (fast !== null && fast.next !== null) {
        slow = slow.next;
        fast = fast.next.next;
    }
    return slow.value;
}
```

### Python
```python
def find_middle(head):
    slow = head
    fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow.value
```

---

## 3. Implement a Stack Using Arrays

**Explanation**: A stack is a LIFO (Last In, First Out) data structure. Here, we use an array to mimic stack operations.

### JavaScript
```javascript
class Stack {
    constructor() {
        this.stack = [];
    }

    push(item) {
        this.stack.push(item);
    }

    pop() {
        return this.stack.pop();
    }

    peek() {
        return this.stack[this.stack.length - 1];
    }

    isEmpty() {
        return this.stack.length === 0;
    }
}
```

### Python
```python
class Stack:
    def __init__(self):
        self.stack = []

    def push(self, item):
        self.stack.append(item)

    def pop(self):
        return self.stack.pop()

    def peek(self):
        return self.stack[-1] if self.stack else None

    def is_empty(self):
        return len(self.stack) == 0
```

---

## 4. Implement a Queue Using Arrays

**Explanation**: A queue is a FIFO (First In, First Out) data structure. We can use an array to implement enqueue and dequeue operations.

### JavaScript
```javascript
class Queue {
    constructor() {
        this.queue = [];
    }

    enqueue(item) {
        this.queue.push(item);
    }

    dequeue() {
        return this.queue.shift();
    }

    front() {
        return this.queue[0];
    }

    isEmpty() {
        return this.queue.length === 0;
    }
}
```

### Python
```python
class Queue:
    def __init__(self):
        self.queue = []

    def enqueue(self, item):
        self.queue.append(item)

    def dequeue(self):
        return self.queue.pop(0) if not self.is_empty() else None

    def front(self):
        return self.queue[0] if not self.is_empty() else None

    def is_empty(self):
        return len(self.queue) == 0
```

---

## 5. Find the Factorial of a Number Using Recursion

**Explanation**: The factorial of a number `n` is the product of all positive integers less than or equal to `n`.

### JavaScript
```javascript
function factorial(n) {
    if (n === 0 || n === 1) return 1;
    return n * factorial(n - 1);
}
```

### Python
```python
def factorial(n):
    if n == 0 or n == 1:
        return 1
    return n * factorial(n - 1)
```

---

## 6. Implement Binary Search in an Array

**Explanation**: Binary search finds an element in a sorted array by repeatedly dividing the search interval in half.

### JavaScript
```javascript
function binarySearch(arr, target) {
    let left = 0, right = arr.length - 1;
    while (left <= right) {
        const mid = Math.floor((left + right) / 2);
        if (arr[mid] === target) return mid;
        if (arr[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return -1;
}
```

### Python
```python
def binary_search(arr, target):
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1
```

---

## 7. Find the Largest/Smallest Element in an Array

**Explanation**: The largest or smallest element can be found using array traversal.

### JavaScript
```javascript
function findLargest(arr) {
    return Math.max(...arr);
}

function findSmallest(arr) {
    return Math.min(...arr);
}
```

### Python
```python
def find_largest(arr):
    return max(arr)

def find_smallest(arr):
    return min(arr)
```

---


# Algorithm Solutions in JavaScript and Python

This document provides solutions to common algorithm problems in both JavaScript and Python, with explanations for each.

---

## 14. Reverse a Binary Tree

**Explanation**: Reversing (or mirroring) a binary tree involves swapping the left and right children of all nodes in the tree.

### JavaScript
```javascript
function reverseBinaryTree(root) {
    if (root === null) return null;

    let temp = root.left;
    root.left = root.right;
    root.right = temp;

    reverseBinaryTree(root.left);
    reverseBinaryTree(root.right);

    return root;
}
```

### Python
```python
def reverse_binary_tree(root):
    if root is None:
        return None

    root.left, root.right = root.right, root.left

    reverse_binary_tree(root.left)
    reverse_binary_tree(root.right)

    return root
```

---

## 15. Find the Height of a Binary Tree

**Explanation**: The height of a binary tree is the maximum depth from the root node to any leaf node.

### JavaScript
```javascript
function findHeight(root) {
    if (root === null) return 0;

    const leftHeight = findHeight(root.left);
    const rightHeight = findHeight(root.right);

    return Math.max(leftHeight, rightHeight) + 1;
}
```

### Python
```python
def find_height(root):
    if root is None:
        return 0

    left_height = find_height(root.left)
    right_height = find_height(root.right)

    return max(left_height, right_height) + 1
```

---

## 16. Implement Depth-First Search (DFS) on a Graph

**Explanation**: DFS explores nodes by going as deep as possible along each branch before backtracking.

### JavaScript
```javascript
function dfs(graph, start, visited = new Set()) {
    if (!visited.has(start)) {
        console.log(start);
        visited.add(start);

        for (const neighbor of graph[start]) {
            dfs(graph, neighbor, visited);
        }
    }
}
```

### Python
```python
def dfs(graph, start, visited=None):
    if visited is None:
        visited = set()

    if start not in visited:
        print(start)
        visited.add(start)

        for neighbor in graph[start]:
            dfs(graph, neighbor, visited)
```

---

## 17. Implement Breadth-First Search (BFS) on a Graph

**Explanation**: BFS explores nodes layer by layer, starting from the given node.

### JavaScript
```javascript
function bfs(graph, start) {
    const queue = [start];
    const visited = new Set();

    while (queue.length > 0) {
        const node = queue.shift();

        if (!visited.has(node)) {
            console.log(node);
            visited.add(node);

            for (const neighbor of graph[node]) {
                if (!visited.has(neighbor)) {
                    queue.push(neighbor);
                }
            }
        }
    }
}
```

### Python
```python
from collections import deque

def bfs(graph, start):
    queue = deque([start])
    visited = set()

    while queue:
        node = queue.popleft()

        if node not in visited:
            print(node)
            visited.add(node)

            for neighbor in graph[node]:
                if neighbor not in visited:
                    queue.append(neighbor)
```

---

## 18. Check if a Graph is Connected

**Explanation**: A graph is connected if all its nodes are reachable from any starting node.

### JavaScript
```javascript
function isGraphConnected(graph) {
    const visited = new Set();

    function dfs(node) {
        if (!visited.has(node)) {
            visited.add(node);
            for (const neighbor of graph[node]) {
                dfs(neighbor);
            }
        }
    }

    dfs(Object.keys(graph)[0]);
    return visited.size === Object.keys(graph).length;
}
```

### Python
```python
def is_graph_connected(graph):
    visited = set()

    def dfs(node):
        if node not in visited:
            visited.add(node)
            for neighbor in graph[node]:
                dfs(neighbor)

    dfs(next(iter(graph)))
    return len(visited) == len(graph)
```

---

## 19. Implement Dijkstra's Algorithm for Shortest Path

**Explanation**: Dijkstra's algorithm finds the shortest path from a source node to all other nodes in a weighted graph.

### JavaScript
```javascript
function dijkstra(graph, start) {
    const distances = {};
    const visited = new Set();

    Object.keys(graph).forEach(node => distances[node] = Infinity);
    distances[start] = 0;

    while (visited.size < Object.keys(graph).length) {
        const unvisitedNodes = Object.keys(graph).filter(node => !visited.has(node));
        const current = unvisitedNodes.reduce((minNode, node) =>
            distances[node] < distances[minNode] ? node : minNode, unvisitedNodes[0]);

        visited.add(current);

        for (const [neighbor, weight] of Object.entries(graph[current])) {
            const newDist = distances[current] + weight;
            if (newDist < distances[neighbor]) {
                distances[neighbor] = newDist;
            }
        }
    }

    return distances;
}
```

### Python
```python
import heapq

def dijkstra(graph, start):
    distances = {node: float('inf') for node in graph}
    distances[start] = 0
    pq = [(0, start)]

    while pq:
        current_distance, current_node = heapq.heappop(pq)

        if current_distance > distances[current_node]:
            continue

        for neighbor, weight in graph[current_node].items():
            distance = current_distance + weight

            if distance < distances[neighbor]:
                distances[neighbor] = distance
                heapq.heappush(pq, (distance, neighbor))

    return distances
```

---

## 20. Implement Prim's Algorithm for Minimum Spanning Tree

**Explanation**: Prim's algorithm builds a minimum spanning tree by starting with one vertex and adding the smallest edge that connects a visited node to an unvisited one.

### JavaScript
```javascript
function prim(graph, start) {
    const mst = [];
    const visited = new Set();
    const edges = [];

    visited.add(start);

    for (const [neighbor, weight] of Object.entries(graph[start])) {
        edges.push([start, neighbor, weight]);
    }

    while (edges.length > 0) {
        edges.sort((a, b) => a[2] - b[2]);
        const [from, to, weight] = edges.shift();

        if (!visited.has(to)) {
            visited.add(to);
            mst.push([from, to, weight]);

            for (const [neighbor, w] of Object.entries(graph[to])) {
                if (!visited.has(neighbor)) {
                    edges.push([to, neighbor, w]);
                }
            }
        }
    }

    return mst;
}
```

### Python
```python
def prim(graph, start):
    mst = []
    visited = set()
    edges = []

    visited.add(start)
    for neighbor, weight in graph[start].items():
        edges.append((weight, start, neighbor))

    while edges:
        edges.sort()
        weight, from_node, to_node = edges.pop(0)

        if to_node not in visited:
            visited.add(to_node)
            mst.append((from_node, to_node, weight))

            for neighbor, w in graph[to_node].items():
                if neighbor not in visited:
                    edges.append((w, to_node, neighbor))

    return mst
```


