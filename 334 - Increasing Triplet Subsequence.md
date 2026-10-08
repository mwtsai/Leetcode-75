# 334 - Increasing Triplet Subsequence

## Approach

Use Greedy with two variables:

* `first`: the smallest candidate for the first number `a`
* `second`: the smallest candidate for the second number `b`

The goal is:

```text
a < b < c
```

Keep both `first` and `second` as small as possible, because smaller values make it easier to find a larger number later.

### Three stages

```text
Find a
  ↓
Find a < b
  ↓
Find a < b < c
```

## Code

```java
class Solution {
    public boolean increasingTriplet(int[] nums) {
        int first = Integer.MAX_VALUE;
        int second = Integer.MAX_VALUE;

        for (int num : nums) {

            // Find a smaller candidate for 'a'
            if (num <= first) {
                first = num;

            // We have first < num, so we found a candidate for 'b'
            } else if (num <= second) {
                second = num;

            // first < second < num
            // Increasing triplet found
            } else {
                return true;
            }
        }

        return false;
    }
}
```

## Proof

Maintain these invariants:

1. `first` is always the smallest value seen so far.
2. When `second` exists, `first < second`.
3. When updating `first`, the new `first` is smaller, so `first < second` is still valid.
4. When updating `second`, `num > first`, so the new `second` is still greater than `first`.

Therefore, when:

```text
num > second
```

we have:

```text
first < second < num
```

so an increasing triplet exists.

## Complexity

```text
Time:  O(n)
Space: O(1)
```

## Key Takeaway

```text
first  → smallest possible a
second → smallest possible b
num    → if num > second, triplet found
```

The greedy idea is:

> **Keep `first` and `second` as small as possible.**
