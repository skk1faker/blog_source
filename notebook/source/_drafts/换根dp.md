---
title: 换根dp
tags:
- dp
- 换根dp
- 树形dp
---

# 换根dp

换根dp的基本思路是

1. 先计算一棵树中所有节点的dp值。
2. 将根节点a附近的节点b重新视为根，此时只有ab节点的父子关系发生了变化，其他节点的关系并不改变，依赖于父子关系的dp值只需要改变ab节点处的dp值
    2.1 现将父节点a与子节点b断开，改变相应的dp值，即改变父节点a的dp值，将b的贡献去掉。
    2.2 将b节点的值加上节点a的贡献，得到新的dp值，完成换根过程。
3. 利用dfs过程从跟开始，不断进行换根，每次换根时间为`O(1)`，换根过程先记录原根节点与即将变换的根节点dp值，然后变换，变换后经过dfs过程后应将dp值恢复，使得下一次换根过程正确计算，可看下述代码的dfs2过程。

以下为luogu的p3647连珠线

```cpp
/*================================================================
*   Copyright (C) 2024 Wang XinPeng. All rights reserved.
*   
*   filename:     p3647.cpp
*   username:     skt1faker
*   create time:  11:06  2024.05.04
*   modify time:  11:06  2024.05.04
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
const int maxn = 400011;

struct Edge{
  int length;
  int to;
  int next;
};
ll dp[2][maxn],head[maxn],now_edge_num,f[maxn][2];
Edge e[maxn << 1];
void add_edge(int a,int b,int c){
  e[now_edge_num].length = c;
  e[now_edge_num].to = b;
  e[now_edge_num].next = head[a];
  head[a] = now_edge_num++;
}
void init(int n){
  memset(dp,-1,sizeof(dp));
  for(int i =0;i < n;i++){
    f[i][0] = -2e16;
    f[i][1] = -2e16;
  }
}
int dfs(int status,int now_posi,int fa);
int get_max(int edge_id,int now_posi){
  int num = 0;
  for(int i = head[e[edge_id].to];i != -1;i = e[i].next){
    num++;
    if(num > 1)break;
  }
  if(num == 1){
    dp[0][e[edge_id].to] = 0;
    return 0;
  }
  return max(dfs(0,e[edge_id].to,now_posi), dfs(1,e[edge_id].to,now_posi) + e[edge_id].length);
}
int dfs(int status,int now_posi,int fa){
  if(dp[status][now_posi] >= 0)return dp[status][now_posi];
  dp[status][now_posi] = 0;
  if(status == 0){
    for(int i = head[now_posi];i != -1 ;i = e[i].next){
      if(e[i].to == fa)continue;
      dp[status][now_posi] += get_max(i,now_posi);
    }
  }
  else {
    int flag = 0;
    int length = 0; // TODO:可能值会更小
    for(int i = head[now_posi];i != -1 ;i = e[i].next){
      int next_posi = e[i].to;
      if(next_posi == fa)continue;
      if(flag){
        if(length <= e[i].length + dfs(0,next_posi,now_posi) - get_max(i,now_posi)){
          length = max(length,e[i].length + dfs(0,next_posi,now_posi) - get_max(i,now_posi));
          f[now_posi][1] = f[now_posi][0];
          f[now_posi][0] = length;
        }
        else if(f[now_posi][1] < e[i].length + dfs(0,next_posi,now_posi) - get_max(i,now_posi)){
          f[now_posi][1] = e[i].length + dfs(0,next_posi,now_posi) - get_max(i,now_posi);
        }
      }
      else {
        length = e[i].length + dfs(0,next_posi,now_posi) - get_max(i,now_posi);
        f[now_posi][1] = f[now_posi][0];
        f[now_posi][0] = length;
        flag = 1;
      }
    }
    dp[status][now_posi] = dfs(0,now_posi,fa) + length;
  }
  return dp[status][now_posi];
}
ll ans;
void dfs2(int now_posi,int fa,int edge_id){
  // 换根过程
  int temp[8];
  if(fa != -1){
    temp[0] = dp[0][fa];
    temp[1] = dp[1][fa];
    temp[2] = dp[0][now_posi];
    temp[3] = dp[1][now_posi];
    temp[4] = f[fa][0];
    temp[5] = f[fa][1];
    temp[6] = f[now_posi][0];
    temp[7] = f[now_posi][1];
    int length;
    
    dp[0][fa] -= get_max(edge_id,fa);
    if(f[fa][0] == e[edge_id].length + dp[0][now_posi] - get_max(edge_id,fa)){   // 只能使用次大
      dp[1][fa] = dp[0][fa] + f[fa][1];
    }
    else {
      dp[1][fa] =  dp[0][fa] + f[fa][0];
    }
    dp[0][now_posi] += get_max(edge_id ^ 1,now_posi);
    if(f[now_posi][0] <= e[edge_id ^ 1].length + dp[0][fa] - get_max(edge_id ^ 1, now_posi)){
      f[now_posi][1] = f[now_posi][0];
      f[now_posi][0] = e[edge_id ^ 1].length + dp[0][fa] - get_max(edge_id ^ 1, now_posi);
    }
    else if(f[now_posi][1] <= e[edge_id ^ 1].length + dp[0][fa] - get_max(edge_id ^ 1, now_posi)){
      f[now_posi][1] = e[edge_id ^ 1].length + dp[0][fa] - get_max(edge_id ^ 1, now_posi);
    }
  }
  ans = max(ans,dp[0][now_posi]);
  for(int i = head[now_posi]; i != -1;i = e[i].next){    // 
    if(e[i].to == fa)continue;
    dfs2(e[i].to,now_posi,i);
  }
  if(fa != -1){
    dp[0][fa] = temp[0];
    dp[1][fa] = temp[1];
    dp[0][now_posi] = temp[2];
    dp[1][now_posi] = temp[3];
    f[fa][0] = temp[4];
    f[fa][1] = temp[5];
    f[now_posi][0] = temp[6];
    f[now_posi][1] = temp[7];
  }
}
void solve(int n){
  init(n);
  dfs(0,0,-1);
  dfs(1,0,-1);
  dfs2(0,-1,-1);
  cout<<ans<<endl;
}
int main()
{
  int n;
  cin>>n;
  memset(head,-1,sizeof(head));
  for(int i = 1;i < n;i++){
    int a,b,c;
    scanf("%d%d%d",&a,&b,&c);
    a--;
    b--;
    add_edge(a,b,c);
    add_edge(b,a,c);
  }
  solve(n);
  return 0;
}
```
