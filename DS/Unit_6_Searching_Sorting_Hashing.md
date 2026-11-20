# Unit 6 — Searching, Sorting and Hashing

> **Syllabus (DJS23IPC201, 8 hrs):** Searching — linear search, binary search, Fibonacci search. Sorting — bubble sort, selection sort, heap sort, insertion sort, radix sort, merge sort, quick sort; analysis of searching and sorting techniques. Hashing — hash functions, overflow handling, collision and collision-resolution techniques, linear hashing, hashing with chaining, separate chaining, open addressing, rehashing and extendible hashing.
>
> **Past-paper coverage:** linear/binary/Fibonacci search with traces; bubble, selection, insertion, merge, quick, radix and heap sort with passes; complexity and stability comparisons; hash functions (division, mid-square, multiplication); linear probing, quadratic probing, double hashing, separate chaining; rehashing; collision counts.

**Source codes**

| Code | Paper |
|---|---|
| IT-F24 | IT Final 2024-25 |
| CO-F24 | Computer Final 2024-25 |
| IO-F24 | IOT Final 2024-25 |
| AI-F24 | AIDS/AIML Final 2024-25 |
| IT-R25 | IT Re-Exam 2024-25 |
| CO-R25 | Computer Re-Exam 2024-25 |
| AI-R25 | AIDS/AIML Re-Exam 2024-25 |
| P8 Jan-24 | Common Final 2023-24 (65 marks) |
| IT-F25 | IT Final 2025-26 |
| IT-R26 | IT Re-Exam 2025-26 |
| AI-R26 | AIDS Re-Exam 2025-26 |
| IC-R26 | CSE (ICB) Re-Exam 2025-26 |
| CO-R26 | Computer Re-Exam 2024-25 batch |
| CO-B26 | 2023-24 Backlog Re-Exam (65 marks) |

---

## 6.1 Linear search **[5]**

**Asked in:** CO-F24 Q4B(1); IT-F25 Q4A.

**Questions (verbatim):**
> Compare and contrast Linear and Binary Search? Apply the Linear Search to determine whether the key element 88 is present or not in the given list [2,4,6,8,10,12,14,16].
>
> Explain Linear Search, Binary Search, and Fibonacci Search and compare how each method searches for element 25 in the following array: [5, 10, 15, 20, 25, 30, 35].

**Answer**

**Linear search** scans the array from the first element to the last, comparing each element with the key. It works on **sorted or unsorted** data.

```
LINEAR_SEARCH(A, n, key):
1. for i ← 0 to n−1:
       if A[i] = key: return i
2. return −1
```

**Trace — key 88 in `[2,4,6,8,10,12,14,16]`**

| Comparison | Index | Value | Match? |
|---|---:|---:|---|
| 1 | 0 | 2 | no |
| 2 | 1 | 4 | no |
| 3 | 2 | 6 | no |
| 4 | 3 | 8 | no |
| 5 | 4 | 10 | no |
| 6 | 5 | 12 | no |
| 7 | 6 | 14 | no |
| 8 | 7 | 16 | no |

All 8 elements checked; **88 is not present**. Time `O(n)`, space `O(1)`.

**Trace — key 25 in `[5,10,15,20,25,30,35]`** — 5 comparisons, found at index 4.

**Complexity**

| Case | Comparisons |
|---|---|
| Best | 1 (`O(1)`) — key at index 0 |
| Worst | `n` (`O(n)`) — key last or absent |
| Average | `(n+1)/2` (`Θ(n)`) |

**Advantages:** simple, no sorting needed, works on linked lists.
**Disadvantages:** slow for large `n`; does not exploit order.

---

## 6.2 Binary search **[5]**

**Asked in:** AI-R26 Q2A; CO-F24 Q4B(1); IT-F25 Q4A.

**Questions (verbatim):**
> Apply Binary Search on B = [4, 9, 13, 21, 27, 35, 41, 56, 62] to find 35. Show low, high, and mid values.
>
> Compare and contrast Linear and Binary Search? …

**Answer**

**Binary search** works only on a **sorted** array. It compares the key with the middle element and discards the half that cannot contain the key.

```
BINARY_SEARCH(A, n, key):
1. low ← 0; high ← n − 1
2. while low ≤ high:
       mid ← ⌊(low + high) / 2⌋
       if A[mid] = key: return mid
       else if A[mid] < key: low ← mid + 1
       else: high ← mid − 1
3. return −1
```

**Trace — key 35 in `[4,9,13,21,27,35,41,56,62]`**

| Step | low | high | mid | A[mid] | Action |
|---|---:|---:|---:|---:|---|
| 1 | 0 | 8 | 4 | 27 | 27 < 35 → `low = 5` |
| 2 | 5 | 8 | 6 | 41 | 41 > 35 → `high = 5` |
| 3 | 5 | 5 | 5 | 35 | **found at index 5** |

**Result: index 5**, 3 comparisons.

**Trace — key 25 in `[5,10,15,20,25,30,35]`**

| Step | low | high | mid | A[mid] | Action |
|---|---:|---:|---:|---:|---|
| 1 | 0 | 6 | 3 | 20 | 20 < 25 → `low = 4` |
| 2 | 4 | 6 | 5 | 30 | 30 > 25 → `high = 4` |
| 3 | 4 | 4 | 4 | 25 | **found** |

**Trace — key 88 in `[2,4,6,8,10,12,14,16]`** — `mid=3 (8)`, `mid=5 (12)`, `mid=6 (14)`, `mid=7 (16)`, then `low=8 > high=7`; **not found** after 4 comparisons.

**Complexity**

| Case | Comparisons | Time |
|---|---|---|
| Best | 1 | `O(1)` |
| Worst/average | `⌊log₂n⌋ + 1` | `O(log n)` |

Space `O(1)` iterative, `O(log n)` recursive. Requires random access (array), not a linked list.

---

## 6.2A Count occurrences of a key in a sorted array **[5]**

**Asked in:** CO-B26 Q2A(1).

**Question (verbatim):**
> Given a sorted array of n elements, possibly with duplicates. Write down an algorithm/program to find the number of occurrences of a number.

**Answer**

Binary-search the **first** and **last** occurrence; the count is `last − first + 1`. This avoids scanning the whole array and keeps `O(log n)` time.

```
COUNT_OCCURRENCES(A, n, key):
1. first ← FIRST_OCC(A, 0, n−1, key)
2. if first = −1: return 0
3. last ← LAST_OCC(A, 0, n−1, key)
4. return last − first + 1

FIRST_OCC(A, low, high, key):
1. ans ← −1
2. while low ≤ high:
       mid ← (low + high) / 2
       if A[mid] = key: ans ← mid; high ← mid − 1    /* search left */
       else if A[mid] < key: low ← mid + 1
       else: high ← mid − 1
3. return ans

LAST_OCC(A, low, high, key):
1. ans ← −1
2. while low ≤ high:
       mid ← (low + high) / 2
       if A[mid] = key: ans ← mid; low ← mid + 1     /* search right */
       else if A[mid] < key: low ← mid + 1
       else: high ← mid − 1
3. return ans
```

**Example:** `A = [1, 2, 2, 2, 3, 4]`, `key = 2`.

| Search | mid sequence (value) | Result |
|---|---|---|
| First | 2 (2) → 0 (1) → 1 (2) | first = 1 |
| Last | 2 (2) → 4 (3) → 3 (2) | last = 3 |

Count = `3 − 1 + 1 = 3`.

**Complexity:** two binary searches → `O(log n)` time, `O(1)` space.

---

## 6.3 Fibonacci search **[5]**

