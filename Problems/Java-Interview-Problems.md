# 🔥 Top 30 Java Interview Problems (Easy → Hard)

---

## 🟢 EASY (1–10)

---

### 1. Reverse a String

**Approach:** Use the **two-pointer technique** — place one pointer at the start (`l`) and one at the end (`r`). Swap characters at both ends and move inward until they meet. This reverses the string in-place in **O(n)** time with **O(1)** extra space (ignoring the char array). The one-liner uses `StringBuilder.reverse()` which does the same thing internally.

```java
// Without built-in
public static String reverse(String s) {
    char[] arr = s.toCharArray();
    int l = 0, r = arr.length - 1;
    while (l < r) {
        char temp = arr[l];
        arr[l++] = arr[r];
        arr[r--] = temp;
    }
    return new String(arr);
}

// One-liner
public static String reverse(String s) {
    return new StringBuilder(s).reverse().toString();
}
```

---

### 2. Check Palindrome

**Approach:** Again **two pointers** from both ends. Compare `charAt(l)` with `charAt(r)` — if any mismatch is found, return `false` immediately. If the loop completes without mismatch, the string is a palindrome. **O(n)** time, **O(1)** space. No need to create a reversed copy.

```java
public static boolean isPalindrome(String s) {
    int l = 0, r = s.length() - 1;
    while (l < r) {
        if (s.charAt(l++) != s.charAt(r--)) return false;
    }
    return true;
}
```

---

### 3. Find Duplicate Characters in a String

**Approach:** Use a **frequency map** (`LinkedHashMap` to preserve insertion order). Iterate through the string, count occurrences of each character using `merge()`. Then filter entries with count > 1. **O(n)** time, **O(k)** space where k = unique characters.

```java
public static void findDuplicates(String s) {
    Map<Character, Integer> map = new LinkedHashMap<>();
    for (char c : s.toCharArray()) {
        map.merge(c, 1, Integer::sum);
    }
    map.entrySet().stream()
       .filter(e -> e.getValue() > 1)
       .forEach(e -> System.out.println(e.getKey() + " : " + e.getValue()));
}
```

---

### 4. FizzBuzz

**Approach:** Simple **modulo arithmetic**. Key trick: check `% 15` (divisible by both 3 and 5) **first**, before checking `% 3` or `% 5` individually. If you check 3 and 5 separately first, the combined case never triggers. **O(n)** time.

```java
public static void fizzBuzz(int n) {
    for (int i = 1; i <= n; i++) {
        if (i % 15 == 0)      System.out.println("FizzBuzz");
        else if (i % 3 == 0)  System.out.println("Fizz");
        else if (i % 5 == 0)  System.out.println("Buzz");
        else                   System.out.println(i);
    }
}
```

---

### 5. Check if Two Strings are Anagrams

**Approach:** Use a **frequency array** of size 26 (for lowercase letters). Increment the count for characters in string `a`, decrement for string `b`. If every count is zero at the end, they're anagrams. This avoids sorting (**O(n log n)**) and solves it in **O(n)** time, **O(1)** space (fixed-size array).

```java
public static boolean isAnagram(String a, String b) {
    if (a.length() != b.length()) return false;
    int[] freq = new int[26];
    for (int i = 0; i < a.length(); i++) {
        freq[a.charAt(i) - 'a']++;
        freq[b.charAt(i) - 'a']--;
    }
    for (int f : freq) {
        if (f != 0) return false;
    }
    return true;
}
```

---

### 6. Swap Two Numbers Without Temp Variable

**Approach:** **Math trick** — `a = a+b` stores the sum, then `b = a-b` recovers original `a`, and `a = a-b` recovers original `b`. The **XOR method** works similarly: XOR is its own inverse (`a^b^b = a`). XOR is safer — no overflow risk. Both are **O(1)**.

```java
public static void swap(int a, int b) {
    System.out.println("Before: a=" + a + ", b=" + b);
    a = a + b;
    b = a - b;
    a = a - b;
    System.out.println("After: a=" + a + ", b=" + b);
}

// Using XOR
a = a ^ b;
b = a ^ b;
a = a ^ b;
```

