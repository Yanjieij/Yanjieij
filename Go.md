## Go

### 结构

Go 语言的基础组成有以下几个部分：



- 包声明 package

  ​	定义包名，每个go文件的第一行都要有，且每个go程序都要有一个main包

- 引入包

  ​	

  ```go
  import "fmt"
  
  import(
      "fmt"
      "http"
      "myFunc"
        )
  ```

  

- 函数

  ​	

  ```go
  func myfunction() {
  
  		func code
  
  }
  ```

  

- 变量

- 语句 & 表达式

- 注释

  ```go
  //	这是单行注释
  
  /*
  	这是多行注释
  */
  ```

  

### 语法

Go语言的访问控制是使用变量命名控制的，如果想要对外暴露一个函数或者一个变量，只需将其首字母大写即可；反之只需要将其首字母小写即可。

```go
func privateFunc() {
	fmt.Println("Hello")
}

func PublicFunc(){
	fmt.Println("Hello")
}
```

声明变量使用var，格式为var identifier type

```go
var mystr string = "hello"

const constStr = "const string"	//常量的变量类型可以省略

var num1, num2, num3 int

var (
	name    string
	age     int
	address string
)

name, age, address = "jack", 1, "home"
```

### 数据类型

1. **布尔类型（bool）：**

   ​	表示真或假，只有两个取值：true和false。

2. **数字类型：**

   - **整数类型**：int8, int16, int32, int64, uint8, uint16, uint32, uint64, int, uint, uintptr
   - **浮点数类型**：float32, float64
   - **复数类型**：complex64, complex128

3. **字符串类型（string）：**

   ​	由一串字节组成，一旦创建就不可改变。

4. **派生类型：**

   - **指针类型（pointer）**：指向一个变量的内存地址
   - **数组类型**：固定长度的相同类型元素的集合
   - **切片类型（slice）**：可以动态增长的序列
   - **字典类型（map）**：无序的键值对的集合
   - **通道类型（channel）**：用于在协程之间传递数据
   - **结构体类型（struct）**：可以包含不同类型字段的集合
   - **接口类型（interface）**：定义了一组方法的集合，任何类型只要实现了这些方法即被视为实现了该接口
   - **函数类型（function）**：用于定义函数的类型



### 运算符

| 优先级 | 运算符           |
| :----- | :--------------- |
| 5      | * / % << >> & &^ |
| 4      | + - \| ^         |
| 3      | == != < <= > >=  |
| 2      | &&               |
| 1      | \|\|             |

### 条件分支

与C/C++类似，但不存在三目运算符

| 语句           | 描述                                                                                                                          |
| :------------- | :---------------------------------------------------------------------------------------------------------------------------- |
| if 语句        | **if 语句** 由一个布尔表达式后紧跟一个或多个语句组成。                                                                        |
| if...else 语句 | **if 语句** 后可以使用可选的 **else 语句**, else 语句中的表达式在布尔表达式为 false 时执行。                                  |
| if 嵌套语句    | 你可以在 **if** 或 **else if** 语句中嵌入一个或多个 **if** 或 **else if** 语句。                                              |
| switch 语句    | **switch** 语句用于基于不同条件执行不同动作。                                                                                 |
| select 语句    | **select** 语句类似于 **switch** 语句，但是select会随机执行一个可运行的case。如果没有case可运行，它将阻塞，直到有case可运行。 |

#### Switch

与C/C++中不同的点有：

- 支持的类型更多，比如变量类型也可以Switch
- 默认break，如果需要顺序执行要手动添加fallthrough
- 更灵活，Switch关键词后可以不设置变量，而是在case语句中使用条件表达式
- 合并case，一个case后可以接多个匹配值
- 同样的，每个case的数据类型必须一致

