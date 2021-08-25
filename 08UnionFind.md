##
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