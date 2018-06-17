# Longest Palindromic Subsequence

LintCode 667

LeetCode 516

## Problem

#### Description

Given a string s, find the longest palindromic subsequence's length in s. You may assume that the maximum length of s is 1000.

**Example 1:**  
Input: `"bbbab"`, Output: 4

One possible longest palindromic subsequence is `"bbbb"`.

**Example 2:**  
Input: `"cbbd"`, Output: 2

One possible longest palindromic subsequence is `"bb"`.

## Solution

The keyword **subsequence** means the ordering of characters is immutable. The problem can be easily solved by 2D DP.

 `dp[i][j]`: the length of the longest palindromic subsequence in substring \(i, j\)

Initially,  `dp[i][i] = 1`. Transition function:  

`dp[i][j] = dp[i+1][j-1] + 2` if `s.charAt(i) == s.charAt(j)`  
otherwise, `dp[i][j] = Math.max(dp[i+1][j], dp[i][j-1])`

#### Code

```java
public class Solution {
    /**
     * @param s: the maximum length of s is 1000
     * @return: the longest palindromic subsequence's length
     */
    public int longestPalindromeSubseq(String s) {
        // write your code here
        if (s == null || s.length() == 0) {
            return 0;
        }
        
        int[][] dp = new int[s.length()][s.length()];
        
        for (int i = s.length() - 1; i >= 0; i--) {
            dp[i][i] = 1;
            for (int j = i + 1; j < s.length(); j++) {
                if (s.charAt(i) == s.charAt(j)) {
                    dp[i][j] = dp[i + 1][j - 1] + 2;
                } else {
                    dp[i][j] = Math.max(dp[i + 1][j], dp[i][j - 1]);
                }
            }
        }
        
        return dp[0][s.length() - 1];
    }
}
```

