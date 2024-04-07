---
title: cf1606-Xor-Paths-双向搜索
date: 2023-11-07 18:27:46
tags: 
- 双向搜索
---
# 题意

在一个$ 20 \times 20 $的网格上，每个格子上有一些权值，选出一个从$(1,1)$点到$(n,m)$点的路径，这条路径满足：

- 路径的当前点到下一个点的运动方向只能是坐标值增加的方向，即$(i , j)$可以移动到$(i,j+1)、(i + 1,j)$
- 路径上权值异或和为k，其中$0 \le k  \le 10^{18}$

现求满足条件的路径数

# 题解

如果k值很小的话，那么一个很简单的一个dp方案就出现了
$$dp[i][j][k_1] = dp[i - 1][j][k_1 \oplus k] + dp[i][j - 1][k \oplus k_1]$$
但是观察k的取值范围，如果真的使用这个转移方程，只会导致空间开的特别大。所以这里使用stl中的map，`map<ll,ll>dp[][]`来完成这个转移。

当然到这步其实还没有完全结束，如果使用这种转移，k的数量一样会很多，我们假设每种路径的异或值都不一样，那么每个$dp[x][y]$中的结果存储元素个数应该是$C_{x + y - 2}^{x - 1}$种，$C_{20}^{10} = \frac{20!}{10! \times 10!} $为了解决这个问题可以使用双向搜索。

```cpp
/*================================================================
*   Copyright (C) 2023 Wangxinpeng. All rights reserved.
*
*   filename：    cf1006f.cpp
*   username:     skt1faker
*   create time:  09:37  2023.11.07
    email:        skk1faker@163.com
*   descripe:
*
================================================================*/

#include <bits/stdc++.h>
using namespace std;
#define ll long long
#define DEBUG0
const int mod1 = 1e9 + 7;
const int mod2 = 998244353;
const int maxx = 25;
map<ll,ll> dp[2][maxx][maxx];
ll v[maxx][maxx];
int main() {
  int n, m;
  ll k;
  cin >> n >> m >> k;
  for (int i = 0; i < n; i++) {
    for (int j = 0; j < m; j++) {
      scanf("%lld", &v[i][j]);
    }
  }
  int split_posi = (n - 1);
  dp[0][0][0][v[0][0]] = 1;
  for (int i = 0; i < n; i++) {
    for (int j = 0; j < m; j++) {
      if(i == 0 && j == 0 || (i + j) > split_posi)continue;
      if(i - 1 >= 0){
        for (map<ll,ll>::iterator iter = dp[0][i - 1][j].begin();iter != dp[0][i - 1][j].end(); iter++) {
          dp[0][i][j][iter->first ^ v[i][j]] += iter->second;
        }
      }
      if(j - 1 >= 0){
        for (map<ll,ll>::iterator iter = dp[0][i][j - 1].begin();iter != dp[0][i][j - 1].end(); iter++) {
          dp[0][i][j][iter->first ^ v[i][j]] += iter->second;
        }
      }
    }
  }
  if(n + m - 2 == split_posi)
    dp[1][n - 1][m - 1][0] = 1;
  else 
    dp[1][n - 1][m - 1][v[n - 1][m - 1]] = 1;
  for (int i = n - 1; i >= 0; i--) {
    for (int j = m - 1; j >= 0; j--) {
      if(i == n - 1 && j == m - 1 || (i + j) < split_posi)continue;
      if(i + 1 < n){
        for (map<ll,ll>::iterator iter = dp[1][i + 1][j].begin();iter != dp[1][i + 1][j].end(); iter++) {
          if(i + j == split_posi)
            dp[1][i][j][iter->first] += iter->second;
          else 
            dp[1][i][j][iter->first ^ v[i][j]] += iter->second;
        }
      }
      if(j + 1 < m){
        for (map<ll,ll>::iterator iter = dp[1][i][j + 1].begin();iter != dp[1][i][j + 1].end(); iter++) {
          if(i + j == split_posi)
            dp[1][i][j][iter->first] += iter->second;
          else 
            dp[1][i][j][iter->first ^ v[i][j]] += iter->second;
        }
      }
    }
  }
  ll ans = 0;
  for(int i = 0;i < n;i++){
    int j = split_posi - i;
    if(!(0 <= j && j < m))continue;
    for(map<ll,ll>::iterator iter = dp[1][i][j].begin();iter != dp[1][i][j].end();iter++){
      ans += iter->second * dp[0][i][j][(k ^ (iter->first))];
      //cout<<(k ^ iter->first)<<' '<<dp[0][i][j][(k ^ (iter->first))]<<endl;;
    }
  }
  cout<<ans<<endl;
  return 0;
}

```
