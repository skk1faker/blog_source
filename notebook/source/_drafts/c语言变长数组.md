---
title: c语言变长数组
tags:
---
# c语言变长数组的实现
变长数组指的是数组的长度可以任意改变，而不是可以定义任意长度的数组  

这里给出他的写法  

```cpp

#include<string.h>
#include<stdio.h>

struct ss
{
  int n;      // 变长数组的长度
  char a[0];  // 不可写为char *a, 否则会报错
  // 注意这里的a[0]不占内存，但是*a占内存
  // 因为sizeof(a) == 0, 所以需要一个n来给出当前变长数组的长度。
};

int main()
{
  
  int n;
  n = 10;
  int saf[n];           // 这不是一个变长数组，因为一旦定义之后，就不能改变大小
  printf("%ld",sizeof(saf));


  // 定义变长数组大小为n
  struct ss *b = (struct ss *)malloc(sizeof(struct ss) + n * sizeof(char)); 
  b->n = n;

  // 现在改变数组的长度
  n =  100;
  b = (struct ss *)malloc(sizeof(struct ss) + n * sizeof(char));
  b->n = n;

  char c[10] = "wfasfa";
  memcpy(b->a,c,sizeof(c));
  printf("%s\n",b->a);

  // 变长数组的大小不能使用sizeof得到
  printf("%ld\n",sizeof(b->a));
  struct ss *d = (struct ss *)malloc(sizeof(struct ss) + 10 * sizeof(char ));

  return 0;
}


```
参考自：  
https://stackoverflow.com/questions/3607859/meaning-of-char-arr0  
https://blog.csdn.net/hyqsong/article/details/50863746  
https://blog.csdn.net/ligeforrent/article/details/48639277  

