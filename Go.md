# 第一章、Golang概述

## 1. Golang的概述

### 1.1 Go语言特点

1. Go语言保证了技能达到静态编译语言的安全和性能，又达到了动态开发维护的高效率

2. 从 C 语言中继承了很多理念，包括表达式语法，控制结构，基础数据类型，调用参数传值，指针等

3. 引入包的概念，用于组织程序结构，**Go** 语言的一个文件都要归属于一个包，而不能单独存在。

4. 垃圾回收机制，内存自动回收，不需开发人员管理

5. 天然并发 (重要特点)

   (1) 从语言层面支持并发，实现简单

   (2) goroutine，轻量级线程，可实现大并发处理，高效利用多核。

   (3) 基于 CPS 并发模型(Communicating Sequential Processes )实现

6. 吸收了管道通信机制，形成 Go 语言特有的管道 channel 通过管道 channel , 可以实现不同的 goroute之间的相互通信。

7. 函数可以返回多个值。

```go
//写一个函数，实现同时返回 和，差
//go 函数支持返回多个值
func getSumAndSub(n1 int, n2 int) (int, int ) {
sum := n1 + n2 //go 语句后面不要带分号.
sub := n1 - n2
return sum , sub
}
```

8. 新的创新：比如切片 slice、延时执行 defer

9. go build 命令 编译成可执行文件，go run 直接编译运行

### 1.2 注意事项

​	1) Go 源文件以 "go" 为扩展名。

​	2) Go 应用程序的执行入口是 main()函数。 这个是和其它编程语言（比如 java/c）

​	3) Go 语言严格区分大小写。

​	4) Go 方法由一条条语句构成，每个语句后不需要分号(Go 语言会在每行后自动加分号)，这也体现出 Golang 的简洁性

​	5) Go 编译器是一行行进行编译的，因此我们一行就写一条语句，不能把多条语句写在同一个，否则报错，可以用分号间隔，但是不推荐

​	6) go 语言定义的变量或者 **import** 的包如果没有使用到，代码不能编译通过。

### 1.3 转义字符

​	1) \t : 表示一个制表符，通常使用它可以排版，一个Tab。

​	2) \n	：换行符	fmt.Println("天龙八部雪山飞狐\r 张飞");

​	3) \ \	：一个\	

​	4) \ "	：一个"	

​	5) \r	：一个回车，从当前行的最前面开始输出，覆盖掉以前内容

### 1.4 gofmt

​	gofmt -w main.go  将格式化后的内容重新写入文件，和gg=G类似

# 第二章、数据类型

通用：

```go
%v	值的默认格式表示
%+v	类似%v，但输出结构体时会添加字段名
%#v	值的Go语法表示
%T	值的类型的Go语法表示
%%	百分号
```

布尔值：

```go
%t	单词true或false
```

整数：

```go
%b	表示为二进制
%c	该值对应的unicode码值
%d	表示为十进制
%o	表示为八进制
%q	该值对应的单引号括起来的go语法字符字面值，必要时会采用安全的转义表示
%x	表示为十六进制，使用a-f
%X	表示为十六进制，使用A-F
%U	表示为Unicode格式：U+1234，等价于"U+%04X"
```

浮点数与复数的两个组分：

```go
%b	无小数部分、二进制指数的科学计数法，如-123456p-78；参见strconv.FormatFloat
%e	科学计数法，如-1234.456e+78
%E	科学计数法，如-1234.456E+78
%f	有小数部分但无指数部分，如123.456
%F	等价于%f
%g	根据实际情况采用%e或%f格式（以获得更简洁、准确的输出）
%G	根据实际情况采用%E或%F格式（以获得更简洁、准确的输出）
```

字符串和[]byte：

```go
%s	直接输出字符串或者[]byte
%q	该值对应的双引号括起来的go语法字符串字面值，必要时会采用安全的转义表示
%x	每个字节用两字符十六进制数表示（使用a-f）
%X	每个字节用两字符十六进制数表示（使用A-F）    
```

指针：

```go
%p	表示为十六进制，并加上前导的0x    
```

没有%u。整数如果是无符号类型自然输出也是无符号的。类似的，也没有必要指定操作数的尺寸（int8，int64）。

宽度通过一个紧跟在百分号后面的十进制数指定，如果未指定宽度，则表示值时除必需之外不作填充。精度通过（可选的）宽度后跟点号后跟的十进制数指定。如果未指定精度，会使用默认精度；如果点号后没有跟数字，表示精度为0。举例如下：

```go
%f:    默认宽度，默认精度
%9f    宽度9，默认精度
%.2f   默认宽度，精度2
%9.2f  宽度9，精度2
%9.f   宽度9，精度0
```

|   类型    | 有无符号 |     范围     |
| :-------: | :------: | :----------: |
| **int8**  |    有    |   -128~127   |
| **int16** |    有    | -2^15~2^15-1 |
| **int32** |    有    | -2^31~2^31-1 |
| **int64** |    有    | -2^64~2^64-1 |



## 1.变量使用的三种方式

(1) 第一种：指定变量类型，声明后若不赋值，使用默认值，不同数据类型的默认值不同，int类型默认值为0；string 默认值为空串，小数默认为 0，bool默认是false

```go
var i int
```

​	(2) 第二种：根据值自行判定变量类型(类型推导)

```go
var num=10.11
```

​	(3) 第三种：省略 var, 注意 :=左侧的变量不应该是已经声明过的，否则会导致编译错误

```go
name :="tom" //等价于 var name string; name = "tom"
```

4) 多变量声明，一次性声明多个变量

```go
n1,name,n3 :=100,"tome",888
```

```go
//一次性声明多个全局变量
//在go中，函数外部定义变量就是全局变量
var (n3 = 300 n4 = 900 name2 = "tony")
```

## 2.变量基本操作

查看某个变量的字节大小和数据类型----unsafe包提供函数

```go
var n2 int64 = 10
unsafe.Sizeof(n2)
```

1) 如果我们保存的字符在 ASCII 表的,比如[0-1, a-z,A-Z..]直接可以保存到 byte类型

2) 如果我们保存的字符对应码值大于 255,这时我们可以考虑使用 int 类型保存

3) 如果我们需要按照字符的方式输出，这时我们需要格式化输出，即 fmt.Printf(“%c”, c1)

## 3.字符类型的使用细节

```go
1. 字符常量是用单引号('')括起来的单个字符。例如：var c1	byte = 'a'	var c2 int = '中'	var	c3
   byte =	'9'	= ‘\n’

2. Go 中允许使用转义字符 '\’来将其后的字符转变为特殊字符型常量。例如：var c3 char	// '\n'表示换行符

3. Go 语 言 的 字 符 使 用 UTF-8 编 码	英文字母-1 个字节	汉字-3 个字节

4. 在 Go 中，字符的本质是一个整数，直接输出时，是该字符对应的 UTF-8 编码的码值。

5. 可以直接给某个变量赋一个数字，然后按格式化输出时%c，会输出该数字对应的 unicode 字符

6. 字符类型是可以进行运算的，相当于一个整数，因为它都对应有 Unicode 码.
```

## 4.string类型使用细节

1. 字符串一旦赋值了，字符串就不能修改了：在 Go 中字符串是不可变的。

2. 字符串的两种表示形式

   (1) 双引号 "", 会识别转义字符

   (2) 反引号 ``，以字符串的原生形式输出，包括换行和特殊字符，可以实现防止攻击、输出源代码等效果

3. 字符串可以用 + 号拼接(中间无空格)

```go
var str ="hello"+"world"
str += "haha!"
```

4.当一个拼接的操作很长需分行写时，可用+号拼接，但须注意，+ 要保留在上一行

```go
str := "hello" + "world" +
"hello" 
```

## 5. 基本数据类型的相互转换

### 5.1 基本语法

**go中不存在隐式转换，数据类型之间的转换都要显式进行**

表达式 T(v) 将值 v 转换为类型 T

**T**: 就是数据类型，比如 int32，int64，float32 等等

**v**: 就是需要转换的变量

### 5.2 注意事项

1. Go 中，数据类型的转换可以是从 表示范围小-->表示范围大，也可以 范围大--->范围小

2. 转化之后要用一个新的值接收转换的变量，被转换的是变量存储的数据(即值)，变量本身的数据类型并没有变化

```go
var i int32 =100;
var n1 float32=float32(i)
var n2 int64=int64(i) //低精度-->高精度
```

3. 在转换中，比如将 int64	转成 int8 【-128---127】 ，编译时不会报错，只是转换的结果是按溢出处理。 因此在转换时，需要考虑范围.

### 5.3 基本数据类型和string转换

方式 1：fmt.Sprintf("%参数",	表达式)，会返回转换后的字符串

```go
package main
import (
	"fmt"
	_ "unsafe"
	"strconv"
)

//基本数据练习转成string使用
func main() {

	var num1 int = 99
	var num2 float64 = 23.456
	var b bool = true
	var myChar byte = 'h'
	var str string //空的str

	//使用第一种方式来转换 fmt.Sprintf方法

	str = fmt.Sprintf("%d", num1)
	fmt.Printf("str type %T str=%q\n", str, str)

	str = fmt.Sprintf("%f", num2)
	fmt.Printf("str type %T str=%q\n", str, str)

	str = fmt.Sprintf("%t", b)
	fmt.Printf("str type %T str=%q\n", str, str)

	str = fmt.Sprintf("%c", myChar)
	fmt.Printf("str type %T str=%q\n", str, str)

	//第二种方式 strconv 函数 
	var num3 int = 99
	var num4 float64 = 23.456
	var b2 bool = true

	str = strconv.FormatInt(int64(num3), 10)
	fmt.Printf("str type %T str=%q\n", str, str)
	
	// strconv.FormatFloat(num4, 'f', 10, 64)
	// 说明： 'f' 格式 10：表示小数位保留10位 64 :表示这个小数是float64
	str = strconv.FormatFloat(num4, 'f', 10, 64)
	fmt.Printf("str type %T str=%q\n", str, str)

	str = strconv.FormatBool(b2)
	fmt.Printf("str type %T str=%q\n", str, str)

	//strconv包中有一个函数Itoa
	var num5 int64 = 4567
	str = strconv.Itoa(int(num5))
	fmt.Printf("str type %T str=%q\n", str, str)

}
```

### 5.4 string类型转基本数据类型

```go
package main
import (
	"fmt"
	"strconv"
)

//string转成基本数据类型
func main() {

	var str string = "true"
	var b bool
	// b, _ = strconv.ParseBool(str)
	// 说明
	// 1. strconv.ParseBool(str) 函数会返回两个值 (value bool, err error)
	// 2. 因为我只想获取到 value bool ,不想获取 err 所以我使用_忽略
	b , _ = strconv.ParseBool(str)
	fmt.Printf("b type %T  b=%v\n", b, b)
	
	var str2 string = "1234590"
	var n1 int64
	var n2 int
	n1, _ = strconv.ParseInt(str2, 10, 64)
	n2 = int(n1)
	fmt.Printf("n1 type %T  n1=%v\n", n1, n1)
	fmt.Printf("n2 type %T n2=%v\n", n2, n2)

	var str3 string = "123.456"
	var f1 float64
	f1, _ = strconv.ParseFloat(str3, 64)
	fmt.Printf("f1 type %T f1=%v\n", f1, f1)


	//注意：
	var str4 string = "hello"
	var n3 int64 = 11
	n3, _ = strconv.ParseInt(str4, 10, 64)
	fmt.Printf("n3 type %T n3=%v\n", n3, n3)

}
```

**注意事项**：在将 String 类型转成 基本数据类型时，要确保 **String** 类型能够转成有效的数据，比如 我们可以把 "123" , 转成一个整数，但是不能把 "hello" 转成一个整数，如果这样做，Golang 直接将其转成 0 ，其它类型也是一样的道理. float => 0 bool => false；

## 6. 指针类型

1. 基本数据类型，变量存的就是值，也叫值类型

2. 获取变量的地址，用&，比如： var num int, 获取 num 的地址：&num

![image-20211020094506632](E:\typroa_pic\image-20211020094506632.png)  

3. 指针类型，指针变量存的是一个地址，这个地址指向的空间存的才是值
   比如：var ptr *int = &num

![image-20211020094514944](E:\typroa_pic\image-20211020094514944.png)

4. 获取指针类型所指向的值，使用：*，比如：var ptr *int, 使用*ptr 获取 ptr 指向的值

```go
package main
import (
	"fmt"
)
//golang中指针类型
func main() {

	//基本数据类型在内存布局
	var i int = 10
	// i 的地址是什么,&i
	fmt.Println("i的地址=", &i)
	
	//下面的 var ptr *int = &i
	//1. ptr 是一个指针变量
	//2. ptr 的类型 *int
	//3. ptr 本身的值&i
	var ptr *int = &i 
	fmt.Printf("ptr=%v\n", ptr)
	fmt.Printf("ptr 的地址=%v", &ptr) 
	fmt.Printf("ptr 指向的值=%v", *ptr)

}
```

## 7.值类型和引用类型

### 6.1值类型

值类型：基本数据类型 int 系列, float 系列, bool, string 、数组和结构体 struct；

值类型：变量直接存储值，内存通常在栈中分配

![image-20211020144359039](E:\typroa_pic\image-20211020144359039.png)

### 6.2引用类型

引用类型：指针、slice 切片、map、管道 chan、interface 等都是引用类型

引用类型：变量存储的是一个地址，这个地址对应的空间才真正存储数据(值)，内存通常在堆上分配，当没有任何变量引用这个地址时，该地址对应的数据空间就成为一个垃圾，由 GC 来回收

![image-20211020144403727](E:\typroa_pic\image-20211020144403727.png)



![image-20211020100309942](E:\typroa_pic\image-20211020100309942.png)

ps：只是通常，go有一个逃逸分析对变量进行判断

## 8.标识符的命名规范

1) 由 26 个英文字母大小写，0-9 ，_ 组成

2) 数字不可以开头。var num int //ok	var 3num int //error

3) Golang 中严格区分大小写。
var num int
var Num int
说明：在 golang 中，num 和 Num 是两个不同的变量

4) 标识符不能包含空格

5) 下划线"_"本身在 Go 中是一个特殊的标识符，称为空标识符。可以代表任何其它的标识符，但是它对应的值会被忽略(比如：忽略某个返回值)。所以仅能被作为占位符使用，不能作为标识符使用

6) 不能以系统保留关键字作为标识符（一共有 25 个），比如 break，if 等等...

7）int 和float32 可以用作命名（int和float32不属于保留关键字），但是不建议使用；

8）**如果变量名、函数名、常量名首字母大写，则可以被其他的包访问；如果首字母小写，则只能在本包中使用 ( 注：可以简单的理解成，首字母大写是公有的，首字母小写是私有的) ,在 golang 没有public , private 等关键字。**

## 9.系统保留关键字和预定义标识符

![image-20211020102747023](E:\typroa_pic\image-20211020102747023.png)

![image-20211020102756697](E:\typroa_pic\image-20211020102756697.png)

# 第三章、运算符

## 1. 算数运算符

![image-20211020103416573](E:\typroa_pic\image-20211020103416573.png)

ps:

1. 对于除号 "/"，它的整数除和小数除是有区别的：整数之间做除法时，只保留整数部分而舍弃小数部分。 例如： x := 19/5 ,结果是	3

2. 当对一个数取模时，可以等价 a%b=a-a/b*b ， 这样我们可以看到 取模的一个本质运算。

```go
% 的使用特点
// % 的使用
// 看公式 a % b = a - a / b * b
fmt.Println("10%3=", 10 % 3) // =1
fmt.Println("-10%3=", -10 % 3) // = -10 - (-10) / 3 * 3 = -10 - (-9) = -1
fmt.Println("10%-3=", 10 % -3) // =1
fmt.Println("-10%-3=", -10 % -3) // =-1
```

3. Golang 的自增自减只能当做一个独立语言使用时，不能这样使用

![image-20211020105101619](E:\typroa_pic\image-20211020105101619.png)

4. Golang 的++ 和 -- 只能写在变量的后面，不能写在变量的前面，即：只有 a++ ，a-- 没有 ++a，--a







## 2. 关系运算符

![image-20211020103507499](E:\typroa_pic\image-20211020103507499.png)

PS：

1. 关系运算符的结果都是 bool 型，也就是要么是 true，要么是 false。

2. 关系运算符组成的表达式，我们称为关系表达式： a > b

3. 比较运算符"=="不能误写成 "=" !!

## 3. 逻辑运算符

![image-20211020103824876](E:\typroa_pic\image-20211020103824876.png)

PS：

1. &&也叫短路与：如果第一个条件为 **false**，则第二个条件不会判断，最终结果为 **false**

2. ||也叫短路或：如果第一个条件为 **true**，则第二个条件不会判断，最终结果为 **true**

## 4. 赋值运算符

![image-20211020105356508](E:\typroa_pic\image-20211020105356508.png)

![image-20211020105422659](E:\typroa_pic\image-20211020105422659.png)

ps：运算顺序从右往左

## 5. 位运算符

![image-20211020105730101](E:\typroa_pic\image-20211020105730101.png)

右移和左移,运算规则:
右移运算符	>>：低位溢出,符号位不变,并用符号位补溢出的高位	
左移运算符	<<:	符 号位不变,低位补 0	

a := 1 >> 2	// 0000 0001 =>0000 0000 = 0

c := 1 << 2	// 0000 0001 ==> 0000 0100 => 4



## 6. 运算符优先级

![image-20211020105822973](E:\typroa_pic\image-20211020105822973.png)

1. 运算符有不同的优先级，所谓优先级就是表达式运算中的运算顺序。如表，上一行运算符总优先于下一行。

2. 只有单目运算符、赋值运算符是从右向左运算的。

3. 梳理了一个大概的优先级
   	1：括号，++, --
   	2:	单目运算
   	3：算术运算符
   	4：移位运算
   	5：关系运算符
   	6：位运算符
   	7：逻辑运算符
   	8：赋值运算符
   	9：逗号

## 7.原码补码反码

![image-20211020111633826](E:\typroa_pic\image-20211020111633826.png)

# 第四章、流程控制

## 1. 顺序控制

（同C,略）

## 2.分支控制

### 2. 1 单分支

![image-20211020112811095](E:\typroa_pic\image-20211020112811095.png)

ps:支持在if 中定义变量再判断，中间用 ; 隔开

### 2.2 双分支

（同C,略）

### 2.3 switch分支

1. case/switch 后是一个表达式( 即：常量值、变量、一个有返回值的函数等都可以)

![image-20211020113545614](E:\typroa_pic\image-20211020113545614.png)

![image-20211020113552303](E:\typroa_pic\image-20211020113552303.png)

2. case 后的各个表达式的值的数据类型，必须和 switch 的表达式数据类型一致

![image-20211020113610550](E:\typroa_pic\image-20211020113610550.png)

3. case 后面可以带多个表达式，使用逗号间隔。比如 case 表达式 1, 表达式 2 ...

![image-20211020113621719](E:\typroa_pic\image-20211020113621719.png)

4. case 后面的表达式如果是常量值(字面量)，则要求不能重复

5. case 后面不需要带 break , 程序匹配到一个 case 后就会执行对应的代码块，然后退出 switch，如

果一个都匹配不到，则执行 default

6. default 语句不是必须的.

7. switch 后也可以不带表达式，类似 if --else 分支来使用

![image-20211020113701400](E:\typroa_pic\image-20211020113701400.png)

8. switch 穿透-fallthrough ，如果在 case 语句块后增加 fallthrough ,则会继续执行下一个 case，也叫 switch 穿透

## 3. 循环控制

### 1. for循环控制

1.第一种实现方式

![image-20211020122203290](E:\typroa_pic\image-20211020122203290.png)

2.第二种实现方式

![image-20211020122214320](E:\typroa_pic\image-20211020122214320.png)

如果我们的字符串含有中文，那么传统的遍历字符串方式，就是错误，会出现乱码。原因是传统的对字符串的遍历是按照字节来遍历，而一个汉字在 utf8 编码是对应 3 个字节。如何解决 需要要将	str 转成 []rune 切片.

![image-20211020122522111](E:\typroa_pic\image-20211020122522111.png)

3.第三种实现方式

![image-20211020122239624](E:\typroa_pic\image-20211020122239624.png)

for	{	
//循环执行语句
}
上面的写法等价 for ; ; {}	是一个无限循环， 通常需要配合 break 语句使用

#### 1.1 for-range方式

![image-20211020122408016](E:\typroa_pic\image-20211020122408016.png)

对应 for-range 遍历方式而言，是按照字符方式遍历。因此如果有字符串有中文，也是 ok

![image-20211020122540184](E:\typroa_pic\image-20211020122540184.png)

### 2. while和do…while的实现

**ps:Go 中没有 while 和 do...while 语法可以通过 for循环来实现其使用效果。**

#### 2.1 while

![image-20211020122929054](E:\typroa_pic\image-20211020122929054.png)

1) for 循环是一个无限循环

2) break 语句就是跳出 for 循环

#### 2.2 do…while实现

![image-20211020123003193](E:\typroa_pic\image-20211020123003193.png)

1) 上面的循环是先执行，在判断，因此至少执行一次。

2) 当循环条件成立后，就会执行 break, break 就是跳出 for 循环，结束循环.



### 3.跳转控制语句

#### 1.break和continue

break 语句出现在多层嵌套的语句块中时，可以通过标签指明要终止的是哪一层语句块

![image-20211020124049942](E:\typroa_pic\image-20211020124049942.png)

continue 语句用于结束本次循环，继续执行下一次循环。

continue 语句出现在多层嵌套的循环语句体中时，可以通过标签指明要跳过的是哪一层循环 ,

#### 2. goto

1. Go 语言的 goto 语句可以无条件地转移到程序中指定的行。

2. goto 语句通常与条件语句配合使用。可用来实现条件转移，跳出循环体等功能。

3. 在 Go 程序设计中一般不主张使用 goto 语句， 以免造成程序流程的混乱，使理解和调试程序都产生困难

![image-20211020124432641](E:\typroa_pic\image-20211020124432641.png)

![image-20211020124437817](E:\typroa_pic\image-20211020124437817.png)



# 第五章、包和函数

## 1.包

1. 说明：go 的每一个文件都是属于一个包的，也就是说 go 是以包的形式来管理文件和项目目录结构的

2. 作用：

   ​	区分相同名字的函数、变量等标识符

   ​	当程序文件很多时,可以很好的管理项目

   ​	控制函数、变量等访问范围，即作用域

3. 打包基本语法

   ​		package 包名

   引入包的基本语法
   		import "包的路径"



4. 在给一个文件打包时，该包对应一个文件夹，比如这里的 utils 文件夹对应的包名就是 utils,文件的包名通常和文件所在的文件夹名一致，一般为小写字母。

5. 当一个文件要使用其它包函数或变量时，需要先引入对应的包
   引入方式 1：import	"包名"
   引入方式 2：	

```go
	import	(	
"包名"
"包名"
)
```

package 指令在 文件第一行，然后是 import 指令。在 import 包时，路径从 $GOPATH 的	src 下开始，不用带 src , 编译器会自动从 src 下开始引入

6. 为了让其它包的文件，可以访问到本包的函数，则该函数名的首字母需要大写，类似其它语言的 public ,这样才能跨包访问。比如 utils.go 的package 指令在 文件第一行，然后是 import 指令。
   在 import 包时，路径从 $GOPATH 的	src 下开始，不用带 src , 编译器会自动从 src 下开始引入.

7. 为了让其它包的文件，可以访问到本包的函数，则该函数名的首字母需要大写，类似其它语言的 public ,这样才能跨包访问。

8. 在访问其它包函数，变量时，其语法是 包名.函数名， 比如这里的 main.go 文件中

![image-20211020125816057](E:\typroa_pic\image-20211020125816057.png)

9. 如果包名较长，Go 支持给包取别名， 注意细节：取别名后，原来的包名就不能使用了

![image-20211020125809987](E:\typroa_pic\image-20211020125809987.png)

10. 如果你要编译成一个可执行程序文件，就需要将这个包声明为 main , 即 package main .这个就是一个语法规范，如果你是写一个库 ，包名可以自定义

## 2.函数

### 2.1.基本语法

![image-20211020143552432](E:\typroa_pic\image-20211020143552432.png)

![image-20211020143640208](E:\typroa_pic\image-20211020143640208.png)

![image-20211020143642570](E:\typroa_pic\image-20211020143642570.png)



go语言中函数可以有多个返回值：

返回多个值时：在接受的时候，如果希望忽略某个返回值，则使用_符号占位忽略；

返回值只有一个时，（返回值类型列表）可以不写（）

### 2.2 注意事项和细节

1) 函数的形参列表可以是多个，返回值列表也可以是多个。

2) 形参列表和返回值列表的数据类型可以是值类型和引用类型。

3) 函数的命名遵循标识符命名规范，首字母不能是数字，首字母大写该函数可以被本包文件和其它包文件使用，类似 public , 首字母小写，只能被本包文件使用，其它包文件不能使用，类似 private

4.基本数据类型和数组默认都是值传递的，即进行值拷贝。在函数内修改，不会影响到原来的值

5.如果希望函数内的变量能修改函数外的变量(指的是默认以值传递的方式的数据类型)，可以传入变量的地址&，函数内以指针的方式操作变量。从效果上看类似引用

![image-20211020144712929](E:\typroa_pic\image-20211020144712929.png)

6.Go 函数不支持函数重载

7.在 Go 中，函数也是一种数据类型，可以赋值给一个变量，则该变量就是一个函数类型的变量了。通过该变 量可以对函数调用

![image-20211020144811113](E:\typroa_pic\image-20211020144811113.png)

8.函数既然是一种数据类型，因此在 Go 中，函数可以作为形参，并且调用

![image-20211020145026624](E:\typroa_pic\image-20211020145026624.png)

![image-20211020145030304](E:\typroa_pic\image-20211020145030304.png)

9.为了简化数据类型定义，Go 支持自定义数据类型
基本语法：type 自定义数据类型名	数据类型	//	理解: 相当于一个别名	
案例：type myInt int	// 这时 myInt 就等价 int 来使用了.	

案例：type mySum	func (int, int) int	// 这时 mySum 就等价 一个 函数类型 func (int, int) int

10.支持对函数返回值命名

此时不需要在函数内部重新定义变量

![image-20211020151726780](E:\typroa_pic\image-20211020151726780.png)

11.Go 支持可变参数

![image-20211020151928705](E:\typroa_pic\image-20211020151928705.png)

![image-20211020152744386](E:\typroa_pic\image-20211020152744386.png)

**ps**: 如果一个函数的形参列表中有可变参数，则可变参数需要放在形参列表最后。

### 2.3init函数

#### 1.基本介绍

