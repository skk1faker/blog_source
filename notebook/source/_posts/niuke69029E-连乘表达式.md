---
title: niuke69029E-连乘表达式
date: 2023-11-11 10:50:05
tags: 
- 数学
- 牛客练习赛118
- 连乘式
categories:
- acm
- 数学
---

链接：https://ac.nowcoder.com/acm/contest/69029/E
来源：牛客网

题目描述
给定 n 个模 998244353 意义下的函数，第 i 个函数为 $f_i(x)=a_ix+b_i$

q 次询问，每次询问一个区间 [l,r] 以及一个整数 y，在模 998244353 意义下解如下方程 $f_l(f_{l+1}(…f_{r−1}(f_r(x))…))≡y(mod998244353)$，若有多个解 x 输出 inf，无解输出 −1。否则输出解 x。
输入描述:

第一行一个整数 $n(1≤n≤3×10^5)$

第二行 n 个非负整数，表示 $a_i(0≤a_i<998244353)$

第三行 n 个非负整数，表示 $b_i(0≤b_i<998244353)$

接下来一行一个整数 $q(1≤q≤3×105)$

接下来 q 行每行三个整数 $l,r,y(1≤l≤r≤n,0≤y<998244353)$

输出描述:

接下来 q 行每行输出答案。若有多个解 x 输出 inf，无解输出 −1。否则输出解 x。

示例1
输入
```
3
1 5 2
5 8 2
4
1 2 0
2 3 9
1 3 7
1 1 5
```
输出
```
199648868
299473305
199648869
0
```

# 题解

首先猜测表达式结果$f_l(f_{l+1}(…f_{r−1}(f_r(x))…)) = f(l,r)= A(l,r)x + B(l,r)$，然后可以得到如下表达式

![tuidao](tuidao.png)
这里主要需要注意的是连乘法中的想乘元素为0时如何处理，详见{% post_link 任意区间连乘计算陷阱 %}这篇文章

有了这样的表达式，我们探讨如何求解x

最直接的方案是 $x = (y - B(l,r)) / A(l,r)$但是这里要保证A(l,r) != 0，如果为0的话就说明源表达式为$y = 0 * x + B(l,r)$ 这个时候x是任意值都可以，只需要判断y == B(l,r)即可，两者相等则说明多解，不相等则说明无解。


```cpp
/*================================================================
*   Copyright (C) 2023 Wangxinpeng. All rights reserved.
*   
*   filename：    D.cpp
*   username:     skt1faker
*   create time:  20:52  2023.11.10
    email:        skk1faker@163.com
*   descripe:     
*
================================================================*/

#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define DEBUG0
const int mod1 = 1e9 + 7;
const int mod2 = 998244353;
const int maxx = (int)3e5 + 10;

ll A[maxx],B[maxx];
ll rev(ll a)
{
  int b = mod2 - 2;
  ll temp = a;
  ll ans = 1;
  while(b){
    if(b & 1) ans = (ans * temp) % mod2;
    temp = (temp * temp) % mod2;
    b >>= 1;
  }
  return ans;
}

ll A_(int l,int r){
  return ((A[r] * rev(A[l - 1])) % mod2);
}
ll B_(int l,int r){
  return ((((B[r] - B[l - 1] + mod2) % mod2) * rev(A[l - 1])) % mod2);
}
int rposi[maxx];    //右侧第一个0的位置
int main()
{
  int n;
  cin>>n;
  A[0] = 1;
  for(int i = 1;i <= n;i++){
    ll a;
    scanf("%lld",&a);
    if(a == 0){
      rposi[i] = i;
      a = 1;
    }
    A[i] = (A[i - 1] * a) %mod2;
  }
  rposi[n + 1] = n + 1;
  for(int i = n;i >= 1;i--){
    if(rposi[i] == 0){
      rposi[i] = rposi[i + 1];
    }
  }
  B[0] = 0;
  for(int i = 1;i <= n;i++){
    ll b;
    scanf("%lld",&b);
    B[i] = (B[i - 1] + ((b * A[i - 1]) % mod2)) % mod2;
  }
  int q;
  cin>>q;
  for(int i =0 ;i < q;i++){
    ll l,r,y;
    cin>>l>>r>>y;
    ll x;
    if(rposi[l] > r){
      x = (((y + mod2 - B_(l,r)) % mod2) * rev(A_(l,r))) % mod2; 
      cout<<x<<endl;
    }
    else {
      r = rposi[l];
      //x = (((y + mod2 - B_(l,r)) % mod2) * rev(A_(l,r))) % mod2; 
      if(B_(l,r) == y){
        cout<<"inf"<<endl;
      }
      else {
        cout<<-1<<endl;
      }
    }
  }
  return 0;
}

```
