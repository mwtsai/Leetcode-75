# 605 - Can Place Flowers

## Problem

You have a long flowerbed represented by an integer array `flowerbed`, where:

* `0` means the position is empty.
* `1` means the position already contains a flower.

Flowers cannot be planted in adjacent positions.

Given an integer `n`, return `true` if `n` new flowers can be planted without violating the no-adjacent-flowers rule.

---

## Solution

### Approach: Greedy with Pattern Skipping

Instead of checking every position independently, scan from left to right and use the current position and the next position to determine how far we can safely skip.

The key idea is:

> Once the current pattern tells us that certain positions cannot be used, we should skip them instead of checking them again.

There are three main patterns:

```text
[1, ?]  → skip 2 positions

[0, 1]  → skip 3 positions

[0, 0]  → plant at current position, then skip 2 positions
```

We only need one pointer, `left`.

The next position can always be represented as:

```text
left + 1
```

Therefore, a separate `right` pointer is unnecessary.

---

### Code

```java
class Solution {
    public boolean canPlaceFlowers(int[] flowerbed, int n) {
        // If no flowers need to be planted, we are already done.
        if (n <= 0) return true;

        int left = 0;

        while (left < flowerbed.length) {

            // If there is already a flower at the current position,
            // skip the current position and the next position.
            if (flowerbed[left] == 1) {
                left += 2;

            // The current position is empty, but the next position
            // already has a flower. Skip to the next possible position.
            } else if (left + 1 < flowerbed.length && flowerbed[left + 1] == 1) {
                left += 3;

            // The current position is empty, and the next position
            // is either empty or outside the flowerbed.
            // Therefore, we can greedily plant a flower here.
            } else {
                n--;
                left += 2;
            }

            // Stop early once we have planted enough flowers.
            if (n <= 0) {
                return true;
            }
        }

        // The entire flowerbed was checked, but more flowers are needed.
        return false;
    }
}
```

---

## Why Greedy Works

We scan the flowerbed from left to right.

Whenever the current position is empty and the next position is also empty, we immediately plant a flower at the current position.

This is safe because planting at the earliest available position does not reduce the maximum number of flowers that can be planted later.

For example:

```text
[0,0,0]
 ^
 left
```

We can safely plant at `left`:

```text
[1,0,0]
```

After planting, the next position cannot be used, so we can move directly to the next possible candidate.

---

## Pattern Analysis

### Case 1: Current position already has a flower

```text
[1, ?]
 ^
 left
```

The current position cannot be used, and the next position cannot be used because flowers cannot be adjacent.

Therefore:

```java
left += 2;
```

---

### Case 2: Current position is empty, but the next position has a flower

```text
[0,1]
 ^
 left
```

The current position cannot be used because the next position already contains a flower.

We can safely skip to the next possible candidate:

```java
left += 3;
```

---

### Case 3: Current position and next position are both empty

```text
[0,0]
 ^
 left
```

We can greedily plant at the current position:

```java
n--;
left += 2;
```

After planting, the next position cannot be used.

---

### Case 4: Current position is the last position

```text
[...,0]
      ^
     left
```

In this case:

```java
left + 1 < flowerbed.length
```

is `false`.

Therefore, the code naturally falls into the final `else` branch:

```java
n--;
left += 2;
```

Since there is no position to the right, an empty last position can be used.

This means the tail boundary does not require a separate condition.

---

## Head and Tail Boundaries

### Head

We start with:

```java
int left = 0;
```

If the flowerbed starts with:

```text
[0,0,...]
 ^
 left
```

the first position can be planted because there is no position to its left.

Therefore, no special head logic is required.

### Tail

When `left` reaches the last position:

```text
[...,0]
      ^
     left
```

there is no next position.

The condition:

```java
left + 1 < flowerbed.length
```

becomes `false`, so the algorithm naturally falls into the planting case.

Therefore, no separate tail-processing step is required.

---

## Why Not Check Every Position?

A more straightforward solution checks the left and right neighbors for every index.

This solution is also `O(n)` and is arguably easier to understand.

However, our approach uses information from the current pattern to skip positions whose state is already known.

For example:

```text
[0,1,...]
 ^
 left
```

Once we know that the next position contains a flower, we already know that the current position cannot be used.

There is no reason to check those positions again.

The difference is not Big-O complexity:

```text
Both approaches: O(n)
```

The difference is the number of operations and array accesses.

---

## Trade-off

| Neighbor Checking                 | Pattern Skipping                         |
| --------------------------------- | ---------------------------------------- |
| Check each position independently | Skip positions based on known patterns   |
| Easier to understand              | More optimized traversal                 |
| Easier to verify                  | Requires understanding of skip distances |
| More array checks                 | Fewer array checks                       |
| `O(n)` time                       | `O(n)` time                              |
| `O(1)` space                      | `O(1)` space                             |

The neighbor-checking solution is simpler and has lower implementation risk.

The pattern-skipping solution is slightly more sophisticated, but it avoids redundant checks while keeping the same asymptotic complexity.

---

## Complexity

```text
Time:  O(n)
Space: O(1)
```

Each iteration advances `left` by at least 2 positions, and sometimes by 3 positions.

Therefore, the number of iterations is bounded by `O(n)`.

No additional data structure is used, so the auxiliary space is `O(1)`.

---

## Takeaway

The key idea is to recognize that we do not always need to inspect every position.

```text
[1, ?]  → skip 2

[0, 1]  → skip 3

[0, 0]  → plant → skip 2
```

The algorithm is still `O(n)`, but it reduces unnecessary checks by using information that has already been established.

The important distinction is:

> The optimization does not improve the Big-O complexity. It improves the constant factor by avoiding redundant checks.

The overall strategy is:

```text
Scan left to right
       ↓
Identify the current pattern
       ↓
Plant if possible
       ↓
Skip positions that cannot be candidates
       ↓
Repeat
```

The key insight is:

> Once the current pattern tells us that certain positions cannot be used, we should skip them instead of checking them again.
