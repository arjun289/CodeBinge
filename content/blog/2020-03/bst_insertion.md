---
title: "Binary Search Tree Insertion"
date: 2020-03-01T13:59:48+08:00
draft: false
tags: ["binary_search_trees, datastructures"]
categories: ["BinarySearchTree"]
series: ["datastructure"]
---

Insertion code:

```python
class Node:
    def __init__(self, data=0, left=None, right=None):
        self.data = data
        self.left = left
        self.right = right


class BinarySearchTree():
    def __init__(self):
        self.root = None

    def insert(self, data):
        if not self.root:
            node = Node(data)
            self.root = node
        else:
            self.insertNode(data, self.root)

    # O(logN) if the tree is balanced.
    # worst case scenario can be O(N)
    def insertNode(self, data, parent):
        if data < parent.data:
            if parent.left is None:
                parent.left = Node(data)
            else:
                self.insertNode(data, parent.left)
        else:
            if parent.right is None:
                parent.right = Node(data)
            else:
                self.insertNode(data, parent.right)

```
