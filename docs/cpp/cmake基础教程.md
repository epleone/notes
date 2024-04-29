# cmake学习笔记

#todo

[CMake Tutorial — CMake 3.28.20231128-g2d0b779 Documentation](https://cmake.org/cmake/help/git-master/guide/tutorial/index.html)

官方文档 api
[CMake Reference Documentation — CMake 3.28.0-rc5 Documentation](https://cmake.org/cmake/help/latest/)

中文文档
[Introduction - 《CMake菜谱（CMake Cookbook中文版）》 - 书栈网 · BookStack](https://www.bookstack.cn/read/CMake-Cookbook/README.md)

Modern CMake
[An Introduction to Modern CMake · Modern CMake](https://cliutils.gitlab.io/modern-cmake/)

Modern CMake for C++
[GitHub - xiaoweiChen/Modern-CMake-for-Cpp: 《Modern CMake for C++》的非专业个人翻译](https://github.com/xiaoweiChen/Modern-CMake-for-Cpp)

# 基础知识

CMake本身是一个工具集，由五个可执行的程序组成：cmake、ctest、cpack、cmake-gui和ccmake，其中cmake可以说是出镜率最高的明星级别程序了，它用于在构建项目的第一步，进行项目的配置、生成和构建项目的主要可执行文件的工作。其他的程序们ctest用于运行和报告测试结果的测试驱动程序，cpack用来生成安装程序和源包的打包程序，cmake-gui是 cmake 的图形界面，ccmake（注意ccmake比cmake多了一个c）也是用于图形界面，与cmake-gui不同的地方在于ccmake是基于控制台（terminal）的图形界面。

## 基础流程
CMake 的整体流程可以分为配置（Configure）、生成（Generate）、构建（Build）和安装（Install）四个主要阶段。这四个阶段通常是按顺序依次执行的。如图表示。

![图片](cmake-pipeline.png)

下面是每个阶段的主要任务：

- 配置（configuration）– 配置项目，将目标的构建信息全部计算出来，`${CXX_COMPILER_ID}`的值在此计算
- 生成（generation）- 生成对应构建系统/生成器的构建文件, 生成表达式所在的阶段，`$<CXX_COMPILER_ID>`的值在此计算
- 构建（build） – 调用构建系统构建项目, 可以手动使用目标构建系统（VS, makefile）构建，也可以使用cmake调用目标构建系统（生成器）构建。
- 安装（install）- 选择将构建生成的可执行文件、库、头文件等安装到指定的目录中。

下面我们将详细解析每个步骤。

**1、配置（Configuration）**

配置阶段是CMake解析CMakeLists.txt文件的过程。在这个阶段，CMake会读取CMakeLists.txt文件，并执行其中的命令。这些命令主要用于检查系统环境（例如编译器、库等），设置构建选项，以及定义构建目标（例如库、可执行文件等）。

CMakeLists.txt文件是CMake的核心，它定义了项目的构建规则和依赖关系。每个目录（包括子目录）中都可以有一个CMakeLists.txt文件。在配置阶段，CMake会从顶层目录的CMakeLists.txt文件开始，递归地处理每个子目录中的CMakeLists.txt文件。

- 在配置阶段，CMake解析项目的CMakeLists.txt文件，设置变量，选择生成器，并生成一个CMake缓存文件（通常是 `CMakeCache.txt`）。
- 在这个阶段，CMake会记录用户设置的变量和CMakeLists.txt文件中的信息，以及其他配置选项。生成表达式在这个阶段通常不会得到具体的值，因为它们通常是在构建阶段动态计算的

**2、生成（Generation）**

生成阶段是CMake根据配置阶段的结果，生成实际的构建文件的过程。这些构建文件通常是Makefile文件，但也可以是其他类型的构建文件，例如Ninja构建文件，或者Visual Studio项目文件，这取决于你选择的构建工具。

在生成阶段，CMake会将CMakeLists.txt文件中定义的构建规则和依赖关系，转换为构建工具可以理解的形式。例如，如果你选择的构建工具是Make，CMake会生成Makefile文件。每个目录（包括子目录）中都会生成一个Makefile文件。

- 在生成阶段，CMake使用配置阶段生成的CMake缓存文件，根据用户的选择生成实际的构建系统文件（例如Makefile、Visual Studio项目文件等）。
- 生成表达式在这个阶段会被动态计算，生成具体的值。这些值依赖于构建配置、平台和其他上下文信息。

**3、构建（Build）**

构建阶段是使用构建工具（例如Make、Ninja或Visual Studio）根据生成的构建文件，编译源代码并链接生成目标文件的过程。

在构建阶段，构建工具会读取生成的构建文件，按照其中定义的规则和依赖关系，执行实际的编译和链接操作。构建工具会自动处理依赖关系，确保在编译和链接一个目标文件之前，其所有依赖的目标文件都已经被正确地编译和链接。

**这个阶段即可以手动使用目标构建系统（VS, makefile）构建，也可以使用cmake调用目标构建系统（生成器）构建。**

以上就是CMake构建过程的基本流程。在理解了这个流程之后，我们就可以更深入地探讨CMake如何生成Makefile，以及CMake构建过程中的关键步骤了。


我们来看一个具体的例子：
```bash
cmake -DBUILD_TESTBED=ON -DBUILD_SHARED_LIBS=ON -S ./ -B build -G "Visual Studio 16 2019" 
cmake --build build --config Release
```

第一行命令涵盖了CMake的配置阶段和生成阶段。详细解释一下：

1. **配置阶段：**
    - `-DBUILD_TESTBED=ON -DBUILD_SHARED_LIBS=ON `: 这部分是配置阶段，通过设置CMake变量来配置项目。具体来说，它设置了`BUILD_TESTBED`、`BUILD_SHARED_LIBS`等变量的值。
    - `-S ./`: 指定了源代码目录，告诉CMake去哪里找`CMakeLists.txt`文件。
    - `-B build`: 指定了生成文件的目录，告诉CMake将生成的文件放在哪里。

1. **生成阶段：**
    - `-G "Visual Studio 16 2019"`: 这部分是生成阶段，它选择了生成器为"Visual Studio 16 2019"。这告诉CMake生成Visual Studio所需的项目文件。

2. **综合起来：** 所以，整个命令包含了配置阶段的变量设置和生成阶段的生成器选择。执行这个命令后，CMake将会在指定的生成目录（这里是`build`）生成与Visual Studio 2019相关的项目文件（例如`.sln`文件和`.vcxproj`文件），并配置这些项目以符合你在命令中设置的变量。

第二行命令是执行构建阶段的CMake命令，也可以不用这行命令生成，直接打开Visual Studio来生成。
- `--build build`: 这部分告诉CMake要执行构建。`--build`选项后面需要指定构建目录，这里是`build`，表示构建系统文件已经生成在`build`目录中。
 - 在Windows系统中会自动调用VS的编译器去进行编译
 - 在Linux系统中会自动调用make命令进行编译
- `--config Release`: 这是可选的，用于指定构建的配置。在这个例子中，配置被设置为`Release`，这是一个特定于Visual Studio的配置。如果不指定，CMake将使用默认配置。

综合起来，这个命令告诉CMake在指定的构建目录（`build`）中执行构建操作，使用的是之前生成的构建系统文件，而构建的配置类型为`Release`。这将会编译和链接项目，并生成Release版本的可执行文件或库。

CMake构建完成后，会在项目的根目录下生成一个名为`build`的目录。这个目录是CMake构建过程中所有中间文件和最终生成的目标文件的存放地。下面我们将详细解析这个目录的结构。

首先，我们来看一下`build`目录的一级子目录：

- CMakeFiles：这个目录中存放的是CMake在构建过程中生成的临时文件，包括编译器检查的结果、Find模块（Find Modules）查找的结果等。这些文件主要用于CMake自身的需求，一般情况下，我们不需要关注这个目录的内容。
- Testing：如果你的项目中包含了CTest测试，那么这个目录将会被生成。它包含了所有CTest测试的结果。
- bin：这个目录中包含了所有的可执行文件（Executable Files）。如果你的CMake项目中包含了多个可执行文件，那么它们都会被放在这个目录中。
- lib：这个目录中包含了所有的库文件（Library Files）。无论是静态库（Static Libraries）还是动态库（Dynamic Libraries），都会被放在这个目录中。

接下来，我们再深入到CMakeFiles目录中，看一下它的二级子目录：

- project.dir：这个目录中包含了项目构建过程中的临时文件，如.o文件和.d文件。这些文件是编译器在编译源代码时生成的。
- CMakeOutput.log：这个文件记录了CMake在配置过程中的输出信息，包括编译器检查的结果、Find模块查找的结果等。
- CMakeError.log：这个文件记录了CMake在配置过程中遇到的错误信息。

以上就是CMake构建后的目录结构的基本情况。在实际的项目中，可能会根据项目的具体需求，生成更多的子目录和文件。但是，这些基本的目录和文件是你在任何一个使用CMake构建的项目中都能看到的。


[cmake从入门到完全放弃 - 知乎](https://zhuanlan.zhihu.com/p/647086078)
[CMake构建Makefile深度解析：从底层原理到复杂项目 - 知乎](https://zhuanlan.zhihu.com/p/635875056?utm_id=0)

## 基础语法

CMake的基础概念是目标。构建系统的核心也在于此：管理目标的构建、不同目标的依赖问题。在CMake中的目标/目录等等还附带有[属性（property）](https://cmake.org/cmake/help/latest/manual/cmake-properties.7.html)

**变量类型有字符串，布尔值或列表（由分号分隔的元素组成的序列）**

- 注释使用 `#` 多行注释 `#[[ ]]`

- 变量使用`${var}`方式取值，但是在 `if` 控制语句中是直接使用变量名`var`

- **命令command是大小写无关的，参数和变量是大小写相关的**。
	指令名不区分大小写，但在 CMake 社区中有一个约定，即在指令名中使用 snake_case(即小写 单词与下划线连接)

- SET(SRC_LIST main.c) 可以写成 SET(SRC_LIST “main.c”)，如果源文件名中含有空格，就必须要加双引号
- ADD_EXECUTABLE(hello main) 后缀可以不加，他会自动去找.c和.cpp，最好不要这样写

- 计算 `math(EXPR <variable> "<expression>")`
- 控制流  [if](https://cmake.org/cmake/help/latest/command/if.html)
  `if` 控制语句中直接使用变量名更可靠，即 `if(b_var)` 比 `if(${b_var})` 好。
``` cmake
if(<condition>)
  <commands>
elseif(<condition>) # optional block, can be repeated
  <commands>
else()              # optional block
  <commands>
endif()

# 是否定义变量
if(DEFINED <name>) 
if(DEFINED CACHE{<name>}) 
if(DEFINED ENV{<name>})

# 以下操作符支持比较操作: EQUAL，LESS，LESS_EQUAL，GREATER 和 GREATER_EQUAL

# 字符串相等
if ("A" STREQUAL "${B}")

# MATCHES 操作符，匹配的组都在CMAKE_MATCH_<n>变量中。
 <variable|string> MATCHES <regex> 

# 还有其他检查，若满足条件则返回 true。 
• 若值在列表中: <variable|string> in _LIST<variable> 
• 若指令可用: command<command-name> 
• 若 CMake 策略存在: POLICY<policy-id>
• 若使用 add_test() 添加 CTest 测试:test<test-name> 
• 若定义了构建目标:target<target-name>


# 文件系统的check
# 检查文件或目录是否存在 这将解析符号链接 (若符号链接的目标存在，则返回 true)。 
EXISTS <path-to-file-or-directory>

# 检查哪个文件更新 如果 file1 比 (或等于)file2 更新，
# 或者两个文件中有一个不存在，则返回 true。
<file1> IS_NEWER_THAN <file2>

# 检查路径是否为目录 
IS_DIRECTORY path-to-directory

# 检查路径是否为符号链接 
IS_SYMLINK file-name

# 检查路径是否为绝对路径
IS_ABSOLUTE path

```





[foreach](https://cmake.org/cmake/help/latest/command/foreach.html)

```text
foreach(<loop_var> <items>)
  <commands>
endforeach()
```

[while](https://cmake.org/cmake/help/latest/command/while.html#command:while)

```text
while(<condition>)
  <commands>
endwhile()
```

- 变量 `set(<variable> <value>... [PARENT_SCOPE])`

其中`function()`新增了一个作用域（SCOPE），`set(... PARENT_SCOPE)`设置父作用域。一般用作函数中返回值的作用。这种解决方法有一定的局限性，因为其不允许访问超过一个级别的变量。另一件值得注意的事 情是，使用 PARENT_SCOPE 不会改变当前作用域中的变量。

作用域的参数 `DIRECTORY` 或 `GLOBAL` 参数。顾名思义，`DIRECTORY` 关键字将在当前目录及其以下应用保护，而 `GLOBAL` 关 键字将对整个构建应用保护。
### 函数function和宏定义macro


macro定义：
``` cmake 
macro(<name> [arg1 [arg2 [arg3 ...]]])
  COMMAND1(ARGS ...)
  COMMAND2(ARGS ...)
  ...
endmacro(<name>)
```

function定义：
``` cmake
function(<name> [arg1 [arg2 [arg3 ...]]])
  COMMAND1(ARGS ...)
  COMMAND2(ARGS ...)
  ...
endfunction(<name>)
```


函数和宏的默认内部变量

| 变量  | 说明                                                             |
| ----- | ---------------------------------------------------------------- |
| ARGV# | ARGV0为第一个参数，ARGV1为第二个参数，依次类推                   |
| ARGV  | 定义宏（函数）时参数为2个，实际传了4个，则ARGV代表实际传入的两个 |
| ARGN  | 定义宏（函数）时参数为2个，实际传了4个，则ARGN代表剩下的两个     |
| ARGC  | 实际传入的参数的个数                                             | 

**注意，参数可以多传，但不能少传。**

- CMAKE_CURRENT_FUNCTION：当前函数名称
- CMAKE_CURRENT_FUNCTION_LIST_DIR： 当前函数路径
- CMAKE_CURRENT_FUNCTION_LIST_FILE：当前函数所属文件
- CMAKE_CURRENT_FUNCTION_LIST_LINE：当前函数定义的起始行数

``` cmake

# function 函数测试
# 函数名: xyz
function(xyz arg1 arg2)
     message("ARGC: ${ARGC}")
     message("ARGV: ${ARGV}")
     message("ARGN: ${ARGN}")
     message("ARGV0: ${ARGV0}")
     message("ARGV1: ${ARGV1}")
 
     # 循环打印出各个参数
     set(i 0)
     foreach(loop ${ARGV})
         message("arg${i}: " ${loop})
         math(EXPR i "${i} + 1")
     endforeach()
endfunction()
 
# 调用函数
xyz(A B C D E F G)

# 实际输出
ARGC: 7
ARGV:A;B;C;D;E;F;G
ARGN:C;D;E;F;G
ARGVO:A
ARGV1:B
argo:A
arg1:B
arg2:C
arg3:D
arg4:E
arg5:F
arg6:G
```


CMake 函数的返回值通常是通过设置变量的值来实现的，然后在函数外部访问这个变量。
``` cmake
# 定义一个简单的函数
function(my_function result_var)
    set(${result_var} "Hello, World!" PARENT_SCOPE)
endfunction()

# 调用函数并获取返回值
my_function(my_result)

# 输出返回值
message("Returned value: ${my_result}")

```

在这个例子中，我们定义了一个名为 `my_function` 的函数，该函数接受一个参数 `result_var` 作为返回值的变量。在函数内部，我们使用 `set(${result_var} "Hello, World!" PARENT_SCOPE)` 设置变量的值，并通过 `PARENT_SCOPE` 标志将其作用域扩展到调用该函数的父级作用域。然后，在函数外部，我们调用了函数，并通过 `${my_result}` 获取了返回值，并使用 `message` 命令输出了这个返回值。

请注意，CMake 函数的返回值通常是通过在调用函数时设置变量的值，而不是通过显式的 `return` 语句。这是因为 CMake 函数在设计上更侧重于设置变量来影响调用方的行为，而不是通过返回值来传递结果。

[[#^ref3 |参考链接]][^3]


### 生成表达式 Generator expressions

CMake首先解析项目中的CMakeLists.txt文件-称为"配置阶段"，然后生成生成环境-称为"生成阶段"。

因此，基本上，生成器表达式适用于仅生成器可以知道的所有内容

官方文档: 
[cmake-generator-expressions(7) — CMake 3.28.0-rc5 Documentation](https://cmake.org/cmake/help/latest/manual/cmake-generator-expressions.7.html)

[CMake应用：生成器表达式 - 知乎](https://zhuanlan.zhihu.com/p/437404485)

Generator expressions are evaluated during build system generation to produce information specific to each build configuration. They have the form `$<...>`

``` cmake 
# case 1
set(LIB_SUFFIX $<IF:${BUILD_SHARED_LIBS}, $<IF: ${WIN32}, .dll, .so>, $<IF:${WIN32}, .lib, .a> >)
set(LIB_TYPE $<IF:${BUILD_SHARED_LIBS}, SHARED, STATIC>)
add_library(${PROJECT_NAME} ${LIB_TYPE} IMPORTED GLOBAL)

# case 2
$<TARGET_FILE:${target}>

# case 3, debug库加后缀
arcsoft_hand_gesture_recognition$<$<CONFIG:Debug>:_d>

$<IF:${BUILD_SHARED_LIBS}, SHARED, STATIC>

$<${_MT_}: -mt>

```

生成器表达式将在生成阶段计算 (当配置完成并创建构建系统时)，所以不能将其输出捕获到 变量中，并打印到控制台。
**要调试可以使用以下方法**:
- 将其写入一个文件 (file() 指令的这个特定版本支持生成器表达式):
``` cmake
file(GENERATE OUTPUT filename CONTENT "$<...>")
```
- 添加一个自定义目标，并根据命令行显式构建:
``` cmake
custom_target(gendbg COMMAND ${CMAKE_COMMAND} -E echo "$<...>")
```


注意：已导入目标（Imported Target）的属性 `IMPORTED_LOCATION`的值不能使用生成表达式。它需要在配置阶段就能够被确定，以确保正确的链接。

在CMake中，有一些属性是不能使用生成表达式的，因为它们需要在配置阶段（configure time）就能够确定，而生成表达式是在生成阶段（generate time）使用的。以下是一些不能使用生成表达式的属性的示例：

1. `INTERFACE_INCLUDE_DIRECTORIES`: 用于指定接口目标的包含目录。这个属性在配置阶段就需要确定，而不能使用生成表达式。
2. `IMPORTED_LOCATION`: 已导入目标的位置，如前面提到的。需要在配置阶段确定。
3. `INTERFACE_LINK_LIBRARIES`: 用于指定接口目标的链接库。在配置阶段需要明确指定，而不能使用生成表达式。
4. `INTERFACE_COMPILE_DEFINITIONS`: 接口目标的编译定义。在配置阶段需要明确指定。
5. `VERSION` and `SOVERSION` : 用于设置共享库的版本信息。在配置阶段需要明确指定。

``` cmake
# 1. INTERFACE_INCLUDE_DIRECTORIES
target_include_directories(target_name INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)

# 2. IMPORTED_LOCATION
set_target_properties(external_library PROPERTIES
    IMPORTED_LOCATION /path/to/external/library/libexternal_library.so
)

# 3. INTERFACE_LINK_LIBRARIES
target_link_libraries(target_name INTERFACE
    library1
    library2
)

# 4. INTERFACE_COMPILE_DEFINITIONS
target_compile_definitions(target_name INTERFACE
    INTERFACE_DEFINE1
    INTERFACE_DEFINE2
)

# 5. VERSION and SOVERSION
set_target_properties(my_library PROPERTIES
    VERSION 1.2.3
    SOVERSION 1
)

```

- **Conditional Expressions**


[CMake应用：生成器表达式 - 知乎](https://zhuanlan.zhihu.com/p/437404485)


## 编译基础

### **.a .so .lib .dll的区分**
.a .so .lib .dll这几个文件扩展名代表不同类型的库文件，它们在不同的操作系统和编程环境中使用。以下是它们的一些常见用途和区别：

1. **.a (Archive)**:
    - **用途：** 在Unix/Linux系统上，`.a` 文件是静态库的一种形式，其中包含了一组目标文件的归档（archive）。
    - **工作原理：** 当你链接一个程序时，静态库的内容会被复制到可执行文件中，使得可执行文件独立于库文件。
    - **扩展名含义：** Archive 意味着它是一个包含其他文件的档案文件。
2. **.so (Shared Object)**:
    - **用途：** 在Unix/Linux系统上，`.so` 文件是共享库的一种形式，也称为动态链接库。
    - **工作原理：** 共享库在运行时动态加载到内存中，多个程序可以共享同一个库的实例，这有助于减少内存占用。
    - **扩展名含义：** Shared Object 表示这是一个共享对象。
3. **.lib (Library)**:
    
    - **用途：** 在Windows系统上，`.lib` 文件通常是用于链接静态库的文件。
    - **工作原理：** 类似于Unix/Linux中的`.a`文件，`.lib` 文件包含了一组目标文件的归档。
    - **扩展名含义：** Library 表示这是一个库文件。
4. **.dll (Dynamic Link Library)**:
    
    - **用途：** 在Windows系统上，`.dll` 文件是动态链接库的一种形式，类似于Unix/Linux中的`.so`文件。
    - **工作原理：** 动态链接库在运行时加载到内存中，允许多个程序共享同一个库的实例，从而减少内存占用。
    - **扩展名含义：** Dynamic Link Library 表示这是一个动态链接库。


**特别的，在Windows系统下**，对于共享库（DLLs），通常需要搭配 `.lib` 文件在编译和链接过程中使用。

在Windows下，`.lib` 文件可以有两种类型：

1. **导入库（Import Library）：** 这种 `.lib` 文件在链接时告诉编译器和链接器如何调用共享库（`.dll` 文件）。它包含了共享库中函数的声明，以便编译器在编译时检查函数调用的正确性。这个导入库（`.lib`）通常是由编译器的工具链生成的，也叫符号表。
    
2. **静态库（Static Library）：** 这是与Unix/Linux系统中的 `.a` 类似的概念，包含了一组目标文件的归档。静态库在编译时被链接到可执行文件中，使得可执行文件不再依赖于外部的 `.dll` 文件。


### VS 运行时库及MT/MTD、MD/MDD研究说明

- MT：mutithread，多线程库，编译器会从运行时库里面选择多线程静态连接库来解释程序中的代码，即连接LIBCMT.lib库
- MTd：mutithread+debug，多线程调试版，连接LIBMITD.lib库
- MD：MT+DLL，多线程动态库，连接MSVCRT.lib库，这是个导入库，对应动态库为MSVCRT.dll
- MDd： MT+DLL+debug，多线程动态调试库，连接MSVCRTD.lib库，对应动态库为MSVCRTD.dll

**默认编译出来的都是`MD/MDd`** 如果是`MT`库, 一般会加后缀加以说明。 

可以使用[[#`target_compile_options` 命令| target_compile_options设置编译MT库]]

一般来说，静态库才会特别区分MT/MTD、MD/MDD。如果是动态库，则不需要区分。
静态库可以方便处理依赖冲突，而动态库可能彼此之间的依赖会打架。

1. [visual stdio爆强新手教程：VS 运行时库及MT/MTD、MD/MDD研究说明 - 知乎](https://zhuanlan.zhihu.com/p/400478966)


![[#Visual Studio设置加载dll路径]]


# 变量(Variable)

[cmake-variables(7) — CMake 3.28.0 Documentation](https://cmake.org/cmake/help/latest/manual/cmake-variables.7.html)

[小白入门笔记：CMake编译过程详解](https://mp.weixin.qq.com/s?__biz=MzU1MjY4MTA1MQ==&mid=2247642929&idx=1&sn=1ed6dcd891d21ed538a43816cb006650&scene=58&subscene=0)
变量是区分大小写的。

**普通变量、缓存变量和环境变量**这三类变量组成了CMake变量这一个“复杂”的主题，让人头疼的一点在于上述三个变量在不同的作用域中的“被使用和修改”，而且CMake作用域之间变量如何影响的“特定规则”也会经常在CMake变量的使用过程中体现。

基本的变量操作指令是`set()\unset()`，变量名区分大小写并可以包含字符（使用括号和引号参数允许在变量名中包含空格。但当以后引用时，必须使用反斜杠来转义空格(`\`)，因此，建议在变量名中只使用字母数字字符、减号(`-`) 和下划线(`_`)）。具体的使用方式为在设置变量时只需使用`set()`并提供名称和值，要取消变量的设置时可以使用`unset()`并提供名称。

```
set(MyString1 "Text1")
set([[My String2]] "Text2")
set("My String 3" "Text3")


message(${MyString1})
message(${My\ String2})
message(${My\ String\ 3})

unset(MyString1)
```

由上面示例可以看到，对已定义变量的引用需要使用`${} `语法，`e.g. message(${MyString1})`，其中`message`是用以构建过程中的打印，通过`${}`告诉CMake遍历**作用域堆栈**，尝试将`${MyString1}`替换为具体的值供`message命令`打印出来。值得注意的是在查询`${MyString1}`过程中，CMake若是没有找到对应的变量则会将其替换为空字符串并不会产生错误。另外，在通过`${} `语法进行变量的求值和展开时，是由内而外执行的。

考虑包含以下变量的例子：
• `MyInner` 的值是`Hello`
• `MyOuter` 的值是`${My`
若使用`message(”${MyOuter}Inner} World”)`，输出将是`Hello World`，这是因为`${My `替换了`${MyOuter}`，当与顶层值`Inner}` 结合时，会创建另一个变量引用`${MyInner}`。

当涉及到变量类别时，变量引用的工作方式有点奇怪。以下是通常情况适用的方式:
• `${}` 用于引用普通变量或缓存变量。
• `$ENV{}` 用于引用环境变量。
• `$CACHE{}` 用于引用缓存变量。

`message("${var}")`  和 `message(${var})`  一样，但前者在处理变量值中包含空格等特殊字符时更加安全，因为双引号会将整个变量值作为一个整体。
- `"${var}"` 可以保证是单值变量，而 `${var}`可能会被展开为列表, 各个成员作为多个参数，即splat, 这是他们的**重要区别**。

## 环境变量
首先说明如何修改或创建一个环境变量，使用`set(ENV{<variable>} <value>)` 指令用以声明，使用`unset(ENV{<variable>})`来清除某一个环境变量，其中`ENV`表示环境变量标志性前缀，`variable`指变量名称，`value`则为变量值，需要注意的是设定或读取环境变量时，都通过`ENV`前缀来访问环境变量，读取环境变量值时，要在`ENV`前加`$`符号；但`if`判断是否定义时，不用加`$`符号。具体示例如下：
``` cmake
#示例1：
set(ENV{CXX} "clang++")
unset(ENV{VERBOSE})


# 示例2：
set(ENV{CMAKE_PATH} "myown/path/example")

# 判断CMAKE_PATH环境变量是否定义
if(DEFINED ENV{CMAKE_PATH}) //注意此处ENV前没有$符号
	message("CMAKE_PATH_1: $ENV{CMAKE_PATH}") //注意此处ENV前有$符号
else()
	message("NOT DEFINED CMAKE_PATH VARIABLES")
endif()

```


设定环境变量后，其作用域只影响当前CMake进程，也就是说环境变量设定后是整个CMake进程的作用域都可用，但是不会影响CMake进程外的整个系统环境。

另一个需要注意的点在于，环境变量在启动CMake进程后会基于CMake在配置阶段中收集的信息在**CMake生成阶段**生成环境变量的**副本**，该副本会在**单一的、全局的**作用域中可用。即，若使用`ENV` 变量作为指令的参数，这些值将在CMake生成构建系统期间（配置阶段+生成阶段）插入，并且会将其嵌入到构建树中，在构建系统完成后即使再通过脚本或者命令行修改环境变量`ENV{<variable>}`的`value`，在构建阶段时该环境变量值也不会更新成新的`value`（因为在构建系统中保存的是之前环境变量的副本），具体实例如下：

```cmake
# 示例3:
#CMakeLists.txt:
cmake_minimum_required(VERSION 3.20.0)
project(Environment)

#在配置期间打印myenv环境变量
message("generated with " $ENV{myenv})

#在构建阶段过程中打印相同的变量
add_custom_target(EchoEnv ALL COMMAND echo "myenv in build is" $ENV{myenv})
```

在上述示例3的`CMakeLists.txt`中是有两个展示阶段：第一将在配置期间打印`myenv`环境变量并通过`add_custom_target() `添加一个构建阶段，第二将在构建阶段过程中打印相同的变量。构建上述`CMakeLists.txt`通过一个`bash`脚本文件执行，见下：

``` bash
#示例4:
#bash脚本:
#先定义myenv环境变量，并打印
export myenv=first
echo myenv is now $myenv
#基于CMakeList.txt生成一个构建系统
cmake -B build .
cd build
#基修改myenv环境变量，并打印
export myenv=second
echo myenv is now $myenv
#开始构建
cmake --build .
```

运行上面的代码，可以清楚地看到在配置过程中，设置的值会保留在生成的构建系统中：

``` bash
1.  $ ./build.sh | grep -v "\-\-"
2.  myenv is now first
3.  generated with first
4.  myenv is now second
5.  Scanning dependencies of target EchoEnv
6.  myenv in build is first
7.  Built target EchoEnv
```

## 缓存变量

缓存变量可以通过`$CACHE{<name>}` 语法来引用，而设置一个缓存变量使用`set(<variable> <value> CACHE <type> <docstring> [FORCE])`指令，与用于普通变量的`set() `指令相比，缓存变量的设定中有一些必需参数和关键字`（CACHE &FORCE）`。与环境变量不同的是，缓存变量是CMake进程在配置阶段收集相关信息后存储在在构建树中的`CMakeCache.txt `文件中的变量，缓存变量不可像环境变量中在脚本使用但是可以通过`cmake-gui`或者`ccmake`来声明。
`Cache Variable`缓存变量相当于一个全局变量，在同一个`CMake`工程中任何地方都可以使用，比如父目录，子目录等，而如上文中缓存变量的指令格式是

``` c
set(<variable> <value>... CACHE <type> <docstring> [FORCE])

# variable：变量名称
# value：变量值列表
# CACHE：cache变量的标志
# type：变量类型，取决于变量的值。类型为：BOOL、FILEPATH、PATH、STRING、INTERNAL
# docstring：必须是字符串，作为变量概要说明
# FORCE：强制选项，强制修改变量值

```

其中FORCE选项，在**定义缓存变量时**不加也能定义成功，但是**修改时**不加FORCE选项则修改无效，所以不论定义或修改缓存变量时，建议都加上FORCE选项，具体实例如下：

``` c
// 设置一个string类型的缓存变量，名称为FOO，变量值为BAR
set(FOO "BAR" CACHE STRING "interesting value" FORCE)

// 设置一个string类型的缓存变量，名称为CMAKE_BUILD_TYPE，变量值为Release
set(CMAKE_BUILD_TYPE Release CACHE STRING "Build type" FORCE)

```

## 变量作用域 

> • 函数作用域: 用于执行用function() 定义的自定义函数
> 
> • 目录作用域: 当从add_subdirectory() 指令执行嵌套目录中的CMakeLists.txt 文件

如果对C/C++比较熟悉的话，CMake中变量的作用域就可以类比C/C++中的一些定义，举例来说:

1. CMake中变量的函数作用域可类比成C/C++中函数的参数值传递（不是引用、也不是指针），也就是说在一般情况下CMake函数中对变量的修改不会影响到函数外的CMake变量`value`值，而CMake函数中的变量就是从`parent scope`中“查询”到并生成的副本；
2. CMake中变量的目录作用域，也是类似于C/C++中的`include`文件依赖，也就是在子目录下的对变量的修改不会影响父目录中变量定义的`value`值；

话已至此，不妨再类比一下CMake中的普通变量、缓存变量和环境变量。
- CMake普通变量就好比C/C++中的普通变量定义。都是作用在定义变量时所在的作用域（scope）之下；
- CMake缓存变量有些像C/C++中的指针，只是CMake中引用缓存变量的value值时不用像C/C++那样加一个“取地址符”，而且这个缓存变量（“指针”）是不对外部可见的（不能通过命令行修改和引用CMake缓存变量），如果想要CMake中修改后的缓存变量value值生效就必须加上FORCE关键字；
- CMake的环境变量就好比C/C++中的宏定义了，不仅对外部可见，同时CMake中还支持对环境变量（“宏”）的修改。

回归本质，CMake变量作用域作为一个通用概念是为了**分离不同的抽象层**，以便在调用用户定义的函数时，该函数中设置的变量是局部的，这些局部变量不会影响全局作用域，即使局部变量的名称与全局变量的名称完全相同。若显式需要，函数也应该具有对全局变量的读/写访问权。这种变量(或作用域) 分离必须在多个层面上工作——当一个函数调用另一个函数时，分离规则同样适用。针对变量的作用域，**理解“副本“的概念是关键**，当创建**嵌套**（子）作用域时，CMake只需用来自当前（父）作用域的**所有变量的副本**填充，后续**嵌套**（子）作用域命令将影响这些副本。但若完成了**嵌套**（子）作用域的执行，所有的副本都会删除，而原始的父作用域将恢复，嵌套作用域中操作的变量将不会更新到父作用域中。

接下来再根据CMake Documentation中的定义，感受一下CMake变量作用域的具体说明。第一，针对函数作用域（Function Scope）：

> A variable “set” or “unset” binds in this scope and is visible for the current function and any nested calls within it, but not after the function returns.---from cmake language

举个例子，当在函数内通过`set()`或`unset()`将变量”v”与当前函数作用域绑定时，变量”v”的新值仅在函数作用域内有效，出了这个作用域，如果这个作用域外也有同名的变量”v”，那么使用将是域外同名变量”v”的值。第二，针对目录作用域（Directory Scope）：

> Each of the Directories in a source tree has its own variable bindings. Before processing the CMakeLists.txt file for a directory, CMake copies all variable bindings currently defined in the parent directory, if any, to initialize the new directory scope. .---from cmake language

目录作用域的启用一般是在父目录下的CmakeList.txt中有`add_subdirectory`(“子目录路径”)指令，而在子目录的CMakeLists.txt会将父目录的所有变量拷贝到当前CMakeLists.txt中，当前CMakeLists.txt中的变量的作用域仅在当前子目录有效。

综上，不管是针对CMake函数作用域还是CMake目录作用域，其都有两个特点：向下有效和数值拷贝生成副本，在不使用特殊关键字的情况下，嵌套（子）作用域针对普通变量的修改不会影响到父作用域。针对变量，普通变量仅仅有效于当前作用域，而缓存变量和环境变量可以在全局作用域中使用。

## 常用系统变量

**操作系统名**

`CMAKE_SYSTEM_NAME`

``` cmake
if(CMAKE_SYSTEM_NAME STREQUAL "Linux") 
	message(STATUS "Doing things the usual way")  
elseif(CMAKE_SYSTEM_NAME STREQUAL "Windows") 
	message(STATUS "I'm supported here too.")
elseif(CMAKE_SYSTEM_NAME STREQUAL "Darwin") 
	message(STATUS "Thinking differently")
endif()
```

CMake 将预定义一些变量，这些变量将提供关于主机和目标系统的信息。
`UNIX` 如果为真，表示为 `UNIX-like` 的系统，包括 `Linux`、`macOS` 和 `Cygwin`
`WIN32` 如果为真，表示为 `Windows` 系统
`APPLE` 如果为真，表示为 `Apple` 系统
`ANDROID`


**平台是 32 位还是 64 位架构**
`CMAKE_SIZEOF_VOID_P` 表示` void*` 的大小（例如为 4 或者 8），可以使用其来判断当前构建为 32 位还是 64 位

`CMAKE_SOURCE_DIR`  工程顶层目录，即入口CMakeLists文件所在源代码目录路径
`CMAKE_CURRENT_SOURCE_DIR`   当前CMakeLists.txt 文件的所在源代码目录路径

`CMAKE_BINARY_DIR`  工程编译发生的目录，即执行cmake命令进行项目配置的目录，一般为build
`CMAKE_CURRRENT_BINARY_DIR`  当前处理的CMakeLists.txt中生成目标文件所在编译目录

`CMAKE_CURRENT_LIST_DIR` 表示当前文件所在的目录，可能是源代码目录，也可能是构建目录。

`CMAKE_INCLUDE_PATH`  一个包含头文件的目录列表，用于指定额外的头文件搜索路径。
`CMAKE_LIBRARY_PATH`  一个包含库文件的目录列表，用于指定额外的库文件搜索路径。

`CMAKE_INSTALL_PREFIX`  安装目标的根目录，`make install` 命令将安装文件到该目录。

`CMAKE_BUILD_TYPE`   指定构建类型，例如 Debug、Release、RelWithDebInfo 等。

**设定 C++ 标准**

通过设置 `CMAKE_CXX_STANDARD` 变量来设定实现:98、 11、14、17、20 或 23(自 CMake 3.20 以来)。
现代cmake推荐使用 [[#`target_compile_features` 命令]]。

`CMAKE_C_COMPILER`   C 编译器的路径和名称。
`CMAKE_C_FLAGS`   C 编译器的额外编译选项。

`CMAKE_CXX_COMPILER`  C++ 编译器的路径和名称。
`CMAKE_CXX_FLAGS`  C++ 编译器的额外编译选项。

`CMAKE_EXE_LINKER_FLAGS`  可执行文件的链接器标志
`CMAKE_SHARED_LINKER_FLAGS`  共享库的链接器标志

`CMAKE_CXX_EXTENSIONS`: 允许或禁用特定于供应商的扩展，也就是不同编译器对同一个标准的额外扩展。建议关闭。

``` cmake
# 建议的C++设置
set(CMAKE_CXX_STANDARD 17)

# 表示编译时要求使用指定的 C++ 标准（在这里是 C++17）。如果编译器不支持 C++17，将生成一个错误。
set(CMAKE_CXX_STANDARD_REQUIRED ON) 

set(CMAKE_CXX_EXTENSIONS OFF)

# 启用生成位置无关代码，对动态库很重要。类似通用的-fPIC选项
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

#或者使用set_property
# 设置全局属性
set_property(GLOBAL PROPERTY CXX_STANDARD 17)
set_property(GLOBAL PROPERTY CXX_STANDARD_REQUIRED ON)
set_property(GLOBAL PROPERTY CXX_EXTENSIONS OFF)
set_property(GLOBAL PROPERTY POSITION_INDEPENDENT_CODE ON)

# 或者，如果要为特定的目标设置属性
set_property(TARGET your_target PROPERTY CXX_STANDARD 17)
set_property(TARGET your_target PROPERTY CXX_STANDARD_REQUIRED ON)
set_property(TARGET your_target PROPERTY CXX_EXTENSIONS OFF)
set_property(TARGET your_target PROPERTY POSITION_INDEPENDENT_CODE ON)


# 或者更现代的写法, 可以同时设置多种属性
set_target_properties(my_target PROPERTIES 
		CXX_STANDARD 17 
		CXX_STANDARD_REQUIRED ON 
		CXX_EXTENSIONS OFF 
		POSITION_INDEPENDENT_CODE ON 
		)
```


**修改库默认输出路径**

CMake 提供了一些预定义的变量来控制不同类型文件的默认输出路径，常见的有：

- `CMAKE_ARCHIVE_OUTPUT_DIRECTORY` 表示静态库的默认输出路径
- `CMAKE_LIBRARY_OUTPUT_DIRECTORY` 表示linux上动态库（*.so）的输出路径
- `CMAKE_RUNTIME_OUTPUT_DIRECTORY` 表示可执行程序，以及windows上动态库（*.dll）的输出路径
- `CMAKE_PDB_OUTPUT_DIRECTORY` 表示MSVC环境下pdb文件的输出路径
同一个 target 有可能有不同类型的产出，比如在 MSVC 环境中，动态库 target 会产 .dll 文件，以及配套的 .lib 文件，两者就分别属于 `RUNTIME` 和 `ARCHIVE` 类型。另外需要注意，CMake 对动态库的处理有些特殊，虽然都是动态库，但在 linux 上属于 `LIBRARY` 类型，但在 windows 上则属于 `RUNTIME` 类型，需要用不同的变量进行指定。

修改以上这些变量可以影响所有 target 的输出路径，让它们不再产生到CMAKE_CURRENT_BINARY_DIR中。例如：

```cmake
# set default output path
# 这个会影响所有的设置，可以设置在根目录下
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}/bin")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}/lib")
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${PROJECT_BINARY_DIR}/arch")

# 推荐只为目标设置
add_library(lib_a ${SRC_FILES})

set_target_properties(lib_a PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/lib
    LIBRARY_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/lib
    ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/lib
)

```
按照这样的配置后，所有的可执行程序默认产生到构建目录下的 bin 中，window 上动态库（*.dll）也产生到 bin 内，linux 上动态库（*.so）则产生到 lib，而所有的静态库产生到 arch 中。

[[转]CMake：子目录工程和修改输出路径\_cmake 生成路径\_shada的博客-CSDN博客](https://blog.csdn.net/shada/article/details/130772705)


其它可能用到的：

| 变量名                    | 含义                                                               |
| ------------------------- | ------------------------------------------------------------------ |
| PROJECT_NAME              | project命令中写的项目名                                            |
| CMAKE_VERSION             | 当前使用CMake的版本                                                |
| PROJECT_SOURCE_DIR        | 同CMAKE_SOURCE_DIR                                                 |
| PROJECT_BINARY_DIR        | 同CMAKE_BINARY_DIR                                                 |
| CMAKE_INSTALL_PREFIX      | 指定make install命令执行时包安装路径                               |
| CMAKE_INSTALL_MESSAGE     | 控制打印哪些安装信息，LAZY：打印正在安装但不打印最新信息           |
| CMAKE_MODULE_PATH         | find_package命令搜索包路径之一，默认为空                           |


#todo

 **`CMAKE_CURRENT_LIST_DIR` vs `CMAKE_CURRENT_SOURCE_DIR` 的区别** 

`CMAKE_CURRENT_SOURCE_DIR` 更关注源代码目录相关的配置，而 `CMAKE_CURRENT_LIST_DIR` 更关注当前 CMake 文件所在的目录。

 **使用 `CMAKE_CURRENT_SOURCE_DIR` 当**：

1. **需要源代码目录的绝对路径：**
    - 如果你需要引用源代码目录的绝对路径，比如添加头文件、源文件或其他源代码目录相关的路径，使用 `CMAKE_CURRENT_SOURCE_DIR` 更合适。


 **使用 `CMAKE_CURRENT_LIST_DIR` 当：**

1. **在当前 CMake 文件中引用自身所在的目录：
    - 如果你在当前 CMake 文件中引用自身所在的目录，包括用于配置生成的文件、设置目标属性等，使用 `CMAKE_CURRENT_LIST_DIR` 更为合适。
    
    `configure_file(config.h.in ${CMAKE_CURRENT_LIST_DIR}/config.h)`
    
2. **在子目录中引用父目录：** 
	- 在子目录的 CMake 文件中，如果你需要引用父目录的路径，包括用于设置目标属性、引用父目录的头文件等，使用 `CMAKE_CURRENT_LIST_DIR`。
    `set(MY_PARENT_DIR ${CMAKE_CURRENT_LIST_DIR}/..) include_directories(${MY_PARENT_DIR}/include)`
    
3. **设置目标属性：**
    - 在子目录的 CMake 文件中，如果你需要引用父目录的路径，包括用于设置目标属性、引用父目录的头文件等，使用 `CMAKE_CURRENT_LIST_DIR`。
    `set_target_properties(my_target     PROPERTIES     LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/lib )`
    这样可以将编译生成的库文件输出到当前 CMake 文件所在目录下的 `lib` 子目录

**举一个实际的例子：**
`src/lib/cmakelists.txt` 中`include`了一个 `src/common.cmake`, 
`common.cmake`中的`CMAKE_CURRENT_LIST_DIR`会被解析成`common.cmake`所在的位置， 而`CMAKE_CURRENT_SOURCE_DIR`会被解析成`cmakelists.txt` 所在的位置。


## CMAKE_BUILD_TYPE

`CMAKE_BUILD_TYPE`是控制构建类型的，有这些选项值，

`Debug`：用于开发和调试的构建类型。此模式通常包含调试符号信息，并启用额外的调试功能，例如断言检查和运行时错误检查。此模式下通常关闭优化，以便更容易进行调试。

`Release`：用于发布最终产品的构建类型。此模式通常启用各种优化选项，以提高代码的执行速度和减小最终产品的大小。同时，此模式通常不包含调试符号信息，以减小可执行文件的大小。

`RelWithDebInfo`：结合了`Release`和`Debug`的特点。此模式启用了优化选项，同时保留了调试符号信息。这样可以在需要进行调试时获得更多的信息。

`MinSizeRel`：用于最小化生成的可执行文件大小的构建类型。此模式启用了各种优化选项，以减小最终产品的大小。通常不包含调试符号信息。  
在我们默认什么都没做的的情况下，`CMAKE_BUILD_TYPE`为空，CMake默认编译为`Debug`模式，但是通常我们发布在github上时我们是`Release`版本，所以我们经常会在别人的CMakeLists.txt文件中看见如下代码片段
``` cmake
if(NOT CMAKE_BUILD_TYPE)
 set(CMAKE_BUILD_TYPE "Release")
endif()
```
即如果用户没有指定这个变量，则把CMAKE_BUILD_TYPE设置为Release，编译的时候就会为Release版本，而不是Debug版本。

``` bash
# 生成时 Generate
cmake .. -DCMAKE_BUILD_TYPE=MinSizeRel

# 构建时 build
cmake --build build --config Release
```

同时用户也可以在执行cmake命令的时候，加-DCMAKE_BUILD_TYPE，来手动指定构建的类型。

# 命令(Command)

CMake命令不区分大小写（参数会区分大小写），命令由命令、参数列表组成，参数之间使用空格进行分隔。使用一对双引号包括的字符串认为是一个参数。

## 实用命令
### project 命令

命令简述：用于指定项目的名称, 最外层生效

``` cmake
# 命令语法
project(<projectname> [languageName1 languageName2 … ] )

# 使用范例
project(Main)
project(Main CXX)

# 这段代码和project一样，放在最外层生效
# 设置 Visual Studio 启动项目
set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT HSG_testbed)
```

``` cmake 
# 指定了工程的名字，并且支持所有语言
PROJECT (HELLO)
# 指定了工程的名字，并且支持语言是C++
PROJECT (HELLO CXX) 
# 指定了工程的名字，并且支持语言是C和C++
PROJECT (HELLO C CXX) 
```

该指定默认定义了两个CMAKE的变量
`<projectname>_BINARY_DIR`，本例中是 `HELLO_BINARY_DIR`，二进制路径
`<projectname>_SOURCE_DIR`，本例中是 `HELLO_SOURCE_DIR`，工程文件路径

同时，又定义两个与工程名无耦合的两个预定义变量：**`PROJECT_BINARY_DIR`** 和 **`PROJECT_SOURCE_DIR`**

### cmake_minimum_required命令

命令简述：用于指定需要的 CMake 的最低版本, 主CMakeLists.txt文件中使用

```cmake
# 命令语法：
cmake_minimum_required(VERSION major[.minor[.patch[.tweak]]][FATAL_ERROR])

# 使用范例：
cmake_minimum_required(VERSION 3.8)
```


### option命令

命令简述： 提供用户可以选择的布尔选项。

option命令，第一个参数是变量名，第二个参数是描述字符串，第三个是option的值，ON或OFF，不写就是默认OFF。

``` cmake
# 命令语法：
option(<variable> "<help_text>" [value])

# 使用范例：
option(MYDEBUG "enable debug compilation" OFF)

if (MYDEBUG)
    add_executable(main2 main2.c)
else()
    message(STATUS "Currently is not in debug mode")    
endif()

# 通过 `cmake DMYDEBUG=ON` 来控制, 注意加前缀`D`
```

**注意：即使不使用`option`, 也可以从用`cmake -DXX=ON` 直接传入 `XX` 。**

`option` 命令的主要好处包括：

1. **用户友好：** `option` 命令提供了一种简单的方式，让用户通过命令行或者图形界面工具更改项目的配置选项。这些选项可以在 CMake 配置阶段由用户进行设置，而不需要修改 CMakeLists.txt 文件。
2. **清晰的默认值：** 通过在 `option` 命令中设置默认值，可以确保项目具有明确定义的默认行为。这使得项目更容易被其他开发者理解，并在没有特定配置的情况下按照某种标准行为运行。
3. **条件编译：** `option` 命令创建了一个 CMake 变量，可以在 CMakeLists.txt 文件中用于条件编译。这允许根据选项的设置来决定是否包含、编译或执行特定的代码块。

### aux_source_directory命令

命令简述：用于将 dir 目录下的所有源文件的名字保存在变量 variable 中

``` cmake
# 命令语法
aux_source_directory(<dir> <variable>)

# 使用范例
aux_source_directory(. DIR_SRCS)
```


### add_dependencies 命令

命令简述：用于指定某个目标（可执行文件或者库文件）依赖于其他的目标。这里的目标必须是 add_executable、add_library、add_custom_target 命令创建的目标

``` cmake
# 命令语法：
add_dependencies(target-name depend-target1 depend-target2 …)

# 使用范例, 表示hsg依赖于handpose_lib， 构建时先构建handpose_lib
add_dependencies(${hsg} ${handpose_lib})
```

### **add_subdirectory 命令**

命令简述：用于添加一个需要进行构建的子目录，该子目录中包含了一个额外的 CMakeLists.txt 文件，它将被独立解析和处理。

`add_subdirectory` 会在构建系统中添加一个新的构建目标，该目标对应于子目录中的构建。

``` cmake
# 命令语法
add_subdirectory(source_dir [binary_dir] [EXCLUDE_FROM_ALL])

# 使用范例
add_subdirectory(src)

# 将 src 子目录加入工程并指定编译输出(包含编译中间结果)路径为 bin 目录，如果不进行 bin 目录的指定，那么编译结果(包括中间结果)都将存放在build/src 目录
ADD_SUBDIRECTORY(src bin) 
```

> **注意：vs中如果想将`add_subdirectory` 引入的子项目设置成启动项目，需要在主项目的 CMakeLists.txt 文件中使用 `set_property(DIRECTORY ...)` 来设置。具体使用可以参考[[#project 命令]]**

可以对比 `include命令`，来查看它们的区别。
### include命令

命令简述：  `include` 命令用于包含另一个 CMake 脚本文件的内容。这可以包含变量、函数、宏等定义。

`include` 是一种在当前 CMakeLists.txt 文件中嵌入其他脚本内容的方式，它不会创建新的 CMake 构建目标，而是直接在当前上下文中执行被包含的脚本。从给定的文件中读取CMake的清单文件代码。在清单文件中的命令会被立即处理，就像它们是写在这条include命令展开的地方一样。如果指定了OPTIONAL选项，那么如果被包含文件不存在的话，不会报错。如果指定了RESULT_VARIABLE选项，那么var或者会被设置为被包含文件的完整路径，或者是NOTFOUND，表示没有找到该文件。


``` cmake
# 命令语法
include(<file|module> [OPTIONAL] [RESULT_VARIABLE <VAR>] [NO_POLICY_SCOPE])
```

使用范例:  假设有两个 CMake 脚本文件，分别为 `Utils.cmake` 和 `Main.cmake`

``` cmake
# Utils.cmake
function(print_hello_world()
    message("Hello, world!")
endfunction()
```

``` cmake
# Main.cmake
include(Utils.cmake)
print_hello_world()
```

在这个例子中，`Main.cmake` 使用 `include` 命令包含了 `Utils.cmake`，然后调用了 `print_hello_world` 函数。这样可以将一些功能或变量定义在单独的文件中，然后在其他文件中使用，提高代码的模块化程度。

执行 `Main.cmake`，可以通过以下命令来运行：
``` bash
cmake -P Main.cmake
```


`include` 用于在当前 CMake 文件中嵌入另一个文件的内容，不会创建新的构建目标。

`add_subdirectory` 用于在构建系统中添加一个子目录，其中的 CMakeLists.txt 文件将被解析为独立的构建目标。

在选择使用 `include` 还是 `add_subdirectory` 时，主要取决于项目结构和代码组织的需求。 `add_subdirectory` 更适合将一个子目录看作一个独立的模块，而 `include` 更适合将一个脚本的内容嵌入当前上下文。



### set 命令

命令简述：用于设定变量 variable 的值为 value。如果指定了 CACHE 变量将被放入 Cache（缓存）中。

``` cmake
# 命令语法：
set(<variable> <value> [[CACHE <type><docstring> [FORCE]] | PARENT_SCOPE])

# 使用范例：
set(ProjectName Main)

# 创建变量，SRC_LIST变量就包含了main.c
SET(SRC_LIST main.c) 

# 创建列表，定义包含多个.c文件，中间一般用空格隔开
SET(SRC_LIST main.c t1.c t2.c)

# 创建了一个列表 "a;b;c;d"
set (var a b c d)

# 创建了一个变量"a c c d"
set (var "a b c d")
```


```cmake
# 设置 C++ 标准
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
```

这三行 CMake 指令用于设置 C++ 项目的编译选项。

`set(CMAKE_CXX_STANDARD 17)`：这一行指令设置了项目使用的 C++ 标准版本。在这个例子中，我们选择了 C++17 标准。CMake 支持设置多种 C++ 标准版本，如 C++11、C++14、C++17、C++20 等。可以根据项目的需求，选择合适的 C++ 标准版本。

`set(CMAKE_CXX_STANDARD_REQUIRED ON)`：这一行指令表示，如果编译器不支持指定的 C++ 标准，CMake 将报错并终止构建过程。如果将此选项设置为 OFF，则 CMake 会尽量使用所选 C++ 标准版本进行编译，但如果编译器不支持该版本，CMake 会自动降级到编译器支持的最接近的 C++ 标准版本。

`set(CMAKE_CXX_EXTENSIONS OFF)`：这一行指令用于禁用编译器特定的 C++ 语言扩展。将此选项设置为 OFF 可确保项目遵循 C++ 标准，并具有更好的可移植性。如果将此选项设置为 ON，则 CMake 允许编译器使用其特定的 C++ 语言扩展，这可能导致项目在不同编译器之间的行为不一致。

`set(CMAKE_POSITION_INDEPENDENT_CODE ON)`: 这一行指令设置生成的目标为位置无关代码（Position Independent Code，PIC）, 这通常通过将 `-fPIC`（对于 GCC 和 Clang）或者 `/DPI`（对于 MSVC）等编译选项传递给编译器来实现。 这对于生成共享库在不同程序中共享使用时是非常有用的。

更多变量设置的细节可以参考：
[现代CMake构建项目第一篇 - 知乎](https://zhuanlan.zhihu.com/p/583600075)

### **unset 命令**

命令简述：用于移除变量 variable。如果指定了 CACHE 变量将被从 Cache 中移除。

``` cmake
# 命令语法
unset(<variable> [CACHE])

# 使用范例
unset(VAR CACHE)
```

###  message命令
命令简述：用于输出信息
``` cmake
# 命令语法：
message([STATUS|WARNING|AUTHOR_WARNING|FATAL_ERROR|SEND_ERROR] “message todisplay”…)


(无) = 重要消息；
STATUS = 非重要消息；
WARNING = CMake 警告, 会继续执行；
AUTHOR_WARNING = CMake 警告 (dev), 会继续执行；
SEND_ERROR = CMake 错误, 继续执行，但是会跳过生成的步骤；
FATAL_ERROR = CMake 错误, 终止所有处理过程；

# 使用范例：
message("Hello World")
message(FATAL_ERROR "Hello World")
```




### find_path 命令

命令简述：用于查找包含文件 `name1` 的路径，如果找到则将路径保存在 `VAR` 中（此路径为一个绝对路径），如果没有找到则结果为 `<VAR>-NOTFOUND`。默认的情况下，`VAR` 会被保存在 `Cache` 中，这时候我们需要清除 `VAR` 才可以进行下一次查询（使用 `unset` 命令）

``` cmake
# 命令语法
find_path(<VAR> name1 [path1 path2 …])


# 使用范例
find_path(LUA_INCLUDE_PATH lua.h${LUA_INCLUDE_FIND_PATH})
if(NOT LUA_INCLUDE_PATH)
   message(SEND_ERROR "Header file lua.h not found")
endif()

```


### find_library 命令

命令简述：该命令用于查找库（动态库或者静态库），当构建依赖于第三方库/系统库，可以使用该命令来查找并使用库。
				

类似的命令 **`link_directories`  和 `target_link_directories` 已经不太建议使用了**

``` cmake
# 命令语法：
find_library (<VAR> name [path1 path2 ...])


# 使用范例, 寻找handpose库的路径，存放到lib_path中
find_library (lib_path handpose ./input)

```


**几个基本参数的解析**：

- `<var>`用于存储该命令执行的结果，也就是找到的库的全路径（包含库名）:  
     1. `<var>`可以是普通变量（需要指定`NO_CACHE`选项），也可以是缓存条目（意味着会存放在`CMakeCache.txt`中，不删除该文件或者用`set`重新设置该变量，其存储的值不会再刷新）；  
     2. 当库能被找到，`<var>`会被存放正常的库路径，当库未被找到，`<var>`中存放的值为`"<var>-NOTFOUND"`。只要`<var>`中的值`不是"<var>-NOTFOUND"`，那么即使多次调用`find_library`，`<var>`也不会再刷新;
- `name`用于指定待查找的库名称，库名称可以使用全称，例如`libmymath.a`（优先会当成全名搜索）；也可以不带前缀（例如前缀`lib`）和后缀（例如`Linux`中的`.so`、`.a`，`Mac`中的`.dylib`等），直接使用`mymath`；
- 
- `path`用于指定库的查找的路径；

###  find_package命令

命令简述： `find_package`用于查找包（通常是使用三方库），并返回关于包的细节（使用包所依赖的头文件、库文件、编译选项、链接选项等）  
与[`find_libaray`](https://www.jianshu.com/p/8a64c77343cb)直接在指定搜索目录下搜索库不同，`find_package`命令可以获取更多的信息，那么它的搜索方式也是与`find_libaray`不一样，它有两种不同的搜索方式：**模块模式**（`Module mode`）和**配置模式**（`Config mode`）。


[Cmake命令之find\_package介绍 - 简书](https://www.jianshu.com/p/a0915895dbbc)

### file 命令


[Cmake命令之file - 简书](https://www.jianshu.com/p/f0636394e88f)

命令简述：此命令提供了丰富的文件和目录的相关操作（这里仅说一下比较常用的）

``` cmake
# 子命令：读
  file(READ <filename> <out-var> [...])
  file(STRINGS <filename> <out-var> [...])
  file(<HASH> <filename> <out-var>)
  file(TIMESTAMP <filename> <out-var> [...])
  file(GET_RUNTIME_DEPENDENCIES [...])

# 子命令：写
  file({WRITE | APPEND} <filename> <content>...)
  file({TOUCH | TOUCH_NOCREATE} [<file>...])
  file(GENERATE OUTPUT <output-file> [...])
  file(CONFIGURE OUTPUT <output-file> CONTENT <content> [...])

# 子命令：文件系统
  file({GLOB | GLOB_RECURSE} <out-var> [...] [<globbing-expr>...])
  file(MAKE_DIRECTORY [<dir>...])
  file({REMOVE | REMOVE_RECURSE } [<files>...])
  file(RENAME <oldname> <newname> [...])
  file(COPY_FILE <oldname> <newname> [...])
  file({COPY | INSTALL} <file>... DESTINATION <dir> [...])
  file(SIZE <filename> <out-var>)
  file(READ_SYMLINK <linkname> <out-var>)
  file(CREATE_LINK <original> <linkname> [...])
  file(CHMOD <files>... <directories>... PERMISSIONS <permissions>... [...])
  file(CHMOD_RECURSE <files>... <directories>... PERMISSIONS <permissions>... [...])

# 子命令：路径转换
  file(REAL_PATH <path> <out-var> [BASE_DIRECTORY <dir>] [EXPAND_TILDE])
  file(RELATIVE_PATH <out-var> <directory> <file>)
  file({TO_CMAKE_PATH | TO_NATIVE_PATH} <path> <out-var>)

# 子命令：传输
  file(DOWNLOAD <url> [<file>] [...])
  file(UPLOAD <file> <url> [...])

# 子命令：锁
  file(LOCK <path> [...])

# 子命令：归档
  file(ARCHIVE_CREATE OUTPUT <archive> PATHS <paths>... [...])
  file(ARCHIVE_EXTRACT INPUT <archive> [...])
```

#### READ

``` cmake
# 读文件内容
file(READ myfile_read out_var)
message("Content of myfile_read is: ${out_var}")
```

#### WRITE
```bash
set(content "Write hello to file.")
file(WRITE myfile_write ${content}) # 文件不存在会创建，直接覆盖文件写
```
#### GLOB
``` cmake
# 使用范例：
# 目录的遍历
# 1. --> GLOB 用于产生一个文件（目录）路径列表并保存在variable 中
# 文件路径列表中的每个文件的文件名都能匹配globbing expressions（非正则表达式，但是类似）
# 如果指定了 RELATIVE 路径，那么返回的文件路径列表中的路径为相对于 RELATIVE 的路径
file(GLOB variable [RELATIVE path][globbing expressions]...)


# 获取当前目录下的所有的文件（目录）的路径并保存到 ALL_FILE_PATH 变量中
file(GLOB ALL_FILE_PATH ./*)

# 获取当前目录下的 .h 文件的文件名并保存到ALL_H_FILE 变量中
# 这里的变量CMAKE_CURRENT_LIST_DIR 表示正在处理的 CMakeLists.txt 文件的所在的目录的绝对路径（2.8.3 以及以后版本才支持）
file(GLOB ALL_H_FILE RELATIVE${CMAKE_CURRENT_LIST_DIR} ${CMAKE_CURRENT_LIST_DIR}/*.h)

```




#### COPY

``` cmake
# 将文件拷贝到./copy目录下
file(COPY myfile_read DESTINATION ./copy) 
```


#### ARCHIVE_CREATE
```cmake
# 压缩归档
# 将myfile_read myfile_write myfile_download打包成tar文件格式
file(ARCHIVE_CREATE OUTPUT "result.tar" PATHS myfile_read myfile_write myfile_download FORMAT gnutar) 
```

#### STRINGS
 按照`ASCII`编码从文件中读取并存储在`<variable>`中。换行符会被忽略，如果是二进制数据，也会被忽略。默认情况下是以换行符对读取的内容进行分割，每一行读取为输出结果序列的一个子项，不同子项以分号分割（也可以使用长度进行分割等，见下详解）。

``` cmake
file(STRINGS myfile_read content_s) # 默认会以换行将文件的内容分割成多个子项，按照分号序列的方式存储
message("read myfile_read as STRINGS: ${content_s}")

# 限制每次提取的字符串长度不超过10
file(STRINGS myfile_read content_s LENGTH_MAXIMUM 10)
message("read myfile_read as STRINGS - LMax: ${content_s}")
```



### list 命令

命令简述：LIST与SET命令类似，即使列表本身是在父域中定义的，LIST命令也只会在当前域创建新的变量，要想将这些操作的结果向上传递，需要通过SET　PARENT_SCOPE， SET CACHE INTERNAL或运用其他值域扩展的方法。

``` cmake
# 命令语法：
list (subcommand <list> [args...])

Reading
  list(LENGTH <list> <out-var>)
  list(GET <list> <element index> [<index> ...] <out-var>)
  list(JOIN <list> <glue> <out-var>)
  list(SUBLIST <list> <begin> <length> <out-var>)

Search
  list(FIND <list> <value> <out-var>)

Modification
  list(APPEND <list> [<element>...])
  list(FILTER <list> {INCLUDE | EXCLUDE} REGEX <regex>)
  list(INSERT <list> <index> [<element>...])
  list(POP_BACK <list> [<out-var>...])
  list(POP_FRONT <list> [<out-var>...])
  list(PREPEND <list> [<element>...])
  list(REMOVE_ITEM <list> <value>...)
  list(REMOVE_AT <list> <index>...)
  list(REMOVE_DUPLICATES <list>)
  list(TRANSFORM <list> <ACTION> [...])

Ordering
  list(REVERSE <list>)
  list(SORT <list> [...])


# 创建列表变量"a;b;c;d"
set (list_test a b c d) 
list (LENGTH list_test length) 
message (">>> LENGTH: ${length}")`

list (FIND list_test d list_index)
message (">>> FIND 'd': ${list_index}")

list (FIND list_test e list_index)
message (">>> FIND 'e': ${list_index}")

```

[CMake语法—命令list - kaizenly - 博客园](https://www.cnblogs.com/Braveliu/p/15820627.html)
[list — CMake 3.28.0-rc5 Documentation](https://cmake.org/cmake/help/latest/command/list.html)


### string 命令

[CMake String函数：如何巧妙地在cmake中操作字符串 - 知乎](https://zhuanlan.zhihu.com/p/661283261)

### CONFIGURE_FILE命令

命令简述：configure_file 指令通过读取配置文件中的内容，可以通过cmake构建生成c/c++代码，并加入项目编译。
``` cmake

# 命令语法
configure_file(<input> <output>
               [NO_SOURCE_PERMISSIONS | USE_SOURCE_PERMISSIONS |
                FILE_PERMISSIONS <permissions>...]
               [COPYONLY] [ESCAPE_QUOTES] [@ONLY]
               [NEWLINE_STYLE [UNIX|DOS|WIN32|LF|CRLF] ])
```

使用范例：

在include文件夹下新建helloconfig.h.in 配置文件
```cmake
# helloconfig.h.in
// the configured options and settings for HELLOWORLD
#define HELLOWORLD_VERSION_MAJOR "@HELLOWORLD_VERSION_MAJOR@"
#define HELLOWORLD_VERSION_MINOR "@HELLOWORLD_VERSION_MINOR@"
#define DATE "${PROJECT_DATE}"

# CMakeLists.txt
cmake_minimum_required(VERSION 3.10)
project(HELLOWORLD VERSION 2.6)
set(PROJECT_DATE 2022-09-03)
add_subdirectory(src)

# 调用configure_file命令生成helloconfig.h
# 如果输出文件不加${CMAKE_SOURCE_DIR}/include/，会默认输出到${CMAKE_BINARY_DIR}文件夹下
configure_file(${CMAKE_SOURCE_DIR}/include/helloconfig.h.in ${CMAKE_SOURCE_DIR}/include/helloconfig.h)


# Main.c 中使用
#include "hello.h"
#include "stdio.h"
#include "helloconfig.h"

int main(void)
{
    HelloFunc();
    printf("Version: %s.%s\n", HELLOWORLD_VERSION_MAJOR, HELLOWORLD_VERSION_MINOR);
    printf("Date: %s\n", DATE);
    return 0;
}
```


### install命令
#todo 深入理解`install`命令，比较和`file`的区别

命令简述：install 命令用于指定安装规则，它定义了在构建完成后如何将目标文件（可执行文件、库等）以及其他相关文件（如动态库、配置文件等）安装到指定的目录。这对于将构建好的项目打包成安装包或将项目部署到目标系统上非常有用。

```cmake
# 命令语法：

# 安装目标
install(TARGETS targets... [EXPORT <export-name>]
        [RUNTIME_DEPENDENCIES args...|RUNTIME_DEPENDENCY_SET <set-name>]
        [[ARCHIVE|LIBRARY|RUNTIME|OBJECTS|FRAMEWORK|BUNDLE|
          PRIVATE_HEADER|PUBLIC_HEADER|RESOURCE|FILE_SET <set-name>|CXX_MODULES_BMI]
         [DESTINATION <dir>]
         [PERMISSIONS permissions...]
         [CONFIGURATIONS [Debug|Release|...]]
         [COMPONENT <component>]
         [NAMELINK_COMPONENT <component>]
         [OPTIONAL] [EXCLUDE_FROM_ALL]
         [NAMELINK_ONLY|NAMELINK_SKIP]
        ] [...]
        [INCLUDES DESTINATION [<dir> ...]]
        )

# 安装文件
install(<FILES|PROGRAMS> files...
        TYPE <type> | DESTINATION <dir>
        [PERMISSIONS permissions...]
        [CONFIGURATIONS [Debug|Release|...]]
        [COMPONENT <component>]
        [RENAME <name>] [OPTIONAL] [EXCLUDE_FROM_ALL])
```


```cmake
# 指定安装规则
install(TARGETS MyApp RUNTIME DESTINATION bin)
install(FILES "${CMAKE_SOURCE_DIR}/libs/dynamic/libDynamic.dll" DESTINATION bin)
```

以下是这两条 `install` 命令的详细解释：

1. `install(TARGETS MyApp RUNTIME DESTINATION bin)`  
- `TARGETS MyApp`：指定要安装的目标，这里是 `MyApp` 可执行文件。  
- `RUNTIME`：表示我们要安装可执行文件的运行时组件。对于可执行文件，这通常指的就是可执行文件本身。
- `DESTINATION bin`：指定安装目标的目录。这里，`MyApp` 可执行文件将被安装到 `bin` 目录下。  
    

2. `install(FILES "${CMAKE_SOURCE_DIR}/libs/dynamic/libDynamic.dll" DESTINATION bin)`  
- `FILES`：表示我们要安装的是文件，而不是目标。在这个例子中，我们要安装的文件是动态库 `libDynamic.dll`。  
- `"${CMAKE_SOURCE_DIR}/libs/dynamic/libDynamic.dll"`：指定要安装的文件的路径。`${CMAKE_SOURCE_DIR}` 是一个变量，表示项目的根目录。


**再看具体例子**
**安装目标：**

```cmake
install(TARGETS 
        run my_math
        RUNTIME DESTINATION bin
        LIBRARY DESTINATION lib)
```

安装run和my_math这两个目标，RUNTIME表示可执行目标文件会被安装到bin目录下，LIBRARY表示静态库会被安装在lib下。这里的目标都是相对路径，相对于`CMAKE_INSTALL_PREFIX`这个宏，也就是实际安装在`${CMAKE_INSTALL_PREFIX}/bin`和`${CMAKE_INSTALL_PREFIX}/lib`下[^5]


**安装文件：**

```cmake
 install(FILES 
                include/my_math.hpp DESTINATION my_math/include)
```

也就是将`my_math.hpp`安装到`${CMAKE_INSTALL_PREFIX}/my_math/include`下去。

一般需要执行命令 `make install` [[#^ref5| 参考链接]][^5].

[CMake Install：深度解析与实践 - 知乎](https://zhuanlan.zhihu.com/p/661283021)


## 高级命令(Advanced)

### execute_process 命令

命令简述：用于执行一个或者多个外部命令。每一个命令的标准输出通过管道转为下一个命令的标准输入。WORKING_DIRECTORY 用于指定外部命令的工作目录，RESULT_VARIABLE 用于指定一个变量保存外部命令执行的结果，这个结果可能是最后一个执行的外部命令的退出码或者是一个描述错误条件的字符串，OUTPUT_VARIABLE 或者 ERROR_VARIABLE 用于指定一个变量保存标准输出或者标准错误，OUTPUT_QUIET 或者 ERROR_QUIET 用于忽略标准输出和标准错误。

``` cmake
# 命令语法：
execute_process(COMMAND <cmd1>[args1...]]
                  [COMMAND <cmd2>[args2...] [...]]
                  [WORKING_DIRECTORY<directory>]
                  [TIMEOUT <seconds>]
                  [RESULT_VARIABLE<variable>]
                  [OUTPUT_VARIABLE<variable>]
                  [ERROR_VARIABLE<variable>]
                  [INPUT_FILE <file>]
                  [OUTPUT_FILE <file>]
                  [ERROR_FILE <file>]
                  [OUTPUT_QUIET]
                  [ERROR_QUIET]
                 [OUTPUT_STRIP_TRAILING_WHITESPACE]
                 [ERROR_STRIP_TRAILING_WHITESPACE])

# 使用范例
execute_process(COMMAND ls)
                 
```



### add_custom_command 命令
#todo 
[CMake深度解析：掌握add\_custom\_command，精通Makefile生成规则 - 知乎](https://zhuanlan.zhihu.com/p/661284935)

### add_custom_target 命令

``` cmake
# 命令语法：
add_custom_target(Name [ALL] [command1 [args1...]] 
			[COMMAND command2 [args2...] ...] 
			[DEPENDS depend depend depend ... ] 
			[BYPRODUCTS [files...]] 
			[WORKING_DIRECTORY dir] 
			[COMMENT comment] 
			[JOB_POOL job_pool] 
			[VERBATIM] [USES_TERMINAL] 
			[COMMAND_EXPAND_LISTS] 
			[SOURCES src1 [src2...]])
```

上面的命令将搜索所有扩展名为.gcda 的文件，并删除它们。
``` cmake
add_custom_target(clean_stale_coverage_files 
			COMMAND find . -name "*.gcda" -type f -delete
			)
```

**不过与可执行目标和 库目标不同，自定义目标只有添加到依赖关系图中才会构建。**
使用 [[#add_dependencies 命令]]可以管理依赖。

CMake 可以生成graphviz 格式的依赖关系图。它支持内部和外部依赖关系!
``` bash
cmake --graphviz=test.dot .
```
可以直接从浏览器运行 Graphviz 查看，地址如下: https://dreampuf.github.io/GraphvizOnline/。

自 定 义 目 标 在 默 认 情 况 下 是 不 可 见 的， 需 要 创 建 一 个 特 殊 的 配 置 文 件 `CMakeGraphVizOptions.cmake`， 可 以 自 定 义 图 形。 设 置 了 一 个 方 便 的 定 制 命 令 (`GRAPHVIZ_CUSTOM_TARGETS TRUE`); 将 其 添 加 到 特 殊 的 配 置 文 件 中， 以 支 持 报 告 图中的自定义目标。


[cmake：添加自定义操作 - 知乎](https://zhuanlan.zhihu.com/p/95771200)
## 全局命令 (不推荐)
### include_directories 命令

命令简述：全局设置头文件搜索路径，这些设定的目录将被编译器用来查找 include 文件, 它会影响整个项目中的所有目标。
				 当前CMakeList.txt中的所有目标以及所有在其调用点之后添加的子目录中的所有目标将具有此头文件搜索路径。
				 更建议用`target_include_directories`

``` cmake
# 命令语法
include_directories([AFTER|BEFORE] [SYSTEM] dir1 dir2 …)

# 使用范例
include_directories(${PROJECT_SOURCE_DIR}/lib)

```


### add_definitions 命令

命令简述：用于添加编译器命令行标志（选项）包括但不限于 `-C`、`-D`、`-U` 等，通常的情况下我们使用其来添加预处理器定义(`-D`)。**是过时命令，不建议使用**。
`add_compile_definitions` 是它的新版代替，可以给当前目录及其子目录的 sources 添加编译选项。
而且现在更推荐现代写法的`target_compile_definitions`

``` cmake
# 命令语法
add_definitions(-DFOO -DBAR …)

# 使用范例
add_definitions(-D_UNICODE -DUNICODE)

# 与add_compile_definitions的区别
add_definitions(-DFOO=1 -DBAR=2)
add_compile_definitions(FOO=1 BAR=2)
```

对比现代写法 ![[#`target_compile_definitions` 命令]]

#todo 
1. link_libraries
2. link_directories
3. add_link_options

他们由于未被声明绑定


## 其它命令

#todo



# 属性(properties)

属性（Properties）和变量（Variables）是两个不同的概念。变量是可以独立存在的，而属性需要依赖一个具体的目标(可以类比于类的字段)。

***现代化的CMake是围绕 Target 和 Property 来定义的。***

变量是使用`set()`命令定义的，可以具有不同的作用域，包括全局作用域和局部作用域。全局变量在整个项目中可见，而局部变量通常只在定义它们的作用域内可见。变量通常用于存储和传递值，如文件路径、编译选项等。CMake中的变量是一种灵活的机制，可以用于在不同的地方存储和检索值。

属性是通过在目标（如可执行文件、库等）上使用`set_target_properties()`或`set_directory_properties()`等命令定义的，例如：
``` cmake
set_target_properties(target_name PROPERTIES PROPERTY_NAME value)
```

属性通常关联到具体的目标，如可执行文件或库。它们的作用范围限定在目标上。
属性用于配置目标的各种行为，例如编译选项、输出路径、链接库等。通过设置目标的属性，可以定制生成的可执行文件或库的各个方面。

``` cmake
# 通过target的属性，指定target C++标准的版本,只影响target
set_target_properties(my_executable PROPERTIES CXX_STANDARD 11) 

# 设置全局变量，指定C++标准的版本, 影响全局
set(CMAKE_CXX_STANDARD 11)
```

`get_property` 和 `set_property` 可以用于获取/设置任何类型的属性，包括`Global Scope`  `Directories` `Targets` `Tests` `Source Files`等等。
具体可以参考 [属性（property）](https://cmake.org/cmake/help/latest/manual/cmake-properties.7.html)

## `get_property`
[get\_property — CMake 3.28.0 Documentation](https://cmake.org/cmake/help/latest/command/get_property.html#command:get_property)

## `set_property`
[set\_property — CMake 3.28.0 Documentation](https://cmake.org/cmake/help/latest/command/set_property.html#set-property)


# Target 

参考文章 [Modern CMake 简介](https://mp.weixin.qq.com/s?__biz=MzU5NzE2Njk4NQ==&mid=2247483664&idx=1&sn=4e4d62e49295be830c7a00a4515517f7&scene=58&subscene=0)

现代化的CMake是围绕 **_Target_ 和 _Property_** 来定义的，并且竭力避免出现变量variable的定义。Variable横行是典型CMake2.8时期的风格。现代版的CMake更像是在遵循OOP的规则，通过target来约束link、compile等相关属性的作用域。

如果把一个Target想象成一个对象（Object），会发现两者的组织方式非常相似, 前后CMake可以类比为C和C++。
**因此建议围绕`Target`来写cmake, 去除之前全局命令的写法。**

如果把一个Target想象成一个对象（Object），会发现两者的组织方式非常相似：

**构造函数**：
- add_executable()
- add_library()

**成员函数**：
- get_target_property()
- set_target_properties()
- get_property(TARGET)
- set_property(TARGET)
    
- target_compile_definitions()
- target_compile_features()
- target_compile_options()
- target_include_directories()
- target_link_libraries()
- target_sources()

**成员变量**
- Target properties（太多）

在Target中有两个概念非常重要：Build-Requirements 和 Usage-Requirements。这两个概念对于理解为什么现代CMake会如此设计提供了指导意义。
- Build-Requirements：包含了所有构建Target必须的材料。如源代码，include路径，预编译命令，链接依赖，编译/链接选项，编译/链接特性等。
- Usage-Requirements：包含了所有使用Target必须的材料。如源代码，include路径，预编译命令，链接依赖，编译/链接选项，编译/链接特性等。这些往往是当另一个Target需要使用当前target时，必须包含的依赖。

传统的CMake和现代化的CMake的主要区别（非语法层面）如下图所示。Traditioncal CMake在设置build-requirements和usage-requirements上都依赖手动输入命令，并且人工维持其作用域（变量的作用域以目录为单位）。而Modern CMake在设置上述requirement均以target为单位，所以在传递target属性到其依赖的下游链条中更自动也更智能。

![图片](modern_cmake_target.jpg)

在Moden CMake中新增了不少关键字，其中最常见的是**PUBLIC、PRIVATE、INTERFACE**。

##  `PUBLIC PRIVATE INTERFACE`

参数 `INTERFACE`，`PUBLIC`，和 `PRIVATE` 主要用于指定头文件搜索路径的范围，以便在构建过程中正确传递依赖关系。以下是这三个参数的区别：

1. **PRIVATE:**
    - `PRIVATE` 用于将指定的头文件目录添加到目标的私有头文件搜索路径中。
    - 这意味着只有当前目标能够访问这些头文件目录，而依赖于当前目标的其他目标无法访问这些目录。
    - 适用于当前目标的实现细节，其他目标不应直接访问的头文件。
2. **INTERFACE:**
    - `INTERFACE` 用于将指定的头文件目录添加到目标的接口头文件搜索路径中。
    - 接口头文件搜索路径会传递给依赖于当前目标的其他目标，但不影响当前目标本身。
    - 适用于定义库的接口，以确保依赖于该库的目标能够找到必要的头文件。
1. **PUBLIC:**
    - `PUBLIC` 同时影响当前目标和依赖于当前目标的其他目标，将指定的头文件目录添加到目标的公共头文件搜索路径中。
    - 适用于需要在当前目标和其依赖项之间共享的头文件。

**`PUBLIC`  和`PRIVATE`  区别示例：**

```cmake
add_library(my_library my_source.cpp)

# 将 private_include_dir 添加到 my_library 的私有头文件搜索路径中
target_include_directories(my_library PRIVATE private_include_dir)

# 将 public_include_dir 添加到 my_library 的公共头文件搜索路径中
target_include_directories(my_library PUBLIC public_include_dir)

```

在这个例子中，`my_library` 的实现细节位于 `private_include_dir`，其他目标（依赖于 `my_library` 的目标）可以访问 `public_include_dir` 中的头文件，而不能访问`private_include_dir`。

**`PUBLIC`  和`INTERFACE`  区别示例：**

- `PUBLIC` 影响当前目标和依赖于当前目标的其他目标。
- `INTERFACE` 只影响依赖于当前目标的其他目标，而不直接影响当前目标。

示例：
``` cmake
add_library(my_library my_source.cpp)

# 将 public_include_dir 添加到 my_library 的公共头文件搜索路径中
target_include_directories(my_library PUBLIC public_include_dir)

# 将 interface_include_dir 添加到 my_library 的接口头文件搜索路径中
target_include_directories(my_library INTERFACE interface_include_dir)

```

在这个例子中，`public_include_dir` 被添加到 `my_library` 的公共头文件搜索路径，这将影响 `my_library` 本身以及依赖于 `my_library` 的其他目标。而 `interface_include_dir` 被添加到 `my_library` 的接口头文件搜索路径，这只会影响依赖于 `my_library` 的其他目标。

**理解 `INTERFACE`** 

当一个目标依赖于另一个目标时，INTERFACE 头文件搜索路径将被传递给依赖目标，但不会直接影响定义这些路径的目标本身。

下面通过一个示例来说明这个概念：
``` cmake
# 定义库目标 my_library，它有一个头文件 include/my_header.h
add_library(my_library my_source.cpp)
target_include_directories(my_library INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/include)

# 定义可执行目标 my_executable，它依赖于 my_library
add_executable(my_executable main.cpp)
target_link_libraries(my_executable my_library)

```

在这个例子中，`my_library` 使用 `target_include_directories` 将 `include` 目录添加到 INTERFACE 头文件搜索路径中。然后，`my_executable` 通过 `target_link_libraries` 声明它依赖于 `my_library`。

现在，让我们来理解这个过程：

1. **`my_library` 的影响：**
    - `my_library` 自身不直接使用 INTERFACE 指定的头文件搜索路径，因为它不包含 `#include` 语句使用这些路径中的头文件。所以，对于 `my_library` 本身来说，INTERFACE 头文件搜索路径没有直接影响。
    - 
2. **`my_executable` 的影响：**
    - 当 `my_executable` 声明依赖于 `my_library` 时，由于 `my_library` 使用 INTERFACE 头文件搜索路径，`my_executable` 也会继承这些路径。
    - 这意味着在构建 `my_executable` 时，CMake 会考虑 `my_library` 的 INTERFACE 头文件搜索路径，以确保它可以找到 `include/my_header.h`。

这样设计的好处在于，如果你有其他目标依赖于 `my_executable`，它们也会继承 INTERFACE 头文件搜索路径，从而保证整个项目的一致性。

总之，INTERFACE 头文件搜索路径是为了确保目标和依赖于它的其他目标都能正确找到相关的头文件，从而提高构建系统的模块化性和可维护性。


自我总结：
如果都从源码构建的话，一般只使用 `PUBLIC` 和`PRIVATE` ，

`PUBLIC` 一般用在普通库的构建，它会影响当前目标和 依赖于当前目标的其它目标（上层库或者可执行程序）。
`PRIVATE`  一般用于可执行程序的构建（如testbed），它只影响当前的目标，不会将依赖向上传递。

如果使用已经编好的库，使用 `INTERFACE`， 然后用`add_subdirectory` 加到当前目标中来，更好管理。
`INTERFACE` 只影响它的依赖，而不影响当前目标，适合用于编好的依赖库。


**从源码来分析**，从本质上来看`target_include_directories`总归是为目标的属性赋值。如果是设置为`PRIVATE`，那么它实际是给 `INCLUDE_DIRECTORIES`这个属性赋值。如果是设置为`INTERFACE`，那么它实际是给`INTERFACE_INCLUDE_DIRECTORIES`这个属性赋值。如果设置为`PUBLIC`，那么同时给`INCLUDE_DIRECTORIES`和`INTERFACE_INCLUDE_DIRECTORIES`赋值。相关的属性解释在[https://cmake.org/cmake/help/latest/manual/cmake-properties.7.html](https://cmake.org/cmake/help/latest/manual/cmake-properties.7.html)

最后总结一下，目标A本身编译生成时，找的是自己里面的`INCLUDE_DIRECTORIES`这个路径。目标B生成需要链接目标A，那么它既找自己里面的`INCLUDE_DIRECTORIES`路径，也找目标A的`INTERFACE_INCLUDE_DIRECTORIES`路径[[#^ref5|参考链接]][^5]   。


## target构建命令

CMake 有三个基本命令，用于定义 CMake Target。
### add_executable 命令

命令简述：用于指定从一组源文件 source1 source2 … sourceN 编译出一个可执行文件且命名为 name
``` cmake
# 命令语法
add_executable(<name> [WIN32] [MACOSX_BUNDLE][EXCLUDE_FROM_ALL] source1 source2 … sourceN)

# 使用范例

# 生成可执行文件main
add_executable(main main.cpp)
add_executable(main ${DIR_SRCS})
```

### add_library 命令

命令简述：用于指定从一组源文件 source1 source2 … sourceN 编译出一个库文件且命名为 name
``` cmake
# 命令语法
add_library([STATIC | SHARED | MODULE] [EXCLUDE_FROM_ALL] source1source2 … sourceN)

# 使用范例
add_library(Lib ${DIR_SRCS})          # 默认是静态库，可以通过BUILD_SHARED_LIBS控制
add_library(Lib SHARED ${DIR_SRCS})   # 生成共享库目标
add_library(Lib STATIC ${DIR_SRCS})   # 生成静态库目标


# 这两段代码是相同的
add_library(${LIBNAME} ${SRC_FILES} ${HEADER_FILES})

add_library(${LIBNAME}) 
target_sources(${LIBNAME} PUBLIC ${SRC_FILES} ${HEADER_FILES})


# 创建了target, 但是不会构建库，使用了OBJECT关键字，这表示只对生成目标文件感兴趣，
# 一般用于收集编译源文件，并进行依赖传递
add_library(cars OBJECT car.cpp)

```

从外部导入已经编好的库：
```cmake
add_library(${PROJECT_NAME} SHARED IMPORTED GLOBAL)

# 设置它的属性, 注意需要在配置时就确认，所以不能用生成表达式
set_target_properties(${PROJECT_NAME} PROPERTIES
  INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/inc"
  IMPORTED_IMPLIB "${LIB_PATH}"   # windows的动态库需要的符号表lib。
  IMPORTED_LOCATION "${DLL_PATH}"
)
```

这行CMake命令用于在CMake构建系统中添加一个共享库（shared library）。让我们逐步解释这个命令：

1. `add_library`: 这是CMake的一个命令，用于向项目中添加库。它可以用于创建静态库、共享库或模块库。
2. `${PROJECT_NAME}`: 这是一个CMake变量，表示当前项目的名称。`${PROJECT_NAME}`会被替换为你在项目中设置的`project()`命令中指定的项目名称。
3. `SHARED`: 这个关键字指定要创建的库的类型。在这里，`SHARED`表示创建一个共享库（动态链接库，DLL或共享对象，SO）。
4. `IMPORTED`: 这个关键字表示库是由外部提供的，而不是由CMake构建系统生成的。它告诉CMake不需要尝试构建这个库，而是将其作为外部库进行处理。
5. `GLOBAL`: 这个关键字将库的作用域设置为全局，使得该库可以在整个项目中的任何地方访问。

因此，这一行命令的作用是告诉CMake在项目中添加一个全局的、由外部提供的共享库，并且这个库的名称与项目的名称相同。这通常用于将预编译的库与CMake项目关联起来，而不需要在项目中显式地指定库的源代码或构建规则。相反，开发人员只需确保外部提供的库与其项目相匹配即可。

### add_custom_target命令

见前面章节 `命令`-`高级命令`
![[#add_custom_target 命令]]


### 伪目标(pseudo targets)
具体来说，这是指不表示构建系 统的输出而是表示输入的东西——外部依赖项、别名等。这些是伪目标，或者没有到达生成的构建 系统的目标。

#### 导入库目标

若浏览了目录，就会知道将讨论 CMake 如何管理外部依赖项——其他项目、库等。导入的目标 基本上是这个过程的产物。CMake 可以定义它们作为 find_ package() 指令的结果。 可以调整这样一个目标的目标属性: 编译定义、编译选项、包含目录等——甚至将支持可传递 的使用需求。但应该将它们视为不可变的，不要改变它们的来源或依赖关系。 导入目标的范围可以是全局的，也可以是本地目录 (在子目录中可见，但在父目录中不可见)。

```cmake
add_library(<name> <type> IMPORTED [GLOBAL])

```

```cmake

set(LIB_DIR "${CMAKE_CURRENT_LIST_DIR}/lib/${ARCBUILD_PLATFORM}-${ARCBUILD_ARCH}")

if(BUILD_SHARED_LIBS)
	set(LIB_PATH "${LIB_DIR}-shared/${PROJECT_NAME}.lib")
	set(DLL_PATH "$${LIB_DIR}-shared/${PROJECT_NAME}.dll")
	add_library(${PROJECT_NAME} SHARED IMPORTED GLOBAL)
	
	set_target_properties(${PROJECT_NAME} PROPERTIES
	  INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/inc"
	  IMPORTED_IMPLIB "${LIB_PATH}"
	  IMPORTED_LOCATION "${DLL_PATH}"
	)
else()
    set(LIB_PATH "${LIB_DIR}-static/${PROJECT_NAME}.lib")
    add_library(${PROJECT_NAME} STATIC IMPORTED GLOBAL)

    set_target_properties(${PROJECT_NAME} PROPERTIES
      INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/inc"
      IMPORTED_LOCATION "${LIB_PATH}"
	)

endif()

# 注意: 库的位置总是使用 `IMPORTED_LOCATION`, 而windows下的动态库，需要额外配置导入库`IMPORTED_IMPLIB`

# 可以更详细的设置
# set_target_properties(${PROJECT_NAME} PROPERTIES  
#   INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/inc"    
#   IMPORTED_IMPLIB_DEBUG "${LIB_PATH}"  
#   IMPORTED_IMPLIB_RELEASE "${LIB_PATH}"  
#   IMPORTED_IMPLIB_MINSIZEREL "${LIB_PATH}"  
#   IMPORTED_IMPLIB_RELWITHDEBINFO "${LIB_PATH}"  
#   IMPORTED_LOCATION_DEBUG "${DLL_PATH}"  
#   IMPORTED_LOCATION_RELEASE "${DLL_PATH}"  
#   IMPORTED_LOCATION_MINSIZEREL "${DLL_PATH}"  
#   IMPORTED_LOCATION_RELWITHDEBINFO "${DLL_PATH}"  
# )

```

#### 别名目标 

别名目标完全符合期望——不同的名称下创建对目标的另一个引用。可以使用以下指令为可执 行文件和库创建别名目标: 
``` c
add_executable(<name> ALIAS <target>) 
add_library(<name> ALIAS <target>)
``` 
别名目标的属性只读，并且不能安装或导出别名 (在生成的构建系统中不可见)。 那么，使用别名的理由到底是什么呢? 当项目的某些部分 (如子目录) 需要具有特定名称的目标 时，就很方便了，而实际的实现可能根据情况在不同的名称下可用。例如，可能希望构建一个随解 决方案一起提供的库，或者根据用户的选择导入。

**我的理解**：如果同一个库有多个版本，比如`hand1`, `hand2` `hand3`, 链接的时候直接链接别名 `hand`。具体使用哪个可以用 `ALIAS`来确定。[[#**add_subdirectory 命令**]] 也能做到类似的功能。

#### 接口库 

这是一个有趣的构造——一个库不编译任何东西，而是作为一个实用工具目标。其整个概念是 围绕传播属性 (传递使用需求) 构建的。 

接口库有两个主要用途——纯头文件库和将一堆传播的属性捆绑到一个逻辑单元中。 

使用 `add_library(INTERFACE)` 创建纯头文件库相当容易: 
``` cmake
add_library(Eigen INTERFACE src/eigen.h src/vector.h src/matrix.h ) 

target_include_directories(Eigen INTERFACE 
			$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src> 
			$<INSTALL_INTERFACE:include/Eigen> 
			)
```
 前面的代码片段中，创建了一个具有三个头文件的特征接口库。接下来，使用生成器表达式， 在导出目标时将其 `include` 目录设置为 `${CMAKE_CURRENT_SOURCE_DIR}/src`，在安装目标时设 置为 `include/Eigen`。 要使用这样的库，只需要链接`Eigen`即可: 
 ```cmake
target_link_libraries(executable Eigen)
```
这里没有实际的链接，但是 CMake 将此命令理解为将所有` INTERFACE `属性传播到可执行目标的请求。 
第二个用例使用了完全相同的机制，但目的不同——其创建了一个逻辑目标，可以作为传播属 性的占位符。然后，可以将这个目标用作其他目标的依赖项，并以一种简洁、方便的方式设置属性

这里有一个例子: 
``` cmake
add_library(warning_props INTERFACE) 
target_compile_options(warning_props INTERFACE -Wall -Wextra -Wpedantic ) 

target_link_libraries(executable warning_props) 
```
`add_library(INTERFACE) `指令创建一个逻辑 `warning_props `目标，用于在可执行目标上设置第 二个命令中指定的编译选项。推荐使用这些 `INTERFACE` 目标，这提高了代码的可读性和可重用性。 可以把它看作是将一堆神奇的值重构为一个命名良好的变量，还建议使用`_props` 后缀将接口库与常 规库区分开来。

**我的理解**：接口库这个是为了纯头文件的库设计的。


## target常用命令

**这些指令都支持生成器表达式。**

### `target_include_directories` 命令

命令简述：指定目标包含的头文件路径。用于为特定目标设置头文件搜索路径，而不是全局影响。

对应的老式写法，全局命令是 `include_directories`。

``` cmake
# 命令语法
target_include_directories(<target> [SYSTEM] [AFTER|BEFORE] <INTERFACE|PUBLIC|PRIVATE> [items1…])

# 使用范例
target_include_directories(target_lib PUBLIC /usr1/dirs)

```

使用 `BEFORE` 或 `AFTER` 确定路径是应该添加到目标 `INCLUDE_DIRECTORIES` 属性的前面还 是后面，这仍然由编译器决定是在默认目录之前还是之后检查这里提供的目录 (通常是在之前)。 

`SYSTEM` 关键字通知编译器所提供的目录是标准的系统目录 (与尖括号形式一起使用)。对于许 多编译器，这个值将作为`-system `标志提供。

### `target_link_libraries` 命令

#todo  这个命令值得深入研究， 如果它传入的 item是target, 可以获取相关的依赖等，

命令简述：用于指定 target 需要链接 item1 item2 …。这里 target 必须已经被创建(可以由add_executable或者add_library创建)，**链接的 item 可以是已经存在的 target（依赖关系会自动添加，要设置为PUBLIC INTERFACE）**

``` cmake
# 命令语法
target_link_libraries(<target> [item1 [item2 […]]][[debug|optimized|general] ] …)

# 使用范例
target_link_libraries(Main Lib)
```

**链接器是按照从左到右的顺序查找符号。链接顺序可能会影响符号解析和链接的结果，尤其是对于静态库和动态库的链接。**

一般来说，链接顺序的一些建议是：

1. **将目标放在依赖项的前面：** 在 `target_link_libraries` 命令中，放置目标应该在依赖项的前面。这通常是因为链接器是按照从左到右的顺序查找符号的。
    
    `target_link_libraries(my_target PRIVATE lib1 lib2 lib3)`
    
    在这个例子中，`my_target` 依赖于 `lib1`、`lib2` 和 `lib3`，它们会按照指定的顺序链接。
    
2. **将依赖的库放在静态库之前：** 如果你同时链接了静态库和动态库，确保将动态库放在静态库之前。这是因为一些链接器会忽略之前已经解析的静态库中的符号。
    
    `target_link_libraries(my_target PRIVATE lib_static lib_dynamic)`
    
    在这个例子中，`lib_static` 是静态库，`lib_dynamic` 是动态库。
    
3. **对于某些平台，可能需要调整链接顺序：** 在某些平台上，特别是在使用 GCC 的情况下，可能需要调整链接顺序以正确解析符号。这通常是在特定情况下才需要的。


### `target_compile_definitions` 命令


添加预编译选项到编译目标中，用于定义宏，取消定义宏，以及设置宏的值。可以通过 `PUBLIC`  和`PRIVATE`  进行范围限定。通过`PUBLIC`可以传递给上层使用。

类似 `add_definitions` `add_compile_definitions`，是更现代的写法。

``` cmake
add_library(my_library source.cpp)

# 定义宏
target_compile_definitions(my_library
    PRIVATE FOO  # 定义宏 `FOO`，没有赋值
    PUBLIC BAR=1 # 定义宏 `BAR`，赋值为1
)

# 使用-U 取消宏
target_compile_definitions(my_executable PRIVATE -UBAZ)

```
在上面的示例中，`FOO=1` 是 `my_library` 的私有定义，而 `BAR=2` 是一个公共定义，将对链接到 `my_library` 的任何目标可见。

对于-D的前导符和空字符串""会被CMake忽略，例如下面命令完全等价：

```cmake
target_compile_definitions(target PUBLIC DEF_FLAG)
target_compile_definitions(target PUBLIC -DDEF_FLAG)  # -D 被移除
target_compile_definitions(target PUBLIC "" DEF_FLAG) # "" 被忽略
target_compile_definitions(target PUBLIC -D DEF_FLAG) # -D 变成 "" 然后被忽略
```


和老式写法对比一下。
[[#add_definitions 命令]]


### `target_compile_options` 命令
#todo 
该命令允许直接指定编译器选项。对应 变量`CMAKE_CXX_FLAGS` 或者  `add_compile_options`
``` cmake
# 作用全局,老式写法
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -Wall")
add_compile_options(-std=c++11 -Wall)

# 指定目标
target_compile_options(my_target PRIVATE -std=c++11 -Wall)
```

实际上可以代替`target_compile_definitions`, `target_compile_features` 。

```cmake

# case 1
target_compile_options(sample PUBLIC /arch=avx2 /Wall)

# 定义宏, 定义宏还是交给`target_compile_definitions`吧
target_compile_options(sample PRIVATE -DFOO -DBAR=1)

# 老式写法
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC -std=c++17 /bigobj")
target_compile_options(my_executable PRIVATE -fPIC /std:c++17 /bigobj)

#设置windows 静态库为 MT
if(_MT_ AND WIN32 AND NOT BUILD_SHARED_LIBS)
	message( "USE -MT (Multi-threaded)" )
    target_compile_options(${LIBNAME} PRIVATE /MT$<$<CONFIG:Debug>:d>)
endif()
```

由于`target_compile_options` 命令是直接给编译器添加编译选项，所以和使用的编译器相关，不同的编译器会有不同的设置。比如：

 - **GCC/G++ 编译器：**
```cmake
target_compile_options(my_target PRIVATE -Wall -Wextra)
```
这里使用 `-Wall` 和 `-Wextra` 开启了 GCC/G++ 的所有警告。

- **Clang 编译器：**
```cmake
target_compile_options(my_target PRIVATE -Wall -Wextra)

```
Clang 与 GCC 共享许多相同的编译选项，因此可以使用相似的语法。

- **MSVC 编译器：**
```cmake
target_compile_options(my_target PRIVATE /W4 /WX)
```
MSVC 使用不同的语法，这里使用 `/W4` 开启警告级别 4，`/WX` 将警告视为错误。

这些示例涉及开启警告的选项，但实际上，编译器选项可能包括许多其他方面，如优化级别、目标架构、调试信息等。因此，在编写跨平台的 CMake 文件时，可能需要使用条件语句来根据不同的编译器设置不同的编译选项。

``` cmake
if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    target_compile_options(my_target PRIVATE -Wall -Wextra)
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    target_compile_options(my_target PRIVATE -Wall -Wextra)
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
    target_compile_options(my_target PRIVATE /W4 /WX)
endif()
```

这样的条件语句允许你根据不同的编译器进行设置，以确保正确的编译选项被应用。


使用 target_compile_options() 配置编译器。**哪些编译选项比较有用**?

- `-Werror`:  开启后会将所有警告都视为错误，除非解决了所有警告，否则代码不会编译。
- `-Wall` 和可选的`-Wextra`: 这些是认为是非常有用和有意义的警告。

### `target_compile_features` 命令
#todo
此函数专门向目标设置c / c++版本。可以在 `target_compile_options` 中搞定，或者设置`CMAKE_CXX_STANDARD` (老式写法，不推荐)。


``` cmake
# 过时写法，作用全局
set(CMAKE_CXX_STANDARD 11)

# 现代写法
target_compile_features(sample PRIVATE cxx_std_11)
target_compile_features(sample PUBLIC cxx_std_17)

# 对应的
target_compile_options(my_executable PRIVATE -std=c++11) # GCC/G++
target_compile_options(my_executable PRIVATE /std:c++11) # MSVC

```

推荐使用 `target_compile_features`，因为它提供更清晰、更语义化的方式来表达代码的要求，并且可以更轻松地**适应不同的编译器和平台**。

建议使用通用 C++ 标准：
- `cxx_std_98`
- `cxx_std_11`
-  `cxx_std_14`
- `cxx_std_17`
- `cxx_std_20`
- `cxx_std_23`

### `target_link_options` 命令
#todo 

### `target_link_directories` 命令
#todo 

### `target_sources` 命令
#todo

向已定义的目标添加源。

### `target_precompile_headers` 命令

预编译头文件。


## target常用属性

现代cmake中可以通过 `get_target_property()` 或者 `get_property(TARGET ...)` 来获得`target`的属性，通过` set_target_properties()` 或者 `set_property(TARGET ...)`来设置`target`的属性。

[get\_target\_property — CMake 3.28.0 Documentation](https://cmake.org/cmake/help/latest/command/get_target_property.html#get-target-property)
[set\_target\_properties — CMake 3.28.0 Documentation](https://cmake.org/cmake/help/latest/command/set_target_properties.html#command:set_target_properties)

**Properties on Targets (Targets的属性列表)**:
[cmake-properties(7) — CMake 3.28.0 Documentation](https://cmake.org/cmake/help/latest/manual/cmake-properties.7.html#target-properties)


- **设置属性**

``` cmake

#设置包含路径, 见上文
target_include_directories(target_name PUBLIC include_dir)

#设置链接的库, 见上文
target_link_libraries(target_name library_name)

#设置编译时预定义的宏
target_compile_definitions(target_name PUBLIC definition)

#设置编译选项
target_compile_options(target_name PUBLIC option)

#设置源文件
target_sources(target_name PUBLIC source_file)

#设置编译C++特性
target_compile_features(target_name PUBLIC feature)


# 设置目标的属性还有一个通用命令，可以用这个命令代替上述指令
set_target_properties(target1 target2 ...
                      PROPERTIES prop1 value1
                      prop2 value2 ...)

# 除了作用域PUBLIC等，基本等价上述设置
set_target_properties(target_name 
                      PROPERTIES 
                      INCLUDE_DIRECTORIES include_dir
                      COMPILE_DEFINITIONS definition 
                      COMPILE_OPTIONS option
                      LINK_LIBRARIES library_name
                      SOURCES source_file
                      COMPILE_FEATURES feature)

```

- **获取属性**

使用`get_target_property`可以获得目标某个属性的值，并存放到变量中供我们使用。使用下面代码可以把，我们上面赋值的属性取出来：

```cmake

get_target_property(<VAR> target property)

get_target_property(include_dir target_name INCLUDE_DIRECTORIES)
get_target_property(definition target_name COMPILE_DEFINITIONS)
get_target_property(option target_name COMPILE_OPTIONS)
get_target_property(library_name target_name LINK_LIBRARIES)
get_target_property(source_file target_name SOURCES)
get_target_property(feature target_name COMPILE_FEATURES)
```




### set_target_properties 命令

命令简述：设置目标的一些属性来改变它们构建的方式。为一个目标设置属性。

该命令的语法是列出所有你想要变更的文件，然后提供你想要设置的值。你能够使用任何你想要的属性/值对，并且在随后的代码中调用`GET_TARGET_PROPERTY`命令取出属性的值。

``` cmake
# 命令语法
set_target_properties(target1 target2 ... PROPERTIES prop1 value1 prop2 value2 ...)


# 使用范例
set_target_properties(${PROJECT_NAME} PROPERTIES
  # 头文件路径
  INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_SOURCE_DIR}/inc"
  # windows下的动态库的符号表，也叫导入库（Import Library） 容易和静态库.lib混淆
  IMPORTED_IMPLIB "${LIB_PATH}"
  # 库的位置(.a .so .lib .dll .dylib)
  IMPORTED_LOCATION "${DLL_PATH}"
)

# 设置输出名称 ，把 `my_executable` 的输出名称设置为 `my_custom_name`。
set_target_properties(my_executable
    PROPERTIES
    OUTPUT_NAME my_custom_name
)

# 设置编译器标志，将为 `my_library` 设置编译标志，例如 `-Wall` 表示开启所有警告。
set_target_properties(my_library
    PROPERTIES
    COMPILE_FLAGS "-Wall"
)

# 设置输出目录， 将设置 `my_executable` 的运行时输出目录为 `${CMAKE_BINARY_DIR}/bin`。
set_target_properties(my_executable
    PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin
)

# 启用 C++ 11 标准，见set
set_target_properties(my_executable
    PROPERTIES
    CXX_STANDARD 11
    CXX_STANDARD_REQUIRED ON
)

# 设置链接器标志，设置 `my_library` 的链接标志，例如 `-Wl,-rpath,./lib` 表示将 `./lib` 添加到运行时库搜索路径。
set_target_properties(my_library
    PROPERTIES
    LINK_FLAGS "-Wl,-rpath,./lib"
)

# 设置target的输出目录
set_target_properties(${LIBNAME} PROPERTIES
	ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/lib	# .a .lib
    LIBRARY_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/lib	# .so
	RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/lib	# .dll, .exe
)


```


影响一个目标输出文件的名字的属性详述如下。PREFIX和SUFFIX属性覆盖了默认的目标名前缀（比如lib）和后缀（比如.so）。`IMPORT_PREFIX`和`IMPORT_SUFFIX`是与之等价的属性，不过针对的是`DLL`（共享库目标）的导入库。在构建目标时，`OUTPUT_NAME`属性设置目标的真实名字，并且可以用来辅助创建两个具有相同名字的目标，即使CMake需要唯一的逻辑目标名。`<CONFIG>_OUTPUT_NAME`可以为不同的配置设置输出的目标名字。当目标在指定的配置名`<CONFIG>`（全部大写，例如`DEBUG_POSTFIX`）下被构建时，`<CONFIG>_POSTFIX`为目标的真实名字设置一个后缀。该属性的值在目标创建时被初始化为`CMAKE_<CONFIG>_POSTFIX`的值（可执行目标除外，因为较早的CMake版本不会为可执行文件使用这个属性。）

`LINK_FLAGS`属性可以用来为一个目标的链接阶段添加额外的标志。`LINK_FLAGS_<CONFIG>`将为配置`<CONFIG>`添加链接标志，例如`DEBUG`，`RELEASE`，`MINSIZEREL`，`RELWITHDEBINFO`。`DEFINE_SYMBOL`属性设置了编译一个共享库中的源文件时才会被定义的预处理器符号名。如果这个值没有被设置的话，那么它会被设置为默认值`target_EXPORTS`（如果目标不是一个合法的C标示符的话可以用一些替代标志）。这对于检测头文件是包含在它们的库以内还是以外很有帮助，从而可以合理设置dllexport/dllimport修饰符（注意，只有在编译到的时候，这个符号才会被定义；因此猜测在代码中，判断预处理符号是否被定义可以知道依赖库是导入的还是导出的——译注）。`COMPILE_FLAGS`属性可以设置附加的编译器标志，它们会在构建目标内的源文件时被用到。它也可以用来传递附加的预处理器定义。

`LINKER_LANGUAGE`属性用来改变链接可执行文件或共享库的工具。默认的值是设置与库中的文件相匹配的语言。CXX和C是这个属性的公共值。

对于共享库，`VERSION`和`SOVERSION`属性分别可以用来指定构建的版本号以及API版本号。当构建或者安装时，如果平台支持符号链接并且链接器支持so名字，那么恰当的符号链接会被创建。如果只指定两者中的一个，缺失的另一个假定为具有相同的版本号。对于可执行文件，`VERSION`可以被用来指定构建版本号。当构建或者安装时，如果该平台支持符号链接，那么合适的符号链接会被创建。对于在Windows系统而言，共享库和可执行文件的`VERSION`属性被解析成为一个`"major.minor"`的版本号。这些版本号被用做该二进制文件的镜像版本。

还有一些属性用来指定`RPATH`规则。`INSTALL_RPATH`是一个分号分隔的list，它指定了在安装目标时使用的`rpath`（针对支持`rpath`的平台而言）（`-rpath`在gcc中用于在编译时指定加载动态库的路径；优先级较系统库路径要高。详情参见CMake Wiki has moved——译注）。`INSTALL_RPATH_USE_LINK_PATH`是一个布尔值属性，如果它被设置为真，那么在链接器的搜索路径中以及工程之外的目录会被附加到`INSTALL_RPATH`之后。`SKIP_BUILD_RPATH`是一个布尔值属性，它指定了是否跳过一个`rpath`的自动生成过程，从而可以从构建树开始运行。`BUILD_WITH_INSTALL_RPATH`是一个布尔值属性，它指定了是否将在构建树上的目标与`INSTALL_RPATH`链接。该属性要优先于`SKIP_BUILD_RPATH`，因此避免了安装之前的重新链接。`INSTALL_NAME_DIR`是一个字符串属性，它用于在Mac OSX系统上，指定了被安装的目标中使用的共享库的"install_name"域的目录部分。如果目标已经被创建，变量`CMAKE_INSTALL_RPATH`, `CMAKE_INSTALL_RPATH_USE_LINK_PATH`, `CMAKE_SKIP_BUILD_RPATH`,` CMAKE_BUILD_WITH_INSTALL_RPATH`和`CMAKE_INSTALL_NAME_DIR`的值会被用来初始化这个属性。



# 安装

许多情况下，我们要把生成的库和相关的头文件，安装到对应的系统目录下，去供别人使用。
这时候需要使用 ![[#install命令]]


# 测试

添加测试同样很简单。CMake 提供了一个称为 CTest 的测试工具。我们要做的只是在项目根目录的 CMakeLists 文件中调用一系列的 `add_test` 命令。

```cmake
# 启用测试
enable_testing()

# 测试程序是否成功运行
add_test (test_run Demo 5 2)

# 测试帮助信息是否可以正常提示
add_test (test_usage Demo)
set_tests_properties (test_usage
  PROPERTIES PASS_REGULAR_EXPRESSION "Usage: .* base exponent")

# 测试 5 的平方
add_test (test_5_2 Demo 5 2)

set_tests_properties (test_5_2
 PROPERTIES PASS_REGULAR_EXPRESSION "is 25")

# 测试 10 的 5 次方
add_test (test_10_5 Demo 10 5)

set_tests_properties (test_10_5
 PROPERTIES PASS_REGULAR_EXPRESSION "is 100000")

# 测试 2 的 10 次方
add_test (test_2_10 Demo 2 10)

set_tests_properties (test_2_10
 PROPERTIES PASS_REGULAR_EXPRESSION "is 1024")
```

上面的代码包含了四个测试。第一个测试 `test_run` 用来测试程序是否成功运行并返回 0 值。剩下的三个测试分别用来测试 5 的 平方、10 的 5 次方、2 的 10 次方是否都能得到正确的结果。其中 `PASS_REGULAR_EXPRESSION` 用来测试输出是否包含后面跟着的字符串。让我们看看测试的结果：

```cpp
[ehome@xman Demo5]$ make test
Running tests...
Test project /home/ehome/Documents/programming/C/power/Demo5
    Start 1: test_run
1/4 Test #1: test_run .........................   Passed    0.00 sec
    Start 2: test_5_2
2/4 Test #2: test_5_2 .........................   Passed    0.00 sec
    Start 3: test_10_5
3/4 Test #3: test_10_5 ........................   Passed    0.00 sec
    Start 4: test_2_10
4/4 Test #4: test_2_10 ........................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 4

Total Test time (real) =   0.01 sec
```

如果要测试更多的输入数据，像上面那样一个个写测试用例未免太繁琐。这时可以通过编写宏来实现：

```cpp
# 定义一个宏，用来简化测试工作
macro (do_test arg1 arg2 result)
  add_test (test_${arg1}_${arg2} Demo ${arg1} ${arg2})
  set_tests_properties (test_${arg1}_${arg2}
    PROPERTIES PASS_REGULAR_EXPRESSION ${result})
endmacro (do_test)
 
# 使用该宏进行一系列的数据测试
do_test (5 2 "is 25")
do_test (10 5 "is 100000")
do_test (2 10 "is 1024")
```

关于 CTest 的更详细的用法可以通过 `man 1 ctest` 参考 CTest 的文档。[[#^ref6|参考链接]][^6]

# find_package 第三方库
#todo 

参考 [[#^ref1| 链接1]]  [[#^ref5|链接5]] 


# VS相关设置

Visual Studio 官方指导：
[Visual Studio 中的 CMake 项目 | Microsoft Learn](https://learn.microsoft.com/zh-cn/cpp/build/cmake-projects-in-visual-studio?view=msvc-170)

``` cmake
# 设置 Visual Studio 启动项目  
set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT HSG_testbed)
    

# 组织文件目录结构
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} PREFIX "src" FILES ${sources})  
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} PREFIX "include" FILES ${export_headers})

# 设置依赖的dll路径
set_target_properties(${EXENAME} PROPERTIES VS_DEBUGGER_ENVIRONMENT "${DLL_PATH}")

```

## Visual Studio设置加载dll路径

运行 Visual Studio 编译的程序时遇到缺少 DLL 的问题，有几种常见的方法可以尝试解决：
1. **将 .dll 放到执行文件所在目录**
2. **设置环境变量 PATH**: 将包含缺少 DLL 的目录添加到系统的 PATH 环境变量中。这样，系统会在所有路径中查找所需的 DLL。
3. 在Visual Studio的`配置属性` -- `调试` --`环境` 中，添加 `path=dll_path;$(path)`,`dll_path`是需要加载的dll所在的路径，`$(path)`是环境变量定义的
4. `cmake`中使用 `set_target_properties(${EXENAME} PROPERTIES VS_DEBUGGER_ENVIRONMENT "${DLL_PATH}")` 设置
5. `arcpkg`中使用`arcpkg_vs_debugger_environment(${EXENAME})` ，它会自动寻找依赖的`target`，且加载目标属性为`IMPORTED`的路径。 


## 使用 CMake Preset简化输入

参考链接
[现代 CMake 进阶 | CMake Preset](https://mp.weixin.qq.com/s?__biz=MzA3MTcxMTA2Nw==&mid=2247492453&idx=1&sn=fdb57c3958c7d152f00f3c286864157d&scene=58&subscene=0)

[cmake-presets(7) — CMake 3.28.0 Documentation](https://cmake.org/cmake/help/latest/manual/cmake-presets.7.html)

``` bash
:: 动态库
cmake -DBUILD_SHARED_LIBS=ON -DBUILD_TESTBED=ON -D_MG_=ON -D_DG_=ON -S ./ -B build -v 4

:: 静态库
::MD
cmake -DBUILD_SHARED_LIBS=OFF -DBUILD_TESTBED=ON -D_MG_=ON -D_DG_=ON -D_HTCROP_BODYBASED_=ON -S ./ -B build -v 4

:: MT
::cmake -DBUILD_SHARED_LIBS=OFF -DBUILD_TESTBED=ON -D_MT_=ON -D_MG_=ON -D_DG_=ON -S ./ -B build -v 4

```

编写CMake Preset设置，`CMakePresets.json`

``` json
# 注意 CMake Presets 在`3.21.1`中引入，版本不应该低于这个版本
# CMakePresets.json
{
  "version": 3,
  "cmakeMinimumRequired": {
    "major": 3,
    "minor": 23,
    "patch": 0
  },
  "configurePresets": [
    {
      "name": "shared",
      "description": "Build shared libraries with VS2019, x64, and testbed",
      "generator": "Visual Studio 16 2019",
      "binaryDir": "${sourceDir}/build",
      "architecture": "x64",
      "cacheVariables": {
        "BUILD_SHARED_LIBS": "ON",
        "BUILD_TESTBED": "ON",
        "_MG_": "ON",
        "_DG_": "ON"
      }
    },
    {
      "name": "static-md",
      "description": "Build static libraries with VS2019, x64, MD, and testbed",
      "generator": "Visual Studio 16 2019",
      "binaryDir": "${sourceDir}/build",
      "architecture": "x64",
      "cacheVariables": {
        "BUILD_SHARED_LIBS": "OFF",
        "BUILD_TESTBED": "ON",
        "_MG_": "ON",
        "_DG_": "ON", 
        "_HTCROP_BODYBASED_": "ON"
      }
    },
    {
      "name": "static-mt",
      "description": "Build static libraries with VS2019, x64, MT, and testbed",
      "generator": "Visual Studio 16 2019",
      "binaryDir": "${sourceDir}/build",
      "architecture": "x64",
      "cacheVariables": {
        "BUILD_SHARED_LIBS": "OFF",
        "BUILD_TESTBED": "ON",
        "_MT_": "ON",
        "_MG_": "ON",
        "_DG_": "ON"
      }
    }
  ]
}
```

其中 `binaryDir` 等于设置 `-B`,  不过现在CMake Preset和`-P arcbuild.cmake` 冲突。

``` bash
::使用CMake Preset,
::验证-P arcbuild.cmake是否可行, ！！冲突
CMake --preset=static-md
CMake --preset=static-mt
CMake --preset=shared
```
# Ref

[^1]: [cmake笔记（二）\_add\_dependencies-CSDN博客](https://blog.csdn.net/weixin_44028799/article/details/121876902) ^ref1
[^2]: [【CMake 入门与进阶（10）】 CMake如何定义函数，内部参数及作用域（附代码）\_cmake 参数-CSDN博客](https://blog.csdn.net/cj_lsk/article/details/131283811) ^ref2
[^3]: [CMake语法—宏和函数（macro vs function） - kaizenly - 博客园](https://www.cnblogs.com/Braveliu/p/15621973.html) ^ref3
[^4]: [一个简单例子，完全入门CMake语法与CMakeList编写 - 知乎](https://zhuanlan.zhihu.com/p/630144233) ^ref4
[^5]: [CMake实用指南，0基础上手，建议收藏！ - 知乎](https://zhuanlan.zhihu.com/p/655302131)  ^ref5
[^6]: [全网最细的CMake教程！(强烈建议收藏) - 知乎](https://zhuanlan.zhihu.com/p/534439206) ^ref6








