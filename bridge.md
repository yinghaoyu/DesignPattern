# 桥接模式

桥接模式是一种**结构型**模式。它将抽象部分和实现部分分离，使他们可以独立变化。

`GG` 在追 `MM` 的时候，可以送书和花两种礼物

```cpp
class GiftImpl
{

};

class Book : public GiftImpl
{
  // 实现
};

class Flower : public GiftImpl
{
  // 实现
};

class Gift
{
 protected:
  GiftImpl* m_impl;
};

class WarmGift : public Gift
{
 // 抽象
 public:
 WarmGift(GiftImpl* impl)
 {
  m_impl = impl;
 }
};

class WildGift : public Gift
{
 // 抽象
 public:
 WildGift(GiftImpl* impl)
 {
  m_impl = impl;
 }
};

class GG
{
 public:
  void chase(MM* mm)
  {
    // 抽象类 WarmGift
    // 实现类 Flower
    Gift* g = new WarmGift(new Flower());
    give(mm, g);
  }

  void give(MM* mm, Gift g)
  {
    cout << g + " gived" << endl;
  }
}

class MM
{
 public:
  string m_name;
};

int main()
{
  GG gg = new GG();
  MM mm = new MM();
  gg->chase(mm);
  return 0;
}
```

如上代码，礼物 `Flower` 被包装成了一个 `WarmGift` 送给 `MM`，`WarmGift` 和 `WildGift` 都是 `Gift` 的一种抽象，
`Flower` 和 `Book` 都是 `Gift` 的一种具体实现。
