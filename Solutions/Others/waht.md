## $\text{50pts}$

考虑 DP。设 $f_{i, 0/1}$ 表示位置 $i$ 上安排一个男 / 女生时所有方案获得的价值之和，$g_{i, 0/1}$ 表示位置 $i$ 上安排一个男 / 女生的总方案数。这是一个很朴素的线性 DP，容易看出，初始条件为

$$
f_{0, 0} = d \\
f_{0, 1} = e \\
g_{0, 0} = 1 \\
g_{0, 1} = 1 \\
$$

，表示在队首安排一个男生或女生的价值和与方案数。如果我们认为对于所有的 $i < 0$，都有 $f_{i, 0/1} = g_{i, 0/1} = 0$ 的话，就容易得到方程为

$$
\begin{aligned}
f_{i, 0} &= (f_{i - a, 0} + d \times g_{i - a, 0}) + (f_{i - b, 1} + d \times g_{i - b, 1}) \\
f_{i, 1} &= (f_{i - c, 1} + e \times g_{i - c, 1}) + (f_{i - b, 0} + e \times g_{i - b, 0}) \\
g_{i, 0} &= g_{i - a, 0} + g_{i - b, 1} \\
g_{i, 1} &= g_{i - c, 1} + g_{i - b, 0} \\
\end{aligned}
$$

，这个稍微做一些分类讨论就可以得到。那么答案即为 $f_{n, 0} + f_{n, 1}$，就得到一个时空复杂度均为 $\mathcal{O}(n)$ 的做法。

## $\text{100pts}$

考虑优化上边的这个 DP。我们发现，上边的方程虽说有 4 个，但只跟下标为 $i - a, i - b, i - c$ 的值有关，这启发我们将其写成矩阵的形式。设 $F_{i} = \begin{bmatrix} f_{i, 0} \\ f_{i, 1} \\ g_{i, 0} \\ g_{i, 1} \end{bmatrix}$，则我们希望将方程写成如下的形式：

$$
F_{i} = A \cdot F_{i - a} + B \cdot F_{i - b} + C \cdot F_{i - c} \tag{1}
$$

其中 $A, B, C$ 是我们需要确定的系数矩阵。

现在来考虑如何确定这三个矩阵。首先考虑 $A$，我们考虑将上边四个方程中有关 $i - a$ 的项提出来：

$$
\begin{aligned}
f_{i, 0} &= (f_{i - a, 0} + d \times g_{i - a, 0}) + \dots \\
\dots \\
g_{i, 0} &= g_{i - a, 0} + \dots \\
\dots \\
\end{aligned}
$$

通过手模可以得到，若写成矩阵的形式则上式等同于：

$$
F_{i} = \begin{bmatrix}
1 & 0 & d & 0 \\
0 & 0 & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 0 \\
\end{bmatrix} \cdot F_{i - a}
$$

即我们有：

$$
A = \begin{bmatrix}
1 & 0 & d & 0 \\
0 & 0 & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 0 \\
\end{bmatrix}
$$

通过同样的过程，不难得到：

$$
B = \begin{bmatrix}
0 & 1 & 0 & d \\
1 & 0 & e & 0 \\
0 & 0 & 0 & 1 \\
0 & 0 & 1 & 0 \\
\end{bmatrix} \\
C = \begin{bmatrix}
0 & 0 & 0 & 0 \\
0 & 1 & 0 & e \\
0 & 0 & 0 & 0 \\
0 & 0 & 0 & 1 \\
\end{bmatrix}
$$

这样就得到了 $(1)$ 式。

但是还不够。如果只有 $(1)$ 式的话，我们还是只能 $\mathcal{O}(n)$ 递推（甚至还带一个矩阵乘法的巨大常数）。不过，观察到上式是一个线性递推的式子，而 $a, b, c \le 30$ 的条件还没用到，这就启发我们联想到斐波纳契数列的矩阵递推做法，设

$$
G_{i} = \begin{bmatrix}
F_{i} \\
\vdots \\
F_{i - 30} \\
\end{bmatrix}
$$

，则 $G_{n}$ 就是我们所求的答案所在的矩阵。我们希望找到 $G_{i - 1}$ 与 $G_{i}$ 之间的递推关系，这样就可以通过矩阵快速幂来优化递推的过程。

设 $G_{i} = T \cdot G_{i - 1}$，则我们有 $G_{n} = T^{n - 30} \cdot G_{30}$。观察到，除了 $F_{i}$ 本身之外，其他的项（即 $F_{i - 1} \sim F_{i - 30}$）都在 $G_{i - 1}$ 中出现过了，那么这一部分就非常的容易转移，只需要令 $j \in [1, 30]$ 的 $T_{j, j - 1} = I$ 即可，其中 $I$ 是单位矩阵。这一步其实就是相当于把 $G_{i - 1}$ 中的第 $j - 1$ 项移到了 $G_{i}$ 中的第 $j$ 项，画个图就很好理解。

