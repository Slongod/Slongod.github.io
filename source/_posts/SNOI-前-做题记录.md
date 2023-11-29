---
title: SNOI前 做题记录
date: 2023-11-27 09:08:17
tags: 做题记录
secret: 08020923@Chisato&&Takina
description: SNOI前 做题记录，包含一些 洛谷省选计划 的题。
---

# SNOI前 做题记录

## T402404 星辰

对于一个排列 $P$，它的逆排列 $P'$ 满足 $P'{p_i}=i$，显然，对合排列的逆排列为其本身。

对于原排列 $P$ 中的任意一组逆序对 $(i,j),(p_i,p_j)$，满足 $i<j,p_i>p_j$，其在 $P'$ 中唯一对应一组逆序对 $(p_j,p_i),(j,i)$。则我们可以对于所有的逆序对为 $k$ 的排列，同时删除当前存在的所有**非对合排列**，同时保证数量奇偶性不变。因此，**逆序对个数为 $k$ 的对合排列的奇偶性，就是逆序对个位数为 $k$ 的排列的奇偶性**。

考虑 DP，枚举填了 $t$ 个数，令 $f_{k}$ 表示考虑前 $t$ 个数（$1,2,\dots t$），现在有 $k$ 个逆序对的方案数，则由于 $t>t-1>\dots>1$，所以枚举 $t$ 的位置，可得：
$$
f_{k}=\sum_{i=k-t+1}^{k}f_{i}
$$
记 $m=\frac{n(n-1)}{2}$，直接 DP 是 $O(n^2m)$ 的。进行前缀和优化为 $O(nm)$。

考虑第 $t$ 轮转移，实际上是先进行前缀和 $f_i=f_{i-1}+f_{i}$，然后令 $f_i=f_{i}-f_{i-t}$。考虑 $f$ 的生成函数 $F$，则实际上是：

{% raw %}
$$
F_t=(\sum_{i=0}^{\infty}x_i)(1-x^{t})F_{t-1}=\frac{1}{1-x}(1-x^{t})F_{t-1}
$$
边界为 $F_0=1$，则：
$$
F_n=\prod_{t=1}^{n}\frac{1}{1-x}(1-x^{t})\\
=\frac{1}{(1-x)^n}\prod_{t=1}^{n}(1-x^{t})\\
=(\sum_{i=0}{{n+i-1}\choose{i}}x_i)\prod_{t=1}^{n}(1-x^t)
$$
考虑卢卡斯定理：

$$
{n\choose{m}}\pmod{p}={\lfloor\frac{n}{p}\rfloor\choose{\lfloor\frac{m}{p}\rfloor}}{n\operatorname{mod} p\choose{m \operatorname{mod} p}}\pmod{p}
$$
则对于 $p=2$，${{n}\choose{m}} \equiv 1 \pmod{p}$ 当且仅当 $n\&m=m$，即 $n$ 的二进制表示是 $m$ 的超集。

于是可以快速计算出  $\sum_{i=0}{{n+i-1}\choose{i}}x_i$，对于后面的乘积，由于答案对 $2$ 取模，可以用 ```bitset``` 优化。


{% endraw %}

时间复杂度为 $O(\frac{nm}{w})$。

--------------------------------------------

## CF1883G2 Dances (Hard Version)

答辩题面，看了好久才懂。虽然之前好像嘴过（

当 $a_1$ 不变的时候，显然可以二分出答案。

考虑 $a_1$ 的变化对答案的影响，当 $a_1$ 很小的时候，答案与 $a_1=1$ 的时候相同。当 $a_1=1$ 时候的答案不合法的时候，只要多删一个就能合法，因此，总的答案段数只有至多两段，且答案分别为 $ans(a_1=1),ans(a_1=1)+1$。

二分出这个分界点，每次 $check$ 的时候再套一层二分就行了，时间复杂度 $O(n\log n\log m)$。 

----------------------------------------------------

## CF1344D Résumé Review

记 $f(i,x)=x(a_i-x^2),g(i,x)=f(i,x+1)-f(i,x)$。

考虑暴力的做法，先令所有的 $b_i=0$，然后每次贪心地在满足 $b_i<a_i$ 的 $i$ 中选择 $g(i,b_i)$ 最大的 $i$，令 $b_i+1$，然后就做完了。但是时间复杂度为 $O(k \log n)$ $\huge \color{red} 过 \color{black} 不 \color{purple}去$，所以我们需要优化计算的过程。

