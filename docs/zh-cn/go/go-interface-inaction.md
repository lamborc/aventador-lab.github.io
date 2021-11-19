# Go 接口

> Go 语言中的接口是一组方法的签名，它是 Go 语言的重要组成部分。使用接口能够让我们写出易于测试的代码，然而很多工程师对 Go 的接口了解都非常有限，也不清楚其底层的实现原理，这成为了开发高性能服务的阻碍。

> 在计算机科学中，接口是计算机系统中多个组件共享的边界，不同的组件能够在边界上交换信息1。如下图所示，接口的本质是引入一个新的中间层，调用方可以通过接口与具体实现分离，解除上下游的耦合，上层的模块不再需要依赖下层的具体模块，只需要依赖一个约定好的接口

![](../../assets/img/go/golang-interface.png)

这种面向接口的编程方式有着非常强大的生命力，无论是在框架还是操作系统中我们都能够找到接口的身影。可移植操作系统接口（Portable Operating System Interface，POSIX)2就是一个典型的例子，它定义了应用程序接口和命令行等标准，为计算机软件带来了可移植性 — 只要操作系统实现了 POSIX，计算机软件就可以直接在不同操作系统上运行。

> 定义良好的接口能够隔离底层的实现，让我们将重点放在当前的代码片段中。SQL 就是接口的一个例子，当我们使用 SQL 语句查询数据时，其实不需要关心底层数据库的具体实现，我们只在乎 SQL 返回的结果是否符合预期

![](../../assets/img/go/go-demo-sql-and-databases.png)


## 隐式接口

> 很多面向对象语言都有接口这一概念，例如 Java 和 C#。Java 的接口不仅可以定义方法签名，还可以定义变量，这些定义的变量可以直接在实现接口的类中使用

```java
public interface MyInterface {
    public String hello = "Hello";
    public void sayHello();
}

public class MyInterfaceImpl implements MyInterface {
    public void sayHello() {
        System.out.println(MyInterface.hello);
    }
}
```

Java 中的类必须通过上述方式显式地声明实现的接口，但是在 Go 语言中实现接口就不需要使用类似的方式。首先，我们简单了解一下在 Go 语言中如何定义接口。定义接口需要使用 interface 关键字，在接口中我们只能定义方法签名，不能包含成员变量，一个常见的 Go 语言接口是这样的

```golang
type error interface {
  Error() string
}

/**
 * 如果一个类型需要实现 error 接口，那么它只需要实现 Error() string 方法，
 * 下面的 RPCError 结构体就是 error 接口的一个实现
 */
type RPCError struct {
  Code    int64
  Message string
}

func (e *RPCError) Error() string {
  return fmt.Sprintf("%s, code=%d", e.Message, e.Code)
}
```

上述代码根本就没有 error 接口的影子，这是为什么呢？Go 语言中接口的实现都是隐式的，我们只需要实现 Error() string 方法就实现了 error 接口。Go 语言实现接口的方式与 Java 完全不同

  - 在 Java 中：实现接口需要显式地声明接口并实现所有方法
  - 在 Go 中：实现接口的所有方法就隐式地实现了接口

上述 RPCError 结构体时并不关心它实现了哪些接口，Go 语言只会在传递参数、返回参数以及变量赋值时才会对某个类型是否实现接口进行检查，这里举几个例子来演示发生接口类型检查的时机：

```golang

func main() {
  var rpcErr error = NewRPCError(400, "unknown err") // typecheck1
  err := AsErr(rpcErr) // typecheck2
  println(err)
}

func NewRPCError(code int64, msg string) error {
  return &RPCError{ // typecheck3
    Code:    code,
    Message: msg,
  }
}

func AsErr(err error) error {
  return err
}
```

Go 语言在编译期间对代码进行类型检查，上述代码总共触发了三次类型检查：

  - 将 *RPCError 类型的变量赋值给 error 类型的变量 rpcErr；
  - 将 *RPCError 类型的变量 rpcErr 传递给签名中参数类型为 error 的 AsErr 函数；
  - 将 *RPCError 类型的变量从函数签名的返回值类型为 error 的 NewRPCError 函数中返回；


### 类型

接口也是 Go 语言中的一种类型，它能够出现在变量的定义、函数的入参和返回值中并对它们进行约束，不过 Go 语言中有两种略微不同的接口，一种是带有一组方法的接口，另一种是不带任何方法的 interface{}

GOLANG DIFFERENT INTERFACE
  - iface
  - eface

Go 语言使用 runtime.iface 表示第一种接口，使用 runtime.eface 表示第二种不包含任何方法的接口 interface{}，两种接口虽然都使用 interface 声明，但是由于后者在 Go 语言中很常见，所以在实现时使用了特殊的类型。

