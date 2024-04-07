---
title: matplotlib使用中文字体
date: 2024-02-10 16:07:27
tags: 绘图
---

[参考](https://zhuanlan.zhihu.com/p/32981102)
[SimHei下载地址](https://github.com/StellarCN/scp_zh/blob/master/fonts/SimHei.ttf)
[代码参考](https://blog.csdn.net/OldDriver1995/article/details/114827088)
[清除缓存参考](https://zhuanlan.zhihu.com/p/32981102)

首先下载simHei字体，将这个字体放在`/home/skt1faker/anaconda3/envs/homework/lib/python3.9/site-packages/matplotlib/mpl-data/fonts/ttf`下，路径的位置是`python -c "import matplotlib; print(matplotlib.matplotlib_fname())"`，这个是个文件位置，上一级就快到我们要找的路径了。

之后代码中加入
```python
import matplotlib as mpl  
mpl.rcParams['axes.unicode_minus'] = False
mpl.rcParams['font.family'] = 'SimHei'

from matplotlib.font_manager import _rebuild
_rebuild()

```
注意`from matplotlib.font_manager import _rebuild`可能会报错，这个时候就要手动进行内存的清除，使用命令`pyhton -c "import matplotlib;print(matplotlib.get_cachedir())"`即可得到缓存位置`/home/skt1faker/.cache/matplotlib/* `之后运行`rm -rf /home/skt1faker/.cache/matplotlib/* `重新运行程序即可使用当前SinHei字体


## latex显示

需要进行如下配置来晚上环境
[环境配置](https://stackoverflow.com/questions/58121461/runtimeerror-failed-to-process-string-with-tex-because-latex-could-not-be-found)
[环境配置B](https://stackoverflow.com/questions/31214214/matplotlib-error-latex-was-not-able-to-process-the-following-string-lp)
```bash
sudo apt install texlive texlive-latex-extra texlive-fonts-recommended dvipng
pip install latex
```

## latex与中文共同显示

最后使用matplotlib中使用latex直接使用如下代码，如果需要中文显示需要设置字体
```python
import numpy as np
import matplotlib.pyplot as plt
import matplotlib as mpl

def f5():
   # 设置matplotlib库字体的非衬线字体为黑体
   plt.rcParams["font.sans-serif"]=["SimSun"]
   # 设置matplotlib库字体族为非衬线字体
   plt.rcParams["font.family"]="sans-serif"
   x = [100, 200, 300, 400, 500]
   y = [100, 300, 500, 700, 900]
   fig, ax = plt.subplots()
   ax.plot(x, y)
   x_labels = [r'第五重要特征', r'第四重要特征', r'第三重要特征', r'第二重要特征', r'第一重要特征']
   # x_labels = ['1', '2', '3', '4', '1']
   y_labels = [r'$\lambda_{min}$', '', '', '', r'$\lambda_{max}$']
   ax.set_xticks(x)
   ax.set_yticks(y)
   ax.set_xticklabels(x_labels)
   ax.set_yticklabels(y_labels)
   ax.scatter(x,y,color='red',marker='o')
   # plt.xticks(rotation=45)
   plt.savefig('hhh.pdf',dpi=200,bbox_inches='tight', pad_inches=0.1)
   plt.close()
   # plt.show()

f5() 

```
