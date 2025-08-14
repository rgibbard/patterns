# 🧠 Data Structures & Algorithm Patterns

A comprehensive, annotated reference for mastering technical interviews through reusable DSA patterns. Covers 20+ core data structures (arrays, hash maps, trees, graphs, tries) and algorithmic strategies (two pointers, sliding window, union-find, greedy, backtracking). Each entry includes:

- ✅ Recognition cues and common prompts
- ⏱️ Time complexity and use-case framing
- 🧠 Python implementations with commentary

Designed for fast recall, interview prep, and real-world coding fluency.

<br>

# 🧱 Data Structures

<br>

## 1. 📦 Array / List

⚒️ **Use Case:** Store ordered elements, fast index access

⏰ **Time Complexity:**

- Access: O(1)
- Insert/Delete: O(n) (due to shifting)

✅ **Recognize When:**
- You need fast random access
- Elements are stored in a fixed order
- Problem involves "subarrays", "sliding window", or "prefix sum"

🔍 **Common Prompts:**

- "Find max subarray"
- "Rotate array"
- "Two sum"

🎗 **Tips:**
- Arrays are indexed by 0
- Use `range(len(arr))` to index through all elements
- Python slices `arr[s:e]` are inclusive of the start index and exclusive of the last
- Negative indexing starts at -1 and returns the last n element
- You can initialize an array like this: `[0] * n` where `n` is an integer of the size.

```python
arr = ['a', 'b', 'c']
arr.append(4)       # Add to end
arr.pop()           # Remove last
arr[1]              # Returns 'a'
arr[0:2]            # Returns ['a', 'b']
arr[-1]             # Returns 'c'
```


## 2. 🧺 Hash Map / Dictionary

⚒️ **Use Case:** Key-value storage, frequency counting, fast lookup

⏰ **Time Complexity:**

- Access/Insert/Delete: O(1) average, O(n) worst-case

✅ **Recognize When:**

- You need constant-time lookup
- Problem involves counting, grouping, or mapping
- Avoid nested loops with hash-based access

🔍 **Common Prompts:**

- "Sorted Two sum"
- "Group anagrams"
- "Find duplicates"

```python
freq = {}
freq['a'] = freq.get('a', 0) + 1  # Count frequency
if 'a' in freq:
    print(freq['a'])              # Lookup value
```


## 3. 🧮 Set

⚒️ **Use Case:** Store unique elements, fast membership tests

⏰ **Time Complexity:**

- Add/Delete/Lookup: O(1) average

✅ **Recognize When:**

- You need to track uniqueness
- Problem involves "distinct", "intersection", or "duplicates"

🔍 **Common Prompts:**

- "Longest substring without repeats"
- "Find intersection of arrays"

```python
seen = set()
seen.add(1)
if 2 in seen:
    print("Exists")
seen.remove(1)
```


## 4. 📚 Stack (LIFO)

⚒️ **Use Case:** Reverse order, undo operations, nested structure parsing

⏰ **Time Complexity:**

- Push/Pop/Peek: O(1)

✅ **Recognize When:**

- You need to process items in reverse order
- Problem involves "next greater element", "valid parentheses", or "DFS"

🔍 **Common Prompts:**

- "Evaluate expression"
- "Daily temperatures"
- "Backspace string compare"

```python
stack = []
stack.append(5)     # Push
top = stack[-1]     # Peek
stack.pop()         # Pop
```


## 5. 📖 Queue (FIFO)

⚒️ **Use Case:** Process items in arrival order, level-order traversal

⏰ **Time Complexity:**

- Enqueue/Dequeue: O(1) with deque

✅ **Recognize When:**

- You need to process items in order
- Problem involves "BFS", "task scheduling", or "sliding window max"

🔍 **Common Prompts:**

- "Level order traversal"
- "Moving average from data stream"

```python
from collections import deque
queue = deque()
queue.append(1)     # Enqueue
queue.popleft()     # Dequeue
```


## 6. 🌲 Binary Tree

⚒️ **Use Case:** Hierarchical data, recursive traversal

⏰ **Time Complexity:**

- Traversal: O(n)
- Insert/Search (balanced): O(log n)

✅ **Recognize When:**

- Data is naturally hierarchical
- Problem involves "path", "ancestor", or "traversal"

🔍 **Common Prompts:**

