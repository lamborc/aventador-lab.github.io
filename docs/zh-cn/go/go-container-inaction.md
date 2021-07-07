# Go 语言容器


## 数组

var a [3]int             // 定义三个整数的数组


## Go语言切片

> 切片（slice）是对数组的一个连续片段的引用，所以切片是一个引用类型（因此更类似于 C/C++ 中的数组类型，或者 Python 中的 list 类型），这个片段可以是整个数组，也可以是由起始和终止索引标识的一些项的子集，需要注意的是，终止索引标识的项不包括在切片内。

> Go语言中切片的内部结构包含地址、大小和容量，切片一般用于快速地操作一块数据集合，如果将数据集合比作切糕的话，切片就是你要的“那一块”，切的过程包含从哪里开始（切片的起始位置）及切多大（切片的大小），容量可以理解为装切片的口袋大小，如下图所示。

![](../../assets/img/go/go-slice.jpg)

### 从数组或切片生成新的切片

> 切片默认指向一段连续内存区域，可以是数组，也可以是切片本身

从连续内存区域生成切片是常见的操作，格式如下：

  slice [开始位置 : 结束位置]

**语法说明如下：**

  * slice：表示目标切片对象；
  * 开始位置：对应目标切片对象的索引；
  * 结束位置：对应目标切片的结束索引。

```golang
var a  = [3]int{1, 2, 3}
fmt.Println(a, a[1:2])      // [1 2 3]  [2]
```

>  从指定范围中生成切片

```golang
var highRiseBuilding [30]int
for i := 0; i < 30; i++ {
        highRiseBuilding[i] = i + 1
}
// 区间
fmt.Println(highRiseBuilding[10:15])  // [11 12 13 14 15]
// 中间到尾部的所有元素
fmt.Println(highRiseBuilding[20:])  // [21 22 23 24 25 26 27 28 29 30]
// 开头到中间指定位置的所有元素
fmt.Println(highRiseBuilding[:2])  // [1 2]
```

> 生成切片的格式中，当开始和结束位置都被忽略时，生成的切片将表示和原切片一致的切片，并且生成的切片与原切片在数据内容上也是一致的

```golang
a := []int{1, 2, 3}
fmt.Println(a[:])  // [1 2 3]
```

>  把切片的开始和结束位置都设为 0 时，生成的切片将变空. 

```golang
a := []int{1, 2, 3}
fmt.Println(a[0:0])  // []
```

### 直接声明新的切片

> 除了可以从原有的数组或者切片中生成切片外，也可以声明一个新的切片，每一种类型都可以拥有其切片类型，表示多个相同类型元素的连续集合，因此切片类型也可以被声明

  var name []Type

### 使用 make() 函数构造切片

> 如果需要动态地创建一个切片，可以使用 make() 内建函数

  make( []Type, size, cap ) 

Type 是指切片的元素类型，size 指的是为这个类型分配多少个元素，cap 为预分配的元素数量，这个值设定后不影响 size，只是能提前分配空间，降低多次分配空间造成的性能问题

```golang
a := make([]int, 2)
b := make([]int, 2, 10)

fmt.Println(a, b)                   // [0 0] [0 0]
fmt.Println(len(a), len(b))         // 2 2
```

### **重要**

> 使用 make() 函数生成的切片一定发生了内存分配操作，但给定开始与结束位置（包括切片复位）的切片只是将新的切片结构指向已经分配好的内存区域，设定开始与结束位置，不会发生内存分配操作


###  append()为切片添加元素

> 切片在扩容时，容量的扩展规律是按容量的 2 倍数进行扩充，例如 1、2、4、8、16……，代码如下

```golang
var a []int
a = append(a, 1) // 追加1个元素
a = append(a, 1, 2, 3) // 追加多个元素, 手写解包方式
a = append(a, []int{1,2,3}...) // 追加一个切片, 切片需要解包

var numbers []int
for i := 0; i < 10; i++ {
    numbers = append(numbers, i)
    fmt.Printf("len: %d  cap: %d pointer: %p\n", len(numbers), cap(numbers), numbers)
}

// len: 1  cap: 1 pointer: 0xc0420080e8
// len: 2  cap: 2 pointer: 0xc042008150
// len: 3  cap: 4 pointer: 0xc04200e320
// len: 4  cap: 4 pointer: 0xc04200e320
// len: 5  cap: 8 pointer: 0xc04200c200
// len: 6  cap: 8 pointer: 0xc04200c200
// len: 7  cap: 8 pointer: 0xc04200c200
// len: 8  cap: 8 pointer: 0xc04200c200
// len: 9  cap: 16 pointer: 0xc042074000
// len: 10  cap: 16 pointer: 0xc042074000
```

**在切片的开头添加元素**
在切片开头添加元素一般都会导致内存的重新分配，而且会导致已有元素全部被复制 1 次，因此，从切片的开头添加元素的性能要比从尾部追加元素的性能差很多

```golang
var a []int
a = append(a[:i], append([]int{x}, a[i:]...)...) // 在第i个位置插入x
a = append(a[:i], append([]int{1,2,3}, a[i:]...)...) // 在第i个位置插入切片
```

### copy()：切片复制

> 内置函数 copy() 可以将一个数组切片复制到另一个数组切片中，如果加入的两个数组切片不一样大，就会按照其中较小的那个数组切片的元素个数进行复制

