# Bitmask DP: Traveling Salesman Problem (TSP)

### Problem Statement

The **Traveling Salesman Problem (TSP)** is a classic algorithmic problem in computer science. Given a set of cities and the distance between every pair of cities, the problem asks: 
> *"What is the shortest possible route that visits every city exactly once and returns to the origin city?"*

### Naive Approach vs Bitmask DP

**1. Brute Force (Naive Approach):**
The most direct way to solve this is to generate all possible permutations of the cities. Since we fix the starting city, there are $(N-1)!$ permutations for the remaining cities. 
* **Time Complexity:** $O(N!)$
* **Feasibility:** This is extremely slow and only computationally feasible for $N \le 10$.

**2. Dynamic Programming with Bitmasking (The GFG Approach):**
To optimize the brute force approach, we can use Dynamic Programming. We notice that when exploring paths, the exact order of visited cities doesn't matter as much as:
1. Which cities have been visited so far (represented by a **Bitmask**).
2. Which city we are currently at.

By storing the minimum cost for each `(visited_mask, current_city)` state, we avoid recomputing overlapping subproblems.

* **Time Complexity:** $O(N^2 \cdot 2^N)$. There are $2^N$ possible subsets (masks) and $N$ possible current cities. For each state, we iterate over $N$ possible next cities.
* **Space Complexity:** $O(N \cdot 2^N)$ for the DP memoization table `dp[mask][city]`.
* **Feasibility:** This drastically reduces the time complexity, making it feasible for $N$ up to 20. (e.g., $2^{20}$ states is roughly 1 million, which is easily manageable in memory and time, whereas $20!$ is astronomical).

### Concept & State Transition

* **State (`mask`, `pos`)**: 
  * `mask`: An integer where the $i$-th bit is `1` if the $i$-th city has been visited, and `0` otherwise.
  * `pos`: The index of the current city we are at.
* **Recurrence Relation**: 
  To find the minimum cost from the current state, we iterate over all unvisited cities `next_city`. 
  `Cost = dist[pos][next_city] + TSP(mask | (1 << next_city), next_city)`
  We take the minimum of these costs.

---

A recursive solution to the Traveling Salesman Problem (TSP) with memoization can be implemented to achieve a similar effect as the DP table but using a top-down approach. This recursive approach uses the same principles of DP with bitmasking but is often easier to understand and implement for some people.

### Memoization (Top-Down DP)

### Steps

1. **State Representation**:
    - Use a bitmask to represent the set of visited cities.
    - Use a DP table (memoization table) to store the results of subproblems. This table is typically a 2D array where `dp[mask][i]` represents the minimum cost to visit all cities in `mask` ending at city `i`.
2. **Recursive Function**:
    - Define a recursive function that takes the current state (current city and visited cities) and returns the minimum cost to complete the tour.
3. **Base Case**:
    - If all cities have been visited, return the cost to return to the starting city.
4. **State Transition**:
    - For each unvisited city, recursively calculate the cost of visiting that city and then completing the tour from there.

### C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

int f(int mask, int pos, const vector<vector<int>>& cost, vector<vector<int>>& dp) {
    int N = cost.size();
    if (mask == (1 << N) - 1) {
        return cost[pos][0];  // Return to the starting city
    }
    if (dp[mask][pos] != -1) {
        return dp[mask][pos];
    }

    int ans = INT_MAX;
    for (int city = 0; city < N; ++city) {
        if (!(mask & (1 << city))) {  // If the city is not visited
            int newCost = cost[pos][city] + f(mask | (1 << city), city, cost, dp);
            ans = min(ans, newCost);
        }
    }
    return dp[mask][pos] = ans;
}

int tsp(const vector<vector<int>>& cost) {
    int N = cost.size();
    vector<vector<int>> dp(1 << N, vector<int>(N, -1));
    return f(1, 0, cost, dp);
}

int main() {
    vector<vector<int>> cost = {
        {0, 10, 15, 20},
        {10, 0, 35, 25},
        {15, 35, 0, 30},
        {20, 25, 30, 0}
    };
    cout << "The minimum cost is " << tsp(cost) << endl;
    return 0;
}

```

### Explanation

1. **State Representation**:
    - `mask` is a bitmask representing the set of visited cities.
    - `pos` is the current city.
2. **Recursive Function `tspUtil`**:
    - If `mask` equals `(1 << N) - 1` (all cities visited), return the cost to return to the starting city.
    - If the result for the current state (`dp[mask][pos]`) is already computed, return it.
    - Iterate through all cities and calculate the cost of visiting each unvisited city (`newCost`).
    - Recursively call `tspUtil` for the new state (new city visited).
3. **Memoization Table**:
    - `dp` is used to store results of subproblems to avoid redundant calculations.
4. **Base Case**:
    - When all cities are visited, the function returns the cost to return to the starting city.
5. **Main Function `tsp`**:
    - Initializes the DP table and calls the recursive function starting from the first city with only the first city visited (`mask = 1`).

### Dry Run

For the `cost` matrix:

```
0  10  15  20
10  0  35  25
15  35  0  30
20  25  30  0

```

1. Start at city 0 with `mask = 1` (only city 0 visited).
2. From city 0, try visiting all other cities recursively.
3. Update the DP table with the minimum cost for each state.
4. Continue until all cities are visited and calculate the total cost.

This recursive approach with memoization efficiently solves the TSP problem by breaking it down into smaller subproblems and storing their results to avoid redundant calculations.

Must Do Question:- https://leetcode.com/problem-list/50vt8ied/