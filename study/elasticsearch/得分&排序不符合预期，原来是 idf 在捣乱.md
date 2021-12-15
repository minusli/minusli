# 得分&排序不符合预期，原来是 idf 在捣乱
### 问题：
一个 cluster 中，有三个文档内容分别是:
- first doc
- first doc
- doc

在搜索 doc 时，发现排序结果是:
1. first doc
2. doc
3. first doc

查看了具体得分，确实是 1>2>3

### 原因：
得分逻辑是跟聚 tf-idf 计算而来；其中 idf 是全局统计值；而这个全局指的是当个 shard 之中；
所以「first doc」在 shard1 中，"doc" 的 idf 很高；导致在这个 shard 上得到「first doc」文档得分很高；而在 shard2 上，同时有「doc」和「first doc」，二者在 "doc" 的 idf 上是相同的，但觉得值又远小于 shar1 上 "doc" 的 idf，但 tf 上「doc」明显高于「first doc」，由此产生了这种排序结果；
> 参考：[相关性计算](https://www.elastic.co/guide/cn/elasticsearch/guide/current/scoring-theory.html#scoring-theory)

### 解决办法：
根据业务情况判断 idf 的必要性，可以调整打分逻辑，取消 idf 得分；
> 参考：[可插拔相似算法](https://www.elastic.co/guide/cn/elasticsearch/guide/current/pluggable-similarites.html) ， [更改相似度](https://www.elastic.co/guide/cn/elasticsearch/guide/current/changing-similarities.html) ，[调试相关度是最后 10% 要做的事情](https://www.elastic.co/guide/cn/elasticsearch/guide/current/relevance-conclusion.html#relevance-conclusion)

