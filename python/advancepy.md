# Python 中一切皆是对象

- 一切皆是对象
  - 动态语言和静态语言的区别：python的面向对象更加彻底
  - **函数和类也是对象，属于python的一等公民**
    - **赋值给一个变量**
    - **可以添加到集合对象中**
    - **可以作为参数传递给函数**
    - **可以当做函数的返回值**
- type/object/class的关系
  - type > class > obj
  - 所有类继承自object

```python
# python shell >>>
a = 1
type(a)
type(int)

class A:
  pass
a = A()
type(a)
type(A)
A.__base__

int.__bases__
object.__bases__
type(object)
```

![image-20200116211633800](../pic/image-20200116211633800.png)

- python中常见内置类型

  - 对象的三个特征

    - 身份 id()
    - 类型
    - 值

  - None 全局只有一个

  - 数值 int/float/complex/bool

  - 迭代类型 

  - 序列类型 list/tuple/str/array/range/bytes bytearray memoryview(二级制序列)

  - 映射 dict

  - 集合 set/frozenset

  - 上下文管理类型 with

  - 其他 实现一些特定协议的对象就成为一种特殊类型

    - 模块类型
    - class 和 实例
    - 函数类型
    - 方法类型
    - 代码类型
    - object对象
    - type类型
    - ellipsis类型 ...
    - notimplemented类型

    

# 魔法方法

- 什么是魔法方法

```python
# for 循环调用__getitem__方法
class A:
    def __init__(self, a):
        self.a = a

    def __getitem__(self, item):
        print('item')
        return self.a[item]

a = A([1, 2, 3, 4, 6])

for i in a:
    print(i)

```



- python的数据模型以及数据模型对python的影响

  - 魔法函数可以用于支持一些python语法或函数

- 魔法函数一览

  - 非数学运算

    - 字符串表示 `__repr__,__str__`
    - 集合序列相关 `__len__,__getitem__,__setitem__,__delitem__,__contains__`
    - 迭代相关 `__iter__, __next__`
    - 可调用 `__call__`
    - With 上下文管理器 `__enter__, __exit__`
    - 数值转换 `__abs__, __bool__, __int__, __float__, __hash__, __index__, `
    - 元类相关 `__new__, __init__,`
    - 属性相关 `__getattr__, __setattr__, __getattribute__, __setattribute__, __dir__`

    - 属性描述符 `__get__, __set__, __delete__`
    - 协程 `__await__, __aiter__, __anext__, __aenter__, __aexit__`

  - 数学运算
```python
# 一元运算符
__neg__, __pos__, __abs__
# 二元运算符
__lt__, __le__, __eq__, __ne__, __gt__, __ge__
# 算数运算符
__add__, __sub__
# 反向算数运算符
# 增量赋值算数运算符
# 位运算符
# 反向位运算符
# 增量赋值运算符
```

![](../pic/pic2020011609.png)

- len()
    - python 默认类型取len效率高，长度维护好了的

# 深入类和对象

- 鸭子类型和多态
    - 鸭子类型在[程序设计](https://wiki.hk.wjbk.site/baike-程序设计)中是[动态类型](https://wiki.hk.wjbk.site/baike-類型系統)的一种风格。在这种风格中，一个对象有效的语义，不是由继承自特定的类或实现特定的接口，而是由"当前[方法](https://wiki.hk.wjbk.site/baike-方法_(電腦科學))和属性的集合"决定。
    - 当看到一只鸟走起来像鸭子、游泳起来像鸭子、叫起来也像鸭子，那么这只鸟就可以被称为鸭子
    - [https://wiki.hk.wjbk.site/wiki/%E9%B8%AD%E5%AD%90%E7%B1%BB%E5%9E%8B](https://wiki.hk.wjbk.site/wiki/鸭子类型)
    - 不同类实现了相同的方法，就可以当做一种类型处理。
    - python的多态，鸭子类型在不使用继承的情况下使用了多态
    - 鸭子类型不是一种类型，与其对应的其他类型系统，结构类型系统，接口，模板或泛型



- 抽象基类abc模块，不能实例化
    - 继承抽象基类，必须实现指定方法
    - 可以做类型检查isinstance(a, AbsType)

```python
# 一个简单的抽象基类 调用时才能抛出异常
class AbsClass():
  	def get(self):
    		raise NotImplementedError

# 初始化时就抛出异常
import abc
class AbsClass(metaclass=abc.ABCMeta):
    @abc.abstractmethod
    def get(self):
        pass
```



- 使用isinstance而不是type
- 类变量和对象变量
- 类属性和实例属性以及查找顺序
- 静态方法，类方法和对象方法以及参数
    - 静态方法 不会将调用者传到方法里，将方法的命名空间放到类里
    - 类方法，cls 将类对象传进方法的第一个位置
- 数据封装和私有属性

- Python 对象的自省机制
    - 通过一定的机制查询到对象的内部结构

```python
# 使用.__dict__/dir()获取属性 dir比较强大，但是只能获取属性名称，不能获取值
# 类比实例有更多的内容
class User:
  	def __init__(self):
      	self.name = "abc"
        
User.__dict__
u = User()
u.__dict__
dir(u)
l = [1]
dir(l)
l.__dict__ # 异常
```

- super函数
    - 调用父类的方法
    - mro继承顺序
- drf中对多继承使用的经验 mixin
- with语句
    - 实现魔法方法 
- contextlib实现上下文管理器



# 自定义序列类

- 序列类型的分类
    - 容器序列（嵌套） list、tuple、duque
    - 扁平序列 str、bytes、bytearray、array.array
    - 可变序列 list、deque、bytearray、array
    - 不可变序列 str、tuple、bytes
- 序列的abc继承关系
- 序列的+ += extend的区别
- 实现可切片的对象
- bisect管理可排序序列
- 什么时候不应该使用列表
- 列表推导式，生成器表达式，字典推导式