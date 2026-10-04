# LeetCode 1768 - Merge Strings Alternately

## 📌 Problem Overview
Given two strings `word1` and `word2`, merge the strings by adding letters in alternating order, starting with `word1`. If a string is longer than the other, append the additional letters onto the end of the merged string.

---

## 💡 Solution Approaches & Analysis

We compare two primary architectural approaches to solve this problem:

1. **Two-Stage Approach (Separation of Concerns)**: Splits the task into two explicit steps—merging the common prefix alternatingly, and appending the remaining suffix.
2. **Single Loop Pointer Approach**: Uses a single `while` loop with bounds checks to merge in one pass.

### Pros & Cons Comparison

| Approach | Pros | Cons |
| :--- | :--- | :--- |
| **Two-Stage Approach** *(Recommended for Enterprise)* | • **Extensibility**: Step 2 provides a clean entry point for custom business logic (e.g., logging mismatches, appending UI tags/red flags).<br>• **Compiler Optimization**: The uniform loop in Step 1 contains no `if` branches, enabling potential loop unrolling and SIMD vectorization.<br>• **Performance on Asymmetric Input**: Highly efficient when one string is significantly longer than the other. | • Slightly higher line count compared to a concise single loop.<br>• Requires developers to handle range indices explicitly. |
| **Single Loop Approach** *(Common LeetCode Answer)* | • **Concise Code**: Unified logic wrapped in a single `while` loop.<br>• **Intuitive Idea**: Easy to implement for simple algorithm challenges without multi-stage thinking. | • **Branch Overhead**: Evaluates condition checks (`if (i < len)`) on every iteration.<br>• **Lower Extensibility**: Squeezing edge-case business logic into the loop litters it with state flags. |

---

## 🚘 Real-World Analogies & System Architecture

### 1. Two-Stage Approach: Bounded Resource Matching (Cars & Parking Slots)
- **Scenario**: Merging incoming cars (`word1`) with available parking slots (`word2`).
- **Step 1**: Match cars to slots 1:1 and flag them with **Green Lights** (Successful match / Normal processing).
- **Step 2**: Handle remaining unmapped items cleanly. Excess cars get **Red Lights** (Overflow / Queue warning), while excess slots get **Yellow Lights** (Idle capacity).
- **Takeaway**: Ideal for processing bounded batches where matched items and tail ends require distinct state flags or business rules.

### 2. Single-Pass Approach: Dynamic Multi-Lane Queue (Continuous Traffic Ingestion)
- **Scenario**: Ingesting continuous streams of data or merging dynamically increasing lanes (e.g., adding `Line C`, `Line D`).
- **Mechanism**: A unified `while` loop behaves like a Round-Robin multiplexer (`while (hasMoreData())`).
- **Takeaway**: Superior when expanding to $N$-way streaming inputs where sequence bounds are variable or determined at runtime.

---

## ☕ Implementation in Java

### Enterprise-Grade Two-Stage Solution

This solution incorporates critical performance and engineering best practices:
- **Pre-allocated Capacity**: `new StringBuilder(len1 + len2)` eliminates array reallocations ($O(1)$ allocations).
- **Zero Temporary String Allocation**: Uses `sb.append(str, start, end)` instead of `substring()` to avoid creating intermediate Garbage Collection (GC) objects.
- **Defensive Programming**: Handles `null` inputs gracefully.

```java
class Solution {
    public String mergeAlternately(String word1, String word2) {
        // Defensive check
        if (word1 == null || word2 == null) {
            return "";
        }

        int len1 = word1.length();
        int len2 = word2.length();
        int minLen = Math.min(len1, len2);

        // Pre-allocate buffer capacity to prevent array reallocation overhead
        StringBuilder sb = new StringBuilder(len1 + len2);

        // STEP 1: Alternately merge characters up to the common minimum length
        for (int i = 0; i < minLen; i++) {
            sb.append(word1.charAt(i));
            sb.append(word2.charAt(i));
        }

        // STEP 2: Append the remaining suffix without creating temporary String objects
        if (len1 > minLen) {
            sb.append(word1, minLen, len1);
        } else if (len2 > minLen) {
            sb.append(word2, minLen, len2);
        }

        return sb.toString();
    }
}
