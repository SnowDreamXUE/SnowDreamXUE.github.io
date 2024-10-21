---
title: Codeforces Round 934 (Div. 2)A. Destroying Bridges
date: 2024-10-21 10:32
categories:
  - Codeforces
tags:
  - Codeforces Round 934 (Div. 2)
---

[Codeforces Round 934 (Div. 2)](https://codeforces.com/contest/1944)

[A. Destroying Bridges](https://codeforces.com/contest/1944/problem/A)

```cpp
#include<bits/stdc++.h>
using namespace std;
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int t;
    cin >> t;
    int n,k;
    while(t--)
    {
        cin >> n >> k;
        if(k>=n-1)
            cout << "1" << endl;
        else
            cout << n << endl;
    }
}
```
