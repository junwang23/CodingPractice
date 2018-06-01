# Kth Smallest Element in a Sorted Matrix

LintCode 1272

LeetCode 378

## Problem

#### Description

Given a n x n matrix where each of the rows and columns are sorted in ascending order, find the kth smallest element in the matrix.

Note that it is the kth smallest element in the sorted order, not the kth distinct element.

You may assume k is always valid, 1 ≤ k ≤ n2.Have you met this question in a real interview?  Yes

#### Example

`matrix = [[ 1, 5, 9],  
          [10, 11, 13],  
          [12, 13, 15]],  
k = 8  
return 13.`

## Solution

Remembering the problem of _searching in a sorted matrix_? If we know a number to search, we can easily count the number of element smaller/larger than it in the matrix. Then how to locate the number? Binary search between min and max. We know min and max.

#### Code

```java
public class Solution {
    /**
     * @param matrix: List[List[int]]
     * @param k: a integer
     * @return: return a integer
     */
    public int kthSmallest(int[][] matrix, int k) {
        // write your code here
        int n = matrix.length;
        
        int lo = matrix[0][0], hi = matrix[n - 1][n - 1];
        while (lo <= hi) {
            int mid = lo + (hi - lo) / 2;
            int count = countLessEqual(matrix, mid);
            if (count < k) {
                lo = mid + 1;
            } else { // if count == k, reduce hi but keep lo
                hi = mid - 1;   // as we will return lo
            }
        }
        return lo;
    }
    
    private int countLessEqual(int[][] matrix, int val) {
        int count = 0;
        int n = matrix.length, i = 0, j = n - 1;
        while (i < n && j >= 0) {
            if (matrix[i][j] > val) {
                j--;
            } else {
                count += j + 1;
                i++;
            }
        }
        return count;
    }
}
```

