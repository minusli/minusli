# section3 其他
# 提单
- [x] 114.二叉树展开为链表
- [x] 147.对链表进行插入排序
- [x] 146.LRU 缓存
- [x] 328.奇偶链表
- [x] 382.链表随机节点
- [x] 430.扁平化多级双向链表
- [x] 445.两数相加 II
- [x] 622.设计循环队列
- [x] 1019.链表中的下一个更大节点
- [x] 1171.从链表中删去总和值为零的连续节点
- [x] 1367.二叉树中的列表
- [x] 1669.合并两个链表
- [x] 2058.找出临界点之间的最小和最大距离
- [x] 2074.反转偶数长度组的节点

# 解析：
### 114.二叉树展开为链表
1. 先序遍历，记录 pre，每次将 pre.left 设置为 cur;  
2. 最后遍历 list，将 node.right = node.left, node.left = None;
> 为什么设置 pre.left = cur，而不直接 pre.right = cur?  
> 因为先序遍历，当扫描 cur 时，pre.left 就是 cur，此时 left 已经使用了，可以拿来复用；
而 pre.right，还未被使用，这个使用修改 right，会导致先序遍历顺序有问题；

### 147.对链表进行插入排序
1. 实现插入排序 insert 方法
2. 遍历 head，不断插入新队列；需要注意，遍历 head 拿到的节点 n，需要将 n.next 置为 None，避免成环

### 146.LRU 缓存
> 整体思路就是实现一个 linkedhashmap；list 采用双向链表
1. hashmap 可以直接用 map；
2. linklist 需要自己实现，采用双向链表；
3. linklist 需要单独实现 pop(),push(n),remove(n) 接口，别揉在 LRU 中，而是在 LRU 中调用 linklist 的接口；
4. 双向链表的 pop(), push(n), remove(n) 需要注意节点的 pre 和 next 都需要更新，还有 head 和 tail 可能更新

### 328.奇偶链表
1. odd 和 even 链表不停的尾插，最后 odd.tail.next = even.head, return odd.head  
2. odd 和 even 都是 List，实现了 push(node) 方法，采用尾插

### 382.链表随机节点
> 链表遍历+水塘抽样

### 430.扁平化多级双向链表
> if child -> 递归, 返回链表的 head 和 tail

### 445.两数相加 II
1. 头插法 reverse 两个链表  
2. 两个链表按位相加，注意记录进位
3. 未遍历到头的列表再按位和进位相加
4. 最后仍然有进位，则新增 node 插入 ret head 头部

### 622.设计循环队列
1. 数组or链表都可以实现，关键点在抽象好接口
2. enQueue, deQueue, isEmpty, isFull 接口实现，主要依赖于head, tail 的变化，以及 empty 的变更

### 1019.链表中的下一个更大节点
1. 反转单链表
2. 单调栈

### 1171.从链表中删去总和值为零的连续节点
> 反转链表+前缀和
1. 需要注意，更新 cur，presum_map，presum_list

### 1367.二叉树中的列表
> 二叉树遍历+递归判断是否相等

### 1669.合并两个链表
> 注意边界即可(a 空，b 空，list1 空，list2 空等)
> 注意"下标"和"第几个"的区别（一个从 0 开始，一个从 1 开始）

### 2058.找出临界点之间的最小和最大距离
1. 遍历，不断判断 pre,cur,next
2. 发现一个极值，就去更新 min，max，同时更新 min_d
3. 遍历完成后，max_d = max-min
4. 返回时，判断min_d和max_d有效性即可

### 2074.反转偶数长度组的节点
1. group -> head, tail, next, group
2. 如果 group 为奇数则直接跳过
3. group 为偶数，则将 head->tail 进行翻转（头插法），翻转前记得断开链表的前面和后面部分
4. 每次处理 group，额外记录 pre，因为翻转的时候需要使用