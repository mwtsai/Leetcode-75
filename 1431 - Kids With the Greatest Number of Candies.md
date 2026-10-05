# 1431 - Kids With the Greatest Number of Candies

## Problem

There are `n` kids with candies.

Given an integer array `candies`, where `candies[i]` represents the number of candies the `ith` kid has, and an integer `extraCandies`.

Return a boolean array where `result[i]` is `true` if, after giving all `extraCandies` to the `ith` kid, they will have the greatest number of candies among all the kids.

Multiple kids can have the greatest number of candies.

Example:

```text
candies = [2, 3, 5, 1, 3]
extraCandies = 3

Answer = [true, true, true, false, true]
```

---

## Solution

### Approach: Find Maximum + Threshold

First, find the current maximum number of candies.

For each kid, we need to check:

```text
candies[i] + extraCandies >= max
```

Instead of adding `extraCandies` for every kid, we can rearrange the condition:

```text
candies[i] >= max - extraCandies
```

Define:

```text
threshold = max - extraCandies
```

Then a kid can have the greatest number of candies if:

```text
candies[i] >= threshold
```

### Java

```java
class Solution {
    public List<Boolean> kidsWithCandies(int[] candies, int extraCandies) {

        // Find the maximum number of candies
        int max = candies[0];

        for (int candy : candies) {
            max = Math.max(max, candy);
        }

        // A kid needs at least this many candies
        // to become one of the kids with the greatest number.
        int threshold = max - extraCandies;

        List<Boolean> result = new ArrayList<>();

        for (int candy : candies) {
            result.add(candy >= threshold);
        }

        return result;
    }
}
```

The key transformation is:

```text
candies[i] + extraCandies >= max

            ↓

candies[i] >= max - extraCandies
```

This allows us to calculate the threshold once and then simply compare each kid's candies against it.

---

## Complexity

```text
Time:  O(N)
Space: O(1)
```

The `result` list itself requires `O(N)` space, but the algorithm only uses `O(1)` additional space.

We need to scan the array once to find the maximum and once more to build the result.

---

## Takeaway

This problem is a simple example of the **Find Maximum + Compare** pattern.

```text
Find max
   ↓
Calculate threshold = max - extraCandies
   ↓
For each candy:
   candy >= threshold ?
   ↓
true / false
```

The important insight is that we do not need to simulate giving the extra candies to every kid.

Instead, transform:

```text
candies[i] + extraCandies >= max
```

into:

```text
candies[i] >= max - extraCandies
```

This keeps the solution simple and efficient.

### Key Pattern

> If every element needs to be compared with the maximum value, find the maximum first and reuse it instead of recalculating it for every element.

This gives us an `O(N)` solution instead of an `O(N²)` brute-force approach.
