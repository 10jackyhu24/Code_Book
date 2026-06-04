# C++ Codebook

This codebook contains commonly used algorithms and utilities optimized for competitive programming.

## 1. Basic Utilities

### IO Speedup (cin, cout)

```cpp
#include <iostream>

using namespace std;

void speedup() {
    cin.sync_with_stdio(0);
    cin.tie(0);
}
```

### Greatest Common Divisor (GCD)

```cpp
int GCD(int x, int y) {
    if (!x) return y;
    if (!y) return x;
    while ((x %= y) && (y %= x));
    return x + y;
}
```

### Least Common Multiple (LCM)

```cpp
int LCM(int num1, int num2) {
    return ((num1 * num2) / GCD(num1, num2));
}
```

### Priority Queue (std::priority_queue)
> **Description:** Demonstrates the use of C++ built-in `std::priority_queue` (from `<queue>`), including default behavior (Max-Heap), built-in Min-Heap, and Custom Struct Sorting.
> - **Default:** Max-Heap (largest element at the top).
> - **Built-in Min-Heap:** Uses `std::greater` (smallest element at the top). Very common with `std::pair` in graph algorithms.
> - **Custom Struct Sorting:** Overload the `<` operator inside a custom `struct`.

```cpp
#include <queue>
#include <vector>
#include <iostream>
#include <utility> // for pair

using namespace std;

// Struct with custom sorting rules
struct Item {
    int id;
    int priority;

    // Custom sorting rule: sort by priority ascending (min-heap)
    // If priority is equal, sort by id ascending (smaller id at the top)
    // Note: Since priority_queue is a Max-Heap by default, we invert the comparison logic.
    bool operator<(const Item& other) const {
        if (priority != other.priority) {
            return priority > other.priority; // Larger priority means "less" priority in max-heap (so smaller comes to top)
        }
        return id > other.id; // Larger ID means "less" priority (so smaller ID comes to top)
    }
};

void priorityQueueUsage() {
    priority_queue<int> max_pq;
    max_pq.push(10);
    max_pq.push(30);
    max_pq.push(20);
    // max_pq.top() is 30 (largest element first)

    priority_queue<int, vector<int>, greater<int>> min_pq;
    min_pq.push(10);
    min_pq.push(30);
    min_pq.push(20);
    // min_pq.top() is 10 (smallest element first)

    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pair_pq;
    pair_pq.push({5, 101}); // {distance, node}
    pair_pq.push({2, 102});
    pair_pq.push({5, 100});
    // pair_pq.top() is {2, 102} (smallest distance first, then smallest node index)

    priority_queue<Item> custom_pq;
    custom_pq.push({101, 5});
    custom_pq.push({102, 5});
    custom_pq.push({103, 2});
    // custom_pq.top() is {103, 2} (priority 2), then {101, 5} (priority 5, smaller id first)
}
```


## 2. Prime Numbers

### Count Primes (Sieve of Eratosthenes)
> **Time Complexity:** $O(N \log \log N)$  
> **Space Complexity:** $O(N)$
> **Usage:** Efficiently counts large ranges of primes.

```cpp
#include <vector>
#include <algorithm>

using namespace std;

int countPrimes(int N) {
    if (N < 2) return 0;
    vector<bool> isPrime(N + 1, true);
    isPrime[0] = isPrime[1] = false;
    for (int i = 2; i * i <= N; ++i) {
        if (isPrime[i]) {
            for (int j = i * i; j <= N; j += i) {
                isPrime[j] = false;
            }
        }
    }
    return count(isPrime.begin(), isPrime.end(), true);
}
```

### Prime Number Test
> **Description:** Tests if a given 64-bit integer $n$ is prime using $O(\sqrt{n})$ trial division optimized with a $6k \pm 1$ rule.
> **Time Complexity:** $O(\sqrt{n})$  
> **Space Complexity:** $O(1)$
> **Usage:** Fast primality test for single large integers.

