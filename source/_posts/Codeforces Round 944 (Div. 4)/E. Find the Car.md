---
title: Codeforces Round 944 (Div. 4) E. Find the Car
date: 2024-10-21 10:32
categories:
  - Codeforces
tags: 
  - Codeforces Round 944 (Div. 4)
---
[Codeforces Round 944 (Div. 4)](https://codeforces.com/contest/1971)

[E. Find the Car](https://codeforces.com/contest/1971/problem/E)

计算时必须先乘后除

```cpp
// By SnowDream
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e6+10;
int n,k,q,d;
ll a[N],b[N];
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int t;
    cin >> t;
    while(t--)
    {
        cin >> n >> k >> q;
        for(int i=1;i<=k;++i)
        {
            cin >> a[i];
        }
        for(int i=1;i<=k;++i)
        {
            cin >> b[i];
        }
        while(q--)
        {
            cin >> d;
            if(d==0)
            {
                cout << 0 << " ";
                continue;
            }
            ll i = lower_bound(a,a+k+1,d)-a;
            ll b_left=b[i-1],b_right=b[i];
            cout << b_left + (b_right-b_left) * (d-a[i-1]) / (a[i]-a[i-1])  << " ";
        }
        cout << "\n";
    }
    return 0;
}
```

