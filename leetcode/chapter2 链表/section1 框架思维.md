# section1 基础框架

# 框架思维:

学习链表，可大致分为 4 块来学习：

1. 节点遍历：顺序/逆序遍历(二叉树遍历影子)
2. 节点插入：头插法，尾插法
3. 节点删除：一定要注意 pre
4. 双指针操作：fast 和 slow 用法

- 注意事项：
1. 遍历节点，一定要注意 `cur = cur.next`
2. 寻找中间节点，循环判断是 `fast and fast.next`
3. 链表根据 k 分组中，判断 tail 一定是 `step and tail and tail.next`，同时别忘了 `step -= 1; tail = tail.next`

# 节点遍历：

- 顺序遍历

```python
def traverse(node):
    """
    顺序遍历
    """
    if not node:
        return
    cur = node
    while cur:
        handle(cur)
        cur = cur.next
```

- 逆序遍历: 可以利用栈来实现，也可以是递归; 可以看到二叉树遍历的影子；

```python
def traverse(node):
    """
    逆序遍历: 可以利用栈来实现，也可以是递归
    """
    if not node:
        return

    # 是不是和二叉树遍历很像
    # handle(node)  # 顺序遍历
    traverse(node.next)
    handle(node)  # 逆序遍历 
```

# 节点插入

- 头插法：插入顺序和链表顺序相反

```python
def insert(*nodes):
    """
    头插法：插入顺序和链表顺序相反
    """
    head = None

    for node in nodes:
        node.next = head
        head = node
    return head
```

- 尾插法：插入顺序与链表顺序一致，额外维护 tail

```python
def insert(*nodes):
    """
    尾插法：插入顺序与链表顺序一致，额外维护 tail
    """
    head = None
    tail = None
    for node in nodes:
        if not head:
            head = tail = node
            continue

        tail.next = node
        tail = node

    return head
```

- 中间插入：一定要确定 pre 节点

```python
def insert(pre, node, head):
    """
    中间插入：一定要确定 pre 节点
    """
    if not node:
        return head

    if not pre:
        node.next = head
        return node  # 如果插入节点的前驱节点是空，说明当前插入节点是头节点

    node.next = pre.next
    pre.next = node

    return head
```

# 节点删除

```python
def delete(pre, node, head):
    """
    删除节点：一定要确定 pre
    """
    if node:
        return head

    if not pre:
        return head.next

    pre.next = node.next
    node.next = None

    return head
```

# 双指针操作

这块儿比较经典，经典问题：

- 19.删除链表的倒数第 N 个结点
- 876.链表的中间结点
- 141.环形链表

```python
"""
如何定位倒数第 n 个节点
"""


def find(head, n):
    if not head:
        return

    if n <= 0:
        return

    fast = head
    slow = head

    while n and fast:
        n -= 1
        fast = fast.next

    if n > 0:  # n > 链表长度
        return

    while fast:
        fast = fast.next
        slow = slow.next

    return slow
```

```python
"""
快慢指针找中间节点
"""


def find(head):
    if not head:
        return

    fast = head
    slow = head
    while fast and fast.next:
        fast = fast.next
        fast = fast.next
        slow = slow.next

    return slow
```

```python
"""
快慢指针判断环
"""


def circle(head):
    if not head:
        return False

    fast = head
    slow = head
    while fast and fast.next:
        fast = fast.next
        fast = fast.next
        slow = slow.next
        if fast == slow:
            return True

    return False
```