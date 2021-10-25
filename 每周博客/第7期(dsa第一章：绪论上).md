## 声明

本文内容来源于学堂在线邓俊辉老师的数据结构课程以及邓俊辉老师编写的《数据结构(C++语言版)》，本文为学习内容记录。
&nbsp;

@[TOC](目录)

## 计算机与计算

计算(信息处理)：借助某种工具，遵照一定的规则，以明确而机械的形式进行。

计算机 = 计算模型 = 信息处理工具

&nbsp;

## 算法

### 定义

所谓算法，是指基于特定的计算模型，旨在解决某一信息处理问题而设计的一个指令序列。

&nbsp;

### 算法实例

#### 冒泡排序

对于一组由整数组成的序列，不难看出，每一对相邻的元素都是顺序的。反之，若所有相邻元素均顺序，则整个序列必然也整体有序。

&nbsp;

基于上述特征，我们可以不断通过改善局部的有序性从而实现整体的有序：从前向后依次检查每一对相邻元素，一旦发现逆序则交换二者位置。冒泡排序实现代码如下：

```cpp
void bubblesort(int A[], int n)
{
	for(bool sorted = false; sorted = !sorted; n--)				//逐趟扫描交换，直至完全有序
		for(int i = 1; i < n; i++)								//自左向右，逐对检查A[0,n)内各相邻元素
			if (A[i - 1] > A[i])								//若逆序，则令其呼唤，同时由于无法确保是否整体有序，故清楚有序标志
			{
				swap(A[i - 1], A[i]);
				sorted = false;
			}
    //借助布尔型标志位sorted，可及时提前退出，而不至于总是蛮力地做n-1趟扫描交换
}
```

以上是针对“将若干元素按大小排序”这一问题，基于图灵机模型而设计的`bubblesort()`算法。

&nbsp;

### 算法要素：

- 输入与输出
- 基本操作、确定性与可行性
  - 算法应可描述为由若干语义明确的基本操作组成的指令序列，且每一基本操作在对应的计算模型中均可实现。
- 有穷性(finiteness)、正确性(correctness)
  - 任意算法都应在执行有限次基本操作之后终止并给出正确的输出结果。
- 退化(degeneracy)与鲁棒性(robustness)
  - 退化指除一般情况外，实用的算法还应能够处理各种极端的输入实例。
  - 鲁棒性就是要求能够尽可能充分地应对各种退化情况。
- 重用性
  - 算法模式可推广并适用于不同类型基本元素的这种特性，即是重用性的一种典型形式。

&nbsp;

证明算法的有穷性和正确性的技巧：从适当的角度审视整个计算过程，并找出其所具有的某种不变性和单调性。其中的单调性是指，问题的有效规模会随着算法的不断推进不断递减。不变性则不仅应在算法初始状态下自然满足，而且应与最终的正确性相呼应——当问题的有效规模缩减为0时，不变性应随即等价于正确性。

&nbsp;

### 有穷性

$$序列Hailstone(n) = \begin{cases} {1} & n <= 1 \\ {n}∪Hailstone(n/2) & n为偶数 \\  {n}∪Hailstone(3*n + 1) & n为奇 \end{cases}$$



例子：

Hailstone(42) = {42, 21, 64, 32, ..., 1  }

我们注意到：这个序列中的数值总体来说 它会持续下降，但不会持续上升，其变化捉摸不定，与冰雹运动过程非常相像：有时候上升，有时候下降，直到为1时落地。

序列长度与n不成正比，Hailstone序列是否有穷，现在还没有定论，所以这个程序不能称作一个算法。

&nbsp;

## 渐进复杂度

### 时间复杂度
A：什么是时间复杂度？
Q：时间复杂是一个函数，它用来定性的描述该算法的运行时间。
- 好的算法要注重效率，速度尽可能快，存储空间尽可能少。

- 在图灵机和RAM等计算模型中，指令语句可分解为若干次基本操作，一种自然且可行的方法是将时间复杂度理解为算法中各条指令的执行时间之和。
- 由于每一次基本操作都可在常数时间内完成，不妨将T(n)定义为算法所执行基本操作的总次数。

- 从保守估计的角度出发，在规模为n的所有输入中，选择执行时间最长者作为T(n)，并以T(n)度量该算法的时间复杂度(time complexity)。

&nbsp;

在评价算法运行效率时，我们往往可以忽略其处理小规模问题时的能力差异，转而关注其在处理更大规模问题时的表现。这种着眼长远、更为注重时间复杂度的总体变化趋势和增长速度的策略与方法，即所谓的渐进分析。

&nbsp;

### 大O记号

用大O记号(big-O notation)来表示T(n)的渐进上界。即若存在非负常数c和函数f(n)，使得对于任何n >> 2都有 T(n) <= c * f(n)

则可认为在n足够大之后，f(n)给出了T(n)增长速度的一个渐进上界，此时记之为T(n) = O(f(n))

大O记号的性质：

- 对于任一非负常数c，有O(f(n)) = O(c * f(n))     即函数各项正的常数可以忽略并等同于1。
- 对于任意常数a > b > 0，有O(n^a + n^b) = O(n^a)  即多项式中的低次项可忽略。



大O记号确定上界，大Ω记号确定下界，大Θ记号给出了一个确界，是一个准确估计。

![在这里插入图片描述](https://img-blog.csdnimg.cn/d4cc65d56cf34d2ba8c43d1361689545.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3doaGNz,size_16,color_FFFFFF,t_70#pic_center)



## 复杂度分析

### 复杂度分析的主要方法

- 迭代：级数求和

- 递归：递归跟踪 + 递推方程

- 猜测 + 验证



#### 级数的分类和求和
![在这里插入图片描述](https://img-blog.csdnimg.cn/b75d1327c4ae40ab865078a55fe98249.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3doaGNz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/135d32ebd8794bf9a4977d02f4f5000e.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3doaGNz,size_16,color_FFFFFF,t_70#pic_center)

&nbsp;

#### 封底估算(Back-Of-The-Envelope Calculation)

![在这里插入图片描述](https://img-blog.csdnimg.cn/268a8a6c3efb47f39c404d1697dd40fe.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3doaGNz,size_16,color_FFFFFF,t_70#pic_center)

## 后续
立个flag，下周发两篇博客（push自己学习）
