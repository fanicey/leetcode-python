

# Basics

## Tree types
* Binary tree
* **Binary search tree**
* Balanced tree

## Property of binary search tree
All the nodes on the left subtree are smaller than the root; all the nodes on the right subtree are greater than the root.

Minimum appears at the leftmost leaf. Maximum appears at the rightmost leaf.

To find the successor of a node `x`, we need to consider two scenarios (general binary tree):

1. `x` has right node, then the successor is the tree-minimum(x.right)
2. `x` does not have right node. Then the successor `y` is the lowest ancestor of x whose left child is also ancestor of `x`. In other words, we need to find the first ancestor such that `x` is on its left side. 

If the tree is a binary search tree, then successor will be the node whose value is the next larger node.

## Benefits

Benefit: Support **search, min, max, predecessor, successor, insert, delete** with time proportional to $d$, where $d$ is the height of the tree. On average, the height is logN. For red-blck tree, the height is always logN.

## Tree walks:  
* pre-order: root->children
* in-order: left->root->right
* post-order: children ->root
Implementation: iterative, recursive.

### Implement in-order traversal iteratively

Note: if a node is in stack, it means the node has been visited, but not processed

```
class Solution:
    def inorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        result = []
        stack = []
        node = root
        while node or stack:
            if node:
                stack.append(node)
                node = node.left
            else:
                node = stack.pop()
                result.append(node.val)
                node = node.right
        return result
```

### Implement pre-order traversal iteratively

```
class Solution:
    def preorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        result = []
        stack = []
        node = root
        while node or stack:
            if node:
                result.append(node.val)
                stack.append(node.right)
                node = node.left
            else:
                node = stack.pop()
        return result
```

### Implement post-order traversal iteratively

Because its non-tail recursion nature, implementing post-order traversal with iteration is much more complex than in-order and pre-order. But we can easily do it with 2 stacks: the first stack is to store nodes to be processed; the second one is to store the nodes in recursive order.

*Tips: A recursive function is said to be tail-recursive if the recursive call is the last thing done by the function, there is no need to keep record of the previous state.*

```
class Solution:
    def postorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        if not root:
            return []
        stack1, stack2 = [root],[]
        result = []
        while stack1:
            node = stack1.pop()
            if node.left:
                stack1.append(node.left)
            if node.right:
                stack1.append(node.right)
            stack2.append(node)
        while stack2:
            result.append(stack2.pop().val)
        return result
```



## Morris Traversal

Morris traversal is a method that does NOT employ any recursion or stacks. It creates links between the predecessor and the current node, and check for loops to reconstruct the original tree.

Morris Traversal could easily apply to solve pre-order and in-order traversal. 

**Algorithm outline:** https://www.educative.io/edpresso/what-is-morris-traversal

Start from node=root

1. if node does not have left node, means node haas no precessesor. go to visit right.

2. If node has left node, then find the **immediate precessor** of node. We need to think about two cases:

   (1) if no loop, then link precessor.right = node, and visit node.left node

   (2) detect a loop, it means we have iterate through all the nodes on the left, then set precessor.right = null, and visit node.right.

   

```
class Solution:
    def preorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
        node, output = root, []
        while node:  
            if not node.left:
                output.append(node.val)
                node = node.right
            else:
                predecessor = node.left
                while predecessor.right and predecessor.right != node:
                    predecessor = predecessor.right
                if not predecessor.right:
                    predecessor.right = node
                    #output.append(node.val)  <-- preorder
                    node = node.left
                else:
                    predecessor.right = None
                    # output.append(node.val)  <-- inorder
                    node = node.right
            
        return output
```



## Think Recursively

### One root
```
def recursiveSolver(root):
    if not root: return something
    l = recursiveSolver(root.left)
    r = recursiveSolver(root.right)
    return f(root.val, l, r)

```
Example: No.104, No.111, No.112
### Two roots
No. 100 Same tree, 101 Symmetric Tree, 951

## Algorithms
Key to solve tree problems: think recursively.
### Construct a binary search tree
* from sorted list
* from non sorted list
* Balance tree
### search.
* find closest value No.270
* find successor/ predecessor No
### Insertion and Deletion
* insertion No.701

### Iterate BST

* 173. Binary Search Tree Iterator

## Resources