然后再来处理 $G_{i}$ 的第一项，也即 $F_{i}$ 的转移。根据 $(1)$ 式，我们得到 $F_{i}$ 的转移仅需要 $F_{i - a}, F_{i - b}, F_{i - c}$ 这三项，而由于 $a, b, c \le 30$，因此这三项一定在 $G_{i - 1}$ 中出现过。那么我们令 $T_{0, a - 1} = A, T_{0, b - 1} = B, T_{0, c - 1} = C$ 就可以完成转移（$-1$ 是因为要处理 $i$ 与 $i - 1$ 之间的偏移量）。

到这里，基本上就已就做完了，剩下的就是一些细节问题。

首先是对于 $i \le 30$ 的项的处理。这个是容易的，因为只有 $30$ 项，我们就可以考虑直接通过 $(1)$ 式把 $F_{0} \sim F_{30}$ 算出来。再判断根据 $n$ 的大小判断一下就可以了。

然后是一个隐藏的很深的点，也是我挂分的原因。注意到题目中并没有保证 $a, b, c$ 两两不同，如果存在两个数相同的情况，直接给 $T_{0}$ 赋值的话会导致某些矩阵被覆盖，从而导致转移不完全。当然，这个问题也是很好解决的，我们考虑把赋值换成加法即可。

```cpp
struct Mat {
  i64 a[4][4];
  Mat() { memset(a, 0, sizeof(a)); }
  i64 *operator[](int idx) { return a[idx]; }
  const i64 *operator[](int idx) const { return a[idx]; }
  friend Mat operator+(const Mat &lhs, const Mat &rhs) {
    Mat res;
    for (int i = 0; i < 4; i++) {
      for (int j = 0; j < 4; j++) res[i][j] = (lhs[i][j] + rhs[i][j]) % mod;
    }
    return res;
  }
  friend Mat operator*(const Mat &lhs, const Mat &rhs) {
    Mat res;
    for (int i = 0; i < 4; i++) {
      for (int j = 0; j < 4; j++) {
        for (int k = 0; k < 4; k++) {
          res[i][j] = (res[i][j] + lhs[i][k] * rhs[k][j] % mod) % mod;
        }
      }
    }
    return res;
  }
  void print() {
    for (int i = 0; i < 4; i++) {
      for (int j = 0; j < 4; j++) std::cout << a[i][j] << " \n"[j == 3];
    }
    std::cout << "\n";
  }
} F[35];
struct MAT {
  Mat a[35][35];
  Mat *operator[](int idx) { return a[idx]; }
  const Mat *operator[](int idx) const { return a[idx]; }
  friend MAT operator*(const MAT &lhs, const MAT &rhs) {
    MAT res;
    for (int i = 0; i <= 30; i++) {
      for (int j = 0; j <= 30; j++) {
        for (int k = 0; k <= 30; k++) {
          res[i][j] = res[i][j] + lhs[i][k] * rhs[k][j];
        }
      }
    }
    return res;
  }
};
MAT FastPow(MAT base, i64 exp) {
  MAT res = base;
  exp--;
  for (; exp; exp >>= 1) {
    if (exp & 1) res = res * base;
    base = base * base;
  }
  return res;
}
void solve() {
  Mat A, B, C, I;
  A[0][0] = 1, A[0][2] = d, A[2][2] = 1;
  C[1][1] = 1, C[1][3] = e, C[3][3] = 1;
  B[0][1] = 1, B[0][3] = d, B[1][0] = 1, B[1][2] = e, B[2][3] = 1, B[3][2] = 1;
  I[0][0] = I[1][1] = I[2][2] = I[3][3] = 1;
  F[0][0][0] = d, F[0][1][0] = e, F[0][2][0] = 1, F[0][3][0] = 1;
  for (int i = 1; i <= 30; i++) {
    if (i >= a) F[i] = F[i] + A * F[i - a];
    if (i >= b) F[i] = F[i] + B * F[i - b];
    if (i >= c) F[i] = F[i] + C * F[i - c];
  }
  if (n <= 30) {
    std::cout << (F[n][0][0] + F[n][1][0]) % mod << "\n";
    return;
  }
  MAT ans, coef;
  for (int i = 0; i <= 30; i++) ans[i][0] = F[30 - i];
  coef[0][a - 1] = coef[0][a - 1] + A;
  coef[0][b - 1] = coef[0][b - 1] + B;
  coef[0][c - 1] = coef[0][c - 1] + C;
  for (int i = 1; i <= 30; i++) coef[i][i - 1] = I;
  // auto tmp = coef;
  // for (int i = 1; i <= n - 30; i++) coef = coef * tmp;
  coef = FastPow(coef, n - 30);
  ans = coef * ans;
  std::cout << (ans[0][0][0][0] + ans[0][0][1][0]) % mod << "\n";
}
```