- "Path sum"
- "Lowest common ancestor"
- "Balanced tree check"

```python
class TreeNode:
    def __init__(self, val):
        self.val = val
        self.left = None
        self.right = None
```


## 7. 🧠 Heap (Priority Queue)

⚒️ **Use Case:** Efficient min/max retrieval, top-K problems

⏰ **Time Complexity:**

- Insert/Pop: O(log n)
- Peek: O(1)

✅ **Recognize When:**

- You need to track top or bottom elements
- Problem involves "top K", "merge sorted lists", or "median stream"

🔍 **Common Prompts:**

- "Kth largest element"
- "Top K frequent words"

```python
import heapq
heap = []
heapq.heappush(heap, 3)
heapq.heappush(heap, 1)
heapq.heappop(heap)  # Returns 1 (min-heap)
```


## 8. 🧵 Trie (Prefix Tree)

⚒️ **Use Case:** Fast prefix search, autocomplete

⏰ **Time Complexity:** O(m) per operation

✅ **Recognize When:**

- You need to search or match prefixes
- Problem involves dictionary or word filtering

🔍 **Common Prompts:**

- "Autocomplete system"
- "Prefix matching"

```python
class TrieNode:
    def __init__(self):
        self.children = {}  # Maps character to TrieNode
        self.is_end = False  # True if node marks end of a word

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        # Insert word into trie
        node = self.root
        for ch in word:
            # Create node if not already present
            node = node.children.setdefault(ch, TrieNode())
        node.is_end = True  # Mark end of word

    def search(self, word):
        # Check if exact word exists in trie
        node = self.root
        for ch in word:
            if ch not in node.children:
                return False  # Character path doesn't exist
            node = node.children[ch]
        return node.is_end  # True only if word ends here

    def startsWith(self, prefix):
        # Check if any word starts with given prefix
        node = self.root
        for ch in prefix:
            if ch not in node.children:
                return False  # Prefix path doesn't exist
            node = node.children[ch]
        return True  # Prefix exists

trie = Trie()
trie.insert("apple")
trie.insert("app")

trie.search("apple")     # True
trie.search("app")       # True
trie.search("appl")      # False
trie.startsWith("appl")  # True
trie.startsWith("banana")# False
```


## 9. 🔗 Graph (Adjacency List)

⚒️ **Use Case:** Represent relationships, networks, dependencies

⏰ **Time Complexity:**

- Traversal: O(V + E)

✅ **Recognize When:**

- Problem involves connections, paths, or dependencies
- You need to traverse or analyze relationships

🔍 **Common Prompts:**

- "Course schedule"
- "Number of islands"
- "Shortest path"

```python
# Example directed graph represented as an adjacency list
DAG = {
    'A': ['B', 'C', 'D'],
    'B': ['E', 'F'],
    'C': ['G', 'H'],
    'D': [],
    'E': [],
    'F': [],
    'G': [],
    'H': []
}

#           A
#        /  |  \
#       B   C   D
#      / \  | \
#     E   F G  H
```

#### 🧠 **Breadth-First Search**:
```python
from collections import deque

def bfs(graph, start):
    visited = set()  # To keep track of visited nodes
    queue = deque([start])  # Initialize a queue with the starting node
    result = []  # To store the order of traversal

    while queue:
        node = queue.popleft() # Dequeue a node
        if node not in visited:
            visited.add(node)  # Mark as visited
            result.append(node)  # Add to result
            # Add all unvisited neighbors to the queue
            queue.extend(neighbor for neighbor in graph[node] if neighbor not in visited)
    return result

print(bfs(DAG, 'A')) # Output: ['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H']
```

#### 🧠 **Depth-First Search**:
```python
def dfs(graph, start, visited=None):
    if visited is None:
        visited = set()  # Initialize visited set if not provided
    visited.add(start)  # Mark the current node as visited
    result = [start]  # Store the order of traversal

    for neighbor in graph[start]:
        if neighbor not in visited:
            result.extend(dfs(graph, neighbor, visited))  # Recursively visit neighbors
    return result

print(dfs(DAG, 'A'))  # Output: ['A', 'B', 'E', 'F', 'C', 'G', 'H', 'D']
```


## 10. 🔗 Linked List

⚒️ **Use Case:** Dynamic memory allocation, efficient insert/delete at arbitrary positions

⏰ **Time Complexity:**

