# 策略模式

策略模式是一种**行为型**模式，它定义了一组算法，将每个算法都封装起来，并且使它们之间可以互换。

以实例来说明

> 假设我们有一个猫类，这个类里面有体重和身高这两个属性，给你一个猫的集合，然后需要你按猫的体重从小到大排序

主要思路

我们可以把体重从小到大这个看成是一个策略，后续可能衍生其他的策略，比如：按身高从高到低，体重从小到大，体重一样的身高从高到低...

以身高从低到高排序这个策略为例

```cpp
class Cat
{
 public:
  int height;
  int weight;
  Cat(int height, int weight)
  {
    this->height = height;
    this->weight = weight;
  }
  int getHeight()
  {
    return height;
  }
  int getWeight()
  {
    return weight;
  }
};

class CatSortStrategy
{
  bool operator(Cat& l, Cat& r)
  {
    return l.getHeight() < r.getHeight();
  }
};

int main()
{
  vector<Cat*> cats = {new Cat(10, 20), new Cat(19, 41),
                        new Cat(15, 40), new Cat(18, 33), new Cat(14, 23)};
  std::sort(cats.begin(), cat.end(), CatSortStrategy);
  return 0;
}
```
