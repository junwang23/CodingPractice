# Count of Range Sum

LeetCode 327

LintCode 1293

## Problem

#### Description

Given an integer array `nums`, return the number of range sums that lie in `[lower, upper]` inclusive.  
Range sum `S(i, j)` is defined as the sum of the elements in `nums` between  indices `i` and `j` \(`i ≤ j`\), inclusive.

#### Example

`Given nums = [-2, 5, -1], lower = -2, upper = 2,  
Return 3.  
The three ranges are : [0, 0], [2, 2], [0, 2] and their respective sums are: -2, -1, 2.`

## Solution

Binary Indexed Tree \(Fenwick Tree\) solution would be the easiest to code with O\(n log n\) time.

It's easy to think of using a prefix sum array `int[] sum` to store range sum. Here, for index i, we hope to find the number of index j where `j > i`  and `lower <= sum[j] - sum[i] <= upper`. This can be transformed into `sum[i] + lower <= sum[j] <= sum[i] + upper`, means that we can count the number of `sum[j]` located within `[sum[i] + lower, sum[i] + upper]` if they have been stored in the Fenwick tree. Thus visiting the sum array backwards.

One thing must be noticed is that there may be duplicates in the sum array. We use a modified binary search to find the index of the first element larger or equal to the searching value in the sorted sum array, which is carefully handled in the following code between line 36-38. Also, declare the sum array with `long` type to avoid overflow.

### Code

```java
class Solution {
    int n;
    int[] bit;
    
    void insert(int i, int x) {
        while (i <= n) {
            bit[i] += x;
            i += (i & -i);
        }
    }
    
    int query(int i) {
        int sum = 0;
        while (i > 0) {
            sum += bit[i];
            i -= (i & -i);
        }
        return sum;
    }
    
    public int countRangeSum(int[] nums, int lower, int upper) {
        
        long[] sums = new long[nums.length + 1];
        for (int i = 1; i <= nums.length; i++) {
            sums[i] = sums[i - 1] + nums[i - 1];
        }
        
        n = sums.length;
        bit = new int[n + 1];
        
        long[] sorted = Arrays.copyOf(sums, n);
        Arrays.sort(sorted);
        
        int res = 0;
        for (int i = n - 1; i >= 0; i--) {
            int hi = index(sorted, sums[i] + upper + 1),
                lo = index(sorted, sums[i] + lower);
            res += query(hi - 1) - query(lo - 1);
            insert(index(sorted, sums[i]), 1);
        }
        return res;
    }
    
    int index(long[] arr, long val) {
        int l = 0, r = arr.length - 1, m = 0;
        while (l <= r) {
            m = l + ((r - l) >> 1);
            if (arr[m] >= val) {
                r = m - 1;
            } else {
                l = m + 1;
            }
        }
        return l + 1;
    }
}
```