```cpp
bool isPrime(long long n) {
    if (n <= 1) return false;
    if (n <= 3) return true;
    if (n % 2 == 0 || n % 3 == 0) return false;
    for (long long i = 5; i * i <= n; i += 6) {
        if (n % i == 0 || n % (i + 2) == 0) {
            return false;
        }
    }
    return true;
}
```

### List Primes (Sieve of Eratosthenes)
> **Description:** Generates and returns a list of all prime numbers less than or equal to $N$.
> **Time Complexity:** $O(N \log \log N)$  
> **Space Complexity:** $O(N)$

```cpp
#include <vector>

using namespace std;

vector<int> sieve(int N) {
    vector<bool> isPrime(N + 1, true);
    isPrime[0] = isPrime[1] = false;
    for (int i = 2; i * i <= N; ++i) {
        if (isPrime[i]) {
            for (int j = i * i; j <= N; j += i) {
                isPrime[j] = false;
            }
        }
    }
    vector<int> primes;
    for (int i = 2; i <= N; ++i) {
        if (isPrime[i]) {
            primes.push_back(i);
        }
    }
    return primes;
}
```

### Prime Factorization
> **Description:** Performs prime factorization on a 64-bit integer $n$.
> **Time Complexity:** $O(\sqrt{n})$
> **Space Complexity:** $O(\log n)$ (number of unique prime factors)
> **Returns:** A list of pairs where each pair is `{base, exponent}` (representing the base prime and its exponent/power).

```cpp
#include <vector>
#include <utility>

using namespace std;

// Returns a vector of pairs: {prime_base, exponent}
vector<pair<long long, int>> primeFactorization(long long n) {
    vector<pair<long long, int>> factors;
    for (long long i = 2; i * i <= n; ++i) {
        if (n % i == 0) {
            int count = 0;
            while (n % i == 0) {
                count++;
                n /= i; // Eliminate the factor
            }
            factors.push_back({i, count});
        }
    }
    if (n > 1) {
        factors.push_back({n, 1}); // Remaining prime factor
    }
    return factors;
}
```

## 3. String Processing

### String Split (Regex)
> **Description:** Splits a string into substrings based on a regular expression pattern delimiter.
> **Parameters:**
> - `str`: The target string to split.
> - `keys`: Regex pattern specifying the delimiters (default matches dashes `-` or commas `,`).
> - `keep`: If `true`, retains the matched delimiters as separate tokens in the output.
> **Usage:** Ideal for parsing complex string inputs. Requires `<regex>`.

```cpp
#include <vector>
#include <string>
#include <regex>

using namespace std;

vector<string> split(const string& str, string keys = "(-{1,3}|,)", bool keep = false) {
    vector<string> tokens;
    regex re(keys);
    sregex_token_iterator it;
    if (keep) {
        it = sregex_token_iterator(str.begin(), str.end(), re, {-1, 0});
    } else {
        it = sregex_token_iterator(str.begin(), str.end(), re, -1);
    }
    sregex_token_iterator end;
    while (it != end) {
        tokens.push_back(*it);
        ++it;
    }
    return tokens;
}
```

### Knuth-Morris-Pratt (KMP) Substring Search
> **Description:** High-performance pattern searching algorithm. Precomputes the Longest Prefix Suffix (LPS) table to skip redundant character comparisons, eliminating the need to backtrack the text index.
> **Time Complexity:** $O(N + M)$ where $N$ is text length, $M$ is pattern length.
> **Space Complexity:** $O(M)$ for the LPS table.
> **Usage:** Fast substring matching when brute-force $O(N \cdot M)$ is too slow.

