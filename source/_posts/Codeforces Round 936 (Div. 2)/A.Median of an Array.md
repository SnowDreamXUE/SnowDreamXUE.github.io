---
title: Codeforces Round 936 (Div. 2) A. Median of an Array
date: 2024-10-21 10:32
categories:
  - Codeforces
tags: 
  - Codeforces Round 936 (Div. 2)
---
[Codeforces Round 936 (Div. 2)](https://codeforces.com/contest/1946)

[A. Median of an Array](https://codeforces.com/contest/1946/problem/A)

中位数被定义为排序数组中索引 $\lceil \frac{n}{2} \rceil$ 处的数字，因此我们可以对数组进行排序并加以处理。

因此，我们先对数组进行排序，找出数组中的中位数，即数字 $a_{\lceil \frac{n}{2} \rceil}$ ，让它等于 $x$ 。为了使中位数增加，即至少变为 $x + 1$ ，数组中必须至少有 $n - \lceil \frac{n}{2} \rceil + 1$ 个数字大于或等于 $x + 1$ 。

现在，让我们找出 $a_t$ 等于 $x$ 的最大索引 $t$ 。然后我们知道，目前有 $n - t$ 个数字大于或等于 $x + 1$ （所有这样的 $a_i$ 即 $i > t$ ），这意味着至少需要进行 $(n - \lceil \frac{n}{2} \rceil + 1) - (n - t) = t - \lceil \frac{n}{2} \rceil + 1$ 次运算。我认为这个估计值是可以实现的，只需对从 $\lceil \frac{n}{2} \rceil$ 到 $t$ 的每个索引进行一次运算即可，因为这些索引下的所有数字都等于 $x$ ，所以在进行运算后，它们将等于 $x + 1$ 。最后，大于或等于 $x + 1$ 的数将等于 $(n - t) + (t - \lceil \frac{n}{2} \rceil + 1) = n - \lceil \frac{n}{2} \rceil + 1$ ，这正是我们所需要的。

```cpp
// By SnowDream
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=2e6+10;
ll a[N];
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
        int ans=0;
        for(int i=1;i<=n;++i)
        {
            cin >> a[i];
        }
        sort(a+1,a+n+1);
        int p = (n+1)/2;
        for(int i=p;i<=n;++i)
            if(a[i]==a[p])
                ans++;
        cout << ans << "\n";
    }
    return 0;
}
```

