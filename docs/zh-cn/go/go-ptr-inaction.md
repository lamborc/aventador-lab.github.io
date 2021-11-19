# Go 语言中的指针

> 与 Java 和 .NET 等编程语言不同，Go语言为程序员提供了控制数据结构指针的能力，但是，并不能进行指针运算。Go语言允许你控制特定集合的数据结构、分配的数量以及内存访问模式，这对于构建运行良好的系统是非常重要的。指针对于性能的影响不言而喻，如果你想要做系统编程、操作系统或者网络应用，指针更是不可或缺的一部分

> 指针（pointer）在Go语言中可以被拆分为两个核心概念

  - 类型指针，允许对这个指针类型的数据进行修改，传递数据可以直接使用指针，而无须拷贝数据，类型指针不能进行偏移和运算
  - 切片，由指向起始元素的原始指针、元素数量和容量组成

> 受益于这样的约束和拆分，Go语言的指针类型变量即拥有指针高效访问的特点，又不会发生指针偏移，从而避免了非法修改关键性数据的问题。同时，垃圾回收也比较容易对不会发生偏移的指针进行检索和回收

**切片比原始指针具备更强大的特性，而且更为安全。切片在发生越界时，运行时会报出宕机，并打出堆栈，而原始指针只会崩溃**

> GO 指针分为: **指针地址、指针类型和指针取值**

## 对比**C/C++中的指针**

> 其实，指针是 C/C++ 语言拥有极高性能的根本所在，在操作大块数据和做偏移时即方便又便捷。因此，操作系统依然使用C语言及指针的特性进行编写

> C/C++ 中指针饱受诟病的根本原因是指针的运算和内存释放，C/C++ 语言中的裸指针可以自由偏移，甚至可以在某些情况下偏移进入操作系统的核心区域，我们的计算机操作系统经常需要更新、修复漏洞的本质，就是为解决指针越界访问所导致的“缓冲区溢出”的问题

## 指针地址和指针类型

> 一个指针变量可以指向任何一个值的内存地址，它所指向的值的内存地址在 32 和 64 位机器上分别占用 4 或 8 个字节，占用字节的大小与所指向的值的大小无关。当一个指针被定义后没有分配到任何变量时，它的默认值为 nil。指针变量通常缩写为 ptr。

**每个变量在运行时都拥有一个地址，这个地址代表变量在内存中的位置。Go语言中使用在变量名前面添加&操作符（前缀）来获取变量的内存地址（取地址操作），格式如下：**

```golang
// v 的类型为 T
ptr := &v    // 其中 v 代表被取地址的变量，变量 v 的地址使用变量 ptr 进行接收，ptr 的类型为*T，称做 T 的指针类型，*代表指针。
```

**Code**
```golang
package main

import (
  "fmt"
)

func main() {
  var cat int = 1
  var str string = "orange"

  fmt.Printf("%p %p",&cat,&str)  // 运行结果:0xc042052088 0xc0420461b0 运行时
}

```

## **从指针获取指针指向的值**

```golang
package main

import (
  "fmt"
)

func main() {
  var house = "Malagebi Point 10080,90265"

  // 对字符串取地址, ptr类型为*string
  ptr := &house

  fmt.Printf("ptr type: %T\n",ptr)          // 打印ptr的类型,输出: *string
  fmt.Printf("address:%p\n",ptr)            // 打印ptr的指针地址,输出: 0xc0423...

  value := *ptr                             // 对指针进行取值操作

  fmt.Printf("value type: %T\n",value)      // 取值类型,输出: string
  fmt.Print("value : %s\n",value)           // 值输出: Malagebi Point 10080,90265
}

```

> 使用指针修改值

```golang
package main

import (
  "fmt"
)

func swap(a,b *int){
  // 取a指针的值,赋给临时变量t
  t := *a

  // 取b指针的值,赋给a指针指向的变量
  *a = *b

  // 将临时值赋给 b指针指向的变量
  *b = t
}

func main() {
  x,y :=1,2 

  swap(&x,&y)

  fmt.Printf(x,y)           // 输出:2 1
}
```

## make vs new

  - **make** 的作用是初始化内置的数据结构，也就是我们在前面提到的切片、哈希表和 Channel；
  - **new** 的作用是根据传入的类型分配一片内存空间并返回指向这片内存空间的指针

> make

```golang
slice := make([]int, 0, 100)      // slice 是一个包含 data、cap 和 len 的结构体
hash := make(map[int]bool, 10)    // hash 是一个指向 runtime.hmap 结构体的指针
ch := make(chan int, 5)           // ch 是一个指向 runtime.hchan 结构体的指针；
```

> new

```golang
i := new(int)

var v int
i := &v
```

![](../../assets/img/go/golang-make-and-new.png)

#### 字符串运行时指针 

> 因为 Cat 结构体的定义中只包含一个字符串，而字符串在 Go 语言中总共占 16 字节，所以每一个 Cat 结构体的大小都是 16 字节。初始化 Cat 结构体之后就进入了将 *Cat 转换成 Duck 类型的过程了：

![](../../assets/img/go/golang-new-struct-pointer.png)

```language
LEAQ  go.itab.*"".Cat,"".Duck(SB), AX    ;; AX = *itab(go.itab.*"".Cat,"".Duck)
MOVQ  DI, (SP)                           ;; SP = AX
```