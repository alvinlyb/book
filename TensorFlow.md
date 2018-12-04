TensorFlow
===

> python各模块参考python库整理

```python
#下面的tf为tensorflow导入的模块
import tensorflow as tf
```
## 函数
### tf.global_variables_initializer
初始化全局变量，不进行初始化Variables不能使用
```python
init = tf.global_variables_initializer()   
sess.run(init)
```
### tf.train.Saver
保存变量，也可以指定保存的内容
```python
saver=tf.train.Saver({"my_v2":v2)
```

### tf.constant

```python
tf.constant(value, dtype=None, shape=None, name='Const', verify_shape=False)
```

创建一个常量tensor,按照给出value来赋值，可以用shape来指定其形状。
value可以是一个数，也可以是一个list。
如果是list,那么len(value)一定要小于等于shape展开后的长度。
赋值时，先将value中的值逐个存入。不够的部分，则全部存入value的最后一个值。
```python
tensor=tf.constant([1,2,3,4,5,6],dtype=tf.int32,shape=[2,3]) 
```
### tf.ones

创建全是1的张量tenso

```python
#创建2x3的全部为1.的float32类型矩阵
x1=tf.ones([2,3],dtype=tf.float32)  
```
### tf.zeros

```python
tf.zeros([2, 3], dtype=int32) 
```

创建全是零的张量tenso



### tf.Variable

* **initialized_value**:初始化别一个Variable到当前相同的变量
```python
# 原始的变量  
weights = tf.Variable(tf.random_normal([784, 200], stddev=0.35),name="weights")  
# 创造相同内容的变量  
w2 = tf.Variable(weights.initialized_value(), name="w2") 
# 也可以直接乘以比例  
w_twice = tf.Variable(weights.initialized_value() * 0.2, name="w_twice")  
```
```python
#创建2x3的全部为0.的float32类型矩阵
x2=tf.Variable(tf.zeros([2,3],dtype=tf.float32),name='variable_x2')
```
### tf.ones_like

```python
#新建一个指定的tensor类型大小一致的tensor，其所有元素为1
tensor=[[1, 2, 3], [4, 5, 6]] 
x = tf.ones_like(tensor) 
print(sess.run(x))
#[[1 1 1],
# [1 1 1]]
```
### tf.zeros_like
```python
#新建一个指定的tensor类型大小一致的tensor，其所有元素为0
zeros_like=tf.zeros_like(tensor)
```
### tf.fill
```python
#创建一个形状大小为[2x3]的tensor，初始值为2
fill=tf.fill([2,3],2)   
```
### tf.truncated_normal_initializer

```python
tf.truncated_normal(shape, mean=0.0, stddev=1.0, dtype=tf.float32, seed=None, name=None)
```

从截断的正态分布中输出随机值。 
生成的值服从具有指定平均值和标准偏差的正态分布，如果生成的值大于平均值2个标准偏差的值则丢弃重新选择。

在正态分布的曲线中，横轴区间（μ-σ，μ+σ）内的面积为68.268949%。 
横轴区间（μ-2σ，μ+2σ）内的面积为95.449974%。 
横轴区间（μ-3σ，μ+3σ）内的面积为99.730020%。 
X落在（μ-3σ，μ+3σ）以外的概率小于千分之三，在实际问题中常认为相应的事件是不会发生的，基本上可以把区间（μ-3σ，μ+3σ）看作是随机变量X实际可能的取值区间，这称之为正态分布的“3σ”原则。 
在tf.truncated_normal中如果x的取值在区间（μ-2σ，μ+2σ）之外则重新进行选择。这样保证了生成的值都在均值附近。

参数:

- shape: 一维的张量，也是输出的张量。
- mean: 正态分布的均值。
- stddev: 正态分布的标准差。
- dtype: 输出的类型。
- seed: 一个整数，当设置之后，每次生成的随机数都一样。
- name: 操作的名字。

### tf.random_normal
```python
tf.random_normal(shape,mean=0.0,stddev=1.0,dtype=tf.float32,seed=None,name=None) 
```
生成随机正大随机数，均值为mean指定的,stddev是标准差的tensor，尺寸是shape

参数:

- shape: 一维的张量，也是输出的张量。
- mean: 正态分布的均值。
- stddev: 正态分布的标准差。
- dtype: 输出的类型。
- seed: 一个整数，当设置之后，每次生成的随机数都一样。
- name: 操作的名字。

```python
random_normal=tf.random_normal(shape=[2,3],mean=0.5,stddev=10.0,dtype=tf.float32,seed=None,name=None)
```

### tf.truncated_normal
截断正态分布随机数，均值为mean,标准差stddev，不过只保留[mean-2*stddev,mean+2*stddev]范围内的随机数

