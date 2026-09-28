### What is Digit DP?

Digit DP (Dynamic Programming) is a technique used to solve problems involving numbers and their digits. It's particularly useful for counting or optimizing properties of numbers within a given range, where a brute force approach would be inefficient due to the large size of the numbers.

### Use Cases

Digit DP is often used in problems where you need to:

1. Count numbers within a certain range that satisfy specific properties (e.g., numbers with a certain sum of digits).
2. Find numbers with specific digit patterns.
3. Optimize or maximize/minimize certain properties related to the digits of numbers.

### Concept

Digit DP works by processing a number as a sequence of digits and building it from left to right (most significant to least significant digit). 

**The Range Transformation Principle:**
A core concept highlighted in standard GeeksforGeeks tutorials is transforming range queries. If a problem asks to find the count of numbers in a range $[L, R]$ satisfying a certain condition, it is usually decomposed into two sub-problems:
$$\text{Result}(L, R) = \text{Count}(R) - \text{Count}(L - 1)$$
where $\text{Count}(x)$ computes the answer for the range $[0, x]$.

### Key Components of Digit DP

1. **State Definition (`pos`, `tight`, `sum`/`rem` etc.)**:
    - **`pos`**: The current index (digit position) being processed.
    - **`tight` (or `isLimit`)**: A boolean flag. If `tight = 1`, it means the prefix of the number we are forming matches exactly the prefix of the upper bound. Thus, the current digit is restricted to a maximum of the upper bound's digit at this position. If `tight = 0`, we can place any digit from `0` to `9`.
    - **Additional Constraints**: Things like the running sum of digits, modulo remainders, or specific digit counts depending on the problem.
2. **Transition**: Try placing all valid digits `d` from `0` up to `limit`. The `limit` is the upper bound's current digit if `tight` is true, or `9` otherwise. The new tight value will be `tight && (d == limit)`.
3. **Base Case**: When all digits are placed (`pos == length`), check if the final state (e.g., target sum) is satisfied.
4. **Memoization**: Cache the results using a multidimensional array (e.g., `dp[pos][tight][sum]`) to avoid redundant calculations. 

### Example Problem: Counting Numbers with a Certain Digit Sum

Let's walk through a classic example: Count numbers between $L$ and $R$ that have a digit sum equal to $S$.

### Implementation in C++

```cpp
#include <bits/stdc++.h>
using namespace std;

// Memoization table: dp[pos][tight][sum]
// pos max: 20 (for numbers up to 10^18)
// tight max: 2 (0 or 1)
// sum max: 180 (maximum digit sum for 20 digits is 20 * 9 = 180)
long long dp[20][2][180];

// Recursive function for Digit DP
long long f(const string& numStr, int pos, int tight, int sum) {
    if (sum < 0) return 0;
    
    // Base Case: when all digits are processed
    if (pos == numStr.length()) {
        return (sum == 0) ? 1 : 0;
    }
    
    // If state is already computed
    if (dp[pos][tight][sum] != -1) {
        return dp[pos][tight][sum];
    }
    
    long long result = 0;
    
    // Determine the upper limit for the current digit
    int limit = tight ? (numStr[pos] - '0') : 9;
    
    for (int d = 0; d <= limit; d++) {
        // Calculate the new tight flag
        int new_tight = tight && (d == limit);
        
        result += f(numStr, pos + 1, new_tight, sum - d);
    }
    
    // Memoize the result
    return dp[pos][tight][sum] = result;
}

// Helper to count valid numbers from 0 to N
long long countNumbers(long long N, int S) {
    if (N < 0) return 0;
    string numStr = to_string(N);
    memset(dp, -1, sizeof(dp));
    return f(numStr, 0, 1, S);
}

// Function to find numbers in range [L, R] with digit sum S
long long countNumbersInRange(long long L, long long R, int S) {
    return countNumbers(R, S) - countNumbers(L - 1, S);
}

int main() {
    long long L = 1, R = 100;
    int S = 5;
    
    cout << "Count of numbers between " << L << " and " << R 
         << " with sum of digits equal to " << S << " is: " 
         << countNumbersInRange(L, R, S) << "\n";
         
    return 0;
}
```

### Popular Problems on LeetCode Based on Digit DP

Here are some popular problems on LeetCode that involve digit DP:

1. "Numbers At Most N Given Digit Set" - 'https://leetcode.com/problems/numbers-at-most-n-given-digit-set/'
2. "Count Unique Digits Numbers with Even Sum" - 'https://leetcode.com/problems/count-unique-digits-numbers-with-even-sum/'
3. "Maximum Sum of Digits" - 'https://leetcode.com/problems/maximum-sum-of-digits/'
4. "Count Stepping Numbers in Range" - 'https://leetcode.com/problems/count-stepping-numbers-in-range/'
5. "Numbers with Equal Digit Sum" - 'https://leetcode.com/problems/numbers-with-equal-digit-sum/'

### Conclusion

Digit DP is a powerful technique for solving problems related to digits of numbers, especially when dealing with large ranges or specific properties of numbers. By breaking down the problem into manageable subproblems using dynamic programming and memoization, you can efficiently solve problems that would otherwise be infeasible with brute force methods.