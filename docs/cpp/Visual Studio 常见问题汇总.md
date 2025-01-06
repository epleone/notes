# Visual Studio 常见问题汇总

## 严格编译

MSVC 的警告级别从 0 到 4：

| 警告级别    | 说明               |
| ------- | ---------------- |
| `/W0`   | 关闭所有警告           |
| `/W1`   | 仅显示严重警告          |
| `/W2`   | 显示较多警告           |
| `/W3`   | 默认级别，显示更多警告      |
| `/W4`   | 显示所有有用的警告        |
| `/Wall` | 显示所有警告（包括很多不常用的） |

- `add_compile_options(/W3)` 只设置警告
- `add_compile_options(/W3 /WX)`  设置警告 + 将警告视作错误



``` cmake

# 只设置警告级别为 3（默认级别）
add_compile_options(/W3)

# 或设置警告级别为 4（更严格的警告检查）
add_compile_options(/W4)

# 所有可能的警告（包括很多不常用的）
add_compile_options(/Wall)


// 将默认警告都视为错误
add_compile_options(/W3 /WX)  

// 将所有的W4警告都视为错误
if(MSVC)
	add_compile_options(/W4 /WX)
endif()

// 显示所有有用的警告，将其中的we4715视为错误
add_compile_options(/W4 /we4715)

```

## /bigobj（增加 .Obj 文件中的节数）

``` cmake

if(MSVC)
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /bigobj")
endif()
```

[/bigobj（增加 .Obj 文件中的节数） | Microsoft Learn](https://learn.microsoft.com/zh-cn/cpp/build/reference/bigobj-increase-number-of-sections-in-dot-obj-file?view=msvc-170)

## vs编译c++工程提示堆空间不足解决办法

在vs生成的.vcxproj工程中加入以下属性

``` xml
在<PropertyGroup Label="Globals">的下一行添加以下内容即可

<PreferredToolArchitecture>x64</PreferredToolArchitecture>
```

下面cmake代码将会在生成的`.vcxproj `文件实现相同的功能。(==未经验证==)

``` cmake

# 设置全局属性

if(MSVC)
	set_property(GLOBAL PROPERTY PreferredToolArchitecture x64)
endif()

```

## 显示空白符

`工具` -> `选项`  -> `文本编辑器`-> `显示` -> `查看空白` ☑️

![[Pasted image 20240531162335.png]]

## 制表符TAB键转空格

`工具` -> `选项`  -> `文本编辑器`-> `高级` -> 关闭 `使用自适应格式`

![[Pasted image 20240531162232.png]]

`工具` -> `选项`  -> `文本编辑器`-> `所有语言` -> `制表符`

![[Pasted image 20240531162113.png]]

## warning C4819: 该文件包含不能在当前代码页(936)中表示的字符

[warning C4819: 该文件包含不能在当前代码页(936)中表示的字符。请将该文件保存为 Unicode 格式以防止数据丢失](https://blog.51cto.com/u_15179769/5633310)

![[Pasted image 20240720140126.png]]

## 内存泄露

**AddressSanitizer(ASan)** 是一个快速内存检测器，可以检测出缓冲区溢出、使用已释放内存等问题。编译时带上参数 -fsanitize=address及-g。

[求推荐一个工具 能在 Ubuntu 上分析 C++程序内存使用情况 - V2EX](https://www.v2ex.com/t/1064659)
