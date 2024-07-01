# cmake tips



## 第三方依赖库

`find_package`: 用于调用已经安装到本地的第三方库。

`git subodule`:  cmake版本要求比较低，建议直接选择 `FetchContent`。

`FetchContent`  支持从多种来源（如Git、SVN、HTTP/HTTPS等）下载源代码, 并将源代码直接加入到项目一起编译。 在 CMake 3.11 中引入，但推荐使用 CMake 3.14 或更高版本。

`ExternalProject` 将源代码下载并编译成库，再link到项目中。这样的好处是可以为每个外部项目指定独立的构建命令和参数。


``` cmake
cmake_minimum_required(VERSION 3.20)
 
project("HelloWorld" CXX)
 
add_executable(${PROJECT_NAME} main.cpp)
 
include(FetchContent)
FetchContent_Declare(NumCpp
        GIT_REPOSITORY https://github.com/dpilger26/NumCpp
        GIT_TAG Version_2.12.1)
FetchContent_MakeAvailable(NumCpp)
 
target_link_libraries(${PROJECT_NAME}
    NumCpp::NumCpp
)

# NumCpp::NumCpp是别名，可以为target设置多个别名
```





> [!quote]
> 1.  [CMake进阶教程：深入FetchContent与ExternalProject模块](https://zhuanlan.zhihu.com/p/681696425)
> 2. [NumCpp: Building](https://dpilger26.github.io/NumCpp/doxygen/html/md__home_dpilger__github__num_cpp_docs_markdown__building.html)
> 3. [CMake系列专题--find_package](https://mp.weixin.qq.com/s/SI3QT3cZ0dhW0fkWcZsbGA)

