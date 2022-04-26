# MinorGC、MajorGC、FullGC以及MixedGC区别

### 结论：

- Minor GC：回收年轻代(Eden & Survivor)
- Major GC：回收老年代
- Full GC：回收整个对空间(年轻代、老年代以及元空间)
- Mixed GC：G1 独有，回收整个年轻代+部分老年代、

### 扩展

HotSpot VM的实现中，对 GC 分类只有两种：

- Partial GC：部分回收模式，不回收整个堆
    - Young GC：只回收年轻代(serial new)
    - Old GC：只回收老年代(cms)
    - Mixed GC：回收整个年轻代+部分老年代(G1)
- Full GC：回收整个堆，包括年轻代、老年代以及元空间
