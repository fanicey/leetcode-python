# Intro
1. use dummy head if the edge case is hard to handle
2. two pointers

# Problems
## 206. Reverse Linked List
Reverse a singly linked list.

Example:
Input: 1->2->3->4->5->NULL
Output: 5->4->3->2->1->NULL
### Solution 1. Iterate 

```
class Solution(object):
    def reverseList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """  
        prev, curr = None, head
        while curr:
            curr.next, prev, curr = prev, curr, curr.next
        return prev
```
### Solution2. Recursive
```
class Solution(object):
    def reverseList(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """  
        if head==None or head.next==None: return head
        p1 = self.reverseList(head.next)
        head.next.next = head
        head.next = None
        return p1
```

## 19. Remove Nth Node From End of List
Given a linked list, remove the n-th node from the end of list and return its head.

Example:

Given linked list: 1->2->3->4->5, and n = 2.

After removing the second node from the end, the linked list becomes 1->2->3->5.

Note:
Given n will always be valid.

Follow up:
Could you do this in one pass?
```
class Solution(object):
    def removeNthFromEnd(self, head, n):
        """
        :type head: ListNode
        :type n: int
        :rtype: ListNode
        """
        # two pointers
        # Assume n is always valid!
        # Note: what if head itself is going to be removed?
        prehead = ListNode(0, head)
        fast, slow = head, prehead
        for i in range(n):
            fast = fast.next
        while fast:
            fast = fast.next
            slow = slow.next
        slow.next = slow.next.next
        return prehead.next
```


