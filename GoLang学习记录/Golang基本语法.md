@[TOC](目录)

# Golang基本语法

## 基本变量与类型

### 变量声明及赋值



1. 声明变量的一般形式是使用`var`关键字：

   ```go
   var 变量名 变量类型
   ```

2. 也可以使用`var()`批量声明变量：

   ```go
   var (
   	变量名1 变量类型1
       变量名2 变量类型2	
   )
   ```

3. 还可以使用短变量声明并初始化：

   ```go
   var a int := 3
   ```

   短变量声明并初始化只能在函数内使用。

4. 变量类型省略后，Go编译器在编译时会根据等号右边表达式推导变量的类型。

   ```go
   var a = 15
   str := "Hello"
   ```

5. 使用短变量给多个变量赋值时，必须要保证至少有一个变量是没有声明过的。

   ```go
   func main() {
   	var (
   		a = 1
   		b = true
   		c = "yyds"
   	)
   	// 短变量操作多个值时要保证里面至少有一个新变量
   	b, c, d := false, "aaaa", 3
   	fmt.Println(a, b, c, d)
   }
   ```
   
6. GO语言要求变量声明后至少要使用一次（赋值不属于使用）。

&nbsp;

当一个变量被声明后，系统会自动赋予它该类型的零值，即所有的内存在Go中都是经过初始化的。

1. 整型和浮点型的默认值为0
2. string为空字符串
3. bool为false
4. 指针为nil

&nbsp;

### 内建变量类型

1. bool、string
2. (u)int、(u)int8、(u)int16、(u)int32、(u)int64、uintptr
3. byte、rune
4. float32、float64、complex64、complex128


&nbsp;

#### 整型

1. 有符号整型：int8、int16、int32、int64。
2. 无符号整型：uint8、uint16、uint32、uint64。

有符号整型其二进制最高位存储符号，因此两者的区别就是无符号整型可以存放的正数范围比有符号整型中的整数范围大一倍。

1. 有符号整数范围：$${-2}^{n-1}$$ 到 $$2^{n-1}-1$$
2. 无符号整数范围：0 到 $$2^n-1$$

|    类型 | 取值范围                                                     |
| ------: | :----------------------------------------------------------- |
|    int8 | [-128 , 127]                                                 |
|   int16 | [-32768 , 32767]                                             |
|   int32 | [-2147483648 , 2147483647] Go语言中没有字符类型,所有字符都使用int32存储 |
|   int64 | [-9223372036854775808 , 9223372036854775807]                 |
|     int | 受限于计算机系统,系统是多少位,int为多少位                    |
|   uint8 | [0 , 255]                                                    |
|  uint16 | [0 , 65535]                                                  |
|  uint32 | [0 , 4294967295]                                             |
|  uint64 | [0 , 18446744073709551615]                                   |
|    uint | 受限于计算机系统,系统是多少位,uint为多少位                   |
|    rune | 与int32类似,常用在获取值的Unicode码                          |
|    byte | 与uint8类似.强调值为原始数据.一个字节占用8个二进制           |
| uintptr | 大小不确定,类型取决于底层编程                                |

&nbsp;

##### 强制类型转换

* Go语言是静态类型语言，并且不具备低精度向高精度自动转换的功能，所以不同类型变量之间相互赋值需要进行类型转换。

```go
func triangle() {
	var a, b int = 3, 4
	var c int
	c = int(math.Sqrt(float64(a*a + b*b)))
	fmt.Println(c)
}
```

&nbsp;

##### 不同进制整数

* Go语言支持八进制、十进制、十六进制创建整型，但不支持二进制值，最后由系统转换为十进制。

  ```go
  func main() {
  	//默认表示十进制
  	d := 17
  	
  	//0开头表示八进制
  	o := 021
  	
  	//0x开头表示十六进制
  	x := 0x11
  	
  	//e2表示10的2次方
  	e := 11e2
  	
  	//输出
  	fmt.Println(d, o, x, e)
  	
  	//把变量d中内容转换为二进制
  	b := fmt.Sprintf("%b", d)
  	fmt.Println(b)
  }
  ```


&nbsp;

#### 浮点型

1. 一个整数数值可以赋值给浮点类型，但是一个整型变量不可以赋值给浮点类型。

2. 浮点数取值范围：

   |  类型   |                    取值范围                    |
   | :-----: | :--------------------------------------------: |
   | float32 |  3.40282346638528859811704183484516925440e+38  |
   | float64 | 1.797693134862315708145274237317043567981e+308 |

3. 可以通过math快速获取浮点数的最大值：

   ```go
   package main
   
   import (
   	"fmt"
   	"math"
   )
   
   func main() {
   	fmt.Println(math.MaxFloat32)
   	fmt.Println(math.MaxFloat64)
   }
   ```

