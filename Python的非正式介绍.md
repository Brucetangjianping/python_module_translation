在下面的例子中，输入和输出是用含有提示符（`>>>`和`...`）或没有提示符来区分也就是说，当提示符出现的时候，你可以在其后键入任何东西，而没有以提示符开头的行是解释器输出的东西。注意，当你想要结束多行编辑，你需要键入一个空白行来退出。

在这套手册中有很多的例子，甚至在交互式提示符中都含有注释。 在python中的注释都是以一个hash字符`#`开头，并且注释是扩展的物理行。它可以出现在一行的开头，或者是空白处的后面亦或者是代码的后面，但绝不能在代码的字符串文字中。一个带有字符串文字的hash字符依然只是一个hash字符。因为注释是用来解释代码，并且不会被python解释器解释执行，所以在键入一个例子的时候，可以忽略它。

以下就是一些例子：
```
# this is the first comment
spam = 1  # and this is the second comment
          # ... and now a third!
text = "# This is not a comment because it's inside quotes."
```

### 3.1 python可以当做计算器使用
让我们来尝试以下简单的Python命令，启动解释器并且等待它的主提示符，`>>>`（这应该花不了多长时间）

#### 3.1.1 数字
解释器可以充当一个简单的计算器，你可以键入一个表达式，然后解释器会返回该表达式的值。表达式的语法是直截了当的，python的运算符`+`, `-`, `*`, `/`和大多数其它的语言（例如，Pascal 或 C）类似,用括号来分组，例如：
```
>>> 2 + 2
4
>>> 50 - 5*6
20
>>> 8 / 5  # 除法总是返回一个浮点类型的数
1.6
>>> (50 - 5*6) / 4
5.0
```
整型数(例如： 2，4，20)都是`int`类型，而含有小数的都是`float`类型，我们在教程后面会见识到更多的数字类型。

除法(`/`)通常返回的是一个float, 在进行向下取整(舍弃所有的小数部分)时，你可以使用`//`运算符，而取余的话可以使用`%`云算符。
```
>>> 17 / 3  # 典型的除法返回的是`float`值
5.666666666666667
>>> 17 // 3  # 向下取整会舍弃所有的小数部分
5
>>> 17 % 3 # 使用%云算符得到是余数
2
>>> 5 * 3 + 2 
17
```
python可以使用`**`进行指数运算
```
>>> 5 ** 2 # 5的平方
25
>>> 2 ** 7 # 2的7次方
128
```
等号(`=`)是用来给变量赋值， 使用了等号之后，在下一个交互提示符中就不会返回运算的结果。
```
>>> width = 20
>>> height = 5 * 9
>>> width * height
900
```
如果一个变量没有被"定义"(赋一个初始值)的话, 在使用的时候就会报错
```
>>> n 
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'n' is not defined
```

python完全支持浮点数，不同类型的操作数，会将integer的操作数先变成浮点类型：
```
>>> 3 * 3.75 / 1.5
7.5
>>> 7.0  /  2
3.5
```
在交互模式中，最后打印的表达式被赋给了`_`变量。这就意味着，当你使用Python作为一个桌面计算器时，对于继续计算在某些方面老说很方便。例如：
```
>>> tax = 12.5/100
>>> price = 100.50
>>> price * tax
12.5625
>>> price + _
113.0625
>>> round(_, 2)
113.06
```
这个变量应该当做一个只读数据，不要尝试显式给它赋值，你可能会创建了一个和内置变量相同名字的本地变量，那么你的变量就会掩盖了内置变量，以及失去了它本身的很酷的行为。

除了`int`和`float`，Python还支持其它类型的数字，例如`Decimal`和`Fraction`。Python也支持`Complex Numbers`，使用`j`或者`J`当后缀来表示虚数部分(如：2 + 4j)

#### 3.1.2 字符串
除了数字，Python也能操作用多种方式表示的字符串， 使用单引号`('...')`和双引号`("...")`表示的字符串是相同结果，使用`\`来转义引号：
```
>>> 'spam eggs'
'spam eggs'
>>> 'doesn'\t'
  File "<stdin>", line 1
    'doesn'\t'
             ^
SyntaxError: unexpected character after line continuation character
>>> 'doesn\'t'
"doesn't"
>>>
>>>
>>> '"Yes", he said'
'"Yes", he said'
>>> "\"Yes\", he said"
'"Yes", he said'
>>> '"Isn\'t it", she said'
'"Isn\'t it", she said'
```
如果你不想字符的`\`前缀被转义为特殊字符，你可以在字符串第一个引号前面加一个`r`
```
>>> print('C:\some\name')
C:\some
ame
>>> print(r'C:\some\name')
C:\some\name
```
有一些字符串文字可能跨了好几行，可以使用`"""..."""`或者`'''...'''`.字符串会自动添加换行符，但是你也可以在后面添加`\`取消自动添加行结束符，如下所示：
```
print("""\
... heheh
... hahah
... enene
... """)
```
打印的输出(注意，第一个行并不是空格换行)如下所示
```
heheh
hahah
enene
```
多个字符串可以用`+`操作符连成一个字符串（粘在一块），并且可以使用`*`来处理多个相同的字符
```
>>> # 3 times 'un', followed by 'ium'
...
>>> 3 * 'un' + 'ium'
'unununium'
```
两个或多个字符串(都是使用引号包住)，如果中间没有其它符号，则会自动的连成一个字符串：
```
>>> 'Py''thon'
'Python'
```
这仅仅适用两个都是字符串的情况，变量和表达式则不适用
```
>>> prefix'thon'
  File "<stdin>", line 1
    prefix'thon'
               ^
