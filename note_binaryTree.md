# 树

树是一种抽象数据类型（ADT）或是实现这种抽象数据类型的数据结构，用来模拟具有树状结构性质的数据集合。它是由$n(n>0)$个有限节点组成一个具有层次关系的集合。
具有以下的特点：
    
每个节点都只有有限个子节点或无子节点；
没有父节点的节点称为根节点；
每一个非根节点有且只有一个父节点；
除了根节点外，每个子节点可以分为多个不相交的子树；
树里面没有环路。

**重点：树的遍历方法及其特点**

## 二叉树

二叉树的定义：
```python
class treeNode():
    def __init__(self,val):
        self.val = val
        self.l_child = None
        self.r_child = None
```

## 树的遍历

树的遍历分为前序遍历、中序遍历、后序遍历、层次遍历。前中后三种遍历方法所指为根节点(root)的访问顺序，三种遍历都有递归和迭代两种不同的实现方法。以下树的遍历均以二叉树为例。

### 前序遍历

先访问根节点，再访问左子节点，最后访问右子节点。

递归方法：
```python
def preTransversal(tree_node):
    if not tree_node :
        return None
    print(tree_node.val)
    preTransversal(tree_node.l_child)
    preTransversal(tree_node.r_child)
```
利用栈的后入先出性质实现前序遍历，迭代方法：
```python
def preTransversal(tree_node):
    if not tree_node :
        return None
    stack = []
    while len(stack) or tree_node:
        while tree_node:
            print(tree_node.val)
            stack.insert(0,tree_node)
            tree_node = tree_node.l_child
        if len(stack):
            node = stack.pop(0)
            tree_node = node.r_child     
```
### 中序遍历

先访问左子节点，再访问根节点，最后访问右子节点。

递归方法：
```python
def inTransversal(tree_node):
    if not tree_node :
        return None
    inTransversal(tree_node.l_child)
    print(tree_node.val)
    inTransversal(tree_node.r_child)
```
利用栈的后入先出性质实现中序遍历，与前序遍历相比只是打印父节点的位置发生了变化，迭代方法：
```python
def inTransversal(tree_node):
    if not tree_node :
        return None
    stack = []
    while len(stack) or tree_node:
        while tree_node:
            stack.insert(0,tree_node)
            tree_node = tree_node.l_child
        if len(stack):
            node = stack.pop(0)
            print(node.val)
            tree_node = node.r_child
```

### 后序遍历

先访问左子节点，再访问右子节点，最后访问根节点。

递归方法：
```python
def posTransversal(tree_node):
    if not tree_node :
        return None
    posTransversal(tree_node.l_child)
    posTransversal(tree_node.r_child)
    print(tree_node.val)
```

后序遍历较为复杂，需要判断当前是否返回至父节点，判断返回的方法为该节点的右子节点已被访问或者为None，迭代方法为：
```python
def posTransversal(tree_node):
    if not tree_node:
        return None
    stack = []
    last_node = None
    while len(stack) or tree_node:
        while tree_node:
            stack.insert(0,tree_node)
            tree_node = tree_node.l_child
        if len(stack):
            tree_node = stack.pop(0)
            # 判断该节点的子节点是否已访问完毕
            if tree_node.r_child == None or tree_node.r_child == last_node:
                print(tree_node.val)
                last_node = tree_node
                # tree_node标记为None，防止内while再次将其添加至栈中
                tree_node = None
            else:
                # 若还未访问则继续添加至栈中，访问其右子节点
                stack.insert(0,tree_node)
                tree_node = tree_node.r_child
```

### 层次遍历

深度优先遍历，即为层次遍历，先访问树的第一层节点，再访问树的第二层节点...一直到访问到最下面一层节点。同层节点，以从左到右的顺序依次访问。

利用队列的先入先出实现二叉树的层次遍历。

```python
def depthFirst(tree_node):
    if not tree_node :
        return None
    stack = [tree_node]
    while len(stack) > 0:
        node = stack.pop(0)
        print(node.val)
        if node.l_child:
            stack.append(node.l_child)
        if node.r_child:
            stack.append(node.r_child)
```

## 题目

### 面试题07. [重建二叉树](https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/)

根据一棵树的前序遍历与中序遍历构造二叉树。

注意:
你可以假设树中没有重复的元素。

例如，给出

    前序遍历 preorder = [3,9,20,15,7]
    中序遍历 inorder = [9,3,15,20,7]
返回如下的二叉树：

        3
       / \
      9  20
        /  \
       15   7

**题解:**

```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> TreeNode:
        if not len(preorder):
            return None
        val = preorder[0]
        index = inorder.index(val)
        root = TreeNode(val)
        root.left = self.buildTree(preorder[1:index+1],inorder[:index])
        root.right = self.buildTree(preorder[index+1:],inorder[index+1:])
        return root
```

    优化代码：使用字典结构存储中序遍历中节点位置。
```python
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> TreeNode:
        self.dic, self.po = {}, preorder
        for i in range(len(inorder)):
            self.dic[inorder[i]] = i
        return self.recur(0, 0, len(inorder) - 1)

    def recur(self, pre_root, in_left, in_right):
        if in_left > in_right: 
            return None
        root = TreeNode(self.po[pre_root]) 
        i = self.dic[self.po[pre_root]]    # 搜索根节点在中序遍历中的索引，从而可对根节点、左子树、右子树完成划分。
        root.left = self.recur(pre_root + 1, in_left, i - 1)    # 开启左子树的下层递归
        root.right = self.recur(i - in_left + pre_root + 1, i + 1, in_right) # 开启右子树的下层递归
        return root
```

### 538.[把二叉搜索树转换为累加树](https://leetcode-cn.com/problems/convert-bst-to-greater-tree/)

给定一个二叉搜索树（Binary Search Tree），把它转换成为累加树（Greater Tree)，使得每个节点的值是原来的节点值加上所有大于它的节点值之和。

例如：

输入: 原始二叉搜索树:

              5
            /   \
           2     13

输出: 转换为累加树:

             18
            /   \
          20     13

**题解**

    反向中序遍历
递归法：
```python
class Solution():
    def __init__(self):
        self.total = 0

    def convertBST(self, root: TreeNode) -> TreeNode:
        if root:
            self.convertBST(root.right)
            self.total += root.val
            root.val = self.total
            self.convertBST(root.left)
        return root
```
迭代法：
```python
class Solution():
    def convertBST(self,root:TreeNode) -> TreeNode:
            if not root:
                return None
            node = root
            stack = []
            sum_num = 0
            while len(stack) or node:
                while node:
                    stack.insert(0,node)
                    node = node.right
                if len(stack):            
                    node = stack.pop(0)
                    sum_num += node.val
                    node.val = sum_num
                    node = node.left
            return root
```
