---
layout: post
title: TensorFlow【深度学习】逻辑分类（Logistic Classification）
categories: DeepLearning
---

- postname: TensorFlow【深度学习】逻辑分类（Logistic Classification）
- 2017-07-18 shine

# 线性分类器

## 建模方案:

![这里写图片描述](http://img.blog.csdn.net/20170718162704676?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 输入一个X
- 将x应用到一个线性函数中去
 - 生成多维矩阵
 - 将输入当做一个大的向量
 - 多维输入向量当作矩阵，用W表示权重
 - b表示偏项
 - 通过调整权重和偏重以获得最佳预测
- 输出y
 - 对每个输出类进行预测
 - Y是向量，表示每个标签的概率
 - 优秀的模型，预测值应该接近1
 - 一般得到的Y刚开始不是概率，而是一些具体值，所以需要转换
 - 使用Softmax回归模型：
 ![这里写图片描述](http://img.blog.csdn.net/20170718161126465?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 
## 关于softmax

- 代码实现：

```python
"""Softmax."""
scores = [3.0, 1.0, 0.2]
import numpy as np
def softmax(x):
    return np.exp(x) / np.sum(np.exp(x), axis=0)
print(softmax(scores))
# Plot softmax curves
import matplotlib.pyplot as plt
x = np.arange(-2.0, 6.0, 0.1)
scores = np.vstack([x, np.ones_like(x), 0.2 * np.ones_like(x)])
plt.plot(x, softmax(scores).T, linewidth=2)
plt.show()
```
 
- input的score差异越大，则输出的各项label概率差异越大，反之差异越小， 如输入[30, 10, 2]
- Softmax只关label之间的概率，不关心具体值
- 机器学习是一个让预测成功率升高的事情，因此是一个让score之间差异增大的过程

## 一次热编程

![这里写图片描述](http://img.blog.csdn.net/20170718162805430?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 正确预测结果应当是只有一个label成立，其他label不成立。这种情况下，预测概率最大的则是最可能的结果
- 在label很多的情况下效率不好，因为输出向量到处都是0，很稀疏，因此效率低
- 但是可以通过比对两个vector得知其与理想情况的差距
- 分类器输出：[0.7 0.2 0.1] <=> 与label对应的真实情况：[1 0 0]
- 需要注意的是： D(S, L) != D(L, S)

## 比对两个向量

![这里写图片描述](http://img.blog.csdn.net/20170718162924276?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

D(S, L) != D(L, S)

- 找到合适的W和b，使得S和L的距离D的平均值，在整个数据集n中最小。
- 最小化公式：

![这里写图片描述](http://img.blog.csdn.net/20170718164528376?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- D的平均值即是训练误差，求和和矩阵相乘有大量计算，数据量非常大

![这里写图片描述](http://img.blog.csdn.net/20170718164817997?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 两个参数的误差导致一个呈圆形的误差，所以我们要做的就是找到尽量靠近圆心的权重

## 数值优化

- 解决方案1： 求导

![这里写图片描述](http://img.blog.csdn.net/20170718165018488?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 修改参数，检查误差是否变大，往变小的趋势修改，直到抵达bottom。
- 图中weight是二维的，但事实上可能有非常多的weight

## 数值稳定性

- 量级相差太多的数运算会导致许多错误
- 举例说明:

```python
# Numerical stability
a = 1000000000
for i in xrange(1000000):
    a += 1e-6
print(a - 1000000000)
a = 1
for i in xrange(1000000):
    a += 1e-6
print a - 1
```

- 看起来输出是1， 但结果是接近0.95的数。这就是量化的误差，因此需要让前面提到的误差函数中的数据不要太大或者太小

## 归一化输入和初始权重

- 理想目标： 均值为0

![这里写图片描述](http://img.blog.csdn.net/20170718174840077?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 方差处处相等 
- 通过优化可以很好的找到解决方案

![这里写图片描述](http://img.blog.csdn.net/20170718175140324?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 举例： 图像归一化

```
R = (R - 128) / 128
G = (G - 128) / 128
B = (B - 128) / 128
```

- 好的初始化有助于好的权重与偏差

![这里写图片描述](http://img.blog.csdn.net/20170718175431752?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 用均值为0，标准偏差的高斯分布产生随机的数据填充W矩阵 

![这里写图片描述](http://img.blog.csdn.net/20170718175521434?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 高斯分布模型也决定了初始输出(softmax输出)的概率分布
- 高斯分布的sigma越小，说明预测越不确定，sigma的取值有较大影响
- 需要选一个较小的sigma，让sigma变小到合适的值，使得预测更确定。
- 优化过程:

![这里写图片描述](http://img.blog.csdn.net/20170718175659451?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 调整W和b，使得Train loss最小.