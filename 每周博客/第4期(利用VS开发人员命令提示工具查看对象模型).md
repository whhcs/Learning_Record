# 利用VS开发人员命令提示工具查看对象模型

## 1.打开VS2019的开发人员命令提示符

![image-20210710215242443](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20210710215242443.png)

打开之后如下图所示

![image-20210710215313498](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20210710215313498.png)

&nbsp;

## 2.跳转盘符

![image-20210710215600778](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20210710215600778.png)

首先打开文件所在的文件加，复制文件路径

![image-20210710215640841](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20210710215640841.png)

之后检查文件所在盘符与当前盘符是否一致。若要跳转至D盘，可输入`d:`之后回车

![image-20210710215834779](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20210710215834779.png)

然后输入`cd 文件路径`之后回车，跳转至文件所在位置，输入`dir`查看确实当前路径是否存在所要查看的源文件。

![image-20210710220202364](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20210710220202364.png)

&nbsp;

## 3.输入`cl /d1 reportSingleClassLayout类名 文件名`

例：

![image-20210710220907754](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20210710220907754.png)

得到：

![image-20210710222009788](C:\Users\whh\AppData\Roaming\Typora\typora-user-images\image-20210710222009788.png)

