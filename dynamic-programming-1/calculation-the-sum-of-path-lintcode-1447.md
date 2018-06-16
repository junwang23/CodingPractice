# Calculation The Sum Of Path \[LintCode 1447\]

LintCode 1447

## Problem

#### Description

Enter the length `l` and width `w` of a matrix, and three points that must pass. Ask how many ways you can go from the upper left corner to the lower right corner \(every step, you can only go right or go down\). The input is guaranteed that there is at least one legal path. You only need to return the solution number mod `1000000007`.

* 1 ≤ l, w ≤ 2000

#### Example

Given `l=4`, `w=4`. The three mandatory points are `[1,1],[2,2],[3,3]`. Return `8`.

```text
Explanation:
[1,1]->[1,2]->[2,2]->[2,3]->[3,3]->[3,4]->[4,4]
[1,1]->[1,2]->[2,2]->[2,3]->[3,3]->[4,3]->[4,4]
[1,1]->[1,2]->[2,2]->[3,2]->[3,3]->[3,4]->[4,4]
[1,1]->[1,2]->[2,2]->[3,2]->[3,3]->[4,3]->[4,4]
[1,1]->[2,1]->[2,2]->[2,3]->[3,3]->[3,4]->[4,4]
[1,1]->[2,1]->[2,2]->[2,3]->[3,3]->[4,3]->[4,4]
[1,1]->[2,1]->[2,2]->[3,2]->[3,3]->[3,4]->[4,4]
[1,1]->[2,1]->[2,2]->[3,2]->[3,3]->[4,3]->[4,4]
The sum is 8.
```

Given `l=1`, `w=5`. The three points are `[1,2],[1,3],[1,4]`. Return `1`.

```text
Explanation:
[1,1]->[1,2]->[1,3]->[1,4]->[1,5]
The sum is 1.
```

## Solution

Since we can only move right and down, we know that the three points must be located from upper-left to lower right. Then the problem turns into four sub-problems calculating the number of paths from an upper-left point to a lower right point in a matrix. And the final answer would be the product of the four solutions. We just need to be careful of the mod.

Time and space complexity are both O\(m\*n\). But actually is bounded by the largest scale of sub-problems.

#### Code

```java

/**
Definition for a point.class Point {int x;int y;Point() { x = 0; y = 0; }Point(int a, int b) { x = a; y = b; }}*/
public class Solution { int mod = 1000000007; /**
@param l: The length of the matrix@param w: The width of the matrix@param points: three points @return: The sum of the paths sum / public long calculationTheSumOfPath(int l, int w, Point[] points) { // Write your code here Arrays.sort(points, new Comparator() { @Override public int compare(Point p1, Point p2) { return p1.x == p2.x ? p1.y - p2.y : p1.x - p2.x; } }); long total = count(points[0].x, points[0].y); for (int i = 1; i < 3; i++) { total = total  count(points[i].x - points[i - 1].x + 1, points[i].y - points[i - 1].y + 1) % mod; } total = total * count(l - points[2].x + 1, w - points[2].y + 1) % mod; return total; }long count(int m, int n) { long sum = 0; long[] dp = new long[n]; Arrays.fill(dp, 1); for (int i = 1; i < m; i++) { for (int j = 1; j < n; j++) { dp[j] = (dp[j] + dp[j - 1]) % mod; } } return dp[n - 1]; } }
```