[cracking coding interview](https://www.youtube.com/watch?v=oSWTXtMglKE&list=PLX6IKgS15Ue02WDPRCmYKuZicQHit9kFt&index=15)

### 270. Closest Binary Search Tree Value (Easy)
Given a non-empty binary search tree and a target value, find the value in the BST that is closest to the target.

Note:  
Given target value is a floating point.
You are guaranteed to have only one unique value in the BST that is closest to the target.
Example:

Input: root = [4,2,5,1,3], target = 3.714286
```
    4
   / \
  2   5
 / \
1   3
```
```
class Solution:
    def closestValue(self, root: TreeNode, target: float) -> int:
        # iterative
        closest = root.val
        while root:
            closest = min(root.val, closest, key=lambda x:abs(target-x)) 
            root = root.left if root.val>target else root.right
        return closest
```
Output: 4

### 285 Inorder Successor in BST

Given a binary search tree and a node in it, find the in-order successor of that node in the BST.

(Introduction to Algorithms)The successor of a node p is the node with the smallest key greater than p.val.
1. x.right != null. Then y is the leftmost node in x.right
2. x.right == null. Then y is the lowest ancestor of x whose leftmost child is also and ancessor of x. 

```
class Solution:
    def inorderSuccessor(self, root: 'TreeNode', p: 'TreeNode') -> 'TreeNode':
        if p.right:
            y = p.right
            while y.left:
                y = y.left
            return y
        stack = []
        y = root
        while y!=p :
            stack.append(y)
            if y.val<p.val:
                y = y.right
            else:
                y = y.left
        stack.append(p)
        for i in range(len(stack)-1, 0, -1):
            if stack[i] == stack[i-1].left:
                return stack[i-1]
        return None
```
A simpler solution uses the property of BST: find the smallest node that is larger than p.
```
class Solution:
    def inorderSuccessor(self, root: 'TreeNode', p: 'TreeNode') -> 'TreeNode':
        x = root
        successor = None
        while x:
            if x.val<=p.val:
                x = x.right
            elif x.:val>p.val:
                successor = x
                x = x.left
        return successor
```

# Problems
### 101. Symmetric Tree
Given a binary tree, check whether it is a mirror of itself (ie, symmetric around its center). Solve it both recursively and iteratively

For example, this binary tree [1,2,2,3,4,4,3] is symmetric:
```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```
#### Recursive
```
class Solution:
    def isSymmetric(self, root: TreeNode) -> bool:
        def isMirror(left, right):
            if not left and not right: return True
            if not left or not right: return False
            return left.val==right.val and isMirror(left.right, right.left) and isMirror(left.left, right.right)
        
        return not root or isMirror(root.left, root.right)
```
#### Iterative


### 108. Convert Sorted Array to Binary Search Tree
Given an array where elements are sorted in ascending order, convert it to a height balanced BST.

For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of every node never differ by more than 1.

```
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> TreeNode:
        if len(nums)<1:
            return None
        mid = (len(nums)-1)//2
        left = self.sortedArrayToBST(nums[:mid])
        right = self.sortedArrayToBST(nums[mid+1:])
        return TreeNode(nums[mid], left, right)
```

### 669. Trim a Binary Search Tree
Given a binary search tree and the lowest and highest boundaries as L and R, trim the tree so that all its elements lies in [L, R] (R >= L). You might need to change the root of the tree, so the result should return the new root of the trimmed binary search tree.

Recursive Solution:
```
class Solution:
    def trimBST(self, root: TreeNode, L: int, R: int) -> TreeNode:
        def trim(node):
            if not node:
                return None
            elif node.val<L:
                return trim(node.right)
            elif node.val>R:
                return trim(node.left)
            else:
                node.left=trim(node.left)
                node.right=trim(node.right)
                return node
        return trim(root)
```

An iterative solution: (use fake root to deal the root change problem)
```
class Solution:
    def trimBST(self, root: TreeNode, L: int, R: int) -> TreeNode:
        fake_root = TreeNode(0, root, None)
        p = fake_root
        while p!=None:
            if p.left ==None:
                break
            if p.left.val <L:
                p.left = p.left.right
            else:
                p = p.left
        fake_root = TreeNode(0, None, fake_root.left)
        p = fake_root
        while p!=None:
            if p.right ==None:
                break
            if p.right.val >R:
                p.right = p.right.left
            else:
                p = p.right
        
        return fake_root.right
```
### 701. Insert into a Binary Search Tree
Given the root node of a binary search tree (BST) and a value to be inserted into the tree, insert the value into the BST. Return the root node of the BST after the insertion. It is guaranteed that the new value does not exist in the original BST.

Note that there may exist multiple valid ways for the insertion, as long as the tree remains a BST after insertion. You can return any of them.
```
class Solution:
    def insertIntoBST(self, root: TreeNode, val: int) -> TreeNode:
        if not root:
            return TreeNode(val)
        if root.val>val:
            root.left = self.insertIntoBST(root.left, val)
        else:
            root.right = self.insertIntoBST(root.right, val)
        return root
```

### 99. Recover Binary Search Tree (medium)

You are given the `root` of a binary search tree (BST), where the values of **exactly** two nodes of the tree were swapped by mistake. *Recover the tree without changing its structure*.

Solution:

Consider swapping two elements in a sorted array. we gonna have a peak and a valley. So we can consider of using in-order traversal to find the peak and the valley in the tree;

Next question: do it recursively or iteratively? Since we need to memorize the last node (predecussor), it's better to use iteration.

Be careful about the special case: two consecutive elements are swapped.

```
class Solution:
    def recoverTree(self, root: Optional[TreeNode]) -> None:
        """
        Do not return anything, modify root in-place instead.
        """
        # inorder traversal to find a peak and a valley
        # Note. Inorder traversal of BST should be sorted array
        x = y = None
        stack = []
        node = root
        lastNode = TreeNode(-float('inf'))
        while node or stack:
            if node:
                print(node.val)
                stack.append(node)
                node = node.left
            else:
                node = stack.pop()
                if node.val < lastNode.val:
                    y = node
                    if not x:    # <-- special case: two consecutive values swapped
                        x = lastNode
                    else:
                        break
                lastNode = node
                node = node.right
        x.val, y.val = y.val, x.val
```

