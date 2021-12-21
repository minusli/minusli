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

##### 617.合并二叉树
分治递归，需要注意返回条件的多种情况