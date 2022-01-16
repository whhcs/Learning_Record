@[TOC](文章目录)

# Go语言中的测试、(pprof)性能调优、生成文档和示例代码

## 代码测试(test)

我们在写程序时应该尽可能多写测试，而不是多做调试。

代码测试的目的是确保代码按照你的设想执行，编写测试代码可以尽早地发现程序所存在的问题。

`go test`命令用于对Go语言编写的代码包进行测试。可以指定要测试的文件，也可以直接对整个包进行测试，但需要包中含有测试文件，测试文件都是以`_test.go`结尾。

&nbsp;

### 测试文件格式

测试程序必须属于被测试的包，并且测试程序所在文件名满足`XX_test.go`，所以测试代码和包中的业务代码是分开的。

测试文件中必须导入 `testing` 包，编写的测试函数为全局函数，测试函数的命名必须以`Test`开头。

例如：`func TestSubstr(t *testing.T)`、`func TestTriangle(t *testing.T)`。

T 是传给测试函数的结构类型，用来管理测试状态，支持格式化测试日志，如` t.Log`，`t.Error`，`t.ErrorF` 等。

&nbsp;

### 传统测试

![image-20220116171857696](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20220116171857696.png)

缺点：

1. 测试数据和测试逻辑混在一起
2. 出错信息不明确
3. 一旦一个数据出错，程序宕机，测试全部结束

&nbsp;

### 单元测试(表格驱动测试)

```go
func calcTriangle(a, b int) int {
	var c int
	c = int(math.Sqrt(float64(a*a + b*b)))
	return c
}
```

```go
func TestTriangle(t *testing.T) {
	tests := []struct{ a, b, c int }{
		{3, 4, 5},
		{5, 12, 13},
		{8, 15, 17},
		{12, 35, 37},
		{30000, 40000, 50000},
	}

	for _, tt := range tests {
		if actual := calcTriangle(tt.a, tt.b); actual != tt.c {
			t.Errorf("calcTriangle(%d, %d); "+"got %d; expected %d",
				tt.a, tt.b, actual, tt.c)
		}
	}
}
```

优点：

1. 分离的测试数据和测试逻辑
2. 明确的出错信息
3. 可以部分失败
4. Go语言的语法使得我们更易实践表格驱动测试

&nbsp;

### 基准测试（性能测试）

基准测试提供可自定义的计时器和一套基准测试算法，能方便快速地分析一段代码可能存在的CPU性能和内存分配问题。

类似于单元测试，使用者无须准备高精度的计时器和各种分析工具，
基准测试本身即可打印出非常标准的测试报告。  

基准测试的使用也有一套书写规范，使用`Benchmark`为前缀，后面加上需要测试的函数名，并使用`*testing.B`作为函数参数，如下所示。  

```go
func BenchmarkFunc(b *testing.B) {
	b.ResetTimer()
	for idx := 0; idx < b.N; idx++ {
		// 要测试的代码
	}
	b.StopTimer()
}
```

其中`b.N`表示必须循环`b.N`次，`b.N`这个数字会在运行中调整，以便最终达到合适的时间消耗，方便计算出合理的数据。  

有些测试代码需要一定的初始化时间，如果从Benchmark()函数开始计时，很大程度上会影响测试结果的精准性。

`testing.B`提供了可以方便地控制计时器的一系列方法，从而让计时器只在需要的区间进行测试。  

`ResetTimer()`表示重置计时器，`StopTimer()`表示停止计时，需要精准测试的代码只需要放到这两个函数中间即可。

通过基准测试，我们能够知道使用哪种编码方式会让程序的运行效率最高  

&nbsp;

### 覆盖率测试

覆盖率测试用于统计通过运行程序包的测试有多少代码得到了执行。如果执行测试代码有70％的语句得到了运行，则测试覆盖率为70％。

使用-cover参数就可以对源码文件进行覆盖率测试。

例如我们还可以使用以下命令:

使用`go test -coverprofile=c.out`命令可以统计测试覆盖率，接着使用`go tool cover -html=c.out`可以在网页中查看更加详细的测试覆盖率结果，统计每个函数的测试覆盖率。

&nbsp;

## 性能分析和调优

性能分析和调优是一种强大的技术，用于验证是否满足实际性能要求。Go语言支持使用`go tool pprof`工具进行性能查看及调优。性能分析工具可以将程序的CPU耗用、内存分配、竞态等问题以图形化方式展现出来。

在使用这个工具前，我们需要安装其依赖的图形绘制工`Graphviz`。

&nbsp;

### 安装Graphviz

点击以下链接[Download | Graphviz](https://www.graphviz.org/download/)下载自己操作系统所对应的版本，按照提示进行默认安装即可。

安装完成之后，需要将Graphviz安装路径的bin文件加添加到环境变量path中。

![image-20220116182849095](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20220116182849095.png)

可以在命令行中输入`dot -v`查看是否添加成功。

&nbsp;

### 使用pprof进行性能分析与调优

首先我们先输入`go test -bench . -cpuprofile cpu.out`，生成一个`cpu.out`的二进制文件，该文件为程序运行的性能日志文件。接着输入`go tool pprof cpu.out`，接着我们使用web命令来查看图形界面的程序执行流程。

![image-20220116185408941](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20220116185408941.png)

输入web后，浏览器性能可视化的图，模块越大表示性能消耗越大。![image-20220116185517270](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20220116185517270.png)

然后我们分析性能消耗的主要模块，进行优化代码即可，这里就能体现出程序员自身功力了。

性能调优的主要流程如下图：

![image-20220116185834783](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20220116185834783.png)

&nbsp;

## 生成文档和示例代码

#### 生成文档

我们可以使用`go doc`查看指定包的文档，例如我们查看函数`fmt.Println`的文档说明。

![image-20220116190513621](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20220116190513621.png)

那么，如何编写Go语言的文档呢？

![image-20220116190608057](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20220116190608057.png)

我们观察源码可以发现，Go语言的文档只需要在每个函数上方用注释的方式介绍该函数的作用及使用方法，go doc命令就会自动将这些注释转化为文档展示出来。

&nbsp;

godoc命令有一个非常重要的参数-http，这个参数的作用是开启Web服务，提供交互式的文档查看页面。  

例如我们运行命令`godoc -http :6060`，运行之后，浏览器访问`http://localhost:6060/`即可通过网页的方式查看Go语言文档。如下图所示：

![image-20220116191454417](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20220116191454417.png)

&nbsp;

#### 生成示例代码

在测试文件中，编写以`Example`开头的函数，同时在函数中需要写好输出结果。如下图所示：

![image-20220116192008598](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20220116192008598.png)

运行程序时，不但会在文档中生成示例代码，还会做测试，检查结果是否通过测试。运行后，文档所示效果如下图：

![image-20220116192122340](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20220116192122340.png)

&nbsp;
