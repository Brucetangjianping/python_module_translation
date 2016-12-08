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

## 模块接口
序列化对象层次结构，您只需要调用dumps()函数，类似的，反序列化数据流，你可以调用loads()函数，但是，如果您想更好的操作序列化和反序列化，您可以分别创建一个Pickler和Unpickler对象。
pickle模块提供了以下常量：
#### pickle.HIGHEST_PROTOCOL
    一个整形
