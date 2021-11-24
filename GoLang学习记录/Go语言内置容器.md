@[TOC](目录)

# Go语言内置容器(数组(array)、切片(slice)和映射(map))

Go语言的内置容器主要有数组(array)、切片(slice)和映射(map)。

&nbsp;

## 数组(array)

数组是具有相同类型且长度固定的一组数据项序列，这组数据项序列对应存放在内存中的一块连续区域中，数组大小之后不可再变。

&nbsp;

### 声明数组

格式如下：

```go
var 数组变量名 [数组长度]元素类型
// 例：
var student [3]string
var grid [4][5]bool // 二维数组
```

&nbsp;

### 初始化数组

1. 数组可以在声明时进行赋值：

   ```go
   var student [3]string{"Tom", "Ben", "Peter"}
   ```

2. 可以用`...`代替中括号里面的数字，Go语言编译器在编译时可以根据元素的个数来设置数组的大小

   ```go
   var student = [...]string{"Tom", "Ben", "Peter"}
   ```

&nbsp;

### 数组是值类型

1. `[10]int` 和 `[20]int`是不同的类型
2. 把一个数组变量赋值给另一个数组变量时为复制副本，重新开辟一块空间
3. 可以使用`==`比较数组中值是否相等

&nbsp;

## 切片(slice)

切片同样表示多个同类型元素的连续集合，但切片的长度是可变的，由于长度是可变的，所以可以解决数组长度在数据个数不确定情况下浪费内存的问题。

但是切片本身并不存储任何元素，而只是对现有数组的引用。(即slice本身没有数据，是对底层array的一个view)

```go
	arr := [...]int{0, 1, 2, 3, 4, 5, 6, 7}
	s := arr[2:6]
	s[0] = 10
	fmt.Println(arr)// [0 1 10 3 4 5 6 7]
```

切片结构包括：地址、长度和容量。

1. 地址：切片的地址一般指切片中第一个元素所指向的内存地址，用十六进制表示。
2. 长度：切片中实际存在的元素个数。
3. 容量：从切片的起始元素开始到其底层数组中的最后一个元素的个数。

切片的长度和容量都是不固定的，可以通过追加元素使切片的长度和容量增大。

切片主要有三种生成方式：

1. 从数组生成一个新的切片
2. 从切片生成一个新的切片
3. 直接生成一个新的切片

&nbsp;

### 从数组/切片生成一个新的切片

格式如下：

```go
slice [开始位置:结束位置]
```

例：

```go
	var student = [...]string{"Tom", "Ben", "Peter"}
	var student1 = student[1:3] // 从数组生成一个新的切片
	var student2 = student1[0:1] // 从切片生成一个新的切片
	fmt.Println("student数组：", student)
	fmt.Println("student1切片：", student1)
	fmt.Println("student2切片：", student2)
	fmt.Println("student数组地址为：", &student[1]) // 这里我们取student[1]元素的地址
	fmt.Println("student1切片地址为：", &student1[0])
	fmt.Println("student2切片地址为：", &student2[0])
	fmt.Println("student1切片长度为：", len(student1))
	fmt.Println("student1切片容量为：", cap(student1))
	fmt.Println("student2切片长度为：", len(student2))
	fmt.Println("student2切片容量为：", cap(student2))
```

上面代码运行结果为：

![image-20211122171311071](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20211122171311071.png)

根据运行结果，我们可以归纳出从数组或切片生成新的切片有如下特性：

1. 新生成的切片长度：结束位置 - 开始位置
2. 新生成的切片取出的元素不包括结束位置对应的元素
3. 新生成的切片是对现有数组或切片的引用，其地址与截取的数组或切片开始位置对应的元素地址相同。
4. 新生成的切片容量指从切片的起始元素开始到其底层数组中的最后一个元素的个数。

&nbsp;

### 直接生成一个新的切片

#### 声明切片

```go
var 切片变量名 []元素类型
```

&nbsp;

####  初始化切片

1. 在声明的同时初始化

   ```go
   var student = []string{"Tom", "Ben", "Peter"}
   ```

2. 使用make()函数初始化

   1. 声明完切片后，可以通过内建函数make()来初始化切片，格式如下：

      ```go
      make ([]元素类型, 切片长度, 切片容量)
      ```

      对于切片的容量应该有个大概的估值，若容量值过小，对切片的多次扩充会造成性能损耗。

   2. 例：

      ```go
      var student []int
      student = make([]int, 2, 10) // student切片在初始化后，自动填充0值
      ```

