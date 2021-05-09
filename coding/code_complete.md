

# 使用变量的一般事项

## 数据认知测试

| 名称                |        | 分值 |
| ------------------- | ------ | ---- |
| 抽象数据类型        |        | 1    |
| 数组                |        | 1    |
| 位图                | Bitmap |      |
| 布尔变量            |        | 1    |
| B-树                |        | 0.5  |
| 字符变量            |        | 1    |
| 容器类              |        | 1    |
| 双精度              |        | 0.5  |
| elongated stream    |        |      |
| 枚举类型            |        |      |
| 浮点                |        | 0.5  |
| 堆                  |        |      |
| 下标                |        |      |
| 整数                |        | 1    |
| 链表                |        | 1    |
| 具名常量            |        | 1    |
| 文字量              |        | 1    |
| 局部变量            |        | 1    |
| 查找表              |        |      |
| 数据成员            |        | 1    |
| 指针                |        | 0.5  |
| 私用                |        | 1    |
| retoractive synapse |        |      |
| 引用完整性          |        |      |
| 栈                  |        | 1    |
| 字符串              |        | 1    |
| 结构变量            |        | 1    |
| 树                  |        | 1    |
| typedef             |        |      |
| 共用体              |        | 0.5  |
| value chain         |        |      |
| 变体                |        |      |
|                     |        |      |
|                     |        |      |

## 变量定义

- 关闭隐式声明
- 声明全部变量
- ⭐️遵循一套命名规则 
  - acctNum / acctNo
  - total_value / coin_value / cash_value / cash_num
  - 使用 is 前缀
- 检查变量名

## 变量初始化

- 在声明变量时初始化
- 在靠近变量第一次使用的位置初始化它
- ⭐️理想情况下，在第一次使用变量的位置声明和定义该变量
- 在可能的情况下使用 final 和 const
- ⭐️特别注意计数器和累加器
  - i,j,k ...
  - 使用 for range 语法
- ⭐️在类的构造函数里初始化该类的数据成员
  - new / init

```go
func newPlayer(name string) *Player {
	p := new(Player)
	p.name = name
	return p
}

func (p *Player) init() {
    p.level = 1
    ...
}
```



- 检查是否需要重新初始化
- 一次性初始化具名常量；用可执行代码来初始化变量
- 使用编译器设置自动初始化所有变量
- 利用编译器的警告信息
- 检查输入参数的合法性
- 使用内存访问检查工具来检查错误的指针
- 在程序开始时初始化工作内存

## 作用域

- 使变量引用局部化
  - 变量跨度：同一个变量相邻两次被引用的行数的间隔

```
var a = 0
var b = 0
var c = 0
a = b + c
// 修改为
var a, b, c int
a = b + c
```

- 尽可能缩短变量的存活时间
  - 存活时间：一个变量存在期间所跨越的语句总数
  - 全局变量的存活周期和跨度都很长。
- 减小左右域的一般规则
  - 在循环开始之前再去初始化循环里使用的变量，而不是在该循环所属的子程序的开始处初始化这些变量
  - 直到变量即将被使用再为其赋值
  - 把相关语句放到一起
  - ⭐️把相关语句提取成子程序
  - ⭐️开始时使用最严格的可见性，然后根据需要扩展变量的作用域。

```go
// go 的其他作用域：可以通过 if / for / {} 产生新的作用域。
func foo() {
    a := 1
    b := 2
    {
        a = 10
        b := 20
        println(a, b)
    }
    println(a, b)
}
// 同一级别作用域命名参数可以直接通过 return 返回，不需要接参数，而不是同一级别作用域返回的时候，函数要求返回的参数跟在return后面
func foo2() (res int) {
    res = 1
    {
        res := 10
        return // res is shadowed during return
    }
    return
}
```

## 持续性

## 绑定时间

- 把变量和值绑定在一起的时间
- ⭐️采用越晚的绑定时间越有利，使代码更具有灵活性。

```java
// 硬编码
titleBar.color = 0xFF // 0xFF is hex value for color bule
    
// 编译期绑定
public static final int COLOR_BLUE = 0xFF
public static final int TITLE_BAR_COLOR = COLOR_BLUE

titleBar.color = TITLE_BAR_COLOR

// 运行期绑定
titleBar.color = readTitleBarColor()
```

- ⭐️变量与值绑定的时间总结
  - 编码时（使用神秘数值）我们应该避免使用
  - 编译时（具名常量）
  - 加载时（Windos 注册表，Java 属性文件等外部数据源读取数据）
  - 对象实例化时（窗口创建时读取数据）
  - 即时（窗口重绘时读取数据）

## 数据类型和控制结构的关系

- 序列型数据翻译为顺序语句
- 选择型数据翻译为 if / case 语句
- 迭代型数据翻译为 for，while 等循环结构

## 为变量指定单一用途

- 避免使用 x, temp 之类无意义的变量，在不同场合使用。
- 



# 重构