$f(i,x)$ 这个东西显然是个凸的，所以 $g(i,x)$ 是单调不升的。考虑我们选择的最后一个 $i$，记 $t=g(i,b_i -1)$，一定有 $\forall j\in[1,n], g(j,b_j) \le t$。又因为 $g(i,x)$ 单调，所以 $t$ 越大，$\sum b_i$ 越小，反之亦然。

这样我们就可以二分 $t$ 了。对于每一个二分出来的 $mid$，我们对于每个 $i$ 找出最小的 $b_i$ 使得 $g(i,b_i)\le t$，这也可以用另一个二分得到。然后我们判断 $\sum b_i$ 和 $k$ 的关系，若 $\sum b_i > k$ 说明 $t >mid$，否则说明 $t\le mid$。

还有一个细节，由于我们选择是的最小的 $b_i$，所以最终的 $\sum b_i$ 可能小于 $k$，需要调整。这时候我们就可以再选择一些满足 $g(i,b_i)=t$ 的 $i$ 令 $b_i+1$。由于我们二分出的是满足 $\sum b_i <= k$ 的最小 $t$，这样调整一定能够使得 $\sum b_i = k$。

二分的边界开大点，$1e18$ 都不够，我开的 $1e30$ 才过。

-----------------------------------

## P8150 再会 | Sayounara

感觉不如原神。

如果我们能知道最小值和位置的话，我们就可以用 $n$ 次询问获得所有 $a_i$。记 $p_{min}$ 是最小值的位置，对于 $i>p_{min}$，我们可以知道 $\sum_{j=p_{min}+1}^{i} a_j$，然后差分一下就行，$i<p_{min}$ 同理。

现在我们只需要知道最小值的位置了。用心感受一下，发现在 $300$ 次以内找到可以得满分，于是我们大胆猜测需要三分。

考虑对于 $lm , rm(lm<rm)$，我们记 $gl = \text{query}(1,rm)-\text{query}(1,lm-1)，gr=\text{query}(lm,r)-\text{query}(rm+1,r)$，最小值为 $minval$，则：

- 若最小值在 $[lm,rm]$ 中，则 $gl=\sum_{i=lm}^{rm}a_i-minval-\min_{i=1}^{lm-1}a_i,gr=\sum_{i=lm}^{rm}a_i-minval-\min_{i=rm+1}^{n}a_i$，由于所有的 $a_i$ 不相等，因此 $gl\not =gr$。
- 若最小值在 $[1,lm)$ 中，则 $gl=\sum_{i=lm}^{rm} a_i,gr=\sum_{i=lm}^{rm}a_i$ 或 $gr=\sum_{i=lm}^{rm}a_i-\min_{i=lm}^{rm}a_i-\min_{i=rm+1}^{n}a_i$（取决于 $\min_{i=lm}^{n} a_i$ 的位置）。比较一下可以得到 $gl\le gr$。
- 若最小值在 $(rm,n]$ 中，同上可得 $gl \ge gr$。

因此我们每次可以将区间大小至少缩小 $\lfloor\frac{len}{3}\rfloor$，最后剩下 $1,2$ 个的时候直接 $\text{get}$ 即可。

------------------------------------

## [ARC016D] 軍艦ゲーム

设 $f_{u,k}$ 表示从 $u$ 这个点出发，当前 HP 是 $k$，到达 $n$ 的期望时间，记 $deg_i$ 是 $i$ 的个数，$u\rightarrow v$ 表示存在一条边 $(u,v)$。直接 DP 是简单的：


$$
\left \{
    \begin{array}{l}  
    & f_{u,v}=\infty \quad && (j\le 0)\\
    & f_{u,j}=\frac{1}{deg_u}\sum_{u\rightarrow v} f_{v,j-d_v}+1 \quad && (u=1,j>0)\\
    & f_{u,j}=\min\{\frac{1}{deg_u}\sum_{u\rightarrow v} f_{v,j-d_v}+1,f_{1,H}+H-j\} \quad && (u\not =1,u\not = n,j>0)\\
    & f_{u,j}=0 \quad && (u = n,j>0)\\
    \end{array}  
\right.
$$
我们要求的就是 $f_{1,H}$，但是这个式子直接算的话会出现环。

**难以发现**，我们先假定 $f_{1,H}=A$，记 $g(A)$ 为此时计算出的 $f_{1,H}$，我们就需要找到一个 $A=g(A)$ 的 $A$，这就是我们的答案。记 $h(A)=g(A)-A$，则 $h(A)$ 单调不升，证明可以看一下[题解 AT771 軍艦ゲーム - iMya_nlgau 的博客](https://www.luogu.com.cn/blog/Mly2019/solution-at771)。

于是我们直接二分 $A$ 就行了，时间复杂度是 $O(nH\log V)$。