&nbsp;

### slice的扩展

```go
	arr := [...]int{0, 1, 2, 3, 4, 5, 6, 7}
	s1 := arr[2:6]
	s2 := s1[3:5]
```

可以思考一下s1的值是什么？s2的值又是什么呢？

![image-20211123104835080](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20211123104835080.png)

![image-20211123104858490](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20211123104858490.png)

1. s1的值为[2 3 4 5]，s2的值为[5 6]
2. slice可以向后扩展，不可以向前扩展
3. `[i]`其中`i`不可以超越`len(s)`，向后扩展不可以超越底层数组`cap(s)`

&nbsp;

### 为切片添加元素

Go语言中，我们可以使用append()函数来对切片进行元素的添加。

```go
	var student = [...]string{"Tom", "Ben", "Peter"}
	var student1 = student[0:1]
	fmt.Println("student数组：", student)
	fmt.Println("student1切片：", student1)
	student1 = append(student1, "Danny") // 对student1切片的元素添加，会覆盖引用数组对应的元素
	fmt.Println("扩充Danny后的student1切片：", student1,", 切片长度为：", len(student1),
		",切片容量为：",cap(student1))
	fmt.Println("扩充Danny后的student数组：", student)
```

![image-20211122200302316](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20211122200302316.png)

为student1切片添加元素会覆盖引用数组对应的元素，所以如果切片是从其他数组或切片生成，新切片的元素添加需要考虑对原有数组中的数据的影响。

当切片不能再容纳其他元素时（即当前切片长度值等于容量值），下一次使用append()函数对切片进行元素添加，系统会重新分配更大的底层数组，容量会按2倍进行扩充。

```go
	arr := [...]int{0, 1, 2, 3, 4, 5, 6, 7}
	s1 := arr[2:6]
	s2 := s1[3:5]
	s3 := append(s2, 10) // [5 6 10]
	s4 := append(s3, 11) // [5 6 10 11]
	s5 := append(s4, 12) // [5 6 10 11 12]
	// arr [0 1 2 3 4 5 6 10]
```

&nbsp;

### 从切片删除元素

由于Go语言没有为删除切片元素提供方法，所以需要我们手动将删除元素点前后的元素连接起来，从而实现对切片中元素的删除

```go
	var student = []string{"Tom", "Ben", "Peter", "Danny"}
	student = append(student[0:1], student[2:]...) // 与下一行注释代码等价
	// student = append(student[0:1], student[2], student[3])
	fmt.Println("student切片：", student)
	fmt.Println("student切片长度：", len(student))
	fmt.Println("student切片容量：", cap(student))
```

&nbsp;

## 映射(map)

映射是一种`无序`的键值对的集合，map的键类似于索引，指向数据的值。

&nbsp;

### 声明映射

格式如下：

```go
var map [键类型]值类型
```

获取元素：m[key]。

1. 当key不存在时，获得value类型的初始值
2. 用value, ok := m[key] 来判断是否存在key
3. key是唯一的，添加重复的key会覆盖之前的元素

&nbsp;

### 初始化映射

1. 在声明的同时初始化

   ```go
   var studentScoreMap = map[string]int {
       "Tom" : 80,
       "Ben" : 85,
       "Peter" : 90,
   }
   ```

2. 使用make()函数初始化

   ```go
   make(map[键类型]值类型, map容量)
   ```

   使用make()函数初始化map时可以不指定map容量，但是对于map的多次扩充会造成性能损耗。

   cap()函数只能用于获取切片的容量，无法获得map的容量。

&nbsp;

### 从映射中删除键值对

Go语言通过delete()函数来对map中的指定键值对进行删除操作。格式如下：

```go
delete(map实例,键)
```

1. 如果key存在，执行删除元素
2. 如果key不存在，map中内容不变，也不会报错

Go语言没有为map提供清空所有元素的方法，想要清空map的唯一方法就是重新定义一个新的map。

&nbsp;

### map的key类型及map的遍历

1. key的类型
   1. map以哈希表的方式存储键值对集合，所以key必须可以比较相等。
   2. 除了slice，map，function的内建类型都可以作为key
   3. struct类型不包含上述字段，也可以作为key
2. map的遍历
   1. 使用range遍历key，或者遍历key，value对
   2. 遍历顺序是随机的，如需特定顺序，需手动对key排序
   3. 可以使用len获得map元素个数，不可以使用cap获得容量