---

### 7. Find Largest and Smallest in an Array

**Approach:** **Single-pass linear scan**. Initialize `min` and `max` to the first element, then iterate through the array updating them. **O(n)** time, **O(1)** space. Better than sorting (O(n log n)) when you only need min/max.

```java
public static void findMinMax(int[] arr) {
    int min = arr[0], max = arr[0];
    for (int num : arr) {
        if (num < min) min = num;
        if (num > max) max = num;
    }
    System.out.println("Min: " + min + ", Max: " + max);
}
```

---

### 8. Count Vowels and Consonants in a String

**Approach:** Convert to lowercase first, then do a **single-pass** through characters. For each character, check if it's a letter (`a-z`), then check if it exists in the vowel string `"aeiou"` using `indexOf()`. Non-letter characters (spaces, digits, symbols) are skipped. **O(n)** time.

```java
public static void countVowelsConsonants(String s) {
    int vowels = 0, consonants = 0;
    for (char c : s.toLowerCase().toCharArray()) {
        if (c >= 'a' && c <= 'z') {
            if ("aeiou".indexOf(c) != -1) vowels++;
            else consonants++;
        }
    }
    System.out.println("Vowels: " + vowels + ", Consonants: " + consonants);
}
```

---

### 9. Fibonacci Series (Iterative + Recursive)

**Approach:** **Iterative** — keep two variables `a`, `b` and shift them forward each step. **O(n)** time, **O(1)** space. **Recursive** — classic `fib(n-1) + fib(n-2)` but it's **O(2^n)** due to repeated subproblems (can be fixed with memoization). For interviews, know both but prefer iterative for efficiency.

```java
// Iterative
public static void fibonacci(int n) {
    int a = 0, b = 1;
    for (int i = 0; i < n; i++) {
        System.out.print(a + " ");
        int temp = a + b;
        a = b;
        b = temp;
    }
}

// Recursive
public static int fib(int n) {
    if (n <= 1) return n;
    return fib(n - 1) + fib(n - 2);
}
```

---

### 10. Check if a Number is Prime

**Approach:** **Optimized trial division**. Key optimizations: (1) Check 2 and 3 separately, (2) only loop from 5 to √n, (3) increment by 6 — because all primes > 3 are of the form `6k ± 1`. This skips multiples of 2 and 3 automatically. **O(√n)** time.

```java
public static boolean isPrime(int n) {
    if (n <= 1) return false;
    if (n <= 3) return true;
    if (n % 2 == 0 || n % 3 == 0) return false;
    for (int i = 5; i * i <= n; i += 6) {
        if (n % i == 0 || n % (i + 2) == 0) return false;
    }
    return true;
}
```

---

## 🟡 MEDIUM (11–20)

---

### 11. Remove Duplicates from an Array (Preserve Order)

**Approach:** Use a **LinkedHashSet** which automatically removes duplicates while maintaining insertion order. `HashSet` alone doesn't guarantee order. The Stream API version uses `.distinct()` which internally uses a `LinkedHashSet`. **O(n)** time, **O(n)** space.

```java
public static int[] removeDuplicates(int[] arr) {
    return Arrays.stream(arr).distinct().toArray();
}

// Without streams
public static List<Integer> removeDuplicates(int[] arr) {
    Set<Integer> seen = new LinkedHashSet<>();
    for (int num : arr) seen.add(num);
    return new ArrayList<>(seen);
}
```

---

### 12. Find First Non-Repeated Character in a String

**Approach:** Use a **LinkedHashMap** (preserves insertion order). First pass: count character frequencies. Second pass: iterate through the map and return the first entry with count `1`. `LinkedHashMap` is key here — a regular `HashMap` doesn't preserve order, so you'd lose track of which non-repeated char came first. **O(n)** time.

```java
public static char firstNonRepeated(String s) {
    Map<Character, Integer> map = new LinkedHashMap<>();
    for (char c : s.toCharArray()) {
        map.merge(c, 1, Integer::sum);
    }
    return map.entrySet().stream()
              .filter(e -> e.getValue() == 1)
              .map(Map.Entry::getKey)
              .findFirst()
              .orElse('\0');
}
```