**Asked in:** AI-F24 Q1(2); AI-R25 Q1(2); IT-F25 Q4A; CO-R26 Q4B.

**Questions (verbatim):**
> Write the Fibonacci search algorithm. How does it differ from binary search?
>
> Suppose we have a sorted array of elements [12, 14, 16, 17, 20, 24, 31, 43, 50, 62] find the location of element 24 in it using Fibonacci Search.
>
> Explain Linear Search, Binary Search, and Fibonacci Search and compare how each method searches for element 25 in the following array: [5, 10, 15, 20, 25, 30, 35].
>
> Write a program/algorithm to implement Fibonacci Search.

**Answer**

**Fibonacci search** is a comparison-based search on a **sorted** array. Instead of halving, it splits the array using Fibonacci numbers; this avoids division and is useful for sequential access (tapes, linked structures).

**Algorithm**

```
FIBONACCI_SEARCH(A, n, key):
1. fibM2 ← 0; fibM1 ← 1; fibM ← fibM2 + fibM1
2. while fibM < n:
       fibM2 ← fibM1
       fibM1 ← fibM
       fibM ← fibM1 + fibM2
3. offset ← −1
4. while fibM > 1:
       i ← min(offset + fibM2, n − 1)
       if A[i] < key:
           fibM ← fibM1
           fibM1 ← fibM2
           fibM2 ← fibM − fibM1
           offset ← i
       else if A[i] > key:
           fibM ← fibM2
           fibM1 ← fibM1 − fibM2
           fibM2 ← fibM − fibM1
       else:
           return i
5. if fibM1 and offset + 1 < n and A[offset + 1] = key:
       return offset + 1
6. return −1
```

**Trace — key 24 in `[12,14,16,17,20,24,31,43,50,62]`**

`n = 10`; the smallest Fibonacci number ≥ 10 is 13, so `fibM = 13, fibM1 = 8, fibM2 = 5`; `offset = −1`.

| Step | offset | fibM | fibM1 | fibM2 | i = min(offset+fibM2, n−1) | A[i] | Action |
|---|---:|---:|---:|---:|---:|---:|---|
| 1 | −1 | 13 | 8 | 5 | 4 | 20 | 20 < 24 → offset=4, fibM=8, fibM1=5, fibM2=3 |
| 2 | 4 | 8 | 5 | 3 | 7 | 43 | 43 > 24 → fibM=3, fibM1=2, fibM2=1 |
| 3 | 4 | 3 | 2 | 1 | 5 | 24 | **found at index 5** |

**Result: index 5** (6th element).

**Trace — key 25 in `[5,10,15,20,25,30,35]`**

`n = 7`; `fibM = 8, fibM1 = 5, fibM2 = 3`; `offset = −1`.

| Step | offset | fibM | fibM1 | fibM2 | i | A[i] | Action |
|---|---:|---:|---:|---:|---:|---:|---|
| 1 | −1 | 8 | 5 | 3 | 2 | 15 | 15 < 25 → offset=2, fibM=5, fibM1=3, fibM2=2 |
| 2 | 2 | 5 | 3 | 2 | 4 | 25 | **found at index 4** |

**Trace — key 88 in `[2,4,6,8,10,12,14,16]`**

`n = 8`; `fibM = 8, fibM1 = 5, fibM2 = 3`; `offset = −1`.

| Step | i | A[i] | Action |
|---|---:|---:|---|
| 1 | 2 | 6 | 6 < 88 → offset=2, fibM=5, fibM1=3, fibM2=2 |
| 2 | 4 | 10 | 10 < 88 → offset=4, fibM=3, fibM1=2, fibM2=1 |
| 3 | 5 | 12 | 12 < 88 → offset=5, fibM=2, fibM1=1, fibM2=1 |
| 4 | 6 | 14 | 14 < 88 → offset=6, fibM=1, fibM1=1, fibM2=0 |

`fibM = 1`, loop ends; check `A[7] = 16 ≠ 88` → **not found**.

**Fibonacci vs binary search**

| Point | Binary search | Fibonacci search |
|---|---|---|
| Split | Middle `(low+high)/2` | Fibonacci split `offset + fibM2` |
| Division | Needs division by 2 | Only addition/subtraction |
| Data access | Random access (array) | Works with sequential access |
| Time | `O(log n)` | `O(log n)` |
| Comparisons | ~`log₂n` | Slightly fewer on average in some distributions |
| Pre-processing | None | Compute Fibonacci numbers up to `n` |
| Use | Arrays in memory | Tapes, linked lists, large sequential data |

**C function**

```c
int fibSearch(int a[], int n, int key) {
    int fibM2 = 0, fibM1 = 1, fibM = fibM2 + fibM1;
    while (fibM < n) { fibM2 = fibM1; fibM1 = fibM; fibM = fibM1 + fibM2; }
    int offset = -1;
    while (fibM > 1) {
        int i = (offset + fibM2 < n) ? offset + fibM2 : n - 1;
        if (a[i] < key) {
            fibM = fibM1; fibM1 = fibM2; fibM2 = fibM - fibM1; offset = i;
        } else if (a[i] > key) {
            fibM = fibM2; fibM1 = fibM1 - fibM2; fibM2 = fibM - fibM1;
        } else return i;
    }
    if (fibM1 && offset + 1 < n && a[offset + 1] == key) return offset + 1;
    return -1;
}
```

---

## 6.4 Comparison of linear, binary and Fibonacci search **[5]**

**Asked in:** IT-F25 Q4A; CO-F24 Q4B(1).

**Question (verbatim):**
> Explain Linear Search, Binary Search, and Fibonacci Search and compare how each method searches for element 25 in the following array: [5, 10, 15, 20, 25, 30, 35].

**Answer**

**Search for 25 in `[5,10,15,20,25,30,35]`**

| Method | Probes | Result |
|---|---|---|
| **Linear** | `5` (indices 0,1,2,3,4) | found at index 4 — 5 comparisons |
| **Binary** | `mid 3 (20) → mid 5 (30) → mid 4 (25)` | found at index 4 — 3 comparisons |
| **Fibonacci** | `i=2 (15) → i=4 (25)` | found at index 4 — 2 comparisons |

**Comparison**

| Point | Linear | Binary | Fibonacci |
|---|---|---|---|
| Precondition | none | sorted + random access | sorted + sequential access |
| Strategy | scan one by one | halve the range | split by Fibonacci numbers |
| Best | `O(1)` | `O(1)` | `O(1)` |
| Worst | `O(n)` | `O(log n)` | `O(log n)` |
| Average | `O(n)` | `O(log n)` | `O(log n)` |
| Space | `O(1)` | `O(1)` | `O(1)` |
| Division | no | yes (`/2`) | no (add/subtract) |
| Linked list | yes | no | yes (sequential) |
| When to use | tiny/unsorted data | sorted array in RAM | sorted sequential storage |

**Conclusion:** binary is usually fastest on arrays; Fibonacci is preferable when division is expensive or access is sequential; linear is the only option for unsorted data.

---

## 6.5 Bubble sort **[5]**

**Asked in:** IT-F24 Q2B(1); IO-F24 Q1B(2) (complexity).

**Questions (verbatim):**
> Apply Bubble sort on given numbers and show intermediate steps 40, 10, 50, 70, 30.
>
> Find time complexity of following snippet … (bubble-sort nested loops).

**Answer**

**Idea:** repeatedly compare adjacent elements and swap them if they are out of order. After pass `i`, the largest `i` elements are in place.

