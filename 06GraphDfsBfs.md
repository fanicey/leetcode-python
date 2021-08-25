# Graph
Representation:
1. adjacency-list: good for sparse graphs
2. adjacency-matrix: good for dense graphs
# Problems
## DFS
### Template
Graph nodes can be {white, gray, black}
* white: have not been visited
* gray: visited, but have not finish DFS
* black: visited, and finished DFS

DFS can be implemented recursively, or iteratively by using a stack. Recursive is elegant, but for languages like python, iteration is preferable because of the speed issue.
```
class Solution:
    #def cloneGraph(self, node: 'Node') -> 'Node':
    def dfs(graph):
        # graph node color
        color = [0]*numNodes
        for node in graph:
            if color[node] == 0:
                dfs_visit(node)
                
    def dfs_visit(node):
        color[node] = -1
        for next in node_connected:
            if color[next] == 0:
                dfs_visit(next)
        color[node] = 1

```
### Topological sort
A **topological sort** or **topological ordering** of a directed graph is a linear ordering of its vertices such that all edges point to one direction.

Procedure:
1. Cal DFS(G) for each vertex
2. When a vertex is finished, insert it onto the front of a list
3. The resulted list of vertices have the property that all the edges goes from left to right.
### 200. Number of Islands
Given a 2d grid map of '1's (land) and '0's (water), count the number of islands. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.
```
Input: grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
Output: 3
```
#### Solution
```
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        if len(grid) == 0 or len(grid[0]) == 0:
            return 0
        num_rows = len(grid)
        num_cols = len(grid[0])
        
        # visited is a mask. If grid can be modified, then we can save memory by setting the visited cells to '0'
        visited = [[False for _ in range(num_cols)]
                   for i in range(num_rows)]
    
        def searchIsland(row, col):
            if row<0 or row>=num_rows or col<0 or col>=num_cols:
                return
            if visited[row][col]:
                return
            visited[row][col] = True
            if grid[row][col] =='1':
                for (r,c) in [(row-1, col), (row, col-1), (row+1, col), (row, col+1)]:
                    searchIsland(r,c)
            
        num_islands = 0
        for row in range(len(grid)):
            for col in range(len(grid[0])):
                if grid[row][col]=='1' and not visited[row][col]:
                    num_islands += 1
                    searchIsland(row, col)
        return num_islands
```
### 785. Is Graph Bipartite?
Given an undirected graph, return true if and only if it is bipartite.

Recall that a graph is bipartite if we can split it's set of nodes into two independent subsets A and B such that every edge in the graph has one node in A and another node in B.

```
class Solution:
    def isBipartite(self, graph: List[List[int]]) -> bool:
        visited = [0]*len(graph) 
        
        def dfsHelper( i,  group):
            if visited[i]!=0:
                return visited[i]==group
            visited[i] = group
            for j in graph[i]:
                if not dfsHelper(j,  -group):
                    return False
            return True
            
        for i in range(len(graph)):
            if visited[i] != 0:
                continue
            if not dfsHelper( i, 1):
                return False
        return True
```

### 98. Validate Binary Search Tree
Given a binary tree, determine if it is a valid binary search tree (BST).
#### Solution 1. inorder triversal
```
class Solution:
    def __init__(self):
        self.predecessor = None
        
    def isValidBST(self, root: TreeNode) -> bool:
        self.predecessor = None
        
        def helper(root):
            if not root:
                return True
            left_is_bst = helper(root.left)
            if not left_is_bst:
                return False
            if self.predecessor!=None and root.val<= self.predecessor:
                return False
            self.predecessor = root.val
            return helper(root.right)
        
        return helper(root)
```

#### Solution 2. Recursive
```
class Solution:
    def isValidBST(self, root):
        """
        :type root: TreeNode
        :rtype: bool
        """
        def helper(node, lower = float('-inf'), upper = float('inf')):
            if not node:
                return True
            
            val = node.val
            if val <= lower or val >= upper:
                return False

            if not helper(node.right, val, upper):
                return False
            if not helper(node.left, lower, val):
                return False
            return True

        return helper(root)
```

