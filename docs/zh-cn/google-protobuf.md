# 何为 ProtoBuf

> protocol buffers 是一种语言无关、平台无关、可扩展的序列化结构数据的方法，它可用于（数据）通信协议、数据存储等。
>
> Protocol Buffers 是一种灵活，高效，自动化机制的结构数据序列化方法－可类比 XML，但是比 XML 更小（3 ~ 10倍）、更快（20 ~ 100倍）、更为简单。
>
> 你可以定义数据的结构，然后使用特殊生成的源代码轻松的在各种数据流中使用各种语言进行编写和读取结构数据。你甚至可以更新数据结构，而不破坏由旧数据结构编译的已部署程序
>

简单来讲， ProtoBuf 是结构数据序列化[1] 方法，可简单类比于 XML[2]，其具有以下特点：

  - **语言无关、平台无关** 即 ProtoBuf 支持 Java、C++、Python 等多种语言，支持多个平台
  - **高效** 即比 XML 更小（3 ~ 10倍）、更快（20 ~ 100倍）、更为简单
  - **扩展性、兼容性好** 你可以更新数据结构，而不影响和破坏原有的旧程序

## 使用 ProtoBuf

> **具体该如何使用 ProtoBuf** ?

### 第一步，创建 .proto 文件，定义数据结构

```protobuf
// 例1: 在 xxx.proto 文件中定义 Example1 message
/**
 * 类型：int32、int64、sint32、sint64、string、32-bit ....
 * 字段编号：0 ~ 536870911（除去 19000 到 19999 之间的数字）
 * 字段规则 类型 名称 = 字段编号;
 */
message Example1 {
    optional string stringVal = 1; // 字段规则：optional -> 字段可出现 0 次或1次
    optional bytes bytesVal = 2;
    message EmbeddedMessage {
        int32 int32Val = 1;
        string stringVal = 2;
    }
    optional EmbeddedMessage embeddedExample1 = 3;
    repeated int32 repeatedInt32Val = 4; // 字段规则：required -> 字段只能也必须出现 1 
    repeated string repeatedStringVal = 5; // 字段规则：repeated -> 字段可出现任意多次（包括 0）
}
```

### 第二步，protoc 编译 .proto 文件生成读写接口

> 我们在 .proto 文件中定义了数据结构，这些数据结构是面向开发者和业务程序的，并不面向存储和传输\
> 当需要把这些数据进行存储或传输时，就需要将这些结构数据进行序列化、反序列化以及读写。\
> 通过 protoc 这个编译器,编译 ProtoBuf 为我们提供相应的接口代码。

```shell-script
// $SRC_DIR: .proto 所在的源目录
// --cpp_out: 生成 c++ 代码
// $DST_DIR: 生成代码的目标目录
// xxx.proto: 要针对哪个 proto 文件生成接口代码

protoc -I=$SRC_DIR --cpp_out=$DST_DIR $SRC_DIR/xxx.proto
```

最终生成的代码将提供类似如下的接口:

![](../../assets/img/google/google-protobuf-compile.png)

### 第三步，调用接口实现序列化、反序列化以及读写

针对第一步中例1定义的 message，我们可以调用第二步中生成的接口，实现测试代码如下

```cpp
//
#include <iostream>
#include <fstream>
#include <string>
#include "single_length_delimited_all.pb.h"

int main() {
    Example1 example1;
    example1.set_stringval("hello,world");
    example1.set_bytesval("are you ok?");

    Example1_EmbeddedMessage *embeddedExample2 = new Example1_EmbeddedMessage();

    embeddedExample2->set_int32val(1);
    embeddedExample2->set_stringval("embeddedInfo");
    example1.set_allocated_embeddedexample1(embeddedExample2);

    example1.add_repeatedint32val(2);
    example1.add_repeatedint32val(3);
    example1.add_repeatedstringval("repeated1");
    example1.add_repeatedstringval("repeated2");

    std::string filename = "single_length_delimited_all_example1_val_result";
    std::fstream output(filename, std::ios::out | std::ios::trunc | std::ios::binary);
    if (!example1.SerializeToOstream(&output)) {
        std::cerr << "Failed to write example1." << std::endl;
        exit(-1);
    }

    return 0;
}
```

