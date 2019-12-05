# Python 魔法方法

```python
# __setitem__
class Foo(object):
    def __init__(self):
        self.name = "boo"
    def __getitem__(self, item):
        print("调用__getitem__方法了")
        if item in self.__dict__:
            return self.__dict__[item]
    def __setitem__(self, key, value):
        print("调用__setitem__方法了")
        self.__dict__[key] = value
    def __delitem__(self, key):
        print("调用__delitem__方法了")
        del self.__dict__[key]

f = Foo()
ret = f['name']
f['mmm'] = print
del f['mmm']
print(ret)

```