```
BUBBLE_SORT(A, n):
1. for i ← 1 to n−1:
       swapped ← false
       for j ← 0 to n−i−1:
           if A[j] > A[j+1]:
               swap(A[j], A[j+1])
               swapped ← true
       if not swapped: break
```

**Trace — `40, 10, 50, 70, 30`**

| Pass | Comparisons and swaps | Array after pass |
|---|---|---|
| 1 | 40↔10, 50 ok, 70↔30 | `10, 40, 50, 30, 70` |
| 2 | 10 ok, 40 ok, 50↔30 | `10, 40, 30, 50, 70` |
| 3 | 10 ok, 40↔30 | `10, 30, 40, 50, 70` |
| 4 | no swaps | `10, 30, 40, 50, 70` (sorted) |

**Detailed pass 1**

```
[40, 10, 50, 70, 30]
compare 40,10 → swap → [10, 40, 50, 70, 30]
compare 40,50 → ok
compare 50,70 → ok
compare 70,30 → swap → [10, 40, 50, 30, 70]
```

**Complexity**

| Case | Comparisons | Time |
|---|---|---|
| Best (already sorted, with flag) | `n−1` | `O(n)` |
| Worst (reverse sorted) | `n(n−1)/2` | `O(n²)` |
| Average | `n(n−1)/2` | `O(n²)` |

Space `O(1)`; **stable**; in-place. The nested-loop snippet in IO-F24 is exactly this algorithm, so its complexity is `O(n²)`.

---

## 6.6 Selection sort **[5]**

**Asked in:** IT-R26 Q4B(1).

**Question (verbatim):**
> Step by Step Illustrate Selection Sort to arrange following numbers in ascending order. [30, 10, 50, 20, 40].

**Answer**

**Idea:** in each pass, find the minimum of the unsorted part and swap it into the next position.

```
SELECTION_SORT(A, n):
1. for i ← 0 to n−2:
       minIndex ← i
       for j ← i+1 to n−1:
           if A[j] < A[minIndex]: minIndex ← j
       swap(A[i], A[minIndex])
```

**Trace — `[30, 10, 50, 20, 40]`**

| Pass | Unsorted part | Minimum | Swap | Array after |
|---|---|---|---:|---|
| 1 | 30,10,50,20,40 | 10 (index 1) | 30↔10 | `10, 30, 50, 20, 40` |
| 2 | 30,50,20,40 | 20 (index 3) | 30↔20 | `10, 20, 50, 30, 40` |
| 3 | 50,30,40 | 30 (index 3) | 50↔30 | `10, 20, 30, 50, 40` |
| 4 | 50,40 | 40 (index 4) | 50↔40 | `10, 20, 30, 40, 50` |

**Complexity:** always `n(n−1)/2` comparisons → `O(n²)` in all cases; swaps at most `n−1` (good when writes are expensive). Space `O(1)`; **not stable** in the usual swap implementation.

---

## 6.7 Insertion sort **[5]**

**Asked in:** IT-R25 Q2B(1); CO-F24 Q4B; IO-F24 Q2A (nearly sorted).

**Questions (verbatim):**
> Apply Insertion sort on given numbers show intermediate steps 54, 26, 93, 17, 77, 31, 44, 55, 20.
>
> Write a program/algorithm to implement Insertion Sort.
>
> Suppose we have an array where we expect the majority of elements to be sorted “almost in order”. What would be a good sorting algorithm to use? Justify.

**Answer**

**Idea:** grow a sorted prefix. Take the next element and shift larger elements right until it finds its place.

```
INSERTION_SORT(A, n):
1. for i ← 1 to n−1:
       key ← A[i]
       j ← i − 1
       while j ≥ 0 and A[j] > key:
           A[j+1] ← A[j]
           j ← j − 1
       A[j+1] ← key
```

**Trace — `54, 26, 93, 17, 77, 31, 44, 55, 20`**

| i | key | Shifts | Array after |
|---:|---:|---|---|
| 1 | 26 | 54 → right | `26, 54, 93, 17, 77, 31, 44, 55, 20` |
| 2 | 93 | none | `26, 54, 93, 17, 77, 31, 44, 55, 20` |
| 3 | 17 | 93,54,26 → right | `17, 26, 54, 93, 77, 31, 44, 55, 20` |
| 4 | 77 | 93 → right | `17, 26, 54, 77, 93, 31, 44, 55, 20` |
| 5 | 31 | 93,77,54 → right | `17, 26, 31, 54, 77, 93, 44, 55, 20` |
| 6 | 44 | 93,77 → right | `17, 26, 31, 44, 54, 77, 93, 55, 20` |
| 7 | 55 | 93,77 → right | `17, 26, 31, 44, 54, 55, 77, 93, 20` |
| 8 | 20 | 93,77,55,54,44,31,26 → right | `17, 20, 26, 31, 44, 54, 55, 77, 93` |

**Complexity**

| Case | Comparisons | Time |
|---|---|---|
| Best (sorted) | `n−1` | `O(n)` |
| Worst (reverse) | `n(n−1)/2` | `O(n²)` |
| Average | `n²/4` | `O(n²)` |

Space `O(1)`; **stable**; in-place; adaptive (fast on nearly sorted data).

**Nearly sorted array — best choice**

**Insertion sort** (or a flagged bubble sort). Each element is already close to its final position, so the inner `while` shifts only a few places. If only `k` inversions exist, insertion sort runs in `O(n + k)`, effectively `O(n)` when `k` is small. Merge sort and quick sort ignore the existing order and still take `O(n log n)`; selection sort always takes `O(n²)`.

---

## 6.7A Sort an array of 0s, 1s and 2s **[5]**

**Asked in:** P8 Jan-24 Q2A(1).

**Question (verbatim):**
> Given an array A[] consisting of 0's, 1's and 2's, give an algorithm/program for sorting A[]. The algorithm should put all 0's first, then all 1's and finally all 2's at the end. Example Input = {0,1,1,0,1,2,1,2,0,0,0,1}, Output = {0,0,0,0,0,1,1,1,1,1,2,2}

**Answer**

Use the **Dutch National Flag** three-pointer partition. It sorts in one pass, `O(n)` time and `O(1)` space.

```
SORT_012(A, n):
1. low ← 0; mid ← 0; high ← n − 1
2. while mid ≤ high:
       if A[mid] = 0:
           swap(A[low], A[mid])
           low ← low + 1; mid ← mid + 1
       else if A[mid] = 1:
           mid ← mid + 1
       else:                         /* A[mid] = 2 */
           swap(A[mid], A[high])
           high ← high − 1           /* do not increment mid */
```

**Invariant**

```
A[0 … low−1] = 0
A[low … mid−1] = 1
A[mid … high] = unknown
A[high+1 … n−1] = 2
```

When `A[mid] = 2`, the element swapped in from `high` is unknown, so `mid` is **not** advanced. When `A[mid] = 0`, the element swapped in from `low` is a known 1 (or `mid == low`), so both pointers advance.

**Trace** on `[0,1,1,0,1,2,1,2,0,0,0,1]`

