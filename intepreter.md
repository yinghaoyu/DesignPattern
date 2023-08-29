# 解释器模式

解释器模式是一种**行为型**模式。

解释器模式为某个语言定义它的语法（或者文法）表示，并定义一个解释器来处理这个语法。

一般用于脚本语言解释器。

示例：如何实现一个自定义接口告警规则功能？

> 一般来讲，监控系统支持开发者自定义告警规则，比如我们可以用下面这样一个表达式，
来表示一个告警规则，它表达的意思是：每分钟 API 总出错数超过 100 或者每分钟 API 总调用数超过 10000 就触发告警。

```cpp
if(api_error_per_minute > 100 || api_count_per_minute > 10000)
{
  // 告警
}
```

在监控系统中，告警模块只负责根据统计数据和告警规则，判断是否触发告警。
至于每分钟 `API` 接口出错数、每分钟接口调用数等统计数据的计算，是由其他模块来负责的。
其他模块将统计数据放到一个 `Map` 中（数据的格式如下所示），发送给告警模块。
接下来，我们只关注告警模块。

```cpp
map<string, long> apiStat;
apiStat["api_error_per_minute"] = 103;
apiStat["api_count_per_minute"] = 987;
```

实际上，我们可以把自定义的告警规则，看作一种特殊“语言”的语法规则。

我们实现一个解释器，能够根据规则，针对用户输入的数据，判断是否触发告警。

利用解释器模式，我们把解析表达式的逻辑拆分到各个小类中，避免大而复杂的大类的出现。

```cpp
class Expression
{
 public:
  virtual bool interpret(string context);
};

class AndExpression : public Expression
{
 private:
 Expression* exp1 = nullptr;
 Expression* exp2 = nullptr;

 public:
  AndExpression(Expression* exp1, Expression* exp2)
  {
    this->exp1 = exp1;
    this->exp2 = exp2;
  }

  bool interpret(string context) override
  {
    return exp1->interpret(context) && exp2->interpret(context);
  }
};

class OrExpression : public Expression
{
 private:
 Expression* exp1 = nullptr;
 Expression* exp2 = nullptr;

 public:
  OrExpression(Expression* exp1, Expression* exp2)
  {
    this->exp1 = exp1;
    this->exp2 = exp2;
  }

  bool interpret(string context) override
  {
    return exp1->interpret(context) || exp2->interpret(context);
  }
};

class TerminalExpression : public Expression
{
 private:
 string data;

 public:
  TerminalExpression(string data)
  {
    this->data = data;
  }

  bool interpret(string context) override
  {
    if(context.find(data) != context.end())
    {
      return true;
    }
    return false;
  }
};

int main()
{
  Expression* robert = new TerminalExpression("Robert");
  Expression* john = new TerminalExpression("John");
  Expression* isMale = OrExpression(robert, john);

  Expression* julie = new TerminalExpression("Julie");
  Expression* married = new TerminalExpression("Married");
  Expression* isMarriedWoman = AndExpression(julie, married);

  cout << "John is male ? " + isMale->interpret("John") << endl;
  cout << "Julie is a married women ?" + isMarriedWoman->interpret("Married Julie");
  return 0;
}
```
