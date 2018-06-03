# K-th Smallest Prime Fraction

LintCode 1030

LeetCode 786

## Problem

#### Description

A sorted list A contains 1, plus some number of primes. Then, for every p &lt; q in the list, we consider the fraction p/q.

What is the K-th smallest fraction considered? Return your answer as an array of ints, where answer\[0\] = p and answer\[1\] = q.

{% hint style="info" %}
A will have length between 2 and 2000.  
Each A\[i\] will be between 1 and 30000.  
K will be between 1 and A.length \* \(A.length - 1\) / 2.
{% endhint %}

#### Example

Input: A = \[1, 2, 3, 5\], K = 3  
Output: \[2, 5\]  
Explanation:  
The fractions to be considered in sorted order are:  
1/5, 1/3, 2/5, 1/2, 3/5, 2/3.  
The third fraction is 2/5.

Input: A = \[1, 7\], K = 1  
Output: \[1, 7\]

## Solution

Variation of the typical problem _searching in a sorted matrix_.

Since A is sorted and contains only primes, if we arrange all the fractions into a matrix, it will be a upper triangular where each row is sorted max to min and each column is sorted min to max, i.e. `frac[i][j] < frac[i][j - 1]` and `frac[i][j] < frac[i + 1][j]` where `frac[i][j] = A[i]/A[j]`

Then we count the number of fractions smaller than a value by scanning through each row with a monotonically increasing column pointer, as in every row, the only way to a smaller fraction value is for the column pointer to go right.

 If there are more than K elements in total satisfying `frac[i][j] < m`, we make `m` smaller, otherwise make it bigger, by binary searching within the interval of \[0.0, 1.0\].

The only extra work here is to keep tracking the numbers making the largest fraction in each iteration. As we are scanning from left to right in each row and there is no guarantee that the last element we reached in a row is larger than the last element reached in the previous row, we need to maintain a `maxFrac`.

O\(n log n\) time and O\(1\) space. We don't really need to create the matrix.

#### Code

```java
public class Solution {
    /**
     * @param A: a list of integers
     * @param K: a integer
     * @return: return two integers
     */
    public int[] kthSmallestPrimeFraction(int[] A, int K) {
        // write your code here
        int n = A.length;
        double l = 0, r = 1;
        int p = 0, q = 0;
        while (l < r) {
            double m = (l + r) / 2;
            double maxFrac = 0;
            int count = 0;
            for (int i = 0, j = 0; i < n && j < n; i++) {
                while (j < n && A[i] > m * A[j]) j++;
                count += n - j;
                if (j < n && A[i] > maxFrac * A[j]) {
                    maxFrac = (double)A[i] / A[j];
                    p = i;
                    q = j;
                }
            }
            if (count == K) {
                break;
            } if (count > K) {
                r = m;
            } else {
                l = m;
            }
        }
        return new int[] {A[p], A[q]};
    }
}
```