- Access by index: O(n)
- Insert/Delete at head: O(1)
- Insert/Delete at tail or arbitrary node: O(n)

✅ **Recognize When:**

- You need frequent insertions/deletions without shifting elements
- Problem involves "reverse list", "cycle detection", or "merge sorted lists"
- You’re asked to manipulate pointers or simulate memory-efficient structures

🔍 **Common Prompts:**

- "Reverse a linked list"
- "Detect cycle in linked list"
- "Merge two sorted linked lists"
- "Remove nth node from end"

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val      # Value of the node
        self.next = next    # Pointer to the next node

# Example: Reverse a linked list
def reverse_list(head):
    prev = None
    curr = head
    while curr:
        next_node = curr.next   # Save next node
        curr.next = prev        # Reverse pointer
        prev = curr             # Move prev forward
        curr = next_node        # Move curr forward
    return prev
```

🧠 **Cycle Detection (Floyd’s Tortoise and Hare):**

```python
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next         # Move 1 step
        fast = fast.next.next    # Move 2 steps
        if slow == fast:
            return True
    return False
```


## 11. 🔄 Circular Array

⚒️ **Use Case:** Fixed-size buffer with wraparound behavior

⏰ **Time Complexity:**

- Access: O(1)
- Insert/Delete: O(1) (with proper pointer management)

✅ **Recognize When:**

- You need a fixed-size queue or buffer
- Problem involves "wraparound", "modulo indexing", or "circular rotation"
- You’re implementing a ring buffer, round-robin scheduler, or sliding window

🔍 **Common Prompts:**

- "Design circular queue"
- "Rotate array"
- "Find winner in circular game"
- "Sliding window max with wraparound"

```python
class RingBuffer:
    def __init__(self, capacity):
        self.buffer = [None] * capacity  # Fixed-size array
        self.capacity = capacity
        self.head = 0  # Points to the oldest element
        self.tail = 0  # Points to the next insertion index
        self.size = 0  # Tracks current number of elements

    def enqueue(self, value):
        if self.size == self.capacity:
            raise OverflowError("Buffer is full")
        self.buffer[self.tail] = value
        self.tail = (self.tail + 1) % self.capacity  # Wrap around
        self.size += 1

    def dequeue(self):
        if self.size == 0:
            raise IndexError("Buffer is empty")
        value = self.buffer[self.head]
        self.buffer[self.head] = None  # Optional: clear slot
        self.head = (self.head + 1) % self.capacity
        self.size -= 1
        return value

    def peek(self):
        if self.size == 0:
            return None
        return self.buffer[self.head]

    def is_full(self):
        return self.size == self.capacity

    def is_empty(self):
        return self.size == 0

    def __str__(self):
        return f"Buffer: {self.buffer}, Head: {self.head}, Tail: {self.tail}"
```

🧠 **Modulo Indexing Example (Rotate Array):**

```python
def rotate_array(arr, k):
    n = len(arr)
    k %= n
    return arr[-k:] + arr[:-k]  # Simple Pythonic rotation
```

<br>
<br>

# 🧑‍💻Algorithm Patterns


## 1. 🔍 Binary Search

⚒️ **Use Case:** Search in sorted arrays, find boundaries, peak elements
⏰ **Time Complexity:** O(log n)

✅ **Recognize When:**

- Array is sorted or can be sorted
- Asked to find element, boundary, or condition

🔍 **Common Prompts:**

- "Search in sorted array"
- "Find peak element"

🎓 **Key Concepts:**

- Split the search space in half on every iteration
- Requires sorted values or problems looking for any pattern occurrence

🦺 **Bounds Safety:**

- `left <= right` avoids infinite loop
- `mid = (left + right) // 2` is safe for Python (no overflow)

🧠 **Example (sorted):**

```python
def binary_search(arr: list[int], target: int) -> int:
    left, right = 0, len(arr) - 1  # Initialize search boundaries

    while left <= right:
        mid = (left + right) // 2  # Calculate midpoint

        if arr[mid] == target:
            return mid  # Target found
        elif arr[mid] < target:
            left = mid + 1  # Search right half
        else:
            right = mid - 1  # Search left half

    return -1  # Target not found

```

🧠 **Example (Any occurrence):**

