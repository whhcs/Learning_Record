@[TOC](目录)

# 结构体及其方法

## 结构体

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

结构体实例化时，会真正地分配内存。

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

### 结构体内嵌

Go语言的结构体内嵌是一种组合特性，使用结构体内嵌可构建一种面向对象编程思想中的继承关系。

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

&nbsp;

### 结构体方法

方法和函数比较像，区别是函数属于包，通过包调用函数，而方法属于结构体，通过结构体变量调用。

Go语言中，一个方法就是一个包含了接收者的函数。格式如下：

```go
func (变量名 结构体类型) 方法名(参数列表) (返回值列表) {
    // 方法体
}
```

对于结构体方法，接收者可以是结构体类型的值或指针。

1. 指针类型接收者：当接收者类型为指针时，可以通过该方法改变该接收者的成员变量值，即使你使用了非指针类型的实例调用该函数，也可以改变实例对应的成员变量值。
2. 值类型接收者：该方法操作对应接收者值得副本，即使你使用了指针类型的实例调用该函数，也无法改变成员变量的值。

&nbsp;

# 接口与类型

## 接口的定义

在Go语言中，接口(interface)是一个自定义类型。接口类型是一种抽象的类型，它不会暴露出它所代表的内部属性的结构，而只会展示出它自己的方法，因此，不能将接口类型实例化。

Go语言接口是方法的集合，使用接口是实现模块化的重要方式。

&nbsp;

## 接口的创建与实现

接口是用来定义行为类型的。这些被定义的行为不由接口直接实现，而是由用户自定义的类型实现，一个实现了这些方法的具体类型就叫作这个接口类型的实例。(接口中声明完方法后，结构体重写接口中所有的方法，即认为结构体实现了接口)

使用type和interface关键字即可创建一个接口，接口内部只需要声明存在什么方法，不需要实现这些方法。

```go
type Animal interface {
	Name() string
	Speak() string
}

type Cat struct {
}

func (cat Cat) Name() string {
	return "Cat"
}

func (cat Cat) Speak() string {
	return "喵喵喵"
}
```

上面代码中创建了一个叫Animal的接口，结构体Cat实现了Animal接口的两个方法，因此我们就可以认为Cat是Animal接口的实例。

&nbsp;

### 多态

同一件事情由于条件不同产生的结果不同即为多态，多态在代码层面最常见的一种方式是接口作为方法参数。

```go
type Live interface {
	run()
}
type People struct{}
type Animate struct{}

func (p *People) run() {
	fmt.Println("人在跑")
}
func (a *Animate) run() {
	fmt.Println("动物在跑")
}

func sport(live Live) {
	live.run()
}

func main() {
	peo := &People{}
	sport(peo) //输出:人在跑
	ani := &Animate{}
	sport(ani) //输出:动物在跑
}
```

1. 结构体实现了接口的全部方法，就认为结构体属于接口类型，这时可以把结构体变量赋值给接口变量。
2. 重写接口时，方法接收者为`Type`和`*Type`的区别
   1. `*Type`可以调用`*Type`和`Type`作为接收者的方法。所以只要接口多个方法中，至少出现一个使用`*Type`作为接收者进行重写的方法，就必须把结构体指针赋值给接口变量，否者编译报错。
   2. `Type`只能调用`Type`作为接收者的方法。

&nbsp;

## 空接口

在Go语言中，空接口(interface{})不包含任何方法，也正因如此，所有的类型都实现了空接口，因此空接口可以存储任意类型的数值。

```go
func Log(name string, i interface{}) {
	fmt.Printf("Name = %s, Type = %T, value = %v\n",
		name, i , i)
}

func main() {
	var v1 interface{} = 1
	var v2 interface{} = "abc"
	var v3 interface{} = true
	var v4 interface{} = &v1
	var v5 interface{} = struct {
		Name string
	}{"Xiao Ming"}
	var v6 interface{} = &v5

	Log("v1", v1)
	Log("v2", v2)
	Log("v3", v3)
	Log("v4", v4)
	Log("v5", v5)
	Log("v6", v6)
}
```

![image-20211121165730076](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20211121165730076.png)

&nbsp;

## 类型断言

类型断言是使用在接口变量上的操作，简单来说，接口类型向普通类型的转换就是类型断言。格式如下：

```go
t, ok := X.(T)
```

这句代码的含义是判断X的类型是否是T。

如果断言成功，则ok为true，t为接口变量X的动态值；如果断言失败，则ok为false，t为类型T的初始值。即t的类型始终为T。

&nbsp;

### ok-pattern

接口类型断言有两种方式，一种是ok-pattern，另一种是switch-type。

一般来说，当要断言的接口类型种类较少时，可以使用ok-pattern这种方式。

```go
if value, ok := 接口变量.(类型); ok == true {
   // 接口变量是该类型时的处理
}
```

&nbsp;

### switch-type

当要断言的接口类型种类较多时，使用ok-pattern方式，代码非常冗余。这时我们可以使用switch-type来进行批量判断：

```go
type Person struct {
	Name string
	Age int
}

func main() {
	s := make([]interface{}, 3)
	s[0] = 1
	s[1] = "abc"
	s[2] = Person{"张三", 20}

	for index, data := range s {
		switch value := data.(type) {
		case int:
			fmt.Printf("s[%d] Type = int, value = %d\n", index, value)
		case string:
			fmt.Printf("s[%d] Type = string, value = %s\n", index, value)
		case Person:
			fmt.Printf("s[%d] Type = Person, Person.name = %v, Person.Age = %d\n", index, value.Name,value.Age)
		}
	}
}
```

&nbsp;

## 非侵入式接口

- 侵入式接口：需要显示地创建一个类去实现接口。
- 非侵入式接口，不需要显示地创建一个类去实现一个接口。

大部分语言的接口都是侵入式接口，Go语言的接口是非侵入式的。如果接口A的方法集是接口B方法集的子集，那么接口B的实例可以直接给接口A赋值。

&nbsp;

非侵入式接口有三个好处：

1. 去掉了繁杂的继承体系，Go语言的标准库再也不需要绘制类库的继承树图。

   在Go中，类的继承树并无意义，你只需要知道这个类实现了哪些方法，每个方法是何含义就足够了。

2. 实现类的时候，只需要关心自己应该提供哪些方法，不用再纠结接口需要拆得多细才合理。接口由使用方按需定义，而不用事前规划。

3. 不用为了实现一个接口而导入一个包，因为多引用一个外部的包，就意味着更多的耦合。接口由使用方按自身需求来定义，使用方无须关心是否有其他模块定义过类似的接口。

