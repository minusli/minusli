# section2 快慢指针运用
# 提单
- [x] 109.有序链表转换二叉搜索树
- [x] 142.环形链表 II
- [x] 143.重排链表
- [x] 148.排序链表

# 解析：
### 109.有序链表转换二叉搜索树
1. 利用快慢指针找到 mid 节点；
2. 将 mid 左右拆成两个链表，然后递归 mid 左右两个链表构造平衡bst；注意需要 pre.next = None；

### 142.环形链表 II
1. 利用快慢指针找相遇，有环则能相遇，无环则 fast||fast.next 为 None
2. 再从 head 和 slow 一起开始，不停 next 找相遇，相遇点就是环入口

### 143.重排链表
1. 利用快慢指针找 mid
2. 利用头插法返序 mid 之后的节点，需要将 mid 的 pre.next 置为空
3. 将 mid 之前的链表，和 mid 之后的返序链表进行合并

### 148.排序链表
> 链表归并排序，递归归并，快慢指针找 mid，merge 有序 list
1. 利用快慢指针找 mid，需要将 mid 的 pre.next 设为 None，这样就将原本 list 从中间划为两个 list
2. 如果 mid == head，说明只有 1 个节点，这时直接返回 head 即可
3. 如果 list1.next != None，说明 list1 有两个节点，则递归进一步划分
4. 同理 list2
5. 递归处理 list1 和 list2 之后，则两个 list 都是有序的了，最后返回 merge_with_sort(l1, l2) 即可
