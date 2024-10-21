---
title: Codeforces Round 944 (Div. 4) A. My First Sorting Problem
date: 2024-10-21 10:32
categories:
  - Codeforces
tags: 
  - Codeforces Round 944 (Div. 4)
---
[Codeforces Round 944 (Div. 4)](https://codeforces.com/contest/1971)

[A. My First Sorting Problem](https://codeforces.com/contest/1971/problem/A)

```cpp
// By SnowDream
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e6+10;
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int t;
    cin >> t;
    int x,y;
    while(t--)
    {
        cin >> x >> y;
        if(x<y)
            cout << x << " " << y;
        else
            cout << y << " " << x;
        cout << "\n";
    }
    return 0;
}
```

