# 👆👆 Two Pointers — Complete Deep Dive

> **Series**: DSA Made Simple | **Chapter**: 02 | **Level**: Beginner → Advanced
> **Language**: Java

---

## 🧠 What is the Two Pointers Technique?

Imagine you're reading a book and you put **one finger at the beginning** and **one finger at the end**. You move them toward each other, comparing pages as you go. That's two pointers.

In code, **pointers are just index variables** that point to positions in an array or string. Instead of using one loop to check every possible pair (which is slow — O(n²)), you use **two index variables** and move them **smartly** to get the answer in O(n).

```
Without Two Pointers:        With Two Pointers:
Check every pair             Move intelligently
[i=0,j=1] [i=0,j=2] ...     [left] --------→ ←-------- [right]
O(n²) — SLOW ❌              O(n) — FAST ✅
```

---

## 🗂️ The 4 Types of Two Pointers

```
TWO POINTERS
│
├── Type 1: Opposite Ends       → left & right move toward each other
├── Type 2: Fast & Slow         → two speeds on same direction  
├── Type 3: Sliding Window      → window expands and shrinks
└── Type 4: Multiple Arrays     → one pointer per array
```

---

---

# 🔷 Type 1: Opposite Ends (Left & Right)

## Concept

Two pointers start at **opposite ends** of the array and move **toward each other** until they meet.

```
Start:
arr = [ 1,  2,  3,  4,  5,  6,  7 ]
        ↑                        ↑
       left                    right

Move inward based on some condition:
       left ++              right --

Stop when: left >= right
```

## When to Use It?
- ✅ Array/string is **sorted** (or you need to sort it first)
- ✅ Finding **pairs** that satisfy a condition (sum, product)
- ✅ **Palindrome** checks
- ✅ **Reversing** in-place

## How to Think About It

Ask yourself:
1. If the current pair satisfies the condition → done or record answer
2. If we need a **bigger** value → move `left` right (increase)
3. If we need a **smaller** value → move `right` left (decrease)

---

## Example 1 — Reverse an Array

```
arr = [1, 2, 3, 4, 5]

Step 1: swap(arr[0], arr[4]) → [5, 2, 3, 4, 1],  left=1, right=3
Step 2: swap(arr[1], arr[3]) → [5, 4, 3, 2, 1],  left=2, right=2
Step 3: left >= right → STOP

Result: [5, 4, 3, 2, 1] ✅
```

```java
public static void reverse(int[] arr) {
    int left = 0, right = arr.length - 1;

    while (left < right) {
        int temp = arr[left];
        arr[left] = arr[right];
        arr[right] = temp;
        left++;
        right--;
    }
}
```

---

## Example 2 — Two Sum (Sorted Array)

**Problem**: Given a sorted array, find two numbers that add up to a target.

```
arr    = [1, 2, 4, 6, 8, 9]
target = 10

Step 1: left=0(1), right=5(9)  → sum=10 ✅ FOUND!
Answer: indices [0, 5]
```

```
What if sum was 7?

Step 1: left=0(1), right=5(9)  → sum=10 > 7  → move right--
Step 2: left=0(1), right=4(8)  → sum=9  > 7  → move right--
Step 3: left=0(1), right=3(6)  → sum=7  == 7 ✅ FOUND!
```

```java
public static int[] twoSumSorted(int[] arr, int target) {
    int left = 0, right = arr.length - 1;

    while (left < right) {
        int sum = arr[left] + arr[right];

        if (sum == target) {
            return new int[]{left, right};   // found!
        } else if (sum < target) {
            left++;    // need bigger sum → move left right
        } else {
            right--;   // need smaller sum → move right left
        }
    }

    return new int[]{-1, -1}; // not found
}
```

---

## Example 3 — Valid Palindrome

**Problem**: Check if a string (ignoring non-alphanumeric characters) is a palindrome.

```
s = "A man, a plan, a canal: Panama"

Clean:  "amanaplanacanalpanama"

left=0 (a)  right=19 (a) → match ✅ → move inward
left=1 (m)  right=18 (m) → match ✅ → move inward
...continues until left >= right → It's a palindrome! ✅
```

```java
public static boolean isPalindrome(String s) {
    int left = 0, right = s.length() - 1;

    while (left < right) {
        // Skip non-alphanumeric characters
        while (left < right && !Character.isLetterOrDigit(s.charAt(left)))  left++;
        while (left < right && !Character.isLetterOrDigit(s.charAt(right))) right--;

        if (Character.toLowerCase(s.charAt(left)) != 
            Character.toLowerCase(s.charAt(right))) {
            return false; // mismatch → not a palindrome
        }

        left++;
        right--;
    }

    return true;
}
```

