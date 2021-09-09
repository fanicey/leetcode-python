Binary Tree

### 112. Path Sum
Given a binary tree and a sum, determine if the tree has a root-to-leaf path such that adding up all the values along the path equals the given sum.

Note: **A leaf is a node with no children.**
```
class Solution:
    def hasPathSum(self, root: TreeNode, sum: int) -> bool:
        if not root:
            return False
        if not root.left and not root.right:
            return True if sum==root.val else False       
        return self.hasPathSum(root.left, sum-root.val) or self.hasPathSum(root.right, sum-root.val)
```

### 113. Path Sum II

Given the `root` of a binary tree and an integer `targetSum`, return all **root-to-leaf** paths where the sum of the node values in the path equals `targetSum`. Each path should be returned as a list of the node **values**, not node references.

```
class Solution:
    def pathSum(self, root: Optional[TreeNode], targetSum: int) -> List[List[int]]:
        result = []
        
        def helper(root, targetSum, path):
            if not root:
                return
            newPath = path+[root.val]
            if not root.left and not root.right:
                if root.val == targetSum:
                    result.append(newPath)
                return
            helper(root.left, targetSum - root.val, newPath)
            helper(root.right, targetSum - root.val, newPath)
            
        helper(root, targetSum, [])
        return result
```

## 437. Path Sum III

Given the `root` of a binary tree and an integer `targetSum`, return *the number of paths where the sum of the values along the path equals* `targetSum`.

The path does not need to start or end at the root or a leaf, but it must go downwards (i.e., traveling only from parent nodes to child nodes).

```
class Solution:
        
    def pathSum(self, root: Optional[TreeNode], targetSum: int) -> int:
        # note: the start and end notes are not necessary to be root or leaf    
        sumMap = defaultdict(int)
        
        def _pathSum(root, cursum):
            count = 0
            if not root:
                return count
            cursum = cursum + root.val
            if cursum == targetSum:
                count += 1
            count += sumMap[cursum-targetSum]
            sumMap[cursum] += 1
            count += _pathSum(root.left, cursum)
            count += _pathSum(root.right, cursum)
            sumMap[cursum] -= 1
            return count
        
        return _pathSum(root, 0)
```

Note: The difficulty of this question is, the path may start from any node and end with any node. A easier & similar problem is 560. Subarray Sum Equals K.

## 666. Path Sum IV

If the depth of a tree is smaller than `5`, then this tree can be represented by an array of three-digit integers. For each integer in this array:

- The hundreds digit represents the depth `d` of this node where `1 <= d <= 4`.
- The tens digit represents the position `p` of this node in the level it belongs to where `1 <= p <= 8`. The position is the same as that in a full binary tree.
- The units digit represents the value `v` of this node where `0 <= v <= 9`.

Given an array of **ascending** three-digit integers `nums` representing a binary tree with a depth smaller than `5`, return *the sum of all paths from the root towards the leaves*.

It is **guaranteed** that the given array represents a valid connected binary tree.

Example: 

```
Input: nums = [113,215,221]
Output: 12
Explanation: The tree that the list represents is shown.
The path sum is (3 + 5) + (3 + 1) = 12.
```

Solution:

The key point is to use the position relationship between the parent node and children nodes: 10level+position ->10(level+1)+2*position-1 (left) and 10(level+1)+2*position (right). My solution below is using the sum-product rule; another standard solution is using DFS.

```
class Solution:
    def pathSum(self, nums: List[int]) -> int:
        h = defaultdict(int)
        result = 0
        for node in reversed(nums):
            print(node)
            val = node%10
            level = node//100
            pos = (node-100*level)//10
            left = (level+1)*10+2*pos-1 
            right = (level+1)*10+2*pos
            if left not in h and right not in h: # this is a leaf node
                h[10*level+pos] = 1
                result += val
            else:
                h[10*level+pos] = h[left] + h[right]
                result = result + val*h[10*level+pos] 
        return result
```

