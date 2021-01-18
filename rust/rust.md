# Rust 基础



## hello world

```rust
fn main() {
    print!("{} {}", "hello", "world");
}
```



## 基本数据类型

```
i32, u32, f64, bool,  char(4Byte), 元组
```



## 基础语法

### 变量声明赋值

```
let a = 1;
let mut b = "abc";
let c: f64;  // 声明
c = 3.14;
let d: char = '好'
let e = ["a", "b"]
let s = S{}
```



### 控制流

### 循环



## 函数



## 结构/方法

```rust
sturct A {
    name : String,
    id: i32,
}

impl A {
	fn say_hi (&self, name: String) {
        print!("{}: hi {}\n",self.name,  name);
    }
}
```



## 枚举



## 特性



## 容器



## 异常处理



## 面向对象



## 泛型



## 包管理 Cargo



## 编译工具 rustc，cargo

