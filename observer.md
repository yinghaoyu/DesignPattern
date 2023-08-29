# 观察者模式

观察者模式是一种**行为型**模式。在对象之间定义一个一对多的依赖，当一个对象状态改变的时候，
所有依赖的对象都会自动收到通知。

一般可以用作事件处理往往和责任链模式搭配使用。

```cpp
#include <algorithm>
#include <iostream>
#include <vector>

class Observer
{
 public:
  virtual void update() = 0;
};

class ConcreteObserver : public Observer
{
 public:
  void update() override
  {
    std::cout << "ConcreteObserver received an update." << std::endl;
    // 观察者根据具体需求执行相应的操作
  }
};

class Subject
{
 private:
  std::vector<class Observer *> observers;

 public:
  void attach(Observer *observer) { observers.push_back(observer); }

  void detach(Observer *observer)
  {
    // 在实际应用中可能需要更复杂的逻辑来删除观察者
    observers.erase(std::remove(observers.begin(), observers.end(), observer),
                    observers.end());
  }

  void notify()
  {
    for (auto observer : observers)
    {
      observer->update();
    }
  }
};

int main()
{
  Subject subject;
  ConcreteObserver observer1;
  ConcreteObserver observer2;

  subject.attach(&observer1);
  subject.attach(&observer2);

  // 假设主题发生变化，需要通知观察者
  subject.notify();

  subject.detach(&observer1);

  // 再次触发通知
  subject.notify();

  return 0;
}
```

观察者模式允许主题和观察者之间的松耦合关系，当主题发生变化时，观察者可以灵活地做出相应。
