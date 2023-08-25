# 设计原则

## 开闭原则

> 对扩展开放，对修改关闭，降低维护来带的新风险

先看一个不符合开闭原则的代码，每次增加一种类型，都需要增加一个 `if` 条件判断（修改），如下：

```cpp
#include <iostream>

using namespace std;

class Shape
{
 public:
  int m_type;
};

class GraphicEditor
{
 public:
  void draw(Shape *s)
  {
    if (s->m_type == 1)
    {
      drawRectangle();
    }
    else if (s->m_type == 2)
    {
      drawCircle();
    }
    // ... 每增加一种类型，这里的代码就要加一个 if 分支，不符合开闭原则
  }

  void drawRectangle() { cout << "画长方形" << endl; }

  void drawCircle() { cout << "画圆形" << endl; }
};

class Rectangle : public Shape
{
  Rectangle() { m_type = 1; }
};

class Circle : public Shape
{
  Circle() { m_type = 2; }
};
```

要调整为符合开闭原则的代码，需要将 `Share` 抽象为接口，
`GraphicEditor` 中的 `draw` 方法中直接传入 `Shape` 这个接口类型，
并调用其 `draw` 方法，每次增加一种 `Shape` 类型，只需要增加一个类，并实现 `Shape` 接口即可，
这样就实现了对扩展开放，而且不需要修改 `GraphicEditor` 中的 `draw` 方法内容，实现了对修改关闭，修改后代码如下：

```cpp
#include <iostream>

using namespace std;

class Shape
{
 public:
  Shape() {}
  virtual ~Shape() {}
  virtual void draw() = 0;
};

class Rectangle : public Shape
{
 public:
  void draw() override { cout << "画矩形" << endl; }
};

class Circle : public Shape
{
 public:
  void draw() override { cout << "画圆形" << endl; }
};

class GraphicEditor
{
  void draw(Shape *s) { s->draw(); }
};
```

## 依赖倒置

> 高层不应该依赖低层，这样有利于代码结构的升级拓展

先看一段不符合依赖倒置的代码，`driver` 方法参数里面写了具体车的实现，
如果以后要换一种车，只能修改代码实现。

```cpp
#include <iostream>
#include <string>

using namespace std;

class Benz
{
 public:
  void run() { cout << "奔驰车跑起来了" << endl; }
};

class Driver
{
 public:
  Driver(const string &name) { m_name = name; }
  // driver方法参数里面写了具体的车的实现，如果以后要换一种车，只能修改代码实现
  // 不符合依赖倒置原则
  void driver(Benz *benz) { benz->run(); }

 private:
  string m_name;
};

int main()
{
  Benz *benz = new Benz();
  Driver *driver = new Driver("张三");
  driver->driver(benz);
  return 0;
}
```

调整后的代码如下，因为 `driver` 方法的参数变成了抽象的 `ICar` 类型，
所以，所有实现 `ICar` 类型的车都可以调用这个方法，这样就实现了高层不依赖低层。

```cpp
#include <iostream>
#include <string>

using namespace std;

class ICar
{
 public:
  ICar() {}
  virtual ~ICar() {}
  virtual void run() = 0;
};

class Benz : public ICar
{
 public:
  void run() override { cout << "奔驰跑起来了" << endl; }
};

class BMW : public ICar
{
 public:
  void run() override { cout << "宝马跑起来了" << endl; }
};

class IDriver
{
 public:
  IDriver() {}
  virtual ~IDriver();
  virtual void driver(ICar *car) = 0;
};

class Driver : public IDriver
{
 public:
  void driver(ICar* car) { car->run(); }
};

int main()
{
  IDriver* driver = new Driver();
  driver->driver(new Benz());
  driver->driver(new BMW());
  return 0;
}
```

## 单一职责

> 一个类只干一件事，提高代码的可读性

举个例子，一般来说，系统只有登录，注册，注销功能，可以写成如下形式：

