---
title: Codeforces Round 946 (Div. 3) B.Symmetric Encoding
date: 2024-10-21 10:32
categories:
  - Codeforces
tags: 
  - Codeforces Round 946 (Div. 3)
---
[Codeforces Round 946 (Div. 3)](https://codeforces.com/contest/1974)

[B.Symmetric Encoding](https://codeforces.com/contest/1974/problem/B)

```cpp
// By SnowDream
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e6+10;
string b;
set<char> s;
int n;
char v[26];
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int t;
    cin >> t;
    while(t--)
    {
        cin >> n;
        cin >> b;
        string ans;
        for(auto c : b)
        {
            s.insert(c);
        }
        auto j=s.end();
        j--;
        for(char i : s)
        {
            v[i-'a']=*j;
            j--;
        }
        for(auto c:b)
        {
            ans+=v[c-'a'];
        }
        s.clear();
        cout << ans << "\n";
    }
    return 0;
}
```

