---
layout: post
title: TensorFlow【深度学习】安装Tensorflow与测试
categories: DeepLearning
---

> 推荐使用 清华大学镜像与资源包，(请点我参考)[https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/]

## 安装过程

> 当前使用的安装方式是 anaconda, 当然也可以使用native pip方法, 参见官网: https://www.tensorflow.org/install/install_windows

> anaconda : https://www.continuum.io/downloads

![安装anaconda](http://img.blog.csdn.net/20170723132209316?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 新建一个conda叫做tensorflow的环境

> 如果command line提示conda不识别,需要重启 comand line


```
conda create -n tensorflow python=3.5
```

![初始化流程](http://img.blog.csdn.net/20170723135748679?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


- 激活conda环境

```
active tensorflow
```

![激活tensorflow](http://img.blog.csdn.net/20170723143224186?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 测试初始化是否完成

> py3.5需要注意的是: 在引入tensorflow时, 需要激活tensorflow

![这里写图片描述](http://img.blog.csdn.net/20170723145340031?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


```python
>>> import tensorflow as tf
>>> hello = tf.constant('Hello, TensorFlow!')
>>> sess = tf.Session()
>>> print(sess.run(hello))
```


> 至此, 安装完毕, 需要注意的是: 需要在tensorflow的环境下使用python3.5调用tensorflow