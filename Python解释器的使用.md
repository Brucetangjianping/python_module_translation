### 2.1 解释器的调用

Python解释器一般像这样/usr/local/bin/python3.5安装在可以安装的系统中，在Unix中将/usr/local/bin设置为shell的search path(全局路径)，这样不管你在什么目录下，都可以使用以下命令直接运行python解释器
```
python3.5
```
对于shell而言，选择哪个目录安装python只是你的一个安装选择而已，其它目录也是可以的，如果在其它地方，问问一些Python大牛或者系统管理员（例如：/usr/local/python是最常见的安装目录）

在Windows系统中，Python解释器一般安装在C:\Python35,当然你也可以在你安装的时候选择其它的目录，你可以在你的命令提示符的DOS界面中键入以下命令，将这个安装路径设置成全局路径：
```
set path=%path%;C:\Python35
```
在命令提示符中键入文件结束符（end-of-file）(Control-D on Unix, Control-Z on Windows)可以使python解释器以0的退出状态退出。如果这样没效果的话，你也可以使用命令quit()进行退出操作。

解释器的行编辑特性包含了交互式编辑，历史回溯以及代码补全。检查是否支持行编辑的最快方法就是键入Control-P在python的提示符中，如果它哔的响了一声，那恭喜你，是支持的，查看附录了解更多关于交互输入行编辑和历史回溯的。如果什么都没发生的话，或者出现了```^p```，那么很可惜，你的系统并不支持行编辑，你只能使用backspace来删除当前行的字符

解释器的操作有些像Unix shell, 当连接到tty进行标准输入的时候，会交互式的读取和执行命令， 当调用文件名或者文件作为标准输入时，解释器运行该文件的一个脚本进行读取和执行

启动解释器的第二种方法是在命令提示符中执行```python -c command [args] ...```这条语句，与shell的```-c```选项类似， 因为Python的语句经常包含空字符串或者是对于shell来说，比较特殊的字符。建议使用单引号将该命令括起来。

一些python的模块也会像脚本一样有用处，这些模块可以执行```python -m module [args] ```命令，从而执行源文件，这样就不必详细的将全名书写到命令行中。

当一个脚本文件被使用时，在交互式模式下跑脚本有时还是很有好处的，你可以在脚本名字前加入一个-i的参数来实现。

在命令行和环境中有所有关于命令行选择的描述

#### 2.1.1 传递参数
当我们谈到解释器时，python脚本的名称和之后附加的参数是字符串的列表和在```sys```模块中分配给叫做```argv```的变量， 你可以执行```import sys```来访问这些参数列表。该列表的长度至少为1，当不传递脚本名称和参数时，```sys.argv[0]```是一个空字符串。当脚本的名称赋值为```'-'```(意味着标准输入)， ```sys.argv[0]``` 被设置为'-'。当使用```-c```命令被使用时，```sys.argv[0] ``` 被设置为```'-c'```， 当```'-m'```模块被使用时，```sys.argv[0]```被设置为当前模块的全称，在```-c```命令或```'-m'```模块之后的选项并不会当作python解释器的选项执行，仅仅是留给```sys.argv[0]```当作命令或模块处理

#### 2.1.2 交互模式
当是从tty读取到命令行时，这就叫做处于交互模式。在这种模式下，是通过主提示符来提示输入下一个命令，通常是三个大于号(```>>>```),而继续输入的提示符默认是使用三个点(```...```)来提示的.python解释器输出第一个主提示符时会打印出解释器的版本信息和版权声明作为欢迎语。
```
Python 3.5.2 (v3.5.2:4def2a2901a5, Jun 25 2016, 22:18:55) [MSC v.1900 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

当创建多行输入的结构时，你需要继续输入的行符，如下if语句所示：
```
>>> the_world_is_flat = True
>>> if the_world_is_flat:
...     print('Be careful not to fall off !')
...
Be careful not to fall off !
```
想要了解更多关于交互模式的信息，请跳转到```交互模式```

### 解释器和它的环境
#### 源代码编码
默认情况下，python的源文件是使用UTF-8作为编码格式的，因为在utf-8的字符集中，世界上大多数的字符都能够找到到相应的编码。虽然标准库中只是用ASCII字符集作为标识符， 但是使用utf-8是任何可移植代码需要遵守的公约。为了能够正确的展示你的字符，你的编辑器必须识别UTF-8的文件，并且该编辑器含有一种字体，能支持文件中所有的字符。

为了声明不同于默认的编码格式，你需要在文件头加一个特殊的注释代码，语法如下所示：
```
# -*- coding: encoding -*- 
```
其中的encoding代指python的编解码器认可的任何一种编码。

例如，为了表明该文件是使用Windows-1252的编码格式，你的源代码文件的首行应该这样添加：
```
# -*- coding: cp-1252 -*-
```
有个例外，当源代码是以Unix"shebang"行开始的，那么编码的声明应该放在第二行，如下所示：
```
#! /usr/bin/env python3
# -*- coding: cp-1252 -*-
```

#### 脚注
在unix系统中，Python3.x解释器的执行程序是使用默认的命名，而不是使用```python```命名，所以可以同时装python2.x而不冲突。
