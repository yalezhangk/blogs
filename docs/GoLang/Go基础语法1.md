#
# 一、Go语言设计哲学

Go设计者推崇“`最小方式`”思维，即一件事仅有一种方式，或尽可能少的方式去完成，目的是减少开发人员的选择痛苦。

* 仅有25个关键字，简洁的语法
* 内置垃圾回收器，大大降低程序员管理内存的负担
* `去除隐式类型转换、去除指针算数`，提高语言健壮性
* 首字母大小写决定可见性，通过约定而不是声明告诉开发者，提高阅读效率
* 故意`不支持函数默认参数`，必须明确每个参数意义，提高设计的清晰度和代码可读性
* `没有面向对象`的类，也没有子类，没有构造函数和析构函数
* 偏好`组合`
* 任何类型都可以拥有方法
* 接口只是方法的集合，其实现是`隐式`的(无需implements显式声明)
* n--、n++都是语句，不是表达式。没有--n、++n
* `没有三元运算符`
* 内存总是初始化为零值
* 没有异常
* 内置字符串、切片、map等类型
* 内置并发支持，对多核计算机支持友好。`goroutine、channel、select`就是为并发而生的
* 官方提供了丰富的工具链、涵盖了编译、编辑、依赖获取、调试、文档、性能分析等诸多方面

# 二、常量

## 1. 字面常量

1. 字面常量（literal），是指程序中硬编码的常量，如：-12。**它们的值即为它们本身，是无法被改变的**
2. **值不可改变的常量称为`字面常量`**
   ，在Go语言中，boolean、rune、integer、float、complex、string都是字面常量，其中，rune、integer、float、complex常量被称为`数值常量`

## 2. 常量

1. 使用`const`定义常量，且常量必须在声明时进行赋值，并且之后不能改变

```go

const a int = 123
```

1. `_`是一个空白标识符，可以定义，但不可以使用
2. `iota`: 总是从0开始递增+1的integer常量

# 三、字符串

## 1. 字符串介绍

1. Go语言中使**用`反引号`或者`双引号`来定义字符串**。反引号表示原生的字符串，即不进行转义。

```go
// 1. 双引号，将转义字符
str: "Hello Wrold. \n ZK\N"
// 输出：
Hello World.
ZK

// 2. 反引号，不会转义相关的字符

str :=  `Hello World! \n Hello Gopher! \n` 

输出：
Hello World! \nHello Gopher! \n
```

1. string类型是一种值类型，存储的**字符串是不可变的**，如果要修改string内容需要将string转换为\[]byte或\[]
   rune，并且修改后的string内容是重新分配的
2. string类型的零值是为长度为零的字符串，即空字符串 ""
3. 一般的比较运算符（`==、!=、<、<=、>=、>`）通过在内存中按字节比较来实现字符串的对比。通过函数 **`len()`**
   来获取字符串所占的字节长度，例如：len(str)
4. 字符串的内容（纯字节）可以通过标准索引法来获取，在中括号 \[] 内写入索引，索引从 0 开始计数：
5. 字符串 str 的第 1 个字节：str\[0] 第 i 个字节：str\[i - 1] 最后 1 个字节：str\[len(str)-1]
6. **Go语言默认使用UTF-8编码**, **len(str)获取的是每个字符的`UTF-8编码的长度和`，而不是直接的字符数量。每个中文字符的长度为3，而非1
   **

```go
package main

import (
	"fmt"
	"unicode/utf8"
)

func main() {

	s := "其实就是rune"
	fmt.Println(len(s))                    // "16"
	fmt.Println(utf8.RuneCountInString(s)) // "8"
}
```

## 2. 字符串拼接

1. 直接使用运算符`+` 或者 使用`+=`

```go
s := "hel" + "lo, "
s += "world!"
fmt.Println(s) // 输出 “hello, world!”
```

1. 使用`fmt.Sprintf()`格式化，该方法内部使用\[]byte实现。

```go
fmt.Sprintf("%d:%s", 2018, "年")
```

1. `strings.Join()`, Join会先根据字符串数组的内容，计算出一个拼接之后的长度，然后申请对应大小的内存，一个一个字符串填入，在已有一个数组的情况下，这种效率会很高，但是本来没有，去构造这个数据的代价也不小

