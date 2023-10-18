## 35 pts

我们考虑 DP，设 $f_{i, j}$ 表示第一个人最后接的球为 $i$，第二个人最后接的球为 $j$ 时的最小速度上限。容易看出下一个球是 $\max\{i, j\} + 1$，不妨设 $i > j$，那么下一个球就是 $i + 1$ 号球。

那么此时显然有两种决策，即第一个人去接这个球或者第二个人去接这个球。如果是第一个人去接，那么就会转移到 $f_{i + 1, j}$，因为此时第二个人最后接的球仍然为 $j$，如果是第二个人去接，那么同理应该是转移到 $f_{i, i + 1}$ 的，不过为了保证 $f_{i, j}$ 中的 $i$ 始终不小于 $j$，而且两个人没有任何区别，我们可以选择转移到 $f_{i + 1, i}$，这样在实际写的时候会更方便。

那么方程就是：

$$
f_{i + 1, j} \gets \min\left\{f_{i + 1, j}, \max\{f_{i, j}, \dfrac{|x_{i + 1} - x_{i}|}{t_{i + 1} - t_{i}}\}\right\}  \\
f_{i + 1, i} \gets \min\left\{f_{i + 1, i}, \max\{f_{i, j}, \dfrac{|x_{i + 1} - x_{j}|}{t_{i + 1} - t_{j}}\}\right\}  \\
$$

状态数 $\mathcal{O}(n ^ 2)$，转移复杂度 $\mathcal{O}(1)$，很容易做到 $\mathcal{O}(n ^ 2)$ 的时空复杂度。注意数组不能开得太大，否则很容易 MLE。

```cpp
for (int i = 0; i <= n; i++) {
  for (int j = 0; j <= n; j++) f[i][j] = 1e18;
}
f[0][0] = 0;
for (int i = 0; i < n; i++) {
  for (int j = 0; j <= i; j++) {
    f[i + 1][j] =
        std::min(f[i + 1][j],
                 std::max(f[i][j], abs(x[i + 1] - x[i]) / (t[i + 1] - t[i])));
    f[i + 1][i] =
        std::min(f[i + 1][i],
                 std::max(f[i][j], abs(x[i + 1] - x[j]) / (t[i + 1] - t[j])));
  }
}
f80 ans = 1e18;
for (int i = 0; i <= n; i++) ans = std::min(ans, f[n][i]);
std::cout << std::fixed << std::setprecision(9) << ans << "\n";
```

（不要在意为啥要手动赋极大值）
