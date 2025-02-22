---
title: cf2038E-Barrels
tags:

- 模拟
---


## 题意

有n个水缸，每个水缸之间有n - 1个联通管，初始时加入一些水，现在向水缸里加入一些黏土，使得水面升高。如果黏土高度高过连通管的高度，那么连通管将被堵死不能使用，问经过一些列操作后，第一个管中最多会有多少水

## 题解

主要通过模拟得到

```cpp

/*================================================================
*   Copyright (C) 2024 Wang XinPeng. All rights reserved.
*   
*   filename:     CF2038E.cpp
*   username:     skt1faker
*   create time:  10:03  2024.12.29
*   modify time:  10:03  2024.12.29
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
const int maxx = 2e5 + 10;
int h[maxx], v[maxx], n;
double sum[maxx];
struct bar{
	ll l,r;
	double h;	// 桶内联通器
	double h_pre;
}s[maxx];
int front,rear;


int main()
{
	cin>>n;
	sum[0] = 0;
	for(int i =0;i < n;i++){
		scanf("%d",&v[i]);
		sum[i + 1] = sum[i] + v[i];
	}
	for(int i =0;i < n - 1;i++){
		scanf("%d",&h[i]);
	}
	h[n - 1] = 1e9;
	double v_hsum = 0, v_now = 0;
	front = 0,rear = -1;
	int now_posi = n - 1;
	do{
		// 栈中第一个元素为即将合并的元素
		while(now_posi != -1  && (rear - front < 1 || h[now_posi] < s[rear].h )){
			v_now += v[now_posi];
			int pre_height = 0;
			if(now_posi == 0){
				pre_height = 0;
			}
			else {
				pre_height = h[now_posi - 1];
			}
			s[++rear] = bar{now_posi,now_posi,(double)h[now_posi],(double)pre_height};
			v_hsum += pre_height;
			now_posi--;
			if(rear - front >= 1 && s[rear].h > s[rear - 1].h){
				break;
			}
		}
		// 第一个元素一定为下一状态的高度
		double v_next = 0;
		if(rear - front >= 1){
			int mode = 0;
			v_next = v_hsum + (s[rear].h - s[rear].h_pre) *(s[rear].r - s[rear].l + 1);
			double v_add_value = v_now + h[s[front].r - 1];
			if(v_next <= v_add_value){		// 此时可进入下一状态
				v_hsum -= (s[rear - 1].r - s[rear - 1].l + 1) * (s[rear - 1].h_pre) + (s[rear].r - s[rear].l + 1) * (s[rear].h_pre);
				s[rear - 1].l = s[rear].l;
				s[rear - 1].h_pre = s[rear].h_pre;
				rear--;
				v_hsum += s[rear].h_pre * (s[rear].r - s[rear].l + 1);
			}
			else if(v_next > v_add_value ){		// 此时这个管子已经没有作用了
				if(s[front].l != s[front].r){
					double length = s[front].r - s[front].l + 1;
					v_now -= min((v_add_value - (sum[s[front].l] - sum[s[rear].l])) / length , s[front].h_pre) - h[s[front].r - 1];
				}
				v_hsum -= s[front].h_pre;
				s[front].r--;
			}
			if(s[front].l > s[front].r){
				s[front+1].h = s[front].h;
				front++;
			}
		}
		else if(s[front].l != s[front].r){	// 最后一个合并的管子中管子数量大于一个时才能使用如下的算法
			// 元素不足两个，直接将剩余元素通过删管子删除
			double v_temp = v_now + h[s[front].r - 1];
			double length = s[front].r - s[front].l + 1;
			v_now = v_temp * (length - 1) / length;
			s[front].r--;
		}
	}while(!(rear == front && s[front].l == s[front].r && s[front].l == 0));
	printf("%0.20f\n",v_now);
	return 0;
}



/*
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;

#define N 200000

int i,j,k,n,m,t,l,r,L=N+1,R=N,h[N+50],a[N+50],q[N+N+50];
double tot,h1,h2;

int main(){
	ios::sync_with_stdio(0); cin.tie(0);
	scanf("%d",&n);
	for(i=1;i<=n;i++)scanf("%d",&a[i]);
	for(i=2;i<=n;i++)scanf("%d",&h[i]);
	h[1]=1e9;
	l=r=n; tot=a[n];
	for(r=n;r>=2;r--)while(1){
		while(L<=R&&q[R]>r)R--;
		h1=(tot+h[r])/(r-l+1);
		if(L<=R){
			h2=h[q[R]];
			if(h1<h2){tot-=(h2-h[r]); break;}
		}
		if(h1<h[l]){tot-=(h1-h[r]); break;}
		while(L<=R&&h[q[L]]<=h[l])L++;
		q[--L]=l; l--; tot+=a[l];
	}
	printf("%.20lf",tot);
}
*/

```
