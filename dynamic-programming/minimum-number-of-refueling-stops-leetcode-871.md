# Minimum Number of Refueling Stops \[LeetCode 871\]

LeetCode 871

## Problem

### Description

A car travels from a starting position to a destination which is `target` miles east of the starting position.

Along the way, there are gas stations.  Each `station[i]` represents a gas station that is `station[i][0]` miles east of the starting position, and has `station[i][1]` liters of gas.

The car starts with an infinite tank of gas, which initially has `startFuel` liters of fuel in it.  It uses 1 liter of gas per 1 mile that it drives.

When the car reaches a gas station, it may stop and refuel, transferring all the gas from the station into the car.

What is the least number of refueling stops the car must make in order to reach its destination?  If it cannot reach the destination, return `-1`.

Note that if the car reaches a gas station with 0 fuel left, the car can still refuel there.  If the car reaches the destination with 0 fuel left, it is still considered to have arrived.

**Example 1:**

```text
Input: target = 1, startFuel = 1, stations = []
Output: 0
Explanation: We can reach the target without refueling.
```

**Example 2:**

```text
Input: target = 100, startFuel = 1, stations = [[10,100]]
Output: -1
Explanation: We can't reach the target (or even the first gas station).
```

**Example 3:**

```text
Input: target = 100, startFuel = 10, stations = [[10,60],[20,30],[30,30],[60,40]]
Output: 2
Explanation: 
We start with 10 liters of fuel.
We drive to position 10, expending 10 liters of fuel.  We refuel from 0 liters to 60 liters of gas.
Then, we drive from position 10 to position 60 (expending 50 liters of fuel),
and refuel from 10 liters to 50 liters of gas.  We then drive to and reach the target.
We made 2 refueling stops along the way, so we return 2.
```

**Note:**

1. `1 <= target, startFuel, stations[i][1] <= 10^9`
2. `0 <= stations.length <= 500`
3. `0 < stations[0][0] < stations[1][0] < ... < stations[stations.length-1][0] < target`

## Solution

### Solution 1 \(DP\)

The DP solution is a bit tricky to think of. 

Let `dp[t] = the furthest distance we can reach with t times of fueling`. 

For each station `i`, it will be considered only for at most `i + 1` times of refuels \(stations array is counted from 0\) and when`dp[t - 1] >= stations[i - 1][0]` \(means we can pass the station with `t - 1` refuels,\). We then update`dp[t] = max(dp[t], dp[t - 1] + stations[i][1]),` meaning that we refuel at the current station trying to improve`dp[t]`.

For example, in **Example 3**, obviously, `dp[0] = startFuel = 10`. We first consider station 0 \(`i = 0`\), we then update `dp[1] = max(dp[1], dp[0] + stations[0][1]) = 70` in the inner loop as `dp[0] >= stations[0][0]` \(we can pass the first station with the initial gas\). 

Next, we consider station 1, we will have `dp[2] = max(dp[2], dp[1] + stations[1][1]) = 100` as `dp[1] > stations[1][0]`, but `dp[0] < stations[1][0]` so we will not update `dp[1]`.

Similarly, we go to stations 2. As `dp[2] >= stations[2][0]`, `dp[3] = dp[2] + stations[2][1] = 130`. Also, `dp[1] >= stations[2][0]`, however, `dp[1] + stations[2][1] = 100 == dp[2]`, so `dp[2]` will remain the same. Again, `dp[0] < stations[2][0]`.

At last, we return the minimum t with `dp[t] >= target`. O\(n^2\) time and O\(n\) space.

#### Code

```java
class Solution {
    public int minRefuelStops(int target, int startFuel, int[][] stations) {
        int len = stations.length;
        long[] dp = new long[len + 1];
        
        dp[0] = startFuel;
        for (int i = 0; i < len; i++) {
            for (int t = i + 1; t > 0 && dp[t - 1] >= stations[i][0]; t--) {
                dp[t] = Math.max(dp[t], dp[t - 1] + stations[i][1]);
            }
        }

        for (int t = 0; t <= len; t++) {
            if (dp[t] >= target) return t;
        }
        
        return -1;
    }
}
```

### Solution 2 \(Greedy\)

The greedy solution keeps track of the gas left in tank whenever we pass a station and refuel when tank is below 0. We use a **max heap** to store the gas supply of all passing stations, so that we always refuel from the biggest gas station when necessary. By such, we guarantee the minimum refuel numbers, and also detect -1 condition when the priority queue is empty and tank is still below 0.

O\(n\*log n\) time and O\(n\) space.

#### Code

```java
class Solution {
    public int minRefuelStops(int target, int startFuel, int[][] stations) {
        PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());
        int tank = startFuel;
        int res = 0, prev = 0;
        
        for (int[] s: stations) {
            int loc = s[0], gas = s[1];
            tank -= loc - prev;
            
            while (tank < 0 && !pq.isEmpty()) {
                tank += pq.poll();
                res++;
            }
            
            if (tank < 0) return -1;
            pq.offer(gas);
            prev = loc;
        }
        
        tank -= target - prev;
        while (tank < 0 && !pq.isEmpty()) {
            tank += pq.poll();
            res++;
        }
        
        return tank < 0 ? -1 : res;
    }
}
```