```cpp
#include <iostream>

using namespace std;

class UserInfo
{
};

class UserOperator
{
 public:
  virtual void login(UserInfo *info);
  virtual void regist(UserInfo *info);
  virtual void logout(UserInfo *info);
};

class UserOperatorImpl : public UserOperator
{
 public:
  void login(UserInfo *info) override
  {
    // 用户登录
  }

  void regist(UserInfo *info) override
  {
    // 用户注册
  }

  void logout(UserInfo *info) override
  {
    // 用户登出
  }
};
```

以上设计针对各个方法都不是很复杂的情况，如果每个方法都比较复杂，可以考虑独立成类来做，示例如下：

```cpp
class Register
{
 public:
  virtual void regist();
};

class Login
{
 public:
  virtual void login();
};

class Logout
{
 public:
  virtual void logout();
};

class RegisterImpl : public Register
{
 public:
  void regist() override
  {
    // 用户注册
  }
};

class LoginImpl : public Login
{
 public:
  void login() override
  {
    // 用户登录
  }
};

class LogoutImpl : public Logout
{
 public:
  void logout() override
  {
    // 用户登出
  }
};
```

这就实现了单一职责原则。

## 接口隔离

> 一个接口只干一件事，功能解耦，高聚合，低耦合

举个例子，学生成绩管理程序一般包含查询成绩，新增成绩，删除成绩，修改成绩，计算总分，计算平均分，打印成绩信息等功能，
如果都写在一个类里面，就会变成如下形式：

```cpp
class IStudentScore
{
 public:
  // 查询成绩
  void queryScore();

  // 修改成绩
  void updateScore();

  // 添加成绩
  void saveScore();

  // 删除成绩
  void del();

  // 计算总分
  double sum();

  // 计算平均分
  double avg();

  // 打印成绩单
  void printScore();
};
```

这样的方式不利于扩展. 比如:

学生只有查看成绩,打印成绩单的权限, 没有增删改的权限;

老师拥有所有的权限。

采用接口隔离原则设计，可以做如下改进：

```cpp
class IQueryScore
{
 public:
  // 查询成绩
  void queryScore();

  // 打印成绩单
  void printScore();
};

class IOperateScore
{
 public:
  // 修改成绩
  void updateScore();

  // 添加成绩
  void saveScore();

  // 删除成绩
  void del();

  // 计算总分
  double sum();

  // 计算平均分
  double avg();
};
```

分为查询接口和操作接口，这样学生端就不需要重写和他不相关的接口了。

## 迪米特法则

> 不该知道的不要知道，减少代码臃肿

示例：一个人用咖啡机煮咖啡的过程，例子中只有两个类，一个是人，一个是咖啡机。

首先是咖啡机类 `CoffeeMachine` ，咖啡机制作咖啡只需要三个方法

第一步：加咖啡豆；

第二步：加水；

第三步：制作咖啡。

如果把咖啡机的所有方法暴露给人调用，就会出现如下代码：

```cpp
// 咖啡机抽象接口
class ICoffeeMachine
{
 public:
  // 加咖啡豆
  virtual void addCoffeeBean();

  // 加水
  virtual void addWater();

  // 制作咖啡
  virtual void makeCoffee();
};

// 咖啡机实现
class CoffeeMachine : public ICoffeeMachine
{
 public:
  void addCoffeeBean() override { cout << "放咖啡豆" << endl; }

  void addWater() override { cout << "放水" << endl; }

  void makeCoffee() override { cout << "制作咖啡" << endl; }
};

// 人
class IMan
{
 public:
  // 制作咖啡
  virtual void makeCoffee();
};

class Man : public IMan
{
 private:
  ICoffeeMachine *m_cm;

 public:
  Man(ICoffeeMachine *cm) { m_cm = cm; }

  void makeCoffee() override
  {
    m_cm->addWater();
    m_cm->addCoffeeBean();
    m_cm->makeCoffee();
  }
};

// 客户端
class Client
{
  void main()
  {
    ICoffeeMachine *cm = new CoffeeMachine();
    IMan *man = new Man(cm);
    man->makeCoffee();
  }
};
```

