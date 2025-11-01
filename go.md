```go
package main
import "fmt"
func main() {
	fmt.Println("Hello, World!")
}
```

# GO定义变量

go语言中必须要定义文件所属的包

```go
package main
```

go中定义变量有3种方式

## 1.在变量前加var

```go
var 变量名 类型(可省略) = 表达式
var name string = "zhangsan"
```

## 2.类型推导方式定义变量

```
变量名 := 表达式
```



# Print和Println、Printf

```go
fmt.Println("Hellow world")
fmt.Print("Hellow world")
fmt.Printf("Hellow world")
```

快速注释：Ctrl+/

Println和Print区别：

Println可以换行

Print不可以换行

Printf默认不会换行

Printf输出格式化后的内容

# 变量

go语言中变量定义了必须使用

变量名称的命名：Go语言变量名有字母、数字、下划线组成，其中首个字符不能为数字，关键字和保留字不能作为变量名

变量声明后没有初始化 值为空

```go
# 第一种初始化变量方法
var username string
username = "张三"
# 第二种初始化变量的方法
var username string = "张三"
# 第三种初始化变量的方法
var username = "张三"
```

同一个作用域变量不能重复声明

```go
# 一次声明多个变量

var 变量名称，变量名称 类型

var (
    变量名称 类型
    变量名称 类型
)
```

Go语言中定义的是string类型，赋值的时候必须是string类型

短变量声明法 在函数内部，可以使用更简略的 :=方式声明并进行初始化

段变量只能用于声明局部变量，不能用于全局变量

匿名变量 在使用多重赋值时，如果想要忽略某个值，可以使用匿名变量

匿名变量用一个下划线\_表示 

匿名变量不占用命名空间，不会分配内存，所以匿名变量之间不存在重复声明

# 常量

常量是恒定不变的，无法改变

```
# 使用const定义变量
const pi = 3.1415
const e = 2.7182
```

多个常量也可以一起声明

```go
const (
    A = "AAA"
    B = "BBB"
)
```

const同事声明多个常量时，如果省略了值则表示和上面一行的值一样

## iota

iota是go语言的常量计数器，只能在常量的表达式中使用

iota在const关键字出现时将被重置为0(const内部第一行之前)，const中每新增一行常量声明将时iota计数一次(iota可理解为const语句中的行索引)

每次const出现时，都会让iota初始化为0

```go
# iota插队
const (
    a = iota //0
    b = 100 //100
    c = iota //2
    d //3
)
# 多个iota定义在一行
const (
    a,b = iota + 1,iota + 2 //1,2
    c,d //2,3
    e,f //3,4
)
```

# 数据类型

## 基本数据类型

### 整形

有符号整型int8、int16、int31、int64


对应无符号整型uint8、uint16、uint32、uint64
强制转换：

```
var a1 int32 = 10
var a2 int64 = 20
fmt,Println(int64(a1) + a2)
```

高位向低位转换要注意是否超出范围

### 数字字面量语法

%d表示10进制输出 %b表示二进制输出 %o表示八进制输出 %x表示16进制输出 %T输出数据类型 %v原样输出 %f保留六位小数 %.2f保留两位小数

### unsafe.Sizeof

unsafe.Sizeof(n1)是unsafe包的一个函数，可以返回n1变量占用的字节数

## 浮点型

go支持两种浮点型：float32和float64

go语言中默认float64

### 科学计数法表示浮点数

```
var f2 float32 = 3.14e2 //表示f2等于3.14*10的2次方
```

## 布尔类型

布尔类型只有true和false

注意：

* 布尔类型变量默认为false
* go语言不允许将整型强制转换为布尔型
* 布尔型无法参与数值计算，也无法与其他类型进行转换

string型变量默认值为空

int型变量默认值为0

float型变量默认值为0

## 字符串

字符串转义符

\n换行 \转义字符

### 多行字符串

