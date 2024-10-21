---
title: Codeforces Round 937 (Div. 4) D. Product of Binary Decimals
date: 2024-10-21 10:32
categories:
  - Codeforces
tags: 
  - Codeforces Round 937 (Div. 4)
---
[Codeforces Round 937 (Div. 4)](https://codeforces.com/contest/1950)

[D. Product of Binary Decimals](https://codeforces.com/contest/1950/problem/D)

本人使用了朴实无华的打表，从最大的开始代入遍历

代码：

```cpp
// By SnowDream
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=1e5+10;
int a[30]={10,11,100,101,110,
          111,1000,1001,1010,1011,
          1100,1101,1110,1111,10000,
          10001,10010,10011,10100,10101,
          10110,10111,11000,11001,11010,
          11011,11100,11101,11110,11111};
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int t;
    cin >> t;
    while(t--)
    {
        int n;
        cin >> n;
        bool flag=false;
        if(n==1)
            flag= true;
        else
        {
            for(int i=29;i>=0&&n>1;i--)
            {
                if(n%a[i]==0)
                {
                    flag=true;
                    n=n/a[i];
                    i=29;//n改变，故从头再判断遍历
                }
                else
                    flag= false;
            }
        }
        if(flag)
            cout << "YES\n";
        else
            cout << "NO\n";
    }
    return 0;
}
```

