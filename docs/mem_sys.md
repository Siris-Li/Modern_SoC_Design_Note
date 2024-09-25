# 三、内存系统

## 3.1 内存层次结构

<figure>
  <img src="../figs/mem_hier.png" width=80%>
  <figcaption>Memory Hierarchy</figcaption>
</figure>

寄存器和缓存之间的数据粒度为 Word（32-bit），缓存之间的数据粒度为 Block（multi-word），缓存和主存之间的数据粒度为 Page（>1KB）。

## 3.2 缓存

### 分类

*直接映射 Direct-Mapped*

<figure>
  <img src="../figs/directly_mapped_cache.png" width=80%>
  <figcaption>Direct-Mapped Cache</figcaption>
</figure>

内存中的数据只会被映射到缓存中的一个位置。

*组相联映射 Set-Associative*

<figure>
  <img src="../figs/set_associate_cache.png" width=80%>
  <figcaption>Set-Assocoative Cache</figcaption>
</figure>

内存中的数据会被映射到缓存中的多个位置，每个位置称为一路（Way）。

<figure>
  <img src="../figs/range_associative_cache.png" width=80%>
  <figcaption>Range of Set Associative Caches</figcaption>
</figure>

对于固定大小的缓存，关联性每增加两倍，每组的块数（即路数）就会加倍，并将组数（set）减半——组索引的大小减少 1 位并增加标签的 1 位。

<figure>
  <img src="../figs/miss_rate_vs_associativity.png" width=50%>
  <figcaption>Benefits of Set Associative Caches</figcaption>
</figure>

最大的收益是从直接映射变为 2 路（miss rate 降低 20% 以上）。
增加关联性的收益递减，4 路和 8 路的性能差别不大。

需要注意的是，访问标签（tag）存储器和数据存储器的步骤可以顺序执行，也可以并行执行。
**顺序访问**的好处是，在确定对某路的缓存命中后，缓存仅访问发生命中的数据存储单元的一路，更加**节约功耗**。
另一种方法是**并行访问**标签存储器和数据存储器，这种方法**更快**，因为在确定命中或未命中时，所有路的缓存行都已被读取，剩下的就是从读取的缓存行中选择匹配的和对应的字节。
这种设计方法的功耗相对之下就更高。

### 替换策略

