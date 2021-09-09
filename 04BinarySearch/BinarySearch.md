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
3. The template is designed for right-side inclusive problems. If we want left-side inclusive, the `mid` calculation, the update logic should change. See the problem sqrt(x) for more details.

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
This is a recursive method to search for the root of  $$f(x)=0$$. 
$$ x_n = x_{n-1} - \frac{f(x_{n-1})}{f'(x_{n-1})} $$.

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


### 4 . Median of Two Sorted Arrays (Hard)

Given two sorted arrays nums1 and nums2 of size m and n respectively, return the median of the two sorted arrays.

The overall run time complexity should be O(log (m+n)).

Example 1:

Input: nums1 = [1,3], nums2 = [2]
Output: 2.00000  
Explanation: merged array = [1,2,3] and median is 2.

Explanation:

First, the median depends on the total length:
  * if (m+n) is odd, median is the (m+n+1)/2-th element
* if (m+n) is even, median is the average of (m+n+1)/2-th and (m+n+2)/2-th 

Solution 1 is a O(log (min(m,n))) algorithm. It iterates over the shorter array, to find the valid partitions such that:
 * total elements in the left partitions of nums1 and nums2 is (m+n+1)/2
* satisfy nums1[left]<nums2[right] and nums1[right]>nums2[left]

The hard part is to handle the edge cases. Notice how to use float('inf') to concisely handle the out-of-range cases.

Solution 1.
```
class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        # iterate search over shorter array
        m, n = len(nums1), len(nums2)
        if m > n:
            return self.findMedianSortedArrays(nums2, nums1)
        if m == 0:
            return (nums2[(n-1)//2] + nums2[n//2])/2
    
        half = (m + n + 1) // 2
        l,r = 0, m-1
        while l<=r:
            m1 = (l+r) // 2   # m1+1 elements in left partition in nums1
            m2 = half - m1 -2  # m2=-1 if half=1, m1=0, m2=-1
            # m1 = -1, m2 = 1
            leftA = nums1[m1] if m1>=0 else -float('inf')
            leftB = nums2[m2] if m2>=0 else -float('inf')
            rightA = nums1[m1+1] if m1+1<len(nums1) else float('inf')
            rightB = nums2[m2+1] if m2+1<len(nums2) else float('inf')
            # check if this is a valid partition
            if leftA<=rightB and leftB<=rightA:
                if (m+n) % 2 == 0:
                    return (max(leftA, leftB)+min(rightA, rightB))/2
                else:
                    return max(leftA, leftB)
            elif leftA > rightB:
                r = m1 - 1 
            else:
                l = m1 + 1
        return None
```

Solution 2. is a O(log(m+n)) solution. It's binary search + recursion. To find the k-th element, each time compare the i+k/2-th element in nums1 and the j+k/2-th element in nums2, and filter out the smaller k/2 elements.  Then the problem reduces to find the (k-k/2)th element in the remaining arrays. 
```
class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
    
        def findKthSortedArrays(nums1, i, nums2, j, k):
            # helper function to find the k-th element after merging nums1 and nums2
            # edge case 1. one array has reached end
            if i >= len(nums1):
                return nums2[j+k-1]
            if j >= len(nums2):
                return nums1[i+k-1]
            # edge case 2. 
            if k==1:
                return min( nums1[i],nums2[j] )
            # general case
            ele1 = nums1[i+k//2-1] if i+k//2-1 < len(nums1) else float('inf')
            ele2 = nums2[j+k//2-1] if j+k//2-1 < len(nums2) else float('inf')
            if ele1 < ele2:
                i = i+k//2
            else:
                j = j+k//2
            return findKthSortedArrays(nums1, i, nums2, j, k-k//2)
                    
        # assume at least one array has non-zero length
        m, n = len(nums1), len(nums2)
        if (m+n) % 2 == 0:
            return (findKthSortedArrays(nums1, 0, nums2, 0, (m+n+1)//2) + 
                   findKthSortedArrays(nums1, 0, nums2, 0, (m+n+2)//2) )/2
        else:
            return findKthSortedArrays(nums1, 0, nums2, 0, (m+n+1)//2)
        
```

### 33. Search in Rotated Sorted Array

There is an integer array `nums` sorted in ascending order (with **distinct** values).

Prior to being passed to your function, `nums` is **rotated** at an unknown pivot index `k` (`0 <= k < nums.length`) such that the resulting array is `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]` (**0-indexed**). For example, `[0,1,2,4,5,6,7]` might be rotated at pivot index `3` and become `[4,5,6,7,0,1,2]`.

Given the array `nums` **after** the rotation and an integer `target`, return *the index of* `target` *if it is in* `nums`*, or* `-1` *if it is not in* `nums`.

You must write an algorithm with `O(log n)` runtime complexity.

My solution: recursive + binary search

```
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        
        def searchSorted(nums, left, right, target):
            while (left<=right):
                mid = (left + right) // 2
                if nums[mid] == target:
                    return mid
                elif nums[mid] > target:
                    right = mid - 1
                else:
                    left = mid + 1
            return -1
                
        
        def searchRotated(nums, left, right, target):
            if left == right:
                return left if nums[left]==target else -1
            
            mid = (left + right) // 2
            leftResult = -1
            if nums[left] <= nums[mid]:
                leftResult = searchSorted(nums, left, mid, target)
            else:
                leftResult = searchRotated(nums, left, mid, target)
            rightResult = -1
            if nums[mid] <= nums[right]:
                rightResult = searchSorted(nums, mid+1, right, target)
            else:
                rightResult = searchRotated(nums, mid+1, right, target)
            if leftResult != -1:
                return leftResult
            elif rightResult != -1:
                return rightResult
            else:
                return -1
            
        return searchRotated(nums, 0, len(nums)-1, target)
```



A standard one-pass binary search:

```
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left, right = 0, len(nums)-1
        while (left<=right):
            mid = (left + right) // 2
            if target == nums[mid]:
                return mid
            
            if nums[mid] >= nums[left]:
                if target >= nums[left] and target < nums[mid]:
                    right = mid - 1
                else:
                    left = mid + 1 
            else:
                if target <= nums[right] and target > nums[mid]:
                    left = mid + 1
                else:
                    right = mid - 1
        return -1
```

### 34. Find First and Last Position of Element in Sorted Array

Given an array of integers `nums` sorted in ascending order, find the starting and ending position of a given `target` value.

If `target` is not found in the array, return `[-1, -1]`.

You must write an algorithm with `O(log n)` runtime complexity.

Solution: A natrual idea is breaking the problem into two familiar subproblems:

1. find the closest number smaller than target
2. find the closest number larger than target.

The following solution is using a different approach: recursively filter out out-of-range elements.

```
class Solution:
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        # convert to: removing the subarrays in List that not equals to target
        
        def helper(nums, start, end, target):
            if start>end:
                return [-1, -1]
            if start==end:
                return [start, end] if nums[start] == target else [-1, -1]
            
            mid = (start + end)//2
            if nums[mid] < target:
                return helper(nums, mid+1, end, target)
            elif nums[mid] > target:
                return helper(nums, start, mid-1, target)
            else:
                result = [mid, mid]
                pos1 = helper(nums, start, mid-1, target)
                pos2 = helper(nums, mid+1, end, target)
                if pos1[0] != -1:
                    result[0] = pos1[0]
                if pos2[1] != -1:
                    result[1] = pos2[1]
                return result
        
        return helper(nums, 0, len(nums)-1, target)
```

