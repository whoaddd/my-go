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