---

### 13. Binary Search

**Approach:** Classic **divide and conquer** on a sorted array. Compare the target with the middle element — if equal, found; if target is greater, search the right half; if smaller, search the left half. Use `lo + (hi - lo) / 2` instead of `(lo + hi) / 2` to avoid **integer overflow**. **O(log n)** time, **O(1)** space.

```java
public static int binarySearch(int[] arr, int target) {
    int lo = 0, hi = arr.length - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (arr[mid] == target) return mid;
        else if (arr[mid] < target) lo = mid + 1;
        else hi = mid - 1;
    }
    return -1; // not found
}
```

---

### 14. Reverse a Linked List

**Approach:** **Iterative pointer reversal** with 3 pointers — `prev`, `curr`, `next`. At each step: save `curr.next`, point `curr.next` to `prev`, then advance both `prev` and `curr` forward. When `curr` is null, `prev` is the new head. **O(n)** time, **O(1)** space. This is a must-know for interviews.

```java
public static ListNode reverse(ListNode head) {
    ListNode prev = null, curr = head;
    while (curr != null) {
        ListNode next = curr.next;
        curr.next = prev;
        prev = curr;
        curr = next;
    }
    return prev;
}
```

---

### 15. Detect Cycle in a Linked List (Floyd's Algorithm)

