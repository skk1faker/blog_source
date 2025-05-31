---
title: dp记录路径技巧
tags:
  - dp
  - error
date: 2025-03-24 22:17:33
---


# 路径记录

路径记录只需在找到答案时记录当前选择即可.注意对max和min的处理,如下的方式即为错误的处理方式

## 错误方式
```cpp
if(j - a[k] >= 0){
    dp[k][i][j] = max(a: dp[k][i][j], b: dp[k - 1][i][j - a[k]]);
    pre_[k][i][j].first = i;
    pre_[k][i][j].second = j - a[k];
}
```
其中pre_和dp分别用来记录路径和更新答案,但是,pre_的更新时机应在dp实际发生更新时,所以做如下改动.

## 正确方式

将更新时机加上,即可得到正确代码
```cpp
if(j - a[k] >= 0 && dp[k][i][j] < dp[k - 1][i][j - a[k]]){
    dp[k][i][j] = max(a: dp[k][i][j], b: dp[k - 1][i][j - a[k]]);
    pre_[k][i][j].first = i;
    pre_[k][i][j].second = j - a[k];
}
```
# 路径记录方式

记录路径时应将所有状态全部记录下来,否则可能会导致前向寻找出现错误.

## 错误的方式
```cpp
if(dp[k - 1][i][j] == 1){
    dp[k][i][j] = dp[k - 1][i][j];
    pre_[k][i][j] = pre_[k - 1][i][j];
}
if(i - a[k] >= 0 && dp[k - 1][i - a[k]][j] == 1){ //> dp[k][i][j]{
    dp[k][i][j] = max(dp[k][i][j], dp[k - 1][i -a[k]][j]);
    pre_[k][i][j].first = i - a[k];
    pre_[k][i][j].second = j;
}
if(j - a[k] >= 0 && dp[k - 1][i][j - a[k]] == 1){ //> dp[k][i][j]){
    dp[k][i][j] = max(dp[k][i][j], dp[k - 1][i][j - a[k]]);
    pre_[k][i][j].first = i;
    pre_[k][i][j].second = j - a[k];
}
```

注意这里dp中的k没有记录在`pre_`中这很可能导致最终寻找答案时出现问题,所以务必类似与下方操作处理,否则可能会wa


## 正确的方式
```cpp
if(dp[k - 1][i][j] == 1){
    dp[k][i][j] = dp[k - 1][i][j];
    pre_[k][i][j] = pre_[k - 1][i][j];
}
if(i - a[k] >= 0 && dp[k - 1][i - a[k]][j] == 1){ //> dp[k][i][j]{
    dp[k][i][j] = max(dp[k][i][j], dp[k - 1][i -a[k]][j]);
    pre_[k][i][j].first = k - 1;
    pre_[k][i][j].second.first = i - a[k];
    pre_[k][i][j].second.second = j;
}
if(j - a[k] >= 0 && dp[k - 1][i][j - a[k]] == 1){ //> dp[k][i][j]){
    dp[k][i][j] = max(dp[k][i][j], dp[k - 1][i][j - a[k]]);
    pre_[k][i][j].first = k - 1;
    pre_[k][i][j].second.first = i;
    pre_[k][i][j].second.second = j - a[k];
}
```
这里将dp中的所有状态记录在`pre_`中,使得找下一状态十分方便.

# 给出一个错误例子

## 题意

数组s中有k个数,问对于x,是否可通过将k分为两组(一些数可以不选), 使得两组数和的积等于x,并给出这种组合.

## 题解

典型的二维背包

