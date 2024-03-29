## P3592

这题还是蛮有意思的。

我们设 $f_{l, r, x}$ 为区间 $[l, r]$ 内的最小值不小于 $x$ 时的答案。根据区间 DP 的套路，转移时我们考虑枚举断点 $k$。令 $p$ 表示询问中左右端点 $s, t$ 满足 $l \le s \le k \le r$，且询问值大于等于 $x$ 的询问个数，则我们可以得到方程：

$$
f_{l, r, x} = \max \left\{ f_{l, r, x + 1}, p \cdot x + \max_{l \le k \le r} \left\{ f_{l, k - 1, x} + f_{k + 1, r, x} \right\} \right\}
$$

再把 $c$ 离散化一下就可以做到 $\mathcal{O}(mn^3)$ 了。

本题还要求输出方案，我们考虑再开两个数组记录下每个区间的决策分割点与对应的 $x$ 的值即可。

## P4362

感觉有点套路的。我们设 $f_{u, s, 0 / 1}$ 表示当前节点所在联通块大小为 $s$，且是否为 $1$ 所在的联通块。那么这显然是一个类似树上背包的模型。

考虑如何转移。
