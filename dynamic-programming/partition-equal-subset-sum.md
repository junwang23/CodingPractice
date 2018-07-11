# Partition Equal Subset Sum

LeetCode 416

LintCode 588

## Problem

#### Description

Given a `non-empty` array containing `only positive integers`, find if the array can be partitioned into `two` subsets such that the sum of elements in both subsets is equal.

{% hint style="info" %}
Each of the array element will not exceed 100.  
The array size will not exceed 200.
{% endhint %}

#### Example

Given nums = `[1, 5, 11, 5]`, return `true`  
two subsets: `[1, 5, 5]`, `[11]`

Given nums = `[1, 2, 3, 9]`, return `false`

## Solution

Consider this problem as to find a subset of the array summing to a specific number, thus a 0/1 knapsack problem.

We set the boolean `dp[i][j] = if there exists a subset summing to j in the first i numbers`, then `dp[i][j] = dp[i - 1][j] || dp[i - 1][j - nums[i - 1]]`

### Code

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int sum = 0, n = nums.length;
        for (int x: nums) {
            sum += x;
        }
        if ((sum & 1) == 1) return false; // odd number
        
        sum /= 2;
        
        boolean[][] dp = new boolean[n + 1][sum + 1];
        dp[0][0] = true;
        for (int i = 1; i <= n; i++) {
            dp[i][0] = true;
        }
        
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= sum; j++) {
                dp[i][j] = dp[i - 1][j] || (j >= nums[i - 1] && dp[i - 1][j - nums[i - 1]]);
            }
        }
        
        return dp[n][sum];
    }
}
```

An optimized version with less memory cost.

```java
class Solution {
    public boolean canPartition(int[] nums) {
        int sum = 0, n = nums.length;
        for (int x: nums) {
            sum += x;
        }
        if ((sum & 1) == 1) return false; // odd number
        
        sum /= 2;
        
        boolean[] dp = new boolean[sum + 1];
        dp[0] = true;
        
        for (int x: nums) {
            for (int i = sum; i > 0; i--) {
                dp[i] = dp[i] || (i >= x && dp[i - x]);
            }
        }
        
        return dp[sum];
    }
}
```