| Step | mid | low | high | Action | Array after |
|---|---:|---:|---:|---|---|
| 1 | 0 | 0 | 11 | `A[0]=0`, advance low/mid | `0,1,1,0,1,2,1,2,0,0,0,1` |
| 2 | 1 | 1 | 11 | `A[1]=1`, mid++ | `0,1,1,0,1,2,1,2,0,0,0,1` |
| 3 | 2 | 1 | 11 | `A[2]=1`, mid++ | `0,1,1,0,1,2,1,2,0,0,0,1` |
| 4 | 3 | 1 | 11 | `A[3]=0`, swap with low=1 | `0,0,1,1,1,2,1,2,0,0,0,1` |
| 5 | 4 | 2 | 11 | `A[4]=1`, mid++ | `0,0,1,1,1,2,1,2,0,0,0,1` |
| 6 | 5 | 2 | 11 | `A[5]=2`, swap with high=11 | `0,0,1,1,1,1,1,2,0,0,0,2` |
| 7 | 5 | 2 | 10 | `A[5]=1`, mid++ | `0,0,1,1,1,1,1,2,0,0,0,2` |
| 8 | 6 | 2 | 10 | `A[6]=1`, mid++ | `0,0,1,1,1,1,1,2,0,0,0,2` |
| 9 | 7 | 2 | 10 | `A[7]=2`, swap with high=10 | `0,0,1,1,1,1,1,0,0,0,2,2` |
| 10 | 7 | 2 | 9 | `A[7]=0`, swap with low=2 | `0,0,0,1,1,1,1,1,0,0,2,2` |
| 11 | 8 | 3 | 9 | `A[8]=0`, swap with low=3 | `0,0,0,0,1,1,1,1,1,0,2,2` |
| 12 | 9 | 4 | 9 | `A[9]=0`, swap with low=4 | `0,0,0,0,0,1,1,1,1,1,2,2` |

At the next check `mid = 10 > high = 9`, so the loop stops.

**Final:** `0,0,0,0,0,1,1,1,1,1,2,2`.

**Complexity:** each element is processed once → `O(n)` time, `O(1)` space; **not stable** (the swaps can change the order of equal keys, though equal 0/1/2 values are indistinguishable).

**Alternative:** counting sort — count the 0s, 1s and 2s in one pass, then overwrite the array. Also `O(n)` time but needs two passes and three counters.

---

## 6.8 Merge sort **[5–8]**

**Asked in:** IT-F24 Q2B(2); AI-F24 Q2(1); IT-R25 Q2B(2); IC-R26 Q4B(2).

**Questions (verbatim):**
> Apply Merge sort on given numbers and illustrate intermediate steps 38, 27, 43, 3, 9, 82, 10.
>
> Sort the array [38, 27, 43, 10] using the Merge Sort algorithm and also write time complexity of Merge Sort.
>
> Apply Merge sort on given numbers and illustrate intermediate steps: 40, 29, 45, 4, 11, 84, 12.
>
> Differentiate merge sort and quick sort. Sort given sequence using merge sort and analyze the complexity of merge sort algorithm. Sequence: 12, 8, 9, 3, 11, 5, 4

**Answer**

**Idea:** divide the array into two halves, sort each recursively, then **merge** two sorted halves in linear time.

```
MERGE_SORT(A, low, high):
1. if low ≥ high: return
2. mid ← (low + high) / 2
3. MERGE_SORT(A, low, mid)
4. MERGE_SORT(A, mid+1, high)
5. MERGE(A, low, mid, high)

MERGE(A, low, mid, high):
1. copy left = A[low…mid], right = A[mid+1…high]
2. i ← 0; j ← 0; k ← low
3. while i < |left| and j < |right|:
       if left[i] ≤ right[j]: A[k++] ← left[i++]
       else: A[k++] ← right[j++]
4. copy any remaining elements
```

**Trace — `[38, 27, 43, 3, 9, 82, 10]`**

```
Split:
[38, 27, 43, 3] | [9, 82, 10]
[38, 27] [43, 3] | [9, 82] [10]
[38] [27] [43] [3] | [9] [82] [10]

Merge:
[27, 38]  [3, 43]  | [9, 82]  [10]
[3, 27, 38, 43]    | [9, 10, 82]
[3, 9, 10, 27, 38, 43, 82]
```

| Level | Arrays |
|---|---|
| Split 1 | `38 27 43 3` and `9 82 10` |
| Split 2 | `38 27`, `43 3`, `9 82`, `10` |
| Split 3 | single elements |
| Merge 1 | `27 38`, `3 43`, `9 82`, `10` |
| Merge 2 | `3 27 38 43`, `9 10 82` |
| Merge 3 | **`3 9 10 27 38 43 82`** |

**Trace — `[38, 27, 43, 10]`**

```
[38,27] [43,10] → [27,38] [10,43] → [10,27,38,43]
```

**Trace — `[40, 29, 45, 4, 11, 84, 12]`**

```
[40,29,45] → [29,40,45]
[4,11,84,12] → [4,11] [12,84] → [4,11,12,84]
merge → [4,11,12,29,40,45,84]
```

**Trace — `[12, 8, 9, 3, 11, 5, 4]`**

```
[12,8,9] → [8,9,12]
[3,11,5,4] → [3,11] [4,5] → [3,4,5,11]
merge → [3,4,5,8,9,11,12]
```

**Complexity**

```
T(n) = 2T(n/2) + O(n)
     = O(n log n)
```

| Case | Time |
|---|---|
| Best | `O(n log n)` |
| Average | `O(n log n)` |
| Worst | `O(n log n)` |

Space `O(n)` for the temporary merge arrays (plus `O(log n)` recursion). **Stable**; not in-place. Preferred when guaranteed `O(n log n)` and stability matter; slower than quick sort in practice due to copying.

---

## 6.9 Quick sort **[5–10]**

**Asked in:** AI-R25 Q2(1); IT-F25 Q4B(2); AI-R26 Q1B; CO-B26 Q1B; CO-R25 Q4B; P8 Jan-24 Q3B(2).

**Questions (verbatim):**
> Sort the array [24, 9, 29, 14, 19, 27] using the Quick Sort algorithm and also write time complexity of Quick Sort.
>
> Using Last element as pivot element, step by step illustrate Quick Sort to sort following numbers: [5, 3, 8, 4, 2, 7, 1]
>
> Apply Quick Sort using the last element as the pivot at every partition and sort the array in ascending order. Show the pivot position and partitioned arrays after each recursive step. Array: 15, 82, 63, 91, 28, 74, 47, 39, 55, 18, 70, 26. Comment on Complexity of Quick sort when it run in worst case with any suitable example.
>
> Write a program/Pseudocode to implement Quick Sort. Also discuss about Quick sort time complexity if input elements are already sorted in either order.
>
> Write a program/algorithm to implement Quick Sort.
>
> Perform Worst Case analysis of Quick Sort algorithm.

**Answer**

**Idea:** choose a **pivot**, partition the array so that elements ≤ pivot are on its left and > pivot on its right, then recurse on both sides. The pivot is in its final position after each partition.

```
QUICK_SORT(A, low, high):
1. if low ≥ high: return
2. p ← PARTITION(A, low, high)
3. QUICK_SORT(A, low, p−1)
4. QUICK_SORT(A, p+1, high)

PARTITION_LAST(A, low, high):       /* Lomuto, pivot = A[high] */
1. pivot ← A[high]; i ← low − 1
2. for j ← low to high−1:
       if A[j] ≤ pivot:
           i ← i + 1
           swap(A[i], A[j])
3. swap(A[i+1], A[high])
4. return i + 1
```

**Trace — `[24, 9, 29, 14, 19, 27]` with the first element as pivot**

| Step | Pivot | Partition result | Pivot position |
|---|---:|---|---:|
| 1 | 24 | `14, 9, 19, 24, 29, 27` | 3 |
| 2 | 14 (left part) | `9, 14, 19` | 1 |
| 3 | 29 (right part) | `27, 29` | 5 |

Final: **`9, 14, 19, 24, 27, 29`**. (If your class uses the last element as pivot, the same array sorts to the same result with a different partition order; state the convention.)

