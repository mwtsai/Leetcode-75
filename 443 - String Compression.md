# 443 - String Compression

## Problem

Given an array of characters `chars`, compress it using the following algorithm:

* Begin with an empty string `s`.
* For each group of consecutive repeating characters in `chars`:

  * Append the character to `s`.
  * If the group length is greater than `1`, append the group length.

Return the new length of the compressed array.

**Important:** The compressed result must be stored in the input array `chars`. Do not allocate another array to store the result.

## Example

```text
Input:  chars = ["a","a","b","b","c","c","c"]
Output: 6

Compressed array: ["a","2","b","2","c","3"]
```

## Solution

### Approach: Two Pointers + In-place Write

Use two pointers:

* `read`: Reads the original characters and identifies each group.
* `write`: Writes the compressed result back into the original array.

For each group:

1. Read the current character.
2. Count consecutive occurrences of that character.
3. Write the character once.
4. If the count is greater than `1`, write each digit of the count from left to right.

### Java Code

```java
class Solution {
    public int compress(char[] chars) {
        int read = 0;
        int write = 0;

        while (read < chars.length) {
            // Step 1: Read the current character
            char current = chars[read];
            int start = read;

            // Find the end of the current group
            while (read < chars.length && chars[read] == current) {
                read++;
            }

            // Calculate the number of occurrences
            int count = read - start;

            // Step 2: Write the character once
            chars[write++] = current;

            // Write the count only if it is greater than 1
            if (count > 1) {
                // Find the highest decimal place
                int digit = 1;
                while (count / digit >= 10) {
                    digit *= 10;
                }

                // Write each digit from left to right
                while (digit > 0) {
                    chars[write++] = (char) ('0' + count / digit);
                    count %= digit;
                    digit /= 10;
                }
            }
        }

        // Return the length of the compressed array
        return write;
    }
}
```

## How It Works

For `chars = ["a","a","a","b","b","c"]`:

### Step 1: Read and Count

```text
Group "aaa" -> count = 3
Group "bb"  -> count = 2
Group "c"   -> count = 1
```

### Step 2: Write the Compressed Result

```text
"aaa" -> "a3"
"bb"  -> "b2"
"c"   -> "c"
```

Final result:

```text
["a","3","b","2","c"]
```

Return `5`.

## Key Details

### 1. Why use `read - start`?

`start` records the first index of the current group.

After the `read` pointer moves past the group, the group length is:

```java
int count = read - start;
```

### 2. Why write the count only when `count > 1`?

A character that appears only once should be written without a count.

```text
"a"  -> "a"
"aa" -> "a2"
```

### 3. Why use `digit` instead of `% 10` alone?

Using `% 10` extracts digits from right to left. For example, `123` produces `3`, `2`, `1`.

Instead, `digit` identifies the highest decimal place, allowing digits to be written in the correct order:

```text
count = 123
digit = 100

Write '1' -> count becomes 23
Write '2' -> count becomes 3
Write '3' -> count becomes 0
```

### 4. Why return `write` instead of creating a new array?

The compressed result is already stored in `chars[0..write-1]`.

Only the returned length matters. The unused portion of the original array can be ignored.

## Complexity

* **Time:** `O(n)` — Each character is read once, and the compressed result is written in place.
* **Auxiliary Space:** `O(1)` — Only a fixed number of variables are used; no additional array or count string is created.

## Takeaway

The key idea is to separate the solution into two phases:

```text
Read
  ↓
Find a group of identical characters
  ↓
Count the group length
  ↓
Write the character once
  ↓
Write the count if count > 1
```

The `read` pointer identifies groups, while the `write` pointer builds the compressed result in place.

This approach satisfies the in-place requirement and uses constant auxiliary space.
