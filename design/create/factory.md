- 简单工厂 一个工厂生产不同的产品

    - 工厂角色: 所有产品创建
    - 抽象产品角色:约定产品需要具备的用途 接口
    - 具体产品角色(多个):针对自己实现使用方法
    - 调用角色:调用工厂方法生成产品,使用产品

    

```python
from abc import abstractmethod, ABC

# 工厂方法 根据不同的name生成不同的产品
def factory(name):
    if name == "A":
        return ProductA()
    elif name == "B":
        return ProductB()

# 抽象产品
class AbsProduct(ABC):
    @abstractmethod
    def function(self):
        pass

# 产品A
class ProductA(AbsProduct):
    def function(self):
        return "Use ProductA"

# 产品B
class ProductB(AbsProduct):
    def function(self):
        return "Use ProductB"

# 使用者
def client():
    res = factory("A")
    print(res.function())

client()
```

- 工厂方法模式 不同的工厂生产对应的产品 将创建具体产品的逻辑放到具体工厂中
    - 抽象工厂
    - 具体工厂
    - 抽象产品
    - 具体产品
    - 使用者

```python

```

- 抽象工厂模式 增加产品等级 产品族的概念 每个工厂可以生产不同的产品