**Approach:** **Floyd's Tortoise and Hare** — use two pointers, `slow` (moves 1 step) and `fast` (moves 2 steps). If there's a cycle, fast will eventually catch up to slow (they'll meet inside the cycle). If fast reaches `null`, there's no cycle. **O(n)** time, **O(1)** space — much better than using a `HashSet` to track visited nodes.

```java
public static boolean hasCycle(ListNode head) {
    ListNode slow = head, fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) return true;
    }
    return false;
}
```

---

### 16. Implement a Stack Using Queues

**Approach:** Use **two queues**. On `push`: add the new element to `q2`, then move everything from `q1` to `q2`, then swap `q1` and `q2`. This ensures the most recently pushed element is always at the front of `q1`. So `pop()` and `top()` are O(1), while `push()` is O(n). This simulates LIFO using FIFO queues.

```java
class MyStack {
    private Queue<Integer> q1 = new LinkedList<>();
    private Queue<Integer> q2 = new LinkedList<>();

    public void push(int x) {
        q2.add(x);
        while (!q1.isEmpty()) q2.add(q1.poll());
        Queue<Integer> temp = q1;
        q1 = q2;
        q2 = temp;
    }

    public int pop()  { return q1.poll(); }
    public int top()  { return q1.peek(); }
    public boolean empty() { return q1.isEmpty(); }
}
```

---

### 17. Two Sum — Return Indices of Two Numbers That Add Up to Target

**Approach:** Use a **HashMap** to store `{value → index}` as you iterate. For each element, compute `complement = target - nums[i]` and check if the complement is already in the map. If yes, return both indices. One-pass solution. **O(n)** time, **O(n)** space. Brute force would be O(n²) with nested loops.

```java
public static int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        int complement = target - nums[i];
        if (map.containsKey(complement)) {
            return new int[] { map.get(complement), i };
        }
        map.put(nums[i], i);
    }
    return new int[] {}; // no solution
}
```

---

### 18. Producer-Consumer Problem (wait/notify)

**Approach:** Classic **inter-thread communication** using `synchronized`, `wait()`, and `notifyAll()`. The producer waits when the buffer is full, the consumer waits when it's empty. Use `while` (not `if`) for the wait condition to handle **spurious wakeups**. `notifyAll()` wakes all waiting threads so they can re-check their condition. This is the foundation of blocking queues.

```java
class SharedBuffer {
    private final Queue<Integer> queue = new LinkedList<>();
    private final int capacity;

    SharedBuffer(int capacity) { this.capacity = capacity; }

    public synchronized void produce(int item) throws InterruptedException {
        while (queue.size() == capacity) wait();
        queue.add(item);
        System.out.println("Produced: " + item);
        notifyAll();
    }

    public synchronized int consume() throws InterruptedException {
        while (queue.isEmpty()) wait();
        int item = queue.poll();
        System.out.println("Consumed: " + item);
        notifyAll();
        return item;
    }
}
```

---

### 19. Merge Two Sorted Arrays

**Approach:** **Two-pointer merge** (same as the merge step in Merge Sort). Maintain pointers `i`, `j` for both arrays and `k` for the result. Compare elements at both pointers, pick the smaller one, advance that pointer. After one array is exhausted, copy the remaining elements from the other. **O(n + m)** time, **O(n + m)** space.

```java
public static int[] mergeSorted(int[] a, int[] b) {
    int[] result = new int[a.length + b.length];
    int i = 0, j = 0, k = 0;
    while (i < a.length && j < b.length) {
        result[k++] = (a[i] <= b[j]) ? a[i++] : b[j++];
    }
    while (i < a.length) result[k++] = a[i++];
    while (j < b.length) result[k++] = b[j++];
    return result;
}
```

---

### 20. Singleton Pattern (Thread-Safe)

**Approach:** **Bill Pugh** uses a static inner class — the `Holder` class is loaded only when `getInstance()` is called (lazy initialization), and class loading is thread-safe by JVM guarantee. No synchronization needed. **Double-checked locking** uses `volatile` + two `null` checks — the outer check avoids synchronization overhead on every call, the inner check ensures only one instance is created. `volatile` prevents instruction reordering.

```java
// Bill Pugh Singleton (best practice)
public class Singleton {
    private Singleton() {}

    private static class Holder {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return Holder.INSTANCE;
    }
}

// Double-Checked Locking
public class Singleton {
    private static volatile Singleton instance;

    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

---

## 🔴 HARD (21–30)

---

### 21. LRU Cache Implementation

**Approach:** **LinkedHashMap with access-order** — passing `true` as the 3rd constructor arg enables access-order (most recently accessed moves to end). Override `removeEldestEntry()` to auto-evict when size exceeds capacity. Both `get()` and `put()` are **O(1)**. The from-scratch version uses a **Doubly Linked List + HashMap** — the DLL maintains access order (head = most recent), and the HashMap gives O(1) key lookup. On access, remove the node and re-insert at head.

```java
class LRUCache {
    private final int capacity;
    private final Map<Integer, Integer> map;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        this.map = new LinkedHashMap<>(capacity, 0.75f, true) {
            @Override
            protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
                return size() > capacity;
            }
        };
    }

    public int get(int key) {
        return map.getOrDefault(key, -1);
    }

    public void put(int key, int value) {
        map.put(key, value);
    }
}
```

**From scratch (Doubly Linked List + HashMap):**
```java
class LRUCache {
    class Node {
        int key, value;
        Node prev, next;
        Node(int k, int v) { key = k; value = v; }
    }

    private Map<Integer, Node> map = new HashMap<>();
    private Node head = new Node(0, 0), tail = new Node(0, 0);
    private int capacity;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        head.next = tail;
        tail.prev = head;
    }

    public int get(int key) {
        if (!map.containsKey(key)) return -1;
        Node node = map.get(key);
        remove(node);
        insertToHead(node);
        return node.value;
    }

    public void put(int key, int value) {
        if (map.containsKey(key)) remove(map.get(key));
        if (map.size() == capacity) {
            remove(tail.prev);
        }
        insertToHead(new Node(key, value));
    }

    private void remove(Node node) {
        map.remove(node.key);
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    private void insertToHead(Node node) {
        map.put(node.key, node);
        node.next = head.next;
        node.prev = head;
        head.next.prev = node;
        head.next = node;
    }
}
```

---

### 22. Implement a Custom HashMap

**Approach:** Mimics `java.util.HashMap` internals. Uses an **array of buckets** where each bucket is a **linked list** (separate chaining). `hashCode() % capacity` determines the bucket. On collision, new entries are prepended to the linked list. When the **load factor** (size/capacity) exceeds 0.75, the array is **resized to 2x** and all entries are rehashed. `put()` is amortized **O(1)**, `get()` is **O(1)** average, **O(n)** worst case (all keys collide).

```java
class MyHashMap<K, V> {
    private static final int INITIAL_CAPACITY = 16;
    private static final float LOAD_FACTOR = 0.75f;

