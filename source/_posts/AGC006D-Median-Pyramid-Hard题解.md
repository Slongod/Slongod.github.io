---
title: AGC006D Median Pyramid Hard题解
date: 2023-09-07 09:49:50
tags: 题解
description: AGC006D Median Pyramid Hard题解
---

### 引入

先考虑一下这个问题的简化版，如果最下面的数不是排列，而是 $01$ 串，应该怎么做。

比如：

<img src="https://pic.imgdb.cn/item/64f92e01661c6c8e542f807b.jpg" style="zoom: 10%;" />

我们发现，最顶上的那个数字是底部 **离中心（位置 $n$）最近的两个相同的数字**。

证明如下（方便起见，**记这两个数字的位置分别是** $i,j(i<j)$ **且** $i,j\ge n,a[i]=a[j]=0$）：

- 若 $i=n$ 或 $j=n$，则 $i,j$ 正上方的两列都将一直是 $0$，直到顶端。

- 若 $i\not=n$ 且 $j\not=n$，$则 i>n$，小于同理。那么由 ”$i,j$ 上的数字是离位置 $n$ 最近的两个相同的数字“ 可以知道：

  1. 子串 $[n-(i-n),i-1]$ 是 $0/1$ 交错出现组成的。
  2.  $i-1$ 上的数是 $1$。

  那么对于从下往上数第二行：

  1. $i-1$ 和 $i$ 上的数字都将是 $0$
  2. 若$i-1=n$，则可以一直通到顶端，否则由于最下面一行子串 $[n-(i-n),i-1]$ 是 $0/1$ 交错出现组成的，第二行的 $[n-(i-1-n),i-2]$ 一定是 $0/1$ 交错出现组成的。

于是问题又回到了上述的两种情况之一，且 $i,j$ 均减小 $1$，于是重复若干次后一定会使得 $i=n$。$i,j\le n$ 与上述情况类似。

于是我们证明了：**最顶上的那个数字是底部离中心（位置 $n$）最近的两个相同的数字**。

若不存在两个相同且相邻的数，则对于从下往上数第 $i$ 行，最中间的数字 $x$ 为第 $i-1$ 行的数 $y\operatorname{xor} 1$，简单计算一下即可。

### Sol

对于这个问题，考虑二分答案。对于二分到的 $x$，我们需要判断最后顶端的数 $k\ge x$ 或 $k<x$。

我们遍历整个 $a$ 数组，将大于等于 $x$ 的数的值变成 $1$，小于 $x$ 的数的值变成 $0$，于是问题转化成了对于上述的弱化版问题，判断顶端的数是否是 $1$。扫一遍即可。

总时间复杂度 $O(n\log n)$。

AC Code：

```cpp
#include <bits/stdc++.h>
using namespace std;
namespace Slongod{
const int N = 3e5+7;
int n , a[N] , p[N];
bool check(int x)
{
    for (int i = 1; i <= 2*n-1; i++) {
        p[i] = (a[i] >= x ? 1 : 0);
    }
    for (int i = 0; i < n-1; i++) {
        if (p[n+i] == p[n+i+1]) {
            return p[n+i];
        } else if (p[n-i] == p[n-i-1]) {
            return p[n-i];
        }
    }
    return ((n&1) ? p[n] : !p[n]);
}
int main()
{
    cin >> n; for (int i = 1; i <= 2*n-1; i++){cin >> a[i];}
    int l = 1 , r = 2*n-1 , ans = 0;
    while(l <= r) {
        int mid = (r - l) / 2 + l;
        if (!check(mid)) {
            r = mid - 1;
        } else {
            l = mid + 1;
            ans = mid;
        }
    } cout << ans << '\n';
    return 0;
}
}int main()
{
    ios :: sync_with_stdio(0);
    cin.tie(0) , cout.tie(0);
    return Slongod :: main();
}
```

