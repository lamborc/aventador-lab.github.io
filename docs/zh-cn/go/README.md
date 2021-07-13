# Go In Action

## 编译原理

### **抽象语法树** (Abstract Syntax Tree、AST) 

>是源代码语法的结构的一种抽象表示，它用树状的方式表示编程语言的语法结构1。抽象语法树中的每一个节点都表示源代码中的一个元素，每一棵子树都表示一个语法元素，以表达式 2 * 3 + 7 为例，编译器的语法分析阶段会生成如下图所示的抽象语法树。

![](../../assets/img/go/go-abstract-syntax-tree.png)

**作为编译器常用的数据结构，抽象语法树抹去了源代码中不重要的一些字符 - 空格、分号或者括号等等。编译器在执行完语法分析之后会输出一个抽象语法树，这个抽象语法树会辅助编译器进行语义分析，我们可以用它来确定语法正确的程序是否存在一些类型不匹配的问题。**

### 静态单赋值 (Static Single Assignment、SSA) 

> 是中间代码的特性，如果中间代码具有静态单赋值的特性，那么每个变量就只会被赋值一次

```golang
x := 1
x := 2
y := x 
/**
 * 经过简单的分析，我们就能够发现上述的代码第一行的赋值语句 x := 1 不会起到任何作用。下面是具有 SSA 特性的中间代码，我们可以清晰地发现变量 y_1 和 x_1 是没有任何关系的，所以在机器码生成时就可以省去 x := 1 的赋值，通过减少需要执行的指令优化这段代码
 */
x_1 := 1
x_2 := 2
y_1 := x_2

```

### 指令集 

> x86 是目前比较常见的指令集，除了 x86 之外，还有 arm 等指令集，苹果最新 Macbook 的自研芯片就使用了 arm 指令集，不同的处理器使用了不同的架构和机器语言，所以很多编程语言为了在不同的机器上运行需要将源代码根据架构翻译成不同的机器代码

  * **复杂指令集CISC** : 通过增加指令的类型减少需要执行的指令数
  * **精简指令集RISC** : 使用更少的指令类型完成目标的计算任务

早期的 CPU 为了减少机器语言指令的数量一般使用复杂指令集完成计算任务，这两者并没有绝对的优劣，它们只是在一些设计上的选择不同以达到不同的目的


### 原理

> Go 语言编译器的源代码在 src/cmd/compile 目录中，目录下的文件共同组成了 Go 语言的编译器，学过编译原理的人可能听说过编译器的前端和后端，编译器的前端一般承担着词法分析、语法分析、类型检查和中间代码生成几部分工作，而编译器后端主要负责目标代码的生成和优化，也就是将中间代码翻译成目标机器能够运行的二进制机器码。

![](../../assets/img/go/go-complication-process.png)

> Go 的编译器在逻辑上可以被分成四个阶段

  - 词法与语法分析
  - 类型检查和 AST 转换
  - 通用 SSA 生成
  - 最后的机器代码生成

#### 词法与语法分析

所有的编译过程其实都是从解析代码的源文件开始的，词法分析的作用就是解析源代码文件，它将文件中的字符串序列转换成 Token 序列，方便后面的处理和解析，我们一般会把执行词法分析的程序称为词法解析器（lexer）

而语法分析的输入是词法分析器输出的 Token 序列，语法分析器会按照顺序解析 Token 序列，该过程会将词法分析生成的 Token 按照编程语言定义好的文法（Grammar）自下而上或者自上而下的规约，每一个 Go 的源代码文件最终会被归纳成一个 SourceFile 结构体；

```go
SourceFile = PackageClause ";" { ImportDecl ";" } { TopLevelDecl ";" } 
```

词法分析会返回一个不包含空格、换行等字符的 Token 序列，例如：package, json, import, (, io, ), …，而语法分析会把 Token 序列转换成有意义的结构体，即语法树：

```go
"json.go": SourceFile {
    PackageName: "json",
    ImportDecl: []Import{
        "io",
    },
    TopLevelDecl: ...
}
```

Token 到上述抽象语法树（AST）的转换过程会用到语法解析器，每一个 AST 都对应着一个单独的 Go 语言文件，这个抽象语法树中包括当前文件属于的包名、定义的常量、结构体和函数等

![](../../assets/img/go/golang-files-and-ast.png)

#### 类型检查 
当拿到一组文件的抽象语法树之后，Go 语言的编译器会对语法树中定义和使用的类型进行检查，类型检查会按照以下的顺序分别验证和处理不同类型的节点

  - 常量、类型和函数名及类型；
  - 变量的赋值和初始化；
  - 函数和闭包的主体；
  - 哈希键值对的类型；
  - 导入函数体；
  - 外部的声明；

通过对整棵抽象语法树的遍历，我们在每个节点上都会对当前子树的类型进行验证，以保证节点不存在类型错误，所有的类型错误和不匹配都会在这一个阶段被暴露出来，其中包括：结构体对接口的实现。

类型检查阶段不止会对节点的类型进行验证，还会展开和改写一些内建的函数，例如 make 关键字在这个阶段会根据子树的结构被替换成 runtime.makeslice 或者 runtime.makechan 等函数. 