---

---

# 🔷 Type 2: Fast & Slow Pointers (Floyd's Algorithm)

## Concept

Both pointers start at the **same end** but move at **different speeds**.
- `slow` moves **1 step** at a time
- `fast` moves **2 steps** at a time

This is also called **Floyd's Tortoise and Hare** algorithm 🐢🐇.

```
Start:
arr = [ 1 → 2 → 3 → 4 → 5 ]
        ↑
      slow
      fast

After Step 1:         After Step 2:
slow = 2              slow = 3
fast = 3              fast = 5
```

## When to Use It?
- ✅ Detecting a **cycle** in a linked list
- ✅ Finding the **middle** of a linked list
- ✅ Finding the **start of a cycle**
- ✅ Detecting **duplicate numbers** (array as implicit linked list)

## How to Think About It

Think of two runners on a circular track. If there's a loop, the fast runner will **lap** the slow one — they'll meet. If there's no loop, the fast runner reaches the end.

---

## Example 1 — Find the Middle of a Linked List

```
List: 1 → 2 → 3 → 4 → 5

Step 1: slow=2, fast=3
Step 2: slow=3, fast=5
fast.next == null → STOP
Middle = slow = 3 ✅
```

```java
public static ListNode findMiddle(ListNode head) {
    ListNode slow = head;
    ListNode fast = head;

    while (fast != null && fast.next != null) {
        slow = slow.next;        // move 1 step
        fast = fast.next.next;   // move 2 steps
    }

    return slow; // slow is at the middle
}
```

---

## Example 2 — Detect Cycle in a Linked List

```
List: 1 → 2 → 3 → 4 → 5
                    ↑       ↓
                    └───────┘  (cycle: 5 points back to 3)

slow and fast will eventually meet inside the cycle ✅
If fast reaches null → no cycle ❌
```

```java
public static boolean hasCycle(ListNode head) {
    ListNode slow = head;
    ListNode fast = head;

    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;

        if (slow == fast) {
            return true;  // they met → cycle exists!
        }
    }

    return false; // fast reached end → no cycle
}
```

---

## Example 3 — Find Duplicate Number (Array)

**Problem**: Array of n+1 integers where each value is between 1 and n. One number repeats. Find it without modifying the array and using O(1) space.

**Trick**: Treat array values as "next pointers" — like a linked list with a cycle. The duplicate creates the cycle's entry point.

```java
public static int findDuplicate(int[] nums) {
    int slow = nums[0];
    int fast = nums[0];

    // Phase 1: Detect the cycle
    do {
        slow = nums[slow];
        fast = nums[nums[fast]];
    } while (slow != fast);

    // Phase 2: Find the entry point of the cycle (= duplicate)
    slow = nums[0];
    while (slow != fast) {
        slow = nums[slow];
        fast = nums[fast];
    }

    return slow;
}
```

---

---

# 🔷 Type 3: Sliding Window

## Concept

Think of a **train window**. As the train moves, the window slides — new scenery enters on one side, old scenery leaves the other. The window size can be **fixed** or **variable**.

```
Fixed window (size = 3):
arr = [2, 1, 5, 1, 3, 2]

Window slides one step at a time:
[2, 1, 5] → sum=8
   [1, 5, 1] → sum=7
      [5, 1, 3] → sum=9  ← max!
         [1, 3, 2] → sum=6
```

```
Variable window:
Expand right pointer to grow the window.
Shrink left pointer when a condition is violated.

"Find longest substring without repeating characters"
s = "abcabcbb"

→ →  window grows: a, ab, abc (valid)
→ → → window hits 'a' again → shrink from left
```

## When to Use It?
- ✅ **Subarray/substring** problems
- ✅ Problems with **contiguous** elements
- ✅ "**Longest / Shortest** subarray that satisfies X"
- ✅ "**Maximum / Minimum** sum of subarray of size k"

## Fixed vs Variable Window

```
Fixed Window:             Variable Window:
─────────────             ────────────────
window size = k           window size changes
right - left = k - 1      based on condition
slide by 1 each time      left pointer catches up
```

---

## Example 1 — Max Sum Subarray of Size K (Fixed)

```
arr = [2, 1, 5, 1, 3, 2],  k=3

Step 1: Build first window [2,1,5] → sum=8
Step 2: Slide → add arr[3]=1, remove arr[0]=2 → sum=7
Step 3: Slide → add arr[4]=3, remove arr[1]=1 → sum=9
Step 4: Slide → add arr[5]=2, remove arr[2]=5 → sum=6

Max = 9 ✅
```

