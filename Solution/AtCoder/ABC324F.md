[在我的博客园中查看](https://www.cnblogs.com/forgot-dream/p/17765214.html)

> 给出一张 DAG，每条边有两种边权 $b$ 与 $c$，求一条从 $1$ 到 $n$ 的路径，问路径经过的边的 $\dfrac{\sum b}{\sum c}$ 的最大值是多少。  
> $n, m \le 2 \times 10^5$。

这不是经典 01 分数规划吗？将题目中的要求写成如下形式：

$$
\begin{aligned}
& \text{Maximize } \dfrac{\sum_{1 \le i \le m} x_i b_i}{\sum_{1 \le i \le m} x_i c_i} \\
& \text{s.t. } \text{ 所有 } i \in [1, m] \land x_i = 1 \text{ 的边构成一条 } 1 \text{ 到 } n \text{ 的路径}
\end{aligned}
$$

我们考虑二分一个答案 $p$，使得有如下关系成立：

$$
\forall s \in S, \dfrac{\sum_{i \in s} b_i}{\sum_{i \in s} c_i} \le p
$$

其中 $S$ 为 $1$ 到 $n$ 的所有路径所构成的集合。容易看出 $p$ 的最小值就是所求的答案。将上边的式子转化一下，我们就得到：

$$
\begin{aligned}
\dfrac{\sum_{i \in s} b_i}{\sum_{i \in s} c_i} &\le p \\
\sum_{i \in s} b_i &\le p \sum_{i \in s} c_i \\
\sum_{i \in s} b_i - p \sum_{i \in s} c_i &\le 0 \\
\sum_{i \in s} \left(b_i - p c_i \right) &\le 0 \\
\end{aligned}
$$

将边权重定义为 $b - pc$，那么我们就容易发现上述条件成立等价于 $1$ 到 $n$ 的最长路小于等于 $0$，当且仅当 $p$ 取到答案时取等号。于是我们只需要二分这个答案 $p$，将边权重新定义，然后跑 $1$ 到 $n$ 的最长路即可。

这里需要注意的是，原题给定的是一张 DAG，那么最长路是可以用拓扑排序去跑的（而且据说 SPFA 被卡了），于是就得到了一个 $\mathcal{O}((n + m) \log V)$ 的做法。

记得拓扑排序的时候把每个入度为 $0$ 的点塞进去。

[代码](https://atcoder.jp/contests/abc324/submissions/46586959)
