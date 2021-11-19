<h1>Go 语法基础</h1>


## 变量


> 当一个变量被声明之后，系统自动赋予它该类型的零值,所有的内存在 Go 中都是经过初始化的

> 变量的命名规则遵循骆驼命名法，即首个单词小写，每个新单词的首字母大写，例如：numShips 和 startDate

> **uintptr** 无符号的整数类型 uintptr，它没有指定具体的 bit 大小但是足以容纳指针。uintptr 类型只有在底层编程时才需要，特别是Go语言和C语言函数库或操作系统接口相交互的地方

> 哪些情况下使用 int 和 uint: 程序逻辑对整型范围没有特殊需求。例如，对象的长度使用内建 len() 函数返回，这个长度可以根据不同平台的字节长度进行变化。实际使用中，切片或 map 的元素数量等都可以用 int 来表示. 反之，在二进制传输、读写文件的结构描述时，为了保持文件的结构不会受到不同编译目标平台字节长度的影响，不要使用 int 和 uint

> 字符串是一种值类型，且值不可变，即创建某个文本后将无法再次修改这个文本的内容，更深入地讲，字符串是字节的定长数组
> Go语言中字符串的内部实现使用 UTF-8 编码，通过 rune 类型，可以方便地对每个 UTF-8 字符进行访问。Go语言也支持按照传统的 ASCII 码方式逐字符进行访问

```golang
var big int  // var name type 
/* 基础类型[
  bool(false),sting(空字符串,指针为nil),
  int,int8,int16,int32,int64,uint,uint8,uint16,uint32,uint64,uintptr,
  byte(uint8 别名),
  rune(int32 别名,代表一个Unicode码)
  float32,float64, (默认值0.0)
  complex64,complex128
  ]
*/
var small *int  // *int  指针类型
var timestampStr string 

// 批量定义变量
var (
  a int
  b string 
  c []float32
  e struct {
    x int
    y int
  }
)

// 简短格式:定义变量同时初始化,只能在函数内部使用
i,j:=0,1

// 复数声明
var complexA complex64 = complex(2,4)

```
> 变量初始化的标准格式 var hp int = 100

#### 复数运算法则 

> 在计算机中，复数是由两个浮点数表示的，其中一个表示实部（real），一个表示虚部（imag）

> 加法: z1 = a+bi z2 = c+di  ==> sum = (a+bi)+(c+di) = (a+c)+(b+d)i

> 减法: z1 = a+bi z2 = c+di  ==> diff = (a+bi)-(c+di) = (a-c)+(b-d)i

> 乘法: z1 = a+bi z2 = c+di  ==> mul = (a+bi)(c+di) = (ac-bd)+(bc+ad)i

> 指数,密指




### 匿名变量 _ 

> 匿名变量不占用内存空间，不会分配内存。匿名变量与匿名变量之间也不会因为多次声明而无法使用

```golang
func GetData() (int, int) {
    return 100, 200
}
func main(){
    a, _ := GetData()
    _, b := GetData()
    fmt.Println(a, b)
}
```

----
## Go语言内置的 中字符类型(byte,rune)

> 字符串中的每一个元素叫做“字符”，在遍历或者单个获取字符串元素时可以获得字符 

> Go语言的字符有以下两种

 * 一种是 uint8 类型，或者叫 byte 型，代表了 ASCII 码的一个字符。
 * 另一种是 rune 类型，代表一个 UTF-8 字符，当需要处理中文、日文或者其他复合字符时，则需要用到 rune 类型。rune 类型等价于 int32 类型。

**在 ASCII 码表中，A 的值是 65，使用 16 进制表示则为 41，所以下面的写法是等效的：**

  var ch byte = 65 或 var ch byte = '\x41'      //（\x 总是紧跟着长度为 2 的 16 进制数）

  另外一种可能的写法是\后面紧跟着长度为 3 的八进制数，例如 \377。

> 在书写 Unicode 字符时，需要在 16 进制数之前加上前缀\u或者\U。因为 Unicode 至少占用 2 个字节，所以我们使用 int16 或者 int 类型来表示。如果需要使用到 4 字节，则使用\u前缀，如果需要使用到 8 个字节，则使用\U前缀 

```golang
var ch int = '\u0041'
var ch2 int = '\u03B2'
var ch3 int = '\U00101234'
fmt.Printf("%d - %d - %d\n", ch, ch2, ch3) // integer
fmt.Printf("%c - %c - %c\n", ch, ch2, ch3) // character
fmt.Printf("%X - %X - %X\n", ch, ch2, ch3) // UTF-8 bytes
fmt.Printf("%U - %U - %U", ch, ch2, ch3)   // UTF-8 code point
```

**输出**

  65 - 946 - 1053236
  A - β - r
  41 - 3B2 - 101234
  U+0041 - U+03B2 - U+101234

> UTF-8 和 Unicode 有何区别  

字符集为每个字符分配一个唯一的 ID，我们使用到的所有字符在 Unicode 字符集中都有一个唯一的 ID，例如上面例子中的 a 在 Unicode 与 ASCII 中的编码都是 97。汉字“你”在 Unicode 中的编码为 20320，在不同国家的字符集中，字符所对应的 ID 也会不同。而无论任何情况下，Unicode 中的字符的 ID 都是不会变化的

TF-8 是编码规则，将 Unicode 中字符的 ID 以某种方式进行编码，UTF-8 的是一种变长编码规则，从 1 到 4 个字节不等。编码规则如下：

  - 0xxxxxx 表示文字符号 0～127，兼容 ASCII 字符集。
  - 从 128 到 0x10ffff 表示其他字符。

**广义的 Unicode 指的是一个标准，它定义了字符集及编码规则，即 Unicode 字符集和 UTF-8、UTF-16 编码等**

## 常量

> Go语言中的常量使用关键字 const 定义，用于存储不会改变的数据，常量是在编译时被创建的，即使定义在函数内部也是如此，并且只能是布尔型、数字型（整数型、浮点型和复数）和字符串型。由于编译时的限制，定义常量的表达式必须为能被编译器求值的常量表达式

  const name[type] = value 

### iota 常量生成器

> 常量声明可以使用 iota 常量生成器初始化，它用于生成一组以相似规则初始化的常量，但是不用每行都写一遍初始化表达式。在一个 const 声明语句中，在第一个声明的常量所在的行，iota 将会被置为 0，然后在每一个有常量声明的行加一

```golang
type Weekday int

// 日将对应 0，周一为 1，以此类推
const (
    Sunday Weekday = iota
    Monday
    Tuesday
    Wednesday
    Thursday
    Friday
    Saturday
)
```
## Go语言关键字

||   |   |   |   |   |
:----|:---:|:---:|:---:|:---:|:---:|
|:key:| break | default |   func | interface |select |
|:shit:| case |  defer | go | map |struct
|:heart:|chan | else | goto | package| switch
|:smile:|const | fallthrough | if | range | type
|:cry:|continue | for | import |  return |  var


## 标识符

> 标识符是指Go语言对各种变量、方法、函数等命名时使用的字符序列，标识符由若干个字母、下划线_、和数字组成，且第一个字符必须是字母。通俗的讲就是凡可以自己定义的名称都可以叫做标识符

> int在Go语言中还存在着一些特殊的标识符，叫做预定义标识符

| | | | | | | | | |
:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
append | bool | byte | cap | close |complex |complex64| complex128 |  uint16 |
copy  |false |float32 |float64 |imag | int |int8  |int16 |uint32|
int32 |int64 |iota | len| make|  new| nil| panic| uint64
print |println| real | recover |string | true | uint | uint8| uintptr