```java
public static int maxSumFixed(int[] arr, int k) {
    int windowSum = 0;

    // Build the first window
    for (int i = 0; i < k; i++) windowSum += arr[i];

    int maxSum = windowSum;

    // Slide the window
    for (int i = k; i < arr.length; i++) {
        windowSum += arr[i];        // add incoming element
        windowSum -= arr[i - k];    // remove outgoing element
        maxSum = Math.max(maxSum, windowSum);
    }

    return maxSum;
}
```

---

## Example 2 — Longest Substring Without Repeating Characters (Variable)

```
s = "a b c a b c b b"
     0 1 2 3 4 5 6 7

left=0, right moves →

right=0: window="a"     seen={a:0}        len=1
right=1: window="ab"    seen={a,b}        len=2
right=2: window="abc"   seen={a,b,c}      len=3
right=3: 'a' seen at 0! → left moves to 1
         window="bca"   seen={b,c,a}      len=3
right=4: 'b' seen at 1! → left moves to 2
         window="cab"   ...               len=3

Max length = 3 ✅
```

```java
public static int lengthOfLongestSubstring(String s) {
    Map<Character, Integer> lastSeen = new HashMap<>();
    int maxLen = 0;
    int left = 0;

    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);

        // If character was seen and is inside current window
        if (lastSeen.containsKey(c) && lastSeen.get(c) >= left) {
            left = lastSeen.get(c) + 1;  // shrink window
        }

        lastSeen.put(c, right);                          // update last seen index
        maxLen = Math.max(maxLen, right - left + 1);     // update max length
    }

    return maxLen;
}
```

---

---

# 🔷 Type 4: Multiple Arrays (Merge Type)

## Concept

One pointer per array. You advance the pointer in the array whose current element is **smaller** (or satisfies a condition). Think of it like a **merge** operation — you always pick from whichever array has the next smallest element.

```
arr1 = [1, 3, 5, 7]
arr2 = [2, 4, 6, 8]
        ↑            ↑
        i            j

Step 1: arr1[i]=1 < arr2[j]=2 → pick 1, i++
Step 2: arr1[i]=3 > arr2[j]=2 → pick 2, j++
Step 3: arr1[i]=3 < arr2[j]=4 → pick 3, i++
...
Merged: [1, 2, 3, 4, 5, 6, 7, 8] ✅
```

## When to Use It?
- ✅ **Merging** two sorted arrays
- ✅ Finding **intersection** or **union** of two arrays
- ✅ Comparing two sequences (strings, arrays)
- ✅ **Merge Sort** (merge step)

---

## Example 1 — Merge Two Sorted Arrays

```java
public static int[] mergeSorted(int[] arr1, int[] arr2) {
    int[] result = new int[arr1.length + arr2.length];
    int i = 0, j = 0, k = 0;

    while (i < arr1.length && j < arr2.length) {
        if (arr1[i] <= arr2[j]) {
            result[k++] = arr1[i++];  // pick from arr1
        } else {
            result[k++] = arr2[j++];  // pick from arr2
        }
    }

    // Remaining elements from arr1
    while (i < arr1.length) result[k++] = arr1[i++];

    // Remaining elements from arr2
    while (j < arr2.length) result[k++] = arr2[j++];

    return result;
}
```

---

## Example 2 — Intersection of Two Arrays

**Problem**: Return elements that appear in both arrays.

```
arr1 = [1, 2, 2, 3, 4]
arr2 = [2, 2, 4, 6]

Sort both first, then use two pointers:

i→1, j→2: 1 < 2 → i++
i→2, j→2: 2 == 2 → record 2, both++
i→2, j→2: 2 == 2 → record 2, both++
i→3, j→4: 3 < 4 → i++
i→4, j→4: 4 == 4 → record 4, both++

Intersection = [2, 2, 4] ✅
```

```java
public static List<Integer> intersect(int[] arr1, int[] arr2) {
    Arrays.sort(arr1);
    Arrays.sort(arr2);

    List<Integer> result = new ArrayList<>();
    int i = 0, j = 0;

    while (i < arr1.length && j < arr2.length) {
        if (arr1[i] == arr2[j]) {
            result.add(arr1[i]);  // match → record it
            i++;
            j++;
        } else if (arr1[i] < arr2[j]) {
            i++;  // arr1 is behind → advance i
        } else {
            j++;  // arr2 is behind → advance j
        }
    }

    return result;
}
```

---

---

# 🧭 When to Use Which Type? — Decision Guide