每一个源文件都可以包含一个 **init** 函数，该函数会在 main 函数执行前，被 Go 运行框架调用，也就是说 init 会在 main 函数前被调用。

#### 2.init函数的注意事项和细节

1. 如果一个文件同时包含全局变量定义，**init** 函数和 **main** 函数，则执行的流程： 全局变量定义**->init**函数**->main** 函数

2. init 函数最主要的作用，就是完成一些初始化的工作

3. 如果 main.go 和 utils.go 都含有 变量定义，init 函数时，执行的流程又是怎么样的呢？

![image-20211020154114089](E:\typroa_pic\image-20211020154114089.png)

### 2.4 匿名函数

​	Go 支持匿名函数，匿名函数就是没有名字的函数，如果我们某个函数只是希望使用一次，可以考虑使用匿名函数，匿名函数也可以实现多次调用。匿名函给变量赋值在最后的括号中！

#### 1.使用方式

1.在定义匿名函数时就直接调用，这种方式匿名函数只能调用一次。

![image-20211020155220980](E:\typroa_pic\image-20211020155220980.png)

2.将匿名函数赋给一个变量(函数变量)，再通过该变量来调用匿名函数，可重复使用

![image-20211020155241338](E:\typroa_pic\image-20211020155241338.png)

#### 2.全局匿名函数

如果将匿名函数赋给一个全局变量 ，那么这个匿名函数，就成为一个全局匿名函数，可以在程序有效。

![image-20211020155302200](E:\typroa_pic\image-20211020155302200.png)

![image-20211020155305272](E:\typroa_pic\image-20211020155305272.png)

### 2.5 闭包

#### 1.基本介绍

基本介绍：闭包就是一个函数和与其相关的引用环境组合的一个整体(实体)

```go
package main

import (
	"fmt"
)

func addUper() func(int) int {
	var n int = 10//只初始化一次
    fmt.Println("闭包外n=：", n)//只执行一次
	return func(x int) int {
		n = n + x
		return n
	}
}

func main() {
	f := addUper()
	fmt.Println(f(1))//11
	fmt.Println(f(2))//13
	fmt.Println(f(3))//16
}
```

#### 2.代码说明

1) AUpper 是一个函数，返回的数据类型是 fun (int) int

2) 闭包的说明

```go
var n int = 10
	return func(x int) int {
		n = n + x
		return n
	}
```

返回的是一个匿名函数, 但是这个匿名函数引用到函数外的 n ,因此这个匿名函数就和 n 形成一个整体，构成闭包。

​	可以这样理解: 闭包是类, 函数是操作，n 是字段。函数和它使用到 n 构成闭包。

**PS：闭包的关键就是，函数和它引用到的函数外部的变量共同构成闭包**

#### 3.闭包练习

请编写一个程序，具体要求如下	可以接收一个文件后缀名(比如.jpg)，并返回一个闭包
1) 编写一个函数 makeSuffix(suffix string)	
2) 调用闭包，可以传入一个文件名，如果该文件名没有指定的后缀(比如.jpg) ,则返回 文件名.jpg , 如果已经有.jpg 后缀，则返回原文件名。
3) 要求使用闭包的方式完成
4) strings.HasSuffix , 该函数可以判断某个字符串是否有指定的后缀。

```go
package main

import (
	"fmt"
	"strings"
)

func makeSuffix(suffix string) func(string) string {

	return func(fileName string) string {
		if !strings.HasSuffix(fileName, suffix) {
			return fileName + suffix
		}
		return fileName
	}
}

func main() {
	f := makeSuffix(".jpg")
	fmt.Println("处理后的名称", f("winter"))
	fmt.Println("处理后的名称", f("go.jpg"))
}

```

1) 返回的匿名函数和 makeSuffix (suffix string) 的 suffix 变量 组合成一个闭包,因为 返回的函数引用到 suffix 这个变量

2) 闭包的好处，如果使用传统的方法，也可以轻松实现这个功能，但是传统方法需要每次都传入 后缀名，比如 .jpg ,而闭包因为可以保留上次引用的某个值，所以我们传入一次就可以反复使用。

### 2.6 函数的defer

#### 1. 为什么需要defer

在函数中，程序员经常需要创建资源(比如：数据库连接、文件句柄、锁等) ，为了在函数执行完毕后，及时的释放资源，Go 的设计者提供 defer (延时机制)。

#### 2.示例

```go
package main

import (
	"fmt"
)
func sum(n1 int, n2 int) int {
	defer fmt.Println("ok1 n1=", n1)//3. ok1 n1=10
	defer fmt.Println("ok2 n2=", n2)//2. ok2 n2=20
	n1++
    n2++
	res := n1 + n2
	fmt.Println("ok3 res=", res)//1.  ok3 res=32
	return res
}

func main() {

    defer fmt.Println("hello")
	res := sum(10, 20)//4.  res=32
	fmt.Println("res=", res)
}
//执行顺序如上所标注，先执行n2 是因为栈是先进后出，n1语句先入栈，所以后出栈执行
```

当执行到defer时，暂时先不执行，会将defer后面的语句压入到独立的栈，当函数执行完毕后，再从defer栈，按照先入后出的方式出栈，执行，main函数中也是如此

PS:在 defer 将语句放入到栈时，也会将相关的值拷贝，同时入栈，之后值改变之后，栈中值不变

### 2.7 函数参数传递方式

#### 1.基本介绍

​	值类型参数默认就是值传递，而引用类型参数默认就是引用传递。

不管是值传递还是引用传递，传递给函数的都是变量的副本，不同的是，值传递的是值的拷贝，引用传递的是地址的拷贝，一般来说，地址拷贝效率高，因为数据量小，而值拷贝决定拷贝的数据大小，数据越大，效率越低。

#### 2.值类型和引用类型

1. **值类型：基本数据类型 int 系列, float 系列, bool, string 、数组和结构体 struct**

2. **引用类型：指针、slice 切片、map、管道 chan、interface 等都是引用类型**

### 2.8 字符串常用系统函数

1. 统计字符串的长度，按字节 len(str)

![image-20211020175434476](E:\typroa_pic\image-20211020175434476.png)

2. 字符串遍历，同时处理有中文的问题 

```go
r := []rune(str)
```

![image-20211020175427335](E:\typroa_pic\image-20211020175427335.png)

3. 字符串转整数:	

```go
n, err := strconv.Atoi("12")
```

![image-20211020175854385](E:\typroa_pic\image-20211020175854385.png)

4. 整数转字符串	

```go
str = strconv.Itoa(12345)
```

![image-20211020175903417](E:\typroa_pic\image-20211020175903417.png)

5. 字符串 转 []byte:	

```go
var bytes = []byte("hello go")
```

![image-20211020175923570](E:\typroa_pic\image-20211020175923570.png)

6. []byte 转 字符串: 

```go
str = string([]byte{97, 98, 99})
```

![image-20211020180152617](E:\typroa_pic\image-20211020180152617.png)

7. 10 进制转 2, 8, 16 进制:	

```go
str = strconv.FormatInt(123, 2) // 2-> 8 , 16
```

![img](E:\typroa_pic\clip_image002-1634732632977.jpg)

8. 查找子串是否在指定的字符串中:

```go
 strings.Contains("seafood", "foo") //true
```

![image-20211020202442695](E:\typroa_pic\image-20211020202442695.png)

9. 统计一个字符串有几个指定的子串 ：

```go
 strings.Count("ceheese", "e") //4
```

![image-20211020202502938](E:\typroa_pic\image-20211020202502938.png)

10. 不区分大小写的字符串比较(==是区分字母大小写的): 

```go
fmt.Println(strings.EqualFold("abc","Abc")) // true

```

![image-20211020202516171](E:\typroa_pic\image-20211020202516171.png)

11. 返回子串在字符串第一次出现的 index 值，如果没有返回-1 : 

```go
strings.Index("NLT_abc", "abc") // 4
```

![image-20211020202606843](E:\typroa_pic\image-20211020202606843.png)

12. 返回子串在字符串最后一次出现的 index，如没有返回-1 : 

```go
strings.LastIndex("go golang", "go")
```

![image-20211020202615795](E:\typroa_pic\image-20211020202615795.png)

13. 将指定的子串替换成 另外一个子串:

```go
 strings.Replace("go go hello", "go", "go 语言", n) n 可以指定你希望替换几个，如果 n=-1 表示全部替换
```

![image-20211020202627265](E:\typroa_pic\image-20211020202627265.png)

14. 按 照 指 定 的 某 个 字 符 ， 为 分 割 标 识 ， 将 一 个 字 符 串 拆 分 成 字 符 串 数 组 ：

```go
	strings.Split("hello,wrold,ok", ",")
```

![image-20211020202657242](E:\typroa_pic\image-20211020202657242.png)

15. 将字符串的字母进行大小写的转换: 

```go
strings.ToLower("Go") // go strings.ToUpper("Go") // GO
```

![image-20211020202708521](E:\typroa_pic\image-20211020202708521.png)

16. 将字符串左右两边的空格去掉：

```go
 strings.TrimSpace(" tn a lone gopher ntrn	")
```

![image-20211020203133020](E:\typroa_pic\image-20211020203133020.png)

17. 将字符串左右两边指定的字符去掉 ：

```go
 strings.Trim("! hello! ", " !")	// ["hello"] //将左右两边 !和 " "去掉
```

![image-20211020203152580](E:\typroa_pic\image-20211020203152580.png)

18. 将字符串左边指定的字符去掉 ：

```go
 strings.TrimLeft("! hello! ", " !")// ["hello"] //将左边 ! 和 ""去掉
```

19. 将字符串右边指定的字符去掉 ：

```go
strings.TrimRight("! hello! ", " !")	// ["hello"] //将右边 ! 和 ""去掉
```

20. 判断字符串是否以指定的字符串开头: 

```go
strings.HasPrefix("ftp://192.168.10.1", "ftp") // true
```

![image-20211020203524619](E:\typroa_pic\image-20211020203524619.png)

21. 判断字符串是否以指定的字符串结束: 

```go
strings.HasSuffix("NLT_abc.jpg", "abc") //false
```

### 2.9 时间日期函数

#### 1.基本介绍

1) 时间和日期相关函数，需要导入 time 包

2) time.Time 类型，用于表示时间

3) 如何获取到其它的日期信息

![image-20211020205627942](E:\typroa_pic\image-20211020205627942.png)

4) 格式化日期时间

方式 1: 就是使用 Printf 或者 SPrintf

![image-20211020205654643](E:\typroa_pic\image-20211020205654643.png)

方式二: 使用 time.Format() 方法完成:

![image-20211020205711363](E:\typroa_pic\image-20211020205711363.png)

对上面代码的说明:
"2006/01/02 15:04:05"这个字符串的各个数字是固定的，必须是这样写。
"2006/01/02 15:04:05"	这个字符串各个数字可以自由的组合，这样可以按程序需求来返回时间和日期

5) 时间的常量

常量的作用:在程序中可用于获取指定时间单位的时间，比如想得到 **100** 毫秒 ：  100 * time. Millisecond

```go
const (
Nanosecond    Duration = 1 //纳秒
Microsecond   = 1000 * Nanosecond	//微秒
Millisecond   = 1000 * Microsecond //毫秒
Second        = 1000 * Millisecond //秒
Minute        = 60 * Second //分钟
Hour	      = 60 * Minute //小时
)
```

6) time.Sleep  休眠

7) time 的 Unix 和 UnixNano 的方法 -----可以获取随机数字

![image-20211020212405183](E:\typroa_pic\image-20211020212405183.png)

![image-20211020212409541](E:\typroa_pic\image-20211020212409541.png)



### 2.10 内置函数

Golang 设计者为了编程方便，提供了一些函数，这些函数可以直接使用，我们称为 Go 的内置函数。官方文档：https://studygolang.com/pkgdoc

1) **len**：用来求长度，比如 string、array、slice、map、channel

2) **new**：用来分配内存，主要用来分配值类型，比如 int、float32,struct...返回的是指针

new一个值类型的内存图：

![image-20211020213638672](E:\typroa_pic\image-20211020213638672.png)

3) **make**：用来分配内存，主要用来分配引用类型，比如 channel、map、slice。

4）**recover()**内置函数，可以捕获到异常

```go
err := recover()
```



## 3. 错误处理

1. Go 语言追求简洁优雅，所以，Go 语言不支持传统的 try…catch…finally 这种处理。

2. Go 中引入的处理方式为：**defer**, **panic**, **recover**

3. 这几个异常的使用场景可以这么简单描述：Go 中可以抛出一个 panic 的异常，然后在 defer 中通过 recover 捕获这个异常，然后正常处理

### 1.使用 defer+recover 来处理错误

```go
func test() {
	//使用defer + recover 来捕获和处理异常
	defer func() {
		err := recover()  // recover()内置函数，可以捕获到异常
		if err != nil {  // 说明捕获到错误
			fmt.Println("err=", err)
			//这里就可以将错误信息发送给管理员....
			fmt.Println("发送邮件给admin@sohu.com~")
		}
	}()
	num1 := 10
	num2 := 0
	res := num1 / num2
	fmt.Println("res=", res)
}
```

### 2.自定义错误

Go 支持自定义错误， 使用 errors.New 和 panic 内置函数。

1. **errors.New("错误说明") , 会返回一个 error 类型的值，表示一个错误.**

2. **panic 内置函数 ,接收一个 interface{}类型的值（就是任何值）作为参数。可以接收 error 类型的变量，输出错误信息，并退出程序.**

```go
//函数去读取以配置文件init.conf的信息
//如果文件名传入不正确，我们就返回一个自定义的错误
func readConf(name string) (err error) {
	if name == "config.ini" {
		//读取...
		return nil
	} else {
		//返回一个自定义错误
		return errors.New("读取文件错误..")
	}
}

func test02() {

	err := readConf("config2.ini")
	if err != nil {
		//如果读取文件发送错误，就输出这个错误，并终止程序
		panic(err)
	}
	fmt.Println("test02()继续执行....")
}

```

# 第六章、数组和切片

## 1.数组

### 1.数组定义

var	数组名 [数组大小]数据类型

```go
	var a [5]int //此时数组的各个元素有默认值0

//四种初始化数组的方式
	var numArr01 [3]int = [3]int{1, 2, 3}
	fmt.Println("numArr01=", numArr01)

	var numArr02 = [3]int{5, 6, 7}
	fmt.Println("numArr02=", numArr02)
	//这里的 [...] 是规定的写法
	var numArr03 = [...]int{8, 9, 10}
	fmt.Println("numArr03=", numArr03)

	var numArr04 = [...]int{1: 800, 0: 900, 2:999}
	fmt.Println("numArr04=", numArr04)
```

### 2.数组的内存布局

![image-20211021082349474](E:\typroa_pic\image-20211021082349474.png)

1) 数组的地址可以通过数组名来获取 &intArr
2) 数组的第一个元素的地址，就是数组的首地址
3) 数组的各个元素的地址间隔是依据数组的类型决定，比如 int64 -> 8	int32->4...

### 3.for--range基本语法

![image-20211021084732165](E:\typroa_pic\image-20211021084732165.png)

### 4.注意事项和细节

1） 数组是多个相同类型数据的组合,一个数组一旦声明/定义了,其长度是固定的, 不能动态变化。

2） var arr []int	这时 arr 就是一个 slice 切片。

3） 数组中的元素可以是任何数据类型，包括值类型和引用类型，但是不能混用。

4） 数组创建后，如果没有赋值，有默认值(零值)
		数值类型数组：默认值为 0
		字符串数组：	默认值为 ""
		bool 数组： 默认值为 false

5） Go 的数组属值类型， 在默认情况下是值传递， 因此会进行值拷贝。数组间不会相互影响

![image-20211021085159067](E:\typroa_pic\image-20211021085159067.png)

6）如想在其它函数中，去修改原来的数组，可以使用引用传递(指针方式)

![image-20211021085150207](E:\typroa_pic\image-20211021085150207.png)

7）长度是数组类型的一部分，在传递函数参数时 需要考虑数组的长度

![image-20211021085315259](E:\typroa_pic\image-20211021085315259.png)

## 2.切片

### 1.基本介绍

1) 切片的英文是 slice

2) 切片是数组的一个引用，因此切片是引用类型，在进行传递时，遵守引用传递的机制。

3) 切片的使用和数组类似，遍历切片、访问切片的元素和求切片长度 len(slice)都一样。

4) 切片的长度是可以变化的，因此切片是一个可以动态变化数组。

5) 切片定义的基本语法:
var 切片名 []类型
比如：**var a [ ] int**

```go
package main
import (
	"fmt"
)

func main() {

	//切片的基本使用
	var intArr [5]int = [...]int{1, 22, 33, 66, 99}
	//声明/定义一个切片
	//slice := intArr[1:3]
	//1. slice 就是切片名
	//2. intArr[1:3] 表示 slice 引用到intArr这个数组 
	//3. 引用intArr数组的起始下标为 1 , 最后的下标为3(但是不包含3)    
	slice := intArr[1:3] 
	fmt.Println("intArr=", intArr)
	fmt.Println("slice 的元素是 =", slice) //  22, 33
	fmt.Println("slice 的元素个数 =", len(slice)) // 2
	fmt.Println("slice 的容量 =", cap(slice)) // 切片的容量是可以动态变化  

	fmt.Printf("intArr[1]的地址=%p\n", &intArr[1])
	fmt.Printf("slice[0]的地址=%p slice[0==%v\n", &slice[0], slice[0])
	slice[1] = 34
	fmt.Println()
	fmt.Println()
	fmt.Println("intArr=", intArr)
	fmt.Println("slice 的元素是 =", slice) //  22, 33

}
```

### 2.切片的内存布局

![image-20211021095626875](E:\typroa_pic\image-20211021095626875.png)

```go
1. slice 是一个引用类型
2. slice 从底层来说，其实就是一个数据结构(struct 结构体)
   type slice struct {
   ptr*[2]int
   len  int
   cap	int
   }
```

### 3.切片的使用

方式 1:
第一种方式：定义一个切片，然后让切片去引用一个已经创建好的数组，比如前面的案例就是这样的。

```go
	var intArr [5]int = [...]int{1, 22, 33, 66, 99}
	//声明/定义一个切片
	//slice := intArr[1:3]
	//1. slice 就是切片名
	//2. intArr[1:3] 表示 slice 引用到intArr这个数组 
	//3. 引用intArr数组的起始下标为 1 , 最后的下标为3(但是不包含3)    
	slice := intArr[1:3] 
```

方式 2
第二种方式：通过 make 来创建切片.

```go
基本语法：var 切片名 []type = make([]type, len, [cap])

参数说明: type: 就是数据类型	len : 大小	cap ：指定切片容量，可选， 如果你分配了 cap,则要求 cap>=len.
```

```go
//切片的使用 make
	var slice []float64 = make([]float64, 5, 10)
	slice[1] = 10
	slice[3] = 20
	//对于切片，必须make后使用.
	fmt.Println(slice)
	fmt.Println("slice的size=", len(slice))
	fmt.Println("slice的cap=", cap(slice))

```

小结：

1. 通过 make 方式创建切片可以指定切片的大小和容量

2. 如果没有给切片的各个元素赋值，那么就会使用默认值[int , float=> 0	string =>””	bool =>false]

3. 通过 make 方式创建的切片对应的数组是由 make 底层维护，对外不可见，即只能通过 slice 去访问各个元素.

方式 3
第 3 种方式：定义一个切片，直接就指定具体数组，使用原理类似 make 的方式

```go
//方式3
	//第3种方式：定义一个切片，直接就指定具体数组，使用原理类似make的方式
	var strSlice []string = []string{"tom", "jack", "mary"}
	fmt.Println("strSlice=", strSlice)
	fmt.Println("strSlice size=", len(strSlice)) //3
	fmt.Println("strSlice cap=", cap(strSlice)) // 3
```

方式1和方式2 的区别：

![image-20211021101227673](E:\typroa_pic\image-20211021101227673.png)

### 4.注意事项和细节

1) 切片初始化时 var slice = arr[startIndex:endIndex]
说明：从 arr 数组下标为 startIndex，取到 下标为 endIndex 的元素(不含 arr[endIndex])。

2) 切片初始化时，仍然不能越界。范围在 [0-len(arr)] 之间，但是可以动态增长.

```go
var slice = arr[0:end]:
		可以简写 var slice = arr[:end]
var slice = arr[start:len(arr)]:
		可以简写： var slice = arr[start:]
var slice = arr[0:len(arr)]:
		可以简写: var slice = arr[:]
```

3) cap 是一个内置函数，用于统计切片的容量，即最大可以存放多少个元素。

4) **切片定义完后，还不能使用，因为本身是一个空的，需要让其引用到一个数组，或者 make 一个空间供切片来使用**

5) 切片可以继续切片

6) **用 append 内置函数，可以对切片进行动态追加go，内建函数append将元素追加到切片的末尾。若它有足够的容量，其目标就会重新切片以容纳新的元素。否则，就会分配一个新的基本数组。append返回更新后的切片，因此必须用变量存储追加后的结果。**

7) 切片的拷贝操作

​			切片使用 copy 内置函数完成拷贝

8) 切片是引用类型，所以在传递时，遵守引用传递机制

```go
package main
import (
	"fmt"
)

func main() {

	//使用常规的for循环遍历切片
	var arr [5]int = [...]int{10, 20, 30, 40, 50}
	//slice := arr[1:4] // 20, 30, 40
	slice := arr[1:4]
	for i := 0; i < len(slice); i++ {
		fmt.Printf("slice[%v]=%v ", i, slice[i])
	}

	fmt.Println()
	//使用for--range 方式遍历切片
	for i, v := range slice {
		fmt.Printf("i=%v v=%v \n", i, v)
	}

	slice2 := slice[1:2] //  slice [ 20, 30, 40]    [30]
	slice2[0] = 100  // 因为arr , slice 和slice2 指向的数据空间是同一个，因此slice2[0]=100，其它的都变化

	fmt.Println("slice2=", slice2)
	fmt.Println("slice=", slice)
	fmt.Println("arr=", arr)

	fmt.Println()

	//用append内置函数，可以对切片进行动态追加
	var slice3 []int = []int{100, 200, 300}
	//通过append直接给slice3追加具体的元素
	slice3 = append(slice3, 400, 500, 600)
	fmt.Println("slice3", slice3) //100, 200, 300,400, 500, 600

	//通过append将切片slice3追加给slice3
	slice3 = append(slice3, slice3...) // 100, 200, 300,400, 500, 600 100, 200, 300,400, 500, 600
	fmt.Println("slice3", slice3)


	//切片的拷贝操作
	//切片使用copy内置函数完成拷贝，举例说明
	fmt.Println()
	var slice4 []int = []int{1, 2, 3, 4, 5}
	var slice5 = make([]int, 10)
	copy(slice5, slice4)
	fmt.Println("slice4=", slice4)// 1, 2, 3, 4, 5
	fmt.Println("slice5=", slice5) // 1, 2, 3, 4, 5, 0 , 0 ,0,0,0
}
```

### 5.切片和string

1. string 底层是一个 byte 数组，因此 string 也可以进行切片处理

2. string 和切片在内存的形式，

![image-20211021105530478](E:\typroa_pic\image-20211021105530478.png)

3. **string 是不可变的**，也就说不能通过 str[0] = 'z' 方式来修改字符串

4. 如果需要修改字符串，可以先将 string -> []byte / 或者 []rune -> 修改 -> 重写转成 string

```go
package main
import (
	"fmt"
)

func main() {

	//string底层是一个byte数组，因此string也可以进行切片处理
	str := "hello@atguigu"
	//使用切片获取到 atguigu
	slice := str[6:] 
	fmt.Println("slice=", slice)

	//string是不可变的，也就说不能通过 str[0] = 'z' 方式来修改字符串 
	//str[0] = 'z' [编译不会通过，报错，原因是string是不可变]

	//如果需要修改字符串，可以先将string -> []byte / 或者 []rune -> 修改 -> 重写转成string
	// "hello@atguigu" =>改成 "zello@atguigu"
	// arr1 := []byte(str) 
	// arr1[0] = 'z'
	// str = string(arr1)
	// fmt.Println("str=", str)

	// 细节，我们转成[]byte后，可以处理英文和数字，但是不能处理中文
	// 原因是 []byte 字节来处理 ，而一个汉字，是3个字节，因此就会出现乱码
	// 解决方法是 将  string 转成 []rune 即可， 因为 []rune是按字符处理，兼容汉字

	arr1 := []rune(str) 
	arr1[0] = '北'
	str = string(arr1)
	fmt.Println("str=", str)
}
```

## 3.二维数组

### 1.使用方式

​	语法: var 数组名 [大小][大小]类型
​	比如: var arr [2][3]int ， 再赋值。

### 2.二维数组内存布局

![image-20211021143757370](E:\typroa_pic\image-20211021143757370.png)



```go
package main
import (
	"fmt"
)
func main() {

	var arr2 [2][3]int 
	arr2[1][1] = 10
	fmt.Println(arr2)

	fmt.Printf("arr2[0]的地址%p\n", &arr2[0])
	fmt.Printf("arr2[1]的地址%p\n", &arr2[1])

	fmt.Printf("arr2[0][0]的地址%p\n", &arr2[0][0])
	fmt.Printf("arr2[1][0]的地址%p\n", &arr2[1][0])

	fmt.Println()
	arr3  := [2][3]int{{1,2,3}, {4,5,6}}
	fmt.Println("arr3=", arr3)

}
```

### 3.二维数组遍历

```go
package main
import (
	"fmt"
)

func main() {

	//演示二维数组的遍历
	var arr3  = [2][3]int{{1,2,3}, {4,5,6}}

	//for循环来遍历
	for i := 0; i < len(arr3); i++ {
		for j := 0; j < len(arr3[i]); j++ {
			fmt.Printf("%v\t", arr3[i][j])
		}
		fmt.Println()
	}

	//for-range来遍历二维数组
	for i, v := range arr3 {
		for j, v2 := range v {
			fmt.Printf("arr3[%v][%v]=%v \t",i, j, v2)
		}
		fmt.Println()	
	}
}
```

### 4.二维数组初始化

var 数组名 [大小] [大小]类型 = [大小] [大小]类型{{初值..},{初值..}}

var 数组名 [大小] [大小]类型 = [...] [大小]类型{{初值..},{初值..}}

var 数组名	= [大小] [大小]类型{{初值..},{初值..}}	

var 数组名	= [...] [大小]类型{{初值..},{初值..}}

# 第七章、map

## 1. 基本介绍

map 是 key-value 数据结构，又称为字段或者关联数组。

内置map类型不支持并发安全，Go中有sync.Map类型，支持并发，另有一套方法

## 2. 基本语法

```go
var map 变量名 map[keytype]valuetype
```

