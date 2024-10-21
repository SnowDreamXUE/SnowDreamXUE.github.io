---
title: Codeforces Round 934 (Div. 2) C. MEX Game 1
date: 2024-10-21 10:32
tags: 
  - Codeforces
---
[Codeforces Round 934 (Div. 2)](https://codeforces.com/contest/1944)

[C. MEX Game 1](https://codeforces.com/contest/1944/problem/C)

```cpp
#include<bits/stdc++.h>
using namespace std;
const int Max=1e6;
int g[Max];
int read() //快速读入函数
{
    int s = 0,f = 1;char ch = getchar();
    while (!isdigit(ch)) f = ch == '-'? -1 : 1, ch = getchar();
    while(isdigit(ch)) s = s * 10 + ch - '0', ch = getchar();
    return s * f;
}
int main()
{
    int t=read();
    while(t--)
    {
        int n=read();
        for(int i=0;i<=n;i++)
            g[i]=0;
        for(int i=1;i<=n;i++)
        {
            g[read()]++;//储存每个数字的出现次数
        }
        bool flag = false;//记录状态
        int mex;
        for(int i=0;i<=n;i++)//i需要小于等于n的区间循环
        {
            if(!g[i])
            {
                mex=i;
                break;
            }
            if(g[i]==1)
            {
                if(flag)
                {
                    mex=i;
                    break;
                }
                flag=true;//第一次出现单次出现的数，被爱丽丝拿走，故下次被bob拿走
            }
        }
        printf("%d\n",mex);
    }
}
```

