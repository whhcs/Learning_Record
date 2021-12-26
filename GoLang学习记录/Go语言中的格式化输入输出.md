# Golang中的格式化输入输出

## 打印输出

在Go语言中有多种输出方式，不同的输出适用场景不同。归纳起来有三种，每种还分为3种方式(原内容、原内容+ln、原内容+f)

- `PrintXX()`
- `FprintXX()`
- `SprintXX()`

&nbsp;

### FprintXX

- FprintXX在Go Web中使用比较多，把内容写到响应流中。

- 以Fprintln()为例，源码如下：

  ```go
  // Fprintln formats using the default formats for its operands and writes to w.
  // Spaces are always added between operands and a newline is appended.
  // It returns the number of bytes written and any write error encountered.
  func Fprintln(w io.Writer, a ...interface{}) (n int, err error) {
  	p := newPrinter()
  	p.doPrintln(a)
  	n, err = w.Write(p.buf)
  	p.free()
  	return
  }
  ```

  - 函数参数中第一个参数是输出流，后面参数是内容，表示把内容写入到输出流中

  - 第一个返回值表示输出内容的长度（字节数）

    - Fprintln()输出后会添加换行符，所以长度比内容多1个

    - Fprintln()源码中p.doPrintln(a)的源码如下：

      ```go
      // doPrintln is like doPrint but always adds a space between arguments
      // and a newline after the last argument.
      func (p *pp) doPrintln(a []interface{}) {
      	for argNum, arg := range a {
      		if argNum > 0 {
      			p.buf.writeByte(' ')
      		}
      		p.printArg(arg, 'v')
      	}
      	p.buf.writeByte('\n')
      }
      ```

  - FprintXX()支持下面三种方式

    - os.Stdout表示控制台输出流

    ```go
    func main() {
    	fmt.Fprint(os.Stdout, "内容1")        // 向流中写入内容，多个内容之间没有空格
    	fmt.Fprintln(os.Stdout, "内容2")      // 向流中写入内容额外写如换行符，多个内容之间有空格分割
    	fmt.Fprintf(os.Stdout, verb, "内容3") // 根据verb格式向流中写入内容
    }
    ```


&nbsp;

### PrintXX

- 以Println()举例，源码如下：

  ```go
  // Println formats using the default formats for its operands and writes to standard output.
  // Spaces are always added between operands and a newline is appended.
  // It returns the number of bytes written and any write error encountered.
  func Println(a ...interface{}) (n int, err error) {
  	return Fprintln(os.Stdout, a...)
  }
  ```

  - 可以看到Println()底层实际是Fprintln()
  - Println()把结果打印到控制台，返回内容长度和错误。

- PrintXX支持下面三种方式

  ```go
  func main() {
  	fmt.Print("内容", "内容")       // 输出内容后不换行
  	fmt.Fprintln("内容", "内容")   // 输出内容后换行
  	fmt.Fprintf(verb, "内容") // 根据verb输出指定格式内容
  }
  ```

&nbsp;

### SprintXX

- 以Sprintln()举例，源码如下：

  ```go
  // Sprintln formats using the default formats for its operands and returns the resulting string.
  // Spaces are always added between operands and a newline is appended.
  func Sprintln(a ...interface{}) string {
  	p := newPrinter()
  	p.doPrintln(a)
  	s := string(p.buf)
  	p.free()
  	return s
  }
  ```

  - Sprintln()把形成结果以字符串形式返回，并没有打印到控制台。
  - 从严格意义讲SprintXX不是打印输出，而更像字符串转换。

- SprintXX支持下面三种写法

  ```go
  func main() {
  	fmt.Sprint("内容1", "内容2")
  	fmt.Sprintln("内容2")
  	fmt.Sprintf(verb, "内容3")
  }
  ```

&nbsp;

## 常用转义字符汇总

| verb     |            含义            |
| -------- | :------------------------: |
| %d       |         十进制整数         |
| %x,%X    | 大小写方式显示十六进制整数 |
| %o       |         八进制整数         |
| %b       |         二进制整数         |
| %f,%g,%e |           浮点数           |
| %t       |           布尔值           |
| %c       |            字符            |
| %s       |           字符串           |
| %q       |    输出带双引号的字符串    |
| %v       |      对应值的默认格式      |
| %T       |            类型            |
| %p       |          内存地址          |
| %%       |           字符%            |
| \n       |            换行            |
| \t       |            缩进            |

&nbsp;

## 接收用户输入

程序运行时，运行到接收用户输入语句，程序阻塞，用户在控制台输入内容后，把内容赋值给对应的变量，程序继续运行。

### 接收用户输入的几种方式

#### 使用Scanln(&变量名, &变量名)的方式接收

- 输入的内容必须都在同一行
- 每个内容之间使用空格分割
- 回车换行后表示停止输入
  - 如果希望接收3个值，而在控制台只输入2个值，回车后也停止接收
  - 如果希望接收2个值，而在控制台输入3个值，回车后只能接收两个值

```go
func main() {
	var name, age string
	fmt.Print("请输入姓名和年龄: ")
	fmt.Scanln(&name, &age) // 此处&变量名是地址
	fmt.Println("接收到的内容为: ", name, age)
}
```

&nbsp;

#### 使用Scanf(verb, &变量名)按照指定的格式进行输入

```go
func main() {
	var a, b string
	fmt.Scanf("%s\n%s", &a, &b)
	fmt.Printf("%s\n%s", a, b)
}
```

上面程序中，每次换行输入一个内容。

如果需要同行输入两个字符串，中间使用空格分割。

```go
func main() {
	var a string
	var b string
	//输入时必须输入: aaa bbb
	//如果中间没有空格则把所有内容都赋值给了a
	fmt.Println(a, b)
}
```