```go
1. key 可以是什么类型
   golang 中的 map，的 key 可以是很多种类型，比如 bool, 数字，string, 指针, channel , 还可以是只包含前面几个类型的 接口, 结构体, 数组通常 key 为 int 、string

   注意: slice， map 还有 function 不可以，因为这几个没法用 == 来判断

2. valuetype 可以是什么类型
   valuetype 的类型和 key 基本一样
   通常为: 数字(整数,浮点数),string,map,struct
```

## 3. map的声明和使用

```go
var a map[string]string
var a map[string]int
var a map[int]string
var a map[string]map[string]string
注意：声明是不会分配内存的，初始化需要 make ，分配内存后才能赋值和使用

```

1. **map 在使用前一定要 make**

2. map 的 key 是不能重复，如果重复了，则以最后这个 key-value 为准

3. map 的 value 是可以相同的.

4. **map 的 key-value 是无序**

5. make 内置函数数目

```go
package main
import (
	"fmt"
)

func main() {
	//第一种使用方式
	
	var a map[string]string
	//在使用map前，需要先make , make的作用就是给map分配数据空间
	a = make(map[string]string, 10)
	a["no1"] = "宋江" //ok?
	a["no2"] = "吴用" //ok?
	a["no1"] = "武松" //ok?
	a["no3"] = "吴用" //ok?
	fmt.Println(a)

	//第二种方式
	cities := make(map[string]string)
	cities["no1"] = "北京"
	cities["no2"] = "天津"
	cities["no3"] = "上海"
	fmt.Println(cities)

	//第三种方式
	heroes := map[string]string{
		"hero1" : "宋江",
		"hero2" : "卢俊义",
		"hero3" : "吴用",
	}
	heroes["hero4"] = "林冲"
	fmt.Println("heroes=", heroes)
}
```

## 4. map的增删查改和遍历

**1.map 增加和更新：**
map["key"] = value //如果 key 还没有，就是增加，如果 key 存在就是修改

**2.map 删除：	**
		`delete(map，"key")` ，`delete `是一个内置函数，如果 key 存在，就删除该 key-value,如果 key 不存在，不操作，但是也不会报错3.

​		如果我们要删除 map 的所有 key ,没有一个专门的方法一次删除，可以遍历一下 key, 逐个删除或者 map = make(...)，make 一个新的，让原来的成为垃圾，被 gc 回收

**3.map 查找**

```go
val, ok := cities["no2"]
	if ok {
		fmt.Printf("有no1 key 值为%v\n", val)
	} else {
		fmt.Printf("没有no1 key\n")
	}
```

```go
package main
import (
	"fmt"
)

func main() {
	//第二种方式
	cities := make(map[string]string)
	cities["no1"] = "北京"
	cities["no2"] = "天津"
	cities["no3"] = "上海"
	fmt.Println(cities)

	//因为 no3这个key已经存在，因此下面的这句话就是修改
	cities["no3"] = "上海~" 
	fmt.Println(cities)

	//删除
	delete(cities, "no1")
	fmt.Println(cities)
	//当delete指定的key不存在时，删除不会操作，也不会报错
	delete(cities, "no4")
	fmt.Println(cities)


	//map的查找
	val, ok := cities["no2"]
	if ok {
		fmt.Printf("有no1 key 值为%v\n", val)
	} else {
		fmt.Printf("没有no1 key\n")
	}

	//如果希望一次性删除所有的key
	//1. 遍历所有的key,如何逐一删除 [遍历]
	//2. 直接make一个新的空间
	cities = make(map[string]string)
	fmt.Println(cities)

}
```

**4.map遍历**

```go
package main
import (
	"fmt"
)

func main() {
	//使用for-range遍历map
	//第二种方式
	cities := make(map[string]string)
	cities["no1"] = "北京"
	cities["no2"] = "天津"
	cities["no3"] = "上海"
	
	for k, v := range cities {
		fmt.Printf("k=%v v=%v\n", k, v)
	}

	fmt.Println("cities 有", len(cities), " 对 key-value")

	//使用for-range遍历一个结构比较复杂的map
	studentMap := make(map[string]map[string]string)
	
	studentMap["stu01"] =  make(map[string]string, 3)
	studentMap["stu01"]["name"] = "tom"
	studentMap["stu01"]["sex"] = "男"
	studentMap["stu01"]["address"] = "北京长安街~"

	studentMap["stu02"] =  make(map[string]string, 3) //这句话不能少!
	studentMap["stu02"]["name"] = "mary"
	studentMap["stu02"]["sex"] = "女"
	studentMap["stu02"]["address"] = "上海黄浦江~"

	for k1, v1 := range studentMap {
		fmt.Println("k1=", k1)
		for k2, v2 := range v1 {
				fmt.Printf("\t k2=%v v2=%v\n", k2, v2)
		}
		fmt.Println()
	}
}
```

## 5. map切片

​	切片的数据类型如果是 **map**，则我们称为 slice of map，**map** 切片，这样使用则 **map** 个数就可以动态变化了。

```go
package main
import (
	"fmt"
)

func main() {
	//map切片的使用
	/*
	要求：使用一个map来记录monster的信息 name 和 age, 也就是说一个monster对应一个map,并且妖怪的个数可以动态的增加=>map切片
	*/
	//1. 声明一个map切片
	var monsters []map[string]string
	monsters = make([]map[string]string, 2) //准备放入两个妖怪
	//2. 增加第一个妖怪的信息
	if monsters[0] == nil {
		monsters[0] = make(map[string]string, 2)
		monsters[0]["name"] = "牛魔王"
		monsters[0]["age"] = "500"
	}

	if monsters[1] == nil {
		monsters[1] = make(map[string]string, 2)
		monsters[1]["name"] = "玉兔精"
		monsters[1]["age"] = "400"
	}

	// 下面这个写法越界。
	// if monsters[2] == nil {
	// 	monsters[2] = make(map[string]string, 2)
	// 	monsters[2]["name"] = "狐狸精"
	// 	monsters[2]["age"] = "300"
	// }

	//这里我们需要使用到切片的append函数，可以动态的增加monster
	//1. 先定义个monster信息
	newMonster := map[string]string{
		"name" : "新的妖怪~火云邪神",
		"age" : "200",
	}
	monsters = append(monsters, newMonster)

	fmt.Println(monsters)
}
```



## 6. map 排序

1) golang 中没有一个专门的方法针对 map 的 key 进行排序

2) golang 中的 map 默认是无序的，注意也不是按照添加的顺序存放的，你每次遍历，得到的输出可能不一样.

3) golang 中 map 的排序，是先将 key 进行排序，然后根据 key 值遍历输出即可

调用sort包中的函数，详情看官方文档https://studygolang.com/pkgdoc

```go
package main
import (
	"fmt"
	"sort"
)

func main() {

	//map的排序
	map1 := make(map[int]int, 10)
	map1[10] = 100
	map1[1] = 13
	map1[4] = 56
	map1[8] = 90

	fmt.Println(map1)

	//如果按照map的key的顺序进行排序输出
	//1. 先将map的key 放入到 切片中
	//2. 对切片排序 
	//3. 遍历切片，然后按照key来输出map的值

	var keys []int
	for k, _ := range map1 {
		keys = append(keys, k)
	}
	//排序
	sort.Ints(keys)
	fmt.Println(keys)

	for _, k := range keys{
		fmt.Printf("map1[%v]=%v \n", k, map1[k])
	}
	
}
```

## 7. map使用细节

1) map 是引用类型，遵守引用类型传递的机制，在一个函数接收 map，修改后，会直接修改原来的 map

2) map 的容量达到后，再想 map 增加元素，会自动扩容，并不会发生 panic，也就是说 map 能动态的增长 键值对(key-value)

3) map 的 **value** 也经常使用 **struct** 类型，更适合管理复杂的数据(比前面 value 是一个 map 更好)，比如 value 为 Student 结构体 



# 第八章、面向 ”对象“编程

## 1.Golang 语言面向对象编程说明

1. Golang 也支持面向对象编程(OOP)，但是和传统的面向对象编程有区别，并不是纯粹的面向对象语言。所以我们说 Golang 支持面向对象编程特性是比较准确的。

2. Golang 没有类(class)，Go 语言的结构体(struct)和其它编程语言的类(class)有同等的地位，你可以理解 Golang 是基于 struct 来实现 OOP 特性的。

3. Golang 面向对象编程非常简洁，去掉了传统 OOP 语言的继承、方法重载、构造函数和析构函数、隐藏的 this 指针等等

4. Golang 仍然有面向对象编程的继承，封装和多态的特性，只是实现的方式和其它 OOP 语言不一样，比如继承 ：Golang 没有 extends 关键字，继承是通过匿名字段来实现。

5. Golang 面向对象(OOP)很优雅，OOP 本身就是语言类型系统(type system)的一部分，通过接口(interface)关联，耦合性低，也非常灵活。也就是说在 Golang 中面向接口编程是非常重要的特性。

## 2. 结构体struct

### 1. 结构体和结构体变量

1) 结构体是自定义的数据类型，代表一类事物.

2) 结构体变量(实例)是具体的，实际的，代表一个具体变量

### 2. 结构体变量内存布局

![image-20211022090913626](E:\typroa_pic\image-20211022090913626.png)

### 3. 结构体声明

**PS: 结构体是值类型，默认是值拷贝**

```go
type 结构体名称 struct {
	field1 type
	field2 type
}

type Student struct {
Name string //字段
Age int //字段
Score float32

```

在创建一个结构体变量后，如果没有给字段赋值，都对应一个零值(默认值):

**结构体内部字段的顺序也和内存对齐有关，分配连续的内存，而cpu是一个字节一个字节的读，应合理定义字段顺序，使之刚好可以凑齐**

布尔类型是 false ，数值是 0 ，字符串是 " "。数组类型的默认值和它的元素类型相关，比如 score [3]int 则为[0, 0, 0]；

**指针**，**slice**，和 **map** 的零值都是 **nil** ，即还没有分配空间。

### 4. 创建结构体变量

方式 1-直接声明

```go
var person Person
```

方式 2-{}

```go
var person Person = Person{}
```

方式 3-&

```go
var person *Person = new (Person)
```

方式 4-{}

```go
var person *Person = &Person{}
```

1. 第 3 种和第 4 种方式返回的是 结构体指针。
2. 结构体指针访问字段的标准方式应该是：(*结构体指针).字段名 ，比如 (*person).Name = "tom"
3. 但 go 做了一个简化，也支持 结构体指针.字段名,	比如 person.Name = "tom"。go 编译器底层对 person.Name 做了转化 (*person).Name。

```go
package main
import (
	"fmt"
)

type Person struct{
	Name string
	Age int
}
func main() {
	//方式1

	//方式2
	p2 := Person{"mary", 20}
	// p2.Name = "tom"
	// p2.Age = 18
	fmt.Println(p2)

	//方式3-&
	//案例: var person *Person = new (Person)

	var p3 *Person= new(Person)
	//因为p3是一个指针，因此标准的给字段赋值方式
	//(*p3).Name = "smith" 也可以这样写 p3.Name = "smith"

	//原因: go的底层会对 p3.Name = "smith" 进行处理
	//会给 p3 加上 取值运算 (*p3).Name = "smith"
	(*p3).Name = "smith" 
	p3.Name = "john" //

	(*p3).Age = 30
	p3.Age = 100
	fmt.Println(*p3)

	//方式4-{}
	//案例: var person *Person = &Person{}

	//下面的语句，也可以直接给字符赋值
	//var person *Person = &Person{"mary", 60} 
	var person *Person = &Person{}

	//因为person 是一个指针，因此标准的访问字段的方法
	// (*person).Name = "scott"
	// 也可以 person.Name = "scott"
	// 原因和上面一样，底层会对 person.Name = "scott" 进行处理， 会加上 (*person)
	(*person).Name = "scott"
	person.Name = "scott~~"

	(*person).Age = 88
	person.Age = 10
	fmt.Println(*person)

}
```

### 5.创建结构体变量时指定字段值

```go
package main

import (
	"fmt"	
)
type Stu struct {
	Name string
	Age int
}

func main() {

	//方式1
	//在创建结构体变量时，就直接指定字段的值
	var stu1 = Stu{"小明", 19} // stu1---> 结构体数据空间
	stu2 := Stu{"小明~", 20}

	//在创建结构体变量时，把字段名和字段值写在一起, 这种写法，就不依赖字段的定义顺序.
	var stu3 = Stu{
			Name :"jack",
			Age : 20,
		}
	stu4 := Stu{
		Age : 30,
		Name : "mary",
	}
	
	fmt.Println(stu1, stu2, stu3, stu4)

	//方式2， 返回结构体的指针类型(!!!)
	var stu5 *Stu = &Stu{"小王", 29}  // stu5--> 地址 ---》 结构体数据[xxxx,xxx]
	stu6 := &Stu{"小王~", 39}

	//在创建结构体指针变量时，把字段名和字段值写在一起, 这种写法，就不依赖字段的定义顺序.
	var stu7 = &Stu{
		Name : "小李",
		Age :49,
	}
	stu8 := &Stu{
		Age :59,
		Name : "小李~",
	}
	fmt.Println(*stu5, *stu6, *stu7, *stu8) //

}
```



### 6. 结构体注意事项和细节

1. **结构体的所有字段在内存中是连续的**

```go
package main 
import "fmt"

//结构体
type Point struct {
	x int
	y int
}

//结构体
type Rect struct {
	leftUp, rightDown Point
}

//结构体
type Rect2 struct {
	leftUp, rightDown *Point
}

func main() {

	r1 := Rect{Point{1,2}, Point{3,4}} 

	//r1有四个int, 在内存中是连续分布
	//打印地址
	fmt.Printf("r1.leftUp.x 地址=%p r1.leftUp.y 地址=%p r1.rightDown.x 地址=%p r1.rightDown.y 地址=%p \n", 
	&r1.leftUp.x, &r1.leftUp.y, &r1.rightDown.x, &r1.rightDown.y)

    
	//r2有两个 *Point类型，这个两个*Point类型的本身地址也是连续的，
	//但是他们指向的地址不一定是连续

	r2 := Rect2{&Point{10,20}, &Point{30,40}} 

	//打印地址
	fmt.Printf("r2.leftUp 本身地址=%p r2.rightDown 本身地址=%p \n", 
		&r2.leftUp, &r2.rightDown)

	//他们指向的地址不一定是连续...， 这个要看系统在运行时是如何分配
	fmt.Printf("r2.leftUp 指向地址=%p r2.rightDown 指向地址=%p \n", 
		r2.leftUp, r2.rightDown)

}
```

2) 结构体是用户单独定义的类型，和其它类型进行转换时需要有完全相同的字段(名字、个数和类型)

3) 结构体进行 type 重新定义(相当于取别名)，Golang 认为是新的数据类型，但是相互间可以强转

![image-20211022095109286](E:\typroa_pic\image-20211022095109286.png)

4) struct 的每个字段上，可以写上一个 **tag**, 该 tag 可以通过反射机制获取，常见的使用场景就是序列化和反序列化。因为GO中变量名只有首字母大写才能被其他包调用，而序列化后首字母就会被大写，部分程序员不习惯首字母大写，所以在struct结构体后加上tag，序列化后就会是tag的内容

![image-20211022163137020](E:\typroa_pic\image-20211022163137020.png)

## 3. 方法

### 1. 基本介绍

​		Golang 中的方法是作用在**指定的数据类型上的(即：和指定的数据类型绑定)**，因此自定义类型，都可以有方法，而不仅仅是 **struct**。

### 2. 声明和调用

```go
func (recevier type) methodName（参数列表）(返回值列表){
	方法体
return 返回值

}
```

1. 参数列表：表示方法输入

2. recevier type : 表示这个方法和 type 这个类型进行绑定，或者说该方法作用于 type 类型

3. receiver type : type 可以是结构体，也可以其它的自定义类型

4. receiver : 就是 type 类型的一个变量(实例)，比如 ：Person 结构体 的一个变量(实例)

5. 返回值列表：表示返回的值，可以多个

6. 方法主体：表示为了实现某一功能代码块

7. return 语句不是必须的。

```go
type A struct {
	Num int
}
func (a A) test() {
	fmt.Println(a.Num)
}

1. func(a A) test(){}//表示 A 结构体有一方法，方法名为test

2. (a A) 体现 test 方法是和 A 类型绑定的
3. func (p Person) test() {}... p 表示哪个 Person 变量调用，这个 p 就是它的副本, 这点和函数传参非常相似，结构体是值类型，所以默认值拷贝，所以在方法内对属性进行修改不会影响main函数中的值；
```





```go
package main

import (
	"fmt"
)

type Person struct {
	Age  int
	Name string
}

//给Person类型 绑定一个方法
func (a Person) test() {
	a.Name = "小黄"
	a.Age = 18
	fmt.Println(a.Name, a.Age)//小黄  18
}

func main() {
	var p Person
	p.Age = 20
	p.Name = "小王"
	fmt.Println(p.Name, p.Age)//小王  20
	p.test()
}
```

### 3. 方法的调用和传参机制原理

1. 在通过一个变量去调用方法时，其调用机制和函数一样

2. 不一样的地方时，变量调用方法时，该变量本身也会作为一个参数传递到方法(如果变量是值类型，则进行值拷贝，如果变量是引用类型，则进行地址拷贝)

### 4. 方法的注意事项和细节

1) 结构体类型是值类型，在方法调用中，遵守值类型的传递机制，是值拷贝传递方式

2) 如程序员希望在方法中，修改结构体变量的值，可以通过结构体指针的方式来处理

3) Golang 中的方法作用在指定的数据类型上的(即：和指定的数据类型绑定)，因此自定义类型，都可以有方法，而不仅仅是 struct， 比如 int , float32 等都可以有方法

4) 方法的访问范围控制的规则，和函数一样。方法名首字母小写，只能在本包访问，方法首字母大写，可以在本包和其它包访问。

**5) 如果一个类型实现了 String()这个方法，那么 fmt.Println 默认会调用这个变量的 String()进行输出**



```go
type Student struct {
	Name string
	Age int
}

//给*Student实现方法String()
func (stu *Student) String() string {
	str := fmt.Sprintf("Name=[%v] Age=[%v]", stu.Name, stu.Age)
	return str
}

//定义一个Student变量
	stu := Student{
		Name : "tom",
		Age : 20,
	}
	//如果你实现了 *Student 类型的 String方法，就会自动调用
	fmt.Println(&stu) 
```



### 5. 方法小练习

```go
package main

import (
	"fmt"
)

type Circle struct {
	radius float64
}

func (circle Circle) area() float64 {

	return 3.14 * circle.radius * circle.radius
}

func main() {

	cir := Circle{}
	fmt.Println("请输入半径：")
	fmt.Scanln(&cir.radius)
	res := cir.area()
	fmt.Println("面积是", res)

}
```

### 6. 方法和函数的区别

1) 调用方式不一样
	函数的调用方式:	函数名(实参列表)
	方法的调用方式:	变量.方法名(实参列表)	

2) 对于普通函数，接收者为值类型时，不能将指针类型的数据直接传递，反之亦然

3) 对于方法（如 struct 的方法），接收者为值类型时，可以直接用指针类型的变量调用方法，反过来同样也可以

```go
package main

import (
	"fmt"	
)

type Person struct {
	Name string
} 

//函数
//对于普通函数，接收者为值类型时，就不能将指针类型的数据直接传递，反之亦然

func test01(p Person) {
	fmt.Println(p.Name)
}

func test02(p *Person) {
	fmt.Println(p.Name)
}

//对于方法（如struct的方法），
//接收者为值类型时，可以直接用指针类型的变量调用方法，反过来同样也可以

func (p Person) test03() {
	p.Name = "jack"
	fmt.Println("test03() =", p.Name) // jack
}

func (p *Person) test04() {
	p.Name = "mary"
	fmt.Println("test03() =", p.Name) // mary
}

func main() {

	p := Person{"tom"}
	test01(p)
	test02(&p)

	p.test03()
	fmt.Println("main() p.name=", p.Name) // tom
	
	(&p).test03() // 从形式上是传入地址，但是本质仍然是值拷贝

	fmt.Println("main() p.name=", p.Name) // tom


	(&p).test04()
	fmt.Println("main() p.name=", p.Name) // mary
	p.test04() // 等价 (&p).test04 , 从形式上是传入值类型，但是本质仍然是地址拷贝

}
```

**总结**:
1) 不管调用形式如何，真正决定是值拷贝还是地址拷贝，看这个方法是和哪个类型绑定.
2) 如果是和值类型，比如	(p Person) , 则是值拷贝， 如果和指针类型，比如是 (p *Person) 则是地址拷贝



## 4. 工厂模式

​		Golang 的结构体没有构造函数，通常可以使用工厂模式来解决这个问题。通过在model包中创建函数或者结构体方法，来访问私有属性的变量

main包：

```go
package main

import (
	"fmt"
	"go_code/project03/model"
)

func main() {
	/*var stu = model.Student{
		Name:  "tony",
		Score: 90.5,
	}*/
	
	var stu = model.NewStudent("tony",88.6)//返回的是一个指针

	fmt.Println("name=",stu.Name,"score=",stu.GetScore())
}

```

model包：

```go
package model

type student struct{
	Name string
	score float64
}

func (stu student)GetScore()float64{
	return stu.score
}

func NewStudent(n string, s float64)*student{
	return &student{
		Name : n,
		score : s,
	}

}
```

## 5. 封装

1) 将结构体、字段(属性)的首字母小写(不能导出了，其它包不能使用，类似 private)

2) 给结构体所在包提供一个工厂模式的函数，首字母大写。类似一个构造函数

3) 提供一个首字母大写的 Set 方法(类似其它语言的 public)，用于对属性判断并赋值

```go
func (var	结构体类型名) SetXxx(参数列表) (返回值列表) {
//加入数据验证的业务逻辑
var.字段 = 参数
}
```

4) 提供一个首字母大写的 Get 方法(类似其它语言的 public)，用于获取属性的值

```go
func (var 结构体类型名) GetXxx() {
return var.age;
}
```

## 6. 继承

​	在 Golang 中，如果一个 struct 嵌套了另一个匿名结构体，那么这个结构体可以直接访问匿名结构体的字段和方法，从而实现了继承特性。

### 1. 基本语法

```go
type Goods struct {
Name string
Price int
}
type Book struct {
Goods	//这里就是嵌套匿名结构体 Goods
Writer string
}

```

### 2. 细节理解

1. **结构体可以使用嵌套匿名结构体继承所有的字段和方法，即：首字母大写或者小写的字段、方法，都可以使用。**

2. 匿名结构体字段访问可以简化

​			1. 当我们直接通过 **b** 访问字段或方法时，其执行流程如下比如 b.Name

​			2. 编译器会先看 b 对应的类型有没有 Name, 如果有，则直接调用 B 类型的 Name 字段

​			3. 如果没有就去看 B 中嵌入的匿名结构体 A 有没有声明 Name 字段，如果有就调用,如果没有继续查找.如果都找不到就报错.

```go
package main

import (
	"fmt"
)

type A struct {
	Name string
	age int
}

func (a *A) SayOk() {
	fmt.Println("A SayOk", a.Name)
}

func (a *A) hello() {
	fmt.Println("A hello", a.Name)
}

type B struct {
	A
	Name string 
}

func (b *B) SayOk() {
	fmt.Println("B SayOk", b.Name)
}

func main() {

	 var b B
	 b.A.Name = "tom"
	 b.A.age = 19
	 b.A.SayOk()
	 b.A.hello()

	//上面的写法可以简化

	 b.Name = "smith"
	 b.age = 20
	 b.SayOk()
	 b.hello()
}
```

3. 当结构体和匿名结构体有相同的字段或者方法时，编译器采用就近访问原则访问，如希望访问匿名结构体的字段和方法，可以通过匿名结构体名来区分

4. 结构体嵌入两个(或多个)匿名结构体，如两个匿名结构体有相同的字段和方法(同时结构体本身没有同名的字段和方法)，在访问时，就必须明确指定匿名结构体名字，否则编译报错。

5. 如果一个 struct 嵌套了一个有名结构体，这种模式就是组合，如果是组合关系，那么在访问组合的结构体的字段或方法时，必须带上结构体的名字

6. 嵌套匿名结构体后，也可以在创建结构体变量(实例)时，直接指定各个匿名结构体字段的值

```go
package main
import (
	"fmt"
)

type A struct {
	Name string
	age int
}
type B struct {
	Name string
	Score float64
}
type C struct {
	A
	B
	//Name string
}

type D struct {
	a A //有名结构体
}


type Goods struct {
	Name string
	Price float64
}

type Brand struct {
	Name string
	Address string
}

type TV struct {
	Goods
	Brand	
}

type TV2 struct {
	*Goods
	*Brand	
}

type Monster struct  {
	Name string
	Age int
}

type E struct {
	Monster
	int
	n int
}

func main() {
	var c C
	//如果c 没有Name字段，而A 和 B有Name, 这时就必须通过指定匿名结构体名字来区分
	//所以 c.Name 就会包编译错误， 这个规则对方法也是一样的！
	c.A.Name = "tom" // error
	fmt.Println("c")

	//如果D 中是一个有名结构体，则访问有名结构体的字段时，就必须带上有名结构体的名字
	//比如 d.a.Name 
	var d D 
	d.a.Name = "jack"

	//嵌套匿名结构体后，也可以在创建结构体变量(实例)时，直接指定各个匿名结构体字段的值
	tv := TV{ Goods{"电视机001", 5000.99},  Brand{"海尔", "山东"}, }

	//演示访问Goods的Name
	fmt.Println(tv.Goods.Name)
	fmt.Println(tv.Price) 

	tv2 := TV{ 
		Goods{
			Price : 5000.99,
			Name : "电视机002", 
		},  
		Brand{
			Name : "夏普", 
			Address :"北京",
		}, 
	}

	fmt.Println("tv", tv)
	fmt.Println("tv2", tv2)

	tv3 := TV2{ &Goods{"电视机003", 7000.99},  &Brand{"创维", "河南"}, }

	tv4 := TV2{ 
			&Goods{
				Name : "电视机004", 
				Price : 9000.99,
			},  
			&Brand{
				Name : "长虹", 
				Address : "四川",
			}, 
		}

	fmt.Println("tv3", *tv3.Goods, *tv3.Brand)
	fmt.Println("tv4", *tv4.Goods, *tv4.Brand)


	//匿名字段是基本数据类型的使用
	var e E
	e.Name = "狐狸精"
	e.Age = 300
	e.int = 20
	e.n = 40
	fmt.Println("e=", e)

}
```

### 3. 多重继承

​		一个 **struct** 嵌套了多个匿名结构体，那么该结构体可以直接访问嵌套的匿名结构体的字段和方法，从而实现了多重继承。

```go
type Goods struct {
	Name string
	Price float64
}

type Brand struct {
	Name string
	Address string
}

type TV struct {
	Goods
	Brand	
}
```