```cpp
#include <vector>
#include <string>

using namespace std;

// Build the Longest Prefix which is also a Suffix (LPS) table
vector<int> buildLPS(const string &pattern) {
    int m = pattern.size();
    vector<int> lps(m, 0);
    int len = 0; // Length of the previous longest prefix suffix
    for (int i = 1; i < m; ) {
        if (pattern[i] == pattern[len]) {
            len++;
            lps[i] = len;
            i++;
        } else {
            if (len != 0) {
                len = lps[len - 1]; // Backtrack in pattern
            } else {
                lps[i++] = 0;
            }
        }
    }
    return lps;
}

// KMP search main function: Returns starting indices of matches
vector<int> KMPsearch(const string &text, const string &pattern) {
    vector<int> result;
    int n = text.size(), m = pattern.size();
    if (m == 0) return result;
    vector<int> lps = buildLPS(pattern);
    int i = 0, j = 0; // i -> text index, j -> pattern index
    while (i < n) {
        if (text[i] == pattern[j]) {
            i++; j++;
            if (j == m) {
                result.push_back(i - j); // Found match at index (i - j)
                j = lps[j - 1];
            }
        } else {
            if (j != 0) {
                j = lps[j - 1];
            } else {
                i++;
            }
        }
    }
    return result;
}
```

## 4. Computational Geometry

### Closest Pair of Points (Divide & Conquer)
> **Description:** Finds the closest pair of points in a 2D Euclidean plane using a divide-and-conquer strategy.
> 1. **Sort:** Sort all points by X-coordinates.
> 2. **Divide & Solve:** Recursively find the minimum distance in left and right halves.
> 3. **Combine:** Check points within a vertical strip of width $2d$ around the midline. Sort them by Y-coordinates and compare each point with at most 6 subsequent points.
> **Time Complexity:** $O(N \log N)$  
> **Space Complexity:** $O(N)$
> **Usage:** Standard high-performance solution for closest-pair problems.

```cpp
#include <vector>
#include <cmath>
#include <algorithm>
#include <limits>
#include <utility>

using namespace std;

struct Point {
    double x, y;
};

// Calculate Euclidean distance between two points
double dist(const Point& a, const Point& b) {
    return hypot(a.x - b.x, a.y - b.y);
}

// Brute force method for 3 or fewer points
double bruteForce(vector<Point>& points, int left, int right, pair<Point, Point>& bestPair) {
    double minDist = numeric_limits<double>::max();
    for (int i = left; i <= right; ++i) {
        for (int j = i + 1; j <= right; ++j) {
            double d = dist(points[i], points[j]);
            if (d < minDist) {
                minDist = d;
                bestPair = {points[i], points[j]};
            }
        }
    }
    return minDist;
}

// Check the strip area for closer points crossing the divide boundary
double stripClosest(vector<Point>& strip, double d, pair<Point, Point>& bestPair) {
    double minDist = d;
    sort(strip.begin(), strip.end(), [](const Point& a, const Point& b) {
        return a.y < b.y;
    });
    for (size_t i = 0; i < strip.size(); ++i) {
        // Compare each point with next points inside the strip
        for (size_t j = i + 1; j < strip.size() && (strip[j].y - strip[i].y) < minDist; ++j) {
            double newDist = dist(strip[i], strip[j]);
            if (newDist < minDist) {
                minDist = newDist;
                bestPair = {strip[i], strip[j]};
            }
        }
    }
    return minDist;
}

// Recursive Divide & Conquer helper utility
double closestUtil(vector<Point>& pointsSortedByX, int left, int right, pair<Point, Point>& bestPair) {
    // Use brute force if points count is 3 or less
    if (right - left <= 3) {
        return bruteForce(pointsSortedByX, left, right, bestPair);
    }
    int mid = (left + right) / 2;
    Point midPoint = pointsSortedByX[mid];
    pair<Point, Point> leftPair, rightPair;
    double dl = closestUtil(pointsSortedByX, left, mid, leftPair);
    double dr = closestUtil(pointsSortedByX, mid + 1, right, rightPair);
    double d = dl;
    bestPair = leftPair;
    if (dr < dl) {
        d = dr;
        bestPair = rightPair;
    }
    // Gather points inside vertical strip of width 2d
    vector<Point> strip;
    for (int i = left; i <= right; ++i) {
        if (abs(pointsSortedByX[i].x - midPoint.x) < d) {
            strip.push_back(pointsSortedByX[i]);
        }
    }
    pair<Point, Point> stripPair;
    double dStrip = stripClosest(strip, d, stripPair);
    if (dStrip < d) {
        d = dStrip;
        bestPair = stripPair;
    }
    return d;
}

// Main entry function to find the closest pair of points
pair<Point, Point> findClosestPair(vector<Point>& points) {
    vector<Point> pointsSortedByX = points;
    sort(pointsSortedByX.begin(), pointsSortedByX.end(), [](const Point& a, const Point& b) {
        return a.x < b.x;
    });
    pair<Point, Point> bestPair;
    closestUtil(pointsSortedByX, 0, pointsSortedByX.size() - 1, bestPair);
    return bestPair;
}
```

