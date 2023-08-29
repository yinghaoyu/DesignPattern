# 命令模式

命令模式是一种**行为型**模式。

通过调用者调用接受者执行命令，执行顺序是： 调用者 → 命令 → 接受者

```cpp
class Command
{
 public:
 virtual void doit();
 virtual void undo();
};

class Content
{
 public:
 string msg = "Hello World";
};

class CopyCommand : public Command
{
 private:
  Content* content;
 public:
  CopyCommand(Content* content)
  {
    this->content = content;
  }

  void doit() override
  {
    content->msg = content->msg + content->msg;
  }

  void undo() override
  {
    content->msg = content->msg.substr(0, content->msg.length() / 2);
  }
};

int main()
{
  Content* content = new Content();
  CopyCommand* copyCommand = new CopyCommand(content);
  cout << "origin msg is " << content->msg << endl;
  copyCommand->doit();
  cout << "do copy command, result is " << content->msg << endl;
  copyCommand->undo();
  cout << "undo copy command, result is " << content->msg << endl;
  return 0;
}
```

命令模式的应用：

1. 结合责任链模式实现多次 `undo`；
2. 结合组合模式实现宏命令；
3. 结合备忘录模式实现事务回滚。