**Trace — `[5, 3, 8, 4, 2, 7, 1]`, last element pivot**

| Recursive call | Pivot | Array after partition | Pivot index |
|---|---:|---|---:|
| whole array `[0..6]` | 1 | `1, 3, 8, 4, 2, 7, 5` | 0 |
| `[1..6]` | 5 | `1, 3, 4, 2, 5, 7, 8` | 4 |
| `[1..3]` | 2 | `1, 2, 4, 3, 5, 7, 8` | 1 |
| `[2..3]` | 3 | `1, 2, 3, 4, 5, 7, 8` | 2 |
| `[5..6]` | 8 | `1, 2, 3, 4, 5, 7, 8` | 6 |

Final: **`1, 2, 3, 4, 5, 7, 8`**.

**Trace — `[15, 82, 63, 91, 28, 74, 47, 39, 55, 18, 70, 26]`, last element pivot**

| Recursive call | Pivot | Array after partition | Pivot index |
|---|---:|---|---:|
| `[0..11]` | 26 | `15, 18, 26, 91, 28, 74, 47, 39, 55, 82, 70, 63` | 2 |
| `[0..1]` | 18 | `15, 18, 26, …` | 1 |
| `[3..11]` | 63 | `… 28, 47, 39, 55, 63, 91, 82, 70, 74` | 7 |
| `[3..6]` | 55 | `… 28, 47, 39, 55, 63, …` | 6 |
| `[3..5]` | 39 | `… 28, 39, 47, 55, …` | 4 |
| `[8..11]` | 74 | `… 70, 74, 91, 82` | 9 |
| `[10..11]` | 82 | `… 82, 91` | 10 |

Final: **`15, 18, 26, 28, 39, 47, 55, 63, 70, 74, 82, 91`**.

**Complexity**

| Case | Recurrence | Time |
|---|---|---|
| Best | `2T(n/2) + O(n)` | `O(n log n)` |
| Average | balanced partitions | `O(n log n)` |
| Worst | `T(n−1) + O(n)` | `O(n²)` |

Space `O(log n)` average recursion stack, `O(n)` worst. **Not stable**; in-place.

**Worst case**

The worst case occurs when the pivot is always the smallest or largest element, e.g., an **already sorted** array with the first or last element as pivot:

```
[1,2,3,4,5,6,7,8]
pivot 8 → partition [1..7] + [8]
pivot 7 → [1..6] + [7]
…
T(n) = T(n−1) + O(n) = O(n²)
```

The same happens for reverse-sorted input. **Fixes:** random pivot, median-of-three, or shuffle before sorting; these make the expected time `O(n log n)`.

**Quick vs merge sort**

| Point | Quick sort | Merge sort |
|---|---|---|
| Strategy | partition around pivot | divide and merge |
| Worst time | `O(n²)` | `O(n log n)` |
| Average | `O(n log n)` | `O(n log n)` |
| Space | `O(log n)` stack | `O(n)` extra |
| Stable | No | Yes |
| In-place | Yes (apart from stack) | No |
| Speed in practice | Usually fastest | Good, predictable |
| Sensitive to input order | Yes (sorted/reverse worst) | No |

---

## 6.10 Radix sort **[5–10]**

**Asked in:** P8 Jan-24 Q1B; IT-R26 Q4B(2); IC-R26 Q4B(1).

**Questions (verbatim):**
> State Idea behind Radix Sort. Illustrate Radix Sort stepwise for following elements 170, 45, 75, 90, 802, 24, 2, 66.
>
> Step by step Illustrate Radix Sort to sort the following numbers: [329, 457, 657, 839, 436, 720, 355].
>
> Write an algorithm / function for the Radix sort. Consider below sequence and sort it pass-by-pass: 170, 45, 75, 90, 802, 24, 2, 66. Analyze the time complexity of the algorithm and justify the same.

**Answer**

**Idea:** radix sort is a **non-comparison** sort. It processes digits from the **least significant digit (LSD)** to the most significant, using a **stable** bucket sort on each digit. After the last pass, the array is sorted.

```
RADIX_SORT(A):
1. max ← maximum element
2. for exp ← 1, 10, 100, … while max / exp > 0:
       counting/bucket sort A on digit (A[i] / exp) mod 10 (stable)
3. return A
```

**Trace — `[170, 45, 75, 90, 802, 24, 2, 66]`**

**Pass 1 — units digit**

| Digit | Bucket |
|---:|---|
| 0 | 170, 90 |
| 1 | — |
| 2 | 802, 2 |
| 3 | — |
| 4 | 24 |
| 5 | 45, 75 |
| 6 | 66 |
| 7–9 | — |

After pass 1: `170, 90, 802, 2, 24, 45, 75, 66`

**Pass 2 — tens digit**

| Digit | Bucket |
|---:|---|
| 0 | 802, 2 |
| 1 | — |
| 2 | 24 |
| 3 | — |
| 4 | 45 |
| 5 | — |
| 6 | 66 |
| 7 | 170, 75 |
| 8 | — |
| 9 | 90 |

After pass 2: `802, 2, 24, 45, 66, 170, 75, 90`

**Pass 3 — hundreds digit**

| Digit | Bucket |
|---:|---|
| 0 | 2, 24, 45, 66, 75, 90 |
| 1 | 170 |
| 8 | 802 |

After pass 3: **`2, 24, 45, 66, 75, 90, 170, 802`**

**Trace — `[329, 457, 657, 839, 436, 720, 355]`**

| Pass | Digit | Array after pass |
|---|---|---|
| 1 | units | `720, 355, 436, 457, 657, 329, 839` |
| 2 | tens | `720, 329, 436, 839, 355, 457, 657` |
| 3 | hundreds | `329, 355, 436, 457, 657, 720, 839` |

Final: **`329, 355, 436, 457, 657, 720, 839`**.

**Complexity**

Let `d` = number of digits in the largest number, `n` = number of elements, `k` = radix (10 here).

```
Time  = O(d × (n + k))
Space = O(n + k)
```

For fixed-width integers, `d` is constant, so radix sort is effectively **`O(n)`** — faster than `O(n log n)` comparison sorts. It is **stable** (each pass preserves the order of equal digits) and not in-place (needs buckets).

---

## 6.11 Heap sort **[5]**

**Asked in:** IT-F25 Q4B(1).

**Question (verbatim):**
> Step by Step Illustrate Heap Sort to arrange following numbers in ascending order. [40, 20, 30, 10, 50].

**Answer**

**Idea:** build a **max-heap**, then repeatedly swap the root (maximum) with the last element, shrink the heap, and heapify the new root. This places the maximum at the end each time.

```
HEAP_SORT(A, n):
1. BUILD_MAX_HEAP(A, n)
2. for end ← n down to 2:
       swap(A[1], A[end])
       MAX_HEAPIFY(A, 1, end−1)
```

**Trace — `[40, 20, 30, 10, 50]`**

**Build max-heap**

| Step | Action | Array |
|---|---|---|
| start | — | `40, 20, 30, 10, 50` |
| heapify i=2 (20) | children 10, 50 → swap 20/50 | `40, 50, 30, 10, 20` |
| heapify i=1 (40) | children 50, 30 → swap 40/50 | `50, 40, 30, 10, 20` |

Max-heap: `[50, 40, 30, 10, 20]`

```
        50
       /  \
     40    30
    /  \
   10  20
```

**Extraction phase**

