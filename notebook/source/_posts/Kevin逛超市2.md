---
title: Kevin逛超市2
date: 2023-12-08 11:24:38
tags: 排序
---
链接：https://ac.nowcoder.com/acm/contest/70845/D
来源：牛客网

# 题意：

现有两种券，一种是折扣券，他可以将物品打折，如折扣券的折扣为x，物品i价格为p[i]，那么用折扣券购买时会将价钱变为$p[i] \times x%$，还有一种券是立减券，立减券减少金额为y，商品使用立券购买后价格减少y，但商品价格最小为0。

现在有a张折扣券，b张立减劵，一个物品只能用一张券，现在有n个物品，买光所有物品时，问怎样使用才能将花费降到最低

# 题解

可以明确的知道，无论那种券，价格越高的商品使用券会越合适（如果商品价格太小，那么立减券可能无法发挥全部作用，折扣券必然是商品价格越大，减少金额越多），所以价格前(a+b)大的商品使用优惠券才能满足题意。

但是如何进行合适的分配呢。现考虑两个商品i,j，他们的价格为p[i],p[j]，如果i更适合使用折扣券，j更适合立减券，那么有如下表达式
$$
\frac{p[i](100 - x)}{100} + min(y,p[j]) > \frac{p[j](100 - x)}{100} + min(y,p[j])
$$
利用如上表达式，对价格前a+b大的商品进行排序，得到的序列会在进行合理分配后有如下特点。  
- 这些商品中使用折扣券的聚集在一起，使用立减券的积聚在一起  
- 但是分界点未知。

知道以上特点，我们可以讨论下分界点，假设排序完之后使用折扣券的在序列前面，使用立减券的在序列后面，那么当  
- a + b <= n  
这种情况直接前a个就是折扣券，后b个是立减券  
- a + b > n  
这种情况使用多少a，多少b是未知的，所以需要暴力确定分界点，先假设使用券的情况为尽量将折扣券用完(可能用不完)，此时立减券还剩很多，然后逐步将一些折扣券替换为立减券，在替换过程中寻找花费最小的情况。
    

```cpp
#include<bits/stdc++.h>
using namespace std;
#define ll long long
const int maxx = (int)2e5 + 10;
int x,y;
struct pact
{
  int val,has_used;

}p[maxx];
bool cmp1(pact v1,pact v2)      // 此函数返回真时，元素v1在前放，当前判断中v1使用折扣券合适时为真，所以最后序列中前半部分使用折扣券。
{
  return (100 - x) * v1.val + min(v2.val ,y) * 100 > (100 - x) * v2.val + min(v1.val ,y) * 100;
}
bool cmp2(pact v1,pact v2){     // 此表达式为返回真时，v1在前面，当前判断中只有v1大时返回真，所以排序结果为元素较大在前
  return v1.val > v2.val;
}

int main()
{
  int T;
  cin>>T;
  int n,a,b;
  while(T--){
    cin>>n>>a>>b>>x>>y;
    //cin>>n>>x>>y;
    //a = 1;
    //b = 1;
    for(int i = 0;i < n;i++){
      scanf("%d",&p[i].val);
      p[i].has_used = 0;
    }
    sort(p,p+n,cmp2);
    int length = min(n,a+b);
    sort(p,p+length,cmp1);      // 寻找最大值
    double sum = 0;
    double minus_sum = 0;
    int aa = a;
    int bb = b;
    for(int i = 0;i < length && aa > 0;i++){
      aa--;
      p[i].has_used = 1;
      minus_sum += 1.0 * p[i].val * (100 - x) / 100;
      //cout<<"val = "<<p[i].val<<endl;
    }
    for(int i = 0;i < length && bb > 0;i++){
      if(p[i].has_used == 1)continue;
      else bb--;
      minus_sum += min(p[i].val, y);
    }
    for(int i =0 ;i < n;i++)sum += p[i].val;
    if(a + b > n){      // 此时需要确定优惠券用量，直接暴力求解
      for(int i = length - 1;i >= 0 && bb > 0;i--){
        if(p[i].has_used == 1){
          double new_minus_sum = max(minus_sum - 1.0 * p[i].val * (100 - x) / 100 + min(p[i].val,y) ,minus_sum);
          if(fabs(new_minus_sum - minus_sum) > 1e-5){
            bb--;
            minus_sum = new_minus_sum;
          }
        }
      }
    }
    //cout<<sum - minus_sum<<endl;
    printf("%.5lf\n",sum - minus_sum);
  }
  return 0;
}
```
