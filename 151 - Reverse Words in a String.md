# 151 - Reverse Words in a String

## Problem

Given a string `s`, reverse the order of the words.

A word is defined as a sequence of non-space characters.

The returned string should:

* Have the words in reverse order.
* Contain only a single space between words.
* Have no leading or trailing spaces.

### Example

```text
Input:
s = "  the sky is blue  "

Output:
"blue is sky the"
```

The words are:

```text
the → sky → is → blue
```

After reversing their order:

```text
blue → is → sky → the
```

---

# Solution 1

## Approach: Two Pointer

The key idea is to scan the string **from right to left**.

We use two pointers:

* `right` finds the end of the current word.
* `left` scans left to find the beginning of the word.

For example:

```text
"the sky is blue"
             ^
           right
```

`right` first skips any trailing spaces.

Then `left` moves left until it reaches a space:

```text
"the sky is blue"
          ^   ^
        left right
```

The word is therefore:

```text
s[left + 1 ... right]
```

We append this word to the result, then move `right` to the position before the current word:

```text
right = left - 1
```

This allows us to process the next word from right to left.

---

## Code

```java
class Solution {
    public String reverseWords(String s) {
        int right = s.length() - 1;

        StringBuilder result = new StringBuilder();

        while (right >= 0) {

            // Skip trailing or consecutive spaces.
            // After this loop, right points to the last character of a word.
            while (right >= 0 && s.charAt(right) == ' ') {
                right--;
            }

            // No more words left.
            if (right < 0) {
                break;
            }

            // right is guaranteed to be a non-space character,
            // so we can start from the character immediately before it.
            int left = right - 1;

            // Move left until we reach the beginning of the word.
            while (left >= 0 && s.charAt(left) != ' ') {
                left--;
            }

            // Append the current word.
            // The range is [left + 1, right + 1).
            result.append(s, left + 1, right + 1);

            // Add exactly one space between words.
            result.append(' ');

            // Move to the position before the current word.
            right = left - 1;
        }

        // Remove the extra space added after the last word.
        if (result.length() > 0) {
            result.deleteCharAt(result.length() - 1);
        }

        return result.toString();
    }
}
```

---

## Why `left = right - 1`?

After this loop:

```java
while (right >= 0 && s.charAt(right) == ' ') {
    right--;
}
```

we already know:

```text
right < 0
```

or

```text
s.charAt(right) != ' '
```

Therefore, `right` is guaranteed to be inside a word.

We do not need to check `right` again.

So we can start `left` directly from:

```java
int left = right - 1;
```

For example:

```text
"hello"
     ^
   right

"hello"
    ^
   left
```

`left` then continues moving left until it reaches a space or the beginning of the string.

This is a small optimization based on the invariant established by the previous loop.

---

## Why This Works

Consider:

```text
"  the sky is blue  "
```

We start from the right:

```text
"  the sky is blue  "
                   ^
                 right
```

Skip spaces:

```text
"  the sky is blue  "
                ^
              right
```

Find the beginning of `"blue"`:

```text
"  the sky is blue  "
             ^    ^
           left  right
```

Append:

```text
blue
```

Then move:

```java
right = left - 1;
```

Now we process `"is"`:

```text
"  the sky is blue  "
          ^  ^
        left right
```

Eventually:

```text
blue is sky the
```

---

## Complexity

```text
Time:  O(n)
Space: O(n)
```

### Time

Each character is visited a constant number of times.

Therefore:

```text
O(n)
```

### Space

The result itself requires `O(n)` space.

The algorithm does not use an additional Stack or List, but the output `StringBuilder` still requires:

```text
O(n)
```

---

# Solution 2

## Approach: Reverse All

Another classic solution is to use the following observation:

Instead of directly reversing the **order of words**, we can perform two types of reversal.

### Step 1

Remove unnecessary spaces.

```text
"  the sky is blue  "
        ↓
"the sky is blue"
```

### Step 2

Reverse the entire string.

```text
"the sky is blue"
        ↓
"eulb si yks eht"
```

### Step 3

Reverse every individual word.

```text
"eulb si yks eht"
        ↓
"blue is sky the"
```

The two reversals cancel the reversal of the characters inside each word while keeping the words in reverse order.

---

## Code