需要注意的是，与 C 语言中的 void * 不同，interface{} 类型不是任意类型。如果我们将类型转换成了 interface{} 类型，变量在运行期间的类型也会发生变化，获取变量类型时会得到 interface{}。

```golang
package main

func main() {
  type Test struct{}
  v := Test{}
  Print(v)
}

func Print(v interface{}) {
  println(v)
}
```

### 指针和接口 

> 在 Go 语言中同时使用指针和接口时会发生一些让人困惑的问题，接口在定义一组方法时没有对实现的接收者做限制，所以我们会看到某个类型实现接口的两种方式

> 这是因为结构体类型和指针类型是不同的，就像我们不能向一个接受指针的函数传递结构体一样，在实现接口时这两种类型也不能划等号。虽然两种类型不同，但是上图中的两种实现不可以同时存在，Go 语言的编译器会在结构体类型和指针类型都实现一个方法时报错 “method redeclared”。

对 Cat 结构体来说，它在实现接口时可以选择接受者的类型，即结构体或者结构体指针，在初始化时也可以初始化成结构体或者指针。下面的代码总结了如何使用结构体、结构体指针实现接口，以及如何使用结构体、结构体指针初始化变量。

```golang
type Cat struct {}
type Duck interface { ... }

func (c  Cat) Quack {}  // 使用结构体实现接口
func (c *Cat) Quack {}  // 使用结构体指针实现接口

var d Duck = Cat{}      // 使用结构体初始化变量
var d Duck = &Cat{}     // 使用结构体指针初始化变量
```

![](../../assets/img/go/golang-interface-method-receiver.png)

### nil 和 non-nil 

> 可以通过一个例子理解Go 语言的接口类型不是任意类型这一句话，下面的代码在 main 函数中初始化了一个 *TestStruct 类型的变量，由于指针的零值是 nil，所以变量 s 在初始化之后也是 nil

```golang
package main

type TestStruct struct{}

func NilOrNot(v interface{}) bool {
  return v == nil
}

func main() {
  var s *TestStruct
  fmt.Println(s == nil)      // #=> true
  fmt.Println(NilOrNot(s))   // #=> false
}
```

  - 将上述变量与 nil 比较会返回 true；
  - 将上述变量传入 NilOrNot 方法并与 nil 比较会返回 false；

出现上述现象的原因是 —— 调用 NilOrNot 函数时发生了隐式的类型转换，除了向方法传入参数之外，变量的赋值也会触发隐式类型转换。在类型转换时，*TestStruct 类型会转换成 interface{} 类型，转换后的变量不仅包含转换前的变量，还包含变量的类型信息 TestStruct，所以转换后的变量与 nil 不相等。

## 反射

虽然在大多数的应用和服务中并不常见，但是很多框架都依赖 Go 语言的反射机制简化代码。因为 Go 语言的语法元素很少、设计简单，所以它没有特别强的表达能力，但是 Go 语言的 reflect 包能够弥补它在语法上reflect.Type的一些劣势

> reflect: 实现了运行时的反射能力，能够让程序操作不同类型的对象1。反射包中有两对非常重要的函数和类型，两个函数分别是:

  - reflect.TypeOf 能获取类型信息；
  - reflect.ValueOf 能获取数据的运行时表示；

两个类型是 reflect.Type 和 reflect.Value，它们与函数是一一对应的关系：

![](../../assets/img/go/golang-reflection.png)

类型 reflect.Type 是反射包定义的一个接口，我们可以使用 reflect.TypeOf 函数获取任意变量的类型，reflect.Type 接口中定义了一些有趣的方法，MethodByName 可以获取当前类型对应方法的引用、Implements 可以判断当前类型是否实现了某个接口

```golang
type Type interface {
  Align() int
  FieldAlign() int
  Method(int) Method
  MethodByName(string) (Method, bool)
  NumMethod() int
  ...
  Implements(u Type) bool
  ...
}

/**
 * 反射包中 reflect.Value 的类型与 reflect.Type 不同，它被声明成了结构体。
 * 这个结构体没有对外暴露的字段，但是提供了获取或者写入数据的方法
 */

type Value struct {
        // 包含过滤的或者未导出的字段
}

func (v Value) Addr() Value
func (v Value) Bool() bool
func (v Value) Bytes() []byte
...
```

反射包中的所有方法基本都是围绕着 reflect.Type 和 reflect.Value 两个类型设计的。我们通过 reflect.TypeOf、reflect.ValueOf 可以将一个普通的变量转换成反射包中提供的 reflect.Type 和 reflect.Value，随后就可以使用反射包中的方法对它们进行复杂的操作。

