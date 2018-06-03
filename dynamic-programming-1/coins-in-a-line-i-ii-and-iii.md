# Coins in a Line I, II, and III

LintCode 394, 395, and 396

## Problem

#### Coins in a Line I

There are n coins in a line. Two players take turns to take one or two coins from right side until there are no more coins left. The player who take the last coin wins.

#### Coins in a Line II

 There are n coins with different value in a line. Two players take turns to take one or two coins from left side until there are no more coins left. The player who take the coins with the most value wins.

#### Coins in a Line III

There are _n_ coins in a line. Two players take turns to take a coin from one of the ends of the line until there are no more coins left. The player with the larger amount of money wins.

Could you please decide the **first** player will win or lose?

## Solution

The three games with different rules can all be solved with DP and min-max, with proper initialization and transition.

For problem I, we define `dp[i]` as the outcome of the current player when the player starts from coin `i`. Then `dp[i] = !dp[i - 1] || !dp[i - 2]`, meaning that, if the player wants to win, then by taking one or two coins from right, there exists a path the opponent will sure lose.

```java
public boolean firstWillWin(int n) {
    // write your code here
    if (n == 0) return false;
    if (n <= 2) return true;
    
    boolean[] dp = new boolean[n + 1];
    dp[1] = dp[2] = true;
    for (int i = 3; i <= n; i++) {
        dp[i] = !dp[i - 1] || !dp[i - 2];
    }
    return dp[n];
}
```

There is also a mathematical solution for problem I, which we can simply return `n % 3 != 0`.

Problem II becomes more complex as each coin now has different values. But we can still define `dp[i]` as the maximum total a player can get if he/she starts from i. The player can either take one coin or two coins, so does the other player. By assuming player 2 will always make the optimal move, if player 1 takes one coin, then `take1 = coin[i] + min(dp[i + 2], dp[i + 3])`, and if takes two, then `take2 = coin[i] + coin[i + 1] + min(dp[i + 3], dp[i + 4])`. Player 1 also makes the best move, so `dp[i] = max(take1, take2)`. Also, if a player can at most make `dp[0]`, then the opponent can get `total value of all coins - dp[0]`.

Note that here we iterate the 2D dp array diagonally as the values from longer coin arrays rely on the values from shorter coin arrays.

```java
public boolean firstWillWin(int[] values) {
    // write your code here
    if (values == null || values.length == 0) return false;
    if (values.length <= 2) return true;
    
    int n = values.length;
    int[] dp = new int[n + 1]; // dp[i]: max value a player gets starting from coin i
    dp[n - 1] = values[n - 1];
    dp[n - 2] = values[n - 2] + values[n - 1];
    dp[n - 3] = values[n - 3] + values[n - 2];
    for (int i = n - 4; i >= 0; i--) {
        dp[i] = Math.max(values[i] + Math.min(dp[i + 2], dp[i + 3]),
                         values[i] + values[i + 1] + Math.min(dp[i + 3], dp[i + 4]));
    }
    int sum = 0;
    for (int x: values)
        sum += x;
    return dp[0] > sum - dp[0];
}
```

Problem III further changes the rules by allowing a player to take one coin from either end, however, we can still solve it using the very same idea of min-max in 2D. We define dp\[i\]\[j\] as the max value a player can get when coin i to coin j are left. The player will take either the left-end coin so `total1 = coin[i] + min(dp[i + 2][j], dp[i + 1][j - 1])`, or the right-end coin `total2 = coin[j] + min(dp[i][j - 2], dp[i + 1][j - 1])`. Then the player takes the best move so `dp[i][j] = max(total1, total2)`.

```java
public boolean firstWillWin(int[] values) {
    // write your code here
    if (values == null) return false;
    int n = values.length;
    if (n == 1) return true;
    
    int[][] dp = new int[n][n];
    int sum = 0;
    for (int i = 0; i < n; i++) {
        dp[i][i] = values[i];
        sum += values[i];
    }
    
    for (int i = 0; i < n - 1; i++) {
        dp[i][i + 1] = Math.max(values[i], values[i + 1]);
    }
    
    for (int len = 3; len <= n; len++) {
        for (int i = 0, j = len - 1; j < n; i++, j++) {
            dp[i][j] = Math.max(values[i] + Math.min(dp[i + 2][j], dp[i + 1][j - 1]),
                                values[j] + Math.min(dp[i + 1][j - 1], dp[i][j - 2]));
        }
    }
    
    return dp[0][n - 1] > sum - dp[0][n - 1];
}
```

