---
title: 概率dp
date: 2024-03-13 08:16:42
tags:   
- dp
- 数学
mathjax: true
---

#  期望是什么


假如一个事件A对应多种取值（例如买彩票中奖，抛硬币），求解期望的方式是求和取值与取值对应概率的积，用公式表示为：
$$
    E(A) = \sum_{i = 0}^{n - 1}P_{i}A_{i}
$$

例如彩票中5、10、15元的概率为0.1、0.05、0.01，那么中奖钱数期望为$5 * 0.1 + 10 * 0.05 + 15 * 0.01$

# 期望的性质

1. 如A、B事件概率上独立，则有E(AB) = E(A)E(B)，概率独立是指$P_{A_{i}B_{i}} = P_{A_{i}}P_{B_{i}}$。（概率上独立和事件独立不是一回事，事件独立是指两个事件的结果互不影响，例如掷骰子的每次一都不会影响下一次的结果。事件AB概率上独立是指概率值满足$P_{A_{i}B_{i}} = P_{A_{i}}P_{B_{i}}$，与两事件是否独立无关）


**证明：**
$$
\begin{cases}
E(AB) &= \sum_{i = 0,j = 0}^{i<n,j<m}{P_{A_{i}B_{j}}A_{i}B_{j}}\\
&=\sum_{i = 0,j = 0}^{i<nj<n}P_{A_{i}}A_{i}P_{B_{j}}B_{j}\\
\end{cases}
$$
观察发现，求和式子中A、B相关求解可分离（联想积分处理方式，如$\int_{0}^{1}\int_{2}^{3}xydxdy = \int_{0}^{1}xdx\int_{2}^{3}ydy$ ）故可将其拆分为如下表达式：

$$
\begin{cases}
E(AB) &=\sum_{i = 0,j = 0}^{i<n,j<n}P_{A_{i}}A_{i}P_{B_{j}}B_{j}\\
 &=\sum_{i = 0}^{i<n}P_{A_{i}}A_{i}\sum_{j = 0}^{j < n}P_{B_{j}}B_{j}\\
 &=E(A)E(B)
\end{cases}
$$

2. $E(A + B) \neq E(A) + E(B)$

可尝试给出表达式的形式观察表达式是否相等

$$
E(A+B) = \sum_{i = 0,j = 0}^{i < n,j<m}P_{A_{i}B_{j}}(A_{i} + B_{j})\\
E(A) + E(B) = \sum_{i = 0}^{n}P_{A_{i}}A_{i} + \sum_{j = 0}^{m}P_{B_{i}}B_{i}
$$


**3. 事件A会导致事件B、C中的一个发生，概率为$P_{B}、P_{C}$，则期望表达式为：**
$$
E(A) = P_{B}E(B) + P_{C}E(C)
$$
证明：

假设B事件取值情况为$B_{i}$，相应概率为$P_{B_{i}}$。C事件取值情况为$C_{j}$相应概率为$P_{C_{j}}$，则有E(A)的表达式为：
$$
\begin{cases}
E(A) &= \sum_{i = 0}^{n - 1}P_{B}P_{B_{i}}B_{i} + \sum_{j = 0}^{m - 1}  P_{C}P_{C_{j}}C_{j}\\
&= P_{B}\sum_{i = 0}^{n - 1}P_{B_{i}}B_{i} + P_{C}\sum_{j = 0}^{m - 1}  P_{C_{j}}C_{j}
&= P_{B}E(B) + P_{C}E(C)
\end{cases}
$$

# 题目


### 题目描述

有一只甲壳虫想要爬上一颗高度为 n 的树，它一开始位于树根, 高度为 0，当它尝试从高度 i−1 爬到高度为 i 的位置时有 Pi 的概率会掉回树根, 求它从树根爬到树顶时, 经过的时间的期望值是多少。
### 输入格式

输入第一行包含一个整数n表示树的高度。

接下来 n 行每行包含两个整数 xi,yi, 用一个空格分隔，表示 Pi=xiyi 。
### 输出格式

输出一行包含一个整数表示答案，答案是一个有理数，请输出答案对质数 998244353 取模的结果。其中有理数 $\frac{a}{b}$ 对质数 P 取模的结果是整数 c 满足 0≤cP 且 c⋅b≡a( mod P) 。

答：
绘制事件的展开树，利用上方期望第三个特点给出结论可有如下推导：
![_概率dp甲壳虫爬树](_概率dp甲壳虫爬树.png)
观察s与t可通过迭代求解，接触s与t即可完成求解


```cpp
/*================================================================
*   Copyright (C) 2024 Wang XinPeng. All rights reserved.
*   
*   filename:     P8774.cpp
*   username:     skt1faker
*   create time:  22:35  2024.03.12
*   modify time:  09:44  2024.03.13
*   email:        skk1faker@163.com
*   descripe:     
*
================================================================*/

#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define lll __int128
#define DEBUG0
const int mod1 = 1e9 + 7;
const int mod2 = 998244353;
const int maxx = (int)1e5 + 10; 
ll p[maxx];
ll inv_mod(ll a)
{
  int b = mod2 - 2;
  ll ans = 1;
  while(b){
    if(b & 1)
      ans = (ans * a) % mod2;
    b /= 2;
    a = (a * a) % mod2;
  }
  return ans;
}

int main()
{
  int n;
  cin>>n;
  ll ans = 0;
  ll x,y;
  for(int i = 0;i < n;i++){
    scanf("%lld%lld",&x,&y);
    p[i] = (x * inv_mod(y)) % mod2;
  }
  ll s = 0;
  ll t = 0;
  ll temp_s = 1;
  for(int i = 0;i < n;i++){
    s = (s + (p[i] * temp_s) % mod2) % mod2;
    t = (t + (((p[i] * (i + 1)) % mod2) * temp_s) % mod2 ) % mod2;
    temp_s = (temp_s * (1 - p[i] + mod2) % mod2) % mod2;
  }
  t = (t + (n * temp_s) % mod2) % mod2;
  ans = (t * inv_mod(1 - s + mod2)) % mod2;
  cout<<ans<<endl;
  return 0;
}
```

## 补充，乘法逆元的计算

### 逆元的作用

逆元是一种规定，规定了模运算中除法的计算方式，$x/y\ mod\ p$可表示为$xy^{-1}\ mod\ p$，其中$y^{-1}$为模p下y的逆元。

由于y / y mod = 1,所以$y \times y^{-1}\ mod\ p= 1$。

### 如何求解逆元

费马小定理为如下形式：
$$
1 = a^{p - 1} mod p,p为质数
$$

可以得到在模p计算下，a的逆元为$a^{p - 2}$，使用快速幂模p求解即可。

```cpp
ll inv_mod(ll a)
{
  int b = p - 2;
  ll ans = 1;
  while(b){
    if(b & 1)
      ans = (ans * a) % p;
    b /= 2;
    a = (a * a) % p;
  }
  return ans;
}
```
