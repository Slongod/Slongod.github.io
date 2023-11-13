---
title: SP29458 AROPE2 - Alphabetic Rope2 题解
date: 2023-11-13 10:48:46
tags: 题解
---

不需要可持久化。

题目并没有强制在线，这启示我们可以直接将询问离线下来，每次处理到某个版本的时候回答这个版本的询问。

对于字符串的维护，我们可以使用 FHQ-Treap。

对于第一种操作，我们可以 ```split``` 出三部分，分别是 $[1,l-1],[l,r],[r+1,n]$，当然有些部分可能并不存在（如 $l=1$ 的时候 $[1,l-1]$ 就不存在）。然后再按照 $[l,r],[1,l-1],[r+1,n]$ 的顺序 ```merge``` 回去。具体见代码中的 ```change_to_front()``` 函数。

对于第二种操作，我们也是 ```split``` 出 $[1,l-1],[l,r],[r+1,n]$ 三部分，然后按照 $[1,l-1],[r+1,n],[l,r]$ 的顺序 ```merge``` 回去。具体见代码中的 ```change_to_end()``` 函数。

对于第三、四种操作，我们直接按照查询排名为 $k$ 的数的方法查询即可。具体见代码中的 ```find()``` 函数。

然后就做完了，比较好写，跑得也不慢。

AC Code :

```cpp
#include <bits/stdc++.h>
using namespace std;
namespace Slongod{
const int N = 1e4+7;

//fhq-treap
int root , cnt , son[N][2] , size[N]; char val[N]; mt19937 rd(time(nullptr));
void update(int ro){size[ro] = 1 + size[son[ro][1]] + size[son[ro][0]];}
int merge(int x , int y)
{
    if (!x or !y){return x^y;}
    if (rd() % 2) {
        son[y][1] = merge(x , son[y][1]);
        update(y); return y;
    } else {
        son[x][0] = merge(son[x][0] , y);
        update(x); return x;
    }
}
pair <int,int> split(int ro , int x) //将 size<=x 放到 first
{
    if (!ro){return {0 , 0};}
    if (size[son[ro][1]] >= x) {
        auto p = split(son[ro][1] , x);
        son[ro][1] = p.second; update(ro);
        return {p.first , ro};
    } else {
        auto p = split(son[ro][0] , x-size[son[ro][1]]-1);
        son[ro][0] = p.first; update(ro);
        return {ro , p.second};
    }
}
void change_to_front(int l , int r)
{
    auto p = split(root , l-1);
    auto sdp = split(p.second , r-l+1);
    root = merge(merge(sdp.first , p.first) , sdp.second);
}
void change_to_end(int l , int r)
{
    auto p = split(root , l-1);
    auto sdp = split(p.second , r-l+1);
    root = merge(merge(p.first , sdp.second) , sdp.first);
}
char find(int ro , int x)
{
    if (size[son[ro][1]] >= x){return find(son[ro][1] , x);}
    if (size[son[ro][1]] + 1 == x){return val[ro];}
    return find(son[ro][0] , x - size[son[ro][1]] - 1);
}
int build(int l , int r , const char s[])
{
    if (l > r){return 0;}
    int mid = (r - l) / 2 + l;
    int ro = ++cnt; val[ro] = s[mid];
    son[ro][1] = build(l , mid-1 , s);
    son[ro][0] = build(mid+1 , r , s);
    update(ro); return ro;
}

string s; int q , opt[N] , l[N] , r[N]; vector <pair<int,int>> que[N];
void main()
{
    cin >> s >> q;
    root = build(0 , s.length()-1 , s.c_str());
    
    for (int i = 1; i <= q; i++) {
        cin >> opt[i];
        if (opt[i] <= 2){cin >> l[i] >> r[i]; l[i]++; r[i]++;}
        if (opt[i] == 3){cin >> l[i]; que[i].push_back({l[i]+1 , i});}
        if (opt[i] == 4){cin >> l[i] >> r[i]; que[l[i]].push_back({r[i]+1 , i});}
    }

    for (auto p : que[0]){r[p.second] = find(root , p.first);}
    for (int i = 1; i <= q; i++) {
        if (opt[i] == 1){change_to_front(l[i] , r[i]);}
        if (opt[i] == 2){change_to_end(l[i] , r[i]);}    
        for (auto p : que[i]){r[p.second] = find(root , p.first);}
        if (opt[i] >= 3){cout << char(r[i]) << '\n';}
    }
}
}int main()
{
    ios :: sync_with_stdio(0);
    cin.tie(0) , cout.tie(0);
    return Slongod :: main(),0;
}
```

