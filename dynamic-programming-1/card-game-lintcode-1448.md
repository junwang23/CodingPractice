# Card Game \[LintCode 1448\]

LintCode 1448

## Problem

#### Description

A card game that gives you two non-negative integers: `totalProfit`, `totalCost`, and `n` cards'information. The `i`th card has a profit value `a[i]`and a cost value `b[i]`.It is possible to select any number of cards from these cards, form a scheme. Now we want to know how many schemes are satisfied that all selected cards' profit values are greater than `totalProfit` and the costs are less than `totalCost`.

{% hint style="info" %}
Since this number may be large, you only need to return the solution number mod `1e9 + 7`.  
0 ≤ n ≤ 100  
0 ≤ a\[i\] ≤ 100  
0 ≤ b\[i\] ≤ 100  
0 ≤ totalProfit ≤ 100  
0 ≤ totalCost ≤ 100
{% endhint %}

#### Example

Given `n = 2`，`totalProfit = 3`，`totalCost = 5`，`a = [2,3]`，`b = [2,2]` ，return`1`.

```text
Explanation:
At this time, there is only one legal scheme, which is to select both cards. At this time, a[1]+a[2] = 5 > totalProfit and b[1] + b[2] < totalCost.
```

Given `n = 3`，`totalProfit = 5`，`totalCost = 10`，`a = [6,7,8]`，`b = [2,3,5]`，return`6`.

```text
Explanation:
Suppose a legal scheme (i,j) indicates that the i-th card and the j-th card are selected.
The legal solutions at this time are:
(1),(2),(3),(1,2),(1,3),(2,3)
```

## Solution

3D DP.

The smaller problem is to calculate the number of schemes with the first k cards with total profit of i and total cost of j, i.e. `dp[k][i][j]`. As we can store all solutions with `i > totalProfit` to `dp[k][totalProfit + 1][j]`, the answer would then be the sum of `dp[n][totalProfit + 1][j]` where `j < totalCost`.

To fill the 3D matrix, if `dp[k - 1][i][j] > 0`, we have`dp[k][i][j] += dp[k - 1][i][j]`, and if `j + b[k] < totalCost`, we have`dp[k][i + a[k]][j + b[k]] += dp[k - 1][i][j]`. 

The time and space complexity are both O\(n \* totalProfit  \* totalCost\).

#### Code

```java
public int numOfPlan (int n, int totalProfit, int totalCost, int[] a, int[] b) {
    long mod = 1000000007; 
    long [][][]dp = new long [n + 1][totalProfit + 2][totalCost + 1]; 
    // dp[k][i][j] = # of schemes with k cards, totalProfit of i, and totalCost of j
    dp[0][0][0] = 1; 
    for (int k = 1; k <= n; k++) {
        // the card is counted from 1 
        for (int i = 0; i <= totalProfit + 1; i++) {
            // more than totalProfit 
            for (int j = 0; j < totalCost; j++) {
                // less than totalCost
                if (dp[k - 1][i][j] > 0) { 
                    dp[k][i][j] += dp[k - 1][i][j];
                    dp[k][i][j] %= mod;
                    if (j + b[k - 1] < totalCost) {
                        int profit = Math.min(totalProfit + 1, i + a[k - 1]);
                        dp[k][profit][j + b[k - 1]] += dp[k - 1][i][j];
                        dp[k][profit][j + b[k - 1]] %= mod;
                    }
                }
            }
        }
    }
    long sum = 0;
    for (int j = 0; j < totalCost; j++) {
        sum = (sum + dp[n][totalProfit + 1][j]) % mod;
    }
    return (int)sum;
}
```

**P. S.** As each inner iteration only deals with k - 1 and k cards, we can optimize the memory to O\(totalProfit  \* totalCost\).

