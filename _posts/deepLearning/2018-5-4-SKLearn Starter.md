---
layout: post
title:SKlearn-Starter
categories: DeepLearning
---

## 机器学习-问题设置

通常，学习问题会考虑一组n 个数据样本，然后尝试预测未知数据的属性。如果每个样本不止一个数字，例如多维条目（也称为多元数据），则称其具有多个属性或特征。

我们可以将学习分为几大类：

### 监督式学习

其中数据包含带有我们想要预测的其他属性。这个问题可以是：

- 分类：样本属于两个或更多类别，我们希望从已标记的数据中学习如何预测未标记数据的类别。分类问题的一个例子是手写数字识别的例子，其中的目标是将每个输入向量分配给有限数量的离散类别之一。另一种考虑分类的方法是作为一种离散的（而不是连续的）监督式学习形式，其中一个分类的数量有限，对于提供的n个样本中的每一个，一个是试图用正确的分类或类别来标记它们。

- 回归：如果期望的输出包含一个或多个连续变量，则该任务称为回归。回归问题的一个例子是预测鲑鱼的长度与年龄和体重的关系。

### 无监督学习

其中训练数据由一组没有任何相应目标值的输入向量组成。这些问题的目标可能是发现数据中类似例子的集合，称为集群，或确定输入空间内的数据分布（称为 密度估计），或者从高维度投影数据为了可视化的目的，将空间降低到二维或三维 。

## 训练集和测试集

> 机器学习是关于学习数据集的一些属性并将其应用于新数据。这就是为什么在机器学习中评估一种算法的一种常见做法是，将手头的数据分成两组，一组称为训练组，我们称其为学习数据属性的训练集，另一组叫 我们测试这些测试集的测试集属性。

## 加载数据示例

scikit-learn带有一些标准数据集，例如 用于分类的 虹膜和数字数据集以及用于回归的波士顿房价数据集。

在下面，我们从我们的shell启动一个Python解释器，然后加载iris和digits数据集。我们的符号约定是 $表示shell提示符，同时>>>表示Python解释器提示符：

```python
$ python
>>> from sklearn import datasets
>>> iris = datasets.load_iris()
>>> digits = datasets.load_digits()
```

数据集可以比拟为字典，保存所有数据和一些关于数据的元素，数据存储在.data成员中，该成员是一个数组。在监督情况下，一个或多个响应变量存在成员.target中.

例如，在数字数据集的情况下digits.data，可以访问可用于对数字样本进行分类的特征：

```python
>>> print(digits.data)  
[[  0.   0.   5. ...,   0.   0.   0.]
 [  0.   0.   0. ...,  10.   0.   0.]
 [  0.   0.   0. ...,  16.   9.   0.]
 ...,
 [  0.   0.   1. ...,   6.   0.   0.]
 [  0.   0.   2. ...,  12.   0.   0.]
 [  0.   0.  10. ...,  12.   1.   0.]]
```

digits.target给出了数字数据集的真实数据，即与我们试图学习的每个数字图像对应的数字：

```
>>> digits.target
array([0, 1, 2, ..., 8, 9, 8])
```

## 数据阵列的形状

尽管原始数据可能具有不同的形状，但数据始终是2D阵列形式(n_samples, n_features)。在数字的情况下，每个原始样本都是形状的图像，可以通过以下方式访问：

```
>>> digits.images[0]
array([[  0.,   0.,   5.,  13.,   9.,   1.,   0.,   0.],
       [  0.,   0.,  13.,  15.,  10.,  15.,   5.,   0.],
       [  0.,   3.,  15.,   2.,   0.,  11.,   8.,   0.],
       [  0.,   4.,  12.,   0.,   0.,   8.,   8.,   0.],
       [  0.,   5.,   8.,   0.,   0.,   9.,   8.,   0.],
       [  0.,   4.,  11.,   0.,   1.,  12.,   7.,   0.],
       [  0.,   2.,  14.,   5.,  10.,  12.,   0.,   0.],
       [  0.,   0.,   6.,  13.,  10.,   0.,   0.,   0.]])
```

这个数据集的简单例子说明了如何从最初的问题开始，可以通过scikit-learn来形成数据供用户使用。

> 也可从外部加载数据集

## 学习和预测