其实人根本不关心咖啡机具体制作咖啡的过程。所以我们可以作如下优化：

优化后的咖啡机类，只暴露一个 `work()` 方法，把制作咖啡的三个具体的方法`addCoffeeBean()`、`addWater()`、`makeCoffee()`设为私有，`work()`方法封装了这三个具体方法的实现。

```cpp
// 咖啡机抽象接口
class ICoffeeMachine
{
 public:
  // 咖啡机工作
  virtual void work();
};

// 咖啡机实现
class CoffeeMachine : public ICoffeeMachine
{
 public:
  void work() override
  {
    addCoffeeBean();
    addWater();
    makeCoffee();
  }

 private:
  void addCoffeeBean() { cout << "放咖啡豆" << endl; }

  void addWater() { cout << "放水" << endl; }

  void makeCoffee() { cout << "制作咖啡" << endl; }
};

// 人
class IMan
{
 public:
  // 制作咖啡
  virtual void makeCoffee();
};

class Man : public IMan
{
 private:
  ICoffeeMachine *m_cm;

 public:
  Man(ICoffeeMachine *cm) { m_cm = cm; }

  void makeCoffee() override { m_cm->work(); }
};

// 客户
class Client
{
  void main()
  {
    ICoffeeMachine *cm = new CoffeeMachine();
    IMan *man = new Man(cm);
    man->makeCoffee();
  }
};
```

通过减少 `CoffeeMachine` 对外暴露的方法，减少 `Man` 对 `CoffeeMachine` 的了解，从而降低了它们之间的耦合。

## 里氏替换原则

> 子类重写的方法发生改变，不应该影响父类方法的含义，防止继承泛滥。

比如以下代码，就不符合里氏替换原则

```cpp
class A
{
 public:
  virtual int sub(int a, int b) { return a - b; }
};

class Client
{
 public:
  void main()
  {
    A *a = new A();
    cout << "100 - 50 = " << a->sub(100, 50) << endl;
    cout << "100 - 80 = " << a->sub(100, 80) << endl;
  }
};
```

接下来需要增加一个新的功能；完成两数相加，然后再与 `100` 求和，由类 `B` 来负责，
即类 `B` 需要完成两个功能：

1. 两数相减。

2. 两数相加，然后再加100。

由于类 `A` 已经实现了第一个功能，所以类 `B` 继承类 `A` 后，只需要再完成第二个功能即可：

```cpp
class B : public A
{
 public:
  int sub(int a, int b) override
  {
    // 重写了父类的方法，发生了功能改变，影响了父类方法的含义
    return a + b;
  }

  int incHandurd(int a, int b) { return sub(a, b) + 100; }
};

class Client
{
 public:
  void main()
  {
    B *b = new B();
    cout << "100 - 50 = " << b->sub(100, 50) << endl;
    cout << "100 + 20 + 100 = " << b->incHandurd(100, 20) << endl;
  }
};
```

可以发现原本运行正常的相减功能发生了错误。

原因就是类 `B` 在给方法起名时无意中重写了父类的方法，造成所有运行相减功能的代码全部调用了类 `B` 重写后的方法，
造成原本运行正常的功能出现了错误。

在本例中，引用基类 `A` 完成的功能，换成子类 `B` 之后，发生了异常。

在实际编程中，我们常常会通过重写父类的方法来完成新的功能，这样写起来虽然简单，但是整个继承体系的可复用性会比较差，特别是运用多态比较频繁时，程序运行出错的几率非常大。

如果非要重写父类的方法，比较通用的做法是：**原来的父类和子类都继承一个更通俗的基类，原有的继承关系去掉，采用依赖、聚合，组合等关系代替。**
