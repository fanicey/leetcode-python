# Basics
tradeoff between space and speed.

Similarly to recursive, DP solves the problem by reducing it to smaller sub problems. These sub problems have overlaps. 
# Problems
## Array
### 70. Climbing Stairs
You are climbing a stair case. It takes n steps to reach to the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

Solution. This problem can be solved in many ways. For a bottom-up approach:

```
class Solution(object):
    def climbStairs(self, n):
        """
        :type n: int
        :rtype: int
        """
        # r(n) = r(n-1) + r(n-2)
        r = [0, 1, 2]
        i = 3
        while i<=n:
            r.append(r[i-1] + r[i-2])
            i += 1
        return r[n]
```
Also notice how this problem is related to Fibonacci number. It helps us to save some memory:
```
class Solution(object):
    def climbStairs(self, n):
        """
        :type n: int
        :rtype: int
        """
        # r(n) = r(n-1) + r(n-2)
        if (n < 2): return 1
        r1, r2 = 1, 2
        i = 3
        while i<=n:
            temp = r1 + r2
            r1, r2 = r2, temp
            i += 1
        return r2
```
LeetCode has other sophisticated solutions, whose time complexity is only O(logN)

## 213. House Robber II

You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed. All houses at this place are arranged in a circle. That means the first house is the neighbor of the last one. Meanwhile, adjacent houses have security system connected and it will automatically contact the police if two adjacent houses were broken into on the same night.

Given a list of non-negative integers representing the amount of money of each house, determine the maximum amount of money you can rob tonight without alerting the police.

Example 2:

Input: [1,2,3,1], 
Output: 4. 
Explanation: Rob house 1 (money = 1) and then rob house 3 (money = 3).
Total amount you can rob = 1 + 3 = 4.
```
class Solution(object):
    def rob(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        # subproblems:
        # 1. rob_straight(nums[1:])
        # 2. rob_straight(nums[:-2])
        def helper(nums):
            prev, curr = 0,0
            for i in nums:
                temp = curr
                curr = max(i+prev, curr)
                prev = temp
            return curr
        
        if len(nums) == 0:
            return 0
        if len(nums) == 1:
            return nums[0]
        return max(helper(nums[1:]), helper(nums[:-1]))
        
```
![](/Users/bingfan/Documents/leetcode-python/pictures/213_house_robberII_approach1_slide01.png)

This problem is an extension of 198. Rob a house. The trick is how to break the circle into straight lines. Also pay attention to the edge cases.

## Matrix
### 64. Minimum Path Sum
Given a m x n grid filled with non-negative numbers, find a path from top left to bottom right which minimizes the sum of all numbers along its path.

Note: You can only move either down or right at any point in time.

Example:

Input:
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
Output: 7. 

```
class Solution(object):
    def minPathSum(self, grid):
        """
        :type grid: List[List[int]]
        :rtype: int
        """
        if not grid: return 0
        m = len(grid)
        n = len(grid[0])
        dp = [[0 for _ in range(n)] for _ in range(m)]
        dp[0][0] = grid[0][0]
        for j in range(1, n):
            dp[0][j] = dp[0][j-1] + grid[0][j]
        for i in range(1, m):
            dp[i][0] = dp[i-1][0] + grid[i][0]
        for i in range(1, m):
            for j in range(1, n):
                dp[i][j] = min(dp[i-1][j], dp[i][j-1]) + grid[i][j]
        return dp[m-1][n-1]
```

### 62. Unique Paths
A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).

How many possible unique paths are there?

```
class Solution(object):
    def uniquePaths(self, m, n):
        """
        :type m: int
        :type n: int
        :rtype: int
        """
        # pascal's triangle
        if m==0 or n==0: return 0
        dp = [1]*n
        for i in range(1, m):
            for j in range(1, n):
                dp[j] = dp[j-1] + dp[j]
        return dp[-1]
```
Notice that the resulting DP matrix forms a Pascal's Triangle. So another approach is to use the (n choose k) to calculate. (Also, binomial theorem)

## String
### 91. Decode Ways
A message containing letters from A-Z is being encoded to numbers using the following mapping:

'A' -> 1
'B' -> 2
...
'Z' -> 26

Given a non-empty string containing only digits, determine the total number of ways to decode it.

Example 1:

Input: "12"
Output: 2
Explanation: It could be decoded as "AB" (1 2) or "L" (12).

Example 2:

Input: "226"
Output: 3
Explanation: It could be decoded as "BZ" (2 26), "VF" (22 6), or "BBF" (2 2 6).

My initial solution:
```
class Solution(object):
    def numDecodings(self, s):
        """
        :type s: str
        :rtype: int
        """

        # edge case: s= ""
        # what if string contains "0"?
        if len(s)==0 or s[0]=='0':
            return 0
        
        dp = [1, 1]
        for i in range(1, len(s)):
            if s[i]=='0':
                if s[i-1]=='1' or s[i-1]=='2':
                    count = dp[0]
                else:
                    return 0
            else:
                count= dp[1]
                if s[i-1: i+1]<'27' and s[i-1] !='0':
                    count += dp[0]
            dp[0], dp[1] = dp[1], count
        return dp[1]
```

This problem is all about the edge cases involved with "0" being in the input. The following solution solves the problem by thinking if a "1 digit" or "2 digits" slice is a decodable.

