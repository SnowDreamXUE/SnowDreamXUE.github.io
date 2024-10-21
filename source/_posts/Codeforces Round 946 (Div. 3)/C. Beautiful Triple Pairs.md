---
title: Codeforces Round 946 (Div. 3) C.Beautiful Triple Pairs
date: 2024-10-21 10:32
categories:
  - Codeforces
tags: 
  - Codeforces Round 946 (Div. 3)
---
[Codeforces Round 946 (Div. 3)](https://codeforces.com/contest/1974)

[C.Beautiful Triple Pairs](https://codeforces.com/contest/1974/problem/C)

代码的主要作用是计算在给定的多个测试用例中，每个测试用例的数组中特定模式（对和三元组）出现的次数，并对这些模式出现的次数进行加减操作以得到最终结果。

具体步骤如下：

1. 读取测试用例数量 `t`。
2. 对于每个测试用例，使用两个 `map` 分别记录三元组和二维对的出现次数。
3. 读取数组长度 `n` 和数组元素。
4. 初始化结果 `res` 为 0。
5. 从数组的第三个元素开始遍历：
   - 计算并增加以当前元素为结尾的不同模式的对的出现次数。
   - 减去当前三元组出现次数的三倍（避免重复计数）。
6. 将最终结果输出。

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;

// 定义一些全局变量
int i,j,k,n,m,t,a[1005000];
ll res;

int main(){
	ios::sync_with_stdio(0); cin.tie(0);
	// 读取测试用例数量
	cin >> t;
	while(t--){
		// 定义两个map，一个用于存储三元组，一个用于存储二维对
		map<tuple<int, int, int>, int> mp1;
		map<pair<int, int>, int> mp[4];
		// 读取数组的大小
		cin >> n;
		// 读取数组元素
		for(i = 1; i <= n; i++) cin >> a[i];
		// 初始化结果
		res = 0;
		// 从第3个元素开始处理，因为至少需要前两个元素
		for(i = 3; i <= n; i++){
			// 统计以a[i-1]和a[i]为结尾的对出现的次数
			res += mp[1][{a[i-1], a[i]}]++;
			// 统计以a[i-2]和a[i]为结尾的对出现的次数
			res += mp[2][{a[i-2], a[i]}]++;
			// 统计以a[i-1]和a[i-2]为结尾的对出现的次数
			res += mp[3][{a[i-1], a[i-2]}]++;
			// 减去出现的三元组(a[i-2], a[i-1], a[i])的三倍次数
			res -= mp1[{a[i-2], a[i-1], a[i]}] * 3;
			// 增加当前三元组的计数
			mp1[{a[i-2], a[i-1], a[i]}]++;
		}
		// 输出结果
		cout << res << '\n';
	}
}

```

