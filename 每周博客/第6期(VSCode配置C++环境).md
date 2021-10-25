# VSCode配置C++环境

@[TOC](目录)

因为打算刷题，嫌弃`codeblocks`界面太丑了。vs界面经过我更换之后倒是看着很舒服，但是用vs刷题实在是大材小用，毕竟`oj`都只是单文件（而且打开vs也挺慢的）。所以果断投入`vscode`的怀抱了。

&nbsp;

一开始是按照热门博客[整理：Visual Studio Code (vscode) 配置C、C++环境/编写运行C、C++（主要Windows、简要Linux）_一苇以航-CSDN博客_vscode配置c++环境](https://blog.csdn.net/bat67/article/details/76095813)这个来配置的，结果出现`prelaunchtask"g++"已终止, 退出代码为1解决`的问题。

&nbsp;

这篇博客就是想记录一下，避免大多数坑，使得小白也能使用`vscode`运行C++程序。

&nbsp;

## 安装编译，调试环境

如果已安装`codeblocks或者Dev c++`，则可以使用其自带的编译器。否则需自行下载`MinGW`，下载地址为https://osdn.net/projects/mingw/downloads/68260/mingw-get-setup.exe/

&nbsp;

然后将bin所在文件加入系统环境变量中，比如由于我已经装有`codeblocks`，所以如下图所示：

![](A:\Pictures\Screenshots\微信图片_20210725012915.png)

&nbsp;

## 生成.vscode文件

如图:

![](A:\Pictures\Screenshots\微信图片_20210725013245.png)

&nbsp;

## 将下面代码分别复制到对应文件中

### c_cpp_properties.json

```json
{
    "configurations": [
        {
          "name": "Win32",
          "includePath": ["${workspaceFolder}/**"],
          "defines": ["_DEBUG", "UNICODE", "_UNICODE"],
          "windowsSdkVersion": "10.0.17763.0",
          "compilerPath": "D:\\codeblocks\\codeblocks-20.03mingw-nosetup\\MinGW\\bin\\g++.exe",
          "cStandard": "c11",
          "cppStandard": "c++17",
          "intelliSenseMode": "${default}"
        }
      ],
      "version": 4
}

```

此处需要修改`compilerPath`路径

&nbsp;

### launch.json

```json
{  
    "version": "0.2.0",  
    "configurations": [  
        {  
            "name": "(gdb) Launch", // 配置名称，将会在启动配置的下拉菜单中显示  
            "type": "cppdbg",       // 配置类型，这里只能为cppdbg  
            "request": "launch",    // 请求配置类型，可以为launch（启动）或attach（附加）  
            "program": "${fileDirname}/${fileBasenameNoExtension}.exe",// 将要进行调试的程序的路径  
            "args": [],             // 程序调试时传递给程序的命令行参数，一般设为空即可  
            "stopAtEntry": false,   // 设为true时程序将暂停在程序入口处，一般设置为false  
            "cwd": "${workspaceFolder}", // 调试程序时的工作目录，一般为${workspaceFolder}即代码所在目录  
            "environment": [],  
            "externalConsole": true, // 调试时是否显示控制台窗口，一般设置为true显示控制台  
            "MIMode": "gdb",  
            "miDebuggerPath": "D:\\codeblocks\\codeblocks-20.03mingw-nosetup\\MinGW\\bin\\gdb.exe", // miDebugger的路径，注意这里要与MinGw的路径对应  
            "setupCommands": [  
                {   
		            "description": "Enable pretty-printing for gdb",  
                    "text": "-enable-pretty-printing",  
                    "ignoreFailures": true  
                }  
            ],
            "preLaunchTask": "build hello world"//和tasks中label保持一致
        }  
    ]  
}
```

此处注意修改`miDebuggerPath`路径

&nbsp;

### tasks.json

```json
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558 
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
        "label": "build hello world",
        "type": "shell",
        "command": "g++",
        "args": [
            "-g",
            "${file}",
            "-o",
            "${fileDirname}\\${fileBasenameNoExtension}.exe"
        ],
        "group": {
            "kind": "build",
            "isDefault": true
        }
        }
    ],
    "presentation": {
        "echo": true,
        "reveal": "always",
        "focus": false,
        "panel": "new", //shared表示共享，改成new之后每个进程创建新的端口
        "showReuseMessage": true,
        "clear": false
    }
}

```

`tasks.json`文件无需修改，直接复制即可。

&nbsp;

## 编译运行

写好代码之后，按F5启动调试，会发现有一个黑框一闪而过，这是正常现象，只需在`return 0;`的上一句加上`getchar();`即可

&nbsp;

## 后序

我在配置环境的过程中，还遇到了`vs code中命名空间不明确`问题，如果大家也有遇到这个问题，可以参考一下[解决vs code中命名空间不明确问题_爱打羽球的码猿的博客-CSDN博客](https://blog.csdn.net/weixin_46822367/article/details/115307166?ops_request_misc=%7B%22request%5Fid%22%3A%22162714072416780261999248%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=162714072416780261999248&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-115307166.first_rank_v2_pc_rank_v29&utm_term=vscode+std不明确&spm=1018.2226.3001.4187)这篇博客。