```python
def find_any_peak(nums: list[int]) -> int:
    left, right = 0, len(nums) - 1

    while left < right:
        mid = (left + right) // 2

        if nums[mid] > nums[mid + 1]:
            right = mid  # peak is to the left
        else:
            left = mid + 1  # peak is to the right

    return left  # index of a peak
```


## 2. 👯 Two Pointers

⚒️ **Use Case:** Sorted arrays, pair finding, duplicate removal

⏰ **Time Complexity:** O(n)

✅ **Recognize When:**

- Array is sorted
- You're finding pairs, triplets, or removing duplicates
- Need to reduce space complexity from hash map

🔍 **Common Prompts:**

- "Find two numbers that add up to target"
- "Remove duplicates in-place"
- "Merge two sorted arrays"

### **Search:**

🎓 **Key Concepts:**

- Search an array by walking two indexes through it
- Indexes generally begin at the start and end of array and converge

🦺 **Bounds Safety:**

- `left < right` usually terminates the search

🧠 **Example:**

```python
def two_sum_sorted(arr: list[int], target: int) -> list[int]:
    left, right = 0, len(arr) - 1  # Start from both ends

    while left < right:
        curr_sum = arr[left] + arr[right]

        if curr_sum == target:
            return [left, right]  # Found the pair
        elif curr_sum < target:
            left += 1  # Need a larger sum
        else:
            right -= 1  # Need a smaller sum
```

### **Merge:**

🎓 **Key Concepts:**

- Move an index through each array 
- Add the next largest value from either array to result and increment the index
- If array size is imbalanced add the remainder of the larger array to the end

🦺 **Bounds Safety:**

- Ensure both pointers are inbounds: `while i < len(A) and j < len(B)`

🧠 **Example:**

```python
def merge_sorted_arrays(A: list, B: list) -> list:
    i, j = 0, 0
    merged = []

    # Traverse both arrays until one is exhausted
    while i < len(A) and j < len(B):
        # Compare current elements from A and B
        if A[i] <= B[j]:
            # A[i] is smaller or equal, so add it to result
            merged.append(A[i])
            i += 1  # Move pointer in A
        else:
            # B[j] is smaller, so add it to result
            merged.append(B[j])
            j += 1  # Move pointer in B

    # If any elements remain in A, append them
    while i < len(A):
        merged.append(A[i])
        i += 1

    # If any elements remain in B, append them
    while j < len(B):
        merged.append(B[j])
        j += 1

    # Return the fully merged sorted array
    return merged
```


## 3. 🎞 Sliding Window

⚒️ **Use Case:** Subarrays or substrings with fixed or dynamic size

⏰ **Time Complexity:** O(n)

✅ **Recognize When:**

- Subarray or substring with constraints (length, sum, uniqueness)
- Need to optimize brute-force O(n²) substring logic
- Problem involves "longest", "shortest", or "maximum/minimum"

🔍 **Common Prompts:**

- "Longest substring without repeating characters"
- "Max sum of k-length subarray"
- "Minimum window substring"

🎓 **Key Concepts:**

- Move a logical window through an array
- Index `left` marks the start and `right` marks the end 
- Expand or reset window depending on conditions

🦺 **Bounds Safety:**

- `right` is usually controlled by `range(len(arr))`
- Use `left <= right` to ensure window integrity

🧠 **Example:**

```python
def longest_unique_substring(s):
    seen = {}  # Stores last seen index of each character
    left = max_len = 0

    for right in range(len(s)):
        if s[right] in seen:
            # Move left pointer to avoid repeating character
            left = max(left, seen[s[right]] + 1)

        seen[s[right]] = right  # Update last seen index
        max_len = max(max_len, right - left + 1)

    return max_len
```

```python
def max_sum_subarray(arr, k):
    window_sum = sum(arr[:k])  # Initial window sum
    max_sum = window_sum

    for i in range(k, len(arr)):
        # Slide window: remove leftmost, add new rightmost
        window_sum += arr[i] - arr[i - k]
        max_sum = max(max_sum, window_sum)

    return max_sum
```

```python
def string_in_string(self, haystack: str, needle: str) -> int:
    if not needle:
        return 0

    for i in range(len(haystack) - len(needle) + 1):
        found = True
        for j in range(len(needle)):
            if haystack[i+j] != needle[j]:
                found = False
                break

        if found:
            return i

    return -1
```


## 4. ➕ Prefix Sum

