# C++ Competitive Programming Codebook

This codebook contains commonly used algorithms and utilities optimized for competitive programming.

---

## 1. Basic Utilities

### IO Speedup (cin, cout)
> Accelerates C++ standard input/output operations. Avoid mixing `cin/cout` with `scanf/printf` after execution.

```cpp
#include <iostream>

using namespace std;

void speedup() {
    cin.sync_with_stdio(0);
    cin.tie(0);
}
```

### Greatest Common Divisor (GCD)
> Calculates the greatest common divisor of two integers using the Euclidean algorithm.

```cpp
int GCD(int num1, int num2) {
    if (num2 == 0) return num1;
    return GCD(num2, num1 % num2);
}
```

### Least Common Multiple (LCM)
> Calculates the least common multiple of two integers.

```cpp
int LCM(int num1, int num2) {
    return ((num1 * num2) / GCD(num1, num2));
}
```

---

## 2. Prime Numbers

### Count Primes (Sieve of Eratosthenes)
> **Description:** Counts the number of prime numbers strictly less than or equal to $N$ using the classical Sieve of Eratosthenes.
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

---

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

---

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

## 5. Math

### Euler Totient Function (歐拉函數)
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

## 6. Range Queries

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