```go
var grade string = "B"
var marks int = 90

switch marks {
  case 90: grade = "A"
  case 80: grade = "B"
  case 50,60,70 : grade = "C"
  default: grade = "D"  
}

switch {
  case grade == "A" :
     fmt.Printf("优秀!\n" )
     fallthrough
  case grade == "B", grade == "C" :
     fmt.Printf("良好\n" )      
  case grade == "D" :
     fmt.Printf("及格\n" )
     fallthrough
  case grade == "F":
     fmt.Printf("不及格\n" )
  default:
     fmt.Printf("差\n" );
}
```

#### Select

- select 语句只能用于通道操作，每个 case 必须是一个通道操作，要么是发送要么是接收
- select 语句会监听所有指定的通道上的操作，一旦其中一个通道准备好就会执行相应的代码块
- 如果多个通道都准备好，那么 select 语句会随机选择一个通道执行。如果所有通道都没有准备好，那么执行 default 块中的代码

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	var ch1 chan string = make(chan string)
	ch2 := make(chan string)

	go func() {
		for {
			ch1 <- "from 1"
		}
	}()
	go func() {
		for {
			ch2 <- "from 2"
		}
	}()

	timeout := time.After(3 * time.Second) // 设定最长运行时间为5秒

	for {
		select {
		case msg1 := <-ch1:
			fmt.Println(msg1)
		case msg2 := <-ch2:
			fmt.Println(msg2)
		case <-timeout:
			fmt.Println("Program timed out. Exiting.")
			return
		}
	}
}
```

### 循环

- Go只有for一种循环，但是for支持多种变体，可以实现变相的while、for-each等等
- 支持continue和break，同时也支持goto语句

```go
myLabel:

sum := 0
for i := 0; i <= 10; i++ {
	sum += i
}

// go的for训练支持多个循环变量
for i, j := 0, N; i < j; i, j = i+1, j-1 {
   i = i + j
}

// 如果不涉及初始化和迭代，可以只保留判断条件（变相的while）
for sum <= 10{
	sum += sum
}

goto myLabel

// 不写条件就是无限循环
for {
    fmt.Println("Infinite.")
    break
}

// 可以使用range来实现for-each循环
strings := []string{"google", "runoob"}
for i, s := range strings {
	fmt.Println(i, s)
    continue
    fmt.Println(i, s)
}
```

### 函数

go语言函数的基本格式如下

```go
/*

func function_name( [parameter list] ) [return_types] {
   函数体
}

*/

func max(num1, num2 int) int {
   var result int
   if (num1 > num2) {
      result = num1
   } else {
      result = num2
   }
   return result 
}
```

值得一提的是，go是支持多个返回值的
在多返回值的情况下，空白符`_`是常用的接受不需要的参数的工具

```go
func divide(dividend, divisor int) (quotient int, err error) {
    if divisor == 0 {
        return 0, errors.New("division by zero")
    }
    quotient = dividend / divisor
    return quotient, nil
}

func main() {
    q, err := divide(10, 2) //这样获得多个返回值
    if err != nil {
        fmt.Println(err) 
    } else {
        fmt.Println(q) 
    }
}
```

Go语言同样支持变长参数，形式为参数中包含 `...type`
这种情况下，函数的参数类似于一个切片，可以对参数进行slice操作，同时你可以将切片作为参数传入
```go
func min(s ...int) int {
	if len(s)==0 {
		return 0
	}
	min := s[0]
	for _, v := range s {
		if v < min {
			min = v
		}
	}
	return min
}
```

`匿名函数`是一种没有指定函数名的函数，可以在函数内部定义并立即调用，也可以赋值给变量后再调用

`匿名函数`又叫做`闭包`：一个闭包继承了函数所声明时的作用域。这种状态（作用域内的变量）都被共享到闭包的环境中，因此这些变量可以在闭包中被操作，直到被销毁

```go
func main() {
    // 定义并调用匿名函数
    func() {
        fmt.Println("Hello, this is an anonymous function.")
    }() //这里的()表示立即调用，该定义语句之后此函数会被自动调用

    // 匿名函数赋值给变量后调用
    add := func(a, b int) int {
        return a + b
    }

    result := add(3, 5)
    fmt.Println("Result:", result)
}
```

Go还支持特殊的 `defer` 关键词，允许声明一个将在包含它的函数返回前执行的延迟函数调用

```go
func myfunc(a int) {
	println("a is ", a)
}