&nbsp;

#### 布尔类型

1. 布尔类型占用1个byte。

2. 可以使用unsafe包下的Sizeof()查看类型占用字节大小。

   ```go
   func main() {
   	a := false
   	fmt.Println(unsafe.Sizeof(a))
   }
   ```

3. 虽然bool类型占用一个byte，但是bool不能和byte或int8相互转换。

   ```go
   func main() {
   	var a int8 = 1
   	var b byte = 0
   	var c bool = false
   	fmt.Println(a, b, c)
   	a = int8(c) //cannot convert c (type bool) to type int8
   	b = byte(c) //cannot convert c (type bool) to type byte
   	c = bool(a) //cannot convert a (type int8) to type bool
   	c = bool(b) //cannot convert b (type byte) to type bool
   	b = byte(a) //可以
   }
   ```


&nbsp;

### 常量与枚举(iota)

1. 常量是一个固定值，在编译期就确定结果，声明时必须赋值且结果不可以不改变。

2. 常量定义完可以不使用。

3. 定义常量时如果省略类型，可以直接参与运算。

   ```go
   func consts() {
   	const (
   		filename = "abc.txt"
   		a, b     = 3, 4
   	)
   	var c int
   	c = int(math.Sqrt(a*a + b*b))	// 此时Sqrt内无需强制类型转换
   	fmt.Println(filename, c)
   }
   ```

4. 常量的值可以是表达式，但不可以出现变量。

5. 定义常量组时，后一个常量如果没有提供初始值，表示将使用上一行的表达式。

   ```go
   func main() {
   	const (
   		a = 1
   		b
   		c
   	)
   	fmt.Println(a,b,c)//输出:1 1 1
   }
   ```

6. iota在const关键字出现时被重置为0，const中每新增一行常量声明，将使iota进行一次计数。

   ```go
   func main() {
   	const (
   		a = 5    //iota=0
   		b = 3    //iota=1
   		c = iota //iota=2
   		d        //iota=3
   	)
   	fmt.Println(a, b, c, d) //输出5 3 2 3
   
   	const (
   		e = iota //iota=0
   		f        //iota=1
   		g = 10   //iota=2
   		h        //iota=3
   		i = iota //iota=4
   		j        //iota=5
   	)
   	fmt.Println(e, f, g, h, i, j) // 0 1 10 10 4 5
   }
   ```

&nbsp;

### 条件判断

* if的条件里可以声明变量，这个变量的作用域就在这个if语句里。

  ```go
  	const filename = "abc.txt"
  	if contents, err := ioutil.ReadFile(filename); err != nil {
  		fmt.Print(err)
  	} else {
  		fmt.Printf("%s\n", contents)
  	}
  ```
  
* switch也支持在条件位置定义变量，变量有效范围为当前switch

  ```go
  func main() {
  	switch num := 16; num {
  	case 2:
  		fmt.Println("2进制")
  	case 8:
  		fmt.Println("8进制")
  	case 10:
  		fmt.Println("10进制")
  	case 16:
  		fmt.Println("16进制")
  	default:
  		fmt.Println("内容不正确")
  	}
  	fmt.Println("程序结束")
  }
  ```

  * default上下位置没有影响，当且仅当所有case都不成立时才执行default

* 当条件是范围而不是固定值时

  ```go
  func main() {
  	score := 71
  	switch {
  	case score >= 90:
  		fmt.Println("优秀")
  	case score >= 80:
  		fmt.Println("良好")
  	case score >= 70:
  		fmt.Println("中等")
  	case score >= 60:
  		fmt.Println("及格")
  	default:
  		fmt.Println("不及格")
  	}
  	fmt.Println("程序结束")
  }
  ```

* case条件支持多个值,每个值使用逗号分开

  ```go
  func main() {
  	month := 5
  	switch month {
  	case 1, 3, 5, 7, 8, 10, 12:
  		fmt.Println("31天")
  	case 2:
  		fmt.Println("28或29天")
  	default:
  		fmt.Println("30天")
  	}
  	fmt.Println("程序结束")
  }
  ```

* switch结构中最多只能执行一个case，会自动break，使用fallthrough可以让下一个case/default继续执行。

  ```go
  func main() {
  	switch num := 1; num {
  	case 1:
  		fmt.Println("1")
  		fallthrough
  	case 2:
  		fmt.Println("2")
  	case 3:
  		fmt.Println("3")
  		fallthrough
  	case 4:
  		fmt.Println("4")
  	default:
  		fmt.Println("不是1,2,3,4")
  	}
  	fmt.Println("程序结束")
  }
  ```

