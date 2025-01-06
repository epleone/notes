# cmake tips

## 编译器警告与错误

### GCC/Clang

| 选项 | 说明 | 使用场景 | 示例警告 |
|------|------|----------|----------|
| -w | 禁用所有警告 | - 第三方代码<br>- 临时调试 | 无警告输出 |
| -Wall | 启用常见警告 | - 日常开发<br>- 基本代码质量保证 | - 未使用的变量<br>- 未初始化变量<br>- 缺少返回值 |
| -Wextra | 启用额外警告 | - 更严格的检查<br>- 配合 -Wall | - 未使用的参数<br>- 符号遮蔽<br>- 空语句体 |
| -Wpedantic | 严格遵守标准 | - 跨平台代码<br>- 标准兼容性要求 | - 变长数组<br>- GNU扩展<br>- C++特性在C中使用 |
| -Werror | 警告视为错误 | - CI/CD环境<br>- 严格质量控制 | 将警告转为错误 |
 > [!warning] `-Wpedantic` 的警告类型
 > 1.  `int array[n];    // C89不允许变长数组`
 > 2.  `struct foo { };  // 不允许空结构`
 > 3.  `int x = 0b1010; // 二进制字面量是GNU扩展`
 >
 >  `-Wpedantic` 太过严格了，一般不使用。



``` cmake
# 关闭警告
add_compile_options(-w)

# 基础警告
add_compile_options(-Wall)

# 更多警告, 差不多等价 W3
add_compile_options(-Wall -Wextra)

# 更严格的警告, 差不多等价 W4
add_compile_options(-Wall -Wextra -Wpedantic)

# 只显示特定警告
add_compile_options(-Wreturn-type)  # 返回值警告
add_compile_options(-Wshadow)       # 变量遮蔽警告
add_compile_options(-Wconversion)   # 类型转换警告


# 特定警告转为错误
add_compile_options(-Werror=return-type) 

# 所有警告转为错误
add_compile_options(-Werror)

# 将警告视为错误
add_compile_options(-Wall -Werror)
# 等价于
# add_compile_options(-Wall)
# add_compile_options(-Werror)


# 更严格的警告视为错误
add_compile_options(-Wall -Wextra -Werror)
```

- `add_compile_options(-Wall)` 只设置警告
- `add_compile_options(-Wall -Werror)`  设置警告 + 将警告视作错误


> [!warning]
> CMake 的 add_compile_options() 命令是累加的，后面的选项会追加到之前的选项中。
> `add_compile_options(-Wall -Wextra -Werror)`
> 等价于
> `add_compile_options(-Wall -Wextra)`
>  `add_compile_options(-Werror)`

### MSVC

![[Visual Studio 常见问题汇总#严格编译]]

### 常用设置

``` cmake

# 将函数不是所有路径都有返回值的警告视为错误
if(MSVC)
    add_compile_options(/W4 /we4715)  # 4715 是"not all control paths return a value"的警告码
else()
    add_compile_options(-Wreturn-type -Werror=return-type)
endif()


# 将所有警告视为错误
if(MSVC)
	add_compile_options(/W4 /WX)
endif()
```

## VS 相关设置

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

### Visual Studio设置加载dll路径

运行 Visual Studio 编译的程序时遇到缺少 DLL 的问题，有几种常见的方法可以尝试解决：

1. **将 .dll 放到执行文件所在目录**
2. **设置环境变量 PATH**: 将包含缺少 DLL 的目录添加到系统的 PATH 环境变量中。这样，系统会在所有路径中查找所需的 DLL。
3. 在Visual Studio的`配置属性` -- `调试` --`环境` 中，添加 `path=dll_path;$(path)`,`dll_path`是需要加载的dll所在的路径，`$(path)`是环境变量定义的
4. `cmake`中使用 `set_target_properties(${EXENAME} PROPERTIES VS_DEBUGGER_ENVIRONMENT "${DLL_PATH}")` 设置
5. `arcpkg`中使用`arcpkg_vs_debugger_environment(${EXENAME})` ，它会自动寻找依赖的`target`，且加载目标属性为`IMPORTED`的路径。

### 设置文件夹筛选器

[cmake: how to create visual studio filters - Stack Overflow](https://stackoverflow.com/questions/33808087/cmake-how-to-create-visual-studio-filters)

See [How to set Visual Studio Filters for nested sub directory using cmake](https://stackoverflow.com/questions/31422680/how-to-set-visual-studio-filters-for-nested-sub-directory-using-cmake)

Just be aware that

- the [`source_group()`](https://cmake.org/cmake/help/v3.4/command/source_group.html) command only works in combination with [`add_library()`](https://cmake.org/cmake/help/v3.4/command/add_library.html) or [`add_executable()`](https://cmake.org/cmake/help/v3.4/command/add_executable.html) commands listing the same sources (the paths must match)
- the `source_group()` command does not check if the file actually exists (so it takes anything you give it and during project file generation it tries to match the given source group file names against files used in the project)

``` cmake

file(GLOB_RECURSE SRC_UI
    "${VD_SRC}/ui/*.cpp"
    "${VD_SRC}/ui/*.h"
)
file(GLOB_RECURSE SRC_IMPORT
    "${VD_SRC}/import/*.cpp"
    "${VD_SRC}/import/*.h"
)

add_library(VisalDesigner ${SRC_UI} ${SRC_IMPORT})

# 注意需要使用add_library用的
source_group("ui"  FILES ${SRC_UI})
source_group("import" FILES ${SRC_IMPORT})

```

下面的代码可以获取文件夹`src`下面所有文件，然后自动分组。

``` cmake
cmake_minimum_required(VERSION 3.15)

project(MyProject)

# C++标准
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)


# 递归查找所有源文件
file(GLOB_RECURSE SRC_FILES
	${CMAKE_CURRENT_SOURCE_DIR}/src/*.h
	${CMAKE_CURRENT_SOURCE_DIR}/src/*.c
	${CMAKE_CURRENT_SOURCE_DIR}/src/*.cc
	${CMAKE_CURRENT_SOURCE_DIR}/src/*.hpp
    ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp
)

# 可以加上需要排除的目录
# TODO

# 创建可执行文件
add_executable(MyExecutable ${SRC_FILES})


if(MSVC)
	# 设置基础名
	set(BASE_FILTER "Source Files\\")
	
	# 函数：vs自动分组
	function(auto_group_sources cur_dir)
	    foreach(file ${SRC_FILES})
	        # 获取文件相对路径
	        file(RELATIVE_PATH relative_path ${cur_dir} ${file})
	        # 提取目录部分
	        get_filename_component(folder ${relative_path} PATH)
	        # 替换 '/' 为 '\'
	        string(REPLACE "/" "\\" group_name "${folder}")
		# 创建分组
		set(final_group_name "${BASE_FILTER}${group_name}")
		message(STATUS ${file} " to " ${final_group_name})
		source_group(${final_group_name} FILES ${file})
	    endforeach()
	endfunction()
	
	# 调用分组函数
	auto_group_sources(${CMAKE_CURRENT_SOURCE_DIR})
endif()

```

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

