---
layout: post
title: Install SKLearn with python36 on Windows
categories: DeepLearning
---

### Steps:

#### 1.Install Python 36 on windows

[Python36](https://www.python.org/ftp/python/3.6.5/python-3.6.5-amd64.exe)

After installed, Test by

```
python -V
```

it should be 'THE VERSION OF PYTHON'

**After installed, We can change the mirrors of pip to Tsinghua sources.It will be better in using of pip**

- 1.Create folder in %PATH_TO_users/USER% and named pip
- 2.Create a file named pip.ini in pip folder with the content of below

```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

#### 2.Install Numpy+MKL,Scipy package and Scikit-learn

Open the Website of: https://www.lfd.uci.edu/~gohlke/pythonlibs. It is the libs of python, And most of all packages could be found on it.

- 1.Find the **numpy‑1.14.3+mkl‑cp37‑cp37m‑win_amd64.whl** and download, It is about 217MB
- 2.Find the **scipy‑1.1.0‑cp36‑cp36m‑win_amd64.whl** and download, It is about 12MB
- 3.Find the **scikit_learn-0.19.1-cp36-cp36m-win_amd64.whl**
- 4.Install by the comind:

```
pip install PATH_TO_THE_FILE_**numpy-1.14.3+mkl-cp36-cp36m-win_amd64.whl**
```

**Note: The file name should be same with website name, and the file name should be format with utf8, If copy directly from website, the format would be wrong.**

After installed:

```
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
Processing c:\app\numpy-1.14.3+mkl-cp36-cp36m-win_amd64.whl
Installing collected packages: numpy
Successfully installed numpy-1.14.3+mkl
```

```
pip install PATH_TO_THE_FILE_**scipy-1.1.0-cp36-cp36m-win_amd64.whl**
```

After installed:

```
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
Processing c:\app\scipy-1.1.0-cp36-cp36m-win_amd64.whl
Requirement already satisfied: numpy>=1.8.2 in c:\program files\python36\lib\site-packages (from scipy==1.1.0) (1.14.3+mkl)
Installing collected packages: scipy
Successfully installed scipy-1.1.0
```

```
pip install PATH_TO_THE_FILE_**scikit_learn-0.19.1-cp36-cp36m-win_amd64.whl**
```

After installed:

```
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
Processing c:\app\scikit_learn-0.19.1-cp36-cp36m-win_amd64.whl
Installing collected packages: scikit-learn
Successfully installed scikit-learn-0.19.1
```

#### 3.Install matplotlib

```
pip install matplotlib
```
#### 4.Install Ipython

```
pip install ipython
```

And now, the prepare work is done.

Test:

```
PS C:\App> python
Python 3.6.5 (v3.6.5:f59c0932b4, Mar 28 2018, 17:00:18) [MSC v.1900 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> from sklearn import datasets
>>> iris = datasets.load_iris()
>>> digits = datasets.load_digits()
>>> print(digits.data)
[[ 0.  0.  5. ...  0.  0.  0.]
 [ 0.  0.  0. ... 10.  0.  0.]
 [ 0.  0.  0. ... 16.  9.  0.]
 ...
 [ 0.  0.  1. ...  6.  0.  0.]
 [ 0.  0.  2. ... 12.  0.  0.]
 [ 0.  0. 10. ... 12.  1.  0.]]
>>>
```