func main() {
	a := 1
	myfunc(a)
	defer myfunc(a)
	a = a + 111
	myfunc(a)
}
// 结果：1 112 1
```

`defer`的实现原理是将调用时函数参数以结构体的形式暂存起来，直到母函数返回时输入函数进行调用

实际上，匿名函数和defer一同使用时会出现许多特殊的`未定义行为`，比如下面的代码返回值为2而不是1，应该确保安全使用
```go
package main

import "fmt"

func f() (ret int) {
	defer func() {
		ret++
	}()
	return 1
}
func main() {
	fmt.Println(f())
}
// output: 2
// 从编译的角度来说，return 1会直接写入内存，返回应该就是1
// go语言专门针对这种情况进行了设计，此时return值会被更新为ret的新值
```


### 变量作用域

go语言中的变量也存在作用域，与C/C++类似，包含局部变量、全局变量等

### 数组

go语言中数组的语法格式如下

```go
// var arrayName [size]dataType

var balance = [5]float32{1000.0, 2.0, 3.4, 7.0, 50.0}

balance := [5]float32{1000.0, 2.0, 3.4, 7.0, 50.0}

// 支持自动设定数组长度

var arrLazy = [...]int{5, 6, 7, 8, 22}

balance := [5]float32{1:2.0,3:7.0}	// 可以指定数组中某个位置进行初始化

//访问方式与C类似
var salary float32 = balance[9]
```

当数组作为参数时

```go
func myFunction(param [10]int) {
    ....
}

// 不指定长度也可以
func myFunction(param []int) {
    ....
}
```

#### 多维数组

```go
// var variable_name [SIZE1][SIZE2]...[SIZEN] variable_type

var threedim [5][10][4]int

// 多维数组直接初始化
a := [3][4]int{  
 {0, 1, 2, 3} ,
 {4, 5, 6, 7} ,
 {8, 9, 10, 11},
}

// 多维数组间接初始化
values := [][]int{}

// 使用 append() 函数向空的二维数组添加两行一维数组
row1 := []int{1, 2, 3}
row2 := []int{4, 5, 6}
values = append(values, row1)
values = append(values, row2)

```

### 指针

go语言的指针与C/C++非常相似

```go
var a int = 20    /* 声明实际变量 */
var ip *int = nil /* 声明指针变量 */

ip = &a /* 指针变量的存储地址 */

fmt.Printf("a变量的地址是: %d\n", ip)
fmt.Printf("a变量是: %d\n", *ip)
```

值得一提的是，go中空指针的值为nil

### 结构体

go的结构体与C类似

```go
// 结构体语法
type Books struct {
   title string
   author string
   subject string
   book_id int
}

var Book1 Books

Book1.title = "Go 语言"
Book1.author = "www.runoob.com"
Book1.subject = "Go 语言教程"
Book1.book_id = 6495407

fmt.Printf( "Book 1 title : %s\n", Book1.title)
fmt.Printf( "Book 1 author : %s\n", Book1.author)
fmt.Printf( "Book 1 subject : %s\n", Book1.subject)
fmt.Printf( "Book 1 book_id : %d\n", Book1.book_id)