在数字数据集的情况下，任务是预测给定的图像，它代表了哪个数字。我们给出了10个可能类别（数字0到9）中每一个的样本，我们可以根据这些样本拟合一个 估计器，以便能够预测 未看到的样本所属的类别。

在scikit学习中，分类的估计用python 对象 fit(X, y)和predict(T)实现

估计器的一个例子是sklearn.svm.SVC实现支持向量分类的类。估计量的构造函数将模型的参数作为参数，但是暂时我们会将估计量视为一个黑盒子：

一个预测的例子是sklearn.svm.SVC实现支持向量分类。估计量的构造函数将模型作为参数，但是暂时我们会将估计量视为一个黑盒子：

```
>>> from sklearn import svm
>>> clf = svm.SVC(gamma=0.001, C=100.)
```

### 选择模型参数

在这个例子中，我们手动设置了gamma值。通过使用网格搜索和交叉验证等工具，可以自动为参数自动找到合适的值。

我们估计器称为clf，因为它是一个分类器。现在它要找到合适的模型，也就是说，它必须从模型中学习。通过将训练集传递给fit方法来完成的。作为训练集，让我们使用除最后一个之外的数据集的所有图像。我们用[:-1]Python语法来选择这个训练集，它产生一个新的数组，其中包含除最后一项之外的所有数据digits.data：

```
>>> clf.fit(digits.data[:-1], digits.target[:-1])  
SVC(C=100.0, cache_size=200, class_weight=None, coef0=0.0,
  decision_function_shape='ovr', degree=3, gamma=0.001, kernel='rbf',
  max_iter=-1, probability=False, random_state=None, shrinking=True,
  tol=0.001, verbose=False)
```

现在您可以预测新的值，我们可以向分类器询问digits数据集中最后一幅图像的数字是什么，我们还没有用它来训练分类器：

```
>>> clf.predict(digits.data[-1:])
array([8])
```

表明该预测为8

## 持久化模型

通过使用Python的内置持久化模型，即pickle，可以在scikit中保存模型：

```
>>> from sklearn import svm
>>> from sklearn import datasets
>>> clf = svm.SVC()
>>> iris = datasets.load_iris()
>>> X, y = iris.data, iris.target
>>> clf.fit(X, y)  
SVC(C=1.0, cache_size=200, class_weight=None, coef0=0.0,
  decision_function_shape='ovr', degree=3, gamma='auto', kernel='rbf',
  max_iter=-1, probability=False, random_state=None, shrinking=True,
  tol=0.001, verbose=False)

>>> import pickle
>>> s = pickle.dumps(clf)
>>> clf2 = pickle.loads(s)
>>> clf2.predict(X[0:1])
array([0])
>>> y[0]
0
```

在scikit的特定情况下，使用joblib更换pickle（joblib.dump＆joblib.load）可能会更有趣，它对大数据更有效，但只能存到磁盘而不是以字符串方式存储

```
>>> from sklearn.externals import joblib
>>> joblib.dump(clf, 'filename.pkl') 
```

稍后，您可以使用以下方式加载pickled模型（可以在另一个Python进程中）：

```
>>> clf = joblib.load('filename.pkl') 
```

**注意: joblib.dump和joblib.load函数也接受文件类对象而不是文件名。**

**请注意，pickle有一些安全性和可维护性问题。**

## 约定

scikit-learn 预测器遵循一定的规则使他们的行为更具预测性。

### 类型转换

除非另有说明，否则输入将转换为float64：

```
>>> import numpy as np
>>> from sklearn import random_projection

>>> rng = np.random.RandomState(0)
>>> X = rng.rand(10, 2000)
>>> X = np.array(X, dtype='float32')
>>> X.dtype
dtype('float32')

>>> transformer = random_projection.GaussianRandomProjection()
>>> X_new = transformer.fit_transform(X)
>>> X_new.dtype
dtype('float64')
```

在这个例子中，X是float32，通过fit_transform(X)被转换为float64 。

将回归目标投射到float64，分类目标得到维护：

