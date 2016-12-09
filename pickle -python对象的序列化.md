pickle 模块实现了将二进制协议（binary protocols） 序列化和反序列化成一个python对象结构。'Pickling'是一个python层次结构对象序列化为字节流（byte stream）的过程,而'unpickling'是其相反的过程，是将字节流反序列化成一个python层次结构对象。Pickling（或者unpickling）可以被称为'serialization'、'marshalling'、或者是‘flattening’；但是，为了避免混淆，这里使用的术语为pickling和unpickling  

```
Warning: pickle模块无法防止错误或恶意构造的数据不被序列化， 没有反序列化过的数据可能来自不被信任或者没有认证的源
```

## 与python其它模块的关系   
#### 与marshal的比较  
Python有个更原始的序列化模块叫做marshal,但是一般情况下，pickle应该成为序列化python对象的首选方式，marshal的存在主要是处理python的.pyc文件    
不同点：    
* pickle会记录序列化过的对象，下次引用相同的对象时就不需要再序列化了
* pickle同样适用于递归和共享对象，递归对象包含了对自己对象的引用，这些是不能使用marshal进行处理的，强行使用marshal的话会导致python解释器崩溃，而对象共享发生在对象被序列化的时候，不同地方的多个引用却指向了相同的对象。pickle对于这些对象只会存储一次，并且确保其它所有的引用会指向这个主副本（the master copy）。共享对象仍能可以共享，这对于可变的对象来说非常重要
* marshal不能序列化用户自定义的类和类的实例，pickle却显然可以对类实例的存储和恢复，但是前提是这些自定义的类必须可以被import，并且当对象被存储时得和import的类处于相同的模块中
* marshal序列化的格式不能保证能在不同版本的python中使用，因为它的主要工作是为了序列化.pyc文件，python的编写者认为这种无法向后兼容的序列化格式很菜，所以pickle能保证向后兼容python版本的序列化格式横空出世  

#### 与json的比较
 pickle和JSON(JavaScript Object Notation)本质上的不同：
 * JSON是文本序列化格式，(它输出的是unicode的文本，尽管大多数时间编码成utf-8的形式)，而pickle是二进制序列化格式
 * JSON具有可读性，而pickle不能
 * JSON可以在python生态系统外被其它语言广泛使用，而pickle只能在python中使用
 * JSON,在默认情况下，只能表示python内置类型中的一个子类，并且没有定制类；而pickle可以代表非常多的python类型（而大多数的这些类型可以自动的，并且巧妙的被python内省工具使用），复杂情况下可以特殊的API实现解决问题

## 数据流格式
pickle使用的数据格式是python特有的，这正是它的一个优点，不需要强迫使用外部的标准，如JSON或者XDR(无法指针共享)，但这也意味着非python的项目可能无法pickle成python对象
默认情况下， pickle的数据格式是一个相对紧凑的二进制，如果你想要更佳的文件尺寸，你可以对pickle过的数据进行有效的压缩
pickletools模块包含了由pickle生成的分析数据流的工具，pickletools源代码有大量关于使用pickle协议的的操作码的评论
现在有五个不同的协议可以使用pickle序列化，使用的协议等级越高，就越需要使用最新的python版本
* Protocol version 0 是原始的可读性协议，并且能够兼容python的早期版本
* Protocol version 1 是一个老的二进制格式，并且也能够兼容python的早期版本
* Protocol version 2 是python2.3版本引进的，它提供了更加高效的pickling的新型类，参考PEP 307查看关于python 2版本带来的改进信息
* Protocol version 3 加入了python3.0，它对python2.x版本无法反序列化字节对象有了更好的支持，这也是python3.0默认协议，并且也是当需要兼容python 3 其它版本的时候强烈推荐的协议
* Protocol version 4 是python 3.4新增的，它新增了对大型对象的支持，可以pickling更多种类的对象，并且可以对一些数据格式的进行优化。参考PEP 3154查看更多关于Protocol version 4有关的内容

```
NOTE： 序列化是比持久化更原始的概念，尽管pickle只是读写文件对象，并不处理命名持久化对象的问题，也不（或者说以一种更复杂的方式）处理并发访问持久化对象的问题，pickle可以将一个复杂的对象序列化成一个二进制流，也可以将二进制流反序列化成一个具有相同内部结构的对象。也许最显而易见的处理二进制流的方式就是将这些流写入文件中，也可以通过网络进行传输这些流或者将他们保存在数据库中。shelve模块提供了一个简单的接口处理在DDM-style数据库文件中pickle和unpickle对象

```

## 模块接口:
序列化对象层次结构，您只需要调用dumps()函数，类似的，反序列化数据流，你可以调用loads()函数，但是，如果您想更好的操作序列化和反序列化，您可以分别创建一个Pickler和Unpickler对象。<br/>
<br/>
pickle模块提供了以下常量：<br/>

