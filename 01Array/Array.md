## Array in Python
1. `enumerate()` function could simplify looping with counters. Instead of writing:
2.  `[0]*3`
3. list comprehension
4. Most list operations are in-place. For example, `nums.sort()`, `nums.reverse()` will change the elements order in place. If you want a new list and don't want to change the original one, use `new_nums = sorted(nums)` insteand
5. Commonly used 
5. In python3, list1=list2 assigns the reference, while list1=list2.copy() takes a shallow copy of list2. Therefore, if you change the value of list2[0], in the first case list1[0] will also change, but in the latter one list1[0] does not change. Note: if the list elements are not primary datatypes, taking shallow copy only copies their reference, not the real contents.
 
5. Use list to mimick a stack. Nums.pop()
5. Two pointers 
    * slow-fast
    * left-right

quicksort can be implemented in both ways
## Algorithms
### quicksort

## Exercises
### 53. Maximum Subarrays
Given an integer array nums, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

Example:

Input: [-2,1,-3,4,-1,2,1,-5,4],
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.
```
class Solution(object):
    def maxSubArray(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        # Greedy
        if len(nums)<1: return 0
        res = nums[0]
        cursum = nums[0]
        for i in nums[1:]:
            cursum = max( cursum+i, i )
            res = max(res, cursum)
        return res
```
**Note.** 
1. The maximum subarray  
1. Also think of how to use divide-conquer to solve the problem

### 15. 3 Sum

Given an array nums of n integers, are there elements a, b, c in nums such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero.

Note:
The solution set must not contain duplicate triplets.

Example:
Given array nums = [-1, 0, 1, 2, -1, -4],
A solution set is:
[
  [-1, 0, 1],
  [-1, -1, 2]
]

There are many ways to solve this problem. Here only list the best one, which uses two pointers.

```
def threeSum(self, nums):
    """
    :type nums: List[int]
    :rtype: List[List[int]]
    """
	result = []
    nums.sort() # sort to avoid repetition
    for i in range(len(nums)-2):
        # Skip index to avoid repetition
        if i>0 and nums[i]==nums[i-1]:
            continue
        j = i+1
        k = len(nums)-1
        while j<k:
            s = nums[i]+nums[j]+nums[k]
            if s == 0:
                result.append([nums[i], nums[j], nums[k]])
                j += 1 
                k -= 1
                while j<k and nums[j]==nums[j-1]:
                    j += 1
                    # The following is not necessary because nums[j] is different,
                    # there won't be a duplicated solution 
                    # while j<k and nums[k]==nums[k+1]:
                    #     k -= 1
            elif s > 0:
                k -= 1
            else:
                j += 1
    return result

```


### 27. Remove Element
Given an array nums and a value val, remove all instances of that value in-place and return the new length.

Do not allocate extra space for another array, you must do this by modifying the input array **in-place with O(1) extra memory**.

**The order of elements can be changed**. It doesn't matter what you leave beyond the new length.
```
class Solution(object):
    def removeElement(self, nums, val):
        """
        :type nums: List[int]
        :type val: int
        :rtype: int
        """
        # Solution 1
        i = 0
        j = len(nums)-1
        while i <= j:
            if nums[i] == val:
                nums[i] = nums[j]
                j -= 1
            else:
                i += 1
        return i
            
        
        """
        # Solution 2
        i = -1
        for j in range( len(nums) ):
            if nums[j] != val:
                i += 1
                nums[i] = nums[j]
        return i+1
        """
```
**Note.** 

Method: 2 pointers. Solution 1 uses the left-right pointers, while solution 2 uses the slow-fast pointers. Solution 1 is faster if `val` is rare in the list.

### 566 Reshape the Matrix
You're given a matrix represented by a two-dimensional array, and two positive integers r and c representing the row number and column number of the wanted reshaped matrix, respectively.

The reshaped matrix need to be filled with all the elements of the original matrix in the same row-traversing order as they were.

If the 'reshape' operation with given parameters is possible and legal, output the new reshaped matrix; Otherwise, output the original matrix.

Example 1:

Input: 
nums = 
[[1,2],
 [3,4]]
r = 1, c = 4
Output: 
[[1,2,3,4]]
```
class Solution(object):
    def matrixReshape(self, nums, r, c):
        """
        :type nums: List[List[int]]
        :type r: int
        :type c: int
        :rtype: List[List[int]]
        """
        if len(nums)==0 or len(nums)*len(nums[0]) != r*c:
            return nums
        row = len(nums)
        col = len(nums[0])
        res = [[0]*c for _ in range(r)]
        for i in range(r*c):
            res[ i//c ][ i%c ] = nums[ i//col ][ i%col ]
        return res
```
**Note**
1. `res = [[0]*c for _ in range(r)]` Python's way to create a 2D array

2. `i//c` round down to the nearest integer.
### 121. Best Time to Buy and Sell Stock
(related: 53)
Say you have an array for which the ith element is the price of a given stock on day i.

If you were only permitted to complete at most one transaction (i.e., buy one and sell one share of the stock), design an algorithm to find the maximum profit.

Note that you cannot sell a stock before you buy one.

Example 1:

