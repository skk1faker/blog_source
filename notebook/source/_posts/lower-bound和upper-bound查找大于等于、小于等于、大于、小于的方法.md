---
title: lower_bound和upper_bound查找大于等于、小于等于、大于、小于的方法
date: 2023-12-25 17:01:26
tags: c++
---

https://www.zhihu.com/question/63931004

常规情况下`lower_bound`查找的是大于等于，`upper_bound`查找的是大于，但是通过`great<int>()`可以将`lower_bound()`变为查找小于等于的函数，`upper_bound()`变为查找小于的函数
```cpp
#include <bits/stdc++.h>
using namespace std;

int main()
{
	int a[3] = { 1 , 10 , 20 };
	sort( a , a + 3 , greater< int >() );
	//从大到小排序 
	auto t = lower_bound( a , a + 3 , 5 , greater<int>() );
	// 小于等于5中最大的 
	cout << *t << endl ;
	auto p = upper_bound( a , a + 3 , 10 , greater<int>() );
	// 小与 10 中最大的
	cout << *p << endl ;
	return 0; 
}

作者：PHarr
链接：https://www.zhihu.com/question/63931004/answer/828795483
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

方法2：

`auto iter = lower_bound`找到大于等于后，`iter--`的值便是小于，`auto iter = upper_bound`找到大于后，`iter--`的值便是小于等于.

```cpp

#include <iostream>
#include <map>

int main() {
    std::map<int, std::string> myMap;
    myMap[1] = "one";
    myMap[3] = "three";
    myMap[5] = "five";
    myMap[7] = "seven";

    int a = 6;
    auto it = myMap.upper_bound(a);

    if (it == myMap.begin()) {
        std::cout << "No element is less than " << a << std::endl;
    } else {
        --it;
        std::cout << "The largest key less than " << a << " is " << it->first << " with value " << it->second << std::endl;
    }

    return 0;
}

```

