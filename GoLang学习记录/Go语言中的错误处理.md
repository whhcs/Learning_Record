@[TOC](目录)

# Go语言中的错误处理与资源管理

## defer调用实现资源管理

defer关键字允许我们将某个语句或函数延迟到函数返回之前才发生，常用于释放某些已分配的资源。

### defer特点

1. 能够确保调用在函数结束时发生

2. 参数在defer语句时计算

3. defer列表为后进先出，类似栈。

   ```go
   for i := 0; i <= 30; i++ {
       defer fmt.Println(i)
   } // 打印30到0
   ```

&nbsp;

### 何时使用defer调用

1. Open/Close
2. Lock/Unlock
3. PrintHeader/PrintFooter

&nbsp;

### defer和return结合

* defer与return同时存在时，要把return理解成两条执行指令的结合（不是原子指令），一个指令是给返回值赋值，另一个指令是跳出该函数。
* defer和return同时存在时，整体执行顺序如下
  * 先给返回值赋值
  * 然后执行defer
  * 最后跳出函数

没有定义返回值接收变量，执行defer时返回值已经赋值

```go
func f() int { // 没有定义返回值接收变量
	i := 0
	defer func() {
		i = i + 2
	}()
	return i
}

func main() {
    fmt.Println(f()) // 0
}
```

&nbsp;

声明接收返回值变量，执行defer语句时修改了返回值内容

```go
func f() (i int) { // 没有定义返回值接收变量
	defer func() {
		i = i + 2
	}()
	return i
}

func main() {
    fmt.Println(f()) // 2
}
```

&nbsp;

## 错误处理

我们在编写程序时，为了加强程序的健壮性，往往会考虑到对程序中可能出现的错误和异常进行处理。

### 错误类型

- Go语言中使用`builtin`包下error接口作为错误类型，error接口只包含了一个方法，返回值是string，表示错误信息。官方源码定义如下：

  ```go
  // The error built-in interface type is the conventional interface for
  // representing an error condition, with the nil value representing no error.
  type error interface {
  	Error() string
  }
  ```

- Go语言设计者认为类似`try-catch-finally`的传统异常处理机制很容易造成开发者对异常机制的滥用，从而使代码结构变得很混乱。因此，Go语言中会使用多值返回来返回错误。

- 在Go语言标准库的errors包中提供了error接口的实现结构体`errorString`，还额外提供了快速创建错误的函数。

  ```go
  package errors
  
  // New returns an error that formats as the given text.
  // Each call to New returns a distinct error value even if the text is identical.
  func New(text string) error {
  	return &errorString{text}
  }
  
  // errorString is a trivial implementation of error.
  type errorString struct {
  	s string
  }
  
  func (e *errorString) Error() string {
  	return e.s
  }
  ```

- 如果错误信息由很多变量组成，可以借助`fmt.Errorf(format string, a ...interface{})`完成错误信息格式化，因为底层还是`errors.New()`

  ```go
  // Errorf formats according to a format specifier and returns the string
  // as a value that satisfies error.
  func Errorf(format string, a ...interface{}) error {
  	return errors.New(Sprintf(format, a...))
  }
  ```

&nbsp;

### 自定义错误

1. 使用errors包的New函数来创建自定义错误：

   ```go
   	err := errors.New("this is an error")
   	var err2 error
   	fmt.Println(err.Error())
   	fmt.Println(err2) // error的类型变量在声明后其默认值为nil
   ```

   执行结果：

   > this is an error
   > <nil>

2. 使用fmt包中的`Errorf`函数来设置错误内容的格式。

   ```go
   	if _, _, line, ok := runtime.Caller(0); ok { // 错误所在位置
   		err := fmt.Errorf("***Line %d error***", line)
   		fmt.Println(err)
   	}
   ```

   在程序中，我们通过调用runtime包的Caller方法获取当前代码所在行数，用来模拟错误的产生。

&nbsp;

### 错误处理的方式