⚒️**Use Case:** Fast range sum queries, subarray sum problems

⏰ **Time Complexity:** O(n)

✅ **Recognize When:**

- Asked for sum of subarrays or ranges
- Frequent sum queries over static array

🔍 **Common Prompts:**

- "Subarray sum equals k"
- "Range sum query"

🎓**Key Concepts:**  
- Precompute cumulative sums to enable O(1) range queries  
- `prefix[i+1] = sum(arr[0] to arr[i])`  
- Enables fast subarray sum checks and range queries

🦺 **Bounds Safety:**  
- Use `prefix = [0] * (len(arr) + 1)` to avoid off-by-one errors  
- Always access `prefix[j+1] - prefix[i]` for range `[i, j]`

🧠 **Example:**

```python
def prefix_sum(arr):
    prefix = [0] * (len(arr) + 1)  # Extra space for prefix[0] = 0

    for i in range(len(arr)):
        # Each prefix[i+1] is sum of arr[0] to arr[i]
        prefix[i+1] = prefix[i] + arr[i]

    return prefix  # Enables fast range queries: sum(i to j) = prefix[j+1] - prefix[i]
```


## 5. 🐢🐇 Fast & Slow Pointers

⚒️ **Use Case:** Cycle detection, middle of linked list

⏰ **Time Complexity:** O(n)

✅ **Recognize When:**

- Linked list or cyclic structure
- Need to detect loops or find middle
- Problem involves "cycle", "loop", or "intersection"

🔍 **Common Prompts:**

- "Detect cycle in linked list"
- "Find start of cycle"
- "Find middle node"

🎓 **Key Concepts:**  
- Use two pointers moving at different speeds to detect cycles or find midpoints  
- Common in linked lists and cyclic structures

🦺 **Bounds Safety:**  
- Ensure `fast` and `fast.next` are valid before accessing  
- Loop condition: `while fast and fast.next`

🧠 **Example:**
```python
def has_cycle(head):
    slow = fast = head  # Initialize both pointers

    while fast and fast.next:
        slow = slow.next         # Move slow by 1
        fast = fast.next.next    # Move fast by 2

        if slow == fast:
            return True  # Cycle detected

    return False  # No cycle
```

## 6. 📦 Hash Map / Frequency Counter

⚒️ **Use Case:** Count elements, detect duplicates, solve two-sum efficiently

⏰ **Time Complexity:** O(n)

✅ **Recognize When:**

- Need to count or track frequency of elements
- Detect duplicates, anagrams, or patterns
- Optimize nested loops with constant-time lookup

🔍 **Common Prompts:**

- "Find all anagrams"
- "Two sum"
- "Group strings by pattern"

🎓 **Key Concepts:**

- Use key/value pairs to efficiently track or accumulate results in O(1) time

🦺 **Bounds Safety:**

- Ensure values are pre-initialized with `defaultdict` or use `dict[k] = dict.get(k, 0) + 1`

🧠 **Example:**
```python
def has_duplicates(arr):
    freq = {}  # Dictionary to store frequency of each element

    for num in arr:
        if num in freq:
            return True  # Found a duplicate
        freq[num] = 1  # Mark as seen

    return False  # No duplicates found


def two_sum(arr, target):
    seen = {}  # Maps number to its index

    for i, num in enumerate(arr):
        complement = target - num  # What we need to reach target

        if complement in seen:
            return [seen[complement], i]  # Found the pair

        seen[num] = i  # Store index of current number

    return []  # No valid pair found
```


## 7. 🔝 Top K Elements (Heap / Bucket Sort)

⚒️ **Use Case:** Most frequent elements, largest/smallest k values

⏰ **Time Complexity:** O(n log k)

✅ **Recognize When:**

- Asked to find most frequent or top-ranked items
- Need partial sorting or ranking
- Problem involves "top K", "most frequent", or "Kth largest"

🔍 **Common Prompts:**

- "Top K frequent words"
- "Kth largest element"
- "Sort characters by frequency"

```python
def top_k_frequent(nums, k):
    import heapq
    freq = {}  # Frequency map

    # Count frequency of each number
    for num in nums:
        freq[num] = freq.get(num, 0) + 1

    # Use heap to extract top k frequent elements
    return [item for item, _ in heapq.nlargest(k, freq.items(), key=lambda x: x[1])]
```