//当然，也存在指向结构体的指针
var BookPtr *Books
BookPtr = *Book1
```

### 切片

`切片 (slice)` 是对数组一个连续片段的引用，所以切片是一个引用类型，因此更类似于 C/C++ 中的数组类型，或者 Python 中的 list 类型

- 切片是一个`长度可变`的数组(注意，内存角度下他们本质不同!)
- 多个切片如果表示同一个数组的片段，它们可以共享数据，一个切片和相关数组的其他切片是共享存储的（相反，不同的数组总是代表不同的存储）
- 因为切片是引用，所以它们不需要使用额外的内存并且比使用数组更有效率，所以在 Go 代码中切片比数组更常用
- 切片在内存中的组织方式实际上是一个有 `3个域`的结构体：指向相关数组的`指针`，切片`长度`以及切片`容量`

```go

```


### Map

`map` 是一种内建的数据结构，用于存储键值对（key-value pairs）。map 的特点在于它的键（`key`）是唯一的，并且它允许你通过键快速查找、插入或删除相应的值（`value`）

这种数据结构在内部通常使用散列表（hash table）实现

- 未初始化的 map 变量的零值是 `nil`
- 每个键在 map 中必须是唯一的，这意味着你不能有重复的键。
- 键可以是任意可比较的类型（如 int, string, struct 等），而值可以是任何类型
- 因为 map 是无序的，所以在遍历 map 时，元素的顺序可能每次都不相同

```go
// 用make创建map
m := make(map[keyType]valueType)

ages := map[string]int{"Alice": 30, "Bob": 25}

// 索引取值，这是显然的
var age int = ages["Alice"]

// map取值可能出现key不存在的情况，可以使用如下语法
age, exists := ages["Dave"]
if exists {
    fmt.Println(age)
} else {
    fmt.Println("Key not found")
}

// 循环遍历map，要注意这是无序的
for key, value := range ages {
	value = 0
}

// 删除key-value
delete(ages,"Bob")

// 实际上，map可以嵌套起来
var nestedMap = make(map[int]map[string]int)
nestedMap[0] = make(map[string]int)
nestedMap[0]["Alice"] = 30
nestedMap[1] = make(map[string]int)
nestedMap[1]["Bob"] = 25

// 或者map类型的切片
// 但数组不行，这是底层决定的，切片只是指针，但数组要连续保存！
items := make([]map[int]int, 5)
	for i:= range items {
		items[i] = make(map[int]int, 1)
		items[i][1] = 2
	}

```

### New函数

`new` 是一个内置函数，用于为指定类型分配内存，并返回指向该类型零值的指针

new 函数的主要用途是在运行时动态地为数据类型分配内存空间，这对于需要延迟初始化或控制资源分配的场景特别有用

### Make函数

`make` 是一个内建函数，主要用于初始化三个特殊的内建数据类型：切片（`slices`）、映射（`maps`）和通道（`channels`）

这些数据结构需要动态分配内存，`make` 除了分配内存之外，还会初始化这些类型的内部状态，使它们可以直接使用

### 格式化输出

Go中的输出函数种类很多，其中
-  `Print`和`Printf`是原生函数，只输出在命令行中
-  `fmt`包中的`Sprint`系列返回字符串，不进行输出
-  `fmt`包中的`Fprint`系列用于在文件中写入
-  后面带有`ln`的输出函数代表尾部自带换行

Go支持与C类似的格式化输出，在格式化字符串里

- `%d` 用于格式化整数（`%x` 和 `%X` 用于格式化 16 进制表示的数字）
- `%f` 于格式化浮点型（`%e` 输出科学计数表示法）
- `%0nd` 用于规定输出长度为 n 的整数，其中开头的数字 0 是必须的
- `%.xf` 用于表示精确到小数点后 x 位，除了使用 f 之外，还可以使用 e
- `%s` 用于格式化字符串

### 字符串处理

在Go语言中，字符串的不可变性很强，一旦一个字符串被创建，它的内容就不能被更改

如果需要对字符串进行字符操作，可以使用如下代码
```go
// 将字符串转为字节数组，然后对字符进行操作
s := "hello"
c := []byte(s)
c[0] = 'c'
s2 := string(c) // s2 == "cello"
```