```golang
package main
import "fmt"
func main() {
    // 设置元素数量为1000
    const elementCount = 1000
    // 预分配足够多的元素切片
    srcData := make([]int, elementCount)
    // 将切片赋值
    for i := 0; i < elementCount; i++ {
        srcData[i] = i
    }
    // 引用切片数据,切片不会因为等号操作进行元素的复制
    refData := srcData

    // 预分配足够多的元素切片
    copyData := make([]int, elementCount)
    // 将数据复制到新的切片空间中
    copy(copyData, srcData)
    // 修改原始数据的第一个元素
    srcData[0] = 999
    // 打印引用切片的第一个元素
    fmt.Println(refData[0])           // 引用数据的第一个元素将会发生变化:999

    // 打印复制切片的第一个和最后一个元素
    fmt.Println(copyData[0], copyData[elementCount-1])  // 0 999

    // 复制原始数据从4到6(不包含)
    copy(copyData, srcData[4:6])
    for i := 0; i < 5; i++ {
        fmt.Printf("%d ", copyData[i])
    }
}
```

### 从切片中删除元素

> Go语言并没有对删除切片元素提供专用的语法或者接口，需要使用切片本身的特性来删除元素，根据要删除元素的位置有三种情况，分别是从开头位置删除、从中间位置删除和从尾部删除，其中删除切片尾部的元素速度最快

**从开头位置删除**
```golang
a = []int{1, 2, 3}
a = a[1:] // 删除开头1个元素
a = a[N:] // 删除开头N个元素
```

**从中间位置删除**
```golang
a = []int{1, 2, 3, ...}
a = append(a[:i], a[i+1:]...) // 删除中间1个元素
a = append(a[:i], a[i+N:]...) // 删除中间N个元素
a = a[:i+copy(a[i:], a[i+1:])] // 删除中间1个元素
a = a[:i+copy(a[i:], a[i+N:])] // 删除中间N个元素
```
**从尾部删除** 
```golang
a = []int{1, 2, 3}
a = a[:len(a)-1] // 删除尾部1个元素
a = a[:len(a)-N] // 删除尾部N个元素
```

> 删除开头的元素和删除尾部的元素都可以认为是删除中间元素操作的特殊情况

```golang
package main
import "fmt"
func main() {
    seq := []string{"a", "b", "c", "d", "e"}
    // 指定删除位置
    index := 2

    // 查看删除位置之前的元素和之后的元素
    fmt.Println(seq[:index], seq[index+1:]) // 输出:[a b] [d e]

    // 将删除点前后的元素连接起来
    seq = append(seq[:index], seq[index+1:]...)

    fmt.Println(seq)  // 输出:[a b d e]
}
```

![](../../assets/img/go/go-container-delete.jpg)


<h4 style="color:#c8f;font-size: 20px;">
  <span>
    连续容器的元素删除无论在任何语言中，都要将删除点前后的元素移动到新的位置，随着元素的增加，这个过程将会变得极为耗时，因此，当业务需要大量、频繁地从一个切片中删除元素时，如果对性能要求较高的话，就需要考虑更换其他的容器了（如双链表等能快速从删除点删除元素）
  </span>
</h4>

### range 关键字

```golang
// 创建一个整型切片，并赋值
slice := []int{10, 20, 30, 40}
// 迭代每一个元素，并显示其值
for index, value := range slice {
    fmt.Printf("Index: %d Value: %d\n", index, value)
}
```
----

## Go语言映射 Map

> Go语言中 map 是一种特殊的数据结构，一种元素对（pair）的无序集合，pair 对应一个 key（索引）和一个 value（值），所以这个结构也称为关联数组或字典，这是一种能够快速寻找值的理想结构，给定 key，就可以迅速找到对应的 value。

> 声明的时候不需要知道 map 的长度，因为 map 是可以动态增长的，未初始化的 map 的值是 nil，使用函数 len() 可以获取 map 中 pair 的数目.

**map 是引用类型，可以使用如下方式声明**

  var mapname map[keytype]valuetype

  * mapname 为 map 的变量名。
  * keytype 为键类型。
  * valuetype 是键对应的值类型。

```golang
package main

import "fmt"

func main() {

    var mapLit map[string]int
    //var mapCreated map[string]float32
    var mapAssigned map[string]int

    mapLit = map[string]int{"one": 1, "two": 2}

    mapCreated := make(map[string]float32)

    mapAssigned = mapLit
    mapCreated["key1"] = 4.5
    mapCreated["key2"] = 3.14159
    mapAssigned["two"] = 3

    fmt.Printf("Map literal at \"one\" is: %d\n", mapLit["one"])
    fmt.Printf("Map created at \"key2\" is: %f\n", mapCreated["key2"])
    fmt.Printf("Map assigned at \"two\" is: %d\n", mapLit["two"])
    fmt.Printf("Map literal at \"ten\" is: %d\n", mapLit["ten"])
}
```

> map 容量: 和数组不同，map 可以根据新增的 key-value 动态的伸缩，因此它不存在固定长度或者最大限制，但是也可以选择标明 map 的初始容量 capacity

  make(map[keytype]valuetype, cap)  

```golang
/*
当 map 增长到容量上限的时候，如果再增加新的 key-value，map 的大小会自动加 1，
所以出于性能的考虑，对于大的 map 或者会快速扩张的 map，即使只是大概知道容量，也最好先标明
 */
map2 := make(map[string]float, 100)
```

### 使用 delete() 函数从 map 中删除键值对

```golang
scene := make(map[string]int)
// 准备map数据
scene["route"] = 66
scene["brazil"] = 4
scene["china"] = 960
delete(scene, "brazil")
```

### 清空 map 中的所有元素

> 有意思的是，Go语言中并没有为 map 提供任何清空所有元素的函数、方法，清空 map 的唯一办法就是重新 make 一个新的 map，不用担心垃圾回收的效率，Go语言中的并行垃圾回收效率比写一个清空函数要高效的多