| Step | Swap root with last | Heapify | Array after |
|---|---|---|---|
| 1 | 50 ↔ 20 | 20 with 40 → swap | `40, 20, 30, 10, 50` |
| 2 | 40 ↔ 10 | 10 with 30 → swap | `30, 20, 10, 40, 50` |
| 3 | 30 ↔ 10 | 10 with 20 → swap | `20, 10, 30, 40, 50` |
| 4 | 20 ↔ 10 | no child | `10, 20, 30, 40, 50` |

**Sorted: `10, 20, 30, 40, 50`.**

**Complexity:** build-heap `O(n)`, `n−1` extractions each `O(log n)` → **`O(n log n)`** in all cases. Space `O(1)`; **not stable**.

---

## 6.12 Comparison of sorting algorithms **[5]**

**Asked in:** IO-F24 Q2A (nearly sorted); IC-R26 Q4B(2) (merge vs quick); CO-B26 Q1B (quick sorted input).

**Question (verbatim):**
> Suppose we have an array where we expect the majority of elements to be sorted “almost in order”. What would be a good sorting algorithm to use? Justify.

**Answer**

| Algorithm | Best | Average | Worst | Extra space | Stable | In-place | Adaptive |
|---|---|---|---|---|---|---|---|
| **Bubble** | `O(n)` | `O(n²)` | `O(n²)` | `O(1)` | Yes | Yes | Yes (with flag) |
| **Selection** | `O(n²)` | `O(n²)` | `O(n²)` | `O(1)` | No | Yes | No |
| **Insertion** | `O(n)` | `O(n²)` | `O(n²)` | `O(1)` | Yes | Yes | **Yes** |
| **Merge** | `O(n log n)` | `O(n log n)` | `O(n log n)` | `O(n)` | Yes | No | No |
| **Quick** | `O(n log n)` | `O(n log n)` | `O(n²)` | `O(log n)` | No | Yes | No |
| **Heap** | `O(n log n)` | `O(n log n)` | `O(n log n)` | `O(1)` | No | Yes | No |
| **Radix** | `O(d(n+k))` | `O(d(n+k))` | `O(d(n+k))` | `O(n+k)` | Yes | No | No |

**Nearly sorted input → insertion sort.** Its inner loop shifts an element only past the few larger elements before it. With `k` inversions, the work is `O(n + k)`, which is `O(n)` when `k` is small. It is stable and in-place. A flagged bubble sort is also `O(n)` on already-sorted input but does more comparisons on nearly sorted data.

**Other choices**

- Guaranteed `O(n log n)` + stability → **merge sort**.
- In-memory average speed, no stability requirement → **quick sort**.
- `O(1)` extra space + guaranteed `O(n log n)` → **heap sort**.
- Fixed-width integers/strings → **radix sort**.
- Small `n` (say `n < 20`) → insertion sort, even inside quick/merge as a base case.

---

## 6.13 Hashing and hash functions **[5]**

**Asked in:** CO-F24 Q1B; P8 Jan-24 Q2A(2); IT-R26 Q4A; IT-F24 Q2A (collision methods).

**Questions (verbatim):**
> What is Collision? Explain Multiplication method of Hash Function in brief. Given a hash table of size 1000, map the key 12345 to an appropriate location in the hash table using multiplication method.
>
> Apply Mid-square method hash function to generate hash addresses for keys 5642, 6789 and 5124. The hash table has 100 memory locations and addresses are from 00 to 99.
>
> Compare hashing and rehashing. Hash the keys [5, 15, 25, 35] using hash function h(k) = k % 10 in table size 5.
>
> Explain the common methods of resolving collisions in hash tables.

**Answer**

**Hashing** maps a key `k` to a table index `h(k)` so that search/insert/delete take **`O(1)` average** time. A **collision** occurs when two different keys map to the same index (`h(k₁) = h(k₂)`). Collisions are unavoidable when the key universe is larger than the table.

**Properties of a good hash function**

1. **Uniform:** distributes keys evenly over the table.
2. **Deterministic:** same key always gives the same index.
3. **Fast:** `O(1)` to compute.
4. **Uses the whole key**, not just one part.
5. **Minimises clustering** and is hard to invert (for security).

**Common hash functions**

| Method | Formula / idea | Example |
|---|---|---|
| **Division** | `h(k) = k mod m`, `m` prime or with few factors | `h(12345) = 12345 mod 1000 = 345` |
| **Mid-square** | square the key, take middle `r` digits | see below |
| **Folding** | split key into parts, add them, take last digits | `123456 → 12+34+56 = 102 → 02` |
| **Multiplication** | `h(k) = ⌊m × frac(k·A)⌋`, `0 < A < 1` | see below |
| **Universal** | randomly chosen function from a family | `h(k) = ((a·k + b) mod p) mod m` |

**Mid-square — 100 locations, 2-digit addresses**

| Key | Square | Middle 2 digits | Address |
|---:|---:|---:|---:|
| 5642 | 31,832,164 | 32 | **32** |
| 6789 | 46,090,521 | 90 | **90** |
| 5124 | 26,255,376 | 55 | **55** |

(Write the square with 8 digits; take digits 4–5.)

**Multiplication method — table size 1000, key 12345**

Use `A = (√5 − 1)/2 ≈ 0.6180339887`.

```
k·A = 12345 × 0.6180339887 = 7629.6296
frac = 0.6296
h(k) = ⌊1000 × 0.6296⌋ = 629
```

**Address = 629.** The method is less sensitive to the choice of `m` than division hashing.

**Load factor**

```
α = n / m        (number of keys / table size)
```

Average successful search with chaining ≈ `1 + α/2`; unsuccessful ≈ `α`. Keep `α ≤ 0.75` for open addressing.

---

## 6.14 Collision resolution: open addressing and chaining **[5–10]**

**Asked in:** IT-F24 Q2A; IT-R25 Q2A; CO-R25 Q1B; CO-R26 Q1B; IO-F24 Q4B(2); IC-R26 Q3B; CO-B26 Q2A(2); AI-F24 Q2(1)(ii); AI-R25 Q2(2) (chaining alternative).

**Questions (verbatim):**
> Explain the common methods of resolving collisions in hash tables. Given the values {2341, 4234, 2839, 430, 22, 397, 3920}, a hash table of size 7, and hash function h(x) = x mod 7, show the resulting tables after inserting the values in the given order and resolve collision using Quadratic Probing with c1=0 and c2=1.
>
> Discuss Double Hashing. Apply double hashing for collision resolution to store the following values inside a hash table of size 20 using h1(n)=n%20 and h2(n)=n%13. 16, 8, 63, 9, 27, 37, 48, 5, 69, 34, 1.
>
> Consider a hash table of size 10. Using quadratic probing, insert the keys 72, 27, 36, 24, 63, 81, and 101 into the table. Take c1 = 1 and c2 = 3.
>
> Consider a hash table of size 10. Using linear probing, insert the keys 72, 27, 36, 24, 63, 81, 92, and 101 into the table.
>
> Consider a hash table with a size of 10. Using linear probing, insert the keys 27,37,62,28,36,18,29,101 into the table.
>
> A hash table of size 10 uses linear probing to resolve collision. Consider Hash function h(k) = Key % 10. Illustrate linear probing hashing with hash table that results after inserting elements in following order: 28, 22, 67, 88, 11, 90, 44, 9, 34, 32.
>
> Insert the keys 7, 24, 18, 52, 36, 54, 11, and 23 in a hash table of size 9. Use h(k) = k mod m. Use separate chaining method to address collision, if any.
>
> Insert the following sequence of keys in the hash table [3, 2, 9, 6, 11, 13, 7, 12] using modulo division hashing technique & linear probing for collision resolution.