pickle.__HIGHEST_PROTOCOL__: 
* 一个整型值，最高的可用协议版本，这个整型值可以和Pickler的构造函数一样，当作一个协议的值传递给dump()和dumps()函数<br/>

pickle.__DEFAULT_PROTOCOL__:
* 一个整型值，序列化的默认协议版本，可能比HIGHEST_PROTOCOL值小，当前的默认协议为3，为Python 3设计的新协议<br/>
 
pickle模块提供了以下几个函数可以更便捷的实现序列化

pickle.__dump__(obj, file, protocol=None, *, fix_imports=True):
* 将一个序列化过的对象(obj参数)写入到文件中（使用open打开的文件），这个和Pickler(file, protocol).dump(obj)是等价的<br/>
* 可选参数Protocol是一个整型值，告诉pickler使用给定的协议等级，pickler支持从0到最高等级的协议，如果没有指定，就使用默认的DEFAULT_PROTOCOL协议，如果指定的是一个负数，那么pickler将会选择HIGHEST_PROTOCOL.<br/>
* file参数必须能执行接收一个单字节参数的write()方法，它可以是磁盘上的文件，为了写入二进制而打开，也可以是io.BytesIO的实例，或者是其它自定义并且满足这个接口的对象<br/>
* 如果fix_imports为True，并且Protocol的值小于3，pickle会试着将Python 3中的names映射成python 2中使用的names,这样序列化后的对象可以被Python 2读取<br/>

pickle.__dumps__(obj, protocol=None, *, fix_imports=True):
* 将序列化后的对象（obj）当做一个字节对象返回，而不是写入到一个文件中
* 参数Protocol和fix_imports的含义与dump是一样的<br/>  

pickle.__load__(file, *, fix_imports=True, encoding="ASCII", errors="strict"):
* 从一个open的文件中读取序列化后的对象，并且返回被重组的对象层次结构，等价于Unpickler(file).load()
* pickle会自动检测协议版本，所以并不需要protocol参数，超过的序列化的对象二进制字节将会被忽略
* file必须可以执行两个方法，一个是需要整型参数的read方法，一个是无参的readline()方法，这两个方法都返回字节（bytes）, 因此file是磁盘上的一个可读取二进制的文件，一个io.BytesIO对象，或者其它任意的自定义满足此接口的对象
* fix_imports、encoding和errors为可选的关键字参数，这些参数是用来处理由Python 2的序列化生成的流的兼容问题，如果fix_imports为True,pickle会将Python 2中的names映射成Python 3使用的names， encoding和errors告知pickler如何处理被Python 2序列化的八比特字符串实例，它们的默认值分别是'ASCII' 和'strict'， encoding可以将这些八比特的字符串实例当做字节对象进行读取<br/>

pickle.__loads__(bytes_object, *, fix_imports=True, encoding="ASCII", errors="strict"):
* 从字节对象中读取反序列化后的对象层次结构，并返回相应的对象
* pickle会自动检测协议版本，所以并不需要protocol参数，超过的序列化的对象二进制字节将会被忽略
* fix_imports、encoding和errors为可选的关键字参数，这些参数是用来处理由Python 2的序列化生成的流的兼容问题，如果fix_imports为True,pickle会将Python 2中的names映射成Python 3使用的names， encoding和errors告知pickler如何处理被Python 2序列化的八比特字符串实例，它们的默认值分别是'ASCII' 和'strict'， encoding可以将这些八比特的字符串实例当做字节对象进行读取<br/>

pickled定义了三个异常：

exception.pickle.__PickleError__:<br/>
&ensp;&ensp;&ensp;&ensp;其它序列化异常的共同父类，同时它也是继承于Exception

exception.pickle.__PicklingError__:<br/>
&ensp;&ensp;&ensp;&ensp;当pickler遇到了无法序列化的对象时，抛出该异常。它继承于PickleError
&ensp;&ensp;&ensp;&ensp;参考 What can be pickled and unpickled?查看那些对象无法序列化

exception.pickle.__UnpicklingError__:<br/>
&ensp;&ensp;&ensp;&ensp;当对象无法被反序列化时，例如：数据损坏，加密文件，则抛出该异常。它继承于PickleError
&ensp;&ensp;&ensp;&ensp;注意：序列化过程中也可能抛出其它异常，包含AttributeErro、EOFError、ImportError和IndexError

pickle模块会输出两个类，Pickler和Unpickler

class pickle.__Pickler__(file, protocol=None, *, fix_imports=True):
* 此类可以将序列化后的数据流写入二进制文件中
* 可选参数Protocol是一个整型值，告知pickler使用给定的协议等级，pickler支持从0到最高等级的协议，如果没有指定，就使用默认的DEFAULT_PROTOCOL协议，如果指定的是一个负数，那么pickler将会选择HIGHEST_PROTOCOL.
* file参数必须能执行接收一个单字节参数的write()方法，它可以是磁盘上的文件，为了写入二进制而打开，也可以是io.BytesIO的实例，或者是其它自定义并且满足这个接口的对象
* 如果fix_imports为True，并且Protocol的值小于3，pickle会试着将Python 3中的names映射成python 2中使用的names,这样序列化后的对象可以被Python 2读取