```
class Solution(object):
    def numDecodings(self, s):
        """
        :type s: str
        :rtype: int
        """

        # edge case: s= ""
        # what if string contains "0"?
        if len(s)==0 or s[0]=='0':
            return 0
        
        dp = [1, 1]
        for i in range(1, len(s)):
            count = 0
            if s[i] != '0':
                count= dp[1]
            else:
                if s[i-1] !='1' and s[i-1] !='2':
                    return 0
                
            if s[i-1: i+1]<'27' and s[i-1: i+1] >='10':
                count += dp[0]
            dp[0], dp[1] = dp[1], count
        return dp[1]
``` 

### 300. Longest Increasing Subsequence
Given an unsorted array of integers, find the length of longest increasing subsequence.

Example:

Input: [10,9,2,5,3,7,101,18]
Output: 4 
Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4. 

Note:
There may be more than one LIS combination, it is only necessary for you to return the length.
Your algorithm should run in O(n2) complexity.

Follow up: Could you improve it to O(n log n) time complexity?

#### Solution 1. DP
Complexity: O(n^2) Memory: O(n)

The trick is for nums[i], use dp to store the longest increasing subsequence that is **ended by nums[i]**. To do so, need to revisit dp[0:i].
```
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        if len(nums)<1: 
            return 0
        
        dp = [0]*len(nums)
        for i in range(len(nums)):
            for j in range(i):
                if nums[i]>nums[j]:
                    dp[i] = max(dp[i], dp[j])
            dp[i] +=1
        return max(dp)
```
#### Solution 2. DP + binary search
Time complexity O(NlogN), memory: O(N)

A list dp is to store the "possible" longest increasing subsequence. Note dp array does not give a valid longest increasing subsequence.

Binary search is to find the index to insert nums[i].

Consider the example: [3,5,6,2,3,4,5].  
6 -> dp=[3,5,6], length=3  
2 -> dp=[2,5,6], length=3
3 -> dp=[2,3,6], length=3  
4 -> dp=[2,3,4], length=3  
5 -> dp=[2,3,4,5], length=4  


```
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        def BinarySearch(dp, l, r, num):
            # return the index of num in dp
            # r = len(dp)
            while l < r:
                mid = (l+r)//2
                if dp[mid] < num:
                    l = mid + 1   
                else:
                    r = mid
            return l
        
        dp = [0]*len(nums)
        length = 0
        for num in nums:
            i = BinarySearch(dp, 0, length, num)
            dp[i] = num
            if i==length:
                length += 1
        return length
```

### 1048. Longest String Chain
Given a list of words, each word consists of English lowercase letters.

Let's say word1 is a predecessor of word2 if and only if we can add exactly one letter anywhere in word1 to make it equal to word2.  For example, "abc" is a predecessor of "abac".

A word chain is a sequence of words [word_1, word_2, ..., word_k] with k >= 1, where word_1 is a predecessor of word_2, word_2 is a predecessor of word_3, and so on.

Return the longest possible length of a word chain with words chosen from the given list of words.

 

Example 1:

Input: ["a","b","ba","bca","bda","bdca"]  
Output: 4.   
Explanation: one of the longest word chain is "a","ba","bda","bdca".
 

Note:

* 1 <= words.length <= 1000
* 1 <= words[i].length <= 16
* words[i] only consists of English lowercase letters.

#### Solution.
This problem can be broken into two subproblems:
1. how to check predecessor
2. how to get the longest word chain

##### check predecessor
One way to check if w1 is a predessor of w2 is,
```
for i in [0,..., len(w1)]:
    compare w2 and w1[:i]+w[i+1:]
	
``` 
Another faster way is to use two pointers (see final solution)

##### find the longest word chain
We can use the same method in No.300. But notice: different from No.3, the word chain does not need to be in-order. i.e., the length of the longest word chain of ["a","b","ba","bdca","bca","bda"] is 4, although "bdca" appears before "bca".

Therefore, one solution is to sort words first; another one uses a 2D list to reocrd the length and word indices. 

```
class Solution:
    def IsPredecessor(self, w1, w2):
        # return true if w2 is predecessor of w1
        if len(w1)+1 != len(w2):
            return False
        if len(w1)==0:
            return True  

        diff = 0
        for i in range(len(w2)):
            if i-diff==len(w1) or w1[i-diff] != w2[i]:
                diff += 1

        return diff==1
        
    def longestStrChain(self, words: List[str]) -> int:
         
        if len(words)<1:
            return 0
        
        # Use a hashmap to record the length and the index
        length_position = [[] for i in range(17)]
        for i in range(len(words)):
            w = words[i]
            length_position[len(w)].append(i)
                  
        dp = [1]*len(words)
        
        # visit the dictionaty from shorter length to longer length
        for i in range(2, 17):
            if len(length_position[i-1])==0 or len(length_position[i])==0:
                continue
            current = length_position[i]
            previous = length_position[i-1]
            for j in current:
                for k in previous:
                    if self.IsPredecessor(words[k], words[j]):
                        dp[j] = max(dp[j], dp[k]+1)
        return max(dp)
```
A shorter solution with sorting:
```
def longestStrChain(self, words):
        dp = {}
        for w in sorted(words, key=len):
            dp[w] = max(dp.get(w[:i] + w[i + 1:], 0) + 1 for i in range(len(w)))
        return max(dp.values())
```


