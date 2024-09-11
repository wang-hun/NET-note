
# .NET进阶:lazy、线程安全与特殊字典

## ***Part *0**** 你所不知的特殊字典📑

在日常的开发中，论使用率最高的容器，List和Dictionary数一数二🏆。

这里主要想讲讲作为初学者的你，可能不知道的字典。

> + ConcurrentDictionary<TKey, TValue>：这是一个线程安全的字典实现，允许多个线程同时读写而不需要额外的同步机制。它在多线程环境中非常有用，可以提高性能并减少锁的开销。例如，如果你需要在多个线程之间共享字典数据，ConcurrentDictionary 是一个很好的选择。
> + ReadOnlyDictionary<TKey, TValue>：这个类提供了一个只读的字典视图，一旦创建，就不能修改字典的内容。这在你需要防止字典被修改，但又想提供字典内容的访问时非常有用。例如，你可以将一个可变的字典转换为只读字典，然后将其传递给不需要修改字典的组件或方法。
> + SortedDictionary<TKey, TValue>：这个字典类型按照键的顺序存储元素，并且可以在O(log n)时间内进行查找、插入和删除操作。它适合于需要按键的顺序访问元素的场景。

这里我们展开聊聊前两种。

## ***Part *1**** Concurrent字典💯

刚才已经提过了，这是一个线程安全字典。你使用多线程或者异步操作时，字典内部有一套同步机制，会防止死锁🔗。

这里聊一些比较常用的扩展方法。以下我们将TValue称做值🔓，Tkey称做键🔑。
1. GetOrAdd
   顾名思义，同时包含了Get和Add的一个方法，返回值是对应的TValue，也就是你定义字典的值的类型。这个方法会先试图查询字典获取对应值，如果没有则将你给出的一个新值插入字典。  
   <br/>
   这个方法有三个重载:
   > - GetOrAdd(TKey key, TValue value)
   > - GetOrAdd(TKey key, Func<TKey, TValue> valueFactory)
   > - GetOrAdd<TArg>(TKey key, Func<TKey, TArg, TValue> valueFactory, TArg factoryArgument)：