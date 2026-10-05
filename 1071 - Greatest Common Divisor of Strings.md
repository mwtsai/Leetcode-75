# 1071 - Greatest Common Divisor of Strings

## Problem

Given two strings `str1` and `str2`, return the largest string `x` such that `x` divides both `str1` and `str2`.

A string `t` divides `s` if `s` can be constructed by concatenating `t` one or more times.

Example:

```text
str1 = "ABCABC"
str2 = "ABC"

Answer = "ABC"
```

---

## Solution

### Approach 1: String Euclidean Algorithm

Apply the Euclidean Algorithm directly to strings.

If the shorter string is a prefix of the longer string, remove it and recursively solve the remaining strings.

```java
class Solution {
    public String gcdOfStrings(String str1, String str2) {
        int i = 0;

        // Check whether the two strings have the same prefix
        while (i < str1.length() && i < str2.length()) {
            if (str1.charAt(i) != str2.charAt(i)) {
                return "";
            }
            i++;
        }

        // If both strings have the same length, we found the GCD
        if (str1.length() == str2.length()) {
            return str1;
        }

        // Remove the shorter string from the longer string
        if (str1.length() > str2.length()) {
            return gcdOfStrings(
                str1.substring(str2.length()),
                str2
            );
        }

        return gcdOfStrings(
            str1,
            str2.substring(str1.length())
        );
    }
}
```

This is essentially applying:

```text
gcd(a, b) = gcd(a - b, b)
```

to strings.

---

### Approach 2: Concatenation + Integer GCD

First check whether the two strings can be constructed from the same base string.

If a common divisor exists:

```java
(str1 + str2).equals(str2 + str1)
```

must be true.

Then use the GCD of the two lengths to find the length of the answer.

```java
class Solution {
    public String gcdOfStrings(String str1, String str2) {

        // If the concatenation order changes the result,
        // the two strings cannot have a common divisor.
        if (!(str1 + str2).equals(str2 + str1)) {
            return "";
        }

        // The length of the largest common divisor
        // is the GCD of the two string lengths.
        int len = gcd(str1.length(), str2.length());

        // The answer is the prefix with the GCD length.
        return str1.substring(0, len);
    }

    private int gcd(int a, int b) {
        // Standard Euclidean Algorithm
        while (b != 0) {
            int temp = a % b;
            a = b;
            b = temp;
        }

        return a;
    }
}
```

### Trade-off

|             | String Euclidean              | Concatenation + GCD              |
| ----------- | ----------------------------- | -------------------------------- |
| Idea        | Directly apply GCD to strings | Reduce String GCD to Integer GCD |
| Readability | More intuitive                | More concise                     |
| Performance | More substring operations     | More efficient                   |
| Difficulty  | Easier to derive              | More tricky to discover          |

---

## Complexity

### String Euclidean Algorithm

```text
Time:  O((N + M) × number of reduction steps)
Space: O(N + M)
```

Due to repeated `substring()` operations.

### Concatenation + Integer GCD

```text
Time:  O(N + M)
Space: O(N + M)
```

The extra space mainly comes from creating the concatenated strings.

---

## Takeaway

The first approach is a good example of applying the **Euclidean Algorithm directly to strings**.

The second approach is the more standard and optimized solution:

```text
String GCD
    ↓
Check str1 + str2 == str2 + str1
    ↓
GCD(str1.length(), str2.length())
    ↓
Take the prefix
```

The key insight is:

> If two strings have a common divisor, concatenating them in either order must produce the same string.
