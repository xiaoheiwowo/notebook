## pip 私有仓库

```sh
# 创建pip服务器
pipserver
http://172.27.106.3:8002/packages/
# 配置源
vim .pip/pip.conf
[global]
index-url = http://172.27.106.3:8002/
trusted-host = 172.27.106.3
# 打包上传
python3 setup.py sdist upload -r <server-url>
server-url=http://221.238.157.245:8002
python3 setup.py sdist upload -r http://pip.idaka.vip:8002
# 安装
pip install <包名>
```


## requests

在通过requests.post()进行POST请求时，传入报文的参数有两个，一个是data，一个是json。
data与json既可以是str类型，也可以是dict类型。

- 区别：
1、不管json是str还是dict，如果不指定headers中的content-type，默认为application/json
2、data为dict时，如果不指定content-type，默认为application/x-www-form-urlencoded，相当于普通form表单提交的形式
3、data为str时，如果不指定content-type，默认为application/json
4、用data参数提交数据时，request.body的内容则为a=1&b=2的这种形式，用json参数提交数据时，request.body的内容则为'{"a": 1, "b": 2}'的这种形式

## type方法创建类

```python
# type(类名, 父类的元组（针对继承的情况，可以为空），包含属性的字典（名称和值）)
# type(class_name, base_class_tuple, attr_func_dict) -> class
class A(B):  	
    name = 'a'    
    def f(self):      	
        print(self.name)
# 用type声明类
def f(self):  	
    print(self.name)

A = type('A', (B,), {'name':'a', 'f': f})
```

## 数值方法

```python
divmod(x,y)  # 返回x/y的整数商和余数round(x,n) # 将x保留n位小数
```

## 列表方法

```python
# map
li = list(map(int, input().strip().split()))
>>> 34  6 6 7 8 8 99 9 990 
li = [34,  6, 6, 7, 8, 8, 99, 9, 990]
# zip
list(zip([1,2,3],[4,5,6]))
>>> [(1, 4), (2, 5), (3, 6)]
# filter
print(list(filter(lambda x: x % 2 == 1,[1, 2, 3, 4, 5, 6, 7, 8, 9, 10])))
>>> [1, 3, 5, 7, 9]
# slice
myslice = slice(5) # 设置截取5个元素的切片a
# 打乱列表顺序
random.shuffle(list(range(10)))
# 拼接
','.join(list)
```

## 字符串方法

```python
ord() / chr() ASCI 字符互转

# 分割 str.partition/rpartition(str1) -> (str0, str1, str2)
s = "http://www.baidu.com"
host = s.partition("//")[-1]

```

## 字典方法


```python
# 字典常用方法
for k, v in dict.items():
  print(k, v)
dict.pop('key')
dict.popitem()
dict.get('key', 'not found')

```
```python
# 目录结构
# 返回运行程序的目录到程序文件所在目录的路径
BASE_DIR = os.path.dirname(__file__) 
FILE_PATH = os.path.join(BASE_DIR, 'file')

sys.path.append(os.path.abspath('.'))
```

```python
# 装饰器
from functiontools import wraps # 修改__name__等属性

# login
def login_required(func):
    @wraps(func)
    def wrapper():
		pass
    return wrapper

# __wrapped__
@api.route('/index')
def index():
    pass
index.__wrapped__ 去掉一层装饰器
```

```python
# print
# \r\n：通常的回车
# \r：移到行首
# \n：移到下一行
print(end, flush)
print('\33[3A')

# print颜色  数值表示的参数含义：
# 显示方式: 0（默认值）、1（高亮）、22（非粗体）、4（下划线）、24（非下划线）、 5（闪烁）、25（非闪烁）、7（反显）、27（非反显）
# 前景色: 30（黑色）、31（红色）、32（绿色）、 33（黄色）、34（蓝色）、35（洋红）、36（青色）、37（白色）
# 背景色: 40（黑色）、41（红色）、42（绿色）、 43（黄色）、44（蓝色）、45（洋红）、46（青色）、47（白色）
print("\033[0;{color};40m{value}\033[0m".format(color=random.randint(31, 37), value=textStr[i // 16]), end="")
          
```

```python
a:="hello"
f"{a=}"
import datetime
time = datetime.datetime.now()
print(f'{time=!s}')
# time=2019-07-30 16:58:00.123412
import math
print(f'{math.pi=!f:.2f}')  # 精确到小数点后面两位

def f(pos1, pos2, /, pos_or_kwd, *, kwd1, kwd2):
    pass
```



```python
# 捕获异常修改提示信息再返回
try:
	from django.core.management import execute_from_command_line
except ImportError as exc:
    raise ImportError(
        "Couldn't import Django. Are you sure it's installed and "
        "available on your PYTHONPATH environment variable? Did you "
        "forget to activate a virtual environment?"
    ) from exc
```



```python
assert False, ValueError("assert")
# =>
if False:
    raise ValueError("assert")
```



dis 包 转换字节码



## nose nosetests

```shell
# 命令
nosetests  –h # 查看所有nose相关命令
nosetests –s	# 执行并捕获输出
nosetests –with-xunit	# 输出xml结果报告
nosetests -v: # 查看nose的运行信息和调试信息 
nosetests -w 	# 目录：指定一个目录运行测试
```



```
res = (true_res, false_res)[condition]
```



```
concurrent.futures.precess.ProcessPoolExecutor
```