## 5. Permutation Operations

### Next Permutation (Array-based)
> **Description:** Rearranges the array elements into the lexicographically next greater permutation of elements. If no such permutation exists (i.e., the array is sorted in descending order), it rearranges it into the lowest possible order (sorted in ascending order).
> **Time Complexity:** $O(N)$  
> **Space Complexity:** $O(1)$ auxiliary space
> **Usage:** Finds the next permutation of a vector/array in-place or returns a copy of the next permutation.

```cpp
#include <vector>
#include <algorithm>

using namespace std;

// Returns the lexicographically next permutation of the array.
vector<int> nextPermutation(vector<int> nums) {
    int n = nums.size();
    int i = n - 2;
    // Find the first decreasing element from the right
    while (i >= 0 && nums[i] >= nums[i + 1]) {
        i--;
    }
    if (i >= 0) {
        // Find the successor to nums[i] from the right
        int j = n - 1;
        while (nums[j] <= nums[i]) {
            j--;
        }
        swap(nums[i], nums[j]);
    }
    // Reverse the remaining ascending suffix
    reverse(nums.begin() + i + 1, nums.end());
    return nums;
}
```

### Permutation Rank (Lexicographical Rank)
> **Description:** Calculates the 1-based lexicographical rank (index) of a permutation. This implementation handles generic values by using coordinate compression, uses a Fenwick Tree (Binary Indexed Tree) for $O(N \log N)$ complexity, and supports modulo arithmetic to prevent integer overflow for larger arrays.
> **Time Complexity:** $O(N \log N)$  
> **Space Complexity:** $O(N)$
> **Usage:** Given a permutation of unique elements, returns its 1-based rank modulo $10^9 + 7$.

```cpp
#include <vector>
#include <algorithm>

using namespace std;

// Binary Indexed Tree (Fenwick Tree) helper
struct FenwickTree {
    int n;
    vector<int> tree;
    FenwickTree(int n) : n(n), tree(n + 1, 0) {}
    void update(int i, int delta) {
        for (; i <= n; i += i & -i) tree[i] += delta;
    }
    int query(int i) {
        int sum = 0;
        for (; i > 0; i -= i & -i) sum += tree[i];
        return sum;
    }
};

// Calculates the 1-based lexicographical rank of a permutation.
// Assumes unique elements. Returns rank modulo MOD (default is 1e9 + 7).
long long getPermutationRank(vector<int> nums, long long MOD = 1000000007) {
    int n = nums.size();
    if (n == 0) return 1;

    // Precompute factorials modulo MOD
    vector<long long> fact(n, 1);
    for (int i = 1; i < n; ++i) {
        fact[i] = (fact[i - 1] * i) % MOD;
    }

    // Coordinate compression to map values to [1, n]
    vector<int> temp = nums;
    sort(temp.begin(), temp.end());
    vector<int> compressed(n);
    for (int i = 0; i < n; ++i) {
        compressed[i] = lower_bound(temp.begin(), temp.end(), nums[i]) - temp.begin() + 1;
    }

    FenwickTree bit(n);
    for (int i = 1; i <= n; ++i) {
        bit.update(i, 1);
    }

    long long rank = 1;
    for (int i = 0; i < n; ++i) {
        // Count how many unused elements to the right are smaller than compressed[i]
        int smallerCount = bit.query(compressed[i] - 1);
        rank = (rank + smallerCount * fact[n - 1 - i]) % MOD;
        // Mark current element as used
        bit.update(compressed[i], -1);
    }
    return rank;
}
```