## 7. 接口

 Golang 中，多态特性主要是通过接口来体现的。

![image-20211025165422130](E:\typroa_pic\image-20211025165422130.png)

### 1. 基本概念

​		interface 类型可以定义一组方法，但是这些不需要实现。并且 interface 不能包含任何变量。到某个自定义类型(比如结构体 Phone)要使用的时候,在根据具体情况把这些方法写出来(实现)。

```go
package main
import (
	"fmt"
)

//声明/定义一个接口
type Usb interface {
	//声明了两个没有实现的方法
	Start() 
	Stop()
}

//声明/定义一个接口
type Usb2 interface {
	//声明了两个没有实现的方法
	Start() 
	Stop()
	Test()
}

type Phone struct {

}  

//让Phone 实现 Usb接口的方法
func (p Phone) Start() {
	fmt.Println("手机开始工作。。。")
}
func (p Phone) Stop() {
	fmt.Println("手机停止工作。。。")
}

type Camera struct {

}
//让Camera 实现   Usb接口的方法
func (c Camera) Start() {
	fmt.Println("相机开始工作~~~。。。")
}
func (c Camera) Stop() {
	fmt.Println("相机停止工作。。。")
}


//计算机
type Computer struct {

}

//编写一个方法Working 方法，接收一个Usb接口类型变量
//只要是实现了 Usb接口 （所谓实现Usb接口，就是指实现了 Usb接口声明所有方法）
func (c Computer) Working(usb Usb) {

	//通过usb接口变量来调用Start和Stop方法
	usb.Start()
	usb.Stop()
}

func main() {

	//测试
	//先创建结构体变量
	computer := Computer{}
	phone := Phone{}
	camera := Camera{}

	//关键点
	computer.Working(phone)
	computer.Working(camera) //
}
```

1) 接口里的所有方法都没有方法体，即接口的方法都是没有实现的方法。接口体现了程序设计的多态和高内聚低偶合的思想。

2) Golang 中的接口，不需要显式的实现。只要一个变量，含有接口类型中的所有方法，那么这个变量就实现这个接口。

### 2. 接口使用理解

1.首先在接口中写出需要实现的方法

2.其次让结构体类型或其他数据类型完成接口中的方法

3.定义一个含接口变量的方法来调用接口中的方法

4.用不同的数据类型做3中方法的变量，调用对应数据类型实现的接口方法。



### 3. 注意事项和细节

**1) 接口本身不能创建实例,但是可以指向一个实现了该接口的自定义类型的变量(实例)**

**2) 接口中所有的方法都没有方法体,即都是没有实现的方法。**

**3) 在 Golang 中，一个自定义类型需要将某个接口的所有方法都实现，我们说这个自定义类型实现了该接口。**

**4) 一个自定义类型只有实现了某个接口，才能将该自定义类型的实例(变量)赋给接口类型**

**5) 只要是自定义数据类型，就可以实现接口，不仅仅是结构体类型。**

**6)	一个自定义类型可以实现多个接口**

**7) Golang 接口中不能有任何变量**

**8) 一个接口(比如 A 接口)可以继承多个别的接口(比如 B,C 接口)，这时如果要实现 A 接口，也必须将 B,C 接口的方法也全部实现。**

**9) interface 类型默认是一个指针(引用类型)，如果没有对 interface 初始化就使用，那么会输出 nil**

**10) 空接口 interface{} 没有任何方法，所以所有类型都实现了空接口, 即我们可以把任何一个变量赋给空接口**。

```go
package main
import (
	"fmt"
)

type Stu struct {
	Name string
}

func (stu Stu) Say() {
	fmt.Println("Stu Say()")
}


type integer int

func (i integer) Say() {
	fmt.Println("integer Say i =" ,i )
}


type AInterface interface {
	Say()
}

type BInterface interface {
	Hello()
}
type Monster struct {

}

//一个自定义类型实现多个接口
func (m Monster) Hello() {
	fmt.Println("Monster Hello()~~")
}
func (m Monster) Say() {
	fmt.Println("Monster Say()~~")
}


func main() {
	var stu Stu //结构体变量，实现了 Say() 实现了 AInterface
 	var a AInterface = stu
	a.Say()


	var i integer = 10
	var b AInterface = i
	b.Say() // integer Say i = 10


	//Monster实现了AInterface 和 BInterface
	var monster Monster
	var a2 AInterface = monster
	var b2 BInterface = monster
	a2.Say()
	b2.Hello()
}
```

```go
package main
import (
	"fmt"
)

type BInterface interface {
	test01()
}

type CInterface interface {
	test02()
}

//接口继承接口，但所继承接口需要全部实现
type AInterface interface {
	BInterface
	CInterface
	test03()
}

//如果需要实现AInterface,就需要将BInterface CInterface的方法都实现
type Stu struct {
}
func (stu Stu) test01() {

}
func (stu Stu) test02() { 
	
}
func (stu Stu) test03() {
	
}

//空接口，所有类型的变量都可以赋给空接口
type T  interface{

}

func main() {
	var stu Stu
	var a AInterface = stu
	a.test01()

	var t T = stu //ok
	fmt.Println(t)
	var t2 interface{}  = stu//空接口实现的另一种方式
	var num1 float64 = 8.8
	t2 = num1
	t = num1
	fmt.Println(t2, t)
}
```

### 4. 接口编程实践

```go
package main

import(
	"fmt"
	"sort"
	"math/rand"
)

//声明一个结构体
type Hero struct{
	Name string
	Age int
}

//声明一个结构体切片类型
type HeroSlice []Hero

//实现Interface接口
func (hs HeroSlice)Len()int{
	return len(hs)
}

//决定使用升序还是降序进行排序
func (hs HeroSlice)Less(i,j int)bool{
	//  return hs[i].Age< hs[j].Age
	 //修改成对姓名排序
	 return hs[i].Name< hs[j].Name
}


func (hs HeroSlice)Swap(i,j int){
	// temp:=hs[i]
	// hs[i]=hs[j]
	// hs[j]=temp
	hs[i],hs[j]=hs[j],hs[i]//go中的多重赋值
}

func main(){
	var slice =[]int{0,-1,10,7,90}
	sort.Ints(slice)
	fmt.Println(slice)

	//对结构体切片进行排序
	var heros HeroSlice
	for i:=0;i<10;i++{
		hero:=Hero{
			Name:fmt.Sprintf("英雄~%d ",rand.Intn(100)),
			Age: rand.Intn(20),
		}

		//将hero  append到heros切片中
		heros =append(heros,hero)
	}

	//排序前
	for _,v:=range heros{
		fmt.Println(v)
	}

	//调用sort包中的sort接口类型变量方法排序后
	sort.Sort(heros)
	fmt.Println("-------------------")
	for _,v:=range heros{
		fmt.Println(v)
	}
}
```

### 5. 接口和继承比较

```go
package main

import(
	"fmt"
)

//Monkey结构体
type Monkey struct{
	Name string
}

type BirdAble interface{
	Flying()
}

type FishAble interface{
	Swimming()
}
func (this *Monkey)climbing(){
	fmt.Println(this.Name,"生来会爬树")
}

//LittleMonkey
type LittleMonkey struct{
	Monkey
}
func (this *LittleMonkey)Flying(){
	fmt.Println(this.Name,"学会了飞翔")
}

func (this *LittleMonkey)Swimming(){
	fmt.Println(this.Name,"学会了游泳")
}

  


func main(){
	var lm LittleMonkey
	lm.Name="小王"
	lm.climbing()
	lm.Flying()
	lm.Swimming()

}
```

1. 当 A 结构体继承了 B 结构体，那么 A 结构就自动的继承了 B 结构体的字段和方法（不论首字母大小写），并且可以直接使用

2. 当 A 结构体需要扩展功能，同时不希望去破坏继承关系，则可以去实现某个接口即可，因此我们可以认为：实现接口是对继承机制的补充

3. 接口和继承解决的解决的问题不同

   继承的价值主要在于：解决代码的复用性和可维护性。
   接口的价值主要在于：设计，设计好各种规范(方法)，让其它自定义类型去实现这些方法。

4. 接口在一定程度上实现代码解耦

## 8. 多态

### 1. 基本介绍

​		变量(实例)具有多种形态。面向对象的第三大特征，在 Go 中，多态特征是通过接口实现的。可以按照统一的接口来调用不同的实现。这时接口变量就呈现不同的形态。

### 2. 接口体现多态的两种形式

#### 1. 多态参数

定义一个方法，参数为接口类型，则该方法可以接收不同数据类型的参数，前提是该数据类型完成了接口中定义的方法，此时，该参数体现的接口多态。

```go
package main
import (
	"fmt"
)

//声明/定义一个接口
type Usb interface {
	//声明了两个没有实现的方法
	Start() 
	Stop()
}

func (c Computer) Working(usb Usb) {

	//通过usb接口变量来调用Start和Stop方法
	usb.Start()
	usb.Stop()
}

```

#### 2. 多态数组

​		在Usb 数组中，存放 Phone 结构体 和 Camera 结构体变量


```go

//声明/定义一个接口
type Usb interface {
	//声明了两个没有实现的方法
	Start() 
	Stop()
}

type Phone struct {

} 
type Camera struct {

}
func main() {
	//定义一个Usb接口数组,可以存放Phone和Camear的结构体变量
	//这里体现出多态数组
	var usbArr [3]Usb
	usbArr[0]=Phone{}
	usbArr[1]=Phone{}
	usbArr[2]=Camera{}
	fmt.Println(usbArr)

}
```

## 9. 类型断言

### 1. 基本介绍

​	类型断言，由于接口是一般类型，不知道具体类型，如果要转成具体类型，就需要使用类型断言，

```go
package main
import (
	"fmt"
)

type Point struct{
	x int
	t int
}

func main(){
	var a interface{}
	var point Point=Point{1,2}
	a=point //ok

	//如何将a赋值给一个Piont变量
	var b Point
	//  错误写法 b=a
	b=a.(Point) //类型断言
	fmt.Println(b)

}
```

​	**在进行类型断言时，如果类型不匹配，就会报 panic, 因此进行类型断言时，要确保原来的空接口指向的就是断言的类型.**

### 2. 带检测的类型断言

​		在进行断言时，带上检测机制，如果成功就 ok,否则也不要报 panic

```go
func main(){

	var x interface{}
	var b2 float32 =1.1
	x = b2 
	//带检测的类型断言  
	if y,ok :=x.(float64);ok{
		fmt.Println("convert success")
		fmt.Printf("y的类型是%T  值是%v",y,y)
	}else {
		fmt.Println("convert fail")
	}
	fmt.Println("继续执行")
}
```

```go
func (c Computer) Working(usb Usb) {

	//通过usb接口变量来调用Start和Stop方法
	usb.Start()
		//如果是Phone类型则执行它特有的call方法
		if phone,ok:=usb.(Phone);ok{
			phone.Call()
		}
	usb.Stop()
}
```

### 3. 实践

```go
package main
import (
    "fmt"
)


//定义Student类型
type Student struct {

}

//编写一个函数，可以判断输入的参数是什么类型
func TypeJudge(items... interface{}) {
    for index, x := range items {
        //此处type为关键字，固定写法
        switch x.(type) {
            case bool :
            fmt.Printf("第%v个参数是 bool 类型，值是%v\n", index, x)
            case float32 :
            fmt.Printf("第%v个参数是 float32 类型，值是%v\n", index, x)
            case float64 :
            fmt.Printf("第%v个参数是 float64 类型，值是%v\n", index, x)
            case int, int32, int64 :
            fmt.Printf("第%v个参数是 整数 类型，值是%v\n", index, x)
            case string :
            fmt.Printf("第%v个参数是 string 类型，值是%v\n", index, x)
            case Student :
            fmt.Printf("第%v个参数是 Student 类型，值是%v\n", index, x)
            case *Student :
            fmt.Printf("第%v个参数是 *Student 类型，值是%v\n", index, x)
            default :
            fmt.Printf("第%v个参数是  类型 不确定，值是%v\n", index, x)
        }
    }
}



func main() {

    var n1 float32 = 1.1
    var n2 float64 = 2.3
    var n3 int32 = 30
    var name string = "tom"
    address := "北京"
    n4 := 300

    stu1 := Student{}
    stu2 := &Student{}

    TypeJudge(n1, n2, n3, name, address, n4, stu1, stu2)

}
```

# 第九章、文件操作

## 1. 基本介绍

**输入输出流**

![image-20211026161250269](E:\typroa_pic\image-20211026161250269.png)

**os.File 封装所有文件相关操作，File 是一个结构体**

打开文件：os.Open()，只能打开已存在的文件

打开文件并赋予权限：os.OpenFile() 可以设置打开属性，os.O_WRONLY  只写打开

os.O_TRUNC	打开时清空

os.O_CREATE	文件不存在时则创建

os.APPEND		以追加方式打开

## 2. 读文件

两种方式：

1.bufio中的  NewReader()和NerWriiter()带缓冲区的都写，写时需要刷新Flush

2.io/ioutil中的ReadFile()和WriteFile()，一次性读和写，无缓冲区

### 1. 带缓冲区的读文件

```go
package main

import (
	"fmt"
	"os"
	"bufio"
	"io"
)

func main(){
	file,err:= os.Open("./test.txt")//返回指针类型
	if err !=nil{
		fmt.Println("open error",err)
	}

	defer file.Close()//及时关闭文件，避免内存泄漏
	
	//床架你一个 *Reader，是带缓冲的
	//NewReader 默认缓冲区大小为4096字节
	reader:= bufio.NewReader(file)
	for{
		str,err:= reader.ReadString('\n')
		if err==io.EOF{//io.EOF表示文件的末尾
			break;
		}
		//输出读取内容
		fmt.Print(str)
	}
	fmt.Println("文件读取结束")
}
```

### 2. 一次性读文件

```go
package main
import (
	"fmt"
	"io/ioutil" 
)
func main() {

	//使用ioutil.ReadFile一次性将文件读取到位
	file := "d:/test.txt"
	content, err := ioutil.ReadFile(file)
	if err != nil {
		fmt.Printf("read file err=%v", err)
	}
	//把读取到的内容显示到终端
	//fmt.Printf("%v", content) // []byte
	fmt.Printf("%v", string(content)) // []byte
	
	//我们没有显式的Open文件，因此也不需要显式的Close文件
	//因为，文件的Open和Close被封装到 ReadFile 函数内部
}
```

## 3. 写文件

### 1. 基本介绍 os.OpenFile 函数

![image-20211026202418825](E:\typroa_pic\image-20211026202418825.png)

```go
package main
import (
	"fmt"
	"bufio"
	"os" 
)
func main() {
	//创建一个新文件，写入内容 5句 "hello, Gardon"
	//1 .打开文件 d:/abc.txt
	filePath := "d:/abc.txt"
	file, err := os.OpenFile(filePath, os.O_WRONLY | os.O_CREATE, 0666)
	if err != nil {
		fmt.Printf("open file err=%v\n", err)
		return 
	}
	//及时关闭file句柄
	defer file.Close()
	//准备写入5句 "hello, Gardon"
	str := "hello,Gardon\r\n" // \r\n 表示换行
	//写入时，使用带缓存的 *Writer
	writer := bufio.NewWriter(file)
	for i := 0; i < 5; i++ {
		writer.WriteString(str)
	}
	//因为writer是带缓存，因此在调用WriterString方法时，其实
	//内容是先写入到缓存的,所以需要调用Flush方法，将缓冲的数据
	//真正写入到文件中， 否则文件中会没有数据!!!
	writer.Flush()
}
```

### 2. 实践

​		编程一个程序，将一个文件的内容，写入到另外一个文件。注：这两个文件已经存在了.

说明：使用 ioutil.ReadFile / ioutil.WriteFile 完成写文件的任务.

```go
package main
import (
	"fmt"
	"io/ioutil" 
)
func main() {
	//将d:/abc.txt 文件内容导入到  e:/kkk.txt
	//1. 首先将  d:/abc.txt 内容读取到内存
	//2. 将读取到的内容写入 e:/kkk.txt
	file1Path := "d:/abc.txt" 
	file2Path := "e:/kkk.txt" 
	data, err := ioutil.ReadFile(file1Path)
	if err != nil {
		//说明读取文件有错误
		fmt.Printf("read file err=%v\n", err)
		return
	}
	err = ioutil.WriteFile(file2Path, data, 0666)
	if err != nil {
		fmt.Printf("write file error=%v\n", err)
	}
}
```

## 4. 判断文件是否存在

os.Stat()函数

![image-20211026212503682](E:\typroa_pic\image-20211026212503682.png)

## 5. 拷贝文件

将一张图片/电影/mp3 拷贝到另外一个文件	e:/abc.jpg	

io 包
func Copy(dst Writer, src Reader) (written int64, err error)
注意; Copy 函数是 io 包提供的

```go
package main
import (
	"fmt"
	"os"
	"io"
	"bufio" 
)

//自己编写一个函数，接收两个文件路径 srcFileName dstFileName
func CopyFile(dstFileName string, srcFileName string) (written int64, err error) {

	srcFile, err := os.Open(srcFileName)
	if err != nil {
		fmt.Printf("open file err=%v\n", err)
	}
	defer srcFile.Close()
	//通过srcfile ,获取到 Reader
	reader := bufio.NewReader(srcFile)

	//打开dstFileName
	dstFile, err := os.OpenFile(dstFileName, os.O_WRONLY | os.O_CREATE, 0666)
	if err != nil {
		fmt.Printf("open file err=%v\n", err)
		return 
	}

	//通过dstFile, 获取到 Writer
	writer := bufio.NewWriter(dstFile)
	defer dstFile.Close()

	return io.Copy(writer, reader)


}

func main() {

	//将d:/flower.jpg 文件拷贝到 e:/abc.jpg

	//调用CopyFile 完成文件拷贝
	srcFile := "d:/flower.jpg"
	dstFile := "e:/abc.jpg"
	_, err := CopyFile(dstFile, srcFile)
	if err == nil {
		fmt.Printf("拷贝完成\n")
	} else {
		fmt.Printf("拷贝错误 err=%v\n", err)
	}
	
}
```

## 6. 统计字符串个数

```go
package main
import (
	"fmt"
	"os"
	"io"
	"bufio" 
)

//定义一个结构体，用于保存统计结果
type CharCount struct {
	ChCount int // 记录英文个数
	NumCount int // 记录数字的个数
	SpaceCount int // 记录空格的个数
	OtherCount int // 记录其它字符的个数
}

func main() {

	//思路: 打开一个文件, 创一个Reader
	//每读取一行，就去统计该行有多少个 英文、数字、空格和其他字符
	//然后将结果保存到一个结构体
	fileName := "e:/abc.txt"
	file, err := os.Open(fileName)
	if err != nil {
		fmt.Printf("open file err=%v\n", err)
		return
	}
	defer file.Close()
	//定义个CharCount 实例
	var count CharCount
	//创建一个Reader
	reader := bufio.NewReader(file)

	//开始循环的读取fileName的内容
	for {
		str, err := reader.ReadString('\n')
		if err == io.EOF { //读到文件末尾就退出
			break
		}
		//遍历 str ，进行统计
		for _, v := range str {
			
			switch {
				case v >= 'a' && v <= 'z':
						fallthrough //穿透
				case v >= 'A' && v <= 'Z':
						count.ChCount++
				case v == ' ' || v == '\t':
						count.SpaceCount++
				case v >= '0' && v <= '9':
						count.NumCount++
				default :
						count.OtherCount++
			}
		}
	}

	//输出统计的结果看看是否正确
	fmt.Printf("字符的个数为=%v 数字的个数为=%v 空格的个数为=%v 其它字符个数=%v", 
		count.ChCount, count.NumCount, count.SpaceCount, count.OtherCount)


}
```

## 7. 命令行参数

### 1. 基本介绍

**os.Args** 是一个 string 的切片，用来存储所有的命令行参数

```go
var Args []string
```

Args保管了命令行参数，第一个是程序名。

### 2. 示例

```go
package main
import (
	"fmt"
	"os"
)

func main() {
	fmt.Println("命令行的参数有", len(os.Args))
	//遍历os.Args切片，就可以得到所有的命令行输入参数值
	for i, v := range os.Args {
		fmt.Printf("args[%v]=%v\n", i, v)
	}
}
```

### 3. flag包用来解析命令行参数

**func (*FlagSet) [Parse]**

```go
func (f *FlagSet) Parse(arguments []string) error
```

从arguments中解析注册的flag。必须在所有flag都注册好而未访问其值时执行。未注册却使用flag -help时，会返回ErrHelp。

使用flag包可以忽略命令行参数的顺序要求

```go
package main
import (
	"fmt"
	"flag"
)

func main() {

	//定义几个变量，用于接收命令行的参数值
	var user string
	var pwd string
	var host string
	var port int

	//&user 就是接收用户命令行中输入的 -u 后面的参数值
	//"u" ,就是 -u 指定参数
	//"" , 默认值
	//"用户名,默认为空" 说明
	flag.StringVar(&user, "u", "", "用户名,默认为空")
	flag.StringVar(&pwd, "pwd", "", "密码,默认为空")
	flag.StringVar(&host, "h", "localhost", "主机名,默认为localhost")
	flag.IntVar(&port, "port", 3306, "端口号，默认为3306")
	//这里有一个非常重要的操作,转换， 必须调用该方法
	flag.Parse()

	//输出结果
	fmt.Printf("user=%v pwd=%v host=%v port=%v", 
		user, pwd, host, port)

}
```

## 8. json

### 1. 序列化

json 序列化是指，将有 **key-valu**e 结构的数据类型(比如结构体、**map**、切片)序列化成 json 字符串的操作。

json.Marshal()----序列化

对于结构体的序列化，如果希望序列化后的 key 的名字，由我们自己重新制定，那么可以给 struct指定一个 tag 标签

```go
package main
import (
	"fmt"
	"encoding/json"
)

//定义一个结构体
type Monster struct {
	Name string `json:"monster_name"` //反射机制
	Age int `json:"monster_age"`
	Birthday string //....
	Sal float64
	Skill string
}

func testStruct() {
	
	monster := Monster{
		Name :"牛魔王",
		Age : 500 ,
		Birthday : "2011-11-11",
		Sal : 8000.0,
		Skill : "牛魔拳",
	}

	//将monster 序列化
	data, err := json.Marshal(&monster) //..
	if err != nil {
		fmt.Printf("序列号错误 err=%v\n", err)
	}
	//输出序列化后的结果
	fmt.Printf("monster序列化后=%v\n", string(data))

}

//将map进行序列化
func testMap() {
	//定义一个map
	var a map[string]interface{}
	//使用map,需要make
	a = make(map[string]interface{})
	a["name"] = "红孩儿"
	a["age"] = 30
	a["address"] = "洪崖洞"

	//将a这个map进行序列化
	//将monster 序列化
	data, err := json.Marshal(a)
	if err != nil {
		fmt.Printf("序列化错误 err=%v\n", err)
	}
	//输出序列化后的结果
	fmt.Printf("a map 序列化后=%v\n", string(data))

}

//对切片进行序列化, 切片为 []map[string]interface{}
func testSlice() {
	var slice []map[string]interface{}
	var m1 map[string]interface{}
	//使用map前，需要先make
	m1 = make(map[string]interface{})
	m1["name"] = "jack"
	m1["age"] = "7"
	m1["address"] = "北京"
	slice = append(slice, m1)

	var m2 map[string]interface{}
	//使用map前，需要先make
	m2 = make(map[string]interface{})
	m2["name"] = "tom"
	m2["age"] = "20"
	m2["address"] = [2]string{"墨西哥","夏威夷"}
	slice = append(slice, m2)

	//将切片进行序列化操作
	data, err := json.Marshal(slice)
	if err != nil {
		fmt.Printf("序列化错误 err=%v\n", err)
	}
	//输出序列化后的结果
	fmt.Printf("slice 序列化后=%v\n", string(data))
	
}

//对基本数据类型序列化，对基本数据类型进行序列化意义不大
func testFloat64() {
	var num1 float64 = 2345.67

	//对num1进行序列化
	data, err := json.Marshal(num1)
	if err != nil {
		fmt.Printf("序列化错误 err=%v\n", err)
	}
	//输出序列化后的结果
	fmt.Printf("num1 序列化后=%v\n", string(data))
}

func main() {
	//将结构体, map , 切片进行序列号
	testStruct()
	testMap()
	testSlice()//对切片的序列化
	testFloat64()//对基本数据类型的序列化
}
```

### 2. 反序列化

​		json 反序列化是指，将 json 字符串反序列化成对应的数据类型(比如结构体、map、切片)的操作

```go
func Unmarshal(data []byte, v interface{}) error
```

Unmarshal函数解析json编码的数据并将结果存入v指向的值。

Unmarshal和Marshal做相反的操作，必要时申请映射、切片或指针，有如下的附加规则：

​		要将json数据解码写入一个指针，Unmarshal函数首先处理json数据是json字面值null的情况。此时，函数将指针设为nil；否则，函数将json数据解码写入指针指向的值；如果指针本身是nil，函数会先申请一个值并使指针指向它。

​		要将json数据解码写入一个结构体，函数会匹配输入对象的键和Marshal使用的键（结构体字段名或者它的标签指定的键名），优先选择精确的匹配，但也接受大小写不敏感的匹配。

​		要将json数据解码写入一个接口类型值，函数会将数据解码为如下类型写入接口：

```
Bool                   对应JSON布尔类型
float64                对应JSON数字类型
string                 对应JSON字符串类型
[]interface{}          对应JSON数组
map[string]interface{} 对应JSON对象
nil                    对应JSON的null
```

​		如果一个JSON值不匹配给出的目标类型，或者如果一个json数字写入目标类型时溢出，Unmarshal函数会跳过该字段并尽量完成其余的解码操作。如果没有出现更加严重的错误，本函数会返回一个描述第一个此类错误的详细信息的UnmarshalTypeError。

​		JSON的null值解码为go的接口、指针、切片时会将它们设为nil，因为null在json里一般表示“不存在”。 解码json的null值到其他go类型时，不会造成任何改变，也不会产生错误。

​		当解码字符串时，不合法的utf-8或utf-16代理（字符）对不视为错误，而是将非法字符替换为unicode字符U+FFFD。

