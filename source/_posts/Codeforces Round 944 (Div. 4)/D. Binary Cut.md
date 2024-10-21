---
title: Codeforces Round 944 (Div. 4) D. Binary Cut
date: 2024-10-21 10:32
categories:
  - Codeforces
tags: 
  - Codeforces Round 944 (Div. 4)
---
[Codeforces Round 944 (Div. 4)](https://codeforces.com/contest/1971)

[D. Binary Cut](https://codeforces.com/contest/1971/problem/D)

遇到**'10'**分割一次,**'01'**分割**n-1(n为'01'出现次数)**次

```cpp
// By SnowDream
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e6+10;
string s;
int ans;
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int t;
    cin >> t;
    while(t--)
    {
        cin >> s;
        ans=1;
        if(s.size()<=1)
        {
            cout << ans << "\n";
            continue;
        }
        for(int i=1;i<s.size();++i)
        {
            if(s[i-1]=='1'&&s[i]=='0')
            {
                ans++;
            }
        }
        cout << ans << "\n";
    }
    return 0;
}
```

