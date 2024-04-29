
`std::filesystem`是C++17引入的文件系统库，可以方便的处理文件, 类似python中的`path` 库

# 使用

需要将C++语言标准设置为 **/std:c++17**

![[Pasted image 20230705110953.png]]

在cmake中, 可以通过下面代码设定

```cmake
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /std:c++17")
```

# 常用功能和函数

```c++
	// 常用功能
	path  p("E:/testA/testB/testC/filename.txt");
	std::cout <<"根目录:" <<p.root_directory() << std::endl;         // --> 根目录:"/"
	std::cout << "根目录:" << p.root_name() << std::endl;            // --> 根目录:"E:"
	std::cout << "根目录:" << p.root_path() << std::endl;            // --> 根目录:"E:/"
	std::cout << "不带扩展的文件名:" << p.stem() << std::endl;        // --> 不带扩展的文件名:"filename"
	std::cout << "扩展名:" << p.extension() << std::endl;            // --> 扩展名:".txt"
	std::cout << "文件名:" << p.filename() << std::endl;             // --> 文件名:"filename.txt"
	std::cout << "相对目录:" << p.relative_path() << std::endl;      // -->  相对目录:"testA/testB/testC/filename.txt"
	std::cout << "父目录:" << p.parent_path()<< std::endl;           // --> 父目录:"E:/testA/testB/testC"
	p.replace_extension("jpg");                                      // --> "E:/testA/testB/testC/filename.jpg"
	p.replace_filename("haha");                                      // --> "E:/testA/testB/testC/haha"

	// 常用函数
	void copy(const path& from, const path& to)  //目录复制
	path current_path()  // 当前路径
	path absolute(const path& pval, const path& base = current_path()) //获取相对于base的绝对路径
	bool create_directory(const path& pval) //当目录不存在时创建目录
	bool create_directories(const path& pval) //形如/a/b/c这样的，如果都不存在，创建目录结构
	bool exists(const path& pval) //用于判断path是否存在
	uintmax_t file_size(const path& pval) //返回目录的大小
	file_time_type last_write_time(const path& pval) //返回目录最后修改日期的file_time_type对象
	bool remove(const path& pval) //删除目录
	uintmax_t remove_all(const path& pval) //递归删除目录下所有文件，返回被成功删除的文件个数
	void rename(const path& from, const path& to) //移动文件或者重命名
```

# 路径分割与合并

```c++
#include <iostream>
#include <filesystem>

int main() {
    std::filesystem::path filePath = "path/to/file.txt";

    // 获取路径名, dirname
    std::string pathName = filePath.parent_path().string();  //  -->  "path/to"

    // 获取文件名, basename
    std::string fileName = filePath.filename().string(); //  -->  "file.txt"

	// 获取文件名部分,不包括后缀
	std::string fileName = filePath.stem().string();   //  -->  "file"
	
	// 获取文件后缀 
	std::string fileExtension = filePath.extension().string(); //  -->  ".txt"

	// 将字符串转换为std::filesystem::path
	std::filesystem::path filePath(filePathString);

	// 进行路径拼接, 可以用 `/` `/=`  或者 append， 参数可以是string 或者filesystem::path
	std::filesystem::path newPath = parentPath / "new_folder" / fileName; 
	// 输出拼接后的路径 
	std::cout << "拼接后的路径: " << newPath << std::endl;

	// 进行路径拼接 
	std::filesystem::path fullPath = basePath; 
	fullPath.append(folderName); 
	fullPath.append(fileName);
	
    return 0;
}


// 具体的使用

// 创建保存路径
if (g_engine.b_save && g_engine.save_path != "")
{
	auto absolutePath = std::filesystem::absolute(g_engine.save_path);
	if (!std::filesystem::exists(absolutePath))
	{
		std::cout << "mkdir " << absolutePath << std::endl;

		// 创建文件夹
		// std::filesystem::create_directory 一次创建一个
		if (std::filesystem::create_directories(absolutePath)) {
			std::cout << "Created successfully！" << std::endl;
		}
		else {
			std::cout << "Create failed!" << std::endl;
		}

	}
}
	
MRESULT ImageStream::saveData(cv::Mat& vframe, std::string save_dir)
{
	std::filesystem::path filePath(m_img_path);
	std::filesystem::path dirPath(save_dir);

	if (save_dir != "")
	{
		auto _save_name = dirPath / filePath.filename();
		cv::imwrite(_save_name.string(), vframe);
	}
	else
	{
		// 获取文件名部分
		std::string fileName = filePath.stem().string();

		// 获取文件后缀
		std::string fileExtension = filePath.extension().string();

		fileName = fileName + "_rslt" + fileExtension;

		auto _save_name = filePath.parent_path() / fileName;

		cv::imwrite(_save_name.string(), vframe);
	}

	return 0;
}



```

