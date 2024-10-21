---
title: Codeforces Round 944 (Div. 4) B. Different String
date: 2024-10-21 10:32
categories:
  - Codeforces
tags: 
  - Codeforces Round 944 (Div. 4)
---
[Codeforces Round 944 (Div. 4)](https://codeforces.com/contest/1971)

[B. Different String](https://codeforces.com/contest/1971/problem/B)

```cpp
// By SnowDream
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e6+10;
string s;
char tmp;
bool flag;
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int t;
    cin >> t;
    while(t--)
    {
        cin >> s;
        if(s.size()<=1)
        {
            cout << "NO\n";
            continue;
        }
        flag=false;
        for(int i=1;i<s.size();++i)
        {
            if(s[i]!=s[i-1])
            {
                tmp=s[i-1];
                s[i-1]=s[i];
                s[i]=tmp;
                flag=true;
                break;
            }
        }
        if(flag)
        {
            cout << "YES\n" << s << "\n";
        }
        else
        {
            cout << "NO\n";
        }
    }
    return 0;
}
```

