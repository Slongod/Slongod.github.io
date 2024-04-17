---
title: CF1842G Tenzing and Random Operations 题解
date: 2024-04-17 00:00:00
tags: 题解
description: CF1842G Tenzing and Random Operations 题解
---

## 题意

给你一个长为 $n$ 的序列 $a$，操作 $m$ 次，每次在 $[1,n]$ 中随机一个 $i$，令 $\forall j>i,a_j \leftarrow a_j+v$，问 $m$ 次操作之后 $\displaystyle \prod_{i=1}^{n} a_i$ 的期望。

## 题解

喵喵题。

显然期望是假的，算一下总的方案的和再除以一个 $n^{m}$ 就可以了。于是有一个 $O(nm^2)$ 的做法，我们设 $f_{i,j}$ 表示在 $m$ 次操作中一共随到了 $j$ 次前 $i$ 个数时，前 $\displaystyle \prod_{k=1}^{i}a_k$ 的值，转移显然，但是不能通过，也没有什么优化空间（当然可能是我太菜了想不出来怎么优化）。

记 $b$ 代表最开始的 $a$，我们考虑 $\displaystyle \prod_{i=1}^{n} (b_i + v + \dots+v)$ 的意义是什么。我们有 $n+1$ 个点，总共 $m$ 次操作，每次会选择一个 $i$，在第 $i$ 个点里放一个神奇遗器，到达这个点之后就可以永久解锁这个遗器。操作后要从 $1$ 号点走到 $n+1$ 号点，每次可以选择走原始的 $a_i$ 条路，也可以选一个已经解锁的遗器（一个遗器可以有 $v$ 个走到下一个点的方法），问最后有多少走到 $n+1$ 的方案。我们设 $f_{i,j}$ 表示走到了 $i$，并且已经解锁了 $j$ 个遗器的方案数，显然 $j\le \min\{n,m\}$，则转移为：

- 选本来就有的路，贡献为 $f_{i+1,j}\leftarrow f_{i,j}\times a_{i+1}$。
- 选一个已经解锁的遗器，贡献为 $f_{i+1,j}\leftarrow f_{i,j}\times j\times v$。
- 新解锁一个遗器，贡献为 $f_{i+1,j+1}\leftarrow f_{i,j}\times (m-j)\times i\times v$，即先选出来一个遗器，再考虑它的位置，然后再走。

则答案为：
$$
\sum_{i=0}^{n}\frac{f_{n,i}\times n^{m-j}}{n^m}
$$
总的复杂度为 $O(n^2)$，可以通过。

```cpp
void main()
{
    cin >> n >> m >> v; f[0][0] = 1;
    for (int i = 1; i <= n; i++){cin >> a[i];}
    for (int i = 0; i <= n; i++) {
        for (int j = 0; j <= n; j++) {
            f[i+1][j] = (f[i+1][j] + 1ll * f[i][j] * a[i+1]) % mod;
            f[i+1][j] = (f[i+1][j] + 1ll * f[i][j] * j % mod * v) % mod;
            f[i+1][j+1] = (f[i+1][j+1] + 1ll * f[i][j] * (i + 1) % mod * (m - j) % mod * v) % mod;
        }
    }
    for (int i = 0 , t = 1 , invn = qkpow(n , mod - 2); i <= n; i++ , t = 1ll * t * invn % mod) {
        ans = (ans + 1ll * f[n][i] * t) % mod;
    } cout << ans << '\n';
}
```

