# 建造者模式

建造者模式是创建型模式。

我们在对一个实体类进行属性的 `get` 或 `set` 的时候，可以通过封装一些常用的构造方法来简化实体类的构造。

当创建一个对象需要很多步骤时，可以使用建造者模式。

可以看下面这个例子：

```cpp
class Builder;
class NutritionFacts
{
  friend class Builder;
 public:
  // inner class
  class Builder
  {
    friend class NutritionFacts;
   private:
    int m_servingSize;
    int m_servings;
    int m_calories;
    int m_fat;
    int m_sodium;
    int m_carbohydrate;

   public:
    Builder(int servingSize, int servings)
    {
      this->m_servingSize = servingSize;
      this->m_servings = servings;
    }

    Builder *calories(int v)
    {
      m_calories = v;
      return this;
    }

    Builder *fat(int v)
    {
      m_fat = v;
      return this;
    }

    Builder *sodium(int v)
    {
      m_sodium = v;
      return this;
    }

    Builder *carbohydrate(int v)
    {
      m_carbohydrate = v;
      return this;
    }

    NutritionFacts *build() { return new NutritionFacts(this); }
  };

 private:
  int m_servingSize;
  int m_servings;
  int m_calories;
  int m_fat;
  int m_sodium;
  int m_carbohydrate;

  // 私有构造函数对外不可见
  NutritionFacts(Builder *builder)
  {
    m_servingSize = builder->m_servingSize;
    m_servings = builder->m_servings;
    m_calories = builder->m_calories;
    m_fat = builder->m_fat;
    m_sodium = builder->m_sodium;
    m_carbohydrate = builder->m_carbohydrate;
  }
};

int main()
{
  // 不考虑内存泄漏，否则可用智能指针
  NutritionFacts *cocaCola = (new NutritionFacts::Builder(240, 8))
                                 ->calories(100)
                                 ->sodium(35)
                                 ->carbohydrate(27)
                                 ->build();
  return 0;
}
```

构造者模式也适用于类层次结构。抽象类有抽象类的 `Builder`，具体类有具体类的 `Builder`。
再举个例子，假设我们抽象出披萨类，各种各样的披萨可以继承这个抽象类来实现自己的具体类型的披萨。

`Pizza` 抽象类如下：

```cpp
class Pizza
{
 public:
  enum Topping
  {
    HAM,
    MUSHROOM,
    ONION,
    PEPPER,
    SAUSAGE
  };

  template <typename T>
  class Builder
  {
    friend class Pizza;

   private:
    set<Topping> m_toppings;

   public:
    T *addTopping(Topping t)
    {
      m_toppings.insert(t);
      // 如果返回的指针类型为父类的 Builder (即Pizza::Builder)，则不能指向子类 Builder 的成员函数
      // 因此这里需要得到子类的指针类型
      return self();
    }
    virtual Pizza *build();

   protected:
    virtual T *self();
  };

 private:
  set<Topping> m_toppings;

 protected:
  template <typename T>
  Pizza(Builder<T> *builder)
  {
    // 父类的 Builder 构造父类的成员 m_toppings
    m_toppings = builder->m_toppings;
  }
};
```

其中的 Builder 方法是抽象的，所以子类需要实现具体的 Builder 策略，
一种披萨的具体实现 `NyPizza` ：

```cpp
class NyPizza : public Pizza
{
 public:
  enum Size
  {
    SMALL,
    MEDIUM,
    LARGE
  };

  class Builder : public Pizza::Builder<Builder>
  {
    friend class NyPizza;

   private:
    Size m_size;

   public:
    Builder(Size size)
    {
      // 子类的 Builder 为子类构造成员 m_size
      m_size = size;
    }

    NyPizza *build() override { return new NyPizza(this); }

   protected:
    Builder *self() override { return this; }
  };

 private:
  Size m_size;

  NyPizza(Builder *builder) : Pizza(builder) { m_size = builder->m_size; }
};
```

另一种披萨的具体实现 `CalZone`：

```cpp
class CalZone : public Pizza
{
 public:
  class Builder : public Pizza::Builder<Builder>
  {
    friend class CalZone;

   private:
    bool m_sauceInside;

   public:
    Builder *sauceInside()
    {
      m_sauceInside = true;
      return this;
    }

    CalZone *build() override { return new CalZone(this); }

   protected:
    Builder *self() override { return this; }
  };

 private:
  bool m_sauceInside;

  CalZone(Builder *builder) : Pizza(builder)
  {
    // 子类的 Builder 为子类构造成员 m_sauceInside
    m_sauceInside = builder->m_sauceInside;
  }
};
```

我们在具体调用的时候，可以通过如下方式：

```cpp
int main()
{
  NyPizza *pizza = (new NyPizza::Builder(NyPizza::SMALL))
                       ->addTopping(NyPizza::SAUSAGE)
                       ->addTopping(NyPizza::ONION)
                       ->build();
  CalZone *calzone =
      (new CalZone::Builder())->addTopping(Pizza::HAM)->sauceInside()->build();
  return 0;
}
```
