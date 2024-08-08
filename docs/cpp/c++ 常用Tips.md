---
tags:
  - cpp
  - tips
---

# c++ 常用Tips

## size_t

当你看到一个对象声明为size_t类型，你马上就知道它代表字节大小或数组索引

[为什么size\_t重要？（Why size\_t matters） - Jeremy's blog](https://jeremybai.github.io/blog/2014/09/10/size-t)

``` c++
// printf打印size_t，需要搭配 `zu`
sizt_t idx = 0;
printf("%zu\n",  idx);
```


> [!tips] `printf`打印 `size_t`，需要搭配 `zu`

## 结构体初始化

当结构体中出现`C++`定义的结构体或者类（需要调用构造函数的），比如标准库的类。

初始化结构体不能使用`C`的方式，申请内存然后置零。这样隐藏着bug。使用`C++`的`new`申请内存，这样会隐式的调用构造函数。

例如下面结构体如果使用`C`的方式申请内存会在`vs2019`引发bug。

[Visual studio 2019 throws Read Access Violation when we push_back more than 5 elements to std:vector.](https://developercommunity.visualstudio.com/t/visual-studio-2019-throws-read-access-violation-wh/1305498/1000)

``` c++
typedef struct {  
    // ...
    std::vector<cv::Mat>        vecInputimgs;  
    std::vector<cv::Mat>        vecMaskimgs;  
    std::vector<cv::Mat>        vecOutputimgs;  
    // ...
} MatSt,


// 正确
MatSt* m = new MatSt;


// 暗藏bug, 结构体内的vector可能没有正确构造和初始化。
MatSt* m = (MatSt*) malloc(sizeof(MatSt));

```
