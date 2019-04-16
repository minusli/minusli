# Golang有趣的内存泄露问题.md
#####参考：
- [记一个golang“内存泄漏”的问题](https://zhuanlan.zhihu.com/p/32686933)
- [golang逃逸分析](https://bytekm.bytedance.net/kmf/articleDetail/3212)

总结下来：
1. golang不是单纯通过new()来决定内存分配在栈上还是堆上的
2. golang还会通过逃逸分析，将栈上的内存调整至堆上