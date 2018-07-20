---
title: DispatchQueue.init 思考
date: 2018-07-20 11:48:03
tags:
---
线程队列的创建同常用下面这个函数创建，这几个参数很有意思，我们来慢慢解读

``` swift
        public convenience init(label: String,
                                  qos: DispatchQoS = default,
                           attributes: DispatchQueue.Attributes = default,
                 autoreleaseFrequency: DispatchQueue.AutoreleaseFrequency = default,
                               target: DispatchQueue? = default)
```

> Parameter: label
> Parameter: qos
> Parameter: attributes

前三个参数请参考，这里讲解的很详细了。 [Swift 3 中的 GCD 与 Dispatch Queue](https://swift.gg/2016/11/30/grand-central-dispatch)

### 参数 autoreleaseFrequency:

由参数名我们可以看，这个参数和内存管理有关

``` swift
    // 类型定义
    public enum AutoreleaseFrequency {
        // 继承？ 继承谁的？
        case inherit
        // 为每个 workItem 创建一个自动释放池
        @available(OSX 10.12, iOS 10.0, tvOS 10.0, watchOS 3.0, *)
        case workItem
        // 从不创建自动释放池
        @available(OSX 10.12, iOS 10.0, tvOS 10.0, watchOS 3.0, *)
        case never
    }
```

> 系统在每个runloop迭代中都加入了自动释放池Push和Pop。[黑幕背后的Autorelease](https://blog.sunnyxx.com/2014/10/15/behind-autorelease/)


但是由于在多线程中 `Runloop` 是属于懒加载的模式，如果不是我们手动调用的话是不会创建 `Runloop` 的。
所以这个参数非常重要，根据定义我们知道 `workItem` 为每个 workItem 创建一个自动释放池， `never` 不会创建自动释放池， 那 `inherit` 呢？