Input: [7,1,5,3,6,4]  
Output: 5  
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5. Not 7-1 = 6, as selling price needs to be larger than buying price.

```
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if len(prices)==0: return 0
        profit = 0
        minimum = prices[0]
        for i in range(1, len(prices)):
            minimum = min(minimum, prices[i])
            profit = max(profit, prices[i]-minimum)
        return profit
```

### 122. Best Time to Buy and Sell Stock II
Say you have an array prices for which the ith element is the price of a given stock on day i.

Design an algorithm to find the maximum profit. You may complete as many transactions as you like (i.e., buy one and sell one share of the stock multiple times).

Note: You may not engage in multiple transactions at the same time (i.e., you must sell the stock before you buy again).

Example 1:

Input: [7,1,5,3,6,4]  
Output: 7  
Explanation: Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5-1 = 4. Then buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6-3 = 3.

#### Solution
The profit equals to summing up all the positive prices[i]-prices[i-1].

```
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if len(prices)<1:
            return 0
        profit = 0
        for i in range(1, len(prices)):
            if prices[i]>prices[i-1]:
                profit = profit + (prices[i]-prices[i-1])
        return profit
```
The above codes are equivalent to one single line:
```
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        return sum( [max(prices[i]-prices[i-1], 0) for i in range(1, len(prices))])
```
###123. Best Time to Buy and Sell Stock III
Design an algorithm to find the maximum profit. **You may complete at most two transactions.** (1 transaction inclues one buy and one sell)

Example 1:

Input: [3,3,5,0,0,3,1,4]  
Output: 6  
Explanation: Buy on day 4 (price = 0) and sell on day 6 (price = 3), profit = 3-0 = 3. Then buy on day 7 (price = 1) and sell on day 8 (price = 4), profit = 4-1 = 3.

#### Solution 1
For each position in prices, break the list into two pieces, and find the max profit of buy-sell-stock-once in each of them.  
Use DP to save time

```
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        N = len(prices)
        if N<1: return 0
        
        left_to_right = [0]*N
        right_to_left = [0]*N
        
        valley = prices[0]
        peak = prices[-1]
        for i in range(1, N):
            left_to_right[i] = max(left_to_right[i-1], prices[i]-valley)
            valley = min(valley, prices[i])
        for i in range(N-2, -1, -1):
            right_to_left[i] = max(right_to_left[i+1], peak-prices[i])
            peak = max(peak, prices[i])
            
        return max([left_to_right[i]+right_to_left[i] for i in range(N)])
```

#### Solution 2.
Another one pass solution with constant space. This one is importan. It can be extended to the general k transactions case.

The intuition is that on each day there are only two possible status: hold a stock, or sell a stock.  
Let prev[i][0] denote the maxProfit after the previous (k-1) transactions at day i and not hold a stock, while prev[i][1] denotes the maxProfit and holds a stock. Then for the k-th transaction, we have:  
`curr[i][1]=max(curr[i-1][1], prev[i-1][0]-prices[i])`  
`curr[i][0]=max(curr[i-1][0], curr[i-1][1]+prices[i])`

For k=2, we can save memory by just tracking the previous day.

```
class Solution(object):
    def maxProfit(self, prices):
        """
        :type prices: List[int]
        :rtype: int
        """
        t1_hold, t2_hold = -float('inf'), -float('inf')
        t1_release, t2_release = 0, 0

        for price in prices:
            # the maximum profit if only one transaction is allowed
            t1_hold = max(t1_hold, -price)
            t1_release = max(t1_release, t1_hold+price)
            # reinvest the gained profit in the second transaction
            t2_hold = max(t2_hold, t1_release-price)
            t2_release = max(t2_release, t2_hold+price)

        return t2_release
```
### 188. Best Time to Buy and Sell Stock IV
Compared to No.123, the condition extends to **at most k transactions**. 

Following Solution 2 of No.123:

```
class Solution:
    def maxProfit(self, k: int, prices: List[int]) -> int:
        if 2*k>=len(prices):
            return sum([max(0, prices[i]-prices[i-1]) for i in range(1, len(prices))])
        
        prev = [[0 for j in [0,1]] for _ in range(len(prices))]

        for j in range(k):
            curr = [[0 for j in [0,1]] for _ in range(len(prices))]
            curr[0][1] = -prices[0]
            for i in range(1, len(prices)):
                curr[i][1] = max(curr[i-1][1], prev[i-1][0]-prices[i])
                curr[i][0] = max(curr[i-1][0], curr[i-1][1]+prices[i])
            prev = curr

        return prev[-1][0]
```
Another cleaner solution is: (not easy to explain)
```
class Solution:
    def maxProfit(self, k: int, prices: List[int]) -> int:
        if 2*k >= len(prices): 
            return sum(max(0, prices[i]-prices[i-1]) for i in range(1, len(prices)))
        
        pnl = [0]*len(prices)
        for _ in range(k):
            val = 0
            for i in range(1, len(pnl)): 
                val = max(pnl[i], val + prices[i] - prices[i-1]) 
                pnl[i] = max(pnl[i-1], val)
        return pnl[-1]
```

Summary?
