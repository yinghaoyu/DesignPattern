# 代理模式

代理模式是**结构型模式**。

```cpp
#include <iostream>

// 抽象主题接口
class Subject
{
 public:
  virtual void request() = 0;
};

// 真实主题类
class RealSubject : public Subject
{
 public:
  void request() override
  {
    std::cout << "RealSubject: Processing request." << std::endl;
  }
};

// 代理类
class Proxy : public Subject
{
 private:
  RealSubject *realSubject;

 public:
  Proxy() { realSubject = new RealSubject(); }

  ~Proxy() { delete realSubject; }

  void request() override
  {
    // 在访问真实主题之前或之后可以进行一些额外的操作
    std::cout << "Proxy: Pre-processing request." << std::endl;

    // 调用真实主题的方法
    realSubject->request();

    std::cout << "Proxy: Post-processing request." << std::endl;
  }
};

int main()
{
  // 创建代理对象并调用方法
  Proxy proxy;
  proxy.request();

  return 0;
}
```
