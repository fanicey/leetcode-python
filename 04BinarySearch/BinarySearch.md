# Templates
## exact match
```
def binary_search(nums, target):
    """
    binary search，在两个边界，[left, right]中搜寻目标值
    :param array:sorted number array
    :param target: target number
    :return: target index in array, if find nothing return None
    """
    left = 0
    right = len(nums)-1
    while left <= right:
        mid = (left+right)//2
        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return None
```
## search for the closest element
```
def binary_search(nums, target):
    """
    :param array:sorted number array
    :param target: target number
    :return: the index of the closest element that is larger than or equal to target
    """
    left = 0
    right = len(nums)-1
    while left < right:
        mid = (left+right)//2
        if nums[mid] < target:
            left = mid + 1
        else:
            right = mid
    if nums[left] < target:
        return left
    else:
        return None
```
**Note**
1. The stop criteria is `left < right`. If change to `left <= right`, we may end up in an endless loop when left == right.
2. The search always ends up when `left == right`. and `nums[left]` may not have been checked. So if the question is to find the exact match, then we need an additional comparison between nums[left] and target

# Problems
## sqrt(x)
Compute and return the square root of x, where x is guaranteed to be a non-negative integer.
Since the return type is an integer, the decimal digits are truncated and only the integer part of the result is returned.
### solution 1. Binary Search
```
def mySqrt(self, x):
    """
    :type x: int
    :rtype: int
    """
    left, right = 0, x
    while left < right:
        mid = (left + right + 1)//2
        if mid*mid > x:
            right = mid - 1
        else:
            left = mid
   return left
```
### solution 2. Recursive + Bit shift
Use the recursive function sqrt(x) = 2*sqrt(x/4)
```
class Solution(object):
    def mySqrt(self, x):
        """
        :type x: int
        :rtype: int
        """
        if x < 2:
            return x
        s = self.mySqrt(x>>2)<<1
        m = s + 1
        return s if m*m>x else m
```

### solution 3. Newton's method
This is a recursive method to search for the root of $$f(x)=0$$. 
$$x_n = x_{n-1} - \frac{f(x_{n-1})}{f'(x_{n-1})}$$.

For this problem, it is equivalent to find the root of $$x^2-target = 0$$. Use the recursive formular:
$$x_{k+1} = 1/2x_k - target/(2x_k)$$. Stop if $diff(x_k, x_{k+1})<1$


## 540. Single Element in a Sorted Array

You are given a sorted array consisting of only integers where every element appears exactly twice, except for one element which appears exactly once. Find this single element that appears only once.

Follow up: Your solution should run in O(log n) time and O(1) space.

Example 1:
Input: nums = [1,1,2,3,3,4,4,8,8]
Output: 2
```
class Solution(object):
    def singleNonDuplicate(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        # Observation: 
        # before the single element, nums[even_index] == nums[even_index+1]
        # after the singl element: nums[even_index] != nums[even_index+1]
        left, right = 0, len(nums)-1
        while left < right:
            mid = (left + right)//2
            find = (mid%2 == 0 and nums[mid] == nums[mid+1]) or (mid%2 == 1 and nums[mid] == nums[mid-1])
            if find:
                left = mid + 1              
            else:
                right = mid
        return nums[left]
```