gi语言中要定义一个多行字符串时，就必须使用反引号字符\`

```go
str := `dfgh
dfghj
bnm
`
```

### 字符串的常用操作

| len(str)                            | 求长度         |
| ----------------------------------- | -------------- |
| +或fmt.Sprintf                      | 拼接字符串     |
| strings.Split                       | 分割           |
| strings.contains                    | 判断是否包含   |
| strings.HasPrefix,strings.HasSuffix | 前缀/后缀判断  |
| strings.Index(),strings.LastIndex() | 字串出现的位置 |
| strings.Join(a[]string,sep string)  | join操作       |


## byte和rune

字符不属于基本数据类型，而是int型

字符只能使用单引号，里面只能有一个字母或汉字

当我们直接输出byte字符时输出的是这个字符对应的码值

GO中的字符有两种

1.uint8，或者byte型，代表了ASCLL码的一个字符

2.rune类型，代表一个UTF-8字符

一个汉字占用3个字节，一个字母占用1个字节

unsafe.Sizeof()没法查看string类型数据所占用的存储空间，只能用len()

golang中汉字使用的是utf-8编码，编码过后的值就是int类型

### 修改字符串

要修改字符串，需要先将其转换成[]rune或[]byte，完成后再转换为string，无论哪种转换，都会重新分配内存，并复制字节数组

```
func main() {
	s := "hello, world"
	byteStr := []byte(s)
	byteStr[0] = 'H'
	fmt.Println(string(byteStr))
}
```

## 基本数据类型之间转换

转换的时候建议从地位转换为高位

### 其他类型转换为string型

1.使用fmt.Sprintf

```
strs = fmt.Sprintf("%t",t)
```

int为%d float为%f bool为%t byte为%c

2.使用strconv包里面的几种转换方式进行转换

FormatInt:参数1：int64类型 参数2：传值int类型的进制

```
str1 := strconv.FormatInt(int64(i),10)
```

FormatFloat：参数1：要转换的值 

参数2：格式化的类型 'f'(-ddd.dddd) 

'b'(-ddddp+-ddd，指数为二进制) 

'e'(-d.dddde+-dd,十进制指数)

'E'(-d.ddddE+-dd,十进制指数) 

'g'(指数很大时用'e'格式，否则'f格式') 

'G'(指数很大时用'E'格式，否则'f'格式)

参数3：保留的小数点-1(不对小数点格式化)

参数4：格式化的类型 传入 64 32

```
str2 := strconv.FormatFloat(float64(f),'f',4,64)
```

#  运算符

## 位运算符

| &    | 参与运算的两数各对应的二进位相与(两位均为1才为1)             |
| ---- | ------------------------------------------------------------ |
| \|   | 参与运算的两数各对应的二进位相或(两个有一个为1就为1)         |
| ^    | 参与运算的两数各对应的二进位相异或，当两对应的二进位相异时，结果为1(两位不一样则为1) |
| <<   | 左移n位就是乘以2的n次方。"a<<b"就是把a的各二进位全部左移b位，高位丢弃，低位补0 |
| >>   | 右移n位就是除以2的n次方。"a>>b"是把a的各二进位全部右移b位    |

# 流程控制

## if else

```
if 表达式1 {
    分支1
}else if 表达式2 {
    分支2
}else{
    分支3
}
```

```go
package main

import "fmt"

func main() {
	score := 85
	if score >= 90 {
		fmt.Println("A")
	} else if score >= 75 {
		fmt.Println("B")
	} else {
		fmt.Println("C")
	}
}


//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
//B

```

注意：if{}不能省略，if(){}后()可以省略,{要求与条件语句在同一行

## for循环

go语言中所有循环类型均可以实验for关键字来完成

```
for 初始语句;条件表达式;结束语句{
     循环体语句
}
条件表达式返回true时循环体不停地进行循环，直到条件表达式返回false时自动退出循环
```

```go
package main

import "fmt"

func main() {
	for i := 1; i <= 10; i++ {
		fmt.Println(i)
	}
}
```

for循环的初始语句可以被忽略，但是初始语句后的分号必须要写；注意死循环

```go
package main

import "fmt"

func main() {
	i := 1
	for {
		if i <= 10 {
			fmt.Println(i)
		} else {
			break
		}
		i++
	}
}
```

注意：go语言中没有while语句，可以使用for代替

### for循环嵌套

```go
package main

import "fmt"

