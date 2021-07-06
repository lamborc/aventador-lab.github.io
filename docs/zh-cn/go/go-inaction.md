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


### 基础语法

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

### go 中字符类型(byte,rune)

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

### go 的各路语法

```golang
func Sha(){}    // 方法名首字母大写,就是将方法暴露, 在node 里module.exports or export ;
func privateSha() {}  // 小写私有
```


