---
layout: post
title: TensorFlow【深度学习】MNIST 视觉数据集 训练识别模型与评估 初级
categories: DeepLearning
---

- postname: TensorFlow【深度学习】逻辑分类（Logistic Classification）
- 2017-07-28 shine

> MNIST是一个入门级的计算机视觉数据集，它包含各种手写数字图片, 它也包含每一张图片对应的标签，告诉我们这个是数字几。比如，下面这四张图片的标签分别是5，0，4，1。

![这里写图片描述](http://img.blog.csdn.net/20170726173325352?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## 准备MNIST数据集

> 通过以下代码下载数据集

- input_data.py

```python
# Copyright 2015 Google Inc. All Rights Reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
# ==============================================================================
"""Functions for downloading and reading MNIST data."""
from __future__ import absolute_import
from __future__ import division
from __future__ import print_function
import gzip
import os
import tensorflow.python.platform
import numpy
from six.moves import urllib
from six.moves import xrange  # pylint: disable=redefined-builtin
import tensorflow as tf
SOURCE_URL = 'http://yann.lecun.com/exdb/mnist/'
def maybe_download(filename, work_directory):
  """Download the data from Yann's website, unless it's already here."""
  if not os.path.exists(work_directory):
    os.mkdir(work_directory)
  filepath = os.path.join(work_directory, filename)
  if not os.path.exists(filepath):
    filepath, _ = urllib.request.urlretrieve(SOURCE_URL + filename, filepath)
    statinfo = os.stat(filepath)
    print('Successfully downloaded', filename, statinfo.st_size, 'bytes.')
  return filepath
def _read32(bytestream):
  dt = numpy.dtype(numpy.uint32).newbyteorder('>')
  return numpy.frombuffer(bytestream.read(4), dtype=dt)[0]
def extract_images(filename):
  """Extract the images into a 4D uint8 numpy array [index, y, x, depth]."""
  print('Extracting', filename)
  with gzip.open(filename) as bytestream:
    magic = _read32(bytestream)
    if magic != 2051:
      raise ValueError(
          'Invalid magic number %d in MNIST image file: %s' %
          (magic, filename))
    num_images = _read32(bytestream)
    rows = _read32(bytestream)
    cols = _read32(bytestream)
    buf = bytestream.read(rows * cols * num_images)
    data = numpy.frombuffer(buf, dtype=numpy.uint8)
    data = data.reshape(num_images, rows, cols, 1)
    return data
def dense_to_one_hot(labels_dense, num_classes=10):
  """Convert class labels from scalars to one-hot vectors."""
  num_labels = labels_dense.shape[0]
  index_offset = numpy.arange(num_labels) * num_classes
  labels_one_hot = numpy.zeros((num_labels, num_classes))
  labels_one_hot.flat[index_offset + labels_dense.ravel()] = 1
  return labels_one_hot
def extract_labels(filename, one_hot=False):
  """Extract the labels into a 1D uint8 numpy array [index]."""
  print('Extracting', filename)
  with gzip.open(filename) as bytestream:
    magic = _read32(bytestream)
    if magic != 2049:
      raise ValueError(
          'Invalid magic number %d in MNIST label file: %s' %
          (magic, filename))
    num_items = _read32(bytestream)
    buf = bytestream.read(num_items)
    labels = numpy.frombuffer(buf, dtype=numpy.uint8)
    if one_hot:
      return dense_to_one_hot(labels)
    return labels
class DataSet(object):
  def __init__(self, images, labels, fake_data=False, one_hot=False,
               dtype=tf.float32):
    """Construct a DataSet.
    one_hot arg is used only if fake_data is true.  `dtype` can be either
    `uint8` to leave the input as `[0, 255]`, or `float32` to rescale into
    `[0, 1]`.
    """
    dtype = tf.as_dtype(dtype).base_dtype
    if dtype not in (tf.uint8, tf.float32):
      raise TypeError('Invalid image dtype %r, expected uint8 or float32' %
                      dtype)
    if fake_data:
      self._num_examples = 10000
      self.one_hot = one_hot
    else:
      assert images.shape[0] == labels.shape[0], (
          'images.shape: %s labels.shape: %s' % (images.shape,
                                                 labels.shape))
      self._num_examples = images.shape[0]
      # Convert shape from [num examples, rows, columns, depth]
      # to [num examples, rows*columns] (assuming depth == 1)
      assert images.shape[3] == 1
      images = images.reshape(images.shape[0],
                              images.shape[1] * images.shape[2])
      if dtype == tf.float32:
        # Convert from [0, 255] -> [0.0, 1.0].
        images = images.astype(numpy.float32)
        images = numpy.multiply(images, 1.0 / 255.0)
    self._images = images
    self._labels = labels
    self._epochs_completed = 0
    self._index_in_epoch = 0
  @property
  def images(self):
    return self._images
  @property
  def labels(self):
    return self._labels
  @property
  def num_examples(self):
    return self._num_examples
  @property
  def epochs_completed(self):
    return self._epochs_completed
  def next_batch(self, batch_size, fake_data=False):
    """Return the next `batch_size` examples from this data set."""
    if fake_data:
      fake_image = [1] * 784
      if self.one_hot:
        fake_label = [1] + [0] * 9
      else:
        fake_label = 0
      return [fake_image for _ in xrange(batch_size)], [
          fake_label for _ in xrange(batch_size)]
    start = self._index_in_epoch
    self._index_in_epoch += batch_size
    if self._index_in_epoch > self._num_examples:
      # Finished epoch
      self._epochs_completed += 1
      # Shuffle the data
      perm = numpy.arange(self._num_examples)
      numpy.random.shuffle(perm)
      self._images = self._images[perm]
      self._labels = self._labels[perm]
      # Start next epoch
      start = 0
      self._index_in_epoch = batch_size
      assert batch_size <= self._num_examples
    end = self._index_in_epoch
    return self._images[start:end], self._labels[start:end]
def read_data_sets(train_dir, fake_data=False, one_hot=False, dtype=tf.float32):
  class DataSets(object):
    pass
  data_sets = DataSets()
  if fake_data:
    def fake():
      return DataSet([], [], fake_data=True, one_hot=one_hot, dtype=dtype)
    data_sets.train = fake()
    data_sets.validation = fake()
    data_sets.test = fake()
    return data_sets
  TRAIN_IMAGES = 'train-images-idx3-ubyte.gz'
  TRAIN_LABELS = 'train-labels-idx1-ubyte.gz'
  TEST_IMAGES = 't10k-images-idx3-ubyte.gz'
  TEST_LABELS = 't10k-labels-idx1-ubyte.gz'
  VALIDATION_SIZE = 5000
  local_file = maybe_download(TRAIN_IMAGES, train_dir)
  train_images = extract_images(local_file)
  local_file = maybe_download(TRAIN_LABELS, train_dir)
  train_labels = extract_labels(local_file, one_hot=one_hot)
  local_file = maybe_download(TEST_IMAGES, train_dir)
  test_images = extract_images(local_file)
  local_file = maybe_download(TEST_LABELS, train_dir)
  test_labels = extract_labels(local_file, one_hot=one_hot)
  validation_images = train_images[:VALIDATION_SIZE]
  validation_labels = train_labels[:VALIDATION_SIZE]
  train_images = train_images[VALIDATION_SIZE:]
  train_labels = train_labels[VALIDATION_SIZE:]
  data_sets.train = DataSet(train_images, train_labels, dtype=dtype)
  data_sets.validation = DataSet(validation_images, validation_labels,
                                 dtype=dtype)
  data_sets.test = DataSet(test_images, test_labels, dtype=dtype)
  return data_sets
```

- 下载数据集脚本, download.py

```python
import input_data
mnist = input_data.read_data_sets("MNIST_data/", one_hot=True)
```

> 下载下来的数据集被分成两部分：60000行的训练数据集（mnist.train）和10000行的测试数据集（mnist.test）。这样的切分很重要，在机器学习模型设计时必须有一个单独的测试数据集不用于训练而是用来评估这个模型的性能，从而更加容易把设计的模型推广到其他数据集上（泛化）。每一个MNIST数据单元有两部分组成：一张包含手写数字的图片和一个对应的标签。把这些图片设为“xs”，把这些标签设为“ys”。训练数据集和测试数据集都包含xs和ys，比如训练数据集的图片是 mnist.train.images ，训练数据集的标签是 mnist.train.labels。

> 每一张图片包含28像素X28像素。用一个数字数组来表示这张图片.长度是 28x28 = 784。如何展开这个数组不重要，只要保持各个图片采用相同的方式展开。从这个角度来看，MNIST数据集的图片就是在784维向量空间里面的点, 并且拥有比较复杂的结构.

> 在MNIST训练数据集中，mnist.train.images 是一个形状为 [60000, 784] 的张量，第一个维度数字用来索引图片，第二个维度数字用来索引每张图片中的像素点。在此张量里的每一个元素，都表示某张图片里的某个像素的强度值，值介于0和1之间。

> 相对应的MNIST数据集的标签是介于0到9的数字，用来描述给定图片里表示的数字。标签数据是"one-hot vectors,  一个one-hot向量除了某一位的数字是1以外其余各维度数字都是0。数字n将表示成一个只有在第n维度（从0开始）数字为1的10维向量。比如, 标签0将表示成([1,0,0,0,0,0,0,0,0,0,0])。因此， mnist.train.labels 是一个 [60000, 10] 的数字矩阵。

![这里写图片描述](http://img.blog.csdn.net/20170726174750067?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

##　构建模型

### softmax 回归

MNIST的每一张图片都表示一个数字，从0到9。要得到给定图片代表每个数字的概率。模型可能推测一张包含9的图片代表数字9的概率是80%但是判断它是8的概率是5%（因为8和9都有上半部分的小圆），然后给予它代表其他数字的概率更小的值。

> 这是一个使用softmax回归（softmax regression）模型的经典案例。softmax模型可以用来给不同的对象分配概率。

 - **softmax回归（softmax regression）分两步：第一步**

> 为了得到一张给定图片属于某个特定数字类的证据（evidence），我们对图片像素值进行加权求和。如果这个像素具有很强的证据说明这张图片不属于该类，那么相应的权值为负数，相反如果这个像素拥有有利的证据支持这张图片属于这个类，那么权值是正数。

- 下面的图片显示了一个模型学习到的图片上每个像素对于特定数字类的权值。红色代表负数权值，蓝色代表正数权值。

![这里写图片描述](http://img.blog.csdn.net/20170726175206980?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 需要加入一个额外的偏置量（bias），因为输入往往会带有一些无关的干扰量。因此对于给定的输入图片 x 它代表的是数字 i 的证据可以表示为:

![这里写图片描述](http://img.blog.csdn.net/20170726175257726?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


- 其中Wi 代表权重，bi 代表数字 i 类的偏置量，j 代表给定图片 x 的像素索引用于像素求和。然后用softmax函数可以把这些证据转换成概率 y:

> y=softmax(evidence)

- softmax可以看成是一个激励（activation）函数或者链接（link）函数，把定义的线性函数的输出转换成想要的格式，也就是关于10个数字类的概率分布。因此，给定一张图片，它对于每一个数字的吻合度可以被softmax函数转换成为一个概率值。softmax函数可以定义为：

> softmax(x) = normalize(exp(x))

- 展开等式右边的子式：

![这里写图片描述](http://img.blog.csdn.net/20170726180036667?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 但是更多的时候把softmax模型函数定义为前一种形式：把输入值当成幂指数求值，再正则化这些结果值。这个幂运算表示，更大的证据对应更大的假设模型（hypothesis）里面的乘数权重值。反之，拥有更少的证据意味着在假设模型里面拥有更小的乘数系数。假设模型里的权值不可以是0值或者负值。Softmax然后会正则化这些权重值，使它们的总和等于1，以此构造一个有效的概率分布。

- 对于softmax回归模型可以用下面的图解释，对于输入的xs加权求和，再分别加上一个偏置量，最后再输入到softmax函数中：

![这里写图片描述](http://img.blog.csdn.net/20170728160148489?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

如果把它写成一个等式，可以得到：

![这里写图片描述](http://img.blog.csdn.net/20170728160228159?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- 也可以用向量表示这个计算过程：用矩阵乘法和向量相加。这有助于提高计算效率。（也是一种更有效的思考方式）

![这里写图片描述](http://img.blog.csdn.net/20170728160343937?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

更进一步，可以写成更加紧凑的方式：

> y=softMax(Wx+b)


### **实现softmax模型**

- 为了用python实现高效的数值计算，我们通常会使用函数库，比如NumPy，会把类似矩阵乘法这样的复杂运算使用其他外部语言实现。不幸的是，从外部计算切换回Python的每一个操作，仍然是一个很大的开销。如果你用GPU来进行外部计算，这样的开销会更大。用分布式的计算方式，也会花费更多的资源用来传输数据。

> TensorFlow也把复杂的计算放在python之外完成，但是为了避免前面说的那些开销，做了进一步完善。Tensorflow不单独地运行单一的复杂计算，而是让我们可以先用图描述一系列可交互的计算操作，然后全部一起在Python之外运行。

使用TensorFlow之前，首先导入它：

```python
import tensorflow as tf
```

通过操作符号变量来描述这些可交互的操作单元，可以用下面的方式创建一个：

```python
x = tf.placeholder("float", [None, 784])
```

> x不是一个特定的值，而是一个占位符placeholder，在TensorFlow运行计算时输入这个值。希望能够输入任意数量的MNIST图像，每一张图展平成784维的向量。用2维的浮点数张量来表示这些图，这个张量的形状是[None，784 ]。（这里的None表示此张量的第一个维度可以是任何长度的。）

模型也需要权重值和偏置量，当然可以把它们当做是另外的输入（使用占位符），但TensorFlow有一个更好的方法来表示它们：Variable 。 一个Variable代表一个可修改的张量，存在在TensorFlow的用于描述交互性操作的图中。它们可以用于计算输入值，也可以在计算中被修改。对于各种机器学习应用，一般都会有模型参数，可以用Variable表示。

```python
W = tf.Variable(tf.zeros([784,10]))
b = tf.Variable(tf.zeros([10]))
```

赋予tf.Variable不同的初值来创建不同的Variable：在这里，都用全为零的张量来初始化W和b。因为要学习W和b的值，它们的初值可以随意设置。

- 注意，**W的维度是[784，10]**，因为想要用784维的图片向量乘以它以得到一个10维的证据值向量，每一位对应不同数字类。b的形状是[10]，所以可以直接把它加到输出上面。

现在，可以实现模型啦。只需要一行代码:

```python
y = tf.nn.softmax(tf.matmul(x,W) + b)
```

首先，用tf.matmul(​​X，W)表示x乘以W，对应之前等式里面的，这里x是一个2维张量拥有多个输入。然后再加上b，把和输入到tf.nn.softmax函数里面。

TensorFlow不仅仅可以使softmax回归模型计算变得特别简单，它也用这种非常灵活的方式来描述其他各种数值计算，从机器学习模型对物理学模拟仿真模型。一旦被定义好之后，模型就可以在不同的设备上运行：计算机的CPU，GPU，甚至是手机！

## **训练模型**

> 为了训练模型，首先需要定义一个指标来评估这个模型是好的。其实，在机器学习，通常定义指标来表示一个模型是坏的，这个指标称为成本（cost）或损失（loss），然后尽量最小化这个指标。但是，这两种方式是相同的。

- 一个非常常见的，非常漂亮的成本函数是“交叉熵”（cross-entropy）。交叉熵产生于信息论里面的信息压缩编码技术，但是它后来演变成为从博弈论到机器学习等其他领域里的重要技术手段。它的定义如下：

![这里写图片描述](http://img.blog.csdn.net/20170728162549488?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hpbmVwYW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

- y 是预测的概率分布, y' 是实际的分布（输入的one-hot vector)。比较粗糙的理解是，交叉熵是用来衡量我们的预测用于描述真相的低效性。

> 最小交叉熵详细理解： http://colah.github.io/posts/2015-09-Visual-Information/

为了计算交叉熵，首先需要添加一个新的占位符用于输入正确值：

```python
y_ = tf.placeholder("float", [None,10])
```

然后使用公式计算最小交叉熵

```python
cross_entropy = -tf.reduce_sum(y_*tf.log(y))
```

> 首先，用 tf.log 计算 y 的每个元素的对数。接下来，把 y_ 的每一个元素和 tf.log(y_) 的对应元素相乘。最后，用 tf.reduce_sum 计算张量的所有元素的总和。（注意，**这里的交叉熵不仅仅用来衡量单一的一对预测和真实值，而是所有100幅图片的交叉熵的总和。**对于100个数据点的预测表现比单一数据点的表现能更好地描述模型的性能。用TensorFlow来训练它是非常容易的。因为TensorFlow拥有一张描述各个计算单元的图，它可以自动地使用反向传播算法(backpropagation algorithm)来有效地确定变量是如何影期望的最小化的那个成本值。然后，TensorFlow会用选择的优化算法来不断地修改变量以降低成本。

```python
train_step = tf.train.GradientDescentOptimizer(0.01).minimize(cross_entropy)
```

在这里, 使用TensorFlow用梯度下降算法（gradient descent algorithm）以0.01的学习速率最小化交叉熵。梯度下降算法（gradient descent algorithm）是一个简单的学习过程，TensorFlow只需将每个变量一点点地往使成本不断降低的方向移动。当然TensorFlow也提供了其他许多优化算法：只要简单地调整一行代码就可以使用其他的算法。

TensorFlow在这里实际上所做的是，它会在后台给描述计算的那张图里面增加一系列新的计算操作单元用于实现反向传播算法和梯度下降算法。然后，它返回的只是一个单一的操作，当运行这个操作时，它用梯度下降算法训练模型，微调变量，不断减少成本。

现在，已经设置好了模型。在运行计算之前，需要添加一个操作来初始化创建的变量：

```python
init = tf.initialize_all_variables()
```

> 现在可以在一个Session里面启动模型，并且初始化变量：

```python
sess = tf.Session()
sess.run(init)
```

> 然后开始训练模型，这里我们让模型循环训练1000次

```python
for i in range(1000):
  batch_xs, batch_ys = mnist.train.next_batch(100)
  sess.run(train_step, feed_dict={x: batch_xs, y_: batch_ys})
```

> 该循环的每个步骤中，都会随机抓取训练数据中的100个批处理数据点，然后用这些数据点作为参数替换之前的占位符来运行train_step。使用一小部分的随机数据来进行训练被称为随机训练（stochastic training）- 在这里更确切的说是随机梯度下降训练。在理想情况下，用所有的数据来进行每一步的训练，因为这能有更好的训练结果，但显然这需要很大的计算开销。所以，每一次训练我们使用不同的数据子集，这样做既可以减少计算开销，又可以最大化地学习到数据集的总体特性。

## **评估模型**

> 首先找出那些预测正确的标签。tf.argmax 是一个非常有用的函数，它能给出某个tensor对象在某一维上的其数据最大值所在的索引值。由于标签向量是由0,1组成，因此最大值1所在的索引位置就是类别标签，比如tf.argmax(y,1)返回的是模型对于任一输入x预测到的标签值，而 tf.argmax(y_,1) 代表正确的标签，可以用 tf.equal 来检测预测是否真实标签匹配(索引位置一样表示匹配)。

```python
correct_prediction = tf.equal(tf.argmax(y,1), tf.argmax(y_,1))
```

> 这行代码会得到一组布尔值。为了确定正确预测项的比例，可以把布尔值转换成浮点数，然后取平均值。例如，[True, False, True, True] 会变成 [1,0,1,1] ，取平均值后得到 0.75.

```python
accuracy = tf.reduce_mean(tf.cast(correct_prediction, "float"))
```

> 最后，计算所学习到的模型在测试数据集上面的正确率。

```python
print sess.run(accuracy, feed_dict={x: mnist.test.images, y_: mnist.test.labels})
```

> **这个最终结果值应该大约是91%, 需要进一步优化**