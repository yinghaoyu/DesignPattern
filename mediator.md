# 中介者模式

中介者模式是一种**行为型**模式。

举个简单的例子，如果一个聊天室里面的用户1和用户2要聊天，聊天室就相当于中介的地位，
用户1和用户2只管调用发消息方法，聊天室即可把消息给对方

```cpp
class User
{
 private:
  string name;
 public:
  User(string name)
  {
    this->name = name;
  }
  void sendMessage(string content)
  {
    CharRoom::showMessage(this, content);
  }
  string getName()
  {
    return name;
  }
};

class CharRoom
{
 public:
  static void showMessage(User* user, string content)
  {
    cout << "user: " << user->getName() << " send a message, content is " << content;
  }
};

int main()
{
  User* user = new User("Peter");
  user->sendMessage("Hello ");
  user = new User("Harry");
  user->sendMessage("Hi");
  return 0;
}
```