## 8. 📐 Intervals

### Merging
---

⚒️ **Use Case:** Merge overlapping intervals, calendar booking, range compression

⏰ **Time Complexity:** O(n log n)

✅ **Recognize When:**

- Input is a list of intervals
- Need to merge, insert, or find overlaps
- Problem involves "calendar", "booking", or "range"

🔍 **Common Prompts:**

- "Merge overlapping intervals"
- "Insert interval"
- "Find free time slots"

```python
def merge_intervals(intervals: list[tuple]):
    intervals.sort(key=lambda x: x[0])  # Sort by start time
    merged = [intervals[0]]

    for start, end in intervals[1:]:
        if start <= merged[-1][1]:
            # Merge by updating end time
            merged[-1][1] = max(merged[-1][1], end)
        else:
            merged.append([start, end])  # No overlap, add new interval

    return merged
```

### Overlap
---

⚒️ **Use Case:** Count max concurrent intervals, resource allocation, scheduling conflicts

⏰ **Time Complexity:** O(n log n)

✅ **Recognize When:**

- Input is a list of intervals
- Need to count overlaps or allocate resources
- Problem involves "meeting rooms", "concurrent sessions", or "minimum resources"

🔍 **Common Prompts:**

- "Minimum number of meeting rooms"
- "Max concurrent intervals"
- "Schedule without conflict"

```python
import heapq

def min_meeting_rooms(intervals: list[tuple]):
    intervals.sort(key=lambda x: x[0])  # Sort by start time
    heap = []  # Min-heap of end times

    for start, end in intervals:
        if heap and start >= heap[0]:
            heapq.heappop(heap)  # Reuse room
        heapq.heappush(heap, end)  # Allocate room

    return len(heap)  # Max rooms needed
```

🧠 **Alternative (Sweep Line):**

```python
def min_meeting_rooms(intervals: list[tuple]):
    starts = sorted(i[0] for i in intervals)
    ends = sorted(i[1] for i in intervals)

    s = e = rooms = 0
    while s < len(intervals):
        if starts[s] < ends[e]:
            rooms += 1  # Need new room
        else:
            e += 1  # Room freed
        s += 1

    return rooms
```


## 9. 🧵 String Manipulation

⚒️ **Use Case:** Reverse words, validate patterns, compress strings

⏰ **Time Complexity:** O(n)

✅ **Recognize When:**

- Problem involves reversing, splitting, or formatting strings
- Often solved with built-in string methods or regex

🔍 **Common Prompts:**

- "Reverse words in a string"
- "Compress string"
- "Validate pattern"

```python
def reverse_words(s):
    # Remove leading/trailing spaces, split by space, reverse list, join back
    return ' '.join(reversed(s.strip().split()))
```


## 10. 🌲 Tree Traversals (DFS / BFS)

⚒️ **Use Case:** Traverse binary trees, search paths

⏰ **Time Complexity:** O(n)

✅ **Recognize When:**

- Binary tree or graph traversal
- Need to explore nodes level-by-level or depth-first
- Problem involves "path", "ancestor", or "level"

🔍 **Common Prompts:**

- "Level order traversal"
- "Path sum"
- "Lowest common ancestor"

```python
def dfs_inorder(root):
    # Recursive in-order traversal: Left -> Root -> Right
    if not root:
        return []
    return dfs_inorder(root.left) + [root.val] + dfs_inorder(root.right)

def bfs_level_order(root):
    from collections import deque
    if not root:
        return []

    queue = deque([root])  # Initialize queue with root
    result = []

    while queue:
        node = queue.popleft()  # Visit current node
        result.append(node.val)

        # Add children to queue for next level
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)

    return result
```


## 11. 🧩 Matrix Traversal

⚒️ **Use Case:** Search in 2D matrix, spiral traversal, DFS/BFS on grid

⏰ **Time Complexity:** O(m + n)

✅ **Recognize When:**

- 2D grid or matrix problem
- Movement in 4 directions or spiral traversal
- Problem involves "islands", "search", or "traverse"

🔍 **Common Prompts:**

- "Search in 2D matrix"
- "Spiral order"
- "Number of islands"

```python
def search_matrix(matrix: list[list[int]], target: int) -> bool:
    # Start from top-right corner
    row, col = 0, len(matrix[0]) - 1

    while row < len(matrix) and col >= 0:
        if matrix[row][col] == target:
            return True  # Found target
        elif matrix[row][col] < target:
            row += 1  # Move down
        else:
            col -= 1  # Move left

    return False  # Target not found
```

