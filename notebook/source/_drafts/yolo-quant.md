---
title: yolo_quant
tags:

- 毕业
---



## 试验环境
```
alias quant_="cd /data_160TB/2018/songzhengxi/haha/test/dt_mqbench_test;conda activate o"
alias yolo_="cd /data_160TB/2018/songzhengxi/haha/test/yolov5_example;conda activate o"
```


## yolo
## quant


## 实验结果 

### 代码
1. 给出训练曲线         -- 0%
2. 给出量化位数情况     -- 0%
3. 给出量化模型保存方案 -- 0% 
4. 统计模型大小         -- 0%

### 实验

1. 与其他量化方法进行对比
2. 本方法做一个消融实验
3. 


### 参考资料

https://datawhalechina.github.io/dive-into-cv-pytorch/#/chapter03_object_detection_introduction/3_4
https://start.oneflow.org/oneflow-yolo-doc/tutorials/01_chapter/yolov5_network_structure_analysis.html

### 目标检测基本流程

1. 训练过程

将图片分成多个锚框，锚框在图片中的比例 + 不同的长宽比 + 不同中心点的位置构成了生成锚框的三个要素。如果将目标检测比作做选择题，那么锚框就是选择题的选项，他是一个粗略的备选答案。


1. 当前训练中exp35最为优质
