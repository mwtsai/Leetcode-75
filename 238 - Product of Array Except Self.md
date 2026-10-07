# 238 - Product of Array Except Self

## Problem

Given an integer array `nums`, return an array `answer` such that `answer[i]` is equal to the product of all the elements of `nums` except `nums[i]`.

The product of any prefix or suffix of `nums` is guaranteed to fit in a 32-bit integer.

**Constraints:**

* `O(n)` time
* Without using the division operation

---

## Approach

For each index `i`, the answer can be separated into two parts:

```text
answer[i] = product of elements to the left
          × product of elements to the right
```

For example:

```text
nums = [1, 2, 3, 4]

index 0:
left  = 1
right = 2 × 3 × 4
answer = 24

index 1:
left  = 1
right = 3 × 4
answer = 12

index 2:
left  = 1 × 2
right = 4
answer = 8

index 3:
left  = 1 × 2 × 3
right = 1
answer = 6
```

So we can use two passes:

1. Traverse from left to right and store the **prefix product** in `result`.
2. Traverse from right to left and multiply `result[i]` by the **suffix product**.

We don't need separate arrays for prefix and suffix.

---

## Prefix

First, store the product of all elements **before** index `i`.

```java
int prefix = 1;

for (int i = 0; i < nums.length; i++) {
    result[i] = prefix;
    prefix *= nums[i];
}
```

For:

```text
nums = [1, 2, 3, 4]
```

After the first pass:

```text
result = [1, 1, 2, 6]
```

Each `result[i]` represents:

```text
product of all elements to the left of i
```

---

## Suffix

Then traverse from right to left.

Maintain a `suffix` variable containing the product of all elements **after** index `i`.

```java
int suffix = 1;

for (int i = nums.length - 1; i >= 0; i--) {
    result[i] *= suffix;
    suffix *= nums[i];
}
```

For:

```text
result = [1, 1, 2, 6]
```

The suffix products are incorporated from right to left:

```text
i = 3:
result[3] = 6 × 1 = 6

i = 2:
result[2] = 2 × 4 = 8

i = 1:
result[1] = 1 × 12 = 12

i = 0:
result[0] = 1 × 24 = 24
```

Final result:

```text
[24, 12, 8, 6]
```

---

## Code

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int[] result = new int[nums.length];

        // Prefix
        int prefix = 1;
        for (int i = 0; i < nums.length; i++) {
            result[i] = prefix;
            prefix *= nums[i];
        }

        // Suffix
        int suffix = 1;
        for (int i = nums.length - 1; i >= 0; i--) {
            result[i] *= suffix;
            suffix *= nums[i];
        }

        return result;
    }
}
```

---

## Why It Works

For every index `i`:

```text
result[i]
= prefix product × suffix product
```

The prefix pass gives us:

```text
product of nums[0 ... i-1]
```

The suffix pass gives us:

```text
product of nums[i+1 ... n-1]
```

Therefore:

```text
result[i]
= nums[0] × ... × nums[i-1]
  ×
  nums[i+1] × ... × nums[n-1]
```

which is exactly the product of all elements except `nums[i]`.

---

## Edge Case: Zero

No special handling for `0` is required.

For example:

```text
nums = [1, 2, 0, 4]
```

The algorithm naturally produces:

```text
[0, 0, 8, 0]
```

If there are two or more zeros, every result will naturally become `0`.

This is one advantage of the prefix/suffix approach.

---

## Complexity

**Time:** `O(n)`

We traverse the array twice:

```text
O(n) + O(n) = O(n)
```

**Space:** `O(1)` extra space.

The `result` array is the output array, so it is not counted as extra space.

---

## Takeaway

The key idea is:

```text
answer[i]
= prefix[i] × suffix[i]
```

Instead of creating separate prefix and suffix arrays, we can:

1. Store prefix products directly in `result`.
2. Traverse from right to left and multiply by a running `suffix`.

This gives:

```text
Time:  O(n)
Space: O(1) extra
```

and does not require division.
