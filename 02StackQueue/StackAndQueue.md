# Basics
### Common functions in stack

### Common functions in queue

### Python implementation
1. list: mostly optimized for fixed length operations. Need O(n) memory movement costs for pop(0) and insert(0, v).
2. collections.deque has many methods in common with list.  
    * Construction: `deque()`, `deque(nums)`
    * Addtional methods: `appendleft()`, `popleft`

# Problems
## 225. Implement Stack using Queues
Implement the following operations of a stack using queues.

* push(x) -- Push element x onto stack.
* pop() -- Removes the element on top of the stack.
* top() -- Get the top element.
* empty() -- Return whether the stack is empty.

This solution uses 1 queue. pop() takes O(1), and push() takes O(n). The trick is when push, keep the queue in a reverse order (do a cyclic shift)

```
class MyStack(object):
    # use one queue to implement

    def __init__(self):
        """
        Initialize your data structure here.
        """
        self.queue = []
        

    def push(self, x):
        """
        Push element x onto stack.
        :type x: int
        :rtype: None
        """
        length = len(self.queue)
        self.queue.append(x)
        for i in range(length):
            self.queue.append(self.queue[i])
        self.queue = self.queue[length:]
        
        
    def pop(self):
        """
        Removes the element on top of the stack and returns that element.
        :rtype: int
        """
        return self.queue.pop(0)
        

    def top(self):
        """
        Get the top element.
        :rtype: int
        """
        return self.queue[0]
        

    def empty(self):
        """
        Returns whether the stack is empty.
        :rtype: bool
        """
        return len(self.queue)==0
```
## 232. Implement Queue using Stacks

Implement the following operations of a queue using stacks.

* push(x) -- Push element x to the back of queue.
* pop() -- Removes the element from in front of queue.
* peek() -- Get the front element.
* empty() -- Return whether the queue is empty.
```
class MyQueue(object):

    def __init__(self):
        """
        Initialize your data structure here.
        """
        self.stack_in = []
        self.stack_out = []
        

    def push(self, x):
        """
        Push element x to the back of queue.
        :type x: int
        :rtype: None
        """
        self.stack_in.append(x)
        

    def pop(self):
        """
        Removes the element from in front of queue and returns that element.
        :rtype: int
        """
        if len(self.stack_in) == 0 and len(self.stack_out) == 0: return None
        if len(self.stack_out) == 0:
            while self.stack_in:
                self.stack_out.append(self.stack_in.pop())
        return self.stack_out.pop()
        

    def peek(self):
        """
        Get the front element.
        :rtype: int
        """
        if len(self.stack_out) > 0:
            return self.stack_out[-1]
        while self.stack_in:
            self.stack_out.append(self.stack_in.pop())
        if len(self.stack_out) == 0:
            return None
        else:
            return self.stack_out[-1]
        

    def empty(self):
        """
        Returns whether the queue is empty.
        :rtype: bool
        """
        return len(self.stack_in)==0 and len(self.stack_out)==0
```

## 155. Min Stack
Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

* push(x) -- Push element x onto stack.
* pop() -- Removes the element on top of the stack.
* top() -- Get the top element.
* getMin() -- Retrieve the minimum element in the stack.

Solution: use one stack
```
class MinStack(object):

    def __init__(self):
        """
        initialize your data structure here.
        """
        self.stack = []
        self.min = None 

    def push(self, x):
        """
        :type x: int
        :rtype: None
        """
        if self.min==None or x<=self.min:
            self.stack.append(self.min)
            self.min = x
        self.stack.append(x) 
            
    def pop(self):
        """
        :rtype: None
        """
        x = self.stack.pop()
        if x == self.min:
            self.min = self.stack.pop()

    def top(self):
        """
        :rtype: int
        """
        return self.stack[-1]
        

    def getMin(self):
        """
        :rtype: int
        """
        return self.min
```
**Note** A similar question is 716. Max Stack. It additionally requests to implement popMax(), which is removing the max value from the stack. To realize it, call stack.pop() until you find the element == max_value. So O(N) time complexity.