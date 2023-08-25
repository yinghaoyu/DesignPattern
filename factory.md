# 工厂模式

## 简单工厂模式

```cpp
class Mouse
{
  void sayHi()
  {
    cout << "Hi" << endl;
  }
};

class HpMouse : public Mouse
{
};

class LenovoMouse : public Mouse
{
};

class DellMouse : public Mouse
{
};

class MouseFactory
{
  Mouse *createMouse(int type)
  {
    switch (type)
    {
    case 1:
      return new HpMouse();
    case 2:
      return new LenovoMouse();
    case 0:
    default:
      return new DellMouse();
    }
  }
};

int main()
{
  MouseFactory* mf = new MouseFactory;
  Mouse* m = mf->createMouse(1);
  m->sayHi();
  return 0;
}
```

根据不同的 `type` 来创建不同的鼠标即可。这个模式的缺点很明显，违反了开闭原则，所以我们引入工厂方法

## 工厂方法

在工厂方法中，我们可以定义对应产品的对应方法，我们可以增加工厂的接口：

```cpp
class MouseFactory
{
 public:
  virtual Mouse* createMouse();
};
```

不同类型的鼠标工厂实现这个工厂即可，以 `Dell` 鼠标工厂为例：

```cpp
class DellMouseFactory : public MouseFactory
{
 public:
  Mouse* createMouse() override
  {
    return new DellMouse();
  }
};
```

主函数在调用的时候，直接指定工厂即可制造对应的鼠标了：

```cpp
int main()
{
  MouseFactory* mf = new DellMouseFactory();
  Mouse* m = mf->createMouse();
  m->sayHi();
  return 0;
}
```

工厂方法的优点是符合开闭原则，但是缺点也很明显，就是**在增加子类的时候，同时要增加一个子类的工厂，而且，只支持同一类产品的创建，不适用于同一产品族。**

## 抽象工厂模式

举例，现在需要通过工厂来制造交通工具，如果是现代的工厂，制造的就是汽车，如果是古代的工厂，制造的就是马车，
我们可以先把工厂抽象出来：

```cpp
class AbstractorFactory
{
 protected:
 virtual Transportation* createTransportation();
 virtual WritingInstrument* createWritingInstrument();
};
```

交通工具也可以抽象出来：

```cpp
class Transportation
{
 protected:
  virtual void go();
};
```

对于马车和汽车来说，只需要继承 `Transportation` 类，实现对应的 `go` 方法，以汽车为例：

```cpp
class Car : public Transportation
{
 protected:
  void go() override
  {
    cout << "car go" << endl;
  }
};
```

对于现代工厂还是古代工厂，只需要继承 `AbstractorFactory` 这个抽象类，实现 `createTransportation` 方法即可，
以现代工厂为例：

```cpp
class ModernFactory : public AbstractorFactory
{
 protected:
  Transportation* createTransportation() override
  {
    return new Car();
  }

  WritingInstrument* createWritingInstrument() override
  {
    return new Pen();
  }
};
```

主方法在调用的时候，只需要：

```cpp
int main()
{
  AbstractorFactory* f = new ModernFactory();
  f->createTransportation().go();
  return 0;
}
```
