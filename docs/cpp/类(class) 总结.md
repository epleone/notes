---
tags:
  - cpp
---

# 类(class) 总结

## 构造函数

想要声明一个类对象并调用默认构造函数来创建它，应该使用 `A a;`，而不是 `A a();`

``` c++

// case 1
class A { }; 
A a;     // 对象声明，使用了默认构造函数创建对象 `a`
A a();   // 函数声明，它声明了一个名为 `a` 的函数，该函数没有参数并返回类型为 `A`。


// case 2
class A 
{ 
	A(int,int,int){}
}; 
A a;                       // 错误，没有定义默认构造函数
A b(0, 1, 1);              // OK


// case 3
// case 2
class A 
{ 
	A(){}  // 有没有`;`都可以
	A(int,int,int){};
}; 
A a;                       // OK
A b(0, 1, 1);              // OK
A a{};                     // OK

A a();      // 注意是函数声明，而不是对象声明

```

>[!warning]  
>1. 如果类中没有定义构造函数，则会默认定义一个 `A()`
>2. 如果类中定义了构造函数，就不会默认生成。即定义了 `A(int ,int, int)`, 就不存在 `A()`
>3. `A a;` 大致等同于 `A a{};` ，和 `A a();` 是不一样的!!!

在 C++ 中，`A a(0, 1, 1);` 和 `A a();` 的行为不同是由于语法的差异以及 C++ 中的“最令人困惑的翻译”（Most Vexing Parse）问题引起的。

1. `A a(0, 1, 1);`：这是一个对象声明，通过类 `A` 的带有参数的构造函数来创建对象 `a`。在这里，`(0, 1, 1)` 表示传递了三个参数给构造函数，用来初始化对象 `a`。
    
2. `A a();`：尽管看起来像是一个对象声明，但实际上它被解析为一个函数声明，声明了一个名为 `a` 的函数，它没有参数并返回类型为 `A`。这是因为 C++ 的语法规则，当你使用一对空的括号 `()` 初始化对象时，编译器将其解释为一个函数声明，而不是一个对象声明，这就导致了“最令人困惑的翻译”问题。
    

解决这个问题的一种方法是使用花括号 `{}` 来初始化对象，因为这样可以避免被解析为函数声明.

> [!faq] 那么 `A a(0, 1, 1)` 和 `A a {0, 1, 1}`一样吗?

在大多数情况下是等价的，但有一些微妙的区别：

1. 初始化列表：使用花括号 `{}` 进行初始化称为初始化列表初始化，而使用圆括号 `()` 进行初始化称为直接初始化。
    
2. 隐式类型转换：花括号 `{}` 语法不允许进行隐式类型转换，如果类型不匹配则会导致编译错误。而圆括号 `()` 语法允许进行隐式类型转换，编译器会尝试将提供的参数转换为相应的类型。
    
3. 列表初始化的优先级更高：在某些情况下，花括号 `{}` 初始化列表的优先级更高，因此可能会选择不同的构造函数。而圆括号 `()` 初始化则没有这种情况。
    
4. 花括号 `{}` 语法更加现代化和推荐：在现代 C++ 中，使用花括号 `{}` 进行初始化被视为更加安全和一致的做法，因为它可以避免一些潜在的问题，比如“最令人困惑的翻译”问题。
    

总的来说，在大多数情况下，`A a(0, 1, 1);` 和 `A a{0, 1, 1};` 是等价的，但由于它们的初始化方式和语义上的微妙差异，可能在某些情况下会产生不同的行为。


``` c++
Size(IMG_SIZE_W, IMG_SIZE_H);

Size sz(IMG_SIZE_W, IMG_SIZE_H);

Size sz = Size(IMG_SIZE_W, IMG_SIZE_H);
```
这三种写法都是合法的，并且它们之间的区别在于对象的命名和初始化方式。

1. **`Size(IMG_SIZE_W, IMG_SIZE_H);`**： 这是一个匿名对象的创建方式。它会调用 `Size` 类的构造函数创建一个临时的 `Size` 对象，但没有将其赋值给任何变量。通常情况下，这种方式用于执行一些临时的操作，例如作为函数参数传递，或者在表达式中使用。
    
2. **`Size sz(IMG_SIZE_W, IMG_SIZE_H);`**： 这是一个命名对象的创建方式。它声明了一个名为 `sz` 的 `Size` 类型的对象，并调用 `Size` 类的构造函数来初始化它的成员变量。这样创建的对象可以在后续的代码中引用，并且可以对其进行操作和传递给其他函数。
    
3. **`Size sz = Size(IMG_SIZE_W, IMG_SIZE_H);`**： 这也是一个命名对象的创建方式，但使用了拷贝构造函数进行初始化。它声明了一个名为 `sz` 的 `Size` 类型的对象，并使用 `Size(IMG_SIZE_W, IMG_SIZE_H)` 创建了一个临时的 `Size` 对象，然后通过拷贝构造函数将临时对象的值复制给了 `sz` 对象。这种方式和第二种方式效果相同，但是多了一次拷贝构造的过程。

