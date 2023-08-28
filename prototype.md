# 原型模式

原型模式数**创建型**模式。

如果对象的创建成本比较大，而同一个类的不同对象之间差别不大（大部分字段的值都相同），在这种情况下，
我们可以利用对已有对象（原型）进行复制（或者叫拷贝）的方式来创建新对象，以达到节省创建时间的目的。

实际上，创建对象包含的申请内存，给成员变量赋值这一过程，本身并不需要花费太多时间，或者对大部分业务系统来说，
这点时间完全是可以忽略的。应用一个复杂的模式，只得到一点点的性能提升，这就是所谓的过度设计，得不偿失。
但是，如果对象中的数据需要经过复杂的计算才能得到（比如排序、计算hash），或者要从 `RPC`、网络、数据库、文件系统等
非常慢的 `I/O` 读取，每次读取的代价非常高，在这种情况下，我们可以利用原型模式，从其他已有的对象拷贝得到。

典型的应用是对象的克隆方法，示例如下：

```cpp
#include <iostream>
#include <string>

// "抽象原型" 接口
class Animal
{
 public:
  virtual ~Animal() {}
  virtual Animal *clone() const = 0;
  virtual void sound() const = 0;
};

// "具体原型" 实现
class Cat : public Animal
{
 public:
  Animal *clone() const override { return new Cat(*this); }

  void sound() const override { std::cout << "喵喵喵..." << std::endl; }
};

class Dog : public Animal
{
 public:
  Animal *clone() const override { return new Dog(*this); }

  void sound() const override { std::cout << "汪汪汪..." << std::endl; }
};

class Bird : public Animal
{
 public:
  Animal *clone() const override { return new Bird(*this); }

  void sound() const override { std::cout << "啾啾啾..." << std::endl; }
};

int main()
{
  // 创建原型对象
  Animal *catPrototype = new Cat();
  Animal *dogPrototype = new Dog();
  Animal *birdPrototype = new Bird();

  // 克隆对象
  Animal *cat = catPrototype->clone();
  Animal *dog = dogPrototype->clone();
  Animal *bird = birdPrototype->clone();

  cat->sound();   // 输出：喵喵喵...
  dog->sound();   // 输出：汪汪汪...
  bird->sound();  // 输出：啾啾啾...

  return 0;
}
```
