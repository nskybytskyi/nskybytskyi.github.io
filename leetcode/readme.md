- [213. House Robber II](#213-house-robber-ii)
- [216. Combination Sum III](#216-combination-sum-iii)
- [223. Rectangle Area](#223-rectangle-area)
- [310. Minimum Height Trees](#310-minimum-height-trees)
- [319. Bulb Switcher](#319-bulb-switcher)

<a id="213-house-robber-ii"></a>
# 213. House Robber II

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed. All houses at this place are **arranged in a circle**. That means the first house is the neighbor of the last one. Meanwhile, adjacent houses have security system connected and **it will automatically contact the police if two adjacent houses were broken into on the same night**.

Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight **without alerting the police**.

**Example 1:**
```
Input: [2,3,2]
Output: 3
Explanation: You cannot rob house 1 (money = 2) and then rob house 3 (money = 2),
             because they are adjacent houses.
```
**Example 2:**
```
Input: [1,2,3,1]
Output: 4
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
             Total amount you can rob = 1 + 3 = 4.
```

<details>
    <h2><summary>Solution</summary></h2>

Note that in an optimal subsets of houses which we can rob there won't be three consecutive not robbed houses.  Otherwise, we could've robbed the middle of them and increase our profit.  At this point, the problem sounds something like fibonacci steps, i.e. pass one house or pass two houses.  Fibonacci should remaind us of the dynamic programming.

Indeed, we can already see most of the solution: maintain two `dp` arrayy with `dp[0][i]` equalling the max profit we can make out of first _i_ houses if we **don't** rob the house _i_, and `dp[1][i]` equalling the max profit we can make out of first _i_ houses if we **do** rob the house _i_.

There is, however, one tricky question about the boundaries: we cannot rob the first and the last house simultaneously.  I suggest dealing with this by running the initial dp on `nums[1:]` and `nums[:-1]` and then taking the best of two results.  In such a way we manually ensure that at least one of the boundary houses is not robbed.  This will work in all cases except for length 1 and length 0 arrays.

### Complexity

Both time and space complexity is _O(n)_, as we have _O(n)_ dp states with _O(1)_ transitions each.  Space complexity can be reduced to _O(1)_ if we maintain only a sliding window of three dp elements and carefully avoid copying the input array.

### Code

```python
class Solution:
    def rob(self, nums: List[int]) -> int:
        if not nums:
            return 0
        if len(nums) == 1:
            return nums[0]
        
        def dpRob(a: List[int]) -> int:
            # start with two zeros to avoid casework for small `i`
            dp = [[0 for _ in range(len(a) + 2)] for _ in range(2)]
            for i, ai in enumerate(a):
                dp[1][i + 2] = ai + max(dp[1][i], dp[0][i + 1])
                dp[0][i + 2] = max(dp[0][i + 1], dp[1][i + 1])
            return max(dp[0][-1], dp[1][-1])
            
        return max(dpRob(nums[1:]), dpRob(nums[:-1]))

```

</details>

<a id="216-combination-sum-iii"></a>
# 216. Combination Sum III

Find all possible combinations of _k_ numbers that add up to a number _n_, given that only numbers from _1_ to _9_ can be used and each combination should be a unique set of numbers.

**Note:**

- All numbers will be positive integers.
- The solution set must not contain duplicate combinations.

**Example 1:**
```
Input: k = 3, n = 7
Output: [[1,2,4]]
```
**Example 2:**
```
Input: k = 3, n = 9
Output: [[1,2,6], [1,3,5], [2,3,4]]
```

<details>
    <h2><summary>Solution</summary></h2>

We can use a recursive approach if we suppose that we take digits in descending order and add another (optional) parameter _d_ to our function, indicating the largest digit which we still can use.  The logic behind is the following: let's try to construct an answer starting with some digit _c_.  Then we'll be left with the original problem with _k_ replaced by _k - 1_, _n_ replaced by _n - c_, and _d_ replaced by _c - 1_.

### Complexity

There is a limited number of possible answers, namely _2<sup>9</sup>_.  Our solution manipulates at most that many lists of a limited length of _9_.  Therefore, the complexity is constant _O(1)_.

The interesting question is whether one can do any better if we allow summand bigger than _9_.  At the first glance it looks like the answer size can be exponential in terms of _n_ if we do not limit the summand at all.  

### Code

```python
class Solution:
    def combinationSum3(self, k: int, n: int, d: int=9) -> List[List[int]]:
        if k == 1:
            if 1 <= n <= d:
                return [[n]]
            else:
                return []
        answers = []
        for c in range(1, d + 1):
            # can be improved to avoid unnecessary copy if we construct `partial_answer` inplace
            for partial_answer in self.combinationSum3(k - 1, n - c, c - 1):
                partial_answer.append(c)
                answers.append(partial_answer)
        return answers

```

</details>

<a id="223-rectangle-area"></a>
# 223. Rectangle Area

Find the total area covered by two **rectilinear** rectangles in a **2D** plane.

Each rectangle is defined by its bottom left corner and top right corner as shown in the figure.

![Rectangle Area](rectangle_area.png)

**Example:**
```
Input: A = -3, B = 0, C = 3, D = 4, E = 0, F = -1, G = 9, H = 2
Output: 45
```

**Note:**

Assume that the total area is never beyond the maximum possible value of **int**.

<details>
    <h2><summary>Solution</summary></h2>

It's an easy geometry task, complicated by rather messy input format.  We are going to use inclusion/exclusion formula.  It means that we have to find the intersection area of the rectangles.  It can be easily found if you recall that a rectangle is an intersection of four half-planes and notice that some of these half-planes include others.  This observation allows us to calculate the potential vertices of the intersection. 

The only tricky point to watch out for is when the intersection is empty.  We address this by max-relaxation of the side lengths with zero.

### Complexity

_O(1)_ ofc.

### Code

```python
class Solution:
    def computeArea(self, a: int, b: int, c: int, d: int, e: int, f: int, g: int, h: int) -> int:
        def area(a: int, b: int, c: int, d: int) -> int:
            return max(c - a, 0) * max(0, d - b)  # note the max-relaxation with 0

        return area(a, b, c, d) + area(e, f, g, h) - area(max(a, e), max(b, f), min(c, g), min(d, h))

```

</details>

<a id="310-minimum-height-trees"></a>
# 310. Minimum Height Trees

For an undirected graph with tree characteristics, we can choose any node as the root. The result graph is then a rooted tree. Among all possible rooted trees, those with minimum height are called minimum height trees (MHTs). Given such a graph, write a function to find all the MHTs and return a list of their root labels.

**Format**
The graph contains `n` nodes which are labeled from `0` to `n - 1`. You will be given the number `n` and a list of undirected edges (each edge is a pair of labels).

You can assume that no duplicate edges will appear in `edges`. Since all edges are undirected, `[0, 1]` is the same as `[1, 0]` and thus will not appear together in edges.

**Example 1:**
```
Input: n = 4, edges = [[1, 0], [1, 2], [1, 3]]

        0
        |
        1
       / \
      2   3 

Output: [1]
```
**Example 2:**
```
Input: n = 6, edges = [[0, 3], [1, 3], [2, 3], [4, 3], [5, 4]]

     0  1  2
      \ | /
        3
        |
        4
        |
        5 

Output: [3, 4]
```
**Note:**

- According to the definition of tree on Wikipedia: “a tree is an undirected graph in which any two vertices are connected by exactly one path. In other words, any connected graph without simple cycles is a tree.”
- The height of a rooted tree is the number of edges on the longest downward path between the root and a leaf.

<details>
    <h2><summary>Solution</summary></h2>

A tree can have up to two centers (roots that lead to MHTs).  One can verify this claim by supposing the contrary, considering three centers and corresponding longest paths.  If you draw a picture, it should be clear how to construct a longer path for one of the centers, violating the assumption.

Any center is literally a center of any diameter.  This proposition can be proven in a similar fashion.  It remains to recall the [classical two-bfs algorithm](https://medium.com/@tbadr/tree-diameter-why-does-two-bfs-solution-work-b17ed71d2881) for finding a diameter of a tree.

The only remark still to be made is that we have to store the bfs-parents in the second bfs to effectively retrieve the diamter itself, not only the length.

### Complexity

_O(n)_, as we use a constant number of BFSs, and every one of them costs _O(V + E)_ which is _O(n)_ for the tree case.

### Code

Rather long this time.

```python
from collections import deque


class Solution:
    def findMinHeightTrees(self, n: int, edges: List[List[int]]) -> List[int]:
        graph = [[] for _ in range(n)]
        for src, dst in edges:
            graph[src].append(dst)
            graph[dst].append(src)
            
        # ofc in prod code there should be a template bfs with visitor
        visited = [False for _ in range(n)]
        bfs = deque([0])
        first_endpoint = -1
        while bfs:
            v = bfs.popleft()
            visited[v] = True
            for w in graph[v]:
                if not visited[w]:
                    bfs.append(w)
            if not bfs:
                first_endpoint = v

        # but you won't implement it at an hour-long interview, will you?
        distance = [-1 for _ in range(n)]
        parent = [-1 for _ in range(n)]
        bfs = deque([first_endpoint])
        distance[first_endpoint] = 0
        second_endpoint = -1
        while bfs:
            v = bfs.popleft()
            for w in graph[v]:
                if distance[w] == -1:
                    bfs.append(w)
                    parent[w] = v
                    distance[w] = distance[v] + 1
            if not bfs:
                second_endpoint = v
        
        diameter_length = distance[second_endpoint]
        if diameter_length & 1:
            for k in range(diameter_length >> 1):
                second_endpoint = parent[second_endpoint]
            return [second_endpoint, parent[second_endpoint]]
        else:
            for k in range(diameter_length >> 1):
                second_endpoint = parent[second_endpoint]
            return [second_endpoint]

```

</details>

<a id="319-bulb-switcher"></a>
# 319. Bulb Switcher

There are _n_ bulbs that are initially off. You first turn on all the bulbs. Then, you turn off every second bulb. On the third round, you toggle every third bulb (turning on if it's off or turning off if it's on). For the _i<sup>th</sup>_ round, you toggle every _i<sup>th</sup>_ bulb. For the _n<sup>th</sup>_ round, you only toggle the last bulb. Find how many bulbs are on after n rounds.

**Example:**
```
Input: 3
Output: 1 
Explanation: 
At first, the three bulbs are [off, off, off].
After first round, the three bulbs are [on, on, on].
After second round, the three bulbs are [on, off, on].
After third round, the three bulbs are [on, off, off]. 

So you should return 1, because there is only one bulb is on.
```

## Not a Solution

We are going to simulate the process.

It is clear that we will make _n + n/2 + n/3 + ... + n/n_ switches.  But how do you calculate this sum?  Well, one can recognize the integral from _1_ to _n_ of _n/x_, which is _O(n log n)_.  Should work for small inputs.  However, it will TLE on the values of _n_ about a million.

<details>
    <h2><summary>Solution</summary></h2>

While this may not sound like a number theory question, it actually is.

The trick is that we can tell exactly which bulbs will be turned on.  In fact, these will be squares (1, 4, 9, 16, &hellip;).  If you wonder why&mdash;count how many times some bulb _k_ is switched.  It turns out that bulb number _k_ is switched as many times as many divisors _k_ has.  From the prime decomposition it follow that we will change an odd number of times only the lightbulbs, all prime exponents of which are even, a.k.a. the squares.  It remains to note that there are _[√n]_ squares up to _n_.

### Complexity

_O(1)_ ofc.

### Code

```python
class Solution:
    def bulbSwitch(self, n: int) -> int:
        return int(sqrt(n))

```

</details>
