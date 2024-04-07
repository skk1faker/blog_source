---
title: 二维hash
date: 2023-10-17 09:39:45
tags: acm,hash
---


# 二维hash表达式


```cpp
/*================================================================
*   Copyright (C) 2023 Wangxinpeng. All rights reserved.
*
*   filename：    uva11019.cpp
*   username:     skt1faker
*   create time:  19:27  2023.10.16
    email:        skk1faker@163.com
*   descripe:
*
================================================================*/


#include <bits/stdc++.h>
using namespace std;
#define ll long long
#define ull unsigned long long 
#define DEBUG0
const int mod = (int)1e9 + 7;
const int mod1 = 131;
const int mod2 = 13561;
const int maxx = (int)1e3 + 10;
ull hash_s[maxx][maxx]; // 二维hash
ull hash_f[maxx][maxx]; // 模版的hash
char s[maxx][maxx],f[maxx][maxx];
ull powi[maxx];
ull powj[maxx];

ull get_hash(int x1,int y1,int x2,int y2){
  return hash_s[x2][y2] - (hash_s[x1 - 1][y2]) * powi[x2 - x1 + 1] - hash_s[x2][y1 - 1] * powj[y2 - y1 + 1] + hash_s[x1 - 1][y1 - 1] * powi[x2 - x1 + 1] * powj[y2 - y1 + 1];
}

void init(int n){
  powi[0] = 1;
  powj[0] = 1;

  for(int i = 1;i<=n;i++){
    powi[i] = powi[i - 1] * mod1;
    powj[i] = powj[i - 1] * mod2;
  }
}

void init_hash(int n,int m,ull hash_list[][maxx],char ss[][maxx])
{
  for(int i = 1;i <= n;i++){
    for(int j = 1;j <= m;j++){
      hash_list[i][j] = hash_list[i][j - 1] * powj[1] + hash_list[i - 1][j] * powi[1] + (ss[i][j] - 'a') - hash_list[i - 1][j - 1] * powi[1] * powj[1];
    }
  }
}


int main()
{
  int T;
  cin>>T;
  init(maxx);
  while(T--){
    int n[2],m[2];
    scanf("%d%d",&n[0],&m[0]);
    for(int i = 1;i <= n[0];i++){
      scanf("%s",s[i] + 1);
    }
    init_hash(n[0],m[0],hash_s,s);
    scanf("%d%d",&n[1],&m[1]);
    for(int i = 1;i <= n[1];i++){
      scanf("%s",f[i] + 1);
    }
    init_hash(n[1],m[1],hash_f,f);
    int ans = 0;
    for(int i = n[1];i <= n[0];i++){
      for(int j = m[1];j <= m[0];j++){
        if(hash_f[n[1]][m[1]] == get_hash(i - n[1] + 1, j - m[1] + 1,i,j)){
          ans++;
          //cout<<i - n[1] + 1<<' '<<j - m[1] + 1<<endl;
        }
      }
    }
    cout<<ans<<endl;
  }
  return 0;
}

/*TLE
const int maxx = (int)1e3 + 10;
ll sums[maxx][maxx][2];
ll sums_template[maxx][maxx][2];
char val[maxx][maxx];
char template_val[maxx][maxx];
ll pw[maxx][2];
ll mod[2];

ll qpow(ll a, ll b, int mode) {
  if(pw[b][mode] != 0)
    return pw[b][mode];
  ll m = mod[mode];
  ll ans = 1;
  ll temp = a;
  while (b) {
    if (b & 1) {
      ans *= temp;
      ans %= m;
    }
    temp *= temp;
    temp %= m;
    b /= 2;
  }
  return pw[b][mode] = ans;
}

ll get_val(int x1, int y1, int x2, int y2, int m, int mode) {
  return (sums[x1][y1][mode] * qpow(26, (x2 - x1) * m + (y2 - y1), mode)) %
         mod[mode];
}

ll get_hash(int x1, int y1, int x2, int y2, int m, int mode) { // n表示列数
  ll ans = (sums[x2][y2][mode] + get_val(x1 - 1, y1 - 1, x2, y2, m, mode) 
            - get_val(x1 - 1, y2, x2, y2, m, mode) 
            - get_val(x2, y1 - 1, x2, y2, m, mode) + 2 * mod[mode]) % mod[mode];
  return ans;
}

void init(int n, int m,int length,char s[][maxx],ll sum[][maxx][2]) {
  for (int i = 1; i <= n; i++) {
    ll temp_sum[2] = {0};
    for (int j = 1; j <= m; j++) {
      for (int k = 0; k < 2; k++) {
        temp_sum[k] *= 26;
        temp_sum[k] += s[i][j] - 'a';
        temp_sum[k] %= mod[k];
        sum[i][j][k] = (sum[i - 1][j][k] * qpow(26 ,length,k) + temp_sum[k]) % mod[k];
      }
    }
  }
}

int main() { 
  int T;
  cin>>T;
  mod[0] = mod1;
  mod[1] = mod2;

  while(T--){
    int n,m;
    scanf("%d%d",&n,&m);
    for(int i = 1;i <= n;i++){
      scanf("%s",val[i] + 1);
    }
    init(n,m,m,val,sums);
    int n1,m1;
    cin>>n1>>m1;
    for(int i = 1;i <= n1;i++){
      scanf("%s",template_val[i] + 1);
    }
    if(n1 > n || m1 > m){
      cout<<0<<endl;
      continue;
    }
    init(n1,m1,m,template_val,sums_template);
    ll template_hash_val[2] = {sums_template[n1][m1][0] ,sums_template[n1][m1][1]};
    int ans = 0;
    for(int i = n1;i <= n;i++){
      for(int j = m1;j <= m;j++){
        int pre_i = i - n1 + 1, pre_j = j - m1 + 1;
        int flag = 1;
        for(int k = 0;k < 2;k++){
          if(template_hash_val[k] != get_hash(pre_i,pre_j,i,j,m,k))flag = 0;
        }
        ans += flag;
      }
    }
    cout<<ans<<endl;
  }
  return 0; 
}
*/

```