SyntaxError: invalid syntax
>>> ('un'*3)'um'
  File "<stdin>", line 1
    ('un'*3)'um'
               ^
SyntaxError: invalid syntax
```
如果你想将变量或者一个变量和一个字符串连在一块，可以使用`+`
```
>>> prefix + 'thon'
'Python'
```
这个功能对于长字符串的拼写很有帮助:
```
>>> text = ('this is a very long string,')
>>> text = ('this is a very long string,'
...         'you should break into two lines')
>>> text
'this is a very long string,you should break into two lines'
```
字符串还可以被索引(使用下标访问)，第一个字符的下标为0，没有分割的字符串类型，一个字符就是长度为1的字符串：
```
>>> word = 'Python'
>>> word[0]
'P'
>>> word[5]
'n'
```
指数也可以是负数的形式，也就是从字符串的右边起数数
```
>>> word[-1]
'n'
>>> word[-2]
'o'
>>> word[-5]
'y'
```
注意，因为0的负数还是0,所以负指数是从-1开始
字符串不仅支持索引，还支持分片功能，索引是用来检索单个字符，而分片是用来获取子字符串
```
>>> word[0:2]  # 字符时从0开始包括0，到2结束不包括2
'Py'
>>> word[2:5] # 字符串从2开始包括2，到5结束不包括5
'tho'
```
注意，因为起始的指数是包含的，而结束的指数是不包含的，这也就是说`s[:i] + s[i:]`获得的字符串等于's'
```
>>> word[:2] + word[2:]
'Python'
>>> word[:4] + word[4:]
'Python'
```
切片索引有很好用的默认值，如果第一字符不写的话默认为0， 如果第二个指数不写的话默认为字符串的长度
```
>>> word[:2]
'Py'
>>> word[4:]
'on'
>>> word[-2:]
'on'
```
有种很好记的方法，来记录切片是如何工作的，也就是左边第一个字母对应的小编为0，一个长度为n的字符串右边最后一个字符的下标为n,如下所示：
```
 +---+---+---+---+---+---+
 | P | y | t | h | o | n |
 +---+---+---+---+---+---+
 0   1   2   3   4   5   6
-6  -5  -4  -3  -2  -1
```
第一行的数字是字符串的正序下标，从0到6， 而第二行给出的是该字符串的相应的负数下标。使用边界下标为i和j分别是用i到j之间的字符组成了一个字符串，可以进行切片。

对于没有负数的切片，切片的长度和指数是有区别的，如果两个都有边界，例如`word[1:3]`的长度是2

如果尝试使用大于字符串长度的数字作为字符串的下标查值，则会返回一个错误
```
>>> word[42]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: string index out of range
```
但是，如果使用一个大于字符串长度的值来进行切片，却是可行的。
```
>>> word[4:42]
'on'
>>> word[42:]
''
>>> word[42: 45]
''
```
虽然字符串可以切片，但是Python的字符串是不变的，因此给字符串的某个下标赋值是会返回错误的。
```
>>> word[0]='J'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment
>>> word[2:] = 'py'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment
```
如果你想要个不同的字符串，自己建一个新的就好了。
```
>>> word[2:] = 'py'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment
>>> 'J' + word[2:]
'Jthon'
```
内置函数`len()`返回的是字符串的长度
```
>>> s = 'supercalifragilisticexpialidocious'
>>> len(s)
34
```
#### 字符串拓展
```
文本序列类型--str
    序列类型的样例是字符串，并且该类型支持字符串的通用操作。
字符串方法
    字符串提供了大量基本转换和查找的方法
格式化的字符串
    字符串可以内嵌方法
格式字符串的语法
    有关格式化字符串的方法`str.format()`
打印类型的字符串格式化
    详细描述了当字符串左边被`%`号修饰时，旧的格式话操作调用的函数
```
#### 3.13 Lists
Python可以识别大量的复合数据类型，用来和其它的值组合在一块。最吊的是list,它是使用中括号将数据集合在一块，并用逗号进行分割值(元素).Lists可能包含不同数据类型的元素，但是通常这些元素都是同一类型的。
```
>>> squares = [1,4,9,16,25]
>>> squares
[1, 4, 9, 16, 25]
```
就像字符串(以及其它的内建的有序类型),lists可以使用指数和分片
```
>>> squares[1]
4
>>> squares[-2]
16
>>> squares[-3:]
[9, 16, 25]
```
所有的分片操作会返回一个新的包含所需的元素的list,这就意味着下面的分片会返回一个浅复制的list
```
>>> squares[:]
[1, 4, 9, 16, 25]
```
Lists也支持像连接一样的操作符
```
>>> squares + [36, 49, 64, 81, 100]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```
不像字符串是值不可变， lists是可变的类型，也就是说可以改变它的内容：
```
>>> cubes = [1, 8, 27, 65, 125] #有个值是错的
>>> 4**3  # 4的立方是64
>>> cubes[3] = 64 # 替换错误的数字
>>> cubes
[1, 8, 27, 64, 125]
```
你也可以在List的末端使用`append()`方法增加新的元素：
```
>>> cubes.append(216)
>>> cubes.append(7**3)
>>> cubes
[1, 8, 27, 64, 125, 216, 343]
```
给切片赋值也是可以的，甚至可以改变整个list的长度或者清楚整个list
```
>>> letters = ['a', 'b', 'c', 'd', 'e', 'f', 'g']
>>> letters
['a', 'b', 'c', 'd', 'e', 'f', 'g']
>>> letters[2:5] = ['C', 'D', 'E']
>>> letters
['a', 'b', 'C', 'D', 'E', 'f', 'g']
>>> letters[2:5] = []
>>> letters
['a', 'b', 'f', 'g']
>>> letters[:] = []
>>> letters
[]
```
