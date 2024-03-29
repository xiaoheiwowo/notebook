# Redis

- [Redis](#redis)
  - [AOF](#aof)
  - [SDS](#sds)
    - [要点](#要点)
  - [链表](#链表)
  - [字典](#字典)
  - [跳表](#跳表)

## AOF

三种策略：
abc

## SDS

Simple Dynamic String

- Struct

```
struct sdshdr {
    int len;
    int free;
    char buf[];
}
```

- 为了兼容 C 字符串，buf 中除了字符串外额外保存一个 '\0'
- 通过 len 做到二进制安全
- 通过 free 优化内存分配，回收

### 要点

- 空间预分配

  - 如果对 SDS 进行修改之后， SDS 的长度（也即是 len 属性的值）将小于 1 MB ， 那么程序分配和 len 属性同样大小的未使用空间， 这时 SDS len 属性的值将和 free 属性的值相同。 举个例子， 如果进行修改之后， SDS 的 len 将变成 13 字节， 那么程序也会分配 13 字节的未使用空间， SDS 的 buf 数组的实际长度将变成 13 + 13 + 1 = 27 字节（额外的一字节用于保存空字符）。
  - 如果对 SDS 进行修改之后， SDS 的长度将大于等于 1 MB ， 那么程序会分配 1 MB 的未使用空间。 举个例子， 如果进行修改之后， SDS 的 len 将变成 30 MB ， 那么程序会分配 1 MB 的未使用空间， SDS 的 buf 数组的实际长度将为 30 MB + 1 MB + 1 byte 。

- 惰性释放
- 比起 C 字符串， SDS 具有以下优点：
  - 常数复杂度获取字符串长度。
  - 杜绝缓冲区溢出。
  - 减少修改字符串长度时所需的内存重分配次数。
  - 二进制安全。
  - 兼容部分 C 字符串函数。

## 链表

双向链表

## 字典

- 4 个结构体: dictht, dictEntry, dict, dictType
- 大概的关系

```go
type dict struct {
  typ dictType
  ht [2]dictht{
    table dictEntry
  }
  rehashidx int
}
```

- dictEntry: k,v,next 链表法解决哈希冲突，新节点放在 head, 用 sizemask 计算索引值
- dictType: hash func，算法 MurmurHash2

###

## 跳表
