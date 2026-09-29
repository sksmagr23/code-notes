### Palindrome Transformation

- **PS**: Given an array, transform it into a palindrome using the minimum number of operations.
In one operation, choose two values `x` and `y` and replace **every occurrence of `x` with `y`**.
We need to determine the minimum operations required to make all symmetric elements equal.

- **Approach**:- Compare every symmetric pair `a[i]` and `a[n-1-i]`.
For every mismatch, create an edge between the two values, forming groups of values that must become equal.
Find the number of connected components using BFS/DFS.
If there are `V` distinct values and `C` connected components, the answer is **`V - C`**.

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int getMinOperations(vector<int> data) {
        int n = data.size();
        unordered_set<int> values;
        for (int i = 0; i < n; i++) {
            values.insert(data[i]);
        }

        unordered_map<int, vector<int>> graph;
        for (int i = 0; i < n / 2; i++) {
            int x = data[i];
            int y = data[n - 1 - i];

            if (x != y) {
                graph[x].push_back(y);
                graph[y].push_back(x);
            }
        }

        unordered_set<int> visited;
        int components = 0;

        for (int x : values) {
            if (visited.count(x))
                continue;

            components++;

            queue<int> q;
            q.push(x);
            visited.insert(x);

            while (!q.empty()) {
                int u = q.front();
                q.pop();

                for (int v : graph[u]) {
                    if (!visited.count(v)) {
                        visited.insert(v);
                        q.push(v);
                    }
                }
            }
        }
        return values.size() - components;
    }
};
```