```go
strings.Join([]string{"hello", "world"}, ", ")  // 输出："hello,world"
```

1. `bytes.Buffer`, 比较理想，**可以当成可变字符使用**，对内存的增长也有优化，如果能预估字符串的长度，还可以用 buffer.Grow()
   接口来设置 capacity。

```go
var buffer bytes.Buffer
buffer.WriteString("hello")
buffer.WriteString(", ")
buffer.WriteString("world")

fmt.Print(buffer.String())  // 输出："hello,world"
```

1. `strings.Builder`, strings.Builder 内部通过 slice 来保存和管理内容。slice 内部则是通过一个指针指向实际保存内容的数组。strings.Builder
   同样也提供了 Grow() 来支持预定义容量。当我们可以预定义我们需要使用的容量时，strings.Builder 就能避免扩容而创建新的
   slice 了。strings.Builder是非线程安全，性能上和 bytes.Buffer 相差无几

```go
var b1 strings.Builder
b1.WriteString("ABC")
b1.WriteString("DEF")

fmt.Print(b1.String())  //输出: "ABCDEF"
```

## 3. 有关string处理

1. 标准库中有四个包对字符串处理尤为重要：`bytes`、`strings`、`strconv`和`unicode`包。

<!---->

    strings 包提供了很多操作字符串的简单函数，通常一般的字符串操作需求都可以在这个包中找到。下面简单举几个例子：

    判断是否以某字符串打头/结尾 strings.HasPrefix(s, prefix string) bool strings.HasSuffix(s, suffix string) bool

    字符串分割 strings.Split(s, sep string) []string

    返回子串索引 strings.Index(s, substr string) int strings.LastIndex 最后一个匹配索引

    字符串连接 strings.Join(a []string, sep string) string 另外可以直接使用“+”来连接两个字符串

    字符串替换 strings.Replace(s, old, new string, n int) string

    字符串转化为大小写 strings.ToUpper(s string) string strings.ToLower(s string) string

    统计某个字符在字符串出现的次数 strings.Count(s, substr string) int

    判断字符串的包含关系 strings.Contains(s, substr string) bool

# 四、数组(Array)

1. `数组`是**具有相同唯一类型的一组已编号且长度固定的数据项序列**
   。这种类型可以是任意的原始类型例如整型、字符串或者自定义类型。数组长度必须是一个常量表达式，并且必须是一个非负整数。
2. 数组长度也是数组类型的一部分，所以[5]int和[10]int是属于不同类型的
3. `一维数组`声明以及初始化常见方式如下：

```go
var arrAge  = [5]int{18, 20, 15, 22, 16}
var arrName = [5]string{3: "Chris", 4: "Ron"} //指定索引位置初始化 
// {"","","","Chris","Ron"}
var arrCount = [4]int{500, 2: 100} //指定索引位置初始化 {500,0,100,0}
var arrLazy = [...]int{5, 6, 7, 8, 22} //数组长度初始化时根据元素多少确定
var arrPack = [...]int{10, 5: 100} //指定索引位置初始化，数组长度与此有关 {10,0,0,0,0,100}
var arrRoom [20]int
var arrBed = new([20]int)
```

4. **数组在声明时需要确定长度**，但是也可以采用上面不定长数组(**使用[...]int声明**)的方式声明，在初始化时会自动确定好数组的长度。上面
   arrPack 声明中 len(arrPack) 结果为6
   ，表明初始化时已经确定了数组长度。而arrRoom和arrBed这两个数组的所有元素这时都为0，这是因为每个元素是一个整型值，当声明数组时所有的元素都会被自动初始化为默认值
   0。
5. **new([5]int)创建的是数组指针, [5]int创建的是数组**

```go
package main

import (
	"fmt"
)

func main() {

	var arr1 = new([5]int)
	arr := arr1
	arr1[2] = 100
	fmt.Println(arr1[2], arr[2])

	var arr2 [5]int
	newarr := arr2
	arr2[2] = 100
	fmt.Println(arr2[2], newarr[2])
}

程序输出：
100 100
100 0
```

