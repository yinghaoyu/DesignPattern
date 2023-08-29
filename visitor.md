# 访问者模式

访问者模式是一种**行为型**模式。

访问者模式在**结构不变**的情况下动态改变对于内部元素的动作。

假设我们需要构造一台电脑有主板（Board）、CPU、内存（Memory），但是针对企业用户和个人用户，电脑组件的价格不一样的，
我们需要根据不同客户获取一台电脑的总价格。

我们先抽象出电脑组件这个类：

```cpp
class ComputerPart
{
 public:
  virtual void accept(Visitor* visitor);
  virtual int getPrice();
};
```

具体每个组件会继承这个抽象类：

```cpp
class Board : public ComputerPart
{
 public:
 void accept(Visitor* visitor) override
 {
  visitor->visitorBoard(this);
 }

 int getPrice()
 {
  return 20;
 }
};

class CPU : public ComputerPart
{
 public:
 void accept(Visitor* visitor) override
 {
  visitor->visitorCPU(this);
 }

 int getPrice()
 {
  return 200;
 }
};

class Memory : public ComputerPart
{
 public:
 void accept(Visitor* visitor) override
 {
  visitor->visitorMemory(this);
 }

 int getPrice()
 {
  return 11;
 }
};
```

抽象出一个访问者接口：

```cpp

class Visitor
{
 public:
 virtual void visitorCPU(CPU* cpu);
 virtual void visitorBoard(Board* board);
 virtual void visitorMemory(Memory* memory);
};
```

每个具体类型的访问者实现这个接口，然后定义不同的价格策略，以公司访问者为例：

```cpp
class CorpVisitor : public Visitor
{
 private:
  int totalPrice;
 public:
  void visitorCPU(CPU* cpu) override
  {
    totalPrice += cpu->getPrice() - 1;
  }

  void visitorBoard(Board* board) override
  {
    totalPrice += board->getPrice() - 2;
  }

  void visitorMemory(Memory* memory) override
  {
    totalPrice += memory->getPrice() - 3;
  }

  int getTotalPrice()
  {
    return totalPrice;
  }
};
```

个人访问者类似：

```cpp
class PersonalVisitor : public Visitor
{
 private:
  int totalPrice;
 public:
  void visitorCPU(CPU* cpu) override
  {
    totalPrice += cpu->getPrice() + 1;
  }

  void visitorBoard(Board* board) override
  {
    totalPrice += board->getPrice() + 2;
  }

  void visitorMemory(Memory* memory) override
  {
    totalPrice += memory->getPrice() + 3;
  }

  int getTotalPrice()
  {
    return totalPrice;
  }
};
```

主方法调用如下：

```cpp
int main()
{
  ComputerPart* cpu = new CPU();
  ComputerPart* memory = new Memory();
  ComputerPart* board = new Board();
  PersonalVisitor* personalVisitor = new PersonalVisitor();
  cpu->accept(personalVisitor);
  memory->accept(personalVisitor);
  board->accept(personalVisitor);
  cout << personalVisitor->getTotalPrice() << endl;
  return 0;
}
```

可以看到，不同的访问者，对于电脑的价格是不一样的。