# 判断文件是否存在

```c++

#include <iostream>
#include <filesystem>

int main() {
    std::filesystem::path filePath = "path/to/file.txt";

    // 判断文件是否存在
    if (std::filesystem::exists(filePath)) {
        std::cout << "文件存在。" << std::endl;
    } else {
        std::cout << "文件不存在。" << std::endl;
    }

    return 0;
}

```

# 创建文件夹

```c++
#include <iostream>
#include <filesystem>

int main() {
    std::filesystem::path folderPath = "path/to/folder";

	// std::filesystem::create_directory  只能创建一个
	// std::filesystem::create_directorys 可以一次创建多个
    // 创建文件夹
    if (std::filesystem::create_directorys(folderPath)) {
        std::cout << "文件夹创建成功！" << std::endl;
    } else {
        std::cout << "文件夹创建失败或已存在。" << std::endl;
    }

    return 0;
}

```

# 将相对路径转换为绝对路径

``` c++
#include <iostream>
#include <filesystem>

int main() {
    std::filesystem::path relativePath = "path/to/file.txt";
    std::filesystem::path absolutePath = std::filesystem::absolute(relativePath);

    std::cout << "Absolute path: " << absolutePath << std::endl;

    return 0;
}

```

# 遍历文件夹

可以使用标准库中的`std::filesystem::directory_iterator`来遍历文件夹，并通过检查文件的后缀来筛选出指定后缀的文件。

```c++
void GetAllFilesByDir(std::string dirPath, std::vector<string>& files, string suffix)
{
	fs::path DirPath(dirPath);
	//is exists
	if (!fs::exists(DirPath))
	{
		std::cout << dirPath << ",not exists..." << std::endl;
	}
	if (!fs::is_directory(DirPath))
	{
		std::cout << dirPath << ",not dir...." << std::endl;
	}

	fs::directory_entry Dir(DirPath);

	for (fs::directory_iterator end, begin(Dir); begin != end; ++begin)
	{
		if (!fs::is_directory(begin->path()))
		{
			if (begin->path().extension().string() == suffix)
			{
				files.push_back(begin->path().string());
				std::cout << "find a file:" << begin->path().string() << "in" << dirPath << std::endl;
			}
		}
	}

};
```

递归遍历文件夹，找出所有图像文件

```c++
#include <iostream>
#include <filesystem>
#include <vector>
#include <string>
#include <algorithm> // 用于字符串比较

namespace fs = std::filesystem;

bool isImageFile(const fs::path& filePath) {
    static const std::vector<std::string> imageExtensions = { ".jpg", ".jpeg", ".png", ".bmp", ".gif" };
    const std::string extension = filePath.extension().string();
    
    // 将文件扩展名转换为小写，并检查是否在图像文件扩展名列表中
    std::string lowercaseExt = extension;
    std::transform(lowercaseExt.begin(), lowercaseExt.end(), lowercaseExt.begin(), ::tolower);

    for (const std::string& imageExt : imageExtensions) {
        if (lowercaseExt == imageExt) {
            return true;
        }
    }
    
    return false;
}

void findImageFiles(const fs::path& directory, std::vector<fs::path>& imageFiles) {
    for (const fs::directory_entry& entry : fs::directory_iterator(directory)) {
        if (fs::is_regular_file(entry)) {
            if (isImageFile(entry.path())) {
                imageFiles.push_back(entry.path());
            }
        } else if (fs::is_directory(entry)) {
            findImageFiles(entry.path(), imageFiles);
        }
    }
}

int main() {
    fs::path rootDirectory = "/path/to/your/root/directory";
    std::vector<fs::path> imageFiles;

    findImageFiles(rootDirectory, imageFiles);

    for (const fs::path& imagePath : imageFiles) {
        std::cout << imagePath << std::endl;
    }

    return 0;
}


```