6. 从上面代码结果可以看到，new([5]int)
   创建的是数组指针，arr其实和arr1指向同一地址，故而修改arr1时arr同样也生效。而newarr是由arr2值传递（拷贝），故而修改任何一个都不会改变另一个的值。在写函数或方法时，如果参数是数组，需要注意参数长度不能过大。
7. `多维数组`在Go语言中也是支持的，例如：在定义多维数组时，仅第一维允许使用“…”,而内置函数len和cap也都返回第一维度长度

```go
[...][5]int{ {10, 20}, {30, 40} }	   // len() 长度根据实际初始化时数据的长度来定，这里为2
[3][5]int				   // len() 长度为3
[2][2][2]float64			   // 可以这样理解 [2]([2]([2]float64))
```

8. 数组元素可以通过索引（下标）来读取（或者修改），索引从 0 开始，数组长度在声明时就需给出，数组大小最大为2GB。

```go
var arrAge = [5]int{18, 20, 15, 22, 16}
	for i, v := range arrAge {
		fmt.Printf("%d 的年龄： %d\n", i, v)
}

0 的年龄： 18
1 的年龄： 20
2 的年龄： 15
3 的年龄： 22
4 的年龄： 16

```

# 五、切片

## 1. 切片

1. **`切片`（`slice`）** 是对底层数组一个连续片段的引用，所以切片是一个引用类型。切片提供对该数组中编号的元素序列的访问。未初始化切片的值为nil。
2. 可以将**切片看成是一个长度可变的数组**
3. 切片提供了计算容量的函数 cap() ，可以测量切片最大长度。切片的长度永远不会超过它的容量，所以对于切片 s
   来说，这个不等式永远成立：0 <= len(s) <= cap(s)。
4. 因为**切片是引用**，所以它们不需要使用额外的内存并且比使用数组更有效率，所以在 Go 代码中切片比数组更常用。
5. 声明切片的方式是： var identifier []type(不需要说明长度)。一个切片在未初始化之前默认为 nil，长度为 0。

```go
// 切片初始化格式
var slice1 []type = arr1[start:end]

// 切片也可以用类似数组的方式初始化,这样就创建了一个长度为 5 的数组并且创建了一个相关切片
var x = []int{2, 3, 5, 7, 11}
```

6. 当相关数组还没有定义时，我们可以使用**make() 函数来创建一个切片，同时创建好相关数组**

```go
var slice1 []type = make([]type, len,cap)
// 也可以简写为:
slice1 := make([]type, len)
// 这里 len 是数组的长度并且也是切片的初始长度。cap是容量，其中cap是可选参数。
```

7. 如果从数组或者切片中生成一个新的切片，我们可以使用下面的表达式：**a[low : high : max]**，max-low的结果表示容量，high-low的结果表示长度。

```go
a := [5]int{1, 2, 3, 4, 5}
t := a[1:3:5]
// a: [1, 2, 3, 4, 5]
// t: [2 3]
```

## 2. 切片重组(reslice)

1. slice1 := make([]type, start_length, capacity)
2. 通过改变切片长度得到新切片的过程称之为切片重组 reslicing，做法如下：slice1 = slice1[0:end]，其中 end 是新的末尾索引（即长度）
3. 从临时的切片中使用内置函数`copy()`，拷贝数据（而不是重新划分切片）到新切片.
4. `append()`内置函数: append()函数将 0 个或多个具有相同类型S的元素追加到切片s后面并且返回新的切片，**追加的元素必须和原切片的元素同类型
   **
5. append()函数操作后，有没有生成新的切片需要看原有切片的容量是否足够。

```go
s0 := []int{0, 0}
s1 := append(s0, 2)                // append 单个元素     s1 == []int{0, 0, 2}
s2 := append(s1, 3, 5, 7)          // append 多个元素    s2 == []int{0, 0, 2, 3, 5, 7}
s3 := append(s2, s0...)            // append 一个切片     s3 == []int{0, 0, 2, 3, 5, 7, 0, 0}
s4 := append(s3[3:6], s3[2:]...)   // append 切片片段    s4 == []int{3, 5, 7, 2, 3, 5, 7, 0, 0}

```

# 六、字典(Map)

## 1. map概述

1. **`map`是一种元素对的无序集合**，一组称为元素value，另一组为唯一键索引key。未初始化的map的值为nil。map是引用类型，声明格式如下：