```
You see an array/string problem...
│
├── Is it about finding pairs/triplets in a SORTED array?
│   └── ✅ Type 1: Opposite Ends
│
├── Is it a LINKED LIST problem (cycle, middle, duplicate)?
│   └── ✅ Type 2: Fast & Slow
│
├── Is it about a CONTIGUOUS subarray or substring?
│   ├── Fixed size k?   → ✅ Type 3: Fixed Sliding Window
│   └── Variable size?  → ✅ Type 3: Variable Sliding Window
│
└── Are there TWO separate arrays to compare or merge?
    └── ✅ Type 4: Multiple Arrays
```

---

# ⏱️ Complexity Summary

| Type | Time | Space | Key Condition |
|------|------|-------|---------------|
| Opposite Ends | O(n) | O(1) | Array should be sorted |
| Fast & Slow | O(n) | O(1) | Works on lists/arrays with cycles |
| Sliding Window | O(n) | O(1) or O(k) | Contiguous subarray/substring |
| Multiple Arrays | O(n + m) | O(1) | Both arrays should be sorted |

---

# 🧩 Practice Problems

### 🟢 Easy

| # | Problem | Type | Link |
|---|---------|------|------|
| 1 | Reverse String | Opposite Ends | [LeetCode 344](https://leetcode.com/problems/reverse-string/) |
| 2 | Valid Palindrome | Opposite Ends | [LeetCode 125](https://leetcode.com/problems/valid-palindrome/) |
| 3 | Squares of a Sorted Array | Opposite Ends | [LeetCode 977](https://leetcode.com/problems/squares-of-a-sorted-array/) |
| 4 | Merge Sorted Array | Multiple Arrays | [LeetCode 88](https://leetcode.com/problems/merge-sorted-array/) |
| 5 | Middle of Linked List | Fast & Slow | [LeetCode 876](https://leetcode.com/problems/middle-of-the-linked-list/) |
| 6 | Linked List Cycle | Fast & Slow | [LeetCode 141](https://leetcode.com/problems/linked-list-cycle/) |
| 7 | Max Avg Subarray I | Sliding Window (Fixed) | [LeetCode 643](https://leetcode.com/problems/maximum-average-subarray-i/) |

### 🟡 Medium

| # | Problem | Type | Link |
|---|---------|------|------|
| 8 | Two Sum II (Sorted) | Opposite Ends | [LeetCode 167](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/) |
| 9 | 3Sum | Opposite Ends | [LeetCode 15](https://leetcode.com/problems/3sum/) |
| 10 | Container With Most Water | Opposite Ends | [LeetCode 11](https://leetcode.com/problems/container-with-most-water/) |
| 11 | Longest Substring Without Repeating | Sliding Window (Variable) | [LeetCode 3](https://leetcode.com/problems/longest-substring-without-repeating-characters/) |
| 12 | Fruits Into Baskets | Sliding Window (Variable) | [LeetCode 904](https://leetcode.com/problems/fruit-into-baskets/) |
| 13 | Find Duplicate Number | Fast & Slow | [LeetCode 287](https://leetcode.com/problems/find-the-duplicate-number/) |
| 14 | Intersection of Two Arrays II | Multiple Arrays | [LeetCode 350](https://leetcode.com/problems/intersection-of-two-arrays-ii/) |

### 🔴 Hard

| # | Problem | Type | Link |
|---|---------|------|------|
| 15 | Trapping Rain Water | Opposite Ends | [LeetCode 42](https://leetcode.com/problems/trapping-rain-water/) |
| 16 | Minimum Window Substring | Sliding Window (Variable) | [LeetCode 76](https://leetcode.com/problems/minimum-window-substring/) |
| 17 | Sliding Window Maximum | Sliding Window + Deque | [LeetCode 239](https://leetcode.com/problems/sliding-window-maximum/) |
| 18 | Linked List Cycle II (find start) | Fast & Slow | [LeetCode 142](https://leetcode.com/problems/linked-list-cycle-ii/) |

---

## 💡 Common Mistakes to Avoid

- ❌ **Using Two Pointers on unsorted arrays** for pair-sum problems → always sort first
- ❌ **Infinite loops** — always make sure at least one pointer moves every iteration
- ❌ **Off-by-one errors** — carefully decide `left < right` vs `left <= right`
- ❌ **Not handling edge cases** — empty array, single element, all duplicates
- ❌ **Sliding window**: forgetting to shrink the window when the condition breaks

---



*Part of the **DSA Made Simple** series — Breaking down complex concepts, one pattern at a time.*
