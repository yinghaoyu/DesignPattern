# 外观模式

外观模式（或者说门面模式）是一种**结构型**模式。

外观模式为子系统提供一组统一的接口，定义一组高层接口让子系统更易用。

假设建造一个房子需要以下三个步骤：

1. 和泥
2. 搬砖
3. 砌墙

如果每次我们制造一个房子都要分别调用这三个方法，就会比较麻烦一些，
我们可以设置一个门面，这个门面封装了这三个步骤，后续建造房子，只需要调用这个门面即可。

```cpp
// 和泥
class Mason
{
 public:
  void mix()
  {
    cout << "我和好泥了" << endl;
  }
};

// 搬砖
class BrickWorker
{
 public:
  void carry()
  {
    cout << "我搬好砖了" << endl;
  }
};

// 砌墙
class BrickLayer
{
 public:
  void neat()
  {
    cout << "我砌好墙了" << endl;
  }
}

// 门面
class LabourConstractor
{
 private:
  Mason* work1 = new Mason();
  BrickWorker work2 = new BrickWorker();
  BrickLayer work3 = new BrickLayer();

 public:
  void buildHouse()
  {
    work1.mix();
    work2.carry();
    work3.neat();
  }
};

int main()
{
  LabourConstractor* lab = new LabourConstractor();
  lab->buildHouse();
  return 0;
}
```
