# section4 二叉搜索树属性
### 题单
- [x] 700.二叉搜索树中的搜索
- [x] 98.验证二叉搜索树
- [x] 530.二叉搜索树的最小绝对差
- [x] 501.二叉搜索树中的众数

### 解析
##### 700.二叉搜索树中的搜索
> `二叉搜索树` + `dfs-find` 

实质上就是在 dfs 上的变种；
```python
class Solution(object):
    def searchBST(self, root, val):
        """
        :type root: TreeNode
        :type val: int
        :rtype: TreeNode
        """
        if not root:
            return

        if root.val == val:
            return root
        if val < root.val:
            return self.searchBST(root.left, val)
        else:
            return self.searchBST(root.right, val)
```
##### 98.验证二叉搜索树
> `二叉搜索树` + `中序遍历有序`

思路肯定是：左子树的最大值<小于当前节点<右节点的最小值
搜索二叉树中序遍历就是有序的，同时能保证上述条件；
所以只用判断中序遍历是否有序即可；
```python
class Solution(object):
    def isValidBST(self, root):
        """
        :type root: TreeNode
        :rtype: bool
        """
        self.pre = None
        self.flag = True
        self.midorder = []
        self.traverse(root)
        return self.flag

    def traverse(self, root):
        if not root:
            return
        if not self.flag:
            return

        if root.left:
            self.traverse(root.left)

        val = root.val
        if self.pre is not None and val <= self.pre:
            self.flag = False
        else:
            self.pre = val

        if root.right:
            self.traverse(root.right)
```
##### 530.二叉搜索树的最小绝对差
> `二叉搜索数`+`中序遍历有序`

二叉搜索数中序遍历有序，然后中序遍历中相邻两个取差值，取最小即可；

##### 501.二叉搜索树中的众数
> `二叉搜索数`+`中序遍历有序`

中序遍历有序，保证相同数一定在一起，然后遍历计数即可；
需要注意众数有多个，相当于每次记录众数，需要记录众数val 和众数的次数，新众数进来后，要判断次数，相等则 append，大于则丢弃，小于取消之前记录的众数，使用当前众数以及次数；


