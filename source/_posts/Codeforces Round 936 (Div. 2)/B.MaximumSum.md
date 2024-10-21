---
title: Codeforces Round 936 (Div. 2) B. Maximum Sum
date: 2024-10-21 10:32
categories:
  - Codeforces
tags: 
  - Codeforces Round 936 (Div. 2)
---
[Codeforces Round 936 (Div. 2)](https://codeforces.com/contest/1946)

[B. Maximum Sum](https://codeforces.com/contest/1946/problem/B)

先求最大连续子列，后面最大连续子列多次加到原数组的和中，$max+max*2+max*3+...+sum$,类似这样

```cpp
#include <bits/stdc++.h>
#define int long long 
using namespace std;
const int INF=1e6+5;
const int Mod=1e9+7;
int n,a[INF],k;
void solve() 
{
	cin>>n>>k;
    int sum3=0;
	for (int i=1;i<=n;i++)
    {
        cin>>a[i];
        sum3+=a[i];
        sum3%=Mod;
    }
	int Max=0,sum=0;
	for (int i=1;i<=n;i++) {
		sum=max(0ll,sum);
		sum+=a[i];
		Max=max(Max,sum);
	}
	for (int i=1;i<=k;i++) {
		sum3+=Max;
		Max*=2;
        Max%=Mod;
		sum3%=Mod;
	}
	cout<<(sum3%Mod+Mod)%Mod<<"\n";
	// sum3 + Max + Max*2 + Max*3 
}
signed main()
{
	ios::sync_with_stdio(false);
	int t=0;
    cin>>t;
	while (t--)
    {
        solve();
    }
	return 0;
}
```

