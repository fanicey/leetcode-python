# Basics

## Disjoint Set

### What problem to be solved?

Given a graph (vertices, edges), how to decide if two nodes are connected?

We can easily find this answer by using a disjoint set data structure, also known as "union-find" data structure/algorithm.

### Algorithm

The logic: Each node points to its parent node. A node is a root node if it points to itself. Two nodes are connected if they have the same root node.

Two basic operations:

* `find(x)`: return the root node of x
* `union(x,y)` link x and y

**Path Compression Optimization**

```
class Union-Find:
    def __init__(self, size):
        self.roots = [i for i in range(size)]
    def find(self, x):
        if self.roots[x]!=x:    # <-- do not use while here.
            self.roots[x] = self.find(roots[x])
        return self.roots[x]
    def union(self, x, y):
        x_p = self.find(x)
        y_p = self.find(y)
        self.roots[x_p] = y_p
```

### When to use Union-Find?

Given a list of edges, not sorted.

Need to check connectivities of nodes

DFS, 

## Problems

### 261. Graph Valid Tree (Medium)

You have a graph of `n` nodes labeled from `0` to `n - 1`. You are given an integer n and a list of `edges` where `edges[i] = [ai, bi]` indicates that there is an undirected edge between nodes `ai` and `bi` in the graph.

Return `true` *if the edges of the given graph make up a valid tree, and* `false` *otherwise*.

Solution: This problem can be solved in many ways: DFS, BFS, union-find. Below uses the union-find algorithm. The important thing is to understand what is a valid tree: (1) no cycle (2) all nodes are connected.

```
class Solution:
    def validTree(self, n: int, edges: List[List[int]]) -> bool:
        if len(edges)!=n-1:
            return False
        # valid tree: after scanning all edges, all nodes have the same roots
        
        roots = [i for i in range(n)]
        
        def find(x):
            if roots[x] != x:
                roots[x] = find(roots[x])
            return roots[x]
        def union(x, y):
            rootX = find(x)
            rootY = find(y)
            roots[rootY] = rootX
        def isConnected(x,y):
            return find(x) == find(y)
        
        
        for edge in edges:
            if isConnected(*edge):
                return False
            union(*edge)
        
        root = find(0)
        for node in range(1, n):
            if find(node) != root:
                return False
        return True
```



### 323. Number of Connected Components in an Undirected Graph

Medium

You have a graph of `n` nodes. You are given an integer `n` and an array `edges` where `edges[i] = [ai, bi]` indicates that there is an edge between `ai` and `bi` in the graph.

Return *the number of connected components in the graph*.

Solution: Very similar to the previous problem. The only difference is the main function part:

```
count = n
for edge in edges:
    if not isConnected(*edge):
        count -= 1 
    union(*edge)
return count
```

Other similar problems:

1101 The Earliest Moment When Everyone Become Friends (medium)

### 1202. Smallest String With Swaps (Medium)

You are given a string `s`, and an array of pairs of indices in the string `pairs` where `pairs[i] = [a, b]` indicates 2 indices(0-indexed) of the string.

You can swap the characters at any pair of indices in the given `pairs` **any number of times**.

Return the lexicographically smallest string that `s` can be changed to after using the swaps.

Solution: The hard part of this problem is to understand the property: we can get any permutation of the vertices within a connected component.

```
class Solution:
    def smallestStringWithSwaps(self, s: str, pairs: List[List[int]]) -> str:
        # using the pairs we can obtain a permutation graph
        # Find all the connected components in the graph (disjoint sets)
        # Then sort within each components.
        n = len(s)
        roots = [i for i in range(n)]
        
        def find(x):
            if roots[x] != x:
                roots[x] = find(roots[x])
            return roots[x]
        def union(x, y):
            rootX = find(x)
            rootY = find(y)
            roots[rootY] = rootX
        def isConnected(x,y):
            return find(x) == find(y)
        
        for pair in pairs:
            union(*pair)
        components = defaultdict(list)
        for x in range(n):
            r = find(x)
            components[r].append(x)
        
        smallestString = ['']*n
        for r in components:
            chars = [ s[i] for i in components[r]]
            chars.sort()
            for index in range(len(chars)):
                smallestString[components[r][index]] = chars[index]
        return ''.join(smallestString)
```

### 399. Evaluate Division (Medium)

You are given an array of variable pairs `equations` and an array of real numbers `values`, where `equations[i] = [Ai, Bi]` and `values[i]` represent the equation `Ai / Bi = values[i]`. Each `Ai` or `Bi` is a string that represents a single variable.

You are also given some `queries`, where `queries[j] = [Cj, Dj]` represents the `jth` query where you must find the answer for `Cj / Dj = ?`.

Return *the answers to all queries*. If a single answer cannot be determined, return `-1.0`.

**Note:** The input is always valid. You may assume that evaluating the queries will not result in division by zero and that there is no contradiction.

Solution: DFS will not be an efficient way because we need to search for path for every query. 

The solution below uses union-find + weighted graph. Pay attention to the line with the comment. I made a mistake here in first trial because if the query = [x, x], where x is not seen before, we want to output -1, not 1.