### 207. Course Schedule
There are a total of numCourses courses you have to take, labeled from 0 to numCourses-1.

Some courses may have prerequisites, for example to take course 0 you have to first take course 1, which is expressed as a pair: [0,1]

Given the total number of courses and a list of prerequisite pairs, is it possible for you to finish all courses?
```
Example 2:

Input: numCourses = 2, prerequisites = [[1,0],[0,1]]
Output: false
Explanation: There are a total of 2 courses to take. 
             To take course 1 you should have finished course 0, and to take course 0 you should
             also have finished course 1. So it is impossible.
```
#### Solution 1
1. Need to construct an adjacency-list representation
2. Use DFS to check isCyclic. Remember the "finished" nodes to avoid redundant work.
```
class Solution:
    def canFinish(self, numCourses: int, prerequisites: List[List[int]]) -> bool:
        # Topological sort
        # adjacency-list representation
        from collections import defaultdict
        courseDict = defaultdict(list)
        for relation in prerequisites:
            courseDict[relation[0]].append(relation[1])
        
        checked = [False]*numCourses
        path = [False]*numCourses
        
        for course in range(numCourses):
            if self.isCyclic(course, courseDict, checked, path):
                return False
        return True
    
    def isCyclic(self, course, courseDict, checked, path):
        if checked[course]:
            return False
        if path[course]:
            return True     
        path[course] = True
        
        for pre in courseDict[course]:
            if self.isCyclic(pre, courseDict, checked, path):
                return True
        
        path[course] = False
        checked[course] = True
```
#### Solution 2. Topological sorting

### 210. Course Schedule II
This is the follow-up question from 207. Given the total number of courses numCourses and a list of the prerequisite pairs, return the ordering of courses you should take to finish all courses.

If there are many valid answers, return any of them. If it is impossible to finish all courses, return an empty array.

#### Solution 1:
By Topological sorting, ordering nodes by their finishing time in DFS.
```
class Solution:
    def findOrder(self, numCourses: int, prerequisites: List[List[int]]) -> List[int]:   
        
        from collections import defaultdict
        courseDict = defaultdict(list)
        for pair in prerequisites:
            courseDict[pair[0]].append(pair[1])
            
        finished = [False]*numCourses
        path = [False]*numCourses
        ordering = []
        
        def dfs(course):
            if finished[course]:
                return False
            if path[course]:
                return True
            path[course] = True
            for pre in courseDict[course]:
                foundCyclic = dfs(pre)
                if foundCyclic:
                    return True
            finished[course] = True
            ordering.append(course)
            path[course] = False
            return False
        
        for i in range(numCourses):
            if finished[i]: 
                continue
            foundCyclic = dfs(i)
            if foundCyclic:
                ordering = []
                break
        return ordering
```
### 199. Binary Tree Right Side View
Given a binary tree, imagine yourself standing on the right side of it, return the values of the nodes you can see ordered from top to bottom.

Example:  
Input: [1,2,3,null,5,null,4]  
Output: [1, 3, 5]  
```

   1            <---
 /   \
2     3         <---
 \      
  5             <---

```
#### Solution 1. BFS
```
class Solution:
    def rightSideView(self, root: TreeNode) -> List[int]:
        if root==None:
            return []
        result = []
        
        from collections import deque
        queue = deque([root])
        counter = 1
        
        while len(queue)>0:
            numNodes = counter
            counter = 0
            for i in range(numNodes):
                node = queue.popleft()
                if i==0:
                    result.append(node.val)
                if node.right!=None:
                    queue.append(node.right)
                    counter += 1
                if node.left!=None:
                    queue.append(node.left)
                    counter += 1
        return result
```
time complexity: O(N) since we visit all nodes  
space complexity: O(D), D is the tree diameter
#### Solution 2. DFS
```
class Solution:
    def rightSideView(self, root: TreeNode) -> List[int]:
        if root==None:
            return []
        rightside = []
        # note: the length of rightside == the max depth have been visited
        def dfs(node, level):
            if level>len(rightside):
                rightside.append(node.val)
            for child in [node.right, node.left]:
                if child:
                    dfs(child, level+1)
        dfs(root, 1)
        return rightside
```
### 261. Graph Valid Tree
Given n nodes labeled from 0 to n-1 and a list of undirected edges (each edge is a pair of nodes), write a function to check whether these edges make up a valid tree.

