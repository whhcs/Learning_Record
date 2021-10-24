# Golang基本语法

@[TOC](目录)

## 切片slice

切片与数组一样表示多个同类型元素的连续集合，但是切片本身并不存储任何元素，而只是对现有数组的引用。

&nbsp;

切片结构包括：地址、长度和容量。

1. 地址：切片的地址一般指切片中第一个元素所指向的内存地址，用十六进制表示。
2. 长度：切片中实际存在的元素的个数。
3. 容量：从切片的起始元素开始到其底层数组中的最后一个元素的个数。

&nbsp;

切片的长度和容量都是不固定的，可以通过追加元素使切片的长度和容量增大。

&nbsp;

切片主要有三种生成方式：

1. 从数组生成一个新的切片。
2. 从切片生成一个新的切片。
3. 直接生成一个新的切片。



&nbsp;

### 从数组生成一个新的切片

```go
package main

import (
	"fmt"
)

func main() {
	var student = [...]string{"Tom", "Ben", "Peter"}
	var student1 = student[0:2]
	fmt.Println("student数组：", student)
	fmt.Println("student1切片：", student1)
	fmt.Println("student数组地址为：", &student[0])
	fmt.Println("student1切片地址为：", &student1[0])

	fmt.Println("student1切片长度为：", len(student1))
	fmt.Println("student1切片容量为：", cap(student1))
}
```

![image-20211020194939355](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20211020194939355.png)

运行上面这段代码，我们可以发现：

1. 新生成的切片长度 = 结束位置 - 开始位置
2. 新生成的切片取出的元素不包括结束位置对应的元素（左闭右开）。
3. 新生成的切片是对现有数组或切片的引用，其地址与截取的数组或切片开始位置对应的元素地址相同。
4. 新生成的切片容量指从切片的起始元素开始到其底层数组中的最后一个元素的个数。

&nbsp;

### 直接生成一个新的切片

1. 声明切片

   切片的声明格式：

   > var 切片变量名 []元素类型

   切片声明后，其内容为空，长度和容量均为0。

2. 初始化切片

   1. 在声明的同时初始化

      ```go
      var student = []string{"Tom", "Ben", "Peter"}
      ```

   2. 使用make()函数初始化

      声明完切片后，可以通过内置函数make()来初始化切片，格式如下：

      ```go
      slice = make ([]元素类型, 切片长度, 切片容量)
      ```

      对切片的容量应该有个大概的估值，若容量过小，对切片的多次扩充会造成性能损耗。

3. 为切片添加元素

   Go语言中，我们可以使用append()函数来对切片进行元素的添加。当切片长度值等于容量值时，下一次使用append()函数对切片进行元素添加，容量会按2被数进行扩充。

   ```go
   func main() {
   	student := make([]int, 1, 1)
   	for i := 0; i < 8; i++ {
   		student = append(student, i)
   		fmt.Println("当前切片的长度：", len(student),"当前切片的容量：", cap(student))
   	}
   }
   ```

   ![image-20211020204757431](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20211020204757431.png)

4. 从切片中删除元素

   由于Go语言没有为删除切片元素提供方法，所以需要我们手动将删除点前后的元素连接起来，从而实现对切片中元素的删除。

   ```go
   func main() {
   	var student = []string{"Tom", "Ben", "Peter", "Danny"}
   	student = append(student[0:1], student[2:]...)
       //student = student[0:0]	清空切片中所有的元素
   	fmt.Println("student切片：", student)
   	fmt.Println("student切片长度：", len(student))
   	fmt.Println("student切片容量：", cap(student))
   }
   ```

   ![image-20211020205756950](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20211020205756950.png)

   其中append()函数中传入省略号代表按student切片展开，等价于：

   ```go
   student = append(student[0:1], student[2], student[3])
   ```

   清空切片中所有的元素可以把切片的开始和结束下标都设为0来实现。

&nbsp;

## 映射map

映射（map）是一种无序的键值对的集合，map的键类似于索引，指向数据的值。

&nbsp;

1. 声明映射

   map的声明格式如下：

   ```go
   var map [键类型]值类型
   ```

2. 初始化映射

   1. 在声明的同时初始化

      ```go
      func main() {
      	var studentScoreMap = map[string]int {
      		"Tom" : 80,
      		"Ben" : 85,
      		"Peter" : 90,
      	}
      	fmt.Println(studentScoreMap)	// map[Ben:85 Peter:90 Tom:80]
      }
      ```

   2. 使用make()函数初始化

      格式如下：

      ```go
      make(map[键类型]值类型, map容量)
      ```

      使用make()函数初始化时可以不指定map容量，但是对于map的多次扩充会造成性能损耗。

3. 从映射中删除键值对

   Go语言通过delete()函数来对map的指定键值对进行删除操作，格式如下：

   ```go
   delete(map实例, 键)
   ```

   另外Go语言没有为map提供清空所有元素的方法，想要清空map的唯一方法就是重新定义一个新的map。
   
   ```go
   package main
   
   import "fmt"
   
   func main() {
   	var studentScoreMap map[string]int
   	studentScoreMap = make(map[string]int)
   	studentScoreMap["Tom"] = 80
   	studentScoreMap["Ben"] = 70
   	studentScoreMap["Peter"] = 90
   	delete(studentScoreMap, "Tom")
   		fmt.Println(studentScoreMap) // map[Ben:70 Peter:90]
   }
   ```
   
   &nbsp;
   
## 函数

   Go语言函数支持的特性：

   1. 参数数量不固定（可变参数）。
   2. 匿名函数及闭包。
   3. 函数本身作为值传递。
   4. 函数的延迟执行。
   5. 把函数作为接口调用。

&nbsp;

### 声明函数

格式如下：

```go
func 函数名（参数列表） （返回参数列表） {
    函数体
}
```

1. 在参数列表中，如果相邻的变量为同类型，则不必重复写出类型。

   ```go
   func add(x, y int) (sum int) {
       sum = x + y
       return sum
   }
   ```

2. 如果函数的返回值都是同一类型，在返回值列表中可以将返回参数省略。

   ```go
   func returnValue() (int, int) {
       return 0, 1
   }// 不推荐这种写法，代码可读性降低，无法区分每个返回值的实际意义。
   ```

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

