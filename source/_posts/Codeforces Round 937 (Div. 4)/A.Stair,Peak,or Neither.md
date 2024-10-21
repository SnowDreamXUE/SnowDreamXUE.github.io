---
title: Codeforces Round 937 (Div. 4) A. Stair, Peak, or Neither?
date: 2024-10-21 10:32
categories:
  - Codeforces
tags: 
  - Codeforces Round 937 (Div. 4)
---
[Codeforces Round 937 (Div. 4)](https://codeforces.com/contest/1950)

[A. Stair, Peak, or Neither?](https://codeforces.com/contest/1950/problem/A)

签到题，判断大小即可

代码：

```cpp
// By SnowDream
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e5+10;
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int t;
    cin >> t;
    int a,b,c;
    while(t--)
    {
        cin >> a >> b >> c;
        if(a<b)
        {
            if(b<c)
                cout << "STATR" << "\n";
            else if(b>c)
                cout << "PEAK" << "\n";
            else
                cout << "NONE" << "\n";
        }
        else
            cout << "NONE" << "\n";
    }
    return 0;
}
```

