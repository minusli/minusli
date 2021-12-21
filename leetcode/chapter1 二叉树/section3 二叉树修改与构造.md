# section3 二叉树修改与构造
### 题单
- [x] 226.翻转二叉树
- [x] 106.从中序与后序遍历序列构造二叉树 
- [x] 105.从前序与中序遍历序列构造二叉树
- [x] 654.最大二叉树
- [x] 617.合并二叉树

### 解析
##### 226.翻转二叉树
基于 dfs，分治处理即可：
```python
def traverse(root):
    if not root:
        return

    temp = root.left
    root.left = root.right
    root.right = temp

    traverse(root.left)
    traverse(root.right)
```
##### 105/106 从中序与先/后序遍历序列构造二叉树
典型的分支递归：
```python
class Solution(object):
    def buildTree(self, inorder, postorder):
        """
        :type inorder: List[int]
        :type postorder: List[int]
        :rtype: TreeNode
        """
        self.postorder_order = {postorder[i]: i for i in range(len(postorder))}
        return self.build(inorder, postorder)

    def build(self, inorder, postorder):
        if not postorder:
            return
        if not inorder:
            return

        mid_value = postorder.pop(-1)
        node = TreeNode(mid_value)

        mid_index = inorder.index(mid_value)
        left_inorder = inorder[:mid_index]
        right_inorder = inorder[mid_index+1:]
        left_postorder = sorted(left_inorder, key=lambda item: self.postorder_order[item])
        right_postorder = sorted(right_inorder, key=lambda item: self.postorder_order[item])
        left_node = self.build(left_inorder, left_postorder)
        right_node = self.build(right_inorder, right_postorder)
        if left_node:
            node.left = left_node
        if right_node:
            node.right = right_node
        return node
```
##### 654.最大二叉树
分治递归
```python
class Solution(object):
    def constructMaximumBinaryTree(self, nums):
        """
        :type nums: List[int]
        :rtype: TreeNode
        """
        max_i, max_v = self.findMax(nums)
        if max_v is None:
            return None
        node = TreeNode(max_v)
        left = self.constructMaximumBinaryTree(nums[:max_i])
        right = self.constructMaximumBinaryTree(nums[max_i+1:])
        if left:
            node.left = left
        if right:
            node.right = right
        return node

    def findMax(self, nums):
        ret_i = None
        ret_v = None
        for i in range(len(nums)):
            v = nums[i]
            if ret_v is None or ret_v < v:
                ret_i = i
                ret_v = v
        return ret_i, ret_v
```

##### 617.合并二叉树
分治递归，需要注意返回条件的多种情况
```python
class Solution(object):
    def mergeTrees(self, root1, root2):
        """
        :type root1: TreeNode
        :type root2: TreeNode
        :rtype: TreeNode
        """
        if not root1:
            root1 = root2
            root2 = None
        self.merge(root1, root2, None, True)
        return root1

    def merge(self, root1, root2, p1, is_left):
        if root1 and root2:
            root1.val += root2.val
            self.merge(root1.left, root2.left, root1, True)
            self.merge(root1.right, root2.right, root1, False)
        elif root1 and not root2:
            pass
        elif not root1 and root2 and p1:
            if is_left:
                p1.left = root2
            else:
                p1.right = root2
```