    static class Entry<K, V> {
        K key; V value; Entry<K, V> next;
        Entry(K k, V v, Entry<K, V> n) { key = k; value = v; next = n; }
    }

    private Entry<K, V>[] buckets;
    private int size;

    @SuppressWarnings("unchecked")
    public MyHashMap() {
        buckets = new Entry[INITIAL_CAPACITY];
    }

    private int getBucketIndex(K key) {
        return Math.abs(key.hashCode()) % buckets.length;
    }

    public void put(K key, V value) {
        int idx = getBucketIndex(key);
        Entry<K, V> curr = buckets[idx];
        while (curr != null) {
            if (curr.key.equals(key)) { curr.value = value; return; }
            curr = curr.next;
        }
        buckets[idx] = new Entry<>(key, value, buckets[idx]);
        size++;
        if ((float) size / buckets.length > LOAD_FACTOR) resize();
    }

    public V get(K key) {
        Entry<K, V> curr = buckets[getBucketIndex(key)];
        while (curr != null) {
            if (curr.key.equals(key)) return curr.value;
            curr = curr.next;
        }
        return null;
    }

    @SuppressWarnings("unchecked")
    private void resize() {
        Entry<K, V>[] oldBuckets = buckets;
        buckets = new Entry[oldBuckets.length * 2];
        size = 0;
        for (Entry<K, V> head : oldBuckets) {
            while (head != null) {
                put(head.key, head.value);
                head = head.next;
            }
        }
    }
}
```

---

### 23. Merge Sort

**Approach:** **Divide and Conquer**. Recursively split the array in half until single elements remain, then merge sorted halves back together. The `merge()` step uses the two-pointer technique to combine two sorted subarrays into a temp array, then copies back. **O(n log n)** time (always — no worst case degradation unlike QuickSort), **O(n)** extra space for the temp array. It's a **stable sort** (equal elements maintain their relative order).

```java
public static void mergeSort(int[] arr, int l, int r) {
    if (l >= r) return;
    int mid = l + (r - l) / 2;
    mergeSort(arr, l, mid);
    mergeSort(arr, mid + 1, r);
    merge(arr, l, mid, r);
}

private static void merge(int[] arr, int l, int mid, int r) {
    int[] temp = new int[r - l + 1];
    int i = l, j = mid + 1, k = 0;
    while (i <= mid && j <= r) {
        temp[k++] = (arr[i] <= arr[j]) ? arr[i++] : arr[j++];
    }
    while (i <= mid) temp[k++] = arr[i++];
    while (j <= r)   temp[k++] = arr[j++];
    System.arraycopy(temp, 0, arr, l, temp.length);
}
```

---

### 24. Find All Permutations of a String (Backtracking)

**Approach:** **Backtracking with swap**. Fix one character at position `idx`, then recursively permute the rest. After the recursive call, **swap back** (backtrack) to restore the original state. A `HashSet` tracks characters already placed at each position to **skip duplicates**. Time complexity is **O(n! × n)** — there are n! permutations, each of length n. This is the most efficient in-place approach.

```java
public static List<String> permutations(String s) {
    List<String> result = new ArrayList<>();
    backtrack(s.toCharArray(), 0, result);
    return result;
}

private static void backtrack(char[] arr, int idx, List<String> result) {
    if (idx == arr.length - 1) {
        result.add(new String(arr));
        return;
    }
    Set<Character> used = new HashSet<>();
    for (int i = idx; i < arr.length; i++) {
        if (used.add(arr[i])) { // skip duplicates
            swap(arr, idx, i);
            backtrack(arr, idx + 1, result);
            swap(arr, idx, i); // backtrack
        }
    }
}

