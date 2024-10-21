---
title: Codeforces Round 946 (Div. 3) A.Phone Desktop
date: 2024-10-21 10:32
categories:
  - Codeforces
tags: 
  - Codeforces Round 946 (Div. 3)
---
[Codeforces Round 946 (Div. 3)](https://codeforces.com/contest/1974)

[A.Phone Desktop](https://codeforces.com/contest/1974/problem/A)

```cpp
// By SnowDream
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e6+10;
int x,y;
int ans;
int room;
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int t;
    cin >> t;
    while(t--)
    {
        cin >> x >> y;
        ans = (y+1)/2;
        if(y%2==0)
        {
            room=7*ans;
        }
        else
        {
            room=7*ans+4;
        }
        while(room<x)
        {
            room+=15;
            ans++;
        }
        cout << ans << "\n";
    }
    return 0;
}
```

