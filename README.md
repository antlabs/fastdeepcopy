## 作用
[![Go](https://github.com/antlabs/fastdeepcopy/workflows/Go/badge.svg)](https://github.com/antlabs/fastdeepcopy/actions)
[![codecov](https://codecov.io/gh/antlabs/fastdeepcopy/branch/master/graph/badge.svg)](https://codecov.io/gh/antlabs/fastdeepcopy)

fastdeepcopy.Copy主要用于两个类型间的深度拷贝[从零实现]
和deepcopy功能上没有区别，使用了一些hack手段提升性能


## feature
* 支持异构结构体拷贝, dst和src可以是不同的类型，会拷贝dst和src交集的部分
* 多类型支持struct/map/slice/array/int...int64/uint...uint64/ 等等
* 性能相比json序列化和反序列化的做法，拥有更快的执行速度
* 可以控制拷贝结构体层次
* 可以通过tag控制感兴趣的字段

## 内容
- [Installation](#Installation)
- [Quick start](#quick-start)
- [example](#example)
    - [1. 控制拷贝结构体最多深度](#max-copy-depth)
    - [2. 只拷贝设置tag的结构体成员](#copy-only-the-specified-tag)
    - [3.拷贝slice](#copy-slice)
    - [4.拷贝map](#copy-map)

## Installation
```
go get github.com/antlabs/fastdeepcopy
```

## Quick start
```go
package main

import (
    "fmt"
    "github.com/antlabs/fastdeepcopy"
)

type dst struct {
    ID int
    Result string
}

type src struct{
    ID int
    Text string
}
func main() {
   d, s := dst{}, src{ID:3}
   fastdeepcopy.Copy(&d, &s).Do()
   fmt.Printf("%#v\n", d)
   
}

```

## max copy depth
如果src的结构体嵌套了两套，MaxDepth可以控制只拷贝一层
```go
fastdeepcopy.Copy(&dst{}, &src{}).MaxDepth(1).Do()
```

## copy only the specified   tag
只拷贝结构体里面有copy tag的字段，比如下面只会拷贝ID成员
```go
package main

import (
        "fmt"

        "github.com/antlabs/fastdeepcopy"
)

type dst struct {
        ID     int `copy:"ID"`
        Result string
}

type src struct {
        ID     int `copy:"ID"`
        Result string
}

func main() {
        d := dst{}
        s := src{ID: 3, Result: "use tag"}

        fastdeepcopy.Copy(&d, &s).RegisterTagName("copy").Do()

        fmt.Printf("%#v\n", d)
}

```
## copy slice
```go
package main

import (
        "fmt"

        "github.com/antlabs/fastdeepcopy"
)

func main() {
        i := []int{1, 2, 3, 4, 5, 6}
        var o []int

        fastdeepcopy.Copy(&o, &i).Do()

        fmt.Printf("%#v\n", o)
}

```

## copy map
```go
package main

import (
        "fmt"

        "github.com/antlabs/fastdeepcopy"
)

func main() {
        i := map[string]int{
                "cat":  100,
                "head": 10,
                "tr":   3,
                "tail": 44,
        }

        var o map[string]int
        fastdeepcopy.Copy(&o, &i).Do()

        fmt.Printf("%#v\n", o)
}

```
## 性能
从零实现的fastdeepcopy相比deepcopy序列化与反序列化方式拥有更好的性能
```
goos: linux
goarch: amd64
pkg: github.com/antlabs/fastdeepcopy
Benchmark_MiniCopy-12    	  243212	      4987 ns/op
Benchmark_DeepCopy-12    	  273775	      4781 ns/op
PASS
ok  	github.com/antlabs/fastdeepcopy	4.496s

```
