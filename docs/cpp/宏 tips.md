# 宏 tips

使用c++编译器(GCC)预定义的宏
https://sourceforge.net/p/predef/wiki/OperatingSystems/

## 生成预处理后的文件

### Liunx

```c++
// clang
clang++ -E -P -o output_i.cpp source.cpp

// g++
g++ -E -P -o output_i.cpp source.cpp

// `-E` 选项用于执行预处理，`-P` 选项用于禁止生成 `#line` 指令，以便更好地查看预处理结果。
// output_i.cpp是输出结果，source.cpp是原C++文件

```

### windows

如果你想使用命令行操作来生成预处理后的文件，你可以使用 Visual Studio 2019 提供的命令行工具 `cl.exe` 结合预处理器选项来完成。

以下是在命令行中使用 Visual Studio 2019 生成预处理后文件的一般步骤：

1. 打开命令提示符或 PowerShell。
    
2. 导航到包含源代码的目录。
    
3. 运行以下命令：
4. 
    `cl /E /P /Fi:OutputFileName.i YourSourceFile.cpp`
    
    这个命令使用 `cl.exe` 编译器，并指定了以下选项：
    
    - `/E`：执行预处理操作。
    - `/P`：将预处理结果输出到文件。
    - `/Fi:OutputFileName.i`：指定输出文件名为 `OutputFileName.i`。你可以自行替换为你想要的文件名。
    - `YourSourceFile.cpp`：指定你的源文件名。
5. 执行该命令后，将会在当前目录生成一个与源文件同名但扩展名为 `.i` 的文件，包含了预处理后的代码。
    

请注意，在运行命令之前，确保你已经将 Visual Studio 2019 的开发工具环境添加到你的系统 `PATH` 环境变量中，这样才能在命令行中直接使用 `cl` 命令。
如果不在环境变量中，可以临时添加环境变量 ![[Windows bash#临时添加环境变量路径]]


以上是一种基本的命令行操作方式，根据你的需求可能会有不同的选项和参数。你可以参考 Visual Studio 2019 的文档来了解更多关于 `cl.exe` 和预处理器选项的详细信息。

下面这是可以用的bash脚本，在vs2019下生成宏展开的代码
```bash
set PATH=%PATH%;C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.29.30133\bin\Hostx86\x64
set /p filename=输入cpp文件名:
cl /E /P /Fi:%filename%.i %filename%
```


```c
// 错误写法
#define GENERATE_CONST(view, platform, mode) \
    const AHP_MODEL_INFO ##view_##platform_##mode = { MODEL_VIEW_##view, MODEL_PLATFORM_##platform, MODEL_MODE_##mode };
    
// 正确写法
#define GENERATE_CONST(view, platform, mode) \
    const AHP_MODEL_INFO view##_##platform##_##mode = { MODEL_VIEW_##view, MODEL_PLATFORM_##platform, MODEL_MODE_##mode };
 
GENERATE_CONST(FACEVIEW, GPU, RGB)

// 生成的结果
const AHP_MODEL_INFO FACEVIEW_GPU_RGB = { MODEL_VIEW_FACEVIEW, MODEL_PLATFORM_GPU, MODEL_MODE_RGB };
```


日志打印宏

```c
#include <stdio.h>

#define ADD_PREFIX_SUFFIX(str) "%s[%d]: " str "\n"

// 在 __VA_ARGS__ 前面加上##，处理可变参数在linux为空的情况
#define print_error(fmt, ...)	printf(ADD_PREFIX_SUFFIX(fmt), __FUNCTION__, __LINE__, ##__VA_ARGS__);

#define print_info(...)	printf(__VA_ARGS__); printf("\n");


#ifdef _LOG_

	#ifdef __ANDROID__
		#define print_log(...) __android_log_print(ANDROID_LOG_ERROR, "AHGR", __VA_ARGS__)
	#else
		#define print_log(fmt, ...)	printf(ADD_PREFIX_SUFFIX(fmt), __FUNCTION__, __LINE__, ##__VA_ARGS__);
	#endif

#else

	#define print_log(...)

#endif


```


## 预定义的宏

```c++
__LINE__                %d //当前行号
__FILE__                %s //当前文件名称
__FUNCTION__            %s //当前函数名称
__DATA__                %s //含有形式为月/日/年的串，表示源文件被翻译到代码时的日期
__TIME__                %s //源代码翻译到目标代码的时间作为串包含在_ T I M E _中。串形式为时：分：秒。   

printf("This program was compiled on %s at %s.\n", __DATE__, __TIME__);
// This program was compiled on Jul 17 2021 at 15:30:40.
```

## 常用的宏

```c++
#include <stdio.h> 
#include <stdlib.h> 

// 这四个宏算是非常常见的宏了
#define IF_ERR_RETURN(res) if(res!= AHGR_ERR_NOERROR){ return res; }
#define IF_ERR_GOTO_EXIT(ret) if(res!= AHGR_ERR_NOERROR){ goto exit; } 

#define IF_NULLPTR_RETURN(ptr) if(!ptr){ print_error(#ptr ":%p, Memory allocation failed!" , ptr);	\
										return AHGR_ERR_ALLOC_MEM_FAIL;}
										
#define IF_NULLPTR_GOTO_EXIT(ptr) if(!ptr){goto exit; }

// 各种变种
#define IF_ERR_RETURN(res, fmt, ...) if(res != 0){printf(fmt, ##__VA_ARGS__); return -1; }

#define IF_ERR_RETURN(express, res) {if(express){return ret;}}

#define IF_ERR_RETURN(express, res, fmt, ...) if(express){printf(fmt, ##__VA_ARGS__); return res; }


// 自己写的工程代码
#define COLOR_RED     "31"
#define COLOR_GREEN   "32"
#define COLOR_YELLOW  "33"
#define COLOR_BLUE	  "34"
#define COLOR_MAGENTA "35"
#define COLOR_CYAN    "36"

#define ADD_COLOR(color, str) "\x1b[" color "m" str "\x1b[0m"
// 彩色打印
#define cprint(color, fmt, ...) printf(ADD_COLOR(color, fmt), ##__VA_ARGS__)

#define RETURN_CHECK_RES(res, fmt, ...) if(res != 0){cprint(COLOR_RED, fmt, ##__VA_ARGS__); return -1; }
#define EXIT_CHECK_RES(res, fmt, ...) if(res != 0){cprint(COLOR_RED, fmt, ##__VA_ARGS__); DestroyAndExit(); }

#define RETURN_CHECK_PTR(ptr, fmt, ...) if(!ptr){cprint(COLOR_RED, fmt, ##__VA_ARGS__); return -1; }
#define EXIT_CHECK_PTR(ptr, fmt, ...) if(!ptr){cprint(COLOR_RED, fmt, ##__VA_ARGS__); DestroyAndExit();  }


```