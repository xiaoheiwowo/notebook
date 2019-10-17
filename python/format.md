# format使用



**基本用法**

```python
"Hello {0}".format("Peppa Pig") # 引用第一个参数
# 输出 'Hello Peppa Pig'

"{} is cute".format("Peppa Pig") # 引用第一个参数
# 输出 'Peppa Pig is cute'

"My name is {name}".format(name="Peppa Pig") # 引用名字为name的参数
# 输出 'My name is Peppa Pig'
```

**类型转换**

```python
"Peppa pig is a cute {0!s}".format("baby") # !s 相当于对于参数调用str()
# 输出 'Peppa pig is a cute baby'

"Peppa pig is a cute {0!r}".format("baby") # !r 相当于对于参数调用repr()
# 输出 "Peppa pig is a cute 'baby'"
```

**format中进行进制转换**

```python
'{0} in HEX is {0:#x}'.format(16)
# 输出 '16 in HEX is 0x10'

'{0} is OCT is {0:#o}'.format(16)
# 输出 '16 is OCT is 0o20'
```

**对齐字符串**

```python
"{:>5}".format(1) # 设置宽度为5，右对齐
"{:>5}".format(10)
"{:>5}".format(100)
"{:>5}".format(1000)
# 输出下面的结果
'    1'
'   10'
'  100'
' 1000'

'{:_<10}'.format('test') # 左对齐，并且指定"_"填充空白部分
# 输出 'test______'

'{:_^10}'.format('test') # 居中对齐，并且指定"_"填充两侧
# 输出 '___test___'
# 注意：用%格式化字符串不支持居中对齐
```

**截断字符串**

```python
'{:.5}'.format('Hello Peppa') # 截取前5个字符
# 输出 'Hello'
```

**时间格式化**

```python
from datetime import datetime

'{:%Y-%m-%d %H:%M}'.format(datetime(2018, 5, 19, 21, 00))
# 输出 '2018-05-19 21:00'
# 注意：用%格式化字符串不支持此功能
```

**访问对象属性**

```python
class Pig(object):
    def __init__(self, name, age, weight):
        self.name = name
        self.age = age
        self.weight = weight

    def __repr__(self):
        return '<Pig name="{0}" age={1}>'.format(self.name, self.age)

a = Pig("Peppa", 5, 30)
print("Peppa is {0.age} years old, {0.weight} kilograms".format(a))

# 输出 Peppa is 5 years old, 30 kilograms
# 注意：用%格式化字符串不支持此功能
```

**访问元组中的元素**

```python
a = (1,2)
'X: {0[0]};  Y: {0[1]}'.format(a)
# 输出 'X: 1;  Y: 2'
# 注意：用%格式化字符串不支持此功能
```

**访问字典中的元素**

```python
peppa = {"name": "Peppa", "age": 5}
"My name is {pig[name]} and my age is {pig[age]}".format(pig=peppa)
# 输出 'My name is Peppa and my age is 5'
# 注意：用%格式化字符串不支持此功能
```

**参数指定格式**

format支持传入参数指定格式，如下：

```python
'{:{char}{align}{width}}'.format('test', char='_', align='^', width='10')
# 输出 '___test___'
# 注意：用%格式化字符串不支持此功能

from datetime import datetime
dt = datetime(2018, 5, 19, 21, 0)
'{:{dfmt} {tfmt}}'.format(dt, dfmt='%Y-%m-%d', tfmt='%H:%M')
# 输出 '2018-05-19 21:00'
# 注意：用%格式化字符串不支持此功能
```


# 3.6 新特性  f-string

```
http://www.mlln.cn/2018/05/19/python3%20f-string%E6%A0%BC%E5%BC%8F%E5%8C%96%E5%AD%97%E7%AC%A6%E4%B8%B2%E7%9A%84%E9%AB%98%E7%BA%A7%E7%94%A8%E6%B3%95/
```
