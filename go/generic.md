# 泛型



概念

泛型，参数化类型，类型模板



泛型约束

泛型类型

类型形参[列表]

类型实参

泛型实例化



语法

```go
// 类型
type Amount[T int64|float64|string] struct {
    Value T
}
// reciver
func (a *Amount[T]) Set(v T) {
    a.Value = v
}
// 函数
func Add[T int64|float32](a, b T) T {
	return a + b
}
// 接口
type DataProcessor2[T any] interface {
    int | ~struct{ Data interface{} }

    Process(data T) (newData T)
    Save(data T) error
}
```



base interface & generial



### 什么时候使用泛型？

- 需要使用slice, map, channel类型，但是slice, map, channel里的元素类型可能有多种。
- 通用的数据结构，比如链表，二叉树等。下面的代码实现了一个支持任意数据类型的二叉树。

- 当一个方法的实现对所有类型都一样。



### 什么时候不要使用泛型?

1. 只是单纯调用实参的方法时，不要用泛型。

```go
// good
func foo(w io.Writer) {
   b := getBytes()
   _, _ = w.Write(b)
}

// bad
func foo[T io.Writer](w T) {
   b := getBytes()
   _, _ = w.Write(b)
}
```

比如上面的例子，单纯是调用`io.Writer`的`Write`方法，把内容写到指定地方。使用`interface`作为参数更合适，可读性更强。

2. 当函数或者方法或者具体的实现逻辑，对于不同类型不一样时，不要用泛型。比如`encoding/json`这个包使用了`reflect`，如果用泛型反而不合适。

> [Go 1.18 泛型全面讲解：一篇讲清泛型的全部](https://segmentfault.com/a/1190000041634906)

