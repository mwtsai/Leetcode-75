# 605 - Can Place Flowers

## Problem

You have a long flowerbed represented by an integer array `flowerbed`, where:

* `0` means the position is empty.
* `1` means the position already contains a flower.

Flowers cannot be planted in adjacent positions.

Given an integer `n`, return `true` if `n` new flowers can be planted without violating the no-adjacent-flowers rule.

### Example

```text
flowerbed = [1,0,0,0,1]
n = 1

Output: true
```

The flower can be planted at index `2`.

---

## Solution

### Approach: Greedy with Pattern Skipping

Instead of checking every position independently, we can use the current position and the next position to determine how far we can safely skip.

The key idea is that once we know the current pattern, some positions can no longer be valid candidates.

There are three important patterns:

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

### Code

```java
class Solution {
    public boolean canPlaceFlowers(int[] flowerbed, int n) {

        // If no flowers need to be planted, we are already done.
        if (n == 0) {
            return true;
        }

        // left represents the current position we are checking.
        int left = 0;

        while (left < flowerbed.length) {

            // If there is already a flower at left,
            // skip the current position and the next position.
            if (flowerbed[left] == 1) {
                left += 2;

            // left is the last position.
            // Since it is empty, we can plant a flower here.
            } else if (left + 1 == flowerbed.length) {
                n--;
                left += 2;

            // left is empty, but the next position has a flower.
            // Skip both positions and move to the next possible position.
            } else if (flowerbed[left + 1] == 1) {
                left += 3;

            // Both left and the next position are empty.
            // Greedily plant a flower at left.
            } else {
                n--;
                left += 2;
            }

            // Check if we have planted enough flowers.
            if (n == 0) {
                return true;
            }
        }

        // We checked the entire flowerbed but still need more flowers.
        return false;
    }
}
```

---

## Why Greedy Works

We scan the flowerbed from left to right.

Whenever the current position is empty and the next position is also empty, we immediately plant a flower at the current position.

This is safe because planting at the earliest available position does not reduce the number of flowers that can be planted later.

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

After planting, the next possible position is two indexes later.

```text
[1,0,0]
     ^
     next candidate
```

Therefore, we can skip two positions after planting.

---

## Pattern Analysis

### Case 1: Current position already has a flower

```text
[1, ?]
 ^
 left
```

`left` cannot be used, and the next position cannot be used because flowers cannot be adjacent.

Therefore:

```java
left += 2;
```

---

### Case 2: Current position is the last position

```text
[..., 0]
      ^
     left
```

There is no right neighbor.

Since the current position is empty, we can safely plant here.

```java
n--;
left += 2;
```

---

### Case 3: Current position is empty, but the next position has a flower

```text
[0,1]
 ^
 left
```

Neither position can be used.

Instead of checking the next positions one by one, we can skip directly to the next possible candidate.

```java
left += 3;
```

---

### Case 4: Current position and next position are both empty

```text
[0,0]
 ^
 left
```

We can greedily plant at `left`.

```java
n--;
left += 2;
```

This avoids checking positions that are already known to be unavailable.

---

## Why Not Check Every Position?

A more straightforward solution checks the left and right neighbors for every index.

For example:

```java
if (flowerbed[i] == 0
        && (i == 0 || flowerbed[i - 1] == 0)
        && (i == flowerbed.length - 1 || flowerbed[i + 1] == 0)) {
    // plant a flower
}
```

This solution is also `O(n)` and is arguably easier to understand.

However, this approach may repeatedly inspect positions whose state can already be determined from the previous pattern.

Our approach instead skips those positions directly.

The difference is not Big-O complexity:

```text
Both approaches: O(n)
```

The difference is in the number of operations and array accesses.

This becomes more meaningful when checking a position is expensive.

For example, if a real-world validation involves multiple business rules or expensive checks, avoiding unnecessary validations can reduce the actual runtime.

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

The main trade-off is **readability vs. unnecessary work**.

The neighbor-checking solution is simpler and has lower implementation risk.

The pattern-skipping solution is slightly more sophisticated, but it avoids redundant checks while keeping the same asymptotic complexity.

---

## Complexity

```text
Time:  O(n)
Space: O(1)
```

Each iteration advances `left` by at least 2 positions, and sometimes by 3 positions.

Therefore, the number of iterations is still bounded by `O(n)`.

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