## ProtoBuf 使用场景

> 官方文档说 ProtoBuf 可类比 XML 或 JSON,那么 ProtoBuf 是否就等同于 XML 和 JSON 呢，它们是否具有完全相同的应用场景呢?

**如果要将 ProtoBuf、XML、JSON 三者放到一起去比较，应该区分两个维度。**

  一个是数据结构化，一个是数据序列化

这里的数据结构化主要面向开发或业务层面，数据序列化面向通信或存储层面，当然数据序列化也需要“结构”和“格式”，所以这两者之间的区别主要在于面向领域和场景不同，一般要求和侧重点也会有所不同。数据结构化侧重人类可读性甚至有时会强调语义表达能力，而数据序列化侧重效率和压缩

XML 作为一种扩展标记语言，JSON 作为源于 JS 的数据格式，都具有数据结构化的能力

例如 XML 可以衍生出 HTML （虽然 HTML 早于 XML，但从概念上讲，HTML 只是预定义标签的 XML），HTML 的作用是标记和表达万维网中资源的结构，以便浏览器更好的展示万维网资源，同时也要尽可能保证其人类可读以便开发人员进行编辑，这就是面向业务或开发层面的数据结构化

再如 XML 还可衍生出 RDF/RDFS，进一步表达语义网中资源的关系和语义，同样它强调数据结构化的能力和人类可读

JSON 也是同理，在很多场合更多的是体现了数据结构化的能力，例如作为交互接口的数据结构的表达。在 MongoDB 中采用 JSON 作为查询语句，也是在发挥其数据结构化的能力

当然，JSON、XML 同样也可以直接被用来数据序列化，实际上很多时候它们也是这么被使用的，例如直接采用 JSON、XML 进行网络通信传输，此时 JSON、XML 就成了一种序列化格式，它发挥了数据序列化的能力。但是经常这么被使用，不代表这么做就是合理。实际将 JSON、XML 直接作用数据序列化通常并不是最优选择，因为它们在速度、效率、空间上并不是最优。换句话说它们更适合数据结构化而非数据序列化。

> 同样的 ProtoBuf 也具有数据结构化的能力，其实也就是上面介绍的 message 定义。我们能够在 .proto 文件中，通过 message、import、内嵌 message 等语法来实现数据结构化，但是很容易能够看出，ProtoBuf 在数据结构化方面和 XML、JSON 相差较大，人类可读性较差，不适合上面提到的 XML、JSON 的一些应用场景

> 但是如果从数据序列化的角度你会发现 ProtoBuf 有着明显的优势，效率、速度、空间几乎全面占优，看完后面的 ProtoBuf 编码的文章，你更会了解 ProtoBuf 是如何极尽所能的压榨每一寸空间和性能，而其中的编码原理正是 ProtoBuf 的关键所在，message 的表达能力并不是 ProtoBuf 最关键的重点。所以可以看出 ProtoBuf 重点侧重于数据序列化 而非 数据结构化

### 总结

- XML、JSON、ProtoBuf 都具有数据结构化和数据序列化的能力
- XML、JSON 更注重数据结构化，关注人类可读性和语义表达能力。ProtoBuf 更注重数据序列化，关注效率、空间、速度，人类可读性差，语义表达能力不足（为保证极致的效率，会舍弃一部分元信息）
- ProtoBuf 的应用场景更为明确，XML、JSON 的应用场景更为丰富

----

## Protobuf 编码结构

ProtoBuf 是如何尽其所能的压榨编码性能和效率的?

> TLV 格式是我们比较熟悉的编码格式。\
> 所谓的 TLV 即 Tag - Length - Value。Tag 作为该字段的唯一标识，Length 代表 Value 数据域的长度，最后的 Value 便是数据本身。

ProtoBuf 编码采用类似的结构，但是实际上又有较大区别，其编码结构可见下图:

![](../../assets/img/google/protobuf-compress-struct.png)

首先，每一个 message 进行编码，其结果由一个个字段组成，每个字段可划分为 Tag - [Length] - Value，如下图所示