```
class Solution:
    def calcEquation(self, equations: List[List[str]], values: List[float], queries: List[List[str]]) -> List[float]:
        # construct the disjoint set
        graph_weights = defaultdict(tuple)
        def find(x):
            if x not in graph_weights:
                graph_weights[x] = (x, 1)
            elif graph_weights[x][0] != x:          
                parent = find(graph_weights[x][0])
                graph_weights[x] = (parent[0], graph_weights[x][1]*parent[1])
            return graph_weights[x]
        def union(x, y, weight): # weight = x/y
            p_x = find(x)
            p_y = find(y)
            if p_x[0] != p_y[0]:
                graph_weights[p_x[0]] = (p_y[0], weight*graph_weights[y][1]/graph_weights[x][1])
        
        for i,eq in enumerate(equations):
            union(eq[0], eq[1], values[i])
        
        outputs = []
        for query in queries:
            if query[0] in graph_weights and query[1] in graph_weights: # <-- pay attention
                p_1 = find(query[0])
                p_2 = find(query[1])
                if p_1[0] == p_2[0]:
                    outputs.append(p_1[1]/p_2[1])
                else:
                    outputs.append(-1.0)
            else:
                outputs.append(-1.0)
        return outputs
```



### 684. Redundant Connection
In this problem, a tree is an undirected graph that is connected and has no cycles.

The given input is a graph that started as a tree with N nodes (with distinct values 1, 2, ..., N), with one additional edge added. The added edge has two different vertices chosen from 1 to N, and was not an edge that already existed.

The resulting graph is given as a 2D-array of edges. Each element of edges is a pair [u, v] with u < v, that represents an undirected edge connecting nodes u and v.

Return an edge that can be removed so that the resulting graph is a tree of N nodes. If there are multiple answers, return the answer that occurs last in the given 2D-array. The answer edge [u, v] should be in the same format, with u < v.

```
Example 1:

Input: [[1,2], [1,3], [2,3]]
Output: [2,3]
Explanation: The given undirected graph will be like this:
  1
 / \
2 - 3
```
#### Solution 1. Union-Find
This problem is a perfect application of union-find. This algorithm is much faster than Solution 2.
```
class Solution:
    def findRedundantConnection(self, edges: List[List[int]]) -> List[int]:
        group_id = {}
        # find
        def root(x):
            if x not in group_id:
                group_id[x] = x
            if group_id[x] == x:
                return x
            group_id[x] = root(group_id[x])
            return group_id[x]
        
        for u,v in edges:
                if root(u) == root(v):
                    return [u,v]
                else:
                    # union
                    group_id[root(v)] = root(u)
        return None
```
Complexity Analysis

Time Complexity: 
O(Nα(N))≈O(N), where 
N is the number of vertices (and also the number of edges) in the graph, and 
α is the Inverse-Ackermann function. We make up to 
N queries of dsu.union, which takes (amortized) 
O(α(N)) time. Outside the scope of this article, it can be shown why dsu.union has O(α(N)) complexity, what the Inverse-Ackermann function is, and why 
O(α(N)) is approximately O(1).

Space Complexity: 
O(N). The current construction of the graph (embedded in our dsu structure) has at most N nodes.
#### Solution 2. DFS
1. Use a dict {k: (neighbors of k)} to model the graph
2. For every edge, use DFS to check if there is a path between them 
3. If not, add this edge into the graph
```
class Solution:
    def findRedundantConnection(self, edges: List[List[int]]) -> List[int]:
        from collections import defaultdict
        graph = defaultdict(set)
        
        def dfs(source, target):
            # check if source and target on the seem path
            if source == target or target in graph[source]:
                return True
            if source in seen:
                return False
            seen.add(source)
            for nei in graph[source]:
                if dfs(nei, target):
                    return True
            return False
                      
        for u,v in edges:
            # to record path
            seen = set()
            if u in graph and v in graph and dfs(u,v):
                return u,v
            graph[u].add(v)
            graph[v].add(u)
```
Complexity Analysis

Time Complexity: 
O(N^2) where 
N is the number of vertices (and also the number of edges) in the graph. In the worst case, for every edge we include, we have to search every previously-occurring edge of the graph.

Space Complexity: 
O(N). The current construction of the graph has at most 
N nodes.

### 547. Friend Circles
There are N students in a class. Some of them are friends, while some are not. Their friendship is transitive in nature. For example, if A is a direct friend of B, and B is a direct friend of C, then A is an indirect friend of C. And we defined a friend circle is a group of students who are direct or indirect friends.

Given a N*N matrix M representing the friend relationship between students in the class. If M[i][j] = 1, then the ith and jth students are direct friends with each other, otherwise not. And you have to output the total number of friend circles among all the students.

```
Example 1:

Input: 
[[1,1,0],
 [1,1,0],
 [0,0,1]]
Output: 2
Explanation:The 0th and 1st students are direct friends, so they are in a friend circle. 
The 2nd student himself is in a friend circle. So return 2.
```
#### Solution 1. DFS
```
class Solution:
    def findCircleNum(self, M: List[List[int]]) -> int:
        # DFS
        numStudents = len(M)
        numCircle = 0
        visited = [False]*numStudents
        def dfs(student):
            if visited[student]:
                return
            visited[student] = True
            for j in range(numStudents):
                if M[student][j] == 1:
                    dfs(j)
                    
        for i in range(numStudents):
            if not visited[i]:
                numCircle += 1
                dfs(i)
        return numCircle
```

### Solution. Union-Find
```
class Solution:
    def findCircleNum(self, M: List[List[int]]) -> int:
        # union-find
        numStudents = len(M)
        root = [i for i in range(numStudents)]
        
        def find(student):
            # update and return root of the student group
            if root[student] == student: return student
            root[student]=find(root[student])
            return root[student]
        def union(s1, s2):
            r1 = find(s1)
            r2 = find(s2)
            root[r1] = r2
        
        for i in range(numStudents):
            for j in range(i+1, numStudents):
                if M[i][j] == 1:
                    union(i,j)
        for i in range(numStudents):
            find(i)
        return len(set(root))
```