private static void swap(char[] arr, int i, int j) {
    char t = arr[i]; arr[i] = arr[j]; arr[j] = t;
}
```

---

### 25. Deadlock Demonstration & Prevention

**Approach:** Deadlock occurs when two threads hold one lock each and wait for the other's lock — **circular wait**. Thread 1 holds `lock1` and waits for `lock2`, Thread 2 holds `lock2` and waits for `lock1`. **Fix:** Always acquire locks in a **consistent global order** (e.g., always `lock1` before `lock2`). This breaks the circular wait condition. Other strategies: use `tryLock()` with timeout, or use a single coarser lock.

```java
// DEADLOCK EXAMPLE
class DeadlockDemo {
    private final Object lock1 = new Object();
    private final Object lock2 = new Object();

    public void method1() {
        synchronized (lock1) {
            System.out.println("Lock1 acquired, waiting for Lock2...");
            synchronized (lock2) {
                System.out.println("Both locks acquired");
            }
        }
    }

    public void method2() {
        synchronized (lock2) { // Reverse order → DEADLOCK!
            System.out.println("Lock2 acquired, waiting for Lock1...");
            synchronized (lock1) {
                System.out.println("Both locks acquired");
            }
        }
    }
}

// FIX: Always acquire locks in the SAME ORDER
public void method2Fixed() {
    synchronized (lock1) { // Same order as method1
        synchronized (lock2) {
            System.out.println("Both locks acquired — no deadlock");
        }
    }
}
```

---

### 26. Balanced Parentheses Checker

**Approach:** Use a **Stack** (ArrayDeque). When you see an opening bracket, push it. When you see a closing bracket, pop from the stack and check if it matches the corresponding opening bracket using a map. If the stack is empty when you try to pop, or the popped bracket doesn't match, return `false`. At the end, the stack must be empty (no unmatched opening brackets). **O(n)** time, **O(n)** space.

```java
public static boolean isBalanced(String expr) {
    Deque<Character> stack = new ArrayDeque<>();
    Map<Character, Character> pairs = Map.of(')', '(', '}', '{', ']', '[');

    for (char c : expr.toCharArray()) {
        if (pairs.containsValue(c)) {
            stack.push(c);
        } else if (pairs.containsKey(c)) {
            if (stack.isEmpty() || stack.pop() != pairs.get(c)) return false;
        }
    }
    return stack.isEmpty();
}
```

---

### 27. Implement Thread Pool from Scratch

**Approach:** A **BlockingQueue** holds submitted tasks. A fixed number of **worker threads** are started at construction — each runs an infinite loop calling `taskQueue.take()` (blocks when queue is empty). When a task is submitted, it's added to the queue, and an idle worker picks it up. `shutdown()` sets a flag and interrupts all workers. This is essentially how `java.util.concurrent.ThreadPoolExecutor` works underneath.

```java
class SimpleThreadPool {
    private final BlockingQueue<Runnable> taskQueue;
    private final List<WorkerThread> threads;
    private volatile boolean isShutdown;

    public SimpleThreadPool(int poolSize) {
        taskQueue = new LinkedBlockingQueue<>();
        threads = new ArrayList<>();
        for (int i = 0; i < poolSize; i++) {
            WorkerThread worker = new WorkerThread();
            threads.add(worker);
            worker.start();
        }
    }

    public void submit(Runnable task) {
        if (!isShutdown) {
            taskQueue.offer(task);
        }
    }

    public void shutdown() {
        isShutdown = true;
        threads.forEach(Thread::interrupt);
    }

