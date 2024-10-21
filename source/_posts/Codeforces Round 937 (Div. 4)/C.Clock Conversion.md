---
title: Codeforces Round 937 (Div. 4) C. Clock Conversion
date: 2024-10-21 10:32
categories:
  - Codeforces
tags: 
  - Codeforces Round 937 (Div. 4)
---
[Codeforces Round 937 (Div. 4)](https://codeforces.com/contest/1950)

[C. Clock Conversion](https://codeforces.com/contest/1950/problem/C)

代码：

```cpp
// By SnowDream
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e5+10;
string s;
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int t;
    cin >> t;
    while(t--)
    {
        cin >> s;
        string s1=s.substr(0,2);//储存小时
        string s2=s.substr(2,3);//储存分钟
        if(s1<"12"&&s1>"00")
            cout << s << " AM\n";
        else if(s1=="00")
            cout << "12" << s2 << " AM\n";
        else if(s1=="12")
            cout << "12" << s2 << " PM\n";
        else
        {
            int a=10*(s1[0]-'0')+(s1[1]-'0')-12;
            if(a<10)//22:00-12:00会出现不需要前导零的情况
                cout << "0" << a << s2 << " PM\n";
            else
                cout << a << s2 << " PM\n";
        }
    }
    return 0;
}
```