Example 1:  
Input: n = 5, and edges = [[0,1], [0,2], [0,3], [1,4]]  
Output: true
Example 2:  
Input: n = 5, and edges = [[0,1], [1,2], [2,3], [1,3], [1,4]]  
Output: false

#### Solution 1. DFS.
Note: 
1. A graph, G, is a tree iff the following two conditions are met:
    * G is fully connected.
    * G contains no cycles. 
In this problem, G is an undirected graph. Therefore we need to record the node's parent, to avoid double check.
2. A quick check is `if len(edges) != n - 1`.

```
class Solution:
    def validTree(self, n: int, edges: List[List[int]]) -> bool:
        adjacency = [[] for i in range(n)]
        for a,b in edges:
            adjacency[a].append(b)
            adjacency[b].append(a)
        visited = [False]*n
        # dfs check if their is a circle
        def isCyclic(index, parent):
            if visited[index]:
                return True
            visited[index] = True
            for neighbor in adjacency[index]:
                if neighbor == parent:
                    continue
                if isCyclic(neighbor, index):
                    return True
            return False
        
        if isCyclic(0, None) or visited.count(False)>0:
            return False
        return True
```
### BFS
```
class Solution:
    def validTree(self, n: int, edges: List[List[int]]) -> bool:
        if len(edges) != n - 1: return False

        # Create an adjacency list.
        adj_list = [[] for _ in range(n)]
        for A, B in edges:
            adj_list[A].append(B)
            adj_list[B].append(A)

        # We still need a seen set to prevent our code from infinite
        # looping if there *is* cycles (and on the trivial cycles!)
        seen = {0}
        print(type(seen))
        
        queue = collections.deque([0])

        while queue:
            node = queue.popleft()
            for neighbour in adj_list[node]:
                if neighbour in seen:
                    continue
                seen.add(neighbour)
                queue.append(neighbour)

        return len(seen) == n
```
### 127. Word Ladder
Given two words (beginWord and endWord), and a dictionary's word list, find the length of shortest transformation sequence from beginWord to endWord, such that:

Only one letter can be changed at a time.
Each transformed word must exist in the word list.

Note:  
Return 0 if there is no such transformation sequence.
All words have the same length.
All words contain only lowercase alphabetic characters.
You may assume no duplicates in the word list.
You may assume beginWord and endWord are non-empty and are not the same.

Example 1:  
Input:
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]  
Output: 5

Explanation: As one shortest transformation is "hit" -> "hot" -> "dot" -> "dog" -> "cog",
return its length 5.
#### Solution. BFS
Note.
1. How to construct a graph from the wordList?
2. How to count the depth of the tree?
	* iteration side the while loop, conter the diameter, or
    * push (node, depth) into the queue

```
class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        if endWord not in wordList or not endWord or not beginWord or not wordList:
            return 0
        # Construct an adjDict
        from collections import defaultdict, deque
        all_combo_dict = defaultdict(list)
        L = len(beginWord)
        for word in wordList:
            for i in range(L):
                all_combo_dict[word[:i]+'*'+word[i+1:]].append(word)
        # BFS
        queue = deque([beginWord])
        path = set()
        depth = 0
        while len(queue)>0:
            diameter = len(queue)
            depth += 1
            for k in range(diameter):
                word = queue.popleft()
                if word == endWord:
                    return depth
                for i in range(L):
                    key = word[:i]+'*'+word[i+1:]
                    if key in path:
                        continue
                    next_words = all_combo_dict[key]
                    queue.extend(next_words)
                    path.add(key)
        return 0
```