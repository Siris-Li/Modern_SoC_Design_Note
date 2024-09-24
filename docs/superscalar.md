# 二、超标量概述

## 2.1 流水线建模

### 性能模型

<figure>
  <img src="../figs/pipe_perf_model.png" width=30%>
  <figcaption>Pipeline Performance Model</figcaption>
</figure>

假设非流水线时延为 T，则非流水线吞吐率为：

$$
\textit{Throughput}_{unpipelined} = \frac{1}{T}
$$

假设流水线级数为 k，寄存器时延为 S，则流水线吞吐率为：

$$
\textit{Throughput}_{pipelined} = \frac{1}{\dfrac{T}{k} + S}
$$

### 成本模型

<figure>
  <img src="../figs/pipe_cost_model.png" width=30%>
  <figcaption>Pipeline Cost Model</figcaption>
</figure>

假设非流水线的设计成本为 G：

$$
\textit{Cost}_{unpipelined} = G
$$

假设流水线级数为 k，每一级流水线的成本为 L，则流水线的设计成本为：

$$
\textit{Cost}_{pipeline} = G + k \cdot L
$$

### 性能/成本权衡

<figure>
  <img src="../figs/pipe_cost_perf_balance.png" width=30%>
  <figcaption>Pipeline Cost Model</figcaption>
</figure>

$$
\frac{\textit{Cost}_{pipelined}}{\textit{Performance}_{pipelined}} = \frac{G + k \cdot L}{\dfrac{T}{k} + S} = (G + k \cdot L) \cdot (\frac{T}{k} + S)
$$

$$
k_{opt} = \sqrt{\frac{G \cdot T}{L \cdot S}}
$$

## 2.2 超标量

<figure>
  <img src="../figs/ss_advantage.png" width=60%>
  <figcaption>Easing Squential Bottleneck with ILP</figcaption>
</figure>

$f$ 为可以被向量化操作加速的代码比例，$N$ 为处理器的个数，则根据阿姆达尔定律，整体的加速比为：

$$
S=\frac{1}{(1-f)+(f/6)}
$$

引入超标量之后，可以提高处理器的 ILP（Instruction Level Parallelism）。
假设无法被矢量加速部分的并行度可以被超标量提升到2，则整体的加速比为：

$$
S=\frac1{\dfrac{(1-f)}2+\dfrac f6}
$$

因此，超标量实现了较为底层的 ILP（Instruction-Level Parallelism），在更为普遍的情况下都能实现性能的提升。