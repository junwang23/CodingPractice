# k Sum

LintCode 89

## Problem

#### Description

Given _n_ distinct positive integers, integer _k_ \(_k_ &lt;= _n_\) and a number _target_.

Find _k_ numbers where sum is target. Calculate how many solutions there are?

#### Example

Given `[1,2,3,4]`, k = `2`, target = `5`.

There are `2` solutions: `[1,4]` and `[2,3]`.

Return `2`.

## Solution

Dynamic Programming in 3D.

`dp[p][i][t]` = number of solutions for selecting p numbers with the sum of t from first i numbers of the array. Initially, `dp[0][i][0]` = 1 \(select 0 numbers with sum 0\).

#### Transition function

```java
dp[p][i][t] = dp[p][i-1][t] + dp[p-1][i-1][t-A[i-1]]
```

dp\[p\]\[i-1\]\[t\] = first i - 1 elements select p summing to t \(solutions without A\[i -1\]\)

dp\[p - 1\]\[i-1\]\[t - A\[i - 1\]\] = first i - 1 element select p - 1 summing to t - A\[i - 1\] \(solutions with A\[i - 1\]\)

#### Code

```java
public class Solution {
    /**
     * @param A: An integer array
     * @param k: A positive integer (k <= length(A))
     * @param target: An integer
     * @return: An integer
     */
    public int kSum(int[] A, int k, int target) {
        // write your code here
        if(A == null || A.length == 0){
            return 0;
        }

        int n = A.length;
        int[][][] dp = new int[k + 1][n + 1][target + 1];

        for(int j = 0; j <= n; j++){
            dp[0][j][0] = 1;
        }

        for (int p = 1; p <= k; p++) {
            for (int i = p; i <= n; i++) {
                for (int t = 1; t <= target; t++) {
                    dp[p][i][t] = dp[p][i - 1][t];
                    if (t >= A[i - 1]) {
                        dp[p][i][t] += dp[p - 1][i - 1][t - A[i - 1]];
                    }
                }
            }
        }

        return dp[k][n][target];
    }
}
```

We can reduce the space cost to O\(n^2\) by slicing either the dimension of k or of n.

```java
public class Solution {
    /**
     * @param A: An integer array
     * @param k: A positive integer (k <= length(A))
     * @param target: An integer
     * @return: An integer
     */
    public int kSum(int[] A, int k, int target) {
        // write your code here
        int[][] prev = new int[k+1][target+1];
        prev[0][0] = 1;
        for(int i = 0; i < A.length; i++) {
            int[][] current = new int[k+1][target+1];
            current[0][0] = 1;
            for(int j = 1; j <= k; j++) {
                for(int x = 0; x <= target; x++) {
                    current[j][x] = prev[j][x];
                    if(x>=A[i]) {
                        current[j][x] += prev[j-1][x-A[i]];
                    }
                }
            }
            prev = current;
        }
        return prev[k][target];
    }
}
```

