# 装饰器模式

装饰者模式是一种**结构型**模式。

顾名思义，就是对某个方法或者对象进行装饰，举个简单的例子，有个圆形类 `Circle`，我需要把这个圆形涂上红色，
其实就是新增一个装饰器来装饰这个圆形类。

如果要让装饰器通用一些，可以处理圆形对应的抽象类 `Sharp`，那么对于任意的 `Sharp` 的子类，都可以用红色装饰器来吐红色。

示例代码如下：

我们先定义 `Sharp` 这个抽象类

```cpp
class Sharp
{
 protected:
  virtual void draw();
}
```

然后我们定义 `Sharp` 的装饰类 `SharpDecorator`，这个类是所有装饰器类的抽象类，
后续装饰器只需要实现这个抽象类就可以对 `Sharp` 进行各种装饰了

```cpp
class SharpDecorator : public Sharp
{
 protected:
  Sharp* m_dec;
 public:
  SharpDecorator(Sharp* dec)
  {
    m_dec = dec;
  }
};
```

红色装饰器实现这个抽象类即可：

```cpp
class RedSharpDecorator : public SharpDecorator
{
 public:
  RedSharpDecorator(Sharp* dec) : SharpDecorator(dec) {}

 private:
  static void redIt()
  {
    cout << "[RED]" << endl;
  }

 protected:
  void draw() override
  {
    redIt();
    m_dec->draw();
    redIt();
  }
};
```

主函数调用如下：

```cpp
class Circle : public Sharp
{
 protected:
  void draw() override
  {
    cout << "Circle" << endl;
  }
};

class Rectangle : public Sharp
{
 protected:
  void draw() override
  {
    cout << "Rectangle" << endl;
  }
};

int main()
{
  (new RedSharpDecorator(new Circle()))->draw();
  (new RedSharpDecorator(new Rectangle()))->draw();
  return 0;
}
```

归功于与原始类继承同样的父类，如果我们要将圆形先染红，再将圆内加上标签，实现嵌套装饰，
可以再声明一个 `TagSharpDecorator` 类：

```cpp
class TagSharpDecorator : public SharpDecorator
{
 public:
  TagSharpDecorator(Sharp* dec) : SharpDecorator(dec) {}

 private:
  static void tagIt()
  {
    cout << "[Tag]" << endl;
  }

 protected:
  void draw() override
  {
    tagIt();
    m_dec->draw();
    tagIt();
  }
};

int main()
{
  (new RedSharpDecorator(new TagSharpDecorator(new Circle())))->draw();
  return 0;
}
```

说明：

1. 装饰器类和原始类继承同样的父类，这样我们可以对原始类“嵌套”多个装饰器类。
2. 装饰器类是对功能的增强，这也是装饰器模式应用场景的一个重要特点。符合“组合关系”这种代码结构的设计模式有很多，
比如代理模式、桥接模式，还有现在的装饰这模式。尽管它们的代码结构相似，但是每种设计模式的意图是不同的。
就拿比较相似的代理模式和装饰器模式来说：
代理模式中，代理类附加的是跟原始类无关的功能；
装饰器模式中，装饰器附加的是跟原始类相关的增强功能。