## 代码
### 错解
这个错解就是因为路径中没有正确的记录dp状态,更新路径的时机也并不正确. 错误将会在代码中标注
```cpp
/*================================================================
*   Copyright (C) 2025 Wang XinPeng. All rights reserved.
*   
*   filename:     E.cpp
*   username:     skt1faker
*   create time:  19:11  2025.03.21
*   modify time:  19:11  2025.03.21
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
const int maxx = 1e2 + 10;
int dp[102][maxx][maxx];
int a[maxx];
pair<int,int>pre_[102][maxx][maxx];
vector<int>as[2];
void get_ans(int i,int j,int deep){
	if(deep == 0)return ;
	auto next_ = pre_[deep][i][j];
	if(next_.first == i && next_.second == j)return;
	if(next_.first != i){
		as[0].push_back(i - next_.first); 
	}
	if(next_.second != j){
		as[1].push_back(j - next_.second);
	}
	get_ans(next_.first,next_.second,deep - 1);
}
int main()
{
	
	int n,m;
	cin>>n>>m;
	for(int i = 1;i <= n;i++){
		scanf("%d",&a[i]);
	}
	dp[0][0][0] = 1;
	pre_[0][0][0] = make_pair(0,0);
	for(int k = 1;k <= n;k++){
		for(int i = 0;i <= 100;i++){
			for(int j = 0;j <= 100;j++){
				if(i * j > 100)continue;
				dp[k][i][j] = dp[k - 1][i][j];
				pre_[k][i][j] = pre_[k - 1][i][j];
				if(j - a[k] >= 0){              // 更新时机不正确
					dp[k][i][j] = max(dp[k][i][j], dp[k - 1][i][j - a[k]]);
					pre_[k][i][j].first = i;            // pre_没有完全记录选择的状态,烧了一个k
					pre_[k][i][j].second = j - a[k];
				}
				if(i - a[k] >= 0){
					dp[k][i][j] = max(dp[k][i][j], dp[k - 1][i -a[k]][j]);
					pre_[k][i][j].first = i - a[k];
					pre_[k][i][j].second = j;
				}
			}
		}
	}
	
	while(m--){
		int x;
		scanf("%d",&x);
		bool ans = false;
		for(int i = 1;i * i <= x;i++){
			if((x % i))continue;
			int j = x / i;
			if(dp[n][i][j]){
				ans = true;
				cout<<"Yes"<<endl;
				as[0].clear();
				as[1].clear();
				get_ans(i,j,n);
				cout<<as[0].size()<<' '<<as[1].size()<<endl;;
				for(int ii = 0;ii < as[0].size();ii++)cout<<as[0][ii]<<' ';cout<<endl;
				for(int ii = 0;ii < as[1].size();ii++)cout<<as[1][ii]<<' ';cout<<endl;
				break;
			}
		}
		if(!ans){
			cout<<"No"<<endl;
		}
	}
    return 0;
}
```
### 正解

```cpp

/*================================================================
*   Copyright (C) 2025 Wang XinPeng. All rights reserved.
*
*   filename:     E.cpp
*   username:     skt1faker
*   create time:  19:11  2025.03.21
*   modify time:  19:11  2025.03.21
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
const int maxx = 1e2 + 10;
int dp[102][maxx][maxx];
int a[maxx];
pair<int,pair<int,int>>pre_[102][maxx][maxx];
vector<int>as[2];
void get_ans(int i,int j,int deep){
    if(deep == 0)return ;
    auto next_ = pre_[deep][i][j].second;
    if(next_.first == i && next_.second == j)return;
    if(next_.first != i){
        as[0].push_back(i - next_.first);
    }
    if(next_.second != j){
        as[1].push_back(j - next_.second);
    }
    get_ans(next_.first,next_.second, pre_[deep][i][j].first);
}
int main()
{

    int n,m;
    cin>>n>>m;
    for(int i = 1;i <= n;i++){
        scanf("%d",&a[i]);
    }
    dp[0][0][0] = 1;
    pre_[0][0][0] = make_pair(0,make_pair(0,0));
    for(int k = 1;k <= n;k++){
        for(int i = 0;i <= 100;i++){
            for(int j = 0;j <= 100;j++){
                if(i * j > 100)continue;
                if(dp[k - 1][i][j] == 1){
                    dp[k][i][j] = dp[k - 1][i][j];
                    pre_[k][i][j] = pre_[k - 1][i][j];
                }
                if(i - a[k] >= 0 && dp[k - 1][i - a[k]][j] == 1){ //> dp[k][i][j]{
                    dp[k][i][j] = max(dp[k][i][j], dp[k - 1][i -a[k]][j]);
					pre_[k][i][j].first = k - 1;
                    pre_[k][i][j].second.first = i - a[k];
                    pre_[k][i][j].second.second = j;
                }
                if(j - a[k] >= 0 && dp[k - 1][i][j - a[k]] == 1){ //> dp[k][i][j]){
                    dp[k][i][j] = max(dp[k][i][j], dp[k - 1][i][j - a[k]]);
					pre_[k][i][j].first = k - 1;
                    pre_[k][i][j].second.first = i;
                    pre_[k][i][j].second.second = j - a[k];
                }
            }
        }
    }

    while(m--){
        int x;
        scanf("%d",&x);
        bool ans = false;
        for(int i = 1;i * i <= x;i++){
            if((x % i))continue;
            int j = x / i;
            if(dp[n][i][j]){
                ans = true;
                cout<<"Yes"<<endl;
                as[0].clear();
                as[1].clear();
                get_ans(i,j,n);
                cout<<as[0].size()<<' '<<as[1].size()<<endl;;
                for(int ii = 0;ii < as[0].size();ii++)cout<<as[0][ii]<<' ';cout<<endl;
                for(int ii = 0;ii < as[1].size();ii++)cout<<as[1][ii]<<' ';cout<<endl;
                break;
            }
        }
        if(!ans){
            cout<<"No"<<endl;
        }
    }
    return 0;
}
```