```go
package main
import (
	"fmt"
	"encoding/json"
)

//定义一个结构体
type Monster struct {
	Name string  
	Age int 
	Birthday string //....
	Sal float64
	Skill string
}


//将json字符串，反序列化成struct
func unmarshalStruct() {
	//说明str 在项目开发中，是通过网络传输获取到.. 或者是读取文件获取到
	str := "{\"Name\":\"牛魔王~~~\",\"Age\":500,\"Birthday\":\"2011-11-11\",\"Sal\":8000,\"Skill\":\"牛魔拳\"}"

	//定义一个Monster实例
	var monster Monster

	err := json.Unmarshal([]byte(str), &monster)
	if err != nil {
		fmt.Printf("unmarshal err=%v\n", err)
	}
	fmt.Printf("反序列化后 monster=%v monster.Name=%v \n", monster, monster.Name)

}
//将map进行序列化
func testMap() string {
	//定义一个map
	var a map[string]interface{}
	//使用map,需要make
	a = make(map[string]interface{})
	a["name"] = "红孩儿~~~~~~"
	a["age"] = 30
	a["address"] = "洪崖洞"

	//将a这个map进行序列化
	//将monster 序列化
	data, err := json.Marshal(a)
	if err != nil {
		fmt.Printf("序列化错误 err=%v\n", err)
	}
	//输出序列化后的结果
	//fmt.Printf("a map 序列化后=%v\n", string(data))
	return string(data)

}

//将json字符串，反序列化成map
func unmarshalMap() {
	//str := "{\"address\":\"洪崖洞\",\"age\":30,\"name\":\"红孩儿\"}"
	str := testMap()
	//定义一个map
	var a map[string]interface{} 

	//反序列化
	//注意：反序列化map,不需要make,因为make操作被封装到 Unmarshal函数
	err := json.Unmarshal([]byte(str), &a)
	if err != nil {
		fmt.Printf("unmarshal err=%v\n", err)
	}
	fmt.Printf("反序列化后 a=%v\n", a)

}

//将json字符串，反序列化成切片
func unmarshalSlice() {
	str := "[{\"address\":\"北京\",\"age\":\"7\",\"name\":\"jack\"}," + 
		"{\"address\":[\"墨西哥\",\"夏威夷\"],\"age\":\"20\",\"name\":\"tom\"}]"
	
	//定义一个slice
	var slice []map[string]interface{}
	//反序列化，不需要make,因为make操作被封装到 Unmarshal函数
	err := json.Unmarshal([]byte(str), &slice)
	if err != nil {
		fmt.Printf("unmarshal err=%v\n", err)
	}
	fmt.Printf("反序列化后 slice=%v\n", slice)
}

func main() {

	unmarshalStruct()
	unmarshalMap()
	unmarshalSlice()
}
```

PS:

1. 在反序列化一个 json 字符串时，要确保反序列化后的数据类型和原来序列化前的数据类型一致。

2. 如果 json 字符串是通过程序获取到的，则不需要再对 “ 转义处理。

# 第十章、单元测试

## 1. 基本介绍

​		Go 语言中自带有一个轻量级的测试框架 testing 和自带的 go test 命令来实现单元测试和性能测试

![image-20211027103505838](E:\typroa_pic\image-20211027103505838.png)

```go
func TestXxx(*testing.T)
//其中 Xxx 可以是任何字母数字字符串（但第一个字母不能是 [a-z]）
```

​		在`*_test.go`文件中有三种类型的函数，单元测试函数、基准测试函数和示例函数。

|   类型   |         格式          |              作用              |
| :------: | :-------------------: | :----------------------------: |
| 测试函数 |   函数名前缀为Test    | 测试程序的一些逻辑行为是否正确 |
| 基准函数 | 函数名前缀为Benchmark |         测试函数的性能         |
| 示例函数 |  函数名前缀为Example  |       为文档提供示例文档       |

`go test`命令会遍历所有的`*_test.go`文件中符合上述命名规则的函数，然后生成一个临时的main包用于调用相应的测试函数，然后构建并运行、报告测试结果，最后清理测试中生成的临时文件。

## 2. 注意事项

```go
1. 测试用例文件名必须以 _test.go 结尾。 比如 cal_test.go , cal 不是固定的。

2. 测试用例函数必须以 Test 开头，一般来说就是 Test+被测试的函数名，比如 TestAddUpper

3. TestAddUpper(t *tesing.T)	的形参类型必须是 *testing.T 

4. 一个测试用例文件中，可以有多个测试用例函数，比如 TestAddUpper、TestSub

5. 运行测试用例指令
    (1) cmd>go test
    (2) cmd>go test -v	[如果运行正确，无日志，错误时，会输出日志]
[运行正确或是错误，都输出日志]

6. 当出现错误时，可以使用 t.Fatalf 来格式化输出错误信息，并退出程序

7. t.Logf 方法可以输出相应的日志

8. 测试用例函数，并没有放在 main 函数中，也执行了，这就是测试用例的方便之处[原理图].

9. PASS 表示测试用例运行成功，FAIL 表示测试用例运行失败

10. 测试单个文件，一定要带上被测试的原文件
	go test -v cal_test.go cal.go

11. 测试单个方法
	go test -v -test.run	TestAddUpper	
```

## 3. 示例

**monster.go**

```go
package monster
import (
	"encoding/json"
	"io/ioutil"
	"fmt"
)

type Monster struct {
	Name string
	Age int
	Skill string
} 

//给Monster绑定方法Store, 可以将一个Monster变量(对象),序列化后保存到文件中

func (this *Monster) Store() bool {

	//先序列化
	data, err := json.Marshal(this)
	if err != nil {
		fmt.Println("marshal err =", err)
		return false
	} 

	//保存到文件
	filePath := "d:/monster.ser"
	err = ioutil.WriteFile(filePath, data, 0666)
	if err != nil {
		fmt.Println("write file err =", err)
		return false
	}
	return true
}


//给Monster绑定方法ReStore, 可以将一个序列化的Monster,从文件中读取，
//并反序列化为Monster对象,检查反序列化，名字正确
func (this *Monster) ReStore() bool {

	//1. 先从文件中，读取序列化的字符串
	filePath := "d:/monster.ser"
	data, err := ioutil.ReadFile(filePath)
	if err != nil {
		fmt.Println("ReadFile err =", err)
		return false
	}

	//2.使用读取到data []byte ,对反序列化
	err = json.Unmarshal(data, this)
	if err != nil {
		fmt.Println("UnMarshal err =", err)
		return false
	}
	return true
}
```

**monster_test.go**

```go
package monster

 import (
	"testing"
)

//测试用例,测试 Store 方法
func TestStore(t *testing.T) {

	//先创建一个Monster 实例
	monster := &Monster{
		Name : "红孩儿",
		Age :10,
		Skill : "吐火.",
	}
	res := monster.Store()
	if !res {
		t.Fatalf("monster.Store() 错误，希望为=%v 实际为=%v", true, res)
	}
	t.Logf("monster.Store() 测试成功!")
}

func TestReStore(t *testing.T) {

	//测试数据是很多，测试很多次，才确定函数，模块..
	
	//先创建一个 Monster 实例 ， 不需要指定字段的值
	var monster = &Monster{}
	res := monster.ReStore() 
	if !res {
		t.Fatalf("monster.ReStore() 错误，希望为=%v 实际为=%v", true, res)
	}

	//进一步判断
	if monster.Name != "红孩儿" {
		t.Fatalf("monster.ReStore() 错误，希望为=%v 实际为=%v", "红孩儿", monster.Name)
	}

	t.Logf("monster.ReStore() 测试成功!") 
}

```

# 第十一章、goroutine协程 和channel管道

## 1. goroutine(协程)

### 1. 基本介绍

**Go 协程的特点**

1. 有独立的栈空间

2. 共享程序堆空间

3. 调度由用户控制

4. 协程是轻量级的线程

![image-20211027144936215](E:\typroa_pic\image-20211027144936215.png)

```go
package main

import (
	"fmt"
	"time"
)

func test(){
	for i:=0;i<10;i++{
		fmt.Printf("test():hello world--%v\n",i)
		time.Sleep(time.Second)
	}
}

func main(){
	go test()  //开启一个协程
	for i:=0;i<10;i++{
		fmt.Printf("main():hello golang--%v\n",i)
		time.Sleep(time.Second)
	}

}
```

示意图：

![image-20211027150142148](E:\typroa_pic\image-20211027150142148.png)

1. 主线程是一个物理线程，直接作用在 cpu 上的。是重量级的，非常耗费 cpu 资源。

2. 协程从主线程开启的，是轻量级的线程，是逻辑态。对资源消耗相对小。

3. Golang 的协程机制是重要的特点，可以轻松的开启上万个协程。其它编程语言的并发机制是一般基于线程的，开启过多的线程，资源耗费大，这里就突显 Golang 在并发上的优势了

4. 以主线程的退出为主

### 2. goroutine的调度模型

#### 1. MPG 模式基本介绍

GMP模型运行的基本过程：

首先创建一个G对象，然后G被保存在P的本地队列或者全局队列

这时P会唤醒一个M，M寻找一个空闲的P将G移动到它自己，然后M执行一个调度循环：调用G对象->执行->清理线程->继续寻找Goroutine。

在M的执行过程中，上下文切换随时发生。当切换发生，任务的执行现场需要被保护，这样在下一次调度执行可以进行现场恢复。

M的栈保存在G对象，只有现场恢复需要的寄存器(SP,PC等)，需要被保存到G对象。

![image-20211027151054920](E:\typroa_pic\image-20211027151054920.png)

#### 2. MPG 模式运行的状态 1

![image-20211027151109174](E:\typroa_pic\image-20211027151109174.png)

#### 3. MPG模式运行的状态2

![image-20211027151844822](E:\typroa_pic\image-20211027151844822.png)

### 3.设置Golang运行的cpu数

```go
package main

import (
	"fmt"
	"runtime"
)

func main(){
	cpuNum:=runtime.NumCPU()
	fmt.Println("cpuname=",cpuNum)

	//可以自己设置使用多个cpu
	runtime.GOMAXPROCS(cpuNum-1)
	fmt.Println("OK")
}
```

### 4.goroutine之间通讯方式

1) 全局变量的互斥锁

2) 使用管道 channel 来解决

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

var (
	mymap  =make(map[int]int,10)
	//声明一个全局的互斥锁
	//lock 是一个全局的互斥锁，
	//sync 是包: synchornized 同步
	//Mutex : 是互斥
	lock sync.Mutex
)

//计算阶乘并将结果放入到map中
func test(n int){
	res:=1
	for i:=1;i<=n;i++{
		res*=i
	}
	//res加入mymap
	lock.Lock()
	mymap[n]=res
	lock.Unlock()
}

func main(){
	//开20个协程
	for i:=1;i<=20;i++{
		go test(i)
	}

	time.Sleep(time.Second*5)//此处等待时间要另外解决
	//要等协程结束再打印
	lock.Lock()//此处也应加锁
	for i,v :=range mymap{
		fmt.Printf("%d!=%d\n",i,v)
	}
	lock.Unlock()
}
```



## 2.channel(管道)

### 1.为什么需要channel

1. 前面使用全局变量加锁同步来解决 goroutine 的通讯，但不完美

2. 主线程在等待所有 goroutine 全部完成的时间很难确定，我们这里设置 10 秒，仅仅是估算。

3. 如果主线程休眠时间长了，会加长等待时间，如果等待时间短了，可能还有 goroutine 处于工作状态，这时也会随主线程的退出而销毁

4. 通过全局变量加锁同步来实现通讯，也并不利用多个协程对全局变量的读写操作。

### 2.基本介绍

1. channle 本质就是一个数据结构-队列

2. 数据是先进先出【FIFO : first in first out】

3. **线程安全，多 goroutine 访问时，不需要加锁，就是说 channel 本身就是线程安全的（底层数组做循环队列，用到了锁，所以线程安全）**

4. **channel 有类型的，一个 string 的 channel 只能存放 string 类型数据。**

![image-20211027165712402](E:\typroa_pic\image-20211027165712402.png)



### 3.定义/声明channel

```go
var 变量名 chan 数据类型
举例：
	var	intChan	chan	int (intChan 用于存放 int 数据)
	var	mapChan chan map[int]string (mapChan 用于存放 map[int]string 类型)
	var	perChan	chan	Person
	var	perChan2	chan	*Person
...
说明
channel 是引用类型
channel 必须初始化才能写入数据, 即 make 后才能使用
管道是有类型的，intChan 只能写入 整数 int

```

### 4.示例

```go
package main
import (
	"fmt"
)

func main() {

	//管道的使用
	//1. 创建一个可以存放3个int类型的管道
	var intChan chan int
	intChan = make(chan int, 3)

	//2. 看intChan
	fmt.Printf("intChan 的值=%v intChan本身的地址=%p\n", intChan, &intChan)


	//3. 向管道写入数据
	intChan<- 10
	num := 211
	intChan<- num
	intChan<- 50
	// //如果从channel取出数据后，可以继续放入
	<-intChan
	intChan<- 98//注意点, 给管写入数据时，不能超过其容量


	//4. 看看管道的长度和cap(容量)
	fmt.Printf("channel len= %v cap=%v \n", len(intChan), cap(intChan)) // 3, 3

	//5. 从管道中读取数据

	var num2 int
	num2 = <-intChan 
	fmt.Println("num2=", num2)
	fmt.Printf("channel len= %v cap=%v \n", len(intChan), cap(intChan))  // 2, 3

	//6. 在没有使用协程的情况下，如果我们的管道数据已经全部取出，再取就会报告 deadlock

	num3 := <-intChan
	num4 := <-intChan

	//num5 := <-intChan

	fmt.Println("num3=", num3, "num4=", num4/*, "num5=", num5*/)

}
```

注意事项：

1. channel 中只能存放指定的数据类型

2. channle 的数据放满后，就不能再放入了

3. 如果从 channel 取出数据后，可以继续放入

4. 在没有使用协程的情况下，如果 channel 数据取完了，再取，就会报 dead lock

```go
package main

import "fmt"

type Cat struct{
	Name string
	Age int
}

func main(){
	var allChan chan interface{}
	allChan=make(chan interface{},3)
	cat1:=Cat{"tom",18}
	allChan<- 10
	allChan<- "sfsa"
	allChan<- cat1

	//希望得到管道中的第三个元素，则先将前两个出队
	<-allChan
	<-allChan

	cat11:=<-allChan
	fmt.Printf("%T,%v\n",cat11,cat11)
	a:=cat11.(Cat)//管道类型为interface,所以取出来的数据也是interface,要想变回结构体，应使用类型断言
	fmt.Printf("%v",a.Name)

}
```

### 5. channel 的遍历和关闭

#### 1.channel关闭

​		使用内置函数 close 可以关闭 channel, 当 channel 关闭后，就不能再向 channel 写数据了，但是仍然可以从该 channel 读取数据

```go
package main

import "fmt"

func main(){
	mychan:=make(chan int,3)
	mychan<-10
	mychan<-20
	mychan<-30
	close(mychan)//关闭之后不可再写入，但是任然可以取数据，数据取完之后，再继续取会得到0值
	a:=<-mychan
	fmt.Println(a)
}
```

#### 2.chnnel遍历

channel 支持 for--range 的方式进行遍历，注意两个细节
1) 在遍历时，如果 channel 没有关闭，则回出现 deadlock 的错误

2) 在遍历时，如果 channel 已经关闭，则会正常遍历数据，遍历完后，就会退出遍历。

```go
package main

import "fmt"

func main(){
	//遍历管道
	intChan2 := make(chan int, 100)
	for i := 0; i < 100; i++ {
		intChan2<- i * 2  //放入100个数据到管道
	}

	//遍历管道不能使用普通的 for 循环，因为管道长度会变化
	// for i := 0; i < len(intChan2); i++ {

	// }
	//在遍历时，如果channel没有关闭，则会出现deadlock的错误
	//在遍历时，如果channel已经关闭，则会正常遍历数据，遍历完后，就会退出遍历
	close(intChan2)
	for v := range intChan2 {
		fmt.Println("v=", v)
	}
}
```

### 6.channel细节和注意事项

1. channel 可以声明为只读，或者只写性质

```go
package main

import (
	"fmt"
)

func main(){
	//管道可以声明为只读或者可写
	//默认情况下，管道是可读可写的

	//声明为只写
	chan1:=make(chan<- int,3)
	chan1<-10
	fmt.Println(chan1)

	//声明为只读
	var chan2 <-chan int
	num2:=<-chan2
	fmt.Println(num2)
}
```

2. 管道做函数参数时，可以定义为只读或只写，只读和只写是它的属性，不会成为一个新的类型。

3. 使用 select 可以解决从管道取数据的阻塞问题

```go
package main
import (
	"fmt"
	"time"
)

func main() {

	//使用select可以解决从管道取数据的阻塞问题

	//1.定义一个管道 10个数据int
	intChan := make(chan int, 10)
	for i := 0; i < 10; i++ {
		intChan<- i
	}
	//2.定义一个管道 5个数据string
	stringChan := make(chan string, 5)
	for i := 0; i < 5; i++ {
		stringChan <- "hello" + fmt.Sprintf("%d", i)
	}

	//传统的方法在遍历管道时，如果不关闭会阻塞而导致 deadlock

	//不好确定什么关闭该管道.可以使用select 方式可以解决
	//label:
	for {
		select {
		//注意: 这里，如果intChan一直没有关闭，不会一直阻塞而deadlock
		//，会自动到下一个case匹配
		case v := <-intChan :
			fmt.Printf("从intChan读取的数据%d\n", v)
			time.Sleep(time.Second)
		case v := <-stringChan :
			fmt.Printf("从stringChan读取的数据%s\n", v)
			time.Sleep(time.Second)
		default :
			fmt.Printf("都取不到了，不玩了, 程序员可以加入逻辑\n")
			time.Sleep(time.Second)
			return
			//break label 
		}
	}
}
```

4. goroutine 中使用 **recover**，解决协程中出现 panic，导致程序崩溃问题.

```go
package main
import (
	"fmt"
	"time"
)

//函数
func sayHello() {
	for i := 0; i < 10; i++ {
		time.Sleep(time.Second)
		fmt.Println("hello,world")
	}
}
//函数
func test() {
	//这里我们可以使用defer + recover
	defer func() {
		//捕获test抛出的panic
		if err := recover(); err != nil {
			fmt.Println("test() 发生错误", err)
		}
	}()
	//定义了一个map
	var myMap map[int]string
	myMap[0] = "golang" //error
}

func main() {

	go sayHello()
	go test()


	for i := 0; i < 10; i++ {
		fmt.Println("main() ok=", i)
		time.Sleep(time.Second)
	}

}
```



## 3.goroutine和channel结合

![image-20211027181832631](E:\typroa_pic\image-20211027181832631.png)

```go
package main

import (
	"fmt"

)


func writeData(intchan  chan int){
	for i:=0;i<50;i++{
		//放入数据
		intchan<- i
		fmt.Println("写入数据",i)
	}
	close(intchan)
}

func readData(intchan chan int, exitchan chan bool){
	for {
		v,ok :=<-intchan//读不到数据返回error
		if !ok{
			break
		}
		fmt.Printf("readData 读到数据=%v\n",v)
	}

	exitchan<-true
	close(exitchan)
}

func main(){
	intChan:=make(chan int,50)
	exitChan:=make(chan bool,1)

	go writeData(intChan)
	go readData(intChan,exitChan)

	//等待协程运行结束
	for{
		_,ok:=<-exitChan
		if ok{//读到true就结束
			break
		}
	}
}
```

**ps:只要管道中的数据在流动（写慢读快或写快读慢），就不会因为阻塞发生死锁，**

2. 要求统计 1-8000 的数字中，哪些是素数？

![image-20211027223811476](E:\typroa_pic\image-20211027223811476.png)

```go
package main

import (
	"fmt"
)

//写入数据
func putNum(intChan chan int){
	for i:=0;i<8000;i++{
		//time.Sleep(time.Microsecond)
		intChan<- i

		fmt.Println("写入数据",i)
	}
	close(intChan)//写完数据后关闭管道，便于其他协程遍历
}
func primeNum(intChan chan int,primeChan chan int,exitChan chan bool){

	var flag bool
	for {
		num ,ok:=<-intChan
		//fmt.Println("取出数据",num)
		if !ok{ //管道取不到数据
			break
		}
		flag=true
		//判断是否为素数
		for i:=2;i<num;i++{
			if num%i==0{
				flag=false
				break
			}
		}
		if flag{//flag为true则该数为素数
			primeChan<-num
		}
	}
	fmt.Println("有一个primeNum协程取不到数据，退出")
	exitChan<- true
}




func main(){
	intChan :=make(chan int,1000)
	primeChan:=make(chan int,2000)
	exitChan:=make(chan bool,4)

	//开启一个协程写入数据
	go putNum(intChan)

	//开启四个协程读取数据判断是否为素数
	for i:=0;i<4;i++{
		go primeNum(intChan,primeChan,exitChan)
		//fmt.Println("开启一个协程")
	}

	go func() {
		for i:=0;i<4;i++{
			<-exitChan
		}
		close(primeChan)
	}()


	for {
		res,ok:=<-primeChan
		if !ok{
			break
		}
		fmt.Printf("素数=%d\n",res)
	}

	fmt.Println("主线程退出")

}
```

# 第十二章、反射

**PS：反射是运行时反射**

## 1.使用场景

1. 使用反射机制，为结构体字段起序列化之后的名称
2. 使用反射机制，编写函数的适配器, 桥连接。

![image-20211028092543005](E:\typroa_pic\image-20211028092543005.png)

## 2.基本介绍

1) 反射可以在运行时动态获取变量的各种信息, 比如变量的类型(type)，类别(kind)

2) 如果是结构体变量，还可以获取到结构体本身的信息(包括结构体的字段、方法)

3) 通过反射，可以修改变量的值，可以调用关联的方法。

4) 使用反射，需要 import (“reflect”)

![image-20211028092719526](E:\typroa_pic\image-20211028092719526.png)

## 3.反射重要的函数和概念

![image-20211028094243797](E:\typroa_pic\image-20211028094243797.png)

3) 变量、interface{} 和 reflect.Value 是可以相互转换的，这点在实际开发中，会经常使用到

```go
var student Stu 
var num int 
```

变量、interface{} 和 reflect.Value的转换，在使用反射的过程中，通常的方式是：

```go
//专门用于做反射
func  test(b   interface{})   {
       
          //1. 如何将 Interface{} 转成  reflect.Val ue
           rVal :=  reflect.ValueOf(b) 
         
         // 2. 如何将  reflect.Value -> interface{}
          iVal := rVal.Interface() 
         // 3. 如何将 inteface{} 转成 原来的变量类型,使用类型断言
        v := iVal.(Stu)
}

```

![image-20211028094312239](E:\typroa_pic\image-20211028094312239.png)

## 4.示例

​		对(基本数据类型、interface{}、reflect.Value)进行反射的基本操作，对(结构体类型、interface{}、reflect.Value)进行反射的基本操作。

```go
package main
import(
	"fmt"
	"reflect"
)
//基本类型
func reflectTest(b interface{}){
	//通过反射获取传入变量的type ,kind和值

	//1.先获取到 reflect.Type
	rTyp:=reflect.TypeOf(b)
	fmt.Println("n2=",rTyp)

	//2.获取到 reflect.value
	rVal:=reflect.ValueOf(b)
	n2:=rVal.Int()+2
	fmt.Println("rType=",n2)
	fmt.Printf("rType=%v,rTyp=%T\n",rVal,rVal.Type())

	//将 rval转成interfa
	ival:=rVal.Interface()

	//将interface通过类型断言转成需要的类型
	num:=ival.(int)
	fmt.Println("v=",num)
}

//结构体类型
func reflectTest02(b interface{}){
	//1.先获取到 reflect.Type
	rTyp:=reflect.TypeOf(b)
	fmt.Println(rTyp)

	//2.获取到 reflect.value
	rVal:=reflect.ValueOf(b)
	fmt.Println(rVal)

	//将 rVal转成interfa

	ival:=rVal.Interface()
	fmt.Printf("rType=%v,rTyp=%T\n",ival,ival)

	//反射是运行时反射，编辑阶段并不能知道ival是结构体类型，所以需要类型断言
	//将interface通过类型断言转成需要的类型
	rstu,ok:=ival.(Student)
	if ok{
		fmt.Println("stu.Name=",rstu.Name)
	}

}
type Student struct {
	Name string
	Age int
}
func main() {

	////定义一个int
	//var num int =100
	//reflectTest(num)

	//定义一个student实例
	stu:=Student{Name: "小王",Age: 18}
	reflectTest02(stu)

}
```



## 5.注意事项和细节

1) reflect.Value.Kind，获取变量的类别，返回的是一个常量

2) Type 和 Kind 的区别
Type 是类型, Kind 是类别， Type 和 Kind 可能是相同的，也可能是不同的.
	比如:	var num int = 10	num 的 Type 是 int , Kind 也是 int
	比如:	var stu Student	stu 的 Type 是 pkg1.Student , Kind 是 struct

3) 通过反射的来修改变量, 注意当使用 SetXxx 方法来设置需要通过对应的指针类型来完成, 这样才能改变传入的变量的值, 同时需要使用到 reflect.Value.Elem()方法，**因为引用传递会传递地址，所以需要用Elem()方法是返回指针指向的值。**

```go

package main

import (
	"fmt"
	"reflect"
)

//通过反射，修改,
// num int 的值
// 修改 student的值

func reflect01(b interface{}) {
	//2. 获取到 reflect.Value
	rVal := reflect.ValueOf(b)
	// 看看 rVal的Kind是
	fmt.Printf("rVal kind=%v\n", rVal.Kind())
	//3. rVal
	//Elem返回v持有的接口保管的值的Value封装，或者v持有的指针指向的值的Value封装
	rVal.Elem().SetInt(20)
}

func main() {

	var num int = 10
	reflect01(&num)
	fmt.Println("num=", num) // 20


	//可以这样理解rVal.Elem()
	// num := 9
	// ptr *int = &num
	// num2 := *ptr  //=== 类似 rVal.Elem()
}
```

## 6.实践

1. 使用反射来遍历结构体的字段，调用结构体的方法，并获取结构体标签的值
2. 使用反射的方式来获取结构体的 tag 标签, 遍历字段的值，修改字段值，调用结构体方法

```go
package main
import (
	"fmt"
	"reflect"
)
//定义了一个Monster结构体
type Monster struct {
	Name  string `json:"name"`
	Age   int `json:"monster_age"`
	Score float32 `json:"成绩"`
	Sex   string
	
}

//方法，返回两个数的和
func (s Monster) GetSum(n1, n2 int) int {
	return n1 + n2
}
//方法， 接收四个值，给s赋值
func (s Monster) Set(name string, age int, score float32, sex string) {
	s.Name = name
	s.Age = age
	s.Score = score
	s.Sex = sex
}