```java
class Solution {
    public String reverseWords(String s) {
        char[] chars = s.toCharArray();

        // 1. Remove leading, trailing, and consecutive spaces.
        int n = removeExtraSpaces(chars);

        // 2. Reverse the entire string.
        reverse(chars, 0, n - 1);

        // 3. Reverse each individual word.
        int start = 0;

        for (int i = 0; i <= n; i++) {

            // i == n means we reached the end of the string.
            if (i == n || chars[i] == ' ') {

                // Reverse the current word.
                reverse(chars, start, i - 1);

                // Start the next word after the space.
                start = i + 1;
            }
        }

        return new String(chars, 0, n);
    }

    private int removeExtraSpaces(char[] chars) {
        int slow = 0;
        int fast = 0;

        while (fast < chars.length) {

            // Skip leading or consecutive spaces.
            while (fast < chars.length && chars[fast] == ' ') {
                fast++;
            }

            // No more characters.
            if (fast == chars.length) {
                break;
            }

            // Add exactly one space between words.
            if (slow > 0) {
                chars[slow++] = ' ';
            }

            // Copy the current word.
            while (fast < chars.length && chars[fast] != ' ') {
                chars[slow++] = chars[fast++];
            }
        }

        // slow represents the length of the cleaned string.
        return slow;
    }

    private void reverse(char[] chars, int left, int right) {

        // Reverse characters using two pointers.
        while (left < right) {
            char temp = chars[left];
            chars[left] = chars[right];
            chars[right] = temp;

            left++;
            right--;
        }
    }
}
```

---

## Example

Suppose:

```text
s = "  the sky is blue  "
```

### Step 1: Remove Extra Spaces

```text
"  the sky is blue  "
          ↓
"the sky is blue"
```

---

### Step 2: Reverse the Entire String

```text
"the sky is blue"
        ↓
"eulb si yks eht"
```

Notice that both:

* the word order
* the characters inside each word

are reversed.

---

### Step 3: Reverse Each Word

Reverse `"eulb"`:

```text
eulb → blue
```

Reverse `"si"`:

```text
si → is
```

Reverse `"yks"`:

```text
yks → sky
```

Reverse `"eht"`:

```text
eht → the
```

Final result:

```text
"blue is sky the"
```

---

# Why Does Reverse All Work?

Consider the original:

```text
A B C
```

where `A`, `B`, and `C` are words.

After reversing the entire string:

```text
reverse(C) reverse(B) reverse(A)
```

Then reverse each word individually:

```text
C B A
```

Therefore, we successfully reverse the **order of the words** while restoring the characters inside each word.

The important idea is:

```text
Reverse entire string
        ↓
Word order is reversed
        ↓
Characters inside every word are also reversed
        ↓
Reverse every word
        ↓
Characters inside words are restored
```

---

# Complexity

```text
Time:  O(n)
Space: O(n) in Java
```

The algorithm itself only needs constant auxiliary space after obtaining the mutable character array.

However, Java `String` is immutable, so we need:

```java
char[] chars = s.toCharArray();
```

and finally:

```java
new String(chars, 0, n);
```

Therefore, in practical Java implementation, the character array requires `O(n)` memory.

If the problem allows an already mutable character array as input, the algorithm can be considered:

```text
Time:  O(n)
Auxiliary Space: O(1)
```

---

# Two Pointer vs. Reverse All

|                      | Two Pointer                   | Reverse All                       |
| -------------------- | ----------------------------- | --------------------------------- |
| Main idea            | Scan words from right to left | Reverse entire string + each word |
| Direction            | Right → Left                  | Multiple passes                   |
| Extra data structure | `StringBuilder`               | `char[]`                          |
| Time                 | `O(n)`                        | `O(n)`                            |
| Space                | `O(n)` output                 | `O(n)` in Java                    |
| Implementation       | Simple                        | Slightly more complicated         |
| In-place potential   | No                            | Yes                               |
| Interview follow-up  | Good                          | Excellent                         |

---

# Which One Should We Use?

For a normal LeetCode solution, I would prefer **Two Pointer** first.

The core logic is very direct:

```text
Start from the right
      ↓
Skip spaces
      ↓
Find the current word
      ↓
Append it
      ↓
Move to the previous word
```

It is easy to explain and easy to verify.

For an interview, however, I would also know the **Reverse All** solution because it handles the common follow-up:

> Can you solve it in-place?

In that case, the Reverse All approach is particularly useful because the main transformation can be performed directly on a mutable character array.

---

# Takeaway

There are two important patterns in this problem.

### Two Pointer

```text
right
  ↓
"the sky is blue"

Skip spaces
     ↓
Find word
     ↓
Append word
     ↓
Move right backward
```

The key idea is:

> Scan from right to left and use two pointers to identify each word.

### Reverse All

```text
"the sky is blue"
        ↓
"eulb si yks eht"
        ↓
"blue is sky the"
```

The key idea is:

> Reverse the entire string to reverse the word order, then reverse each word to restore its characters.

Both solutions are:

```text
Time: O(n)
```

The main difference is the **way the reversed word order is obtained**:

```text
Two Pointer → directly read words from right to left

Reverse All → reverse globally, then fix each word
```