![](../../assets/img/google/protobuf-tlv-struct.png)

> 特别注意这里的 [Length] 是可选的，含义是针对不同类型的数据编码结构可能会变成 Tag - Value 的形式，如果变成这样的形式，没有了 Length 我们该如何确定 Value 的边界？答案就是 Varint 编码

继续深入 Tag ，Tag 由 field_number 和 wire_type 两个部分组成：

  - field_number: message 定义字段时指定的字段编号
  - wire_type: ProtoBuf 编码类型，根据这个类型选择不同的 Value 编码方案。

![](../../assets/img/google/protobuf-tlv-tag.png)

> 3 bit 的 wire_type 最多可以表达 8 种编码类型，目前 ProtoBuf 已经定义了 6 种

 Type | Meaning | Used For 
:--:|:--:|:--|
0|Varint|int32,int64,uint32,uint64,sint32,sint64,bool,enum
1|64-bit| fixed64,sfixed64,double
2|Length-delimited|string,bytes,embedded message,packed repeated field
3|Start group| groups(deprecated)
4|End group|groups(deprecated)
5|32-bit|fixed32,sfixed32,float

> 虽然 wire_type 代表编码类型，但是 Varint 这个编码类型里针对 sint32、sint64 又会有一些特别编码（ZigTag 编码）处理，相当于 Varint 这个编码类型里又存在两种不同编码

![](../../assets/img/google/protobuf-encoding-complete.png)

一个 message 编码将由一个个的 field 组成，每个 field 根据类型将有如下两种格式：

- Tag - Length - Value：编码类型表中 Type = 2 即 Length-delimited 编码类型将使用这种结构，
- Tag - Value：编码类型表中 Varint、64-bit、32-bit 使用这种结构。

### Varints 编码

Varints 编码的规则主要为以下三点:

  - 1.在每个字节开头的 bit 设置了 msb(most significant bit )，标识是否需要继续读取下一个字节
  - 2.存储数字对应的二进制补码
  - 3.补码的低位排在前面. (主要是为编码实现（移位操作）做的一个小优化)

```protobuf
int32 val =  1;  // 设置一个 int32 的字段的值 val = 1; 这时编码的结果如下
原码：0000 ... 0000 0001  // 1 的原码表示
补码：0000 ... 0000 0001  // 1 的补码表示
Varints 编码：0#000 0001（0x01）  // 1 的 Varints 编码，其中第一个字节的 msb = 0
```

#### 编码过程：
数字 1 对应补码 0000 ... 0000 0001（规则 2），从末端开始取每 7 位一组并且反转排序（规则 3），因为 0000 ... 0000 0001 除了第一个取出的 7 位组（即原数列的后 7 位），剩下的均为 0。所以只需取第一个 7 位组，无需再取下一个 7 bit，那么第一个 7 位组的 msb = 0。最终得到

```protobuf
0 | 000 0001（0x01） 
```
  

#### 解码过程:

编码结果为 0#000 0001（0x01）。首先，每个字节的第一个 bit 为 msb 位，msb = 1 表示需要再读一个字节（还未结束），msb = 0 表示无需再读字节（读取到此为止）

在上面的例子中，数字 1 的 Varints 编码中 msb = 0，所以只需要读完第一个字节无需再读。去掉 msb 之后，剩下的 000 0001 就是补码的逆序，但是这里只有一个字节，所以无需反转，直接解释补码 000 0001，还原即为数字 1

> 这里编码数字 1，Varints 只使用了 1 个字节。而正常情况下 int32 将使用 4 个字节存储数字 1

**再看一个需要两个字节的数字 666 的编码**

```protobuf
int32 val = 666; // 设置一个 int32 的字段的值 val = 666; 这时编码的结果如下
原码：000 ... 101 0011010  // 666 的源码
补码：000 ... 101 0011010  // 666 的补码
Varints 编码：1#0011010  0#000 0101 （9a 05）  // 666 的 Varints 编码

```

> 编码过程：

666 的补码为 000 ... 101 0011010，从后依次向前取 7 位组并反转排序，则得到

