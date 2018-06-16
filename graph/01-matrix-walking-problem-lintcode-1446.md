# 01 Matrix Walking Problem \[LintCode 1446\]

LintCode 1446

## Problem

#### Description

Given an `01` matrix `gird` of size `n*m`, `1` is a wall, `0` is a road, now you can turn a `1` in the `grid` into `0`, Is there a way to go from the upper left corner to the lower right corner? If there is a way to go, how many steps to take at least?

{% hint style="info" %}
1 ≤ n ≤ 1 ∗ 10^​3​​  
1 ≤ m ≤ 1 ∗ 10^​3​​
{% endhint %}

#### Example

Given `a = [[0,1,0,0,0],[0,0,0,1,0],[1,1,0,1,0],[1,1,1,1,0]]`，return `7`.

```text
Explanation:
Change `1` at (0,1) to `0`, the shortest path is as follows:
(0,0)->(0,1)->(0,2)->(0,3)->(0,4)->(1,4)->(2,4)->(3,4) There are many other options of length `7`, not listed here.
```

Given `a = [[0,1,1],[1,1,0],[1,1,0]]`, return `-1`.

```text
Explanation:
Regardless of which `1` is changed to `0`, there is no viable path.
```

## Solution

Once the idea is clear, the problem becomes very easy to solve.

Since we are allowed to flip one cell, we calculate the distances from upper left cell to each 1 and the distances from lower right cell to each 1, saving in two matrices. If an 1 is not reachable, its distance is set to m\*n \(i.e. infinite\). These distances can be easily calculated with two BFS.

Then the final answer would be the smallest value from summing the corresponding cells of the two distance matrices, if the grid is 1 in that position, meaning we flip that cell. If there is no solution, the minimum sum will be at least m\*n, in which case we return -1.

The time and space complexity are both O\(m\*n\).

```java
public class Solution {
    int[][] dir = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};
    /**
     * @param grid: The gird
     * @return: Return the steps you need at least
     */
    public int getBestRoad(int[][] grid) {
        // Write your code here
        int n = grid.length;
        int m = grid[0].length;
        
        int[][] distToUL = bfs(grid, n, m, 0, 0);
        int[][] distToLR = bfs(grid, n, m, n - 1, m - 1);
        
        int minDist = m * n;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == 1) {
                    minDist = Math.min(minDist, distToUL[i][j] + distToLR[i][j]);
                }
            }
        }
        return minDist >= m * n ? -1 : minDist;
    }
    
    int[][] bfs(int[][] grid, int n, int m, int sx, int sy) {
        boolean[][] visited = new boolean[n][m];
        visited[sx][sy] = true;
        int[][] distMat = new int[n][m];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == 1)
                    distMat[i][j] = n * m;
            }
        }
        
        Queue<int[]> q = new LinkedList<>();
        q.offer(new int[]{sx, sy});
        int dist = 0;
        while (!q.isEmpty()) {
            int size = q.size();
            while (size-- > 0) {
                int[] cur = q.poll();
                int x = cur[0];
                int y = cur[1];
                if (grid[x][y] == 1) {
                    distMat[x][y] = dist;
                    continue;
                }
                for (int[] d: dir) {
                    int r = x + d[0];
                    int c = y + d[1];
                    if (r >= 0 && r < n && c >= 0 && c < m && !visited[r][c]) {
                        visited[r][c] = true;
                        q.offer(new int[]{r, c});
                    }
                }
            }
            dist++;
        }
        return distMat;
    }
}
```