```go
	file, err := os.Open("abc.txt")
	if err != nil {
		if pathError, ok := err.(*os.PathError); ok {
			fmt.Println(pathError.Err)
		} else {
			fmt.Println("unknown error", err)
		}
	} else {
		fmt.Println(file)
	}
```

执行上面的函数，执行结果如下：

> The system cannot find the file specified.

由于源代码所在文件下没有abc.txt文件，系统未找到指定的文件，因此使用`os.Open`函数打开失败。

使用if处理错误，这就是Go语言编程中最常见的错误处理方式之一。

&nbsp;

## Go语言宕机

`panic`方法是Go语言的一个内置函数。`panic`有点类似其他编程语言的throw，抛出异常。当执行到panic后，停止当前函数的执行，一直向上返回，执行每一层的defer，如果没有遇见recover，程序退出，并打印错误栈信息。

panic的源码如下：

```go
func panic(v interface{})
```

我们可以传入任意类型的值作为宕机内容。

一般而言，只有当程序发生不可逆的错误时，才会使用`panic`方法来触发宕机。如果遇到以下情形，可以调用panic方法来退出程序：

- 程序处于失控状态且无法恢复，继续执行将影响其他正常程序，引发操作系统异常甚至是死机。
- 发生不可预知的错误。

&nbsp;

### defer和panic

在使用panic方法触发宕机后，且在退出函数前，会调用延迟执行语句defer。

```go
func protect() e
	defer func() {
		fmt.Println("func protect exit")
	}()
	panic("Serious bug") // 触发宕机
}

func main() {
	defer func() {
		fmt.Println("func main exit")
	}()
	protect()
	fmt.Println("Invalid code")
}
```

上面代码执行结果如下：

![image-20211212161717193](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20211212161717193.png)

以上程序执行流程如下：

1. protect函数内的panic触发宕机。
2. 由于protect函数内的匿名函数通过defer语句延迟执行，在panic方法触发宕机后，且在退出protect函数前，会执行protect函数中的匿名函数，打印”func protect exit"。
3. 由于main函数内的匿名函数通过defer语句延迟执行，在main函数退出前会执行main函数中的匿名函数，打印"func main exit"。
4. 程序退出。

&nbsp;

## 宕机恢复

### recover捕获宕机

Go语言通过内置函数recover来捕获宕机，类似于其他编程语言中的`try-catch`机制。recover源码如下：

```go
func recover() interface{}
```

由于defer语句延迟执行的特性，我们可以通过“defer语句+匿名函数+recover方法”来完成对宕机的捕获。如果没有panic信息返回nil，如果有panic，recover会把panic状态取消，恢复程序的正常运行。

```go
func protect() {
	defer func() {
		if err := recover(); err != nil {
			fmt.Println(err)
		}
	}()
	panic("Serious bug")
}

func main() {
	protect()
	fmt.Println("Invalid code")
}
```

程序的执行结果如下：

![image-20211212165753140](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20211212165753140.png)

&nbsp;

### 函数调用过程中panic和recover

```go
func demo1() {
	fmt.Println("demo1上半部分")
	demo2()
	fmt.Println("demo1下半部分")
}

func demo2() {
	defer func() {
		recover() // 此处进行恢复
	}()
	fmt.Println("demo2上半部分")
	demo3()
	fmt.Println("demo2下半部分")
}

func demo3() {
	fmt.Println("demo3上半部分")
	panic("在demo3出现了panic")
	fmt.Println("demo3下半部分")
}

func main() {
	fmt.Println("程序开始")
	demo1()
	fmt.Println("程序结束")
}
```

以上程序的执行结果如下：

![image-20211212170615982](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20211212170615982.png)

- recover()只能恢复当前函数级或当前函数调用函数中的panic()，恢复后当前级函数结束，但是调用此函数的函数可以继续执行。
- panic会停止当前函数的执行，然后一直向上返回，如果没有遇见recover，程序退出，并打印错误栈信息。
