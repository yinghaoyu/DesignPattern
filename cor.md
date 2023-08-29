# 责任链模式

责任链模式是一种**行为型**模式。

举个例子，有一段文本需要过滤一系列敏感字，我们可以通过责任链模式来设计这个功能，假设文本是

```txt
scripts Hell World! 996
```

我们有多个过滤规则，比如第一个规则是：过滤 `scripts` 这个关键字（实际的规则可能很复杂，目前只是举这个例子来说明情况）

第二哥规则是：过滤 `996` 这个关键字

首先，将这个文本封装成一个 `Msg` 类，文本内容在 `content` 变量中

```cpp
class Msg
{
 private:
  string content;
  string getContent()
  {
    return content;
  }
  void setContent()
  {
    this->content = content;
  }
};
```

然后抽象一个 `Filter` 接口，各种过滤规则无非就是实现这个接口即可

```cpp
class Filter
{
 public:
  virtual bool doFilter(Msg* msg);
};
```

过滤掉 `996` 这个关键字的规则：

```cpp
class SensitiveFilter : public Filter
{
 public:
  bool doFilter(Msg* msg)
  {
    string content = msg->getContent();
    size_t pos = 0;
    string key = "996";
    while((pos = content.find(key, pos)) != string::npos)
    {
      content.erase(pos, key.length());
    }
    msg->setContent(content);
    return true;
  }
};
```

过滤掉 `scripts` 的规则：

```cpp
class HTMLFilter : public Filter
{
 public:
  bool doFilter(Msg* msg)
  {
    string content = msg->getContent();
    size_t pos = 0;
    string key = "scripts";
    while((pos = content.find(key, pos)) != string::npos)
    {
      content.erase(pos, key.length());
    }
    msg->setContent(content);
    return true;
  }
};
```

主方法调用时，直接用相应的 `Filter` 来处理即可：

```cpp
int main()
{
  Msg* msg = new Msg();
  Msg->setContent("scripts Hell World! 996");
  Filter* se = new SensitiveFilter();
  Filter* html = new HTMLFilter();
  se->doFilter(msg);
  html->doFilter(msg);
  return 0;
}
```

不过更为优雅的方式是设计一个 `FilterChain`，把所有的 `Filter` 都加入到这个类里面，
对于 `Msg` 直接取调用 `FilterChain` 的过滤方法即可把 `FilterChain` 所有的 `Filter` 都执行，而且可以很灵活地
指定 `Filter` 顺序。

```cpp
class FilterChain : public Filter
{
 private:
  std::list<Filter*> filters;

  FilterChain* addFilter(Filter* filter)
  {
    filters.push_back(filter);
    return this;
  }

 public:
  bool doFilter(Msg* msg)
  {
    for(Filter* filter : filters)
    {
      if(!filter.doFilter(msg))
      {
        return false;
      }
    }
    return true;
  }
};

int main()
{
  FilterChain* filterChain = new FilterChain();
  filterChain->addFilter(new HTMLFilter())->(new SensitiveFilter());
  Msg* msg = new Msg();
  msg->setContent("scripts Hell World! 996");
  filterChain->doFilter(msg);
  return 0;
}
```
