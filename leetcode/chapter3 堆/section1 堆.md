# section1 堆

# 典型代码

```python
# 大根堆实现
import math


class Heap:
    def __init__(self):
        self.heap = []

    def shiftDown(self):
        # pop 的时候用
        if not self.heap:
            return
        index = 0
        while index < len(self.heap):
            left = index * 2 + 1
            right = left + 1
            max = -1
            if right < len(self.heap):
                max = right if self.val(right) > self.val(left) else left
            elif left < len(self.heap):
                max = left
            
            if max == -1:
                break
            
            if self.val(index) < self.val(max):
                self.swap(index, max)
                index = max
            else:
                break

    def shiftUp(self):
        # push 的时候用
        if not self.heap:
            return
        index = len(self.heap) - 1
        while index > 0:
            parent = math.floor((index-1)/2)
            
            if self.val(parent) < self.val(index):
                self.swap(parent, index)
                index = parent
            else:
                break

    def push(self, v):
        self.heap.append(v)
        self.shiftUp()

    def pop(self):
        if not self.heap:
            return
        ret = self.heap[0]
        self.heap[0] = self.heap[-1]
        self.heap.pop()
        self.shiftDown()

    def swap(self, i, j):
        self.heap[i], self.heap[j] = self.heap[j], self.heap[i]
        
    def val(self, i):
        return self.heap[i]

```

# 提单
- [x] 703.数据流中的第 K 大元素
- [x] 1046.最后一块石头的重量
- [x] 2099.找到和最大的长度为 K 的子序列
- [x] 373.查找和最小的 K 对数字
- [x] 378.有序矩阵中第 K 小的元素
- [x] 973.最接近原点的 K 个点
- [x] 295.数据流的中位数

# 解析
### 703.数据流中的第 K 大元素
> 最小堆，shiftup 和 shiftdown 实现，pop 和 push 实现
1. 利用最小堆实现，每次添加都判断长度，超长则 pop

### 1046.最后一块石头的重量
> 最大堆实现，每次 pop 两个，撞碎之后再 push 进去

### 2099.找到和最大的长度为 K 的子序列
> TopK 问题，最小堆实现，需要记录位置，用于最后的排序输出

### 373.查找和最小的 K 对数字
> TopK 最小=大根堆 TopK 问题；  
> 注意双层遍历的内层循环中，和是递增的，一旦大于 top，则没必要再继续循环了

### 378.有序矩阵中第 K 小的元素
> 第 k 小 = topK 小 = 大根堆 TopK 问题；
> k 是矩阵行数
> 每次 pop 出来的数，都把它右边的数 push 回去

### 973.最接近原点的 K 个点
> 最近 k 个点 = 距离 topK 小 = 大根堆 TopK 问题；

### 295.数据流的中位数
> 大根堆+小根堆
> 保持两个对的长度大小，同时每次都保持大根堆 top <= 小根堆 top