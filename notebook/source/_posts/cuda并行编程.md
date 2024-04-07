---
title: cuda并行编程
date: 2023-12-19 20:19:27
tags:
- cuda并行编程
- 开发
---

# 参考资料：
[](https://godweiyang.com/2021/01/25/cuda-reading/)
[](https://zhuanlan.zhihu.com/p/34587739)



## 环境配置

主要是从https://developer.nvidia.com/cuda-downloads网站上下载一个`.run`的安装包，如果之前有安装过显卡驱动的话需要现将显卡驱动卸载,
```bash
sudo apt-get --purge remove nvidia*
sudo apt-get autoremove         # 将一些相关的库删除，不然之后在安装cuda驱动的时候会出现显卡驱动还存在的问题。
```

执行完之后就是将驱动安装`sudo sh cuda-xxxx-.run`，然后`accpet`之后按照需要安装。

安装结束之后记得设置环境变量，安装位置在`/usr/local/cuda`，将环境变量中添加一些，即在/etc/profile中添加如下内容

```bash
export LD_LIBRARY_PATH="/usr/local/cuda/lib64:$LD_LIBRARY_PATH"
export C_INCLUDE_PATH="/usr/local/cuda/include:$C_INCLUDE_PATH"
export CPLUS_INCLUDE_PATH="/usr/local/cuda/include:$CPLUS_INCLUDE_PATH"
```

## host内存和device内存的分配

host指的是cpu，device指的是gpu，两个单位分配的内存不在一个位置，所以就有一个分配的问题，cuda提供了一个管理内存的api  
1. `cudaError_t cudaMallocManaged(void **devPtr, size_t size, unsigned int flag=0);`用于分配内存  
2. `cudaDeviceSynchronize()`函数保证device和host同步，这样后面才可以正确访问kernel计算的结果  
3. `cudaFree()`用于释放内存。
4. `cudaMemcpy(result_ptr, te.data_ptr(), sizeof(float) * te.numel(),cudaMemcpyDeviceToHost);`  注意cudaMemcpyDeviceToHost表示device上的内存到cpu上，te.data_ptr()是在gpu上的地址，result_ptr表示在cpu上的内存地址。同样cudaMemcpyHostToDevice表示将cpu上的内存数据复制到gpu上

==，可以自动将host上的数据传输到device上。==


## cuda编程和torch编程

1. 在cuda中想修改torch可以先将torch的地址搞到，将其视为一维数组，然后对这个地址进行操作，主要难点在与使用什么api
`tensor.data_ptr<float>()`

2. cuda多线程编程中锁的问题
https://blog.csdn.net/Limiao_123/article/details/111197531

## 在torch c++中需要加入的头文件如下：
```
#include<torch/torch.h>
#include<torch/extension.h>     // 这个不加不行，在使用pybind11生成链接库时，如果一些函数中参数为tensor，那么在调用时会出现TypeError: torch2numpy(): incompatible function arguments. The following argument types are supporte，这个错误
 
```


## python中如何调用c++中的函数
https://zhuanlan.zhihu.com/p/362834539

在c++模块中的最后一部分一定要加上如下部分，这是是的python和c++之间产生联系的一部分代码
```
PYBIND11_MODULE(example, m) {
    m.doc() = "pybind11 example plugin"; // optional module docstring

    m.def("add", &add, "A function which adds two numbers");
  
    m.def("inadd",&inadd,"cin and count");
}

最重要的就是这个python模块绑定

example：模型名，切记不需要引号

m：可以理解成模块对象把

m.doc()：help说明

m.def：用来注册函数和python打通界限
```

## c++中使用pybind11修改numpy

https://blog.csdn.net/howard789/article/details/125128283
https://cloud.tencent.com/developer/ask/sof/1032057

主要还是将numpy转换为`float *`或者`double *`，难点在于api是什么
```
pybind11::array_t<double> narray;
auto buf = narray.request();
float *data = (float *)buf.ptr; // static_cast<float *>buf.ptr
```
上述过程用于获取numpy中存储数据的地址

```
 int N = array.shape()[0], M = array.shape()[1];
```
上述过程用于获取numpy中数据的维度信息。

numpy在c++中的一些函数：
1. pybind11::array_t<float>.size()      // 元素数量有多少
2. pybind11::array_t<float>.shape()     // 每个维度大小是什么
3. pybind11::array_t<float>.ndim()      // 有几个维度

## c++和python联动

https://blog.csdn.net/weixin_44966641/article/details/125377080#t23

主要使用一个pybind11，通过生成库进行处理。编译命令如下
```bash
g++ -O3 -shared -std=c++11 -fPIC `python3 -m pybind11 --includes` test.cpp -o example`python3-config --extension-suffix`
```
其中`python3 -m pybind11 --includes`表示的是使用pybind11时需要include的对象，python3-config --extension-suffix表示最终的动态链接库的后缀是什么。
下面是例子
```cpp
#include <pybind11/pybind11.h>
#include <pybind11/stl.h>
#include <pybind11/numpy.h>



namespace py = pybind11;
int add_func(int i, int j) {
    return i + j;
}

void print_list(py::list my_list) {
   for (auto item : my_list)
       py::print(item);
}

std::vector<int> print_list2(std::vector<int> & my_list) {
    auto x = std::vector<int>();
    for (auto item : my_list){
        x.push_back(item + 233);
    }
    return x;
}

py::array_t<double> add_arrays(py::array_t<double> input1, py::array_t<double> input2) {
    py::buffer_info buf1 = input1.request(), buf2 = input2.request();

    if (buf1.ndim != 1 || buf2.ndim != 1)
        throw std::runtime_error("Number of dimensions must be one");

    if (buf1.size != buf2.size)
        throw std::runtime_error("Input shapes must match");

    /* No pointer is passed, so NumPy will allocate the buffer */
    auto result = py::array_t<double>(buf1.size);

    py::buffer_info buf3 = result.request();

    double *ptr1 = (double *) buf1.ptr,
           *ptr2 = (double *) buf2.ptr,
           *ptr3 = (double *) buf3.ptr;

    for (size_t idx = 0; idx < buf1.shape[0]; idx++)
        ptr3[idx] = ptr1[idx] + ptr2[idx];

    return result;
}

PYBIND11_MODULE(example, m) {
	m.doc() = "pybind11 example plugin";  //可选，说明这个模块是做什么的
    m.def("add_func", &add_func, "A function which adds two numbers");
    m.attr("the_answer") = 23333;
    py::object world = py::cast("World");
    m.attr("what") = world;
    m.def("print_list", &print_list, "function to print list", py::arg("my_list"));
    m.def("print_list2", &print_list2, "help message", py::arg("my_list2"));
    m.def("add_arrays", &add_arrays, "Add two NumPy arrays");
}
```
这里的PYBIND11_MODULE中example表示之后包的名称，编译之后的库名字也应该是这个。m.def用于表示模块中的函数有哪些。

```bash
#指定编译器
# CC = nvcc
CC = g++
#指定编译参数
# CFLAGS = -shared -Xcompiler -fPIC -std=c++17 -g -D_GLIBCXX_USE_CXX11_ABI=0 
CFLAGS = -shared -fPIC -std=c++17 -g -D_GLIBCXX_USE_CXX11_ABI=0 
 
#指定头文件路径，此处用于指定非标准库的头文件路径
INC = -I./ -I /home/skt1faker/anaconda3/envs/homework/include/python3.9 -I/home/skt1faker/anaconda3/envs/homework/lib/python3.9/site-packages/pybind11/include
#指定要链接的动态库或静态库 -L是指定动态库路径，非标准的动态库需要此参数
LIBS =  -L/home/skt1faker/anaconda3/envs/homework/lib -lpython3.9  


#把上面放一起
CXXFLAGS = ${CFLAGS} ${INC} ${LIBS}
 
#指定生成可执行文件名称
EXEC = example.cpython-39-x86_64-linux-gnu.so	# 这个后缀是python3-config_--extension-suffix得到的
 
#指定由哪些文件来生成可执行文件
# COBJS = mpi.cpp mpi_cuda.cu
COBJS = test.cpp 
 
#make执行动作
all:
	${CC} -o ${EXEC} ${COBJS} ${CXXFLAGS}
 
#make clean动作
clean:
	rm -rf ${OBJS}
 
# nvcc -O3 -shared -std=c++11 -Xcompiler -fPIC `python3 -m pybind11 --includes` test.cpp -o example`python3-config --extension-suffix`

```
```python
from ctypes import *

import numpy as np
import example as e
# result = example.add(3, 4)
# print(result)
a = np.ones(3)
b = np.ones(3)
c = e.add_arrays(a,b)
# e.add_func(1,2)
list_a = [1,2,3]
e.print_list(list_a);
print(c)


```

## 如何查看链接库中的符号
可以使用`nm -C 动态链接库 |grep 要查找的函数名`来进行查看。
## 生成的动态链接库怎么调试

https://www.cnblogs.com/lidabo/p/17076870.html
使用gdb可以调试，`gdb --args python file.py`

之后在动态链接库的源文件上打断点。

后面的调试就和以前一样了

## .cu文件如何调试

https://zhuanlan.zhihu.com/p/386527323

使用cuda-gdb，可以调试.cu文件，但是在编译的时候需要加上-G选项，否则编译结束之后无法出现调试信息。

`nvcc -g -G source.cpp cuda.cu -o haha`

调试的时候使用命令

`cuda-deb --args python test.py`

下面是一些调试命令：
1. 查看cuda线程
`info cuda threads`
2. 保存断点
`save breakpoints b.cfg`
3. 切换cuda线程
```bash
(cuda-gdb) cuda device 0 sm 1 warp 2 lane 3
[Switching focus to CUDA kernel 1, grid 2, block (8,0,0), thread
(67,0,0), device 0, sm 1, warp 2, lane 3]
374 int totalThreads = gridDim.x * blockDim.x;

(cuda-gdb) cuda thread (15)
[Switching focus to CUDA kernel 1, grid 2, block (8,0,0), thread
(15,0,0), device 0, sm 1, warp 0, lane 15]
374 int totalThreads = gridDim.x * blockDim.x;

(cuda-gdb) cuda block 1 thread 3
[Switching focus to CUDA kernel 1, grid 2, block (1,0,0), thread (3,0,0), 
device 0, sm 3, warp 0, lane 3]
374 int totalThreads = gridDim.x * blockDim.
```

## cuda编程过程中出现内存错误时应如何排查

`compute-sanitizer python test.py`
这个代码输入之后，会出现很多很多输出，这个时候就要在输出中搜索相关cuda文件的名字，来定位问题出现的位置。

## blockIdx,gridDim,threadIdx,blockDim的关系
[参考](https://blog.csdn.net/qq_43715171/article/details/121794135)

gridDim描述了线程格的维度情况，blockIdx描述了线程块在线程格中坐标，blockDim描述了线程块的维度情况，threadIdx描述了线程在线程块中的坐标。


blockDim和gridDim

可以把线程格和线程块都看作一个三维的矩阵。这里假设线程格是一个3*4*5的三维矩阵， 线程块是一个4*5*6的三维矩阵。

1. gridDim

gridDim.x、gridDim.y、gridDim.z分别表示线程格各个维度的大小，所以有

gridDim.x=3
gridDim.y=4
gridDim.z=5

2. blockDim
blockDim.x、blockDim.y、blockDim.z分别表示线程块中各个维度的大小，所以有

blockDim.x=4
blockDim.y=5
blockDim.z=6

3. blockIdx

blockIdx.x、blockIdx.y、blockIdx.z分别表示当前线程块所处的线程格的坐标位置

4. threadIdx

threadIdx.x、threadIdx.y、threadIdx.z分别表示当前线程所处的线程块的坐标位置

通过 blockIdx.x、blockIdx.y、blockIdx.z、threadIdx.x、threadIdx.y、threadIdx.z就可以完全定位一个线程的坐标位置了。

线程格里面总的线程个数N即可通过下面的公式算出

`N = gridDim.x*gridDim.y*gridDim.z*blockDim.x*blockDim.y*blockDim.z`


三、举例

将所有的线程排成一个序列，序列号为0,1,2,…,N，如何找到当前的序列号？

先找到当前线程位于线程格中的哪一个线程块blockId

`blockId = blockIdx.x + blockIdx.y*gridDim.x + blockIdx.z*gridDim.x*gridDim.y;`

找到当前线程位于线程块中的哪一个线程threadId

`threadId = threadIdx.x + threadIdx.y*blockDim.x + threadIdx.z*blockDim.x*blockDim.y;`

计算一个线程块中一共有多少个线程M

`M = blockDim.x*blockDim.y*blockDim.z`

求得当前的线程序列号idx

`idx = threadId + M*blockId;`

## 什么是warp
https://zhuanlan.zhihu.com/p/123170285

每一个warp含有32个线程，一个warp中的所有线程在一个block中，当一个block中的线程数量不是warp中的正数倍时，gpu会给与多余的线程分配，但是多出的线程都是inactive状态，所以block中的线程数量最好为32的倍数。

warp中的线程拥有**同步执行规则**，即同步执行一个函数，如果一个warp中的线程由锁来实现资源互斥访问的话，那必然会导致死锁的问题，因为一个线程获取了锁，其他线程没有获取，获取锁的线程会向下执行，但是warp中的线程要同步执行，所以即便是warp中一个线程获取了锁之后，其他线程没有获取锁也无法继续执行。

例如
```c++
struct Lock {
   
    int *mutex;
    Lock(void) {
   
        int state = 0;
        cudaMalloc((void **) &mutex, sizeof(int));
        cudaMemcpy(mutex, &state, sizeof(int), cudaMemcpyHostToDevice);
    }
    ~Lock(void) {
   
        cudaFree(mutex);
    }
    __device__ void lock(void) {
   
        while (atomicCAS(mutex, 0, 1) != 0);
    }
    __device__ void unlock(void) {
   
        atomicExch(mutex, 0);
    }
};
.......

__global__ void theKernel(Lock myLock) {
   
	myLock.lock();
	Do_your_job();
	myLock.unlock();
}

```
在调用theKernel时，theKernel<<<1,128>>>(lock)会发生死锁，但是theKernel<<<128,1>>>(lock)就不会发生死锁
https://blog.csdn.net/Limiao_123/article/details/111197531

解决这种死锁的方法就是用条件语句，warp各个线程虽然是同步的，但是不同线程中根据不同的条件语句执行的也有所不同，一种比较脑瘫的方法是这样的：
```
__global__ void haha(int *lock,int n){
    int id = blockIdx.x * blockDim.x + threadIdx.x;
    if(id > n)return ;
    switch(id){
        case 0:{
            while(atomicExch(lock,0) == 0);
            // 进行获取锁之后的操作
            *lock = 1;
        }break;
        case 1:{
            while(atomicExch(lock,0) == 0);
            // 进行获取锁之后的操作
            *lock = 1;
        }break;
        case 2:{
            while(atomicExch(lock,0) == 0);
            // 进行获取锁之后的操作
            *lock = 1;
        }break;
        case 3:{
            while(atomicExch(lock,0) == 0);
            // 进行获取锁之后的操作
            *lock = 1;
        }break;
          .
          .
          .
    }
}
```

也就是引入分支结构就可以解决这个问题，将这个分支结构进行浅浅的改动

```
__global__ void haha(int *lock,int n){
    int id = blockIdx.x * blockDim.x + threadIdx.x;
    if(id > n)return ;
    while(ture){
        if(atomicExch(lock,0) != 0){
            // 进行获取锁之后的操作
            *lock = 1;
            break;
        }
    }
}
```

可以看到本文使用了自旋锁，lock = 1 表示锁被释放，lock = 0表示锁被占用。`atomicExch(unsigned int *address,unsigned int val)`表示将address处的值设置为val，并返回address处原先的值，两个操作为一个原子操作。锁的原理是先检查锁是否被占用，未被占用时获取锁。本文实现的自旋锁刚好反过来，先上锁，然后在查看原先锁的状态，如果原先锁是被释放的，那么就上锁成功。所以自旋锁的写法为 while(atomicExch(lock,0) == 0);

cuda中还有其他原子操作，这里给出地址。
https://blog.csdn.net/Bruce_0712/article/details/63251532

## 如何设置grid与block的维度
https://blog.csdn.net/yangdashi888/article/details/78183464
https://blog.csdn.net/quicmous/article/details/115250605

GPU 由多个 SM 处理器构成，一个 SM 处理器包含 8 个 SP 核。一个 SM 处理器可同时处理 32 个线程，实际上就是同一套指令在每个 SP 核上重复 4 次， 这样提交一次任务，8 个SP 核同时就能处理 32 个线程。
用于查看显卡中sm的数量
```cpp
cudaDeviceProp prop;
cudaGetDeviceProperties(&prop, 0); //0可以被替换成你的其他卡的实际编号
int result = prop.multiProcessorCount;  // sm的数量
```

sp的数量在官网上能查到

在设置的过程中grid中block的数量应为sm的正数倍数，这样才能使得任务均匀的分配在用于计算的sm上，block中的线程数量应为sp * 4的整数倍。

## numpy.array和torch.tensor维度为[a,b,c],对于坐标为[x,y,z]的元素，其在numpy.array和torch.tensor转化为一维指针之后的对应位置如下：

int posi = z + y * c + x * b * c;

## numpy与torch操作中python与c++的api对应
1. 与torch相对应的操作
https://blog.csdn.net/weixin_45209433/article/details/103495558

2. torch.tensor中的切片操作
https://blog.csdn.net/Flag_ing/article/details/109628297