```python
def print_diagonals(matrix: list[list[int]]):
    if not matrix or not matrix[0]:
        return

    M, N = len(matrix), len(matrix[0])

    # Start from each column in the top row (right to left)
    for col in range(N - 1, -1, -1):
        i, j = 0, col
        diagonal = []
        while i < M and j < N:
            diagonal.append(matrix[i][j])
            i += 1
            j += 1
        print(' '.join(map(str, diagonal)))

    # Then start from each row in the first column (excluding the top-left corner)
    for row in range(1, M):
        i, j = row, 0
        diagonal = []
        while i < M and j < N:
            diagonal.append(matrix[i][j])
            i += 1
            j += 1
        print(' '.join(map(str, diagonal)))
```


## 14. 🧠 Bit Manipulation

⚒️ **Use Case:** Find unique number, parity check, power of two

⏰ **Time Complexity:** O(1)

✅ **Recognize When:**

- Problem involves powers of two, parity, or toggling bits
- Often used for optimization or clever tricks
- Problem involves "bitwise", "mask", or "flip"

🔍 **Common Prompts:**

- "Check if power of two"
- "Find unique number"
- "Flip bits"

```python
def is_power_of_two(n):
    # A power of two has only one bit set in binary
    # n & (n - 1) removes the lowest set bit
    return n > 0 and (n & (n - 1)) == 0
```


## 15. 🔁 Backtracking

⚒️ **Use Case:** Explore all combinations, permutations, or valid configurations

⏰ **Time Complexity:** O(k · nᵏ) (varies by branching factor and depth)

✅ **Recognize When:**

- Problem involves generating all possible combinations, permutations, or paths
- You need to explore decision trees recursively
- Constraints must be checked dynamically (e.g., valid Sudoku, balanced parentheses)

🔍 **Common Prompts:**

- "Generate all subsets"
- "Permutations of a list"
- "N-Queens"
- "Word search in grid"
- "Restore IP addresses"

```python
def subsets(nums):
    res = []

    def backtrack(start, path):
        res.append(path[:])  # Add current subset to result

        for i in range(start, len(nums)):
            path.append(nums[i])         # Choose
            backtrack(i + 1, path)       # Explore
            path.pop()                   # Un-choose (backtrack)

    backtrack(0, [])
    return res
```

```python
def generate_parenthesis(self, n: int) -> list[str]: 
    # Given n pairs of parenthesis, write a function to 
    # generate all combinations of well-formed parenthesis.
    stack = []
    res = []

    def backtrack(num_open: int, num_close: int) -> None:
        # Each recursive call of backtrack adds additional combinations
        # to the stack list.
        if num_open == num_close == n:
            # open and closed are balanced with n pairs. We're done.
            res.append("".join(stack)) 
            return

        # Progress towards n open parenthesis
        if num_open < n:
            stack.append("(")
            backtrack(num_open + 1, num_close)
            stack.pop()

        # Ensure parenthesis are balanced
        if num_close < num_open:
            stack.append(")")
            backtrack(num_open, num_close + 1)
            stack.pop()

    backtrack(0,0)
    return res
```


## 16. 🧮 Dynamic Programming

⚒️ **Use Case:** Optimize recursive problems by storing intermediate results

⏰ **Time Complexity:** Typically O(n), O(n²), or O(n·m) depending on state space

✅ **Recognize When:**

- Problem has **optimal substructure** (solution can be built from subproblems)
- Problem has **overlapping subproblems** (same subproblem solved repeatedly)
- You’re asked for **count**, **min/max**, or **true/false** outcomes over sequences

🔍 **Common Prompts:**

- "Climbing stairs"
- "Longest common subsequence"
- "0/1 Knapsack"
- "Edit distance"
- "Minimum path sum"

