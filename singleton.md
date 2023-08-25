# 单例模式

单例模式是创建型模式。

> “一个类只允许创建唯一一个对象（或者实例），那这个类就是一个单例类，这种设计模式就叫作单例设计模式，简称单例模式。”定义中提到，“一个类只允许创建唯一一个对象”。那对象的唯一性的作用范围是指进程内只允许创建一个对象，也就是说，单例模式创建的对象是进程唯一的（而非线程）

## 为什么要使用单例

1. 处理资源访问冲突，比如写日志的类，如果不使用单例，就必须使用锁机制来解决日志被覆盖的问题。

2. 表示全局唯一类，比如配置信息类，在系统中，只有一个配置文件，当配置文件加载到内存中，以对象形式存在，也理所应当只有一份；
唯一 ID 生成器也是类似的机制。如果程序中有两个对象，那就会存在生成重复 ID 的情况，所以，我们应该将 ID 生成器类设计为单例。

## 饿汉式

在类加载时就被创建，不管是否需要使用这个实例。因此比较浪费资源。

```cpp
template <typename T>
class Singleton
{
 private:
static T v;

 public:
  static T *getInstance()
  {
    return &v;
  }
};
```

## 懒汉式

实例对象在第一次使用时才被创建。

```cpp
template <typename T>
class Singleton
{
 public:
  static T *getInstance()
  {
    // C++11 开始线程安全
    static T v;
    return &v;
  }
};
```

## 线程单例式

见 [Muduo Singleton](https://github.com/chenshuo/muduo/blob/master/muduo/base/Singleton.h)