## 6. Math

### Euler Totient Function
> **Description:** Calculates Euler's totient function $\phi(x)$, which counts the number of positive integers strictly less than $x$ that are coprime to $x$.
> **Time Complexity:** $O(\sqrt{x})$  
> **Space Complexity:** $O(1)$
> **Usage:** Commonly used in number theory, particularly for finding modular multiplicative inverses and applying Euler's theorem.

```cpp
#include <cmath>

int Phi(int x) {
    if (x < 2) return 0;
    int ret = x;
    int sq = sqrt(x);
    for (int p = 2; p <= sq; p++) {
        if (x % p == 0) {
            while (x % p == 0) x /= p;
            ret -= ret / p;
        }
        if (x == 1) break;
    }
    if (x > 1) ret -= ret / x;
    return ret;
}
```

## 7. Range Queries

### Difference Array
> **Description:** Efficiently applies multiple range addition updates $[L, R]$ by modifying only the boundaries of a difference array. A prefix sum is then used to reconstruct the final array values.
> **Returns:** The minimum coverage value across all positions after applying all updates.
> **Time Complexity:** $O(M + N)$ where $M$ is the number of updates and $N$ is the array size.
> **Space Complexity:** $O(N)$ for the difference array.
> **Usage:** Ideal for scenarios requiring many range updates followed by a single full-array query or state evaluation.

```cpp
#include <iostream>
#include <vector>
#include <climits>

using namespace std;

long long differenceArray() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    long long N, M;
    if (!(cin >> N >> M)) return -1;
    
    // Size N+2 to prevent R+1 from exceeding boundary
    vector<long long> diff(N + 2, 0LL);
    
    // Read interval [L, R] for each turret
    for (int i = 0; i < M; i++) {
        long long L, R;
        cin >> L >> R;
        // Difference array update: +1 at start L, -1 at position after end R+1
        diff[L] += 1;
        if (R + 1 <= N) {
            diff[R + 1] -= 1;
        }
    }
    
    // Compute prefix-sum directly on diff array and track the minimum value
    long long cover = 0;          // Current cumulative coverage at the current position
    long long answer = LLONG_MAX; // Stores the minimum coverage value
    
    for (int pos = 1; pos <= N; pos++) {
        cover += diff[pos]; // diff[pos] indicates the change in coverage at pos
        // cover represents the actual coverage count at pos
        if (cover < answer) {
            answer = cover;
        }
    }
    
    // Under normal circumstances, answer is at least 0.
    // Return the answer.
    return answer;
}
```

### Fenwick Tree (Binary Indexed Tree)
**Ddescription:** Support Point Update, Prefix Sum Query, Range Sum Query
**Complexity:** 
Update : O(logN), Query  : O(logN), Memory : O(N)
```cpp
struct BIT {
    int n;
    vector<long long> bit;

    BIT(int _n) {
        n = _n;
        bit.assign(n + 1, 0);
    }

    void add(int idx, long long val) {
        while (idx <= n) {
            bit[idx] += val;
            idx += idx & -idx;
        }
    }

    long long sum(int idx) {
        long long res = 0;

        while (idx > 0) {
            res += bit[idx];
            idx -= idx & -idx;
        }

        return res;
    }

    long long query(int l, int r) {
        return sum(r) - sum(l - 1);
    }
};
```
**Note:** 
- `lowbits[i]` 表示第 `i` 格所管理的區間大小
```cpp
lowbit(x) = x & -x
```

