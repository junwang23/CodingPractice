# Bus Routes/Station

LeetCode 815

LintCode 825

## Problem

There are minor variations in problem description in LeetCode and LintCode that only affects edge condition.

#### Description

There are a city's `N` bus information, `route[i]` stores the bus stop through which the `i-th` bus passes, find the minimum number of transfers from station `A` to station `B`. If you can't get to station `B` from station `A`, return `-1`.

* `1 <= N <= 100`, `2 <= |route[i]| <= 100`, `0 <= route[i][j] <= 2^31 - 1`
* A and B two stations must exist and they are different

#### Example

Given N = `2`, route = `[[1, 2, 3, 4], [3, 5, 6, 7]]`, A = `1`, B = `4`, return `1`.

```text
Explanation:
We only need to take the bus No.0, you can get to Station 3 from Station 0.
```

Given N = `2`, route = `[[1, 2, 3, 4], [3, 5, 6, 7]]`, A = `1`, B = `7`, return `2`.

```text
Explanation:
We need to take bus No.0 from station 0 and then take bus No.1 to station 6 at station 2.
```

## Solution

It is obvious that this is a graph problem. Here we consider each station as a node in the graph. Then the problem can be naturally solved by BFS. That is to say, in each station that is not the destination, we want to know by transferring one more time, which stations can we reach. If we can reach the destination, we return the result.

We need two maps in this process, which are 1\) the stations that can be reached by each bus \(given by the problem\) and 2\) the buses we can take at each station \(needs to be built\). Also, if a bus has been taken, we don't take it again. This also means that it is OK to visit the same station multiple times as the second time we visit it, we won't take the buses again.

The time complexity is O\(N\*M\) when there are N stations and M routes, as we are going from station to station and at each station there can be at most M routes. Same goes for the space cost.

### Code

```java
class Solution {
    public getMinTransferNumber(int N, int[][] route, int A, int B) {
        if (A == B) return 0; // check if this has been ensured in problem description
        
        Map<Integer, List<Integer>> stations = new HashMap<>(); // station -> buses
        for (int i = 0; i < N; i++) {
            for (int st: route[i]) {
                List<Integer> buses = stations.getOrDefault(st, new ArrayList<>());
                buses.add(i);
                stations.put(st, buses);
            }
        }
        
        Queue<Integer> q = new LinkedList<>();
        q.offer(A);
        Set<Integer> visited = new HashSet<>();
        int transfer = 0;
        while (!q.isEmpty()) {
            int size = q.size();
            transfer++;
            while (size-- > 0) {
                int curSt = q.poll();
                for (int bus: stations.get(curSt)) {
                    if (visited.contains(bus))
                        continue;
                        
                    visited.add(bus);
                    for (int st: route[bus]) {
                        if (st == B)
                            return transfer;
                        q.offer(st);
                    }
                }
            }
        }
        
        return -1;
    }
}
```