__dump__(obj):<br/>
&ensp;&ensp;&ensp;&ensp;将obj参数表示的对象写入构造函数中给定的文件中

__persistent_id__(obj):<br/>
* 默认不做任何事，为了子类可以重写而存在
* 如果persistent_id函数返回None，则正常对obj进行序列化，若是返回其它的值作为obj的persistent id，并且这个persistent * id的含义已经在Unpickler.persistent_load()中定义了。注意这个方法返回的值自身不能有一个persistent ID

__dispatch_table__:<br/>
* pickler序列化对象的dispatch table是可以被声明使用copyreeg.pickle()的reduction函数的注册表, dispatch_table是一个mapping,它的key值是类，而value值是reduction函数。 reduction函数需要相关类的一个参数，并且符合__reduce__()方法的相同条件
* 默认情况下，一个pickler对象不会有dispatch_table属性，而是使用copyreg模块管理的全局dispatch_table。但是，当需要自定*义序列化一个特殊对象时，可以在一个字典对象中设置dispatch_table属性。当Pickler的子类有dispatch_table属性时，那么这将&ensp;&ensp;&ensp;&ensp;成为该子类实例的默认dispatch_table。

__fast__:<br/>
&ensp;&ensp;&ensp;&ensp;已弃用。当设置True值时，启用fast模式。而fast模式将禁用memo的使用，因此不生成多余的PUT操作码，但是<br/>   
&ensp;&ensp;&ensp;&ensp;不适用于自我参照的对象（self-referential Object）,否则将会导致Pickler的无限递归操作。

class pickle.Unpickler(file, *, fix_imports=True, encoding="ASCII", errors="strict"):<br/>
* 该类需要一个二进制文件来读取序列化的数据流
* pickle会自动检测协议版本，因此无需protocol参数
* file必须可以执行两个方法，一个是需要整型参数的read方法，一个是无参的readline()方法，这两个方法都返回字节（bytes）, 因此file是磁盘上的一个可读取二进制的文件，一个io.BytesIO对象，或者其它任意的自定义满足此接口的对象
* fix_imports、encoding和errors为可选的关键字参数，这些参数是用来处理由Python 2的序列化生成的流的兼容问题，如果fix_imports为True,pickle会将Python 2中的names映射成Python 3使用的names， encoding和errors告知pickler如何处理被Python 2序列化的八比特字符串实例，它们的默认值分别是'ASCII' 和'strict'， encoding可以将这些八比特的字符串实例当做字节对象进行读取<br/> 

__load()__: <br/>
&ensp;&ensp;&ensp;&ensp;从构造器中的初始化的文件对象中读取序列化的数据，并返回相应的反序列化对象，忽略对象多余的字节

__persistent_load(pid)__: <br/>
* 默认抛出UnpicklingError异常
* pid已经定义了的话，则persistent_load会返回pid所表示的persistent id所表示的特定对象，如果是一个不合法的persistentr ID，则会抛出UnpicklingError异常

__find_class(module,name)__:<br/>
* 当需要import一个模块时，返回该模块中的对象的名称，并且该模块和name参数是字符串对象，注意，不要望文生义，find_class也可以用于查找方法。
* 子类可能重写这个方法来加强对对象类型以及对象怎样加载的控制，来减少潜在的安全问题。

## 什么可以被序列化和反序列化？
下面的类型可以被序列化:
* None, True以及False
* 整型数、浮点型数、复数
* 字符串、字节（bytes）、字节数组(bytearrays)
* 元组（tuple）、列表（lists）、集合（sets）以及只包含可序列化对象的字典
* 使用模块中最高等级的定义方式定的的函数（使用def，而不是lambda）
* 使用模块中最高等级的定义方式定义的内置函数
* 使用模块中最高等级定义方式定义的类
* 类的实例中__dict__或者__getstat__()方法是可序列化的

如果尝试序列化不能序列化的对象将会抛出PicklingError异常；当该异常发生时，一些未知的字节可能已经写入文件中了。如果你正在序列化一个高度递归的数据结构，可能会导致超出递归的最大深度，然后抛出ResursionError异常，你可以通过sys.setrecursionlimit()设置递归长度来防止抛出异常

注意：函数（无论是内置的还是用户定义的）序列化的是它可以序列化的命名参数，而不是它的值。这就意味着只有该函数名称，以及该函数所在模块的名称被序列化。而不是函数的代码，或者函数的属性。所以模块必须在反序列化环境中可以被import,并且该模块必须包含模块名对象,否则将会抛出异常

类似地，类也是序列化类名参数，所以有与上述一样的限制。注意：并不是类中的代码或者是数据被序列化，所以在下面的例子中类属性attr并没有存储在反序列化环境中

```
class Foo:
    attr = 'A class attribute'
picklestring = pickle.dumps(Foo)
```
