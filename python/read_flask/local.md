```python
class Local(object):
    __slots__ = ("__storage__", "__ident_func__")

    def __init__(self):
        object.__setattr__(self, "__storage__", {})  # 用一个字典存储所有东东
        object.__setattr__(self, "__ident_func__", get_ident)  # 获取线程id的方法

    def __release_local__(self):
        self.__storage__.pop(self.__ident_func__(), None)

    def __call__(self, proxy):
        """Create a proxy for a name."""
        return LocalProxy(self, proxy)

    def __getattr__(self, name): # local 对象获取属性的方法 set 和 del 同理
        try:
            return self.__storage__[self.__ident_func__()][name]  # 获取local对象的属性其实是从__storage__字典中拿线程id的值的属性 所以线程安全
        except KeyError:
            raise AttributeError(name)

```

```python
class LocalStack(object):
	# 在local对象的storage中创建一个{"stack": []},使用push,pop方法, local.__storage__ = {"ident": {"stack": []}}
    def push(self, obj):
    """Pushes a new item to the stack"""
        rv = getattr(self._local, "stack", None)
        if rv is None:
            self._local.stack = rv = []
        rv.append(obj)
        return rv

    def __call__(self):
        def _lookup():
            rv = self.top
            if rv is None:
                raise RuntimeError("object unbound")
            return rv

        return LocalProxy(_lookup)
```


```python
class LocalProxy(object)
    def __init__(self, local, name=None):
        object.__setattr__(self, "_LocalProxy__local", local)  
        # python的private属性, self.__private = "private" 实际添加的是 _类名__private 属性,外部调用者通过instance.__private无法访问到该属性, 在对象内部可以通过self.__private 访问,此处使用父类的__setattr__方法(避免赋值递归), 所以属性名称需要加上类名.
        # 用法一,传一个local对象和一个属性名称来创建代理, 那么就通过getattr(local, name) 来获取属性的值. (有relase_local方法说明是个local对象)
        # 用法二,传一个访问local对象属性的方法来创建代理(flask的用法), 那么就直接调用该方法获取属性的值. 方法存在__wrapped__.
        object.__setattr__(self, "__name__", name)
        if callable(local) and not hasattr(local, "__release_local__"):
            # "local" is a callable that is not an instance of Local or
            # LocalManager: mark it as a wrapped function.
            object.__setattr__(self, "__wrapped__", local)

    def _get_current_object(self):
        """Return the current object.  This is useful if you want the real
        object behind the proxy at a time for performance reasons or because
        you want to pass the object into a different context.
        """
        if not hasattr(self.__local, "__release_local__"):
            return self.__local()
        try:
            return getattr(self.__local, self.__name__)
        except AttributeError:
            raise RuntimeError("no object bound to %s" % self.__name__)
```


```python
from werkzeug.local import LocalProxy, Local, LocalStack

class RequestCtx:
    pass

local = Local()
local.request = RequestCtx()
a = local("request")

print(a)
print(type(a))
print("==================")

stack = LocalStack()
stack.push(RequestCtx())

b = LocalProxy(lambda: stack.top)
print(b)
print(type(b))
```