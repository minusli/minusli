# 二叉树遍历
### 题单
- [x] 144.二叉树的前序遍历 
- [x] 145.二叉树的后序遍历 
- [x] 94.二叉树的中序遍历
- [x] 102.二叉树的层序遍历
- [x] 429.N叉树的前序遍历
- [x] 107.二叉树的层次遍历II 
- [x] 199.二叉树的右视图 
- [x] 637.二叉树的层平均值 
- [x] 515.在每个树行中找最大值
- [x] 116.填充每个节点的下一个右侧节点指针
- [x] 117.填充每个节点的下一个右侧节点指针II

### 解析：
##### 144/94/145 二叉树的前/中/序遍历
```python
"""
① 递归方式
"""
def traverse(root):
    if not root:
        return
    
    handle(root)  # 前序
    if root.left:
        traverse(root.left)
    handle(root)  # 中序
    if root.right:
        traverse(root.right)
    handle(root)  # 后序
```
```python
"""
② 模拟栈的方式
"""
def traverse(root):
    stack = []
    if root:
        stack.append(root)
    
    while stack:
        node = stack.pop()
        if isinstance(node, TreeNode):
            # 需要注意的是「栈」是「先进后出」的
            # 所以处理过程上和[递归]方式相反的；
            stack.append(handle_partial(node))  # 后续遍历
            if node.right:
                stack.append(node.right)
            stack.append(handle_partial(node))  # 中序遍历    
            if node.left:
                stack.append(node.left)
            stack.append(handle_partial(node))  # 前序遍历

        else:
            handle = node
            handle()
```

##### 102/429/107/199/637/515/116/117 层序遍历及其变种
```python
"""
③首先是广度优先遍历，借助队列来实现
""" 
def traverse(root):
    queue = []
    if root:
        queue.append(root)
    
    while queue:
        node = queue.pop(0)
        handle(node)
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
```
```python
"""
④下面进行改造，增加参数标识节点的层级，实现层序遍历
"""
def traverse(root):
    queue = []
    if root:
        queue.append((root, 0))
    while queue:
        node, depth = queue.pop(0)
        handle(node, depth)
        if node.left:
            queue.append((node.left, depth+1))
        if node.right:
            queue.append((node.right, depth+1))

nodes = []
def handle(node, depth):
    while len(nodes) <= depth:
        nodes.append([])
    nodes[depth].append(node.val)

"""
至此层序遍历结果就是 nodes
107 就是 nodes[::-1]
199 就是 [ns[-1] for ns in nodes]
637 就是 [avg(ns) for ns in nodes]
515 就是 [max(ns) for ns in nodes]
116/117 就是遍历 nodes 每一层，挨着挨着赋值 next
"""
```
### 总结：
记住几种遍历方式的框架代码：
- 递归遍历：①
- 栈式遍历：②
- 层序遍历：③ / ④

剩下的就是在这个基础上进行变化