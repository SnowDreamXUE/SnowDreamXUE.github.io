---
title: Codeforces Round 934 (Div. 2) B. Equal XOR
date: 2024-10-21 10:32
categories:
  - Codeforces
tags:
  - Codeforces Round 934 (Div. 2)
---
[Codeforces Round 934 (Div. 2)](https://codeforces.com/contest/1944)

[B. Equal XOR](https://codeforces.com/contest/1944/problem/B)

测试案例解释有误导性，异或运算，相同数字异或为0，每个数字都出现两次，不是两个都在一边，就是一边一个，只需要分开来输出即可

```cpp
#include<bits/stdc++.h>
using namespace std;
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int t;
    cin >> t;
    while(t--)
    {
        int n,k;
        cin >> n >> k;
        int a[2*n];
        vector<int> pos[n+1];//储存出现位置的容器
        for(int i=0;i<2*n;++i)
        {
            cin >> a[i];
            pos[a[i]].push_back(i);//将a[i]的位置i记录
        }
        vector<int> l,r;
        /*如果一个数字 i 在前 n 个位置出现两次,且 l 的长度小于 2k,那么就将 i 添加到 l 中两次。
         * 如果一个数字 i 在后 n 个位置出现两次,且 r 的长度小于 2k,那么就将 i 添加到 r 中两次。*/
        for(int i=1;i<=n;++i)//数字从1开始
        {
            if(pos[i].front() < n && pos[i].back() <n && l.size()<2*k)
            {
                l.push_back(i);
                l.push_back(i);
            }
            else if(pos[i].front()>=n && pos[i].back() >=n &&r.size()<2*k)
            {
                r.push_back(i);
                r.push_back(i);
            }
        }
        for(int i=1;i<=n;++i)//处理剩余的数字。如果一个数字 i 在前后 n 个位置各出现一次,且 l 的长度小于 2k,那么就将 i 添加到 l 和 r 中。
        {
            if((pos[i].front()<n&&pos[i].back()<n) || (pos[i].front()>=n&&pos[i].back()>=n))
            {
                continue;
            }
            if(l.size()<2*k)
            {
                l.push_back(i);
                r.push_back(i);
            }
        }
        for(auto i : l)
            cout << i << " ";
        cout << endl;
        for(auto i : r)
            cout << i << " ";
        cout << endl;
    }
}
```

