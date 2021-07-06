# Go In Action


## golang module

> 从 Go 1.13 开始，模块模式将成为默认模式

> go mod 命令

```bash
mdkir test && cd test 
go mod init test    # create test mod 

```

> 添加 依赖包

```shell-script
go get -v github.com/gin-gonic/gin<@v1.1.4>          # 然后引入
go list -m all                                       # 列出所有包
go list -m -versions github.com/gin-gonic/gin         # 列出历史版本  
go mod edit -require="github.com/gin-gonic/gin@v1.1.3"  # 修改 go.mod 文件
go tidy   # 下载更新依赖
go mod tidy           # 移除不需要的依赖 
```


### go 的各路语法

```golang
func Sha(){}    // 方法名首字母大写,就是将方法暴露, 在node 里module.exports or export ;
func privateSha() {}  // 小写私有
```

#### Go 语言堆栈

  - 堆（heap）：堆是用于存放进程执行中被动态分配的内存段。它的大小并不固定，可动态扩张或缩减。当进程调用 malloc 等函数分配内存时，新分配的内存就被动态加入到堆上（堆被扩张）。当利用 free 等函数释放内存时，被释放的内存从堆中被剔除（堆被缩减）；

  - 栈(stack)：栈又称堆栈， 用来存放程序暂时创建的局部变量，也就是我们函数的大括号{ }中定义的局部变量

> 在程序的编译阶段，编译器会根据实际情况自动选择在栈或者堆上分配局部变量的存储空间，不论使用 var 还是 new 关键字声明变量都不会影响编译器的选择

```golang
/**
 * 函数 f 里的变量 x 必须在堆上分配，因为它在函数退出后依然可以通过包一级的 global 变量找到，虽然它是在函数内部定义的。用Go语言的术语说，这个局部变量 x 从函数 f 中逃逸了
 */
var global *int

func f() {
  var x int
  x = 1
  global = &x
}

/*
当函数 g 返回时，变量 *y 不再被使用，也就是说可以马上被回收的。因此，*y 并没有从函数 g 中逃逸，编译器可以选择在栈上分配 *y 的存储空间，也可以选择在堆上分配，然后由Go语言的 GC（垃圾回收机制）回收这个变量的内存空间
 */
func g() {
  y := new(int)

  *y = 1
}

```

----
#### Go语言内置的 flag 包实现了对命令行参数的解析，flag 包使得开发命令行工具更为简单

> 代码通过提前定义一些命令行指令和对应的变量，并在运行时输入对应的参数，经过 flag 包的解析后即可获取命令行的数据

```golang
package main

import (
  "flag"
  "fmt"
)

var mode = flag.String("mode","","process mode")

func main(){
  flag.parse()            // 解析命令行参数

  fmt.Print(*mode)        // 输出命令行参数 
}
```

```bash
go run main.go --mode=fast # 输出 fast
```
  
