## 一、迭代器和生成器

1.使用列表推导构建笛卡尔积

```python
colors = ['black', 'white']
sizes = ['S', 'M', 'L']
tshirts = [(color, size) in for color in colors
          									for size in sizes]
```

2.使用生成器构建笛卡尔积

```python
colors = ['black', 'white']
sizes = ['S', 'M', 'L']
for tshirt in ('%s %s' % (c, s) for c in colors
               									for s in sizes)
```

- ​	列表推导和生成器的区别在于一是列表推导用[], 而生成器用().二则是生成器构建不会直接生成六个元素而是逐个产出

## 二、命名元组

`collections.namedtuple`是一个工厂函数，它可以用来构建一个带字段名的元组和一个有名字的类

```python
from collections import namedtuple
‘’‘
实现City类， 命名元组第二个参数可以是数个字符串组成的可迭代对象，或者是由空格分割开的字段名的字符串，代表这个类的个个字段名
’‘’
City = namedtuple('City', 'name country population coodinates')
```

## 三、处理字典中不存在的键

`dict`类有一个方法处理不存在的键`__missing__()`方法，这个方法会在调用`__getitem__()`时调用判断。而`__getitem__()`函数则是在调用`get()`或`dict[key]`是被调用。

一下是一个例子使用`__missing__()`函数来为一个StrKeyDict0类在获取一个键对应的值的时候对整数兼容的实现

```python
class StrKeyDict0(dict):
  #当我们使用dict[key]key不存在时，会调用__missing__()方法，而在这个类中我们使用这个方法对非字符串的key进行来兼容
  def __missing__(self, key):
    if isinstance(key, str):
      raise KeyError
    return self[str(key)]
  def get(self, key, default=None):
    try:
      return self[key]
    except KeyError:
      return default
    def __contains__(self, key):
      return key in self.keys() or str(key) in self.keys()
```

## 四、集合

`set`类型本身是unhashable的但是`frozenset`是hashable的。Python在实现`set`时重写了很多中缀运算符。对于两个`set`a和b来讲。`a | b`代表两个集合的合集， `a & b`表示交集，`a - b`表示差集。

## 五、文本和字节序列

1.编码和解码：通俗来讲，编码是把人类语言转换成计算机语言，解码是将计算机语言转化成人类语言。一般用`encoding=`和`decoding=`两个字段来表示编解码所用的标准

2.字节：

Python内置两种二进制字节类型：不可变的`bytes`类型和可变的`bytesarray`类型

## 六、将函数视为对象

1.`__dict__`参数：`__dict__`参数是关于对象的属性和值的一对键值对。这个例子表明它的作用：

```python
class Test():
    def __init__(self, val=0, num=0):
        self.val = val
        self.num = num

t = Test(1, 1)
print(t.__dict__)


output:{'val': 1, 'num': 1}
```

2.在Python中，函数是一等对象。一等对象满足以下几个条件

- 在运行时创建
- 能赋值给变量或者数据结构中的元素
- 能作为参数传递给函数
- 能作为函数的返回结果

## 七、装饰器和闭包

#### 一、装饰器简介

装饰器是可调用的对象（一般来说就是一个函数），它的参数是另一个函数（被装饰的函数）。

```python
@decorate
def target():
  print("running target()")

等效于

def target():
  print("running target()")
target = decorate(target)
#执行完后的target不是原来的target
```

函数装饰器在导入模块时立即执行



策略模式用装饰器改进

```python
promos = []

def promotion(promo_func):
  promos.append(promofunc)
  return promo_func

@promotion
def fidelity(order):
  pass

@promotion
def bulk_item(order):
  pass

@prmootion
def large_order(order):
  pass

def best_promo(order):
  return max(promo(order) for promo in promos)



#添加了promotion装饰器之后对每一个策略都使用，改进在于在新加入策略的时候会自动放入promos列表
```

上面的例子再次说明了装饰器只是参数为函数的一个函数，相当于为装饰的函数进行一些对象操作。

#### 二、闭包

晦涩难懂的定义：闭包指延伸了作用域的函数，其中包含函数定义体中引用、但是不在定义体中的非全局变量。函数是不是匿名的没有关系，关键是它能访问定义体之外的非全局变量。

通过以下代码理解

