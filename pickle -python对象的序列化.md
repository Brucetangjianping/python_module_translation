pickle 模块实现了将二进制协议（binary protocols） 序列化和反序列化成一个python对象结构。'Pickling'是一个python层次结构对象序列化为字节流（byte stream）的过程,而'unpickling'是其相反的过程，是将字节流反序列化成一个python层次结构对象。Pickling（或者unpickling）可以被称为'serialization'、'marshalling'、或者是‘flattening’；但是，为了避免混淆，这里使用的术语为pickling和unpickling  

```
Warning: pickle模块无法防止错误或恶意构造的数据不被序列化， 没有反序列化过的数据可能来自不被信任或者没有认证的源
```

## 与python其它模块的关系   
#### 与marshal的比较  
Python有个更原始的序列化模块叫做marshal,但是一般情况下，pickle应该成为序列化python对象的首选方式，marshal的存在主要是处理python的.pyc文件    
不同点：    
* pickle会记录序列化过的对象，下次引用相同的对象时就不需要再序列化了
