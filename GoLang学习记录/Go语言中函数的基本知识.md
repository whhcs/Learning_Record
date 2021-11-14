@[TOC](目录)

## Go语言中函数的基本知识

在Go语言中，函数是一等公民，这意味着函数不但可以用于封装代码、分割功能、解耦逻辑，还可以化身为普通的值，在其他函数间传递、赋予变量、做类型判断和转换等。

Go语言函数支持的特性：

1. 参数数量不固定（可变参数）。
2. 匿名函数及其闭包。
3. 函数本身作为值传递。
4. 函数的延迟执行。
5. 把函数作为接口调用。

&nbsp;

### 函数变量

Go语言中，函数也是一种类型，我们可以将其保存在变量中。

```go
var 变量名称 func()
```

```go
package main

import "fmt"

func addSub(x int, y int) (sum int, sub int) {
	sum = x + y
	sub = x - y
	return sum, sub
}

func main() {
	a := 1
	b := 2
	f1 := addSub
	//或 var f1 func(x int, y int) (sum int, sub int)
	//f1 = addSub
	sum, sub := f1(a, b)
	fmt.Println(a, "+", b, "=", sum)
	fmt.Println(a, "-", b, "=", sub)
}
```

函数变量可以用短变量格式进行声明和初始化。

或者函数变量f1声明后其值初始化为nil，再将addSub函数赋值给f1，所有对f1的调用即为对addSub函数的调用。

&nbsp;

### 多返回值函数

* 在GO语言中每个函数声明时都可以定义成多返回值函数

* Go语言中所有的错误都是通过返回值返回的

  ```GO
  func demo() (string, int) {
  	return "smallming", 17
  }
  
  func main() {
  	//不接收函数返回值，表示执行函数
  	demo()
  
  	//每个返回值都接收
  	a, b := demo()
  	fmt.Println(a, b)
  
  	//不希望接收的返回值使用下划线占位
  	c, _ := demo()
  	fmt.Println(c)
  }
  ```

&nbsp;

### 可变参数

#### 可变参数函数的使用

Go语言支持可变参数的特性，即函数声明时可以没有固定数量的参数。格式如下：

```go
func 函数名(固定参数列表，v ... T) (返回参数列表) {
	函数体
}
```

1. 固定参数列表可以省略
2. `v ... T`代表的是变量v，类型为T的`切片`。

```go
func sum(numbers ...int) int {
	s := 0
	for i := range numbers {
		s += numbers[i]
	}
	return s
}
func main() {
	fmt.Println(sum(1, 2, 3, 4, 5)) // 15
}
```

&nbsp;

#### 可变参数与内置函数

Go语言中许多内置函数的参数都用了可变参数，比如`fmt`包中的`Println`函数和`Printf`函数。

```go
func Println(a ...interface{}) (n int, err error) {
	return Fprintln(os.Stdout, a...)
        // 可变参数本质上是一个切片，如果要在多个函数中传递可变参数，可在传递时添加"..."
}

func Printf(format string, a ...interface{}) (n int, err error) {
    return Fprintf(os.Stdout, format, a...)
}
```

&nbsp;

### 匿名函数和闭包

#### 定义并调用匿名函数

匿名函数即在需要函数时定义函数，匿名函数能以变量方式传递，它常常被用于实现闭包。

匿名函数的调用方式有两种：

1. 定义并同时调用匿名函数，可以在匿名函数后添加“()”直接传入实参：

   ```go
   	func(data string) {
   		fmt.Println("Hello " + data)
   	}("world!")
   ```

2. 将匿名函数赋值给变量，之后再调用：

   ```go
   	f1 := func(data string) {
   		fmt.Println("Hello " + data)
   	}
   	f1("world!")
   ```

&nbsp;

#### 闭包

闭包就是包含了自由变量的匿名函数，其中自由变量即使已经脱离了原有的自由变量环境也不会被删除，在闭包的作用域内可以继续使用这个自由变量。

闭包可对作用域内变量的引用进行修改，在闭包内成功修改变量值后会对实际的值产生修改。

```go
func addOne(i int) func() int {
	return func() int {
		i++
		return i
	}
}

func main() {
	a1 := addOne(0)
	fmt.Println(a1())	// 1
	fmt.Println(a1())	// 2
	a2 := addOne(10)
	fmt.Println(a2())	// 11
	fmt.Print("a1闭包的地址为：")
	fmt.Printf("%p\n", &a1)
	fmt.Print("a2闭包的地址为：")
	fmt.Printf("%p\n", &a2)
	// a1闭包的地址为：0xc000006028
	// a2闭包的地址为：0xc000006038
}
```

两个闭包实例的地址完全不同，两个闭包的调用结果互不影响。

&nbsp;

### 延迟执行语句

Go语言中存在一种由关键字defer标识的延迟执行语句:

```go
defer 任意语句
```

defer后的语句不会被马上执行，在defer所属的函数即将返回时，函数体中所有defer语句将会按出现的顺序被逆序执行。

由于defer语句是在当前函数即将返回时被调用，所以defer常常被用来释放资源。

&nbsp;

### Go内置函数

![image-20211112194124193](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20211112194124193.png)

&nbsp;

### 结尾

Go语言中，函数是一等公民，这是函数式编程的重要特征。Go语言在语言层面支持了函数式编程。关于函数式编程我仅仅了解了一点点，还不是很能理解函数式编程的思想，随着后面慢慢接触研究了解之后再单独写一篇博客吧。

