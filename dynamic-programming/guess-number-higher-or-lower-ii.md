# Guess Number Higher or Lower II

LeetCode 375

LintCode 666

## Problem

#### Description

We are playing the Guess Game. The game is as follows:

I pick a number from **1** to **n**. You have to guess which number I picked.

Every time you guess wrong, I'll tell you whether the number I picked is higher or lower.

However, when you guess a particular number x, and you guess wrong, you pay **$x**. You win the game when you guess the number I picked.

**Example:**

```text
n = 10, I pick 8.

First round:  You guess 5, I tell you that it's higher. You pay $5.
Second round: You guess 7, I tell you that it's higher. You pay $7.
Third round:  You guess 9, I tell you that it's lower. You pay $9.

Game over. 8 is the number I picked.

You end up paying $5 + $7 + $9 = $21.
```

Given a particular **n ≥ 1**, find out how much money you need to have to guarantee a **win**.

## Solution

Can be solved with DP.

Let `dp[i][j] = the minimum money needed to guarantee a win if we pick numbers from i to j`.

Next, we pick a number `k` in this range. As we are considering the worst case, `k` must be wrong number and the correct number is in either `[i, k-1]` or `[k + 1, j]`. If we want to guarantee a win, then we must prepare `k + Math.max(dp[i][k - 1], dp[k + 1][j])` money. Thus the transition equation `dp[i][j] = min{k + Math.max(dp[i][k - 1], dp[k + 1][j])}`

```java
public int getMoneyAmount(int n) {
        int[][] dp = new int[n + 1][n + 1];
        
        for (int i = 1; i < n; i++) {
            dp[i][i + 1] = i; // if there are only two numbers
        }
        
        for (int len = 3; len <= n; len++) {
            for (int i = 1; i <= n - len + 1; i++) {
                int j = i + len - 1;
                dp[i][j] = Integer.MAX_VALUE;
                for (int k = i + 1; k <= j - 1; k++) {
                    dp[i][j] = Math.min(dp[i][j], k + Math.max(dp[i][k - 1], dp[k + 1][j]));
                }
            }
        }
        
        return dp[1][n];
    }
```

