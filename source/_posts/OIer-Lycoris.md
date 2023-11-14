---
title: OIer Lycoris
date: 2023-11-14 17:50:27
tags: 其他
description: 锦木千束和井上泷奈是 西工大附中 的 OI 选手
---

锦木千束和井上泷奈是 西工大附中 的 OI 选手，这是她们今天写的代码：

```cpp
/*

没有补题😒

*/

#include <bits/stdc++.h>
using namespace std;
namespace 🤣{
using ll = long long;
const int N = 2e4+7 , M = 505 , mod = 998244353 , mt = 500 , inf = 0x3f3f3f3f;
int n , q , w[N] , lp[N] , rp[N] , mp[N] , as[N];
int v[N] , ans[N*10] , f[N][M] , id[N][M];
vector <int> ps[N];
int pmod(int x){return x >= mod ? x - mod : x;}
void solve(int lt , int rt)
{
	if (lt >= rt) {
		for (auto p : ps[lt]) {
			if (lp[p] == rp[p]) {
				if (mp[p] >= w[lt]) {
					ans[p] = v[lt];
					as[p] = 1;
				} else {
					ans[p] = 0;
					as[p] = 0;
				}
			}
		}
		return;
	}
	int mid = (rt - lt) / 2 + lt;
	solve(lt , mid); solve(mid+1 , rt);
	
	//left
	for (int i = 0; i <= mt; i++){f[mid][i] = -inf; id[mid][i] = 0;}
	f[mid][0] = 0; id[mid][0] = 1; f[mid][w[mid]] = v[mid]; id[mid][w[mid]] = 1;
	for (int i = mid-1; i >= lt; i--) {
		for (int j = 0; j <= mt; j++) {
			f[i][j] = f[i+1][j];
			id[i][j] = id[i+1][j];
			if (j >= w[i]) {
				if (f[i][j] < f[i+1][j-w[i]] + v[i]) {
					f[i][j] = f[i+1][j-w[i]] + v[i];
					id[i][j] = id[i+1][j-w[i]];
				} else if (f[i][j] == f[i+1][j-w[i]] + v[i]) {
					id[i][j] = pmod(id[i][j] + id[i+1][j-w[i]]);
				}
			}
		}
	}
	//right
	for (int i = 0; i <= mt; i++){f[mid+1][i] = 0; id[mid+1][i] = 1;}
    for (int i = w[mid+1]; i <= mt; i++){f[mid+1][i] = v[mid+1]; id[mid+1][i] = 1;}
	for (int i = mid+2; i <= rt; i++) {
		for (int j = 0; j <= mt; j++) {
			f[i][j] = f[i-1][j];
			id[i][j] = id[i-1][j];
			if (j >= w[i]) {
				if (f[i][j] < f[i-1][j-w[i]] + v[i]) {
					f[i][j] = f[i-1][j-w[i]] + v[i];
					id[i][j] = id[i-1][j-w[i]];
				} else if (f[i][j] == f[i-1][j-w[i]] + v[i]) {
					id[i][j] = pmod(id[i][j] + id[i-1][j-w[i]]);
				}
			}
		}
	}
    //merge
	for (int i = mid+1; i <= rt; i++) {
		for (auto p : ps[i]) {
			int l = lp[p] , r = i , m = mp[p];
			if (l <= mid and l >= lt) {
				for (int j = 0; j <= m; j++) {
					if (f[l][j] + f[r][m-j] > ans[p]) {
						ans[p] = f[l][j] + f[r][m-j];
						as[p] = 1ll * id[l][j] * id[r][m-j] % mod;
					} else if (f[l][j] + f[r][m-j] == ans[p] and ans[p]) {
						as[p] = pmod(1ll * id[l][j] * id[r][m-j] % mod + as[p]);
					}
				}
			}
		}
	} 
}
void 👉()
{
	cin >> n; for (int i = 1; i <= n; i++){cin >> v[i] >> w[i];}
	cin >> q; for (int i = 1; i <= q; i++){cin >> lp[i] >> rp[i] >> mp[i]; ps[rp[i]].push_back(i);}
	solve(1 , n); for (int i = 1; i <= q; i++){cout << ans[i] << ' ' << (ans[i] ? as[i] : 0) << '\n';}
}
}int main()
{
	freopen("knapsack.in" , "r" , stdin);
	freopen("knapsack.out" , "w" , stdout);
	ios :: sync_with_stdio(0);
	cin.tie(0) , cout.tie(0);
	return 🤣::👉(),0;
}
```