`tree[i]` 儲存的是第 `i` 格所管理的區間大小的 sum

## 8. Graph Algorithms

### Minimum Spanning Tree (Kruskal's Algorithm)
> **Description:** Finds the Minimum Spanning Tree (MST) of a connected, undirected, weighted graph. It uses the greedy approach: sorts all edges in non-decreasing order of their weight, and adds edges one by one if they don't form a cycle. A Disjoint Set Union (DSU) structure is used to check and manage cycles efficiently.
> **Time Complexity:** $O(E \log E + E \alpha(V))$ where $E$ is the number of edges, $V$ is the number of vertices, and $\alpha$ is the Inverse Ackermann function.
> **Space Complexity:** $O(V)$ for the DSU parent and rank arrays.
> **Usage:** Suitable for sparse graphs. Supports 1-indexed vertices by default.

```cpp
#include <vector>
#include <algorithm>

using namespace std;

// Representation of an edge in the graph
struct Edge {
    int u, v;    // u, v: The two endpoint vertices (nodes) connected by this edge
    long long w; // w: The weight (cost/distance) of this edge
    // Overload the < operator to sort edges by weight
    bool operator<(const Edge& other) const {
        return w < other.w;
    }
};

// Disjoint Set Union (DSU) / Union-Find structure
struct DSU {
    vector<int> parent; // parent[i] stores the parent/leader of element i
    vector<int> rank;   // rank[i] stores the approximate depth of the tree rooted at i (for Union by Rank optimization)
    
    DSU(int n) { // n: The number of vertices/elements in the graph
        parent.resize(n + 1);
        rank.resize(n + 1, 0);
        for (int i = 0; i <= n; i++) {
            parent[i] = i; // Initially, every vertex is its own parent (disjoint set)
        }
    }
    
    // Finds the representative (root leader) of the set containing element i
    int find(int i) {
        if (parent[i] == i)
            return i;
        return parent[i] = find(parent[i]); // Path compression optimization
    }
    
    // Merges the set containing element i with the set containing element j.
    // Returns true if they were in different sets and successfully merged; false if they were already in the same set.
    bool unite(int i, int j) {
        int root_i = find(i);
        int root_j = find(j);
        if (root_i != root_j) {
            // Union by rank optimization: attach the smaller tree under the larger tree
            if (rank[root_i] < rank[root_j]) {
                parent[root_i] = root_j;
            } else if (rank[root_i] > rank[root_j]) {
                parent[root_j] = root_i;
            } else {
                parent[root_j] = root_i;
                rank[root_i]++;
            }
            return true; // Successfully merged
        }
        return false; // Already in the same set (connecting them would form a cycle)
    }
};

// Computes the MST weight and optionally stores the selected edges.
// n: Number of vertices (nodes) in the graph.
// edges: Input vector containing all edges in the graph (will be sorted in-place).
// mstEdges: Output vector to store the selected edges that form the MST.
// Returns the total MST weight, or -1 if the graph is disconnected (no MST exists).
long long kruskal(int n, vector<Edge>& edges, vector<Edge>& mstEdges) {
    mstEdges.clear();
    sort(edges.begin(), edges.end()); // Sort edges in ascending order of weight (greedy approach)
    
    DSU dsu(n); // Disjoint Set Union to detect cycles
    long long mst_weight = 0; // Accumulator for the total weight of the MST
    int edges_used = 0; // Counter for the number of edges added to the MST
    
    for (const auto& edge : edges) {
        // Try to connect the two endpoints of the current edge (edge.u and edge.v)
        if (dsu.unite(edge.u, edge.v)) {
            mst_weight += edge.w;
            mstEdges.push_back(edge);
            edges_used++;
            // A tree with n vertices always has exactly n - 1 edges
            if (edges_used == n - 1) {
                break;
            }
        }
    }
    
    if (edges_used == n - 1) {
        return mst_weight;
    }
    return -1; // Graph is not fully connected (could not select n - 1 edges)
}
```