**Answer**

**Open addressing** keeps all keys in the table. On collision, probe another slot:

| Method | Probe sequence |
|---|---|
| **Linear probing** | `(h(k) + i) mod m`, `i = 0,1,2,…` |
| **Quadratic probing** | `(h(k) + c₁i + c₂i²) mod m` |
| **Double hashing** | `(h₁(k) + i·h₂(k)) mod m` |

**Chaining** stores colliding keys in a linked list attached to the bucket; the table never overflows (unless memory runs out).

### 6.14.1 IT-F24 — quadratic probing, `m=7`, `c₁=0, c₂=1`

Keys `2341, 4234, 2839, 430, 22, 397, 3920`; `h(k) = k mod 7`.

| Key | `h(k)` | Probe sequence | Slot |
|---|---:|---|---:|
| 2341 | 3 | 3 free | **3** |
| 4234 | 6 | 6 free | **6** |
| 2839 | 4 | 4 free | **4** |
| 430 | 3 | 3 occupied → +1=4 occupied → +4=7≡0 free | **0** |
| 22 | 1 | 1 free | **1** |
| 397 | 5 | 5 free | **5** |
| 3920 | 0 | 0 occupied → 1 occupied → 4 occupied → 9≡2 free | **2** |

**Table**

| Index | 0 | 1 | 2 | 3 | 4 | 5 | 6 |
|---|---|---|---|---|---|---|---|
| Key | 430 | 22 | 3920 | 2341 | 2839 | 397 | 4234 |

**Collisions:** 2 for 430 + 3 for 3920 = **5 collisions** (extra probes). Load factor `7/7 = 1`.

### 6.14.2 IT-R25 — double hashing, `m=20`, `h₁=n%20`, `h₂=n%13`

Keys: `16, 8, 63, 9, 27, 37, 48, 5, 69, 34, 1`.

| Key | h₁ | h₂ | Probe sequence | Slot |
|---|---:|---:|---|---:|
| 16 | 16 | 3 | 16 free | **16** |
| 8 | 8 | 8 | 8 free | **8** |
| 63 | 3 | 11 | 3 free | **3** |
| 9 | 9 | 9 | 9 free | **9** |
| 27 | 7 | 1 | 7 free | **7** |
| 37 | 17 | 11 | 17 free | **17** |
| 48 | 8 | 9 | 8 occupied → 17 occupied → 26≡6 free | **6** |
| 5 | 5 | 5 | 5 free | **5** |
| 69 | 9 | 4 | 9 occupied → 13 free | **13** |
| 34 | 14 | 8 | 14 free | **14** |
| 1 | 1 | 1 | 1 free | **1** |

**Table**

| Index | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10–12 | 13 | 14 | 15 | 16 | 17 | 18 | 19 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Key | — | 1 | — | 63 | — | 5 | 48 | 27 | 8 | 9 | — | 69 | 34 | — | 16 | 37 | — | — |

**Collisions:** 2 (48) + 1 (69) = **3**. Load factor `11/20 = 0.55`.

### 6.14.3 CO-R25 — quadratic probing, `m=10`, `c₁=1, c₂=3`

Keys: `72, 27, 36, 24, 63, 81, 101`; `h(k)=k mod 10`.

| Key | h(k) | Probe | Slot |
|---|---:|---|---:|
| 72 | 2 | free | **2** |
| 27 | 7 | free | **7** |
| 36 | 6 | free | **6** |
| 24 | 4 | free | **4** |
| 63 | 3 | free | **3** |
| 81 | 1 | free | **1** |
| 101 | 1 | 1 occupied → `1+1+3=5` free | **5** |

**Table:** `—, 81, 72, 63, 24, 101, 36, 27, —, —`

**Collisions:** 1.

### 6.14.4 CO-R26 — linear probing, `m=10`

Keys: `72, 27, 36, 24, 63, 81, 92, 101`.

| Key | h(k) | Probes | Slot |
|---|---:|---|---:|
| 72 | 2 | 2 | **2** |
| 27 | 7 | 7 | **7** |
| 36 | 6 | 6 | **6** |
| 24 | 4 | 4 | **4** |
| 63 | 3 | 3 | **3** |
| 81 | 1 | 1 | **1** |
| 92 | 2 | 2→3→4→5 | **5** |
| 101 | 1 | 1→2→3→4→5→6→7→8 | **8** |

**Table:** `—, 81, 72, 63, 24, 92, 36, 27, 101, —`

**Collisions:** 3 (92) + 7 (101) = **10**.

### 6.14.5 IO-F24 — linear probing, `m=10`

Keys: `27, 37, 62, 28, 36, 18, 29, 101`.

| Key | h(k) | Probes | Slot |
|---|---:|---|---:|
| 27 | 7 | 7 | **7** |
| 37 | 7 | 7→8 | **8** |
| 62 | 2 | 2 | **2** |
| 28 | 8 | 8→9 | **9** |
| 36 | 6 | 6 | **6** |
| 18 | 8 | 8→9→0 | **0** |
| 29 | 9 | 9→0→1 | **1** |
| 101 | 1 | 1→2→3 | **3** |

**Table:** `18, 29, 62, 101, —, —, 36, 27, 37, 28`

**Collisions:** 1+1+2+2+2 = **8**.

### 6.14.6 IC-R26 — linear probing, `m=10`

Keys: `28, 22, 67, 88, 11, 90, 44, 9, 34, 32`.

| Key | h(k) | Probes | Slot |
|---|---:|---|---:|
| 28 | 8 | 8 | **8** |
| 22 | 2 | 2 | **2** |
| 67 | 7 | 7 | **7** |
| 88 | 8 | 8→9 | **9** |
| 11 | 1 | 1 | **1** |
| 90 | 0 | 0 | **0** |
| 44 | 4 | 4 | **4** |
| 9 | 9 | 9→0→1→2→3 | **3** |
| 34 | 4 | 4→5 | **5** |
| 32 | 2 | 2→3→4→5→6 | **6** |

**Table:** `90, 11, 22, 9, 44, 34, 32, 67, 28, 88`

**Collisions:** 1+3+1+4 = **9**.

### 6.14.7 CO-B26 — separate chaining, `m=9`

Keys: `7, 24, 18, 52, 36, 54, 11, 23`; `h(k) = k mod 9`.

| Bucket | Chain |
|---:|---|
| 0 | 18 → 36 → 54 |
| 1 | — |
| 2 | 11 |
| 3 | — |
| 4 | — |
| 5 | 23 |
| 6 | 24 |
| 7 | 7 → 52 |
| 8 | — |

Collisions are the extra nodes in each chain: bucket 0 has 2, bucket 7 has 1 → **3 collisions**. Average chain length `8/9 = 0.89`.

### 6.14.8 AI-F24 / AI-R25 — 8 keys, printed table size 5

AI-F24 asks for `[3, 2, 9, 6, 11, 13, 7, 12]` with “modulo division hashing and linear probing” but gives no table size. AI-R25 asks the same keys with **linked-list chaining**. With 8 keys, a 5-slot table cannot hold them by open addressing (at most 5 keys), so the self-consistent reading is `h(k) = k mod 5` with **separate chaining**:

| Bucket | Chain |
|---:|---|
| 0 | — |
| 1 | 6 → 11 |
| 2 | 2 → 7 → 12 |
| 3 | 3 → 13 |
| 4 | 9 |

Check: `3 mod 5 = 3`, `2 mod 5 = 2`, `9 mod 5 = 4`, `6 mod 5 = 1`, `11 mod 5 = 1`, `13 mod 5 = 3`, `7 mod 5 = 2`, `12 mod 5 = 2`.

