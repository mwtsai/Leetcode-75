# 345 - Reverse Vowels of a String

## Problem

Given a string `s`, reverse only all the vowels in the string and return it.

The vowels are:

```text
a, e, i, o, u
A, E, I, O, U
```

### Example

```text
Input:  "hello"
Output: "holle"
```

Only the vowels are reversed:

```text
h e l l o
  ↓     ↓
h o l l e
```

---

## Approach

### Two Pointers

Use two pointers to find vowels from both ends.

1. `left` starts from the beginning.
2. `right` starts from the end.
3. Move `left` until it finds a vowel.
4. Move `right` until it finds a vowel.
5. Swap the two vowels.
6. Move both pointers inward.

```text
left → vowel       vowel ← right
          ↓
         swap
          ↓
      move inward
```

The key idea is to **skip non-vowels** because their positions should not change.

---

## Solution

```java
class Solution {
    public String reverseVowels(String s) {
        char[] chars = s.toCharArray();

        int left = 0;
        int right = chars.length - 1;

        while (left < right) {

            // Find the next vowel from the left.
            while (left < right && !isVowel(chars[left])) {
                left++;
            }

            // Find the next vowel from the right.
            while (left < right && !isVowel(chars[right])) {
                right--;
            }

            // Swap the vowels.
            if (left < right) {
                char temp = chars[left];
                chars[left] = chars[right];
                chars[right] = temp;

                left++;
                right--;
            }
        }

        return new String(chars);
    }

    private boolean isVowel(char c) {
        return "aeiouAEIOU".indexOf(c) != -1;
    }
}
```

---

## Alternative: Stack

Another approach is to use a **Stack**.

### Idea

1. Scan the string and push every vowel into a stack.
2. Scan the string again.
3. When a vowel is found, replace it with `stack.pop()`.

```text
Original:   h e l l o

Stack:      e → o

Scan again:
h e l l o
  ↓     ↓
  o     e

Result:     h o l l e
```

### Complexity

* Time: `O(n)`
* Space: `O(n)`

The Stack solution is straightforward, but it requires an additional data structure.

---

## Comparison

| Approach           |   Time |   Space | Pros                                    | Cons                              |
| ------------------ | -----: | ------: | --------------------------------------- | --------------------------------- |
| **Two Pointers**   | `O(n)` | `O(n)*` | Simple, direct, no extra data structure | Need to handle pointer boundaries |
| **Stack**          | `O(n)` |  `O(n)` | Easy to understand                      | Requires extra Stack              |
| **List + Reverse** | `O(n)` |  `O(n)` | Simple implementation                   | Requires extra List               |

* `O(n)` space comes from the `char[]` used because Java `String` is immutable. The **Two Pointers algorithm itself uses `O(1)` extra space**.

### Why prefer Two Pointers?

For this problem, **Two Pointers is the most natural approach** because we need to reverse elements from both ends.

It avoids storing all vowels separately and processes the string in one pass.

---

## Complexity

### Two Pointers

* **Time:** `O(n)`
* **Space:** `O(n)` for the `char[]`
* **Auxiliary Space:** `O(1)`

### Stack

* **Time:** `O(n)`
* **Space:** `O(n)`

---

## Key Takeaway

**Two Pointers + Skip non-vowels + Swap**

```text
find vowel
    ↓
find vowel
    ↓
  swap
    ↓
move inward
```

When the problem asks you to reverse or swap only specific elements, consider **Two Pointers** before reaching for an additional data structure.
