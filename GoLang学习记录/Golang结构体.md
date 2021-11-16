# 结构体及其方法

## 结构体

@[TOC](目录)

### 定义结构体

Go语言中通过关键字type定义自定义类型，结构体定义需要使用type和struct关键字，格式如下：

```go
type 结构体名 struct {
	成员变量1 类型1
	成员变量2 类型2
	成员变量3 类型3
	...
}
```

&nbsp;

### 实例化结构体

一个结构体在定义完成后，才能进行使用。结构体实例化时，会真正地分配内存。

Go语言实例化结构体主要有以下三种方式：

1. 标准实例化
2. new函数实例化
3. 取地址实例化

```go
type Book struct {
    title string
    author string
   	id int
}

func main () {
    // 标准实例化
    var book1 Book	
    // new函数实例化，实例化完成后会返回结构体地指针类型
    book2 := new(Book)
    // 取地址实例化返回的也是结构体指针类型
    book3 := &Book{}
}
```

&nbsp;

### 初始化结构体

1. 键值对格式初始化

   ```go
   结构体实例 := 结构体类型{
   	成员变量1:值1,
   	成员变量2:值2,
   	成员变量3:值3,
   }
   ```

2. 列表格式初始化

   ```go
   结构体实例 := 结构体类型{
   	值1,
   	值2,
   	值3,
   }
   ```

   使用这种方式初始化结构体必须初始化所有的成员变量。

&nbsp;

### 结构体方法

Go语言中，一个方法就是一个包含了接收者的函数。对于结构体方法，接收者可以是结构体类型的值或指针。

1. 指针类型接收者：当接收者类型为指针时，可以通过该方法改变该接收者的成员变量值，即使你使用了非指针类型的实例调用该函数，也可以改变实例对应的成员变量值。
2. 值类型接收者：该方法操作对应接收者值得副本，即使你使用了指针类型的实例调用该函数，也无法改变成员变量的值。

&nbsp;

### 结构体内嵌

Go语言的结构体内嵌式一种组合特性，使用结构体内嵌可构建一种面向对象编程思想中的继承关系。

结构体实例化后，可直接访问内嵌结构体的所有成员变量和方法。

```go
type Book struct {
	title string
	author string
	num int
	id int
}

type BookBorrow struct {
	Book	// 继承了Book的基本属性和方法
	borrowTime string
}

type BookNotBorrow struct {
	Book	// 继承了Book的基本属性和方法
	readTime string
}

// 初始化结构体内嵌
func main() {
	bookBorrow := &BookBorrow {
		Book:Book{
			"Go语言",
			"Tom",
			20,
			152368,
		},
		borrowTime:"30",
	}
}
```

