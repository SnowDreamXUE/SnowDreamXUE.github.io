---
title: Codeforces Round 944 (Div. 4) C. Clock and Strings
date: 2024-10-21 10:32
categories:
  - Codeforces
tags: 
  - Codeforces Round 944 (Div. 4)
---
[Codeforces Round 944 (Div. 4)](https://codeforces.com/contest/1971)

[C. Clock and Strings](https://codeforces.com/contest/1971/problem/C)

```cpp
// By SnowDream
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e6+10;
bool ans[13];
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int t;
    cin >> t;
    int a,b,c,d,tmp;
    while(t--)
    {
        cin >> a >> b >> c >> d;
        memset(ans, false,sizeof ans);
        if(a==c||a==d||b==c||b==d)
        {
            cout << "YES\n";
            continue;
        }
        if(a>b)
        {
            tmp=a;
            a=b;
            b=tmp;
        }
        for(int i=a+1;i<b;++i)
        {
            ans[i]=true;
        }
        if((ans[c]&&!ans[d])||(!ans[c]&&ans[d]))
        {
            cout << "YES\n";
        }
        else
        {
            cout << "NO\n";
        }
    }
    return 0;
}
```