![白话统计学：什么叫正态分布](http://s12.sinaimg.cn/mw690/001Pzntqgy6M7rVM9uzfb&690)

```python
truncated_normal=tf.truncated_normal(shape=[3,2],mean=0.0,stddev=1.0,dtype=tf.float32,seed=None,name=None)
```
### tf.random_uniform
均匀分布随机数，范围为[minval,maxval]
```python
random_uniform=tf.random_uniform(shape=[2,3],minval=0.1,maxval=None,dtype=tf.float32,seed=None,name=None)
```
### tf.random_shuffle
```python
tf.random_shuffle(value,seed=None,name=None)
```
对value（是一个tensor）的第一维进行随机化。
```
    [[1,2],            [[2,3],
    [2,3],     ==>      [1,2],
    [3,4]]             [3,4]]
```
### tf.random_seed
```
tf.random_seed(seed)
```
生成随机种子数

### tf.get_variable
如果在该命名域中之前已经有名字=name的变量，则调用那个变量；
如果没有，则根据输入的参数重新创建一个名字为name的变量。在众多的输入参数中
* name: 变量的名字 
* shape: 变量的形状，[]表示一个数，[3]表示长为3的向量，[2,3]表示矩阵或者张量(tensor) 
* dtype: 变量的数据格式，主要有tf.int32, tf.float32, tf.float64等等 
* initializer:初始化的方式，初始化的方式有以下几种：
    - tf.constant_initializer：常量初始化函数
    - tf.random_normal_initializer：正态分布
    - tf.truncated_normal_initializer：截取的正态分布
    - tf.random_uniform_initializer：均匀分布
    - tf.zeros_initializer：全部是0
    - tf.ones_initializer：全是1
    - tf.uniform_unit_scaling_initializer：满足均匀分布，但不影响输出数量级的随机值
```python
get_variable=tf.get_variable(name='a1', shape=[2,3], initializer=tf.ones_initializer())
```
### tf.linspace
```python
tf.linspace(start,stop,num,name=None)
```
返回一个tensor等差数列，该tensor中的数值在start到stop区间取等差数列(包含start和stop),
如果num>1则差值为(start-stop)/(num-1),以保证最后一个元素的值为stop.
```python
linspace=tf.linspace(10.0,12.0,3,name=None)     #输出为10,11,12三个数列
```
### tf.range
```python
tf.range(start,limit=None,delta=1,name='range')
```
返回一个tensor等差数列，该tensor中的数值在start到limit之间，不包括limit，delta是等差数列的差值。
start，limit和delta都是int32类型。

```python
 # 'start' is 3 
 # 'limit' is 18 
 # 'delta' is 3
 tf.range(start, limit, delta) ==> [3, 6, 9, 12, 15] 
 # 'limit' is 5 start is 0
 tf.range(start, limit) ==> [0, 1, 2, 3, 4]
```
### tf.assign
```python
tf.assign(ref, value, validate_shape=None, use_locking=None, name=None)
```
assign是用来更新模型中变量的值的。ref是待赋值的变量，value是要更新的值。即效果等同于 `ref = value`
### tf.placeholder

```python
tf.placeholder(dtype, shape=None, name=None)
```

占位符告诉系统这里有一个值，在运行时才会补充上,首先要指定尺寸和类型,dtype是必须要指定的参数，shape如果是None，说明任何大小的tensor都能够接受，使用shape=None很容易定义好图，但是在debug的时候这将成为噩梦，所以最好是指定好shape。

```python
#shape=[None,3]表示输入一个的矩阵
placeholder=tf.placeholder(tf.float32,shape=[None,3],name='input')
a = tf.placeholder(tf.float32, shape=[3])
b = tf.constant([5, 5, 5], tf.float32)
c = a + b
with tf.Session() as sess:
    print(sess.run(c, feed_dict={a: [1, 2, 3]}))
```

除此之外，也可以给tensorflow中的运算进行feed操作，如下

```python
a = tf.add(2, 3)
b = tf.multiply(a, 3)
with tf.Session() as sess:
    print(sess.run(b, feed_dict={a: 2}))
>> 6
```

## 矩阵变换

### tf.shape
返回张量的形状，但是注意tf.shape函数本身也是返回一个张量。
```python
shape=tf.shape(tensor)   
```
### tf.expand_dims
为张量+1维
```python
expand_dims=tf.expand_dims(tensor,-1)
```
### tf.stack
```python
tf.pack(values, axis=0, name=”pack”)
```
矩阵拼接，将一个R维张量列表沿着axis轴组合成一个R+1维的张量。
```python
#将a,b矩阵拼接
a=tf.constant([1,2,3])
b=tf.constant([4,5,6])
stack=tf.stack([a,b],axis=0)
```
### tf.unstack
将矩阵分解,axis是分解的不同维度。
```
unstack=tf.unstack(stack,axis=0)
```
### tf.concat
将张量沿着指定维数拼接起来。跟前面的stack用法类似
```
tf.concat(concat_dim,values,name=None)
```
将张量沿着指定维数拼接起来。个人感觉跟前面的pack用法类似

### tf.sparse_to_dense
```python
tf.sparse_to_dense(sparse_indices, output_shape, sparse_values, default_value, name=None)
```
* **sparse_indices**：稀疏矩阵中那些个别元素对应的索引值,元素的坐标[[0,0],[1,2]] 表示(0,0)，和(1,2)处有值。有三种情况：
  - sparse_indices 是个数，那么它只能指定一维矩阵的某一个元素
  - sparse_indices 是个向量，那么它可以指定一维矩阵的多个元素
  - sparse_indices 是个矩阵，那么它可以指定二维矩阵的多个元素
* **output_shape** :输出的稀疏矩阵的shape
* **sparse_values**: sparse_indices坐标表示的点的值，可以是0D或者1D张量。若0D，则所有稀疏值都一样。若是1D，则len(sparse_values)应该等于len(sparse_indices)
  分两种情况：
  - sparse_values 是个数：所有索引指定的位置都用这个数
  - sparse_values 是个向量：输出矩阵的某一行向量里某一行对应的数（所以这里向量的长度应该和输出矩阵的行数对应，不然报错）
* **default_value**：未指定元素的默认值，一般如果是稀疏矩阵的话就是0了
```
举一个例子：
在mnist里面有一个把数字标签转化成onehot标签的操作，所谓`onehot标签`就是：
如果标签是6那么对应onehot就是[ 0.  0.  0.  0.  0.  0.  1.  0.  0.  0.]
如果标签是1那么对应onehot就是[ 0.  1.  0.  0.  0.  0.  0.  0.  0.  0.]
如果标签是0那么对应onehot就是[ 1.  0.  0.  0.  0.  0.  0.  0.  0.  0.]
就是把标签变为适用于神经网络输出的形式
```

```python
BATCHSIZE=6  
label=tf.expand_dims(tf.constant([0,2,3,6,7,9]),1)
index=tf.expand_dims(tf.range(0, BATCHSIZE),1)
concated = tf.concat(1, [index, label])
onehot_labels = tf.sparse_to_dense(concated, tf.stack([BATCHSIZE,10]), 1.0, 0.0)
```
### tf.argmax
```
tf.argmax(input=tensor,dimention=axis)
```
找到给定的张量tensor中在指定轴axis上的最大值的位置。
### tf.argmin
找到给定的张量tensor中在指定轴axis上的最小值的位置。
```
tf.argmin(input=tensor,dimention=axis)
```
### tf.equal
```
tf.equal(x,y,name=None)
```
判断两个tensor是否每个元素都相等。返回一个格式为bool的tensor

### tf.cast
```python
tf.cast(x,dtype,name=None)
```
将x的数据格式转化成dtype.例如，原来x的数据格式是bool，那么将其转化成float以后，就能够将其转化成0和1的序列。反之也可以
```python
把a转换为bool数据格式,大于0为True，小于0,False
cast=tf.cast(a,dtype=tf.bool,name=None)
```
### tf.matmul
矩阵乘法。若a为l*m的矩阵，b为m*n的矩阵，那么通过tf.matmul(a,b) 结果就会得到一个l*n的矩阵 
不过这个函数还提供了很多额外的功能。我们来看下函数的定义：
```python
tf.matmul(a,b,transpose_a=False,transpose_b=False,a_is_sparse=False,b_is_sparse=False,name=None)
```
可以看到还提供了transpose和is_sparse的选项。 
如果对应的transpose项为True，例如transpose_a=True,那么a在参与运算之前就会先转置一下。 
而如果a_is_sparse=True,那么a会被当做稀疏矩阵来参与运算。
### tf.reshape
```
tf.reshape(tensor,shape,name=None)
```
顾名思义，就是将tensor按照新的shape重新排列。一般来说，shape有三种用法： 
如果 shape=[-1], 表示要将tensor展开成一个list 
如果 shape=[a,b,c,…] 其中每个a,b,c,..均>0，那么就是常规用法 
如果 shape=[a,-1,c,…] 此时b=-1，a,c,..依然>0。这表示tf会根据tensor的原尺寸，自动计算b的值。 
官方给的例子:
```python
# tensor 't' is [1, 2, 3, 4, 5, 6, 7, 8, 9]
# tensor 't' has shape [9]
reshape(t, [3, 3]) ==> [[1, 2, 3],
                        [4, 5, 6],
                        [7, 8, 9]]

#tensor 't' is [[[1, 1], [2, 2]],
#                [[3, 3], [4, 4]]]
# tensor 't' has shape [2, 2, 2]
reshape(t, [2, 4]) ==> [[1, 1, 2, 2],
                        [3, 3, 4, 4]]
#tensor 't' is [[[1, 1, 1],
#                 [2, 2, 2]],
#                [[3, 3, 3],
#                 [4, 4, 4]],
#                [[5, 5, 5],
#                 [6, 6, 6]]]
#tensor 't' has shape [3, 2, 3]
#pass '[-1]' to flatten 't'

reshape(t, [-1]) ==> [1, 1, 1, 2, 2, 2, 3, 3, 3, 4, 4, 4, 5, 5, 5, 6, 6, 6]

#-1 can also be used to infer the shape
#-1 is inferred to be 9:
reshape(t, [2, -1]) ==> [[1, 1, 1, 2, 2, 2, 3, 3, 3],
                         [4, 4, 4, 5, 5, 5, 6, 6, 6]]

# -1 is inferred to be 2:
reshape(t, [-1, 9]) ==> [[1, 1, 1, 2, 2, 2, 3, 3, 3],
                         [4, 4, 4, 5, 5, 5, 6, 6, 6]]

# -1 is inferred to be 3:
reshape(t, [ 2, -1, 3]) ==> [[[1, 1, 1],
                              [2, 2, 2],
                              [3, 3, 3]],
                             [[4, 4, 4],
                              [5, 5, 5],
                              [6, 6, 6]]]
```

### tf.random_uniform	
创建了一个平均分布
$$
(-\sqrt{\frac{6}{n_{in}+n_{out}}},\sqrt{\frac{6}{n_{in}+n_{out}}})
$$
范围内的均匀分面，而它的方差根据公式

$$
D(x)=(max-min)^2
$$
刚好等于
$$
\frac{2}{n_{in}+n_{out}}
$$
例子：一个xavier的初始化器
```python
def xavier_init(fan_in,fan_out,constant=1):
    low=-constant*np.sqrt(6.0/(fan_in+fan_out))
    high=constant*np.sqrt(6.0/(fan_in+fan_out))
    return tf.random_uniform((fan_in,fan_out),minval=low,maxval=high,dtype=tf.float32)
```
### tf.random_normal

从正态分布中输出随机值。
参数:
    shape: 一维的张量，也是输出的张量。
    mean: 正态分布的均值。
    stddev: 正态分布的标准差。
    dtype: 输出的类型。
    seed: 一个整数，当设置之后，每次生成的随机数都一样。
    name: 操作的名字。

```python
tf.random_normal([2,3], mean=0.0, stddev=1.0, dtype=tf.float32, seed=1234, name="v8_2")
```



## 规范化

### tf.variable_scope
```python
tf.ariable_scope(name_or_scope, reuse=None, initializer=None,
                   regularizer=None, caching_device=None, partitioner=None,
                   custom_getter=None)
```
为变量添加命名域

### tf.get_variable_scope
返回当前变量的命名域，返回一个tensorflow.Python.ops.variable_scope.VariableScope变量。

## 算术操作
| 操作                          | 描述                                       |
| :-------------------------- | :--------------------------------------- |
| tf.add(x, y, name=None)     | 求和                                       |
| tf.sub(x, y, name=None)     | 减法                                       |
| tf.mul(x, y, name=None)     | 乘法                                       |
| tf.div(x, y, name=None)     | 除法                                       |
| tf.mod(x, y, name=None)     | 取模                                       |
| tf.abs(x, name=None)        | 求绝对值                                     |
| tf.neg(x, name=None)        | 取负 (y = -x).                             |
| tf.sign(x, name=None)       | 返回符号 y = sign(x) = -1 if x < 0; 0 if x == 0; 1 if x > 0. |
| tf.inv(x, name=None)        | 取反                                       |
| tf.square(x, name=None)     | 计算平方 $$(y = x * x = x^2)$$               |
| tf.round(x, name=None)      | 舍入最接近的整数#‘a’ is [0.9, 2.5, 2.3, -4.4], tf.round(a) ==> [ 1.0, 3.0, 2.0, -4.0 ] |
| tf.sqrt(x, name=None)       | 开根号 $$ (y = \sqrt{x} = x^{1/2})$$        |
| tf.pow(x, y, name=None)     | 幂次方#tensor ‘x’ is [[2, 2], [3, 3]],#tensor ‘y’ is [[8, 16], [2, 3]],tf.pow(x, y) ==> [[256, 65536], [9, 27]] |
| tf.exp(x, name=None)        | 计算e的次方                                   |
| tf.log(x, name=None)        | 计算log，一个输入计算e的ln，两输入以第二输入为底              |
| tf.maximum(x, y, name=None) | 返回最大值 (x > y ? x : y)                    |
| tf.minimum(x, y, name=None) | 返回最小值 (x < y ? x : y)                    |
| tf.cos(x, name=None)        | 三角函数cosine                               |
| tf.sin(x, name=None)        | 三角函数sine                                 |
| tf.tan(x, name=None)        | 三角函数tan                                  |
| tf.atan(x, name=None)       | 三角函数ctan                                 |
## 张量操作Tensor Transformations
* 数据类型转换Casting

| 操作                                       | 描述                                       |
| :--------------------------------------- | :--------------------------------------- |
| tf.string_to_number(string_tensor, out_type=None, name=None) | 字符串转为数字                                  |
| tf.to_double(x, name=’ToDouble’)         | 转为64位浮点类型–float64                        |
| tf.to_float(x, name=’ToFloat’)           | 转为32位浮点类型–float32                        |
| tf.to_int32(x, name=’ToInt32’)           | 转为32位整型–int32                            |
| tf.to_int64(x, name=’ToInt64’)           | 转为64位整型–int64                            |
| tf.cast(x, dtype, name=None)             | 将x或者x.values转换为dtype#tensor 'a' is [1.8, 2.2], dtype=tf.float,tf.cast(a, tf.int32) ==> [1, 2] # dtype=tf.int32 |

* 形状操作Shape and shaping

### tf.shape

```python
tf.shape(input, name=None)
```

返回数据的shape
```python
#‘t’ is [[[1, 1, 1], [2, 2, 2]], [[3, 3, 3], [4, 4, 4]]]
shape(t) ==> [2, 2, 3]
```
### tf.size
返回数据的元素数量

```python
tf.size(input, name=None)
```

```python
#‘t’ is [[[1, 1, 1], [2, 2, 2]], [[3, 3, 3], [4, 4, 4]]]]
size(t) ==> 12
```
### tf.rank

```python
tf.rank(input, name=None)
```

返回tensor的rank,注意：此rank不同于矩阵的ranktensor的rank表示一个tensor需要的索引数目来唯一表示任何一个元素也就是通常所说的 “order”, “degree”或”ndims”
```python
#’t’ is [[[1, 1, 1], [2, 2, 2]], [[3, 3, 3], [4, 4, 4]]]
# shape of tensor ‘t’ is [2, 2, 3]
rank(t) ==> 3
```
### tf.reshape

```python
tf.reshape(tensor, shape, name=None)
```

改变tensor的形状
```python
#tensor ‘t’ is [1, 2, 3, 4, 5, 6, 7, 8, 9]
#tensor ‘t’ has shape [9]
reshape(t, [3, 3]) ==>
[[1, 2, 3],
[4, 5, 6],
[7, 8, 9]]
```
如果shape有元素[-1],表示在该维度打平至一维#-1 将自动推导得为 9:
```python
reshape(t, [2, -1]) ==>
[[1, 1, 1, 2, 2, 2, 3, 3, 3],
[4, 4, 4, 5, 5, 5, 6, 6, 6]]
```
### tf.expand_dims

```python
tf.expand_dims(input, dim, name=None)
```

插入维度1进入一个tensor中该操作要求-1-input.dims()
```python
#‘t’ is a tensor of shape [2]shape(expand_dims(t, 0)) ==> [1, 2]
shape(expand_dims(t, 1)) ==> [2, 1]
shape(expand_dims(t, -1)) ==> [2, 1] <= dim <= input.dims()
```
*   切片与合并（Slicing and Joining）

### tf.slice

```python
tf.slice(input_, begin, size, name=None)
```

对tensor进行切片操作其中size[i] = input.dim_size(i) - begin[i]
该操作要求 0 <= begin[i] <= begin[i] + size[i] <= Di for i in [0, n]
```python
#’input’ is #[[[1, 1, 1], [2, 2, 2]],[[3, 3, 3], [4, 4, 4]],[[5, 5, 5], [6, 6, 6]]]
 tf.slice(input, [1, 0, 0], [1, 1, 3]) ==> [[[3, 3, 3]]]
 tf.slice(input, [1, 0, 0], [1, 2, 3]) ==> 
[[[3, 3, 3]
[4, 4, 4]]] 
tf.slice(input, [1, 0, 0], [2, 1, 3]) ==> 
[[[3, 3, 3]],
[[5, 5, 5]]] 
```
### tf.split 

```python
tf.split(split_dim, num_split, value, name=’split’)
```

沿着某一维度将tensor分离为num_split tensors
```python
#‘value’ is a tensor with shape [5, 30]
#Split ‘value’ into 3 tensors along dimension 1
split0, split1, split2 = tf.split(1, 3, value)
tf.shape(split0) ==> [5, 10]
```
### tf.concat 

```python
tf.concat(concat_dim, values, name=’concat’)
```

沿着某一维度连结tensor
```python
t1 = [[1, 2, 3], [4, 5, 6]]
t2 = [[7, 8, 9], [10, 11, 12]]
tf.concat(0, [t1, t2]) ==> [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10, 11, 12]]
tf.concat(1, [t1, t2]) ==> [[1, 2, 3, 7, 8, 9], [4, 5, 6, 10, 11, 12]]
```
如果想沿着tensor一新轴连结打包,那么可以：

```python
tf.concat(axis, [tf.expand_dims(t, axis) for t in tensors])
```

等同于tf.pack(tensors, axis=axis)

### tf.pack 

```python
tf.pack(values,axis=0,name='pack')
```

将一系列rank-R的tensor打包为一个rank-(R+1)的tensor
```python
#‘x’ is [1, 4], ‘y’ is [2, 5], ‘z’ is [3, 6]
pack([x, y, z]) => [[1, 4], [2, 5], [3, 6]] 
#沿着第一维stack
stack([x, y, z], axis=1) => [[1, 2, 3], [4, 5, 6]]
等价于tf.stack([x, y, z]) = np.asarray([x, y, z])
```
### tf.reverse 

```python
tf.reverse(tensor, dims, name=None)
```

沿着某维度进行序列反转其中dim为列表，元素为bool型，size等于rank(tensor)
```python
#tensor ‘t’ is 
[[[[ 0, 1, 2, 3],
#[ 4, 5, 6, 7],
#[ 8, 9, 10, 11]],
#[[12, 13, 14, 15],
#[16, 17, 18, 19],
#[20, 21, 22, 23]]]]
#tensor ‘t’ shape is [1, 2, 3, 4]
#‘dims’ is [False, False, False, True]
reverse(t, dims) ==>
[[[[ 3, 2, 1, 0],
[ 7, 6, 5, 4],
[ 11, 10, 9, 8]],
[[15, 14, 13, 12],
[19, 18, 17, 16],
[23, 22, 21, 20]]]]
```
### tf.transpose 

```python
tf.transpose(a, perm=None, name=’transpose’)
```

调换tensor的维度顺序按照列表perm的维度排列调换tensor顺序，如为定义，则perm为(n-1…0)
```python
#‘x’ is [[1 2 3],[4 5 6]]
tf.transpose(x) ==> [[1 4], [2 5],[3 6]]
#Equivalently
tf.transpose(x, perm=[1, 0]) ==> [[1 4],[2 5], [3 6]]
```
### tf.gather 

```python
tf.gather(params, indices, validate_indices=None, name=None)
```

合并索引indices所指示params中的切片
![tf.gather](http://img.blog.csdn.net/20160808174705034) 
### tf.one_hot 

```python
tf.one_hot(indices, depth, on_value=None, off_value=None, axis=None, dtype=None, name=None)
```

```python
indices = [0, 2, -1, 1]
depth = 3
on_value = 5.0 
off_value = 0.0 
axis = -1 
#Then output is [4 x 3]: 
output = 
[5.0 0.0 0.0] // one_hot(0) 
[0.0 0.0 5.0] // one_hot(2) 
[0.0 0.0 0.0] // one_hot(-1) 
[0.0 5.0 0.0] // one_hot(1)
```
## 矩阵相关运算
### tf.diag

```python
tf.diag(diagonal,name=None)
```

返回一个给定对角值的对角tensor,也就是生成对角矩阵。
```python
#‘diagonal’ is [1, 2, 3, 4]
tf.diag(diagonal) ==> 
[[1, 0, 0, 0]
[0, 2, 0, 0]
[0, 0, 3, 0]
[0, 0, 0, 4]]
```
### tf.diag_part

```python
f.diag_part(input, name=None)
```

功能与上面tf.diag相反,返回对角矩阵的对角元素。
```python
diagonal =tf.constant([[1,0,0,0],[0,1,0,0],[0,0,1,0],[0,0,0,1]])  
with tf.Session() as sess:  
	print(sess.run(tf.diag_part(diagonal)))
#输出结果为[1,1,1,1]
```
### tf.trace

```python
tf.trace(x, name=None)
```

求一个2维tensor足迹，即对角值diagonal之和
### tf.transpose

```python
tf.transpose(a, perm=None, name=’transpose’)
```

调换tensor的维度顺序
按照列表perm的维度排列调换tensor顺序，
如为定义，则perm为(n-1…0)
```python
# ‘x’ is [[1 2 3],[4 5 6]]
tf.transpose(x) ==> [[1 4], [2 5],[3 6]]
# Equivalently
tf.transpose(x, perm=[1, 0]) ==> [[1 4],[2 5], [3 6]]
```
### tf.matmul

```python
tf.matmul(a, b, transpose_a=False, transpose_b=False, a_is_sparse=False, b_is_sparse=False, name=None)
```

矩阵相乘

### tf.matrix_determinant

```python
tf.matrix_determinant(input, name=None)
```

返回方阵的行列式

### tf.matrix_inverse

```python
tf.matrix_inverse(input, adjoint=None, name=None)
```

求方阵的逆矩阵，adjoint为True时，计算输入共轭矩阵的逆矩阵

### tf.cholesky

```python
tf.cholesky(input, name=None)
```

对输入方阵cholesky分解，即把一个对称正定的矩阵表示成一个下三角矩阵L和其转置的乘积的分解$$A=LL^T$$

### tf.matrix_solve

```python
tf.matrix_solve(matrix, rhs, adjoint=None, name=None)
```

求解tf.matrix_solve(matrix, rhs, adjoint=None, name=None)
matrix为方阵shape为[M,M],rhs的shape为[M,K]，output为[M,K]

## 复数操作
### tf.complex

```python
tf.complex(real, imag, name=None)
```

将两实数转换为复数形式
```python
# tensor ‘real’ is [2.25, 3.25]
# tensor `imag` is [4.75, 5.75]
tf.complex(real, imag) ==> [[2.25 + 4.75j], [3.25 + 5.75j]]
```
### tf.complex_abs

```python
tf.complex_abs(x, name=None)
```

计算复数的绝对值，即长度。
```python
# tensor ‘x’ is [[-2.25 + 4.75j], [-3.25 + 5.75j]]
tf.complex_abs(x) ==> [5.25594902, 6.60492229]
```
### tf.conj

```python
tf.conj(input, name=None)
```

计算共轭复数

### tf.imag

```python
tf.imag(input, name=None) tf.real(input, name=None)
```

提取复数的虚部和实部

### tf.fft

```python
tf.fft(input, name=None)
```

计算一维的离散傅里叶变换，输入数据类型为complex64

## 归约计算
### tf.reduce_sum 

```python
tf.reduce_sum(input_tensor, reduction_indices=None, keep_dims=False, name=None)
```

计算输入tensor元素的和，或者安照reduction_indices指定的轴进行求和
```python
# ‘x’ is [[1, 1, 1]
# [1, 1, 1]]
tf.reduce_sum(x) ==> 6
tf.reduce_sum(x, 0) ==> [2, 2, 2]
tf.reduce_sum(x, 1) ==> [3, 3]
tf.reduce_sum(x, 1, keep_dims=True) ==> [[3], [3]]
tf.reduce_sum(x, [0, 1]) ==> 6 
```
### tf.reduce_prod 
```python
tf.reduce_prod(input_tensor,reduction_indices=None, keep_dims=False, name=None)
```

计算输入tensor元素的乘积，或者安照reduction_indices指定的轴进行求乘积 

### tf.reduce_min

```python
tf.reduce_min(input_tensor, reduction_indices=None, keep_dims=False, name=None) 
```

求tensor中最小值

### tf.reduce_max 

```python
tf.reduce_max(input_tensor, reduction_indices=None, keep_dims=False, name=None)
```

求tensor中最大值 ,参数：

input_tensor：待求值 的tensor。

reduction_indices：在那一维上求解

### tf.reduce_mean 

```python
tf.reduce_mean(input_tensor, reduction_indices=None, keep_dims=False, name=None)
```

求tensor中平均值，参数：

input_tensor：待求值 的tensor。

reduction_indices：在那一维上求解

### tf.reduce_all

```python
tf.reduce_all(input_tensor, reduction_indices=None, keep_dims=False, name=None)
```

对tensor中各个元素求逻辑’与’
```python
# ‘x’ is 
# [[True, True]
# [False, False]]
tf.reduce_all(x) ==> False
tf.reduce_all(x, 0) ==> [False, False]
tf.reduce_all(x, 1) ==> [True, False]
```
### tf.reduce_any
```python
tf.reduce_any(input_tensor,reduction_indices=None, keep_dims=False, name=None)
```

对tensor中各个元素求逻辑’或’

### tf.accumulate_n 

```python
tf.accumulate_n(inputs, shape=None, tensor_dtype=None, name=None)
```

计算一系列tensor的和
```python
# tensor ‘a’ is [[1, 2], [3, 4]]
# tensor `b` is [[5, 0], [0, 6]]
tf.accumulate_n([a, b, a]) ==> [[7, 4], [6, 14]]
```
### tf.cumsum 

```python
tf.cumsum(x, axis=0, exclusive=False, reverse=False, name=None)
```

求累积和
```python
tf.cumsum([a, b, c]) ==> [a, a + b, a + b + c]
tf.cumsum([a, b, c], exclusive=True) ==> [0, a, a + b]
tf.cumsum([a, b, c], reverse=True) ==> [a + b + c, b + c, c]
tf.cumsum([a, b, c], exclusive=True, reverse=True) ==> [b + c, c, 0]
```
## 分割(segmentation)
### tf.segment_sum 

```python
tf.segment_sum(data, segment_ids, name=None)
```

根据segment_ids的分段计算各个片段的和
其中segment_ids为一个size与data第一维相同的tensor
其中id为int型数据，最大id不大于size
```python
c = tf.constant([[1,2,3,4], [-1,-2,-3,-4], [5,6,7,8]])
tf.segment_sum(c, tf.constant([0, 0, 1]))
==>[[0 0 0 0] 
[5 6 7 8]]
```
上面例子分为[0,1]两id,对相同id的data相应数据进行求和,
并放入结果的相应id中，
且segment_ids只升不降 

### tf.segment_prod 

```python
tf.segment_prod(data, segment_ids, name=None)
```

根据segment_ids的分段计算各个片段的积

### tf.segment_min 

```python
tf.segment_min(data, segment_ids, name=None)
```

根据segment_ids的分段计算各个片段的最小值

### tf.segment_max 

```python
tf.segment_max(data, segment_ids, name=None)
```

根据segment_ids的分段计算各个片段的最大值

### tf.segment_mean 

```python
tf.segment_mean(data, segment_ids, name=None)
```

根据segment_ids的分段计算各个片段的平均值 

### tf.unsorted_segment_sum 

```python
tf.unsorted_segment_sum(data, segment_ids,num_segments, name=None)
```

与tf.segment_sum函数类似，不同在于segment_ids中id顺序可以是无序的

### tf.sparse_segment_sum 

```python
tf.sparse_segment_sum(data, indices, segment_ids, name=None)
```

输入进行稀疏分割求和
```python
c = tf.constant([[1,2,3,4], [-1,-2,-3,-4], [5,6,7,8]])
# Select two rows, one segment.
tf.sparse_segment_sum(c, tf.constant([0, 1]), tf.constant([0, 0])) 
==> [[0 0 0 0]]
```
对原data的indices为[0,1]位置的进行分割，
并按照segment_ids的分组进行求和

## 序列比较与索引提取(Sequence Comparison and Indexing)
### tf.argmin 

```python
tf.argmin(input, dimension, name=None)
```

返回input最小值的索引index 

### tf.argmax 

```python
tf.argmax(input, dimension, name=None)
```

返回input最大值的索引index

### tf.listdiff 

```python
tf.listdiff(x, y, name=None)
```

返回x，y中不同值的索引

### tf.where 

```python
tf.where(input, name=None)
```

返回bool型tensor中为True的位置
```python
# ‘input’ tensor is 
#[[True, False]
#[True, False]]
# ‘input’ 有两个’True’,那么输出两个坐标值.
# ‘input’的rank为2, 所以每个坐标为具有两个维度.
where(input) ==>
[[0, 0],
[1, 0]]
```
### tf.unique

```python
tf.unique(x, name=None)
```

返回一个元组tuple(y,idx)，y为x的列表的唯一化数据列表，
idx为x数据对应y元素的index
```python
# tensor ‘x’ is [1, 1, 2, 4, 4, 4, 7, 8, 8]
y, idx = unique(x)
y ==> [1, 2, 4, 7, 8]
idx ==> [0, 0, 1, 2, 2, 2, 3, 4, 4]
```
### tf.invert_permutation 

```python
f.invert_permutation(x, name=None)
```

置换x数据与索引的关系
```python
# tensor `x` is [3, 4, 0, 2, 1]
invert_permutation(x) ==> [2, 4, 3, 0, 1]
```
## 激活函数

### tf.nn.relu

relu函数是目前用的最多也是最受欢迎的激活函数，公式和函数图像如下：

$$f(x)=max(x,0)$$

![img](http://img.blog.csdn.net/20170527202010332?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2FpY2FpYXRuYnU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

由上图的函数图像可以知道，relu在x<0时是硬饱和。由于当x>0时一阶导数为1。所以，relu函数在x>0时可以保持梯度不衰减，从而缓解梯度消失问题，还可以更快的去收敛。但是，随着训练的进行，部分输入会落到硬饱和区，导致对应的权重无法更新。我们称之为“神经元死亡”。

除了relu本身外，TensorFlow还定义了relu6，也就是定义在min(max(features, 0), 6)的tf.nn.relu6(features, name=None),以及crelu,也就是tf.nn.crelu(features, name=None).

### tf.nn.relu6

```python
tf.nn.relu6(features,name=None)
```

以6为阈值的整流函数：min(max(features, 0), 6)

### tf.nn.sigmoid

这是传统神经网络中最常用的激活函数之一，公式和函数图像如下：
$$
s(x)=\frac{1}{1+e^{-x}}
$$
![img](http://img.blog.csdn.net/20170526193852228?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2FpY2FpYXRuYnU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

优点：它输出映射在(0,1)内，单调连续，非常适合用作输出层，并且求导比较容易；

缺点：具有软饱和性，一旦输入落入饱和区，一阶导数就变得接近于0，很容易产生梯度消失。

饱和性：当|x|>c时，其中c为某常数，此时一阶导数等于0，通俗的说一阶导数就是上图中的斜率，函数越来越水平。

### tf.nn.tanh

tanh也是传统神经网络中比较常用的激活函数，公式和函数图像如下：

$$tanh(x)=\frac{sinh x}{cosh x}=\frac{e^x-e^{-x}}{e^x+e^{-x}}=\frac{1-e^{-2x}}{1+e^{-2x}}$$

![img](http://img.blog.csdn.net/20170526193948259?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2FpY2FpYXRuYnU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

tanh函数也具有软饱和性。因为它的输出是以0为中心，收敛速度比sigmoid函数要快。但是仍然无法解决梯度消失问题。

### tf.nn.softplus

softplus函数可以看作是relu函数的平滑版本，公式和函数图像如下：

$$f(x)=log(1+e^x)$$

![img](http://img.blog.csdn.net/20170528003724503?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2FpY2FpYXRuYnU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### tf.nn.dropout

```python
tf.nn.dropout(x, keep_prob,noise_shape=None, seed=None, name=None)
```

计算dropout，keep_prob为keep概率noise_shape为噪声的shape。

### tf.nn.leakrelu

leakrelu函数是relu激活函数的改进版本，解决部分输入会落到硬饱和区，导致对应的权重无法更新的问题。公式和图像如下：

$$f(x)=max(x,leak*x)$$

![img](http://img.blog.csdn.net/20170702211001517?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2FpY2FpYXRuYnU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

左边缩小方差，右边保持方差；方差整体还是缩小的，而均值得不到保障。

### tf.nn.bias_add

```python
tf.nn.bias_add(value, bias, data_format=None, name=None)
```

对value加一偏置量
此函数为tf.add的特殊情况，bias仅为一维，
函数通过广播机制进行与value求和,
数据格式可以与value不同，返回为与value相同格式

### tf.nn.elu

leakrelu函数是relu激活函数的改进版本，解决部分输入会落到硬饱和区，导致对应的权重无法更新的问题。公式和图像如下：
$$
ELU(x)=
\begin{equation}
\left\{\begin{aligned}
x,  &&if x>0 \\
a(e^x-1), &&if x \le0 
\end{aligned}
\right.
\end{equation}
$$

![img](http://img.blog.csdn.net/20170702212504302?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2FpY2FpYXRuYnU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

左边缩小方差，右边保持方差；方差整体还是缩小的，而均值得不到保障。

### SELU

最近的自归一化网络中提出，函数和图像如下：
$$
SELU(x)=M
\begin{equation}
\left\{\begin{aligned}
x,& &if&x>0 \\
a(e^x-1)&, &if&x\le 0
\end{aligned}
\right.
且M>1
\end{equation}
$$
蓝色是：selu，橙色是：elu
![img](http://img.blog.csdn.net/20170702214956820?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2FpY2FpYXRuYnU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

左边缩小方差，右边放大方差，适当选取参数alpha和lambda，使得整体上保持方差与期望。如果选取：

lambda=1.0506，alpha=1.67326，那么可以验证如果输入的x是服从标准正态分布，那么SELU(x)的期望为0，方差为1.

## 卷积

### tf.nn.conv2d
给定4维的input和filter,计算出一个2维的卷积结果
```python
tf.nn.conv2d(input, filter, strides, padding,use_cudnn_on_gpu=None,data_format=None, name=None)
```
* **input**：待卷积的数据。格式要求为一个张量，[batch, in_height, in_width, in_channels]. 
  分别表示 批次数，图像高度，宽度，输入通道数。

* **filter**： 卷积核。格式要求为[filter_height, filter_width, in_channels, out_channels]. 分别表示 卷积核的高度，宽度，输入通道数，输出通道数。

* **strides** :一个长为4的list. 表示每次卷积以后卷积窗口在input中滑动的距离

* **padding** ：有SAME和VALID两种选项，表示是否要保留图像边上那一圈不完全卷积的部分。如果是SAME，则保留

* **use_cudnn_on_gpu** ：是否使用cudnn加速。默认是True
### tf.nn.convolution
### tf.nn.depthwise_conv2d
### tf.nn.separable_conv2d
### tf.nn.atrous_conv2d
### tf.nn.conv2d_transpose
### tf.nn.conv1d
### tf.nn.conv3d

在给定的5D input与 filter下计算3D卷积
输入shape为[batch, in_depth, in_height, in_width, in_channels]

### tf.nn.conv3d_transpose

## 池化
### tf.nn.avg_pool
```python
tf.nn.avg_pool(value,ksize,strides,padding,data_format='NHWC',name=None)
```
计算池化区域中元素的平均值

### tf.nn.max_pool
进行最大值池化操作，在每个区域中寻找最大值。而avg_pool则进行平均值池化操作
```python
tf.nn.max_pool(value, ksize, strides, padding, data_format="NHWC", name=None)
```

![max pool](http://images2015.cnblogs.com/blog/1062917/201611/1062917-20161117212026498-272435652.png)
* **value**: 一个4D张量，格式为[batch, height, width, ]，与conv2d中input格式一样 
* **ksize**: 长为4的list,表示池化窗口的尺寸 
* **strides**: 池化窗口的滑动值，与conv2d中的一样 
* **padding**: 与conv2d中用法一样。

### tf.nn.max_pool_with_argmax

```python
tf.nn.max_pool_with_argmax(input, ksize, strides,padding, Targmax=None, name=None)
```

返回一个二维元组(output,argmax),最大值pooling，返回最大值及其相应的索引

### tf.nn.embedding_lookup
```python
embedding_lookup(params, ids, partition_strategy=”mod”, name=None, validate_indices=True)
```
简单的来讲，就是将一个数字序列ids转化成embedding序列表示。 
假设params.shape=[v,h], ids.shape=[m], 那么该函数会返回一个shape=[m,h]的张量。

### tf.trainable_variables
返回的是需要训练的变量列表在创造变量(tf.Variable, tf.get_variable 等操作)时，都会有一个trainable的选项，表示该变量是否可训练。这个函数会返回图中所有trainable=True的变量。
### tf.gradients
用来计算导数，该函数的定义如下:
```python
tf.gradients(ys,
              xs,
              grad_ys=None,
              name="gradients",
              colocate_gradients_with_ops=False,
              gate_gradients=False,
              aggregation_method=None)
```
虽然参数很多，但是最常用的还是ys和xs。根据说明得知，ys和xs都可以是一个tensor或者tensor列表。而计算完成以后，该函数会返回一个长为len(xs)的tensor列表，列表中的每个tensor是ys中每个值对xs[i]求导之和。如果用数学公式表示的话，那么 `g = tf.gradients(y,x)`可以表示成gi=∑j=0len(y)∂yj∂xig=[g0,g1,...,glen(x)]
### tf.clip_by_global_norm
修正梯度值，用于**控制梯度爆炸的问题**。梯度爆炸和梯度弥散的原因一样，都是因为链式法则求导的关系，导致梯度的指数级衰减。为了避免梯度爆炸，需要对梯度进行修剪。 
先来看这个函数的定义：
```python
tf.clip_by_global_norm(t_list, clip_norm, use_norm=None, name=None)
```
**输入参数中**：t_list为待修剪的张量, clip_norm 表示修剪比例(clipping ratio).

**函数返回2个参数**： list_clipped，修剪后的张量，以及global_norm，一个中间计算量。当然如果你之前已经计算出了global_norm值，你可以在use_norm选项直接指定global_norm的值。

那么具体如何计算呢？根据源码中的说明，可以得到 
list_clipped[i]=t_list[i] * clip_norm / max(global_norm, clip_norm),其中 
global_norm = sqrt(sum([l2norm(t)**2 for t in t_list]))

如果你更熟悉数学公式，则可以写作 

Lic=Lit∗Ncmax(Nc,Ng)Ng=∑i(Lit)2

其中， 
Lic和Lig代表t_list[i]和list_clipped[i]， 
Nc和Ng代表clip_norm 和 global_norm的值。 
其实也可以看到其实Ng就是t_list的L2模。上式也可以进一步写作 

Lic={Lit,(Ng<=Nc)Lit∗NcNg,(Ng>Nc)Ng=∑i(Lit)2

 也就是说，当t_list的L2模大于指定的Nc时，就会对t_list做等比例缩放
### tf.nn.dropout
```python
tf.nn.dropout(x, keep_prob, noise_shape=None, seed=None, name=None)
```
按概率来将x中的一些元素值置零，并将其他的值放大。用于进行dropout操作，一定程度上可以防止过拟合 
x是一个张量，而keep_prob是一个（0,1]之间的值。x中的各个元素清零的概率互相独立，为1-keep_prob,而没有清零的元素，则会统一乘以1/keep_prob, 目的是为了保持x的整体期望值不变。

## 优化

### tf.train.GradientDescentOptimizer

### tf.train.AdadeltaOptimizer

### tf.train.AdagradOptimizer

### tf.train.AdagradDAOptimizer

### tf.train.MomentumOptimizer

### tf.train.AdamOptimizer

```python
tf.train.AdamOptimizer.__init__(learning_rate=0.001, beta1=0.9, beta2=0.999, epsilon=1e-08, use_locking=False, name=’Adam’)
```

Adam算法的优化器

### tf.train.FtrlOptimizer

### tf.train.ProximalGradientDescentOptimizer

### tf.train.ProximalAdagradOptimizer

### tf.train.RMSPropOptimizer

## 交叉熵

### tf.nn.softmax_cross_entropy_with_logits

$$H_{y'}(y) = -\sum_i y'_i \log(y_i)$$

## 可视化

### tf.summary.FileWriter

```python
writer = tf.summary.FileWriter('./graph', sess.graph)
```

能够创建一个文件写入器，./graph是存储目录，sess.graph表示读入的图结构。

## tf.image
---
### tf.image.random_flip_left_right
随机水平翻转
### tf.image.random_contrast(distorted_image,lower=0.2, upper=1.8)
随机对比度

### tf.image.random_brightness(distorted_image,max_delta=63)
随机亮度

### tf.image.per_image_standardization(distorted_image)
对数据进行标准化，对数据减去均值，除以方差，保证数据零均值，方差为1