func main() {
	row := 3
	column := 4
	for i := 0; i < row; i++ {
		for j := 0; j < column; j++ {
			fmt.Printf("* ")
		}
		fmt.Println("")
	}
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
* * * * 
* * * * 
* * * * 
```

```go
package main

import "fmt"

func main() {
	row := 4
	for i := 0; i < row; i++ {
		for j := 0; j < i; j++ {
			fmt.Printf("* ")
		}
		fmt.Println("")
	}
}

// [Running] go run "d:\Code\go\go_demo\demo1\main.go"

* 
* * 
* * * 

```

```go
package main

import "fmt"

func main() {
	for i := 0; i <= 9; i++ {
		for j := 1; j <= i; j++ {
			fmt.Printf("%v*%v=%v \t", i, j, i*j)
		}
		fmt.Println("")
	}
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"

1*1=1 	
2*1=2 	2*2=4 	
3*1=3 	3*2=6 	3*3=9 	
4*1=4 	4*2=8 	4*3=12 	4*4=16 	
5*1=5 	5*2=10 	5*3=15 	5*4=20 	5*5=25 	
6*1=6 	6*2=12 	6*3=18 	6*4=24 	6*5=30 	6*6=36 	
7*1=7 	7*2=14 	7*3=21 	7*4=28 	7*5=35 	7*6=42 	7*7=49 	
8*1=8 	8*2=16 	8*3=24 	8*4=32 	8*5=40 	8*6=48 	8*7=56 	8*8=64 	
9*1=9 	9*2=18 	9*3=27 	9*4=36 	9*5=45 	9*6=54 	9*7=63 	9*8=72 	9*9=81 	

```

## for range(键值循环)

go语言可以使用for range遍历数组、切片、字符串、map及通道(channel)，通过for range遍历的返回值有以下规律

1.数组、切片、字符串返回索引和值

2.map返回键和值

3.通道(channel)只返回通道内的值

```go
package main

import "fmt"

func main() {
	var arr = []string{"apple", "banana", "cherry"}
	for _, val := range arr {
		fmt.Println(val)
	}
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
apple
banana
cherry
```

## switch case

使用switch语句可方便的对大量的值进行条件判断

```go
package main

import "fmt"

func main() {
	var ext = ".go"
	switch ext {
	case ".go":
		fmt.Println("Go source file")
	case ".py":
		fmt.Println("Python source file")
	case ".js":
		fmt.Println("JavaScript source file")
	default:
		fmt.Println("Unknown file type")
	}
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
Go source file
```

```go
package main

import "fmt"

func main() {
	var score = "A"
	switch score {
	case "A", "B", "C":
		fmt.Println("及格")
	case "D":
		fmt.Println("不及格")
	}
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
及格
```

分支还可以使用表达式，这时候switch语句后面不需要再跟判断变量

```go
package main

import "fmt"

func main() {
	var age = 30
	switch {
	case age < 24:
		fmt.Println("好好学习")
	case age >= 24 && age < 60:
		fmt.Println("好好工作")
	default:
		fmt.Println("好好养老")
	}
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
好好工作
```

### 穿透 fallthrought

fallthrought语法可以执行满足条件的case的下一个case，是为了兼容c语言的case设计

```go
package main

import "fmt"

func main() {
	var age = 30
	switch {
	case age < 24:
		fmt.Println("好好学习")
		fallthrough
	case age >= 24 && age < 60:
		fmt.Println("好好工作")
		fallthrough
	default:
		fmt.Println("好好养老")
	}
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
好好工作
好好养老
```

## break

go语言中的break语句用于以下几个方面

* 用于循环语句中跳出循环，并开始执行循环之后的语句
* break再switch中执行一条case后跳出语句的作用(可以去掉)
* 在多重循环中，可以使用标号label标出break的循环

```go
package main

import "fmt"

func main() {
label:
	for i := 0; i < 2; i++ {
		for j := 0; j < 10; j++ {
			if j == 3 {
				break label
			}
			fmt.Printf("i=%d, j=%d\n", i, j)
		}
	}
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
i=0, j=0
i=0, j=1
i=0, j=2
```

## continue

continue语句可以结束当前循环，开始下一次的循环迭代过程，仅限在for循环中使用

在continue语句后添加标签时，表示开始标签对应循环

```go
package main

import "fmt"

func main() {
label:
	for i := 0; i < 2; i++ {
		for j := 0; j < 10; j++ {
			if j == 3 {
				continue label
			}
			fmt.Printf("i=%d, j=%d\n", i, j)
		}
	}
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
i=0, j=0
i=0, j=1
i=0, j=2
i=1, j=0
i=1, j=1
i=1, j=2
```

## goto(跳转到指定标签)

goto语句通过标签进行代码键的无条件转跳，goto语句可以在快速跳出循环、避免重复退出上有一定的帮助，go语言中使用goto语句能简化一些代码的实现过程

```go
package main

import "fmt"

func main() {
	var n = 30
	if n > 24 {
		fmt.Println("成年人")
		goto label3
	}
	fmt.Println("aaa")
	fmt.Println("bbb")
label3:
	fmt.Println("结束")
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
成年人
结束
```

# 数组

Array(数组)的介绍

数组是指一系列同一类型的数据的集合

数组的长度是类型的一部分

## 数组定义

```
var a [3]int
var 数组变量名 [元素数量]T
```

```go
package main

import "fmt"

func main() {
	var arr1 [5]int
	var arr2 = [3]string{"Go", "Python", "Java"}
	fmt.Printf("arr1: %T arr2: %T\n", arr1, arr2)
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
arr1: [5]int arr2: [3]string
```

go可以直接输出数组

```go
package main

import "fmt"

func main() {
	var arr [3]int
	arr[0] = 10
	arr[1] = 20
	arr[2] = 30

	fmt.Println(arr)
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
[10 20 30]
```

## 数组的初始化

```go
1.
var arr = [3]int
arr[0] = 1
arr[1] = 2
arr[2] = 3
2.
var arr = [3]int{23,34,56}
3.
var arr = [...]int{1,2}
4.
var arr = [...]int{0: 1, 1: 20, 5: 30}//最大下标
```

注意数组的长度

改变数组里的值

```go
var arr = [...]int{1,2}
arr[0] = 3
```

## 数组的循环遍历

for循环遍历数组

```go
package main

import "fmt"

func main() {
	var arr [3]int
	arr[0] = 10
	arr[1] = 20
	arr[2] = 30

	for i := 0; i < len(arr); i++ {
		fmt.Println(arr[i])
	}
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
10
20
30
```

for range循环

```go
package main

import "fmt"

func main() {
	var arr [3]int
	arr[0] = 10
	arr[1] = 20
	arr[2] = 30

	for k, v := range arr {
		fmt.Printf("arr[%d]=%d\n", k, v)
	}
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
arr[0]=10
arr[1]=20
arr[2]=30
```

## 多维数组

以二维数组为例

```
var 数组变量名 [元素数量][元素数量]T
var 数组变量名 [...][元素数量]T
```

# 值类型

基本数据类型和数组都是值类型

赋值和传参会复制整个数组，因此改变副本的值，不会改变本身的值

# 引用类型

切片

改变变量副本的值，会改变本身的值

# 切片

切片是一个拥有相同类型元素的可变长度的序列

切片是一个引用类型，他的内部结构包含地址、长度、和容量

```
var 变量名 []T
```

切片的使用基本和数组一样 

## 基于数组定义切片

由于切片的底层就是一个数组，所以我们可以基于数组定义切片

```go
package main

import "fmt"

func main() {
	a := [5]int{1, 2, 3, 4, 5}
	b := a[:] //获取全部
    //b := a[1:4] 左包右不包
	fmt.Printf("%T\n %T\n", b, a)
}

//Running] go run "d:\Code\go\go_demo\demo1\main.go"
[]int
 [5]int
```

## 切片的长度和容量

使用len()求长度，cap()求容量

长度就是元素的个数

容量就是从他的第一个元素到其底层数组元素末尾的个数

```go
package main

import "fmt"

func main() {
	a := []int{1, 2, 3, 4, 5}
	b := a[1:3]
	fmt.Printf("%v %v\n", len(a), cap(a))
	fmt.Printf("%v %v\n", len(b), cap(b))
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
5 5
2 4
```

切片的本质就是数组的封装

## 使用make()定义切片

如果需要动态创建一个切片，需要使用make()函数

```
make([]T,size,cap)
```

append()追加数据，扩容，还可以合并切片

```go
package main

import "fmt"

func main() {
	a := []int{1, 2, 3, 4, 5}
	b := []int{6, 7, 8, 9, 10}
	a = append(a, b...)
	fmt.Println(a)
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
[1 2 3 4 5 6 7 8 9 10]

```

## copy()

copy()可以复制切片数据而不受对方修改数据影响

## 删除元素

go中没有删除切片元素的专用方法，可以使用切片本身的特性来删除

```go
package main

import "fmt"

func main() {
	a := []int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
	a = append(a[:3], a[6:]...)
	fmt.Println(a)
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
[1 2 3 7 8 9 10]
```



# nil

当你声明了一个变量，但却还并没有赋值时，go中会自动给你的变量赋值一个默认零值。这是每种类型对应的零值

# map

go语言中的map是引用数据类型，必须初始化才能使用

```
map[KeyType]ValueType
```

KeyType表示键的类型

ValueType表示键对应的值的类型

map类型的变量的默认初始化的值为nil，需要使用make()函数来分配内存

```go
package main

import "fmt"

func main() {
	var userinfo = make(map[string]string)
	userinfo["name"] = "John Doe"
	userinfo["email"] = "john@example.com"
	userinfo["age"] = "30"

	fmt.Println(userinfo)
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
map[age:30 email:john@example.com name:John Doe]

```

```go
package main

import "fmt"

func main() {
	var userinfo = map[string]string{
		"name":    "Alice",
		"country": "Wonderland",
	}
	fmt.Println(userinfo)
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
map[country:Wonderland name:Alice]
```

## delete()

可以使用delete()删除map的数据

## 判断某个键是否存在

```
value, ok := map 对象[key]
```

# 函数

函数是组织好的、可重复使用的、用于执行指定任务代码的代码块

go语言支持函数、匿名函数和闭包

```
func 函数名(参数)(返回值类型){
    函数体
}
```

```go
package main

import "fmt"

func main() {
	result := sumFn(10, 20)
	fmt.Println("Sum is:", result)

}

func sumFn(a int, b int) int {
	return a + b
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
Sum is: 30
```

## 可变参数

可变参数是指函数的参数数量不固定，go语言中的可变参数通过在参数后面加...来表示

```go
package main

import "fmt"

func main() {
	sumFn1(1222, 2333, 3444)

}

func sumFn1(a ...int) {
	fmt.Printf("%v %T", a, a)
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
[1222 2333 3444] []int
```

## 多个返回值

```go
package main

import "fmt"

func main() {
	a, b := sumFn(10, 20)
	fmt.Println(a, b)

}

func sumFn(a int, b int) (sum int, sub int) {
	sum = a + b
	sub = a - b
	return sum, sub
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
30 -10
```

## 函数变量作用域

### 全局变量

全局变量是定义在函数外部的变量，他在程序整个运行周期内都有效，在函数中可以访问到全局变量

### 局部变量

局部变量是函数内部定义的变量，函数内定义的变量无法在该函数外使用

## 定义函数类型

```go
type calculation func(int,int) int
```

定义了一个calculation函数类型，凡是满足接收两个int并返回一个Int的都是calculation类型的函数

## 匿名函数

```
func(参数)(返回值){
     函数体
}
```

## 闭包

闭包可以理解成定义在一个函数内部的函数。本质上，闭包是将函数内部和外部连接起来的桥梁，或者说是函数和其他引用环境的组合体

```go
package main

import (
	"fmt"
)

func main() {
	var fn = adder()
	fmt.Println(fn(10))
    fmt.Println(fn(20))
	fmt.Println(fn(30))
}

func adder() func(y int) int {
	var i = 10 //常驻内存，不污染全局
	return func(y int) int {
		i += y
		return i
	}
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
20
40
70
```

## defer语句

go语言中的defer语句会将其后面跟随的语句进行延迟处理，在defer归属的函数即将返回时将延迟处理的语句按defer定义的逆序进行执行，也就是说，先被defer的语句最后被执行

```go
package main

import (
	"fmt"
)

func main() {
	f1()
}

func f1() {
	fmt.Println("开始")
	defer func() {
		fmt.Println("aaa")
	}()
	fmt.Println("结束")
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
开始
结束
aaa
```

![屏幕截图 2025-10-28 174516](../../../pictures/屏幕截图 2025-10-28 174516.png)

defer注册要延迟执行的函数时该函数所有的参数都需要确定其值

## panic/recover

go语言中没有异常机制，但是使用panic/recover模式来处理错误

panic可以在任何地方引发，但recover只在defer调用的函数中有效

```go
package main

import (
	"fmt"
)

func fn1() {
	fmt.Println("This is demo1 fn1")
}

func fn2() {
	defer func() {
		err := recover()
		if err != nil {
			fmt.Println("err", err)
		}
	}()
	panic("抛出一异常")
}

func main() {
	fn1()
	fn2()
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
This is demo1 fn1
err 抛出一异常
```

# 日期

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	timeObj := time.Now()
	fmt.Println("当前时间:", timeObj)
	year := timeObj.Year()
	month := timeObj.Month()
	day := timeObj.Day()
	fmt.Printf("当前日期: %d-%02d-%02d\n", year, month, day)
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
当前时间: 2025-10-28 18:26:43.0569626 +0800 CST m=+0.000000001
当前日期: 2025-10-28
```

事件类型有一个自带的方法Format进行格式化

需要注意的是go语言中格式化时间模板不是常见的Y-m-d H:M:S

而是使用go诞生的时间2006年1月2号15点04分

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	timeObj := time.Now()
	fmt.Println("当前时间:", timeObj)
	str := timeObj.Format("2006-01-02 03:04:05")//03表示12小时制15表示24小时制
	fmt.Println("格式化时间:", str)
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
当前时间: 2025-10-28 18:31:09.7929777 +0800 CST m=+0.000000001
格式化时间: 2025-10-28 06:31:09
```

## 获取当前时间戳

时间戳是自1970年1月1日8点整到当前时间的总毫秒数，它也被称为Unix时间戳

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	timeObj := time.Now()
	unixtime := timeObj.Unix()
	fmt.Println("当前时间的Unix时间戳为：", unixtime)
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
当前时间的Unix时间戳为： 1761647714
```

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	timeObj := time.Now()
	unixtime := timeObj.Unix()
	timeObj1 := time.Unix(unixtime, 0)
	str := timeObj1.Format("2006-01-02 15:04:05")
	fmt.Println("当前时间为：", str)
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
当前时间为： 2025-10-28 18:38:56
```

## 日期字符转时间戳

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	var str = "2024-06-15 14:30:00"
	var tmp = "2006-01-02 15:04:05"
	timeObj, _ := time.ParseInLocation(tmp, str, time.Local)
	fmt.Println(timeObj)
	fmt.Println(timeObj.Unix())
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
2024-06-15 14:30:00 +0800 CST
1718433000
```

# 指针

指针也是一个变量，但他是一个特殊的变量，他存储的数据不是一个普通的值，而是另一个变量的内存地址

指针是引用数据类型，使用需要初始化

```go
package main

import (
	"fmt"
)

func main() {
	a := 10
	p := &a
	fmt.Printf("p的值为%v p的类型为%T", p, p)
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
p的值为0xc00000a088 p的类型为*int(指针类型)
```

go语言使用&字符放在变量面前对变量进行取地址操作

```
ptr := &v 
```

指针也有地址值

```go
package main

import (
	"fmt"
)

func main() {
	a := 10
	p := &a
	fmt.Println(p)
	fmt.Println(*p)//*p表示a的值
	*p = 20
	fmt.Println(a)
}

[Running] go run "d:\Code\go\go_demo\demo1\main.go"
0xc00000a088
10
20
```

## new与make的区别

1.二者都是用来做内存分配的

2.make只用于slice、map、以及channel的初始化，返回的还是这三个应用类型本身

3.而new用于类型的内存分配，并且内存对应的值为类型零值，返回的指向类型的指针

# 结构体

go语言中没有类，go中的结构体和其他语言的类相似，结构体具有更高的扩展性和灵活性

go提供了一种自定义数据类型，可以封装多个数据类型，这种数据类型叫结构体，我们可以通过struct来定义自己的类型

```go
package main

import (
	"fmt"
)

type myInt int //自定义类型
type myFloat = float64 //类型别名

func main() {
	var a myInt = 10
	var b myFloat = 3.14
	fmt.Printf("a: %d,%T b: %.2f,%T\n", a, a, b, b)
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
a: 10,main.myInt b: 3.14,float64
```

结构体的定义

```
type 类型名 struct {
     字段名 字段类型
     ...
}
```

结构体属性首字母大写表示这个结构体是公有的，在其他包里可以使用，小写表示私有

结构体实例化

```
var 结构体实例 = new(结构体类型)
```

```
var 结构体实例 = &结构体类型{}
```

```
var 结构体实例 = 结构体类型{键值对}
```



```
var 结构体实例 结构体类型
```

```go
package main

import (
	"fmt"
)

type Person struct {
	name string
	age  int
}

func main() {
	var p1 Person
	p1.name = "Alice"
	p1.age = 30

	p2 := Person{name: "Bob", age: 25}

	fmt.Printf("Person 1: Name=%s, Age=%d\n", p1.name, p1.age)
	fmt.Printf("Person 2: Name=%s, Age=%d\n", p2.name, p2.age)
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
Person 1: Name=Alice, Age=30
Person 2: Name=Bob, Age=25
```

%#v打印实例全部信息

## 结构体方法和接收者

方法定义

```
func (接收者变量 接收者类型) 方法名(参数列表)(返回参数) {
     函数体
}
```

```go
package main

import (
	"fmt"
)

type Person struct {
	Name string
	Age  int
}

func (p Person) PrintInfo() {
	fmt.Printf("Name: %s, Age: %d\n", p.Name, p.Age)
}

func main() {
	var p1 = Person{Name: "Alice", Age: 30}
	p1.PrintInfo()

}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
Name: Alice, Age: 30
```

 

## 结构体的继承

```go
package main

import (
	"fmt"
)

type Animal struct {
	Name string
}

type Dog struct {
	Animal
	Age int
}

func (a Animal) PrintInfo() {
	fmt.Printf("%v在运动\n", a.Name)
}

func (d Dog) Wang() {
	fmt.Printf("%v在汪汪叫\n", d.Name)
}

func main() {
	var d Dog
	d.Name = "小黑"
	d.Age = 3
	d.Wang()
	d.PrintInfo()

}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
小黑在汪汪叫
小黑在运动
```

# JSON

JSON是一种轻量级的数据交换格式

```json
{
    "a": "Hello"
    "b": "World"
}
```

## 结构体与JSON字符串互转

属性必须大写

 使用json.Marshal转换

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Animal struct {
	Name string
}

type Dog struct {
	Animal
	Age int
}

func main() {
	var a Dog
	a.Name = "Buddy"
	a.Age = 5
	jsonByte, _ := json.Marshal(a)
	fmt.Println(string(jsonByte))

}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
{"Name":"Buddy","Age":5}
```

# 包

go中分为三种

1.系统内置包

2.自定义包

3.第三方包

## go mod init初始化项目

实际项目开发中首发我们项目目录中用go mod命令生成一个go.mod文件管理项目的依赖

比如我们的golang项目文件要放在itying这个文件夹，这个时候我们需要itying文件夹里面使用go.mod命令生成一个go.mod文件

```
go mod init 文件名
```

init函数优先于main函数执行

## 第三方包

我们可以在https://pkg.go.dev/查找看常见的第三方包

初始化

```
go mod init 项目名
```

下载

```
go get github.com/shopspring/包名
go install github.com/shopspring/包名@latest
```

1.go mod init 项目名称

2.配置第三方包

3.go mod tidy

4.运行项目

# 接口

go中的接口是一种抽象数据类型，go中接口定义了对象的行为规范，只定义规范不实现，接口中定义的规范由具体的对象来实现

```
type 接口名 interface{
    方法名1(参数列表1)返回值列表1
    ...
}
```

 

```go
package main

import "fmt"

type Usber interface {
	Start()
	Stop()
}

// 如果接口里有方法，必须通过结构体或者通过自定义类型实现这些方法，才能赋值给接口变量
type Phone struct {
	Name string
}

func (p Phone) Start() {
	fmt.Println(p.Name, "Phone Start")
}
func (p Phone) Stop() {
	fmt.Println(p.Name, "Phone Stop")
}

func main() {
	u := Phone{Name: "iPhone"}
	u.Start()
	u.Stop()
}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
iPhone Phone Start
iPhone Phone Stop
```

## 空接口

表示没有任何约束，任意类型都能实现空接口

空接口也可以直接当做类型来使用，可以表示任意类型

空接口可以表示函数类型

如果想要判断空接口的类型，可以使用类型断言

```
x.(T)
```

```go
package main

import "fmt"

type A interface{}

func main() {
	var a A
	a = 42
	v, ok := a.(string)
	if ok {
		fmt.Println("a is a string:", v)
	} else {
		fmt.Println("a is not a string")
	}

}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
a is not a string

```

用于定义一个方法，可以传入任意数据类型，然后根据不同的类型实现不同的功能

x.(type)判断以后给变量的类型，这个语句只能用在swicth语句中

## 值接收者和指针接收者

值接收者：如果结构体中的方法是值接收者，那么实例化后的结构体值类型和结构体指针类型都可以被赋值给看接口变量

 指针接收者：如果结合体中的方法是指针接收者，那么实例化后结构体指针类型都可以赋值给接口变量，结构体值类型没法赋值给接口变量

# goroutine channel实现并发和并行

进程：进程就是一个正在执行的程序

线程：线程是进程的一个执行实例，是程序执行的最小单元，他是比进程更小的能独立运行的基本单位

一个进程可以创建多个线程，同一个进程的多个线程可以并发执行，一个程序要运行的化至少有一个进程

并发：多个线程同时竞争一个位置，竞争到的才可以执行，每一个时间段只有一个线程在执行

并行：多个线程可以同时执行，每一个时间段，可以由多个线程同时执行

在一个go程序的主线程序上可以起多个协程，go中多协程可以实现并行和并发

协程占用内存非常小

```
go 结构体()//表示开启一个协程
```

主线程结束协程也会结束

使用sync.WaitGroup

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

var wg sync.WaitGroup

func test() {
	for i := 0; i < 10; i++ {
		fmt.Println("Goroutine:", i)
		time.Sleep(time.Millisecond * 100)
	}
	wg.Done()
}

func main() {
	wg.Add(1)
	go test()
	for i := 0; i < 10; i++ {
		fmt.Println("Main:", i)
		time.Sleep(time.Millisecond * 50)

	}
	wg.Wait()

}

//[Running] go run "d:\Code\go\go_demo\demo1\main.go"
Main: 0
Goroutine: 0
Main: 1
Main: 2
Goroutine: 1
Main: 3
Main: 4
Goroutine: 2
Main: 5
Goroutine: 3
Main: 6
Main: 7
Goroutine: 4
Main: 8
Main: 9
Goroutine: 5
Goroutine: 6
Goroutine: 7
Goroutine: 8
Goroutine: 9
```

## channel管道

管道是go在语言级别上提供的goroutine间的通讯方式，我们使用channel在多个goroutine之间传递消息

go的并发模型是CSP，提倡通过通信共享内存而不是共享内存而实现通信

管道是一种特殊的类型，管道像一个传送带活着队列，总是遵循先入先出的规则，保证收发数据的顺序，每一个管道都是一个具体类型的管道，也就是声明channel的时候需要为其指定元素类型

```
var 变量 chan 元素类型
```

创建channel

```
make(chan 元素类型，容量)
ch := make(chan int,3)
```

发送(将数据放在管道中)

```
ch <-10 //把10发送到ch中
```

接收(从管道内取值)

```
x := <- ch //从ch中接收值并赋值给变量x
<-ch //从ch中接收值，忽略结果
```

关闭管道

## 互斥锁

sync.Mutex类型只有两个公开的指针方法，Lock和Unlock。Lock锁定当前的共享资源，Unlock进行解锁

