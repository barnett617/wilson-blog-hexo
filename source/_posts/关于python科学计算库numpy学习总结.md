---
title: 关于python科学计算库numpy学习总结
date: 2017-08-16 16:20:15
update: 2017-08-22 12:00:00
tags: [python]
categories: 后端
---

> 本文中部分print输出可能会报错，原因为python3的print通过函数方式使用，与python2中的print通过解释执行不同，需要使用print()进行控制台打印

<!-- more -->

###	安装numpy
python3 -m pip install -U pip更新pip
pip install numpy

#### 安装方式II
pip install ipython
ipython --pylab

pylab模式下会自动导入SciPy,NumPy,Matplotlib模块

###  引入numpy
import numpy as py

### 使用numpy
arange()函数用于创建同类型多维数组（homogeneous multidimensional array）

用arange创建的数组使用type()查看类型为ndarray

reshape()函数用于重新构造数组成为其他维度数组

例如：np.arange(20).reshape(4,5)



[[ 0  1  2  3  4]
 [ 5  6  7  8  9]
 [10 11 12 13 14]
 [15 16 17 18 19]]

#### arrry数组相关属性：
ndim：维度
shape：各维度大小
size：元素个数
dtype：元素类型
dsize：元素占位大小

#### 生成特殊矩阵
全零矩阵：np.zeros()

注意：ones()和zeros()函数的第一个参数是一个指向数列的指针，不能直接是一个数列，例如上图报错情况

全一矩阵：np.ones(d,dtype=int)
默认生成浮点型，可通过第二个参数指定元素数据类型

随机数数组

np.random.rand(5)生成包含5个[0,1)区间的数的数组

#### 数组计算
a = np.array([1.0, 2],[2, 4])
a
[[ 1.  2.]
 [ 2.  4.]]
 由于数组是【同质】的，python会自动将整型转换为浮点型

 -  np.exp(a)：自然常数e（约等于2.7）的a次方
 - np.sqrt(a)：a的开方
 - np.square(a)：a的平方
 -  np.power(a,3)：a的3次方


 - a.sum()：所有元素之和
 - a.max()：最大元素
 - a.min()：最小元素
 - a.max(axis=1)：每行最大
 - a.min(axis=0)：每列最小

#### 数组与矩阵（matrix）
##### 注意：

 - 矩阵是二维数组，矩阵乘法相求左侧矩阵列数等于右侧矩阵行数
 - 数组可以是任意正整数维数，乘法要求两侧数组行列数均相同

##### 相互转换
###### 数组转矩阵
np.asmatrix(a)
np.mat(a)
###### 直接生成
np.matrix('1.0 2.0;3.0 4.0')

#### 生成指定长度的一维数组
np.linspace(0,2,9)：生成从0开始，到2结束，包含9个元素的等差数列

#### **数组元素访问
a = np.array([3.2, 1.5],[2.5, 4])
print a[0][1]
1.5
print a[0,1]
1.5

注意：
若b=a是将b和a同时指向同一个array，若修改a或者b的某个元素，a和b都会改变
若想a和b不会关联修改，则需要b = a.copy()为b单独生成一份拷贝

a:
[[ 0  1  2  3  4]
 [ 5  6  7  8  9]
 [10 11 12 13 14]
 [15 16 17 18 19]]

a[: , [1,3]]：访问a的所有行的2、4列

##### **访问符合条件的元素
a[: , 2][a[: , 0] > 5]

解释：
a [x] [y]表示访问符合x、y条件的a的元素，[: , 2]表示取所有行的第3列，[a[: , 0] > 5]表示取第一列大于5的行（即第3、4行），最终即表示取第3、4行的第3列，即得结果array([12, 17])这个“子”数组

numpy.where()查找符合条件的位置
例如：loc = np.where(a == 11)
print loc
(array([2]), array([1]))

结果是一个表示坐标的元组，元组第一个数组表示查询结果的行坐标，第二个数组表示结果的列坐标
print a[loc[0][0], loc[1][0]]
11

上式为通过位置反求元素11
注意：where求出的结果为元组，不能通过loc[x,y]的方式获取元素（该获取方式为数组的方式，因为元组没有索引），只能通过loc[x][y]的方式获取

### 数组其他操作
#### 矩阵转置
a = np.random.rand(2,4)
a = np.transpose(a)将a数组转置

b = np.random.rand(2,4)
b = np.mat(b)
print b.T    转置矩阵

#### 矩阵求逆
import numpy.linalg as nlg
a = np.random.rand(2,2)
a = np.mat(a)
ia = nlg.inv(a)  得逆矩阵
print a * ia

[[ 1.  0.]
 [ 0.  1.]]

#### 特征值和特征向量
a = np.random.rand(3,3)
eig_value, eig_vector = nlg.eig(a)

#### 拼接矩阵（使用场景：循环处理某些数据后的操作）
按列拼接两个向量成一个矩阵

vstack
hstack

实例：
a = np.random.rand(2,2)
b = np.random.rand(2,2)
c = np.hstack([a,b])  水平拼接
d = np.vstack([a,b])  垂直拼接

#### 缺失值
nan作为缺失值的记录
通过isnan判定
a = np.random.rand(2,2)
a[0, 1] = np.nan
print (np.isnan(a))

nan_to_num可用来将nan替换成0
pandas提供能指定nan替换值的函数

print(np.nan_to_num(a))
[[ 0.54266589  0.        ]
 [ 0.92468339  0.70599254]]

更多Numpy函数见
[http://wiki.scipy.org/Numpy_Example_List](http://wiki.scipy.org/Numpy_Example_List)
[http://docs.scipy.org/doc/numpy](http://docs.scipy.org/doc/numpy)

参考文献
https://uqer.io/community/share/54ca15f9f9f06c276f651a56

http://wiki.scipy.org/Tentative_NumPy_Tutorial

Sheppard K. Introduction to Python for econometrics, statistics and data analysis. Self-published, University of Oxford, version, 2012, 2.