### 三大法则

运行时反射是程序在运行期间检查其自身结构的一种方式。反射带来的灵活性是一把双刃剑，反射作为一种元编程方式可以减少重复代码，但是过量的使用反射会使我们的程序逻辑变得难以理解并且运行缓慢。我们在这一节中会介绍 Go 语言反射的三大法则，其中包括：

从 interface{} 变量可以反射出反射对象；
从反射对象可以获取 interface{} 变量；
要修改反射对象，其值必须可设置；

#### 第一法则

反射的第一法则是我们能将 Go 语言的 interface{} 变量转换成反射对象。很多读者可能会对这以法则产生困惑 — 为什么是从 interface{} 变量到反射对象？当我们执行 reflect.ValueOf(1) 时，虽然看起来是获取了基本类型 int 对应的反射类型，但是由于 reflect.TypeOf、reflect.ValueOf 两个方法的入参都是 interface{} 类型，所以在方法执行的过程中发生了类型转换。

因为Go 语言的函数调用都是值传递的，所以变量会在函数调用时进行类型转换。基本类型 int 会转换成 interface{} 类型，这也就是为什么第一条法则是从接口到反射对象。

上面提到的 reflect.TypeOf 和 reflect.ValueOf 函数就能完成这里的转换，如果我们认为 Go 语言的类型和反射类型处于两个不同的世界，那么这两个函数就是连接这两个世界的桥梁

![](../../assets/img/go/golang-interface-to-reflection.png)

```golang
package main

import (
  "fmt"
  "reflect"
)

func main() {
  author := "draven"
  fmt.Println("TypeOf author:", reflect.TypeOf(author))     // TypeOf author: string
  fmt.Println("ValueOf author:", reflect.ValueOf(author))   // ValueOf author: draven
}

```

#### 第二法则

反射的第二法则是我们可以从反射对象可以获取 interface{} 变量。既然能够将接口类型的变量转换成反射对象，那么一定需要其他方法将反射对象还原成接口类型的变量，reflect 中的 reflect.Value.Interface 就能完成这项工作

![](../../assets/img/go/golang-reflection-to-interface.png)

不过调用 reflect.Value.Interface 方法只能获得 interface{} 类型的变量，如果想要将其还原成最原始的状态还需要经过如下所示的显式类型转换

```golang
v := reflect.ValueOf(1)
v.Interface().(int)
```

从反射对象到接口值的过程是从接口值到反射对象的镜面过程，两个过程都需要经历两次转换：

  - 从接口值到反射对象：
    * 从基本类型到接口类型的类型转换；
    * 从接口类型到反射对象的转换；
  - 从反射对象到接口值：
    * 反射对象转换成接口类型；
    * 通过显式类型转换变成原始类型；

![](../../assets/img/go/golang-bidirectional-reflection.png)

当然不是所有的变量都需要类型转换这一过程。如果变量本身就是 interface{} 类型的，那么它不需要类型转换，因为类型转换这一过程一般都是隐式的，所以我不太需要关心它，只有在我们需要将反射对象转换回基本类型时才需要显式的转换操作

#### 第三法则

Go 语言反射的最后一条法则是与值是否可以被更改有关，如果我们想要更新一个 reflect.Value，那么它持有的值一定是可以被更新的，假设我们有以下代码

```golang
func main() {
  i := 1
  v := reflect.ValueOf(i)
  v.SetInt(10)
  fmt.Println(i)
}

```

```shell-script
$ go run reflect.go
panic: reflect: reflect.flag.mustBeAssignable using unaddressable value

goroutine 1 [running]:
reflect.flag.mustBeAssignableSlow(0x82, 0x1014c0)
  /usr/local/go/src/reflect/value.go:247 +0x180
reflect.flag.mustBeAssignable(...)
  /usr/local/go/src/reflect/value.go:234
reflect.Value.SetInt(0x100dc0, 0x414020, 0x82, 0x1840, 0xa, 0x0)
  /usr/local/go/src/reflect/value.go:1606 +0x40
main.main()
  /tmp/sandbox590309925/prog.go:11 +0xe0
```


运行上述代码会导致程序崩溃并报出 “reflect: reflect.flag.mustBeAssignable using unaddressable value” 错误，仔细思考一下就能够发现出错的原因：由于 Go 语言的函数调用都是传值的，所以我们得到的反射对象跟最开始的变量没有任何关系，那么直接修改反射对象无法改变原始变量，程序为了防止错误就会崩溃。

想要修改原变量只能使用如下的方法：

```golang
func main() {
  i := 1
  v := reflect.ValueOf(&i)
  v.Elem().SetInt(10)
  fmt.Println(i)
}
```