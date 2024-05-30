# Visual Studio 常见问题汇总


## 严格编译

``` cmake

if(MSVC)
	add_compile_options(/W4 /WX)
endif()

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





