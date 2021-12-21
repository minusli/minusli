# section6 二叉搜索树修改与构造
### 题单
- [x] 701.二叉搜索树中的插入操作
- [x] 450.删除二叉搜索树中的节点
- [ ] 669.修剪二叉搜索树
- [x] 108.将有序数组转换为二叉搜索树
- [x] 538.把二叉搜索树转化为累加树

### 解析
##### 701.二叉搜索树中的插入操作
> `二叉搜索树` + `dfs-find`

##### 450.删除二叉搜索树中的节点
> `二叉搜索树` + `dfs-find` + `dfs-min`/`dfs-max`

注意几种情况的处理：
1. 被删除节点无左右节点 => 父节点的对应子节点设为 None
2. 被删除节点只有左/右节点 => 父节点的对应子节点设为 左/右 节点
3. 被删除节点同时有左右节点 => 被删除节点的值替换为将左子树的最大值，将左子树的最大值节点删除(因为已经是最大值节点了，说明一定无右节点了，删除比较方便)

##### 669.修剪二叉搜索树
太麻烦了，就不做了，如果让做这个题，感觉属于劝退

##### 108.将有序数组转换为二叉搜索树
> `分治递归构造` + `二叉搜索树` 

注意递归结束条件：len(lst) <= 3即可
```python
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> TreeNode:
        return self.build(nums, 0, len(nums) - 1)

    def build(self, nums, start, end):
        length = end - start + 1
        if length <= 0:
            return None
        if length == 1:
            return TreeNode(nums[start])
        if length == 2:
            n = TreeNode(nums[end])
            n.left = TreeNode(nums[start])
            return n
        if length == 3:
            n = TreeNode(nums[start + 1])
            n.left = TreeNode(nums[start])
            n.right = TreeNode(nums[end])
            return n

        mid = (start + end) // 2
        n = TreeNode(nums[mid])
        n.left = self.build(nums, start, mid - 1)
        n.right = self.build(nums, mid + 1, end)
        return n
```

##### 538.把二叉搜索树转化为累加树
> `二叉搜索树` + `逆中序遍历`

二叉搜索书的逆中序遍历，就是的逆序数列；
遍历到当前节点，能保证比自己大的节点已经被遍历了
累计值通过 pre 不断累计，遍历到当前节点时累加给当前节点即可；
```python
class Solution:
    def convertBST(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        self.pre = 0
        self.traverse(root)
        return root

    def traverse(self, root):
        if not root:
            return

        if root.right:
            self.traverse(root.right)

        val = root.val + self.pre
        self.pre += root.val
        root.val = val

        if root.left:
            self.traverse(root.left)
```