![](../../assets/img/go/golang-keyword-make.png)

类型检查这一过程在整个编译流程中还是非常重要的，Go 语言的很多关键字都依赖类型检查期间的展开和改写

#### 中间代码生成

当将源文件转换成了抽象语法树、对整棵树的语法进行解析并进行类型检查之后，就可以认为当前文件中的代码不存在语法错误和类型错误的问题了，Go 语言的编译器就会将输入的抽象语法树转换成中间代码。

在类型检查之后，编译器会通过 cmd/compile/internal/gc.compileFunctions 编译整个 Go 语言项目中的全部函数，这些函数会在一个编译队列中等待几个 Goroutine 的消费，并发执行的 Goroutine 会将所有函数对应的抽象语法树转换成中间代码。

![](../../assets/img/go/go-concurrency-compiling.png)

由于 Go 语言编译器的中间代码使用了 SSA 的特性，所以在这一阶段我们能够分析出代码中的无用变量和片段并对代码进行优化，中间代码生成一节会详细介绍中间代码的生成过程并简单介绍 Go 语言中间代码的 SSA 特性


#### 机器码生成

Go 语言源代码的 src/cmd/compile/internal 目录中包含了很多机器码生成相关的包，不同类型的 CPU 分别使用了不同的包生成机器码，其中包括 amd64、arm、arm64、mips、mips64、ppc64、s390x、x86 和 wasm，其中比较有趣的就是 WebAssembly（Wasm)了

作为一种在栈虚拟机上使用的二进制指令格式，它的设计的主要目标就是在 Web 浏览器上提供一种具有高可移植性的目标语言。Go 语言的编译器既然能够生成 Wasm 格式的指令，那么就能够运行在常见的主流浏览器中 

  $ GOARCH=wasm GOOS=js go build -o lib.wasm main.go 

我们可以使用上述的命令将 Go 的源代码编译成能够在浏览器上运行 WebAssembly 文件，当然除了这种新兴的二进制指令格式之外，Go 语言经过编译还可以运行在几乎全部的主流机器上，不过它的兼容性在除 Linux 和 Darwin 之外的机器上可能还有一些问题


![](../../assets/img/go/go-supported-hardware.png)

####  编译器入口

Go 语言的编译器入口在 src/cmd/compile/internal/gc/main.go 文件中，其中 600 多行的 cmd/compile/internal/gc.Main 就是 Go 语言编译器的主程序，该函数会先获取命令行传入的参数并更新编译选项和配置，随后会调用 cmd/compile/internal/gc.parseFiles 对输入的文件进行词法与语法分析得到对应的抽象语法树

得到抽象语法树后会分九个阶段对抽象语法树进行更新和编译，就像我们在上面介绍的，抽象语法树会经历类型检查、SSA 中间代码生成以及机器码生成三个阶段 


  - 检查常量、类型和函数的类型；
  - 处理变量的赋值；
  - 对函数的主体进行类型检查；
  - 决定如何捕获变量；
  - 检查内联函数的类型；
  - 进行逃逸分析；
  - 将闭包的主体转换成引用的捕获变量；
  - 编译顶层函数；
  - 检查外部依赖的声明

```golang
for i := 0; i < len(xtop); i++ {
    n := xtop[i]
    if op := n.Op; op != ODCL && op != OAS && op != OAS2 && (op != ODCLTYPE || !n.Left.Name.Param.Alias) {
      xtop[i] = typecheck(n, ctxStmt)
    }
  }

  for i := 0; i < len(xtop); i++ {
    n := xtop[i]
    if op := n.Op; op == ODCL || op == OAS || op == OAS2 || op == ODCLTYPE && n.Left.Name.Param.Alias {
      xtop[i] = typecheck(n, ctxStmt)
    }
  }
```

类型检查会遍历传入节点的全部子节点，这个过程会展开和重写 make 等关键字，在类型检查会改变语法树中的一些节点，不会生成新的变量或者语法树，这个过程的结束也意味着源代码中已经不存在语法和类型错误，中间代码和机器码都可以根据抽象语法树正常生成

```golang
  initssaconfig()

  peekitabs()

  for i := 0; i < len(xtop); i++ {
    n := xtop[i]
    if n.Op == ODCLFUNC {
      funccompile(n)
    }
  }

  compileFunctions()

  for i, n := range externdcl {
    if n.Op == ONAME {
      externdcl[i] = typecheck(externdcl[i], ctxExpr)
    }
  }

  checkMapKeys()
}
```

在主程序运行的最后，编译器会将顶层的函数编译成中间代码并根据目标的 CPU 架构生成机器码，不过在这一阶段也有可能会再次对外部依赖进行类型检查以验证其正确性

----

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
  
### 函数内代码块

> 函数内代码块控制作用域

```golang
func test() {
  x : = 1;

  fmt.Printf('x value: %d',x)     // 1
  
  {
    x : = 2     // 旨在作用域内有效
    fmt.Printf('x value: %d',x)   // 2
  } 

  fmt.Printf('x value: %d',x)     // 1
}
```