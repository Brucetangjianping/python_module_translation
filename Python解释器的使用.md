## 2.1 解释器的调用
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

