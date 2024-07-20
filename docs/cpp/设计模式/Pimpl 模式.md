# Pimpl 模式

## Pimpl 主要思想

Effective Modern C++ 中的条款 22 提到：在一些场景下，为了减少编译依赖，提高编译的速度，应该在适当的时候使用 Pimpl 写法。Pimpl 可以理解成指向实现对象的指针（Pointer To Implementation），也就是一个指向实现对象的指针。具体实现为，在头文件中，对类进行声明，将其中的一些成员变量用一个具体实现类或者结构体的指针来代替；然后将这个具体实现类或结构体的定义放在源文件中。

> [!quote]
> [Effective Modern C++: 使用 Pimpl 技法（条款22）](https://zhuanlan.zhihu.com/p/623852536)