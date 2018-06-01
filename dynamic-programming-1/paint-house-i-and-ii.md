# Paint House I & II

LintCode 515 & 526

LeetCode 256 \[locked\] & 265 \[locked\]

## Problem

#### Description

There are a row of `n` houses, each house can be painted with one of the `k` colors. The cost of painting each house with a certain color is different. You have to paint all the houses such that no two adjacent houses have the same color.

The cost of painting each house with a certain color is represented by a `n` x `k` cost matrix. For example, `costs[0][0]` is the cost of painting house `0` with color `0`; `costs[1][2]` is the cost of painting house `1` with color `2`, and so on... Find the minimum cost to paint all houses.

All costs are positive integers.

#### Example

Given `n` = 3, `k` = 3, `costs` = `[[14,2,11],[11,14,5],[14,3,10]]` return `10`

house 0 is color 2, house 1 is color 3, house 2 is color 2, `2 + 5 + 3 = 10`

#### Challenge

Could you solve it in O\(nk\)?

## Solution

Paint House I limits k = 3. Both can be solved by scanning through each house i, and the min cost of painting house i into color j is costs\[i\]\[j\] + min\(cost of painting house i - 1 with colors other than j\). Thus can be easily solved in O\(nk\) time and O\(k\) space.

#### Code

```java
public class Solution {
    /**
     * @param costs: n x k cost matrix
     * @return: an integer, the minimum cost to paint all houses
     */
    public int minCostII(int[][] costs) {
        // write your code here
        if (costs == null || costs.length == 0)
            return 0;
        
        int m = costs.length; // # houses
        int n = costs[0].length; // # colors;
        
        int[] minCosts = new int[n];
        for (int i = 0; i < m; i++) {
            int[] next = new int[n];
            for (int j = 0; j < n; j++) {
                next[j] = minExcept(minCosts, j) + costs[i][j];
            }
            minCosts = next;
        }
        return minExcept(minCosts, -1);
    }
    
    int minExcept(int[] costs, int i) {
        int min = Integer.MAX_VALUE;
        for (int j = 0; j < costs.length; j++) {
            if (j != i) {
                min = Math.min(costs[j], min);
            }
        }
        return min;
    }
}
```