//方法，显示s的值
func (s Monster) Print() {
	fmt.Println("---start~----")
	fmt.Println(s)
	fmt.Println("---end~----")
}
func TestStruct(a interface{}) {
	//获取reflect.Type 类型
	typ := reflect.TypeOf(a)
	//获取reflect.Value 类型
	val := reflect.ValueOf(a)
	//获取到a对应的类别
	kd := val.Kind()
	//如果传入的不是struct，就退出
	if kd !=  reflect.Struct {
		fmt.Println("expect struct")
		return
	}

	//获取到该结构体有几个字段
	num := val.NumField()

	fmt.Printf("struct has %d fields\n", num) //4
	//变量结构体的所有字段
	for i := 0; i < num; i++ {
		fmt.Printf("Field %d: 值为=%v\n", i, val.Field(i))
		//获取到struct标签, 注意需要通过reflect.Type来获取tag标签的值
		tagVal := typ.Field(i).Tag.Get("json")
		//如果该字段于tag标签就显示，否则就不显示
		if tagVal != "" {
			fmt.Printf("Field %d: tag为=%v\n", i, tagVal)
		}
	}
	
	//获取到该结构体有多少个方法
	numOfMethod := val.NumMethod()
	fmt.Printf("struct has %d methods\n", numOfMethod)
	
	//var params []reflect.Value
	//方法的排序默认是按照 函数名的排序（ASCII码）
	val.Method(1).Call(nil) //获取到第二个方法。调用它

	
	//调用结构体的第1个方法Method(0)
	var params []reflect.Value  //声明了 []reflect.Value
	params = append(params, reflect.ValueOf(10))
	params = append(params, reflect.ValueOf(40))
	res := val.Method(0).Call(params) //传入的参数是 []reflect.Value, 返回[]reflect.Value
	fmt.Println("res=", res[0].Int()) //返回结果, 返回的结果是 []reflect.Value*/

}
func main() {
	//创建了一个Monster实例
	var a Monster = Monster{
		Name:  "黄鼠狼精",
		Age:   400,
		Score: 30.8,
	}
	//将Monster实例传递给TestStruct函数
	TestStruct(a)	
}

```

# 第十三章、网络编程

## 1. 基本介绍

网络编程有两种:

1. TCP socket 编程，是网络编程的主流。之所以叫 Tcp socket 编程，是因为底层是基于 Tcp/ip 协议

2. b/s 结构的 http 编程，我们使用浏览器去访问服务器时，使用的就是 http 协议，而 http 底层依
   旧是用 tcp socket 实现的

## 2.端口注意

1. 一个端口只能被一个程序监听

2. 如果使用 netstat –an 可以查看本机有哪些端口在监听

3. 可以使用 netstat –anb 来查看监听端口的 pid,在结合任务管理器关闭不安全的端口

## 3.socket编程

### 1.服务器端处理流程

1) 监听端口 8888

2) 接收客户端的 tcp 链接，建立客户端和服务器端的链接.

3) 创建 **goroutine**，处理该链接的请求(通常客户端会通过链接发送请求包)

```go
package main
import(
"fmt"
	"io"
	"net"
	"strings"
)

func recData(conn net.Conn){
	//循环读取客户端发送的数据
	defer conn.Close()//关闭conn连接

	for{
		//创建新切片
		buf:=make([]byte,1024)
		n,err:=conn.Read(buf)//从conn读取，read为阻塞函数
		//fmt.Print("服务器在等待客户端："+conn.RemoteAddr().String()+"的信息\n")
		if err==io.EOF{
			fmt.Println("客户端退出",err)
			return
		}
		//显示数据在服务器终端
		fmt.Print(string(buf[:n]))//显示到实际读取的内容
		str:=strings.ToUpper(string(buf[:n]))
		conn.Write([]byte(str))
	}
}

func main(){

	//服务器设置监听
	//表示使用tcp协议，本地监听8888端口
	ln,err:=net.Listen("tcp","0.0.0.0:8888")
	if err!=nil{
		fmt.Println("listen err",err)
		return
	}
	defer ln.Close() //延时关闭连接
	//循环等待客户端连接
	for {
		//等待客户端连接
		fmt.Println("等待客户端连接")
		conn,err:=ln.Accept()
		if err!=nil{
			fmt.Println("accept() err+",err)
		}else {
			fmt.Printf("conn:%v,客户端Ip:%v\n",conn,conn.RemoteAddr().String())
		}

		//开启一个协程为客户端连接服务
		go recData(conn)
	}


}

```



### 2.客户端处理流程

1) 建立与服务端的链接

2) 发送请求数据[终端]，接收服务器端返回的结果数据

3) 关闭链接

```go
package main
import (
	"bufio"
	"fmt"
	"io"
	"net"
	"os"
	"strings"
)
func main() {
	conn,err:=net.Dial("tcp","172.23.17.219:8888")
	defer conn.Close()
	if err!=nil{
		fmt.Println("dial err",err)
		return
	}
	for {
		//发送数据
		reader := bufio.NewReader(os.Stdin) //标准输入

		//从终端读取输入
		line, err := reader.ReadString('\n')
		if err != nil {
			fmt.Println("read err")
		}
		line = strings.Trim(line," \r\n")
		if line=="exit"{
			break
		}
		//给服务器发送数据
		n, err := conn.Write([]byte(line))
		if err != nil {
			fmt.Println("conn.write err=", err)
		}
		fmt.Printf("传输了%d个字节\n", n)

		//读取数据
		buf:=make([]byte,1024)
		m,err:=conn.Read(buf)//从conn读取，read为阻塞函数
		fmt.Print("等待服务端的信息\n")
		if err==io.EOF{
			fmt.Println("客户端退出",err)
			return
		}
		//显示数据在客户端终端
		fmt.Print(string(buf[:m]))//显示到实际读取的内容
	}
}

```

# 第十四章、数据库使用

## 1. Redis

说明: Redis 安装好后，默认有 16 个数据库，初始默认使用 0 号库, 编号是 0...15

1. 切换 redis 数据库 [select index]

2. 如何查看当前数据库的 key-val 数量 [dbsize]

3. 清空当前数据库的 key-val 和清空所有数据库的 key-val [flushdb flushall]

> 1. 知道redis是什么?
>    - 非关系型数据库 也可以叫 内存数据库
> 2. 能干什么?
>    - 存储访问频率高的数据
>    - 共享内存
>      - 服务器端 -> redis
> 3. 怎么使用?
>    - 常用的操作命令
>      - 各种数据类型 -> 会查
>    - redis的配置文件
>    - redis的数据持久化
>    - 写程序的时候如何对redis进行操作
>      - 客户端 -> 服务器

### 1.1 基本知识点

1. 安装包下载

   - 英文官方： <https://redis.io/>
   - 中文官方： <http://redis.cn/>

2. Redis安装

   - make
   - make install

3. redis中的两个角色

   ```shell
   # 服务器 - 启动
   redis-server	# 默认启动
   redis-server confFileName # 根据配置文件的设置启动
   # 客户端
   redis-cli	# 默认连接本地, 绑定了6379默认端口的服务器
   redis-cli -p 端口号
   redis-cli -h IP地址 -p 端口 # 连接远程主机的指定端口的redis
   # 通过客户端关闭服务器
   shutdown
   # 客户端的测试命令
   ping [MSG]
   ```

4. redis中数据的组织格式

   - 键值对
     - key: 必须是字符串 - "hello"
     - value: 可选的
       - String类型
       - List类型
       - Set类型
       - SortedSet类型
       - Hash类型

5. redis中常用数据类型

   - String类型
     - 字符串
   - List类型
     - 存储多个string字符串的
   - Set类型
     - 集合
       - stl集合
         - 默认是排序的, 元素不重复
       - redis集合
         - 元素不重复, 数据是无序的
   - SortedSet类型
     - 排序集合, 集合中的每个元素分为两部分
       - [分数, 成员] -> [66, ''tom'']
   - Hash类型
     - 跟map数据组织方式一样: key:value
       - Qt -> QHash, QMap
       - Map -> 红黑树
       - hash -> 数组
         - a[index] = xx

### 1.2 redis常用命令

- String类型

  ```shell
  key -> string
  value -> string
  # 设置一个键值对->string:string
  SET key value
  
  # 通过key得到value
  GET key
  
  # 同时设置一个或多个 key-value 对
  MSET key value [key value ...]
  
  # 同时查看过个key
  MGET key [key ...]
  
  # 如果 key 已经存在并且是一个字符串， APPEND 命令将 value 追加到 key 原来的值的末尾
  # key: hello, value: world, append: 12345
  APPEND key value
  
  # 返回 key 所储存的字符串值的长度
  STRLEN key
  
  # 将 key 中储存的数字值减一。
  # 前提, value必须是数字字符串 -"12345"
  DECR key
  ```

- List类型 - 存储多个字符串

  ```shell
  key -> string
  value -> list
  # 将一个或多个值 value 插入到列表 key 的表头
  LPUSH key value [value ...]
  # 将一个或多个值 value 插入到列表 key 的表尾 (最右边)。
  RPUSH key value [value ...]
  # list中删除元素
  LPOP key # 删除最左侧元素
  RPOP key # 删除最右侧元素
  # 遍历
  LRANGE key start stop
  	start: 起始位置, 0
  	stop: 结束位置, -1
  # 通过下标得到对应位置的字符串
  LINDEX key index
  # list中字符串的个数
  LLEN key
  ```

- Set类型

  ```shell
  key -> string
  value -> set类型 ("string", "string1")
  # 添加元素
  # 将一个或多个 member 元素加入到集合 key 当中，已经存在于集合的 member 元素将被忽略
  SADD key member [member ...]
  
  # 遍历
  SMEMBERS key
  
  # 判断是否成员
  SISMEMBER key
  
  # 差集
  SDIFF key [key ...]
  
  # 交集
  SINTER key [key ...]
  
  # 并集
  SUNION key [key ...]
  ```

- ZSet 类型

  ```shell
  key -> string
  value -> sorted ([socre, member], [socre, member], ...)
  # 添加元素
  ZADD key score member [[score member] [score member] ...]
  
  # 遍历
  ZRANGE key start stop [WITHSCORES] # -> 升序集合
  ZREVRANGE key start stop [WITHSCORES] # -> 降序集合
  
  # 指定分数区间内元素的个数
  ZCOUNT key min max
  ```

- Hash类型

  ![](E:\typroa_pic\2.png)

  ```shell
  key ->string
  value -> hash ([key:value], [key:value], [key:value], ...)
  # 添加数据
  HSET key field value
  
  # 取数据
  HGET key field
  
  # 批量插入键值对
  HMSET key field value [field value ...]
  
  # 批量取数据
  HMGET key field [field ...]
  
  # 删除键值对
  HDEL key field [field ...]
  ```

- Key 相关的命令

  ```shell
  # 删除键值对
  DEL key [key ...]
  
  # 查看key值
  KEYS pattern
  查找所有符合给定模式 pattern 的 key 。
  KEYS * 匹配数据库中所有 key 。
  KEYS h?llo 匹配 hello ， hallo 和 hxllo 等。
  KEYS h*llo 匹配 hllo 和 heeeeello 等。
  KEYS h[ae]llo 匹配 hello 和 hallo ，但不匹配 hillo
  
  # 给key设置生存时长
  EXPIRE key seconds
  
  # 取消生存时长
  PERSIST key
  
  # key对应的valued类型
  TYPE key
  ```


### 1.3 redis配置文件

> 配置文件是给**redis服务器**使用 的

1. 配置文件位置

   - 从源码安装目录中找 -> redis.conf

2. 配置文件配置项

   ```shell
   # redis服务器绑定谁之后, 谁就能访问redis服务器
   # 任何客户端都能访问服务器, 需要注释该选项
   bind 127.0.0.1 192.168.1.100 
   
   # 保护模式, 如果要远程客户端访问服务器, 该模式要关闭
   protected-mode yes
   
   # reids服务器启动时候绑定的端口, 默认为6379 
   port 6379
   
   # 超时时长, 0位关闭该选项, >0则开启
   timeout 0
   
   # 服务器启动之后不是守护进程
   daemonize no
   # 如果服务器是守护进程, 就会生成一个pid文件
   # ./ -> reids服务器启动时候对应的目录
   pidfile ./redis.pid
   
   # 日志级别
    loglevel notice
    
   # 如果服务器是守护进程, 才会写日志文件
    logfile "" -> 这是没写
    logfile ./redis.log
    
    # redis中数据库的个数
    databases 16 
    	- 切换 select dbID [dbID == 0 ~ 16-1]
   ```

### 1.4 redis数据持久化

> 持久化: 数据从内存到磁盘的过程

持久化的两种方式:

- rdb方式
  - 这是一种默认的持久化方式, 默认打开
  - 磁盘的持久化文件xxx.rdb
  - 将内存数据以二进制的方式直接写入磁盘文件
  - 文件比较小, 恢复时间短, 效率高
  - 以用户设定的频率 -> 容易丢失数据
  - 数据完整性相对较低
- aof方式
  - 默认是关闭的
  - 磁盘的持久化文件xxx.aof
  - 直接将生成数据的命令写入磁盘文件
  - 文件比较大, 恢复时间长, 效率低
  - 以某种频率 -> 1sec
  - 数据完整性高

```shell
# rdb的同步频率, 任意一个满足都可以
save 900 1
save 300 10
save 60 10000

# rdb文件的名字
dbfilename dump.rdb

# 生成的持久化文件保存的那个目录下, rdb和aof
dir ./ 

# 是不是要打开aof模式
appendonly no
 -> 打开: yes
 
# 设置aof文件的名字
appendfilename "appendonly.aof"

# aof更新的频率
# appendfsync always
appendfsync everysec
# appendfsync no
```

1. aof和rdb能不能同时打开?

   - 可以

2. aof和rdb能不能同时关闭?

   - 可以

   - rdb如何关闭?

     ```shell
     save ""
     ```

3. 两种模式同时开启, 如果要进行数据恢复, 如何选择?

   - 效率上考虑:  rdb模式
   - 数据的完整性: aof模式

### 1.5 go连接redis

可以使用redigo或者go-redis等多种第三方开源库，详情看文档，基础使用样例：

```go
package main

import (
	"fmt"
	"github.com/garyburd/redigo/redis"
)

// 创建 redis 客户端
func createClient()  {

}

func main(){
	//通过go向redis写入和读取数据
	//1.连接到redis
	conn,err:=redis.Dial("tcp","127.0.0.1:6379")
	if err!=nil{
		fmt.Println("redis.Dial err",err)
		return
	}
	defer conn.Close() //关闭连接
	//通过go向redis写入数据
	_,err =conn.Do("set","Age","18")
	if err!=nil{
		fmt.Println("set.Do err",err)
		return
	}

	//通过go向redis获取数据
	r,err :=redis.Strings(conn.Do("keys","*"))
	if err!=nil{
		fmt.Println("get.Do err",err)
		return
	}

	//因为返回r是空接口intreface{}，所以要转成对应类型

	fmt.Println("redis operate success",r)

}
```



### 1.6 redis连接池

1) 事先初始化一定数量的连接，放入到连接池

2) 当 Go 需要操作 Redis 时，直接从 **Redis** 链接池取出链接即可。

3) 这样可以节省临时获取 **Redis** 链接的时间，从而提高效率.

```go
package main

import (
	"fmt"
	"github.com/garyburd/redigo/redis"
)
//定义一个全局的 pool
var pool *redis.Pool
//当启动程序时，就初始化连接池
func init() {//init()函数在main函数之前执行
	pool = &redis.Pool{ //返回的是一个结构体，所以返回引用
		MaxIdle: 8, //最大空闲链接数
		MaxActive: 0, // 表示和数据库的最大链接数， 0 表示没有限制
		IdleTimeout: 100, // 最大空闲时间
		Dial: func() (redis.Conn, error){ // 初始化链接的代码， 链接哪个 ip 的 redis
			return redis.Dial("tcp", "localhost:6379")
		},
	}
}
func main() {
	//先从 pool 取出一个链接
	conn := pool.Get()
	defer conn.Close()
	_, err := conn.Do("Set", "name", "汤姆猫~~")
	if err != nil {
		fmt.Println("conn.Do err=", err)
		return
	}
	//取出
	r, err := redis.String(conn.Do("Get", "name"))
	if err != nil {
		fmt.Println("conn.Do err=", err)
		return
	}
	fmt.Println("r=", r)
	//如果我们要从 pool 取出链接，一定保证链接池是没有关闭
	//pool.Close()
	conn2 := pool.Get()
	_, err = conn2.Do("Set", "name2", "汤姆猫~~2")
	if err != nil {
		fmt.Println("conn.Do err~~~~=", err)
		return
	}
	//取出
	r2, err := redis.String(conn2.Do("Get", "name2"))
	if err != nil {
		fmt.Println("conn.Do err=", err)
		return
	}
	fmt.Println("r=", r2)
	//fmt.Println("conn2=", conn2)
}
```

## 2.MySQL

​		go为关系型数据库提供了一套sql标准——`database/sql`，所有数据库的使用方法都是相同的，只需要引入不同的驱动即可；

​		Open函数可能只是验证其参数格式是否正确，实际上并不创建与数据库的连接。如果要检查数据源的名称是否真实有效，应该调用Ping方法。

​		**返回的DB对象可以安全地被多个goroutine并发使用，并且维护其自己的空闲连接池**。因此，Open函数应该仅被调用一次，很少需要关闭这个DB对象。

```go
package main

import (
	"database/sql"
	"fmt"
	_"github.com/go-sql-driver/mysql"
)
//Go连接Mysql示例

func main(){
	//连接数据库
	dsn:="user:dbpasswd@tcp(127.0.0.1:3306)/dbname"
	db, err := sql.Open("mysql", dsn)//不会校验用户名和密码是否正确，只会校验格式是否正确
	if err != nil {//格式不正确时报错
		fmt.Printf("dsn value %s err,err:%v\n",dsn,err)
		return
	}
	//尝试建立连接（校验dsn是否正确）
	err = db.Ping()
	if err != nil {
		fmt.Printf("open %s faild,err:%v\n",dsn,err)
	}

	fmt.Println("连接数据库成功！")
	defer db.Close()  // 注意这行代码要写在上面err判断的下面
}
```

### SetMaxOpenConns

```go
func (db *DB) SetMaxOpenConns(n int)
```

`SetMaxOpenConns`设置与数据库建立连接的最大数目。 如果n大于0且小于最大闲置连接数，会将最大闲置连接数减小到匹配最大开启连接数的限制。 如果n<=0，不会限制最大开启连接数，默认为0（无限制）。

### SetMaxIdleConns

```go
func (db *DB) SetMaxIdleConns(n int)
```

SetMaxIdleConns设置连接池中的最大闲置连接数。 如果n大于最大开启连接数，则新的最大闲置连接数会减小到匹配最大开启连接数的限制。 如果n<=0，不会保留闲置连接。

### 查询

为了方便查询，我们事先定义好一个结构体来存储user表的数据。

```go
type user struct {
	id   int
	age  int
	name string
}
```

#### 单行查询

单行查询`db.QueryRow()`执行一次查询，并期望返回最多一行结果（即Row）。QueryRow总是返回非nil的值，直到返回值的Scan方法被调用时，才会返回被延迟的错误。（如：未找到结果）

```go
func (db *DB) QueryRow(query string, args ...interface{}) *Row
```

具体示例代码：

```go
// 查询单条数据示例
func queryRowDemo() {
	sqlStr := "select id, name, age from user where id=?"
	var u user
	// 非常重要：确保QueryRow之后调用Scan方法，否则持有的数据库链接不会被释放
	err := db.QueryRow(sqlStr, 1).Scan(&u.id, &u.name, &u.age)//调用Scan接收返回的值，且其中有释放连接的方法，所以必须调用
	if err != nil {
		fmt.Printf("scan failed, err:%v\n", err)
		return
	}
	fmt.Printf("id:%d name:%s age:%d\n", u.id, u.name, u.age)
}
```

#### 多行查询

多行查询`db.Query()`执行一次查询，返回多行结果（即Rows），一般用于执行select命令。参数args表示query中的占位参数。

```go
func (db *DB) Query(query string, args ...interface{}) (*Rows, error)
```

具体示例代码：

```go
// 查询多条数据示例
func queryMultiRowDemo() {
	sqlStr := "select id, name, age from user where id > ?"
	rows, err := db.Query(sqlStr, 0)
	if err != nil {
		fmt.Printf("query failed, err:%v\n", err)
		return
	}
	// 非常重要：关闭rows释放持有的数据库链接
	defer rows.Close()

	// 循环读取结果集中的数据
	for rows.Next() {
		var u user
		err := rows.Scan(&u.id, &u.name, &u.age)
		if err != nil {
			fmt.Printf("scan failed, err:%v\n", err)
			return
		}
		fmt.Printf("id:%d name:%s age:%d\n", u.id, u.name, u.age)
	}
}
```



### 插入数据

插入、更新和删除操作都使用`Exec`方法。

```go
func (db *DB) Exec(query string, args ...interface{}) (Result, error)
```

Exec执行一次命令（包括查询、删除、更新、插入等），返回的Result是对已执行的SQL命令的总结。参数args表示query中的占位参数。

具体插入数据示例代码如下：

```go
// 插入数据
func insertRowDemo() {
	sqlStr := "insert into user(name, age) values (?,?)"
	ret, err := db.Exec(sqlStr, "王五", 38)
	if err != nil {
		fmt.Printf("insert failed, err:%v\n", err)
		return
	}
	theID, err := ret.LastInsertId() // 新插入数据的id
	if err != nil {
		fmt.Printf("get lastinsert ID failed, err:%v\n", err)
		return
	}
	fmt.Printf("insert success, the id is %d.\n", theID)
}
```

### 更新数据

具体更新数据示例代码如下：

```go
// 更新数据
func updateRowDemo() {
	sqlStr := "update user set age=? where id = ?"
	ret, err := db.Exec(sqlStr, 39, 3)
	if err != nil {
		fmt.Printf("update failed, err:%v\n", err)
		return
	}
	n, err := ret.RowsAffected() // 操作影响的行数
	if err != nil {
		fmt.Printf("get RowsAffected failed, err:%v\n", err)
		return
	}
	fmt.Printf("update success, affected rows:%d\n", n)
}
```

### 删除数据

具体删除数据的示例代码如下：

```go
// 删除数据
func deleteRowDemo() {
	sqlStr := "delete from user where id = ?"
	ret, err := db.Exec(sqlStr, 3)
	if err != nil {
		fmt.Printf("delete failed, err:%v\n", err)
		return
	}
	n, err := ret.RowsAffected() // 操作影响的行数
	if err != nil {
		fmt.Printf("get RowsAffected failed, err:%v\n", err)
		return
	}
	fmt.Printf("delete success, affected rows:%d\n", n)
}
```

### 什么是预处理？

普通SQL语句执行过程：

1. 客户端对SQL语句进行占位符替换得到完整的SQL语句。
2. 客户端发送完整SQL语句到MySQL服务端
3. MySQL服务端执行完整的SQL语句并将结果返回给客户端。

预处理执行过程：

1. 把SQL语句分成两部分，命令部分与数据部分。
2. 先把命令部分发送给MySQL服务端，MySQL服务端进行SQL预处理。
3. 然后把数据部分发送给MySQL服务端，MySQL服务端对SQL语句进行占位符替换。
4. MySQL服务端执行完整的SQL语句并将结果返回给客户端。

### 为什么要预处理？

1. 优化MySQL服务器重复执行SQL的方法，可以提升服务器性能，提前让服务器编译，一次编译多次执行，节省后续编译的成本。
2. 避免SQL注入问题。

### Go实现MySQL预处理

`database/sql`中使用下面的`Prepare`方法来实现预处理操作。

```go
func (db *DB) Prepare(query string) (*Stmt, error)
```

`Prepare`方法会先将sql语句发送给MySQL服务端，返回一个准备好的状态用于之后的查询和命令。返回值可以同时执行多个查询和命令。

查询操作的预处理示例代码如下：

```go
// 预处理查询示例
func prepareQueryDemo() {
	sqlStr := "select id, name, age from user where id > ?"
	stmt, err := db.Prepare(sqlStr)
	if err != nil {
		fmt.Printf("prepare failed, err:%v\n", err)
		return
	}
	defer stmt.Close()
	rows, err := stmt.Query(0)
	if err != nil {
		fmt.Printf("query failed, err:%v\n", err)
		return
	}
	defer rows.Close()
	// 循环读取结果集中的数据
	for rows.Next() {
		var u user
		err := rows.Scan(&u.id, &u.name, &u.age)
		if err != nil {
			fmt.Printf("scan failed, err:%v\n", err)
			return
		}
		fmt.Printf("id:%d name:%s age:%d\n", u.id, u.name, u.age)
	}
}
```

插入、更新和删除操作的预处理十分类似，这里以插入操作的预处理为例：

```go
// 预处理插入示例
func prepareInsertDemo() {
	sqlStr := "insert into user(name, age) values (?,?)"
	stmt, err := db.Prepare(sqlStr)
	if err != nil {
		fmt.Printf("prepare failed, err:%v\n", err)
		return
	}
	defer stmt.Close()
	_, err = stmt.Exec("小王子", 18)
	if err != nil {
		fmt.Printf("insert failed, err:%v\n", err)
		return
	}
	_, err = stmt.Exec("沙河娜扎", 18)
	if err != nil {
		fmt.Printf("insert failed, err:%v\n", err)
		return
	}
	fmt.Println("insert success.")
}
```

### SQL注入问题

**任何时候都不应该自己拼接SQL语句！**

这里演示一个自行拼接SQL语句的示例，编写一个根据name字段查询user表的函数如下：

```go
// sql注入示例
func sqlInjectDemo(name string) {
	sqlStr := fmt.Sprintf("select id, name, age from user where name='%s'", name)//用户自己拼接SQL语句
	fmt.Printf("SQL:%s\n", sqlStr)
	var u user
	err := db.QueryRow(sqlStr).Scan(&u.id, &u.name, &u.age)
	if err != nil {
		fmt.Printf("exec failed, err:%v\n", err)
		return
	}
	fmt.Printf("user:%#v\n", u)
}
```

此时以下输入字符串都可以引发SQL注入问题：

```go
sqlInjectDemo("xxx' or 1=1#")
sqlInjectDemo("xxx' union select * from user #")
sqlInjectDemo("xxx' and (select count(*) from user) <10 #")
```

**补充：**不同的数据库中，SQL语句使用的占位符语法不尽相同。

|   数据库   |  占位符语法  |
| :--------: | :----------: |
|   MySQL    |     `?`      |
| PostgreSQL | `$1`, `$2`等 |
|   SQLite   |  `?` 和`$1`  |
|   Oracle   |   `:name`    |

### 什么是事务？

事务：一个最小的不可再分的工作单元；通常一个事务对应一个完整的业务(例如银行账户转账业务，该业务就是一个最小的工作单元)，同时这个完整的业务需要执行多次的DML(insert、update、delete)语句共同联合完成。A转账给B，这里面就需要执行两次update操作。

在MySQL中只有使用了`Innodb`数据库引擎的数据库或表才支持事务。事务处理可以用来维护数据库的完整性，保证成批的SQL语句要么全部执行，要么全部不执行。

### 事务的ACID

通常事务必须满足4个条件（ACID）：原子性（Atomicity，或称不可分割性）、一致性（Consistency）、隔离性（Isolation，又称独立性）、持久性（Durability）。

|  条件  |                             解释                             |
| :----: | :----------------------------------------------------------: |
| 原子性 | 一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。 |
| 一致性 | 在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。 |
| 隔离性 | 数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）。 |
| 持久性 | 事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。 |

### 事务相关方法

Go语言中使用以下三个方法实现MySQL中的事务操作。 

开始事务

```go
func (db *DB) Begin() (*Tx, error)
```

提交事务

```go
func (tx *Tx) Commit() error
```

回滚事务

```go
func (tx *Tx) Rollback() error
```

### 事务示例

下面的代码演示了一个简单的事务操作，该事物操作能够确保两次更新操作要么同时成功要么同时失败，不会存在中间状态。

```go
// 事务操作示例
func transactionDemo() {
	tx, err := db.Begin() // 开启事务
	if err != nil {
		if tx != nil {
			tx.Rollback() // 回滚
		}
		fmt.Printf("begin trans failed, err:%v\n", err)
		return
	}
	sqlStr1 := "Update user set age=30 where id=?"
	ret1, err := tx.Exec(sqlStr1, 2)
	if err != nil {
		tx.Rollback() // 回滚
		fmt.Printf("exec sql1 failed, err:%v\n", err)
		return
	}
	affRow1, err := ret1.RowsAffected()
	if err != nil {
		tx.Rollback() // 回滚
		fmt.Printf("exec ret1.RowsAffected() failed, err:%v\n", err)
		return
	}

	sqlStr2 := "Update user set age=40 where id=?"
	ret2, err := tx.Exec(sqlStr2, 3)
	if err != nil {
		tx.Rollback() // 回滚
		fmt.Printf("exec sql2 failed, err:%v\n", err)
		return
	}
	affRow2, err := ret2.RowsAffected()
	if err != nil {
		tx.Rollback() // 回滚
		fmt.Printf("exec ret1.RowsAffected() failed, err:%v\n", err)
		return
	}

	fmt.Println(affRow1, affRow2)
	if affRow1 == 1 && affRow2 == 1 {
		fmt.Println("事务提交啦...")
		tx.Commit() // 提交事务
	} else {
		tx.Rollback()
		fmt.Println("事务回滚啦...")
	}

	fmt.Println("exec trans success!")
}
```



# 第十五章、第三方库

## 1. sync/atomic库

​	原子操作，线程安全，比互斥锁性能高

## 2.性能测试

耗时性能测试基本格式:

```go
func BenchmarkName(b *testing.B){
    // ...
}
```

基准测试并不会默认执行，需要增加`-bench`参数，所以我们通过执行`go test -bench=Split`命令执行基准测试

## 3.性能优化

### CPU性能分析

开启CPU性能分析：

```go
pprof.StartCPUProfile(w io.Writer)
```

停止CPU性能分析：

```go
pprof.StopCPUProfile()
```

应用执行结束后，就会生成一个文件，保存了我们的 CPU profiling 数据。得到采样数据之后，使用`go tool pprof`工具进行CPU性能分析。

### 内存性能优化

记录程序的堆栈信息

```go
pprof.WriteHeapProfile(w io.Writer)
```

得到采样数据之后，使用`go tool pprof`工具进行内存性能分析。

`go tool pprof`默认是使用`-inuse_space`进行统计，还可以使用`-inuse-objects`查看分配对象的数量。

# 第十六章、go-http编程

## 1. http_sever

**调用net/http包**

```go
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
)