## 前向声明 Forward declaration

这个一般用来解决类之间相互依赖的问题。

Forward declaration可以用于友元。

``` c++
// 这是一个前向声明，类似于一个指针
// 作用比较有限，不能定义类B的对象，但是可以定义类B的指针或引用，也可以作为函数的形参或返回类型。
class B;

class A
{
private:
    int id;
......
public:
   bool test(int id, B* b);
};


// 模板类的前向声明
template<typename T> class MyClass;
```

[C++ 类的前向声明 - 外卖 - 博客园](https://www.cnblogs.com/waimai/p/3875692.html)
[C++工程项目中常见的写法与解释——类的前向声明（class XXX；）篇\_c++ class \_\_x x;-CSDN博客](https://blog.csdn.net/qq_14873525/article/details/118186038)
[C++类的组合--前向引用声明（学习笔记：第4章 07） - 知乎](https://zhuanlan.zhihu.com/p/96858290)

## 子类


``` c++
class Data { 
public: 
	virtual void init() = 0; 
};

//错误
class AData: public Data { }; 

//正确
class AData: public Data 
{ 
public: 
	void init() override; 
};

void AData::init() { std << "init" << endl; }
```

> [!tips]
> 当声明一个类继承另一个类时, 如果不修改父类函数的行为，可以不重新声明它。
> 如果修改了父类的成员函数， 让其有了不同的行为，你就需要重新声明它，并提供新的实现。

// 用virtual修饰的函数，会构建虚函数表，则表明实际调用子类的函数。


``` c++
// 模板类的写法

template <typename T>
class NetData
{
	void init() ;
}

template <typename T>
MRESULT NetData<T>::init()
{
	
}

```


## 模板

> [!tips]
> 模板类的一般全部放到头文件中, 避免LINK错误

[C++学习15：C++模板的参数\_c++ 模板参数-CSDN博客](https://blog.csdn.net/yueni_zhao/article/details/128235347)


[关于UE4模块中模板类的问题(error LNK2019) - 知乎](https://zhuanlan.zhihu.com/p/201489341?utm_id=0)

模板类的实现最好放到头文件中，这样可以避免LINK不到。

[【c++】因模板类导致的 LNK2019 错误：“ 无法解析的外部符号 ” 详解\_lnk2019无法解析的外部符号-CSDN博客](https://blog.csdn.net/qq_41884002/article/details/99816073)

``` c++
// 可以在源文件中显式调用可能用到的模板类型，这样可以将声明与实现分开。

# source.cpp
template class MyTemplateClass<int>; 
template class MyTemplateClass<double>;

```


> [!warning] 模版类有很多坑
>  -  子类会隐藏模板父类的成员变量(包括继承来的)
>  -  非模板类则不会
>  -  模板子类并不会隐藏非模板父类的成员变量
>
>  需要显式的调用 `this->`   或者 使用 `using Base<T>::data ;` 来调用父类的成员变量


``` c++
#include <iostream>
using namespace std;

// 非模板类继承，这种做法ok, 如果是模板类，则在严格的编译器（比如linux下）编不过
class Base {
    int data = 1;
};

class Derive:public Base{
public:

    void test() {
        cout<< data << endl;
    }
};

int main()
{
    Derive d;
    d.test();
}


// 模板类继承
template <typename T>
class Base {
    int data = 1;
};

template <typename T>
class Derive:public Base<T> {
public:
	// using Base<T>::data; // 或者使用 this->data
    void test() {
        cout<< this->data << endl;
    }
};

int main()
{
    Derive<int> d;
    d.test();
}
```
 
## 初始化列表

`std::initializer_list<T>`
实现了迭代器，拥有 `begin()` `end()`  `size()` 

作为轻量级容器够用了。

```c++
void print_ints(std::initializer_list<int> ints) {
    for (auto i : ints) {
        std::cout << i << " ";
    }
    std::cout << std::endl;
}

int main() {
    print_ints({1, 2, 3});
    return 0;
}
```

[第三节 列表初始化—std::initializer\_list - 知乎](https://zhuanlan.zhihu.com/p/354588791)


## 友元

> [!warning]
> - 友元无法继承，父类是友元，子类如果没有声明过，则不是友元
> - 声明 friend 友元，朋友可以访问我的东西，但我不能访问他的东西, 除非他也声明我是他的友元

> [!faq]
> Q: `友元用private修饰和public修饰有区别吗？`
> A: `chatgpt说没有区别`


``` c++
// 代码1
template <typename T>
class A
{
private:
    friend class B<T>;
};

// 代码2
class A
{
private:
    template <typename T>
    friend class B<T>;
};

```


注意代码1和代码2的区别。
在代码1中：类 `A` 是一个模板类，对于类 `A` 的每个不同的模板实例，都会有一个对应的类 `B<T>` 的友元声明。这样，对于每个 `A<int>`、`A<double>` 等实例，都会有一个 `B<int>`、`B<double>` 的友元关系。

在代码2中：类 `A` 不是一个模板类，而是一个普通的类。而所有 `B<T>` 实例都声明为 `A` 的友元。