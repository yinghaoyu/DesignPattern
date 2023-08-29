# 模版方法

模版方法是一种**行为型**模式。

假设我们要实现一个游戏，这个游戏有初始化、启动、结束三个方法，那么可以定义一个游戏的模版：

```cpp
class Game
{
  protected:
    virtual void init();
    virtual void start();
    virtual void end();
    void play()
    {
      init();
      start();
      end();
    }
};
```

每种类似这样结构的游戏都可以继承这个类来实现这三个方法，比如：

```cpp
class BasketballGame
{
 protected:
  void init() override
  {
    cout << "basketball init" << endl;
  }

  void start() override
  {
    cout << "basketball start" << endl;
  }

  void end() override
  {
    cout << "basketball end" << endl;
  }
};
```
