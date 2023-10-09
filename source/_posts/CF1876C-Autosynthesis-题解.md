---
title: CF1876C Autosynthesis 题解
date: 2023-10-09 08:49:02
tags: 题解
description: CF1876C Autosynthesis 题解
---

## 题意

给你一个长度为 $n$ 的序列 $\{a_1,a_2\dots a_n\}$，满足 $\forall 1\le i\le n,1\le a_i\le n$。你要将这个序列划分成两个不交的子序列，满足其中一个子序列的下标构成的集合**等于**另一个子序列的值构成的集合。输出作为值的那一个子序列。

## 题解

将每个位置看做一个点，$i$ 向 $a_i$ 连一条有向边，由于每个点只有一个出度，所以整张图是一棵基环树，且根的位置是一个环。

我们将作为值的点称作 $1$ 类点，将作为下标的点称作 $0$ 类点。我们可以得到以下结论：

- 一个点是 $0$ 类点，当且仅当它有一个直接儿子是 $1$ 类点。
- 一个点没有直接儿子是 $1$ 类点，则它是 $1$ 类点。
- 一个点是 $1$ 类点，则它的父亲一定是 $0$ 类点。

则叶子处的点必定是 $1$ 类点。于是，我们可以对这张图进行拓扑排序，从叶子开始，考虑每个点对它父亲的影响，并将所有儿子都考虑完的点放到队列中。最后，我们只可能剩下若干个环没有考虑。这时候剩下的环分为一下几种情况：

- 若这个环中有已经确定了的 $0$ 类点，则这个环中的点一定可以合法分类。具体地：
  - 如果一个点 $i$ 是 $0$ 类点，则当 $a_i$ 是 $0$ 类点的时候，说明 $a_i$ 有一个在环外面的直接儿子是 $1$ 类点，合法；当 $a_i$ 没有被确定的时候，将$a_i$ 变为 $1$ 类点。
  - 如果一个点 $i$ 是 $1$ 类点，则 $a_i$ 是 $0$ 类点的时候合法；当 $a_i$ 未确定的时候将 $a_i$ 变为 $0$ 类点。

- 若这个环中没有已经确定了的 $0$ 类点，则当环的大小是偶数的时候交叉分类，奇数无解。

然后输出就行了。

AC Code:

```cpp
#include <bits/stdc++.h>
using namespace std;
namespace Slongod{
const int N = 2e5+7;
int n , a[N] , f[N] , rd[N];
int main()
{
    cin >> n; for (int i = 1; i <= n; i++){cin >> a[i]; rd[a[i]]++;}
    for (int i = 1; i <= n; i++){f[i] = 1;}
    queue <int> q; for (int i = 1; i <= n; i++){if (!rd[i]){q.push(i);}}
    while(!q.empty()) {
        int u = q.front(); q.pop();
        if (f[u]){f[a[u]] = 0;}
        rd[a[u]]--;
        if (!rd[a[u]]) {
            q.push(a[u]);
        }
    }
    for (int i = 1; i <= n; i++) {
        if (rd[i] and !f[i]) {
            int x = !f[i] , t = i;
            rd[i]--;
            do {
                t = a[t];
                if (x != f[t]) {
                    if (x == 1){x = f[t];}
                    else {f[t] = x;}
                } rd[t]--; x ^= 1;
            } while(t != i);
        }
    }
    for (int i = 1; i <= n; i++) {
        if (rd[i] == 1) {
            rd[i]--; int x = 0;
            for (int t = a[i]; t != i; t = a[t] , x ^= 1) {
                rd[t]--; f[t] = x;
            } if (f[i] != x) {
                cout << "-1\n";
                return 0;
            }
        }
    }
    int cnt = 0;
    for (int i = 1; i <= n; i++){cnt += (f[i] == 1);}
    cout << cnt << '\n';
    for (int i = 1; i <= n; i++) {
        if (f[i] == 1) {
            cout << a[i] << ' ';
        }
    } cout << '\n';
    return 0;
}
}int main()
{
    ios :: sync_with_stdio(0);
    cin.tie(0) , cout.tie(0);
    return Slongod :: main();
}
```