```go
var map1 map[keytype]valuetype
```

2. 在声明的时候不需要知道 map 的长度，map 是可以动态增长的
3. key 可以是任意可以用 == 或者 != 操作符比较的类型，比如 string、int、float, struct ;数组、函数、字典、切片和结构体不能作为
   key
4. map 可以用 {key1: val1, key2: val2} 的描述方法来初始化，就像数组和结构体一样。
   map 是引用类型的，内存用 `make `方法来分配。map 的初始化：

```go
var map1 = make(map[keytype]valuetype)
```

5. map 容量： 和数组不同，map 可以根据新增的 key-value 对动态的伸缩，因此它不存在固定长度或者最大限制。但是你也可以选择标明
   map 的初始容量 capacity，就像这样：make(map[keytype]valuetype，cap)。

```go
// 声明但未初始化map，此时是map的零值状态
map1 := make(map[string]string, 5)

map2 := make(map[string]string)

// 创建了初始化了一个空的的map，这个时候没有任何元素
map3 := map[string]string{}

// map中有三个值
map4 := map[string]string{"a": "1", "b": "2", "c": "3"}

// 删除key1, 如果key1不存在，该操作不会产生错误
delete(map4, "key1")
```

6. map 默认是无序的，不管是按照 key 还是按照 value 默认都不排序。如果你想为 map 排序，需要将 key（或者
   value）拷贝到一个切片，再对切片排序（使用 sort 包）

## 2. range语句

1. **在"range"语句中生成的数据的值是真实集合元素的拷贝，它们不是原有元素的引用**
   。这意味着更新这些值将不会修改原来的数据。同时也意味着使用这些值的地址将不会得到原有数据的指针

```go
// range语句中生成的数据的值是真实集合元素的拷贝
package main
import "fmt"
func main() {  
    data := []int{1, 2, 3}
    for _, v := range data {
        v *= 10 // 通常数据项不会改变
    }
    fmt.Println("data:", data) // 程序输出: [1 2 3]
}

程序输出：
data: [1 2 3]

// 如果你需要更新原有集合中的数据，使用索引操作符来获得数据
package main
import "fmt"
func main() {  
    data := []int{1, 2, 3}
    for i, _ := range data {
        data[i] *= 10
    }

    fmt.Println("data:", data) // 程序输出 data: [10 20 30]
}

程序输出：
data: [10 20 30]
```

# 七、流程控制

## 1. switch语句

```go
// var1
switch var1 {
    case val1:
        ...
    case val2:
        ...
    default:
        ...
}
// switch初识无判断值
switch {
    case condition1:
        ...
    case condition2:
        ...
    default:
        ...
}
// 有判断值
switch result := calculate(); {
    case result < 0:
        ...
    case result > 0:
        ...
    default:
        // 0
}
```

1. 变量 var1 可以是任何类型，而 val1 和 val2
   则可以是同类型的任意值。类型不被局限于常量或整数，但必须是相同的类型；或者最终结果为相同类型的表达式。前花括号 { 必须和
   switch 关键字在同一行。
2. 如果在执行完每个分支的代码后，还希望继续执行后续分支的代码，可以使用 `fallthrough` 关键字来达到目的。
   **fallthrough强制执行后面的case代码，fallthrough不会判断下一条case的expr结果是否为true。**

```go
package main

import "fmt"

func main() {

	switch a := 1; {
	case a == 1:
		fmt.Println("The integer was == 1")
		fallthrough
	case a == 2:
		fmt.Println("The integer was == 2")
	case a == 3:
		fmt.Println("The integer was == 3")
		fallthrough
	case a == 4:
		fmt.Println("The integer was == 4")
	case a == 5:
		fmt.Println("The integer was == 5")
		fallthrough
	default:
		fmt.Println("default case")
	}
}
```

## 2. select控制

1. `select`也是一个控制结构，类似于switch语句，主要用于**处理异步通道操作**， 所有情况都会涉及通信操作。
2. select会监听分支语句中通道的读写操作，当分支中的通道读写操作为非阻塞状态（即能读写）时，将会触发相应的动作。
3. select语句会选择一组可以发送或接收操作中的一个分支继续执行。select没有条件表达式，一直在等待分支进入可运行状态。

> select中的case语句必须是一个`channel`操作
> select中的default子句总是可运行的。

- 如果有多个分支都可以运行，select会伪随机公平地选出一个执行，其他分支不会执行。
- 如果没有可运行的分支，且有default语句，那么就会执行default的动作。
- 如果没有可运行的分支，且没有default语句，select将阻塞，直到某个分支可以运行。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	var c1, c2, c3 chan int
	var i1, i2 int
	select {
	case i1 = <-c1:
		fmt.Printf("received ", i1, " from c1\n")
	case c2 <- i2:
		fmt.Printf("sent ", i2, " to c2\n")
	case i3, ok := (<-c3): 
		if ok {
			fmt.Printf("received ", i3, " from c3\n")
		} else {
			fmt.Printf("c3 is closed\n")
		}
	case <-time.After(time.Second * 3): //超时退出
		fmt.Println("request time out")
	}
}