**If linear probing is required**, use a 10-slot table (the next convenient size): `h(k)=k mod 10` gives `3,2,9,6,11→1,13→3→4,7,12→2→3→4→5`; table `—,11,2,3,13,12,6,7,—,9`, 4 collisions. State the table size explicitly in the exam.

### 6.14.9 Comparison of collision methods

| Method | Probe | Advantage | Disadvantage |
|---|---|---|---|
| **Linear probing** | `h(k)+i` | cache-friendly, simple | **primary clustering**; deletions need tombstones |
| **Quadratic probing** | `h(k)+c₁i+c₂i²` | reduces primary clustering | **secondary clustering**; may not visit all slots |
| **Double hashing** | `h₁(k)+i·h₂(k)` | best distribution among open addressing; few clusters | two hash functions; `h₂` must be coprime to `m` |
| **Separate chaining** | linked list per bucket | handles high load; easy deletion | extra pointers; cache-unfriendly |

**Deletion in open addressing** cannot simply empty a slot (it would break probe chains); mark it **DELETED** (tombstone) or rehash the cluster.

---

## 6.15 Hashing vs rehashing **[5]**

**Asked in:** IT-R26 Q4A.

**Question (verbatim):**
> Compare hashing and rehashing. Hash the keys [5, 15, 25, 35] using hash function h(k) = k % 10 in table size 5.

**Answer**

| Point | Hashing | Rehashing |
|---|---|---|
| Meaning | Map a key to a table index with `h(k)` | Grow the table and reinsert all keys with a new hash function |
| When | Every insert/search/delete | Load factor exceeds a threshold (e.g., 0.75) |
| Cost | `O(1)` average per operation | `O(n)` for the rebuild; amortised `O(1)` per insert |
| Table size | Fixed (or chained) | Usually doubled, then rounded to the next prime |
| Purpose | Fast direct access | Restore a low load factor and short probe chains |
| Effect on keys | — | Every key gets a new index; old positions are invalid |

**Rehashing procedure**

```
REHASH(old):
1. newSize ← nextPrime(2 × oldSize)
2. create new table of newSize
3. for each key in old table:
       insert key into new table using h_new
4. free old table; update size and count
```

**IT-R26 numeric**

The printed question asks `h(k) = k % 10` with table size 5. These are inconsistent: `k % 10` can return 5–9, which are outside a 5-slot table. Two self-consistent readings:

1. **Use `h(k) = k % 5` (table size 5).** All four keys give `0`:
   `5 mod 5 = 0, 15 mod 5 = 0, 25 mod 5 = 0, 35 mod 5 = 0`.
   Linear probing: slot 0 = 5, slot 1 = 15, slot 2 = 25, slot 3 = 35, slot 4 empty. Extra probes = `0+1+2+3 = 6` (open addressing collisions).
2. **Use `h(k) = k % 10` with table size 10.** All keys give 5; linear probing: slot 5 = 5, 6 = 15, 7 = 25, 8 = 35. **6 collisions**.

The first reading matches the stated table size; the second matches the stated function. In the exam, write the table size explicitly and use `h(k) = k mod tableSize`; mention the mismatch if you want to be safe.

**Rehashing example:** if the 5-slot table above reaches load factor 0.8, rehash to 11 slots and insert with `h(k)=k mod 11`: `5→5, 15→4, 25→3, 35→2` — no collisions.

---

## 6.16 Hashing applications and extendible hashing **[5]**

**Asked in:** IT-R26 Q4A (comparison); linear and extendible hashing are part of the syllabus.

**Answer**

**Applications**

1. **Symbol tables** in compilers (identifier → type/address).
2. **Database indexes** (hash index for equality joins).
3. **Caches and dictionaries** (`dict`, `HashMap`, `unordered_map`).
4. **Password storage** (salted cryptographic hashes, not plain hashing).
5. **File systems** (name → inode).
6. **Deduplication** (content hash).
7. **Bloom filters** and routing tables.

**Linear hashing** grows the table one bucket at a time (not a full rehash), using a split pointer and two hash functions `h₀, h₁`; it handles dynamic growth with predictable `O(1)` amortised cost.

**Extendible hashing** uses a directory of pointers to buckets and a local depth per bucket. When a bucket overflows, its local depth increases and it splits; the directory doubles only when the global depth must increase. This avoids long chains and full rehashing; it is used in database systems.

**Comparison**

| Scheme | Growth | Directory | Best for |
|---|---|---|---|
| Static hashing | fixed | no | known, stable size |
| Linear hashing | one bucket at a time | no (or small) | slowly growing files |
| Extendible hashing | bucket split + directory doubling | yes | large dynamic databases |

---

## Quick revision checklist — Unit 6

**Searching**

- [ ] Linear: `O(n)`, unsorted, `n` comparisons worst.
- [ ] Binary: sorted, `O(log n)`, `mid=(low+high)/2`.
- [ ] Binary trace 35 in `[4,9,13,21,27,35,41,56,62]`: mid 4, 6, 5.
- [ ] Fibonacci: `O(log n)`, `fibM/fibM1/fibM2`, no division.
- [ ] Fibonacci 24 in 10 elements: indices 4, 7, 5.
- [ ] Linear vs binary vs Fibonacci: precondition, access, comparisons.

**Sorting**

- [ ] Bubble: adjacent swaps, `O(n²)`, stable, `O(n)` best with flag.
- [ ] Bubble trace `40,10,50,70,30` → `10,30,40,50,70`.
- [ ] Selection: select minimum, `O(n²)` always, not stable.
- [ ] Insertion: sorted prefix, `O(n²)` worst, `O(n)` best, stable, adaptive.
- [ ] Merge: divide + merge, `O(n log n)` all cases, `O(n)` space, stable.
- [ ] Quick: partition, `O(n log n)` average, `O(n²)` worst, `O(log n)` stack, not stable.
- [ ] Quick worst case: sorted/reverse with first/last pivot.
- [ ] Radix: LSD passes, stable buckets, `O(d(n+k))`.
- [ ] Heap: build max-heap, extract, `O(n log n)`, `O(1)` space.
- [ ] Comparison table: best/avg/worst/space/stable.
- [ ] Nearly sorted → insertion sort `O(n)`.

**Hashing**

- [ ] Hash function: division, mid-square, folding, multiplication, universal.
- [ ] Collision: two keys → same index.
- [ ] Load factor `α = n/m`.
- [ ] Linear probing: `h+i`; primary clustering.
- [ ] Quadratic: `h+c₁i+c₂i²`; secondary clustering.
- [ ] Double hashing: `h₁+i·h₂`; best distribution.
- [ ] Chaining: linked list per bucket; easy deletion.
- [ ] Open addressing deletion needs tombstones.
- [ ] Rehashing: grow table, reinsert all; `O(n)` rebuild.
- [ ] Mid-square: 5642→32, 6789→90, 5124→55.
- [ ] Multiplication `m=1000, k=12345` → 629.
- [ ] IT-F24 quadratic table `430,22,3920,2341,2839,397,4234`.
- [ ] IT-R25 double hashing: 11 keys, 3 collisions.
- [ ] CO-R26 linear: table `—,81,72,63,24,92,36,27,101,—`.
- [ ] IO-F24 linear: table `18,29,62,101,—,—,36,27,37,28`.
- [ ] IC-R26 linear: table `90,11,22,9,44,34,32,67,28,88`.
- [ ] CO-B26 chaining: 0:18→36→54; 7:7→52.
