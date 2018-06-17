# Palindrome Partitioning I & II

LintCode 136 & 108

LeetCode 131 & 132

## Problem

#### Description

Given a string _s_, cut _s_ into some substrings such that every substring is a palindrome.

Return the **minimum** cuts needed for a palindrome partitioning of _s_.

{% hint style="info" %}
This is Palindrome Partitioning II. Question I requires returning all possible partitioning.
{% endhint %}

#### Example

Given s = `"aab"`,

Return `1` since the palindrome partitioning \["aa", "b"\] could be produced using _1_ cut.

## Solution

Palindrome Partitioning I looks for all possible palindrome partitioning and can be solved by DFS with Backtracking - keep adding next palindrome until a partitioning is reached and then record it in solution.

Palindrome Partitioning I asks for the number of minimum cuts. This can be solved by DP. Considering dp\[i\] as the minimum cuts needed for partitioning the first i characters, then, dp\[i\] = min{dp\[j\] + 1} for all j &lt; i and string s\[i, j\] is a palindrome. Finally we return dp\[n\] - 1 since a palindrome string needs 0 cut, not 1.

Both problem requires the function of checking if substring\(i, j\) is palindrome. All the checking can be computed in advance to reduce the complexity. This is also done with DP where `dp[i][j] = dp[i + 1][j - 1] && s.charAt(i) == s.charAt(j)`.

#### Code

The palindrome matrix calculation

```java
boolean[][] isParlindrome(String s) {
    int n = s.length();
    boolean[][] pal = new boolean[n][n];
    for (int i = 0; i < n; i++) {
        pal[i][i] = true;
        if (i < n - 1) {
            pal[i][i + 1] = s.charAt(i) == s.charAt(i + 1);
        }
    }
    
    for (int len = 3; len <= n; len++) {
        for (int i = 0; i + len - 1 < n; i++) {
            int j = i + len - 1;
            pal[i][j] = pal[i + 1][j - 1] && s.charAt(i) == s.charAt(j);
        }
    }
    return pal;
}
```

Palindrome Partitioning I with DFS and Backtracking.

```java
public List<List<String>> partition(String s) {
    // write your code here
    List<List<String>> res = new ArrayList<>();
    
    if (s == null || s.length() == 0) return res;
    
    boolean[][] palinrome = isParlindrome(s);
    dfs(palinrome, s, 0, new ArrayList<>(), res);
    return res;
}

void dfs(boolean[][] palinrome, String s, int i, List<String> path, List<List<String>> res) {
    if (i == s.length()) {
        res.add(new ArrayList<>(path));
        return;
    }
    
    for (int j = i + 1; j <= s.length(); j++) {
        if (palinrome[i][j - 1]) {
            path.add(s.substring(i, j));
            dfs(palinrome, s, j, path, res);
            path.remove(path.size() - 1);
        }
    }
}
```

Palindrome Partitioning II with DP

```java
public int minCut(String s) {
    // write your code here
    if (s == null || s.length() == 0) {
        return 0;
    }

    boolean[][] palindrome = isPalindrome(s);
    int n = s.length();
    
    int[] cut = new int[n + 1];
    for (int i = 1; i <= n; i++) {
        cut[i] = i + 1; // at most cut i times
        for (int j = 0; j < i; j++) {
            if (palindrome[j][i - 1]) {
                cut[i] = Math.min(cut[i], cut[j] + 1);
            }
        }
    }

    return cut[n] - 1;
}
```