// 输出：request time out
```

## 3. for循环

1. 最简单的基于计数器的迭代，基本形式为：

```go
for  初始化语句; 条件语句; 修饰语句 {}
```

## 4. for-range 结构

1. `for-range`可以迭代任何一个集合，包括数组（array）和字典（map），同时可以获得每次迭代所对应的**索引和值**。一般形式为：

```go
for index, val := range coll { }
```

2. **If语句**由布尔表达式后紧跟一个或多个语句组成，注意布尔表达式不用()

```go
if 布尔表达式 {
   /* 在布尔表达式为 true 时执行 */
}
```

3. 一个 **break**的作用范围为该语句出现后的最内部的结构，它可以被用于任何形式的for循环（计数器、条件判断等）
4. 但在 switch 或 select 语句中，break 语句的作用结果是跳过整个代码块，执行后续的代码。
5. 关键字 **continue**忽略剩余的循环体而直接进入下一次循环的过程，但不是无条件执行下一次循环，执行之前依旧需要满足循环的判断条件
6. 关键字 continue 只能被用于 for 循环中。
7. `label`标签 for、switch 或 select 语句都可以配合标签（label）形式的标识符使用，即某一行第一个以冒号（:）结尾的单词（Gofmt
   会将后续代码自动移至下一行）,**不推荐使用**

# 八、错误处理

## 1. 错误类型

1. 任何时候当需要一个新的错误类型，都可以使用errors.New函数接收错误信息来创建
2. 也可以用fmt 创建错误对象

```go
// new一个error
err := errors.New("math - square root of negative number")
func Sqrt(f float64) (float64, error) {
if f < 0 {
        return 0, errors.New ("math - square root of negative number")
    }
}
// 使用占位符
if f < 0 {
    return 0, fmt.Errorf("square root of negative number %g", f)
}
```

## 2. Panic

1. 在Go语言中 `panic()` 是一个内置函数，用来表示非常严重的不可恢复的错误。

## 3. Recover：从异常中恢复

1. recover() 这个内建函数被用于从异常或错误场景中恢复：让程序可以从 panicking 重新获得控制权，停止终止过程进而恢复正常执行。
   **recover() 只能在 defer 修饰的函数中使用：用于取得异常调用中传递过来的错误值，如果是正常执行，调用 recover() 会返回
   nil，且没有其它效果**。 总结：异常会导致栈被展开直到 defer 修饰的 recover() 被调用或者程序中止。

## 4. defer

- 规则一 当defer被声明时，其参数就会被实时解析
- 规则二 **defer执行顺序为先进后出**
- 规则三 defer可以读取有名返回值，也就是可以改变有名返回参数的值。

# 九、函数func

## 1. 函数介绍

1. 函数基本组成：关键字`func`、函数名、参数列表、返回值、函数体和返回语句。语法如下:

```go
func 函数名(参数列表) (返回值列表) {
    // 函数体
return
}
```

2. `函数签名`由函数参数、返回值以及它们的类型组成，被统称为函数签名
3. 函数调用传入的参数必须按照参数声明的顺序。而且**Go语言没有默认参数值**的说法
4. 函数签名中的最后传入参数可以具有前缀为...的类型（...int），这样的参数称为可变参数，并且可以使用零个或多个参数来调用该函数，这样的函数称为
   **变参函数**。

```go
func doFix (prefix string, values ...int)
```

5. 