---
title: luogu-p2534估值函数问题
tags:
- 数学
- 搜索
---

# 题意

存在序列$a_{1}, a_{2} ... a_{n}$, 通过翻转$(a[1...i])$改变序列,问最少操作多少次才能将序列变为从小到大.

# 题解

## 估值函数的特点

`IDA*`估值函数$h(a)$用于减枝, 通常使用方法如下,其中a为当前搜索到达的状态, `nowLength`和`maxLength`分别为已经走过的距离和最大可搜索的距离.
```cpp
int main(){
    for(int i = 0;;i++){
        if(dfs(a, 0,i)){        // a为初始状态
            printf("ans = %d", ans);
            break;
        }
    }
}
void dfs(int a[], int nowLength, int maxLength){    // maxLength 可作为最后的答案

    if(h(a) + nowLength < maxLength){ // 当前已走距离 + 预测距离 < 最大距离 ==> 可以裁剪调了
        return false;   // 没有找到
    }
    if(h(a) == 0)return true;
    // 遍历a的后续.

}
```

为了不将答案通过剪枝去掉, 必须满足`估值函数计算的距离 <= 到达终端的准确距离`,这样,如下减枝才不会将答案裁掉. 因为如果裁掉,那么根据ida\*的操作, 后续的得到的答案距离会加大(因为被裁掉时答案长度可能已经被枚举出来了), 这显然不符合题意.


## 根据每次操作的特点,设计估值函数

我们可以发现,每一次翻转数列$a_{1} ... a_{i}$时, 数列中只有$a_{i}$与$a_{i + 1}$相邻变为了$a_{1}$与$a_{i + 1}$相邻, i = n时不改变数列中两元素的相邻关系, 也就是说一次翻转<font color = "red">最多</font>能改变一对数的相邻关系,假设每一次操作都能让两个错误相邻的数相邻关系正确,那么一个数列的最小操作步数应为相邻两数差不为1的数量,估值函数$h(a)$如下:
$$
    h(a) = \sum_{i = 1}^{i + 1 <= n}(|a_{i} - a_{i + 1}| > 1)
$$

但这样的函数并不能保证搜索终点为"数列从小到大"还是"数列从大到小". 一些人给出了取巧的方法. 

### 方案一(正确)

添加$a_{n + 1} = n + 1$,且
$$
    h(a) = \sum_{i = 1}^{i + 1 <= n + 1}(|a_{i} - a{i + 1}| > 1)
$$

### 方案二(不正确)
添加$a_{0} = 0$,且
$$
    h(a) = \sum_{i = 0}^{i + 1 <= n}(|a_{i} - a{i + 1}| > 1)
$$

注意这里只有方案一正确,方案二错误. 原因是$h(a)$成立的前提是每一次翻转数列$a[1 -- i]$<font color="red">最多</font>能改变一对元素的相邻关系,添加$a_{n+1}$并不会影响上述$h(a)$成立条件,但是添加$a_{0}$会导致每次翻转最多会改变<font color = "red">两对</font>元素间的相邻关系发生改变,这时应调整h(a)为如下,才能满足方案二

$$
    g(a) = \sum_{i = 0}^{i + 1 <= n}(|a_{i} - a{i + 1}| > 1)
    h(a) = \lceil (g(a) / 2) \rceil
$$
假设每次能调整正确两个元素之间的关系,调整最后剩下一对关系不整确元素时,再使用一次操作将关系调整正确.  

但这样的操作会导致代码超时.

# 代码

```cpp

/*================================================================
*   Copyright (C) 2025 Wang XinPeng. All rights reserved.
*   
*   filename:     p2534.cpp
*   username:     skt1faker
*   create time:  20:25  2025.04.28
*   modify time:  20:25  2025.04.28
*   email:        skk1faker@163.com
*   descripe:     
*
================================================================*/

#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define lll __int128
#define DEBUG0
#define L(x) (x << 1)
#define R(x) (L(x) + 1)
const int mod1 = 1e9 + 7;
const int mod2 = 998244353;
const int maxx = 1e5 + 10;

int a[20],n,id[102];
int b[20];

int h(){
	int cnt = 0;
	for(int i = 0;i < n;i++){
		cnt += (abs(a[i] - a[i + 1]) > 1);
	}
	return cnt;
}

void output()
{
	for(int i =0 ;i < n;i++)
		cout<<a[i]<<' ';
	cout<<endl;
}
bool dfs(int now_deep, int max_deep){
	int hh = h();
	if(hh + now_deep > max_deep){
		return false;
	}
	if(hh == 0){
		return true;
	}
	bool flag = true;
	for(int i = n - 1;i >= 0;i--){
		int c[20];
		memcpy(c,a,sizeof(a));
		// 改变状态
		for(int j =0,k = i;j < k;j++,k--){
			swap(a[j],a[k]);
		}
		if(dfs(now_deep + 1, max_deep)){
			memcpy(a,c,sizeof(b));
			return true;
		}
		// 状态改回来
		memcpy(a,c,sizeof(b));
	}
	return false;
}

int main()
{
	cin>>n;
	for(int i = 0;i < n;i++){
		scanf("%d",&a[i]);
		b[i] = a[i];
	}
	a[n] = n;
	sort(b,b+n);
	for(int i = 0;i < n;i++){
		id[b[i]] = i;
	}
	for(int i = 0;i < n;i++){
		a[i] = id[a[i]];
	}
	for(int deep = 0;deep <= 2 * n;deep++){
		if(dfs(0,deep)){
			cout<<deep<<endl;
			return 0;
		}
	}
    return 0;
}


```
