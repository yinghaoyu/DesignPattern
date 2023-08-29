# 迭代器模式

迭代器模式是一种**行为型**模式。

迭代器最典型的应用是容器遍历。

```cpp
#include <iostream>
#include <vector>

class Iterator
{
 public:
  virtual int getNext() = 0;
  virtual bool hasMore() = 0;
};

class ArrayIterator : public Iterator
{
 private:
  std::vector<int> &arr;
  size_t position;

 public:
  ArrayIterator(std::vector<int> &array) : arr(array), position(0) {}

  int getNext() override { return arr[position++]; }

  bool hasMore() override { return position < arr.size(); }
};

int main()
{
  std::vector<int> numbers = {1, 2, 3, 4, 5};

  Iterator *iterator = new ArrayIterator(numbers);

  while (iterator->hasMore())
  {
    std::cout << iterator->getNext() << " ";
  }

  std::cout << std::endl;

  return 0;
}
```