```
>>> from sklearn import datasets
>>> from sklearn.svm import SVC
>>> iris = datasets.load_iris()
>>> clf = SVC()
>>> clf.fit(iris.data, iris.target)  
SVC(C=1.0, cache_size=200, class_weight=None, coef0=0.0,
  decision_function_shape='ovr', degree=3, gamma='auto', kernel='rbf',
  max_iter=-1, probability=False, random_state=None, shrinking=True,
  tol=0.001, verbose=False)

>>> list(clf.predict(iris.data[:3]))
[0, 0, 0]

>>> clf.fit(iris.data, iris.target_names[iris.target])  
SVC(C=1.0, cache_size=200, class_weight=None, coef0=0.0,
  decision_function_shape='ovr', degree=3, gamma='auto', kernel='rbf',
  max_iter=-1, probability=False, random_state=None, shrinking=True,
  tol=0.001, verbose=False)

>>> list(clf.predict(iris.data[:3]))  
['setosa', 'setosa', 'setosa']
```

在这里，第一个predict()返回一个整数数组，因为iris.target （一个整数数组）被用于fit。第二个predict()返回一个字符串数组，因为iris.target_names用于拟合。

### 更新参数

估计器的超参数可以在通过该sklearn.pipeline.Pipeline.set_params方法构建之后更新。fit() 多次调用会覆盖以前所学习的内容fit()：

```
>>> import numpy as np
>>> from sklearn.svm import SVC

>>> rng = np.random.RandomState(0)
>>> X = rng.rand(100, 10)
>>> y = rng.binomial(1, 0.5, 100)
>>> X_test = rng.rand(5, 10)

>>> clf = SVC()
>>> clf.set_params(kernel='linear').fit(X, y)  
SVC(C=1.0, cache_size=200, class_weight=None, coef0=0.0,
  decision_function_shape='ovr', degree=3, gamma='auto', kernel='linear',
  max_iter=-1, probability=False, random_state=None, shrinking=True,
  tol=0.001, verbose=False)
>>> clf.predict(X_test)
array([1, 0, 1, 1, 0])

>>> clf.set_params(kernel='rbf').fit(X, y)  
SVC(C=1.0, cache_size=200, class_weight=None, coef0=0.0,
  decision_function_shape='ovr', degree=3, gamma='auto', kernel='rbf',
  max_iter=-1, probability=False, random_state=None, shrinking=True,
  tol=0.001, verbose=False)
>>> clf.predict(X_test)
array([0, 0, 0, 1, 0])
```

这里，默认内核rbf首先linear在估计器已经被构造之后被SVC()改变，并且改回到rbf重新估计器并且进行第二次预测。

### 多类与多标签拟合

使用时，执行的学习和预测任务取决于适合的目标数据的格式：multiclass classifiers

```
>>> from sklearn.svm import SVC
>>> from sklearn.multiclass import OneVsRestClassifier
>>> from sklearn.preprocessing import LabelBinarizer

>>> X = [[1, 2], [2, 4], [4, 5], [3, 2], [3, 1]]
>>> y = [0, 0, 1, 1, 2]

>>> classif = OneVsRestClassifier(estimator=SVC(random_state=0))
>>> classif.fit(X, y).predict(X)
array([0, 0, 1, 1, 2])
```

在上述情况下，分类器适合多分类标签的一维阵列，predict()因此该方法提供相应的多分类预测。它也可以适用于二维标签指标的二维数组：

```
>>> y = LabelBinarizer().fit_transform(y)
>>> classif.fit(X, y).predict(X)
array([[1, 0, 0],
       [1, 0, 0],
       [0, 1, 0],
       [0, 0, 0],
       [0, 0, 0]])
```

这里，分类器是fit() 上的二维二进制标记表示y，使用LabelBinarizer。在这种情况下predict()返回的的二维数组表示相应的多标签预测。

请注意，第四个和第五个实例返回的全部是0，表示它们没有匹配三个标签。对于多标签输出，同样可以为一个实例分配多个标签：

```
>> from sklearn.preprocessing import MultiLabelBinarizer
>> y = [[0, 1], [0, 2], [1, 3], [0, 2, 3], [2, 4]]
>> y = MultiLabelBinarizer().fit_transform(y)
>> classif.fit(X, y).predict(X)
array([[1, 1, 0, 0, 0],
       [1, 0, 1, 0, 0],
       [0, 1, 0, 1, 0],
       [1, 0, 1, 1, 0],
       [0, 0, 1, 0, 1]])
```

在这种情况下，分类器适用于每个分配了多个标签的实例。MultiLabelBinarizer用于multilabels的二维阵列以二进制化fit。因此， predict()为每个实例返回一个包含多个预测标签的二维数组。
