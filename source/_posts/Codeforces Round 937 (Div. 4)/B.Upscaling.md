---
title: Codeforces Round 937 (Div. 4) B. Upscaling
date: 2024-10-21 10:32
categories:
  - Codeforces
tags: 
  - Codeforces Round 937 (Div. 4)
---
[Codeforces Round 937 (Div. 4)](https://codeforces.com/contest/1950)

[B. Upscaling](https://codeforces.com/contest/1950/problem/B)

..,##交替输出两次即可

```cpp
// By SnowDream
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e5+10;
string s[2]={"##",".."};
int n;
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int t;
    cin >> t;
    while(t--)
    {
        cin >> n;
        for(int i=0;i<n;++i)
        {
            for(int j=0;j<n;j++)
            {
                cout << s[(j+i)%2];
            }
            cout << "\n";
            for(int j=0;j<n;j++)
            {
                cout << s[(i+j)%2];
            }
            cout << "\n";
        }
    }
    return 0;
}
```

