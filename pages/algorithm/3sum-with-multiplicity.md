---

title: Binary Search Tree Iterator
last_updated: Apr 20, 2022
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: binary-search-tree-iterator.html


---


## #. Problem

[Link](https://leetcode.com/problems/binary-search-tree-iterator/)



## 2. My approach

Keep all the nodes in a list.

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class BSTIterator:
    lst = []

    def __init__(self, root: Optional[TreeNode]):
        self.inOrder(root)
        
    def inOrder(self, root):
        if not root: return
        
        self.inOrder(root.left)
        self.lst.append(root)
        self.inOrder(root.right)
        
    def next(self) -> int:
        return self.lst.pop(0).val

    def hasNext(self) -> bool:
        return self.lst


# Your BSTIterator object will be instantiated and called as such:
# obj = BSTIterator(root)
# param_1 = obj.next()
# param_2 = obj.hasNext()
```

| Time(O(N)) | Space(O(N)) |
| ---------- | ----------- |
| 195 ms     | 20.3 MB     |

## 2. Solution - Stack

It also uses O(N) of space, however, except the worst case, it won't have O(N) space. 
Bast case, it would have O(1) of space.

If the iteration goes from *0* to *N*, time complexity shouldn't be very different from the first approach. That's because both approaches travel N times. However, if one only iterate till N/2, 2nd approach would be faster than the 1st, because 2nd approach has more calculations than just pop.  

```python
class BSTIterator:
    stack = []

    def __init__(self, root: Optional[TreeNode]):
        self.stack = []
        self.appendStack(root)
        
    def next(self) -> int:
        node = self.stack.pop()
        root = node.right
        self.appendStack(root)
        return node.val
        
    def hasNext(self) -> bool:
        return self.stack

    def appendStack(self, root):
        while root:
            self.stack.append(root)
            root = root.left

# Your BSTIterator object will be instantiated and called as such:
# obj = BSTIterator(root)
# param_1 = obj.next()
# param_2 = obj.hasNext()
```


| Time(O(N)) | Space(O(N)) |
| ---------- | ----------- |
| 100 ms      | 20.3 MB     |



## 3. Epilogue 

What I've learned from this exercise:

- quite cleaver way of using Binary Search Tree