```python
#在此段代码中我们实现一个可以计算持续输入平均值的函数
#1.第一个实现
class Average():
  def __init__(self):
    self.nums = []
    
  def __call__(self, new_val):
    self.nums.append(new_val)
    return sum(self.nums) / len(self.nums)
  

#2.第二个实现

def make_average():
  nums = []
  
  def average(new_num):
    self.nums.append(new_num)
    return sum(self.nums) / len(nums)
  
  return average    
```



## 八、对象引用，可变性和垃圾回收

#### 一、对象引用

`is`和`==`的区别: `==`比较的是两个对象的值是否相同是根据`__eq__`函数定义的比较规则来比较的；而`is`比较的则是两个对象的标识即两个对象使用`id()`函数所获得的值是否一样

元组的相对不可变性：元组的相对不可变性是指不能通过给元组某个索引下标赋值的方式来改变元组，但是由于**元组内部是保存对象的引用**，所以引用对象改变时，元组就会改变。例如

```python
#test.py

a = [1, 2, 3]
t = (4, 5, a)
print(t) # output: (4, 5, [1, 2, 3])
a.append(6)
print(t) # output: (4, 5, [1, 2, 3, 6])
```



浅拷贝和深拷贝：浅拷贝拷贝的是对象的引用，而深拷贝给整个对象包含的对象一个新地址（区别在于副本共享不共享内部对象的引用）。例如

```python
#test.py
#浅拷贝示例
a = [1, [1,2,3], 4]
b = list(a) #相当于做浅拷贝
b[1].append(4)
print(a) # output: [1, [1, 2, 3, 4], 4]
```

```python
#test.py
#深拷贝示例
a = [1, [1, 2, 3], 4]
b = copy.deepcopy(a)
b[1].append(4)
print(a) #output: [1, [1, 2, 3], 4]
```

`copy`包下的`copy.deepcopy()`和`copy.copy()`函数分别是对参数进行深拷贝和浅拷贝



函数的传参：`Python`和`Java(引用类型)`的函数都采用共享传参模式。共享传参是指函数各个形式参数获得实参的引用。也就是说，函数有可能对可变对象进行不期望的改变

```python
#test.py

def test(a):
  a.append(10)
  
l = [1, 2]
test(l)

print(l) #output: [1, 2, 10]
```

#### 二、del和垃圾回收

在CPython中，垃圾回收使用的主要是引用计数算法，每个对象会统计有多少引用指向自己。当引用计数归零时，对象就立即销毁：CPython会在对象上调用`__del__`方法。



## 九、符合Python风格的对象

通过实现一个二维向量模型，涵盖以下主题：

- 支持用于生成对象其他表示形式的内置函数（如`__repr__`）
- 使用一个类方法实现备选构造方法
- 扩展内置的`format()`函数和`str.format()`方法使用的格式微语言
- 实现只读属性
- 把对象变成可散列的，可以作为`dict`的键
- 使用`__slots__`节省内存
- 如何以及何时使用`@classmethod`和`@staticmethod`装饰器
- Python的私有属性和受保护属性的用法、约定和局限

####  1.对象表示形式

`repr()`和`str()`方法是`__repr__`和`__str__`特殊方法提供的方法，前者以便于开发者理解的形式返回对象的字符串表示，后者以便于用户理解的方式返回对象的字符串表示。

```python
from array import array
import math

class Vector2d:
  # class attribute. 在实例和字节序之间的转换用
  typecode = 'd'
  
  def __init__(self, x, y):
    self.x = float(x)
    self.y = float(y)
    
  def __iter__(self):#定义__iter__方法， 把Vector2d实例变成可迭代对象，这样可以提供拆包
    return (i for i in (self.x, self.y))
  
  def __repr__(self):
    class_name = type(self).__name__
    return '{}({!r}, {!r})'.format(class_name, *self) #因为已经实现可迭代方法，所以{!r}会把self的各个分量获取
  
  def __str__(self):
    return str(tuple(self))
  
  def __bytes__(self):
    return (bytes([ord(self.typecode)]) + bytes(array(self.typecode, self)))
  
  def __eq__(self, other):
    return tuple(self) == tuple(other)
  
  def __abs__(self):
    return math.hypot(self.x, self.y) #对于一个向量来说模是两个方向的直角三角形斜边长
  
  def __bool__(self):
    return bool(abs(self))
    
```