```python
def climb_stairs(n):
    # Base cases
    if n <= 2:
        return n

    dp = [0] * (n + 1)  # dp[i] = ways to reach step i
    dp[1], dp[2] = 1, 2

    for i in range(3, n + 1):
        # Ways to reach step i = from (i-1) + from (i-2)
        dp[i] = dp[i - 1] + dp[i - 2]

    return dp[n]
    
def edit_distance(word1, word2):
    m, n = len(word1), len(word2)

    # dp[i][j] = min operations to convert word1[0:i] to word2[0:j]
    dp = [[0] * (n + 1) for _ in range(m + 1)]

    # Initialize base cases: converting to/from empty string
    for i in range(m + 1):
        dp[i][0] = i  # All deletes
    for j in range(n + 1):
        dp[0][j] = j  # All inserts

    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if word1[i - 1] == word2[j - 1]:
                # Characters match, no operation needed
                dp[i][j] = dp[i - 1][j - 1]
            else:
                # Choose min of insert, delete, replace
                dp[i][j] = 1 + min(
                    dp[i - 1][j],     # Delete
                    dp[i][j - 1],     # Insert
                    dp[i - 1][j - 1]  # Replace
                )

    return dp[m][n]
```


## 17. 🔗 Union-Find (Disjoint Set)

⚒️ **Use Case:** Track connected components, detect cycles in undirected graphs

⏰ **Time Complexity:** O(α(n)) per operation (nearly constant with path compression)

✅ **Recognize When:**

- You need to group or merge sets dynamically
- Detect cycles or connectivity in undirected graphs
- Problem involves "connected components", "merge accounts", or "friend circles"

🔍 **Common Prompts:**

- "Number of connected components"
- "Accounts merge"
- "Redundant connection"

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))  # Each node is its own parent

    def find(self, x):
        # Path compression: flatten the tree
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, x, y):
        # Connect two components
        root_x = self.find(x)
        root_y = self.find(y)
        if root_x != root_y:
            self.parent[root_y] = root_x

def count_components(n, edges):
    uf = UnionFind(n)
    for u, v in edges:
        uf.union(u, v)
    # Count unique roots
    return len(set(uf.find(i) for i in range(n)))
```


## 18. 📚 Topological Sort

⚒️ **Use Case:** Dependency resolution, DAG traversal

⏰ **Time Complexity:** O(V + E)

✅ **Recognize When:**

- You need to order tasks with dependencies
- Graph is a DAG (Directed Acyclic Graph)
- Problem involves "course schedule", "build order", or "task scheduling"

🔍 **Common Prompts:**

- "Course schedule"
- "Alien dictionary"
- "Task ordering"

```python
from collections import deque, defaultdict

def topological_sort(num_courses, prerequisites):
    graph = defaultdict(list)
    in_degree = [0] * num_courses

    # Build graph and in-degree count
    for dest, src in prerequisites:
        graph[src].append(dest)
        in_degree[dest] += 1

    # Start with nodes that have no prerequisites
    queue = deque([i for i in range(num_courses) if in_degree[i] == 0])
    order = []

    while queue:
        node = queue.popleft()
        order.append(node)

        for neighbor in graph[node]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)

    return order if len(order) == num_courses else []  # Return empty if cycle detected
```


## 19. 💡 Greedy Algorithms

⚒️ **Use Case:** Make locally optimal choices for global solution

⏰ **Time Complexity:** O(n log n) or O(n)

✅ **Recognize When:**

- Problem can be solved by always choosing the best immediate option
- You’re asked to minimize or maximize something
- Problem involves "scheduling", "jump", or "coverage"

🔍 **Common Prompts:**

- "Jump game"
- "Interval scheduling"
- "Gas station"

```python
def can_jump(nums):
    max_reach = 0  # Furthest index we can reach

    for i, jump in enumerate(nums):
        if i > max_reach:
            return False  # Can't reach this index
        max_reach = max(max_reach, i + jump)

    return True
```


## 20. 🎲 Reservoir Sampling

⚒️ **Use Case:** Random sampling from unknown-size stream

⏰ **Time Complexity:** O(n) for n items, O(1) space

✅ **Recognize When:**

- Input is a stream or size is unknown
- You need to sample uniformly without storing all data
- Problem involves "pick random node", "sample k elements"

🔍 **Common Prompts:**

- "Pick random node from linked list"
- "Sample k elements from stream"

```python
import random

def reservoir_sample(stream, k):
    reservoir = []

    for i, item in enumerate(stream):
        if i < k:
            reservoir.append(item)  # Fill initial reservoir
        else:
            j = random.randint(0, i)
            if j < k:
                reservoir[j] = item  # Replace with decreasing probability

    return reservoir
```