func f1(w http.ResponseWriter,r *http.Request){
	str,err:=ioutil.ReadFile("./web.html")
	if err!=nil{
		w.Write([]byte(fmt.Sprintf("%v",err)))
	}
	w.Write(str)
}

func main(){
	http.HandleFunc("/posts/Go/",f1)
	http.ListenAndServe("127.0.0.1:9090",nil)
}
```

## 2. http_client

### get请求示例

```go
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
)

func main()  {
	resp,err:=http.Get("http://127.0.0.1:9090/Go/?name=sb&age=18")
	if err!=nil{
		fmt.Println("Get err:",err)
		return
	}
	defer resp.Body.Close()
	//把resp中服务端返回的数据读出来
	b,err:=ioutil.ReadAll(resp.Body)//读出服务端响应的内容
	if err!=nil{
		fmt.Printf("read resp.body ,err %v\n",err)
		return
	}
	fmt.Println(string(b))
}
```

2.post请求示例

### Post请求示例

发送`POST`请求的示例代码如下：

```go
package main

import (
	"fmt"
	"io/ioutil"
	"net/http"
	"strings"
)

// net/http post demo

func main() {
	url := "http://127.0.0.1:9090/post"
	// 表单数据
	//contentType := "application/x-www-form-urlencoded"
	//data := "name=小王子&age=18"
	// json
	contentType := "application/json"
	data := `{"name":"小王子","age":18}`
	resp, err := http.Post(url, contentType, strings.NewReader(data))
	if err != nil {
		fmt.Printf("post failed, err:%v\n", err)
		return
	}
	defer resp.Body.Close()
	b, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		fmt.Printf("get resp failed, err:%v\n", err)
		return
	}
	fmt.Println(string(b))
}
```

对应的Server端HandlerFunc如下：

```go
func postHandler(w http.ResponseWriter, r *http.Request) {
	defer r.Body.Close()
	// 1. 请求类型是application/x-www-form-urlencoded时解析form数据
	r.ParseForm()
	fmt.Println(r.PostForm) // 打印form数据
	fmt.Println(r.PostForm.Get("name"), r.PostForm.Get("age"))
	// 2. 请求类型是application/json时从r.Body读取数据
	b, err := ioutil.ReadAll(r.Body)
	if err != nil {
		fmt.Printf("read request.Body failed, err:%v\n", err)
		return
	}
	fmt.Println(string(b))
	answer := `{"status": "ok"}`
	w.Write([]byte(answer))
}
```

# 第十七章、消息队列

**同步变异步**

**进程间通信**

## 1.NSQ

nsqd是一个守护进程，它接收、排队并向客户端发送消息。

启动`nsqd`，指定`-broadcast-address=127.0.0.1`来配置广播地址

```go
./nsqd -broadcast-address=127.0.0.1
```

如果是在搭配`nsqlookupd`使用的模式下需要还指定`nsqlookupd`地址:

```bash
./nsqd -broadcast-address=127.0.0.1 -lookupd-tcp-address=127.0.0.1:4160
```

如果是部署了多个`nsqlookupd`节点的集群，那还可以指定多个`-lookupd-tcp-address`

### 1.1 go操作NSQ

#### 安装

```bash
go get -u github.com/nsqio/go-nsq
```

#### 生产者

一个简单的生产者示例代码如下：

```go
// nsq_producer/main.go
package main

import (
	"bufio"
	"fmt"
	"os"
	"strings"

	"github.com/nsqio/go-nsq"
)

// NSQ Producer Demo

var producer *nsq.Producer

// 初始化生产者
func initProducer(str string) (err error) {
	config := nsq.NewConfig()
	producer, err = nsq.NewProducer(str, config)
	if err != nil {
		fmt.Printf("create producer failed, err:%v\n", err)
		return err
	}
	return nil
}

func main() {
	nsqAddress := "127.0.0.1:4150"
	err := initProducer(nsqAddress)
	if err != nil {
		fmt.Printf("init producer failed, err:%v\n", err)
		return
	}

	reader := bufio.NewReader(os.Stdin) // 从标准输入读取
	for {
		data, err := reader.ReadString('\n')
		if err != nil {
			fmt.Printf("read string from stdin failed, err:%v\n", err)
			continue
		}
		data = strings.TrimSpace(data)
		if strings.ToUpper(data) == "Q" { // 输入Q退出
			break
		}
		// 向 'topic_demo' publish 数据
		err = producer.Publish("topic_demo", []byte(data))
		if err != nil {
			fmt.Printf("publish msg to nsq failed, err:%v\n", err)
			continue
		}
	}
}
```

#### 消费者

一个简单的消费者示例代码如下：

```go
// nsq_consumer/main.go
package main

import (
	"fmt"
	"os"
	"os/signal"
	"syscall"
	"time"

	"github.com/nsqio/go-nsq"
)

// NSQ Consumer Demo

// MyHandler 是一个消费者类型
type MyHandler struct {
	Title string
}

// HandleMessage 是需要实现的处理消息的方法
func (m *MyHandler) HandleMessage(msg *nsq.Message) (err error) {
	fmt.Printf("%s recv from %v, msg:%v\n", m.Title, msg.NSQDAddress, string(msg.Body))
	return
}

// 初始化消费者
func initConsumer(topic string, channel string, address string) (err error) {
	config := nsq.NewConfig()
	config.LookupdPollInterval = 15 * time.Second
	c, err := nsq.NewConsumer(topic, channel, config)
	if err != nil {
		fmt.Printf("create consumer failed, err:%v\n", err)
		return
	}
	consumer := &MyHandler{
		Title: "沙河1号",
	}
	c.AddHandler(consumer)

	// if err := c.ConnectToNSQD(address); err != nil { // 直接连NSQD
	if err := c.ConnectToNSQLookupd(address); err != nil { // 通过lookupd查询
		return err
	}
	return nil

}

func main() {
	err := initConsumer("topic_demo", "first", "127.0.0.1:4161")
	if err != nil {
		fmt.Printf("init consumer failed, err:%v\n", err)
		return
	}
	c := make(chan os.Signal)        // 定义一个信号的通道
	signal.Notify(c, syscall.SIGINT) // 转发键盘中断信号到c
	<-c                              // 阻塞
}
```

**点对点模式、发布/订阅（topic）模式、kafka**



# 第十八章、依赖管理go mode

`go module`是Go1.11版本之后官方推出的版本管理工具，并且从Go1.13版本开始，`go module`将是Go语言默认的依赖管理工具。

### GO1.11MODULE

要启用`go module`支持首先要设置环境变量`GO111MODULE`，通过它可以开启或关闭模块支持，它有三个可选值：`off`、`on`、`auto`，默认值是`auto`。

1. `GO111MODULE=off`禁用模块支持，编译时会从`GOPATH`和`vendor`文件夹中查找包。
2. `GO111MODULE=on`启用模块支持，编译时会忽略`GOPATH`和`vendor`文件夹，只根据 `go.mod`下载依赖。
3. `GO111MODULE=auto`，当项目在`$GOPATH/src`外且项目根目录有`go.mod`文件时，开启模块支持。

简单来说，设置`GO111MODULE=on`之后就可以使用`go module`了，以后就没有必要在GOPATH中创建项目了，并且还能够很好的管理项目依赖的第三方包信息。

使用 go module 管理依赖后会在项目根目录下生成两个文件`go.mod`和`go.sum`。

### GOPROXY

Go1.11之后设置GOPROXY命令为：

```bash
set GOPROXY=https://goproxy.cn
```

Go1.13之后`GOPROXY`默认值为`https://proxy.golang.org`，在国内是无法访问的，所以十分建议大家设置GOPROXY，这里我推荐使用[goproxy.cn](https://studygolang.com/topics/10014)。

```bash
go env -w GOPROXY=https://goproxy.cn,direct
```

### go mod命令

常用的`go mod`命令如下：

```go
go mod download    下载依赖的module到本地cache（默认为$GOPATH/pkg/mod目录）
go mod edit        编辑go.mod文件
go mod graph       打印模块依赖图
go mod init        初始化当前文件夹, 创建go.mod文件
go mod tidy        增加缺少的module，删除无用的module
go mod vendor      将依赖复制到vendor下
go mod verify      校验依赖
go mod why         解释为什么需要依赖
```

### go.mod

go.mod文件记录了项目所有的依赖信息，其结构大致如下：

```sh
module github.com/Q1mi/studygo/blogger

go 1.12

require (
	github.com/DeanThompson/ginpprof v0.0.0-20190408063150-3be636683586
	github.com/gin-gonic/gin v1.4.0
	github.com/go-sql-driver/mysql v1.4.1
	github.com/jmoiron/sqlx v1.2.0
	github.com/satori/go.uuid v1.2.0
	google.golang.org/appengine v1.6.1 // indirect
)
```

其中，

- `module`用来定义包名
- `require`用来定义依赖包及版本
- `indirect`表示间接引用

#### 依赖的版本

go mod支持语义化版本号，比如`go get foo@v1.2.3`，也可以跟git的分支或tag，比如`go get foo@master`，当然也可以跟git提交哈希，比如`go get foo@e3702bed2`。关于依赖的版本支持以下几种格式：

```go
gopkg.in/tomb.v1 v1.0.0-20141024135613-dd632973f1e7
gopkg.in/vmihailenco/msgpack.v2 v2.9.1
gopkg.in/yaml.v2 <=v2.2.1
github.com/tatsushid/go-fastping v0.0.0-20160109021039-d7bb493dee3e
latest
```

#### replace

在国内访问golang.org/x的各个包都需要翻墙，你可以在go.mod中使用replace替换成github上对应的库。

```go
replace (
	golang.org/x/crypto v0.0.0-20180820150726-614d502a4dac => github.com/golang/crypto v0.0.0-20180820150726-614d502a4dac
	golang.org/x/net v0.0.0-20180821023952-922f4815f713 => github.com/golang/net v0.0.0-20180826012351-8a410e7b638d
	golang.org/x/text v0.3.0 => github.com/golang/text v0.3.0
)
```

### go get

在项目中执行`go get`命令可以下载依赖包，并且还可以指定下载的版本。

1. 运行`go get -u`将会升级到最新的次要版本或者修订版本(x.y.z, z是修订版本号， y是次要版本号)
2. 运行`go get -u=patch`将会升级到最新的修订版本
3. 运行`go get package@version`将会升级到指定的版本号version

如果下载所有依赖可以使用`go mod download`命令。

### 整理依赖

我们在代码中删除依赖代码后，相关的依赖库并不会在`go.mod`文件中自动移除。这种情况下我们可以使用`go mod tidy`命令更新`go.mod`中的依赖关系。

### go mod edit

#### 格式化

因为我们可以手动修改go.mod文件，所以有些时候需要格式化该文件。Go提供了一下命令：

```bash
go mod edit -fmt
```

#### 添加依赖项

```bash
go mod edit -require=golang.org/x/text
```

#### 移除依赖项

如果只是想修改`go.mod`文件中的内容，那么可以运行`go mod edit -droprequire=package path`，比如要在`go.mod`中移除`golang.org/x/text`包，可以使用如下命令：

```bash
go mod edit -droprequire=golang.org/x/text
```

关于`go mod edit`的更多用法可以通过`go help mod edit`查看。

# 第十九章、contex--goroutine管理

## Context初识

​	Go1.7加入了一个新的标准库`context`，它定义了`Context`类型，专门用来简化 对于处理单个请求的多个 goroutine 之间与请求域的数据、取消信号、截止时间等相关操作，这些操作可能涉及多个 API 调用。

​	对服务器传入的请求应该创建上下文，而对服务器的传出调用应该接受上下文。它们之间的函数调用链必须传递上下文，或者可以使用`WithCancel`、`WithDeadline`、`WithTimeout`或`WithValue`创建的派生上下文。当一个上下文被取消时，它派生的所有上下文也被取消。

## Context接口

`context.Context`是一个接口，该接口定义了四个需要实现的方法。具体签名如下：

```go
type Context interface {
    Deadline() (deadline time.Time, ok bool)
    Done() <-chan struct{}
    Err() error
    Value(key interface{}) interface{}
}
```

其中：

- `Deadline`方法需要返回当前`Context`被取消的时间，也就是完成工作的截止时间（deadline）；

- `Done`方法需要返回一个`Channel`，这个Channel会在当前工作完成或者上下文被取消之后关闭，多次调用`Done`方法会返回同一个Channel；

- ```
  Err
  ```

  方法会返回当前

  ```
  Context
  ```

  结束的原因，它只会在

  ```
  Done
  ```

  返回的Channel被关闭时才会返回非空的值；

  - 如果当前`Context`被取消就会返回`Canceled`错误；
  - 如果当前`Context`超时就会返回`DeadlineExceeded`错误；

- `Value`方法会从`Context`中返回键对应的值，对于同一个上下文来说，多次调用`Value` 并传入相同的`Key`会返回相同的结果，该方法仅用于传递跨API和进程间跟请求域的数据；

## Contex示例

```go
package main

import (
	"context"
	"fmt"
	"sync"

	"time"
)

var wg sync.WaitGroup

func worker(ctx context.Context) {
	go worker2(ctx)
LOOP:
	for {
		fmt.Println("worker")
		time.Sleep(time.Second)
		select {
		case <-ctx.Done(): // 等待上级通知
			break LOOP
		default:
		}
	}
	wg.Done()
}

func worker2(ctx context.Context) {
LOOP:
	for {
		fmt.Println("worker2")
		time.Sleep(time.Second)
		select {
		case <-ctx.Done(): // 等待上级通知
			break LOOP
		default:
		}
	}
}
func main() {
	ctx, cancel := context.WithCancel(context.Background())
	wg.Add(1)
	go worker(ctx)
	time.Sleep(time.Second * 3)
	cancel() // 通知子goroutine结束
	wg.Wait()
	fmt.Println("over")
}
```

### Background()和TODO()

Go内置两个函数：`Background()`和`TODO()`，这两个函数分别返回一个实现了`Context`接口的`background`和`todo`。我们代码中最开始都是以这两个内置的上下文对象作为最顶层的`partent context`，衍生出更多的子上下文对象。

`Background()`主要用于main函数、初始化以及测试代码中，作为Context这个树结构的最顶层的Context，也就是根Context。

`TODO()`，它目前还不知道具体的使用场景，如果我们不知道该使用什么Context的时候，可以使用这个。

`background`和`todo`本质上都是`emptyCtx`结构体类型，是一个不可取消，没有设置截止时间，没有携带任何值的Context。

## With系列函数

此外，`context`包中还定义了四个With系列函数。

### WithCancel

`WithCancel`的函数签名如下：

```go
func WithCancel(parent Context) (ctx Context, cancel CancelFunc)
```

`WithCancel`返回带有新Done通道的父节点的副本。当调用返回的cancel函数或当关闭父上下文的Done通道时，将关闭返回上下文的Done通道，无论先发生什么情况。

取消此上下文将释放与其关联的资源，因此代码应该在此上下文中运行的操作完成后立即调用cancel。

```go
func gen(ctx context.Context) <-chan int {
		dst := make(chan int)
		n := 1
		go func() {
			for {
				select {
				case <-ctx.Done():
					return // return结束该goroutine，防止泄露
				case dst <- n:
					n++
				}
			}
		}()
		return dst
	}
func main() {
	ctx, cancel := context.WithCancel(context.Background())
	defer cancel() // 当我们取完需要的整数后调用cancel

	for n := range gen(ctx) {
		fmt.Println(n)
		if n == 5 {
			break
		}
	}
}
```

上面的示例代码中，`gen`函数在单独的goroutine中生成整数并将它们发送到返回的通道。 gen的调用者在使用生成的整数之后需要取消上下文，以免`gen`启动的内部goroutine发生泄漏。

### WithDeadline

`WithDeadline`的函数签名如下：

```go
func WithDeadline(parent Context, deadline time.Time) (Context, CancelFunc)
```

返回父上下文的副本，并将deadline调整为不迟于d。如果父上下文的deadline已经早于d，则WithDeadline(parent, d)在语义上等同于父上下文。当截止日过期时，当调用返回的cancel函数时，或者当父上下文的Done通道关闭时，返回上下文的Done通道将被关闭，以最先发生的情况为准。

取消此上下文将释放与其关联的资源，因此代码应该在此上下文中运行的操作完成后立即调用cancel。

```go
func main() {
	d := time.Now().Add(50 * time.Millisecond)
	ctx, cancel := context.WithDeadline(context.Background(), d)

	// 尽管ctx会过期，但在任何情况下调用它的cancel函数都是很好的实践。
	// 如果不这样做，可能会使上下文及其父类存活的时间超过必要的时间。
	defer cancel()

	select {
	case <-time.After(1 * time.Second):
		fmt.Println("overslept")
	case <-ctx.Done():
		fmt.Println(ctx.Err())
	}
}
```

上面的代码中，定义了一个50毫秒之后过期的deadline，然后我们调用`context.WithDeadline(context.Background(), d)`得到一个上下文（ctx）和一个取消函数（cancel），然后使用一个select让主程序陷入等待：等待1秒后打印`overslept`退出或者等待ctx过期后退出。

在上面的示例代码中，因为ctx 50毫秒后就会过期，所以`ctx.Done()`会先接收到context到期通知，并且会打印ctx.Err()的内容。

### WithTimeout

`WithTimeout`的函数签名如下：

```go
func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)
```

`WithTimeout`返回`WithDeadline(parent, time.Now().Add(timeout))`。

取消此上下文将释放与其相关的资源，因此代码应该在此上下文中运行的操作完成后立即调用cancel，通常用于数据库或者网络连接的超时控制。具体示例如下：

```go
package main

import (
	"context"
	"fmt"
	"sync"

	"time"
)

// context.WithTimeout

var wg sync.WaitGroup

func worker(ctx context.Context) {
LOOP:
	for {
		fmt.Println("db connecting ...")
		time.Sleep(time.Millisecond * 10) // 假设正常连接数据库耗时10毫秒
		select {
		case <-ctx.Done(): // 50毫秒后自动调用
			break LOOP
		default:
		}
	}
	fmt.Println("worker done!")
	wg.Done()
}

func main() {
	// 设置一个50毫秒的超时
	ctx, cancel := context.WithTimeout(context.Background(), time.Millisecond*50)
	wg.Add(1)
	go worker(ctx)
	time.Sleep(time.Second * 5)
	cancel() // 通知子goroutine结束
	wg.Wait()
	fmt.Println("over")
}
```

### WithValue

`WithValue`函数能够将请求作用域的数据与 Context 对象建立关系。声明如下：

```go
func WithValue(parent Context, key, val interface{}) Context
```

`WithValue`返回父节点的副本，其中与key关联的值为val。

仅对API和进程间传递请求域的数据使用上下文值，而不是使用它来传递可选参数给函数。

所提供的键必须是可比较的，并且不应该是`string`类型或任何其他内置类型，以避免使用上下文在包之间发生冲突。`WithValue`的用户应该为键定义自己的类型。为了避免在分配给interface{}时进行分配，上下文键通常具有具体类型`struct{}`。或者，导出的上下文关键变量的静态类型应该是指针或接口。

```go
package main

import (
	"context"
	"fmt"
	"sync"

	"time"
)

// context.WithValue

type TraceCode string

var wg sync.WaitGroup

func worker(ctx context.Context) {
	key := TraceCode("TRACE_CODE")
	traceCode, ok := ctx.Value(key).(string) // 在子goroutine中获取trace code
	if !ok {
		fmt.Println("invalid trace code")
	}
LOOP:
	for {
		fmt.Printf("worker, trace code:%s\n", traceCode)
		time.Sleep(time.Millisecond * 10) // 假设正常连接数据库耗时10毫秒
		select {
		case <-ctx.Done(): // 50毫秒后自动调用
			break LOOP
		default:
		}
	}
	fmt.Println("worker done!")
	wg.Done()
}

func main() {
	// 设置一个50毫秒的超时
	ctx, cancel := context.WithTimeout(context.Background(), time.Millisecond*50)
	// 在系统的入口中设置trace code传递给后续启动的goroutine实现日志数据聚合
	ctx = context.WithValue(ctx, TraceCode("TRACE_CODE"), "12512312234")
	wg.Add(1)
	go worker(ctx)
	time.Sleep(time.Second * 5)
	cancel() // 通知子goroutine结束
	wg.Wait()
	fmt.Println("over")
}
```

## 使用Context的注意事项

- 推荐以参数的方式显示传递Context
- 以Context作为参数的函数方法，应该把Context作为第一个参数。
- 给一个函数方法传递Context的时候，不要传递nil，如果不知道传递什么，就使用context.TODO()
- Context的Value相关方法应该传递请求域的必要数据，不应该用于传递可选参数
- Context是线程安全的，可以放心的在多个goroutine中传递

# 第二十章、日志收集项目

**项目架构**

![image-20211103155719950](E:\typroa_pic\image-20211103155719950.png)



为什么要自己写，不用ELK？

ELK：部署的时候麻烦，每一个filebeat都需要一个配置文件

**使用etcd来管理被收集的日志项**

## 1.`kafka`

### 1.`kafka`集群的架构

![image-20211104110121570](E:\typroa_pic\image-20211104110121570.png)

**1.`broker `：**

​		指部署了Kafka实例的服务器节点，每个服务器上有一个或多个kafka的实例，姑且认为每个broker对应一台服务器。每个kafka集群内的broker都有一个**不重复**的编号。

**2.`topic`：**

​		消息 的主题，可以理解为消息的分类，kafka的数据就保存在topic。在每个broker上都可以创建多个topic。实际应用中通常是一个业务线建一个topic。

**3.`partition`：**

​		分区，把同一个`topic`分成不同的分区，每个topic可以有多个分区，分区的作用是负载，提高`kafka`的吞吐量。同一个`topic`在不同分区的数据十不重复的，`partition`的表现形式就是一个一个的文件夹。

​	**`leader`：**分区的主节点

​	**`follower`：**分区的从节点，**从`leader`中拉取数据信息**

### 2.生产者往Kafka发送数据的流程

![image-20211102155600376](E:\typroa_pic\image-20211102155600376.png)

1.生产者从`Kafka`集群获取分区`leader`信息

2.生产者将消息发送给`leader`

3.`leader`将消息写入本地磁盘

4.`follower`从`leader`拉去消息数据

5.`follower`将消息写入本地磁盘后向`leader`发送ACK

6.`leader`收到所有的`follower`的ACK之后向生产者发送ACK

### 3. ACK应答机制：0、1、all

生产者往kafka发送数据的模式

0：表示生产者往集群发送数据不需要等到集群的返回，不确保消息发送成功。安全性最低但是效率最高。

1：表示生产者往集群发送数据只要leader应答就可以发送下一条，只确保`leader`发送成功

all：表示生产者往集群发送数据需要所有的`follower`都完成从leader的同步才会发送下一条，确保`leader`发送成功和所有的副本都完成备份。安全性最高，但是效率最低。

### 4. 选择partition的原则

那在kafka中，如果某个`topic`有多个`partition`，`producer`又怎么知道该将数据发往哪个`partition`呢?`kafka`中有几个原则:

1. `partition`在写入的时候可以指定需要写入的`partition`，如果有指定，则写入对应的`partition`。

2. 如果没有指定`partition`，但是设置了数据的key，则会根据`key`的值hash出一个`partition`。

3. 如果既没指定`partition`，又没有设置`key`，则会采用轮询方式，即每次取一小段时间的数据写入某个`partition`，下一小段的时间写入下一个`partition`。

### 5.分区存储文件的原理

![image-20211103144817785](E:\typroa_pic\image-20211103144817785.png)

​			每个`partition`都是一个有序并且不可变的消息记录集合。当新的数据写入时，就被追加到`partition`的未尾。在每个`partition`中，每条消息都会被分配一个顺序的唯一标识，这个标识被称为`offset`，即偏移量。注意，`Kafka`只保证在同一个`partition`内部消息是有序的，在不同`partition`之间，并不能保证消息有序。

**因为加入了offset标识，所以可以顺序读，阔以kafka速度快**



### 6.消费者消费数据的原理

![image-20211103144523596](E:\typroa_pic\image-20211103144523596.png)

```go
package main

import (
	"fmt"
	"github.com/Shopify/sarama"
)

func main(){
	consumer,err:=sarama.NewConsumer([]string{"127.0.0.1:9092"},nil)
	if err!=nil{
		fmt.Printf("fail to start consumer,err:%v\n",err)
		return
	}
	partitionLisy,err:=consumer.Partitions("redis_log")	//  根据topic取到所有的分区
	if err!=nil{
		fmt.Printf("fail to get list of partition,err:%v\n",err)
		return
	}
	fmt.Println("分区列表：",partitionLisy)
	for partion:=range partitionLisy{	//遍历所有分区
		// 针对每个分区创建一个对应分区的消费者
		pc,err:=consumer.ConsumePartition("redis_log",int32(partion),sarama.OffsetNewest)
		if err!=nil{
			fmt.Printf("fail to start consumer for partion %d,err:%v\n",partion,err)
			return
		}
		defer pc.AsyncClose()

		// 异步从每个分区消费信息
		go func(sarama.PartitionConsumer){
			for msg:=range pc.Messages(){
				fmt.Printf("Partition:%d  Offset:%d  Key:%v  Value:%s\n",msg.Partition,msg.Offset,msg.Key,msg.Value)
			}
		}(pc)
	}
	select {

	}
}
```



## 2.LogAgent的工作流程

打开kafka和zookeeper------管理员权限运行

```bash
bin\windows\zookeeper-server-start.bat config\zookeeper.properties
```

```bash
bin\windows\kafka-server-start.bat config\server.properties
```



### 1.读日志 ---`tailf`第三方库

```go
package main

import (
	"fmt"
	"github.com/hpcloud/tail"
	"time"
)

func main()  {
	fileName:="E:/Goproject/src/go_Code/logagent/main/mylog.txt"
	config:=tail.Config{
		ReOpen: true,		//重新打开
		Follow: true,		//是否跟随
		Location: &tail.SeekInfo{Offset: 0,Whence: 2},		//从文件的哪个地方开始读
		MustExist: false,		//文件不存在不报错
		Poll: true,
	}
	tails,err:=tail.TailFile(fileName,config)
	if err!=nil{
		fmt.Println("tail file failed,err: ",err)
		return
	}
	var(
		line *tail.Line
		ok bool
	)
	for {
		line,ok= <-tails.Lines
		if !ok{
			fmt.Printf("tail file close reopen,filename:%s\n",tails.Filename)
			time.Sleep(time.Second)
			continue
		}
		fmt.Printf("line:%s\n",line.Text)
	}
}
```

### 2.往kafka写日志 ---`sarama`第三方库

```go
package main

import (
	"fmt"
	"github.com/Shopify/sarama"
)

func main() {
	config:=sarama.NewConfig()

	config.Producer.RequiredAcks=sarama.WaitForAll  //第三种ACK应答机制，发送完数据等leader和follower确认
	config.Producer.Partitioner=sarama.NewRandomPartitioner //随机新选择一个分区
	config.Producer.Return.Successes=true	//成功交付的消息将在success_channel返回

	//构造一个消息
	msg:=&sarama.ProducerMessage{}
	msg.Topic="web_log"
	msg.Value=sarama.StringEncoder("this is a test log")

	//连接kafka
	client,err:=sarama.NewSyncProducer([]string{"127.0.0.1:9092"},config)
	if err!=nil{
		fmt.Println("producer closed, err:",err)
		return
	}
	fmt.Println("连接Kafka成功")
	defer client.Close()
	pid,offset,err:=client.SendMessage(msg)
	if err!=nil{
		fmt.Println("send msg faild, err:",err)
		return
	}
	fmt.Printf("pid:%v , offset: %v",pid,offset)
	fmt.Println("发送成功")
}
```



### 3.kafka终端读取数据

```bash
bin\windows\kafka-console-consumer.bat --bootstrap-server=127.0.0.1:9092 --topic=game_log --from-beginning
```

### 4.配置文件ini

**gopkg.in.ini.v1**

**1.创建ini文件**

```ini
[kafka]
address=127.0.0.1:9092
topic=game_log

[taillog]
filename=./mylog.txt
```

**2.创建结构体映射**

```go
package conf

type AppConf struct {
	KafkaConf `ini:"kafka"`  //名称和ini文件对应
	TaillogConf`ini:"taillog"`
}


type KafkaConf struct {
	Address string`ini:"address"`
	Topic string`ini:"topic"`
}


type TaillogConf struct {
	FileName string`ini:"filename"`
}

```

**3.main函数创建结构体指针并完成映射**

```go
var(
	cfg =new(conf.AppConf)
)//创建全局结构体指针

err:=ini.MapTo(cfg,"./conf/config.ini")

cfg.KafkaConf.Address
cfg.TaillogConf.FileName
```

## 3.etcd

![image-20211104163257739](E:\typroa_pic\image-20211104163257739.png)

​		从etcd的架构图中我们可以看到，etcd主要分为四个部分。

`HTTP Server`:用于处理用户发送的API请求以及其它etcd节点的同步与心跳信息请求。

`Store`:用于处理etcd支持的各类功能的事务，包括数据索引、节点状态变更、监控与反馈、事件处理与执行等等，是etcd对用户提供的大多数API功能的具体实现。

`Raft`: Raft强一致性算法的具体实现，是etcd 的核心。

`WAL: Write Ahead Log` (预写式日志)，是etcd 的数据存储方式。除了在内存中存有所有数据的状态以及节点的索引以外，etcd就通过WAL进行持久化存储。WAL中，所有的数据提交前都会事先记录日志。Snapshot是为了防止数据过多而进行的状态快照;Entry表示存储的具体日志内容。

​		[etcd](https://etcd.io/)是使用Go语言开发的一个开源的、高可用的分布式key-value存储系统，可以用于配置共享和服务的注册和发现。

类似项目有zookeeper和consul。

etcd具有以下特点：

- 完全复制：集群中的每个节点都可以使用完整的存档
- 高可用性：Etcd可用于避免硬件的单点故障或网络问题
- 一致性：每次读取都会返回跨多主机的最新写入
- 简单：包括一个定义良好、面向用户的API（gRPC）
- 安全：实现了带有可选的客户端证书身份验证的自动化TLS
- 快速：每秒10000次写入的基准速度
- 可靠：使用Raft算法实现了强一致、高可用的服务存储目录



### 服务发现

服务发现要解决的也是分布式系统中最常见的问题之一，即在同一个分布式集群中的进程或服务，要如何才能找到对方并建立连接。本质上来说，服务发现就是想要了解集群中是否有进程在监听 udp 或 tcp 端口，并且通过名字就可以查找和连接。

![img](E:\typroa_pic\etcd_01.png)

### 配置中心

将一些配置信息放到 etcd 上进行集中管理。

这类场景的使用方式通常是这样：应用在启动的时候主动从 etcd 获取一次配置信息，同时，在 etcd 节点上注册一个 Watcher 并等待，以后每次配置有更新的时候，etcd 都会实时通知订阅者，以此达到获取最新配置信息的目的。

### 分布式锁

​		因为 etcd 使用 Raft 算法保持了数据的强一致性，某次操作存储到集群中的值必然是全局一致的，所以很容易实现分布式锁。锁服务有两种使用方式，一是保持独占，二是控制时序。

- **保持独占即所有获取锁的用户最终只有一个可以得到**。etcd 为此提供了一套实现分布式锁原子操作 CAS（`CompareAndSwap`）的 API。通过设置`prevExist`值，可以保证在多个节点同时去创建某个目录时，只有一个成功。而创建成功的用户就可以认为是获得了锁。
- 控制时序，即所有想要获得锁的用户都会被安排执行，但是**获得锁的顺序也是全局唯一的，同时决定了执行顺序**。etcd 为此也提供了一套 API（自动创建有序键），对一个目录建值时指定为`POST`动作，这样 etcd 会自动在目录下生成一个当前最大的值为键，存储这个新的值（客户端编号）。同时还可以使用 API 按顺序列出所有当前目录下的键值。此时这些键的值就是客户端的时序，而这些键中存储的值可以是代表客户端的编号。

![img](E:\typroa_pic\etcd_02.png)

### 为什么不选择ZooKeeper？

1. 部署维护复杂，其使用的`Paxos`强一致性算法复杂难懂。官方只提供了`Java`和`C`两种语言的接口。
2. 使用`Java`编写引入大量的依赖。运维人员维护起来比较麻烦。
3. 最近几年发展缓慢，不如`etcd`和`consul`等后起之秀。

### 为什么选择etcd？

1. 简单。使用 Go 语言编写部署简单；支持HTTP/JSON API,使用简单；使用 Raft 算法保证强一致性让用户易于理解。
2. etcd 默认数据一更新就进行持久化。
3. etcd 支持 SSL 客户端安全认证。

最后，etcd 作为一个年轻的项目，正在高速迭代和开发中，这既是一个优点，也是一个缺点。优点是它的未来具有无限的可能性，缺点是无法得到大项目长时间使用的检验。然而，目前 `CoreOS`、`Kubernetes`和`CloudFoundry`等知名项目均在生产环境中使用了`etcd`，所以总的来说，etcd值得你去尝试。

### Go语言操作etcd

这里使用官方的[etcd/clientv3](https://github.com/etcd-io/etcd/tree/master/client/v3)包来连接etcd并进行相关操作。

### 安装

```bash
go get go.etcd.io/etcd/clientv3
```

### put和get操作

`put`命令用来设置键值对数据，`get`命令用来根据key获取值。

```go
package main

import (
	"context"
	"fmt"
	"go.etcd.io/etcd/clientv3"
	"time"
)

// etcd client put/get demo
// use etcd/clientv3

func main() {
	cli, err := clientv3.New(clientv3.Config{
		Endpoints:   []string{"127.0.0.1:2379"},
		DialTimeout: 5 * time.Second,
	})
	if err != nil {
		// handle error!
		fmt.Printf("connect to etcd failed, err:%v\n", err)
		return
	}
	fmt.Println("connect to etcd success")
	defer cli.Close()
	// put
	fmt.Println("准备put")
	ctx, cancel := context.WithTimeout(context.Background(), time.Second)
	_, err = cli.Put(ctx, "q1mi", "dsb")
	cancel()
	if err != nil {
		fmt.Printf("put to etcd failed, err:%v\n", err)
		return
	}

	// get
	fmt.Println("准备get")
	ctx, cancel = context.WithTimeout(context.Background(), time.Second)
	resp, err := cli.Get(ctx, "wangye")
	cancel()

	if err != nil {
		fmt.Printf("get from etcd failed, err:%v\n", err)
		return
	}
	for _, ev := range resp.Kvs {
		fmt.Printf("%s:%s\n", ev.Key, ev.Value)
	}
}
```

### watch操作

`watch`用来获取未来更改的通知。

```go
package main

import (
	"context"
	"fmt"
	"go.etcd.io/etcd/clientv3"
	"time"
)

// etcd client put/get demo
// use etcd/clientv3

func main() {
	cli, err := clientv3.New(clientv3.Config{
		Endpoints:   []string{"127.0.0.1:2379"},
		DialTimeout: 5 * time.Second,
	})
	if err != nil {
		fmt.Printf("connect to etcd failed, err:%v\n", err)
		return
	}
	fmt.Println("connect to etcd success")
	defer cli.Close()
	// watch key:wangye change

	rch := cli.Watch(context.Background(),"wangye") // <-chan WatchResponse

	for wresp := range rch {
		for _, ev := range wresp.Events {
			fmt.Printf("Type: %s Key:%s Value:%s\n", ev.Type, ev.Kv.Key, ev.Kv.Value)
		}
	}
}
```



### 基于etcd实现分布式锁

`go.etcd.io/etcd/clientv3/concurrency`在etcd之上实现并发操作，如分布式锁、屏障和选举。

导入该包：

```go
import "go.etcd.io/etcd/clientv3/concurrency"
```

基于etcd实现的分布式锁示例：

```go
cli, err := clientv3.New(clientv3.Config{Endpoints: endpoints})
if err != nil {
    log.Fatal(err)
}
defer cli.Close()

// 创建两个单独的会话用来演示锁竞争
s1, err := concurrency.NewSession(cli)
if err != nil {
    log.Fatal(err)
}
defer s1.Close()
m1 := concurrency.NewMutex(s1, "/my-lock/")

s2, err := concurrency.NewSession(cli)
if err != nil {
    log.Fatal(err)
}
defer s2.Close()
m2 := concurrency.NewMutex(s2, "/my-lock/")

// 会话s1获取锁
if err := m1.Lock(context.TODO()); err != nil {
    log.Fatal(err)
}
fmt.Println("acquired lock for s1")

m2Locked := make(chan struct{})
go func() {
    defer close(m2Locked)
    // 等待直到会话s1释放了/my-lock/的锁
    if err := m2.Lock(context.TODO()); err != nil {
        log.Fatal(err)
    }
}()

if err := m1.Unlock(context.TODO()); err != nil {
    log.Fatal(err)
}
fmt.Println("released lock for s1")

<-m2Locked
fmt.Println("acquired lock for s2")
```

输出：

```bash
acquired lock for s1
released lock for s1
acquired lock for s2
```

### 其他操作

其他操作请查看[etcd/clientv3官方文档](https://github.com/etcd-io/etcd/tree/master/client/v3)。



## 4.要弄明白的点

1.Raft协议

​	1.选举

​	2.日志复制机制

​	3.异常处理（脑裂）

​	4.zookeeper的zad协议的区别

2.etcd的watch

​	1.底层如何实现给watch给客户发通知（websocket）



## 5.LogTransfer

从kafka里面把日志取出来，写入ES，使用Kibana做可视化的展示

### 5.1 Elastic search--ES 搜索引擎

### go操作es

```go
package main

import (
	"context"
	"fmt"
	"github.com/olivere/elastic/v7"
)

type student struct {
	Name string `json:"name"`
	Age int `json:"age"`
	Married bool `json:"married"`
}

func main(){
	//1、初始化连接
	client,err:=elastic.NewClient(elastic.SetURL("http://127.0.0.1:9200"))
	if err!=nil{
		panic(err)
	}
	fmt.Println("connect to es success")

	p1:= student{
		Name: "Rion",
		Age:22,
		Married: false,
	}

	//链式操作---->结构体连续调用多个方法用 . 拼接，需注意，各个方法要返回该结构体指针类型
	put1, err := client.Index().
		Index("student").
		BodyJson(p1).
		Do(context.Background())
	if err != nil {
		// Handle error
		panic(err)
	}
	fmt.Printf("Indexed stydent %s to index %s, type %s\n", put1.Id, put1.Index, put1.Type)


}
```

#### ES基本概念与关系型数据库的比较

|                     ES概念                     |    关系型数据库    |
| :--------------------------------------------: | :----------------: |
|           Index（索引）支持全文检索            | Database（数据库） |
|                  Type（类型）                  |    Table（表）     |
| Document（文档），不同文档可以有不同的字段集合 |   Row（数据行）    |
|                 Field（字段）                  |  Column（数据列）  |
|                Mapping（映射）                 |   Schema（模式）   |



## 6.系统监控

gopsutil做系统监控信息的菜鸡，写入influxDB，使用grafana作展示

prometheus监控：采集性能指标数据，保存起来，用grafana作展示

![image-20211108165900805](E:\typroa_pic\image-20211108165900805.png)



# 第二十一章、Gin框架

## 1.简单使用

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

//Json格式
func Gojson(c *gin.Context){
	c.JSON(http.StatusOK,gin.H{
		"name":"admin",
		"age":24,
	})
}

//XML格式
func Gohtml(c *gin.Context){
	c.XML(http.StatusOK,gin.H{
		"name":"admin",
		"age":24,
	})
}

func main() {
	engine:=gin.Default()//实例化一个对象
	engine.GET("/gojson",Gojson)
	engine.GET("/gohtml",Gohtml)
	engine.Run()//监听端口，默认8080端口可以改动

}
```

## 2.参数接收

三种接收方式：

```cpp
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"net/http"
)


func main() {
	engine:=gin.Default()
     //URL参数
	engine.GET("/get",func(c *gin.Context){
		fmt.Println(c.Query("name"))
		fmt.Println(c.Query("age"))
		c.String(http.StatusOK,"get")
	})

    //表单参数 post
	engine.POST("/post", func(c *gin.Context) {
		fmt.Println(c.PostForm("name"))
		fmt.Println(c.PostForm("age"))
		c.String(http.StatusOK,"post")
	})

     //API参数
	engine.GET("/getUser/:id", func(c *gin.Context) {
		fmt.Println(c.Param("id"))
		c.String(http.StatusOK,"getUser")
	})

	engine.Run()

}
```

## 3.参数绑定

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

type User struct {
	Name string `form:"name" binding:"required,len=6"`
	Age int `form:"age" binding:"numeric,min=18,max=100"`
}


func main() {
	engine:=gin.Default()
	engine.GET("/get",func(c *gin.Context){
		var user User
		err:=c.ShouldBind(&user)
		if err!=nil{
			c.String(http.StatusOK,err.Error())
		}else {
			c.String(http.StatusOK,"name->%s,age=%d",user.Name,user.Age)
		}
	})

	engine.Run()

}
```

## 4.上传文件

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"log"
	"net/http"
)


func main() {
	engine:=gin.Default()
	engine.POST("/upload", func(c *gin.Context) {
		//标单取文件
		file,_:=c.FormFile("file")
		log.Println(file.Filename)
		//传到项目根目录，名称不修改
		c.SaveUploadedFile(file,file.Filename)
		//打印信息
		c.String(http.StatusOK,fmt.Sprintf("%s upload! ",file.Filename))

	})
	engine.Run()

}
```

## 5.上传多个文件

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"net/http"
)


func main() {
	engine:=gin.Default()
	engine.MaxMultipartMemory=8<<20
	engine.POST("/upload", func(c *gin.Context) {
		//标单取文件,限制大小 8MB，gin默认为32MB
		form,err:=c.MultipartForm()
		if err!=nil{
			c.String(http.StatusOK,fmt.Sprintf("get err %s",err.Error()))
		}
		//获取所有图片
		file:=form.File["file"]

		//遍历所有图片
		for _,v := range file{
			//逐个存储
			if err=c.SaveUploadedFile(v,v.Filename);err!=nil{
				c.String(http.StatusOK,fmt.Sprintf("upload err %s",err.Error()))
				return
			}
		}
		c.String(http.StatusOK,fmt.Sprintf("upload ok %d files",len(file)))
	})
	engine.Run()

}
```

## 6.Gin路由原理

将各个路由组合成一颗前缀树

## 7.gin数据解析和绑定

**JSON格式：**

```GO
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

//定义接收数据的结构体
type Login struct {
	//binding:"required" 修饰的字段，必须存在，不能为空
	User string `form:"username" json:"user" uri:"user" xml:"user" binding:"required"`
	Passwd string `form:"passwd" json:"passwd" uri:"passwd" xml:"passwd" binding:"required"`
}
func main() {
	engine:=gin.Default()
	engine.POST("loginJSON", func(c *gin.Context) {
		//声明接收变量
		var json Login
		//将request的body中的数据  自动按照json格式解析到结构体
		if err:=c.ShouldBindJSON(&json);err!=nil{
			//返回错误信息
			c.JSON(http.StatusBadRequest,gin.H{
				"errr":err.Error(),
			})
			return
		}

		//判断用户名密码是否正确
		if json.User!="root"||json.Passwd!="admin"{
			c.JSON(http.StatusBadRequest,gin.H{"status":"504"})
			return
		}
		c.JSON(http.StatusOK,gin.H{
			"status":200,
		})
	})
	engine.Run()

}
```

## 8.HTML渲染

```go
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)


func main() {
	engine:=gin.Default()

	// 加载模板文件
	engine.LoadHTMLGlob("templates/*")//传路径
	//engine.LoadHTMLFiles("templates/index.html")//传文件
	engine.GET("/index", func(c *gin.Context) {
		//根据文件名渲染
		//最终json将title替换
		c.HTML(http.StatusOK,"index.html",gin.H{"title":"我的标题"})
	})

	engine.Run()

}
```

## 9.重定向

### HTTP重定向

HTTP 重定向很容易。 内部、外部重定向均支持。

```go
r.GET("/test", func(c *gin.Context) {
	c.Redirect(http.StatusMovedPermanently, "http://www.sogo.com/")
})
```

### 路由重定向

路由重定向，使用`HandleContext`：

```go
r.GET("/test", func(c *gin.Context) {
    // 指定重定向的URL
    c.Request.URL.Path = "/test2"
    r.HandleContext(c)
})
r.GET("/test2", func(c *gin.Context) {
    c.JSON(http.StatusOK, gin.H{"hello": "world"})
})
```

## 10.同步异步

```go
package main

import (
	"github.com/gin-gonic/gin"
	"log"
	"time"
)


func main() {
	engine:=gin.Default()

	//异步
	engine.GET("/long_async", func(c *gin.Context) {
		//需要使用c的副本
		copyContex:=c.Copy()
		//异步处理
		go func(){
			time.Sleep(time.Second*3)
			log.Println(  "异步执行"+copyContex.Request.URL.Path)
		}()

	})

	//同步
	engine.GET("/long_sync", func(c    *gin.Context){
		time.Sleep(time.Second*3)
		log.Println("同步执行"+c.Request.URL.Path)
	})

	engine.Run()

}
```

## 11.Gin中间件

Gin框架允许开发者在处理请求的过程中，加入用户自己的钩子（Hook）函数。这个钩子函数就叫中间件，中间件适合处理一些公共的业务逻辑，比如登录认证、权限校验、数据分页、记录日志、耗时统计等。

### 定义中间件

Gin中的中间件必须是一个`gin.HandlerFunc`类型。例如我们像下面的代码一样定义一个统计请求耗时的中间件。

```go
// StatCost 是一个统计耗时请求耗时的中间件
func StatCost() gin.HandlerFunc {
	return func(c *gin.Context) {
		start := time.Now()
		c.Set("name", "小王子") // 可以通过c.Set在请求上下文中设置值，后续的处理函数能够取到该值
		// 调用该请求的剩余处理程序
		c.Next()
		// 不调用该请求的剩余处理程序
		// c.Abort()
		// 计算耗时
		cost := time.Since(start)
		log.Println(cost)
	}
}
```

### 注册中间件

在gin框架中，我们可以为每个路由添加任意数量的中间件。

#### 为全局路由注册

```go
func main() {
	// 新建一个没有任何默认中间件的路由
	r := gin.New()
	// 注册一个全局中间件
	r.Use(StatCost())
	
	r.GET("/test", func(c *gin.Context) {
		name := c.MustGet("name").(string) // 从上下文取值
		log.Println(name)
		c.JSON(http.StatusOK, gin.H{
			"message": "Hello world!",
		})
	})
	r.Run()
}
```

#### 为某个路由单独注册

```go
// 给/test2路由单独注册中间件（可注册多个）
	r.GET("/test2", StatCost(), func(c *gin.Context) {
		name := c.MustGet("name").(string) // 从上下文取值
		log.Println(name)
		c.JSON(http.StatusOK, gin.H{
			"message": "Hello world!",
		})
	})
```

#### 为路由组注册中间件

为路由组注册中间件有以下两种写法。

写法1：

```go
shopGroup := r.Group("/shop", StatCost())
{
    shopGroup.GET("/index", func(c *gin.Context) {...})
    ...
}
```

写法2：

```go
shopGroup := r.Group("/shop")
shopGroup.Use(StatCost())
{
    shopGroup.GET("/index", func(c *gin.Context) {...})
    ...
}
```

### 中间件注意事项

#### gin默认中间件

`gin.Default()`默认使用了`Logger`和`Recovery`中间件，其中：

- `Logger`中间件将日志写入`gin.DefaultWriter`，即使配置了`GIN_MODE=release`。
- `Recovery`中间件会recover任何`panic`。如果有panic的话，会写入500响应码。

如果不想使用上面两个默认的中间件，可以使用`gin.New()`新建一个没有任何默认中间件的路由。

#### gin中间件中使用goroutine

当在中间件或`handler`中启动新的`goroutine`时，**不能使用**原始的上下文（c *gin.Context），必须使用其只读副本（`c.Copy()`）。

### next()方法

中间件中的next方法，会使中间件执行调用方的剩余程序，等剩余程序处理完才会执行next之后的语句

# 第二十二章、Raft

是consoul 和 etcd 保持数据一致性的核心算法

## 1.介绍

​		Raft提供了一种在计算系统集群中分布状态机的通用方法,确保集群中的每个节点都同意一系列相同的状态转换它有许多开源参考实现，具有Go，C++，Java和scala中的完整规范实现。

​		一个Raft 集群包含若干个服务器节点，通常是5个，这允许整个系统容忍2个节点的失效，每个节点处于以下三种状态之一：

​	1.  `follower`(跟随者):所有节点都以`follower`的状态开始。如果没收到`leader`消息.则会变成` candidate`状态

​	2.  `	candidate`(候选人〉:会向其他节点“拉选票”，如果得到大部分的票则成为`leader`，这个过程就叫做`Leader`选举(`Leader Election`)

​	3.  `leader`(领导者):所有对系统的修改都会先经过`leader`

## 2.raft一致性算法

`Raft`通过选出一个`leader`来简化日志副本的管理，例如，日志项(`log entry`)只允许从`leader`流向`follower`

基于`leader `的方法，`Raft`算法可以分解成三个子问题
`Leader election` (领导选举):原来的`leader` 挂掉后，必须选出一个新的`leader`

`Log replication`(日志复制):` leader` 从客户端接收日志，并复制到整个集群中

`Safety` (安全性):如果有任意的`server`将日志项回放到状态机中了，那么其他的`server`只会回放相同的日志项

## 3.动画演示

http://thesecretlivesofdata.com/raft/