```protobuf
  0011010 | 0000101
```
  

加上 msb，则

```protobuf
1 0011010 | 0 0000101 （0x9a 0x05）
```
    

> 解码过程：

编码结果为 1#0011010 0#000 0101 （9a 05），与第一个例子类似，但是这里的第一个字节 msb = 1，所以需要再读一个字节，第二个字节的 msb = 0，则读取两个字节后停止。读到两个字节后先去掉两个 msb，剩下

```protobuf
0011010  000 0101
```
将这两个 7-bit 组反转得到补码

```protobuf
000 0101 0011010
```

然后还原其原码为 666。

> 这里编码数字 666，Varints 只使用了 2 个字节。而正常情况下 int32 将使用 4 个字节存储数字 666

仔细品味上述的 Varints 编码，我们可以发现 Varints 的本质实际上是每个字节都牺牲一个 bit 位（msb），来表示是否已经结束（是否还需要读取下一个字节），msb 实际上就起到了 Length 的作用，正因为有了 msb（Length），所以我们可以摆脱原来那种无论数字大小都必须分配四个字节的窘境。通过 Varints 我们可以让小的数字用更少的字节表示。从而提高了空间利用和效率

**总结**



>> 因为每个字节都拿出一个 bit 做 msb，而原先这个 bit 是可直接用来表示 Value 的，现在每个字节都少了一个 bit 位即只有 7 位能真正用来表达 Value。那就意味这 4 个字节能表达的最大数字为 2^28，而不再是 2^32 了。\
>> 这意味着当数字大于 2^28   时，采用 Varints 编码将导致分配 5 个字节，而原先明明只需要 4 个字节，此时 Varints 编码的效率不仅不是提高反而是下降。\
>> 但这并不影响 Varints 在实际应用时的高效，因为事实证明，在大多数情况下，小于 2^28 的数字比大于 2^28的数字出现的更为频繁。


> 当前的 Varints 编码却存在着明显缺陷。我们的例子好像只给出了正数，我们来看一下负数的 Varints 编码情况.

```protobuf
int32 val = -1
原码：1000 ... 0001  // 注意这里是 8 个字节
补码：1111 ... 1111  // 注意这里是 8 个字节
再次复习 Varints 编码：对补码取 7 bit 一组，低位放在前面。
上述补码 8 个字节共 64 bit，可分 9 组且这 9 组均为 1，这 9 组的 msb 均为 1（因为还有最后一组）
最后剩下一个 bit 的 1，用 0 补齐作为最后一组放在最后，最后得到 Varints 编码
Varints 编码：1#1111111 ... 0#000 0001 （FF FF FF FF FF FF FF FF FF 01）
```

注意，因为负数必须在最高位（符号位）置 1，这一点意味着无论如何，负数都必须占用所有字节，所以它的补码总是占满 8 个字节。你没法像正数那样去掉多余的高位（都是 0）。再加上 msb，最终 Varints 编码的结果将固定在 10 个字节

int32 不应该是 4 个字节吗？这里是 ProtoBuf 基于兼容性的考虑（比如开发者将 int64 的字段改成 int32 后应当不影响旧程序），而将 int32 扩展成 int64 的八个字节。

所以目前的情况是我们定义了一个 int32 类型的变量，如果将变量值设置为负数，那么直接采用 Varints 编码的话，其编码结果将总是占用十个字节，这显然不是我们希望得到的结果

### ZigZag 编码

为解决Varints 编码对负数编码效率低的问题，ProtoBuf 为我们提供了 sint32、sint64 两种类型，当你在使用这两种类型定义字段时，ProtoBuf 将使用 ZigZag 编码，而 ZigZag 编码将解决负数编码效率低的问题

> ZigZag 编码：有符号整数映射到无符号整数，然后再使用 Varints 编码

![](../../assets/img/google/protobuf-zigzag.png)

ZigZag 编码的思维不难理解，既然负数的 Varints 编码效率很低，那么就将负数映射到正数，然后对映射后的正数进行 Varints 编码。解码时，解出正数之后再按映射关系映射回原来的负数.