    private class WorkerThread extends Thread {
        @Override
        public void run() {
            while (!isShutdown) {
                try {
                    Runnable task = taskQueue.take();
                    task.run();
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                    break;
                }
            }
        }
    }
}
```

---

### 28. Binary Tree — Level Order Traversal (BFS)

**Approach:** **BFS using a Queue**. Start by adding root to the queue. At each level, record the queue size (= number of nodes at this level), then poll exactly that many nodes, adding their children. This naturally processes the tree level by level. **O(n)** time (visit every node once), **O(w)** space where w = maximum width of the tree (worst case n/2 for a complete tree).

```java
public static List<List<Integer>> levelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    if (root == null) return result;

    Queue<TreeNode> queue = new LinkedList<>();
    queue.add(root);

    while (!queue.isEmpty()) {
        int levelSize = queue.size();
        List<Integer> level = new ArrayList<>();

        for (int i = 0; i < levelSize; i++) {
            TreeNode node = queue.poll();
            level.add(node.val);
            if (node.left  != null) queue.add(node.left);
            if (node.right != null) queue.add(node.right);
        }
        result.add(level);
    }
    return result;
}
```

---

### 29. Longest Substring Without Repeating Characters (Sliding Window)

**Approach:** **Sliding Window + HashMap**. Maintain a window `[left, right]`. The map stores `{character → last seen index}`. When a duplicate is found (and it's within the current window), move `left` to one position after the duplicate's last occurrence. Update `maxLen` at every step. Each character is processed at most once by each pointer. **O(n)** time, **O(min(n, 26))** space.

```java
public static int lengthOfLongestSubstring(String s) {
    Map<Character, Integer> map = new HashMap<>();
    int maxLen = 0, left = 0;

    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        if (map.containsKey(c) && map.get(c) >= left) {
            left = map.get(c) + 1;
        }
        map.put(c, right);
        maxLen = Math.max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

---

### 30. Implement a Custom Immutable Class

**Approach:** Follow the **5 rules of immutability**: (1) `final` class — prevent subclassing that could override behavior, (2) all fields `private final` — no direct access or reassignment, (3) no setters, (4) **defensive copies** for mutable fields — in the constructor, copy the input list so external code can't mutate it later; wrap in `unmodifiableList` so the getter returns a read-only view, (5) override `equals()`/`hashCode()` for value-based comparison. Immutable objects are inherently **thread-safe**.

```java
import java.util.*;

public final class ImmutablePerson {
    private final String name;
    private final int age;
    private final List<String> hobbies; // mutable field

    public ImmutablePerson(String name, int age, List<String> hobbies) {
        this.name = name;
        this.age = age;
        // Defensive copy — never store the original reference
        this.hobbies = Collections.unmodifiableList(new ArrayList<>(hobbies));
    }

    public String getName()       { return name; }
    public int getAge()           { return age; }
    public List<String> getHobbies() { return hobbies; } // already unmodifiable

    // No setters!

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof ImmutablePerson)) return false;
        ImmutablePerson that = (ImmutablePerson) o;
        return age == that.age &&
               Objects.equals(name, that.name) &&
               Objects.equals(hobbies, that.hobbies);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age, hobbies);
    }

    @Override
    public String toString() {
        return "ImmutablePerson{name='" + name + "', age=" + age + ", hobbies=" + hobbies + "}";
    }
}
```

**Key rules for immutability:**
1. Class is `final` — can't be subclassed
2. All fields are `private final`
3. No setters
4. Defensive copies for mutable fields (in constructor and getter)
5. Override `equals()`, `hashCode()`, `toString()`

---

## 📝 Quick Reference — Key Java Concepts

| Topic | Key Points |
|---|---|
| **String Pool** | `"abc"` uses pool; `new String("abc")` creates new object |
| **== vs .equals()** | `==` compares references; `.equals()` compares content |
| **HashMap internals** | Array of buckets → LinkedList → TreeMap (≥8 nodes, Java 8+) |
| **ConcurrentHashMap** | Segment-level locking (Java 7), node-level CAS (Java 8+) |
| **volatile** | Ensures visibility across threads, no caching |
| **synchronized** | Mutual exclusion — only one thread at a time |
| **Generics** | Type erasure at runtime; `<? extends T>` = upper bound |
| **Stream API** | `.filter()`, `.map()`, `.reduce()`, `.collect()` |
| **Optional** | Avoid NPE: `Optional.ofNullable(x).orElse(default)` |
| **CompletableFuture** | Async programming: `.thenApply()`, `.thenCompose()`, `.exceptionally()` |

---
